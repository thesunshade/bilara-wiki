The most obvious strategy would be to use one commit per change. This runs into a problem: In the pali canon we have about 170,000 segments and that would be a large number of commits. Github can handle 1 million commits but it definitely has potential to cause problems if the number of small commits is excessive.

Better would be to make commits of logical segments of work. In git a commit must be authored, and while with Bilara it would be possible to use a machine-user, it is more logical to use the logged in author for each commit. So that means that each commit can contain the work only done by a single user.

Logical Commits:

* Changes by one user to one file
* Changes by one user to many files, where the change is highly repetitive (i.e. search and replace)

The basic flow is:

1. A branch is created for each user
2. A commit is created for the user (starting as an empty commit)
3. As the user makes submissions, the file is updated and *appended* to the commit.
4. At the proper time, the branch is pushed and merged into master, at this point a new empty commit is created (back to step 2).
5. "The proper time" is when the user navigates away from the text or a certain amount of time has elapsed (maybe 5 minutes) or perhaps when another user wants to update the file.

The local repository is always updated immediately to avoid data loss in the case of a crash. It is mainly the pushes to github that can be delayed, as it is when a push is made that the state can be considered to be set in stone.
