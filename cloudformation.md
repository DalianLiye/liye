# **关于CloudFormation Template**

Template就是一个text文本文件，文件内容的格式是json或yaml

Template里声明了要在Amazone创建或配置的resource，这些resource相互关联，组成了stack

Template声明顶层对象包括以下：

- **AWSTemplateFormatVersion：**可选

- **Description：**可选
- **Parameters：**可选
- **Mappings：**可选
- **Resources：**必须，Template至少要声明一个resource
- **Outputs：**可选

# **AWSTemplateFormatVersion对象**

用于指定Template的版本，如果不指定，则CloudFormation会使用最新的版本

# **Description对象**

用于对Template添加描述信息，可以是任何json或yaml格式的文本

# Parameters对象

用于声明向Template传递的参数

通过参数，可以避免将敏感信息嵌入到template里

Parameters对象可以包含多个parameter对象，每个parameter对象又可以有多个attribute，其中type attribute是必须项

Default attribute表示默认值，Default attribute如果没有指定，则必须通过参数传递过来，否则创建stack时会报错

------

**NoEcho**

通过指定NoEcho attribute为true，可以将返回的参数信息做Data Masking处理(****)

**NoEcho不会对以下attribute做Data Masking处理**

- Metadata Section，CloudFormation不会对metadata里的任何信息做转换，修改或编辑处理

- Outputs Section

- Resouce Section的metadata attribute

**注：**强烈建议不要直接在CloudFormation Template里嵌入敏感信息(密码,验证信息等)，可将敏感信息存储在Template之外(比如Amazon Systems Manager Parameter Store 或者Amazon Secrets Manager)，再将敏感信息以动态传参的方式传入Template

------

**参数的类型为以下：**

- String

- Number

- Amazon的特定类型

------

**通过以下两种方式验证参数的有效性**：

- **声明约束(constraints)** 

  参数若是String类型，可以使用以下Attribute用于约束

  - MinLength

  - MaxLength
  - Default
  - AllowedValues
  - AllowedPattern

  参数若是Number类型，数字可以是整型或浮点型，可以使用以下Attribute用于约束

  - MinValue
  - MaxValue
  - Default
  - AllowedValues

- **为parameter的type attribute 指定Amazon的特定类型**

  参数若是Amazon的特定类型，CloudFormation在创建stack资源之前，会根据用户帐户和要创建的stack的区域中的现有值验证参数值

# **Resources对象**

Resources对象至少要包含一个reosurce对象

每一个resource对象都有对应的attribute，其中type attribute是必须的

每个attribute都是resource的子对象，它们都是自描述的

type attribute的格式是：

```yaml
AWS::ProductIdentifier::ResourceType
```

比如Amazon S3 bucket的resource type是

```yaml
AWS::S3::Bucket
```

------

不同type的resource会有不同的attribute，它们有的是必须的，有的是可选的，要根据实际情况而定，当没有显式的指定时，会使用默认值	

比如创建bucket时，如果没有指定其他的attribute，则CloudFormation会使用默认值的设置这些attribute

```yaml
Resources:
  HelloBucket:
    Type: AWS::S3::Bucket
```

------

在resource的声明里，可以使用Properties attribute来指定创建resource时需要的一些信息

不同type的resource可以有不同的property

每个resource的Properties可以有多个子项，每个子项的值都是string，比如：

```yaml
Resources:
  HelloBucket:
    Type: AWS::S3::Bucket
    Properties:
      AccessControl: PublicRead
      WebsiteConfiguration:
        IndexDocument: index.html
        ErrorDocument: error.html
```

------

CloudFormation Template可以创建多个resource，这些resource可以相互配合工作从而搭建起一个应用或解决方案

resource声明里的名称是逻辑名称，当CloudFormation创建这些resource时，会将逻辑名，stack名和unique id拼接起来作为这些资源的物理名

------

**以下attribute是可以在任何resource里作为可选attribute**

- **DependsOn：**用于指定该resource依赖于哪个resource

- **DeletionPolicy** **：** 用于指定CloudFormation如何执行删除resource操作

- **Metadata：**用于指定使用resource构建的结构化数据

# **Mappings对象**

类似java里的switch分支语句，可以根据需要，通过不同的输入值返回不同的结果

需要使用FindInMa函数

```yaml
Mappings:
  RegionMap:
    us-east-1:
      AMI: ami-76f0061f
    us-west-1:
      AMI: ami-655a0a20
    eu-west-1:
      AMI: ami-7fd4e10b
    ap-southeast-1:
      AMI: ami-72621c20
    ap-northeast-1:
      AMI: ami-8e08a38f
Resources:
  Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      KeyName: !Ref KeyName
      ImageId: !FindInMap 
        - RegionMap
        - !Ref 'AWS::Region'
        - AMI
      UserData: !Base64 '80'
```

# 伪参数

## **AWS::Region**

用于获取创建stack所在的region

## **AWS::CloudFormation::Stack**

将其他stack作为一个resource嵌入到template中

# 函数

## **!Ref函数**

用于指定某一个resource

```yaml
Parameters:
  KeyName:
    Description: The EC2 Key Pair to allow SSH access to the instance
    Type: 'AWS::EC2::KeyPair::KeyName'
Resources:
  Ec2Instance:
    Type: 'AWS::EC2::Instance'
    Properties:
      SecurityGroups:
        - !Ref InstanceSecurityGroup
        - MyExistingSecurityGroup
      KeyName: !Ref KeyName
      ImageId: ami-7a11e213
  InstanceSecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Enable SSH access via port 22
      SecurityGroupIngress:
        - IpProtocol: tcp
          FromPort: '22'
          ToPort: '22'
          CidrIp: 0.0.0.0/0
```

## **!GetAZs函数**

用于获取一个region下所有可用的zones列表

## **!GetAtt函数**

用于获取resource的指定attribute

有两个参数，第一个参数是要获取attribute所在的resource，第二个参数是想要获取的resource的attribute

```yaml
Resources:
  myBucket:
    Type: 'AWS::S3::Bucket'
  myDistribution:
    Type: 'AWS::CloudFront::Distribution'
    Properties:
      DistributionConfig:
        Origins:
          - DomainName: !GetAtt 
              - myBucket
              - DomainName
            Id: myS3Origin
            S3OriginConfig: {}
        Enabled: 'true'
        DefaultCacheBehavior:
          TargetOriginId: myS3Origin
          ForwardedValues:
            QueryString: 'false'
          ViewerProtocolPolicy: allow-all
```

## **!FindInMap函数**

用于匹配mapping的值

## **!Join函数**

用于拼接值

有两个参数，第一个参数是拼接符，第二个参数是一个数组

返回结果是用第一个参数表示的拼接符，按照第二个参数表示的数组中元素的顺序将各个元素拼接起来

**例：**

```yaml
HealthCheck:
        Target: !Join 
          - '-'
          - - 123
            - 456
            - 789
```

结果：Target的值是123-456-789

## !ImportValue函数

引用其他stack导出的resource

# 更新Template

Template中的resource是可以更新的

基本操作就是在本地将Template更新好后，通过console上传，CloudFormation会查看并更新stack

CloudFormation只会对更新部分做更新，未更新部分仍会保持原样

------

**当更新Template时，需要考虑以下几点：**

- 当需要更新resource时，是否会对resource本身后者其他关联的resource有影响，比如更新instance type时，需要先停止instance然后再启动，这样可能就会对业务有影响
- 确定更新是可变还是不可变的，一些resource的property的更新是不被底层的服务支持的；如果property支持更新，则底层服务使用modify或update类型的api；如果property不支持更新，则会用更新的属性创建新的resource，并链接到stack中，最后删除就的resource；在stack重新配置期间，应用将无法完全运行，比如请求和后台访问等

# Security

Cloud Security是Amazone最高的层级的Security

# 技巧

## 模块化组织stack

最好不要将所有的resource都放到一个stack中，最好按照不同的目的将resource分类，不同的类别放到放到不同的stack中

**组织stack的方式有以下两种：**

- 多层架构

  就是将所有stack组织划分成各个层级，每个层级都各自依赖于其他层级，每个层级又可以有多个stack

- 面向服务架构(SOA)

  根据业务逻辑，将不同的业务划分为不同的service，然后根据需求将不同的service整合到同一个stack中，这些stack之间相互协同

## 使用跨stack引用导出共享resource

如果一个stack中需要使用另外一个stack里的resource，可以通过hard code或参数的方式实现，但是这样会使Template的维护变得困难

如果通过跨stack引用的方式，将一个stack中的resource导出，这样其他stack就可以引用导出的resource了

使用ImportValue函数可以其他stack导出的resource

## 验证所有类型reource的配额

在启动stack之前，需要确保所创建的所有resource不要超过账户对应的resource上限

比如，默认一个账号在一个region可创建最多200个stack，但如果该账号创建的stack数超过了200，则CloudFormation在创建这些stack时会失败，直到把账号的200个stack上限提高

## 多环境下复用Template

Template在dev环境开发好后，还要在tst环境测试，并最终发布到prd环境

但是不同的环境下，设置可能会有不同，比如dev环境所需要的配置可能会低于prd或tst环境，但环境配置外的部分应该都是不变的

因此，将配置部分通过参数，mapping等方式做自定义化，这样同一个Template可以在三个环境同时使用了

## 使用模块复用resource配置

随着使用资源不断增长，可以将一些共通的组件整合到一起，使用Modules是一个很好的方式

## 使用Amazon特定的参数类型

如果Template要求既存的Amazon特定值作为参数，则使用Amazone特定的参数类型，这样CloudFormation可以很快的通过验证

## 使用参数约束

通过使用参数约束，CloudFormation可以捕捉到不合法的参数

## 使用AWS::CloudFormation::Init来发布软件应用到EC2实例

## 使用最新版本的helper scripts

## 使用Template前先验证

使用Template创建stack之前，请先通过CloudFormation验证下Template是否有错误，包括语法错误，依赖关联等

CloudFormation 首先先验证Template是否是合格的json格式，如果不是则验证是否是合格的yaml格式，若都不是，则会返回验证错误

## 验证Template是否符合公司的安全要求

## 通过CloudFormation来管理所有stack资源

不要通过CloudFormation之外更新调整resource，这样会使用=resource和Template的描述不一致，从而导致通过Template更新resource时会报错

## 在更新stack前创建change set

更新stack之前创建change set，即可通过检查change set确定本次更新是否符合预期及评估影响

## 使用stack policy

使用stack policy可以防止一些重要的resource不被更新，因为有些重要的resource更新时会影响业务

比如数据库实例，更新是会停止运行

stack policy就是一个json文档，用于描述在哪些resource上会执行哪些操作

在stack更新过程中，必须显式的指定哪些被保护的resource是要被更新的，否则这些被保护的resource是不会被更新的

## 通过代码review及版本控制管理Template

像管理代码源文件一样，管理Template

review Template可确保质量，同时确保历史各个时期版本的完整，保证出问题时可以追根朔源

## 定期更新EC2 linux实例

对于所有EC2 linux实例以及通过CloudFormation创建的EC2 linux实例，要定期通过yum update命令更新，确保获得最新的版本
