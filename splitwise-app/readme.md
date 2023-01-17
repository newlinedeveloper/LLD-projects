A low-level design for an Expense Sharing App like Splitwise using Java could include the following components:

User: This class represents a user of the app and would contain information such as name, email, and a list of their expenses.

Expense: This class represents an expense and would contain information such as the amount, description, and a list of the users involved in the expense.

Group: This class represents a group of users and would contain information such as the group name, a list of the users in the group, and a list of expenses for the group.

ExpenseService: This class would handle the logic for adding and editing expenses, calculating the amounts owed by each user, and updating the balances for each user.

GroupService: This class would handle the logic for creating and managing groups, including adding and removing users from a group.

UserService: This class would handle the logic for creating and managing users, including adding expenses to a user's account and updating their balance.

Database: This class would handle the connection to the database and provide methods for storing and retrieving data such as users, groups, and expenses.

GUI/UI: This class would handle the user interface and provide methods for displaying the data to the user and accepting user input.

Here is an example of a possible class structure:

```

public class User {
    private String name;
    private String email;
    private List<Expense> expenses;
    private float balance;

    public User(String name, String email) {
        this.name = name;
        this.email = email;
        expenses = new ArrayList<>();
    }

    public void addExpense(Expense expense) {
        expenses.add(expense);
        balance += expense.getAmount() / expense.getUsers().size();
    }

    public void updateBalance(float amount) {
        balance += amount;
    }

    public String getName() {
        return name;
    }

    public String getEmail() {
        return email;
    }

    public List<Expense> getExpenses() {
        return expenses;
    }

    public float getBalance() {
        return balance;
    }
}


public class Expense {
    private float amount;
    private String description;
    private List<User> users;

    public Expense(float amount, String description, List<User> users) {
        this.amount = amount;
        this.description = description;
        this.users = users;
    }

    public float getAmount() {
        return amount;
    }

    public String getDescription() {
        return description;
    }

    public List<User> getUsers() {
        return users;
    }
}

public class Group {
    private String name;
    private List<User> users;
    private List<Expense> expenses;

    public Group(String name, List<User> users) {
        this.name = name;
        this.users = users;
        expenses = new ArrayList<>();
    }

    public void addExpense(Expense expense) {
        expenses.add(expense);
    }

    public void addUser(User user) {
        users.add(user);
    }

    public void removeUser(User user) {
        users.remove(user);
    }

    public String getName() {
        return name;
    }

    public List<User> getUsers() {
        return users;
    }

    public List<Expense> getExpenses() {
        return expenses;
    }
}

public class ExpenseService {
    public void addExpense(Expense expense) {
        for (User user : expense.getUsers()) {
            user.addExpense(expense);
        }
    }

    public void editExpense(Expense expense, float newAmount) {
        for (User user : expense.getUsers()) {
            user.updateBalance(newAmount - expense.getAmount());
            expense.setAmount(newAmount);
        }
    }

    public Map<User, Float> calculateBalances(Group group) {
        Map<User, Float> balances = new HashMap<>();
        for (User user : group.getUsers()) {
            float balance = 0;
            for (Expense expense : group.getExpenses()) {
                if (expense.getUsers().contains(user)) {
                    balance += expense.getAmount() / expense.getUsers().size();
                }
            }
            balances.put(user, balance);
        }
        return balances;
    }

    public Map<User, Float> calculateOwes(Group group) {
        Map<User, Float> balances = calculateBalances(group);
        Map<User, Float> owes = new HashMap<>();
        float totalBalance = 0;
        for (Float balance : balances.values()) {
            totalBalance += balance;
        }
        float averageBalance = totalBalance / group.getUsers().size();
        for (Map.Entry<User, Float> entry : balances.entrySet()) {
            owes.put(entry.getKey(), averageBalance - entry.getValue());
        }
        return owes;
    }
}

public class GroupService {
    public void createGroup(String name, List<User> users) {
        Group group = new Group(name, users);
    }

    public void addExpense(Group group, Expense expense) {
        group.addExpense(expense);
        expenseService.addExpense(expense);
    }

    public void addUser(Group group, User user) {
        group.addUser(user);
    }

    public void removeUser(Group group, User user) {
        group.removeUser(user);
    }
}


```


Here are some examples of test cases and sample inputs that you could use to test your Expense Sharing App like Splitwise using Java code:

1. Test case: Create a new group and add users to it

```
    // Create a new group
    GroupService groupService = new GroupService();
    groupService.createGroup("Weekend Trip", Arrays.asList(new User("John"), new User("Sarah"), new User("Mike")));

    // Get the created group
    Group group = groupService.getGroup("Weekend Trip");

    // Assert that the group is created with the correct name and users
    assertEquals("Weekend Trip", group.getName());
    assertEquals(3, group.getUsers().size());
    assertTrue(group.getUsers().contains(new User("John")));
    assertTrue(group.getUsers().contains(new User("Sarah")));
    assertTrue(group.getUsers().contains(new User("Mike")));


```

2 . Test case: Add an expense to a group

```
    // Create a new expense
    Expense expense = new Expense(100, "Hotel stay", Arrays.asList(new User("John"), new User("Sarah")));

    // Add the expense to the group
    groupService.addExpense(group, expense);

    // Assert that the expense is added to the group
    assertEquals(1, group.getExpenses().size());
    assertEquals(expense, group.getExpenses().get(0));


```

3. Test case: Remove a user from a group

```
    // Remove a user from the group
    groupService.removeUser(group, new User("Mike"));

    // Assert that the user is removed from the group
    assertEquals(2, group.getUsers().size());
    assertFalse(group.getUsers().contains(new User("Mike")));


```

4. Test case: Calculate the balance for each user in a group

```
    // Calculate the balance for each user in the group
    ExpenseService expenseService = new ExpenseService();
    Map<User, Float> balances = expenseService.calculateBalances(group);

    // Assert that the balances are calculated correctly
    assertEquals(50f, balances.get(new User("John")), 0.01);
    assertEquals(50f, balances.get(new User("Sarah")), 0.01);


```

5. Test case: Calculate the amount each user owes in a group

```
    // Calculate the amount each user owes in the group
    Map<User, Float> owes = expenseService.calculateOwes(group);

    // Assert that the owes are calculated correctly
    assertEquals(0f, owes.get(new User("John")), 0.01);
    assertEquals(0f, owes.get(new User("Sarah")), 0.01);


```

6. Test case: Edit an expense

```
    // Edit the expense
    expenseService.editExpense(expense, 150);

    // Assert that the expense is edited correctly
    assertEquals(150, expense.getAmount(), 0.01);


```


