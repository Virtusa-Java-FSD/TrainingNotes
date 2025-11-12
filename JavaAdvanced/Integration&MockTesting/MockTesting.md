

##  **What is Mockito?**

###  **Definition**

Mockito is a **mocking framework** for Java that allows you to:

| Capability          | Purpose                          |
| ------------------- | -------------------------------- |
| Mock objects        | Replace real dependencies        |
| Stub methods        | Predefine return values/behavior |
| Verify interactions | Check method calls, arguments    |
| Capture arguments   | Inspect real parameters passed   |
For example, to isolate business logic from actual DB interactions and ensure fast, unit-level tests.
(Integration with real DB is handled separately using Testcontainers — not here.)
---

##  **JDBC Architecture Context**

In a layered JDBC project:

| Layer      | Example          | Test Strategy     |
| ---------- | ---------------- | ----------------- |
| Controller | REST endpoint    | Mock Service      |
| Service    | Business logic   | Mock DAO          |
| DAO        | SQL + JDBC calls | Mock JDBC classes |

---

##  **Sample Project Structure**

```
src/main/java
 └── com.example.jdbc
      ├── model
      │     └── Employee.java
      ├── dao
      │     └── EmployeeDAO.java
      ├── service
      │     └── EmployeeService.java
      └── util
            └── DBConnection.java
```

---

##  **Scenario**

Test `EmployeeService` logic without calling real DB.
DAO will be **mocked**, not DB.

### **Employee Model**

```java
public class Employee {
    private int id;
    private String name;
    private double salary;

    // Constructors, getters, setters
}
```

### **EmployeeDAO**

```java
public interface EmployeeDAO {
    Employee findById(int id);
    void save(Employee employee);
}
```

### **Service Layer**

```java
public class EmployeeService {

    private final EmployeeDAO employeeDAO;

    public EmployeeService(EmployeeDAO employeeDAO) {
        this.employeeDAO = employeeDAO;
    }

    public double getAnnualSalary(int id) {
        Employee emp = employeeDAO.findById(id);
        return emp.getSalary() * 12;
    }

    public void registerEmployee(Employee emp) {
        if(emp.getSalary() < 0) {
            throw new IllegalArgumentException("Salary cannot be negative");
        }
        employeeDAO.save(emp);
    }
}
```

---

## **Mockito Setup (JUnit 5)**

Add dependency (Maven):

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-core</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>
```

(Optional JUnit-Mockito integration)

```xml
<dependency>
    <groupId>org.mockito</groupId>
    <artifactId>mockito-junit-jupiter</artifactId>
    <version>5.10.0</version>
    <scope>test</scope>
</dependency>
```

---

## **Mockito Test for Service Layer**

### **Mock DAO, Stub Behavior**

```java
@ExtendWith(MockitoExtension.class)
class EmployeeServiceTest {

    @Mock
    private EmployeeDAO employeeDAO;

    @InjectMocks
    private EmployeeService employeeService;

    @Test
    void testAnnualSalaryCalculation() {
        Employee emp = new Employee(1, "Aru", 50000.0);

        Mockito.when(employeeDAO.findById(1))
               .thenReturn(emp);

        double annual = employeeService.getAnnualSalary(1);

        assertEquals(600000.0, annual);
        Mockito.verify(employeeDAO).findById(1);
    }
}
```

###  Key Concepts Learned

* Mock dependency
* Stub return values
* Verify method invocation

---

##  Testing Validation Logic

```java
@Test
void testRegisterEmployeeNegativeSalary() {
    Employee emp = new Employee(2, "Guru", -100);

    assertThrows(IllegalArgumentException.class,
        () -> employeeService.registerEmployee(emp)
    );

    Mockito.verify(employeeDAO, Mockito.never()).save(emp);
}
```

Shows **negative case testing + verification**.

---

##  **Mockito for DAO Layer: Mock JDBC Objects**

Even DAO methods can be unit-tested with mocks.

### DAO Implementation (to test)

```java
public class EmployeeDAOImpl implements EmployeeDAO {

    private DataSource dataSource;

    public EmployeeDAOImpl(DataSource dataSource) {
        this.dataSource = dataSource;
    }

    @Override
    public Employee findById(int id) {
        try (Connection con = dataSource.getConnection();
             PreparedStatement ps = con.prepareStatement("SELECT * FROM employee WHERE id = ?")) {

            ps.setInt(1, id);
            ResultSet rs = ps.executeQuery();

            if (rs.next()) {
                return new Employee(rs.getInt("id"), 
                                    rs.getString("name"), 
                                    rs.getDouble("salary"));
            }
        } catch (SQLException e) {
            throw new RuntimeException(e);
        }

        return null;
    }
}
```

---

### DAO Test with Mockito

```java
class EmployeeDAOImplTest {

    @Mock DataSource dataSource;
    @Mock Connection connection;
    @Mock PreparedStatement ps;
    @Mock ResultSet rs;

    private EmployeeDAOImpl employeeDAO;

    @BeforeEach
    void setup() throws Exception {
        MockitoAnnotations.openMocks(this);

        Mockito.when(dataSource.getConnection()).thenReturn(connection);
        Mockito.when(connection.prepareStatement(Mockito.anyString()))
               .thenReturn(ps);
        employeeDAO = new EmployeeDAOImpl(dataSource);
    }

    @Test
    void testFindById() throws Exception {
        Mockito.when(ps.executeQuery()).thenReturn(rs);
        Mockito.when(rs.next()).thenReturn(true);
        Mockito.when(rs.getInt("id")).thenReturn(1);
        Mockito.when(rs.getString("name")).thenReturn("Aru");
        Mockito.when(rs.getDouble("salary")).thenReturn(50000.0);

        Employee emp = employeeDAO.findById(1);

        assertNotNull(emp);
        assertEquals("Aru", emp.getName());
        Mockito.verify(ps).setInt(1, 1);
        Mockito.verify(ps).executeQuery();
    }
}
```

---

##  When To Mock vs Not Mock in JDBC Projects

| Situation                 | Approach                                            |
| ------------------------- | --------------------------------------------------- |
| Service/business logic    | **Mock DAO**                                        |
| DAO method logic          | Mock `Connection`, `PreparedStatement`, `ResultSet` |
| End-to-end DB interaction | **Use Testcontainers + real DB** (no mocks)         |
| External REST APIs        | Mock/stub HTTP client                               |

---

## Best Practices

| Good                        | Avoid                             |
| --------------------------- | --------------------------------- |
| Mock interfaces/services    | Mocking business logic itself     |
| Mock DB for unit tests      | Using mocks for integration tests |
| Verify behavior & arguments | Over-stubbing (unmaintainable)    |
| Test negative & edge cases  | Using real DB in unit tests       |

---

##  Common Mockito Methods Cheat-Sheet

| Purpose            | Syntax                            |
| ------------------ | --------------------------------- |
| Create mock        | `@Mock` or `Mockito.mock()`       |
| Stub behavior      | `when(...).thenReturn()`          |
| Exception stub     | `thenThrow()`                     |
| Verify method call | `verify(mock)`                    |
| Match any input    | `Mockito.anyInt()`, `anyString()` |
| Never called       | `verify(..., never())`            |
| Capture args       | `ArgumentCaptor`                  |

---

##  Conclusion

Mockito helps test **logic, flow, and decision making** without actual DB.
For real DB behavior, combine with **Testcontainers** in integration tests.

---
