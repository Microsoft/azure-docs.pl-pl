---
title: Planowanie Azure Active Directory wdrożenia przy użyciu logowania jednokrotnego
description: Przewodnik ułatwiający planowanie, wdrażanie i zarządzanie logowaniem jednokrotnym w organizacji.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 14520c7c448231045b36e025c2c92332f9daac53
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658727"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planowanie wdrożenia logowania jednokrotnego

Logowanie jednokrotne (SSO) oznacza uzyskiwanie dostępu do wszystkich aplikacji i zasobów potrzebnych przez użytkownika, logując się tylko raz przy użyciu jednego konta użytkownika. Za pomocą logowania jednokrotnego użytkownicy mogą uzyskiwać dostęp do wszystkich potrzebnych aplikacji bez konieczności uwierzytelniania po raz drugi.

## <a name="benefits-of-sso"></a>Zalety logowania jednokrotnego

Logowanie jednokrotne (SSO) zwiększa bezpieczeństwo i wygodę, gdy użytkownicy logują się do aplikacji w Azure Active Directory (Azure AD). 

Wiele organizacji korzysta z aplikacji SaaS (Software as a Service), takich jak Microsoft 365, Box i Salesforce, w celu zwiększenia produktywności użytkowników końcowych. W przeszłości pracownicy działu IT musieli indywidualnie utworzyć i zaktualizować konta użytkowników w każdej aplikacji SaaS, a użytkownicy musieli zapamiętać hasło dla każdego z nich.

Portal Azure Marketplace obejmuje ponad 3000 aplikacji z wstępnie zintegrowanymi połączeniami SSO, co ułatwia ich integrację z dzierżawą.

## <a name="licensing"></a>Licencjonowanie

- **Licencjonowanie usługi Azure AD** — Logowanie jednokrotne dla wstępnie zintegrowanych aplikacji SaaS jest bezpłatne. Jednak liczba obiektów w katalogu i funkcje, które mają zostać wdrożone, mogą wymagać dodatkowych licencji. Aby uzyskać pełną listę wymagań dotyczących licencji, zobacz [Cennik usługi Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licencjonowanie aplikacji** — wymagane są odpowiednie licencje dla aplikacji SaaS, które będą spełniały Twoje potrzeby biznesowe. Aby określić, czy użytkownicy przypisani do aplikacji mają odpowiednie licencje dla ich ról w aplikacji, należy skontaktować się z właścicielem aplikacji. Jeśli usługa Azure AD zarządza automatyczną obsługą administracyjną opartą na rolach, role przypisane w usłudze Azure AD muszą być wyrównane z liczbą licencji należących do aplikacji. Niewłaściwa liczba licencji należących do aplikacji może prowadzić do błędów podczas aprowizacji/aktualizowania użytkownika.

## <a name="plan-your-sso-team"></a>Planowanie zespołu ds. rejestracji jednokrotnej

- Weź **udział w odpowiednich udziałowcach** — w przypadku niepowodzenia projektów technologicznych zazwyczaj wynika to z niezgodnego oczekiwania na wpływ, wyniki i obowiązki. Aby uniknąć tych pułapek, [upewnij się, że interesują](../fundamentals/active-directory-deployment-plans.md) Cię odpowiednie osoby zainteresowane i że uczestnicy projektu rozumieją swoje role.
- **Planowanie** komunikacji — komunikacja jest niezwykle ważna dla sukcesu każdej nowej usługi. Aktywnie Komunikuj się z użytkownikami, aby dowiedzieć się, jak ich środowisko zostanie zmienione, kiedy ulegnie zmianie, i jak uzyskać pomoc techniczną, jeśli wystąpią problemy. Zapoznaj się z opcjami dotyczącymi sposobu, w [jaki użytkownicy końcowi będą uzyskiwać dostęp do swoich aplikacji z obsługą logowania JEDNOkrotnego, a następnie wystawania](end-user-experiences.md)komunikacji 

## <a name="plan-your-sso-protocol"></a>Planowanie protokołu SSO

Implementacja logowania jednokrotnego oparta na protokołach federacyjnych podnosi bezpieczeństwo, niezawodność i środowisko użytkownika końcowego i jest łatwiejsza do wdrożenia. Wiele aplikacji jest wstępnie zintegrowanych w usłudze Azure AD [, a dostępne są Przewodniki krok po kroku](../saas-apps/tutorial-list.md). Możesz je znaleźć w [portalu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Szczegółowe informacje na temat każdej metody rejestracji jednokrotnej można znaleźć w artykule Logowanie jednokrotne [do aplikacji w Azure Active Directory](what-is-single-sign-on.md).

Istnieją dwa podstawowe sposoby pozwalające użytkownikom na logowanie jednokrotne do aplikacji:

- **Z federacyjnym logowaniem jednokrotnym** Usługa Azure AD uwierzytelnia użytkownika w aplikacji przy użyciu konta usługi Azure AD. Ta metoda jest obsługiwana w przypadku aplikacji obsługujących protokoły takie jak SAML 2,0, WS-Federation lub OpenID Connect Connect, a także jest zaawansowanym trybem rejestracji jednokrotnej. Zalecamy używanie federacyjnego logowania jednokrotnego w usłudze Azure AD, gdy aplikacja je obsługuje, zamiast logowania jednokrotnego i usług ADFS opartych na hasłach.

- Logowanie jednokrotne **oparte na hasłach** loguje się do aplikacji przy użyciu nazwy użytkownika i hasła po raz pierwszy uzyskuje do niej dostęp. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji. Logowanie jednokrotne oparte na hasłach umożliwia bezpieczne przechowywanie i odtwarzanie haseł aplikacji przy użyciu rozszerzenia przeglądarki sieci Web lub aplikacji mobilnej. Ta opcja wykorzystuje istniejący proces logowania udostępniany przez aplikację, umożliwia administratorowi zarządzanie hasłami i nie wymaga od użytkownika poznania hasła.

### <a name="considerations-for-federation-based-sso"></a>Zagadnienia dotyczące logowania jednokrotnego opartego na Federacji

- **Przy użyciu programu OpenID Connect Connect i uwierzytelniania OAuth** — Jeśli aplikacja, z którą nawiązujesz połączenie, obsługuje tę funkcję, użyj metody OIDC/OAuth 2,0, aby włączyć logowanie jednokrotne do tej aplikacji. Ta metoda wymaga mniejszej konfiguracji i umożliwia bogatsze środowisko użytkownika. Aby uzyskać więcej informacji, zobacz [uwierzytelnianie OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect Connect 1,0](../develop/v2-protocols-oidc.md)i [przewodnik dewelopera Azure Active Directory](../develop/index.yml).
- **Konfiguracje punktów końcowych dla logowania jednokrotnego opartego na** protokole SAML — Jeśli używasz protokołu SAML, deweloperzy będą musieli uzyskać określone informacje przed skonfigurowaniem aplikacji. Aby uzyskać więcej informacji, zobacz [Konfigurowanie logowania jednokrotnego opartego na protokole SAML](configure-saml-single-sign-on.md).
- **Zarządzanie certyfikatami dla logowania jednokrotnego opartego** na protokole SAML — po włączeniu federacyjnego logowania jednokrotnego dla aplikacji usługa Azure AD tworzy certyfikat, który jest domyślnie ważny przez trzy lata. W razie potrzeby można dostosować datę wygaśnięcia dla tego certyfikatu. Upewnij się, że masz procesy do odnawiania certyfikatów przed ich wygaśnięciem. Aby dowiedzieć się więcej, zobacz temat [Zarządzanie certyfikatami w usłudze Azure AD](./manage-certificates-for-federated-single-sign-on.md).

### <a name="considerations-for-password-based-sso"></a>Zagadnienia dotyczące logowania jednokrotnego opartego na hasłach

Korzystanie z usługi Azure AD na potrzeby logowania jednokrotnego opartego na hasłach wymaga wdrożenia przeglądarki, która będzie bezpiecznie pobierać poświadczenia i uzupełnić formularze logowania. Zdefiniuj mechanizm wdrażania rozszerzenia na dużą skalę z [obsługiwanymi przeglądarkami](../user-help/my-apps-portal-end-user-access.md). Dostępne są następujące opcje:

- [zasady grupy programu Internet Explorer](./access-panel-deployment-plan.md)
- [Configuration Manager programu Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Pobieranie i Konfigurowanie na podstawie użytkownika dla programu Chrome, Firefox, Microsoft Edge lub IE](../user-help/my-apps-portal-end-user-access.md)

Aby dowiedzieć się więcej, zobacz [jak skonfigurować Logowanie jednokrotne przy użyciu hasła](./configure-password-single-sign-on-non-gallery-applications.md).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Przechwytywanie metadanych formularzy logowania dla aplikacji, które nie znajdują się w galerii

Firma Microsoft obsługuje przechwytywanie metadanych w aplikacji sieci Web na potrzeby obsługi magazynów haseł (przechwytywanie pól username i Password). Przejdź do adresu URL logowania podczas procesu konfigurowania aplikacji w celu przechwytywania metadanych formularzy. Poproszenie właściciela aplikacji o dokładny adres URL logowania. Te informacje są używane podczas procesu logowania, które umożliwiają mapowanie poświadczeń usługi Azure AD do aplikacji podczas logowania.

Aby dowiedzieć się więcej, zobacz [co to jest dostęp do aplikacji i logowanie JEDNOkrotne w usłudze Azure AD? — logowanie JEDNOkrotne oparte na haśle](./what-is-single-sign-on.md).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Wskazanie, że metadane w formularzach wymagają ponownego przechwycenia

Gdy aplikacje zmieniają układ HTML, może być konieczne ponowne przechwycenie metadanych w celu dostosowania zmian. Typowe objawy wskazujące, że układ HTML ma zmianę:

- Użytkownicy raportujący, że kliknięcie aplikacji otrzymuje "Zablokowano" na stronie logowania
- Użytkownicy raportujący, że nazwa użytkownika lub hasło nie są wypełnione

#### <a name="shared-accounts"></a>Współużytkowane konta

Z perspektywy logowania aplikacje z udostępnionymi kontami nie różnią się od aplikacji galerii, która używa hasła SSO dla poszczególnych użytkowników. Istnieje jednak kilka dodatkowych kroków, które należy wykonać podczas planowania i konfigurowania aplikacji przeznaczonej do korzystania z udostępnionych kont:

1. Pracuj z użytkownikami biznesowymi aplikacji, aby udokumentować następujące elementy:
   1. Zbiór użytkowników w organizacji, którzy będą korzystać z aplikacji
   1. Istniejący zestaw poświadczeń w aplikacji skojarzonej z zestawem użytkowników 
1. Dla każdej kombinacji zestawu i poświadczeń użytkownika należy utworzyć grupę zabezpieczeń w chmurze lub lokalnie zgodnie z wymaganiami.
1. Zresetuj poświadczenia udostępnione. Po wdrożeniu aplikacji w usłudze Azure AD użytkownicy nie potrzebują hasła do konta współużytkowanego. Ponieważ usługa Azure AD będzie przechowywać hasło, rozważ ustawienie jej jako bardzo długiej i złożonej. 
1. Skonfiguruj automatyczne Przerzucanie hasła, jeśli jest ono obsługiwane przez aplikację. W ten sposób, nawet administrator, który przeprowadził wstępną konfigurację, będzie znać hasło do konta udostępnionego. 

## <a name="plan-your-authentication-method"></a>Zaplanuj metodę uwierzytelniania

Wybór odpowiedniej metody uwierzytelniania jest kluczową pierwszą decyzją dotyczącą konfigurowania rozwiązania do tworzenia tożsamości hybrydowej usługi Azure AD. Zaimplementuj metodę uwierzytelniania skonfigurowaną za pomocą Azure AD Connect, która również udostępnia użytkownikom w chmurze.

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącą infrastrukturę, złożoność i koszt implementacji własnego wyboru. Te czynniki są różne dla każdej organizacji i mogą ulec zmianie w czasie. Należy wybrać ten, który najlepiej pasuje do konkretnego scenariusza. Aby uzyskać więcej informacji, zobacz [Wybieranie odpowiedniej metody uwierzytelniania dla Azure Active Directory rozwiązanie do tworzenia tożsamości hybrydowej](../hybrid/choose-ad-authn.md).

## <a name="plan-your-security-and-governance"></a>Planowanie zabezpieczeń i zarządzania 

Tożsamość to nowy podstawowy obszar Pivot dotyczący zabezpieczeń i inwestycji, ponieważ obwódy sieci stają się coraz bardziej porowate i mniej efektywne w przypadku wybuchu urządzeń BYOD i aplikacji w chmurze. 

### <a name="plan-access-reviews"></a>Planowanie przeglądów dostępu

[Przeglądy dostępu](../governance/create-access-review.md) pozwalają organizacjom efektywnie zarządzać członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisaniami ról. Należy zaplanować regularne przeglądanie dostępu użytkowników, aby upewnić się, że tylko odpowiednie osoby mają stały dostęp.

Niektóre z najważniejszych tematów do zaplanowania podczas konfigurowania przeglądów dostępu obejmują:

1. Zidentyfikowanie erze do przeglądów dostępu, które pasują do potrzeb Twojej firmy. Może to być tak częste co tydzień, co miesiąc, co rok lub jako ćwiczenie na żądanie.

1. Utwórz grupy reprezentujące recenzentów raportów dostępu do aplikacji. Musisz się upewnić, że najbardziej zaznajomieni z aplikacją i jej użytkownikami docelowymi oraz przypadkami użycia są uczestnicy recenzji dostępu

1. Zakończenie przeglądu dostępu obejmuje uprawnienia dostępu do aplikacji dla użytkowników, którzy nie potrzebują już dostępu. Zaplanuj obsługę potencjalnych żądań pomocy technicznej od użytkowników, którym odmówiono dostępu. Usunięty użytkownik pozostanie usunięty w usłudze Azure AD przez 30 dni, podczas którego może zostać przywrócony przez administratora w razie potrzeby. Po 30 dniach użytkownik jest trwale usuwany. Za pomocą portalu Azure Active Directory Administrator globalny może jawnie usunąć niedawno usuniętego użytkownika przed osiągnięciem tego okresu.

### <a name="plan-auditing"></a>Inspekcja planu

Usługa Azure AD udostępnia [raporty zawierające informacje techniczne i biznesowe](../reports-monitoring/overview-reports.md). 

Dostępne są zarówno raporty dotyczące zabezpieczeń, jak i działania. Raporty zabezpieczeń przedstawiają użytkowników oflagowanych w celu ryzyka oraz ryzykowne logowania. Raporty dotyczące działań pomagają zrozumieć zachowanie użytkowników w organizacji, wyświetlając szczegóły działania związanego z logowaniem i dostarczając wszystkie identyfikatory logowania. Raporty umożliwiają zarządzanie ryzykiem, zwiększanie produktywności i monitorowanie zgodności.

| Typ raportu | Przegląd dostępu | Raporty dotyczące zabezpieczeń | Raport logowania |
|-------------|---------------|------------------|----------------|
| Użyj do przejrzenia | Uprawnienia i użycie aplikacji. | Potencjalnie naruszone konta | Kto uzyskuje dostęp do aplikacji |
| Potencjalni akcje | Inspekcja dostępu; Odwołaj uprawnienia | Odwołaj dostęp; Wymuś Resetowanie zabezpieczeń | Odwołaj dostęp |

Usługa Azure AD zachowuje większość danych inspekcji przez 30 dni i udostępnia dane za pomocą portalu administracyjnego platformy Azure albo za pomocą interfejsu API, który można pobrać do systemów analizy.

### <a name="consider-using-microsoft-cloud-application-security"></a>Rozważ użycie Microsoft Cloud zabezpieczenia aplikacji

Microsoft Cloud App Security (MCAS) to rozwiązanie brokera zabezpieczeń usługi Cloud Access (CASB). Zapewnia ona wgląd w aplikacje i usługi w chmurze, zapewnia zaawansowane analizy umożliwiające identyfikację i walkę dotyczące środowiskach oraz pozwala kontrolować sposób poruszania się danych.

Wdrożenie MCAS umożliwia:

- Użyj Cloud Discovery, aby zamapować i zidentyfikować środowisko chmury oraz aplikacje w chmurze używane przez organizację.
- Zaakceptowanie i anulowanie aplikacji w chmurze
- Korzystaj z łatwych w do wdrożenia łączników aplikacji, które wykorzystują interfejsy API dostawcy, aby widzieć widoczność i nadzór nad aplikacjami, z którymi się łączysz
- Korzystanie z Kontrola dostępu warunkowego aplikacji ochrony w celu uzyskania wglądu w czasie rzeczywistym i kontroli nad dostępem i działaniami w ramach aplikacji w chmurze
- Pomaga w zapewnianiu ciągłej kontroli według ustawienia, a następnie ciągłego dostrajania, zasad.

Kontrola sesji Microsoft Cloud Application Security (MCAS) jest dostępna dla dowolnej przeglądarki na dowolnej głównej platformie w dowolnym systemie operacyjnym. Aplikacje mobilne i aplikacje klasyczne mogą być również blokowane lub dozwolone. Dzięki natywnej integracji z usługą Azure AD wszystkie aplikacje, które są skonfigurowane przy użyciu protokołu SAML lub mogą być obsługiwane przez funkcję Open ID Connect Apps z logowaniem jednokrotnym w usłudze Azure AD, w tym [kilka polecanych aplikacji](/cloud-app-security/proxy-intro-aad).

Aby uzyskać informacje na temat MCAS, zobacz [omówienie Microsoft Cloud App Security](/cloud-app-security/what-is-cloud-app-security). MCAS to usługa subskrypcji oparta na użytkownikach. Szczegóły dotyczące licencjonowania można przejrzeć w [arkuszu danych licencjonowania MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Korzystanie z dostępu warunkowego

Za pomocą dostępu warunkowego można zautomatyzować decyzje dotyczące kontroli dostępu oparte na kryteriach dla aplikacji w chmurze.

Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwszy wiersz obrony przed scenariuszami, takimi jak ataki typu "odmowa usługi" (DoS), ale mogą używać sygnałów z tych zdarzeń w celu określenia dostępu. Przykładowo można użyć poziomu ryzyka logowania, lokalizacji żądania itd. Aby uzyskać więcej informacji na temat dostępu warunkowego, zobacz [Omówienie](../conditional-access/plan-conditional-access.md) i [Plan wdrażania](../conditional-access/plan-conditional-access.md).

## <a name="azure-sso-technical-requirements"></a>Wymagania techniczne logowania jednokrotnego na platformie Azure

W poniższej sekcji przedstawiono wymagania dotyczące konfigurowania konkretnej aplikacji, w tym niezbędne środowiska, punkty końcowe, mapowanie, wymagane atrybuty, certyfikaty i protokoły. Te informacje będą potrzebne do skonfigurowania logowania jednokrotnego w [portalu usługi Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Szczegóły mechanizmu uwierzytelniania

W przypadku wszystkich wstępnie zintegrowanych aplikacji SaaS firma Microsoft udostępnia samouczek i nie potrzebuje tych informacji. Jeśli aplikacja nie znajduje się w portalu Marketplace/galerii aplikacji, może być konieczne zebranie następujących fragmentów danych:

- **Bieżący dostawca tożsamości używa aplikacji na potrzeby logowania jednokrotnego, jeśli ma zastosowanie** — na przykład: AD FS, serwera pingfederate, usługi okta
- **Protokoły obsługiwane przez aplikację docelową** — na przykład SAML 2,0, OpenID Connect Connect, OAuth, Forms-Based Auth, WS-Trust
- **Protokół konfigurowany za pomocą usługi Azure AD** — na przykład SAML 2,0 lub 1,1, OpenID Connect Connect, OAuth, oparte na formularzach, WS-Fed

### <a name="attribute-requirements"></a>Wymagania dotyczące atrybutów

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowań atrybutów między obiektami użytkowników usługi Azure AD i obiektami użytkowników aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów, takimi jak grupy. Zaplanuj Mapowanie atrybutów użytkownika z usługi Azure AD do aplikacji i [Dostosuj domyślne mapowania atrybutów](../app-provisioning/customize-application-attributes.md) zgodnie z potrzebami biznesowymi.

### <a name="certificate-requirements"></a>Wymagania certyfikatu

Certyfikat aplikacji musi być aktualny lub istnieje ryzyko, że użytkownicy nie mogą uzyskać dostępu do aplikacji. Większość certyfikatów aplikacji SaaS jest dobra przez 36 miesięcy. Ten czas trwania certyfikatu można zmienić w bloku aplikacji. Pamiętaj o udokumentowaniu wygaśnięcia i Dowiedz się, jak zarządzać odnowieniem certyfikatu. 

Istnieją dwa sposoby zarządzania certyfikatami. 

- **Automatyczne Przerzucanie certyfikatów** — firma Microsoft obsługuje [Przerzucanie klucza podpisywania w usłudze Azure AD](../develop/active-directory-signing-key-rollover.md). Chociaż jest to preferowana metoda zarządzania certyfikatami, nie wszystkie niezależne dostawcy oprogramowania obsługują ten scenariusz.

- **Ręczna aktualizacja** — każda aplikacja ma swój własny certyfikat, który wygaśnie na podstawie sposobu jego zdefiniowania. Przed wygaśnięciem certyfikatu aplikacji Utwórz nowy certyfikat i wyślij go do niezależnego dostawcy oprogramowania. Te informacje można ściągnąć z metadanych Federacji. [Więcej informacji na temat metadanych Federacji można znaleźć tutaj.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>Zaimplementuj Logowanie jednokrotne

Aby zaplanować i wdrożyć rozwiązanie w organizacji, należy użyć następujących faz:

### <a name="user-configuration-for-sso"></a>Konfiguracja użytkownika dla logowania jednokrotnego

- **Identyfikowanie użytkowników testowych**

   Skontaktuj się z właścicielem aplikacji i poproś o utworzenie co najmniej trzech użytkowników testowych w aplikacji. Upewnij się, że informacje, które będą używane jako identyfikator podstawowy, są poprawnie wypełnione i zgodne z atrybutem dostępnym w usłudze Azure AD. W większości przypadków zostanie ono zamapowane na "NameID" dla aplikacji opartych na protokole SAML. W przypadku tokenów JWT jest to "preferred_username".
   
   Utwórz użytkownika w usłudze Azure AD ręcznie jako użytkownik oparty na chmurze lub zsynchronizuj użytkownika z lokalnego przy użyciu aparatu synchronizacji Azure AD Connect. Upewnij się, że informacje są zgodne z oświadczeniami wysyłanymi do aplikacji.

- **Konfigurowanie logowania jednokrotnego**

   Z [listy aplikacji](../saas-apps/tutorial-list.md)Znajdź i Otwórz samouczek rejestracji Jednokrotnej dla swojej aplikacji, a następnie postępuj zgodnie z instrukcjami samouczka, aby pomyślnie skonfigurować aplikację SaaS.

   Jeśli nie możesz znaleźć aplikacji, zapoznaj się z [dokumentacją aplikacji niestandardowych](./configure-saml-single-sign-on.md). Zapoznaj się z tematem jak dodać aplikację, która nie znajduje się w galerii usługi Azure AD.

   Opcjonalnie można użyć oświadczeń wystawionych w tokenie SAML dla aplikacji przedsiębiorstwa przy użyciu [dokumentacji wskazówek firmy Microsoft](../develop/active-directory-claims-mapping.md). Upewnij się, że mapowania są zgodne z oczekiwaniami, które chcesz otrzymywać w odpowiedzi SAML dla aplikacji. Jeśli wystąpią problemy podczas konfiguracji, należy skorzystać z naszych wskazówek dotyczących [debugowania integracji z logowaniem JEDNOkrotnym](./debug-saml-sso-issues.md).

### <a name="provide-sso-change-communications-to-end-users"></a>Zapewnianie użytkownikom końcowym komunikacji z logowaniem jednokrotnym

Zaimplementuj plan komunikacji. Zadbaj o to, aby użytkownicy końcowi wiedzieli, że zmiana została wprowadzona, kiedy dotarła do Ciebie, co zrobić teraz i jak szukać pomocy.

### <a name="verify-end-user-scenarios-for-sso"></a>Weryfikowanie scenariuszy użytkowników końcowych na potrzeby logowania jednokrotnego

Do przeprowadzania testów na urządzeniach należących do firmy i osobistych można użyć następujących przypadków testowych, aby upewnić się, że konfiguracje logowania jednokrotnego działają zgodnie z oczekiwaniami. W poniższych scenariuszach przyjęto założenie, że użytkownik przechodzi do adresu URL aplikacji i przechodząc przez przepływ uwierzytelniania inicjowany przez dostawcę usług (proces uwierzytelniania inicjowany przez usługę SP).

| Scenariusz | Oczekiwany wynik dla zainicjowanego przez użytkownika przepływu uwierzytelniania przy użyciu dodatku SP |
|----------|---------------------------------------------------|
| Zaloguj się do aplikacji za pomocą programu IE w sieci firmowej. | Zintegrowane uwierzytelnianie systemu Windows (IWA) występuje bez dodatkowych wskazówek. |
| Zaloguj się do aplikacji przy użyciu programu IE, a poza siecią Corpnet przy użyciu nowej próby logowania. | Monit oparty na formularzach na serwerze AD FS. Użytkownik pomyślnie zalogował się i w przeglądarce zostanie wyświetlony komunikat z uwierzytelnianiem MFA. |
| Zaloguj się do aplikacji przy użyciu programu IE poza siecią Corpnet z bieżącą sesją i nigdy nie przeprowadzono uwierzytelniania MFA. | Użytkownik nie otrzymuje monitu o podanie pierwszego czynnika. Użytkownik otrzymuje monit dotyczący uwierzytelniania wieloskładnikowego. |
| Zaloguj się do aplikacji przy użyciu programu IE, a poza siecią Corpnet z bieżącą sesją, w tej sesji została już wykonana usługa MFA. | Użytkownik nie otrzymuje monitu o podanie pierwszego czynnika. Użytkownik nie otrzymuje usługi MFA. Użytkownik SSOs do aplikacji. |
| Zaloguj się do aplikacji za pomocą przeglądarki Chrome/Firefox/Safari, gdy nie ma sieci firmowej z bieżącą sesją i już wykonała uwierzytelnianie MFA w tej sesji. | Użytkownik nie otrzymuje monitu o podanie pierwszego czynnika. Użytkownik nie otrzymuje usługi MFA. Logowanie jednokrotne użytkownika do aplikacji. |
| Zaloguj się do aplikacji za pomocą przeglądarki Chrome/Firefox/Safari, gdy w sieci firmowej zostanie wykorzystana nowa próba logowania. | Monit oparty na formularzach na serwerze AD FS. Użytkownik pomyślnie zalogował się i w przeglądarce zostanie wyświetlony komunikat z uwierzytelnianiem MFA. |
| Zaloguj się do aplikacji za pomocą przeglądarki Chrome/Firefox w sieci firmowej z bieżącą sesją. | Użytkownik nie otrzymuje monitu o podanie pierwszego czynnika. Użytkownik nie otrzymuje usługi MFA. Logowanie jednokrotne użytkownika do aplikacji. |
| Zaloguj się do aplikacji przy użyciu aplikacji mobilnej aplikacji z nową próbą logowania. | Monit oparty na formularzach na serwerze AD FS. Użytkownik pomyślnie zalogował się i poprosi klienta ADAL o uwierzytelnianie MFA. |
| Nieautoryzowany użytkownik próbuje zalogować się do aplikacji przy użyciu adresu URL logowania. | Monit oparty na formularzach na serwerze AD FS. Użytkownik nie może zalogować się przy użyciu pierwszego czynnika. |
| Autoryzowany użytkownik próbuje się zalogować, ale wprowadzi nieprawidłowe hasło. | Użytkownik przechodzi do adresu URL aplikacji i otrzymuje błąd nazwy użytkownika/hasła. |
| Autoryzowany użytkownik klika link w wiadomości e-mail i jest już uwierzytelniony. | Użytkownik klika pozycję URL i jest zalogowany do aplikacji bez dodatkowych wskazówek. |
| Autoryzowany użytkownik klika link w wiadomości e-mail i nie został jeszcze uwierzytelniony. | Użytkownik klika adres URL i monituje o uwierzytelnienie przy użyciu pierwszego czynnika. |
| Autoryzowany użytkownik loguje się do aplikacji przy użyciu programu Application Mobile App (SP-zainicjowany) z nową próbą logowania. | Monit oparty na formularzach na serwerze AD FS. Użytkownik pomyślnie zalogował się i poprosi klienta ADAL o uwierzytelnianie MFA. |
| Nieautoryzowany użytkownik próbuje zalogować się do aplikacji przy użyciu adresu URL logowania (zainicjowanego przez SP). | Monit oparty na formularzach na serwerze AD FS. Użytkownik nie może zalogować się przy użyciu pierwszego czynnika. |
| Autoryzowany użytkownik próbuje się zalogować, ale wprowadzi nieprawidłowe hasło.| Użytkownik przechodzi do adresu URL aplikacji i otrzymuje błąd nazwy użytkownika/hasła. |
| Autoryzowany użytkownik wylogowuje się, a następnie zaloguje ponownie. | W przypadku skonfigurowania adresu URL logowania użytkownik jest logowany ze wszystkich usług i monituje o uwierzytelnienie. |
| Autoryzowany użytkownik wylogowuje się, a następnie zaloguje ponownie. | Jeśli adres URL wylogowania nie jest skonfigurowany, użytkownik zostanie automatycznie zalogowany przy użyciu istniejącego tokenu z istniejącej sesji przeglądarki Azure AD. |
| Autoryzowany użytkownik klika link w wiadomości e-mail i jest już uwierzytelniony. | Użytkownik klika pozycję URL i jest zalogowany do aplikacji bez dodatkowych wskazówek. |
| Autoryzowany użytkownik klika link w wiadomości e-mail i nie został jeszcze uwierzytelniony. | Użytkownik klika adres URL i monituje o uwierzytelnienie przy użyciu pierwszego czynnika. |

## <a name="manage-sso"></a>Zarządzanie logowaniem jednokrotnym

Ta sekcja zawiera opis wymagań i zaleceń dotyczących pomyślnego zarządzania logowaniem jednokrotnym.

### <a name="required-administrative-roles"></a>Wymagane role administracyjne

Zawsze używaj roli z najmniejszą liczbą uprawnień dostępnych do wykonania wymaganego zadania w ramach Azure Active Directory. Firma Microsoft zaleca, aby [zapoznać się z różnymi rolami, które są dostępne](../roles/permissions-reference.md) , i wybrać jedną z nich, aby rozwiązać Twoje potrzeby dla każdej osoby dla tej aplikacji. Niektóre role mogą wymagać tymczasowego zastosowania i usunąć po zakończeniu wdrażania.

| Osoba| Role | Rola usługi Azure AD (jeśli jest wymagana) |
|--------|-------|-----------------------------|
| Administrator pomocy technicznej | Obsługa warstwy 1 | Brak |
| Administrator tożsamości | Konfigurowanie i debugowanie w przypadku problemów mających wpływ na usługę Azure AD | Administrator globalny |
| Administrator aplikacji | Zaświadczanie użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami | Brak |
| Administratorzy infrastruktury | Właściciel przerzucania certyfikatu | Administrator globalny |
| Właściciel firmy/uczestnik projektu | Zaświadczanie użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami | Brak |

Zalecamy używanie [Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) do zarządzania rolami w celu zapewnienia dodatkowej inspekcji, kontroli i przeglądu dostępu użytkownikom z uprawnieniami do katalogów.

### <a name="sso-certificate-lifecycle-management"></a>Zarządzanie cyklem życia certyfikatu SSO

Ważne jest, aby zidentyfikować odpowiednie role i listy dystrybucyjne poczty e-mail z zadaniami zarządzania cyklem życia certyfikatu podpisywania między usługą Azure AD a aplikacją, która jest konfigurowana przy użyciu logowania jednokrotnego. Poniżej przedstawiono niektóre z kluczowych ról, które zalecamy:

- Właściciel do aktualizowania właściwości użytkownika w aplikacji
- Właściciel w wywołaniu do obsługi podziału/usuwania aplikacji
- Ściśle monitorowana lista dystrybucyjna poczty e-mail dla powiadomień o zmianach związanych z certyfikatem

Maksymalny okres istnienia certyfikatu wynosi trzy lata. Zalecamy ustanowienie procesu dotyczącego sposobu obsługi zmiany certyfikatu między usługą Azure AD a aplikacją. Może to pomóc w zapobieganiu lub minimalizacji przestojów spowodowanych wygaśnięciem certyfikatu lub wymuszeniem przerzucania certyfikatów.

### <a name="rollback-process"></a>Proces wycofywania

Po zakończeniu testowania w oparciu o przypadki testowe czas przejścia do środowiska produkcyjnego w środowisku produkcyjnym. Przechodzenie do środowiska produkcyjnego spowoduje zaimplementowanie planowanych i przetestowanych konfiguracji w dzierżawie produkcyjnej oraz wycofanie jej do użytkowników. Ważne jest jednak, aby zaplanować czynności wykonywane w przypadku, gdy wdrożenie nie zostanie zaplanowane. Jeśli konfiguracja logowania jednokrotnego nie powiedzie się podczas wdrażania, należy zrozumieć, jak wyeliminować ewentualne przestoje i ograniczyć wpływ na użytkowników.

Dostępność metod uwierzytelniania w aplikacji określi najlepszą strategię. Zawsze upewnij się, że masz szczegółową dokumentację dla właścicieli aplikacji dokładnie, jak wrócić do oryginalnego stanu konfiguracji logowania na wypadek, gdyby wdrożenie zostało uruchomione w ramach problemów.

- **Jeśli aplikacja obsługuje wielu dostawców tożsamości**, na przykład LDAP i AD FS i polecenie ping, nie usuwaj istniejącej konfiguracji logowania jednokrotnego podczas wdrażania. Zamiast tego należy wyłączyć ją podczas migracji, jeśli trzeba będzie ją później zmienić. 

- **Jeśli aplikacja nie obsługuje wielu dostawców tożsamości** , ale umożliwia użytkownikom logowanie się przy użyciu uwierzytelniania opartego na formularzach (username/Password), należy się upewnić, że użytkownicy mogą wrócić do tego podejścia w przypadku niepowodzenia nowego wdrożenia konfiguracji logowania jednokrotnego.

### <a name="access-management"></a>Zarządzanie dostępem

Zalecamy wybranie skalowalnego podejścia do zarządzania dostępem do zasobów. Typowe podejścia obejmują używanie grup lokalnych przez synchronizację za pośrednictwem Azure AD Connect, [Tworzenie grup dynamicznych w usłudze Azure AD na podstawie atrybutów użytkownika](../enterprise-users/groups-dynamic-membership.md)lub tworzenie [grup samoobsługi](../enterprise-users/groups-self-service-management.md) w usłudze Azure AD zarządzanych przez właściciela zasobu.

### <a name="monitor-security"></a>Monitorowanie zabezpieczeń

Zalecamy skonfigurowanie regularnego erze, w którym można przejrzeć różne aspekty zabezpieczeń aplikacji SaaS i wykonać wszelkie wymagane akcje zaradcze.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniższe linki prowadzą do rozwiązywania problemów. Możesz chcieć utworzyć konkretny Przewodnik dla personelu pomocy technicznej, który obejmuje te scenariusze, oraz kroki umożliwiające ich rozwiązanie.

#### <a name="consent-issues"></a>Problemy z zgodą

- [Nieoczekiwany błąd zgody](./application-sign-in-unexpected-user-consent-prompt.md)

- [Błąd zgody użytkownika](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Problemy dotyczące logowania

- [Problemy z logowaniem się z portalu niestandardowego](./application-sign-in-other-problem-access-panel.md)

- [Problemy podczas logowania się z poziomu sekcji Moje aplikacje](./application-sign-in-other-problem-access-panel.md)

- [Błąd na stronie logowania aplikacji](./application-sign-in-problem-application-error.md)

- [Problem z logowaniem do aplikacji firmy Microsoft](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problemy z logowaniem jednokrotnym dla aplikacji wymienionych w galerii aplikacji platformy Azure

- [Problem z logowaniem jednokrotnym hasła dla aplikacji wymienionych w galerii aplikacji platformy Azure](./troubleshoot-password-based-sso.md) 

- [Problem z federacyjnym logowaniem jednokrotnym dla aplikacji wymienionych w galerii aplikacji platformy Azure](./application-sign-in-problem-federated-sso-gallery.md)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problemy z logowaniem jednokrotnym dla aplikacji, które nie są wymienione w galerii aplikacji platformy Azure

- [Problem z logowaniem jednokrotnym dla aplikacji, które nie znajdują się w galerii aplikacji platformy Azure](./troubleshoot-password-based-sso.md) 

- [Problem z federacyjnym logowaniem jednokrotnym dla aplikacji, które nie są wymienione w galerii aplikacji platformy Azure](./application-sign-in-problem-federated-sso-gallery.md)

## <a name="next-steps"></a>Następne kroki

[Debugowanie logowania jednokrotnego opartego na protokole SAML](./debug-saml-sso-issues.md)

[Mapowanie roszczeń dla aplikacji za pośrednictwem programu PowerShell](../develop/active-directory-claims-mapping.md)

[Dostosowywanie oświadczeń wystawionych w tokenie SAML](../develop/active-directory-saml-claims-customization.md)

[Protokół SAML logowania jednokrotnego](../develop/single-sign-on-saml-protocol.md)

[Pojedynczy Sign-Out protokół SAML](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md) (dla użytkowników zewnętrznych, takich jak partnerzy i dostawcy)

[Dostęp warunkowy w usłudze Azure AD](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[Dostęp z użyciem logowania jednokrotnego](./what-is-single-sign-on.md)

[Samouczek logowania jednokrotnego dla aplikacji](../saas-apps/tutorial-list.md)

[Pobieranie oficjalnego dokumentu — plan wdrożenia logowania jednokrotnego](https://aka.ms/SSODeploymentPlan)