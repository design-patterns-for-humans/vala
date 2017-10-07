![Design Patterns For Humans](https://cloud.githubusercontent.com/assets/11269635/23065273/1b7e5938-f515-11e6-8dd3-d0d58de6bb9a.png)

***
<p align="center">
🎉 Ultra-simplified explanation to design patterns! 🎉
</p>
<p align="center">
A topic that can easily make anyone's mind wobble. Here I try to make them stick in to your mind (and maybe mine) by explaining them in the <i>simplest</i> way possible.
</p>

***

🚀 Introduction
=================

Design patterns are solutions to recurring problems; **guidelines on how to tackle certain problems**. They are not classes, packages or libraries that you can plug into your application and wait for the magic to happen. These are, rather, guidelines on how to tackle certain problems in certain situations.

> Design patterns are solutions to recurring problems; guidelines on how to tackle certain problems

Wikipedia describes them as

> In software engineering, a software design pattern is a general reusable solution to a commonly occurring problem within a given context in software design. It is not a finished design that can be transformed directly into source or machine code. It is a description or template for how to solve a problem that can be used in many different situations.

⚠️ Be Careful
-----------------
- Design patterns are not a silver bullet to all your problems.
- Do not try to force them; bad things are supposed to happen, if done so. Keep in mind that design patterns are solutions **to** problems, not solutions **finding** problems; so don't overthink.
- If used in a correct place in a correct manner, they can prove to be a savior; or else they can result in a horrible mess of a code.

> Also note that the code samples below are in Vala, however this shouldn't stop you because the concepts are same anyways. Plus the **support for other languages is underway**.

Types of Design Patterns
-----------------

* [Creational](#creational-design-patterns)
* [Structural](#structural-design-patterns)
* [Behavioral](#behavioral-design-patterns)

Creational Design Patterns
==========================

In plain words
> Creational patterns are focused towards how to instantiate an object or group of related objects.

Wikipedia says
> In software engineering, creational design patterns are design patterns that deal with object creation mechanisms, trying to create objects in a manner suitable to the situation. The basic form of object creation could result in design problems or added complexity to the design. Creational design patterns solve this problem by somehow controlling this object creation.

 * [Simple Factory](#-simple-factory)
 * [Factory Method](#-factory-method)
 * [Abstract Factory](#-abstract-factory)
 * [Builder](#-builder)
 * [Prototype](#-prototype)
 * [Singleton](#-singleton)

🏠 Simple Factory
--------------
Real world example
> Consider, you are building a house and you need doors. It would be a mess if every time you need a door, you put on your carpenter clothes and start making a door in your house. Instead you get it made from a factory.

In plain words
> Simple factory simply generates an instance for client without exposing any instantiation logic to the client

Wikipedia says
> In object-oriented programming (OOP), a factory is an object for creating other objects – formally a factory is a function or method that returns objects of a varying prototype or class from some method call, which is assumed to be "new".

**Programmatic Example**

First of all we have a door interface and the implementation
```vala
interface Door : Object {
    public abstract float get_width ();
    public abstract float get_height ();
}

class WoodenDoor: Object, Door {
    protected float width;
    protected float height;

    public WoodenDoor (float width, float height) {
        this.width = width;
        this.height = height;
    }

    public float get_width () {
        return width;
    }

    public float get_height () {
        return height;
    }
}
```
Then we have our door factory that makes the door and returns it
```vala
class DoorFactory {
    public static Door make_door (float width, float height) {
        return new WoodenDoor (width, height);
    }
}
```
And then it can be used as
```vala
var door = DoorFactory.make_door (100,200);
print ("width: %f\n", door.get_width ());
print ("height: %f\n", door.get_height ());
```

**When to Use?**

When creating an object is not just a few assignments and involves some logic, it makes sense to put it in a dedicated factory instead of repeating the same code everywhere.

🏭 Factory Method
--------------

Real world example
> Consider the case of a hiring manager. It is impossible for one person to interview for each of the positions. Based on the job opening, she has to decide and delegate the interview steps to different people.

In plain words
> It provides a way to delegate the instantiation logic to child classes.

Wikipedia says
> In class-based programming, the factory method pattern is a creational pattern that uses factory methods to deal with the problem of creating objects without having to specify the exact class of the object that will be created. This is done by creating objects by calling a factory method—either specified in an interface and implemented by child classes, or implemented in a base class and optionally overridden by derived classes—rather than by calling a constructor.

 **Programmatic Example**

Taking our hiring manager example above. First of all we have an interviewer interface and some implementations for it

```vala
interface Interviewer : Object {
    public abstract void ask_questions ();
}

class Developer : Object, Interviewer {
    public void ask_questions () {
        print ("Asking questions about patterns!\n");
    }
}

class CommunityExecutive : Object, Interviewer {
    public void ask_questions () {
        print ("Asking questions about community building.\n");
    }
}
```

Now let us create our `HiringManager`

```vala
abstract class HiringManager {
    // Factory Method
    public abstract Interviewer make_interviewer ();

    public void take_interview () {
        var interviewer = this.make_interviewer ();
        interviewer.ask_questions ();
    }
}

```
Now any child can extend it and provide the required interviewer
```vala
class DevelopmentManager : HiringManager {
    public override Interviewer make_interviewer () {
        return new Developer ();
    }
}

class MarketingManager : HiringManager {
    public override Interviewer make_interviewer () {
        return new CommunityExecutive ();
    }
}
```
and then it can be used as

```vala
var dev_manager = new DevelopmentManager ();
dev_manager.take_interview (); // Output: Asking about design patterns

var marketing_manager = new MarketingManager ();
marketing_manager.take_interview (); // Output: Asking about community building
```

**When to use?**

Useful when there is some generic processing in a class but the required sub-class is dynamically decided at runtime. Or putting it in other words, when the client doesn't know what exact sub-class it might need.

🔨 Abstract Factory
----------------

Real world example
> Extending our door example from Simple Factory. Based on your needs you might get a wooden door from a wooden door shop, iron door from an iron shop or a PVC door from the relevant shop. Plus you might need a guy with different kind of specialities to fit the door, for example a carpenter for wooden door, welder for iron door etc. As you can see there is a dependency between the doors now, wooden door needs carpenter, iron door needs a welder etc.

In plain words
> A factory of factories; a factory that groups the individual but related/dependent factories together without specifying their concrete classes.

Wikipedia says
> The abstract factory pattern provides a way to encapsulate a group of individual factories that have a common theme without specifying their concrete classes

**Programmatic Example**

Translating the door example above. First of all we have our `Door` interface and some implementation for it

```vala
interface Door : Object {
    public abstract void get_description ();
}

class WoodenDoor : Object, Door {
    public void get_description () {
        print ("I'm a wooden door\n");
    }
}

class IronDoor : Object, Door {
    public void get_description () {
        print ("I'm a iron door\n");
    }
}

```
Then we have some fitting experts for each door type

```vala
interface DoorFittingExpert : Object {
    public abstract void get_description ();
}

class Welder : Object, DoorFittingExpert {
    public void get_description () {
        print ("I can only fit iron doors\n");
    }
}

class Carpenter : Object, DoorFittingExpert {
    public void get_description () {
        print ("I can only fit wooden doors\n");
    }
}
```

Now we have our abstract factory that would let us make family of related objects i.e. wooden door factory would create a wooden door and wooden door fitting expert and iron door factory would create an iron door and iron door fitting expert
```vala
interface DoorFactory : Object {
    public abstract Door make_door ();
    public abstract DoorFittingExpert make_fitting_expert ();
}

// Wooden factory to return carpenter and wooden door
class WoodenDoorFactory : Object, DoorFactory {
    public Door make_door () {
        return new WoodenDoor ();
    }

    public DoorFittingExpert make_fitting_expert () {
        return new Carpenter ();
    }
}

// Iron door factory to get iron door and the relevant fitting expert
class IronDoorFactory : Object, DoorFactory {
    public Door make_door () {
        return new IronDoor ();
    }

    public DoorFittingExpert make_fitting_expert () {
        return new Welder ();
    }
}
```
And then it can be used as
```vala
var wooden_factory = new WoodenDoorFactory ();

var door = wooden_factory.make_door ();
var expert = wooden_factory.make_fitting_expert ();

door.get_description (); // Output: I am a wooden door
expert.get_description (); // Output: I can only fit wooden doors

// Same for Iron Factory
var iron_factory = new IronDoorFactory ();

door = iron_factory.make_door ();
expert = iron_factory.make_fitting_expert ();

door.get_description (); // Output: I am an iron door
expert.get_description (); // Output: I can only fit iron doors
```

As you can see the wooden door factory has encapsulated the `carpenter` and the `wooden door` also iron door factory has encapsulated the `iron door` and `welder`. And thus it had helped us make sure that for each of the created door, we do not get a wrong fitting expert.   

**When to use?**

When there are interrelated dependencies with not-that-simple creation logic involved

👷 Builder
--------------------------------------------
Real world example
> Imagine you are at Hardee's and you order a specific deal, lets say, "Big Hardee" and they hand it over to you without *any questions*; this is the example of simple factory. But there are cases when the creation logic might involve more steps. For example you want a customized Subway deal, you have several options in how your burger is made e.g what bread do you want? what types of sauces would you like? What cheese would you want? etc. In such cases builder pattern comes to the rescue.

In plain words
> Allows you to create different flavors of an object while avoiding constructor pollution. Useful when there could be several flavors of an object. Or when there are a lot of steps involved in creation of an object.

Wikipedia says
> The builder pattern is an object creation software design pattern with the intentions of finding a solution to the telescoping constructor anti-pattern.

Having said that let me add a bit about what telescoping constructor anti-pattern is. At one point or the other we have all seen a constructor like below:

```vala
public Burger (int size, bool cheese = true, bool pepperoni = true, bool tomato = false, bool lettuce = true) {
}
```

As you can see; the number of constructor parameters can quickly get out of hand and it might become difficult to understand the arrangement of parameters. Plus this parameter list could keep on growing if you would want to add more options in future. This is called telescoping constructor anti-pattern.

**Programmatic Example**

The sane alternative is to use the builder pattern. First of all we have our burger that we want to make

```vala
class Burger {
    protected int size;
    
    protected bool cheese = false;
    protected bool pepperoni = false;
    protected bool lettuce = false;
    protected bool tomato = false;

    public Burger (BurgerBuilder builder) {
        size = builder.size;
        cheese = builder.cheese;
        pepperoni = builder.pepperoni;
        lettuce = builder.lettuce;
        tomato = builder.tomato;
    }
}
```

And then we have the builder

```vala
class BurgerBuilder {
    public int size;

    public bool cheese = false;
    public bool pepperoni = false;
    public bool lettuce = false;
    public bool tomato = false;

    public BurgerBuilder (int size) {
        this.size = size;
    }

    public BurgerBuilder add_cheese () {
        cheese = true;
        return this;
    } 

    public BurgerBuilder add_pepperoni () {
        pepperoni = true;
        return this;
    } 

    public BurgerBuilder add_lettuce () {
        lettuce = true;
        return this;
    }

    public BurgerBuilder add_tomato () {
        tomato = true;
        return this;
    }

    public Burger build () {
        return new Burger (this);
    }
}
```
And then it can be used as:

```vala
var burger = (new BurgerBuilder (14))
	.add_pepperoni ()
	.add_lettuce ()
	.add_tomato ()
	.build ();
```

**When to use?**

When there could be several flavors of an object and to avoid the constructor telescoping. The key difference from the factory pattern is that; factory pattern is to be used when the creation is a one step process while builder pattern is to be used when the creation is a multi step process.

🐑 Prototype
------------
Real world example
> Remember dolly? The sheep that was cloned! Lets not get into the details but the key point here is that it is all about cloning

In plain words
> Create object based on an existing object through cloning.

Wikipedia says
> The prototype pattern is a creational design pattern in software development. It is used when the type of objects to create is determined by a prototypical instance, which is cloned to produce new objects.

In short, it allows you to create a copy of an existing object and modify it to your needs, instead of going through the trouble of creating an object from scratch and setting it up.

**Programmatic Example**

In Vala, there's no 'native', generic, shallow or deep, 'clone' method and this topic is a bit controversial, so we just implement a clone method that returns another instance of the same class with duplicated properties.

```vala
class Sheep {
    protected string name;
    protected string category;

    public Sheep (string name, string category = "Mountain Sheep") {
        this.name = name;
        this.category = category;
    }

    public void set_name (string name) {
        this.name = name;
    }

    public string get_name () {
        return name;
    }

    public void set_category (string category) {
        this.category = category;
    }

    public string get_category () {
        return category;
    }

    // No Object clone method, lets implement a clone method
    public Sheep clone () {
        return new Sheep (name, category);
    }
}
```
Then it can be cloned like below
```vala
    var original = new Sheep ("Jolly");
    print ("%s\n", original.get_name ()); // Jolly
    print ("%s\n", original.get_category ()); // Mountain Sheep

// Clone and modify what is required
    var cloned = original.clone ();
    cloned.set_name ("Dolly");
    print ("%s\n", cloned.get_name ()); // Dolly
    print ("%s\n", cloned.get_category ()); // Mountain sheep
```

**When to use?**

When an object is required that is similar to existing object or when the creation would be expensive as compared to cloning.

💍 Singleton
------------
Real world example
> There can only be one president of a country at a time. The same president has to be brought to action, whenever duty calls. President here is singleton.

In plain words
> Ensures that only one object of a particular class is ever created.

Wikipedia says
> In software engineering, the singleton pattern is a software design pattern that restricts the instantiation of a class to one object. This is useful when exactly one object is needed to coordinate actions across the system.

Singleton pattern is actually considered an anti-pattern and overuse of it should be avoided. It is not necessarily bad and could have some valid use-cases but should be used with caution because it introduces a global state in your application and change to it in one place could affect in the other areas and it could become pretty difficult to debug. The other bad thing about them is it makes your code tightly coupled plus it mocking the singleton could be difficult.

**Programmatic Example**

To create a singleton, make the constructor private, disable cloning, disable extension and create a static variable to house the instance
```vala
public class President : Object {
    static President? _instance;
 
    // Private constructor
    private President () {
 
    }
 
    // Public instance read-only property
    public static President instance {
        get{
            if(_instance == null){
                lock(_instance) {
                    if(_instance == null) {
                        _instance = new President();
                    }
                }
            }
            return _instance;
        }
    }

    // No default clone and unserialize methods.
}
```
Then in order to use
```vala
 President a = President.instance;
 President b = President.instance;

 print ((a == b).to_string () + "\n"); // true
```

Structural Design Patterns
==========================
In plain words
> Structural patterns are mostly concerned with object composition or in other words how the entities can use each other. Or yet another explanation would be, they help in answering "How to build a software component?"

Wikipedia says
> In software engineering, structural design patterns are design patterns that ease the design by identifying a simple way to realize relationships between entities.

 * [Adapter](#-adapter)
 * [Bridge](#-bridge)
 * [Composite](#-composite)
 * [Decorator](#-decorator)
 * [Facade](#-facade)
 * [Flyweight](#-flyweight)
 * [Proxy](#-proxy)

🔌 Adapter
-------
Real world example
> Consider that you have some pictures in your memory card and you need to transfer them to your computer. In order to transfer them you need some kind of adapter that is compatible with your computer ports so that you can attach memory card to your computer. In this case card reader is an adapter.
> Another example would be the famous power adapter; a three legged plug can't be connected to a two pronged outlet, it needs to use a power adapter that makes it compatible with the two pronged outlet.
> Yet another example would be a translator translating words spoken by one person to another

In plain words
> Adapter pattern lets you wrap an otherwise incompatible object in an adapter to make it compatible with another class.

Wikipedia says
> In software engineering, the adapter pattern is a software design pattern that allows the interface of an existing class to be used as another interface. It is often used to make existing classes work with others without modifying their source code.

**Programmatic Example**

Consider a game where there is a hunter and he hunts lions.

First we have an interface `Lion` that all types of lions have to implement

```vala
interface Lion {
    public abstract void roar ();
}

class AfricanLion: Lion {
    public void roar () {

    }
}

class AsiaLion: Lion {
    public void roar () {

    }
}
```
And hunter expects any implementation of `Lion` interface to hunt.
```vala
class Hunter {
    public void hunt (Lion lion) {

    }
}
```

Now let's say we have to add a `WildDog` in our game so that hunter can hunt that also. But we can't do that directly because dog has a different interface. To make it compatible for our hunter, we will have to create an adapter that is compatible

```vala
// This needs to be added to the game
class WildDog {
    public void bark () {
    }
}

// Adapter around wild dog to make it compatible with our game
class WildDogAdapter: Lion {
    protected WildDog dog;

    public WildDogAdapter (WildDog dog) {
        this.dog = dog;
    }

    public void roar () {
        dog.bark ();
    }
}
```
And now the `WildDog` can be used in our game using `WildDogAdapter`.

```vala
var wild_dog = new WildDog ();
var wild_dog_adapter = new WildDogAdapter (wild_dog);

var hunter = new Hunter ();
hunter.hunt (wild_dog_adapter);
```

🚡 Bridge
------
Real world example
> Consider you have a website with different pages and you are supposed to allow the user to change the theme. What would you do? Create multiple copies of each of the pages for each of the themes or would you just create separate theme and load them based on the user's preferences? Bridge pattern allows you to do the second i.e.

![With and without the bridge pattern](https://cloud.githubusercontent.com/assets/11269635/23065293/33b7aea0-f515-11e6-983f-98823c9845ee.png)

In Plain Words
> Bridge pattern is about preferring composition over inheritance. Implementation details are pushed from a hierarchy to another object with a separate hierarchy.

Wikipedia says
> The bridge pattern is a design pattern used in software engineering that is meant to "decouple an abstraction from its implementation so that the two can vary independently"

**Programmatic Example**

Translating our WebPage example from above. Here we have the `WebPage` hierarchy

```vala
interface WebPage {
    //abstract constructor doesn't exist, at least that i know of
    public abstract string get_content ();
}

class About : WebPage {
    protected Theme theme;

    public About (Theme theme) {
        this.theme = theme; 
    } 

    public string get_content () {
        return "About page in " + theme.get_color () + "\n";
    }
}

class Careers : WebPage {
    protected Theme theme;

    public Careers (Theme theme) {
        this.theme = theme; 
    } 

    public string get_content () {
        return "Careers page in " + theme.get_color () + "\n";
    }
}
```
And the separate theme hierarchy
```vala

interface Theme : Object {
    public abstract string get_color ();
}

class DarkTheme : Object, Theme {
    public string get_color () {
        return "Dark Black";
    }
}

class LightTheme : Object, Theme {
    public string get_color () {
        return "Off White";
    }
}

class AquaTheme : Object, Theme {
    public string get_color () {
        return "Light blue";
    }
}
```
And both the hierarchies
```vala
var dark_theme = new DarkTheme ();

var about = new About (dark_theme); 
var careers = new Careers (dark_theme); 

print ("%s", about.get_content ()); // "About page in Dark Black";
print ("%s", careers.get_content ()); // "Careers page in Dark Black";
```

🌿 Composite
-----------------

Real world example
> Every organization is composed of employees. Each of the employees has the same features i.e. has a salary, has some responsibilities, may or may not report to someone, may or may not have some subordinates etc.

In plain words
> Composite pattern lets clients treat the individual objects in a uniform manner.

Wikipedia says
> In software engineering, the composite pattern is a partitioning design pattern. The composite pattern describes that a group of objects is to be treated in the same way as a single instance of an object. The intent of a composite is to "compose" objects into tree structures to represent part-whole hierarchies. Implementing the composite pattern lets clients treat individual objects and compositions uniformly.

**Programmatic Example**

Taking our employees example from above. Here we have different employee types

```vala
interface Employee : Object {
    protected abstract string _name {protected get; protected set;}
    protected abstract float _salary {protected get; protected set;}

    // no overridable construct
    
    public string get_name () {
        return _name;
    }

    public void set_salary (float salary) {
        _salary = salary;
    }

    public float get_salary () {
        return _salary;
    } 
}


class Developer : Object, Employee {
    protected string _name {protected get; protected set;}
    protected float _salary {protected get; protected set;}

    public Developer (string name, float salary) {
        _name = name;
        _salary = salary;
    }
}

class Designer : Object, Employee {
    protected string _name {protected get; protected set;}
    protected float _salary {protected get; protected set;}

    public Designer (string name, float salary) {
        _name = name;
        _salary = salary;
    }
}
```

Then we have an organization which consists of several different types of employees

```vala
class Organization {
    protected List<Employee> employees;

    public void add_employee (Employee employee) {
        employees.append (employee);
    }

    public float get_net_salaries () {
        float net_salary = 0;

        employees.foreach ((employee) => {
            net_salary += employee.get_salary ();
        });

        return net_salary;
    }
}
```

And then it can be used as

```vala
// Prepare the employees
var john = new Developer ("John Doe", 12000);
var jane = new Developer ("Jane", 10000);

// Add them to organization
var organization = new Organization ();
organization.add_employee (john);
organization.add_employee (jane);

print ("Net salaries: " + organization.get_net_salaries ().to_string () + "\n");
```

☕ Decorator
-------------

Real world example

> Imagine you run a car service shop offering multiple services. Now how do you calculate the bill to be charged? You pick one service and dynamically keep adding to it the prices for the provided services till you get the final cost. Here each type of service is a decorator.

In plain words
> Decorator pattern lets you dynamically change the behavior of an object at run time by wrapping them in an object of a decorator class.

Wikipedia says
> In object-oriented programming, the decorator pattern is a design pattern that allows behavior to be added to an individual object, either statically or dynamically, without affecting the behavior of other objects from the same class. The decorator pattern is often useful for adhering to the Single Responsibility Principle, as it allows functionality to be divided between classes with unique areas of concern.

**Programmatic Example**

Lets take coffee for example. First of all we have a simple coffee implementing the coffee interface

```vala
interface Coffee : Object {
    public abstract int get_cost ();
    public abstract string get_description ();
}

class SimpleCoffee : Object, Coffee {
    public int get_cost () {
        return 10;
    }

    public string get_description () {
        return "Simple coffee";
    }
}   
```
We want to make the code extensible to allow options to modify it if required. Lets make some add-ons (decorators)
```vala
class MilkCoffee : Object, Coffee {
    protected Coffee coffee;

    public MilkCoffee (Coffee coffee) {
        this.coffee = coffee;
    }

    public int get_cost () {
        return coffee.get_cost () + 2;
    }
    
    public string get_description () {
        return coffee.get_description () + ", milk";
    }
}

class WhipCoffee : Object, Coffee {
    protected Coffee coffee;

    public WhipCoffee (Coffee coffee) {
        this.coffee = coffee;
    }

    public int get_cost () {
        return coffee.get_cost () + 5;
    }
    
    public string get_description () {
        return coffee.get_description () + ", whip";
    }
}

class VanillaCoffee : Object, Coffee {
    protected Coffee coffee;

    public VanillaCoffee (Coffee coffee) {
        this.coffee = coffee;
    }

    public int get_cost () {
        return coffee.get_cost () + 3;
    }
    
    public string get_description () {
        return coffee.get_description () + ", vanilla";
    }
}
```

Lets make a coffee now

```vala
Coffee some_coffee = new SimpleCoffee ();
print ("%d\n", some_coffee.get_cost ()); // 10
print ("%s\n", some_coffee.get_description ()); // Simple Coffee

some_coffee = new MilkCoffee (some_coffee);
print ("%d\n", some_coffee.get_cost ()); // 12
print ("%s\n", some_coffee.get_description ()); // Simple Coffee, milk

some_coffee = new WhipCoffee (some_coffee);
print ("%d\n", some_coffee.get_cost ()); // 17
print ("%s\n", some_coffee.get_description ()); // Simple Coffee, milk, whip

some_coffee = new VanillaCoffee (some_coffee);
print ("%d\n", some_coffee.get_cost ()); // 20
print ("%s\n", some_coffee.get_description ()); // Simple Coffee, milk, vanilla
```

📦 Facade
----------------

Real world example
> How do you turn on the computer? "Hit the power button" you say! That is what you believe because you are using a simple interface that computer provides on the outside, internally it has to do a lot of stuff to make it happen. This simple interface to the complex subsystem is a facade.

In plain words
> Facade pattern provides a simplified interface to a complex subsystem.

Wikipedia says
> A facade is an object that provides a simplified interface to a larger body of code, such as a class library.

**Programmatic Example**

Taking our computer example from above. Here we have the computer class

```vala
class Computer {
    public void get_electric_shock () {
        print ("Ouch!\n");
    }

    public void make_sound () {
        print ("Beep beep!\n");
    }

    public void show_loading_screen () {
        print ("Loading...\n");
    }

    public void bam () {
        print ("Ready to be used!\n");
    }

    public void close_everything () {
        print ("Bup bup bup buzzzz!\n");
    }

    public void sooth () {
        print ("Zzzzz\n");
    }

    public void pull_current () {
        print ("Haaah!\n");
    }
}
```
Here we have the facade
```vala
class ComputerFacade {
    protected Computer computer;

    public ComputerFacade (Computer computer) {
        this.computer = computer;
    }

    public void turn_on () {
        computer.get_electric_shock ();
        computer.make_sound ();
        computer.show_loading_screen ();
        computer.bam ();
    }

    public void turn_off () {
        computer.close_everything ();
        computer.pull_current ();
        computer.sooth ();
    }
}
```
Now to use the facade
```vala
    var computer = new ComputerFacade (new Computer());
    computer.turn_on (); // Ouch! Beep beep! Loading.. Ready to be used!
    computer.turn_off (); // Bup bup buzzz! Haah! Zzzzz
```

🍃 Flyweight
---------

Real world example
> Did you ever have fresh tea from some stall? They often make more than one cup that you demanded and save the rest for any other customer so to save the resources e.g. gas etc. Flyweight pattern is all about that i.e. sharing.

In plain words
> It is used to minimize memory usage or computational expenses by sharing as much as possible with similar objects.

Wikipedia says
> In computer programming, flyweight is a software design pattern. A flyweight is an object that minimizes memory use by sharing as much data as possible with other similar objects; it is a way to use objects in large numbers when a simple repeated representation would use an unacceptable amount of memory.

**Programmatic example**

Translating our tea example from above. First of all we have tea types and tea maker

```vala
// Anything that will be cached is flyweight.
// Types of tea here will be flyweights.
using Gee;

class KarakTea {

}

// Acts as a factory and saves the tea
class TeaMaker {
    protected HashMap<string, KarakTea> available_tea = new HashMap<string, KarakTea> ();

    public KarakTea make (string preference) {
        if (!available_tea.has_key (preference)) {
            available_tea[preference] = new KarakTea ();
        }

        return available_tea[preference];
    }
}
```

Then we have the `TeaShop` which takes orders and serves them

```vala
class TeaShop {
    protected HashMap<int, KarakTea> orders = new HashMap<int, KarakTea> ();
    protected TeaMaker tea_maker;

    public TeaShop (TeaMaker tea_maker) {
        this.tea_maker = tea_maker;
    }

    public void take_order (string tea_type, int table) {
        orders[table] = tea_maker.make (tea_type); 
    }

    public void serve () {
        foreach (int table in orders.keys) {
            print ("Serving tea to table# %d\n", table);
        }
    }
}
```
And it can be used as below

```vala
var tea_maker = new TeaMaker ();
var shop = new TeaShop (tea_maker);

shop.take_order ("less sugar", 1);
shop.take_order ("more milk", 2);
shop.take_order ("without sugar", 5);

shop.serve ();
// Serving tea to table# 1
// Serving tea to table# 2
// Serving tea to table# 5
```

🎱 Proxy
-------------------
Real world example
> Have you ever used an access card to go through a door? There are multiple options to open that door i.e. it can be opened either using access card or by pressing a button that bypasses the security. The door's main functionality is to open but there is a proxy added on top of it to add some functionality. Let me better explain it using the code example below.

In plain words
> Using the proxy pattern, a class represents the functionality of another class.

Wikipedia says
> A proxy, in its most general form, is a class functioning as an interface to something else. A proxy is a wrapper or agent object that is being called by the client to access the real serving object behind the scenes. Use of the proxy can simply be forwarding to the real object, or can provide additional logic. In the proxy extra functionality can be provided, for example caching when operations on the real object are resource intensive, or checking preconditions before operations on the real object are invoked.

**Programmatic Example**

Taking our security door example from above. Firstly we have the door interface and an implementation of door

```vala
interface Door : Object {
    public abstract void open ();
    public abstract void close ();
}

class LabDoor : Object, Door {
    public void open () {
        print ("Opening lab door\n");
    }

    public void close () {
        print ("Closing the lab door\n");
    }
}
```
Then we have a proxy to secure any doors that we want
```vala
class Security : Object {
    protected Door door;

    public Security (Door door) {
        this.door = door;
    }

    public void open (string password) {
        if (authenticate (password)) {
            door.open ();
        } else {
            print ("Big no! It ain't passible.\n");
        }
    }

    public bool authenticate (string password) {
        return password == "$ecr@t";
    }

    public void close () {
        door.close ();
    }
}
```
And here is how it can be used
```vala
var door = new Security (new LabDoor ());
door.open ("invalid"); // Big no! It ain't possible.

door.open ("$ecr@t"); // Opening lab door
door.close (); // Closing lab door
```
Yet another example would be some sort of data-mapper implementation. For example, I recently made an ODM (Object Data Mapper), in PHP, for MongoDB using this pattern where I wrote a proxy around mongo classes while utilizing the magic method `__call()`. All the method calls were proxied to the original mongo class and result retrieved was returned as it is but in case of `find` or `findOne` data was mapped to the required class objects and the object was returned instead of `Cursor`.

Behavioral Design Patterns
==========================

In plain words
> It is concerned with assignment of responsibilities between the objects. What makes them different from structural patterns is they don't just specify the structure but also outline the patterns for message passing/communication between them. Or in other words, they assist in answering "How to run a behavior in software component?"

Wikipedia says
> In software engineering, behavioral design patterns are design patterns that identify common communication patterns between objects and realize these patterns. By doing so, these patterns increase flexibility in carrying out this communication.

* [Chain of Responsibility](#-chain-of-responsibility)
* [Command](#-command)
* [Iterator](#-iterator)
* [Mediator](#-mediator)
* [Memento](#-memento)
* [Observer](#-observer)
* [Visitor](#-visitor)
* [Strategy](#-strategy)
* [State](#-state)
* [Template Method](#-template-method)

🔗 Chain of Responsibility
-----------------------

Real world example
> For example, you have three payment methods (`A`, `B` and `C`) setup in your account; each having a different amount in it. `A` has 100 USD, `B` has 300 USD and `C` having 1000 USD and the preference for payments is chosen as `A` then `B` then `C`. You try to purchase something that is worth 210 USD. Using Chain of Responsibility, first of all account `A` will be checked if it can make the purchase, if yes purchase will be made and the chain will be broken. If not, request will move forward to account `B` checking for amount if yes chain will be broken otherwise the request will keep forwarding till it finds the suitable handler. Here `A`, `B` and `C` are links of the chain and the whole phenomenon is Chain of Responsibility.

In plain words
> It helps building a chain of objects. Request enters from one end and keeps going from object to object till it finds the suitable handler.

Wikipedia says
> In object-oriented design, the chain-of-responsibility pattern is a design pattern consisting of a source of command objects and a series of processing objects. Each processing object contains logic that defines the types of command objects that it can handle; the rest are passed to the next processing object in the chain.

**Programmatic Example**

Translating our account example above. First of all we have a base account having the logic for chaining the accounts together and some accounts

```vala
public errordomain OurError {
    PAY_ERROR 
}

abstract class Account : Object {
    protected Account? successor = null;
    protected float balance;

    public void set_next (Account account) {
        successor = account;
    }

    public void pay (float ammount_to_pay) throws OurError {
        if (can_pay (ammount_to_pay)) {
            print ("Paid %f using %s\n", ammount_to_pay, get_type ().name ()); 
        } else if (successor != null) {
            print ("Cannot pay using %s, Proceeding ..\n", get_type ().name ()); 
            successor.pay (ammount_to_pay);
        } else {
                throw new OurError.PAY_ERROR ("None of the accounts have enough balance");
        }
    }

    public bool can_pay (float ammount) {
        return balance >= ammount;
    }
}

class Bank : Account {
    public Bank (float balance) {
        this.balance = balance;
    }
}

class Paypal : Account {
    public Paypal (float balance) {
        this.balance = balance;
    }
}

class Bitcoin : Account {
    public Bitcoin (float balance) {
        this.balance = balance;
    }
}
```

Now let's prepare the chain using the links defined above (i.e. Bank, Paypal, Bitcoin)

```vala
// Let's prepare a chain like below
//      $bank->$paypal->$bitcoin
//
// First priority bank
//      If bank can't pay then paypal
//      If paypal can't pay then bit coin

var bank = new Bank (100);       // Bank with balance 100
var paypal = new Paypal (200);   // Paypal with balance 200
var bitcoin = new Bitcoin (300); // Bitcoin with balance 300

bank.set_next (paypal);
paypal.set_next (bitcoin);

// Let's try to pay using the first priority i.e. bank
try {
  bank.pay (259);
} catch (OurError e) {
  stderr.printf ("%s\n", e.message);
}

// Output will be
// ==============
// Cannot pay using bank. Proceeding ..
// Cannot pay using paypal. Proceeding ..:
// Paid 259 using Bitcoin!
```

👮 Command
-------

Real world example
> A generic example would be you ordering a food at restaurant. You (i.e. `Client`) ask the waiter (i.e. `Invoker`) to bring some food (i.e. `Command`) and waiter simply forwards the request to Chef (i.e. `Receiver`) who has the knowledge of what and how to cook.
> Another example would be you (i.e. `Client`) switching on (i.e. `Command`) the television (i.e. `Receiver`) using a remote control (`Invoker`).

In plain words
> Allows you to encapsulate actions in objects. The key idea behind this pattern is to provide the means to decouple client from receiver.

Wikipedia says
> In object-oriented programming, the command pattern is a behavioral design pattern in which an object is used to encapsulate all information needed to perform an action or trigger an event at a later time. This information includes the method name, the object that owns the method and values for the method parameters.

**Programmatic Example**

First of all we have the receiver that has the implementation of every action that could be performed
```vala
// Receiver
class Bulb {
    public void turn_on () {
        print ("Bulb has been lit\n");
    }

    public void turn_off () {
        print ("Darkness!\n");
    }
}
```
then we have an interface that each of the commands are going to implement and then we have a set of commands
```vala
interface Command {
    public abstract void execute ();
    public abstract void undo ();
    public abstract void redo ();
}

// Command
class TurnOn : Command {
    protected Bulb bulb;

    public TurnOn (Bulb bulb) {
        this.bulb = bulb;
    }

    public void execute () {
        bulb.turn_on ();
    }

    public void undo () {
        bulb.turn_off ();
    }

    public void redo () {
        execute ();
    }
}

class TurnOff : Command {
    protected Bulb bulb;

    public TurnOff (Bulb bulb) {
        this.bulb = bulb;
    }

    public void execute () {
        bulb.turn_off ();
    }

    public void undo () {
        bulb.turn_on ();
    }

    public void redo () {
        execute ();
    }
}
```
Then we have an `Invoker` with whom the client will interact to process any commands
```vala
// Invoker
class RemoteControl {
    public void submit (Command command) {
        command.execute ();
    }
}
```
Finally let's see how we can use it in our client
```vala
var bulb = new Bulb ();

var turn_on = new TurnOn (bulb);
var turn_off= new TurnOff (bulb);

var remote = new RemoteControl ();
remote.submit (turn_on); // Bulb has been lit!
remote.submit (turn_off); // Darkness!
```

Command pattern can also be used to implement a transaction based system. Where you keep maintaining the history of commands as soon as you execute them. If the final command is successfully executed, all good otherwise just iterate through the history and keep executing the `undo` on all the executed commands.

➿ Iterator
--------

Real world example
> An old radio set will be a good example of iterator, where user could start at some channel and then use next or previous buttons to go through the respective channels. Or take an example of MP3 player or a TV set where you could press the next and previous buttons to go through the consecutive channels or in other words they all provide an interface to iterate through the respective channels, songs or radio stations.  

In plain words
> It presents a way to access the elements of an object without exposing the underlying presentation.

Wikipedia says
> In object-oriented programming, the iterator pattern is a design pattern in which an iterator is used to traverse a container and access the container's elements. The iterator pattern decouples algorithms from containers; in some cases, algorithms are necessarily container-specific and thus cannot be decoupled.

**Programmatic example**

In Vala we'll use Libgee which implements collections and derivatives, includind iterators. Translating our radio stations example from above. First of all we have `RadioStation`

```vala
class RadioStation {
    protected float frequency;

    public RadioStation (float frequency) {
        this.frequency = frequency;
    }

    public float get_frequency () {
        return frequency;
    }
}
```
Then we have our iterator

```vala
using Gee; 

class StationList : Object, Traversable<RadioStation>, Iterable<RadioStation> {
    protected ArrayList<RadioStation> stations = new ArrayList<RadioStation> ();
    
    public delegate bool ForallFunc (owned RadioStation r);

    public void add_station (RadioStation station) {
        stations.add (station);
    }

    public bool remove_station (RadioStation to_remove) {
        foreach (RadioStation station in stations) {
            if (station.get_frequency () == to_remove.get_frequency ()) {
                stations.remove (station);
                return true;
            }
        }

        return false;
    }

    public int count () {
        return stations.size;
    }

    public Type element_type {
        get { return typeof (RadioStation); }
    }

    public bool @foreach (ForallFunc f) {
        // ... 
        return true;
    }

    public Iterator<RadioStation> iterator () {
        return stations.iterator ();
    }
}
```
And then it can be used as
```Vala
var station_list = new StationList ();

station_list.add_station (new RadioStation (89.0f));
station_list.add_station (new RadioStation (101.0f));
station_list.add_station (new RadioStation (102.0f));
station_list.add_station (new RadioStation (103.2f));

foreach (RadioStation r in station_list) {
  print ("%f\n", r.get_frequency ());
}

station_list.remove_station (new RadioStation (89.0f)); // Will remove station 89
```

👽 Mediator
========

Real world example
> A general example would be when you talk to someone on your mobile phone, there is a network provider sitting between you and them and your conversation goes through it instead of being directly sent. In this case network provider is mediator.

In plain words
> Mediator pattern adds a third party object (called mediator) to control the interaction between two objects (called colleagues). It helps reduce the coupling between the classes communicating with each other. Because now they don't need to have the knowledge of each other's implementation.

Wikipedia says
> In software engineering, the mediator pattern defines an object that encapsulates how a set of objects interact. This pattern is considered to be a behavioral pattern due to the way it can alter the program's running behavior.

**Programmatic Example**

Here is the simplest example of a chat room (i.e. mediator) with users (i.e. colleagues) sending messages to each other.

First of all, we have the mediator i.e. the chat room

```vala
interface ChatRoomMediator : Object {
    public abstract void show_message (User user, string message);
}

// Mediator
class ChatRoom : Object, ChatRoomMediator {
    public void show_message (User user, string message) {
        var time = new DateTime.now_local ();
        var sender = user.get_name ();
        print ("%s [%s]:%s\n", time.to_string (), sender, message);
    }
}
```

Then we have our users i.e. colleagues
```vala
class User {
    protected string name;
    protected ChatRoomMediator chat_mediator;

    public User (string name, ChatRoomMediator chat_mediator) {
        this.name = name;
        this.chat_mediator = chat_mediator;
    }

    public string get_name () {
        return name;
    }

    public void send (string message) {
        chat_mediator.show_message (this, message);
    }
}
```
And the usage
```vala
var mediator = new ChatRoom ();

var john = new User ("John Doe", mediator);
var jane = new User ("Jane Dow", mediator);

john.send ("Hi there!");
jane.send ("Hey!");

// Output will be similar to
// Feb 14, 10:58 [John]: Hi there!
// Feb 14, 10:58 [Jane]: Hey!
```

💾 Memento
-------
Real world example
> Take the example of calculator (i.e. originator), where whenever you perform some calculation the last calculation is saved in memory (i.e. memento) so that you can get back to it and maybe get it restored using some action buttons (i.e. caretaker).

In plain words
> Memento pattern is about capturing and storing the current state of an object in a manner that it can be restored later on in a smooth manner.

Wikipedia says
> The memento pattern is a software design pattern that provides the ability to restore an object to its previous state (undo via rollback).

Usually useful when you need to provide some sort of undo functionality.

**Programmatic Example**

Lets take an example of text editor which keeps saving the state from time to time and that you can restore if you want.

First of all we have our memento object that will be able to hold the editor state

```vala
class EditorMemento {
    protected string content;

    public EditorMemento (string content) {
        this.content = content;
    }

    public string get_content () {
        return content;
    }
}
```

Then we have our editor i.e. originator that is going to use memento object

```vala
class Editor {
    protected string content = "";

    public void type (string words) {
        content = content + " " + words;
    }

    public string get_content () {
        return content;
    }

    public EditorMemento save () {
        return new EditorMemento (content);
    }

    public void restore (EditorMemento memento) {
        content = memento.get_content ();
    }
}
```

And then it can be used as

```vala
var editor = new Editor ();

// Type some stuff
editor.type ("This is the first sentence.");
editor.type ("This is second."); 

// Save the state to restore to : This is the first sentence. This is second.
var saved = editor.save ();

// Type some more
editor.type ("And this is third.");

// Output: Content before Saving
print ("%s\n", editor.get_content ());

// Restoring to last saved state
editor.restore (saved);

print ("%s\n", editor.get_content ());
```

😎 Observer
--------
Real world example
> A good example would be the job seekers where they subscribe to some job posting site and they are notified whenever there is a matching job opportunity.   

In plain words
> Defines a dependency between objects so that whenever an object changes its state, all its dependents are notified.

Wikipedia says
> The observer pattern is a software design pattern in which an object, called the subject, maintains a list of its dependents, called observers, and notifies them automatically of any state changes, usually by calling one of their methods.

**Programmatic example**

Translating our example from above. First of all we have job seekers that need to be notified for a job posting
```vala
interface Observer : Object {
}

interface Observable {
}

class JobPost {
    protected string title;

    public JobPost (string title) {
        this.title = title;
    }

    public string get_title () {
        return title;
    }
}

class JobSeeker : Object, Observer {
    protected string name;

    public JobSeeker (string name) {
        this.name = name;
    }

    public void on_job_posted (JobPost job) {
        // Do something with the job posting
        print ("Hi %s! New job posted: %s\n", name, job.get_title ()); 
    }
}
```
Then we have our job postings to which the job seekers will subscribe
```vala
class JobPostings : Observable {
    protected ArrayList<Observer> observers = new ArrayList<Observer> ();

    public void notify (JobPost job_posting) {
        foreach (Observer observer in observers) {
            ((JobSeeker) observer).on_job_posted (job_posting);
        }
    }

    public void attach (Observer observer) {
        observers.add (observer);
    }

    public void add_job (JobPost job_posting) {
        notify (job_posting);
    }
}
```
Then it can be used as
```vala
// Create subscribers
var john_doe = new JobSeeker ("John Doe");
var jane_doe = new JobSeeker ("Jane Doe");

// Create publisher and attach subscribers
var job_postings = new JobPostings ();
job_postings.attach (john_doe);
job_postings.attach (jane_doe);

// Add a new job and see if subscribers get notified
job_postings.add_job (new JobPost ("Software Engineer"));

// Output
// Hi John Doe! New job posted: Software Engineer
// Hi Jane Doe! New job posted: Software Engineer
```

🏃 Visitor
-------
Real world example
> Consider someone visiting Dubai. They just need a way (i.e. visa) to enter Dubai. After arrival, they can come and visit any place in Dubai on their own without having to ask for permission or to do some leg work in order to visit any place here; just let them know of a place and they can visit it. Visitor pattern lets you do just that, it helps you add places to visit so that they can visit as much as they can without having to do any legwork.

In plain words
> Visitor pattern lets you add further operations to objects without having to modify them.

Wikipedia says
> In object-oriented programming and software engineering, the visitor design pattern is a way of separating an algorithm from an object structure on which it operates. A practical result of this separation is the ability to add new operations to existing object structures without modifying those structures. It is one way to follow the open/closed principle.

**Programmatic example**

Let's take an example of a zoo simulation where we have several different kinds of animals and we have to make them Sound. Let's translate this using visitor pattern

```vala
// Visitee
interface Animal {
    public abstract void accept (AnimalOperation operation);
}

// Visitor
interface AnimalOperation {
    public abstract void visit_monkey (Monkey monkey);
    public abstract void visit_lion (Lion lion);
    public abstract void visit_dolphin (Dolphin dolphin);
}
```
Then we have our implementations for the animals
```vala
class Monkey : Animal {
    public void shout () {
        print ("Ooh oo aa aa!\n"); 
    }

    public void accept (AnimalOperation operation) {
        operation.visit_monkey (this);
    }
}

class Lion : Animal {
    public void roar () {
        print ("Roaaar !\n"); 
    }

    public void accept (AnimalOperation operation) {
        operation.visit_lion (this);
    }
}

class Dolphin : Animal {
    public void speak () {
        print ("Tuut tuttu tuutt!\n"); 
    }

    public void accept (AnimalOperation operation) {
        operation.visit_dolphin (this);
    }
}
```
Let's implement our visitor
```vala
class Speak : AnimalOperation {
    public void visit_monkey (Monkey monkey) {
        monkey.shout ();
    }

    public void visit_lion (Lion lion) {
        lion.roar ();
    }

    public void visit_dolphin (Dolphin dolphin) {
        dolphin.speak ();
    }
}
```

And then it can be used as
```vala
    var monkey = new Monkey ();
    var lion = new Lion ();
    var dolphin = new Dolphin ();

    var speak = new Speak ();

    monkey.accept (speak);  // Ooh oo aa aa! 
    lion.accept (speak);    // Roaaar!
    dolphin.accept (speak); // Tuut tutt tuutt!
```
We could have done this simply by having a inheritance hierarchy for the animals but then we would have to modify the animals whenever we would have to add new actions to animals. But now we will not have to change them. For example, let's say we are asked to add the jump behavior to the animals, we can simply add that by creating a new visitor i.e.

```vala
class Jump : AnimalOperation {
    public void visit_monkey (Monkey monkey) {
        print ("Jumped 20 feet high! on to the tree!\n");
    }

    public void visit_lion (Lion lion) {
        print ("Jumped 7 feet! Back on the ground!\n");
    }

    public void visit_dolphin (Dolphin dolphin) {
        print ("Walked on water a little and disappeared\n");
    }
}
```
And for the usage
```vala
var jump = new Jump ();

monkey.accept (speak);  // Ooh oo aa aa! 
monkey.accept (jump);   // Jumped 20 feet high! on to the tree!


lion.accept (speak);    // Roaaar!
lion.accept (jump);     // Jumped 7 feet! Back on the ground!

dolphin.accept (speak); // Tuut tutt tuutt!
dolphin.accept (jump);  // Walked on water a little and disappeared
```

💡 Strategy
--------

Real world example
> Consider the example of sorting, we implemented bubble sort but the data started to grow and bubble sort started getting very slow. In order to tackle this we implemented Quick sort. But now although the quick sort algorithm was doing better for large datasets, it was very slow for smaller datasets. In order to handle this we implemented a strategy where for small datasets, bubble sort will be used and for larger, quick sort.

In plain words
> Strategy pattern allows you to switch the algorithm or strategy based upon the situation.

Wikipedia says
> In computer programming, the strategy pattern (also known as the policy pattern) is a behavioural software design pattern that enables an algorithm's behavior to be selected at runtime.

**Programmatic example**

Translating our example from above. First of all we have our strategy interface and different strategy implementations

```vala
interface SortStrategy : Object {
    public abstract int[] sort (int[] dataset);
}

class BubbleSortStrategy : Object, SortStrategy {
    public int[] sort (int[] dataset) {
        print ("Sorting using bubble sort\n");
        
        //do sorting
        return dataset;
    }
}

class QuickSortStrategy : Object, SortStrategy {
    public int[] sort (int[] dataset) {
        print ("Sorting using quick sort\n");
        
        //do sorting
        return dataset;
    }
}
```

And then we have our client that is going to use any strategy
```vala
class Sorter {
    protected SortStrategy sorter;

    public Sorter (SortStrategy sorter) {
        this.sorter = sorter;
    }
    
    public int[] sort (int[] dataset) {
        return sorter.sort (dataset);
    }
}
```
And it can be used as
```vala
int[] dataset = {1, 5, 4, 3 ,2, 8};

var sorter = new Sorter (new BubbleSortStrategy ());
sorter.sort (dataset); // output : sorting using bubble sort


sorter = new Sorter (new QuickSortStrategy ());
sorter.sort (dataset); // Output : Sorting using quick sort
```

💢 State
-----
Real world example
> Imagine you are using some drawing application, you choose the paint brush to draw. Now the brush changes its behavior based on the selected color i.e. if you have chosen red color it will draw in red, if blue then it will be in blue etc.  

In plain words
> It lets you change the behavior of a class when the state changes.

Wikipedia says
> The state pattern is a behavioral software design pattern that implements a state machine in an object-oriented way. With the state pattern, a state machine is implemented by implementing each individual state as a derived class of the state pattern interface, and implementing state transitions by invoking methods defined by the pattern's superclass.
> The state pattern can be interpreted as a strategy pattern which is able to switch the current strategy through invocations of methods defined in the pattern's interface.

**Programmatic example**

Let's take an example of text editor, it lets you change the state of text that is typed i.e. if you have selected bold, it starts writing in bold, if italic then in italics etc.

First of all we have our state interface and some state implementations

```vala
interface WritingState : Object {
    public abstract void write (string words);
}

class UpperCase : Object, WritingState {
    public void write (string words) {
        print ("%s\n", words.up ());
    }
}

class LowerCase : Object, WritingState {
    public void write (string words) {
        print ("%s\n", words.down ());
    }
}


class Default : Object, WritingState {
    public void write (string words) {
        print ("%s\n", words);
    }
}
```
Then we have our editor
```vala
class TextEditor {
    protected WritingState state;

    public TextEditor (WritingState state) {
        this.state = state;
    }

    public void set_state (WritingState state) {
        this.state = state;
    }

    public void type (string words) {
        state.write (words);
    }
}
```
And then it can be used as
```vala
var editor = new TextEditor (new Default ());

editor.type ("First line");

editor.set_state (new UpperCase ());

editor.type ("Second line");
editor.type ("Third line");

editor.set_state (new LowerCase ());
editor.type ("Fourth line");
editor.type ("Fifth line");

// Output:
// First line
// SECOND LINE
// THIRD LINE
// fourth line
// fifth line
```

📒 Template Method
---------------

Real world example
> Suppose we are getting some house built. The steps for building might look like
> - Prepare the base of house
> - Build the walls
> - Add roof
> - Add other floors

> The order of these steps could never be changed i.e. you can't build the roof before building the walls etc but each of the steps could be modified for example walls can be made of wood or polyester or stone.

In plain words
> Template method defines the skeleton of how a certain algorithm could be performed, but defers the implementation of those steps to the children classes.

Wikipedia says
> In software engineering, the template method pattern is a behavioral design pattern that defines the program skeleton of an algorithm in an operation, deferring some steps to subclasses. It lets one redefine certain steps of an algorithm without changing the algorithm's structure.

**Programmatic Example**

Imagine we have a build tool that helps us test, lint, build, generate build reports (i.e. code coverage reports, linting report etc) and deploy our app on the test server.

First of all we have our base class that specifies the skeleton for the build algorithm
```vala
abstract class Builder {

    // Template method
    public void build()
    {
        this.test();
        this.lint();
        this.assemble();
        this.deploy();
    }

    public abstract void test();
    public abstract void lint();
    public abstract void assemble();
    public abstract void deploy();
}
```

Then we can have our implementations

```vala
class AndroidBuilder : Builder {
    public override void test()
    {
        print ("Running android tests\n");
    }

    public override void lint()
    {
        print ("Linting the android code\n");
    }

    public override void assemble()
    {
        print ("Assembling the android build\n");
    }

    public override void deploy()
    {
        print ("Deploying android build to server\n");
    }
}

class IosBuilder : Builder {
    public override void test()
    {
        print ("Running ios tests\n");
    }

    public override void lint()
    {
        print ("Linting the ios code\n");
    }

    public override void assemble()
    {
        print ("Assembling the ios build\n");
    }

    public override void deploy()
    {
        print ("Deploying ios build to server\n");
    }
}
```
And then it can be used as

```vala
var android_builder = new AndroidBuilder();
android_builder.build ();

// Output:
// Running android tests
// Linting the android code
// Assembling the android build
// Deploying android build to server

var ios_builder = new IosBuilder ();
ios_builder.build ();

// Output:
// Running ios tests
// Linting the ios code
// Assembling the ios build
// Deploying ios build to server
```

## 🚦 Wrap Up Folks

And that about wraps it up. I will continue to improve this, so you might want to watch/star this repository to revisit. Also, I have plans on writing the same about the architectural patterns, stay tuned for it.

## 👬 Contribution

- Report issues
- Open pull request with improvements
- Spread the word
- Reach out to me directly at kamranahmed.se@gmail.com or on twitter [@kamranahmedse](http://twitter.com/kamranahmedse)

## License
MIT © [Kamran Ahmed](http://kamranahmed.info)
