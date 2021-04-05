---
title: Tworzenie woluminu NFS dla Azure NetApp Files | Microsoft Docs
description: W tym artykule opisano sposób tworzenia woluminu NFS w Azure NetApp Files. Dowiedz się więcej na temat zagadnień, takich jak używane wersje oraz najlepsze rozwiązania.
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
ms.date: 09/24/2020
ms.author: b-juche
ms.openlocfilehash: 2cc9d3e0fb711a0662852ce4f2c5a08dc626f246
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96854737"
---
# <a name="create-an-nfs-volume-for-azure-netapp-files"></a>Tworzenie woluminu NFS dla usługi Azure NetApp Files

Azure NetApp Files obsługuje tworzenie woluminów przy użyciu systemu plików NFS (NFSv3 i NFSv 4.1), protokołu SMB3 lub Dual Protocol (NFSv3 i SMB). Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. W tym artykule pokazano, jak utworzyć wolumin systemu plików NFS. 

## <a name="before-you-begin"></a>Zanim rozpoczniesz 
* Potrzebujesz skonfigurowanej puli pojemności.  
    Zobacz [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md).   
* Podsieć musi być delegowana do usługi Azure NetApp Files.  
    Zobacz [delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Zagadnienia do rozważenia 

* Wybór wersji systemu plików NFS do użycia  
  NFSv3 może obsługiwać wiele różnych przypadków użycia i jest często wdrażany w większości aplikacji dla przedsiębiorstw. Należy sprawdzić poprawność wersji (NFSv3 lub NFSv 4.1) wymaganej przez aplikację i utworzyć wolumin przy użyciu odpowiedniej wersji. Jeśli na przykład używasz platformy [Apache ActiveMQ](https://activemq.apache.org/shared-file-system-master-slave), zalecane jest blokowanie plików z nfsv 4.1 w NFSv3. 

* Zabezpieczenia  
  Obsługa bitów trybu systemu UNIX (odczyt, zapis i wykonywanie) jest dostępna dla NFSv3 i NFSv 4.1. Aby można było zainstalować woluminy NFS, na kliencie NFS jest wymagany dostęp na poziomie głównym.

* Lokalna użytkownicy/Grupa i obsługa protokołu LDAP dla NFSv 4.1  
  Obecnie NFSv 4.1 obsługuje tylko dostęp do woluminów głównych. Zobacz [Konfigurowanie domyślnej domeny nfsv 4.1 dla Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md). 

## <a name="best-practice"></a>Najlepsze rozwiązanie

* Upewnij się, że używasz odpowiednich instrukcji instalacji dla woluminu.  Zobacz [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md).

* Klient systemu plików NFS powinien znajdować się w tej samej sieci wirtualnej lub komunikacji równorzędnej co Azure NetApp Files woluminie. Połączenie spoza sieci wirtualnej jest obsługiwane; jednak spowoduje to wprowadzenie dodatkowych opóźnień i zmniejszenie ogólnej wydajności.

* Upewnij się, że klient NFS jest aktualny i uruchomiono najnowsze aktualizacje dla systemu operacyjnego.

## <a name="create-an-nfs-volume"></a>Tworzenie woluminu NFS

1.  Kliknij blok **woluminy** w bloku pule pojemności. Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  W oknie Tworzenie woluminu kliknij pozycję **Utwórz**, a następnie podaj informacje dotyczące następujących pól na karcie podstawowe:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w ramach każdej puli pojemności. Musi zawierać co najmniej trzy znaki. Można użyć dowolnych znaków alfanumerycznych.   

        Nie można użyć `default` lub `bin` jako nazwy woluminu.

    * **Pula pojemności**  
        Określ pulę pojemności, w której ma zostać utworzony wolumin.

    * **limit przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Przepływność (MiB/S)**   
        Jeśli wolumin jest tworzony w ręcznej puli pojemności usługi QoS, określ przepływność dla danego woluminu.   

        Jeśli wolumin jest tworzony w puli pojemności usługi autoqos, wartość wyświetlana w tym polu to (przepływność na poziomie usługi).   

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Możesz również uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Jeśli chcesz zastosować istniejące zasady migawek do woluminu, kliknij przycisk **Pokaż sekcję zaawansowaną** , aby je rozwinąć, określ, czy chcesz ukryć ścieżkę migawki, a następnie wybierz pozycję Zasady migawek w menu rozwijanym. 

        Aby uzyskać informacje na temat tworzenia zasad migawek, zobacz [Zarządzanie zasadami migawek](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Pokaż zaznaczenie zaawansowane](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Kliknij pozycję **Protokół**, a następnie wykonaj następujące czynności:  
    * Wybierz system **plików NFS** jako typ protokołu dla woluminu.   
    * Określ **ścieżkę pliku** , która zostanie użyta do utworzenia ścieżki eksportu dla nowego woluminu. Ścieżka eksportu służy do instalowania woluminu oraz uzyskiwania do niego dostępu.

        Nazwa ścieżki pliku może zawierać tylko litery, cyfry i łączniki („-”). Musi mieć długość od 16 do 40 znaków. 

        Ścieżka pliku musi być unikatowa w ramach każdej subskrypcji i każdego regionu. 

    * Wybierz wersję systemu plików NFS (**NFSv3** lub **nfsv 4.1**) dla woluminu.  

    * Jeśli używasz NFSv 4.1, wskaż, czy chcesz włączyć szyfrowanie **Kerberos** dla woluminu.  

        Dodatkowe konfiguracje są wymagane, jeśli używasz protokołu Kerberos z NFSv 4.1. Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie nfsv 4.1 szyfrowanie Kerberos](configure-kerberos-encryption.md).

    * Opcjonalnie [Skonfiguruj zasady eksportowania dla woluminu systemu plików NFS](azure-netapp-files-configure-export-policy.md).

    ![Określanie protokołu NFS](../media/azure-netapp-files/azure-netapp-files-protocol-nfs.png)

4. Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły woluminu.  Następnie kliknij przycisk **Utwórz** , aby utworzyć wolumin.

    Utworzony wolumin zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.


## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie domyślnej domeny NFSv4.1 dla usługi Azure NetApp Files](azure-netapp-files-configure-nfsv41-domain.md)
* [Konfigurowanie szyfrowania Kerberos NFSv4.1](configure-kerberos-encryption.md)
* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Konfigurowanie zasad eksportu dla woluminu NFS](azure-netapp-files-configure-export-policy.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)