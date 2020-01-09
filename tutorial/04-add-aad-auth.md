<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="256bc-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="256bc-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="256bc-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="256bc-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="256bc-103">In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="256bc-103">In this step you will integrate the [Microsoft Authentication Library (MSAL) for .NET](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) into the application.</span></span>

<span data-ttu-id="256bc-104">Initialisieren Sie den [geheimen .net-entwicklungsspeicher](https://docs.microsoft.com/aspnet/core/security/app-secrets) , indem Sie die CLI in dem Verzeichnis öffnen, das **GraphTutorial. csproj** enthält, und führen Sie den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="256bc-104">Initialize the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

```Shell
dotnet user-secrets init
```

<span data-ttu-id="256bc-105">Als Nächstes fügen Sie die Anwendungs-ID und eine Liste der erforderlichen Bereiche dem geheimen Speicher mithilfe der folgenden Befehle hinzu.</span><span class="sxs-lookup"><span data-stu-id="256bc-105">Next, add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="256bc-106">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID, die Sie im Azure-Portal erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="256bc-106">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

```Shell
dotnet user-secrets set appId "YOUR_APP_ID_HERE"
dotnet user-secrets set scopes "User.Read;Calendars.Read"
```

## <a name="implement-sign-in"></a><span data-ttu-id="256bc-107">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="256bc-107">Implement sign-in</span></span>

<span data-ttu-id="256bc-108">In diesem Abschnitt erstellen Sie einen Authentifizierungsanbieter, der mit dem Graph-SDK verwendet werden kann und auch verwendet werden kann, um ein Zugriffstoken mithilfe des [Gerätecode Flusses](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)explizit anzufordern.</span><span class="sxs-lookup"><span data-stu-id="256bc-108">In this section you will create an authentication provider that can be used with the Graph SDK and can also be used to explicitly request an access token by using the [device code flow](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code).</span></span>

### <a name="create-an-authentication-provider"></a><span data-ttu-id="256bc-109">Erstellen eines Authentifizierungsanbieters</span><span class="sxs-lookup"><span data-stu-id="256bc-109">Create an authentication provider</span></span>

1. <span data-ttu-id="256bc-110">Erstellen Sie ein neues Verzeichnis im **GraphTutorial** -Verzeichnis mit dem Namen **Authentication**.</span><span class="sxs-lookup"><span data-stu-id="256bc-110">Create a new directory in the **GraphTutorial** directory named **Authentication**.</span></span>
1. <span data-ttu-id="256bc-111">Erstellen Sie eine neue Datei im **Authentifizierungs** Verzeichnis mit dem Namen **DeviceCodeAuthProvider.cs** , und fügen Sie der Datei den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="256bc-111">Create a new file in the **Authentication** directory named **DeviceCodeAuthProvider.cs** and add the following code to that file.</span></span>

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

<span data-ttu-id="256bc-112">Überprüfen Sie die Funktionsweise dieses Codes.</span><span class="sxs-lookup"><span data-stu-id="256bc-112">Consider what this code does.</span></span>

- <span data-ttu-id="256bc-113">Es verwendet die MSAL `IPublicClientApplication` -Implementierung, um Token anzufordern und zu verwalten.</span><span class="sxs-lookup"><span data-stu-id="256bc-113">It uses the MSAL `IPublicClientApplication` implementation to request and manage tokens.</span></span>
- <span data-ttu-id="256bc-114">Die `GetAccessToken` Funktion:</span><span class="sxs-lookup"><span data-stu-id="256bc-114">The `GetAccessToken` function:</span></span>
  - <span data-ttu-id="256bc-115">Meldet den Benutzer an, wenn er nicht bereits mit dem Gerätecode Fluss angemeldet ist.</span><span class="sxs-lookup"><span data-stu-id="256bc-115">Signs in the user if they are not already signed in using the device code flow.</span></span>
  - <span data-ttu-id="256bc-116">Stellt sicher, dass das zurückgegebene Token immer `AcquireTokenSilent` neu ist, indem es die-Funktion verwendet, die das zwischengespeicherte Token zurückgibt, wenn es nicht abgelaufen ist, und das Token aktualisiert, wenn es abgelaufen ist.</span><span class="sxs-lookup"><span data-stu-id="256bc-116">Ensures that the token returned is always fresh by using the `AcquireTokenSilent` function, which returns the cached token if it not expired, and refreshes the token if it is expired.</span></span>
- <span data-ttu-id="256bc-117">Die `IAuthenticationProvider` Schnittstelle wird implementiert, sodass das Graph-SDK die Klasse zum Authentifizieren von Graph-aufrufen verwenden kann.</span><span class="sxs-lookup"><span data-stu-id="256bc-117">It implements the `IAuthenticationProvider` interface so that the Graph SDK can use the class to authenticate Graph calls.</span></span>

## <a name="sign-in-and-display-the-access-token"></a><span data-ttu-id="256bc-118">Anmelden und Anzeigen des Zugriffstokens</span><span class="sxs-lookup"><span data-stu-id="256bc-118">Sign in and display the access token</span></span>

<span data-ttu-id="256bc-119">In diesem Abschnitt aktualisieren Sie die Anwendung so, dass die `GetAccessToken` Funktion aufgerufen wird, die sich am Benutzer anmeldet.</span><span class="sxs-lookup"><span data-stu-id="256bc-119">In this section you will update the application to call the `GetAccessToken` function, which will sign in the user.</span></span> <span data-ttu-id="256bc-120">Sie fügen auch Code hinzu, um das Token anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="256bc-120">You will also add code to display the token.</span></span>

1. <span data-ttu-id="256bc-121">Öffnen Sie **Program.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="256bc-121">Open **Program.cs** and add the following `using` statements to the top of the file.</span></span>

    ```csharp
    using Microsoft.Extensions.Configuration;
    ```

1. <span data-ttu-id="256bc-122">Fügen Sie die folgende Funktion zur `Program`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="256bc-122">Add the following function to the `Program` class.</span></span>

    ```csharp
    static IConfigurationRoot LoadAppSettings()
    {
        var appConfig = new ConfigurationBuilder()
            .AddUserSecrets<Program>()
            .Build();

        // Check for required settings
        if (string.IsNullOrEmpty(appConfig["appId"]) ||
            string.IsNullOrEmpty(appConfig["scopes"]))
        {
            return null;
        }

        return appConfig;
    }
    ```

1. <span data-ttu-id="256bc-123">Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `Console.WriteLine(".NET Core Graph Tutorial\n");` -Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="256bc-123">Add the following code to the `Main` function immediately after the `Console.WriteLine(".NET Core Graph Tutorial\n");` line.</span></span>

    ```csharp
    var appConfig = LoadAppSettings();

    if (appConfig == null)
    {
        Console.WriteLine("Missing or invalid appsettings.json...exiting");
        return;
    }

    var appId = appConfig["appId"];
    var scopesString = appConfig["scopes"];
    var scopes = scopesString.Split(';');

    // Initialize the auth provider with values from appsettings.json
    var authProvider = new DeviceCodeAuthProvider(appId, scopes);

    // Request a token to sign in the user
    var accessToken = authProvider.GetAccessToken().Result;
    ```

1. <span data-ttu-id="256bc-124">Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `// Display access token` -Verbindung hinzu.</span><span class="sxs-lookup"><span data-stu-id="256bc-124">Add the following code to the `Main` function immediately after the `// Display access token` line.</span></span>

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. <span data-ttu-id="256bc-125">Erstellen Sie die APP, und führen Sie Sie aus.</span><span class="sxs-lookup"><span data-stu-id="256bc-125">Build and run the app.</span></span> <span data-ttu-id="256bc-126">Die Anwendung zeigt eine URL und einen Gerätecode an.</span><span class="sxs-lookup"><span data-stu-id="256bc-126">The application displays a URL and device code.</span></span>

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > <span data-ttu-id="256bc-127">Wenn Fehler auftreten, vergleichen Sie Ihr **Program.cs** mit dem [Beispiel auf GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span><span class="sxs-lookup"><span data-stu-id="256bc-127">If you encounter errors, compare your **Program.cs** with the [example on GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).</span></span>

1. <span data-ttu-id="256bc-128">Öffnen Sie einen Browser, und navigieren Sie zu der angezeigten URL.</span><span class="sxs-lookup"><span data-stu-id="256bc-128">Open a browser and browse to the URL displayed.</span></span> <span data-ttu-id="256bc-129">Geben Sie den bereitgestellten Code ein und melden Sie sich an.</span><span class="sxs-lookup"><span data-stu-id="256bc-129">Enter the provided code and sign in.</span></span> <span data-ttu-id="256bc-130">Kehren Sie nach Abschluss zurück zur Anwendung, und wählen Sie die **1 aus. Option Zugriffstoken anzeigen** , um das Zugriffstoken anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="256bc-130">Once completed, return to the application and choose the **1. Display access token** option to display the access token.</span></span>
