<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="c174e-101">In dieser Übung werden Sie das Microsoft Graph in die Anwendung integrieren.</span><span class="sxs-lookup"><span data-stu-id="c174e-101">In this exercise you will incorporate the Microsoft Graph into the application.</span></span> <span data-ttu-id="c174e-102">Für diese Anwendung verwenden Sie das [Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) , um Anrufe an Microsoft Graph zu tätigen.</span><span class="sxs-lookup"><span data-stu-id="c174e-102">For this application, you will use the [Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) to make calls to Microsoft Graph.</span></span>

## <a name="get-calendar-events-from-outlook"></a><span data-ttu-id="c174e-103">Abrufen von Kalenderereignissen aus Outlook</span><span class="sxs-lookup"><span data-stu-id="c174e-103">Get calendar events from Outlook</span></span>

<span data-ttu-id="c174e-104">In diesem Abschnitt erweitern Sie die `GraphManager` Klasse so, dass eine Funktion hinzugefügt wird, um die Ereignisse `CalendarViewController` des Benutzers abzurufen und diese neuen Funktionen zu aktualisieren.</span><span class="sxs-lookup"><span data-stu-id="c174e-104">In this section you will extend the `GraphManager` class to add a function to get the user's events and update `CalendarViewController` to use these new functions.</span></span>

1. <span data-ttu-id="c174e-105">Öffnen Sie **graphmanager. Swift** , und fügen Sie der `GraphManager` Klasse die folgende Methode hinzu.</span><span class="sxs-lookup"><span data-stu-id="c174e-105">Open **GraphManager.swift** and add the following method to the `GraphManager` class.</span></span>

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
    > <span data-ttu-id="c174e-106">Überprüfen Sie, was `getEvents` der Code in tut.</span><span class="sxs-lookup"><span data-stu-id="c174e-106">Consider what the code in `getEvents` is doing.</span></span>
    >
    > - <span data-ttu-id="c174e-107">Die URL, die aufgerufen wird `/v1.0/me/events`.</span><span class="sxs-lookup"><span data-stu-id="c174e-107">The URL that will be called is `/v1.0/me/events`.</span></span>
    > - <span data-ttu-id="c174e-108">Der `select` Abfrageparameter schränkt die für die einzelnen Ereignisse zurückgegebenen Felder auf diejenigen ein, die von der APP tatsächlich verwendet werden.</span><span class="sxs-lookup"><span data-stu-id="c174e-108">The `select` query parameter limits the fields returned for each events to just those the app will actually use.</span></span>
    > - <span data-ttu-id="c174e-109">Der `orderby` Abfrageparameter sortiert die Ergebnisse nach dem Datum und der Uhrzeit, zu der Sie erstellt wurden, wobei das letzte Element zuerst angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="c174e-109">The `orderby` query parameter sorts the results by the date and time they were created, with the most recent item being first.</span></span>

1. <span data-ttu-id="c174e-110">Öffnen Sie **CalendarViewController. Swift** , und ersetzen Sie den gesamten Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="c174e-110">Open **CalendarViewController.swift** and replace its entire contents with the following code.</span></span>

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

<span data-ttu-id="c174e-111">Sie können nun die app ausführen, sich anmelden und auf das Navigationselement **Kalender** im Menü tippen.</span><span class="sxs-lookup"><span data-stu-id="c174e-111">You can now run the app, sign in, and tap the **Calendar** navigation item in the menu.</span></span> <span data-ttu-id="c174e-112">Es sollte ein JSON-Dump der Ereignisse in der App angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="c174e-112">You should see a JSON dump of the events in the app.</span></span>

## <a name="display-the-results"></a><span data-ttu-id="c174e-113">Anzeigen der Ergebnisse</span><span class="sxs-lookup"><span data-stu-id="c174e-113">Display the results</span></span>

<span data-ttu-id="c174e-114">Nun können Sie das JSON-Dump durch etwas ersetzen, um die Ergebnisse auf eine benutzerfreundliche Weise anzuzeigen.</span><span class="sxs-lookup"><span data-stu-id="c174e-114">Now you can replace the JSON dump with something to display the results in a user-friendly manner.</span></span> <span data-ttu-id="c174e-115">In diesem Abschnitt ändern Sie die `getEvents` Funktion so, dass stark typisierte Objekte zurückgegeben werden, `CalendarViewController` und ändern, um die Ereignisse mithilfe einer Tabellenansicht zu rendern.</span><span class="sxs-lookup"><span data-stu-id="c174e-115">In this section, you will modify the `getEvents` function to return strongly-typed objects, and modify `CalendarViewController` to use a table view to render the events.</span></span>

### <a name="update-getevents"></a><span data-ttu-id="c174e-116">Aktualisieren von GetEvents</span><span class="sxs-lookup"><span data-stu-id="c174e-116">Update getEvents</span></span>

1. <span data-ttu-id="c174e-117">Öffnen Sie **graphmanager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="c174e-117">Open **GraphManager.swift**.</span></span> <span data-ttu-id="c174e-118">Ändern Sie `getEvents` die Funktionsdeklaration in Folgendes.</span><span class="sxs-lookup"><span data-stu-id="c174e-118">Change the `getEvents` function declaration to the following.</span></span>

    ```Swift
    public func getEvents(completion: @escaping([MSGraphEvent]?, Error?) -> Void)
    ```

1. <span data-ttu-id="c174e-119">Ersetzen Sie die Zeile `completion(eventsData, nil)` durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="c174e-119">Replace the `completion(eventsData, nil)` line with the following code.</span></span>

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

### <a name="update-calendarviewcontroller"></a><span data-ttu-id="c174e-120">CalendarViewController aktualisieren</span><span class="sxs-lookup"><span data-stu-id="c174e-120">Update CalendarViewController</span></span>

1. <span data-ttu-id="c174e-121">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarTableViewCell.swift`Projekt mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="c174e-121">Create a new **Cocoa Touch Class** file in the **GraphTutorial** project named `CalendarTableViewCell.swift`.</span></span> <span data-ttu-id="c174e-122">Wählen Sie **UITableViewCell** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="c174e-122">Choose **UITableViewCell** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="c174e-123">Öffnen Sie **CalendarTableViewCell. Swift** , und fügen Sie der `CalendarTableViewCell` -Klasse den folgenden Code hinzu.</span><span class="sxs-lookup"><span data-stu-id="c174e-123">Open **CalendarTableViewCell.swift** and add the following code to the `CalendarTableViewCell` class.</span></span>

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

1. <span data-ttu-id="c174e-124">Öffnen Sie **Main. Storyboard** , und suchen Sie die **Kalender Szene**.</span><span class="sxs-lookup"><span data-stu-id="c174e-124">Open **Main.storyboard** and locate the **Calendar Scene**.</span></span> <span data-ttu-id="c174e-125">Wählen Sie die **Ansicht** in der **Kalender Szene** aus, und löschen Sie Sie.</span><span class="sxs-lookup"><span data-stu-id="c174e-125">Select the **View** in the **Calendar Scene** and delete it.</span></span>

    ![Screenshot der Ansicht in der Kalender Szene](./images/view-in-calendar-scene.png)

1. <span data-ttu-id="c174e-127">Hinzufügen einer **Tabellenansicht** aus der **Bibliothek** zur **Kalender Szene**.</span><span class="sxs-lookup"><span data-stu-id="c174e-127">Add a **Table View** from the **Library** to the **Calendar Scene**.</span></span>
1. <span data-ttu-id="c174e-128">Wählen Sie die Tabellenansicht aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="c174e-128">Select the table view, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="c174e-129">Legen Sie **Prototype Cells** auf **1**fest.</span><span class="sxs-lookup"><span data-stu-id="c174e-129">Set **Prototype Cells** to **1**.</span></span>
1. <span data-ttu-id="c174e-130">Verwenden Sie die **Bibliothek** , um der Zelle Prototyp drei **Beschriftungen** hinzuzufügen.</span><span class="sxs-lookup"><span data-stu-id="c174e-130">Use the **Library** to add three **Labels** to the prototype cell.</span></span>
1. <span data-ttu-id="c174e-131">Wählen Sie die Zelle Prototyp aus, und wählen Sie dann den **Identitäts Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="c174e-131">Select the prototype cell, then select the **Identity Inspector**.</span></span> <span data-ttu-id="c174e-132">Ändern Sie die **Klasse** in **CalendarTableViewCell**.</span><span class="sxs-lookup"><span data-stu-id="c174e-132">Change **Class** to **CalendarTableViewCell**.</span></span>
1. <span data-ttu-id="c174e-133">Wählen Sie den **Attributes Inspector** aus, `EventCell`und legen Sie **Identifier** auf fest.</span><span class="sxs-lookup"><span data-stu-id="c174e-133">Select the **Attributes Inspector** and set **Identifier** to `EventCell`.</span></span>
1. <span data-ttu-id="c174e-134">Wählen Sie mit ausgewähltem **EventCell** den **Verbindungs Inspektor** aus, `durationLabel`und `organizerLabel`verbinden `subjectLabel` Sie die Beschriftungen, die Sie der Zelle im Storyboard hinzugefügt haben.</span><span class="sxs-lookup"><span data-stu-id="c174e-134">With the **EventCell** selected, select the **Connections Inspector** and connect `durationLabel`, `organizerLabel`, and `subjectLabel` to the labels you added to the cell on the storyboard.</span></span>

    ![Screenshot des Zellenlayouts für Prototypen](./images/prototype-cell-layout.png)

1. <span data-ttu-id="c174e-136">Öffnen Sie **CalendarViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="c174e-136">Open **CalendarViewController.swift** and replace its contents with the following code.</span></span>

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

1. <span data-ttu-id="c174e-137">Führen Sie die APP aus, melden Sie sich an, und tippen Sie auf die Registerkarte **Kalender** . Die Liste der Ereignisse sollte angezeigt werden.</span><span class="sxs-lookup"><span data-stu-id="c174e-137">Run the app, sign in, and tap the **Calendar** tab. You should see the list of events.</span></span>

    ![Ein Screenshot der Ereignistabelle](./images/calendar-list.png)
