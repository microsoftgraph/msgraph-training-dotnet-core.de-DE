<!-- markdownlint-disable MD002 MD041 -->

Erstellen Sie zunächst ein neues .net Core-Konsolenprojekt mithilfe der [.net-Kern-CLI](/dotnet/core/tools/).

1. Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten. Führen Sie den folgenden Befehl aus.

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. Nachdem das Projekt erstellt wurde, stellen Sie sicher, dass es funktioniert, indem Sie das aktuelle Verzeichnis in das **GraphTutorial** -Verzeichnis ändern und den folgenden Befehl in ihrer CLI ausführen.

    ```Shell
    dotnet run
    ```

    Wenn die APP funktioniert, sollte Sie ausgegeben `Hello World!`werden.

## <a name="install-dependencies"></a>Installieren von Abhängigkeiten

Bevor Sie fortfahren, fügen Sie einige zusätzliche Abhängigkeiten hinzu, die Sie später verwenden werden.

- [Microsoft. Extensions. Configuration. UserSecrets](https://github.com/aspnet/extensions) zum Lesen der Anwendungskonfiguration aus dem [geheimen .net-entwicklungsspeicher](https://docs.microsoft.com/aspnet/core/security/app-secrets).
- [Microsoft Authentication Library (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) , um den Benutzer zu authentifizieren und Zugriffstoken zu erwerben.
- [Microsoft Graph .NET-Client Bibliothek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.

Führen Sie die folgenden Befehle in der CLI aus, um die Abhängigkeiten zu installieren.

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.2
dotnet add package Microsoft.Identity.Client --version 4.10.0
dotnet add package Microsoft.Graph --version 3.0.1
```

## <a name="design-the-app"></a>Entwerfen der App

In diesem Abschnitt wird ein einfaches konsolenbasiertes Menü erstellt.

Öffnen Sie **Program.cs** in einem Text-Editor (beispielsweise [Visual Studio Code](https://code.visualstudio.com/)), und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

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

Dadurch wird ein einfaches Menü implementiert, und die Auswahl des Benutzers wird von der Befehlszeile aus gelesen.
