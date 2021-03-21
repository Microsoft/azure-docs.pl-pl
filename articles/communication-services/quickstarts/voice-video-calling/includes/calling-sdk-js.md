---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: dee692dc6c82ae91272b39093398eba6ad908c1c
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104612028"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- Token dostępu użytkownika umożliwiający włączenie klienta wywołującego. Aby uzyskać więcej informacji, zobacz [Tworzenie tokenów dostępu i zarządzanie nimi](../../access-tokens.md).
- Opcjonalnie: Ukończ przewodnik szybkiego startu, aby [dodać połączenie głosowe do aplikacji](../getting-started-with-calling.md).

## <a name="install-the-client-library"></a>Zainstaluj bibliotekę kliencką

> [!NOTE]
> Ten dokument używa wersji 1.0.0-beta. 6 z wywołującej biblioteki klienta.

Użyj `npm install` polecenia, aby zainstalować usługi komunikacyjne Azure, wywołując i Common Client librarys for JavaScript.
Ten dokument zawiera odwołania do typów w wersji 1.0.0-beta. 5 biblioteki wywołującej.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta wywołującego usługi Azure Communications Services:

| Nazwa                             | Opis                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Główny punkt wejścia do biblioteki wywołującej klienta.                                                                       |
| `CallAgent`                        | Służy do uruchamiania wywołań i zarządzania nimi.                                                                                            |
| `DeviceManager`                    | Używany do zarządzania urządzeniami multimedialnymi.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementuje `CommunicationTokenCredential` interfejs, który jest używany do tworzenia wystąpienia `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Zainicjuj wystąpienie CallClient, Utwórz wystąpienie CallAgent i uzyskaj dostęp do elementu devicemanager

Utwórz nowe `CallClient` wystąpienie. Można ją skonfigurować z opcjami niestandardowymi, takimi jak wystąpienie rejestratora.

Jeśli masz `CallClient` wystąpienie, możesz utworzyć `CallAgent` wystąpienie przez wywołanie `createCallAgent` metody w `CallClient` wystąpieniu. Asynchronicznie zwraca `CallAgent` obiekt wystąpienia.

`createCallAgent`Metoda używa `CommunicationTokenCredential` jako argumentu. Akceptuje [token dostępu użytkownika](../../access-tokens.md).

Po utworzeniu `callAgent` wystąpienia można `getDeviceManager` uzyskać dostęp do metody na tym `CallClient` wystąpieniu `deviceManager` .

```js
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Umieść wywołanie

Aby utworzyć i uruchomić wywołanie, użyj jednego z interfejsów API w systemie `callAgent` i podaj użytkownika, który został utworzony za pomocą biblioteki klienta tożsamości usług komunikacyjnych.

Tworzenie i uruchamianie wywołań jest synchroniczne. Wystąpienie wywołania pozwala subskrybować zdarzenia.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Umieszczenie 1: n wywołania do użytkownika lub sieci PSTN

Aby wywołać innego użytkownika usług komunikacyjnych, użyj `startCall` metody z `callAgent` i przekaż odbiorcę `CommunicationUserIdentifier` , który został [utworzony za pomocą biblioteki administracyjnej usług komunikacyjnych](https://docs.microsoft.com/azure/communication-services/quickstarts/access-tokens).

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Aby nawiązać połączenie z publiczną siecią telefoniczną (PSTN), użyj `startCall` metody z `callAgent` i przekaż odbiorcę `PhoneNumberIdentifier` . Zasób usług komunikacyjnych musi być skonfigurowany tak, aby zezwalał na nawiązywanie połączeń PSTN.

W przypadku wywołania numeru w sieci PSTN Określ alternatywny identyfikator obiektu wywołującego. Alternatywny identyfikator obiektu wywołującego to numer telefonu (oparty na standardzie E. 164), który identyfikuje obiekt wywołujący w wywołaniu PSTN. Jest to numer telefonu, który odbiorca wywołania widzi dla wywołania przychodzącego.

> [!NOTE]
> Wywołanie PSTN jest obecnie w prywatnej wersji zapoznawczej. W celu uzyskania dostępu należy [zastosować do programu wczesnego](https://aka.ms/ACS-EarlyAdopter)zastosowania.

W przypadku wywołania 1:1 należy użyć następującego kodu:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

Dla wywołania 1: n należy użyć następującego kodu:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Umieszczanie wywołania 1:1 z kamerą wideo

> [!IMPORTANT]
> Nie może istnieć więcej niż jeden wychodzący lokalny strumień wideo.

Aby umieścić połączenie wideo, należy określić aparaty przy użyciu `getCameras()` metody w `deviceManager` .

Po wybraniu aparatu należy używać go do konstruowania `LocalVideoStream` wystąpienia. Przekaż go w ramach `videoOptions` elementu w `localVideoStream` tablicy do `startCall` metody.

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
videoDeviceInfo = cameras[0];
localVideoStream = new LocalVideoStream(videoDeviceInfo);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Gdy połączenie zostanie nawiązane, automatycznie rozpocznie się wysyłanie strumienia wideo z wybranego aparatu do innego uczestnika. Dotyczy to również `Call.Accept()` opcji wideo i `CallAgent.join()` opcji wideo.

### <a name="join-a-group-call"></a>Dołącz do wywołania grupy

Aby rozpocząć nowe wywołanie grupy lub dołączyć do trwającego wywołania grupy, użyj `join` metody i przekaż obiekt z `groupId` właściwością. `groupId`Wartość musi być identyfikatorem GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Dołącz do spotkania zespołów

Aby przyłączyć się do spotkania zespołów, użyj `join` metody i przekaż link do spotkania lub współrzędne.

Dołącz przy użyciu linku spotkania:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Przyłączanie za pomocą współrzędnych spotkania:

```js
const locator = {
    threadId: <thread id>,
    organizerId: <organizer id>,
    tenantId: <tenant id>,
    messageId: <message id>
}
const call = callAgent.join(locator);
```

## <a name="receive-an-incoming-call"></a>Odbieranie połączenia przychodzącego

`callAgent`Wystąpienie emituje zdarzenie, `incomingCall` gdy zarejestrowana tożsamość odbiera wywołanie przychodzące. Aby nasłuchiwać tego zdarzenia, zasubskrybuj przy użyciu jednej z następujących opcji:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    //Get information about caller
    var callerInfo = incomingCall.callerInfo

    //Accept the call
    var call = await incomingCall.accept();

    //Reject the call
    incomingCall.reject();
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

`incomingCall`Zdarzenie zawiera `incomingCall` wystąpienie, które można zaakceptować lub odrzucić.

## <a name="manage-calls"></a>Zarządzaj wywołaniami

Podczas wywołania można uzyskać dostęp do właściwości wywołań i zarządzać ustawieniami wideo i audio.

### <a name="check-call-properties"></a>Sprawdź właściwości wywołania

Pobierz unikatowy identyfikator (ciąg) dla wywołania:

   ```js
    const callId: string = call.id;
   ```

Dowiedz się więcej o innych uczestnikach wywołania, sprawdzając `remoteParticipant` kolekcję:

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Zidentyfikuj obiekt wywołujący wywołanie przychodzące:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` jest jednym z `CommunicationIdentifier` typów.

Pobierz stan wywołania:

   ```js
   const callState = call.state;
   ```

   Zwraca ciąg reprezentujący bieżący stan wywołania:

  - `None`: Początkowy stan wywołania.
  - `Incoming`: Wskazuje, że wywołanie jest przychodzące. Musi zostać zaakceptowany lub odrzucony.
  - `Connecting`: Początkowy stan przejścia, gdy wywołanie jest złożone lub zaakceptowane.
  - `Ringing`: W przypadku wywołania wychodzącego wskazuje, że wywołanie jest sygnalizowane dla uczestników zdalnych. Jest on `Incoming` po stronie.
  - `EarlyMedia`: Wskazuje stan, w którym jest odtwarzany anons przed połączeniem połączenia.
  - `Connected`: Wskazuje, że wywołanie jest połączone.
  - `LocalHold`: Wskazuje, że wywołanie jest wstrzymane przez uczestnika lokalnego. Nie przepływają żadne multimedia między lokalnym punktem końcowym i uczestnikami zdalnymi.
  - `RemoteHold`: Wskazuje, że wywołanie zostało wstrzymane przez uczestnika zdalnego. Nie przepływają żadne multimedia między lokalnym punktem końcowym i uczestnikami zdalnymi.
  - `Disconnecting`: Stan przejścia, zanim połączenie przejdzie do `Disconnected` stanu.
  - `Disconnected`: Stan końcowy wywołania. Po utracie połączenia sieciowego stan zmieni się na `Disconnected` po dwie minuty.

Dowiedz się, dlaczego wywołanie zostało zakończone przez sprawdzenie `callEndReason` Właściwości:

   ```js
   const callEndReason = call.callEndReason;
   // callEndReason.code (number) code associated with the reason
   // callEndReason.subCode (number) subCode associated with the reason
   ```

Dowiedz się, czy bieżące wywołanie jest przychodzące lub wychodzące przez sprawdzenie `direction` właściwości. Zwraca wartość `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Sprawdź, czy bieżący mikrofon jest wyciszony. Zwraca wartość `Boolean` .

   ```js
   const muted = call.isMicrophoneMuted;
   ```

Sprawdź, czy strumień udostępniania ekranu jest wysyłany z danego punktu końcowego przez sprawdzenie `isScreenSharingOn` właściwości. Zwraca wartość `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Sprawdź, czy aktywne strumienie wideo są sprawdzane w `localVideoStreams` kolekcji. Zwraca `LocalVideoStream` obiekty.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```

### <a name="check-a-callended-event"></a>Sprawdź zdarzenie callEnded

`call`Wystąpienie emituje `callEnded` zdarzenie po zakończeniu wywołania. Aby nasłuchiwać tego zdarzenia, zasubskrybuj przy użyciu następującego kodu:

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

Aby uruchomić wideo, musisz określić kamer przy użyciu `getCameras` metody dla `deviceManager` obiektu. Następnie utwórz nowe wystąpienie `LocalVideoStream` przez przekazanie żądanego aparatu do `startVideo` metody jako argument:

```js
const localVideoStream = new LocalVideoStream(videoDeviceInfo);
await call.startVideo(localVideoStream);
```

Po pomyślnym rozpoczęciu wysyłania wideo `LocalVideoStream` wystąpienie jest dodawane do `localVideoStreams` kolekcji w wystąpieniu wywołania.

```js
call.localVideoStreams[0] === localVideoStream;
```

Aby zatrzymać lokalne wideo, należy przekazać `localVideoStream` wystąpienie dostępne w `localVideoStreams` kolekcji:

```js
await call.stopVideo(localVideoStream);
```

Można przełączyć się na inne urządzenie aparatu fotograficznego, podczas gdy film wideo jest wysyłany przez wywołanie `switchSource` na `localVideoStream` wystąpienie:

```js
const cameras = await callClient.getDeviceManager().getCameras();
localVideoStream.switchSource(cameras[1]);
```

## <a name="manage-remote-participants"></a>Zarządzanie uczestnikami zdalnymi

Wszyscy uczestnicy zdalni są reprezentowani przez program `remoteParticipant` i są dostępni za pomocą `remoteParticipants` kolekcji w wystąpieniu wywołania.

### <a name="list-the-participants-in-a-call"></a>Wyświetl listę uczestników wywołania

`remoteParticipants`Kolekcja zwraca listę zdalnych uczestników wywołania:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Dostęp do właściwości zdalnego uczestnika

Uczestnicy zdalni mają zestaw skojarzonych właściwości i Kolekcje:

- `CommunicationIdentifier`: Pobierz identyfikator uczestnika zdalnego. Tożsamość jest jednym z `CommunicationIdentifier` typów:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Może to być jeden z następujących `CommunicationIdentifier` typów:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: Obiekt reprezentujący użytkownika ACS.
  - `{ phoneNumber: '<E.164>' }`: Obiekt reprezentujący numer telefonu w formacie E. 164.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: Obiekt reprezentujący użytkownika zespołów.

- `state`: Pobieranie stanu uczestnika zdalnego.

  ```js
  const state = remoteParticipant.state;
  ```

  Stanem może być:

  - `Idle`: Stan początkowy.
  - `Connecting`: Stan przejścia, gdy uczestnik nawiązuje połączenie z wywołaniem.
  - `Ringing`: Trwa dzwonienie do uczestnika.
  - `Connected`: Uczestnik jest połączony z wywołaniem.
  - `Hold`: Uczestnik jest wstrzymany.
  - `EarlyMedia`: Anons, który jest odtwarzany przed nawiązaniem połączenia przez uczestnika z wywołaniem.
  - `Disconnected`: Stan końcowy. Uczestnik jest odłączony od wywołania. Jeśli uczestnik zdalny utraci połączenie sieciowe, jego stan zmieni się na `Disconnected` po dwóch minutach.

- `callEndReason`: Aby dowiedzieć się, dlaczego uczestnik pozostawił wywołanie, sprawdź `callEndReason` Właściwość:

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  // callEndReason.code (number) code associated with the reason
  // callEndReason.subCode (number) subCode associated with the reason
  ```

- `isMuted` stan: Aby dowiedzieć się, czy uczestnik zdalny został wyciszony, sprawdź `isMuted` Właściwość. Zwraca wartość `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` stan: Aby dowiedzieć się, czy uczestnik zdalny mówi, sprawdź `isSpeaking` Właściwość. Zwraca wartość `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Aby sprawdzić wszystkie strumienie wideo wysyłane przez danego uczestnika w ramach tego wywołania, sprawdź `videoStreams` kolekcję. Zawiera `RemoteVideoStream` obiekty.

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```

### <a name="add-a-participant-to-a-call"></a>Dodawanie uczestnika do wywołania

Aby dodać uczestnika (użytkownika lub numer telefonu) do wywołania, możesz użyć `addParticipant` . Podaj jeden z `Identifier` typów. Zwraca `remoteParticipant` wystąpienie.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Usuwanie uczestnika z wywołania

Aby usunąć uczestnika (użytkownika lub numer telefonu) z wywołania, można wywołać `removeParticipant` . Musisz przekazać jeden z `Identifier` typów. Jest to rozwiązanie asynchroniczne po usunięciu uczestnika z wywołania. Uczestnik zostanie również usunięty z `remoteParticipants` kolekcji.

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
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Aby renderować `RemoteVideoStream` , musisz subskrybować `isAvailableChanged` wydarzenie. Jeśli `isAvailable` Właściwość zmieni się na `true` , uczestnik zdalny wysyła strumień. Po wykonaniu tej czynności Utwórz nowe wystąpienie programu `Renderer` , a następnie utwórz nowe `RendererView` wystąpienie przy użyciu metody asynchronicznej `createView` .  Następnie można dołączyć `view.target` do dowolnego elementu interfejsu użytkownika.

Po zmianie dostępności strumienia zdalnego można zniszczyć `Renderer` , zniszczyć określone `RendererView` wystąpienie lub zachować wszystko. Moduły renderowania dołączone do niedostępnego strumienia spowodują powstanie pustej ramki wideo.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let renderer: Renderer = new Renderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await renderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            renderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Właściwości zdalnego strumienia wideo

Zdalne strumienie wideo mają następujące właściwości:

- `id`: Identyfikator zdalnego strumienia wideo.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `Stream.size`: Wysokość i szerokość zdalnego strumienia wideo.

  ```js
  const size: {width: number; height: number} = remoteVideoStream.size;
  ```

- `mediaStreamType`: Może być `Video` lub `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: Czy zdalny punkt końcowy uczestnika aktywnie wysyła strumień.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="renderer-methods-and-properties"></a>Metody i właściwości modułu renderowania

Utwórz `rendererView` wystąpienie, które może być dołączone w interfejsie użytkownika aplikacji, aby renderować zdalny strumień wideo:

  ```js
  renderer.createView()
  ```

Usuń z `renderer` i wszystkie skojarzone `rendererView` wystąpienia:

  ```js
  renderer.dispose()
  ```

### <a name="rendererview-methods-and-properties"></a>RendererView — metody i właściwości

Podczas tworzenia można `rendererView` określić `scalingMode` `isMirrored` właściwości i. `scalingMode` może być `Stretch` , `Crop` lub `Fit` . Jeśli `isMirrored` jest określony, wyrenderowany strumień zostanie przerzucony w pionie.

```js
const rendererView: RendererView = renderer.createView({ scalingMode, isMirrored });
```

Każde `RendererView` wystąpienie ma `target` Właściwość reprezentującą powierzchnię renderowania. Dołącz tę właściwość w interfejsie użytkownika aplikacji:

```js
document.body.appendChild(rendererView.target);
```

Możesz zaktualizować `scalingMode` przez wywołanie `updateScalingMode` metody:

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Zarządzanie urządzeniami

W programie `deviceManager` można określić urządzenia lokalne, które mogą przesyłać strumienie audio i wideo w wywołaniu. Ułatwia także zażądanie uprawnienia dostępu do mikrofonu i aparatu innego użytkownika przy użyciu interfejsu API natywnej przeglądarki.

Możesz uzyskać dostęp, `deviceManager` wywołując `callClient.getDeviceManager()` metodę:

> [!IMPORTANT]
> `callAgent`Aby można było uzyskać dostęp, musisz mieć obiekt `deviceManager` .

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Pobierz urządzenia lokalne

Aby uzyskać dostęp do urządzeń lokalnych, można użyć metod wyliczania w programie `deviceManager` .

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Ustawianie domyślnego mikrofonu i głośników

W programie `deviceManager` można ustawić urządzenie domyślne, które będzie używane do uruchamiania wywołania. Jeśli wartości domyślne klienta nie są ustawione, usługi komunikacyjne programu korzystają z domyślnych ustawień systemu operacyjnego.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(AudioDeviceInfo);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(AudioDeviceInfo);
```

### <a name="local-camera-preview"></a>Podgląd lokalnego aparatu fotograficznego

Możesz użyć `deviceManager` i, `Renderer` Aby rozpocząć renderowanie strumieni z aparatu lokalnego. Ten strumień nie zostanie wysłany do innych uczestników; jest to lokalna wersja zapoznawcza.

```js
const cameras = await deviceManager.getCameras();
const localVideoDevice = cameras[0];
const localCameraStream = new LocalVideoStream(localVideoDevice);
const renderer = new Renderer(localCameraStream);
const view = await renderer.createView();
document.body.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Zażądaj uprawnień do aparatu i mikrofonu

Monituj użytkownika o przyznanie uprawnień do aparatu i mikrofonu:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

To rozwiązanie jest rozpoznawane przy użyciu obiektu, który wskazuje, czy `audio` i `video` zostały przyznane uprawnienia:

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Wywołania rekordu

[!INCLUDE [Private Preview Notice](../../../includes/private-preview-include-section.md)]

Nagrywanie wywołań jest rozszerzoną funkcją podstawowego `Call` interfejsu API. Najpierw musisz uzyskać obiekt interfejsu API funkcji rejestrowania:

```js
const callRecordingApi = call.api(Features.Recording);
```

Następnie, aby sprawdzić, czy wywołanie jest rejestrowane, zbadaj `isRecordingActive` Właściwość `callRecordingApi` . Zwraca wartość `Boolean` .

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

## <a name="transfer-calls"></a>Wywołania transferu

Transfer wywołań jest rozszerzoną funkcją podstawowego `Call` interfejsu API. Najpierw musisz uzyskać obiekt interfejsu API funkcji transferu:

```js
const callTransferApi = call.api(Features.Transfer);
```

Transfery wywołań obejmują trzy strony:

- *Transfer*: osoba, która inicjuje żądanie przeniesienia.
- *Przejmujący*: osoba, która jest przekazywana.
- *Docelowy transfer*: osoba, która jest przekazywana do.

Transfery wykonaj następujące kroki:

1. Istnieje już połączenie połączone między *transferem* a *cesjonariuszem*. *Transfer* decyduje o przeniesieniu wywołania z elementu *przejmującego* do celu *przeniesienia*.
1. *Transfer* wywołuje `transfer` interfejs API.
1. *Przejmujący* decyduje o `accept` tym, czy `reject` żądanie transferu do *elementu docelowego transferu* można wykonać przy użyciu `transferRequested` zdarzenia.
1. *Docelowy transfer* odbiera wywołanie przychodzące tylko wtedy, gdy *przejmujący* akceptuje żądanie transferu.

Aby przesłać bieżące wywołanie, można użyć `transfer` interfejsu API. `transfer` przyjmuje opcjonalny `transferCallOptions` , który umożliwia ustawienie `disableForwardingAndUnanswered` flagi:

- `disableForwardingAndUnanswered = false`: Jeśli *docelowy transfer* nie odpowiada na wywołanie transferu, transfer postępuje zgodnie z ustawieniami przekazywania *docelowego transferu* i nieodpowiedzią.
- `disableForwardingAndUnanswered = true`: Jeśli *docelowy transfer* nie odpowiada na wywołanie transferu, zostanie zakończona próba transferu.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

`transfer`Interfejs API umożliwia subskrybowanie `transferStateChanged` `transferRequested` zdarzeń i. `transferRequested`Zdarzenie pochodzi z `call` wystąpienia, `transferStateChanged` zdarzenie i transfer `state` i `error` pochodzą z `transfer` wystąpienia.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

*Przejmujący* może zaakceptować lub odrzucić żądanie transferu zainicjowane przez *transfer* w `transferRequested` zdarzeniu przy użyciu `accept()` lub `reject()` w `transferRequestedEventArgs` . Możesz uzyskać dostęp do `targetParticipant` informacji `accept` i `reject` metod w `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>Informacje o modelach zdarzeń

Sprawdzaj bieżące wartości i Subskrybuj zdarzenia w celu aktualizacji dla przyszłych wartości.

### <a name="properties"></a>Właściwości

```js
// Inspect the current value
console.log(object.property);

// Subscribe to value updates
object.on('propertyChanged', () => {
    // Inspect new value
    console.log(object.property)
});

// Unsubscribe from updates:
object.off('propertyChanged', () => {});



// Example for inspecting a call state
console.log(call.state);
call.on('stateChanged', () => {
    console.log(call.state);
});
call.off('stateChanged', () => {});
```

### <a name="collections"></a>Kolekcje

```js
// Inspect the current collection
object.collection.forEach(v => {
    console.log(v);
});

// Subscribe to collection updates
object.on('collectionUpdated', e => {
    // Inspect new values added to the collection
    e.added.forEach(v => {
        console.log(v);
    });
    // Inspect values removed from the collection
    e.removed.forEach(v => {
        console.log(v);
    });
});

// Unsubscribe from updates:
object.off('collectionUpdated', () => {});

// Example for subscribing to remote participants and their video streams
call.remoteParticipants.forEach(p => {
    subscribeToRemoteParticipant(p);
})

call.on('remoteParticipantsUpdated', e => {
    e.added.forEach(p => { subscribeToRemoteParticipant(p) })
    e.removed.forEach(p => { unsubscribeFromRemoteParticipant(p) })
});

function subscribeToRemoteParticipant(p) {
    console.log(p.state);
    p.on('stateChanged', () => { console.log(p.state); });
    p.videoStreams.forEach(v => { subscribeToRemoteVideoStream(v) });
    p.on('videoStreamsUpdated', e => { e.added.forEach(v => { subscribeToRemoteVideoStream(v) }) })
}
```
