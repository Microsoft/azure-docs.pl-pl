---
title: Dynamiczna zmiana poziomu usługi woluminu dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób dynamicznego zmieniania poziomu usługi woluminu.
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
ms.date: 11/06/2020
ms.author: b-juche
ms.openlocfilehash: fe4b2925a34ae7c06bb0b597f0bcdcc3f4d80896
ms.sourcegitcommit: 22da82c32accf97a82919bf50b9901668dc55c97
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/08/2020
ms.locfileid: "94363225"
---
# <a name="dynamically-change-the-service-level-of-a-volume"></a>Dynamiczna zmiana poziomu usługi woluminu

> [!IMPORTANT] 
> Rejestracja publicznej wersji zapoznawczej tej funkcji jest wstrzymana do momentu dalszej uwagi. 

Możesz zmienić poziom usług istniejącego woluminu, przenosząc wolumin do innej puli pojemności, która używa żądanego [poziomu usługi](azure-netapp-files-service-levels.md) dla woluminu. Ta zmiana poziomu usługi w miejscu dla woluminu nie wymaga migracji danych. Nie ma to również wpływu na dostęp do woluminu.  

Ta funkcja pozwala sprostać wymaganiom związanym z obciążeniem.  Możesz zmienić istniejący wolumin, aby użyć wyższego poziomu usługi w celu uzyskania lepszej wydajności lub użyć niższego poziomu usługi do optymalizacji kosztu. Na przykład jeśli wolumin znajduje się obecnie w puli pojemności, która używa *standardowego* poziomu usługi i chcesz, aby wolumin używał poziomu usługi *Premium* , można przenieść wolumin dynamicznie do puli pojemności korzystającej z poziomu usługi *Premium* .  

Pula pojemności, do której ma zostać przeniesiony wolumin, musi już istnieć. Pula pojemności może zawierać inne woluminy.  Jeśli chcesz przenieść wolumin do puli pojemności nowej marki, musisz [utworzyć pulę pojemności](azure-netapp-files-set-up-capacity-pool.md) przed przeniesieniem woluminu.  

## <a name="considerations"></a>Kwestie do rozważenia

* Po przeniesieniu woluminu do innej puli pojemności nie będziesz mieć już dostępu do poprzednich dzienników aktywności woluminu i metryk woluminów. Wolumin rozpocznie pracę od nowych dzienników aktywności i metryk w ramach nowej puli pojemności.

* W przypadku przenoszenia woluminu do puli pojemności wyższego poziomu usługi (na przykład przechodzenia z warstwy *standardowa* do *Premium* lub *Ultra* Service Level) należy odczekać co najmniej siedem dni, zanim będzie można *ponownie* przenieść ten wolumin do puli pojemności niższego poziomu usługi (na przykład przechodzenie między *wersjami* od *Ultra* do *Premium* lub standard).  
<!-- 
## Register the feature

The feature to move a volume to another capacity pool is currently in preview. If you are using this feature for the first time, you need to register the feature first.

1. Register the feature: 

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```

2. Check the status of the feature registration: 

    > [!NOTE]
    > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is **Registered** before continuing.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFTierChange
    ```
You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
--> 
## <a name="move-a-volume-to-another-capacity-pool"></a>Przenoszenie woluminu do innej puli pojemności

1.  Na stronie woluminy kliknij prawym przyciskiem myszy wolumin, którego poziom usługi chcesz zmienić. Wybierz pozycję **Zmień pulę**.

    ![Kliknij prawym przyciskiem myszy wolumin](../media/azure-netapp-files/right-click-volume.png)

2. W oknie zmiana puli wybierz pulę pojemności, do której chcesz przenieść wolumin. 

    ![Pula zmian](../media/azure-netapp-files/change-pool.png)

3.  Kliknij pozycję **OK**.


## <a name="next-steps"></a>Następne kroki  

* [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
