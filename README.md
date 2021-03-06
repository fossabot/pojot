Pojot  
=====
Provides test coverage of methods such as basic getters and setters, equals and hashcode testing, and enums.

[![Circle CI](https://circleci.com/gh/danhagberg/pojot.svg?style=svg&circle-token=e554fafca069e81e8ce2c9c2ff2fa2bfce82d1b3)](https://circleci.com/gh/danhagberg/pojot) [![Download](https://api.bintray.com/packages/danhagberg/maven/pojot/images/download.svg) [![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fdanhagberg%2Fpojot.svg?type=shield)](https://app.fossa.io/projects/git%2Bgithub.com%2Fdanhagberg%2Fpojot?ref=badge_shield)
](https://bintray.com/danhagberg/maven/pojot/_latestVersion)

[Javadocs](http://digitaltsunami.net/projects/pojot/site/apidocs/index.html)

Features
--------
* Tests getters/setters using range of values as applicable for type: min, max, null.
* Tests equals and hashCode methods given a set of properties.  
* Tests toString method to ensure that default values do not raise an exception. 
* Provides code coverage for Enum methods provided by language: toString and valueOf

Assumptions
------------
* Getters 
    Methods being tested must not contain any special logic and must return the value for the property without modification.
* Setters 
    Methods being tested must not contain any special logic and must set the value for the property without modification.
* Equals/Hashcode 
    If being tested, each uses the same properties in calculating the values.

Usage
-----
Basic usage is to create an instance of TestAid for the class under test and invoke the validate method.
This will return a set of 0 to many errors found during validation, with each error string providing
 information on the test that failed.
 
``` java
List<String> errors = new TestAid<>(Customer.class).validate();
```

### Filters
The default behavior is to in include all getters and setters for testing.  If not all 
getters and/or setters should be tested, they can be excluded at the property or method level. 
The filter can be inclusion or exclusion depending on what is the smaller set.  

Examples
--------
_Note: Tests below are shown with JUnit, but there is no dependency on any test framework._

### Basic Test
This will test all non-private getters and setters, run hash code and equals tests with no fields, and invoke the toString method. 
Any errors are simply printed out with List.toString.  
``` java
@Test
public void testBasicMethodsForClassUnderTest() throws IntrospectionException {
    TestAid<ClassUnderTest> ta = new TestAid<>(ClassUnderTest.class);
    List<String> errors = ta.validate();
    assertTrue(errors.toString(), errors.isEmpty());
}
```

### Providing fields for equals/hashcode testing. 
Providing fields for equality testing will ensure that the equality changes as the field values change.  
There are currently three ways to provide fields names: include all, include a set, or exclude a set.  
If field names are not provided using one of those methods, then the equality
testing will be assumed to use the default methods. 

*Note that final fields will not be included as they cannot be changed during testing.*
#### Include a subset of fields
``` java
@Test
public void testBasicOrderMethods() throws IntrospectionException {
    TestAid<Order> ta = new TestAid<>(Order.class);
    ta.includeInEquals( "customerId", "orderNumber");
    List<String> errors = ta.validate();
    assertTrue(errors.toString(), errors.isEmpty());
}
```
#### Include all fields
``` java
@Test
public void testBasicOrderMethods() throws IntrospectionException {
    TestAid<Order> ta = new TestAid<>(Order.class);
    ta.includeAllInEquals();
    List<String> errors = ta.validate();
    assertTrue(errors.toString(), errors.isEmpty());
}
```
##### Exclude a subset of fields. All others will be included.
``` java
@Test
public void testBasicOrderMethods() throws IntrospectionException {
    TestAid<Order> ta = new TestAid<>(Order.class);
    ta.excludeFromEquals( "customerComments");
    List<String> errors = ta.validate();
    assertTrue(errors.toString(), errors.isEmpty());
}
```
### Excluding a field from testing.
If a property has specialized processing, it can be excluded from getter/setter testing. 
This example also shows the fluent style of invocation.
``` java
@Test
public void testBasicCustomerMethods() throws IntrospectionException {
    List<String> errors = new TestAid<>(Customer.class)
            .includeInEquals( "firstName", "lastName")
            .excludeFields("age")
            .validate();
    assertTrue(errors.toString(), errors.isEmpty());
}
```
### Including only a subset a fields for testing.
If a property has specialized processing, it can be excluded from getter/setter testing. 
``` java
@Test
public void testBasicCustomerMethods() throws IntrospectionException {
    List<String> errors = new TestAid<>(Customer.class)
            .includeFields("firstName", "lastName", "suffix")
            .validate();
    assertTrue(errors.toString(), errors.isEmpty());
}
```
### Excluding a setter from testing.
If a getter or setter has specialized processing, then that method for the property can be excluded. 
Note that the property name is passed in and not the setter name. 
``` java
@Test
public void testBasicCustomerMethods() throws IntrospectionException {
    List<String> errors = new TestAid<>(Customer.class)
            .excludeSetters("weight", "height" )
            .validate();
    assertTrue(errors.toString(), errors.isEmpty());
}
```
### Including fluent or builder type setters.
Default processing does not include fluent or builder style setters for the class under test. 
These setters return an instance of the class to allow the setters to chained.  Normal bean 
processing does not return these as setters; therefore, they will be skipped.
Fluent style setters can be enabled using TestAid
``` java
@Test
public void testSettersUseFluentStyle() throws IntrospectionException {
    List<String> errors = new TestAid(FluentSetterClass.class)
            .setClassUsesFluentSetters(true)
            .validate();
    assertTrue(errors.toString(), errors.isEmpty());
}
```

### Provide code coverage for basic enum
The EnumExerciser will return the number of values that it covered.  
``` java
@Test
public void testExercise() throws Exception {
    assertTrue("Failed to exercise enum", EnumExerciser.exercise(EnumUnderTest.class) > 0L);
}
```


## License
[![FOSSA Status](https://app.fossa.io/api/projects/git%2Bgithub.com%2Fdanhagberg%2Fpojot.svg?type=large)](https://app.fossa.io/projects/git%2Bgithub.com%2Fdanhagberg%2Fpojot?ref=badge_large)