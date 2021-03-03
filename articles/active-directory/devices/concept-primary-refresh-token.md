---
title: Podstawowy token odświeżania (PRT) i usługa Azure AD — Azure Active Directory
description: Co to jest rola i jak zarządzamy podstawowym tokenem odświeżania (PRT) w Azure Active Directory?
services: active-directory
ms.service: active-directory
ms.subservice: devices
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: ravenn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 46cc8ef1158c02190f905cbe8eb1d12ea7be50a2
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101644939"
---
# <a name="what-is-a-primary-refresh-token"></a>Co to jest podstawowy token odświeżania?

Podstawowy token odświeżania (PRT) to kluczowy artefakt uwierzytelniania usługi Azure AD w systemie Windows 10, Windows Server 2016 i nowszych wersjach, urządzeniach z systemem iOS i Android. Jest to token sieci Web JSON (JWT) specjalnie wystawiony dla brokerów tokenów firmy Microsoft, aby umożliwić Logowanie jednokrotne w aplikacjach używanych na tych urządzeniach. W tym artykule przedstawiono szczegółowe informacje na temat sposobu wystawiania, używania i ochrony PRT na urządzeniach z systemem Windows 10.

W tym artykule przyjęto założenie, że znasz już inne Stany urządzeń dostępne w usłudze Azure AD oraz sposób działania logowania jednokrotnego w systemie Windows 10. Aby uzyskać więcej informacji o urządzeniach w usłudze Azure AD, zobacz artykuł [co to jest zarządzanie urządzeniami w usłudze Azure Active Directory?](overview.md)

## <a name="key-terminology-and-components"></a>Najważniejsze Terminologia i składniki

Poniższe składniki systemu Windows odgrywają kluczową rolę w żądaniu i przy użyciu PRT:

* **Dostawca uwierzytelniania w chmurze** (CloudAP): CloudAP to nowoczesnego dostawcę uwierzytelniania dla systemu Windows, który weryfikuje użytkowników logujących się na urządzeniu z systemem Windows 10. CloudAP udostępnia strukturę wtyczek, którą dostawcy tożsamości mogą skompilować, aby umożliwić uwierzytelnianie w systemie Windows przy użyciu poświadczeń tego dostawcy tożsamości.
* **Menedżer kont sieci Web** (wam): wam jest domyślnym brokerem tokenów na urządzeniach z systemem Windows 10. WAM oferuje również strukturę wtyczek, którą dostawcy tożsamości mogą skompilować, i włączyć logowanie jednokrotne do swoich aplikacji polegających na tym dostawcy tożsamości.
* **Wtyczka usługi Azure AD CloudAP**: określona Wtyczka usługi Azure AD oparta na platformie CloudAP, która weryfikuje poświadczenia użytkownika w usłudze Azure AD podczas logowania do systemu Windows.
* **Wtyczka usługi Azure AD wam**: wtyczka specyficzna dla usługi Azure AD oparta na ARCHITEKTURZE Menedżera wam, która umożliwia logowanie jednokrotne do aplikacji korzystających z usługi Azure AD na potrzeby uwierzytelniania.
* **Dsreg**: specyficzny dla systemu Windows 10 składnik usługi Azure AD, który obsługuje proces rejestracji urządzeń dla wszystkich stanów urządzeń.
* **Trusted Platform Module** (TPM): moduł TPM to składnik sprzętowy wbudowany w urządzenie, który zapewnia funkcje zabezpieczeń oparte na sprzęcie dla wpisów tajnych użytkowników i urządzeń. Więcej informacji można znaleźć w artykule [Trusted Platform Module Omówienie technologii](/windows/security/information-protection/tpm/trusted-platform-module-overview).

## <a name="what-does-the-prt-contain"></a>Co zawiera PRT?

PRT zawiera oświadczenia ogólnie zawarte w dowolnym tokenie odświeżania usługi Azure AD. Ponadto w PRT są zawarte pewne oświadczenia specyficzne dla urządzenia. Są one następujące:

* **Identyfikator urządzenia**: PRT jest wystawiany dla użytkownika na określonym urządzeniu. Identyfikator urządzenia `deviceID` określa urządzenie, na którym został wystawiony PRT użytkownika. To zgłoszenie jest następnie wystawione dla tokenów uzyskanych za pośrednictwem PRT. Identyfikator urządzenia jest używany do określania autoryzacji dostępu warunkowego na podstawie stanu urządzenia lub zgodności.
* **Klucz sesji**: klucz sesji to zaszyfrowany klucz symetryczny generowany przez usługę uwierzytelniania usługi Azure AD, wystawiony jako część PRT. Klucz sesji pełni rolę dowodu posiadania, gdy PRT jest używany do uzyskania tokenów dla innych aplikacji.

### <a name="can-i-see-whats-in-a-prt"></a>Czy mogę zobaczyć, co znajduje się w PRT?

PRT to nieprzezroczysty obiekt BLOB wysyłany z usługi Azure AD, którego zawartość nie jest znana żadnym składnikom klienta. Nie możesz zobaczyć, co znajduje się wewnątrz PRT.

## <a name="how-is-a-prt-issued"></a>Jak jest wystawiony PRT?

Rejestracja urządzeń jest wymaganiem wstępnym w przypadku uwierzytelniania opartego na urządzeniach w usłudze Azure AD. PRT jest wystawiany dla użytkowników tylko na zarejestrowanych urządzeniach. Aby uzyskać bardziej szczegółowe informacje na temat rejestracji urządzeń, zobacz artykuł [Windows Hello dla firm i rejestracja urządzeń](/windows/security/identity-protection/hello-for-business/hello-how-it-works-device-registration). Podczas rejestracji urządzenia składnik dsreg generuje dwa zestawy par kluczy kryptograficznych:

* Klucz urządzenia (dkpub/dkpriv)
* Klucz transportu (tkpub/tkpriv)

Klucze prywatne są powiązane z modułem TPM urządzenia, jeśli urządzenie ma prawidłowy i działający moduł TPM, podczas gdy klucze publiczne są wysyłane do usługi Azure AD podczas procesu rejestracji urządzenia. Te klucze są używane do sprawdzania poprawności stanu urządzenia podczas żądań PRT.

PRT jest wystawiany podczas uwierzytelniania użytkownika na urządzeniu z systemem Windows 10 w dwóch scenariuszach:

* Przyłączone do usługi **Azure AD** lub **hybrydowe usługi Azure AD**: element PRT jest wystawiany podczas logowania do systemu Windows, gdy użytkownik zaloguje się przy użyciu poświadczeń organizacji. PRT jest wystawiany ze wszystkimi obsługiwanymi poświadczeniami systemu Windows 10, na przykład hasło i funkcja Windows Hello dla firm. W tym scenariuszu Wtyczka usługi Azure AD CloudAP jest podstawowym urzędem dla PRT.
* **Zarejestrowane urządzenie usługi Azure AD**: PRT jest wystawiane, gdy użytkownik dodaje dodatkowe konto służbowe do urządzenia z systemem Windows 10. Użytkownicy mogą dodawać konta do systemu Windows 10 na dwa różne sposoby —  
   * Dodawanie konta za pomocą **tego konta wszędzie na tym urządzeniu** po zalogowaniu się do aplikacji (na przykład Outlook)
   * Dodawanie konta z **ustawień**  >  **konta**  >  **dostęp do służbowego**  >  **połączenia**

W scenariuszach zarejestrowanych urządzeń w usłudze Azure AD Wtyczka Menedżera WAM usługi Azure AD jest głównym urzędem dla PRT, ponieważ logowanie systemu Windows nie odbywa się przy użyciu tego konta usługi Azure AD.

> [!NOTE]
> dostawcy tożsamości innych firm muszą obsługiwać protokół WS-Trust, aby umożliwić wydawanie PRT na urządzeniach z systemem Windows 10. Bez protokołu WS-Trust PRT nie może zostać wystawiony dla użytkowników z dołączoną hybrydą usługi Azure AD lub urządzeniami przyłączonymi do usługi Azure AD. W przypadku usług ADFS wymagane są tylko punkty końcowe usernamemixed. Usługi ADFS/Services/Trust/2005/windowstransport i ADFS/Services/Trust/13/windowstransport powinny być włączone tylko jako punkty końcowe dostępne dla intranetu i **nie mogą być udostępniane** jako punkty końcowe do ekstranetu za pośrednictwem serwera proxy aplikacji sieci Web

## <a name="what-is-the-lifetime-of-a-prt"></a>Jaki jest okres istnienia PRT?

Po wydaniu PRT jest ważny przez 14 dni i ciągle jest odnawiany, o ile użytkownik aktywnie korzysta z tego urządzenia.  

## <a name="how-is-a-prt-used"></a>Jak jest używane PRT?

PRT jest używany przez dwa kluczowe składniki w systemie Windows:

* **Wtyczka usługi Azure AD CloudAP**: podczas logowania do systemu Windows Wtyczka usługi Azure AD CLOUDAP żąda PRT z usługi Azure AD przy użyciu poświadczeń dostarczonych przez użytkownika. W pamięci podręcznej PRT jest również włączone logowanie buforowane, gdy użytkownik nie ma dostępu do połączenia z Internetem.
* **Wtyczka usługi Azure AD wam**: gdy użytkownicy próbują uzyskać dostęp do aplikacji, Wtyczka usługi Azure AD wam używa PRT, aby włączyć logowanie jednokrotne w systemie Windows 10. Wtyczka usługi Azure AD WAM używa PRT, aby zażądać tokenów odświeżania i dostępu dla aplikacji korzystających z Menedżera WAM dla żądań tokenów. Umożliwia także Logowanie jednokrotne w przeglądarkach przez wstrzyknięcie PRT do żądań przeglądarki. Logowanie jednokrotne przeglądarki w systemie Windows 10 jest obsługiwane w przeglądarce Microsoft Edge (natywnie) i Chrome (za pośrednictwem [kont systemu Windows 10](https://chrome.google.com/webstore/detail/windows-10-accounts/ppnbnpeolgkicgegkbkbjmhlideopiji?hl=en) lub rozszerzeń usługi [Office Online](https://chrome.google.com/webstore/detail/office/ndjpnladcallmjemlbaebfadecfhkepb?hl=en) ).

## <a name="how-is-a-prt-renewed"></a>Jak jest odnawiana PRT?

PRT jest odnawiany w dwóch różnych metodach:

* **Wtyczka usługi Azure AD CloudAP co 4 godziny**: wtyczka CLOUDAP odnawia PRT co 4 godziny podczas logowania do systemu Windows. Jeśli użytkownik nie ma połączenia z Internetem w tym czasie, wtyczka CloudAP odnowi PRT, gdy urządzenie jest połączone z Internetem.
* **Wtyczka usługi Azure AD wam podczas żądań tokenów aplikacji**: Wtyczka Menedżera wam umożliwia logowanie jednokrotne na urządzeniach z systemem Windows 10 przez włączenie żądań tokenów dyskretnych dla aplikacji. Wtyczka Menedżera WAM może odnowić PRT podczas tych żądań tokenów na dwa różne sposoby:
   * Aplikacja żąda WAM dla tokenu dostępu dyskretnie, ale nie ma dostępnego tokenu odświeżania dla tej aplikacji. W takim przypadku WAM używa PRT do żądania tokenu dla aplikacji i pobiera nowy PRT w odpowiedzi.
   * Aplikacja żąda WAM dla tokenu dostępu, ale PRT jest nieprawidłowa lub usługa Azure AD wymaga dodatkowej autoryzacji (na przykład Multi-Factor Authentication usługi Azure AD). W tym scenariuszu jednostka WAM inicjuje Logowanie interakcyjne wymagające ponownego uwierzytelnienia użytkownika lub zapewnienia dodatkowej weryfikacji, a nowe PRT są wydawane po pomyślnym uwierzytelnieniu.

W środowisku usług AD FS bezpośredni wiersz wglądu do kontrolera domeny nie jest wymagany do odnowienia PRT. Odnowienie PRT wymaga włączenia tylko/ADFS/Services/Trust/2005/usernamemixed i/ADFS/Services/Trust/13/usernamemixed punktów końcowych na serwerze proxy przy użyciu protokołu WS-Trust.

Punkty końcowe transportu systemu Windows są wymagane do uwierzytelniania hasła tylko w przypadku zmiany hasła, a nie do odnawiania PRT.

### <a name="key-considerations"></a>Najważniejsze zagadnienia

* PRT jest wystawiane i odnawiane tylko podczas natywnego uwierzytelniania aplikacji. Element PRT nie jest odnawiany ani wystawiany podczas sesji przeglądarki.
* W usłudze Azure AD przyłączonych i hybrydowych urządzeniach z usługą Azure AD, wtyczka CloudAP jest podstawowym urzędem dla PRT. Jeśli PRT jest odnawiany podczas żądania tokenu opartego na procesorze WAM, PRT jest wysyłana z powrotem do wtyczki CloudAP, która weryfikuje prawidłowość PRT w usłudze Azure AD przed jej zaakceptowaniem.

## <a name="how-is-the-prt-protected"></a>Jak jest chronione PRT?

PRT jest chroniony przez powiązanie go z urządzeniem, do którego użytkownik zalogował się. Usługa Azure AD i system Windows 10 umożliwiają ochronę PRT przy użyciu następujących metod:

* **Podczas pierwszego logowania**: podczas pierwszego logowania PRT jest wystawiane przez żądania podpisywania przy użyciu klucza urządzenia, który został wygenerowany kryptograficznie podczas rejestracji urządzenia. Na urządzeniu z prawidłowym i działającym modułem TPM klucz urządzenia jest zabezpieczony przez moduł TPM zapobiegający złośliwemu dostępowi. PRT nie zostanie wystawiony, jeśli nie można zweryfikować odpowiedniej sygnatury klucza urządzenia.
* **Podczas żądania tokenu i odnawiania**: po wystawieniu PRT usługa Azure AD wygeneruje również zaszyfrowany klucz sesji na urządzeniu. Jest on szyfrowany za pomocą publicznego klucza transportowego (tkpub) wygenerowanego i wysyłanego do usługi Azure AD w ramach rejestracji urządzeń. Ten klucz sesji może zostać odszyfrowany tylko przez prywatny klucz transportu (tkpriv) zabezpieczony przez moduł TPM. Klucz sesji to klucz dowodu posiadania (POP) dla wszystkich żądań wysyłanych do usługi Azure AD.  Klucz sesji jest również chroniony przez moduł TPM i żaden inny składnik systemu operacyjnego nie może uzyskać do niego dostępu. Żądania tokenów lub żądania odnowienia PRT są bezpiecznie podpisane przez ten klucz sesji za pomocą modułu TPM, dlatego nie mogą być modyfikowane za pomocą programu. Usługa Azure AD unieważnia wszystkie żądania z urządzenia, które nie są podpisane przy użyciu odpowiedniego klucza sesji.

Zabezpieczając te klucze przy użyciu modułu TPM, ulepszamy zabezpieczenia PRT ze złośliwych aktorów próbujących ukraść klucze lub powtórzyć PRT.  Dlatego użycie modułu TPM znacznie podnosi poziom bezpieczeństwa przyłączonych do usługi Azure AD, dołączono hybrydową usługę Azure AD i zarejestrowane urządzenia usługi Azure AD przed kradzieżą poświadczeń. W przypadku wydajności i niezawodności zalecaną wersją dla wszystkich scenariuszy rejestracji urządzeń usługi Azure AD w systemie Windows 10 jest moduł TPM 2,0. Począwszy od systemu Windows 10, 1903 Update, usługa Azure AD nie używa modułu TPM 1,2 dla żadnego z powyższych kluczy ze względu na problemy z niezawodnością. 

### <a name="how-are-app-tokens-and-browser-cookies-protected"></a>Jak są chronione tokeny aplikacji i pliki cookie przeglądarki?

**Tokeny aplikacji**: gdy aplikacja żąda tokenu za pomocą Menedżera wam, usługa Azure AD wystawia token odświeżania i token dostępu. Jednak tylko firma WAM zwraca token dostępu do aplikacji i zabezpiecza token odświeżania w swojej pamięci podręcznej, szyfrując go za pomocą klucza programowania aplikacji ochrony danych (DPAPI) użytkownika. Firma WAM bezpiecznie używa tokenu odświeżania przez zażądanie podpisania przy użyciu klucza sesji, aby wystawić dalsze tokeny dostępu. Klucz DPAPI jest zabezpieczony przez klucz symetryczny oparty na usłudze Azure AD w samej usłudze Azure AD. Gdy urządzenie musi odszyfrować profil użytkownika przy użyciu klucza DPAPI, usługa Azure AD udostępnia klucz DPAPI zaszyfrowany przez klucz sesji, który CloudAP wtyczki do odszyfrowania. Ta funkcja zapewnia spójność zabezpieczania tokenów odświeżania i eliminuje aplikacje implementujące własne mechanizmy ochrony.  

**Pliki cookie przeglądarki**: w systemie Windows 10 usługa Azure AD obsługuje logowanie jednokrotne w przeglądarce Internet Explorer i Microsoft Edge natywnie lub w Google Chrome za pomocą rozszerzenia kont systemu Windows 10. Zabezpieczenia są tworzone nie tylko w celu ochrony plików cookie, ale również punktów końcowych, do których są wysyłane pliki cookie. Pliki cookie przeglądarki są chronione w taki sam sposób, jak PRT, przy użyciu klucza sesji do podpisywania i ochrony plików cookie.

Gdy użytkownik inicjuje interakcję z przeglądarką, przeglądarka (lub rozszerzenie) wywołuje natywnego hosta klienta COM. Natywny Host klienta zapewnia, że strona pochodzi z jednej z dozwolonych domen. Przeglądarka może wysłać inne parametry do natywnego hosta klienta, w tym identyfikatora jednorazowego, ale natywny Host klienta gwarantuje weryfikację nazwy hosta. Natywny Host klienta żąda PRT-cookie z wtyczki CloudAP, która tworzy i podpisuje go przy użyciu klucza sesji chronionego przez moduł TPM. Ponieważ plik PRT-cookie jest podpisywany przez klucz sesji, jest bardzo trudne do manipulowania. Ten PRT-cookie jest zawarty w nagłówku żądania dla usługi Azure AD w celu sprawdzenia poprawności urządzenia, z którego pochodzi. W przypadku korzystania z przeglądarki Chrome tylko rozszerzenie jawnie zdefiniowane w manifeście natywnego hosta klienta może wywoływać te żądania, uniemożliwiając dowolnych rozszerzeń. Gdy usługa Azure AD zweryfikuje plik cookie PRT, wysyła plik cookie sesji do przeglądarki. Ten plik cookie sesji zawiera również ten sam klucz sesji, który został wystawiony przez PRT. Podczas kolejnych żądań klucz sesji jest weryfikowany w sposób efektywny powiązany z plikiem cookie na urządzeniu i uniemożliwia odtwarzanie z innego miejsca.

## <a name="when-does-a-prt-get-an-mfa-claim"></a>Kiedy usługa PRT Pobiera wystąpienie usługi MFA?

PRT może uzyskać żądania uwierzytelniania wieloskładnikowego (MFA) w określonych scenariuszach. Gdy PRT oparty na usłudze MFA jest używana do żądania tokenów dla aplikacji, żądanie MFA jest przesyłane do tych tokenów aplikacji. Ta funkcja zapewnia bezproblemowe środowisko dla użytkowników, uniemożliwiając wyzwanie usługi MFA dla każdej aplikacji, która go wymaga. PRT może uzyskać żądania MFA w następujący sposób:

* **Logowanie za pomocą usługi Windows Hello dla firm**: funkcja Windows Hello dla firm zastępuje hasła i używa kluczy kryptograficznych w celu zapewnienia silnego uwierzytelniania dwuskładnikowego. Funkcja Windows Hello dla firm jest specyficzna dla użytkownika na urządzeniu i sama wymaga obsługi usługi MFA. Gdy użytkownik loguje się przy użyciu usługi Windows Hello dla firm, PRT użytkownika pobiera w ramach usługi MFA. Ten scenariusz dotyczy również użytkowników logujących się przy użyciu kart inteligentnych w przypadku, gdy uwierzytelnianie przy użyciu karty inteligentnej generuje takie zdarzenie z usług AD FS.
   * Ponieważ funkcja Windows Hello dla firm jest uważana za usługę uwierzytelniania wieloskładnikowego, to w przypadku odświeżenia PRT zostanie ona zaktualizowana, a więc czas trwania usługi MFA zostanie automatycznie rozbudowany, gdy użytkownicy logują się za pomocą usługi WIndows Hello dla firm
* Uwierzytelnianie **wieloskładnikowe podczas logowania interaktywnego Menedżera wam**: podczas żądania tokenu za pomocą Menedżera wam, jeśli użytkownik musi wykonać uwierzytelnianie wieloskładnikowe w celu uzyskania dostępu do aplikacji, PRT, który jest odnawiany podczas tej interakcji, jest wydrukowany z użyciem żądania MFA.
   * W takim przypadku nie jest ono ciągle aktualizowane, więc czas trwania usługi MFA jest oparty na okresie istnienia ustawionym dla katalogu.
   * Gdy poprzedni istniejący PRT i RT są używane w celu uzyskania dostępu do aplikacji, PRT i RT będą traktowane jako pierwsze potwierdzenie uwierzytelniania. Nowe wystąpienie będzie wymagane z drugim potwierdzeniem i wydrukowaniem roszczeń usługi MFA. Spowoduje to również wydanie nowych PRT i RT.
* Uwierzytelnianie **wieloskładnikowe podczas rejestracji urządzenia**: Jeśli administrator skonfigurował ustawienia urządzenia w usłudze Azure AD, aby [wymagać uwierzytelniania wieloskładnikowego w celu zarejestrowania urządzeń](device-management-azure-portal.md#configure-device-settings), użytkownik musi wykonać uwierzytelnianie wieloskładnikowe, aby zakończyć rejestrację. W trakcie tego procesu PRT wystawiony dla użytkownika ma odebrane przez usługę MFA w trakcie rejestracji. Ta funkcja ma zastosowanie tylko do użytkownika, który wykonał operację join, a nie do innych użytkowników logujących się na tym urządzeniu.
   * Podobnie jak w przypadku logowania interaktywnego w programie WAM, nie jest ono aktualizowane w sposób ciągły, więc czas trwania usługi MFA jest oparty na okresie istnienia ustawionym dla katalogu.

System Windows 10 utrzymuje podzielona na partycje listę PRTs dla każdego poświadczenia. W związku z tym istnieje PRT dla każdej usługi Windows Hello dla firm, hasła lub karty inteligentnej. Takie partycjonowanie gwarantuje, że oświadczenia usługi MFA są izolowane na podstawie użytych poświadczeń i nie są mieszane podczas żądań tokenów.

## <a name="how-is-a-prt-invalidated"></a>W jaki sposób PRT jest unieważniony?

PRT jest unieważniony w następujących scenariuszach:

* **Nieprawidłowy użytkownik**: Jeśli użytkownik został usunięty lub wyłączony w usłudze Azure AD, ich PRT są unieważnione i nie można go użyć do uzyskania tokenów dla aplikacji. Jeśli usunięty lub wyłączony użytkownik został już zalogowany na urządzeniu przed logowaniem w pamięci podręcznej, logowanie do nich zostanie zarejestrowane w usłudze do momentu, gdy CloudAP zostanie powiadomiony o ich nieprawidłowym stanie. Gdy CloudAP ustali, że użytkownik jest nieprawidłowy, zablokuje kolejne logowania. Nieprawidłowy użytkownik jest automatycznie blokowany przed zalogowaniem się do nowych urządzeń, które nie mają zbuforowanych poświadczeń.
* **Nieprawidłowe urządzenie**: Jeśli urządzenie zostało usunięte lub wyłączone w usłudze Azure AD, PRT uzyskany na tym urządzeniu jest unieważniony i nie można go użyć do uzyskania tokenów dla innych aplikacji. Jeśli użytkownik jest już zalogowany na nieprawidłowym urządzeniu, może kontynuować to działanie. Ale wszystkie tokeny na urządzeniu są unieważnione i użytkownik nie ma logowania jednokrotnego do żadnych zasobów z tego urządzenia.
* **Zmiana hasła**: po zmianie hasła przez użytkownika PRT uzyskany przy poprzednim haśle jest unieważniony przez usługę Azure AD. W wyniku zmiany hasła użytkownik otrzymuje nowe PRT. To unieważnienie może wystąpić na dwa różne sposoby:
   * Jeśli użytkownik zaloguje się do systemu Windows przy użyciu nowego hasła, CloudAP odrzuci stare PRT i zażąda od usługi Azure AD nowego PRT z nowym hasłem. Jeśli użytkownik nie ma połączenia z Internetem, nie można zweryfikować nowego hasła, system Windows może wymagać od użytkownika wprowadzenia starego hasła.
   * Jeśli użytkownik zalogował się przy użyciu starego hasła lub zmienił hasło po zalogowaniu się do systemu Windows, stary PRT jest używany w przypadku wszystkich żądań tokenów opartych na WAM. W tym scenariuszu użytkownik jest monitowany o ponowne uwierzytelnienie w trakcie żądania tokenu Menedżera WAM i wystawienie nowego PRT.
* **Problemy z modułem TPM**: czasami moduł TPM urządzenia może Falter lub kończyć się niepowodzeniem, co prowadzi do niedostępności kluczy zabezpieczonych przez moduł TPM. W takim przypadku urządzenie nie może uzyskać PRT lub żądać tokenów korzystających z istniejących PRT, ponieważ nie można udowodnić posiadania kluczy kryptograficznych. W związku z tym wszystkie istniejące PRT są unieważnione przez usługę Azure AD. Gdy system Windows 10 wykryje błąd, inicjuje przepływ odzyskiwania, aby ponownie zarejestrować urządzenie przy użyciu nowych kluczy kryptograficznych. Przy użyciu hybrydowego sprzężenia usługi Azure AD, podobnie jak rejestracja początkowa, odzyskiwanie odbywa się w trybie dyskretnym bez wprowadzania danych przez użytkownika. W przypadku urządzeń z usługą Azure AD dołączonymi lub zarejestrowanymi w usłudze Azure AD odzyskiwanie musi być wykonywane przez użytkownika, który ma uprawnienia administratora na urządzeniu. W tym scenariuszu przepływ odzyskiwania jest inicjowany przez wiersz systemu Windows, który prowadzi użytkownika do pomyślnego odzyskania urządzenia.

## <a name="detailed-flows"></a>Szczegółowe przepływy

Na poniższych diagramach przedstawiono podstawowe informacje dotyczące wydawania, odnawiania i korzystania z PRT w celu żądania tokenu dostępu do aplikacji. Ponadto te kroki opisują także sposób stosowania wyżej wymienionych mechanizmów zabezpieczeń podczas tych interakcji.

### <a name="prt-issuance-during-first-sign-in"></a>Wystawianie PRT podczas pierwszego logowania

![PRT wystawianie podczas pierwszego logowania w szczegółowym przepływie](./media/concept-primary-refresh-token/prt-initial-sign-in.png)

> [!NOTE]
> W przypadku urządzeń przyłączonych do usługi Azure AD ta wymiana odbywa się synchronicznie, aby wystawić PRT, zanim użytkownik będzie mógł zalogować się do systemu Windows. W przypadku urządzeń dołączonych do hybrydowej usługi Azure AD jest to główny urząd Active Directory. W związku z tym użytkownik czeka tylko na uzyskanie biletu TGT do logowania, podczas gdy wystawianie PRT odbywa się asynchronicznie. Ten scenariusz nie dotyczy zarejestrowanych urządzeń usługi Azure AD, ponieważ logowanie nie korzysta z poświadczeń usługi Azure AD.

| Krok | Opis |
| :---: | --- |
| A | Użytkownik wprowadza swoje hasło w interfejsie użytkownika logowania. LogonUI przekazuje poświadczenia w buforze uwierzytelniania do urzędu LSA, który w tym przypadku przekazuje go wewnętrznie do CloudAP. CloudAP przekazuje to żądanie do wtyczki CloudAP. |
| B | Wtyczka CloudAP inicjuje żądanie odnajdywania obszaru, aby zidentyfikować dostawcę tożsamości dla użytkownika. Jeśli dzierżawa użytkownika ma konfigurację dostawcy Federacji, usługa Azure AD zwraca punkt końcowy wymiany metadanych dostawcy federacyjnego (MEX). W przeciwnym razie usługa Azure AD zwróci informację o tym, że użytkownik jest zarządzany, wskazując, że użytkownik może uwierzytelniać się w usłudze Azure AD. |
| C | Jeśli użytkownik jest zarządzany, CloudAP uzyska identyfikator jednorazowy z usługi Azure AD. Jeśli użytkownik jest federacyjny, wtyczka CloudAP żąda tokenu SAML od dostawcy federacyjnego z poświadczeniami użytkownika. Po odebraniu token SAML żąda identyfikatora nonce z usługi Azure AD. |
| D | Wtyczka CloudAP konstruuje żądanie uwierzytelnienia przy użyciu poświadczeń użytkownika, identyfikatora nonce i zakresu brokera, podpisuje żądanie przy użyciu klucza urządzenia (dkpriv) i wysyła je do usługi Azure AD. W środowisku federacyjnym wtyczka CloudAP używa tokenu SAML zwróconego przez dostawcę federacyjnego zamiast poświadczeń użytkownika. |
| E | Usługa Azure AD weryfikuje poświadczenia użytkownika, identyfikator jednorazowy oraz sygnaturę urządzenia, sprawdza, czy urządzenie jest prawidłowe w dzierżawie i wystawia zaszyfrowane PRT. Wraz z PRT usługa Azure AD wystawia klucz symetryczny, nazywany kluczem sesji zaszyfrowanym przez usługę Azure AD przy użyciu klucza transportowego (tkpub). Ponadto klucz sesji jest również osadzony w PRT. Ten klucz sesji działa jako klucz dowodu posiadania (PoP) dla kolejnych żądań z PRT. |
| F | Wtyczka CloudAP przekazuje zaszyfrowane PRT i klucz sesji do CloudAP. CloudAP Zażądaj modułu TPM do odszyfrowania klucza sesji przy użyciu klucza transportowego (tkpriv), a następnie Zaszyfruj go przy użyciu własnego klucza modułu TPM. CloudAP przechowuje zaszyfrowany klucz sesji w pamięci podręcznej wraz z PRT. |

### <a name="prt-renewal-in-subsequent-logons"></a>PRT odnowienie w przypadku kolejnych logowań

![PRT odnowienie w przypadku kolejnych logowań](./media/concept-primary-refresh-token/prt-renewal-subsequent-logons.png)

| Krok | Opis |
| :---: | --- |
| A | Użytkownik wprowadza swoje hasło w interfejsie użytkownika logowania. LogonUI przekazuje poświadczenia w buforze uwierzytelniania do urzędu LSA, który w tym przypadku przekazuje go wewnętrznie do CloudAP. CloudAP przekazuje to żądanie do wtyczki CloudAP. |
| B | Jeśli użytkownik zalogował się wcześniej do użytkownika, system Windows inicjuje logowanie w pamięci podręcznej i sprawdza poprawność poświadczeń w celu zarejestrowania użytkownika w programie. Co 4 godziny wtyczka CloudAP inicjuje proces odnawiania PRT asynchronicznie. |
| C | Wtyczka CloudAP inicjuje żądanie odnajdywania obszaru, aby zidentyfikować dostawcę tożsamości dla użytkownika. Jeśli dzierżawa użytkownika ma konfigurację dostawcy Federacji, usługa Azure AD zwraca punkt końcowy wymiany metadanych dostawcy federacyjnego (MEX). W przeciwnym razie usługa Azure AD zwróci informację o tym, że użytkownik jest zarządzany, wskazując, że użytkownik może uwierzytelniać się w usłudze Azure AD. |
| D | Jeśli użytkownik jest federacyjny, wtyczka CloudAP żąda tokenu SAML od dostawcy federacyjnego z poświadczeniami użytkownika. Po odebraniu token SAML żąda identyfikatora nonce z usługi Azure AD. Jeśli użytkownik jest zarządzany, CloudAP będzie bezpośrednio uzyskać identyfikator jednorazowy z usługi Azure AD. |
| E | Wtyczka CloudAP konstruuje żądanie uwierzytelnienia przy użyciu poświadczeń użytkownika, identyfikatora nonce i istniejącego PRT, podpisuje żądanie przy użyciu klucza sesji i wysyła je do usługi Azure AD. W środowisku federacyjnym wtyczka CloudAP używa tokenu SAML zwróconego przez dostawcę federacyjnego zamiast poświadczeń użytkownika. |
| F | Usługa Azure AD weryfikuje sygnaturę klucza sesji, porównując ją z kluczem sesji osadzonym w PRT, weryfikuje identyfikator jednorazowy i weryfikuje, czy urządzenie jest prawidłowe w dzierżawie i wystawia nowe PRT. Jak wspomniano wcześniej, PRT ponownie towarzyszy kluczowi sesji zaszyfrowanemu przy użyciu klucza transportowego (tkpub). |
| G | Wtyczka CloudAP przekazuje zaszyfrowane PRT i klucz sesji do CloudAP. CloudAP żąda od modułu TPM odszyfrowania klucza sesji przy użyciu klucza transportowego (tkpriv) i ponownego zaszyfrowania przy użyciu własnego klucza modułu TPM. CloudAP przechowuje zaszyfrowany klucz sesji w pamięci podręcznej wraz z PRT. |

> [!NOTE]
> PRT można odnowić zewnętrznie bez potrzeby połączenia sieci VPN, gdy punkty końcowe usernamemixed są włączone zewnętrznie.

### <a name="prt-usage-during-app-token-requests"></a>PRT użycie podczas żądań tokenów aplikacji

![PRT użycie podczas żądań tokenów aplikacji](./media/concept-primary-refresh-token/prt-usage-app-token-requests.png)

| Krok | Opis |
| :---: | --- |
| A | Aplikacja (na przykład Outlook, OneNote itp.) inicjuje żądanie tokenu do Menedżera WAM. WAM z kolei prosi o wtyczkę Menedżera WAM usługi Azure AD w celu obsługi żądania tokenu. |
| B | Jeśli token odświeżania aplikacji jest już dostępny, Wtyczka usługi Azure AD WAM używa go do żądania tokenu dostępu. Aby zapewnić potwierdzenie powiązania urządzenia, Wtyczka Menedżera WAM podpisuje żądanie przy użyciu klucza sesji. Usługa Azure AD sprawdza poprawność klucza sesji i wystawia token dostępu oraz nowy token odświeżania aplikacji szyfrowany przez klucz sesji. Wtyczka Menedżera aplikacji żąda wtyczki AP do odszyfrowania tokenów, co z kolei żąda od modułu TPM odszyfrowania przy użyciu klucza sesji, co spowodowało włączenie wtyczki Menedżera WAM w celu uzyskania obu tokenów. Następnie Wtyczka Menedżera WAM udostępnia tylko token dostępu do aplikacji, podczas gdy ponownie szyfruje token odświeżania przy użyciu DPAPI i zapisuje go w własnej pamięci podręcznej  |
| C |  Jeśli token odświeżania aplikacji nie jest dostępny, Wtyczka usługi Azure AD WAM używa PRT do żądania tokenu dostępu. Aby zapewnić dowód posiadania, Wtyczka Menedżera WAM podpisuje żądanie zawierające PRT z kluczem sesji. Usługa Azure AD weryfikuje sygnaturę klucza sesji, porównując ją z kluczem sesji osadzonym w PRT, sprawdza, czy urządzenie jest prawidłowe i wystawia token dostępu i token odświeżenia dla aplikacji. Ponadto usługa Azure AD może wydać nowe PRT (w oparciu o cykl odświeżania), wszystkie z nich zaszyfrowane za pomocą klucza sesji. |
| D | Wtyczka Menedżera aplikacji żąda wtyczki AP do odszyfrowania tokenów, co z kolei żąda od modułu TPM odszyfrowania przy użyciu klucza sesji, co spowodowało włączenie wtyczki Menedżera WAM w celu uzyskania obu tokenów. Następnie Wtyczka Menedżera WAM udostępnia tylko token dostępu do aplikacji, podczas gdy ponownie szyfruje token odświeżania za pomocą DPAPI i zapisuje go w własnej pamięci podręcznej. Wtyczka Menedżera WAM będzie używać tokenu odświeżania do przesyłania dalej dla tej aplikacji. Wtyczka Menedżera WAM udostępnia również nowy PRT do wtyczki AP w chmurze, który sprawdza poprawność PRT w usłudze Azure AD przed aktualizacją jej we własnej pamięci podręcznej. Wtyczka AP w chmurze będzie używać nowej PRT do przodu. |
| E | WAM udostępnia nowo wystawiony token dostępu do Menedżera WAM, co z kolei umożliwia powrót do aplikacji wywołującej|

### <a name="browser-sso-using-prt"></a>Logowanie jednokrotne w przeglądarce za pomocą PRT

![Logowanie jednokrotne w przeglądarce za pomocą PRT](./media/concept-primary-refresh-token/browser-sso-using-prt.png)

| Krok | Opis |
| :---: | --- |
| A | Użytkownik loguje się do systemu Windows przy użyciu swoich poświadczeń w celu uzyskania PRT. Gdy użytkownik otworzy przeglądarkę, przeglądarka (lub rozszerzenie) ładuje adresy URL z rejestru. |
| B | Gdy użytkownik otworzy adres URL logowania do usługi Azure AD, przeglądarka lub rozszerzenie zweryfikuje adres URL przy użyciu tych uzyskanych z rejestru. Jeśli są one zgodne, przeglądarka wywołuje natywnego hosta klienta w celu uzyskania tokenu. |
| C | Natywny Host klienta sprawdza, czy adresy URL należą do dostawców tożsamości firmy Microsoft (konto Microsoft lub Azure AD), wyodrębniają identyfikator jednorazowy wysłany z adresu URL i tworzy wywołanie CloudAP wtyczki, aby uzyskać plik cookie PRT. |
| D | Wtyczka CloudAP utworzy plik cookie PRT, zaloguj się przy użyciu klucza sesji powiązanego z modułem TPM i wyśle go z powrotem do natywnego hosta klienta. |
| E | Natywny Host klienta zwróci ten plik cookie PRT do przeglądarki, który będzie zawierać go jako część nagłówka żądania o nazwie x-MS-RefreshTokenCredential i zażądać tokenów z usługi Azure AD. |
| F | Usługa Azure AD sprawdza poprawność sygnatury klucza sesji w pliku cookie PRT, sprawdza poprawność identyfikatora nonce, weryfikuje, czy urządzenie jest prawidłowe w dzierżawie, i wystawia token identyfikatora dla strony sieci Web oraz pliku cookie zaszyfrowanej sesji dla przeglądarki. |

> [!NOTE]
> Przepływ rejestracji jednokrotnej w przeglądarce opisany w powyższych krokach nie dotyczy sesji w trybach prywatnych, takich jak InPrivate w przeglądarce Microsoft Edge lub incognito w usłudze Google Chrome (w przypadku korzystania z rozszerzenia konta Microsoft).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat rozwiązywania problemów związanych z PRT, zobacz artykuł [Rozwiązywanie problemów z programem Azure Active Directory hybrydowym dołączonym do urządzeń z systemem Windows 10 i Windows Server 2016](troubleshoot-hybrid-join-windows-current.md).
