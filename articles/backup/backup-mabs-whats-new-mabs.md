---
title: Co nowego w usłudze Microsoft Azure Backup Server
description: Serwer Microsoft Azure Backup zapewnia ulepszone możliwości tworzenia kopii zapasowych w celu ochrony maszyn wirtualnych, plików i folderów, obciążeń i nie tylko. Dowiedz się, jak zainstalować program lub uaktualnić go do wersji Azure Backup Server v3.
ms.topic: conceptual
ms.date: 11/13/2018
ms.openlocfilehash: 358de6f2986036621accde90550e2afcc47f9135
ms.sourcegitcommit: 8017209cc9d8a825cc404df852c8dc02f74d584b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/01/2020
ms.locfileid: "84247550"
---
# <a name="whats-new-in-microsoft-azure-backup-server"></a>Co nowego w usłudze Microsoft Azure Backup Server

Microsoft Azure Backup Server w wersji 3 (serwera usługi MAB v3) to najnowsza aktualizacja, która zawiera krytyczne poprawki błędów, wsparcie dla systemu Windows Server 2019, pomoc techniczną programu SQL 2017 oraz inne funkcje i ulepszenia. Aby wyświetlić listę poprawionych usterek i instrukcje dotyczące instalacji programu serwera usługi MAB v3, zobacz artykuł [4457852](https://support.microsoft.com/help/4457852/microsoft-azure-backup-server-v3)w bazie wiedzy.

W serwera usługi MAB v3 uwzględniono następujące funkcje:

## <a name="volume-to-volume-migration"></a>Migracja woluminu do woluminu

W przypadku Nowoczesny magazyn kopii zapasowych (MB) w serwera usługi MAB v2 ogłoszono magazyn z obsługą obciążeń, w którym można tworzyć kopie zapasowe pewnych obciążeń do określonego magazynu na podstawie właściwości magazynu. Jednak po zakończeniu konfiguracji może zajść potrzeba przeniesienia kopii zapasowych niektórych źródeł danych do innego magazynu w celu zoptymalizowania użycia zasobów. SERWERA usługi MAB v3 daje możliwość migrowania kopii zapasowych i konfigurowania ich do przechowywania na innym woluminie w [trzech krokach](https://techcommunity.microsoft.com/t5/system-center-blog/sc-2016-dpm-ur4-migrate-backup-storage-in-3-simple-steps/ba-p/351842).

## <a name="prevent-unexpected-data-loss"></a>Zapobiegaj nieoczekiwanej utracie danych

W przedsiębiorstwach serwera usługi MAB jest zarządzany przez zespół administratorów. Chociaż istnieją wskazówki dotyczące magazynu, które powinny być używane do tworzenia kopii zapasowych, nieprawidłowy wolumin serwera usługi MAB jako magazyn kopii zapasowych może prowadzić do utraty danych o kluczowym znaczeniu. W programie serwera usługi MAB v3 można uniknąć takich scenariuszy, konfigurując te woluminy jako te, które nie są dostępne dla magazynu za pomocą [tych poleceń cmdlet programu PowerShell](https://docs.microsoft.com/azure/backup/backup-mabs-add-storage).

## <a name="custom-size-allocation"></a>Alokacja rozmiaru niestandardowego

Nowoczesny magazyn kopii zapasowych (MB) zużywa cienki magazyn, jak i w razie konieczności. W tym celu serwera usługi MAB oblicza rozmiar kopii zapasowej danych, gdy jest ona skonfigurowana do ochrony. Jeśli jednak kopia zapasowa wielu plików i folderów jest tworzona razem, tak jak w przypadku serwera plików, Obliczanie rozmiaru może trwać długo. Za pomocą serwera usługi MAB v3 można skonfigurować serwera usługi MAB do akceptowania rozmiaru woluminu jako domyślnego, zamiast obliczania rozmiaru każdego pliku, co oszczędza czas.

## <a name="optimized-cc-for-rct-vms"></a>Zoptymalizowane maszyny wirtualne CC for RCT

SERWERA usługi MAB korzysta z RCT (natywne śledzenie zmian w funkcji Hyper-V), co zmniejsza potrzebę czasochłonnych kontroli spójności w sytuacjach, gdy awarie maszyn wirtualnych. Śledzenie RCT zapewnia większą odporność niż śledzenie zmian w opartych na migawkach kopiach zapasowych usługi VSS. SERWERA usługi MAB v3 optymalizuje użycie sieci i magazynu przez transfer tylko zmienionych danych podczas kontroli spójności.

## <a name="support-to-tls-12"></a>Obsługa protokołu TLS 1,2

TLS 1,2 to bezpieczny sposób komunikacji sugerowanej przez firmę Microsoft z użyciem najlepszego szyfrowania klas. Usługa serwera usługi MAB obsługuje teraz komunikację TLS 1,2 między serwerami serwera usługi MAB i chronionymi, na potrzeby uwierzytelniania opartego na certyfikatach oraz kopii zapasowych w chmurze.

## <a name="vmware-vm-protection-support"></a>Obsługa ochrony maszyn wirtualnych VMware

Kopia zapasowa maszyny wirtualnej VMware jest teraz obsługiwana w przypadku wdrożeń produkcyjnych. SERWERA usługi MAB v3 oferuje następujące funkcje ochrony maszyny wirtualnej VMware:

- Obsługa programu vCenter i ESXi 6,5 wraz z obsługą 5,5 i 6,0.
- Autoochrona maszyn wirtualnych VMware do chmury. Jeśli do chronionego folderu zostaną dodane nowe maszyny wirtualne VMware, są one automatycznie chronione na dyskach i w chmurze.
- Ulepszenia wydajności odzyskiwania w przypadku odzyskiwania alternatywnej lokalizacji programu VMware.

## <a name="sql-2017-support"></a>Obsługa programu SQL 2017

SERWERA usługi MAB v3 można zainstalować przy użyciu programu SQL 2017 jako bazy danych serwera usługi MAB. Możesz uaktualnić program SQL Server z bazy danych SQL 2016 do programu SQL 2017 lub zainstalować go ponownie. Można również utworzyć kopię zapasową obciążenia SQL 2017 zarówno w środowisku klastrowanym, jak i nieklastrowanym z serwera usługi MAB v3.

## <a name="windows-server-2019-support"></a>Obsługa systemu Windows Server 2019

SERWERA usługi MAB v3 można zainstalować w systemie Windows Server 2019. Aby użyć serwera usługi MAB v3 z WS2019, można uaktualnić system operacyjny do wersji WS2019 przed instalacją/uaktualnieniem do wersji serwera usługi MAB v3 lub uaktualnić wpis systemu operacyjnego instalujący/uaktualniający v3 na WS2016.

SERWERA usługi MAB v3 to pełna wersja, którą można zainstalować bezpośrednio w systemie Windows Server 2016, Windows Server 2019 lub można uaktualnić z serwera usługi MAB v2. Przed uaktualnieniem do programu Backup Server v3 lub jego zainstalowanie Przeczytaj informacje o wymaganiach wstępnych instalacji.
Więcej informacji na temat kroków instalacji/uaktualniania dla programu serwera usługi MAB znajdziesz [tutaj](https://docs.microsoft.com/azure/backup/backup-azure-microsoft-azure-backup#software-package).

> [!NOTE]
>
> SERWERA usługi MAB ma ten sam kod podstawowy co System Center Data Protection Manager. SERWERA usługi MAB V3 jest równoważne Data Protection Manager 1807.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak przygotować serwer lub rozpocząć ochronę obciążeń:

- [Znane problemy w programie serwera usługi MAB v3](backup-mabs-release-notes-v3.md)
- [Przygotowywanie obciążeń serwera kopii zapasowej](backup-azure-microsoft-azure-backup.md)
- [Tworzenie kopii zapasowej serwera VMware przy użyciu serwera kopii zapasowej](backup-azure-backup-server-vmware.md)
- [Użyj serwera kopii zapasowej, aby utworzyć kopię zapasową SQL Server](backup-azure-sql-mabs.md)
- [Używanie Nowoczesny magazyn kopii zapasowych z serwerem kopii zapasowej](backup-mabs-add-storage.md)
