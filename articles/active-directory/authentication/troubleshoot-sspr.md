---
title: Rozwiązywanie problemów z samoobsługowym resetowaniem hasła — Azure Active Directory
description: Dowiedz się, jak rozwiązywać typowe problemy i kroki rozwiązywania problemów z funkcją samoobsługowego resetowania hasła w Azure Active Directory
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 08/26/2020
ms.author: justinha
author: justinha
manager: daveba
ms.reviewer: rhicock
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7099de23b825fa7af203dff9696bb53e23a099c8
ms.sourcegitcommit: ad83be10e9e910fd4853965661c5edc7bb7b1f7c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/06/2020
ms.locfileid: "96741120"
---
# <a name="troubleshoot-self-service-password-reset-in-azure-active-directory"></a>Rozwiązywanie problemów z samoobsługowym resetowaniem haseł w Azure Active Directory

Usługa Azure Active Directory (Azure AD) Samoobsługowe resetowanie haseł (SSPR) umożliwia użytkownikom Resetowanie swoich haseł w chmurze.

Jeśli masz problemy z usługą SSPR, następujące kroki rozwiązywania problemów i typowe błędy mogą pomóc. Jeśli nie możesz znaleźć odpowiedzi na Twój problem, [nasze zespoły pomocy technicznej są zawsze dostępne](#contact-microsoft-support) , aby pomóc Ci w dalszej próbie.

## <a name="sspr-configuration-in-the-azure-portal"></a>Konfiguracja SSPR w Azure Portal

Jeśli masz problemy z wyświetlaniem lub konfiguracją opcji SSPR w Azure Portal, zapoznaj się z następującymi krokami rozwiązywania problemów:

### <a name="i-dont-see-the-password-reset-section-under-azure-ad-in-the-azure-portal"></a>Nie widzę sekcji **resetowania hasła** w usłudze Azure AD w Azure Portal.

Jeśli nie masz licencji usługi Azure AD przypisanej do administratora wykonującego operację, nie zostanie wyświetlona opcja menu **resetowania hasła** .

Aby przypisać licencję do konta administratora, postępuj zgodnie z instrukcjami [dotyczącymi przypisywania, weryfikowania i rozwiązywania problemów z licencjami](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="i-dont-see-a-particular-configuration-option"></a>Nie widzę określonej opcji konfiguracji.

Wiele elementów interfejsu użytkownika jest ukrytych do momentu, gdy są one zbędne. Upewnij się, że opcja jest włączona przed wyszukiwaniem określonych opcji konfiguracji.

### <a name="i-dont-see-the-on-premises-integration-tab"></a>Nie widzę karty **integracja lokalna** .

Lokalne zapisywanie zwrotne haseł jest widoczne tylko wtedy, gdy pobrano Azure AD Connect i skonfigurowano funkcję.

Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD Connect](../hybrid/how-to-connect-install-express.md).

## <a name="sspr-reporting"></a>Raportowanie SSPR

Jeśli masz problemy z raportowaniem SSPR w Azure Portal, zapoznaj się z następującymi krokami rozwiązywania problemów:

### <a name="i-dont-see-any-password-management-activity-types-in-the-self-service-password-management-audit-event-category"></a>Nie widzę żadnych typów działań związanych z zarządzaniem hasłami w kategorii zdarzenia inspekcji samoobsługowego **zarządzania hasłami** .

Taka sytuacja może wystąpić, jeśli nie masz licencji usługi Azure AD przypisanej do administratora wykonującego operację.

Aby przypisać licencję do konta administratora, postępuj zgodnie z instrukcjami [dotyczącymi przypisywania, weryfikowania i rozwiązywania problemów z licencjami](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="user-registrations-show-multiple-times"></a>Rejestracje użytkowników są wyświetlane wiele razy.

Podczas rejestrowania użytkownika rejestrujemy obecnie poszczególne dane, które są rejestrowane jako oddzielne zdarzenie.

Jeśli chcesz agregować te dane i mieć większą elastyczność w sposobie ich wyświetlania, możesz pobrać raport i otworzyć dane jako tabelę przestawną w programie Excel.

## <a name="sspr-registration-portal"></a>Portal rejestracji SSPR

Jeśli użytkownicy mają problemy z rejestrowaniem w usłudze SSPR, zapoznaj się z następującymi krokami rozwiązywania problemów:

### <a name="the-directory-isnt-enabled-for-password-reset-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>W katalogu nie włączono obsługi resetowania hasła. Użytkownik może zobaczyć błąd, który raportuje, "Administrator nie włączył do korzystania z tej funkcji".

Możesz włączyć SSPR dla wszystkich użytkowników, brak użytkowników lub dla wybranych grup użytkowników. Obecnie można włączyć tylko jedną grupę usługi Azure AD dla SSPR przy użyciu Azure Portal. W ramach szerszego wdrożenia SSPR są obsługiwane zagnieżdżone grupy. Upewnij się, że użytkownicy w wybranych grupach mają przypisane odpowiednie licencje.

W Azure Portal Zmień konfigurację z włączoną funkcją samoobsługowego **resetowania hasła** na *wybraną* lub *wszystkie* , a następnie wybierz pozycję **Zapisz**.

### <a name="the-user-doesnt-have-an-azure-ad-license-assigned-the-user-may-see-an-error-that-reports-your-administrator-has-not-enabled-you-to-use-this-feature"></a>Użytkownik nie ma przypisanej licencji usługi Azure AD. Użytkownik może zobaczyć błąd, który raportuje, "Administrator nie włączył do korzystania z tej funkcji".

Obecnie można włączyć tylko jedną grupę usługi Azure AD dla SSPR przy użyciu Azure Portal. W ramach szerszego wdrożenia SSPR są obsługiwane zagnieżdżone grupy. Upewnij się, że użytkownicy w wybranych grupach mają przypisane odpowiednie licencje. Przejrzyj poprzedni krok rozwiązywania problemów, aby włączyć SSPR zgodnie z wymaganiami.

Należy również zapoznać się z krokami rozwiązywania problemów, aby upewnić się, że administrator wykonujący opcje konfiguracji ma przypisaną licencję. Aby przypisać licencję do konta administratora, postępuj zgodnie z instrukcjami [dotyczącymi przypisywania, weryfikowania i rozwiązywania problemów z licencjami](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses).

### <a name="theres-an-error-processing-the-request"></a>Wystąpił błąd podczas przetwarzania żądania.

Błędy rejestracji ogólnej SSPR mogą być spowodowane przez wiele problemów, ale zazwyczaj ten błąd jest spowodowany awarią usługi lub problemem z konfiguracją. Jeśli ten błąd ogólny będzie nadal występować w przypadku ponowienia procesu rejestracji SSPR, [skontaktuj się z pomocą techniczną firmy Microsoft](#contact-microsoft-support) w celu uzyskania dodatkowej pomocy.

## <a name="sspr-usage"></a>SSPR użycie

Jeśli ty lub użytkownicy mają problemy z używaniem SSPR, zapoznaj się z następującymi scenariuszami rozwiązywania problemów i krokami rozwiązania:

| Błąd | Rozwiązanie |
| --- | --- |
| W katalogu nie włączono obsługi resetowania hasła. | W Azure Portal Zmień konfigurację z włączoną funkcją samoobsługowego **resetowania hasła** na *wybraną* lub *wszystkie* , a następnie wybierz pozycję **Zapisz**. |
| Użytkownik nie ma przypisanej licencji usługi Azure AD. | Taka sytuacja może wystąpić, jeśli nie masz licencji usługi Azure AD przypisanej do żądanego użytkownika. Aby przypisać licencję do konta administratora, postępuj zgodnie z instrukcjami [dotyczącymi przypisywania, weryfikowania i rozwiązywania problemów z licencjami](../enterprise-users/licensing-groups-assign.md#step-1-assign-the-required-licenses). |
| Katalog jest włączony do resetowania hasła, ale użytkownik nie ma lub ma nieprawidłowo sformułowane informacje o uwierzytelnianiu. | Upewnij się, że użytkownik ma poprawnie sformułowane dane kontaktowe w pliku w katalogu. Aby uzyskać więcej informacji, zobacz dane używane przez funkcję samoobsługowego [resetowania hasła w usłudze Azure AD](howto-sspr-authenticationdata.md). |
| Katalog jest włączony do resetowania haseł, ale użytkownik ma tylko jedną część danych kontaktowych w pliku, gdy zasady są ustawione tak, aby wymagały dwóch metod weryfikacji. | Upewnij się, że użytkownik ma co najmniej dwie prawidłowo skonfigurowane metody kontaktu. Przykładem jest numer telefonu komórkowego *i* numer telefonu biurowego. |
| Katalog jest włączony do resetowania hasła i użytkownik jest prawidłowo skonfigurowany, ale nie można skontaktować się z użytkownikiem. | Może to być wynikiem tymczasowego błędu usługi lub w przypadku nieprawidłowych danych kontaktowych, których nie można poprawnie wykryć. <br> <br> Jeśli użytkownik odczeka 10 sekund, zostanie wyświetlony link "Spróbuj ponownie" i "Skontaktuj się z administratorem". Jeśli użytkownik wybierze opcję "Spróbuj ponownie", ponawia próbę wywołania. Jeśli użytkownik wybierze opcję "Skontaktuj się z administratorem", wyśle wiadomość e-mail do administratorów żądających zresetowania hasła dla tego konta użytkownika. |
| Użytkownik nigdy nie otrzymuje wiadomości SMS lub połączenia telefonicznego resetowania hasła. | Może to być spowodowane błędnym numerem telefonu w katalogu. Upewnij się, że numer telefonu jest w formacie "+ 1 4251234567". <br> <br>Resetowanie hasła nie obsługuje rozszerzeń, nawet jeśli określisz je w katalogu. Rozszerzenia są usuwane przed wywołaniem. Użyj liczby bez rozszerzenia lub Zintegruj rozszerzenie do numeru telefonu w prywatnej wymianie gałęzi. |
| Użytkownik nigdy nie otrzymuje wiadomości e-mail dotyczącej resetowania hasła. | Najbardziej typową przyczyną tego problemu jest to, że komunikat jest odrzucany przez filtr spamu. Sprawdź wiadomość e-mail z folderem spam, wiadomości-śmieci lub elementy usunięte. <br> <br> Upewnij się również, że użytkownik sprawdzi poprawne konto e-mail zarejestrowane w usłudze SSPR. |
| Zasady resetowania hasła zostały ustawione, ale jeśli konto administratora używa resetowania hasła, te zasady nie są stosowane. | Firma Microsoft zarządza zasadami resetowania hasła administratora i kontroluje je w celu zapewnienia najwyższego poziomu zabezpieczeń. |
| Użytkownik nie próbuje zresetować hasła zbyt wiele razy w ciągu dnia. | Mechanizm automatycznego ograniczania przepustowości służy do blokowania użytkownikom próby resetowania haseł zbyt wiele razy w krótkim czasie. Ograniczanie przepływności odbywa się w następujących scenariuszach: <br><ul><li>Użytkownik próbuje zweryfikować numer telefonu pięć razy w ciągu jednej godziny.</li><li>Użytkownik próbuje użyć bramy pytania zabezpieczające pięć razy w ciągu godziny.</li><li>Użytkownik próbuje zresetować hasło dla tego samego konta użytkownika pięć razy w ciągu jednej godziny.</li></ul>Jeśli użytkownik napotka ten problem, musi odczekać 24 godziny od ostatniej próby. Użytkownik może następnie zresetować swoje hasło. |
| Użytkownik widzi błąd podczas weryfikowania numeru telefonu. | Ten błąd występuje, gdy wprowadzony numer telefonu nie jest zgodny z numerem telefonu w pliku. Upewnij się, że użytkownik wprowadza pełny numer telefonu, w tym kod obszaru i kraju, przy próbie użycia telefonicznej metody resetowania hasła. |
| Wystąpił błąd podczas przetwarzania żądania. | Błędy rejestracji ogólnej SSPR mogą być spowodowane przez wiele problemów, ale zazwyczaj ten błąd jest spowodowany awarią usługi lub problemem z konfiguracją. Jeśli ten błąd ogólny będzie nadal występować w przypadku ponownego wypróbowania procesu rejestracji SSPR, [skontaktuj się z pomocą techniczną firmy Microsoft](#contact-microsoft-support) w celu uzyskania dodatkowej pomocy. |
| Naruszenie zasad lokalnych | Hasło nie jest zgodne z lokalnymi zasadami haseł Active Directory. Użytkownik musi zdefiniować hasło spełniające wymagania dotyczące złożoności lub siły. |
| Hasło nie jest zgodne z zasadami rozmytymi | Używane hasło pojawia się na [liście zakazanych haseł](./concept-password-ban-bad.md#how-are-passwords-evaluated) i nie można jej użyć. Użytkownik musi zdefiniować hasło, które spełnia lub przekracza zasady listy zakazanych haseł. |

## <a name="sspr-errors-that-a-user-might-see"></a>SSPR błędy, które użytkownik może zobaczyć

Użytkownik może wyświetlić następujące błędy i szczegóły techniczne w ramach procesu SSPR. Często błąd nie jest elementem, który można rozwiązać, ponieważ funkcja SSPR musi zostać włączona, skonfigurowana lub zarejestrowana dla swojego konta.

Skorzystaj z poniższych informacji, aby zrozumieć problem i co należy poprawić w ramach dzierżawy usługi Azure AD lub indywidualnego konta użytkownika.

| Błąd | Szczegóły | Szczegóły techniczne |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Niestety, nie można teraz zresetować hasła, ponieważ administrator wyłączył Resetowanie hasła dla organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o włączenie tej funkcji.<br /><br />Aby dowiedzieć się więcej, zobacz [Pomoc I nie pamiętam hasła usługi Azure AD](../user-help/active-directory-passwords-update-your-own-password.md#common-problems-and-their-solutions). | SSPR_0009: wykryliśmy, że Resetowanie hasła nie zostało włączone przez administratora. Skontaktuj się z administratorem i poproś o włączenie resetowania hasła dla organizacji. |
| WritebackNotEnabled = 10 |Niestety, nie można teraz zresetować hasła, ponieważ administrator nie włączył niezbędnej usługi dla Twojej organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o sprawdzenie konfiguracji swojej organizacji.<br /><br />Aby dowiedzieć się więcej na temat tej niezbędnej usługi, zobacz [Konfigurowanie zapisywania zwrotnego haseł](./tutorial-enable-sspr-writeback.md). | SSPR_0010: wykryto, że funkcja zapisywania zwrotnego haseł nie została włączona. Skontaktuj się z administratorem i poproś o włączenie funkcji zapisywania zwrotnego haseł. |
| SsprNotEnabledInUserPolicy = 11 | Niestety, nie można teraz zresetować hasła, ponieważ administrator nie skonfigurował resetowania hasła dla Twojej organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o skonfigurowanie resetowania hasła.<br /><br />Aby dowiedzieć się więcej na temat konfiguracji resetowania haseł, zobacz Szybki Start: funkcja samoobsługowego [resetowania hasła w usłudze Azure AD](./tutorial-enable-sspr.md). | SSPR_0011: Twoja organizacja nie określiła zasad resetowania hasła. Skontaktuj się z administratorem i poproś o zdefiniowanie zasad resetowania hasła. |
| UserNotLicensed = 12 | Niestety, nie można teraz zresetować hasła, ponieważ w organizacji brakuje wymaganych licencji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o sprawdzenie przypisania licencji.<br /><br />Aby dowiedzieć się więcej o licencjonowaniu, zobacz [wymagania dotyczące licencjonowania dla funkcji samoobsługowego resetowania hasła w usłudze Azure AD](./concept-sspr-licensing.md). | SSPR_0012: organizacja nie ma wymaganych licencji niezbędnych do przeprowadzenia resetowania hasła. Skontaktuj się z administratorem i poproś o zapoznanie się z przypisaniami licencji. |
| UserNotMemberOfScopedAccessGroup = 13 | Niestety, nie można teraz zresetować hasła, ponieważ administrator nie skonfigurował konta do używania resetowania hasła. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o skonfigurowanie konta do resetowania hasła.<br /><br />Aby dowiedzieć się więcej o konfigurowaniu kont na potrzeby resetowania haseł, zobacz artykuł [wdrażanie hasła do resetowania haseł dla użytkowników](./howto-sspr-deployment.md). | SSPR_0013: nie jesteś członkiem grupy z włączoną obsługą resetowania hasła. Skontaktuj się z administratorem i poproś o dodanie do grupy. |
| UserNotProperlyConfigured = 14 | Niestety, nie można teraz zresetować hasła, ponieważ brakuje w nim wymaganych informacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o zresetowanie hasła. Po ponownym uzyskaniu dostępu do konta musisz zarejestrować niezbędne informacje.<br /><br />Aby zarejestrować informacje, wykonaj kroki opisane w artykule Rejestracja artykułu samoobsługowego [resetowania hasła](../user-help/active-directory-passwords-reset-register.md) . | SSPR_0014: dodatkowe informacje zabezpieczające są konieczne do zresetowania hasła. Aby to zrobić, skontaktuj się z administratorem i poproś o zresetowanie hasła. Po uzyskaniu dostępu do konta możesz zarejestrować dodatkowe informacje zabezpieczające pod adresem https://aka.ms/ssprsetup . Administrator może dodać do swojego konta dodatkowe informacje zabezpieczające, wykonując czynności opisane w temacie [Ustawianie i odczytywanie danych uwierzytelniania na potrzeby resetowania haseł](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Niestety, nie można teraz zresetować hasła z powodu problemu z konfiguracją resetowania hasła w organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o zbadanie. <br /><br />Lub<br /><br />W tej chwili nie możemy zresetować hasła z powodu problemu z konfiguracją resetowania hasła w organizacji. Nie ma dalszych działań, które można wykonać, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o zbadanie.<br /><br />Aby dowiedzieć się więcej o potencjalnym problemie, zobacz [Rozwiązywanie problemów z funkcją zapisywania zwrotnego haseł](troubleshoot-sspr-writeback.md). | SSPR_0029: nie możemy zresetować hasła z powodu błędu w konfiguracji lokalnej. Skontaktuj się z administratorem i poproś o zbadanie. |
| OnPremisesConnectivityError = 30 | Niestety, w tej chwili nie możemy zresetować hasła z powodu problemów z łącznością z Twoją organizacją. Nie ma żadnych akcji, które należy wykonać, ale problem może zostać rozwiązany, jeśli spróbujesz ponownie później. Jeśli problem będzie się powtarzać, skontaktuj się z administratorem i poproś o zbadanie.<br /><br />Aby dowiedzieć się więcej o problemach z łącznością, zobacz [Rozwiązywanie problemów z funkcją zapisywania zwrotnego haseł](troubleshoot-sspr-writeback.md) | SSPR_0030: nie można zresetować hasła z powodu słabego połączenia ze środowiskiem lokalnym. Skontaktuj się z administratorem i poproś o zbadanie.|

## <a name="azure-ad-forums"></a>Fora usługi Azure AD

Jeśli masz ogólne pytania dotyczące usługi Azure AD i samoobsługowego resetowania hasła, możesz poproś społeczność o pomoc na stronie pytań i odpowiedzi na [pytania firmy&Microsoft dotyczące Azure Active Directory](/answers/topics/azure-active-directory.html). Członkowie społeczności obejmują inżynierów, menedżerów produktów, MVP i innych specjalistów IT.

## <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Jeśli nie możesz znaleźć odpowiedzi na problem, nasze zespoły pomocy technicznej są zawsze dostępne, aby pomóc Ci w dalszej próbie.

Aby prawidłowo pomóc, prosimy o podanie możliwie największej szczegółowości podczas otwierania sprawy. Te szczegóły obejmują następujące elementy:

* **Ogólny opis błędu**: jaki jest błąd? Jak zauważono zachowanie? Jak odtworzyć błąd? Podaj tyle szczegółów, ile to możliwe.
* **Strona**: jaka strona została zadana, gdy zauważysz błąd? Dołącz adres URL, jeśli możesz i zrzut ekranu strony.
* **Kod pomocy technicznej**: jaki był kod pomocy technicznej wygenerowany podczas napotkania błędu przez użytkownika?
   * Aby znaleźć ten kod, Odtwórz błąd, a następnie wybierz link **kod pomocy technicznej** u dołu ekranu i Wyślij do niego identyfikator GUID.

    :::image type="content" source="./media/troubleshoot-sspr-writeback/view-support-code.png" alt-text="Kod pomocy technicznej znajduje się w prawym dolnym rogu okna przeglądarki sieci Web.":::

  * Jeśli jesteś na stronie bez kodu pomocy technicznej u dołu, wybierz klawisz F12 i wyszukaj identyfikatory SID i CID i Wyślij te dwa wyniki do inżyniera pomocy technicznej.
* **Data, godzina i strefa czasowa**: Uwzględnij dokładną datę i godzinę *w strefie czasowej* wystąpienia błędu.
* **Identyfikator użytkownika**: użytkownik, który wystąpił błąd? Przykładem jest *user \@ contoso.com*.
   * Czy ten użytkownik jest federacyjny?
   * Czy to jest użytkownik uwierzytelniania Pass-through?
   * Czy to jest użytkownik z synchronizacją skrótu hasła?
   * Czy jest to użytkownik tylko w chmurze?
* **Licencjonowanie**: czy użytkownik ma przypisaną licencję usługi Azure AD?
* **Dziennik zdarzeń aplikacji**: Jeśli używasz funkcji zapisywania zwrotnego haseł, a błąd znajduje się w infrastrukturze lokalnej, Dołącz spakowaną kopię dziennika zdarzeń aplikacji z serwera Azure AD Connect.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat SSPR, zobacz [jak to działa: Samoobsługowe resetowanie haseł w usłudze Azure AD](concept-sspr-howitworks.md) lub [sposób działania funkcji zapisywania zwrotnego resetowania hasła w usłudze Azure AD?](concept-sspr-writeback.md).
