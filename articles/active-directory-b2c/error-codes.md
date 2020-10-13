---
title: Odwołanie do kodu błędu
titleSuffix: Azure AD B2C
description: Lista kodów błędów, które mogą zostać zwrócone przez usługę Azure Active Directory B2C.
services: B2C
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 10/02/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0122fa43c9d99c01797e3523748e4f31b4b7469a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91664855"
---
# <a name="error-codes-azure-active-directory-b2c"></a>Kody błędów: Azure Active Directory B2C

Usługa Azure Active Directory B2C może zwrócić następujące błędy.

| Kod błędu | Wiadomość |
| ---------- | ------- |
| `AADB2C90002` | Zasób CORS " {0} " zwrócił element 404, który nie został znaleziony. |
| `AADB2C90006` | Identyfikator URI przekierowania " {0} " podany w żądaniu nie jest zarejestrowany dla identyfikatora klienta " {1} ". |
| `AADB2C90007` | Aplikacja skojarzona z identyfikatorem klienta " {0} " nie ma zarejestrowanych identyfikatorów URI przekierowania. |
| `AADB2C90008` | Żądanie nie zawiera parametru identyfikatora klienta. |
| `AADB2C90010` | Żądanie nie zawiera parametru zakresu. |
| `AADB2C90011` | Identyfikator klienta " {0} " podany w żądaniu nie jest zgodny z identyfikatorem klienta " {1} " zarejestrowanym w zasadach. |
| `AADB2C90012` | Zakres " {0} " podany w żądaniu nie jest obsługiwany. |
| `AADB2C90013` | Żądany typ odpowiedzi " {0} " podany w żądaniu nie jest obsługiwany. |
| `AADB2C90014` | Żądany tryb odpowiedzi " {0} " podany w żądaniu nie jest obsługiwany. |
| `AADB2C90016` | Żądany typ potwierdzenia klienta "" nie jest {0} zgodny z oczekiwanym typem " {1} ". |
| `AADB2C90017` | Potwierdzenie klienta podane w żądaniu jest nieprawidłowe: {0} |
| `AADB2C90018` | Identyfikator klienta " {0} " określony w żądaniu nie jest zarejestrowany w dzierżawie " {1} ". |
| `AADB2C90019` | Kontener kluczy o identyfikatorze " {0} " w dzierżawie " {1} " nie ma prawidłowego klucza. Przyczyna: {2} . |
| `AADB2C90021` | Profil techniczny " {0} " nie istnieje w zasadach " {1} " dzierżawy " {2} ". |
| `AADB2C90022` | Nie można zwrócić metadanych dla zasad " {0} " w dzierżawie " {1} ". |
| `AADB2C90023` | Profil " {0} " nie zawiera wymaganego klucza metadanych " {1} ". |
| `AADB2C90025` | Profil " {0} " w zasadach "" {1} w dzierżawie " {2} " nie zawiera wymaganego klucza kryptograficznego " {3} ". |
| `AADB2C90027` | Poświadczenia podstawowe określone dla elementu " {0} " są nieprawidłowe. Sprawdź, czy poświadczenia są poprawne i czy dostęp został udzielony przez zasób. |
| `AADB2C90028` | Certyfikat klienta określony dla elementu " {0} " jest nieprawidłowy. Sprawdź, czy certyfikat jest prawidłowy, zawiera klucz prywatny i czy dostęp został udzielony przez zasób. |
| `AADB2C90031` | Zasady " {0} " nie określają domyślnej podróży użytkownika. Upewnij się, że zasady lub jej elementy nadrzędne określają domyślną podróż użytkownika w sekcji jednostki uzależnionej. |
| `AADB2C90035` | Usługa jest tymczasowo niedostępna. Spróbuj ponownie za kilka minut. |
| `AADB2C90036` | Żądanie nie zawiera identyfikatora URI, aby przekierować użytkownika do wylogowania. Określ identyfikator URI w polu post_logout_redirect_uri parametru. |
| `AADB2C90037` | Wystąpił błąd podczas przetwarzania żądania. Skontaktuj się z administratorem witryny, do której próbujesz uzyskać dostęp. |
| `AADB2C90039` | Żądanie zawiera potwierdzenie klienta, ale w podanej zasadzie " {0} " w dzierżawie " {1} " brakuje Client_secret w RelyingPartyPolicy. |
| `AADB2C90040` | Podróż użytkownika " {0} " nie zawiera kroku wysyłania oświadczeń. |
| `AADB2C90043` | Monit zawarty w żądaniu zawiera nieprawidłowe wartości. Oczekiwano wartości "none", "login'", "zgody" lub "select_account". |
| `AADB2C90044` | Element Claim "" {0} nie jest obsługiwany przez program rozpoznawania nazw " {1} ". |
| `AADB2C90046` | Mamy problemy z ładowaniem bieżącego stanu. Możesz spróbować uruchomić sesję od początku. |
| `AADB2C90047` | Zasób " {0} " zawiera błędy skryptów uniemożliwiające jego załadowanie. |
| `AADB2C90048` | Na serwerze Wystąpił nieobsługiwany wyjątek. |
| `AADB2C90051` | Nie znaleziono odpowiednich dostawców oświadczeń. |
| `AADB2C90052` | Nieprawidłowa nazwa użytkownika lub hasło. |
| `AADB2C90053` | Nie można znaleźć użytkownika z określonym poświadczeniem. |
| `AADB2C90054` | Nieprawidłowa nazwa użytkownika lub hasło. |
| `AADB2C90055` | Zakres " {0} " podany w żądaniu musi określać zasób, taki jak " https://example.com/calendar.read ". |
| `AADB2C90057` | Podana aplikacja nie jest skonfigurowana tak, aby zezwalać na niejawny przepływ uwierzytelniania OAuth. |
| `AADB2C90058` | Podana aplikacja nie jest skonfigurowana tak, aby zezwalać na publiczne komputery klienckie. |
| `AADB2C90067` | Identyfikator URI przekierowania wylogowania " {0} " ma nieprawidłowy format. Określ adres URL oparty na protokole https, taki jak " https://example.com/return ", lub dla klientów natywnych Użyj natywnego identyfikatora URI "urn: IETF klienta" (IETF: 2.0: OOB). |
| `AADB2C90068` | Podana aplikacja o IDENTYFIKATORze " {0} " nie jest prawidłowa dla tej usługi. Użyj aplikacji utworzonej za pośrednictwem portalu B2C i spróbuj ponownie. |
| `AADB2C90075` | W {0} kroku "" określono wymianę oświadczeń "" {1} , która zwróciła odpowiedź na błąd http z kodem " {2} " i przyczyną " {3} ". |
| `AADB2C90077` | Użytkownik nie ma istniejącej sesji i parametr monitu żądania ma wartość " {0} ". |
| `AADB2C90079` | Klienci muszą wysyłać client_secret podczas realizowania dotacji poufnej. |
| `AADB2C90080` | Podany przydział wygasł. Uwierzytelnij ponownie i spróbuj ponownie. Bieżący czas: {0} , udziel czasu wystawienia: {1} , przyznaj czas wygaśnięcia okna przewijania: {2} . |
| `AADB2C90081` | Określony client_secret nie jest zgodny z oczekiwaną wartością tego klienta. Popraw client_secret i spróbuj ponownie. |
| `AADB2C90083` | W żądaniu brakuje wymaganego parametru: {0} . |
| `AADB2C90084` | Klienci publiczni nie powinni wysyłać client_secret w przypadku zrealizowania publicznie nabytego przydziału. |
| `AADB2C90085` | Usługa napotkała błąd wewnętrzny. Ponownie Uwierzytelnij i spróbuj ponownie. |
| `AADB2C90086` | Podany grant_type [ {0} ] nie jest obsługiwany. |
| `AADB2C90087` | Podany przydział nie został wystawiony dla tej wersji punktu końcowego protokołu. |
| `AADB2C90088` | Podany przydział nie został wystawiony dla tego punktu końcowego. Rzeczywista wartość: {0} i oczekiwana wartość: {1} |
| `AADB2C90091` | Anulowanie przez użytkownika. |
| `AADB2C90092` | Podana aplikacja o IDENTYFIKATORze " {0} " jest wyłączona dla dzierżawy " {1} ". Włącz aplikację i spróbuj ponownie. |
| `AADB2C90107` | Aplikacja o IDENTYFIKATORze " {0} " nie może uzyskać tokenu identyfikatora, ponieważ w żądaniu nie podano zakresu OpenID Connect lub aplikacja nie ma do niej autoryzacji. |
| `AADB2C90108` | Krok aranżacji " {0} " nie określa elementu CpimIssuerTechnicalProfileReferenceId, gdy było oczekiwane. |
| `AADB2C90110` | Parametr Scope musi zawierać element "OpenID Connect" podczas żądania response_type zawierającego element "id_token". |
| `AADB2C90111` | Twoje konto zostało zablokowane. Skontaktuj się z pomocą techniczną, aby ją odblokować, a następnie spróbuj ponownie. |
| `AADB2C90114` | Twoje konto jest tymczasowo zablokowane, aby zapobiec nieautoryzowanemu użyciu. Spróbuj ponownie później. |
| `AADB2C90115` | Podczas żądania response_type "Code" parametr Scope musi zawierać identyfikator zasobu lub klienta dla tokenów dostępu oraz "OpenID Connect" dla tokenów identyfikatorów. Dodatkowo Dołącz "offline_access" do tokenów odświeżania. |
| `AADB2C90117` | Zakres " {0} " podany w żądaniu nie jest obsługiwany. |
| `AADB2C90118` | Użytkownik zapomniał hasła. |
| `AADB2C90120` | Maksymalny parametr wieku " {0} " określony w żądaniu jest nieprawidłowy. Maksymalny wiek musi być liczbą całkowitą z przedziału od " {1} " do "" {2} włącznie. |
| `AADB2C90122` | {0}Nie powiodło się sprawdzenie poprawności żądania HTTP dla danych wejściowych "" odebranych w żądaniu. Upewnij się, że dane wejściowe nie zawierają znaków takich jak < lub &. |
| `AADB2C90128` | Konto skojarzone z tym przyznanym już nie istnieje. Ponownie Uwierzytelnij i spróbuj ponownie. |
| `AADB2C90129` | Podany przydział został odwołany. Ponownie Uwierzytelnij i spróbuj ponownie. |
| `AADB2C90145` | Nie znaleziono niezweryfikowanych numerów telefonów, a zasady nie zezwalają na wprowadzanie numeru przez użytkownika. |
| `AADB2C90146` | Zakres " {0} " podany w żądaniu określa więcej niż jeden zasób dla tokenu dostępu, który nie jest obsługiwany. |
| `AADB2C90149` | {0}Nie można załadować skryptu "". |
| `AADB2C90151` | Użytkownik przekroczył maksymalną liczbę ponownych prób uwierzytelniania wieloskładnikowego. |
| `AADB2C90152` | Żądanie sondy wieloskładnikowej nie może pobrać odpowiedzi z usługi. |
| `AADB2C90154` | Żądanie weryfikacji wieloskładnikowej nie może pobrać identyfikatora sesji z usługi. |
| `AADB2C90155` | Żądanie weryfikacji wieloskładnikowej nie powiodło się z powodu " {0} ". |
| `AADB2C90156` | Żądanie weryfikacji wieloskładnikowej nie powiodło się z powodu " {0} ". |
| `AADB2C90157` | Użytkownik przekroczył maksymalną liczbę ponownych prób dla samodzielnie określonego kroku. |
| `AADB2C90158` | Zakończyło się niepowodzeniem żądanie walidacji z powodu " {0} ". |
| `AADB2C90159` | Niepowodzenie żądania weryfikacji z dowolnego potwierdzenia z powodu " {0} ". |
| `AADB2C90161` | Odpowiedź na własne potwierdzenie nie powiodła się z powodu " {0} ". |
| `AADB2C90165` | Nie można odnaleźć komunikatu inicjującego SAML o identyfikatorze " {0} " w stanie. |
| `AADB2C90168` | Żądanie HTTP-Redirect nie zawiera wymaganego parametru " {0} " dla podpisanego żądania. |
| `AADB2C90178` | Certyfikat podpisywania " {0} " nie ma klucza prywatnego. |
| `AADB2C90182` | Podane code_verifier nie są zgodne ze skojarzonymi code_challenge |
| `AADB2C90183` | Podany code_verifier jest nieprawidłowy |
| `AADB2C90184` | Podane code_challenge_method nie są obsługiwane. Obsługiwane wartości to zwykłe lub S256 |
| `AADB2C90188` | Profil techniczny SAML " {0} " określa adres URL PartnerEntity " {1} ", ale Pobieranie metadanych kończy się niepowodzeniem z powodu " {2} ". |
| `AADB2C90194` | Oświadczenie " {0} " określone dla tokenu okaziciela nie występuje w dostępnych oświadczeniach. Dostępne oświadczenia " {1} ". |
| `AADB2C90205` | Ta aplikacja nie ma wystarczających uprawnień do tego zasobu sieci Web, aby wykonać tę operację. |
| `AADB2C90206` | Przekroczono limit czasu podczas inicjowania klienta. |
| `AADB2C90208` | Podany parametr id_token_hint utracił ważność. Podaj inny token i spróbuj ponownie. |
| `AADB2C90209` | Podany parametr id_token_hint nie zawiera zaakceptowanych odbiorców. Prawidłowe wartości odbiorców: " {0} ". Podaj inny token i spróbuj ponownie. |
| `AADB2C90210` | Nie można sprawdzić poprawności podanego parametru id_token_hint. Podaj inny token i spróbuj ponownie. |
| `AADB2C90211` | Żądanie zawierało plik cookie niekompletnego stanu. |
| `AADB2C90212` | Żądanie zawierało nieprawidłowy plik cookie stanu. |
| `AADB2C90220` | Kontener kluczy w dzierżawie " {0} " o identyfikatorze magazynu " {1} " istnieje, ale nie zawiera prawidłowego certyfikatu. Certyfikat mógł wygasnąć lub certyfikat może być aktywny w przyszłości (NBF). |
| `AADB2C90223` | Wystąpił błąd podczas oczyszczania zasobu CORS. |
| `AADB2C90224` | Nie włączono przepływu właściciela zasobów dla aplikacji. |
| `AADB2C90225` | Nazwa użytkownika lub hasło podane w żądaniu są nieprawidłowe. |
| `AADB2C90226` | Określona wymiana tokenów jest obsługiwana tylko w przypadku POST protokołu HTTP. |
| `AADB2C90232` | Podany parametr id_token_hint nie zawiera zaakceptowanego wystawcy. Prawidłowi emitenci: " {0} ". Podaj inny token i spróbuj ponownie. |
| `AADB2C90233` | Sprawdzanie poprawności sygnatury podanego parametru id_token_hint nie powiodło się. Podaj inny token i spróbuj ponownie. |
| `AADB2C90235` | Podany id_token utracił ważność. Podaj inny token i spróbuj ponownie. |
| `AADB2C90237` | Podany id_token nie zawiera prawidłowych odbiorców. Prawidłowe wartości odbiorców: " {0} ". Podaj inny token i spróbuj ponownie. |
| `AADB2C90238` | Podany id_token nie zawiera prawidłowego wystawcy. Prawidłowe wartości wystawcy: " {0} ". Podaj inny token i spróbuj ponownie. |
| `AADB2C90239` | Podano niepowodzenie walidacji podpisu dla podanej id_token. Podaj inny token i spróbuj ponownie. |
| `AADB2C90240` | Podane id_token są źle sformułowane i nie można go przeanalizować. Podaj inny token i spróbuj ponownie. |
| `AADB2C90242` | Profil techniczny SAML " {0} " określa PARTNERENTITY CDATA, której nie można załadować z powodu przyczyny " {1} ". |
| `AADB2C90243` | Klucz/wpis tajny dostawcy tożsamości klienta nie został poprawnie skonfigurowany. |
| `AADB2C90244` | W tej chwili jest zbyt wiele żądań. Poczekaj chwilę i spróbuj ponownie. |
| `AADB2C90248` | Przepływu właściciela zasobów można używać tylko w przypadku aplikacji utworzonych za pomocą portalu administracyjnego B2C. |
| `AADB2C90250` | Ogólny punkt końcowy logowania nie jest obsługiwany. |
| `AADB2C90255` | Wymiana oświadczeń określona w profilu technicznym " {0} " nie została ukończona zgodnie z oczekiwaniami. Możesz spróbować uruchomić sesję od początku. |
| `AADB2C90261` | Podczas wymiany oświadczeń " {0} " określonej w kroku " {1} " została zwrócona odpowiedź dotycząca błędu HTTP, której nie można przeanalizować. |
| `AADB2C90272` | W żądaniu nie został określony parametr id_token_hint. Podaj token i spróbuj ponownie. |
| `AADB2C90273` | Odebrano nieprawidłową odpowiedź: " {0} " |
| `AADB2C90274` | Metadane dostawcy nie określają usługi wylogowania jednokrotnego lub powiązanie punktu końcowego nie jest jednym z wartości "urn: języka Oasis: names: TC: SAML: 2.0: bindings: HTTP-Redirect" lub "urn: języka Oasis: names: TC: SAML: 2.0: bindings: HTTP-POST". |
| `AADB2C90276` | Żądanie jest niespójne z ustawieniem kontroli " {0} ": " {1} " w profilu technicznym " {2} " dla {3} dzierżawy "" {4} . |
| `AADB2C90277` | Krok aranżacji " {0} " podróży użytkownika "" {1} zasad " {2} " nie zawiera odwołania do definicji zawartości. |
| `AADB2C90279` | Podany identyfikator klienta "" nie jest {0} zgodny z identyfikatorem klienta, który wystawił przyznanie. |
| `AADB2C90284` | Aplikacja o identyfikatorze " {0} " nie została udzielona zgoda i nie można jej użyć dla kont lokalnych. |
| `AADB2C90285` | Nie znaleziono aplikacji o identyfikatorze ' {0} '. |
| `AADB2C90288` | UserJourney z identyfikatorem " {0} ", do którego odwołuje się element profilu technicznym " {1} " dla dzierżawy tokenów odświeżania " {2} " nie istnieje w zasadach " {3} " ani w żadnej z jej zasad podstawowych. |
| `AADB2C90289` | Wystąpił błąd podczas nawiązywania połączenia z dostawcą tożsamości. Spróbuj ponownie później. |
| `AADB2C90296` | Aplikacja nie została poprawnie skonfigurowana. Skontaktuj się z administratorem witryny, do której próbujesz uzyskać dostęp. |
| `AADB2C99005` | Żądanie zawiera nieprawidłowy parametr zakresu, który zawiera niedozwolony znak " {0} ". |
| `AADB2C99006` | Azure AD B2C nie może znaleźć aplikacji rozszerzenia o identyfikatorze aplikacji " {0} ". https://go.microsoft.com/fwlink/?linkid=851224Aby uzyskać więcej informacji, odwiedź stronę. |
| `AADB2C99011` | Wartość metadanych " {0} " nie została określona w profilu technicznym " {1} " w zasadach " {2} ". |
| `AADB2C99013` | Podana kombinacja grant_type [ {0} ] i TOKEN_TYPE [ {1} ] nie jest obsługiwana. |
| `AADB2C99015` | W profilu " {0} " w ramach zasad "" {1} w dzierżawie " {2} " brakuje wszystkich InputClaims wymaganych dla przepływu poświadczeń hasła właściciela zasobu. |
