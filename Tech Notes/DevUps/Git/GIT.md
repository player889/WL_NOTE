#sourceTree #git #squash

![[Pasted image 20230314163417.png|800]]
![[Pasted image 20230314163713.png|700]]
![[Pasted image 20230314164130.png|500]]

#####

```git
#查看git配置
git config --list
git config -l

#查看系统配置
git config --system --list

#查看当前用户（global）全局配置
git config --list --global

#查看当前仓库配置信息
git config --local  --list。
```

```git
$ git config --global user.name "Your Name"
$ git config --global user.email "email@example.com"
# 配置完后，看看用户配置文件：
$ cat 'C:\Users\Kwongad\.gitconfig'
[user]
        name = Kanding
        email = 123anding@163.com
```

###### 如何使用 git 更新 branch 到 master 最新狀態

1. 更新最新 Master 版本
    ```git
    git checkout master
    git pull origin
    ```
2. 切換至分支下，更新版本
    ```git
    git checkout hotfix
    git merge master
    ```
3. 檢查
    ```git
    git status
    ```
4. push 至遠端分支
    ```git
    git push origin hotfix
    ```

- 可以使用 fetch 指令，抓最新版本至 temp branch， `git diff`進行比較，merge 後刪除 temp branch

###### 重設第一個 commit

所有的改動都重新放回工作區，並清空所有的 commit，這樣就可以重新提交第一個 commit 了

```git
git update-ref -d HEAD
```

- [Git 將自己的開發分支代碼更新到和 master 分支一樣](https://blog.csdn.net/weixin_43959963/article/details/112580465?utm_medium=distribute.pc_relevant.none-task-blog-baidujs_title-1&spm=1001.2101.3001.4242)
- [Git 的奇技淫巧](https://github.com/521xueweihan/git-tips)

# Git: Clear local and remote repo and start over tart-over

> Simply remove local `.git` directory, remove repo from server (if it is github - do Repo -> setiings -> remove).
> Then create new repository on server, and locally do:

```git
	git init
	git remote add origin git@github.com:user/project.git
	git add .
	git commit -m "Initial commit"
	git push -u origin master
```

# Source tree Q&A

Q1: When I push source code, error message
==use ctrl+c to cancel basic credential prompt.==

A1:

1. Tools -> Options -> Authentication
2. Click "Edit" on Your Account
3. Click "Refresh OAuth Token"

## igorne file or folder already commit

主要是由於之前 commit 過某些文件和文件夾，導致無法忽略這些文件
采用命令 git rm --cached "文件路徑"
刪除文件夾的時候，出現 not removing 'game/logs' recursively without -r，說明我們需要添加參數 -r 來遞歸刪除文件夾裏面的文件
git rm -r --cached "文件路徑"
然後 commit

```shell
git rm -r --cached google_token/
```

## How to edit message already commit to local from sourceTree

![[Pasted image 20230419104013.png]]

## [Git – Remove a git commit which has not been pushed]

**IF you have NOT pushed your changes to remote**

```
git reset HEAD~1
```

Check if the working copy is clean by `git status`.

**ELSE you have pushed your changes to remote**

```
git revert HEAD
```

This command will revert/remove the local commits/change and then you can push

Stash

```cmd
git stash list
git stash apply stash@{0}
```

## Ignore Global

有時候第一次未加或者修改的時候.gitignore 忽略的文件不會生效，這個時候只能刪除所有緩存重新提交。

1. Step1.點擊右上角的終端進入項目目錄
2. Step2.刪除所有緩存
   `git rm -r --cached .`
3. Step3.重新添加所有文件：`git add .`
4. Step4.提交`git commit -m 'ignore something'`

## Ignore Files

--assume-unchanged 假定開發人員不會更改文件。此標記旨在為無變化文件夾（如 SDK）改善性能。
--skip-worktree 用於命 GIT 不再染指特定文件——即便開發人員可能更改它——的情形。例如，如果主源碼庫上遊承載某些即將投入生產的配置文件而妳不希望意外的提交影響到那些文件，--skip-worktree 正是妳的菜。

### Ignore files locally without committing ignore rules

`.gitignore` ignores files locally, but it is intended to be committed to the repository and shared with other contributors and users. You can set a global `.gitignore`, but then all your repositories would share those settings.

If you want to ignore certain files in a repository locally and not make the file part of any repository, edit `.git/info/exclude` inside your repository.

```
# these files are only ignored on this repo
# these rules are not shared with anyone
# as they are personal
*.iml
```

### `fas:ExclamationTriangle`With command following command if previous solution is not work

```git
git update-index --assume-unchanged .idea/misc.xml
git update-index --assume-unchanged .idea/jarRepositories.xml
git update-index --assume-unchanged .idea/encodings.xml
git update-index --assume-unchanged .idea/compiler.xml
```

```git
git update-index –no-assume-unchanged
```

### undo with following command

```
git update-index --no-assume-unchanged <filename>
```

### SKIP workTree

```bash
git update-index --skip-worktree
```

```bash
git update-index –no-skip-worktree
```

```bash
git update-index --skip-worktree optimus-web-ui/package.json
git update-index --skip-worktree optimus-web-ui/package-lock.json
git update-index --skip-worktree optimus-web-ui/proxy.conf.json
git update-index --skip-worktree optimus-web-ui/.vscode/settings.json
git update-index --skip-worktree optimus-web-ui/src/env.js
git update-index --skip-worktree optimus-web-ui/src/app/optimus/optimus-routing.module.ts
```

Git ignore

```bash
git ls-files -v . | findstr "^S"
```

### 如果某些檔案已經被納入了版本管理中，則修改.gitignore 是無效的

```git
git rm -r --cached .
git add .
git commit -m 'update .gitignore'
```

## Customized action in sourcetree to ignore files

https://www.codeproject.com/Articles/5363987/Git-SourceTree-Custom-Actions-for-skip-worktree-Op

![[Pasted image 20230922095606.png]]

![[Pasted image 20230922095437.png]]

![[Pasted image 20230922095531.png]]  
.bat
`"C:\jay\Git\bin\git.exe" ls-files -v | "C:\WINDOWS\system32\findstr.exe" /b S`

## How to fix the issue that SoureTree cannot open

Go the location `C:\Users\w114211\AppData\Local\Atlassian` and delete other folders which name is not `SourceTree`

## Hunk

![[Pasted image 20230719175011.png]]

## How to make all remote commit as one

To squash commits 'b' and 'c' into a single commit using Sourcetree, you can follow these steps:

1. **Open Repository in Sourcetree:**
   Open Sourcetree and navigate to your repository.

2. **Switch to the Branch:**
   Make sure you are on the branch where commits 'b' and 'c' are located.

3. **Start Interactive Rebase:**
   In Sourcetree, click on the "Terminal" option in the top menu bar. This will open a terminal window.

4. **Enter Interactive Rebase:**
   In the terminal window, enter the following command to start an interactive rebase on the branch:

    ```shell
    git rebase -i HEAD~3
    ```

    This command will open an interactive rebase window that shows the last 3 commits (including 'a', 'b', and 'c').

5. **Squash Commits:**
   In the interactive rebase window, you'll see a list of commits with the word "pick" in front of them. For commit 'b' and 'c', change the word "pick" to "squash" or "s". For example:

    ```shell
    pick a123456 Commit message for commit a
    squash b789012 Commit message for commit b
    squash c345678 Commit message for commit c
    ```

    Save and close the rebase window.

6. **Edit the New Commit:**
   After saving and closing the rebase window, another window will open allowing you to edit the new commit message. Keep the commit message that you want for the combined commit 'b' and 'c', and then save and close the window.

7. **Complete the Rebase:**
   Once you've saved the new commit message, the rebase will continue. Git will combine commits 'b' and 'c' into a single commit and place it after commit 'a'.

8. **Push the Changes:**
   After the rebase is complete, you will need to force push the changes to the remote branch. In Sourcetree, click the "Push" button to push the changes. You might need to force push by checking the "Force push" option, as the commit history has been rewritten.

Please note that interactive rebasing and force pushing can rewrite commit history, so make sure you communicate with your team if you're collaborating on the same branch. Also, keep backups of your changes in case something goes wrong during the rebase process.

## Cherry-pick

Pick commit from another branch to current branch, with or without commits files.
![[Pasted image 20230904111728.png]]

## Branch

![[35ldbo8r5w.png]]

## Squash

![[Pasted image 20230906105023.png]]

## pull remote into local without checkout

```shell
git fetch origin branchName:branchName
```

## Rebase on top of another branch..

![[Pasted image 20231019111406.png]]

# Amend

Push code with `force` command in order to amen the last commit
![[Pasted image 20231206172415.png]]

# Sourcetree red exclamation mark

![[Pasted image 20240131170301.png|500]]  
![[Pasted image 20240131170313.png]]  
![[Pasted image 20240131170324.png]]

# ADD SSH KEY to gitLab or github

`ssh-keygen -t ed25519 -C "your_email@example.com"`

`ssh-keygen -t ed25519 -C "your_email@example.com"`

//rename file if need, ex:id_ed25519_github

`eval "$(ssh-agent -s)"` //with git brash

`ssh-add ~/.ssh/id_ed25519_github`

`ssh-add ~/.ssh/id_ed25519_gitlab`


![[Pasted image 20241213180358.png]]

`touch config`

```
# GitHub
Host github.com
  HostName github.com
  User git
  IdentityFile ~/.ssh/id_ed25519_github

# GitLab
Host gitlab.com
  HostName gitlab.com
  User git
  IdentityFile ~/.ssh/id_ed25519_gitlab
```


`Copy the SSH Key from xx.pub and paste to github/gitlab`