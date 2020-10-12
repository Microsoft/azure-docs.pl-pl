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
ms.date: 09/16/2020
ms.author: b-juche
ms.openlocfilehash: 0904ac36a9453e51dbb1efc50eee2b9bf3c669c7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708821"
---
# <a name="delete-replications"></a>Usuwanie replikacji

Możesz przerwać połączenie replikacji między woluminem źródłowym a docelowym, usuwając replikację woluminu. Można wykonać operację usuwania z woluminu źródłowego lub docelowego. Operacja usuwania usuwa tylko autoryzację na potrzeby replikacji; nie powoduje usunięcia źródła ani woluminu docelowego. 

## <a name="steps"></a>Kroki

1. Aby usunąć replikację woluminu, wybierz pozycję **replikacja** ze źródła lub woluminu docelowego.  

2. Kliknij polecenie **Usuń**.    

3. Potwierdź usunięcie, wpisując **Yes** i klikając przycisk **Usuń**.   

    ![Usuń replikację](../media/azure-netapp-files/cross-region-replication-delete-replication.png)

## <a name="next-steps"></a>Następne kroki  

* [Replikacja między regionami](cross-region-replication-introduction.md)
* [Wymagania i zagadnienia dotyczące korzystania z replikacji między regionami](cross-region-replication-requirements-considerations.md)
* [Wyświetlanie stanu kondycji relacji replikacji](cross-region-replication-display-health-status.md)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)

