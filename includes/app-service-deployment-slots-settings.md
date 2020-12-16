---
author: cephalin
ms.service: app-service
ms.topic: include
ms.date: 09/18/2019
ms.author: cephalin
ms.openlocfilehash: 7458f6868d7fbee72b55ad002148691a113c269d
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97531800"
---
W przypadku klonowania konfiguracji z innego miejsca wdrożenia sklonowana konfiguracja jest edytowalna. Niektóre elementy konfiguracji są zgodne z zawartością w wymianie (nie w konkretnym gnieździe), podczas gdy inne elementy konfiguracji pozostają w tym samym miejscu po wymianie (specyficzne dla miejsca). Poniższe listy pokazują ustawienia, które zmieniają się w przypadku wymiany miejsc.

**Ustawienia, które zostały zamienione**:

* Ustawienia ogólne, takie jak wersja platformy, 32/64-bitowa, gniazda sieci Web
* Ustawienia aplikacji (można skonfigurować do nalepki do miejsca)
* Parametry połączenia (można je skonfigurować do nawiązania połączenia z gniazdem)
* Mapowania obsługi
* Certyfikaty publiczne
* Zawartość zadań WebJob
* Połączenia hybrydowe *
* Punkty końcowe usługi *
* Azure Content Delivery Network *

Funkcje oznaczone gwiazdką (*) są planowane jako niezamienione. 

**Ustawienia, które nie zostały zamienione**:

* Publikowanie punktów końcowych
* Niestandardowe nazwy domen
* Certyfikaty inne niż publiczne i ustawienia protokołu TLS/SSL
* Ustawienia skalowania
* Harmonogramy zadań WebJob
* Ograniczenia adresów IP
* Zawsze włączone
* Ustawienia diagnostyczne
* Współużytkowanie zasobów między źródłami (CORS)
* Integracja sieci wirtualnej

> [!NOTE]
> Aby można było wymienić te ustawienia, należy dodać ustawienie aplikacji `WEBSITE_OVERRIDE_PRESERVE_DEFAULT_STICKY_SLOT_SETTINGS` w każdym gnieździe aplikacji i ustawić jego wartość na `0` lub `false` . Te ustawienia są albo w ogóle wymieniane, czy nie. Nie można wprowadzać tylko niektórych ustawień, a nie innych.

> Niektóre ustawienia aplikacji, które mają zastosowanie do niewymienianych ustawień, również nie są zamieniane. Na przykład, ponieważ ustawienia diagnostyczne nie są wymieniane, powiązane ustawienia aplikacji, takie jak `WEBSITE_HTTPLOGGING_RETENTION_DAYS` i, `DIAGNOSTICS_AZUREBLOBRETENTIONDAYS` również nie są wymieniane, nawet jeśli nie są wyświetlane jako ustawienia gniazda.
>
