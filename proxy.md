* 非代理模式：
```java
class Jay {
    public void sing() {
        System.out.println("七里香");
    }
}
public class Main {
    public static void main(String[] args) {
        // 首先，杰伦要存在
        Jay jay = new Jay();
        // 请杰伦唱歌
        jay.sing();
    }
}
```
<br/>
<br/>
* 静态代理

```java
// 明星接口
interface Celebrity {
    public void sing();
}
// 杰伦类
class Jay implements Celebrity{
    @Override
    public void sing() {
        System.out.println("sing..七里香..");
    }
}
// 杰伦代理类
class jayProxy implements Celebrity{
    // 代理类会有一个接口对象作为成员属性
    Celebrity celebrity;
    // 构造器，为成员属性赋值
    public jayProxy(Celebrity celebrity) {
        this.celebrity = celebrity;
    }
    // 代理类
    @Override
    public void sing() {
        // 代理类方法调用真实类的方法，并在调用前后完成一些辅助工作
        System.out.println("谈价钱");
        celebrity.sing();
        System.out.println("结束合作");
    }
}
public class Main {
    public static void main(String[] args) {
        // 首先，杰伦要存在
        Jay jay = new Jay();
        // 直接找杰伦的经纪人
        Celebrity proxy = new jayProxy(jay);
        // 经济人和杰伦商讨并请他唱歌
        proxy.sing();
    }
}
```
<br/>
<br/>
* 动态代理：

```java
interface Celebrity {
    public void sing();
}

class Jay implements Celebrity{
    @Override
    public void sing() {
        System.out.println("sing..七里香..");
    }
}

class ProxyFactory {
    /**
     * 获取被代理类对象。这个对象一定是动态获取的，因为它根据被代理类和其实现的接口通过反射来获取。
     * @param obj 这个obj就是被代理类的对象，他是创建代理类对象的依据
     * @return 返回代理类对象
     */
    public static Object getInstance(Object obj) {
        // 调用Proxy类的newProxyInstance方法通过反射机制来创建代理类对象
        return Proxy.newProxyInstance(obj.getClass().getClassLoader(), obj.getClass().getInterfaces(),
                new InvocationHandler() {
                    /**
                     * 匿名内部类，返回一个实现了InvocationHandler类的对象
                     * @param proxy 就是代理类的对象（不是被代理类，根据上下文被代理类是obj）
                     * @param method 就是被代理类中的需要被代理的方法（不止一个）
                     * @param args  被代理类中需要被代理的方法的参数（所有参数）
                     * @return  返回被代理类中需要被代理的方法的返回值
                     * @throws Throwable
                     */
                    @Override
                    public Object invoke(Object proxy, Method method, Object[] args) throws Throwable {
                        // 直接通过反射机制，方法点invoke就可以调用这个方法
                        // 两个参数分别表示调用方法的对象和方法参数
                        return method.invoke(obj, args);  // 仍然返回原被代理类同名方法的返回值
                    }
                });
    }
}
public class Main {
    public static void main(String[] args) {
        // 请杰伦唱歌
        Jay jay = new Jay();
        // 找杰伦的经纪人
        Celebrity proxy = (Celebrity) ProxyFactory.getInstance(jay);
        // 经纪人处理唱歌相关事宜
        proxy.sing();
    }
}
```
