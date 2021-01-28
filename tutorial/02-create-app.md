<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f2b16-101">Erstellen Sie zunächst mithilfe der .NET Core CLI ein [neues .NET Core-Konsolenprojekt.](/dotnet/core/tools/)</span><span class="sxs-lookup"><span data-stu-id="f2b16-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/).</span></span>

1. <span data-ttu-id="f2b16-102">Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="f2b16-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="f2b16-103">Führen Sie den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="f2b16-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="f2b16-104">Nachdem das Projekt erstellt wurde, überprüfen Sie, ob es funktioniert, indem Sie das aktuelle Verzeichnis in das Verzeichnis **"GraphTutorial"** ändern und den folgenden Befehl in Ihrer CLI ausführen.</span><span class="sxs-lookup"><span data-stu-id="f2b16-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="f2b16-105">Wenn dies funktioniert, sollte die App ausgabe `Hello World!` .</span><span class="sxs-lookup"><span data-stu-id="f2b16-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="f2b16-106">Installieren von Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="f2b16-106">Install dependencies</span></span>

<span data-ttu-id="f2b16-107">Fügen Sie vor dem Wechsel einige zusätzliche Abhängigkeiten hinzu, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="f2b16-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="f2b16-108">[Microsoft.Extensions.Configuration. UserSecrets](https://github.com/aspnet/extensions) zum Lesen der Anwendungskonfiguration aus [dem geheimen .NET-Entwicklungsspeicher.](https://docs.microsoft.com/aspnet/core/security/app-secrets)</span><span class="sxs-lookup"><span data-stu-id="f2b16-108">[Microsoft.Extensions.Configuration.UserSecrets](https://github.com/aspnet/extensions) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="f2b16-109">[Microsoft Authentication Library (MSAL) für .NET,](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) um den Benutzer zu authentifizieren und Zugriffstoken zu erwerben.</span><span class="sxs-lookup"><span data-stu-id="f2b16-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="f2b16-110">[Microsoft Graph .NET-Clientbibliothek,](https://github.com/microsoftgraph/msgraph-sdk-dotnet) um Aufrufe an Microsoft Graph zu senden.</span><span class="sxs-lookup"><span data-stu-id="f2b16-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>
- <span data-ttu-id="f2b16-111">[TimeZoneConverter zum](https://github.com/mj1856/TimeZoneConverter) Übersetzen von Windows-Zeitzonen-IDs in IANA-IDs.</span><span class="sxs-lookup"><span data-stu-id="f2b16-111">[TimeZoneConverter](https://github.com/mj1856/TimeZoneConverter) for translating Windows time zone identifiers to IANA identifiers.</span></span>

<span data-ttu-id="f2b16-112">Führen Sie die folgenden Befehle in Ihrer CLI aus, um die Abhängigkeiten zu installieren.</span><span class="sxs-lookup"><span data-stu-id="f2b16-112">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 5.0.0
dotnet add package Microsoft.Identity.Client --version 4.25.0
dotnet add package Microsoft.Graph --version 3.22.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a><span data-ttu-id="f2b16-113">Entwerfen der App</span><span class="sxs-lookup"><span data-stu-id="f2b16-113">Design the app</span></span>

<span data-ttu-id="f2b16-114">In diesem Abschnitt erstellen Sie ein einfaches konsolenbasiertes Menü.</span><span class="sxs-lookup"><span data-stu-id="f2b16-114">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="f2b16-115">Öffnen **Sie ./Program.cs** in einem Texteditor (z. [B. Visual Studio Code),](https://code.visualstudio.com/)und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="f2b16-115">Open **./Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

```csharp
using Microsoft.Extensions.Configuration;
using System;
using System.Collections.Generic;

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
                Console.WriteLine("2. View this week's calendar");
                Console.WriteLine("3. Add an event");

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
                    case 3:
                        // Create a new event
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

<span data-ttu-id="f2b16-116">Dadurch wird ein einfaches Menü implementiert und die Auswahl des Benutzers über die Befehlszeile gelesen.</span><span class="sxs-lookup"><span data-stu-id="f2b16-116">This implements a basic menu and reads the user's choice from the command line.</span></span>
