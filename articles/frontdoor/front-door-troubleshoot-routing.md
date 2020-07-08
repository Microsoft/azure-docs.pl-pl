---
title: Rozwiązywanie problemów z konfiguracją z przodu platformy Azure
description: W ramach tego samouczka nauczysz się, jak rozwiązywać problemy z niektórymi typowymi problemami, które mogą być używane w przypadku zewnętrznych drzwi.
services: frontdoor
documentationcenter: ''
author: sharad4u
editor: ''
ms.service: frontdoor
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 09/22/2018
ms.author: sharadag
ms.openlocfilehash: f4310350e83284d6a2839f8c3d86526d3cda74ff
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "84743579"
---
# <a name="troubleshooting-common-routing-issues"></a>Rozwiązywanie typowych problemów z routingiem

W tym artykule opisano sposób rozwiązywania niektórych typowych problemów z routingiem, które mogą być potrzebne w konfiguracji drzwi platformy Azure.

## <a name="503-response-from-front-door-after-a-few-seconds"></a>503 odpowiedzi z przednich drzwi po kilku sekundach

### <a name="symptom"></a>Objaw

- Regularne żądania wysyłane do zaplecza bez przechodzenia przez drzwi do przodu kończą się powodzeniem, ale za pośrednictwem czołowych drzwi są 503 odpowiedzi na błędy.

- Awaria z czołowych drzwi ukazuje się po kilku sekundach (zwykle około 30 sekund)

### <a name="cause"></a>Przyczyna

Ten objaw występuje, gdy zaplecze przekroczy konfigurację limitu czasu (wartość domyślna to 30 sekund), aby otrzymać żądanie od drzwi zewnętrznych lub przekroczyć tę wartość limitu czasu w celu wysłania odpowiedzi na żądanie od drzwi zewnętrznych. 

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

- Wyślij żądanie bezpośrednio do zaplecza (bez przechodzenia przez drzwi) i zobacz, co to jest zwykły czas odpowiedzi Twojej wewnętrznej bazy danych na odpowiedź.
- Wyślij żądanie za pośrednictwem drzwi i sprawdź, czy widzisz jakiekolwiek 503 odpowiedzi. W przeciwnym razie może to nie być problem z limitem czasu. Skontaktuj się z pomocą techniczną.
- W przypadku przechodzenia przez przód od drzwi do kodu błędu 503, następnie należy skonfigurować pole sendReceiveTimeout dla drzwi przednich, aby zwiększyć domyślny limit czasu do 4 minut (240 sekund). Ustawienie jest pod `backendPoolSettings` i jest wywoływane `sendRecvTimeoutSeconds` . 

## <a name="requests-sent-to-the-custom-domain-returns-400-status-code"></a>Żądania wysyłane do domeny niestandardowej zwracają kod stanu 400

### <a name="symptom"></a>Objaw

- Utworzono tylne drzwi, ale żądanie do domeny lub hosta frontonu zwraca kod stanu HTTP 400.

- Utworzono mapowanie DNS z domeny niestandardowej na skonfigurowany Host frontonu. Jednak wysyłanie żądania do nazwy hosta domeny niestandardowej zwraca kod stanu HTTP 400 i nie wydaje się kierować do skonfigurowanych zaplecza.

### <a name="cause"></a>Przyczyna

Ten objaw może wystąpić, jeśli nie skonfigurowano reguły routingu dla domeny niestandardowej, która została dodana jako host frontonu. Regułę routingu należy jawnie dodać do tego hosta frontonu, nawet jeśli została już skonfigurowana dla hosta frontonu w poddomenie frontonu (*. azurefd.net), do której domena niestandardowa ma mapowanie DNS.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Dodaj regułę routingu z domeny niestandardowej do żądanej puli zaplecza.

## <a name="front-door-is-not-redirecting-http-to-https"></a>Drzwi tylne nie przekierowują protokołu HTTP do protokołu HTTPS

### <a name="symptom"></a>Objaw

Drzwi z przodu mają regułę routingu, która wskazuje przekierowanie protokołu HTTP do protokołu HTTPS, ale dostęp do domeny nadal utrzymuje protokół HTTP.

### <a name="cause"></a>Przyczyna

Takie zachowanie może wystąpić, jeśli nie skonfigurowano poprawnie reguł routingu dla drzwi z przodu. Zasadniczo Bieżąca konfiguracja nie jest określona i może mieć sprzeczne reguły.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

## <a name="request-to-frontend-hostname-returns-404-status-code"></a>Żądanie do nazwy hosta frontonu zwraca kod stanu 404

### <a name="symptom"></a>Objaw

- Utworzono drzwiczki z przodu i skonfigurowano hosta frontonu, pulę zaplecza z co najmniej jednym zaplecem oraz regułę routingu łączącą hosta frontonu z pulą zaplecza. Zawartość nie jest dostępna podczas wysyłania żądania do skonfigurowanego hosta frontonu, ponieważ zwracany jest kod stanu HTTP 404.

### <a name="cause"></a>Przyczyna

Istnieje kilka możliwych przyczyn tego symptomu:

- Zaplecze nie jest publicznym zaplecem i nie jest widoczne dla drzwi przednich.
- Zaplecze jest nieprawidłowo skonfigurowana, co sprawia, że poprzednia próba wysłania niewłaściwego żądania (oznacza to, że zaplecze akceptuje tylko protokół HTTP, ale nie sprawdzono, czy nie zostało to zrobione, aby umożliwić podjęcie dalej żądań HTTPS).
- Zaplecze odrzuca nagłówek hosta, który został przekazany z żądaniem do zaplecza.
- Konfiguracja zaplecza nie została jeszcze w pełni wdrożona.

### <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

1. Czas wdrożenia
   - Upewnij się, że w ciągu 10 minut zainstalowano konfigurację do wdrożenia.

2. Sprawdź ustawienia zaplecza
    - Przejdź do puli zaplecza, do której żądanie powinno być routowane (zależnie od tego, jak masz skonfigurowaną regułę routingu) i sprawdź, czy _typ hosta zaplecza_ i nazwa hosta zaplecza są poprawne. Jeśli zaplecze jest hostem niestandardowym, upewnij się, że został wpisany poprawnie. 

    - Sprawdź porty HTTP i HTTPS. W większości przypadków 80 i 443 (odpowiednio) są poprawne i nie będą wymagane żadne zmiany. Istnieje jednak możliwość, że zaplecze nie skonfigurowano w ten sposób i nasłuchuje na innym porcie.

        - Sprawdź _nagłówek hosta zaplecza_ skonfigurowany na potrzeby zaplecza, do którego powinna być kierowanie hosta frontonu. W większości przypadków ten nagłówek powinien być taki sam jak _Nazwa hosta zaplecza_. Jednak nieprawidłowa wartość może spowodować różne kody stanu 4xx HTTP, jeśli zaplecze oczekuje czegoś innego. Jeśli wprowadzasz adres IP zaplecza, może być konieczne ustawienie _nagłówka hosta zaplecza_ na nazwę hosta zaplecza.


3. Sprawdź ustawienia reguły routingu
    - Przejdź do reguły routingu, która powinna kierować z danej nazwy hosta frontonu do puli zaplecza. Upewnij się, że akceptowane protokoły są prawidłowo skonfigurowane lub jeśli nie, upewnij się, że protokół z przodu zostanie użyty podczas przesyłania dalej żądania. Pole _zaakceptowane protokoły_ określa, które żądania powinny zostać zaakceptowane, a _Protokół przekazywania_ określa, jakie z nich drzwi powinny być używane do przesyłania dalej żądania do zaplecza.
         - Jeśli na przykład zaplecze akceptuje tylko żądania HTTP, następujące konfiguracje byłyby prawidłowe:
            - _Akceptowane protokoły_ to http i https. _Protokół przekazywania_ to http. Żądanie dopasowania nie będzie działało, ponieważ HTTPS jest dozwolonym protokołem, a jeśli żądanie zostało odebrane jako HTTPS, przód drzwiczki spróbuje przesłać je dalej przy użyciu protokołu HTTPS.

            - _Akceptowane protokoły_ to http. _Protokół przekazywania_ jest zgodny z żądaniem lub https.

    - Ponowne _Zapisywanie adresów URL_ jest domyślnie wyłączone i należy używać tego pola tylko wtedy, gdy chcesz zawęzić zakres zasobów hostowanych przez zaplecza, które mają być dostępne. Po wyłączeniu drzwi do przodu przekażą tę samą ścieżkę żądania, która otrzymuje. Istnieje możliwość, że to pole jest błędnie skonfigurowane i drzwi przede wszystkim żądają zasobu od zaplecza, który nie jest dostępny, co zwraca kod stanu HTTP 404.

