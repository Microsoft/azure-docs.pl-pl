---
title: 'Scenariusz: kierowanie ruchu przez urządzeń WUS przy użyciu ustawień niestandardowych'
titleSuffix: Azure Virtual WAN
description: Ten scenariusz ułatwia kierowanie ruchu przez urządzeń WUS przy użyciu innego urządzenie WUS dla ruchu związanego z Internetem.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 031cbb48a7e0c572866dc591d26fb1e6b6b12dba
ms.sourcegitcommit: 6906980890a8321dec78dd174e6a7eb5f5fcc029
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92424733"
---
# <a name="scenario-route-traffic-through-nvas---custom-preview"></a>Scenariusz: kierowanie ruchu przez urządzeń WUS — niestandardowy (wersja zapoznawcza)

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu urządzenie WUS (sieciowe urządzenie wirtualne) celem jest kierowanie ruchu przez urządzenie WUS w celu komunikacji między sieci wirtualnych i gałęziami oraz użycie innego urządzenie WUS dla ruchu związanego z Internetem. Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="design"></a><a name="design"></a>Projekt

W tym scenariuszu będziemy używać konwencji nazewnictwa:

* "Szprych" dla sieci wirtualnych podłączonych do koncentratora wirtualnego (Sieć wirtualna 1, Sieć wirtualna 2 i Sieć wirtualna 3 na **rysunku 1**).
* "Sieć wirtualna usług" dla sieci wirtualnych, w których użytkownicy wdrożyły urządzenie WUS (Sieć wirtualna 4 na **rysunku 1**) w celu sprawdzenia ruchu niepochodzącego z Internetu i prawdopodobnie z typowymi usługami dostępnymi przez szprychy.
* "Sieć wirtualna strefy DMZ" dla sieci wirtualnych, w których użytkownicy wdrażają urządzenie WUS do użycia w celu sprawdzenia ruchu powiązanego z Internetem (Sieć wirtualna 5 na **rysunku 1**).
* "Centra" dla wirtualnych centrów sieci WAN zarządzanych przez firmę Microsoft.

Następująca macierz łączności podsumowuje przepływy obsługiwane w tym scenariuszu:

**Macierz łączności**

| Źródło          | Do:|*Szprychy*|*Sieć wirtualna usługi*|*Gałęzie*|*Internet*|
|---|---|:---:|:---:|:---:|:---:|:---:|
| **Szprychy**| &#8594;| Bezpośrednio |Bezpośrednio | Za poorednictwem sieci wirtualnej usługi |Za poorednictwem sieci wirtualnej DMZ |
| **Sieć wirtualna usługi**| &#8594;| Bezpośrednio |nie dotyczy| Bezpośrednio | |
| **Gałęzie** | &#8594;| Za poorednictwem sieci wirtualnej usługi |Bezpośrednio| Bezpośrednio |  |

Każda z komórek w macierzy łączności zawiera opis, czy połączenie odbywa się bezpośrednio za pośrednictwem wirtualnej sieci WAN, czy za pośrednictwem jednego z sieci wirtualnych z urządzenie WUS. Przejdźmy szczegółowo do różnych wierszy:

* Szprychy
  * Szprychy docierają do innych szprych bezpośrednio za pośrednictwem wirtualnych centrów sieci WAN.
  * Szprychy będą łączyć się z gałęziami za pośrednictwem trasy statycznej wskazującej na sieć wirtualną usługi. Nie powinny uczyć się określonych prefiksów z gałęzi (w przeciwnym razie byłyby bardziej szczegółowe i zastąpią podsumowanie).
  * Szprychy będą wysyłać ruch internetowy do sieci obwodowej DMZ za pośrednictwem bezpośredniej sieci równorzędnej.
* Gałęzi
  * Rozgałęzienia będą współdziałać za pośrednictwem routingu statycznego wskazującego na sieć wirtualną usługi. Nie powinni uczyć się określonych prefiksów sieci wirtualnych, które zastępują Podsumowanie trasy statycznej.
* Sieć wirtualna usługi będzie podobna do sieci wirtualnej usług udostępnionych, która musi być osiągalna z każdej sieci wirtualnej i każdej gałęzi.
* Sieć wirtualna strefy DMZ nie musi mieć łączności za pośrednictwem wirtualnej sieci WAN, ponieważ jedyny ruch, który będzie obsługiwał, będzie przekroczyć bezpośrednie połączenia równorzędne sieci wirtualnej. Jednak użyjemy tego samego modelu łączności dla sieci wirtualnej DMZ, aby uprościć konfigurację.

Nasza macierz łączności daje nam trzy różne wzorce łączności, które tłumaczą na trzy tabele tras. Skojarzenia z różnymi sieci wirtualnychami będą następujące:

* Szprychy
  * Skojarzona tabela tras: **RT_V2B**
  * Propagowanie do tabel tras: **RT_V2B** i **RT_SHARED**
* URZĄDZENIE WUS sieci wirtualnych (Sieć wirtualna sieci VNET i Strefa DMZ):
  * Skojarzona tabela tras: **RT_SHARED**
  * Propagowanie do tabel tras: **RT_SHARED**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **RT_SHARED** i **domyślnych**

Potrzebujemy tych tras statycznych, aby upewnić się, że ruch między sieciami wirtualnymi i gałęzią jest kierowany przez urządzenie WUS w sieci wirtualnej usługi (Sieć wirtualna 4):

| Opis | Tabela tras | Trasa statyczna              |
| ----------- | ----------- | ------------------------- |
| Gałęzie    | RT_V2B      | 10.2.0.0/16 — > vnet4conn  |
| URZĄDZENIE WUS szprychy  | Domyślny     | 10.1.0.0/16 — > vnet4conn  |

Teraz wirtualna sieć WAN wie, z którym połączeniem należy wysyłać pakiety, ale połączenie musi wiedzieć, co należy zrobić podczas otrzymywania tych pakietów: jest to miejsce, w którym są używane tabele tras połączeń.

| Opis | Połączenie | Trasa statyczna            |
| ----------- | ---------- | ----------------------- |
| VNet2Branch | vnet4conn  | 10.2.0.0/16 — > 10.4.0.5 |
| Branch2VNet | vnet4conn  | 10.1.0.0/16 — > 10.4.0.5 |

W tym momencie wszystko powinno być stosowane.

Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="architecture"></a><a name="architecture"></a>Architektura

Na **rysunku 1**znajduje się jedno centrum, **Hub 1**.

* **Koncentrator 1** jest podłączony bezpośrednio do urządzenie WUS sieci wirtualnych sieci **wirtualnej 4** i sieci **wirtualnej 5**.

* Ruch między sieci wirtualnych 1, 2 i 3 i gałęziami (VPN/ER/P2S) powinien zostać przeszedł przez **Sieć VNET 4 urządzenie WUS** 10.4.0.5.

* Cały ruch związany z Internetem z sieci wirtualnych 1, 2 i 3 oczekuje się za pośrednictwem sieci **wirtualnej 5 urządzenie WUS** 10.5.0.5.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-1.png" alt-text="Rysunek 1":::

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

Aby skonfigurować Routing za pośrednictwem urządzenie WUS, poniżej przedstawiono kroki, które należy wziąć pod uwagę:

1. Aby ruch związany z Internetem przeszedł przez sieć wirtualną 5, potrzebne są sieci wirtualnych 1, 2 i 3 do bezpośredniego połączenia za pośrednictwem komunikacji równorzędnej sieci wirtualnej z siecią wirtualną 5. Wymagany jest również UDR skonfigurowany w sieci wirtualnych dla 0.0.0.0/0 i następnego skoku 10.5.0.5. Obecnie wirtualna sieć WAN nie zezwala na urządzenie WUS następnego skoku w koncentratorze wirtualnym dla 0.0.0.0/0.

1. W Azure Portal przejdź do centrum wirtualnego i Utwórz niestandardową tabelę tras **RT_Shared** , która będzie uczyć się tras za pośrednictwem propagacji ze wszystkich połączeń sieci wirtualnych i gałęzi. Na **rysunku 2**przedstawiono to pustą tabelę tras niestandardowych **RT_Shared**.

   * **Trasy:** Nie trzeba dodawać żadnych tras statycznych.

   * **Skojarzenie:** Wybierz sieci wirtualnych 4 i 5, co oznacza, że połączenia sieci wirtualnych 4 i 5 są kojarzone z tabelą tras **RT_Shared**.

   * **Propagacja:** Ponieważ chcesz, aby wszystkie gałęzie i połączenia sieci wirtualnej propagowanie tras dynamicznie do tej tabeli tras, zaznacz opcję gałęzie i wszystkie sieci wirtualnych.

1. Utwórz niestandardową tabelę tras **RT_V2B** do kierowania ruchu z sieci wirtualnych 1, 2 i 3 do gałęzi.

   * **Trasy:** Dodaj zagregowany wpis trasy statycznej dla gałęzi (VPN/ER/P2S) (10.2.0.0/16 na **rysunku 2**) z następnym przeskokiem jako połączenie sieci wirtualnej 4. Należy również skonfigurować trasę statyczną w połączeniu sieci wirtualnej 4 dla prefiksów gałęzi i wskazać następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:** Zaznacz wszystkie sieci wirtualnych 1, 2 i 3. Oznacza to, że połączenia sieci wirtualnej 1, 2 i 3 będą skojarzone z tą tabelą tras i będą mogły uczyć się tras (statyczne i dynamiczne za pośrednictwem propagacji) w tej tabeli tras.

   * **Propagacja:** Połączenia propagują trasy do tabel tras. Wybranie sieci wirtualnych 1, 2 i 3 umożliwi propagację tras z sieci wirtualnych 1, 2 i 3 do tej tabeli tras. Nie ma potrzeby propagowania tras z połączeń gałęzi do RT_V2B, ponieważ ruch sieci wirtualnej gałęzi odbywa się za pośrednictwem urządzenie WUS w sieci wirtualnej 4.
  
1. Edytuj domyślną tabelę tras **DefaultRouteTable**.

   Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników są skojarzone z domyślną tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras.

   * **Trasy:** Dodaj zagregowany wpis trasy statycznej dla sieci wirtualnych 1, 2 i 3 (10.1.0.0/16 na **rysunku 2**) przy następnym przeskoku jako połączenie sieci wirtualnej 4. Należy również skonfigurować trasę statyczną w połączeniu sieci wirtualnej 4, 2 i 3 zagregowane prefiksy, a następnie wskazać następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:** Upewnij się, że wybrano opcję dla gałęzi (VPN/ER/P2S), dzięki czemu lokalne połączenia gałęzi są skojarzone z *defaultroutetable*.

   * **Propagacja z:** Upewnij się, że jest wybrana opcja dla gałęzi (VPN/ER/P2S), dzięki czemu połączenia lokalne są propagowane do *defaultroutetable*.

**Rysunek 2**

:::image type="content" source="./media/routing-scenarios/nva-custom/figure-2.png" alt-text="Rysunek 1" lightbox="./media/routing-scenarios/nva-custom/figure-2.png":::

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
