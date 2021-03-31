---
title: 'Samouczek: kopiowanie danych do Azure Data Box Heavy za pośrednictwem usługi kopiowania danych'
description: W ramach tego samouczka nauczysz się kopiować dane do urządzenia Azure Data Box Heavy za pośrednictwem usługi kopiowania danych
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 601e5cf15c47b16c53ff9ca81a56cb613bcfc3f5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96006563"
---
# <a name="tutorial-use-the-data-copy-service-to-copy-data-into-azure-data-box-heavy-preview"></a>Samouczek: używanie usługi kopiowania danych do kopiowania danych do Azure Data Box Heavy (wersja zapoznawcza)

W tym samouczku opisano sposób pozyskiwania danych przy użyciu usługi kopiowania danych bez hosta pośredniego. Usługa kopiowania danych działa lokalnie na Azure Data Box Heavy, nawiązuje połączenie z urządzeniem magazynu podłączonym do sieci (NAS) za pośrednictwem protokołu SMB i kopiuje dane do Data Box Heavy.

Z usługi kopiowania danych można korzystać w następujących scenariuszach:

- W środowiskach NAS, gdzie hosty pośrednie mogą być niedostępne.
- W przypadku korzystania z małych plików, dla których pozyskanie i przekazanie danych trwałoby tygodniami. Usługa kopiowania danych znacznie skraca czas pozyskiwania i przekazywania danych w przypadku małych plików.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Kopiowanie danych na urządzenie Data Box Heavy

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ten samouczek został ukończony: [konfiguracja Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Urządzenie Data Box Heavy zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Masz poświadczenia źródłowego urządzenia NAS, z którym połączysz się na potrzeby kopiowania danych.
4. Dysponujesz szybkim połączeniem sieciowym. Aby uzyskać największe szybkości kopiowania, można użyć dwóch równoległych połączeń 40-GbE (po jednym na węzeł). Jeśli nie masz dostępnego połączenia 40-GbE, zalecamy skorzystanie z co najmniej dwóch połączeń 10-GbE (po jednym na węzeł). 

## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych na urządzenie Data Box Heavy

Po nawiązaniu połączenia z urządzeniem NAS następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z limitami rozmiaru opisanymi w artykule [Magazyn platformy Azure i limity Data Box Heavy](data-box-heavy-limits.md).
- Jeśli dane przekazywane przez Data Box Heavy są przesyłane współbieżnie przez inne aplikacje poza Data Box Heavy, mogą wystąpić błędy przekazywania-zadania i uszkodzenie danych.
- Jeśli dane są modyfikowane podczas ich odczytywania przez usługę kopiowania danych, mogą wystąpić niepowodzenia lub uszkodzenie danych.

Aby skopiować dane za pomocą usługi kopiowania danych, należy utworzyć zadanie:

1. W lokalnym interfejsie użytkownika sieci Web urządzenia Data Box Heavy przejdź do pozycji **Zarządzaj**  >  **danymi Kopiuj dane**.
2. Na stronie **Kopiowanie danych** wybierz pozycję **Utwórz**.

    ![Wybierz pozycję Utwórz na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/click-create.png)

3. W oknie dialogowym **Skonfiguruj zadanie i uruchom** wypełnij następujące pola:
    
    |Pole                          |Wartość    |
    |-------------------------------|---------|
    |**Nazwa zadania**                       |Unikatową nazwę zadania składającą się z mniej niż 230 znaków. Te znaki nie są dozwolone w nazwie zadania:,,,,,, \<, \> \| \? \* \\ \: \/ , i \\\.         |
    |**Lokalizacja źródła**                |Podaj ścieżkę SMB do źródła danych w formacie: `\\<ServerIPAddress>\<ShareName>` lub `\\<ServerName>\<ShareName>`.        |
    |**Nazwa użytkownika**                       |Nazwa użytkownika w formacie `\\<DomainName><UserName>` używana do uzyskiwania dostępu do źródła danych. Jeśli administrator lokalny nawiązuje połączenie, będą musieli mieć jawne uprawnienia zabezpieczeń. Kliknij prawym przyciskiem myszy folder, wybierz pozycję **Właściwości** , a następnie wybierz pozycję **zabezpieczenia**. Powinno to spowodować dodanie administratora lokalnego na karcie **zabezpieczenia** .       |
    |**Password** (Hasło)                       |Hasło używane do uzyskiwania dostępu do źródła danych.           |
    |**Docelowe konto magazynu**    |Z listy wybierz docelowe konto magazynu, do którego będą przekazywane dane.         |
    |**Typ docelowy**       |Wybierz docelowy typ magazynu z listy: **blokowy obiekt BLOB**, **stronicowy obiekt BLOB** lub **Azure Files**.        |
    |**Docelowy kontener/udział**    |Wprowadź nazwę kontenera lub udziału, do którego chcesz przekazać dane w ramach docelowego konta magazynu. Może to być nazwa udziału lub kontenera. Użyj na przykład nazwy `myshare` lub `mycontainer`. Nazwę możesz również wprowadzić w formacie `sharename\directory_name` lub `containername\virtual_directory_name`.        |
    |**Kopiowanie plików pasujących do wzorca**    | Nazwę pliku pasującą do wzorca możesz wprowadzić na następujące dwa sposoby:<ul><li>**Używaj wyrażeń wieloznacznych:** Tylko `*` i `?` są obsługiwane w wyrażeniach wieloznacznych. Na przykład do wyrażenia `*.vhd` będą pasowały wszystkie pliki z rozszerzeniem `.vhd`. Podobnie do wyrażenia `*.dl?` będą pasowały wszystkie pliki z rozszerzeniem `.dl` i zaczynające się od `.dl`, na przykład `.dll`. Wyrażenie `*foo` będzie pasowało do wszystkich plików, których nazwy kończą się na `foo`.<br>Wyrażenie wieloznaczne można wprowadzić bezpośrednio w polu. Domyślnie wartość wprowadzona w polu jest traktowana jako wyrażenie wieloznaczne.</li><li>**Używaj wyrażeń regularnych:** Obsługiwane są wyrażenia regularne oparte na standardzie POSIX. Na przykład wyrażenie regularne `.*\.vhd` będzie zgodne ze wszystkimi plikami z rozszerzeniem `.vhd`. Dla wyrażeń regularnych podaj `<pattern>` bezpośrednio jako `regex(<pattern>)`. Aby uzyskać więcej informacji na temat wyrażeń regularnych, przejdź do tematu [Język wyrażeń regularnych — krótki przewodnik](/dotnet/standard/base-types/regular-expression-language-quick-reference).</li><ul>|
    |**Optymalizacja pliku**              |Kiedy ta funkcja jest włączona, pliki mniejsze niż 1 MB są pakowane podczas pozyskiwania. Przyspiesza to kopiowanie danych w przypadku małych plików. Pozwala to także zaoszczędzić czas, gdy liczba plików znacznie przekracza liczbę katalogów.        |
 
4. Wybierz pozycję **Uruchom**. Dane wejściowe są weryfikowane i jeśli ten proces zakończy się pomyślnie, zadanie jest uruchamiane. Uruchomienie zadania może potrwać kilka minut.

    ![Uruchamianie zadania z poziomu okna dialogowego „Skonfiguruj zadanie i uruchom”](media/data-box-deploy-copy-data-via-copy-service/configure-and-start.png)

5. Zadanie zostanie utworzone przy użyciu wybranych ustawień. Zadanie można wstrzymać, wznowić, anulować i uruchomić ponownie. Zaznacz pole wyboru obok nazwy zadania, a następnie wybierz odpowiedni przycisk.

    ![Zarządzanie zadaniem na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/select-job.png)
    
    - Można wstrzymać zadanie, jeśli ma ono wpływ na zasoby urządzenia NAS w godzinach szczytu:

        ![Wstrzymywanie zadania na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/pause-job.png)

        Zadanie można wznowić później, kiedy miną godziny szczytu:

        ![Wznawianie zadania na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/resume-job.png)

    - Zadanie można anulować w dowolnym momencie:

        ![Anulowanie zadania na stronie „Kopiowanie danych”](media/data-box-deploy-copy-data-via-copy-service/cancel-job.png)
        
        Anulowanie zadania musi zostać potwierdzone:

        ![Potwierdzanie anulowania zadania](media/data-box-deploy-copy-data-via-copy-service/confirm-cancel-job.png)

        Jeśli zdecydujesz się na anulowanie zadania, skopiowane już dane nie zostaną usunięte. Aby usunąć dane skopiowane na urządzenie Data Box, należy zresetować urządzenie.

        ![Resetowanie urządzenia](media/data-box-deploy-copy-data-via-copy-service/reset-device.png)

        >[!NOTE]
        > Jeśli anulujesz lub wstrzymasz zadanie, duże pliki mogą być tylko częściowo skopiowane. Te częściowo skopiowane pliki są przekazywane w takim stanie na platformę Azure. Kiedy anulujesz lub wstrzymujesz zadanie, upewnij się, że Twoje pliki zostały skopiowane poprawnie. Aby zweryfikować pliki, sprawdź udziały SMB lub pobierz plik BOM.

    - Zadanie można uruchomić ponownie, jeśli wystąpiło niepowodzenie wywołane przejściowym błędem, na przykład zakłóceniem w sieci. Zadania nie można jednak uruchomić ponownie, jeśli osiągnęło ono stan końcowy, taki jak **Powodzenie** lub **Ukończone z błędami**. Niepowodzenia zadań mogą być spowodowane problemami z nazwami lub rozmiarami plików. Te błędy są rejestrowane, ale zadania nie można uruchomić ponownie, jeśli zostało zakończone.

        ![Ponowne uruchamianie zadania zakończonego niepowodzeniem](media/data-box-deploy-copy-data-via-copy-service/restart-failed-job.png)

        Jeśli wystąpi niepowodzenie i zadania nie można uruchomić ponownie, pobierz dzienniki błędów i poszukaj niepowodzenia w plikach dziennika. Po rozwiązaniu problemu utwórz nowe zadanie kopiowania plików. Możesz również [skopiować pliki przy użyciu protokołu SMB](data-box-deploy-copy-data.md).
    
    - W tej wersji nie można usunąć zadania.
    
    - Można utworzyć nieograniczoną liczbę zadań, ale równolegle można uruchomić maksymalnie 10 zadań jednocześnie.
    - Jeśli włączono opcję **Optymalizacja plików**, małe pliki są pakowane podczas pozyskiwania w celu zwiększenia wydajności kopiowania. W takiej sytuacji zobaczysz spakowany plik (jego nazwą będzie identyfikator GUID). Nie usuwaj tego pliku. Zostanie on rozpakowany podczas przekazywania.

6. Podczas przetwarzania zadania na stronie **Kopiowanie danych** można zauważyć następujące elementy:

    - W kolumnie **Stan** można wyświetlić stan zadania kopiowania. Możliwe stany to:
        - **Uruchomienie**
        - **Niepowodzenie**
        - **Powodzenie**
        - **Wstrzymywanie**
        - **Wstrzymano**
        - **Anulowanie**
        - **Anulowane**
        - **Zakończone z błędami**
    - W kolumnie **Pliki** można zobaczyć liczbę kopiowanych plików i ich całkowity rozmiar.
    - W kolumnie **Przetworzone** można zobaczyć liczbę i całkowity rozmiar przetworzonych plików.
    - W kolumnie **Szczegóły zadania** wybierz pozycję **Wyświetl**, aby wyświetlić szczegóły zadania.
    - Jeśli podczas kopiowania wystąpią jakieś błędy (widoczne w kolumnie **# Błędy**), przejdź do kolumny **Dziennik błędów** i pobierz dzienniki błędów w celu rozwiązania problemów.

Poczekaj na zakończenie zadania kopiowania. Ponieważ niektóre błędy są rejestrowane tylko na stronie **Połącz i skopiuj**, przed przejściem do następnego kroku upewnij się, że zadanie kopiowanie zostało zakończone bez błędów.

![Brak błędów na stronie „Połącz i skopiuj”](media/data-box-deploy-copy-data-via-copy-service/verify-no-errors-on-connect-and-copy.png)

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania wybierz pozycję **Wyświetl pulpit nawigacyjny**, aby zweryfikować użyte miejsce i wolne miejsce na urządzeniu.
    
![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data-via-copy-service/verify-used-space-dashboard.png)

> [!IMPORTANT]
> Powtórz te same instrukcje, aby skopiować dane do drugiego węzła na Data Box Heavy.

Po zakończeniu zadania kopiowania możesz wybrać pozycję **Przygotuj do wysłania**.

>[!NOTE]
> Nie można uruchomić **przygotowanie do wysłania** , gdy zadania kopiowania są w toku.

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego samouczka, aby dowiedzieć się, jak dostarczyć urządzenie Data Box Heavy z powrotem do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wyślij urządzenie Azure Data Box Heavy do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)