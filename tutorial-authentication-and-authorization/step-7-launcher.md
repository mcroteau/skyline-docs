# Step 7: Launcher

Finally we need our Launcher or Main class to execute the main method with SkylineServer prepared, ready to run. The 70 is the number of threads or request negotiators on call.

## `Launcher`.cs

`Location: src/Persistence/Launcher.cs`

````csharp
```csharp
using System;
using System.Net;
using System.Net.Sockets;
using System.Text;
using System.IO;
using System.Collections;
using System.Threading;
using System.Data.SQLite;

using Skyline;
using Skyline.Model;
using Skyline.Schemes;
using Skyline.Security;

using Persistence.Model;
using Persistence.Repo;

namespace Persistence{

    class Launcher{

        public static int Main(String[] args){
            DatabaseSetup databaseSetup = new DatabaseSetup();
            databaseSetup.clean().setup();
        
            SkylineServer server = new SkylineServer(4000, 70);
            server.setPersistentMode(true);
            server.setSecurityAccessType(new AuthAccess().GetType());
            server.start();

            return 0;
        }
    }
}


```
````
