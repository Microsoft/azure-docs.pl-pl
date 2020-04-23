---
title: Używanie obrazów klienta systemu Windows na platformie Azure
description: Jak używać korzyści z subskrypcji programu Visual Studio do wdrażania systemu Windows 7, Windows 8 lub Windows 10 na platformie Azure w scenariuszach deweloperskich/testowych
author: cynthn
ms.subservice: imaging
ms.service: virtual-machines-windows
ms.topic: article
ms.workload: infrastructure-services
ms.date: 12/15/2017
ms.author: cynthn
ms.openlocfilehash: 51e4862fe55428f112841ef7176cdb916653de0b
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82083278"
---
# <a name="use-windows-client-in-azure-for-devtest-scenarios"></a>Używanie klienta systemu Windows na platformie Azure w scenariuszach deweloperskich/testowych
Systemu Windows 7, Windows 8 lub Windows 10 Enterprise (x64) można użyć do scenariuszy deweloperskich/testowych, pod warunkiem że masz odpowiednią subskrypcję programu Visual Studio (dawniej MSDN). W tym artykule opisano wymagania dotyczące uprawnień do uruchamiania systemu Windows 7, Windows 8.1, Windows 10 Enterprise na platformie Azure i korzystania z następujących obrazów galerii platformy Azure.

![Szczegóły obrazu z witryny Azure portal](./media/client-images/windows-client-msdn-images.png) 

> [!NOTE]
> W przypadku obrazów Windows 10 Pro i Windows 10 Pro N w Galerii Platformy Azure zapoznaj się [z instrukcjami wdrażania systemu Windows 10 na platformie Azure ze](windows-desktop-multitenant-hosting-deployment.md)
>![szczegółami obrazu Multitenant Hosting Rights Pro z portalu Azure](./media/client-images/windows-client-pro-images.png) 
>

## <a name="subscription-eligibility"></a>Uprawnienia do subskrypcji
Subskrybenci programu Active Visual Studio (osoby, które uzyskały licencję subskrypcyjną programu Visual Studio) mogą używać klienta systemu Windows do celów programowania i testowania. Klient systemu Windows może być używany na własnym sprzęcie i maszynach wirtualnych platformy Azure działających w dowolnej subskrypcji platformy Azure. Klient systemu Windows nie może być wdrożony lub używany na platformie Azure do normalnego użytku produkcyjnego lub używany przez osoby, które nie są aktywnymi subskrybentami programu Visual Studio.

Dla Twojej wygody niektóre obrazy systemu Windows 10 są dostępne w Galerii Platformy Azure w [ramach kwalifikujących się ofert deweloperskich/testowych.](#eligible-offers) Subskrybenci programu Visual Studio w ramach dowolnej oferty mogą również [odpowiednio przygotować i utworzyć](prepare-for-upload-vhd-image.md) 64-bitowy obraz systemu Windows 7, Windows 8 lub Windows 10, a następnie [przesłać go na platformę Azure.](upload-generalized-managed.md) Użycie pozostaje ograniczone do dev/test przez aktywnych subskrybentów programu Visual Studio.

## <a name="eligible-offers"></a>Kwalifikujące się oferty
W poniższej tabeli opisano identyfikatory ofert, które kwalifikują się do wdrożenia systemu Windows 10 za pośrednictwem galerii Azure. Obrazy systemu Windows 10 są widoczne tylko dla następujących ofert. Subskrybenci programu Visual Studio, którzy muszą uruchomić klienta systemu Windows w innym typie oferty, wymagają [odpowiedniego przygotowania i utworzenia](prepare-for-upload-vhd-image.md) 64-bitowego obrazu systemu Windows 7, Windows 8 lub Windows 10, a następnie [przesłania na platformę Azure](upload-generalized-managed.md).

| Offer Name | Numer oferty | Dostępne obrazy klientów |
|:--- |:---:|:---:|
| [Płatność zgodnie z rzeczywistym użyciem — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0023p/) |0023p |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise (MPN)](https://azure.microsoft.com/offers/ms-azr-0029p/) |0029p |Windows 10 |
| [Subskrybenci programu Visual Studio Professional](https://azure.microsoft.com/offers/ms-azr-0059p/) |0059p |Windows 10 |
| [Subskrybenci programu Visual Studio Test Professional](https://azure.microsoft.com/offers/ms-azr-0060p/) |0060P |Windows 10 |
| [Visual Studio Premium z MSDN (korzyści)](https://azure.microsoft.com/offers/ms-azr-0061p/) |0061P |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise](https://azure.microsoft.com/offers/ms-azr-0063p/) |0063P |Windows 10 |
| [Subskrybenci programu Visual Studio Enterprise (BizSpark)](https://azure.microsoft.com/offers/ms-azr-0064p/) |0064p |Windows 10 |
| [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/) |0148p |Windows 10 |

## <a name="check-your-azure-subscription"></a>Sprawdź swoją subskrypcję platformy Azure
Jeśli nie znasz identyfikatora oferty, możesz uzyskać go za pośrednictwem witryny Azure portal na jeden z następujących dwóch sposobów:  

- W oknie *Subskrypcje:*

  ![Szczegóły identyfikatora oferty z witryny Azure portal](./media/client-images/offer-id-azure-portal.png) 

- Możesz też kliknąć pozycję **Rozliczenia,** a następnie kliknąć identyfikator subskrypcji. Identyfikator oferty pojawi się w oknie *Rozliczenia.*

Identyfikator oferty można również wyświetlić na [karcie "Subskrypcje"](https://account.windowsazure.com/Subscriptions) w portalu konta Azure:

![Szczegóły identyfikatora oferty z portalu konta usługi Azure](./media/client-images/offer-id-azure-account-portal.png) 

## <a name="next-steps"></a>Następne kroki
Teraz można wdrażać maszyny wirtualne przy użyciu programów [PowerShell,](quick-create-powershell.md) [szablonów Menedżera zasobów](ps-template.md)lub [programu Visual Studio.](../../vs-azure-tools-resource-groups-deployment-projects-create-deploy.md)

