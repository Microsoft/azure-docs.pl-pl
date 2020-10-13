---
title: Rozwiązywanie problemów z konfiguracją z przodu platformy Azure
description: W ramach tego samouczka nauczysz się, jak rozwiązywać problemy z niektórymi typowymi problemami, które mogą być używane w przypadku zewnętrznych drzwi.
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
ms.openlocfilehash: dbce9019e33c07dd4faa91ffd490eba4d313c675
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91630614"
---
# <a name="troubleshooting-common-routing-issues"></a>Rozwiązywanie typowych problemów z routingiem

W tym artykule opisano sposób rozwiązywania niektórych typowych problemów z routingiem, które mogą być potrzebne w konfiguracji drzwi platformy Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 odpowiedzi z przednich drzwi po kilku sekundach

### <a name="symptom"></a>Objaw

* Regularne żądania wysyłane do zaplecza bez przechodzenia przez drzwi do przodu kończą się powodzeniem, ale za pośrednictwem czołowych drzwi są 503 odpowiedzi na błędy.
* Awaria z czołowych drzwi ukazuje się po kilku sekundach (zwykle około 30 sekund)

### <a name="cause"></a>Przyczyna

Przyczyną tego problemu może być jedna z dwóch rzeczy:
 
* Zaplecze trwa dłużej niż skonfigurowany limit czasu (wartość domyślna to 30 sekund), aby otrzymać żądanie od drzwi z przodu.
* Czas potrzebny na wysłanie odpowiedzi na żądanie od drzwi zewnętrznych trwa dłużej niż wartość limitu czasu. 

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

* Wyślij żądanie bezpośrednio do zaplecza (bez przechodzenia przez drzwi) i zobacz, co to jest zwykły czas odpowiedzi Twojej wewnętrznej bazy danych na odpowiedź.
* Wyślij żądanie za pośrednictwem drzwi i sprawdź, czy otrzymujesz odpowiedzi na 503. W przeciwnym razie problem nie może być problemem z limitem czasu. Skontaktuj się z pomocą techniczną.
* W przypadku przechodzenia przez przód od drzwi do kodu odpowiedzi na błąd 503, a następnie skonfiguruj `sendReceiveTimeout` pole dla drzwi z przodu. Domyślny limit czasu można zwiększyć do 4 minut (240 sekund). Ustawienie jest pod `backendPoolSettings` i jest wywoływane `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Żądania wysyłane do domeny niestandardowej zwracają kod stanu 400

### <a name="symptom"></a>Objaw

* Utworzono tylne drzwi, ale żądanie do domeny lub hosta frontonu zwraca kod stanu HTTP 400.
* Utworzono mapowanie DNS dla domeny niestandardowej na skonfigurowany Host frontonu. Jednak wysyłanie żądania do nazwy hosta domeny niestandardowej zwraca kod stanu HTTP 400. Które nie wydaje się kierować do skonfigurowanej wewnętrznej bazy danych.

### <a name="cause"></a>Przyczyna

Problem występuje, jeśli nie skonfigurowano reguły routingu dla domeny niestandardowej, która została dodana jako host frontonu. Należy jawnie dodać regułę routingu dla tego hosta frontonu. Nawet jeśli jedna z nich została już skonfigurowana dla hosta frontonu w poddomenie przednim (*. azurefd.net).

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Dodaj regułę routingu dla domeny niestandardowej, aby skierować ruch do wybranej puli zaplecza.

## <a name="front-door-doesnt-redirect-http-to-https"></a>Drzwi tylne nie przekierowują protokołu HTTP do protokołu HTTPS

### <a name="symptom"></a>Objaw

Drzwi z przodu mają regułę routingu, która wskazuje przekierowanie protokołu HTTP do protokołu HTTPS, ale dostęp do domeny nadal utrzymuje protokół HTTP.

### <a name="cause"></a>Przyczyna

Takie zachowanie może wystąpić, jeśli nie skonfigurowano prawidłowo reguł routingu dla drzwi z przodu. Zasadniczo Bieżąca konfiguracja nie jest określona i może mieć sprzeczne reguły.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Żądanie do nazwy hosta frontonu zwraca kod stanu 404

### <a name="symptom"></a>Objaw

 Po utworzeniu drzwi czołowych należy skonfigurować hosta frontonu, pulę zaplecza z co najmniej jednym zaplecem oraz regułę routingu łączącą hosta frontonu z pulą zaplecza. Zawartość jest niedostępna, gdy zostanie wysłane żądanie do skonfigurowanego hosta frontonu w wyniku zwrócenia kodu stanu HTTP 404.

### <a name="cause"></a>Przyczyna

Istnieje kilka możliwych przyczyn tego symptomu:

* Zaplecze nie jest publicznym zaplecem i nie jest widoczne dla drzwi z przodu.
* Zaplecze jest nieprawidłowo skonfigurowana, powodując, że tylne drzwi wysyłają Nieprawidłowe żądanie. Innymi słowy, zaplecze akceptuje tylko protokół HTTP i nie sprawdzono, czy zezwolono na korzystanie z protokołu HTTPS. Dlatego drzwi przed próbami przekazują żądania HTTPS.
* Zaplecze odrzuca nagłówek hosta, który został przekazany z żądaniem do zaplecza.
* Konfiguracja zaplecza nie została jeszcze w pełni wdrożona.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

1. Czas wdrożenia
   * Upewnij się, że w ciągu 10 minut została wdrożona konfiguracja.

2. Sprawdź ustawienia zaplecza
    * Przejdź do puli zaplecza, do której żądanie powinno być routowane (zależnie od tego, jak masz skonfigurowaną regułę routingu). Sprawdź, czy *typ hosta zaplecza* i nazwa hosta zaplecza są poprawne. Jeśli zaplecze jest hostem niestandardowym, upewnij się, że został wpisany poprawnie. 

    * Sprawdź porty HTTP i HTTPS. W większości przypadków 80 i 443 (odpowiednio) są poprawne i nie będą wymagane żadne zmiany. Istnieje jednak możliwość, że zaplecze nie skonfigurowano w ten sposób i nasłuchuje na innym porcie.

        * Sprawdź _nagłówek hosta zaplecza_ skonfigurowany na potrzeby zaplecza, do którego powinna być kierowanie hosta frontonu. W większości przypadków ten nagłówek powinien być taki sam jak *Nazwa hosta zaplecza*. Jednak nieprawidłowa wartość może spowodować różne kody stanu 4xx HTTP, jeśli zaplecze oczekuje czegoś innego. Jeśli wprowadzasz adres IP zaplecza, może być konieczne ustawienie *nagłówka hosta zaplecza* na nazwę hosta zaplecza.

3. Sprawdź ustawienia reguły routingu:
    * Przejdź do reguły routingu, która powinna kierować z danej nazwy hosta frontonu do puli zaplecza. Upewnij się, że akceptowane protokoły są prawidłowo skonfigurowane podczas przesyłania żądania. Pole *zaakceptowane protokoły* określa, które żądania powinny być akceptowane z przodu. *Protokół przekazywania* określa, jaki protokół powinien być używany do przesyłania dalej żądania do zaplecza.
         * Jeśli na przykład zaplecze akceptuje tylko żądania HTTP, następujące konfiguracje byłyby prawidłowe:
            * *Akceptowane protokoły* to http i https. *Protokół przekazywania* to http. Żądanie dopasowania nie zostanie wykonane, ponieważ HTTPS jest dozwolonym protokołem, a jeśli żądanie zostało wysłane jako HTTPS, przód drzwi spróbuje przesłać je dalej przy użyciu protokołu HTTPS.

            * *Akceptowane protokoły* to http. *Protokół przekazywania* jest zgodny z żądaniem lub http.

    - Ponowne *Zapisywanie adresów URL* jest domyślnie wyłączone. To pole jest używane tylko wtedy, gdy chcesz zawęzić zakres zasobów hostowanych przez zaplecza, które mają być dostępne. Po wyłączeniu drzwi do przodu przekażą tę samą ścieżkę żądania, która otrzymuje. Możliwe jest błędna konfiguracja tego pola. W związku z tym, gdy drzwi czołowe żądają zasobu z zaplecza, który nie jest dostępny, zwróci kod stanu HTTP 404.
