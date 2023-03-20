# Git

[[toc]]

## NOTE

\[ \] means that it can be omitted.

## Setup

```bash
git config --global user.name "my name"
git config --global user.email "my-address@goes.here"
git config --global core.editor "notepad++"
git config --list

git config --global -e # to use specific editor

cat ~/.gitconfig
```

## 4 Areas

- Working Directory
- Staging Area
- Repository (.git folder)
- Remote

## Basics

### Words

- `HEAD` means last commit.

### Add

```bash
git add . # all files under current directory
git add -A # all files
git add -u # all files except new files (u = update)

git add . --dry-run # pre test
```

### Commit

```bash
git commit -m "commit message"
git commit -am "commit message" # add & commit at same time
```

### List tracked files

```bash
git ls-files
```

### Unstage

```bash
git reset [HEAD]
git reset [HEAD] somefile.txt
```

### Discard changes

```bash
git checkout [--] somefile.txt
```

### Move files

```bash
git mv file1.txt file2.txt
```

the difference between `mv` and`git move` is that `git mv` automatically stages files.

### Delete files

```bash
git rm file1.txt
```

### Show logs

```bash
git log --all --oneline --graph --decorate
git log f8vk34...1ffai8
git log somefile.txt
git log --follow somefile.txt # follow rename

git show fi8vd2
```

## Alias

```bash
git config --global alias.h "log --oneline"
git h # => equiv with "git log --oneline"
```

or edit .gitconfig directly.

## Merge & Diff tools

```bash
git config --global merge.tool p4merge
git config --global mergetool.p4merge.path "C:/Program Files/Perforce/p4merge.exe"

git config --global diff.tool p4merge
git config --global difftool.p4merge.path "C:/Program Files/Perforce/p4merge.exe"
```

## Compare

All followning `diff`s can be replaced with `difftool`

### HEAD vs Working directory

```bash
git diff
```

### HEAD vs Staging area

```bash
git diff --staged
#or
git diff --cached
```

### HEAD vs All changes(staged & unstaged)

```bash
git diff HEAD
```

### HEAD vs Specific commit

```bash
git diff fj38v87 [HEAD]
git diff HEAD^ [HEAD]
```

Note that args should be lined old commit first. Otherwise result will be opposite.

### Between specific commits

```bash
git diff fj38v87 ow8vjq3
```

### Between branches

```bash
git diff mainbranch topicbranch
```

### Local vs Remote

```bash
git diff origin/remoteBranchName localBranchName
```

## Branch & Marge

### Show branch

```bash
git branch -a # show remote & local branch
```

### Create branch

```bash
git branch somename
git checkout -b somename # create branch & checkout
```

### Rename branch

```bash
git branch -m some_name some_new_name
```

### Delete branch

```bash
git branch -d somename
git branch -D somename # delete unmerged branch
```

### Merge branch (Fast-Forward)

```bash
git merge somebranch
```

### Merge branch (Non Fast-Forward)

```bash
git merge --no-ff somebranch
git merge --no-ff somebranch -m "commit message"
```

### Resolving conflicts

```bash
git merge somebranch
git mergetool
git commit -m "this is merge commit message"
```

.orig file (backup file) may be created when resolving conflicts. remove or ignore it.

## Remote Branch

### push

ローカルブランチをリモートにプッシュする

```bash
git push origin some_localbranch:remote_branch
git push -u origin some_localbranch:remote_branch # 設定を記憶しておく
```

### branch

リモートブランチの一覧を表示する

```bash
git branch -v # ブランチ一覧
git branch -vv # ブランチ一覧（リモートとの対応つき）
```

### prune

リモートで削除されたブランチをローカルでも削除する

```bash
git remote prune origin
git remote prune origin --dry-run
```

## Rebase

### from local branch

```bash
git checkout -b myfeature
git rebase master
git mergetool # if there is conflicts, fix it
git rebase --continue
git rebase --abort # cancel rebasing
```

### from remote branch

```bash
git fetch origin/master
git checkout master
git rebase origin/master

# or

git pull --rebase origin master
```

### onto

```sh
git rebase --onto TARGET_BASE CURRENT_BASE BRANCH_OR_LAST_COMMIT

# Example:
#
#                     G - H - I(unit-test)
#                    /
#           D - E - F (develop)
#          /
# A - B - C (master)
#
# git rebase --onto master develop unit-test
#
#           D - E - F (develop)
#          /
# A - B - C - (master) - G' - H' - I'(unit-test)
```

## Stash

```bash
git stash [save "this is stash message"]
git stash -u # include new file (u = untracked)
git stash list
git stash show [stash@{0}]
git stash apply [stash@{0}]
git stash drop # delete most recent stash
git stash drop [stash@{0}]
git stash clear # delete all stash
git stash pop [stash@{0}] # apply & drop at a time

git stash branch newbranchname
# Create a new branch with name `newbranchname`
# Checkout to new branch
# Apply & drop a stash
```

## Tag

Tags can be used as params.

```bash
git diff mytag HEAD
```

### Lightweight tag

```bash
git tag mytag
git tag mytag 870f3c
git tag mytag 870f3c -f # force to move tag point
git tag --list
git tag --delete mytag
```

### Annotated tag

Annotated tag has Tagger(author), date&time, tag message.
Rest is same as lightweight tag.

```bash
git tag -a v-1.0
git show v-1.0
```

### push tags to remote

```bash
git push origin master --tags # transfer all tag
git push origin :mytag # delete tag
```

## Reflog

```bash
git reflog
git reset 357vef9 # back to this reflog
```

## submodule

- 親リポジトリ内で、別のリポジトリを子として管理するときに使う
- `.gitmodules`に、submodule の一覧が記録される
- submodule のバージョンはコミットハッシュ（特定のバージョン）で管理されている。
- 親リポジトリを`checkout`した際など submodule の参照先コミットハッシュが変わったときは、submodule を手動でアップデートする必要がある。

```bash
git submodule add ${REPO_URL} ${TARGET_DIR}

# submoduleが参照しているコミットハッシュの一覧を表示する
git submodule

# submoduleが参照しているコミットハッシュが変わったときは
# 手動でsubmoduleを更新する必要がある
git submodule update
```

[参考資料](https://qiita.com/sotarok/items/0d525e568a6088f6f6bb)
