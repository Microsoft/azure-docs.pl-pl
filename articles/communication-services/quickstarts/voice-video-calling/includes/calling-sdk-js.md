---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: 7d391998e7f20cff0f77f6aab7938bc375f75c9e
ms.sourcegitcommit: f377ba5ebd431e8c3579445ff588da664b00b36b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99616427"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- A, `User Access Token` Aby włączyć klienta wywołania. Aby uzyskać więcej informacji na temat [uzyskiwania `User Access Token` ](../../access-tokens.md)
- Opcjonalnie: Ukończ Przewodnik Szybki Start dotyczący [dodawania wywołania do aplikacji](../getting-started-with-calling.md)

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

Użyj `npm install` polecenia, aby zainstalować usługi komunikacyjne Azure, wywołując i Common Client librarys for JavaScript.

```console
npm install @azure/communication-calling --save
```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta wywołującego usługi Azure Communications Services:

| Nazwa                             | Opis                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| CallClient                       | CallClient jest głównym punktem wejścia do biblioteki wywołującej klienta.                                                                       |
| CallAgent                        | CallAgent jest używany do uruchamiania wywołań i zarządzania nimi.                                                                                            |
| Devicemanager                    | Devicemanager jest używany do zarządzania urządzeniami multimedialnymi                                                                                           |
| AzureCommunicationTokenCredential | Klasa AzureCommunicationTokenCredential implementuje interfejs CommunicationTokenCredential, który jest używany do tworzenia wystąpienia CallAgent. |


## <a name="initialize-the-callclient-create-callagent-and-access-devicemanager"></a>Inicjowanie programu CallClient, Create CallAgent i accessmanager

Utwórz wystąpienie nowego `CallClient` wystąpienia. Można ją skonfigurować z opcjami niestandardowymi, takimi jak wystąpienie rejestratora.
Po utworzeniu wystąpienia `CallClient` można utworzyć `CallAgent` wystąpienie przez wywołanie `createCallAgent` metody w `CallClient` wystąpieniu. Asynchronicznie zwraca `CallAgent` obiekt wystąpienia.
`createCallAgent`Metoda przyjmuje `CommunicationTokenCredential` jako argument, który akceptuje [token dostępu użytkownika](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).
Aby uzyskać dostęp do `DeviceManager` wystąpienia callAgent, należy najpierw utworzyć wystąpienie. Następnie można użyć `getDeviceManager` metody z `CallClient` wystąpienia, aby uzyskać obiekt devicemanager.

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-an-outgoing-call"></a>Umieść wywołanie wychodzące

Aby utworzyć i uruchomić wywołanie, należy użyć jednego z interfejsów API w programie CallAgent i udostępnić użytkownikowi, który został utworzony za pomocą biblioteki klienta Administracja usług komunikacyjnych.

Tworzenie i uruchamianie wywołań jest synchroniczne. Wystąpienie wywołania pozwala subskrybować zdarzenia.

## <a name="place-a-call"></a>Umieść wywołanie

### <a name="place-a-11-call-to-a-user-or-pstn"></a>Umieść wywołanie 1:1 dla użytkownika lub sieci PSTN
Aby nawiązać połączenie z innym użytkownikiem usług komunikacyjnych, wywołaj `call` metodę na `callAgent` i przekaż CommunicationUserIdentifier wywoływany:

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.call([userCallee]);
```

Aby umieścić wywołanie do sieci PSTN, wywołaj `call` metodę na `callAgent` i przekaż PhoneNumberIdentifier wywoływany.
Zasób usług komunikacyjnych musi być skonfigurowany tak, aby zezwalał na nawiązywanie połączeń PSTN.
Podczas wywoływania numeru w sieci PSTN należy określić alternatywny identyfikator obiektu wywołującego.
```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.call([pstnCallee], {alternateCallerId});
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Umieszczenie 1: n połączenia z użytkownikami i PSTN
```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.call([userCallee, pstnCallee], {alternateCallerId});
```

### <a name="place-a-11-call-with-video-camera"></a>Umieszczanie wywołania 1:1 z kamerą wideo
> [!WARNING]
> Nie może istnieć więcej niż jeden wychodzący lokalny strumień wideo.
Aby umieścić połączenie wideo, należy wyliczyć aparaty lokalne przy użyciu `getCameraList` interfejsu API urządzenia.
Po wybraniu żądanego aparatu Użyj go do skonstruowania `LocalVideoStream` wystąpienia i przekazania go w ramach `videoOptions` elementu `localVideoStream` tablicy do `call` metody.
Po nawiązaniu połączenia zostanie automatycznie rozpoczęte wysyłanie strumienia wideo z wybranego aparatu do innych uczestników. Dotyczy to również opcji wideo Call. Accept () i opcji wideo CallAgent. Join ().
```js
const deviceManager = await callClient.getDeviceManager();
const videoDeviceInfo = deviceManager.getCameraList()[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.call(['acsUserId'], placeCallOptions);

```

### <a name="join-a-group-call"></a>Dołącz do wywołania grupy
Aby rozpocząć nowe wywołanie grupy lub dołączyć do trwającego wywołania grupy, użyj metody "join" i przekaż obiekt z `groupId` właściwością. Wartość musi być identyfikatorem GUID.
```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```
### <a name="join-a-teams-meeting"></a>Dołącz do spotkania zespołów
Aby przyłączyć się do spotkania zespołów, użyj metody "join" i przekaż link do spotkania lub współrzędnej spotkania
```js
// Join using meeting link
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);

// Join using meeting coordinates
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receiving-an-incoming-call"></a>Odbieranie wywołania przychodzącego

`CallAgent`Wystąpienie emituje zdarzenie, `incomingCall` gdy zalogowana tożsamość odbiera wywołanie przychodzące. Aby nasłuchiwać tego zdarzenia, zasubskrybuj następujący sposób:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //accept the call
    var call = await incomingCall.accept();

    //reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall`Zdarzenie będzie zawierać wystąpienie, `IncomingCall` na którym można zaakceptować lub odrzucić wywołanie.


## <a name="call-management"></a>Zarządzanie połączeniami

Można uzyskać dostęp do właściwości wywołania i wykonać różne operacje w trakcie wywołania zarządzania ustawieniami związanymi z wideo i dźwiękiem.

### <a name="call-properties"></a>Właściwości wywołania
* Pobierz unikatowy identyfikator (ciąg) dla tego wywołania.
```js

const callId: string = call.id;

```

* Aby dowiedzieć się więcej o innych uczestnikach wywołania, zbadaj `remoteParticipant` kolekcję na tym `call` wystąpieniu. Tablica zawiera `RemoteParticipant` obiekty list
```js
const remoteParticipants = call.remoteParticipants;
```

* Tożsamość obiektu wywołującego, jeśli wywołanie jest przychodzące. Tożsamość jest jednym z `CommunicationIdentifier` typów
```js

const callerIdentity = call.callerInfo.identity;

```

* Pobierz stan wywołania.
```js

const callState = call.state;

```
Zwraca ciąg reprezentujący bieżący stan wywołania:
* "Brak" — stan wywołania początkowego
* "Przychodzące" — wskazuje, że wywołanie jest przychodzące, musi zostać zaakceptowane lub odrzucone
* "Łączenie" — stan przejścia początkowego po zainicjowaniu lub zaakceptowaniu wywołania
* "Dzwonienie" — dla wywołania wychodzącego — wskazuje, że wywołanie jest sygnalizowane dla uczestników zdalnych, po stronie "przychodzące"
* "EarlyMedia" — wskazuje stan, w którym jest odtwarzany anons przed połączeniem połączenia
* "Połączone" — połączenie jest połączone
* "Hold" — wywołanie jest wstrzymane, żaden nośnik nie przepływa między lokalnym punktem końcowym i uczestnikami zdalnymi
* "Rozłączanie"-stan przejścia przed przejściem do stanu "Rozłączono"
* "Rozłączono" — stan końcowy wywołania
  * Po utracie połączenia sieciowego stan przechodzi do "Rozłączono" po około 2 minutach.

* Aby zobaczyć dlaczego dane wywołanie zostało zakończone, sprawdź `callEndReason` Właściwość.
```js

const callEndReason = call.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```

* Aby dowiedzieć się, czy bieżące wywołanie jest wywołaniem przychodzącym lub wychodzącym, zbadaj `direction` Właściwość, która zwraca wartość `CallDirection` .
```js
const isIncoming = call.direction == 'Incoming';
const isOutgoing = call.direction == 'Outgoing';
```

*  Aby sprawdzić, czy bieżący mikrofon jest wyciszony, sprawdź `muted` , czy właściwość zwraca `Boolean` .
```js

const muted = call.isMicrophoneMuted;

```

* Aby sprawdzić, czy strumień udostępniania ekranu jest wysyłany z danego punktu końcowego, sprawdź `isScreenSharingOn` Właściwość, która zwraca wartość `Boolean` .
```js

const isScreenSharingOn = call.isScreenSharingOn;

```

* Aby sprawdzić aktywne strumienie wideo, sprawdź `localVideoStreams` , czy kolekcja zawiera `LocalVideoStream` obiekty
```js

const localVideoStreams = call.localVideoStreams;

```

### <a name="call-ended-event"></a>Zdarzenie zakończenia wywołania

`Call`Wystąpienie emituje `callEnded` zdarzenie po zakończeniu wywołania. Aby nasłuchiwać tego zdarzenia, zasubskrybuj w następujący sposób:

```js
const callEndHander = async (args: { callEndReason: CallEndReason }) => {
    console.log(args.callEndReason)
};

call.on('callEnded', callEndHander);
```

### <a name="mute-and-unmute"></a>Wycisz i Wycisz

Aby wyciszyć lub wyłączyć lokalny punkt końcowy, można użyć `mute` i `unmute` asynchronicznych interfejsów API:

```js

//mute local device 
await call.mute();

//unmute local device 
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Uruchamianie i zatrzymywanie wysyłania lokalnego wideo


Aby uruchomić wideo, należy wyliczyć aparaty fotograficzne przy użyciu `getCameraList` metody dla `deviceManager` obiektu. Następnie utwórz nowe wystąpienie `LocalVideoStream` przekazanie żądanego aparatu do `startVideo` metody jako argument:


```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);

```

Po pomyślnym rozpoczęciu wysyłania wideo `LocalVideoStream` wystąpienie zostanie dodane do `localVideoStreams` kolekcji w wystąpieniu wywołania.

```js

call.localVideoStreams[0] === localVideoStream;

```

Aby zatrzymać lokalne wideo, Przekaż `localVideoStream` wystąpienie dostępne w `localVideoStreams` kolekcji:

```js

await call.stopVideo(localVideoStream);

```

Możesz przełączyć się do innego urządzenia aparatu fotograficznego, gdy wideo jest wysyłane przez wywołanie `switchSource` na `localVideoStream` wystąpienie:

```js
const source callClient.getDeviceManager().getCameraList()[1];
localVideoStream.switchSource(source);

```

## <a name="remote-participants-management"></a>Zdalne zarządzanie uczestnikami

Wszyscy uczestnicy zdalni są reprezentowani według `RemoteParticipant` typu i dostępne za pomocą `remoteParticipants` kolekcji w wystąpieniu wywołania.

### <a name="list-participants-in-a-call"></a>Wyświetlanie listy uczestników wywołania
`remoteParticipants`Kolekcja zwraca listę zdalnych uczestników w danym wywołaniu:

```js

call.remoteParticipants; // [remoteParticipant, remoteParticipant....]

```

### <a name="remote-participant-properties"></a>Właściwości uczestnika zdalnego
Uczestnik zdalny ma skojarzoną z nim zestaw właściwości i kolekcji
#### <a name="communicationidentifier"></a>CommunicationIdentifier
Pobierz identyfikator dla tego uczestnika zdalnego.
```js
const identifier = remoteParticipant.identifier;
```
Może to być jeden z typów "CommunicationIdentifier":
  * {communicationUserId: "<ACS_USER_ID" >}-obiekt reprezentujący użytkownika ACS
  * {unphone: "<E. 164>"}-obiekt reprezentujący numer telefonu w formacie E. 164
  * {microsoftTeamsUserId: "<TEAMS_USER_ID>", IsAnonymous?: Boolean; chmura?: "Public" | "dod" | "gcch"} — obiekt reprezentujący zespołów

#### <a name="state"></a>Stan
Pobierz stan tego uczestnika zdalnego.
```js

const state = remoteParticipant.state;
```
Stan może być jednym z
* "Bezczynne" — stan początkowy
* "Łączenie" — stan przejścia, gdy uczestnik nawiązuje połączenie z wywołaniem
* "Połączone" — uczestnik jest połączony z wywołaniem
* "Hold" — uczestnik jest wstrzymany
* "EarlyMedia" — anons jest odtwarzany przed połączeniem uczestnika z wywołaniem
* "Rozłączono" — stan końcowy — uczestnik jest odłączony od wywołania
  * Jeśli zdalny uczestnik utraci połączenie sieciowe, stan uczestnika zdalnego przechodzi do "odłączony" po około 2 minutach.

#### <a name="call-end-reason"></a>Przyczyna zakończenia wywołania
Aby dowiedzieć się, dlaczego uczestnik pozostawił wywołanie, zbadaj `callEndReason` Właściwość:
```js
const callEndReason = remoteParticipant.callEndReason;
// callEndReason.code (number) code associated with the reason
// callEndReason.subCode (number) subCode associated with the reason
```
#### <a name="is-muted"></a>Jest wyciszony
Aby sprawdzić, czy ten Uczestnik zdalny jest wyciszony, zbadaj `isMuted` Właściwość, zwraca `Boolean`
```js
const isMuted = remoteParticipant.isMuted;
```
#### <a name="is-speaking"></a>Mówi
Aby sprawdzić, czy ten Uczestnik zdalny mówi, czy nie, sprawdź, czy `isSpeaking` Właściwość zwraca `Boolean`
```js
const isSpeaking = remoteParticipant.isSpeaking;
```

#### <a name="video-streams"></a>Strumienie wideo
Aby sprawdzić wszystkie strumienie wideo wysyłane przez danego uczestnika w ramach tego wywołania, sprawdź `videoStreams` kolekcję, zawiera `RemoteVideoStream` obiekty
```js

const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]

```


### <a name="add-a-participant-to-a-call"></a>Dodawanie uczestnika do wywołania

Aby dodać uczestnika do wywołania (użytkownika lub numeru telefonu), można wywołać `addParticipant` .
Podaj jeden z typów identyfikatora.
Spowoduje to synchroniczne zwrócenie wystąpienia uczestnika zdalnego.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-participant-from-a-call"></a>Usuń uczestnika z wywołania

Aby usunąć uczestnika z wywołania (użytkownika lub numeru telefonu), można wywołać `removeParticipant` .
Musisz przekazać jeden z typów identyfikatora, który zostanie rozwiązany asynchronicznie po usunięciu uczestnika z wywołania.
Uczestnik zostanie również usunięty z `remoteParticipants` kolekcji.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Renderuj strumienie wideo zdalnego uczestnika

Aby wyświetlić listę strumieni wideo i strumieni udostępniania ekranu dla uczestników zdalnych, należy sprawdzić `videoStreams` Kolekcje:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.type;
```
 
Aby renderować a `RemoteVideoStream` , musisz subskrybować `isAvailableChanged` wydarzenie.
Jeśli `isAvailable` Właściwość zmieni się na `true` , uczestnik zdalny wysyła strumień.
Gdy tak się stanie, Utwórz nowe wystąpienie `Renderer` , a następnie utwórz nowe `RendererView` wystąpienie przy użyciu metody asynchronicznej `createView` .  Następnie można dołączyć `view.target` do dowolnego elementu interfejsu użytkownika.
Zawsze, gdy jest dostępna zmiana strumienia zdalnego, można wybrać opcję zniszczenia całego modułu renderowania, określonych `RendererView` lub zachowywania, ale spowoduje to wyświetlenie pustej ramki wideo.

```js
let renderer: Renderer = new Renderer(remoteParticipantStream);
const displayVideo = () => {
    const view = await renderer.createView();
    htmlElement.appendChild(view.target);
}
remoteParticipantStream.on('availabilityChanged', async () => {
    if (remoteParticipantStream.isAvailable) {
        displayVideo();
    } else {
        renderer.dispose();
    }
});
if (remoteParticipantStream.isAvailable) {
    displayVideo();
}
```

### <a name="remote-video-stream-properties"></a>Właściwości zdalnego strumienia wideo
Zdalne strumienie wideo mają następujące właściwości:

* `Id` -Identyfikator zdalnego strumienia wideo
```js
const id: number = remoteVideoStream.id;
```

* `StreamSize` -size (szerokość/wysokość) zdalnego strumienia wideo
```js
const size: {width: number; height: number} = remoteVideoStream.size;
```

* `MediaStreamType` — może to być "wideo" lub "ScreenSharing"
```js
const type: MediaStreamType = remoteVideoStream.type;
```
* `isAvailable` -Wskazuje, czy zdalny punkt końcowy uczestnika aktywnie wysyła strumień
```js
const type: boolean = remoteVideoStream.isAvailable;
```

### <a name="renderer-methods-and-properties"></a>Metody i właściwości modułu renderowania

* Utwórz `RendererView` wystąpienie, które może zostać później dołączone w interfejsie użytkownika aplikacji, aby renderować zdalny strumień wideo.
```js
renderer.createView()
```

* Usuń moduł renderowania i wszystkie skojarzone `RendererView` wystąpienia.
```js
renderer.dispose()
```


### <a name="rendererview-methods-and-properties"></a>RendererView — metody i właściwości
Podczas tworzenia `RendererView` można określić `scalingMode` `mirrored` właściwości i.
Tryb skalowania może mieć wartość "Rozciągnij", "Kadruj" lub "Dopasuj", jeśli `Mirrored` jest określony, wyrenderowany strumień zostanie przerzucony w pionie.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, mirrored });
```
Każde określone `RendererView` wystąpienie ma `target` Właściwość reprezentującą powierzchnię renderowania. Ta wartość musi być dołączona do interfejsu użytkownika aplikacji:
```js
document.body.appendChild(rendererView.target);
```

Możesz później zaktualizować tryb skalowania, wywołując `updateScalingMode` metodę.
```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Zarządzanie urządzeniami

`DeviceManager` umożliwia Wyliczenie urządzeń lokalnych, które mogą być używane w wywołaniu przesyłania strumieni audio/wideo. Umożliwia także zażądanie uprawnień od użytkownika w celu uzyskania dostępu do mikrofonu i aparatu przy użyciu natywnego interfejsu API przeglądarki.

Dostęp można uzyskać `deviceManager` za pomocą metody wywołującej `callClient.getDeviceManager()` .
> [!WARNING]
> Obecnie `callAgent` przed uzyskaniem dostępu do elementu devicemanager należy najpierw utworzyć wystąpienie obiektu.

```js

const deviceManager = await callClient.getDeviceManager();

```

### <a name="enumerate-local-devices"></a>Wyliczanie urządzeń lokalnych

Aby uzyskać dostęp do urządzeń lokalnych, można użyć metod wyliczania na Device Manager. Wyliczenie jest akcją synchroniczną.

```js

//  Get a list of available video devices for use.
const localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]

```

### <a name="set-default-microphonespeaker"></a>Ustaw domyślny Mikrofon/głośnik

Menedżer urządzeń umożliwia ustawienie domyślnego urządzenia, które będzie używane podczas uruchamiania wywołania.
Jeśli wartości domyślne klienta nie są ustawione, usługi komunikacyjne powracają do domyślnych ustawień systemu operacyjnego.

```js

// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.getMicrophone();

// Set the microphone device to use.
await deviceManager.setMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.getSpeaker();

// Set the speaker device to use.
await deviceManager.setSpeaker(AudioDeviceInfo);

```

### <a name="local-camera-preview"></a>Podgląd lokalnego aparatu fotograficznego

Możesz użyć `DeviceManager` i, `Renderer` Aby rozpocząć renderowanie strumieni z aparatu lokalnego. Ten strumień nie zostanie wysłany do innych uczestników; jest to lokalna wersja zapoznawcza. Jest to akcja asynchroniczna.

```js
const localVideoDevice = deviceManager.getCameraList()[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-cameramicrophone"></a>Żądaj uprawnień do aparatu/mikrofonu

Monituj użytkownika o przyznanie uprawnień do aparatu/mikrofonu przy użyciu następujących czynności:

```js
const result = await deviceManager.askDevicePermission(audio: true, video: true);
```
Spowoduje to rozwiązanie asynchroniczne z obiektem wskazującym `audio` , czy `video` uprawnienia zostały przyznane:
```js
console.log(result.audio);
console.log(result.video);
```

Bieżący stan uprawnień dla danego typu można sprawdzić, wywołując `getPermissionState` :

```js

const result = deviceManager.getPermissionState('Microphone'); // for microphone permission state
const result = deviceManager.getPermissionState('Camera'); // for camera permission state

console.log(result); // 'Granted' | 'Denied' | 'Prompt' | 'Unknown';

```

## <a name="call-recording-management"></a>Zarządzanie rejestrowaniem wywołań

Nagrywanie wywołań jest rozszerzoną funkcją podstawowego `Call` interfejsu API. Najpierw musisz uzyskać obiekt interfejsu API funkcji rejestrowania:

```js
const callRecordingApi = call.api(Features.Recording);
```

Następnie, aby sprawdzić, czy wywołanie jest rejestrowane, sprawdź `isRecordingActive` Właściwość `callRecordingApi` , która zwraca wartość `Boolean` .

```js
const isResordingActive = callRecordingApi.isRecordingActive;
```

Możesz również subskrybować rejestrowanie zmian:

```js
const isRecordingActiveChangedHandler = () => {
  console.log(callRecordingApi.isRecordingActive);
};

callRecordingApi.on('isRecordingActiveChanged', isRecordingActiveChangedHandler);
               
```

## <a name="call-transfer-management"></a>Zarządzanie transferem wywołań

Transfer wywołań jest rozszerzoną funkcją podstawowego `Call` interfejsu API. Najpierw musisz uzyskać obiekt interfejsu API funkcji transferu:

```js
const callTransferApi = call.api(Features.Transfer);
```

*Transfer wywołań obejmuje trzech stron,* *przejmujących* i *docelowych transferu*. Przepływ transferu działa w następujący sposób:

1. Istnieje już połączenie połączone między usługą *transfer* i *cesjonariuszem*
2. *transfer* decyduje o *przeniesieniu wywołania (*  ->  *docelowy transfer przeniesienia*)
3. Interfejs API wywołania usługi *transfer* `transfer`
4. *cesjonariusz* decyduje o tym, czy żądanie transferu przetransferować `accept` `reject` *cel* za pośrednictwem `transferRequested` zdarzenia.
5. *element docelowy transferu* otrzyma wywołanie przychodzące tylko wtedy, gdy *przejmujący* `accept` żądanie transferu

### <a name="transfer-terminology"></a>Zatransferuj terminologię

- Transfer — ten, który inicjuje żądanie przeniesienia
- Przejmujący — ten, kto jest transferowany przez transfer do elementu docelowego transferu
- Docelowy transfer — ten, który jest elementem docelowym, który jest przesyłany do

Aby przetransferować bieżące wywołanie, można użyć `transfer` synchronicznego interfejsu API. `transfer` przyjmuje wartość opcjonalną `TransferCallOptions` , która umożliwia ustawienie `disableForwardingAndUnanswered` flagi:

- `disableForwardingAndUnanswered` = false — Jeśli *docelowy transfer* nie odpowiada na wywołanie transferu, będzie postępować zgodnie z ustawieniami przekazywania *docelowego transferu* i nieodpowiedziami
- `disableForwardingAndUnanswered` = true — Jeśli *docelowy transfer* nie odpowiada na wywołanie transferu, próba transferu zostanie zakończona

```js
// transfer target can be ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Funkcja transfer pozwala subskrybować `transferStateChanged` `transferRequested` zdarzenia i. `transferRequsted` zdarzenie pochodzi z `call` wystąpienia, `transferStateChanged` zdarzenia i transferu `state` i `error` pochodzi z `transfer` wystąpienia

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if transfer request failed
```

Przejmujący może zaakceptować lub odrzucić żądanie transferu zainicjowane przez transfer w `transferRequested` ramach zdarzenia przez program `accept()` lub `reject()` w `transferRequestedEventArgs` . Możesz uzyskać dostęp do `targetParticipant` informacji `accept` , `reject` metod w `transferRequestedEventArgs` .

```js
// Transferee to accept the transfer request
callTransferApi.on('transferRequested', args => {
  args.accept();
});

// Transferee to reject the transfer request
callTransferApi.on('transferRequested', args => {
  args.reject();
});
```

## <a name="eventing-model"></a>Model zdarzeń

Aby otrzymywać powiadomienia o zmianach wartości, można subskrybować większość właściwości i kolekcji.

### <a name="properties"></a>Właściwości
Aby subskrybować `property changed` zdarzenia:

```js

const eventHandler = () => {
    // check current value of a property, value is not passed to callback
    console.log(object.property);
};
object.on('propertyNameChanged',eventHandler);

// To unsubscribe:

object.off('propertyNameChanged',eventHandler);

```

### <a name="collections"></a>Kolekcje
Aby subskrybować `collection updated` zdarzenia:

```js

const eventHandler = (e) => {
    // check added elements
    console.log(e.added);
    // check removed elements
    console.log(e.removed);
};
object.on('collectionNameUpdated',eventHandler);

// To unsubscribe:

object.off('collectionNameUpdated',eventHandler);

```
