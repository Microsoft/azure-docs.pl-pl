---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 03/10/2021
ms.author: mikben
ms.openlocfilehash: f20099943d3cfa3dd4afc161c26e5582e467ca8d
ms.sourcegitcommit: 272351402a140422205ff50b59f80d3c6758f6f6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/17/2021
ms.locfileid: "107590008"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Wdrożony zasób Communication Services zasobów. [Utwórz zasób Communication Services zasobów.](../../create-communication-resource.md)
- Token dostępu użytkownika w celu włączenia wywołującego klienta. Aby uzyskać więcej informacji, zobacz [Tworzenie tokenów dostępu i zarządzanie nimi.](../../access-tokens.md)
- Opcjonalnie: ukończ przewodnik Szybki start, aby [dodać połączenia głosowe do aplikacji.](../getting-started-with-calling.md)

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

> [!NOTE]
> W tym dokumencie jest używany zestaw SDK sieci Web wywoływania usług ACS.

Użyj polecenia `npm install` , aby zainstalować wywołanie Azure Communication Services i typowe zestawy SDK dla języka JavaScript.

```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save

```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje zestawu AZURE COMMUNICATION SERVICES SDK:

| Nazwa                             | Opis                                                                                                                                 |
| ---------------------------------| ------------------------------------------------------------------------------------------------------------------------------------------- |
| `CallClient`                      | Główny punkt wejścia do wywołującego zestawu SDK.                                                                       |
| `CallAgent`                        | Służy do uruchamiania wywołań i zarządzania nimi.                                                                                            |
| `DeviceManager`                    | Służy do zarządzania urządzeniami multimedialnymi.                                                                                           |
| `AzureCommunicationTokenCredential` | Implementuje `CommunicationTokenCredential` interfejs , który służy do wystąpienia `callAgent` . |

## <a name="initialize-a-callclient-instance-create-a-callagent-instance-and-access-devicemanager"></a>Inicjowanie wystąpienia CallClient, tworzenie wystąpienia CallAgent i uzyskiwanie dostępu do menedżera urządzeń

Utwórz nowe `CallClient` wystąpienie. Można go skonfigurować za pomocą opcji niestandardowych, takich jak wystąpienie rejestratora.

Jeśli masz `CallClient` wystąpienie, możesz utworzyć wystąpienie, wywołując `CallAgent` metodę w `createCallAgent` `CallClient` wystąpieniu. To asynchronicznie zwraca `CallAgent` obiekt wystąpienia.

Metoda `createCallAgent` używa metody jako `CommunicationTokenCredential` argumentu. Akceptuje token dostępu [użytkownika](../../access-tokens.md).

Aby uzyskać dostęp do obiektu , możesz użyć `getDeviceManager` `CallClient` metody w wystąpieniu `deviceManager` .

```js
// Set the logger's log level
setLogLevel('verbose');
// Redirect logger output to wherever desired. By default it logs to console
AzureLogger.log = (...args) => { console.log(...args) };
const userToken = '<user token>';
callClient = new CallClient(options);
const tokenCredential = new AzureCommunicationTokenCredential(userToken);
const callAgent = await callClient.createCallAgent(tokenCredential, {displayName: 'optional ACS user name'});
const deviceManager = await callClient.getDeviceManager()
```

## <a name="place-a-call"></a>Umieść wywołanie

Aby utworzyć i uruchomić wywołanie, użyj jednego z interfejsów API na stronie i podaj użytkownika utworzonego za pomocą zestawu SDK Communication Services `callAgent` tożsamości.

Tworzenie i uruchamianie wywołań jest synchroniczne. Wystąpienie wywołania umożliwia subskrybowanie zdarzeń wywołań.

### <a name="place-a-1n-call-to-a-user-or-pstn"></a>Umieść wywołanie 1:n do użytkownika lub sieci PSTN

Aby wywołać Communication Services użytkownika, użyj metody na i przekaż adresaci, który został utworzony za pomocą `startCall` `callAgent` `CommunicationUserIdentifier` [biblioteki administracyjnej](../../access-tokens.md)Communication Services administracyjnej .

```js
const userCallee = { communicationUserId: '<ACS_USER_ID>' }
const oneToOneCall = callAgent.startCall([userCallee]);
```

Aby wywołać publiczną sieć telefoniczną z przełącznikiem (PSTN), użyj metody na i przekaż adres `startCall` `callAgent` . `PhoneNumberIdentifier` Zasób Communication Services musi być skonfigurowany do zezwalania na połączenia PSTN.

Podczas wywołania numeru PSTN określ alternatywny identyfikator wywołujący. Alternatywny identyfikator rozmówcy to numer telefonu (na podstawie standardu E.164), który identyfikuje wywołującego w wywołaniu PSTN. Jest to numer telefonu, który adresat połączenia widzi dla połączenia przychodzącego.

> [!NOTE]
> Wywołanie pstn jest obecnie w prywatnej wersji zapoznawczej. Aby uzyskać dostęp, [zastosuj do programu wczesnego użytkownika](https://aka.ms/ACS-EarlyAdopter).

W przypadku wywołania 1:1 użyj następującego kodu:

```js
const pstnCalee = { phoneNumber: '<ACS_USER_ID>' }
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const oneToOneCall = callAgent.startCall([pstnCallee], {alternateCallerId});
```

W przypadku wywołania 1:n użyj następującego kodu:

```js
const userCallee = { communicationUserId: <ACS_USER_ID> }
const pstnCallee = { phoneNumber: <PHONE_NUMBER>};
const alternateCallerId = {alternateCallerId: '<Alternate caller Id>'};
const groupCall = callAgent.startCall([userCallee, pstnCallee], {alternateCallerId});

```

### <a name="place-a-11-call-with-video-camera"></a>Umieść wywołanie 1:1 za pomocą kamery wideo

> [!IMPORTANT]
> Obecnie nie może być więcej niż jeden wychodzący lokalny strumień wideo.

Aby umieścić wywołanie wideo, należy wyliczyć lokalne kamery przy użyciu `getCameras()` metody w metodzie `deviceManager` .

Po wybraniu aparatu użyj go do skonstruowania `LocalVideoStream` wystąpienia. Przekaż go `videoOptions` jako element w `localVideoStream` tablicy do metody `startCall` .


```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
localVideoStream = new LocalVideoStream(camera);
const placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};
const call = callAgent.startCall(['acsUserId'], placeCallOptions);

```

Gdy połączenie nawiąże połączenie, automatycznie rozpocznie wysyłanie strumienia wideo z wybranej kamery do innego uczestnika. Dotyczy to również opcji `Call.Accept()` wideo i `CallAgent.join()` wideo.

### <a name="join-a-group-call"></a>Dołączanie do wywołania grupy

> [!NOTE]
> Parametr jest traktowany jako metadane systemu i może być używany przez firmę Microsoft do `groupId` operacji wymaganych do uruchomienia systemu. Nie uwzględniaj w wartości danych `groupId` osobowych. Firma Microsoft nie traktuje tego parametru jako danych osobowych, a jego zawartość może być widoczna dla pracowników firmy Microsoft lub przechowywana długoterminowo.
>
> Parametr `groupId` wymaga, aby dane mieć format identyfikatora GUID. Zalecamy używanie losowo wygenerowanych identyfikatorów GUID, które nie są uznawane za dane osobowe w systemach.
>

Aby rozpocząć nowe wywołanie grupy lub dołączyć bieżące wywołanie grupy, użyj metody i `join` przekaż obiekt z `groupId` właściwością . Wartość `groupId` musi być identyfikatorem GUID.

```js

const context = { groupId: <GUID>}
const call = callAgent.join(context);

```

### <a name="join-a-teams-meeting"></a>Dołączanie do spotkania zespołów
> [!NOTE]
> Ten interfejs API jest udostępniany dla deweloperów i może zostać zmieniony na podstawie otrzymanych opinii. Nie należy używać tego interfejsu API w środowisku produkcyjnym. Aby użyć tego interfejsu API, użyj wersji "beta" zestawu WEB SDK wywoływania ACS

Aby dołączyć do spotkania w układzie Teams, użyj metody i przekaż link do spotkania lub `join` współrzędne spotkania.

Dołącz przy użyciu linku spotkania:

```js
const locator = { meetingLink: <meeting link>}
const call = callAgent.join(locator);
```

Dołącz przy użyciu współrzędnych spotkania:

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

Wystąpienie `callAgent` emituje `incomingCall` zdarzenie, gdy tożsamość zalogowanego odbiera wywołanie przychodzące. Aby nasłuchiwać tego zdarzenia, zasubskrybuj je przy użyciu jednej z tych opcji:

```js
const incomingCallHander = async (args: { incomingCall: IncomingCall }) => {
    const incomingCall = args.incomingCall; 
    // Get incoming call ID
    var incomingCallId = incomingCall.id
    // Get information about this Call. This API is provided as a preview for developers
    // and may change based on feedback that we receive. Do not use this API in a production environment.
    // To use this api please use 'beta' release of ACS Calling Web SDK
    var callInfo = incomingCall.info;

    // Get information about caller
    var callerInfo = incomingCall.callerInfo

    // Accept the call
    var call = await incomingCall.accept();

    // Reject the call
    incomingCall.reject();

    // Subscribe to callEnded event and get the call end reason
     incomingCall.on('callEnded', args => {
        console.log(args.callEndReason);
    });

    // callEndReason is also a property of IncomingCall
    var callEndReason = incomingCall.callEndReason;
};
callAgentInstance.on('incomingCall', incomingCallHander);
```

Zdarzenie `incomingCall` obejmuje `incomingCall` wystąpienie, które można zaakceptować lub odrzucić.

## <a name="manage-calls"></a>Zarządzanie wywołaniami

Podczas rozmowy można uzyskać dostęp do właściwości wywołania oraz zarządzać ustawieniami wideo i audio.

### <a name="check-call-properties"></a>Sprawdzanie właściwości wywołania

Pobierz unikatowy identyfikator (ciąg) dla wywołania:

   ```js
    const callId: string = call.id;
   ```
Uzyskaj informacje o wywołaniu:
> [!NOTE]
> Ten interfejs API jest udostępniany dla deweloperów i może zostać zmieniony na podstawie otrzymanych opinii. Nie należy używać tego interfejsu API w środowisku produkcyjnym. Aby użyć tego interfejsu API, użyj wersji "beta" wywoływanego internetowego zestawu SDK usługi ACS
   ```js
   const callInfo = call.info;
   ```

Dowiedz się więcej o innych uczestnikach wywołania, inspekcji `remoteParticipants` kolekcji w wystąpieniu "wywołania":

   ```js
   const remoteParticipants = call.remoteParticipants;
   ```

Identyfikowanie wywołującego wywołania przychodzącego:

   ```js
   const callerIdentity = call.callerInfo.identifier;
   ```

   `identifier` jest jednym z `CommunicationIdentifier` typów.

Pobierz stan wywołania:

   ```js
   const callState = call.state;
   ```

   Zwraca ciąg reprezentujący bieżący stan wywołania:

  - `None`: początkowy stan wywołania.
  - `Connecting`: początkowy stan przejścia po umieszczeniu lub zaakceptowaniu wywołania.
  - `Ringing`: W przypadku połączenia wychodzącego element wskazuje, że wywołanie jest dzwoniące dla zdalnych uczestników. Jest po `Incoming` ich stronie.
  - `EarlyMedia`: wskazuje stan, w którym anons jest odtwarzany przed połączeniem wywołania.
  - `Connected`: wskazuje, że wywołanie jest połączone.
  - `LocalHold`: wskazuje, że wywołanie jest wstrzymane przez uczestnika lokalnego. Żaden nośnik nie przepływa między lokalnym punktem końcowym i uczestnikami zdalnymi.
  - `RemoteHold`: wskazuje, że wywołanie zostało wstrzymane przez uczestnika zdalnego. Żaden nośnik nie przepływa między lokalnym punktem końcowym i uczestnikami zdalnymi.
  - `InLobby`: wskazuje, że użytkownik znajduje się w usa.
  - `Disconnecting`: stan przejścia, zanim wywołanie przejdzie do `Disconnected` stanu.
  - `Disconnected`: końcowy stan wywołania. Jeśli połączenie sieciowe zostanie utracone, stan zmieni się na `Disconnected` po dwóch minutach.

Dowiedz się, dlaczego wywołanie zakończyło się, sprawdzając `callEndReason` właściwość :

   ```js
   const callEndReason = call.callEndReason;
   const callEndReasonCode = callEndReason.code // (number) code associated with the reason
   const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
   ```

Sprawdź, czy bieżące wywołanie jest przychodzące, czy wychodzące, przez sprawdzenie `direction` właściwości . Zwraca wartość `CallDirection` .

  ```js
   const isIncoming = call.direction == 'Incoming';
   const isOutgoing = call.direction == 'Outgoing';
   ```

Sprawdź, czy bieżący mikrofon jest wyciszony. Zwraca wartość `Boolean` .

   ```js
   const muted = call.isMuted;
   ```

Sprawdź, czy strumień udostępniania ekranu jest wysyłany z danego punktu końcowego, sprawdzając `isScreenSharingOn` właściwość . Zwraca wartość `Boolean` .

   ```js
   const isScreenSharingOn = call.isScreenSharingOn;
   ```

Sprawdź aktywne strumienie wideo, sprawdzając `localVideoStreams` kolekcję. Zwraca `LocalVideoStream` obiekty.

   ```js
   const localVideoStreams = call.localVideoStreams;
   ```




### <a name="mute-and-unmute"></a>Wyciszanie i wyciszanie

Aby wyciszyć lub wyłączyć wyciszenie lokalnego punktu końcowego, można użyć asynchronicznych `mute` `unmute` interfejsów API i :

```js

//mute local device
await call.mute();

//unmute local device
await call.unmute();

```

### <a name="start-and-stop-sending-local-video"></a>Uruchamianie i zatrzymywanie wysyłania lokalnego wideo

Aby rozpocząć film wideo, należy wyliczyć kamery przy `getCameras` użyciu metody obiektu `deviceManager` . Następnie utwórz nowe wystąpienie klasy z `LocalVideoStream` żądaną kamerą, a następnie przekaż `LocalVideoStream` obiekt do metody `startVideo` :

```js
const deviceManager = await callClient.getDeviceManager();
const cameras = await deviceManager.getCameras();
const camera = cameras[0]
const localVideoStream = new LocalVideoStream(camera);
await call.startVideo(localVideoStream);
```

Po pomyślnym rozpoczęciu wysyłania wideo `LocalVideoStream` wystąpienie jest dodawane do `localVideoStreams` kolekcji w wystąpieniu wywołania.

```js
call.localVideoStreams[0] === localVideoStream;
```

Aby zatrzymać lokalne wideo, `localVideoStream` przekaż wystąpienie dostępne w `localVideoStreams` kolekcji:

```js
await call.stopVideo(localVideoStream);
```

Możesz przełączyć się na inne urządzenie aparatu podczas wysyłania wideo, inwołując `switchSource` `localVideoStream` wystąpienie:

```js
const cameras = await callClient.getDeviceManager().getCameras();
const camera = cameras[1];
localVideoStream.switchSource(camera);
```

## <a name="manage-remote-participants"></a>Zarządzanie uczestnikami zdalnymi

Wszyscy uczestnicy zdalni są reprezentowani przez `RemoteParticipant` typ i dostępni `remoteParticipants` za pośrednictwem kolekcji w wystąpieniu wywołania.

### <a name="list-the-participants-in-a-call"></a>Lista uczestników rozmowy

Kolekcja `remoteParticipants` zwraca listę zdalnych uczestników wywołania:

```js
call.remoteParticipants; // [remoteParticipant, remoteParticipant....]
```

### <a name="access-remote-participant-properties"></a>Uzyskiwanie dostępu do właściwości zdalnego uczestnika

Uczestnicy zdalni mają zestaw skojarzonych właściwości i kolekcji:

- `CommunicationIdentifier`: Pobierz identyfikator zdalnego uczestnika. Tożsamość jest jednym z `CommunicationIdentifier` typów:

  ```js
  const identifier = remoteParticipant.identifier;
  ```

  Może to być jeden z następujących `CommunicationIdentifier` typów:

  - `{ communicationUserId: '<ACS_USER_ID'> }`: obiekt reprezentujący użytkownika ACS.
  - `{ phoneNumber: '<E.164>' }`: obiekt reprezentujący numer telefonu w formacie E.164.
  - `{ microsoftTeamsUserId: '<TEAMS_USER_ID>', isAnonymous?: boolean; cloud?: "public" | "dod" | "gcch" }`: obiekt reprezentujący użytkownika aplikacji Teams.
  - `{ id: string }`: identyfikator ponownego wcniania obiektu, który nie pasuje do żadnego z innych typów identyfikatorów

- `state`: Pobierz stan zdalnego uczestnika.

  ```js
  const state = remoteParticipant.state;
  ```

  Stan może być:

  - `Idle`: stan początkowy.
  - `Connecting`: stan przejścia, gdy uczestnik łączy się z wywołaniem.
  - `Ringing`: uczestnik dzwoni.
  - `Connected`: Uczestnik jest połączony z wywołaniem.
  - `Hold`: uczestnik jest wstrzymywany.
  - `EarlyMedia`: Anons odtwarzany, zanim uczestnik połączy się z wywołaniem.
  - `InLobby`: wskazuje, że uczestnik zdalny znajduje się w usa.
  - `Disconnected`: stan końcowy. Uczestnik jest odłączony od wywołania. Jeśli uczestnik zdalny utraci łączność sieciową, jego stan zmieni się `Disconnected` na po dwóch minutach.

- `callEndReason`: Aby dowiedzieć się, dlaczego uczestnik opuścił wywołanie, sprawdź `callEndReason` właściwość :

  ```js
  const callEndReason = remoteParticipant.callEndReason;
  const callEndReasonCode = callEndReason.code // (number) code associated with the reason
  const callEndReasonSubCode = callEndReason.subCode // (number) subCode associated with the reason
  ```

- `isMuted` status: Aby dowiedzieć się, czy uczestnik zdalny jest wyciszone, sprawdź `isMuted` właściwość. Zwraca wartość `Boolean` .

  ```js
  const isMuted = remoteParticipant.isMuted;
  ```

- `isSpeaking` status: Aby dowiedzieć się, czy uczestnik zdalny mówi, sprawdź `isSpeaking` właściwość . Zwraca wartość `Boolean` .

  ```js
  const isSpeaking = remoteParticipant.isSpeaking;
  ```

- `videoStreams`: Aby sprawdzić wszystkie strumienie wideo, które dany uczestnik wysyła w tym wywołaniu, sprawdź `videoStreams` kolekcję. Zawiera on `RemoteVideoStream` obiekty .

  ```js
  const videoStreams = remoteParticipant.videoStreams; // [RemoteVideoStream, ...]
  ```
- `displayName`: Aby uzyskać nazwę wyświetlaną dla tego zdalnego uczestnika, sprawdź właściwość `displayName` zwraca ciąg. 

  ```js
  const displayName = remoteParticipant.displayName;
  ```

### <a name="add-a-participant-to-a-call"></a>Dodawanie uczestnika do wywołania

Aby dodać uczestnika (użytkownika lub numer telefonu) do połączenia, możesz użyć `addParticipant` . Podaj jeden z `Identifier` typów. Synchronicznie zwraca `remoteParticipant` wystąpienie . Zdarzenie `remoteParticipantsUpdated` z wywołania jest wywoływana, gdy uczestnik zostanie pomyślnie dodany do wywołania.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
const remoteParticipant = call.addParticipant(userIdentifier);
const remoteParticipant = call.addParticipant(pstnIdentifier, {alternateCallerId: '<Alternate Caller ID>'});
```

### <a name="remove-a-participant-from-a-call"></a>Usuwanie uczestnika z wywołania

Aby usunąć uczestnika (użytkownika lub numer telefonu) z połączenia, możesz wywołać `removeParticipant` . Musisz przekazać jeden z `Identifier` typów. Jest to rozwiązywane asynchronicznie po uczestnika jest usuwany z wywołania. Uczestnik jest również usuwany z `remoteParticipants` kolekcji.

```js
const userIdentifier = { communicationUserId: <ACS_USER_ID> };
const pstnIdentifier = { phoneNumber: <PHONE_NUMBER>}
await call.removeParticipant(userIdentifier);
await call.removeParticipant(pstnIdentifier);
```

## <a name="render-remote-participant-video-streams"></a>Renderowanie zdalnych strumieni wideo uczestnika

Aby wyświetlić listę strumieni wideo i strumieni udostępniania ekranu uczestników zdalnych, sprawdź `videoStreams` kolekcje:

```js
const remoteVideoStream: RemoteVideoStream = call.remoteParticipants[0].videoStreams[0];
const streamType: MediaStreamType = remoteVideoStream.mediaStreamType;
```

Aby `RemoteVideoStream` renderować , musisz zasubskrybować jego `isAvailableChanged` zdarzenie. Jeśli właściwość `isAvailable` zmieni się na , zdalny uczestnik wysyła `true` strumień. Następnie utwórz nowe wystąpienie klasy , a następnie utwórz nowe wystąpienie przy `VideoStreamRenderer` `VideoStreamRendererView` użyciu metody `createView` asynchronicznej.  Następnie można dołączyć `view.target` do dowolnego elementu interfejsu użytkownika.

Za każdym razem, gdy dostępność zdalnego strumienia się zmienia, można zdecydować się na zniszczenie całego strumienia lub zachowanie go, ale spowoduje to wyświetlenie `VideoStreamRenderer` `VideoStreamRendererView` pustej ramki wideo.

```js
function subscribeToRemoteVideoStream(remoteVideoStream: RemoteVideoStream) {
    let videoStreamRenderer: VideoStreamRenderer = new VideoStreamRenderer(remoteVideoStream);
    const displayVideo = () => {
        const view = await videoStreamRenderer.createView();
        htmlElement.appendChild(view.target);
    }
    remoteVideoStream.on('isAvailableChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            displayVideo();
        } else {
            videoStreamRenderer.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        displayVideo();
    }
}
```

### <a name="remote-video-stream-properties"></a>Właściwości zdalnego strumienia wideo

Zdalne strumienie wideo mają następujące właściwości:

- `id`: identyfikator zdalnego strumienia wideo.

  ```js
  const id: number = remoteVideoStream.id;
  ```

- `mediaStreamType`: może być `Video` lub `ScreenSharing` .

  ```js
  const type: MediaStreamType = remoteVideoStream.mediaStreamType;
  ```

- `isAvailable`: czy zdalny punkt końcowy uczestnika aktywnie wysyła strumień.

  ```js
  const type: boolean = remoteVideoStream.isAvailable;
  ```

### <a name="videostreamrenderer-methods-and-properties"></a>Metody i właściwości obiektu VideoStreamRenderer
Utwórz wystąpienie, które można dołączyć do interfejsu użytkownika aplikacji w celu renderowania zdalnego strumienia wideo, użyj metody asynchronicznej, rozwiąże problem, gdy strumień jest gotowy do renderowania, i zwraca obiekt z właściwością reprezentującą element, który można dołączyć w dowolnym miejscu drzewa `VideoStreamRendererView` `createView()` `target` `video` DOM

  ```js
  videoStreamRenderer.createView()
  ```

Dispose of `videoStreamRenderer` i wszystkie skojarzone `VideoStreamRendererView` wystąpienia:

  ```js
  videoStreamRenderer.dispose()
  ```

### <a name="videostreamrendererview-methods-and-properties"></a>Metody i właściwości Obiektu VideoStreamRendererView

Podczas tworzenia `VideoStreamRendererView` obiektu można określić właściwości `scalingMode` i `isMirrored` . `scalingMode` Może to być `Stretch` `Crop` , lub `Fit` . Jeśli `isMirrored` zostanie określony, renderowany strumień zostanie przerzucony w pionie.

```js
const videoStreamRendererView: VideoStreamRendererView = await videoStreamRenderer.createView({ scalingMode, isMirrored });
```

Każde `VideoStreamRendererView` wystąpienie ma `target` właściwość, która reprezentuje powierzchnię renderowania. Dołącz tę właściwość w interfejsie użytkownika aplikacji:

```js
htmlElement.appendChild(view.target);
```

Aktualizację można `scalingMode` zaktualizować przez wywołania `updateScalingMode` metody :

```js
view.updateScalingMode('Crop')
```

## <a name="device-management"></a>Zarządzanie urządzeniami

W `deviceManager` programie można wyliczać urządzenia lokalne, które mogą przesyłać strumienie audio i wideo w wywołaniu. Można go również użyć do żądania uprawnień dostępu do mikrofonów i kamer urządzenia lokalnego.

Dostęp można `deviceManager` uzyskać, wywołując `callClient.getDeviceManager()` metodę :

```js
const deviceManager = await callClient.getDeviceManager();
```

### <a name="get-local-devices"></a>Uzyskiwanie urządzeń lokalnych

Aby uzyskać dostęp do urządzeń lokalnych, można użyć metod wyliczania w programie `deviceManager` . Wyliczenie jest akcją asynchroniczną

```js
//  Get a list of available video devices for use.
const localCameras = await deviceManager.getCameras(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
const localMicrophones = await deviceManager.getMicrophones(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
const localSpeakers = await deviceManager.getSpeakers(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-the-default-microphone-and-speaker"></a>Ustawianie domyślnego mikrofonu i prelegenta

W `deviceManager` programie można ustawić domyślne urządzenie, które będzie używać do uruchamiania wywołania. Jeśli wartości domyślne klienta nie są ustawione, program Communication Services wartości domyślne systemu operacyjnego.

```js
// Get the microphone device that is being used.
const defaultMicrophone = deviceManager.selectedMicrophone;

// Set the microphone device to use.
await deviceManager.selectMicrophone(localMicrophones[0]);

// Get the speaker device that is being used.
const defaultSpeaker = deviceManager.selectedSpeaker;

// Set the speaker device to use.
await deviceManager.selectSpeaker(localSpeakers[0]);
```

### <a name="local-camera-preview"></a>Podgląd kamery lokalnej

Możesz użyć funkcji `deviceManager` i `VideoStreamRenderer` , aby rozpocząć renderowanie strumieni z lokalnej kamery. Ten strumień nie będzie wysyłany do innych uczestników; jest to lokalny kanał informacyjny podglądu.

```js
const cameras = await deviceManager.getCameras();
const camera = cameras[0];
const localCameraStream = new LocalVideoStream(camera);
const videoStreamRenderer = new VideoStreamRenderer(localCameraStream);
const view = await videoStreamRenderer.createView();
htmlElement.appendChild(view.target);

```

### <a name="request-permission-to-camera-and-microphone"></a>Żądanie uprawnień do aparatu i mikrofonu

Monituj użytkownika o udzielenie uprawnień do aparatu i mikrofonu:

```js
const result = await deviceManager.askDevicePermission({audio: true, video: true});
```

Jest to rozwiązywane przy użyciu obiektu , który wskazuje, czy `audio` `video` udzielono uprawnień i :

```js
console.log(result.audio);
console.log(result.video);
```

## <a name="record-calls"></a>Rejestrowanie wywołań
> [!NOTE]
> Ten interfejs API jest udostępniany dla deweloperów i może zostać zmieniony na podstawie otrzymanych opinii. Nie należy używać tego interfejsu API w środowisku produkcyjnym. Aby użyć tego interfejsu API, użyj wersji "beta" wywoływanego internetowego zestawu SDK usługi ACS

Rejestrowanie wywołań to rozszerzona funkcja podstawowego interfejsu `Call` API. Najpierw musisz uzyskać obiekt interfejsu API funkcji rejestrowania:

```js
const callRecordingApi = call.api(Features.Recording);
```

Następnie, aby sprawdzić, czy wywołanie jest rejestrowane, sprawdź `isRecordingActive` właściwość `callRecordingApi` . Zwraca wartość `Boolean` .

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
> [!NOTE]
> Ten interfejs API jest udostępniany dla deweloperów i może zostać zmieniony na podstawie otrzymanych opinii. Nie należy używać tego interfejsu API w środowisku produkcyjnym. Aby użyć tego interfejsu API, użyj wersji "beta" wywoływanego internetowego zestawu SDK usługi ACS

Transfer wywołań to rozszerzona funkcja podstawowego interfejsu `Call` API. Najpierw należy pobrać obiekt interfejsu API funkcji transferu:

```js
const callTransferApi = call.api(Features.Transfer);
```

Transfery połączeń obejmują trzy strony:

- *Transferator:* osoba, która inicjuje żądanie przeniesienia.
- *Transferowany:* osoba, która jest transferowana.
- *Cel przeniesienia:* osoba, do której jest transferowany.

Transfery wykonaj następujące kroki:

1. Istnieje już połączone wywołanie między *transferatorem* a *przekierowywcą*. Transferator *decyduje* się na przeniesienie wywołania od obiektu *transferowego* do *obiektu docelowego transferu*.
1. Transferator *wywołuje* interfejs `transfer` API.
1. Transferowany *decyduje o* tym, czy do lub żądania transferu do `accept` obiektu `reject` *docelowego transferu* przy użyciu `transferRequested` zdarzenia.
1. Obiekt *docelowy transferu* odbiera przychodzącego wywołania tylko wtedy, gdy *transferee akceptuje* żądanie przeniesienia.

Aby przenieść bieżące wywołanie, możesz użyć interfejsu `transfer` API. `transfer` przyjmuje opcjonalną `transferCallOptions` flagę , która umożliwia ustawienie `disableForwardingAndUnanswered` flagi :

- `disableForwardingAndUnanswered = false`: jeśli *obiekt docelowy transferu* nie odpowiada na wywołanie  transferu, transfer jest zgodny z ustawieniami przekazywania obiektu docelowego transferu i bez odpowiedzi.
- `disableForwardingAndUnanswered = true`: Jeśli obiekt *docelowy transferu* nie odpowiada na wywołanie transferu, próba transferu kończy się.

```js
// transfer target can be an ACS user
const id = { communicationUserId: <ACS_USER_ID> };
```

```js
// call transfer API
const transfer = callTransferApi.transfer({targetParticipant: id});
```

Interfejs `transfer` API umożliwia subskrybowanie `transferStateChanged` zdarzeń i `transferRequested` . Zdarzenie `transferRequested` pochodzi z `call` wystąpienia, zdarzenie i transfer, a następnie pochodzi z `transferStateChanged` `state` `error` `transfer` wystąpienia.

```js
// transfer state
const transferState = transfer.state; // None | Transferring | Transferred | Failed

// to check the transfer failure reason
const transferError = transfer.error; // transfer error code that describes the failure if a transfer request failed
```

Transferowany *może* zaakceptować lub odrzucić żądanie  przeniesienia zainicjowane przez transferatora w `transferRequested` zdarzeniu przy użyciu lub `accept()` w `reject()` `transferRequestedEventArgs` . Dostęp do informacji `targetParticipant` i metod można uzyskać w `accept` `reject` programie `transferRequestedEventArgs` .

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

## <a name="learn-about-eventing-models"></a>Dowiedz się więcej o modelach obsługi zdarzeń

Sprawdź bieżące wartości i zasubskrybuj zdarzenia aktualizacji, aby uzyskać przyszłe wartości.

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
