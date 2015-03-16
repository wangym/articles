在Java开发阶段，因为追求架构规范和遵循设计原则，所以需要用private和protected修饰符去定义类的成员方法、变量、常量，这使得代码具封装性、内聚性等，但同时在测试阶段也会造成一定的不便。而通过Java的反射机制，便能很好地解决该问题。

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

    /**
     * Getting field values
     *
     * @param object    Java object
     * @param fieldName 类私有属性名
     * @return 属性值
     */
    public static Object getFieldValue(Object object, String fieldName) {
        Object fieldValue = null;

        if (null != object) {
            try {
                Field field = getDeclaredField(object, fieldName);
                fieldValue = field.get(object);
            } catch (NoSuchFieldException e) {
                //......
            } catch (IllegalAccessException e) {
                //......
            }
        }

        return fieldValue;
    }

    /**
     * Setting field values
     *
     * @param object     Java object
     * @param fieldName  类私有属性名
     * @param fieldValue 属性值
     * @return true|false
     */
    public static boolean setFieldValue(Object object, String fieldName, Object fieldValue) {
        boolean result = false;

        if (null != object) {
            try {
                Field field = getDeclaredField(object, fieldName);
                field.set(object, fieldValue);
                result = true;
            } catch (NoSuchFieldException e) {
                //......
            } catch (IllegalAccessException e) {
                //......
            }
        }

        return result;
    }

    /**
     * Invoking methods
     *
     * @param object         Java object
     * @param methodName     方法名称
     * @param parameterTypes 入参类型
     * @param args           入参对象
     * @return 执行结果对象
     */
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

    /**
     * Getting field object
     *
     * @param object    Java object
     * @param fieldName 类私有属性名
     * @return Field object
     * @throws NoSuchFieldException
     */
    public static Field getDeclaredField(Object object, String fieldName) throws NoSuchFieldException {
        Field field = null;

        if (null != object) {
            field = object.getClass().getDeclaredField(fieldName);
            field.setAccessible(true);
        }

        return field;
    }

    /**
     * Getting method object
     *
     * @param object         Java object
     * @param methodName     方法名称
     * @param parameterTypes 入参类型
     * @return Method object
     * @throws NoSuchMethodException
     */
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

    @Test
    public void testGetFieldValueForPrivate() throws Exception {
        String fieldValue = "yumin";
        Person person = new Person();
        person.setName(fieldValue);

        String result = (String) ReflectUtil.getFieldValue(person, "name");

        Assert.assertEquals(fieldValue, result);
    }

    @Test
    public void testGetFieldValueForProtected() throws Exception {
        String result = (String) ReflectUtil.getFieldValue(new Person(), "country");

        Assert.assertEquals("china", result);
    }

    @Test
    public void testSetFieldValue() throws Exception {
        String result = null;
        String fieldName = "name";
        String fieldValue = "yumin";
        Person person = new Person();

        if (ReflectUtil.setFieldValue(person, fieldName, fieldValue)) {
            result = (String) ReflectUtil.getFieldValue(person, fieldName);
        }

        Assert.assertEquals(fieldValue, result);
    }

    @Test
    public void testGetAndSetFieldValue() throws Exception {
        SexEnum result = null;
        String fieldName = "sex";
        SexEnum fieldValue = SexEnum.MALE;
        Person person = new Person();

        if (ReflectUtil.setFieldValue(person, fieldName, fieldValue)) {
            result = (SexEnum) ReflectUtil.getFieldValue(person, fieldName);
        }

        Assert.assertEquals(fieldValue, result);
    }

    @Test
    public void testInvokeMethod() throws Exception {
        String whatIsYourName = null;
        String howOldAreYou = null;
        String whereAreYouFrom = null;
        String name = "yumin";
        int age = 18;
        String country = null;
        Person person = new Person();

        if (ReflectUtil.setFieldValue(person, "name", name)) {
            whatIsYourName = (String) ReflectUtil.invokeMethod(person, "whatIsYourName", null, null);
            howOldAreYou = (String) ReflectUtil.invokeMethod(person, "howOldAreYou", new Class[]{int.class}, new Object[]{age});
            country = (String) ReflectUtil.getFieldValue(person, "country");
            whereAreYouFrom = (String) ReflectUtil.invokeMethod(person, "whereAreYouFrom", null, null);
        }

        Assert.assertEquals(Person.whatIsYourName + name, whatIsYourName);
        Assert.assertEquals(Person.howOldAreYou + age, howOldAreYou);
        Assert.assertEquals(Person.whereAreYouFrom + country, whereAreYouFrom);
    }

    /**
     * 测试用类
     */
    enum SexEnum {
        MALE, FEMALE
    }

    public class Person {

        public static final String whatIsYourName = "My name is ";
        public static final String howOldAreYou = "I'm ";
        public static final String whereAreYouFrom = "I'm from ";
        private String name; // 姓名
        private SexEnum sex;
        protected String country = "china"; // 国家

        public void setName(String name) {
            this.name = name;
        }

        private String whatIsYourName() {
            return whatIsYourName + name;
        }

        private String howOldAreYou(int age) {
            return howOldAreYou + age;
        }

        private String whereAreYouFrom() {
            return whereAreYouFrom + country;
        }
    }
}

</code></pre>

可以看到通过Java反射机制，实现了对被private和protected所修饰方法和属性的调用、取值、传值等读写操作，既没破坏原始代码也能完成私有成员的单元测试。需要特别注意的是，建议仅运用在测试场景下，切莫图方便在发行的代码逻辑中通过反射调用类的私有成员，这样便会破坏原有类的设计，产生不可预料的运行结果及污染了体系结构，造成后续难以维护。
