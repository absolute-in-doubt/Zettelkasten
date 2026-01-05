
03-01-2026 17:42

Status: #adult 

Tags: [[JDBC]] [[Java+]]

---
# Транзакции в JDBC


в JDBC транзакции управляются через `setAutoCommit(false)`, `commit()` и `rollback()`.



```java
public static void executeTransaction(Connection con) {
    try {
        //Switch to manual transaction mode by setting
        //autocommit to false. Note that this starts the first 
        //manual transaction.
        con.setAutoCommit(false);
        Statement stmt = con.createStatement();
        stmt.executeUpdate("INSERT INTO Production.ScrapReason(Name) VALUES('Wrong size')");
        stmt.executeUpdate("INSERT INTO Production.ScrapReason(Name) VALUES('Wrong color')");
        con.commit(); //This commits the transaction and starts a new one.
        stmt.close(); //This turns off the transaction.
        System.out.println("Transaction succeeded. Both records were written to the database.");
    }
    catch (SQLException ex) {
        ex.printStackTrace();
        try {
            System.out.println("Transaction failed.");
            con.rollback();
        }
        catch (SQLException se) {
            se.printStackTrace();
        }
    }
}
```



### Уровень изоляции транзакции

```java
connection.setTransactionIsolation(TRANSACTION_READ_UNCOMMITTED);
```


----
#### [[Транзакции в JDBC - Flashcards|Link to flashcards]]



---
### References:

