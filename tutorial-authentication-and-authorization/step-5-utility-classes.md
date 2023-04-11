# Step 5: Utility Classes

We are next going to create a simple utility class for creating and deleting our development database running on SQLite. We are going to call it **DatabaseSetup.cs**. See below:

## `DatabaseSetup`.cs

`Location: src/Persistence/DatabaseSetup.cs`

````csharp
```csharp
using System;

using System.Data.SQLite;

namespace Persistence{

    public class DatabaseSetup{
    
        SQLiteConnection connection;

        public DatabaseSetup(){
            this.connection = new SQLiteConnection("Data Source=system.db;Version=3;New=True");
        }

        public void setup(){
            connection.Open();

            var command = connection.CreateCommand();
            command.CommandText =
            @"
                CREATE TABLE users (
                    id integer not null primary key autoincrement,
                    email text not null,
                    password text not null
                );
                CREATE TABLE roles (
                    id integer not null primary key autoincrement,
                    description text not null
                );
                CREATE TABLE user_roles (
                    id integer not null primary key autoincrement,
                    user_id integer,
                    role_id integer
                );
                CREATE TABLE user_permissions (
                    id integer not null primary key autoincrement,
                    user_id integer,
                    permission text not null
                );
                insert into users (id, email, password) values (1, 'abc@plsar.net', 'effort.');
                insert into roles (id, description) values (1, 'super-role');
                insert into user_roles (id, user_id, role_id) values (1, 1, 1);
                insert into user_permissions (id, user_id, permission) values (1, 1, 'users:maintenance:1');
            ";
            command.ExecuteNonQuery();

            connection.Close();
        }    
        public DatabaseSetup clean(){
            File.Delete("system.db");
            return this;
        }
        public SQLiteConnection getConnection(){
            return this.connection;
        }
    }
}
```
````



## `AuthAccess`.cs

`Location: src/Persistence/AuthAcccess.cs`

The AuthAccess class acts like an intermediary to Skylines SecurityManager. The SecurityManager relies on this class to determine whether or not someone is authenticated, has certain roles or permissions.

````csharp
```csharp
using System;
using System.Collections;

using Skyline;
using Skyline.Model;
using Skyline.Implement;

using Persistence.Model;
using Persistence.Repo;

namespace Persistence {

    public class AuthAccess : SecurityAccess {
        
        UserRepo userRepo;
        DataTransferObject dto;

        public AuthAccess(){}

        public AuthAccess(DataTransferObject dto){
            this.userRepo = new UserRepo(dto);
        }
        
        public String getPassword(String email){
            User user = userRepo.getEmail(email);
            if(user != null){
                return user.getPassword();
            }
            return "";
        }

        public HashSet<String> getRoles(String email){
            User user = userRepo.getEmail(email);
            return userRepo.getRoles(user);
        }

        public HashSet<String> getPermissions(String email){
            User user = userRepo.getEmail(email);
            return userRepo.getPermissions(user);
        }
    }
}
```
````

