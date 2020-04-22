<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie das [Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) , um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen von Outlook

In diesem Abschnitt erweitern Sie die `GraphManager` Klasse so, dass eine Funktion hinzugefügt wird, um die Ereignisse `CalendarViewController` des Benutzers abzurufen und diese neuen Funktionen zu aktualisieren.

1. Öffnen Sie **graphmanager. Swift** , und fügen Sie der `GraphManager` Klasse die folgende Methode hinzu.

    ```Swift
    public func getEvents(completion: @escaping(Data?, Error?) -> Void) {
        // GET /me/events?$select='subject,organizer,start,end'$orderby=createdDateTime DESC
        // Only return these fields in results
        let select = "$select=subject,organizer,start,end"
        // Sort results by when they were created, newest first
        let orderBy = "$orderby=createdDateTime+DESC"
        let eventsRequest = NSMutableURLRequest(url: URL(string: "\(MSGraphBaseURL)/me/events?\(select)&\(orderBy)")!)
        let eventsDataTask = MSURLSessionDataTask(request: eventsRequest, client: self.client, completion: {
            (data: Data?, response: URLResponse?, graphError: Error?) in
            guard let eventsData = data, graphError == nil else {
                completion(nil, graphError)
                return
            }

            // TEMPORARY
            completion(eventsData, nil)
        })

        // Execute the request
        eventsDataTask?.execute()
    }
    ```

    > [!NOTE]
    > Überprüfen Sie, was `getEvents` der Code in tut.
    >
    > - Die URL, die aufgerufen wird, lautet `/v1.0/me/events`.
    > - Der `select` Abfrageparameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der APP tatsächlich verwendet werden.
    > - Der `orderby` Abfrageparameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.

1. Öffnen Sie **CalendarViewController. Swift** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code.

    ```Swift
    import UIKit
    import MSGraphClientModels

    class CalendarViewController: UIViewController {

        @IBOutlet var calendarJSON: UITextView!

        private let spinner = SpinnerViewController()

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.
            self.spinner.start(container: self)

            GraphManager.instance.getEvents {
                (data: Data?, error: Error?) in
                DispatchQueue.main.async {
                    self.spinner.stop()

                    guard let eventsData = data, error == nil else {
                        self.calendarJSON.text = error.debugDescription
                        return
                    }

                    let jsonString = String(data: eventsData, encoding: .utf8)
                    self.calendarJSON.text = jsonString
                    self.calendarJSON.sizeToFit()
                }
            }
        }
    }
    ```

Sie können nun die app ausführen, sich anmelden und auf das Navigationselement **Kalender** im Menü tippen. Es sollte ein JSON-Dump der Ereignisse in der App angezeigt werden.

## <a name="display-the-results"></a>Anzeigen der Ergebnisse

Nun können Sie das JSON-Dump durch etwas ersetzen, um die Ergebnisse auf eine benutzerfreundliche Weise anzuzeigen. In diesem Abschnitt ändern Sie die `getEvents` Funktion so, dass stark typisierte Objekte zurückgegeben werden, `CalendarViewController` und ändern, um die Ereignisse mithilfe einer Tabellenansicht zu rendern.

1. Öffnen Sie **graphmanager. Swift**. Ersetzen Sie die vorhandene `getEvents`-Funktion durch Folgendes.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/GraphManager.swift" id="GetEventsSnippet" highlight="1,17-38":::

1. Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarTableViewCell.swift`Projekt mit dem Namen. Wählen Sie **UITableViewCell** in der unter **Klasse von Field aus** .

1. Öffnen Sie **CalendarTableViewCell. Swift** , und fügen Sie der `CalendarTableViewCell` -Klasse den folgenden Code hinzu.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarTableViewCell.swift" id="PropertiesSnippet":::

1. Öffnen Sie **Main. Storyboard** , und suchen Sie die **Kalender Szene**. Wählen Sie die **Ansicht** in der **Kalender Szene** aus, und löschen Sie Sie.

    ![Screenshot der Ansicht in der Kalender Szene](./images/view-in-calendar-scene.png)

1. Hinzufügen einer **Tabellenansicht** aus der **Bibliothek** zur **Kalender Szene**.
1. Wählen Sie die Tabellenansicht aus, und wählen Sie dann den **Attributes Inspector**aus. Legen Sie **Prototype Cells** auf **1**fest.
1. Verwenden Sie die **Bibliothek** , um der Zelle Prototyp drei **Beschriftungen** hinzuzufügen.
1. Wählen Sie die Zelle Prototyp aus, und wählen Sie dann den **Identitäts Inspektor**aus. Ändern Sie die **Klasse** in **CalendarTableViewCell**.
1. Wählen Sie den **Attributes Inspector** aus, `EventCell`und legen Sie **Identifier** auf fest.
1. Wählen Sie mit ausgewähltem **EventCell** den **Verbindungs Inspektor** aus, `durationLabel`und `organizerLabel`verbinden `subjectLabel` Sie die Beschriftungen, die Sie der Zelle im Storyboard hinzugefügt haben.
1. Legen Sie die Eigenschaften und Einschränkungen für die drei Beschriftungen wie folgt fest.

    - **Betreff-Bezeichnung**
        - Add Constraint: Leading Space to Content View Leading Margin, Value: 0
        - Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0
        - Hinzufügen von Einschränkungen: oberer Bereich für oberer Rand der Inhaltsansicht, Wert: 0
    - **Organizer-Bezeichnung**
        - Schriftart: System 12,0
        - Add Constraint: Leading Space to Content View Leading Margin, Value: 0
        - Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0
        - Add Constraint: Top Space to subject Label Bottom, Value: Standard
    - **Dauer Bezeichnung**
        - Schriftart: System 12,0
        - Farbe: dunkle graue Farbe
        - Add Constraint: Leading Space to Content View Leading Margin, Value: 0
        - Add Constraint: nachgestellter Raum zur Inhaltsansicht nach folgender Rand, Wert: 0
        - Add Constraint: Top Space to Organizer Label Bottom, Value: Standard
        - Add Constraint: Bottom Space to Content View Bottom Margin, Value: 8

    ![Screenshot des Zellenlayouts für Prototypen](./images/prototype-cell-layout.png)

1. Öffnen Sie **CalendarViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/CalendarViewController.swift" id="CalendarViewSnippet":::

1. Führen Sie die APP aus, melden Sie sich an, und tippen Sie auf die Registerkarte **Kalender** . Die Liste der Ereignisse sollte angezeigt werden.

    ![Ein Screenshot der Tabelle mit Ereignissen](./images/calendar-list.png)
