# Version Control from the Command Line

## TL:DR

1. `git checkout -b my-feature-branch`
1. Work, committing like normal.
1. Close _Editing Tools_.
1. `git checkout main`
1. `git pull --rebase`
1. `git checkout my-feature-branch`
1. `git rebase main`
1. Open _Editing Tools_, test your feature branch, fix as necessary.
1. `git rebase -i main`
    1. Pick the top one, squash the rest.
    1. Write a good commit message.
1. Close _Editing Tools_.
1. `git checkout main`
1. `git rebase my-feature-branch`
1. `git branch -d my-feature-branch`
1. `git push`

## Running the command line

From Windows, the first thing I would do is install [Visual Studio Code](https://code.visualstudio.com/).
Next, [download and install git](https://git-scm.com/download/win), which will include Git Bash as part of the process. 
You will be able to select Visual Studio Code as your commit editor.
Run Git Bash to open your command line interface.

Mac users, install git whatever the normal way is, and then open the terminal in the normal way. You can tell if it works if you run `git` and it doesn't complain about the command not being found.

## Getting around

The shell will tell you your current directory, where "~" means your home directory. Use `ls` to list the contents of the current directory. Use `cd` to change directories and `mkdir` to make directories. 

For example, let's say I'm on Windows and I want to go to my desktop folder. I'd run Git Bash, see that I am in my home folder, and then use `cd Desktop`.

## Checking out the project

```
git clone https://github.com/Jordan-Demske/Project-Alpha.git
```

This will clone the project into a new direcotry, `Project_Alpha`, off of the current directory. For example, if you followed the instructions above, you would see this directory on your desktop.

Note that, since you have a copy of the project, you can now open it with _Editing Tools_.

## A quick note about merge conflicts

Merge conflicts happen when two people have edited the same resource. The best way to avoid them is to make sure two groups aren't touching the same 
files at the same time. When you _do_ get a merge conflict, the solution is always to find the people who wrote the conflicting code and then
collaborate to resolve the conflict. There's no silver bullet.

## Simple changes (making changes directly to `main`)

Let's say you make a simple change to the project and nobody is screaming about how you haven't made a feature branch. Make your change in the editor, 
then go back to the command line, and make sure you're in the project directory. Use `git status` to see what files changed. Notice how the output of this command gives
you some idea of what to do next. For example, use `git add` to add any new files that need to be tracked, or use `git add -A` to add all untracked files to 
the current changeset.

Use `git commit -am "Add foobar"` to commit your changes, where "Add foobar" should be replaced by your commit message [that complies with the standards](https://cbea.ms/git-commit/).

Use `git pull --rebase` to update your local copy of the project. Hopefully you will have no merge conflicts. If you have merge conflicts, find the person who wrote the 
conflicting code and sort it out.

When you're done, `git push` to push your changes to GitHub.

## A better way: Feature Branching

Usually, your changes will require many small steps, and you'll want to be a bit more disciplined about organizing your changes. An effective way to do this 
is with _feature branching_.

Either before you start your work, or before you commit your work, create a new feature branch with a command like `git checkout -b my-feature-branch`, where "my-feature-branch" is the name of the branch. Then, whenever you commit, it will be made on that branch. 

If you don't finish the work in one sitting, or have to split it across two 
or more stations, you can also push your feature branch to GitHub with `git push -u origin my-feature-branch`. Then,
your collaborators can pull that branch down with `git pull origin my-feature-branch`. 

Now, when you're done working on that feature branch, you'll have a lot of small commits that complete a feature for integration. 
You could just merge all those into `main`, but there is a better way: _squashing commits_.

At any time, you can run `git log --abbrev --decorate --oneline --graph` to see all the changes to the repository. Once you run this command once, you can go find it again in your history (via `history`), or you can repeat it with the shell command `!?git bash`. (That literally means, "Repeat the last command I wrote that started with the text `git log`.")

OK, so you have a branch called `my-feature-branch` and you're ready to bring it in to `main` for the whole team. Here's what to do.

First, close the editor. If you change branches while _Editing Tools_ is open, you're more likely to have a bad time.

Then, update `main` to get any changes that have happened there since you started working on your feature branch. Do that like this:
- Switch branches: `git checkout main`
- Update this branch: `git pull --rebase`
    -  Note that if all your changes were on a feature branch, this should never cause any trouble. In practice, if you had made some changes to the main branch, you could hit a merge conflict here.
- Switch back to your feature branch: `git checkout my-feature-branch`
- Bring the changes from `main` into your feature branch: `git rebase main`
    - This is another spot where you could get merge conflicts, if you changed something in your feature branch that someone changed and already merged into `main`.

Now you can actually squash all your little commits into one clear commit. This is done through an "interactive rebase."
You initiate that with this command: `git rebase -i main`. That is, interactively rebase this branch, looking back to where it branched from `main`.

The editor will pop up, and it will show you that, by default, it's going to "pick" all of the commits. This is actually a "no-op": if you close the editor,
it will pick everything as it is, and nothing changes. To squash commits, leave the top one as "pick" but change all the subsequent ones to "squash". You can
actually just use "s" as shorthand for "squash". Save the buffer and close the editor, and then it will come back again, but showing all the commit messages
for the commits you're squashing. Here, you want to write a new commit message that describes the whole branch&mdash;all the things you have done on the branch,
summarized in one clear commit message.

Once that's saved and closed, the interactive rebase is done. Use your `git log` command (e.g. `!?git log`) and you should see just one clean commit.
Note that you could turn 10 commits into 2 or 3 if you didn't want to squash down to one for some reason: there's not one right answer here. The thing
we're setting up is having the version control history clear, so we can go back to specific points, without having to deal with a preponderance of commits.

Now, you're not quite done, because someone might have changed `main` while you were doing your interactive rebase! You know what to do:
- Check out the main branch with `git checkout main`
- Pull any changes to it with `git pull --rebase`
- If it says you're up to date, you're ready to go. If not, repeat the process described above of switching to your feature branch, bringing the changes from `main` into your feature branch, and squashing those commits.

Finally, from the main branch, bring in your feature branch with `git rebase my-feature-branch`.

## Cleaning up

Once you've completed your feature branch, you should delete your feature branch.

If you never pushed it to GitHub, it's easy: `git branch -d my-feature-branch`

If you did push to GitHub, you can try that command above, and it will complain about changes being merged locally but not remotely. The easiest way to handle this is:
1. Force-delete the local feature branch: `git branch -D my-feature-branch`
2. Go to the web view of the repository on GitHub, go to branches, and delete the feature branch from there.

There is a way to do that all from the command line, but that's where I draw the line, since the syntax goes insaneo style.
