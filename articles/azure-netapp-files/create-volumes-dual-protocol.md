---
title: Utwórz wolumin z podwójnym protokołem (NFSv3 i SMB) dla Azure NetApp Files | Microsoft Docs
description: Opisuje sposób tworzenia woluminu, który używa podwójnego protokołu NFSv3 i SMB z obsługą mapowania użytkowników LDAP.
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
ms.date: 04/19/2021
ms.author: b-juche
ms.openlocfilehash: c702c41228512eceebeaf45ccae709db38a85a51
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107725688"
---
# <a name="create-a-dual-protocol-nfsv3-and-smb-volume-for-azure-netapp-files"></a>Utwórz wolumin z podwójnym protokołem (NFSv3 i SMB) dla Azure NetApp Files

Azure NetApp Files obsługuje tworzenie woluminów przy użyciu systemu plików NFS (NFSv3 i NFSv4.1), SMB3 lub podwójnego protokołu. W tym artykule pokazano, jak utworzyć wolumin, który używa podwójnego protokołu NFSv3 i SMB z obsługą mapowania użytkowników LDAP. 

Aby utworzyć woluminy NFS, zobacz [Tworzenie woluminu NFS](azure-netapp-files-create-volumes.md). Aby utworzyć woluminy SMB, zobacz [Tworzenie woluminu SMB.](azure-netapp-files-create-volumes-smb.md) 

## <a name="before-you-begin"></a>Zanim rozpoczniesz 

* Pula pojemności musi być już utworzona.  
    Zobacz [Konfigurowanie puli pojemności.](azure-netapp-files-set-up-capacity-pool.md)   
* Podsieć musi być delegowana do usługi Azure NetApp Files.  
    Zobacz [Delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="considerations"></a>Zagadnienia do rozważenia

* Upewnij się, że spełniasz [wymagania dotyczące połączeń usługi Active Directory.](create-active-directory-connections.md#requirements-for-active-directory-connections) 
* Utwórz strefę wyszukiwania wstecznego na serwerze DNS, a następnie dodaj rekord wskaźnika (PTR) maszyny hosta usługi AD w tej strefie wyszukiwania wstecznego. W przeciwnym razie tworzenie woluminów dwu protokołów nie powiedzie się.
* Upewnij się, że klient sieciowego systemu plików jest aktualny i ma najnowsze aktualizacje systemu operacyjnego.
* Upewnij się, że serwer LDAP usługi Active Directory (AD) jest uruchomiony w usłudze AD. Można to zrobić, instalując i [konfigurując rolę Usługi LDS Active Directory (AD LDS)](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/hh831593(v=ws.11)) na maszynie usługi AD.
* Woluminy z podwójnym protokołem nie obsługują obecnie Azure Active Directory Domain Services (AADDS). Nie można włączyć protokołu LDAP za pośrednictwem protokołu TLS, jeśli używasz usługi AADDS.
* Wersja systemu plików NFS używana przez wolumin z dwoma protokołami to NFSv3. W związku z tym należy wziąć pod uwagę następujące kwestie:
    * Podwójny protokół nie obsługuje atrybutów rozszerzonych listy ACL systemu Windows `set/get` z klientów systemu plików NFS.
    * Klienci systemu plików NFS nie mogą zmieniać uprawnień do stylu zabezpieczeń ntfs, a klienci systemu Windows nie mogą zmieniać uprawnień dla woluminów z podwójnym protokołem w stylu systemu UNIX.   

    W poniższej tabeli opisano style zabezpieczeń i ich efekty:  
    
    | Styl zabezpieczeń    | Klienci, którzy mogą modyfikować uprawnienia   | Uprawnienia, których mogą używać klienci  | Wynikowy skuteczny styl zabezpieczeń    | Klienci, którzy mogą uzyskać dostęp do plików     |
    |-  |-  |-  |-  |-  |
    | `Unix`    | NFS   | Bity trybu NFSv3   | UNIX  | System plików NFS i system Windows   |
    | `Ntfs`    | Windows   | Ntfs ACL     | NTFS  |System plików NFS i system Windows|
* Użytkownicy systemu UNIX łoący wolumin w stylu zabezpieczeń NTFS przy użyciu systemu plików NFS będą uwierzytelniani jako użytkownicy systemu Windows dla systemu UNIX i `root` `root` dla wszystkich innych `pcuser` użytkowników. Przed instalowaniem woluminu w przypadku korzystania z systemu plików NFS upewnij się, że te konta użytkowników istnieją w usłudze Active Directory. 
* Jeśli masz duże topologie i używasz stylu zabezpieczeń z woluminem z dwoma protokołami lub LDAP z rozszerzonymi grupami, Azure NetApp Files może nie mieć dostępu do wszystkich serwerów w twoich `Unix` topologiach.  W takiej sytuacji skontaktuj się z zespołem klienta w celu uzyskania pomocy.  <!-- NFSAAS-15123 --> 
* Do utworzenia woluminu dwu protokołowego nie jest potrzebny certyfikat głównego urzędu certyfikacji serwera. Jest to wymagane tylko wtedy, gdy jest włączona funkcja LDAP za pośrednictwem protokołu TLS.


## <a name="create-a-dual-protocol-volume"></a>Tworzenie woluminu dwuprotokołowego

1.  Kliknij blok **Woluminy** w bloku Pule pojemności. Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin. 

    ![Przechodzenie do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png) 

2.  W oknie Tworzenie woluminu kliknij pozycję **Utwórz** i podaj informacje dla następujących pól na karcie Podstawowe informacje:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w obrębie każdej puli pojemności. Musi zawierać co najmniej trzy znaki. Można użyć dowolnych znaków alfanumerycznych.   

        Nie można użyć `default` ani `bin` jako nazwy woluminu.

    * **Pula pojemności**  
        Określ pulę pojemności, w której ma zostać utworzony wolumin.

    * **Przydziału**  
        Określ wielkość magazynu logicznego, który zostanie przydzielony do woluminu.  

        W polu **Dostępny limit przydziału** jest wyświetlana ilość nieużywanego miejsca w wybranej puli pojemności, które można wykorzystać do utworzenia nowego woluminu. Rozmiar nowego woluminu nie może przekraczać dostępnego limitu przydziału.  

    * **Przepływność (MiB/S)**   
        Jeśli wolumin jest tworzony w ręcznej puli pojemności QoS, określ przepływność dla woluminu.   

        Jeśli wolumin jest tworzony w puli pojemności automatycznego QoS, wartość wyświetlana w tym polu to (limit przydziału x przepływność na poziomie usługi).   

    * **Sieć wirtualna**  
        Określ sieć wirtualną platformy Azure, z której chcesz uzyskać dostęp do woluminu.  

        W wybranej sieci wirtualnej musi znajdować się podsieć delegowana do usługi Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej znajdującej się w tym samym regionie, co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnej. Dostęp do woluminu można również uzyskać z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nowe** na stronie Utwórz wolumin. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Jeśli chcesz zastosować istniejące zasady migawki do  woluminu, kliknij pozycję Pokaż sekcję Zaawansowane, aby ją rozwinąć, określ, czy chcesz ukryć ścieżkę migawki, a następnie wybierz zasady migawki z menu rozwijanego. 

        Aby uzyskać informacje na temat tworzenia zasad migawek, zobacz [Zarządzanie zasadami migawek.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Pokaż wybór zaawansowany](../media/azure-netapp-files/volume-create-advanced-selection.png)

3. Kliknij **pozycję Protokół**, a następnie wykonaj następujące czynności:  
    * Wybierz **podwójny protokół (NFSv3 i SMB)** jako typ protokołu woluminu.   

    * Określ **ścieżkę woluminu** dla woluminu.   
    Ta ścieżka woluminu jest nazwą woluminu udostępnionego. Nazwa musi zaczynać się od znaku alfabetycznego i musi być unikatowa w obrębie każdej subskrypcji i każdego regionu.  

    * Określ styl **zabezpieczeń do użycia:** NTFS (ustawienie domyślne) lub UNIX.

    * Jeśli chcesz włączyć szyfrowanie protokołu SMB3 dla woluminu z podwójnym protokołem, wybierz pozycję Włącz szyfrowanie protokołu **SMB3.**   

        Ta funkcja umożliwia szyfrowanie tylko danych W locie SMB3. Nie szyfruje on danych w locie NFSv3. Klienci SMB, którzy nie korzystali z szyfrowania SMB3, nie będą mogli uzyskać dostępu do tego woluminu. Dane w spoczynku są szyfrowane niezależnie od tego ustawienia. Aby uzyskać dodatkowe informacje, zobacz Często zadawane pytania dotyczące szyfrowania [SMB.](azure-netapp-files-faqs.md#smb-encryption-faqs) 

        Funkcja **szyfrowania protokołu SMB3** jest obecnie dostępna w wersji zapoznawczej. Jeśli używasz tej funkcji po raz pierwszy, zarejestruj ją przed jej użyciem: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Sprawdź stan rejestracji funkcji: 

        > [!NOTE]
        > Stan **RegistrationState** może być w `Registering` stanie do 60 minut przed zmianą na `Registered` . Przed kontynuowaniem poczekaj, aż `Registered` stan będzie się zmienił.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Możesz również użyć poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) oraz zarejestrować funkcję i wyświetlić stan `az feature register` `az feature show` rejestracji.  

    * Opcjonalnie skonfiguruj [zasady eksportu dla woluminu](azure-netapp-files-configure-export-policy.md).

    ![Określanie podwójnego protokołu](../media/azure-netapp-files/create-volume-protocol-dual.png)

4. Kliknij **pozycję Przejrzyj i utwórz,** aby przejrzeć szczegóły woluminu. Następnie kliknij **przycisk Utwórz,** aby utworzyć wolumin.

    Utworzony wolumin zostanie wyświetlony na stronie Woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="allow-local-nfs-users-with-ldap-to-access-a-dual-protocol-volume"></a>Zezwalaj lokalnym użytkownikom systemu plików NFS z protokołem LDAP na dostęp do woluminu z podwójnym protokołem 

Można włączyć lokalnych użytkowników klienta NFS, którzy nie są obecni na serwerze LDAP systemu Windows, aby uzyskać dostęp do woluminu z dwoma protokołami z włączonym protokołem LDAP z rozszerzonymi grupami. W tym celu włącz opcję Zezwalaj użytkownikom **lokalnego systemu plików NFS na LDAP** w następujący sposób:

1. Kliknij **pozycję Połączenia usługi Active Directory.**  W istniejącym połączeniu z usługą Active Directory kliknij menu kontekstowe (trzy kropki), `…` a następnie wybierz pozycję **Edytuj**.  

2. W **wyświetlonym oknie Edytowanie** ustawień usługi Active Directory wybierz opcję Zezwalaj na lokalnych **użytkowników systemu plików NFS z LDAP.**  

    ![Zrzut ekranu przedstawiający opcję Zezwalaj użytkownikom lokalnego systemu plików NFS na LDAP](../media/azure-netapp-files/allow-local-nfs-users-with-ldap.png)  


## <a name="manage-ldap-posix-attributes"></a>Zarządzanie atrybutami LDAP POSIX

Atrybutami POSIX, takimi jak UID, Katalog macierzysty i inne wartości, można zarządzać za pomocą Użytkownicy i komputery usługi Active Directory MMC.  W poniższym przykładzie przedstawiono Edytor atrybutów usługi Active Directory:  

![Edytor atrybutów usługi Active Directory](../media/azure-netapp-files/active-directory-attribute-editor.png) 

Należy ustawić następujące atrybuty dla użytkowników LDAP i grup LDAP: 
* Wymagane atrybuty dla użytkowników protokołu LDAP:   
    `uid: Alice`, `uidNumber: 139`, `gidNumber: 555`, `objectClass: posixAccount`
* Wymagane atrybuty dla grup LDAP:   
    `objectClass: posixGroup`, `gidNumber: 555`

## <a name="configure-the-nfs-client"></a>Konfigurowanie klienta systemu plików NFS 

Postępuj zgodnie z [instrukcjami w konfigurowanie klienta systemu plików NFS Azure NetApp Files](configure-nfs-clients.md) skonfigurować klienta systemu plików NFS.  

## <a name="next-steps"></a>Następne kroki  

* [Konfigurowanie klienta sieciowego systemu plików dla usługi Azure NetApp Files](configure-nfs-clients.md)
* [Rozwiązywanie problemów z woluminami SMB lub dwoma protokołami](troubleshoot-dual-protocol-volumes.md)
* [Rozwiązywanie problemów z woluminem LDAP](troubleshoot-ldap-volumes.md)
