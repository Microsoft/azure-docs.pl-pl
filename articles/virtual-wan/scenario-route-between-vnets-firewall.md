---
title: 'Scenariusz: niestandardowy Routing zapory platformy Azure dla wirtualnej sieci WAN'
titleSuffix: Azure Virtual WAN
description: Scenariusze dotyczące ruchu routingu routingu między sieci wirtualnychami bezpośrednio, ale przy użyciu zapory platformy Azure dla sieci wirtualnej — >Internetu/gałęzi i gałęzi do przepływów ruchu
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: d083607782f96744ecbd7d23976f77ee53fec49d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94515573"
---
# <a name="scenario-azure-firewall---custom"></a>Scenariusz: Zapora platformy Azure — niestandardowa

Podczas pracy z routingiem wirtualnego koncentratora sieci WAN jest dość kilka dostępnych scenariuszy. W tym scenariuszu celem jest kierowanie ruchu między sieci wirtualnychami bezpośrednio, ale przy użyciu zapory platformy Azure dla przepływów ruchu między sieciami wirtualnymi i gałęzią.

## <a name="design"></a><a name="design"></a>Projekt

Aby ustalić, ile tabel tras będzie potrzebnych, można utworzyć macierz łączności, gdzie każda komórka reprezentuje, czy źródło (wiersz) może komunikować się z miejscem docelowym (kolumna). Macierz łączności w tym scenariuszu jest prosta, ale jest spójna z innymi scenariuszami. nadal możemy ją obejrzeć.

**Macierz łączności**

| Źródło           | Do:      | *Sieci wirtualnych*      | *Gałęzie*    | *Internet*   |
|---             |---       |---           |---            |---           |
| **Sieci wirtualnych**      |   &#8594;|    Direct    |     AzFW      |     AzFW     |
| **Gałęzie**   |   &#8594;|    AzFW      |    Direct     |    Direct    |

W poprzedniej tabeli "bezpośrednie" reprezentuje bezpośrednią łączność między dwoma połączeniami bez ruchu przechodzącego przez zaporę platformy Azure w wirtualnej sieci WAN, a "AzFW" wskazuje, że przepływ przejdzie przez zaporę platformy Azure. Ponieważ w macierzy istnieją dwa odrębne wzorce łączności, potrzebne są dwie tabele tras, które zostaną skonfigurowane w następujący sposób:

* Sieci wirtualne:
  * Skojarzona tabela tras: **RT_VNet**
  * Propagowanie do tabel tras: **RT_VNet**
* Gałęzi
  * Skojarzona tabela tras: **Domyślna**
  * Propagowanie do tabel tras: **domyślne**

> [!NOTE]
> Można utworzyć oddzielne wystąpienie sieci wirtualnej z jednym jednym bezpiecznym koncentratorem wirtualnym w każdym regionie, a następnie połączyć każdą wirtualną sieć WAN ze sobą za pośrednictwem sieci VPN typu lokacja-lokacja.

Aby uzyskać informacje na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).

## <a name="workflow"></a><a name="workflow"></a>Przepływ pracy

W tym scenariuszu należy kierować ruchem za pośrednictwem zapory platformy Azure w celu połączenia między sieciami wirtualnymi, z siecią wirtualną lub gałęzią, ale chcemy przejść bezpośrednio do ruchu typu sieć wirtualna-sieć wirtualna. Jeśli używasz Menedżera zapory platformy Azure, ustawienia trasy są automatycznie wypełniane w **domyślnej tabeli tras**. Ruch prywatny dotyczy sieci wirtualnej i gałęzi, ruch internetowy ma zastosowanie do 0.0.0.0/0.

Połączenia sieci VPN, ExpressRoute i sieci VPN użytkownika są nazywane oddziałami zbiorczymi i są skojarzone z tą samą (domyślną) tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras. Aby skonfigurować ten scenariusz, należy wziąć pod uwagę następujące czynności:

1. Utwórz niestandardową tabelę tras **RT_VNet**.
1. Utwórz trasę umożliwiającą aktywowanie połączeń między sieciami wirtualnymi i VNet: 0.0.0.0/0 z następnym przeskokiem wskazującym na zaporę platformy Azure. W sekcji Propagacja upewnij się, że wybrano opcję sieci wirtualnych, która zapewni bardziej konkretne trasy, umożliwiając tym samym przepływ ruchu między sieciami wirtualnymi.

   * W obszarze **skojarzenie:** wybierz pozycję sieci wirtualnych, która będzie oznaczać, że sieci wirtualnych osiągnie miejsce docelowe zgodnie z trasami tej tabeli tras.
   * W **propagacji:** wybierz pozycję sieci wirtualnych, która będzie oznaczać, że sieci wirtualnych jest propagowana do tej tabeli tras; Innymi słowy, bardziej szczegółowe trasy są propagowane do tej tabeli tras, co zapewnia bezpośredni ruch ruchu między sieciami wirtualnymi.

1. Dodaj zagregowaną trasę statyczną dla sieci wirtualnych do **tabeli tras domyślnych** , aby aktywować przepływ gałęzi do sieci wirtualnej za pośrednictwem zapory platformy Azure.

   * Należy pamiętać, że gałęzie są skojarzone i propagowanie do domyślnej tabeli tras.
   * Gałęzie nie są propagowane do RT_VNet tabeli tras. Zapewnia to przepływ ruchu między sieciami wirtualnymi za pośrednictwem zapory platformy Azure.

Spowoduje to zmianę konfiguracji routingu, jak pokazano na **rysunku 1**.

**Rysunek 1**

:::image type="content" source="./media/routing-scenarios/between-vnets-firewall/routing.png" alt-text="Rysunek 1":::

> [!NOTE]
> Wirtualne centra sieci WAN i połączone sieci wirtualne powinny znajdować się w tym samym regionie świadczenia usługi Azure.

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
* Aby uzyskać więcej informacji na temat routingu koncentratorów wirtualnych, zobacz [Informacje o routingu koncentratora wirtualnego](about-virtual-hub-routing.md).
* Więcej informacji o sposobie konfigurowania routingu koncentratora wirtualnego znajduje się w temacie [jak skonfigurować Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md).
