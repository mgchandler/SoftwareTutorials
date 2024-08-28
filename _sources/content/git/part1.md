# Initialise a Repository

Git works by saving content within a directory, essentially taking a snapshot of the current state of all of the files inside the directory. Make a new directory either in the file explorer or using `mkdir` from the command line, and navigate into it:

```console
cd <path>
```

Now we need to initialise a repository with Git. A repository, or "repo", is the name for this folder which will be managed by Git. To do this, use

```console
git init
```

inside the space. We have now initialised an empty repository, and can now tell Git to start tracking files.

<img src="init.png" alt="Git Bash output for initialising a new repo" width="500"/>

Note that Git Bash now has the name `(master)` next to the directory `~/new_repo`. This indicates that the repo was set up successfully inside this directory.

## Adding files

We can now start using Git to manage files. Make a new text file in the repo.

<img src="file.png" alt="Text file in repo" width="500"/>

Currently, the text file `readme.txt` is located within the repo directory. Git will keep track of what's going on inside the repo - we can check this using
```console
git status
```

<img src="status.png" alt="Output from git status" width="500"/>

We can see that the file has been detected in the repo, but it is not yet being tracked. To tell Git to start tracking the file, use
```console
git add readme.txt`
```

We can add multiple files at once by chaining filenames, or use
```console
git add .
```
to add everything in the directory at once.

<img src="add-file.png" alt="Add a file to be tracked" width="500"/>

## Saving files

We can see that Git has detected that something had changed: we had told it to start tracking the file `readme.txt`. At the moment, we haven't actually saved anything
