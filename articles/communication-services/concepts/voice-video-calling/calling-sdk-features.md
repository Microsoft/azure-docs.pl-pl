---
title: Omówienie wywoływania biblioteki klienta usługi Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Zawiera omówienie biblioteki klienta wywołującego.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 09/30/2020
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: 666474b7c7b823114b6dee2925ed5f81973e212d
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100650937"
---
# <a name="calling-client-library-overview"></a>Omówienie biblioteki klienta połączeń

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Istnieją dwie oddzielne rodziny wywołujących biblioteki klienckie dla *klientów* i *usług.* Obecnie dostępne biblioteki klienckie są przeznaczone dla środowiska użytkownika końcowego: witryny sieci Web i aplikacje natywne.

Biblioteki klienta usługi nie są jeszcze dostępne i zapewniają dostęp do niesformatowanych płaszczyzn danych dźwiękowych i wideo, które są odpowiednie do integracji z botów i innymi usługami.

## <a name="calling-client-library-capabilities"></a>Wywoływanie możliwości biblioteki klienta

Na poniższej liście przedstawiono zestaw funkcji, które są obecnie dostępne w bibliotekach klienta wywołujących usługi komunikacyjne Azure.

| Grupa funkcji | Możliwość                                                                                                          | JS  | Java (Android) | Objective-C (iOS) 
| ----------------- | ------------------------------------------------------------------------------------------------------------------- | ---  | -------------- | -------------
| Podstawowe możliwości | Umieszczanie wywołania jeden-do-jednego między dwoma użytkownikami                                                                           | ✔️   | ✔️            | ✔️  
|                   | Umieść połączenie grupy z więcej niż dwoma użytkownikami (do 350 użytkowników)                                                       | ✔️   | ✔️            | ✔️ 
|                   | Podnieś poziom wywołania "jeden do jednego" z dwoma użytkownikami do wywołania grupy z więcej niż dwoma użytkownikami                                 | ✔️   | ✔️            | ✔️ 
|                   | Dołącz do wywołania grupy po jego rozpoczęciu                                                                              | ✔️   | ✔️            | ✔️ 
|                   | Zapraszanie innego uczestnika VoIP do dołączenia do trwającego wywołania grupy                                                       | ✔️   | ✔️            | ✔️
|                   | Włącz/Wyłącz wideo                                                         | ✔️   | ✔️            | ✔️ 
|                   | Wycisz/Wyłącz mikrofon                                                                                                     | ✔️   | ✔️            | ✔️         
|                   | Przełączanie między kamerami                                                                                              | ✔️   | ✔️            | ✔️           
|                   | Blokada lokalna/wstrzymanie                                                                                                  | ✔️   | ✔️            | ✔️           
|                   | Aktywny głośnik                                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Wybieranie osoby mówiącej dla wywołań                                                                                            | ✔️   | ✔️            | ✔️           
|                   | Wybieranie mikrofonu dla wywołań                                                                                         | ✔️   | ✔️            | ✔️           
|                   | Pokaż stan uczestnika<br/>*Bezczynne, wczesne nośniki, łączenie, połączone, wstrzymane, w poczekalni, rozłączone*         | ✔️   | ✔️            | ✔️           
|                   | Pokaż stan wywołania<br/>*Wczesny nośnik, przychodzący, łączący, dzwonienie, połączenie, wstrzymanie, rozłączanie, Rozłączono* | ✔️   | ✔️            | ✔️           
|                   | Pokaż, jeśli uczestnik jest wyciszony                                                                                      | ✔️   | ✔️            | ✔️           
|                   | Pokaż powód, dla którego Uczestnik pozostawił wywołanie                                                                       | ✔️   | ✔️            | ✔️     
| Udostępnianie ekranu    | Udostępnianie całego ekranu z poziomu aplikacji                                                                 | ✔️   | ❌            | ❌           
|                   | Udostępnianie określonej aplikacji (z listy uruchomionych aplikacji)                                                | ✔️   | ❌            | ❌           
|                   | Udostępnianie karty przeglądarki sieci Web z listy otwartych kart                                                                  | ✔️   | ❌            | ❌           
|                   | Uczestnik może wyświetlić udział ekranu zdalnego                                                                            | ✔️   | ✔️            | ✔️         
| Spis            | Wyświetl listę uczestników                                                                                                   | ✔️   | ✔️            | ✔️           
|                   | Usuwanie uczestnika                                                                                                | ✔️   | ✔️            | ✔️         
| PSTN              | Umieszczanie wywołania "jeden do jednego" z uczestnikiem sieci PSTN                                                                     | ✔️   | ✔️            | ✔️   
|                   | Umieść połączenie grupy z uczestnikami PSTN                                                                           | ✔️   | ✔️            | ✔️
|                   | Podwyższanie poziomu wywołania "jeden do jednego" uczestnika PSTN do wywołania grupy                                                 | ✔️   | ✔️            | ✔️
|                   | Wybieranie numeru z wywołania grupy jako uczestnika sieci PSTN                                                                    | ✔️   | ✔️            | ✔️   
| Ogólne           | Testowanie mikrofonu, głośników i aparatu za pomocą usługi testowania audio (dostępnej przez wywołanie 8: echo123)                   |  ✔️  | ✔️            | ✔️   

## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>Obsługa języka JavaScript wywoływana przez system operacyjny i przeglądarkę

Poniższa tabela przedstawia zestaw obsługiwanych przeglądarek i wersji, które są obecnie dostępne.

|                                  | Windows          | macOS          | Ubuntu | Linux  | Android | iOS    | system operacyjny iPad|
| -------------------------------- | ---------------- | -------------- | ------- | ------ | ------ | ------ | -------|
| **Wywoływanie biblioteki klienta** | Chrome *, Nowa krawędź | Chrome *, Safari** | Własnego  | Własnego | Własnego | Safari * * | Safari * * |


* Zwróć uwagę, że oprócz poprzednich dwóch wydań jest obsługiwana Najnowsza wersja programu Chrome.<br/>

* * Należy zauważyć, że obsługiwane są wersje Safari 13.1 +. Wychodzące wideo dla programu Safari macOS nie jest jeszcze obsługiwane, ale jest obsługiwane w systemie iOS. Udostępnianie ekranu wychodzącego jest obsługiwane tylko na komputerze stacjonarnym z systemem iOS. 1:1 i wywołania grupowe nie są obecnie dostępne w przeglądarce Safari.

## <a name="calling-client---browser-security-model"></a>Wywoływanie modelu zabezpieczeń przeglądarki klienta

### <a name="user-webrtc-over-https"></a>Protokołu WebRTC użytkownika za pośrednictwem protokołu HTTPS

Interfejsy API protokołu WebRTC, takie jak `getUserMedia` Wymagaj, że aplikacja, która wywołuje te interfejsy API, jest obsługiwana za pośrednictwem protokołu HTTPS.

W przypadku lokalnego programowania można użyć programu `http://localhost` .

### <a name="embed-the-communication-services-calling-sdk-in-an-iframe"></a>Osadzanie zestawu SDK wywoływania usług komunikacyjnych w elemencie iframe

Nowe [zasady uprawnień (nazywane również zasadami funkcji)](https://www.w3.org/TR/permissions-policy-1/#iframe-allow-attribute) są wprowadzane przez różne przeglądarki. Te zasady wpływają na wywołania scenariuszy poprzez kontrolowanie sposobu, w jaki aplikacje mogą uzyskiwać dostęp do aparatu i mikrofonu urządzenia za pomocą elementu iframe między źródłami.

Jeśli chcesz użyć elementu IFRAME do hostowania części aplikacji z innej domeny, musisz dodać `allow` atrybut z poprawną wartością do elementu iframe.

Na przykład ten element IFRAME umożliwia dostęp do aparatu i mikrofonu:

```html
<iframe allow="camera *; microphone *">
```

## <a name="calling-client-library-streaming-support"></a>Wywoływanie obsługi przesyłania strumieniowego biblioteki klienta
Biblioteka klienta wywołującego usługi komunikacyjne obsługuje następujące konfiguracje przesyłania strumieniowego:

|           |Internet | System Android/iOS|
|-----------|----|------------|
|**Liczba strumieni wychodzących, które mogą być wysyłane jednocześnie** |1 udostępnianie plików audio/wideo lub 1 | 1 dźwięk/wideo | 
|**Liczba strumieni przychodzących, które mogą być renderowane jednocześnie** |1 udostępnianie plików audio/wideo lub 1| 6 audio/wideo lub 1 udostępnianie ekranu |

Należy pamiętać, że w scenariuszach grupy jeden mieszany strumień audio jest używany do obsługi wszystkich uczestników audio.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wywoływania](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- Zapoznaj się z ogólnymi [przepływami wywołań](../call-flows.md) 
- Informacje o [typach wywołań](../voice-video-calling/about-call-types.md)
- Dowiedz się więcej o [typach numerów telefonów](../telephony-sms/plan-solution.md)
