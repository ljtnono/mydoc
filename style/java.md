> 此规范参照阿里巴巴码出高效Java开发手册，跟部分资料有所出入

## 命名规范

### (一)命名风格
1. 无论什么命名都不能以下划线或者美元符号开始，也不能以下划线和美元符号结束

2. 严禁使用拼音和英文混用方式命名 <br>
> 例如： `DaZhePromotion [打折]` <br> `getPingfenByName() [评分]` <br> `int 某变量 = 3`

3. 类名使用大驼峰，变量名，函数名，字段名使用小驼峰，当类为`DO` / `BO` / `VO` / `DTO` / `AO` 时可以以DO结尾

4. 常量命名全部大写，单词之间以下划线隔开<br>
> 例如： MAX_SOCK_COUNT <br> DEFAULT_IMAGE_URL

5. 抽象类命名使用`Abstract`或者`Base`开头；异常类使用Exception结尾；测试类以测试的类的名称开始，以Test结尾

6. 包名使用小写，以`.`作为分隔，统一使用单数形式

7. 尽量在命名的时候使用全英文单词，不要缩写

8. 如果类使用了设计模式，在命名的时候尽量体现出来<br>
> 例如： `public class orderFactory` <br> `public class LoginProxy` <br> `public class ResourceObserver`

9. 尽量为类、接口、字段、方法都写上javadoc

10. 枚举类命名以Enum结尾

11. 各层命名规范
    * Service/DAO 方法命名规范
        * 获取单个对象的方法用 `get` 作为前缀。
        * 获取多个对象的方法用 `list` 做前缀。
        * 获取统计值的方法用 `count` 做前缀。
        * 插入的方法用 `save/insert` 做前缀。
        * 删除的方法用 `remove/delete` 做前缀。
        * 修改的方法用 `update` 做前缀。
    * 领域模型命名归约
        * 数据对象：`xxxDO`，xxx 即为数据表名。
        * 数据传输对象：`xxxDTO`，xxx 为业务领域相关的名称。
        * 展示对象：`xxxVO`，xxx 一般为网页名称。
        * `POJO` 是 `DO/DTO/BO/VO` 的统称，禁止命名成 `xxxPOJO`。

### (二)常量定义
1. 不允许魔法值（未经定义的常量）出现在代码中

2. Long或者long类型字面量要在后面加上L

3. 使用多个常量类（不要使用一个常量类）来维护所有的常量<br>
> 例如： `CacheConsts` `ConfigConsts`

4. 尽量使用枚举代替常量类

### (三)代码格式
1. 如果是大括号内为空，则简洁地写成{}即可，大括号中间无需换行和空格；如果是非空代码块则：
   * 左大括号前不换行
   * 左大括号后换行
   * 右大括号前换行
   * 右大括号后还有 else 等代码则不换行；表示终止的右大括号后必须换行

2. `if`/`for`/`while`/`switch`/`do`等保留字之间都必须加空格

3. 采用4个空格缩进，禁止使用tab字符<br>
说明：如果使用 tab 缩进，必须设置 1 个 tab 为 4 个空格。IDEA 设置 tab 为 4 个空格时，请勿勾选 Use
tab character；而在 eclipse 中，必须勾选 insert spaces for tabs。<br>

```java
public static void main(String[] args) {
    // 缩进 4 个空格
    String say = "hello";
    // 运算符的左右必须有一个空格
    int flag = 0;
    // 关键词 if 与括号之间必须有一个空格，括号内的 f 与左括号，0 与右括号不需要空格
    if (flag == 0) {
        System.out.println(say);
    }

    // 左大括号前加空格且不换行；左大括号后换行
    if (flag == 1) {
        System.out.println("world");
        // 右大括号前换行，右大括号后有 else，不用换行
    } else {
        System.out.println("ok");
        // 在右大括号后直接结束，则必须换行
    }
} 
```

4. 单行字符数限制不超过120个，超出需要换行，换行时遵循如下原则：<br>
    * 第二行相对第一行缩进 4 个空格，从第三行开始，不再继续缩进，参考示例
    * 运算符与下文一起换行
    * 方法调用的点符号与下文一起换行
    * 方法调用中的多个参数需要换行时，在逗号后进行
    * 在括号前不要换行，见反例
  
```java
    // 正例：
    StringBuilder sb = new StringBuilder();
    // 超过 120 个字符的情况下，换行缩进 4 个空格，点号和方法名称一起换行
    sb.append("Jack").append("Ma")...
    .append("alibaba")...
    .append("alibaba")...
    .append("alibaba");


    // 反例：
    StringBuilder sb = new StringBuilder();
    // 超过 120 个字符的情况下，不要在括号前换行
    sb.append("Jack").append("Ma")...append
    ("alibaba");
    // 参数很多的方法调用可能超过 120 个字符，不要在逗号前换行
    method(args1, args2, args3, ...
    , argsX); 
```

5. 单个方法的总行数不要超过80行

### (四)OOP规约
1. 静态方法通过类名调用，不要使用对象调用，会增加编译器解析成本

2. 所有复写代码必须加上@Override注解

3. 尽量不用可变参数编程

4. 过时接口使用@Deprecated注解并且清晰地说明新接口或者新服务是什么

5. 不能使用过时的类或方法

6. 浮点数之间的等值判断，基本数据类型不能用==来比较，包装数据类型不能用
equals 来判断<br>
说明：浮点数采用“尾数+阶码”的编码方式，类似于科学计数法的“有效数字+指数”的表示方式。
二进制无法精确表示大部分的十进制小数，具体原理参考《码出高效》。

```java

    // 反例：
    float a = 1.0f - 0.9f;
    float b = 0.9f - 0.8f;

    if (a == b) {
    // 预期进入此代码快，执行其它业务逻辑
    // 但事实上 a==b 的结果为 false
    }

    Float x = Float.valueOf(a);
    Float y = Float.valueOf(b);
    if (x.equals(y)) {
    // 预期进入此代码快，执行其它业务逻辑
    // 但事实上 equals 的结果为 false
    }

    // 正例：
    // (1) 指定一个误差范围，两个浮点数的差值在此范围之内，则认为是相等的。
    float a = 1.0f - 0.9f;
    float b = 0.9f - 0.8f;
    float diff = 1e-6f;

    if (Math.abs(a - b) < diff) {
        System.out.println("true");
    }
    // (2) 使用 BigDecimal 来定义值，再进行浮点数的运算操作。
    BigDecimal a = new BigDecimal("1.0");
    BigDecimal b = new BigDecimal("0.9");
    BigDecimal c = new BigDecimal("0.8");

    BigDecimal x = a.subtract(b);
    BigDecimal y = b.subtract(c);

    if (x.equals(y)) {
        System.out.println("true");
    }

```

7. 定义数据对象DO类时，属性类型要与数据库字段类型匹配
   
8. 为了防止精度损失，禁止使用构造方法`BigDecimal(double)`的方式把double值转化为BigDecimal对象<br>
说明：`BigDecimal(double)`存在精度损失风险，在精确计算或值比较的场景中可能会导致业务逻辑异常。
如：`BigDecimal g = new BigDecimal(0.1f)`; 实际的存储值为：`0.10000000149`<br>

```java
    /**
    * 正例：优先推荐入参为 `String` 的构造方法，或使用 `BigDecimal` 的 `valueOf` 方法，
    * 此方法内部其实执行了Double 的 `toString`，而 `Double` 的 `toString` 
    * 按 `double` 的实际能表达的精度对尾数进行了截断。
    * 
    */
    BigDecimal recommend1 = new BigDecimal("0.1");
    BigDecimal recommend2 = BigDecimal.valueOf(0.1);
```

<!-- 9. 关于基本类型与包装数据类型的使用标准如下： -->
    


### 持续更新中...