---
title: Co nowego w usłudze Microsoft Azure Backup Server
description: Serwer Microsoft Azure Backup zapewnia ulepszone możliwości tworzenia kopii zapasowych w celu ochrony maszyn wirtualnych, plików i folderów, obciążeń i nie tylko.
ms.topic: conceptual
ms.date: 05/24/2020
ms.openlocfilehash: 39050d0f658e29b82f270f1fe53026e2fb80bfa1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91332767"
---
# <a name="whats-new-in-microsoft-azure-backup-server-mabs"></a>Co nowego w programie Microsoft Azure Backup Server (serwera usługi MAB)

## <a name="whats-new-in-mabs-v3-ur1"></a>Co nowego w programie serwera usługi MAB v3 UR1

Serwer Microsoft Azure Backup (serwera usługi MAB) w wersji 3 UR1 to najnowsza aktualizacja i zawiera krytyczne poprawki błędów oraz inne funkcje i ulepszenia. Aby wyświetlić listę poprawionych błędów i instrukcje dotyczące instalacji programu serwera usługi MAB v3 UR1, zobacz artykuł KB [4534062](https://support.microsoft.com/help/4534062).

>[!NOTE]
>Obsługa agenta ochrony 32-bitowego jest przestarzała z serwera usługi MAB v3 UR1. Zobacz [wycofanie agenta ochrony 32 bitowego](#32-bit-protection-agent-deprecation).

### <a name="faster-backups-with-tiered-storage-using-ssds"></a>Szybsze tworzenie kopii zapasowych przy użyciu magazynu warstwowego za pomocą dysków SSD

SERWERA usługi MAB v2 wprowadził [nowoczesny magazyn kopii zapasowych](backup-mabs-add-storage.md) (MB), zwiększając wykorzystanie magazynu i wydajność. MB korzysta z systemu plików ReFS jako bazowego i jest przeznaczony do korzystania z magazynu hybrydowego, takiego jak magazyn warstwowy.

Aby osiągnąć skalę i wydajność, MB zalecamy użycie małego procentu (4% całkowitego magazynu) w usłudze Flash Storage (SSD) z serwera usługi MAB v3 UR1 jako woluminu warstwowego w połączeniu z magazynem dysków twardych programu DPM. SERWERA usługi MAB v3 UR1 z magazynem warstwowym zapewnia 50-70% szybszych kopii zapasowych. Zapoznaj się z artykułem DPM [set up MB with Tiered Storage](/system-center/dpm/add-storage#set-up-mbs-with-tiered-storage) , aby uzyskać instrukcje dotyczące konfigurowania magazynu warstwowego.

### <a name="support-for-refs-volumes"></a>Obsługa woluminów ReFS

Za pomocą serwera usługi MAB v3 UR1 można utworzyć kopię zapasową woluminów i obciążeń systemu plików ReFS wdrożonych na woluminie systemu plików ReFS. Można utworzyć kopię zapasową następujących obciążeń wdrożonych na woluminach systemu plików ReFS:

* System operacyjny (64 bitowy): Windows Server 2019, 2016, 2012 R2, 2012.
* SQL Server: SQL Server 2019, SQL Server 2017, 2016.
* Exchange: Exchange 2019, 2016.
* SharePoint: SharePoint 2019, 2016 z najnowszym dodatkiem SP.

>[!NOTE]
> Tworzenie kopii zapasowych maszyn wirtualnych funkcji Hyper-V przechowywanych na woluminie systemu plików ReFS jest obsługiwane z serwera usługi MAB v3

>WAŻNE Zidentyfikowano kilka problemów z kopią zapasową deduplikowanych woluminów ReFS. Pracujemy nad naprawianiem tych elementów i zaktualizujemy tę sekcję zaraz po udostępnieniu poprawki. Do tego czasu usuwamy obsługę kopii zapasowych deduplikowanych woluminów ReFS z MABSv3 UR1.

### <a name="azure-vmware-solution-protection-support"></a>Obsługa ochrony rozwiązań VMware na platformie Azure

Program serwera usługi MAB v3 UR1 umożliwia teraz ochronę maszyn wirtualnych wdrożonych w [rozwiązaniu VMware platformy Azure](../azure-vmware/index.yml).

### <a name="vmware-parallel-backups"></a>Równoległe kopie zapasowe VMware

W przypadku serwera usługi MAB v3 UR1 wszystkie kopie zapasowe maszyn wirtualnych VMware w ramach jednej grupy ochrony będą równoległe, co prowadzi do 25% szybszych kopii zapasowych maszyn wirtualnych.
We wcześniejszych wersjach programu serwera usługi MAB równoległe kopie zapasowe były wykonywane tylko w grupach ochrony. W przypadku serwera usługi MAB v3 UR1 zadania replikacji różnicowej programu VMware są uruchamiane równolegle. Domyślnie liczba zadań do uruchomienia równoległego jest ustawiona na 8. Dowiedz się więcej o [równoległych kopiach zapasowych VMware](backup-azure-backup-server-vmware.md#vmware-parallel-backups).

### <a name="disk-exclusion-for-vmware-vm-backup"></a>Wykluczanie dysku dla kopii zapasowej maszyny wirtualnej VMware

Za pomocą serwera usługi MAB v3 UR1 można wykluczyć określone dyski z kopii zapasowej maszyny wirtualnej VMware. Dowiedz się więcej [na temat wykluczania dysków z kopii zapasowej maszyny wirtualnej VMware](backup-azure-backup-server-vmware.md#exclude-disk-from-vmware-vm-backup).  

### <a name="support-for-additional-layer-of-authentication-to-delete-online-backup"></a>Obsługa dodatkowej warstwy uwierzytelniania do usuwania kopii zapasowych online

W przypadku serwera usługi MAB v3 UR1 dodano dodatkową warstwę uwierzytelniania dla operacji krytycznych. Po **zatrzymaniu ochrony przy użyciu operacji usuwania danych** zostanie wyświetlony monit o wprowadzenie zabezpieczającego numeru PIN.

### <a name="offline-backup-improvements"></a>Ulepszenia kopii zapasowej w trybie offline

SERWERA usługi MAB v3 UR1 usprawnia wykonywanie kopii zapasowych w trybie offline za pomocą usługi Azure Import/Export. Aby uzyskać więcej informacji, zapoznaj się z zaktualizowanymi [krokami](./backup-azure-backup-server-import-export.md).

>[!NOTE]
>Aktualizacja udostępnia również wersję zapoznawczą kopii zapasowej offline przy użyciu Azure Data Box w serwera usługi MAB. Skontaktuj się z nami [SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com) , aby dowiedzieć się więcej.

### <a name="new-cmdlet-parameter"></a>Nowy parametr polecenia cmdlet

SERWERA usługi MAB v3 UR1 zawiera nowy parametr **[-CheckReplicaFragmentation]**. Nowy parametr oblicza wartość procentową fragmentacji dla repliki i jest uwzględniona w poleceniu cmdlet **copy-DPMDatasourceReplica** .

### <a name="32-bit-protection-agent-deprecation"></a>32 — przestarzały Agent ochrony

W programie serwera usługi MAB v3 UR1 obsługa agenta ochrony 32-bitowego nie jest już obsługiwana. Nie będzie można chronić 32-bitowych obciążeń po uaktualnieniu serwera serwera usługi MAB v3 do UR1. Wszystkie istniejące 32-bitowe agenci ochrony będą w stanie wyłączenia, a zaplanowane kopie zapasowe zakończą się niepowodzeniem, a **Agent jest wyłączony** . Jeśli chcesz zachować dane kopii zapasowej dla tych agentów, możesz zatrzymać ochronę przy użyciu opcji Zachowaj dane. W przeciwnym razie Agent ochrony może zostać usunięty.

>[!NOTE]
>Zapoznaj się ze [zaktualizowaną matrycą ochrony](./backup-mabs-protection-matrix.md) , aby poznać obsługiwane obciążenia na potrzeby ochrony za pomocą serwera usługi MAB ur 1.

## <a name="whats-new-in-mabs-v3-rtm"></a>Co nowego w programie serwera usługi MAB v3 RTM

Microsoft Azure Backup Server w wersji 3 (serwera usługi MAB v3) obejmuje poprawki krytyczne, wsparcie dla systemu Windows Server 2019, obsługę programu SQL 2017 oraz inne funkcje i ulepszenia. Aby wyświetlić listę poprawionych usterek i instrukcje dotyczące instalacji programu serwera usługi MAB v3, zobacz artykuł [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)w bazie wiedzy.

W serwera usługi MAB v3 uwzględniono następujące funkcje:

### <a name="volume-to-volume-migration"></a>Migracja woluminu do woluminu

W przypadku Nowoczesny magazyn kopii zapasowych (MB) w serwera usługi MAB v2 ogłoszono magazyn z obsługą obciążeń, w którym można tworzyć kopie zapasowe pewnych obciążeń do określonego magazynu na podstawie właściwości magazynu. Jednak po zakończeniu konfiguracji może zajść potrzeba przeniesienia kopii zapasowych niektórych źródeł danych do innego magazynu w celu zoptymalizowania użycia zasobów. SERWERA usługi MAB v3 daje możliwość migrowania kopii zapasowych i konfigurowania ich do przechowywania na innym woluminie w [trzech krokach](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

### <a name="prevent-unexpected-data-loss"></a>Zapobiegaj nieoczekiwanej utracie danych

W przedsiębiorstwach serwera usługi MAB jest zarządzany przez zespół administratorów. Chociaż istnieją wskazówki dotyczące magazynu, które powinny być używane do tworzenia kopii zapasowych, nieprawidłowy wolumin serwera usługi MAB jako magazyn kopii zapasowych może prowadzić do utraty danych o kluczowym znaczeniu. W programie serwera usługi MAB v3 można zapobiec takim scenariuszom, konfigurując te woluminy jako te, które nie są dostępne dla magazynu za pomocą [tych poleceń cmdlet programu PowerShell](./backup-mabs-add-storage.md).

### <a name="custom-size-allocation"></a>Alokacja rozmiaru niestandardowego

Nowoczesny magazyn kopii zapasowych (MB) zużywa cienki magazyn, jak i w razie konieczności. W tym celu serwera usługi MAB oblicza wielkość kopii zapasowej danych, gdy jest ona skonfigurowana do ochrony. Jeśli jednak kopia zapasowa wielu plików i folderów jest tworzona razem, tak jak w przypadku serwera plików, Obliczanie rozmiaru może trwać długo. Za pomocą serwera usługi MAB v3 można skonfigurować serwera usługi MAB do akceptowania rozmiaru woluminu jako domyślnego, zamiast obliczania rozmiaru każdego pliku, co oszczędza czas.

### <a name="optimized-cc-for-rct-vms"></a>Zoptymalizowane maszyny wirtualne CC for RCT

SERWERA usługi MAB korzysta z RCT (natywne śledzenie zmian w funkcji Hyper-V), co zmniejsza potrzebę czasochłonnych kontroli spójności w sytuacjach, gdy awarie maszyn wirtualnych. Śledzenie RCT zapewnia większą odporność niż śledzenie zmian w opartych na migawkach kopiach zapasowych usługi VSS. SERWERA usługi MAB v3 optymalizuje użycie sieci i magazynu przez transfer tylko zmienionych danych podczas kontroli spójności.

### <a name="support-to-tls-12"></a>Obsługa protokołu TLS 1,2

TLS 1,2 to bezpieczny sposób komunikacji sugerowanej przez firmę Microsoft z użyciem najlepszego szyfrowania klas. Usługa serwera usługi MAB obsługuje teraz komunikację TLS 1,2 między serwerami serwera usługi MAB i chronionymi, na potrzeby uwierzytelniania opartego na certyfikatach oraz kopii zapasowych w chmurze.

### <a name="vmware-vm-protection-support"></a>Obsługa ochrony maszyn wirtualnych VMware

Kopia zapasowa maszyny wirtualnej VMware jest teraz obsługiwana w przypadku wdrożeń produkcyjnych. SERWERA usługi MAB v3 oferuje następujące funkcje ochrony maszyny wirtualnej VMware:

* Obsługa programu vCenter i ESXi 6,5 wraz z obsługą 5,5 i 6,0.
* Autoochrona maszyn wirtualnych VMware do chmury. Jeśli do chronionego folderu zostaną dodane nowe maszyny wirtualne VMware, są one automatycznie chronione na dyskach i w chmurze.
* Ulepszenia wydajności odzyskiwania w przypadku odzyskiwania alternatywnej lokalizacji programu VMware.

### <a name="sql-2017-support"></a>Obsługa programu SQL 2017

SERWERA usługi MAB v3 można zainstalować przy użyciu programu SQL 2017 jako bazy danych serwera usługi MAB. Możesz uaktualnić program SQL Server z bazy danych SQL 2016 do programu SQL 2017 lub zainstalować go ponownie. Można również utworzyć kopię zapasową obciążenia SQL 2017 zarówno w środowisku klastrowanym, jak i nieklastrowanym z serwera usługi MAB v3.

### <a name="windows-server-2019-support"></a>Obsługa systemu Windows Server 2019

SERWERA usługi MAB v3 można zainstalować w systemie Windows Server 2019. Aby użyć serwera usługi MAB v3 z WS2019, można uaktualnić system operacyjny do wersji WS2019 przed instalacją/uaktualnieniem do wersji serwera usługi MAB v3 lub uaktualnić system operacyjny po zainstalowaniu/uaktualnieniu wersji v3 na WS2016.

SERWERA usługi MAB v3 to pełna wersja, którą można zainstalować bezpośrednio w systemie Windows Server 2016, Windows Server 2019 lub można uaktualnić z serwera usługi MAB v2. Przed uaktualnieniem do programu Backup Server v3 lub jego zainstalowanie Przeczytaj informacje o wymaganiach wstępnych instalacji.
Więcej informacji na temat kroków instalacji/uaktualniania dla programu serwera usługi MAB znajdziesz [tutaj](./backup-azure-microsoft-azure-backup.md#software-package).

> [!NOTE]
>
> SERWERA usługi MAB ma ten sam kod podstawowy co System Center Data Protection Manager. SERWERA usługi MAB V3 jest równoważne Data Protection Manager 1807. SERWERA usługi MAB v3 UR1 jest równoważne Data Protection Manager 2019 UR1.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak przygotować serwer lub rozpocząć ochronę obciążeń:

* [Przygotowywanie obciążeń serwera kopii zapasowej](backup-azure-microsoft-azure-backup.md)
* [Tworzenie kopii zapasowej serwera VMware przy użyciu serwera kopii zapasowej](backup-azure-backup-server-vmware.md)
* [Użyj serwera kopii zapasowej, aby utworzyć kopię zapasową SQL Server](backup-azure-sql-mabs.md)
* [Używanie Nowoczesny magazyn kopii zapasowych z serwerem kopii zapasowej](backup-mabs-add-storage.md)
