---
title: Wdrażanie usługi StorSimple Menedżer urządzeń na platformie Azure | Microsoft Docs
description: Poznaj kroki wymagane do tworzenia, usuwania, migracji usługi i zarządzania kluczem rejestracji usługi.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/09/2018
ms.author: alkohli
ms.openlocfilehash: 66244bd8e24ff62be41df72f7a39c0ce0ed13135
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360703"
---
# <a name="deploy-the-storsimple-device-manager-service-for-storsimple-8000-series-devices"></a>Wdrażanie usługi StorSimple Menedżer urządzeń dla urządzeń z serii StorSimple 8000

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Omówienie

Usługa StorSimple Menedżer urządzeń działa w Microsoft Azure i łączy się z wieloma urządzeniami StorSimple. Po utworzeniu usługi można za jej pomocą zarządzać wszystkimi urządzeniami połączonymi z usługą StorSimple Menedżer urządzeń z jednej lokalizacji centralnej, co zmniejsza obciążenie administracyjne.

W tym samouczku opisano kroki wymagane do tworzenia, usuwania, migracji usługi i zarządzania kluczem rejestracji usługi. Informacje zawarte w tym artykule dotyczą tylko urządzeń z serii StorSimple 8000. Aby uzyskać więcej informacji na temat macierzy wirtualnych StorSimple, przejdź do [wdrożenia usługi StorSimple Menedżer urządzeń dla macierzy wirtualnej StorSimple](storsimple-virtual-array-manage-service.md).

> [!NOTE]
> -  Azure Portal obsługuje urządzenia z aktualizacją Update 5,0 lub nowszą. Jeśli urządzenie nie jest aktualne, należy natychmiast zainstalować aktualizację Update 5. Aby uzyskać więcej informacji, przejdź do [Install Update 5](storsimple-8000-install-update-5.md). 
> - W przypadku korzystania z urządzenia w chmurze StorSimple (8010/8020) nie można zaktualizować urządzenia w chmurze. Użyj najnowszej wersji oprogramowania w celu utworzenia nowego urządzenia w chmurze z aktualizacją 5,0, a następnie przełączenia w tryb failover do nowo utworzonego urządzenia w chmurze. 
> - Wszystkie urządzenia z aktualizacją Update 4,0 lub wcześniejszą spowodują zmniejszenie funkcjonalności zarządzania. 

## <a name="create-a-service"></a>Tworzenie usługi
Aby utworzyć usługę StorSimple Menedżer urządzeń, musisz dysponować:

* Subskrypcja z Umowa Enterprise
* Konto magazynu Active Microsoft Azure
* Informacje dotyczące rozliczeń, które są używane do zarządzania dostępem

Dozwolone są tylko subskrypcje z Umowa Enterprise. Podczas tworzenia usługi można również generować domyślne konto magazynu.

Pojedyncza usługa może zarządzać wieloma urządzeniami. Jednak urządzenie nie może obejmować wielu usług. Duże przedsiębiorstwo może mieć wiele wystąpień usługi do pracy z różnymi subskrypcjami, organizacjami lub nawet lokalizacjami wdrażania. 

> [!NOTE]
> Potrzebujesz oddzielnych wystąpień usługi StorSimple Menedżer urządzeń, aby zarządzać urządzeniami z serii StorSimple 8000 i macierzami wirtualnymi StorSimple.

Wykonaj następujące kroki, aby utworzyć usługę.

[!INCLUDE [storsimple-create-new-service](../../includes/storsimple-8000-create-new-service.md)]


Dla każdej usługi StorSimple Menedżer urządzeń istnieją następujące atrybuty:

* **Name** — nazwa przypisana do usługi StorSimple Menedżer urządzeń, gdy została utworzona. **Nazwy usługi nie można zmienić po utworzeniu usługi. Dotyczy to również innych obiektów, takich jak urządzenia, woluminy, kontenery woluminów i zasady tworzenia kopii zapasowych, których nazwy nie można zmienić w Azure Portal.**
* **Status** — stan usługi, która może być **aktywna** , **utworzona** lub w **trybie online**.
* **Location** — lokalizacja geograficzna, w której zostanie wdrożone Urządzenie StorSimple.
* **Subskrypcja** — subskrypcja rozliczeń skojarzona z usługą.

## <a name="delete-a-service"></a>Usuwanie usługi

Przed usunięciem usługi upewnij się, że żadne podłączone urządzenia nie korzystają z niej. Jeśli usługa jest używana, Dezaktywuj połączone urządzenia. Operacja Dezaktywuj spowoduje nawiązanie połączenia między urządzeniem i usługą, ale zachowa dane urządzenia w chmurze.

> [!IMPORTANT]
> Po usunięciu usługi nie można cofnąć tej operacji. Wszystkie urządzenia, które używały usługi, muszą zostać zresetowane do domyślnych ustawień fabrycznych, zanim będą mogły być używane z inną usługą. W tym scenariuszu dane lokalne na urządzeniu oraz konfiguracja zostaną utracone.

Wykonaj następujące kroki, aby usunąć usługę.

### <a name="to-delete-a-service"></a>Aby usunąć usługę

1. Wyszukaj usługę, którą chcesz usunąć. Kliknij ikonę **zasoby** , a następnie wprowadź odpowiednie warunki do wyszukania. W wynikach wyszukiwania kliknij usługę, którą chcesz usunąć.

    ![Usługa wyszukiwania do usunięcia](./media/storsimple-8000-manage-service/deletessdevman1.png)

2. Spowoduje to przejście do bloku usługi StorSimple Menedżer urządzeń. Kliknij polecenie **Usuń**.

    ![Usuń usługę](./media/storsimple-8000-manage-service/deletessdevman2.png)

3. Kliknij przycisk **tak** w powiadomieniu o potwierdzeniu. Usunięcie usługi może potrwać kilka minut.

    ![Potwierdzenie usunięcia](./media/storsimple-8000-manage-service/deletessdevman3.png)

## <a name="get-the-service-registration-key"></a>Pobieranie klucza rejestracji usługi

Po pomyślnym utworzeniu usługi konieczne będzie zarejestrowanie urządzenia z systemem StorSimple w usłudze. Aby zarejestrować swoje pierwsze urządzenie StorSimple, będzie potrzebny klucz rejestracji usługi. Do zarejestrowania dodatkowych urządzeń za pomocą istniejącej usługi StorSimple wymagany jest zarówno klucz rejestracji, jak i klucz szyfrowania danych usługi (który jest generowany na pierwszym urządzeniu podczas rejestracji). Aby uzyskać więcej informacji na temat klucza szyfrowania danych usługi, zobacz [zabezpieczenia StorSimple](storsimple-8000-security.md). Klucz rejestracji można uzyskać, uzyskując dostęp do **kluczy** w bloku StorSimple Menedżer urządzeń.

Wykonaj następujące kroki, aby uzyskać klucz rejestracji usługi.

[!INCLUDE [storsimple-8000-get-service-registration-key](../../includes/storsimple-8000-get-service-registration-key.md)]

Przechowuj klucz rejestracji usługi w bezpiecznym miejscu. Ten klucz będzie potrzebny, a także klucz szyfrowania danych usługi, aby zarejestrować dodatkowe urządzenia w tej usłudze. Po uzyskaniu klucza rejestracji usługi należy skonfigurować urządzenie za pomocą interfejsu program Windows PowerShell dla usługi StorSimple.

Aby uzyskać szczegółowe informacje na temat korzystania z tego klucza rejestracji, zobacz [krok 3. Konfigurowanie i rejestrowanie urządzenia za pomocą program Windows PowerShell dla usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple).

## <a name="regenerate-the-service-registration-key"></a>Wygeneruj ponownie klucz rejestracji usługi
Należy ponownie wygenerować klucz rejestracji usługi, jeśli wymagane jest przeprowadzenie wymiany kluczy lub zmiana listy administratorów usługi. W przypadku ponownego wygenerowania klucza nowy klucz jest używany tylko do rejestrowania kolejnych urządzeń. Ten proces nie ma na nie wpływ na urządzenia, które zostały już zarejestrowane.

Wykonaj następujące kroki, aby ponownie wygenerować klucz rejestracji usługi.

### <a name="to-regenerate-the-service-registration-key"></a>Aby ponownie wygenerować klucz rejestracji usługi
1. W bloku **StorSimple Menedżer urządzeń** przejdź do pozycji **klucze** **zarządzania &gt;** .
    
    ![Przejdź do bloku klucze](./media/storsimple-8000-manage-service/regenregkey2.png)

2. W bloku **klucze** kliknij polecenie **Generuj ponownie**.

    ![Kliknij pozycję Wygeneruj ponownie](./media/storsimple-8000-manage-service/regenregkey3.png)
3. W bloku ponowne **generowanie klucza rejestracji usługi** zapoznaj się z akcją wymaganą, gdy klucze zostaną ponownie wygenerowane. Wszystkie kolejne urządzenia zarejestrowane w ramach tej usługi używają nowego klucza rejestracji. Kliknij przycisk **Wygeneruj** ponownie, aby potwierdzić. Po zakończeniu ponownej generacji otrzymasz powiadomienie.

    ![Potwierdź ponowne wygenerowanie](./media/storsimple-8000-manage-service/regenregkey4.png)

4. Zostanie wyświetlony nowy klucz rejestracji usługi.

5. Skopiuj ten klucz i Zapisz go, aby zarejestrować nowe urządzenia w tej usłudze.



## <a name="change-the-service-data-encryption-key"></a>Zmień klucz szyfrowania danych usługi
Klucze szyfrowania danych usługi są używane do szyfrowania poufnych danych klienta, takich jak poświadczenia konta magazynu, które są wysyłane z usługi StorSimple Manager do urządzenia StorSimple. Należy okresowo zmienić te klucze, jeśli organizacja IT ma kluczowe zasady rotacji na urządzeniach magazynujących. Proces zmiany klucza może się nieco różnić w zależności od tego, czy istnieje jedno urządzenie lub wiele urządzeń zarządzanych przez usługę StorSimple Manager. Aby uzyskać więcej informacji, przejdź do [StorSimple zabezpieczenia i ochrona danych](storsimple-8000-security.md).

Zmiana klucza szyfrowania danych usługi jest procesem trójwymiarowym:

1. Za pomocą skryptów programu Windows PowerShell dla Azure Resource Manager Autoryzuj urządzenie, aby zmienić klucz szyfrowania danych usługi.
2. Przy użyciu program Windows PowerShell dla usługi StorSimple zainicjuj zmianę klucza szyfrowania danych usługi.
3. Jeśli masz więcej niż jedno urządzenie StorSimple, zaktualizuj klucz szyfrowania danych usługi na innych urządzeniach.

### <a name="step-1-use-windows-powershell-script-to-authorize-a-device-to-change-the-service-data-encryption-key"></a>Krok 1. używanie skryptu programu Windows PowerShell do autoryzacji urządzenia do zmiany klucza szyfrowania danych usługi
Zazwyczaj administrator urządzenia będzie żądać, aby administrator usługi autoryzuje urządzenie w celu zmiany kluczy szyfrowania danych usługi. Następnie administrator usługi autoryzuje urządzenie, aby zmienić klucz.

Ten krok jest wykonywany przy użyciu skryptu opartego na Azure Resource Manager. Administrator usługi może wybrać urządzenie, które ma uprawnienia do autoryzacji. Urządzenie jest następnie autoryzowane do uruchomienia procesu zmiany klucza szyfrowania danych usługi. 

Aby uzyskać więcej informacji o używaniu skryptu, przejdź do [Authorize-ServiceEncryptionRollover.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Authorize-ServiceEncryptionRollover.ps1)

#### <a name="which-devices-can-be-authorized-to-change-service-data-encryption-keys"></a>Które urządzenia mogą być autoryzowane, aby można było zmienić klucze szyfrowania danych usługi?
Aby można było autoryzować zmiany klucza szyfrowania danych usługi, urządzenie musi spełniać następujące kryteria:

* Urządzenie musi być w trybie online, aby można było kwalifikować się do autoryzacji zmiany klucza szyfrowania danych usługi.
* Możesz autoryzować to samo urządzenie ponownie po 30 minutach, jeśli zmiana klucza nie została zainicjowana.
* Można autoryzować inne urządzenie, pod warunkiem, że zmiana klucza nie została zainicjowana przez wcześniej autoryzowane urządzenie. Gdy nowe urządzenie zostanie autoryzowane, stare urządzenie nie może zainicjować zmiany.
* Nie można autoryzować urządzenia, gdy trwa Przerzucanie klucza szyfrowania danych usługi.
* Możesz autoryzować urządzenie, gdy niektóre z urządzeń zarejestrowanych w usłudze przekroczą szyfrowanie, a inne nie. 

### <a name="step-2-use-windows-powershell-for-storsimple-to-initiate-the-service-data-encryption-key-change"></a>Krok 2. użycie program Windows PowerShell dla usługi StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi
Ten krok jest wykonywany w interfejsie program Windows PowerShell dla usługi StorSimple na autoryzowanym urządzeniu StorSimple.

> [!NOTE]
> Nie można wykonać żadnych operacji w Azure Portal usługi StorSimple Manager, dopóki nie zostanie zakończone Przerzucanie kluczy.


Jeśli używasz konsoli szeregowej urządzenia do nawiązywania połączenia z interfejsem programu Windows PowerShell, wykonaj następujące czynności.

#### <a name="to-initiate-the-service-data-encryption-key-change"></a>Aby zainicjować zmianę klucza szyfrowania danych usługi
1. Wybierz opcję 1, aby zalogować się z pełnymi prawami dostępu.
2. W wierszu polecenia wpisz polecenie:
   
     `Invoke-HcsmServiceDataEncryptionKeyChange`
3. Po pomyślnym ukończeniu tego polecenia cmdlet otrzymasz nowy klucz szyfrowania danych usługi. Skopiuj i Zapisz ten klucz do użycia w kroku 3 tego procesu. Ten klucz zostanie użyty do zaktualizowania wszystkich pozostałych urządzeń zarejestrowanych w usłudze StorSimple Manager.
   
   > [!NOTE]
   > Ten proces musi zostać zainicjowany w ciągu czterech godzin od autoryzowania urządzenia StorSimple.
   > 
   > 
   
   Ten nowy klucz jest następnie wysyłany do usługi w celu wypychania do wszystkich urządzeń zarejestrowanych w usłudze. Zostanie wyświetlony alert na pulpicie nawigacyjnym usługi. Usługa spowoduje wyłączenie wszystkich operacji na zarejestrowanych urządzeniach, a następnie administrator urządzenia będzie musiał zaktualizować klucz szyfrowania danych usługi na innych urządzeniach. Jednak systemy I/OS (hosty wysyłające dane do chmury) nie zostaną zakłócone.
   
   Jeśli masz pojedyncze urządzenie zarejestrowane w usłudze, proces przerzucania jest teraz zakończony i możesz pominąć następny krok. Jeśli masz wiele urządzeń zarejestrowanych w usłudze, przejdź do kroku 3.

### <a name="step-3-update-the-service-data-encryption-key-on-other-storsimple-devices"></a>Krok 3. aktualizowanie klucza szyfrowania danych usługi na innych urządzeniach StorSimple
Te kroki należy wykonać w interfejsie programu Windows PowerShell urządzenia StorSimple, jeśli istnieje wiele urządzeń zarejestrowanych w usłudze StorSimple Manager. Aby zaktualizować wszystkie pozostałe urządzenia StorSimple zarejestrowane w usłudze StorSimple Manager, należy użyć klucza uzyskanego w kroku 2.

Wykonaj następujące kroki, aby zaktualizować szyfrowanie danych usługi na urządzeniu.

#### <a name="to-update-the-service-data-encryption-key-on-physical-devices"></a>Aby zaktualizować klucz szyfrowania danych usługi na urządzeniach fizycznych
1. Użyj program Windows PowerShell dla usługi StorSimple, aby nawiązać połączenie z konsolą programu. Wybierz opcję 1, aby zalogować się z pełnymi prawami dostępu.
2. W wierszu polecenia wpisz:  `Invoke-HcsmServiceDataEncryptionKeyChange – ServiceDataEncryptionKey`
3. Podaj klucz szyfrowania danych usługi uzyskany w [kroku 2: użyj program Windows PowerShell dla usługi StorSimple do zainicjowania zmiany klucza szyfrowania danych usługi](#to-initiate-the-service-data-encryption-key-change).

#### <a name="to-update-the-service-data-encryption-key-on-all-the-80108020-cloud-appliances"></a>Aby zaktualizować klucz szyfrowania danych usługi na wszystkich urządzeniach chmury 8010/8020
1. Pobierz i skonfiguruj [Update-CloudApplianceServiceEncryptionKey.ps1](https://github.com/anoobbacker/storsimpledevicemgmttools/blob/master/Update-CloudApplianceServiceEncryptionKey.ps1) skrypt programu PowerShell. 
2. Otwórz program PowerShell i w wierszu polecenia wpisz:  `Update-CloudApplianceServiceEncryptionKey.ps1 -SubscriptionId [subscription] -TenantId [tenantid] -ResourceGroupName [resource group] -ManagerName [device manager]`

Ten skrypt zapewni, że klucz szyfrowania danych usługi jest ustawiony na wszystkich urządzeniach chmury 8010/8020 w ramach Menedżera urządzeń.

## <a name="supported-operations-on-devices-running-versions-prior-to-update-50"></a>Obsługiwane operacje na urządzeniach z wersjami wcześniejszymi niż aktualizacja 5,0
W Azure Portal obsługiwane są tylko urządzenia StorSimple z aktualizacją Update 5,0 i nowsze. Urządzenia, na których są uruchomione starsze wersje, mają ograniczoną pomoc techniczną. Po przeprowadzeniu migracji do Azure Portal Skorzystaj z poniższej tabeli, aby zrozumieć, które operacje są obsługiwane na urządzeniach z wersjami wcześniejszych niż aktualizacja 5,0.

| Operacja                                                                                                                       | Obsługiwane      |
|---------------------------------------------------------------------------------------------------------------------------------|----------------|
| Rejestrowanie urządzenia                                                                                                               | Tak            |
| Konfigurowanie ustawień urządzenia, takich jak ogólne, Sieć i zabezpieczenia                                                                | Tak            |
| Skanowanie, pobieranie i instalowanie aktualizacji                                                                                             | Tak            |
| Dezaktywuj urządzenie                                                                                                               | Tak            |
| Usuwanie urządzenia                                                                                                                   | Tak            |
| Tworzenie, modyfikowanie i usuwanie kontenera woluminów                                                                                   | Nie             |
| Tworzenie, modyfikowanie i Usuwanie woluminu                                                                                             | Nie             |
| Tworzenie, modyfikowanie i usuwanie zasad tworzenia kopii zapasowych                                                                                      | Nie             |
| Ręczne tworzenie kopii zapasowej                                                                                                            | Nie             |
| Utwórz zaplanowaną kopię zapasową                                                                                                         | Nie dotyczy |
| Przywróć z zestawu kopii zapasowych                                                                                                        | Nie             |
| Klonowanie do urządzenia z aktualizacją 3,0 lub nowszą <br> Na urządzeniu źródłowym jest uruchomiona wersja wcześniejsza niż aktualizacja 3,0.                                | Tak            |
| Klonowanie do urządzenia z wersjami przed aktualizacją 3,0                                                                          | Nie             |
| Tryb failover jako urządzenie źródłowe <br> (z urządzenia z uruchomioną wersją wcześniejszą niż aktualizacja 3,0 do urządzenia z aktualizacją Update 3,0 lub nowszym)                                                               | Tak            |
| Tryb failover jako urządzenie docelowe <br> (na urządzeniu z uruchomioną wersją oprogramowania przed aktualizacją 3,0)                                                                                   | Nie             |
| Czyszczenie alertu                                                                                                                  | Tak            |
| Wyświetlanie zasad tworzenia kopii zapasowych, wykazu kopii zapasowych, woluminów, kontenerów woluminów, wykresów monitorowania, zadań i alertów utworzonych w portalu klasycznym | Tak            |
| Włączanie i wyłączanie kontrolerów urządzeń                                                                                              | Tak            |


## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [procesie wdrażania StorSimple](storsimple-8000-deployment-walkthrough-u2.md).
* Dowiedz się więcej o [zarządzaniu kontem magazynu StorSimple](storsimple-8000-manage-storage-accounts.md).
* Dowiedz się więcej na temat [używania usługi StorSimple Menedżer urządzeń do administrowania urządzeniem StorSimple](storsimple-8000-manager-service-administration.md).
