---
title: Informacje o agencie MARS
description: Dowiedz się, jak Agent MARS obsługuje scenariusze tworzenia kopii zapasowych
ms.topic: conceptual
ms.date: 08/04/2020
ms.openlocfilehash: 8e4ace0c17dbe75e989981db56583ed9477b3716
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87562603"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informacje o agencie Microsoft Azure Recovery Services (MARS)

W tym artykule opisano, jak usługa Azure Backup używa agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych i przywracania plików, folderów oraz stanu woluminu lub systemu z komputera lokalnego na platformę Azure.

## <a name="backup-scenarios"></a>Scenariusze tworzenia kopii zapasowych

Agent MARS obsługuje następujące scenariusze tworzenia kopii zapasowych:

![Scenariusze tworzenia kopii zapasowych MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Pliki i foldery**: selektywnie Chroń pliki i foldery systemu Windows.
- **Poziom głośności**: Chroń cały wolumin systemu Windows na komputerze.
- **Poziom systemu**: Chroń cały stan systemu Windows.

### <a name="additional-scenarios"></a>Dodatkowe scenariusze

- **Tworzenie kopii zapasowych określonych plików i folderów w usłudze Azure Virtual** Machines: podstawowa metoda tworzenia kopii zapasowych maszyn wirtualnych platformy Azure polega na użyciu rozszerzenia Azure Backup na maszynie wirtualnej. Rozszerzenie tworzy kopię zapasową całej maszyny wirtualnej. Jeśli chcesz utworzyć kopię zapasową określonych plików i folderów w ramach maszyny wirtualnej, możesz zainstalować agenta MARS na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [Architektura: Wbudowana kopia zapasowa maszyny wirtualnej platformy Azure](./backup-architecture.md#architecture-built-in-azure-vm-backup).

- **Rozmieszczanie w trybie offline**: wstępne pełne kopie zapasowe danych na platformie Azure zazwyczaj przesyłają duże ilości danych i wymagają większej przepustowości sieci. Kolejne kopie zapasowe przesyłają tylko dane różnicowe lub przyrostowe. Azure Backup kompresuje początkowe kopie zapasowe. W procesie umieszczania w *trybie offline*, Azure Backup mogą używać dysków do przekazywania skompresowanych danych początkowej kopii zapasowej w trybie offline do platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Backup w trybie offline — tworzenie kopii zapasowej przy użyciu Azure Data Box](offline-backup-azure-data-box.md).

## <a name="restore-scenarios"></a>Scenariusze przywracania

Agent MARS obsługuje następujące scenariusze przywracania:

![Scenariusze odzyskiwania MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Ten sam serwer**: serwer, na którym utworzono kopię zapasową.
  - **Pliki i foldery**: Wybierz pojedyncze pliki i foldery, które chcesz przywrócić.
  - **Poziom głośności**: Wybierz wolumin i punkt odzyskiwania, które chcesz przywrócić. Następnie Przywróć ją do tej samej lokalizacji lub innej lokalizacji na tym samym komputerze.  Utwórz kopię istniejących plików, Zastąp istniejące pliki lub Pomiń odzyskiwanie istniejących plików.
  - **Poziom systemu**: Wybierz stan systemu i punkt odzyskiwania do przywrócenia na ten sam komputer w określonej lokalizacji.

- **Serwer alternatywny**: serwer inny niż serwer, na którym wykonano kopię zapasową.
  - **Pliki i foldery**: Wybierz pojedyncze pliki i foldery, których punkt odzyskiwania ma zostać przywrócony do maszyny docelowej.
  - **Poziom głośności**: Wybierz wolumin i punkt odzyskiwania, które chcesz przywrócić do innej lokalizacji. Utwórz kopię istniejących plików, Zastąp istniejące pliki lub Pomiń odzyskiwanie istniejących plików.
  - **Poziom systemu**: Wybierz stan systemu i punkt odzyskiwania do przywrócenia jako plik stanu systemu do alternatywnej maszyny.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

1. Z Azure Portal Utwórz [magazyn Recovery Services](install-mars-agent.md#create-a-recovery-services-vault), a następnie wybierz pliki, foldery i stan systemu z **celów tworzenia kopii zapasowych**.
2. [Pobierz poświadczenia magazynu Recovery Services i instalatora agenta](./install-mars-agent.md#download-the-mars-agent) na maszynę lokalną.

3. [Zainstaluj agenta](./install-mars-agent.md#install-and-register-the-agent) i użyj pobranych poświadczeń magazynu, aby zarejestrować maszynę w magazynie Recovery Services.
4. Z poziomu konsoli agenta na kliencie [Skonfiguruj kopię zapasową](./backup-windows-with-mars-agent.md#create-a-backup-policy) , aby określić, co ma być wykonywane w ramach kopii zapasowej (harmonogram), jak długo kopie zapasowe mają być przechowywane na platformie Azure (zasady przechowywania) i Rozpocznij ochronę.

![Diagram agenta Azure Backup](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-information"></a>Dodatkowe informacje

- **Początkowa kopia zapasowa** (pierwsza kopia zapasowa) jest uruchamiana zgodnie z ustawieniami kopii zapasowej.  Agent MARS korzysta z usługi VSS, aby wykonać migawkę woluminów wybranych do utworzenia kopii zapasowej w danym momencie. Agent używa tylko operacji modułu zapisywania systemu Windows, aby przechwycić migawkę. Nie używa żadnych składników zapisywania usługi VSS aplikacji i nie przechwytuje migawek spójnych na poziomie aplikacji. Po przeprowadzeniu migawki przy użyciu usługi VSS Agent MARS tworzy wirtualny dysk twardy (VHD) w folderze pamięci podręcznej określonym podczas konfigurowania kopii zapasowej. Agent przechowuje również sumy kontrolne dla każdego bloku danych.

- **Przyrostowe kopie zapasowe** (kolejne kopie zapasowe) działają zgodnie z określonym harmonogramem. Podczas przyrostowych kopii zapasowych zostaną zidentyfikowane zmienione pliki i zostanie utworzony nowy wirtualny dysk twardy. Wirtualny dysk twardy jest kompresowany i szyfrowany, a następnie wysyłany do magazynu. Po zakończeniu przyrostowej kopii zapasowej nowy wirtualny dysk twardy zostanie scalony z dyskiem VHD utworzonym po replikacji początkowej. Ten scalony wirtualny dysk twardy zawiera najnowszy stan, który będzie używany do porównywania ciągłej kopii zapasowej.

- Agent MARS może uruchomić zadanie tworzenia kopii zapasowej w **trybie zoptymalizowanym** przy użyciu numeru USN (numer sekwencji aktualizacji) lub w **trybie niezoptymalizowanym** , sprawdzając zmiany w katalogach lub plikach przez skanowanie całego woluminu. Tryb niezoptymalizowany jest wolniejszy, ponieważ Agent programu musi skanować każdy plik w woluminie i porównywać go z metadanymi w celu określenia zmienionych plików.  **Początkowa kopia zapasowa** będzie zawsze uruchamiana w trybie niezoptymalizowanym. Jeśli Poprzednia kopia zapasowa nie powiodła się, następne zaplanowane zadanie tworzenia kopii zapasowej zostanie uruchomione w trybie niezoptymalizowanym. Aby dowiedzieć się więcej o tych trybach i sposobach ich weryfikowania, zapoznaj się z [tym artykułem](backup-azure-troubleshoot-slow-backup-performance-issue.md#cause-backup-job-running-in-unoptimized-mode).

## <a name="next-steps"></a>Następne kroki

[Macierz obsługi agenta usługi MARS](./backup-support-matrix-mars-agent.md)

[Agent MARS — często zadawane pytania](./backup-azure-file-folder-backup-faq.md)
