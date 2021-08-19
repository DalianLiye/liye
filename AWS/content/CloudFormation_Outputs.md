# 关于Outputs

可选

**Outputs可以有以下作用：**

- 用于声明Output值，该Output值可以由其他stack导入(跨stack引用)
- 返回响应值
- 在console中显示返回响应值

Output不会对输出值做任何数据上处理，都是直接输出的，因此不要将敏感信息放到Outputs里来

Output值只有在stack操作完成之后才有效，当stack处于正在进行中的任何状态时，output值是无效的

不建议在服务运行时和stack output值之间创建依赖，因为output值不是所有时间都是有效的

可以将output跟condition进行关联使用，前提是需要在Conditions中定义condition

用于描述当查看resource属性时的返回值

比如，在Template中定义了output并创建完stack之后，可通过CLI执行查看stack命令，即可返回output定义好了的返回值

# 语法

一个Template可以创建最多200个outputs

```yaml
Outputs:
  Logical ID:
    Description: Information about the value
    Value: Value to return
    Export:
      Name: Value to export
```

# Output字段

- **Logical ID**

  必须，当前output的标识符，由数字或字母组成，Template内必须唯一

- **Description**

  可选，用于描述output，长度为0-1024字节，不可以通过参数或函数来设定Description，长度最多为4K

- **Value**

  必须，aws cloudformation describe-stacks命令返回的值

  output值可以包括文本，参数引用，伪参数，映射值，内置函数

- **Export** 

  用于跨stack引用的导出值

  **例：**

  通过内置函数自定义Export的Name值

  ```yaml
  Export:
    Name: !Join [ ":", [ !Ref "AWS::StackName", AccountVPC ] ]
  ```

  **当出现跨stack引用时，会有以下限制：**

  - 对于每个账号，Export的name值必须是唯一的
  - 跨stack引用只能在同一个region下，可以使用内置函数ImportValue导入同一个region下的导出值
  - 一个output的Export的name值不可以使用基于resource的Ref或GetAtt函数，同理，ImportValue函数不可以使用基于resource的Ref或GetAtt函数
  - 如果一个A stack引用了另一个B stack的output，则B stack是不能被删除的
  - 如果一个output值被其他stack引用，则该output值不可以被更改或删除

# 实例

**例1**

定义多个output，BackupLoadBalancerDNSName output用于当condition返回true时，返回BackupLoadBalancer的DNSName属性

```yaml
Outputs:
  BackupLoadBalancerDNSName:
    Description: The DNSName of the backup load balancer
    Value: !GetAtt BackupLoadBalancer.DNSName
    Condition: CreateProdResources
  InstanceID:
    Description: The Instance ID
    Value: !Ref EC2Instance
```

**例2**

output用于跨stack引用

```yaml
Outputs:
  StackVPC:
    Description: The ID of the VPC
    Value: !Ref MyVPC
    Export:
      Name: !Sub "${AWS::StackName}-VPCID"
```



