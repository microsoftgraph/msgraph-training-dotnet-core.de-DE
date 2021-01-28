<!-- markdownlint-disable MD002 MD041 -->

Erstellen Sie zunächst mithilfe der .NET Core CLI ein [neues .NET Core-Konsolenprojekt.](/dotnet/core/tools/)

1. Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten. Führen Sie den folgenden Befehl aus.

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. Nachdem das Projekt erstellt wurde, überprüfen Sie, ob es funktioniert, indem Sie das aktuelle Verzeichnis in das Verzeichnis **"GraphTutorial"** ändern und den folgenden Befehl in Ihrer CLI ausführen.

    ```Shell
    dotnet run
    ```

    Wenn dies funktioniert, sollte die App ausgabe `Hello World!` .

## <a name="install-dependencies"></a>Installieren von Abhängigkeiten

Fügen Sie vor dem Wechsel einige zusätzliche Abhängigkeiten hinzu, die Sie später verwenden werden.

- [Microsoft.Extensions.Configuration. UserSecrets](https://github.com/aspnet/extensions) zum Lesen der Anwendungskonfiguration aus [dem geheimen .NET-Entwicklungsspeicher.](https://docs.microsoft.com/aspnet/core/security/app-secrets)
- [Microsoft Authentication Library (MSAL) für .NET,](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) um den Benutzer zu authentifizieren und Zugriffstoken zu erwerben.
- [Microsoft Graph .NET-Clientbibliothek,](https://github.com/microsoftgraph/msgraph-sdk-dotnet) um Aufrufe an Microsoft Graph zu senden.
- [TimeZoneConverter zum](https://github.com/mj1856/TimeZoneConverter) Übersetzen von Windows-Zeitzonen-IDs in IANA-IDs.

Führen Sie die folgenden Befehle in Ihrer CLI aus, um die Abhängigkeiten zu installieren.

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 5.0.0
dotnet add package Microsoft.Identity.Client --version 4.25.0
dotnet add package Microsoft.Graph --version 3.22.0
dotnet add package TimeZoneConverter
```

## <a name="design-the-app"></a>Entwerfen der App

In diesem Abschnitt erstellen Sie ein einfaches konsolenbasiertes Menü.

Öffnen **Sie ./Program.cs** in einem Texteditor (z. [B. Visual Studio Code),](https://code.visualstudio.com/)und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

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

Dadurch wird ein einfaches Menü implementiert und die Auswahl des Benutzers über die Befehlszeile gelesen.
