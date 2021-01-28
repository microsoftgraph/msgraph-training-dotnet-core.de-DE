<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="42186-101">In dieser Übung integrieren Sie Microsoft Graph in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="42186-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="42186-102">Für diese Anwendung verwenden Sie die [Microsoft Graph .NET-Clientbibliothek,](https://github.com/microsoftgraph/msgraph-sdk-dotnet) um Aufrufe an Microsoft Graph zu senden.</span><span class="sxs-lookup"><span data-stu-id="42186-102">For this application, you will use the [Microsoft Graph .NET Client Library](https://github.com/microsoftgraph/msgraph-sdk-dotnet) to make calls to Microsoft Graph.</span></span>

## <a name="get-user-details"></a><span data-ttu-id="42186-103">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="42186-103">Get user details</span></span>

1. <span data-ttu-id="42186-104">Erstellen Sie ein neues Verzeichnis im **GraphTutorial-Verzeichnis** mit dem Namen **Graph**.</span><span class="sxs-lookup"><span data-stu-id="42186-104">Create a new directory in the **GraphTutorial** directory named **Graph**.</span></span>
1. <span data-ttu-id="42186-105">Erstellen Sie eine neue Datei im **Graph-Verzeichnis** **namens GraphHelper.cs,** und fügen Sie der Datei den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="42186-105">Create a new file in the **Graph** directory named **GraphHelper.cs** and add the following code to that file.</span></span>

    ```csharp
    using Microsoft.Graph;
    using System;
    using System.Collections.Generic;
    using System.Threading.Tasks;
    using TimeZoneConverter;

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
                    return await graphClient.Me
                        .Request()
                        .Select(u => new{
                            u.DisplayName,
                            u.MailboxSettings
                        })
                        .GetAsync();
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

1. <span data-ttu-id="42186-106">Fügen Sie den folgenden Code `Main` in **"./Program.cs** direkt nach dem Aufruf hinzu, um den Benutzer zu erhalten und den `GetAccessToken` Anzeigenamen des Benutzers ausausgabe.</span><span class="sxs-lookup"><span data-stu-id="42186-106">Add the following code in `Main` in **./Program.cs** just after the `GetAccessToken` call to get the user and output the user's display name.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

<span data-ttu-id="42186-107">Wenn Sie die App jetzt ausführen, werden Sie nach der Anmeldung in der App mit ihrem Namen willkommen gegrüst.</span><span class="sxs-lookup"><span data-stu-id="42186-107">If you run the app now, after you log in the app welcomes you by name.</span></span>

## <a name="get-a-calendar-view"></a><span data-ttu-id="42186-108">Kalenderansicht erhalten</span><span class="sxs-lookup"><span data-stu-id="42186-108">Get a calendar view</span></span>

1. <span data-ttu-id="42186-109">Fügen Sie der Klasse die folgende Funktion `GraphHelper` hinzu, um Ereignisse aus dem Kalender des Benutzers zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="42186-109">Add the following function to the `GraphHelper` class to get events from the user's calendar.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

<span data-ttu-id="42186-110">Überlegen Sie sich, was dieser Code macht.</span><span class="sxs-lookup"><span data-stu-id="42186-110">Consider what this code is doing.</span></span>

- <span data-ttu-id="42186-111">Die URL, die aufgerufen wird, lautet `/me/calendarview`.</span><span class="sxs-lookup"><span data-stu-id="42186-111">The URL that will be called is `/me/calendarview`.</span></span>
- <span data-ttu-id="42186-112">Die `startDateTime` Parameter definieren den Anfang und das Ende der `endDateTime` Kalenderansicht.</span><span class="sxs-lookup"><span data-stu-id="42186-112">The `startDateTime` and `endDateTime` parameters define the start and end of the calendar view.</span></span>
- <span data-ttu-id="42186-113">Der Header bewirkt, dass das Ereignis und die Ereignisse in der Zeitzone des `Prefer: outlook.timezone` `start` Benutzers zurückgegeben `end` werden.</span><span class="sxs-lookup"><span data-stu-id="42186-113">The `Prefer: outlook.timezone` header causes the `start` and `end` of the events to be returned in the user's time zone.</span></span>
- <span data-ttu-id="42186-114">Die `Top` Funktion fordert mindestens 50 Ereignisse an.</span><span class="sxs-lookup"><span data-stu-id="42186-114">The `Top` function requests at most 50 events.</span></span>
- <span data-ttu-id="42186-115">Die Funktion beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `Select` tatsächlich von der App verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="42186-115">The `Select` function limits the fields returned for each event to just those the app will actually use.</span></span>
- <span data-ttu-id="42186-116">Die `OrderBy` Funktion sortiert die Ergebnisse nach Startdatum und -uhrzeit.</span><span class="sxs-lookup"><span data-stu-id="42186-116">The `OrderBy` function sorts the results by the start date and time.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="42186-117">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="42186-117">Display the results</span></span>

1. <span data-ttu-id="42186-118">Fügen Sie der Klasse die folgende Funktion hinzu, um die `Program` [dateTimeTimeZone-Eigenschaften](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) aus Microsoft Graph in ein benutzerfreundliches Format zu formatieren.</span><span class="sxs-lookup"><span data-stu-id="42186-118">Add the following function to the `Program` class to format the [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) properties from Microsoft Graph into a user-friendly format.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. <span data-ttu-id="42186-119">Fügen Sie der Klasse die folgende Funktion hinzu, um die Ereignisse des Benutzers zu erhalten und `Program` sie an die Konsole ausausgabe.</span><span class="sxs-lookup"><span data-stu-id="42186-119">Add the following function to the `Program` class to get the user's events and output them to the console.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. <span data-ttu-id="42186-120">Fügen Sie folgendes direkt nach dem `// List the calendar` Kommentar in der Funktion `Main` hinzu.</span><span class="sxs-lookup"><span data-stu-id="42186-120">Add the following just after the `// List the calendar` comment in the `Main` function.</span></span>

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. <span data-ttu-id="42186-121">Speichern Sie alle Änderungen, und führen Sie die App aus.</span><span class="sxs-lookup"><span data-stu-id="42186-121">Save all of your changes and run the app.</span></span> <span data-ttu-id="42186-122">Wählen Sie **die Kalenderoption "Diese Woche anzeigen"** aus, um eine Liste der Ereignisse des Benutzers anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="42186-122">Choose the **View this week's calendar** option to see a list of the user's events.</span></span>

    ```Shell
    Welcome Lynne Robbins!

    Please choose one of the following options:
    0. Exit
    1. Display access token
    2. View this week's calendar
    3. Add an event
    2
    Events:
    Subject: Meeting
      Organizer: Lynne Robbins
      Start: 9/28/2020 10:00 AM
      End: 9/28/2020 11:30 AM
    Subject: Weekly meeting
      Organizer: Lynne Robbins
      Start: 9/28/2020 2:00 PM
      End: 9/28/2020 3:00 PM
    Subject: Carpool
      Organizer: Lynne Robbins
      Start: 9/28/2020 4:00 PM
      End: 9/28/2020 5:30 PM
    Subject: Tailspin Toys Proposal Review + Lunch
      Organizer: Lidia Holloway
      Start: 9/29/2020 12:00 PM
      End: 9/29/2020 1:00 PM
    Subject: Weekly meeting
      Organizer: Lynne Robbins
      Start: 9/29/2020 2:00 PM
      End: 9/29/2020 3:00 PM
    Subject: Project Tailspin
    ```
