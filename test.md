# <font size=6>■ 关于Button</font>
一个Button类（javafx.scene.control.Button）对象，代表了一个Button控件
当用户点击Button时，会执行相应的操作
# <font size=6>■ 创建Button</font>
```java
Button button = new Button("My Button");
```
# <font size=6>■ 追加Button到Scene Graph</font>
想要Button可见， 必须将其加入到Scene，或将其加入到一个layout再将layout加入到Scene
```java
Button button = new Button("My Button");
Scene scene = new Scene(button, 200, 100);
primaryStage.setScene(scene);
primaryStage.show();
```
**注：**
当Button被直接加入到Scene时，Button的大小会跟Scene一样，因此无法看到Button的边界
将Button加入到layout，再将layout加入到Scene，这样就会看到button的边界
# <font size=6>■ 文本</font>
有以下两种方式，可以改变按钮的文本：
- 创建Button实例时，通过构造函数设置Button文本
- 创建Button实例后，通过setText()方法设置Button的文本
```java
Button button1 = new Button("My Button1");
Button button2 = new Button();
button2.setText("My Button2");
```
# <font size=6>■ 文本大小</font>
可以通过CSS设置Button的Text Size，比如-fx-text-size
# <font size=6>■ 文本跨行</font>
当Button的Text过长，可以让其跨行显示
通过 **setWrapText()** 方法设置按钮文本的跨行显示
```java
button.setWrapText(true);  //当按钮文本过长，可跨行显示
button.setWrapText(false);  //当按钮文本过长，不可跨行显示，过长的部分显示为省略号
```
# <font size=6>■ 文本字体样式</font>
可以为按钮的文本设置字体样式
通过 **setFont()** 方法为按钮的文本设置字体样式
```java
Button button = new Button("Click me!");
Font font = Font.font("Courier New", FontWeight.BOLD, 36);
button.setFont(font);
```
# <font size=6>■ 默认模式</font>
可以为按钮设置默认模式
默认模式，就是界面初始化时，光标默认落在了指定按钮上，这样用户就不必手动移动鼠标去点这个按钮，直接敲击回车即可
通过 **setDefaultButton()** 方法为按钮设置默认模式
```java
button.setDefaultButton(true);
```
# <font size=6>■ 取消模式</font>
可以为按钮设置取消模式
当一个按钮处于取消模式时，点击Esc按钮后(同一scene内，Esc按钮没有被设置为其他按钮的快捷键)，该按钮被激活
通过setCancelButton()方法为按钮设置取消模式
```java
buttonDefault.setCancelButton(true);
```
# <font size=6>■ 图片</font>
按钮里既可以显示文本，又可以显示图片
```java
primaryStage.setTitle("HBox Experiment 1");
FileInputStream input = new FileInputStream("resources/images/iconmonstr-home-6-48.png");
Image image = new Image(input);
ImageView imageView = new ImageView(image);
Button button = new Button("Home", imageView);
Scene scene = new Scene(button, 200, 100);
primaryStage.setScene(scene);
primaryStage.show();
```
# <font size=6>■ 大小</font>
可以为按钮设置大小
有以下方法：
- setMinWidth() //最小宽度
- setMaxWidth()  //最大宽度
- setPrefWidth()  //期待宽度，如果有空余的空间，则按钮显示期待的宽度； 否则，会缩小直到最小宽度

- setMinHeight()  //最小高度
- setMaxHeight()  //最大高度
- setPrefHeight()  //期待宽度，如果有空余的空间，则按钮显示期待的高度； 否则，会缩小直到最小高度

- setMinSize()  //同时设定最小宽度，高度
- setMaxSize()  //同时设定最大宽度，高度
- setPrefSize()  //同时设定期待宽度，高度

```java
button.setMaxSize(100, 200);
//相当于以下代码
button.setMaxWidth(100);
button.setMaxHeight(200);
```
# <font size=6>■ 事件</font>
可以为按钮添加事件
当按下按钮时，可以做相应的处理
```java
button.setOnAction(new EventHandler() {
    @Override
    public void handle(ActionEvent actionEvent) {
        //... do something in here.
    }
});
```
**lamada表达式**
```java
button.setOnAction(actionEvent ->  {
    //... do something in here.    
});
```
```java
primaryStage.setTitle("HBox Experiment 1");

Label label = new Label("Not clicked");
Button button = new Button("Click");

button.setOnAction(value ->  {
   label.setText("Clicked!");
});

HBox hbox = new HBox(button, label);
Scene scene = new Scene(hbox, 200, 100);
primaryStage.setScene(scene);
primaryStage.show();
```
# <font size=6>■ 助记符</font>
可以为一个按钮设置助记符
一个助记符就是一个按键，当按下**alt + 按键**，会触发某一个指定的按钮，简单讲就是为一个按钮设置快捷键
助记符是大小写敏感的
```java
button.setMnemonicParsing(true);
button.setText("_Click");  //表示button的助记符是C，同时按下alt + C, 就会触发该按钮

button.setMnemonicParsing(false);
button.setText("_Click"); //如果设定为false，则下划线_会被当作普通字符
```
# <font size=6>■ CSS样式</font>
可以为按钮设置CSS样式
按钮控件支持以下CSS样式：
- -fx-border-width
- -fx-border-color
- -fx-background-color
- -fx-font-size
- -fx-text-fill
```java
Button button = new Button("My Button");
button.setStyle("-fx-background-color: #ff0000; ");  //既可以通过setStyle()方法设置CSS样式，也可以通过CSS文件设置
button1.setStyle("-fx-border-color: #ff0000; -fx-border-width: 5px;");
button2.setStyle("-fx-background-color: #00ff00");
button3.setStyle("-fx-font-size: 2em; ");
button4.setStyle("-fx-text-fill: #0000ff");
```
# <font size=6>■ 不可用</font>
通过 **setDisable()** 方法为按钮设置不可用状态
```java
button.setDisable(true);  //按钮设置为可用
button.setDisable(false);  //按钮设置为不可用
```
# <font size=6>■ FXML</font>
可以通过FXML文件对Button进行修饰
**button-example.fxml**
```xml
<?xml version="1.0" encoding="UTF-8"?>
    <?import javafx.scene.layout.VBox?>
    <?import javafx.scene.control.Button?>
    <VBox xmlns:fx="http://javafx.com/fxml" spacing="20">
    <children>
        <Button fx:id="button1" text="Click me!" onAction="#buttonClicked"/>
    </children>
</VBox>
```
**Controller.java**
```java
import javafx.event.Event;
import javafx.fxml.FXML;
import javafx.scene.control.Button;

public class ButtonFXMLController {

    public Button button1 = null;
    private int   button1ClickCount = 0;

    @FXML
    public void buttonClicked(Event e){
        this.button1ClickCount++;
        String text = "Button1 clicked " + this.button1ClickCount + " times";
        System.out.println(text);
        button1.setText(text);
    }
}
```
**Main.java**
```java
FXMLLoader loader = new FXMLLoader();

ButtonFXMLController controller = new ButtonFXMLController();
loader.setController(controller);

File fxmlFile = new File("assets/fxml/button-example.fxml");
URL fxmlUrl = fxmlFile.toURI().toURL();
loader.setLocation(fxmlUrl);
VBox vbox = loader.<VBox>load();
Scene scene = new Scene(vbox);
primaryStage.setScene(scene);
primaryStage.setTitle("Button FXML Example");
primaryStage.setWidth(300);
primaryStage.setHeight(300);
primaryStage.show();
```
# <font size=6>■ Transformations</font>
可以为Button追加一些处理，比如缩放，旋转，翻译等
```java
Scale scaleTransformation = new Scale();
scaleTransformation.setX(3.0);
scaleTransformation.setY(2.0);
scaleTransformation.setPivotX(0);
scaleTransformation.setPivotY(0);

Button button = new Button();
button.setText("Click me!");
button.setOnAction((event) -> {
	System.out.println("Button clicked!");
});

button.getTransforms().add(scaleTransformation);
```
