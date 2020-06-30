##### String转byte编码格式

```java
//String转byte
String str = "测试";
byte[] strByte = str.getBytes("UTF-8"); //不指定编码时使用默认编码

//byte转String
byte[] strByte = {1,2,3};
String str = new String(strByte,"UTF-8");
```