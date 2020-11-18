---
title: Usuwanie replikacji dla Azure NetApp Files replikacji między regionami | Microsoft Docs
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
ms.date: 11/17/2020
ms.author: b-juche
ms.openlocfilehash: e08b69271ba9d115c26418bc5e421ee6c94b031d
ms.sourcegitcommit: e2dc549424fb2c10fcbb92b499b960677d67a8dd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94695711"
---
# <a name="delete-replications"></a>Usuwanie replikacji

Możesz przerwać połączenie replikacji między woluminem źródłowym a docelowym, usuwając replikację woluminu. Można wykonać operację usuwania z woluminu źródłowego lub docelowego. Operacja usuwania usuwa tylko autoryzację na potrzeby replikacji; nie powoduje usunięcia źródła ani woluminu docelowego. 

## <a name="steps"></a>Kroki

1. Upewnij się, że Komunikacja równorzędna replikacji została przerwana przed usunięciem replikacji woluminu.    
    Zobacz [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md) i [przerywanie replikacji komunikacji równorzędnej](cross-region-replication-manage-disaster-recovery.md#break-replication-peering-to-activate-the-destination-volume).  

1. Aby usunąć replikację woluminu, wybierz pozycję **replikacja** ze źródła lub woluminu docelowego.  

2. Kliknij polecenie **Usuń**.    

3. Potwierdź usunięcie, wpisując **Yes** i klikając przycisk **Usuń**.   

    ![Usuń replikację](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Następne kroki  

* [Replikacja między regionami](cross-region-replication-introduction.md)
* [Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)

