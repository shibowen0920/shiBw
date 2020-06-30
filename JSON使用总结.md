##### JSON使用总结

+ JSON字符串（Map转JSONString，JSONObject转JSONString）	

  - ```java
    String str = JSON.toJSONString(returnMap);
    ```

+ JSONString转Map，转实体类

  - ```java
    Map<String,Object> result = JSONObject.parseObject(resultStr,Map.class);
    ```

    

