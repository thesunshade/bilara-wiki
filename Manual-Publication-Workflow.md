It is straightforward to manually publish texts. I will demonstrate how to do so by publishing some root texts. 

## Step 1

* first we need to know the file or folder path which we will publish, I will use `root/pli/ms`

```
root/pli/ms
```

## Step 2

* navigate to the bilara-data repo (i.e. bilara/repo)
    - **This is confusing, which one is it? What is the relationship between these things?**
* checkout the published branch
* make sure it's up to date
* create a new branch from it, in this case I will use `publish-root-pli-ms`
    - better to use the full name of the thing, right?

```
git checkout published
git pull
git checkout -b publish-root-pli-ms
```

## Step 3

* checkout the files to-be-published from the unpublished branch (uses the path from earlier)
* create a commit containing the checked out files
* push the branch to github

```
git checkout unpublished root/pli/ms
git commit -a -m "Publishing the Mahasangiti Pali texts"
git push -u origin publish-root-pli-ms
```

## Step 4

* Go to github.com
* Create a Pull Request, from `publish-root-pli-ms` to `published` 
    - get the branches right!
* Once the pull request has been accepted, the texts are in the `published` branch!