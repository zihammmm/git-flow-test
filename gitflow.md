## 流程
+ 不同的feature在不同的分支上开发
+ 确定版本号和此版本要发布的功能后，将相应的feature分支统一想develop分支合并，然后拉出新的release预发布分支
+ release分支作为持续集成关注的分支，修复bug
+ 待release分支测试通过后，统一向master分支和develop分支合并，并在master分支打tag
+ 根据tag发布apk版本

若线上发现严重bug，走hotfix流程
+ 基于master分支拉出hotfix分支修复线上问题
+ bug修复完成统一向master和develop分支合并
+ master分支打上新的tag，发布新版本


## 分支
### 长期分支
+ master:主分支，存储了正式发布的历史，保存随时可发布的代码，所有的hotfix分支都是从master分支上拉，所有到master的提交都必须有tag。不允许直接进行commit，只接受merge request
+ develop:主开发分支。作为功能的集成分支，功能最新最全的分支，所有的feature、release分支都是从develop分支上拉。feature分支完成开发后，也需要合并到该分支。

## 初始化
先将远程仓库克隆到本地，然后初始化gitflow配置
``` 
git clone xxx
git flow init
```
开发新功能时创建一个feature分支  
```
git flow feature start gitworkflow
```

## 行为
一种场景的完整生命周期：
+ start 开始开发
+ publish 发布到远程分支
+ finish 完成开发、合并到主分支

### start
在本地创建名为<feature_name>的分支，并切换到该分支(基于本地的develop分支创建)
```
git flow feature start <feature_name>
```
### publish
在本地开发完成新功能并commit后，需要将本地代码push到远程仓库
```
git flow feature publish <feature_name>
```
该指令执行了以下操作：
+ 创建一个名为feature/<feature_name>的远程分支
+ 本地分支track上述远程分支
+ 如果本地有未push代码，则执行push

**注意：**如果已经执行了publish后又有新代码需要push，再次执行会报错，因为它会试图创建一个远程分支。此时需要执行正常的push命令:git push origin

### finish
当功能开发完毕后将进入测试阶段，此时需要将一个或多个feature分支统一合并到develop分支。 
```
git flow feature finish <feature_name>
```
该指令做了如下操作：  
+ 切换到develop分支
+ 合并代码到develop分支
+ 删除本地feature/<feature_name>分支

在merge时，gitflow会检查本次merge有多少次commit记录，若仅有一条则采用ff模式，否则采用no-ff模式，会多生成一条merge的commit记录。

### release
新功能开发完毕，将进入测试阶段，此时需要基于develop分支拉出release分支进行集成测试，也有将release场景作为预发布环境进行测试。
```
git flow release start v2.0
```
此命令会基于本地的develop分支创建一个release/v2.0分支，并切换到这个分支上  
为了让其他协同人员看到该分支，需要将该分支发布出去  
```
git flow release publish v2.0
```
测试通过后需要发布正式版：  
```
git flow release finish v2.0
```
该命令执行以下操作：  
+ git fetch
+ release/v2.0分支代码向master合并
+ 生成名为v2.0的tag
+ release/v2.0 分支代码向develop合并
+ 删除本地release/v2.0分支
+ 切换回develop分支

如果merge产生冲突不会终止流程，不会将本地的release分支删除，待解决完冲突后需再次执行finish指令。  
如果本地还有未finish的release分支，将不允许使用start指令开启新的release分支。  
然后还需要同步到远程仓库。

### hotfix
正式版本发布后，如果线上发现了严重的bug，需要进行紧急修复。  
假设版本号变为v2.0-patch。  
```
git flow hotfix start v2.0-patch
```
这将从master分支fork一个hotfix/v2.0本地分支并切换到该分支。hotfix没有publish指令，因为hotfix应该是一个小范围的改动，不需要其他人员参与。  
待本地修改结束commit后，执行finish指令。  
```
git flow hotfix finish v2.0-patch
```
该指令会执行以下操作：
+ git fetch
+ hotfix/v2.0-patch分支代码向master合并
+ 生成名为v2.0-patch的tag
+ hotfix/v2.0-patch分支代码向develop合并
+ 删除本地hotfix/v2.0-patch分支
+ 切换回develop分支

## gitflow有害论
