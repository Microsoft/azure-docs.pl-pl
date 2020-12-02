---
title: VPN Gateway klasyczny do Menedżer zasobów migracji | Microsoft Docs
description: Ta strona zawiera omówienie VPN Gateway klasycznej do Menedżer zasobów migracji.
documentationcenter: na
services: vpn-gateway
author: amsriva
manager: rossort
editor: amsriva
ms.assetid: caa8eb19-825a-4031-8b49-18fbf3ebc04e
ms.service: vpn-gateway
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/06/2020
ms.author: amsriva
ms.openlocfilehash: c9d7fb8be1894ffa5f8c35e16e1ed3aa0949b3ff
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96488208"
---
# <a name="vpn-gateway-classic-to-resource-manager-migration"></a>VPN Gateway klasyczny do Menedżer zasobów migracji
Bramy sieci VPN można teraz migrować z klasycznego do Menedżer zasobów model wdrażania. Więcej informacji o Azure Resource Manager [funkcjach i korzyściach](../azure-resource-manager/management/overview.md)można znaleźć w artykule. W tym artykule szczegółowo opisano sposób migrowania z wdrożeń klasycznych do nowszej wersji Menedżer zasobów opartej na modelu. 

Bramy sieci VPN są migrowane w ramach migracji wirtualnej z klasycznej do Menedżer zasobów. Ta migracja wykonuje jedną sieć wirtualną jednocześnie. Nie ma dodatkowych wymagań w zakresie narzędzi lub wymagań wstępnych migracji. Kroki migracji są identyczne z istniejącą migracją sieci wirtualnej i są udokumentowane na [stronie migracji zasobów IaaS](../virtual-machines/migration-classic-resource-manager-ps.md). Podczas migracji nie ma żadnego przestoju ścieżki danych i w ten sposób istniejące obciążenia byłyby nadal działać bez utraty łączności lokalnej podczas migracji. Publiczny adres IP skojarzony z bramą sieci VPN nie zmienia się w trakcie procesu migracji. Oznacza to, że nie trzeba ponownie konfigurować routera lokalnego po zakończeniu migracji.  

Model w Menedżer zasobów różni się od modelu klasycznego i składa się z bram sieci wirtualnych, bram sieci lokalnej i zasobów połączeń. Reprezentują one samą bramę sieci VPN, lokację lokalną reprezentującą lokalnie lokalną przestrzeń adresową i łączność między nimi. Po zakończeniu migracji bramy nie będą dostępne w modelu klasycznym, a wszystkie operacje zarządzania na bramach sieci wirtualnej, bramach sieci lokalnej i obiektach połączeń należy wykonać przy użyciu modelu Menedżer zasobów.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze
Najczęstsze scenariusze łączności sieci VPN są objęte klasycznym Menedżer zasobów migracją. Obsługiwane scenariusze obejmują:

* Wskaż łączność z lokacją
* Łączność między lokacjami i VPN Gateway podłączonymi do lokalizacji lokalnej
* Łączność między dwiema sieci wirtualnych przy użyciu bram sieci VPN
* Wiele sieci wirtualnych połączonych z tą samą lokalizacją lokalną
* Łączność między lokacjami
* Wymuszone tunelowanie włączone sieci wirtualnych

Scenariusze, które nie są obsługiwane, to m.in.:  

* Sieć wirtualna z usługą ExpressRoute Gateway i VPN Gateway nie jest obecnie obsługiwana.
* Scenariusze tranzytowe, w przypadku których rozszerzenia maszyn wirtualnych są połączone z serwerami lokalnymi. Ograniczenia dotyczące łączności między sieciami VPN są szczegółowo opisane poniżej.

> [!NOTE]
> Walidacja CIDR w modelu Menedżer zasobów jest bardziej rygorystyczna niż w modelu klasycznym. Przed przeprowadzeniem migracji upewnij się, że klasyczne zakresy adresów są zgodne z prawidłowym formatem CIDR przed rozpoczęciem migracji. Można sprawdzić poprawność routingu CIDR przy użyciu wszelkich typowych modułów sprawdzania poprawności CIDR. Po przeprowadzeniu migracji Lokacje wirtualne lub lokalne z nieprawidłowymi zakresami CIDR spowodują niepowodzenie stanu.
> 
> 

## <a name="vnet-to-vnet-connectivity-migration"></a>Migracja połączeń z sieci wirtualnej do sieci wirtualnej
Połączenie między sieciami wirtualnymi w klasycznych zostało osiągnięte przez utworzenie reprezentacji lokacji lokalnej połączonej sieci wirtualnej. Klienci musieli utworzyć dwie lokacje lokalne, które reprezentują dwie sieci wirtualnych, które muszą być połączone ze sobą. Następnie zostały one połączone z odpowiednim sieci wirtualnych przy użyciu tunelu IPsec w celu ustanowienia łączności między tymi dwoma sieci wirtualnych. Ten model ma problemy związane z zarządzaniem, ponieważ wszystkie zmiany zakresu adresów w jednej sieci wirtualnej muszą być również utrzymywane w odpowiedniej reprezentacji lokacji lokalnej. W modelu Menedżer zasobów to obejście nie jest już potrzebne. Połączenie między tymi dwoma sieci wirtualnych można uzyskać bezpośrednio przy użyciu typu połączenia "Vnet2Vnet" w zasobie połączenia. 

![Zrzut ekranu migracji sieci wirtualnej do sieci wirtualnej.](./media/vpn-gateway-migration/migration1.png)

Podczas migracji sieci wirtualnej wykryjemy, że połączona jednostka z bieżącą wirtualną siecią VNet jest inną sieć wirtualną i upewnij się, że po zakończeniu migracji obydwu sieci wirtualnych nie będą już wyświetlane dwie lokacje lokalne reprezentujące drugą sieć wirtualną. Klasyczny model dwóch bram sieci VPN, dwie lokacje lokalne i dwa połączenia między nimi są przekształcane w model Menedżer zasobów z dwoma bramami sieci VPN i dwoma połączeniami typu Vnet2Vnet.

## <a name="transit-vpn-connectivity"></a>Tranzytowa łączność z siecią VPN
Bramy sieci VPN można skonfigurować w topologii, tak aby łączność lokalna dla sieci wirtualnej była osiągana przez połączenie z inną siecią wirtualną, która jest bezpośrednio podłączona do lokalnego. Jest to tranzytowa łączność z siecią VPN, w której wystąpienia w pierwszej sieci wirtualnej są połączone z zasobami lokalnymi przez przetransferowanie do bramy sieci VPN w połączonej podsieci wirtualnej, która jest bezpośrednio podłączona do lokalnego. Aby osiągnąć tę konfigurację w klasycznym modelu wdrażania, należy utworzyć lokację lokalną, która ma zagregowane prefiksy reprezentujące zarówno połączoną sieć wirtualną, jak i lokalną przestrzeń adresową. Ta reprezentacja lokacji lokalnej jest następnie połączona z siecią wirtualną w celu uzyskania łączności tranzytowej. Ten model klasyczny ma także podobne wyzwania związane z zarządzaniem, ponieważ wszelkie zmiany w zakresie adresów lokalnych muszą być również utrzymywane w lokacji lokalnej reprezentującej zagregowaną sieć wirtualną i lokalną. Wprowadzenie obsługi protokołu BGP w Menedżer zasobów obsługiwanych bram upraszcza zarządzanie, ponieważ połączone bramy mogą uczyć się tras od lokalnych, bez konieczności ręcznej modyfikacji prefiksów.

![Zrzut ekranu scenariusza routingu tranzytowego.](./media/vpn-gateway-migration/migration2.png)

Ze względu na to, że przekształcamy sieć wirtualną na łączność sieci wirtualnej bez konieczności używania lokacji lokalnych, scenariusz przesyłania utraci łączność lokalną dla sieci wirtualnej, która jest połączona bezpośrednio z lokalną. Utrata łączności można wyeliminować na dwa sposoby, po zakończeniu migracji: 

* Włącz protokół BGP na bramach sieci VPN połączonych ze sobą i lokalnymi. Włączenie protokołu BGP przywraca łączność bez żadnej innej zmiany konfiguracji od momentu, gdy trasy są rozwiązane i anonsowane między bramami sieci wirtualnej. Należy zauważyć, że opcja protokołu BGP jest dostępna tylko w jednostkach SKU w wersji Standard i wyższych.
* Nawiąż jawne połączenie z bramą sieci lokalnej, która reprezentuje lokalizację lokalną. Wymaga to również zmiany konfiguracji na routerze lokalnym w celu utworzenia i skonfigurowania tunelu IPsec.

## <a name="next-steps"></a>Następne kroki
Po zapoznaniu się z obsługą migracji za pomocą usługi VPN Gateway przejdź do obszaru [migracja zasobów IaaS z klasycznej do Menedżer zasobów](../virtual-machines/migration-classic-resource-manager-ps.md) , aby rozpocząć pracę.