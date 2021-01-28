<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung integrieren Sie Microsoft Graph in die Anwendung. Für diese Anwendung verwenden Sie die [Microsoft Graph .NET-Clientbibliothek,](https://github.com/microsoftgraph/msgraph-sdk-dotnet) um Aufrufe an Microsoft Graph zu senden.

## <a name="get-user-details"></a>Benutzerdetails abrufen

1. Erstellen Sie ein neues Verzeichnis im **GraphTutorial-Verzeichnis** mit dem Namen **Graph**.
1. Erstellen Sie eine neue Datei im **Graph-Verzeichnis** **namens GraphHelper.cs,** und fügen Sie der Datei den folgenden Code hinzu.

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

1. Fügen Sie den folgenden Code `Main` in **"./Program.cs** direkt nach dem Aufruf hinzu, um den Benutzer zu erhalten und den `GetAccessToken` Anzeigenamen des Benutzers ausausgabe.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="GetUserSnippet":::

Wenn Sie die App jetzt ausführen, werden Sie nach der Anmeldung in der App mit ihrem Namen willkommen gegrüst.

## <a name="get-a-calendar-view"></a>Kalenderansicht erhalten

1. Fügen Sie der Klasse die folgende Funktion `GraphHelper` hinzu, um Ereignisse aus dem Kalender des Benutzers zu erhalten.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="GetEventsSnippet":::

Überlegen Sie sich, was dieser Code macht.

- Die URL, die aufgerufen wird, lautet `/me/calendarview`.
- Die `startDateTime` Parameter definieren den Anfang und das Ende der `endDateTime` Kalenderansicht.
- Der Header bewirkt, dass das Ereignis und die Ereignisse in der Zeitzone des `Prefer: outlook.timezone` `start` Benutzers zurückgegeben `end` werden.
- Die `Top` Funktion fordert mindestens 50 Ereignisse an.
- Die Funktion beschränkt die für jedes Ereignis zurückgegebenen Felder auf die Felder, die `Select` tatsächlich von der App verwendet werden.
- Die `OrderBy` Funktion sortiert die Ergebnisse nach Startdatum und -uhrzeit.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

1. Fügen Sie der Klasse die folgende Funktion hinzu, um die `Program` [dateTimeTimeZone-Eigenschaften](/graph/api/resources/datetimetimezone?view=graph-rest-1.0) aus Microsoft Graph in ein benutzerfreundliches Format zu formatieren.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="FormatDateSnippet":::

1. Fügen Sie der Klasse die folgende Funktion hinzu, um die Ereignisse des Benutzers zu erhalten und `Program` sie an die Konsole ausausgabe.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="ListEventsSnippet":::

1. Fügen Sie folgendes direkt nach dem `// List the calendar` Kommentar in der Funktion `Main` hinzu.

    ```csharp
    ListCalendarEvents(
        user.MailboxSettings.TimeZone,
        $"{user.MailboxSettings.DateFormat} {user.MailboxSettings.TimeFormat}"
    );
    ```

1. Speichern Sie alle Änderungen, und führen Sie die App aus. Wählen Sie **die Kalenderoption "Diese Woche anzeigen"** aus, um eine Liste der Ereignisse des Benutzers anzuzeigen.

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
