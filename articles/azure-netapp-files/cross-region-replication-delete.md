---
title: Usuwanie replikacji woluminów lub woluminów dla Azure NetApp Files replikacji między regionami | Microsoft Docs
description: Opisuje sposób usuwania połączenia replikacji, które nie jest już potrzebne między woluminami źródłowymi i docelowymi.
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
ms.date: 11/18/2020
ms.author: b-juche
ms.openlocfilehash: 5ce7a591acd8203775808457219b0ec392cd696e
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "95249898"
---
# <a name="delete-volume-replications-or-volumes"></a>Usuwanie replikacji woluminów lub woluminów

W tym artykule opisano sposób usuwania replikacji woluminów. Opisano w nim również sposób usuwania woluminu źródłowego lub docelowego.

## <a name="delete-volume-replications"></a>Usuwanie replikacji woluminów

Możesz przerwać połączenie replikacji między woluminem źródłowym a docelowym, usuwając replikację woluminu. Należy usunąć replikację z woluminu docelowego. Operacja usuwania usuwa tylko autoryzację na potrzeby replikacji; nie powoduje usunięcia źródła ani woluminu docelowego. 

1. Upewnij się, że Komunikacja równorzędna replikacji została przerwana przed usunięciem replikacji woluminu. Aby przerwać replikację komunikacji równorzędnej: 

    1. Wybierz wolumin *docelowy* . Kliknij pozycję **replikacja** w obszarze usługa magazynu.  

    2.  Przed kontynuowaniem sprawdź następujące pola:  
        * Upewnij się, że stan dublowania pokazuje ***dublowanie***.   
            Nie należy próbować przerwać replikacji komunikacji równorzędnej, jeśli stan dublowany pokazuje *niezainicjowany*.
        * Upewnij się, że stan relacji jest wyświetlany jako ***bezczynny***.   
            Nie należy próbować przerwać replikacji komunikacji równorzędnej, jeśli stan relacji pokazuje *transfer*.   

        Zobacz [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md). 

    3.  Kliknij pozycję **Przerwij komunikację równorzędną**.  

    4.  Wpisz **tak** po wyświetleniu monitu, a następnie kliknij przycisk **Przerwij**. 

        ![Przerwij replikację komunikacji równorzędnej](../media/azure-netapp-files/cross-region-replication-break-replication-peering.png)


1. Aby usunąć replikację woluminu, wybierz pozycję **replikacja** ze źródła lub woluminu docelowego.  

2. Kliknij polecenie **Usuń**.    

3. Potwierdź usunięcie, wpisując **Yes** i klikając przycisk **Usuń**.   

    ![Usuń replikację](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="delete-source-or-destination-volumes"></a>Usuń woluminy źródłowe lub docelowe

Jeśli chcesz usunąć wolumin źródłowy lub docelowy, należy wykonać następujące kroki w opisanej kolejności. W przeciwnym razie wystąpi `Volume with replication cannot be deleted` błąd.  

1. Z woluminu docelowego [Usuń replikację woluminu](#delete-volume-replications).   

2. Usuń wolumin docelowy lub źródłowy zgodnie z wymaganiami, klikając prawym przyciskiem myszy nazwę woluminu i wybierając polecenie **Usuń**.   

    ![Zrzut ekranu, który pokazuje menu prawym przyciskiem myszy woluminu.](../media/azure-netapp-files/cross-region-replication-delete-volume.png)

## <a name="next-steps"></a>Następne kroki  

* [Replikacja między regionami](cross-region-replication-introduction.md)
* [Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)

