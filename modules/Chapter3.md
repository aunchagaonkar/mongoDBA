# The MongoDB Shell

---

## Lesson 1: Installing and Connecting to the MongoDB Shell

### Code Summary: Installing and Connecting to the MongoDB Shell
The following code demonstrates how to install and use `mongosh` in a Linux environment.

Update `apt` and install `gnupg`. Then add the MongoDB public GPG key to the system:
```bash
apt update 
apt install gnupg
wget -qO - https://www.mongodb.org/static/pgp/server-6.0.asc | sudo apt-key add - 
```

Create a list file for MongoDB:
```bash
echo "deb [ arch=amd64,arm64 ] https://repo.mongodb.org/apt/ubuntu focal/mongodb-org/6.0 multiverse" | sudo tee /etc/apt/sources.list.d/mongodb-org-6.0.list
```

Update `apt` and install `mongosh`:
```bash
apt update
apt install -y mongodb-mongosh
```

Check that `mongosh` is installed:
```bash
mongosh --version
```

Exit `mongosh`:
```bash
exit
```

#### Connect to Your Atlas Cluster by Using mongosh
The following code demonstrates how to connect to your MongoDB Atlas cluster by using `mongosh`.

Run the `mongosh` command followed by your connection string:
```bash
mongosh "mongodb+srv://<username>:<password>@<cluster_name>.example.mongodb.net"
```

Alternatively, you can log in by providing the username and password as a command line argument with `-u` and `-p`:
```bash
mongosh -u exampleuser -p examplepass "mongodb+srv://myatlasclusteredu.example.mongodb.net"
```

After connecting to the Atlas cluster, run `db.hello()`, which provides some information about the role of the `mongod` instance you are connected to:
```javascript
db.hello()
```

Finally, run the `exit` command inside `mongosh` to go back to the terminal:
```javascript
exit
```

---

### Question 1
**Which of the following commands can be used to connect to a database called `students` on a local MongoDB instance?** *(Select one)*

- **a. `mongosh`**
  - **Incorrect**: This command will connect to a MongoDB instance, but it will not connect to a database called `students`. Instead, it will connect to the `test` database.

- **b. `mongosh --host localhost --db students`**
  - **Incorrect**: This command will connect to a MongoDB instance, but the `--db` option is not valid for `mongosh`. You can, however, connect to `localhost` and a database called `students` by adding the database after the host, like this: `mongosh --host localhost students`.

- **c. `mongosh --db students`**
  - **Incorrect**: This command will connect to a MongoDB instance, but the `--db` option is not valid for `mongosh`. You can, however, connect to `localhost` and a database called `students` by removing the `--db` flag from the command, like this: `mongosh students`.

- **d. `mongosh students`**
  - **Correct**: This command will connect to a local MongoDB instance and select the `students` database. This method is also the shortest.

---

### Question 2
**Which of the following are valid methods for connecting `mongosh` to a MongoDB Atlas cluster and selecting the `sample_training` database?** *(Select all that apply)*

- **a. `mongosh "mongodb+srv://<clustername>.mongodb.net/?database=sample_training" --username <username>`**
  - **Incorrect**: Although using the `--username` flag is a valid way to pass in the database user, passing the database name as a query parameter within the connection string will result in an error.

- **b. `mongosh "mongodb+srv://<username>:<password>@<clustername>.mongodb.net/sample_training"`**
  - **Correct**: This connection string is valid for connecting `mongosh` to a MongoDB Atlas cluster and selecting the `sample_training` database. This connection string appends the database name to the connection string as part of its URI.

- **c. `mongosh "mongodb+srv://<clustername>.mongodb.net/" --username <username> --db sample_training`**
  - **Incorrect**: Although this command allows you to connect to the Atlas cluster, the `--db` flag is not recognized in `mongosh`. Therefore, the user will be connected to the `test` database rather than the `sample_training` database.

- **d. `mongosh "mongodb+srv://<clustername>.mongodb.net/sample_training" --username <username>`**
  - **Correct**: This connection string is valid for connecting `mongosh` to a MongoDB Atlas cluster and selecting the `sample_training` database. You can use the `--username` flag to specify the username, which will prompt the user for their password.

---

## Lesson 2: Configuring the MongoDB Shell

### Code Summary: Configuring the MongoDB Shell
The following sections explain how to configure the MongoDB Shell, or `mongosh`, by using the `config` API, the configuration file, and the `--eval` flag.

#### Configure mongosh by Using the config API
The `config` API allows you to configure the behavior of `mongosh` by retrieving, setting, and resetting configuration options. To get a list of the available configuration options, run the following command:

```javascript
config
```

The available configuration options are as follows:
- `displayBatchSize` - The number of documents to display when using the `it` iterator.
- `maxTimeMS` - The maximum amount of time to allow a query to run.
- `enableTelemetry` - Whether to enable telemetry.
- `editor` - The editor to use when editing code.
- `snippetIndexSourceURLs` - The URLs to use when fetching snippet index files.
- `snippetRegistryURL` - The URL to use when fetching snippet registry files.
- `snippetAutoload` - Whether to automatically load snippets.
- `inspectCompact` - Whether to use compact mode when inspecting objects.
- `inspectDepth` - The maximum depth to use when inspecting objects.
- `historyLength` - The number of history entries to keep.
- `showStackTraces` - Whether to show stack traces when errors occur.
- `redactHistory` - Whether to redact sensitive information from history.

To retrieve the value of a specific configuration option, use the `config.get()` method. As an example, here's the command to get the current value of `enableTelemetry`:
```javascript
config.get('enableTelemetry') // returns true or false
```

To set the value of a specific configuration option, use the `config.set()` method, and pass in the name of the option and the value that you want to set:
```javascript
config.set('enableTelemetry', false)
```

To reset the value of a specific configuration option, use the `config.reset()` method, and pass in the name of the option that you want to reset:
```javascript
config.reset('enableTelemetry')
```

#### Configure mongosh by Using the Configuration File
You can also configure `mongosh` by using a configuration file called `mongosh.conf`. The location of this file depends on your operating system:
- **Windows**: `mongosh.cfg`, in the same directory as the `mongosh.exe` binary.
- **macOS**:
  - `/usr/local/etc/mongosh.conf`
  - `/etc/mongosh.conf`
  - `/opt/homebrew/etc/mongosh.conf`
- **Linux**: `/etc/mongosh.conf`

The `mongosh.conf` file uses YAML format. The following code shows how to configure the `displayBatchSize`, `inspectDepth`, and `redactHistory` options in `mongosh.conf`:
```yaml
mongosh: 
  displayBatchSize: 50 
  inspectDepth: 20 
  redactHistory: "remove-redact"
```

#### Configure mongosh by Using the --eval Flag
Finally, you can set configuration options in `mongosh` by using the `--eval` flag with a command. For example, to disable telemetry, you would run the following command to invoke the `disableTelemetry()` function:
```bash
mongosh --eval "disableTelemetry()"
```

You can also use the `--eval` flag to run queries and other commands. For example, to run a query for some documents, you would run the following command:
```bash
mongosh --eval "db.accounts.find().limit(3)" --quiet
```

---

### Question 1
**What method from the `config` API allows you to set a configuration option in `mongosh`?** *(Select one)*

- **a. `config.get()`**
  - **Incorrect**: The `config.get()` method is used to retrieve a configuration option in `mongosh`.

- **b. `config.set()`**
  - **Correct**: The `config.set()` method, one of the methods available in the `config` API, allows you to set the value of a specific configuration option by passing the name of the option that you want to set and the value. For example, if you want to disable telemetry, you would use `config.set('enableTelemetry', false)`.

- **c. `config.apply()`**
  - **Incorrect**: The `config.apply()` method is not a valid method in `mongosh`.

- **d. `db.config.set()`**
  - **Incorrect**: The `db.config.set()` method is not a valid method in `mongosh`.

---

### Question 2
**Which of the following settings can be adjusted by editing an option in the `mongosh.conf` file?** *(Select all that apply)*

- **a. The color of the font that's displayed in `mongosh`**
  - **Incorrect**: The `mongosh.conf` file is used to configure the behavior of `mongosh`. It does not affect the font color.

- **b. How many items per batch are displayed when using the "it" iterator**
  - **Correct**: The `mongosh.conf` file can be used to change the `displayBatchSize` option, which controls how many items per batch are displayed when using the `it` iterator. For example, running `db.accounts.find()` would return a batch of 10 documents, assuming the `displayBatchSize` is set to 10, instead of returning all documents in the collection.

- **c. The editor used by `mongosh` when using the `edit()` method**
  - **Correct**: The `mongosh.conf` file can be used to change the `editor` option, which determines the editor that's used by `mongosh` when using the `edit()` method. By default, no editor is set. So, if you run `config.get("editor")`, it will return a null value.

- **d. Whether `mongosh` prompts the user for confirmation before running a `deleteOne()` or `deleteMany()` command**
  - **Incorrect**: There is no option in the `mongosh.conf` file that allows the user to confirm whether they want to delete documents when `deleteOne()` or `deleteMany()` is run against the database.

---

## Lesson 3: Using the MongoDB Shell

### Code Summary: Using the MongoDB Shell
The following sections explain how to use `mongosh` to run external scripts and to edit commands in an external editor.

#### Run External Scripts
To run an external script in `mongosh`, use the `load()` method. For example, to run the `randomPost.js` file, you would run the following code:
```javascript
load('randomPost.js')
```

Within your script, you can use the `db.getSiblingDB()` method to access a database without having to switch to it in `mongosh`. For example, here's how you would access the `sample_training` database in the `randomPost.js` script:
```javascript
db = db.getSiblingDB("sample_training");
console.log(`\nCurrent database: ${db.getName()}`);
console.log("Random post sample of 500 words:\n");
let result = db.posts.aggregate({
  $sample: { size: 1 },
});
printjson(result.next().body.slice(0, 500) + " ...");
```

#### Edit Commands in an External Editor
To edit a function or command in an external text editor, like Vim or nano, while using `mongosh`, use the `edit` command. To use this command, you must first set the `config.editor` value in `mongosh`. To do so, use the `config.set()` method:
```javascript
config.set("editor", "vim")
```

Once the editor is set, you can then use `edit` to modify a new or existing command. For example, to edit the `giveMeADate` function, you would run the following code:
```javascript
edit giveMeADate
```

---

### Question 1
**You want to use an external JavaScript file within an active `mongosh` session. What method should you use?** *(Select one)*

- **a. `load()`**
  - **Correct**: The `load()` method allows you to load and use an external JavaScript file within an active `mongosh` session. For example, if you have a file named `myScript.js` in the current working directory, you can load it by running `load('myScript.js')`.

- **b. `loadjs()`**
  - **Incorrect**: The `loadjs()` method is not a valid method in `mongosh`.

- **c. `run()`**
  - **Incorrect**: The `run()` method is not a valid method in `mongosh`.

- **d. You cannot use external JavaScript files within an active `mongosh` session.**
  - **Incorrect**: `mongosh` allows you to load JavaScript files into the shell by using the `load()` method.

---

### Question 2
**What method is used to change databases within a script by using the `load()` method in `mongosh`?** *(Select one)*

- **a. `db.getDb()`**
  - **Incorrect**: The `db.getDb()` method is not a valid method for use in `mongosh`. It will throw an error when used within a script that's loaded into `mongosh` by using the `load()` method.

- **b. `db.getMongo()`**
  - **Incorrect**: The `db.getMongo()` method will return the connection string for the current deployment when used in a script or `mongosh`. However, it will not allow you to change databases.

- **c. `use`**
  - **Incorrect**: The `use` helper can be used to change databases when using `mongosh`. However, it will throw an error when used in a script that's loaded into `mongosh` by using the `load()` method.

- **d. `db.getSiblingDB()`**
  - **Correct**: The `db.getSiblingDB()` method allows you to change databases within a script that gets loaded into `mongosh` by using the `load()` method. The `db.getSiblingDB()` method accepts one argument, which is a string that contains the name of the database that you want to switch to.

---

## Lesson 4: Using the MongoDB Shell Library (.mongoshrc.js)

### Code Summary: Using the MongoDB Shell Library (.mongoshrc.js)
The `.mongoshrc.js` file can be used to run code when `mongosh` starts. This file doesn't exist unless you create it. To create the file, run the following command in your home directory:

```bash
touch ~/.mongoshrc.js
```

`db.adminCommand()` is used to run administrative commands against the `admin` database, which we can write helper functions for and then add them directly to the `.mongoshrc.js` file. For example, the following code shows a lengthy command to get the server's compatibility version wrapped in a helper function called `fcv()`. This function is added to the global scope of `mongosh` by adding the function directly to the `.mongoshrc.js` file:

```javascript
const fcv = () => db.adminCommand({getParameter: 1, featureCompatibilityVersion: 1})
```

We can also use the `.mongoshrc.js` file to customize the prompt. For example, we can customize the prompt to display information about the current `mongosh` session by using the `prompt()` function. For example, the following code displays the database name, the current user, and the read preference in the prompt:

```javascript
prompt = () => {
  let returnString = "";
  const dbName = db.getName();
  const isEnterprise = db.serverBuildInfo().modules.includes("enterprise");
  const mongoURL = db.getMongo()._uri.includes("mongodb.net");
  const nonAtlasEnterprise = isEnterprise && !mongoURL;
  const usingAtlas = mongoURL && isEnterprise;
  const readPref = db.getMongo().getReadPrefMode();
  const isLocalHost = /localhost|127\.0\.0\.1/.test(db.getMongo()._uri);
  const currentUser = db.runCommand({ connectionStatus: 1 }).authInfo
    .authenticatedUsers[0]?.user;
  if (usingAtlas) {
    returnString += `Atlas || ${dbName} || ${currentUser} || ${readPref} || =>`;
  } else if (isLocalHost) {
    returnString += `${
      nonAtlasEnterprise ? "Enterprise || localhost" : "localhost"
    } || ${dbName} || ${readPref} || =>`;
  } else if (nonAtlasEnterprise) {
    returnString += `Enterprise || ${dbName} || ${currentUser} || ${readPref} || =>`;
  } else {
    returnString += `${dbName} || ${readPref} || =>`;
  }
  return returnString;
};
```

---

### Question 1
**Where should the `.mongoshrc.js` file be located?** *(Select one)*

- **a. The same directory as the `mongosh` executable.**
  - **Incorrect**: The `.mongoshrc.js` file should not be located in the same directory as the `mongosh` executable, as `mongosh` won't be able to use the file.

- **b. The user's home directory.**
  - **Correct**: The `.mongoshrc.js` file should be located in the user's home directory so that `mongosh` will be able to load and use it.

- **c. The file must be loaded into the `mongosh` session by using the `load()` method.**
  - **Incorrect**: The `.mongoshrc.js` file is loaded automatically when you start a new `mongosh` session. It does not need to be loaded by using the `load()` method.

---

### Question 2
**Which of the following methods terminate an active `mongosh` session?** *(Select one)*

- **a. `exit`**
  - **Correct**: You can end the current `mongosh` session by running `exit` in the shell. You can also end the current session by using `quit`, or by pressing `Ctrl + D` on your keyboard.

- **b. `db.exit()`**
  - **Incorrect**: In `mongosh`, `db.exit()` is not recognized as a valid method for terminating a session. Doing so will throw an error message that states `db.exit is not a function`.

- **c. `db.quit()`**
  - **Incorrect**: In `mongosh`, `db.quit()` is not recognized as a valid method for terminating a session. Doing so will throw an error message that states `db.quit is not a function`.

---

## Lesson 5: MongoDB Shell Tips and Tricks

### Code Summary: MongoDB Shell Tips and Tricks
The following sections explain some tips and tricks for using `mongosh`.

#### Use Node.js APIs to Read and Write Files
`mongosh` provides access to all native Node.js APIs, including the file system module (`fs`). This means that you can use `mongosh` to read and write files. In the following example, we assign the result of a query to a variable called `customers` so that it can be used later:

```javascript
const customers = db.sales.find({}, {customer: 1, _id: 0})
```

Next, we use the `fs` module to write the results of the query to a file called `customers.json`. To do this, we pass in the `customers` variable and use the `EJSON.stringify()` method to convert the array to a string. We also use the `null` and `2` parameters to format the output so that it's easier to read:

```javascript
fs.writeFileSync('customers.json', EJSON.stringify(customers.toArray()), null, 2)
```

#### Generate Seed Data
You can also use `npm` packages in `mongosh`, as they support external scripts and `require` statements. In the following example, we use the `faker` package to generate an array of 10 fake users. We then insert the users as documents into a new database:

```javascript
const { faker } = require("@faker-js/faker");
const users = [];
for (let i = 0; i < 10; i++) {
  users.push({
    name: faker.person.fullName(),
    email: faker.internet.email(),
    phone: faker.phone.number(),
  });
}

console.log("Inserting fake users ...");
db.getSiblingDB("test_data").users.insertMany(users);
```

> **Note**: To use the `faker` package, you must first install it by using `npm install @faker-js/faker --save-dev` in the same directory as your external script. Or, you can install the package globally by using `npm install -g @faker-js/faker`.

---

### Question 1
**Which of the following examples demonstrates the correct usage of the `EJSON.stringify()` method in `mongosh` to convert an extended JSON object into a string?** *(Select one)*

- **Option A**: `EJSON.stringify({ name: "Test User", dob: new Date("1990-01-01") })`
- **Option B**: `EJSON.stringify(name: "Test User", dob: new Date("1990-01-01")`
- **Option C**: `({name: "Test User", dob: new Date("1990-01-01")}).EJSON.stringify()`
- **Option D**: `EJSON.stringify = { name: "Test User", dob: new Date("1990-01-01") }`

Options:
- **a. Option A**
  - **Correct**: The proper syntax for the `EJSON.stringify()` method is to pass the EJSON object into the method as an argument.

- **b. Option B**
  - **Incorrect**: While the method is invoked correctly, and the argument is passed to the method, the argument that's passed is not an object.

- **c. Option C**
  - **Incorrect**: The way that the method is invoked in this example implies that `EJSON.stringify` is a method on the object that precedes it. This is not the case.

- **d. Option D**
  - **Incorrect**: The syntax in this example attempts to reassign the value of `EJSON.stringify` to an object.

---

### Question 2
**In `mongosh`, what Node.js `fs` module API method can be used to write the results of a query to a file?** *(Select one)*

- **a. `fs.write()`**
  - **Incorrect**: The Node.js `fs` module does have a `write()` method, but it's used to indicate a position and buffer that you want to write to.

- **b. `fs.writeFileSync()`**
  - **Correct**: The Node.js `fs` module's `writeFileSync()` method allows you to specify a path to write to as well as the data to write. It can be used in `mongosh` to write the results of a query after converting the results to an EJSON string.

- **c. `fs.commit()`**
  - **Incorrect**: The Node.js `fs` module does not have a `commit()` method. There are two methods available in the `fs` API that enable writing to files, one that is synchronous and another that is asynchronous.

- **d. `EJSON.stringify()`**
  - **Incorrect**: The `EJSON.stringify()` method is not specific to Node.js. It's used in `mongosh` for converting extended JSON objects into strings.

---

### Question 3
**You want to load a script into `mongosh` that requires an `npm` package. To do so, where should the `npm` package be installed?** *(Select all that apply)*

- **a. An option for using an `npm` package in an external script is to install the package globally and then require it in the script.**
  - **Correct**: An option for using an `npm` package in an external script is to install the package globally and then require it in the script.

- **b. The package can be installed in the `node_modules` directory in your current working directory. Then it can be added to a `mongosh` script that can be used with the `load()` method.**
  - **Correct**: An option for using an `npm` package in an external script is to install the package in the same directory as the script and then require it in the script.

- **c. `mongosh` will automatically download and install the necessary dependencies when the script is run in the shell with the `load()` method.**
  - **Incorrect**: `mongosh` will not automatically download and install the necessary dependencies when the script is loaded. Like a Node.js application, when working in `mongosh`, you must install the necessary dependencies before you can use them by using a package manager like `npm`.

---

## Conclusion

### The MongoDB Shell
In this unit, you learned how to:
- Install the MongoDB Shell (`mongosh`) and connect to self-hosted and Atlas deployments
- Configure `mongosh` by using the `config` API and setting options in the configuration file (`mongosh.conf`)
- Pass command line arguments to `mongosh`
- Write a JavaScript function inside `mongosh`
- Use `db.getSiblingDB()` to change databases within a script by using the MongoDB Shell's `load()` method
- Edit a function or variable from `mongosh` in an external text editor
- Add a helper function to `.mongoshrc.js` to add functionality to the MongoDB Shell
- Add information to the `mongosh` prompt by editing the `.mongoshrc.js` file

### Resources
Use the following resources to learn more about how to install, use, and customize the MongoDB Shell, or `mongosh`:

- **General**
  - Getting Started with MongoDB Atlas
  - mongodb-js/mongosh (GitHub)

- **Lesson 1: Installing and Connecting to the MongoDB Shell**
  - Install mongosh
  - Connect to a Deployment
  - Connect to a Database Deployment (for Atlas)
  - MongoDB Shell Options: Host, Port, Username

- **Lesson 2: Configuring the MongoDB Shell**
  - Configure mongosh
  - Configure Settings Using the API
  - Configure Settings Using a Configuration File
  - Configure Settings

- **Lesson 3: Using the MongoDB Shell**
  - Write Scripts
  - `db.getSiblingDB()`
  - Use an Editor for Commands
  - Gist for `randomPost.js`
  - Gist for `giveMeADate.js`

- **Lesson 4: Using the MongoDB Shell Library (.mongoshrc.js)**
  - Run Code From a Configuration File
  - Customize the `mongosh` Prompt
  - Gist for `featureCompatibilityVersion` Helper (`.mongoshrc.js`)
  - Gist for Additional Information in the `mongosh` Prompt (`.mongoshrc.js`)

- **Lesson 5: MongoDB Shell Tips and Tricks**
  - MongoDB Shell Tips and Tricks
  - `writeFileSync()` (Node.js Documentation)
  - Faker npm Package
  - `load()` in `mongosh`
  - `require()` in `mongosh`
