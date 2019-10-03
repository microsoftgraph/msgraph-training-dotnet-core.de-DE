<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft Graph .NET-Client Bibliothek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-user-details"></a>Abrufen von Benutzer Details

1. Erstellen Sie ein neues Verzeichnis im **GraphTutorial** -Verzeichnis mit dem Namen **Graph**.
1. Erstellen Sie eine neue Datei im **Graph** -Verzeichnis mit dem Namen **GraphHelper.cs** , und fügen Sie der Datei den folgenden Code hinzu.

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

Fügen Sie den folgenden Code `Main` in in **Program.cs** unmittelbar nach `GetAccessToken` dem Aufruf zum Abrufen des Benutzers und ausgeben des Anzeigenamens des Benutzers hinzu.

```csharp
// Initialize Graph client
GraphHelper.Initialize(authProvider);

// Get signed in user
var user = GraphHelper.GetMeAsync().Result;
Console.WriteLine($"Welcome {user.DisplayName}!\n");
```

Wenn Sie die APP jetzt ausführen, werden Sie nach der Anmeldung in der APP nach Namen begrüßt.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen aus Outlook

Fügen Sie der `GraphHelper` Klasse die folgende Funktion hinzu, um Ereignisse aus dem Kalender des Benutzers abzurufen.

```csharp
public static async Task<IEnumerable<Event>> GetEventsAsync()
{
    try
    {
        // GET /me/events
        var resultPage = await graphClient.Me.Events.Request()
            // Only return the fields used by the application
            .Select("subject,organizer,start,end")
            // Sort results by when they were created, newest first
            .OrderBy("createdDateTime DESC")
            .GetAsync();

        return resultPage.CurrentPage;
    }
    catch (ServiceException ex)
    {
        Console.WriteLine($"Error getting events: {ex.Message}");
        return null;
    }
}
```

Überprüfen Sie, was dieser Code tut.

- Die URL, die aufgerufen wird `/me/events`.
- Die `Select` Funktion schränkt die für jedes Ereignis zurückgegebenen Felder auf diejenigen ein, die von der APP tatsächlich verwendet werden.
- Die `OrderBy` -Funktion sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

1. Fügen Sie die folgende Funktion zur `Program` -Klasse hinzu, um die [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) -Eigenschaften aus Microsoft Graph in ein benutzerfreundliches Format zu formatieren.

```csharp
static string FormatDateTimeTimeZone(Microsoft.Graph.DateTimeTimeZone value)
{
    // Get the timezone specified in the Graph value
    var timeZone = TimeZoneInfo.FindSystemTimeZoneById(value.TimeZone);
    // Parse the date/time string from Graph into a DateTime
    var dateTime = DateTime.Parse(value.DateTime);

    // Create a DateTimeOffset in the specific timezone indicated by Graph
    var dateTimeWithTZ = new DateTimeOffset(dateTime, timeZone.BaseUtcOffset)
        .ToLocalTime();

    return dateTimeWithTZ.ToString("g");
}
```

1. Fügen Sie die folgende Funktion zur `Program` -Klasse hinzu, um die Ereignisse des Benutzers abzurufen und Sie in der Konsole auszugeben.

```csharp
static void ListCalendarEvents()
{
    var events = GraphHelper.GetEventsAsync().Result;

    Console.WriteLine("Events:");

    foreach (var calendarEvent in events)
    {
        Console.WriteLine($"Subject: {calendarEvent.Subject}");
        Console.WriteLine($"  Organizer: {calendarEvent.Organizer.EmailAddress.Name}");
        Console.WriteLine($"  Start: {FormatDateTimeTimeZone(calendarEvent.Start)}");
        Console.WriteLine($"  End: {FormatDateTimeTimeZone(calendarEvent.End)}");
    }
}
```

Fügen Sie schließlich das folgende direkt nach dem `// List the calendar` Kommentar in der `Main` -Funktion hinzu.

```csharp
ListCalendarEvents();
```

Speichern Sie alle Änderungen, und führen Sie die APP aus. Wählen Sie die Option **Listen Kalenderereignisse** aus, um eine Liste der Ereignisse des Benutzers anzuzeigen.

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
