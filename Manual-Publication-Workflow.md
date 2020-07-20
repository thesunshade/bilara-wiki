It is straightforward to manually publish texts. I will demonstrate how to to so by publishing some root texts. 

##Step 1

* first we need to know the file or folder path which we will publish, I will use **root/pli/ms**

```
root/pli/ms
```

##Step 2

* navigate the bilara-data repo (i.e. bilara/repo)
* checkout the published branch
* make sure it's up to date
* create a new branch from it, in this case I will use **publish-pali**

```
git checkout published
git pull
git checkout -b publish-pali
```

##Step 3

* checkout the files to-be-published from the unpublished branch (uses the path from earlier)
* create a commit containing the checked out files
* push the branch to github

```
git checkout unpublished root/pli/ms
git commit -a -m "Publishing the main pali texts"
git push -u origin publish-pali
```

##Step 4

* Go to github.com
* Create a Pull Request, from **publish-pali** to **published** - get the branches right!
* Once the pull request has been accepted, the texts are published!