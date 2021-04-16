---
title: Informacje o agencie MARS
description: Dowiedz się, jak agent MARS obsługuje scenariusze tworzenia kopii zapasowych
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 9e01694aca386482f9ff7ba52593c88326ba3d62
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107517746"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informacje o agencie Microsoft Azure Recovery Services (MARS)

W tym artykule opisano sposób, w jaki usługa Azure Backup używa agenta usług Microsoft Azure Recovery Services (MARS) do obsługi kopii zapasowej i przywracania plików, folderów oraz stanu woluminu lub systemu z komputera lokalnego na platformę Azure.

## <a name="backup-scenarios"></a>Scenariusze tworzenia kopii zapasowych

Agent MARS obsługuje następujące scenariusze tworzenia kopii zapasowych:

![Scenariusze tworzenia kopii zapasowych usługi MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Pliki i foldery:** selektywnie chroń pliki i foldery systemu Windows.
- **Poziom woluminu:** ochrona całego woluminu systemu Windows na maszynie.
- **Poziom systemu:** ochrona całego stanu systemu Windows.

### <a name="additional-scenarios"></a>Dodatkowe scenariusze

- **Kopię zapasową** określonych plików i folderów na maszynach wirtualnych platformy Azure: Podstawową metodą tworzenia kopii zapasowych maszyn wirtualnych platformy Azure jest użycie rozszerzenia Azure Backup na maszynie wirtualnej. Rozszerzenie kopii zapasowej całej maszyny wirtualnej. Jeśli chcesz utworzyć kopię zapasową określonych plików i folderów na maszynie wirtualnej, możesz zainstalować agenta MARS na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [Architecture: Built-in Azure VM Backup (Architektura: wbudowana kopia zapasowa maszyny wirtualnej platformy Azure).](./backup-architecture.md#architecture-built-in-azure-vm-backup)

- **Wstępne inicjowanie w** trybie offline: początkowe pełne kopie zapasowe danych na platformę Azure zwykle przesyłą duże ilości danych i wymagają większej przepustowości sieci. Kolejne kopie zapasowe przesyłą tylko przyrostową lub przyrostową ilość danych. Azure Backup kompresuje początkowe kopie zapasowe. W ramach procesu wstępnego *inicjowania* w trybie offline Azure Backup używać dysków do przekazywania skompresowanych danych początkowej kopii zapasowej w trybie offline na platformę Azure. Aby uzyskać więcej informacji, zobacz [Azure Backup offline-backup using Azure Data Box](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Scenariusze przywracania

Agent MARS obsługuje następujące scenariusze przywracania:

![Scenariusze odzyskiwania mars](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Ten sam serwer:** serwer, na którym pierwotnie utworzono kopię zapasową.
  - **Pliki i foldery:** wybierz poszczególne pliki i foldery, które chcesz przywrócić.
  - **Poziom woluminu:** wybierz wolumin i punkt odzyskiwania, które chcesz przywrócić. Następnie przywróć ją do tej samej lokalizacji lub lokalizacji alternatywnej na tym samym komputerze.  Utwórz kopię istniejących plików, zastąp istniejące pliki lub pomiń odzyskiwanie istniejących plików.
  - **Poziom systemu:** wybierz stan systemu i punkt odzyskiwania do przywrócenia na tę samą maszynę w określonej lokalizacji.

- **Serwer alternatywny:** serwer inny niż serwer, na którym została podjęta kopia zapasowa.
  - **Pliki i foldery:** wybierz poszczególne pliki i foldery, których punkt odzyskiwania chcesz przywrócić na maszynie docelowej.
  - **Poziom woluminu:** wybierz wolumin i punkt odzyskiwania, które chcesz przywrócić do innej lokalizacji. Utwórz kopię istniejących plików, zastąp istniejące pliki lub pomiń odzyskiwanie istniejących plików.
  - **Poziom systemu:** wybierz stan systemu i punkt odzyskiwania do przywrócenia jako plik stanu systemu na maszynie alternatywnej.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

1. Na stronie Azure Portal magazyn usługi [Recovery Services](install-mars-agent.md#create-a-recovery-services-vault)i wybierz pliki, foldery i stan systemu z grupy Cele kopii **zapasowej.**
2. [Pobierz poświadczenia magazynu usługi Recovery Services i instalatora agenta](./install-mars-agent.md#download-the-mars-agent) na maszynę lokalną.

3. [Zainstaluj agenta i](./install-mars-agent.md#install-and-register-the-agent) użyj pobranych poświadczeń magazynu, aby zarejestrować maszynę w magazynie usługi Recovery Services.
4. W konsoli agenta na [](./backup-windows-with-mars-agent.md#create-a-backup-policy) kliencie skonfiguruj kopię zapasową, aby określić, co należy utworzyć, kiedy utworzyć kopię zapasową (harmonogram), jak długo kopie zapasowe mają być przechowywane na platformie Azure (zasady przechowywania) i rozpocząć ochronę.

![Azure Backup diagram agenta](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Dodatkowe informacje

- Początkowa **kopia zapasowa** (pierwsza kopia zapasowa) jest uruchamiana zgodnie z ustawieniami kopii zapasowej.  Agent MARS używa usługi VSS do utworzenia migawki woluminów wybranych do utworzenia kopii zapasowej. Agent przechwytuje migawkę tylko za pomocą operacji zapisu systemu Windows. Nie używa żadnych autorzy usługi VSS aplikacji i nie przechwytuje migawek spójnych z aplikacją. Po zrobieniu migawki za pomocą usługi VSS agent MARS tworzy wirtualny dysk twardy (VHD) w folderze pamięci podręcznej określonym podczas konfigurowania kopii zapasowej. Agent przechowuje również sumy kontrolne dla każdego bloku danych.

- **Przyrostowe kopie zapasowe** (kolejne kopie zapasowe) są uruchamiane zgodnie z harmonogramem. Podczas tworzenia przyrostowych kopii zapasowych są identyfikowane zmienione pliki i tworzony jest nowy dysk VHD. Wirtualny dysk twardy jest kompresowany i szyfrowany, a następnie wysyłany do magazynu. Po zakończeniu tworzenia przyrostowej kopii zapasowej nowy wirtualny dysk twardy jest scalony z dyskiem VHD utworzonym po replikacji początkowej. Ten scalony wirtualny dysk twardy zapewnia najnowszy stan, który będzie używany do porównywania bieżących kopii zapasowych.

- Agent MARS może uruchomić zadanie  tworzenia kopii zapasowej w trybie zoptymalizowanym przy użyciu dziennika zmian USN (Numer sekwencji aktualizacji) lub w trybie **niezoptymalizowanym,** sprawdzając zmiany w katalogach lub plikach za pomocą skanowania całego woluminu. Tryb niezoptymalizowany jest wolniejszy, ponieważ agent musi przeskanować każdy plik na woluminie i porównać go z metadanymi, aby określić zmienione pliki.  Początkowa **kopia zapasowa** będzie zawsze uruchamiana w trybie niezoptymalizowanym. Jeśli poprzednia kopia zapasowa nie powiodła się, następne zaplanowane zadanie tworzenia kopii zapasowej zostanie uruchomione w niezoptymalizowanym trybie. Aby dowiedzieć się więcej na temat tych trybów i sposobu ich weryfikowania, zapoznaj się z [tym artykułem.](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode)

## <a name="next-steps"></a>Następne kroki

[Macierz obsługi agenta usługi MARS](./backup-support-matrix-mars-agent.md)

[Agent MARS — często zadawane pytania](./backup-azure-file-folder-backup-faq.yml)
