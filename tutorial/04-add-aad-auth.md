<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="ec710-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="ec710-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="ec710-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="ec710-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="ec710-103">In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="ec710-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="ec710-104">Erstellen Sie eine neue Datei im **GraphTutorial** -Verzeichnis mit dem Namen **appSettings. JSON**.</span><span class="sxs-lookup"><span data-stu-id="ec710-104">Create a new file in the **GraphTutorial** directory named **appsettings.json**.</span></span> <span data-ttu-id="ec710-105">Fügen Sie den folgenden Text in die Datei ein.</span><span class="sxs-lookup"><span data-stu-id="ec710-105">Add the following text in that file.</span></span>

```json
{
  "appId": "YOUR_APP_ID_HERE",
  "scopes": [
    "User.Read",
    "Calendars.Read"
  ]
}
```

<span data-ttu-id="ec710-106">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID, die Sie im Azure-Portal erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="ec710-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

> [!IMPORTANT]
> <span data-ttu-id="ec710-107">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei **appSettings. JSON** aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="ec710-107">If you're using source control such as git, now would be a good time to exclude the **appsettings.json** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="ec710-108">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="ec710-108">Implement sign-in</span></span>

<span data-ttu-id="ec710-109">In diesem Abschnitt erstellen Sie einen Authentifizierungsanbieter, der mit dem Graph-SDK verwendet werden kann und auch verwendet werden kann, um ein Zugriffstoken mithilfe des [Gerätecode Flusses](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)explizit anzufordern.</span><span class="sxs-lookup"><span data-stu-id="ec710-109">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="ec710-110">Erstellen eines Authentifizierungsanbieters</span><span class="sxs-lookup"><span data-stu-id="ec710-110">Create an authentication provider</span></span>

1. <span data-ttu-id="ec710-111">Erstellen Sie ein neues Verzeichnis im **GraphTutorial** -Verzeichnis mit dem Namen **Authentication**.</span><span class="sxs-lookup"><span data-stu-id="ec710-111">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="ec710-112">Erstellen Sie eine neue Datei im **Authentifizierungs** Verzeichnis mit dem Namen **DeviceCodeAuthProvider.cs** , und fügen Sie der Datei den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec710-112">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using Microsoft.Identity.Client;
    using System;
    using System.Net.Http;
    using System.Net.Http.Headers;
    using System.Threading.Tasks;

    namespace GraphTutorial
    {
        public class DeviceCodeAuthProvider : IAuthenticationProvider
        {
            private IPublicClientApplication _msalClient;
            private string[] _scopes;
            private IAccount _userAccount;

            public DeviceCodeAuthProvider(string appId, string[] scopes)
            {
                _scopes = scopes;

                _msalClient = PublicClientApplicationBuilder
                    .Create(appId)
                    .WithAuthority(AadAuthorityAudience.AzureAdAndPersonalMicrosoftAccount, true)
                    .Build();
            }

            public async Task<string> GetAccessToken()
            {
                // If there is no saved user account, the user must sign-in
                if (_userAccount == null)
                {
                    try
                    {
                        // Invoke device code flow so user can sign-in with a browser
                        var result = await _msalClient.AcquireTokenWithDeviceCode(_scopes, callback => {
                            Console.WriteLine(callback.Message);
                            return Task.FromResult(0);
                        }).ExecuteAsync();

                        _userAccount = result.Account;
                        return result.AccessToken;
                    }
                    catch (Exception exception)
                    {
                        Console.WriteLine($"Error getting access token: {exception.Message}");
                        return null;
                    }
                }
                else
                {
                    // If there is an account, call AcquireTokenSilent
                    // By doing this, MSAL will refresh the token automatically if
                    // it is expired. Otherwise it returns the cached token.

                        var result = await _msalClient
                            .AcquireTokenSilent(_scopes, _userAccount)
                            .ExecuteAsync();

                       return result.AccessToken;
                }
            }

            // This is the required function to implement IAuthenticationProvider
            // The Graph SDK will call this function each time it makes a Graph
            // call.
            public async Task AuthenticateRequestAsync(HttpRequestMessage requestMessage)
            {
                requestMessage.Headers.Authorization =
                    new AuthenticationHeaderValue("bearer", await GetAccessToken());
            }
        }
    }
    ```

<span data-ttu-id="ec710-113">Überprüfen Sie die Funktionsweise dieses Codes.</span><span class="sxs-lookup"><span data-stu-id="ec710-113">Consider what this code does.</span></span>

- <span data-ttu-id="ec710-114">Es verwendet die MSAL `IPublicClientApplication` -Implementierung, um Token anzufordern und zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="ec710-114">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="ec710-115">Die `GetAccessToken` Funktion:</span><span class="sxs-lookup"><span data-stu-id="ec710-115">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="ec710-116">Meldet den Benutzer an, wenn er nicht bereits mit dem Gerätecode Fluss angemeldet ist.</span><span class="sxs-lookup"><span data-stu-id="ec710-116">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="ec710-117">Stellt sicher, dass das zurückgegebene Token immer `AcquireTokenSilent` neu ist, indem es die-Funktion verwendet, die das zwischengespeicherte Token zurückgibt, wenn es nicht abgelaufen ist, und das Token aktualisiert, wenn es abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="ec710-117">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="ec710-118">Die `IAuthenticationProvider` Schnittstelle wird implementiert, sodass das Graph-SDK die Klasse zum Authentifizieren von Graph-aufrufen verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="ec710-118">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="ec710-119">Anmelden und Anzeigen des Zugriffstokens</span><span class="sxs-lookup"><span data-stu-id="ec710-119">Sign in and display the access token</span></span>

<span data-ttu-id="ec710-120">In diesem Abschnitt aktualisieren Sie die Anwendung so, dass die `GetAccessToken` Funktion aufgerufen wird, die sich am Benutzer anmeldet.</span><span class="sxs-lookup"><span data-stu-id="ec710-120">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="ec710-121">Sie fügen auch Code hinzu, um das Token anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ec710-121">You will also add code to display the token.</span></span>

1. <span data-ttu-id="ec710-122">Öffnen Sie **Program.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec710-122">Open **Program.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Microsoft.Extensions.Configuration;
    using System.IO;
    ```

1. <span data-ttu-id="ec710-123">Fügen Sie die folgende Funktion zur `Program`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="ec710-123">Add the following function to the `Program` class.</span></span>

    ```csharp
    static IConfigurationRoot LoadAppSettings()
    {
        var appConfig = new ConfigurationBuilder()
            .SetBasePath(Directory.GetCurrentDirectory())
            .AddJsonFile("appsettings.json", false, true)
            .Build();

        // Check for required settings
        if (string.IsNullOrEmpty(appConfig["appId"]) ||
            // Make sure there's at least one value in the scopes array
            string.IsNullOrEmpty(appConfig["scopes:0"]))
        {
            return null;
        }

        return appConfig;
    }
    ```

1. <span data-ttu-id="ec710-124">Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `Console.WriteLine(".NET Core Graph Tutorial\n");` -Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec710-124">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    ```csharp
    var appConfig = LoadAppSettings();

    if (appConfig == null)
    {
        Console.WriteLine("Missing or invalid appsettings.json...exiting");
        return;
    }

    var appId = appConfig["appId"];
    var scopes = appConfig.GetSection("scopes").Get<string[]>();

    // Initialize the auth provider with values from appsettings.json
    var authProvider = new DeviceCodeAuthProvider(appId, scopes);

    // Request a token to sign in the user
    var accessToken = authProvider.GetAccessToken().Result;
    ```

1. <span data-ttu-id="ec710-125">Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `// Display access token` -Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="ec710-125">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="ec710-126">Erstellen Sie die APP, und führen Sie Sie aus.</span><span class="sxs-lookup"><span data-stu-id="ec710-126">Build and run the app.</span></span> <span data-ttu-id="ec710-127">Die Anwendung zeigt eine URL und einen Gerätecode an.</span><span class="sxs-lookup"><span data-stu-id="ec710-127">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="ec710-128">Wenn Fehler auftreten, vergleichen Sie Ihr **Program.cs** mit dem [Beispiel auf GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span><span class="sxs-lookup"><span data-stu-id="ec710-128">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="ec710-129">Öffnen Sie einen Browser, und navigieren Sie zu der angezeigten URL.</span><span class="sxs-lookup"><span data-stu-id="ec710-129">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="ec710-130">Geben Sie den bereitgestellten Code ein und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="ec710-130">Enter the provided code and sign in.</span></span> <span data-ttu-id="ec710-131">Kehren Sie nach Abschluss zurück zur Anwendung, und wählen Sie die **1 aus. Option Zugriffstoken anzeigen** , um das Zugriffstoken anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="ec710-131">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
