---
title: Konfigurowanie puli pojemności na potrzeby usługi Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano sposób konfigurowania puli pojemności, w której można następnie tworzyć woluminy.
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
ms.date: 09/22/2020
ms.author: b-juche
ms.openlocfilehash: 2b52ad50854092cddd7b9e79cbeebd4a83017081
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91325418"
---
# <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności

Skonfigurowanie puli pojemności umożliwia tworzenie woluminów w tej puli.  

## <a name="before-you-begin"></a>Zanim rozpoczniesz 

Potrzebujesz utworzonego konta usługi NetApp.   

[Tworzenie konta usługi NetApp](azure-netapp-files-create-netapp-account.md)

## <a name="steps"></a>Kroki 

1. Przejdź do bloku zarządzania na koncie usługi NetApp, a następnie w okienku nawigacji kliknij pozycję **Pule pojemności**.  
    
    ![Przechodzenie do puli pojemności](../media/azure-netapp-files/azure-netapp-files-navigate-to-capacity-pool.png)

2. Kliknij pozycję **+ Dodaj pule**, aby dodać nową pulę pojemności.   
    Zostanie wyświetlone okno Nowa pula pojemności.

3. Podaj następujące informacje dotyczące nowej puli pojemności:  
   * **Nazwa**  
     Określ nazwę puli pojemności.  
     Nazwa puli pojemności musi być unikatowa na tym koncie usługi NetApp.

   * **Poziom usług**   
     To pole wskazuje docelową wydajność puli pojemności.  
     Określ poziom usługi dla puli pojemności: [**Ultra**](azure-netapp-files-service-levels.md#Ultra), [**Premium**](azure-netapp-files-service-levels.md#Premium)lub [**Standard**](azure-netapp-files-service-levels.md#Standard).

    * **Zmienia**     
     Określ rozmiar kupowanej puli pojemności.        
     Minimalny rozmiar puli pojemności to 4 TiB. Można utworzyć pulę o rozmiarze będącym wielokrotnością 4 TiB.   

   * **QoS**   
     Określ, czy pula pojemności ma używać typu **ręcznego** , **czy autoqos.**  

     Zobacz tematy dotyczące hierarchii i [wydajności](azure-netapp-files-performance-considerations.md) [magazynu](azure-netapp-files-understand-storage-hierarchy.md) , aby poznać typy QoS.  

     > [!IMPORTANT] 
     > Ustawienie **typu QoS** na **ręczny** jest trwałe. Nie można przekonwertować ręcznej puli pojemności usługi QoS, aby używać funkcji autoqos. Można jednak skonwertować pulę pojemności autoqos, aby używać ręcznej jakości usług. Zobacz [zmiana puli pojemności, aby użyć ręcznej jakości](manage-manual-qos-capacity-pool.md#change-to-qos)usług.   
     > Korzystanie z ręcznego typu QoS dla puli pojemności wymaga rejestracji. Zobacz [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md#register-the-feature). 

    ![Nowa pula pojemności](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kliknij przycisk **Utwórz**.

## <a name="next-steps"></a>Następne kroki 

- [Hierarchia magazynu](azure-netapp-files-understand-storage-hierarchy.md) 
- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- [Strona cennika Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Zarządzanie ręczną pulą pojemności usługi QoS](manage-manual-qos-capacity-pool.md)
- [Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
