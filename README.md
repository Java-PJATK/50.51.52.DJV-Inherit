# DJV-Inherit
50 51 52 DJV-Inherit/Point.java DJV-Inherit/Pixel.java DJV-Inherit/Main.java 88 89 89

Cont. from [9.2 Class StringBuilder 49.BHI-StrBuilder](https://github.com/Java-PJATK/49.BHI-StrBuilder/)

# Section 10. Introduction to inheritance

Inheritance applies to situations when one type describes objects which behave as objects of another (‘base’) type but in a different way, or they possess some additional properties. 

For example dog is an animal, so a class describing dogs may inherit from (extend) class describing animals. 

This is called ‘is-a’ relation: dog _is-a_ animal (but not necessarily the other way around — not all animals are dogs). 

By using inheritance, we can reuse the code written in the base class (`Animal`) and not repeat it in class `Dog`. What is more important, however, everywhere where an animal is expected (any animal: maybe dog, maybe cat), we can use a dog, because dog is-a animal.


In particular, you can create an object of the derived class (`Dog`) and assign the address (reference) obtained from `new` to a reference variable declared as having the type of the base class (`Animal`). 

In such a situation, we say that the _static_ (declared) type of the reference is `Animal`, but the dynamic (‘real’) type is `Dog`. 

Through this reference, the compiler will allow you to use all methods from `Animal`, but not those which exist in `Dog` but are not inherited from `Animal`. 

However, if, in class `Dog`, you have **overridden** (redefined) a method inherited from `Animal;`, then at runtime this redefined (coming from `Dog`) method will be used, even though you refer to the object by a reference whose static type is `Animal` — this is called “late binding” and is the essence of **polymorphism**.

Methods with such behavior are sometimes called **virtual** — in Java, therefore, all methods are virtual. The only exception is a method declared as `final`: any attempt to override such `final` method will fail.

Any class can directly extend (inherit from) _only one_ base class (also called its **superclass**). If, defining a class, we do not specify its superclass, it is assumed that it inherits from a special class `Object`; it follows, that _every_ class inherits directly or indirectly, from `Object`. Class `Object` defines a few methods which are therefore inherited (and, consequently, _exist_) in any class.

One important example of such a method is `toString` (shown in the example below).

Its purpose is to return a `String` describing somehow the object it was invoked on.

Usually, we redefine `toString` in our classes: otherwise the inherited version will be used, what will always succeed but may return a rather useless result.

When we redefine (override) an inherited method, it is recommended (although not required) to annotate this redefinition with `Override`, as in the example below; this makes our intention explicit to the compiler which can therefore verify if indeed the declaration of the method is correct. Also, note the use of `instanceof` operator:

```java
a instanceof AClass
```

answers (at runtime) the question _is the object referred to by_ `a` _of type_ `AClass` `or any of its subclasses`. We can also find or compare types of objects refereed to by a reference using `getClass` method (which is defined in `Object`, so all classes inherit it). 

It returns an object of class Class which represents the real type of the object it was invoked on: such a single object is created for each class loaded by the JVM. As there is only one such object for any class, one can use == operator to compare types of any two objects by using the objects of class Class representing their types.

Let us consider an example: we define a simple class Point  

## Listing 50 DJV-Inherit/Point.java  

```java
public class Point {

    protected int x, y;

    public Point(int x, int y) {
        this.x = x;
        this.y = y;
    }

    public Point(int x) {
        this(x,0);
    }

    public Point() {
        this(0,0);
    }

    public Point translate(int dx,int dy) {
        x += dx;
        y += dy;
        return this;
    }

    public Point scale(int sx,int sy) {
        x *= sx;
        y *= sy;
        return this;
    }

    public int getX() {
        return x;
    }

    public int getY() {
        return y;
    }

    @Override
    public String toString() {
        return "[" + x + "," + y + "]";
    }
}
```

and then class `Pixel` which extends `Point` (all pixels are points, but they have also a color):  

## Listing 51 DJV-Inherit/Pixel.java 

```java
import java.awt.Color;

public class Pixel extends Point {

    private Color color;

    public Pixel(int x, int y, Color color) {
        super(x,y);
        this.color = color;
    }

    public Pixel(int x, int y) {
        this(x,y,Color.BLACK);
    }

    public Pixel(Color color) {
        this(0,0,color);
    }

    public Pixel() {
        this(0,0,Color.BLACK);
    }

      // n o t  inherited
    public Color getColor() {
        return color;
    }

    @Override
    public String toString() {
        return super.toString() + "(r=" + color.getRed() +
               ",g=" + color.getGreen() + ",b=" +
               color.getBlue() + ")";
    }
}
```

We then check static and dynamic types of various references

## Listing 52 DJV-Inherit/Main.java

```java
import java.awt.Color;

public class Main {
    public static void main(String[] args) {
        Point pp = new Point(2,1);
        Point pt = new Pixel(1,2);
        Pixel px = new Pixel(new Color(255,51,102));

        System.out.println("is 'pp' a Point? " +
                (pp instanceof Point));
        System.out.println("is 'pp' a Pixel? " +
                (pp instanceof Pixel));
        System.out.println("is 'pt' a Point? " +
                (pt instanceof Point));
        System.out.println("is 'pt' a Pixel? " +
                (pt instanceof Pixel));
        System.out.println("is 'px' a Point? " +
                (px instanceof Point));
        System.out.println("is 'px' a Pixel? " +
                (px instanceof Pixel));
        System.out.println("class of pp: " +
                pp.getClass().getName());
        System.out.println("class of pt: " +
                pt.getClass().getName());
        System.out.println("class of px: " +
                px.getClass().getName());
          // Pixel is a Point; we can translate or scale it
        px.translate(5,4).scale(2,3).translate(-1,-3);
        System.out.println("pp: " + pp);
        System.out.println("pt: " + pt);
        System.out.println("px: " + px);
        System.out.println("Color px : " + px.getColor());
          // casting required!
        System.out.println("Color pt : " +
                           ((Pixel)pt).getColor());
    }
}
```

The program prints

```
    is 'pp' a Point? true
    is 'pp' a Pixel? false
    is 'pt' a Point? true
    is 'pt' a Pixel? true
    is 'px' a Point? true
    is 'px' a Pixel? true
    class of pp: Point
    class of pt: Pixel
    class of px: Pixel
    pp: [2,1]
    pt: [1,2](r=0,g=0,b=0)
    px: [9,9](r=255,g=51,b=102)
    Color px : java.awt.Color[r=255,g=51,b=102]
    Color pt : java.awt.Color[r=0,g=0,b=0]
```

Note that we got that `pt` is an instance of `Pixel` although its static type is Point. 

This is because the `instanceof` operator is dynamic (polymorphic): it detects, at runtime, the ‘real’ type of the object pointed to by a reference. 

However, note that we _cannot_ call `getColor` on `pt`: it refers to a `Pixel`, but its static type is `Point` and only the static type is taken into consideration at compile time — as there is no getColor method in Point, the compilation would fail. 

Therefore, we have to _cast_ the `pt` reference (see the last line of the program). 

By writing (`(Pixel)pt`), we are saying to the compiler ‘this is just a `Point` for you, but believe me, I know and I promise you that its real type is `Pixel`’. 

The compiler cannot check it, but at runtime the program will crash if it turns out that the object pointed to by pt is in fact _not_ a `Pixel`.

Next [Listing 53.54.55.56.DJW-InherAnimal](https://github.com/Java-PJATK/53.54.55.56.DJW-InnerAnimal) 


