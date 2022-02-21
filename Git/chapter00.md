|                                            |          |
| ------------------------------------------ | -------- |
| <a>git config -l</a>                       | 查看配置 |
| <a>git config --global user.name "xxx"</a> |          |
| <a> git config --global user.email ""</a>  |          |
| <a>ssh-keygen</a>                          | 生成秘钥 |
|                                            |          |

![image-20211130192228941](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211130192228941.png)

![image-20211130200735833](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211130200735833.png)



> **<a>文件的4中状态</a> **
>
> <a>**Untracked**</a> 未跟踪，此文件在文件夹中，但没有加入到git库，不参与版本控制。通过<a>git add</a> 变为<a>Staged</a> 
>
> <a>**Unmodify**</a> 文件已入库，未修改，即版本库中的文件快照内容与文件夹中完全一致，这种类型的文件有两种去处，如果它被修改，变为<a>Modified</a> ,如果使用<a>git rm</a> k移除版本库，则成为<a>Untracked</a> 文件
>
> <a>**Modified**</a> 文件已修改，仅仅是修改，没有其他操作。也有两个去处。通过git add进入暂存<a>staged</a> ,使用<a>git checkout</a> 则丢弃修改，返回到<a>unmodify</a> 状态，git checkout 即从库中去处文件，覆盖当前修改。
>
> <a>**Staged**</a> 暂存状态。执行 git commit 则将修改同步到库中，这时库中的文件又和本地文件一致，文件为<a>Unmodify</a> 状态



> **忽略添加到暂存区 .gitignore文件**
>
> 1. 忽略文件的注释(#)
>
> 2. 可以使用Linux通配符，例如
>
>    <a>*</a> 表示多个字符，<a>?</a> 表示一个字符，<a>[abc]</a> 表示可选字符范围，<a>{String 1,String 2}</a> 代表可选字符串等
>
> 3. 如果名称的最前面由一个<a>!</a> ，表示例外规则，不被忽略
>
> 4. 如果名称的最前面是一个路径分割符 <a>/</a> ,表示要忽略的文件在此目录下，而子目录的文件不忽略
>
> 5. 如果名称的最后面是一个路径分隔符<a> /</a> ,表示要忽略的是此目录下该名称的子目录，而非文件，默认文件或目录都忽略。

```properties
# 注释
*.txt       #忽略所有.txt结尾的文件
!lib.txt    #除该文件外都忽略
/temp       #仅忽略项目根目录下的TODO文件，不包括其他目录temp
build/ 		#忽略build/ 目录下的所有文件
doc/*.txt	#忽略 doc/notes.txt 但不包括 doc/server/arch.txt
```





#### 修改远程仓库在本地的别名(origin)

```html
//方法一
git remote rm origin
git remote add origin <url>
```

```html
git remote origin set-url <url>
```





#### 跨团队

![image-20211203201946590](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211203201946590.png)

### 尚硅谷

> 未追踪的文件只能用<a>git add</a> 添加到暂存区，已追踪的文件，可以先添加到暂存区再提交，也可以<a>git commit -a</a> 直接提交 

|                                                        |                                   |
| ------------------------------------------------------ | --------------------------------- |
| <a>git --oneline</a>                                   | 查看历史记录                      |
| <a>git reflog</a>                                      | HEAD@{1}:移动到历史版本需要的步数 |
| <a>git reset --hard xxxxxxx</a>                        | 回到历史版本(基于索引值操作)      |
| <a>git reset --hard HEAD^[^..]</a>                     | 只能回退                          |
| <a>git diff [fileName]</a>                             | 比较                              |
| <a>git diff HEAD^[...] [fileName]</a>                  | 和历史版本比较                    |
| <a>git diff [版本号] [fileName]</a>                    | ..                                |
| <a>git branch -v</a>                                   | 查看分支                          |
| <a>git branch [分支名]</a>                             | 创建分支                          |
| <a>git checkout [分支名]</a>                           | 切换分支                          |
| 1.切换到接收的修改的分支上2. <a>git merge [分支名]</a> | 分支合并                          |
| <a>git remote add origin https://..</a>                | 添加远程库地址                    |
| <a>git clone 远程库地址</a>                            |                                   |

#### 冲突 

> **冲突的产生**： 多个分支都对同一当前文件进行修改，且修改内容不同

![image-20211204094722637](C:\Users\Simon Kerola\AppData\Roaming\Typora\typora-user-images\image-20211204094722637.png)

> **冲突的解决**
>
> 1. 删除特殊符号，修改文件
>
> 2. git add [fileName]
>
> 3. git commit -m "xxx"
>
>    (这里来的commit不用带文件名)

```properties
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
```

```html
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  //qq
```

```
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
```

