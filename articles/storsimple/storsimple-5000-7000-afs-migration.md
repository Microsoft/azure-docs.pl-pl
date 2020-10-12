---
title: Migrowanie danych z serii StorSimple 5000-7000 do Azure File Sync | Microsoft Docs
description: Opisuje sposób migrowania danych z serii StorSimple 5000/7000 do Azure File Sync (AFS).
services: storsimple
documentationcenter: NA
author: alkohli
manager: twooley
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/19/2019
ms.author: alkohli
ms.openlocfilehash: 5d656fd8757580b8ce96acf168e92fc847d400ca
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85514059"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-azure-file-sync"></a>Migrowanie danych z serii StorSimple 5000-7000 do Azure File Sync

> [!IMPORTANT]
> 9 lipca 2019 seria StorSimple 5000/7000 osiągnie status końca wsparcia (EOS). Firma Microsoft zaleca, aby klienci z serii StorSimple 5000/7000 migrowani do jednej z wariantów opisanych w dokumencie.

Migracja danych to proces przenoszenia danych z jednej lokalizacji magazynu do innej. Obejmuje to dokładną kopię aktualnych danych organizacji z jednego urządzenia do innego, najlepiej bez zakłócania ani wyłączania aktywnych aplikacji, a następnie przekierowywanie wszystkich działań wejścia/wyjścia (we/wy) do nowego urządzenia. 

Urządzenia magazynujące z serii StorSimple 5000 i 7000 uzyskają dostęp do końca usługi w lipcu 2019. Oznacza to, że firma Microsoft nie będzie już w stanie obsługiwać sprzętu i oprogramowania dla serii StorSimple 5000/7000 po lipca 2019. Klienci korzystający z tych urządzeń powinni migrować swoje dane StorSimple do innych rozwiązań magazynu hybrydowego na platformie Azure. W tym artykule omówiono migrację danych z urządzenia z serii StorSimple 5000/7000 do Azure File Sync (AFS).

## <a name="intended-audience"></a>Docelowi odbiorcy

Ten artykuł jest przeznaczony dla specjalistów IT i pracowników merytorycznych odpowiedzialnych za wdrażanie urządzeń z serii StorSimple 5000/7000 w centrum danych i zarządzanie nimi. Klienci korzystający z urządzeń StorSimple do obciążeń serwera plików (z systemem Windows Server) mogą znaleźć tę ścieżkę migracji, szczególnie odnoszące się do niej. Jeśli uważasz, że funkcje Azure File Sync dobrze działały w organizacji, ten artykuł pomoże Ci zrozumieć, jak przenieść się do tych rozwiązań z StorSimple.

## <a name="migration-considerations"></a>Zagadnienia dotyczące migracji

Ten proces działa w przypadku klientów, którzy skonfigurowali udział plików systemu Windows przy użyciu woluminu StorSimple na potrzeby magazynu. Migrowanie danych z StorSimple 5000/7000 do Azure File Sync obejmuje przekonwertowanie tej lokalizacji udziału plików na [punkt końcowy serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning) , a następnie użycie lokalnie dołączonych dysków jako innego punktu końcowego, który stanie się nową lokalizacją. 

Podczas przejścia do programu AFS należy rozważyć następujące kwestie:

1. Azure Files jest obecnie ograniczeniem do 5 TB/udziału. To ograniczenie można przezwyciężyć, korzystając z Azure File Sync z rozproszeniem danych między wieloma udziałami plików platformy Azure. Aby uzyskać więcej informacji, zapoznaj się ze [wzorcem wzrostu danych Azure Files wdrożenia](https://docs.microsoft.com/azure/storage/files/storage-files-planning).
2. Ta migracja pobiera cały podstawowy zestaw danych na urządzenie lokalne, ponieważ kopia danych jest wykonywana z urządzenia lokalnego. Upewnij się, że masz wystarczającą przepustowość, aby pomieścić ten transfer.
3. Ten proces nie zachowuje migawek, które zostały już utworzone. Migruje tylko dane podstawowe. Proces ten nie zachowuje również skojarzonych z nimi szablonów przepustowości ani zasad tworzenia kopii zapasowych. [Użyj Azure Backup](https://docs.microsoft.com/azure/backup/backup-azure-files) , aby skonfigurować zasady tworzenia kopii zapasowych po migracji danych w udziale plików platformy Azure.
4. StorSimple zapewnia sprzęt pierwszej firmy. Jednak w przypadku Azure Files/Azure File Sync używany jest własny lokalny sprzęt systemu Windows Server jako lokalna pamięć podręczna. Musisz upewnić się, że masz wystarczającą pojemność magazynu, aby zapewnić, że zestaw danych ma być skonfigurowany jako lokalny. Aby uzyskać więcej informacji o warstwach i ustawianiu wymaganego miejsca docelowego wolne miejsce, zapoznaj się z tematem [Tworzenie punktu końcowego serwera podczas wdrażania Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal). 
5. Zapoznaj się z [cennikiem dla Azure File Sync](https://azure.microsoft.com/pricing/details/storage/files/) , ponieważ różni się od StorSimple. AFS nie ma deduplikacji i kompresji, takich jak StorSimple.

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

W tym miejscu znajdziesz wymagania wstępne dotyczące migracji dla starszego urządzenia serii 5000 lub 7000 do Azure File Sync. Przed rozpoczęciem upewnij się, że masz:

- Dostęp do urządzenia z serii StorSimple 5000/7000 z oprogramowaniem w wersji v 2.1.1.518 lub nowszym. Wcześniejsze wersje nie są obsługiwane. W prawym górnym rogu interfejsu użytkownika sieci Web urządzenia StorSimple powinna być wyświetlana uruchomiona wersja oprogramowania.  
    Jeśli na urządzeniu nie uruchomiono programu 2.1.1.518, Uaktualnij system do wymaganej minimalnej wersji. Aby uzyskać szczegółowe instrukcje, zapoznaj się z artykułem [Uaktualnij system do wersji v 2.1.1.518](http://onlinehelp.storsimple.com/111_Appliance/6_System_Upgrade_Guides/Current_(v2.1.1)/000_Software_Patch_Upgrade_Guide_v2.1.1.518).
- Sprawdź, czy istnieją aktywne zadania tworzenia kopii zapasowej uruchomione na urządzeniu źródłowym. Obejmuje to zadania na hoście konsoli ochrony danych StorSimple. Poczekaj na zakończenie bieżących zadań. 
- Dostęp do hosta z systemem Windows Server podłączonym do urządzenia z serii StorSimple 5000-7000. Na hoście musi być uruchomiona obsługiwana wersja systemu Windows Server zgodnie z opisem w temacie [współdziałanie Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-planning).
- Woluminy StorSimple są zainstalowane na hoście i zawierają udziały plików.
- Host ma wystarczającą ilość miejsca do przechowywania danych przechowywanych lokalnie w pamięci podręcznej.
- Dostęp na poziomie właściciela do subskrypcji platformy Azure, która będzie używana do wdrażania Azure File Sync. Podczas tworzenia punktu końcowego w chmurze dla grupy synchronizacji mogą wystąpić problemy, jeśli nie masz uprawnień na poziomie właściciela lub administratora.
- Dostęp do [konta magazynu ogólnego przeznaczenia w wersji 2](https://docs.microsoft.com/azure/storage/common/storage-account-overview) z udziałem plików platformy Azure, do którego chcesz przeprowadzić synchronizację. Aby uzyskać więcej informacji, zobacz temat [Tworzenie konta](https://docs.microsoft.com/azure/storage/common/storage-quickstart-create-account).
  - Jak [utworzyć udział plików platformy Azure](https://docs.microsoft.com/azure/storage/files/storage-how-to-create-file-share).

## <a name="migration-process"></a>Proces migracji

Migrowanie danych z StorSimple 5000-7000 do platformy AFS jest procesem dwuetapowym:
1.  Replikowanie danych z lokalnego serwera plików, w którym woluminy StorSimple są instalowane do udziału Azure Files.  Replikacja odbywa się za pośrednictwem instalowanego agenta AFS.
2.  Odłącz urządzenie StorSimple. Na dyskach lokalnych działa jako lokalna pamięć podręczna.

### <a name="migration-steps"></a>Kroki migracji

Wykonaj następujące kroki, aby zmigrować udział plików systemu Windows skonfigurowany na woluminach StorSimple do udziału Azure File Sync. 
1.  Wykonaj te kroki na tym samym hoście systemu Windows Server, na którym są zainstalowane woluminy StorSimple lub użyj innego systemu. 
    - [Przygotuj system Windows Server do użycia z Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#prepare-windows-server-to-use-with-azure-file-sync).
    - [Zainstaluj agenta Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#install-the-azure-file-sync-agent).
    - [Wdróż usługę synchronizacji magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#deploy-the-storage-sync-service). 
    - [Zarejestruj system Windows Server z usługą synchronizacji magazynu](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#register-windows-server-with-storage-sync-service). 
    - [Utwórz grupę synchronizacji i punkt końcowy w chmurze](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide#create-a-sync-group-and-a-cloud-endpoint). Należy wykonać grupy synchronizacji dla każdego udziału plików systemu Windows, który należy zmigrować z hosta.
    - [Utwórz punkt końcowy serwera](https://docs.microsoft.com/azure/storage/files/storage-sync-files-deployment-guide?tabs=portal#create-a-server-endpoint). Określ ścieżkę jako ścieżkę woluminu StorSimple zawierającego dane udziału plików. Na przykład jeśli wolumin StorSimple ma wartość Drive `J` , a Twoje dane znajdują się w `J:/<myafsshare>` , Dodaj tę ścieżkę jako punkt końcowy serwera. Pozostaw **warstwy** jako **wyłączone**.
2.  Poczekaj na zakończenie synchronizacji serwera plików. Upewnij się, że dla każdego serwera w danej grupie synchronizacji:
    - Sygnatury czasowe ostatniej próby synchronizacji zarówno do przekazywania, jak i pobierania są ostatnie.
    - Stan jest zielony w przypadku przekazywania i pobierania.
    - W **działaniu synchronizacji** są wyświetlane bardzo mało plików, które mają zostać zsynchronizowane.
    - Pliki, które **nie są synchronizowane** , to 0 w przypadku przekazywania i pobierania.
    Aby uzyskać więcej informacji na temat kończenia synchronizacji serwera, przejdź do obszaru [Rozwiązywanie problemów Azure File Sync](https://docs.microsoft.com/azure/storage/files/storage-sync-files-troubleshoot?tabs=portal1%2Cportal#how-do-i-know-if-my-servers-are-in-sync-with-each-other). Synchronizacja może zająć kilka godzin, w zależności od rozmiaru danych i przepustowości. Po zakończeniu synchronizacji wszystkie dane są bezpiecznie w udziale plików platformy Azure. 
3.  Przejdź do udziałów na woluminach StorSimple. Wybierz udział, kliknij prawym przyciskiem myszy i wybierz pozycję **Właściwości**. Zwróć uwagę na uprawnienia udziału w obszarze **zabezpieczenia**. Te uprawnienia należy zastosować ręcznie do nowego udziału w późniejszym kroku.
4.  W zależności od tego, czy używany jest ten sam Host systemu Windows Server, czy inny, kolejne kroki będą różne.

    Pomiń ten krok i przejdź do następnego kroku, jeśli używasz innego hosta z systemem Windows Server. Jeśli używasz tego samego serwera plików systemu Windows dla programu AFS, będziesz mieć już kilka minut przestoju. 
    - **Początek przestoju** — usuwa punkt końcowy serwera, który został utworzony w *kroku 1F*. 
    - Utwórz nowy punkt końcowy serwera z ścieżką, w której mają być przechowywane dane.
    - Gdy punkt końcowy serwera wyświetli się w dobrej kondycji (może to potrwać kilka minut), dane zostaną wyświetlone w nowej lokalizacji. Teraz można skonfigurować hosta systemu Windows Server do obsługi plików z tej nowej lokalizacji. - **Zakończenie przestoju**.
5.  Jeśli używasz innego serwera plików systemu Windows do Azure File Sync, nie będzie żadnych przestojów. 
    - Dodaj inny punkt końcowy serwera ze ścieżką lokalnego magazynu, który ma być używany jako pamięć podręczna zamiast urządzenia StorSimple. 
    - Pliki na nowym serwerze będą widoczne w ciągu kilku minut. Przełączenie z urządzenia StorSimple do nowej lokalizacji na hoście jest bezpłatne.

    > [!TIP] 
    > Należy rozważyć skonfigurowanie tego nowego udziału plików o tej samej nazwie i tej samej ścieżce co zastępowane, aby zminimalizować zakłócenia. W przypadku korzystania z systemu plików DFS-N może być wymagane wprowadzenie zmian w konfiguracji.
6.  Skonfiguruj ponownie uprawnienia udostępniania zgodnie z opisem w *kroku 3*.

Jeśli wystąpią jakiekolwiek problemy podczas migracji danych, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md). 



## <a name="next-steps"></a>Następne kroki

Jeśli AFS nie jest odpowiednim rozwiązaniem, Dowiedz się, jak [migrować dane z serii StorSimple 5000-7000 do urządzenia z serii 8000](storsimple-8000-migrate-from-5000-7000.md).

