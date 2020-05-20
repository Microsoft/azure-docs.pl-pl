---
title: Oferty modułu IoT Edge portalu Azure Marketplace
description: Dowiedz się więcej o publikowaniu ofert modułu IoT Edge w portalu Azure Marketplace.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: dsindona
ms.openlocfilehash: 49f86a79eb5358d27c15d93004db396436c3e680
ms.sourcegitcommit: fdec8e8bdbddcce5b7a0c4ffc6842154220c8b90
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/19/2020
ms.locfileid: "83657957"
---
# <a name="iot-edge-modules"></a>Moduły usługi IoT Edge

Platforma [Azure IoT Edge](https://azure.microsoft.com/services/iot-edge/) jest obsługiwana przez Microsoft Azure.  Ta platforma umożliwia użytkownikom wdrażanie obciążeń w chmurze do uruchomienia bezpośrednio na urządzeniach IoT.  Moduł IoT Edge może uruchamiać obciążenia w trybie offline i analizować dane lokalnie. Ten typ oferty pomaga zaoszczędzić przepustowość, chronić lokalne i poufne dane oraz zapewnia czas odpowiedzi o małym opóźnieniu.  Masz teraz możliwość skorzystania z tych wstępnie skompilowanych obciążeń. Do tej pory dostępne są tylko kilku rozwiązań pierwszej firmy od firmy Microsoft.  Musisz zainwestować czas i zasoby na tworzenie własnych niestandardowych rozwiązań IoT.

Dzięki [modułom IoT Edge w portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/category/internet-of-things?page=1)firma Microsoft dysponuje jednym miejscem docelowym umożliwiającym wystawianie i sprzedawanie rozwiązań użytkownikom IoT. Deweloperzy IoT mogą ostatecznie znajdować i kupować funkcje przyspieszające tworzenie rozwiązań.  

## <a name="key-benefits-of-iot-edge-modules-in-azure-marketplace"></a>Najważniejsze zalety modułów IoT Edge w portalu Azure Marketplace

| **Dla wydawców**    | **Dla klientów (deweloperzy IoT)**  |
| :------------------- | :-------------------|
| Dotrzej do milionów deweloperów, którzy chcą kompilować i wdrażać rozwiązania IoT Edge.  | Twórz IoT Edge rozwiązanie z zachowaniem bezpiecznych i przetestowanych składników. |
| Publikuj raz i uruchamiaj na dowolnym IoT Edge sprzęt obsługujący kontenery. | Skracaj czas wprowadzenia na rynek, wyszukując i wdrażając moduły IoT Edge 1 i innych firm dla konkretnych potrzeb. |
| Zarabiaj z elastycznymi opcjami rozliczania <ul> <li> Bezpłatna i korzystaj z własnej licencji (BYOL). </li> </ul> | Twórz zakupy przy użyciu wybranych modeli rozliczeń. <ul> <li> Bezpłatna i korzystaj z własnej licencji (BYOL). </li> </ul> |

## <a name="what-is-an-iot-edge-module"></a>Co to jest moduł IoT Edge?

Azure IoT Edge pozwala wdrożyć logikę biznesową i zarządzać nią na krawędzi w formie modułów. Moduły Azure IoT Edge są najmniejszymi jednostkami obliczeniowymi zarządzanymi przez IoT Edge i mogą zawierać usługi firmy Microsoft (takie jak Azure Stream Analytics), usługi innych firm lub własny kod specyficzny dla rozwiązania. Aby dowiedzieć się więcej na temat modułów IoT Edge, zobacz [Opis modułów Azure IoT Edge](https://docs.microsoft.com/azure/iot-edge/iot-edge-modules).

**Jaka jest różnica między typem oferty kontenera a typem oferty modułu IoT Edge module?**

Typ oferty modułu IoT Edge jest określonym typem kontenera uruchomionym na urządzeniu IoT Edge. Dostępne są domyślne ustawienia konfiguracji, które mogą być uruchamiane w kontekście IoT Edge i IoT Edge opcjonalnie są zintegrowane z IoT Edge środowiska uruchomieniowego.

## <a name="publishing-your-iot-edge-module"></a>Publikowanie modułu IoT Edge

**Wybieranie odpowiedniego sklepu**

Moduły IoT Edge są publikowane tylko w portalu Azure Marketplace, AppSource nie ma zastosowania.  Aby uzyskać więcej informacji na temat różnic i docelowych odbiorców w sklepie, zobacz [Określanie opcji publikowania dla rozwiązania](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type).
 
**Opcje rozliczeń**

Portal Marketplace obsługuje obecnie **bezpłatne** opcje rozliczania **licencji (BYOL)** dla modułów IoT Edge.
 
**Opcje publikowania**

We wszystkich przypadkach moduł IoT Edge powinien wybrać opcję publikowania **Transact** .  Aby uzyskać więcej informacji na temat opcji publikowania, zobacz [Wybieranie opcji publikowania](https://docs.microsoft.com/azure/marketplace/determine-your-listing-type) .  

## <a name="eligibility-criteria"></a>Kryteria kwalifikujące

Wszystkie postanowienia Microsoft Azure Marketplace umów i zasad mają zastosowanie do ofert IoT Edge module.  Ponadto istnieją wymagania wstępne i techniczne dla modułów IoT Edge.  

**Wymagania wstępne**

Aby opublikować moduł IoT Edge w portalu Azure Marketplace, należy spełnić następujące wymagania wstępne:

- Dostęp do Centrum partnerskiego. Aby uzyskać więcej informacji, zobacz artykuł [Azure Marketplace i Podręcznik publikowania AppSource](https://docs.microsoft.com/azure/marketplace/marketplace-publishers-guide).
- Hostowanie modułu IoT Edge w Azure Container Registry. 
- Zastąp metadane modułu IoT Edge, takie jak (lista niepełna): 
    - Tytuł
    - Opis (w formacie HTML)
    - Obraz logo (format PNG i stałe rozmiary obrazu, w tym 40x40px, 90x90px, 115x115px, 255x115px)
    - Warunki użytkowania i zasady zachowania poufności informacji
    - Domyślna konfiguracja modułu (trasa, wymagane właściwości przędzy, opcje i zmienne środowiskowe)
    - Dokumentacja
    - Kontakt z pomocą techniczną

**Wymagania techniczne**

Podstawowe wymagania techniczne dotyczące modułu IoT Edge, aby można było uzyskać certyfikat i opublikować go w witrynie Azure Marketplace, opisano szczegółowo w temacie [przygotowanie zasobów technicznych modułu IoT Edge](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="documentation-and-resources"></a>Dokumentacja i zasoby

[Tworzenie oferty modułu IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) — kroki publikowania nowej oferty modułu IoT Edge w centrum partnerskim.

## <a name="next-steps"></a>Następne kroki

Jeśli jeszcze tego nie zrobiono,

- [Dowiedz się więcej](https://azuremarketplace.microsoft.com/sell) o portalu Marketplace.

Aby zarejestrować się w centrum partnerskim i zacząć tworzyć nową ofertę lub pracować na istniejącym,

- Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- Aby uzyskać informacje na temat publikowania oferty modułu IoT Edge, zobacz temat [Tworzenie oferty modułu IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) .
