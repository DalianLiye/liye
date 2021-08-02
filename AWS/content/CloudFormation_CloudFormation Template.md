# **关于CloudFormation Template**

Template就是一个text文本文件，文件内容的格式是json或yaml

Template里声明了要在Amazone创建或配置的resource，这些resource相互关联，组成了stack

------

# Template结构

**Template包含了以下几个部分**

创建Template时，建议按照以下排列的顺序进行排列，因为某一部分中的值可能会参照前一个部分的值

- **Format Version (可选)**

  Template符合的 CloudFormation template版本，Template版本跟API或WSDL版本是不相同的，它跟API或WSDL版本是独立的

- **Description (可选)**

  用于描述Template的，必须紧接着Format Version这部分

- **Metadata (可选)**

  用于提供Template额外的信息

- **Parameters (可选)**

  在运行时（创建或更新stack时）传递给Template的参数值

- **Rules (可选)**

  用于验证在stack创建或更新期间传递给Template的参数或参数组合

- **Mappings (可选)**

  一个键值对映射，用于根据参数返回不同的值，类似于java的switch语句

- **Conditions (可选)**

  控制是否创建某些resource或是否在stack创建或更新期间为某些resource属性赋值的条件

  例如，可以有条件地创建一个resource，该resource取决于stack是用于prd环境还是用于tst环境

- **Transform (可选)**

- **Resources (必须)**

  用于指定stack的resource及其属性

- **Outputs (可选)**

  用于描述当查看resource属性时的返回值

  比如，在Template中定义了output并创建完stack之后，可通过CLI执行查看stack命令，即可返回output定义好了的返回值

# 更新Template

Template中的resource是可以更新的

基本操作就是在本地将Template更新好后，通过console上传，CloudFormation会查看并更新stack

CloudFormation只会对更新部分做更新，未更新部分仍会保持原样

------

**当更新Template时，需要考虑以下几点：**

- 当需要更新resource时，是否会对resource本身后者其他关联的resource有影响，比如更新instance type时，需要先停止instance然后再启动，这样可能就会对业务有影响
- 确定更新是可变还是不可变的，一些resource的property的更新是不被底层的服务支持的；如果property支持更新，则底层服务使用modify或update类型的api；如果property不支持更新，则会用更新的属性创建新的resource，并链接到stack中，最后删除就的resource；在stack重新配置期间，应用将无法完全运行，比如请求和后台访问等
