---
title: SAP HANA dostępność na maszynach wirtualnych platformy Azure — omówienie | Microsoft Docs
description: Opisuje operacje SAP HANA na natywnych maszynach wirtualnych platformy Azure.
services: virtual-machines-linux,virtual-machines-windows
documentationcenter: ''
author: msjuergent
manager: patfilot
editor: ''
tags: azure-resource-manager
keywords: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 03/05/2018
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 3589b4af06bc27a79c429bb0ccd743857651db97
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88653549"
---
# <a name="sap-hana-high-availability-for-azure-virtual-machines"></a>SAP HANA wysokiej dostępności dla maszyn wirtualnych platformy Azure

Możesz użyć wielu możliwości platformy Azure do wdrożenia baz danych o kluczowym znaczeniu, takich jak SAP HANA na maszynach wirtualnych platformy Azure. Ten artykuł zawiera wskazówki dotyczące uzyskiwania dostępności SAP HANA wystąpień hostowanych na maszynach wirtualnych platformy Azure. W tym artykule opisano kilka scenariuszy, które można zaimplementować przy użyciu infrastruktury platformy Azure w celu zwiększenia dostępności SAP HANA na platformie Azure. 

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule założono, że znasz już podstawy infrastruktury jako usługi (IaaS) na platformie Azure, w tym: 

- Jak wdrażać maszyny wirtualne lub sieci wirtualne za pośrednictwem Azure Portal lub programu PowerShell.
- Za pomocą międzyplatformowego interfejsu wiersza polecenia platformy Azure (CLI), w tym opcji używania szablonów JavaScript Object Notation (JSON).

W tym artykule przyjęto również założenie, że wiesz już, jak instalować SAP HANA wystąpienia, a także z wystąpieniami administratora i SAP HANA operacyjnym. Szczególnie ważne jest zapoznanie się z konfiguracją i operacjami replikacji systemu HANA. Obejmuje to zadania, takie jak tworzenie kopii zapasowych i przywracanie dla SAP HANA baz danych.

W tych artykułach zawarto informacje na temat korzystania z SAP HANA na platformie Azure:

- [Ręczna instalacja SAP HANA pojedynczego wystąpienia na maszynach wirtualnych platformy Azure](./hana-get-started.md)
- [Konfigurowanie replikacji systemu SAP HANA na maszynach wirtualnych platformy Azure](sap-hana-high-availability.md)
- [Tworzenie kopii zapasowych platformy SAP HANA na maszynach wirtualnych platformy Azure](./sap-hana-backup-guide.md)

Warto również zapoznać się z następującymi artykułami dotyczącymi SAP HANA:

- [Wysoka dostępność dla SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.02/en-US/6d252db7cdd044d19ad85b46e6c294a4.html)
- [Często zadawane pytania: wysoka dostępność dla SAP HANA](https://archive.sap.com/documents/docs/DOC-66702)
- [Wykonaj replikację systemu dla SAP HANA](https://archive.sap.com/documents/docs/DOC-47702)
- [SAP HANA 2,0 SPS 01 co nowego: wysoka dostępność](https://blogs.sap.com/2017/05/15/sap-hana-2.0-sps-01-whats-new-high-availability-by-the-sap-hana-academy/)
- [Zalecenia dotyczące sieci SAP HANA replikacji systemu](https://www.sap.com/documents/2016/06/18079a1c-767c-0010-82c7-eda71af511fa.html)
- [Replikacja systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/b74e16a9e09541749a745f41246a065e.html)
- [Ponowne uruchamianie usługi SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/cf10efba8bea4e81b1dc1907ecc652d3.html)
- [Konfigurowanie replikacji systemu SAP HANA](https://help.sap.com/viewer/6b94445c94ae495c83a19646e7c3fd56/2.0.01/en-US/676844172c2442f0bf6c8b080db05ae7.html)

Przed zdefiniowaniem architektury dostępności na platformie Azure zalecamy zapoznanie się z tematem Wdrażanie [maszyn wirtualnych](../../windows/manage-availability.md)na platformie Azure.

## <a name="service-level-agreements-for-azure-components"></a>Umowy dotyczące poziomu usług dla składników platformy Azure

Platforma Azure ma inne umowy SLA dostępność dla różnych składników, takich jak sieć, magazyn i maszyny wirtualne. Wszystkie umowy SLA są udokumentowane. Aby uzyskać więcej informacji, zobacz [Microsoft Azure umów dotyczących poziomu usług](https://azure.microsoft.com/support/legal/sla/). 

[Umowa SLA dla Virtual Machines](https://azure.microsoft.com/support/legal/sla/virtual-machines/v1_8/) opisuje trzy różne konfiguracje:

- Pojedyncza maszyna wirtualna korzystająca z [usługi Azure Premium dysków SSD](../../managed-disks-overview.md) dla dysku systemu operacyjnego i wszystkich dysków z danymi. Ta opcja zapewnia miesięczny czas pracy w wysokości 99,9%.
- Wiele (co najmniej dwóch) maszyn wirtualnych zorganizowanych w [zestawie dostępności platformy Azure](../../windows/tutorial-availability-sets.md). Ta opcja zapewnia miesięczny czas pracy w wysokości 99,95%.
- Wiele (co najmniej dwa) maszyn wirtualnych, które są zorganizowane w [strefie dostępności](../../../availability-zones/az-overview.md). Ta opcja zapewnia miesięczny czas pracy w wysokości 99,99%.

Zmierz wymaganie dostępności na umowy SLA, które mogą być dostępne dla składników platformy Azure. Następnie wybierz scenariusze do SAP HANA, aby osiągnąć wymagany poziom dostępności.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [dostępności SAP HANA w ramach jednego regionu świadczenia usługi Azure](./sap-hana-availability-one-region.md).
- Dowiedz się więcej o [dostępności SAP HANA w różnych regionach świadczenia usługi Azure](./sap-hana-availability-across-regions.md). 















  
