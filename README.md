# Home



<figure><img src=".gitbook/assets/identity-icon (3).png" alt=""><figcaption></figcaption></figure>

## Main

```csharp
using Skyline;

namespace RestEasy {
    public class Launcher{
        public static int Main(String[] args){
            SkylineServer server = new SkylineServer(4000);
            server.start();
            return 0;
        }
    }
}
```

First off, you purchased Skyline, and I thank you. I appreciate your support. Forgive me, if you downloaded for Nuget, your version has limited resources available, meaning concurrency is limited greatly. Purchasing the enterprise version has unlimited resources and performance is determined by your CPU. If you are interested in the enterprise version, please feel free to contact me @ mike@ae0n.net.

This site is dedicated to helping you build .Net Systems using Skyline. Skyline is in essence ASP.NET + ASP MVC.NET + IIS packaged into one single composite runnable. You will not need to deploy to the IIS Application server. Running Skyline is as easy as running a simple command:

`$ dotnet run`

That's it!

Let's get started. [Quick Start!](quick-start.md)
