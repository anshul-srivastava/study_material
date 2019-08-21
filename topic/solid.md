## Single Responsibility Principle
The definition of Single Responsibility Principle sounds simple i.e. one class has one responsibility. But in reality, designs go in the other direction. Lets see an example to understand. Take a look at the Employee class below –
Employee.java

```Java
public class Employee{
  private String employeeId;
  private String name;
  private string address;
  private Date dateOfJoining;
  public boolean isPromotionDueThisYear(){
    //promotion logic implementation
  }
  public Double calcIncomeTaxForCurrentYear(){
    //income tax logic implementation
  }
  //Getters & Setters for all the private attributes
}
```

The above Employee class looks logically correct. It has all the employee attributes like employeeId, name, age, address & dateOfJoining. It even tells you if the employee is eligible for promotion this year and calculates the income tax he has to pay for the year.
However, Employee class breaks the Single Responsibility Principle. Lets see how –
* The logic of determining whether the employee is due this year is actually not a responsibility which the employee owns. The company’s HR department owns this responsibility based on the company’s HR policies which may change every few years. On any such change in HR policies, the Employee class will need to be updated as it is currently has the responsibility of promotion determination.
* Similarly, income tax calculation is not a responsibility of the Employee. It is the finance department’s responsibility which it takes care of the current tax structure which may get updated every year. If Employee class owns the income tax calculation responsibility then whenever tax structure/calculations change Employee class will need to be changed.
* Lastly, Employee class should have the single responsibility of maintaining core attributes of an employee.
Refactoring the Employee class so that it adheres to Single Responsibility Principle
Let us now refactor the Employee class to make it own a single responsibility.
Lets move the promotion determination logic from Employee class to the HRPromotions class like this –
HRPromotions.java
```Java
public class HRPromotions{
  public boolean isPromotionDueThisYear(Employee emp){
    //promotion logic implementation using the employee information passed
  }
}
```
Similarly, lets move the income tax calculation logic from Employee class to FinITCalculations class –
FinITCalculations.java
```java
public class FinITCalculations{
  public Double calcIncomeTaxForCurrentYear(Employee emp){
    //income tax logic implementation using the employee information passed
  }
}
```
Our Employee class now remains with a single responsibility of maintaining core employee attributes –
Employee.java adhering to Single Responsibility Principle
```java
public class Employee{
  private String employeeId;
  private String name;
  private string address;
  private Date dateOfJoining;
  //Getters & Setters for all the private attributes
}

```


## Open Closed Principle

* A class can be considered to be closed if its runtime or compiled class is available for use as a base class which can be extended by child classes. Baselining here refers to making sure that changes are guaranteed to not happen. In short – The said class is open for extension.
* A class can be considered to be open if its functionality can be enhanced by sub-classing it. When a class is a sub-class then by using the Liskov Substitution rule it can be replaced by its sub-class. This sub-class behaves as its parent class but is an enhanced version of it. In short – The said class is open for modification(via extension).
Example of Open/Closed Principle in Java
Lets say we need to calculate areas of various shapes. We start with creating a class for our first shape Rectangle which has 2 attributes length & width–
Rectangle.java
```java
public class Rectangle{
 public double length;
 public double width;
}
```
Next we create a class to calculate area of this Rectangle which has a method calculateRectangleArea()which takes the Rectangle as an input parameter and calculates its area –
AreaCalculator.java
```java
public class AreaCalculator{
  public double calculateRectangleArea(Rectangle rectangle){
    return rectangle.length *rectangle.width;
  }
}
```
So far so good. Now lets say we get our second shape circle. So we promptly create a new class Circle with a single attribute radius –
Circle.java
```java
public class Circle{
 public double radius;
}
```
Then we modify AreaCalculator class to add circle calculations through a new method calculateCircleArea() –
AreaCalculator.java
```java
public class AreaCalculator{
  public double calculateRectangleArea(Rectangle rectangle){
    return rectangle.length *rectangle.width;
  }
  public double calculateCircleArea(Circle circle){
    return (22/7)*circle.radius*circle.radius;
  }
}
```
However, note that there were flaws in the way we designed our solution above.
Lets say we have a new shape pentagon next. In that case we will again end up modifying AreaCalculatorclass. As the types of shapes grows this becomes messier as AreaCalculator keeps on changing and any consumers of this class will have to keep on updating their libraries which contain AreaCalculator. As a result, AreaCalculator class will not be baselined(finalized) with surety as every time a new shape comes it will be modified. So, this design is not closed for modification.

Also, note that this design is not extensible, i.e what if complicated shapes keep coming, AreaCalculator will need to keep on adding their computation logic in newer methods. We are not really expanding the scope of shapes; rather we are simply doing piece-meal(bit-by-bit) solution for every shape that is added.
Modification of above design to comply with Open/Closed Principle
Let us now see a more elegant design which solves the flaws in the above design by adhering to the Open/Closed Principle. We will first of all make the design extensible. For this we need to first define a base typeShape and have Circle & Rectangle implement Shape interface –
Shape.java
```java
public interface Shape{
  public double calculateArea();
}
 
public class Rectangle implements Shape{
  double length;
  double width;
  public double calculateArea(){
    return length * width;
  }
}
 
public class Circle implements Shape{
  public double radius;
  public double calculateArea(){
    return (22/7)*radius*radius;
  }
}
```
The design has now undergone the following important changes to become extensible in nature –
* There is a base interface Shape. All shapes now implement the base interface Shape
* Shape interface has an abstract method calculateArea(). Both circle & rectangle provide their own overridden implementation of calculateArea() method using their own attributes.
* We have brought-in a degree of extensibility as shapes are now an instance of Shape interfaces. This allows us to use Shape instead of individual classes wherever these classes are used by any consumer.
The last point above mentioned consumer of these shapes. In our case consumer will be the AreaCalculatorclass which would now look like this –
AreaCalculator.java
```java
public class AreaCalculator{
  public double calculateShapeArea(Shape shape){
    return shape.calculateArea();
  }
}
```
This AreaCalculator class now fully removes our design flaws noted above and gives a clean solution which adheres to the Open-Closed Principle.
The design is now correct as per Open Closed Principle due to the following reasons –
* The design is open for extension as more shapes can be added without modifying the existing code. We just need to create a new class for the new shape and implement the calculateArea() method with a formula specific to that new shape.
* This design is also closed for modification. AreaCalculator class is complete w.r.t area calculations. It now caters to all the shapes which exists now, as well as to those that may be created later.

