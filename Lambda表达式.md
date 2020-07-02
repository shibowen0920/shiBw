##### Lambda表达式

- 理解functional interface是学习Java8 lambda表达式的关键所在

- 函数式接口定义：

  - 任何接口，如果只包含唯一一个抽象方法，那么他就是一个函数式接口。

  ~~~java
  public interface Runncable{
  	public abstract void run();
  }
  ~~~

  - 对于函数式接口，我们可以通过lambda表达式来创建该接口的对象。

***

- **为什么要用lambda表达式**
  - 避免内部类定义过多
  - 代码更简洁
  - 去掉了很多没有意义的代码，只留下核心逻辑

```java
public class LamdaDemo {

    //静态内部实现类
    static class Like2 implements ILike {

        @Override
        public void test() {
            System.out.println("i like 2");
        }
    }

    public static void main(String[] args) {
        //第一步
        ILike iLike = new Like();
        iLike.test();

        //第二步
        iLike = new Like2();
        iLike.test();

        //第三步 内部类
        class Like3 implements ILike {

            @Override
            public void test() {
                System.out.println("i like 3");
            }
        }

        iLike = new Like3();
        iLike.test();

        //第四步 匿名内部类
        iLike = new ILike() {
            @Override
            public void test() {
                System.out.println("i like 4");
            }
        };

        iLike.test();

        //第五 匿名内部类
        iLike = () -> {
            System.out.println("i like 5");
        };

        iLike.test();

    }
}

interface ILike {
    void test();
}

//实现类
class Like implements ILike {

    @Override
    public void test() {
        System.out.println("i like 1 ");
    }
}
```

