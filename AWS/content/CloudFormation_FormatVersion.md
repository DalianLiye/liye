# 关于Format Version 

可选

该Template的对应CloudFormation template版本，Template版本跟API或WSDL版本是不相同的，它跟API或WSDL版本是独立的

Template不同的版本，其对应包含的功能是不一样的，当前最新且唯一版本版是2010-09-09

Template的版本是不可以通过参数传递进来的

如果没有指定版本，则默认使用最新的版本

**例：**

```yaml
AWSTemplateFormatVersion: "2010-09-09"
```

