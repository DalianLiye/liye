# Template对象

Template声明顶层对象包括以下：

- ** AWSTemplateFormatVersion  (可选) **
- **Description (可选) **
- **Parameters  (可选) ** 
- **Mappings  (可选) ** 
- **Resources  (可选)  **
- **Outputs  (可选) ** 

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

