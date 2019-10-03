<!-- markdownlint-disable MD002 MD041 -->

<span data-ttu-id="3322a-101">In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen.</span><span class="sxs-lookup"><span data-stu-id="3322a-101">In this exercise you will extend the application from the previous exercise to support authentication with Azure AD.</span></span> <span data-ttu-id="3322a-102">Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten.</span><span class="sxs-lookup"><span data-stu-id="3322a-102">This is required to obtain the necessary OAuth access token to call the Microsoft Graph.</span></span> <span data-ttu-id="3322a-103">Hierzu integrieren Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) in die Anwendung.</span><span class="sxs-lookup"><span data-stu-id="3322a-103">To do this, you will integrate the [Microsoft Authentication Library (MSAL) for iOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) into the application.</span></span>

1. <span data-ttu-id="3322a-104">Erstellen Sie eine neue **Eigenschaftenlisten** Datei im **GraphTutorial** -Projekt mit dem Namen **AuthSettings. plist**.</span><span class="sxs-lookup"><span data-stu-id="3322a-104">Create a new **Property List** file in the **GraphTutorial** project named **AuthSettings.plist**.</span></span>
1. <span data-ttu-id="3322a-105">Fügen Sie die folgenden Elemente zur Datei im **Stamm** Wörterbuch hinzu.</span><span class="sxs-lookup"><span data-stu-id="3322a-105">Add the following items to the file in the **Root** dictionary.</span></span>

    | <span data-ttu-id="3322a-106">Schlüssel</span><span class="sxs-lookup"><span data-stu-id="3322a-106">Key</span></span> | <span data-ttu-id="3322a-107">Typ</span><span class="sxs-lookup"><span data-stu-id="3322a-107">Type</span></span> | <span data-ttu-id="3322a-108">Wert</span><span class="sxs-lookup"><span data-stu-id="3322a-108">Value</span></span> |
    |-----|------|-------|
    | `AppId` | <span data-ttu-id="3322a-109">Zeichenfolge</span><span class="sxs-lookup"><span data-stu-id="3322a-109">String</span></span> | <span data-ttu-id="3322a-110">Die Anwendungs-ID aus dem Azure-Portal</span><span class="sxs-lookup"><span data-stu-id="3322a-110">The application ID from the Azure portal</span></span> |
    | `GraphScopes` | <span data-ttu-id="3322a-111">Array</span><span class="sxs-lookup"><span data-stu-id="3322a-111">Array</span></span> | <span data-ttu-id="3322a-112">Zwei Zeichenfolgenwerte `User.Read` : und`Calendars.Read`</span><span class="sxs-lookup"><span data-stu-id="3322a-112">Two String values: `User.Read` and `Calendars.Read`</span></span> |

    ![Ein Screenshot der Datei AuthSettings. plist in Xcode](./images/auth-settings.png)

> [!IMPORTANT]
> <span data-ttu-id="3322a-114">Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es nun ein guter Zeitpunkt, die Datei **AuthSettings. plist** aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.</span><span class="sxs-lookup"><span data-stu-id="3322a-114">If you're using source control such as git, now would be a good time to exclude the **AuthSettings.plist** file from source control to avoid inadvertently leaking your app ID.</span></span>

## <a name="implement-sign-in"></a><span data-ttu-id="3322a-115">Implementieren der Anmeldung</span><span class="sxs-lookup"><span data-stu-id="3322a-115">Implement sign-in</span></span>

<span data-ttu-id="3322a-116">In diesem Abschnitt Konfigurieren Sie das Projekt für MSAL, erstellen eine Authentifizierungs-Manager-Klasse und aktualisieren die APP, um sich anzumelden und abzumelden.</span><span class="sxs-lookup"><span data-stu-id="3322a-116">In this section you will configure the project for MSAL, create an authentication manager class, and update the app to sign in and sign out.</span></span>

### <a name="configure-project-for-msal"></a><span data-ttu-id="3322a-117">Konfigurieren des Projekts für MSAL</span><span class="sxs-lookup"><span data-stu-id="3322a-117">Configure project for MSAL</span></span>

1. <span data-ttu-id="3322a-118">Steuerelement klicken Sie auf **Info. plist** , und wählen Sie dann **Öffnen als**, dann **Quellcode**aus.</span><span class="sxs-lookup"><span data-stu-id="3322a-118">Control click **Info.plist** and select **Open As**, then **Source Code**.</span></span>
1. <span data-ttu-id="3322a-119">Fügen Sie das folgende innerhalb `<dict>` des-Elements hinzu.</span><span class="sxs-lookup"><span data-stu-id="3322a-119">Add the following inside the `<dict>` element.</span></span>

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
    <array>
        <string>msauthv2</string>
        <string>msauthv3</string>
    </array>
    ```

1. <span data-ttu-id="3322a-120">Öffnen Sie **AppDelegate. Swift** , und fügen Sie die folgende Importanweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="3322a-120">Open **AppDelegate.swift** and add the following import statement at the top of the file.</span></span>

    ```Swift
    import MSAL
    ```

1. <span data-ttu-id="3322a-121">Fügen Sie die folgende Funktion zur `AppDelegate`-Klasse hinzu:</span><span class="sxs-lookup"><span data-stu-id="3322a-121">Add the following function to the `AppDelegate` class.</span></span>

    ```Swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
    ```

### <a name="create-authentication-manager"></a><span data-ttu-id="3322a-122">Erstellen des Authentifizierungs-Managers</span><span class="sxs-lookup"><span data-stu-id="3322a-122">Create authentication manager</span></span>

1. <span data-ttu-id="3322a-123">Erstellen Sie eine neue **SWIFT-Datei** im **GraphTutorial** -Projekt mit dem Namen **AuthenticationManager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="3322a-123">Create a new **Swift File** in the **GraphTutorial** project named **AuthenticationManager.swift**.</span></span> <span data-ttu-id="3322a-124">Fügen Sie den folgenden Code in die Datei ein:</span><span class="sxs-lookup"><span data-stu-id="3322a-124">Add the following code to the file.</span></span>

    ```Swift
    import Foundation
    import MSAL

    class AuthenticationManager {

        // Implement singleton pattern
        static let instance = AuthenticationManager()

        private let publicClient: MSALPublicClientApplication?
        private let appId: String
        private let graphScopes: Array<String>

        private init() {
            // Get app ID and scopes from AuthSettings.plist
            let bundle = Bundle.main
            let authConfigPath = bundle.path(forResource: "AuthSettings", ofType: "plist")!
            let authConfig = NSDictionary(contentsOfFile: authConfigPath)!

            self.appId = authConfig["AppId"] as! String
            self.graphScopes = authConfig["GraphScopes"] as! Array<String>

            do {
                // Create the MSAL client
                try self.publicClient = MSALPublicClientApplication(clientId: self.appId,
                                                                    keychainGroup: bundle.bundleIdentifier)
            } catch {
                print("Error creating MSAL public client: \(error)")
                self.publicClient = nil
            }
        }

        public func getPublicClient() -> MSALPublicClientApplication? {
            return self.publicClient
        }

        public func getTokenInteractively(completion: @escaping(_ accessToken: String?, Error?) -> Void) {
            // Call acquireToken to open a browser so the user can sign in
            publicClient?.acquireToken(forScopes: self.graphScopes, completionBlock: {
                (result: MSALResult?, error: Error?) in
                guard let tokenResult = result, error == nil else {
                    print("Error getting token interactively: \(String(describing: error))")
                    completion(nil, error)
                    return
                }

                print("Got token interactively: \(tokenResult.accessToken)")
                completion(tokenResult.accessToken, nil)
            })
        }

        public func getTokenSilently(completion: @escaping(_ accessToken: String?, Error?) -> Void) {
            // Check if there is an account in the cache
            var userAccount: MSALAccount?

            do {
                userAccount = try publicClient?.allAccounts().first
            } catch {
                print("Error getting account: \(error)")
            }

            if (userAccount != nil) {
                // Attempt to get token silently
                publicClient?.acquireTokenSilent(forScopes: self.graphScopes, account: userAccount!, completionBlock: {
                    (result: MSALResult?, error: Error?) in
                    guard let tokenResult = result, error == nil else {
                        print("Error getting token silently: \(String(describing: error))")
                        completion(nil, error)
                        return
                    }

                    print("Got token silently: \(tokenResult.accessToken)")
                    completion(tokenResult.accessToken, nil)
                })
            } else {
                print("No account in cache")
                completion(nil, NSError(domain: "AuthenticationManager",
                                        code: MSALErrorCode.interactionRequired.rawValue, userInfo: nil))
            }
        }

        public func signOut() -> Void {
            do {
                // Remove all accounts from the cache
                let accounts = try publicClient?.allAccounts()

                try accounts!.forEach({
                    (account: MSALAccount) in
                    try publicClient?.remove(account)
                })
            } catch {
                print("Sign out error: \(String(describing: error))")
            }
        }
    }
    ```

### <a name="add-sign-in-and-sign-out"></a><span data-ttu-id="3322a-125">Hinzufügen von Anmelde-und Abmeldungen</span><span class="sxs-lookup"><span data-stu-id="3322a-125">Add sign-in and sign-out</span></span>

1. <span data-ttu-id="3322a-126">Öffnen Sie **SignInViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="3322a-126">Open **SignInViewController.swift** and replace its contents with the following code.</span></span>

    ```Swift
    import UIKit

    class SignInViewController: UIViewController {

        private let spinner = SpinnerViewController()

        override func viewDidLoad() {
            super.viewDidLoad()
            // Do any additional setup after loading the view.

            // See if a user is already signed in
            spinner.start(container: self)

            AuthenticationManager.instance.getTokenSilently {
                (token: String?, error: Error?) in

                DispatchQueue.main.async {
                    self.spinner.stop()

                    guard let _ = token, error == nil else {
                        // If there is no token or if there's an error,
                        // no user is signed in, so stay here
                        return
                    }

                    // Since we got a token, a user is signed in
                    // Go to welcome page
                    self.performSegue(withIdentifier: "userSignedIn", sender: nil)
                }
            }
        }

        @IBAction func signIn() {
            spinner.start(container: self)

            // Do an interactive sign in
            AuthenticationManager.instance.getTokenInteractively {
                (token: String?, error: Error?) in

                DispatchQueue.main.async {
                    self.spinner.stop()

                    guard let _ = token, error == nil else {
                        // Show the error and stay on the sign-in page
                        let alert = UIAlertController(title: "Error signing in",
                                                      message: error.debugDescription,
                                                      preferredStyle: .alert)

                        alert.addAction(UIAlertAction(title: "OK", style: .default, handler: nil))
                        self.present(alert, animated: true)
                        return
                    }

                    // Signed in successfully
                    // Go to welcome page
                    self.performSegue(withIdentifier: "userSignedIn", sender: nil)
                }
            }
        }
    }
    ```

1. <span data-ttu-id="3322a-127">Öffnen Sie **WelcomeViewController. Swift** , und ersetzen `signOut` Sie die vorhandene Funktion durch Folgendes.</span><span class="sxs-lookup"><span data-stu-id="3322a-127">Open **WelcomeViewController.swift** and replace the existing `signOut` function with the following.</span></span>

    ```Swift
    @IBAction func signOut() {
        AuthenticationManager.instance.signOut()
        self.performSegue(withIdentifier: "userSignedOut", sender: nil)
    }
    ```

1. <span data-ttu-id="3322a-128">Speichern Sie Ihre Änderungen, und starten Sie die Anwendung im Simulator neu.</span><span class="sxs-lookup"><span data-stu-id="3322a-128">Save your changes and restart the application in Simulator.</span></span>

<span data-ttu-id="3322a-129">Wenn Sie sich bei der App anmelden, sollten Sie ein Zugriffstoken sehen, das im Ausgabefenster von Xcode angezeigt wird.</span><span class="sxs-lookup"><span data-stu-id="3322a-129">If you sign in to the app, you should see an access token displayed in the output window in Xcode.</span></span>

![Screenshot des Ausgabefensters in Xcode mit einem Zugriffstoken](./images/access-token-output.png)

## <a name="get-user-details"></a><span data-ttu-id="3322a-131">Abrufen von Benutzer Details</span><span class="sxs-lookup"><span data-stu-id="3322a-131">Get user details</span></span>

<span data-ttu-id="3322a-132">In diesem Abschnitt erstellen Sie eine Hilfsklasse, die alle Aufrufe von Microsoft Graph enthält, und aktualisieren Sie `WelcomeViewController` , um die neue Klasse zum Abrufen des angemeldeten Benutzers zu verwenden.</span><span class="sxs-lookup"><span data-stu-id="3322a-132">In this section you will create a helper class to hold all of the calls to Microsoft Graph and update the `WelcomeViewController` to use this new class to get the logged-in user.</span></span>

1. <span data-ttu-id="3322a-133">Öffnen Sie **AuthenticationManager. Swift** , und fügen Sie der `AuthenticationManager` -Klasse die folgende Funktion hinzu.</span><span class="sxs-lookup"><span data-stu-id="3322a-133">Open **AuthenticationManager.swift** and add the following function to the `AuthenticationManager` class.</span></span>

    ```Swift
    public func getGraphAuthProvider() -> MSALAuthenticationProvider? {
        // Create an MSAL auth provider for use with the Graph client
        return MSALAuthenticationProvider(publicClientApplication: self.publicClient,
                                          andScopes: self.graphScopes)
    }
    ```

1. <span data-ttu-id="3322a-134">Erstellen Sie eine neue **SWIFT-Datei** im **GraphTutorial** -Projekt mit dem Namen **graphmanager. Swift**.</span><span class="sxs-lookup"><span data-stu-id="3322a-134">Create a new **Swift File** in the **GraphTutorial** project named **GraphManager.swift**.</span></span> <span data-ttu-id="3322a-135">Fügen Sie den folgenden Code in die Datei ein:</span><span class="sxs-lookup"><span data-stu-id="3322a-135">Add the following code to the file.</span></span>

    ```Swift
    import Foundation
    import MSGraphClientSDK
    import MSGraphClientModels

    class GraphManager {

        // Implement singleton pattern
        static let instance = GraphManager()

        private let client: MSHTTPClient?

        private init() {
            client = MSClientFactory.createHTTPClient(with: AuthenticationManager.instance.getGraphAuthProvider())
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

1. <span data-ttu-id="3322a-136">Öffnen Sie **WelcomeViewController. Swift** , und fügen `import` Sie die folgende Anweisung am Anfang der Datei hinzu.</span><span class="sxs-lookup"><span data-stu-id="3322a-136">Open **WelcomeViewController.swift** and add the following `import` statement at the top of the file.</span></span>

    ```Swift
    import MSGraphClientModels
    ```

1. <span data-ttu-id="3322a-137">Fügen Sie der- `WelcomeViewController` Klasse die folgende Eigenschaft hinzu.</span><span class="sxs-lookup"><span data-stu-id="3322a-137">Add the following property to the `WelcomeViewController` class.</span></span>

    ```Swift
    private let spinner = SpinnerViewController()
    ```

1. <span data-ttu-id="3322a-138">Ersetzen Sie den `viewDidLoad` vorhandenen durch den folgenden Code.</span><span class="sxs-lookup"><span data-stu-id="3322a-138">Replace the existing `viewDidLoad` with the following code.</span></span>

    ```Swift
    override func viewDidLoad() {
        super.viewDidLoad()

        // Do any additional setup after loading the view.

        self.spinner.start(container: self)

        // Get the signed-in user
        self.userProfilePhoto.image = UIImage(imageLiteralResourceName: "DefaultUserPhoto")

        GraphManager.instance.getMe {
            (user: MSGraphUser?, error: Error?) in

            DispatchQueue.main.async {
                self.spinner.stop()

                guard let currentUser = user, error == nil else {
                    print("Error getting user: \(String(describing: error))")
                    return
                }

                // Set display name
                self.userDisplayName.text = currentUser.displayName ?? "Mysterious Stranger"
                self.userDisplayName.sizeToFit()

                // AAD users have email in the mail attribute
                // Personal accounts have email in the userPrincipalName attribute
                self.userEmail.text = currentUser.mail ?? currentUser.userPrincipalName ?? ""
                self.userEmail.sizeToFit()
            }
        }
    }
    ```

<span data-ttu-id="3322a-139">Wenn Sie Ihre Änderungen speichern und die APP jetzt neu starten, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.</span><span class="sxs-lookup"><span data-stu-id="3322a-139">If you save your changes and restart the app now, after sign-in the UI is updated with the user's display name and email address.</span></span>
