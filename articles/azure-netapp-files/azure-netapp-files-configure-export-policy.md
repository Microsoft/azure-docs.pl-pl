---
title: Konfigurowanie zasad eksportowania dla woluminu NFS — Azure NetApp Files
description: Opisuje sposób konfigurowania zasad eksportu w celu kontrolowania dostępu do woluminu NFS przy użyciu Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/24/2020
ms.openlocfilehash: 6d990b94210383ba4b30569693f4471f43306ed2
ms.sourcegitcommit: d7bd8f23ff51244636e31240dc7e689f138c31f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/24/2020
ms.locfileid: "87169838"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurowanie zasad eksportu dla woluminu NFS

Można skonfigurować zasady eksportowania, aby kontrolować dostęp do woluminu Azure NetApp Files. Azure NetApp Files Eksportowanie zasad obsługuje woluminy korzystające z protokołu NFS (zarówno NFSv3, jak i NFSv 4.1) i Dual Protocol (NFSv3 i SMB). 

Można utworzyć maksymalnie pięć reguł eksportowania zasad.

## <a name="steps"></a>Kroki 

1.  Na stronie woluminy Wybierz wolumin, dla którego chcesz skonfigurować zasady eksportowania, a następnie kliknij pozycję **Eksportuj zasady**. 

    Możesz również skonfigurować zasady eksportowania podczas tworzenia woluminu.

2.  Podaj informacje w następujących polach, aby utworzyć regułę zasad eksportu:   
    *  **Indeks**   
        Określ numer indeksu dla tej reguły.  
        Zasady eksportu mogą zawierać maksymalnie pięć reguł. Reguły są oceniane zgodnie z kolejnością numerów indeksu. Reguły o niższych numerach indeksu są oceniane w pierwszej kolejności. Na przykład reguła o numerze indeksu 1 zostanie oceniona przed regułą o numerze indeksu 2. 

    * **Dozwoleni klienci**   
        Określ wartość w jednym z następujących formatów:  
        * Adres IPv4, na przykład `10.1.12.24`. 
        * Adres IPv4 z maską podsieci wyrażoną za pomocą liczby bitów, na przykład `10.1.12.10/4`.

    * **Dostęp**  
        Wybierz jeden z następujących typów dostępu:  
        * Brak dostępu 
        * Odczyt i zapis
        * Tylko do odczytu

    * **Dostęp do katalogu głównego**  
        Określ, czy `root` konto ma mieć dostęp do woluminu.  Domyślnie dostęp do katalogu głównego jest ustawiony na wartość **włączone**, a `root` konto ma dostęp do woluminu.

        ![Zasady eksportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 



## <a name="next-steps"></a>Następne kroki 
* [Zarządzanie woluminami](azure-netapp-files-manage-volumes.md)
* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Zarządzanie migawkami](azure-netapp-files-manage-snapshots.md)
