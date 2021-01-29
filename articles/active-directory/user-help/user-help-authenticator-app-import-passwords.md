---
title: Importowanie haseł do aplikacji Microsoft Authenticator — usługa Azure AD
description: Jak zaimportować hasła do aplikacji uwierzytelniania firmy Microsoft z popularnych menedżerów haseł.
services: active-directory
author: curtand
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.subservice: user-help
ms.topic: end-user-help
ms.date: 01/28/2021
ms.author: curtand
ms.reviewer: olhaun
ms.openlocfilehash: b0d7eeeb840a3efc560c20310b38bee93a038795
ms.sourcegitcommit: d1e56036f3ecb79bfbdb2d6a84e6932ee6a0830e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/29/2021
ms.locfileid: "99056024"
---
# <a name="import-passwords-into-the-microsoft-authenticator-app"></a>Importowanie haseł do aplikacji Microsoft Authenticator

Microsoft Authenticator obsługuje importowanie haseł z usług Google Chrome, Firefox, LastPass, Bitwarden i RoboForm. Jeśli firma Microsoft nie obsługuje obecnie istniejącego Menedżera haseł, możesz [ręcznie wprowadzić poświadczenia logowania do naszego pliku CSV szablonu](https://go.microsoft.com/fwlink/?linkid=2134938). Aby zaimportować istniejące hasła i zarządzać nimi w aplikacji uwierzytelniającej, po prostu wyeksportuj hasła z istniejącego Menedżera haseł do formatu wartości rozdzielanych przecinkami (CSV). Następnie zaimportuj wyeksportowany plik CSV do uwierzytelniania w naszym rozszerzeniu przeglądarki Chrome lub bezpośrednio w aplikacji uwierzytelniania (Android i iOS).

## <a name="import-from-google-chrome-or-android-smart-lock"></a>Importuj z usługi Google Chrome lub Android Smart Lock

Hasła z usługi Google Chrome lub Android Smart Lock można zaimportować do uwierzytelniania na telefonie Smartphone lub komputerze stacjonarnym. Oto co możesz zrobić:

- [Importowanie z programu Chrome w systemach Android i iOS](#import-from-chrome-on-android-and-ios)
- [Importuj z przeglądarki Chrome Desktop](#import-from-chrome-desktop-browser)

### <a name="import-from-chrome-on-android-and-ios"></a>Importowanie z programu Chrome w systemach Android i iOS

Użytkownicy usługi Google Chrome na telefony z systemami Android i Apple mogą zaimportować swoje hasła bezpośrednio z telefonu przy użyciu kilku prostych kroków.

1. Zainstaluj aplikację Authenticator na telefonie i Otwórz kartę **hasła** .

1. Zaloguj się do usługi Google Chrome na telefonie.

1. Naciśnij ![ menu wielokropka Google Chrome ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) w prawym górnym rogu dla telefonów z systemem Android lub w prawym dolnym rogu dla urządzeń z systemem iOS, a następnie naciśnij pozycję **Ustawienia.**

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Lokalizacja menu ustawień Google Chrome](./media/user-help-authenticator-app-import-passwords/android-settings-menu.png)
   iOS | ![Ikona menu ustawień Google Chrome](./media/user-help-authenticator-app-import-passwords/apple-settings-menu.png)

1. W obszarze **Ustawienia** Otwórz pozycję **hasła**.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Lokalizacja polecenia andoid Chrome hasła](./media/user-help-authenticator-app-import-passwords/android-passwords-location.png)
   iOS | ![Lokalizacja polecenia haseł Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-passwords-location.png)

1. Na urządzeniach z systemem Android naciśnij ![ menu wielokropka Google Chrome ](./media/user-help-authenticator-app-import-passwords/ellipsis-chrome.png) w prawym górnym rogu dla telefonów z systemem Android lub w prawym dolnym rogu dla urządzeń z systemem iOS, a następnie naciśnij pozycję **Eksportuj hasła**.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Lokalizacja lokalizacji eksportu programu Android Chrome](./media/user-help-authenticator-app-import-passwords/android-export-passwords-location.png)
   iOS | ![Lokalizacja innych haseł eksportowania programu Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-export-passwords-location.png)

   Musisz podać numer PIN, odcisk palca lub rozpoznawanie twarzy. Potwierdź swoją tożsamość i ponownie naciśnij pozycję **Eksportuj hasła** , aby rozpocząć eksportowanie.

1. Po wyeksportowaniu haseł program Chrome wyświetli monit o wybranie aplikacji, do której importujesz. Wybierz pozycję **wystawca uwierzytelnienia** , aby rozpocząć importowanie haseł. Gdy wszystko będzie gotowe, otrzymasz informację o stanie importu.

   &nbsp; | &nbsp;
   ---------- | --------
   Android | ![Lokalizacja importowania haseł dla programu Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
   iOS | ![Lokalizacja importowania haseł w programie Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

### <a name="import-from-chrome-desktop-browser"></a>Importuj z przeglądarki Chrome Desktop

Przed rozpoczęciem należy zainstalować i zalogować się do [rozszerzenia Microsoft Auto](https://chrome.google.com/webstore/detail/microsoft-autofill/fiedbfgcleddlbcmgdigjgdfcggjcion) -in w przeglądarce Chrome.

1. Otwórz [Menedżera haseł Google](https://passwords.google.com) w dowolnej przeglądarce. Jeśli jeszcze tego nie zrobiono, zaloguj się do swojego konta Google.

1. Wybierz ikonę koła zębatego ![Ikona koła zębatego Menedżera haseł](./media/user-help-authenticator-app-import-passwords/desktop-password-manager-gear.png) Aby otworzyć stronę ustawienia hasła.

1. Wybierz pozycję **Eksportuj**, a następnie na następnej stronie wybierz pozycję **Eksportuj** ponownie, aby rozpocząć eksportowanie haseł. Podaj hasło Google, gdy zostanie wyświetlony monit o potwierdzenie tożsamości. Gdy wszystko będzie gotowe, otrzymasz informację o stanie importu.

   ![Lokalizacja polecenia eksportowania haseł w przeglądarce Chrome programu Desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-export-passwords-location.png)

1. Otwórz rozszerzenie Autowypełnianie i wybierz pozycję **Ustawienia**.

   ![Lokalizacja ustawień autouzupełniania w przeglądarce Chrome programu Desktop](./media/user-help-authenticator-app-import-passwords/desktop-chrome-autofill-settings.png)

1. Wybierz pozycję **Importuj dane** , aby otworzyć okno dialogowe. Następnie wybierz pozycję **Wybierz plik** , aby zlokalizować i ZAIMPORTOWAĆ plik CSV.

   ![Lokalizacja pliku CSV importu programu Desktop Chrome Browser](./media/user-help-authenticator-app-import-passwords/desktop-chrome-import-csv.png)

## <a name="import-from-firefox"></a>Importuj z przeglądarki Firefox

Firefox zezwala tylko na Eksportowanie haseł z przeglądarki pulpitu, więc przed zaimportowaniem haseł z programu Firefox upewnij się, że masz dostęp do przeglądarki Firefox Desktop.

1. Zaloguj się do najnowszej wersji programu Firefox na pulpicie i wybierz ![Menu Firefox "Hamburger"](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) w prawym górnym rogu ekranu.

1. Wybierz pozycję **logowania i hasła**.

   ![Lokalizacja logowania i hasła w przeglądarce Firefox dla komputerów stacjonarnych](./media/user-help-authenticator-app-import-passwords/desktop-firefox-passwords-location.png)

1. Na stronie Firefox Lockwise wybierz ![ menu wielokropka przeglądarki Firefox ](./media/user-help-authenticator-app-import-passwords/desktop-firefox-ellipsis-icon.png) , wybierz pozycję **Eksportuj nazwy logowania**, a następnie potwierdź zamiar, wybierając pozycję **Eksportuj**. Zostanie wyświetlony monit o zidentyfikowanie siebie przez wprowadzenie numeru PIN, hasła urządzenia lub zeskanowania odcisków palców. Po pomyślnym zidentyfikowaniu przeglądarki Firefox eksportuje hasła w formacie CSV do wybranej lokalizacji.

   ![Lokalizacja eksportowania haseł w przeglądarce Firefox dla komputerów stacjonarnych](./media/user-help-authenticator-app-import-passwords/desktop-firefox-export-passwords-location.png)

1. Hasła można zaimportować do uwierzytelniania przy użyciu przeglądarki pulpitu lub telefonów z systemem iOS lub Android. Aby zaimportować do aplikacji Authenticator na telefonie:

      1. Przenieś wyeksportowany plik CSV na telefon z systemem Android lub iOS przy użyciu preferowanego i bezpiecznego sposobu, a następnie pobierz go. Następnie należy udostępnić plik CSV z aplikacją Authenticator, aby rozpocząć importowanie.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Lokalizacja importowania haseł dla programu Android Chrome](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Lokalizacja importowania haseł w programie Apple Chrome](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po pomyślnym zaimportowaniu hasła do uwierzytelniania, usuń plik CSV z pulpitu lub telefonu komórkowego.

## <a name="import-from-lastpass"></a>Importuj z LastPass

LastPass obsługuje eksport haseł tylko z przeglądarki komputerowej, dlatego przed rozpoczęciem importowania haseł upewnij się, że masz dostęp do przeglądarki pulpitu.

1. Zaloguj się do [witryny sieci Web LastPass](https://lastpass.com) i wybierz **Opcje zaawansowane**, a następnie wybierz pozycję **Eksportuj**.

   ![Lokalizacja eksportowania haseł w programie Desktop LastPass](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-export-passwords-location.png)

1. Zidentyfikuj siebie, gdy zostanie wyświetlony monit o podanie hasła głównego. Następnie zostaną wyświetlone wyeksportowane hasła na stronie sieci Web.

1. Skopiuj zawartość strony sieci Web.

1. Otwórz Notatnik (lub ulubiony Edytor tekstu) i wklej skopiowaną zawartość.

1. Zapisz ten plik Notatnika, wybierając pozycję **plik** &gt; **Zapisz jako**. Podaj nazwę kończącą się ciągiem ". csv" (np. LastPass.csv) w bezpiecznej lokalizacji na pulpicie.

   ![Plik CSV zapisywania LastPass w programie Desktop](./media/user-help-authenticator-app-import-passwords/desktop-lastpass-save-import-file.png)

1. Hasła można zaimportować do uwierzytelniania w przeglądarce klasycznej lub na telefonach z systemem iOS lub Android. Aby zaimportować do aplikacji Authenticator na telefonie:

      1. Przenieś wyeksportowany plik CSV na telefon smartphone przy użyciu preferowanego i bezpiecznego sposobu, a następnie pobierz go. Następnie należy udostępnić plik CSV z aplikacją Authenticator, aby rozpocząć importowanie.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Lokalizacja importowania haseł LastPass systemu Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Lokalizacja importowania haseł w programie Apple LastPass](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po pomyślnym zaimportowaniu hasła do uwierzytelniania, usuń plik CSV z pulpitu lub telefonu komórkowego.

## <a name="import-from-bitwarden"></a>Importuj z Bitwarden

Bitwarden obsługuje eksport haseł tylko z przeglądarki komputerowej, dlatego przed rozpoczęciem importowania haseł upewnij się, że masz dostęp do przeglądarki pulpitu.

1. Zaloguj się do programu https://vault.bitwarden.com/ i wybierz pozycję **Narzędzia** &gt; **Eksportuj magazyn**. Wybierz format pliku jako plik CSV, podaj hasło główne, a następnie wybierz pozycję **Eksportuj magazyn** , aby rozpocząć eksportowanie.

   ![Lokalizacja magazynu eksportowania Bitwarden](./media/user-help-authenticator-app-import-passwords/desktop-bitwarden-export-command-location.png)

1. Hasła można zaimportować do uwierzytelniania w przeglądarce klasycznej lub na telefonach z systemem iOS lub Android. Aby zaimportować do aplikacji Authenticator na telefonie:

      1. Przenieś wyeksportowany plik CSV na telefon smartphone przy użyciu preferowanego i bezpiecznego sposobu, a następnie pobierz go. Następnie należy udostępnić plik CSV z aplikacją Authenticator, aby rozpocząć importowanie.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Lokalizacja importowania haseł Bitwarden systemu Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Lokalizacja importowania haseł w programie Apple Bitwarden](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po pomyślnym zaimportowaniu hasła do uwierzytelniania, usuń plik CSV z pulpitu lub telefonu komórkowego.

## <a name="import-from-roboform"></a>Importuj z RoboForm

RoboForm umożliwia eksportowanie haseł tylko z aplikacji klasycznej, dlatego przed rozpoczęciem importowania upewnij się, że masz dostęp do aplikacji RoboForm na pulpicie.

1. Zacznij RoboForm od klienta pulpitu i zaloguj się na swoim koncie.

1. Wybierz **Opcje** z menu **RoboForm** .

   ![Menu opcji RoboForm pulpitu](./media/user-help-authenticator-app-import-passwords/desktop-roboform-options.png)

1. Wybierz pozycję **konto &** &gt; **eksport** danych.

   ![Lokalizacja polecenia eksportu RoboForm pulpitu](./media/user-help-authenticator-app-import-passwords/desktop-roboform-accounts-data.png)

1. Wybierz bezpieczną lokalizację, w której ma zostać zapisany wyeksportowany plik. Wybierz pozycję **logowania** jako typ **danych** i wybierz plik CSV jako format, a następnie wybierz pozycję **Eksportuj**.

   ![Okno dialogowe Eksport RoboForm pulpitu](./media/user-help-authenticator-app-import-passwords/desktop-roboform-export-dialog.png)

1. Potwierdź swój cel, a następnie plik CSV zostanie wyeksportowany do wybranej lokalizacji.

   ![Okno dialogowe potwierdzania eksportu RoboForm pulpitu](./media/user-help-authenticator-app-import-passwords/desktop-roboform-confirmation.png)

1. Hasła można zaimportować do uwierzytelniania w przeglądarce klasycznej lub na telefonach z systemem iOS lub Android. Aby zaimportować do aplikacji Authenticator na telefonie:

      1. Przenieś wyeksportowany plik CSV na telefon smartphone przy użyciu preferowanego i bezpiecznego sposobu, a następnie pobierz go. Następnie należy udostępnić plik CSV z aplikacją Authenticator, aby rozpocząć importowanie.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Lokalizacja importowania haseł RoboForm systemu Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Lokalizacja importowania haseł w programie Apple RoboForm](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po pomyślnym zaimportowaniu hasła do uwierzytelniania, usuń plik CSV z pulpitu lub telefonu komórkowego.

## <a name="import-by-creating-a-csv"></a>Importuj przez utworzenie pliku CSV

Jeśli nie wymieniono w tym artykule kroków dotyczących importowania haseł z Menedżera haseł, można utworzyć wolumin CSV, którego można użyć do zaimportowania haseł do uwierzytelniania. Firma Microsoft zaleca, aby wykonać następujące czynności na pulpicie w celu ułatwienia formatowania.

1. Na pulpicie [Pobierz i Otwórz nasz szablon importu](https://go.microsoft.com/fwlink/?linkid=2134938). Jeśli jesteś użytkownikiem telefonu iPhone, Safari i pęku kluczy, możesz teraz przejść do kroku 4.

1. Wyeksportuj swoje hasła z istniejącego Menedżera haseł w niezaszyfrowanym pliku CSV.

1. Skopiuj odpowiednie kolumny z wyeksportowanego pliku CSV do pliku CSV szablonu, a następnie Zapisz.

1. Jeśli nie masz wyeksportowanego woluminu CSV, możesz skopiować każdą nazwę logowania z istniejącego Menedżera haseł do pliku CSV szablonu. Nie usuwaj ani nie zmieniaj wiersza nagłówka. Po zakończeniu Sprawdź integralność danych przed rozpoczęciem następnego kroku.

1. Hasła można zaimportować do uwierzytelniania w przeglądarce klasycznej lub na telefonach z systemem iOS lub Android. Aby zaimportować do aplikacji Authenticator na telefonie:

      1. Przenieś wyeksportowany plik CSV na telefon smartphone przy użyciu preferowanego i bezpiecznego sposobu, a następnie pobierz go. Następnie należy udostępnić plik CSV z aplikacją Authenticator, aby rozpocząć importowanie.

         &nbsp; | &nbsp;
         ---------- | --------
         Android | ![Lokalizacja importowania plików CSV systemu Android](./media/user-help-authenticator-app-import-passwords/android-chrome-import.png)
         iOS | ![Lokalizacja plików importu Apple CSV](./media/user-help-authenticator-app-import-passwords/apple-chrome-import.png)

      1. Po pomyślnym zaimportowaniu hasła do uwierzytelniania, usuń plik CSV z pulpitu lub telefonu komórkowego.

## <a name="troubleshooting-steps"></a>Kroki rozwiązywania problemów

Najbardziej typową przyczyną niepowodzenia Imports jest niepoprawne formatowanie w pliku CSV. Aby rozwiązać problem, można spróbować wykonać poniższe czynności.

- Zapoznaj się z tym artykułem, aby sprawdzić, czy jest już obsługiwane Importowanie haseł z bieżącego Menedżera haseł. W razie potrzeby można ponowić próbę importowania, wykonując kroki opisane dla odpowiedniego dostawcy.

- Jeśli obecnie nie obsługujemy importowania formatu Menedżera haseł, możesz ponowić próbę, [tworząc ręcznie plik CSV](#import-by-creating-a-csv).

- Integralność danych CSV można zweryfikować, korzystając z następujących sugestii:

  - Pierwszy wiersz musi zawierać nagłówek z trzema kolumnami: **adres URL**, **Nazwa użytkownika** i **hasło**.

  - Każdy wiersz musi zawierać wartość w kolumnie **adresy URL** i **hasła** .

- Możesz odtworzyć wolumin CSV, wklejając zawartość w [pliku szablonu CSV](https://go.microsoft.com/fwlink/?linkid=2134938).

- Jeśli nic nie działa, zgłoś problem przy użyciu linku **Wyślij opinię** z ustawień aplikacji uwierzytelniania.
