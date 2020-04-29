---
title: Informacje o agencie MARS
description: Dowiedz się, jak Agent MARS obsługuje scenariusze tworzenia kopii zapasowych
ms.reviewer: srinathv
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: d2cc8e32152f6930c9c250e2811668cc2c924616
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78673291"
---
# <a name="about-the-microsoft-azure-recovery-services-mars-agent"></a>Informacje o agencie Microsoft Azure Recovery Services (MARS)

W tym artykule opisano, jak usługa Azure Backup używa agenta Microsoft Azure Recovery Services (MARS) do tworzenia kopii zapasowych i przywracania plików, folderów oraz stanu woluminu lub systemu z komputera lokalnego na platformę Azure.

Agent MARS obsługuje następujące scenariusze tworzenia kopii zapasowych:

![Scenariusze tworzenia kopii zapasowych MARS](./media/backup-try-azure-backup-in-10-mins/backup-scenarios.png)

- **Pliki i foldery**: selektywnie Chroń pliki i foldery systemu Windows.
- **Poziom głośności**: Chroń cały wolumin systemu Windows na komputerze.
- **Poziom systemu**: Chroń cały stan systemu Windows.

Agent MARS obsługuje następujące scenariusze przywracania:

![Scenariusze odzyskiwania MARS](./media/backup-try-azure-backup-in-10-mins/restore-scenarios.png)

- **Ten sam serwer**: serwer, na którym utworzono kopię zapasową.
  - **Pliki i foldery**: Wybierz pojedyncze pliki i foldery, które chcesz przywrócić.
  - **Poziom głośności**: Wybierz wolumin i punkt odzyskiwania, które chcesz przywrócić, a następnie Przywróć je do tej samej lokalizacji lub lokalizacji alternatywnej na tym samym komputerze.  Utwórz kopię istniejących plików, Zastąp istniejące pliki lub Pomiń odzyskiwanie istniejących plików.
  - **Poziom systemu**: Wybierz stan systemu i punkt odzyskiwania do przywrócenia na ten sam komputer w określonej lokalizacji.

- **Serwer alternatywny**: serwer inny niż serwer, na którym wykonano kopię zapasową.
  - **Pliki i foldery**: Wybierz pojedyncze pliki i foldery, których punkt odzyskiwania ma zostać przywrócony do maszyny docelowej.
  - **Poziom głośności**: Wybierz wolumin i punkt odzyskiwania, które chcesz przywrócić do innej lokalizacji. Utwórz kopię istniejących plików, Zastąp istniejące pliki lub Pomiń odzyskiwanie istniejących plików.
  - **Poziom systemu**: Wybierz stan systemu i punkt odzyskiwania do przywrócenia jako plik stanu systemu do alternatywnej maszyny.

## <a name="backup-process"></a>Proces tworzenia kopii zapasowej

1. Z Azure Portal Utwórz [magazyn Recovery Services](install-mars-agent.md#create-a-recovery-services-vault), a następnie wybierz pliki, foldery i stan systemu z celów tworzenia kopii zapasowych.
2. [Pobierz poświadczenia magazynu Recovery Services i instalatora agenta](https://docs.microsoft.com/azure/backup/install-mars-agent#download-the-mars-agent) na maszynę lokalną.

    Aby chronić maszynę lokalną przez wybranie opcji kopia zapasowa, wybierz pliki, foldery i stan systemu, a następnie pobierz agenta MARS.

3. Przygotuj infrastrukturę:

    a. Uruchom Instalatora, aby [zainstalować agenta](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent).

    b. Użyj pobranych poświadczeń magazynu, aby zarejestrować maszynę w magazynie Recovery Services.
4. W konsoli agenta programu na kliencie [Skonfiguruj kopię zapasową](https://docs.microsoft.com/azure/backup/backup-windows-with-mars-agent#create-a-backup-policy). Określ zasady przechowywania danych kopii zapasowej, aby rozpocząć ich ochronę.

![Diagram agenta Azure Backup](./media/backup-try-azure-backup-in-10-mins/backup-process.png)

### <a name="additional-scenarios"></a>Dodatkowe scenariusze

- **Tworzenie kopii zapasowych określonych plików i folderów w usłudze Azure Virtual**Machines: podstawowa metoda tworzenia kopii zapasowych maszyn wirtualnych platformy Azure polega na użyciu rozszerzenia Azure Backup na maszynie wirtualnej. Rozszerzenie tworzy kopię zapasową całej maszyny wirtualnej. Jeśli chcesz utworzyć kopię zapasową określonych plików i folderów w ramach maszyny wirtualnej, możesz zainstalować agenta MARS na maszynach wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [Architektura: Wbudowana kopia zapasowa maszyny wirtualnej platformy Azure](https://docs.microsoft.com/azure/backup/backup-architecture#architecture-built-in-azure-vm-backup).

- **Rozmieszczanie w trybie offline**: wstępne pełne kopie zapasowe danych na platformie Azure zazwyczaj przesyłają duże ilości danych i wymagają większej przepustowości sieci. Kolejne kopie zapasowe przesyłają tylko dane różnicowe lub przyrostowe. Azure Backup kompresuje początkowe kopie zapasowe. W procesie umieszczania w *trybie offline*, Azure Backup mogą używać dysków do przekazywania skompresowanych danych początkowej kopii zapasowej w trybie offline do platformy Azure. Aby uzyskać więcej informacji, zobacz [Azure Backup w trybie offline — tworzenie kopii zapasowej przy użyciu Azure Data Box](offline-backup-azure-data-box.md).

## <a name="next-steps"></a>Następne kroki

[Macierz obsługi agenta usługi MARS](https://docs.microsoft.com/azure/backup/backup-support-matrix-mars-agent)

[Agent MARS — często zadawane pytania](https://docs.microsoft.com/azure/backup/backup-azure-file-folder-backup-faq)
