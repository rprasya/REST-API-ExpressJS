# Membuat REST API Menggunakan ExpressJS

Panduan ini akan memandu Anda melalui proses pembuatan REST API sederhana menggunakan ExpressJS dan MySQL.

## Langkah-langkah

1. **Inisialisasi Proyek:**
    ```bash
    npm init -y
    ```
2. **Instalasi Dependencies:**
    ```bash
    npm install express mysql2 nodemon
    ```
3. **Pengaturan `package.json`:**
    Tambahkan script berikut di `package.json` untuk menjalankan server dengan nodemon:
    ```json
    "scripts": {
      "start": "nodemon index.js"
    }
    ```
4. **Membuat File `index.js`:**
    Di dalam `index.js`, import express dan buat instance aplikasi:
    ```javascript
    const express = require('express');
    const app = express();
    app.listen(XXXX, () => {
        console.log('Server running di port XXXX');
    });
    ```
5. **Membuat Middleware Contoh:**
    ```javascript
    app.use("/", (req, res, next) => {
        res.send('Ini middleware');
    });
    ```
6. **Merapikan Struktur Folder:**
    - `routes/`: Menyimpan semua file yang berfungsi untuk routing.
    - `controller/`: Menyimpan fungsi yang digunakan di setiap routes.
    - `middleware/`: Menyimpan fungsi middleware.
    - `models/`: Menyimpan fungsi untuk melakukan query ke database.
    - `config/`: Menyimpan konfigurasi database.

7. **Membuat Routing `users`:**
    Buat file `users.js` di dalam folder `routes`:
    ```javascript
    const express = require('express');
    const router = express.Router();

    router.get("/", (req, res) => {
        res.json({
            message: "GET all users success",
        });
    });

    module.exports = router;
    ```

8. **Menghubungkan Routing ke `index.js`:**
    ```javascript
    const usersRoutes = require('./routes/users.js');
    app.use("/users", usersRoutes);
    ```

9. **Merapikan Function Routing:**
    Pindahkan fungsi di dalam routes ke dalam folder `controller`:
    ```javascript
    // controllers/users.js
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

10. **Menghubungkan Controller di Routing:**
    ```javascript
    const UserController = require("../controller/users");

    router.get("/", UserController.getAllUsers);
    router.post("/", UserController.createNewUsers);
    ```

11. **Membuat Middleware untuk Logging:**
    ```javascript
    const logRequest = (req, res, next) => {
        console.log("Log request", req.path);
        next();
    };

    app.use(logRequest);
    ```

12. **Mengizinkan Request Body JSON:**
    Tambahkan middleware berikut di `index.js` untuk mengizinkan parsing JSON:
    ```javascript
    app.use(express.json());
    ```

13. **Mengintegrasikan Database:**
    Buat konfigurasi database:
    ```javascript
    const mysql = require('mysql2');
    const pool = mysql.createPool({
        host: 'localhost',
        user: 'root',
        password: 'password',
        database: 'your_database_name'
    });

    module.exports = pool.promise();
    ```

14. **Membuat Model untuk Query Database:**
    Buat file `models/users.js`:
    ```javascript
    const dbPool = require("../config/database");

    const getAllUsers = () => {
        const SQLQuery = "SELECT * FROM users";
        return dbPool.execute(SQLQuery);
    };

    module.exports = {
        getAllUsers,
    };
    ```

15. **Menggunakan Model di Controller:**
    ```javascript
    const UsersModel = require('../models/users');

    const getAllUser = async (req, res) => {
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
    ```

16. **Mengelola Variabel Lingkungan (Environment Variables):**
    Instal `dotenv` dan buat file `.env` untuk menyimpan konfigurasi sensitif:
    ```bash
    npm install dotenv
    ```
    Di `index.js`, import dan konfigurasi dotenv:
    ```javascript
    require('dotenv').config();
    const PORT = process.env.PORT || 9000;
    ```

17. **Memindahkan Konfigurasi Database ke `.env`:**
    Contoh isi `.env`:
    ```
    PORT=XXXX
    DB_HOST=localhost
    DB_USERNAME=root
    DB_PASSWORD=password
    DB_NAME=your_database_name
    ```

18. **Membuat CRUD di Model:**
    - **CREATE**:
      ```javascript
      const createUser = (body) => {
          const SQLQuery = `INSERT INTO users (name, email, address) 
                            VALUES ('${body.name}','${body.email}','${body.address}')`;
          return dbPool.execute(SQLQuery);
      };
      ```
    - **UPDATE**:
      ```javascript
      const updateUser = (body, idUser) => {
          const SQLQuery = `UPDATE users 
                            SET name='${body.name}', email='${body.email}', address='${body.address}' 
                            WHERE id=${idUser}`;
          return dbPool.execute(SQLQuery);
      };
      ```
    - **DELETE**:
      ```javascript
      const deleteUser = (idUser) => {
          const SQLQuery = `DELETE FROM users WHERE id=${idUser}`;
          return dbPool.execute(SQLQuery);
      };
      ```

19. **Menjalankan dan Menguji API:**
    Jalankan server:
    ```bash
    npm start
    ```
    Uji endpoint dengan Postman atau aplikasi lain untuk memastikan semua CRUD operation berfungsi dengan baik.

20. **Mengabaikan `.env` dari Git:**
    Tambahkan `.env` ke `.gitignore` dan buat file dummy `env.example` untuk di-commit ke repository.


Anda telah berhasil membuat REST API sederhana menggunakan ExpressJS dan MySQL, dengan implementasi CRUD. Semoga bermanfaat KEEP CODING AND FUN!

## Authors

- [@rprasya](https://github.com/rprasya)
