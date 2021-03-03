---
title: Kierowanie ruchu przez urządzeń WUS przy użyciu ustawień niestandardowych
titleSuffix: Azure Virtual WAN
description: Ten scenariusz ułatwia kierowanie ruchu przez urządzeń WUS przy użyciu różnych urządzenie WUS dla ruchu związanego z Internetem.
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/25/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: 8916fbc7c2a0b9789dcc73697324cee370f1fc1c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704909"
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
|---|:---:|:---:|:---:|:---:|:---:|
| **Szprychy**| ->| wprost |wprost | za poorednictwem sieci wirtualnej usługi |za poorednictwem sieci wirtualnej |
| **Sieć wirtualna usługi**| ->| wprost |n/d| wprost | |
| **Gałęzie** | ->| za poorednictwem sieci wirtualnej usługi |wprost| wprost |  |

Każda z komórek w macierzy łączności zawiera opis, czy połączenie odbywa się bezpośrednio za pośrednictwem wirtualnej sieci WAN, czy za pośrednictwem jednej z sieci wirtualnych z urządzenie WUS.

Zwróć uwagę na następujące szczegóły:

* Szprychy
  * Szprychy docierają do innych szprych bezpośrednio za pośrednictwem wirtualnych centrów sieci WAN.
  * Szprychy będą łączyć się z gałęziami za pośrednictwem trasy statycznej wskazującej na sieć wirtualną usługi. Nie poznają określonych prefiksów z gałęzi, ponieważ te prefiksy są bardziej szczegółowe i zastępują podsumowanie.
  * Szprychy będą wysyłać ruch internetowy do sieci obwodowej przy użyciu bezpośredniej komunikacji równorzędnej sieci wirtualnej.
* Rozgałęzienia będą współdziałać za pośrednictwem routingu statycznego wskazującego na sieć wirtualną usługi. Nie poznają określonych prefiksów z sieci wirtualnych, które zastępują Podsumowanie trasy statycznej.
* Sieć wirtualna usługi będzie podobna do sieci wirtualnej usługi udostępnionej, która musi być osiągalna z wszystkich sieci wirtualnych i każdej gałęzi.
* Sieć wirtualna obwodu nie musi mieć łączności za pośrednictwem wirtualnej sieci WAN, ponieważ jedyny ruch, który będzie obsługiwany, jest przesyłany przez bezpośrednie połączenia równorzędne w sieciach wirtualnych. Aby uprościć konfigurację, należy jednak użyć tego samego modelu łączności, co w przypadku sieci wirtualnej obwodowej.

Istnieją trzy różne wzorce łączności, które przekładają się na trzy tabele tras. Skojarzenia z różnymi sieciami wirtualnymi są następujące:

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
> Upewnij się, że sieci wirtualnych szprych nie są propagowane do etykiety domyślnej. Gwarantuje to, że ruch z gałęzi do szprychy sieci wirtualnych jest przekazywany do urządzeń WUS.

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

Na poniższym diagramie przedstawiono architekturę opisaną wcześniej w artykule.

Na diagramie istnieje jedno centrum. **Hub 1**.

* **Koncentrator 1** jest podłączony bezpośrednio do urządzenie WUS sieci wirtualnych sieci **wirtualnej 4** i sieci **wirtualnej 5**.

* Ruch między sieci wirtualnych 1, 2, 3, a gałęzie oczekuje się za pośrednictwem sieci **wirtualnej 4 urządzenie WUS** 10.4.0.5.

* Cały ruch związany z Internetem z sieci wirtualnych 1, 2 i 3 oczekuje się za pośrednictwem sieci **wirtualnej 5 urządzenie WUS** 10.5.0.5.

:::image type="content" source="./media/routing-scenarios/nva-custom/architecture.png" alt-text="Diagram architektury sieci.":::

## <a name="workflow"></a><a name="workflow"></a>Utworzonego

:::image type="content" source="./media/routing-scenarios/nva-custom/workflow.png" alt-text="Diagram przepływu pracy." lightbox="./media/routing-scenarios/nva-custom/workflow.png":::

Aby skonfigurować Routing za pośrednictwem urządzenie WUS, należy wziąć pod uwagę następujące czynności:

1. W przypadku ruchu związanego z Internetem za pośrednictwem sieci wirtualnej 5 potrzeba sieci wirtualnych 1, 2 i 3, aby bezpośrednio łączyć się za pośrednictwem komunikacji równorzędnej usługi Virtual Network z siecią wirtualną 5. Wymagana jest również trasa zdefiniowana przez użytkownika w sieciach wirtualnych dla 0.0.0.0/0 i następnego skoku 10.5.0.5.

   Jeśli nie chcesz łączyć sieci wirtualnych 1, 2 i 3 z siecią wirtualną 5, a zamiast tego użyj urządzenie WUS w sieci wirtualnej 4 do kierowania ruchem 0.0.0.0/0 z gałęzi (lokalne połączenia VPN lub ExpressRoute), przejdź do [alternatywnego przepływu pracy](#alternate).

   Jeśli jednak ruch między sieciami wirtualnymi ma być przenoszony za pośrednictwem urządzenie WUS, należy rozłączyć sieć wirtualną 1, 2, 3 z koncentratora wirtualnego i połączyć ją z usługą urządzenie WUS szprych sieci vnet4. W przypadku wirtualnej sieci WAN ruch sieciowy między siecią wirtualną za pomocą wirtualnego koncentratora sieci WAN lub zapory platformy Azure wirtualnego centrum sieci WAN (bezpieczny koncentrator). Jeśli usługa sieci wirtualnych peer bezpośrednio przy użyciu komunikacji równorzędnej sieci wirtualnej, może komunikować się bezpośrednio z przechodzeniem przez koncentrator wirtualny.

1. W Azure Portal przejdź do koncentratora wirtualnego i Utwórz niestandardową tabelę tras o nazwie **RT_Shared**. Ta tabela zawiera informacje o trasach za pośrednictwem propagacji ze wszystkich sieci wirtualnych i połączeń gałęzi. Tę pustą tabelę można zobaczyć na poniższym diagramie.

   * **Trasy:** Nie musisz dodawać żadnych tras statycznych.

   * **Skojarzenie:** Wybierz sieci wirtualnych 4 i 5, co oznacza, że połączenia tych sieci wirtualnych są skojarzone z tabelą tras **RT_Shared**.

   * **Propagacja:** Ponieważ wszystkie gałęzie i połączenia sieci wirtualnej mają być dynamicznie propagowane do tej tabeli tras, wybierz gałęzie i wszystkie sieci wirtualne.

1. Utwórz niestandardową tabelę tras o nazwie **RT_V2B** do kierowania ruchu z sieci wirtualnych 1, 2 i 3 do gałęzi.

   * **Trasy:** Dodaj zagregowany wpis trasy statycznej dla gałęzi, z następnym przeskokiem jako połączenie sieci wirtualnej 4. Skonfiguruj trasę statyczną w połączeniu z siecią VNet 4 dla prefiksów gałęzi. Wskaż następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:** Zaznacz wszystkie **sieci wirtualnych 1, 2 i 3**. Oznacza to, że połączenia sieci wirtualnej 1, 2 i 3 będą skojarzone z tą tabelą tras i będą mogły uczyć się tras (statyczne i dynamiczne za pośrednictwem propagacji) w tej tabeli tras.

   * **Propagacja:** Połączenia propagują trasy do tabel tras. Wybranie sieci wirtualnych 1, 2 i 3 włącza propagację tras z sieci wirtualnych 1, 2 i 3 do tej tabeli tras. Nie ma potrzeby propagowania tras z połączeń gałęzi do **RT_V2B**, ponieważ ruch sieci wirtualnej gałęzi przechodzi za pośrednictwem urządzenie WUS w VNET 4.
  
1. Edytuj domyślną tabelę tras, **DefaultRouteTable**.

   Wszystkie połączenia sieci VPN, platformy Azure ExpressRoute i sieci VPN użytkowników są skojarzone z domyślną tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras.

   * **Trasy:** Dodaj zagregowany wpis trasy statycznej dla sieci wirtualnych 1, 2 i 3 przy następnym przeskoku jako połączenie sieci wirtualnej 4. Skonfiguruj trasę statyczną w połączeniu sieci wirtualnej 4 dla prefiksów zagregowanych 1, 2 i 3. Wskaż następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:** Upewnij się, że jest wybrana opcja dla gałęzi (VPN/ER/P2S), dzięki czemu lokalne połączenia gałęzi są skojarzone z domyślną tabelą tras.

   * **Propagacja z:** Upewnij się, że jest wybrana opcja dla gałęzi (VPN/ER/P2S), dzięki czemu połączenia lokalne są propagowane do domyślnej tabeli tras.

## <a name="alternate-workflow"></a><a name="alternate"></a>Alternatywny przepływ pracy

W tym przepływie pracy nie można łączyć sieci wirtualnych 1, 2 i 3 z siecią wirtualną 5. Zamiast tego należy użyć urządzenie WUS w sieci wirtualnej 4 do kierowania ruchem 0.0.0.0/0 z gałęzi (lokalne połączenia sieci VPN lub ExpressRoute).

:::image type="content" source="./media/routing-scenarios/nva-custom/alternate.png" alt-text="Diagram alternatywnego przepływu pracy." lightbox="./media/routing-scenarios/nva-custom/alternate.png":::

Aby skonfigurować Routing za pośrednictwem urządzenie WUS, należy wziąć pod uwagę następujące czynności:

1. W Azure Portal przejdź do koncentratora wirtualnego i Utwórz niestandardową tabelę tras o nazwie **RT_NVA** do kierowania ruchu za pośrednictwem urządzenie WUS 10.4.0.5  

   * **Trasy:**   Nie jest wymagana żadna akcja.

   * **Skojarzenie:**   Wybierz pozycję **sieci vnet4**. Oznacza to, że połączenie sieci wirtualnej 4 zostanie skojarzone z tą tabelą tras i będzie w stanie poznać trasy (statyczne i dynamiczne za pośrednictwem propagacji) w tej tabeli tras.

   * **Propagacja:**   Połączenia propagują trasy do tabel tras. Wybranie sieci wirtualnych 1, 2 i 3 włącza propagację tras z sieci wirtualnych 1, 2 i 3 do tej tabeli tras. Wybranie gałęzi (VPN/ER/P2S) umożliwia propagowanie tras z rozgałęzień/połączeń lokalnych do tej tabeli tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras.

1. Utwórz niestandardową tabelę tras o nazwie **RT_VNET** do kierowania ruchu z sieci wirtualnych 1, 2 i 3 do gałęzi lub Internetu (0.0.0.0/0) za pośrednictwem sieci vnet4 urządzenie WUS. Ruch między sieciami wirtualnymi będzie bezpośredni, a nie za pośrednictwem urządzenie WUS sieci wirtualnej 4. Jeśli chcesz, aby ruch przeszedł przez urządzenie WUS, odłącz sieć VNet 1, 2 i 3, a następnie połącz je za pomocą komunikacji równorzędnej sieci wirtualnej z sieci vnet4.

   * **Rozsyłan**  

     * Dodaj zagregowaną trasę "10.2.0.0/16" z następnym przeskokiem jako połączenie sieci wirtualnej 4 dla ruchu przechodzącego z sieci wirtualnych 1, 2 i 3 do gałęzi. W połączeniu sieci vnet4 Skonfiguruj trasę dla elementu "10.2.0.0/16" i wskaż następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

     * Dodaj trasę "0.0.0.0/0" z następnym przeskokiem jako połączenie sieci wirtualnej 4. wartość "0.0.0.0/0" jest dodawana, aby oznaczać wysyłanie ruchu do Internetu. Nie implikuje określonych prefiksów adresów odnoszących się do sieci wirtualnych lub gałęzi. W połączeniu sieci vnet4 Skonfiguruj trasę dla elementu "0.0.0.0/0" i wskaż następny przeskok jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:**   Zaznacz wszystkie **sieci wirtualnych 1, 2 i 3**. Oznacza to, że połączenia sieci wirtualnej 1, 2 i 3 będą skojarzone z tą tabelą tras i będą mogły uczyć się tras (statyczne i dynamiczne za pośrednictwem propagacji) w tej tabeli tras.

   * **Propagacja:**   Połączenia propagują trasy do tabel tras. Wybór sieci wirtualnych 1, 2 i 3 umożliwiają propagację tras z sieci wirtualnych 1, 2 i 3 do tej tabeli tras. Upewnij się, że opcja dla gałęzi (VPN/ER/P2S) nie jest zaznaczona. Dzięki temu połączenia lokalne nie mogą być bezpośrednio sieci wirtualnych 1, 2 i 3.

1. Edytuj domyślną tabelę tras, **DefaultRouteTable**.

   Wszystkie połączenia sieci VPN, platformy Azure ExpressRoute i sieci VPN użytkowników są skojarzone z domyślną tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras.

   * **Rozsyłan**  

     * Dodaj zagregowaną trasę "10.1.0.0/16" dla **sieci wirtualnych 1, 2 i 3** z następnym przeskokiem jako **połączenie sieci wirtualnej 4**.

     * Dodaj trasę "0.0.0.0/0" z następnym przeskokiem jako **połączenie sieci wirtualnej 4**. wartość "0.0.0.0/0" jest dodawana, aby oznaczać wysyłanie ruchu do Internetu. Nie implikuje określonych prefiksów adresów odnoszących się do sieci wirtualnych lub gałęzi. We wcześniejszym kroku dla połączenia sieci vnet4 już skonfigurowano trasę dla "0.0.0.0/0" z następnym przeskokiem jako określony adres IP urządzenie WUS w sieci wirtualnej 4.

   * **Skojarzenie:**   Upewnij się, że wybrano opcję gałęzi **(VPN/er/P2S)** . Gwarantuje to, że lokalne połączenia gałęzi są skojarzone z domyślną tabelą tras. Wszystkie połączenia sieci VPN, platformy Azure ExpressRoute i sieci VPN użytkownika są skojarzone tylko z domyślną tabelą tras.

   * **Propagacja z:**   Upewnij się, że wybrano opcję gałęzi **(VPN/er/P2S)** . Dzięki temu połączenia lokalne są propagowane tras do domyślnej tabeli tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do "tego samego zestawu tabel tras".

   >[!Note]
   >
   > * Aby trasa 0.0.0.0/0 zaczęła obowiązywać, użytkownicy portalu muszą włączyć opcję "Propaguj do trasy domyślnej" w połączeniach (VPN/ER/P2S/VNet).
   > * Aby trasa 0.0.0.0/0 zaczęła obowiązywać, Użytkownicy platformy PS/CLI muszą ustawić wartość true dla flagi "enableinternetsecurity".
   >

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
