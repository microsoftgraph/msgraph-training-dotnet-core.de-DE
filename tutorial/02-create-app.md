<!-- markdownlint-disable MD002 MD041 -->

Erstellen Sie zunächst ein neues .net Core-Konsolenprojekt mithilfe der [.net-Kern-CLI](/dotnet/core/tools/?tabs=netcore2x).

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

- [Microsoft. Extensions. Configuration](https://github.com/aspnet/Extensions) zum Lesen der Anwendungskonfiguration aus einer JSON-Datei.
- [Microsoft Authentication Library (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) , um den Benutzer zu authentifizieren und Zugriffstoken zu erwerben.
- [Microsoft Graph .NET-Client Bibliothek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.
- [Authentifizierungsanbieter für Microsoft Graph .NET SDK](https://github.com/microsoftgraph/msgraph-sdk-dotnet-auth) , um zu ermöglichen, dass die Graph-Clientbibliothek Token beim Erstellen von API-Aufrufen automatisch anfordert.

Führen Sie die folgenden Befehle in der CLI aus, um die Abhängigkeiten zu installieren.

```Shell
dotnet add package Microsoft.Extensions.Configuration --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.FileExtensions --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.Json --version 3.1.0
dotnet add package Microsoft.Extensions.Configuration.Binder --version 3.1.0
dotnet add package Microsoft.Identity.Client --version 4.7.1
dotnet add package Microsoft.Graph --version 1.21.0
```

## <a name="design-the-app"></a>Entwerfen der APP

In diesem Abschnitt wird ein einfaches konsolenbasiertes Menü erstellt.

Öffnen Sie **Program.cs** in einem Text-Editor (beispielsweise [Visual Studio Code](https://code.visualstudio.com/)), und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

[!code-csharp[](../demos/01-create-app/GraphTutorial/Program.cs)]

Dadurch wird ein einfaches Menü implementiert, und die Auswahl des Benutzers wird von der Befehlszeile aus gelesen.
