# Self-Managed Database Security

---

## Lesson 1: Introduction to Security

### Question 1
**A user is required to enter a valid username and password in order to access a database. This is an example of which security practice?** *(Select one)*

- **a. Authentication**
  - **Correct**: **Authentication** is the process of verifying the identity of a user attempting to access a database. Requiring a username and password is a commonly used authentication mechanism.

- **b. Authorization**
  - **Incorrect**: **Authorization** determines the specific permissions a user has on the database. Requiring a username and password verifies identity — it does not determine permissions.

- **c. Auditing**
  - **Incorrect**: **Auditing** is the process of monitoring and recording changes to data and database configuration — not verifying identity.

---

### Question 2
**You grant a user permission to create and modify roles and users on the `sample_analytics` database. This is an example of which security practice?** *(Select one)*

- **a. Auditing**
  - **Incorrect**: Auditing is the process of monitoring and recording changes to data and database configuration — not granting permissions.

- **b. Authentication**
  - **Incorrect**: Authentication is the process of verifying the identity of a user — not granting permissions.

- **c. Authorization**
  - **Correct**: **Authorization** determines the specific permissions that a user has on the database.

---

### Question 3
**At your company, you're in charge of making sure only the portions of data necessary for the Security team to complete their tasks are accessible. Which practice is the best choice?** *(Select one)*

- **a. Auditing**
  - **Incorrect**: Auditing is the process of monitoring and recording changes to data and database configuration.

- **b. Role-Based Access Control (RBAC)**
  - **Correct**: **Role-Based Access Control (RBAC)** is a widely used authorization approach in which permissions are granted to roles rather than to users directly. Users are granted one or more roles that determine their access to database resources and operations.

- **c. Authentication**
  - **Incorrect**: Authentication is the process of verifying the identity of a user attempting to access a database.

---

### Question 4
**What is the purpose of auditing in the context of database security?** *(Select all that apply)*

- **a. Increase database performance**
  - **Incorrect**: Recording audit events can **degrade** the performance of a system. It is important to be intentional about the operations you audit to minimize potential performance costs.

- **b. Support the analysis of security incidents**
  - **Correct**: Auditing can be used to support the analysis of security incidents. For example, you could use an audit log to determine what users were logged in at the time of an incident.

- **c. Record all read and write operations made by all users on a database**
  - **Incorrect**: While you can audit read and write operations, this can impact database performance. It's generally recommended to be **selective** and only audit necessary operations.

- **d. Comply with regulatory requirements**
  - **Correct**: Auditing is often needed to comply with regulatory requirements. Some industries require organizations to keep an audit trail of database actions such as user authentication and authorization events.

---

## Lesson 2: Enabling Authentication for a Self-Managed MongoDB Deployment

### Code Summary: Enabling Authentication for a Self-Managed MongoDB Deployment

Review the following code, which demonstrates how to enable authentication on a standalone `mongod` instance using SCRAM, MongoDB's default authentication mechanism.

#### Enable Access Control

**1. Open the `mongod` configuration file:**

```bash
sudo vi /etc/mongod.conf
```

**2. Add the `security.authorization` setting:**

```yaml
# mongod.conf

storage:
  dbPath: /var/lib/mongodb

systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

net:
  port: 27017
  bindIp: 127.0.0.1

processManagement:
  timeZoneInfo: /usr/share/zoneinfo

security:
  authorization: enabled
```

**3. Save the file and restart `mongod` with access control enabled:**

```bash
sudo systemctl restart mongod
```

#### Create the User Administrator

**1. Connect to `mongod` with `mongosh`:**

```bash
mongosh localhost:27017
```

**2. Switch to the `admin` database:**

```javascript
use admin
```

**3. Create the user administrator:**

```javascript
db.createUser({
  user: "globalUserAdmin",
  pwd: passwordPrompt(),
  roles: [
    { role: "userAdminAnyDatabase", db: "admin" }
  ]
})
```

**4. When prompted, enter a password. Then quit the shell:**

```javascript
quit()
```

#### Verify the User Administrator

```javascript
use admin
db.getUsers()
```

---

### Question 1
**What is the default authentication mechanism for MongoDB?** *(Select one)*

- **a. x.509 Certificates**
  - **Incorrect**: MongoDB supports x.509 certificate authentication, but it is **not** the default mechanism. It requires a secure TLS/SSL connection.

- **b. LDAP**
  - **Incorrect**: MongoDB Enterprise supports LDAP, but it is **not** the default mechanism.

- **c. SCRAM**
  - **Correct**: **SCRAM** (Salted Challenge Response Authentication Mechanism) is MongoDB's default authentication mechanism. It verifies the identity of a user by exchanging a challenge and response protected by a cryptographic key.

---

### Question 2
**You assign a user the `userAdminAnyDatabase` role. What types of actions can the user take with this role only?** *(Select all that apply)*

- **a. Create users and roles**
  - **Correct**: The `userAdminAnyDatabase` superuser role provides the ability to **create users and roles**, among other privilege actions.

- **b. Ensure that only users with valid credentials connect to a database**
  - **Incorrect**: That is the purpose of **authentication** — `userAdminAnyDatabase` cannot set authentication measures.

- **c. Modify users and roles**
  - **Correct**: The `userAdminAnyDatabase` superuser role provides the ability to **modify users and roles**, among other privilege actions.

- **d. Read all data**
  - **Incorrect**: `userAdminAnyDatabase` provides the same access to user administration operations as `userAdmin` on all databases except `local` and `config` — it does **not** grant read access to data.

---

## Lesson 3: Establishing Authorization for a Self-Managed MongoDB Deployment

### Code Summary: Establishing Authorization for a Self-Managed MongoDB Deployment

Review the following code, which demonstrates how to assign a built-in role to a database user, authenticate as a specific user, and remove a built-in role from a user.

#### Assign a Built-In Role to a Database User

**1. Connect as the user administrator:**

```bash
mongosh --username globalUserAdmin
```

**2. Switch to the `admin` database:**

```javascript
use admin
```

**3. Create `analystUser` with the `read` role on `sample_analytics`:**

```javascript
db.createUser({
  user: "analystUser",
  pwd: passwordPrompt(),
  roles: [
    { role: "read", db: "sample_analytics" }
  ]
})
```

**4. Quit the shell:**

```javascript
quit()
```

#### Authenticate as `analystUser`

```bash
mongosh "mongodb://analystUser@localhost:27017/sample_analytics?authSource=admin"
```

```javascript
show collections
db.accounts.findOne()
quit()
```

#### Remove a Built-In Role from a Database User

**1. Connect as the user administrator:**

```bash
mongosh --username globalUserAdmin
```

**2. Switch to the `admin` database and check `financeUser`'s current roles:**

```javascript
use admin
db.getUser("financeUser")
```

**3. Revoke the `read` role on `sample_training` from `financeUser`:**

```javascript
db.revokeRolesFromUser(
  "financeUser",
  [
    { role: "read", db: "sample_training" }
  ]
)
```

**4. Review the updated roles:**

```javascript
db.getUser("financeUser")
```

---

### Question 1
**You want to create users on a self-managed MongoDB deployment. What do you need to do before you can do so?** *(Select all that apply)*

- **a. Enable access control**
  - **Correct**: Enabling access control is a **prerequisite** for creating users. With access control enabled, users must authenticate themselves and can only perform actions as allowed by their roles.

- **b. Assign a role**
  - **Incorrect**: Assigning one or more roles is **part of** the process of creating a user, not a prerequisite for creating users.

- **c. Create a user administrator**
  - **Correct**: The user administrator is responsible for managing all users and roles. You must create a user administrator **prior to creating any other users**.

---

### Question 2
**You need to create a new user and want to assign roles to them at the same time. What method should you use?** *(Select one)*

- **a. `db.getUser()`**
  - **Incorrect**: `db.getUser()` returns user information, including current roles, for a specified user — it does **not** create users.

- **b. `db.createUser()`**
  - **Correct**: `db.createUser()` creates a new user on the current database. You can **assign roles** to the new user within the call to `db.createUser()`.

- **c. `db.revokeRolesFromUser()`**
  - **Incorrect**: `db.revokeRolesFromUser()` **removes** one or more roles from a user — it does not assign roles.

---

## Lesson 4: Security Auditing in MongoDB

### Code Summary: Security Auditing in MongoDB

Review the following code, which demonstrates how to access the audit log file.

#### Locate the Audit Log File

```bash
cat /etc/mongod.conf
```

Review the `auditLog.path` setting:

```yaml
auditLog:
  destination: file
  format: JSON
  path: /var/log/mongodb/auditLog.json
```

#### Access the Audit Log File

Use `tail` and `jq` to review the most recent audit log entries:

```bash
sudo tail /var/log/mongodb/auditLog.json | jq
```

---

### Question 1
**Which destinations can audit events be printed to when using a self-managed MongoDB instance?** *(Select all that apply)*

- **a. Console**
  - **Correct**: Audit events can be printed to the **console** in JSON format.

- **b. Syslog**
  - **Correct**: Audit events can be printed to the **syslog** in JSON format. This option is not available on Windows.

- **c. Configuration file**
  - **Incorrect**: Audit events are **not** printed to the configuration file. The configuration file can be used to *enable* auditing by setting the `auditLog.destination` option.

- **d. BSON file**
  - **Correct**: Audit events can be printed to a file in either **BSON** or **JSON** file format.

---

### Question 2
**Given the following configuration file, where are audit events printed on this MongoDB instance?** *(Select one)*

```yaml
# mongod.conf

storage:
  dbPath: /var/lib/mongodb

systemLog:
  destination: file
  logAppend: true
  path: /var/log/mongodb/mongod.log

net:
  port: 27017
  bindIp: 127.0.0.1

processManagement:
  timeZoneInfo: /usr/share/zoneinfo

security:
  authorization: enabled

auditLog:
  destination: file
  format: BSON
  path: /var/log/mongodb/auditLog.bson
```

- **a. `/var/log/mongodb/auditLog.json`**
  - **Incorrect**: The path `/var/log/mongodb/auditLog.json` does not appear in this configuration file.

- **b. `/var/lib/mongodb`**
  - **Incorrect**: `/var/lib/mongodb` is the `storage.dbPath` — the directory where the `mongod` instance stores its data.

- **c. `/var/log/mongodb/auditLog.bson`**
  - **Correct**: `/var/log/mongodb/auditLog.bson` is the `auditLog.path` setting. This is the output file location for auditing when `auditLog.destination` is set to `file`.

- **d. `/var/log/mongodb/mongod.log`**
  - **Incorrect**: `/var/log/mongodb/mongod.log` is the `systemLog.path` setting, used for operational server logs — **not** audit events.

---

## Lesson 5: Introduction to Encryption Concepts

### Question 1
**What are some limitations of encryption at rest?** *(Select all that apply)*

- **a. Managing encryption keys can be a challenge.**
  - **Correct**: If the key used to encrypt data at rest is lost or stolen, the data might become compromised or inaccessible. Anyone with the encryption key will have access to the data on the server.

- **b. It does not protect against attacks on data in memory.**
  - **Correct**: With encryption at rest, data is decrypted for use. Once data is being processed by the database server, it is **vulnerable to attack**.

- **c. It does not encrypt filesystem-based backups.**
  - **Incorrect**: Encryption at rest **can** be used to encrypt backup copies of your data. Atlas users may enable database-level encryption via the WiredTiger Encrypted Storage Engine.

- **d. It does not protect against insider threats.**
  - **Correct**: Trusted users with authorization can use their privileges to decrypt data protected by encryption at rest, potentially exposing or tampering with the data.

---

### Question 2
**A doctor's office stores patient medical records in a database. You want to encrypt diagnosis information in the client application before it's sent over the network to MongoDB servers. What type of encryption should you use?** *(Select one)*

- **a. Encryption at rest**
  - **Incorrect**: Encryption at rest only encrypts data **in storage on the server** — it does not encrypt data in the client application.

- **b. Client-Side Field Level Encryption (CSFLE)**
  - **Correct**: **CSFLE** encrypts data in the client before it's sent to the database. This ensures data is tamper- and read-proof on the server.

- **c. TLS/SSL (Transport Encryption)**
  - **Incorrect**: TLS/SSL encrypts **network traffic** between the client and MongoDB. It ensures traffic is readable only by the intended client, but data is not encrypted at-rest or in-use on the server.

---

## Lesson 6: Encryption in Self-Managed MongoDB Deployments

### Question 1
**How does MongoDB provide encryption at rest?** *(Select all that apply)*

- **a. Client-Side Field Level Encryption (CSFLE)**
  - **Correct**: MongoDB's **CSFLE** feature provides in-use and at-rest encryption. It enables you to encrypt data in your application before sending it to MongoDB. Protected fields are encrypted from the time they leave the application boundary until received back.

- **b. Transport Layer Security (TLS)**
  - **Incorrect**: TLS provides **transport encryption** (data in transit) — not encryption at rest.

- **c. Encrypted Storage Engine**
  - **Correct**: MongoDB's **Encrypted Storage Engine** enables you to natively encrypt MongoDB data files on disk. This feature is only available on **MongoDB Enterprise** instances.

---

### Question 2
**How does MongoDB support encryption of data in transit?** *(Select all that apply)*

- **a. Encrypted Storage Engine**
  - **Incorrect**: The Encrypted Storage Engine encrypts **data files on disk** — not data in transit.

- **b. Client-Side Field Level Encryption (CSFLE)**
  - **Correct**: **CSFLE** provides in-use encryption. Protected fields are encrypted from the time they leave the application boundary until they are received back, covering data in transit.

- **c. Transport Layer Security (TLS)**
  - **Correct**: MongoDB supports **TLS** to provide encryption of data in transit. TLS enables encrypted communication between client applications and MongoDB instances.

---

### Question 3
**How does MongoDB support in-use encryption?** *(Select all that apply)*

- **a. Client-Side Field Level Encryption (CSFLE)**
  - **Correct**: MongoDB's **CSFLE** feature provides in-use encryption, enabling you to encrypt data in your application before sending it over the network to MongoDB.

- **b. Automatic encryption**
  - **Correct**: **Automatic encryption** is a mechanism available in MongoDB Enterprise for setting up CSFLE. It enables encrypted read and write operations without having to write code to specify how to encrypt fields.

- **c. Transport Layer Security (TLS)**
  - **Incorrect**: TLS provides encryption of **data in transit** — not in-use encryption.

---

## Lesson 7: Enabling Network Encryption for a Self-Managed MongoDB Deployment

### Code Summary: Enabling Network Encryption for a Self-Managed MongoDB Deployment

Review the following code, which demonstrates how to deploy and test a three-member replica set that uses TLS transport encryption on a self-managed deployment.

#### Step 1: Update the Configuration File for Each Server to Enable TLS

**1. Open the `mongod` configuration file:**

```bash
sudo vi /etc/mongod.conf
```

**2. Set `net.tls.mode` to `requireTLS` and specify the certificate key file path:**

```yaml
net:
  tls:
    mode: requireTLS
    certificateKeyFile: /etc/tls/mongodb.pem
```

**3. Assign a name to the replica set:**

```yaml
replication:
  replSetName: TLSEnabledReplSet
```

**4. Save and exit.**

#### Step 2: Restart the MongoDB Service on Each Server

```bash
sudo systemctl restart mongod
```

#### Step 3: Initiate the Replica Set

**1. Connect to `mongod` using a TLS connection string:**

```bash
mongosh "mongodb://mongod0.replset.com/?tls=true&tlsCAFile=/etc/tls/root-ca.pem"
```

**2. Switch to the `admin` database and initiate the replica set:**

```javascript
use admin

rs.initiate(
  {
    _id: "TLSEnabledReplSet",
    version: 1,
    members: [
      { _id: 0, host: "mongod0.replset.com" },
      { _id: 1, host: "mongod1.replset.com" },
      { _id: 2, host: "mongod2.replset.com" }
    ]
  }
)
```

#### Step 4: Test the TLS Requirement

**1. Exit and reconnect with TLS (should succeed):**

```bash
exit

mongosh "mongodb://mongod0.replset.com,mongod1.replset.com,mongod2.replset.com/?replicaSet=TLSEnabledReplSet&tls=true&tlsCAFile=/etc/tls/root-ca.pem"
```

**2. Try to connect without TLS (should fail):**

```bash
exit

mongosh "mongodb://mongod0.replset.com,mongod1.replset.com,mongod2.replset.com/?replicaSet=TLSEnabledReplSet"
```

---

### Question 1
**You want to enable TLS for a self-managed MongoDB deployment. Which certificate should you or your organization obtain prior to enabling TLS?** *(Select one)*

- **a. A keyfile that provides use of local key management**
  - **Incorrect**: A keyfile supports **local key management** for the encrypted storage engine's master key — this supports **encryption at rest**, not TLS.

- **b. A valid TLS certificate issued by a certificate authority for each server in the deployment**
  - **Correct**: To enable TLS in a self-managed environment, obtain a valid, signed **TLS certificate from a certificate authority** for each server. This permits MongoDB drivers to verify the server's identity.

- **c. A valid, self-signed TLS certificate for each server in the deployment**
  - **Incorrect**: While MongoDB can use a self-signed certificate, there will be **no validation of server identity**, leaving you vulnerable to a **man-in-the-middle attack**.

---

### Question 2
**You want to specify that a server uses and accepts only TLS-encrypted connections. What should you set the `net.tls.mode` configuration file setting to?** *(Select one)*

- **a. `preferTLS`**
  - **Incorrect**: `preferTLS` specifies that connections between servers use TLS, but incoming connections accept **both TLS and non-TLS**.

- **b. `/etc/tls/mongodb.pem`**
  - **Incorrect**: `/etc/tls/mongodb.pem` is a **file path** for the TLS certificate and key — it is not a valid value for `net.tls.mode`.

- **c. `requireTLS`**
  - **Correct**: Setting `net.tls.mode` to **`requireTLS`** specifies that the server uses and accepts **only** TLS-encrypted connections.

- **d. `disabled`**
  - **Incorrect**: Setting `net.tls.mode` to `disabled` specifies that the server does **not** use TLS.