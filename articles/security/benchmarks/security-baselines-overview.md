---
title: Omówienie testu porównawczego zabezpieczeń platformy Azure
description: Omówienie testu porównawczego zabezpieczeń
author: msmbaldwin
manager: rkarlin
ms.service: security
ms.topic: conceptual
ms.date: 12/16/2019
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 43718e992349ff9bcb3635ab1b949c2b2f2c9ec0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82871989"
---
# <a name="security-baselines-for-azure"></a>Punkty odniesienia zabezpieczeń dla platformy Azure

Linie bazowe zabezpieczeń systemu Azure ułatwiają zwiększenie bezpieczeństwa dzięki udoskonalonym funkcjom narzędzi, śledzenia i zabezpieczeń. Zapewniają one również spójne środowisko w przypadku zabezpieczania środowiska.

Linie bazowe zabezpieczeń dla platformy Azure koncentrują się na obszarach formantów skoncentrowanych na chmurze. Te kontrolki są spójne z dobrze znanymi testami zabezpieczeń, takimi jak opisane w centrum dla zabezpieczeń Internetu (CIS). Nasze linie bazowe zapewniają wskazówki dotyczące obszarów kontroli wymienionych w [teście zabezpieczeń Azure](overview.md).

Każde zalecenie zawiera następujące informacje:

- **Identyfikator platformy Azure**: Identyfikator testu porównawczego zabezpieczeń platformy Azure, który odpowiada zalecenia.
- **Zalecenie**: bezpośrednio po identyfikatorze platformy Azure zalecenie zawiera ogólny opis formantu.
- **Wskazówki**: uzasadnienie zalecenia i linki do wskazówek dotyczących sposobu ich implementacji. Jeśli zalecenie jest obsługiwane przez Azure Security Center, te informacje będą również wyświetlane.
- **Odpowiedzialność**: kto jest odpowiedzialny za implementację kontroli. Możliwe scenariusze to odpowiedzialność klienta, odpowiedzialność firmy Microsoft lub współdzielona odpowiedzialność.
- **Azure Security Center monitorowania**: czy formant jest monitorowany przez Azure Security Center, z linkiem do odwołania.

Wszystkie zalecenia, w tym zalecenia, które nie mają zastosowania do tej konkretnej usługi, znajdują się w linii bazowej, aby zapewnić kompletną wersję testu porównawczego zabezpieczeń platformy Azure dla każdej usługi. Mogą być sporadyczne kontrolki, które nie mają zastosowania z różnych powodów — na przykład kontrolki ukierunkowane na IaaS/obliczeniowe (takie jak kontrolki specyficzne dla zarządzania konfiguracją systemu operacyjnego) mogą nie być stosowane do usług PaaS Services.


Poznamy Twoją opinię na temat linii bazowych zabezpieczeń dla usług platformy Azure. Zachęcamy do podania komentarzy w obszarze opinii poniżej. Lub, jeśli wolisz udostępnić dane wejściowe bardziej prywatnie z zespołem usługi Azure Security test https://aka.ms/AzSecBenchmark
