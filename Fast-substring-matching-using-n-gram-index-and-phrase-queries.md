A problem in text search is finding a substring anywhere in a string.

English lends itself well to word based search, but many languages such as German or Pali like to bung words together into long compounds. For example in english we might say "fancy chocolate cake", but in german it would tend to be something like "fancychocolatecake" (a real example from Pali is "cīvarapiṇḍapātasenāsanagilānappaccayabhesajjaparikkhārānaṁ"). Suppose now we want to search for the word "chocolate", in english any standard text indexing strategy will work just fine, but in german none of them will. Then there are even languages that don't use spaces (like Thai) or use spaces to separate syllables instead of words (like Vietnamese).

# N-Grams

The general solution is to use n-grams. In this case, fixed width n-grams. The way it works is a word is broken up, for example our compound word, when broken up into 3-grams, becomes:
```
["fan", "anc", "ncy", "cyc", "ych", "cho", "hoc", "oco", "col", "ola", "lat", "ate", "tec", "eca", "cak", "ake"]
```

When indexed into a search engine, this allows searching on any 3-gram.

Traditionally N-gram searches use a "bag of grams", and indeed in ArangoDB this is how "NGRAM_MATCH" works. By "bag of grams" I mean it doesn't care what order they come in, or whether they form anything coherent. The bag of grams approach can be very useful for fuzzy searching, but has a lot of needless false positives for a simple string search which makes it slower than it needs to be, and a second post-processing step is needed to find if there's actually an exact match.

# PHRASE Search to the rescue

A phrase search simple looks for a series of words/tokens. As per above, a compound word is simply indexed as a series of n-grams, so if we use another series of n-grams in a phrase query we get precisely the behavior we want.

For example if we search "chocolate" we turn it into the 3-grams `["cho", "hoc", "oco", "col", "ola", "lat", "ate"]`, and since that precise sequence exists in the n-grams we indexed, phrase search will find it!

Even though this is kind of a "trick", any good search engine will allow running a phrase query over an n-gram index, both Elasticsearch and ArangoDB support it, though it may take some poking to get the tokenization and indexing to work exactly as required. For example in ArangoDB, it is necessary to disable "preserveOriginal" when doing the tokenization, as this would insert the original word into the n-grams which messes up the sequence (e.g. "hello" gets tokenized as `["hel", "hello", "ell", "llo"]`, this destroys the ability to use PHRASE query to find substrings, if it put the original in the 0 position it would actually work fine, and this is probably an example of the combination of n-grams with PHRASE not really being intended).

Once the indexing is done correctly, the query is beautifully simple, this being an actual working query:
```
FOR doc IN ngram_view
    SEARCH ANALYZER(PHRASE(doc.string, TOKENS(@query)), "ngrams3")
    RETURN doc
```

It simply is a PHRASE search over an n-gram index and this query will be extremely fast provided that the query term is not so vague as to produce an excessive number of results.

# Tokenization of the source

Fundamentally there are two ways to approach tokenization, word based tokenization or string based tokenization, that is either you split the string into words then tokenize those words into n-grams, or you just throw in the entire string. The difference is with the latter you get the spaces in the n-grams, i.e. if you tokenize "hello world", you'll get the tokens `"lo ", "o w", " wo"`, this is actually potentially useful for allowing a literal string search where that string contains spaces. Also if you append and prepend the indexed strings with a space, you can use spaces to identify where words start and end, and if you wanted to do a prefix search, you could simply search for " chocolate", this might well be a known trick from searching webpages, like if you want to search for "ring" by default the browser will also find it in words like "bring", prepending a space forces the browser to find it only at the start of words.

Hence my conclusion is that it generally seems best to to tokenize the entire string as n-grams rather than first tokenizing it into words, this is also more broadly compatible over different languages.

# Multiple word queries

Suppose you want to do a traditional search, using multiple words and don't require those words be adjacent, can this be done in ArangoDB? Well yes, but it's a little annoying. Basically you have to construct a query that runs a PHRASE query for each word:
```
FOR doc IN ngram_view
    SEARCH ANALYZER(PHRASE(doc.string, TOKENS(@word1)) AND PHRASE(doc.string, TOKENS(@word2)), "ngrams3")
    RETURN doc
```

While it is mildly irritating to have to construct AQL queries programmatically, the queries are extremely fast.

# Limitations

## Minimum length

The most obvious limitation of this technique, is that substrings have a minimum length, for example when using 3-grams you can't search for a 2 character string. Of course this can be resolved by using 1-grams and this is actually exactly what is often done for CJK since single character resolution is required for functional search in CJK. But there is a substantial performance penalty for using shorter grams, with 4-grams being about twice as fast as 3-grams. This is basically because with shorter n-grams there will be many more matches. At the extreme case, using 1-grams for a language with 26 characters means that basically every document will match the first 1-gram as it'll be a common "sequence" like "c" or something, and "c" followed by "h" is also enormously common, and so the search engine has a lot of narrowing down work to do. In contrast, with a 4-gram the sequence "choc" will occur in far fewer documents, and choc followed by "hoco" in even fewer, so it narrows down much faster.

Thus practically it is necessary to mandate a minimum search query length, but one of my pet peeves is search engines which forbid short queries and it is of course possible to support shorter queries via a special query, suppose for example that some user wants to search for "go" (a good example of a short legit word, in english referring to a programming language and a board game in addition to being a verb). For a very short string, a query like this can be used:

```
FOR doc IN ngram_view
    SEARCH ANALYZER(STARTS_WITH(doc.string, @query), "ngrams3")
    LIMIT 10000
    RETURN doc
```

In this query the STARTS_WITH function is used to search based on *3-grams* that start with `go`, for example it would find "goc" (gocariya). We also throw in a reasonable limit, with as many documents as Bilara has (around 450,000 just for Pali root texts) setting a limit of 10000 drastically slashes the amount of work ArangoDB has to do meaning the query runs in a few milliseconds. This is also a common trick used by search engines for overly vague queries, they just say "of many results" or something because trying to calculate exactly how many matches there are or a good relevance value is too much work for an overly vague query.

Further exploring this concept might allow using longer n-grams with the associated performance benefits, and with very few downsides as short queries would still be supported about as well as one could expect. This may even be suitable for CJK as a universal indexing system.

## Relevance

Generally the further a query deviates from how the search engine expects it to be used, the poorer term frequency based relevance may perform. But relevance is always a hard problem in search engines.

# REGEX / LIKE search

Another option is using fast substring search to narrow down results before applying a REGEX or LIKE query. Imagine a regex like `metta.{0,10}citta`, meaning "I want to find the word metta and citta separated by not more than 10 characters", with certain limitations the query could be analyzed and determined that it can only be true if the sequence "metta" exists in the document, and so the fast substring search used to narrow down the documents. Regex analysis is tricky though, and this sort of feature would be simpler on LIKE syntax, which is along the lines of `_` for any character or `*` for any number of characters. Given a query like "metta*citta" it's straightforward that both metta and citta must exist in the document, while a regex can be very hard to make deductions about.

# Conclusion

An n-gram based fast substring search is a very powerful technique, allowing extremely high performance queries and with simple hacks for allowing queries shorter than the chosen n-gram length. It is broadly applicable to many or even all languages, the weakness in stemming may well be compensated for by its strength in searching inside words and based on prefix/suffix.