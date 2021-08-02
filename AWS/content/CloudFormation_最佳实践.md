# 模块化组织stack

最好不要将所有的resource都放到一个stack中，最好按照不同的目的将resource分类，不同的类别放到放到不同的stack中

**组织stack的方式有以下两种：**

- 多层架构

  就是将所有stack组织划分成各个层级，每个层级都各自依赖于其他层级，每个层级又可以有多个stack

- 面向服务架构(SOA)

  根据业务逻辑，将不同的业务划分为不同的service，然后根据需求将不同的service整合到同一个stack中，这些stack之间相互协同

# 使用跨stack引用导出共享resource

如果一个stack中需要使用另外一个stack里的resource，可以通过hard code或参数的方式实现，但是这样会使Template的维护变得困难

如果通过跨stack引用的方式，将一个stack中的resource导出，这样其他stack就可以引用导出的resource了

使用ImportValue函数可以其他stack导出的resource

# 验证所有类型reource的配额

在启动stack之前，需要确保所创建的所有resource不要超过账户对应的resource上限

比如，默认一个账号在一个region可创建最多200个stack，但如果该账号创建的stack数超过了200，则CloudFormation在创建这些stack时会失败，直到把账号的200个stack上限提高

# 多环境下复用Template

Template在dev环境开发好后，还要在tst环境测试，并最终发布到prd环境

但是不同的环境下，设置可能会有不同，比如dev环境所需要的配置可能会低于prd或tst环境，但环境配置外的部分应该都是不变的

因此，将配置部分通过参数，mapping等方式做自定义化，这样同一个Template可以在三个环境同时使用了

# 使用模块复用resource配置

随着使用资源不断增长，可以将一些共通的组件整合到一起，使用Modules是一个很好的方式

# 使用Amazon特定的参数类型

如果Template要求既存的Amazon特定值作为参数，则使用Amazone特定的参数类型，这样CloudFormation可以很快的通过验证

# 使用参数约束

通过使用参数约束，CloudFormation可以捕捉到不合法的参数

# 使用AWS::CloudFormation::Init来发布软件应用到EC2实例

# 使用最新版本的helper scripts

# 使用Template前先验证

使用Template创建stack之前，请先通过CloudFormation验证下Template是否有错误，包括语法错误，依赖关联等

CloudFormation 首先先验证Template是否是合格的json格式，如果不是则验证是否是合格的yaml格式，若都不是，则会返回验证错误

# 验证Template是否符合公司的安全要求

# 通过CloudFormation来管理所有stack资源

不要通过CloudFormation之外更新调整resource，这样会使用=resource和Template的描述不一致，从而导致通过Template更新resource时会报错

# 在更新stack前创建change set

更新stack之前创建change set，即可通过检查change set确定本次更新是否符合预期及评估影响

# 使用stack policy

使用stack policy可以防止一些重要的resource不被更新，因为有些重要的resource更新时会影响业务

比如数据库实例，更新是会停止运行

stack policy就是一个json文档，用于描述在哪些resource上会执行哪些操作

在stack更新过程中，必须显式的指定哪些被保护的resource是要被更新的，否则这些被保护的resource是不会被更新的

# 通过代码review及版本控制管理Template

像管理代码源文件一样，管理Template

review Template可确保质量，同时确保历史各个时期版本的完整，保证出问题时可以追根朔源

# 定期更新EC2 linux实例

对于所有EC2 linux实例以及通过CloudFormation创建的EC2 linux实例，要定期通过yum update命令更新，确保获得最新的版本