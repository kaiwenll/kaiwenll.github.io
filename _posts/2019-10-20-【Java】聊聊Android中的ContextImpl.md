# 聊聊Android中的ContextImpl

  字数 1407 

说起这个ContextImpl.可能有些同学不太熟悉，但说起Context，我想都认识它吧，上下文，也可以说是代表一种所在的场景，由于Context只是一个抽象类，而抽象类必定是有一个具体的实现类的，另外还有ContextThemeWrapper和ContextWrapper,不过这些都是Context的子类而已，他们是以装饰模式而存在的一种关系，简单说下装饰模式，装饰模式是常用的设计模式之一，一般情况下如果需要动态地给一个对象添加一些额外的职责但又不想增加子类，那么就可以用到装饰模式了，如果单纯增加功能来说，Decorator模式相比生成子类更为灵活,该模式以对客 户端透明的方式扩展对象的功能,下面举一个简单的例子说明一下，首先一个人，有吃饭的功能接口

代码如下:

Component

```csharp
 public interface Person {

    void eat();
}
```

ConcreteComponent

```java
public class Man implements Person {

    public void eat() {
        System.out.println("男人在吃饭");
    }
}
```

Decorator(这个类是关键，实现了接口并且有真实对象的引用)

```java
public abstract class Decorator implements Person {

    protected Person person;

    public Decorator(Person person){
        this.person=person;
    }

    public void eat() {
        person.eat();
    }
}
```

下面的就是具体的添加额外功能的了具体子类，比如有些人吃饭前先洗手或者吃完饭后洗碗之类，当然具体什么事情根据业务决定

```java
public class ManDecoratorA extends Decorator {

    public ManDecoratorA(Person person) {
        super(person);
    }
    public void eat() {
        wash();
        super.eat();
    }

    private void wash() {
        System.out.println("饭前先洗洗手");
    }
}
public class ManDecoratorB extends Decorator {

    public ManDecoratorB(Person person) {
        super(person);
    }
    public void eat() {
        super.eat();
        washDishes();
    }

    private void washDishes(){
          System.out.println("吃完饭后洗碗");
    }
}
```

测试的结果为:

```csharp
 public static void main(String[] args) {
        Person person=new Man();
        ManDecoratorA md1=new ManDecoratorA(person);
        ManDecoratorB md2=new ManDecoratorB(person);
        md1.eat();
        System.out.println("===============");
        md2.eat();
    }
```

运行结果为:



![img](https://upload-images.jianshu.io/upload_images/1779326-89fd201625baf992.png?imageMogr2/auto-orient/strip|imageView2/2/w/588/format/webp)

QQ图片20171208164154.png

可以看到在吃饭前和吃饭后做了一些事情，这就达到了不增加子类而又可以添加一些额外功能的作用

回到ContextImpl和Context，其实也是一样的，这个ContextImpl相当于代码中的Man,而Context相当于Person,只是一个接口，一个抽象类，但本质都是一样，而Decorator相当于ContextWrapper，那么具体的抽象实现类为什么呢，在Android中Activity和Service就是类似代码中的ManDecoratorA和ManDecoratorB了，只不过Activity有界面，自然就有Theme主题，因此对应的是ContextThemeWrapper，现在已经对装饰模式理解的更深了吧。

现在回到ContextImpl本身来，ContextImpl作为Context的抽象类，实现了所有的方法，我们常见的getResources(),getAssets(),getApplication()等等的具体实现都是在ContextImpl的，下面是具体的一些代码

```java
 @Override
    public ContentResolver getContentResolver() {
        return mContentResolver;
    }

    @Override
    public Looper getMainLooper() {
        return mMainThread.getLooper();
    }

    @Override
    public Context getApplicationContext() {
        return (mPackageInfo != null) ?
                mPackageInfo.getApplication() : mMainThread.getApplication();
    }
```

可以看到我们平常开发所调用的方法的实现都是在这里完成，作为一名android开发者，我们应该多去研究一些源码之类的，这样在出问题的时候可以根据源码找出问题的具体所在，ContextImpl在主线程ActivityThread通过传入主线程对象创建了一个系统的ContextImpl,下面是代码:

```java
 public ContextImpl getSystemContext() {
        synchronized (this) {
            if (mSystemContext == null) {
                mSystemContext = ContextImpl.createSystemContext(this);
            }
            return mSystemContext;
        }
    }
```

这个是在ActivityThread里面完成的，ActivityThread是Android应用程序的主线程环境，关于对ActivityThread的分析，可以看我的另一篇文章 [关于Android主线程(ActivityThread)源代码分析以及一些特殊问题的非常规方法](https://link.jianshu.com/?t=https://link.juejin.im?target=http%3A%2F%2Fblog.csdn.net%2Fshifuhetudi%2Farticle%2Fdetails%2F52089562)
大家有空可以去看看,实际上Activity和Service中的Context也是通过ContextImpl来的,大家有时间可以去看看主线程的源码，好了，我们知道了ContextImpl里面的方法了，下面说一个具体的不太常见的需求.曾经有个需求，要求用户在卸载之后再重新安装进入的时候能够读取一些配置信息，当初服务端要求这个客户端自己去实现，有同学说了这个本身不难，很简单啊，用SharePreference就可以搞定，恩，是很简单，可是需求说了再卸载之后重新进入的时候需要读取出来原来的配置，卸载之后整个应用程序的目录都不见了，所有数据都消失了，配置文件哪里来呢？,我们知道，SharePreference是保存在一个叫做shared_prefs目录下面的，这个目录随着程序卸载也会被删掉，也就是说卸载之后，保存在原来默认的存储就会全部消失，那应该怎么办呢，其实只需要修改原来的路径改为自定义的路径就好，比如放在外部SD卡或者其他地方，这样程序卸载的时候就不会删除这些自定义的目录了，从而可以在安装再次进入的时候读取出来，看起来这个方法可以的

ContextImpl里面有一个字段mPreferencesDir，这个文件目录就是保存了SharePreference路径的，我们只需要修改这个为我们自定义的路径就好了，由于ContextImpl是一个隐藏类，我们需要使用反射去实现，随我走一波吧，下面是具体的代码:

```csharp
try {
            Class<?> clazz=Class.forName("android.app.ContextImpl");
            Method method=clazz.getDeclaredMethod("getImpl", Context.class);
            method.setAccessible(true);
            Object mContextImpl=method.invoke(null,this);
            //获取ContextImpl的实例
            Log.d("[app]","mContextImpl="+mContextImpl);
            Field mPreferencesDir=clazz.getDeclaredField("mPreferencesDir");
            mPreferencesDir.setAccessible(true);
            //我们自定义的目录假设在SD卡, 其他目录也是一样的
            if (Environment.getExternalStorageState().equals(Environment.MEDIA_MOUNTED)){
                File file=new File(Environment.getExternalStorageDirectory(),"new_shared_pres");
                if (!file.exists()){
                    file.mkdirs();
                }
                mPreferencesDir.set(mContextImpl,file);
                Log.d("[app]","修改sp路径成功");
            }
        } catch (Exception e) {
            e.printStackTrace();
        }

    下面是具体的执行结果:
    12-08 17:28:42.811 12404-12404/com.example.hotfixdemo D/[app]: mContextImpl=android.app.ContextImpl@db6fc37
12-08 17:28:42.818 12404-12404/com.example.hotfixdemo D/[app]: 修改sp路径成功
```

OK,已经成功修改为自定义的路径了，这样就达到目的了。

实际上，除了SP的路径，ContextImpl里面还有很多类似这样的路径，一样是可以通过类似的手段修改的，达到一些特定的目的，比如360的插件框架也是Hook了ContextImpl类的数据库路径，达到加载的目的，大家有空可以去看看，Java层的Hook基本以反射和动态代理为主，这两方面的内容，有时间再写，今天就写到这里，感谢大家阅读。

转载地址：https://www.jianshu.com/p/b36b70521b79
