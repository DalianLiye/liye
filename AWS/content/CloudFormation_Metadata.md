# 关于Metadata

可选

用于提供Template额外的信息，即元数据信息，比如可以包含一些特定resource的实现细节

json和yaml格式

在stack更新期间，Metadata部分不可以自己更新，只能在追加，修改，和删除resource时更新Metadata

CloudFormation无法修改，转换或编辑包含在Metadata的任何信息，正因为如此，强烈建议不要使用Metadata存储敏感信息，如密钥或密码等

**例：**

```yaml
Metadata:
  Instances:
    Description: "Information about the instances"
  Databases: 
    Description: "Information about the databases"
```

## Metadata keys

有一些CloudFormation的特性会从Metadata中检索定义的配置信息

- **AWS::CloudFormation::Init** 

  为 cfn-init helper script指定configuration task，该script在在EC2实例上配置或安装应用时会用到

- **AWS::CloudFormation::Interface**

  用于指定在console中参数的分组及排序，默认情况下，console是通过它们的logical id来进行排序的

- **AWS::CloudFormation::Designer**

  用于指定resource在Designer里的坐标信息