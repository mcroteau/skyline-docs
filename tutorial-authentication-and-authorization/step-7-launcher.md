# Step 7: Launcher

Finally we need our Launcher or Main class to execute the main method with SkylineServer prepared, ready to run.&#x20;

## `Launcher`.cs

`Location: Envato/Launcher.cs`

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

using Model;
using Repo;

class Launcher{

    public static int Main(String[] args){
        
        DatabaseSetup databaseSetup = new DatabaseSetup();
        databaseSetup.clean().setup();
    
        SkylineServer server = new SkylineServer();
        server.setPorts(new Int32[]{2000, 3000, 4000});
        server.setNumberOfRequestNegotiators(1000);
        server.setPersistentMode(true);
        server.setSecurityAccessType(new AuthAccess().GetType());
        server.start();

        return 0;
    }
}
```
````
