---
title: Często zadawane pytania dotyczące samoobsługowego resetowania hasła — Azure Active Directory
description: Często zadawane pytania dotyczące samoobsługowego resetowania hasła w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 07/20/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0874f6aeb74b028e92e10d972757cc5971735e39
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96744316"
---
# <a name="self-service-password-reset-frequently-asked-questions"></a>Często zadawane pytania dotyczące samoobsługowego resetowania hasła

Poniżej przedstawiono niektóre często zadawane pytania dotyczące wszystkich zagadnień związanych z samoobsługowym resetowaniem haseł.

Jeśli masz ogólne pytanie dotyczące Azure Active Directory (Azure AD) i samoobsługowego resetowania hasła (SSPR), które nie są tutaj dostępne, możesz poproś społeczność o pomoc na stronie pytań i odpowiedzi [Microsoft&pytań](/answers/topics/azure-active-directory.html)i odpowiedzi na Azure Active Directory. Członkowie społeczności obejmują inżynierów, menedżerów produktów, MVP i innych specjalistów IT.

Często zadawane pytania są podzielone na następujące sekcje:

* [Pytania dotyczące rejestracji resetowania haseł](#password-reset-registration)
* [Pytania dotyczące resetowania haseł](#password-reset)
* [Pytania dotyczące zmiany hasła](#password-change)
* [Pytania dotyczące raportów zarządzania hasłami](#password-management-reports)
* [Pytania dotyczące funkcji zapisywania zwrotnego haseł](#password-writeback)

## <a name="password-reset-registration"></a>Rejestracja w funkcji resetowania haseł

* **P: Czy moi użytkownicy rejestrują swoje własne dane resetowania hasła?**

  > **Odpowiedź:** tak. Dopóki funkcja resetowania hasła jest włączona i jest licencjonowana, użytkownicy mogą przejść do portalu rejestracji resetowania haseł ( https://aka.ms/ssprsetup) Aby zarejestrować swoje informacje uwierzytelniania. Użytkownicy mogą również rejestrować się za pomocą panelu dostępu ( https://myapps.microsoft.com) . Aby zarejestrować się w panelu dostępu, należy wybrać swój obraz profilu, wybrać pozycję **profil**, a następnie wybrać opcję **zarejestruj do resetowania hasła** .
  >
  > Jeśli włączysz [rejestrację łączną](concept-registration-mfa-sspr-combined.md), użytkownicy mogą rejestrować się jednocześnie zarówno w SSPR, jak i w usłudze Azure Multi-Factor Authentication AD.
* **P: Jeśli włączam funkcję resetowania haseł dla grupy, a następnie zdecyduje się ją włączyć dla wszystkich użytkowników, wymagane jest ponowne zarejestrowanie?**

  > Odp **.:** Znaleziono. Użytkownicy, którzy wypełnili dane uwierzytelniania, nie muszą być ponownie rejestrowani.
  >
  >
* **P: Czy można definiować dane resetowania haseł w imieniu moich użytkowników?**

  > Odp **.:** Tak, można to zrobić za pomocą Azure AD Connect, programu PowerShell, [Azure Portal](https://portal.azure.com)lub [Centrum administracyjnego Microsoft 365](https://admin.microsoft.com). Aby uzyskać więcej informacji, zobacz dane używane przez funkcję samoobsługowego [resetowania hasła w usłudze Azure AD](howto-sspr-authenticationdata.md).
  >
  >
* **P: Czy można synchronizować dane z pytaniami zabezpieczeń z lokalnego?**

  > Odp **.:** Nie. nie jest to obecnie możliwe.
  >
  >
* **P: Czy moi użytkownicy mogą rejestrować dane w taki sposób, że inni użytkownicy nie będą widzieć tych danych?**

  > **Odpowiedź:** tak. Gdy użytkownicy rejestrują dane przy użyciu portalu rejestracji resetowania haseł, dane są zapisywane w prywatnych polach uwierzytelniania, które są widoczne tylko dla administratorów globalnych i użytkownika.
  >
  >
* **P: Czy wszyscy użytkownicy muszą zostać zarejestrowani, zanim będą mogli używać resetowania haseł?**

  > Odp **.:** Znaleziono. Jeśli zdefiniujesz wystarczającą ilość informacji o uwierzytelnianiu w ich imieniu, użytkownicy nie będą musieli rejestrować. Resetowanie hasła działa tak długo, jak prawidłowo sformatowane dane przechowywane w odpowiednich polach w katalogu.
  >
  >
* **P: Czy można synchronizować lub ustawiać pola numer telefonu uwierzytelniania, adres e-mail uwierzytelniania lub alternatywny numer telefonu uwierzytelniania w imieniu użytkowników?**

  > Odp **.:** Pola, które mogą być ustawiane przez administratora globalnego, są zdefiniowane w artykule [SSPR wymagania dotyczące danych](howto-sspr-authenticationdata.md).
  >
  >
* **P: w jaki sposób Portal rejestracji określa, które opcje mają być widoczne dla użytkowników?**

  > Odp **.:** W portalu rejestracji resetowania haseł są wyświetlane tylko opcje, które zostały włączone dla użytkowników. Te opcje są dostępne w sekcji **Zasady resetowania hasła użytkownika** na karcie **Konfiguracja** Twojego katalogu. Jeśli na przykład nie włączysz pytań zabezpieczających, użytkownicy nie będą mogli zarejestrować się w tej opcji.
  >
  >
* **P: Kiedy użytkownik jest uważany za zarejestrowany?**

  > Odp **.:** Użytkownik jest uznawany za zarejestrowany dla SSPR, gdy zarejestrowali co najmniej **liczbę metod wymaganych do zresetowania** hasła, które zostało ustawione w [Azure Portal](https://portal.azure.com).
  >
  >

## <a name="password-reset"></a>Resetowanie hasła

* **P: czy uniemożliwiasz użytkownikom wielokrotne podejmowanie prób resetowania hasła w krótkim czasie?**

  > Odp **.:** Tak, istnieją funkcje zabezpieczeń wbudowane w funkcję resetowania hasła w celu ochrony przed nieprawidłowym użyciem. 
  >
  > Użytkownicy mogą wypróbować tylko pięć prób resetowania hasła w okresie 24 godzin, zanim zostaną one zablokowane przez 24 godziny. 
  >
  > Użytkownicy mogą próbować sprawdzić numer telefonu, wysłać wiadomość SMS lub zweryfikować pytania zabezpieczające i odpowiedzi tylko pięć razy w ciągu godziny przed zablokowaniem ich przez 24 godziny. 
  >
  > Użytkownicy mogą wysyłać wiadomości e-mail maksymalnie 10 razy w ciągu 10 minut, zanim zostaną zablokowane przez 24 godziny.
  >
  > Liczniki są resetowane po zresetowaniu hasła przez użytkownika.
  >
  >
* **P: jak długo należy czekać na odebranie wiadomości e-mail, wiadomości SMS lub połączenia telefonicznego od resetowania hasła?**

  > Odp **.:** Adresy e-mail, wiadomości SMS i rozmowy telefoniczne powinny się pojawić w ciągu minuty. Normalna wielkość wynosi od 5 do 20 sekund.
  > Jeśli powiadomienie nie zostanie odebrane w tym przedziale czasowym:
  > * Sprawdź folder wiadomości-śmieci.
  > * Sprawdź, czy numer lub adres e-mail, z którym nawiązano kontakt, jest oczekiwany.
  > * Sprawdź, czy dane uwierzytelniania w katalogu są poprawnie sformatowane, na przykład + 1 4255551234 lub *user \@ contoso.com*. 
* **P: jakie języki są obsługiwane przez zresetowanie hasła?**

  > Odp **.:** Interfejs użytkownika służący do resetowania haseł, wiadomości SMS i połączenia głosowe są zlokalizowane w tych samych językach, które są obsługiwane w Microsoft 365.
  >
  >
* **P: jakie części środowiska resetowania hasła są znakowane, gdy ustawiam pozycje znakowania organizacyjnego na karcie Konfiguracja mojego katalogu?**

  > Odp **.:** Portal resetowania haseł pokazuje logo organizacji i umożliwia skonfigurowanie linku "Skontaktuj się z administratorem", aby wskazać niestandardową wiadomość e-mail lub adres URL. Wszystkie wiadomości e-mail wysyłane przez Resetowanie hasła obejmują logo, kolory i nazwę organizacji w treści wiadomości e-mail i są dostosowane do ustawień tej konkretnej nazwy.
  >
  >
* **P: Jak mogę poinformować użytkowników o tym, gdzie przejdź, aby zresetować swoje hasła?**

  > Odp **.:** Wypróbuj niektóre z sugestii w naszym artykule dotyczącym [wdrażania SSPR](howto-sspr-deployment.md#plan-communications) .
  >
  >
* **P: Czy można użyć tej strony na urządzeniu przenośnym?**

  > Odp **.:** Tak, ta strona działa na urządzeniach przenośnych.
  >
  >
* **P: Czy chcesz obsługiwać odblokowywanie lokalnych kont Active Directory, gdy użytkownicy zresetują swoje hasła?**

  > **Odpowiedź:** tak. Gdy użytkownik resetuje swoje hasło, jeśli funkcję zapisywania zwrotnego haseł została wdrożona za pomocą Azure AD Connect, konto tego użytkownika zostanie automatycznie odblokowane po zresetowaniu hasła.
  >
  >
* **P: Jak mogę zintegrować resetowanie haseł bezpośrednio z interfejsem logowania do pulpitu użytkownika?**

  > Odp **.:** Jeśli jesteś klientem Azure AD — wersja Premium, możesz zainstalować Microsoft Identity Manager bez dodatkowych kosztów i wdrożyć lokalne rozwiązanie do resetowania haseł.
  >
  >
* **P: Czy można ustawić różne pytania zabezpieczające dla różnych ustawień regionalnych?**

  > Odp **.:** Nie. nie jest to obecnie możliwe.
  >
  >
* **P: Ile pytań można skonfigurować dla opcji uwierzytelniania pytań zabezpieczających?**

  > Odp **.:** W [Azure Portal](https://portal.azure.com)można skonfigurować maksymalnie 20 niestandardowych pytań zabezpieczających.
  >
  >
* **P: jak długo mogą być pytania zabezpieczające?**

  > Odp **.:** Pytania zabezpieczające mogą składać się z od 3 do 200 znaków.
  >
  >
* **P: jak długo można udzielić odpowiedzi na pytania zabezpieczające?**

  > Odp **.:** Odpowiedzi mogą składać się z od 3 do 40 znaków.
  >
  >
* **P: czy odpowiedzi na pytania zabezpieczające są zduplikowane?**

  > Odp **.:** Tak, odrzucamy zduplikowane odpowiedzi na pytania zabezpieczające.
  >
  >
* **P: czy użytkownik może zarejestrować to samo pytanie zabezpieczeń więcej niż raz?**

  > Odp **.:** Znaleziono. Po zarejestrowaniu określonego pytania przez użytkownika nie można zarejestrować tego pytania w drugim momencie.
  >
  >
* **P: Czy można ustawić minimalny limit pytań zabezpieczających na potrzeby rejestracji i resetowania?**

  > Odp **.:** Tak, można ustawić jeden limit na potrzeby rejestracji i drugi do resetowania. Do rejestracji można wymagać od trzech do pięciu pytań zabezpieczających, a do resetowania można wymagać od trzech do pięciu pytań.
  >
  >
* **P: skonfigurowano zasady, aby wymagać od użytkowników używania pytań zabezpieczających do resetowania, ale Administratorzy platformy Azure pozornie konfigurują się inaczej.**

  > Odp **.:** Jest to oczekiwane zachowanie. Firma Microsoft wymusza silną domyślną zasadę resetowania hasła z użyciem dwóch bram dla dowolnej roli administratora platformy Azure. Pozwala to administratorom na korzystanie z pytań zabezpieczających. Więcej informacji na temat tych zasad można znaleźć w temacie [zasady haseł i ograniczenia w artykule Azure Active Directory](concept-sspr-policy.md) .
  >
  >
* **P: Jeśli użytkownik zarejestrował ponad maksymalną liczbę pytań wymaganych do zresetowania, w jaki sposób są wybrane pytania zabezpieczające podczas resetowania?**

  > Odp **.:** *n* liczba pytań zabezpieczających jest wybierana losowo z łącznej liczby pytań, na które użytkownik zarejestrował, gdzie *N* to kwota ustawiona dla **liczby pytań wymaganych do zresetowania** opcji. Na przykład jeśli użytkownik zarejestrował pięć pytań zabezpieczających, ale tylko trzy są wymagane do zresetowania hasła, trzy z pięciu pytań są losowo wybierane i są wyświetlane podczas resetowania. Aby zapobiec utracie pytania, jeśli użytkownik otrzyma odpowiedzi na pytania niewłaściwe, proces wyboru zostanie uruchomiony.
  >
  >
* **P: jak długo są ważne nieprawidłowe kody dostępu wiadomości e-mail i SMS?**

  > Odp **.:** Okres istnienia sesji dla resetowania hasła wynosi 15 minut. Od rozpoczęcia operacji resetowania hasła użytkownik ma 15 minut na zresetowanie hasła. Jednorazowy kod dostępu wiadomości e-mail i SMS jest ważny przez 5 minut podczas sesji resetowania hasła.
  >
  >
* **P: Czy mogę zablokować użytkownikom możliwość resetowania swojego hasła?**

  > Odp **.:** Tak. w przypadku używania grupy do włączania SSPR można usunąć pojedynczego użytkownika z grupy, który umożliwia użytkownikom Resetowanie hasła. Jeśli użytkownik jest administratorem globalnym, będzie mieć możliwość resetowania hasła i nie można go wyłączyć.
  >
  >

## <a name="password-change"></a>Zmiana hasła

* **P: gdzie wszyscy użytkownicy mogą zmieniać swoje hasła?**

  > Odp **.:** Użytkownicy mogą zmieniać swoje hasła w dowolnym miejscu, w którym widzisz swój obraz lub ikonę profilu, na przykład w prawym górnym rogu portalu [Office 365](https://portal.office.com) lub [panelu dostępu](https://myapps.microsoft.com) . Użytkownicy mogą zmieniać hasła na [stronie profilu panelu dostępu](https://account.activedirectory.windowsazure.com/r#/profile). Użytkownicy mogą również poprosić o zmianę haseł automatycznie na stronie logowania usługi Azure AD, jeśli hasło wygasło. Na koniec użytkownicy mogą przejść do [portalu zmiany hasła usługi Azure AD](https://account.activedirectory.windowsazure.com/ChangePassword.aspx) bezpośrednio, jeśli chcą zmienić swoje hasła.
  >
  >
* **P: Czy wszyscy użytkownicy będą powiadamiani w portalu Office po wygaśnięciu hasła lokalnego?**

  > Odp **.:** Tak. obecnie jest to możliwe, jeśli używasz Active Directory Federation Services (AD FS). Jeśli używasz AD FS, postępuj zgodnie z instrukcjami zawartymi w artykule [wysyłanie oświadczeń dotyczących zasad haseł przy użyciu AD FS](/windows-server/identity/ad-fs/operations/configure-ad-fs-to-send-password-expiry-claims?f=255&MSPPError=-2147217396) artykułu. Jeśli używasz synchronizacji skrótów haseł, nie jest to obecnie możliwe. Zasady haseł nie są synchronizowane z katalogów lokalnych, dlatego nie jest możliwe wysyłanie powiadomień o wygaśnięciu do środowiska chmury. W obu przypadkach możliwe jest również [powiadomienie użytkowników, których hasła wkrótce wygasną za pomocą programu PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/23313.notify-active-directory-users-about-password-expiry-using-powershell.aspx).
  >
  >
* **P: Czy mogę zablokować użytkownikom możliwość zmiany hasła?**

  > Odp **.:** W przypadku użytkowników tylko w chmurze nie można zablokować zmian haseł. Dla użytkowników lokalnych można ustawić opcję **nie można zmienić hasła** na wybraną. Wybranym użytkownikom nie można zmienić hasła.
  >
  >

## <a name="password-management-reports"></a>Raporty zarządzania hasłami

* **P: jak długo trwa wyświetlanie danych w raportach zarządzania hasłami?**

  > Odp **.:** Dane powinny być wyświetlane w raportach zarządzania hasłami w ciągu 5 – 10 minut. W niektórych przypadkach może upłynąć do godziny.
  >
  >
* **P: jak można filtrować raporty zarządzania hasłami?**

  > Odp **.:** Aby odfiltrować raporty zarządzania hasłami, wybierz małe szkło powiększające z prawej strony etykiet kolumn w górnej części raportu. Jeśli chcesz przeprowadzić bardziej zaawansowane filtrowanie, możesz pobrać raport do programu Excel i utworzyć tabelę przestawną.
  >
  >
* **P: Jaka jest maksymalna liczba zdarzeń przechowywanych w raportach zarządzania hasłami?**

  > Odp **.:** Do 75 000 zdarzeń rejestracji lub resetowania haseł są przechowywane w raportach zarządzania hasłami, z powrotem przez 30 dni. Pracujemy nad rozwinięciem tej liczby, aby uwzględnić więcej zdarzeń.
  >
  >
* **P: jak daleko wstecz są raporty dotyczące zarządzania hasłami?**

  > Odp **.:** Raporty zarządzania hasłami pokazują operacje, które wystąpiły w ciągu ostatnich 30 dni. Teraz, jeśli trzeba zarchiwizować te dane, możesz pobrać raporty okresowo i zapisać je w oddzielnej lokalizacji.
  >
  >
* **P: czy istnieje maksymalna liczba wierszy, które mogą być wyświetlane w raportach zarządzania hasłami?**

  > **Odpowiedź:** tak. W każdym z raportów zarządzania hasłami może znajdować się maksymalnie 75 000 wierszy, niezależnie od tego, czy są one wyświetlane w interfejsie użytkownika, czy pobierane.
  >
  >
* **P: czy istnieje interfejs API do uzyskiwania dostępu do danych raportowania lub resetowania haseł?**

  > **Odpowiedź:** tak. Aby dowiedzieć się, jak uzyskać dostęp do danych raportowania resetowania haseł, zobacz [Informacje o interfejsie API REST usługi Azure log Analytics](/rest/api/loganalytics/).
  >
  >

## <a name="password-writeback"></a>Zapisywanie zwrotne haseł

* **P: w jaki sposób zapisywanie zwrotne haseł działa w tle?**

  > Odp **.:** Zapoznaj się z artykułem [jak działa funkcja zapisywania zwrotnego haseł](./tutorial-enable-sspr-writeback.md) , aby dowiedzieć się, co się dzieje po włączeniu funkcji zapisywania zwrotnego haseł oraz sposobie powrotu danych przez system do środowiska lokalnego.
  >
  >
* **P: jak długo trwa zapisywanie funkcji zapisywania zwrotnego haseł? Czy istnieje opóźnienie synchronizacji takie jak w przypadku synchronizacji skrótów haseł?**

  > Odp **.:** Zapisywanie zwrotne haseł jest błyskawiczne. Jest to potok synchroniczny, który działa zasadniczo inaczej niż synchronizacja skrótów haseł. Zapisywanie zwrotne haseł umożliwia użytkownikom uzyskiwanie informacji o powodzeniu operacji resetowania lub zmiany hasła. Średni czas pomyślnego stornowania hasła jest równy 500 ms.
  >
  >
* **P: jeśli moje konto lokalne jest wyłączone, w jaki sposób ma to wpływ na konto w chmurze i dostęp?**

  > Odp **.:** Jeśli Twój identyfikator lokalny jest wyłączony, identyfikator chmury i dostęp zostaną również wyłączone przy następnym interwale synchronizacji za pomocą Azure AD Connect. Domyślnie ta synchronizacja jest co 30 minut.
  >
  >
* **P: Jeśli konto lokalne jest ograniczone przez lokalne zasady haseł Active Directory, czy SSPR przestrzegać tych zasad w przypadku zmiany hasła?**

  > Odp **.:** Tak, SSPR opiera się na i przestrzega zasad przez lokalne zasady haseł Active Directory. Zasady te obejmują typowe zasady haseł domen Active Directory, a także wszystkie zdefiniowane, szczegółowe zasady haseł, które są przeznaczone dla użytkownika.
  >
  >
* **P: jakie typy kont działają dla zapisywania zwrotnego haseł?**

  > Odp **.:** Zapisywanie zwrotne haseł działa w przypadku kont użytkowników synchronizowanych z Active Directory lokalnych do usługi Azure AD, w tym federacyjnych, synchronizowanych skrótów haseł i użytkowników uwierzytelniania Pass-Through.
  >
  >
* **P: czy zapisywanie zwrotne haseł wymusza zasady haseł mojej domeny?**

  > **Odpowiedź:** tak. Zapisywanie zwrotne haseł wymusza okres ważności hasła, historię, złożoność, filtry i wszelkie inne ograniczenia, które mogą być stosowane do haseł w domenie lokalnej.
  >
  >
* **P: czy zapisywanie zwrotne haseł jest zabezpieczone?  Jak można się upewnić, że nie mogę pobrać zaatakowana?**

  > Odp **.:** Tak, zapisywanie zwrotne haseł jest bezpieczne. Aby dowiedzieć się więcej o wielu warstwach zabezpieczeń zaimplementowanych przez usługę zapisywania zwrotnego haseł, zapoznaj się z sekcją [zabezpieczenia zapisywania zwrotnego haseł](concept-sspr-writeback.md#password-writeback-security) w artykule [Omówienie zapisywania zwrotnego haseł](./tutorial-enable-sspr-writeback.md) .
  >
  >

## <a name="next-steps"></a>Następne kroki

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmiana hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](./tutorial-enable-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Myślę, że coś jest zerwane. Jak mogę Rozwiązywanie problemów z SSPR?](./troubleshoot-sspr.md)