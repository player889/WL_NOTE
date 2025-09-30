# Effective Java

## Chapter 2

### Consider static factory methods instead of constructors

1. **Constructors don't have meaningful names**. **Static factory methods can have meaningful names**, hence explicitly [^f1] conveying[^f2] what they do.
2. **Static factory methods can return the same type that implements the method(s), a subtype, and also primitives**, so they offer a more flexible range of returning types.
   all the logic required for pre-constructing fully initialized instances\*\*, so they can be used for moving this additional logic out of constructors. This prevents constructors from
3. **Static factory methods can be controlled-instanced methods**, with the [Singleton pattern](https://en.wikipedia.org/wiki/Singleton_pattern) being the most glaring example of this feature

[^1]: 明確
[^2]: 傳達

### Consider a builder when faced with many constructor parameters

#### Lombok

```java
@Builder
public class Entity {
  private String body;
}
Entity entity = Entity.builder().body("body").build();
```

```java
@Builder
public class Entity {
  private T body;
}
Entity entity = Entity.builder().body("body").build()
```

![[Pasted image 20230807110459.png|400]]

```ad-note
title:How to exclude property from Lombok builder?

Yes, you can place [@Builder](https://projectlombok.org/features/Builder.html) on a constructor or static (factory) method, containing just the fields you want.
```

---

#### Sample

##### Clean Sample

```java
public class Tool {

	private String first;
	private String last;

	public Tool(String first, String last) {
		super();
		this.first = first;
		this.last = last;
	}

	public static class Builder {
		private String first;
		private String last;

		public Builder() {
		}

		public Builder setFirst(String first) {
			this.first = first;
			return this;
		}

		public Builder setLast(String last) {
			this.last = last;
			return this;
		}

		public Tool build() {
			return new Tool(first, last);
		}
	}

	public String getFirst() {
		return first;
	}

	public String getLast() {
		return last;
	}

	public static void main(String[] args) {
		Tool tool = new Tool.Builder().setFirst("F").setLast("L").build();
		System.out.println(tool.getFirst());
		System.out.println(tool.getLast());
	}

}
```

##### Sample with Required Parameter

```java
public class Computer {

	// required parameters
	private String HDD;
	private String RAM;

	// optional parameters
	private boolean isGraphicsCardEnabled;
	private boolean isBluetoothEnabled;

	public String getHDD() {
		return HDD;
	}

	public String getRAM() {
		return RAM;
	}

	public boolean isGraphicsCardEnabled() {
		return isGraphicsCardEnabled;
	}

	public boolean isBluetoothEnabled() {
		return isBluetoothEnabled;
	}

	private Computer(ComputerBuilder builder) {
		this.HDD = builder.HDD;
		this.RAM = builder.RAM;
		this.isGraphicsCardEnabled = builder.isGraphicsCardEnabled;
		this.isBluetoothEnabled = builder.isBluetoothEnabled;
	}

	// Builder Class
	public static class ComputerBuilder {

		// required parameters
		private String HDD;
		private String RAM;

		// optional parameters
		private boolean isGraphicsCardEnabled;
		private boolean isBluetoothEnabled;

		public ComputerBuilder(String hdd, String ram) {
			this.HDD = hdd;
			this.RAM = ram;
		}

		public ComputerBuilder setGraphicsCardEnabled(boolean isGraphicsCardEnabled) {
			this.isGraphicsCardEnabled = isGraphicsCardEnabled;
			return this;
		}

		public ComputerBuilder setBluetoothEnabled(boolean isBluetoothEnabled) {
			this.isBluetoothEnabled = isBluetoothEnabled;
			return this;
		}

		public Computer build() {
			return new Computer(this);
		}

	}

	public static void main(String[] args) {
		Computer comp = new Computer.ComputerBuilder("500 GB", "2 GB").setBluetoothEnabled(true).setGraphicsCardEnabled(true).build();
		System.out.println(comp.getHDD());
		System.out.println(comp.isBluetoothEnabled);

	}

}
```

#### ??? abstract P.13

```java
public abstract class Pizza{
    public enum Topping{
        HAM,MUSHROOM
    }
    final Set<Topping> toppings;
    abstract static class Builder<T extends Builder<T>>{
        EnumSet<Topping> toppings = EnumSet.noneOf(Topping.class);
        public T addTopping(Topping topping){
            toppings.add(Objects.requireNonNull(topping));
            return self;
        }
        abstract Pizza Builder();
        protect abstract T self();
    }
    Pizza(Builder<?> builder){
        toppings = builder.toppings.clone();
    }
}
```

### Enforce the singleton property with a private constructor or an enum type

- Singleton with public final field

    ```java
    public class Elvis {
        public static final Elvis INSTANCE = new Elvis();
        private Elvis() { ... }
        public void leaveTheBuilding() { ... }
    }
    ```

- Singleton with static factory

    ```java
    public class Elvis implements Serializable {
        private static final Elvis INSTANCE = new Elvis();
        private Elvis() { ... }
        public static Elvis getInstance() { return INSTANCE; }
        public void leaveTheBuilding() { ... }

        // readResolve method to preserve singleton property
        private Object readResolve() {
            // Return the one true Elvis and let the garbage collector
            // take care of the Elvis impersonator.
            return INSTANCE;
        }
    }
    ```

- Enum singleton - the preferred approach

    ```java
    public enum Elvis {
        INSTANCE;
        public void leaveTheBuilding() { ... }
    }
    Elvis e = Elvis.INSTANCE;
    e.leaveTheBuilding();
    ```

> a single-element enum type is often the best way to implement a singleton

### NONINSTANTIABILITY WITH A PRIVATE CONSTRUCTOR

```java
// Noninstantiable utility class
public class UtilityClass {
    // Suppress default constructor for noninstantiability
    private UtilityClass() {
    throw new AssertionError();
    }
	... // Remainder omitted
}
```

PREFER DEPENDENCY INJECTION TO HARDWIRING RESOURCES

- Inappropriate use of static utility - inflexible & untestable!

    ```java
    public class SpellChecker {
        private static final Lexicon dictionary = ...;
        private SpellChecker() {} // Noninstantiable
        public static boolean isValid(String word) { ... }
        public static List<String> suggestions(String typo) { ... }
    }
    ```

    ```java
    public class SpellChecker {
        private final Lexicon dictionary = ...;
        private SpellChecker(...) {}
        public static INSTANCE = new SpellChecker(...);
        public boolean isValid(String word) { ... }
        public List<String> suggestions(String typo) { ... }
    }
    ```

- Dependency injection provides flexibility and testability

    ```java
    public class SpellChecker {
        private final Lexicon dictionary;
        public SpellChecker(Lexicon dictionary) {
        this.dictionary = Objects.requireNonNull(dictionary);
        }
        public boolean isValid(String word) { ... }
        public List<String> suggestions(String typo) { ... }
    }
    ```

In summary, ==do not use a singleton or static utility class to implement a class that depends on one or more underlying resources[^3]== whose behavior affects that of the class, and do not have the class create these resources directly. Instead, pass the resources, or factories to create them, ==into the constructor (or static factory or builder)==. This practice, known as dependency injection, will greatly enhance the flexibility, reusability, and testability of a class.

[^3]: 不要使用 Singleton 和靜態工具類來實現依賴一個或多個底層資源的類，將資源或工廠傳給構造器(或是靜態工廠或是建構器)

### AVOID CREATING UNNECESSARY OBJECTS

### ELIMINATE OBSOLETE OBJECT REFERENCES

### AVOID FINALIZERS AND CLEANERS

### PREFER TRY-WITH-RESOURCES TO TRY-FINALLY

```java
static String firstLineOfFile(String path, String defaultVal) {
    try (BufferedReader br = new BufferedReader(new FileReader(path))) {
	    return br.readLine();
    } catch (IOException e) {
    	return defaultVal;
    }
}
```

## Chapter 3

### OBEY THE GENERAL CONTRACT WHEN OVERRIDING EQUALS

### ALWAYS OVERRIDE HASHCODE WHEN YOU OVERRIDE EQUALS

### ALWAYS OVERRIDE TOSTRING

### OVERRIDE CLONE JUDICIOUSLY

### CONSIDER IMPLEMENTING COMPARABLE

If you are writing a value class with an obvious natural ordering, such as alphabetical order, numerical order, or chronological order, you should implement the Comparable interface:

```java
public interface Comparable<T> {
	int compareTo(T t);
}
```

## Chapter 4

### MINIMIZE THE ACCESSIBILITY OF CLASSES AND MEMBERS

- make each class or member as inaccessible as possible.

- Instance fields of public classes should rarely be public.

- it is wrong for a class to have a public static final array field, or an accessor that returns such a field.

    ```java
    private static final Thing[] PRIVATE_VALUES = { ... };
    public static final List<Thing> VALUES = Collections.unmodifiableList(Arrays.asList(PRIVATE_VALUES)) ;
    ```

    ```java
    private static final Thing[] PRIVATE_VALUES = { ... };
    public static final Thing[] values() {
        return PRIVATE_VALUES.clone();
    }
    ```

### IN PUBLIC CLASSES, USE ACCESSOR METHODS, NOT PUBLIC FIELDS

```java
class Point {
    private double x;
    private double y;
    public Point(double x, double y) {
        this.x = x;
        this.y = y;
    }
    public double getX() { return x; }
    public double getY() { return y; }
    public void setX(double x) { this.x = x; }
    public void setY(double y) { this.y = y; }
}
```

### MINIMIZE MUTABILITY

- Don’t provide methods that modify the object’s state
- Ensure that the class can’t be extended
- Make all fields final
- Make all fields private
- Ensure exclusive access to any mutable components
- Immutable objects are inherently thread-safe; they require no synchronization.
- Constructors should create fully initialized objects with all of their invariants established

```java
// Immutable class with static factories instead of constructors
public class Complex {

    private final double re;
    private final double im;

    private Complex(double re, double im) {
        this.re = re;
        this.im = im;
	}

    public static Complex valueOf(double re, double im) {
        return new Complex(re, im);
    }
	... // Remainder unchanged
}
```

### FAVOR COMPOSITION OVER INHERITANCE

- Unlike method invocation, inheritance violates encapsulation

- Inheritance is an ==is-a== relationship. Composition is a ==as-a==

- 不擴展現有的類，而是在新的類中增加一個私有域，引用現有類的一個實例。

- 如何從繼承和復合之間做出選擇?
    比較抽象的說法是，只有子類和父類確實存在==is-a==關系的時候使用繼承，否則使用復合。
    或者比較實際點的說法是，如果子類只需要實現超類的部分行為，則考慮使用復合。

    ***

          <img src="C:\Users\Jay\Typora\PIC\vue\Snipaste_2020-05-23_16-34-42.png" style="zoom:50%;" align="left"/>

          <img src="C:\Users\Jay\Typora\PIC\vue\Snipaste_2020-05-23_16-36-41.png" style="zoom: 50%;" align="left"/>

### DESIGN AND DOCUMENT FOR INHERITANCE OR ELSE PROHIBIT IT

### PREFER INTERFACES TO ABSTRACT CLASSES

- skeletal implement
    - [Java Skeletal Implementation/Abstract Interfaces（骨架实现/抽象接口）](https://blog.csdn.net/l_o_s/article/details/80901441)

### DESIGN INTERFACES FOR POSTERITY

- default method

### USE INTERFACES ONLY TO DEFINE TYPES

### PREFER CLASS HIERARCHIES TO TAGGED CLASSES

### FAVOR STATIC MEMBER CLASSES OVER NONSTATICA

### LIMIT SOURCE FILES TO A SINGLE TOP-LEVEL CLASS

## Chapter 5

### DON’T USE RAW TYPES

- it is legal to use raw types (generic types without their type parameters), but you should never do it. If you use raw types, you lose all the safety and expressiveness benefits of generics.

### ELIMINATE UNCHECKED WARNINGS

### PREFER LISTS TO ARRAYS

### FAVOR GENERIC TYPES

### FAVOR GENERIC METHODS

### USE BOUNDED WILDCARDS TO INCREASE API FLEXIBILITY

- T 是一個確定的類型，通常用於泛型類和泛型方法的定義，？是一個不確定的類型，通常用於泛型方法的調用代碼和形參，不能用於定義類和泛型方法。
- use Comparable \<? super T> in preference to Comparable\<T>
- use Comparator\<? super T> in preference to Comparator\<T>

```java
public void popAll(Collection<? super E> dst) {
    while (!isEmpty())
        dst.add(pop());
}
```

### COMBINE GENERICS AND VARARGS JUDICIOUSLY

- it is unsafe to store a value in a generic varargs array parameter

### CONSIDER TYPESAFE HETEROGENEOUS CONTAINERS

## chapter 6
