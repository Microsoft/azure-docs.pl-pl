---
title: Rozwiązywanie problemów z powolnej kopii zapasowej plików i folderów
description: Zawiera wskazówki dotyczące rozwiązywania problemów ułatwiające zdiagnozowanie przyczyny problemów z wydajnością Azure Backup
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: b3f2ac343ef4a703f347ec8a57f242a636bb32d2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88824019"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów ułatwiające zdiagnozowanie przyczyny niskiej wydajności kopii zapasowych plików i folderów w przypadku korzystania z Azure Backup. W przypadku tworzenia kopii zapasowej plików przy użyciu agenta Azure Backup proces tworzenia kopii zapasowej może trwać dłużej niż oczekiwano. Ta wartość opóźnienia może być spowodowana przez jedną lub więcej z następujących czynności:

* [Na komputerze, na którym jest tworzona kopia zapasowa, istnieją wąskie gardła wydajności.](#cause1)
* [Inny proces lub oprogramowanie antywirusowe zakłóca proces Azure Backup.](#cause2)
* [Agent kopii zapasowej jest uruchomiony na maszynie wirtualnej platformy Azure.](#cause3)  
* [Wykonujesz kopię zapasową dużej liczby plików (miliony).](#cause4)

Przed rozpoczęciem rozwiązywania problemów zalecamy pobranie i zainstalowanie [najnowszego agenta Azure Backup](https://aka.ms/azurebackup_agent). Udostępniamy częste aktualizacje agenta kopii zapasowej, aby rozwiązać różne problemy, dodać funkcje i zwiększyć wydajność.

Zdecydowanie zalecamy także przejrzenie [często zadawanych pytań dotyczących usługi Azure Backup](backup-azure-backup-faq.md) , aby upewnić się, że nie występują żadne typowe problemy z konfiguracją.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Przyczyna: zadanie tworzenia kopii zapasowej działa w trybie niezoptymalizowanym

* Agent MARS może uruchomić zadanie tworzenia kopii zapasowej w **trybie zoptymalizowanym** przy użyciu numeru USN (numer sekwencyjny aktualizacji) lub w **trybie niezoptymalizowanym** , sprawdzając zmiany w katalogach lub plikach przez skanowanie całego woluminu.
* Tryb niezoptymalizowany działa wolno, ponieważ Agent programu musi skanować każdy plik w woluminie i porównać go z metadanymi w celu określenia zmienionych plików.
* Aby to sprawdzić, Otwórz **szczegóły zadania** z konsoli agenta Mars i sprawdź stan, aby sprawdzić, czy mówi **, że dane są transferowane (niezoptymalizowane, może upłynąć więcej czasu)** , jak pokazano poniżej:

    ![Uruchamianie w trybie niezoptymalizowanym](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Następujące warunki mogą spowodować uruchomienie zadania tworzenia kopii zapasowej w trybie niezoptymalizowanym:
  * Pierwsza kopia zapasowa (znana również jako replikacja początkowa) będzie zawsze uruchamiana w trybie niezoptymalizowanym
  * Jeśli poprzednie zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem, następne zaplanowane zadanie tworzenia kopii zapasowej zostanie uruchomione jako niezoptymalizowane.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Przyczyna: wąskie gardła wydajności na komputerze

Wąskie gardła na komputerze, którego kopia zapasowa jest tworzona, mogą spowodować opóźnienia. Na przykład komputer może odczytywać lub zapisywać dane na dysku albo dostępną przepustowość do przesyłania danych za pośrednictwem sieci, mogą spowodować wąskie gardła.

System Windows udostępnia wbudowane narzędzie o nazwie [Performance Monitor](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (PerfMon) do wykrywania tych wąskich gardeł.

Poniżej przedstawiono niektóre liczniki wydajności i zakresy, które mogą być przydatne w diagnozowaniu wąskich gardeł dla optymalnych kopii zapasowych.

| Licznik | Stan |
| --- | --- |
| Dysk logiczny (dysk fizyczny) — czas bezczynności (%) |<li> 100% czasu bezczynności do 50% bezczynności = w dobrej kondycji</br><li> 49% bezczynności do 20% bezczynności = ostrzeżenie lub monitor</br><li> 19% bezczynności do 0% bezczynności = krytyczne lub poza specyfikacją |
| Dysk logiczny (dysk fizyczny) — średni czas odczytu lub zapisu dysku w s |<li> 0,001 MS do 0,015 MS = dobra kondycja</br><li> 0,015 MS do 0,025 MS = ostrzeżenie lub monitor</br><li> 0,026 MS lub dłużej = krytyczne lub poza specyfikacją |
| Dysk logiczny (dysk fizyczny) — bieżąca długość kolejki dysku (dla wszystkich wystąpień) |80 żądań przez ponad 6 minut |
| Pamięć — Bajty puli niestronicowanej |<li> Mniej niż 60% używanej puli = dobra kondycja<br><li> 61% do 80% używanej puli = ostrzeżenie lub monitor</br><li> Więcej niż 80% zużywanej puli = krytyczne lub poza specyfikacją |
| Pamięć — bajty w puli stronicowanej |<li> Mniej niż 60% używanej puli = dobra kondycja</br><li> 61% do 80% używanej puli = ostrzeżenie lub monitor</br><li> Więcej niż 80% zużywanej puli = krytyczne lub poza specyfikacją |
| Pamięć — dostępne megabajty |<li> 50% dostępnej wolnej pamięci lub więcej = dobra kondycja</br><li> 25% dostępnej wolnej pamięci = monitor</br><li>10% dostępnej wolnej pamięci = ostrzeżenie</br><li> Mniej niż 100 MB lub 5% dostępnej wolnej pamięci = krytyczne lub poza specyfikacją |
| Procesor — \% czas procesora (wszystkie wystąpienia) |<li> Mniej niż 60% zużyte = w dobrej kondycji</br><li> 61% do 90% zużyte = monitor lub przestroga</br><li> 91% do 100% zużyte = krytyczny |

> [!NOTE]
> Jeśli określisz, że infrastruktura jest przyczynaa, zalecamy regularne defragmentowanie dysków w celu zapewnienia lepszej wydajności.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Przyczyna: inny proces lub oprogramowanie antywirusowe zakłócające działanie Azure Backup

Zaobserwowano kilka wystąpień, w których inne procesy w systemie Windows mają negatywny wpływ na wydajność procesu agenta Azure Backup. Na przykład, jeśli używasz agenta Azure Backup i innego programu do tworzenia kopii zapasowych danych lub jeśli oprogramowanie antywirusowe jest uruchomione i ma blokadę plików, których kopia zapasowa ma zostać utworzona, wiele blokad dla plików może spowodować rywalizację. W takiej sytuacji tworzenie kopii zapasowej może zakończyć się niepowodzeniem lub zadanie może trwać dłużej niż oczekiwano.

Najlepszym zaleceniem w tym scenariuszu jest wyłączenie innego programu do tworzenia kopii zapasowych, aby sprawdzić, czy czas tworzenia kopii zapasowej dla agenta Azure Backup ulega zmianie. Zwykle w celu zapewnienia, że wiele zadań tworzenia kopii zapasowych nie jest uruchomionych w tym samym czasie, wystarczy, aby zapobiec wpływowi ich na siebie nawzajem.

W przypadku programów antywirusowych zaleca się wykluczenie następujących plików i lokalizacji:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* C:\Program Files\Microsoft Azure Recovery Services Agent \ foldery
* Lokalizacja zapasowa (jeśli nie używasz lokalizacji standardowej)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Przyczyna: Agent kopii zapasowej działający na maszynie wirtualnej platformy Azure

Jeśli na maszynie wirtualnej jest uruchomiony agent kopii zapasowej, wydajność będzie wolniejsza niż podczas uruchamiania na komputerze fizycznym. Jest to oczekiwane ze względu na ograniczenia liczby IOPS.  Można jednak zoptymalizować wydajność, przełączając dyski danych, których kopie zapasowe są tworzone w usłudze Azure Premium Storage. Pracujemy nad rozwiązaniem tego problemu, a poprawka będzie dostępna w przyszłej wersji.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Przyczyna: wykonywanie kopii zapasowej dużej liczby (milionów) plików

Przeniesienie dużej ilości danych zajmie więcej czasu niż przeniesienie mniejszej ilości danych. W niektórych przypadkach czas tworzenia kopii zapasowej jest związany z nie tylko rozmiarem danych, ale również liczbą plików lub folderów. Jest to szczególnie ważne, gdy tworzona jest kopia zapasowa milionów małych plików (kilka bajtów do kilku kilobajtów).

Takie zachowanie występuje, ponieważ podczas tworzenia kopii zapasowej danych i przechodzenia do platformy Azure, platforma Azure jednocześnie wykazuje pliki. W niektórych rzadkich scenariuszach operacja katalogu może trwać dłużej niż oczekiwano.

Następujące wskaźniki mogą pomóc zrozumieć wąskie gardło i odpowiednio wykonać kolejne kroki:

* **Interfejs użytkownika pokazuje postęp transferu danych**. Dane nadal są transferowane. Przepustowość sieci lub rozmiar danych może powodować opóźnienia.
* **Interfejs użytkownika nie pokazuje postępu transferu danych**. Otwórz dzienniki zlokalizowane w folderze C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, a następnie sprawdź wpis FileProvider:: EndData w dziennikach. Ten wpis oznacza, że proces transferu danych zakończył pracę i jest wykonywane działanie katalogu. Nie Anuluj zadań tworzenia kopii zapasowej. Zamiast tego poczekaj chwilę na zakończenie operacji katalogu. Jeśli problem będzie się powtarzać, skontaktuj się z [pomocą techniczną platformy Azure](https://portal.azure.com/#create/Microsoft.Support).

Jeśli próbujesz utworzyć kopię zapasową dużych dysków, zaleca się użycie [Azure Data Box](./offline-backup-azure-data-box.md) do pierwszej kopii zapasowej (replikacja początkowa).  Jeśli nie można użyć urządzenie Data Box, wszystkie przejściowe problemy z siecią występujące w danym środowisku podczas długotrwałego przesyłania danych przez sieć mogą powodować błędy kopii zapasowych.  Aby zapewnić ochronę przed awariami, można dodać kilka folderów do początkowej kopii zapasowej i stale dodawać więcej folderów do momentu pomyślnego utworzenia kopii zapasowej wszystkich folderów na platformie Azure.  Kolejne przyrostowe kopie zapasowe będą stosunkowo szybsze.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące tworzenia kopii zapasowych plików i folderów](backup-azure-file-folder-backup-faq.md)
