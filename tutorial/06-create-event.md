<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="b90a0-101">In diesem Abschnitt können Sie die Möglichkeit zum Erstellen von Ereignissen im Kalender des Benutzers hinzufügen.</span><span class="sxs-lookup"><span data-stu-id="b90a0-101">In this section you will add the ability to create events on the user's calendar.</span></span>

1. <span data-ttu-id="b90a0-102">Öffnen Sie **/Graph/GraphHelper.cs** , und fügen Sie die folgende Funktion zur **GraphHelper** -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="b90a0-102">Open **./Graph/GraphHelper.cs** and add the following function to the **GraphHelper** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Graph/GraphHelper.cs" id="CreateEventSnippet":::

    <span data-ttu-id="b90a0-103">Dieser Code initialisiert ein **Event** -Objekt und verwendet das Graph-SDK, um es dem Kalender des Benutzers hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="b90a0-103">This code initializes an **Event** object and uses the Graph SDK to add it to the user's calendar.</span></span>

1. <span data-ttu-id="b90a0-104">Öffnen Sie **./Program.cs** , und fügen Sie der **Program** -Klasse die folgenden Funktionen hinzu.</span><span class="sxs-lookup"><span data-stu-id="b90a0-104">Open **./Program.cs** and add the following functions to the **Program** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="UserInputSnippet":::

    <span data-ttu-id="b90a0-105">Diese Funktionen sind Hilfsfunktionen zum Lesen von Benutzereingaben.</span><span class="sxs-lookup"><span data-stu-id="b90a0-105">These functions are helper functions for reading user input.</span></span>

1. <span data-ttu-id="b90a0-106">Fügen Sie die folgende Funktion zur **Program** -Klasse hinzu.</span><span class="sxs-lookup"><span data-stu-id="b90a0-106">Add the following function to the **Program** class.</span></span>

    :::code language="csharp" source="../demo/GraphTutorial/Program.cs" id="CreateEventSnippet":::

    <span data-ttu-id="b90a0-107">Diese Funktion fordert den Benutzer für Betreff, Teilnehmer, Start, Ende und Text an und verwendet dann diese Werte zum Aufrufen `GraphHelper.CreateEvent` .</span><span class="sxs-lookup"><span data-stu-id="b90a0-107">This function prompts the user for subject, attendees, start, end, and body, then uses those values to call `GraphHelper.CreateEvent`.</span></span>

1. <span data-ttu-id="b90a0-108">Fügen Sie das folgende direkt nach dem `// Create a new event` Kommentar in der `Main` -Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="b90a0-108">Add the following just after the `// Create a new event` comment in the `Main` function.</span></span>

    ```csharp
    CreateEvent(user.MailboxSettings.TimeZone);
    ```

1. <span data-ttu-id="b90a0-109">Speichern Sie alle Änderungen, und führen Sie die APP aus.</span><span class="sxs-lookup"><span data-stu-id="b90a0-109">Save all of your changes and run the app.</span></span> <span data-ttu-id="b90a0-110">Wählen Sie die Option **Ereignis hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="b90a0-110">Choose the **Add an event** option.</span></span> <span data-ttu-id="b90a0-111">Antworten Sie auf die Eingabeaufforderungen, um ein neues Ereignis im Kalender des Benutzers zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="b90a0-111">Respond to the prompts to create a new event on the user's calendar.</span></span>
