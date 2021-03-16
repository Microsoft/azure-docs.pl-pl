---
title: Zalecenia na podstawie agenta
titleSuffix: Azure Defender for IoT
description: Dowiedz się więcej na temat koncepcji zaleceń dotyczących zabezpieczeń i sposobu ich używania w przypadku urządzeń z programem Defender for IoT.
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
ms.date: 02/16/2021
ms.author: shhazam
ms.openlocfilehash: e746f37fdf3b67467c1844ebea9191679d52d6d1
ms.sourcegitcommit: 4bda786435578ec7d6d94c72ca8642ce47ac628a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103491269"
---
# <a name="security-recommendations-for-iot-devices"></a>Zalecenia dotyczące zabezpieczeń urządzeń IoT

Usługa Defender for IoT skanuje zasoby platformy Azure i urządzenia IoT oraz zapewnia zalecenia dotyczące zabezpieczeń, aby zmniejszyć obszar narażony na ataki.
Zalecenia dotyczące zabezpieczeń są funkcjonalne i mają na celu pomóc klientom w spełnieniu najlepszych rozwiązań w zakresie zabezpieczeń.

W tym artykule znajdziesz listę zaleceń, które mogą być wyzwalane na urządzeniach IoT.

## <a name="agent-based-recommendations"></a>Zalecenia na podstawie agenta

Zalecenia dotyczące urządzeń zapewniają szczegółowe informacje i sugestie dotyczące usprawnienia stan zabezpieczeń urządzeń.

| Ważność | Nazwa | Źródło danych | Opis |
|--|--|--|--|
| Śred. | Otwórz porty na urządzeniu | Klasyczna usługa Defender-IoT-Micro-Agent| Znaleziono punkt końcowy nasłuchiwania na urządzeniu. |
| Śred. | Odnoszące się do zasad zapory znajdują się w jednym z łańcuchów. | Klasyczna usługa Defender-IoT-Micro-Agent| Znaleziono dozwolone zasady zapory (dane wejściowe/wyjściowe). Zasady zapory powinny domyślnie blokować cały ruch i definiować reguły zezwalające na niezbędną komunikację z urządzeniem lub z niego. |
| Śred. | Znaleziono ograniczającą regułę zapory w łańcuchu wejściowym | Klasyczna usługa Defender-IoT-Micro-Agent| Znaleziono regułę w zaporze, która zawiera oddzielny wzorzec dla szerokiego zakresu adresów IP lub portów. |
| Śred. | Znaleziono ograniczającą regułę zapory w łańcuchu wyjściowym | Klasyczna usługa Defender-IoT-Micro-Agent| Znaleziono regułę w zaporze, która zawiera oddzielny wzorzec dla szerokiego zakresu adresów IP lub portów. |
| Śred. | Weryfikacja linii bazowej systemu operacyjnego nie powiodła się | Klasyczna usługa Defender-IoT-Micro-Agent| Urządzenie nie jest zgodne z wynikami [testów CIS systemu Linux](https://www.cisecurity.org/cis-benchmarks/). |

### <a name="agent-based-operational-recommendations"></a>Zalecenia dotyczące działania oparte na agencie

Zalecenia dotyczące działania zapewniają szczegółowe informacje i sugestie dotyczące poprawy konfiguracji agenta zabezpieczeń.

| Ważność | Nazwa | Źródło danych | Opis |
|--|--|--|--|
| Niski | Agent wysyła niewykorzystywane wiadomości | Klasyczna usługa Defender-IoT-Micro-Agent| 10% lub więcej komunikatów o zabezpieczeniach było mniejsze niż 4 KB w ciągu ostatnich 24 godzin. |
| Niski | Konfiguracja przędzy zabezpieczającej nie jest optymalna | Klasyczna usługa Defender-IoT-Micro-Agent| Konfiguracja przędzy zabezpieczeń nie jest optymalna. |
| Niski | Konflikt konfiguracji przędzy zabezpieczającej | Klasyczna usługa Defender-IoT-Micro-Agent| Zidentyfikowano konflikty w konfiguracji dwuosiowych zabezpieczeń. |  |

## <a name="next-steps"></a>Następne kroki

- Usługa Defender for IoT [— Omówienie](overview.md)
- Dowiedz się, jak [uzyskać dostęp do danych zabezpieczeń](how-to-security-data-access.md)
- Dowiedz się więcej [na temat badania urządzenia](how-to-investigate-device.md)
