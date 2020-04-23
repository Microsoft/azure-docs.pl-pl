---
title: Instalowanie woluminów plików NetApp platformy Azure dla maszyn wirtualnych
description: Dowiedz się, jak zainstalować lub odinstalować wolumin dla maszyn wirtualnych systemu Windows lub maszyn wirtualnych systemu Linux na platformie Azure.
author: b-juche
ms.author: b-juche
ms.service: azure-netapp-files
ms.workload: storage
ms.topic: conceptual
ms.date: 04/22/2020
ms.openlocfilehash: c439ff8df95d759e96d2fc82356bda8551507e8d
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/23/2020
ms.locfileid: "82084944"
---
# <a name="mount-or-unmount-a-volume-for-windows-or-linux-virtual-machines"></a>Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux 

W razie potrzeby można zainstalować lub odinstalować wolumin dla maszyn wirtualnych z systemem Windows lub Linux.  Instrukcje instalacji dla maszyn wirtualnych systemu Linux są dostępne w plikach NetApp platformy Azure.  

1. Kliknij blok **Woluminy,** a następnie wybierz wolumin, dla którego chcesz zamontować. 
2. Kliknij **pozycję Zainstaluj instrukcje** z wybranego woluminu, a następnie postępuj zgodnie z instrukcjami, aby zamontować wolumin. 

    ![Instrukcja montażu NFS](../media/azure-netapp-files/azure-netapp-files-mount-instructions-nfs.png)

    ![Instrukcje montażu SMB](../media/azure-netapp-files/azure-netapp-files-mount-instructions-smb.png)
    
    Jeśli używasz systemu NFSv4.1, użyj następującego polecenia, aby zainstalować system plików:`sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=4.1,tcp,sec=sys $MOUNTTARGETIPADDRESS:/$VOLUMENAME $MOUNTPOINT`  

3. Jeśli chcesz mieć wolumin NFS automatycznie zainstalowany po uruchomieniu lub ponownym uruchomieniu maszyny Wirtualnej platformy Azure, dodaj wpis do `/etc/fstab` pliku na hoście. 

    Na przykład: `$ANFIP:/$FILEPATH        /$MOUNTPOINT    nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0`

    * `$ANFIP`jest adresem IP woluminu Usługi Azure NetApp Files znajdującego się w bloku właściwości woluminu.
    * `$FILEPATH`to ścieżka eksportu woluminu plików NetApp platformy Azure.
    * `$MOUNTPOINT`to katalog utworzony na hoście linuksa używany do instalacji eksportu NFS.

4. Jeśli chcesz zainstalować wolumin w systemie Windows przy użyciu systemu plików NFS:

    a. Najpierw zamontuj wolumin na maszynie wirtualnej Unix lub Linux.  
    b. Uruchom `chmod 777` lub `chmod 775` polecenie względem woluminu.  
    d. Zamontuj wolumin za pośrednictwem klienta systemu plików NFS w systemie Windows.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie domyślnej domeny NFSv4.1 dla usługi Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Najczęściej zadawane pytania dotyczące nfs](https://docs.microsoft.com/azure/azure-netapp-files/azure-netapp-files-faqs#nfs-faqs)
* [Omówienie sieciowego systemu plików](https://docs.microsoft.com/windows-server/storage/nfs/nfs-overview)
