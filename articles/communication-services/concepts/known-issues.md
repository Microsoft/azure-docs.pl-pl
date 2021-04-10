---
title: Usługi komunikacyjne platformy Azure — znane problemy
description: Dowiedz się więcej o usłudze Azure Communications Services
author: rinarish
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: troubleshooting
ms.service: azure-communication-services
ms.openlocfilehash: b9ed71a8fc9346ecd454eba98dcbb3b13186eba2
ms.sourcegitcommit: 02bc06155692213ef031f049f5dcf4c418e9f509
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/03/2021
ms.locfileid: "106276046"
---
# <a name="known-issues-azure-communication-services-calling-sdks"></a>Znane problemy: usługi komunikacyjne Azure wywołujące zestawy SDK
Ten artykuł zawiera informacje o ograniczeniach i znanych problemach związanych z zestawami SDK wywołujących usługi Azure Communications Services.

> [!IMPORTANT]
> Istnieje wiele czynników, które mogą mieć wpływ na jakość wywoływania. Zapoznaj się z dokumentacją **[dotyczącą wymagań sieciowych](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements)** , aby dowiedzieć się więcej na temat konfiguracji sieci usług komunikacyjnych i najlepszych rozwiązań dotyczących testowania.


## <a name="javascript-sdk"></a>Zestaw SDK dla języka JavaScript

Ta sekcja zawiera informacje o znanych problemach związanych z zestawem SDK dla głosu i wideo dla usług Azure Communication Services.

### <a name="refreshing-a-page-doesnt-immediately-remove-the-user-from-their-call"></a>Odświeżenie strony nie powoduje natychmiastowego usunięcia użytkownika z wywołania

Jeśli użytkownik jest w wywołaniu i zdecyduje się odświeżyć stronę, Usługa multimediów usług komunikacyjnych nie usunie tego użytkownika bezpośrednio z wywołania. Poczeka na ponowne dołączenie użytkownika. Użytkownik zostanie usunięty z wywołania po upływie limitu czasu usługi multimedialnej.

Najlepszym rozwiązaniem jest skompilowanie środowiska użytkownika, które nie wymagają od użytkowników końcowych odświeżania strony aplikacji podczas wywołania. Jeśli użytkownik Odświeża stronę, należy ponownie użyć tego samego identyfikatora użytkownika usług komunikacyjnych po powrocie z powrotem do aplikacji.

Od perspektywy innych uczestników wywołania użytkownik pozostanie w wywołaniu przez okres czasu (1-2 minut). Jeśli użytkownik ponownie przyłączy się przy użyciu tego samego identyfikatora użytkownika usług komunikacyjnych, będzie reprezentowany jako ten sam istniejący obiekt w `remoteParticipants` kolekcji.

Jeśli Użytkownik wysłał wideo przed odświeżeniem, `videoStreams` kolekcja będzie utrzymywać poprzednie informacje o strumieniu do momentu przełączenia usługi i usunięcia jej. W tym scenariuszu aplikacja może zdecydować się na obserwowanie wszelkich nowych strumieni dodanych do kolekcji i renderowanie ich przy użyciu najwyższej `id` . 


### <a name="its-not-possible-to-render-multiple-previews-from-multiple-devices-on-web"></a>Nie jest możliwe renderowanie wielu podglądów z wielu urządzeń w sieci Web
Jest to znane ograniczenie. Aby uzyskać więcej informacji, zobacz [Omówienie WYWOŁYWANIA SDK](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/calling-sdk-features).

### <a name="enumerating-devices-isnt-possible-in-safari-when-the-application-runs-on-ios-or-ipados"></a>Wyliczanie urządzeń w przeglądarce Safari nie jest możliwe, gdy aplikacja działa w systemie iOS lub iPadOS

Aplikacje nie mogą wyliczyć/wybrać urządzeń MIC/prelegenta (takich jak Bluetooth) w systemie iOS/iPad. Jest to znane ograniczenie systemu operacyjnego.

Jeśli korzystasz z przeglądarki Safari w systemie macOS, aplikacja nie będzie mogła wyliczać ani wybierać głośników za pośrednictwem usług komunikacyjnych Device Manager. W tym scenariuszu urządzenia muszą być wybierane za pośrednictwem systemu operacyjnego. Jeśli używasz programu Chrome w systemie macOS, aplikacja będzie mogła wyliczyć/wybrać urządzenia za pomocą Device Manager usług komunikacyjnych.

### <a name="audio-connectivity-is-lost-when-receiving-sms-messages-or-calls-during-an-ongoing-voip-call"></a>Połączenie audio jest tracone podczas otrzymywania wiadomości SMS lub wywołań podczas trwającego wywołania usługi VoIP
Ten problem może wystąpić z kilku powodów:

- Niektóre przeglądarki dla urządzeń przenośnych nie utrzymują łączności w tle. Może to prowadzić do pogorszenia wydajności połączeń, jeśli wywołanie VoIP zostało przerwane przez zdarzenie, które wypycha aplikację do tła. 
- Czasami wywołanie programu SMS lub PSTN przechwytuje dźwięk audio i nie zwalnia dźwięku z powrotem do wywołania VoIP. Firma Apple rozwiązała ten problem w systemie iOS w wersji 14.4.1 +. 

<br/>Biblioteka kliencka: wywoływanie (JavaScript)
<br/>Przeglądarki: Safari, Chrome
<br/>System operacyjny: iOS, Android

### <a name="repeatedly-switching-video-devices-may-cause-video-streaming-to-temporarily-stop"></a>Wielokrotne przełączanie urządzeń wideo może spowodować tymczasowe zatrzymanie przesyłania strumieniowego wideo

Przełączenie między urządzeniami wideo może spowodować wstrzymanie strumienia wideo w czasie, gdy strumień zostanie pobrany z wybranego urządzenia.

#### <a name="possible-causes"></a>Możliwe przyczyny
Przełączenie między urządzeniami często może spowodować spadek wydajności. Deweloperzy są zachęcani do zatrzymania jednego strumienia urządzenia przed rozpoczęciem kolejnego.

### <a name="bluetooth-headset-microphone-is-not-detected-therefore-is-not-audible-during-the-call-on-safari-on-ios"></a>Nie wykryto mikrofonu z zestawem słuchawkowym Bluetooth
Zestawy słuchawkowe Bluetooth nie są obsługiwane przez przeglądarkę Safari w systemie iOS. Urządzenie Bluetooth nie zostanie wyświetlone na liście dostępnych opcji mikrofonu, a inni uczestnicy nie będą mogli Cię słyszeć w przypadku próby użycia połączenia Bluetooth przez przeglądarkę Safari.

#### <a name="possible-causes"></a>Możliwe przyczyny
Jest to znane ograniczenie systemu operacyjnego macOS/iOS/iPadOS. 

Korzystając z przeglądarki Safari w systemach **macOS** i **iOS/iPadOS**, nie jest możliwe wyliczanie/wybieranie urządzeń prelegenta za pomocą usług komunikacyjnych, Device Manager ponieważ Wyliczenie głośników/zaznaczenie nie jest obsługiwane przez przeglądarkę Safari. W tym scenariuszu wybór urządzenia powinien zostać zaktualizowany za pośrednictwem systemu operacyjnego.

### <a name="rotation-of-a-device-can-create-poor-video-quality"></a>Rotacja urządzenia może spowodować słabą jakość wideo
Gdy urządzenia są obracane, użytkownicy mogą mieć obniżoną jakość wideo.

<br/>Uwzględnione urządzenia: Google Pixel 5, Google Pixel 3A, Apple iPad 8 i Apple iPad X
<br/>Biblioteka kliencka: wywoływanie (JavaScript)
<br/>Przeglądarki: Safari, Chrome
<br/>System operacyjny: iOS, Android


### <a name="camera-switching-makes-the-screen-freeze"></a>Przełączenie aparatu powoduje zablokowanie ekranu 
Gdy użytkownik usług komunikacyjnych dołącza do wywołania przy użyciu zestawu SDK wywołującego JavaScript, a następnie trafi przycisk przełącznika aparatu fotograficznego, interfejs użytkownika może przestać odpowiadać, dopóki aplikacja nie zostanie odświeżona lub przeglądarka nie zostanie wypchnięte do tła przez użytkownika.

<br/>Uwzględnione urządzenia: Google Pixel 4a
<br/>Biblioteka kliencka: wywoływanie (JavaScript)
<br/>Przeglądarki: Chrome
<br/>System operacyjny: iOS, Android


#### <a name="possible-causes"></a>Możliwe przyczyny
W trakcie badania.

### <a name="if-the-video-signal-was-stopped-while-the-call-is-in-connecting-state-the-video-will-not-be-sent-after-the-call-started"></a>Jeśli sygnał wideo został zatrzymany, gdy wywołanie jest w stanie "łączenie", wideo nie zostanie wysłane po rozpoczęciu wywołania 
Jeśli użytkownicy zdecydują się szybko włączyć/wyłączyć wideo, gdy wywołanie jest w `Connecting` stanie — może to prowadzić do problemu ze strumieniem uzyskanym dla wywołania. Zachęcamy deweloperów do kompilowania aplikacji w taki sposób, że nie wymaga to włączania i wyłączania wideo, gdy wywołanie jest w `Connecting` stanie. Przyczyną tego problemu może być spadek wydajności wideo w następujących scenariuszach:

 - Jeśli użytkownik rozpoczyna pracę od dźwięk, a następnie uruchomi i zatrzyma wideo, gdy wywołanie jest w `Connecting` stanie.
 - Jeśli użytkownik rozpoczyna pracę od dźwięk, a następnie uruchomi i zatrzyma wideo, gdy wywołanie jest w `Lobby` stanie.

#### <a name="possible-causes"></a>Możliwe przyczyny
W trakcie badania.

### <a name="enumeratingaccessing-devices-for-safari-on-macos-and-ios"></a>Wyliczanie/uzyskiwanie dostępu do urządzeń dla przeglądarki Safari w systemach MacOS i iOS 
Jeśli dostęp do urządzeń zostanie udzielony po pewnym czasie, uprawnienia urządzenia są resetowane. Przeglądarka Safari w systemach MacOS i iOS nie zachowuje uprawnień przez dłuższy czas, chyba że zostanie pobrany strumień. Najprostszym sposobem obejścia tego problemu jest wywoływanie interfejsu API devicemanager. askDevicePermission () przed wywołaniem interfejsów API wyliczania urządzeń Menedżera urządzeń (devicemanager. getcameras (), devicemanager. getgłośnikis () i devicemanager. getmicrophones ()). Jeśli w tym miejscu znajdują się uprawnienia, użytkownik nie będzie widział żadnych elementów, a jeśli nie, zostanie ponownie wyświetlony monit.

<br/>Urządzenia, których to dotyczy: iPhone
<br/>Biblioteka kliencka: wywoływanie (JavaScript)
<br/>Przeglądarki: Safari
<br/>System operacyjny: iOS

###  <a name="sometimes-it-takes-a-long-time-to-render-remote-participant-videos"></a>Czasami renderowanie zdalnych filmów wideo trwa zbyt długo
Podczas trwającego wywołania grupy _użytkownik a_ wysyła wideo, a następnie _użytkownik B_ dołącza wywołanie. Czasami użytkownik B nie widzi wideo od użytkownika A lub użytkownik A wideo rozpoczyna renderowanie po długim opóźnieniu. Przyczyną tego problemu może być środowisko sieciowe, które wymaga dalszej konfiguracji. Wskazówki dotyczące konfiguracji sieci znajdują się w dokumentacji [dotyczącej wymagań sieciowych](https://docs.microsoft.com/azure/communication-services/concepts/voice-video-calling/network-requirements) .
