---
title: Tworzenie woluminu SMB dla Azure NetApp Files | Microsoft Docs
description: W tym artykule pokazano, jak utworzyć wolumin protokołu SMB3 w Azure NetApp Files. Dowiedz się więcej o wymaganiach dotyczących Active Directory połączeń i usług domenowych.
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
ms.date: 03/19/2021
ms.author: b-juche
ms.openlocfilehash: c673f7a9556193fb05e05ea372bfccd17cd3c5ed
ms.sourcegitcommit: 42e4f986ccd4090581a059969b74c461b70bcac0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104868515"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Tworzenie woluminu SMB dla usługi Azure NetApp Files

Azure NetApp Files obsługuje tworzenie woluminów przy użyciu systemu plików NFS (NFSv3 i NFSv 4.1), protokołu SMB3 lub Dual Protocol (NFSv3 i SMB). Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. W tym artykule pokazano, jak utworzyć wolumin protokołu SMB3.

## <a name="before-you-begin"></a>Zanim rozpoczniesz 

* Potrzebujesz skonfigurowanej puli pojemności. Zobacz [Konfigurowanie puli pojemności](azure-netapp-files-set-up-capacity-pool.md).     
* Podsieć musi być delegowana do usługi Azure NetApp Files. Zobacz [delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Konfigurowanie połączeń Active Directory 

Przed utworzeniem woluminu SMB należy utworzyć połączenie Active Directory. Jeśli nie skonfigurowano Active Directory połączeń dla plików NetApp platformy Azure, wykonaj instrukcje opisane w temacie [Tworzenie i zarządzanie połączeniami Active Directory](create-active-directory-connections.md).

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
    * Jeśli chcesz włączyć ciągłą dostępność dla woluminu SMB, wybierz opcję **Włącz ciągłą dostępność**.    

        > [!IMPORTANT]   
        > Funkcja ciągłej dostępności protokołu SMB jest obecnie dostępna w publicznej wersji zapoznawczej. Musisz przesłać żądanie waitlist, aby uzyskać dostęp do funkcji za pomocą **[strony przesyłania ciągłej publicznej wersji zapoznawczej usługi SMB w Azure NetApp Files](https://aka.ms/anfsmbcasharespreviewsignup)**. Przed skorzystaniem z funkcji ciągłej dostępności poczekaj na oficjalną wiadomość e-mail z potwierdzeniem z zespołu Azure NetApp Files.   
        > 
        > Ciągłej dostępności należy włączyć tylko dla obciążeń SQL. Używanie udziałów ciągłej dostępności protokołu SMB dla obciążeń innych niż SQL Server *nie* jest obsługiwane. Ta funkcja jest obecnie obsługiwana w systemie Windows SQL Server. SQL Server systemu Linux nie jest obecnie obsługiwana. Jeśli do zainstalowania SQL Server używasz konta innego niż administrator, upewnij się, że konto ma przypisane wymagane uprawnienia zabezpieczeń. Jeśli konto domeny nie ma wymaganego uprawnienia zabezpieczeń ( `SeSecurityPrivilege` ) i nie można ustawić uprawnienia na poziomie domeny, można udzielić uprawnienia do konta za pomocą pola **Użytkownicy uprawnień zabezpieczeń** dla połączeń Active Directory. Zobacz [Tworzenie połączenia Active Directory](create-active-directory-connections.md#create-an-active-directory-connection).

    <!-- [1/13/21] Commenting out command-based steps below, because the plan is to use form-based (URL) registration, similar to CRR feature registration -->
    <!-- 
        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```

        Check the status of the feature registration: 

        > [!NOTE]
        > The **RegistrationState** may be in the `Registering` state for up to 60 minutes before changing to`Registered`. Wait until the status is `Registered` before continuing.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBCAShare
        ```
        
        You can also use [Azure CLI commands](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) `az feature register` and `az feature show` to register the feature and display the registration status. 
    --> 

    ![Zrzut ekranu, który opisuje kartę Protokół tworzenia woluminu SMB.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

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
* [Rozwiązywanie problemów z woluminami SMB lub Dual-Protocol](troubleshoot-dual-protocol-volumes.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Instalowanie nowego lasu Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
