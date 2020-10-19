---
title: Macierz obsługi dla agenta MARS
description: Ten artykuł zawiera podsumowanie Azure Backup pomocy technicznej podczas tworzenia kopii zapasowej maszyn, na których jest uruchomiony agent Microsoft Azure Recovery Services (MARS).
ms.date: 08/30/2019
ms.topic: conceptual
ms.openlocfilehash: 53034d058e0cd2e1623acc6629da0a694b35e60b
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173539"
---
# <a name="support-matrix-for-backup-with-the-microsoft-azure-recovery-services-mars-agent"></a>Macierz obsługi kopii zapasowej za pomocą agenta Microsoft Azure Recovery Services (MARS)

Za pomocą [usługi Azure Backup](backup-overview.md) można tworzyć kopie zapasowe maszyn i aplikacji lokalnych oraz tworzyć kopie zapasowe maszyn wirtualnych platformy Azure. Ten artykuł zawiera podsumowanie ustawień i ograniczeń pomocy technicznej w przypadku używania agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych maszyn.

## <a name="the-mars-agent"></a>Agent MARS

Azure Backup używa agenta MARS do tworzenia kopii zapasowych danych z maszyn lokalnych i maszyn wirtualnych platformy Azure w magazynie kopii zapasowych Recovery Services na platformie Azure. Agent MARS może:

- Uruchom na lokalnych maszynach z systemem Windows, aby można było tworzyć kopie zapasowe bezpośrednio do magazynu Recovery Services kopii zapasowych na platformie Azure.
- Uruchom na maszynach wirtualnych z systemem Windows, aby można było tworzyć kopie zapasowe bezpośrednio w magazynie.
- Uruchom na serwerze Microsoft Azure Backup Server (serwera usługi MAB) lub w programie System Center Data Protection Manager (DPM). W tym scenariuszu maszyny i obciążenia wykonują kopie zapasowe w programie serwera usługi MAB lub na serwerze programu DPM. Następnie Agent MARS tworzy kopię zapasową tego serwera w magazynie na platformie Azure.

> [!NOTE]
>Azure Backup nie obsługuje automatycznego dostosowywania zegara dla czasu letniego (DST). Zmodyfikuj zasady, aby zapewnić, że zmiany czasu letniego są brane pod uwagę, aby zapobiec niezgodności między rzeczywistym czasem a zaplanowaną godziną tworzenia kopii zapasowej.

Opcje tworzenia kopii zapasowej zależą od tego, gdzie jest zainstalowany agent programu. Aby uzyskać więcej informacji, zobacz [Azure Backup architektury przy użyciu agenta Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders). Aby uzyskać informacje na temat architektury kopii zapasowych serwera usługi MAB i programu DPM, zobacz [Tworzenie kopii zapasowej w programie DPM lub serwera usługi MAB](backup-architecture.md#architecture-back-up-to-dpmmabs). Zapoznaj się również z [wymaganiami](backup-support-matrix-mabs-dpm.md) dotyczącymi architektury tworzenia kopii zapasowych.

**Instalacja** | **Szczegóły**
--- | ---
Pobierz najnowszego agenta MARS | Możesz pobrać najnowszą wersję agenta z magazynu lub [pobrać ją bezpośrednio](https://aka.ms/azurebackup_agent).
Instalowanie bezpośrednio na komputerze | Agenta MARS można zainstalować bezpośrednio na lokalnym serwerze Windows lub na maszynie wirtualnej z systemem Windows, na której działa dowolny z [obsługiwanych systemów operacyjnych](./backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).
Zainstaluj na serwerze kopii zapasowej | Po skonfigurowaniu programu DPM lub serwera usługi MAB na potrzeby tworzenia kopii zapasowych na platformie Azure należy pobrać i zainstalować agenta MARS na serwerze. Agenta programu można zainstalować w [obsługiwanych systemach operacyjnych](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems) w macierzy obsługi serwera kopii zapasowych.

> [!NOTE]
> Domyślnie maszyny wirtualne platformy Azure, dla których włączono obsługę kopii zapasowej, mają instalację rozszerzenia Azure Backup. To rozszerzenie tworzy kopię zapasową całej maszyny wirtualnej. Możesz zainstalować i uruchomić agenta MARS na maszynie wirtualnej platformy Azure obok rozszerzenia, jeśli chcesz utworzyć kopię zapasową określonych folderów i plików, a nie na całej maszynie wirtualnej.
> Po uruchomieniu agenta MARS na maszynie wirtualnej platformy Azure program tworzy kopię zapasową plików lub folderów znajdujących się w magazynie tymczasowym na maszynie wirtualnej. Tworzenie kopii zapasowych kończy się niepowodzeniem, jeśli pliki lub foldery zostaną usunięte z magazynu tymczasowego lub jeśli magazyn tymczasowy zostanie usunięty.

## <a name="cache-folder-support"></a>Obsługa folderu pamięci podręcznej

W celu utworzenia kopii zapasowej danych przy użyciu agenta MARS Agent tworzy migawkę danych i zapisuje ją w folderze lokalnego pamięci podręcznej, zanim wyśle dane do platformy Azure. Folder pamięci podręcznej (Scratch) ma kilka wymagań:

**Cache** | **Szczegóły**
--- | ---
Rozmiar |  Ilość wolnego miejsca w folderze pamięci podręcznej powinna wynosić co najmniej 5 do 10 procent całkowitego rozmiaru danych kopii zapasowej.
Lokalizacja | Folder pamięci podręcznej musi być przechowywany lokalnie na komputerze, na którym jest wykonywana kopia zapasowa, i musi być w trybie online. Folder pamięci podręcznej nie powinien znajdować się w udziale sieciowym na nośniku wymiennym ani w woluminie w trybie offline.
Folder | Folder pamięci podręcznej nie powinien być szyfrowany na deduplikowanym woluminie lub w folderze skompresowanym, który jest rozrzedzony, lub ma punkt ponownej analizy.
Zmiany lokalizacji | Można zmienić lokalizację pamięci podręcznej, zatrzymując aparat kopii zapasowych ( `net stop bengine` ) i kopiując folder pamięci podręcznej na nowy dysk. (Upewnij się, że nowy dysk ma wystarczającą ilość miejsca). Następnie zaktualizuj dwa wpisy rejestru w obszarze **HKLM\SOFTWARE\Microsoft\Windows Azure Backup** (**config/ScratchLocation** i **config/CloudBackupProvider/ScratchLocation**) do nowej lokalizacji i ponownie uruchom aparat.

## <a name="networking-and-access-support"></a>Obsługa sieci i dostępu

### <a name="url-and-ip-access"></a>Dostęp do adresów URL i adresów IP

Agent MARS musi mieć dostęp do tych adresów URL:

- `http://www.msftncsi.com/ncsi.txt`
- *.Microsoft.com
- *.WindowsAzure.com
- *. MicrosoftOnline.com
- *. Windows.net

I na te adresy IP:

- 20.190.128.0/18
- 40.126.0.0/18

Dostęp do wszystkich adresów URL i adresów IP wymienionych powyżej używa protokołu HTTPS na porcie 443.

Podczas tworzenia kopii zapasowych plików i folderów z maszyn wirtualnych platformy Azure przy użyciu agenta MARS należy również skonfigurować usługę Azure Virtual Network tak, aby zezwalała na dostęp. Jeśli używasz sieciowych grup zabezpieczeń (sieciowej grupy zabezpieczeń), Użyj znacznika usługi *AzureBackup* , aby zezwolić na dostęp wychodzący do Azure Backup. Oprócz znacznika Azure Backup należy również zezwolić na połączenie z uwierzytelnianiem i transferem danych, tworząc podobne [reguły sieciowej grupy zabezpieczeń](../virtual-network/network-security-groups-overview.md#service-tags) dla usługi Azure AD (*usługi azureactivedirectory*) i usługi Azure Storage (*Magazyn*). Poniższe kroki opisują proces tworzenia reguły dla tagu Azure Backup:

1. W obszarze **wszystkie usługi**przejdź do pozycji **sieciowe grupy zabezpieczeń** i wybierz grupę zabezpieczeń sieci.
2. W obszarze **Ustawienia**wybierz pozycję **reguły zabezpieczeń dla ruchu wychodzącego** .
3. Wybierz pozycję **Dodaj**. Wprowadź wszystkie wymagane szczegóły dotyczące tworzenia nowej reguły zgodnie z opisem w [ustawieniach reguły zabezpieczeń](../virtual-network/manage-network-security-group.md#security-rule-settings). Upewnij się, że opcja **miejsce docelowe** jest ustawiona na *tag usługi* i **znacznik usługi docelowej** jest ustawiony na *AzureBackup*.
4. Wybierz pozycję **Dodaj** , aby zapisać nowo utworzoną regułę zabezpieczeń dla ruchu wychodzącego.

W podobny sposób można tworzyć reguły zabezpieczeń wychodzące sieciowej grupy zabezpieczeń dla usługi Azure Storage i usługi Azure AD. Aby uzyskać więcej informacji na temat tagów usługi, zobacz [ten artykuł](../virtual-network/service-tags-overview.md).

### <a name="azure-expressroute-support"></a>Pomoc techniczna platformy Azure ExpressRoute

Możesz tworzyć kopie zapasowe danych za pośrednictwem usługi Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępne dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowej za pośrednictwem prywatnej komunikacji równorzędnej nie jest obsługiwane.

Przy użyciu publicznej komunikacji równorzędnej: Upewnij się, że dostęp do następujących domen/adresów:

- `http://www.msftncsi.com/ncsi.txt`
- `microsoft.com`
- `.WindowsAzure.com`
- `.microsoftonline.com`
- `.windows.net`

W przypadku komunikacji równorzędnej firmy Microsoft wybierz następujące usługi/regiony i odpowiednie wartości społeczności:

- Azure Backup (zgodnie z lokalizacją magazynu Recovery Services)
- Azure Active Directory (12076:5060)
- Azure Storage (zgodnie z lokalizacją magazynu Recovery Services)

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące routingu ExpressRoute](../expressroute/expressroute-routing.md#bgp).

>[!NOTE]
>Publiczna Komunikacja równorzędna jest przestarzała dla nowych obwodów.

### <a name="private-endpoint-support"></a>Obsługa prywatnego punktu końcowego

Możesz teraz używać prywatnych punktów końcowych do bezpiecznego tworzenia kopii zapasowych danych z serwerów w magazynie Recovery Services. Ponieważ Azure Active Directory nie obsługuje obecnie prywatnych punktów końcowych, adresy IP i nazwy FQDN wymagane dla Azure Active Directory muszą mieć dozwolony dostęp wychodzący osobno.

W przypadku korzystania z agenta MARS do tworzenia kopii zapasowych zasobów lokalnych upewnij się, że sieć lokalna (zawierająca zasoby, których kopia zapasowa ma zostać utworzona), jest równorzędna z siecią wirtualną platformy Azure, która zawiera prywatny punkt końcowy magazynu. Następnie można nadal zainstalować agenta MARS i skonfigurować kopię zapasową. Należy jednak upewnić się, że cała komunikacja w ramach kopii zapasowej odbywa się tylko za pomocą sieci równorzędnej.

Jeśli usuniesz prywatne punkty końcowe dla magazynu po zarejestrowaniu agenta MARS, należy ponownie zarejestrować kontener z magazynem. Nie musisz zatrzymać ochrony dla nich.

Przeczytaj więcej [na temat prywatnych punktów końcowych dla Azure Backup](private-endpoints.md).

### <a name="throttling-support"></a>Obsługa ograniczania przepustowości

**Funkcja** | **Szczegóły**
--- | ---
Kontrola przepustowości | Obsługiwane. W agencie MARS Użyj **właściwości Zmień** , aby dostosować przepustowość.
Ograniczanie przepustowości sieci | Niedostępne dla maszyn z kopią zapasową z systemem Windows Server 2008 R2, Windows Server 2008 z dodatkiem SP2 lub Windows 7.

## <a name="supported-operating-systems"></a>Obsługiwane systemy operacyjne

>[!NOTE]
> Agent MARS nie obsługuje podstawowych jednostek SKU systemu Windows Server.

Agenta MARS można użyć do utworzenia kopii zapasowej bezpośrednio na platformie Azure w systemach operacyjnych wymienionych poniżej, które są uruchamiane na:

1. Lokalne serwery z systemem Windows
2. Maszyny wirtualne platformy Azure z systemem Windows

Systemy operacyjne muszą być 64 bitowe i powinny mieć zainstalowane najnowsze pakiety i aktualizacje usług. W poniższej tabeli zestawiono te systemy operacyjne:

**System operacyjny** | **Pliki/foldery** | **Stan systemu** | **Wymagania dotyczące oprogramowania/modułu**
--- | --- | --- | ---
Windows 10 (Enterprise, Pro, Home) | Yes | Nie |  Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows 8.1 (Enterprise, Pro)| Yes |Nie | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows 8 (Enterprise, Pro) | Yes | Nie | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu
Windows Server 2016 (wersje Standard, Datacenter, Essentials) | Yes | Yes | — .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0
Windows Server 2012 R2 (wersje Standard, Datacenter, Foundation, Essentials) | Yes | Yes | — .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0
Windows Server 2012 (wersje Standard, Datacenter, Foundation) | Yes | Yes |— .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0 <br> — Obsługa i zarządzanie obrazami wdrażania (DISM.exe)
Windows Storage Server 2016/2012 R2/2012 (standard, Grupa robocza) | Yes | Nie | — .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0
Windows Server 2019 (wersje Standard, Datacenter, Essentials) | Yes | Yes | — .NET 4,5 <br> — Windows PowerShell <br> -Najnowsza zgodna z pakietem redystrybucyjnym Microsoft VC + + <br> — Program Microsoft Management Console (MMC) 3,0

Aby uzyskać więcej informacji, zobacz [obsługiwane systemy operacyjne serwera usługi MAB i DPM](backup-support-matrix-mabs-dpm.md#supported-mabs-and-dpm-operating-systems).

### <a name="operating-systems-at-end-of-support"></a>Systemy operacyjne na końcu wsparcia

Następujące systemy operacyjne mają na koniec wsparcie i zdecydowanie zaleca się uaktualnienie systemu operacyjnego w celu kontynuowania ochrony.

Jeśli istniejące zobowiązania uniemożliwiają uaktualnienie systemu operacyjnego, należy rozważyć Migrowanie serwerów z systemem Windows do maszyn wirtualnych platformy Azure i korzystanie z kopii zapasowych maszyn wirtualnych platformy Azure w celu kontynuowania ochrony. Odwiedź [stronę migracji tutaj](https://azure.microsoft.com/migration/windows-server/) , aby uzyskać więcej informacji na temat migrowania systemu Windows Server.

W przypadku środowisk lokalnych lub hostowanych, w których nie można uaktualnić systemu operacyjnego lub migracji na platformę Azure, Aktywuj rozszerzone aktualizacje zabezpieczeń dla maszyn, aby nadal były chronione i obsługiwane. Należy zauważyć, że tylko określone wersje kwalifikują się do aktualizacji zabezpieczeń rozszerzonych. Odwiedź [stronę często zadawanych pytań](https://www.microsoft.com/windows-server/extended-security-updates) , aby dowiedzieć się więcej.

| **System operacyjny**                                       | **Pliki/foldery** | **Stan systemu** | **Wymagania dotyczące oprogramowania/modułu**                           |
| ------------------------------------------------------------ | ----------------- | ------------------ | ------------------------------------------------------------ |
| Windows 7 (Ultimate, Enterprise, Pro, Home Premium/Basic, Starter) | Yes               | Nie                 | Sprawdź odpowiednią wersję serwera pod kątem wymagań dotyczących oprogramowania/modułu |
| Windows Server 2008 R2 (wersje Standard, Enterprise, Datacenter, Foundation) | Yes               | Yes                | — .NET 3,5, .NET 4,5 <br>  — Windows PowerShell <br>  -Zgodna z pakietem redystrybucyjnym Microsoft VC + + <br>  — Program Microsoft Management Console (MMC) 3,0 <br>  — Obsługa i zarządzanie obrazami wdrażania (DISM.exe) |
| Windows Server 2008 z dodatkiem SP2 (wersje Standard, Datacenter, Foundation)  | Yes               | Nie                 | — .NET 3,5, .NET 4,5 <br>  — Windows PowerShell <br>  -Zgodna z pakietem redystrybucyjnym Microsoft VC + + <br>  — Program Microsoft Management Console (MMC) 3,0 <br>  — Obsługa i zarządzanie obrazami wdrażania (DISM.exe) <br>  -Virtual Server 2005 Base + KB KB948515 |

## <a name="backup-limits"></a>Limity kopii zapasowych

### <a name="size-limits"></a>Limity rozmiaru

Azure Backup ogranicza rozmiar źródła danych pliku lub folderu, dla którego można utworzyć kopię zapasową. Elementy, których kopię zapasową utworzono z pojedynczego woluminu, nie mogą przekroczyć rozmiarów podsumowanych w tej tabeli:

**System operacyjny** | **Limit rozmiaru**
--- | ---
Windows Server 2012 lub nowszy |54 400 GB
Windows Server 2008 R2 SP1 |1 700 GB
Windows Server 2008 SP2| 1 700 GB
Windows 8 lub nowszy| 54 400 GB
Windows 7| 1 700 GB

### <a name="other-limitations"></a>Inne ograniczenia

- Usługa MARS nie obsługuje ochrony wielu maszyn o tej samej nazwie w pojedynczym magazynie.

## <a name="supported-file-types-for-backup"></a>Obsługiwane typy plików dla kopii zapasowej

**Typ** | **Pomoc techniczna**
--- | ---
Szyfrowane<sup>*</sup>| Obsługiwane.
Skompresowane | Obsługiwane.
Rozrzedzone | Obsługiwane.
Skompresowane i rozrzedzone |Obsługiwane.
Twarde linki| Nieobsługiwane. Pominięto.
Punkt ponownej analizy| Nieobsługiwane. Pominięto.
Zaszyfrowane i rozrzedzone |Nieobsługiwane. Pominięto.
Strumień skompresowany| Nieobsługiwane. Pominięto.
Strumień rozrzedzony| Nieobsługiwane. Pominięto.
OneDrive (synchronizowane pliki to strumienie rozrzedzone)| Nieobsługiwane.
Foldery z włączonym Replikacja systemu plików DFS | Nieobsługiwane.

\* Upewnij się, że Agent MARS ma dostęp do wymaganych certyfikatów, aby uzyskać dostęp do zaszyfrowanych plików. Niedostępne pliki zostaną pominięte.

## <a name="supported-drives-or-volumes-for-backup"></a>Obsługiwane dyski lub woluminy na potrzeby tworzenia kopii zapasowych

**Dysk/wolumin** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
Woluminy tylko do odczytu| Nieobsługiwane | Usługa kopiowania woluminów w tle (VSS) działa tylko wtedy, gdy wolumin jest zapisywalny.
Woluminy offline| Nieobsługiwane |Usługa VSS działa tylko wtedy, gdy wolumin jest w trybie online.
Udział sieciowy| Nieobsługiwane |Wolumin musi być lokalny na serwerze.
Woluminy zablokowane przez funkcję BitLocker| Nieobsługiwane |Wolumin musi zostać odblokowany przed rozpoczęciem tworzenia kopii zapasowej.
Identyfikacja systemu plików| Nieobsługiwane |Obsługiwany jest tylko system plików NTFS.
Nośnik wymienny| Nieobsługiwane |Wszystkie źródła elementów kopii zapasowej muszą mieć *ustalony* stan.
Deduplikowane dyski | Obsługiwane | Azure Backup konwertuje deduplikowane dane na normalne dane. Optymalizuje, szyfruje, przechowuje i wysyła dane do magazynu.

## <a name="support-for-initial-offline-backup"></a>Obsługa początkowej kopii zapasowej offline

Azure Backup obsługuje umieszczanie *w trybie offline* w celu transferu danych początkowej kopii zapasowej na platformę Azure przy użyciu dysków. Ta pomoc techniczna jest przydatna, jeśli początkowa kopia zapasowa prawdopodobnie będzie znajdować się w zakresie od terabajtów (TBs). Kopia zapasowa offline jest obsługiwana dla:

- Bezpośrednie tworzenie kopii zapasowych plików i folderów na maszynach lokalnych, na których jest uruchomiony agent MARS.
- Tworzenie kopii zapasowych obciążeń i plików z serwera DPM lub serwera usługi MAB.

Kopii zapasowej offline nie można używać dla plików stanu systemu.

## <a name="support-for-data-restoration"></a>Obsługa przywracania danych

Za pomocą funkcji [natychmiastowego przywracania](backup-instant-restore-capability.md) Azure Backup można przywrócić dane przed ich skopiowaniem do magazynu. Maszyna, której kopia zapasowa jest wykonywana, musi działać .NET Framework 4.5.2 lub nowszy.

Kopie zapasowe nie mogą zostać przywrócone na komputerze docelowym, na którym działa Starsza wersja systemu operacyjnego. Na przykład kopie zapasowe wykonane z komputera z systemem Windows 7 można przywrócić w systemie Windows 8 lub nowszym. Ale nie można przywrócić kopii zapasowej wykonanej z komputera z systemem Windows 8 na komputerze z systemem Windows 7.

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [architekturze tworzenia kopii zapasowych korzystającej z agenta Mars](backup-architecture.md#architecture-direct-backup-of-on-premises-windows-server-machines-or-azure-vm-files-or-folders).
- Dowiedz się, co jest obsługiwane podczas [uruchamiania agenta Mars na serwera usługi MAB lub serwerze DPM](backup-support-matrix-mabs-dpm.md).