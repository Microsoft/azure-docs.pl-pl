---
title: Szybki Start — Dodawanie filmu wideo do aplikacji (JavaScript)
titleSuffix: An Azure Communication Services quickstart
description: W tym przewodniku szybki start dowiesz się, jak dodać możliwości wywoływania wideo do aplikacji przy użyciu usług Azure Communications Services.
author: xumo-95
ms.author: mikben
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: azure-communication-services
ms.openlocfilehash: e7f74298b8bf8209a6b1473880b33d64bd17cfd9
ms.sourcegitcommit: bed20f85722deec33050e0d8881e465f94c79ac2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/25/2021
ms.locfileid: "105108098"
---
# <a name="quickstart-add-11-video-calling-to-your-app-javascript"></a>Szybki Start: Dodawanie 1:1 wideo dla aplikacji (JavaScript)

## <a name="download-code"></a>Pobierz kod

Znajdź końcowy kod dla tego przewodnika Szybki Start w witrynie [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling)

## <a name="prerequisites"></a>Wymagania wstępne
- Uzyskaj konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Node.js](https://nodejs.org/en/) Aktywne wersje LTS LTS i Maintenance (8.11.1 i 10.14.1)
- Utwórz zasób aktywnego usługi komunikacyjnej. [Utwórz zasób usług komunikacyjnych](../create-communication-resource.md?pivots=platform-azp&tabs=windows).
- Utwórz token dostępu użytkownika, aby utworzyć wystąpienie klienta wywołania. [Dowiedz się, jak tworzyć tokeny dostępu użytkowników i zarządzać nimi](../access-tokens.md?pivots=programming-language-csharp).

## <a name="setting-up"></a>Konfigurowanie
### <a name="create-a-new-nodejs-application"></a>Tworzenie nowej aplikacji Node.js
Otwórz terminal lub okno poleceń Utwórz nowy katalog dla aplikacji i przejdź do niego.
```console
mkdir calling-quickstart && cd calling-quickstart
```
### <a name="install-the-package"></a>Zainstaluj pakiet
Użyj `npm install` polecenia, aby zainstalować zestaw SDK wywoływania usługi Azure Communications Services dla języka JavaScript.

> [!IMPORTANT]
> Ten przewodnik Szybki Start używa wersji zestawu SDK wywołującej usługi komunikacyjnej platformy Azure `1.0.0.beta-10` . 


```console
npm install @azure/communication-common --save
npm install @azure/communication-calling --save
```
### <a name="set-up-the-app-framework"></a>Konfigurowanie struktury aplikacji

Ten przewodnik Szybki Start używa pakietu WebPack do łączenia zasobów aplikacji. Uruchom następujące polecenie, aby zainstalować `webpack` pakiety, `webpack-cli` i `webpack-dev-server` npm i wyświetlić je jako zależności deweloperskie w programie `package.json` :

```console
npm install webpack@4.42.0 webpack-cli@3.3.11 webpack-dev-server@3.10.3 --save-dev
```
Utwórz `index.html` plik w katalogu głównym projektu. Ten plik zostanie użyty do skonfigurowania podstawowego układu, który zezwoli użytkownikowi na umieszczenie połączenia wideo 1:1.

Oto kod:
```html
<!DOCTYPE html>
<html>
<head>
    <title>Communication Client - 1:1 Video Calling Sample</title>
</head>

<body>
    <h4>Azure Communication Services</h4>
    <h1>1:1 Video Calling Quickstart</h1>
    <input 
    id="callee-id-input"
    type="text"
    placeholder="Who would you like to call?"
    style="margin-bottom:1em; width: 200px;"
    />

    <div>
    <button id="call-button" type="button" disabled="true">
        start call
    </button>
        &nbsp;
    <button id="hang-up-button" type="button" disabled="true">
        hang up
    </button>
        &nbsp;
    <button id="start-Video" type="button" disabled="true">
        start video
    </button>
        &nbsp;
    <button id="stop-Video" type="button" disabled="true">
        stop video
    </button>     
    </div>

    <div>Local Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;">
      <div id="myVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>     
    </div>
    <div>Remote Video</div>
    <div style="height:200px; width:300px; background-color:black; position:relative;"> 
      <div id="remoteVideo" style="background-color: black; position:absolute; top:50%; transform: translateY(-50%);">
      </div>
    </div>

    <script src="./bundle.js"></script>
</body>
</html>
```

Utwórz plik w katalogu głównym projektu o nazwie, `client.js` aby zawierał logikę aplikacji dla tego przewodnika Szybki Start. Dodaj następujący kod, aby zaimportować klienta wywołującego i uzyskać odwołania do elementów modelu DOM.

```JavaScript
import { CallClient, CallAgent, VideoStreamRenderer, LocalVideoStream } from "@azure/communication-calling";
import { AzureCommunicationTokenCredential } from '@azure/communication-common';

let call;
let callAgent;
const calleeInput = document.getElementById("callee-id-input");
const callButton = document.getElementById("call-button");
const hangUpButton = document.getElementById("hang-up-button");
const stopVideoButton = document.getElementById("stop-Video");
const startVideoButton = document.getElementById("start-Video");

let placeCallOptions;
let deviceManager;
let localVideoStream;
let rendererLocal;
let rendererRemote;
```
## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje zestawu SDK wywołującego usługi Azure Communications Services:

| Nazwa      | Opis | 
| :---        |    :----   |
| CallClient  | CallClient jest głównym punktem wejścia do wywołującego zestawu SDK.      |
| CallAgent  | CallAgent jest używany do uruchamiania wywołań i zarządzania nimi.        |
| Devicemanager | Devicemanager jest używany do zarządzania urządzeniami multimedialnymi.    |
| AzureCommunicationTokenCredential | Klasa AzureCommunicationTokenCredential implementuje interfejs CommunicationTokenCredential, który jest używany do tworzenia wystąpienia CallAgent.        |

## <a name="authenticate-the-client-and-access-devicemanager"></a>Uwierzytelnianie klienta i dostęp do urządzenia

Należy zastąpić <USER_ACCESS_TOKEN> z prawidłowym tokenem dostępu użytkownika dla zasobu. Jeśli nie masz jeszcze dostępnego tokenu, zapoznaj się z dokumentacją tokenu dostępu użytkownika. Przy użyciu `CallClient` , zainicjuj `CallAgent` wystąpienie z, `CommunicationUserCredential` które umożliwi nam wykonywanie i odbieranie wywołań. Aby uzyskać dostęp do `DeviceManager` wystąpienia callAgent, należy najpierw utworzyć wystąpienie. Następnie można użyć metody z `getDeviceManager` `CallClient` wystąpienia, aby uzyskać `DeviceManager` .

Dodaj następujący kod do pliku `client.js`:

```JavaScript
async function init() {
    const callClient = new CallClient();
    const tokenCredential = new AzureCommunicationTokenCredential("<USER ACCESS TOKEN>");
    callAgent = await callClient.createCallAgent(tokenCredential, { displayName: 'optional ACS user name' });
    
    deviceManager = await callClient.getDeviceManager();
    callButton.disabled = false;
}
init();
```
## <a name="place-a-11-outgoing-video-call-to-a-user"></a>Umieść wychodzące wywołanie wideo 1:1 dla użytkownika

Dodaj odbiornik zdarzeń, aby zainicjować wywołanie po `callButton` kliknięciu:

Najpierw należy wyliczyć aparaty lokalne przy użyciu `getCameraList` interfejsu API urządzenia. W tym przewodniku szybki start używamy pierwszego aparatu w kolekcji. Po wybraniu żądanego aparatu wystąpienie LocalVideoStream zostanie skonstruowane i przesłane `videoOptions` jako element w tablicy LocalVideoStream do metody Call. Po nawiązaniu połączenia zostanie automatycznie rozpoczęte wysyłanie strumienia wideo do innego uczestnika. 

```JavaScript
callButton.addEventListener("click", async () => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    placeCallOptions = {videoOptions: {localVideoStreams:[localVideoStream]}};

    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;

    const userToCall = calleeInput.value;
    call = callAgent.startCall(
        [{ communicationUserId: userToCall }],
        placeCallOptions
    );

    subscribeToRemoteParticipantInCall(call);

    hangUpButton.disabled = false;
    callButton.disabled = true;
});
```  
Aby renderować a `LocalVideoStream` , należy utworzyć nowe wystąpienie `VideoStreamRenderer` , a następnie utworzyć nowe `VideoStreamRendererView` wystąpienie przy użyciu metody asynchronicznej `createView` . Następnie można dołączyć `view.target` do dowolnego elementu interfejsu użytkownika. 

```JavaScript
async function localVideoView() {
    rendererLocal = new VideoStreamRenderer(localVideoStream);
    const view = await rendererLocal.createView();
    document.getElementById("myVideo").appendChild(view.target);
}
```
Wszyscy uczestnicy zdalni są dostępni za pomocą `remoteParticipants` kolekcji w wystąpieniu wywołania. Musisz nasłuchiwać zdarzenia, aby otrzymywać `remoteParticipantsUpdated` powiadomienia, gdy do wywołania zostanie dodany nowy uczestnik zdalny. Należy również wykonać iterację `remoteParticipants` kolekcji, aby subskrybować ich strumienie wideo. 

```JavaScript
function subscribeToRemoteParticipantInCall(callInstance) {
    callInstance.on('remoteParticipantsUpdated', e => {
        e.added.forEach( p => {
            subscribeToParticipantVideoStreams(p);
        })
    }); 
    callInstance.remoteParticipants.forEach( p => {
        subscribeToParticipantVideoStreams(p);
    })
}
```
Musisz subskrybować `videoStreamsUpdated` zdarzenie, aby obsłużyć dodane strumienie wideo dla uczestników zdalnych. Możesz sprawdzić kolekcje, `videoStreams` Aby wyświetlić listę strumieni każdego uczestnika podczas przechodzenia przez `remoteParticipants` kolekcję bieżącego wywołania.

```JavaScript
function subscribeToParticipantVideoStreams(remoteParticipant) {
    remoteParticipant.on('videoStreamsUpdated', e => {
        e.added.forEach(v => {
            handleVideoStream(v);
        })
    });
    remoteParticipant.videoStreams.forEach(v => {
        handleVideoStream(v);
    });
}
```
Musisz subskrybować `isAvailableChanged` zdarzenie, aby renderować `remoteVideoStream` . Jeśli `isAvailable` Właściwość zmieni się na `true` , uczestnik zdalny wysyła strumień. Zawsze, gdy jest dostępna zmiana strumienia zdalnego, można wybrać opcję zniszczenia całości `Renderer` , określonych `RendererView` lub zachowywania, ale spowoduje to wyświetlenie pustej ramki wideo.
```JavaScript
function handleVideoStream(remoteVideoStream) {
    remoteVideoStream.on('availabilityChanged', async () => {
        if (remoteVideoStream.isAvailable) {
            remoteVideoView(remoteVideoStream);
        } else {
            rendererRemote.dispose();
        }
    });
    if (remoteVideoStream.isAvailable) {
        remoteVideoView(remoteVideoStream);
    }
}
```
Aby renderować a `RemoteVideoStream` , należy utworzyć nowe wystąpienie `VideoStreamRenderer` , a następnie utworzyć nowe `VideoStreamRendererView` wystąpienie przy użyciu metody asynchronicznej `createView` . Następnie można dołączyć `view.target` do dowolnego elementu interfejsu użytkownika. 

```JavaScript
async function remoteVideoView(remoteVideoStream) {
    rendererRemote = new VideoStreamRenderer(remoteVideoStream);
    const view = await rendererRemote.createView();
    document.getElementById("remoteVideo").appendChild(view.target);
}
```
## <a name="receive-an-incoming-call"></a>Odbieranie połączenia przychodzącego
Aby obsłużyć wywołania przychodzące, należy nasłuchiwać `incomingCall` zdarzenia `callAgent` . Gdy istnieje wywołanie przychodzące, należy wyliczyć lokalne aparaty i skonstruować `LocalVideoStream` wystąpienie, aby wysłać strumień wideo do innego uczestnika. Musisz również subskrybować, aby `remoteParticipants` obsłużyć zdalne strumienie wideo. Możesz zaakceptować lub odrzucić wywołanie za pomocą `incomingCall` wystąpienia. 

Wprowadź implementację w programie, `init()` Aby obsłużyć wywołania przychodzące. 

```JavaScript
callAgent.on('incomingCall', async e => {
    const videoDevices = await deviceManager.getCameras();
    const videoDeviceInfo = videoDevices[0];
    localVideoStream = new LocalVideoStream(videoDeviceInfo);
    localVideoView();

    stopVideoButton.disabled = false;
    callButton.disabled = true;
    hangUpButton.disabled = false;

    const addedCall = await e.incomingCall.accept({videoOptions: {localVideoStreams:[localVideoStream]}});
    call = addedCall;

    subscribeToRemoteParticipantInCall(addedCall);  
});
```
## <a name="end-the-current-call"></a>Zakończ bieżące wywołanie
Dodaj odbiornik zdarzeń, aby zakończyć bieżące wywołanie po `hangUpButton` kliknięciu:
```JavaScript
hangUpButton.addEventListener("click", async () => {
    // dispose of the renderers
    rendererLocal.dispose();
    rendererRemote.dispose();
    // end the current call
    await call.hangUp();
    // toggle button states
    hangUpButton.disabled = true;
    callButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
## <a name="subscribe-to-call-updates"></a>Subskrybuj, aby wywoływać aktualizacje
Należy zasubskrybować zdarzenie, gdy uczestnik zdalny skończy wywołanie usunięcia elementów renderowania wideo i przełączników. 

Umieść implementację w init (), aby subskrybować `callsUpdated` zdarzenie. 
 ```JavaScript 
callAgent.on('callsUpdated', e => {
    e.removed.forEach(removedCall => {
        // dispose of video renders
        rendererLocal.dispose();
        rendererRemote.dispose();
        // toggle button states
        hangUpButton.disabled = true;
        callButton.disabled = false;
        stopVideoButton.disabled = true;
    })
})
```

## <a name="start-and-end-video-during-the-call"></a>Rozpocznij i Zakończ wideo podczas wywołania
Możesz zatrzymać wideo podczas bieżącego wywołania, dodając odbiornik zdarzeń do przycisku Zatrzymaj wideo, aby usunąć moduł renderujący `localVideoStream` . 
 ```JavaScript       
stopVideoButton.addEventListener("click", async () => {
    await call.stopVideo(localVideoStream);
    rendererLocal.dispose();
    startVideoButton.disabled = false;
    stopVideoButton.disabled = true;
});
```
Możesz dodać odbiornik zdarzeń do przycisku Uruchom wideo, aby włączyć odtwarzanie wideo, gdy zostało zatrzymane podczas bieżącego wywołania. 
```JavaScript
startVideoButton.addEventListener("click", async () => {
    await call.startVideo(localVideoStream);
    localVideoView();
    stopVideoButton.disabled = false;
    startVideoButton.disabled = true;
});
```
## <a name="run-the-code"></a>Uruchamianie kodu
Użyj, `webpack-dev-server` Aby skompilować i uruchomić aplikację. Uruchom następujące polecenie, aby powiązać hosta aplikacji w lokalnym serwerze WebServer:

```console
npx webpack-dev-server --entry ./client.js --output bundle.js --debug --devtool inline-source-map
```
Otwórz przeglądarkę i przejdź do http://localhost:8080/ . Powinien zostać wyświetlony następujący ekran:

:::image type="content" source="./media/javascript/1-on-1-video-calling.png" alt-text="1 na 1 stronie wywołującej wideo":::

Wychodzące połączenie wideo 1:1 można wykonać, podając identyfikator użytkownika w polu tekstowym, a następnie klikając przycisk Rozpocznij połączenie. 

## <a name="sample-code"></a>Przykładowy kod
Przykładową aplikację można pobrać z witryny [GitHub](https://github.com/Azure-Samples/communication-services-javascript-quickstarts/tree/main/add-1-on-1-video-calling).

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli chcesz wyczyścić i usunąć subskrypcję usług komunikacyjnych, możesz usunąć zasób lub grupę zasobów. Usunięcie grupy zasobów spowoduje również usunięcie wszystkich skojarzonych z nią zasobów. Dowiedz się więcej o [czyszczeniu zasobów](../create-communication-resource.md?pivots=platform-azp&tabs=windows#clean-up-resources).

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej informacji, zobacz następujące artykuły:

- Zapoznaj się z naszym [przykładem wywoływania sieci Web](https://docs.microsoft.com/azure/communication-services/samples/web-calling-sample)
- Informacje o [wywoływaniu możliwości zestawu SDK](https://docs.microsoft.com/azure/communication-services/quickstarts/voice-video-calling/calling-client-samples?pivots=platform-web)
- Dowiedz się więcej o [sposobie wywoływania programu](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/about-call-types)

