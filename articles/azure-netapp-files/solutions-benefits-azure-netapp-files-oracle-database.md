---
title: Zalety korzystania z Azure NetApp Files z Oracle Database | Microsoft Docs
description: Opisuje technologię i zapewnia porównanie wydajności między programem Oracle Direct NFS (dNFS) a tradycyjnym klientem systemu plików NFS. Pokazuje zalety używania dNFS z Azure NetApp Files.
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
ms.date: 04/23/2020
ms.author: b-juche
ms.openlocfilehash: 56322dc8def288ed388713e143f6b77816360ba3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "82117050"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Korzyści związane z korzystaniem z usługi Azure NetApp Files w bazie danych Oracle Database

Program Oracle Direct NFS (dNFS) umożliwia zwiększenie wydajności niż własny sterownik NFS systemu operacyjnego. W tym artykule opisano technologię i przedstawiono porównanie wydajności między dNFS i tradycyjnym klientem systemu plików NFS (jądrem systemu plików NFS). Przedstawiono w nim również zalety i łatwość używania dNFS z Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Jak działa program Oracle Direct NFS

Poniższe podsumowanie wyjaśnia, jak działa program Oracle Direct NFS na wysokim poziomie:

* Firma Oracle Direct NFS pomija pamięć podręczną buforów systemu operacyjnego. Dane są buforowane tylko raz w przestrzeni użytkownika, eliminując obciążenie kopii pamięci.  

* Tradycyjny klient systemu plików NFS używa pojedynczego przepływu sieci, jak pokazano poniżej:    

    ![Tradycyjny klient systemu plików NFS korzystający z pojedynczego przepływu sieci](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

    Usługa Oracle Direct w systemie NFS zwiększa wydajność dzięki obciążeniu ruchu sieciowego przez wiele przepływów sieciowych. Zgodnie z przetestowanym i pokazanym poniżej 650 odrębne połączenia sieciowe zostały ustanowione dynamicznie przez Oracle Database:  

    ![Bezpośrednia poprawa wydajności systemu plików NFS firmy Oracle](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Często zadawane pytania dotyczące programu Oracle dotyczące usługi Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) pokazują, że Oracle dNFS to zoptymalizowany klient systemu plików NFS. Zapewnia szybki i skalowalny dostęp do magazynu NFS znajdującego się na urządzeniach pamięci masowej NAS (dostępny za pośrednictwem protokołu TCP/IP). dNFS jest wbudowana w jądro bazy danych, podobnie jak ASM, która jest używana głównie z magazynem DAS lub SAN. W związku *z tym wytyczną jest użycie dNFS podczas implementowania magazynu nas i użycie ASM podczas implementowania magazynu sieci SAN.*

dNFS jest opcją domyślną w programie Oracle 18c.

dNFS jest dostępna od Oracle Database 11g. Na poniższym diagramie porównano dNFS z natywnym systemem plików NFS. W przypadku korzystania z dNFS baza danych programu Oracle działająca na maszynie wirtualnej platformy Azure może zwiększyć liczbę operacji we/wy niż w przypadku macierzystego klienta systemu plików NFS.

![Oracle i Azure NetApp Files porównanie dNFS z natywnym systemem plików NFS](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

DNFS można włączyć lub wyłączyć, uruchamiając dwa polecenia i uruchamiając ponownie bazę danych.

Aby włączyć:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Aby wyłączyć:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Azure NetApp Files połączony z usługą Oracle Direct NFS

Wydajność programu Oracle dNFS można zwiększyć przy użyciu usługi Azure NetApp Files. Usługa daje całkowitą kontrolę nad wydajnością aplikacji. Może to być zgodne z niezwykle wymagającymi aplikacjami. Połączenie programu Oracle dNFS z Azure NetApp Files zapewnia dużą korzyść dla obciążeń.

## <a name="next-steps"></a>Następne kroki

- [Architektury rozwiązań używające usługi Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Omówienie aplikacji i rozwiązań firmy Oracle na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)
