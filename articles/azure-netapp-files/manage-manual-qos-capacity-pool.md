---
title: Zarządzanie ręczną pulą pojemności usługi QoS dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób zarządzania pulą pojemności, która korzysta z ręcznego typu QoS, w tym konfigurowania ręcznej puli pojemności i zmiany puli pojemności do korzystania z ręcznej jakości usług.
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
ms.date: 02/04/2021
ms.author: b-juche
ms.openlocfilehash: 566cc3b1192d632bbffb8f9ef091f291b4bcc6e6
ms.sourcegitcommit: 2817d7e0ab8d9354338d860de878dd6024e93c66
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/05/2021
ms.locfileid: "99581157"
---
# <a name="manage-a-manual-qos-capacity-pool"></a>Zarządzanie ręczną pulą pojemności usługi QoS

W tym artykule opisano sposób zarządzania pulą pojemności, która korzysta z ręcznego typu QoS.  

Zobacz [Hierarchia magazynu Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md) i [zagadnienia dotyczące wydajności dla Azure NetApp Files](azure-netapp-files-performance-considerations.md) , aby zrozumieć zagadnienia dotyczące typów QoS.  

## <a name="register-the-feature"></a>Rejestrowanie funkcji
Ręczna funkcja typu QoS jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz tej funkcji po raz pierwszy, musisz najpierw zarejestrować funkcję.
  
1.  Zarejestruj funkcję:

    ```azurepowershell-interactive
    Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```

2. Sprawdź stan rejestracji funkcji: 

    > [!NOTE]
    > **RegistrationState** może być w stanie od `Registering` do 60 minut przed zmianą na `Registered` . Przed kontynuowaniem Zaczekaj na **zarejestrowanie** stanu.

    ```azurepowershell-interactive
    Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFFlexPool
    ```
Możesz również użyć [poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` , `az feature show` Aby zarejestrować funkcję i wyświetlić stan rejestracji. 

## <a name="set-up-a-new-manual-qos-capacity-pool"></a>Skonfiguruj nową ręczną pulę pojemności usługi QoS 

Aby utworzyć nową pulę pojemności przy użyciu typu ręcznej jakości usługi:

1. Wykonaj kroki opisane w temacie [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md).  

2. W oknie Nowa pula pojemności wybierz typ **ręcznych ustawień QoS** .  

## <a name="change-a-capacity-pool-to-use-manual-qos"></a><a name="change-to-qos"></a>Zmień pulę pojemności, aby korzystać z ręcznej jakości usług

Można zmienić pulę pojemności, która aktualnie używa typu autoqos, aby użyć typu ręcznego QoS.  

> [!IMPORTANT]
> Ustawienie typu pojemności na ręczną QoS jest trwałą zmianą. Nie można skonwertować ręcznego narzędzia pojemności typu QoS na pulę wydajności autoqos.  
> W czasie konwersji poziomy przepływności mogą być ograniczone, aby były zgodne z limitami przepływności dla woluminów typu ręcznej jakości usług. Zobacz [limity zasobów dla Azure NetApp Files](azure-netapp-files-resource-limits.md#resource-limits).

1. W bloku Zarządzanie dla konta usługi NetApp kliknij pozycję **Pule pojemności** , aby wyświetlić istniejące pule pojemności.   
 
2.  Kliknij pulę pojemności, którą chcesz zmienić, aby użyć ręcznej jakości usług.

3.  Kliknij pozycję **Zmień typ QoS**. Następnie ustaw **nowy typ QoS** na **ręczny**. Kliknij przycisk **OK**. 

![Zmień typ QoS](../media/azure-netapp-files/change-qos-type.png)


## <a name="monitor-the-throughput-of-a-manual-qos-capacity-pool"></a>Monitorowanie przepływności ręcznej puli pojemności usługi QoS  

Dostępne są metryki ułatwiające monitorowanie przepływności odczytu i zapisu woluminu.  Zobacz [metryki dla Azure NetApp Files](azure-netapp-files-metrics.md).  

## <a name="modify-the-allotted-throughput-of-a-manual-qos-volume"></a>Modyfikowanie przypisanej przepływności ręcznego woluminu QoS 

Jeśli wolumin jest zawarty w ręcznej puli pojemności usługi QoS, można zmodyfikować przydzieloną przepustowość woluminu zgodnie z wymaganiami.

1. Na stronie **woluminy** Wybierz wolumin, którego przepływność chcesz zmodyfikować.   

2. Kliknij pozycję **Zmień przepływność**. Określ żądaną **przepływność (MIB/S)** . Kliknij przycisk **OK**. 

    ![Zmień przepływność QoS](../media/azure-netapp-files/change-qos-throughput.png)

## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)
* [Metryki dla usługi Azure NetApp Files](azure-netapp-files-metrics.md)
* [Kwestie dotyczące wydajności dla usługi Azure NetApp Files](azure-netapp-files-performance-considerations.md)
* [Rozwiązywanie problemów z pulami pojemności](troubleshoot-capacity-pools.md)
* [Hierarchia magazynu usługi Azure NetApp Files](azure-netapp-files-understand-storage-hierarchy.md)
* [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
* [Model kosztów usługi Azure NetApp Files](azure-netapp-files-cost-model.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Tworzenie woluminu NFS](azure-netapp-files-create-volumes.md)
* [Tworzenie woluminu SMB](azure-netapp-files-create-volumes-smb.md)
* [Tworzenie woluminu dwuprotokołowego](create-volumes-dual-protocol.md)
