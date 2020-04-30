---
title: Rozwiązywanie problemów z samoobsługowym resetowaniem hasła — Azure Active Directory
description: Rozwiązywanie problemów z samoobsługowym resetowania haseł w usłudze Azure AD
services: active-directory
ms.service: active-directory
ms.subservice: authentication
ms.topic: troubleshooting
ms.date: 11/21/2019
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: sahenry
ms.custom: seo-update-azuread-jan
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1224aef044c558147f4c7234f6514d0de2f09e0c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "82143993"
---
# <a name="troubleshoot-self-service-password-reset"></a>Troubleshoot self-service password reset (Rozwiązywanie problemów z samoobsługowym resetowaniem hasła)

Czy masz problem z usługą Azure Active Directory (Azure AD) samoobsługowego resetowania haseł (SSPR)? Poniższe informacje mogą pomóc w ponownym uruchomieniu.

## <a name="troubleshoot-self-service-password-reset-errors-that-a-user-might-see"></a>Rozwiązywanie problemów z samoobsługowym resetowaniem haseł, które użytkownik może zobaczyć

| Error | Szczegóły | Szczegóły techniczne |
| --- | --- | --- |
| TenantSSPRFlagDisabled = 9 | Niestety, nie można teraz zresetować hasła, ponieważ administrator wyłączył Resetowanie hasła dla organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o włączenie tej funkcji. Aby dowiedzieć się więcej, zobacz [Pomoc I nie pamiętam hasła usługi Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-update-your-own-password#common-problems-and-their-solutions). | SSPR_0009: wykryliśmy, że Resetowanie hasła nie zostało włączone przez administratora. Skontaktuj się z administratorem i poproś o włączenie resetowania hasła dla organizacji. |
| WritebackNotEnabled = 10 |Niestety, nie można teraz zresetować hasła, ponieważ administrator nie włączył niezbędnej usługi dla Twojej organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o sprawdzenie konfiguracji swojej organizacji. Aby dowiedzieć się więcej na temat tej niezbędnej usługi, zobacz [Konfigurowanie zapisywania zwrotnego haseł](howto-sspr-writeback.md). | SSPR_0010: wykryto, że funkcja zapisywania zwrotnego haseł nie została włączona. Skontaktuj się z administratorem i poproś o włączenie funkcji zapisywania zwrotnego haseł. |
| SsprNotEnabledInUserPolicy = 11 | Niestety, nie można teraz zresetować hasła, ponieważ administrator nie skonfigurował resetowania hasła dla Twojej organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o skonfigurowanie resetowania hasła. Aby dowiedzieć się więcej na temat konfiguracji resetowania haseł, zobacz Szybki Start: funkcja samoobsługowego [resetowania hasła w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-getting-started). | SSPR_0011: Twoja organizacja nie określiła zasad resetowania hasła. Skontaktuj się z administratorem i poproś o zdefiniowanie zasad resetowania hasła. |
| UserNotLicensed = 12 | Niestety, nie można teraz zresetować hasła, ponieważ w organizacji brakuje wymaganych licencji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o sprawdzenie przypisania licencji. Aby dowiedzieć się więcej o licencjonowaniu, zobacz [wymagania dotyczące licencjonowania dla funkcji samoobsługowego resetowania hasła w usłudze Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-licensing). | SSPR_0012: organizacja nie ma wymaganych licencji niezbędnych do przeprowadzenia resetowania hasła. Skontaktuj się z administratorem i poproś o zapoznanie się z przypisaniami licencji. |
| UserNotMemberOfScopedAccessGroup = 13 | Niestety, nie można teraz zresetować hasła, ponieważ administrator nie skonfigurował konta do używania resetowania hasła. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o skonfigurowanie konta do resetowania hasła. Aby dowiedzieć się więcej o konfigurowaniu kont na potrzeby resetowania haseł, zobacz artykuł [wdrażanie hasła do resetowania haseł dla użytkowników](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-best-practices). | SSPR_0013: nie jesteś członkiem grupy z włączoną obsługą resetowania hasła. Skontaktuj się z administratorem i poproś o dodanie do grupy. |
| UserNotProperlyConfigured = 14 | Niestety, nie można teraz zresetować hasła, ponieważ brakuje w nim wymaganych informacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o zresetowanie hasła. Po ponownym uzyskaniu dostępu do konta musisz zarejestrować niezbędne informacje. Aby zarejestrować informacje, wykonaj kroki opisane w artykule Rejestracja artykułu samoobsługowego [resetowania hasła](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-reset-register) . | SSPR_0014: dodatkowe informacje zabezpieczające są konieczne do zresetowania hasła. Aby to zrobić, skontaktuj się z administratorem i poproś o zresetowanie hasła. Po uzyskaniu dostępu do konta możesz zarejestrować dodatkowe informacje zabezpieczające pod adresem https://aka.ms/ssprsetup. Administrator może dodać do swojego konta dodatkowe informacje zabezpieczające, wykonując czynności opisane w temacie [Ustawianie i odczytywanie danych uwierzytelniania na potrzeby resetowania haseł](howto-sspr-authenticationdata.md). |
| OnPremisesAdminActionRequired = 29 | Niestety, nie można teraz zresetować hasła z powodu problemu z konfiguracją resetowania hasła w organizacji. Nie ma dalszych działań, które można podjąć, aby rozwiązać ten problem. Skontaktuj się z administratorem i poproś o zbadanie. Aby dowiedzieć się więcej o potencjalnym problemie, zobacz [Rozwiązywanie problemów z funkcją zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback). | SSPR_0029: nie możemy zresetować hasła z powodu błędu w konfiguracji lokalnej. Skontaktuj się z administratorem i poproś o zbadanie. |
| OnPremisesConnectivityError = 30 | Niestety, w tej chwili nie możemy zresetować hasła z powodu problemów z łącznością z Twoją organizacją. Nie ma żadnych akcji, które należy wykonać, ale problem może zostać rozwiązany, jeśli spróbujesz ponownie później. Jeśli problem będzie się powtarzać, skontaktuj się z administratorem i poproś o zbadanie. Aby dowiedzieć się więcej o problemach z łącznością, zobacz [Rozwiązywanie problemów z funkcją zapisywania zwrotnego haseł](https://docs.microsoft.com/azure/active-directory/active-directory-passwords-troubleshoot#troubleshoot-password-writeback-connectivity) | SSPR_0030: nie można zresetować hasła z powodu słabego połączenia ze środowiskiem lokalnym. Skontaktuj się z administratorem i poproś o zbadanie.|

## <a name="troubleshoot-the-password-reset-configuration-in-the-azure-portal"></a>Rozwiązywanie problemów z konfiguracją resetowania hasła w Azure Portal

| Error | Rozwiązanie |
| --- | --- |
| Nie widzę sekcji **resetowania hasła** w usłudze Azure AD w Azure Portal. | Taka sytuacja może wystąpić, jeśli nie masz licencji usługi Azure AD przypisanej do administratora wykonującego operację. <br> <br> Przypisz licencję do danego konta administratora. Można wykonać kroki opisane w artykule [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) .|
| Nie widzę określonej opcji konfiguracji. | Wiele elementów interfejsu użytkownika jest ukrytych do momentu, gdy są one zbędne. Spróbuj włączyć wszystkie opcje, które chcesz zobaczyć. |
| Nie widzę karty **integracja lokalna** . | Ta opcja zostanie wyświetlona tylko wtedy, gdy pobrano Azure AD Connect i skonfigurowano funkcję zapisywania zwrotnego haseł. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Azure AD Connect przy użyciu ustawień ekspresowych](../hybrid/how-to-connect-install-express.md). |

## <a name="troubleshoot-password-reset-reporting"></a>Rozwiązywanie problemów z raportowaniem resetowania hasła

| Error | Rozwiązanie |
| --- | --- |
| Nie widzę żadnych typów działań związanych z zarządzaniem hasłami w kategorii zdarzenia inspekcji samoobsługowego **zarządzania hasłami** . | Taka sytuacja może wystąpić, jeśli nie masz licencji usługi Azure AD przypisanej do administratora wykonującego operację. <br> <br> Ten problem można rozwiązać, przypisując licencję do danego konta administratora. Wykonaj kroki opisane w artykule [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) . |
| Rejestracje użytkowników są wyświetlane wiele razy. | Obecnie podczas rejestrowania użytkownika rejestrujemy każdy indywidualny element danych, który jest zarejestrowany jako oddzielne zdarzenie. <br> <br> Jeśli chcesz agregować te dane i mieć większą elastyczność w sposobie ich wyświetlania, możesz pobrać raport i otworzyć dane jako tabelę przestawną w programie Excel.

## <a name="troubleshoot-the-password-reset-registration-portal"></a>Rozwiązywanie problemów z portalem rejestracji resetowania haseł

| Error | Rozwiązanie |
| --- | --- |
| W katalogu nie włączono obsługi resetowania hasła. **Administrator nie włączył korzystania z tej funkcji.** | Przełącz flagę włączenia funkcji samoobsługowego **resetowania hasła** na **wybraną** lub **wszystkie** , a następnie wybierz pozycję **Zapisz**. |
| Użytkownik nie ma przypisanej licencji usługi Azure AD. **Administrator nie włączył korzystania z tej funkcji.** | Taka sytuacja może wystąpić, jeśli nie masz licencji usługi Azure AD przypisanej do administratora wykonującego operację. <br> <br> Ten problem można rozwiązać, przypisując licencję do danego konta administratora. Wykonaj kroki opisane w artykule [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) .|
| Wystąpił błąd podczas przetwarzania żądania. | Przyczyną może być wiele problemów, ale zazwyczaj ten błąd jest spowodowany awarią usługi lub problemem z konfiguracją. Jeśli ten błąd wystąpi i ma wpływ na firmę, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania dodatkowej pomocy. |

## <a name="troubleshoot-the-password-reset-portal"></a>Rozwiązywanie problemów z portalem resetowania haseł

| Error | Rozwiązanie |
| --- | --- |
| W katalogu nie włączono obsługi resetowania hasła. | Przełącz flagę włączenia funkcji samoobsługowego **resetowania hasła** na **wybraną** lub **wszystkie** , a następnie wybierz pozycję **Zapisz**. |
| Użytkownik nie ma przypisanej licencji usługi Azure AD. | Taka sytuacja może wystąpić, jeśli nie masz licencji usługi Azure AD przypisanej do administratora wykonującego operację. <br> <br> Ten problem można rozwiązać, Jeśli przypiszesz licencję do danego konta administratora. Wykonaj kroki opisane w artykule [Przypisywanie, weryfikowanie i rozwiązywanie problemów z licencjami](../users-groups-roles/licensing-groups-assign.md#step-1-assign-the-required-licenses) . |
| Katalog jest włączony do resetowania hasła, ale użytkownik nie ma lub ma nieprawidłowo sformułowane informacje o uwierzytelnianiu. | Przed kontynuowaniem upewnij się, że użytkownik ma poprawnie sformułowane dane kontaktowe w pliku w katalogu. Aby uzyskać więcej informacji, zobacz dane używane przez funkcję samoobsługowego [resetowania hasła w usłudze Azure AD](howto-sspr-authenticationdata.md). |
| Katalog jest włączony do resetowania haseł, ale użytkownik ma tylko jedną część danych kontaktowych w pliku, gdy zasady są ustawione tak, aby wymagały dwóch metod weryfikacji. | Przed kontynuowaniem upewnij się, że użytkownik ma co najmniej dwie prawidłowo skonfigurowane metody kontaktu. Przykładem jest numer telefonu komórkowego *i* numer telefonu biurowego. |
| Katalog jest włączony do resetowania hasła i użytkownik jest prawidłowo skonfigurowany, ale nie można skontaktować się z użytkownikiem. | Może to być wynikiem tymczasowego błędu usługi lub w przypadku nieprawidłowych danych kontaktowych, których nie można poprawnie wykryć. <br> <br> Jeśli użytkownik odczeka 10 sekund, pojawią się linki "Spróbuj ponownie" i "Skontaktuj się z administratorem". Jeśli użytkownik wybierze opcję "Spróbuj ponownie", ponawia próbę wywołania. Jeśli użytkownik wybierze opcję "Skontaktuj się z administratorem", wyśle wiadomość e-mail z formularzem z prośbą o przeprowadzenie resetowania hasła dla tego konta użytkownika. |
| Użytkownik nigdy nie otrzymuje wiadomości SMS lub połączenia telefonicznego resetowania hasła. | Może to być spowodowane błędnym numerem telefonu w katalogu. Upewnij się, że numer telefonu jest w formacie "+ CCC xxxyyyzzzzXeeee". <br> <br> Resetowanie hasła nie obsługuje rozszerzeń, nawet jeśli określisz je w katalogu. Rozszerzenia są usuwane przed wysłaniem wywołania. Użyj numeru bez rozszerzenia lub Zintegruj rozszerzenie do numeru telefonu w prywatnej wymianie gałęzi. |
| Użytkownik nigdy nie otrzymuje wiadomości e-mail dotyczącej resetowania hasła. | Najbardziej typową przyczyną tego problemu jest to, że komunikat jest odrzucany przez filtr spamu. Sprawdź wiadomość e-mail z folderem spam, wiadomości-śmieci lub elementy usunięte. <br> <br> Upewnij się również, że sprawdzasz poprawność konta e-mail dla wiadomości. |
| Ustawiono zasadę resetowania hasła, ale jeśli konto administratora używa resetowania hasła, te zasady nie są stosowane. | Firma Microsoft zarządza zasadami resetowania hasła administratora i kontroluje je w celu zapewnienia najwyższego poziomu zabezpieczeń. |
| Użytkownik nie próbuje zresetować hasła zbyt wiele razy w ciągu dnia. | Implementuje mechanizm automatycznego ograniczania przepustowości, aby uniemożliwić użytkownikom próbę resetowania swoich haseł zbyt wiele razy w krótkim czasie. Ograniczanie wystąpienia występuje, gdy: <br><ul><li>Użytkownik próbuje zweryfikować numer telefonu pięć razy w ciągu jednej godziny.</li><li>Użytkownik próbuje użyć bramy pytania zabezpieczające pięć razy w ciągu godziny.</li><li>Użytkownik próbuje zresetować hasło dla tego samego konta użytkownika pięć razy w ciągu jednej godziny.</li></ul>Aby rozwiązać ten problem, należy polecić użytkownikowi odczekanie 24 godzin od ostatniej próby. Użytkownik może następnie zresetować swoje hasło. |
| Użytkownik widzi błąd podczas weryfikowania numeru telefonu. | Ten błąd występuje, gdy wprowadzony numer telefonu nie jest zgodny z numerem telefonu w pliku. Upewnij się, że użytkownik wprowadza pełny numer telefonu, w tym kod obszaru i kraju, przy próbie użycia telefonicznej metody resetowania hasła. |
| Wystąpił błąd podczas przetwarzania żądania. | Przyczyną może być wiele problemów, ale zazwyczaj ten błąd jest spowodowany awarią usługi lub problemem z konfiguracją. Jeśli ten błąd wystąpi i ma wpływ na firmę, skontaktuj się z pomocą techniczną firmy Microsoft w celu uzyskania dodatkowej pomocy. |
| Naruszenie zasad lokalnych | Hasło nie jest zgodne z lokalnymi zasadami haseł Active Directory. |
| Hasło nie jest zgodne z zasadami rozmytymi | Używane hasło pojawia się na [liście zakazanych haseł](https://docs.microsoft.com/azure/active-directory/authentication/concept-password-ban-bad#how-are-passwords-evaluated) i nie może być używane. |

## <a name="troubleshoot-password-writeback"></a>Rozwiązywanie problemów z zapisywaniem zwrotnym haseł

| Error | Rozwiązanie |
| --- | --- |
| Usługa resetowania haseł nie jest uruchamiana lokalnie. Błąd 6800 jest wyświetlany w dzienniku zdarzeń aplikacji maszyny Azure AD Connect. <br> <br> Po dołączeniu, federacyjnym, uwierzytelnianiu lub uwierzytelnianiem w skrócie hasła użytkownicy nie mogą resetować haseł. | Po włączeniu funkcji zapisywania zwrotnego haseł aparat synchronizacji wywołuje bibliotekę zapisywania zwrotnego w celu przeprowadzenia konfiguracji (dołączania), komunikując się z usługą w chmurze. Wszelkie błędy napotkane podczas dołączania lub podczas uruchamiania punktu końcowego Windows Communication Foundation (WCF) na potrzeby zapisywania zwrotnego haseł w dzienniku zdarzeń na komputerze Azure AD Connect. <br> <br> Podczas ponownego uruchamiania usługi Azure AD Sync (ADSync), jeśli został skonfigurowany zapis, zostanie uruchomiony punkt końcowy WCF. Jeśli jednak uruchomienie punktu końcowego nie powiedzie się, będziemy rejestrować zdarzenie 6800 i umożliwić uruchomienie usługi synchronizacji. Obecność tego zdarzenia oznacza, że punkt końcowy zapisywania zwrotnego haseł nie został uruchomiony. Szczegóły dziennika zdarzeń dla tego zdarzenia 6800, a także wpisy dziennika zdarzeń generowane przez składnik PasswordResetService, wskazują dlaczego nie można uruchomić punktu końcowego. Przejrzyj te błędy w dzienniku zdarzeń i spróbuj ponownie uruchomić Azure AD Connect, jeśli zapisywanie zwrotne haseł nadal nie działa. Jeśli problem będzie się powtarzał, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.
| Gdy użytkownik podejmie próbę zresetowania hasła lub odblokowania konta z włączoną funkcją zapisywania zwrotnego haseł, operacja kończy się niepowodzeniem. <br> <br> Ponadto zostanie wyświetlone zdarzenie w dzienniku zdarzeń Azure AD Connect zawierający: "aparat synchronizacji zwrócił błąd HR = 800700CE, Message = nazwa pliku lub rozszerzenie jest zbyt długie" po wystąpieniu operacji odblokowywania. | Znajdź konto Active Directory dla Azure AD Connect i zresetuj hasło, tak aby nie zawierało więcej niż 256 znaków. Następnie otwórz **usługę synchronizacji** z menu **Start** . Przejdź do **łączników** i Znajdź **Active Directory łącznik**. Zaznacz go, a następnie wybierz pozycję **Właściwości**. Przejdź do strony **poświadczenia** i wprowadź nowe hasło. Wybierz **przycisk OK** , aby zamknąć stronę. |
| W ostatnim kroku procesu instalacji Azure AD Connect zostanie wyświetlony komunikat o błędzie informujący o tym, że nie można skonfigurować funkcji zapisywania zwrotnego haseł. <br> <br> Dziennik zdarzeń aplikacji Azure AD Connect zawiera błąd 32009 z tekstem "błąd podczas pobierania tokenu uwierzytelniania". | Ten błąd występuje w następujących dwóch przypadkach: <br><ul><li>Określono nieprawidłowe hasło do konta administratora globalnego określonego na początku procesu instalacji Azure AD Connect.</li><li>Podjęto próbę użycia użytkownika federacyjnego dla konta administratora globalnego określonego na początku procesu instalacji Azure AD Connect.</li></ul> Aby rozwiązać ten problem, upewnij się, że nie używasz konta federacyjnego dla administratora globalnego określonego na początku procesu instalacji. Upewnij się również, że podane hasło jest poprawne. |
| Dziennik zdarzeń Azure AD Connect Machine zawiera błąd 32002, który jest generowany przez uruchomienie PasswordResetService. <br> <br> Błąd: "błąd podczas nawiązywania połączenia z ServiceBus. Dostawca tokenu nie może dostarczyć tokenu zabezpieczającego ". | W środowisku lokalnym nie można nawiązać połączenia z punktem końcowym Azure Service Bus w chmurze. Ten błąd jest zwykle spowodowany przez regułę zapory blokującą połączenie wychodzące z określonym portem lub adresem sieci Web. Aby uzyskać więcej informacji, zobacz [wymagania wstępne dotyczące łączności](../hybrid/how-to-connect-install-prerequisites.md) . Po zaktualizowaniu tych reguł należy ponownie uruchomić komputer Azure AD Connect, a zapisywanie zwrotne haseł powinno rozpocząć pracę. |
| Po pewnym czasie, federacyjnym, uwierzytelnianiem przekazującym lub użytkownikami z synchronizacją skrótu hasła nie można resetować haseł. | W niektórych rzadkich przypadkach usługa zapisywania zwrotnego haseł może zakończyć się niepowodzeniem po ponownym uruchomieniu Azure AD Connect. W takich przypadkach należy najpierw sprawdzić, czy funkcja zapisywania zwrotnego haseł jest włączona w środowisku lokalnym. Możesz sprawdzić za pomocą Kreatora Azure AD Connect lub programu PowerShell (zobacz poprzednią sekcję HowTos). Jeśli ta funkcja jest włączona, spróbuj ponownie włączyć lub wyłączyć tę funkcję za pomocą interfejsu użytkownika lub programu PowerShell. Jeśli to nie zadziała, spróbuj przeprowadzić pełne odinstalowanie i ponowne zainstalowanie Azure AD Connect. |
| Federacyjnego, przekazywane uwierzytelnianie lub Użytkownicy z synchronizacją hasła, którzy próbują zresetować swoje hasła, zobaczą błąd po próbie przesłania hasła. Błąd wskazuje, że wystąpił problem z usługą. <br ><br> Oprócz tego problemu podczas operacji resetowania hasła może zostać wyświetlony komunikat o błędzie, że agent zarządzania odmówił dostępu w lokalnych dziennikach zdarzeń. | Jeśli te błędy są widoczne w dzienniku zdarzeń, upewnij się, że konto agenta zarządzania Active Directory (ADMA) określone w Kreatorze w momencie konfiguracji ma uprawnienia wymagane do zapisywania zwrotnego haseł. <br> <br> Po podaniu tego uprawnienia może upłynąć do godziny, gdy uprawnienia Trickle się w `sdprop` tle na kontrolerze domeny (DC). <br> <br> Aby Resetowanie hasła działało, uprawnienie musi być oznaczone przy użyciu deskryptora zabezpieczeń obiektu użytkownika, którego hasło jest resetowane. Dopóki to uprawnienie nie zostanie wyświetlone w obiekcie User, Resetowanie hasła nadal kończy się niepowodzeniem z komunikatem o odmowie dostępu. |
| Federacyjne, przekazywane uwierzytelnianie lub Użytkownicy ze skróconymi hasłami, którzy próbują zresetować swoje hasła, zobaczą błąd po przesłaniu hasła. Błąd wskazuje, że wystąpił problem z usługą. <br> <br> Oprócz tego problemu podczas operacji resetowania hasła może zostać wyświetlony komunikat o błędzie w dziennikach zdarzeń z usługi Azure AD Connectej wskazujący, że nie można odnaleźć obiektu. | Ten błąd zazwyczaj wskazuje, że aparat synchronizacji nie może znaleźć obiektu użytkownika w obszarze łącznika usługi Azure AD lub połączonym obiekcie Metaverse (MV) lub w powiązanym z nim łącznika usługi Azure AD. <br> <br> Aby rozwiązać ten problem, upewnij się, że użytkownik jest rzeczywiście synchronizowany z lokalnego do usługi Azure AD za pośrednictwem bieżącego wystąpienia Azure AD Connect i sprawdź stan obiektów w miejscach łączników i MV. Upewnij się, że obiekt usługi certyfikatów Active Directory (AD CS) jest połączony z obiektem MV za pośrednictwem reguły "Microsoft.InfromADUserAccountEnabled.xxx".|
| Federacyjne, przekazywane uwierzytelnianie lub Użytkownicy z synchronizacją hasła, którzy próbują zresetować swoje hasła, zobaczą błąd po przesłaniu hasła. Błąd wskazuje, że wystąpił problem z usługą. <br> <br> Oprócz tego problemu podczas operacji resetowania hasła może zostać wyświetlony błąd w dziennikach zdarzeń z usługi Azure AD Connect, która wskazuje, że występuje błąd "znaleziono wiele dopasowań". | Oznacza to, że aparat synchronizacji wykrył, że obiekt MV jest podłączony do więcej niż jednego obiektu usług AD CS za pośrednictwem "Microsoft.InfromADUserAccountEnabled.xxx". Oznacza to, że użytkownik ma włączone konto w więcej niż jednym lesie. Ten scenariusz nie jest obsługiwany w przypadku zapisywania zwrotnego haseł. |
| Operacje na haśle kończą się niepowodzeniem z powodu błędu konfiguracji. Dziennik zdarzeń aplikacji zawiera błąd Azure AD Connect 6329 z tekstem "0x8023061f (operacja nie powiodła się, ponieważ nie włączono synchronizacji haseł na tym agencie zarządzania)". | Ten błąd występuje w przypadku zmiany konfiguracji Azure AD Connect w celu dodania nowego lasu Active Directory (lub usunięcia i odczytania istniejącego lasu) po włączeniu funkcji zapisywania zwrotnego haseł. Operacje na hasłach dla użytkowników w tych ostatnio dodanych lasach kończą się niepowodzeniem. Aby rozwiązać ten problem, Wyłącz, a następnie ponownie włącz funkcję zapisywania zwrotnego haseł po zakończeniu zmian konfiguracji lasu. |

## <a name="password-writeback-event-log-error-codes"></a>Zdarzenia zapisywania zwrotnego haseł — kody błędów dziennika

Najlepszym rozwiązaniem w przypadku rozwiązywania problemów z funkcją zapisywania zwrotnego haseł jest zbadanie dziennika zdarzeń aplikacji na komputerze Azure AD Connect. Ten dziennik zdarzeń zawiera zdarzenia z dwóch źródeł interesujących funkcję zapisywania zwrotnego haseł. Źródło PasswordResetService opisuje operacje i problemy związane z operacją zapisywania zwrotnego haseł. Źródło ADSync opisuje operacje i problemy związane z ustawianiem haseł w środowisku Active Directoryu.

### <a name="if-the-source-of-the-event-is-adsync"></a>Jeśli źródłem zdarzenia jest ADSync

| Code | Nazwa lub komunikat | Opis |
| --- | --- | --- |
| 6329 | Poprzednia: MMS (4924) 0x80230619: "ograniczenie uniemożliwia zmianę hasła do bieżącego określonego." | To zdarzenie występuje, gdy usługa zapisywania zwrotnego haseł próbuje ustawić hasło w katalogu lokalnym, które nie spełnia wymagań dotyczących wieku, historii, złożoności i filtrowania. <br> <br> Jeśli użytkownik ma minimalny okres ważności hasła i ostatnio zmienił hasło w tym oknie, nie można ponownie zmienić hasła, dopóki nie osiągnie on określonego wieku w domenie. W celach testowych minimalny wiek powinien być ustawiony na 0. <br> <br> Jeśli włączono wymagania dotyczące historii haseł, należy wybrać hasło, które nie było używane w ciągu ostatnich *N* razy, gdzie *N* jest ustawieniem historii haseł. Jeśli wybierzesz hasło, które zostało użyte w ciągu ostatnich *N* razy, zobaczysz błąd w tym przypadku. W celach testowych historia hasła powinna być ustawiona na 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła, wszystkie z nich są wymuszane, gdy użytkownik próbuje zmienić lub zresetować hasło. <br> <br> Jeśli włączono filtry haseł, a użytkownik wybierze hasło, które nie spełnia kryteriów filtrowania, operacja resetowania lub zmiany nie powiedzie się. |
| 6329 | MMS (3040): admaexport. cpp (2837): serwer nie zawiera kontroli zasad haseł LDAP. | Ten problem występuje, gdy w kontrolerach domen nie jest włączony formant LDAP_SERVER_POLICY_HINTS_OID (1.2.840.113556.1.4.2066). Aby użyć funkcji zapisywania zwrotnego haseł, należy włączyć kontrolkę. W tym celu kontrolery domeny muszą być w systemie Windows Server 2008R2 lub nowszym. |
| HR 8023042 | Aparat synchronizacji zwrócił błąd HR = 80230402, Message = próba pobrania obiektu nie powiodła się, ponieważ istnieją zduplikowane wpisy z tą samą kotwicą. | Ten błąd występuje, gdy ten sam identyfikator użytkownika jest włączony w wielu domenach. Przykładem jest synchronizacja lasów kont i zasobów oraz posiadanie tego samego identyfikatora użytkownika i włączenie go w każdym lesie. <br> <br> Ten błąd może również wystąpić, jeśli używasz nieunikatowego atrybutu zakotwiczenia, takiego jak alias lub nazwa UPN, i dwóch użytkowników współużytkują ten sam atrybut zakotwiczenia. <br> <br> Aby rozwiązać ten problem, upewnij się, że nie masz żadnych zduplikowanych użytkowników w domenach i używasz unikatowego atrybutu zakotwiczenia dla każdego użytkownika. |

### <a name="if-the-source-of-the-event-is-passwordresetservice"></a>Jeśli źródłem zdarzenia jest PasswordResetService

| Code | Nazwa lub komunikat | Opis |
| --- | --- | --- |
| 31001 | PasswordResetStart | To zdarzenie oznacza, że Usługa lokalna wykryła żądanie zresetowania hasła dla federacyjnego, przekazywanego uwierzytelniania lub użytkownika z synchronizacją skrótu hasła, który pochodzi z chmury. To zdarzenie jest pierwszym zdarzeniem w każdej operacji zapisywania zwrotnego resetowania hasła. |
| 31002 | PasswordResetSuccess | To zdarzenie oznacza, że użytkownik wybrał nowe hasło podczas operacji resetowania hasła. Ustalono, że to hasło spełnia wymagania dotyczące hasła firmowego. Hasło zostało pomyślnie zapisaną w lokalnym środowisku Active Directory. |
| 31003 | PasswordResetFail | To zdarzenie oznacza, że użytkownik wybrał hasło, a hasło zostało pomyślnie dostarczone do środowiska lokalnego. Ale podczas próby ustawienia hasła w lokalnym środowisku Active Directory Wystąpił błąd. Ten błąd może wystąpić z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności lub filtru dla domeny. Aby rozwiązać ten problem, Utwórz nowe hasło.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika znajduje się w grupie ochrony, takiej jak domena lub grupa administratorów przedsiębiorstwa, która nie zezwala na operacje ustawiania haseł.</li></ul>|
| 31004 | OnboardingEventStart | To zdarzenie występuje w przypadku włączenia funkcji zapisywania zwrotnego haseł w Azure AD Connect i rozpoczęciu pracy organizacji z usługą sieci Web zapisywania zwrotnego haseł. |
| 31005 | OnboardingEventSuccess | To zdarzenie wskazuje, że proces dołączania zakończył się pomyślnie i że funkcja zapisywania zwrotnego haseł jest gotowa do użycia. |
| 31006 | ChangePasswordStart | To zdarzenie oznacza, że Usługa lokalna wykryła żądanie zmiany hasła dla federacyjnego, przekazywanego uwierzytelniania lub użytkownika z synchronizacją skrótu hasła, który pochodzi z chmury. To zdarzenie jest pierwszym zdarzeniem podczas każdej operacji zapisywania zwrotnego zmiany hasła. |
| 31007 | ChangePasswordSuccess | To zdarzenie oznacza, że użytkownik wybrał nowe hasło podczas operacji zmiany hasła, ustalił, że hasło spełnia wymagania dotyczące hasła firmowego i że hasło zostało pomyślnie zapisaną w lokalnym środowisku Active Directory. |
| 31008 | ChangePasswordFail | To zdarzenie oznacza, że użytkownik wybrał hasło i że hasło zostało pomyślnie dostarczone do środowiska lokalnego, ale podczas próby ustawienia hasła w lokalnym środowisku Active Directory Wystąpił błąd. Ten błąd może wystąpić z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności lub filtru dla domeny. Aby rozwiązać ten problem, Utwórz nowe hasło.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika należy do grupy ochrony, takiej jak Administratorzy domeny lub przedsiębiorstwa, które nie zezwalają na operacje ustawiania haseł.</li></ul> |
| 31009 | ResetUserPasswordByAdminStart | Usługa lokalna wykryła żądanie zresetowania hasła dla federacyjnego, uwierzytelniania przekazywanego lub użytkownika z synchronizacją skrótu z uprawnieniami administratora w imieniu użytkownika. To zdarzenie jest pierwszym zdarzeniem w każdej operacji zapisywania zwrotnego resetowania hasła, która została zainicjowana przez administratora. |
| 31010 | ResetUserPasswordByAdminSuccess | Administrator zaznaczył nowe hasło podczas operacji inicjowania hasła zainicjowane przez administratora. Ustalono, że to hasło spełnia wymagania dotyczące hasła firmowego. Hasło zostało pomyślnie zapisaną w lokalnym środowisku Active Directory. |
| 31011 | ResetUserPasswordByAdminFail | Administrator zaznaczył hasło w imieniu użytkownika. Hasło zostało pomyślnie dostarczone do środowiska lokalnego. Ale podczas próby ustawienia hasła w lokalnym środowisku Active Directory Wystąpił błąd. Ten błąd może wystąpić z kilku powodów: <br><ul><li>Hasło użytkownika nie spełnia wymagań dotyczących wieku, historii, złożoności lub filtru dla domeny. Spróbuj użyć nowego hasła, aby rozwiązać ten problem.</li><li>Konto usługi ADMA nie ma odpowiednich uprawnień, aby ustawić nowe hasło dla danego konta użytkownika.</li><li>Konto użytkownika należy do grupy ochrony, takiej jak Administratorzy domeny lub przedsiębiorstwa, które nie zezwalają na operacje ustawiania haseł.</li></ul>  |
| 31012 | OffboardingEventStart | To zdarzenie występuje w przypadku wyłączenia funkcji zapisywania zwrotnego haseł przy użyciu Azure AD Connect i wskazuje, że rozpoczęto odłączanie organizacji do usługi sieci Web zapisywania zwrotnego haseł. |
| 31013| OffboardingEventSuccess| To zdarzenie wskazuje, że proces odłączania zakończył się powodzeniem, a funkcja zapisywania zwrotnego haseł została pomyślnie wyłączona. |
| 31014| OffboardingEventFail| To zdarzenie wskazuje, że proces odłączania nie powiódł się. Może to być spowodowane błędem uprawnień w chmurze lub lokalnym kontem administratora określonym podczas konfiguracji. Ten błąd może również wystąpić, jeśli próbujesz użyć federacyjnego administratora globalnego w chmurze podczas wyłączania zapisywania zwrotnego haseł. Aby rozwiązać ten problem, Sprawdź uprawnienia administracyjne i upewnij się, że nie korzystasz z konta federacyjnego podczas konfigurowania funkcji zapisywania zwrotnego haseł.|
| 31015| WriteBackServiceStarted| To zdarzenie wskazuje, że usługa zapisywania zwrotnego haseł została uruchomiona pomyślnie. Jest gotowy do akceptowania żądań zarządzania hasłami z chmury.|
| 31016| WriteBackServiceStopped| To zdarzenie wskazuje, że usługa zapisywania zwrotnego haseł została zatrzymana. Wszystkie żądania zarządzania hasłami z chmury nie powiodą się.|
| 31017| AuthTokenSuccess| To zdarzenie wskazuje, że został pomyślnie pobrany Token autoryzacji dla administratora globalnego określonego podczas instalacji Azure AD Connect, aby rozpocząć proces odłączania lub dołączania.|
| 31018| KeyPairCreationSuccess| To zdarzenie wskazuje, że klucz szyfrowania hasła został pomyślnie utworzony. Ten klucz służy do szyfrowania haseł z chmury do wysłania do środowiska lokalnego.|
| 32000| UnknownError| To zdarzenie wskazuje, że podczas operacji zarządzania hasłami wystąpił nieznany błąd. Aby uzyskać więcej informacji, zobacz tekst wyjątku w zdarzeniu. Jeśli masz problemy, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł. Jeśli to nie pomoże, Dołącz kopię dziennika zdarzeń wraz z IDENTYFIKATORem śledzenia, który został określony dla inżyniera pomocy technicznej.|
| 32001| Wystąpił błąd| To zdarzenie wskazuje, że wystąpił błąd podczas nawiązywania połączenia z usługą resetowania hasła w chmurze. Ten błąd występuje zazwyczaj, gdy Usługa lokalna nie może nawiązać połączenia z usługą sieci Web resetowania hasła.|
| 32002| ServiceBusError| To zdarzenie wskazuje, że wystąpił błąd podczas nawiązywania połączenia z wystąpieniem Service Bus dzierżawy. Taka sytuacja może wystąpić, jeśli blokujesz połączenia wychodzące w środowisku lokalnym. Sprawdź zaporę, aby zezwolić na połączenia za pośrednictwem protokołu TCP 443 https://ssprdedicatedsbprodncu.servicebus.windows.neti do programu, a następnie spróbuj ponownie. Jeśli nadal występują problemy, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.|
| 32003| InPutValidationError| To zdarzenie oznacza, że dane wejściowe przesłane do naszego interfejsu API usługi sieci Web były nieprawidłowe. Spróbuj ponownie wykonać operację.|
| 32004| DecryptionError| To zdarzenie wskazuje, że wystąpił błąd podczas odszyfrowywania hasła, które dotarło do chmury. Może to być spowodowane niezgodnością klucza odszyfrowywania między usługą w chmurze i środowiskiem lokalnym. Aby rozwiązać ten problem, Wyłącz, a następnie ponownie włącz funkcję zapisywania zwrotnego haseł w środowisku lokalnym.|
| 32005| ConfigurationError| Podczas dołączania zapisujemy informacje specyficzne dla dzierżawy w pliku konfiguracji w środowisku lokalnym. To zdarzenie wskazuje, że wystąpił błąd podczas zapisywania tego pliku lub gdy usługa została uruchomiona, wystąpił błąd podczas odczytu pliku. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł w celu wymuszenia ponownego zapisania pliku konfiguracji.|
| 32007| OnBoardingConfigUpdateError| Podczas dołączania dane są wysyłane z chmury do lokalnej usługi resetowania haseł. Te dane są następnie zapisywane w pliku w pamięci przed wysłaniem do usługi synchronizacji w celu bezpiecznego przechowywania na dysku. To zdarzenie wskazuje, że występuje problem z zapisywaniem lub aktualizowaniem tych danych w pamięci. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł, aby wymusić ponowne zapisywanie tego pliku konfiguracji.|
| 32008| ValidationError| To zdarzenie wskazuje, że odebrano nieprawidłową odpowiedź z usługi sieci Web resetowania hasła. Aby rozwiązać ten problem, spróbuj wyłączyć i ponownie włączyć funkcję zapisywania zwrotnego haseł.|
| 32009| AuthTokenError| To zdarzenie wskazuje, że nie można uzyskać tokenu autoryzacji dla konta administratora globalnego określonego podczas instalacji Azure AD Connect. Ten błąd może być spowodowany przez nieprawidłową nazwę użytkownika lub hasło określone dla konta administratora globalnego. Ten błąd może również wystąpić, jeśli określone konto administratora globalnego jest federacyjne. Aby rozwiązać ten problem, należy ponownie uruchomić konfigurację przy użyciu prawidłowej nazwy użytkownika i hasła oraz upewnić się, że administrator jest kontem zarządzanym (tylko w chmurze lub z synchronizacją).|
| 32010| CryptoError| To zdarzenie wskazuje, że wystąpił błąd podczas generowania klucza szyfrowania hasła lub odszyfrowywania hasła przychodzącego z usługi w chmurze. Ten błąd najkorzystnie wskazuje na problem z Twoim środowiskiem. Zapoznaj się ze szczegółowymi informacjami o dzienniku zdarzeń, aby dowiedzieć się więcej o tym, jak rozwiązać ten problem. Możesz również spróbować wyłączyć i ponownie włączyć usługę zapisywania zwrotnego haseł.|
| 32011| OnBoardingServiceError| To zdarzenie oznacza, że Usługa lokalna nie może prawidłowo skomunikować się z usługą sieci Web resetowania hasła w celu zainicjowania procesu dołączania. Może to być spowodowane regułą zapory lub problemem z uzyskaniem tokenu uwierzytelniania dla dzierżawy. Aby rozwiązać ten problem, upewnij się, że połączenia wychodzące nie są blokowane przez protokoły TCP 443 i https://ssprdedicatedsbprodncu.servicebus.windows.netTCP 9350-9354 lub do. Upewnij się również, że konto administratora usługi Azure AD, które jest używane do dołączania, nie jest federacyjne.|
| 32013| OffBoardingError| To zdarzenie oznacza, że Usługa lokalna nie może prawidłowo skomunikować się z usługą sieci Web resetowania hasła w celu zainicjowania procesu odłączania. Może to być spowodowane regułą zapory lub problemem z uzyskaniem tokenu autoryzacji dla dzierżawy. Aby rozwiązać ten problem, upewnij się, że nie blokujesz połączeń wychodzących przez 443 lub https://ssprdedicatedsbprodncu.servicebus.windows.netdo i że konto administratora Azure Active Directory, którego używasz do odłączania nie jest federacyjne.|
| 32014| ServiceBusWarning| To zdarzenie wskazuje, że musiałeś ponowić próbę nawiązania połączenia z wystąpieniem Service Bus dzierżawy. W normalnych warunkach nie powinna to stanowić problemu, ale jeśli widzisz to zdarzenie wiele razy, rozważ sprawdzenie połączenia sieciowego w celu Service Bus, szczególnie jeśli jest to połączenie o dużej opóźnieniu lub niskiej przepustowości.|
| 32015| ReportServiceHealthError| W celu monitorowania kondycji usługi zapisywania zwrotnego haseł wysyłamy dane pulsu do usługi sieci Web resetowania hasła co pięć minut. To zdarzenie wskazuje, że wystąpił błąd podczas wysyłania informacji o kondycji z powrotem do usługi sieci Web w chmurze. Te informacje o kondycji nie obejmują żadnych danych osobowych i są całkowicie Pulse i podstawowe statystyki usługi, dzięki czemu możemy zapewnić informacje o stanie usługi w chmurze.|
| 33001| ADUnKnownError| To zdarzenie wskazuje, że wystąpił nieznany błąd zwrócony przez Active Directory. Aby uzyskać więcej informacji, sprawdź dziennik zdarzeń serwera Azure AD Connect pod kątem zdarzeń ze źródła ADSync.|
| 33002| ADUserNotFoundError| To zdarzenie oznacza, że użytkownik próbujący zresetować lub zmienić hasło nie został znaleziony w katalogu lokalnym. Ten błąd może wystąpić, jeśli użytkownik został usunięty z lokalizacji lokalnej, ale nie w chmurze. Ten błąd może również wystąpić, jeśli wystąpił problem z synchronizacją. Sprawdź dzienniki synchronizacji i ostatnie szczegóły uruchamiania synchronizacji, aby uzyskać więcej informacji.|
| 33003| ADMutliMatchError| Gdy żądanie resetowania lub zmiany hasła pochodzi z chmury, używamy kotwicy w chmurze określonej podczas procesu instalacji Azure AD Connect, aby określić, jak połączyć żądanie z powrotem do użytkownika w środowisku lokalnym. To zdarzenie oznacza, że znaleźliśmy dwóch użytkowników w katalogu lokalnym z tym samym atrybutem zakotwiczenia w chmurze. Sprawdź dzienniki synchronizacji i ostatnie szczegóły uruchamiania synchronizacji, aby uzyskać więcej informacji.|
| 33004| ADPermissionsError| To zdarzenie oznacza, że konto usługi Active Directory Management Agent (ADMA) nie ma odpowiednich uprawnień dla danego konta, aby ustawić nowe hasło. Upewnij się, że konto ADMA w lesie użytkownika ma uprawnienia do resetowania hasła do wszystkich obiektów w lesie. Aby uzyskać więcej informacji na temat sposobu ustawiania uprawnień, zobacz krok 4. Konfigurowanie odpowiednich uprawnień Active Directory. Ten błąd może również wystąpić, gdy atrybut użytkownika AdminCount jest ustawiony na 1.|
| 33005| ADUserAccountDisabled| To zdarzenie oznacza, że podjęto próbę zresetowania lub zmiany hasła dla konta, które zostało wyłączone lokalnie. Włącz konto, a następnie spróbuj ponownie wykonać operację.|
| 33006| ADUserAccountLockedOut| To zdarzenie oznacza, że podjęto próbę zresetowania lub zmiany hasła dla konta, które zostało zablokowane w środowisku lokalnym. Blokady mogą wystąpić, gdy użytkownik próbował wykonać operację zmiany lub resetowania hasła zbyt wiele razy w krótkim czasie. Odblokuj konto, a następnie spróbuj ponownie wykonać operację.|
| 33007| ADUserIncorrectPassword| To zdarzenie oznacza, że użytkownik określił nieprawidłowe bieżące hasło podczas wykonywania operacji zmiany hasła. Określ prawidłowe bieżące hasło i spróbuj ponownie.|
| 33008| ADPasswordPolicyError| To zdarzenie występuje, gdy usługa zapisywania zwrotnego haseł próbuje ustawić hasło w katalogu lokalnym, które nie spełnia wymagań dotyczących wieku, historii, złożoności i filtrowania. <br> <br> Jeśli użytkownik ma minimalny okres ważności hasła i ostatnio zmienił hasło w tym oknie, nie można ponownie zmienić hasła, dopóki nie osiągnie on określonego wieku w domenie. W celach testowych minimalny wiek powinien być ustawiony na 0. <br> <br> Jeśli włączono wymagania dotyczące historii haseł, należy wybrać hasło, które nie było używane w ciągu ostatnich *N* razy, gdzie *N* jest ustawieniem historii haseł. Jeśli wybierzesz hasło, które zostało użyte w ciągu ostatnich *N* razy, zobaczysz błąd w tym przypadku. W celach testowych historia hasła powinna być ustawiona na 0. <br> <br> Jeśli masz wymagania dotyczące złożoności hasła, wszystkie z nich są wymuszane, gdy użytkownik próbuje zmienić lub zresetować hasło. <br> <br> Jeśli włączono filtry haseł, a użytkownik wybierze hasło, które nie spełnia kryteriów filtrowania, operacja resetowania lub zmiany nie powiedzie się.|
| 33009| ADConfigurationError| To zdarzenie oznacza, że wystąpił problem z zapisaniem hasła z powrotem w katalogu lokalnym z powodu problemu z konfiguracją Active Directory. Sprawdź dziennik zdarzeń aplikacji maszyny Azure AD Connect, aby uzyskać więcej informacji na temat tego błędu.|

## <a name="troubleshoot-password-writeback-connectivity"></a>Rozwiązywanie problemów z łącznością zapisywania zwrotnego haseł

Jeśli występują przerwy w świadczeniu usługi ze składnikiem zapisywania zwrotnego haseł Azure AD Connect, poniżej przedstawiono kilka szybkich kroków, które można wykonać, aby rozwiązać ten problem:

* [Potwierdź łączność sieciową](#confirm-network-connectivity)
* [Uruchom ponownie usługę synchronizacji Azure AD Connect](#restart-the-azure-ad-connect-sync-service)
* [Wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł](#disable-and-re-enable-the-password-writeback-feature)
* [Zainstaluj najnowszą wersję Azure AD Connect](#install-the-latest-azure-ad-connect-release)
* [Rozwiązywanie problemów z zapisywaniem zwrotnym haseł](#troubleshoot-password-writeback)

Ogólnie rzecz biorąc, aby odzyskać usługę w najbardziej szybki sposób, zalecamy wykonanie tych kroków w opisanej wcześniej kolejności.

### <a name="confirm-network-connectivity"></a>Potwierdź łączność sieciową

Najbardziej typowym punktem awarii jest to, że porty zapory i serwera proxy oraz limity czasu bezczynności są niepoprawnie skonfigurowane. 

W przypadku Azure AD Connect w wersji wersji 1.1.443.0 lub nowszej wymagany jest wychodzący dostęp HTTPS do następujących:

* \*. passwordreset.microsoftonline.com
* \*.servicebus.windows.net

Aby uzyskać więcej informacji, odwołując się do zaktualizowanej listy [zakresów adresów IP centrum](https://www.microsoft.com/download/details.aspx?id=41653) danych, Microsoft Azure które są aktualizowane w każdą środę i które mają obowiązywać w następnym poniedziałek.

Aby uzyskać więcej informacji, zapoznaj się z wymaganiami wstępnymi dotyczącymi łączności w artykule [wymagania wstępne dotyczące Azure AD Connect](../hybrid/how-to-connect-install-prerequisites.md) .

> [!NOTE]
> SSPR może również zakończyć się niepowodzeniem, jeśli ustawienia "hasło nigdy nie wygasa" lub "użytkownik nie może zmienić hasła" są skonfigurowane na koncie w AD DS lokalnym. 

### <a name="restart-the-azure-ad-connect-sync-service"></a>Uruchom ponownie usługę synchronizacji Azure AD Connect

Aby rozwiązać problemy z łącznością lub inne przejściowe problemy z usługą, należy ponownie uruchomić usługę synchronizacji Azure AD Connect:

1. Jako administrator wybierz pozycję **Uruchom** na serwerze z uruchomionym Azure AD Connect.
1. W polu wyszukiwania wprowadź ciąg **Services. msc** , a następnie wybierz pozycję **wprowadź**.
1. Wyszukaj wpis **synchronizacji Microsoft Azure AD** .
1. Kliknij prawym przyciskiem myszy wpis usługi, wybierz pozycję **Uruchom ponownie**, a następnie poczekaj na zakończenie operacji.

   ![Uruchom ponownie usługę Azure AD Sync przy użyciu graficznego interfejsu użytkownika][Service restart]

Te kroki powodują ponowne nawiązanie połączenia z usługą w chmurze i rozwiązanie ewentualnych przerw w działaniu. Jeśli ponowne uruchomienie usługi ADSync nie rozwiąże problemu, zalecamy wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł.

### <a name="disable-and-re-enable-the-password-writeback-feature"></a>Wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł

Aby rozwiązać problemy z łącznością, Wyłącz, a następnie włącz ponownie funkcję zapisywania zwrotnego haseł:

   1. Jako administrator Otwórz Kreatora konfiguracji Azure AD Connect.
   1. W obszarze **nawiązywanie połączenia z usługą Azure AD**wprowadź swoje poświadczenia administratora globalnego usługi Azure AD.
   1. W obszarze **Połącz z AD DS**wprowadź poświadczenia administratora usług domenowych w usłudze AD.
   1. W **unikatowy sposób identyfikujący użytkowników**, wybierz przycisk **dalej** .
   1. W obszarze **funkcje opcjonalne**wyczyść pole wyboru **zapisywanie zwrotne haseł** .
   1. Wybierz pozycję **dalej** przez pozostałe strony okna dialogowego bez zmiany wszystkiego, dopóki nie przejdziesz do strony **gotowy do konfiguracji** .
   1. Upewnij się, że na **stronie gotowe do skonfigurowania** jest wyświetlana opcja **zapisywania zwrotnego haseł** jako **wyłączona** , a następnie wybierz przycisk zielonej **konfiguracji** , aby zatwierdzić wprowadzone zmiany.
   1. Na **koniec**wyczyść opcję **Synchronizuj teraz** , a następnie wybierz pozycję **Zakończ** , aby zamknąć kreatora.
   1. Otwórz ponownie Kreatora konfiguracji Azure AD Connect.
   1. Powtórz kroki 2-8, z tą różnicą, że wybierz opcję **zapisywania zwrotnego haseł** na stronie **funkcje opcjonalne** , aby ponownie włączyć usługę.

Te kroki powodują ponowne nawiązanie połączenia z naszą usługą w chmurze i rozwiązanie ewentualnych przerw w działaniu.

Jeśli wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł nie rozwiąże problemu, zalecamy ponowne zainstalowanie Azure AD Connect.

### <a name="install-the-latest-azure-ad-connect-release"></a>Zainstaluj najnowszą wersję Azure AD Connect

Ponowne zainstalowanie Azure AD Connect może rozwiązać problemy związane z konfiguracją i połączeniem między usługami w chmurze i lokalnym środowiskiem Active Directory.

Zalecamy wykonanie tego kroku dopiero po wykonaniu dwóch pierwszych kroków opisanych wcześniej.

> [!WARNING]
> Jeśli dostosowano wbudowane reguły synchronizacji, należy *wykonać ich kopię zapasową przed kontynuowaniem uaktualniania, a następnie ręcznie je wdrożyć po zakończeniu.*

1. Pobierz najnowszą wersję Azure AD Connect z [Centrum pobierania Microsoft](https://go.microsoft.com/fwlink/?LinkId=615771).
1. Ponieważ już zainstalowano Azure AD Connect, należy przeprowadzić uaktualnienie w miejscu, aby zaktualizować instalację Azure AD Connect do najnowszej wersji.
1. Wykonaj pobrany pakiet i postępuj zgodnie z instrukcjami wyświetlanymi na ekranie, aby zaktualizować maszynę Azure AD Connect.

Poprzednie kroki powinny ponownie nawiązać połączenie z naszą usługą w chmurze i rozwiązać wszelkie przerwy, które mogą wystąpić.

Jeśli instalacja najnowszej wersji serwera Azure AD Connect nie rozwiąże problemu, zalecamy wyłączenie i ponowne włączenie funkcji zapisywania zwrotnego haseł w ostatnim kroku po zainstalowaniu najnowszej wersji.

## <a name="verify-that-azure-ad-connect-has-the-required-permission-for-password-writeback"></a>Sprawdź, czy Azure AD Connect ma wymagane uprawnienie do zapisywania zwrotnego haseł

Azure AD Connect wymaga uprawnień do Active Directory **resetowania hasła** , aby można było wykonać funkcję zapisywania zwrotnego haseł. Aby dowiedzieć się, czy Azure AD Connect ma wymagane uprawnienie dla danego lokalnego konta użytkownika Active Directory, można użyć funkcji uprawnienia efektywne systemu Windows:

1. Zaloguj się na serwerze Azure AD Connect i uruchom **Synchronization Service Manager** , wybierając pozycję **Uruchom** > **usługę synchronizacji**.
1. Na karcie **Łączniki** wybierz pozycję lokalna **Active Directory Domain Services** łącznika, a następnie wybierz pozycję **Właściwości**.  
   ![Synchronization Service Manager pokazujący, jak edytować właściwości](./media/active-directory-passwords-troubleshoot/checkpermission01.png)  
  
1. W oknie podręcznym wybierz pozycję **Połącz z lasem Active Directory** i zanotuj Właściwość **Nazwa użytkownika** . Ta właściwość jest kontem AD DS używanym przez Azure AD Connect do przeprowadzenia synchronizacji katalogów. Aby Azure AD Connect do wykonywania funkcji zapisywania zwrotnego haseł, konto AD DS musi mieć uprawnienie resetowania hasła.  

   ![Znajdowanie usługi synchronizacji Active Directory konta użytkownika](./media/active-directory-passwords-troubleshoot/checkpermission02.png) 
  
1. Zaloguj się do lokalnego kontrolera domeny i uruchom aplikację **Active Directory Użytkownicy i komputery** .
1. Wybierz pozycję **Widok** i upewnij się, że jest włączona opcja **funkcje zaawansowane** .  

   ![Active Directory Użytkownicy i komputery pokazują funkcje zaawansowane](./media/active-directory-passwords-troubleshoot/checkpermission03.png) 
  
1. Znajdź Active Directory konto użytkownika, które chcesz zweryfikować. Kliknij prawym przyciskiem myszy nazwę konta i wybierz pozycję **Właściwości**.  
1. W oknie podręcznym przejdź do karty **zabezpieczenia** i wybierz pozycję **Zaawansowane**.  
1. W oknie podręcznym **Zaawansowane ustawienia zabezpieczeń dla administratora** przejdź do karty **dostęp czynny** .
1. Wybierz pozycję **Wybierz użytkownika**, wybierz konto AD DS używane przez Azure AD Connect (zobacz krok 3), a następnie wybierz pozycję **Wyświetl dostęp czynny**.

   ![Karta dostęp czynny z informacjami o koncie synchronizacji](./media/active-directory-passwords-troubleshoot/checkpermission06.png) 
  
1. Przewiń w dół i Wyszukaj **hasło resetowania hasła**. Jeśli wpis ma znacznik wyboru, konto AD DS ma uprawnienia do resetowania hasła wybranego Active Directorygo konta użytkownika.  

   ![Sprawdzanie, czy konto synchronizacji ma uprawnienie Resetowanie hasła](./media/active-directory-passwords-troubleshoot/checkpermission07.png)  

## <a name="azure-ad-forums"></a>Fora usługi Azure AD

Jeśli masz ogólne pytanie dotyczące usługi Azure AD i funkcji samoobsługowego resetowania hasła, możesz poproś społeczność o pomoc techniczną na [forach usługi Azure AD](https://social.msdn.microsoft.com/Forums/en-US/home?forum=WindowsAzureAD). Członkowie społeczności obejmują inżynierów, menedżerów produktów, MVP i innych specjalistów IT.

## <a name="contact-microsoft-support"></a>Kontaktowanie się z pomocą techniczną firmy Microsoft

Jeśli nie możesz znaleźć odpowiedzi na problem, nasze zespoły pomocy technicznej są zawsze dostępne, aby pomóc Ci w dalszej próbie.

Aby prawidłowo pomóc, prosimy o podanie możliwie największej szczegółowości podczas otwierania sprawy. Te szczegóły obejmują:

* **Ogólny opis błędu**: jaki jest błąd? Jak zauważono zachowanie? Jak odtworzyć błąd? Podaj tyle szczegółów, ile to możliwe.
* **Strona**: jaka strona została zadana, gdy zauważysz błąd? Dołącz adres URL, jeśli możesz i zrzut ekranu strony.
* **Kod pomocy technicznej**: jaki był kod pomocy technicznej wygenerowany podczas napotkania błędu przez użytkownika?
   * Aby znaleźć ten kod, Odtwórz błąd, a następnie wybierz link **kod pomocy technicznej** u dołu ekranu i Wyślij do niego identyfikator GUID.

   ![Znajdź kod pomocy technicznej u dołu ekranu][Support code]

  * Jeśli jesteś na stronie bez kodu pomocy technicznej u dołu, wybierz klawisz F12 i wyszukaj identyfikatory SID i CID i Wyślij te dwa wyniki do inżyniera pomocy technicznej.
* **Data, godzina i strefa czasowa**: Uwzględnij dokładną datę i godzinę *w strefie czasowej* wystąpienia błędu.
* **Identyfikator użytkownika**: użytkownik, który wystąpił błąd? Przykładem jest *user\@contoso.com*.
   * Czy ten użytkownik jest federacyjny?
   * Czy to jest użytkownik uwierzytelniania Pass-through?
   * Czy to jest użytkownik z synchronizacją skrótu hasła?
   * Czy jest to użytkownik tylko w chmurze?
* **Licencjonowanie**: czy użytkownik ma przypisaną licencję usługi Azure AD?
* **Dziennik zdarzeń aplikacji**: Jeśli używasz funkcji zapisywania zwrotnego haseł, a błąd znajduje się w infrastrukturze lokalnej, Dołącz spakowaną kopię dziennika zdarzeń aplikacji z serwera Azure AD Connect.

[Service restart]: ./media/active-directory-passwords-troubleshoot/servicerestart.png "Uruchom ponownie usługę Azure AD Sync"
[Support code]: ./media/active-directory-passwords-troubleshoot/supportcode.png "Kod pomocy technicznej znajduje się w prawym dolnym rogu okna"

## <a name="next-steps"></a>Następne kroki

Poniższe artykuły zawierają dodatkowe informacje na temat resetowania haseł za pomocą usługi Azure AD:

* [Jak wykonać pomyślne wdrożenie funkcji samoobsługowego resetowania haseł?](howto-sspr-deployment.md)
* [Resetowanie lub zmiana hasła](../user-help/active-directory-passwords-update-your-own-password.md)
* [Rejestrowanie na potrzeby samoobsługowego resetowania haseł](../user-help/active-directory-passwords-reset-register.md)
* [Czy masz pytanie dotyczące licencjonowania?](concept-sspr-licensing.md)
* [Jakie dane są używane przez funkcję samoobsługowego resetowania haseł i jakie dane powinny zostać wypełnione dla użytkowników?](howto-sspr-authenticationdata.md)
* [Jakie metody uwierzytelniania są dostępne dla użytkowników?](concept-sspr-howitworks.md#authentication-methods)
* [Jakie są opcje zasad dla funkcji samoobsługowego resetowania haseł?](concept-sspr-policy.md)
* [Co to jest funkcja zapisywania zwrotnego haseł i dlaczego jest ona tak ważna?](howto-sspr-writeback.md)
* [Jak zgłosić działanie funkcji samoobsługowego resetowania haseł?](howto-sspr-reporting.md)
* [Jakie są dostępne opcje funkcji samoobsługowego resetowania haseł i do czego one służą?](concept-sspr-howitworks.md)
* [Mam pytanie, na które nie mogę znaleźć odpowiedzi](active-directory-passwords-faq.md)
