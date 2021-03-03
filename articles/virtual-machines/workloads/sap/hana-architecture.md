---
title: Architektura SAP HANA na platformie Azure (duże wystąpienia) | Microsoft Docs
description: Architektura wdrażania SAP HANA na platformie Azure (duże wystąpienia).
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 05/25/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: f45b84360c941e879461500a033ada3642fd5cbb
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101671623"
---
# <a name="sap-hana-large-instances-architecture-on-azure"></a>Architektura SAP HANA (duże wystąpienia) na platformie Azure

Na wysokim poziomie rozwiązanie SAP HANA na platformie Azure (duże wystąpienia) zawiera warstwę aplikacji SAP na maszynach wirtualnych. Warstwa bazy danych znajduje się na sprzęcie skonfigurowanym przez środowisko SAP w ramach dużej instancji w tym samym regionie świadczenia usługi Azure, który jest połączony z usługą Azure IaaS.

> [!NOTE]
> Wdróż warstwę aplikacji SAP w tym samym regionie świadczenia usługi Azure, w której znajduje się warstwa SAP DBMS. Ta zasada jest dobrze udokumentowana w opublikowanych informacjach o obciążeniach SAP na platformie Azure. 

Ogólna architektura SAP HANA na platformie Azure (duże wystąpienia) zapewnia konfigurację sprzętową z certyfikatem TDI protokołu SAP, która jest niezwirtualizowanym serwerem o wysokiej wydajności dla SAP HANA bazy danych. Zapewnia również możliwość i elastyczność platformy Azure do skalowania zasobów dla warstwy aplikacji SAP, aby spełniały Twoje potrzeby.

![Omówienie architektury SAP HANA na platformie Azure (duże wystąpienia)](./media/hana-overview-architecture/image1-architecture.png)

Pokazana architektura jest podzielona na trzy sekcje:

- **Prawo**: pokazuje lokalną infrastrukturę, która uruchamia różne aplikacje w centrach danych, dzięki czemu użytkownicy końcowi mogą uzyskiwać dostęp do aplikacji biznesowych, takich jak SAP. W idealnym przypadku ta infrastruktura lokalna jest połączona z platformą Azure za pomocą usługi [ExpressRoute](https://azure.microsoft.com/services/expressroute/).

- **Wyśrodkuj**: pokazuje usługę Azure IaaS i, w tym przypadku, używanie maszyn wirtualnych do HOSTOWANIA oprogramowania SAP lub innych aplikacji, które używają SAP HANA jako systemu DBMS. Mniejsze wystąpienia HANA działające z pamięcią dostarczaną przez maszyny wirtualne są wdrażane na maszynach wirtualnych razem z ich warstwami aplikacji. Aby uzyskać więcej informacji o maszynach wirtualnych, zobacz [maszyny wirtualne](https://azure.microsoft.com/services/virtual-machines/).

   Usługi sieciowe platformy Azure są używane do grupowania systemów SAP wraz z innymi aplikacjami w sieciach wirtualnych. Te sieci wirtualne łączą się z systemami lokalnymi, a także SAP HANA na platformie Azure (duże wystąpienia).

   W przypadku aplikacji SAP NetWeaver i baz danych, które są obsługiwane do uruchamiania na platformie Azure, zobacz [uwagi dotyczące pomocy technicznej sap #1928533 — aplikacje SAP na platformie Azure: obsługiwane produkty i typy maszyn wirtualnych platformy Azure](https://launchpad.support.sap.com/#/notes/1928533). Aby uzyskać dokumentację dotyczącą sposobu wdrażania rozwiązań SAP na platformie Azure, zobacz:

  -  [Korzystanie z oprogramowania SAP na maszynach wirtualnych z systemem Windows](./get-started.md?toc=/azure/virtual-machines/linux/toc.json)
  -  [Korzystanie z rozwiązań SAP na maszynach wirtualnych platformy Azure](get-started.md)

- **Left**: przedstawia sprzęt z certyfikatem TDI SAP HANA w sygnaturze dużej instancji platformy Azure. Jednostki dużego wystąpienia HANA są połączone z sieciami wirtualnymi subskrypcji platformy Azure za pomocą tej samej technologii, co łączność ze stanem lokalnym z platformą Azure. Od maja 2019 wprowadzono optymalizację umożliwiającą komunikację między jednostkami dużej instancji HANA i maszynami wirtualnymi platformy Azure bez udziału bramy ExpressRoute. Ta optymalizacja o nazwie ExpressRoute Fast jest wyświetlana w tej architekturze (czerwona linia). 

Sygnatura dużego wystąpienia platformy Azure łączy następujące składniki:

- **Obliczanie**: serwery oparte na różnych generacjach procesorów Intel Xeon, które zapewniają niezbędną możliwość obliczeniową i są SAP HANA certyfikowane.
- **Sieć**: ujednolicona Sieć szkieletowa o dużej szybkości, która umożliwia łączenie składników obliczeniowych, magazynu i sieci LAN.
- **Magazyn**: infrastruktura magazynów, do której dostęp odbywa się za pomocą ujednoliconej sieci szkieletowej. Określona pojemność magazynu zależy od konkretnej SAP HANA w ramach wdrożonej konfiguracji platformy Azure (duże wystąpienia). Większa pojemność magazynu jest dostępna z dodatkowym miesięcznym kosztem.

W ramach infrastruktury wielodostępnej sygnatury dużego wystąpienia klienci są wdrażani jako izolowane dzierżawy. Podczas wdrażania dzierżawy należy nazwać subskrypcję platformy Azure w ramach rejestracji na platformie Azure. Ta subskrypcja platformy Azure jest rozliczana z dużym wystąpieniem usługi HANA. Dzierżawcy mają 1:1 relacje z subskrypcją platformy Azure. W przypadku sieci można uzyskać dostęp do jednostki dużego wystąpienia HANA wdrożonej w jednej dzierżawie w jednym regionie świadczenia usługi Azure z różnych sieci wirtualnych należących do różnych subskrypcji platformy Azure. Te subskrypcje platformy Azure muszą należeć do tej samej rejestracji platformy Azure. 

Podobnie jak w przypadku maszyn wirtualnych, SAP HANA na platformie Azure (duże wystąpienia) są oferowane w wielu regionach świadczenia usługi Azure. Aby zaoferować możliwości odzyskiwania po awarii, możesz wybrać opcję wyboru. Różne sygnatury dużych wystąpień w jednym regionie geograficznym są połączone ze sobą. Na przykład sygnatury dużych wystąpień HANA w zachodnich stanach USA i Wschodnie stany USA są połączone za pomocą dedykowanego linku sieciowego do replikacji odzyskiwania po awarii. 

Podobnie jak można wybrać różne typy maszyn wirtualnych za pomocą usługi Azure Virtual Machines, można wybrać różne jednostki SKU z dużego wystąpienia HANA, które są dostosowane do różnych typów obciążeń SAP HANA. SAP stosuje stosunek pamięci do procesora dla różnych obciążeń na podstawie generacji procesorów firmy Intel. W poniższej tabeli przedstawiono oferowane typy jednostek SKU.

Dostępne jednostki SKU dostępne w jednostkach SKU można znaleźć [dla elementu HLI](hana-available-skus.md).

**Następne kroki**
- [SAP HANA (duże wystąpienia) Architektura sieci](hana-network-architecture.md)
