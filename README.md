<h1 align="center">Bank App</h1>

<p align="center">
  A simple online banking web application built with <b>Spring Boot</b>, <b>Spring Security</b>, <b>Spring Data JPA (Hibernate)</b> and <b>MySQL</b>.
</p>

<p>
  Users can register, log in, deposit, withdraw, transfer money to other users and view their transaction history.
  This project was built for learning how a Spring Boot application connects the web layer, business logic and a database.
</p>

<hr>

<h2>Features</h2>
<ul>
  <li>User registration with BCrypt-hashed passwords</li>
  <li>Login and logout using Spring Security (form login)</li>
  <li>Dashboard showing the current balance</li>
  <li>Deposit money</li>
  <li>Withdraw money (with insufficient funds check)</li>
  <li>Transfer money to another user (runs inside a database transaction)</li>
  <li>Transaction history for each account</li>
</ul>

<h2>Tech Stack</h2>
<table>
  <thead>
    <tr><th>Layer</th><th>Technology</th></tr>
  </thead>
  <tbody>
    <tr><td>Language</td><td>Java</td></tr>
    <tr><td>Framework</td><td>Spring Boot</td></tr>
    <tr><td>Security</td><td>Spring Security (BCrypt password encoding)</td></tr>
    <tr><td>Persistence</td><td>Spring Data JPA, Hibernate</td></tr>
    <tr><td>Database</td><td>MySQL 8</td></tr>
    <tr><td>Frontend</td><td>HTML templates (Thymeleaf), Bootstrap</td></tr>
    <tr><td>Build tool</td><td>Maven</td></tr>
  </tbody>
</table>

<h2>Project Structure</h2>
<pre>
com.example.bankapp
├── BankappApplication.java        # Application entry point
├── config
│   └── SecurityConfig.java        # Security rules, login/logout, password encoder
├── controller
│   └── BankController.java        # Handles web requests
├── model
│   ├── Account.java               # Account entity (also Spring Security UserDetails)
│   └── Transaction.java           # Transaction entity
├── repository
│   ├── AccountRepository.java     # Database access for accounts
│   └── TransactionRepository.java # Database access for transactions
└── service
    └── AccountService.java        # Business logic (register, deposit, withdraw, transfer)
</pre>

<h2>How It Works</h2>
<pre>
Browser (HTML form)
   ↓  HTTP request
BankController        receives the request
   ↓
AccountService        business logic
   ↓
Repository            AccountRepository / TransactionRepository
   ↓  Hibernate generates SQL
MySQL                 tables: account, transaction
</pre>
<ol>
  <li>The user submits a form. The form field <code>name</code> attributes match the <code>@RequestParam</code> names in <code>BankController</code>.</li>
  <li>The controller calls <code>AccountService</code>.</li>
  <li>The service applies the business rules and calls the repositories.</li>
  <li>Spring Data JPA and Hibernate turn those calls into SQL and run them on MySQL.</li>
</ol>

<h2>Database Tables</h2>
<p>Tables are created automatically by Hibernate (<code>spring.jpa.hibernate.ddl-auto=update</code>).</p>

<h3>account</h3>
<table>
  <thead>
    <tr><th>Column</th><th>Type</th><th>Description</th></tr>
  </thead>
  <tbody>
    <tr><td>id</td><td>BIGINT (PK, auto increment)</td><td>Account ID</td></tr>
    <tr><td>username</td><td>VARCHAR</td><td>Unique login name</td></tr>
    <tr><td>password</td><td>VARCHAR</td><td>BCrypt hash of the password</td></tr>
    <tr><td>balance</td><td>DECIMAL</td><td>Current balance</td></tr>
  </tbody>
</table>

<h3>transaction</h3>
<table>
  <thead>
    <tr><th>Column</th><th>Type</th><th>Description</th></tr>
  </thead>
  <tbody>
    <tr><td>id</td><td>BIGINT (PK, auto increment)</td><td>Transaction ID</td></tr>
    <tr><td>amount</td><td>DECIMAL</td><td>Amount of money</td></tr>
    <tr><td>type</td><td>VARCHAR</td><td>Deposit, Withdrawal, Transfer in / out</td></tr>
    <tr><td>timestamp</td><td>DATETIME</td><td>When it happened</td></tr>
    <tr><td>account_id</td><td>BIGINT (FK)</td><td>Links to <code>account.id</code></td></tr>
  </tbody>
</table>
<p>One account has many transactions (<code>@OneToMany</code> in <code>Account</code>, <code>@ManyToOne</code> in <code>Transaction</code>).</p>

<h2>Endpoints</h2>
<table>
  <thead>
    <tr><th>Method</th><th>URL</th><th>Description</th><th>Access</th></tr>
  </thead>
  <tbody>
    <tr><td>GET</td><td><code>/register</code></td><td>Registration page</td><td>Public</td></tr>
    <tr><td>POST</td><td><code>/register</code></td><td>Create a new account</td><td>Public</td></tr>
    <tr><td>GET</td><td><code>/login</code></td><td>Login page</td><td>Public</td></tr>
    <tr><td>POST</td><td><code>/login</code></td><td>Handled by Spring Security</td><td>Public</td></tr>
    <tr><td>GET</td><td><code>/dashboard</code></td><td>Show account and balance</td><td>Logged in</td></tr>
    <tr><td>POST</td><td><code>/deposit</code></td><td>Deposit money</td><td>Logged in</td></tr>
    <tr><td>POST</td><td><code>/withdraw</code></td><td>Withdraw money</td><td>Logged in</td></tr>
    <tr><td>POST</td><td><code>/transfer</code></td><td>Transfer money to another user</td><td>Logged in</td></tr>
    <tr><td>GET</td><td><code>/transactions</code></td><td>Transaction history</td><td>Logged in</td></tr>
    <tr><td>POST</td><td><code>/logout</code></td><td>Log out</td><td>Logged in</td></tr>
  </tbody>
</table>

<h2>Getting Started</h2>

<h3>Prerequisites</h3>
<ul>
  <li>JDK 17 or later</li>
  <li>Maven</li>
  <li>MySQL 8</li>
</ul>

<h3>1. Clone the repository</h3>
<pre>
git clone &lt;your-repository-url&gt;
cd bankapp
</pre>

<h3>2. Create the database</h3>
<pre>
CREATE DATABASE bankappdb;
</pre>
<p>Hibernate creates the tables on first run, but not the database itself.</p>

<h3>3. Configure the database connection</h3>
<p>Edit <code>src/main/resources/application.properties</code>:</p>
<pre>
spring.datasource.url=jdbc:mysql://localhost:3306/bankappdb?useSSL=false&amp;serverTimezone=UTC
spring.datasource.username=&lt;your-mysql-username&gt;
spring.datasource.password=&lt;your-mysql-password&gt;
</pre>

<h3>4. Run the application</h3>
<pre>
mvn spring-boot:run
</pre>
<p>Open <a href="http://localhost:8080/register">http://localhost:8080/register</a> in your browser.</p>

<h3>5. Try it</h3>
<ol>
  <li>Register a new user.</li>
  <li>Log in.</li>
  <li>Deposit, withdraw or transfer money.</li>
  <li>Check the data in MySQL:</li>
</ol>
<pre>
USE bankappdb;
SELECT * FROM account;
SELECT * FROM transaction;
</pre>
<p><b>Tip:</b> <code>spring.jpa.show-sql=true</code> prints every SQL query in the console, which is a good way to see how data reaches the database.</p>

<h2>Known Limitations and Future Improvements</h2>
<p>This is a learning project and is not production ready.</p>
<ul>
  <li>CSRF protection is disabled in <code>SecurityConfig</code>. Enable it for real applications.</li>
  <li>Amounts are not validated. Negative or zero values should be rejected.</li>
  <li><code>deposit</code> and <code>withdraw</code> should be annotated with <code>@Transactional</code>, like <code>transferAmount</code>.</li>
  <li>Database credentials should come from environment variables instead of <code>application.properties</code>.</li>
  <li>Add separate DTOs instead of using the <code>Account</code> entity as the security user.</li>
  <li>Add unit and integration tests.</li>
</ul>

<h2>License</h2>
<p>This project is for educational purposes.</p>
