---
title: Migrowanie danych z serii StorSimple 5000-7000 do urządzenia z serii 8000 | Microsoft Docs
description: Dowiedz się więcej na temat migrowania danych z serii StorSimple 5000-7000 do urządzenia z serii 8000 i wymagań wstępnych dotyczących procesu migracji.
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
ms.date: 09/25/2020
ms.author: alkohli
ms.openlocfilehash: f6fffadd3c53f67af2e4c833a6a1d442c18efa0b
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/27/2020
ms.locfileid: "91398190"
---
# <a name="migrate-data-from-storsimple-5000-7000-series-to-8000-series-device"></a>Migrowanie danych z serii StorSimple 5000-7000 do urządzenia z serii 8000

> [!IMPORTANT]
> - W dniu 31 lipca 2019 seria StorSimple 5000/7000 osiągnie status końca wsparcia (EOS). Firma Microsoft zaleca, aby klienci z serii StorSimple 5000/7000 migrowani do jednej z wariantów opisanych w dokumencie.
> - Migracja jest obecnie asystowaną operacją. Jeśli planujesz migrację danych z urządzenia z serii StorSimple 5000-7000 do urządzenia z serii 8000, musisz zaplanować migrację za pomocą pomoc techniczna firmy Microsoft. W pomoc techniczna firmy Microsoft następnie zostanie włączona subskrypcja migracji. Aby uzyskać więcej informacji, zobacz jak [otworzyć bilet pomocy technicznej](storsimple-8000-contact-microsoft-support.md).
> - Po zapisaniu żądania obsługi może upłynąć kilka tygodni, aby wykonać plan migracji i ostatecznie rozpocząć migrację.
> - Przed skontaktowaniem się z pomoc techniczna firmy Microsoft upewnij się, że zapoznaj się z [wymaganiami wstępnymi](#migration-prerequisites) dotyczącymi migracji określonymi w artykule.

## <a name="overview"></a>Omówienie

W tym artykule wprowadzono funkcję migracji, która umożliwia klientom serii StorSimple 5000-7000 Migrowanie danych do urządzenia fizycznego z serii StorSimple 8000 lub 8010/8020 urządzenia w chmurze. Ten artykuł zawiera również instrukcje krok po kroku dotyczące czynności wymaganych do przeprowadzenia migracji danych z starszego urządzenia z serii 5000-7000 do urządzenia fizycznego lub w chmurze z serii 8000.

Ten artykuł dotyczy zarówno lokalnego urządzenia z serii 8000, jak i urządzenia w chmurze StorSimple.


## <a name="migration-feature-versus-host-side-migration"></a>Funkcja migracji a migracja po stronie hosta

Dane można przenieść przy użyciu funkcji migracji lub migracji po stronie hosta. W tej sekcji opisano szczegółowe informacje o każdej z tych metod, w tym specjalistów i wady. Skorzystaj z tych informacji, aby ustalić, którą metodę należy wykonać, aby przeprowadzić migrację danych.

Funkcja migracji symuluje proces odzyskiwania po awarii (DR) z serii 7000/5000 do 8000 serii. Ta funkcja umożliwia migrację danych z formatu serii 5000/7000 do formatu serii 8000 na platformie Azure. Proces migracji jest inicjowany za pomocą narzędzia migracji StorSimple. Narzędzie uruchamia pobieranie i konwersję metadanych kopii zapasowych na urządzeniu z serii 8000, a następnie używa najnowszej kopii zapasowej, aby uwidocznić woluminy na urządzeniu.

| Zalety                                                                                                                                     |Wady                                                                                                                                                              |
|-------------------------------------------------------------------------------------------------------------------------------------------|-------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Proces migracji zachowuje historię kopii zapasowych wykonanych w serii 5000/7000.                                               | Gdy użytkownicy próbują uzyskać dostęp do danych, migracja pobierze dane z platformy Azure w ten sposób, co spowoduje policzenie kosztów pobierania danych.                                     |
| Na stronie hosta nie są migrowane żadne dane.                                                                                                     | Proces wymaga przestoju między rozpoczęciem tworzenia kopii zapasowej a ostatnią kopią zapasową w serii 8000 (można go oszacować przy użyciu narzędzia migracji). |
| Ten proces zachowuje wszystkie zasady, szablony przepustowości, szyfrowanie i inne ustawienia na urządzeniach z serii 8000.                      | Użytkownik uzyskuje dostęp tylko do danych, do których użytkownicy uzyskują dostęp, i nie będzie odgrzać całego zestawu danych.                                                  |
| Ten proces wymaga dodatkowego czasu na przekonwertowanie wszystkich starszych kopii zapasowych na platformie Azure, które są wykonywane asynchronicznie bez wpływu na produkcję. | Migracja można przeprowadzić tylko na poziomie konfiguracji chmury.  Nie można osobno migrować poszczególnych woluminów w konfiguracji chmury                       |

Migracja po stronie hosta umożliwia niezależne skonfigurowanie serii 8000 i skopiowanie danych z urządzenia z serii 5000/7000 do urządzenia z serii 8000. Jest to równoznaczne z migracją danych z jednego urządzenia magazynującego do drugiego. Do kopiowania danych służą różne narzędzia, takie jak Diskboss, Robocopy.

| Zalety                                                                                                                      |Wady                                                                                                                                                                                                      |
|---------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
| Migracja może być zbliżana w zależności od ilości woluminu.                                               | Poprzednie kopie zapasowe (wykonane w serii 5000/7000) nie będą dostępne na urządzeniu z serii 8000.                                                                                                       |
| Umożliwia konsolidowanie danych na jedno konto magazynu na platformie Azure.                                                       | Pierwsza kopia zapasowa w chmurze na 8000 serii będzie trwać dłużej, ponieważ wszystkie dane z serii 8000 muszą mieć kopię zapasową na platformie Azure.                                                                     |
| Po pomyślnej migracji wszystkie dane są lokalne na urządzeniu. Brak opóźnień podczas uzyskiwania dostępu do danych. | Użycie usługi Azure Storage zwiększy się do momentu usunięcia danych z urządzenia 5000/7000.                                                                                                        |
|                                                                                                                           | Jeśli urządzenie serii 7000/5000 zawiera dużą ilość danych, podczas migracji te dane muszą zostać pobrane z platformy Azure, co wiąże się z kosztami i opóźnieniami związanymi z pobieraniem danych z platformy Azure |

Ten artykuł koncentruje się tylko na funkcji migracji z 5000/7000 do 8000 serii urządzeń. Aby uzyskać więcej informacji na temat migracji po stronie hosta, przejdź do [migracji z innych urządzeń magazynujących](https://download.microsoft.com/download/9/4/A/94AB8165-CCC4-430B-801B-9FD40C8DA340/Migrating%20Data%20to%20StorSimple%20Volumes_09-02-15.pdf).

## <a name="migration-prerequisites"></a>Wymagania wstępne dotyczące migracji

Poniżej przedstawiono wymagania wstępne dotyczące migracji dla starszego urządzenia serii 5000 lub 7000 oraz urządzenia 8000 Series StorSimple.

> [!IMPORTANT]
> Zapoznaj się z wymaganiami wstępnymi dotyczącymi migracji i wypełnij je przed zapisaniem żądania obsługi w pomoc techniczna firmy Microsoft.

### <a name="for-the-50007000-series-device-source"></a>Dla urządzenia z serii 5000/7000 (Źródło)

Przed rozpoczęciem migracji upewnij się, że:

* Masz urządzenie źródłowe serii 5000 lub 7000; urządzenie może być aktywne lub wyłączone.

    > [!IMPORTANT]
    > Zalecamy, aby na tym urządzeniu był dostęp szeregowy w trakcie procesu migracji. Jeśli występują problemy z urządzeniami, dostęp szeregowy może pomóc w rozwiązywaniu problemów.

* Na urządzeniu źródłowym z serii 5000 lub 7000 jest uruchomione oprogramowanie 2.1.1.518 w wersji v lub nowszej. Wcześniejsze wersje nie są obsługiwane.
* Aby sprawdzić, czy wersja 5000 lub 7000 jest uruchomiona, zobacz prawy górny róg interfejsu użytkownika sieci Web. Powinna to być wyświetlana wersja oprogramowania, która jest uruchomiona na urządzeniu. W przypadku migracji seria 5000 lub 7000 powinna mieć uruchomioną 2.1.1.518.

    ![Sprawdź wersję oprogramowania na starszych urządzeniach](media/storsimple-8000-migrate-from-5000-7000/check-version-legacy-device1.png)

    * Jeśli na urządzeniu działającym na żywo nie jest uruchomiony program 2.1.1.518 lub nowszy, Uaktualnij system do wymaganej minimalnej wersji. Może być konieczne współdziałanie z pomoc techniczna firmy Microsoft, aby ułatwić wykonanie uaktualnienia.
    * Jeśli używasz programu v 2.1.1.518, przejdź do interfejsu użytkownika sieci Web, aby sprawdzić, czy są jakieś powiadomienia o błędach przywracania rejestru. Jeśli przywracanie rejestru zakończyło się niepowodzeniem, uruchom przywracanie rejestru. Może być konieczne współdziałanie z pomoc techniczna firmy Microsoft, aby ułatwić przywrócenie rejestru.
    * Jeśli masz wyłączone urządzenie, na którym nie uruchomiono programu 2.1.1.518, wykonaj przejście w tryb failover na urządzenie zastępcze z systemem 2.1.1.518. Szczegółowe instrukcje znajdują się w dokumencie DR of 5000/7000 Series StorSimple.
    * Wykonaj kopię zapasową danych urządzenia, wykonując migawkę w chmurze.
    * Sprawdź inne aktywne zadania tworzenia kopii zapasowej uruchomione na urządzeniu źródłowym. Obejmuje to zadania na hoście konsoli ochrony danych StorSimple. Poczekaj na zakończenie bieżących zadań.


### <a name="for-the-8000-series-physical-device-target"></a>Dla urządzenia fizycznego z serii 8000 (target)

Przed rozpoczęciem migracji upewnij się, że:

* Docelowe urządzenie serii 8000 zostało zarejestrowane i uruchomione. Aby uzyskać więcej informacji, zobacz jak [wdrożyć urządzenie StorSimple w usłudze StorSimple Manager](storsimple-8000-deployment-walkthrough-u2.md).
* Na urządzeniu z serii 8000 jest zainstalowana najnowsza wersja aktualizacji StorSimple 8000 Seria 5 z systemem 6.3.9600.17845 lub nowszą. Jeśli na urządzeniu nie ma zainstalowanych najnowszych aktualizacji, należy zainstalować najnowsze aktualizacje przed kontynuowaniem migracji. Aby uzyskać więcej informacji, zobacz jak [zainstalować najnowszą aktualizację na urządzeniu z serii 8000](storsimple-8000-install-update-5.md).
* Twoja subskrypcja platformy Azure jest włączona na potrzeby migracji. Jeśli subskrypcja nie jest włączona, skontaktuj się z firmą pomoc techniczna firmy Microsoft, aby umożliwić migrację.

### <a name="for-the-80108020-cloud-appliance-target"></a>Dla urządzenia w chmurze 8010/8020 (miejsce docelowe)

Przed rozpoczęciem migracji upewnij się, że:

* Urządzenie w chmurze docelowej zostało zarejestrowane i uruchomione. Aby uzyskać więcej informacji, zobacz jak [wdrożyć urządzenie w chmurze StorSimple i zarządzać nim](storsimple-8000-cloud-appliance-u2.md).
* Na urządzeniu w chmurze jest uruchomiona Najnowsza wersja oprogramowania StorSimple 8000 Series Update 5 6.3.9600.17845. Jeśli urządzenie w chmurze nie ma uruchomionej aktualizacji Update 5, przed kontynuowaniem migracji Utwórz nowe urządzenie w chmurze Update 5. Aby uzyskać więcej informacji, zobacz jak [utworzyć urządzenie w chmurze 8010/8020](storsimple-8000-cloud-appliance-u2.md).

### <a name="for-the-computer-running-storsimple-migration-tool"></a>Dla komputera z uruchomionym narzędziem migracji StorSimple

Narzędzie migracji StorSimple to narzędzie oparte na interfejsie użytkownika, które umożliwia Migrowanie danych z serii StorSimple 5000-7000 do urządzenia z serii 8000. Aby zainstalować narzędzie do migracji StorSimple, należy użyć komputera, który spełnia następujące wymagania.

Komputer ma łączność z Internetem i:

* Uruchomiony jest następujący system operacyjny
    * Windows 10.
    * System Windows Server 2012 R2 (lub nowszy) do zainstalowania narzędzia migracji StorSimple.
* Ma zainstalowany program .NET 4.5.2.
* Co najmniej 5 GB wolnego miejsca do zainstalowania i korzystania z tego narzędzia.

> [!TIP]
> Jeśli urządzenie StorSimple jest połączone z hostem systemu Windows Server, można zainstalować narzędzie migracji na komputerze hosta z systemem Windows Server.

#### <a name="to-install-storsimple-migration-tool"></a>Aby zainstalować narzędzie migracji StorSimple

Wykonaj następujące kroki, aby zainstalować narzędzie do migracji StorSimple na komputerze.

1. Skopiuj folder _StorSimple8000SeriesMigrationTool_ na komputer z systemem Windows. Upewnij się, że dysk, na którym jest skopiowane oprogramowanie, ma wystarczającą ilość miejsca.

    Otwórz plik konfiguracji narzędzia _StorSimple8000SeriesMigrationTool.exe.config_ w folderze. Oto fragment pliku.
    
    ```xml
        <add key="UserName" value="username@xyz.com" />
        <add key="SubscriptionName" value="YourSubscriptionName" />
        <add key="SubscriptionId" value="YourSubscriptionId" />
        <add key="TenantId" value="YourTenantId" />
        <add key="ResourceName" value="YourResourceName" />
        <add key="ResourceGroupName" value="YourResourceGroupName" />

    ```
2. Edytuj wartości odpowiadające kluczom i Zamień na:

    * `UserName` — Nazwa użytkownika, aby zalogować się do Azure Portal.
    * `SubscriptionName and SubscriptionId` — Nazwa i Identyfikator subskrypcji platformy Azure. Na stronie docelowa usługi StorSimple Menedżer urządzeń w obszarze **Ogólne**kliknij pozycję **Właściwości**. Skopiuj nazwę subskrypcji i Identyfikator subskrypcji skojarzonej z usługą.
    * `ResourceName` — Nazwa usługi StorSimple Menedżer urządzeń w Azure Portal. Podano również w obszarze właściwości usługi.
    * `ResourceGroup` — Nazwa grupy zasobów skojarzonej z usługą StorSimple Menedżer urządzeń w Azure Portal. Podano również w obszarze właściwości usługi.
    ![Sprawdź właściwości usługi dla urządzenia docelowego](media/storsimple-8000-migrate-from-5000-7000/check-service-properties1.png)
    * `TenantId` — Azure Active Directory identyfikator dzierżawy w Azure Portal. Zaloguj się do Microsoft Azure jako administrator. W Microsoft Azure Portal kliknij pozycję **Azure Active Directory**. W obszarze **Zarządzanie** kliknij przycisk **Właściwości**. Identyfikator dzierżawy jest wyświetlany w polu **Identyfikator katalogu** .
    ![Sprawdź identyfikator dzierżawy dla Azure Active Directory](media/storsimple-8000-migrate-from-5000-7000/check-tenantid-aad.png)

3.  Zapisz zmiany wprowadzone w pliku konfiguracji.
4.  Uruchom _StorSimple8000SeriesMigrationTool.exe_ , aby uruchomić narzędzie. Po wyświetleniu monitu o podanie poświadczeń podaj poświadczenia skojarzone z subskrypcją w Azure Portal. 
5.  Zostanie wyświetlony interfejs użytkownika narzędzia migracji StorSimple.
  

## <a name="next-steps"></a>Następne kroki
Pobierz instrukcje krok po kroku dotyczące [migrowania danych z serii StorSimple 5000-7000 do urządzenia z serii 8000](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b).
