---
title: Wyświetl stan kondycji Azure NetApp Files relacji replikacji | Microsoft Docs
description: Opisuje sposób wyświetlania stanu replikacji na woluminie źródłowym lub docelowym woluminu Azure NetApp Files.
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
ms.openlocfilehash: 95c1202fb56e882554d40926e9d5ecec7be49086
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90708811"
---
# <a name="display-health-status-of-replication-relationship"></a>Wyświetlanie stanu kondycji relacji replikacji 

Można wyświetlić stan replikacji woluminu źródłowego lub woluminu docelowego.  

## <a name="display-replication-status"></a>Wyświetl stan replikacji

1. Z woluminu źródłowego lub woluminu docelowego kliknij pozycję **replikacja** w obszarze usługa magazynu dla każdego woluminu.

    Wyświetlane są następujące informacje o stanie replikacji i kondycji:  
    * **Typ punktu końcowego** — określa, czy wolumin jest źródłem lub miejscem docelowym replikacji.
    * **Kondycja** — wyświetla stan kondycji relacji replikacji.
    * **Stan dublowania** — pokazuje jedną z następujących wartości:
        * *Niezainicjowane*:  
            Jest to początkowy i domyślny stan podczas tworzenia relacji komunikacji równorzędnej. Stan nie zostanie zainicjowany do momentu pomyślnego zakończenia inicjalizacji.
        * *Zdublowane*:   
            Wolumin docelowy został zainicjowany i jest gotowy do otrzymywania aktualizacji dublowania.
        * *Przerwany*:   
            Jest to stan po przerwaniu relacji komunikacji równorzędnej. Wolumin docelowy to `‘RW’` i migawki są obecne.
    * **Stan relacji** — pokazuje jedną z następujących wartości: 
        * *Bezczynne*:  
            Żadna operacja transferu nie jest w toku, a przyszłe transfery nie są wyłączone.
        * *Przenoszenie*:  
            Operacja transferu jest w toku, a przyszłe transfery nie są wyłączone.
    * **Harmonogram replikacji** — pokazuje, jak często przyrostowe aktualizacje dublowane będą wykonywane po zakończeniu inicjalizacji (kopia bazowa).

    * **Łączny postęp** — pokazuje łączną ilość danych w bajtach transferowanych dla bieżącej operacji transferu. Ta kwota jest rzeczywistym transferem bitów i może się różnić od przestrzeni logicznej, w której jest raportowany woluminy źródłowe i docelowe.  

    ![Stan kondycji replikacji](../media/azure-netapp-files/cross-region-replication-health-status.png)

> [!NOTE] 
> Relacja replikacji pokazuje stan kondycji jako zła *kondycja* , jeśli poprzednie zadania replikacji nie zostały ukończone. Ten stan jest wynikiem transferu dużych woluminów z dolnym oknem transferu (na przykład 10-minutowego czasu transferu dla dużego woluminu). W takim przypadku stan relacji wskazuje, że *transfer* i stan kondycji są wyświetlane w *złej kondycji*.

## <a name="next-steps"></a>Następne kroki  

* [Replikacja między regionami](cross-region-replication-introduction.md)
* [Zarządzanie odzyskiwaniem po awarii](cross-region-replication-manage-disaster-recovery.md)
* [Metryki replikacji woluminów](azure-netapp-files-metrics.md#replication)
* [Rozwiązywanie problemów z replikacją między regionami](troubleshoot-cross-region-replication.md)

