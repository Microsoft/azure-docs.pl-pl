---
title: Rozwiązywanie problemów z wdrożeniem serii StorSimple 8000 | Microsoft Docs
description: Opisuje, jak diagnozować i naprawiać błędy występujące podczas pierwszego wdrożenia StorSimple.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: troubleshooting
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/03/2017
ms.author: alkohli
ms.openlocfilehash: 5806266955eafab8c3c8c99695ff82736de92e9b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86187068"
---
# <a name="troubleshoot-storsimple-device-deployment-issues"></a>Rozwiązywanie problemów z wdrażaniem urządzeń StorSimple
## <a name="overview"></a>Omówienie
Ten artykuł zawiera pomocne wskazówki dotyczące rozwiązywania problemów z wdrożeniem Microsoft Azure StorSimple. Opisano w nim typowe problemy, możliwe przyczyny i zalecane kroki ułatwiające rozwiązywanie problemów, które mogą wystąpić podczas konfigurowania StorSimple. 

Te informacje dotyczą zarówno urządzenia fizycznego z serii StorSimple 8000, jak i urządzenia w chmurze StorSimple.

> [!NOTE]
> Problemy związane z konfiguracją urządzeń mogą wystąpić podczas wdrażania urządzenia po raz pierwszy lub mogą wystąpić później, gdy urządzenie działa. Ten artykuł koncentruje się na rozwiązywaniu problemów z wdrażaniem pierwszego czasu. Aby rozwiązać problem z urządzeniem operacyjnym, przejdź do [narzędzia Diagnostyka, aby rozwiązać problemy z urządzeniem operacyjnym](storsimple-8000-diagnostics.md).

W tym artykule opisano także narzędzia do rozwiązywania problemów z wdrożeniami StorSimple i przedstawiono przykład rozwiązywania problemów krok po kroku.

## <a name="first-time-deployment-issues"></a>Problemy z wdrażaniem pierwszego czasu
Jeśli wystąpi problem podczas wdrażania urządzenia po raz pierwszy, należy wziąć pod uwagę następujące kwestie:

* W przypadku rozwiązywania problemów z urządzeniem fizycznym upewnij się, że sprzęt został zainstalowany i skonfigurowany zgodnie z opisem w artykule [Instalowanie urządzenia z systemem StorSimple 8100](storsimple-8100-hardware-installation.md) lub [Instalowanie urządzenia z systemem StorSimple 8600](storsimple-8600-hardware-installation.md).
* Sprawdź wymagania wstępne dotyczące wdrażania. Upewnij się, że masz wszystkie informacje opisane na liście [kontrolnej konfiguracji wdrożenia](storsimple-8000-deployment-walkthrough-u2.md#deployment-configuration-checklist).
* Zapoznaj się z informacjami o wersji StorSimple, aby sprawdzić, czy problem został opisany. Informacje o wersji obejmują obejścia znanych problemów z instalacją. 

Podczas wdrażania urządzenia najczęstsze problemy występujące podczas uruchamiania Kreatora instalacji i rejestrowania urządzenia za pośrednictwem program Windows PowerShell dla usługi StorSimple. (W celu zarejestrowania i skonfigurowania urządzenia StorSimple służy program Windows PowerShell dla usługi StorSimple. Aby uzyskać więcej informacji na temat rejestracji urządzeń, zobacz [krok 3. Konfigurowanie i rejestrowanie urządzenia w program Windows PowerShell dla usługi StorSimple](storsimple-8000-deployment-walkthrough-u2.md#step-3-configure-and-register-the-device-through-windows-powershell-for-storsimple)).

Poniższe sekcje mogą pomóc w rozwiązywaniu problemów występujących podczas konfigurowania urządzenia StorSimple po raz pierwszy.

## <a name="first-time-setup-wizard-process"></a>Proces Kreatora instalacji pierwszego czasu
Poniższe kroki podsumowują proces Kreatora instalacji. Aby uzyskać szczegółowe informacje dotyczące instalacji, zobacz [wdrażanie lokalnego urządzenia StorSimple](storsimple-8000-deployment-walkthrough-u2.md).

1. Uruchom polecenie cmdlet [Invoke-HcsSetupWizard](https://technet.microsoft.com/library/dn688135.aspx) , aby uruchomić Kreatora instalacji, który przeprowadzi Cię przez pozostałe kroki. 
2. Konfigurowanie sieci: Kreator instalacji umożliwia skonfigurowanie ustawień sieciowych dla interfejsu sieciowego 0 na urządzeniu StorSimple. Są to m.in. następujące ustawienia:
   * Wirtualny adres IP (VIP), maska podsieci i Brama — polecenie cmdlet [Set-HcsNetInterface](https://technet.microsoft.com/library/dn688161.aspx) jest wykonywane w tle. Konfiguruje adres IP, maskę podsieci i bramę dla interfejsu sieciowego DATA 0 na urządzeniu StorSimple.
   * Podstawowy serwer DNS — polecenie cmdlet [Set-HcsDnsClientServerAddress](https://technet.microsoft.com/library/dn688172.aspx) jest wykonywane w tle. Konfiguruje ustawienia DNS dla rozwiązania StorSimple.
   * Serwer NTP — polecenie cmdlet [Set-HcsNtpClientServerAddress](https://technet.microsoft.com/library/dn688138.aspx) jest wykonywane w tle. Konfiguruje ustawienia serwera NTP dla rozwiązania StorSimple.
   * Opcjonalny serwer proxy sieci Web — polecenie cmdlet [Set-HcsWebProxy](https://technet.microsoft.com/library/dn688154.aspx) jest wykonywane w tle. Ustawia i włącza konfigurację serwera proxy sieci Web dla rozwiązania StorSimple.
3. Skonfiguruj hasło: następnym krokiem jest skonfigurowanie hasła administratora urządzenia.
   Hasło administratora urządzenia służy do logowania się do urządzenia. Domyślne hasło urządzenia to **Password1**.
        
     > [!IMPORTANT]
     > Hasła są zbierane przed rejestracją, ale są stosowane dopiero po pomyślnym zarejestrowaniu urządzenia. Jeśli wystąpi błąd podczas stosowania hasła, zostanie wyświetlony monit o podanie hasła ponownie do momentu zebrania wymaganych haseł (spełniających wymagania dotyczące złożoności).
     
4. Zarejestruj urządzenie: ostatnim krokiem jest zarejestrowanie urządzenia w usłudze StorSimple Menedżer urządzeń działającej w Microsoft Azure. Rejestracja wymaga [pobrania klucza rejestracji usługi](storsimple-8000-manage-service.md#get-the-service-registration-key) z Azure Portal i udostępnienia go w Kreatorze instalacji. **Po pomyślnym zarejestrowaniu urządzenia jest dostarczany klucz szyfrowania danych usługi. Pamiętaj, aby zachować ten klucz szyfrowania w bezpiecznej lokalizacji, ponieważ będzie on wymagany do zarejestrowania wszystkich kolejnych urządzeń w usłudze.**

## <a name="common-errors-during-device-deployment"></a>Typowe błędy podczas wdrażania urządzenia
W poniższej tabeli wymieniono typowe błędy, które mogą wystąpić podczas:

* Skonfiguruj wymagane ustawienia sieci.
* Skonfiguruj opcjonalne ustawienia serwera proxy sieci Web.
* Skonfiguruj hasło administratora urządzenia.
* Zarejestruj urządzenie.

## <a name="errors-during-the-required-network-settings"></a>Błędy w wymaganych ustawieniach sieci
| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: to polecenie można uruchomić tylko na aktywnym kontrolerze. |Konfiguracja została wykonana na kontrolerze pasywnym. |Uruchom to polecenie z poziomu aktywnego kontrolera. Aby uzyskać więcej informacji, zobacz [Identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device). |
| 2 |Invoke-HcsSetupWizard: urządzenie nie jest gotowe. |Występują problemy z łącznością sieciową na danych 0. |Sprawdź łączność sieci fizycznej z danymi 0. |
| 3 |Invoke-HcsSetupWizard: występuje konflikt adresu IP z innym systemem w sieci (wyjątek z HRESULT: 0x80070263). |Adres IP podany dla danych 0 był już używany przez inny system. |Podaj nowy adres IP, który nie jest używany. |
| 4 |Invoke-HcsSetupWizard: zasób klastra nie powiódł się. (Wyjątek od HRESULT: 0x800713AE). |Zduplikowany adres VIP. Podany adres IP jest już używany. |Podaj nowy adres IP, który nie jest używany. |
| 5 |Invoke-HcsSetupWizard: nieprawidłowy adres IPv4. |Adres IP jest podany w niepoprawnym formacie. |Sprawdź format i podaj ponownie adres IP. Aby uzyskać więcej informacji, zobacz [adresowanie IPv4][1]. |
| 6 |Invoke-HcsSetupWizard: nieprawidłowy adres IPv6. |Adres IP jest podany w niepoprawnym formacie. |Sprawdź format i podaj ponownie adres IP. Aby uzyskać więcej informacji, zobacz [adresowanie IPv6][2]. |
| 7 |Invoke-HcsSetupWizard: nie ma więcej punktów końcowych dostępnych w programie mapowania punktów końcowych. (Wyjątek od HRESULT: 0x800706D9) |Funkcjonalność klastra nie działa. |[Skontaktuj się pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu wykonania następnych kroków. |

## <a name="errors-during-the-optional-web-proxy-settings"></a>Błędy podczas opcjonalnych ustawień serwera proxy sieci Web
| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Invoke-HcsSetupWizard: nieprawidłowy parametr (wyjątek z HRESULT: 0x80070057) |Jeden z parametrów podanych dla ustawień serwera proxy jest nieprawidłowy. |Identyfikator URI nie ma poprawnego formatu. Użyj następującego formatu: http:// *\<IP address or FQDN of the web proxy server>* :*\<TCP port number>* |
| 2 |Invoke-HcsSetupWizard: serwer RPC niedostępny (wyjątek z HRESULT: 0x800706ba) |Główna przyczyna jest jedną z następujących czynności:<ol><li>Klaster nie działa.</li><li>Kontroler pasywny nie może komunikować się z aktywnym kontrolerem, a polecenie jest uruchamiane z kontrolera pasywnego.</li></ol> |W zależności od przyczyny głównej:<ol><li>[Skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby upewnić się, że klaster działa.</li><li>Uruchom polecenie z poziomu aktywnego kontrolera. Jeśli chcesz uruchomić polecenie z kontrolera pasywnego, musisz się upewnić, że kontroler pasywny może komunikować się z aktywnym kontrolerem. Jeśli ta łączność zostanie przerwana, należy [skontaktować się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) .</li></ol> |
| 3 |Invoke-HcsSetupWizard: wywołanie RPC nie powiodło się (wyjątek z HRESULT: 0X800706be) |Klaster nie działa. |[Skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby upewnić się, że klaster działa. |
| 4 |Invoke-HcsSetupWizard: nie znaleziono zasobu klastra (wyjątek z HRESULT: 0x8007138f) |Nie znaleziono zasobu klastra. Taka sytuacja może wystąpić, jeśli instalacja nie powiodła się. |Może być konieczne zresetowanie urządzenia do domyślnych ustawień fabrycznych. [Skontaktuj się pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby utworzyć zasób klastra. |
| 5 |Invoke-HcsSetupWizard: zasób klastra nie jest w trybie online (wyjątek z HRESULT: 0x8007138c) |Zasoby klastra nie są w trybie online. |[Skontaktuj się pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu wykonania następnych kroków. |

## <a name="errors-related-to-device-administrator-password"></a>Błędy związane z hasłem administratora urządzenia
Domyślne hasło administratora urządzenia to **Password1**. To hasło wygasa po pierwszym zalogowaniu; w związku z tym należy użyć Kreatora instalacji, aby go zmienić. Podczas pierwszego rejestrowania urządzenia należy podać nowe hasło administratora urządzenia. 

Upewnij się, że hasła spełniają następujące wymagania:

* Hasło administratora urządzenia powinno mieć długość od 8 do 15 znaków.
* Hasła powinny zawierać 3 z następujących 4 typów znaków: małe litery, wielkie litery, cyfry i znaki specjalne. 
* Hasło nie może być takie samo jak w przypadku ostatnich 24 haseł.

Ponadto należy pamiętać, że hasła wygasają co rok i mogą być zmieniane dopiero po pomyślnym zarejestrowaniu urządzenia. Jeśli rejestracja nie powiedzie się z jakiegokolwiek powodu, hasła nie zostaną zmienione.

Aby uzyskać więcej informacji na temat hasła administratora urządzenia, przejdź do [usługi StorSimple Menedżer urządzeń, aby zmienić hasło StorSimple](storsimple-8000-change-passwords.md).

Podczas konfigurowania administratora urządzenia i StorSimple Snapshot Manager hasła może wystąpić co najmniej jeden z następujących błędów.

| Nie. | Komunikat o błędzie | Zalecana akcja |
| --- | --- | --- |
| 1 |Hasło przekracza maksymalną długość. |Hasło administratora urządzenia musi mieć długość od 8 do 15 znaków. |
| 2 |Hasło nie spełnia wymagań dotyczących długości. |Hasło administratora urządzenia musi mieć długość od 8 do 15 znaków.|
| 3 |Hasło musi zawierać małe litery. |Hasła muszą zawierać 3 z następujących 4 typów znaków: małe litery, wielkie litery, cyfry i znaki specjalne. Upewnij się, że hasło spełnia te wymagania. |
| 4 |Hasło musi zawierać znaki numeryczne. |Hasła muszą zawierać 3 z następujących 4 typów znaków: małe litery, wielkie litery, cyfry i znaki specjalne. Upewnij się, że hasło spełnia te wymagania. |
| 5 |Hasło musi zawierać znaki specjalne. |Hasła muszą zawierać 3 z następujących 4 typów znaków: małe litery, wielkie litery, cyfry i znaki specjalne. Upewnij się, że hasło spełnia te wymagania. |
| 6 |Hasło musi zawierać 3 z następujących 4 typów znaków: wielkie litery, małe litery, cyfry i znaki specjalne. |Hasło nie zawiera wymaganych typów znaków. Upewnij się, że hasło spełnia te wymagania. |
| 7 |Parametr nie jest zgodny z potwierdzeniem. |Upewnij się, że hasło spełnia wszystkie wymagania i wprowadzono je poprawnie. |
| 8 |Hasło nie może być zgodne z wartością domyślną. |Domyślne hasło to *Password1*. Po pierwszym zalogowaniu należy zmienić to hasło. |
| 9 |Wprowadzone hasło jest niezgodne z hasłem urządzenia. Wpisz ponownie hasło. |Sprawdź hasło i wpisz je ponownie. |

Hasła są zbierane przed zarejestrowaniem urządzenia, ale są stosowane dopiero po pomyślnej rejestracji. Przepływ pracy odzyskiwania hasła wymaga zarejestrowania urządzenia.

> [!IMPORTANT]
> Ogólnie rzecz biorąc, jeśli próba zastosowania hasła nie powiedzie się, oprogramowanie wielokrotnie próbuje zebrać hasło do momentu jego pomyślnego przeprowadzenia. W rzadkich przypadkach nie można zastosować hasła. W takiej sytuacji możesz zarejestrować urządzenie i wykonać operację, ale hasła nie zostaną zmienione. Po rejestracji z Azure Portal można zmienić hasło administratora urządzenia.


Możesz zresetować hasło w Azure Portal za pośrednictwem usługi StorSimple Menedżer urządzeń. Aby uzyskać więcej informacji, przejdź do [obszaru zmiana hasła administratora urządzenia](storsimple-8000-change-passwords.md#change-the-device-administrator-password).

## <a name="errors-during-device-registration"></a>Błędy podczas rejestracji urządzenia
Aby zarejestrować urządzenie, należy użyć usługi StorSimple Menedżer urządzeń działającej w programie Microsoft Azure. Wystąpił co najmniej jeden z następujących problemów podczas rejestracji urządzenia.

| Nie. | Komunikat o błędzie | Możliwe przyczyny | Zalecana akcja |
| --- | --- | --- | --- |
| 1 |Błąd 350027: nie można zarejestrować urządzenia w Menedżer urządzeń StorSimple. | |Poczekaj kilka minut, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzać, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 2 |Błąd 350013: Wystąpił błąd podczas rejestrowania urządzenia. Może to być spowodowane niepoprawnym kluczem rejestracji usługi. | |Zarejestruj urządzenie ponownie przy użyciu poprawnego klucza rejestracji usługi. Aby uzyskać więcej informacji, zobacz [Pobieranie klucza rejestracji usługi.](storsimple-8000-manage-service.md#get-the-service-registration-key) |
| 3 |Błąd 350063: uwierzytelnianie w usłudze StorSimple Menedżer urządzeń zostało przesłane, ale rejestracja nie powiodła się. Spróbuj ponownie wykonać operację po pewnym czasie. |Ten błąd wskazuje, że uwierzytelnianie za pomocą usługi ACS zakończyło się pomyślnie, ale wywołanie Register zostało zakończone niepowodzeniem. Może to być spowodowane sporadycznym błędem sieci. |Jeśli problem będzie się powtarzać, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md). |
| 4 |Błąd 350049: nie można nawiązać połączenia z usługą podczas rejestracji. |Po wywołaniu usługi zostanie odebrany wyjątek sieci Web. W niektórych przypadkach może to zostać naprawione przez ponowną próbę wykonania operacji później. |Sprawdź adres IP i nazwę DNS, a następnie spróbuj ponownie wykonać operację. Jeśli problem będzie się powtarzał, [skontaktuj się z pomoc techniczna firmy Microsoft.](storsimple-8000-contact-microsoft-support.md) |
| 5 |Błąd 350031: urządzenie zostało już zarejestrowane. | |Nie trzeba podejmować żadnych działań. |
| 6 |Błąd 350016: Rejestracja urządzenia nie powiodła się. | |Upewnij się, że klucz rejestracji jest prawidłowy. |
| 7 |Invoke-HcsSetupWizard: Wystąpił błąd podczas rejestrowania urządzenia. może to być spowodowane niepoprawnym adresem IP lub nazwą DNS. Sprawdź ustawienia sieci i spróbuj ponownie. Jeśli problem będzie się powtarzał, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md). (Błąd 350050) |Upewnij się, że urządzenie może wysyłać polecenia ping do sieci zewnętrznej. Jeśli nie masz łączności z siecią zewnętrzną, Rejestracja może zakończyć się niepowodzeniem z powodu tego błędu. Ten błąd może być kombinacją co najmniej jednego z następujących elementów:<ul><li>Nieprawidłowy adres IP</li><li>Niepoprawna podsieć</li><li>Nieprawidłowa Brama</li><li>Nieprawidłowe ustawienia DNS</li></ul> |Zobacz kroki opisane w [przykładowym kroku krok po kroku](#step-by-step-storsimple-troubleshooting-example). |
| 8 |Invoke-HcsSetupWizard: bieżąca operacja nie powiodła się z powodu wewnętrznego błędu usługi [0x1FBE2]. Spróbuj ponownie wykonać operację po pewnym czasie. Jeśli problem będzie się powtarzać, skontaktuj się z pomoc techniczna firmy Microsoft. |Jest to błąd ogólny zgłoszony dla wszystkich niewidocznych dla użytkownika błędów usługi lub agenta. Najbardziej typową przyczyną może być to, że uwierzytelnianie usługi ACS nie powiodło się. Możliwa przyczyna błędu polega na tym, że występują problemy z konfiguracją serwera NTP i czas na urządzeniu nie zostały prawidłowo ustawione. |Skoryguj czas (jeśli występują problemy), a następnie ponów próbę wykonania operacji rejestracji. W przypadku użycia Set-HcsSystem-strefy czasowej, aby dostosować strefę czasu, należy zrobić wielką literę każdego wyrazu w strefie czasowej (na przykład "Pacyficzny czas standardowy").  Jeśli ten problem będzie się powtarzać, [skontaktuj się z firmą pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu wykonania następnych kroków. |
| 9 |Ostrzeżenie: nie można aktywować urządzenia. Hasła administratora urządzenia i StorSimple Snapshot Manager nie zostały zmienione. |Jeśli rejestracja nie powiedzie się, hasła administratora i StorSimple Snapshot Manager nie są zmieniane. | |

## <a name="tools-for-troubleshooting-storsimple-deployments"></a>Narzędzia do rozwiązywania problemów z wdrożeniami usługi StorSimple
StorSimple zawiera kilka narzędzi, których można użyć do rozwiązywania problemów z rozwiązaniem StorSimple. Należą do nich:

* Obsługa pakietów i dzienników urządzeń.
* Polecenia cmdlet przeznaczone specjalnie do rozwiązywania problemów.

## <a name="support-packages-and-device-logs-available-for-troubleshooting"></a>Pakiety pomocy technicznej i dzienniki urządzeń dostępne do rozwiązywania problemów
Pakiet pomocy technicznej zawiera wszystkie odpowiednie dzienniki, które mogą pomóc zespołowi pomoc techniczna firmy Microsoftemu rozwiązywać problemy z urządzeniem. Za pomocą program Windows PowerShell dla usługi StorSimple można wygenerować zaszyfrowany pakiet pomocy technicznej, który można następnie udostępnić pracownikowi pomocy technicznej.

### <a name="to-view-the-logs-or-the-contents-of-the-support-package"></a>Aby wyświetlić dzienniki lub zawartość pakietu pomocy technicznej
1. Użyj program Windows PowerShell dla usługi StorSimple, aby wygenerować pakiet pomocy technicznej zgodnie z opisem w temacie [Tworzenie pakietu dla pomocy technicznej i zarządzanie](storsimple-8000-create-manage-support-package.md)nim.
2. Pobierz [skrypt odszyfrowywania](https://gallery.technet.microsoft.com/scriptcenter/Script-to-decrypt-a-a8d1ed65) lokalnie na komputerze klienckim.
3. Użyj tej [procedury krok po kroku](storsimple-8000-create-manage-support-package.md#edit-a-support-package) , aby otworzyć i odszyfrować pakiet pomocy technicznej.
4. Odszyfrowane dzienniki pakietów pomocy technicznej są w formacie ETW/etvx. Aby wyświetlić te pliki w systemie Windows Podgląd zdarzeń, można wykonać następujące czynności:
   
   1. Uruchom polecenie **eventvwr** na kliencie systemu Windows. Spowoduje to uruchomienie Podgląd zdarzeń.
   2. W okienku **Akcje** kliknij pozycję **Otwórz zapisany dziennik** i wskaż pliki dziennika w formacie etvx/ETW (pakiet pomocy technicznej). Teraz możesz wyświetlić plik. Po otwarciu pliku możesz kliknąć prawym przyciskiem myszy i zapisać plik jako tekst.
      
      > [!IMPORTANT]
      > Możesz również użyć polecenia cmdlet **Get-WinEvent** , aby otworzyć te pliki w programie Windows PowerShell. Aby uzyskać więcej informacji, zobacz [Get-WinEvent](https://technet.microsoft.com/library/hh849682.aspx) w dokumentacji dotyczącej poleceń cmdlet programu Windows PowerShell.
     
5. Po otwarciu dzienników w Podgląd zdarzeń należy poszukać następujących dzienników zawierających problemy związane z konfiguracją urządzenia:
   
   * hcs_pfconfig dziennik/Operational
   * hcs_pfconfig/config
6. W plikach dziennika wyszukaj ciągi dotyczące poleceń cmdlet wywoływanych przez Kreatora instalacji. Aby uzyskać listę tych poleceń cmdlet, zobacz [proces Kreatora konfiguracji pierwszego czasu](#first-time-setup-wizard-process) .
7. Jeśli nie możesz ustalić przyczyny problemu, możesz [skontaktować się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu wykonania następnych kroków. Aby uzyskać pomoc, wykonaj kroki opisane w temacie [tworzenie Pomoc techniczna firmy Microsoft żądania pomocy technicznej](storsimple-8000-contact-microsoft-support.md#create-a-support-request) .

## <a name="cmdlets-available-for-troubleshooting"></a>Polecenia cmdlet dostępne do rozwiązywania problemów
Aby wykryć błędy łączności, użyj następujących poleceń cmdlet programu Windows PowerShell.

* `Get-NetAdapter`: To polecenie cmdlet służy do wykrywania kondycji interfejsów sieciowych.
* `Test-Connection`: To polecenie cmdlet służy do sprawdzania łączności sieciowej wewnątrz i poza siecią.
* `Test-HcsmConnection`: To polecenie cmdlet służy do sprawdzania łączności pomyślnie zarejestrowanego urządzenia.
* `Sync-HcsTime`: To polecenie cmdlet służy do wyświetlania czasu urządzenia i wymuszania synchronizacji czasu z serwerem NTP.
* `Enable-HcsPing` i `Disable-HcsPing` : Użyj tych poleceń cmdlet, aby umożliwić hostom pingowanie interfejsów sieciowych na urządzeniu StorSimple. Domyślnie interfejsy sieciowe StorSimple nie odpowiadają na żądania ping.
* `Trace-HcsRoute`: Użyj tego polecenia cmdlet jako narzędzia do śledzenia tras. Wysyła pakiety do każdego routera w drodze do ostatecznego miejsca docelowego w danym okresie, a następnie oblicza wyniki na podstawie pakietów zwróconych z każdego przeskoku. Ponieważ `Trace-HcsRoute` pokazuje stopień utraty pakietów na danym routerze lub łączu, można wskazać, które routery lub łącza mogą powodować problemy z siecią.
* `Get-HcsRoutingTable`: Użyj tego polecenia cmdlet, aby wyświetlić tabelę lokalnego routingu IP.

## <a name="troubleshoot-with-the-get-netadapter-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Get-NetAdapter
W przypadku konfigurowania interfejsów sieciowych na potrzeby wdrożenia urządzenia po raz pierwszy stan sprzętu nie jest dostępny w interfejsie użytkownika usługi StorSimple Menedżer urządzeń, ponieważ urządzenie nie zostało jeszcze zarejestrowane w usłudze. Ponadto blok **kondycji sprzętu** może nie zawsze odzwierciedlać stan urządzenia, szczególnie w przypadku problemów mających wpływ na synchronizację usługi. W takich sytuacjach można użyć `Get-NetAdapter` polecenia cmdlet w celu określenia kondycji i stanu interfejsów sieciowych.

### <a name="to-see-a-list-of-all-the-network-adapters-on-your-device"></a>Aby wyświetlić listę wszystkich kart sieciowych na urządzeniu
1. Rozpocznij program Windows PowerShell dla usługi StorSimple, a następnie wpisz `Get-NetAdapter` . 
2. Użyj danych wyjściowych `Get-NetAdapter` polecenia cmdlet i poniższych wytycznych, aby zrozumieć stan interfejsu sieciowego.
   
   * Jeśli interfejs jest w dobrej kondycji i włączony, stan **numer IfIndex** jest wyświetlany **jako.**
   * Jeśli interfejs jest w dobrej kondycji, ale nie jest fizycznie połączony (przez kabel sieciowy), **numer IfIndex** jest pokazywany jako **wyłączony**.
   * Jeśli interfejs jest w dobrej kondycji, ale nie jest włączony, stan **numer IfIndex** jest pokazywany jako **NotPresent**.
   * Jeśli interfejs nie istnieje, nie jest wyświetlany na tej liście. Interfejs użytkownika usługi StorSimple Menedżer urządzeń w dalszym ciągu będzie wyświetlał tego interfejsu w stanie niepowodzenia.

Aby uzyskać więcej informacji na temat korzystania z tego polecenia cmdlet, przejdź do polecenia [Get-adapter](https://docs.microsoft.com/powershell/module/netadapter/get-netadapter?view=win10-ps) w dokumentacji poleceń cmdlet programu Windows PowerShell.

W poniższych sekcjach przedstawiono przykłady danych wyjściowych z `Get-NetAdapter` polecenia cmdlet.

 W tych przykładach kontroler 0 był kontrolerem pasywnym i został skonfigurowany w następujący sposób:

* Na urządzeniu istniały interfejsy sieciowe dane 0, dane 1, dane 2 i dane 3.
* Karty interfejsu sieciowego DATA 4 i DATA 5 nie zostały zainstalowane; w związku z tym nie są one wyświetlane w danych wyjściowych.
* DATA 0 została włączona.

Kontroler 1 jest aktywnym kontrolerem i został skonfigurowany w następujący sposób:

* Na urządzeniu istniały interfejsy sieciowe danych 0, dane 1, dane 2, dane 3, DATA 4 i DATA 5.
* DATA 0 została włączona.

**Przykładowe dane wyjściowe — kontroler 0**

Poniżej przedstawiono dane wyjściowe z kontrolera 0 (kontroler pasywny). DANE 1, dane 2 i dane 3 nie są połączone. DANE 4 i 5 nie są wyświetlane, ponieważ nie znajdują się one na urządzeniu.

```output
Controller0>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter #2     17       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter        14       NotPresent
Ethernet 2           HCS VNIC                                    13       Up
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
```


**Przykładowe dane wyjściowe — kontroler 1**

Poniżej przedstawiono dane wyjściowe z kontrolera 1 (aktywny kontroler). Tylko interfejs sieciowy DATA 0 na urządzeniu jest skonfigurowany i działa.

```output
Controller1>Get-NetAdapter
Name                 InterfaceDescription                        ifIndex  Status
------               --------------------                        -------  ----------
DATA3                Mellanox ConnectX-3 Ethernet Adapter        18       NotPresent
DATA2                Mellanox ConnectX-3 Ethernet Adapter #2     19       NotPresent
DATA1                Intel(R) 82574L Gigabit Network Co...#2     16       NotPresent
DATA0                Intel(R) 82574L Gigabit Network Conn...     15       Up
Ethernet 2           HCS VNIC                                    13       Up
DATA5                Intel(R) Gigabit ET Dual Port Server...     14       NotPresent
DATA4                Intel(R) Gigabit ET Dual Port Serv...#2     17       NotPresent
```


## <a name="troubleshoot-with-the-test-connection-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Test-Connection
Za pomocą `Test-Connection` polecenia cmdlet można określić, czy urządzenie StorSimple może nawiązać połączenie z siecią zewnętrzną. Jeśli wszystkie parametry sieci, w tym DNS, są poprawnie skonfigurowane w Kreatorze instalacji, można użyć `Test-Connection` polecenia cmdlet do pingowania znanego adresu spoza sieci, na przykład Outlook.com.

Należy włączyć polecenie ping, aby rozwiązać problemy z łącznością z tym poleceniem cmdlet, jeśli polecenie ping jest wyłączone.

Zapoznaj się z poniższymi przykładami danych wyjściowych z `Test-Connection` polecenia cmdlet.

> [!NOTE]
> W pierwszym przykładzie urządzenie jest skonfigurowane z nieprawidłowym systemem DNS. W drugim przykładzie serwer DNS jest prawidłowy.

**Przykładowe dane wyjściowe — Nieprawidłowa usługa DNS**

W poniższym przykładzie nie ma danych wyjściowych dla adresów IPV4 i IPV6, co oznacza, że serwer DNS nie został rozpoznany. Oznacza to, że nie ma łączności z siecią zewnętrzną i należy podać prawidłowy serwer DNS.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
HCSNODE0      outlook.com
```

**Przykładowe dane wyjściowe — poprawna usługa DNS**

W poniższym przykładzie serwer DNS zwraca adres IPV4, co oznacza, że serwer DNS jest prawidłowo skonfigurowany. Pozwala to upewnić się, że istnieje łączność z siecią zewnętrzną.

```output
Source        Destination     IPV4Address      IPV6Address
------        -----------     -----------      -----------
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
HCSNODE0      outlook.com     132.245.92.194
```

## <a name="troubleshoot-with-the-test-hcsmconnection-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Test-HcsmConnection
Użyj `Test-HcsmConnection` polecenia cmdlet dla urządzenia, które jest już połączone i zarejestrowane w usłudze StorSimple Menedżer urządzeń. To polecenie cmdlet umożliwia zweryfikowanie łączności między zarejestrowanym urządzeniem i odpowiadającą StorSimple Menedżer urządzeń usługą. To polecenie można uruchomić na program Windows PowerShell dla usługi StorSimple.

### <a name="to-run-the-test-hcsmconnection-cmdlet"></a>Aby uruchomić Test-HcsmConnection polecenie cmdlet
1. Upewnij się, że urządzenie jest zarejestrowane.
2. Sprawdź stan urządzenia. Jeśli urządzenie zostanie zdezaktywowane, w trybie konserwacji lub offline może zostać wyświetlony jeden z następujących błędów:
   
   * ErrorCode. CiSDeviceDecommissioned — wskazuje, że urządzenie zostało zdezaktywowane.
   * ErrorCode. DeviceNotReady — wskazuje, że urządzenie jest w trybie konserwacji.
   * ErrorCode. DeviceNotReady — wskazuje, że urządzenie nie jest w trybie online.
3. Sprawdź, czy usługa Menedżer urządzeń StorSimple jest uruchomiona (Użyj polecenia cmdlet [Get-ClusterResource](https://technet.microsoft.com/library/ee461004.aspx) ). Jeśli usługa nie jest uruchomiona, mogą zostać wyświetlone następujące błędy:
   
   * ErrorCode. CiSApplianceAgentNotOnline
   * ErrorCode. CisPowershellScriptHcsError — wskazuje, że wystąpił wyjątek podczas wykonywania Get-ClusterResource.
4. Sprawdź token Access Control Service (ACS). Jeśli zgłasza wyjątek sieci Web, może to być spowodowane problemem z bramą, brakującym uwierzytelnianiem serwera proxy, nieprawidłowym systemem DNS lub błędem uwierzytelniania. Mogą pojawić się następujące błędy:
   
   * ErrorCode. CiSApplianceGateway — wskazuje wyjątek HttpStatusCode. BadGateway: usługa rozpoznawania nazw nie mogła rozpoznać nazwy hosta.
   * ErrorCode. CiSApplianceProxy — wskazuje wyjątek HttpStatusCode. ProxyAuthenticationRequired (kod stanu HTTP 407): klient nie może uwierzytelnić się na serwerze proxy.
   * ErrorCode. CiSApplianceDNSError — wskazuje wyjątek WebExceptionStatus. NameResolutionFailure: usługa rozpoznawania nazw nie mogła rozpoznać nazwy hosta.
   * ErrorCode. CiSApplianceACSError — wskazuje, że usługa zwróciła błąd uwierzytelniania, ale istnieje łączność.
     
     Jeśli nie zgłasza wyjątku sieci Web, sprawdź, czy jest to ErrorCode. CiSApplianceFailure. Oznacza to, że urządzenie nie powiodło się.
5. Sprawdź łączność z usługą w chmurze. Jeśli usługa zgłasza wyjątek sieci Web, mogą zostać wyświetlone następujące błędy:
   
   * ErrorCode. CiSApplianceGateway — to oznacza wyjątek HttpStatusCode. BadGateway: pośredni serwer proxy otrzymał Nieprawidłowe żądanie od innego serwera proxy lub z oryginalnego serwera.
   * ErrorCode. CiSApplianceProxy — wskazuje wyjątek HttpStatusCode. ProxyAuthenticationRequired (kod stanu HTTP 407): klient nie może uwierzytelnić się na serwerze proxy.
   * ErrorCode. CiSApplianceDNSError — wskazuje wyjątek WebExceptionStatus. NameResolutionFailure: usługa rozpoznawania nazw nie mogła rozpoznać nazwy hosta.
   * ErrorCode. CiSApplianceACSError — wskazuje, że usługa zwróciła błąd uwierzytelniania, ale istnieje łączność.
     
     Jeśli nie zgłasza wyjątku sieci Web, sprawdź, czy jest to ErrorCode. CiSApplianceSaasServiceError. Wskazuje to na problem z usługą StorSimple Menedżer urządzeń.
6. Sprawdź łączność Azure Service Bus. ErrorCode. CiSApplianceServiceBusError wskazuje, że urządzenie nie może nawiązać połączenia z Service Bus.

Pliki dziennika CiSCommandletLog0Curr. errlog i CiSAgentsvc0Curr. errlog będą miały więcej informacji, takich jak szczegóły wyjątku.

Aby uzyskać więcej informacji na temat sposobu korzystania z polecenia cmdlet, przejdź do pozycji [test-HcsmConnection](https://technet.microsoft.com/library/dn715782.aspx) w dokumentacji referencyjnej programu Windows PowerShell.

> [!IMPORTANT]
> To polecenie cmdlet można uruchomić zarówno dla aktywnego, jak i pasywnego kontrolera.

Zapoznaj się z poniższymi przykładami danych wyjściowych z `Test-HcsmConnection` polecenia cmdlet.

**Przykładowe dane wyjściowe — pomyślnie zarejestrowano urządzenie z systemem StorSimple Update 3**

```output
Controller1>Test-HcsmConnection

Checking device registration state  ... Success
Device registered successfully

Checking primary NTP server [time.windows.com] ... Success

Checking web proxy  ... NOT SET

Checking primary IPv4 DNS server [10.222.118.154] ... Success
Checking primary IPv6 DNS server  ... NOT SET
Checking secondary IPv4 DNS server [10.222.120.24] ... Success
Checking secondary IPv6 DNS server  ... NOT SET

Checking device online  ... Success

Checking device authentication  ... This will take a few minutes.
Checking device authentication  ... Success

Checking connectivity from device to service  ... This will take a few minutes.

Checking connectivity from device to service  ... Success

Checking connectivity from service to device  ... Success

Checking connectivity to Microsoft Update servers  ... Success
Controller1>
```

**Przykładowe dane wyjściowe — urządzenie w trybie offline** 

Ten przykład pochodzi z urządzenia, które ma stan **offline** w Azure Portal.

```output
Checking device registrationstate: Success
Device is registered successfully
Checking connectivity from device to SaaS.. Failure
```

Urządzenie nie może nawiązać połączenia przy użyciu bieżącej konfiguracji serwera proxy sieci Web. Może to być problem z konfiguracją serwera proxy sieci Web lub problemem z łącznością sieciową. W takim przypadku należy upewnić się, że ustawienia serwera proxy sieci Web są prawidłowe, a serwery proxy sieci Web są w trybie online i dostępne.

## <a name="troubleshoot-with-the-sync-hcstime-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Sync-HcsTime
Użyj tego polecenia cmdlet, aby wyświetlić czas urządzenia. Jeśli czas urządzenia ma przesunięcie z serwerem NTP, możesz użyć tego polecenia cmdlet, aby wymusić synchronizację czasu z serwerem NTP.
- Jeśli przesunięcie między urządzeniem a serwerem NTP jest większe niż 5 minut, pojawi się ostrzeżenie. 
- Jeśli przesunięcie przekroczy 15 minut, urządzenie przejdzie w tryb offline. Nadal można użyć tego polecenia cmdlet, aby wymusić synchronizację czasu. 
- Jeśli jednak przesunięcie przekroczy 15 godzin, nie będzie można wymusić synchronizacji czasu i zostanie wyświetlony komunikat o błędzie.

**Przykładowe dane wyjściowe — wymuszona synchronizacja czasu przy użyciu polecenia Sync-HcsTime**

```output
Controller0>Sync-HcsTime
The current device time is 4/24/2015 4:05:40 PM UTC.

Time difference between NTP server and appliance is 00.0824069 seconds. Do you want to resync time with NTP server?
[Y] Yes [N] No (Default is "Y"): Y
Controller0>
```

## <a name="troubleshoot-with-the-enable-hcsping-and-disable-hcsping-cmdlets"></a>Rozwiązywanie problemów za pomocą poleceń cmdlet Enable-HcsPing i Disable-HcsPing
Użyj tych poleceń cmdlet, aby upewnić się, że interfejsy sieciowe na urządzeniu odpowiadają na żądania ping protokołu ICMP. Domyślnie interfejsy sieciowe StorSimple nie odpowiadają na żądania ping. Użycie tego polecenia cmdlet jest najprostszym sposobem, aby dowiedzieć się, czy urządzenie jest w trybie online i jest osiągalne.

**Przykładowe dane wyjściowe — Enable-HcsPing i Disable-HcsPing**

```output
Controller0>
Controller0>Enable-HcsPing
Successfully enabled ping.
Controller0>
Controller0>
Controller0>Disable-HcsPing
Successfully disabled ping.
Controller0>
```

## <a name="troubleshoot-with-the-trace-hcsroute-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Trace-HcsRoute
Użyj tego polecenia cmdlet jako narzędzia do śledzenia tras. Wysyła pakiety do każdego routera w drodze do ostatecznego miejsca docelowego w danym okresie, a następnie oblicza wyniki na podstawie pakietów zwróconych z każdego przeskoku. Ponieważ polecenie cmdlet pokazuje stopień utraty pakietów na danym routerze lub łączu, można wskazać, które routery lub łącza mogą powodować problemy z siecią.

**Przykładowe dane wyjściowe pokazujące sposób śledzenia trasy pakietu przy użyciu polecenia Trace-HcsRoute**

```output
Controller0>Trace-HcsRoute -Target 10.126.174.25

Tracing route to contoso.com [10.126.174.25]
over a maximum of 30 hops:
   0  HCSNode0 [10.126.173.90]
   1  contoso.com [10.126.174.25]

Computing statistics for 25 seconds...
            Source to Here   This Node/Link
Hop  RTT    Lost/Sent = Pct  Lost/Sent = Pct  Address
   0                                           HCSNode0 [10.126.173.90]
                                 0/ 100 =  0%   |
   1    0ms     0/ 100 =  0%     0/ 100 =  0%  contoso.com
[10.126.174.25]

Trace complete.
```

## <a name="troubleshoot-with-the-get-hcsroutingtable-cmdlet"></a>Rozwiązywanie problemów za pomocą polecenia cmdlet Get-HcsRoutingTable
Użyj tego polecenia cmdlet, aby wyświetlić tabelę routingu urządzenia StorSimple. Tabela routingu to zestaw reguł, które mogą pomóc w ustaleniu, gdzie będą kierowane pakiety danych przesyłane przez sieć protokołu IP.

W tabeli routingu są wyświetlane interfejsy i Brama, która kieruje dane do określonych sieci. Zapewnia również metrykę routingu, która jest producentem decyzji dla ścieżki, która ma dotrzeć do określonego miejsca docelowego. Im niższa jest Metryka routingu, tym wyższy poziom preferencji.

Na przykład jeśli masz 2 interfejsy sieciowe, dane 2 i dane 3 połączone z Internetem. Jeśli metryki routingu dla danych 2 i danych 3 są odpowiednio 15 i 261, wówczas dane 2 z niższą metryką routingu są preferowanym interfejsem używanym do nawiązywania połączenia z Internetem.

Jeśli na urządzeniu StorSimple jest uruchomiona Aktualizacja Update 1, interfejs sieciowy DATA 0 ma najwyższy poziom preferencji ruchu w chmurze. Oznacza to, że nawet jeśli istnieją inne interfejsy obsługujące chmurę, ruch w chmurze byłby kierowany przez dane 0.

Jeśli `Get-HcsRoutingTable` polecenie cmdlet zostanie uruchomione bez określenia żadnych parametrów (jak pokazano w poniższym przykładzie), polecenie cmdlet będzie wyprowadzać tabele routingu IPv4 i IPv6. Alternatywnie możesz określić `Get-HcsRoutingTable -IPv4` lub `Get-HcsRoutingTable -IPv6`  uzyskać odpowiednią tabelę routingu.

```output
Controller0>
Controller0>Get-HcsRoutingTable
===========================================================================
Interface List
   14...00 50 cc 79 63 40 ......Intel(R) 82574L Gigabit Network Connection
   12...02 9a 0a 5b 98 1f ......Microsoft Failover Cluster Virtual Adapter
   13...28 18 78 bc 4b 85 ......HCS VNIC
   1...........................Software Loopback Interface 1
   21...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #2
   22...00 00 00 00 00 00 00 e0 Microsoft ISATAP Adapter #3
===========================================================================

IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
            0.0.0.0          0.0.0.0  192.168.111.100  192.168.111.101     15
         127.0.0.0        255.0.0.0         On-link         127.0.0.1    306
         127.0.0.1  255.255.255.255         On-link         127.0.0.1    306
   127.255.255.255  255.255.255.255         On-link         127.0.0.1    306
      169.254.0.0      255.255.0.0         On-link     169.254.1.235    261
      169.254.1.235  255.255.255.255         On-link     169.254.1.235    261
   169.254.255.255  255.255.255.255         On-link     169.254.1.235    261
      192.168.111.0    255.255.255.0         On-link   192.168.111.101    266
   192.168.111.101  255.255.255.255         On-link   192.168.111.101    266
   192.168.111.255  255.255.255.255         On-link   192.168.111.101    266
         224.0.0.0        240.0.0.0         On-link         127.0.0.1    306
         224.0.0.0        240.0.0.0         On-link     169.254.1.235    261
         224.0.0.0        240.0.0.0         On-link   192.168.111.101    266
   255.255.255.255  255.255.255.255         On-link         127.0.0.1    306
   255.255.255.255  255.255.255.255         On-link     169.254.1.235    261
   255.255.255.255  255.255.255.255         On-link   192.168.111.101    266
===========================================================================
Persistent Routes:
   Network Address          Netmask  Gateway Address  Metric
            0.0.0.0          0.0.0.0  192.168.111.100       5
===========================================================================

IPv6 Route Table
===========================================================================
Active Routes:
   If Metric Network Destination      Gateway
   1    306 ::1/128                  On-link
   13    276 fd99:4c5b:5525:d80b::/64 On-link
   13    276 fd99:4c5b:5525:d80b::1/128
                                    On-link
   13    276 fd99:4c5b:5525:d80b::3/128
                                    On-link
   13    276 fe80::/64                On-link
   12    261 fe80::/64                On-link
   13    276 fe80::17a:4eba:7c80:727f/128
                                    On-link
   12    261 fe80::fc97:1a53:e81a:3454/128
                                    On-link
   1    306 ff00::/8                 On-link
   13    276 ff00::/8                 On-link
   12    261 ff00::/8                 On-link
   14    266 ff00::/8                 On-link
===========================================================================
Persistent Routes:
   None

Controller0>
```

## <a name="step-by-step-storsimple-troubleshooting-example"></a>Przykładowy przykład rozwiązywania problemów krok po kroku
W poniższym przykładzie przedstawiono krok po kroku rozwiązywanie problemów z wdrożeniem StorSimple. W przykładowym scenariuszu Rejestracja urządzenia kończy się niepowodzeniem z komunikatem o błędzie z informacją, że ustawienia sieciowe lub nazwa DNS są nieprawidłowe.

Zwrócony komunikat o błędzie:

```output
Invoke-HcsSetupWizard: An error has occurred while registering the device. This could be due to incorrect IP address or DNS name. Please check your network settings and try again. If the problems persist, contact Microsoft Support.
+CategoryInfo: Not specified
+FullyQualifiedErrorID: CiSClientCommunicationErros, Microsoft.HCS.Management.PowerShell.Cmdlets.InvokeHcsSetupWizardCommand
```

Przyczyną błędu może być dowolny z następujących elementów:

* Nieprawidłowa instalacja sprzętu
* Błędne interfejsy sieciowe
* Niepoprawny adres IP, maska podsieci, Brama, podstawowy serwer DNS lub serwer proxy sieci Web
* Nieprawidłowy klucz rejestracji
* Nieprawidłowe ustawienia zapory

### <a name="to-locate-and-fix-the-device-registration-problem"></a>Aby zlokalizować i rozwiązać problem z rejestracją urządzenia
1. Sprawdź konfigurację urządzenia: na aktywnym kontrolerze Uruchom polecenie `Invoke-HcsSetupWizard` .
   
   > [!NOTE]
   > Kreator instalacji musi działać na aktywnym kontrolerze. Aby sprawdzić, czy masz połączenie z aktywnym kontrolerem, zapoznaj się z transparentem przedstawionym w konsoli szeregowej. Transparent wskazuje, czy masz połączenie z kontrolerem 0, czy kontrolerem 1 i czy kontroler jest aktywny, czy pasywny. Aby uzyskać więcej informacji, przejdź do [pozycji identyfikowanie aktywnego kontrolera na urządzeniu](storsimple-8000-controller-replacement.md#identify-the-active-controller-on-your-device).
   
2. Upewnij się, że urządzenie jest prawidłowo połączeniowe: Sprawdź okablowanie sieciowe na płaszczyźnie tylnej urządzenia. Okablowanie jest specyficzne dla modelu urządzenia. Aby uzyskać więcej informacji, przejdź do pozycji [Zainstaluj urządzenie z StorSimple 8100](storsimple-8100-hardware-installation.md) lub [zainstaluj urządzenie StorSimple 8600](storsimple-8600-hardware-installation.md).
   
   > [!NOTE]
   > Jeśli używasz portów sieciowych 10 GbE, musisz użyć dostarczonych kart QSFP-SFP i kabli SFP. Aby uzyskać więcej informacji, zobacz [listę kabli, przełączników i urządzeń nadawczych zalecanych dla portów 10 GbE](storsimple-supported-hardware-for-10-gbe-network-interfaces.md).
  
3. Sprawdź kondycję interfejsu sieciowego:
   
   * Użyj polecenia cmdlet Get-NetAdapter, aby wykryć kondycję interfejsów sieciowych dla danych 0. 
   * Jeśli łącze nie działa, stan **numer IfIndex** będzie wskazywać, że interfejs nie działa. Następnie należy sprawdzić połączenie sieciowe portu z urządzeniem i przełącznikiem. Konieczne jest również wyróżnienie nieprawidłowych kabli. 
   * Jeśli podejrzewasz, że port danych 0 na aktywnym kontrolerze zakończył się niepowodzeniem, możesz to potwierdzić, łącząc się z portem danych 0 na kontrolerze 1. Aby to potwierdzić, odłącz kabel sieciowy od tyłu urządzenia od kontrolera 0, podłącz kabel do kontrolera 1, a następnie ponownie uruchom Get-NetAdapter polecenie cmdlet.
     Jeśli port DATA 0 kontrolera nie powiedzie się, [skontaktuj się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) w celu wykonania następnych kroków. Może być konieczne zastąpienie kontrolera w systemie.
4. Sprawdź łączność z przełącznikiem:
   
   * Upewnij się, że interfejsy sieciowe DATA 0 na kontrolerze 0 i kontrolerze 1 w obudowie podstawowej znajdują się w tej samej podsieci. 
   * Sprawdź koncentrator lub router. Zazwyczaj należy połączyć oba kontrolery z tym samym koncentratorem lub routerem. 
   * Upewnij się, że przełączniki używane do połączenia mają dane 0 dla obu kontrolerów w tej samej sieci vLAN.
5. Eliminowanie wszystkich błędów użytkownika:
   
   * Uruchom ponownie Kreatora instalacji (Uruchom polecenie **Invoke-HcsSetupWizard**), a następnie wprowadź wartości ponownie, aby upewnić się, że nie występują żadne błędy. 
   * Sprawdź używany klucz rejestracji. Ten sam klucz rejestracji może służyć do łączenia wielu urządzeń z usługą StorSimple Menedżer urządzeń. Aby upewnić się, że używasz prawidłowego klucza rejestracji, użyj procedury w temacie [Pobieranie klucza rejestracji usługi](storsimple-8000-manage-service.md#get-the-service-registration-key) .
     
     > [!IMPORTANT]
     > Jeśli masz uruchomioną wiele usług, musisz się upewnić, że klucz rejestracji odpowiedniej usługi jest używany do zarejestrowania urządzenia. Jeśli urządzenie zostało zarejestrowane z nieprawidłową usługą StorSimple Menedżer urządzeń, musisz [skontaktować się z pomoc techniczna firmy Microsoft](storsimple-8000-contact-microsoft-support.md) , aby uzyskać kolejne kroki. Może być konieczne przeprowadzenie resetowania urządzenia do ustawień fabrycznych (co może spowodować utratę danych), a następnie nawiązanie połączenia z zamierzoną usługą.
     > 
     > 
6. Użyj polecenia cmdlet Test-Connection, aby sprawdzić, czy masz połączenie z siecią zewnętrzną. Aby uzyskać więcej informacji, przejdź do [rozwiązywania problemów za pomocą polecenia cmdlet Test-Connection](#troubleshoot-with-the-test-connection-cmdlet).
7. Sprawdź, czy istnieją zakłócenia zapory. Jeśli sprawdzono, że ustawienia wirtualnego adresu IP (VIP), podsieci, bramy i DNS są poprawne i nadal występują problemy z łącznością, istnieje możliwość, że zapora blokuje komunikację między urządzeniem a zewnętrzną siecią. Należy upewnić się, że porty 80 i 443 są dostępne na urządzeniu StorSimple na potrzeby komunikacji wychodzącej. Aby uzyskać więcej informacji, zobacz [wymagania dotyczące sieci dla urządzenia StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
8. Sprawdź dzienniki. Przejdź do usługi [support Packages i dzienników urządzeń dostępnych do rozwiązywania problemów](#support-packages-and-device-logs-available-for-troubleshooting).
9. Jeśli powyższe kroki nie rozwiązują problemu, [skontaktuj się z](storsimple-8000-contact-microsoft-support.md) pomocą techniczną pomoc techniczna firmy Microsoft.

## <a name="next-steps"></a>Następne kroki
[Dowiedz się, jak rozwiązywać problemy z urządzeniem StorSimple przy użyciu narzędzia diagnostycznego](storsimple-8000-diagnostics.md).

<!--Link references-->

[1]: https://technet.microsoft.com/library/dd379547(v=ws.10).aspx
[2]: https://technet.microsoft.com/library/dd392266(v=ws.10).aspx 
