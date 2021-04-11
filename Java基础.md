### Java语言基础

* JVM
  * ![image-20210324140440392](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210324140440392.png)
  * 我们需要格外注意的是 .class->机器码 这一步。在这一步 JVM 类加载器首先加载字节码文件，然后通过解释器逐行解释执行，这种方式的执行速度会相对比较慢。而且，有些方法和代码块是经常需要被调用的(也就是所谓的热点代码)，所以后面引进了 JIT 编译器，而 JIT 属于运行时编译。当 JIT 编译器完成第一次编译后，其会将字节码对应的机器码保存下来，下次可以直接使用。而我们知道，机器码的运行效率肯定是高于 Java 解释器的。这也解释了我们为什么经常会说 Java 是编译与解释共存的语言。
  * Java 虚拟机（JVM）是运行 Java 字节码的虚拟机。JVM 有针对不同系统的特定实现（Windows，Linux，macOS），目的是使用相同的字节码，它们都会给出相同的结果。字节码和不同系统的 JVM 实现是 Java 语言“一次编译，随处可以运行”的关键所在。

* JDK、JRE

  * JDK 是 Java Development Kit 缩写，它是功能齐全的 Java SDK。它拥有 JRE 所拥有的一切，还有编译器（javac）和工具（如 javadoc 和 jdb）。它能够创建和编译程序。

    JRE 是 Java 运行时环境。它是运行已编译 Java 程序所需的所有内容的集合，包括 Java 虚拟机（JVM），Java 类库，java 命令和其他的一些基础构件。但是，它不能用于创建新程序。

* 类加载机制

  * java编译器将 .java 文件编译成扩展名为 .class 的文件。.class 文件中保存着java转换后，虚拟机将要执行的指令。当需要某个类的时候，java虚拟机**会加载 .class 文件，并创建对应的class对象**，将class文件**加载到虚拟机的内存**，这个过程被称为类的加载。

  * **加载**
    类加载过程的一个阶段，**ClassLoader**通过一个**类的完全限定名**查找此类**字节码文件**，并利用字节码文件**创建一个class对象。**

    **验证**
    目的在于确保class文件的字节流中包含信息符合当前虚拟机要求，不会危害虚拟机自身的安全，主要包括四种验证：**文件格式的验证，元数据的验证，字节码验证，符号引用验证。**

    **准备**
    为**类变量（static修饰的字段变量）分配内存**并且设置该类变量的**初始值**，（如static int i = 5 这里只是将 i 赋值为0，在**初始化的阶段**再把 i 赋值为5)，这里不包含final修饰的static ，因为final在**编译的时候就已经分配**了。这里不会为实例变量分配初始化，**类变量会分配在方法区**中，实例变量会随着对象**分配到Java堆**中。

    **解析**
    这里主要的任务是**把常量池中的符号引用替换成直接引用**。验证，准备，解析三个过程被称为链接

    **初始化**
    这里是类记载的最后阶段，如果该类具有父类就进行对父类进行初始化，执行其**静态初始化器（静态代码块）和静态初始化成员变量**。（前面已经对static 初始化了默认值，这里我们对它进行赋值，成员变量也将被初始化）

    类记载器的任务是**根据类的全限定名来读取此类的二进制字节流到 JVM** 中，然后转换成一个与目标类对象的java.lang.Class 对象的实例，在java 虚拟机提供三种类加载器，**引导类加载器，扩展类加载器，系统类加载器。**

    - Class.forName()得到的class是已经初始化完成的。
    - Classloader.loaderClass得到的class是还没有链接（验证，准备，解析三个过程被称为链接）的。

* 双亲委派机制

  * 1）启动类加载器（**Bootstrap** Class-Loader），加载 `jre/lib` 包下面的 jar 文件，比如说常见的 rt.jar。

    2）扩展类加载器（**Extension** or Ext Class-Loader），加载 `jre/lib/ext` 包下面的 jar 文件。

    3）应用类加载器（**Application** or App Clas-Loader），根据**程序的类路径（classpath）**来加载 Java 类。

  * 当一个Hello.class这样的文件要被加载时。不考虑我们自定义类加载器，首先会在AppClassLoader中检查**是否加载过**，如果有那就无需再加载了。如果没有，那么会拿到父加载器，然后调用父加载器的**loadClass**方法。父类中同理也会先检查自己是否已经加载过，如果没有再往上。注意这个类似递归的过程，直到到达Bootstrap classLoader之前，都是在检查是否加载过，**并不会选择自己去加载**。直到BootstrapClassLoader，已经没有父加载器了，这时候开始考虑自己是否能加载了，如果自己无法加载，**会下沉到子加载器去加载**，一直到最底层，如果没有任何加载器能加载，就会抛出**ClassNotFoundException**。

  * 作用：**沙箱机制**，当一个类需要加载的时候，最先去尝试加载的就是BootstrapClassLoader（**防止污染源码**），所以其他类加载器并没有机会再去加载，从一定程度上**防止了危险代码的植入。**

* 关键字

  * final	
    * **final修饰的类不能被继承，final类中的所有成员方法都会被隐式的指定为final方法；**
    * **final修饰的方法不能被重写；**
    * **final修饰的变量是常量，如果是基本数据类型的变量，则其数值一旦在初始化之后便不能更改；如果是引用类型的变量，则在对其初始化之后便不能让其指向另一个对象。**
    * 使用final方法的原因有两个。第一个原因是把方法锁定，**以防任何继承类修改它的含义**；第二个原因是效率。在早期的Java实现版本中，会将final方法转为**内嵌调用**。但是如果方法过于庞大，可能看不到内嵌调用带来的任何性能提升（现在的Java版本已经不需要使用final方法进行这些优化了）。**类中所有的private方法都隐式地指定为final。**
  * static
    * static方法就是没有this的方法。在**static方法内部不能调用非静态方法**，反过来是可以的。而且可以在没有创建任何对象的前提下，仅仅通过类本身来调用static方法。这实际上正是static方法的主要用途，方便在没有创建对象的情况下来进行调用（方法/变量）。
    * **修饰成员变量和成员方法:** 被 static 修饰的成员属于类，不属于单个这个类的某个对象，被类中所有对象共享，可以并且建议通过类名调用。被static 声明的成员变量属于静态成员变量，静态变量 存放在 Java 内存区域的方法区。调用格式：`类名.静态变量名` `类名.静态方法名()`
    * **静态代码块:** 静态代码块定义在类中方法外, 静态代码块在非静态代码块之前执行(静态代码块—>非静态代码块—>构造方法)。 该类不管创建多少对象，**静态代码块只执行一次.**
    * **静态内部类（static修饰类的话只能修饰内部类）：** 静态内部类与非静态内部类之间存在一个最大的区别: 非静态内部类在编译完成之后会隐含地保存着一个引用，该引用是指向创建它的外围类，但是静态内部类却没有。没有这个引用就意味着：1. 它的创建是不需要依赖外围类的创建。2. 它不能使用任何外围类的非static成员变量和方法。
    * **静态导包(用来导入类中的静态资源，1.5之后的新特性):** 格式为：`import static` 这两个关键字连用可以指定导入某个类中的指定静态资源，并且不需要使用类名调用类中静态成员，可以直接使用类中静态成员变量和成员方法。
  * this
    * this用于标识引用类的当前实例。
    * 此关键字是可选的，这意味着如果上面的示例在不使用此关键字的情况下表现相同。 但是，使用此关键字可能会使代码更易读或易懂。
  * super
    * super关键字用于从子类访问父类的变量和方法
    * 在构造器中使用 `super()` 调用父类中的其他构造方法时，该语句必须处于构造器的首行，否则编译器会报错。另外，this 调用本类中的其他构造方法时，也要放在首行。
    * this、super不能用在static方法中。
    * 被 static 修饰的成员属于类，不属于单个这个类的某个对象，被类中所有对象共享。而 this 代表对本类对象的引用，指向本类对象；而 super 代表对父类对象的引用，指向父类对象；所以， **this和super是属于对象范畴的东西，而静态方法是属于类范畴的东西**。

* 字符、字符串区别

  * 形式上: 字符常量是单引号引起的一个字符; 字符串常量是双引号引起的 0 个或若干个字符
  * 含义上: 字符常量相当于一个整型值( ASCII 值),可以参加表达式运算; 字符串常量代表一个地址值(该字符串在内存中存放位置)
  * 占内存大小 字符常量只占 2 个字节; 字符串常量占若干个字节 (**注意： char 在 Java 中占两个字节**),

* 序列化

  * 对于不想进行序列化的变量，使用 transient 关键字修饰。

    transient 关键字的作用是：阻止实例中那些用此关键字修饰的的变量序列化；当对象被反序列化时，被 transient 修饰的变量值不会被持久化和恢复。transient 只能修饰变量，不能修饰类和方法

* 关键字

  * ![image-20210407164008211](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210407164008211.png)
  * ![image-20210407164617242](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210407164617242.png)

* 包装类和基本类型区别

  * 包装类可以为null，基本类不行

  * 包装类默认值为null，基本类默认值为0

  * 包装类可以用于泛型，基本类不可以

  * 基本类更高效，**基本类型在栈中直接存储的具体数值，而包装类型则存储的是指向堆中对象的引用**。**两个包装类型在使用“==”进行判断的时候，判断的是其指向的地址是否相等**

  * 自动装箱是通过`Integer.valueOf()`完成的；自动拆箱是通过 `Integer.intValue()` 完成的

  * ##### 当需要进行自动装箱时，如果数字在 -128 至 127 之间时，会直接使用缓存中的对象，而不是重新创建一个对象

  

* 键盘输入

  * 方法 1：通过 Scanner

    ```
    Scanner input = new Scanner(System.in);
    String s  = input.nextLine();
    input.close();
    ```

    方法 2：通过 BufferedReader

    ```
    BufferedReader input = new BufferedReader(new InputStreamReader(System.in));
    String s = input.readLine();
    ```

* 基本数据类型

  * | 基本类型 | 位数 | 字节 | 默认值  |
    | -------- | ---- | ---- | ------- |
    | int      | 32   | 4    | 0       |
    | short    | 16   | 2    | 0       |
    | long     | 64   | 8    | 0L      |
    | byte     | 8    | 1    | 0       |
    | char     | 16   | 2    | 'u0000' |
    | float    | 32   | 4    | 0f      |
    | double   | 64   | 8    | 0d      |
    | boolean  | 1    |      | false   |

  * 自动装箱拆箱：

    * **装箱**：将基本类型用它们对应的引用类型包装起来；
    * **拆箱**：将包装类型转换为基本数据类型；

  * 包装类和常量池

    * **Java 基本类型的包装类的大部分都实现了常量池技术，即 Byte,Short,Integer,Long,Character,Boolean；**两种浮点数类型的包装类 Float,Double 并没有实现常量池技术。
    * **前面 4 种包装类默认创建了数值[-128，127] 的相应类型的缓存数据，Character 创建了数值在[0,127]范围的缓存数据，Boolean 直接返回 True Or False。如果超出对应范围仍然会去创建新的对象。** 
    * `Integer i1=40；`Java 在编译的时候会直接将代码封装成 Integer i1=Integer.valueOf(40);，从而使用常量池中的对象。
    * Integer i1=40；Java 在编译的时候会直接将代码封装成 Integer i1=Integer.valueOf(40);，从而使用常量池中的对象。

* 关键字

  * ![image-20210324143213380](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210324143213380.png)

* 泛型

  * Java 泛型（generics）是 JDK 5 中引入的一个新特性, 泛型提供了编译时类型安全检测机制，该机制允许程序员在编译时检测到非法的类型。泛型的本质是参数化类型，也就是说所操作的数据类型被指定为一个参数。

  * 常用通配符：？表示不确定的Java类型、T表示具体的Java类型、K V分别表示java键值的key value、E表示Element

  * **Java 的泛型是伪泛型，这是因为 Java 在编译期间，所有的泛型信息都会被擦掉，这也就是通常所说类型擦除** 。如：

    ```
    List<Integer> list = new ArrayList<>();
    list.add(12);
    //这里直接添加会报错，因为list类型是Integer
    list.add("a");
    Class<? extends List> clazz = list.getClass(); // 通过实例对象获取所属的泛型类
    Method add = clazz.getDeclaredMethod("add", Object.class); // 通过反射获取方法名对应的方法
    //但是通过反射添加，是可以的，因为反射是运行时执行的过程，表明java中泛型在编译期间会被擦除
    add.invoke(list, "kl");
    ```

* == 和 equals

  * **`==`** : 它的作用是判断两个对象的地址是不是相等。即判断两个对象是不是同一个对象。(**基本数据类型==比较的是值，引用数据类型==比较的是内存地址**)

    > 因为 Java 只有值传递，所以，对于 == 来说，不管是比较基本数据类型，还是引用数据类型的变量，其本质比较的都是值，只是引用类型变量存的值是对象的地址。

    **`equals()`** : 它的作用也是判断两个对象是否相等，它不能用于比较基本数据类型的变量。`equals()`方法存在于`Object`类中，而`Object`类是所有类的直接或间接父类。不被覆盖则比较对象地址，被覆盖则比较对象内容

  * euals两种使用情况
    * 情况 1：类没有覆盖 `equals()`方法。则通过`equals()`比较该类的两个对象时，等价于通过“==”比较这两个对象。使用的默认是 `Object`类`equals()`方法。
    * 情况 2：类覆盖了 `equals()`方法。一般，我们都覆盖 `equals()`方法来判断两个对象的内容相等；若它们的内容相等，则返回 true(即，认为这两个对象相等)。
  * `String` 中的 `equals` 方法是被重写覆盖过的，因为 `Object` 的 `equals` 方法是比较的对象的内存地址，而 `String` 的 `equals` 方法比较的是对象的值。
  * 当创建 `String` 类型的对象时，虚拟机 JVM 会在常量池中查找有没有已经存在的值和要创建的值相同的对象，如果有就把它赋给当前引用。如果没有就在常量池中重新创建一个 `String` 对象。

* hashCode和equals

  * `hashCode()` 的作用是获取哈希码，也称为散列码；它实际上是返回一个 int 整数。这个哈希码的作用是确定该对象在哈希表中的索引位置。
  * `hashCode()`定义在 JDK 的 `Object` 类中，这就意味着 Java 中的任何类都包含有 `hashCode()` 函数。
  * 另外需要注意的是： `Object` 的 hashcode 方法是本地方法，也就是用 c 语言或 c++ 实现的，该方法通常用来将对象的 内存地址 转换为整数之后返回。

* 以`HashSet` 如何检查重复”为例子来说明为什么要有 hashCode？

  当你把对象加入 `HashSet` 时，`HashSet` 会先计算对象的 hashcode 值来判断对象加入的位置，同时也会与其他已经加入的对象的 hashcode 值作比较，如果没有相符的 hashcode，`HashSet` 会假设对象没有重复出现。

  但是如果发现有相同 hashcode 值的对象，这时会调用 `equals()` 方法来检查 hashcode 相等的对象是否真的相同。

  如果两者相同，`HashSet` 就不会让其加入操作成功。如果不同的话，就会重新散列到其他位置。这样我们就大大减少了 equals 的次数，相应就大大提高了执行速度。`hashcode` 只是用来缩小 equals 的查找成本。

* **为什么重写 `equals` 时必须重写 `hashCode` 方法？**

  如果两个对象相等，则 hashcode 一定也是相同的。两个对象相等,  对两个对象分别调用 equals （判断对象地址）方法都返回 true。

  但是，两个对象有相同的 hashcode 值，它们也不一定是相等的 。**因此，equals 方法被覆盖过（判断两个对象的内容），则 `hashCode` 方法也必须被覆盖。**

  > `hashCode()`的默认行为是对堆上的对象产生独特值。如果没有重写 `hashCode()`，则该 class 的两个对象无论如何 hashcode值 都不会相等（即使这两个对象指向相同的数据）

* Java只有值传递？

  * **按值调用(call by value)表示方法接收的是调用者提供的值，而按引用调用（call by reference)表示方法接收的是调用者提供的变量地址。一个方法可以修改传递引用所对应的变量值，而不能修改传递值调用所对应的变量值。** 它用来描述各种程序设计语言（不只是 Java)中方法参数传递方式。

    **Java 程序设计语言总是采用按值调用。也就是说，方法得到的是所有参数值的一个拷贝，也就是说，方法不能修改传递给它的任何参数变量的内容。**

    - 一个方法不能修改一个基本数据类型的参数（即数值型或布尔型）。
    - 一个方法可以改变一个对象参数的状态。
    - 一个方法不能让对象参数引用一个新的对象。

* 重载：

  * 发生在同一个类中（或者父类和子类之间），方法名必须相同，参数类型不同、个数不同、顺序不同，方法返回值和**访问修饰符**可以不同。
  * Java允许重载任何方法，重载就是同一个类中**多个同名方法根据不同的传参来执行不同的逻辑处理。**

* 重写：

  * **重写发生在运行期**，是子类对父类的**允许访问的方法的实现过程进行重新编写。**

  * 返回值类型、方法名、参数列表必须相同，抛出的异常范围小于等于父类，访问修饰符范围大于等于父类。

  * 如果父类方法**访问修饰符**为 `private/final/static` 则子类就不能重写该方法，但是被 static 修饰的方法**能够被再次声明。**

  * **构造方法无法被重写**

  * 重写遵从：

    * “两同”即**方法名相同、形参列表**相同；
    * “两小”指的是子类方法返回值类型应**比父类方法返回值类型更小或相等**，子类方法声明抛出的异常类应比父类方法声明抛出的**异常类更小或相等；**
    * “一大”指的是子类方法的访问权限应比父类方法的**访问权限更大或相等。**

  * | 区别点     | 重载方法 | 重写方法                                                     |
    | ---------- | -------- | ------------------------------------------------------------ |
    | 发生范围   | 同一个类 | 子类                                                         |
    | 参数列表   | 必须修改 | 一定不能修改                                                 |
    | 返回类型   | 可修改   | 子类方法返回值类型应比父类方法返回值类型更小或相等           |
    | 异常       | 可修改   | 子类方法声明抛出的异常类应比父类方法声明抛出的异常类更小或相等； |
    | 访问修饰符 | 可修改   | 一定不能做更严格的限制（可以降低限制）                       |
    | 发生阶段   | 编译期   | 运行期                                                       |

* Object常用方法

  ```java
  //native方法，用于返回当前运行时对象的Class对象，使用了final关键字修饰，故不允许子类重写。
  public final native Class<?> getClass() 
  
  //native方法，用于返回对象的哈希码，主要使用在哈希表中，比如JDK中的HashMap。
  public native int hashCode() 
  
  //用于比较2个对象的内存地址是否相等，String类对该方法进行了重写用户比较字符串的值是否相等。
  public boolean equals(Object obj)
  
  //naitive方法，用于创建并返回当前对象的一份拷贝。一般情况下，对于任何对象 x，表达式 x.clone() != x 为true，x.clone().getClass() == x.getClass() 为true。Object本身没有实现Cloneable接口，所以不重写clone方法并且进行调用的话会发生CloneNotSupportedException异常。
  protected native Object clone() throws CloneNotSupportedException
  
  //返回类的名字@实例的哈希码的16进制的字符串。建议Object所有的子类都重写这个方法。
  public String toString()
  
  //native方法，并且不能重写。唤醒一个在此对象监视器上等待的线程(监视器相当于就是锁的概念)。如果有多个线程在等待只会任意唤醒一个。
  public final native void notify()
  
  //native方法，并且不能重写。跟notify一样，唯一的区别就是会唤醒在此对象监视器上等待的所有线程，而不是一个线程。
  public final native void notifyAll()
  
  //native方法，并且不能重写。暂停线程的执行。注意：sleep方法没有释放锁，而wait方法释放了锁 。timeout是等待时间。
  public final native void wait(long timeout) throws InterruptedException
  
  //多了nanos参数，这个参数表示额外时间（以毫微秒为单位，范围是 0-999999）。 所以超时的时间还需要加上nanos毫秒。
  public final void wait(long timeout, int nanos) throws InterruptedException
  
  //跟之前的2个wait方法一样，只不过该方法一直等待，没有超时时间这个概念
  public final void wait() throws InterruptedException
  
  //实例被垃圾回收器回收的时候触发的操作
  protected void finalize() throws Throwable { }
  ```

* 无参构造作用：
  * Java 程序在执行子类的构造方法之前，如果没有用 `super()`来调用父类特定的构造方法，则会调用父类中“没有参数的构造方法”。因此，如果父类中只定义了有参数的构造方法，而在子类的构造方法中又没有用 `super()`来调用父类中特定的构造方法，则编译时将发生错误，因为 Java 程序在父类中找不到没有参数的构造方法可供执行。解决办法是在父类里加上一个不做事且没有参数的构造方法。
  * 帮助子类做初始化工作。生成类的对象时自动执行无需调用，不能用void声明

* 成员变量和局部变量
  * 从语法形式上看:成员变量是属于类的，而局部变量是在代码块或方法中定义的变量或是方法的参数；成员变量可以被 public,private,static 等修饰符所修饰，而局部变量不能被访问控制修饰符及 static 所修饰；但是，成员变量和局部变量都能被 final 所修饰。
  * 从变量在内存中的存储方式来看:如果成员变量是使用`static`修饰的，那么这个成员变量是属于类的，如果没有使用`static`修饰，这个成员变量是属于实例的。而对象存在于堆内存，局部变量则存在于栈内存。
  * 从变量在内存中的生存时间上看: 成员变量是对象的一部分，它随着对象的创建而存在，而局部变量随着方法的调用而自动消失。
  * 成员变量如果没有被赋初值:则会自动以类型的默认值而赋值（一种情况例外:被 final 修饰的成员变量也必须显式地赋值），而局部变量则不会自动赋值。

* 对象实体和对象引用
  
  * new 运算符，new 创建对象实例（对象实例在堆内存中），对象引用指向对象实例（对象引用存放在栈内存中）。一个对象引用可以指向 0 个或 1 个对象（一根绳子可以不系气球，也可以系一个气球）;一个对象可以有 n 个引用指向它（可以用 n 条绳子系住一个气球）。
  
* 封装：
  
  * 封装是指把一个对象的状态信息（也就是属性）隐藏在对象内部，不允许外部对象直接访问对象的内部信息。但是可以提供一些可以被外界访问的方法来操作属性。就好像我们看不到挂在墙上的空调的内部的零件信息（也就是属性），但是可以通过遥控器（方法）来控制空调。如果属性不想被外界访问，我们大可不必提供方法给外界访问。但是如果一个类没有提供给外界访问的方法，那么这个类也没有什么意义了。就好像如果没有空调遥控器，那么我们就无法操控空凋制冷，空调本身就没有意义了（当然现在还有很多其他方法 ，这里只是为了举例子）。
  
* 继承
  * 子类拥有父类对象所有的属性和方法（包括私有属性和私有方法），但是父类中的私有属性和方法子类是无法访问，**只是拥有**。
  * 子类可以拥有自己属性和方法，即子类可以对父类进行扩展。
  * 子类可以用自己的方式实现父类的方法。（以后介绍）。
  
* 多态：表示一个对象具有多种的状态。具体表现为父类的引用指向子类的实例。
  * 对象类型和引用类型之间具有继承（类）/实现（接口）的关系；
  * 引用类型变量发出的方法调用的到底是哪个类中的方法，必须在程序运行期间才能确定；
  * 多态不能调用“只在子类存在但在父类不存在”的方法；
  * 如果子类重写了父类的方法，真正执行的是子类覆盖的方法，如果子类没有覆盖父类的方法，执行的是父类的方法。

* 静态方法和实例方法
  * 由于静态方法可以不通过对象进行调用，因此在静态方法里，**不能调用其他非静态变量**，也不可以访问非静态变量成员。
  * 静态方法在访问本类的成员时，只允许访问静态成员（即静态成员变量和静态方法），而不允许访问实例成员变量和实例方法；实例方法则无此限制。
  * 在外部调用静态方法时，可以使用"类名.方法名"的方式，也可以使用"对象名.方法名"的方式。而实例方法只有后面这种方式。也就是说，调用静态方法可以无需创建对象。

* String、StringBuilder、StringBuffer

  * `String` 类中使用 final 关键字修饰字符数组来保存字符串，`private final char value[]`，所以`String` 对象是不可变的。

  * 在 Java 9 之后，String 、`StringBuilder` 与 `StringBuffer` 的实现改用 byte 数组存储字符串 `private final byte[] value`

  * 而 `StringBuilder` 与 `StringBuffer` 都继承自 `AbstractStringBuilder` 类，在 `AbstractStringBuilder` 中也是使用字符数组保存字符串`char[]value` 但是没有用 `final` 关键字修饰，所以这两种对象都是可变的。

    `StringBuilder` 与 `StringBuffer` 的构造方法都是调用父类构造方法也就是`AbstractStringBuilder` 实现的

  * `String` 中的对象是不可变的，也就可以理解为常量，线程安全。`AbstractStringBuilder` 是 `StringBuilder` 与 `StringBuffer` 的公共父类，定义了一些字符串的基本操作，如 `expandCapacity`、`append`、`insert`、`indexOf` 等公共方法。`StringBuffer` 对方法加了同步锁或者对调用的方法加了同步锁，所以是线程安全的。`StringBuilder` 并没有对方法进行加同步锁，所以是非线程安全的。

  * 每次对 `String` 类型进行改变的时候，都会生成一个新的 `String` 对象，然后将指针指向新的 `String` 对象。`StringBuffer` 每次都会对 `StringBuffer` 对象本身进行操作，而不是生成新的对象并改变对象引用。相同情况下使用 `StringBuilder` 相比使用 `StringBuffer` 仅能获得 10%~15% 左右的性能提升，但却要冒多线程不安全的风险。

  * 操作少量的数据: 适用 `String`

  * 单线程操作字符串缓冲区下操作大量数据: 适用 `StringBuilder`

  * 多线程操作字符串缓冲区下操作大量数据: 适用 `StringBuffer`



### 并发编程

* synchronized原理

  * synchronized是java提供的原子性内置锁，这种内置的并且使用者看不到的锁也被称为**监视器锁**，使用synchronized之后，会在编译之后在同步的代码块前后加上monitorenter和monitorexit字节码指令，他依赖操作系统底层互斥锁实现。他的作用主要就是实现原子性操作和解决共享变量的内存可见性问题。

    执行monitorenter指令时会尝试获取对象锁，如果对象没有被锁定或者已经获得了锁，锁的计数器+1。此时其他竞争锁的线程则会进入等待队列中。

    执行monitorexit指令时则会把计数器-1，当计数器值为0时，则锁释放，处于等待队列中的线程再继续竞争锁。

  * synchronized是排它锁，当一个线程获得锁之后，其他线程必须等待该线程释放锁后才能获得锁，而且由于Java中的线程和操作系统原生线程是一一对应的，线程被阻塞或者唤醒时时会从用户态切换到内核态，这种上下文切换非常消耗性能。

  * 从内存语义来说，加锁的过程会清除工作内存中的共享变量，再从主内存读取，而释放锁的过程则是将工作内存中的共享变量写回主内存。

  * 如果再深入到源码来说，synchronized实际上有两个队列waitSet和entryList。
    1. 当多个线程进入同步代码块时，首先进入entryList
    2. 有一个线程获取到monitor锁后，就把当前锁赋值给当前线程，并且计数器+1
    3. 如果线程调用wait方法，将释放锁，当前线程置为null，计数器-1，同时进入waitSet等待被唤醒，调用notify或者notifyAll之后又会进入entryList竞争锁
    4. 如果线程执行完毕，同样释放锁，计数器-1，当前线程置为null

![image-20210323160016409](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210323160016409.png)

* 锁优化机制

  * ![image-20210323160234768](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210323160234768.png)

  * 从JDK1.6版本之后，synchronized本身也在不断优化锁的机制，有些情况下他并不会是一个很重量级的锁了。优化机制包括自适应锁、自旋锁、锁消除、锁粗化、轻量级锁和偏向锁。

    锁的状态从低到高依次为**无锁->偏向锁->轻量级锁->重量级锁**，升级的过程就是从低到高，降级在一定条件也是有可能发生的。

    **偏向锁**：当线程访问同步块获取锁时，该锁会在**对象头和栈帧**中的**锁记录**里存储**偏向锁的线程ID**，之后这个线程再次进入同步块时**都不需要CAS**来加锁和解锁了，偏向锁会永远偏向第一个获得锁的线程，如果后续没有其他线程获得过这个锁，持有锁的线程就永远不需要进行同步，反之，当有其他线程竞争偏向锁时，持有偏向锁的线程就会**释放偏向锁**。可以用过设置-XX:+UseBiasedLocking开启偏向锁。

    **自旋锁**：由于大部分时候，锁被占用的时间很短，共享变量的锁定时间也很短，所有没有必要挂起线程，用户态和内核态的来回上下文切换严重影响性能。自旋的概念就是让线程执行一个忙循环，可以理解为就是啥也不干，防止从用户态转入内核态，自旋锁可以通过设置-XX:+UseSpining来开启，自旋的默认次数是10次，可以使用-XX:PreBlockSpin设置。

    **自适应锁**：自适应锁就是**自适应的自旋锁**，自旋的时间不是固定时间，而是由前一次在同一个锁上的自旋时间和锁的持有者状态来决定。

    **锁消除**：锁消除指的是JVM检测到一些同步的代码块，完全不存在数据竞争的场景，也就是不需要加锁，就会进行锁消除。

    **锁粗化**：锁粗化指的是有很多操作都是对同一个对象进行加锁，就会**把锁的同步范围扩展到整个操作序列之外。**

    **轻量级锁**：JVM的对象的对象头中包含有一些锁的标志位，代码进入同步块的时候，JVM将会使用CAS方式来尝试获取锁，如果更新成功则会把对象头中的状态位标记为轻量级锁，如果更新失败，当前线程就尝试自旋来获得锁。

    简单点说，偏向锁就是通过锁的对象头的偏向线程ID来对比，甚至都不需要CAS了，而轻量级锁主要就是通过CAS修改对象头锁记录和自旋来实现，重量级锁则是除了拥有锁的线程其他全部阻塞。

* 锁的对象头

  * 在我们常用的Hotspot虚拟机中，对象在内存中布局实际包含3个部分：

    1. 对象头
    2. 实例数据
    3. 对齐填充

    而对象头包含两部分内容，Mark Word中的内容会随着锁标志位而发生变化，所以只说存储结构就好了。

    1. 对象自身运行时所需的数据，也被称为Mark Word，也就是用于轻量级锁和偏向锁的关键点。具体的内容包含对象的hashcode、分代年龄、轻量级锁指针、重量级锁指针、GC标记、偏向锁线程ID、偏向锁时间戳。
    2. 存储类型指针，也就是指向类的元数据的指针，通过这个指针才能确定对象是属于哪个类的实例。

* ReentrantLock

  * 相比于synchronized，ReentrantLock需要显式的获取锁和释放锁，相对现在基本都是用JDK7和JDK8的版本，ReentrantLock的效率和synchronized区别基本可以持平了。他们的主要区别有以下几点：

    1. 等待可中断，当持有锁的线程长时间不释放锁的时候，等待中的线程可以选择放弃等待，转而处理其他的任务。
    2. 公平锁：synchronized和ReentrantLock默认都是非公平锁，但是ReentrantLock可以通过构造函数传参改变。只不过使用公平锁的话会导致性能急剧下降。
    3. 绑定多个条件：ReentrantLock可以同时绑定多个Condition条件对象。

    ReentrantLock基于AQS(**AbstractQueuedSynchronizer 抽象队列同步器**)实现。

    AQS内部维护一个state状态位，尝试加锁的时候通过CAS(CompareAndSwap)修改值，如果成功设置为1，并且把当前线程ID赋值，则代表加锁成功，一旦获取到锁，其他的线程将会被阻塞进入阻塞队列自旋，获得锁的线程释放锁的时候将会唤醒阻塞队列中的线程，释放锁的时候则会把state重新置为0，同时当前线程ID置为空。

    ![image-20210323161500442](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210323161500442.png)

* CAS

  * CAS叫做CompareAndSwap，比较并交换，主要是通过处理器的指令来保证操作的原子性，它包含三个操作数：

    1. 变量内存地址，V表示
    2. 旧的预期值，A表示
    3. 准备设置的新值，B表示

    当执行CAS指令时，只有当V等于A时，才会用B去更新V的值，否则就不会执行更新操作。

  * **ABA问题**：ABA的问题指的是在CAS更新的过程中，当读取到的值是A，然后准备赋值的时候仍然是A，但是实际上有可能A的值被改成了B，然后又被改回了A，这个CAS更新的漏洞就叫做ABA。只是ABA的问题大部分场景下都不影响并发的最终效果。

    Java中有AtomicStampedReference来解决这个问题，他加入了预期标志和更新后标志两个字段，更新时不光检查值，还要检查当前的标志是否等于预期标志，全部相等的话才会更新。

    **循环时间长开销大**：自旋CAS的方式如果长时间不成功，会给CPU带来很大的开销。

    **只能保证一个共享变量的原子操作**：只对一个共享变量操作可以保证原子性，但是多个则不行，多个可以通过AtomicReference来处理或者使用锁synchronized实现。

### 基本知识

* String
  * 字符串有长度限制，在编译期，要求字符串常量池中的常量不能超过65535，并且在javac执行过程中控制了最大值为65534。
  * 在运行期，长度不能超过Int的范围，否则会抛异常。
  
* HashMap

  * HashMap主要由数组和链表组成，他不是线程安全的。核心的点就是put插入数据的过程，get查询数据以及扩容的方式。JDK1.7和1.8的主要区别在于头插和尾插方式的修改，头插容易导致HashMap链表死循环，并且1.8之后加入红黑树对性能有提升。

  * put插入数据流程

    往map插入元素的时候首先通过对key hash然后与数组长度-1进行与运算： **((n-1)&hash)**，都是2的次幂所以等同于取模，但是位运算的效率更高。找到数组中的位置之后，如果数组中没有元素直接存入，反之则判断key是否相同，key相同就覆盖，否则就会插入到链表的尾部，如果链表的长度超过8，则会转换成红黑树，最后判断数组长度是否超过默认的 长度16*负载因子0.75 也就是12，超过则进行扩容。

  * **get查询数据**

    查询数据相对来说就比较简单了，首先计算出hash值，然后去数组查询，是红黑树就去红黑树查，链表就遍历链表查询就可以了。

    **resize扩容过程**

    扩容的过程就是对key重新计算hash，然后把数据拷贝到新的数组。

* concurrentMap

  * 多线程环境可以使用Collections.synchronizedMap同步加锁的方式，还可以使用HashTable，但是同步的方式显然性能不达标，而ConurrentHashMap更适合高并发场景使用。

    ConcurrentHashmap在JDK1.7和1.8的版本改动比较大，1.7使用Segment+HashEntry分段锁的方式实现，1.8则抛弃了Segment，改为使用CAS+synchronized+Node实现，同样也加入了红黑树，避免链表过长导致性能的问题。

  * **1.7分段锁**

    从结构上说，1.7版本的ConcurrentHashMap采用分段锁机制，里面包含一个Segment数组，Segment继承于ReentrantLock，Segment则包含HashEntry的数组，HashEntry本身就是一个链表的结构，具有保存key、value的能力，以及有能指向下一个节点的指针。

    实际上就是相当于每个Segment都是一个HashMap，默认的Segment长度是16，也就是支持16个线程的并发写，Segment之间相互不会受到影响。

    * **put流程**

      其实发现整个流程和HashMap非常类似，只不过是先定位到具体的Segment，然后通过ReentrantLock去操作而已，后面的流程我就简化了，因为和HashMap基本上是一样的。

      1. 计算hash，定位到segment，segment如果是空就先初始化
      2. 使用ReentrantLock加锁，如果获取锁失败则尝试自旋，自旋超过次数就阻塞获取，保证一定获取锁成功
      3. 遍历HashEntry，就是和HashMap一样，数组中key和hash一样就直接替换，不存在就再插入链表，链表同样

    * **get流程**

      get也很简单，key通过hash定位到segment，再遍历链表定位到具体的元素上，需要注意的是value是volatile的，所以get是不需要加锁的。

  * 1.8 CAS + synchronized

    * 1.8抛弃分段锁，转为用CAS+synchronized来实现，同样HashEntry改为Node，也加入了红黑树的实现。主要还是看put的流程。

    * **put流程**

      1. 首先计算hash，遍历node数组，如果node是空的话，就通过CAS+自旋的方式初始化
      2. 如果当前数组位置是空则直接通过CAS自旋写入数据
      3. 如果hash==MOVED，说明需要扩容，执行扩容
      4. 如果都不满足，就使用synchronized写入数据，写入数据同样判断链表、红黑树，链表写入和HashMap的方式一样，key hash一样就覆盖，反之就尾插法，链表长度超过8就转换成红黑树

    * **get查询**

      get很简单，通过key计算hash，如果key hash相同就返回，如果是红黑树按照红黑树获取，都不是就遍历链表获取。

* volatile

  * 相比synchronized的加锁方式来解决共享变量的内存可见性问题，volatile就是更轻量的选择，他没有上下文切换的额外开销成本。使用volatile声明的变量，可以确保值被更新的时候对其他线程立刻可见。volatile使用内存屏障来保证不会发生指令重排，解决了内存可见性的问题。

    我们知道，线程都是从主内存中读取共享变量到工作内存来操作，完成之后再把结果写会主内存，但是这样就会带来可见性问题。举个例子，假设现在我们是两级缓存的双核CPU架构，包含L1、L2两级缓存。

    1. 线程A首先获取变量X的值，由于最初两级缓存都是空，所以直接从主内存中读取X，假设X初始值为0，线程A读取之后把X值都修改为1，同时写回主内存。这时候缓存和主内存的情况如下图。

    2. 线程B也同样读取变量X的值，由于L2缓存已经有缓存X=1，所以直接从L2缓存读取，之后线程B把X修改为2，同时写回L2和主内存。这时候的X值入下图所示。

       那么线程A如果再想获取变量X的值，因为L1缓存已经有x=1了，所以这时候变量内存不可见问题就产生了，B修改为2的值对A来说没有感知。

    3. 那么，如果X变量用volatile修饰的话，当线程A再次读取变量X的话，CPU就会根据缓存一致性协议强制线程A重新从主内存加载最新的值到自己的工作内存，而不是直接用缓存中的值。

       再来说内存屏障的问题，volatile修饰之后会加入不同的内存屏障来保证可见性的问题能正确执行。这里写的屏障基于书中提供的内容，但是实际上由于CPU架构不同，重排序的策略不同，提供的内存屏障也不一样，比如x86平台上，只有StoreLoad一种内存屏障。

       1. StoreStore屏障，保证上面的普通写不和volatile写发生重排序
       2. StoreLoad屏障，保证volatile写与后面可能的volatile读写不发生重排序
       3. LoadLoad屏障，禁止volatile读与后面的普通读重排序
       4. LoadStore屏障，禁止volatile读和后面的普通写重排序

* JVM

  * 本身随着CPU和内存的发展速度差异的问题，导致CPU的速度远快于内存，所以现在的CPU加入了高速缓存，高速缓存一般可以分为L1、L2、L3三级缓存。基于上面的例子我们知道了这导致了缓存一致性的问题，所以加入了缓存一致性协议，同时导致了内存可见性的问题，而编译器和CPU的重排序导致了原子性和有序性的问题，JMM内存模型正是对多线程操作下的一系列规范约束，因为不可能让陈雇员的代码去兼容所有的CPU，通过JMM我们才屏蔽了不同硬件和操作系统内存的访问差异，这样保证了Java程序在不同的平台下达到一致的内存访问效果，同时也是保证在高效并发的时候程序能够正确执行。![image-20210323162702754](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210323162702754.png)

  * **原子性**：Java内存模型通过read、load、assign、use、store、write来保证原子性操作，此外还有lock和unlock，直接对应着synchronized关键字的monitorenter和monitorexit字节码指令。

    **可见性**：可见性的问题在上面的回答已经说过，Java保证可见性可以认为通过volatile、synchronized、final来实现。

    **有序性**：由于处理器和编译器的重排序导致的有序性问题，Java通过volatile、synchronized来保证。

    **happen-before规则**

    虽然指令重排提高了并发的性能，但是Java虚拟机会对指令重排做出一些规则限制，并不能让所有的指令都随意的改变执行位置，主要有以下几点：

    1. 单线程每个操作，happen-before于该线程中任意后续操作
    2. volatile写happen-before与后续对这个变量的读
    3. synchronized解锁happen-before后续对这个锁的加锁
    4. final变量的写happen-before于final域对象的读，happen-before后续对final变量的读
    5. 传递性规则，A先于B，B先于C，那么A一定先于C发生

  主内存可以认为就是物理内存，Java内存模型中实际就是虚拟机内存的一部分。而工作内存就是CPU缓存，他有可能是寄存器也有可能是L1\L2\L3缓存，都是有可能的。

* 引用类型

  * 引用类型主要分为强软弱虚四种：
    1. 强引用指的就是代码中普遍存在的赋值方式，比如A a = new A()这种。强引用关联的对象，永远不会被GC回收。
    2. 软引用可以用SoftReference来描述，指的是那些有用但是不是必须要的对象。系统在发生内存溢出前会对这类引用的对象进行回收。
    3. 弱引用可以用WeakReference来描述，他的强度比软引用更低一点，弱引用的对象下一次GC的时候一定会被回收，而不管内存是否足够。
    4. 虚引用也被称作幻影引用，是最弱的引用关系，可以用PhantomReference来描述，他必须和ReferenceQueue一起使用，同样的当发生GC的时候，虚引用也会被回收。可以用虚引用来管理堆外内存。

* ThreadLocal

  * ThreadLocal可以理解为线程本地变量，他会在每个线程都创建一个副本，那么在线程之间访问内部副本变量就行了，做到了线程之间互相隔离，相比于synchronized的做法是用空间来换时间。

    ThreadLocal有一个静态内部类ThreadLocalMap，ThreadLocalMap又包含了一个Entry数组，Entry本身是一个弱引用，他的key是指向ThreadLocal的弱引用，Entry具备了保存key value键值对的能力。

    弱引用的目的是为了防止内存泄露，如果是强引用那么ThreadLocal对象除非线程结束否则始终无法被回收，弱引用则会在下一次GC的时候被回收。

    但是这样还是会存在内存泄露的问题，假如key和ThreadLocal对象被回收之后，entry中就存在key为null，但是value有值的entry对象，但是永远没办法被访问到，同样除非线程结束运行。

    但是只要ThreadLocal使用恰当，在使用完之后调用remove方法删除Entry对象，实际上是不会出现这个问题的。

* 线程池

  * 首先线程池有几个核心的参数概念：

    1. 最大线程数maximumPoolSize
    2. 核心线程数corePoolSize
    3. 活跃时间keepAliveTime
    4. 阻塞队列workQueue
    5. 拒绝策略RejectedExecutionHandler

    当提交一个新任务到线程池时，具体的执行流程如下：

    1. 当我们提交任务，线程池会根据corePoolSize大小创建若干任务数量线程执行任务
    2. 当任务的数量超过corePoolSize数量，后续的任务将会进入阻塞队列阻塞排队
    3. 当阻塞队列也满了之后，那么将会继续创建(maximumPoolSize-corePoolSize)个数量的线程来执行任务，如果任务处理完成，maximumPoolSize-corePoolSize额外创建的线程等待keepAliveTime之后被自动销毁
    4. 如果达到maximumPoolSize，阻塞队列还是满的状态，那么将根据不同的拒绝策略对应处理

  * 主要有4种拒绝策略：

    1. AbortPolicy：直接丢弃任务，抛出异常，这是默认策略
    2. CallerRunsPolicy：只用调用者所在的线程来处理任务
    3. DiscardOldestPolicy：丢弃等待队列中最旧的任务，并执行当前任务
    4. DiscardPolicy：直接丢弃任务，也不抛出异常

### IO

* 按照流的流向分，可以分为输入流和输出流；
* 按照操作单元划分，可以划分为字节流和字符流；
* 按照流的角色划分为节点流和处理流。
* InputStream/Reader: 所有的输入流的基类，前者是字节输入流，后者是字符输入流。
* OutputStream/Writer: 所有输出流的基类，前者是字节输出流，后者是字符输出流。

* BIO：同步阻塞 IO 模型中，应用程序发起 read 调用后，会一直阻塞，直到在内核把数据拷贝到用户空间。

* NIO：

  * Java 中的 NIO 于 Java 1.4 中引入，对应 `java.nio` 包，提供了 `Channel` , `Selector`，`Buffer` 等抽象。NIO 中的 N 可以理解为 Non-blocking，不单纯是 New。它支持面向缓冲的，基于通道的 I/O 操作方法。 对于高负载、高并发的（网络）应用，应使用 NIO 。

  * Java 中的 NIO 可以看作是 **I/O 多路复用模型**。

  * ![image-20210324133900745](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210324133900745.png)

  * IO 多路复用模型中，线程首先发起 select 调用，询问内核数据是否准备就绪，等内核把数据准备好了，用户线程再发起 read 调用。read 调用的过程（数据从内核空间->用户空间）还是阻塞的。

    > 目前支持 IO 多路复用的系统调用，有 select，epoll 等等。select 系统调用，是目前几乎在所有的操作系统上都有支持
    >
    > - **select 调用** ：内核提供的系统调用，它支持一次查询多个系统调用的可用状态。几乎所有的操作系统都支持。
    > - **epoll 调用** ：linux 2.6 内核，属于 select 调用的增强版本，优化了 IO 的执行效率。

  * **IO 多路复用模型，通过减少无效的系统调用，减少了对 CPU 资源的消耗。**

    Java 中的 NIO ，有一个非常重要的**选择器 ( Selector )** 的概念，也可以被称为 **多路复用器**。通过它，只需要一个线程便可以管理多个客户端连接。当客户端数据到了之后，才会为其服务。

  * ![image-20210324140229620](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210324140229620.png)

* AIO：异步 IO 是基于事件和回调机制实现的，也就是应用操作之后会直接返回，不会堵塞在那里，当后台处理完成，操作系统会通知相应的线程进行后续的操作。

  

* 深拷贝、浅拷贝
  * **浅拷贝**：对基本数据类型进行值传递，对引用数据类型进行引用传递般的拷贝，此为浅拷贝。
  * **深拷贝**：对基本数据类型进行值传递，对引用数据类型，创建一个新的对象，并复制其内容，此为深拷贝。![image-20210324152558218](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210324152558218.png)

### 代理

* 反射

  * Java反射的原理:   java类的执行需要经历以下过程，

    **编译**:.java文件编译后生成.class字节码文件
  **加载**：类加载器负责根据一个**类的全限定名**来读取此类的**二进制字节流到JVM内部**，并存储在**运行时内存区的方法区**，然后将其转换为一个与目标类型对应的 **java.lang.Class对象实例**
    **连接**：细分三步
      验证：格式（class文件规范） 语义（final类是否有子类） 操作
        准备：静态变量赋初值和内存空间，final修饰的内存空间直接赋原值，此处不是用户指定的初值。
      解析：符号引用转化为直接引用，分配地址
    **初始化**:有父类先初始化父类，然后初始化自己；将static修饰代码执行一遍，如果是静态变量，则用用户指定值覆盖原有初值；如果是代码块，则执行一遍操作。

    Java的反射就是利用上面第二步**加载到jvm中的.class文件**来进行操作的。**.class文件中包含java类的所有信息**，当你不知道某个类具体信息时，可以使用反射**获取class**，然后进行各种操作。

    Java反射就是在**运行状态**中，对于任意一个类，都能够知道这个类的**所有属性和方法**；对于任意一个对象，都能够调用它的任意方法和属性；并且**能改变它的属性（有安全性问题）**。总结说：反射就是把java类中的**各种成分映射成一个个的Java对象**，并且可以进行操作。

  * 反射之所以被称为框架的灵魂，主要是因为它赋予了我们**在运行时分析类以及执行类中方法的能力**。

    通过反射你可以**获取任意一个类的所有属性和方法，你还可以调用这些方法和属性**。

  * **优点** ： 可以让咱们的代码更加灵活、为各种框架提供开箱即用的功能提供了便利

    **缺点** ：让我们在运行时有了分析操作类的能力，这同样也增加了**安全问题**。比如可以**无视泛型参数的安全检查**（泛型参数的安全检查发生在**编译**时）。另外，反射的**性能也要稍差点**，不过，对于框架来说实际是影响不大的。

  * 应用：注解，例如@Bean、@Value，基于反射分析类，然后获取到类/属性/方法/方法的参数上的注解。你获取到注解之后，就可以做进一步的处理。像 Spring/Spring Boot、MyBatis 等等框架中都大量使用了反射机制。

    **这些框架中也大量使用了动态代理，而动态代理的实现也依赖反射。**

  * 反射获取Class对象

    * **1.知道具体类的情况下可以使用：**

      ```
      Class alunbarClass = TargetObject.class;
      ```

      但是我们一般是不知道具体类的，基本都是通过**遍历包下面的类**来获取 Class 对象，通过此方式获取 Class 对象不会进行初始化

      **2.通过 `Class.forName()`传入类的路径获取：**

      ```
      Class alunbarClass1 = Class.forName("cn.javaguide.TargetObject");
      ```

      **3.通过对象实例`instance.getClass()`获取：**

      ```
      TargetObject o = new TargetObject();
      Class alunbarClass2 = o.getClass();
      ```

      **4.通过类加载器`xxxClassLoader.loadClass()`传入类路径获取:**

      ```
      class clazz = ClassLoader.LoadClass("cn.javaguide.TargetObject");
      ```

  * 反射基本操作

    ```java
    public class Main {
        public static void main(String[] args) throws ClassNotFoundException, NoSuchMethodException, IllegalAccessException, InstantiationException, InvocationTargetException, NoSuchFieldException {
            /**
             * 获取TargetObject类的Class对象并且创建TargetObject类实例
             */
            Class<?> tagetClass = Class.forName("cn.javaguide.TargetObject");
            TargetObject targetObject = (TargetObject) tagetClass.newInstance();
            /**
             * 获取所有类中所有定义的方法
             */
            Method[] methods = tagetClass.getDeclaredMethods();
            for (Method method : methods) {
                System.out.println(method.getName());
            }
            /**
             * 获取指定方法并调用
             */
            Method publicMethod = tagetClass.getDeclaredMethod("publicMethod",
                    String.class);
            publicMethod.invoke(targetObject, "JavaGuide"); 
            /**
             * 获取指定参数并对参数进行修改
             */
            Field field = tagetClass.getDeclaredField("value");
            //为了对类中的参数进行修改我们取消安全检查
            field.setAccessible(true);
            field.set(targetObject, "JavaGuide");
            /**
             * 调用 private 方法
             */
            Method privateMethod = tagetClass.getDeclaredMethod("privateMethod");
            //为了调用private方法我们取消安全检查
            privateMethod.setAccessible(true);
            privateMethod.invoke(targetObject);
        }
    }
    ```

* 代理模式

  * 使用代理对象来代替对真实对象(real object)的访问，这样就可以在**不修改原目标对象**的前提下，提供**额外的功能操作**，扩展目标对象的功能。代理模式的主要作用是**扩展目标对象的功能**，比如说在目标对象的某个方法执行前后你可以增加一些自定义的操作。

* 静态代理

  * 从 JVM 层面来说， **静态代理在编译时就将接口、实现类、代理类这些都变成了一个个实际的 class 文件。**
  * 静态代理实现步骤:
    1. 定义一个接口及其实现类；
    2. 创建一个代理类同样实现这个接口
    3. 将目标对象注入进代理类，然后在代理类的对应方法调用目标类中的对应方法。这样的话，我们就可以通过代理类屏蔽对目标对象的访问，并且可以在目标方法执行前后做一些自己想做的事情。

* JDK动态代理

  * **从 JVM 角度来说，动态代理是在运行时动态生成类字节码，并加载到 JVM 中的。**

    说到动态代理，Spring AOP、RPC 框架应该是两个不得不的提的，它们的实现都依赖了动态代理。

  * **在 Java 动态代理机制中 `InvocationHandler` 接口和 `Proxy` 类是核心。**

    `Proxy` 类中使用频率最高的方法是：`newProxyInstance()` ，这个方法主要用来**生成一个代理对象**。这个方法一共有 3 个参数：

    1. **loader** :类加载器，用于加载代理对象。

    2. **interfaces** : 被代理类实现的一些接口；

    3. **h** : 实现了 `InvocationHandler` 接口的对象；

       ```
           public static Object newProxyInstance(ClassLoader loader,
                                                 Class<?>[] interfaces,
                                                 InvocationHandler h)
               throws IllegalArgumentException
           {
               ......
           }
       ```

  * 要实现动态代理的话，还必须需要实现`InvocationHandler` 来自定义处理逻辑。 当我们的动态代理对象调用一个方法时候，这个方法的调用就会被转发到实现`InvocationHandler` 接口类的 `invoke` 方法来调用。

    ```
    public interface InvocationHandler {
        /**
         * 当你使用代理对象调用方法的时候实际会调用到这个方法
         */
        public Object invoke(Object proxy, Method method, Object[] args)
            throws Throwable;
    }
    ```

  * `invoke()` 方法有下面三个参数：

    1. **proxy** :动态生成的代理类
    2. **method** : 与代理类对象调用的方法相对应
    3. **args** : 当前 method 方法的参数

    也就是说：**通过`Proxy` 类的 `newProxyInstance()` 创建的代理对象在调用方法的时候，实际会调用到实现`InvocationHandler` 接口的类的 `invoke()`方法。** 你可以在 `invoke()` 方法中自定义处理逻辑，比如在方法执行前后做什么事情。、

  * 使用步骤：

    * 定义一个接口及其实现类；

    *  定义一个方法实现 `InvocationHandler` 并重写`invoke`方法，在 `invoke` 方法中我们会**调用原生方法（被代理类的方法）**并自定义一些**处理逻辑**；

    *  通过 `Proxy.newProxyInstance(ClassLoader loader,Class<?>[] interfaces,InvocationHandler h)` 方法**创建代理对象**；

       ```java
       // invoke() 方法: 当我们的动态代理对象调用原生方法的时候，最终实际上调用到的是 invoke() 方法，然后 invoke() 方法代替我们去调用了被代理对象的原生方法。
       
       // 获取代理对象的工厂类
       public class JdkProxyFactory {
           public static Object getProxy(Object target) {
               return Proxy.newProxyInstance(
                       target.getClass().getClassLoader(), // 目标类的类加载
                       target.getClass().getInterfaces(),  // 代理需要实现的接口，可指定多个
                       new DebugInvocationHandler(target)   // 代理对象对应的自定义 InvocationHandler
               );
           }
       }
       // getProxy() ：主要通过Proxy.newProxyInstance（）方法获取某个类的代理对象
       SmsService smsService = (SmsService) JdkProxyFactory.getProxy(new SmsServiceImpl());
       ```

* CGLIB动态代理

  * **JDK 动态代理有一个最致命的问题是其只能代理实现了接口的类。**而CGLIB不需要我们必须实现接口，我们可以直接代理实现类( *CGLIB 动态代理机制*)。

  * [CGLIB](https://github.com/cglib/cglib)(*Code Generation Library*)是一个基于[ASM](http://www.baeldung.com/java-asm)的字节码生成库，它允许我们**在运行时对字节码进行修改和动态生成**。CGLIB 通过**继承方式**实现代理。很多知名的开源框架都使用到了[CGLIB](https://github.com/cglib/cglib)， 例如 Spring 中的 AOP 模块中：如果目标对象实现了接口，则默认采用 JDK 动态代理，否则采用 CGLIB 动态代理。

    **在 CGLIB 动态代理机制中 `MethodInterceptor` 接口和 `Enhancer` 类是核心。**

    你需要自定义 `MethodInterceptor` 并重写 `intercept` 方法，`intercept` 用于拦截增强被代理类的方法。

    ```
    public interface MethodInterceptor extends Callback{
        // 拦截被代理类中的方法
        public Object intercept(Object obj, java.lang.reflect.Method method, Object[] args,
                                   MethodProxy proxy) throws Throwable;
    }
    ```
  
  1. **obj** :被代理的对象（需要增强的对象）
    2. **method** :被拦截的方法（需要增强的方法）
    3. **args** : 方法入参
    4. **methodProxy** :用于调用原始方法
  
  你可以通过 `Enhancer`类来**动态获取被代理类**，当代理类调用方法的时候，实际调用的是 `MethodInterceptor` 中的 `intercept` 方法。
  
  * CGLIB 动态代理类使用步骤
  
  1. 定义一个类；
  
  2. 实现 `MethodInterceptor` 并重写 `intercept` 方法，`intercept` 用于拦截增强被代理类的方法，和 JDK 动态代理中的 `invoke` 方法类似；
  
     ```java
       public class DebugMethodInterceptor implements MethodInterceptor {
         /**
            * @param o           被代理的对象（需要增强的对象）
            * @param method      被拦截的方法（需要增强的方法）
            * @param args        方法入参
            * @param methodProxy 用于调用原始方法
            */
           @Override
           public Object intercept(Object o, Method method, Object[] args, MethodProxy methodProxy) throws Throwable {
               //调用方法之前，我们可以添加自己的操作
               System.out.println("before method " + method.getName());
               Object object = methodProxy.invokeSuper(o, args);
               //调用方法之后，我们同样可以添加自己的操作
               System.out.println("after method " + method.getName());
               return object;
           }
       }
     ```
  
     通过 `Enhancer` 类的 `create()`创建代理类；
  
     ```java
       public class CglibProxyFactory {
           public static Object getProxy(Class<?> clazz) {
               // 创建动态代理增强类
               Enhancer enhancer = new Enhancer();
             // 设置类加载器
               enhancer.setClassLoader(clazz.getClassLoader());
             // 设置被代理类
               enhancer.setSuperclass(clazz);
               // 设置方法拦截器
               enhancer.setCallback(new DebugMethodInterceptor());
               // 创建代理类
               return enhancer.create();
           }
       }
       // 生成代理对象
       AliSmsService aliSmsService = (AliSmsService) CglibProxyFactory.getProxy(AliSmsService.class);
     ```
  
  * 对比：
  
    * **JDK 动态代理只能只能代理实现了接口的类或者直接代理接口，而 CGLIB 可以代理未实现任何接口的类。** 另外， CGLIB 动态代理是通过生成一个**被代理类的子类来拦截被代理类的方法调用**，因此**不能代理声明为 final 类型的类和方法。**
    * 就二者的效率来说，大部分情况都是 JDK 动态代理更优秀，随着 JDK 版本的升级，这个优势更加明显。
    * **灵活性** ：动态代理更加灵活，不需要必须实现接口，可以直接代理实现类，并且可以不需要针对每个目标类都创建一个代理类。另外，静态代理中，接口一旦新增加方法，目标对象和代理对象都要进行修改，这是非常麻烦的！
    * **JVM 层面** ：静态代理在**编译时**就将接口、实现类、代理类这些都变成了一个个实际的 class 文件。而动态代理是在**运行时动态生成类字节码，并加载到 JVM 中的。**
  

### 异常

![image-20210324154707024](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210324154707024.png)

* 在 Java 中，所有的异常都有一个共同的祖先 `java.lang` 包中的 `Throwable` 类。`Throwable` 类有两个重要的子类 `Exception`（异常）和 `Error`（错误）。`Exception` 能被程序本身处理(`try-catch`)， `Error` 是无法处理的(只能尽量避免)。

  `Exception` 和 `Error` 二者都是 Java 异常处理的重要子类，各自都包含大量子类。

  - **`Exception`** :程序本身可以处理的异常，可以通过 `catch` 来进行捕获。`Exception` 又可以分为 受检查异常(必须处理) 和 不受检查异常(可以不处理)。
  - **`Error`** ：`Error` 属于程序无法处理的错误 ，我们没办法通过 `catch` 来进行捕获 。例如，Java 虚拟机运行错误（`Virtual MachineError`）、虚拟机内存不够错误(`OutOfMemoryError`)、类定义错误（`NoClassDefFoundError`）等 。这些异常发生时，Java 虚拟机（JVM）一般会选择线程终止。

* 受检查异常
  * Java 代码在编译过程中，如果受检查异常没有被 `catch`/`throw` 处理的话，就没办法通过编译 。
  * 除了`RuntimeException`及其子类不是受检查异常以外，其他的`Exception`类及其子类都属于受检查异常 。常见的受检查异常有： IO 相关的异常、`ClassNotFoundException` 、`SQLException`...。

* 不受检查异常

  * Java 代码在编译过程中 ，我们即使不处理不受检查异常也可以正常通过编译。

    `RuntimeException` 及其子类都统称为非受检查异常，例如：`NullPointerException`、`NumberFormatException`（字符串转换为数字）、`ArrayIndexOutOfBoundsException`（数组越界）、`ClassCastException`（类型转换错误）、`ArithmeticException`（算术错误）等。

* 3.2.2. Throwable 类常用方法
  * **`public string getMessage()`**:返回异常发生时的简要描述
  * **`public string toString()`**:返回异常发生时的详细信息
  * **`public string getLocalizedMessage()`**:返回异常对象的本地化信息。使用 `Throwable` 的子类覆盖这个方法，可以生成本地化信息。如果子类没有覆盖该方法，则该方法返回的信息与 `getMessage（）`返回的结果相同
  * **`public void printStackTrace()`**:在控制台上打印 `Throwable` 对象封装的异常信息

* 3.2.3. try-catch-finally

  * **`try`块：** 用于捕获异常。其后可接零个或多个 `catch` 块，如果没有 `catch` 块，则必须跟一个 `finally` 块。
  * **`catch`块：** 用于处理 try 捕获到的异常。
  * **`finally` 块：** 无论是否捕获或处理异常，`finally` 块里的语句都会被执行。当在 `try` 块或 `catch` 块中遇到 `return` 语句时，`finally` 语句块将在方法返回之前被执行。
  * 当 try 语句和 finally 语句中都有 return 语句时，在方法返回之前，finally 语句的内容将被执行，并且 finally 语句的返回值将会覆盖原始的返回值
  * **在以下 3 种特殊情况下，`finally` 块不会被执行：**
    1. 在 `try` 或 `finally `块中用了 `System.exit(int)`退出程序。但是，如果 `System.exit(int)` 在异常语句之后，`finally` 还是会被执行
    2. 程序所在的线程死亡。
    3. 关闭 CPU。

* try-with-resource

  * 使用 `try-with-resources` 来代替`try-catch-finally`：在 `try-with-resources` 语句中，任何 catch 或 finally 块在声明的资源关闭后运行

  * Java 中类似于`InputStream`、`OutputStream` 、`Scanner` 、`PrintWriter`等的资源都需要我们调用`close()`方法来手动关闭，一般情况下我们都是通过`try-catch-finally`语句来实现这个需求

  * ```
    try (BufferedInputStream bin = new BufferedInputStream(new FileInputStream(new File("test.txt")));
                 BufferedOutputStream bout = new BufferedOutputStream(new FileOutputStream(new File("out.txt")))) {
                int b;
                while ((b = bin.read()) != -1) {
                    bout.write(b);
                }
            }
            catch (IOException e) {
                e.printStackTrace();
            }
    ```

### 多线程

* 进程和线程区别
  * 进程是程序的一次执行，是系统进行资源分配和调度的独立单位，他的作用是是程序能够并发执行提高资源利用率和吞吐率。

    由于进程是资源分配和调度的基本单位，因为进程的创建、销毁、切换产生大量的时间和空间的开销，进程的数量不能太多，而线程是比进程更小的能独立运行的基本单位，他是进程的一个实体，可以减少程序并发执行时的时间和空间开销，使得操作系统具有更好的并发性。

    线程基本不拥有系统资源，只有一些运行时必不可少的资源，比如程序计数器、寄存器和栈，进程则占有堆、栈。

* 线程状态

  * ![image-20210324155750825](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210324155750825.png)
  * ![image-20210324155810957](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210324155810957.png)
    * 线程创建之后它将处于 **NEW（新建）** 状态，调用 `start()` 方法后开始运行，线程这时候处于 **READY（可运行）** 状态。可运行状态的线程获得了 cpu 时间片（timeslice）后就处于 **RUNNING（运行）** 状态。
    * 当线程执行 `wait()`方法之后，线程进入 **WAITING（等待）** 状态。进入等待状态的线程需要依靠其他线程的通知才能够返回到运行状态，而 **TIME_WAITING(超时等待)** 状态相当于在等待状态的基础上增加了超时限制，比如通过 `sleep（long millis）`方法或 `wait（long millis）`方法可以将 Java 线程置于 TIMED WAITING 状态。当超时时间到达后 Java 线程将会返回到 RUNNABLE 状态。当线程调用同步方法时，在没有获取到锁的情况下，线程将会进入到 **BLOCKED（阻塞）** 状态。线程在执行 Runnable 的`run()`方法之后将会进入到 **TERMINATED（终止）** 状态。


### 面试

* epoll

  * 当网卡把数据写入到内存后，网卡向cpu发出一个中断信号，操作系统便能得知有新数据到来，再通过网卡**中断程序**去处理数据。

  * ecv、select和epoll都是阻塞方法

  * 网络编程基本过程：先新建socket对象，依次调用bind、listen、accept，最后调用recv接收数据。recv是个阻塞方法，当程序运行到recv时，它会一直等待，直到接收到数据才往下执行。

  * **阻塞原理**：操作系统为了支持多任务，实现了进程调度的功能，会把进程分为“运行”和“等待”等几种状态。运行状态是进程获得cpu使用权，正在执行代码的状态；等待状态是阻塞状态，比如上述程序运行到recv时，程序会从运行状态变为等待状态，接收到数据后又变回运行状态。操作系统会分时执行各个运行状态的进程，由于速度很快，看上去就像是同时执行多个任务。

  * **等待队列**：进程A执行到创建socket的语句时，操作系统会创建一个由文件系统管理的socket对象（如下图）。这个socket对象包含了发送缓冲区、接收缓冲区、等待队列等成员。等待队列是个非常重要的结构，它指向所有需要等待该socket事件的进程。

  * 创建**socket**：当程序执行到recv时，操作系统会将进程A从工作队列移动到该socket的等待队列中（如下图）。由于工作队列只剩下了进程B和C，依据进程调度，cpu会轮流执行这两个进程的程序，不会执行进程A的程序。所以进程A被阻塞，不会往下执行代码，也不会占用cpu资源。

  * **唤醒线程**：当socket接收到数据后，操作系统将该socket等待队列上的进程重新放回到工作队列，该进程变成运行状态（当socket事件触发时，也就是有数据到来，会取下一个进程结构调用其回调，将其挂到工作队列中），继续执行代码。也由于socket的接收缓冲区已经有了数据，recv可以返回接收到的数据。

  * **内核接收网络数据过程**：

    * 进程在recv阻塞期间，计算机收到了对方传送的数据。数据经由网卡传送到内存，然后网卡通过**中断信号**通知cpu有数据到达，cpu执行中断程序。此处的中断程序主要有两项功能，先将网络数据写入到对应socket的接收缓冲区里面，再唤醒进程A，重新将进程A放入工作队列中。
    * 数据如何对应socket：因为一个socket对应着一个端口号，而网络数据包中包含了ip和端口的信息，内核可以通过端口号找到对应的socket。当然，为了提高处理速度，操作系统会维护端口号到socket的索引结构，以快速读取。（就是说网卡中断CPU后，CPU的中断函数从网卡存数据的内存拷贝数据到对应fd的接收缓冲区，具体是哪一个fd，CPU会检查port，放到对应的fd中）
    * 如何同时监听多个socket数据：IO多路复用

  * IO多路复用

    * 假如能够预先传入一个socket列表，**如果列表中的socket都没有数据，挂起进程，直到有一个socket收到数据，唤醒进程**。这种方法很直接，也是select的设计思想。
    * select：select的实现思路很直接。假如程序同时监视三个socket，那么在调用select之后，操作系统把进程A（包括了select逻辑）分别加入这三个socket的等待队列中（放的是进程A的引用）。当任何一个socket收到数据后，中断程序将唤起进程。下图展示了sock2接收到了数据的处理流程。
      * 缺点：需要遍历2次socket列表来获得接收到数据的socket
        * 每次调用select都需要将进程加入到所有监视socket的等待队列，每次唤醒都需要从每个队列中移除
        * 进程被唤醒后，程序并不知道哪些socket收到数据，还需要遍历一次（这一次遍历是在应用层）

  * epoll

    * 措施1：**功能分离**：
      * select低效的原因之一是将“维护等待队列”和“阻塞进程”两个步骤合二为一。如下图所示，每次调用select都需要这两步操作，然而大多数应用场景中，需要监视的socket相对固定，并不需要每次都修改。epoll将这两个操作分开，先用**epoll_ctl维护等待队列**，再调用epoll_wait阻塞进程（解耦）。相比select，epoll拆分了功能
    * 措施2：就绪列表：
      * select低效的另一个原因在于程序不知道哪些socket收到数据，只能一个个遍历。如果内核维护一个“就绪列表”，引用收到数据的socket，就能避免遍历。socket收到数据的sock2和sock3被rdlist（就绪列表）所引用。当进程被唤醒后，只要获取rdlist的内容，就能够知道哪些socket收到数据。
    * ![image-20210404155124548](https://gitee.com/picgo-table/picgo-img/raw/master/image/image-20210404155124548.png)
    * 用法：先用**epoll_create**创建一个epoll对象**epfd**，再通过**epoll_ctl**将需要监视的socket添加到epfd中，最后调用**epoll_wait**等待数据。
    * 一般认为如果在并发量低，socket都比较活跃的情况下，select效率更高，也就是说活跃socket数目与监控的总的socket数目之比越大，select效率越高，因为select反正都会遍历所有的socket，如果比例大，就没有白白遍历。加之于select本身实现比较简单，导致总体现象比epoll好）

  * epoll原理

    * **创建epoll对象**

      如下图所示，当某个进程调用epoll_create方法时，内核会创建一个eventpoll对象（也就是程序中epfd所代表的对象）。eventpoll对象也是文件系统中的一员，和socket一样，它也会有等待队列（有线程会等待其事件触发，比如调用epoll_wait的线程就会阻塞在其上）。

    * 内核创建eventpoll对象

      创建一个代表该epoll的eventpoll对象是必须的，因为内核要维护“就绪列表”等数据，“就绪列表”可以作为eventpoll的成员。

      **维护监视列表**

      创建epoll对象后，可以用epoll_ctl添加或删除所要监听的socket。以添加socket为例，如下图，如果通过epoll_ctl添加sock1、sock2和sock3的监视，内核会将eventpoll添加到这三个socket的等待队列中。

    * 添加所要监听的socket

      当socket收到数据后，中断程序会操作eventpoll对象，而不是直接操作进程（也就是调用epoll的进程）。

      **接收数据**

      当socket收到数据后，中断程序会给eventpoll的“就绪列表”添加socket引用。如下图展示的是sock2和sock3收到数据后，中断程序让rdlist引用这两个socket。

    * 给就绪列表添加引用

      eventpoll对象相当于是socket和进程之间的中介，socket的数据接收并不直接影响进程，而是通过改变eventpoll的就绪列表来改变进程状态。

      当程序执行到epoll_wait时，如果rdlist已经引用了socket，那么epoll_wait直接返回，如果rdlist为空，阻塞进程。

      **阻塞和唤醒进程**

      假设计算机中正在运行进程A和进程B，在某时刻进程A运行到了epoll_wait语句。如下图所示，内核会将进程A放入eventpoll的等待队列中，阻塞进程。

    * epoll_wait阻塞进程

      当socket接收到数据，中断程序一方面修改rdlist，另一方面唤醒eventpoll等待队列中的进程，进程A再次进入运行状态（如下图）。也因为rdlist的存在，进程A可以知道哪些socket发生了变化。

      epoll唤醒进程

* 深拷贝

  * 深拷贝，在拷贝引用类型成员变量时，为引用类型的数据成员另辟了一个独立的内存空间，实现真正内容上的拷贝。
  * 浅拷贝只复制指向某个对象的指针，而不复制对象本身，新旧对象还是共享同一块内存。但深拷贝会另外创造一个一模一样的对象，新对象跟原对象不共享内存，修改新对象不会改到原对象。
  * (1) 对于基本数据类型的成员对象，因为基础数据类型是值传递的，所以是直接将属性值赋值给新的对象。基础类型的拷贝，其中一个对象修改该值，不会影响另外一个（和浅拷贝一样）。
     (2) 对于引用类型，比如数组或者类对象，深拷贝会新建一个对象空间，然后拷贝里面的内容，所以它们指向了不同的内存空间。改变其中一个，不会对另外一个也产生影响。
     (3) 对于有多层对象的，每个对象都需要实现 `Cloneable` 并重写 `clone()` 方法，进而实现了对象的串行层层拷贝。
     (4) 深拷贝相比于浅拷贝速度较慢并且花销较大。
  * 实现：需要实现 `Cloneable` 接口，并覆写 `clone()` 方法。调用super.clone()获取父类对象parent，再parent.clone() 拷贝一份

* 浅拷贝 

  * 浅拷贝是按位拷贝对象，它会创建一个新对象，这个对象有着原始对象属性值的一份精确拷贝。如果属性是基本类型，拷贝的就是基本类型的值；如果属性是内存地址（引用类型），拷贝的就是内存地址 ，因此如果其中一个对象改变了这个地址，就会影响到另一个对象。即默认拷贝构造函数只是对对象进行浅拷贝复制(逐个成员依次拷贝)，即只复制对象空间而不复制资源。

  * (1) 对于基本数据类型的成员对象，因为基础数据类型是值传递的，所以是**直接将属性值赋值给新的对象**。基础类型的拷贝，其中一个对象修改该值，不会影响另外一个。
     (2) 对于引用类型，比如数组或者类对象，因为引用类型是引用传递，所以浅拷贝只是把内存地址赋值给了成员变量，它们指向了同一内存空间。改变其中一个，会对另外一个也产生影响。

  * 对象拷贝后没有生成新的对象，二者的对象地址是一样的；而浅拷贝的对象地址是不一样的。

  * 实现：需要实现 `Cloneable` 接口，并覆写 `clone()` 方法。直接返回 super.clone();

    

  















