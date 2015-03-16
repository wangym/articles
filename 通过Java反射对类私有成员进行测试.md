在Java开发阶段，因为追求架构规范和遵循设计原则，所以要用private和protected修饰符去定义类的成员方法、变量、常量，这使得代码具封装性、内聚性等，但在测试阶段也会造成一定的不便。通过Java的反射机制，便能很好地解决该问题。

####ReflectUtil.java
<pre><code>
//......

/**
 * @author yumin
 * @since 2015-03-02 14:52
 */
public class ReflectUtil {

    private ReflectUtil() {
    }

    //部份代码略

    public static Object invokeMethod(Object object, String methodName, Class[] parameterTypes, Object[] args) {
        Object result = null;

        if (null != object) {
            try {
                Method method = getDeclaredMethod(object, methodName, parameterTypes);
                result = method.invoke(object, args);
            } catch (NoSuchMethodException e) {
                //......
            } catch (IllegalAccessException e) {
                //......
            } catch (InvocationTargetException e) {
                //......
            }

        }

        return result;
    }

    public static Method getDeclaredMethod(Object object, String methodName, Class[] parameterTypes) throws NoSuchMethodException {
        Method method = null;

        if (null != object) {
            method = object.getClass().getDeclaredMethod(methodName, parameterTypes);
            method.setAccessible(true);
        }

        return method;
    }
}

</code></pre>

####ReflectUtilTest.java
<pre><code>
//......

/**
 * @author yumin
 * @since 2015-03-02 14:53
 */
public class ReflectUtilTest {

    //部份代码略

    @Test
    public void testInvokeMethod() throws Exception {
        String whatIsYourName = null;
        String howOldAreYou = null;
        String name = "yumin";
        int age = 18;
        Person person = new Person();

        whatIsYourName = (String) ReflectUtil.invokeMethod(person, "whatIsYourName", null, null);
        howOldAreYou = (String) ReflectUtil.invokeMethod(person, "howOldAreYou", new Class[]{int.class}, new Object[]{age});

        Assert.assertEquals(Person.whatIsYourName + name, whatIsYourName);
        Assert.assertEquals(Person.howOldAreYou + age, howOldAreYou);
    }

    public class Person {

        public static final String whatIsYourName = "My name is ";
        public static final String howOldAreYou = "I'm ";
        private String name = "yumin"; // 姓名

        public void setName(String name) {
            this.name = name;
        }

        private String whatIsYourName() {
            return whatIsYourName + name;
        }

        private String howOldAreYou(int age) {
            return howOldAreYou + age;
        }
    }
}

</code></pre>

可以看到通过Java反射机制，实现了对被private和protected所修饰方法和属性的调用、取值、传值等读写操作，未破坏原始代码也能完成对私有成员的单元测试。需要特别注意，建议仅运用在测试场景，切莫图方便在发行的代码逻辑中通过反射调用类私有成员，这样便会破坏原有的类设计，产生不可预料的运行结果及污染体系结构，造成后续难以维护。

更详细代码请参见：https://github.com/wangym/java-common/
