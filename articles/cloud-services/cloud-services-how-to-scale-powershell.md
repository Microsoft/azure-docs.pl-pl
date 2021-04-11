---
title: Skalowanie usługi w chmurze platformy Azure (klasycznej) w programie Windows PowerShell | Microsoft Docs
description: motyw Dowiedz się, jak skalować rolę sieci Web lub rolę procesu roboczego na platformie Azure przy użyciu programu PowerShell.
ms.topic: article
ms.service: cloud-services
ms.subservice: autoscale
ms.date: 10/14/2020
ms.author: tagore
author: tanmaygore
ms.reviewer: mimckitt
ms.custom: ''
ms.openlocfilehash: 0b0a9cc8032cfd849f053e2d45e05f85e4216a23
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105936618"
---
# <a name="how-to-scale-an-azure-cloud-service-classic-in-powershell"></a>Jak skalować usługę w chmurze platformy Azure (klasyczną) w programie PowerShell

> [!IMPORTANT]
> [Azure Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md) to nowy model wdrażania oparty na Azure Resource Manager dla produktu Cloud Services platformy Azure.Ta zmiana spowoduje, że usługa Azure Cloud Services uruchomiona w ramach modelu wdrażania opartego na usłudze Azure Service Manager została zmieniona jako Cloud Services (klasyczny), a wszystkie nowe wdrożenia powinny używać [Cloud Services (obsługa rozszerzona)](../cloud-services-extended-support/overview.md).

Za pomocą programu Windows PowerShell można skalować rolę sieci Web lub proces roboczy w lub na zewnątrz przez dodawanie lub usuwanie wystąpień.  

## <a name="log-in-to-azure"></a>Zaloguj się do platformy Azure.

Aby można było wykonać dowolne operacje w ramach subskrypcji za poorednictwem programu PowerShell, należy zalogować się:

```powershell
Add-AzureAccount
```

Jeśli masz wiele subskrypcji skojarzonych z Twoim kontem, być może musisz zmienić bieżącą subskrypcję w zależności od tego, gdzie znajduje się usługa w chmurze. Aby sprawdzić bieżącą subskrypcję, uruchom polecenie:

```powershell
Get-AzureSubscription -Current
```

Jeśli musisz zmienić bieżącą subskrypcję, uruchom polecenie:

```powershell
Set-AzureSubscription -SubscriptionId <subscription_id>
```

## <a name="check-the-current-instance-count-for-your-role"></a>Sprawdź bieżącą liczbę wystąpień roli

Aby sprawdzić bieżący stan roli, uruchom polecenie:

```powershell
Get-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>'
```

Należy uzyskać informacje o roli, w tym jej bieżącą wersję systemu operacyjnego i liczbę wystąpień. W takim przypadku rola ma pojedyncze wystąpienie.

![Informacje o roli](./media/cloud-services-how-to-scale-powershell/get-azure-role.png)

## <a name="scale-out-the-role-by-adding-more-instances"></a>Skalowanie roli przez dodanie większej liczby wystąpień

Aby skalować rolę, Przekaż żądaną liczbę wystąpień jako parametr **Count** do polecenia cmdlet **Set-AzureRole** :

```powershell
Set-AzureRole -ServiceName '<your_service_name>' -RoleName '<your_role_name>' -Slot <target_slot> -Count <desired_instances>
```

Polecenie cmdlet jest blokowane na chwilę, gdy nowe wystąpienia są inicjowane i uruchamiane. W tym czasie, jeśli otworzysz nowe okno programu PowerShell i Wywołaj polecenie **Get-AzureRole** , jak pokazano wcześniej, zobaczysz nową liczbę wystąpień docelowych. Jeśli sprawdzisz stan roli w portalu, zobaczysz nowe wystąpienie:

![Wystąpienie maszyny wirtualnej rozpoczynające się w portalu](./media/cloud-services-how-to-scale-powershell/role-instance-starting.png)

Po rozpoczęciu nowych wystąpień polecenie cmdlet zwróci pomyślne:

![Pomyślne zwiększenie wystąpienia roli](./media/cloud-services-how-to-scale-powershell/set-azure-role-success.png)

## <a name="scale-in-the-role-by-removing-instances"></a>Skalowanie w roli przez usuwanie wystąpień

Możesz skalować w roli, usuwając wystąpienia w ten sam sposób. Na stronie **Set-AzureRole** ustaw wartość parametru **Count** na liczbę wystąpień, które mają być dostępne po zakończeniu operacji skalowania.

## <a name="next-steps"></a>Następne kroki

Nie można skonfigurować automatycznego skalowania dla usług w chmurze w programie PowerShell. Aby to zrobić, zobacz [jak automatycznie skalować usługę w chmurze](cloud-services-how-to-scale-portal.md).
