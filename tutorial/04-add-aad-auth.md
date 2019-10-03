<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten. In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.

Erstellen Sie eine neue Datei im **GraphTutorial** -Verzeichnis mit dem Namen **appSettings. JSON**. Fügen Sie den folgenden Text in die Datei ein.

```json
{
  "appId": "YOUR_APP_ID_HERE",
  "scopes": [
    "User.Read",
    "Calendars.Read"
  ]
}
```

Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID, die Sie im Azure-Portal erstellt haben.

> [!IMPORTANT]
> Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es jetzt ein guter Zeitpunkt, die Datei **appSettings. JSON** aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

In diesem Abschnitt erstellen Sie einen Authentifizierungsanbieter, der mit dem Graph-SDK verwendet werden kann und auch verwendet werden kann, um ein Zugriffstoken mithilfe des [Gerätecode Flusses](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)explizit anzufordern.

### <a name="create-an-authentication-provider"></a>Erstellen eines Authentifizierungsanbieters

1. Erstellen Sie ein neues Verzeichnis im **GraphTutorial** -Verzeichnis mit dem Namen **Authentication**.
1. Erstellen Sie eine neue Datei im **Authentifizierungs** Verzeichnis mit dem Namen **DeviceCodeAuthProvider.cs** , und fügen Sie der Datei den folgenden Code hinzu.

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
                    // Set the tenant ID to "organizations" to disable personal accounts
                    // Azure OAuth does not support device code flow for personal accounts
                    // See https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code
                    .WithTenantId("organizations")
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

Überprüfen Sie die Funktionsweise dieses Codes.

- Es verwendet die MSAL `IPublicClientApplication` -Implementierung, um Token anzufordern und zu verwalten.
- Die `GetAccessToken` Funktion:
  - Meldet den Benutzer an, wenn er nicht bereits mit dem Gerätecode Fluss angemeldet ist.
  - Stellt sicher, dass das zurückgegebene Token immer `AcquireTokenSilent` neu ist, indem es die-Funktion verwendet, die das zwischengespeicherte Token zurückgibt, wenn es nicht abgelaufen ist, und das Token aktualisiert, wenn es abgelaufen ist.
- Die `IAuthenticationProvider` Schnittstelle wird implementiert, sodass das Graph-SDK die Klasse zum Authentifizieren von Graph-aufrufen verwenden kann.

## <a name="sign-in-and-display-the-access-token"></a>Anmelden und Anzeigen des Zugriffstokens

In diesem Abschnitt aktualisieren Sie die Anwendung so, dass die `GetAccessToken` Funktion aufgerufen wird, die sich am Benutzer anmeldet. Sie fügen auch Code hinzu, um das Token anzuzeigen.

1. Öffnen Sie **Program.cs** , und fügen `using` Sie die folgenden Anweisungen am Anfang der Datei hinzu.

    ```csharp
    using Microsoft.Extensions.Configuration;
    using System.IO;
    ```

1. Fügen Sie die folgende Funktion zur `Program`-Klasse hinzu:

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

1. Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `Console.WriteLine(".NET Core Graph Tutorial\n");` -Verbindung hinzu.

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

1. Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `// Display access token` -Verbindung hinzu.

    ```csharp
    Console.WriteLine($"Access token: {accessToken}\n");
    ```

1. Erstellen Sie die APP, und führen Sie Sie aus. Die Anwendung zeigt eine URL und einen Gerätecode an.

    ```Shell
    PS C:\Source\GraphTutorial> dotnet run
    .NET Core Graph Tutorial

    To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code F7CG945YZ to authenticate.
    ```

    > [!TIP]
    > Wenn Fehler auftreten, vergleichen Sie Ihr **Program.cs** mit dem [Beispiel auf GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demos/01-create-app/GraphTutorial/Program.cs).

1. Öffnen Sie einen Browser, und navigieren Sie zu der angezeigten URL. Geben Sie den bereitgestellten Code ein und melden Sie sich an. Kehren Sie nach Abschluss zurück zur Anwendung, und wählen Sie die **1 aus. Option Zugriffstoken anzeigen** , um das Zugriffstoken anzuzeigen.
