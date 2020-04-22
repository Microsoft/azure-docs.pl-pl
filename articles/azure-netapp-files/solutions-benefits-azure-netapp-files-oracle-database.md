---
title: Korzyści z używania plików NetApp platformy Azure z bazą danych Oracle | Dokumenty firmy Microsoft
description: W tym artykule opisano technologię i przedstawiono porównanie wydajności między Oracle Direct NFS (dNFS) a tradycyjnym klientem NFS. Pokazuje zalety korzystania z usług dNFS za pomocą usługi Azure NetApp Files.
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
ms.date: 04/20/2020
ms.author: b-juche
ms.openlocfilehash: d28f5469174a2659869ebb01ef01653b7190e30e
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81772082"
---
# <a name="benefits-of-using-azure-netapp-files-with-oracle-database"></a>Korzyści z używania plików NetApp platformy Azure z bazą danych Oracle Database

Oracle Direct NFS (dNFS) umożliwia wyższą wydajność niż własny sterownik NFS systemu operacyjnego. W tym artykule opisano technologię i przedstawiono porównanie wydajności między dNFS a tradycyjnym klientem systemu plików NFS (Kernel NFS). Pokazuje również zalety i łatwość korzystania z dNFS za pomocą usługi Azure NetApp Files.  

## <a name="how-oracle-direct-nfs-works"></a>Jak działa Oracle Direct NFS

Oracle Direct NFS (dNFS) omija pamięć podręczną buforu systemu operacyjnego. Dane są buforowane tylko raz w przestrzeni użytkownika, eliminując obciążenie kopii pamięci.  

Tradycyjny klient NFS używa pojedynczego przepływu sieciowego, jak pokazano w poniższym przykładzie: 

![Tradycyjny klient NFS przy użyciu jednego przepływu sieciowego](../media/azure-netapp-files/solutions-traditional-nfs-client-using-single-network-flow.png)

Z drugiej strony oracle dNFS zwiększa wydajność poprzez równoważenie obciążenia ruchu sieciowego w wielu przepływach sieciowych. Ta funkcja umożliwia bazy danych Oracle dynamiczne ustanawianie znacznej liczby 650 różnych połączeń sieciowych, jak pokazano w poniższym przykładzie:  

![Oracle Direct NFS zwiększa wydajność](../media/azure-netapp-files/solutions-oracle-direct-nfs-performance-load-balancing.png)

[Oracle FAQ for Direct NFS](http://www.orafaq.com/wiki/Direct_NFS) pokazuje, że Oracle dNFS jest zoptymalizowanym klientem NFS. Zapewnia szybki i skalowalny dostęp do pamięci masowej NFS, która znajduje się na urządzeniach pamięci masowej NAS (dostępnych za pośrednictwem protokołu TCP/IP). dNFS jest wbudowany w jądro bazy danych, podobnie jak ASM, który jest używany głównie z das lub pamięci masowej SAN. W związku *z tym wytyczne jest użycie dNFS podczas implementowania pamięci masowej NAS i używać ASM podczas wdrażania pamięci masowej SAN.*

dNFS jest opcją domyślną w Oracle 18c i domyślną dla RAC.

dNFS jest dostępny począwszy od Bazy Danych Oracle 11g. Poniższy diagram porównuje dNFS z natywnym NFS. Korzystając z dNFS, baza danych Oracle, która działa na maszynie wirtualnej platformy Azure może prowadzić więcej we/wy niż natywny klient systemu plików NFS.

![Oracle i Azure NetApp Files porównanie dNFS z natywnym NFS](../media/azure-netapp-files/solutions-oracle-azure-netapp-files-comparing-dnfs-native-nfs.png)

Można włączyć lub wyłączyć dNFS, uruchamiając dwa polecenia i ponownie uruchamiając bazę danych.

Aby włączyć:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_on`

Aby wyłączyć:  
`cd $ORACLE_HOME/rdbms/lib ; make -f ins_rdbms.mk dnfs_off`

## <a name="azure-netapp-files-combined-with-oracle-direct-nfs"></a>Pliki NetApp platformy Azure w połączeniu z oracle direct nfs

Dzięki usłudze Azure NetApp Files można zwiększyć wydajność usługi Oracle dNFS. Usługa zapewnia całkowitą kontrolę nad wydajnością aplikacji. Może sprostać niezwykle wymagającym aplikacjom. Połączenie oracle dNFS z usługą Azure NetApp Files zapewnia ogromne korzyści dla obciążeń.

## <a name="next-steps"></a>Następne kroki

- [Architektury rozwiązań używające usługi Azure NetApp Files](azure-netapp-files-solution-architectures.md)
- [Omówienie aplikacji i rozwiązań Oracle na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/workloads/oracle/oracle-overview)