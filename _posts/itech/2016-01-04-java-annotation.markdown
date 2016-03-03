---
layout: post
title:  "Java注解全面解析"
date:   2016-01-04 21:54:32 +0800
categories:  itech
mtop:   YES
mrefurl: "http://ifeve.com/java-annotation"
---

<p><strong>1.基本语法</strong></p>

<p>注解定义看起来很像接口的定义。事实上，与其他任何接口一样，注解也将会编译成class文件。</p>

<blockquote><p>@Target(ElementType.Method)</p>

<p>@Retention(RetentionPolicy.RUNTIME)</p>

<p>public @interface Test {}</p></blockquote>
<p><span id="more-23558"></span></p>

<p>除了@符号以外，@Test的定义很像一个空的接口。定义注解时，需要一些元注解（meta－annotation），如@Target和@Retention</p>

<p>@Target用来定义注解将应用于什么地方（如一个方法或者一个域）</p>

<p>@Retention用来定义注解在哪一个级别可用，在源代码中（source），类文件中（class）或者运行时（runtime）</p>

<p>在注解中，一般都会包含一些元素以表示某些值。当分析处理注解时，程序可以利用这些值。没有元素的注解称为标记注解（marker annotation）</p>

<p>四种元注解，元注解专职负责注解其他的注解，所以这四种注解的Target值都是ElementType.ANNOTATION_TYPE</p>

<table border="1">
<tbody>
<tr>
<th>注解</th>
<th>说明</th>
</tr>
<tr>
<td>@Target</td>
<td>表示该注解可以用在什么地方，由ElementType枚举定义<br>
CONSTRUCTOR：构造器的声明<br>
FIELD：域声明（包括enum实例）<br>
LOCAL_VARIABLE：局部变量声明<br>
METHOD：方法声明<br>
PACKAGE：包声明<br>
PARAMETER：参数声明<br>
TYPE：类、接口（包括注解类型）或enum声明<br>
ANNOTATION_TYPE：注解声明（应用于另一个注解上）<br>
TYPE_PARAMETER：类型参数声明（1.8新加入）<br>
TYPE_USE：类型使用声明（1.8新加入）<br>
PS：当注解未指定Target值时，此注解可以使用任何元素之上，就是上面的类型</td>
</tr>
<tr>
<td>@Retention</td>
<td>表示需要在什么级别保存该注解信息，由RetentionPolicy枚举定义<br>
SOURCE：注解将被编译器丢弃（该类型的注解信息只会保留在源码里，源码经过编译后，注解信息会被丢弃，不会保留在编译好的class文件里）<br>
CLASS：注解在class文件中可用，但会被VM丢弃（该类型的注解信息会保留在源码里和class文件里，在执行的时候，不会加载到虚拟机（JVM）中）<br>
RUNTIME：VM将在运行期也保留注解信息，因此可以通过反射机制读取注解的信息（源码、class文件和执行的时候都有注解的信息）<br>
PS：当注解未定义Retention值时，默认值是CLASS</td>
</tr>
<tr>
<td>@Documented</td>
<td>表示注解会被包含在javaapi文档中</td>
</tr>
<tr>
<td>@Inherited</td>
<td>允许子类继承父类的注解</td>
</tr>
</tbody>
</table>
<p><strong>2. 注解元素</strong></p>

<p>– 注解元素可用的类型如下：</p>

<p>– 所有基本类型（int,float,boolean,byte,double,char,long,short）</p>

<p>– String</p>

<p>– Class</p>

<p>– enum</p>

<p>– Annotation</p>

<p>– 以上类型的数组</p>

<p>如果使用了其他类型，那编译器就会报错。也不允许使用任何包装类型。注解也可以作为元素的类型，也就是注解可以嵌套。</p>

<p>元素的修饰符，只能用public或default。</p>

<p>– 默认值限制</p>

<p>编译器对元素的默认值有些过分挑剔。首先，元素不能有不确定的值。也就是说，元素必须要么具有默认值，要么在使用注解时提供元素的值。</p>

<p>其次，对于非基本类型的元素，无论是在源代码中声明，还是在注解接口中定义默认值，都不能以null作为值。这就是限制，这就造成处理器很难表现一个元素的存在或缺失状态，因为每个注解的声明中，所有的元素都存在，并且都具有相应的值。为了绕开这个限制，只能定义一些特殊的值，例如空字符串或负数，表示某个元素不存在。</p>

<blockquote><p>@Target(ElementType.Method)</p>

<p>@Retention(RetentionPolicy.RUNTIME)</p>

<p>public @interface MockNull {</p>

<p>public int id() default -1;</p>

<p>public String description() default “”;</p>

<p>}</p></blockquote>
<p><strong>3. 快捷方式</strong></p>

<p>何为快捷方式呢？先来看下springMVC中的Controller注解</p>

<blockquote><p>@Target({ElementType.TYPE})</p>

<p>@Retention(RetentionPolicy.RUNTIME)</p>

<p>@Documented</p>

<p>@Component</p>

<p>public @interface Controller {</p>

<p>String value() default “”;</p>

<p>}</p></blockquote>
<p>可以看见Target应用于类、接口、注解和枚举上，Retention策略为RUNTIME运行时期，有一个String类型的value元素。平常使用的时候基本都是这样的：</p>

<blockquote><p>@Controller(“/your/path”)</p>

<p>public class MockController { }</p></blockquote>
<p>这就是快捷方式，省略了名－值对的这种语法。下面给出详细解释：</p>

<p>注解中定义了名为value的元素，并且在应用该注解的时候，如果该元素是唯一需要赋值的一个元素，那么此时无需使用名－值对的这种语法，而只需在括号内给出value元素所需的值即可。这可以应用于任何合法类型的元素，当然了，这限制了元素名必须为value。</p>

<p><strong>4. JDK1.8注解增强</strong></p>

<p><em>TYPE_PARAMETER和TYPE_USE</em></p>

<p>在JDK1.8中ElementType多了两个枚举成员，TYPE_PARAMETER和TYPE_USE，他们都是用来限定哪个类型可以进行注解。举例来说，如果想要对泛型的类型参数进行注解：</p>

<blockquote><p>public class AnnotationTypeParameter&lt;@TestTypeParam T&gt; {}</p></blockquote>
<p>那么，在定义@TestTypeParam时，必须在@Target设置ElementType.TYPE_PARAMETER，表示这个注解可以用来标注类型参数。例如：</p>

<blockquote><p>@Target(ElementType.TYPE_PARAMETER)</p>

<p>@Retention(RetentionPolicy.RUNTIME)</p>

<p>public @interface&nbsp;TestTypeParam {}</p></blockquote>
<p>ElementType.TYPE_USE用于标注各种类型，因此上面的例子也可以将TYPE_PARAMETER改为TYPE_USE，一个注解被设置为TYPE_USE，只要是类型名称，都可以进行注解。例如有如下注解定义：</p>

<blockquote><p>@Target(ElementType.TYPE_USE)</p>

<p>@Retention(RetentionPolicy.RUNTIME)</p>

<p>public @interface Test {}</p></blockquote>
<p>那么以下的使用注解都是可以的:</p>

<blockquote><p>List&lt;@Test&nbsp;Comparable&gt; list1 = new ArrayList&lt;&gt;();</p>

<p>List&lt;? extends&nbsp;Comparable&gt; list2 = new ArrayList&lt;@Test Comparable&gt;();</p>

<p>@Test String text;</p>

<p>text = (@Test String)new Object();</p>

<p>java.util. @Test Scanner console;</p>

<p>console = new java.util.@Test Scanner(System.in);</p></blockquote>
<p>PS：以上@Test注解都是在类型的右边，要注意区分1.8之前的枚举成员，例如：</p>

<blockquote><p>@Test java.lang.String text;</p></blockquote>
<p>在上面这个例子中，显然是在进行text变量标注，所以还使用当前的@Target会编译错误，应该加上ElementType.LOCAL_VARIABLE。</p>

<p><em>@Repeatable注解</em></p>

<p>@Repeatable注解是JDK1.8新加入的，从名字意思就可以大概猜出他的意思（可重复的）。可以在同一个位置重复相同的注解。举例：</p>

<blockquote><p>@Target(ElementType.TYPE)</p>

<p>@Retention(RetentionPolicy.RUNTIME)</p>

<p>public @interface Filter {</p>

<p>String [] value();</p>

<p>}</p></blockquote>
<p>如下进行注解使用:</p>

<blockquote><p>@Filter({“/admin”,”/main”})</p>

<p>public class MainFilter { }</p></blockquote>
<p>换一种风格:</p>

<blockquote><p>@Filter(“/admin”)</p>

<p>@Filter(“/main”)</p>

<p>public class MainFilter {}</p></blockquote>
<p>在JDK1.8还没出现之前，没有办法到达这种“风格”，使用1.8，可以如下定义@Filter：</p>

<blockquote><p>@Target(ElementType.TYPE)</p>

<p>@Retention(RetentionPolicy.RUNTIME)</p>

<p>@Repeatable(Filters.class)</p>

<p>public @interface Filter {</p>

<p>String &nbsp;value();</p>

<p>}</p>

<p>@Target(ElementType.TYPE)</p>

<p>@Retention(RetentionPolicy.RUNTIME)</p>

<p>public @interface Filters {</p>

<p>Filter [] value();</p>

<p>}</p></blockquote>
<p>实际上这是编译器的优化，使用@Repeatable时告诉编译器，使用@Filters来作为收集重复注解的容器，而每个@Filter存储各自指定的字符串值。</p>

<p>JDK1.8在AnnotatedElement接口新增了getDeclaredAnnotationsByType和getAnnotationsByType，在指定@Repeatable的注解时，会寻找重复注解的容器中。相对于，getDeclaredAnnotation和getAnnotation就不会处理@Repeatable注解。举例如下：</p>

<blockquote><p>@Filter(“/admin”)</p>

<p>@Filter(“/filter”)</p>

<p>public class FilterClass {</p>

<p>public static void main(String[] args) {</p>

<p>Class&lt;FilterClass&gt; filterClassClass = FilterClass.class;</p>

<p>Filter[] annotationsByType = filterClassClass.getAnnotationsByType(Filter.class);</p>

<p>if (annotationsByType != null) {</p>

<p>for (Filter filter : annotationsByType) {</p>

<p>System.out.println(filter.value());</p>

<p>}</p>

<p>}</p>

<p>System.out.println(filterClassClass.getAnnotation(Filter.class));</p>

<p>}</p>

<p>}</p></blockquote>
<p>日志如下:</p>

<blockquote><p>/admin</p>

<p>/filter</p>

<p>null</p></blockquote>




