---
title: Omówienie SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Omówienie sposobu wdrażania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: bburns
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 01/04/2021
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 1c1cd4e7d65897634b5a8a8fa8be46275bbd4b88
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101676873"
---
#  <a name="what-is-sap-hana-on-azure-large-instances"></a>Co to jest oprogramowanie SAP HANA na platformie Azure — duże wystąpienia?

SAP HANA na platformie Azure (duże wystąpienia) to unikatowe rozwiązanie na platformie Azure. Oprócz zapewniania maszyn wirtualnych do wdrażania i uruchamiania SAP HANA system Azure oferuje możliwość uruchamiania i wdrażania SAP HANA na serwerach bez systemu operacyjnego, które są przeznaczone dla Ciebie. Rozwiązanie SAP HANA na platformie Azure (duże wystąpienia) kompiluje się w przypadku nieudostępnionego sprzętu hosta/serwera bez systemu operacyjnego, który jest przypisany do użytkownika. Sprzęt serwera jest osadzony w większych sygnaturach, które zawierają infrastrukturę obliczeniową/serwerową, sieciową i magazynową. SAP HANA na platformie Azure (duże wystąpienia) oferuje różne jednostki SKU serwera lub rozmiary. Jednostki mogą mieć 36 rdzeni procesora CPU firmy Intel i 768 GB pamięci i znajdować się w jednostkach, które mają do 480 rdzenie procesora Intel i do 24 TB pamięci.

Izolacja klienta w ramach sygnatury infrastruktury jest wykonywana w dzierżawach, które wyglądają następująco:

- **Sieć**: Izolacja klientów w stosie infrastruktury za pomocą sieci wirtualnych dla dzierżawy przypisanej do klienta. Dzierżawa jest przypisana do jednego klienta. Klient może mieć wielu dzierżawców. Izolacja sieci dzierżawców zabrania komunikacji sieciowej między dzierżawcami na poziomie sygnatury infrastruktury, nawet jeśli dzierżawy należą do tego samego klienta.
- **Składniki magazynu**: Izolacja przy użyciu maszyn wirtualnych magazynu z przypisanymi woluminami magazynowymi. Woluminy magazynu można przypisywać tylko do jednej maszyny wirtualnej magazynu. Maszyna wirtualna magazynu jest przypisana wyłącznie do jednej dzierżawy w stosie infrastruktury. W związku z tym woluminy magazynu przypisane do maszyny wirtualnej magazynu są dostępne tylko w jednej określonej i powiązanej dzierżawie. Nie są one widoczne między różnymi wdrożonymi dzierżawcami.
- **Serwer lub host**: serwer lub jednostka hosta nie są współużytkowane przez klientów lub dzierżawców. Serwer lub host wdrożony dla klienta to niepodzielna jednostka obliczeniowa bez systemu operacyjnego, która jest przypisana do jednej dzierżawy. *Nie* jest używane żadne partycjonowanie sprzętowe ani partycjonowanie, które może spowodować udostępnienie hosta lub serwera innemu klientowi. Woluminy magazynu przypisane do maszyny wirtualnej magazynu określonej dzierżawy są instalowane na takim serwerze. Dzierżawca może mieć jeden do wielu jednostek serwera z różnymi jednostkami SKU przypisanymi wyłącznie.
- Za pomocą sygnatury infrastruktury w ramach SAP HANA na platformie Azure (duże wystąpienia) wiele różnych dzierżawców jest wdrażanych i izolowanych nawzajem w oparciu o koncepcje dotyczące sieci, magazynu i mocy obliczeniowej. 


Te jednostki serwera bez systemu operacyjnego są obsługiwane do uruchamiania tylko SAP HANA. Warstwa aplikacji SAP lub warstwa oprogramowania do obsługi obciążeń są uruchamiane na maszynach wirtualnych. Sygnatury infrastruktury, które uruchamiają SAP HANA na platformie Azure (duże wystąpienia), są połączone z kością usług sieciowych platformy Azure. W ten sposób zapewniona jest łączność z niską opóźnieniami między jednostkami SAP HANA na platformie Azure (duże wystąpienia) i na maszynach wirtualnych.

Od stycznia 2021 różnice między dwiema różnymi zmianami sygnatur dużych wystąpień programu HANA i lokalizacją wdrożeń:

- "Poprawka 3" (Rev. 3): to sygnatury, które zostały udostępnione Klientowi do wdrożenia przed lipca 2019
- "Wersja 4" (Rev. 4): nowy projekt sygnatury wdrożony w pobliżu hostów maszyn wirtualnych platformy Azure, które są w tej chwili udostępniane w regionach świadczenia usługi Azure:
    -  Zachodnie stany USA 2 
    -  East US
    -  Wschód stany USA 2 (w dwóch Strefy dostępnościach)
    -  Południowo-środkowe stany USA (w dwóch Strefy dostępności)
    -  West Europe
    -  Europa Północna


Ten dokument jest jednym z kilku dokumentów, które obejmują SAP HANA na platformie Azure (duże wystąpienia). W tym dokumencie przedstawiono podstawową architekturę, obowiązki i usługi udostępniane przez rozwiązanie. Omówiono również możliwości wysokiego poziomu rozwiązania. W przypadku większości innych obszarów, takich jak sieć i łączność, cztery inne dokumenty obejmują szczegóły i informacje dotyczące przechodzenia do szczegółów. Dokumentacja SAP HANA na platformie Azure (duże wystąpienia) nie obejmuje aspektów instalacji i wdrożeń oprogramowania SAP NetWeaver na maszynach wirtualnych. Oprogramowanie SAP NetWeaver na platformie Azure jest omówione w oddzielnych dokumentach, które znajdują się w tym samym kontenerze dokumentacji platformy Azure. 


Różne dokumenty dotyczące dużych wystąpień programu HANA obejmują następujące obszary:

- [Omówienie i architektura SAP HANA (duże wystąpienia) na platformie Azure](hana-overview-architecture.md)
- [Infrastruktura i łączność SAP HANA (duże wystąpienia) na platformie Azure](hana-overview-infrastructure-connectivity.md)
- [Instalowanie i Konfigurowanie SAP HANA (duże wystąpienia) na platformie Azure](hana-installation.md)
- [SAP HANA (duże wystąpienia) wysoka dostępność i odzyskiwanie po awarii na platformie Azure](hana-overview-high-availability-disaster-recovery.md)
- [SAP HANA (duże wystąpienia) Rozwiązywanie problemów i monitorowanie na platformie Azure](troubleshooting-monitoring.md)
- [Wysoka dostępność skonfigurowana w SUSE przy użyciu STONITH](./ha-setup-with-stonith.md)
- [Kopia zapasowa i przywracanie systemu operacyjnego dla jednostek SKU typu II poprawki 3](./os-backup-type-ii-skus.md)
- [Zaoszczędź na dużych wystąpieniach platformy SAP HANA dzięki rezerwacji platformy Azure](../../../cost-management-billing/reservations/prepay-hana-large-instances-reserved-capacity.md)

**Następne kroki**
- Zapoznaj się [z postanowieniami](hana-know-terms.md)
