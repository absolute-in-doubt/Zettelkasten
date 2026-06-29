
27-06-2026 18:57

Status:

Tags: [[Mongo DB]] [[Базы данных]]

---
# authentication - Mongo DB

- For a **self-managed MongoDB Server** container, authentication is enabled by starting MongoDB with access control, for example:
    
    - `--auth` on the command line, or
    - `security.authorization: enabled` in the config file
    
- For **`mongodb/mongodb-atlas-local`**, MongoDB documents environment variables for setting the initial root user:
    
    - `MONGODB_INITDB_ROOT_USERNAME`
    - `MONGODB_INITDB_ROOT_PASSWORD`

For example:

```
docker run -e MONGODB_INITDB_ROOT_USERNAME=user -e MONGODB_INITDB_ROOT_PASSWORD=pass -p 27017:27017 mongodb/mongodb-atlas-local
```

And for Docker Compose:

```yaml
environment:  
  - MONGODB_INITDB_ROOT_USERNAME=user  
  - MONGODB_INITDB_ROOT_PASSWORD=pass
```


### Application side

On the application side, authentication is typically implemented through the **MongoDB connection string**.

A standard authenticated connection string looks like this:

```
mongodb://myDatabaseUser:D1fficultP%40ssw0r@localhost:27017/?authSource=admin
```

Important details:

- Put the **username** and **password** in the URI.
- Use `authSource=admin` if the user was created in the `admin` database.
- If the username or password contains special characters like `$ : / ? # [ ] @`, they must be **percent-encoded**.

A typical application-side flow is:

1. **Create the database user**
    
    - Use the root user or an admin user to create an app-specific user.
    - Give that user only the roles it needs.

2. **Store credentials securely**
    
    - Put username/password in environment variables, secrets, or a secret manager.
    - Do **not** hard-code credentials in source code.

3. **Build the connection string from environment variables**
    
    - Example:
    ```env
    MONGODB_URI=mongodb://appUser:appPassword@localhost:27017/mydb?authSource=admin
    ```

4. **Connect from the application using that URI**
    
    - The driver handles the authentication automatically when the URI contains credentials.


If you’re using Docker for local development, the cleanest setup is usually:

- **Container**: MongoDB server
- **Environment variables**: root/admin credentials
- **Application**: separate app user via connection string
- **Secrets management**: `.env` file, Compose environment values, or your app’s secret store


----
#### [[authentication - Mongo DB - Flashcards|Link to flashcards]]



---
### References:

