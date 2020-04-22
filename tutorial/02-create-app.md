<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="fa9ab-101">Erstellen Sie zunächst ein neues .net Core-Konsolenprojekt mithilfe der [.net-Kern-CLI](/dotnet/core/tools/).</span><span class="sxs-lookup"><span data-stu-id="fa9ab-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/).</span></span>

1. <span data-ttu-id="fa9ab-102">Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="fa9ab-103">Führen Sie den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="fa9ab-104">Nachdem das Projekt erstellt wurde, stellen Sie sicher, dass es funktioniert, indem Sie das aktuelle Verzeichnis in das **GraphTutorial** -Verzeichnis ändern und den folgenden Befehl in ihrer CLI ausführen.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="fa9ab-105">Wenn die APP funktioniert, sollte Sie ausgegeben `Hello World!`werden.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="fa9ab-106">Installieren von Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="fa9ab-106">Install dependencies</span></span>

<span data-ttu-id="fa9ab-107">Bevor Sie fortfahren, fügen Sie einige zusätzliche Abhängigkeiten hinzu, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="fa9ab-108">[Microsoft. Extensions. Configuration. UserSecrets](https://github.com/aspnet/extensions) zum Lesen der Anwendungskonfiguration aus dem [geheimen .net-entwicklungsspeicher](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="fa9ab-108">[Microsoft.Extensions.Configuration.UserSecrets](https://github.com/aspnet/extensions) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="fa9ab-109">[Microsoft Authentication Library (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) , um den Benutzer zu authentifizieren und Zugriffstoken zu erwerben.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="fa9ab-110">[Microsoft Graph .NET-Client Bibliothek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>

<span data-ttu-id="fa9ab-111">Führen Sie die folgenden Befehle in der CLI aus, um die Abhängigkeiten zu installieren.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-111">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.2
dotnet add package Microsoft.Identity.Client --version 4.10.0
dotnet add package Microsoft.Graph --version 3.0.1
```

## <a name="design-the-app"></a><span data-ttu-id="fa9ab-112">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="fa9ab-112">Design the app</span></span>

<span data-ttu-id="fa9ab-113">In diesem Abschnitt wird ein einfaches konsolenbasiertes Menü erstellt.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-113">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="fa9ab-114">Öffnen Sie **Program.cs** in einem Text-Editor (beispielsweise [Visual Studio Code](https://code.visualstudio.com/)), und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-114">Open **Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

```csharp
using Microsoft.Extensions.Configuration;
using System;

namespace GraphTutorial
{
    class Program
    {
        static void Main(string[] args)
        {
            Console.WriteLine(".NET Core Graph Tutorial\n");

            int choice = -1;

            while (choice != 0) {
                Console.WriteLine("Please choose one of the following options:");
                Console.WriteLine("0. Exit");
                Console.WriteLine("1. Display access token");
                Console.WriteLine("2. List calendar events");

                try
                {
                    choice = int.Parse(Console.ReadLine());
                }
                catch (System.FormatException)
                {
                    // Set to invalid value
                    choice = -1;
                }

                switch(choice)
                {
                    case 0:
                        // Exit the program
                        Console.WriteLine("Goodbye...");
                        break;
                    case 1:
                        // Display access token
                        break;
                    case 2:
                        // List the calendar
                        break;
                    default:
                        Console.WriteLine("Invalid choice! Please try again.");
                        break;
                }
            }
        }
    }
}
```

<span data-ttu-id="fa9ab-115">Dadurch wird ein einfaches Menü implementiert, und die Auswahl des Benutzers wird von der Befehlszeile aus gelesen.</span><span class="sxs-lookup"><span data-stu-id="fa9ab-115">This implements a basic menu and reads the user's choice from the command line.</span></span>
