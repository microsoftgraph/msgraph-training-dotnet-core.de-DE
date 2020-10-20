<!-- markdownlint-disable MD002 MD041 -->

In diesem Abschnitt können Sie die Möglichkeit zum Erstellen von Ereignissen im Kalender des Benutzers hinzufügen.

1. Öffnen Sie **/Graph/GraphHelper.cs** , und fügen Sie die folgende Funktion zur **GraphHelper** -Klasse hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="CreateEventSnippet":::

    Dieser Code initialisiert ein **Event** -Objekt und verwendet das Graph-SDK, um es dem Kalender des Benutzers hinzuzufügen.

1. Öffnen Sie **./Program.cs** , und fügen Sie der **Program** -Klasse die folgenden Funktionen hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="UserInputSnippet":::

    Diese Funktionen sind Hilfsfunktionen zum Lesen von Benutzereingaben.

1. Fügen Sie die folgende Funktion zur **Program** -Klasse hinzu.

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="CreateEventSnippet":::

    Diese Funktion fordert den Benutzer für Betreff, Teilnehmer, Start, Ende und Text an und verwendet dann diese Werte zum Aufrufen `GraphHelper.CreateEvent` .

1. Fügen Sie das folgende direkt nach dem `// Create a new event` Kommentar in der `Main` -Funktion hinzu.

    ```csharp
    CreateEvent(user.MailboxSettings.TimeZone);
    ```

1. Speichern Sie alle Änderungen, und führen Sie die APP aus. Wählen Sie die Option **Ereignis hinzufügen** aus. Antworten Sie auf die Eingabeaufforderungen, um ein neues Ereignis im Kalender des Benutzers zu erstellen.
