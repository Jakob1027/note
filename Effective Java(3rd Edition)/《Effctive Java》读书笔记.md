# Chapater2 Creating and Destroying Objects
## Item 1: 考虑用静态工厂方法代替构造器
- 静态工厂方法的一个优点是，与构造函数不同，它们有名称。
- 静态工厂方法的第二个优点是，与构造函数不同，它们不需要每次调用时都创建一个新对象。
- 静态工厂方法的第三个优点是，与构造函数不同，它们可以返回返回类型的任何子类型的对象。
- 静态工厂的第四个优点是，返回对象的类可以随着输入参数的不同而变化。
- 静态工厂的第五个优点是，在编写包含方法的类时，返回对象的类不需要存在。
- 仅提供静态工厂方法的主要限制是，没有公共或受保护构造函数的类不能被继承。
- 静态工厂方法的第二个缺点是程序员很难找到它们。
## Item 2: 遇到多个构造器时考虑使用builder模式
### The Builder pattern simulates named optional parameters:
```java
// Builder Pattern
public class NutritionFacts {

    private final int servingSize;
    private final int servings;
    private final int calories;
    private final int fat;
    private final int sodium;
    private final int carbohydrate;

    public static class Builder {
        // Required parameters
        private final int servingSize;
        private final int servings;

        // Optional parameters
        private int calories = 0;
        private int fat = 0;
        private int sodium = 0;
        private int carbohydrate = 0;

        public Builder(int servingSize, int servings) {
            this.servingSize = servingSize;
            this.servings = servings;
        }

        public Builder calories(int val) {
            this.calories = val;
            return this;
        }

        public Builder fat(int val) {
            this.fat = val;
            return this;
        }

        public Builder sodium(int val) {
            this.sodium = val;
            return this;
        }

        public Builder carbohydrate(int val) {
            this.carbohydrate = val;
            return this;
        }

        public NutritionFacts build() {
            return new NutritionFacts(this);
        }
    }

    private NutritionFacts(Builder builder) {
        servingSize = builder.servingSize;
        servings = builder.servings;
        calories = builder.calories;
        fat = builder.fat;
        sodium = builder.sodium;
        carbohydrate = builder.carbohydrate;
    }

    public int getServingSize() {
        return servingSize;
    }

    public int getServings() {
        return servings;
    }

    public int getCalories() {
        return calories;
    }

    public int getFat() {
        return fat;
    }

    public int getSodium() {
        return sodium;
    }

    public int getCarbohydrate() {
        return carbohydrate;
    }
}
```
### The Builder pattern is well suited to class hierarchies.
```java
// Builder pattern for class hierarchies
public abstract class Pizza {
    public enum Topping {HAM, MUSHROOM, ONION, PEPPER, SAUSAGE}
    final Set<Topping> toppings;

    abstract static class Builder<T extends Builder<T>> {
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addToppings(Topping topping) {
            toppings.add(Objects.requireNonNull(topping));
            return self();
        }

        abstract Pizza build();

        // Subclasses must override this method to return "this".
        protected abstract T self();
    }

    Pizza(Builder<?> builder) {
        toppings = builder.toppings;
    }
}

public class NyPizza extends Pizza {
    public enum Size {SMALL,MEDIUM,LARGE}
    private final Size size;

    public static class Builder extends Pizza.Builder<Builder> {
        private final Size size;

        public Builder(Size size) {
            this.size = Objects.requireNonNull(size);
        }

        @Override
        public NyPizza build() {
            return new NyPizza(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }

    private NyPizza(Builder builder) {
        super(builder);
        size = builder.size;
    }
}

public class Calzone extends Pizza {

    private final Boolean sauceInside;

    public static class Builder extends Pizza.Builder<Builder> {
        private Boolean sauceInside = false;

        public Builder sauceInside() {
            this.sauceInside = true;
            return self();
        }

        @Override
        public Pizza build() {
            return new Calzone(this);
        }

        @Override
        protected Builder self() {
            return this;
        }
    }


    private Calzone(Builder builder) {
        super(builder);
        this.sauceInside = builder.sauceInside;
    }
}
```
### In summary, the Builder pattern is a good choice when designing classes whose constructors or static factories would have more than a handful of parameters,especially if many of the parameters are optional or of identical type.

## Item 3: Enforce the singleton property with a private constructor or an enum type
### A single-element enum type is often the best way to implement a singleton.
## Item 4: Enforce noninstantiability with a private constructor
通常用于utility classes
## Item 5: Prefer dependency injection to hardwiring resources
```java
// Dependency injection provides flexibility and testability
public class SpellChecker {
    private final Object dictionary;

    public SpellChecker(Object dictionary) {
        this.dictionary = dictionary;
    }

    public Boolean isValid(String word) {...}

    public List<String> suggestions(String typo) {...}
}
```
## Item 6: Avoid creating unnecessary objects
```java
// Performance can be greatly improved!
static boolean isRomanNumeral(String s) {
    return s.matches("^(?=.)M*(C[MD]|D?C{0,3})"
        + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
}
// Reusing expensive object for improved performance
public class RomanNumerals {
    private static final Pattern ROMAN = Pattern.compile("^(?=.)M*(C[MD]|D?C{0,3})"
        + "(X[CL]|L?X{0,3})(I[XV]|V?I{0,3})$");
    static boolean isRomanNumeral(String s) {
        return ROMAN.matcher(s).matches();
    }
}
```
quiz:
```java
// Hideously slow! Can you spot the object creation?
    private static long sum() {
        Long sum = 0L;
        for (long i = 0; i <= Integer.MAX_VALUE; i++)
            sum += i;
        return sum;
    }
```
## Item 7: Eliminate obsolete object references
## Item 8: Avoid finalizers and cleaners
## Item 9: Prefer try-with-resources to try-finally
```java
// try-with-resources - the the best way to close resources!
static String firstLineOfFile(String path) throws IOException {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
        return br.readLine();
    }
}

// try-with-resources on multiple resources - short and sweet
static void copy(String src, String dst) throws IOException {
    try (InputStream in = new FileInputStream(src);
        OutputStream out = new FileOutputStream(dst)) {
        byte[] buf = new byte[BUFFER_SIZE];
        int n;
        while ((n = in.read(buf)) >= 0)
            out.write(buf, 0, n);
    }
}

```
# Chapater3 Methods Common to All Objects
## Item 10: Obey the general contract when overriding equals
The equals method implements an equivalence relation. It has these properties:
- Reflexive 自反
- Symmetric 对称
- Transitive 传递
- Consistent 一致
- 对任意非空对象x,`x.equals(null)`必须返回`false`
## Item 11: Always override hashCode when you override equals
## Item 12: Always override toString
## Item 13: Override clone judiciously
- In practice, a class implementing Cloneable is expected to provide a properly functioning public clone method.
- Immutable classes should never provide a clone method 
- The Cloneable architecture is incompatible with normal use of final fields referring to mutable objects
- A better approach to object copying is to provide a copy constructor or copy factory
## Item 14: Consider implementing Comparable
```java
// Multiple-field Comparable with primitive fields
public int compareTo(PhoneNumber pn) {
    int result = Short.compare(areaCode, pn.areaCode);
    if (result == 0) {
        result = Short.compare(prefix, pn.prefix);
    if (result == 0)
        result = Short.compare(lineNum, pn.lineNum);
    }
    return result;
}
```
```java
// Comparable with comparator construction methods
    private static final Comparator<PhoneNumber> COMPARATOR =
            comparingInt((PhoneNumber pn) -> pn.areaCode)
                    .thenComparingInt(pn -> pn.prefix)
                    .thenComparingInt(pn -> pn.lineNum);
    private final short areaCode;
    private final short prefix;
    private final short lineNum;

    public PhoneNumber(short areaCode, short prefix, short lineNum) {
        this.areaCode = areaCode;
        this.prefix = prefix;
        this.lineNum = lineNum;
    }

    public int compareTo(PhoneNumber pn) {
        return COMPARATOR.compare(this,pn);
    }
```
# Chapater4 Classes and Interfaces
## Item 15: Minimize the accessibility of classes and members
It is wrong for a class to have a public static final array field, or an accessor that returns such a field.
## Item 16: In public classes, use accessor methods, not public fields
- If a class is accessible outside its package, provide accessor methods to preserve the flexibility to change the class’s internal representation. 
- If a class is package-private or is a private nested class, there is nothing inherently wrong with exposing its data fields
- In summary, public classes should never expose mutable fields. It is less harmful, though still questionable, for public classes to expose immutable fields.It is, however, sometimes desirable for package-private or private nested classes to expose fields, whether mutable or immutable.
## Item 17: Minimize mutability
To make a class immutable, follow these five rules:
1. Don’t provide methods that modify the object’s state (known as mutators).
2. Ensure that the class can’t be extended.
3. Make all fields final.
4. Make all fields private.
5. Ensure exclusive access to any mutable components.
```java
// Immutable complex number class
public class Complex {

    public static final Complex ZERO = new Complex(0, 0);
    public static final Complex ONE = new Complex(1, 0);
    public static final Complex I = new Complex(0, 1);

    private final double re;
    private final double im;

    private Complex(double re, double im) {
        this.re = re;
        this.im = im;
    }

    public static Complex valueOf(double re, double im) {
        return new Complex(re, im);
    }

    public double realPart() {
        return re;
    }

    public double imaginaryPart() {
        return im;
    }

    public Complex plus(Complex c) {
        return new Complex(re + c.re, im + c.im);
    }

    public Complex minus(Complex c) {
        return new Complex(re - c.re, im - c.im);
    }

    public Complex times(Complex c) {
        return new Complex(re * c.re - im * c.im,
                re * c.im + im * c.re);
    }

    public Complex dividedBy(Complex c) {
        double tmp = c.re * c.re + c.im * c.im;
        return new Complex((re * c.re + im * c.im) / tmp,
                (im * c.re - re * c.im) / tmp);
    }

    @Override
    public boolean equals(Object obj) {
        if (obj == this) return true;
        if (!(obj instanceof Complex)) {
            return false;
        }
        Complex c = (Complex) obj;
        return Double.compare(re, c.re) == 0 && Double.compare(im, c.im) == 0;
    }

    @Override
    public int hashCode() {
        return 31 * Double.hashCode(re) + Double.hashCode(im);
    }

    @Override
    public String toString() {
        return "(" + re + " + " + im + "i)";
    }
}
```
To summarize, resist the urge to write a setter for every getter. Classes should be immutable unless there’s a very good reason to make them mutable.Immutable classes provide many advantages, and their only disadvantage is the potential for performance problems under certain circumstances. 
## Item 18: Favor composition over inheritance
**继承**（`addAll`将返回错误结果，因为`addAll`方法内部调用了`add`）：
```java
// Broken - Inappropriate use of inheritance!
public class InstrumentedHashSet<E> extends HashSet<E> {
    // The number of attempted element insertions
    private int addCount = 0;
    public InstrumentedHashSet() {
    }
    public InstrumentedHashSet(int initCap, float loadFactor) {
        super(initCap, loadFactor);
    }
    @Override public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    public int getAddCount() {
        return addCount;
    }
}
```
**组合**：
```java
// Wrapper class - uses compsition in place of inheritance
public class InstrumentedSet<E> extends ForwardingSet<E> {
    private int addCount = 0;
    public InstrumentedSet(Set<E> s) {
        super(s);
    }
    @Override public boolean add(E e) {
        addCount++;
        return super.add(e);
    }
    @Override public boolean addAll(Collection<? extends E> c) {
        addCount += c.size();
        return super.addAll(c);
    }
    public int getAddCount() {
        return addCount;
    }
}
// Reusable forwarding class
public class ForwardingSet<E> implements Set<E> {
    private final Set<E> s;
    public ForwardingSet(Set<E> s) { this.s = s; }
    public void clear() { s.clear(); }
    public boolean contains(Object o) { return s.contains(o); }
    public boolean isEmpty() { return s.isEmpty(); }
    public int size() { return s.size(); }
    public Iterator<E> iterator() { return s.iterator(); }
    public boolean add(E e) { return s.add(e); }
    public boolean remove(Object o) { return s.remove(o); }
    public boolean containsAll(Collection<?> c)
    { return s.containsAll(c); }
    public boolean addAll(Collection<? extends E> c)
    { return s.addAll(c); }
    public boolean removeAll(Collection<?> c)
    { return s.removeAll(c); }
    public boolean retrainAll(Collection<?> c)
    { return s.retainAll(c); }
    public Object[] toArray() { return s.toArray(); }
    public <T> T[] toArray(T[] a) { return s.toArray(a); }
    @Override public boolean equals(Object o)
    { return s.equals(o); }
    @Override public int hashCode() { return s.hashCode(); }
    @Override public String toString() { return s.toString(); }
}
```
有时候，复合和转发的结果也被错误地成为“委托（delegation）”。从技术的角度而言，这不是委托，除非包装对象把自身传递给被包装的对象
## Item 19: Design and document for inheritance or else prohibit it
- The only way to test a class designed for inheritance is to write subclasses.
- Constructors must not invoke overridable methods, directly or indirectly.
- Designing a class for inheritance requires great effort and places substantial limitations on the class. 
- The best solution to this problem is to prohibit subclassing in classes that are not designed and documented to be safely subclassed. 
## Item 20: Prefer interfaces to abstract classes
- Existing classes can easily be retrofitted to implement a new interface. 
- Interfaces are ideal for defining mixins.
- Interfaces allow for the construction of nonhierarchical type frameworks.
- Interfaces enable safe, powerful functionality enhancements via the wrapper class idiom (Item 18).
- 骨架实现类:
```java
// Skeletal Implementation
public abstract class AbstractMapEntry<K, V> implements Map.Entry<K, V> {
    // Primitive operations
    public abstract K getKey();
    public abstract V getValue();
    // Entries in modifiable maps must override this method
    public V setValue(V value) {
        throw new UnsupportedOperationException();
    }
    // Implements the general contract of Map.Entry.equals
    @Override public boolean equals(Object o) {
        if (o == this)
            return true;
        if (! (o instanceof Map.Entry))
            return false;
        Map.Entry<?, ?> arg = (Map.Entry) o;
        return equals(getKey(), arg.getKey()) &&
                equals(getValue(), arg.getValue());
    }
    private static boolean equals(Object o1, Object o2) {
        return o1 == null ? o2 == null : o1.equals(o2);
    }
    // Implements the general contract of Map.Entry.hashCode
    @Override public int hashCode() {
        return hashCode(getKey()) ^ hashCode(getValue());
    }
    private static int hashCode(Object obj) {
        return obj == null ? 0 : obj.hashCode();
    }
}
```
>骨架实现的美妙之处在于，它们为抽象类提供了实现上的帮助，但又不强加“抽象类被用作类型定义时”所特有的严格限制。对于接口的大多数实现来讲，扩展骨架实现类是个很显然的选择，但并不是必需的。如果预置的类无法扩展骨架实现类，这个类始终可以手工实现这个接口。此外，骨架实现类仍然能够有助于接口的实现。实现了这个接口的类可以把对于接口方法的调用，转发到一个内部私有类的实力上，这个内部私有类扩展了骨架实现类。这种方法被称作模拟多重继承（simulated multiple inheritance），它与第16条中讨论的包装类模式密切相关。这项技术具有多重继承的绝大多数优点，同时又避免了相应的缺陷。
>
## Item 21: Design interfaces for posterity
## Item 22: Use interfaces only to define types
## Item 23: Prefer class hierarchies to tagged classes
>tagged class:
```java
// Tagged class - vastly inferior to a class hierarchy
class Figure {
    enum Shape { RECTANGLE, CIRCLE};
    // Tag field - the shape of this figure
    final Shape shape;
    // These fields are used only if shape is RECTANGLE
    double length;
    double width;
    // This field is used only if shape is CIRCLE
    double radius;
    // Constructor for circle
    Figure(double radius) {
        shape = Shape.CIRCLE;
        this.radius = radius;
    }
    // Constructor for rectangle
    Figure(double length, double width) {
        shape = Shape.RECTANGLE;
        this.length = length;
        this.width = width;
    }
    double area() {
        switch (shape) {
            case RECTANGLE:
                return length * width;
            case CIRCLE:
                return Math.PI * (radius * radius);
            default:
                throw new AssertionError();
        }
    }
}
```
>class hierarchies
```java
// Class hierarchy replacement for a tagged class
abstract class Figure {
    abstract double area();
}
class Circle extends Figure {
    final double radius;
    Circle(double radius) { this.radius = radius; }
    double area() { return Math.PI * (radius * radius); }
}
class Rectangle extends Figure {
    final double length;
    final double width;
    Rectangle(double length, double width) {
        this.length = length;
        this.width = width;
    }
    double area() { return length * width; }
}
class Square extends Rectangle {
    Square(double side) {
        super(side,side);
    }
}
```

## Item 24: Favor static member classes over nonstatic
>如果声明成员类不要求访问外围实例，就要始终把static修饰符放在它的声明中，使它成为静态成员类，而不是非静态成员类。如果省略了static修饰符，则每个实例都将包含一个额外的指向外围对象的引用。保存这份引用要消耗时间和空间，并且会导致外围实例在符合垃圾回收（见第6条）时却仍然得以保留。如果在没有外围实例的情况下，也需要分配实例，就不能使用非静态成员类，因为非静态成员类的实例必须要有一个外围实例。
>

>简而言之，共有四种不同的嵌套类，每一种都有自己的用途。如果一个嵌套类需要在单个方
法之外仍然是可见的，或者太长了，不适合于放在方法内部，就应该使用成员类。如果成员
类的每个实例都需要一个指向其外围实例的引用，就要把成员类做成非静态的；否则，就做
成静态的。假设这个嵌套类属于一个方法的内部，如果你只需要在一个地方创建实例，并且
已经有了一个预置的类型可以说明这个类的特征，就要把它做成匿名类；否则，就做成局部
类。
>






