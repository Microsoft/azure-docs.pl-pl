---
title: Architektury łączności wirtualnej sieci WAN i SD-WAN
titleSuffix: Azure Virtual WAN
description: Dowiedz się więcej na temat łączenia prywatnych SD-WAN z wirtualną siecią WAN platformy Azure
services: virtual-wan
author: skishen525
ms.service: virtual-wan
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: sukishen
ms.openlocfilehash: ea9ddd05fe6fc745a3eefc29ab4f1d6aababc936
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94564705"
---
# <a name="sd-wan-connectivity-architecture-with-azure-virtual-wan"></a>Architektura łączności między SIECIami wirtualnymi z usługą Azure Virtual WAN

Wirtualna sieć WAN platformy Azure to usługa sieciowa, która łączy wiele usług łączności w chmurze i zabezpieczeń z pojedynczym interfejsem operacyjnym. Te usługi obejmują rozgałęzienie (za pośrednictwem sieci VPN typu lokacja-lokacja), użytkowników zdalnych (sieci VPN punkt-lokacja), połączeń prywatnych (ExpressRoute), łączności przechodniej wewnątrz chmury dla sieci wirtualnych, sieci VPN i ExpressRoute łączności, routingu, zapory platformy Azure i szyfrowania na potrzeby połączeń prywatnych.

Mimo że wirtualna sieć WAN platformy Azure to oprogramowanie sieci WAN zdefiniowane w oprogramowaniu (SD-WAN), jest również przeznaczone do zapewniania bezproblemowego połączenia z opartymi na lokalnym technologią i technologiami SD-WAN. Wiele takich usług jest oferowanych przez nasz ekosystem [wirtualnych sieci WAN](virtual-wan-locations-partners.md) i partnerów usług zarządzanych sieci platformy Azure [(msp)](../networking/networking-partners-msp.md). Przedsiębiorstwa, które przekształcają prywatne sieci WAN do SD-WAN, są dostępne w przypadku łączenia ich prywatnych SD-WAN z wirtualną siecią WAN platformy Azure. Przedsiębiorstwa mogą wybierać spośród następujących opcji:

* Model bezpośredniego połączenia
* Model bezpośredniego połączenia z usługą urządzenie WUS-in-VWAN-Hub
* Pośredni model połączeń
* Zarządzany model hybrydowej sieci WAN przy użyciu ulubionych [msp](../networking/networking-partners-msp.md) dostawcy usług zarządzanych

We wszystkich tych przypadkach połączenia wirtualnej sieci WAN z SD-WAN są podobne od łączności, ale mogą się różnić w zależności od aranżacji i działania.

## <a name="direct-interconnect-model"></a><a name="direct"></a>Model bezpośredniego połączenia

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct.png" alt-text="Model bezpośredniego połączenia":::

W tym modelu architektury sprzęt lokalny (CPE) usługi SD jest podłączony bezpośrednio do wirtualnych koncentratorów sieci WAN za pośrednictwem połączeń IPsec. Gałąź CPE może być również połączona z innymi gałęziami za pośrednictwem prywatnej SD-WAN lub wykorzystać wirtualną sieć WAN do połączenia z gałęzią. Gałęzie, które muszą uzyskać dostęp do swoich obciążeń na platformie Azure, będą mogły bezpośrednio i bezpiecznie uzyskiwać dostęp do platformy Azure za pośrednictwem tuneli IPsec zakończonych w wirtualnych centrach sieci WAN.

Partnerzy usługi SD-WAN CPE mogą włączyć automatyzację, aby zautomatyzować normalne żmudnym i podatne na błędy połączenia IPsec z urządzeń CPE. Automatyzacja umożliwia kontrolerowi SD-WAN komunikowanie się z platformą Azure za pośrednictwem wirtualnego interfejsu API sieci WAN w celu skonfigurowania wirtualnych witryn sieci WAN, a także wypychania niezbędnej konfiguracji tunelu IPsec do rozgałęzienia CPEs. Zobacz [wskazówki dotyczące automatyzacji](virtual-wan-configure-automation-providers.md) , aby uzyskać opis automatyzacji połączeń wirtualnej sieci WAN przez różnych partnerów SD-WAN.

URZĄDZENIE typu SD-WAN jest nadal miejscem, w którym Optymalizacja ruchu oraz wybór ścieżki są implementowane i wymuszane. 

W tym modelu pewna niezależna od dostawcy Optymalizacja ruchu oparta na charakterystykach ruchu w czasie rzeczywistym może nie być obsługiwana, ponieważ łączność z wirtualną siecią WAN odbywa się za pośrednictwem protokołu IPsec, a sieć VPN IPsec jest przerywana na wirtualnej bramie sieci VPN. Na przykład można wybrać ścieżkę dynamiczną na urządzeniu CPE, ponieważ odgałęzienie odnosi się do różnych informacji o pakietach sieciowych z innym węzłem SD-WAN, a tym samym zidentyfikowanie najlepszego linku do użycia w przypadku różnych priorytetów ruchu dynamicznego w gałęzi. Ta funkcja może być przydatna w obszarach, w których wymagana jest optymalizacja między ostatnimi milami (rozgałęzienie do najbliższej firmy Microsoft).

Dzięki wirtualnej sieci WAN użytkownicy mogą uzyskać wybór ścieżki platformy Azure, która jest ścieżką opartą na zasadach obejmującą wiele linków usługodawcy internetowego z gałęzi CPE do wirtualnych bram sieci VPN w sieci WAN. Wirtualna sieć WAN umożliwia konfigurowanie wielu linków (ścieżek) z tego samego rozgałęzienia "SD-WAN" CPE; Każde łącze reprezentuje połączenie podwójnego tunelu z unikatowego publicznego adresu IP z systemem SD-WAN CPE do dwóch różnych wystąpień bramy sieci VPN usługi Azure Virtual WAN. Dostawcy SD-WAN mogą zaimplementować najbardziej optymalną ścieżkę do platformy Azure, na podstawie zasad ruchu ustawionych przez aparat zasad w łączach CPE. Na zakończenie na platformie Azure wszystkie połączenia przychodzące są traktowane jako równe.

## <a name="direct-interconnect-model-with-nva-in-vwan-hub"></a><a name="direct"></a>Model bezpośredniego połączenia z usługą urządzenie WUS-in-VWAN-Hub

:::image type="content" source="./media/sd-wan-connectivity-architecture/direct-nva.png" alt-text="Model bezpośredniego połączenia z usługą urządzenie WUS-in-VWAN-Hub":::

Ten model architektury obsługuje wdrożenie [wirtualnego urządzenia sieciowego (urządzenie WUS) innej firmy bezpośrednio w koncentratorze wirtualnym](./about-nva-hub.md). Pozwala to klientom, którzy chcą połączyć swoją gałąź CPE z tą samą marką urządzenie WUS w koncentratorze wirtualnym, dzięki czemu mogą oni korzystać z zastrzeżonych, kompleksowych funkcji SD-WAN podczas łączenia się z obciążeniami platformy Azure. 

Kilku wirtualnych partnerów sieci WAN działały w celu zapewnienia środowiska, które automatycznie konfiguruje urządzenie WUS w ramach procesu wdrażania. Po udostępnieniu urządzenie WUS w koncentratorze wirtualnym każda dodatkowa konfiguracja, która może być wymagana przez urządzenie WUS, musi zostać wykonana za pośrednictwem portalu partnerów urządzenie WUS lub aplikacji do zarządzania. Bezpośredni dostęp do urządzenie WUS jest niedostępny. Urządzeń WUS, które są dostępne do wdrożenia bezpośrednio w wirtualnym Centrum sieci WAN platformy Azure, są przeznaczone do użycia w koncentratorze wirtualnym. W przypadku partnerów, którzy obsługują usługi urządzenie WUS w centrum VWAN, a także ich przewodników wdrażania, zobacz artykuł dotyczący [partnerów sieci WAN](virtual-wan-locations-partners.md#partners-with-integrated-virtual-hub-offerings) .

URZĄDZENIE typu SD-WAN jest nadal miejscem, w którym Optymalizacja ruchu oraz wybór ścieżki są implementowane i wymuszane.
W tym modelu Optymalizacja ruchu przez dostawcę według własności w czasie rzeczywistym jest obsługiwana, ponieważ łączność z wirtualną siecią WAN odbywa się za pośrednictwem urządzenie WUS SD-WAN w centrum.

## <a name="indirect-interconnect-model"></a><a name="indirect"></a>Pośredni model połączeń

:::image type="content" source="./media/sd-wan-connectivity-architecture/indirect.png" alt-text="Pośredni model połączeń":::

W tym modelu architektury gałąź SD-WAN CPEs jest połączona pośrednio z wirtualnymi koncentratorami sieci WAN. Jak widać na ilustracji, wirtualne urządzenie CPE SD-WAN jest wdrażane w sieci wirtualnej przedsiębiorstwa. Ta wirtualna CPE jest z kolei podłączona do wirtualnych koncentratorów sieci WAN przy użyciu protokołu IPsec. Wirtualne urządzenie CPE służy jako brama SD-WAN na platformie Azure. Gałęzie, które muszą uzyskać dostęp do swoich obciążeń na platformie Azure, będą mogły uzyskiwać do nich dostęp za pośrednictwem bramy v-CPE.

Ze względu na to, że łączność z platformą Azure odbywa się za pośrednictwem bramy v-CPE (urządzenie WUS), cały ruch do i z usługi Azure obciążeń sieci wirtualnych do innych gałęzi SD-WAN przejdzie przez urządzenie WUS. W tym modelu użytkownik jest odpowiedzialny za zarządzanie i działanie urządzenie WUS SD-WAN, w tym wysokiej dostępności, skalowalności i routingu.
  
## <a name="managed-hybrid-wan-model"></a><a name="hybrid"></a>Zarządzany model hybrydowej sieci WAN

:::image type="content" source="./media/sd-wan-connectivity-architecture/hybrid.png" alt-text="Zarządzany model hybrydowej sieci WAN":::

W tym modelu architektury przedsiębiorstwa mogą korzystać z zarządzanej usługi SD-WAN oferowanej przez partnera dostawcy usług zarządzanych (MSP). Ten model jest podobny do modeli bezpośrednich lub pośrednich opisanych powyżej. Jednak w tym modelu projekt, aranżacja i operacje z SD-WAN są dostarczane przez dostawcę SD-WAN.

[Partnerzy MSP sieci platformy Azure](../networking/networking-partners-msp.md) mogą używać [platformy Azure Lighthouse](https://azure.microsoft.com/services/azure-lighthouse/) do implementowania usługi SD-WAN i wirtualnej sieci WAN w ramach subskrypcji platformy Azure klienta w przedsiębiorstwie, a także do kompleksowej hybrydowej sieci WAN w imieniu klienta. Te msp mogą być również w stanie zaimplementować platformę Azure ExpressRoute w wirtualnej sieci WAN i obsługiwać ją jako kompleksową usługę zarządzaną.

## <a name="additional-information"></a>Dodatkowe informacje

* [Uwzględnij często zadawane pytania](virtual-wan-faq.md)
* [Rozwiązywanie problemów z łącznością zdalną](work-remotely-support.md)