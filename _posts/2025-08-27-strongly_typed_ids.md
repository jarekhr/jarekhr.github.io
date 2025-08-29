---
title: Strongly-typed IDs
date: 2025-08-27 21:10:15 +0200
categories: [Software, Design]
tags: [programming, java, reliability, type-safety, immutability]     # TAG names should always be lowercase
comments: on
---

# Use strongly typed IDs to ensure more type-safety

When working on various apps we often use identifiers for some objects/entities. Example: customer ID, book ID, transaction ID, etc.
These entities usually end up being stored in some sort of database and our application is either creating them, modifying or deleting. 
When writing code to handle these operation we need to be able to manipulate IDs in a safe and predictable way. 


## Employees in a department, how to get lost quickly

Let imagine we're writing a simple app where we have `Employees` assigned to some `Departments`. And let's assume both are stored in a relational
database in simple tables. Their IDs will be stored as numbers, therefore we will use numeric ID representation in Java.

```java
class Employee {
    private final Long id;
    private final String firstName;
    private final String lastName;
    // details to  follow
}
```
And a department:
```java
class Department {
    private final Long id;
    private final String name;
    // some other details
}
```

As you can see we used `Long` to represent both IDs. In terms of Java it is "strongly typed" on the surface, because we're not using super-generic type like `Object` to represent our IDs.
However, in case of our application we have a problem. Employee ID is something completely different from Department ID, yet **we modelled both** as a simple Java `Long`s.

### Let's add some business logic... 

We need to specify a contract to be used by our external clients. Clients need to have a way of assigning Employees to Departments.

```java
public interface EmployeeHandlingService {

  /**
   * Assigns employee to a department for a given number of days.
   */
  Assignment assignEmployee(Long departmentId, Long employeeId, Long numberOfDays);
}

```

> We used three parameters, each representing something completely different, but each of them being simple `Long`
{: .prompt-warning }

Our newly added interface looks good, but we can't count on compiler to help us out with finding problems like the one below:

```java

class EmployeeOrganizer {

  private final EmployeeHandlingService employeeService;

  public void importantLogic(Long days) {
    // let's assign employee to a department
    Long empId = 1234L;
    Long deptId = 9999L;

    employeeService.assignEmployee(empId, deptId, days);
  }
  
  // some other, important code
  
}

```

> We mixed parameters to `assignEmployee(Long, Long, Long)` method!
{: .prompt-danger }

Unfortunately, we got the order of parameters to method `assignEmployee(...)` wrong, we passed `empId` where `departmentId` was expected, and `deptId` when `employeeId` was expected. 
Such programming errors can easily happen, we need to have a predictable way of preventing them. 

Code above would result in **runtime errors**, or even worse, in unpredictable behavior of the application (as some ids would match existing data, some wouldn't)

## Introducing strongly typed IDs

As we've seen, method with three parameters of the same type creates a field for human errors.
> Employ compiler to help you prevent errors by tracking (**strong**) ID types 
{: .prompt-tip }

Let's go with much safer approach, each ID get its own type:

```java

class EmployeeId {
    private final Long id;
    
    private EmployeeId(Long value) {
        this.id = Objects.requireNonNull(value);
    }
    
    public Long getValue() {
        return value;
    }
    
    public EmployeeId of(Long value) {
        return new EmployeeId(value);
    }
}
```

... and then `DepartmentId`:
```java

class DepartmentId {
    private final Long id;
    
    private DepartmentId(Long value) {
        this.id = Objects.requireNonNull(value);
    }
    
    public Long getValue() {
        return value;
    }
    
    public DepartmentId of(Long value) {
        return new DepartmentId(value);
    }
}
```

We can also respectively modify our model classes for both `Employee` and `Department`. Improved `Employee` class:
```java
class Employee {
    private final EmployeeId id;
    private final String firstName;
    private final String lastName;
    // details to  follow
}
```
`Department` class needs to be modified the same way (skipping for brevity)


### Better interface
Our modified client interface looks like this now:

```java
public interface EmployeeHandlingService {

  /**
   * Assigns employee to a department for a given number of days.
   */
  Assignment assignEmployee(DepartmentId departmentId, EmployeeId employeeId, Long numberOfDays);
}

```

> Type-safe, easier to read and less error-prone!
{: .prompt-info }

Our clients will get compilation error (type error) when attempting to write code like this:

```java
  public void importantLogic(Long days) {
    // let's assign employee to a department 
    EmployeeId empId = EmployeeId.of(1234L);
    DepartmentId deptId = DepartmentId.of(9999L);

    employeeService.assignEmployee(empId, deptId, days); // compilation error! Incorrect types used!
  }

```
We just used compiler to validate code for us! Type checking mechanism of Java spotted programmer errors and saved us from potential costly prod issues!

## Conclusions

* We've employed Java compiler to check for programmer errors. Using separate types for things which semantically are different even though could be represented by same Java types is a powerful strategy to develop reliable software
  * Does not matter if your id is a `Long`, `String` or `UUID`. Wrap it in its own class so it becomes a different type and utilize power of compiler's type checker to ensure your code is correct. 
* Separate class for each ID in your system may seem like a lot of boilerplate and overhead, but is definitely worth it in the long run.
  * Consider using tools like [`immutables`](https://immutables.github.io/) library to generate some of that code for you

### Limitations

What if the language you use does not support compile-time checks? What about `python` or `JavaScript`?
* `python` introduced type hints which allow to introduce some type checks into your scripts. Check out [`mypy`](https://github.com/python/mypy)
* when working with `JavaScipt` try using `TypeScript` and produce `js` code out of it.
