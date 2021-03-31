---
title: Rozwiązywanie problemów z konfiguracją Standard/Premium na platformie Azure
description: W tym samouczku dowiesz się, jak rozwiązywać problemy z typowymi problemami, które mogą być używane w przypadku wystąpienia Standard/Premium na platformie Azure.
services: frontdoor
author: duongau
ms.service: frontdoor
ms.topic: how-to
ms.date: 02/18/2021
ms.author: qixwang
ms.openlocfilehash: 4690a513494d794377ee0c2e8cfb101e8fd66a0f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101100201"
---
# <a name="troubleshooting-common-routing-problems-with-azure-front-door-standardpremium"></a>Rozwiązywanie typowych problemów z routingiem przy użyciu standardu Front/Premium platformy Azure

W tym artykule opisano sposób rozwiązywania typowych problemów z routingiem, które mogą być potrzebne w konfiguracji drzwi platformy Azure.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 odpowiedzi z czołowych drzwi platformy Azure po kilku sekundach

### <a name="symptom"></a>Objaw

* Regularne żądania wysyłane do zaplecza bez przechodzenia przez frontony platformy Azure kończą się powodzeniem. Przechodzenie przez czołowe drzwi platformy Azure w 503 odpowiedzi na błędy.
* Awaria z usług frontonu platformy Azure zwykle jest wyświetlana po około 30 sekundach.

### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być jedna z dwóch rzeczy:
 
* Pochodzenie trwa dłużej niż skonfigurowany limit czasu (wartość domyślna to 30 sekund), aby otrzymać żądanie od zewnętrznych drzwi platformy Azure.
* Czas potrzebny na wysłanie odpowiedzi na żądanie z usługi Azure Front Drzwiczks trwa dłużej niż wartość limitu czasu. 

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

* Wyślij żądanie bezpośrednio do zaplecza (bez przechodzenia przez fronton platformy Azure). Zobacz, jak długo zaplecze zwykle odpowiada.
* Wyślij żądanie za pośrednictwem drzwi platformy Azure i sprawdź, czy otrzymujesz odpowiedzi na 503. Jeśli nie, problem może nie być problemem z limitem czasu. Skontaktuj się z pomocą techniczną.
* Jeśli przechodzenie między frontonem platformy Azure spowoduje wyświetlenie kodu błędu 503, należy skonfigurować `sendReceiveTimeout` pole dla drzwi frontonu platformy Azure. Domyślny limit czasu można zwiększyć do 4 minut (240 sekund). Ustawienie jest w obszarze `Endpoint Setting` i jest wywoływane `Origin response timeout` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Żądania wysyłane do domeny niestandardowej zwracają kod stanu 400

### <a name="symptom"></a>Objaw

* Utworzono wystąpienie drzwi platformy Azure, ale żądanie do domeny lub hosta frontonu zwraca kod stanu HTTP 400.
* Utworzono mapowanie DNS dla domeny niestandardowej na skonfigurowany Host frontonu. Jednak wysłanie żądania do nazwy hosta domeny niestandardowej zwraca kod stanu HTTP 400. Nie wydaje się kierować do skonfigurowanej wewnętrznej bazy danych.

### <a name="cause"></a>Przyczyna

Problem występuje, jeśli nie skonfigurowano reguły routingu dla domeny niestandardowej, która została dodana jako host frontonu. Należy jawnie dodać regułę routingu dla tego hosta frontonu. Jest to prawdziwe, nawet jeśli reguła routingu została już skonfigurowana dla hosta frontonu w poddomenie "przednim" platformy Azure (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Dodaj regułę routingu dla domeny niestandardowej, aby skierować ruch do wybranej grupy pochodzenia.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Drzwi frontonu platformy Azure nie przekierowują protokołu HTTP do protokołu HTTPS

### <a name="symptom"></a>Objaw

Drzwi frontonu platformy Azure mają regułę routingu, która przekierowuje protokół HTTP do protokołu HTTPS, ale dostęp do domeny nadal utrzymuje protokół HTTP.

### <a name="cause"></a>Przyczyna

Taka sytuacja może wystąpić, jeśli reguły routingu nie zostały prawidłowo skonfigurowane dla drzwi platformy Azure. Zasadniczo Bieżąca konfiguracja nie jest określona i może mieć sprzeczne reguły.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów


## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Żądanie do nazwy hosta frontonu zwraca kod stanu 411

### <a name="symptom"></a>Objaw

Utworzono wystąpienie usługi Azure Front-Standard/Premium i skonfigurowano hosta frontonu, grupę źródłową z co najmniej jednym źródłem oraz regułę routingu łączącą hosta frontonu z grupą pierwotną. Zawartość nie jest dostępna, gdy żądanie przejdzie do skonfigurowanego hosta frontonu, ponieważ zwracany jest kod stanu HTTP 411.

Odpowiedzi na te żądania mogą również zawierać stronę błędu HTML w treści odpowiedzi zawierającej instrukcję wyjaśniającą. Na przykład: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Przyczyna

Istnieje kilka możliwych przyczyn tego symptomu. Ogólną przyczyną jest to, że żądanie HTTP nie jest w pełni zgodne ze standardem RFC. 

Przykładem braku zgodności jest `POST` żądanie wysyłane bez `Content-Length` ani `Transfer-Encoding` nagłówka (na przykład przy użyciu `curl -X POST https://example-front-door.domain.com` ). To żądanie nie spełnia wymagań określonych w [dokumencie RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Drzwi frontonu platformy Azure blokują ją z odpowiedzią HTTP 411.

To zachowanie jest niezależne od funkcjonalności zapory aplikacji sieci Web (WAF) platformy Azure. Obecnie nie ma możliwości wyłączenia tego zachowania. Wszystkie żądania HTTP muszą spełniać wymagania, nawet jeśli funkcja WAF nie jest używana.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

- Sprawdź, czy Twoje żądania są zgodne z wymaganiami określonymi w niezbędnych dokumentach RFC.

- Zanotuj wszelkie treści wiadomości HTML, które są zwracane w odpowiedzi na żądanie. Treść komunikatu często wyjaśnia dokładnie, *jak* żądanie jest niezgodne.

## <a name="next-steps"></a>Następne kroki

Dowiedz się [, jak utworzyć warstwę przednią/standardową](create-front-door-portal.md).
