##### String字符串计算每个字符出现的次数

- 找出字符串中出现次数最多的字符（删除法）

  ~~~~java
  public class StringDemo2 {
      public static void main(String[] args) {
          deleteMethodToAchieve("12333333322233333333333");
      }
      /**
       * 用删除法实现 （挺巧妙的）
       * 解题思路：每次取出字符串的第一个字符，
       * 将字符串中与第一个字符相同的字符全部删掉，
       * 然后通过计算删除前后字符串的长度来确定该字符在字符串出现的次数，
       * 最终比较出出现次数最多的字符
       */
      public static void deleteMethodToAchieve(String str) {
          int maxLength = 0;
          String maxStr = "";
          while (str.length()>0){
              String firstChar = str.substring(0,1);
              int length = str.length();
              str = str.replaceAll(firstChar,"");
              if(length - str.length() > maxLength){
                  maxLength = length - str.length();
                  maxStr = firstChar;
              }
          }
          System.out.println("出现次数最多的字符是：" + maxStr + "，出现的次数：" + maxLength);
  
      }
  }
  ~~~~

- 三种查出次数最多字符的方法

  ~~~java
  public class StringDemo3 {
  
      public static void main(String[] args) {
          hashMapMethodToAchieve("zheshiyigezifuchuandeceshi");
      }
  
      /**
       * 用字符数组查找法实现
       * 解题思路：先将字符串拆分成字符数组，然后转存到 HashMap 集合中，
       * 该集合的key为字符串中出现的字符，value为对应字符串出现的次数。
       * 最后只需要在HashMap集合中找到Value值最大的key即可。
       */
      public static void hashMapMethodToAchieve(String string) {
          // 将字符串转换成字符数组
          char[] arr = string.toCharArray();
          Map<Character, Integer> map = new HashMap<>();
          // key为出现的字符，value 为该字符出现的次数，将字符数组转存在 HashMap 中
          if (arr.length > 0 && arr != null) {
              for (Character ch : arr) {
                  if (map.containsKey(ch)) {
                      map.put(ch, map.get(ch) + 1);
                  } else {
                      map.put(ch, 1);
                  }
              }
          }
  
          System.out.println(map.toString());
          // 查找出出现次数最多的字符以及出现的次数也有多种写法
  //        FindTheMostCharByMap(map); // 查找写法一：用 Iterator 遍历 Map 来查找
  
  //         FindTheMostCharByMapEntry(map); // 查找写法二：用 Map.Entry 提高效率
  
           FindTheMostCharByForLoop(map, arr); // 查找写法三：直接用 for 循环来遍历查找
      }
      // 查找写法一：用 Iterator 遍历 Map 来查找
      public static void FindTheMostCharByMap(Map<Character,Integer> map){
  
          Set<Character> keys = map.keySet();
          Iterator iterator = keys.iterator();
  
          Character maxKey = (Character) iterator.next();
          int maxValue = map.get(maxKey);
          while (iterator.hasNext()){
              Character temp = (Character)iterator.next();
              if(maxValue < map.get(temp)){
                  maxKey = temp;
                  maxValue = map.get(temp);
              }
          }
          System.out.println("出现次数最多的字符是：" + maxKey + ", 出现的次数："+maxValue);
      }
      // 查找写法二：用 Map.Entry 提高效率
      public static void FindTheMostCharByMapEntry(Map<Character,Integer> map){
          Iterator iterator = map.entrySet().iterator();
          Map.Entry entry = (Map.Entry)iterator.next();
          char maxKey = (char)entry.getKey();
          int maxValue = (int)entry.getValue();
          while (iterator.hasNext()){
              entry = (Map.Entry)iterator.next();
              char tempKey = (char)entry.getKey();
              int tempValue = (int)entry.getValue();
              if(maxValue < tempValue){
                  maxKey = tempKey;
                  maxValue = tempValue;
              }
          }
          System.out.println("出现次数最多的字符是：" + maxKey + ", 出现的次数：" + maxValue);
      }
      // 查找写法三：直接用 for 循环来遍历查找
      public static void FindTheMostCharByForLoop(Map<Character, Integer> map, char[] arr) {
          int maxValue = map.get(arr[0]);
          char maxKey = ' ';
          for(char ch:arr){
              if(maxValue < map.get(ch)){
                  maxValue = map.get(ch);
                  maxKey = ch;
              }
          }
          System.out.println("出现次数最多的字符是：" + maxKey + ", 出现的次数：" + maxValue);
      }
  ~~~

  