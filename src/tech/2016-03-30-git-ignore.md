### git 忽略本地修改

Django 需要修改数据库信息，改完文件可以用以下命令忽略文件。

```bash
git update-index --skip-worktree [<file>...]
```

### git 忽略已经跟踪的文件

```bash
git ls-files --ignored --exclude-standard | sed 's/.*/"&"/' | xargs git rm -r --cached
```

### git 彻底删除文件

```bash
git filter-branch --force --index-filter \
'git rm --cached --ignore-unmatch PATH-TO-YOUR-FILE-WITH-SENSITIVE-DATA' \
--prune-empty --tag-name-filter cat -- --all
```

删完记得将文件加入 .gitignore

