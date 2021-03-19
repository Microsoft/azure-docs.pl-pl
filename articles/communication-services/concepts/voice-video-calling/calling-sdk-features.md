---
title: Omówienie wywoływania biblioteki klienta usługi Azure Communication Services
titleSuffix: An Azure Communication Services concept document
description: Zawiera omówienie biblioteki klienta wywołującego.
author: mikben
manager: jken
services: azure-communication-services
ms.author: mikben
ms.date: 03/10/2021
ms.topic: overview
ms.service: azure-communication-services
ms.openlocfilehash: e154e43f9e9378d6cccd23e2e5892f2a8ccf9a1e
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104598885"
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
|  Środkowy formant wywołania | Włącz/Wyłącz wideo                                                                                              | ✔️   | ✔️            | ✔️ 
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
| Ogólne           | Testowanie mikrofonu, głośników i aparatu za pomocą usługi testowania audio (dostępnej przez wywołanie 8: echo123)                   | ✔️   | ✔️            | ✔️ 
| Zarządzanie urządzeniami | Poproszenie o zgodę na korzystanie z audio i/lub wideo                                                                       | ✔️   | ✔️            | ✔️
|                   | Pobierz listę kamer                                                                                                     | ✔️   | ✔️            | ✔️ 
|                   | Ustaw kamerę                                                                                                          | ✔️   | ✔️            | ✔️
|                   | Pobierz wybrany aparat                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Pobierz listę mikrofonu                                                                                                 | ✔️   | ✔️            | ✔️
|                   | Ustaw mikrofon                                                                                                      | ✔️   | ✔️            | ✔️
|                   | Pobierz wybrany mikrofon                                                                                             | ✔️   | ✔️            | ✔️
|                   | Pobierz listę głośników                                                                                                   | ✔️   | ✔️            | ✔️
|                   | Ustaw prelegenta                                                                                                         | ✔️   | ✔️            | ✔️
|                   | Pobierz wybranego głośnika                                                                                                | ✔️   | ✔️            | ✔️
| Renderowanie wideo   | Renderuj pojedyncze wideo w wielu miejscach (lokalny aparat fotograficzny lub zdalny strumień)                                                  | ✔️   | ✔️            | ✔️
|                   | Ustawianie/aktualizowanie trybu skalowania                                                                                           | ✔️   | ✔️            | ✔️ 
|                   | Renderowanie zdalnego strumienia wideo                                                                                          | ✔️   | ✔️            | ✔️



## <a name="javascript-calling-client-library-support-by-os-and-browser"></a>Obsługa języka JavaScript wywoływana przez system operacyjny i przeglądarkę

Poniższa tabela przedstawia zestaw obsługiwanych przeglądarek, które są obecnie dostępne. Obsługujemy najnowsze trzy wersje przeglądarki, chyba że wskazano inaczej.

|                                  | Chrome | Safari  | Krawędź (chrom) | 
| -------------------------------- | -------| ------  | --------------  |
| Android                          |  ✔️    | ❌     | ❌             |
| iOS                              |  ❌    | ✔️**** | ❌             |
| macOS * * *                         |  ✔️    | ✔️**   | ❌             |
| Windows * * *                       |  ✔️    | ❌     | ✔️             |
| Ubuntu/Linux                     |  ✔️    | ❌     | ❌             |

* Obsługiwane są wersje Safari 13.1 +, 1:1 wywołania nie są obsługiwane w przeglądarce Safari. 

* * Przeglądarka Safari 14 +/macOS 11 + wymagana do obsługi wychodzącego wideo. 

Udostępnianie ekranu wychodzącego jest obsługiwane tylko na platformach klasycznych (Windows, macOS i Linux), niezależnie od wersji przeglądarki i nie jest obsługiwane przez żadną platformę mobilną (Android, iOS, iPad i tablety).

Aplikacja dla systemu iOS w przeglądarce Safari nie może wyliczyć/wybrać urządzeń z mikrofonu i głośników (na przykład Bluetooth); jest to ograniczenie systemu operacyjnego i istnieje zawsze tylko jedno urządzenie.


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
|**Liczba strumieni wychodzących, które mogą być wysyłane jednocześnie** |1 udostępnianie wideo lub 1 ekran | 1 wideo + 1 udostępnianie ekranu|
|**Liczba strumieni przychodzących, które mogą być renderowane jednocześnie** |1 udostępnianie wideo lub 1 ekran| 6 wideo + 1 udostępnianie ekranu |


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wprowadzenie do wywoływania](../../quickstarts/voice-video-calling/getting-started-with-calling.md)

Aby uzyskać więcej informacji, zobacz następujące artykuły:
- Zapoznaj się z ogólnymi [przepływami wywołań](../call-flows.md)
- Informacje o [typach wywołań](../voice-video-calling/about-call-types.md)
- [Planowanie rozwiązania PSTN](../telephony-sms/plan-solution.md)
