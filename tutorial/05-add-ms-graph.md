<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="2c6bb-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="2c6bb-102">Für diese Anwendung verwenden Sie die [Microsoft Graph .NET-Client Bibliothek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-102">For this application, you will use the [Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-user-details"></a><span data-ttu-id="2c6bb-103">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="2c6bb-103">Get user details</span></span>

1. <span data-ttu-id="2c6bb-104">Erstellen Sie ein neues Verzeichnis im **GraphTutorial** -Verzeichnis mit dem Namen **Graph**.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-104">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>
1. <span data-ttu-id="2c6bb-105">Erstellen Sie eine neue Datei im **Graph** -Verzeichnis mit dem Namen **GraphHelper.cs** , und fügen Sie der Datei den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-105">Create a new file in the **Graph** directory named **GraphHelper.cs** and add the following code to that file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;

    namespace GraphTutorial
    {
        public class GraphHelper
        {
            private static GraphServiceClient graphClient;
            public static void Initialize(IAuthenticationProvider authProvider)
            {
                graphClient = new GraphServiceClient(authProvider);
            }

            public static async Task<User> GetMeAsync()
            {
                try
                {
                    // GET /me
                    return await graphClient.Me.Request().GetAsync();
                }
                catch (ServiceException ex)
                {
                    Console.WriteLine($"Error getting signed-in user: {ex.Message}");
                    return null;
                }
            }
        }
    }
    ```

1. <span data-ttu-id="2c6bb-106">Fügen Sie den folgenden Code `Main` in in **Program.cs** unmittelbar nach `GetAccessToken` dem Aufruf zum Abrufen des Benutzers und ausgeben des Anzeigenamens des Benutzers hinzu.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-106">Add the following code in `Main` in **Program.cs** just after the `GetAccessToken` call to get the user and output the user's display name.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

<span data-ttu-id="2c6bb-107">Wenn Sie die APP jetzt ausführen, werden Sie nach der Anmeldung in der APP nach Namen begrüßt.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-107">If you run the app now, after you log in the app welcomes you by name.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="2c6bb-108">Abrufen von Kalenderereignissen von Outlook</span><span class="sxs-lookup"><span data-stu-id="2c6bb-108">Get calendar events from Outlook</span></span>

1. <span data-ttu-id="2c6bb-109">Fügen Sie der `GraphHelper` Klasse die folgende Funktion hinzu, um Ereignisse aus dem Kalender des Benutzers abzurufen.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-109">Add the following function to the `GraphHelper` class to get events from the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

<span data-ttu-id="2c6bb-110">Überlegen Sie sich, was dieser Code macht.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-110">Consider what this code is doing.</span></span>

- <span data-ttu-id="2c6bb-111">Die URL, die aufgerufen wird, lautet `/me/events`.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-111">The URL that will be called is `/me/events`.</span></span>
- <span data-ttu-id="2c6bb-112">Die `Select` Funktion schränkt die für jedes Ereignis zurückgegebenen Felder auf diejenigen ein, die von der APP tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-112">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
- <span data-ttu-id="2c6bb-113">Die `OrderBy`-Funktion sortiert die Ergebnisse nach Datum und Uhrzeit ihrer Erstellung, wobei das aktuellste Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-113">The `OrderBy` function sorts the results by the date and time they were created, with the most recent item being first.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="2c6bb-114">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="2c6bb-114">Display the results</span></span>

1. <span data-ttu-id="2c6bb-115">Fügen Sie die folgende Funktion zur `Program` -Klasse hinzu, um die [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) -Eigenschaften aus Microsoft Graph in ein benutzerfreundliches Format zu formatieren.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-115">Add the following function to the `Program` class to format the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) properties from Microsoft Graph into a user-friendly format.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. <span data-ttu-id="2c6bb-116">Fügen Sie die folgende Funktion zur `Program` -Klasse hinzu, um die Ereignisse des Benutzers abzurufen und Sie in der Konsole auszugeben.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-116">Add the following function to the `Program` class to get the user's events and output them to the console.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. <span data-ttu-id="2c6bb-117">Fügen Sie das folgende direkt nach `// List the calendar` dem Kommentar in `Main` der-Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-117">Add the following just after the `// List the calendar` comment in the `Main` function.</span></span>

    ```csharp
    ListCalendarEvents();
    ```

1. <span data-ttu-id="2c6bb-118">Speichern Sie alle Änderungen, und führen Sie die APP aus.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-118">Save all of your changes and run the app.</span></span> <span data-ttu-id="2c6bb-119">Wählen Sie die Option **Listen Kalenderereignisse** aus, um eine Liste der Ereignisse des Benutzers anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="2c6bb-119">Choose the **List calendar events** option to see a list of the user's events.</span></span>

    ```Shell
    Welcome Adele Vance

    Please choose one of the following options:
    0. Exit
    1. Display access token
    2. List calendar events
    2
    Events:
    Subject: Team meeting
      Organizer: Adele Vance
      Start: 5/22/19, 3:00 PM
      End: 5/22/19, 4:00 PM
    Subject: Team Lunch
      Organizer: Adele Vance
      Start: 5/24/19, 6:30 PM
      End: 5/24/19, 8:00 PM
    Subject: Flight to Redmond
      Organizer: Adele Vance
      Start: 5/26/19, 4:30 PM
      End: 5/26/19, 7:00 PM
    Subject: Let's meet to discuss strategy
      Organizer: Patti Fernandez
      Start: 5/27/19, 10:00 PM
      End: 5/27/19, 10:30 PM
    Subject: All-hands meeting
      Organizer: Adele Vance
      Start: 5/28/19, 3:30 PM
      End: 5/28/19, 5:00 PM
    ```
