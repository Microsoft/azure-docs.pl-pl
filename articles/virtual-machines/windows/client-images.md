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
ms.openlocfilehash: 0f8a17ca69da15d650ba88642d7a81e477a6c537
ms.sourcegitcommit: 59cfed657839f41c36ccdf7dc2bee4535c920dd4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/06/2021
ms.locfileid: "99627150"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Używanie klienta systemu Windows na platformie Azure w scenariuszach deweloperskich/testowych
Możesz użyć systemu Windows 7, Windows 8 lub Windows 10 Enterprise (x64) na platformie Azure na potrzeby scenariuszy tworzenia i testowania, pod warunkiem że masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN). 

Aby uruchomić system Windows 10 w środowisku produkcyjnym, zobacz artykuł [jak wdrożyć system Windows 10 na platformie Azure z wielodostępnymi prawami hostingu](windows-desktop-multitenant-hosting-deployment.md).


## <a name="subscription-eligibility"></a>Uprawnienie do subskrypcji
Aktywni subskrybenci programu Visual Studio (ludzie, którzy uzyskali licencję na subskrypcję programu Visual Studio) mogą korzystać z obrazów klienta systemu Windows na potrzeby tworzenia i testowania. Obrazy klienta systemu Windows mogą być używane na własny sprzęt lub na maszynach wirtualnych platformy Azure.

Niektóre obrazy klienta systemu Windows są dostępne w portalu Azure Marketplace. Subskrybenci programu Visual Studio w ramach dowolnego typu oferty mogą również [przygotować i utworzyć](prepare-for-upload-vhd-image.md) 64-bitowe obrazy systemu Windows 7, Windows 8 lub Windows 10, a następnie [przekazać je do platformy Azure](upload-generalized-managed.md).

## <a name="eligible-offers-and-client-images"></a>Kwalifikujące się oferty i obrazy klienta
W poniższej tabeli przedstawiono identyfikatory oferty kwalifikujące się do wdrażania obrazów klientów systemu Windows za pomocą witryny Azure Marketplace. Obrazy klienta systemu Windows są widoczne tylko dla następujących ofert. 

| Offer Name | Numer oferty | Dostępne obrazy klienta | 
|:--- |:---:|:---:|
| [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N z dodatkiem SP1 (x64) |
| [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N z dodatkiem SP1 (x64) |
| [Subskrybenci Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N z dodatkiem SP1 (x64) |
| [Subskrybenci programu Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N z dodatkiem SP1 (x64) |
| [Visual Studio Premium z subskrypcją MSDN (korzyść)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N z dodatkiem SP1 (x64) |
| [Subskrybenci programu Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N z dodatkiem SP1 (x64) |
| [Subskrybenci programu Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N z dodatkiem SP1 (x64) |
| [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148P | Windows 10 Enterprise N (x64) <br> Windows 8.1 Enterprise N (x64) <br> Windows 7 Enterprise N z dodatkiem SP1 (x64) |

## <a name="check-your-azure-subscription"></a>Sprawdź swoją subskrypcję platformy Azure
Jeśli nie znasz identyfikatora oferty, możesz uzyskać go za pomocą Azure Portal na jeden z następujących sposobów:  
- W oknie *subskrypcje* : ![ szczegóły identyfikatora oferty z Azure Portal](./media/client-images/offer-id-azure-portal.png) 
- Lub kliknij pozycję **rozliczenia** , a następnie kliknij swój identyfikator subskrypcji. W oknie *rozliczania* zostanie wyświetlony identyfikator oferty.
Możesz również wyświetlić identyfikator oferty na [karcie "subskrypcje"](https://account.windowsazure.com/Subscriptions) portalu konta platformy Azure: ![ szczegóły identyfikatora oferty w portalu konta platformy Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Następne kroki
Teraz możesz wdrażać maszyny wirtualne przy użyciu [programu PowerShell](quick-create-powershell.md), [szablonów Menedżer zasobów](ps-template.md)lub [programu Visual Studio](../../azure-resource-manager/templates/create-visual-studio-deployment-project.md).