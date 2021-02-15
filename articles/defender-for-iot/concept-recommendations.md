---
title: Zalecenia dotyczące zabezpieczeń
description: Dowiedz się więcej na temat koncepcji zaleceń dotyczących zabezpieczeń i sposobu ich użycia w usłudze Defender for IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: shhazam-ms
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/25/2021
ms.author: shhazam
ms.openlocfilehash: 10246277715d23f42eb0bd17d19794b8d4503f3f
ms.sourcegitcommit: 27d616319a4f57eb8188d1b9d9d793a14baadbc3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/15/2021
ms.locfileid: "100521870"
---
# <a name="security-recommendations"></a>Zalecenia dotyczące zabezpieczeń

Usługa Defender for IoT skanuje zasoby platformy Azure i urządzenia IoT oraz zapewnia zalecenia dotyczące zabezpieczeń, aby zmniejszyć obszar narażony na ataki.
Zalecenia dotyczące zabezpieczeń są funkcjonalne i mają na celu pomóc klientom w spełnieniu najlepszych rozwiązań w zakresie zabezpieczeń.

W tym artykule znajdziesz listę zaleceń, które mogą być wyzwalane na urządzeniach IoT Hub i/lub IoT.

## <a name="agent-based-recommendations"></a>Zalecenia oparte na agentach

Zalecenia dotyczące urządzeń zapewniają szczegółowe informacje i sugestie dotyczące usprawnienia stan zabezpieczeń urządzeń.

| Ważność | Nazwa | Źródło danych | Opis |
|--|--|--|--|
| Śred. | Otwórz porty na urządzeniu | Klasyczny moduł zabezpieczeń | Znaleziono punkt końcowy nasłuchiwania na urządzeniu. |
| Śred. | Odnoszące się do zasad zapory znajdują się w jednym z łańcuchów. | Klasyczny moduł zabezpieczeń | Znaleziono dozwolone zasady zapory (dane wejściowe/wyjściowe). Zasady zapory powinny domyślnie blokować cały ruch i definiować reguły zezwalające na niezbędną komunikację z urządzeniem lub z niego. |
| Śred. | Znaleziono ograniczającą regułę zapory w łańcuchu wejściowym | Klasyczny moduł zabezpieczeń | Znaleziono regułę w zaporze, która zawiera oddzielny wzorzec dla szerokiego zakresu adresów IP lub portów. |
| Śred. | Znaleziono ograniczającą regułę zapory w łańcuchu wyjściowym | Klasyczny moduł zabezpieczeń | Znaleziono regułę w zaporze, która zawiera oddzielny wzorzec dla szerokiego zakresu adresów IP lub portów. |
| Śred. | Weryfikacja linii bazowej systemu operacyjnego nie powiodła się | Klasyczny moduł zabezpieczeń | Urządzenie nie jest zgodne z wynikami [testów CIS systemu Linux](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Zalecenia operacyjne oparte na agentach

Zalecenia dotyczące działania zapewniają szczegółowe informacje i sugestie dotyczące poprawy konfiguracji agenta zabezpieczeń.

| Ważność | Nazwa | Źródło danych | Opis |
|--|--|--|--|
| Niski | Agent wysyła niewykorzystywane wiadomości | Klasyczny moduł zabezpieczeń | 10% lub więcej komunikatów o zabezpieczeniach było mniejsze niż 4 KB w ciągu ostatnich 24 godzin. |
| Niski | Konfiguracja przędzy zabezpieczającej nie jest optymalna | Klasyczny moduł zabezpieczeń | Konfiguracja przędzy zabezpieczeń nie jest optymalna. |
| Niski | Konflikt konfiguracji przędzy zabezpieczającej | Klasyczny moduł zabezpieczeń | Zidentyfikowano konflikty w konfiguracji dwuosiowych zabezpieczeń. |  |


## <a name="built-in-recommendations-in-iot-hub"></a>Wbudowane zalecenia w IoT Hub

Alerty dotyczące rekomendacji zapewniają wgląd i sugestie dotyczące działań usprawniających stan zabezpieczeń środowiska.

| Ważność | Nazwa | Źródło danych | Opis |
|--|--|--|--|
| Wys. | Identyczne poświadczenia uwierzytelniania używane przez wiele urządzeń | Usługa IoT Hub | Poświadczenia uwierzytelniania IoT Hub są używane przez wiele urządzeń. Ten proces może wskazywać, że urządzenie illegitimate personifikuje wiarygodne urządzenie. Zduplikowane użycie poświadczeń zwiększa ryzyko personifikacji urządzenia przez złośliwy aktor. |
| Śred. | Domyślne zasady filtrowania adresów IP powinny być Odmów | Usługa IoT Hub | Konfiguracja filtru IP powinna mieć reguły zdefiniowane dla dozwolonego ruchu i powinna domyślnie odmówić całego ruchu. |
| Śred. | Reguła filtru IP obejmuje duży zakres adresów IP | Usługa IoT Hub | Źródłowy zakres adresów IP reguły filtru adresów IP jest zbyt duży. Nadmiernie ograniczające reguły mogą uwidocznić Centrum IoT w złośliwych aktorach. |
| Niski | Włączanie dzienników diagnostycznych w IoT Hub | Usługa IoT Hub | Włącz dzienniki i zachowaj je przez maksymalnie rok. Przechowywanie dzienników umożliwia ponowne utworzenie śladów aktywności w celach dochodzeniowych w przypadku wystąpienia zdarzenia związanego z bezpieczeństwem lub zabezpieczenia sieci. |

## <a name="next-steps"></a>Następne kroki

- Usługa Defender for IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
