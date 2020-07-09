---
title: Instalowanie pakietów językowych na maszynach wirtualnych z systemem Windows 10 w programie Virtual Desktop systemu Windows — Azure
description: Jak zainstalować pakiety językowe dla maszyn wirtualnych z systemem Windows 10 dla wielu sesji na pulpicie wirtualnym systemu Windows.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: how-to
ms.date: 04/03/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 70592d940e3766597475f4a7b90a3902a53406d5
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85361306"
---
# <a name="install-language-packs"></a>Instalowanie pakietów językowych

W przypadku konfigurowania wdrożeń klasycznych pulpitów systemu Windows w trybie międzynarodowym warto upewnić się, że wdrożenie obsługuje wiele języków. Pakiety językowe można zainstalować na obrazie maszyny wirtualnej z wieloma sesjami systemu Windows 10 Enterprise, aby obsługiwać dowolną liczbę języków potrzebnych w organizacji. W tym artykule przedstawiono sposób instalowania pakietów językowych i przechwytywania obrazów umożliwiających użytkownikom wybieranie własnych języków wyświetlania.

Dowiedz się więcej o tym, jak wdrożyć maszynę wirtualną na platformie Azure na stronie [Tworzenie maszyny wirtualnej z systemem Windows w strefie dostępności przy użyciu Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

>[!NOTE]
>Ten artykuł ma zastosowanie do wielosesyjnych maszyn wirtualnych z systemem Windows 10 Enterprise.

## <a name="install-a-language-pack"></a>Instalowanie pakietu językowego

Aby utworzyć obraz maszyny wirtualnej z pakietami językowymi, należy najpierw zainstalować pakiety językowe na komputerze i przechwycić obraz.

Aby zainstalować pakiety językowe:

1. Zaloguj się jako administrator.
2. Upewnij się, że zainstalowano wszystkie najnowsze aktualizacje systemu Windows i sklepu Windows.
3. Przejdź do pozycji **Ustawienia**  >  **czas &**  >  **region**języka.
4. W obszarze **kraj lub region**wybierz preferowany kraj lub region z menu rozwijanego.
    W tym przykładzie wybieramy pozycję **Francja**, jak pokazano na poniższym zrzucie ekranu:

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu strony regionu. Aktualnie wybrany region to Francja.](media/region-page-france.png)

5. Po wybraniu tej opcji wybierz pozycję **Język**, a następnie wybierz pozycję **Dodaj język**. Wybierz język, który chcesz zainstalować z listy, a następnie wybierz pozycję **dalej**.
6. Gdy zostanie otwarte okno **Instalowanie funkcji języka** , zaznacz pole wyboru z etykietą **Zainstaluj pakiet językowy i Ustaw jako mój język wyświetlania systemu Windows**.
7. Wybierz pozycję **Zainstaluj**.
8. Aby dodać wiele języków jednocześnie, wybierz pozycję **Dodaj język**, a następnie powtórz ten proces, aby dodać język w krokach 5 i 6. Powtórz ten proces dla każdego języka, który chcesz zainstalować. Można jednak ustawić tylko jeden język jako język wyświetlania w danym momencie.

    Zacznijmy od szybkiego pokazu wizualnego. Na poniższych ilustracjach przedstawiono sposób instalowania pakietów języka francuskiego i holenderskiego, a następnie ustawiania języka francuskiego jako język wyświetlania.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę języka na początku procesu. Wybrany język wyświetlania systemu Windows jest w języku angielskim.](media/language-page-default.png)

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający okno wyboru języka. Użytkownik wprowadził wartość "francuski" na pasku wyszukiwania, aby znaleźć francuskie pakiety językowe.](media/select-language-french.png)

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę funkcje języka instalacji. Jako preferowany język jest wybrany francuski. Wybrane opcje to "Ustaw mój język wyświetlania", "Zainstaluj pakiet językowy", "Rozpoznawanie mowy" i "pismo ręczne".](media/install-language-features.png)

    Po zainstalowaniu pakietów językowych powinny zostać wyświetlone nazwy pakietów językowych na liście języków.

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu przedstawiający stronę języka z zainstalowanymi nowymi pakietami językowymi. Pakiety językowe francuskie i Holandia są wymienione w obszarze "preferowane języki".](media/language-page-complete.png)

9. Jeśli zostanie wyświetlone okno z prośbą o wylogowanie się z sesji użytkownika. Wyloguj się, a następnie zaloguj się ponownie. Język wyświetlania powinien teraz być wybranym językiem.

10.  Przejdź do pozycji **Panel sterowania**  >  **i**  >  **region**region.

11.  Po otwarciu okna **regionu** wybierz kartę **Administracja** , a następnie wybierz pozycję **Kopiuj ustawienia**.

12.  Zaznacz pola wyboru z etykietami **ekran powitalny i konta systemowe** oraz **nowe konta użytkowników**.

13.  Wybierz przycisk **OK**.

14.  Zostanie otwarte okno z informacją o ponownym uruchomieniu sesji. Wybierz pozycję **Uruchom ponownie teraz**.

15.  Po ponownym zalogowaniu Wróć do pozycji **Panel sterowania**  >  **i**  >  **region**regionu.

16.  Wybierz kartę **Administracja** .

17.  Wybierz pozycję **Zmień ustawienia regionalne systemu**.

18. W menu rozwijanym w obszarze **bieżące ustawienia regionalne systemu**wybierz język ustawień regionalnych, który ma być używany. Następnie wybierz **przycisk OK**.

19. Wybierz pozycję **Uruchom ponownie teraz** , aby ponownie uruchomić sesję.

Gratulacje, zainstalowano pakiety językowe.

Przed kontynuowaniem upewnij się, że w systemie zainstalowano najnowsze wersje systemu Windows i sklepu Windows.

## <a name="sysprep"></a>Sysprep

Następnie należy przygotować program Sysprep do maszyny w celu przygotowania go do procesu przechwytywania obrazu.

Aby przeprowadzić Sysprep na komputerze:

1. Uruchom program Windows PowerShell jako administrator.
2. Uruchom następujące polecenie cmdlet, aby przejść do poprawnego katalogu:

    ```powershell
    cd Windows\System32\Sysprep
    ```

3. Następnie uruchom następujące polecenie cmdlet:

    ```powershell
    .\sysprep.exe
    ```

4. Po otwarciu okna narzędzia przygotowywania systemu zaznacz pole wyboru z etykietą **generalize**, a następnie przejdź do **opcji Zamknij** i wybierz pozycję **Zamknij** z menu rozwijanego.

>[!NOTE]
>Ukończenie procesu syprep może potrwać kilka minut. Gdy maszyna wirtualna zostanie ZAMKNIĘTA, Sesja zdalna zostanie rozłączona.

### <a name="resolve-sysprep-errors"></a>Rozwiązywanie błędów narzędzia Sysprep

Jeśli podczas procesu Sysprep zobaczysz komunikat o błędzie, Oto co należy zrobić:

1. Otwórz **dysk C** i przejdź do pozycji **Windows**  >  **system32 Sysprep**  >  **Panther**, a następnie otwórz plik **Setuperr** .

   Tekst w pliku błędu informuje o konieczności odinstalowania określonego pakietu językowego, jak pokazano na poniższej ilustracji. Skopiuj nazwę pakietu językowego dla następnego kroku.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający plik Setuperr. Tekst z nazwą pakietu zostanie wyróżniony kolorem ciemnoniebieskim.](media/setuperr-package-name.png)

2. Otwórz nowe okno programu PowerShell i uruchom następujące polecenie cmdlet z nazwą pakietu skopiowaną w kroku 2, aby usunąć pakiet językowy:

   ```powershell
   Remove-AppxPackage <package name>
   ```

3. Upewnij się, że pakiet został usunięty, ponownie uruchamiając `Remove-AppxPackage` polecenie cmdlet. Jeśli pakiet został pomyślnie usunięty, powinien zostać wyświetlony komunikat informujący o tym, że pakiet, który próbujesz usunąć, nie istnieje.

4. Ponownie uruchom `sysprep.exe` polecenie cmdlet.

## <a name="capture-the-image"></a>Przechwyć obraz

Teraz, gdy system jest gotowy, możesz przechwycić obraz, aby inni użytkownicy mogli rozpocząć korzystanie z maszyn wirtualnych w oparciu o system bez konieczności powtarzania procesu konfiguracji.

Aby przechwycić obraz:

1. Przejdź do Azure Portal i wybierz nazwę komputera, który został skonfigurowany w [instalacji pakietu językowego](#install-a-language-pack) i [narzędzia Sysprep](#sysprep).

2. Wybierz pozycję **Przechwyć**.

3. Wprowadź nazwę obrazu w polu **Nazwa** i przypisz ją do grupy zasobów przy użyciu menu rozwijanego **Grupa zasobów** , jak pokazano na poniższej ilustracji.

   > [!div class="mx-imgBorder"]
   > ![Zrzut ekranu przedstawiający okno Tworzenie obrazu. Nazwa, którą użytkownik udzielił do tego obrazu testu, to "vmwvd-Image-fr" i przypisano ją do grupy zasobów "testwvdimagerg".](media/create-image.png)

4. Wybierz pozycję **Utwórz**.

5. Poczekaj kilka minut na zakończenie procesu przechwytywania. Gdy obraz jest gotowy, w centrum powiadomień powinien zostać wyświetlony komunikat informujący o przechwyceniu obrazu.

Teraz możesz wdrożyć maszynę wirtualną przy użyciu nowego obrazu. Podczas wdrażania maszyny wirtualnej upewnij się, że postępuj zgodnie z instrukcjami w temacie [Tworzenie maszyny wirtualnej z systemem Windows w strefie dostępności przy użyciu Azure Portal](../virtual-machines/windows/create-portal-availability-zone.md).

### <a name="how-to-change-display-language-for-standard-users"></a>Jak zmienić język wyświetlania dla użytkowników standardowych

Użytkownicy standardowi mogą zmieniać język wyświetlania na swoich maszynach wirtualnych.

Aby zmienić język wyświetlania:

1. Przejdź do pozycji **Ustawienia języka**. Jeśli nie wiesz, gdzie to jest, możesz wprowadzić **Język** na pasku wyszukiwania w menu Start.

2. Z menu rozwijanego Język wyświetlania systemu Windows wybierz język, który ma być używany jako język wyświetlania.

3. Wyloguj się z sesji, a następnie zaloguj się ponownie. Język wyświetlania powinien teraz być tym, który został wybrany w kroku 2.
