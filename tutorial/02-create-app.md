<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="867ff-101">Erstellen Sie zunächst ein neues SWIFT-Projekt.</span><span class="sxs-lookup"><span data-stu-id="867ff-101">Begin by creating a new Swift project.</span></span>

1. <span data-ttu-id="867ff-102">Öffnen Sie Xcode.</span><span class="sxs-lookup"><span data-stu-id="867ff-102">Open Xcode.</span></span> <span data-ttu-id="867ff-103">Wählen Sie im Menü **Datei** die Option **neu**und dann **Projekt**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-103">On the **File** menu, select **New**, then **Project**.</span></span>
1. <span data-ttu-id="867ff-104">Wählen Sie die Vorlage **einzelne Ansicht-App** aus, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-104">Choose the **Single View App** template and select **Next**.</span></span>

    ![Screenshot des Dialogfelds "Xcode-Vorlagenauswahl"](./images/xcode-select-template.png)

1. <span data-ttu-id="867ff-106">Legen Sie den **Produktnamen** auf `GraphTutorial` und die **Sprache** in **SWIFT**fest.</span><span class="sxs-lookup"><span data-stu-id="867ff-106">Set the **Product Name** to `GraphTutorial` and the **Language** to **Swift**.</span></span>
1. <span data-ttu-id="867ff-107">Füllen Sie die restlichen Felder aus, und wählen Sie **weiter**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-107">Fill in the remaining fields and select **Next**.</span></span>
1. <span data-ttu-id="867ff-108">Wählen Sie einen Speicherort für das Projekt aus, und wählen Sie **Erstellen**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-108">Choose a location for the project and select **Create**.</span></span>

## <a name="install-dependencies"></a><span data-ttu-id="867ff-109">Installieren von Abhängigkeiten</span><span class="sxs-lookup"><span data-stu-id="867ff-109">Install dependencies</span></span>

<span data-ttu-id="867ff-110">Installieren Sie vor dem Verschieben einige zusätzliche Abhängigkeiten, die Sie später verwenden werden.</span><span class="sxs-lookup"><span data-stu-id="867ff-110">Before moving on, install some additional dependencies that you will use later.</span></span>

- <span data-ttu-id="867ff-111">[Microsoft Authentication Library (MSAL) für IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) zur Authentifizierung mit Azure AD.</span><span class="sxs-lookup"><span data-stu-id="867ff-111">[Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) for authenticating to with Azure AD.</span></span>
- <span data-ttu-id="867ff-112">[MSAL-Authentifizierungsanbieter für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-auth) zum Verbinden von MSAL mit dem Microsoft Graph-SDK.</span><span class="sxs-lookup"><span data-stu-id="867ff-112">[MSAL Authentication Provider for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-auth) to connect MSAL with the Microsoft Graph SDK.</span></span>
- <span data-ttu-id="867ff-113">[Microsoft Graph-SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) zum tätigen von Anrufen an Microsoft Graph.</span><span class="sxs-lookup"><span data-stu-id="867ff-113">[Microsoft Graph SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc) for making calls to Microsoft Graph.</span></span>
- <span data-ttu-id="867ff-114">[Microsoft Graph-Modell SDK für Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) für stark typisierte Objekte, die Microsoft Graph-Ressourcen wie Benutzer oder Ereignisse darstellen.</span><span class="sxs-lookup"><span data-stu-id="867ff-114">[Microsoft Graph Models SDK for Objective C](https://github.com/microsoftgraph/msgraph-sdk-objc-models) for strongly-typed objects representing Microsoft Graph resources like users or events.</span></span>

1. <span data-ttu-id="867ff-115">Beenden Sie Xcode.</span><span class="sxs-lookup"><span data-stu-id="867ff-115">Quit Xcode.</span></span>
1. <span data-ttu-id="867ff-116">Öffnen Sie Terminal, und ändern Sie das Verzeichnis in den Speicherort Ihres **GraphTutorial** -Projekts.</span><span class="sxs-lookup"><span data-stu-id="867ff-116">Open Terminal and change the directory to the location of your **GraphTutorial** project.</span></span>
1. <span data-ttu-id="867ff-117">Führen Sie den folgenden Befehl aus, um eine Podfile zu erstellen.</span><span class="sxs-lookup"><span data-stu-id="867ff-117">Run the following command to create a Podfile.</span></span>

    ```Shell
    pod init
    ```

1. <span data-ttu-id="867ff-118">Öffnen Sie die Podfile, und fügen Sie die folgenden Zeilen `use_frameworks!` unmittelbar nach der Zeile hinzu.</span><span class="sxs-lookup"><span data-stu-id="867ff-118">Open the Podfile and add the following lines just after the `use_frameworks!` line.</span></span>

    ```Ruby
    pod 'MSAL', '~> 0.3.0'
    pod 'MSGraphMSALAuthProvider', '~> 0.1.1'
    pod 'MSGraphClientSDK', ' ~> 0.1.3'
    pod 'MSGraphClientModels', '~> 0.1.1'
    ```

1. <span data-ttu-id="867ff-119">Speichern Sie die Podfile, und führen Sie dann den folgenden Befehl aus, um die Abhängigkeiten zu installieren.</span><span class="sxs-lookup"><span data-stu-id="867ff-119">Save the Podfile, then run the following command to install the dependencies.</span></span>

    ```Shell
    pod install
    ```

1. <span data-ttu-id="867ff-120">Nachdem der Befehl abgeschlossen ist, öffnen Sie das neu erstellte **GraphTutorial. xcworkspace.** in Xcode.</span><span class="sxs-lookup"><span data-stu-id="867ff-120">Once the command completes, open the newly created **GraphTutorial.xcworkspace** in Xcode.</span></span>

## <a name="design-the-app"></a><span data-ttu-id="867ff-121">Entwerfen der APP</span><span class="sxs-lookup"><span data-stu-id="867ff-121">Design the app</span></span>

<span data-ttu-id="867ff-122">In diesem Abschnitt werden die Ansichten für die App erstellt: eine Anmeldeseite, ein Registerkartenleisten-Navigator, eine Willkommensseite und eine Kalender Seite.</span><span class="sxs-lookup"><span data-stu-id="867ff-122">In this section you will create the views for the app: a sign in page, a tab bar navigator, a welcome page, and a calendar page.</span></span> <span data-ttu-id="867ff-123">Außerdem erstellen Sie ein Aktivitäts Indikator-Overlay.</span><span class="sxs-lookup"><span data-stu-id="867ff-123">You'll also create an activity indicator overlay.</span></span>

### <a name="create-sign-in-page"></a><span data-ttu-id="867ff-124">Anmeldeseite erstellen</span><span class="sxs-lookup"><span data-stu-id="867ff-124">Create sign in page</span></span>

1. <span data-ttu-id="867ff-125">Erweitern Sie den Ordner **GraphTutorial** in Xcode, und wählen Sie dann **ViewController. Swift**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-125">Expand the **GraphTutorial** folder in Xcode, then select **ViewController.swift**.</span></span>
1. <span data-ttu-id="867ff-126">Ändern Sie `SignInViewController.swift`im **Datei Inspektor**den **Namen** der Datei in.</span><span class="sxs-lookup"><span data-stu-id="867ff-126">In the **File Inspector**, change the **Name** of the file to `SignInViewController.swift`.</span></span>

    ![Screenshot des Datei Inspektors](./images/rename-file.png)

1. <span data-ttu-id="867ff-128">Öffnen Sie **SignInViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="867ff-128">Open **SignInViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class SignInViewController: UIViewController {

        override func viewDidLoad() {
            super.viewDidLoad()
            // Do any additional setup after loading the view.
        }

        @IBAction func signIn() {
            self.performSegue(withIdentifier: "userSignedIn", sender: nil)
        }
    }
    ```

1. <span data-ttu-id="867ff-129">Öffnen Sie die Datei **Main. Storyboard** .</span><span class="sxs-lookup"><span data-stu-id="867ff-129">Open the **Main.storyboard** file.</span></span>
1. <span data-ttu-id="867ff-130">Erweitern Sie **View Controller Scene**, und wählen Sie dann **View Controller**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-130">Expand **View Controller Scene**, then select **View Controller**.</span></span>

    ![Screenshot von Xcode mit ausgewähltem Ansichts Controller](./images/storyboard-select-view-controller.png)

1. <span data-ttu-id="867ff-132">Wählen Sie den **Identitäts Inspektor**aus, und ändern Sie dann das Dropdownmenü **Klasse** in **SignInViewController**.</span><span class="sxs-lookup"><span data-stu-id="867ff-132">Select the **Identity Inspector**, then change the **Class** dropdown to **SignInViewController**.</span></span>

    ![Screenshot des Identitäts Inspektors](./images/change-class.png)

1. <span data-ttu-id="867ff-134">Wählen Sie die **Bibliothek**aus, und ziehen Sie dann eine **Schaltfläche** auf den **Anmelde Ansichts Controller**.</span><span class="sxs-lookup"><span data-stu-id="867ff-134">Select the **Library**, then drag a **Button** onto the **Sign In View Controller**.</span></span>

    ![Ein Screenshot der Bibliothek in Xcode](./images/add-button-to-view.png)

1. <span data-ttu-id="867ff-136">Wählen Sie mit ausgewählter Schaltfläche den **Attributes Inspector** aus, und ändern Sie den `Sign In` **Titel** der Schaltfläche in.</span><span class="sxs-lookup"><span data-stu-id="867ff-136">With the button selected, select the **Attributes Inspector** and change the **Title** of the button to `Sign In`.</span></span>

    ![Screenshot des titelfelds im Attributes inspector in Xcode](./images/set-button-title.png)

1. <span data-ttu-id="867ff-138">Wählen Sie den **Controller anmelden**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-138">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="867ff-139">Ziehen Sie unter **empfangene Aktionen**den ungefüllten Kreis neben **SignIn** auf die Schaltfläche.</span><span class="sxs-lookup"><span data-stu-id="867ff-139">Under **Received Actions**, drag the unfilled circle next to **signIn** onto the button.</span></span> <span data-ttu-id="867ff-140">Wählen Sie im Popup Menü die Option nach **oben innen berühren** aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-140">Select **Touch Up Inside** on the pop-up menu.</span></span>

    ![Ein Screenshot des Ziehens der SignIn-Aktion auf die Schaltfläche in Xcode](./images/connect-sign-in-button.png)

1. <span data-ttu-id="867ff-142">Wählen Sie im Menü **Editor** die Option **Probleme beim automatischen Layout auflösen**aus, und wählen Sie dann **unter alle Ansichten im Steuersatz-Ansichts Controller** **fehlende Abhängigkeiten hinzufügen** aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-142">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Sign In View Controller**.</span></span>

### <a name="create-tab-bar"></a><span data-ttu-id="867ff-143">Registerkartenleiste erstellen</span><span class="sxs-lookup"><span data-stu-id="867ff-143">Create tab bar</span></span>

1. <span data-ttu-id="867ff-144">Wählen Sie die **Bibliothek**aus, und ziehen Sie dann einen **Registerkartenleisten-Controller** auf das Storyboard.</span><span class="sxs-lookup"><span data-stu-id="867ff-144">Select the **Library**, then drag a **Tab Bar Controller** onto the storyboard.</span></span>
1. <span data-ttu-id="867ff-145">Wählen Sie den **Controller anmelden**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-145">Select the **Sign In View Controller**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="867ff-146">Ziehen Sie unter **ausgelöste Segues**den ungefüllten Kreis neben **manuell** auf den **Registerkartenleisten-Controller** auf dem Storyboard.</span><span class="sxs-lookup"><span data-stu-id="867ff-146">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Tab Bar Controller** on the storyboard.</span></span> <span data-ttu-id="867ff-147">Wählen Sie im Popup Menü **Modal** aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-147">Select **Present Modally** in the pop-up menu.</span></span>

    ![Ein Screenshot des Ziehens einer manuellen segue auf den neuen Registerkartenleisten-Controller in Xcode](./images/add-segue.png)

1. <span data-ttu-id="867ff-149">Wählen Sie das soeben hinzugefügte segue aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-149">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="867ff-150">Legen Sie \*\*\*\* das Feldbezeichner `userSignedIn`auf fest.</span><span class="sxs-lookup"><span data-stu-id="867ff-150">Set the **Identifier** field to `userSignedIn`.</span></span>

    ![Screenshot des Felds "Bezeichner" im Attributes inspector in Xcode](./images/set-segue-identifier.png)

1. <span data-ttu-id="867ff-152">Wählen Sie die **Szene Element 1**aus, und wählen Sie dann den **Verbindungs Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-152">Select the **Item 1 Scene**, then select the **Connections Inspector**.</span></span>
1. <span data-ttu-id="867ff-153">Ziehen Sie unter **ausgelöste Segues**den ungefüllten Kreis neben **manuell** auf den **Anmelde Ansichts Controller** auf dem Storyboard.</span><span class="sxs-lookup"><span data-stu-id="867ff-153">Under **Triggered Segues**, drag the unfilled circle next to **manual** onto the **Sign In View Controller** on the storyboard.</span></span> <span data-ttu-id="867ff-154">Wählen Sie im Popup Menü **Modal** aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-154">Select **Present Modally** in the pop-up menu.</span></span>
1. <span data-ttu-id="867ff-155">Wählen Sie das soeben hinzugefügte segue aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-155">Select the segue you just added, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="867ff-156">Legen Sie \*\*\*\* das Feldbezeichner `userSignedOut`auf fest.</span><span class="sxs-lookup"><span data-stu-id="867ff-156">Set the **Identifier** field to `userSignedOut`.</span></span>

### <a name="create-welcome-page"></a><span data-ttu-id="867ff-157">Willkommensseite erstellen</span><span class="sxs-lookup"><span data-stu-id="867ff-157">Create welcome page</span></span>

1. <span data-ttu-id="867ff-158">Wählen Sie die Datei **Assets. xcassets** aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-158">Select the **Assets.xcassets** file.</span></span>
1. <span data-ttu-id="867ff-159">Wählen Sie im Menü **Editor** die Option **Objekte hinzufügen**und dann **neuer Bildsatz**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-159">On the **Editor** menu, select **Add Assets**, then **New Image Set**.</span></span>
1. <span data-ttu-id="867ff-160">Wählen Sie das neue **Image** -Objekt aus, und verwenden Sie den **Attribut Inspektor** , um seinen **Namen** auf `DefaultUserPhoto`festzulegen.</span><span class="sxs-lookup"><span data-stu-id="867ff-160">Select the new **Image** asset and use the **Attribute Inspector** to set its **Name** to `DefaultUserPhoto`.</span></span>
1. <span data-ttu-id="867ff-161">Fügen Sie ein beliebiges Bild hinzu, das Sie als Standardbenutzer Profilfoto dienen möchten.</span><span class="sxs-lookup"><span data-stu-id="867ff-161">Add any image you like to serve as a default user profile photo.</span></span>

    ![Screenshot der Ansicht "ImageSet Asset" in Xcode](./images/add-default-user-photo.png)

1. <span data-ttu-id="867ff-163">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `WelcomeViewController`Ordner mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="867ff-163">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `WelcomeViewController`.</span></span> <span data-ttu-id="867ff-164">Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="867ff-164">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="867ff-165">Öffnen Sie **WelcomeViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="867ff-165">Open **WelcomeViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class WelcomeViewController: UIViewController {

        @IBOutlet var userProfilePhoto: UIImageView!
        @IBOutlet var userDisplayName: UILabel!
        @IBOutlet var userEmail: UILabel!

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.

            // TEMPORARY
            self.userProfilePhoto.image = UIImage(imageLiteralResourceName: "DefaultUserPhoto")
            self.userDisplayName.text = "Default User"
            self.userEmail.text = "default@contoso.com"
        }

        @IBAction func signOut() {
            self.performSegue(withIdentifier: "userSignedOut", sender: nil)
        }
    }
    ```

1. <span data-ttu-id="867ff-166">Öffnen Sie **Main. Storyboard**.</span><span class="sxs-lookup"><span data-stu-id="867ff-166">Open **Main.storyboard**.</span></span> <span data-ttu-id="867ff-167">Wählen Sie die **Szene Element 1**aus, und wählen Sie dann den **Identitäts Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-167">Select the **Item 1 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="867ff-168">Ändern Sie den Wert der **Klasse** in **WelcomeViewController**.</span><span class="sxs-lookup"><span data-stu-id="867ff-168">Change the **Class** value to **WelcomeViewController**.</span></span>
1. <span data-ttu-id="867ff-169">Fügen Sie mithilfe der **Bibliothek**die folgenden Elemente zur **Szene Element 1**hinzu.</span><span class="sxs-lookup"><span data-stu-id="867ff-169">Using the **Library**, add the following items to the **Item 1 Scene**.</span></span>

    - <span data-ttu-id="867ff-170">Eine **Bildansicht**</span><span class="sxs-lookup"><span data-stu-id="867ff-170">One **Image View**</span></span>
    - <span data-ttu-id="867ff-171">Zwei **Beschriftungen**</span><span class="sxs-lookup"><span data-stu-id="867ff-171">Two **Labels**</span></span>
    - <span data-ttu-id="867ff-172">Eine **Schaltfläche**</span><span class="sxs-lookup"><span data-stu-id="867ff-172">One **Button**</span></span>

1. <span data-ttu-id="867ff-173">Wählen Sie die Ansicht Bild aus, und wählen Sie dann den **Größen Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-173">Select the image view, then select the **Size Inspector**.</span></span>
1. <span data-ttu-id="867ff-174">Legen Sie die **Breite** und **Höhe** auf 196 fest.</span><span class="sxs-lookup"><span data-stu-id="867ff-174">Set the **Width** and **Height** to 196.</span></span>
1. <span data-ttu-id="867ff-175">Wählen Sie die zweite Bezeichnung aus, und wählen Sie dann den **Attribut Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-175">Select the second label, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="867ff-176">Ändern Sie die **Farbe** in **dunkelgrau**und ändern Sie die **Schriftart** in **System 12,0**.</span><span class="sxs-lookup"><span data-stu-id="867ff-176">Change the **Color** to **Dark Gray Color**, and change the **Font** to **System 12.0**.</span></span>
1. <span data-ttu-id="867ff-177">Wählen Sie die Schaltfläche aus, und wählen Sie dann den **Attribut Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-177">Select the button, then select the **Attributes Inspector**.</span></span>
1. <span data-ttu-id="867ff-178">Ändern Sie \*\*\*\* den Titel `Sign Out`in.</span><span class="sxs-lookup"><span data-stu-id="867ff-178">Change the **Title** to `Sign Out`.</span></span>
1. <span data-ttu-id="867ff-179">Stellen Sie über den **Connections Inspector**folgende Verbindungen her.</span><span class="sxs-lookup"><span data-stu-id="867ff-179">Using the **Connections Inspector**, make the following connections.</span></span>

    - <span data-ttu-id="867ff-180">Verknüpfen Sie die **User** -Steckdose mit dem ersten Etikett.</span><span class="sxs-lookup"><span data-stu-id="867ff-180">Link the **userDisplayName** outlet to the first label.</span></span>
    - <span data-ttu-id="867ff-181">Verknüpfen Sie die **userEmail** -Steckdose mit der zweiten Bezeichnung.</span><span class="sxs-lookup"><span data-stu-id="867ff-181">Link the **userEmail** outlet to the second label.</span></span>
    - <span data-ttu-id="867ff-182">Verknüpfen Sie die **userProfilePhoto** -Steckdose mit der Bildansicht.</span><span class="sxs-lookup"><span data-stu-id="867ff-182">Link the **userProfilePhoto** outlet to the image view.</span></span>
    - <span data-ttu-id="867ff-183">Verknüpfen Sie die Aktion " **abgemeldete** empfangen" mit dem Touch der Schaltfläche nach **innen**.</span><span class="sxs-lookup"><span data-stu-id="867ff-183">Link the **signOut** received action to the button's **Touch Up Inside**.</span></span>

1. <span data-ttu-id="867ff-184">Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-184">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="867ff-185">Ändern Sie \*\*\*\* den Titel `Me`in.</span><span class="sxs-lookup"><span data-stu-id="867ff-185">Change the **Title** to `Me`.</span></span>
1. <span data-ttu-id="867ff-186">Wählen Sie im Menü **Editor** die Option **Probleme beim automatischen Layout auflösen**aus, und wählen Sie dann **fehlende Abhängigkeiten** unterhalb **aller Ansichten im Begrüßungs Ansichts Controller**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-186">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="867ff-187">Die Begrüßungs Szene sollte wie folgt aussehen, wenn Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="867ff-187">The welcome scene should look similar to this once you're done.</span></span>

![Screenshot des Layouts für die Begrüßungs Szene](./images/welcome-scene-layout.png)

### <a name="create-calendar-page"></a><span data-ttu-id="867ff-189">Seite "Kalender erstellen"</span><span class="sxs-lookup"><span data-stu-id="867ff-189">Create calendar page</span></span>

1. <span data-ttu-id="867ff-190">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `CalendarViewController`Ordner mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="867ff-190">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `CalendarViewController`.</span></span> <span data-ttu-id="867ff-191">Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="867ff-191">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="867ff-192">Öffnen Sie **CalendarViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="867ff-192">Open **CalendarViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class CalendarViewController: UIViewController {

        @IBOutlet var calendarJSON: UITextView!

        override func viewDidLoad() {
            super.viewDidLoad()

            // Do any additional setup after loading the view.

            // TEMPORARY
            calendarJSON.text = "Calendar"
            calendarJSON.sizeToFit()
        }
    }
    ```

1. <span data-ttu-id="867ff-193">Öffnen Sie **Main. Storyboard**.</span><span class="sxs-lookup"><span data-stu-id="867ff-193">Open **Main.storyboard**.</span></span> <span data-ttu-id="867ff-194">Wählen Sie die **Szene Element 2**aus, und wählen Sie dann den **Identitäts Inspektor**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-194">Select the **Item 2 Scene**, then select the **Identity Inspector**.</span></span> <span data-ttu-id="867ff-195">Ändern Sie den Wert der **Klasse** in **CalendarViewController**.</span><span class="sxs-lookup"><span data-stu-id="867ff-195">Change the **Class** value to **CalendarViewController**.</span></span>
1. <span data-ttu-id="867ff-196">Fügen Sie mithilfe der **Bibliothek**eine **Text Ansicht** zur **Szene "Element 2**" hinzu.</span><span class="sxs-lookup"><span data-stu-id="867ff-196">Using the **Library**, add a **Text View** to the **Item 2 Scene**.</span></span>
1. <span data-ttu-id="867ff-197">Wählen Sie die soeben hinzugefügte Textansicht aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-197">Select the text view you just added.</span></span> <span data-ttu-id="867ff-198">Wählen Sie im **Editor** **Einbetten in**und dann **Bildlaufansicht**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-198">On the **Editor**, choose **Embed In**, then **Scroll View**.</span></span>
1. <span data-ttu-id="867ff-199">Verbinden Sie die **calendarJSON** -Steckdose mithilfe des **Verbindungs Inspektors**mit der Textansicht.</span><span class="sxs-lookup"><span data-stu-id="867ff-199">Using the **Connections Inspector**, connect the **calendarJSON** outlet to the text view.</span></span>
1. 1. <span data-ttu-id="867ff-200">Wählen Sie das Element der Registerkartenleiste am unteren Rand der Szene aus, und wählen Sie dann den **Attributes Inspector**aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-200">Select the tab bar item at the bottom of the scene, then select the **Attributes Inspector**.</span></span> <span data-ttu-id="867ff-201">Ändern Sie \*\*\*\* den Titel `Calendar`in.</span><span class="sxs-lookup"><span data-stu-id="867ff-201">Change the **Title** to `Calendar`.</span></span>
1. <span data-ttu-id="867ff-202">Wählen Sie im Menü **Editor** die Option **Probleme beim automatischen Layout auflösen**aus, und wählen Sie dann **fehlende Abhängigkeiten** unterhalb **aller Ansichten im Begrüßungs Ansichts Controller**hinzufügen aus.</span><span class="sxs-lookup"><span data-stu-id="867ff-202">On the **Editor** menu, select **Resolve Auto Layout Issues**, then select **Add Missing Constraints** underneath **All Views in Welcome View Controller**.</span></span>

<span data-ttu-id="867ff-203">Die Kalender Szene sollte wie folgt aussehen, wenn Sie fertig sind.</span><span class="sxs-lookup"><span data-stu-id="867ff-203">The calendar scene should look similar to this once you're done.</span></span>

![Screenshot des Kalender Szenen Layouts](./images/calendar-scene-layout.png)

### <a name="create-activity-indicator"></a><span data-ttu-id="867ff-205">Aktivitäts Indikator erstellen</span><span class="sxs-lookup"><span data-stu-id="867ff-205">Create activity indicator</span></span>

1. <span data-ttu-id="867ff-206">Erstellen Sie eine neue **Cocoa Touch-Klassen** Datei im **GraphTutorial** - `SpinnerViewController`Ordner mit dem Namen.</span><span class="sxs-lookup"><span data-stu-id="867ff-206">Create a new **Cocoa Touch Class** file in the **GraphTutorial** folder named `SpinnerViewController`.</span></span> <span data-ttu-id="867ff-207">Wählen Sie **ulviewcontroller** in der unter **Klasse von Field aus** .</span><span class="sxs-lookup"><span data-stu-id="867ff-207">Choose **UIViewController** in the **Subclass of** field.</span></span>
1. <span data-ttu-id="867ff-208">Öffnen Sie **SpinnerViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="867ff-208">Open **SpinnerViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class SpinnerViewController: UIViewController {

        var spinner = UIActivityIndicatorView(style: .whiteLarge)

        override func loadView() {
            view = UIView()
            view.backgroundColor = UIColor(white: 0, alpha: 0.7)

            spinner.translatesAutoresizingMaskIntoConstraints = false
            spinner.startAnimating()
            view.addSubview(spinner)

            spinner.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
            spinner.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
        }

        public func start(container: UIViewController) {
            container.addChild(self)
            self.view.frame = container.view.frame
            container.view.addSubview(self.view)
            self.didMove(toParent: container)
        }

        public func stop() {
            self.willMove(toParent: nil)
            self.view.removeFromSuperview()
            self.removeFromParent()
        }
    }
    ```

## <a name="test-the-app"></a><span data-ttu-id="867ff-209">Testen der App</span><span class="sxs-lookup"><span data-stu-id="867ff-209">Test the app</span></span>

<span data-ttu-id="867ff-210">Speichern Sie Ihre Änderungen, und starten Sie die app.</span><span class="sxs-lookup"><span data-stu-id="867ff-210">Save your changes and launch the app.</span></span> <span data-ttu-id="867ff-211">Sie sollten mit den Schaltflächen **Anmelden** und **Abmelden** und der Registerkartenleiste zwischen den Bildschirmen navigieren können.</span><span class="sxs-lookup"><span data-stu-id="867ff-211">You should be able to move between the screens using the **Sign In** and **Sign Out** buttons and the tab bar.</span></span>

![Screenshots der Anwendung](./images/app-screens.png)
