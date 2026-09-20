# Git 工作流

电脑文件
   |
   | git add
   ↓
暂存区(Stage)
   |
   | git commit
   ↓
版本库(Local Repository)（本地仓库）
   |
   | git push
   ↓
远程仓库(GitHub)

## 1. 查看状态

```
git status
```

作用：

> 查看哪些文件变了

例如：

```
modified:
    User.cpp
```

说明：

你的代码改了，但还没提交。

## 2. 添加修改

全部添加：

```
git add .
```

意思：

> 把当前所有修改放入暂存区

添加单个文件：

```
git add src/User.cpp
```
## 3. 提交版本

```
git commit -m "Implement User class"（这是提交信息）
```

意思：

> 给当前状态拍一张照片

例如：

第一次：

```
Initialize project
```

第二次：

```
Add User entity
```

第三次：

```
Implement repository layer
```
## 4. 上传 GitHub

```
git push
```

作用：

本地版本 → GitHub

# 三、查看历史

## 查看提交记录

```
git log
```

例如：

```
commit 83ab92

Author: W1-heng

Implement User class
```

---

更常用：

```
git log --oneline
```

结果：

```
a82f91 Initialize project
c123ab Add User class
```

一行一个版本。

---

# 四、撤销操作（非常重要）

## 1. 修改了文件，但是不要了

例如：

你改坏了：

```
User.cpp
```

恢复：

```
git restore src/User.cpp
```

回到上一次提交。

---

## 2. add错文件

比如：

你：

```
git add .
```

结果发现：

```
test.exe
```

也进去了。

取消暂存：

```
git restore --staged test.exe
```

文件还在，只是不提交。

---

## 3. commit写错了

例如：

```
git commit -m "aaa"
```

发现名字写错。

修改最近一次提交：

```
git commit --amend
```

---

# 五、分支（以后项目很重要）

现在：

```
main
```

就是主线。

创建开发分支：

```
git branch dev
```

切换：

```
git checkout dev
```

或者新版：

```
git switch dev
```

结构：

```
main
 |
 A---B---C

dev
       \
        D---E
```

以后：

main：

稳定版本

dev：

开发版本

---

# 六、查看差异

非常常用：

```
git diff
```

看：

> 我改了什么

例如：

```
- int age;
+ int id;
```

---

# 七、远程仓库

查看：

```
git remote -v
```

例如：

```
origin https://github.com/xxx/project.git
```

添加远程仓库：

```
git remote add origin 地址
```

---

# 八、删除文件

删除并提交：

```
git rm 文件
```

例如：

```
git rm test.cpp
```




|命令|作用|使用场景|示例|
|---|---|---|---|
|`git init`|初始化 Git 仓库|新项目第一次使用 Git|`git init`|
|`git clone`|克隆远程仓库|下载 GitHub 项目|`git clone url`|
|`git status`|查看当前状态|每次操作前建议先看|`git status`|
|`git add`|添加文件到暂存区|准备提交修改|`git add src/User.cpp`|
|`git add .`|添加所有修改到暂存区|提交一批修改|`git add .`|
|`git restore 文件`|丢弃工作区修改|改坏代码，恢复上次提交|`git restore User.cpp`|
|`git restore --staged 文件`|移出暂存区|add错文件，但保留修改|`git restore --staged test.cpp`|
|`git commit`|创建版本|保存一次历史记录|`git commit`|
|`git commit -m "说明"`|带提交信息创建版本|最常用提交方式|`git commit -m "Add User class"`|
|`git commit --amend`|修改最近一次提交|修改提交信息/补充文件|`git commit --amend`|
|`git log`|查看提交历史|查看完整版本记录|`git log`|
|`git log --oneline`|简洁查看历史|日常查看版本|`git log --oneline`|
|`git diff`|查看工作区修改|看自己改了什么|`git diff`|
|`git diff --staged`|查看暂存区修改|提交前检查|`git diff --staged`|
|`git rm`|删除文件并记录|删除项目文件|`git rm old.cpp`|
|`git mv`|移动/重命名文件|Git追踪文件变化|`git mv a.cpp b.cpp`|


**远程仓库相关**

| 命令                        | 作用         | 示例                          |
| ------------------------- | ---------- | --------------------------- |
| `git remote -v`           | 查看远程仓库地址   | `git remote -v`             |
| `git remote add origin`   | 添加远程仓库     | `git remote add origin url` |
| `git push`                | 上传提交到远程    | `git push`                  |
| `git push -u origin main` | 第一次绑定远程分支  | `git push -u origin main`   |
| `git pull`                | 拉取远程更新     | `git pull`                  |
| `git fetch`               | 获取远程信息但不合并 | `git fetch`                 |
**分支相关**

| 命令                 | 作用                         | 示例                           |
| ------------------ | -------------------------- | ---------------------------- |
| `git branch`       | 查看分支                       | `git branch`                 |
| `git branch 名称`    | 创建分支                       | `git branch dev`             |
| `git switch 分支`    | 切换分支                       | `git switch dev`             |
| `git checkout 分支`  | 老版本切换分支                    | `git checkout dev`           |
| `git switch -c 名称` | 创建并切换                      | `git switch -c feature-user` |
| `git merge 分支`     | 合并分支（最后呈现网状结构，“支路会合”）      | `git merge dev`              |
| `git branch -d 名称` | 删除分支                       | `git branch -d dev`          |
| git rebase `分支`    | 合并分支（将分支插入在合适的位置，最终呈现线性结构） |                              |


**配置相关**

|命令|作用|示例|
|---|---|---|
|`git config --global user.name`|设置用户名|`git config --global user.name "W1-heng"`|
|`git config --global user.email`|设置邮箱|`git config --global user.email "xxx@qq.com"`|
|`git config --list`|查看配置|`git config --list`|