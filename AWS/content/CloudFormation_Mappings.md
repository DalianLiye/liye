# 关于Mappings

可选

一个键值对映射，通过一个key值来匹配与之相应的其他值，类似于java的switch语句

通过FindInMap函数获取映射中的值

Mappings里不可以包含参数，伪参数，以及内置函数

# 语法

Mappings中key必须是文本字符串，value可以是string或者List类型

```yaml
Mappings: 
  Mapping01: 
    Key01: 
      Name: Value01
    Key02: 
      Name: Value02
    Key03: 
      Name: Value03
```

# 例1

```yaml
Mappings: 
  RegionMap: 
    us-east-1: 
      "HVM64": "ami-0ff8a91507f77f867"
    us-west-1: 
      "HVM64": "ami-0bdb828fd58c52235"
    eu-west-1: 
      "HVM64": "ami-047bb4163c506cd98"
    ap-southeast-1: 
      "HVM64": "ami-08569b978cc4dfa10"
    ap-northeast-1: 
      "HVM64": "ami-06cd52961ce9f0d85"
```



例

从映射中返回值





例

通过FindInMap函数和参数，获取映射中的指定值

```yaml
  Parameters: 
    EnvironmentType: 
      Description: The environment type
      Type: String
      Default: test
      AllowedValues: 
        - prod
        - test
      ConstraintDescription: must be a prod or test
  Mappings: 
    RegionAndInstanceTypeToAMIID: 
      us-east-1: 
        test: "ami-8ff710e2"
        prod: "ami-f5f41398"
      us-west-2: 
        test: "ami-eff1028f"
        prod: "ami-d0f506b0"
        
      ...other regions and AMI IDs...
  
  Resources:
  
   ...other resources...

  Outputs: 
    TestOutput: 
      Description: Return the name of the AMI ID that matches the region and environment type keys
      Value: !FindInMap [RegionAndInstanceTypeToAMIID, !Ref "AWS::Region", !Ref EnvironmentType]

```





