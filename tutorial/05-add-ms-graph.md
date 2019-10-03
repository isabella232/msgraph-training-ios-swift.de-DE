<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren. Für diese Anwendung verwenden Sie das [Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) , um Anrufe an Microsoft Graph zu tätigen.

## <a name="get-calendar-events-from-outlook"></a>Abrufen von Kalenderereignissen aus Outlook

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
    > - Die URL, die aufgerufen wird `/v1.0/me/events`.
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

### <a name="update-getevents"></a>Aktualisieren von GetEvents

1. Öffnen Sie **graphmanager. Swift**. Ändern Sie `getEvents` die Funktionsdeklaration in Folgendes.

    ```Swift
    public func getEvents(completion: @escaping([MSGraphEvent]?, Error?) -> Void)
    ```

1. Ersetzen Sie die Zeile `completion(eventsData, nil)` durch den folgenden Code.

    ```Swift
    do {
        // Deserialize response as events collection
        let eventsCollection = try MSCollection(data: eventsData)
        var eventArray: [MSGraphEvent] = []

        eventsCollection.value.forEach({
            (rawEvent: Any) in
            // Convert JSON to a dictionary
            guard let eventDict = rawEvent as? [String: Any] else {
                return
            }

            // Deserialize event from the dictionary
            let event = MSGraphEvent(dictionary: eventDict)!
            eventArray.append(event)
        })

        // Return the array
        completion(eventArray, nil)
    } catch {
        completion(nil, error)
    }
    ```

### <a name="update-calendarviewcontroller"></a>CalendarViewController aktualisieren

1. Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarTableViewCell.swift`Projekt mit dem Namen. Wählen Sie **UITableViewCell** in der unter **Klasse von Field aus** .
1. Öffnen Sie **CalendarTableViewCell. Swift** , und fügen Sie der `CalendarTableViewCell` -Klasse den folgenden Code hinzu.

    ```Swift
    @IBOutlet var subjectLabel: UILabel!
    @IBOutlet var organizerLabel: UILabel!
    @IBOutlet var durationLabel: UILabel!

    var subject: String? {
        didSet {
            subjectLabel.text = subject
        }
    }

    var organizer: String? {
        didSet {
            organizerLabel.text = organizer
        }
    }

    var duration: String? {
        didSet {
            durationLabel.text = duration
        }
    }
    ```

1. Öffnen Sie **Main. Storyboard** , und suchen Sie die **Kalender Szene**. Wählen Sie die **Ansicht** in der **Kalender Szene** aus, und löschen Sie Sie.

    ![Screenshot der Ansicht in der Kalender Szene](./images/view-in-calendar-scene.png)

1. Hinzufügen einer **Tabellenansicht** aus der **Bibliothek** zur **Kalender Szene**.
1. Wählen Sie die Tabellenansicht aus, und wählen Sie dann den **Attributes Inspector**aus. Legen Sie **Prototype Cells** auf **1**fest.
1. Verwenden Sie die **Bibliothek** , um der Zelle Prototyp drei **Beschriftungen** hinzuzufügen.
1. Wählen Sie die Zelle Prototyp aus, und wählen Sie dann den **Identitäts Inspektor**aus. Ändern Sie die **Klasse** in **CalendarTableViewCell**.
1. Wählen Sie den **Attributes Inspector** aus, `EventCell`und legen Sie **Identifier** auf fest.
1. Wählen Sie mit ausgewähltem **EventCell** den **Verbindungs Inspektor** aus, `durationLabel`und `organizerLabel`verbinden `subjectLabel` Sie die Beschriftungen, die Sie der Zelle im Storyboard hinzugefügt haben.

    ![Screenshot des Zellenlayouts für Prototypen](./images/prototype-cell-layout.png)

1. Öffnen Sie **CalendarViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.

    ```Swift
    import UIKit
    import MSGraphClientModels

    class CalendarViewController: UITableViewController {

        private let tableCellIdentifier = "EventCell"
        private let spinner = SpinnerViewController()
        private var events: [MSGraphEvent]?

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.
            self.spinner.start(container: self)

            GraphManager.instance.getEvents {
                (eventArray: [MSGraphEvent]?, error: Error?) in
                DispatchQueue.main.async {
                    self.spinner.stop()

                    guard let events = eventArray, error == nil else {
                        // Show the error
                        let alert = UIAlertController(title: "Error getting events",
                                                      message: error.debugDescription,
                                                      preferredStyle: .alert)

                        alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
                        self.present(alert, animated: true)
                        return
                    }

                    self.events = events
                    self.tableView.reloadData()
                }
            }
        }

        // Number of sections, always 1
        override func numberOfSections(in tableView: UITableView) -> Int {
            return 1
        }

        // Return the number of events in the table
        override func tableView(_ tableView: UITableView, numberOfRowsInSection section: Int) -> Int {
            return events?.count ?? 0
        }

        override func tableView(_ tableView: UITableView, cellForRowAt indexPath: IndexPath) -> UITableViewCell {
            let cell = tableView.dequeueReusableCell(withIdentifier: tableCellIdentifier, for: indexPath) as! CalendarTableViewCell

            // Get the event that corresponds to the row
            let event = events?[indexPath.row]

            // Configure the cell
            cell.subject = event?.subject
            cell.organizer = event?.organizer?.emailAddress?.name

            // Build a duration string
            let duration = "\(self.formatGraphDateTime(dateTime: event?.start)) to \(self.formatGraphDateTime(dateTime: event?.end))"
            cell.duration = duration

            return cell
        }

        private func formatGraphDateTime(dateTime: MSGraphDateTimeTimeZone?) -> String {
            guard let graphDateTime = dateTime else {
                return ""
            }

            // Create a formatter to parse Graph's date format
            let isoFormatter = DateFormatter()
            isoFormatter.dateFormat = "yyyy-MM-dd'T'HH:mm:ss.SSSSSSS"

            // Specify the timezone
            isoFormatter.timeZone = TimeZone(identifier: graphDateTime.timeZone!)

            let date = isoFormatter.date(from: graphDateTime.dateTime)

            // Output like 5/5/2019, 2:00 PM
            let dateFormatter = DateFormatter()
            dateFormatter.dateStyle = .short
            dateFormatter.timeStyle = .short

            return dateFormatter.string(from: date!)
        }
    }
    ```

1. Führen Sie die APP aus, melden Sie sich an, und tippen Sie auf die Registerkarte **Kalender** . Die Liste der Ereignisse sollte angezeigt werden.

    ![Ein Screenshot der Ereignistabelle](./images/calendar-list.png)
