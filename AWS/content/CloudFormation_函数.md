# **!Ref函数**

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

# **!GetAZs函数**

用于获取一个region下所有可用的zones列表

# **!GetAtt函数**

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

# **!FindInMap函数**

用于匹配mapping的值

# **!Join函数**

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

# !ImportValue函数

引用其他stack导出的resource

