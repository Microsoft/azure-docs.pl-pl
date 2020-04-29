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
ms.topic: conceptual
ms.date: 04/02/2020
ms.author: b-juche
ms.openlocfilehash: b21db3e842898e8ce11b560714888b946373300e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80616433"
---
# <a name="set-up-a-capacity-pool"></a>Konfigurowanie puli pojemności

Skonfigurowanie puli pojemności umożliwia tworzenie woluminów w tej puli.  

## <a name="before-you-begin"></a>Przed rozpoczęciem 

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
      
     ![Nowa pula pojemności](../media/azure-netapp-files/azure-netapp-files-new-capacity-pool.png)

4. Kliknij przycisk **OK**.

## <a name="next-steps"></a>Następne kroki 

- [Poziomy usług dla usługi Azure NetApp Files](azure-netapp-files-service-levels.md)
- Informacje o różnych poziomach usługi można uzyskać na [stronie z cennikiem dla usługi Azure NetApp Files](https://azure.microsoft.com/pricing/details/storage/netapp/)
- [Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)
