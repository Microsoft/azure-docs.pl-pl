---
title: Rozwiązywanie problemów z wdrażaniem maszyn wirtualnych z systemem Linux na platformie Azure | Microsoft Docs
description: Rozwiązywanie problemów z wdrażaniem maszyn wirtualnych z systemem Linux w modelu wdrażania Azure Resource Manager.
services: virtual-machines-windows
documentationcenter: ''
author: genlin
manager: dcscontentpm
editor: ''
tags: azure-resource-manager
ms.assetid: 4e383427-4aff-4bf3-a0f4-dbff5c6f0c81
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.topic: troubleshooting
ms.date: 11/01/2018
ms.author: genli
ms.openlocfilehash: cf8fb383625ec2752264d6e5a70d8625f06689fc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82628303"
---
# <a name="troubleshoot-deploying-linux-virtual-machine-issues-in-azure"></a>Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z systemem Linux na platformie Azure

Aby rozwiązać problemy z wdrażaniem maszyn wirtualnych na platformie Azure, zapoznaj się z [najważniejszymi problemami](#top-issues) dotyczącymi typowych awarii i rozwiązań.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [virtual-machines-linux-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-linux-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klaster nie może obsługiwać żądanego rozmiaru maszyny wirtualnej
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Spróbuj ponownie wykonać żądanie, używając mniejszego rozmiaru maszyny wirtualnej.
- Jeśli nie można zmienić rozmiaru żądanej maszyny wirtualnej:
    - Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności. Kliknij pozycję **grupy zasobów** , > grupę zasobów > **zasoby** > zestaw dostępności > **Virtual Machines** > **zatrzymać**maszynę wirtualną.
    - Po zatrzymaniu wszystkich maszyn wirtualnych Utwórz maszynę wirtualną w żądanym rozmiarze.
    - Najpierw uruchom nową maszynę wirtualną, a następnie wybierz każdą z zatrzymanych maszyn wirtualnych, a następnie kliknij przycisk Uruchom.


## <a name="the-cluster-does-not-have-free-resources"></a>W klastrze nie ma bezpłatnych zasobów
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Ponów żądanie później.
- Jeśli nowa maszyna wirtualna może być częścią innego zestawu dostępności
    - Utwórz maszynę wirtualną w innym zestawie dostępności (w tym samym regionie).
    - Dodaj nową MASZYNę wirtualną do tej samej sieci wirtualnej.

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak mogę Aktywuj moje miesięczne środki dla programu Visual Studio Enterprise (BizSpark)

Aby aktywować miesięczne środki, zobacz ten [artykuł](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="why-can-i-not-install-the-gpu-driver-for-an-ubuntu-nv-vm"></a>Dlaczego nie mogę zainstalować sterownika procesora GPU dla maszyny wirtualnej Ubuntu NV?

Obecnie obsługa procesora GPU systemu Linux jest dostępna tylko na maszynach wirtualnych Azure NC z systemem Ubuntu Server 16,04 LTS. Aby uzyskać więcej informacji, zobacz [Konfigurowanie sterowników procesora GPU dla maszyn wirtualnych serii N z systemem Linux](../linux/n-series-driver-setup.md).

## <a name="my-drivers-are-missing-for-my-linux-n-series-vm"></a>Brak moich sterowników dla maszyny wirtualnej z serii N systemu Linux

Instrukcje dotyczące instalowania sterowników maszyn wirtualnych opartych na systemie Linux znajdują się [tutaj](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nie mogę znaleźć wystąpienia procesora GPU w mojej maszynie wirtualnej z serii N

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych serii N platformy Azure, należy zainstalować sterowniki grafiki na każdej maszynie wirtualnej po wdrożeniu. Informacje o konfiguracji sterownika są dostępne [tutaj](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="are-n-series-vms-available-in-my-region"></a>Czy maszyny wirtualne z serii N są dostępne w moim regionie?

Dostępność można sprawdzić w obszarze [produkty dostępne według regionów](https://azure.microsoft.com/regions/services) [i cenniku](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nie mogę wyświetlić rodziny rozmiaru maszyny wirtualnej, którą chcę zmienić podczas zmieniania rozmiaru maszyny wirtualnej.

Gdy maszyna wirtualna jest uruchomiona, jest wdrażana na serwerze fizycznym. Serwery fizyczne w regionach platformy Azure są pogrupowane w klastrach wspólnego sprzętu fizycznego. Zmienianie rozmiarów maszyny wirtualnej, która wymaga przeniesienia maszyny wirtualnej do różnych klastrów sprzętowych, różni się w zależności od modelu wdrażania użytego do wdrożenia maszyny wirtualnej.

- W przypadku maszyn wirtualnych wdrożonych w klasycznym modelu wdrażania należy usunąć i ponownie wdrożyć wdrożenie usługi w chmurze w celu zmiany rozmiaru maszyn wirtualnych w innej rodzinie rozmiarów.

- Maszyny wirtualne wdrożone w modelu wdrażania Menedżer zasobów należy zatrzymać wszystkie maszyny wirtualne w zestawie dostępności przed zmianą rozmiaru każdej maszyny wirtualnej w zestawie dostępności.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Rozmiar wymienionej maszyny wirtualnej nie jest obsługiwany podczas wdrażania w zestawie dostępności.

Wybierz rozmiar obsługiwany w klastrze zestawu dostępności. Jest to zalecane w przypadku tworzenia zestawu dostępności w celu wybrania największego rozmiaru maszyny wirtualnej, którego potrzebujesz, i że jest to pierwsze wdrożenie w zestawie dostępności.

## <a name="what-linux-distributionsversions-are-supported-on-azure"></a>Jakie dystrybucje i wersje systemu Linux są obsługiwane na platformie Azure?

Listę można znaleźć w systemie Linux na temat [dystrybucji z zatwierdzeniem na platformie Azure](../linux/endorsed-distros.md).

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Czy mogę dodać istniejącą klasyczną maszynę wirtualną do zestawu dostępności?

Tak. Istniejącą klasyczną maszynę wirtualną można dodać do nowego lub istniejącego zestawu dostępności. Aby uzyskać więcej informacji [, zobacz Dodawanie istniejącej maszyny wirtualnej do zestawu dostępności](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
