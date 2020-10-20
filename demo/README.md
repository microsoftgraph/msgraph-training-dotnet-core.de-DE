# <a name="how-to-run-the-completed-project"></a>Vorgehensweise Ausführen des abgeschlossenen Projekts

## <a name="prerequisites"></a>Voraussetzungen

Um das abgeschlossene Projekt in diesem Ordner auszuführen, benötigen Sie Folgendes:

- Das [.net Core SDK](https://dotnet.microsoft.com/download) , das auf Ihrem Entwicklungscomputer installiert ist. (**Hinweis:** dieses Lernprogramm wurde mit .net Core SDK Version 3.1.402 geschrieben. Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, aber das wurde nicht getestet.)
- Ein Microsoft-Arbeits-oder Schulkonto.

Wenn Sie kein Microsoft-Konto haben, können Sie [sich für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a>Registrieren einer Webanwendung im Azure-Active Directory Admin Center

1. Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.

1. Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen** unter **Verwalten** aus.

    ![Screenshot der APP-Registrierungen ](/tutorial/images/aad-portal-app-registrations.png)

1. Wählen Sie **Neue Registrierung** aus. Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.

    - Legen Sie **Name** auf `.NET Core Graph Tutorial` fest.
    - Legen Sie **unterstützte Kontotypen** auf **Konten in einem beliebigen Organisations Verzeichnis**fest.
    - Lassen Sie **URI umleiten** leer.

    ![Screenshot der Seite "Anwendung registrieren"](/tutorial/images/aad-register-an-app.png)

1. Wählen Sie **Registrieren** aus. Kopieren Sie auf der Seite " **.net Core Graph Tutorial** " den Wert der **Anwendungs-ID (Client)** , und speichern Sie Sie, um Sie im nächsten Schritt zu benötigen.

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](/tutorial/images/aad-application-id.png)

1. Klicken Sie auf den Link **Umleitungs-URI hinzufügen** . Suchen Sie auf der Seite " **Umleitungs-URIs** " den Abschnitt **vorgeschlagene Umleitungs-URIs für öffentliche Clients (Mobile, Desktop)** . Wählen Sie den `https://login.microsoftonline.com/common/oauth2/nativeclient` URI aus.

    ![Screenshot der Seite "Umleitungs-URIs"](/tutorial/images/aad-redirect-uris.png)

1. Suchen Sie den **standardmäßigen Clienttyp** Abschnitt, und ändern Sie die Option **Anwendung als öffentlichen Client** Umschalten auf **Ja**, und wählen Sie dann **Speichern**aus.

    ![Ein Screenshot des Typs "Standard Clienttyp"](/tutorial/images/aad-default-client-type.png)

## <a name="configure-the-sample"></a>Konfigurieren des Beispiels

1. Initialisieren Sie den [geheimen .net-entwicklungsspeicher](https://docs.microsoft.com/aspnet/core/security/app-secrets) , indem Sie die CLI in dem Verzeichnis öffnen, das **GraphTutorial. csproj** enthält, und führen Sie den folgenden Befehl aus.

    ```Shell
    dotnet user-secrets init
    ```

1. Fügen Sie die Anwendungs-ID und eine Liste der erforderlichen Bereiche dem geheimen Speicher mithilfe der folgenden Befehle hinzu. Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID, die Sie im Azure-Portal erstellt haben.

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="build-and-run-the-sample"></a>Erstellen und Ausführen des Beispiels

Navigieren Sie in der Befehlszeilenschnittstelle (CLI) zum Projektverzeichnis, und führen Sie die folgenden Befehle aus.

```Shell
dotnet restore
dotnet build
dotnet run
```
