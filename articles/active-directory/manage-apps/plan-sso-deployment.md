---
title: Planowanie wdrożenia Azure Active Directory logowaniem pojedynczym
description: Przewodnik ułatwiający planowanie i wdrażanie logowania jednokrotnego oraz zarządzanie nimi w organizacji.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: iangithinji
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 3f395eaf21dce0077c1239ef2251973aeb5faa70
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107375614"
---
# <a name="plan-a-single-sign-on-deployment"></a>Planowanie wdrożenia logowania jednokrotnego

Logowanie jednokrotne oznacza uzyskiwanie dostępu do wszystkich aplikacji i zasobów, których potrzebuje użytkownik, logując się tylko raz przy użyciu jednego konta użytkownika. Dzięki użyciu logowania jednokrotnego użytkownicy mogą uzyskać dostęp do wszystkich potrzebnych aplikacji bez konieczności uwierzytelniania się po raz drugi.

## <a name="benefits-of-sso"></a>Zalety logowania jednokrotnego

Logowanie jednokrotne (SSO) zwiększa bezpieczeństwo i wygodę logowania użytkowników do aplikacji w usłudze Azure Active Directory (Azure AD). 

Wiele organizacji korzysta z aplikacji typu oprogramowanie jako usługa (SaaS), takich jak Microsoft 365, Box i Salesforce, aby zwiększyć produktywność użytkowników końcowych. W przeszłości personel IT musiał indywidualnie tworzyć i aktualizować konta użytkowników w każdej aplikacji SaaS, a użytkownicy musieli pamiętać hasło dla każdej z nich.

Aplikacja Azure Marketplace ponad 3000 aplikacji ze wstępnie zintegrowanymi połączeniami logowania jednokrotnego, co ułatwia ich integrację z dzierżawą.

## <a name="licensing"></a>Licencjonowanie

- **Licencjonowanie usługi Azure AD** — logowanie jednokrotne dla wstępnie zintegrowanych aplikacji SaaS jest bezpłatne. Jednak liczba obiektów w katalogu i funkcje, które chcesz wdrożyć, mogą wymagać dodatkowych licencji. Aby uzyskać pełną listę wymagań dotyczących licencji, zobacz [Azure Active Directory Cennik.](https://azure.microsoft.com/pricing/details/active-directory/)
- **Licencjonowanie** aplikacji — potrzebne są odpowiednie licencje dla aplikacji SaaS, aby spełnić potrzeby biznesowe. We współpracy z właścicielem aplikacji ustal, czy użytkownicy przypisani do aplikacji mają odpowiednie licencje dla swoich ról w aplikacji. Jeśli usługa Azure AD zarządza automatyczną aprowizą na podstawie ról, role przypisane w usłudze Azure AD muszą być zgodne z liczbą licencji należących do aplikacji. Niewłaściwa liczba licencji należących do aplikacji może prowadzić do błędów podczas aprowizowania/aktualizowania użytkownika.

## <a name="plan-your-sso-team"></a>Planowanie zespołu logowania jednokrotnego

- **Angażowanie odpowiednich uczestników** projektu — jeśli projekty technologiczne nie powiodą się, zwykle wynika to z niedopasowanych oczekiwań w zakresie wpływu, wyników i obowiązków. Aby uniknąć tych pułapek, [upewnij](../fundamentals/active-directory-deployment-plans.md) się, że angażowanie odpowiednich uczestników projektu i zrozumienie ich ról.
- **Planowanie komunikacji** — komunikacja ma kluczowe znaczenie dla sukcesu każdej nowej usługi. Proaktywne komunikowanie się z użytkownikami o tym, jak zmieni się ich środowisko, kiedy to się zmieni i jak uzyskać pomoc techniczną, jeśli wystąpią problemy. Przejrzyj opcje dotyczące [sposobu, w jaki użytkownicy](end-user-experiences.md)końcowi będą mieć dostęp do swoich aplikacji z obsługą logowania jednokrotnego, i osłoń komunikację w celu dopasowania do wybranej opcji. 

## <a name="plan-your-sso-protocol"></a>Planowanie protokołu logowania jednokrotnego

Implementacja logowania jednokrotnego oparta na protokołach federacji zwiększa bezpieczeństwo, niezawodność i środowisko użytkownika końcowego oraz jest łatwiejsza do zaimplementowania. Wiele aplikacji jest wstępnie zintegrowanych w usłudze Azure AD z dostępnymi [przewodnikami krok po kroku.](../saas-apps/tutorial-list.md) Możesz je znaleźć na naszej [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Szczegółowe informacje na temat każdej metody logowania jednokrotnego można znaleźć w artykule [Single sign-on to applications in Azure Active Directory](what-is-single-sign-on.md)(Logowanie jednokrotne do aplikacji w aplikacji Azure Active Directory ).

Istnieją dwa podstawowe sposoby, w jakie można umożliwić użytkownikom logowanie się do aplikacji za pomocą logowania pojedynczego:

- **Z federacyjną logowaniem pojedynczym** Usługa Azure AD uwierzytelnia użytkownika w aplikacji przy użyciu konta usługi Azure AD. Ta metoda jest obsługiwana w przypadku aplikacji, które obsługują protokoły takie jak SAML 2.0, WS-Federation lub OpenID Connect, i jest najbogatszym trybem logowania się. Zalecamy używanie federowanych logowania jednokrotnego z usługą Azure AD, gdy aplikacja je obsługuje, zamiast logowania jednokrotnego opartego na hasłach i usług ADFS.

- **Po pierwszym zalogowaniu się do** aplikacji przy użyciu hasła użytkownicy logują się do aplikacji przy użyciu nazwy użytkownika i hasła, aby uzyskać do nich dostęp. Po pierwszym zalogowaniu usługa Azure AD dostarcza nazwę użytkownika i hasło do aplikacji. Logowanie pojedyncze oparte na hasłach umożliwia bezpieczne przechowywanie haseł aplikacji i powtarzanie ich przy użyciu rozszerzenia przeglądarki internetowej lub aplikacji mobilnej. Ta opcja korzysta z istniejącego procesu logowania udostępnianego przez aplikację, umożliwia administratorowi zarządzanie hasłami i nie wymaga od użytkownika znać hasła.

### <a name="considerations-for-federation-based-sso"></a>Zagadnienia dotyczące federacyjnie opartego na rejestracji jednokrotnej

- **Używanie OpenID Connect i protokołu OAuth** — jeśli aplikacja, z która jest nawiązywana połączenie, obsługuje tę funkcję, użyj metody OIDC/OAuth 2.0, aby włączyć logowanie jednokrotne do tej aplikacji. Ta metoda wymaga mniejszej konfiguracji i zapewnia bardziej rozbudowane środowisko użytkownika. Aby uzyskać więcej informacji, zobacz [OAuth 2.0,](../develop/v2-oauth2-auth-code-flow.md) [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)i [Azure Active Directory dewelopera](../develop/index.yml).
- **Konfiguracje punktu końcowego** logowania jednokrotnego opartego na saml — jeśli korzystasz z saml, deweloperzy będą potrzebować określonych informacji przed skonfigurowaniem aplikacji. Aby uzyskać więcej informacji, zobacz [Configure SAML-based single sign-on (Konfigurowanie logowania pojedynczego opartego na saml).](configure-saml-single-sign-on.md)
- **Zarządzanie certyfikatami dla** logowania jednokrotnego opartego na języku SAML — po włączeniu federowego logowania jednokrotnego dla aplikacji usługa Azure AD tworzy certyfikat, który jest domyślnie ważny przez trzy lata. W razie potrzeby można dostosować datę wygaśnięcia tego certyfikatu. Upewnij się, że masz procesy odnawiania certyfikatów przed ich wygaśnięciem. Aby dowiedzieć się więcej, zobacz [Azure AD Managing Certificates (Zarządzanie certyfikatami w usłudze Azure AD).](./manage-certificates-for-federated-single-sign-on.md)

### <a name="considerations-for-password-based-sso"></a>Zagadnienia dotyczące logowania jednokrotnego opartego na hasłach

Korzystanie z usługi Azure AD do logowania jednokrotnego opartego na hasłach wymaga wdrożenia rozszerzenia przeglądarki, które będzie bezpiecznie pobierać poświadczenia i wypełniać formularze logowania. Zdefiniuj mechanizm wdrażania rozszerzenia na dużą skalę przy użyciu [obsługiwanych przeglądarek.](../user-help/my-apps-portal-end-user-access.md) Dostępne są następujące opcje:

- [zasady grupy dla Internet Explorer](my-apps-deployment-plan.md)
- [Menedżer konfiguracji for Internet Explorer](/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Pobieranie i konfiguracja przez użytkownika dla przeglądarek Chrome, Firefox, Microsoft Edge lub IE](../user-help/my-apps-portal-end-user-access.md)

Aby dowiedzieć się więcej, zobacz How to configure password single sign on (Jak [skonfigurować logowanie pojedyncze przy użyciu hasła).](./configure-password-single-sign-on-non-gallery-applications.md)

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Przechwytywanie metadanych formularzy logowania dla aplikacji, które nie są w galerii

Firma Microsoft obsługuje przechwytywanie metadanych w aplikacji internetowej na celu magazynu haseł (przechwytywanie pól nazwy użytkownika i hasła). Przejdź do adresu URL logowania podczas konfigurowania aplikacji w celu przechwycenia metadanych formularzy. Poproś właściciela aplikacji o dokładny adres URL logowania. Te informacje są używane podczas procesu logowania, mapowania poświadczeń usługi Azure AD na aplikację podczas logowania.

Aby dowiedzieć się więcej, zobacz [Co to jest dostęp do aplikacji i logowanie jednokrotne w usłudze Azure AD? — logowanie jednokrotne oparte na hasłach.](./what-is-single-sign-on.md)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Oznaczenia, że metadane w formularzach muszą zostać ponownie podsumowane

Gdy aplikacje zmieniają układ HTML, może być konieczne ponowne podsumowanie metadanych w celu dostosowania ich do zmian. Typowe objawy, które wskazują, że układ HTML zmienił się, to:

- Użytkownicy zgłaszający, że kliknięcie aplikacji zostanie "zablokowane" na stronie logowania
- Użytkownicy zgłaszający, że nazwa użytkownika lub hasło nie zostały wypełnione

#### <a name="shared-accounts"></a>Konta udostępnione

Z perspektywy logowania aplikacje z kontami udostępnionym nie różnią się od aplikacji z galerii, która używa logowania jednokrotnego haseł dla poszczególnych użytkowników. Istnieją jednak pewne dodatkowe kroki wymagane podczas planowania i konfigurowania aplikacji przeznaczonej do korzystania z kont udostępnionych:

1. Aby udokumentować następujące kwestie, należy współpracować z użytkownikami biznesowymi aplikacji:
   1. Zestaw użytkowników w organizacji, którzy będą używać aplikacji
   1. Istniejący zestaw poświadczeń w aplikacji skojarzony z zestawem użytkowników 
1. Dla każdej kombinacji zestawu użytkowników i poświadczeń utwórz grupę zabezpieczeń w chmurze lub lokalnie zgodnie z wymaganiami.
1. Zresetuj poświadczenia udostępnione. Po wdrożeniu aplikacji w usłudze Azure AD poszczególne osoby nie potrzebują hasła do udostępnionego konta. Ponieważ usługa Azure AD będzie przechowywać hasło, rozważ ustawienie go jako bardzo długiego i złożonego. 
1. Skonfiguruj automatyczne przechowanie hasła, jeśli aplikacja je obsługuje. Dzięki temu nawet administrator, który przejmuje początkową konfigurację, nie będzie znał hasła do konta udostępnionego. 

## <a name="plan-your-authentication-method"></a>Planowanie metody uwierzytelniania

Wybór właściwej metody uwierzytelniania jest kluczową pierwszą decyzją podczas konfigurowania rozwiązania tożsamości hybrydowej usługi Azure AD. Zaim implementuj metodę uwierzytelniania, która jest konfigurowana przy użyciu Azure AD Connect, która również akonfigurowała użytkowników w chmurze.

Aby wybrać metodę uwierzytelniania, należy wziąć pod uwagę czas, istniejącą infrastrukturę, złożoność i koszt wdrożenia. Te czynniki są różne dla każdej organizacji i mogą zmieniać się z czasem. Należy wybrać ten, który najlepiej odpowiada konkretnej sytuacji. Aby uzyskać więcej informacji, zobacz Choose the right authentication method for your Azure Active Directory hybrid identity solution (Wybieranie właściwej metody uwierzytelniania dla rozwiązania do Azure Active Directory [tożsamości hybrydowej).](../hybrid/choose-ad-authn.md)

## <a name="plan-your-security-and-governance"></a>Planowanie zabezpieczeń i ładu 

Tożsamość to nowy podstawowy obszar uwagi i inwestycji w bezpieczeństwo, ponieważ obwody sieci stają się coraz bardziej nieporęczne i mniej skuteczne wraz z popularyzną się urządzeniami BYOD i aplikacjami w chmurze. 

### <a name="plan-access-reviews"></a>Planowanie przeglądów dostępu

[Przeglądy dostępu](../governance/create-access-review.md) umożliwiają organizacjom efektywne zarządzanie członkostwem w grupach, dostępem do aplikacji dla przedsiębiorstw i przypisań ról. Należy regularnie przeglądać dostęp użytkowników, aby upewnić się, że tylko odpowiednie osoby nadal mają dostęp.

Niektóre kluczowe tematy, które należy zaplanować podczas konfigurowania przeglądów dostępu, to:

1. Identyfikowanie cadence dla przeglądów dostępu, które pasują do potrzeb twojej firmy. Może to być nawet raz w tygodniu, co miesiąc, co roku lub jako ćwiczenie na żądanie.

1. Tworzenie grup reprezentujących recenzentów raportów dostępu do aplikacji. Należy upewnić się, że uczestnicy projektu najbardziej zaznajomieni z aplikacją i jej docelowymi użytkownikami oraz przypadkami użycia są uczestnikami przeglądów dostępu

1. Ukończenie przeglądu dostępu obejmuje zabranie uprawnień dostępu do aplikacji użytkownikom, którzy już nie potrzebują dostępu. Zaplanuj obsługę potencjalnych żądań pomocy technicznej od odrzuconych użytkowników. Usunięty użytkownik pozostanie usunięty w usłudze Azure AD przez 30 dni, w tym czasie administrator może przywrócić go w razie potrzeby. Po 30 dniach użytkownik jest trwale usuwany. Za pomocą Azure Active Directory portal globalny może jawnie trwale usunąć ostatnio usuniętego użytkownika przed tym okresem.

### <a name="plan-auditing"></a>Planowanie inspekcji

Usługa Azure AD udostępnia [raporty zawierające szczegółowe informacje techniczne i biznesowe.](../reports-monitoring/overview-reports.md) 

Dostępne są zarówno raporty dotyczące zabezpieczeń, jak i działań. Raporty zabezpieczeń pokazują użytkowników oflagowanych w związku z ryzykiem i ryzykownych logów. Raporty aktywności pomagają zrozumieć zachowanie użytkowników w organizacji, szczegółowo opisując aktywność logowania i udostępniając ślady inspekcji wszystkich logowań. Raportów można używać do zarządzania ryzykiem, zwiększania produktywności i monitorowania zgodności.

| Typ raportu | Przegląd dostępu | Raporty dotyczące zabezpieczeń | Raport logowania |
|-------------|---------------|------------------|----------------|
| Użyj, aby przejrzeć | Uprawnienia i użycie aplikacji. | Konta, których bezpieczeństwo może być naruszone | Kto uzyskuje dostęp do aplikacji |
| Potencjalne akcje | Inspekcja dostępu; odwoływanie uprawnień | Odwołaj dostęp; wymuszanie resetowania zabezpieczeń | Odwoływanie dostępu |

Usługa Azure AD przechowuje większość danych inspekcji przez 30 dni i udostępnia je za pośrednictwem portalu administracyjnego platformy Azure lub za pośrednictwem interfejsu API do pobrania w systemach analizy.

### <a name="consider-using-microsoft-cloud-application-security"></a>Rozważ użycie usługi Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) to Cloud Access Security Broker (CASB). Zapewnia wgląd w aplikacje i usługi w chmurze, zapewnia zaawansowaną analizę umożliwiającą identyfikowanie cyberzagrożeń i walkę z cyberzagrożeniami oraz umożliwia kontrolowanie sposobu, w jaki dane są przenoszane.

Wdrożenie mcas umożliwia:

- Użyj Cloud Discovery, aby mapować i identyfikować środowisko chmury oraz aplikacje w chmurze, których używa Twoja organizacja.
- Sanctioning and un-sanction apps in your cloud (Sanctioning and un-sanction apps in your cloud ) (Sanctioning and un-sanction apps in your
- Używaj łatwych do wdrożenia łączników aplikacji, które korzystają z interfejsów API dostawcy, aby uzyskać wgląd i ład w aplikacjach, z których łączysz się
- Użyj Kontrola dostępu warunkowego aplikacji, aby uzyskać wgląd i kontrolę dostępu i działań w aplikacjach w chmurze w czasie rzeczywistym
- Ułatwia ciągłą kontrolę przez ustawienie, a następnie ciągłe dostrajanie zasad.

Kontrola sesji usługi Microsoft Cloud Application Security (MCAS) jest dostępna dla każdej przeglądarki na dowolnej dużej platformie w dowolnym systemie operacyjnym. Aplikacje mobilne i klasyczne również mogą być blokowane lub dozwolone. Dzięki natywnej integracji z usługą Azure AD mogą być obsługiwane wszystkie aplikacje skonfigurowane za pomocą języka SAML lub open ID Connect z logowaniem pojedynczym w usłudze Azure AD, w tym kilka [polecanych aplikacji.](/cloud-app-security/proxy-intro-aad)

Aby uzyskać informacje na temat mcas, zobacz [Microsoft Cloud App Security omówienie.](/cloud-app-security/what-is-cloud-app-security) MCAS to usługa subskrypcji oparta na użytkowniku. Szczegóły licencjonowania można przejrzeć w [arkuszu danych licencjonowania MCAS.](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO)

### <a name="use-conditional-access"></a>Korzystanie z dostępu warunkowego

Za pomocą dostępu warunkowego można zautomatyzować decyzje dotyczące kontroli dostępu na podstawie kryteriów dla aplikacji w chmurze.

Zasady dostępu warunkowego są wymuszane po zakończeniu uwierzytelniania pierwszego czynnika. W związku z tym dostęp warunkowy nie jest przeznaczony jako pierwsza linia obrony dla scenariuszy takich jak ataki typu "odmowa usługi" (DoS), ale może używać sygnałów z tych zdarzeń w celu określenia dostępu. Można na przykład użyć poziomu ryzyka logowania, lokalizacji żądania i tak dalej. Aby uzyskać więcej informacji na temat dostępu warunkowego, [zobacz omówienie](../conditional-access/plan-conditional-access.md) i [plan wdrożenia.](../conditional-access/plan-conditional-access.md)

## <a name="azure-sso-technical-requirements"></a>Wymagania techniczne dotyczące logowania jednokrotnego platformy Azure

W poniższej sekcji szczegółowo przedstawiono wymagania dotyczące konfigurowania określonej aplikacji, w tym niezbędne środowiska, punkty końcowe, mapowanie świadczeń, wymagane atrybuty, certyfikaty i używane protokoły. Te informacje będą potrzebne do skonfigurowania logowania jednokrotnego w portalu [usługi Azure AD.](https://portal.azure.com/)

### <a name="authentication-mechanism-details"></a>Szczegóły mechanizmu uwierzytelniania

W przypadku wszystkich wstępnie zintegrowanych aplikacji SaaS firma Microsoft udostępnia samouczek i te informacje nie będą potrzebne. Jeśli aplikacja nie znajduje się w naszej witrynie Marketplace/galerii aplikacji, może być konieczne zebranie następujących danych:

- **Bieżący dostawca tożsamości używany przez aplikację do** logowania jednokrotnego, jeśli ma to zastosowanie — na przykład: AD FS, PingFederate, Okta
- **Protokoły obsługiwane przez aplikację docelową** — na przykład SAML 2.0, OpenID Connect, OAuth, Forms-Based Auth, WS-Fed, WS-Trust
- **Protokół konfigurowany przy użyciu usługi Azure AD** — na przykład SAML 2.0 lub 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Wymagania dotyczące atrybutów

Istnieje wstępnie skonfigurowany zestaw atrybutów i mapowań atrybutów między obiektami użytkownika usługi Azure AD i obiektami użytkowników poszczególnych aplikacji SaaS. Niektóre aplikacje zarządzają innymi typami obiektów, takimi jak grupy. Zaplanuj mapowanie atrybutów użytkownika z usługi Azure AD na aplikację i dostosuj domyślne mapowania atrybutów [zgodnie](../app-provisioning/customize-application-attributes.md) z potrzebami biznesowymi.

### <a name="certificate-requirements"></a>Wymagania certyfikatu

Certyfikat aplikacji musi być aktualny lub istnieje ryzyko, że użytkownicy nie będą mogli uzyskać dostępu do aplikacji. Większość certyfikatów aplikacji SaaS jest dobra przez 36 miesięcy. Czas trwania certyfikatu można zmienić w bloku aplikacji. Pamiętaj, aby udokumentować datę wygaśnięcia i wiedzieć, jak będziesz zarządzać odnawianiem certyfikatu. 

Istnieją dwa sposoby zarządzania certyfikatami. 

- **Automatyczne przewłaczanie certyfikatów** — firma Microsoft obsługuje [przechowyw klucza podpisywania w usłudze Azure AD.](../develop/active-directory-signing-key-rollover.md) Chociaż jest to preferowana metoda zarządzania certyfikatami, nie wszyscy isV obsługuje ten scenariusz.

- **Ręczne aktualizowanie —** każda aplikacja ma własny certyfikat, który wygasa w zależności od tego, jak jest zdefiniowana. Przed wygaśnięciem certyfikatu aplikacji utwórz nowy certyfikat i wyślij go do isv. Te informacje można ściągnąć z metadanych federacji. [Więcej informacji na temat metadanych federacji można uzyskać tutaj.](../azuread-dev/azure-ad-federation-metadata.md)

## <a name="implement-sso"></a>Implementowanie logowania jednokrotnego

Aby zaplanować i wdrożyć rozwiązanie w organizacji, należy użyć następujących faz:

### <a name="user-configuration-for-sso"></a>Konfiguracja użytkownika na logowanie jednokrotne

- **Identyfikowanie użytkowników testowych**

   Skontaktuj się z właścicielem aplikacji i poproś go o utworzenie co najmniej trzech użytkowników testowych w aplikacji. Upewnij się, że informacje, które będą stosowane jako podstawowy identyfikator, są poprawnie wypełnione i są takie samo jak atrybut dostępny w usłudze Azure AD. W większości przypadków spowoduje to zamapowanie na "NameID" dla aplikacji opartych na saml. W przypadku tokenów JWT jest to "preferred_username".
   
   Utwórz użytkownika w usłudze Azure AD ręcznie jako użytkownik w chmurze lub zsynchronizuj go ze środowiskiem lokalnym przy użyciu Azure AD Connect synchronizacji. Upewnij się, że informacje są takie, jak oświadczenia wysyłane do aplikacji.

- **Konfigurowanie logowania jednokrotnego**

   Na liście [aplikacji znajdź](../saas-apps/tutorial-list.md)i otwórz samouczek logowania jednokrotnego dla aplikacji, a następnie wykonaj kroki samouczka, aby pomyślnie skonfigurować aplikację SaaS.

   Jeśli nie możesz zlokalizować aplikacji, zobacz [dokumentację aplikacji niestandardowej](./configure-saml-single-sign-on.md). W tym tesłudze opisano sposób dodawania aplikacji, która nie znajduje się w galerii usługi Azure AD.

   Opcjonalnie możesz użyć oświadczeń wystawionych w tokenie SAML dla aplikacji przedsiębiorstwa, korzystając z dokumentacji ze wskazówkami [firmy Microsoft.](../develop/active-directory-claims-mapping.md) Upewnij się, że jest to mape na to, czego oczekujesz w odpowiedzi SAML dla aplikacji. Jeśli podczas konfiguracji wystąpią problemy, skorzystaj z naszych wskazówek [dotyczących debugowania integracji logowania jednokrotnego](./debug-saml-sso-issues.md).

### <a name="provide-sso-change-communications-to-end-users"></a>Zapewnianie użytkownikom końcowi komunikacji dotyczącej zmiany logowania jednokrotnego

Zaim implementuj plan komunikacji. Upewnij się, że chcesz, aby użytkownicy końcowi wiedzieli, że nadchodzą zmiany, kiedy przyjdą, co należy teraz zrobić i jak szukać pomocy.

### <a name="verify-end-user-scenarios-for-sso"></a>Weryfikowanie scenariuszy użytkownika końcowego dla logowania jednokrotnego

Następujące przypadki testowe mogą być używane do przeprowadzania testów na urządzeniach firmowych i osobistych, aby upewnić się, że konfiguracje logowania jednokrotnego działają zgodnie z oczekiwaniami. W poniższych scenariuszach założono, że użytkownik przechodzi do adresu URL aplikacji i przechodzi przez przepływ uwierzytelniania zainicjowany przez dostawcę usług (przepływ uwierzytelniania inicjowany przez dostawcę usług).

| Scenariusz | Oczekiwany wynik przepływu uwierzytelniania inicjowanego przez spedycyjną przez użytkownika |
|----------|---------------------------------------------------|
| Zaloguj się do aplikacji przy użyciu programu IE w sieci firmowej. | Integrated Windows Authentication (IWA) występuje bez dodatkowych monitów. |
| Zaloguj się do aplikacji przy użyciu programu IE, podczas gdy poza siecią corpnet z nową próbą logowania. | Monit oparty na formularzach na AD FS Server. Użytkownik pomyślnie loguje się i wyświetla w przeglądarce monity o uwierzytelniania wieloskładnikowego. |
| Zaloguj się do aplikacji przy użyciu programu IE, gdy jest poza siecią firmową z bieżącą sesją i nigdy nie wykonywał uwierzytelniania wieloskładnikowego. | Użytkownik nie otrzymuje monitu o pierwszy czynnik. Użytkownik otrzymuje monit o uwierzytelniania wieloskładnikowego. |
| Zaloguj się do aplikacji przy użyciu programu IE, gdy jest poza siecią firmową z bieżącą sesją i już w tej sesji wykonano uwierzytelniania wieloskładnikowego. | Użytkownik nie otrzymuje monitu o pierwszy czynnik. Użytkownik nie otrzymuje uwierzytelniania wieloskładnikowego. SSO użytkownika do aplikacji. |
| Zaloguj się do aplikacji za pomocą przeglądarki Chrome/Firefox/Safari, gdy jest poza siecią firmową z bieżącą sesją i w tej sesji została już wykonana uwierzytelniania wieloskładnikowego. | Użytkownik nie otrzymuje monitu o pierwszy czynnik. Użytkownik nie otrzymuje uwierzytelniania wieloskładnikowego. Logowanie jednokrotne użytkownika do aplikacji. |
| Zaloguj się do aplikacji za pomocą przeglądarki Chrome/Firefox/Safari, a poza siecią firmowej przy nowej próbie logowania. | Monit oparty na formularzach na AD FS Server. Użytkownik pomyślnie loguje się i wyświetla w przeglądarce monity o uwierzytelniania wieloskładnikowego. |
| Zaloguj się do aplikacji za pomocą przeglądarki Chrome/Firefox w sieci firmowej przy użyciu bieżącej sesji. | Użytkownik nie otrzymuje monitu o pierwszy czynnik. Użytkownik nie otrzymuje uwierzytelniania wieloskładnikowego. Logowanie jednokrotne użytkownika do aplikacji. |
| Zaloguj się do aplikacji za pomocą aplikacji mobilnej przy użyciu nowej próby logowania. | Monit oparty na formularzach na AD FS Server. Użytkownik pomyślnie loguje się i klient biblioteki ADAL monituje o uwierzytelniania WIELOSKŁADNIKOWEGO. |
| Nieautoryzowany użytkownik próbuje zalogować się do aplikacji przy użyciu adresu URL logowania. | Monit oparty na formularzach na AD FS Server. Użytkownik nie może zalogować się przy użyciu pierwszego czynnika. |
| Autoryzowany użytkownik próbuje się zalogować, ale wprowadza nieprawidłowe hasło. | Użytkownik przechodzi do adresu URL aplikacji i otrzymuje błąd złą nazwą użytkownika/hasłem. |
| Autoryzowany użytkownik klika link w wiadomości e-mail i jest już uwierzytelniony. | Użytkownik klika adres URL i jest zalogowany do aplikacji bez dodatkowych monitów. |
| Autoryzowany użytkownik klika link w wiadomości e-mail i nie jest jeszcze uwierzytelniony. | Użytkownik klika adres URL i jest wyświetlany monit o uwierzytelnienie przy użyciu pierwszego czynnika. |
| Autoryzowany użytkownik loguje się do aplikacji przy użyciu aplikacji mobilnej (inicjowanej przez spjęzyka) przy użyciu nowej próby logowania. | Monit oparty na formularzach na AD FS Server. Użytkownik pomyślnie loguje się, a klient biblioteki ADAL wyświetla monit o uwierzytelniania WIELOSKŁADNIKowego. |
| Nieautoryzowany użytkownik próbuje zalogować się do aplikacji przy użyciu adresu URL logowania (inicjowanego przez spjęcie). | Monit oparty na formularzach na AD FS Server. Użytkownik nie może zalogować się przy użyciu pierwszego czynnika. |
| Autoryzowany użytkownik próbuje się zalogować, ale wprowadza nieprawidłowe hasło.| Użytkownik przechodzi do adresu URL aplikacji i otrzymuje błąd złą nazwą użytkownika/hasłem. |
| Autoryzowany użytkownik wyloguje się, a następnie zaloguje się ponownie. | Jeśli skonfigurowano adres URL wylogowania, użytkownik zostanie wylogowany ze wszystkich usług i zostanie wyświetlony monit o uwierzytelnienie. |
| Autoryzowany użytkownik wyloguje się, a następnie zaloguje się ponownie. | Jeśli adres URL wylogowy nie zostanie skonfigurowany, użytkownik zostanie automatycznie zalogowany ponownie przy użyciu istniejącego tokenu z istniejącej sesji przeglądarki usługi Azure AD. |
| Autoryzowany użytkownik klika link w wiadomości e-mail i jest już uwierzytelniony. | Użytkownik klika adres URL i jest zalogowany do aplikacji bez dodatkowych monitów. |
| Autoryzowany użytkownik klika link w wiadomości e-mail i nie jest jeszcze uwierzytelniony. | Użytkownik klika adres URL i jest wyświetlany monit o uwierzytelnienie przy użyciu pierwszego czynnika. |

## <a name="manage-sso"></a>Zarządzanie logowaniem jednokrotnym

W tej sekcji przedstawiono wymagania i zalecenia dotyczące pomyślnego zarządzania logowaniem jednokrotnym.

### <a name="required-administrative-roles"></a>Wymagane role administracyjne

Zawsze używaj roli z najmniejszą dostępną uprawnieniami do wykonania wymaganego zadania w Azure Active Directory. Firma Microsoft zaleca [zapoznanie się z różnymi](../roles/permissions-reference.md) dostępnymi rolami i wybranie odpowiedniej roli do obsługi potrzeb każdej osoby w danej aplikacji. Niektóre role mogą wymagać tymczasowego zastosowania i usunięcia po zakończeniu wdrażania.

| Persona| Role | Rola usługi Azure AD (jeśli jest wymagana) |
|--------|-------|-----------------------------|
| Administrator pomocy technicznej | Obsługa warstwy 1 | Brak |
| Administrator tożsamości | Konfigurowanie i debugowanie, gdy problemy wpływają na usługę Azure AD | Administrator globalny |
| Administrator aplikacji | Zaświadczenia użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami | Brak |
| Administratorzy infrastruktury | Właściciel przewłacania certyfikatu | Administrator globalny |
| Właściciel firmy/uczestnik projektu | Zaświadczenia użytkownika w aplikacji, konfiguracja dla użytkowników z uprawnieniami | Brak |

Zalecamy używanie [usługi Privileged Identity Management](../privileged-identity-management/pim-configure.md) (PIM) do zarządzania rolami w celu zapewnienia dodatkowej inspekcji, kontroli i przeglądu dostępu dla użytkowników z uprawnieniami katalogu.

### <a name="sso-certificate-lifecycle-management"></a>Zarządzanie cyklem życia certyfikatu logowania jednokrotnego

Ważne jest, aby zidentyfikować odpowiednie role i listy dystrybucyjne poczty e-mail, których zadaniem jest zarządzanie cyklem życia certyfikatu podpisywania między usługą Azure AD i aplikacją, która jest konfigurowana przy użyciu logowania pojedynczego. Poniżej podano niektóre z kluczowych ról, które zalecamy:

- Właściciel aktualizowania właściwości użytkownika w aplikacji
- Obsługa łamania/naprawiania aplikacji na wezwanie właściciela
- Ściśle monitorowana lista dystrybucyjna poczty e-mail dla powiadomień o zmianach związanych z certyfikatami

Maksymalny okres istnienia certyfikatu wynosi trzy lata. Zalecamy ustanowienie procesu obsługi zmiany certyfikatu między usługą Azure AD i aplikacją. Może to pomóc zapobiec lub zminimalizować przestoje spowodowane wygaśnięciem certyfikatu lub wymusić przechowanie certyfikatu.

### <a name="rollback-process"></a>Proces wycofywania

Po zakończeniu testowania na podstawie przypadków testowych należy przejść do produkcji z aplikacją. Przejście do produkcji oznacza zaimplementowanie zaplanowanych i przetestowanych konfiguracji w dzierżawie produkcyjnej i wdrożenie ich dla użytkowników. Jednak ważne jest zaplanowanie działań, które należy wykonać w przypadku, gdy wdrożenie nie działa zgodnie z planem. Jeśli konfiguracja logowania jednokrotnego nie powiedzie się podczas wdrażania, musisz zrozumieć, jak zminimalizować wszelkie awarii i zmniejszyć wpływ na użytkowników.

Dostępność metod uwierzytelniania w aplikacji określi najlepszą strategię. Zawsze upewnij się, że masz szczegółową dokumentację dla właścicieli aplikacji dotyczącą dokładnego sposobu powrotu do oryginalnego stanu konfiguracji logowania w przypadku problemów z wdrożeniem.

- **Jeśli twoja aplikacja obsługuje** wielu dostawców tożsamości, na przykład LDAP i AD FS i ping, nie usuwaj istniejącej konfiguracji logowania jednokrotnego podczas jej wycofywania. Zamiast tego należy ją wyłączyć podczas migracji, jeśli trzeba będzie później przełączyć ją z powrotem. 

-  Jeśli aplikacja nie obsługuje wielu identyfikatorów TOŻSAMOŚCI, ale umożliwia użytkownikom logowanie się przy użyciu uwierzytelniania opartego na formularzach (nazwy użytkownika/hasła), upewnij się, że użytkownicy mogą wrócić do tej metody w przypadku niepowodzenia nowego procesu konfiguracji logowania jednokrotnego.

### <a name="access-management"></a>Zarządzanie dostępem

Zalecamy wybranie skalowanej metody zarządzania dostępem do zasobów. Typowe podejścia obejmują korzystanie z grup lokalnych przez synchronizowanie za pośrednictwem usługi Azure AD Connect, tworzenie grup [](../enterprise-users/groups-self-service-management.md) dynamicznych w usłudze [Azure AD](../enterprise-users/groups-dynamic-membership.md)na podstawie atrybutów użytkownika lub tworzenie grup samoobsługi w usłudze Azure AD zarządzanych przez właściciela zasobu.

### <a name="monitor-security"></a>Monitorowanie zabezpieczeń

Zalecamy skonfigurowanie regularnego czasu, w którym przeglądasz różne aspekty zabezpieczeń aplikacji SaaS i wykonujesz wszelkie wymagane działania naprawcze.

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniższe linki prezentują scenariusze rozwiązywania problemów. Możesz utworzyć konkretny przewodnik dla pracowników pomocy technicznej, który zawiera te scenariusze i kroki ich naprawy.

#### <a name="consent-issues"></a>Problemy ze zgodą

- [Nieoczekiwany błąd zgody](./application-sign-in-unexpected-user-consent-prompt.md)

- [Błąd zgody użytkownika](./application-sign-in-unexpected-user-consent-error.md)

#### <a name="sign-in-issues"></a>Problemy dotyczące logowania

- [Problemy z logowaniem z portalu niestandardowego](./application-sign-in-other-problem-access-panel.md)

- [Problemy podczas logowania się z poziomu sekcji Moje aplikacje](./application-sign-in-other-problem-access-panel.md)

- [Błąd na stronie logowania aplikacji](./application-sign-in-problem-application-error.md)

- [Problem z logowaniem do aplikacji firmy Microsoft](./application-sign-in-problem-first-party-microsoft.md)

#### <a name="sso-issues-for-applications"></a>Problemy z logowaniem jednokrotnym dla aplikacji

- [Problem z logowaniem jednokrotnym haseł dla aplikacji](./troubleshoot-password-based-sso.md) 

- [Problemy z logowaniem do aplikacji skonfigurowanych przy użyciu logowania jednokrotnego opartego na protokole SAML](/troubleshoot/azure/active-directory/troubleshoot-sign-in-saml-based-apps)   


## <a name="next-steps"></a>Następne kroki

[Debugowanie logowania jednokrotnego opartego na protokole SAML](./debug-saml-sso-issues.md)

[Mapowanie oświadczenia dla aplikacji za pośrednictwem programu PowerShell](../develop/active-directory-claims-mapping.md)

[Dostosowywanie oświadczeń wystawionych w tokenie SAML](../develop/active-directory-saml-claims-customization.md)

[Protokół SAML logowania pojedynczego](../develop/single-sign-on-saml-protocol.md)

[Pojedynczy Sign-Out SAML](../develop/single-sign-out-saml-protocol.md)

[Azure AD B2B](../external-identities/what-is-b2b.md) (dla użytkowników zewnętrznych, takich jak partnerzy i dostawcy)

[Dostęp warunkowy w usłudze Azure AD](../conditional-access/overview.md)

[Azure Identity Protection](../identity-protection/overview-identity-protection.md)

[Dostęp z użyciem logowania jednokrotnego](./what-is-single-sign-on.md)

[Samouczek dotyczący logowania jednokrotnego aplikacji](../saas-apps/tutorial-list.md)

[Oficjalny oficjalny oficjalny plan wdrażania — pobieranie — plan wdrażania logowania pojedynczego](https://aka.ms/SSODeploymentPlan)