# Java Records

An example of [Java Records](https://docs.oracle.com/en/java/javase/18/language/records.html) using:
- The default constructor (which is part of the record declaration itself) 
  ```java
  public record Invoice(Calendar date, List<LineItem> lineItems){
  }
  ```
- A canonical constructor (otherwise known as a *parameterised constructor*)
  ```java
    public Invoice(Calendar date, LineItem... lineItems) {
        this(date, List.of(lineItems));
    }
  ```
- Record methods

### Note
Unless you need to perform some action or provide multiple construction patterns in the constructor there is no need to even declare them at all.

For example, suppose you have a `Student` record like below, the conanical constructor `Student(int id, String firstName, String lastName)` is already declared.
```java
public record Student(int id, String firstName, String lastName){
}
```
Furthermore, as records are final you cannot create an empty constructor (E.g. `Student student = new Student()`).


## Example Classes

### 1. RecordExample.java (the main class)
```java
package org.example;

import java.util.Calendar;
import java.util.List;

public class RecordExample {

    public static void main(String[] args) {

        //Create an invoice using the default constructor (Calendar, List<LineItem>)
        var invoice_1 = new Invoice(Calendar.getInstance(), List.of(
                 new LineItem("Hawaiian", 2, 5),
                 new LineItem("Hawaiian", 1,5),
                 new LineItem("Peperoni", 1,5),
                 new LineItem("Supreme", 2,5)
        ));

        //Create an invoice using the 2nd constructor (Calendar, LineItem...)
        var invoice_2 = new Invoice(Calendar.getInstance(),
                new LineItem("Item 1", 2, 4.50),
                new LineItem("Item 2", 5, 1.20));

        //Print the invoice data
        printInvoice(invoice_1, invoice_2);
    }

    static void printInvoice(Invoice... invoices){
        for(var inv: invoices){
            System.out.printf("""
                        ==== INVOICE ====
                        Subtotal: $%.2f
                        GST:      $%.2f
                        Total:    $%.2f
                        Items:    %d
                        
                        """,
                    inv.subTotal(), inv.gst(), inv.total(), inv.itemCount());
        }

    }
}
```
### 2. Invoice.java
```java
package org.example;

import java.util.Calendar;
import java.util.List;


public record Invoice(Calendar date, List<LineItem> lineItems) {

    public Invoice(Calendar date, LineItem... lineItems) {
        this(date, List.of(lineItems));
    }

    public double subTotal() {
        return lineItems.stream()
                .mapToDouble(item -> item.price() * item.qty())
                .sum();
    }

    public double gst(){
        return subTotal() * .1;
    }

    public double total(){
        return subTotal() + gst();
    }

    public int itemCount(){
        return (int) lineItems.stream()
                .mapToDouble(LineItem::qty)
                .sum();
    }
}
```
### 3. LineItem.java
```java
package org.example;

public record LineItem(String description, double qty, double price) {
}
```
