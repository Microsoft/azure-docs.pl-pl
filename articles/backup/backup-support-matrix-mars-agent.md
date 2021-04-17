---
title: Macierz obsługi agenta MARS
description: Ten artykuł zawiera podsumowanie Azure Backup w przypadku kopii zapasowej maszyn z uruchomionym agentem Microsoft Azure Recovery Services (MARS).
ms.date: 04/09/2021
ms.topic: conceptual
ms.openlocfilehash: 20bca0e9ca9dfd735501e68bd0e5a6d69d2ef68e
ms.sourcegitcommit: d3bcd46f71f578ca2fd8ed94c3cdabe1c1e0302d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107576503"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Macierz obsługi kopii zapasowych przy użyciu agenta Microsoft Azure Recovery Services (MARS)

Za pomocą usługi [Azure Backup można](backup-overview.md) back up on-premises machines and apps and to back up Azure virtual machines (VMs) (Maszyny wirtualne platformy Azure). Ten artykuł zawiera podsumowanie ustawień pomocy technicznej i ograniczeń dotyczących używania agenta Microsoft Azure Recovery Services (MARS) do obsługi kopii zapasowej maszyn.

## <a name="the-mars-agent"></a>Agent MARS

Azure Backup agent MARS jest używany do tworzenia kopii zapasowych danych z maszyn lokalnych i maszyn wirtualnych platformy Azure do magazynu usługi Recovery Services kopii zapasowej na platformie Azure. Agent MARS może:

- Uruchamianie na lokalnych maszynach z systemem Windows, aby można było tworzyć kopie zapasowe bezpośrednio w magazynie usługi Recovery Services kopii zapasowej na platformie Azure.
- Uruchamianie na maszynach wirtualnych z systemem Windows, aby można było utworzyć ich kopię zapasową bezpośrednio w magazynie.
- Uruchamiany na Microsoft Azure Backup Server (MABS) lub System Center Data Protection Manager (DPM). W tym scenariuszu maszyny i obciążenia są kopiami zapasowymi na serwerze MABS lub dpm. Agent MARS następnie kopię zapasową tego serwera w magazynie na platformie Azure.

> [!NOTE]
>Azure Backup nie obsługuje automatycznego korekty zegara dla czasu letniego (DST). Zmodyfikuj zasady, aby uwzględnić zmiany czasu letniego, aby zapobiec rozbieżnościom między czasem rzeczywistym i zaplanowanym czasem tworzenia kopii zapasowej.

Opcje tworzenia kopii zapasowej zależą od miejsca instalacji agenta. Aby uzyskać więcej informacji, [zobacz Azure Backup przy użyciu agenta MARS.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders) Aby uzyskać informacje o architekturze kopii zapasowych usług MABS i DPM, zobacz [Tworzenie kopii zapasowej w programie DPM lub u usługi MABS.](backup-architecture.md#architecture-back-up-to-dpmmabs) Zobacz również [wymagania dotyczące](backup-support-matrix-mabs-dpm.md) architektury kopii zapasowych.

**Instalacja** | **Szczegóły**
--- | ---
Pobieranie najnowszego agenta MARS | Możesz pobrać najnowszą wersję agenta z magazynu lub [pobrać go bezpośrednio.](https://aka.ms/azurebackup_agent)
Instalowanie bezpośrednio na maszynie | Agenta MARS można zainstalować bezpośrednio na lokalnym serwerze z systemem Windows lub na maszynie wirtualnej z systemem Windows z dowolnym z [obsługiwanych systemów operacyjnych.](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)
Instalowanie na serwerze kopii zapasowych | Po skonfigurowaniu programu DPM lub usługi MABS do kopii zapasowej na platformie Azure należy pobrać i zainstalować agenta MARS na serwerze. Agenta można zainstalować w obsługiwanych [systemach operacyjnych](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) w macierzy obsługi serwera kopii zapasowych.

> [!NOTE]
> Domyślnie maszyny wirtualne platformy Azure, dla których włączono tworzenie kopii zapasowych, Azure Backup instalacji rozszerzenia. To rozszerzenie zawiera kopię zapasową całej maszyny wirtualnej. Agenta MARS można zainstalować i uruchomić na maszynie wirtualnej platformy Azure wraz z rozszerzeniem, jeśli chcesz utworzyć kopię zapasową określonych folderów i plików, a nie pełną maszynę wirtualną.
> Po uruchomieniu agenta MARS na maszynie wirtualnej platformy Azure jest dzielonych na kopię zapasową plików lub folderów, które znajdują się w magazynie tymczasowym na maszynie wirtualnej. Tworzenie kopii zapasowych nie powiedzie się, jeśli pliki lub foldery zostaną usunięte z magazynu tymczasowego lub magazyn tymczasowy zostanie usunięty.

## <a name="cache-folder-support"></a>Obsługa folderów pamięci podręcznej

Gdy używasz agenta MARS do tworzenia kopii zapasowej danych, agent pobiera migawkę danych i zapisuje je w lokalnym folderze pamięci podręcznej przed wysłaniem danych na platformę Azure. Folder pamięci podręcznej (scratch) ma kilka wymagań:

**Cache** | **Szczegóły**
--- | ---
Rozmiar |  Wolne miejsce w folderze pamięci podręcznej powinno mieć co najmniej 5–10% całkowitego rozmiaru danych kopii zapasowej.
Lokalizacja | Folder pamięci podręcznej musi być przechowywany lokalnie na maszynie, na którym jest owana kopii zapasowej, i musi być w trybie online. Folder pamięci podręcznej nie powinien być w udziału sieciowym, na nośniku wymiennym ani na woluminie w trybie offline.
Folder | Folder pamięci podręcznej nie powinien być szyfrowany na zdeduplikowanym woluminie ani w folderze skompresowanym, rozrzednionym lub z punktem ponownej ponownej instalacji.
Zmiany lokalizacji | Lokalizację pamięci podręcznej można zmienić, zatrzymując aparat kopii zapasowych ( ) i `net stop bengine` kopiując folder pamięci podręcznej na nowy dysk. (Upewnij się, że nowy dysk ma wystarczającą ilość miejsca). Następnie zaktualizuj dwa wpisy rejestru w **obszarze HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**Config/ScratchLocation** i **Config/CloudBackupProvider/ScratchLocation**) do nowej lokalizacji i uruchom ponownie aparat.

## <a name="networking-and-access-support"></a>Obsługa sieci i dostępu

### <a name="url-and-ip-access"></a>Dostęp do adresów URL i IP

Agent MARS musi mieć dostęp do tych adresów URL:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net
- `www.msftconnecttest.com`

I do tych adresów IP:

- 20.190.128.0/18
- 40.126.0.0/18

Dostęp do wszystkich adresów URL i IP wymienionych powyżej używa protokołu HTTPS na porcie 443.

Podczas backing up files and folders from Azure VMs using the MARS Agent, the Azure virtual network also needs to be configure to allow access. Jeśli używasz sieciowych grup zabezpieczeń, użyj tagu usługi *AzureBackup,* aby zezwolić na dostęp ruchu wychodzącego do Azure Backup. Oprócz tagu Azure Backup należy również zezwolić na łączność na potrzeby uwierzytelniania i transferu danych, tworząc podobne reguły sieciowej grupy zasobów dla usług Azure AD *(AzureActiveDirectory)* i Azure [](../virtual-network/network-security-groups-overview.md#service-tags) Storage(Storage). W poniższych krokach opisano proces tworzenia reguły dla Azure Backup tagu:

1. W **chmurze Wszystkie** usługi przejdź do **opcji Sieciowe grupy zabezpieczeń** i wybierz sieciową grupę zabezpieczeń.
2. Wybierz **pozycję Reguły zabezpieczeń dla ruchu wychodzącego** w obszarze **Ustawienia**.
3. Wybierz pozycję **Dodaj**. Wprowadź wszystkie wymagane szczegóły dotyczące tworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](../virtual-network/manage-network-security-group.md#security-rule-settings). Upewnij się, że **opcja Destination (Miejsce** docelowe) jest ustawiona na *wartość Service Tag (Tag usługi),* a **opcja Destination service tag (Docelowy tag usługi)** jest ustawiona na *Wartość AzureBackup.*
4. Wybierz **pozycję Dodaj,** aby zapisać nowo utworzoną regułę zabezpieczeń dla ruchu wychodzącego.

Podobnie można utworzyć reguły zabezpieczeń ruchu wychodzącego sieciowej grupy zabezpieczeń dla usług Azure Storage i Azure AD. Aby uzyskać więcej informacji na temat tagów usługi, zobacz [ten artykuł.](../virtual-network/service-tags-overview.md)

### <a name="azure-expressroute-support"></a>Azure ExpressRoute pomocy technicznej

Możesz utworzyć kopię zapasową danych za pośrednictwem Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępnej dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowej za pośrednictwem prywatnej komunikacji równorzędnej nie jest obsługiwane.

W przypadku publicznej komunikacji równorzędnej: Zapewnianie dostępu do następujących domen/adresów:

* Adresy URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Adresy IP
  * 20.190.128.0/18
  * 40.126.0.0/18

Za pomocą komunikacji równorzędnej firmy Microsoft wybierz następujące usługi/regiony i odpowiednie wartości społeczności:

- Azure Backup (zgodnie z lokalizacją magazynu usługi Recovery Services)
- Azure Active Directory (12076:5060)
- Azure Storage (zgodnie z lokalizacją magazynu usługi Recovery Services)

Aby uzyskać więcej informacji, zobacz [ExpressRoute routing requirements (Wymagania dotyczące routingu usługi ExpressRoute).](../expressroute/expressroute-routing.md#bgp)

>[!NOTE]
>Publiczna komunikacja równorzędna jest przestarzała dla nowych obwodów.

### <a name="private-endpoint-support"></a>Obsługa prywatnego punktu końcowego

Teraz możesz używać prywatnych punktów końcowych do bezpiecznego przechowywania kopii zapasowych danych z serwerów do magazynu usługi Recovery Services. Ponieważ Azure Active Directory nie obsługują obecnie prywatnych punktów końcowych, wymagane przez usługę Azure Active Directory dostęp wychodzący będzie wymagał osobnego dostępu dla ruchu wychodzącego.

Jeśli używasz agenta MARS do kopii zapasowych zasobów lokalnych, upewnij się, że sieć lokalna (zawierająca zasoby, których kopię zapasową chcesz wykonać) jest za pomocą komunikacji równorzędnej z siecią wirtualną platformy Azure zawierającą prywatny punkt końcowy magazynu. Następnie możesz kontynuować instalowanie agenta MARS i konfigurowanie kopii zapasowej. Należy jednak upewnić się, że cała komunikacja dla kopii zapasowej odbywa się tylko za pośrednictwem sieci równorzędnej.

Jeśli usuniesz prywatne punkty końcowe magazynu po zarejestrowaniu w nim agenta MARS, musisz ponownie zarejestrować kontener w magazynie. Nie musisz zatrzymywać ich ochrony.

Przeczytaj więcej na [temat prywatnych punktów końcowych dla Azure Backup](private-endpoints.md).

### <a name="throttling-support"></a>Obsługa ograniczania przepustowości

**Funkcja** | **Szczegóły**
--- | ---
Kontrola przepustowości | Obsługiwane. W agencie MARS użyj funkcji **Zmień właściwości,** aby dostosować przepustowość.
Ograniczanie przepustowości sieci | Niedostępne dla maszyn kopii zapasowej z systemem Windows Server 2008 R2, Windows Server 2008 z dodatkiem SP2 lub Windows 7.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

>[!NOTE]
> Agent MARS nie obsługuje podstawowych jednostki SKU systemu Windows Server.

Agenta MARS można użyć do bezpośredniej kopii zapasowej na platformie Azure w wymienionych poniżej systemach operacyjnych, które działają w systemie:

1. Lokalne serwery z systemem Windows
2. Maszyny wirtualne platformy Azure z systemem Windows

Systemy operacyjne muszą być 64-bitowe i powinny mieć uruchomione najnowsze pakiety usług i aktualizacje. W poniższej tabeli przedstawiono podsumowanie tych systemów operacyjnych:

**System operacyjny** | **Pliki/foldery** | **Stan systemu** | **Wymagania dotyczące oprogramowania/modułu**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Yes | Nie |  Sprawdź wymagania dotyczące oprogramowania/modułu w odpowiedniej wersji serwera
Windows 8.1 (Enterprise, Pro)| Yes |Nie | Sprawdź wymagania dotyczące oprogramowania/modułu w odpowiedniej wersji serwera
Windows 8 (Enterprise, Pro) | Yes | Nie | Sprawdź odpowiednią wersję serwera, aby uzyskać informacje o wymaganiach dotyczących oprogramowania/modułu
Windows Server 2016 (Standard, Datacenter, Essentials) | Yes | Yes | — .NET 4.5 <br> — Windows PowerShell <br> - Najnowszy zgodny pakiet redystrybucji Microsoft VC++ <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 R2 (Standard, Datacenter, Foundation, Essentials) | Yes | Yes | — .NET 4.5 <br> — Windows PowerShell <br> - Najnowszy zgodny pakiet redystrybucji Microsoft VC++ <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2012 (Standard, Datacenter, Foundation) | Yes | Yes |— .NET 4.5 <br> -Windows PowerShell <br> - Najnowszy zgodny pakiet redystrybucji Microsoft VC++ <br> - Microsoft Management Console (MMC) 3.0 <br> — Deployment Image Servicing and Management (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (Standard, Workgroup) | Yes | Nie | — .NET 4.5 <br> — Windows PowerShell <br> - Najnowszy zgodny pakiet redystrybucji Microsoft VC++ <br> - Microsoft Management Console (MMC) 3.0
Windows Server 2019 (Standard, Datacenter, Essentials) | Yes | Yes | — .NET 4.5 <br> — Windows PowerShell <br> - Najnowszy zgodny pakiet redystrybucji Microsoft VC++ <br> - Microsoft Management Console (MMC) 3.0

Aby uzyskać więcej informacji, zobacz [Obsługiwane systemy operacyjne MABS i DPM.](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems)

### <a name="operating-systems-at-end-of-support"></a>Systemy operacyjne po zakończeniu wsparcia technicznego

Następujące systemy operacyjne znajdują się na końcu wsparcia i zdecydowanie zaleca się uaktualnienie systemu operacyjnego w celu kontynuowania ochrony.

Jeśli istniejące zobowiązania uniemożliwiają uaktualnienie systemu operacyjnego, rozważ migrowanie serwerów z systemem Windows na maszyny wirtualne platformy Azure i korzystanie z kopii zapasowych maszyn wirtualnych platformy Azure, aby nadal być chronione. Odwiedź stronę [migracji tutaj,](https://azure.microsoft.com/migration/windows-server/) aby uzyskać więcej informacji na temat migracji serwera z systemem Windows.

W przypadku środowisk lokalnych lub hostowanych, w których nie można uaktualnić systemu operacyjnego ani przeprowadzić migracji na platformę Azure, aktywuj rozszerzone aktualizacje zabezpieczeń dla maszyn, aby nadal pozostawać chronione i obsługiwane. Zwróć uwagę, że tylko określone wersje kwalifikują się do rozszerzonych aktualizacji zabezpieczeń. Odwiedź stronę [często zadawanych pytań,](https://www.microsoft.com/windows-server/extended-security-updates) aby dowiedzieć się więcej.

| **System operacyjny**                                       | **Pliki/foldery** | **Stan systemu** | **Wymagania dotyczące oprogramowania/modułu**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Yes               | Nie                 | Sprawdź odpowiednią wersję serwera, aby uzyskać informacje o wymaganiach dotyczących oprogramowania/modułu |
| Windows Server 2008 R2 (Standard, Enterprise, Datacenter, Foundation) | Yes               | Yes                | — .NET 3.5, .NET 4.5 <br>  — Windows PowerShell <br>  — Zgodny pakiet redystrybucyjne Microsoft VC++ <br>  - Microsoft Management Console (MMC) 3.0 <br>  — Deployment Image Servicing and Management (DISM.exe) |
| Windows Server 2008 SP2 (Standard, Datacenter, Foundation)  | Yes               | Nie                 | — .NET 3.5, .NET 4.5 <br>  — Windows PowerShell <br>  — Zgodny pakiet redystrybucyjne Microsoft VC++ <br>  - Microsoft Management Console (MMC) 3.0 <br>  — Deployment Image Servicing and Management (DISM.exe) <br>  - Virtual Server 2005 base + KB KB948515 |

## <a name="backup-limits"></a>Limity kopii zapasowych

### <a name="size-limits"></a>Limity rozmiaru

Azure Backup ogranicza rozmiar źródła danych pliku lub folderu, dla których można utworzyć kopię zapasową. Elementy, których kopię zapasową można utworzyć z jednego woluminu, nie mogą przekraczać rozmiarów podsumowanych w tej tabeli:

**System operacyjny** | **Limit rozmiaru**
--- | ---
Windows Server 2012 lub nowszy |54 400 GB
Windows Server 2008 R2 SP1 |1700 GB
Windows Server 2008 SP2| 1700 GB
Windows 8 lub nowszy| 54 400 GB
Windows 7| 1700 GB

### <a name="minimum-retention-limits"></a>Minimalne limity przechowywania

Poniżej przedstawiono minimalne czasy przechowywania, które można ustawić dla różnych punktów odzyskiwania:

|Punkt odzyskiwania |Czas trwania  |
|---------|---------|
|Codzienny punkt odzyskiwania    |   7 dni      |
|Cotygodniowy punkt odzyskiwania     |    4 tygodnie     |
|Miesięczny punkt odzyskiwania    |   3 miesiące      |
|Yearly recovery point  |      1 rok   |

### <a name="other-limitations"></a>Inne ograniczenia

- Mars nie obsługuje ochrony wielu maszyn o tej samej nazwie w jednym magazynie.

## <a name="supported-file-types-for-backup"></a>Obsługiwane typy plików dla kopii zapasowej

**Typ** | **Pomoc techniczna**
--- | ---
Szyfrowane<sup>*</sup>| Obsługiwane.
Skompresowane | Obsługiwane.
Rozrzedzone | Obsługiwane.
Skompresowane i rozrzedzione |Obsługiwane.
Twarde linki| Nieobsługiwane. Pominięte.
Punkt ponownej analizy| Nieobsługiwane. Pominięte.
Zaszyfrowane i rozrzedniane |Nieobsługiwane. Pominięte.
Skompresowany strumień| Nieobsługiwane. Pominięte.
Rozrzedny strumień| Nieobsługiwane. Pominięte.
OneDrive (zsynchronizowane pliki są strumieniami rozrzednia)| Nieobsługiwane.
Foldery z włączoną Replikacja systemu plików DFS sieci | Nieobsługiwane.

\* Upewnij się, że agent MARS ma dostęp do wymaganych certyfikatów w celu uzyskania dostępu do zaszyfrowanych plików. Niedostępne pliki zostaną pominięte.

## <a name="supported-drives-or-volumes-for-backup"></a>Obsługiwane dyski lub woluminy do tworzenia kopii zapasowej

**Dysk/wolumin** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Woluminy tylko do odczytu| Nieobsługiwane | Usługa kopiowania woluminów w tle (VSS) działa tylko wtedy, gdy wolumin jest zapisywalny.
Woluminy w trybie offline| Nieobsługiwane |Usługa VSS działa tylko wtedy, gdy wolumin jest w trybie online.
Udział sieciowy| Nieobsługiwane |Wolumin musi być lokalny na serwerze.
Woluminy zablokowane przez funkcję BitLocker| Nieobsługiwane |Wolumin musi zostać odblokowany przed rozpoczęciem tworzenia kopii zapasowej.
Identyfikacja systemu plików| Nieobsługiwane |Obsługiwany jest tylko system plików NTFS.
Nośniki wymienne| Nieobsługiwane |Wszystkie źródła elementów kopii zapasowej muszą mieć *stały* stan.
Deduplikowane dyski | Obsługiwane | Azure Backup dane deduplikowane są konwertowane na normalne. Optymalizuje, szyfruje, przechowuje i wysyła dane do magazynu.

## <a name="support-for-initial-offline-backup"></a>Obsługa początkowej kopii zapasowej w trybie offline

Azure Backup obsługuje wstępne *inicjowanie w trybie offline* w celu transferu danych początkowej kopii zapasowej na platformę Azure przy użyciu dysków. Ta obsługa jest przydatna, jeśli początkowa kopia zapasowa prawdopodobnie będzie mieć zakres rozmiaru terabajtów (TB). Kopia zapasowa w trybie offline jest obsługiwana w przypadku:

- Bezpośrednie tworzenie kopii zapasowych plików i folderów na maszynach lokalnych z uruchomionym agentem MARS.
- Tworzenie kopii zapasowych obciążeń i plików z serwera programu DPM lub usługi MABS.

Kopii zapasowej w trybie offline nie można używać dla plików stanu systemu.

## <a name="support-for-data-restoration"></a>Obsługa przywracania danych

Za pomocą [funkcji natychmiastowego przywracania](backup-instant-restore-capability.md) Azure Backup można przywrócić dane przed ich skopiowaniem do magazynu. Maszyna, dla których jest back back up musi być uruchomiona .NET Framework 4.5.2 lub wyższa.

Kopii zapasowych nie można przywrócić na maszynie docelowej, na których działa wcześniejsza wersja systemu operacyjnego. Na przykład kopię zapasową z komputera z systemem Windows 7 można przywrócić na komputerze z systemem Windows 8 nowszym. Jednak kopii zapasowej wykonanej z komputera z systemem Windows 8 nie można przywrócić na komputerze z systemem Windows 7.

## <a name="previous-mars-agent-versions"></a>Poprzednie wersje agenta MARS

W poniższej tabeli wymieniono poprzednie wersje agenta wraz z linkami pobierania. Zalecamy uaktualnienie wersji agenta do najnowszej wersji, aby można było korzystać z najnowszych funkcji i optymalnej wydajności.

**Versions** (Wersje) | **Artykuły bazy wiedzy**
--- | ---
[2.0.9145.0](https://download.microsoft.com/download/4/5/E/45EB38B4-2DA7-45FA-92E1-5CA1E23D18D1/MARSAgentInstaller.exe) | Niedostępne
[2.0.9151.0](https://download.microsoft.com/download/7/1/7/7177B70A-51E8-434D-BDF2-FA3A09E917D6/MARSAgentInstaller.exe) | Niedostępne
[2.0.9153.0](https://download.microsoft.com/download/3/D/D/3DD8A2FF-AC48-4A62-8566-B2C05F0BCCD0/MARSAgentInstaller.exe) | Niedostępne
[2.0.9162.0](https://download.microsoft.com/download/0/1/0/010E598E-6289-47DB-872A-FFAF5030E6BE/MARSAgentInstaller.exe) | Niedostępne
[2.0.9169.0](https://download.microsoft.com/download/f/7/1/f716c719-24bc-4337-af48-113baddc14d8/MARSAgentInstaller.exe) | [4515971](https://support.microsoft.com/help/4538314)
[2.0.9170.0](https://download.microsoft.com/download/1/8/7/187ca9a9-a6e5-45f0-928f-9a843d84aed5/MARSAgentInstaller.exe) | Niedostępne
[2.0.9173.0](https://download.microsoft.com/download/7/9/2/79263a35-de87-4ba6-9732-65563a4274b6/MARSAgentInstaller.exe) | [4538314](https://support.microsoft.com/help/4538314)
[2.0.9177.0](https://download.microsoft.com/download/3/0/4/304d3cdf-b123-42ee-ad03-98fb895bc38f/MARSAgentInstaller.exe) | Niedostępne
[2.0.9181.0](https://download.microsoft.com/download/6/6/9/6698bc49-e30b-4a3e-a1f4-5c859beafdcc/MARSAgentInstaller.exe) | Niedostępne
[2.0.9190.0](https://download.microsoft.com/download/a/c/e/aceffec0-794e-4259-8107-92a3f6c10f55/MARSAgentInstaller.exe) | [4575948](https://support.microsoft.com/help/4575948)
[2.0.9195.0](https://download.microsoft.com/download/6/1/3/613b70a7-f400-4806-9d98-ae26aeb70be9/MARSAgentInstaller.exe) | [4582474](https://support.microsoft.com/help/4582474)
[2.0.9197.0](https://download.microsoft.com/download/2/7/5/27531ace-3100-43bc-b4af-7367680ea66b/MARSAgentInstaller.exe) | [4589598](https://support.microsoft.com/help/4589598)
[2.0.9207.0](https://download.microsoft.com/download/b/5/a/b5a29638-1cef-4906-b704-4d3d914af76e/MARSAgentInstaller.exe) | [5001305](https://support.microsoft.com/help/5001305)

>[!NOTE]
>Wersje agenta MARS z niewielkimi ulepszeniami niezawodności i wydajności nie mają artykułu bazy wiedzy.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [architekturze kopii zapasowych, która korzysta z agenta MARS.](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders)
- Dowiedz się, co jest obsługiwane po uruchomieniu agenta [MARS na serwerze MABS lub dpm.](backup-support-matrix-mabs-dpm.md)
