---
title: Skalowanie usługi w chmurze platformy Azure w programie Windows PowerShell | Microsoft Docs
description: motyw Dowiedz się, jak skalować rolę sieci Web lub rolę procesu roboczego na platformie Azure przy użyciu programu PowerShell.
services: cloud-services
author: mmccrory
ms.service: cloud-services
ms.topic: article
ms.date: 12/01/2016
ms.author: memccror
ms.custom: devx-track-azurepowershell
ms.openlocfilehash: c2cec08d86760694ec3c41a98af62c6b86e0a4c9
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89071095"
---
# <a name="how-to-scale-a-cloud-service-in-powershell"></a>Jak skalować usługę w chmurze w programie PowerShell

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
