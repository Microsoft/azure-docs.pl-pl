---
title: Samoobsługowe resetowanie haseł — usługa Azure Active Directory
description: Często zadawane pytania dotyczące samoobsługowego resetowania hasła usługi Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/15/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.collection: M365-identity-device-management
ms.openlocfilehash: d1246b5b980f60c2f3e65aa5b32a7d79dd6efc7d
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81407172"
---
# <a name="password-management-frequently-asked-questions"></a>Często zadawane pytania dotyczące zarządzania hasłami

Poniżej przedstawiono kilka często zadawanych pytań (FAQ) dotyczących wszystkich rzeczy związanych z resetowaniem hasła.

Jeśli masz ogólne pytanie dotyczące usługi Azure Active Directory (Azure AD) i samoobsługowego resetowania haseł (SSPR), na które nie udzielono odpowiedzi, możesz poprosić społeczność o pomoc na [forum usługi Azure AD.](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD) Członkami społeczności są inżynierowie, menedżerowie produktów, MVPs i inni informatycy.

To często zadawane pytania jest podzielone na następujące sekcje:

* [Pytania dotyczące rejestracji resetowania hasła](#password-reset-registration)
* [Pytania dotyczące resetowania hasła](#password-reset)
* [Pytania dotyczące zmiany hasła](#password-change)
* [Pytania dotyczące raportów zarządzania hasłami](#password-management-reports)
* [Pytania dotyczące zapisywania haseł](#password-writeback)

## <a name="password-reset-registration"></a>Rejestracja w funkcji resetowania haseł

* **P: Czy moi użytkownicy mogą rejestrować własne dane resetowania hasła?**

  > **Odpowiedź:** tak. Tak długo, jak resetowanie hasła jest włączone i są licencjonowane,https://aka.ms/ssprsetup) użytkownicy mogą przejść do portalu rejestracji resetowania hasła ( aby zarejestrować swoje informacje uwierzytelniania. Użytkownicy mogą również rejestrowaćhttps://myapps.microsoft.com)się za pośrednictwem Panelu dostępu ( . Aby zarejestrować się w Panelu dostępu, muszą wybrać swoje zdjęcie profilowe, wybrać **opcję Profil**, a następnie wybrać opcję Zarejestruj **hasło resetowania.**
  >
  >
* **Pyt.: Jeśli włączę resetowanie hasła dla grupy, a następnie zdecydować się włączyć go dla wszystkich są moi użytkownicy wymagane ponownej rejestracji?**

  > **Odpowiedź:** Nie. Użytkownicy, którzy wypełnili dane uwierzytelnienia, nie muszą ponownie się rejestrować.
  >
  >
* **Pyt.: Czy mogę zdefiniować dane resetowania hasła w imieniu moich użytkowników?**

  > **Odp.:** Tak, możesz to zrobić za pomocą usługi Azure AD Connect, programu PowerShell, [portalu Azure lub](https://portal.azure.com)centrum [administracyjnego usługi Microsoft 365.](https://admin.microsoft.com) Aby uzyskać więcej informacji, zobacz [Dane używane przez samoobsługowe resetowanie hasła usługi Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **Pyt.: Czy można synchronizować dane w przypadku pytań zabezpieczających z lokalnych?**

  > **Odp.:** Nie, dziś nie jest to możliwe.
  >
  >
* **Pyt.: Czy moi użytkownicy mogą rejestrować dane w taki sposób, że inni użytkownicy nie widzą tych danych?**

  > **Odpowiedź:** tak. Gdy użytkownicy rejestrują dane przy użyciu portalu rejestracji resetowania hasła, dane są zapisywane w prywatnych polach uwierzytelniania, które są widoczne tylko dla administratorów globalnych i użytkownika.
  >
  >
* **P: Czy moi użytkownicy muszą być zarejestrowani, zanim będą mogli użyć resetowania hasła?**

  > **Odpowiedź:** Nie. Jeśli zdefiniujesz wystarczającą ilość informacji uwierzytelniających w ich imieniu, użytkownicy nie muszą się rejestrować. Resetowanie hasła działa tak długo, jak długo zostały prawidłowo sformatowane dane przechowywane w odpowiednich polach w katalogu.
  >
  >
* **Pyt.: Czy mogę zsynchronizować lub ustawić pola telefonu uwierzytelniania, uwierzytelniania lub alternatywnego telefonu uwierzytelniania w imieniu moich użytkowników?**

  > **Odp.:** Pola, które mogą być ustawione przez administratora globalnego są zdefiniowane w artykule [Wymagania dotyczące danych SSPR](howto-sspr-authenticationdata.md).
  >
  >
* **Pyt.: W jaki sposób portal rejestracji określa, które opcje mają być wyświetlane użytkownikom?**

  > **Odp.:** Portal rejestracji resetowania hasła zawiera tylko opcje, które zostały włączone dla użytkowników. Te opcje znajdują się w sekcji **Zasady resetowania hasła użytkownika** na karcie **Konfiguruj** katalog. Jeśli na przykład nie włączysz pytań zabezpieczających, użytkownicy nie będą mogli zarejestrować się w tej opcji.
  >
  >
* **P: Kiedy użytkownik jest uważany za zarejestrowany?**

  > **Odp.:** Użytkownik jest uważany za zarejestrowanego dla sspr, gdy zarejestrował co najmniej **liczbę metod wymaganych do zresetowania** hasła ustawionego w [witrynie Azure portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetowanie hasła

* **Pyt.: Czy uniemożliwiasz użytkownikom wielokrotne próby zresetowania hasła w krótkim czasie?**

  > **Odp.:** Tak, istnieją funkcje zabezpieczeń wbudowane w resetowanie hasła, aby chronić je przed niewłaściwym użyciem. 
  >
  > Użytkownicy mogą wypróbować tylko pięć prób resetowania hasła w ciągu 24 godzin, zanim zostaną zablokowani na 24 godziny. 
  >
  > Użytkownicy mogą próbować zweryfikować numer telefonu, wysłać WIADOMOŚĆ SMS lub zweryfikować pytania zabezpieczające i odpowiedzi tylko pięć razy w ciągu godziny, zanim zostaną zablokowane na 24 godziny. 
  >
  > Użytkownicy mogą wysłać wiadomość e-mail maksymalnie 10 razy w ciągu 10 minut, zanim zostanie zablokowana na 24 godziny.
  >
  > Liczniki są resetowane, gdy użytkownik zresetuje swoje hasło.
  >
  >
* **P: Jak długo należy czekać na otrzymanie wiadomości e-mail, SMS lub połączenia telefonicznego z resetowania hasła?**

  > **Odp.:** Wiadomości e-mail, wiadomości SMS i połączenia telefoniczne powinny nadejść w mniej niż minutę. Normalny przypadek wynosi od 5 do 20 sekund.
  > Jeśli nie otrzymasz powiadomienia w tym przedziale czasowym:
  > * Sprawdź folder wiadomości-śmieci.
  > * Sprawdź, czy numer lub adres e-mail, z którymi się kontaktujesz, jest tym, którego oczekujesz.
  > * Sprawdź, czy dane uwierzytelniania w katalogu są poprawnie sformatowane, na przykład +1 4255551234 lub *contoso.com użytkownika\@*. 
* **Pyt.: Jakie języki są obsługiwane przez resetowanie hasła?**

  > **Odp.:** Interfejs użytkownika resetowania hasła, wiadomości SMS i połączenia głosowe są zlokalizowane w tych samych językach, które są obsługiwane w usłudze Office 365.
  >
  >
* **Pyt.: Jakie części środowiska resetowania hasła są oznaczane po ustawieniu elementów znakowania organizacyjnego na karcie konfigurowania mojego katalogu?**

  > **Odp.:** Portal resetowania hasła pokazuje logo organizacji i umożliwia skonfigurowanie łącza "Skontaktuj się z administratorem", aby wskazać niestandardowy adres e-mail lub adres URL. Każda wiadomość e-mail wysyłana przez resetowanie hasła zawiera logo, kolory i nazwę organizacji w treści wiadomości e-mail i jest dostosowana do ustawień dla tej konkretnej nazwy.
  >
  >
* **Pyt.: Jak mogę edukować użytkowników o tym, gdzie można zresetować hasła?**

  > **Odp.:** Wypróbuj niektóre z sugestii w naszym artykule [wdrażania sspr.](howto-sspr-deployment.md#plan-communications)
  >
  >
* **P: Czy mogę korzystać z tej strony z urządzenia mobilnego?**

  > **Odp.:** Tak, ta strona działa na urządzeniach mobilnych.
  >
  >
* **Pyt.: Czy obsługujesz odblokowywanie lokalnych kont usługi Active Directory, gdy użytkownicy resetują swoje hasła?**

  > **Odpowiedź:** tak. Gdy użytkownik resetuje swoje hasło, jeśli stornia zwrotne hasła zostało wdrożone za pośrednictwem usługi Azure AD Connect, to konto tego użytkownika jest automatycznie odblokowywane podczas resetowania hasła.
  >
  >
* **Pyt.: Jak mogę zintegrować resetowanie hasła bezpośrednio do środowiska logowania na pulpicie użytkownika?**

  > **Odp.:** Jeśli jesteś klientem usługi Azure AD Premium, możesz zainstalować program Microsoft Identity Manager bez dodatkowych kosztów i wdrożyć lokalne rozwiązanie resetowania hasła.
  >
  >
* **Pyt.: Czy mogę ustawić różne pytania zabezpieczające dla różnych ustawień regionalnych?**

  > **Odp.:** Nie, dziś nie jest to możliwe.
  >
  >
* **Pyt.: Ile pytań można skonfigurować dla opcji uwierzytelniania pytań zabezpieczających?**

  > **Odp.:** W [witrynie Azure Portal](https://portal.azure.com)można skonfigurować maksymalnie 20 niestandardowych pytań zabezpieczających.
  >
  >
* **P: Jak długo mogą być pytania zabezpieczające?**

  > **Odp.:** Pytania zabezpieczające mogą mieć od 3 do 200 znaków.
  >
  >
* **P: Jak długo mogą być odpowiedzi na pytania zabezpieczające?**

  > **Odp.:** Odpowiedzi mogą mieć od 3 do 40 znaków.
  >
  >
* **P: Czy zduplikowane odpowiedzi na pytania zabezpieczające są odrzucane?**

  > **Odp.:** Tak, odrzucamy zduplikowane odpowiedzi na pytania zabezpieczające.
  >
  >
* **Pyt.: Czy użytkownik może zarejestrować to samo pytanie zabezpieczające więcej niż raz?**

  > **Odpowiedź:** Nie. Po zarejestrowaniu określonego pytania przez użytkownika nie może zarejestrować się na to pytanie po raz drugi.
  >
  >
* **Pyt.: Czy można ustawić minimalny limit pytań zabezpieczających do rejestracji i resetowania?**

  > **Odp.:** Tak, jeden limit można ustawić dla rejestracji, a drugi do resetowania. Do rejestracji może być wymaganych od trzech do pięciu pytań zabezpieczających, a do zresetowania może być wymagane od trzech do pięciu pytań.
  >
  >
* **Pyt.: Skonfigurowano moje zasady, aby wymagać od użytkowników używania pytań zabezpieczających do resetowania, ale administratorzy platformy Azure wydają się być skonfigurowani inaczej.**

  > **Odp.:** Jest to oczekiwane zachowanie. Firma Microsoft wymusza silną domyślną zasadę resetowania hasła z użyciem dwóch bram dla dowolnej roli administratora platformy Azure. Uniemożliwia to administratorom korzystanie z pytań zabezpieczających. Więcej informacji na temat tej zasady można znaleźć w [artykule Zasady i ograniczenia haseł w usłudze Azure Active Directory.](concept-sspr-policy.md)
  >
  >
* **Pyt.: Jeśli użytkownik zarejestrował więcej niż maksymalna liczba pytań wymaganych do zresetowania, w jaki sposób pytania zabezpieczające są wybierane podczas resetowania?**

  > **O:** *N* liczba pytań zabezpieczających są wybierane losowo z całkowitej liczby pytań, dla których użytkownik zarejestrował się, gdzie *N* jest kwotą ustawioną dla opcji Liczba **pytań wymaganych do zresetowania.** Na przykład jeśli użytkownik zarejestrował pięć pytań zabezpieczających, ale tylko trzy są wymagane do zresetowania hasła, trzy z pięciu pytań są wybierane losowo i są prezentowane po zresetowaniu. Aby zapobiec wbijaniu pytań, jeśli użytkownik otrzymuje odpowiedzi na pytania źle proces wyboru rozpoczyna się od nowa.
  >
  >
* **P: Jak długo są ważne jednorazowe kody dostępu do poczty e-mail i SMS?**

  > **Odp.:** Okres istnienia sesji resetowania hasła wynosi 15 minut. Od początku operacji resetowania hasła użytkownik ma 15 minut na zresetowanie hasła. Jednorazowy kod dostępu do wiadomości e-mail i SMS jest ważny przez 5 minut podczas sesji resetowania hasła.
  >
  >
* **P: Czy mogę zablokować użytkownikom zresetowanie hasła?**

  > **Odp.:** Tak, jeśli używasz grupy do włączania funkcji SSPR, możesz usunąć indywidualnego użytkownika z grupy, która umożliwia użytkownikom resetowanie hasła. Jeśli użytkownik jest administratorem globalnym, zachowa możliwość zresetowania hasła i nie można go wyłączyć.
  >
  >

## <a name="password-change"></a>Zmiana hasła

* **P: Gdzie moi użytkownicy powinni zmieniać swoje hasła?**

  > **Odp.:** Użytkownicy mogą zmieniać swoje hasła w dowolnym miejscu, w miejscu, w których widzą swoje zdjęcie profilowe lub ikonę, na przykład w prawym górnym rogu portalu [usługi Office 365](https://portal.office.com) lub [w panelu dostępu.](https://myapps.microsoft.com) Użytkownicy mogą zmieniać swoje hasła na [stronie Profil panelu dostępu](https://account.activedirectory.windowsazure.com/r#/profile). Użytkownicy mogą również zostać poproszeni o automatyczną zmianę haseł na stronie logowania usługi Azure AD, jeśli ich hasła wygasły. Na koniec użytkownicy mogą przejść bezpośrednio do [portalu zmiany hasła usługi Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) bezpośrednio, jeśli chcą zmienić swoje hasła.
  >
  >
* **Pyt.: Czy moi użytkownicy mogą być powiadamiani w portalu pakietu Office, gdy ich hasło lokalne wygaśnie?**

  > **Odp.:** Tak, jest to możliwe w przypadku korzystania z usług federacyjnych Active Directory (AD FS). Jeśli używasz usług AD FS, postępuj zgodnie z instrukcjami zawartymi w artykule [Wysyłanie oświadczeń zasad haseł z usługami AD FS.](https://technet.microsoft.com/windows-server-docs/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) Jeśli używasz synchronizacji skrótów haseł, nie jest to obecnie możliwe. Nie synchronizujemy zasad haseł z katalogów lokalnych, więc nie możemy publikować powiadomień o wygaśnięciu w środowisku w chmurze. W obu przypadkach możliwe jest również [powiadomienie użytkowników, których hasła wkrótce wygasną za pośrednictwem programu PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P: Czy mogę zablokować użytkownikom możliwość zmiany hasła?**

  > **Odp.:** W przypadku użytkowników korzystających z chmury nie można blokować zmian haseł. W przypadku użytkowników lokalnych można ustawić opcję **Użytkownik nie może zmienić hasła** na zaznaczoną. Wybrani użytkownicy nie mogą zmienić hasła.
  >
  >

## <a name="password-management-reports"></a>Raporty zarządzania hasłami

* **Pyt.: Jak długo trwa wyświetlanie danych w raportach zarządzania hasłami?**

  > **Odp.:** Dane powinny być wyświetlane w raportach zarządzania hasłami w ciągu 5 do 10 minut. W niektórych przypadkach może upłynąć do godziny.
  >
  >
* **Pyt.: Jak filtrować raporty zarządzania hasłami?**

  > **Odp.:** Aby filtrować raporty zarządzania hasłami, wybierz małe szkło powiększające po skrajnej prawej stronie etykiet kolumn, w górnej części raportu. Jeśli chcesz wykonać bogatsze filtrowanie, możesz pobrać raport do programu Excel i utworzyć tabelę przestawną.
  >
  >
* **Pyt.: Jaka jest maksymalna liczba zdarzeń przechowywanych w raportach zarządzania hasłami?**

  > **Odp.:** W raportach zarządzania hasłami przechowywanych jest do 75 000 zdarzeń resetowania hasła lub resetowania hasła, które trwają do 30 dni. Pracujemy nad rozszerzeniem tej liczby o więcej zdarzeń.
  >
  >
* **Pyt.: Jak daleko wstecz raporty zarządzania hasłami?**

  > **Odp.:** Raporty zarządzania hasłami pokazują operacje, które wystąpiły w ciągu ostatnich 30 dni. Na razie, jeśli chcesz zarchiwizować te dane, możesz okresowo pobierać raporty i zapisywać je w osobnej lokalizacji.
  >
  >
* **Pyt.: Czy istnieje maksymalna liczba wierszy, które mogą pojawić się w raportach zarządzania hasłami?**

  > **Odpowiedź:** tak. Maksymalnie 75 000 wierszy może pojawić się w obu raportach zarządzania hasłami, niezależnie od tego, czy są one wyświetlane w interfejsie użytkownika, czy są pobierane.
  >
  >
* **Pyt.: Czy istnieje interfejs API, aby uzyskać dostęp do danych raportowania resetowania hasła lub rejestracji?**

  > **Odpowiedź:** tak. Aby dowiedzieć się, jak uzyskać dostęp do danych raportowania resetowania hasła, zobacz [odwołanie interfejsu API REST usługi Azure Log Analytics](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>Zapisywanie zwrotne haseł

* **Pyt.: Jak zapisy zwrotne hasła działają za kulisami?**

  > **Odp.:** Zobacz artykuł [Jak działa stornowanie hasła,](howto-sspr-writeback.md) aby wyjaśnić, co się dzieje po włączeniu zapisywania zwrotnego hasła i jak dane przepływają przez system z powrotem do środowiska lokalnego.
  >
  >
* **Pyt.: Jak długo trwa zapisywanie zwrotne hasła do pracy? Czy istnieje opóźnienie synchronizacji, jak istnieje z synchronizacją skrótów haseł?**

  > **Odp.:** Zapisywanie zwrotne hasła jest natychmiastowe. Jest to potok synchroniczne, który działa zasadniczo inaczej niż synchronizacja skrótów haseł. Zapisywanie hasła umożliwia użytkownikom uzyskanie opinii w czasie rzeczywistym na temat powodzenia resetowania lub zmiany hasła. Średni czas pomyślnego zapisu hasła wynosi poniżej 500 ms.
  >
  >
* **Pyt.: Jeśli moje konto lokalne jest wyłączone, w jaki sposób wpływa to na moje konto w chmurze i dostęp?**

  > **Odp.:** Jeśli identyfikator lokalny jest wyłączony, identyfikator chmury i dostęp zostaną również wyłączone w następnym interwale synchronizacji za pośrednictwem usługi Azure AD Connect. Domyślnie ta synchronizacja jest co 30 minut.
  >
  >
* **Pyt.: Jeśli moje konto lokalne jest ograniczone przez lokalną zasadę haseł usługi Active Directory, czy samoo wiele osób jest przestrzegane przez te zasady po zmianie hasła?**

  > **Odp.:** Tak, samooprędne i przestrzega lokalnych zasad haseł usługi Active Directory. Ta zasada obejmuje typowe zasady haseł domeny usługi Active Directory, a także wszelkie zdefiniowane, szczegółowe zasady haseł, które są przeznaczone dla użytkownika.
  >
  >
* **Pyt.: Do jakich typów kont działa zapisywanie zwrotne haseł?**

  > **Odp.:** Stornowanie hasła działa dla kont użytkowników, które są synchronizowane z lokalnej usługi Active Directory do usługi Azure AD, w tym federacyjne, haszysz haseł zsynchronizowane i użytkowników autentication przekazywania.
  >
  >
* **P: Czy stornia zwrotnego hasła wymusza zasady haseł mojej domeny?**

  > **Odpowiedź:** tak. Zmiana czasu zapisywania haseł wymusza wiek, historię, złożoność, filtry i wszelkie inne ograniczenia, które można wprowadzić w przypadku haseł w domenie lokalnej.
  >
  >
* **P: Czy zapisywanie zwrotnego hasła jest bezpieczne?  Jak mogę mieć pewność, że nie zhakuję się?**

  > **Odp.:** Tak, zapisywanie zwrotne hasła jest bezpieczne. Aby dowiedzieć się więcej o wielu warstwach zabezpieczeń zaimplementowanych przez usługę zapisywania zwrotnego hasła, zapoznaj się z [sekcją Zabezpieczenia stornia hasła](concept-sspr-writeback.md#password-writeback-security) w artykule [Omówienie przeglądu zapisu hasła.](howto-sspr-writeback.md)
  >
  >

## <a name="next-steps"></a>Następne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmienianie hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś jest zepsute. Jak rozwiązać problem z łatem SSPR?](active-directory-passwords-troubleshoot.md)
