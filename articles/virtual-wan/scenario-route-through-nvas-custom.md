---
title: Kierowanie ruchu przez urządzeń WUS przy użyciu ustawień niestandardowych
titleSuffix: Azure Virtual WAN
description: Ten scenariusz ułatwia kierowanie ruchu przez urządzeń WUS przy użyciu różnych urządzenie WUS dla ruchu związanego z Internetem.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8e51d7d00120f6facb0fb53a8e379d157ae79ea4
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98938570"
---
# <a name="scenario-route-traffic-through-nvas-by-using-custom-settings"></a>Scenariusz: kierowanie ruchu przez urządzeń WUS przy użyciu ustawień niestandardowych

Gdy pracujesz z routingiem wirtualnego centrum sieci WAN platformy Azure, masz dostęp do wielu opcji. Ten artykuł ma na celu kierowanie ruchu sieciowego przez wirtualne urządzenie sieciowe (urządzenie WUS) w celu komunikacji między sieciami wirtualnymi i gałęziami oraz użycie różnych urządzenie WUS dla ruchu związanego z Internetem. Aby uzyskać więcej informacji, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="design"></a>Projekt

* **Szprychy** dla sieci wirtualnych podłączonych do koncentratora wirtualnego. (Na przykład Sieć wirtualna 1, Sieć wirtualna 2 i Sieć wirtualna 3 na diagramie w dalszej części tego artykułu).
* **Sieć wirtualna usługi** dla sieci wirtualnych, w której użytkownicy wdrożyły urządzenie WUS w celu sprawdzenia ruchu niepochodzącego z Internetu i prawdopodobnie z typowymi usługami dostępnymi przez szprychy. (Na przykład sieć VNet 4 na diagramie w dalszej części tego artykułu). 
* **Obwodowa sieć** wirtualna dla sieci wirtualnych, w której użytkownicy wdrażają urządzenie WUS do użycia w celu sprawdzenia ruchu związanego z Internetem. (Na przykład sieć VNet 5 na diagramie w dalszej części tego artykułu).
* **Centra** dla wirtualnych centrów sieci WAN zarządzanych przez firmę Microsoft.

W poniższej tabeli zestawiono połączenia obsługiwane w tym scenariuszu:

| Źródło          | Działanie|Szprychy|Sieć wirtualna usługi|Gałęzie|Internet|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Szprychy**| ->| wprost |wprost | za poorednictwem sieci wirtualnej usługi |za poorednictwem sieci wirtualnej |
| **Sieć wirtualna usługi**| ->| wprost |n/d| wprost | |
| **Gałęzie** | ->| za poorednictwem sieci wirtualnej usługi |wprost| wprost |  |

Każda z komórek w macierzy łączności zawiera opis, czy połączenie odbywa się bezpośrednio za pośrednictwem wirtualnej sieci WAN, czy za pośrednictwem jednej z sieci wirtualnych z urządzenie WUS. 

Zwróć uwagę na następujące szczegóły:
* Szprychy
  * Szprychy docierają do innych szprych bezpośrednio za pośrednictwem wirtualnych centrów sieci WAN.
  * Szprychy będą łączyć się z gałęziami za pośrednictwem trasy statycznej wskazującej na sieć wirtualną usługi. Nie poznają określonych prefiksów z gałęzi, ponieważ są one bardziej szczegółowe i zastępują podsumowanie.
  * Szprychy będą wysyłać ruch internetowy do sieci obwodowej przy użyciu bezpośredniej komunikacji równorzędnej sieci wirtualnej.
* Rozgałęzienia będą współdziałać za pośrednictwem routingu statycznego wskazującego na sieć wirtualną usługi. Nie poznają określonych prefiksów z sieci wirtualnych, które zastępują Podsumowanie trasy statycznej.
* Sieć wirtualna usługi będzie podobna do sieci wirtualnej usługi udostępnionej, która musi być osiągalna z wszystkich sieci wirtualnych i każdej gałęzi.
* Sieć wirtualna obwodu nie musi mieć łączności za pośrednictwem wirtualnej sieci WAN, ponieważ jedyny ruch, który będzie obsługiwany, jest przesyłany przez bezpośrednie połączenia równorzędne w sieciach wirtualnych. Aby uprościć konfigurację, należy jednak użyć tego samego modelu łączności, co w przypadku sieci wirtualnej obwodowej.

Istnieją trzy różne wzorce łączności, które tłumaczą na trzy tabele tras. Skojarzenia z różnymi sieciami wirtualnymi są następujące:

* Szprychy
  * Skojarzona tabela tras: **RT_V2B**
  * Propagowanie do tabel tras: **RT_V2B** i **RT_SHARED**
* URZĄDZENIE WUS sieci wirtualnych (Sieć wirtualna sieci VNET i Strefa DMZ):
  * Skojarzona tabela tras: **RT_SHARED**
  * Propagowanie do tabel tras: **RT_SHARED**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **RT_SHARED** i **domyślnych**

> [!NOTE] 
> Upewnij się, że sieci wirtualnych szprych nie są propagowane do etykiety domyślnej. Gwarantuje to, że ruch z gałęzi do szprychy sieci wirtualnych zostanie przekazany do urządzeń WUS.

Te trasy statyczne zapewniają, że ruch sieciowy do i z sieci wirtualnej i gałęzi przechodzą przez urządzenie WUS w usłudze VNet (Sieć wirtualna 4):

| Opis | Tabela tras | Trasa statyczna              |
| ----------- | ----------- | ------------------------- |
| Gałęzie    | RT_V2B      | 10.2.0.0/16 — > vnet4conn  |
| URZĄDZENIE WUS szprychy  | Domyślne     | 10.1.0.0/16 — > vnet4conn  |

Teraz możesz użyć wirtualnej sieci WAN, aby wybrać poprawne połączenie, do którego mają być wysyłane pakiety. Należy również użyć wirtualnej sieci WAN, aby wybrać poprawną akcję do wykonania podczas otrzymywania tych pakietów. W tym celu należy użyć tabel tras połączeń w następujący sposób:

| Opis | Połączenie | Trasa statyczna            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 — > 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 — > 10.4.0.5 |

Aby uzyskać więcej informacji, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="architecture"></a>Architektura

Poniżej znajduje się diagram architektury opisanej wcześniej w artykule.

Istnieje jedno centrum o nazwie **Hub 1**.

* **Koncentrator 1** jest podłączony bezpośrednio do urządzenie WUS sieci wirtualnych sieci **wirtualnej 4** i sieci **wirtualnej 5**.

* Ruch między sieci wirtualnych 1, 2 i 3 i rozgałęzienia oczekuje się za pośrednictwem sieci **wirtualnej 4 urządzenie WUS** 10.4.0.5.

* Cały ruch związany z Internetem z sieci wirtualnych 1, 2 i 3 oczekuje się za pośrednictwem sieci **wirtualnej 5 urządzenie WUS** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Diagram architektury sieci.":::

## <a name="workflow"></a>Przepływ pracy

Aby skonfigurować Routing za pośrednictwem urządzenie WUS, poniżej przedstawiono kroki, które należy wziąć pod uwagę:

1. W przypadku ruchu związanego z Internetem za pośrednictwem sieci wirtualnej 5 potrzeba sieci wirtualnych 1, 2 i 3, aby bezpośrednio łączyć się za pośrednictwem komunikacji równorzędnej usługi Virtual Network z siecią wirtualną 5. Wymagana jest również trasa zdefiniowana przez użytkownika w sieciach wirtualnych dla 0.0.0.0/0 i następnego skoku 10.5.0.5. Obecnie wirtualna sieć WAN nie zezwala na urządzenie WUS następnego skoku w koncentratorze wirtualnym dla 0.0.0.0/0.

1. W Azure Portal przejdź do koncentratora wirtualnego i Utwórz niestandardową tabelę tras o nazwie **RT_Shared**. Ta tabela zawiera informacje o trasach za pośrednictwem propagacji ze wszystkich sieci wirtualnych i połączeń gałęzi. Tę pustą tabelę można zobaczyć na poniższym diagramie.

   * **Trasy:** Nie musisz dodawać żadnych tras statycznych.

   * **Skojarzenie:** Wybierz sieci wirtualnych 4 i 5, co oznacza, że połączenia tych sieci wirtualnych są skojarzone z tabelą tras **RT_Shared**.

   * **Propagacja:** Ponieważ wszystkie gałęzie i połączenia sieci wirtualnej mają być dynamicznie propagowane do tej tabeli tras, wybierz gałęzie i wszystkie sieci wirtualne.

1. Utwórz niestandardową tabelę tras o nazwie **RT_V2B** do kierowania ruchu z sieci wirtualnych 1, 2 i 3 do gałęzi.

   * **Trasy:** Dodaj zagregowany wpis trasy statycznej dla gałęzi, z następnym przeskokiem jako połączenie sieci wirtualnej 4. Skonfiguruj trasę statyczną w połączeniu z siecią VNet 4 dla prefiksów gałęzi i wskaż następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:** Zaznacz wszystkie sieci wirtualnych 1, 2 i 3. Oznacza to, że połączenia sieci wirtualnej 1, 2 i 3 będą skojarzone z tą tabelą tras i będą mogły uczyć się tras (statyczne i dynamiczne za pośrednictwem propagacji) w tej tabeli tras.

   * **Propagacja:** Połączenia propagują trasy do tabel tras. Wybór sieci wirtualnych 1, 2 i 3 umożliwiają propagację tras z sieci wirtualnych 1, 2 i 3 do tej tabeli tras. Nie ma potrzeby propagowania tras z połączeń gałęzi do **RT_V2B**, ponieważ ruch sieci wirtualnej gałęzi przechodzi za pośrednictwem urządzenie WUS w VNET 4.
  
1. Edytuj domyślną tabelę tras, **DefaultRouteTable**.

   Wszystkie połączenia sieci VPN, platformy Azure ExpressRoute i sieci VPN użytkowników są skojarzone z domyślną tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras.

   * **Trasy:** Dodaj zagregowany wpis trasy statycznej dla sieci wirtualnych 1, 2 i 3 przy następnym przeskoku jako połączenie sieci wirtualnej 4. Skonfiguruj trasy statyczne w połączeniu sieci wirtualnej 4, 2 i 3 zagregowane prefiksy, a następnie wskaż następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:** Upewnij się, że jest wybrana opcja dla gałęzi (VPN/ER/P2S), dzięki czemu lokalne połączenia gałęzi są skojarzone z domyślną tabelą tras.

   * **Propagacja z:** Upewnij się, że jest wybrana opcja dla gałęzi (VPN/ER/P2S), dzięki czemu połączenia lokalne są propagowane do domyślnej tabeli tras.

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Diagram przepływu pracy." lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
