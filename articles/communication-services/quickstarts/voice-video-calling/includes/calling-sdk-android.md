---
author: mikben
ms.service: azure-communication-services
ms.topic: include
ms.date: 9/1/2020
ms.author: mikben
ms.openlocfilehash: bed2a4ccbe87aef9afa395ed789da393e885cc89
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779647"
---
## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Wdrożony zasób usług komunikacyjnych. [Utwórz zasób usług komunikacyjnych](../../create-communication-resource.md).
- A, `User Access Token` Aby włączyć klienta wywołania. Aby uzyskać więcej informacji na temat [uzyskiwania `User Access Token` ](../../access-tokens.md)
- Opcjonalnie: Ukończ Przewodnik Szybki Start dotyczący [dodawania wywołania do aplikacji](../getting-started-with-calling.md)

## <a name="setting-up"></a>Konfigurowanie

### <a name="install-the-package"></a>Zainstaluj pakiet

<!-- TODO: update with instructions on how to download, install and add package to project -->
Znajdź na poziomie projektu Build. Gradle i upewnij się, że dodano `mavenCentral()` do listy repozytoriów w ramach `buildscript` i `allprojects`
```groovy
buildscript {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```

```groovy
allprojects {
    repositories {
    ...
        mavenCentral()
    ...
    }
}
```
Następnie w kompilacji na poziomie modułu. Gradle Dodaj następujące wiersze do sekcji zależności

```groovy
dependencies {
    ...
    implementation 'com.azure.android:azure-communication-calling:1.0.0-beta.2'
    ...
}

```

## <a name="object-model"></a>Model obiektów

Następujące klasy i interfejsy obsługują niektóre główne funkcje biblioteki klienta wywołującego usługi Azure Communications Services:

| Nazwa                                  | Opis                                                  |
| ------------------------------------- | ------------------------------------------------------------ |
| CallClient| CallClient jest głównym punktem wejścia do biblioteki wywołującej klienta.|
| CallAgent | CallAgent jest używany do uruchamiania wywołań i zarządzania nimi. |
| CommunicationUserCredential | CommunicationUserCredential jest używany jako poświadczenia tokenu do tworzenia wystąpienia CallAgent.|

## <a name="initialize-the-callclient-create-a-callagent-and-access-the-devicemanager"></a>Inicjowanie CallClient, tworzenie CallAgent i dostęp do elementu devicemanager

Aby utworzyć `CallAgent` wystąpienie, należy wywołać `createCallAgent` metodę w `CallClient` wystąpieniu. Asynchronicznie zwraca `CallAgent` obiekt wystąpienia.
`createCallAgent`Metoda przyjmuje `CommunicationUserCredential` jako argument, który hermetyzuje [token dostępu](../../access-tokens.md).
Aby uzyskać dostęp do programu `DeviceManager` , należy najpierw utworzyć wystąpienie callAgent, a następnie użyć `CallClient.getDeviceManager` metody w celu uzyskania elementu devicemanager.

```java
String userToken = '<user token>';
CallClient callClient = new CallClient();
CommunicationUserCredential tokenCredential = new CommunicationUserCredential(userToken);
android.content.Context appContext = this.getApplicationContext(); // From within an Activity for instance
CallAgent callAgent = await callClient.createCallAgent((appContext, tokenCredential).get();
DeviceManage deviceManager = await callClient.getDeviceManager().get();
```

## <a name="place-an-outgoing-call-and-join-a-group-call"></a>Umieść połączenie wychodzące i Dołącz do wywołania grupy

Aby utworzyć i uruchomić wywołanie, należy wywołać `CallAgent.call()` metodę i podać `Identifier` elementy wywoływane.
Aby przyłączyć się do wywołania grupy, należy wywołać `CallAgent.join()` metodę i podać identyfikator GroupID. Identyfikatory grup muszą być w formacie GUID lub UUID.

Tworzenie i uruchamianie wywołań jest synchroniczne. Wystąpienie wywołania pozwala subskrybować wszystkie zdarzenia w wywołaniu.

### <a name="place-a-11-call-to-a-user"></a>Umieść wywołanie 1:1 dla użytkownika
Aby nawiązać połączenie z innym użytkownikiem usług komunikacyjnych, wywołaj `call` metodę na `callAgent` i przekaż obiekt z `communicationUserId` kluczem.
```java
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
CommunicationUser acsUserId = new CommunicationUser(<USER_ID>);
CommunicationUser participants[] = new CommunicationUser[]{ acsUserId };
call oneToOneCall = callAgent.call(appContext, participants, startCallOptions);
```

### <a name="place-a-1n-call-with-users-and-pstn"></a>Umieszczenie 1: n połączenia z użytkownikami i PSTN
> [!WARNING]
> Obecnie wywoływanie PSTN nie jest dostępne, aby nawiązać połączenie 1: n z użytkownikiem i numerem sieci PSTN, aby określić numer telefonu wywoływany.
Zasób usług komunikacyjnych musi być skonfigurowany tak, aby zezwalał na nawiązywanie połączeń PSTN:
```java
CommunicationUser acsUser1 = new CommunicationUser(<USER_ID>);
PhoneNumber acsUser2 = new PhoneNumber("<PHONE_NUMBER>");
CommunicationIdentifier participants[] = new CommunicationIdentifier[]{ acsUser1, acsUser2 };
StartCallOptions startCallOptions = new StartCallOptions();
Context appContext = this.getApplicationContext();
Call groupCall = callAgent.call(participants, startCallOptions);
```

### <a name="place-a-11-call-with-video-camera"></a>Umieszczanie wywołania 1:1 z kamerą wideo
> [!WARNING]
> Obecnie tylko jeden wychodzący lokalny strumień wideo jest obsługiwany w przypadku wywołania z filmem wideo, aby wyliczyć aparaty lokalne przy użyciu `deviceManager` `getCameraList` interfejsu API.
Po wybraniu odpowiedniego aparatu Użyj go, aby skonstruować `LocalVideoStream` wystąpienie i przekazać je do `videoOptions` jako element w `localVideoStream` tablicy do `call` metody.
Po nawiązaniu połączenia zostanie automatycznie rozpoczęte wysyłanie strumienia wideo z wybranego aparatu do innych uczestników.
```java
Context appContext = this.getApplicationContext();
VideoDeviceInfo desiredCamera = callClient.getDeviceManager().get().getCameraList().get(0);
LocalVideoStream currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
VideoOptions videoOptions = new VideoOptions(currentVideoStream);

CommunicationUser[] participants = new CommunicationUser[]{ new CommunicationUser("<acs user id>") };
StartCallOptions startCallOptions = new StartCallOptions();
startCallOptions.setVideoOptions(videoOptions);
Call call = callAgent.call(context, participants, startCallOptions);
```

### <a name="join-a-group-call"></a>Dołącz do wywołania grupy
Aby rozpocząć nowe wywołanie grupy lub dołączyć do trwającego wywołania grupy, należy wywołać metodę "join" i przekazać obiekt z `groupId` właściwością. Wartość musi być identyfikatorem GUID.
```java
Context appContext = this.getApplicationContext();
GroupCallContext groupCallContext = new groupCallContext("<GUID>");
JoinCallOptions joinCallOptions = new JoinCallOptions();

call = callAgent.join(context, groupCallContext, joinCallOptions);
```

## <a name="push-notifications"></a>Powiadomienia wypychane

### <a name="overview"></a>Omówienie
Mobilne powiadomienia wypychane są wyskakującymi powiadomieniami wyświetlanymi na urządzeniach przenośnych. W przypadku wywoływania usługi będziemy koncentrować się na powiadomieniach wypychanych (Voice over Internet Protocol). Będziemy rejestrować powiadomienia wypychane, obsługiwać powiadomienia wypychane, a następnie wycofać rejestrację powiadomień wypychanych.

### <a name="prerequisites"></a>Wymagania wstępne

Aby ukończyć tę sekcję, Utwórz konto Firebase i Włącz obsługę komunikatów w chmurze (FCM). Upewnij się, że obsługa komunikatów w chmurze Firebase jest połączona z wystąpieniem usługi Azure Notification Hub (ANH). Aby uzyskać instrukcje [, zobacz Łączenie Firebase z platformą Azure](https://docs.microsoft.com/azure/notification-hubs/notification-hubs-android-push-notification-google-fcm-get-started) .
W tej sekcji przyjęto również założenie, że używasz Android Studio w wersji 3,6 lub nowszej do kompilowania aplikacji.

Zestaw uprawnień jest wymagany dla aplikacji systemu Android, aby można było odbierać komunikaty powiadomień z usługi Firebase Cloud Messaging. W `AndroidManifest.xml` pliku Dodaj następujący zestaw uprawnień bezpośrednio po * manifeście<... >* lub poniżej *</application>* tagu

```XML
    <uses-permission android:name="android.permission.INTERNET"/>
    <uses-permission android:name="android.permission.GET_ACCOUNTS"/>
    <uses-permission android:name="com.google.android.c2dm.permission.RECEIVE" />
```

### <a name="register-for-push-notifications"></a>Zarejestruj się w celu otrzymywania powiadomień wypychanych

Aby zarejestrować się w celu otrzymywania powiadomień wypychanych, aplikacja musi wywołać `registerPushNotification()` wystąpienie *CallAgent* z tokenem rejestracji urządzenia.

Aby uzyskać token rejestracji urządzenia, dodaj bibliotekę kliencką Firebase do pliku *Build. Gradle* modułu aplikacji, dodając następujące wiersze w sekcji, jeśli jeszcze `dependencies` tego nie zrobiono:

```
    // Add the client library for Firebase Cloud Messaging
    implementation 'com.google.firebase:firebase-core:16.0.8'
    implementation 'com.google.firebase:firebase-messaging:20.2.4'
```

W pliku *Build. Gradle* poziomu projektu Dodaj następujące polecenie w sekcji, jeśli jeszcze `dependencies` nie zostało to zrobione:

```
    classpath 'com.google.gms:google-services:4.3.3'
```

Dodaj następującą wtyczkę na początku pliku, jeśli jeszcze jej nie ma:

```
apply plugin: 'com.google.gms.google-services'
```

Na pasku narzędzi wybierz pozycję *Synchronizuj teraz* . Dodaj następujący fragment kodu, aby uzyskać token rejestracji urządzenia wygenerowany przez bibliotekę klienta usługi Firebase Cloud Messaging dla wystąpienia aplikacji klienta, pamiętaj, aby dodać poniższe elementy Import do nagłówka głównego działania wystąpienia. Są one wymagane do pobrania tokenu dla fragmentu kodu:

```
import com.google.android.gms.tasks.OnCompleteListener;
import com.google.android.gms.tasks.Task;
import com.google.firebase.iid.FirebaseInstanceId;
import com.google.firebase.iid.InstanceIdResult;
```

Dodaj ten fragment kodu, aby pobrać token:

```
        FirebaseInstanceId.getInstance().getInstanceId()
                .addOnCompleteListener(new OnCompleteListener<InstanceIdResult>() {
                    @Override
                    public void onComplete(@NonNull Task<InstanceIdResult> task) {
                        if (!task.isSuccessful()) {
                            Log.w(TAG, "getInstanceId failed", task.getException());
                            return;
                        }

                        // Get new Instance ID token
                        String deviceToken = task.getResult().getToken();
                        // Log
                        Log.d(TAG, "Device Registration token retrieved successfully");
                    }
                });
```
Zarejestruj token rejestracji urządzenia z biblioteką klienta usługi wywołującej dla przychodzących powiadomień wypychanych:

```java
String deviceRegistrationToken = "some_token";
try {
    callAgent.registerPushNotification(deviceRegistrationToken).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while registering for Incoming Calls Push Notifications.")
}
```

### <a name="push-notification-handling"></a>Obsługa powiadomień wypychanych

Aby odbierać przychodzące powiadomienia wypychane, wywołaj *handlePushNotification ()* w wystąpieniu *CallAgent* z ładunkiem.

Aby uzyskać ładunek z usługi Firebase Cloud Messaging, Zacznij od utworzenia nowej usługi (plik > nowej usługi > usługi >), która rozszerza klasę *FirebaseMessagingService* Firebase i zastąpi `onMessageReceived` metodę. Ta metoda jest procedura obsługi zdarzeń wywoływana, gdy usługa Firebase Cloud Messaging dostarcza powiadomienia wypychane do aplikacji.

```java
public class MyFirebaseMessagingService extends FirebaseMessagingService {
    private java.util.Map<String, String> pushNotificationMessageData;

    @Override
    public void onMessageReceived(RemoteMessage remoteMessage) {
        // Check if message contains a notification payload.
        if (remoteMessage.getNotification() != null) {
            Log.d(TAG, "Message Notification Body: " + remoteMessage.getNotification().getBody());
        }
        else {
            pushNotificationMessageData = serializeDictionaryAsJson(remoteMessage.getData());
        }
    }
}
```
Dodaj następującą definicję usługi do `AndroidManifest.xml` pliku, wewnątrz <application> tagu:

```
        <service
            android:name=".MyFirebaseMessagingService"
            android:exported="false">
            <intent-filter>
                <action android:name="com.google.firebase.MESSAGING_EVENT" />
            </intent-filter>
        </service>
```

Po pobraniu ładunku można go przesłać do biblioteki klienta usług komunikacyjnych w celu obsługi przez wywołanie `handlePushNotification` metody w `CallAgent` wystąpieniu.

```java
java.util.Map<String, String> pushNotificationMessageDataFromFCM = remoteMessage.getData();
try {
    callAgent.handlePushNotification(pushNotificationMessageDataFromFCM).get();
}
catch(Exception e) {
    System.out.println("Something went wrong while handling the Incoming Calls Push Notifications.");
}
```

Po pomyślnym zarejestrowaniu komunikatu o błędzie powiadomień wypychanych, a programy obsługi wszystkich zdarzeń są zarejestrowane prawidłowo, aplikacja zostanie przekazana.

### <a name="unregister-push-notifications"></a>Wyrejestrowywanie powiadomień wypychanych

Aplikacje mogą wyrejestrować powiadomienia wypychane w dowolnym momencie. Wywołaj `unregisterPushNotification()` metodę w callAgent, aby wyrejestrować.

```java
try {
    callAgent.unregisterPushNotifications().get();
}
catch(Exception e) {
    System.out.println("Something went wrong while un-registering for all Incoming Calls Push Notifications.")
}
```

## <a name="call-management"></a>Zarządzanie połączeniami
Można uzyskać dostęp do właściwości wywołania i wykonać różne operacje w trakcie wywołania zarządzania ustawieniami związanymi z wideo i dźwiękiem.

### <a name="call-properties"></a>Właściwości wywołania

Pobierz unikatowy identyfikator dla tego wywołania:

```java
String callId = call.getCallId();
```

Aby dowiedzieć się więcej na temat innych uczestników w kolekcji wywołania inspekcji `remoteParticipant` dla `call` wystąpienia:

```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants();
```

Tożsamość obiektu wywołującego, jeśli wywołanie jest przychodzące:

```java
CommunicationIdentifier callerId = call.getCallerId();
```

Pobierz stan wywołania: 

```java
CallState callState = call.getState();
```

Zwraca ciąg reprezentujący bieżący stan wywołania:
* "Brak" — stan wywołania początkowego
* "Przychodzące" — wskazuje, że wywołanie jest przychodzące, musi zostać zaakceptowane lub odrzucone
* "Łączenie" — stan przejścia początkowego po zainicjowaniu lub zaakceptowaniu wywołania
* "Dzwonienie" — dla wywołania wychodzącego — wskazuje, że wywołanie jest sygnalizowane dla uczestników zdalnych, jest to "przychodzące"
* "EarlyMedia" — wskazuje stan, w którym jest odtwarzany anons przed połączeniem połączenia
* "Połączone" — połączenie jest połączone
* "Hold" — wywołanie jest wstrzymane, żaden nośnik nie przepływa między lokalnym punktem końcowym i uczestnikami zdalnymi
* "Rozłączanie"-stan przejścia przed przejściem do stanu "Rozłączono"
* "Rozłączono" — stan końcowy wywołania


Aby dowiedzieć się, dlaczego wywołanie zostało zakończone, zbadaj `callEndReason` Właściwość. Zawiera kod/podkod: 

```java
CallEndReason callEndReason = call.getCallEndReason();
int code = callEndReason.getCode();
int subCode = callEndReason.getSubCode();
```

Aby sprawdzić, czy bieżące wywołanie jest wywołaniem przychodzącym, zbadaj `isIncoming` Właściwość:

```java
boolean isIncoming = call.getIsIncoming();
```

Aby sprawdzić, czy bieżący mikrofon jest wyciszony, sprawdź `muted` Właściwość:

```java
boolean muted = call.getIsMicrophoneMuted();
```

Aby sprawdzić aktywne strumienie wideo, zapoznaj się z `localVideoStreams` kolekcją:

```java
List<LocalVideoStream> localVideoStreams = call.getLocalVideoStreams();
```

### <a name="mute-and-unmute"></a>Wycisz i Wycisz

Aby wyciszyć lub wyłączyć lokalny punkt końcowy, można użyć `mute` i `unmute` asynchronicznych interfejsów API:

```java
call.mute().get();
call.unmute().get();
```

### <a name="start-and-stop-sending-local-video"></a>Uruchamianie i zatrzymywanie wysyłania lokalnego wideo

Aby uruchomić wideo, należy wyliczyć aparaty fotograficzne przy użyciu `getCameraList` interfejsu API w `deviceManager` obiekcie. Następnie utwórz nowe wystąpienie `LocalVideoStream` przekazujące żądany aparat i przekaż je w `startVideo` interfejsie API jako argument:

```java
VideoDeviceInfo desiredCamera = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(desiredCamera, appContext);
videoOptions = new VideoOptions(currentVideoStream);
Future startVideoFuture = call.startVideo(currentVideoStream);
startVideoFuture.get();
```

Po pomyślnym rozpoczęciu wysyłania wideo `LocalVideoStream` wystąpienie zostanie dodane do `localVideoStreams` kolekcji w wystąpieniu wywołania.

```java
currentVideoStream == call.getLocalVideoStreams().get(0);
```

Aby zatrzymać lokalne wideo, Przekaż `localVideoStream` wystąpienie dostępne w `localVideoStreams` kolekcji:

```java
call.stopVideo(localVideoStream).get();
```

Możesz przełączyć się do innego urządzenia aparatu fotograficznego, gdy wideo jest wysyłane przez wywołanie `switchSource` na `localVideoStream` wystąpienie:
```java
localVideoStream.switchSource(source).get();
```

## <a name="remote-participants-management"></a>Zdalne zarządzanie uczestnikami

Wszyscy uczestnicy zdalni są reprezentowani przez `RemoteParticipant` Typ i są dostępni za pomocą `remoteParticipants` kolekcji w wystąpieniu wywołania.

### <a name="list-participants-in-a-call"></a>Wyświetlanie listy uczestników wywołania
`remoteParticipants`Kolekcja zwraca listę zdalnych uczestników w danym wywołaniu:
```java
List<RemoteParticipant> remoteParticipants = call.getRemoteParticipants(); // [remoteParticipant, remoteParticipant....]
```

### <a name="remote-participant-properties"></a>Właściwości uczestnika zdalnego
Każdy uczestnik zdalny ma skojarzoną z nim zestaw właściwości i kolekcji:

* Pobierz identyfikator dla tego uczestnika zdalnego.
Tożsamość jest jednym z typów identyfikatora
```java
CommunicationIdentifier participantIdentity = remoteParticipant.getIdentifier();
```

* Pobierz stan tego uczestnika zdalnego.
```java
ParticipantState state = remoteParticipant.getState();
```
Stan może być jednym z
* "Bezczynne" — stan początkowy
* "Łączenie" — stan przejścia, gdy uczestnik nawiązuje połączenie z wywołaniem
* "Połączone" — uczestnik jest połączony z wywołaniem
* "Hold" — uczestnik jest wstrzymany
* "EarlyMedia" — anons jest odtwarzany przed połączeniem uczestnika z wywołaniem
* "Rozłączono" — stan końcowy — uczestnik jest odłączony od wywołania


* Aby dowiedzieć się, dlaczego uczestnik pozostawił wywołanie, zbadaj `callEndReason` Właściwość:
```java
CallEndReason callEndReason = remoteParticipant.getCallEndReason();
```

* Aby sprawdzić, czy ten Uczestnik zdalny jest wyciszony, sprawdź `isMuted` Właściwość:
```java
boolean isParticipantMuted = remoteParticipant.getIsMuted();
```

* Aby sprawdzić, czy ten Uczestnik zdalny mówi lub nie, sprawdź `isSpeaking` Właściwość:
```java
boolean isParticipantSpeaking = remoteParticipant.getIsSpeaking();
```

* Aby sprawdzić wszystkie strumienie wideo wysyłane przez danego uczestnika w ramach tego wywołania, sprawdź `videoStreams` kolekcję:
```java
List<RemoteVideoStream> videoStreams = remoteParticipant.getVideoStreams(); // [RemoteVideoStream, RemoteVideoStream, ...]
```


### <a name="add-a-participant-to-a-call"></a>Dodawanie uczestnika do wywołania

Aby dodać uczestnika do wywołania (użytkownika lub numeru telefonu), można wywołać `addParticipant` . Spowoduje to synchroniczne zwrócenie wystąpienia uczestnika zdalnego.

```java
const acsUser = new CommunicationUser("<acs user id>");
const acsPhone = new PhoneNumber("<phone number>");
RemoteParticipant remoteParticipant1 = call.addParticipant(acsUser);
RemoteParticipant remoteParticipant2 = call.addParticipant(acsPhone);
```

### <a name="remove-participant-from-a-call"></a>Usuń uczestnika z wywołania
Aby usunąć uczestnika z wywołania (użytkownika lub numeru telefonu), można wywołać `removeParticipant` .
To rozwiązanie zostanie rozwiązane asynchronicznie po usunięciu uczestnika z wywołania.
Uczestnik zostanie również usunięty z `remoteParticipants` kolekcji.
```java
RemoteParticipant remoteParticipant = call.getParticipants().get(0);
call.removeParticipant(acsUser).get();
call.removeParticipant(acsPhone).get();
```

## <a name="render-remote-participant-video-streams"></a>Renderuj strumienie wideo zdalnego uczestnika
Aby wyświetlić listę strumieni wideo i strumieni udostępniania ekranu dla uczestników zdalnych, należy sprawdzić `videoStreams` Kolekcje:
```java
RemoteParticipant remoteParticipant = call.getRemoteParticipants().get(0);
RemoteVideoStream remoteParticipantStream = remoteParticipant.getVideoStreams().get(0);
MediaStreamType streamType = remoteParticipantStream.getType(); // of type MediaStreamType.Video or MediaStreamType.ScreenSharing
```
 
Aby renderować `RemoteVideoStream` z uczestnika zdalnego, musisz subskrybować `OnVideoStreamsUpdated` wydarzenie.

W przypadku zmiany `isAvailable` właściwości na wartość true wskazuje, że uczestnik zdalny aktualnie wysyła strumień. Po wykonaniu tej czynności Utwórz nowe wystąpienie klasy `Renderer` , a następnie utwórz nowe `RendererView` przy użyciu asynchronicznego `createView` interfejsu API i Dołącz `view.target` dowolne miejsce w interfejsie użytkownika aplikacji.

Zawsze, gdy jest dostępna zmiana strumienia zdalnego, można wybrać opcję zniszczenia całego modułu renderowania, określonych `RendererView` lub zachowywania, ale spowoduje to wyświetlenie pustej ramki wideo.

```java
Renderer remoteVideoRenderer = new Renderer(remoteParticipantStream, appContext);
View uiView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
layout.addView(uiView);

remoteParticipant.addOnVideoStreamsUpdatedListener(e -> onRemoteParticipantVideoStreamsUpdated(p, e));

void onRemoteParticipantVideoStreamsUpdated(RemoteParticipant participant, RemoteVideoStreamsEvent args) {
    for(RemoteVideoStream stream : args.getAddedRemoteVideoStreams()) {
        if(stream.getIsAvailable()) {
            startRenderingVideo();
        } else {
            renderer.dispose();
        }
    }
}
```

### <a name="remote-video-stream-properties"></a>Właściwości zdalnego strumienia wideo
Zdalny strumień wideo zawiera kilka właściwości

* `Id` -Identyfikator zdalnego strumienia wideo
```java
int id = remoteVideoStream.getId();
```

* `MediaStreamType` — Może to być "wideo" lub "ScreenSharing"
```java
MediaStreamType type = remoteVideoStream.getType();
```

* `isAvailable` -Wskazuje, czy zdalny punkt końcowy uczestnika aktywnie wysyła strumień
```java
boolean availability = remoteVideoStream.getIsAvailable();
```

### <a name="renderer-methods-and-properties"></a>Metody i właściwości modułu renderowania
Obiekt renderujący — następujące interfejsy API

* Utwórz `RendererView` wystąpienie, które może zostać później dołączone w interfejsie użytkownika aplikacji, aby renderować zdalny strumień wideo.
```java
// Create a view for a video stream
renderer.createView()
```
* Usuwanie modułu renderowania i wszystkich `RendererView` skojarzonych z nim modułu renderowania
```java
renderer.dispose()
```

* `StreamSize` -size (szerokość/wysokość) zdalnego strumienia wideo
```java
StreamSize renderStreamSize = remoteVideoStream.getSize();
int width = renderStreamSize.getWidth();
int height = renderStreamSize.getHeight();
```


### <a name="rendererview-methods-and-properties"></a>RendererView — metody i właściwości
Podczas tworzenia można `RendererView` określić `scalingMode` `mirrored` właściwości i, które będą stosowane do tego widoku: tryb skalowania może mieć wartość "Rozciągnij" | "Przytnij" | "Dopasuj" `mirrored` , jeśli jest ustawiona na `true` , renderowane strumienie zostanie przerzucone w pionie.

```java
Renderer remoteVideoRenderer = new Renderer(remoteVideoStream, appContext);
RendererView rendererView = remoteVideoRenderer.createView(new RenderingOptions(ScalingMode.Fit));
```

Utworzony RendererView można następnie dołączyć do interfejsu użytkownika aplikacji przy użyciu następującego fragmentu kodu:
```java
layout.addView(rendererView);
```

Można później zaktualizować tryb skalowania poprzez wywoływanie `updateScalingMode` interfejsu API w obiekcie RendererView z jednym z elementów skalowaćmode. rozciąganie | Skalowanie. Crop | Skalowanie. Dopasuj jako argument.
```java
// Update the scale mode for this view.
rendererView.updateScalingMode(ScalingMode.Crop)
```


## <a name="device-management"></a>Zarządzanie urządzeniami

`DeviceManager` umożliwia Wyliczenie urządzeń lokalnych, które mogą być używane w wywołaniu przesyłania strumieni audio/wideo. Umożliwia także zażądanie uprawnień od użytkownika w celu uzyskania dostępu do mikrofonu i aparatu przy użyciu natywnego interfejsu API przeglądarki.

Dostęp można uzyskać `deviceManager` za pomocą `callClient.getDeviceManager()` metody wywołującej.
> [!WARNING]
> Obecnie `callAgent` przed uzyskaniem dostępu do elementu devicemanager należy najpierw utworzyć wystąpienie obiektu.

```java
DeviceManager deviceManager = callClient.getDeviceManager().get();
```

### <a name="enumerate-local-devices"></a>Wyliczanie urządzeń lokalnych

Aby uzyskać dostęp do urządzeń lokalnych, można użyć metod wyliczania na Menedżer urządzeń. Wyliczenie jest akcją synchroniczną.

```java
//  Get a list of available video devices for use.
List<VideoDeviceInfo> localCameras = deviceManager.getCameraList(); // [VideoDeviceInfo, VideoDeviceInfo...]

// Get a list of available microphone devices for use.
List<AudioDeviceInfo> localMicrophones = deviceManager.getMicrophoneList(); // [AudioDeviceInfo, AudioDeviceInfo...]

// Get a list of available speaker devices for use.
List<AudioDeviceInfo> localSpeakers = deviceManager.getSpeakerList(); // [AudioDeviceInfo, AudioDeviceInfo...]
```

### <a name="set-default-microphonespeaker"></a>Ustaw domyślny Mikrofon/głośnik

Menedżer urządzeń umożliwia ustawienie domyślnego urządzenia, które będzie używane podczas uruchamiania wywołania.
Jeśli wartości domyślne klienta nie są ustawione, usługi komunikacyjne powracają do domyślnych ustawień systemu operacyjnego.

```java

// Get the microphone device that is being used.
AudioDeviceInfo defaultMicrophone = deviceManager.getMicrophoneList().get(0);

// Set the microphone device to use.
deviceManager.setMicrophone(defaultMicrophone);

// Get the speaker device that is being used.
AudioDeviceInfo defaultSpeaker = deviceManager.getSpeakerList().get(0);

// Set the speaker device to use.
deviceManager.setSpeaker(defaultSpeaker);
```

### <a name="local-camera-preview"></a>Podgląd lokalnego aparatu fotograficznego

Możesz użyć `DeviceManager` i, `Renderer` Aby rozpocząć renderowanie strumieni z aparatu lokalnego. Ten strumień nie zostanie wysłany do innych uczestników; jest to lokalna wersja zapoznawcza. Jest to akcja asynchroniczna.

```java
VideoDeviceInfo videoDevice = <get-video-device>;
Context appContext = this.getApplicationContext();
currentVideoStream = new LocalVideoStream(videoDevice, appContext);
videoOptions = new VideoOptions(currentVideoStream);

Renderer previewRenderer = new Renderer(currentVideoStream, appContext);
View uiView previewRenderer.createView(new RenderingOptions(ScalingMode.Fit));

// Attach the renderingSurface to a viewable location on the app at this point
layout.addView(uiView);
```

## <a name="eventing-model"></a>Model zdarzeń
Aby otrzymywać powiadomienia o zmianach wartości, można subskrybować większość właściwości i kolekcji.

### <a name="properties"></a>Właściwości
Aby subskrybować `property changed` zdarzenia:

```java
// subscribe
PropertyChangedListener callStateChangeListener = new PropertyChangedListener()
{
    @Override
    public void onPropertyChanged(PropertyChangedEvent args)
    {
        Log.d("The call state has changed.");
    }
}
call.addOnCallStateChangedListener(callStateChangeListener);

//unsubscribe
call.removeOnCallStateChangedListener(callStateChangeListener);
```

### <a name="collections"></a>Kolekcje
Aby subskrybować `collection updated` zdarzenia:

```java
LocalVideoStreamsChangedListener localVideoStreamsChangedListener = new LocalVideoStreamsChangedListener()
{
    @Override
    public void onLocalVideoStreamsUpdated(LocalVideoStreamsEvent localVideoStreamsEventArgs) {
        Log.d(localVideoStreamsEventArgs.getAddedStreams().size());
        Log.d(localVideoStreamsEventArgs.getRemovedStreams().size());
    }
}
call.addOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
// To unsubscribe
call.removeOnLocalVideoStreamsChangedListener(localVideoStreamsChangedListener);
```
