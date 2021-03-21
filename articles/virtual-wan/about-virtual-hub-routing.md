---
title: Informacje na temat routingu na koncentratorze wirtualnym
titleSuffix: Azure Virtual WAN
description: W tym artykule opisano Routing koncentratora wirtualnego
services: virtual-wan
author: cherylmc
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 02/17/2021
ms.author: cherylmc
ms.custom: fasttrack-edit
ms.openlocfilehash: c1a2a54bf2d4c5de3e6cfca66256f60592fc1f3e
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101737515"
---
# <a name="about-virtual-hub-routing"></a>Informacje na temat routingu na koncentratorze wirtualnym

Możliwości routingu w koncentratorze wirtualnym są udostępniane przez router, który zarządza całą trasą między bramami przy użyciu Border Gateway Protocol (BGP). Koncentrator wirtualny może zawierać wiele bram, takich jak Brama sieci VPN typu lokacja-lokacja, Brama ExpressRoutea, Brama połączeń punkt-lokacja, Zapora platformy Azure. Ten router zapewnia również łączność między sieciami wirtualnymi, które łączą się z koncentratorem wirtualnym i obsługują zagregowaną przepływność wynoszącą 50 GB/s. Te możliwości routingu dotyczą standardowych wirtualnych klientów sieci WAN.

Aby skonfigurować Routing, zobacz [jak skonfigurować Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md).

## <a name="routing-concepts"></a><a name="concepts"></a>Pojęcia dotyczące routingu

W poniższych sekcjach opisano kluczowe założenia routingu koncentratora wirtualnego.

### <a name="hub-route-table"></a><a name="hub-route"></a>Tabela tras centrum

Tabela tras koncentratora wirtualnego może zawierać jedną lub więcej tras. Trasa obejmuje jego nazwę, etykietę, typ docelowy, listę prefiksów docelowych oraz informacje o następnym przeskoku dla pakietu, który ma być kierowany. **Połączenie** zazwyczaj będzie miało konfigurację routingu skojarzoną lub propagowaną do tabeli tras.

### <a name="connections"></a><a name="connection"></a>Połączenia

Połączenia są Menedżer zasobów zasobach z konfiguracją routingu. Istnieją cztery typy połączeń:

* **Połączenie sieci VPN**: łączy LOKACJĘ sieci VPN z bramą sieci VPN koncentratora wirtualnego.
* **Połączenie ExpressRoute**: łączy obwód usługi ExpressRoute z bramą ExpressRoute Hub.
* **Połączenie konfiguracji P2S**: łączy konfigurację sieci VPN użytkownika (punkt-lokacja) z bramą sieci VPN użytkownika wirtualnego (punkt-lokacja).
* **Centralne połączenie sieci wirtualnej**: łączy sieci wirtualne z koncentratorem wirtualnym.

Konfigurację routingu dla połączenia sieci wirtualnej można skonfigurować podczas instalacji. Domyślnie wszystkie połączenia są kojarzone i propagowane do domyślnej tabeli tras.

### <a name="association"></a><a name="association"></a>Skojarzenie

Każde połączenie jest skojarzone z jedną tabelą tras. Skojarzenie połączenia z tabelą tras zezwala na wysyłanie ruchu do miejsca docelowego wskazanego jako trasy w tabeli tras. Konfiguracja routingu dla połączenia będzie zawierać skojarzoną tabelę tras.  Wiele połączeń może być skojarzonych z tą samą tabelą tras. Wszystkie połączenia sieci VPN, ExpressRoute i VPN użytkowników są skojarzone z tą samą (domyślną) tabelą tras.

Domyślnie wszystkie połączenia są skojarzone z **domyślną tabelą tras** w koncentratorze wirtualnym. Każdy koncentrator wirtualny ma własną domyślną tabelę tras, którą można edytować w celu dodania tras statycznych. Trasy dodane statycznie mają pierwszeństwo przed dynamicznymi trasami dla tych samych prefiksów.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-association.png" alt-text="Skojarzenie":::

### <a name="propagation"></a><a name="propagation"></a>Propagacja

Połączenia dynamicznie propagują trasy do tabeli tras. W przypadku połączenia sieci VPN, połączenia ExpressRoute lub połączenia z konfiguracją P2S trasy są propagowane z koncentratora wirtualnego do routera lokalnego przy użyciu protokołu BGP. Trasy mogą być propagowane do jednej lub wielu tabel tras.

Dla każdego koncentratora wirtualnego jest również dostępna **tabela tras none** . Propagowanie do tabeli tras braku nie oznacza, że trasy nie są wymagane do propagowania z połączenia. Połączenia sieci VPN, ExpressRoute i VPN użytkowników propagują trasy do tego samego zestawu tabel tras.

:::image type="content" source="./media/about-virtual-hub-routing/concepts-propagation.png" alt-text="Propagacja":::

### <a name="labels"></a><a name="labels"></a>Etykiety

Etykiety zapewniają mechanizm logicznego grupowania tabel tras. Jest to szczególnie przydatne podczas propagacji tras z połączeń do wielu tabel tras. Na przykład **Domyślna tabela tras** ma wbudowaną etykietę o nazwie "default". Gdy użytkownicy propagują trasy połączeń do etykiety "default", automatycznie stosuje się do wszystkich domyślnych tabel tras w każdym centrum w wirtualnej sieci WAN.

### <a name="configuring-static-routes-in-a-virtual-network-connection"></a><a name="static"></a>Konfigurowanie tras statycznych w połączeniu sieci wirtualnej

Skonfigurowanie tras statycznych zapewnia mechanizm kierowania ruchu przez adres IP następnego przeskoku, który może należeć do sieciowego urządzenia wirtualnego (urządzenie WUS) obsługiwanego w sieci wirtualnej szprych podłączonych do koncentratora wirtualnego. Trasa statyczna składa się z nazwy trasy, listy prefiksów miejsc docelowych oraz adresu IP następnego przeskoku.

## <a name="route-tables-for-pre-existing-routes"></a><a name="route"></a>Tabele tras dla istniejących tras

Tabele tras mają teraz funkcje do skojarzenia i propagacji. Istniejąca tabela tras jest tabelą tras, która nie ma tych funkcji. Jeśli masz już istniejące trasy w usłudze Routing centralny i chcesz korzystać z nowych funkcji, weź pod uwagę następujące kwestie:

* **Klienci sieci wirtualnej w warstwie Standardowa ze wstępnie istniejącymi trasami w koncentratorze wirtualnym**:

   Jeśli masz wstępnie istniejące trasy w sekcji routingu centrum w Azure Portal, musisz najpierw je usunąć, a następnie próbować utworzyć nowe tabele tras (dostępne w sekcji "tabele tras" centrum w Azure Portal).

* **Podstawowa klienci wirtualnych sieci WAN ze wstępnie istniejącymi trasami w koncentratorze wirtualnym**:

   Jeśli masz już istniejące trasy w sekcji routingu centrum w Azure Portal, musisz najpierw je usunąć, a następnie **uaktualnić** podstawową wirtualną sieć WAN do standardowej wirtualnej sieci WAN. Zobacz [uaktualnianie wirtualnej sieci WAN z warstwy Podstawowa do standardowa](upgrade-virtual-wan.md).

## <a name="hub-reset"></a><a name="reset"></a>Resetowanie centrum

**Resetowanie** koncentratora wirtualnego jest dostępne tylko w Azure Portal. Resetowanie umożliwia przełączenie wszelkich zakończonych niepowodzeniem zasobów, takich jak tabele tras, router centralny lub zasób koncentratora wirtualnego z powrotem do stanu aprowizacji Rightful. Rozważ zresetowanie centrum przed skontaktowaniem się z firmą Microsoft w celu uzyskania pomocy technicznej. Ta operacja nie resetuje żadnych bram w koncentratorze wirtualnym.

## <a name="additional-considerations"></a><a name="considerations"></a>Dodatkowe zagadnienia

Podczas konfigurowania routingu wirtualnego sieci WAN należy wziąć pod uwagę następujące kwestie:

* Wszystkie połączenia gałęzi (punkt-lokacja, lokacja-lokacja i ExpressRoute) muszą być skojarzone z domyślną tabelą tras. W ten sposób wszystkie gałęzie będą uczyć się tych samych prefiksów.
* Wszystkie połączenia gałęzi muszą propagować trasy do tego samego zestawu tabel tras. Na przykład jeśli zdecydujesz, że gałęzie powinny być propagowane do domyślnej tabeli tras, ta konfiguracja powinna być spójna dla wszystkich gałęzi. W związku z tym wszystkie połączenia skojarzone z domyślną tabelą tras będą mogły dotrzeć do wszystkich gałęzi.
* Rozgałęzienie do gałęzi za pośrednictwem zapory platformy Azure nie jest obecnie obsługiwane.
* W przypadku korzystania z zapory platformy Azure w wielu regionach wszystkie sieci wirtualne szprych muszą być skojarzone z tą samą tabelą tras. Na przykład posiadanie podzestawu sieci wirtualnych przez zaporę platformy Azure, podczas gdy inne sieci wirtualnych pomijanie zapory platformy Azure w tym samym koncentratorze wirtualnym nie jest możliwe.
* Pojedynczy adres IP następnego przeskoku można skonfigurować dla każdego połączenia z siecią wirtualną.

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować Routing, zobacz [jak skonfigurować Routing koncentratora wirtualnego](how-to-virtual-hub-routing.md).
* Aby uzyskać więcej informacji na temat wirtualnej sieci WAN, zobacz [często zadawane pytania](virtual-wan-faq.md).
