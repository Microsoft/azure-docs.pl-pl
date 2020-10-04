---
title: Oferty modułu IoT Edge portalu Azure Marketplace
description: Dowiedz się więcej o publikowaniu ofert modułu IoT Edge w portalu Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 08/18/2020
ms.openlocfilehash: 90267f2b75b7b9c1e77a45d7e3faa4b0bf6dd63a
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/04/2020
ms.locfileid: "91708547"
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

Azure IoT Edge pozwala wdrożyć logikę biznesową i zarządzać nią na krawędzi w formie modułów. Moduły Azure IoT Edge są najmniejszymi jednostkami obliczeniowymi zarządzanymi przez IoT Edge i mogą zawierać usługi firmy Microsoft (takie jak Azure Stream Analytics), usługi innych firm lub własny kod specyficzny dla rozwiązania. Aby dowiedzieć się więcej na temat modułów IoT Edge, zobacz [Opis modułów Azure IoT Edge](../iot-edge/iot-edge-modules.md).

**Jaka jest różnica między typem oferty kontenera a typem oferty modułu IoT Edge module?**

Typ oferty modułu IoT Edge jest określonym typem kontenera uruchomionym na urządzeniu IoT Edge. Dostępne są domyślne ustawienia konfiguracji, które mogą być uruchamiane w kontekście IoT Edge i IoT Edge opcjonalnie są zintegrowane z IoT Edge środowiska uruchomieniowego.

## <a name="publishing-your-iot-edge-module"></a>Publikowanie modułu IoT Edge

**Wybieranie odpowiedniego sklepu online**

Moduły IoT Edge są publikowane tylko w portalu Azure Marketplace. AppSource nie ma zastosowania. Aby uzyskać więcej informacji na temat różnic między sklepami online, zobacz [Określanie opcji publikowania](determine-your-listing-type.md).

**Opcje rozliczeń**

Portal Marketplace obsługuje obecnie **bezpłatne** opcje rozliczania **licencji (BYOL)** dla modułów IoT Edge.

### <a name="publishing-options"></a>Opcje publikowania

We wszystkich przypadkach moduł IoT Edge powinien wybrać opcję publikowania **Transact** .  Aby uzyskać więcej informacji na temat opcji publikowania, zobacz [Wybieranie opcji publikowania](determine-your-listing-type.md) .  

## <a name="eligibility-criteria"></a>Kryteria kwalifikujące

Wszystkie postanowienia Microsoft Azure Marketplace umów i zasad mają zastosowanie do ofert IoT Edge module.  Ponadto istnieją wymagania wstępne i techniczne dla modułów IoT Edge.  

### <a name="prerequisites"></a>Wymagania wstępne

Aby opublikować moduł IoT Edge w portalu Azure Marketplace, należy spełnić następujące wymagania wstępne:

- Dostęp do Centrum partnerskiego. Aby uzyskać więcej informacji, zobacz [Tworzenie komercyjnego konta witryny Marketplace w centrum partnerskim](partner-center-portal/create-account.md).
- Hostowanie modułu IoT Edge w Azure Container Registry.
- Zastąp metadane modułu IoT Edge, takie jak (lista niepełna):
    - Tytuł
    - Opis (w formacie HTML)
    - Obraz logo (w rozmiarach 48 x 48 (opcjonalnie), 90 x 90 (opcjonalnie) i od 216 x 216 do 350 x 350 px, wszystko w formacie PNG)
    - Warunki użytkowania i zasady zachowania poufności informacji
    - Domyślna konfiguracja modułu (trasa, wymagane właściwości przędzy, opcje i zmienne środowiskowe)
    - Dokumentacja
    - Kontakt z pomocą techniczną

### <a name="technical-requirements"></a>Wymagania techniczne

Podstawowe wymagania techniczne dotyczące modułu IoT Edge, aby można było uzyskać certyfikat i opublikować go w witrynie Azure Marketplace, opisano szczegółowo w temacie [przygotowanie zasobów technicznych modułu IoT Edge](./partner-center-portal/create-iot-edge-module-asset.md).

## <a name="next-steps"></a>Następne kroki

- Zaloguj się do [Centrum partnerskiego](https://partner.microsoft.com/dashboard/account/v3/enrollment/introduction/partnership) , aby utworzyć lub zakończyć swoją ofertę.
- [Utwórz ofertę modułu IoT Edge](./partner-center-portal/azure-iot-edge-module-creation.md) w centrum partnerskim.
