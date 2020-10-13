---
title: Tworzenie woluminu SMB dla Azure NetApp Files | Microsoft Docs
description: W tym artykule pokazano, jak utworzyć wolumin SMBv3 w Azure NetApp Files. Dowiedz się więcej o wymaganiach dotyczących Active Directory połączeń i usług domenowych.
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
ms.openlocfilehash: d0a16dc639fb3206b480c1091a66686955cbb11d
ms.sourcegitcommit: 50802bffd56155f3b01bfb4ed009b70045131750
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91932349"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Tworzenie woluminu SMB dla usługi Azure NetApp Files

Azure NetApp Files obsługuje tworzenie woluminów przy użyciu systemu plików NFS (NFSv3 i NFSv 4.1), SMBv3 lub Dual Protocol (NFSv3 i SMB). Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. W tym artykule pokazano, jak utworzyć wolumin SMBv3.

## <a name="before-you-begin"></a>Zanim rozpoczniesz 
Potrzebujesz skonfigurowanej puli pojemności.   
[Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md)   
Podsieć musi być delegowana do usługi Azure NetApp Files.  
[Delegowanie podsieci do usługi Azure NetApp Files](azure-netapp-files-delegate-subnet.md)

## <a name="requirements-for-active-directory-connections"></a>Wymagania dotyczące Active Directory połączeń

 Przed utworzeniem woluminu SMB należy utworzyć połączenia Active Directory. Wymagania dotyczące połączeń Active Directory są następujące: 

* Konto administratora, którego używasz, musi mieć możliwość tworzenia kont komputerów w określonej ścieżce jednostki organizacyjnej (OU).  

* Odpowiednie porty muszą być otwarte na odpowiednim serwerze Windows Active Directory (AD).  
    Wymagane porty są następujące: 

    |     Usługa           |     Port     |     Protokół     |
    |-----------------------|--------------|------------------|
    |    Usługi sieci Web AD    |    9389      |    TCP           |
    |    DNS                |    53        |    TCP           |
    |    DNS                |    53        |    UDP           |
    |    Ruch             |    Nie dotyczy       |    Odpowiedź echa    |
    |    Kerberos           |    464       |    TCP           |
    |    Kerberos           |    464       |    UDP           |
    |    Kerberos           |    88        |    TCP           |
    |    Kerberos           |    88        |    UDP           |
    |    LDAP               |    389       |    TCP           |
    |    LDAP               |    389       |    UDP           |
    |    LDAP               |    3268      |    TCP           |
    |    Nazwa NetBIOS       |    138       |    UDP           |
    |    SAM/LSA            |    445       |    TCP           |
    |    SAM/LSA            |    445       |    UDP           |
    |    W32Time            |    123       |    UDP           |

* Topologia lokacji dla Active Directory Domain Services przeznaczonych do celów musi być zgodna z wytycznymi, w szczególności z siecią wirtualną platformy Azure, w której wdrożono Azure NetApp Files.  

    Przestrzeń adresowa dla sieci wirtualnej, w której wdrożono Azure NetApp Files, musi zostać dodana do nowej lub istniejącej lokacji Active Directory (gdzie kontroler domeny osiągalny przez Azure NetApp Files to). 

* Określone serwery DNS muszą być dostępne z [delegowanej podsieci](./azure-netapp-files-delegate-subnet.md) Azure NetApp Files.  

    Zapoznaj się z tematem [wskazówki dotyczące planowania sieci Azure NetApp Files](./azure-netapp-files-network-topologies.md) obsługiwanych topologii sieci.

    Sieciowe grupy zabezpieczeń (sieciowych grup zabezpieczeń) i zapory muszą mieć odpowiednio skonfigurowane reguły zezwalające na żądania ruchu Active Directory i DNS. 

* Delegowana podsieć Azure NetApp Files musi mieć dostęp do wszystkich Active Directory Domain Services (dodaje) kontrolerów domeny w domenie, łącznie z lokalnymi i zdalnymi kontrolerami domeny. W przeciwnym razie może wystąpić przerwa w działaniu usługi.  

    Jeśli masz kontrolery domeny, które są nieosiągalne przez delegowaną Azure NetApp Filesą podsieć, możesz określić lokację Active Directory podczas tworzenia połączenia Active Directory.  Azure NetApp Files musi komunikować się tylko z kontrolerami domeny w lokacji, w której znajduje się Azure NetApp Files przestrzeń adresowa delegowanej podsieci.

    Zobacz [projektowanie topologii lokacji](/windows-server/identity/ad-ds/plan/designing-the-site-topology) dotyczącej witryn i usług AD. 
    
* Można włączyć szyfrowanie AES dla woluminu SMB, sprawdzając pole **szyfrowania AES** w oknie [przyłączanie Active Directory](#create-an-active-directory-connection) . Azure NetApp Files obsługuje typy szyfrowania DES, Kerberos AES 128 i Kerberos AES 256 (od najmniej bezpiecznych do najbezpieczniejszych). W przypadku włączenia szyfrowania AES poświadczenia użytkownika służące do przyłączania Active Directory muszą mieć włączoną opcję najwyższego odpowiadającego konta, która jest zgodna z możliwościami Active Directory.    

    Na przykład jeśli Active Directory ma tylko funkcję AES-128, należy włączyć opcję konta AES-128 dla poświadczeń użytkownika. Jeśli Active Directory ma funkcję AES-256, należy włączyć opcję konta AES-256 (która obsługuje również algorytm AES-128). Jeśli Active Directory nie ma żadnej możliwości szyfrowania Kerberos, Azure NetApp Files domyślnie używa algorytmu DES.  

    Opcje konta można włączyć we właściwościach programu Microsoft Management Console (MMC) Active Directory Użytkownicy i komputery:   

    ![Active Directory przystawki Użytkownicy i komputery](../media/azure-netapp-files/ad-users-computers-mmc.png)

* Azure NetApp Files obsługuje [podpisywanie LDAP](/troubleshoot/windows-server/identity/enable-ldap-signing-in-windows-server), co umożliwia bezpieczną transmisję ruchu LDAP między usługą Azure NetApp Files i kierowanymi [Active Directory kontrolerami domeny](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview). Jeśli masz wskazówki dotyczące usługi Microsoft Advisory [ADV190023](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023) na potrzeby podpisywania LDAP, należy włączyć funkcję podpisywania ldap w Azure NetApp Files, sprawdzając pole **podpisywania LDAP** w oknie [sprzężenie Active Directory](#create-an-active-directory-connection) . 

    Konfiguracja [powiązania kanału LDAP](https://support.microsoft.com/help/4034879/how-to-add-the-ldapenforcechannelbinding-registry-entry) nie ma wpływu na usługę Azure NetApp Files. 

Zobacz Azure NetApp Files usługi [SMB — często zadawane pytania](./azure-netapp-files-faqs.md#smb-faqs) dotyczące dodatkowych informacji o usłudze AD. 

## <a name="decide-which-domain-services-to-use"></a>Zdecyduj, które usługi domenowe mają być używane 

Azure NetApp Files obsługuje zarówno [Active Directory Domain Services](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) (dodaje), jak i Azure Active Directory Domain Services (AADDS) dla połączeń usługi AD.  Przed utworzeniem połączenia z usługą AD należy zdecydować, czy należy użyć Dodawanie lub AADDS.  

Aby uzyskać więcej informacji, zobacz [porównanie Active Directory Domain Services samozarządzanego, Azure Active Directory i zarządzanej Azure Active Directory Domain Services](../active-directory-domain-services/compare-identity-solutions.md). 

### <a name="active-directory-domain-services"></a>Active Directory Domain Services

Dla Azure NetApp Files można użyć preferowanego zakresu [Active Directory witryn i usług](/windows-server/identity/ad-ds/plan/understanding-active-directory-site-topology) . Ta opcja umożliwia odczyt i zapis do Active Directory Domain Services (dodaje) kontrolery domeny, które są [dostępne przez Azure NetApp Files](azure-netapp-files-network-topologies.md). Uniemożliwia to również komunikację usługi z kontrolerami domeny, które nie znajdują się w określonej lokacji Active Directory lokacji i usług. 

Aby znaleźć nazwę witryny podczas korzystania z dodawania, możesz skontaktować się z grupą administracyjną w organizacji, która jest odpowiedzialna za Active Directory Domain Services. W poniższym przykładzie pokazano wtyczka Lokacje i usługi Active Directory, w której wyświetlana jest nazwa lokacji: 

![Lokacje i usługi Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-sites-services.png)

Podczas konfigurowania połączenia z usługą AD dla Azure NetApp Files należy określić nazwę lokacji w polu zakres dla **nazwy witryny usługi AD** .

### <a name="azure-active-directory-domain-services"></a>Azure Active Directory Domain Services 

Aby uzyskać informacje na temat konfiguracji Azure Active Directory Domain Services (AADDS) i wskazówki, zobacz [dokumentację dotyczącą Azure AD Domain Services](../active-directory-domain-services/index.yml).

Dodatkowe zagadnienia dotyczące AADDS mają zastosowanie do Azure NetApp Files: 

* Upewnij się, że sieć wirtualna lub AADDS jest wdrożona w tym samym regionie platformy Azure co wdrożenie Azure NetApp Files.
* Jeśli używasz innej sieci wirtualnej w regionie, w którym wdrożono Azure NetApp Files, należy utworzyć komunikację równorzędną między tymi dwoma sieci wirtualnych.
* Azure NetApp Files obsługuje `user` i `resource forest` typy.
* Dla typu synchronizacji można wybrać `All` lub `Scoped` .   
    W przypadku wybrania opcji `Scoped` upewnij się, że wybrano odpowiednią grupę usługi Azure AD do uzyskiwania dostępu do udziałów SMB.  Jeśli nie masz pewności, możesz użyć `All` typu synchronizacji.
* Wymagane jest użycie jednostki SKU przedsiębiorstwa lub Premium. Standardowa jednostka SKU nie jest obsługiwana.

Podczas tworzenia połączenia Active Directory należy zwrócić uwagę na następujące informacje dotyczące AADDS:

* W menu AADDS można znaleźć informacje dotyczące **podstawowej DNS**, **dodatkowej DNS**i **nazwy domeny DNS usługi AD** .  
W przypadku serwerów DNS dwa adresy IP zostaną użyte do skonfigurowania połączenia Active Directory. 
* **Ścieżka jednostki organizacyjnej** to `OU=AADDC Computers` .  
To ustawienie jest konfigurowane w **Active Directory połączenia** w obszarze **konto NetApp**:

  ![Ścieżka jednostki organizacyjnej](../media/azure-netapp-files/azure-netapp-files-org-unit-path.png)

* Poświadczenia **nazwy użytkownika** mogą być dowolnymi użytkownikami należącymi do grupy **administratorów kontrolerów domen**usługi Azure AD.


## <a name="create-an-active-directory-connection"></a>Utwórz połączenie Active Directory

1. Na koncie usługi NetApp kliknij pozycję **połączenia Active Directory**a następnie kliknij pozycję **Dołącz**.  

    ![Połączenia Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections.png)

2. W oknie sprzęganie Active Directory podaj następujące informacje na podstawie usług domenowych, których chcesz użyć:  

    Aby uzyskać informacje specyficzne dla używanych usług domenowych, zobacz decydowanie o tym, [które usługi domenowe mają być używane](#decide-which-domain-services-to-use). 

    * **Podstawowy serwer DNS**  
        Jest to serwer DNS, który jest wymagany do przyłączania do domeny Active Directory i operacji uwierzytelniania SMB. 
    * **Pomocniczy serwer DNS**   
        Jest to pomocniczy serwer DNS służący do zapewniania nadmiarowych usług nazw. 
    * **Nazwa domeny DNS usługi AD**  
        To jest nazwa domeny Active Directory Domain Services, do której chcesz dołączyć.
    * **Nazwa witryny usługi AD**  
        Jest to nazwa lokacji, do której zostanie ograniczona funkcja odnajdowania kontrolera domeny.
    * **Prefiks serwera SMB (konto komputera)**  
        Jest to prefiks nazwy dla konta komputera w Active Directory, które Azure NetApp Files będzie używane do tworzenia nowych kont.

        Na przykład jeśli standard nazewnictwa używany przez organizację dla serwerów plików to NAS-01, NAS-02..., NAS-045, wpisz "NAS" dla prefiksu. 

        W razie konieczności usługa utworzy dodatkowe konta komputera w Active Directory.

        > [!IMPORTANT] 
        > Zmiana nazwy prefiksu serwera SMB po utworzeniu połączenia Active Directory jest zakłócona. Po zmianie nazwy prefiksu serwera SMB należy ponownie zainstalować istniejące udziały SMB.

    * **Ścieżka jednostki organizacyjnej**  
        Jest to ścieżka LDAP jednostki organizacyjnej (OU), w której zostaną utworzone konta komputerów serwera SMB. Oznacza to, OU = drugi poziom, OU = pierwszy poziom. 

        Jeśli używasz Azure NetApp Files z Azure Active Directory Domain Services, ścieżką jednostki organizacyjnej jest `OU=AADDC Computers` skonfigurowanie Active Directory dla konta NetApp.

    ![Przyłączanie Active Directory](../media/azure-netapp-files/azure-netapp-files-join-active-directory.png)

    * **Szyfrowanie AES**   
        Zaznacz to pole wyboru, aby włączyć szyfrowanie AES dla woluminu SMB. Wymagania można znaleźć w temacie [wymagania dotyczące Active Directory połączeń](#requirements-for-active-directory-connections) . 

        ![Active Directory szyfrowanie AES](../media/azure-netapp-files/active-directory-aes-encryption.png)

        Funkcja **szyfrowania AES** jest obecnie dostępna w wersji zapoznawczej. Jeśli korzystasz z tej funkcji po raz pierwszy, Zarejestruj tę funkcję przed jej użyciem: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```

        Sprawdź stan rejestracji funkcji: 

        > [!NOTE]
        > **RegistrationState** może być w stanie od `Registering` do 60 minut przed zmianą na `Registered` . Przed kontynuowaniem Zaczekaj na **zarejestrowanie** stanu.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFAesEncryption
        ```
        
        Możesz również użyć [poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` , `az feature show` Aby zarejestrować funkcję i wyświetlić stan rejestracji. 

    * **Podpisywanie LDAP**   
        Zaznacz to pole wyboru, aby włączyć podpisywanie LDAP. Ta funkcja Włącza bezpieczne wyszukiwania LDAP między usługą Azure NetApp Files i [Active Directory Domain Services kontrolerami domeny](/windows/win32/ad/active-directory-domain-services)określony przez użytkownika. Aby uzyskać więcej informacji, zobacz [ADV190023 | Wskazówki firmy Microsoft dotyczące włączania powiązań kanału LDAP i podpisywania LDAP](https://portal.msrc.microsoft.com/en-us/security-guidance/advisory/ADV190023).  

        ![Active Directory podpisywanie LDAP](../media/azure-netapp-files/active-directory-ldap-signing.png) 

        Funkcja **podpisywania LDAP** jest obecnie w wersji zapoznawczej. Jeśli korzystasz z tej funkcji po raz pierwszy, Zarejestruj tę funkcję przed jej użyciem: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```

        Sprawdź stan rejestracji funkcji: 

        > [!NOTE]
        > **RegistrationState** może być w stanie od `Registering` do 60 minut przed zmianą na `Registered` . Przed kontynuowaniem Zaczekaj na **zarejestrowanie** stanu.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFLdapSigning
        ```
        
        Możesz również użyć [poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` , `az feature show` Aby zarejestrować funkcję i wyświetlić stan rejestracji. 

     * **Użytkownicy zasad tworzenia kopii zapasowych**  
        Można uwzględnić dodatkowe konta, które wymagają podwyższonego poziomu uprawnień do konta komputera utworzonego do użytku z Azure NetApp Files. Określone konta będą mogły zmienić uprawnienia systemu plików NTFS na poziomie pliku lub folderu. Na przykład można określić konto usługi bez uprawnień używane do migrowania danych do udziału plików SMB w Azure NetApp Files.  

        ![Active Directory użytkowników zasad kopii zapasowych](../media/azure-netapp-files/active-directory-backup-policy-users.png)

        Funkcja **Użytkownicy zasad kopii zapasowych** jest obecnie w wersji zapoznawczej. Jeśli korzystasz z tej funkcji po raz pierwszy, Zarejestruj tę funkcję przed jej użyciem: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```

        Sprawdź stan rejestracji funkcji: 

        > [!NOTE]
        > **RegistrationState** może być w stanie od `Registering` do 60 minut przed zmianą na `Registered` . Przed kontynuowaniem Zaczekaj na **zarejestrowanie** stanu.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFBackupOperator
        ```
        
        Możesz również użyć [poleceń interfejsu wiersza polecenia platformy Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` , `az feature show` Aby zarejestrować funkcję i wyświetlić stan rejestracji. 

    * Poświadczenia, w tym **Nazwa użytkownika** i **hasło**

        ![Poświadczenia Active Directory](../media/azure-netapp-files/active-directory-credentials.png)

3. Kliknij pozycję **Dołącz**.  

    Zostanie wyświetlone utworzone połączenie Active Directory.

    ![Utworzono połączenia Active Directory](../media/azure-netapp-files/azure-netapp-files-active-directory-connections-created.png)

## <a name="add-an-smb-volume"></a>Dodawanie woluminu SMB

1. Kliknij blok **woluminy** w bloku pule pojemności. 

    ![Przejdź do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Zostanie wyświetlone okno Tworzenie woluminu.

3. W oknie Tworzenie woluminu kliknij pozycję **Utwórz** i podaj informacje dla następujących pól na karcie podstawowe:   
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

        Określona Sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files. Dostęp do usługi Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej, która znajduje się w tym samym regionie co wolumin za pośrednictwem sieci równorzędnej. Możesz również uzyskać dostęp do woluminu z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie została delegowana, można kliknąć pozycję **Utwórz nową** na stronie Tworzenie woluminu. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Jeśli chcesz zastosować istniejące zasady migawek do woluminu, kliknij przycisk **Pokaż sekcję zaawansowaną** , aby je rozwinąć, określ, czy chcesz ukryć ścieżkę migawki, a następnie wybierz pozycję Zasady migawek w menu rozwijanym. 

        Aby uzyskać informacje na temat tworzenia zasad migawek, zobacz [Zarządzanie zasadami migawek](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies).

        ![Pokaż zaznaczenie zaawansowane](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Kliknij pozycję **Protokół** i wykonaj następujące informacje:  
    * Wybierz opcję **SMB** jako typ protokołu dla woluminu. 
    * Wybierz połączenie **Active Directory** z listy rozwijanej.
    * Określ nazwę udostępnionego woluminu w polu  **Nazwa udziału**.

    ![Określ protokół SMB](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknij przycisk **Przegląd + Utwórz** , aby przejrzeć szczegóły woluminu.  Następnie kliknij przycisk **Utwórz** , aby utworzyć wolumin SMB.

    Utworzony wolumin zostanie wyświetlony na stronie woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="control-access-to-an-smb-volume"></a>Kontrola dostępu do woluminu SMB  

Dostęp do woluminu SMB jest zarządzany przez uprawnienia.  

### <a name="share-permissions"></a>Uprawnienia do udostępniania  

Domyślnie nowy wolumin ma uprawnienia **Wszyscy/Pełna kontrola** . Członkowie grupy Administratorzy domeny mogą zmieniać uprawnienia udziału za pomocą przystawki Zarządzanie komputerem na koncie komputera, które jest używane dla woluminu Azure NetApp Files.

![Ścieżka instalacji SMB ](../media/azure-netapp-files/smb-mount-path.png) 
 ![ Ustawianie uprawnień udziału](../media/azure-netapp-files/set-share-permissions.png) 

### <a name="ntfs-file-and-folder-permissions"></a>Uprawnienia plików i folderów systemu plików NTFS  

Uprawnienia do pliku lub folderu można ustawić przy użyciu karty **zabezpieczenia** we właściwościach obiektu w kliencie SMB systemu Windows.
 
![Ustawianie uprawnień do plików i folderów](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Następne kroki  

* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Protokół SMB — często zadawane pytania](./azure-netapp-files-faqs.md#smb-faqs)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Instalowanie nowego lasu Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)