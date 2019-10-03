<!-- markdownlint-disable MD002 MD041 -->

In dieser Übung erweitern Sie die Anwendung aus der vorherigen Übung, um die Authentifizierung mit Azure AD zu unterstützen. Dies ist erforderlich, um das erforderliche OAuth-Zugriffstoken zum Aufrufen von Microsoft Graph zu erhalten. Hierzu integrieren Sie die [Microsoft-Authentifizierungsbibliothek (MSAL) für IOS](https://github.com/AzureAD/microsoft-authentication-library-for-objc) in die Anwendung.

1. Erstellen Sie eine neue **Eigenschaftenlisten** Datei im **GraphTutorial** -Projekt mit dem Namen **AuthSettings. plist**.
1. Fügen Sie die folgenden Elemente zur Datei im **Stamm** Wörterbuch hinzu.

    | Schlüssel | Typ | Wert |
    |-----|------|-------|
    | `AppId` | Zeichenfolge | Die Anwendungs-ID aus dem Azure-Portal |
    | `GraphScopes` | Array | Zwei Zeichenfolgenwerte `User.Read` : und`Calendars.Read` |

    ![Ein Screenshot der Datei AuthSettings. plist in Xcode](./images/auth-settings.png)

> [!IMPORTANT]
> Wenn Sie die Quellcodeverwaltung wie git verwenden, wäre es nun ein guter Zeitpunkt, die Datei **AuthSettings. plist** aus der Quellcodeverwaltung auszuschließen, um unbeabsichtigtes Auslaufen ihrer APP-ID zu vermeiden.

## <a name="implement-sign-in"></a>Implementieren der Anmeldung

In diesem Abschnitt Konfigurieren Sie das Projekt für MSAL, erstellen eine Authentifizierungs-Manager-Klasse und aktualisieren die APP, um sich anzumelden und abzumelden.

### <a name="configure-project-for-msal"></a>Konfigurieren des Projekts für MSAL

1. Steuerelement klicken Sie auf **Info. plist** , und wählen Sie dann **Öffnen als**, dann **Quellcode**aus.
1. Fügen Sie das folgende innerhalb `<dict>` des-Elements hinzu.

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

1. Öffnen Sie **AppDelegate. Swift** , und fügen Sie die folgende Importanweisung am Anfang der Datei hinzu.

    ```Swift
    import MSAL
    ```

1. Fügen Sie die folgende Funktion zur `AppDelegate`-Klasse hinzu:

    ```Swift
    func application(_ app: UIApplication, open url: URL, options: [UIApplication.OpenURLOptionsKey : Any] = [:]) -> Bool {

        guard let sourceApplication = options[UIApplication.OpenURLOptionsKey.sourceApplication] as? String else {
            return false
        }

        return MSALPublicClientApplication.handleMSALResponse(url, sourceApplication: sourceApplication)
    }
    ```

### <a name="create-authentication-manager"></a>Erstellen des Authentifizierungs-Managers

1. Erstellen Sie eine neue **SWIFT-Datei** im **GraphTutorial** -Projekt mit dem Namen **AuthenticationManager. Swift**. Fügen Sie den folgenden Code in die Datei ein:

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

### <a name="add-sign-in-and-sign-out"></a>Hinzufügen von Anmelde-und Abmeldungen

1. Öffnen Sie **SignInViewController. Swift** , und ersetzen Sie den Inhalt durch den folgenden Code.

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

1. Öffnen Sie **WelcomeViewController. Swift** , und ersetzen `signOut` Sie die vorhandene Funktion durch Folgendes.

    ```Swift
    @IBAction func signOut() {
        AuthenticationManager.instance.signOut()
        self.performSegue(withIdentifier: "userSignedOut", sender: nil)
    }
    ```

1. Speichern Sie Ihre Änderungen, und starten Sie die Anwendung im Simulator neu.

Wenn Sie sich bei der App anmelden, sollten Sie ein Zugriffstoken sehen, das im Ausgabefenster von Xcode angezeigt wird.

![Screenshot des Ausgabefensters in Xcode mit einem Zugriffstoken](./images/access-token-output.png)

## <a name="get-user-details"></a>Abrufen von Benutzer Details

In diesem Abschnitt erstellen Sie eine Hilfsklasse, die alle Aufrufe von Microsoft Graph enthält, und aktualisieren Sie `WelcomeViewController` , um die neue Klasse zum Abrufen des angemeldeten Benutzers zu verwenden.

1. Öffnen Sie **AuthenticationManager. Swift** , und fügen Sie der `AuthenticationManager` -Klasse die folgende Funktion hinzu.

    ```Swift
    public func getGraphAuthProvider() -> MSALAuthenticationProvider? {
        // Create an MSAL auth provider for use with the Graph client
        return MSALAuthenticationProvider(publicClientApplication: self.publicClient,
                                          andScopes: self.graphScopes)
    }
    ```

1. Erstellen Sie eine neue **SWIFT-Datei** im **GraphTutorial** -Projekt mit dem Namen **graphmanager. Swift**. Fügen Sie den folgenden Code in die Datei ein:

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

1. Öffnen Sie **WelcomeViewController. Swift** , und fügen `import` Sie die folgende Anweisung am Anfang der Datei hinzu.

    ```Swift
    import MSGraphClientModels
    ```

1. Fügen Sie der- `WelcomeViewController` Klasse die folgende Eigenschaft hinzu.

    ```Swift
    private let spinner = SpinnerViewController()
    ```

1. Ersetzen Sie den `viewDidLoad` vorhandenen durch den folgenden Code.

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

Wenn Sie Ihre Änderungen speichern und die APP jetzt neu starten, wird die Benutzeroberfläche nach der Anmeldung mit dem Anzeigenamen und der e-Mail-Adresse des Benutzers aktualisiert.
