---
title: Rozwiązywanie problemów z powolnym tworzeniem kopii zapasowych plików i folderów
description: Zawiera wskazówki dotyczące rozwiązywania problemów, które ułatwiają diagnozowanie przyczyny Azure Backup problemów z wydajnością
ms.topic: troubleshooting
ms.date: 07/05/2019
ms.openlocfilehash: 791f0edf5f50d27147e402f09e7a3e4c2ea7ca43
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107518528"
---
# <a name="troubleshoot-slow-backup-of-files-and-folders-in-azure-backup"></a>Rozwiązywanie problemów związanych z powolnym tworzeniem kopii zapasowych plików i folderów w usłudze Azure Backup

Ten artykuł zawiera wskazówki dotyczące rozwiązywania problemów, które ułatwiają diagnozowanie przyczyny powolnej wydajności tworzenia kopii zapasowych plików i folderów podczas korzystania z Azure Backup. Jeśli używasz agenta Azure Backup do tworzenia kopii zapasowych plików, proces tworzenia kopii zapasowej może trwać dłużej niż oczekiwano. To opóźnienie może być spowodowane przez co najmniej jedną z następujących czynności:

* [Istnieją wąskie gardła wydajności na komputerze, dla których jest dawana kopii zapasowej.](#cause1)
* [Inny proces lub oprogramowanie antywirusowe zakłóca proces Azure Backup oprogramowania antywirusowego.](#cause2)
* [Agent usługi Backup jest uruchomiony na maszynie wirtualnej platformy Azure.](#cause3)  
* [Masz kopię zapasową dużej liczby (milionów) plików.](#cause4)

Przed rozpoczęciem rozwiązywania problemów zalecamy pobranie i zainstalowanie najnowszego agenta [Azure Backup programu](https://aka.ms/azurebackup_agent). Często aktualizujemy agenta kopii zapasowej, aby rozwiązać różne problemy, dodać funkcje i zwiększyć wydajność.

Zdecydowanie zalecamy również przejrzenie często zadawanych pytań dotyczących [Azure Backup,](backup-azure-backup-faq.yml) aby upewnić się, że nie występują żadne typowe problemy z konfiguracją.

[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

## <a name="cause-backup-job-running-in-unoptimized-mode"></a>Przyczyna: Zadanie tworzenia kopii zapasowej uruchomione w trybie niezoptymalizowanym

* Agent MARS może uruchomić zadanie  tworzenia kopii zapasowej w trybie zoptymalizowanym przy użyciu dziennika zmian USN (numeru sekwencji aktualizacji) lub **niezoptymalizowanego** trybu, sprawdzając zmiany w katalogach lub plikach przez skanowanie całego woluminu.
* Tryb niezoptymalizowany jest wolny, ponieważ agent musi przeskanować każdy plik na woluminie i porównać go z metadanymi, aby określić zmienione pliki.
* Aby to sprawdzić, otwórz okno **Szczegóły** zadania z konsoli agenta usługi MARS i sprawdź stan, aby sprawdzić, czy wyświetlany jest stan Transferowanie danych **(niezoptymalizowane,** może zająć więcej czasu), jak pokazano poniżej:

    ![Uruchamianie w trybie niezoptymalizowanym](./media/backup-azure-troubleshoot-slow-backup-performance-issue/unoptimized-mode.png)

* Następujące warunki mogą spowodować uruchomienie zadania tworzenia kopii zapasowej w trybie niezoptymalizowanym:
  * Pierwsza kopia zapasowa (znana także jako replikacja początkowa) będzie zawsze uruchamiana w trybie niezoptymalizowanym
  * Jeśli poprzednie zadanie tworzenia kopii zapasowej zakończy się niepowodzeniem, następne zaplanowane zadanie tworzenia kopii zapasowej zostanie uruchomione jako niezoptymalizowane.

<a id="cause1"></a>

## <a name="cause-performance-bottlenecks-on-the-computer"></a>Przyczyna: Wąskie gardła wydajności na komputerze

Wąskie gardła na komputerze, dla których jest dawana kopię zapasową, mogą powodować opóźnienia. Na przykład zdolność komputera do odczytu lub zapisu na dysku lub dostępna przepustowość do wysyłania danych za pośrednictwem sieci może powodować wąskie gardła.

System Windows udostępnia wbudowane narzędzie o [nazwie monitor wydajności](https://techcommunity.microsoft.com/t5/ask-the-performance-team/windows-performance-monitor-overview/ba-p/375481) (Perfmon) do wykrywania tych wąskich gardeł.

Poniżej znajdują się liczniki wydajności i zakresy, które mogą być przydatne w diagnozowaniu wąskich gardeł w celu uzyskania optymalnych kopii zapasowych.

| Licznik | Stan |
| --- | --- |
| Dysk logiczny (dysk fizyczny)--%bezczynny |<li> 100% bezczynności do 50% bezczynności = W dobrej kondycji</br><li> 49% bezczynności do 20% bezczynności = ostrzeżenie lub monitor</br><li> 19% bezczynności do 0% bezczynności = krytyczne lub poza specyfikacją |
| Dysk logiczny (dysk fizyczny)--%średni czas odczytu lub zapisu dysku |<li> Od 0,001 ms do 0,015 ms = w dobrej kondycji</br><li> Od 0,015 ms do 0,025 ms = ostrzeżenie lub monitor</br><li> 0,026 ms lub więcej = krytyczne lub poza specyfikacją |
| Dysk logiczny (dysk fizyczny) — bieżąca długość kolejki dysku (dla wszystkich wystąpień) |80 żądań przez ponad 6 minut |
| Pamięć — bajty niestronicowane puli |<li> Mniej niż 60% zużytej puli = w dobrej kondycji<br><li> Od 61% do 80% zużytej puli = ostrzeżenie lub monitor</br><li> Więcej niż 80% zużytej puli = krytyczna lub poza specyfikacją |
| Pamięć — bajty stronicowane w puli |<li> Mniej niż 60% zużytej puli = w dobrej kondycji</br><li> Od 61% do 80% zużytej puli = ostrzeżenie lub monitor</br><li> Więcej niż 80% zużytej puli = krytyczna lub poza specyfikacją |
| Pamięć — dostępne megabajty |<li> 50% dostępnej wolnej pamięci lub więcej = W dobrej kondycji</br><li> 25% dostępnej wolnej pamięci = Monitor</br><li>10% dostępnej wolnej pamięci = ostrzeżenie</br><li> Mniej niż 100 MB lub 5% dostępnej wolnej pamięci = krytyczne lub poza specyfikacją |
| Procesor — \% czas procesora (wszystkie wystąpienia) |<li> Mniej niż 60% zużytych = w dobrej kondycji</br><li> Używane: od 61% do 90% = monitorowanie lub przestroga</br><li> Używane od 91% do 100% = krytyczne |

> [!NOTE]
> Jeśli ustalisz, że infrastruktura jest winna, zalecamy regularne defragmentację dysków w celu lepszej wydajności.
>
>

<a id="cause2"></a>

## <a name="cause-another-process-or-antivirus-software-interfering-with-azure-backup"></a>Przyczyna: Inny proces lub oprogramowanie antywirusowe zakłócające Azure Backup

Widzieliśmy kilka wystąpień, w których inne procesy w systemie Windows mają negatywny wpływ na wydajność procesu Azure Backup agenta. Jeśli na przykład używasz zarówno agenta programu Azure Backup, jak i innego programu do kopii zapasowej danych lub jeśli oprogramowanie antywirusowe jest uruchomione i ma blokadę na plikach, których kopię zapasową chcesz wrócić, wiele blokad plików może powodować kontrę. W takiej sytuacji tworzenie kopii zapasowej może zakończyć się niepowodzeniem lub zadanie może trwać dłużej niż oczekiwano.

Najlepszym zaleceniem w tym scenariuszu jest wyłączenie innego programu do tworzenia kopii zapasowych, aby sprawdzić, czy czas wykonywania kopii zapasowej dla agenta Azure Backup się. Zwykle upewnianie się, że wiele zadań tworzenia kopii zapasowej nie jest uruchomionych w tym samym czasie, jest wystarczające, aby zapobiec ich wpływowi na siebie.

W przypadku programów antywirusowych zaleca się wykluczenie następujących plików i lokalizacji:

* C:\Program Files\Microsoft Azure Recovery Services Agent\bin\cbengine.exe jako proces
* Foldery C:\Program Files\Microsoft Azure Recovery Services Agent\
* Lokalizacja na początku (jeśli nie używasz lokalizacji standardowej)

<a id="cause3"></a>

## <a name="cause-backup-agent-running-on-an-azure-virtual-machine"></a>Przyczyna: Agent kopii zapasowej uruchomiony na maszynie wirtualnej platformy Azure

Jeśli używasz agenta kopii zapasowej na maszynie wirtualnej, wydajność będzie niższa niż w przypadku uruchamiania go na maszynie fizycznej. Jest to oczekiwane z powodu ograniczeń dotyczących IOPS.  Można jednak zoptymalizować wydajność, przełączając dyski danych, których kopię zapasową chcesz utworzyć, na platformę Azure Premium Storage. Pracujemy nad rozwiązaniem tego problemu, a poprawka będzie dostępna w przyszłej wersji.

<a id="cause4"></a>

## <a name="cause-backing-up-a-large-number-millions-of-files"></a>Przyczyna: Kopii zapasowej dużej liczby (milionów) plików

Przenoszenie dużej ilości danych będzie trwać dłużej niż przenoszenie mniejszej ilości danych. W niektórych przypadkach czas wykonywania kopii zapasowej jest związany nie tylko z rozmiarem danych, ale także z liczbą plików lub folderów. Dzieje się tak zwłaszcza wtedy, gdy są kopii zapasowej milionów małych plików (od kilku bajtów do kilku kilobajtów).

To zachowanie występuje, ponieważ podczas kopii zapasowej danych i przenoszenia ich na platformę Azure platforma Azure jednocześnie kataloguje pliki. W niektórych rzadkich scenariuszach operacja katalogu może trwać dłużej niż oczekiwano.

Następujące wskaźniki mogą pomóc w zrozumieniu wąskiego gardła i odpowiednio pracować nad kolejnymi krokami:

* **Interfejs użytkownika pokazuje postęp transferu danych.** Dane są nadal przesyłane. Przepustowość sieci lub rozmiar danych mogą powodować opóźnienia.
* **Interfejs użytkownika nie pokazuje postępu transferu danych.** Otwórz dzienniki znajdujące się w folderze C:\Program Files\Microsoft Azure Recovery Services Agent\Temp, a następnie sprawdź wpis FileProvider::EndData w dziennikach. Ten wpis oznacza, że transfer danych został zakończony i że odbywa się operacja katalogu. Nie anuluj zadań tworzenia kopii zapasowej. Zamiast tego poczekaj nieco dłużej na zakończenie operacji katalogu. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą [techniczną platformy Azure.](https://portal.azure.com/#create/Microsoft.Support)

Jeśli próbujesz utworzyć kopię zapasową dużych dysków, zaleca się użycie funkcji [Azure Data Box](./offline-backup-azure-data-box.md) pierwszej kopii zapasowej (replikacji początkowej).  Jeśli nie możesz użyć usługi urządzenie Data Box, wszelkie przejściowe problemy z siecią, które mają miejsce w twoim środowisku podczas długich transferów danych za pośrednictwem sieci, mogą powodować błędy tworzenia kopii zapasowej.  Aby zabezpieczyć się przed tymi awariami, możesz dodać kilka folderów do początkowej kopii zapasowej i przyrostowo dodawać kolejne foldery do momentu pomyślnego tworzenia kopii zapasowej wszystkich folderów na platformie Azure.  Kolejne przyrostowe kopie zapasowe będą względnie szybsze.

## <a name="next-steps"></a>Następne kroki

* [Często zadawane pytania dotyczące kopii zapasowej plików i folderów](backup-azure-file-folder-backup-faq.yml)
