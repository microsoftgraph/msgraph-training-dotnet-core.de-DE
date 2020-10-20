<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="0ddf8-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="0ddf8-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="0ddf8-103">In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

1. <span data-ttu-id="0ddf8-104">Initialisieren Sie den [geheimen .net-entwicklungsspeicher](/aspnet/core/security/app-secrets) , indem Sie die CLI in dem Verzeichnis öffnen, das **GraphTutorial. csproj** enthält, und führen Sie den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-104">Initialize the [.NET development secret store](/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="0ddf8-105">Fügen Sie die Anwendungs-ID und eine Liste der erforderlichen Bereiche dem geheimen Speicher mithilfe der folgenden Befehle hinzu.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-105">Add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="0ddf8-106">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID, die Sie im Azure-Portal erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;MailboxSettings.Read;Calendars.ReadWrite"
    ```

    <span data-ttu-id="0ddf8-107">Sehen wir uns die Berechtigungs Bereiche an, die Sie soeben festgelegt haben.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-107">Let's look at the permission scopes you just set.</span></span>

    - <span data-ttu-id="0ddf8-108">**Benutzer. Read** ermöglicht der APP, das Profil des angemeldeten Benutzers zu lesen, um Informationen wie den Anzeigenamen und die e-Mail-Adresse abzurufen.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-108">**User.Read** will allow the app to read the signed-in user's profile to get information such as display name and email address.</span></span>
    - <span data-ttu-id="0ddf8-109">Mit **Mailbox Settings. Read** kann die APP die bevorzugte Zeitzone, das Datumsformat und das Zeitformat des Benutzers lesen.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-109">**MailboxSettings.Read** will allow the app to read the user's preferred time zone, date format, and time format.</span></span>
    - <span data-ttu-id="0ddf8-110">Mit **Calendars. ReadWrite** kann die APP die vorhandenen Ereignisse im Kalender des Benutzers lesen und neue Ereignisse hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-110">**Calendars.ReadWrite** will allow the app to read the existing events on the user's calendar and add new events.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="0ddf8-111">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="0ddf8-111">Implement sign-in</span></span>

<span data-ttu-id="0ddf8-112">In diesem Abschnitt erstellen Sie einen Authentifizierungsanbieter, der mit dem Graph-SDK verwendet werden kann und auch verwendet werden kann, um ein Zugriffstoken mithilfe des [Gerätecode Flusses](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)explizit anzufordern.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-112">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="0ddf8-113">Erstellen eines Authentifizierungsanbieters</span><span class="sxs-lookup"><span data-stu-id="0ddf8-113">Create an authentication provider</span></span>

1. <span data-ttu-id="0ddf8-114">Erstellen Sie ein neues Verzeichnis im **GraphTutorial** -Verzeichnis mit dem Namen **Authentication**.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-114">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="0ddf8-115">Erstellen Sie eine neue Datei im **Authentifizierungs** Verzeichnis mit dem Namen **DeviceCodeAuthProvider.cs** , und fügen Sie der Datei den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-115">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/DeviceCodeAuthProvider.cs" id="AuthProviderSnippet":::

<span data-ttu-id="0ddf8-116">Überprüfen Sie die Funktionsweise dieses Codes.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-116">Consider what this code does.</span></span>

- <span data-ttu-id="0ddf8-117">Es verwendet die MSAL `IPublicClientApplication` -Implementierung, um Token anzufordern und zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-117">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="0ddf8-118">Die `GetAccessToken` Funktion:</span><span class="sxs-lookup"><span data-stu-id="0ddf8-118">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="0ddf8-119">Meldet den Benutzer an, wenn er nicht bereits mit dem Gerätecode Fluss angemeldet ist.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-119">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="0ddf8-120">Stellt sicher, dass das zurückgegebene Token immer neu ist, indem es die- `AcquireTokenSilent` Funktion verwendet, die das zwischengespeicherte Token zurückgibt, wenn es nicht abgelaufen ist, und das Token aktualisiert, wenn es abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-120">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="0ddf8-121">Die `IAuthenticationProvider` Schnittstelle wird implementiert, sodass das Graph-SDK die Klasse zum Authentifizieren von Graph-aufrufen verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-121">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="0ddf8-122">Anmelden und Anzeigen des Zugriffstokens</span><span class="sxs-lookup"><span data-stu-id="0ddf8-122">Sign in and display the access token</span></span>

<span data-ttu-id="0ddf8-123">In diesem Abschnitt aktualisieren Sie die Anwendung so, dass die Funktion aufgerufen wird, die sich `GetAccessToken` am Benutzer anmeldet.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-123">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="0ddf8-124">Sie fügen auch Code hinzu, um das Token anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-124">You will also add code to display the token.</span></span>

1. <span data-ttu-id="0ddf8-125">Fügen Sie die folgende Funktion zur `Program`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="0ddf8-125">Add the following function to the `Program` class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. <span data-ttu-id="0ddf8-126">Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `Console.WriteLine(".NET Core Graph Tutorial\n");` -Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-126">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

1. <span data-ttu-id="0ddf8-127">Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `// Display access token` -Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-127">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="0ddf8-128">Erstellen Sie die APP, und führen Sie Sie aus.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-128">Build and run the app.</span></span> <span data-ttu-id="0ddf8-129">Die Anwendung zeigt eine URL und einen Gerätecode an.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-129">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="0ddf8-130">Wenn Fehler auftreten, vergleichen Sie Ihr **Program.cs** mit dem [Beispiel auf GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span><span class="sxs-lookup"><span data-stu-id="0ddf8-130">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="0ddf8-131">Öffnen Sie einen Browser, und navigieren Sie zu der angezeigten URL.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-131">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="0ddf8-132">Geben Sie den bereitgestellten Code ein und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-132">Enter the provided code and sign in.</span></span> <span data-ttu-id="0ddf8-133">Kehren Sie nach Abschluss zurück zur Anwendung, und wählen Sie die **1 aus. Option Zugriffstoken anzeigen** , um das Zugriffstoken anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="0ddf8-133">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
