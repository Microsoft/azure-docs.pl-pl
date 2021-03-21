---
title: Macierz obsługi programu serwera usługi MAB & System Center DPM
description: Ten artykuł zawiera podsumowanie Azure Backup pomocy technicznej w przypadku używania serwera Microsoft Azure Backup (serwera usługi MAB) lub programu System Center DPM do tworzenia kopii zapasowych zasobów lokalnych i maszyn wirtualnych platformy Azure.
ms.date: 02/17/2019
ms.topic: conceptual
ms.openlocfilehash: e888b43ea5641f1943a096f045747d547c52fcfa
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102609757"
---
# <a name="support-matrix-for-backup-with-microsoft-azure-backup-server-or-system-center-dpm"></a>Macierz obsługi dla tworzenia kopii zapasowych za pomocą serwera Microsoft Azure Backup lub programu System Center DPM

Za pomocą [usługi Azure Backup](backup-overview.md) można tworzyć kopie zapasowe maszyn i obciążeń lokalnych oraz maszyn wirtualnych platformy Azure. Ten artykuł podsumowuje ustawienia i ograniczenia dotyczące tworzenia kopii zapasowych maszyn przy użyciu Microsoft Azure Backup Server (serwera usługi MAB) lub System Center Data Protection Manager (DPM) i Azure Backup.

## <a name="about-dpmmabs"></a>Informacje o programie DPM/serwera usługi MAB

[Program System Center DPM](/system-center/dpm/dpm-overview) to rozwiązanie korporacyjne służące do konfigurowania i wykonywania kopii zapasowych i odzyskiwania maszyn i danych przedsiębiorstwa oraz zarządzania nimi. Jest częścią pakietu produktów [System Center](https://www.microsoft.com/system-center/pricing) .

SERWERA usługi MAB to produkt serwerowy, który może służyć do tworzenia kopii zapasowych lokalnych serwerów fizycznych, maszyn wirtualnych i działających na nich aplikacji.

SERWERA usługi MAB jest oparta na programie System Center DPM i oferuje podobną funkcjonalność z kilkoma różnicami:

- Do uruchomienia serwera usługi MAB nie jest wymagana żadna licencja programu System Center.
- W przypadku programów serwera usługi MAB i DPM platforma Azure zapewnia długoterminowe przechowywanie kopii zapasowych. Ponadto program DPM umożliwia tworzenie kopii zapasowych danych w celu przechowywania długoterminowego na taśmie. SERWERA usługi MAB nie zapewnia tej funkcji.
- [Można utworzyć kopię zapasową podstawowego serwera DPM przy użyciu pomocniczego serwera programu DPM](/system-center/dpm/back-up-the-dpm-server). Serwer pomocniczy będzie chronić bazę danych serwera podstawowego i repliki źródła danych przechowywane na serwerze podstawowym. W razie awarii serwera podstawowego serwer pomocniczy może nadal chronić obciążenia chronione przez serwer podstawowy do czasu, aż serwer podstawowy stanie się ponownie dostępny.  SERWERA usługi MAB nie zapewnia tej funkcji.

Pobierasz serwera usługi MAB z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=57520). Może być uruchamiany lokalnie lub na maszynie wirtualnej platformy Azure.

Program DPM i serwera usługi MAB obsługują tworzenie kopii zapasowych różnorodnych aplikacji oraz serwerów i systemów operacyjnych klienta. Zapewniają one wiele scenariuszy tworzenia kopii zapasowych:

- Można utworzyć kopię zapasową na poziomie komputera przy użyciu kopii zapasowej stanu systemu lub bezsystemowego.
- Można tworzyć kopie zapasowe określonych woluminów, udziałów, folderów i plików.
- Można tworzyć kopie zapasowe określonych aplikacji przy użyciu zoptymalizowanych ustawień aplikacji.

## <a name="dpmmabs-backup"></a>Kopia zapasowa programu DPM/serwera usługi MAB

Tworzenie kopii zapasowej przy użyciu programu DPM/serwera usługi MAB i Azure Backup działa następująco:

1. Na każdym komputerze, na którym zostanie utworzona kopia zapasowa, jest zainstalowany agent ochrony programu DPM/serwera usługi MAB.
1. Kopie zapasowe maszyn i aplikacji są przechowywane w magazynie lokalnym w programie DPM/serwera usługi MAB.
1. Agent Microsoft Azure Recovery Services (MARS) jest zainstalowany na serwerze DPM/serwera usługi MAB.
1. Agent MARS tworzy kopię zapasową dysków DPM/serwera usługi MAB w magazynie kopii zapasowych Recovery Services na platformie Azure przy użyciu Azure Backup.

Więcej informacji:

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze serwera usługi MAB.
- [Sprawdź, co jest obsługiwane](backup-support-matrix-mars-agent.md) przez agenta Mars.

## <a name="supported-scenarios"></a>Obsługiwane scenariusze

**Scenariusz** | **Odczynnik** | **Lokalizacja**
--- | --- | ---
**Tworzenie kopii zapasowych maszyn/obciążeń lokalnych** | Na maszynach, dla których chcesz utworzyć kopię zapasową, działa Agent ochrony programu DPM/serwera usługi MAB.<br/><br/> Agent MARS na serwerze DPM/serwera usługi MAB.<br/> Minimalna wersja agenta Microsoft Azure Recovery Services lub agenta Azure Backup, wymagana do włączenia tej funkcji, to 2.0.8719.0.  | Program DPM/serwera usługi MAB musi działać lokalnie.

## <a name="supported-deployments"></a>Obsługiwane wdrożenia

Program DPM/serwera usługi MAB można wdrożyć zgodnie z podsumowaniem w poniższej tabeli.

**Wdrożenie** | **Pomoc techniczna** | **Szczegóły**
--- | --- | ---
**Wdrożone lokalnie** | Serwer fizyczny, ale nie w klastrze fizycznym.<br/><br/>Maszyna wirtualna funkcji Hyper-V. SERWERA usługi MAB jako maszynę gościa można wdrożyć w autonomicznej funkcji hypervisor lub w klastrze. Nie można go wdrożyć w węźle klastra lub autonomicznej funkcji hypervisor. Azure Backup Server jest przeznaczony do działania na dedykowanym, wyznaczonym do tego celu serwerze.<br/><br/> Jako maszynę wirtualną systemu Windows w środowisku VMware. | Lokalne serwery serwera usługi MAB nie mogą chronić obciążeń opartych na platformie Azure. <br><br> Aby uzyskać więcej informacji, zobacz [macierz ochrony](backup-mabs-protection-matrix.md).
**Wdrożono jako maszynę wirtualną Azure Stack** | Tylko serwera usługi MAB | Programu DPM nie można używać do tworzenia kopii zapasowych maszyn wirtualnych Azure Stack.
**Wdrożono jako maszynę wirtualną platformy Azure** | Ochrona maszyn wirtualnych platformy Azure i obciążeń uruchomionych na tych maszynach wirtualnych | W programie DPM/serwera usługi MAB działającym na platformie Azure nie można tworzyć kopii zapasowych maszyn lokalnych. Może chronić tylko obciążenia działające na maszynach wirtualnych usługi Azure IaaS.

## <a name="supported-mabs-and-dpm-operating-systems"></a>Obsługiwane systemy operacyjne serwera usługi MAB i DPM

Azure Backup może tworzyć kopie zapasowe wystąpień programu DPM/serwera usługi MAB, które działają w dowolnym z następujących systemów operacyjnych. Systemy operacyjne powinny mieć zainstalowane najnowsze dodatki Service Pack i aktualizacje.

**Scenariusz** | **DPM/SERWERA USŁUGI MAB**
--- | ---
**SERWERA usługi MAB na maszynie wirtualnej platformy Azure** |  Centrum danych systemu Windows 2016.<br/><br/> Centrum danych systemu Windows 2019.<br/><br/> Zalecamy rozpoczęcie od obrazu z portalu Marketplace.<br/><br/> Minimum Standard_A4_v2 z czterema rdzeniami i 8 GB pamięci RAM.
**Program DPM na maszynie wirtualnej platformy Azure** | System Center 2012 R2 z aktualizacją Update 3 lub nowszą.<br/><br/> System operacyjny Windows [wymagany przez program System Center](/system-center/dpm/prepare-environment-for-dpm#dpm-server).<br/><br/> Zalecamy rozpoczęcie od obrazu z portalu Marketplace.<br/><br/> Minimum Standard_A4_v2 z czterema rdzeniami i 8 GB pamięci RAM.
**SERWERA usługi MAB lokalnie** |  SERWERA usługi MAB v3 i nowsze: Windows Server 2016 lub Windows Server 2019
**Lokalna DPM** | Serwer fizyczny/maszyna wirtualna funkcji Hyper-V: System Center 2012 z dodatkiem SP1 lub nowszy.<br/><br/> Maszyna wirtualna VMware: System Center 2012 R2 z aktualizacją Update 5 lub nowszą.

>[!NOTE]
>Instalowanie Azure Backup Server nie jest obsługiwane w systemie Windows Server Core lub Microsoft Hyper-V Server.

## <a name="management-support"></a>Obsługa zarządzania

**Wykonaj** | **Szczegóły**
--- | ---
**Instalacja** | Zainstaluj program DPM/serwera usługi MAB na maszynie z pojedynczym przeznaczeniem.<br/><br/> Nie należy instalować programu DPM/serwera usługi MAB na kontrolerze domeny na komputerze z instalacją roli serwera aplikacji, na komputerze z uruchomionym programem Microsoft Exchange Server lub System Center Operations Manager lub w węźle klastra.<br/><br/> Zapoznaj się ze [wszystkimi wymaganiami systemowymi programu DPM](/system-center/dpm/prepare-environment-for-dpm#dpm-server).
**Domeny** | Program DPM/serwera usługi MAB powinien być przyłączony do domeny. Najpierw zainstaluj program DPM/serwera usługi MAB do domeny. Przeniesienie programu DPM/serwera usługi MAB do nowej domeny po wdrożeniu nie jest obsługiwane.
**Storage** | Nowoczesne magazyny kopii zapasowych (MB) jest obsługiwane przez program DPM 2016/serwera usługi MAB v2 i nowsze. Nie jest on dostępny dla serwera usługi MAB v1.
**SERWERA usługi MAB uaktualnienie** | Można bezpośrednio zainstalować serwera usługi MAB v3 lub uaktualnić do serwera usługi MAB v3 z wersji serwera usługi MAB v2. [Dowiedz się więcej](backup-azure-microsoft-azure-backup.md#upgrade-mabs).
**Przesuwanie serwera usługi MAB** | Przeniesienie serwera usługi MAB na nowy serwer podczas zachowywania magazynu jest obsługiwane, jeśli używasz MB.<br/><br/> Serwer musi mieć taką samą nazwę jak oryginalna. Nie można zmienić nazwy, jeśli chcesz zachować tę samą pulę magazynów i użyć tej samej bazy danych serwera usługi MAB do przechowywania punktów odzyskiwania danych.<br/><br/> Konieczna będzie kopia zapasowa bazy danych serwera usługi MAB, ponieważ należy ją przywrócić.

>[!NOTE]
>Zmiana nazwy serwera DPM/serwera usługi MAB nie jest obsługiwana.

## <a name="mabs-support-on-azure-stack"></a>Obsługa serwera usługi MAB w Azure Stack

Można wdrożyć serwera usługi MAB na maszynie wirtualnej Azure Stack, aby można było zarządzać tworzeniem kopii zapasowych Azure Stack maszyn wirtualnych i obciążeń z jednej lokalizacji.

**Składnik** | **Szczegóły**
--- | ---
**SERWERA usługi MAB na maszynie wirtualnej Azure Stack** | Co najmniej rozmiar a2. Zalecamy rozpoczęcie od obrazu systemu Windows Server 2012 R2 lub Windows Server 2016 z witryny Azure Marketplace.<br/><br/> Nie należy instalować żadnych innych elementów na maszynie wirtualnej serwera usługi MAB.
**Magazyn serwera usługi MAB** | Użyj oddzielnego konta magazynu dla maszyny wirtualnej serwera usługi MAB. Agent MARS uruchomiony w systemie serwera usługi MAB potrzebuje magazynu tymczasowego dla lokalizacji pamięci podręcznej oraz do przechowywania danych przywróconych z chmury.
**Pula magazynu serwera usługi MAB** | Rozmiar puli magazynów serwera usługi MAB zależy od liczby i rozmiaru dysków dołączonych do maszyny wirtualnej serwera usługi MAB. Każdy Azure Stack rozmiaru maszyny wirtualnej ma maksymalną liczbę dysków. Na przykład a2 to cztery dyski.
**Przechowywanie serwera usługi MAB** | Nie przechowuj kopii zapasowych danych na lokalnych dyskach serwera usługi MAB przez ponad pięć dni.
**Skalowanie w górę serwera usługi MAB** | Aby skalować wdrożenie w górę, można zwiększyć rozmiar maszyny wirtualnej serwera usługi MAB. Na przykład można zmienić z serii do D.<br/><br/> Możesz również upewnić się, że dane z kopii zapasowej są wykonywane regularnie na platformie Azure. W razie potrzeby można wdrożyć dodatkowe serwery serwera usługi MAB.
**.NET Framework w serwera usługi MAB** | Maszyna wirtualna serwera usługi MAB wymaga .NET Framework 3,3 z dodatkiem SP1 lub nowszą.
**SERWERA usługi MAB domenę** | Maszyna wirtualna serwera usługi MAB musi być przyłączona do domeny. Użytkownik domeny z uprawnieniami administratora musi zainstalować serwera usługi MAB na maszynie wirtualnej.
**Azure Stack kopii zapasowej danych maszyny wirtualnej** | Można tworzyć kopie zapasowe plików, folderów i aplikacji.
**Obsługiwana kopia zapasowa** | Te systemy operacyjne są obsługiwane w przypadku maszyn wirtualnych, których kopia zapasowa ma zostać utworzona:<br/><br/> Windows Server Semi-Annual Channel (Datacenter, Enterprise, standard)<br/><br/> Windows Server 2016, Windows Server 2012 R2, Windows Server 2008 R2
**SQL Server obsługa Azure Stack maszyn wirtualnych** | Wykonaj kopię zapasową SQL Server 2016, SQL Server 2014 SQL Server 2012 SP1.<br/><br/> Tworzenie kopii zapasowej i odzyskiwanie bazy danych.
**Obsługa programu SharePoint na potrzeby maszyn wirtualnych Azure Stack** | SharePoint 2016, SharePoint 2013, SharePoint 2010.<br/><br/> Tworzenie kopii zapasowej i odzyskiwanie farmy, bazy danych, frontonu i serwera sieci Web.
**Wymagania sieciowe dotyczące kopii zapasowych maszyn wirtualnych** | Wszystkie maszyny wirtualne w obciążeniu Azure Stack muszą należeć do tej samej sieci wirtualnej i należeć do tej samej subskrypcji.

## <a name="dpmmabs-networking-support"></a>Obsługa sieci programu DPM/serwera usługi MAB

### <a name="url-access"></a>Dostęp do adresu URL

Serwer programu DPM/serwer serwera usługi MAB musi mieć dostęp do tych adresów URL i adresów IP:

* Adresy URL
  * `www.msftncsi.com`
  * `*.Microsoft.com`
  * `*.WindowsAzure.com`
  * `*.microsoftonline.com`
  * `*.windows.net`
  * `www.msftconnecttest.com`
* Adresy IP
  * 20.190.128.0/18
  * 40.126.0.0/18:

### <a name="azure-expressroute-support"></a>Pomoc techniczna platformy Azure ExpressRoute

Możesz tworzyć kopie zapasowe danych za pośrednictwem usługi Azure ExpressRoute za pomocą publicznej komunikacji równorzędnej (dostępne dla starych obwodów) i komunikacji równorzędnej firmy Microsoft. Tworzenie kopii zapasowej za pośrednictwem prywatnej komunikacji równorzędnej nie jest obsługiwane.

Przy użyciu publicznej komunikacji równorzędnej: Upewnij się, że dostęp do następujących domen/adresów:

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

W przypadku komunikacji równorzędnej firmy Microsoft wybierz następujące usługi/regiony i odpowiednie wartości społeczności:

- Azure Active Directory (12076:5060)
- Region Microsoft Azure (zgodnie z lokalizacją magazynu Recovery Services)
- Azure Storage (zgodnie z lokalizacją magazynu Recovery Services)

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące routingu ExpressRoute](../expressroute/expressroute-routing.md).

>[!NOTE]
>Publiczna Komunikacja równorzędna jest przestarzała dla nowych obwodów.

### <a name="dpmmabs-connectivity-to-azure-backup"></a>Połączenie programu DPM/serwera usługi MAB z usługą Azure Backup

Aby kopie zapasowe działały prawidłowo, wymagana jest łączność z usługą Azure Backup i subskrypcja platformy Azure powinna być aktywna. W poniższej tabeli przedstawiono zachowanie, jeśli te dwa kwestie nie wystąpią.

**SERWERA usługi MAB na platformę Azure** | **Subskrypcja** | **Tworzenie kopii zapasowej/przywracanie**
--- | --- | ---
Połączone | Aktywna | Utwórz kopię zapasową na dysku programu DPM/serwera usługi MAB.<br/><br/> Utwórz kopię zapasową na platformie Azure.<br/><br/> Przywróć z dysku.<br/><br/> Przywróć z platformy Azure.
Połączone | Wygasłe lub anulowano obsługę administracyjną | Brak kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja wygasła, możesz przywrócić ją z dysku lub platformy Azure.<br/><br/> Jeśli subskrypcja została zlikwidowana, nie można jej przywrócić z dysku lub platformy Azure. Punkty odzyskiwania platformy Azure są usuwane.
Brak łączności przez ponad 15 dni | Aktywna | Brak kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Można przywrócić z dysku lub platformy Azure.
Brak łączności przez ponad 15 dni | Wygasłe lub anulowano obsługę administracyjną | Brak kopii zapasowej na dysku lub na platformie Azure.<br/><br/> Jeśli subskrypcja wygasła, możesz przywrócić ją z dysku lub platformy Azure.<br/><br/> Jeśli subskrypcja została zlikwidowana, nie można jej przywrócić z dysku lub platformy Azure. Punkty odzyskiwania platformy Azure są usuwane.

## <a name="domain-and-domain-trusts-support"></a>Obsługa relacji zaufania domen i domen

|Wymaganie |Szczegóły |
|---------|---------|
|Domena    | Serwer DPM/serwera usługi MAB powinien znajdować się w domenie systemu Windows Server 2019, Windows Server 2016, Windows Server 2012 R2, Windows Server 2012.        |
|Zaufanie domeny   |  Program DPM/serwera usługi MAB obsługuje ochronę danych w różnych lasach, pod warunkiem, że ustanawiane jest zaufanie dwukierunkowe między oddzielnymi lasami.   <BR><BR>   Program DPM/serwera usługi MAB może chronić serwery i stacje robocze w domenach w lesie, który ma ustanowioną dwukierunkową relację zaufania z domeną serwera DPM/serwera usługi MAB. Aby chronić komputery w grupach roboczych lub domenach niezaufanych, zobacz [wykonywanie kopii zapasowych i przywracanie obciążeń w grupach roboczych i domenach niezaufanych.](/system-center/dpm/back-up-machines-in-workgroups-and-untrusted-domains) <br><br> Aby utworzyć kopię zapasową klastrów serwerów funkcji Hyper-V, muszą one znajdować się w tej samej domenie co serwer serwera usługi MAB lub w domenie zaufanej lub podrzędnej. Można wykonać kopię zapasową obciążeń, serwerów i klastrów w niezaufanej domenie z uwierzytelnianiem przy użyciu protokołu NTLM lub certyfikatu dla jednego serwera albo z uwierzytelnianiem tylko przy użyciu certyfikatu dla klastra.  |

## <a name="dpmmabs-storage-support"></a>Obsługa magazynu programu DPM/serwera usługi MAB

Dane, których kopię zapasową utworzono w programie DPM/serwera usługi MAB, są przechowywane na dysku lokalnym.

USB lub dyski wymienne nie są obsługiwane.

Kompresja NTFS nie jest obsługiwana na woluminach programu DPM/serwera usługi MAB.

Funkcję BitLocker można włączyć tylko po dodaniu dysku do puli magazynów. Nie włączaj funkcji BitLocker przed dodaniem jej.

Magazyn dołączony do sieci (NAS) nie jest obsługiwany do użycia w puli magazynów programu DPM.

**Storage** | **Szczegóły**
--- | ---
**MB** | Nowoczesne magazyny kopii zapasowych (MB) jest obsługiwane przez program DPM 2016/serwera usługi MAB v2 i nowsze. Nie jest on dostępny dla serwera usługi MAB v1.
**SERWERA usługi MAB magazynu na maszynie wirtualnej platformy Azure** | Dane są przechowywane na dyskach platformy Azure dołączonych do maszyny wirtualnej programu DPM/serwera usługi MAB, które są zarządzane w programie DPM/serwera usługi MAB. Liczba dysków, które mogą być używane dla puli magazynów programu DPM/serwera usługi MAB, jest ograniczona przez rozmiar maszyny wirtualnej.<br/><br/> Maszyna wirtualna a2:4 dyski; Maszyna wirtualna A3:8 dysków; Maszyna wirtualna A4:16 dysków o maksymalnym rozmiarze 1 TB dla każdego dysku. Ta wartość określa łączną pulę magazynów kopii zapasowych, która jest dostępna.<br/><br/> Ilość danych, dla których można utworzyć kopię zapasową, zależy od liczby i rozmiaru dołączonych dysków.
**Przechowywanie danych serwera usługi MAB na maszynie wirtualnej platformy Azure** | Zalecamy zachowywanie danych przez jeden dzień na dysku platformy Azure DPM/serwera usługi MAB i wykonywanie kopii zapasowych z poziomu programu DPM/serwera usługi MAB do magazynu w celu dłuższego przechowywania. W ten sposób można chronić większą ilość danych, odciążając ją do Azure Backup.

### <a name="modern-backup-storage-mbs"></a>Nowoczesny magazyn kopii zapasowych (MB)

W programie DPM 2016/serwera usługi MAB v2 (uruchomionym w systemie Windows Server 2016) lub nowszym można korzystać z nowoczesnego magazynu kopii zapasowych (MB).

- Kopie zapasowe MB są przechowywane na dysku systemu plików ReFS.
- MB używa klonowania bloków systemu plików ReFS do szybszego tworzenia kopii zapasowych i wydajniejszego wykorzystywania miejsca do magazynowania.
- Podczas dodawania woluminów do lokalnej puli magazynów programu DPM/serwera usługi MAB należy je skonfigurować przy użyciu liter dysków. Następnie można skonfigurować magazyn obciążeń na różnych woluminach.
- Podczas tworzenia grup ochrony w celu wykonywania kopii zapasowych danych w programie DPM/serwera usługi MAB wybierz dysk, którego chcesz użyć. Można na przykład przechowywać kopie zapasowe dla programu SQL lub innych obciążeń o dużej liczbie operacji we/wy na dysku o wysokiej wydajności, a także przechowywać obciążenia, których kopie zapasowe są rzadziej wykonywane na niższym dysku wydajności.

## <a name="supported-backups-to-mabs"></a>Obsługiwane kopie zapasowe do serwera usługi MAB

Aby uzyskać informacje na temat różnych serwerów i obciążeń, które można chronić za pomocą Azure Backup Server, zapoznaj się z [macierzą ochrony Azure Backup Server](./backup-mabs-protection-matrix.md#protection-support-matrix).

## <a name="supported-backups-to-dpm"></a>Obsługiwane kopie zapasowe w programie DPM

Aby uzyskać informacje na temat różnych serwerów i obciążeń, które można chronić za pomocą Data Protection Manager, zapoznaj się z artykułem [co można wykonać w programie DPM?](/system-center/dpm/dpm-protection-matrix).

- Klastrowane obciążenia tworzone w ramach programu DPM/serwera usługi MAB powinny znajdować się w tej samej domenie co program DPM/serwera usługi MAB lub w domenie podrzędnej/zaufanej.
- Przy użyciu uwierzytelniania NTLM/Certificate można tworzyć kopie zapasowe danych w niezaufanych domenach lub grupach roboczych.

## <a name="deduplicated-volumes-support"></a>Obsługa woluminów z deduplikacją

>[!NOTE]
> Obsługa deduplikacji dla usługi serwera usługi MAB zależy od obsługi systemu operacyjnego.

### <a name="for-ntfs-volumes"></a>Dla woluminów NTFS

| System operacyjny serwera chronionego  | System operacyjny serwera serwera usługi MAB  | Wersja serwera usługi MAB  | Obsługa deduplikacji |
| ------------------------------------------ | ------------------------------------- | ------------------ | -------------------- |
| System Windows Server 2019                       | System Windows Server 2019                  | SERWERA usługi MAB v3            | Y                    |
| System Windows Server 2016                       | System Windows Server 2019                  | SERWERA usługi MAB v3            | T                   |
| System Windows Server 2012 R2                    | System Windows Server 2019                  | SERWERA usługi MAB v3            | N                    |
| System Windows Server 2012                       | System Windows Server 2019                  | SERWERA usługi MAB v3            | N                    |
| System Windows Server 2019                       | System Windows Server 2016                  | SERWERA usługi MAB v3            | Y * *                  |
| System Windows Server 2016                       | System Windows Server 2016                  | SERWERA usługi MAB v3            | Y                    |
| System Windows Server 2012 R2                    | System Windows Server 2016                  | SERWERA usługi MAB v3            | Y                    |
| System Windows Server 2012                       | System Windows Server 2016                  | SERWERA usługi MAB v3            | Y                    |

- \* W przypadku ochrony woluminu z deduplikacją systemu plików NTFS 2016 z serwera usługi MAB v3 uruchomionym w usłudze WS 2019 może to wpłynąć na odzyskiwanie. Mamy poprawkę dotyczącą operacji odzyskiwania w sposób nieduplikowany. Skontaktuj się z pomocą techniczną serwera usługi MAB, jeśli potrzebujesz tej poprawki na serwera usługi MAB v3 UR1.
- \** W przypadku ochrony woluminu z deduplikacją systemu plików NTFS 2019 z serwera usługi MAB V3 w WS 2016, kopie zapasowe i przywracanie nie będą deduplikowane. Oznacza to, że kopie zapasowe będą zużywać więcej miejsca na serwerze serwera usługi MAB niż oryginalny wolumin NTFS z deduplikacją.

**Problem**: w przypadku uaktualniania chronionego serwera systemu operacyjnego z systemu windows Server 2016 do systemu windows Server 2019, będzie to miało oddziaływać na kopię zapasową woluminu z deduplikacją NTFS.

**Obejście**: skontaktuj się z pomocą techniczną usługi serwera usługi MAB w przypadku, gdy potrzebna jest ta poprawka dla serwera usługi MAB v3 UR1.

### <a name="for-refs-volumes"></a>Woluminy systemu plików ReFS

>[!NOTE]
> Zidentyfikowano kilka problemów z kopiami zapasowymi deduplikowanych woluminów ReFS. Pracujemy nad naprawianiem tych elementów i zaktualizujemy tę sekcję zaraz po udostępnieniu poprawki. Do tego czasu usuwamy obsługę kopii zapasowych deduplikowanych woluminów ReFS z serwera usługi MAB v3.
>
> SERWERA usługi MAB v3 UR1 i nowsze wersje nadal obsługują ochronę i odzyskiwanie normalnych woluminów ReFS.

## <a name="next-steps"></a>Następne kroki

- [Dowiedz się więcej](backup-architecture.md#architecture-back-up-to-dpmmabs) o architekturze serwera usługi MAB.
- [Sprawdź](backup-support-matrix-mars-agent.md) , co jest obsługiwane przez agenta Mars.
- [Skonfiguruj](backup-azure-microsoft-azure-backup.md) serwer serwera usługi MAB.
- [Skonfiguruj program DPM](/system-center/dpm/install-dpm).
