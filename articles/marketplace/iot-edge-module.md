---
title: Moduły usługi Azure Marketplace ioT Edge
description: Oferta modułu usługi IoT Edge w portalu Azure Marketplace dla wydawców aplikacji i usług.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 762d9947046f159e992f09211bfcd76ff8d6712e
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81684338"
---
# <a name="iot-edge-modules"></a>Moduły usługi IoT Edge

Platforma [Usługi Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) jest wspierana przez usługę Azure Cloud.  Ta platforma umożliwia użytkownikom wdrażanie obciążeń w chmurze do uruchamiania bezpośrednio na urządzeniach IoT.  Moduł usługi IoT Edge może uruchamiać obciążenia w trybie offline i wykonywać analizy danych lokalnie. Ten typ oferty pomaga zaoszczędzić przepustowość, chronić dane lokalne i poufne oraz oferuje czas reakcji o małych opóźnieniach.  Teraz masz opcje, aby skorzystać z tych wstępnie utworzonych obciążeń. Do tej pory dostępnych było tylko kilka rozwiązań firmy Microsoft.  Trzeba było zainwestować czas i zasoby w tworzenie własnych niestandardowych rozwiązań IoT.

Wprowadzając [moduły usługi IoT Edge w portalu Azure Marketplace,](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)mamy teraz jedno miejsce docelowe dla wydawców, aby udostępnić i sprzedać swoje rozwiązania odbiorcom IoT. Deweloperzy IoT mogą ostatecznie znaleźć i kupić możliwości, aby przyspieszyć ich rozwój rozwiązań.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Najważniejsze zalety modułów usługi IoT Edge w portalu Azure Marketplace:

| **Dla wydawców**    | **Dla klientów (deweloperzy IoT)**  |
| :------------------- | :-------------------|
| Docieraj do milionów programistów, którzy chcą tworzyć i wdrażać rozwiązania IoT Edge.  | Skomponuj rozwiązanie Usługi IoT Edge z ufnością przy użyciu bezpiecznych i przetestowanych komponentów. |
| Publikuj raz i uruchom dowolny sprzęt IoT Edge, który obsługuje kontenery. | Skróć czas na rynek, znajdując i wdrażając moduły IoT Edge 1 i 3rd party dla konkretnych potrzeb. |
| Zarabianie dzięki elastycznym opcjom rozliczeń <ul> <li> Bezpłatna i przynieś własną licencję (BYOL). </li> </ul> | Rościj zakupy, dokonując wyboru modeli rozliczeniowych. <ul> <li> Bezpłatna i przynieś własną licencję (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Co to jest moduł IoT Edge?

Usługa Azure IoT Edge umożliwia wdrażanie logiki biznesowej i zarządzanie nią na krawędzi w postaci modułów. Moduły usługi Azure IoT Edge są najmniejszymi jednostkami obliczeniowymi zarządzanymi przez usługę IoT Edge i mogą zawierać usługi firmy Microsoft (takie jak usługa Azure Stream Analytics), usługi innych firm lub własny kod specyficzny dla rozwiązania. Aby dowiedzieć się więcej o modułach usługi IoT Edge, zobacz [Opis modułów usługi Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Jaka jest różnica między typem oferty kontenera a typem oferty modułu Usługi IoT Edge?**

Typ oferty modułu usługi IoT Edge jest określonym typem kontenera działającego na urządzeniu usługi IoT Edge. Jest wyposażony w domyślne ustawienia konfiguracji do uruchomienia w kontekście usługi IoT Edge i opcjonalnie używa SDK modułu usługi IoT Edge do zintegrowanego ze środowiskiem uruchomieniowym usługi IoT Edge.

## <a name="publishing-your-iot-edge-module"></a>Publikowanie modułu IoT Edge

**Wybór odpowiedniego sklepu**

Moduły usługi IoT Edge są publikowane tylko w portalu Azure Marketplace, usługa AppSource nie ma zastosowania.  Aby uzyskać więcej informacji na temat różnic i odbiorców docelowych w witrynach sklepowych, zobacz [określanie opcji publikowania rozwiązania](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Opcje rozliczeń**

Marketplace obsługuje obecnie opcje rozliczeń **bezpłatnej** i **własnej licencji (BYOL)** dla modułów IoT Edge.
 
**Opcje publikowania**

We wszystkich przypadkach moduły IoT Edge powinny wybrać opcję publikowania **transakcji.**  Aby uzyskać więcej informacji na temat opcji publikowania, zobacz [wybieranie opcji publikowania.](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type)  

## <a name="eligibility-criteria"></a>Kryteria kwalifikowalności

Wszystkie warunki umów i zasad w portalu Microsoft Azure Marketplace mają zastosowanie do ofert modułów usługi IoT Edge.  Ponadto istnieją wymagania wstępne i wymagania techniczne dotyczące modułów usługi IoT Edge.  

**Wymagania wstępne**

Aby opublikować moduł usługi IoT Edge w portalu Azure Marketplace, musisz spełnić następujące wymagania wstępne:

- Dostęp do Centrum partnerskiego. Aby uzyskać więcej informacji, zobacz [Przewodnik po publikacjach w portalu Azure Marketplace i AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hostuj moduł usługi IoT Edge w rejestrze kontenerów platformy Azure. 
- Przygotuj metadane modułu Usługi IoT Edge, takie jak (niewyczerpywna lista): 
    - Tytuł
    - Opis (w formacie HTML)
    - Obraz logo (format PNG i stałe rozmiary obrazów, w tym 40x40px, 90x90px, 115x115px, 255x115px)
    - Termin użytkowania i polityka prywatności
    - Domyślna konfiguracja modułu (trasa, dwie wartości żądane, createOptions, zmienne środowiskowe)
    - Dokumentacja
    - Kontakt z pomocą techniczną

**Wymagania techniczne**

Podstawowe wymagania techniczne dotyczące modułu usługi IoT Edge, aby uzyskać certyfikat i opublikowanie w portalu Azure Marketplace, są szczegółowo opisane w [module IoT Edge zasobów technicznych.](./partner-center-portal/create-iot-edge-module-asset.md)

## <a name="documentation-and-resources"></a>Dokumentacja i zasoby

[Utwórz ofertę modułów usługi IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) — kroki publikowania nowej oferty modułów usługi IoT Edge w Centrum partnerów.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiłeś,

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o rynku.

Aby zarejestrować się w Centrum partnerów i rozpocząć tworzenie nowej oferty lub pracę nad istniejącą,

- Zaloguj się do [Centrum partnerów,](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) aby utworzyć lub uzupełnić ofertę.
- Zobacz [tworzenie oferty modułu usługi IoT Edge,](./partner-center-portal/azure-iot-edge-module-creation.md) aby uzyskać informacje na temat publikowania oferty modułów usługi IoT Edge.
