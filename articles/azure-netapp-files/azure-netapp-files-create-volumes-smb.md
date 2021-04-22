---
title: Tworzenie woluminu SMB dla Azure NetApp Files | Microsoft Docs
description: W tym artykule pokazano, jak utworzyć wolumin SMB3 w Azure NetApp Files. Dowiedz się więcej o wymaganiach dotyczących połączeń w usłudze Active Directory i usług domenowych.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: d3ca94524c334a20f5ee75e5300ad419fa1542c5
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2021
ms.locfileid: "107873275"
---
# <a name="create-an-smb-volume-for-azure-netapp-files"></a>Tworzenie woluminu SMB dla usługi Azure NetApp Files

Azure NetApp Files obsługuje tworzenie woluminów przy użyciu systemu plików NFS (NFSv3 i NFSv4.1), protokołu SMB3 lub podwójnego protokołu (NFSv3 i SMB). Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. 

W tym artykule pokazano, jak utworzyć wolumin SMB3. W przypadku woluminów NFS zobacz [Tworzenie woluminu NFS](azure-netapp-files-create-volumes.md). W przypadku woluminów z dwoma protokołami zobacz Create a dual-protocol volume (Tworzenie [woluminu dwu protokołów).](create-volumes-dual-protocol.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz 

* Potrzebujesz skonfigurowanej puli pojemności. Zobacz [Konfigurowanie puli pojemności.](azure-netapp-files-set-up-capacity-pool.md)     
* Podsieć musi być delegowana do usługi Azure NetApp Files. Zobacz [Delegowanie podsieci do Azure NetApp Files](azure-netapp-files-delegate-subnet.md).

## <a name="configure-active-directory-connections"></a>Konfigurowanie połączeń usługi Active Directory 

Przed utworzeniem woluminu SMB należy utworzyć połączenie usługi Active Directory. Jeśli nie skonfigurowano połączeń usługi Active Directory dla plików usługi Azure NetApp, postępuj zgodnie z instrukcjami opisanymi w te tematach Create and manage Active Directory connections (Tworzenie połączeń usługi Active Directory i zarządzanie [nimi).](create-active-directory-connections.md)

## <a name="add-an-smb-volume"></a>Dodawanie woluminu SMB

1. Kliknij blok **Woluminy** w bloku Pule pojemności. 

    ![Przechodzenie do woluminów](../media/azure-netapp-files/azure-netapp-files-navigate-to-volumes.png)

2. Kliknij pozycję **+ Dodaj wolumin**, aby utworzyć wolumin.  
    Zostanie wyświetlone okno Tworzenie woluminu.

3. W oknie Tworzenie woluminu kliknij przycisk **Utwórz** i podaj informacje dla następujących pól na karcie Podstawy:   
    * **Nazwa woluminu**      
        Określ nazwę tworzonego woluminu.   

        Nazwa woluminu musi być unikatowa w obrębie każdej puli pojemności. Musi zawierać co najmniej trzy znaki. Można użyć dowolnych znaków alfanumerycznych.   

        Nie można użyć ani `default` `bin` jako nazwy woluminu.

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

        Określana sieć wirtualna musi mieć podsieć delegowaną do Azure NetApp Files. Dostęp Azure NetApp Files można uzyskać tylko z tej samej sieci wirtualnej lub z sieci wirtualnej, która znajduje się w tym samym regionie co wolumin, za pośrednictwem komunikacji równorzędnej sieci wirtualnych. Dostęp do woluminu można również uzyskać z sieci lokalnej za pośrednictwem usługi Express Route.   

    * **Podsieć**  
        Określ podsieć, której chcesz użyć na potrzeby woluminu.  
        Określana podsieć musi być delegowana do usługi Azure NetApp Files. 
        
        Jeśli podsieć nie jest delegowana, możesz kliknąć pozycję **Utwórz** nową na stronie Tworzenie woluminu. Następnie na stronie Utwórz podsieć określ informacje o podsieci i wybierz pozycję **Microsoft.NetApp/woluminy**, aby delegować podsieć dla usługi Azure NetApp Files. W każdej sieci wirtualnej można delegować tylko jedną podsieć do Azure NetApp Files.   
 
        ![Tworzenie woluminu](../media/azure-netapp-files/azure-netapp-files-new-volume.png)
    
        ![Tworzenie podsieci](../media/azure-netapp-files/azure-netapp-files-create-subnet.png)

    * Jeśli chcesz zastosować istniejące zasady migawek  do woluminu, kliknij pozycję Pokaż sekcję Zaawansowane, aby ją rozwinąć, określ, czy chcesz ukryć ścieżkę migawki, a następnie wybierz zasady migawek w menu rozwijanych. 

        Aby uzyskać informacje na temat tworzenia zasad migawek, zobacz [Zarządzanie zasadami migawek.](azure-netapp-files-manage-snapshots.md#manage-snapshot-policies)

        ![Pokaż wybór zaawansowany](../media/azure-netapp-files/volume-create-advanced-selection.png)

4. Kliknij **pozycję Protokół** i wypełnij następujące informacje:  
    * Wybierz **SMB** jako typ protokołu dla woluminu. 
    * Wybierz połączenie **z usługą Active Directory** z listy rozwijanej.
    * Określ nazwę woluminu udostępnionego w nazwa  **udziału**.
    * Jeśli chcesz włączyć szyfrowanie dla protokołu SMB3, wybierz pozycję Włącz szyfrowanie protokołu **SMB3.**   
        Ta funkcja umożliwia szyfrowanie danych SMB3 w locie. Klienci SMB, którzy nie korzystali z szyfrowania SMB3, nie będą mogli uzyskać dostępu do tego woluminu.  Dane przechowywane są szyfrowane niezależnie od tego ustawienia.  
        Aby uzyskać dodatkowe informacje, zobacz Często zadawane pytania dotyczące szyfrowania [SMB.](azure-netapp-files-faqs.md#smb-encryption-faqs) 

        Funkcja **szyfrowania protokołu SMB3 jest** obecnie dostępna w wersji zapoznawczej. Jeśli używasz tej funkcji po raz pierwszy, zarejestruj ją przed jej użyciem: 

        ```azurepowershell-interactive
        Register-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```

        Sprawdź stan rejestracji funkcji: 

        > [!NOTE]
        > **RegistrationState** może być w stanie do `Registering` 60 minut przed zmianą na `Registered` . Przed kontynuowaniem poczekaj, aż `Registered` stan będzie się zmienił.

        ```azurepowershell-interactive
        Get-AzProviderFeature -ProviderNamespace Microsoft.NetApp -FeatureName ANFSMBEncryption
        ```
        
        Możesz również użyć poleceń interfejsu [wiersza polecenia platformy Azure](/cli/azure/feature?preserve-view=true&view=azure-cli-latest) oraz zarejestrować funkcję i wyświetlić stan `az feature register` `az feature show` rejestracji.  
    * Jeśli chcesz włączyć ciągłą dostępność dla woluminu SMB, wybierz **pozycję Włącz ciągłą dostępność.**    

        > [!IMPORTANT]   
        > Funkcja ciągłej dostępności SMB jest obecnie dostępna w publicznej wersji zapoznawczej. Musisz przesłać żądanie listy oczekiwania w celu uzyskania dostępu do funkcji za pośrednictwem strony przesyłania listy oczekiwania udziałów ciągłej dostępności Azure NetApp Files SMB w **[publicznej wersji zapoznawczej.](https://aka.ms/anfsmbcasharespreviewsignup)** Przed rozpoczęciem korzystania z funkcji ciągłej dostępności poczekaj na oficjalny Azure NetApp Files e-mail od zespołu.   
        > 
        > Ciągłą dostępność należy włączyć tylko dla kontenerów profilu SQL Server [i FSLogix.](../virtual-desktop/create-fslogix-profile-container.md) Używanie udziałów ciągłej dostępności SMB dla obciążeń innych niż SQL Server i FSLogix nie *jest obsługiwane.* Ta funkcja jest obecnie obsługiwana w systemie Windows SQL Server. System Linux SQL Server nie jest obecnie obsługiwany. Jeśli używasz konta bez uprawnień administratora (domeny) do instalowania SQL Server, upewnij się, że konto ma przypisane wymagane uprawnienia zabezpieczeń. Jeśli konto domeny nie ma wymaganych uprawnień zabezpieczeń ( ), a uprawnienia nie można ustawić na poziomie domeny, możesz udzielić tego uprawnienia kontu przy użyciu pola Użytkownicy uprawnień zabezpieczeń połączeń usługi `SeSecurityPrivilege` Active Directory.  Zobacz [Tworzenie połączenia usługi Active Directory.](create-active-directory-connections.md#create-an-active-directory-connection)

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

    ![Zrzut ekranu przedstawiający kartę Protokół tworzenia woluminu SMB.](../media/azure-netapp-files/azure-netapp-files-protocol-smb.png)

5. Kliknij **pozycję Przejrzyj i utwórz,** aby przejrzeć szczegóły woluminu.  Następnie kliknij **przycisk Utwórz,** aby utworzyć wolumin SMB.

    Utworzony wolumin zostanie wyświetlony na stronie Woluminy. 
 
    Wolumin dziedziczy atrybuty Subskrypcja, Grupa zasobów i Lokalizacja z puli pojemności. Stan wdrożenia woluminu możesz monitorować na karcie Powiadomienia.

## <a name="control-access-to-an-smb-volume"></a>Kontrolowanie dostępu do woluminu SMB  

Dostępem do woluminu SMB zarządza się za pomocą uprawnień.  

### <a name="share-permissions"></a>Uprawnienia do udostępniania  

Domyślnie nowy wolumin ma uprawnienia udziału **Wszyscy/Pełna** kontrola. Członkowie grupy Administratorzy domeny mogą zmieniać uprawnienia udziału w następujący sposób:  

1. Zamapuj udział na dysk.  
2. Kliknij prawym przyciskiem myszy dysk, wybierz **pozycję Właściwości,** a następnie przejdź do **karty** Zabezpieczenia.

[![Ustawianie uprawnień udziału](../media/azure-netapp-files/set-share-permissions.png)](../media/azure-netapp-files/set-share-permissions.png#lightbox)

### <a name="ntfs-file-and-folder-permissions"></a>Uprawnienia plików i folderów NTFS  

Uprawnienia do pliku lub folderu można  ustawić przy użyciu karty Zabezpieczenia właściwości obiektu w kliencie SMB systemu Windows.
 
![Ustawianie uprawnień do plików i folderów](../media/azure-netapp-files/set-file-folder-permissions.png) 

## <a name="next-steps"></a>Następne kroki  

* [Instalowanie lub odinstalowywanie woluminu dla maszyn wirtualnych z systemem Windows lub Linux](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md)
* [Limity zasobów dla usługi Azure NetApp Files](azure-netapp-files-resource-limits.md)
* [Protokół SMB — często zadawane pytania](./azure-netapp-files-faqs.md#smb-faqs)
* [Rozwiązywanie problemów z woluminami SMB lub dwoma protokołami](troubleshoot-dual-protocol-volumes.md)
* [Informacje o integracji z siecią wirtualną dla usług platformy Azure](../virtual-network/virtual-network-for-azure-services.md)
* [Instalowanie nowego lasu usługi Active Directory przy użyciu interfejsu wiersza polecenia platformy Azure](/windows-server/identity/ad-ds/deploy/virtual-dc/adds-on-azure-vm)
