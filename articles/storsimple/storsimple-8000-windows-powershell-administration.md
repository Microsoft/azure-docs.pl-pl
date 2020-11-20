---
title: PowerShell dla zarządzania urządzeniami StorSimple
description: Dowiedz się, jak za pomocą program Windows PowerShell dla usługi StorSimple zarządzać urządzeniem StorSimple.
author: alkohli
ms.service: storsimple
ms.topic: how-to
ms.date: 01/09/2018
ms.author: alkohli
ms.openlocfilehash: 65e9657c3948d8ce5883cd33ca8720f501352105
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94950673"
---
# <a name="use-windows-powershell-for-storsimple-to-administer-your-device"></a>Używanie programu Windows PowerShell dla usługi StorSimple do administrowania urządzeniem

## <a name="overview"></a>Omówienie

Program Windows PowerShell dla usługi StorSimple udostępnia interfejs wiersza polecenia, którego można użyć do zarządzania urządzeniem Microsoft Azure StorSimple. Jak sugeruje nazwa, jest to oparty na interfejsie wiersza polecenia programu Windows PowerShell, który jest zbudowany w ograniczonym obszarze działania. Z punktu widzenia użytkownika w wierszu polecenia ograniczone obszaru działania są wyświetlane jako ograniczona wersja środowiska Windows PowerShell. Przy zachowaniu niektórych podstawowych możliwości środowiska Windows PowerShell ten interfejs ma dodatkowe dedykowane polecenia cmdlet, które są dostosowane do zarządzania urządzeniem Microsoft Azure StorSimple.

W tym artykule opisano funkcje program Windows PowerShell dla usługi StorSimple, w tym sposób nawiązywania połączenia z tym interfejsem oraz linki do procedur krok po kroku lub przepływów pracy, które można wykonywać za pomocą tego interfejsu. Przepływy pracy obejmują sposób rejestrowania urządzenia, konfigurowania interfejsu sieciowego na urządzeniu, instalowania aktualizacji, które wymagają, aby urządzenie było w trybie konserwacji, zmieniać stan urządzenia i rozwiązywać wszelkie problemy, które mogą wystąpić.

Po przeczytaniu tego artykułu będzie można:

* Połącz się z urządzeniem StorSimple przy użyciu program Windows PowerShell dla usługi StorSimple.
* Administruj urządzeniem StorSimple przy użyciu program Windows PowerShell dla usługi StorSimple.
* Uzyskaj pomoc w program Windows PowerShell dla usługi StorSimple.

> [!NOTE]
> * Polecenia cmdlet program Windows PowerShell dla usługi StorSimple umożliwiają zarządzanie urządzeniem StorSimple z poziomu konsoli szeregowej lub zdalnie za pośrednictwem komunikacji zdalnej programu Windows PowerShell. Aby uzyskać więcej informacji na temat poszczególnych poleceń cmdlet, które mogą być używane w tym interfejsie, przejdź do [dokumentacji poleceń cmdlet dla program Windows PowerShell dla usługi StorSimple](/powershell/module/hcs/?viewFallbackFrom=winserverr2-ps).
> * Polecenia cmdlet programu Azure PowerShell StorSimple są różnymi kolekcjami poleceń cmdlet, które umożliwiają automatyzację StorSimple zadań dotyczących poziomu usług i migracji z wiersza polecenia. Aby uzyskać więcej informacji na temat Azure PowerShell poleceń cmdlet dla StorSimple, przejdź do [dokumentacji poleceń cmdlet platformy Azure StorSimple](/powershell/module/servicemanagement/azure.service/?view=azuresmps-4.0.0&viewFallbackFrom=azuresmps-3.7.0#azure).


Dostęp do program Windows PowerShell dla usługi StorSimple można uzyskać przy użyciu jednej z następujących metod:

* [Łączenie z konsolą szeregową urządzenia StorSimple](#connect-to-windows-powershell-for-storsimple-via-the-device-serial-console)
* [Zdalne nawiązywanie połączenia z usługą StorSimple przy użyciu programu Windows PowerShell](#connect-remotely-to-storsimple-using-windows-powershell-for-storsimple)

## <a name="connect-to-windows-powershell-for-storsimple-via-the-device-serial-console"></a>Nawiązywanie połączenia z program Windows PowerShell dla usługi StorSimple za pośrednictwem konsoli szeregowej urządzenia

Aby nawiązać połączenie z usługą program Windows PowerShell dla usługi StorSimple, można [pobrać](https://www.putty.org/) lub podobnego oprogramowania do emulacji terminalu. Należy skonfigurować szczegółowe uprawnienia dostępu do urządzenia Microsoft Azure StorSimple. Poniższe tematy zawierają szczegółowe instrukcje dotyczące konfigurowania pozostałej konfiguracji i łączenia się z urządzeniem. Wyjaśniono również różne opcje menu w konsoli szeregowej.

### <a name="putty-settings"></a>Ustawienia programu PuTTY

Aby nawiązać połączenie z interfejsem programu Windows PowerShell z poziomu konsoli szeregowej, należy wykonać następujące czynności.

#### <a name="to-configure-putty"></a>Aby skonfigurować

1. W oknie dialogowym wyznaczanie ponownej **konfiguracji** w okienku **Kategoria** wybierz pozycję **Klawiatura**.
2. Upewnij się, że wybrano następujące opcje (są to ustawienia domyślne podczas uruchamiania nowej sesji).
   
   | Element klawiatury | Wybierz pozycję |
   | --- | --- |
   | Klawisz Backspace |Kontrolka-? (127) |
   | Główne i końcowe klawisze |Standardowa |
   | Klawisze funkcyjne i klawiatura |ESC [n ~ |
   | Początkowy stan kluczy kursora |Normalne |
   | Początkowy stan klawiatury numerycznej |Normalne |
   | Włącz dodatkowe funkcje klawiatury |Control-Alt różni się od AltGr |
   
    ![Obsługiwane ustawienia.](./media/storsimple-windows-powershell-administration/IC740877.png)
3. Kliknij pozycję **Zastosuj**.
4. W okienku **Kategoria** wybierz opcję **tłumaczenie**.
5. W polu listy **zdalny zestaw znaków** wybierz pozycję **UTF-8**.
6. W obszarze **Obsługa znaków rysowania linii** wybierz opcję **Użyj punktów kodowych rysowania linii Unicode**. Poniższy zrzut ekranu przedstawia prawidłowe wybory.
   
    ![Ustawienia kodowania UTF](./media/storsimple-windows-powershell-administration/IC740878.png)
7. Kliknij pozycję **Zastosuj**.

Można teraz użyć instrukcji "Wykorzystaj", aby nawiązać połączenie z konsolą szeregową urządzenia, wykonując następujące czynności.

[!INCLUDE [storsimple-use-putty](../../includes/storsimple-use-putty.md)]

### <a name="about-the-serial-console"></a>Informacje o konsoli szeregowej

Gdy uzyskujesz dostęp do interfejsu programu Windows PowerShell urządzenia StorSimple za pomocą konsoli szeregowej, zostanie wyświetlony komunikat transparent, a następnie opcje menu.

Komunikat transparent zawiera podstawowe informacje o urządzeniu StorSimple, takie jak model, nazwa, zainstalowana wersja oprogramowania i stan kontrolera, do którego uzyskujesz dostęp. Na poniższej ilustracji przedstawiono przykład wiadomości banerowej.

![Wiadomość transparentu szeregowego](./media/storsimple-windows-powershell-administration/IC741098.png)

> [!IMPORTANT]
> Możesz użyć wiadomości transparentu, aby określić, czy kontroler, z którym nawiązano połączenie, jest _aktywny_ , czy _pasywny_.

Na poniższej ilustracji przedstawiono różne opcje obszaru działania, które są dostępne w menu konsoli szeregowej.

![Rejestrowanie urządzenia 2](./media/storsimple-windows-powershell-administration/IC740906.png)

Można wybrać jedną z następujących ustawień:

1. **Zaloguj się z pełnymi prawami dostępu** Ta opcja umożliwia nawiązanie połączenia (z odpowiednimi poświadczeniami) z obszarem działania **SSAdminConsole** na kontrolerze lokalnym. (Kontroler lokalny jest kontrolerem, do którego aktualnie uzyskuje się dostęp za pomocą konsoli szeregowej urządzenia StorSimple). Ta opcja umożliwia również umożliwienie pomoc techniczna firmy Microsoft dostępu do nieograniczonego obszaru działania (sesji obsługi) w celu rozwiązywania problemów z urządzeniami. Po użyciu opcji 1, aby zalogować się, można zezwolić pomoc techniczna firmy Microsoft inżynierowi na dostęp do nieograniczonego obszaru działania przez uruchomienie określonego polecenia cmdlet. Aby uzyskać szczegółowe informacje, zobacz [Uruchamianie sesji obsługi](storsimple-8000-contact-microsoft-support.md#start-a-support-session-in-windows-powershell-for-storsimple).
   
2. **Logowanie do kontrolera równorzędnego z pełnymi prawami dostępu** Ta opcja jest taka sama jak opcja 1, z tą różnicą, że można nawiązać połączenie (z odpowiednimi poświadczeniami) do obszaru działania **SSAdminConsole** na kontrolerze równorzędnym. Ponieważ urządzenie StorSimple jest urządzeniem wysokiej dostępności z dwoma kontrolerami w konfiguracji aktywne-pasywne, element równorzędny odwołuje się do innego kontrolera na urządzeniu, do którego uzyskujesz dostęp za pomocą konsoli szeregowej.
   Podobnie jak w przypadku opcji 1, ta opcja umożliwia także umożliwienie pomoc techniczna firmy Microsoft dostępu do nieograniczonego obszaru działania w kontrolerze równorzędnym.

3. **Łączenie z ograniczonym dostępem** Ta opcja służy do uzyskiwania dostępu do interfejsu programu Windows PowerShell w trybie ograniczonym. Nie zostanie wyświetlony monit o podanie poświadczeń dostępu. Ta opcja nawiązuje połączenie z bardziej ograniczonym obszarem działania w porównaniu z opcjami 1 i 2.  Niektóre z zadań, które są dostępne za pomocą opcji 1 **nie można* wykonać w tym obszarze działania:
   
   * Resetowanie do ustawień fabrycznych
   * Zmień hasło
   * Włącz lub wyłącz dostęp do pomocy technicznej
   * Stosowanie aktualizacji
   * Zainstaluj poprawki

     > [!NOTE]
     > Jest to preferowana opcja, jeśli zapomniano hasła administratora urządzenia i nie można nawiązać połączenia za pomocą opcji 1 lub 2.

4. **Zmień język** Ta opcja umożliwia zmianę języka wyświetlania w interfejsie programu Windows PowerShell. Języki obsługiwane są w języku angielskim, japońskim, rosyjskim, francuskim, koreańskim, angielskim, hiszpańskim, włoskim, niemieckim, chińskim i portugalskim.

## <a name="connect-remotely-to-storsimple-using-windows-powershell-for-storsimple"></a>Zdalne nawiązywanie połączenia z usługą StorSimple przy użyciu program Windows PowerShell dla usługi StorSimple

Możesz użyć komunikacji zdalnej programu Windows PowerShell, aby nawiązać połączenie z urządzeniem StorSimple. Po nawiązaniu połączenia w ten sposób menu nie zostanie wyświetlone. (Menu jest widoczne tylko wtedy, gdy do nawiązania połączenia jest używana konsola szeregowa na urządzeniu). Połączenie zdalne powoduje bezpośrednie przejście do opcji "opcja 1 — pełny dostęp" w konsoli szeregowej. Komunikacja zdalna programu Windows PowerShell polega na połączeniu z określonym obszarem działania. Możesz również określić język wyświetlania.

Język wyświetlania jest niezależny od języka ustawianego za pomocą opcji **Zmień język** w menu konsoli szeregowej. Zdalne środowisko PowerShell automatycznie wybiera ustawienia regionalne urządzenia, z którego nawiązywane jest połączenie, jeśli żaden nie zostanie określony.

> [!NOTE]
> Jeśli pracujesz z Microsoft Azure hostami wirtualnymi i urządzeniami w chmurze StorSimple, możesz użyć komunikacji zdalnej programu Windows PowerShell i hosta wirtualnego, aby nawiązać połączenie z urządzeniem w chmurze. Jeśli skonfigurowano lokalizację udziału na hoście, na którym mają zostać zapisane informacje z sesji programu Windows PowerShell, należy pamiętać, że każdy podmiot zabezpieczeń _Wszyscy_ obejmuje tylko użytkowników uwierzytelnionych. W związku z tym, jeśli skonfigurowano udział w taki sposób, aby zezwalać na dostęp przez _wszystkich użytkowników_ , a połączenie nie zostanie określone, zostanie użyty nieuwierzytelniony podmiot zabezpieczeń anonimowy i zostanie wyświetlony komunikat o błędzie. Aby rozwiązać ten problem, na hoście udziału należy włączyć konto gościa, a następnie nadać kontu gościa pełny dostęp do udziału lub należy określić prawidłowe poświadczenia wraz z poleceniem cmdlet programu Windows PowerShell.


Przy użyciu protokołu HTTP lub HTTPS można nawiązać połączenie za pośrednictwem komunikacji zdalnej programu Windows PowerShell. Skorzystaj z instrukcji w następujących samouczkach:

* [Nawiązywanie połączenia zdalnie przy użyciu protokołu HTTP](storsimple-8000-remote-connect.md#connect-through-http)
* [Nawiązywanie połączenia zdalnie przy użyciu protokołu HTTPS](storsimple-8000-remote-connect.md#connect-through-https)

## <a name="connection-security-considerations"></a>Zagadnienia dotyczące zabezpieczeń połączeń

Podczas podejmowania decyzji o sposobie nawiązywania połączenia z usługą program Windows PowerShell dla usługi StorSimple należy wziąć pod uwagę następujące kwestie:

* Bezpośrednie łączenie z konsolą szeregową urządzenia jest bezpieczne, ale połączenie z konsolą szeregową przez przełączniki sieciowe nie jest obsługiwane. Należy zachować ostrożność w przypadku nawiązywania połączenia z usługą KlawiszeSzeregowe przy użyciu przełączników sieciowych.
* Połączenie za pośrednictwem sesji HTTP może oferować lepsze zabezpieczenia niż łączenie za pośrednictwem konsoli szeregowej za pośrednictwem sieci. Chociaż nie jest to najbezpieczniejsza Metoda, jest ona akceptowalna dla zaufanych sieci.
* Nawiązywanie połączenia za pośrednictwem sesji HTTPS jest najbezpieczniejszym i zalecanym rozwiązaniem.

## <a name="administer-your-storsimple-device-using-windows-powershell-for-storsimple"></a>Administruj urządzeniem StorSimple za pomocą program Windows PowerShell dla usługi StorSimple

W poniższej tabeli przedstawiono podsumowanie wszystkich typowych zadań zarządzania i złożonych przepływów pracy, które można wykonać w interfejsie programu Windows PowerShell urządzenia StorSimple. Aby uzyskać więcej informacji na temat każdego przepływu pracy, kliknij odpowiedni wpis w tabeli.

#### <a name="windows-powershell-for-storsimple-workflows"></a>Przepływy pracy program Windows PowerShell dla usługi StorSimple

| Jeśli chcesz to zrobić... | Użyj tej procedury. |
| --- | --- |
| Rejestrowanie własnego urządzenia |[Konfigurowanie i rejestrowanie urządzenia przy użyciu program Windows PowerShell dla usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple) |
| Konfigurowanie serwera proxy sieci Web</br>Wyświetl ustawienia serwera proxy sieci Web |[Konfigurowanie serwera proxy sieci Web dla urządzenia StorSimple](storsimple-8000-configure-web-proxy.md) |
| Modyfikowanie ustawień interfejsu sieciowego (DATA) 0 na urządzeniu |[Modyfikuj interfejs sieciowy danych 0 dla urządzenia StorSimple](storsimple-8000-modify-data-0.md) |
| Zatrzymaj kontroler </br> Ponowne uruchamianie lub zamykanie kontrolera </br> Zamykanie urządzenia</br>Resetowanie urządzenia do domyślnych ustawień fabrycznych |[Zarządzanie kontrolerami urządzeń](storsimple-8000-manage-device-controller.md) |
| Instalowanie aktualizacji i poprawek w trybie konserwacji |[Aktualizowanie urządzenia](storsimple-update-device.md) |
| Przejdź do trybu konserwacji </br>Wyjdź z trybu konserwacji |[Tryby urządzenia StorSimple](storsimple-8000-device-modes.md) |
| Tworzenie pakietu dla pomocy technicznej</br>Odszyfruj i Edytuj pakiet pomocy technicznej |[Tworzenie pakietu dla pomocy technicznej i zarządzanie nim](storsimple-8000-create-manage-support-package.md) |
| Rozpocznij sesję pomocy technicznej</br> |[Rozpocznij sesję pomocy technicznej w program Windows PowerShell dla usługi StorSimple](storsimple-8000-create-manage-support-package.md#create-a-support-package) |

## <a name="get-help-in-windows-powershell-for-storsimple"></a>Uzyskaj pomoc w program Windows PowerShell dla usługi StorSimple

W program Windows PowerShell dla usługi StorSimple jest dostępna pomoc poleceń cmdlet. Dostępna jest również w trybie online wersja tej pomocy, której można użyć do zaktualizowania pomocy w systemie.

Uzyskiwanie pomocy w tym interfejsie jest podobne do programu w programie Windows PowerShell i większość poleceń cmdlet związanych z pomocą będzie działała. Pomoc dotyczącą programu Windows PowerShell online można znaleźć w [witrynie Microsoft. PowerShell. Core](/powershell/module/Microsoft.PowerShell.Core/).

Poniżej znajduje się krótki opis typów pomocy dla tego interfejsu programu Windows PowerShell, w tym informacje dotyczące sposobu aktualizowania pomocy.

### <a name="to-get-help-for-a-cmdlet"></a>Aby uzyskać pomoc dotyczącą polecenia cmdlet

* Aby uzyskać pomoc dotyczącą dowolnego polecenia cmdlet lub funkcji, użyj następującego polecenia: `Get-Help <cmdlet-name>`
* Aby uzyskać pomoc online dotyczącą dowolnego polecenia cmdlet, użyj poprzedniego polecenia cmdlet z `-Online` parametrem: `Get-Help <cmdlet-name> -Online`
* Aby uzyskać pełną pomoc, można użyć `–Full` parametru, a na przykład użyć `–Examples` parametru.

### <a name="to-update-help"></a>Aby zaktualizować pomoc

Możesz łatwo zaktualizować pomoc w interfejsie programu Windows PowerShell. Wykonaj następujące kroki, aby zaktualizować pomoc w systemie.

#### <a name="to-update-cmdlet-help"></a>Aby zaktualizować pomoc polecenia cmdlet
1. Uruchom program Windows PowerShell przy użyciu opcji **Uruchom jako administrator** .
2. W wierszu polecenia wpisz:  `Update-Help`
3. Zaktualizowane pliki pomocy zostaną zainstalowane.
4. Po zainstalowaniu plików pomocy wpisz: `Get-Help Get-Command` . Zostanie wyświetlona lista poleceń cmdlet, dla których pomoc jest dostępna.

> [!NOTE]
> Aby uzyskać listę wszystkich dostępnych poleceń cmdlet w obszarze działania, zaloguj się do odpowiadającej opcji menu i uruchom `Get-Command` polecenie cmdlet.


## <a name="next-steps"></a>Następne kroki

Jeśli wystąpią problemy z urządzeniem StorSimple podczas wykonywania jednego z powyższych przepływów pracy, zapoznaj się z [narzędziami do rozwiązywania problemów z wdrożeniami StorSimple](storsimple-8000-troubleshoot-deployment.md#tools-for-troubleshooting-storsimple-deployments).