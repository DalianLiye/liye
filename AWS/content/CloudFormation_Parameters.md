# 关于Parameters

可选

用于指定在运行时（创建或更新stack时）传递给Template的值

**例：**

```yaml
Parameters:
  InstanceTypeParameter:
    Type: String
    Default: t2.micro
    AllowedValues:
      - t2.micro
      - m1.small
      - m1.large
    Description: Enter t2.micro, m1.small, or m1.large. Default is t2.micro.
```

# **引用参数**

通过Ref函数引用参数

可以在Resources和Outputs部分引用参数

**例：**

```yaml
  Resources:
    Ec2Instance:
      Type: AWS::EC2::Instance
      Properties:
        InstanceType:
          Ref: InstanceTypeParameter  #引用参数InstanceTypeParameter
        ImageId: ami-0ff8a91507f77f867
```

# **参数基本要求**

**参数的基本要求如下：**

- 参数的最大个数是200
- 每一个参数必须要有一个logical name，可以包含数字和字母，logical name必须在整个template中唯一
- 每一个参数必须要指定一个CloudFormation支持的类型

- 当CloudFormation在创建stack时，必须要指定值，包括显式的传递值或者设置默认值
- 参数必须要在Template中被引用，可以在Resources和Outputs部分中引用参数

**例：**

```yaml
Parameters:
  ParameterLogicalID:
    Type: DataType
    ParameterProperty: value
```

# 参数属性

- **AllowedPattern**

  可选，参数值必须要符合的正则表达式模式

- **AllowedValues**

  可选，一组参数允许的值

- **ConstraintDescription**

  可选，当输入的参数值违反约束条件时，返回表达友好的返回信息

- **Default**

  可选，参数的默认值，如果有约束，则该默认值必须要符合约束条件

- **Description**

  可选，参数的描述信息，最多4000个字符

- **MaxLength**

  可选，参数长度的最大字符数

- **MaxValue**

  可选，参数数值的最大值

- **MinLength**

  可选，参数长度的最小字符数

- **MinValue**

  可选，参数数值的最小值

- **NoEcho**

  可选，用于指定参数值显示在console，CLI或者API返回值时，是否做Data Masking处理，即(****)

  当指定true，则做Data Masking处理，否则不做

  **NoEcho不会对以下attribute做Data Masking处理**

  - Metadata部分，CloudFormation不会对metadata里的任何信息做转换，修改或编辑处理

  - Outputs部分

  - Resouce里的metadata attribute

  **注：**

  1) 强烈建议不要直接在CloudFormation Template里嵌入敏感信息(密码,验证信息等)，可将敏感信息存储在Template之外(比如Amazon Systems Manager Parameter Store 或者Amazon Secrets Manager)，再将敏感信息以动态传参的方式传入Template

  2) 不要在参数的属性中包含NoEcho参数或者敏感信息，因为它们会作为resource主标识符的一部分，这样会暴漏信息

     当一个NoEcho参数被包含在了一个属性里，它的值会以明文的形式组成resource的主标识符，从而导致泄密

- **Type**

  必须，参数的数据类型

  - **string类型**

    文本字符串

  - **Number**

    integer或float，如果在Template的其他地方引用该参数(比如ref)，则会被当做string

  - **List<Number>**

    integer或float类型数字的List，如果在Template的其他地方引用该参数(比如ref)，则会被当做string类型的List

    例："80,20"，通过ref获得的结果为["80","20"]

  - **CommaDelimitedList**

    被逗号分隔的string类型List，每个元素都是前后去空格处理的

    例："test,dev,prod"，通过ref获得的结果为["test","dev","prod"]

  - **Amazon特定参数类型**

    例：Amazon EC2 key pair names 或 VPC IDs

  - **SSM参数类型**

    与Systems Manager Parameter Store中的现有参数相对应的参数

    为该类型参数指定Systems Manager参数key，CloudFormation会从Parameter Store获取最新的值用来创建stack

## Amazone特定参数类型

Amazone特定参数类型便于在创建或更新一个stack时，验证不合法的参数输入

如果参数是Amazone特定参数类型，则传入的参数值必须是当前账号下现有的Amazon值，CloudFormation会通过匹配输入值与现有值进行验证，比如指定的参数类型是AWS::EC2::VPC::Id，则输入的参数值必须是当前账号和region下现有的VPC Id

如果想要让临时用户输入其他账号下的Amazone特定参数值，则需将参数类型定义为string类型(或CommaDelimitedList)

### 支持的Amazone特定参数类型

https://docs.amazonaws.cn/en_us/AWSCloudFormation/latest/UserGuide/parameters-section-structure.html

# 分组和排序

当通过CloudFormation console来创建和更新stack时，console界面显示的参数列表是根据其logical name进行排序的

通过配置metadata key “AWS::CloudFormation::Interface” 可以在console显示参数列表时，对参数列表进行分组和重新指定排序key，这样会使参数展示界面更加友好

比如VPC相关的参数，可以分成一组，然后不同的组内各自进行排序

也可以对参数命名别名，这样界面可以展示参数的描述性信息，而非难以辨识的logical id

Metadata key里引用的参数都必须要在parameter里定义好的