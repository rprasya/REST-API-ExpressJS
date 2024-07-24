# REST API with ExpressJS

This project demonstrates how to create a REST API using ExpressJS, including setting up routes, controllers, middleware, and integrating with a MySQL database.

## Getting Started

### Prerequisites

Make sure you have Node.js and npm installed on your machine. You will also need MySQL installed and running.

### Installation

1. Initialize your project:
    ```sh
    npm init -y
    ```

2. Install necessary packages:
    ```sh
    npm install express mysql2 nodemon
    ```

3. Update `package.json` to use `nodemon`:
    ```json
    "scripts": {
      "start": "nodemon index.js"
    }
    ```

4. Create `index.js` and set up Express:
    ```javascript
    const express = require('express');
    const app = express();

    app.listen(4000, () => {
      console.log('Server running on port 4000');
    });

    app.use("/", (req, res) => {
      res.send('This is middleware');
    });
    ```

### Project Structure

1. Organize your folders:
    ```plaintext
    project-root
    ├── routes
    ├── controller
    ├── middleware
    ├── models
    ├── config
    └── index.js
    ```

2. Create `users.js` in the `routes` folder:
    ```javascript
    const express = require('express');
    const router = express.Router();

    router.get("/", (req, res) => {
      res.json({ message: "GET all users success" });
    });

    module.exports = router;
    ```

3. Import and use the routes in `index.js`:
    ```javascript
    const usersRoutes = require('./routes/users');
    app.use("/users", usersRoutes);
    ```

4. Create controller functions in `controller/users.js`:
    ```javascript
    const getAllUsers = (req, res) => {
      res.json({ message: "GET all users success" });
    };

    const createNewUsers = (req, res) => {
      res.json({ message: "CREATE new users success" });
    };

    module.exports = {
      getAllUsers,
      createNewUsers
    };
    ```

5. Update `routes/users.js` to use controller functions:
    ```javascript
    const UserController = require("../controller/users");

    router.get("/", UserController.getAllUsers);
    router.post("/", UserController.createNewUsers);
    ```

### Middleware

1. Create a sample middleware in `index.js`:
    ```javascript
    app.use((req, res, next) => {
      console.log("Request to API");
      next();
    });
    ```

2. Move middleware to `middleware/log.js`:
    ```javascript
    const logRequest = (req, res, next) => {
      console.log("Request", req.path);
      next();
    };

    module.exports = logRequest;
    ```

3. Import and use the middleware in `index.js`:
    ```javascript
    const logRequest = require("./middleware/log");
    app.use(logRequest);
    ```

### CRUD Operations and Database Integration

1. Allow JSON request bodies in `index.js`:
    ```javascript
    app.use(express.json());
    ```

2. Set up MySQL connection in `index.js`:
    ```javascript
    const mysql = require('mysql2');

    const pool = mysql.createPool({
      host: 'localhost',
      user: 'root',
      database: 'test',
      waitForConnections: true,
      connectionLimit: 10,
      maxIdle: 10,
      idleTimeout: 60000,
      queueLimit: 0,
      enableKeepAlive: true,
      keepAliveInitialDelay: 0,
    });

    app.use("/", (req, res) => {
      pool.execute("SELECT * FROM users", (err, rows) => {
        if (err) {
          res.json({ message: "Connection Failed" });
        } else {
          res.json({
            message: "Connection success",
            data: rows,
          });
        }
      });
    });
    ```

3. Test the connection using Postman:
    - Method: GET
    - URL: `http://localhost:4000/`

4. Move database connection setup to `config/database.js`:
    ```javascript
    const mysql = require('mysql2');

    const pool = mysql.createPool({
      host: 'localhost',
      user: 'root',
      database: 'test',
      waitForConnections: true,
      connectionLimit: 10,
      maxIdle: 10,
      idleTimeout: 60000,
      queueLimit: 0,
      enableKeepAlive: true,
      keepAliveInitialDelay: 0,
    }).promise();

    module.exports = pool;
    ```

5. Update `index.js` to use the new configuration:
    ```javascript
    const dbPool = require('./config/database');

    app.use("/", (req, res) => {
      dbPool.execute("SELECT * FROM users", (err, rows) => {
        if (err) {
          res.json({ message: "Connection Failed" });
        } else {
          res.json({
            message: "Connection success",
            data: rows,
          });
        }
      });
    });
    ```

## Testing

Use Postman or any other API testing tool to test the endpoints.

## Authors

- **Your Name** - *Initial work* - [rprasya](https://github.com/rprasya)
