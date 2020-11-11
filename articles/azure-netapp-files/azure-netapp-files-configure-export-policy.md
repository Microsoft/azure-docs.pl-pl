---
title: Konfigurowanie zasad eksportowania dla woluminu NFS — Azure NetApp Files
description: Opisuje sposób konfigurowania zasad eksportu w celu kontrolowania dostępu do woluminu NFS przy użyciu Azure NetApp Files
services: azure-netapp-files
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: how-to
ms.date: 07/27/2020
ms.openlocfilehash: 77630ddcd61d17f3b47e6cb5d43396c1a6f0e904
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94517873"
---
# <a name="configure-export-policy-for-an-nfs-volume"></a>Konfigurowanie zasad eksportu dla woluminu NFS

Można skonfigurować zasady eksportowania, aby kontrolować dostęp do woluminu Azure NetApp Files. Azure NetApp Files Eksportowanie zasad obsługuje woluminy korzystające z protokołu NFS (zarówno NFSv3, jak i NFSv 4.1) i Dual Protocol (NFSv3 i SMB). 

Można utworzyć maksymalnie pięć reguł eksportowania zasad.

## <a name="configure-the-policy"></a>Konfigurowanie zasad 

1.  Na stronie **woluminy** Wybierz wolumin, dla którego chcesz skonfigurować zasady eksportowania, a następnie wybierz pozycję **Eksportuj zasady**. Możesz również skonfigurować zasady eksportowania podczas tworzenia woluminu.

2.  Aby utworzyć regułę eksportu zasad, podaj następujące informacje:   
    * **Indeks** : Określ numer indeksu dla reguły.  
      
      Zasady eksportu mogą zawierać maksymalnie pięć reguł. Reguły są oceniane zgodnie z kolejnością numerów indeksu. Reguły o niższych numerach indeksu są oceniane w pierwszej kolejności. Na przykład reguła o numerze indeksu 1 zostanie oceniona przed regułą o numerze indeksu 2. 

    * Liczba **dozwolonych klientów** : Określ wartość w jednym z następujących formatów:  
      * Adres IPv4. Przykład: `10.1.12.24`
      * Adres IPv4 z maską podsieci wyrażoną jako liczba bitów. Przykład: `10.1.12.10/4`
      * Adresy IP oddzielane przecinkami. Można wprowadzić wiele adresów IP hosta w jednej regule, rozdzielając je przecinkami. Przykład: `10.1.12.25,10.1.12.28,10.1.12.29`

    * **Dostęp** : Wybierz jeden z następujących typów dostępu:  
      * Brak dostępu 
      * Odczyt i zapis
      * Tylko do odczytu

    * **Tylko do odczytu** i **odczytu/zapisu** : Jeśli używasz szyfrowania Kerberos z nfsv 4.1, postępuj zgodnie z instrukcjami w temacie [Konfigurowanie nfsv 4.1 szyfrowanie Kerberos](configure-kerberos-encryption.md).  Aby uzyskać wpływ na wydajność protokołu Kerberos, zobacz [wpływ na wydajność protokołu Kerberos w systemie nfsv 4.1](configure-kerberos-encryption.md#kerberos_performance). 

      ![Opcje zabezpieczeń protokołu Kerberos](../media/azure-netapp-files/kerberos-security-options.png) 

    * **Dostęp do katalogu głównego** : Określ, czy `root` konto ma dostęp do woluminu.  Domyślnie dostęp do katalogu głównego jest ustawiony na wartość **włączone** , a `root` konto ma dostęp do woluminu.

      ![Zasady eksportu](../media/azure-netapp-files/azure-netapp-files-export-policy.png) 

## <a name="next-steps"></a>Następne kroki 
* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Zarządzanie migawkami](azure-netapp-files-manage-snapshots.md)
