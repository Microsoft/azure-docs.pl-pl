---
title: Tworzenie woluminu Dual-Protocol (NFSv3 i SMB) dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób tworzenia woluminu korzystającego z dwóch protokołów NFSv3 i SMB z obsługą mapowania użytkowników LDAP.
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
ms.date: 10/12/2020
ms.author: b-juche
ms.openlocfilehash: 4fa2c724906c8a6bfb294541b6616ddc7ae22df6
ms.sourcegitcommit: 1cf157f9a57850739adef72219e79d76ed89e264
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/13/2020
ms.locfileid: "94591652"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Tworzenie woluminu Dual-Protocol (NFSv3 i SMB) dla Azure NetApp Files

Azure NetApp Files obsługuje tworzenie woluminów przy użyciu systemu plików NFS (NFSv3 i NFSv 4.1), SMBv3 lub Dual Protocol. W tym artykule opisano sposób tworzenia woluminu korzystającego z dwóch protokołów NFSv3 i SMB z obsługą mapowania użytkowników LDAP.  


## <a name="before-you-begin"></a>Przed rozpoczęciem 

* Trzeba już utworzyć pulę pojemności.  
    Zobacz [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md).   
* Podsieć musi być delegowana do usługi Azure NetApp Files.  
    Zobacz [delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Kwestie do rozważenia

* Upewnij się, że spełniasz [wymagania dotyczące Active Directory połączeń](azure-netapp-files-create-volumes-smb.md#requirements-for-active-directory-connections). 
* Utwórz strefę wyszukiwania wstecznego na serwerze DNS, a następnie Dodaj rekord wskaźnika (PTR) maszyny hosta usługi AD do tej strefy wyszukiwania wstecznego. W przeciwnym razie tworzenie dwuprotokołowego woluminu nie powiedzie się.
* Upewnij się, że klient sieciowego systemu plików jest aktualny i ma najnowsze aktualizacje systemu operacyjnego.
* Upewnij się, że serwer LDAP Active Directory (AD) jest uruchomiony w usłudze AD. Można to zrobić przez zainstalowanie i skonfigurowanie roli [usługi LDS Active Directory (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na maszynie usługi AD.
* Upewnij się, że urząd certyfikacji jest tworzony w usłudze AD przy użyciu roli [usług certyfikatów Active Directory (AD CS)](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) do generowania i eksportowania certyfikatu głównego urzędu certyfikacji z podpisem własnym.   
* Woluminy podwójnego protokołu nie obsługują obecnie Azure Active Directory Domain Services (AADDS).  
* Wersja systemu plików NFS używana przez dwuprotokołowy wolumin to NFSv3. W związku z tym obowiązują następujące zagadnienia:
    * Protokół Dual nie obsługuje rozszerzonych atrybutów list ACL systemu Windows `set/get` z klientów NFS.
    * Klienci NFS nie mogą zmieniać uprawnień do stylu zabezpieczeń systemu plików NTFS, a klienci systemu Windows nie mogą zmieniać uprawnień dla woluminów z podwójnym protokołem w stylu systemu UNIX.   

    W poniższej tabeli opisano style zabezpieczeń i ich skutki:  
    
    | Styl zabezpieczeń    | Klienci, którzy mogą modyfikować uprawnienia   | Uprawnienia, których mogą używać klienci  | Wynikający ze skutecznego stylu zabezpieczeń    | Klienci, którzy mogą uzyskiwać dostęp do plików     |
    |-  |-  |-  |-  |-  |
    | UNIX  | NFS   | NFSv3 tryb bitowy   | UNIX  | NFS i Windows   |
    | NTFS  | Windows   | Listy ACL NTFS     | NTFS  |NFS i Windows|

## <a name="create-a-dual-protocol-volume"></a>Tworzenie woluminu dwuprotokołowego

1.  Kliknij blok **woluminy** w bloku pule pojemności. Kliknij pozycję **+ Dodaj wolumin** , aby utworzyć wolumin. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  W oknie Tworzenie woluminu kliknij pozycję **Utwórz** , a następnie podaj informacje dotyczące następujących pól na karcie podstawowe:   
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
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy** , aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Jeśli chcesz zastosować istniejące zasady migawek do woluminu, kliknij przycisk **Pokaż sekcję zaawansowaną** , aby je rozwinąć, określ, czy chcesz ukryć ścieżkę migawki, a następnie wybierz pozycję Zasady migawek w menu rozwijanym. 

        Aby uzyskać informacje na temat tworzenia zasad migawek, zobacz [Zarządzanie zasadami migawek](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Pokaż zaznaczenie zaawansowane](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Kliknij pozycję **Protokół** , a następnie wykonaj następujące czynności:  
    * Wybierz pozycję **Dual-Protocol (NFSv3 i SMB)** jako typ protokołu dla woluminu.   

    * Z listy rozwijanej wybierz połączenie **Active Directory** .  
    Używany Active Directory musi mieć certyfikat głównego urzędu certyfikacji serwera. 

    * Określ **ścieżkę woluminu** dla woluminu.   
    Ta ścieżka woluminu jest nazwą udostępnionego woluminu. Nazwa musi zaczynać się od litery i musi być unikatowa w ramach każdej subskrypcji i każdego regionu.  

    * Określ **styl zabezpieczeń** , który ma być używany: NTFS (domyślnie) lub UNIX.

    * Opcjonalnie [Skonfiguruj zasady eksportowania dla tego woluminu](azure-netapp-files-configure-export-policy.md).

    ![Określanie podwójnego protokołu](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły woluminu. Następnie kliknij przycisk **Utwórz** , aby utworzyć wolumin.

    Utworzony wolumin zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="upload-active-directory-certificate-authority-public-root-certificate"></a>Przekaż Active Directory publiczny certyfikat główny urzędu certyfikacji  

1.  Aby zainstalować i skonfigurować urząd certyfikacji, należy postępować zgodnie z instrukcjami [instalacji](/windows-server/networking/core-network-guide/cncg/server-certs/install-the-certification-authority) . 

2.  Postępuj zgodnie [z wyświetlaniem certyfikatów z przystawką programu MMC](/dotnet/framework/wcf/feature-details/how-to-view-certificates-with-the-mmc-snap-in) , aby użyć przystawki programu MMC i narzędzia Menedżer certyfikatów.  
    Użyj przystawki Menedżer certyfikatów, aby zlokalizować certyfikat główny lub wystawiający dla urządzenia lokalnego. Polecenia przystawki Zarządzanie certyfikatami należy uruchamiać z jednego z następujących ustawień:  
    * Klient oparty na systemie Windows, który przyłączył się do domeny i ma zainstalowany certyfikat główny 
    * Inny komputer w domenie zawierający certyfikat główny  

3. Wyeksportuj certyfikat główny.  
    Upewnij się, że certyfikat został wyeksportowany w formacie X. 509 z kodowaniem Base-64 (. CER): 

    ![Kreator eksportu certyfikatów](../media/azure-netapp-files/certificate-export-wizard.png)

4. Przejdź do konta NetApp dla dwuprotokołowego woluminu, kliknij przycisk **Active Directory połączenia** i przekaż certyfikat głównego urzędu certyfikacji przy użyciu okna **Active Directory sprzężenia** :  

    ![Certyfikat głównego urzędu certyfikacji serwera](../media/azure-netapp-files/server-root-ca-certificate.png)

    Upewnij się, że nazwa urzędu certyfikacji może zostać rozpoznana przez system DNS. Ta nazwa to pole "wystawione przez" lub "wystawca" certyfikatu:  

    ![Informacje o certyfikacie](../media/azure-netapp-files/certificate-information.png)

## <a name="manage-ldap-posix-attributes"></a>Zarządzanie atrybutami LDAP POSIX

Można zarządzać atrybutami POSIX, takimi jak UID, katalogiem macierzystym i innymi wartościami, za pomocą przystawki MMC Użytkownicy i komputery Active Directory.  Poniższy przykład pokazuje Edytor atrybutów Active Directory:  

![Edytor atrybutów Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Należy ustawić następujące atrybuty dla użytkowników LDAP i grup LDAP: 
* Wymagane atrybuty dla użytkowników LDAP:   
    `uid`: Alicja, `uidNumber` : 139, `gidNumber` : 555, `objectClass` : posixAccount
* Wymagane atrybuty dla grup LDAP:   
    `objectClass`: "POSIX", `gidNumber` : 555

## <a name="configure-the-nfs-client"></a>Konfigurowanie klienta NFS 

Postępuj zgodnie z instrukcjami w temacie [Konfigurowanie klienta NFS dla Azure NetApp Files](configure-nfs-clients.md) , aby skonfigurować klienta systemu plików NFS.  

## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie klienta sieciowego systemu plików dla usługi Azure NetApp Files](configure-nfs-clients.md)
* [Rozwiązywanie problemów z woluminami używającymi dwóch protokołów](troubleshoot-dual-protocol-volumes.md)