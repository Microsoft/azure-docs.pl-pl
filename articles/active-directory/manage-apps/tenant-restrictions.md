---
title: Zarządzanie dostępem do aplikacji SaaS za pomocą ograniczeń dzierżawy — Azure AD
description: Jak używać ograniczeń dzierżawy do zarządzania tym, którzy użytkownicy mogą uzyskać dostęp do aplikacji na podstawie ich dzierżawy usługi Azure AD.
services: active-directory
author: iantheninja
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 4/6/2021
ms.author: iangithinji
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 941bf61f3387abf19be58bdd4d8861ab123e244f
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107376583"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Zarządzanie dostępem do aplikacji SaaS w chmurze przy użyciu ograniczeń dzierżawy

Duże organizacje, które kładą nacisk na bezpieczeństwo, chcą przejść do usług w chmurze, takich jak Microsoft 365, ale muszą wiedzieć, że ich użytkownicy mogą uzyskać dostęp tylko do zatwierdzonych zasobów. Tradycyjnie firmy ograniczają nazwy domen lub adresy IP, gdy chcą zarządzać dostępem. Takie podejście kończy się niepowodzeniem w świecie, w którym aplikacje typu oprogramowanie jako usługa (lub SaaS) są hostowane w chmurze publicznej i działają na udostępnionych nazwach domen, takich jak [outlook.office.com](https://outlook.office.com/) [i login.microsoftonline.com](https://login.microsoftonline.com/). Zablokowanie tych adresów uniemożliwi użytkownikom dostęp do programu Outlook w Sieci Web w całości, a nie tylko ograniczenie ich do zatwierdzonych tożsamości i zasobów.

Rozwiązanie Azure Active Directory (Azure AD) to funkcja nazywana ograniczeniami dzierżawy. Dzięki ograniczeniom dzierżawy organizacje mogą kontrolować dostęp do aplikacji SaaS w chmurze w oparciu o dzierżawę usługi Azure AD używaną przez aplikacje do logowania pojedynczego. Na przykład możesz zezwolić na dostęp do aplikacji Microsoft 365 organizacji, jednocześnie uniemożliwiając dostęp do wystąpień tych samych aplikacji w innych organizacjach.  

Dzięki ograniczeniom dzierżawy organizacje mogą określić listę dzierżaw, do których użytkownicy mają dostęp. Następnie usługa Azure AD udziela dostępu tylko tym dozwolonym dzierżawom.

Ten artykuł koncentruje się na ograniczeniach dzierżawy Microsoft 365, ale funkcja chroni wszystkie aplikacje, które wysyłają użytkownika do usługi Azure AD w celu logowania pojedynczego. Jeśli używasz aplikacji SaaS z dzierżawą usługi Azure AD inną niż dzierżawa używana przez usługę Microsoft 365, upewnij się, że wszystkie wymagane dzierżawy są dozwolone (np. w scenariuszach współpracy B2B). Aby uzyskać więcej informacji na temat aplikacji SaaS w chmurze, zobacz [Witrynę Marketplace usługi Active Directory.](https://azuremarketplace.microsoft.com/marketplace/apps)

Ponadto funkcja ograniczeń dzierżawy obsługuje teraz blokowanie użycia wszystkich aplikacji dla konsumentów firmy [Microsoft](#blocking-consumer-applications-public-preview) (aplikacji MSA), takich jak OneDrive, Hotmail i Xbox.com.  Używa to oddzielnego nagłówka do punktu końcowego i jest szczegółowo `login.live.com` opisane na końcu dokumentu.

## <a name="how-it-works"></a>Jak to działa

Ogólne rozwiązanie składa się z następujących składników:

1. **Azure AD:** jeśli nagłówek `Restrict-Access-To-Tenants: <permitted tenant list>` jest obecny, usługa Azure AD wydaje tokeny zabezpieczające tylko dla dozwolonych dzierżaw.

2. **Lokalna infrastruktura serwera proxy:** ta infrastruktura jest urządzeniem proxy umożliwiającym Transport Layer Security (TLS). Należy skonfigurować serwer proxy, aby wstawić nagłówek zawierający listę dozwolonych dzierżaw do ruchu przeznaczonego dla usługi Azure AD.

3. **Oprogramowanie klienckie:** aby zapewnić obsługę ograniczeń dzierżawy, oprogramowanie klienckie musi żądać tokenów bezpośrednio z usługi Azure AD, aby infrastruktura serwera proxy przechwyciła ruch. Aplikacje oparte na przeglądarce Microsoft 365 obecnie obsługują ograniczenia dzierżawy, podobnie jak klienci pakietu Office, którzy korzystają z nowoczesnego uwierzytelniania (takiego jak OAuth 2.0).

4. **Nowoczesne uwierzytelnianie:** usługi w chmurze muszą używać nowoczesnego uwierzytelniania, aby korzystać z ograniczeń dzierżawy i blokować dostęp do wszystkich niedozwolonych dzierżaw. Należy skonfigurować usługę Microsoft 365 w chmurze, aby domyślnie używać nowoczesnych protokołów uwierzytelniania. Aby uzyskać najnowsze informacje na temat obsługi Microsoft 365 nowoczesnego uwierzytelniania, przeczytaj zaktualizowane nowoczesne uwierzytelnianie usługi [Office 365.](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)

Na poniższym diagramie przedstawiono przepływ ruchu wysokiego poziomu. Ograniczenia dzierżawy wymagają inspekcji TLS tylko w przypadku ruchu do usługi Azure AD, a nie do Microsoft 365 w chmurze. To rozróżnienie jest ważne, ponieważ ilość ruchu w przypadku uwierzytelniania w usłudze Azure AD jest zwykle znacznie niższa niż ruch do aplikacji SaaS, takich jak Exchange Online i SharePoint Online.

![Przepływ ruchu ograniczeń dzierżawy — diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurowanie ograniczeń dzierżawy

Aby rozpocząć pracę z ograniczeniami dzierżawy, należy wykonać dwa kroki. Najpierw upewnij się, że klienci mogą łączyć się z właściwymi adresami. Po drugie skonfiguruj infrastrukturę serwera proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL i IP

Aby można było korzystać z ograniczeń dzierżawy, klienci muszą mieć możliwość łączenia się z następującymi adresami URL usługi Azure AD w celu uwierzytelniania: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.microsoft.com](https://login.microsoft.com/)i [login.windows.net](https://login.windows.net/). Ponadto aby uzyskać dostęp do usługi Office 365, klienci muszą mieć również możliwość łączenia się z w pełni kwalifikowanymi nazwami domen (FQDN), adresami URL i adresami IP zdefiniowanymi w zakresach adresów IP i adresów URL usługi [Office 365.](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2) 

### <a name="proxy-configuration-and-requirements"></a>Konfiguracja serwera proxy i wymagania

Następująca konfiguracja jest wymagana do włączenia ograniczeń dzierżawy za pośrednictwem infrastruktury serwera proxy. Te wskazówki są ogólne, dlatego konkretne kroki implementacji należy znaleźć w dokumentacji dostawcy serwera proxy.

#### <a name="prerequisites"></a>Wymagania wstępne

- Serwer proxy musi mieć możliwość przechwycenia protokołu TLS, wstawiania nagłówka HTTP i filtrowania miejsc docelowych przy użyciu sieci FQDN/adresów URL.

- Klienci muszą ufać łańcuchowi certyfikatów przedstawionemu przez serwer proxy na potrzeby komunikacji TLS. Jeśli na przykład są używane certyfikaty z wewnętrznej infrastruktury kluczy publicznych [(PKI),](/windows/desktop/seccertenroll/public-key-infrastructure) certyfikat wewnętrznego głównego urzędu certyfikacji wystawiającego certyfikaty musi być zaufany.

- Azure AD — wersja Premium ograniczenia dzierżawy wymaga 1 licencji.

#### <a name="configuration"></a>Konfigurowanie

Dla każdego wychodzącego żądania login.microsoftonline.com, login.microsoft.com i login.windows.net wstaw dwa nagłówki HTTP: *Restrict-Access-To-Tenants* i *Restrict-Access-Context*.

> [!NOTE]
> Nie uwzględniaj domen poddomeny `*.login.microsoftonline.com` w obszarze w konfiguracji serwera proxy. Takie działanie obejmuje device.login.microsoftonline.com i będzie zakłócać uwierzytelnianie certyfikatu klienta, które jest używane w scenariuszach rejestracji urządzeń i dostępu warunkowego opartego na urządzeniach. Skonfiguruj serwer proxy, aby wykluczyć device.login.microsoftonline.com z łamania i inspekcji TLS oraz iniekcji nagłówka.

Nagłówki powinny zawierać następujące elementy:

- W *przypadku funkcji Ogranicz dostęp* do dzierżaw należy użyć wartości , która jest rozdzielaną przecinkami listą dzierżaw, do których chcesz zezwolić \<permitted tenant list\> użytkownikom na dostęp. Do zidentyfikowania dzierżawy na tej liście, a także samego identyfikatora katalogu, można użyć dowolnej domeny zarejestrowanej w dzierżawie. Na przykład dla wszystkich trzech sposobów opisywania dzierżawy, para nazwa/wartość, aby umożliwić firmie Contoso, Fabrikam i Microsoft, wygląda następująco: `Restrict-Access-To-Tenants: contoso.com,fabrikam.onmicrosoft.com,72f988bf-86f1-41af-91ab-2d7cd011db47`

- W *przypadku opcji Restrict-Access-Context* użyj wartości pojedynczego identyfikatora katalogu, deklarując, która dzierżawa ustawia ograniczenia dzierżawy. Aby na przykład zadeklarować firmę Contoso jako dzierżawę, która ustawiła zasady ograniczeń dzierżawy, para nazwa/wartość wygląda następująco: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d` .  Aby **uzyskać** dzienniki dla tych uwierzytelnień, musisz użyć własnego identyfikatora katalogu w tym miejscu.

> [!TIP]
> Identyfikator katalogu można znaleźć w witrynie [Azure Active Directory portal.](https://aad.portal.azure.com/) Zaloguj się jako administrator, wybierz pozycję Azure Active Directory , **a** następnie wybierz pozycję **Właściwości.** 
>
> Aby sprawdzić, czy identyfikator katalogu lub nazwa domeny odwołują się do tej samej dzierżawy, użyj tego identyfikatora lub domeny w miejsce tego <tenant> adresu URL: `https://login.microsoftonline.com/<tenant>/v2.0/.well-known/openid-configuration` .  Jeśli wyniki z domeną i identyfikatorem są takie same, odwołują się do tej samej dzierżawy. 

Aby uniemożliwić użytkownikom wstawianie własnego nagłówka HTTP z niezatwierdzoną dzierżawą, serwer proxy musi zastąpić nagłówek *Restrict-Access-To-Tenants,* jeśli jest już obecny w żądaniu przychodzącym.

Klienci muszą być zmuszeni do używania serwera proxy dla wszystkich żądań login.microsoftonline.com, login.microsoft.com i login.windows.net. Jeśli na przykład pliki PAC są używane do kierowania klientów do korzystania z serwera proxy, użytkownicy końcowi nie powinni mieć możliwości edytowania ani wyłączania plików PAC.

## <a name="the-user-experience"></a>Środowisko użytkownika

W tej sekcji opisano środowisko zarówno dla użytkowników końcowych, jak i administratorów.

### <a name="end-user-experience"></a>Środowisko użytkownika końcowego

Przykładowy użytkownik znajduje się w sieci firmy Contoso, ale próbuje uzyskać dostęp do wystąpienia fabrikam udostępnionej aplikacji SaaS, takiej jak Outlook Online. Jeśli firma Fabrikam jest niedozwoloną dzierżawą dla wystąpienia firmy Contoso, użytkownik zobaczy komunikat o odmowie dostępu informujący, że próbujesz uzyskać dostęp do zasobu należącego do organizacji niezatwierdzoną przez dział IT.

![Komunikat o błędzie ograniczenia dzierżawy z kwietnia 2021 r.](./media/tenant-restrictions/error-message.png)

### <a name="admin-experience"></a>Środowisko administratora

Konfiguracja ograniczeń dzierżawy jest wykonywana w infrastrukturze firmowego serwera proxy, ale administratorzy mogą bezpośrednio uzyskać dostęp do raportów ograniczeń dzierżawy w Azure Portal serwerach. Aby wyświetlić raporty:

1. Zaloguj się do [witryny Azure Active Directory portal.](https://aad.portal.azure.com/) Zostanie **Azure Active Directory nawigacyjny centrum administracyjnego.**

2. W lewym okienku wybierz pozycję **Azure Active Directory**. Zostanie Azure Active Directory strona przeglądu aplikacji.

3. Na stronie Przegląd wybierz pozycję **Ograniczenia dzierżawy.**

Administrator dzierżawy określonej jako dzierżawa z ograniczonym dostępem-kontekstem może użyć tego raportu, aby zobaczyć logowania zablokowane z powodu zasad ograniczeń dzierżawy, w tym używanej tożsamości i identyfikatora katalogu docelowego. Logowania są uwzględniane, jeśli ustawienie dzierżawy to dzierżawa użytkownika lub dzierżawa zasobów dla logowania.

Raport może zawierać ograniczone informacje, takie jak identyfikator katalogu docelowego, gdy użytkownik, który znajduje się w dzierżawie innej niż dzierżawa z ograniczonym dostępem i kontekstem, się dodaje. W takim przypadku informacje umożliwiające zidentyfikowanie użytkownika, takie jak nazwa i główna nazwa użytkownika, są maskowane w celu ochrony danych użytkowników w innych dzierżawach ("{PII Removed} @domain.com " lub 000000000-0000-0000-0000-0000000000000 w miejsce nazw użytkowników i identyfikatorów obiektów zgodnie z potrzebami). 

Podobnie jak w przypadku Azure Portal raportach, można użyć filtrów, aby określić zakres raportu. Można filtrować według określonego interwału czasu, użytkownika, aplikacji, klienta lub stanu. Jeśli wybierzesz przycisk **Kolumny,** możesz wybrać wyświetlanie danych przy użyciu dowolnej kombinacji następujących pól:

- **Użytkownik** — to pole może mieć usunięte dane osobowe, dla którego zostanie ustawiona wartość `00000000-0000-0000-0000-000000000000` . 
- **Aplikacja**
- **Stan**
- **Data**
- **Data (UTC)** — czas UTC jest Uniwersalny czas koordynowany
- **Adres IP**
- **Klient**
- **Nazwa** użytkownika — w tym polu mogą zostać usunięte dane osobowe, które zostaną ustawione na `{PII Removed}@domain.com`
- **Lokalizacja**
- **Identyfikator dzierżawy docelowej**

## <a name="microsoft-365-support"></a>Pomoc techniczna platformy Microsoft 365

Microsoft 365 muszą spełniać dwa kryteria, aby w pełni obsługiwać ograniczenia dzierżawy:

1. Używany klient obsługuje nowoczesne uwierzytelnianie.
2. Nowoczesne uwierzytelnianie jest włączone jako domyślny protokół uwierzytelniania dla usługi w chmurze.

Aby uzyskać najnowsze informacje na temat klientów pakietu Office, którzy obecnie obsługują nowoczesne uwierzytelnianie, zobacz Zaktualizowane nowoczesne uwierzytelnianie usługi [Office 365.](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) Ta strona zawiera również linki do instrukcji dotyczących włączania nowoczesnego uwierzytelniania w określonych dzierżawach usług Exchange Online i Skype dla firm Online. W usłudze SharePoint Online jest już domyślnie włączone nowoczesne uwierzytelnianie.

Microsoft 365 oparte na przeglądarce (portal pakietu Office, witryny Yammer, witryny programu SharePoint, program Outlook w sieci Web i inne) obsługują obecnie ograniczenia dzierżawy. Klienci grubi (Outlook, Skype dla firm, Word, Excel, PowerPoint i nie tylko) mogą wymuszać ograniczenia dzierżawy tylko w przypadku korzystania z nowoczesnego uwierzytelniania.  

Klienci programów Outlook i Skype dla firm, którzy obsługują nowoczesne uwierzytelnianie, mogą nadal używać starszych protokołów względem dzierżaw, w których nowoczesne uwierzytelnianie nie jest włączone, skutecznie pomijając ograniczenia dzierżawy. Ograniczenia dzierżawy mogą blokować aplikacje, które używają starszych protokołów, jeśli kontaktują się login.microsoftonline.com, login.microsoft.com lub login.windows.net podczas uwierzytelniania.

W przypadku programu Outlook w systemie Windows klienci mogą zdecydować się na wdrożenie ograniczeń uniemożliwiających użytkownikom końcowych dodawanie niezatwierdzonych kont poczty do swoich profilów. Zobacz na przykład ustawienie zasad grupy [Zapobiegaj dodawaniu kont programu Exchange](https://gpsearch.azurewebsites.net/default.aspx?ref=1) innych niż domyślne.

## <a name="testing"></a>Testowanie

Jeśli chcesz wypróbować ograniczenia dzierżawy przed ich wdrożeniem dla całej organizacji, masz dwie opcje: podejście oparte na hoście przy użyciu narzędzia, takiego jak Fiddler, lub etapowe wdrożenie ustawień serwera proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Program Fiddler dla podejścia opartego na hoście

Fiddler to bezpłatny internetowy serwer proxy debugowania, który może służyć do przechwytywania i modyfikowania ruchu HTTP/HTTPS, w tym do wstawiania nagłówków HTTP. Aby skonfigurować program Fiddler do testowania ograniczeń dzierżawy, wykonaj następujące kroki:

1. [Pobierz i zainstaluj program Fiddler.](https://www.telerik.com/fiddler)

2. Skonfiguruj program Fiddler do odszyfrowywania ruchu HTTPS według dokumentacji pomocy [programu Fiddler.](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS)

3. Skonfiguruj program Fiddler, aby wstawiał nagłówki *Restrict-Access-To-Tenants* i *Restrict-Access-Context* przy użyciu reguł niestandardowych:

   1. W narzędziu Fiddler Web Debugger wybierz menu **Reguły** i wybierz pozycję **Dostosuj reguły...** , aby otworzyć plik CustomRules.

   2. Dodaj następujące wiersze na początku `OnBeforeRequest` funkcji. Zastąp \<List of tenant identifiers\> nazwą domeny zarejestrowanej w dzierżawie (na przykład `contoso.onmicrosoft.com` ). Zastąp \<directory ID\> identyfikator identyfikatorem GUID usługi Azure AD swojej dzierżawy.  Aby **dzienniki** pojawiały się w dzierżawie, należy uwzględnić prawidłowy identyfikator GUID. 

   ```JScript.NET
    // Allows access to the listed tenants.
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<List of tenant identifiers>";
          oSession.oRequest["Restrict-Access-Context"] = "<Your directory ID>";
      }

    // Blocks access to consumer apps
      if (
          oSession.HostnameIs("login.live.com")
      )
      {
          oSession.oRequest["sec-Restrict-Tenant-Access-Policy"] = "restrict-msa";
      }
   ```

   Jeśli chcesz zezwolić na wiele dzierżaw, użyj przecinka, aby oddzielić nazwy dzierżaw. Na przykład:

   `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Zapisz i zamknij plik CustomRules.

Po skonfigurowaniu programu Fiddler można przechwytywać ruch, przechodząc do menu **Plik** i wybierając polecenie **Przechwyć ruch**.

### <a name="staged-rollout-of-proxy-settings"></a>Etapowe wycofywanie ustawień serwera proxy

W zależności od możliwości infrastruktury serwera proxy może być możliwe etapowe wywłaszczanie ustawień dla użytkowników. Oto kilka opcji wysokiego poziomu, które należy wziąć pod uwagę:

1. Użyj plików PAC, aby wskazać użytkownikom testowym infrastrukturę serwera proxy, podczas gdy normalni użytkownicy nadal korzystają z infrastruktury produkcyjnego serwera proxy.
2. Niektóre serwery proxy mogą obsługiwać różne konfiguracje przy użyciu grup.

Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją serwera proxy.

## <a name="blocking-consumer-applications-public-preview"></a>Blokowanie aplikacji dla konsumentów (publiczna wersja zapoznawcza)

Aplikacje firmy Microsoft, które obsługują zarówno konta konsumentów, jak i konta organizacyjne, takie jak [OneDrive](https://onedrive.live.com/) lub [Microsoft Learn](/learn/), mogą być czasami hostowane pod tym samym adresem URL.  Oznacza to, że użytkownicy, którzy muszą uzyskać dostęp do tego adresu URL w celach służbowych, mają również dostęp do niego do użytku osobistego, co może nie być dozwolone zgodnie z wytycznymi dotyczącymi działania.

Niektóre organizacje próbują rozwiązać ten problem przez zablokowanie dostępu do uwierzytelniania `login.live.com` kont osobistych.  Ma to kilka minusów:

1. Blokowanie blokuje korzystanie z kont osobistych w scenariuszach gości B2B, co może mieć wpływ `login.live.com` na gości i współpracę.
1. [Funkcja Autopilot wymaga `login.live.com` użycia](/mem/autopilot/networking-requirements) w celu wdrożenia. Scenariusze usługi Intune i rozwiązania Autopilot mogą się nie `login.live.com` powieść, gdy zostanie zablokowany.
1. Dane telemetryczne organizacji i aktualizacje systemu Windows, które są zależne login.live.com dla identyfikatorów [urządzeń, przestaną działać.](/windows/deployment/update/windows-update-troubleshooting#feature-updates-are-not-being-offered-while-other-updates-are)

### <a name="configuration-for-consumer-apps"></a>Konfiguracja aplikacji dla konsumentów

Chociaż nagłówek działa jako lista zezwalania, blok konto Microsoft (MSA) działa jako sygnał odmowy, informując platformę konto Microsoft, aby nie zezwalała użytkownikom na logowanie się do aplikacji `Restrict-Access-To-Tenants` konsumenckich. Aby wysłać ten sygnał, nagłówek jest wstrzykiwany do ruchu odwiedzającego przy użyciu tego samego firmowego serwera proxy lub zapory co `sec-Restrict-Tenant-Access-Policy` `login.live.com` [powyżej](#proxy-configuration-and-requirements). Wartość nagłówka musi być `restrict-msa` . Gdy nagłówek jest obecny, a aplikacja konsumenta próbuje zalogować się bezpośrednio do użytkownika, to logowanie zostanie zablokowane.

Obecnie uwierzytelnianie w aplikacjach konsumenckich nie jest wyświetlane w dziennikach [administratora,](#admin-experience)ponieważ login.live.com jest hostowane niezależnie od usługi Azure AD.

### <a name="what-the-header-does-and-does-not-block"></a>Co robi nagłówek, a czego nie blokuje

Zasady `restrict-msa` blokuje korzystanie z aplikacji dla konsumentów, ale zezwalają na użycie kilku innych typów ruchu i uwierzytelniania:

1. Ruch urządzeń bez użytkowników.  Obejmuje to ruch dla rozwiązania Autopilot, Windows Update i telemetrii organizacyjnej.
1. Uwierzytelnianie B2B kont konsumentów. Użytkownicy z kontami [](../external-identities/redemption-experience.md#invitation-redemption-flow) Microsoft zaproszeni do współpracy z dzierżawą uwierzytelniają się w login.live.com w celu uzyskania dostępu do dzierżawy zasobów.
    1. Ten dostęp jest kontrolowany przy użyciu nagłówka , aby zezwolić na dostęp do tej dzierżawy zasobów lub odmówić `Restrict-Access-To-Tenants` go.
1. Uwierzytelnianie "passthrough" używane przez wiele aplikacji platformy Azure, a także Office.com, gdzie aplikacje używają usługi Azure AD do logowania użytkowników indywidualnych w kontekście użytkownika.
    1. Ten dostęp jest również kontrolowany przy użyciu nagłówka w celu umożliwienia lub odmowy dostępu do specjalnej `Restrict-Access-To-Tenants` dzierżawy "passthrough" ( `f8cdef31-a31e-4b4a-93e4-5f571e91255a` ).  Jeśli ta dzierżawa nie jest wyświetlana na liście dozwolonych domen, logowanie do tych aplikacji na kontach użytkowników zostanie zablokowane `Restrict-Access-To-Tenants` przez usługę Azure AD.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [zaktualizowanym nowoczesnym uwierzytelnianiu usługi Office 365](https://www.microsoft.com/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Przeglądanie [zakresów adresów URL i IP usługi Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
