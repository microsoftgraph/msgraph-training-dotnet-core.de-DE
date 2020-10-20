<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie die [Microsoft Graph .NET-Client Bibliothek](https://github.com/microsoftgraph/msgraph-sdk-dotnet) , um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-user-details"></a>Benutzerdetails abrufen

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

1. Fügen Sie den folgenden Code in `Main` in **./Program.cs** unmittelbar nach dem `GetAccessToken` Aufruf zum Abrufen des Benutzers und ausgeben des Anzeigenamens des Benutzers hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

Wenn Sie die APP jetzt ausführen, werden Sie nach der Anmeldung in der APP nach Namen begrüßt.

## <a name="get-a-calendar-view"></a>Abrufen einer Kalenderansicht

1. Fügen Sie der Klasse die folgende Funktion hinzu `GraphHelper` , um Ereignisse aus dem Kalender des Benutzers abzurufen.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

Überlegen Sie sich, was dieser Code macht.

- Die URL, die aufgerufen wird, lautet `/me/calendarview`.
- Die `startDateTime` `endDateTime` Parameter und definieren den Anfang und das Ende der Kalenderansicht.
- Der `Prefer: outlook.timezone` Header bewirkt `start` , dass die und der `end` Ereignisse in der Zeitzone des Benutzers zurückgegeben werden.
- Die `Top` Funktion fordert die meisten 50-Ereignisse an.
- Die `Select` Funktion schränkt die für jedes Ereignis zurückgegebenen Felder auf diejenigen ein, die von der APP tatsächlich verwendet werden.
- Die `OrderBy` -Funktion sortiert die Ergebnisse nach Startdatum und-Uhrzeit.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

1. Fügen Sie die folgende Funktion zur- `Program` Klasse hinzu, um die [dateTimeTimeZone](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) -Eigenschaften aus Microsoft Graph in ein benutzerfreundliches Format zu formatieren.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. Fügen Sie die folgende Funktion zur `Program` -Klasse hinzu, um die Ereignisse des Benutzers abzurufen und Sie in der Konsole auszugeben.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. Fügen Sie das folgende direkt nach dem `// List the calendar` Kommentar in der `Main` -Funktion hinzu.

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. Speichern Sie alle Änderungen, und führen Sie die APP aus. Wählen Sie die Option **Kalender dieser Woche anzeigen** aus, um eine Liste der Ereignisse des Benutzers anzuzeigen.

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
