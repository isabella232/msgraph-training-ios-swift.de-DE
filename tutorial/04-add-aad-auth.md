<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="e8b73-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="e8b73-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="e8b73-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="e8b73-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="e8b73-103">Hierzu integrieren Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="e8b73-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) into the application.</span></span>

1. <span data-ttu-id="e8b73-104">Erstellen Sie eine neue **Eigenschaftenlisten** Datei im **GraphTutorial** -Projekt mit dem Namen **AuthSettings. plist**.</span><span class="sxs-lookup"><span data-stu-id="e8b73-104">Create a new **Property List** file in the **GraphTutorial** project named **AuthSettings.plist**.</span></span>
1. <span data-ttu-id="e8b73-105">Fügen Sie die folgenden Elemente zur Datei im **Stamm** Wörterbuch hinzu.</span><span class="sxs-lookup"><span data-stu-id="e8b73-105">Add the following items to the file in the **Root** dictionary.</span></span>

    | <span data-ttu-id="e8b73-106">Key</span><span class="sxs-lookup"><span data-stu-id="e8b73-106">Key</span></span> | <span data-ttu-id="e8b73-107">Typ</span><span class="sxs-lookup"><span data-stu-id="e8b73-107">Type</span></span> | <span data-ttu-id="e8b73-108">Wert</span><span class="sxs-lookup"><span data-stu-id="e8b73-108">Value</span></span> |
    |-----|------|-------|
    | `AppId` | <span data-ttu-id="e8b73-109">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="e8b73-109">String</span></span> | <span data-ttu-id="e8b73-110">Die Anwendungs-ID aus dem Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="e8b73-110">The application ID from the Azure portal</span></span> |
    | `GraphScopes` | <span data-ttu-id="e8b73-111">Array</span><span class="sxs-lookup"><span data-stu-id="e8b73-111">Array</span></span> | <span data-ttu-id="e8b73-112">Zwei Zeichenfolgenwerte `User.Read` : und`Calendars.Read`</span><span class="sxs-lookup"><span data-stu-id="e8b73-112">Two String values: `User.Read` and `Calendars.Read`</span></span> |

    ![Ein Screenshot der Datei AuthSettings. plist in Xcode](./images/auth-settings.png)

> [!IMPORTANT]
> <span data-ttu-id="e8b73-114">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es nun ein guter Zeitpunkt, die Datei **AuthSettings. plist** aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="e8b73-114">If you're using source control such as git, now would be a good time to exclude the **AuthSettings.plist** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="e8b73-115">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="e8b73-115">Implement sign-in</span></span>

<span data-ttu-id="e8b73-116">In diesem Abschnitt Konfigurieren Sie das Projekt für MSAL, erstellen eine Authentifizierungs-Manager-Klasse und aktualisieren die APP, um sich anzumelden und abzumelden.</span><span class="sxs-lookup"><span data-stu-id="e8b73-116">In this section you will configure the project for MSAL, create an authentication manager class, and update the app to sign in and sign out.</span></span>

### <a name="configure-project-for-msal"></a><span data-ttu-id="e8b73-117">Konfigurieren des Projekts für MSAL</span><span class="sxs-lookup"><span data-stu-id="e8b73-117">Configure project for MSAL</span></span>

1. <span data-ttu-id="e8b73-118">Fügen Sie der Projektfunktion eine neue Schlüsselbund Gruppe hinzu.</span><span class="sxs-lookup"><span data-stu-id="e8b73-118">Add a new keychain group to your project's capabilities.</span></span>
    1. <span data-ttu-id="e8b73-119">Wählen Sie das **GraphTutorial** -Projekt aus, und **Signieren Sie & Funktionen**.</span><span class="sxs-lookup"><span data-stu-id="e8b73-119">Select the **GraphTutorial** project, then **Signing & Capabilities**.</span></span>
    1. <span data-ttu-id="e8b73-120">Wählen Sie **+-Funktion**aus, und doppelklicken Sie dann auf **Schlüsselbund Freigabe**.</span><span class="sxs-lookup"><span data-stu-id="e8b73-120">Select **+ Capability**, then double-click **Keychain Sharing**.</span></span>
    1. <span data-ttu-id="e8b73-121">Fügen Sie eine Schlüsselbund Gruppe mit `com.microsoft.adalcache`dem Wert hinzu.</span><span class="sxs-lookup"><span data-stu-id="e8b73-121">Add a keychain group with the value `com.microsoft.adalcache`.</span></span>

1. <span data-ttu-id="e8b73-122">Steuerelement klicken Sie auf **Info. plist** , und wählen Sie dann **Öffnen als**, dann **Quellcode**aus.</span><span class="sxs-lookup"><span data-stu-id="e8b73-122">Control click **Info.plist** and select **Open As**, then **Source Code**.</span></span>
1. <span data-ttu-id="e8b73-123">Fügen Sie das folgende innerhalb `<dict>` des-Elements hinzu.</span><span class="sxs-lookup"><span data-stu-id="e8b73-123">Add the following inside the `<dict>` element.</span></span>

    ```xml
    <key>CFBundleURLTypes</key>
    <array>
      <dict>
        <key>CFBundleURLSchemes</key>
        <array>
          <string>msauth.$(PRODUCT_BUNDLE_IDENTIFIER)</string>
        </array>
      </dict>
    </array>
    <key>LSApplicationQueriesSchemes</key>
    <array>
        <string>msauthv2</string>
        <string>msauthv3</string>
    </array>
    ```

1. <span data-ttu-id="e8b73-124">Öffnen Sie **AppDelegate. Swift** , und fügen Sie die folgende Importanweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="e8b73-124">Open **AppDelegate.swift** and add the following import statement at the top of the file.</span></span>

    ```Swift
    import MSAL
    ```

1. <span data-ttu-id="e8b73-125">Fügen Sie die folgende Funktion zur `AppDelegate`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="e8b73-125">Add the following function to the `AppDelegate` class.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/AppDelegate.swift" id="HandleMsalResponseSnippet":::

### <a name="create-authentication-manager"></a><span data-ttu-id="e8b73-126">Erstellen des Authentifizierungs-Managers</span><span class="sxs-lookup"><span data-stu-id="e8b73-126">Create authentication manager</span></span>

1. <span data-ttu-id="e8b73-127">Erstellen Sie eine neue **SWIFT-Datei** im **GraphTutorial** -Projekt mit dem Namen **AuthenticationManager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="e8b73-127">Create a new **Swift File** in the **GraphTutorial** project named **AuthenticationManager.swift**.</span></span> <span data-ttu-id="e8b73-128">Fügen Sie den folgenden Code in die Datei ein:</span><span class="sxs-lookup"><span data-stu-id="e8b73-128">Add the following code to the file.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/AuthenticationManager.swift" id="AuthManagerSnippet":::

### <a name="add-sign-in-and-sign-out"></a><span data-ttu-id="e8b73-129">Hinzufügen von Anmelde-und Abmeldungen</span><span class="sxs-lookup"><span data-stu-id="e8b73-129">Add sign-in and sign-out</span></span>

1. <span data-ttu-id="e8b73-130">Öffnen Sie **SignInViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="e8b73-130">Open **SignInViewController.swift** and replace its contents with the following code.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/SignInViewController.swift" id="SignInViewSnippet":::

1. <span data-ttu-id="e8b73-131">Öffnen Sie **WelcomeViewController. Swift** , und ersetzen `signOut` Sie die vorhandene Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="e8b73-131">Open **WelcomeViewController.swift** and replace the existing `signOut` function with the following.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.swift" id="SignOutSnippet":::

1. <span data-ttu-id="e8b73-132">Speichern Sie Ihre Änderungen, und starten Sie die Anwendung im Simulator neu.</span><span class="sxs-lookup"><span data-stu-id="e8b73-132">Save your changes and restart the application in Simulator.</span></span>

<span data-ttu-id="e8b73-133">Wenn Sie sich bei der App anmelden, sollten Sie ein Zugriffstoken sehen, das im Ausgabefenster von Xcode angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="e8b73-133">If you sign in to the app, you should see an access token displayed in the output window in Xcode.</span></span>

![Screenshot des Ausgabefensters in Xcode mit einem Zugriffstoken](./images/access-token-output.png)

## <a name="get-user-details"></a><span data-ttu-id="e8b73-135">Benutzerdetails abrufen</span><span class="sxs-lookup"><span data-stu-id="e8b73-135">Get user details</span></span>

<span data-ttu-id="e8b73-136">In diesem Abschnitt erstellen Sie eine Hilfsklasse, die alle Aufrufe von Microsoft Graph enthält, und aktualisieren Sie `WelcomeViewController` , um die neue Klasse zum Abrufen des angemeldeten Benutzers zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="e8b73-136">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `WelcomeViewController` to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="e8b73-137">Erstellen Sie eine neue **SWIFT-Datei** im **GraphTutorial** -Projekt mit dem Namen **graphmanager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="e8b73-137">Create a new **Swift File** in the **GraphTutorial** project named **GraphManager.swift**.</span></span> <span data-ttu-id="e8b73-138">Fügen Sie den folgenden Code in die Datei ein:</span><span class="sxs-lookup"><span data-stu-id="e8b73-138">Add the following code to the file.</span></span>

    ```Swift
    import Foundation
    import MSGraphClientSDK
    import MSGraphClientModels

    class GraphManager {

        // Implement singleton pattern
        static let instance = GraphManager()

        private let client: MSHTTPClient?

        private init() {
            client = MSClientFactory.createHTTPClient(with: AuthenticationManager.instance)
        }

        public func getMe(completion: @escaping(MSGraphUser?, Error?) -> Void) {
            // GET /me
            let meRequest = NSMutableURLRequest(url: URL(string: "\(MSGraphBaseURL)/me")!)
            let meDataTask = MSURLSessionDataTask(request: meRequest, client: self.client, completion: {
                (data: Data?, response: URLResponse?, graphError: Error?) in
                guard let meData = data, graphError == nil else {
                    completion(nil, graphError)
                    return
                }

                do {
                    // Deserialize response as a user
                    let user = try MSGraphUser(data: meData)
                    completion(user, nil)
                } catch {
                    completion(nil, error)
                }
            })

            // Execute the request
            meDataTask?.execute()
        }
    }
    ```

1. <span data-ttu-id="e8b73-139">Öffnen Sie **WelcomeViewController. Swift** , und fügen `import` Sie die folgende Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="e8b73-139">Open **WelcomeViewController.swift** and add the following `import` statement at the top of the file.</span></span>

    ```Swift
    import MSGraphClientModels
    ```

1. <span data-ttu-id="e8b73-140">Fügen Sie der `WelcomeViewController`-Klasse die folgende Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="e8b73-140">Add the following property to the `WelcomeViewController` class.</span></span>

    ```Swift
    private let spinner = SpinnerViewController()
    ```

1. <span data-ttu-id="e8b73-141">Ersetzen Sie den `viewDidLoad` vorhandenen durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="e8b73-141">Replace the existing `viewDidLoad` with the following code.</span></span>

    :::code language="swift" source="../demo/GraphTutorial/GraphTutorial/WelcomeViewController.swift" id="ViewDidLoadSnippet":::

<span data-ttu-id="e8b73-142">Wenn Sie Ihre Änderungen speichern und die APP jetzt neu starten, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="e8b73-142">If you save your changes and restart the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
