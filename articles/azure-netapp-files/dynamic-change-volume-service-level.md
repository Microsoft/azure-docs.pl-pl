---
title: Dynamicznie zmieniaj poziom usługi woluminu dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób dynamicznej zmiany poziomu usługi woluminu.
services: azure-netapp-files
documentationcenter: ''
author: b-juche
manager: ''
editor: ''
ms.assetid: ''
ms.service: azure-netapp-files
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 01/14/2021
ms.author: b-juche
ms.openlocfilehash: 3409387adb1e722d8368907d731e02983dd287fc
ms.sourcegitcommit: 3c460886f53a84ae104d8a09d94acb3444a23cdc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107830164"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Dynamiczna zmiana poziomu usługi woluminu

> [!IMPORTANT] 
> Dynamiczne zmienianie poziomu usługi woluminu docelowego replikacji nie jest obecnie obsługiwane.

Poziom usług istniejącego woluminu można zmienić, przenosząc wolumin do [](azure-netapp-files-service-levels.md) innej puli pojemności, która używa poziomu usługi dla woluminu. Ta zmiana poziomu usługi w miejscu dla woluminu nie wymaga migracji danych. Nie ma to również wpływu na dostęp do woluminu.  

Ta funkcja umożliwia spełnienie wymagań obciążeń na żądanie.  Możesz zmienić istniejący wolumin, aby użyć wyższego poziomu usługi w celu lepszej wydajności lub użyć niższego poziomu usługi w celu optymalizacji kosztów. Jeśli na przykład wolumin znajduje się obecnie w puli pojemności, która korzysta z poziomu usługi *Standardowa,* i chcesz, aby wolumin był używany na poziomie usługi *Premium,* możesz dynamicznie przenieść wolumin do puli pojemności, która korzysta z poziomu usługi *Premium.*  

Pula pojemności, do której chcesz przenieść wolumin, musi już istnieć. Pula pojemności może zawierać inne woluminy.  Jeśli chcesz przenieść wolumin do zupełnie nowej puli pojemności, musisz utworzyć pulę pojemności [przed](azure-netapp-files-set-up-capacity-pool.md) przeniesieniem woluminu.  

## <a name="considerations"></a>Zagadnienia do rozważenia

* Po przeniesioniu woluminu do innej puli pojemności nie będziesz już mieć dostępu do poprzednich dzienników aktywności woluminu i metryk woluminów. Wolumin rozpocznie się od nowych dzienników aktywności i metryk w nowej puli pojemności.

* W przypadku przenoszenia woluminu do puli pojemności o wyższym poziomie  usługi (na przykład w przypadku przechodzenia z poziomu usługi  *Standardowa* do Premium lub *Ultra)* należy poczekać co najmniej siedem dni, zanim będzie można ponownie przenieść ten wolumin do puli pojemności o niższym poziomie usługi (na przykład przejście z wersji *Ultra* do wersji *Premium* lub *Standardowa).*  

## <a name="register-the-feature"></a>Rejestrowanie funkcji

Funkcja przenoszenia woluminu do innej puli pojemności jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz tej funkcji po raz pierwszy, musisz najpierw zarejestrować funkcję.

1. Zarejestruj funkcję: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Sprawdź stan rejestracji funkcji: 

    > [!NOTE]
    > **RegistrationState** może być w stanie do `Registering` 60 minut przed zmianą na `Registered` . Przed kontynuowaniem poczekaj, aż **stan będzie się** zmienił na Zarejestrowano.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
Możesz również użyć poleceń interfejsu [wiersza polecenia platformy Azure](/cli/azure/feature) oraz zarejestrować funkcję i wyświetlić stan `az feature register` `az feature show` rejestracji. 
 
## <a name="move-a-volume-to-another-capacity-pool"></a>Przenoszenie woluminu do innej puli pojemności

1.  Na stronie Woluminy kliknij prawym przyciskiem myszy wolumin, którego poziom usługi chcesz zmienić. Wybierz **pozycję Zmień pulę.**

    ![Wolumin klikany prawym przyciskiem myszy](../media/azure-netapp-files/right-click-volume.png)

2. W oknie Zmienianie puli wybierz pulę pojemności, do której chcesz przenieść wolumin. 

    ![Zmienianie puli](../media/azure-netapp-files/change-pool.png)

3.  Kliknij przycisk **OK**.


## <a name="next-steps"></a>Następne kroki  

* [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Rozwiązywanie problemów ze zmianą puli pojemności woluminu](troubleshoot-capacity-pools.md#issues-when-changing-the-capacity-pool-of-a-volume)
