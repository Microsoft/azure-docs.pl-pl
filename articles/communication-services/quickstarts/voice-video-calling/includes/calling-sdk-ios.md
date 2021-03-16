---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: e9c889dcffe42fde244f8a35ce42032e84d78fff
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103488124"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- A, `User Access Token` Aby włączyć klienta wywołania. Aby uzyskać więcej informacji na temat [uzyskiwania `User Access Token` ](../../access-tokens.md)
- Opcjonalnie: Ukończ Przewodnik Szybki Start dotyczący [dodawania wywołania do aplikacji](../getting-started-with-calling.md)

## <a name="setting-up"></a>Konfigurowanie

### <a name="creating-the-xcode-project"></a>Tworzenie projektu Xcode

> [!NOTE]
> Ten dokument używa wersji 1.0.0-beta. 8 z wywołującej biblioteki klienta.

W Xcode Utwórz nowy projekt dla systemu iOS i wybierz szablon **aplikacji pojedynczego widoku** . Ten przewodnik Szybki Start korzysta z [platformy SwiftUI](https://developer.apple.com/xcode/swiftui/), więc należy ustawić **Język** **SWIFT** i **interfejs użytkownika** na **SwiftUI**. Nie ma potrzeby tworzenia testów jednostkowych ani testów interfejsu użytkownika w ramach tego przewodnika Szybki Start. Możesz usunąć zaznaczenie opcji **Dołącz testy jednostkowe** , a także Usuń zaznaczenie pola wyboru **Dołącz testy interfejsu użytkownika**.

:::image type="content" source="../media/ios/xcode-new-ios-project.png" alt-text="Zrzut ekranu przedstawiający okno Tworzenie nowego projektu w programie Xcode.":::

### <a name="install-the-package-and-dependencies-with-cocoapods"></a>Instalowanie pakietu i zależności za pomocą CocoaPods

1. Utwórz plik podfile dla aplikacji, tak jak to:

   ```
   platform :ios, '13.0'
   use_frameworks!
   target 'AzureCommunicationCallingSample' do
     pod 'AzureCommunicationCalling', '~> 1.0.0-beta.8'
     pod 'AzureCommunication', '~> 1.0.0-beta.8'
     pod 'AzureCore', '~> 1.0.0-beta.8'
   end
   ```

2. Uruchom polecenie `pod install`.
3. Otwórz `.xcworkspace` program with Xcode.

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
| CallClient | CallClient jest głównym punktem wejścia do biblioteki wywołującej klienta.|
| CallAgent | CallAgent jest używany do uruchamiania wywołań i zarządzania nimi. |
| CommunicationTokenCredential | CommunicationTokenCredential jest używany jako poświadczenia tokenu do tworzenia wystąpienia CallAgent.| 
| CommunicationIdentifier | CommunicationIdentifier jest używany do reprezentowania tożsamości użytkownika, który może być jedną z następujących: CommunicationUserIdentifier/PhoneNumberIdentifier/CallingApplication. |

> [!NOTE]
> Podczas implementowania delegatów zdarzeń aplikacja musi przechowywać silną referencję do obiektów, które wymagają subskrypcji zdarzeń. Na przykład gdy `RemoteParticipant` obiekt jest zwracany podczas wywoływania `call.addParticipant` metody, a aplikacja ustawia delegata do nasłuchiwania `RemoteParticipantDelegate` , aplikacja musi przechowywać silne odwołanie do `RemoteParticipant` obiektu. W przeciwnym razie, jeśli ten obiekt zostanie zebrany, delegat zgłosi wyjątek krytyczny, gdy wywoływany zestaw SDK próbuje wywołać obiekt.

## <a name="initialize-the-callagent"></a>Inicjowanie CallAgent

Aby utworzyć `CallAgent` wystąpienie z `CallClient` użyciem `callClient.createCallAgent` metody, która asynchronicznie zwraca `CallAgent` obiekt po zainicjowaniu

Aby utworzyć klienta wywołania, należy przekazać `CommunicationTokenCredential` obiekt.

```swift

import AzureCommunication

let tokenString = "token_string"
var userCredential: CommunicationTokenCredential?
var userCredential: CommunicationTokenCredential?
   do {
       userCredential = try CommunicationTokenCredential(with: CommunicationTokenRefreshOptions(initialToken: token, 
                                                                     refreshProactively: true,
                                                                     tokenRefresher: self.fetchTokenSync))
   } catch {
       return
}

// tokenProvider needs to be implemented by contoso which fetches new token
public func fetchTokenSync(then onCompletion: TokenRefreshOnCompletion) {
    let newToken = self.tokenProvider!.fetchNewToken()
    onCompletion(newToken, nil)
}
```

Przekaż `CommunicationTokenCredential` obiekt utworzony powyżej do `CallClient` i Ustaw nazwę wyświetlaną.

```swift

callClient = CallClient()
let callAgentOptions:CallAgentOptions = CallAgentOptions()!
options.displayName = " iOS User"

callClient?.createCallAgent(userCredential: userCredential!,
    options: callAgentOptions) { (callAgent, error) in
        if error == nil {
            print("Create agent succeeded")
            self.callAgent = callAgent
        } else {
            print("Create agent failed")
        }
})

```

## <a name="place-an-outgoing-call"></a>Umieść wywołanie wychodzące

Aby utworzyć i uruchomić wywołanie, należy wywołać jeden z interfejsów API w systemie `CallAgent` i podać tożsamość usługi komunikacyjnej użytkownika, który został zainicjowany przy użyciu biblioteki klienta zarządzania usługami komunikacyjnymi.

Tworzenie i uruchamianie wywołań jest synchroniczne. Otrzymasz wystąpienie wywołania, które pozwala subskrybować wszystkie zdarzenia w wywołaniu.

### <a name="place-a-11-call-to-a-user-or-a-1n-call-with-users-and-pstn"></a>Nawiązać połączenie 1:1 z użytkownikiem lub 1: n wywołaniem z użytkownikami i PSTN

```swift

let callees = [CommunicationUser(identifier: 'UserId')]
let oneToOneCall = self.callAgent.call(participants: callees, options: StartCallOptions())

```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Umieszczenie 1: n połączenia z użytkownikami i PSTN
Aby nawiązać połączenie z usługą PSTN, musisz określić numer telefonu uzyskany przy użyciu usług komunikacyjnych.
```swift

let pstnCallee = PhoneNumberIdentifier(phoneNumber: '+1999999999')
let callee = CommunicationUserIdentifier(identifier: 'UserId')
let groupCall = self.callAgent.call(participants: [pstnCallee, callee], options: StartCallOptions())

```

### <a name="place-a-11-call-with-with-video"></a>Umieść wywołanie 1:1 z funkcją with wideo
Aby uzyskać wystąpienie Menedżera urządzeń, zapoznaj się [tutaj](#device-management)

```swift

let camera = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: camera)
let videoOptions = VideoOptions(localVideoStream: localVideoStream)

let startCallOptions = StartCallOptions()
startCallOptions?.videoOptions = videoOptions

let callee = CommunicationUserIdentifier(identifier: 'UserId')
let call = self.callAgent?.call(participants: [callee], options: startCallOptions)

```

### <a name="join-a-group-call"></a>Dołącz do wywołania grupy
Aby przyłączyć się do wywołania, należy wywołać jeden z interfejsów API w *CallAgent*

```swift

let groupCallLocator = GroupCallLocator(groupId: UUID(uuidString: "uuid_string"))!
let call = self.callAgent?.join(with: groupCallLocator, joinCallOptions: JoinCallOptions())

```

### <a name="subscribe-for-incoming-call"></a>Subskrybowanie wywołania przychodzącego
Subskrybowanie przychodzącego zdarzenia wywołania

```
final class IncomingCallHandler: NSObject, CallAgentDelegate, IncomingCallDelegate
{
    // Event raised when there is an incoming call
    public func onIncomingCall(_ callAgent: CallAgent!, incomingcall: IncomingCall!) {
        self.incomingCall = incomingcall
        // Subscribe to get OnCallEnded event
        self.incomingCall?.delegate = self
    }

    // Event raised when incoming call was not answered
    public func onCallEnded(_ incomingCall: IncomingCall!, args: PropertyChangedEventArgs!) {
        self.incomingCall = nil
    }
}
```

### <a name="accept-an-incoming-call"></a>Zaakceptuj wywołanie przychodzące
Aby zaakceptować wywołanie, wywołaj metodę "Accept" w obiekcie Call.
Ustaw delegata dla CallAgent 
```swift
final class CallHandler: NSObject, CallAgentDelegate
{
    public var incomingCall: Call?
 
    public func onCallsUpdated(_ callAgent: CallAgent!, args: CallsUpdatedEventArgs!) {
        if let incomingCall = args.addedCalls?.first(where: { $0.isIncoming }) {
            self.incomingCall = incomingCall
        }
    }
}

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)
let acceptCallOptions = AcceptCallOptions()
acceptCallOptions!.videoOptions = VideoOptions(localVideoStream:localVideoStream!)
if let incomingCall = CallHandler().incomingCall {
   incomingCall.accept(options: acceptCallOptions) { (call, error) in
               if error == nil {
                   print("Incoming call accepted")
               } else {
                   print("Failed to accept incoming call")
               }
           }
} else {
   print("No incoming call found to accept")
}
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
callAgent.registerPushNotifications(deviceToken: deviceToken) { (error) in
    if(error == nil) {
        print("Successfully registered to push notification.")
    } else {
        print("Failed to register push notification.")
    }
}

```

#### <a name="push-notification-handling"></a>Obsługa powiadomień wypychanych
Aby odbierać przychodzące wywołania powiadomień wypychanych, wywołaj *handlePushNotification ()* w wystąpieniu *CallAgent* z ładunkiem słownika.

```swift

let callNotification = IncomingCallInformation.from(payload: pushPayload?.dictionaryPayload)

callAgent.handlePush(notification: callNotification) { (error) in
    if (error != nil) {
        print("Handling of push notification failed")
    } else {
        print("Handling of push notification was successful")
    }
}

```
#### <a name="unregister-push-notification"></a>Wyrejestruj Powiadomienie wypychane

Aplikacje mogą wyrejestrować powiadomienia wypychane w dowolnym momencie. Po prostu wywołaj `unregisterPushNotification` metodę w *CallAgent*.
> [!NOTE]
> Aplikacje nie są automatycznie wyrejestrowani z powiadomień wypychanych po wylogowaniu.

```swift

callAgent.unregisterPushNotifications { (error) in
    if (error != nil) {
        print("Unregister of push notification failed, please try again")
    } else {
        print("Unregister of push notification was successful")
    }
}

```

## <a name="mid-call-operations"></a>Operacje w połowie połączenia

Możesz wykonywać różne operacje w trakcie wywołania, aby zarządzać ustawieniami związanymi z wideo i dźwiękiem.

### <a name="mute-and-unmute"></a>Wycisz i Wycisz

Aby wyciszyć lub wyłączyć lokalny punkt końcowy, można użyć `mute` i `unmute` asynchronicznych interfejsów API:

```swift
call!.mute { (error) in
    if error == nil {
        print("Successfully muted")
    } else {
        print("Failed to mute")
    }
}

```

Komunikacji Odciszenie lokalne

```swift
call!.unmute { (error) in
    if error == nil {
        print("Successfully un-muted")
    } else {
        print("Failed to unmute")
    }
}
```

### <a name="start-and-stop-sending-local-video"></a>Uruchamianie i zatrzymywanie wysyłania lokalnego wideo

Aby rozpocząć wysyłanie lokalnego wideo do innych uczestników wywołania, użyj `startVideo` interfejsu API i przekaż `localVideoStream``camera`

```swift

let firstCamera: VideoDeviceInfo? = self.deviceManager!.cameras!.first
let localVideoStream = LocalVideoStream(camera: firstCamera)

call!.startVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video started successfully")
    } else {
        print("Local video failed to start")
    }
}

```

Po rozpoczęciu wysyłania wideo `LocalVideoStream` wystąpienie jest dodawane do `localVideoStreams` kolekcji w wystąpieniu wywołania:

```swift

call.localVideoStreams[0]

```

Komunikacji Aby zatrzymać lokalne wideo, Przekaż `localVideoStream` zwrócone z wywołania `call.startVideo` :

```swift

call!.stopVideo(stream: localVideoStream) { (error) in
    if (error == nil) {
        print("Local video stopped successfully")
    } else {
        print("Local video failed to stop")
    }
}

```

## <a name="remote-participants-management"></a>Zdalne zarządzanie uczestnikami

Wszyscy uczestnicy zdalni są reprezentowani przez `RemoteParticipant` Typ i są dostępni za pomocą `remoteParticipants` kolekcji w wystąpieniu wywołania:

### <a name="list-participants-in-a-call"></a>Wyświetlanie listy uczestników wywołania

```swift

call.remoteParticipants

```

### <a name="remote-participant-properties"></a>Właściwości uczestnika zdalnego

```swift

// [RemoteParticipantDelegate] delegate - an object you provide to receive events from this RemoteParticipant instance
var remoteParticipantDelegate = remoteParticipant.delegate

// [CommunicationIdentifier] identity - same as the one used to provision token for another user
var identity = remoteParticipant.identity

// ParticipantStateIdle = 0, ParticipantStateEarlyMedia = 1, ParticipantStateConnecting = 2, ParticipantStateConnected = 3, ParticipantStateOnHold = 4, ParticipantStateInLobby = 5, ParticipantStateDisconnected = 6
var state = remoteParticipant.state

// [Error] callEndReason - reason why participant left the call, contains code/subcode/message
var callEndReason = remoteParticipant.callEndReason

// [Bool] isMuted - indicating if participant is muted
var isMuted = remoteParticipant.isMuted

// [Bool] isSpeaking - indicating if participant is currently speaking
var isSpeaking = remoteParticipant.isSpeaking

// RemoteVideoStream[] - collection of video streams this participants has
var videoStreams = remoteParticipant.videoStreams // [RemoteVideoStream, RemoteVideoStream, ...]

```

### <a name="add-a-participant-to-a-call"></a>Dodawanie uczestnika do wywołania

Aby dodać uczestnika do wywołania (użytkownika lub numeru telefonu), można wywołać `addParticipant` . Spowoduje to synchroniczne zwrócenie wystąpienia uczestnika zdalnego.

```swift

let remoteParticipantAdded: RemoteParticipant = call.add(participant: CommunicationUserIdentifier(identifier: "userId"))

```

### <a name="remove-a-participant-from-a-call"></a>Usuwanie uczestnika z wywołania
Aby usunąć uczestnika z wywołania (użytkownika lub numeru telefonu), możesz wywołać  `removeParticipant` interfejs API. Zostanie to rozwiązane asynchronicznie.

```swift

call!.remove(participant: remoteParticipantAdded) { (error) in
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

var type: MediaStreamType = remoteParticipantVideoStream.type // 'MediaStreamTypeVideo'

var isAvailable: Bool = remoteParticipantVideoStream.isAvailable // indicates if remote stream is available

var id: Int = remoteParticipantVideoStream.id // id of remoteParticipantStream

```

### <a name="render-remote-participant-stream"></a>Renderuj strumień zdalnego uczestnika

Aby rozpocząć renderowanie zdalnych strumieni uczestników:

```swift

let renderer: Renderer? = Renderer(remoteVideoStream: remoteParticipantVideoStream)
let targetRemoteParticipantView: RendererView? = renderer?.createView(with: RenderingOptions(scalingMode: ScalingMode.crop))
// To update the scaling mode later
targetRemoteParticipantView.update(scalingMode: ScalingMode.fit)

```

### <a name="remote-video-renderer-methods-and-properties"></a>Metody i właściwości zdalnego modułu renderowania wideo

```swift
// [Synchronous] dispose() - dispose renderer and all `RendererView` associated with this renderer. To be called when you have removed all associated views from the UI.
remoteVideoRenderer.dispose()
```

## <a name="device-management"></a>Zarządzanie urządzeniami

`DeviceManager` umożliwia Wyliczenie urządzeń lokalnych, które mogą być używane w wywołaniu przesyłania strumieni audio/wideo. Umożliwia także zażądanie uprawnień od użytkownika w celu uzyskania dostępu do mikrofonu/aparatu. Możesz uzyskać dostęp do `deviceManager` `callClient` obiektu:

```swift

self.callClient!.getDeviceManager { (deviceManager, error) in
        if (error == nil) {
            print("Got device manager instance")
            self.deviceManager = deviceManager
        } else {
            print("Failed to get device manager instance")
        }
    }
```

### <a name="enumerate-local-devices"></a>Wyliczanie urządzeń lokalnych

Aby uzyskać dostęp do urządzeń lokalnych, można użyć metod wyliczania na Device Manager. Wyliczenie jest akcją synchroniczną.

```swift
// enumerate local cameras
var localCameras = deviceManager.cameras! // [VideoDeviceInfo, VideoDeviceInfo...]
// enumerate local cameras
var localMicrophones = deviceManager.microphones! // [AudioDeviceInfo, AudioDeviceInfo...]
// enumerate local cameras
var localSpeakers = deviceManager.speakers! // [AudioDeviceInfo, AudioDeviceInfo...]
``` 

### <a name="set-default-microphonespeaker"></a>Ustaw domyślny Mikrofon/głośnik

Menedżer urządzeń umożliwia ustawienie domyślnego urządzenia, które będzie używane podczas uruchamiania wywołania. Jeśli wartości domyślne stosu nie są ustawione, usługi komunikacyjne powracają do domyślnych ustawień systemu operacyjnego.

```swift
// get first microphone
var firstMicrophone = self.deviceManager!.cameras!.first
// [Synchronous] set microphone
deviceManager.setMicrophone(microphoneDevice: firstMicrophone)
// get first speaker
var firstSpeaker = self.deviceManager!.speakers!
// [Synchronous] set speaker
deviceManager.setSpeaker(speakerDevice: firstSpeaker)
```

### <a name="local-camera-preview"></a>Podgląd lokalnego aparatu fotograficznego

Możesz użyć, `Renderer` Aby rozpocząć renderowanie strumienia z aparatu lokalnego. Ten strumień nie będzie wysyłany do innych uczestników; jest to lokalna wersja zapoznawcza. Jest to akcja asynchroniczna.

```swift

let camera: VideoDeviceInfo = self.deviceManager!.getCameraList()![0]
let localVideoStream: LocalVideoStream = LocalVideoStream(camera: camera)
let renderer: Renderer = Renderer(localVideoStream: localVideoStream)
self.view = try renderer!.createView()

```

### <a name="local-camera-preview-properties"></a>Właściwości podglądu lokalnego aparatu fotograficznego

Moduł renderowania ma zestaw właściwości i metod, które umożliwiają kontrolowanie renderowania:

```swift

// Constructor can take in LocalVideoStream or RemoteVideoStream
let localRenderer = Renderer(localVideoStream:localVideoStream)
let remoteRenderer = Renderer(remoteVideoStream:remoteVideoStream)

// [StreamSize] size of the rendering view
localRenderer.size

// [RendererDelegate] an object you provide to receive events from this Renderer instance
localRenderer.delegate

// [Synchronous] create view
try! localRenderer.createView()

// [Synchronous] create view with rendering options
try! localRenderer.createView(with: RenderingOptions(scalingMode: ScalingMode.fit))

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
public func onCallStateChanged(_ call: Call!,
                               args: PropertyChangedEventArgs!)
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
public func onLocalVideoStreamsChanged(_ call: Call!,
                                       args: LocalVideoStreamsUpdatedEventArgs!)
{
    print(args.addedStreams.count)
    print(args.removedStreams.count)
}
// to unsubscribe
self.call.delegate = nil
```
