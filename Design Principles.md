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





### O — Open-Closed Principle

*You should be able to extend a classes behavior, without modifying it.*

To fulfill the requirement of being both open and closed, the two criteria must be satisfied:

- **Open** for extension

  This means the class must be open to change. As requirements change, the class should behave as we wish it to, to meet these new requirement needs.

- **Closed** for modification

  The source code for this class is set in stone, no person can modify the code.

