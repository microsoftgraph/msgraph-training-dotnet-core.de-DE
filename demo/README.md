# <a name="how-to-run-the-completed-project"></a><span data-ttu-id="2f72d-101">Vorgehensweise Ausführen des abgeschlossenen Projekts</span><span class="sxs-lookup"><span data-stu-id="2f72d-101">How to run the completed project</span></span>

## <a name="prerequisites"></a><span data-ttu-id="2f72d-102">Voraussetzungen</span><span class="sxs-lookup"><span data-stu-id="2f72d-102">Prerequisites</span></span>

<span data-ttu-id="2f72d-103">Um das abgeschlossene Projekt in diesem Ordner auszuführen, benötigen Sie Folgendes:</span><span class="sxs-lookup"><span data-stu-id="2f72d-103">To run the completed project in this folder, you need the following:</span></span>

- <span data-ttu-id="2f72d-104">Das [.net Core SDK](https://dotnet.microsoft.com/download) , das auf Ihrem Entwicklungscomputer installiert ist.</span><span class="sxs-lookup"><span data-stu-id="2f72d-104">The [.NET Core SDK](https://dotnet.microsoft.com/download) installed on your development machine.</span></span> <span data-ttu-id="2f72d-105">(**Hinweis:** dieses Lernprogramm wurde mit .net Core SDK Version 3.1.402 geschrieben.</span><span class="sxs-lookup"><span data-stu-id="2f72d-105">(**Note:** This tutorial was written with .NET Core SDK version 3.1.402.</span></span> <span data-ttu-id="2f72d-106">Die Schritte in diesem Leitfaden funktionieren möglicherweise mit anderen Versionen, aber das wurde nicht getestet.)</span><span class="sxs-lookup"><span data-stu-id="2f72d-106">The steps in this guide may work with other versions, but that has not been tested.)</span></span>
- <span data-ttu-id="2f72d-107">Ein Microsoft-Arbeits-oder Schulkonto.</span><span class="sxs-lookup"><span data-stu-id="2f72d-107">A Microsoft work or school account.</span></span>

<span data-ttu-id="2f72d-108">Wenn Sie kein Microsoft-Konto haben, können Sie [sich für das Office 365 Entwicklerprogramm registrieren](https://developer.microsoft.com/office/dev-program) , um ein kostenloses Office 365-Abonnement zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="2f72d-108">If you don't have a Microsoft account, you can [sign up for the Office 365 Developer Program](https://developer.microsoft.com/office/dev-program) to get a free Office 365 subscription.</span></span>

## <a name="register-a-web-application-with-the-azure-active-directory-admin-center"></a><span data-ttu-id="2f72d-109">Registrieren einer Webanwendung im Azure-Active Directory Admin Center</span><span class="sxs-lookup"><span data-stu-id="2f72d-109">Register a web application with the Azure Active Directory admin center</span></span>

1. <span data-ttu-id="2f72d-110">Öffnen Sie einen Browser, und navigieren Sie zum [Azure Active Directory Admin Center](https://aad.portal.azure.com). Melden Sie sich mit einem **persönlichen Konto** (auch: Microsoft-Konto) oder einem **Geschäfts- oder Schulkonto** an.</span><span class="sxs-lookup"><span data-stu-id="2f72d-110">Open a browser and navigate to the [Azure Active Directory admin center](https://aad.portal.azure.com) and login using a **personal account** (aka: Microsoft Account) or **Work or School Account**.</span></span>

1. <span data-ttu-id="2f72d-111">Wählen Sie in der linken Navigationsleiste **Azure Active Directory** aus, und wählen Sie dann **App-Registrierungen** unter **Verwalten** aus.</span><span class="sxs-lookup"><span data-stu-id="2f72d-111">Select **Azure Active Directory** in the left-hand navigation, then select **App registrations** under **Manage**.</span></span>

    ![<span data-ttu-id="2f72d-112">Screenshot der APP-Registrierungen</span><span class="sxs-lookup"><span data-stu-id="2f72d-112">A screenshot of the App registrations</span></span> ](/tutorial/images/aad-portal-app-registrations.png)

1. <span data-ttu-id="2f72d-113">Wählen Sie **Neue Registrierung** aus.</span><span class="sxs-lookup"><span data-stu-id="2f72d-113">Select **New registration**.</span></span> <span data-ttu-id="2f72d-114">Legen Sie auf der Seite **Anwendung registrieren** die Werte wie folgt fest.</span><span class="sxs-lookup"><span data-stu-id="2f72d-114">On the **Register an application** page, set the values as follows.</span></span>

    - <span data-ttu-id="2f72d-115">Legen Sie **Name** auf `.NET Core Graph Tutorial` fest.</span><span class="sxs-lookup"><span data-stu-id="2f72d-115">Set **Name** to `.NET Core Graph Tutorial`.</span></span>
    - <span data-ttu-id="2f72d-116">Legen Sie **unterstützte Kontotypen** auf **Konten in einem beliebigen Organisations Verzeichnis**fest.</span><span class="sxs-lookup"><span data-stu-id="2f72d-116">Set **Supported account types** to **Accounts in any organizational directory**.</span></span>
    - <span data-ttu-id="2f72d-117">Lassen Sie **URI umleiten** leer.</span><span class="sxs-lookup"><span data-stu-id="2f72d-117">Leave **Redirect URI** empty.</span></span>

    ![Screenshot der Seite "Anwendung registrieren"](/tutorial/images/aad-register-an-app.png)

1. <span data-ttu-id="2f72d-119">Wählen Sie **Registrieren** aus.</span><span class="sxs-lookup"><span data-stu-id="2f72d-119">Select **Register**.</span></span> <span data-ttu-id="2f72d-120">Kopieren Sie auf der Seite " **.net Core Graph Tutorial** " den Wert der **Anwendungs-ID (Client)** , und speichern Sie Sie, um Sie im nächsten Schritt zu benötigen.</span><span class="sxs-lookup"><span data-stu-id="2f72d-120">On the **.NET Core Graph Tutorial** page, copy the value of the **Application (client) ID** and save it, you will need it in the next step.</span></span>

    ![Screenshot der Anwendungs-ID der neuen App-Registrierung](/tutorial/images/aad-application-id.png)

1. <span data-ttu-id="2f72d-122">Klicken Sie auf den Link **Umleitungs-URI hinzufügen** .</span><span class="sxs-lookup"><span data-stu-id="2f72d-122">Select the **Add a Redirect URI** link.</span></span> <span data-ttu-id="2f72d-123">Suchen Sie auf der Seite " **Umleitungs-URIs** " den Abschnitt **vorgeschlagene Umleitungs-URIs für öffentliche Clients (Mobile, Desktop)** .</span><span class="sxs-lookup"><span data-stu-id="2f72d-123">On the **Redirect URIs** page, locate the **Suggested Redirect URIs for public clients (mobile, desktop)** section.</span></span> <span data-ttu-id="2f72d-124">Wählen Sie den `https://login.microsoftonline.com/common/oauth2/nativeclient` URI aus.</span><span class="sxs-lookup"><span data-stu-id="2f72d-124">Select the `https://login.microsoftonline.com/common/oauth2/nativeclient` URI.</span></span>

    ![Screenshot der Seite "Umleitungs-URIs"](/tutorial/images/aad-redirect-uris.png)

1. <span data-ttu-id="2f72d-126">Suchen Sie den **standardmäßigen Clienttyp** Abschnitt, und ändern Sie die Option **Anwendung als öffentlichen Client** Umschalten auf **Ja**, und wählen Sie dann **Speichern**aus.</span><span class="sxs-lookup"><span data-stu-id="2f72d-126">Locate the **Default client type** section and change the **Treat application as a public client** toggle to **Yes**, then choose **Save**.</span></span>

    ![Ein Screenshot des Typs "Standard Clienttyp"](/tutorial/images/aad-default-client-type.png)

## <a name="configure-the-sample"></a><span data-ttu-id="2f72d-128">Konfigurieren des Beispiels</span><span class="sxs-lookup"><span data-stu-id="2f72d-128">Configure the sample</span></span>

1. <span data-ttu-id="2f72d-129">Initialisieren Sie den [geheimen .net-entwicklungsspeicher](https://docs.microsoft.com/aspnet/core/security/app-secrets) , indem Sie die CLI in dem Verzeichnis öffnen, das **GraphTutorial. csproj** enthält, und führen Sie den folgenden Befehl aus.</span><span class="sxs-lookup"><span data-stu-id="2f72d-129">Initialize the [.NET development secret store](https://docs.microsoft.com/aspnet/core/security/app-secrets) by opening your CLI in the directory that contains **GraphTutorial.csproj** and running the following command.</span></span>

    ```Shell
    dotnet user-secrets init
    ```

1. <span data-ttu-id="2f72d-130">Fügen Sie die Anwendungs-ID und eine Liste der erforderlichen Bereiche dem geheimen Speicher mithilfe der folgenden Befehle hinzu.</span><span class="sxs-lookup"><span data-stu-id="2f72d-130">Add your application ID and a list of required scopes to the secret store using the following commands.</span></span> <span data-ttu-id="2f72d-131">Ersetzen `YOUR_APP_ID_HERE` Sie durch die Anwendungs-ID, die Sie im Azure-Portal erstellt haben.</span><span class="sxs-lookup"><span data-stu-id="2f72d-131">Replace `YOUR_APP_ID_HERE` with the application ID you created in the Azure portal.</span></span>

    ```Shell
    dotnet user-secrets set appId "YOUR_APP_ID_HERE"
    dotnet user-secrets set scopes "User.Read;Calendars.Read"
    ```

## <a name="build-and-run-the-sample"></a><span data-ttu-id="2f72d-132">Erstellen und Ausführen des Beispiels</span><span class="sxs-lookup"><span data-stu-id="2f72d-132">Build and run the sample</span></span>

<span data-ttu-id="2f72d-133">Navigieren Sie in der Befehlszeilenschnittstelle (CLI) zum Projektverzeichnis, und führen Sie die folgenden Befehle aus.</span><span class="sxs-lookup"><span data-stu-id="2f72d-133">In your command-line interface (CLI), navigate to the project directory and run the following commands.</span></span>

```Shell
dotnet restore
dotnet build
dotnet run
```
