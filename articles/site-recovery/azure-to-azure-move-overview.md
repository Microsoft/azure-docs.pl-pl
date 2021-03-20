---
title: Przeniesienie maszyn wirtualnych platformy Azure do innego regionu za pomocą Azure Site Recovery
description: Przenoszenie maszyn wirtualnych platformy Azure z jednego regionu platformy Azure do innego za pomocą Azure Site Recovery.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 01/28/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: 61d596c4b3a65c54e1a70682adad5b7328c384f8
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "90007370"
---
# <a name="moving-azure-vms-to-another-azure-region"></a>Przeniesienie maszyn wirtualnych platformy Azure do innego regionu platformy Azure

Ten artykuł zawiera omówienie przyczyn i kroków związanych z przeniesieniem maszyn wirtualnych platformy Azure do innego regionu platformy Azure przy użyciu [Azure Site Recovery](site-recovery-overview.md). 


## <a name="reasons-to-move-azure-vms"></a>Przyczyny przenoszenia maszyn wirtualnych platformy Azure

Maszyny wirtualne można przenosić z następujących powodów:

- Została już wdrożona w jednym regionie i dodano obsługę nowego regionu, która jest bliżej użytkowników końcowych Twojej aplikacji lub usługi. W tym scenariuszu należy przenieść maszyny wirtualne do nowego regionu, aby zmniejszyć opóźnienie. Użyj tego samego podejścia, jeśli chcesz skonsolidować subskrypcje lub jeśli istnieją reguły zarządzania lub organizacji, które wymagają przeniesienia.
- Maszyna wirtualna została wdrożona jako maszyna wirtualna z jednym wystąpieniem lub jako część zestawu dostępności. Jeśli chcesz zwiększyć dostępność umowy SLA, możesz przenieść maszyny wirtualne do strefy dostępności.

## <a name="move-vms-with-resource-mover"></a>Przenoszenie maszyn wirtualnych za pomocą przenoszenia zasobów

Teraz możesz przenosić maszyny wirtualne do innego regionu za pomocą [usługi Azure Resource](../resource-mover/tutorial-move-region-virtual-machines.md)przenoszącej. Program przenoszenia zasobów jest w publicznej wersji zapoznawczej i oferuje następujące informacje:
- Jedno centrum do przemieszczania zasobów między regionami.
- Krótszy czas przenoszenia i złożoność. Wszystko, czego potrzebujesz, znajduje się w jednej lokalizacji.
- Proste i spójne środowisko do przemieszczania różnych typów zasobów platformy Azure.
- Prosty sposób identyfikowania zależności między zasobami, które chcesz przenieść. Ułatwia to przenoszenie powiązanych zasobów razem, dzięki czemu wszystko działa zgodnie z oczekiwaniami w regionie docelowym po przeniesieniu.
- Automatyczne czyszczenie zasobów w regionie źródłowym, jeśli chcesz je usunąć po przeniesieniu.
- Testowy. Możesz wypróbować przechodzenie, a następnie odrzucić go, jeśli nie chcesz wykonać pełnego przeniesienia.



## <a name="move-vms-with-site-recovery"></a>Przenoszenie maszyn wirtualnych za pomocą Site Recovery

Przeniesienie maszyn wirtualnych z Site Recovery obejmuje następujące kroki:

1. Weryfikowanie wymagań wstępnych
2. Przygotuj źródłowe maszyny wirtualne.
3. Przygotuj region docelowy.
4. Skopiuj dane do regionu docelowego. Użyj technologii replikacji Azure Site Recovery, aby skopiować dane ze źródłowej maszyny wirtualnej do regionu docelowego.
5. Przetestuj konfigurację. Po zakończeniu replikacji Przetestuj konfigurację, wykonując test pracy w trybie failover w sieci nieprodukcyjnej.
6. Wykonaj przeniesienie.
7. Odrzuć zasoby w regionie źródłowym.

> [!NOTE]
> Szczegółowe informacje o tych krokach znajdują się w poniższych sekcjach.
> [!IMPORTANT]
> Obecnie Azure Site Recovery obsługuje przeniesienie maszyn wirtualnych z jednego regionu do innego, ale nie obsługuje poruszania się w obrębie regionu.

## <a name="typical-architectures-for-a-multi-tier-deployment"></a>Typowe architektury wdrożenia wielowarstwowego

W tej sekcji opisano najpopularniejsze architektury wdrażania aplikacji wielowarstwowej na platformie Azure. Przykładem jest aplikacja trójwarstwowej z publicznym adresem IP. Każda z warstw (sieci Web, aplikacji i bazy danych) ma dwie maszyny wirtualne z każdą z nich i jest połączona przez moduł równoważenia obciążenia platformy Azure z innymi warstwami. Warstwa bazy danych ma zawsze SQL Server replikację między maszynami wirtualnymi w celu zapewnienia wysokiej dostępności.

* **Maszyny wirtualne z pojedynczym wystąpieniem wdrożone w różnych warstwach**: Każda maszyna wirtualna w warstwie jest skonfigurowana jako maszyna wirtualna o pojedynczym wystąpieniu i jest połączona z modułami równoważenia obciążenia z innymi warstwami. Ta konfiguracja jest najprostsza do przyjęcia.

     ![Wybór przenoszenia wdrożenia maszyny wirtualnej z jednym wystąpieniem w warstwach](media/move-vm-overview/regular-deployment.png)

* **Maszyny wirtualne w poszczególnych warstwach wdrożone w różnych zestawach dostępności**: Każda maszyna wirtualna w warstwie jest konfigurowana w zestawie dostępności. [Zestawy dostępności](../virtual-machines/windows/tutorial-availability-sets.md) zapewniają rozproszenie maszyn wirtualnych wdrożonych na platformie Azure pomiędzy wieloma izolowanymi węzłami sprzętowymi w klastrze. Pozwala to zagwarantować, że w przypadku awarii sprzętu lub oprogramowania na platformie Azure będzie to miało wpływ tylko na podzestaw maszyn wirtualnych, a całe rozwiązanie pozostanie dostępne i działa.

     ![Wdrożenie maszyn wirtualnych w zestawach dostępności](media/move-vm-overview/avset.png)

* **Maszyny wirtualne w poszczególnych warstwach wdrożone w strefy dostępności**: Każda maszyna wirtualna w warstwie jest konfigurowana między [strefy dostępności](../availability-zones/az-overview.md). Strefa dostępności w regionie świadczenia usługi Azure jest kombinacją domeny błędów i domeny aktualizacji. Jeśli na przykład utworzysz trzy lub więcej maszyn wirtualnych w trzech strefach w regionie świadczenia usługi Azure, maszyny wirtualne będą rozproszone w trzech domenach błędów i trzech domenach aktualizacji. Platforma Azure rozpoznaje to rozproszenie w domenach aktualizacji, aby upewnić się, że maszyny wirtualne w różnych strefach nie są aktualizowane w tym samym czasie.

     ![Wdrożenie strefy dostępności](media/move-vm-overview/zone.png)

## <a name="move-vms-as-is-to-a-target-region"></a>Przenoszenie maszyn wirtualnych bez zmian do regionu docelowego

Zgodnie z wymienionymi wcześniej [architekturami](#typical-architectures-for-a-multi-tier-deployment) wdrożenia będą wyglądać podobnie po przeniesieniu do regionu docelowego.

* **Maszyny wirtualne z pojedynczym wystąpieniem wdrożone w różnych warstwach**
* **Maszyny wirtualne w poszczególnych warstwach wdrożone w różnych zestawach dostępności**
* **Maszyny wirtualne w każdej warstwie wdrożone w Strefy dostępności**

## <a name="move-vms-to-increase-availability"></a>Przenoszenie maszyn wirtualnych w celu zwiększenia dostępności

* **Maszyny wirtualne z pojedynczym wystąpieniem wdrożone w różnych warstwach**

     ![Wdrożenie maszyny wirtualnej z jednym wystąpieniem w warstwach](media/move-vm-overview/single-zone.png)

* **Maszyny wirtualne w poszczególnych warstwach wdrożone w zestawach dostępności**: maszyny wirtualne można skonfigurować w ramach zestawu dostępności w osobnym strefy dostępności po włączeniu replikacji dla maszyny wirtualnej przy użyciu Azure Site Recovery. Umowa SLA dla dostępności będzie 99,99% po zakończeniu operacji przenoszenia.

     ![Wdrożenie maszyn wirtualnych w zestawach dostępności i Strefy dostępności](media/move-vm-overview/aset-azone.png)

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> 
> * [Przenoszenie maszyn wirtualnych platformy Azure do innego regionu](azure-to-azure-tutorial-migrate.md)
> 
> * [Przenoszenie maszyn wirtualnych platformy Azure do Strefy dostępności](move-azure-vms-avset-azone.md)

