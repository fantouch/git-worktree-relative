# git-worktree-relative


## Background

- Feature request from 2016 but not implemented yet (as in 2021-03-01) https://public-inbox.org/git/CACsJy8AZVWNQNgsw21EF2UOk42oFeyHSRntw_rpeZz_OT1xdMw@mail.gmail.com/T/
- There are other solution which use go, but not everyone want to install go and compile their own tools (or maybe just cannot be bothered to) https://github.com/harobed/fix-git-worktree (update: alternative bash solution exist in https://gitlab.com/kstr0k/git-worktree-path)
- Even if this feature is not really popular https://stackoverflow.com/questions/66635437/git-worktree-with-relative-path only have 200 views in 4 month since asked (as in 2021-08-01)


## My solution

- Bash script to replace the content of `{worktree}/.git file` and `{repo}/.git/worktrees/{wtname}/gitdir`
- Why bash: almost everyone who use git will use it in some kind of bash-shell-like environment (ex: bash shell in linux, git bash in windows)
- Requirements (should be available on every bash shell):
  - `cat`
  - `echo`
  - `readlink`
  - `tac` and `realpath` (GNU utility / `coreutils` since 2012, might not be preinstalled in very old linux system like debian wheezy or macOS)
  - `sed`
  - `pwd`
  - bash shell parameter expansion `${parameter/pattern/string}` and `${parameter%%word}` https://www.gnu.org/software/bash/manual/bash.html#Shell-Parameter-Expansion
- Another bash script to change it back to absolute path (since `git worktree remove` may refuse on relative path)


## Usage

- Execute the script in your worktree (or supply the worktree directory path in -w options)
- It will read path to repository from `{worktree}/.git` file
- Options:
  - `-v` = verbose
  - `-d` = dry run (do not write any change, use with verbose to show what this script do)
  - `-w worktree_target` = directory of worktree to be made relative (will default to current directory if not supplied)
  - `-r repository_target` = directory of repository (including worktree directory inside .git, will be read from {worktree_target}/.git file if not supplied)
  - `-h` = show help
- This solution works for broken link (ex: worktree directory moved OR parent git directory moved): just supply the repository path in `-r repositor_target` flag
- This solution works for worktree inside parent repository
- example:
  - repository in `/home/myuser/repo/myproject` ; worktree in `/home/myuser/www/myproject` ; worktree is connected with repository (link is not broken)
    ```bash
    cd /home/myuser/www/myproject
    git worktree-relative
    # OR
    git worktree-relative -w /home/myuser/www/myproject
    ```
  - repository in `/home/myuser/repo/myproject` ; worktree in `/home/myuser/www/myproject` ; worktree is NOT connected with repository (link broken)
    ```bash
    cd /home/myuser/www/myproject
    git worktree-relative -r /home/myuser/repo/myproject/.git/worktrees/myproject
    # OR
    git worktree-relative -w /home/myuser/www/myproject -r /home/myuser/repo/myproject/.git/worktrees/myproject
    ```
  - to detect if link is broken, run command 'git status' in worktree directory
- Reversing relative worktree back to absolute: just change `git worktree-relative` command with `git worktree-absolute` (same command line argument)
  - command `git worktree remove` requires the path to be absolute: you can use this reverse script to revert it back to absolute path before removing

## Installation

#### Automatic Installation

- copy paste below command into your terminal:
```bash
git clone https://github.com/Kristian-Tan/git-worktree-relative.git
cd git-worktree-relative
sudo bash install.sh
```
- or this one-line: ```git clone https://github.com/Kristian-Tan/git-worktree-relative.git ; cd git-worktree-relative ; sudo bash install.sh```
- or another one-line: ```/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Kristian-Tan/git-worktree-relative/HEAD/get)"```

#### Manual Installation

- installation for all users:
  - copy `git-worktree-relative.sh` and `git-worktree-absolute.sh` to `/usr/bin` or `/bin` (you can also remove the extension)
  - give other user permission to execute it
  - example:
  ```bash
    cp git-worktree-relative.sh /usr/bin/git-worktree-relative
    cp git-worktree-absolute.sh /usr/bin/git-worktree-absolute
    chown root:root /usr/bin/git-worktree-relative
    chown root:root /usr/bin/git-worktree-absolute
    chmod 0755 /usr/bin/git-worktree-relative
    chmod 0755 /usr/bin/git-worktree-absolute
  ```
- installation for one user:
  - copy it to any directory that is added to your PATH variable

#### Uninstallation

- just remove copied files (or just use uninstall.sh script: ```git clone https://github.com/Kristian-Tan/git-worktree-relative.git ; sudo bash git-worktree-relative/uninstall.sh```)
- or another one-line: ```/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Kristian-Tan/git-worktree-relative/HEAD/remove)"```

##### Installation on macOS

- installation on macOS might encounter problem such as `tac: command not found` or `readlink: illegal option -- f`
- the cause is because macOS does not have `tac` and have their own implementation of `readlink`
- easiest workaround is by installing gnu coreutils with homebrew:
  ```bash
    brew install coreutils
    ln -s $HOMEBREW_PREFIX/bin/greadlink $HOMEBREW_PREFIX/bin/readlink
  ```
- after that, you can continue installation and usage as normal

## Contributing

- Feel free to create issue, pull request, etc if there's anything that can be improved


## Credits

- [REMOVED] Bash implementation of strpos and substr by BR0kEN- (https://gist.github.com/BR0kEN-/a84b18717f8c67ece6f7)
- StackOverflow user `usretc` for advise in https://stackoverflow.com/q/66635437/3706717 

