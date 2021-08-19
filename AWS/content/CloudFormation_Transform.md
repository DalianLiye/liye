# 关于Transform

可选

Transform用于指定一个或多个宏，这些宏用于CloudFormation去处理template

可以在template中，声明一个或多个宏，这些宏按会按照指定的顺序来执行

CloudFormation中的Transform，就是由CloudFormation托管的宏

**例：**

当定义多个宏时，需要使用List格式，并且指定一个或多个宏

```yaml
// Start of processable content for MyMacro and AWS::Serverless
Transform:
  - MyMacro
  - 'AWS::Serverless'
Resources:
  WaitCondition:
    Type: 'AWS::CloudFormation::WaitCondition'
  MyBucket:
    Type: 'AWS::S3::Bucket'
    Properties: 
        BucketName: MyBucket 
        Tags: [{"key":"value"}] 
        CorsConfiguration:[] 
  MyEc2Instance:
    Type: 'AWS::EC2::Instance' 
    Properties:
        ImageID: "ami-123"
// End of processable content for MyMacro and AWS::Serverless
```

