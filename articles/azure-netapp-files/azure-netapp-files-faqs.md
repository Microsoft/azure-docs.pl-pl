---
title: Często zadawane pytania dotyczące Azure NetApp Files | Microsoft Docs
description: Zapoznaj się z często zadawanymi pytaniami Azure NetApp Files, takimi jak sieć, zabezpieczenia, wydajność, zarządzanie pojemnością i migracja/ochrona danych.
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
ms.date: 04/20/2021
ms.author: b-juche
ms.openlocfilehash: 6cef4860184b217e96e8967ab24a3befc632e316
ms.sourcegitcommit: 260a2541e5e0e7327a445e1ee1be3ad20122b37e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2021
ms.locfileid: "107811854"
---
# <a name="faqs-about-azure-netapp-files"></a>Często zadawane pytania dotyczące Azure NetApp Files

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące Azure NetApp Files. 

## <a name="networking-faqs"></a>Często zadawane pytania o sieć

### <a name="does-the-data-path-for-nfs-or-smb-go-over-the-internet"></a>Czy ścieżka danych dla systemu plików NFS lub SMB jest przechodnia przez Internet?  

Nie. Ścieżka danych dla systemu plików NFS lub SMB nie jest przechodnia przez Internet. Azure NetApp Files to usługa natywna platformy Azure wdrażana w sieci Virtual Network Azure, w której usługa jest dostępna. Azure NetApp Files używa podsieci delegowane i a inowiązyuje interfejs sieciowy bezpośrednio w sieci wirtualnej. 

Aby [uzyskać szczegółowe informacje, Azure NetApp Files wskazówki dotyczące planowania](./azure-netapp-files-network-topologies.md) sieci.  

### <a name="can-i-connect-a-vnet-that-i-already-created-to-the-azure-netapp-files-service"></a>Czy mogę połączyć sieć wirtualną, która została już utworzona, z Azure NetApp Files wirtualną?

Tak, możesz połączyć utworzone sieci wirtualne z usługą. 

Aby [uzyskać szczegółowe informacje, Azure NetApp Files wskazówki dotyczące planowania](./azure-netapp-files-network-topologies.md) sieci.  

### <a name="can-i-mount-an-nfs-volume-of-azure-netapp-files-using-dns-fqdn-name"></a>Czy można zainstalować wolumin NFS systemu plików Azure NetApp Files nazwę FQDN DNS?

Tak, można utworzyć wymagane wpisy DNS. Azure NetApp Files dostarcza adres IP usługi dla aprowizowanych woluminów. 

> [!NOTE] 
> Azure NetApp Files wdrożyć dodatkowe ip dla usługi zgodnie z potrzebami.  Może być konieczne okresowe zaktualizowanie wpisów DNS.

### <a name="can-i-set-or-select-my-own-ip-address-for-an-azure-netapp-files-volume"></a>Czy mogę ustawić lub wybrać własny adres IP dla Azure NetApp Files woluminu?  

Nie. Przypisywanie adresów IP Azure NetApp Files woluminów jest dynamiczne. Statyczne przypisywanie adresów IP nie jest obsługiwane. 

### <a name="does-azure-netapp-files-support-dual-stack-ipv4-and-ipv6-vnet"></a>Czy Azure NetApp Files sieci wirtualne z podwójnym stosem (IPv4 i IPv6)?

Nie, Azure NetApp Files obecnie nie obsługuje sieci wirtualnej podwójnego stosu (IPv4 i IPv6).  
 
## <a name="security-faqs"></a>Często zadawane pytania dotyczące zabezpieczeń

### <a name="can-the-network-traffic-between-the-azure-vm-and-the-storage-be-encrypted"></a>Czy ruch sieciowy między maszyną wirtualną platformy Azure i magazynem może być szyfrowany?

Ruch danych między klientami NFSv4.1 i woluminami Azure NetApp Files można zaszyfrować przy użyciu protokołu Kerberos z szyfrowaniem AES-256. Aby uzyskać szczegółowe informacje, zobacz Configure [NFSv4.1 Kerberos encryption for Azure NetApp Files (Konfigurowanie szyfrowania Kerberos przy Azure NetApp Files NFSv4.1).](configure-kerberos-encryption.md)   

Ruch danych między klientami NFSv3 lub SMB3 do woluminów Azure NetApp Files nie jest szyfrowany. Jednak ruch z maszyny wirtualnej platformy Azure (z uruchomionym systemem plików NFS lub klientem SMB) do usługi Azure NetApp Files jest tak bezpieczny, jak każdy inny ruch z maszyny wirtualnej platformy Azure do maszyny wirtualnej. Ten ruch jest lokalny dla sieci centrum danych platformy Azure. 

### <a name="can-the-storage-be-encrypted-at-rest"></a>Czy magazyn może być zaszyfrowany w spoczynku?

Wszystkie Azure NetApp Files są szyfrowane przy użyciu standardu FIPS 140-2. Wszystkie klucze są zarządzane przez Azure NetApp Files usługi. 

### <a name="how-are-encryption-keys-managed"></a>W jaki sposób zarządzane są klucze szyfrowania? 

Zarządzanie kluczami Azure NetApp Files jest obsługiwane przez usługę. Dla każdego woluminu jest generowany unikatowy klucz szyfrowania danych XTS-AES-256. Hierarchia kluczy szyfrowania służy do szyfrowania i ochrony wszystkich kluczy woluminów. Te klucze szyfrowania nigdy nie są wyświetlane ani raportowane w formacie niezaszyfrowanym. Klucze szyfrowania są usuwane natychmiast po usunięciu woluminu.

Obsługa kluczy zarządzanych przez klienta (Bring Your Own Key) przy użyciu usługi Azure Dedicated HSM jest dostępna w sposób kontrolowany w regionach Wschodnie stany USA, Południowo-środkowe stany USA, Zachodnie stany USA 2 US Gov Wirginia regionach. Możesz zażądać dostępu na stronie [anffeedback@microsoft.com](mailto:anffeedback@microsoft.com) . Gdy pojemność stanie się dostępna, żądania zostaną zatwierdzone.

### <a name="can-i-configure-the-nfs-export-policy-rules-to-control-access-to-the-azure-netapp-files-service-mount-target"></a>Czy można skonfigurować reguły zasad eksportu systemu plików NFS w celu kontrolowania dostępu do Azure NetApp Files docelowego instalacji usługi?

Tak, można skonfigurować maksymalnie pięć reguł w ramach jednej zasady eksportu systemu plików NFS.

### <a name="does-azure-netapp-files-support-network-security-groups"></a>Czy Azure NetApp Files sieciowe grupy zabezpieczeń?

Nie. Obecnie nie można zastosować sieciowych grup zabezpieczeń do delegowanej podsieci Azure NetApp Files lub interfejsów sieciowych utworzonych przez usługę.

### <a name="can-i-use-azure-rbac-with-azure-netapp-files"></a>Czy mogę używać kontroli RBAC platformy Azure z Azure NetApp Files?

Tak, Azure NetApp Files obsługuje funkcje RBAC platformy Azure. Oprócz wbudowanych ról platformy Azure można tworzyć role [niestandardowe dla](../role-based-access-control/custom-roles.md) Azure NetApp Files. 

Aby uzyskać pełną listę uprawnień Azure NetApp Files, zobacz Operacje dostawcy zasobów platformy Azure dla [`Microsoft.NetApp`](../role-based-access-control/resource-provider-operations.md#microsoftnetapp) usługi .

### <a name="are-azure-activity-logs-supported-on-azure-netapp-files"></a>Czy dzienniki aktywności platformy Azure są obsługiwane Azure NetApp Files?

Azure NetApp Files to usługa natywna platformy Azure. Wszystkie interfejsy API PUT, POST i DELETE Azure NetApp Files są rejestrowane. Na przykład dzienniki pokazują działania, takie jak to, kto utworzył migawkę, kto zmodyfikował wolumin i tak dalej.

Aby uzyskać pełną listę operacji interfejsu API, zobacz [Azure NetApp Files API REST](/rest/api/netapp/).

### <a name="can-i-use-azure-policies-with-azure-netapp-files"></a>Czy można używać zasad platformy Azure z Azure NetApp Files?

Tak, można tworzyć [niestandardowe zasady platformy Azure.](../governance/policy/tutorials/create-custom-policy-definition.md) 

Nie można jednak tworzyć zasad platformy Azure (niestandardowych zasad nazewnictwa) w Azure NetApp Files interfejsie. Zobacz [Wskazówki dotyczące planowania Azure NetApp Files sieci.](azure-netapp-files-network-topologies.md#considerations)

## <a name="performance-faqs"></a>Często zadawane pytania dotyczące wydajności

### <a name="what-should-i-do-to-optimize-or-tune-azure-netapp-files-performance"></a>Co należy zrobić, aby zoptymalizować lub dostroić Azure NetApp Files wydajności?

Zgodnie z wymaganiami w zakresie wydajności można podjąć następujące działania: 
- Upewnij się, że maszyna wirtualna ma odpowiedni rozmiar.
- Włącz przyspieszoną sieć dla maszyny wirtualnej.
- Wybierz żądany poziom i rozmiar usługi dla puli pojemności.
- Utwórz wolumin o żądanym rozmiarze przydziału dla pojemności i wydajności.

### <a name="how-do-i-convert-throughput-based-service-levels-of-azure-netapp-files-to-iops"></a>Jak mogę konwertowanie poziomów usług opartych na przepływności dla Azure NetApp Files na IOPS?

Możesz przekonwertować MB/s na IOPS przy użyciu następującej formuły:  

`IOPS = (MBps Throughput / KB per IO) * 1024`

### <a name="how-do-i-change-the-service-level-of-a-volume"></a>Jak mogę zmienić poziom usługi woluminu?

Poziom usług istniejącego woluminu można zmienić, przenosząc go do [](azure-netapp-files-service-levels.md) innej puli pojemności, która używa poziomu usług dla woluminu. Zobacz [Dynamiczne zmienianie poziomu usługi woluminu](dynamic-change-volume-service-level.md). 

### <a name="how-do-i-monitor-azure-netapp-files-performance"></a>Jak mogę monitorowanie Azure NetApp Files wydajności?

Azure NetApp Files metryki wydajności woluminu. Za pomocą usługi Azure Monitor można również monitorować metryki użycia dla Azure NetApp Files.  Listę [metryk wydajności](azure-netapp-files-metrics.md) dla Azure NetApp Files można znaleźć w tece Metryki Azure NetApp Files.

### <a name="whats-the-performance-impact-of-kerberos-on-nfsv41"></a>Jaki jest wpływ protokołu Kerberos na wydajność systemu plików NFSv4.1?

Zobacz Wpływ protokołu Kerberos na wydajność na woluminach [NFSv4.1,](performance-impact-kerberos.md) aby uzyskać informacje o opcjach zabezpieczeń dla systemu plików NFSv4.1, przetestowanych wektorach wydajności i oczekiwanym wpływie na wydajność. 

## <a name="nfs-faqs"></a>Często zadawane pytania dotyczące systemu NFS

### <a name="i-want-to-have-a-volume-mounted-automatically-when-an-azure-vm-is-started-or-rebooted--how-do-i-configure-my-host-for-persistent-nfs-volumes"></a>Chcę mieć wolumin zainstalowany automatycznie po uruchomieniu lub ponownym uruchomieniu maszyny wirtualnej platformy Azure.  Jak mogę skonfigurować hosta dla trwałych woluminów NFS?

Aby wolumin NFS automatycznie zainstalować podczas uruchamiania lub ponownego uruchamiania maszyny wirtualnej, dodaj wpis `/etc/fstab` do pliku na hoście. 

Aby [uzyskać szczegółowe informacje, zobacz Mount or unmount a volume for Windows or Linux virtual machines](azure-netapp-files-mount-unmount-volumes-for-virtual-machines.md) (Zainstaluj lub odinstaluj wolumin dla maszyn wirtualnych z systemem Windows lub Linux).  

### <a name="why-does-the-df-command-on-nfs-client-not-show-the-provisioned-volume-size"></a>Dlaczego polecenie DF na kliencie systemu plików NFS nie wyświetla rozmiaru aprowizowanych woluminów?

Rozmiar woluminu zgłoszony w DF jest maksymalnym rozmiarem, do Azure NetApp Files wolumin może rosnąć. Rozmiar woluminu Azure NetApp Files w poleceniu DF nie odzwierciedla limitu przydziału ani rozmiaru woluminu.  Rozmiar woluminu Azure NetApp Files przydział można uzyskać za pośrednictwem Azure Portal lub interfejsu API.

### <a name="what-nfs-version-does-azure-netapp-files-support"></a>Jaka wersja systemu plików NFS Azure NetApp Files obsługuje?

Azure NetApp Files obsługuje NFSv3 i NFSv4.1. Wolumin można [utworzyć przy użyciu](azure-netapp-files-create-volumes.md) jednej z wersji systemu plików NFS. 

### <a name="how-do-i-enable-root-squashing"></a>Jak mogę włączyć zwęgienie katalogu głównego?

Możesz określić, czy konto główne może uzyskać dostęp do woluminu, korzystając z zasad eksportu woluminu. Aby uzyskać szczegółowe informacje, zobacz Konfigurowanie zasad [eksportu dla woluminu NFS.](azure-netapp-files-configure-export-policy.md)

### <a name="can-i-use-the-same-file-path-volume-creation-token-for-multiple-volumes"></a>Czy mogę użyć tej samej ścieżki pliku (tokenu tworzenia woluminu) dla wielu woluminów?

Tak, możesz. Jednak ścieżka pliku musi być używana w innej subskrypcji lub innym regionie.   

Na przykład tworzysz wolumin o nazwie `vol1` . Następnie utworzysz kolejny wolumin o nazwie również `vol1` w innej puli pojemności, ale w tej samej subskrypcji i regionie. W takim przypadku użycie tej samej nazwy `vol1` woluminu spowoduje błąd. Aby użyć tej samej ścieżki pliku, nazwa musi znajdować się w innym regionie lub subskrypcji.

### <a name="when-i-try-to-access-nfs-volumes-through-a-windows-client-why-does-the-client-take-a-long-time-to-search-folders-and-subfolders"></a>Dlaczego podczas próby uzyskania dostępu do woluminów NFS za pośrednictwem klienta systemu Windows wyszukiwanie folderów i podfolderów przez klienta trwa długo?

Upewnij się, że na kliencie systemu Windows włączono funkcję przyspieszania wyszukiwania folderów `CaseSensitiveLookup` i podfolderów:

1. Użyj następującego polecenia programu PowerShell, aby włączyć funkcję CaseSensitiveLookup:   
    `Set-NfsClientConfiguration -CaseSensitiveLookup 1`    
2. Zainstaluj wolumin na serwerze z systemem Windows.   
    Przykład:   
    `Mount -o rsize=1024 -o wsize=1024 -o mtype=hard \\10.x.x.x\testvol X:*`

### <a name="how-does-azure-netapp-files-support-nfsv41-file-locking"></a>Jak Azure NetApp Files blokowanie plików NFSv4.1? 

W przypadku klientów NFSv4.1 program Azure NetApp Files obsługuje mechanizm blokowania plików NFSv4.1, który zachowuje stan wszystkich blokad plików w modelu opartym na dzierżawie. 

Na RFC 3530 Azure NetApp Files jeden okres dzierżawy dla całego stanu przechowywanego przez klienta systemu plików NFS. Jeśli klient nie odnowi dzierżawy w zdefiniowanym okresie, wszystkie stany skojarzone z dzierżawą klienta zostaną zwolnione przez serwer.  

Na przykład jeśli klient instalowanie woluminu przestaje odpowiadać lub ulega awarii poza limitami czasu, blokady zostaną zwolnione. Klient może odnowić dzierżawę jawnie lub niejawnie, wykonując operacje takie jak odczytywanie pliku.   

Okres prolongaty definiuje okres specjalnego przetwarzania, w którym klienci mogą próbować odzyskać stan blokowania podczas odzyskiwania serwera. Domyślny limit czasu dla dzierżaw to 30 sekund z okresem prolongaty 45 sekund. Po tym czasie dzierżawa klienta zostanie zwolniona.   

## <a name="smb-faqs"></a>Protokół SMB — często zadawane pytania

### <a name="which-smb-versions-are-supported-by-azure-netapp-files"></a>Które wersje SMB są obsługiwane przez Azure NetApp Files?

Azure NetApp Files obsługuje SMB 2.1 i SMB 3.1 (w tym obsługę SMB 3.0).    

### <a name="is-an-active-directory-connection-required-for-smb-access"></a>Czy do uzyskania dostępu do SMB jest wymagane połączenie z usługą Active Directory? 

Tak, należy utworzyć połączenie usługi Active Directory przed wdrożeniem woluminu SMB. Określone kontrolery domeny muszą być dostępne dla delegowanego podsieci Azure NetApp Files pomyślnego połączenia.  Aby uzyskać szczegółowe informacje, zobacz Tworzenie [woluminu SMB.](./azure-netapp-files-create-volumes-smb.md) 

### <a name="how-many-active-directory-connections-are-supported"></a>Ile połączeń usługi Active Directory jest obsługiwanych?

Azure NetApp Files nie obsługuje wielu połączeń usługi Active Directory (AD) w jednym *regionie,* nawet jeśli połączenia usługi AD znajdują się na różnych kontach usługi NetApp. Można jednak mieć wiele połączeń usługi AD w ramach jednej *subskrypcji,* o ile połączenia usługi AD znajdują się w różnych regionach. Jeśli potrzebujesz wielu połączeń usługi AD w jednym regionie, możesz w tym celu użyć oddzielnych subskrypcji. 

Połączenie usługi AD jest konfigurowane dla konta usługi NetApp. Połączenie z usługą AD jest widoczne tylko za pośrednictwem konta usługi NetApp, w ramach którego zostało utworzone.

### <a name="does-azure-netapp-files-support-azure-active-directory"></a>Czy Azure NetApp Files obsługuje Azure Active Directory? 

Obsługiwane [Azure Active Directory (AD) Domain Services](../active-directory-domain-services/overview.md) i Active Directory Domain Services [(AD DS).](/windows-server/identity/ad-ds/get-started/virtual-dc/active-directory-domain-services-overview) Istniejących kontrolerów domeny usługi Active Directory można używać z Azure NetApp Files. Kontrolery domeny mogą znajdować się na platformie Azure jako maszyny wirtualne lub lokalnie za pośrednictwem usługi ExpressRoute lub sieci VPN S2S. Azure NetApp Files obecnie nie obsługuje dołączania [](https://azure.microsoft.com/resources/videos/azure-active-directory-overview/) do Azure Active Directory AD.

Jeśli używasz usługi Azure NetApp Files z Azure Active Directory Domain Services, ścieżka jednostki organizacyjnej jest podczas konfigurowania usługi `OU=AADDC Computers` Active Directory dla konta usługi NetApp.

### <a name="what-versions-of-windows-server-active-directory-are-supported"></a>Jakie wersje Windows Server Active Directory są obsługiwane?

Azure NetApp Files obsługuje wersje systemu Windows Server 2008r2SP1-2019 Active Directory Domain Services.

### <a name="why-does-the-available-space-on-my-smb-client-not-show-the-provisioned-size"></a>Dlaczego w dostępnym miejscu na kliencie SMB nie jest pokazywany aprowizowany rozmiar?

Rozmiar woluminu zgłaszany przez klienta SMB jest maksymalnym rozmiarem, do Azure NetApp Files woluminu. Rozmiar woluminu Azure NetApp Files, jak pokazano na kliencie SMB, nie odzwierciedla limitu przydziału ani rozmiaru woluminu. Rozmiar woluminu Azure NetApp Files przydział można uzyskać za pośrednictwem Azure Portal lub interfejsu API.

### <a name="im-having-issues-connecting-to-my-smb-share-what-should-i-do"></a>Mam problemy z nawiązaniem połączenia z moim udziałem SMB. Co mam zrobić?

Najlepszym rozwiązaniem jest ustawienie maksymalnej tolerancji synchronizacji zegara komputera na pięć minut. Aby uzyskać więcej informacji, zobacz [maksymalna tolerancja synchronizacji zegara komputera.](/previous-versions/windows/it-pro/windows-server-2012-r2-and-2012/jj852172(v=ws.11)) 

### <a name="can-i-manage-smb-shares-sessions-and-open-files-through-computer-management-console-mmc"></a>Czy mogę `SMB Shares` zarządzać `Sessions` systemami , i za `Open Files` pomocą konsoli zarządzania komputerem (MMC)?

Zarządzanie `SMB Shares` usługami `Sessions` , i za `Open Files` pośrednictwem konsoli zarządzania komputerem (MMC) nie jest obecnie obsługiwane.

### <a name="how-can-i-obtain-the-ip-address-of-an-smb-volume-via-the-portal"></a>Jak uzyskać adres IP woluminu SMB za pośrednictwem portalu?

Użyj **linku Widok JSON** w okienku przeglądu woluminu i poszukaj identyfikatora **startIp** w obszarze **właściwości**  ->  **mountTargets**.

### <a name="can-an-azure-netapp-files-smb-share-act-as-an-dfs-namespace-dfs-n-root"></a>Czy udział SMB Azure NetApp Files może działać jako katalog główny przestrzeni nazw systemu plików DFS (DFS-N)?

Nie. Jednak Azure NetApp Files SMB mogą służyć jako obiekt docelowy folderu przestrzeni nazw systemu plików DFS (DFS-N).   
Aby użyć udziału SMB Azure NetApp Files jako obiektu docelowego folderu DFS-N, podaj ścieżkę instalacji Universal Naming Convention (UNC) udziału SMB w systemie plików Azure NetApp Files za pomocą procedury Dodawania obiektu docelowego folderu systemu plików [DFS.](/windows-server/storage/dfs-namespaces/add-folder-targets#to-add-a-folder-target)  

### <a name="smb-encryption-faqs"></a>Często zadawane pytania o szyfrowanie SMB

W tej sekcji znajdują się odpowiedzi na często zadawane pytania dotyczące szyfrowania SMB (SMB 3.0 i SMB 3.1.1).

#### <a name="what-is-smb-encryption"></a>Co to jest szyfrowanie SMB?  

[Szyfrowanie SMB](/windows-server/storage/file-server/smb-security) zapewnia end-to-end szyfrowanie danych SMB i chroni dane przed podsłuchiwem wystąpień w niezaufanych sieciach. Szyfrowanie SMB jest obsługiwane w przypadku 3.0 i większych. 

#### <a name="how-does-smb-encryption-work"></a>Jak działa szyfrowanie SMB?

Podczas wysyłania żądania do magazynu klient szyfruje żądanie, które magazyn następnie odszyfrowuje. Odpowiedzi są podobnie szyfrowane przez serwer i odszyfrowywać przez klienta.

#### <a name="which-clients-support-smb-encryption"></a>Którzy klienci obsługują szyfrowanie SMB?

Windows 10, Windows 2012 i nowsze wersje obsługują szyfrowanie SMB.

#### <a name="with-azure-netapp-files-at-what-layer-is-smb-encryption-enabled"></a>W Azure NetApp Files w której warstwie jest włączone szyfrowanie SMB?  

Szyfrowanie SMB jest włączone na poziomie udziału.

#### <a name="what-forms-of-smb-encryption-are-used-by-azure-netapp-files"></a>Jakie formy szyfrowania SMB są używane przez Azure NetApp Files?

SMB 3.0 wykorzystuje algorytm AES-CCM, a SMB 3.1.1 wykorzystuje algorytm AES-GCM

#### <a name="is-smb-encryption-required"></a>Czy szyfrowanie SMB jest wymagane?

Szyfrowanie SMB nie jest wymagane. W związku z tym jest on włączony tylko dla danego udziału, jeśli użytkownik zażąda, Azure NetApp Files go włączyć. Azure NetApp Files nigdy nie są udostępniane w Internecie. Są one dostępne tylko z poziomu danej sieci wirtualnej, za pośrednictwem sieci VPN lub usługi Express Route, Azure NetApp Files udziały są z natury bezpieczne. Wybór opcji włączenia szyfrowania SMB zależy wyłącznie od użytkownika. Przed włączeniem tej funkcji należy pamiętać o przewidywanej karze za wydajność.

#### <a name="what-is-the-anticipated-impact-of-smb-encryption-on-client-workloads"></a><a name="smb_encryption_impact"></a>Jaki jest przewidywany wpływ szyfrowania SMB na obciążenia klientów?

Chociaż szyfrowanie SMB ma wpływ zarówno na klienta (obciążenie procesora CPU związane z szyfrowaniem i odszyfrowywanie komunikatów), jak i na magazyn (zmniejszenie przepływności), w poniższej tabeli przedstawiono tylko wpływ na magazyn. Przed wdrożeniem obciążeń w środowisku produkcyjnym należy przetestować wpływ na wydajność szyfrowania dla własnych aplikacji.

|     Profil We/Wy       |     Wpływ        |
|-  |-  |
|     Obciążenia odczytu i zapisu      |     Od 10% do 15%        |
|     Intensywne metadane        |     5%    |

## <a name="capacity-management-faqs"></a>Zarządzanie pojemnością : często zadawane pytania

### <a name="how-do-i-monitor-usage-for-capacity-pool-and-volume-of-azure-netapp-files"></a>Jak mogę monitorować użycie puli pojemności i woluminu Azure NetApp Files? 

Azure NetApp Files udostępnia metryki użycia puli pojemności i woluminów. Za pomocą Azure Monitor można również monitorować użycie Azure NetApp Files. Aby [uzyskać szczegółowe informacje, zobacz metryki](azure-netapp-files-metrics.md) Azure NetApp Files metryki. 

### <a name="can-i-manage-azure-netapp-files-through-azure-storage-explorer"></a>Czy mogę zarządzać Azure NetApp Files za pośrednictwem Eksplorator usługi Azure Storage?

Nie. Azure NetApp Files nie jest obsługiwane przez Eksplorator usługi Azure Storage.

### <a name="how-do-i-determine-if-a-directory-is-approaching-the-limit-size"></a>Jak mogę określić, czy katalog zbliża się do limitu rozmiaru?

Za pomocą polecenia klienta można sprawdzić, czy katalog zbliża się do maksymalnego limitu rozmiaru metadanych katalogu `stat` (320 MB).   

W przypadku katalogu o rozmiarze 320 MB liczba bloków wynosi 655360, a każdy rozmiar bloku to 512 bajtów.  (Czyli 320x1024x1024/512).  Ta liczba przekłada się na około 4 miliony plików maksymalnych dla katalogu o rozmiarze 320 MB. Jednak rzeczywista maksymalna liczba plików może być mniejsza w zależności od czynników, takich jak liczba plików zawierających znaki inne niż ASCII w katalogu. W związku z tym należy użyć polecenia `stat` w następujący sposób, aby określić, czy katalog zbliża się do limitu.  

Przykłady:

```console
[makam@cycrh6rtp07 ~]$ stat bin
File: 'bin'
Size: 4096            Blocks: 8          IO Block: 65536  directory

[makam@cycrh6rtp07 ~]$ stat tmp
File: 'tmp'
Size: 12288           Blocks: 24         IO Block: 65536  directory
 
[makam@cycrh6rtp07 ~]$ stat tmp1
File: 'tmp1'
Size: 4096            Blocks: 8          IO Block: 65536  directory
```


## <a name="data-migration-and-protection-faqs"></a>Często zadawane pytania dotyczące migracji i ochrony danych

### <a name="how-do-i-migrate-data-to-azure-netapp-files"></a>Jak mogę migrować dane do Azure NetApp Files?
Azure NetApp Files udostępnia woluminy NFS i SMB.  Do migrowania danych do usługi można użyć dowolnego narzędzia kopiowania opartego na plikach. 

Usługa NetApp oferuje oparte na modelu SaaS rozwiązanie [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  Rozwiązanie umożliwia replikowanie danych NFS lub SMB do Azure NetApp Files NFS lub udziałów SMB. 

Możesz również użyć szerokiej gamy bezpłatnych narzędzi do kopiowania danych. W przypadku systemu plików NFS można użyć narzędzi obciążeń, takich jak [rsync,](https://rsync.samba.org/examples.html) aby skopiować i zsynchronizować dane źródłowe Azure NetApp Files woluminie. W przypadku funkcji SMB można używać [robocopy](/windows-server/administration/windows-commands/robocopy) obciążeń w taki sam sposób.  Te narzędzia mogą również replikować uprawnienia do plików lub folderów. 

Wymagania dotyczące migracji danych ze środowiska lokalnego do Azure NetApp Files są następujące: 

- Upewnij Azure NetApp Files, że usługa jest dostępna w docelowym regionie świadczenia usługi Azure.
- Zweryfikuj łączność sieciową między źródłem Azure NetApp Files docelowym adresem IP woluminu. Transfer danych między środowiskiem lokalnym i usługą Azure NetApp Files jest obsługiwany za pośrednictwem usługi ExpressRoute.
- Utwórz wolumin Azure NetApp Files docelowego.
- Przenieś dane źródłowe na wolumin docelowy za pomocą preferowanego narzędzia do kopiowania plików.

### <a name="how-do-i-create-a-copy-of-an-azure-netapp-files-volume-in-another-azure-region"></a>Jak mogę utworzyć kopię woluminu Azure NetApp Files w innym regionie platformy Azure?
    
Azure NetApp Files udostępnia woluminy NFS i SMB.  Do replikowania danych między regionami świadczenia usługi Azure można użyć dowolnego narzędzia do kopiowania plików. 

Usługa NetApp oferuje oparte na modelu SaaS rozwiązanie [NetApp Cloud Sync.](https://cloud.netapp.com/cloud-sync-service)  Rozwiązanie umożliwia replikowanie danych NFS lub SMB do Azure NetApp Files NFS lub udziałów SMB. 

Do kopiowania danych można również użyć szerokiej gamy bezpłatnych narzędzi. W przypadku systemu plików NFS można użyć narzędzi obciążeń, takich jak [rsync,](https://rsync.samba.org/examples.html) aby skopiować i zsynchronizować dane źródłowe Azure NetApp Files woluminie. W przypadku funkcji SMB można w ten sam sposób użyć [funkcji robocopy](/windows-server/administration/windows-commands/robocopy) obciążeń.  Te narzędzia mogą również replikować uprawnienia do plików lub folderów. 

Wymagania dotyczące replikowania woluminu Azure NetApp Files do innego regionu świadczenia usługi Azure są następujące: 
- Upewnij Azure NetApp Files, że usługa jest dostępna w docelowym regionie świadczenia usługi Azure.
- Zweryfikuj łączność sieciową między sieciami wirtualnmi w każdym regionie. Obecnie globalna komunikacja równorzędna między sieciami wirtualnmi nie jest obsługiwana.  Możesz ustanowić łączność między sieciami wirtualnmi, łącząc się z obwodem usługi ExpressRoute lub używając połączenia sieci VPN S2S. 
- Utwórz wolumin Azure NetApp Files docelowego.
- Przenieś dane źródłowe na wolumin docelowy przy użyciu preferowanego narzędzia do kopiowania plików.

### <a name="is-migration-with-azure-data-box-supported"></a>Czy migracja z Azure Data Box obsługiwana?

Nie. Azure Data Box obecnie nie obsługuje Azure NetApp Files. 

### <a name="is-migration-with-azure-importexport-service-supported"></a>Czy migracja przy użyciu usługi Azure Import/Export jest obsługiwana?

Nie. Usługa Azure Import/Export nie obsługuje Azure NetApp Files obecnie.

## <a name="product-faqs"></a>Często zadawane pytania dotyczące produktu

### <a name="can-i-use-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Czy można używać Azure NetApp Files NFS lub SMB z Azure VMware Solution (AVS)?

Woluminy NFS Azure NetApp Files można zainstalować na maszyny wirtualne z systemem Windows lub Linux usługi AVS. Możesz mapować Azure NetApp Files SMB na maszyny wirtualne AVS z systemem Windows. Aby uzyskać więcej informacji, zobacz [Azure NetApp Files z Azure VMware Solution]( ../azure-vmware/netapp-files-with-azure-vmware-solution.md).  

### <a name="what-regions-are-supported-for-using-azure-netapp-files-nfs-or-smb-volumes-with-azure-vmware-solution-avs"></a>Jakie regiony są obsługiwane w przypadku korzystania Azure NetApp Files NFS lub SMB z Azure VMware Solution (AVS)?

Używanie Azure NetApp Files NFS lub SMB z usługą AVS jest obsługiwane w następujących regionach: Wschodnie stany USA, Zachodnie stany USA, Europa Zachodnia i Australia Wschodnia.

## <a name="next-steps"></a>Następne kroki  

- [Microsoft Azure ExpressRoute : często zadawane pytania](../expressroute/expressroute-faqs.md)
- [Microsoft Azure Virtual Network — często zadawane pytania](../virtual-network/virtual-networks-faq.md)
- [Jak utworzyć żądanie pomocy technicznej dla platformy Azure](../azure-portal/supportability/how-to-create-azure-support-request.md)
- [Azure Data Box](../databox/index.yml)
- [Często zadawane pytania dotyczące wydajności SMB dla Azure NetApp Files](azure-netapp-files-smb-performance.md)
