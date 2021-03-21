---
title: SAP HANA dostępność w regionach świadczenia usługi Azure | Microsoft Docs
description: Omówienie zagadnień dotyczących dostępności podczas uruchamiania SAP HANA na maszynach wirtualnych platformy Azure w wielu regionach świadczenia usługi Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 09/12/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f925e72493a94bcdbde64bdfbcef6acf83977f52
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101669725"
---
# <a name="sap-hana-availability-across-azure-regions"></a>Dostępność SAP HANA w różnych regionach świadczenia usługi Azure

W tym artykule opisano scenariusze związane z dostępnością SAP HANA w różnych regionach świadczenia usługi Azure. Ze względu na odległość między regionami platformy Azure skonfigurowanie dostępności SAP HANA w wielu regionach świadczenia usługi Azure obejmuje specjalne zagadnienia.

## <a name="why-deploy-across-multiple-azure-regions"></a>Dlaczego należy wdrożyć w wielu regionach platformy Azure

Regiony platformy Azure często są rozdzielone dużymi odległościami. W zależności od regionu geopolitycznego odległość między regionami platformy Azure może być setkami kilometrów lub nawet kilkoma tysiącami kilometrów, takimi jak w Stany Zjednoczone. Ze względu na odległość ruch sieciowy między zasobami, które są wdrażane w dwóch różnych regionach świadczenia usługi Azure, napotyka duże opóźnienia sieci. Opóźnienie jest wystarczające do wykluczania synchronicznej wymiany danych między dwoma wystąpieniami SAP HANA w ramach typowych obciążeń SAP. 

Z drugiej strony organizacje często mają wymagania dotyczące odległości między lokalizacją podstawowego centrum danych i dodatkowym centrum danych. Zapotrzebowanie na odległość ułatwia zapewnienie dostępności, jeśli klęska żywiołowa występuje w szerszym miejscu geograficznym. Przykłady obejmują huragany), które osiągnęły Karaibów i Florida we wrześniu i październiku 2017. Organizacja może mieć co najmniej wymaganą minimalną odległość. W przypadku większości klientów platformy Azure definicja minimalnej odległości wymaga zaprojektowania pod kątem dostępności w różnych [regionach platformy Azure](https://azure.microsoft.com/regions/). Ze względu na to, że odległość między dwoma regionami świadczenia usługi Azure jest zbyt duża, aby można było używać trybu replikacji synchronicznej HANA, wymagania RTO i RPO mogą spowodować wdrożenie konfiguracji dostępności w jednym regionie, a następnie uzupełnienie z dodatkowymi wdrożeniami w drugim regionie.

Innym aspektem do uwzględnienia w tym scenariuszu jest przejście w tryb failover i przekierowanie klienta. Przyjęto założenie, że tryb failover między wystąpieniami SAP HANA w dwóch różnych regionach platformy Azure zawsze jest ręcznym trybem failover. Ponieważ tryb replikacji SAP HANA replikacji systemu jest ustawiony na asynchroniczne, istnieje możliwość, że dane zatwierdzone w podstawowym wystąpieniu platformy HANA nie zostały jeszcze udostępnione w pomocniczym wystąpieniu platformy HANA. W związku z tym automatyczne przełączanie w tryb failover nie jest opcją dla konfiguracji, w której replikacja jest asynchroniczna. Nawet w przypadku ręcznego kontrolowania trybu failover, jak w ćwiczeniu pracy awaryjnej, należy podjąć środki, aby upewnić się, że wszystkie przekazane dane po stronie podstawowej przechodzą do wystąpienia pomocniczego przed ręcznym przechodzeniem do innego regionu platformy Azure.
 
Usługa Azure Virtual Network używa innego zakresu adresów IP. Adresy IP są wdrażane w drugim regionie platformy Azure. W związku z tym należy zmienić konfigurację klienta SAP HANA lub zaleca się utworzenie kroków w celu zmiany rozpoznawania nazw. Dzięki temu klienci są przekierowywani do adresu IP nowej lokacji dodatkowej. Aby uzyskać więcej informacji, zapoznaj się z tematem [odzyskiwanie połączenia klienta w artykule SAP po przejęciu](https://help.sap.com/doc/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/c93a723ceedc45da9a66ff47672513d3.html).   

## <a name="simple-availability-between-two-azure-regions"></a>Prosta dostępność między dwoma regionami świadczenia usługi Azure

W ramach jednego regionu można zrezygnować z konfigurowania żadnej konfiguracji dostępności, ale nadal będzie to miało miejsce w przypadku wystąpienia awarii. Typowe przypadki takich scenariuszy to systemy nieprodukcyjne. Mimo że system nie działa przez pół dnia, a nawet dzień jest trwały, nie można zezwolić na niedostępność systemu przez 48 godzin. Aby konfiguracja była tańsza, należy uruchomić inny system, który jest jeszcze mniej ważny w maszynie wirtualnej. Inne funkcje systemowe jako miejsce docelowe. Możesz również zmienić rozmiar maszyny wirtualnej w regionie pomocniczym, aby była mniejsza, i wybrać opcję nieładowania danych. Ponieważ przejście w tryb failover jest wykonywane ręcznie i wiąże się z tym wiele kroków, aby zakończyć pracę awaryjną stosu aplikacji, dodatkowy czas na zamknięcie maszyny wirtualnej, zmianę jej rozmiaru, a następnie ponowne uruchomienie maszyny wirtualnej jest akceptowalne.

Jeśli używasz scenariusza udostępniania elementu docelowego odzyskiwania po awarii za pomocą systemu pytań i odpowiedzi na jednej maszynie wirtualnej, musisz wziąć pod uwagę następujące zagadnienia:

- Istnieją dwa [tryby działania](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html) z delta_datashipping i logreplay, które są dostępne dla takiego scenariusza
- Oba tryby operacji mają różne wymagania dotyczące pamięci bez wstępnego ładowania danych
- Delta_datashipping może wymagać znacznie mniejszej ilości pamięci bez opcji wstępnego ładowania, która może wymagać logreplay. Zobacz rozdział 4,3 dokumentu SAP [Jak przeprowadzić replikację systemu dla SAP HANA](https://archive.sap.com/kmuuid2/9049e009-b717-3110-ccbd-e14c277d84a3/How%20to%20Perform%20System%20Replication%20for%20SAP%20HANA.pdf)
- Wymagania dotyczące pamięci trybu operacji logreplay bez wstępnego ładowania nie są deterministyczne i są zależne od załadowanych struktur magazynu kolumn. W skrajnych przypadkach może być wymagane 50% pamięci wystąpienia podstawowego. Pamięć dla trybu operacji logreplay jest niezależna od tego, czy wybrano opcję załadowania wstępnie danych.


![Diagram dwóch maszyn wirtualnych w dwóch regionach](./media/sap-hana-availability-two-region/two_vm_HSR_async_2regions_nopreload.PNG)

> [!NOTE]
> W tej konfiguracji nie można zapewnić punktu odzyskiwania = 0, ponieważ tryb replikacji systemu platformy HANA jest asynchroniczny. Jeśli musisz podać cel punktu odzyskiwania = 0, taka konfiguracja nie jest konfiguracją wyboru.

Niewielka zmiana, którą można wprowadzić w konfiguracji, może spowodować skonfigurowanie danych jako wstępnego ładowania. Jednak w przypadku ręcznego charakteru trybu failover i faktu, że warstwy aplikacji również muszą przejść do drugiego regionu, może nie mieć sensu wstępnego ładowania danych. 

## <a name="combine-availability-within-one-region-and-across-regions"></a>Łączenie dostępności w jednym regionie i w różnych regionach 

W ramach i między regionami mogą być dostępne następujące czynniki:

- Wymaganie celu punktu odzyskiwania = 0 w regionie świadczenia usługi Azure.
- Organizacja nie jest skłonna ani nie może mieć operacji globalnych, których dotyczy główna katastrofami, która ma wpływ na większy region. Miało to zastosowanie w przypadku niektórych huragany), które osiągnęły Karaibów w ciągu ostatnich kilku lat.
- Regulacje, które wymagają odległości między lokacjami podstawowymi i dodatkowymi, które są wyraźnie poza tymi strefami dostępności platformy Azure.

W takich przypadkach można skonfigurować, które rozwiązanie SAP wywołuje [SAP HANA konfigurację replikacji systemu wielowarstwowego](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/ca6f4c62c45b4c85a109c7faf62881fc.html) za pomocą replikacji systemu Hana. Architektura będzie wyglądać następująco:

![Diagram trzech maszyn wirtualnych na dwa regiony](./media/sap-hana-availability-two-region/three_vm_HSR_async_2regions_ha_and_dr.PNG)

W oprogramowaniu SAP wprowadzono [replikację systemu](https://help.sap.com/viewer/42668af650f84f9384a3337bcd373692/2.0.03/en-US/0b2c70836865414a8c65463180d18fec.html) wieloskładnikowego za pomocą platformy Hana 2,0 SPS3. Wieloelementowa replikacja systemu oferuje pewne korzyści w scenariuszach aktualizacji. Na przykład nie ma wpływu na lokację odzyskiwania po awarii (region 2), gdy dodatkowa lokacja HA nie działa na potrzeby konserwacji lub aktualizacji. Więcej informacji na temat replikacji systemu HANA dla wielu obiektów docelowych można znaleźć [tutaj](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.03/en-US/ba457510958241889a459e606bbcf3d3.html).
Możliwa architektura z replikacją wiele obiektów docelowych będzie wyglądać następująco:

![Diagram trzech maszyn wirtualnych w dwóch regionach Milti-Target](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_3VMs.PNG)

Jeśli organizacja ma wymagania dotyczące gotowości wysokiej dostępności w drugim regionie platformy Azure (DR), wówczas architektura będzie wyglądać następująco:

![Diagram przedstawiający organizację, która ma wymagania dotyczące gotowości wysokiej dostępności w drugim regionie platformy Azure.](./media/sap-hana-availability-two-region/saphanaavailability_hana_system_2region_HA_and_DR_multitarget_4VMs.PNG)


W przypadku korzystania z logreplay jako trybu operacji ta konfiguracja zapewnia cel punktu odzyskiwania = 0, z niską RTO w regionie podstawowym. Ta konfiguracja zapewnia także znośnego cel punktu odzyskiwania w przypadku, gdy przejdziesz do drugiego regionu. Czasy RTO w drugim regionie zależą od tego, czy dane są wstępnie załadowane. Wielu klientów korzysta z maszyny wirtualnej w regionie pomocniczym do uruchamiania systemu testowego. W tym przypadku użycia nie można wstępnie załadować danych.

> [!IMPORTANT]
> Tryby operacji między różnymi warstwami muszą być jednorodne. **Nie** można użyć logreply jako trybu działania między warstwą 1 i 2 i delta_datashipping do dostarczania warstwy 3. Można wybrać tylko jeden lub inny tryb operacji, który musi być spójny dla wszystkich warstw. Ponieważ delta_datashipping nie jest odpowiednia do zapewnienia punktu odzyskiwania = 0, jedynym rozsądnym trybem operacji dla takiej konfiguracji wielowarstwowej pozostaje logreplay. Aby uzyskać szczegółowe informacje o trybach operacji i niektórych ograniczeniach, zobacz [tryby operacji artykułów SAP na potrzeby replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/627bd11e86c84ec2b9fcdf585d24011c.html). 

## <a name="next-steps"></a>Następne kroki

Aby uzyskać wskazówki krok po kroku dotyczące konfigurowania tych konfiguracji na platformie Azure, zobacz:

- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Wysoka dostępność dla SAP HANA przy użyciu replikacji systemowej](https://blogs.sap.com/2018/01/08/your-sap-on-azure-part-4-high-availability-for-sap-hana-using-system-replication/)

 



 
  
