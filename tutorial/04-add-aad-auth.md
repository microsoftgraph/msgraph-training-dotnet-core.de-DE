<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten. In diesem Schritt werden Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für .net](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet) in die Anwendung integrieren.

1. Initialisieren Sie den [geheimen .net-entwicklungsspeicher](/aspnet/core/security/app-secrets) , indem Sie die CLI in dem Verzeichnis öffnen, das **GraphTutorial. csproj** enthält, und führen Sie den folgenden Befehl aus.

    ```Shell
    dotnet user-secrets init
    ```

1. Fügen Sie die Anwendungs-ID und eine Liste der erforderlichen Bereiche dem geheimen Speicher mithilfe der folgenden Befehle hinzu. Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID, die Sie im Azure-Portal erstellt haben.

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

In diesem Abschnitt erstellen Sie einen Authentifizierungsanbieter, der mit dem Graph-SDK verwendet werden kann und auch verwendet werden kann, um ein Zugriffstoken mithilfe des [Gerätecode Flusses](https://docs.microsoft.com/azure/active-directory/develop/v2-oauth2-device-code)explizit anzufordern.

### <a name="create-an-authentication-provider"></a>Erstellen eines Authentifizierungsanbieters

1. Erstellen Sie ein neues Verzeichnis im **GraphTutorial** -Verzeichnis mit dem Namen **Authentication**.
1. Erstellen Sie eine neue Datei im **Authentifizierungs** Verzeichnis mit dem Namen **DeviceCodeAuthProvider.cs** , und fügen Sie der Datei den folgenden Code hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Authentication/DeviceCodeAuthProvider.cs" id="AuthProviderSnippet":::

Überprüfen Sie die Funktionsweise dieses Codes.

- Es verwendet die MSAL `IPublicClientApplication` -Implementierung, um Token anzufordern und zu verwalten.
- Die `GetAccessToken` Funktion:
  - Meldet den Benutzer an, wenn er nicht bereits mit dem Gerätecode Fluss angemeldet ist.
  - Stellt sicher, dass das zurückgegebene Token immer `AcquireTokenSilent` neu ist, indem es die-Funktion verwendet, die das zwischengespeicherte Token zurückgibt, wenn es nicht abgelaufen ist, und das Token aktualisiert, wenn es abgelaufen ist.
- Die `IAuthenticationProvider` Schnittstelle wird implementiert, sodass das Graph-SDK die Klasse zum Authentifizieren von Graph-aufrufen verwenden kann.

## <a name="sign-in-and-display-the-access-token"></a>Anmelden und Anzeigen des Zugriffstokens

In diesem Abschnitt aktualisieren Sie die Anwendung so, dass die `GetAccessToken` Funktion aufgerufen wird, die sich am Benutzer anmeldet. Sie fügen auch Code hinzu, um das Token anzuzeigen.

1. Fügen Sie die folgende Funktion zur `Program`-Klasse hinzu:

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="LoadAppSettingsSnippet":::

1. Fügen Sie den folgenden Code der `Main` Funktion unmittelbar nach der `Console.WriteLine(".NET Core Graph Tutorial\n");` -Verbindung hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="InitializationSnippet":::

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
    > Wenn Fehler auftreten, vergleichen Sie Ihr **Program.cs** mit dem [Beispiel auf GitHub](https://github.com/microsoftgraph/msgraph-training-dotnet-core/blob/master/demo/GraphTutorial/Program.cs).

1. Öffnen Sie einen Browser, und navigieren Sie zu der angezeigten URL. Geben Sie den bereitgestellten Code ein und melden Sie sich an. Kehren Sie nach Abschluss zurück zur Anwendung, und wählen Sie die **1 aus. Option Zugriffstoken anzeigen** , um das Zugriffstoken anzuzeigen.
