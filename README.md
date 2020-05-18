# COMP 150 Lab 8 - Pillars of OOP 2

In this lab:

* Inheritance via `extends`
* Superclasses and subclasses
* `abstract` classes

## Inheritance again

As we've discussed (briefly) several times in previous labs, all classes extend the `Object` class. This means they **inherit** all methods and data accessible to instances of the `Object` class. Check out the Java 8 `Object` documentation [here](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html).

Scroll down or [click here](https://docs.oracle.com/javase/8/docs/api/java/lang/Object.html#toString--) for `Object.toString()`'s description. This is the `toString` provided to any class that does not explictly override it.

**<a name="q1"></a>[EXERCISE 1](#a1)** What is the purpose of the `toString` method in general? Note the sentence in the description "It is recommended that all subclasses override this method". Why might one want to implement (override) the `toString` method in any implemented classes?

The `Object.toString` documentation refereces the `hashcode()`. The default `hashcode()` method returns a unique integer value for the object. By default, this integer value is the memory address of the object. In other words, by default the `hashcode()` is the memory address, in integer form. 

**<a name="q2"></a>[EXERCISE 2](#a2)** Run the `main` below:

```java
public class MyClass
{
    public static void main(String[] args)
    {
        MyClass myInstance = new MyClass();

        System.out.println(myInstance);
    }
}
```

The output should be something like `MyClass@61bbe9ba`. Note that `MyClass` does not have any constructors, yet a `MyClass` instance is constructed nonetheless. Where might the constructor being used to create this instance be found? When the instance is printed, its `toString` is called automatically. Explain its output in relation to the code provided in the `Object.toString` documenation: 

```getClass().getName() + '@' + Integer.toHexString(hashCode())```

A Java class can extend more any other class. This is done with the `extends` keyword in the class declaration. In fact, the `MyClass` definition below is equivalent to the one above:

```java
public class MyClass extends Object
{
    public static void main(String[] args)
    {
        MyClass myInstance = new MyClass();

        System.out.println(myInstance);
    }
}
```

Most IDEs will complain about the snippet above; IntelliJ complains that "Class MyClass explicitly extends Object", because the `extends Object` is implied unless another extension is provided.

In the `MyClass` definition above, `MyClass` is a **subclass** of `Object` (i.e. it extends object), and `Object` is the **superclass** of `MyClass`. Often, when constructing a subclass, it is necessary (or convenient) to first run the its superclass's constructor.

In the example above, `Object` is the superclass; its constructor can be accessed with the `super` keyword:

```java
public class MyClass extends Object
{
    public static void main(String[] args)
    {
        MyClass myInstance = new MyClass();

        System.out.println(myInstance);
    }

    public MyClass()
    {
        super();
    }
}
```

The `super()` call calls the `Object` constructor, use to "set up" the `Object` instance "wrapped in" the `MyClass` instance being constructed, and does nothing else. This default constructor (which simply calls the superclass constructor) is implied if no constructors are provided, so the example above is equivalent to the one before it.

**<a name="q3"></a>[EXERCISE 3](#a3)** The `MyClass` definition from above is modified below. Try to run it. What is wrong? 

```java
public class MyClass extends Object
{
    int x;

    public MyClass(int x)
    {
        super();
        this.x = x;
    }
    
    public static void main(String[] args)
    {
        MyClass myInstance = new MyClass(1);

        System.out.println(myInstance);
    }
}
```

**<a name="q4"></a>[EXERCISE 4](#a4)** Edit the `MyClass` definition below to: 

1. Add a `toString` method with an `@Override` tag on the line before the declaration. The new `toString` method should return a `String` containing the class name and the value of its `x` field.
2. Add an `equals` method (`boolean`) to compare two `MyClass` instances and check if the two `x` fields are the same. Try to add an `@Override` tag to the `equals` method as well. What happens? What does this tell you about the `Object` class?
3. Modify the `main` to test these new methods.

```java
public class MyClass
{
    int x;

    public MyClass(int x)
    {
        super();
        this.x = x;
    }
    
    public static void main(String[] args)
    {
        MyClass myInstance = new MyClass(1);

        System.out.println(myInstance);
    }
}
```

**<a name="q5"></a>[EXERCISE 5](#a5)** Read the `BasicColor` class below. Instances of the class contain data to encode a color in the form of three doubles, `red`, `green` and `blue`, all in the range *[0,1]*. The class has a single constructor, which takes in three doubles (the red green and blue values) and creates the corresponding color. 

<a name="BasicColor"></a>

```java
public class BasicColor
{
    public enum PRIMARY_COLOR {
        RED,
        GREEN,
        BLUE
    }

    final private double red;
    final private double green;
    final private double blue;

    public BasicColor(double red, double green, double blue)
    {
        this.red = boundPrimaryColorValue(red);
        this.green = boundPrimaryColorValue(green);
        this.blue = boundPrimaryColorValue(blue);
    }

    public double getPrimaryComponent(PRIMARY_COLOR component)
    {
        double result;
        switch(component)
        {
            case RED:
                result = this.red;
                break;
            case GREEN:
                result = this.green;
                break;
            case BLUE:
                result = this.blue;
                break;
            default:
                result = 0;
        }
        return result;
    }

    // Valid values are doubles in [0,1]
    // Negative values are changed to 0, values greater than 1 are changed to 1.
    private static double boundPrimaryColorValue(double value)
    {
        return Math.max(0, Math.min(value, 1));
    }

    public String toString()
    {
        return "Color(" + red + "," + green + "," + blue + ")";
    }
}
```

Create a class called `RandomPlus` which extends the `Random` class (from `java.util`) to create an additional method, `nextBasicColor`, which creates and returns a new `BasicColor` with random red, green and blue values. Create a minimal client to test your new function and the `getPrimaryComponent` accessor in `BasicColor` (which takes `BasicColor.PRIMARY_COLOR.RED`, `BasicColor.PRIMARY_COLOR.GREEN`, or `BasicColor.PRIMARY_COLOR.BLUE` as an argument and returns the corresponding value in the `BasicColor` instance being accessed.

## Abstract Classes

Recall `interface`s from the previous lab, which consist of `final static public` data and `abstract public` methods. Sometimes several implementations of an interface have some shared data/methods, but some different data/methods. As programmers, we want to avoid repeating these methods multiple times (Don't Repeat Yourself), so there is motivation to have "partially implemented" or "partially abstract" classes, in which some of the `abstract` methods are filled and others are not (and maybe some new `abstract` methods are declared).

Classes with the `abstract` modifier (i.e. classes declared with `abstract class`) can contain `public abstract` methods. These methods might be inherited from an interface or another abstract class, or they might be declared within the abstract class's body.

Here we revist an example that is hopefully familiar by now: polygons. Consider the `abstract class Polygon` below (which should remind you of the corresponding interface from the previous lab):

```java
public abstract class Polygon
{
    // a polygon needs at least 3 sides
    final static int DEFAULT_N_SIDES = 3;
    private int nSides;
    
    // default constructor setting number of sides to 3
    Polygon()
    {
        this.nSides = DEFAULT_N_SIDES;
    }

    // secondary constructor setting nSides to input
    Polygon(int nSides)
    {
        // filter out invalid inputs by throwing an exception
        if (nSides < DEFAULT_N_SIDES && nSides != 0) // we allow 0's through for Circles
        {
            throw new IllegalArgumentException("Invalid number of sides \"" + nSides + "\" for shape.Shape.");
        }
        this.nSides = nSides;
    }

    // accessor for number of sides
    public int getNSides()
    {
        return nSides;
    }
    
    // abstract methods
    public abstract double getPerimeter();
    public abstract double getArea();
    public abstract String toString();
}
```

Notice that unlike the `Polygon` interface from the previous lab, this `Polygon` abstract class is able to fill out the `getNSides` method because it is the same for every `Polygon`.

**<a name="q6"></a>[EXERCISE 6](#a6)** Make a client which tries to instantiate a `Polygon` with the statement `Polygon myPolygon = new Polygon();`. What goes wrong?

## Answers to Selected Exercises

**<a name="a1"></a>[SOLUTION 1](#q1)** As the documentation states, the `toString` method exists to return a `String` that "textually represents" the object. Generally, a textual representation of an object contains some representative data to help create and debug clients which use a class. For instance, a 2D cartesian point would likely want to include its `x` and `y` values in its `toString`.

**<a name="a2"></a>[SOLUTION 2](#q2)** The constructor is found in the `Object` class. The `toString` provided in the `Object` class gets the class name `"MyClass"` with `getClass().getName()`, then adds the `'@'` character, and finally adds the object's `hashcode()` (i.e. the hexadecimal representation of the integer value of its memory address).

**<a name="a3"></a>[SOLUTION 3](#q3)** `MyClass` now has a constructor, so there the default `Object` constructor is no longer included. It can be added to fix the problem:

```java
public class MyClass extends Object
{
    int x;
    
    public MyClass()
    {
        super();
    }

    public MyClass(int x)
    {
        super();
        this.x = x;
    }

    public static void main(String[] args)
    {
        MyClass myInstance = new MyClass();

        System.out.println(myInstance);
    }
}
```

Or an `int` can be provided to construct the `MyClass` instance:

```java
public class MyClass extends Object
{
    int x;

    public MyClass(int x)
    {
        super();
        this.x = x;
    }

    public static void main(String[] args)
    {
        MyClass myInstance = new MyClass(1);

        System.out.println(myInstance);
    }
}
```

Or all constructors can be removed, so the `Object` default constructor is used. Of course, this leaves the newly defined `x` field unused:

```java
public class MyClass extends Object
{
    int x;

    public static void main(String[] args)
    {
        MyClass myInstance = new MyClass();

        System.out.println(myInstance);
    }
}
```

**<a name="a4"></a>[SOLUTION 4](#q4)**

The `@Override` does not work on the `equals` function because the super class (`Object`) does not contain an `equals` method. Most IDEs will complain about this (IntelliJ complains that the `equals` method does not override a method from `Object`). While instances can be compared using the `==` operator (to check if they have the same address), they cannot be compared using an `equals` method unless it is defined (without private access) in by the instances' defining class, superclass, superclass's superclass, ...

```java
public class MyClass
{
    int x;

    public MyClass(int x)
    {
        super();
        this.x = x;
    }

    public static void main(String[] args)
    {
        // construct some instances
        MyClass zero = new MyClass(0);
        MyClass one = new MyClass(1);
        MyClass otherZero = new MyClass(0);

        // test their toString methods
        System.out.println("zero is : " + zero);
        System.out.println("one is : " + one);
        System.out.println("otherZero is : " + otherZero);

        // test their equals methods
        System.out.println("zero.equals(one) : " + zero.equals(one));
        System.out.println("zer.equals(otherZero) : " + zero.equals(otherZero));
    }

    @Override
    public String toString()
    {
        return getClass().getName() + ":" + x;
    }

    public boolean equals(MyClass other)
    {
        return (this.x == other.x);
    }
}
```

**<a name="a5"></a>[SOLUTION 5](#q5)**

```java
import java.util.Random;

public class RandomPlus extends Random
{
    public BasicColor nextBasicColor()
    {
        return new BasicColor(nextDouble(), nextDouble(), nextDouble());
    }

    public static void main(String[] args)
    {
        RandomPlus generator = new RandomPlus();

        BasicColor randomColor = generator.nextBasicColor();

        System.out.println("full color : " + randomColor);
        System.out.println("red : " + randomColor.getPrimaryComponent(BasicColor.PRIMARY_COLOR.RED));
        System.out.println("green : " + randomColor.getPrimaryComponent(BasicColor.PRIMARY_COLOR.GREEN));
        System.out.println("blue : " + randomColor.getPrimaryComponent(BasicColor.PRIMARY_COLOR.BLUE));
    }
}
```

**<a name="a6"></a>[SOLUTION 6](#q6)** There is a syntax error: `Polygon` is abstract an cannot be instantiated. Because `Polygon` has abstract elements, it essentially has "missing functionality". Every `Polygon` must have a `getArea` method, for instance, but the core `Polygon` abstract class only declares this method and doesn't define it. In order to instantiate an abstract class, we must extend it to a concrete class (one which is not abstract, which must define all abstract methods).

## Task 1

Create and test a `Triangle` class which extends the `Polygon` class above to fill out all of its methods. This class should be **concrete** (i.e. it should not be abtract).

Then, create an abstract class called `Quadrilateral` which extends the `Polygon` class. This `Quadrilateral` class should simply implement a default constructor `public Quadrilateral()` which passes the appropriate number of sides into the `Polygon` constructor with the `super` keyword. Then, create two concrete classes `Square` and `Rectangle` which extend `Quadrilateral`. Create a minimal client to test `Triangle`, `Square` and `Rectangle`.

## Task 2

Create an extension of the [`BasicColor`](#BasicColor) class called `MutableColor`. You should add a mutator method called `setPrimaryComponent` which allows clients to change the private `red`, `green` and `blue` fields. Use the corresponding accessor `getPrimaryComponent` as an example. Note that you'll need to edit the access modifiers in `BasicColor` in order to make its fields and the `boundPrimaryColorValue` method (which ensures that the value is between 0 and 1).

Create a client to test your new `MutableColor`.

# Bonus Tasks 

## Points again

Recall the `Point` interface (a version of it is here):

```java
public interface Point
{
    final static double EQUALITY_THRESHOLD = 0.01;
    public abstract void setX(double x);
    public abstract void setY(double y);
    public abstract void setCartesianPosition(double x, double y);
    public abstract void setTheta(double theta);
    public abstract void setThetaDegrees(double theta);
    public abstract void setRadius(double radius);
    public abstract void setPolarPosition(double radius, double theta);
    public abstract void setPolarPositionDegrees(double radius, double theta);
    public abstract double getX();
    public abstract double getY();
    public abstract double getTheta();
    public abstract double getThetaDegrees();
    public abstract boolean equals(Point other);
    public abstract void translateCartesian(double x, double y);
    public abstract void translatePolar(double radius, double theta);
    public abstract void translatePolarDegrees(double radius, double theta);
    public abstract void translate(Point vector);
    public abstract void rotate(double angle);
    public abstract void rotateDegrees(double angle);
    public abstract double distanceTo(Point other);
    public abstract String toString();
}
```

Notice that the `interface` includes both `getX` and `getY` methods, and that the *x* and *y* coordinates of two points is enough information to derive the distance between them.

Modify `Point`. Change it into an anstract class instead of an interface, and define `distanceTo(Point other)` by using both points' `getX` and `getY` methods to calculate the distance.

An alternative is to implement the `Point` interface above with a new abstract class which implements just `distanceTo`.

## Graphic User Interfaces (GUIs) with Swing

1. Download SwingDemos.zip(./SwingDemos.zip)
2. Open the ButtonDemo1 package. Read through ButtonDemo1.java, then run it and make sure you understand how it behaves.
3. Open the ButtonDemo2 package. Read through the DialogButton class. Then, read through ButtonClient2.java. Try to predict what it's going to do, and then run it and confirm.
4. Open the GridLayoutDemo package. The DialogButton here is essentially the same as that in the previous demo, but without the excessive commenting. Read through the GridClient.java. Note the order that the 4 DialogButtons are added to the contents, and what order they appear in the grid. Edit GridClient.java so the button grid has 3 columns and 2 rows. Create two new DialogButtons (with whatever contents you desire) and add them to the contents with the other buttons such that they end up in the right-most column.
5. Open the EnumDemo package. Read through the MoodButton class; what happens when a MoodButton is clicked? Read the EnumClient.java, and try to predict how it will behave. Confirm your predictions.
6. Create a new package, and in it a new collection of java files for a new demo. The package's contents should be as follows:
	* ToggleButton.java, a new class (similar to the MoodButton) which switches between two states, ON and OFF. Any newly created ToggleButton should be OFF. The button should have no text, and should be RED when OFF and GREEN when ON.
	* ComplimentButton.java, a new class (similar to the DialogButton). The ComplimentButton class should contain a final static array of Strings called COMPLIMENTS, consisting of at least 5 different Strings, all of which are nice messages to tell your user. The button should be yellow, and when pressed it should choose a random compliment from its COMPLIMENTS array and make a dialog with that compliment.
	* CountButton.java, a new class which is a JButton whose text is initially the number 0. The button should track the number of times it has been clicked, and whenever it is clicked it should update its displayed text to show how many times it has been clicked.
	* QuitButton.java, a new class which is a Button whose displayed text is "QUIT". When pressed, the button should quit the application. To quit, call the contained JFrame's dispose method. Note that this means the QuitButton object will need to store the JFrame containing it as a field variable during its construction.
	* Client.java, which runs an window consisting of a 2x2 grid of buttons (1 of each of the buttons above).
