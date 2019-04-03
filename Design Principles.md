# Design Principles

---

## SOLID

*An acronym representing object oriented programming and how to do it right.*

### S - Single Responsibility Principle

*A class should have one, and only one, reason to change.*

This idea is based on the fact that if a change must be implemented within the code, then the class will need to change. If a class is responsible for multiple areas, then it is too complicated and this large change could have a large, negative effect on the codebase.

An example could be if a class was creating a shape, say a rectangle. If this rectangle did both the colour and the shape within one class, this would be breaking the SRP. It would be better then to do two different classes for each, calling the shape part (as a constructor) when adding colour in the colour class. 

Problem:

```c#
/// <summary>
/// Class calculates the area and can also draw it on a windows form object.
/// </summary>
public class RectangleShape
    {
        public int Height{ get; set; }
        public int Width { get; set; }
 
        public int Area()
        {
            return Width * Height;
        }
 
        public void Draw(Form form)
        {
            SolidBrush myBrush = new SolidBrush(System.Drawing.Color.Red);
            Graphics formGraphics = form.CreateGraphics();
            formGraphics.FillRectangle(myBrush, new Rectangle(0, 0, Width, Height);
        }
    }

//--------------------------------------------------------------//
                                       
public class GeometricsCalculator
    {
        public void CalculateArea(RectangleShape rectangleShape)
        {
            int area = rectangleShape.Area();
        }
    }   

public class GraphicsManager
    {
        public Form form {get;set;}

        public void DrawOnScreen(RectangleShape rectangleShape)
        {
            rectangleShape.Draw(form);
        }
    }                                       
```

Solution:

```c#
/// <summary>
/// Class calculates the rectangle's area.
/// </summary>
    public class RectangleShape
    {
        public int Height { get; set; }
        public int Width { get; set; }

        public int Area()
        {
            return Width * Height;
        }
    }

/// <summary>
/// Class draws a rectangle on a windows form object.
/// </summary>
    public class RectangleDraw
    {
        public void Draw(Form form, RectangleShape rectangleShape)
        {
            SolidBrush myBrush = new SolidBrush(System.Drawing.Color.Red);
            Graphics formGraphics = form.CreateGraphics();
            formGraphics.FillRectangle(myBrush, 
            new Rectangle(0, 0, rectangleShape.Width,rectangleShape.Height));
        }
    }

//--------------------------------------------------------------//

/// <summary>
/// Consumes the RectangleShape */
/// </summary>
    public class GeometricsCalculator
    {
        public void CalculateArea(RectangleShape rectangleShape)
        {
            int area = rectangleShape.Area();
        }
    }

/// <summary>
/// Consumes the RectangleDraw and RectangleShape */
/// </summary>
    public class GraphicsManager
    {
        public Form form { get; set; }
        
        public void DrawOnScreen(RectangleDraw rectangleDraw, RectangleShape rectangleShape)
        {
            rectangleDraw.Draw(form, rectangleShape);
        }
    }
```





### O  - Open-Closed Principle

*You should be able to extend a classes behavior, without modifying it.*

To fulfill the requirement of being both open and closed, the two criteria must be satisfied:

- **Open** for extension

  This means the class must be open to change. As requirements change, the class should behave as we wish it to, to meet these new requirement needs.

- **Closed** for modification

  The source code for this class is set in stone, no person can modify the code.

*How can this be done?* -- Through abstractions!

We must be able to extend the behavior of a class without changing a single line of code, by making abstractions. A good example would be if we had different shape classes, i.e rectangle, square etc. To make the O in SOLID we must implement a **shape** interface class, this way whenever a dependency injection occurs, the interface can be changed instead of the lower level classes. We can add new shapes without changing any of the dependencies.

### L - Liskov Substitution Principle

*Derived classes must be substitutable for their base classes*

For example a rectangle class should not be derived from a square class, or vice versa. This is because although they are variation of the same shape, they have shared methods which are mathematically different. Changing the height of a square will change the width to equal, whereas the rectangle would only change one, as they must be different.

*The main point is that we want the methods to behave the same for each derived class, not differently.*

The solution is to design by contract. There should be pre/post conditions defined for each method, where the pre-condition must hold true in order for a method to execute, and the post must hold true after the execution.

**When redefining a routine, the precondition must only become weaker, and the post stronger:**

1. Assume your baseclass works with a member int. Now your subtype requires that int to be positive. This is strengthened pre-conditions, and now any code that worked perfectly fine before with negative ints is broken.
2. Likewise, assume the same scenario, but the base class used to guarantee that the member would be positive after being called.  Then the subtype changes the behavior to allow negative ints. Code that works on the object (and assumes that the post-condition is a  positive int) is now broken since the post-condition is not upheld. 

### I  -  Interface Segregation Principle

*Make fine grained interfaces that are client specific.*

A client mustn't be forced to implement interfaces which they do not use. Or it is better to have more smaller interfaces, rather than fewer fatter ones.

Take an example interface called *Animal*, containing *eat*, *sleep* and *walk* methods. This would be a monolithic interface due to the fact that some animals say. can fly. To break this interface down into bitesize chunks, we could see interfaces based on role, rather than the huge animal. I.e CanEat, CanSleep and CanWalk interfaces can be used by all types of animal species meaning it would not be characterized by simply animal.

**By breaking down interfaces, we favor composition instead of inheritance by separating the roles, and decoupling over coupling by not coupling derivative classes with unneeded responsibilities inside a huge interface.**

### D  -  Dependency Inversion Principle

*Depend on abstractions, not on concretions.*

> A. High level modules should not depend upon low level modules. Both should depend upon abstractions.

> B. Abstractions should not depend upon details. Details should depend upon abstractions.

As an example, say we have a system of authentication which goes through different services like Google and Github. Each singular service would have its own class. Now say a user must be authenticated in our system. To make use of all services there are two options:

* The hack job

  **Write code which adapts each service to the authentication process. **

  If for example a new authentication service is added to the system, then this will violate the OCP rule above as the code will need to change for the original system.

* The correct way

  **Define an abstraction of the authentication services.**

  By designing an interface for the authentication service, and implementing this for each one, a dependency injection could be used in the authentication logic.This could result in clean code looking something like this;

  ```java
  authenticate(AuthenticationService authenticationService);
  // Then each service could be individually authenticated:
  authenticate(new GoogleAuthenticationService); 
  // Or
  authenticate(new GitHubAuthenticationService); 
  // etc
  ```

  This avoids having to integrate each service separately, meaning we simply generalize the authentication logic.