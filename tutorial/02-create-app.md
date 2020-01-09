<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="f9ca8-101">Erstellen Sie zunächst ein neues .net Core-Konsolenprojekt mithilfe der [.net-Kern-CLI](/dotnet/core/tools/?tabs=netcore2x).</span><span class="sxs-lookup"><span data-stu-id="f9ca8-101">Begin by creating a new .NET Core console project using the [.NET Core CLI](/dotnet/core/tools/?tabs=netcore2x).</span></span>

1. <span data-ttu-id="f9ca8-102">Öffnen Sie die Befehlszeilenschnittstelle (CLI) in einem Verzeichnis, in dem Sie das Projekt erstellen möchten.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-102">Open your command-line interface (CLI) in a directory where you want to create the project.</span></span> <span data-ttu-id="f9ca8-103">Führen Sie den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-103">Run the following command.</span></span>

    ```Shell
    dotnet new console -o GraphTutorial
    ```

1. <span data-ttu-id="f9ca8-104">Nachdem das Projekt erstellt wurde, stellen Sie sicher, dass es funktioniert, indem Sie das aktuelle Verzeichnis in das **GraphTutorial** -Verzeichnis ändern und den folgenden Befehl in ihrer CLI ausführen.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-104">Once the project is created, verify that it works by changing the current directory to the **GraphTutorial** directory and running the following command in your CLI.</span></span>

    ```Shell
    dotnet run
    ```

    <span data-ttu-id="f9ca8-105">Wenn die APP funktioniert, sollte Sie ausgegeben `Hello World!`werden.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-105">If it works, the app should output `Hello World!`.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="f9ca8-106">Installieren von Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="f9ca8-106">Install dependencies</span></span>

<span data-ttu-id="f9ca8-107">Bevor Sie fortfahren, fügen Sie einige zusätzliche Abhängigkeiten hinzu, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-107">Before moving on, add some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="f9ca8-108">[Microsoft. Extensions. Configuration. UserSecrets](https://github.com/aspnet/extensions) zum Lesen der Anwendungskonfiguration aus dem [geheimen .net-entwicklungsspeicher](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span><span class="sxs-lookup"><span data-stu-id="f9ca8-108">[Microsoft.Extensions.Configuration.UserSecrets](https://github.com/aspnet/extensions) to read application configuration from the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets).</span></span>
- <span data-ttu-id="f9ca8-109">[Microsoft Authentication Library (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) , um den Benutzer zu authentifizieren und Zugriffstoken zu erwerben.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-109">[Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) to authenticate the user and acquire access tokens.</span></span>
- <span data-ttu-id="f9ca8-110">[Microsoft Graph .NET-Client Bibliothek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-110">[Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to the Microsoft Graph.</span></span>

<span data-ttu-id="f9ca8-111">Führen Sie die folgenden Befehle in der CLI aus, um die Abhängigkeiten zu installieren.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-111">Run the following commands in your CLI to install the dependencies.</span></span>

```Shell
dotnet add package Microsoft.Extensions.Configuration.UserSecrets --version 3.1.0
dotnet add package Microsoft.Identity.Client --version 4.7.1
dotnet add package Microsoft.Graph --version 1.21.0
```

## <a name="design-the-app"></a><span data-ttu-id="f9ca8-112">Entwerfen der APP</span><span class="sxs-lookup"><span data-stu-id="f9ca8-112">Design the app</span></span>

<span data-ttu-id="f9ca8-113">In diesem Abschnitt wird ein einfaches konsolenbasiertes Menü erstellt.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-113">In this section you will create a simple console-based menu.</span></span>

<span data-ttu-id="f9ca8-114">Öffnen Sie **Program.cs** in einem Text-Editor (beispielsweise [Visual Studio Code](https://code.visualstudio.com/)), und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-114">Open **Program.cs** in a text editor (such as [Visual Studio Code](https://code.visualstudio.com/)) and replace its entire contents with the following code.</span></span>

[!code-csharp[](../demos/01-create-app/GraphTutorial/Program.cs)]

<span data-ttu-id="f9ca8-115">Dadurch wird ein einfaches Menü implementiert, und die Auswahl des Benutzers wird von der Befehlszeile aus gelesen.</span><span class="sxs-lookup"><span data-stu-id="f9ca8-115">This implements a basic menu and reads the user's choice from the command line.</span></span>
