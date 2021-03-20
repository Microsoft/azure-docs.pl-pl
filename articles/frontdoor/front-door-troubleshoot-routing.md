---
title: Rozwiązywanie problemów z konfiguracją z przodu platformy Azure
description: W ramach tego samouczka nauczysz się, jak rozwiązywać problemy z niektórymi typowymi problemami, które mogą być używane w wystąpieniu usługi Azure front-drzwi.
services: frontdoor
documentationcenter: ''
author: duongau
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/30/2020
ms.author: duau
ms.openlocfilehash: 15cdcefe628a392704e650b560243e2f6a134ec2
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94629992"
---
# <a name="troubleshooting-common-routing-problems"></a>Rozwiązywanie typowych problemów z routingiem

W tym artykule opisano sposób rozwiązywania typowych problemów z routingiem, które mogą być potrzebne w konfiguracji drzwi platformy Azure.

## <a name="503-response-from-azure-front-door-after-a-few-seconds"></a>503 odpowiedzi z czołowych drzwi platformy Azure po kilku sekundach

### <a name="symptom"></a>Objaw

* Regularne żądania wysyłane do zaplecza bez przechodzenia przez frontony platformy Azure kończą się powodzeniem. Przechodzenie przez czołowe drzwi platformy Azure w 503 odpowiedzi na błędy.
* Awaria z usług frontonu platformy Azure zwykle jest wyświetlana po około 30 sekundach.

### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być jedna z dwóch rzeczy:
 
* Zaplecze trwa dłużej niż skonfigurowany limit czasu (wartość domyślna to 30 sekund), aby otrzymać żądanie od zewnętrznych drzwi platformy Azure.
* Czas potrzebny na wysłanie odpowiedzi na żądanie z usługi Azure Front Drzwiczks trwa dłużej niż wartość limitu czasu. 

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

* Wyślij żądanie bezpośrednio do zaplecza (bez przechodzenia przez fronton platformy Azure). Zobacz, jak długo zaplecze zwykle odpowiada.
* Wyślij żądanie za pośrednictwem drzwi platformy Azure i sprawdź, czy otrzymujesz odpowiedzi na 503. Jeśli nie, problem może nie być problemem z limitem czasu. Skontaktuj się z pomocą techniczną.
* Jeśli przechodzenie między frontonem platformy Azure spowoduje wyświetlenie kodu błędu 503, należy skonfigurować `sendReceiveTimeout` pole dla drzwi frontonu platformy Azure. Domyślny limit czasu można zwiększyć do 4 minut (240 sekund). Ustawienie jest w obszarze `backendPoolSettings` i jest wywoływane `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-return-a-400-status-code"></a>Żądania wysyłane do domeny niestandardowej zwracają kod stanu 400

### <a name="symptom"></a>Objaw

* Utworzono wystąpienie drzwi platformy Azure, ale żądanie do domeny lub hosta frontonu zwraca kod stanu HTTP 400.
* Utworzono mapowanie DNS dla domeny niestandardowej na skonfigurowany Host frontonu. Jednak wysłanie żądania do nazwy hosta domeny niestandardowej zwraca kod stanu HTTP 400. Nie wydaje się kierować do skonfigurowanej wewnętrznej bazy danych.

### <a name="cause"></a>Przyczyna

Problem występuje, jeśli nie skonfigurowano reguły routingu dla domeny niestandardowej, która została dodana jako host frontonu. Należy jawnie dodać regułę routingu dla tego hosta frontonu. Jest to prawdziwe, nawet jeśli reguła routingu została już skonfigurowana dla hosta frontonu w poddomenie "przednim" platformy Azure (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Dodaj regułę routingu dla domeny niestandardowej, aby skierować ruch do wybranej puli zaplecza.

## <a name="azure-front-door-doesnt-redirect-http-to-https"></a>Drzwi frontonu platformy Azure nie przekierowują protokołu HTTP do protokołu HTTPS

### <a name="symptom"></a>Objaw

Drzwi frontonu platformy Azure mają regułę routingu, która przekierowuje protokół HTTP do protokołu HTTPS, ale dostęp do domeny nadal utrzymuje protokół HTTP.

### <a name="cause"></a>Przyczyna

Taka sytuacja może wystąpić, jeśli reguły routingu nie zostały prawidłowo skonfigurowane dla drzwi platformy Azure. Zasadniczo Bieżąca konfiguracja nie jest określona i może mieć sprzeczne reguły.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

## <a name="request-to-a-frontend-host-name-returns-a-404-status-code"></a>Żądanie do nazwy hosta frontonu zwraca kod stanu 404

### <a name="symptom"></a>Objaw

Wystąpienie drzwi frontonu platformy Azure zostało utworzone przez skonfigurowanie hosta frontonu, puli zaplecza z co najmniej jednym zaplecze i reguły routingu łączącej hosta frontonu z pulą zaplecza. Zawartość jest niedostępna, gdy zostanie wysłane żądanie do skonfigurowanego hosta frontonu. W związku z tym żądanie zwraca kod stanu HTTP 404.

### <a name="cause"></a>Przyczyna

Istnieje kilka możliwych przyczyn tego symptomu:

* Zaplecze nie jest zapleczem publicznym i nie jest widoczne dla drzwi platformy Azure.
* Zaplecze jest nieprawidłowo skonfigurowana, co sprawia, że drzwi platformy Azure nie wysyłają niewłaściwego żądania. Innymi słowy, zaplecze akceptuje tylko protokół HTTP i nie zaakceptowano protokołu HTTPS. Dzięki temu drzwi platformy Azure próbują przekazywać żądania HTTPS.
* Zaplecze odrzuca nagłówek hosta, który został przekazany z żądaniem do zaplecza.
* Konfiguracja zaplecza nie została jeszcze w pełni wdrożona.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

* Sprawdź czas wdrożenia:
   * Przed wdrożeniem konfiguracji upewnij się, że oczekiwano co najmniej 10 minut.

* Sprawdź ustawienia zaplecza:
    * Przejdź do puli zaplecza, do której żądanie powinno być routowane. (Jest to zależne od tego, jak masz skonfigurowaną regułę routingu). Sprawdź, czy typ hosta zaplecza i nazwa hosta zaplecza są poprawne. Jeśli zaplecze jest hostem niestandardowym, upewnij się, że został wpisany poprawnie. 

    * Sprawdź porty HTTP i HTTPS. W większości przypadków 80 i 443 (odpowiednio) są poprawne i nie są wymagane żadne zmiany. Istnieje jednak możliwość, że zaplecze nie skonfigurowano w ten sposób i nasłuchuje na innym porcie.

    * Sprawdź nagłówek hosta zaplecza skonfigurowany na potrzeby zaplecza, do którego powinna być kierowanie hosta frontonu. W większości przypadków ten nagłówek powinien być taki sam jak nazwa hosta zaplecza. Jednak nieprawidłowa wartość może spowodować różne kody stanu 4xx HTTP, jeśli zaplecze oczekuje czegoś innego. W przypadku wprowadzenia adresu IP zaplecza może być konieczne ustawienie nagłówka hosta zaplecza na nazwę hosta zaplecza.

* Sprawdź ustawienia reguły routingu:
    * Przejdź do reguły routingu, która powinna kierować z danej nazwy hosta frontonu do puli zaplecza. Upewnij się, że akceptowane protokoły są prawidłowo skonfigurowane, gdy żądanie zostanie przesłane dalej. Pole **zaakceptowane protokoły** określa, które żądania mają być akceptowane przez usługi Azure front-drzwi. Protokół przekazywania określa, jaki protokół z usług Azure Front ma używać do przesyłania dalej żądania do zaplecza.
      
      Jeśli na przykład zaplecze akceptuje tylko żądania HTTP, następujące konfiguracje byłyby prawidłowe:
            
      * Akceptowane protokoły to HTTP i HTTPS. Protokół przekazywania to HTTP. Żądanie dopasowania nie zostanie wykonane, ponieważ HTTPS jest dozwolonym protokołem. Jeśli żądanie zostało wysłane jako HTTPS, frontony platformy Azure spróbuje przesłać je dalej przy użyciu protokołu HTTPS.

      * Akceptowany protokół to HTTP. Protokół przekazywania to żądanie dopasowania lub HTTP.
    - Ponowne **Zapisywanie adresów URL** jest domyślnie wyłączone. To pole jest używane tylko wtedy, gdy chcesz zawęzić zakres zasobów hostowanych przez zaplecza, które mają być dostępne. Gdy to pole jest wyłączone, drzwi frontonu platformy Azure będą przekazywać tę samą ścieżkę żądania, która otrzymuje. Możliwe jest błędna konfiguracja tego pola. W związku z tym, gdy drzwiczki frontonu platformy Azure żądają zasobu z zaplecza, który nie jest dostępny, zwróci kod stanu HTTP 404.

## <a name="request-to-the-frontend-host-name-returns-a-411-status-code"></a>Żądanie do nazwy hosta frontonu zwraca kod stanu 411

### <a name="symptom"></a>Objaw

Utworzono wystąpienie drzwi platformy Azure i skonfigurowano hosta frontonu, pulę zaplecza z co najmniej jednym zaplecem oraz regułę routingu łączącą hosta frontonu z pulą zaplecza. Zawartość nie jest dostępna, gdy żądanie przejdzie do skonfigurowanego hosta frontonu, ponieważ zwracany jest kod stanu HTTP 411.

Odpowiedzi na te żądania mogą również zawierać stronę błędu HTML w treści odpowiedzi zawierającej instrukcję wyjaśniającą. Na przykład: `HTTP Error 411. The request must be chunked or have a content length`.

### <a name="cause"></a>Przyczyna

Istnieje kilka możliwych przyczyn tego symptomu. Ogólną przyczyną jest to, że żądanie HTTP nie jest w pełni zgodne ze standardem RFC. 

Przykładem braku zgodności jest `POST` żądanie wysyłane bez `Content-Length` ani `Transfer-Encoding` nagłówka (na przykład przy użyciu `curl -X POST https://example-front-door.domain.com` ). To żądanie nie spełnia wymagań określonych w [dokumencie RFC 7230](https://tools.ietf.org/html/rfc7230#section-3.3.2). Drzwi frontonu platformy Azure blokują ją z odpowiedzią HTTP 411.

To zachowanie jest niezależne od funkcjonalności zapory aplikacji sieci Web (WAF) platformy Azure. Obecnie nie ma możliwości wyłączenia tego zachowania. Wszystkie żądania HTTP muszą spełniać wymagania, nawet jeśli funkcja WAF nie jest używana.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

- Sprawdź, czy żądania są zgodne z wymaganiami określonymi w niezbędnych dokumentach RFC.

- Zanotuj wszelkie treści wiadomości HTML, które są zwracane w odpowiedzi na żądanie. Treść komunikatu często wyjaśnia dokładnie, *jak* żądanie jest niezgodne.
