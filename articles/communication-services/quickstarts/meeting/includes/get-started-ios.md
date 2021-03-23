---
title: Szybki Start — Dodawanie do aplikacji dla systemu iOS za pomocą usług Azure Communication Services
description: W tym przewodniku szybki start dowiesz się, jak używać biblioteki osadzania zespołów usługi Azure Communication Services dla systemu iOS.
author: palatter
ms.author: palatter
ms.date: 01/25/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: 4d28864d41d6540afc87126daf589ed2929f891d
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104804007"
---
W tym przewodniku szybki start dowiesz się, jak przyłączyć się do spotkania zespołów przy użyciu biblioteki Osadź zespoły usługi Azure Communication Services dla systemu iOS.

## <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć ten przewodnik Szybki Start, potrzebne są następujące wymagania wstępne:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Na komputerze Mac z systemem [Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532), wraz z prawidłowym certyfikatem dewelopera zainstalowanym w łańcuchu kluczy.
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- [Token dostępu użytkownika](../../access-tokens.md) dla usługi komunikacyjnej platformy Azure.
- [CocoaPods](https://cocoapods.org/) zainstalowano dla środowiska kompilacji.

## <a name="setting-up"></a>Konfigurowanie

### <a name="creating-the-xcode-project"></a>Tworzenie projektu Xcode

W Xcode Utwórz nowy projekt dla systemu iOS i wybierz szablon **aplikacji** . Będziemy używać scenorysów strukturze UIKit. Nie chcesz tworzyć testów w trakcie tego przewodnika Szybki Start. Możesz usunąć zaznaczenie opcji **Dołącz testy**.

:::image type="content" source="../media/ios/xcode-new-project-template-select.png" alt-text="Zrzut ekranu przedstawiający nowy wybór szablonu projektu w Xcode.":::

Nadaj nazwę projektowi `TeamsEmbedGettingStarted` .

:::image type="content" source="../media/ios/xcode-new-project-details.png" alt-text="Zrzut ekranu przedstawiający szczegóły nowego projektu w programie Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalowanie pakietu i zależności za pomocą CocoaPods

1. Utwórz plik podfile dla swojej aplikacji:

```
platform :ios, '12.0'
use_frameworks!

target 'TeamsEmbedGettingStarted' do
    pod 'AzureCommunication', '~> 1.0.0-beta.8'
end

azure_libs = [
'AzureCommunication',
'AzureCore']

post_install do |installer|
    installer.pods_project.targets.each do |target|
    if azure_libs.include?(target.name)
        puts "Adding BUILD_LIBRARY_FOR_DISTRIBUTION to #{target.name}"
        target.build_configurations.each do |config|
        xcconfig_path = config.base_configuration_reference.real_path
        File.open(xcconfig_path, "a") {|file| file.puts "BUILD_LIBRARY_FOR_DISTRIBUTION = YES"}
        end
    end
    end
end
```

2. Uruchom polecenie `pod install`.
3. Otwórz wygenerowane `.xcworkspace` z Xcode.

### <a name="request-access-to-the-microphone-camera-etc"></a>Zażądaj dostępu do mikrofonu, aparatu itp.

Aby uzyskać dostęp do sprzętu urządzenia, zaktualizuj listę właściwości informacji o aplikacji. Ustaw skojarzoną wartość `string` , która zostanie uwzględniona w oknie dialogowym, którego system używa do żądania dostępu od użytkownika.

Kliknij prawym przyciskiem myszy `Info.plist` wpis drzewa projektu i wybierz polecenie **Otwórz jako**  >  **kod źródłowy**. Dodaj następujące wiersze w sekcji najwyższego poziomu `<dict>` , a następnie Zapisz plik.

```xml
<key>NSBluetoothAlwaysUsageDescription</key>
<string></string>
<key>NSBluetoothPeripheralUsageDescription</key>
<string></string>
<key>NSCameraUsageDescription</key>
<string></string>
<key>NSContactsUsageDescription</key>
<string></string>
<key>NSMicrophoneUsageDescription</key>
<string></string>
```

### <a name="add-the-teams-embed-framework"></a>Dodaj platformę osadzania zespołów

1. Pobierz platformę.
2. Utwórz `Frameworks` folder w katalogu głównym projektu. Np. `\TeamsEmbedGettingStarted\Frameworks\`
3. Skopiuj pobrane `TeamsAppSDK.framework` i `MeetingUIClient.framework` struktury do tego folderu.
4. Dodaj `TeamsAppSDK.framework` i `MeetingUIClient.framework` do elementu docelowego projektu na karcie Ogólne. Użyj, `Add Other`  ->  `Add Files...` Aby przejść do plików struktury i dodać je.

:::image type="content" source="../media/ios/xcode-add-frameworks.png" alt-text="Zrzut ekranu przedstawiający dodane struktury w Xcode.":::

5. Jeśli tak nie jest, Dodaj `$(PROJECT_DIR)/Frameworks` do `Framework Search Paths` pozycji na karcie Ustawienia kompilacji docelowej projektu. Aby znaleźć ustawienie, należy zmienić filtr z `basic` na `all` , można również użyć paska wyszukiwania po prawej stronie.

:::image type="content" source="../media/ios/xcode-add-framework-search-path.png" alt-text="Zrzut ekranu przedstawiający ścieżkę wyszukiwania struktury w Xcode.":::

### <a name="turn-off-bitcode"></a>Wyłącz kodu bitowego

Ustaw `Enable Bitcode` opcję na wartość `No` w ustawieniach kompilacji projektu. Aby znaleźć ustawienie, należy zmienić filtr z `basic` na `all` , można również użyć paska wyszukiwania po prawej stronie.

:::image type="content" source="../media/ios/xcode-bitcode-option.png" alt-text="Zrzut ekranu przedstawiający opcję kodu bitowego w Xcode.":::

### <a name="add-framework-signing-script"></a>Dodaj skrypt podpisywania struktury

Wybierz aplikację docelową i wybierz `Build Phases` kartę.  Następnie kliknij przycisk `+` , a następnie `New Run Script Phase` . Upewnij się, że ta nowa faza występuje po `Embed Frameworks` fazach.



:::image type="content" source="../media/ios/xcode-build-script.png" alt-text="Zrzut ekranu przedstawiający Dodawanie skryptu kompilacji w Xcode.":::

```bash
#!/bin/sh
if [ -d "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks ]; then
    pushd "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    for EACH in *.framework; do
        echo "-- signing ${EACH}"
        /usr/bin/codesign --force --deep --sign "${EXPANDED_CODE_SIGN_IDENTITY}" --entitlements "${TARGET_TEMP_DIR}/${PRODUCT_NAME}.app.xcent" --timestamp=none $EACH
        echo "-- moving ${EACH}"
        mv -nv ${EACH} ../../
    done
    rm -rf "${TARGET_BUILD_DIR}"/"${PRODUCT_NAME}".app/Frameworks/TeamsAppSDK.framework/Frameworks
    popd
    echo "BUILD DIR ${TARGET_BUILD_DIR}"
fi
```

### <a name="turn-on-voice-over-ip-background-mode"></a>Włącz tryb komunikacji głosowej w tle IP.

Wybierz aplikację docelową i kliknij kartę możliwości.

Włącz, `Background Modes` klikając `+ Capabilities` u góry i wybierz pozycję `Background Modes` .

Zaznacz pole wyboru `Voice over IP` .

:::image type="content" source="../media/ios/xcode-background-voip.png" alt-text="Zrzut ekranu przedstawiający włączoną funkcję VOIP w Xcode.":::

### <a name="add-a-window-reference-to-appdelegate"></a>Dodaj odwołanie do okna do AppDelegate

Otwórz plik **AppDelegate. Swift** projektu i Dodaj odwołanie do "window".

```swift
class AppDelegate: UIResponder, UIApplicationDelegate {

    var window: UIWindow?
```

### <a name="add-a-button-to-the-viewcontroller"></a>Dodaj przycisk do plik viewcontroller

Utwórz przycisk w `viewDidLoad` wywołaniu zwrotnym w **plik viewcontroller. Swift**.

```swift
override func viewDidLoad() {
    super.viewDidLoad()
    
    let button = UIButton(frame: CGRect(x: 100, y: 100, width: 200, height: 50))
    button.backgroundColor = .black
    button.setTitle("Join Meeting", for: .normal)
    button.addTarget(self, action: #selector(joinMeetingTapped), for: .touchUpInside)
    
    button.translatesAutoresizingMaskIntoConstraints = false
    self.view.addSubview(button)
    button.centerXAnchor.constraint(equalTo: view.centerXAnchor).isActive = true
    button.centerYAnchor.constraint(equalTo: view.centerYAnchor).isActive = true
```

Utwórz punkt wylotu dla przycisku w **plik viewcontroller. Swift**.

```swift
@IBAction func joinMeetingTapped(_ sender: UIButton) {

}
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Otwórz plik **plik viewcontroller. Swift** projektu i Dodaj `import` deklarację na początku pliku, aby zaimportować `AzureCommunication library` i `MeetingUIClient` . 

```swift
import UIKit
import AzureCommunication
import MeetingUIClient
```

Zastąp implementację `ViewController` klasy prostym przyciskiem, aby zezwolić użytkownikowi na dołączenie do spotkania. Będziemy dołączać logikę biznesową do przycisku w tym przewodniku Szybki Start.

```swift
class ViewController: UIViewController {

    private var meetingUIClient: MeetingUIClient?
    
    override func viewDidLoad() {
        super.viewDidLoad()

        // Initialize meetingClient
    }

    @IBAction func joinMeetingTapped(_ sender: UIButton) {
        joinMeeting()
    }
    
    private func joinMeeting() {
        // Add join meeting logic
    }
}
```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje biblioteki osadzanej zespołów usługi Azure Communications Services:

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| MeetingUIClient | MeetingUIClient jest głównym punktem wejścia do biblioteki osadzania zespołów. |
| MeetingUIClientDelegate | MeetingUIClientDelegate jest używany do odbierania zdarzeń, takich jak zmiany w stanie wywołania. |
| MeetingJoinOptions | MeetingJoinOptions są używane na potrzeby konfigurowalnych opcji, takich jak nazwa wyświetlana. | 
| CallState | CallState jest używany do raportowania zmian stanu wywołań. Dostępne są następujące opcje: łączenie, waitingInLobby, połączone i zakończone. |

## <a name="create-and-authenticate-the-client"></a>Tworzenie i uwierzytelnianie klienta

Zainicjuj `MeetingUIClient` wystąpienie za pomocą tokenu dostępu użytkownika, który umożliwi nam dołączanie do spotkań. Dodaj następujący kod do `viewDidLoad` wywołania zwrotnego w **plik viewcontroller. Swift**:

```swift
do {
    let communicationTokenRefreshOptions = CommunicationTokenRefreshOptions(initialToken: "<USER_ACCESS_TOKEN>", refreshProactively: true, tokenRefresher: fetchTokenAsync(completionHandler:))
    let credential = try CommunicationTokenCredential(with: communicationTokenRefreshOptions)
    meetingUIClient = MeetingUIClient(with: credential)
}
catch {
    print("Failed to create communication token credential")
}
```

Zamień na `<USER_ACCESS_TOKEN>` prawidłowy token dostępu użytkownika dla zasobu. Jeśli nie masz jeszcze dostępnego tokenu, zapoznaj się z dokumentacją [tokenu dostępu użytkownika](../../access-tokens.md) .

### <a name="setup-token-refreshing"></a>Odświeżanie tokenu Instalatora

Utwórz metodę `fetchTokenAsync`. Następnie Dodaj `fetchToken` logikę, aby uzyskać token użytkownika.

```swift
private func fetchTokenAsync(completionHandler: @escaping TokenRefreshOnCompletion) {
    func getTokenFromServer(completionHandler: @escaping (String) -> Void) {
        completionHandler("<USER_ACCESS_TOKEN>")
    }
    getTokenFromServer { newToken in
        completionHandler(newToken, nil)
    }
}
```

Zamień na `<USER_ACCESS_TOKEN>` prawidłowy token dostępu użytkownika dla zasobu.

## <a name="join-a-meeting"></a>Dołącz do spotkania

`joinMeeting`Metoda jest ustawiana jako akcja, która będzie wykonywana po naciśnięciu przycisku *Dołącz do spotkania* . Zaktualizuj implementację, aby dołączyć do spotkania przy użyciu `MeetingUIClient` :

```swift
private func joinMeeting() {
    let meetingJoinOptions = MeetingJoinOptions(displayName: "John Smith")
        
    meetingUIClient?.join(meetingUrl: "<MEETING_URL>", meetingJoinOptions: meetingJoinOptions, completionHandler: { (error: Error?) in
        if (error != nil) {
            print("Join meeting failed: \(error!)")
        }
    })
}
```

Zamień `<MEETING URL>` na link ze spotkaniem zespołów.

### <a name="get-a-teams-meeting-link"></a>Uzyskaj link do spotkania zespołów

Link do spotkania zespołów można pobrać przy użyciu interfejsów API programu Graph. Jest to szczegółowo opisany w [dokumentacji programu Graph](/graph/api/onlinemeeting-createorget?tabs=http&view=graph-rest-beta&preserve-view=true).
Zestaw SDK wywoływania usług komunikacyjnych akceptuje łącze do spotkania z pełnymi zespołami. Ten link jest zwracany jako część `onlineMeeting` zasobu, który jest dostępny w ramach [ `joinWebUrl` Właściwości](/graph/api/resources/onlinemeeting?view=graph-rest-beta&preserve-view=true) , można także uzyskać wymagane informacje o spotkaniu z adresu URL **spotkania dołączania** w przypadku, gdy spotkanie zespołu się zaprosiło.

## <a name="run-the-code"></a>Uruchamianie kodu

Możesz skompilować i uruchomić aplikację w symulatorze systemu iOS, wybierając opcję  >  **Uruchom** produkt lub używając skrótu klawiaturowego (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-join-meeting.png" alt-text="Końcowy wygląd i działanie aplikacji szybkiego startu":::

:::image type="content" source="../media/ios/quick-start-meeting-joined.png" alt-text="Końcowy wygląd i działanie po dołączeniu spotkania":::

> [!NOTE]
> Po pierwszym dołączeniu do spotkania system wyświetli monit o uzyskanie dostępu do mikrofonu. W aplikacji produkcyjnej należy użyć `AVAudioSession` interfejsu API, aby [sprawdzić stan uprawnień](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources) i bezpiecznie aktualizować zachowanie aplikacji po udzieleniu uprawnienia.

## <a name="add-localization-support-based-on-your-app"></a>Dodawanie obsługi lokalizacji na podstawie aplikacji

Zestaw SDK Microsoft Teams obsługuje ponad 100 ciągów i zasobów. Pakiet Framework zawiera Języki podstawowe i angielskie. Pozostałe z nich są zawarte w `Localizations.zip` pliku dołączonym do pakietu.

#### <a name="add-localizations-to-the-sdk-based-on-what-your-app-supports"></a>Dodawanie lokalizacji do zestawu SDK na podstawie zawartości obsługiwanej przez aplikację

1. Określanie rodzaju lokalizacji obsługiwanych przez aplikację z listy lokalizacji > > informacji o projekcie aplikacji
2. Rozpakuj Localizations.zip dołączone do pakietu
3. Skopiuj foldery lokalizacji z folderu rozpakowanego na podstawie zawartości obsługiwanej przez aplikację w katalogu głównym TeamsAppSDK. Framework

## <a name="preparation-for-app-store-upload"></a>Przygotowanie do przekazywania ze sklepu App Store

Usuń architektury i386 i x86_64 z platform w przypadku archiwizowania.

Dodaj `i386` skrypt i `x86_64` Architektura usuwania skryptu do faz kompilacji przed fazą projektowania platformy, jeśli chcesz zarchiwizować aplikację.

W Nawigatorze projektu wybierz projekt. W okienku Edytora przejdź do pozycji kompilacja faz → kliknij pozycję + Podpisz → Utwórz nową fazę skryptu uruchomienia.

```bash
echo "Target architectures: $ARCHS"
APP_PATH="${TARGET_BUILD_DIR}/${WRAPPER_NAME}"
find "$APP_PATH" -name '*.framework' -type d | while read -r FRAMEWORK
do
FRAMEWORK_EXECUTABLE_NAME=$(defaults read "$FRAMEWORK/Info.plist" CFBundleExecutable)
FRAMEWORK_EXECUTABLE_PATH="$FRAMEWORK/$FRAMEWORK_EXECUTABLE_NAME"
echo "Executable is $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
FRAMEWORK_TMP_PATH="$FRAMEWORK_EXECUTABLE_PATH-tmp"
# remove simulator's archs if location is not simulator's directory
case "${TARGET_BUILD_DIR}" in
*"iphonesimulator")
    echo "No need to remove archs"
    ;;
*)
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "i386") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "i386" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "i386 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    if $(lipo "$FRAMEWORK_EXECUTABLE_PATH" -verify_arch "x86_64") ; then
    lipo -output "$FRAMEWORK_TMP_PATH" -remove "x86_64" "$FRAMEWORK_EXECUTABLE_PATH"
    echo "x86_64 architecture removed"
    rm "$FRAMEWORK_EXECUTABLE_PATH"
    mv "$FRAMEWORK_TMP_PATH" "$FRAMEWORK_EXECUTABLE_PATH"
    fi
    ;;
esac
echo "Completed for executable $FRAMEWORK_EXECUTABLE_PATH"
echo $(lipo -info "$FRAMEWORK_EXECUTABLE_PATH")
done
```

## <a name="sample-code"></a>Przykładowy kod

Możesz pobrać przykładową aplikację z usługi [GitHub](https://github.com/Azure-Samples/teams-embed-ios-getting-started)
