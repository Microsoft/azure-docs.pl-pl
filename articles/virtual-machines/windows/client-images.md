---
title: Korzystanie z obrazów klienta systemu Windows na platformie Azure
description: Jak korzystać z zalet subskrypcji programu Visual Studio do wdrażania systemów Windows 7, Windows 8 lub Windows 10 na platformie Azure na potrzeby scenariuszy tworzenia i testowania
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 26bfd9a65cd3e6d36b8190e87d382543cc7f0f60
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87292049"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Używanie klienta systemu Windows na platformie Azure w scenariuszach deweloperskich/testowych
Możesz użyć systemu Windows 7, Windows 8 lub Windows 10 Enterprise (x64) na platformie Azure na potrzeby scenariuszy tworzenia i testowania, pod warunkiem że masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN). W tym artykule opisano wymagania dotyczące uprawnień do uruchamiania systemu Windows 7, Windows 8.1, Windows 10 Enterprise na platformie Azure i korzystania z następujących obrazów galerii platformy Azure.

![Szczegóły obrazu z Azure Portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> W przypadku systemu Windows 10 Pro i Windows 10 Pro N Image w galerii platformy Azure Zapoznaj się z artykułem [jak wdrożyć system Windows 10 na platformie Azure z informacjami o wielodostępnym](windows-desktop-multitenant-hosting-deployment.md)obrazie programu Pro z prawami hostingu 
> ![ z Azure Portal](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Uprawnienie do subskrypcji
Aktywni subskrybenci programu Visual Studio (osoby, które uzyskały licencję na subskrypcję programu Visual Studio) mogą używać klienta systemu Windows do celów deweloperskich i testowych. Klienta systemu Windows można używać na własnym sprzęcie i maszynach wirtualnych platformy Azure działających w dowolnym typie subskrypcji platformy Azure. Klienta systemu Windows nie można wdrożyć ani używać na platformie Azure do normalnego korzystania z produkcji ani używać przez osoby, które nie są aktywnymi subskrybentami programu Visual Studio.

Dla wygody niektóre obrazy systemu Windows 10 są dostępne w galerii platformy Azure w ramach [kwalifikujących się ofert tworzenia i testowania](#eligible-offers). Subskrybenci programu Visual Studio w ramach dowolnego typu oferty mogą również [odpowiednio przygotować i utworzyć](prepare-for-upload-vhd-image.md) 64-bitową wersję obrazu systemu Windows 7, Windows 8 lub Windows 10, a następnie [przekazać do platformy Azure](upload-generalized-managed.md). Użycie pozostaje ograniczone do tworzenia i testowania przez aktywnych subskrybentów programu Visual Studio.

## <a name="eligible-offers"></a>Kwalifikujące się oferty
W poniższej tabeli przedstawiono identyfikatory oferty kwalifikujące się do wdrożenia systemu Windows 10 za pomocą galerii platformy Azure. Obrazy systemu Windows 10 są widoczne tylko dla następujących ofert. Subskrybenci programu Visual Studio, którzy muszą uruchamiać klienta systemu Windows w ramach innego typu oferty, wymagają [odpowiedniego przygotowania i utworzenia](prepare-for-upload-vhd-image.md) obrazu 64-bitowego systemu Windows 7, Windows 8 lub Windows 10, a [następnie przekazania do platformy Azure](upload-generalized-managed.md).

| Offer Name | Numer oferty | Dostępne obrazy klienta |
|:--- |:---:|:---:|
| [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P |Windows 10 |
| [Subskrybenci Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P |Windows 10 |
| [Subskrybenci programu Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium z subskrypcją MSDN (korzyść)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P |Windows 10 |
| [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P |Windows 10 |

## <a name="check-your-azure-subscription"></a>Sprawdź swoją subskrypcję platformy Azure
Jeśli nie znasz identyfikatora oferty, możesz uzyskać go za pomocą Azure Portal na jeden z następujących sposobów:  

- W oknie *subskrypcje* :

  ![Szczegóły identyfikatora oferty z Azure Portal](./media/client-images/offer-id-azure-portal.png) 

- Lub kliknij pozycję **rozliczenia** , a następnie kliknij swój identyfikator subskrypcji. W oknie *rozliczania* zostanie wyświetlony identyfikator oferty.

Identyfikator oferty można także wyświetlić na [karcie "subskrypcje"](https://account.windowsazure.com/Subscriptions) portalu konta platformy Azure:

![Szczegóły identyfikatora oferty z portalu konta platformy Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Następne kroki
Teraz możesz wdrażać maszyny wirtualne przy użyciu [programu PowerShell](quick-create-powershell.md), [szablonów Menedżer zasobów](ps-template.md)lub [programu Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).
