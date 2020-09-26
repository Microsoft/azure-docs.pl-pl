---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 31f7e348a805c86964a8856fb81b83831c611de5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91377521"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- A, `User Access Token` Aby włączyć klienta wywołania. Aby uzyskać więcej informacji na temat [uzyskiwania `User Access Token` ](../../access-tokens.md)
- Opcjonalnie: Ukończ Przewodnik Szybki Start dotyczący [dodawania wywołania do aplikacji](../getting-started-with-calling.md)

## <a name="setting-up"></a>Konfigurowanie

### <a name="creating-the-xcode-project"></a>Tworzenie projektu Xcode

W Xcode Utwórz nowy projekt dla systemu iOS i wybierz szablon **aplikacji pojedynczego widoku** . Ten przewodnik Szybki Start korzysta z [platformy SwiftUI](https://developer.apple.com/xcode/swiftui/), więc należy ustawić **Język** **SWIFT** i **interfejs użytkownika** na **SwiftUI**. Nie ma potrzeby tworzenia testów jednostkowych ani testów interfejsu użytkownika w ramach tego przewodnika Szybki Start. Możesz usunąć zaznaczenie opcji **Dołącz testy jednostkowe** , a także Usuń zaznaczenie pola wyboru **Dołącz testy interfejsu użytkownika**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Zrzut ekranu przedstawiający okno Tworzenie nowego projektu w programie Xcode.":::

### <a name="install-the-package"></a>Zainstaluj pakiet

Dodaj do projektu bibliotekę klienta wywołującą usługi Azure Communication Services oraz jej zależności (AzureCore. Framework i AzureCommunication. Framework).

> [!NOTE]
> Wraz z wydaniem zestawu AzureCommunicationCalling SDK znajdziesz skrypt bash `BuildAzurePackages.sh` . Skrypt, gdy zostanie uruchomiony, `sh ./BuildAzurePackages.sh` zapewnia ścieżkę do wygenerowanych pakietów platformy, które należy zaimportować do przykładowej aplikacji w następnym kroku. Należy pamiętać, że konieczne będzie skonfigurowanie narzędzi wiersza polecenia Xcode, jeśli nie zostało to zrobione przed uruchomieniem skryptu: Start Xcode, wybierz pozycję "Preferencje-> lokalizacje". Wybierz wersję Xcode dla narzędzi wiersza polecenia. **Należy zauważyć, że skrypt BuildAzurePackages.sh działa tylko z Xcode 11,5 i nowszymi.**

1. Pobierz bibliotekę kliencką klienta usługi Azure Communications Services dla systemu iOS.
2. W Xcode kliknij plik projektu i wybierz miejsce docelowe kompilacji, aby otworzyć Edytor ustawień projektu.
3. Na karcie **Ogólne** przewiń do sekcji **struktury, biblioteki i osadzona zawartość** , a następnie kliknij ikonę **"+"** .
4. W lewym dolnym rogu okna dialogowego wybierz pozycję **Dodaj pliki**, przejdź do katalogu **AzureCommunicationCalling. Framework** w pakiecie biblioteki klienta unspakowany.
    1. Powtórz ostatni krok w celu dodania **AzureCore. Framework** i **AzureCommunication. Framework**.
5. Otwórz kartę **Ustawienia kompilacji** w edytorze ustawień projektu i przewiń do sekcji **ścieżki wyszukiwania** . Dodaj nowy wpis **ścieżki wyszukiwania struktury** dla katalogu zawierającego **AzureCommunicationCalling. Framework**.
    1. Dodaj kolejną pozycję ścieżki wyszukiwania struktury wskazującej folder zawierający zależności.

:::image type="content" source="../media/ios/xcode-framework-search-paths.png" alt-text="Zrzut ekranu przedstawiający aktualizowanie ścieżek wyszukiwania struktury w programie XCode.":::

### <a name="request-access-to-the-microphone"></a>Zażądaj dostępu do mikrofonu

Aby można było uzyskać dostęp do mikrofonu urządzenia, należy zaktualizować listę właściwości informacji aplikacji za pomocą `NSMicrophoneUsageDescription` . Należy ustawić skojarzoną wartość `string` , która zostanie uwzględniona w oknie dialogowym, którego system używa do żądania dostępu od użytkownika.

Kliknij prawym przyciskiem myszy `Info.plist` wpis drzewa projektu i wybierz polecenie **Otwórz jako**  >  **kod źródłowy**. Dodaj następujące wiersze w sekcji najwyższego poziomu `<dict>` , a następnie Zapisz plik.

```xml
<key>NSMicrophoneUsageDescription</key>
<string>Need microphone access for VOIP calling.</string>
```

### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Otwórz plik **contentView. Swift** projektu i Dodaj `import` deklarację na początku pliku w celu zaimportowania `AzureCommunicationCalling library` . Ponadto `AVFoundation` należy zaimportować ten kod do żądania uprawnień audio w kodzie.

```swift
import AzureCommunicationCalling
import AVFoundation
```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje usługi komunikacyjnej platformy Azure wywołującej bibliotekę klienta dla systemu iOS.


| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| ACSCallClient | ACSCallClient jest głównym punktem wejścia do biblioteki wywołującej klienta.|
| ACSCallAgent | ACSCallAgent jest używany do uruchamiania wywołań i zarządzania nimi. |
| CommunicationUserCredential | CommunicationUserCredential jest używany jako poświadczenia tokenu do tworzenia wystąpienia CallAgent.| 
| CommunicationIndentifier | CommunicationIndentifier jest używany do reprezentowania tożsamości użytkownika, który może być jedną z następujących: CommunicationUser/numer telefonu/CallingApplication. |

> [!NOTE]
> Podczas implementowania delegatów zdarzeń aplikacja musi przechowywać silną referencję do obiektów, które wymagają subskrypcji zdarzeń. Na przykład gdy `ACSRemoteParticipant` obiekt jest zwracany podczas wywoływania `call.addParticipant` metody, a aplikacja ustawia delegata do nasłuchiwania `ACSRemoteParticipantDelegate` , aplikacja musi przechowywać silne odwołanie do `ACSRemoteParticipant` obiektu. W przeciwnym razie, jeśli ten obiekt zostanie zebrany, delegat zgłosi wyjątek krytyczny, gdy wywoływany zestaw SDK próbuje wywołać obiekt.

## <a name="initialize-the-acscallagent"></a>Inicjowanie ACSCallAgent

Aby utworzyć `ACSCallAgent` wystąpienie z `ACSCallClient` użyciem `callClient.createCallAgent` metody, która asynchronicznie zwraca `ACSCallAgent` obiekt po zainicjowaniu

Aby utworzyć klienta wywołania, należy przekazać `CommunicationUserCredential` obiekt.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationUserCredential?
do {
    userCredential = try CommunicationUserCredential(
        initialToken: tokenString, refreshProactively: true,
        tokenRefresher: self.fetchTokenSync
    )
} catch {
    print("Failed to create CommunicationCredential object")
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Przekaż obiekt CommunicationUserCredential utworzony powyżej do ACSCallClient

```swift

callClient = ACSCallClient()
callClient?.createCallAgent(userCredential!,
    withCompletionHandler: { (callAgent, error) in
        if error != nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Umieść wywołanie wychodzące

Aby utworzyć i uruchomić wywołanie, należy wywołać jeden z interfejsów API w systemie `ACSCallAgent` i podać tożsamość usługi komunikacyjnej użytkownika, który został zainicjowany przy użyciu biblioteki klienta zarządzania usługami komunikacyjnymi.

Tworzenie i uruchamianie wywołań jest synchroniczne. Otrzymasz wystąpienie wywołania, które pozwala subskrybować wszystkie zdarzenia w wywołaniu.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Nawiązać połączenie 1:1 z użytkownikiem lub 1: n wywołaniem z użytkownikami i PSTN

```swift

let callees = [CommunicationUser(identifier: 'acsUserId')]
let oneToOneCall = self.CallingApp.callAgent.call(participants: callees, options: ACSStartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Umieszczenie 1: n połączenia z użytkownikami i PSTN
Aby nawiązać połączenie z usługą PSTN, musisz określić numer telefonu uzyskany przy użyciu usług komunikacyjnych.
```swift

let pstnCallee = PhoneNumber('+1999999999')
let callee = CommunicationUser(identifier: 'acsUserId')
let groupCall = self.CallingApp.callAgent.call(participants: [pstnCallee, callee], options: ACSStartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Umieść wywołanie 1:1 z funkcją with wideo
Aby uzyskać wystąpienie Menedżera urządzeń, zapoznaj się [tutaj](#device-management)

```swift

let camera = self.deviceManager!.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(camera)
let videoOptions = ACSVideoOptions(localVideoStream)

let startCallOptions = ACSStartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUser(identifier: 'acsUserId')
let call = self.callAgent?.call([callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Dołącz do wywołania grupy
Aby przyłączyć się do wywołania, należy wywołać jeden z interfejsów API w *CallAgent*

```swift

let groupCallContext = ACSGroupCallContext()
groupCallContext?.groupId = UUID(uuidString: "uuid_string")!
let call = self.callAgent?.join(with: groupCallContext, joinCallOptions: ACSJoinCallOptions())

```

## <a name="push-notification"></a>Powiadomienie push

Mobilne Powiadomienie wypychane to powiadomienie wyskakujące, które otrzymujesz na urządzeniu przenośnym. W przypadku wywoływania funkcji będziemy koncentrować się na powiadomieniach wypychanych (Voice over Internet Protocol). Firma Microsoft oferuje możliwość rejestrowania powiadomień wypychanych, obsługi powiadomień wypychanych i wyrejestrowania powiadomień wypychanych.

### <a name="prerequisite"></a>Wymaganie wstępne

- Krok 1: Xcode > podpisywania & możliwości — > Dodawanie możliwości-> "powiadomienia wypychane"
- Krok 2: Xcode > podpisywania & możliwości — > Dodawanie możliwości-> "tryby tła"
- Krok 3. "tryby tła" — > wybierz pozycję "Voice over IP" i "powiadomienia zdalne"

:::image type="content" source="../media/ios/xcode-push-notification.png" alt-text="Zrzut ekranu przedstawiający sposób dodawania funkcji w Xcode." lightbox="../media/ios/xcode-push-notification.png":::

#### <a name="register-for-push-notifications"></a>Zarejestruj się w celu otrzymywania powiadomień wypychanych

Aby zarejestrować się na potrzeby powiadomień wypychanych, wywołaj registerPushNotification () w wystąpieniu *CallAgent* z tokenem rejestracji urządzenia.

Po pomyślnej inicjacji należy wywołać rejestr powiadomień wypychanych. Gdy `callAgent` obiekt zostanie zniszczony, `logout` zostanie wywołany, co spowoduje automatyczne Wyrejestrowanie powiadomień wypychanych.


```swift

let deviceToken: Data = pushRegistry?.pushToken(for: PKPushType.voIP)
callAgent.registerPushNotifications(deviceToken,
                withCompletionHandler: { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
})

```

#### <a name="push-notification-handling"></a>Obsługa powiadomień wypychanych
Aby odbierać przychodzące wywołania powiadomień wypychanych, wywołaj *handlePushNotification ()* w wystąpieniu *CallAgent* z ładunkiem słownika.

```swift

let dictionaryPayload = pushPayload?.dictionaryPayload
callAgent.handlePushNotification(dictionaryPayload, withCompletionHandler: { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
})

```
#### <a name="unregister-push-notification"></a>Wyrejestruj Powiadomienie wypychane

Aplikacje mogą wyrejestrować powiadomienia wypychane w dowolnym momencie. Po prostu wywołaj `unRegisterPushNotification` metodę w *CallAgent*.
> [!NOTE]
> Aplikacje nie są automatycznie wyrejestrowani z powiadomień wypychanych po wylogowaniu.

```swift

callAgent.unRegisterPushNotifications(completionHandler: { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
})

```

## <a name="mid-call-operations"></a>Operacje w połowie połączenia

Możesz wykonywać różne operacje w trakcie wywołania, aby zarządzać ustawieniami związanymi z wideo i dźwiękiem.

### <a name="mute-and-unmute"></a>Wycisz i Wycisz

Aby wyciszyć lub wyłączyć lokalny punkt końcowy, można użyć `mute` i `unmute` asynchronicznych interfejsów API:

```swift
call.mute(completionHandler: { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
})

```

Komunikacji Odciszenie lokalne

```swift
call.unmute(completionHandler:{ (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
})
```

### <a name="start-and-stop-sending-local-video"></a>Uruchamianie i zatrzymywanie wysyłania lokalnego wideo

Aby rozpocząć wysyłanie lokalnego wideo do innych uczestników wywołania, użyj `startVideo` interfejsu API i przekaż `localVideoStream``camera`

```swift

let firstCamera: ACSVideoDeviceInfo? = self.deviceManager?.getCameraList()![0]
let localVideoStream = ACSLocalVideoStream(firstCamera)

call.startVideo(localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    }
    else {
        print("Local video failed to start")
    }
}

```

Po rozpoczęciu wysyłania wideo `ACSLocalVideoStream` wystąpienie jest dodawane do `localVideoStreams` kolekcji w wystąpieniu wywołania:

```swift

call.localVideoStreams[0]

```

Komunikacji Aby zatrzymać lokalne wideo, Przekaż `localVideoStream` zwrócone z wywołania `call.startVideo` :

```swift

call.stopVideo(localVideoStream,{ (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    }
    else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Zdalne zarządzanie uczestnikami

Wszyscy uczestnicy zdalni są reprezentowani przez `ACSRemoteParticipant` Typ i są dostępni za pomocą `remoteParticipants` kolekcji w wystąpieniu wywołania:

### <a name="list-participants-in-a-call"></a>Wyświetlanie listy uczestników wywołania

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Właściwości uczestnika zdalnego

```swift

// [ACSRemoteParticipantDelegate] delegate - an object you provide to receive events from this ACSRemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ACSParticipantStateIdle = 0, ACSParticipantStateEarlyMedia = 1, ACSParticipantStateConnecting = 2, ACSParticipantStateConnected = 3, ACSParticipantStateOnHold = 4, ACSParticipantStateInLobby = 5, ACSParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [ACSError] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// ACSRemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [ACSRemoteVideoStream, ACSRemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Dodawanie uczestnika do wywołania

Aby dodać uczestnika do wywołania (użytkownika lub numeru telefonu), można wywołać `addParticipant` . Spowoduje to synchroniczne zwrócenie wystąpienia uczestnika zdalnego.

```swift

let remoteParticipantAdded: ACSRemoteParticipant = call.addParticipant(CommunicationUser(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Usuwanie uczestnika z wywołania
Aby usunąć uczestnika z wywołania (użytkownika lub numeru telefonu), możesz wywołać  `removeParticipant` interfejs API. Zostanie to rozwiązane asynchronicznie.

```swift

call!.remove(remoteParticipantAdded) { (error) in
    if (error == nil) {
        print("Successfully removed participant")
    } else {
        print("Failed to remove participant")
    }
}

```

## <a name="render-remote-participant-video-streams"></a>Renderuj strumienie wideo zdalnego uczestnika

Uczestnicy zdalni mogą inicjować udostępnianie wideo lub ekranu podczas wywołania.

### <a name="handle-remote-participant-videoscreen-sharing-streams"></a>Obsługa strumieni zdalnego udostępniania wideo/filmów uczestnika

Aby wyświetlić listę strumieni zdalnych uczestników, należy sprawdzić `videoStreams` Kolekcje:

```swift

var remoteParticipantVideoStream = call.remoteParticipants[0].videoStreams[0]

```

### <a name="remote-video-stream-properties"></a>Właściwości zdalnego strumienia wideo

```swift

var type: ACSMediaStreamType = remoteParticipantVideoStream.type // 'ACSMediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Renderuj strumień zdalnego uczestnika

Aby rozpocząć renderowanie zdalnych strumieni uczestników:

```swift

let renderer: ACSRenderer? = ACSRenderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: ACSRendererView? = renderer?.createView(ACSRenderingOptions(ACSScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(ACSScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Metody i właściwości zdalnego modułu renderowania wideo

```swift
// [Bool] isRendering - indicating if stream is being rendered
remoteVideoRenderer.isRendering()
```

## <a name="device-management"></a>Zarządzanie urządzeniami

`DeviceManager` umożliwia Wyliczenie urządzeń lokalnych, które mogą być używane w wywołaniu przesyłania strumieni audio/wideo. Umożliwia także zażądanie uprawnień od użytkownika w celu uzyskania dostępu do mikrofonu/aparatu. Możesz uzyskać dostęp do `deviceManager` `callClient` obiektu:

```swift

self.callClient!.getDeviceManager(
    completionHandler: { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    })
```

### <a name="enumerate-local-devices"></a>Wyliczanie urządzeń lokalnych

Aby uzyskać dostęp do urządzeń lokalnych, można użyć metod wyliczania na Menedżer urządzeń. Wyliczenie jest akcją synchroniczną.

```swift
// enumerate local cameras
var localCameras = deviceManager.getCameraList() // [ACSVideoDeviceInfo, ACSVideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.getMicrophoneList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.getSpeakerList() // [ACSAudioDeviceInfo, ACSAudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Ustaw domyślny Mikrofon/głośnik

Menedżer urządzeń umożliwia ustawienie domyślnego urządzenia, które będzie używane podczas uruchamiania wywołania. Jeśli wartości domyślne stosu nie są ustawione, usługi komunikacyjne powracają do domyślnych ustawień systemu operacyjnego.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.getMicrophoneList()![0]
// [Synchronous] set microphone
deviceManager.setMicrophone(ACSAudioDeviceInfo())
// get first speaker
var firstSpeaker = self.deviceManager!.getSpeakerList()![0]
// [Synchronous] set speaker
deviceManager.setSpeakers(ACSAudioDeviceInfo())
```

### <a name="local-camera-preview"></a>Podgląd lokalnego aparatu fotograficznego

Możesz użyć, `ACSRenderer` Aby rozpocząć renderowanie strumienia z aparatu lokalnego. Ten strumień nie będzie wysyłany do innych uczestników; jest to lokalna wersja zapoznawcza. Jest to akcja asynchroniczna.

```swift

let camera: ACSVideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: ACSLocalVideoStream = ACSLocalVideoStream(camera)
let renderer: ACSRenderer = ACSRenderer(localVideoStream: localVideoStream)
self.view = renderer!.createView(ACSRenderingOptions())

```

### <a name="local-camera-preview-properties"></a>Właściwości podglądu lokalnego aparatu fotograficznego

Moduł renderowania ma zestaw właściwości i metod, które umożliwiają kontrolowanie renderowania:

```swift

// Constructor can take in ACSLocalVideoStream or ACSRemoteVideoStream
let localRenderer = ACSRenderer(localVideoStream:localVideoStream)
let remoteRenderer = ACSRenderer(remoteVideoStream:remoteVideoStream)

// [ACSStreamSize] size of the rendering view
localRenderer.size

// [ACSRendererDelegate] an object you provide to receive events from this ACSRenderer instance
localRenderer.delegate

// [Synchronous] create view with rendering options
localRenderer.createView(options:ACSRenderingOptions())
// [Synchronous] dispose rendering view
localRenderer.dispose()

```

## <a name="eventing-model"></a>Model zdarzeń

Aby otrzymywać powiadomienia o zmianach wartości, można subskrybować większość właściwości i kolekcji.

### <a name="properties"></a>Właściwości
Aby subskrybować `property changed` zdarzenia:

```swift
call.delegate = self
// Get the property of the call state by doing get on the call's state member
public func onCallStateChanged(_ call: ACSCall!,
                               _ args: ACSPropertyChangedEventArgs!)
{
    print("Callback from SDK when the call state changes, current state: " + call.state.rawValue)
}

 // to unsubscribe
 self.call.delegate = nil

```

### <a name="collections"></a>Kolekcje
Aby subskrybować `collection updated` zdarzenia:

```swift
call.delegate = self
// Collection contains the streams that were added or removed only
public func onLocalVideoStreamsChanged(_ call: ACSCall!,
                                       _ args: ACSLocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
