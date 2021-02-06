---
title: Rozwiązywanie problemów z wdrażaniem maszyn wirtualnych z systemem Windows na platformie Azure | Microsoft Docs
description: Rozwiązywanie problemów z wdrażaniem maszyn wirtualnych systemu Windows w modelu wdrażania Azure Resource Manager.
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
ms.openlocfilehash: 98a16e0a60ddf149e8f0e1a092051f3e98ea8225
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627066"
---
# <a name="troubleshoot-deploying-windows-virtual-machine-issues-in-azure"></a>Rozwiązywanie problemów z wdrażaniem maszyny wirtualnej z systemem Windows na platformie Azure

Aby rozwiązać problemy z wdrażaniem maszyn wirtualnych na platformie Azure, zapoznaj się z [najważniejszymi problemami](#top-issues) dotyczącymi typowych awarii i rozwiązań.

Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/). Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.

## <a name="top-issues"></a>Najważniejsze problemy
[!INCLUDE [virtual-machines-windows-troubleshoot-deploy-vm-top](../../../includes/virtual-machines-windows-troubleshoot-deploy-vm-top.md)]

## <a name="the-cluster-cannot-support-the-requested-vm-size"></a>Klaster nie może obsługiwać żądanego rozmiaru maszyny wirtualnej
\<properties
supportTopicIds="123456789"
resourceTags="windows"
productPesIds="1234, 5678"
/>
- Spróbuj ponownie wykonać żądanie, używając mniejszego rozmiaru maszyny wirtualnej.
- Jeśli nie można zmienić rozmiaru żądanej maszyny wirtualnej:
    - Zatrzymaj wszystkie maszyny wirtualne w zestawie dostępności. Kliknij pozycję **grupy zasobów** , > grupę zasobów > **zasoby** > zestaw dostępności > **Virtual Machines** > **zatrzymać** maszynę wirtualną.
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

## <a name="how-can-i-use-and-deploy-a-windows-client-image-into-azure"></a>Jak mogę użyć i wdrożyć obraz klienta systemu Windows na platformie Azure?

Jeśli masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN), możesz użyć systemu Windows 7, Windows 8 lub Windows 10 na platformie Azure. W tym [artykule](../windows/client-images.md) przedstawiono wymagania dotyczące uprawnień do uruchamiania klienta systemu Windows na platformie Azure i korzystania z obrazów galerii platformy Azure.

## <a name="how-can-i-deploy-a-virtual-machine-using-the-hybrid-use-benefit-hub"></a>Jak mogę wdrożyć maszynę wirtualną przy użyciu korzyści z używania hybrydowego (centrum)?

Istnieje kilka różnych sposobów wdrażania maszyn wirtualnych z systemem Windows przy użyciu korzyści z używania hybrydowej platformy Azure.

Subskrypcja Enterprise Agreement:

• Wdróż maszyny wirtualne z określonych obrazów w portalu Marketplace, które są wstępnie skonfigurowane przy użyciu korzyści z używania hybrydowej platformy Azure.

Dla umowy Enterprise Agreement:

• Przekaż niestandardową maszynę wirtualną i Wdróż ją przy użyciu szablonu Menedżer zasobów lub Azure PowerShell.

Więcej informacji można znaleźć w następujących zasobach:

 - [Omówienie korzyści z używania hybrydowej platformy Azure](https://azure.microsoft.com/pricing/hybrid-use-benefit/)

 - [Pobieranie często zadawanych pytań](https://download.microsoft.com/download/4/2/1/4211AC94-D607-4A45-B472-4B30EDF437DE/Windows_Server_Azure_Hybrid_Use_FAQ_EN_US.pdf)

 - [Korzyść z używania hybrydowej platformy Azure dla systemu Windows Server i klienta systemu Windows](../windows/hybrid-use-benefit-licensing.md).

 - [Jak korzystać z korzyści z używania hybrydowego na platformie Azure](/archive/blogs/azureedu/how-can-i-use-the-hybrid-use-benefit-in-azure)

## <a name="how-do-i-activate-my-monthly-credit-for-visual-studio-enterprise-bizspark"></a>Jak mogę Aktywuj moje miesięczne środki dla programu Visual Studio Enterprise (BizSpark)

Aby aktywować miesięczne środki, zobacz ten [artykuł](https://azure.microsoft.com/offers/ms-azr-0064p/).

## <a name="how-to-add-enterprise-devtest-to-my-enterprise-agreement-ea-to-get-access-to-window-client-images"></a>Jak dodać Enterprise — tworzenie i testowanie do Enterprise Agreement (EA) w celu uzyskania dostępu do obrazów klientów systemu Windows?

Możliwość tworzenia subskrypcji na podstawie oferty Enterprise — tworzenie i testowanie jest ograniczona do właścicieli kont, którzy uzyskali odpowiednie uprawnienia przez administratora przedsiębiorstwa. Właściciel konta tworzy subskrypcje za pośrednictwem portalu konta platformy Azure, a następnie powinien dodawać aktywnych subskrybentów programu Visual Studio jako współadministratorów. Dzięki temu użytkownicy mogą zarządzać zasobami wymaganymi do projektowania i testowania oraz korzystać z nich. Aby uzyskać więcej informacji, zobacz [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="my-drivers-are-missing-for-my-windows-n-series-vm"></a>Brak moich sterowników dla mojej maszyny wirtualnej z serii N systemu Windows

Instrukcje dotyczące instalowania sterowników maszyn wirtualnych z systemem Windows znajdują się [tutaj](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="i-cant-find-a-gpu-instance-within-my-n-series-vm"></a>Nie mogę znaleźć wystąpienia procesora GPU w mojej maszynie wirtualnej z serii N

Aby skorzystać z możliwości procesora GPU dla maszyn wirtualnych serii N platformy Azure, należy zainstalować sterowniki grafiki na każdej maszynie wirtualnej po wdrożeniu. Informacje o konfiguracji sterownika są dostępne [tutaj](../sizes-gpu.md#supported-operating-systems-and-drivers).

## <a name="are-n-series-vms-available-in-my-region"></a>Czy maszyny wirtualne z serii N są dostępne w moim regionie?

Dostępność można sprawdzić w obszarze [produkty dostępne według regionów](https://azure.microsoft.com/regions/services) [i cenniku](https://azure.microsoft.com/pricing/details/virtual-machines/series/#n-series).

## <a name="what-client-images-can-i-use-and-deploy-in-azure-and-how-to-i-get-them"></a>Jakie obrazy klienckie można używać i wdrażać na platformie Azure oraz jak je uzyskać?

Możesz użyć systemu Windows 7, Windows 8 lub Windows 10 na platformie Azure na potrzeby scenariuszy tworzenia i testowania, pod warunkiem że masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN). 

- Obrazy systemu Windows 10 są dostępne w galerii platformy Azure w ramach [kwalifikujących się ofert tworzenia i testowania](../windows/client-images.md). 
- Subskrybenci programu Visual Studio w ramach dowolnego typu oferty mogą również [odpowiednio przygotować i utworzyć](../windows/prepare-for-upload-vhd-image.md) 64-bitową wersję obrazu systemu Windows 7, Windows 8 lub Windows 10, a następnie [przekazać do platformy Azure](../windows/upload-generalized-managed.md). Użycie pozostaje ograniczone do tworzenia i testowania przez aktywnych subskrybentów programu Visual Studio.

W tym [artykule](../windows/client-images.md) przedstawiono wymagania dotyczące uprawnień do uruchamiania klienta systemu Windows na platformie Azure i korzystania z obrazów galerii platformy Azure.

## <a name="i-am-not-able-to-see-vm-size-family-that-i-want-when-resizing-my-vm"></a>Nie mogę wyświetlić rodziny rozmiaru maszyny wirtualnej, którą chcę zmienić podczas zmieniania rozmiaru maszyny wirtualnej.

Gdy maszyna wirtualna jest uruchomiona, jest wdrażana na serwerze fizycznym. Serwery fizyczne w regionach platformy Azure są pogrupowane w klastrach wspólnego sprzętu fizycznego. Zmienianie rozmiarów maszyny wirtualnej, która wymaga przeniesienia maszyny wirtualnej do różnych klastrów sprzętowych, różni się w zależności od modelu wdrażania użytego do wdrożenia maszyny wirtualnej.

- W przypadku maszyn wirtualnych wdrożonych w klasycznym modelu wdrażania należy usunąć i ponownie wdrożyć wdrożenie usługi w chmurze w celu zmiany rozmiaru maszyn wirtualnych w innej rodzinie rozmiarów.

[!INCLUDE [classic-vm-deprecation](../../../includes/classic-vm-deprecation.md)]

- Maszyny wirtualne wdrożone w modelu wdrażania Menedżer zasobów należy zatrzymać wszystkie maszyny wirtualne w zestawie dostępności przed zmianą rozmiaru każdej maszyny wirtualnej w zestawie dostępności.

## <a name="the-listed-vm-size-is-not-supported-while-deploying-in-availability-set"></a>Rozmiar wymienionej maszyny wirtualnej nie jest obsługiwany podczas wdrażania w zestawie dostępności.

Wybierz rozmiar obsługiwany w klastrze zestawu dostępności. Jest to zalecane w przypadku tworzenia zestawu dostępności w celu wybrania największego rozmiaru maszyny wirtualnej, którego potrzebujesz, i że jest to pierwsze wdrożenie w zestawie dostępności.

## <a name="can-i-add-an-existing-classic-vm-to-an-availability-set"></a>Czy mogę dodać istniejącą klasyczną maszynę wirtualną do zestawu dostępności?

Tak. Istniejącą klasyczną maszynę wirtualną można dodać do nowego lub istniejącego zestawu dostępności. Aby uzyskać więcej informacji [, zobacz Dodawanie istniejącej maszyny wirtualnej do zestawu dostępności](/previous-versions/azure/virtual-machines/windows/classic/configure-availability-classic#addmachine).


## <a name="next-steps"></a>Następne kroki
Jeśli potrzebujesz więcej pomocy w dowolnym punkcie tego artykułu, możesz skontaktować się z ekspertami platformy Azure na [forach MSDN i Stack Overflow](https://azure.microsoft.com/support/forums/).

Alternatywnie możesz zaplikować zdarzenie pomocy technicznej platformy Azure. Przejdź do [witryny pomocy technicznej systemu Azure](https://azure.microsoft.com/support/options/) i wybierz pozycję **Uzyskaj pomoc techniczną**.
