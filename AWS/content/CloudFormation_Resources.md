# 关于Reousrces

必须

用于指定stack的resource及其属性，比如EC2，S3等

# 语法

```yaml
Resources:
  Logical ID:
    Type: Resource type
    Properties:
      Set of properties
```



# **Logical ID**

有字母或数字组成，Template内必须唯一

可以在Template的其他地方，通过Logical id来引用对应的resource

某些特定的resource，它们还有一个physical ID的属性，该属性是给该resource的真实名称，比如EC2的实例ID

当resource创建之后，可以在CloudFormation Template之外通过physical ID来识别该resource

可以在resource里关联使用condition

# **type**

用于定义resource的类型

# **properties**

用于为resource指定额外的选项

**例1**

当创建一个EC2 resource时，需要额外的为其指定AMI属性

```yaml
Resources:
  MyEC2Instance:
    Type: "AWS::EC2::Instance"
    Properties:
      ImageId: "ami-0ff8a91507f77f867"
```

**例2**

Property的值可以是文本，string list，布尔，参数引用，伪引用，函数返回值

```yaml
Properties:
  String: OneStringValue
  String: A longer string value 
  Number: 123
  LiteralList:
    - "[first]-string-value with a special characters"
    - "[second]-string-value with a special characters"
  Boolean: true
  ReferenceForOneValue:
    Ref: MyLogicalResourceName
  ReferenceForOneValueShortCut: !Ref MyLogicalResourceName
  FunctionResultWithFunctionParams: !Sub |
    Key=%${MyParameter}

```



