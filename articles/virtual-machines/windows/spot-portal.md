---
title: Korzystanie z portalu do wdrażania maszyn wirtualnych usługi Azure spot
description: Dowiedz się, w jaki sposób używać Azure PowerShell do wdrażania maszyn wirtualnych w celu oszczędzania kosztów.
author: cynthn
ms.service: virtual-machines
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 03/25/2020
ms.author: cynthn
ms.reviewer: jagaveer
ms.openlocfilehash: 6e7723a437e90807063e3c3b7af2bf068dca5b9f
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "82100655"
---
# <a name="deploy-spot-vms-using-the-azure-portal"></a>Wdrażanie maszyn wirtualnych przy użyciu Azure Portal

Korzystanie z [maszyn wirtualnych na miejscu](spot-vms.md) pozwala korzystać z nieużywanej pojemności przy znaczącym obciążeniu kosztów. W dowolnym momencie, gdy platforma Azure wymaga przywrócenia pojemności, infrastruktura platformy Azure wyłączy maszyny wirtualne. W związku z tym maszyny wirtualne są doskonałe dla obciążeń, które mogą obsłużyć przerwy, takie jak zadania przetwarzania wsadowego, środowiska deweloperskie/testowe, duże obciążenia obliczeniowe i inne.

Ceny maszyn wirtualnych na miejscu są zmienne, na podstawie regionu i jednostki SKU. Aby uzyskać więcej informacji, zobacz cennik maszyn wirtualnych dla [systemów](https://azure.microsoft.com/pricing/details/virtual-machines/windows/) [Linux](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) i Windows. Aby uzyskać więcej informacji na temat ustawiania ceny maksymalnej, zobacz [punkt maszyny wirtualne — Cennik](spot-vms.md#pricing).

Dla maszyny wirtualnej można ustawić maksymalną cenę, która ma być płacona za godzinę. Maksymalna cena maszyny wirtualnej na miejscu może być ustawiona w dolarach amerykańskich (USD) przy użyciu maksymalnie 5 miejsc dziesiętnych. Na przykład wartość będzie `0.05701` Cena maksymalna $0,05701 USD za godzinę. Jeśli ustawisz maksymalną cenę `-1` , maszyna wirtualna nie zostanie wykluczona na podstawie ceny. Cena maszyny wirtualnej to aktualna cena za ilość miejsca lub cena standardowej maszyny wirtualnej, która kiedykolwiek jest mniejsza, o ile jest dostępna pojemność i przydział.


## <a name="create-the-vm"></a>Tworzenie maszyny wirtualnej

Proces tworzenia maszyny wirtualnej korzystającej z maszyn wirtualnych jest taki sam jak szczegółowy w [przewodniku szybki start](quick-create-portal.md). Podczas wdrażania maszyny wirtualnej możesz użyć wystąpienia usługi Azure spot.


Na karcie **podstawowe** w sekcji **szczegóły wystąpienia** **nie** jest domyślnie używana usługa Azure Spot instance.

![Przechwytywanie ekranu w celu wybrania opcji nie, nie używaj wystąpienia usługi Azure spot](media/spot-portal/no.png)

Wybranie opcji **tak**powoduje, że sekcja zostanie rozwinięta i można wybrać [Typ wykluczenia i zasady wykluczania](spot-vms.md#eviction-policy). 

![Przechwytywanie ekranu w celu wybrania opcji tak, użyj wystąpienia usługi Azure spot](media/spot-portal/yes.png)


## <a name="next-steps"></a>Następne kroki

Możesz również tworzyć maszyny wirtualne na miejscu przy użyciu [programu PowerShell](spot-powershell.md).