---
title: Przywracanie woluminu z kopii zapasowej na serii StorSimple 8000 | Microsoft Docs
description: Wyjaśnia, jak używać wykazu kopii zapasowych usługi StorSimple Menedżer urządzeń do przywracania woluminu StorSimple z zestawu kopii zapasowych.
services: storsimple
documentationcenter: NA
author: alkohli
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: how-to
ms.date: 07/15/2020
ms.author: alkohli
ms.openlocfilehash: ff70df225b5ed27960c96889d409d7005f0fbcc4
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950738"
---
# <a name="restore-a-storsimple-volume-from-a-backup-set"></a>Przywracanie woluminu StorSimple z zestawu kopii zapasowych

## <a name="overview"></a>Omówienie

Ten samouczek zawiera opis operacji przywracania wykonanej na urządzeniu z serii StorSimple 8000 przy użyciu istniejącego zestawu kopii zapasowych. Użyj bloku **wykazu kopii zapasowych** , aby przywrócić wolumin z kopii zapasowej lokalnego lub w chmurze. Blok **wykazu kopii zapasowych** zawiera wszystkie zestawy kopii zapasowych, które są tworzone podczas wykonywania ręcznych lub zautomatyzowanych kopii zapasowych. Operacja przywracania z zestawu kopii zapasowych natychmiast przenosi wolumin w tryb online, gdy dane są pobierane w tle.

## <a name="before-you-restore"></a>Przed przystąpieniem do przywracania

Przed rozpoczęciem przywracania zapoznaj się z następującymi zastrzeżeniami:

* **Musisz przełączyć wolumin w tryb offline** — Przełącz wolumin do trybu offline na hoście i urządzeniu przed zainicjowaniem operacji przywracania. Mimo że operacja przywracania automatycznie przenosi wolumin w tryb online na urządzeniu, należy ręcznie przełączyć urządzenie w tryb online na hoście. Wolumin można przenieść do trybu online na hoście, gdy tylko wolumin jest w trybie online na urządzeniu. (Nie musisz czekać na zakończenie operacji przywracania). W przypadku procedur przejdź do [trybu offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline).

* **Typ woluminu po** przywróceniu — woluminy usunięte są przywracane na podstawie typu w migawce; oznacza to, że woluminy przypięte lokalnie są przywracane jako woluminy warstwowe przypięte lokalnie i woluminy, które zostały warstwowe, są przywracane.

    W przypadku istniejących woluminów bieżący typ użycia woluminu zastępuje typ, który jest przechowywany w migawce. Na przykład, jeśli wolumin został przywrócony z migawki, która została wykonana podczas warstwowego typu woluminu i ten typ woluminu jest teraz przypięty lokalnie (z powodu operacji konwersji, która została wykonana), wolumin zostanie przywrócony jako wolumin przypięty lokalnie. Podobnie, jeśli istniejący wolumin przypięty lokalnie został powiększony, a następnie przywrócony ze starszej migawki pobranej, gdy wolumin był mniejszy, przywrócony wolumin zachowa bieżący rozmiar.

    Nie można skonwertować woluminu z woluminu warstwowego na wolumin przypięty lokalnie lub z woluminu przypiętego lokalnie do woluminu warstwowego podczas przywracania woluminu. Poczekaj na zakończenie operacji przywracania, a następnie możesz przekonwertować wolumin na inny typ. Aby uzyskać informacje o konwertowaniu woluminu, przejdź do pozycji [Zmień typ woluminu](storsimple-8000-manage-volumes-u2.md#change-the-volume-type). 

* **Rozmiar woluminu jest odzwierciedlany na przywracanym woluminie** — jest to istotne zagadnienie, jeśli przywracasz wolumin przypięty lokalnie, który został usunięty (ponieważ woluminy przypięte lokalnie są w pełni obsługiwane). Przed próbą przywrócenia woluminu przypiętego lokalnie, upewnij się, że jest wystarczająca ilość miejsca.

* **Nie można rozszerzyć woluminu podczas** przywracania — poczekaj na zakończenie operacji przywrócenia przed podjęciem próby rozszerzenia woluminu. Aby uzyskać informacje o rozszerzaniu woluminu, przejdź do obszaru [Modyfikowanie woluminu](storsimple-8000-manage-volumes-u2.md#modify-a-volume).

* **Podczas przywracania woluminu lokalnego można wykonać kopię zapasową** — w przypadku procedur przejdź do [usługi StorSimple Menedżer urządzeń, aby zarządzać zasadami tworzenia kopii zapasowych](storsimple-8000-manage-backup-policies-u2.md).

* **Możesz anulować operację przywracania** — w przypadku anulowania zadania przywracania wolumin zostanie przywrócony do stanu, który był w stanie przed zainicjowaniem operacji przywracania. W przypadku procedur przejdź do pozycji [Anuluj zadanie](storsimple-8000-manage-jobs-u2.md#cancel-a-job).

## <a name="how-does-restore-work"></a>Jak działa przywracanie

W przypadku urządzeń z aktualizacją Update 4 lub nowszym zaimplementowano przywracanie oparte na mapę cieplną. Ponieważ Host żąda dostępu do danych na urządzeniu, te żądania są śledzone i tworzony jest mapę cieplną. Duża liczba żądań skutkuje fragmentami danych o wyższej wartości cieplnej, a niższa szybkość żądania przekłada się na fragmenty z niższą temperaturą. Musisz uzyskać dostęp do danych co najmniej dwa razy, aby można było je oznaczyć jako _gorącą_. Modyfikowany plik jest również oznaczony jako _gorąca_. Po zainicjowaniu przywracania, aktywne odwodnienie danych odbywa się na podstawie mapę cieplną. W przypadku wersji wcześniejszych niż aktualizacja 4 dane są pobierane podczas przywracania tylko na podstawie dostępu.

Następujące zastrzeżenia dotyczą przywracania opartego na mapę cieplną:

* Śledzenie mapę cieplną jest włączone tylko dla woluminów warstwowych i woluminy przypięte lokalnie nie są obsługiwane.

* Przywracanie oparte na mapę cieplną nie jest obsługiwane w przypadku klonowania woluminu na innym urządzeniu. 

* Jeśli na urządzeniu istnieje przywracanie w miejscu i lokalna migawka dla woluminu, który ma zostać przywrócony, nie zostanie on ponownie odwodniony (ponieważ dane są już dostępne lokalnie). 

* Domyślnie podczas przywracania zadania uzupełniania są inicjowane w celu aktywnego odzyskania danych opartych na mapę cieplną. 

W Update 4 polecenia cmdlet programu Windows PowerShell mogą służyć do wykonywania zapytań dotyczących uruchamiania zadań uzupełniania, anulowania zadania uzupełniania i uzyskiwania stanu zadania uzupełniania.

* `Get-HcsRehydrationJob` — To polecenie cmdlet pobiera stan zadania uzupełniania. Pojedyncze zadanie uzupełniania jest wyzwalane dla jednego woluminu.

* `Set-HcsRehydrationJob` — To polecenie cmdlet pozwala wstrzymywać, zatrzymywać, wznawiać zadanie uzupełniania, gdy trwa uzupełnianie.

Aby uzyskać więcej informacji na temat uzupełniania poleceń cmdlet, przejdź do [dokumentacji poleceń cmdlet programu Windows PowerShell dla StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).

Przy automatycznym uzupełnianiu oczekiwana jest zazwyczaj wyższa szybkość przejściowego odczytu. Rzeczywista wielkość ulepszeń zależy od różnych czynników, takich jak wzorzec dostępu, zmiany danych i typ danych. 

Aby anulować zadanie uzupełniania, można użyć polecenia cmdlet programu PowerShell. Jeśli chcesz trwale wyłączyć zadania uzupełniania dla wszystkich przyszłych operacji przywracania, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md).

## <a name="how-to-use-the-backup-catalog"></a>Jak korzystać z wykazu kopii zapasowych

Blok **wykazu kopii zapasowych** zawiera zapytanie, które pomaga zawęzić wybór zestawu kopii zapasowych. Zestawy kopii zapasowych, które są pobierane, można filtrować na podstawie następujących parametrów:

* **Zakres czasu** — Data i godzina utworzenia zestawu kopii zapasowych.
* **Urządzenie** — urządzenie, na którym został utworzony zestaw kopii zapasowych.
* Zasady lub **wolumin** **kopii zapasowej** — zasady kopii zapasowej lub woluminy skojarzone z tym zestawem kopii zapasowych.

Filtrowane zestawy kopii zapasowych są następnie wyświetlane na podstawie następujących atrybutów:

* **Name** — Nazwa zasad lub woluminu kopii zapasowej skojarzonych z zestawem kopii zapasowych.
* **Typ** — zestawy kopii zapasowych mogą być migawkami lokalnymi lub migawkami w chmurze. Lokalna migawka to kopia zapasowa wszystkich danych woluminu przechowywanych lokalnie na urządzeniu, natomiast migawka w chmurze odwołuje się do kopii zapasowej danych woluminu znajdujących się w chmurze. Migawki lokalne zapewniają szybszy dostęp, podczas gdy migawki chmur są wybrane do odporności danych.
* **Size** — rzeczywisty rozmiar zestawu kopii zapasowych.
* Data i **godzina utworzenia kopii** zapasowych. 
* **Woluminy** — liczba woluminów skojarzonych z zestawem kopii zapasowych.
* **Zainicjowane** — kopie zapasowe mogą być inicjowane automatycznie zgodnie z harmonogramem lub ręcznie przez użytkownika. (Aby zaplanować tworzenie kopii zapasowych, można użyć zasad tworzenia kopii zapasowych. Alternatywnie możesz użyć opcji **Wykonaj kopię zapasową** , aby wykonać kopię zapasową interaktywną lub na żądanie.

## <a name="how-to-restore-your-storsimple-volume-from-a-backup"></a>Jak przywrócić wolumin StorSimple z kopii zapasowej

Możesz użyć bloku **wykazu kopii zapasowych** , aby przywrócić wolumin StorSimple z określonej kopii zapasowej. Należy jednak pamiętać, że przywrócenie woluminu spowoduje przywrócenie woluminu do stanu, w którym był używany podczas tworzenia kopii zapasowej. Wszystkie dane, które zostały dodane po operacji tworzenia kopii zapasowej, zostaną utracone.

> [!WARNING]
> Przywracanie z kopii zapasowej spowoduje zastąpienie istniejących woluminów z kopii zapasowej. Może to spowodować utratę wszelkich danych, które zostały nagrane po wykonaniu kopii zapasowej.


### <a name="to-restore-your-volume"></a>Aby przywrócić wolumin
1. Przejdź do usługi StorSimple Menedżer urządzeń, a następnie kliknij pozycję **wykaz kopii zapasowych**.

2. Wybierz zestaw kopii zapasowych w następujący sposób:
   
   1. Określ zakres czasu.
   2. Wybierz odpowiednie urządzenie.
   3. Z listy rozwijanej wybierz wolumin lub zasady kopii zapasowej dla kopii zapasowej, którą chcesz wybrać.
   4. Kliknij przycisk **Zastosuj** , aby wykonać to zapytanie.

      Kopie zapasowe skojarzone z wybranym woluminem lub zasadami tworzenia kopii zapasowych powinny pojawić się na liście zestawów kopii zapasowych.
   
      ![Lista zestawów kopii zapasowych](./media/storsimple-8000-restore-from-backup-set-u2/bucatalog.png)     
     
3. Rozwiń zestaw kopii zapasowych, aby wyświetlić skojarzone woluminy. Aby można było przywrócić te woluminy, należy przełączyć je do trybu offline na hoście i urządzeniu. Uzyskaj dostęp do woluminów w bloku **woluminy** urządzenia, a następnie wykonaj kroki w [trybie offline](storsimple-8000-manage-volumes-u2.md#take-a-volume-offline) , aby przełączyć je w tryb offline.
   
   > [!IMPORTANT]
   > Upewnij się, że woluminy zostały najpierw przeniesione do trybu offline na hoście przed przełączeniem woluminów w tryb offline na urządzeniu. Jeśli woluminy nie są przełączane do trybu offline na hoście, może to potencjalnie spowodować uszkodzenie danych.
   
4. Przejdź z powrotem do karty **wykaz kopii zapasowych** i wybierz zestaw kopii zapasowych. Kliknij prawym przyciskiem myszy, a następnie z menu kontekstowego wybierz polecenie **Przywróć**.

    ![Lista zestawów kopii zapasowych 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu1.png)

5. Pojawi się monit o potwierdzenie. Przejrzyj informacje o przywracaniu, a następnie zaznacz pole wyboru potwierdzenie.
   
    ![Strona potwierdzenia](./media/storsimple-8000-restore-from-backup-set-u2/restorebu2.png)

7. Kliknij przycisk **Przywróć**. Spowoduje to zainicjowanie zadania przywracania, które można wyświetlić, uzyskując dostęp do strony **zadania** .

   ![Strona potwierdzenia 2](./media/storsimple-8000-restore-from-backup-set-u2/restorebu5.png)

8. Po zakończeniu przywracania Sprawdź, czy zawartość woluminów jest zastępowana przez woluminy z kopii zapasowej.


## <a name="if-the-restore-fails"></a>Jeśli przywracanie nie powiedzie się

Jeśli z jakiegoś powodu operacja przywracania zakończy się niepowodzeniem, zostanie wyświetlony alert. W takim przypadku Odśwież listę kopii zapasowych, aby sprawdzić, czy kopia zapasowa jest nadal ważna. Jeśli kopia zapasowa jest prawidłowa i przywracasz z chmury, problemy z łącznością mogą być przyczyną problemu.

Aby ukończyć operację przywracania, przełącz wolumin w tryb offline na hoście, a następnie spróbuj ponownie wykonać operację przywracania. Należy zauważyć, że wszelkie modyfikacje danych woluminu, które zostały wykonane podczas procesu przywracania, zostaną utracone.

## <a name="next-steps"></a>Następne kroki
* Dowiedz się, jak [zarządzać woluminami StorSimple](storsimple-8000-manage-volumes-u2.md).
* Dowiedz się [, jak zarządzać urządzeniem StorSimple przy użyciu usługi StorSimple Menedżer urządzeń](storsimple-8000-manager-service-administration.md).