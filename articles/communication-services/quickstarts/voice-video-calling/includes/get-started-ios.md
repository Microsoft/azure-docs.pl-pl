---
title: Szybki start — dodawanie wywołań do aplikacji systemu iOS przy użyciu Azure Communication Services
description: Z tego przewodnika Szybki start dowiesz się, jak używać zestawu SDK wywoływania Azure Communication Services dla systemu iOS.
author: chpalm
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e1eed3f9449843e6c2dd8c77719402e709fdeb23
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107327282"
---
W tym przewodniku Szybki start dowiesz się, jak uruchomić wywołanie przy użyciu zestawu SDK wywoływania aplikacji Azure Communication Services dla systemu iOS.

[!INCLUDE [Public Preview Notice](../../../includes/public-preview-include-android-ios.md)]

> [!NOTE]
> W tym dokumencie jest używana wersja 1.0.0-beta.9 zestawu SDK wywołującego.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka potrzebne są następujące wymagania wstępne:

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Komputer Mac z [uruchomionym programem Xcode](https://go.microsoft.com/fwLink/p/?LinkID=266532)wraz z prawidłowym certyfikatem dewelopera zainstalowanym w twoim kluczu.
- Wdrożony zasób Communication Services zasobów. [Utwórz zasób Communication Services zasobów.](../../create-communication-resource.md)
- Token [dostępu użytkownika dla](../../access-tokens.md) usługi Azure Communication Service.

## <a name="setting-up"></a>Konfigurowanie

### <a name="creating-the-xcode-project"></a>Tworzenie projektu Xcode

W programie Xcode utwórz nowy projekt systemu iOS i wybierz szablon **Aplikacja z pojedynczym widokiem.** W tym samouczku jest [używana framework SwiftUI,](https://developer.apple.com/xcode/swiftui/)dlatego należy ustawić wartość **Language** na **Swift,** a w **Interfejs użytkownika** wartość **SwiftUI.** Nie zamierzasz tworzyć testów podczas pracy z tym przewodnikem Szybki start. Możesz usunąć zaznaczenie pola wyboru **Uwzględnij testy.**

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Zrzut ekranu przedstawiający okno Nowy projekt w programie Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalowanie pakietu i zależności za pomocą programu CocoaPods

1. Aby utworzyć plik Podfile dla aplikacji, otwórz terminal, przejdź do folderu projektu i uruchom ```pod init```
3. Dodaj następujący kod do pliku Podfile i zapisz (upewnij się, że element "target" odpowiada nazwie projektu):

   ```
   platform :ios, '13.0'
   use_frameworks!

   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.9'
     pod 'AzureCommunication', '~> 1.0.0-beta.9'
     pod 'AzureCore', '~> 1.0.0-beta.9'
   end
   ```

3. Uruchom polecenie `pod install`.
3. Otwórz program `.xcworkspace` za pomocą Xcode.

### <a name="request-access-to-the-microphone"></a>Żądanie dostępu do mikrofonu

Aby uzyskać dostęp do mikrofonu urządzenia, należy zaktualizować listę właściwości informacji aplikacji za `NSMicrophoneUsageDescription` pomocą . Skojarzoną wartość należy ustawić na wartość , która zostanie uwzględniona w oknie dialogowym używanym przez system do żądania `string` dostępu od użytkownika.

Kliknij prawym przyciskiem `Info.plist` myszy wpis drzewa projektu i wybierz pozycję Open As Source Code **(Otwórz jako** kod  >  **źródłowy).** Dodaj następujące wiersze w sekcji najwyższego `<dict>` poziomu, a następnie zapisz plik.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Otwórz plik **ContentView.swift** projektu i dodaj deklarację na początku pliku, aby `import` zaimportować plik `AzureCommunicationCalling library` . Ponadto zaimportuj `AVFoundation` plik , który będzie potrzebny w przypadku żądania uprawnień audio w kodzie.

```swift
import AzureCommunicationCalling
import AVFoundation
```

Zastąp implementację struktury prostymi kontrolkami interfejsu użytkownika, które umożliwiają użytkownikowi zainicjowanie `ContentView` i zakończenie wywołania. W tym przewodniku Szybki start dołączymy logikę biznesową do tych kontrolek.

```swift
struct ContentView: View {
    @State var callee: String = ""
    @State var callClient: CallClient?
    @State var callAgent: CallAgent?
    @State var call: Call?

    var body: some View {
        NavigationView {
            Form {
                Section {
                    TextField("Who would you like to call?", text: $callee)
                    Button(action: startCall) {
                        Text("Start Call")
                    }.disabled(callAgent == nil)
                    Button(action: endCall) {
                        Text("End Call")
                    }.disabled(call == nil)
                }
            }
            .navigationBarTitle("Calling Quickstart")
        }.onAppear {
            // Initialize call agent
        }
    }

    func startCall() {
        // Ask permissions
        AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
            if granted {
                // Add start call logic
            }
        }
    }

    func endCall() {
        // Add end call logic
    }
}
```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje zestawu AZURE COMMUNICATION SERVICES SDK:

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient | CallClient jest głównym punktem wejścia do wywołującego zestawu SDK.|
| CallAgent | CallAgent służy do uruchamiania wywołań i zarządzania nimi. |
| CommunicationTokenCredential | Token CommunicationTokenCredential jest używany jako poświadczenie tokenu do wystąpienia wywołania CallAgent.| 
| CommunicationUserIdentifier | CommunicationUserIdentifier służy do reprezentowania tożsamości użytkownika, która może być jedną z następujących czynności: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

## <a name="authenticate-the-client"></a>Uwierzytelnianie klienta

`CallAgent`Zaimicjuj wystąpienie za pomocą tokenu dostępu użytkownika, który umożliwi nam odbieranie i odbieranie wywołań. Dodaj następujący kod do wywołania `onAppear` zwrotnego w **contentView.swift:**

```swift
var userCredential: CommunicationTokenCredential?
do {
    userCredential = try CommunicationTokenCredential(token: "<USER ACCESS TOKEN>")
} catch {
    print("ERROR: It was not possible to create user credential.")
    return
}

self.callClient = CallClient()

// Creates the call agent
self.callClient?.createCallAgent(userCredential: userCredential) { (agent, error) in
    if error != nil {
        print("ERROR: It was not possible to create a call agent.")
        return
    }

    if let agent = agent {
        self.callAgent = agent
        print("Call agent successfully created.")
    }
}
```

Należy zastąpić `<USER ACCESS TOKEN>` wartością prawidłowym tokenem dostępu użytkownika dla zasobu. Zapoznaj się z [dokumentacją tokenu dostępu](../../access-tokens.md) użytkownika, jeśli nie masz jeszcze dostępnego tokenu.

## <a name="start-a-call"></a>Uruchamianie wywołania

Metoda `startCall` jest ustawiana jako akcja, która  zostanie wykonana po naciśniu przycisku Uruchom wywołanie. Zaktualizuj implementację, aby uruchomić wywołanie za pomocą `ASACallAgent` :

```swift
func startCall()
{
    // Ask permissions
    AVAudioSession.sharedInstance().requestRecordPermission { (granted) in
        if granted {
            // start call logic
            let callees:[CommunicationIdentifier] = [CommunicationUserIdentifier(identifier: self.callee)]
            self.call = self.callAgent?.startCall(participants: callees, options: StartCallOptions())
        }
    }
}
```

Możesz również użyć właściwości w pliku , aby ustawić początkowe opcje wywołania (tj. umożliwia to rozpoczęcie wywołania z `StartCallOptions` wyciszeniem mikrofonu).

## <a name="end-a-call"></a>Zakończenie wywołania

`endCall`Zaim implementuj metodę  , aby zakończyć bieżące wywołanie po naciśniu przycisku Zakończ wywołanie.

```swift
func endCall()
{    
    self.call!.hangUp(HangUpOptions()) { (error) in
        if (error != nil) {
            print("ERROR: It was not possible to hangup the call.")
        }
    }
}
```

## <a name="run-the-code"></a>Uruchamianie kodu

Aplikację można skompilować i uruchomić w symulatorze systemu iOS, wybierając pozycję **Uruchomienie** produktu lub używając  >   skrótu klawiaturowego (&#8984;-R).

:::image type="content" source="../media/ios/quick-start-make-call.png" alt-text="Końcowy wygląd i sposób pracy aplikacji Szybki start":::

Możesz wykonać wychodzące wywołanie VOIP, podając identyfikator użytkownika w polu tekstowym i naciskając przycisk **Rozpocznij wywołanie.** Wywołanie umożliwia połączenie z echo botem. Jest to doskonałe miejsce do rozpoczęcia pracy i sprawdzenia, `8:echo123` czy urządzenia audio działają. 

> [!NOTE]
> Przy pierwszym wywołaniu system wyświetli monit o dostęp do mikrofonu. W aplikacji produkcyjnej należy użyć interfejsu API, aby sprawdzić stan uprawnień i bezpiecznie zaktualizować zachowanie aplikacji, gdy `AVAudioSession` nie zostanie przyznane uprawnienie. [](https://developer.apple.com/documentation/uikit/protecting_the_user_s_privacy/requesting_access_to_protected_resources)

## <a name="sample-code"></a>Przykładowy kod

Przykładową aplikację możesz pobrać z usługi [GitHub](https://github.com/Azure-Samples/communication-services-ios-quickstarts/tree/main/Add%20Voice%20Calling)
