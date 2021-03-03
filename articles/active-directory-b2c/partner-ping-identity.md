---
title: Samouczek konfigurowania Azure Active Directory B2C przy użyciu polecenia ping Identity
titleSuffix: Azure AD B2C
description: Dowiedz się, jak zintegrować uwierzytelnianie Azure AD B2C za pomocą polecenia ping
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 01/20/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: 94e7ae93d05ae8ee35028882e14d8da74814d833
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/02/2021
ms.locfileid: "101650230"
---
# <a name="tutorial-configure-ping-identity-with-azure-active-directory-b2c-for-secure-hybrid-access"></a>Samouczek: Konfigurowanie tożsamości ping przy użyciu Azure Active Directory B2C na potrzeby bezpiecznego dostępu hybrydowego

W tym przykładowym samouczku dowiesz się, jak rozszerzając Azure Active Directory (AD) B2C z  [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) i [serwera pingfederate](https://www.pingidentity.com/en/software/pingfederate.html) , aby zapewnić bezpieczny dostęp hybrydowy.

Wiele istniejących właściwości sieci Web, takich jak Lokacje handlu elektronicznego i aplikacje sieci Web, które są dostępne dla Internetu, są wdrażane za systemem proxy, czasami określanym jako zwrotny system proxy. Te systemy proxy udostępniają różne funkcje, w tym wstępne uwierzytelnianie, wymuszanie zasad i routing ruchu. Przykładowe przypadki użycia obejmują ochronę aplikacji sieci Web z ruchu przychodzącego sieci Web i zapewnianie jednolite zarządzanie sesjami w ramach wdrożeń rozproszonego serwera.

W większości przypadków ta konfiguracja obejmuje warstwę translacji uwierzytelniania, która uzewnętrznia uwierzytelnianie z aplikacji sieci Web. Zwrotne serwery proxy z kolei zapewniają kontekst uwierzytelnionych użytkowników dla aplikacji sieci Web, w prostszy sposób, taki jak wartość nagłówka w postaci zwykłego lub skróconego formularza. W takiej konfiguracji aplikacje nie używają żadnych standardowych tokenów branżowych, takich jak SAML (SAML), OAuth lub Open ID Connect (OIDC), a zamiast tego nie zależą od serwera proxy w celu zapewnienia kontekstu uwierzytelniania i obsługi sesji przy użyciu agenta użytkownika końcowego, takiego jak przeglądarka lub aplikacja natywna. Jako usługa działająca w "man-in-the-middle", serwery proxy mogą zapewnić ostateczną kontrolę sesji. Oznacza to również, że usługa serwera proxy powinna być wysoce wydajna i skalowalna, a nie stać się wąskim gardłem lub single point of failure dla aplikacji za usługą proxy. Diagram jest ilustracją typowej implementacji zwrotnego serwera proxy i przepływu komunikacji.

![obraz przedstawia implementację zwrotnego serwera proxy](./media/partner-ping/reverse-proxy.png)

Jeśli jesteś w sytuacji, w której chcesz przeprowadzić modernizację platformy tożsamości w takich konfiguracjach, zostaną zgłoszone następujące kwestie.

- Jak można wypróbować modernizację aplikacji z modernizacji platformy tożsamości?

- Jak można nawiązać środowisko współistnienia z nowoczesnym i starszym uwierzytelnianiem, zużywanym przez nowoczesnego dostawcę usługi tożsamości?

  a. Jak zwiększyć spójność środowiska użytkownika końcowego?

  b. Jak zapewnić obsługę logowania jednokrotnego w ramach współistniejących aplikacji?

Omawiamy podejście do rozwiązywania takich problemów poprzez integrację Azure AD B2C z technologiami [PingAccess](https://www.pingidentity.com/en/software/pingaccess.html#:~:text=%20Modern%20Access%20Managementfor%20the%20Digital%20Enterprise%20,consistent%20enforcement%20of%20security%20policies%20by...%20More) i [serwera pingfederate](https://www.pingidentity.com/en/software/pingfederate.html) .

## <a name="coexistence-environment"></a>Środowisko współistnienia

Technicznie wydajne proste rozwiązanie, które jest również opłacalne, jest skonfigurowanie systemu zwrotnego serwera proxy do korzystania z nowoczesnego systemu tożsamości, delegowania uwierzytelniania.  
W tym przypadku serwery proxy będą obsługiwały nowoczesne protokoły uwierzytelniania i używają uwierzytelniania opartego na przekierowaniu (pasywnym), które wyśle użytkownika do nowego dostawcy tożsamości (dostawcy tożsamości).

### <a name="azure-ad-b2c-as-an-identity-provider"></a>Azure AD B2C jako dostawca tożsamości

Azure AD B2C ma możliwość zdefiniowania **zasad** , które są związane z różnymi środowiskami użytkownika i zachowaniami, które są nazywane również **przejazdami użytkowników** jako zorganizowanymi z punktu końcowego serwera. Każda z tych zasad uwidacznia punkt końcowy protokołu, który może wykonywać uwierzytelnianie, tak jakby był to dostawcy tożsamości. Dla określonych zasad nie jest wymagana żadna specjalna obsługa. Aplikacja po prostu tworzy w branży standardowe żądanie uwierzytelniania do punktu końcowego uwierzytelniania specyficznego dla protokołu dostępnego w ramach zasad.  
Azure AD B2C można skonfigurować pod kątem współużytkowania tego samego wystawcy dla wielu zasad lub unikatowego wystawcy dla każdej zasady. Każda aplikacja może wskazywać na jedną lub wiele z tych zasad, wykonując natywne żądania uwierzytelniania protokołu i dysku odpowiednie zachowania użytkownika, takie jak logowanie, rejestrowanie i edytowanie profilów. Diagram przedstawia przepływy pracy aplikacji OIDC i SAML.

![obraz przedstawia implementację OIDC i SAML](./media/partner-ping/azure-ad-identity-provider.png)

Chociaż opisany tutaj scenariusz dobrze sprawdza się w przypadku nowoczesnych aplikacji, może być trudne, aby starsze aplikacje mogły prawidłowo przekierować użytkownika, ponieważ żądanie dostępu do aplikacji może nie zawierać kontekstu dla środowiska użytkownika. W większości przypadków warstwa serwera proxy lub zintegrowany Agent aplikacji sieci Web przechwytuje żądanie dostępu.

### <a name="pingaccess-as-a-reverse-proxy"></a>PingAccess jako zwrotny serwer proxy

Wielu klientów wdrożono PingAccess jako zwrotny serwer proxy, aby odtworzyć jedną lub wiele ról zgodnie z wcześniejszym opisem w tym artykule. PingAccess może przechwycić bezpośrednie żądanie w formie typu man-in-the-Middle lub jako przekierowania pochodzącego z agenta uruchomionego na serwerze aplikacji sieci Web.

PingAccess można skonfigurować za pomocą OIDC, OAuth2 lub SAML, aby przeprowadzić uwierzytelnianie względem nadrzędnego dostawcy uwierzytelniania. Pojedynczy dostawcy tożsamości nadrzędny można skonfigurować do tego celu na serwerze PingAccess. Na poniższym diagramie przedstawiono taką konfigurację.

![obraz przedstawia PingAccess z implementacją OIDC](./media/partner-ping/authorization-flow.png)

W typowym wdrożeniu Azure AD B2C, w którym wiele zasad uwidacznia wiele **dostawców tożsamości**, stanowi wyzwanie. Ponieważ PingAccess można skonfigurować tylko za pomocą jednego dostawcy tożsamości nadrzędnego.  

### <a name="pingfederate-as-a-federation-proxy"></a>Serwera pingfederate jako federacyjny serwer proxy

Serwera pingfederate to mostek tożsamości przedsiębiorstwa, który można w pełni skonfigurować jako dostawcę uwierzytelniania lub serwer proxy dla innych dostawców tożsamości w razie potrzeby. Na poniższym diagramie przedstawiono tę możliwość.

![obraz przedstawia implementację serwera pingfederate](./media/partner-ping/pingfederate.png)

Ta funkcja może służyć do kontekstowego/dynamicznego lub deklaratywnego przełączenia żądania przychodzącego do określonych zasad Azure AD B2C. Poniżej przedstawiono przedstawianie przepływu sekwencji protokołu dla tej konfiguracji.

![obraz przedstawia przepływ pracy PingAccess i serwera pingfederate](./media/partner-ping/pingaccess-pingfederate-workflow.png)

## <a name="prerequisites"></a>Wymagania wstępne

Aby rozpocząć, musisz:

- Subskrypcja platformy Azure. Jeśli go nie masz, uzyskaj [bezpłatne konto](https://azure.microsoft.com/free/).

- [Dzierżawa Azure AD B2C](./tutorial-create-tenant.md) , która jest połączona z subskrypcją platformy Azure.

- PingAccess i serwera pingfederate wdrożone w kontenerach Docker lub bezpośrednio na maszynach wirtualnych platformy Azure.

## <a name="connectivity"></a>Łączność

Sprawdź, czy połączono poniższe.

- **Serwer PingAccess** — możliwość komunikowania się z serwerem serwera pingfederate, przeglądarką klienta, OIDC, znanym kluczem OAuth i odnajdywaniem kluczy publikowanych przez usługę Azure AD B2C i serwer serwera pingfederate.

- **Serwer serwera pingfederate** — umożliwia komunikowanie się z serwerem PingAccess, przeglądarką klienta, OIDC, dobrze znanym OAuth i odnajdywaniem kluczy publikowanych przez usługę Azure AD B2C.

- **Starsza lub oparta na nagłówkach aplikacja AuthN** — umożliwia komunikację z serwerem PingAccess i z niego.

- **Aplikacja jednostki uzależnionej SAML** — umożliwia dostęp do ruchu przeglądarki z klienta programu. Możliwe jest uzyskanie dostępu do metadanych Federacji SAML opublikowanych przez usługę Azure AD B2C.

- **Aplikacja Modern** — może uzyskać dostęp do ruchu przeglądarki z klienta. Możliwy dostęp do odnajdywania OIDC, dobrze znanych i kluczy uwierzytelniania OAuth opublikowanych przez usługę Azure AD B2C.

- **Interfejs API REST** — umożliwia dostęp do ruchu z klienta natywnego lub internetowego. Możliwy dostęp do odnajdywania OIDC, dobrze znanych i kluczy uwierzytelniania OAuth opublikowanych przez usługę Azure AD B2C.

## <a name="configure-azure-ad-b2c"></a>Konfigurowanie Azure AD B2C

W tym celu można użyć w tym celu podstawowych przepływów użytkowników lub zasad programu Advanced Identity Enterprise Framework (IEF). PingAccess generuje punkt końcowy metadanych na podstawie wartości **wystawcy** przy użyciu konwencji odnajdowania opartego na [webfinger](https://tools.ietf.org/html/rfc7033) .
Aby wykonać tę Konwencję, należy zaktualizować aktualizację wystawcy Azure AD B2C przy użyciu właściwości zasad w przepływach użytkownika.

![obraz przedstawia ustawienia tokenu](./media/partner-ping/token-setting.png)

W zaawansowanych zasadach można je skonfigurować za pomocą elementu metadanych **IssuanceClaimPattern** do **AuthorityWithTfp** wartość w [profilu technicznym wystawcy tokenów JWT](./jwt-issuer-technical-profile.md).

## <a name="configure-pingaccesspingfederate"></a>Konfigurowanie PingAccess/serwera pingfederate

W poniższej sekcji omówiono wymaganą konfigurację.
Diagram ilustruje ogólny przepływ użytkowników integracji.

![obraz przedstawia integrację PingAccess i serwera pingfederate](./media/partner-ping/pingaccess.png)

### <a name="configure-pingfederate-as-the-token-provider"></a>Konfigurowanie serwera pingfederate jako dostawcy tokenów

Aby skonfigurować serwera pingfederate jako dostawcę tokenów dla PingAccess, należy upewnić się, że połączenie z serwera pingfederate do PingAccess zostało nawiązane po nawiązaniu połączenia z PingAccess do serwera pingfederate.  
Zapoznaj się z [tym artykułem](https://docs.pingidentity.com/bundle/pingaccess-61/page/zgh1581446287067.html) , aby poznać kroki konfiguracji.

### <a name="configure-a-pingaccess-application-for-header-based-authentication"></a>Konfigurowanie aplikacji PingAccess na potrzeby uwierzytelniania opartego na nagłówkach

Należy utworzyć aplikację PingAccess dla docelowej aplikacji sieci Web na potrzeby uwierzytelniania opartego na nagłówkach. Wykonaj następujące kroki.

#### <a name="step-1--create-a-virtual-host"></a>Krok 1 — Tworzenie hosta wirtualnego

>[!IMPORTANT]
>Aby skonfigurować to rozwiązanie, należy utworzyć hosta wirtualnego dla każdej aplikacji. Aby uzyskać więcej informacji na temat zagadnień związanych z konfiguracją i ich wpływu, zobacz [najważniejsze zagadnienia](https://docs.pingidentity.com/bundle/pingaccess-43/page/reference/pa_c_KeyConsiderations.html).

Wykonaj następujące kroki, aby utworzyć hosta wirtualnego:

1. Przejdź do pozycji **Ustawienia**  >  **dostęp do**  >  **hostów wirtualnych**

2. Wybierz pozycję **Dodaj hosta wirtualnego**

3. W polu host wprowadź część FQDN adresu URL aplikacji.

4. W polu Port wprowadź **443**

5. Wybierz pozycję **Zapisz**

#### <a name="step-2--create-a-web-session"></a>Krok 2 — Tworzenie sesji sieci Web

Wykonaj następujące kroki, aby utworzyć sesję sieci Web:

1. Przejdź do **ustawień**  >  **dostęp** do  >  **sesji sieci Web**

2. Wybierz pozycję **Dodaj sesję sieci Web**

3. Podaj **nazwę** sesji sieci Web.

4. Wybierz **Typ pliku cookie** z **podpisem JWT** lub **szyfrowanym** tokenem JWT

5. Podaj unikatową wartość dla **odbiorców**

6. W polu **Identyfikator klienta** wprowadź **Identyfikator aplikacji usługi Azure AD** .

7. W polu **klucz tajny klienta** wprowadź **klucz** wygenerowany dla aplikacji w usłudze Azure AD.

8. Opcjonalne — można tworzyć i używać oświadczeń niestandardowych za pomocą interfejsu API Microsoft Graph. Jeśli zdecydujesz się to zrobić, wybierz opcję **Zaawansowane** i usuń zaznaczenie opcji **profil żądania** i **Odśwież atrybuty użytkownika** . Aby uzyskać więcej informacji na temat korzystania z oświadczeń niestandardowych, zobacz [Korzystanie z oświadczenia niestandardowego](../active-directory/manage-apps/application-proxy-configure-single-sign-on-with-headers.md).

9. Wybierz pozycję **Zapisz**

#### <a name="step-3--create-identity-mapping"></a>Krok 3 — Tworzenie mapowania tożsamości

>[!NOTE]
>Mapowania tożsamości można używać z więcej niż jedną aplikacją, jeśli więcej niż jedna aplikacja oczekuje na te same dane w nagłówku.

Wykonaj następujące kroki, aby utworzyć mapowanie tożsamości:

1. Przejdź do pozycji **Ustawienia**  >  **dostęp**  >  **mapowania tożsamości**

2. Wybierz pozycję **Dodaj mapowanie tożsamości**

3. Określ **nazwę**

4. Wybierz typ mapowania tożsamości **dla mapowania tożsamości nagłówka**

5. W tabeli **Mapowanie atrybutu** Określ wymagane mapowania. Na przykład

   Nazwa atrybutu | Nazwa nagłówka |
   |-------|--------|
   |głównej | x-userprinciplename |
   |poczta e-mail   |    x-email  |
   |OID   | x-OID  |
   |punkcie   |     x — zakres |
   |AMR    |    x-AMR    |

6. Wybierz pozycję **Zapisz**

#### <a name="step-4--create-a-site"></a>Krok 4 — Tworzenie witryny

>[!NOTE]
>W niektórych konfiguracjach istnieje możliwość, że lokacja może zawierać więcej niż jedną aplikację. Lokacja może być używana z więcej niż jedną aplikacją, w zależności od potrzeb.

Wykonaj następujące kroki, aby utworzyć lokację:

1. Przejdź do   >  **witryn** głównych

2. Wybierz pozycję **Dodaj witrynę**

3. Określ **nazwę** witryny

4. Wprowadź wartość **docelową** witryny. Obiektem docelowym jest para nazwa hosta: port dla serwera, na którym znajduje się aplikacja. Nie wprowadzaj ścieżki do aplikacji w tym polu. Na przykład aplikacja pod adresem https://mysite:9999/AppName będzie miała wartość docelową witryny: 9999

5. Wskaż, czy obiekt docelowy oczekuje bezpiecznych połączeń.

6. Jeśli obiekt docelowy oczekuje bezpiecznych połączeń, ustaw **grupę zaufanych certyfikatów jako zaufaną.**

7. Wybierz pozycję **Zapisz**

#### <a name="step-5--create-an-application"></a>Krok 5 — Tworzenie aplikacji

Wykonaj następujące kroki, aby utworzyć aplikację w programie PingAccess dla każdej aplikacji na platformie Azure, która ma być chroniona.

1. Przejdź do   >  **aplikacji** głównych

2. Wybierz pozycję **Dodaj aplikację**

3. Określ **nazwę** aplikacji

4. Opcjonalnie wprowadź **Opis** aplikacji

5. Określ **katalog główny kontekstu** dla aplikacji. Na przykład aplikacja w https://mysite:9999/AppName programie ma katalog główny kontekstu/APPNAME. Element główny kontekstu musi rozpoczynać się od ukośnika (/), nie może kończyć się ukośnikiem (/) i może mieć więcej niż jedną warstwę głębokiej, na przykład/Apps/MyApp.

6. Wybieranie utworzonego **hosta wirtualnego**

   >[!NOTE]
   >Kombinacja hosta wirtualnego i katalogu głównego kontekstu musi być unikatowa w PingAccess.

7. Wybierz utworzoną **sesję sieci Web**

8. Wybierz utworzoną **witrynę** zawierającą aplikację

9. Wybierz utworzone **Mapowanie tożsamości**

10. Wybierz opcję **włączone** , aby włączyć witrynę podczas zapisywania

11. Wybierz pozycję **Zapisz**

### <a name="configure-the-pingfederate-authentication-policy"></a>Konfigurowanie zasad uwierzytelniania serwera pingfederate

Skonfiguruj zasady uwierzytelniania serwera pingfederate w sfederować dla wielu dostawców tożsamości dostarczonych przez dzierżawy Azure AD B2C

1. Utwórz kontrakt służący do mostkowania atrybutów między dostawców tożsamości i SP. Aby uzyskać więcej informacji, zobacz [kontrakty usługi Federation Hub i zasad uwierzytelniania](https://docs.pingidentity.com/bundle/pingfederate-101/page/ope1564002971971.html). Istnieje konieczność tylko jednego kontraktu, chyba że SP wymaga innego zestawu atrybutów z każdego dostawcy tożsamościu.

2. Dla każdego dostawcy tożsamościu Utwórz połączenie dostawcy tożsamości między dostawcy tożsamości i serwera pingfederate, centrum federacyjne jako SP.

3. W oknie **Mapowanie sesji docelowej** Dodaj odpowiednie kontrakty zasad uwierzytelniania do połączenia usługi dostawcy tożsamości.

4. **W oknie** selektory Skonfiguruj selektor uwierzytelniania. Na przykład, zobacz wystąpienie **pierwszej karty** , aby zmapować każdy dostawcy tożsamości do odpowiedniego połączenia dostawcy tożsamości w zasadach uwierzytelniania.

5. Utwórz połączenie z usługą SP między usługą serwera pingfederate, centrum federacyjne jako dostawcy tożsamości i SP.

6. Dodaj odpowiedni kontrakt zasad uwierzytelniania do połączenia z usługą SP w oknie **Mapowanie źródła uwierzytelniania** .

7. Współpracuj z każdym dostawcy tożsamościem, aby połączyć się z usługą serwera pingfederate, z centrum federacyjnego jako SP.

8. Pracuj z usługą SP, aby nawiązać połączenie z usługą serwera pingfederate, z centrum federacyjnego jako dostawcy tożsamości.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać dodatkowe informacje, zapoznaj się z następującymi artykułami

- [Zasady niestandardowe w usłudze Azure AD B2C](./custom-policy-overview.md)

- [Wprowadzenie do zasad niestandardowych w Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
