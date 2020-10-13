---
title: Używanie ograniczeń dzierżawy do zarządzania dostępem do aplikacji SaaS — Azure AD
description: Jak używać ograniczeń dzierżawy do zarządzania użytkownikami, którzy mogą uzyskiwać dostęp do aplikacji na podstawie ich dzierżawy usługi Azure AD.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 03/28/2019
ms.author: kenwith
ms.reviewer: hpsin
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1cce42cdb63fcfcb9a5841f2f2199daf2bb92304
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90604176"
---
# <a name="use-tenant-restrictions-to-manage-access-to-saas-cloud-applications"></a>Używanie ograniczeń dzierżawy do zarządzania dostępem do aplikacji w chmurze SaaS

Duże organizacje, które podkreślają zabezpieczenia, chcą przejść do usług w chmurze, takich jak Microsoft 365, ale muszą mieć pewność, że użytkownicy mają dostęp tylko do zatwierdzonych zasobów. Tradycyjnie firmy ograniczają nazwy domen lub adresy IP, gdy chcą zarządzać dostępem. Takie podejście kończy się niepowodzeniem na świecie, w którym aplikacje SaaS (Software as a Service) są hostowane w chmurze publicznej, działające na nazwach udostępnionych domen, takich jak [Outlook.Office.com](https://outlook.office.com/) i [login.microsoftonline.com](https://login.microsoftonline.com/). Zablokowanie tych adresów uniemożliwi użytkownikom uzyskiwanie dostępu do programu Outlook w sieci Web, a nie tylko ograniczanie ich do zatwierdzonych tożsamości i zasobów.

Rozwiązanie Azure Active Directory (Azure AD) do tego wezwania jest funkcją z ograniczeniami dzierżawy. Ograniczenia dotyczące dzierżawców pozwalają organizacjom kontrolować dostęp do aplikacji w chmurze SaaS w oparciu o dzierżawę usługi Azure AD, której aplikacje używają do logowania jednokrotnego. Na przykład możesz chcieć zezwolić na dostęp do Microsoft 365 aplikacji w organizacji, jednocześnie uniemożliwiając dostęp do wystąpień innych organizacji tych samych aplikacji.  

W przypadku ograniczeń dzierżawy organizacje mogą określić listę dzierżawców, do których użytkownicy mają prawo dostępu. Usługa Azure AD udziela dostępu tylko do tych dozwolonych dzierżawców.

Ten artykuł koncentruje się na ograniczeniach dzierżawy dla Microsoft 365, ale funkcja powinna współpracować z dowolną aplikacją w chmurze SaaS, która korzysta z nowoczesnego protokołu uwierzytelniania z usługą Azure AD w celu logowania jednokrotnego. Jeśli używasz aplikacji SaaS z inną dzierżawą usługi Azure AD z dzierżawy używanej przez Microsoft 365, upewnij się, że wszystkie wymagane dzierżawy są dozwolone. Aby uzyskać więcej informacji na temat aplikacji w chmurze SaaS, zobacz Portal [Active Directory Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Microsoft.AzureActiveDirectory).

## <a name="how-it-works"></a>Jak to działa

Ogólne rozwiązanie składa się z następujących składników:

1. **Azure AD**: Jeśli `Restrict-Access-To-Tenants: <permitted tenant list>` istnieje, usługa Azure AD wystawia tokeny zabezpieczające tylko dla dozwolonych dzierżawców.

2. **Infrastruktura lokalnego serwera proxy**: ta infrastruktura jest urządzeniem serwera proxy z możliwością inspekcji Transport Layer Security (TLS). Musisz skonfigurować serwer proxy, aby wstawić nagłówek zawierający listę dozwolonych dzierżawców do ruchu przeznaczonego dla usługi Azure AD.

3. **Oprogramowanie klienckie**: aby obsługiwać ograniczenia dzierżawy, oprogramowanie klienckie musi żądać tokenów bezpośrednio z usługi Azure AD, dzięki czemu infrastruktura serwera proxy może przechwytywać ruch. Microsoft 365 aplikacje oparte na przeglądarce obecnie obsługują ograniczenia dzierżawy, ponieważ klienci pakietu Office korzystający z nowoczesnego uwierzytelniania (na przykład OAuth 2,0).

4. **Nowoczesne uwierzytelnianie**: usługi w chmurze muszą używać nowoczesnego uwierzytelniania, aby używać ograniczeń dzierżawy i blokować dostęp do wszystkich niedozwolonych dzierżawców. Aby korzystać z nowoczesnych protokołów uwierzytelniania, należy skonfigurować usługi Microsoft 365 w chmurze. Aby uzyskać najnowsze informacje na temat Microsoft 365 obsługi nowoczesnego uwierzytelniania, Przeczytaj [zaktualizowany nowoczesne uwierzytelnianie pakietu Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/).

Na poniższym diagramie przedstawiono przepływ ruchu wysokiego poziomu. Ograniczenia dzierżawy wymagają inspekcji protokołu TLS tylko w ruchu do usługi Azure AD, a nie do usług w chmurze Microsoft 365. Ta różnica jest ważna, ponieważ ilość ruchu sieciowego do uwierzytelniania w usłudze Azure AD jest zwykle znacznie mniejsza niż ilość ruchu sieciowego do SaaS aplikacji, takich jak Exchange Online i SharePoint Online.

![Przepływ ruchu związany z ograniczeniami dzierżawy — diagram](./media/tenant-restrictions/traffic-flow.png)

## <a name="set-up-tenant-restrictions"></a>Konfigurowanie ograniczeń dzierżawy

Istnieją dwa kroki, które należy wykonać, aby rozpocząć pracę z ograniczeniami dzierżawy. Najpierw upewnij się, że klienci mogą łączyć się z prawidłowymi adresami. Następnie skonfiguruj infrastrukturę serwera proxy.

### <a name="urls-and-ip-addresses"></a>Adresy URL i adresy IP

Aby można było używać ograniczeń dzierżawy, klienci muszą mieć możliwość nawiązywania połączeń z następującymi adresami URL usługi Azure AD w celu uwierzytelniania: [login.microsoftonline.com](https://login.microsoftonline.com/), [login.Microsoft.com](https://login.microsoft.com/)i [login.Windows.NET](https://login.windows.net/). Ponadto w celu uzyskania dostępu do pakietu Office 365 klienci muszą również mieć możliwość nawiązywania połączeń z w pełni kwalifikowanymi nazwami domen (FQDN), adresami URL i adresami IP zdefiniowanymi w adresach [URL i zakresach adresów IP pakietu office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2). 

### <a name="proxy-configuration-and-requirements"></a>Konfiguracja i wymagania serwera proxy

Aby włączyć ograniczenia dzierżawy za pomocą infrastruktury serwera proxy, wymagana jest następująca konfiguracja. Wskazówki te są ogólne, dlatego należy zapoznać się z dokumentacją dostawcy serwera proxy dotyczącą konkretnych kroków implementacji.

#### <a name="prerequisites"></a>Wymagania wstępne

- Serwer proxy musi mieć możliwość przechwycenia protokołu TLS, wstawienia nagłówka HTTP i przefiltrowania miejsc docelowych przy użyciu nazw FQDN/adresów URL.

- Klienci muszą ufać łańcuchowi certyfikatów przedstawionym przez serwer proxy do komunikacji TLS. Na przykład jeśli używane są certyfikaty z wewnętrznej [infrastruktury kluczy publicznych (PKI)](/windows/desktop/seccertenroll/public-key-infrastructure) , certyfikat wewnętrznego wystawiania certyfikatu głównego urzędu certyfikacji musi być zaufany.

- Ta funkcja jest uwzględniona w subskrypcjach Microsoft 365, ale jeśli chcesz użyć ograniczeń dzierżawy do kontrolowania dostępu do innych aplikacji SaaS, wówczas Azure AD — wersja Premium 1 licencje są wymagane.

#### <a name="configuration"></a>Konfiguracja

Dla każdego żądania przychodzącego do login.microsoftonline.com, login.microsoft.com i login.windows.net, Wstaw dwa nagłówki HTTP: *ograniczanie dostępu do dzierżawców* i *ograniczanie dostępu do kontekstu*.

> [!NOTE]
> Podczas konfigurowania przechwycenia protokołu SSL i iniekcji nagłówków upewnij się, że ruch https://device.login.microsoftonline.com jest wykluczony. Ten adres URL jest używany na potrzeby uwierzytelniania urządzeń i wykonywania operacji przerywania i inspekcji protokołu TLS może zakłócać uwierzytelnianie certyfikatu klienta, co może powodować problemy z rejestracją urządzeń i dostępem warunkowym opartym na urządzeniach.



Nagłówki powinny zawierać następujące elementy:

- W przypadku *ograniczania dostępu do dzierżawców*Użyj wartości \<permitted tenant list\> , która jest rozdzielaną przecinkami listą dzierżawców, dla których chcesz zezwolić użytkownikom na dostęp. Wszystkie domeny zarejestrowane za pomocą dzierżawy mogą służyć do identyfikowania dzierżawy na tej liście. Na przykład, aby zezwolić na dostęp zarówno do dzierżaw firmy Contoso, jak i Fabrikam, para nazwa/wartość wygląda następująco: `Restrict-Access-To-Tenants: contoso.onmicrosoft.com,fabrikam.onmicrosoft.com`

- Aby *ograniczyć dostęp do kontekstu*, użyj wartości identyfikatora pojedynczego katalogu, deklarując, który dzierżawca ustawia ograniczenia dzierżawy. Na przykład, aby zadeklarować contoso jako dzierżawcę, który ustawił zasady ograniczeń dzierżawy, para nazwa/wartość będzie wyglądać następująco: `Restrict-Access-Context: 456ff232-35l2-5h23-b3b3-3236w0826f3d`  

> [!TIP]
> Identyfikator katalogu można znaleźć w [portalu Azure Active Directory](https://aad.portal.azure.com/). Zaloguj się jako administrator, wybierz pozycję **Azure Active Directory**, a następnie wybierz pozycję **Właściwości**.

Aby uniemożliwić użytkownikom wstawianie własnego nagłówka HTTP z niezatwierdzonymi dzierżawcami, serwer proxy musi zastąpić nagłówek *ograniczenia dostępu do dzierżawców* , jeśli jest już obecny w żądaniu przychodzącym.

Klienci muszą być zmuszeni do korzystania z serwera proxy dla wszystkich żądań do login.microsoftonline.com, login.microsoft.com i login.windows.net. Na przykład jeśli pliki PAC są używane do kierowania klientom do korzystania z serwera proxy, użytkownicy końcowi nie będą mogli edytować ani wyłączać plików PAC.

> [!NOTE]
> W konfiguracji serwera proxy nie należy uwzględniać poddomen w lokalizacji *. login.microsoftonline.com. Takie działanie będzie obejmować device.login.microsoftonline.com i może zakłócać uwierzytelnianie certyfikatu klienta, które jest używane w scenariuszach rejestracji urządzeń i dostępu warunkowego opartego na urządzeniach. Skonfiguruj serwer proxy w taki sposób, aby wykluczyć device.login.microsoftonline.com z dzielenia i kontroli protokołu TLS.

## <a name="the-user-experience"></a>Środowisko użytkownika

Ta sekcja zawiera opis środowiska zarówno dla użytkowników końcowych, jak i administratorów.

### <a name="end-user-experience"></a>Środowisko użytkownika końcowego

Przykładowy użytkownik znajduje się w sieci contoso, ale próbuje uzyskać dostęp do wystąpienia Fabrikam udostępnionej aplikacji SaaS, takiej jak Outlook online. Jeśli firma Fabrikam jest niedozwolonym dzierżawcą dla wystąpienia contoso, użytkownik zobaczy komunikat odmowy dostępu, który informuje, że próbujesz uzyskać dostęp do zasobu, który należy do organizacji niezatwierdzonej przez dział IT.

### <a name="admin-experience"></a>Środowisko administratora

Podczas konfigurowania ograniczeń dzierżawy w firmowej infrastrukturze serwerów proxy Administratorzy mogą uzyskać bezpośredni dostęp do raportów ograniczeń dzierżawy w Azure Portal. Aby wyświetlić raporty:

1. Zaloguj się do [portalu Azure Active Directory](https://aad.portal.azure.com/). Zostanie wyświetlony pulpit nawigacyjny **Centrum administracyjnego Azure Active Directory** .

2. W lewym okienku wybierz pozycję **Azure Active Directory**. Zostanie wyświetlona strona przegląd Azure Active Directory.

3. Na stronie Przegląd wybierz pozycję **ograniczenia dzierżawy**.

Administrator dzierżawy określony jako dzierżawca kontekstu ograniczonego dostępu może użyć tego raportu, aby zobaczyć, że logowanie zostało zablokowane z powodu zasad ograniczeń dzierżawy, w tym użytych tożsamości i identyfikatora katalogu docelowego. Logowania są uwzględniane, jeśli dzierżawa ustawienia ograniczenia to dzierżawa użytkownika lub dzierżawa zasobów dla logowania.

> [!NOTE]
> Raport może zawierać ograniczone informacje, takie jak identyfikator katalogu docelowego, gdy użytkownik, który znajduje się w dzierżawie poza dzierżawcą z ograniczonym dostępem, loguje się. W takim przypadku informacje identyfikowane przez użytkownika, takie jak nazwa i główna nazwa użytkownika, są maskowane w celu ochrony danych użytkownika w innych dzierżawcach.

Podobnie jak w przypadku innych raportów w Azure Portal, można użyć filtrów, aby określić zakres raportu. Można filtrować według określonego przedziału czasu, użytkownika, aplikacji, klienta lub stanu. Jeśli wybierzesz przycisk **kolumny** , możesz wybrać wyświetlanie danych z dowolną kombinacją następujących pól:

- **Użytkownik**
- **Aplikacja**
- **Stan**
- **Data**
- **Data (UTC)** (gdzie UTC jest uniwersalnym czasem koordynowanym)
- **Metoda auth uwierzytelniania MFA (metoda uwierzytelniania wieloskładnikowego** )
- **Szczegóły uwierzytelniania MFA (szczegóły uwierzytelniania wieloskładnikowego** )
- **Wynik usługi MFA**
- **Adres IP**
- **Klient**
- **Nazwa użytkownika**
- **Lokalizacja**
- **Identyfikator dzierżawy docelowej**

## <a name="microsoft-365-support"></a>Pomoc techniczna platformy Microsoft 365

Aplikacje Microsoft 365 muszą spełniać dwa kryteria, aby w pełni obsługiwać ograniczenia dzierżawy:

1. Używany klient obsługuje nowoczesne uwierzytelnianie.
2. Nowoczesne uwierzytelnianie jest włączone jako domyślny protokół uwierzytelniania dla usługi w chmurze.

Zapoznaj się z [zaktualizowanym nowoczesnego uwierzytelniania pakietu office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/) , aby uzyskać najnowsze informacje na temat tego, którzy klienci pakietu Office obecnie obsługują nowoczesne uwierzytelnianie. Ta strona zawiera również linki do instrukcji dotyczących włączania nowoczesnego uwierzytelniania dla konkretnych dzierżaw usługi Exchange Online i Skype dla firm Online. Usługi SharePoint online już domyślnie umożliwiają nowoczesne uwierzytelnianie.

Microsoft 365 aplikacje oparte na przeglądarce (Portal Office, Yammer, witryny programu SharePoint, program Outlook w sieci Web i inne) obsługują obecnie ograniczenia dzierżawy. Klienci o szerokim zakresie (Outlook, Skype dla firm, Word, Excel, PowerPoint i innych) mogą wymuszać ograniczenia dzierżawy tylko w przypadku korzystania z nowoczesnego uwierzytelniania.  

Klienci programu Outlook i Skype dla firm obsługujący nowoczesne uwierzytelnianie mogą nadal używać starszych protokołów wobec dzierżawców, w których nowoczesne uwierzytelnianie nie jest włączone, co skutecznie pomija ograniczenia dzierżawy. Ograniczenia dzierżawy mogą blokować aplikacje korzystające ze starszych protokołów, jeśli kontaktuje się z login.microsoftonline.com, login.microsoft.com lub login.windows.net podczas uwierzytelniania.

W przypadku programu Outlook w systemie Windows klienci mogą zdecydować się na wdrożenie ograniczeń uniemożliwiających użytkownikom końcowym Dodawanie niezatwierdzonych kont poczty do swoich profilów. Na przykład zapoznaj się z ustawieniem [zapobiegaj dodawaniu zasad grupy innych niż domyślne](https://gpsearch.azurewebsites.net/default.aspx?ref=1) .

## <a name="testing"></a>Testowanie

Jeśli chcesz wypróbować ograniczenia dzierżawy przed wdrożeniem jej w całej organizacji, masz dwie opcje: oparte na hoście rozwiązanie przy użyciu narzędzia, takiego jak programu Fiddler, lub etapowego wdrażania ustawień serwera proxy.

### <a name="fiddler-for-a-host-based-approach"></a>Programu Fiddler dla podejścia opartego na hoście

Programu Fiddler to bezpłatny serwer proxy debugowania sieci Web, który może służyć do przechwytywania i modyfikowania ruchu HTTP/HTTPS, w tym wstawiania nagłówków HTTP. Aby skonfigurować programu Fiddler do testowania ograniczeń dzierżawy, wykonaj następujące czynności:

1. [Pobierz i zainstaluj programu Fiddler](https://www.telerik.com/fiddler).

2. Skonfiguruj programu Fiddler do odszyfrowywania ruchu HTTPS na [programu Fiddler dokumentacji pomocy](https://docs.telerik.com/fiddler/Configure-Fiddler/Tasks/DecryptHTTPS).

3. Skonfiguruj programu Fiddler, aby wstawić nagłówki *ograniczania dostępu do dzierżawców* i *ograniczenia dostępu do kontekstu* przy użyciu reguł niestandardowych:

   1. W narzędziu Web Debugger programu Fiddler wybierz menu **reguły** i wybierz polecenie **Dostosuj reguły...** Aby otworzyć plik CustomRules.

   2. Dodaj następujące wiersze na początku `OnBeforeRequest` funkcji. Zamień \<tenant domain\> na domenę zazarejestrowaną dla dzierżawy (na przykład `contoso.onmicrosoft.com` ). Zamień \<directory ID\> na identyfikator GUID usługi Azure AD Twojej dzierżawy.

      ```JScript.NET
      if (
          oSession.HostnameIs("login.microsoftonline.com") ||
          oSession.HostnameIs("login.microsoft.com") ||
          oSession.HostnameIs("login.windows.net")
      )
      {
          oSession.oRequest["Restrict-Access-To-Tenants"] = "<tenant domain>";
          oSession.oRequest["Restrict-Access-Context"] = "<directory ID>";
      }
      ```

      Jeśli musisz zezwolić na wiele dzierżawców, użyj przecinka do oddzielenia nazw dzierżawców. Na przykład:

      `oSession.oRequest["Restrict-Access-To-Tenants"] = "contoso.onmicrosoft.com,fabrikam.onmicrosoft.com";`

4. Zapisz i zamknij plik CustomRules.

Po skonfigurowaniu programu Fiddler można przechwytywać ruch, przechodząc do menu **plik** i wybierając pozycję **Przechwyć ruch**.

### <a name="staged-rollout-of-proxy-settings"></a>Etapowe wdrażanie ustawień serwera proxy

W zależności od możliwości infrastruktury serwera proxy może być możliwe przygotowanie wdrożenia ustawień dla użytkowników. Poniżej przedstawiono kilka opcji wysokiego poziomu, które należy wziąć pod uwagę:

1. Używaj plików PAC, aby wskazywały na testową infrastrukturę serwera proxy, podczas gdy normalni użytkownicy nadal używają produkcyjnej infrastruktury serwera proxy.
2. Niektóre serwery proxy mogą obsługiwać różne konfiguracje przy użyciu grup.

Aby uzyskać szczegółowe informacje, zapoznaj się z dokumentacją serwera proxy.

## <a name="next-steps"></a>Następne kroki

- Przeczytaj o [zaktualizowanym nowoczesnej uwierzytelnianiu pakietu Office 365](https://www.microsoft.com/en-us/microsoft-365/blog/2015/03/23/office-2013-modern-authentication-public-preview-announced/)
- Przejrzyj [adresy URL i zakresy adresów IP pakietu Office 365](https://support.office.com/article/Office-365-URLs-and-IP-address-ranges-8548a211-3fe7-47cb-abb1-355ea5aa88a2)
