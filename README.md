# Membuat REST API menggunakan ExpressJS

Langkah-langkah untuk membuat REST API dengan menggunakan ExpressJS:

## Persiapan
1. Inisialisasi proyek baru:
    ```sh
    npm init -y
    ```
2. Install dependencies yang dibutuhkan:
    ```sh
    npm i express mysql2 nodemon
    ```
3. Tambahkan script untuk menjalankan aplikasi di `package.json`:
    ```json
    "scripts": {
      "start": "nodemon index.js"
    }
    ```

## Membuat Server Express
4. Buat file `index.js` dan import express:
    ```js
    const express = require('express');
    const app = express();
    const PORT = process.env.PORT || 9000;
    app.listen(PORT, () => {
      console.log(`Server running di port ${PORT}`);
    });
    ```

## Middleware
5. Tambahkan contoh middleware:
    ```js
    app.use("/", (req, res, next) => {
      res.send('Ini middleware');
    });
    ```

## Struktur Folder
6. Buat struktur folder sebagai berikut:
    ```
    /routes
    /controller
    /middleware
    /models
    /config
    ```

## Membuat Routes dan Controller
7. Buat file `users.js` di dalam folder `routes`:
    ```js
    const express = require('express');
    const router = express.Router();
    const UserController = require('../controller/users');

    router.get("/", UserController.getAllUsers);
    router.post("/", UserController.createNewUsers);

    module.exports = router;
    ```
8. Buat controller di dalam `controller/users.js`:
    ```js
    const getAllUsers = (req, res) => {
      res.json({
        message: "GET all users success",
      });
    };

    const createNewUsers = (req, res) => {
      res.json({
        message: "CREATE new users success",
      });
    };

    module.exports = {
      getAllUsers,
      createNewUsers
    };
    ```

## Middleware Logging
9. Buat middleware logging di `middleware/log.js`:
    ```js
    const logRequest = (req, res, next) => {
      console.log("log request", req.path);
      next();
    };

    module.exports = logRequest;
    ```

10. Import middleware logging di `index.js`:
    ```js
    const logRequest = require('./middleware/log');
    app.use(logRequest);
    ```

## CRUD dan Integrasi Database
11. Install MySQL dan buat konfigurasi di `config/database.js`:
    ```js
    const mysql = require('mysql2');
    const pool = mysql.createPool({
      host: process.env.DB_HOST,
      user: process.env.DB_USERNAME,
      password: process.env.DB_PASSWORD,
      database: process.env.DB_NAME
    }).promise();

    module.exports = pool;
    ```

12. Buat model di `models/users.js`:
    ```js
    const dbPool = require('../config/database');

    const getAllUsers = () => {
      const SQLQuery = "SELECT * FROM users";
      return dbPool.execute(SQLQuery);
    };

    module.exports = {
      getAllUsers,
    };
    ```

13. Update controller di `controller/users.js`:
    ```js
    const UsersModel = require('../models/users');

    const getAllUsers = async (req, res) => {
      try {
        const [data] = await UsersModel.getAllUsers();
        res.json({
          message: "GET all users success",
          data: data,
        });
      } catch (error) {
        res.status(500).json({
          message: "Server Error",
          serverMessage: error,
        });
      }
    };

    module.exports = {
      getAllUsers,
    };
    ```

## Menggunakan dotenv untuk Konfigurasi
14. Install dotenv:
    ```sh
    npm install dotenv
    ```
15. Setup dotenv di `index.js`:
    ```js
    require('dotenv').config();
    ```
16. Buat file `.env` untuk menyimpan konfigurasi rahasia:
    ```env
    PORT=XXXX
    DB_HOST=localhost
    DB_USERNAME=root
    DB_PASSWORD=password
    DB_NAME=your_database_name
    ```

17. Tambahkan `.env` ke `.gitignore` dan buat file `env.example`:
    ```sh
    .env
    ```

    ```env
    PORT=
    DB_HOST=
    DB_USERNAME=
    DB_PASSWORD=
    DB_NAME=
    ```

## Testing
18. Test API menggunakan Postman atau alat lain.

Langkah-langkah di atas seharusnya membantu Anda membuat REST API menggunakan ExpressJS dengan struktur yang terorganisir dan aman. Selamat mencoba!
