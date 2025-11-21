## Setting up the Workspace
```bash
mkdir git-advanced-lab && cd git-advanced-lab
git init
echo "Hello, Advanced Git" > hello.txt

# Add file to staging area
git add hello.txt

# Commit changes to local repository
git commit -m "Initial commit"
```

## Amending Your Last Commit

Imagine you've just made a commit, but then you realize you forgot to include a change in a file, or you made a small typo in your commit message. Instead of creating a whole new commit for such a minor fix, Git allows you to fix the most recent commit using the **--amend** option. This is like going back in time slightly to adjust your last action.

```bash
echo "This is an important file." >> hello.txt
git add hello.txt
git commit --amend -m "Initial commit with important note"
git log --oneline
```

- **git commit --amend -m "Initial commit with important note"**: This is where the magic happens.
    - **git commit --amend**: The **--amend** flag tells Git that instead of creating a new commit, we want to modify the last commit.
    - **-m "Initial commit with important note"**: This provides a new commit message. If you omit the -m option, Git will open your default text editor, allowing you to edit the original commit message.

## Reverting a Commit

Sometimes, you make a commit and later realize it introduced a bug or you simply want to undo those changes. You might think of using **git reset** to go back in time and remove the commit. However, **git reset** can be destructive, especially if you've already pushed your changes or if others are working on the same branch. A safer and more collaborative way to undo changes is using **git revert**.

**git revert** creates a new commit that undoes the changes introduced by a specific commit. It doesn't erase the original commit from history; instead, it adds a new commit that effectively reverses the effects of the unwanted commit. This preserves the history and is much safer for shared repositories.

```bash
echo "This line will be reverted" >> hello.txt
git add hello.txt
git commit -m "Add line to be reverted"
```

Now, let's say we decide that adding "This line will be reverted" was a mistake, and we want to remove it. We can revert the last commit using:

```bash
git revert HEAD
```

Let's break down this command:

- **git revert:** This is the command to revert changes.
- **HEAD:** In Git, **HEAD** is a pointer to the current commit of your current branch. In most cases, **HEAD** refers to the most recent commit. So, **git revert HEAD** means **"revert the most recent commit"**. You can also specify a specific commit hash to revert an older commit.

When you run **git revert HEAD**, Git will do the following:

1. **Analyze the changes:** It looks at the changes introduced by the HEAD commit (in our case, "Add line to be reverted").
2. **Create reverse changes:** It figures out how to undo those changes (in our case, removing the "This line will be reverted" line from "hello.txt").
3. **Create a new commit:** It automatically creates a new commit that applies these reverse changes.
4. **Open Text Editor (Optional):** Git will usually open your default text editor (like Vim, Nano, VS Code's editor, etc.) with a pre-filled commit message for the revert commit. The default message is usually something like "Revert 'Add line to be reverted'". You can accept the default message or modify it to provide more context.

### Why is **git revert** preferred over **git reset** in shared repositories?

- **Non-Destructive History:** **git revert** doesn't rewrite history. It adds a new commit to undo changes, making it safe for collaboration. Everyone can see the original mistake and the correction in the history.
- **Avoids Force Pushing:** **git reset** often requires force pushing (**git push --force**) to a remote repository if you've already pushed the commits. Force pushing can be very disruptive and overwrite others' work. git revert avoids this issue.
- **Clear Audit Trail:** **git revert** provides a clear record of when and why changes were undone. This is valuable for understanding the project's evolution and debugging issues.

## Cherry-picking Commits

Cherry-picking in Git is like picking a cherry from a tree – you select a specific commit from one branch and apply it to your current branch. This is useful when you want to incorporate a particular feature or fix from another branch.

Let's simulate a scenario where we have a feature developed in a separate branch, and we want to bring just that feature into our main branch.

First, we need to create a new branch and make a commit on it:

```bash
git checkout -b feature-branch
echo "This is a new feature" >> feature.txt
git add feature.txt
git commit -m "Add new feature"
```

Now, let's imagine we're done with this feature for now, and we want to integrate it into our main branch (**master**). First, we need to switch back to the **master** branch:

```bash
git checkout master
```

This command simply switches your active branch back to "**master**".

Now we want to apply the "Add new feature" commit from "feature-branch" to our **master** branch. We use **git cherry-pick** for this:

```bash
git cherry-pick feature-branch
```

Wait, why **feature-branch** and not a commit hash? In this simple case, **feature-branch** as a commit-ish will resolve to the latest commit on **feature-branch**. Since we only made one commit on **feature-branch**, this effectively cherry-picks that commit. If you had multiple commits on **feature-branch** and wanted to pick a specific one, you would use the commit hash of that specific commit instead. For example, you could find the commit hash using **git log feature-branch --oneline** and then use **git cherry-pick <commit_hash>**.

When you run **git cherry-pick feature-branch**, Git does the following:

- **Find the commit:** It identifies the latest commit on "**feature-branch**" (or the commit you specified by hash).
- **Apply changes:** It tries to apply the changes introduced by that commit to your current branch (master).
- **Create a new commit:** If successful (no conflicts), Git creates a new commit on your master branch that has the same changes as the cherry-picked commit and usually the same commit message.

### Important Considerations for Cherry-picking:

- **New Commit:** Cherry-pick always creates a new commit. It doesn't move or copy the original commit. This is important to remember when thinking about commit history.
- **Potential Conflicts:** Cherry-picking can sometimes lead to conflicts, especially if the codebases of the two branches have diverged significantly. If conflicts occur, Git will pause the cherry-pick process and ask you to resolve them, similar to merge conflicts.
- **Use Sparingly:** While powerful, cherry-picking should be used judiciously. Overusing cherry-pick can make your history harder to follow, especially if you later merge the entire feature branch. It's most effective for applying specific, isolated changes or fixes from one branch to another when a full merge is not desired or appropriate.

## Interactive Rebasing

Interactive rebasing is one of Git's most powerful, and potentially complex, features. It allows you to rewrite your commit history in very flexible ways. You can reorder commits, combine (squash) commits, edit commit messages, or even remove commits entirely. It's like having a fine-grained editor for your commit history.

**Warning:** Interactive rebasing, especially on shared branches, can be risky because it rewrites history. **Never rebase commits that have already been pushed to a shared repository unless you fully understand the implications and have coordinated with your team.** Rebasing is generally safer and more appropriate for cleaning up your local commits before sharing them with others.

Let's create a series of commits to work with so we can see interactive rebasing in action:

```bash
echo "First change" >> hello.txt
git commit -am "First change"
echo "Second change" >> hello.txt
git commit -am "Second change"
echo "Third change" >> hello.txt
git commit -am "Third change"
```

These commands create three new commits in quick succession, each adding a line to our "hello.txt" file. The -am shorthand in git commit **-am** combines staging all modified and deleted files (**-a**) with committing (**-m**). It's a convenient shortcut when you've already been tracking files and just want to commit changes to them.

Now, let's say we want to clean up these three commits. Maybe "First change" and "Second change" were actually part of the same logical change, and we want to combine them into a single commit. And maybe we want to improve the commit message for "Third change". Interactive rebasing is perfect for this.

Start an interactive rebase session using:

```bash
git rebase -i HEAD~3
```

Let's understand this command:

- **git rebase -i:** git rebase is the command for rebasing. The **-i** flag stands for "**interactive**", which tells Git we want to perform an interactive rebase.
- **HEAD~3:** This specifies the range of commits we want to rebase. **HEAD** refers to the current commit. **~3** means "go back three commits from HEAD". So, **HEAD~3** selects the last three commits. You could also specify a commit hash instead of **HEAD~3** to start the rebase from a specific point in history.

####  Our Interactive Rebase Plan:

We want to:
1. **Squash "Second change" into "First change".**
2. **Reword "Third change" to have a better message.**

To achieve this, modify the editor file to look like this:

```bash
pick abc1234 First change
squash def5678 Second change
reword ghi9101 Third change
```

### Important Reminders about Interactive Rebasing:

- **Local History Tool:** Interactive rebase is primarily a tool for cleaning up your local commit history.
- **Avoid Rebasing Shared Branches:** Do not rebase branches that have been pushed to a shared repository unless you are absolutely certain of what you are doing and have coordinated with your team. Rewriting shared history can cause serious problems for collaborators.
- **git rebase --abort:** If you make a mistake during an interactive rebase, or if you get confused or encounter conflicts you can't resolve, you can always use **git rebase --abort** to cancel the rebase operation and return your branch to the state it was in before you started the rebase. This is a safe way to back out of a rebase if things go wrong.

## Rebasing from the root

**git rebase -i --root** allows you to interactively rebase all commits in your repository's history, starting from the very first (root) commit. This gives you the ultimate control to reshape your entire project history.

```bash
git rebase -i --root
```