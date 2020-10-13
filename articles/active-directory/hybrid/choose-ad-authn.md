---
title: Uwierzytelnianie dla rozwiązań tożsamości hybrydowej usługi Azure AD
titleSuffix: Active Directory
description: Ten przewodnik pomaga CEOs, dyrektorzy działu informatyki, CISOs, starszym architektom tożsamości, architektom w przedsiębiorstwach i bezpieczeństwie oraz podejmującym decyzje IT odpowiedzialne za wybór metody uwierzytelniania dla swojego rozwiązania do obsługi tożsamości hybrydowej usługi Azure AD w średnich i dużych organizacjach.
keywords: ''
author: martincoetzer
ms.author: martinco
ms.date: 10/30/2019
ms.topic: article
ms.service: security
ms.subservice: security-fundamentals
ms.workload: identity
ms.openlocfilehash: d5de8da548c2e141eb921aa4f95e82f7199ae1f4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90602374"
---
# <a name="choose-the-right-authentication-method-for-your-azure-active-directory-hybrid-identity-solution"></a>Wybierz właściwą metodę uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej

Wybór odpowiedniej metody uwierzytelniania to pierwszy problem dotyczący organizacji, które chcą przenieść aplikacje do chmury. Nie podejmuj tej decyzji jasno, z następujących powodów:

1. Jest to pierwsza decyzja dla organizacji, która chce przejść do chmury.

2. Metoda uwierzytelniania jest krytycznym składnikiem obecności organizacji w chmurze. Kontroluje dostęp do wszystkich danych i zasobów w chmurze.

3. Jest to podstawą wszystkich innych zaawansowanych funkcji zabezpieczeń i środowiska użytkownika w usłudze Azure AD.

Tożsamość jest nową płaszczyzną kontroli bezpieczeństwa INFORMATYCZNego, więc uwierzytelnianie jest ochroną organizacji do nowego świata w chmurze. Organizacje potrzebują płaszczyzny kontroli tożsamości, która zwiększa bezpieczeństwo i utrzymuje bezpieczeństwo aplikacji w chmurze przed intruzami.

> [!NOTE]
> Zmiana metody uwierzytelniania wymaga planowania, testowania i ewentualnych przestojów. [Wdrażanie etapowe](./how-to-connect-staged-rollout.md) to doskonały sposób na testowanie migracji użytkowników z Federacji do uwierzytelniania w chmurze.

### <a name="out-of-scope"></a>Poza zakresem
Organizacje, które nie mają istniejącej lokalnej usługi katalogowej, nie są fokusem tego artykułu. Zazwyczaj te firmy tworzą tożsamości tylko w chmurze, które nie wymagają rozwiązania do tworzenia tożsamości hybrydowej. Tożsamości tylko w chmurze istnieją wyłącznie w chmurze i nie są skojarzone z odpowiednimi tożsamościami lokalnymi.

## <a name="authentication-methods"></a>Metody uwierzytelniania
Gdy rozwiązanie do tworzenia tożsamości hybrydowej usługi Azure AD jest nową płaszczyzną kontroli, uwierzytelnianie jest podstawą dostępu do chmury. Wybór odpowiedniej metody uwierzytelniania jest kluczową pierwszą decyzją dotyczącą konfigurowania rozwiązania do tworzenia tożsamości hybrydowej usługi Azure AD. Zaimplementuj metodę uwierzytelniania skonfigurowaną za pomocą Azure AD Connect, która również udostępnia użytkownikom w chmurze.

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącą infrastrukturę, złożoność i koszt implementacji własnego wyboru. Te czynniki są różne dla każdej organizacji i mogą ulec zmianie w czasie.

>[!VIDEO https://www.youtube.com/embed/YtW2cmVqSEw]

Usługa Azure AD obsługuje następujące metody uwierzytelniania dla hybrydowych rozwiązań do obsługi tożsamości.

### <a name="cloud-authentication"></a>Uwierzytelnianie w chmurze
Po wybraniu tej metody uwierzytelniania usługa Azure AD obsługuje proces logowania użytkowników. W połączeniu z bezproblemowym logowaniem jednokrotnym użytkownicy mogą logować się do aplikacji w chmurze bez konieczności ponownego wprowadzania poświadczeń. Przy użyciu uwierzytelniania w chmurze możesz wybrać jedną z dwóch opcji:

**Synchronizacja skrótów haseł w usłudze Azure AD**. Najprostszy sposób na włączenie uwierzytelniania dla obiektów katalogu lokalnego w usłudze Azure AD. Użytkownicy mogą używać tej samej nazwy użytkownika i hasła, z których korzystają lokalnie bez konieczności wdrażania dodatkowej infrastruktury. Niektóre funkcje Premium usługi Azure AD, takie jak Ochrona tożsamości i [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md), wymagają synchronizacji skrótów haseł, niezależnie od wybranej metody uwierzytelniania.

> [!NOTE]
> Hasła nigdy nie są przechowywane w postaci zwykłego tekstu ani szyfrowane przy użyciu algorytmu odwracalnego w usłudze Azure AD. Aby uzyskać więcej informacji na temat rzeczywistego procesu synchronizacji skrótów haseł, zobacz [Implementacja synchronizacji skrótów haseł z synchronizacją Azure AD Connect](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md).

**Uwierzytelnianie przekazywane przez usługę Azure AD**. Zapewnia prostą weryfikację hasła dla usług uwierzytelniania usługi Azure AD przy użyciu agenta oprogramowania działającego na co najmniej jednym serwerze lokalnym. Serwery weryfikują użytkowników bezpośrednio przy użyciu Active Directory lokalnych, co gwarantuje, że sprawdzanie poprawności hasła nie odbywa się w chmurze.

W przypadku firm mających wymóg bezpieczeństwa aby natychmiast wymusić lokalne Stany konta użytkownika, zasady haseł i godziny logowania mogą korzystać z tej metody uwierzytelniania. Aby uzyskać więcej informacji o rzeczywistym procesie uwierzytelniania przekazywanego, zobacz [Logowanie użytkownika przy użyciu uwierzytelniania przekazywanego przez usługę Azure AD](../../active-directory/hybrid/how-to-connect-pta.md).

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne
W przypadku wybrania tej metody uwierzytelniania usługa Azure AD nie przełączy proces uwierzytelniania do oddzielnego zaufanego systemu uwierzytelniania, takiego jak Active Directory Federation Services lokalnego (AD FS), aby sprawdzić poprawność hasła użytkownika.

System uwierzytelniania może zapewnić dodatkowe zaawansowane wymagania dotyczące uwierzytelniania. Przykłady to uwierzytelnianie oparte na kartach inteligentnych lub uwierzytelnianie wieloskładnikowe innej firmy. Aby uzyskać więcej informacji, zobacz [wdrażanie Active Directory Federation Services](/windows-server/identity/ad-fs/deployment/windows-server-2012-r2-ad-fs-deployment-guide).

Poniższa sekcja ułatwia podjęcie decyzji, która metoda uwierzytelniania jest odpowiednia dla Ciebie, za pomocą drzewa decyzyjnego. Pomaga w ustaleniu, czy wdrożyć uwierzytelnianie w chmurze, czy federacyjne dla rozwiązania tożsamości hybrydowej usługi Azure AD.

## <a name="decision-tree"></a>Drzewo decyzyjne

![Drzewo decyzyjne uwierzytelniania usługi Azure AD](./media/choose-ad-authn/azure-ad-authn-image1.png)

Szczegółowe informacje na temat decyzji:

1. Usługa Azure AD może obsłużyć logowanie do użytkowników bez polegania na składnikach lokalnych do weryfikowania haseł.
2. Usługa Azure AD może wycofać Logowanie użytkownika do zaufanego dostawcy uwierzytelniania, takiego jak AD FS firmy Microsoft.
3. W przypadku konieczności zastosowania zasad zabezpieczeń Active Directory na poziomie użytkownika, takich jak konto wygasłe, wyłączone konto, wygasłe hasło, konto zablokowane i logowanie przy każdym logowaniu użytkownika, usługa Azure AD wymaga pewnych składników lokalnych.
4. Funkcje logowania, które nie są natywnie obsługiwane przez usługę Azure AD:
   * Zaloguj się przy użyciu kart inteligentnych lub certyfikatów.
   * Zaloguj się przy użyciu lokalnego serwera usługi MFA.
   * Zaloguj się przy użyciu rozwiązania do uwierzytelniania innej firmy.
   * Rozwiązanie do lokalnego uwierzytelniania w ramach usługi wiele lokacji.
5. Azure AD Identity Protection wymaga synchronizacji skrótów haseł niezależnie od wybranej metody logowania, aby zapewnić *użytkownikom raport o przecieku poświadczeń* . Organizacje mogą przechodzić w tryb failover na potrzeby synchronizacji skrótów haseł, jeśli ich podstawowa metoda logowania zakończy się niepowodzeniem i została skonfigurowana przed zdarzeniem błędu.

> [!NOTE]
> Azure AD Identity Protection Wymagaj licencji na [Azure AD — wersja Premium P2](https://azure.microsoft.com/pricing/details/active-directory/) .

## <a name="detailed-considerations"></a>Szczegółowe zagadnienia

### <a name="cloud-authentication-password-hash-synchronization"></a>Uwierzytelnianie w chmurze: synchronizacja skrótów haseł

* **Nakład pracy**. Synchronizacja skrótów haseł wymaga najmniej wysiłku związanego z wdrażaniem, konserwacją i infrastrukturą.  Ten poziom nakładu pracy dotyczy zazwyczaj organizacji, które wymagają, aby użytkownicy mogli logować się do aplikacji Microsoft 365, SaaS i innych zasobów opartych na usłudze Azure AD. Po włączeniu synchronizacja skrótów haseł jest częścią procesu synchronizacji Azure AD Connect i jest uruchamiana co dwie minuty.

* **Środowisko użytkownika**. Aby ulepszyć środowisko logowania użytkowników, wdróż bezproblemową rejestrację jednokrotną przy użyciu synchronizacji skrótów haseł. Bezproblemowe logowanie jednokrotne eliminuje zbędne monity, gdy użytkownicy są zalogowani.

* **Scenariusze zaawansowane**. Jeśli organizacja zdecyduje się na to, można używać szczegółowych informacji z tożsamości z raportami Azure AD Identity Protection z Azure AD — wersja Premium P2. Przykładem jest raport o przeciekach poświadczeń. Funkcja Windows Hello dla firm ma [określone wymagania w przypadku używania synchronizacji skrótów haseł](/windows/access-protection/hello-for-business/hello-identity-verification). [Azure AD Domain Services](../../active-directory-domain-services/tutorial-create-instance.md) wymaga synchronizacji skrótów haseł w celu aprowizacji użytkowników przy użyciu poświadczeń firmowych w domenie zarządzanej.

    Organizacje, które wymagają uwierzytelniania wieloskładnikowego z synchronizacją skrótów haseł, muszą używać [niestandardowych kontrolek](../../active-directory/conditional-access/controls.md#custom-controls-preview)usługi Azure Multi-Factor Authentication lub dostępu warunkowego. Organizacje te nie mogą korzystać z metod uwierzytelniania wieloskładnikowego innych firm ani lokalnych, które opierają się na Federacji.

> [!NOTE]
> Dostęp warunkowy usługi Azure AD wymaga [Azure AD — wersja Premium licencji P1](https://azure.microsoft.com/pricing/details/active-directory/) .

* **Ciągłość**działania. Używanie synchronizacji skrótów haseł z uwierzytelnianiem w chmurze jest wysoce dostępne jako usługa w chmurze, która skaluje się do wszystkich centrów danych firmy Microsoft. Aby upewnić się, że synchronizacja skrótów haseł nie zostanie wyłączona przez Rozszerzone okresy, wdróż drugi Azure AD Connect serwer w trybie przejściowym w konfiguracji gotowości.

* **Zagadnienia**. Obecnie synchronizacja skrótów haseł nie wymusza natychmiast zmian w Stanach kont lokalnych. W takiej sytuacji użytkownik ma dostęp do aplikacji w chmurze, dopóki stan konta użytkownika nie zostanie zsynchronizowany z usługą Azure AD. Organizacje mogą chcieć przezwyciężyć to ograniczenie, uruchamiając nowy cykl synchronizacji, gdy Administratorzy przeprowadzają zbiorcze aktualizacje lokalnych Stanów kont użytkowników. Przykładem jest wyłączenie kont.

> [!NOTE]
> Hasło wygasło, a Stany zablokowanego konta nie są obecnie synchronizowane z usługą Azure AD za pomocą Azure AD Connect. Jeśli zmienisz hasło użytkownika i ustawisz, że *użytkownik musi zmienić hasło przy następnym logowaniu* , skrót hasła nie zostanie zsynchronizowany z usługą Azure AD za pomocą Azure AD Connect, dopóki użytkownik nie zmieni hasła.

Zapoznaj się z artykułem [implementowanie synchronizacji skrótów haseł](../../active-directory/hybrid/how-to-connect-password-hash-synchronization.md) dla kroków wdrażania.

### <a name="cloud-authentication-pass-through-authentication"></a>Uwierzytelnianie w chmurze: uwierzytelnianie przekazywane  

* **Nakład pracy**. W przypadku uwierzytelniania przekazywanego potrzebna jest co najmniej jedna firma (zalecamy trzy) lekkie agenci zainstalowani na istniejących serwerach. Tacy agenci muszą mieć dostęp do Active Directory Domain Services lokalnych, w tym lokalnych kontrolerów domeny usługi AD. Potrzebują dostępu wychodzącego do Internetu i dostępu do kontrolerów domeny. Z tego powodu nie jest obsługiwane wdrażanie agentów w sieci obwodowej.

    Uwierzytelnianie przekazywane wymaga nieograniczonego dostępu sieciowego do kontrolerów domeny. Cały ruch sieciowy jest szyfrowany i ograniczony do żądań uwierzytelniania. Aby uzyskać więcej informacji na temat tego procesu, zapoznaj się z tematem [szczegółowe Security głębokie](../../active-directory/hybrid/how-to-connect-pta-security-deep-dive.md) uwierzytelnianie przekazywane.

* **Środowisko użytkownika**. Aby ulepszyć środowisko logowania użytkowników, wdróż bezproblemową rejestrację jednokrotną przy użyciu uwierzytelniania przekazywanego. Bezproblemowe logowanie jednokrotne eliminuje zbędne monity po zalogowaniu się użytkowników.

* **Scenariusze zaawansowane**. Uwierzytelnianie przekazywane wymusza lokalne zasady kont w momencie logowania się. Na przykład dostęp jest odrzucany, gdy stan konta użytkownika lokalnego jest wyłączony, zablokowany lub [hasło wygasa](../../active-directory/hybrid/how-to-connect-pta-faq.md#what-happens-if-my-users-password-has-expired-and-they-try-to-sign-in-by-using-pass-through-authentication) lub próba logowania przypada poza godzinami, gdy użytkownik może się zalogować.

    Organizacje, które wymagają uwierzytelniania wieloskładnikowego z uwierzytelnianiem przekazującym, muszą używać niestandardowych kontrolek usługi Azure Multi-Factor Authentication (MFA) lub [dostępu warunkowego](../../active-directory/conditional-access/controls.md#custom-controls-preview). Organizacje te nie mogą korzystać z metody uwierzytelniania wieloskładnikowego innej firmy ani lokalnej, która opiera się na Federacji. Funkcje zaawansowane wymagają, aby synchronizacja skrótów haseł była wdrażana niezależnie od tego, czy wybrano opcję uwierzytelnianie przekazywane. Przykładem jest raport dotyczący przecieków poświadczeń programu Identity Protection.

* **Ciągłość**działania. Zalecamy wdrożenie dwóch dodatkowych agentów uwierzytelniania Pass-through. Te dodatki są uzupełnieniem pierwszego agenta na serwerze Azure AD Connect. To dodatkowe wdrożenie zapewnia wysoką dostępność żądań uwierzytelniania. Po wdrożeniu trzech agentów jeden Agent może nadal kończyć się niepowodzeniem, gdy inny agent nie działa do konserwacji.

    Oprócz uwierzytelniania przekazywanego istnieje inna korzyść w zakresie wdrażania synchronizacji skrótów haseł. Działa jako metoda uwierzytelniania kopii zapasowej, gdy podstawowa metoda uwierzytelniania nie jest już dostępna.

* **Zagadnienia**. Możesz użyć synchronizacji skrótów haseł jako metody uwierzytelniania za pomocą kopii zapasowej na potrzeby uwierzytelniania przekazywanego, gdy agenci nie mogą zweryfikować poświadczeń użytkownika z powodu znacznego błędu lokalnego. Przełączenie w tryb failover w celu synchronizacji skrótów haseł nie odbywa się automatycznie i należy użyć Azure AD Connect do ręcznego przełączania metody logowania.

    Aby poznać inne zagadnienia dotyczące uwierzytelniania przekazywanego, w tym alternatywnej obsługi identyfikatorów, zobacz [często zadawane pytania](../../active-directory/hybrid/how-to-connect-pta-faq.md).

Zapoznaj się z artykułem [implementacja uwierzytelniania przekazywanego](../../active-directory/hybrid/how-to-connect-pta.md) dla kroków wdrożenia.

### <a name="federated-authentication"></a>Uwierzytelnianie federacyjne

* **Nakład pracy**. Federacyjny system uwierzytelniania opiera się na zewnętrznym zaufanym systemie do uwierzytelniania użytkowników. Niektóre firmy chcą ponownie wykorzystać swoje istniejące inwestycje systemu federacyjnego przy użyciu rozwiązania do tworzenia tożsamości hybrydowej usługi Azure AD. Konserwacja systemu federacyjnego i zarządzanie nim wykracza poza kontrolę usługi Azure AD. Jest to możliwe w organizacji przy użyciu systemu federacyjnego, aby upewnić się, że jest on bezpiecznie wdrożony i może obsłużyć obciążenie uwierzytelniania.

* **Środowisko użytkownika**. Środowisko użytkownika związane z uwierzytelnianiem federacyjnym zależy od implementacji funkcji, topologii i konfiguracji farmy federacyjnej. Niektóre organizacje potrzebują tej elastyczności, aby dostosować i skonfigurować dostęp do farmy federacyjnej zgodnie z wymaganiami bezpieczeństwa. Można na przykład skonfigurować zalogowanych wewnętrznie użytkowników i urządzenia do automatycznego logowania użytkowników bez monitowania o poświadczenia. Ta konfiguracja działa, ponieważ zarejestrowali się już na swoich urządzeniach. W razie potrzeby niektóre zaawansowane funkcje zabezpieczeń sprawiają, że proces logowania użytkowników jest trudniejszy.

* **Scenariusze zaawansowane**. Rozwiązanie do uwierzytelniania federacyjnego jest wymagane, gdy klienci mają wymaganie uwierzytelniania, które nie są obsługiwane natywnie przez usługę Azure AD. Zobacz szczegółowe informacje pomocne w [wyborze odpowiedniej opcji logowania](/archive/blogs/samueld/choosing-the-right-sign-in-option-to-connect-to-azure-ad-office-365). Należy wziąć pod uwagę następujące typowe wymagania:

  * Uwierzytelnianie, które wymaga kart inteligentnych lub certyfikatów.
  * Lokalne serwery usługi MFA lub dostawcy wieloskładnikowego innych firm wymagające federacyjnego dostawcy tożsamości.
  * Uwierzytelnianie przy użyciu rozwiązań uwierzytelniania innych firm. Zobacz [listę zgodności Federacji usługi Azure AD](../../active-directory/hybrid/how-to-connect-fed-compatibility.md).
  * Logowanie, które wymaga konta sAMAccountName, na przykład domena \ nazwa_użytkownika, zamiast głównej nazwy użytkownika (UPN), na przykład user@domain.com .

* **Ciągłość**działania. Systemy federacyjne zwykle wymagają tablicy serwerów o zrównoważonym obciążeniu, znanej jako Farma. Ta farma jest konfigurowana w wewnętrznej sieci i topologii sieci obwodowej w celu zapewnienia wysokiej dostępności żądań uwierzytelniania.

    Wdróż synchronizację skrótów haseł oraz uwierzytelnianie federacyjne jako metodę uwierzytelniania kopii zapasowej, gdy podstawowa metoda uwierzytelniania nie jest już dostępna. Przykładem jest to, że serwery lokalne nie są dostępne. Niektóre duże organizacje korporacyjne wymagają rozwiązania federacyjnego do obsługi wielu punktów przychodzących z Internetu skonfigurowanych przy użyciu geograficznego systemu DNS dla żądań uwierzytelniania o małym opóźnieniu.

* **Zagadnienia**. Systemy federacyjne zwykle wymagają bardziej znaczącej inwestycji w infrastrukturę lokalną. W przypadku większości organizacji należy wybrać tę opcję, jeśli istnieje już lokalna inwestycja federacyjna. A jeśli jest to silny wymóg biznesowy do korzystania z dostawcy pojedynczej tożsamości. Federacja jest bardziej skomplikowany do działania i rozwiązywania problemów w porównaniu z rozwiązaniami do uwierzytelniania w chmurze.

W przypadku domeny bez obsługi routingu, która nie może zostać zweryfikowana w usłudze Azure AD, potrzebna jest dodatkowa konfiguracja do zaimplementowania logowania identyfikatora użytkownika. To wymaganie jest znane jako obsługa alternatywnego identyfikatora logowania. Zapoznaj się z tematem [Konfigurowanie alternatywnego identyfikatora logowania](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id) dla ograniczeń i wymagań. Jeśli zdecydujesz się na użycie dostawcy uwierzytelniania wieloskładnikowego innej firmy z Federacją, upewnij się, że dostawca obsługuje WS-Trust, aby zezwolić urządzeniom na dołączanie do usługi Azure AD.

Zapoznaj się z tematem [wdrażanie serwerów federacyjnych](/windows-server/identity/ad-fs/deployment/deploying-federation-servers) na potrzeby kroków wdrożenia.

> [!NOTE]
> Podczas wdrażania rozwiązania do obsługi tożsamości hybrydowej usługi Azure AD należy zaimplementować jedną z obsługiwanych topologii Azure AD Connect. Więcej informacji na temat obsługiwanych i nieobsługiwanych konfiguracji znajduje się w [topologii dla Azure AD Connect](../../active-directory/hybrid/plan-connect-topologies.md).

## <a name="architecture-diagrams"></a>Diagramy architektury

Na poniższym diagramie przedstawiono składniki architektury wysokiego poziomu wymagane przez każdą metodę uwierzytelniania, których można używać z rozwiązaniem tożsamości hybrydowej usługi Azure AD. Zapewniają one przegląd, który ułatwia porównanie różnic między rozwiązaniami.

* Prostota rozwiązania synchronizacji skrótów haseł:

    ![Tożsamość hybrydowa usługi Azure AD z synchronizacją skrótów haseł](./media/choose-ad-authn/azure-ad-authn-image2.png)

* Wymagania agenta dotyczące uwierzytelniania przekazywanego przy użyciu dwóch agentów w celu zapewnienia nadmiarowości:

    ![Tożsamość hybrydowa usługi Azure AD z uwierzytelnianiem przekazującym](./media/choose-ad-authn/azure-ad-authn-image3.png)

* Składniki wymagane dla Federacji w sieci obwodowej i wewnętrznej organizacji:

    ![Tożsamość hybrydowa usługi Azure AD z uwierzytelnianiem federacyjnym](./media/choose-ad-authn/azure-ad-authn-image4.png)

## <a name="comparing-methods"></a>Porównywanie metod

|Kwestie do rozważenia|Synchronizacja skrótów haseł + bezproblemowe logowanie jednokrotne|Uwierzytelnianie przekazywane i bezproblemowe logowanie jednokrotne|Federacja z usługami AD FS|
|:-----|:-----|:-----|:-----|
|Gdzie jest wykonywane uwierzytelnianie?|W chmurze|W chmurze po bezpiecznej weryfikacji hasła przy użyciu lokalnego agenta uwierzytelniania|Środowiska lokalne|
|Jakie są wymagania dotyczące serwera lokalnego poza systemem aprowizacji: Azure AD Connect?|Brak|Jeden serwer dla każdego dodatkowego agenta uwierzytelniania|Co najmniej dwa serwery AD FS<br><br>Dwa lub więcej serwerów WAP w sieci obwodowej/strefy DMZ|
|Jakie są wymagania dotyczące lokalnego Internetu i sieci poza systemem aprowizacji?|Brak|[Wychodzący dostęp do Internetu](../../active-directory/hybrid/how-to-connect-pta-quick-start.md) z serwerów z uruchomionymi agentami uwierzytelniania|[Przychodzący dostęp do Internetu](/windows-server/identity/ad-fs/overview/ad-fs-requirements) do serwerów WAP na obrzeżu<br><br>Dostęp do sieci przychodzącej do serwerów AD FS z serwerów WAP na obrzeżu<br><br>Równoważenie obciążenia sieciowego|
|Czy istnieje wymagania dotyczące certyfikatu TLS/SSL?|Nie|Nie|Tak|
|Czy istnieje rozwiązanie do monitorowania kondycji?|Niewymagane|Stan agenta udostępniany przez [Centrum administracyjne Azure Active Directory](../../active-directory/hybrid/tshoot-connect-pass-through-authentication.md)|[Azure AD Connect Health](../../active-directory/hybrid/how-to-connect-health-adfs.md)|
|Czy użytkownicy uzyskują Logowanie jednokrotne do zasobów w chmurze z urządzeń przyłączonych do domeny w sieci firmowej?|Tak, aby [bezproblemowe logowanie jednokrotne](../../active-directory/hybrid/how-to-connect-sso.md)|Tak, aby [bezproblemowe logowanie jednokrotne](../../active-directory/hybrid/how-to-connect-sso.md)|Tak|
|Jakie typy logowania są obsługiwane?|UserPrincipalName + hasło<br><br>Uwierzytelnianie Windows-Integrated przy użyciu [bezproblemowego logowania jednokrotnego](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternatywny identyfikator logowania](../../active-directory/hybrid/how-to-connect-install-custom.md)|UserPrincipalName + hasło<br><br>Uwierzytelnianie Windows-Integrated przy użyciu [bezproblemowego logowania jednokrotnego](../../active-directory/hybrid/how-to-connect-sso.md)<br><br>[Alternatywny identyfikator logowania](../../active-directory/hybrid/how-to-connect-pta-faq.md)|UserPrincipalName + hasło<br><br>sAMAccountName + hasło<br><br>Uwierzytelnianie Windows-Integrated<br><br>[Certyfikat i uwierzytelnianie karty inteligentnej](/windows-server/identity/ad-fs/operations/configure-user-certificate-authentication)<br><br>[Alternatywny identyfikator logowania](/windows-server/identity/ad-fs/operations/configuring-alternate-login-id)|
|Czy funkcja Windows Hello dla firm jest obsługiwana?|[Model zaufania kluczy](/windows/security/identity-protection/hello-for-business/hello-identity-verification)|[Model zaufania kluczy](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br>*Wymaga poziomu funkcjonalności domeny systemu Windows Server 2016*|[Model zaufania kluczy](/windows/security/identity-protection/hello-for-business/hello-identity-verification)<br><br>[Model zaufania certyfikatów](/windows/security/identity-protection/hello-for-business/hello-key-trust-adfs)|
|Jakie są opcje uwierzytelniania wieloskładnikowego?|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Formanty niestandardowe z dostępem warunkowym *](../../active-directory/conditional-access/controls.md)|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Formanty niestandardowe z dostępem warunkowym *](../../active-directory/conditional-access/controls.md)|[Azure MFA](/azure/multi-factor-authentication/)<br><br>[Serwer usługi Azure MFA](../../active-directory/authentication/howto-mfaserver-deploy.md)<br><br>[Uwierzytelnianie wieloskładnikowe innej firmy](/windows-server/identity/ad-fs/operations/configure-additional-authentication-methods-for-ad-fs)<br><br>[Formanty niestandardowe z dostępem warunkowym *](../../active-directory/conditional-access/controls.md)|
|Jakie Stany kont użytkowników są obsługiwane?|Wyłączone konta<br>(opóźnienie do 30 minut)|Wyłączone konta<br><br>Konto zablokowane<br><br>Konto wygasło<br><br>Hasło wygasło<br><br>Godziny logowania|Wyłączone konta<br><br>Konto zablokowane<br><br>Konto wygasło<br><br>Hasło wygasło<br><br>Godziny logowania|
|Jakie są opcje dostępu warunkowego?|[Dostęp warunkowy usługi Azure AD za pomocą Azure AD — wersja Premium](../../active-directory/conditional-access/overview.md)|[Dostęp warunkowy usługi Azure AD za pomocą Azure AD — wersja Premium](../../active-directory/conditional-access/overview.md)|[Dostęp warunkowy usługi Azure AD za pomocą Azure AD — wersja Premium](../../active-directory/conditional-access/overview.md)<br><br>[AD FS regułami dotyczącymi roszczeń](https://adfshelp.microsoft.com/AadTrustClaims/ClaimsGenerator)|
|Czy blokowane są obsługiwane starsze protokoły?|[Tak](../../active-directory/conditional-access/overview.md)|[Tak](../../active-directory/conditional-access/overview.md)|[Tak](/windows-server/identity/ad-fs/operations/access-control-policies-w2k12)|
|Czy można dostosować logo, obraz i opis na stronach logowania?|[Tak, z Azure AD — wersja Premium](../../active-directory/fundamentals/customize-branding.md)|[Tak, z Azure AD — wersja Premium](../../active-directory/fundamentals/customize-branding.md)|[Tak](../../active-directory/hybrid/how-to-connect-fed-management.md)|
|Jakie zaawansowane scenariusze są obsługiwane?|[Blokada hasła inteligentnego](../../active-directory/authentication/howto-password-smart-lockout.md)<br><br>[Raporty dotyczące przecieków poświadczeń z Azure AD — wersja Premium P2](../identity-protection/overview-identity-protection.md)|[Blokada hasła inteligentnego](../../active-directory/authentication/howto-password-smart-lockout.md)|System uwierzytelniania z wieloma opóźnieniami w wielu lokacjach<br><br>[Blokada AD FS ekstranetu](/windows-server/identity/ad-fs/operations/configure-ad-fs-extranet-soft-lockout-protection)<br><br>[Integracja z systemami tożsamości innych firm](../../active-directory/hybrid/how-to-connect-fed-compatibility.md)|

> [!NOTE]
> Kontrolki niestandardowe w dostępie warunkowym usługi Azure AD nie obsługują obecnie rejestracji urządzeń.

## <a name="recommendations"></a>Zalecenia
System tożsamości zapewnia użytkownikom dostęp do aplikacji w chmurze i aplikacji biznesowych, które są migrowane i udostępniane w chmurze. Aby zapewnić nieuprawnionym użytkownikom produktywność i złe działania z poufnych danych organizacji, uwierzytelnianie kontroluje dostęp do aplikacji.

Użyj lub Włącz synchronizację skrótów haseł dla wybranej metody uwierzytelniania, z następujących powodów:

1. **Wysoka dostępność i odzyskiwanie po awarii**. Uwierzytelnianie przekazywane i federacyjne polegają na infrastrukturze lokalnej. W przypadku uwierzytelniania przekazywanego lokalna wartość obejmuje sprzęt serwera i sieci, które są wymagane przez agentów uwierzytelniania przekazywanego. W przypadku federacji wartość lokalna jest jeszcze większa. Wymaga serwerów w sieci obwodowej do żądań uwierzytelniania serwera proxy i wewnętrznych serwerów federacyjnych.

    Aby uniknąć pojedynczych punktów awarii, wdróż nadmiarowe serwery. Następnie żądania uwierzytelniania będą zawsze serwisowane w przypadku niepowodzenia jakiegokolwiek składnika. Zarówno uwierzytelnianie przekazywane, jak i Federacja mogą polegać na kontrolerach domeny w celu reagowania na żądania uwierzytelniania, co może również zakończyć się niepowodzeniem. Wiele z tych składników wymaga konserwacji w dobrej kondycji. Przestoje są bardziej podobne, gdy konserwacja nie jest zaplanowana i niepoprawnie zaimplementowana. Należy unikać przestojów dzięki synchronizacji skrótów haseł, ponieważ Microsoft Azure AD usługa uwierzytelniania w chmurze jest skalowana globalnie i jest zawsze dostępna.

2. **Lokalne przeżycia awarii**.  Konsekwencje awarii lokalnej wynikające z ataku cybernetycznymiego lub awarii mogą być znaczne, od nieupoważnionej marki do organizacji Paralyzed, której nie można zaradzić. Niedawno wiele organizacji było ofiar ataków złośliwego oprogramowania, w tym przeznaczonych dla nich programów wymuszającego okup, które spowodowały, że serwery lokalne przechodzą w dół. Gdy firma Microsoft pomaga klientom w rozproszeniu tego rodzaju ataków, widzi dwie kategorie organizacji:

   * Organizacje, które wcześniej włączyły funkcję synchronizacji skrótów haseł na serwerze federacyjnym lub uwierzytelnianiu przekazującym, zmieniły podstawową metodę uwierzytelniania w taki sposób, aby używały synchronizacji skrótów haseł. Są one z powrotem w trybie online w ciągu kilku godzin. Dzięki dostępowi do poczty e-mail za pośrednictwem Microsoft 365 działały one w celu rozwiązywania problemów i uzyskiwania dostępu do innych obciążeń opartych na chmurze.

   * Organizacje, które wcześniej nie włączyły synchronizacji skrótów haseł, musiały korzystać z niezaufanych zewnętrznych systemów poczty e-mail, aby umożliwić komunikację w celu rozwiązywania problemów. W takich przypadkach przeprowadzono te tygodnie do przywrócenia lokalnej infrastruktury tożsamości, zanim użytkownicy będą mogli ponownie zalogować się do aplikacji opartych na chmurze.

3. **Ochrona tożsamości**. Jednym z najlepszych sposobów ochrony użytkowników w chmurze jest Azure AD Identity Protection z Azure AD — wersja Premium P2. Firma Microsoft nieustannie skanuje Internet pod kątem list użytkowników i haseł, które są niewłaściwymi aktorami sprzedawanymi i udostępnianymi w ciemnej sieci Web. Usługa Azure AD może korzystać z tych informacji w celu sprawdzenia, czy dowolna z nazw użytkowników i haseł w organizacji zostanie naruszona. W związku z tym krytyczne jest włączenie synchronizacji skrótów haseł niezależnie od używanej metody uwierzytelniania, niezależnie od tego, czy jest to uwierzytelnianie federacyjne, czy przekazywane. Ujawnione poświadczenia są prezentowane jako raport. Te informacje służą do blokowania lub wymuszania zmiany haseł przez użytkowników podczas próby zalogowania się przy użyciu nieujawnionych haseł.

## <a name="conclusion"></a>Podsumowanie

W tym artykule opisano różne opcje uwierzytelniania, które organizacje mogą konfigurować i wdrażać w celu zapewnienia obsługi dostępu do aplikacji w chmurze. Aby sprostać różnym wymaganiom biznesowym, zabezpieczeniom i technicznym, organizacje mogą wybierać między synchronizacją skrótów haseł, uwierzytelnianiem przekazywanym i Federacją.

Należy wziąć pod uwagę każdą metodę uwierzytelniania. Czy warto zastanowić się nad wdrożeniem rozwiązania, a środowisko użytkownika procesu logowania spełnia wymagania biznesowe? Oceń, czy organizacja wymaga zaawansowanych scenariuszy i funkcji ciągłości działania każdej metody uwierzytelniania. Na koniec Oceń zagadnienia związane z każdą metodą uwierzytelniania. Czy dowolne z nich uniemożliwia zaimplementowanie wyboru?

## <a name="next-steps"></a>Następne kroki

W dzisiejszym świecie zagrożenia są obecne przez 24 godziny dziennie i pochodzą z dowolnego miejsca. Zaimplementuj poprawną metodę uwierzytelniania, co pozwoli wyeliminować zagrożenia bezpieczeństwa i chronić tożsamości.

[Rozpocznij pracę](../fundamentals/active-directory-whatis.md) z usługą Azure AD i Wdróż odpowiednie rozwiązanie uwierzytelniania dla swojej organizacji.

Jeśli myślisz o migracji z Federacji do uwierzytelniania w chmurze, Dowiedz się więcej [na temat zmiany metody logowania](../../active-directory/hybrid/plan-connect-user-signin.md). Aby ułatwić planowanie i implementowanie migracji, należy użyć [tych planów wdrażania projektu](https://aka.ms/deploymentplans) lub rozważyć użycie nowej funkcji wdrożenia [etapowego](../../active-directory/hybrid/how-to-connect-staged-rollout.md) w celu przeprowadzenia migracji użytkowników federacyjnych do korzystania z uwierzytelniania w chmurze w podejściu przygotowanym.