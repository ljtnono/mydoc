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

<!-- ### (三)代码格式
1. 


##  -->