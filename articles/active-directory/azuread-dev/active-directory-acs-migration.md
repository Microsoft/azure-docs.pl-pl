---
title: Migrowanie z usługi Azure Access Control Service | Microsoft Docs
description: Dowiedz się więcej o opcjach przenoszenia aplikacji i usług z usługi Azure Access Control Service (ACS).
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: azuread-dev
ms.custom: aaddev
ms.topic: how-to
ms.workload: identity
ms.date: 10/03/2018
ms.author: ryanwi
ms.reviewer: jlu, annaba, hirsin
ROBOTS: NOINDEX
ms.openlocfilehash: bf50db4c463f5161adcc88d69eb2ae8970526103
ms.sourcegitcommit: db925ea0af071d2c81b7f0ae89464214f8167505
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2021
ms.locfileid: "107515621"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>How to: Migrate from the Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), usługa usługi Azure Active Directory (Azure AD), zostanie wycofana 7 listopada 2018 r. Aplikacje i usługi, które obecnie Access Control, muszą zostać w pełni zmigrowane do innego mechanizmu uwierzytelniania. W tym artykule opisano zalecenia dla bieżących klientów, którzy planują wycofać korzystanie z Access Control. Jeśli obecnie nie używasz Access Control, nie musisz nic zrobić.

## <a name="overview"></a>Omówienie

Access Control to usługa uwierzytelniania w chmurze, która umożliwia łatwe uwierzytelnianie i autoryzację użytkowników w celu uzyskania dostępu do aplikacji i usług internetowych. Umożliwia to wylienie wielu funkcji uwierzytelniania i autoryzacji z kodu. Access Control jest używana głównie przez deweloperów i architektów klientów Microsoft .NET, ASP.NET internetowych i usług internetowych Windows Communication Foundation (WCF).

Przypadki użycia Access Control można podzielić na trzy główne kategorie:

- Uwierzytelnianie w niektórych usługach w chmurze firmy Microsoft, w tym Azure Service Bus i Dynamics CRM. Aplikacje klienckie uzyskają tokeny z Access Control do uwierzytelniania tych usług w celu wykonywania różnych akcji.
- Dodawanie uwierzytelniania do aplikacji internetowych, zarówno niestandardowych, jak i wstępnie pakowanych (takich jak SharePoint). Korzystając z Access Control "pasywnego", aplikacje internetowe mogą obsługiwać logowanie przy użyciu identyfikatora konto Microsoft (dawniej Live ID) oraz kont google, Facebook, Yahoo, Azure AD i Active Directory Federation Services (AD FS).
- Zabezpieczanie niestandardowych usług sieci Web przy użyciu tokenów wystawionych przez Access Control. Przy użyciu "aktywnego" uwierzytelniania usługi sieci Web mogą zapewnić, że zezwalają na dostęp tylko do znanych klientów, którzy zostali uwierzytelnieni za pomocą Access Control.

Każdy z tych przypadków użycia i zalecane strategie migracji zostały omówione w poniższych sekcjach.

> [!WARNING]
> W większości przypadków do migrowania istniejących aplikacji i usług do nowszej technologii wymagane są znaczące zmiany kodu. Zalecamy natychmiastowe rozpoczęcie planowania i wykonywania migracji, aby uniknąć potencjalnych awarii lub przestojów.

Access Control ma następujące składniki:

- Usługa bezpiecznego tokenu (STS), która odbiera żądania uwierzytelniania i w zamian wydaje tokeny zabezpieczające.
- Klasyczny portal Azure, w którym można tworzyć, usuwać oraz włączać i wyłączać Access Control nazw.
- Oddzielny portal Access Control zarządzania, w którym można dostosowywać i konfigurować Access Control nazw.
- Usługa zarządzania, za pomocą której można zautomatyzować funkcje portali.
- Aparat reguł przekształcania tokenów, którego można użyć do definiowania złożonej logiki do manipulowania formatem danych wyjściowych tokenów, które Access Control problemów.

Aby korzystać z tych składników, należy utworzyć co najmniej jedną Access Control nazw. Przestrzeń *nazw* to dedykowane wystąpienie Access Control, z które komunikują się aplikacje i usługi. Przestrzeń nazw jest izolowana od wszystkich Access Control klientów. Inne Access Control używają własnych przestrzeni nazw. Przestrzeń nazw w Access Control ma dedykowany adres URL, który wygląda następująco:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Cała komunikacja z usługą STS i operacjami zarządzania odbywa się pod tym adresem URL. Możesz używać różnych ścieżek do różnych celów. Aby określić, czy aplikacje lub usługi używają Access Control, monitoruj pod https:// przestrzeni nazw &lt; &gt; .accesscontrol.windows.net. Każdy ruch do tego adresu URL jest obsługiwany przez Access Control i musi zostać wycofany. 

Wyjątkiem od tej reguły jest dowolny ruch do `https://accounts.accesscontrol.windows.net` . Ruch do tego adresu URL jest już  obsługiwany przez inną usługę i nie ma na nie wpływu Access Control obsługi. 

Aby uzyskać więcej informacji na Access Control, [zobacz Access Control Service 2.0 (zarchiwizowane).](/previous-versions/azure/azure-services/hh147631(v=azure.100))

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Dowiedz się, na które aplikacje będzie to miało wpływ

Wykonaj kroki opisane w tej sekcji, aby dowiedzieć się, na które aplikacje będzie miało wpływ wycofanie usług ACS.

### <a name="download-and-install-acs-powershell"></a>Pobieranie i instalowanie programu PowerShell dla usługi ACS

1. Przejdź do strony Galeria programu PowerShell i pobierz [plik Acs.Namespaces.](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2)
2. Zainstaluj moduł, uruchamiając

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Pobierz listę wszystkich możliwych poleceń, uruchamiając polecenie

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Aby uzyskać pomoc na temat określonego polecenia, uruchom polecenie:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    gdzie `[Command-Name]` to nazwa polecenia ACS.

### <a name="list-your-acs-namespaces"></a>Lista przestrzeni nazw usług ACS

1. Nawiąż połączenie z usługami ACS przy użyciu polecenia cmdlet **Connect-AcsAccount.**
  
    Aby można było wykonać polecenia, może być konieczne uruchomienie polecenia i bycie administratorem `Set-ExecutionPolicy -ExecutionPolicy Bypass` tych subskrypcji.

2. Aby wyświetlić listę dostępnych subskrypcji platformy Azure, należy użyć polecenia cmdlet **Get-AcsSubscription.**
3. Aby wyświetlić listę przestrzeni nazw usług ACS, należy użyć polecenia cmdlet **Get-AcsNamespace.**

### <a name="check-which-applications-will-be-impacted"></a>Sprawdzanie, na które aplikacje będzie to miało wpływ

1. Użyj przestrzeni nazw z poprzedniego kroku i przejdź do `https://<namespace>.accesscontrol.windows.net`

    Jeśli na przykład jedna z przestrzeni nazw to contoso-test, przejdź do strony `https://contoso-test.accesscontrol.windows.net`

2. W **obszarze Relacje zaufania** wybierz pozycję Aplikacje **jednostki zależnej,** aby wyświetlić listę aplikacji, na które będzie miało wpływ wycofanie usług ACS.
3. Powtórz kroki 1–2 dla wszystkich innych posiadanych przestrzeni nazw usług ACS.

## <a name="retirement-schedule"></a>Harmonogram wycofania

Od listopada 2017 r. wszystkie Access Control są w pełni obsługiwane i operacyjne. Jedynym ograniczeniem jest to, że nie można tworzyć nowych [przestrzeni nazw Access Control za pośrednictwem klasycznego portalu Azure.](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/)

Oto harmonogram wycofań składników Access Control składników:

- **Listopad 2017** r. : środowisko administratora usługi Azure AD w klasycznym portalu Azure [zostanie wycofane.](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/) W tym momencie zarządzanie przestrzeniami nazw dla Access Control jest dostępne pod nowym dedykowanym adresem URL: `https://manage.windowsazure.com?restoreClassic=true` . Użyj tej listy URl, aby wyświetlić istniejące przestrzenie nazw, włączyć i wyłączyć przestrzenie nazw oraz usunąć przestrzenie nazw, jeśli chcesz.
- **2 kwietnia 2018** r. : Klasyczny portal Azure został całkowicie wycofany, co oznacza, że Access Control przestrzeni nazw nie jest już dostępna za pośrednictwem żadnego adresu URL. W tym momencie nie można wyłączyć ani włączyć, usunąć ani wyliczyć Access Control nazw. Jednak portal zarządzania Access Control będzie w pełni funkcjonalny i znajduje się w `https://<namespace>.accesscontrol.windows.net` . Wszystkie inne składniki Access Control nadal działają normalnie.
- **7 listopada 2018 r.:** wszystkie Access Control są trwale zamykane. Obejmuje to Access Control zarządzania, usługę zarządzania, usługę STS i aparat reguł przekształcania tokenów. W tym momencie wszystkie żądania wysyłane do Access Control (znajdujące się w \<namespace\> .accesscontrol.windows.net) nie powiodą się. Przed tym czasem wszystkie istniejące aplikacje i usługi powinny zostać zmigrowane do innych technologii.

> [!NOTE]
> Zasady wyłączą przestrzenie nazw, które nie żądały tokenu przez określony czas. Od początku września 2018 r. ten okres wynosi obecnie 14 dni braku aktywności, ale zostanie to skrócone do 7 dni braku aktywności w najbliższych tygodniach. Jeśli masz Access Control, które są obecnie wyłączone, możesz pobrać i zainstalować program [ACS PowerShell,](#download-and-install-acs-powershell) aby ponownie włączyć przestrzenie nazw.

## <a name="migration-strategies"></a>Strategie migracji

W poniższych sekcjach opisano zalecenia wysokiego poziomu dotyczące migracji z Access Control do innych technologii firmy Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Klienci usług w chmurze firmy Microsoft

Każda usługa firmy Microsoft w chmurze, która akceptuje tokeny wystawione przez usługę Access Control obsługuje teraz co najmniej jedną alternatywną formę uwierzytelniania. Prawidłowy mechanizm uwierzytelniania różni się dla każdej usługi. Zalecamy zapoznanie się z konkretną dokumentacją dla każdej usługi, aby uzyskać oficjalne wskazówki. Dla wygody każdy zestaw dokumentacji jest dostarczany tutaj:

| Usługa | Wskazówki |
| ------- | -------- |
| Usługa Azure Service Bus | [Migrowanie do sygnatur dostępu współdzielonych](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Azure Service Bus Relay | [Migrowanie do sygnatur dostępu współdzielonych](../../azure-relay/relay-migrate-acs-sas.md) |
| Azure Managed Cache | [Migrowanie do usługi Azure Cache for Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [Migracja do interfejsy API usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migrowanie do Logic Apps funkcji Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Migrowanie do uwierzytelniania usługi Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uaktualnianie Azure Backup agenta](../../backup/backup-azure-file-folder-backup-faq.yml) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Klienci programu SharePoint

Klienci korzystający z usług SharePoint 2013, 2016 i SharePoint Online od dawna używali usług ACS do celów uwierzytelniania w chmurze, lokalnie i w scenariuszach hybrydowych. Wycofanie usługi ACS będzie mieć wpływ na niektóre funkcje programu SharePoint i przypadki użycia, a inne nie. W poniższej tabeli przedstawiono wskazówki dotyczące migracji niektórych najpopularniejszych funkcji programu SharePoint, które wykorzystują usługi ACS:

| Cecha | Wskazówki |
| ------- | -------- |
| Uwierzytelnianie użytkowników z usługi Azure AD | Wcześniej usługa Azure AD nie obsługiła tokenów SAML 1.1 wymaganych przez program SharePoint do uwierzytelniania, a usługi ACS były używane jako pośrednik, który sprawił, że program SharePoint jest zgodny z formatami tokenów usługi Azure AD. Teraz możesz połączyć program [SharePoint bezpośrednio z usługą Azure AD przy użyciu aplikacja usługi Azure AD SharePoint z galerii w aplikacji lokalnej.](../saas-apps/sharepoint-on-premises-tutorial.md) |
| [Uwierzytelnianie aplikacji & uwierzytelnianie serwer-serwer w lokalnym programie SharePoint](/SharePoint/security-for-sharepoint-server/authentication-overview) | Wycofanie ACS nie ma na to wpływu; nie trzeba wprowadzać żadnych zmian. | 
| [Autoryzacja o niskim zaufaniu dla dodatków programu SharePoint (hostowanych przez dostawcę i hostowanych w programie SharePoint)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Wycofanie ACS nie ma na to wpływu; nie trzeba wprowadzać żadnych zmian. |
| [Wyszukiwanie hybrydowe w chmurze programu SharePoint](/archive/blogs/spses/cloud-hybrid-search-service-application) | Wycofanie ACS nie ma na to wpływu; nie trzeba wprowadzać żadnych zmian. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplikacje internetowe, które korzystają z uwierzytelniania pasywnego

W przypadku aplikacji internetowych, które Access Control uwierzytelniania użytkowników, Access Control oferuje następujące funkcje i możliwości deweloperom i architektom aplikacji internetowych:

- Głęboka integracja z programem Windows Identity Foundation (WIF).
- Federacja z kontami firm Google, Facebook, Yahoo, Azure Active Directory i AD FS oraz kontami Microsoft.
- Obsługa następujących protokołów uwierzytelniania: OAuth 2.0 Draft 13, WS-Trust i Web Services Federation (WS-Federation).
- Obsługa następujących formatów tokenów: JSON Web Token (JWT), SAML 1.1, SAML 2.0 i Simple Web Token (SWT).
- Środowisko odnajdywania w zakresie macierzyscym zintegrowane z programem WIF, które umożliwia użytkownikom wybieranie typu konta, za pomocą których się logują. To środowisko jest hostowane przez aplikację internetową i można go w pełni dostosowywać.
- Przekształcanie tokenu, które umożliwia zaawansowane dostosowywanie oświadczeń otrzymywanych przez aplikację internetową z Access Control, w tym:
    - Przekazywać oświadczenia od dostawców tożsamości.
    - Dodawanie dodatkowych oświadczeń niestandardowych.
    - Prosta logika if-then do wydawania oświadczeń w określonych warunkach.

Niestety, nie ma jednej usługi, która oferuje wszystkie te równoważne możliwości. Należy ocenić, które Access Control usługi, a następnie wybrać między usługą [Azure Active Directory,](https://azure.microsoft.com/develop/identity/signin/) [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) lub inną usługą uwierzytelniania w chmurze.

#### <a name="migrate-to-azure-active-directory"></a>Migrowanie do Azure Active Directory

Należy rozważyć zintegrowanie aplikacji i usług bezpośrednio z usługą Azure AD. Usługa Azure AD to oparty na chmurze dostawca tożsamości dla kont służbowych Microsoft. Usługa Azure AD to dostawca tożsamości dla Microsoft 365, platformy Azure i wielu innych. Oferuje ona podobne możliwości uwierzytelniania feder Access Control, ale nie obsługuje wszystkich Access Control funkcji. 

Podstawowym przykładem jest federacja z dostawcami tożsamości społecznościowych, takimi jak Facebook, Google i Yahoo. Jeśli użytkownicy logują się przy użyciu tych typów poświadczeń, usługa Azure AD nie jest rozwiązaniem dla Ciebie. 

Usługa Azure AD również nie musi obsługiwać dokładnie tych samych protokołów uwierzytelniania, co Access Control. Na przykład mimo że zarówno Access Control, jak i usługa Azure AD obsługują uwierzytelnianie OAuth, istnieją subtelne różnice między poszczególnymi implementacjami. Różne implementacje wymagają zmodyfikowania kodu w ramach migracji.

Jednak usługa Azure AD zapewnia kilka potencjalnych korzyści dla Access Control klientów. Natywnie obsługuje konta służbowe Microsoft hostowane w chmurze, które są często używane Access Control klientów. 

Dzierżawę usługi Azure AD można również federować z co najmniej jednym wystąpieniem usługi lokalna usługa Active Directory za pośrednictwem AD FS. Dzięki temu aplikacja może uwierzytelniać użytkowników w chmurze i użytkowników hostowanych lokalnie. Obsługuje również protokół WS-Federation, co sprawia, że stosunkowo prosta jest integracja z aplikacją internetową przy użyciu programu WIF.

W poniższej tabeli porównano funkcje usługi Access Control dotyczące aplikacji internetowych z funkcjami dostępnymi w usłudze Azure AD. 

Na wysokim poziomie usługa Azure Active Directory prawdopodobnie najlepszym wyborem w przypadku migracji, jeśli pozwalasz użytkownikom logować się tylko przy użyciu kont *służbowych Microsoft.*

| Możliwość | Access Control pomocy technicznej | Obsługa usługi Azure AD |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Konta służbowe Microsoft | Obsługiwane | Obsługiwane |
| Konta z Windows Server Active Directory i AD FS |— Obsługiwane za pośrednictwem federacji z dzierżawą usługi Azure AD <br />- Obsługiwane za pośrednictwem federacji bezpośredniej z AD FS | Obsługiwane tylko za pośrednictwem federacji z dzierżawą usługi Azure AD | 
| Konta z innych systemów zarządzania tożsamościami przedsiębiorstwa |— Możliwe za pośrednictwem federacji z dzierżawą usługi Azure AD <br />- Obsługiwane za pośrednictwem federacji bezpośredniej | Możliwe za pośrednictwem federacji z dzierżawą usługi Azure AD |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane za pośrednictwem protokołu OAuth usługi Azure AD w wersji 2.0, ale nie za pośrednictwem innych protokołów | 
| Konta Facebook, Google, Yahoo | Obsługiwane | Nie są obsługiwane w żaden sposób |
| **Zgodność protokołów i zestawu SDK** | | |
| WIF | Obsługiwane | Obsługiwane, ale dostępne są ograniczone instrukcje |
| WS-Federation | Obsługiwane | Obsługiwane |
| OAuth 2.0 | Obsługa wersji Roboczej 13 | Obsługa najnowszej specyfikacji RFC 6749 |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Formaty tokenów** | | |
| JWT | Obsługiwane w wersji beta | Obsługiwane |
| SAML 1.1 | Obsługiwane | Wersja zapoznawcza |
| SAML 2.0 | Obsługiwane | Obsługiwane |
| Swt | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Dostosowywalny interfejs użytkownika odnajdywania/wybierania kont w macierzy głównej | Kod do pobrania, który można włączyć do aplikacji | Nieobsługiwane |
| Przekazywanie niestandardowych certyfikatów podpisywania tokenu | Obsługiwane | Obsługiwane |
| Dostosowywanie oświadczeń w tokenach |- Pass through input claims from identity providers (Przekaż oświadczenia wejściowe od dostawców tożsamości)<br />- Get access token from identity provider as a claim (Uzyskiwanie tokenu dostępu od dostawcy tożsamości jako oświadczenie)<br />- Wydawanie oświadczeń wyjściowych na podstawie wartości oświadczeń wejściowych<br />- Wydawanie oświadczeń wyjściowych z wartościami stałymi |- Nie można przekazać oświadczeń od dostawców tożsamości federacyjnych<br />- Nie można uzyskać tokenu dostępu od dostawcy tożsamości jako oświadczenia<br />- Nie można wystawiać oświadczeń wyjściowych na podstawie wartości oświadczeń wejściowych<br />- Może wystawiać oświadczenia wyjściowe ze stałymi wartościami<br />— Może wystawiać oświadczenia wyjściowe na podstawie właściwości użytkowników zsynchronizowanych z usługą Azure AD |
| **Automatyzacja** | | |
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane za pośrednictwem usługi Access Control Management Service | Obsługiwane przy użyciu interfejsu API Microsoft Graph API |

Jeśli zdecydujesz, że usługa Azure AD jest najlepszą ścieżką migracji dla Twoich aplikacji i usług, musisz wiedzieć o dwóch sposobach integracji aplikacji z usługą Azure AD.

Aby zintegrować program WS-Federation lub WIF z usługą Azure AD, zalecamy zastosowanie podejścia opisanego w te tematze [Configure federated single sign-on for a non-gallery application](../manage-apps/configure-saml-single-sign-on.md)(Konfigurowanie federistycznego logowania pojedynczego dla aplikacji spoza galerii). Artykuł dotyczy konfigurowania usługi Azure AD do logowania pojedynczego opartego na forsłudze SAML, ale działa również w przypadku konfigurowania federacji WS. Takie podejście wymaga licencji Azure AD — wersja Premium licencji. Takie podejście ma dwie zalety:

- Zapewniasz pełną elastyczność dostosowywania tokenu usługi Azure AD. Oświadczenia wystawione przez usługę Azure AD można dostosować tak, aby dopasować je do oświadczeń wystawionych przez Access Control. Dotyczy to zwłaszcza oświadczenia identyfikator użytkownika lub identyfikator nazwy. Aby nadal otrzymywać spójne identyfikatory IDentifier dla użytkowników po zmianie technologii, upewnij się, że identyfikatory użytkowników wystawione przez usługę Azure AD są zgodne z identyfikatorami wystawionymi przez usługę Access Control.
- Możesz skonfigurować certyfikat podpisywania tokenu, który jest specyficzny dla Twojej aplikacji i ma kontrolowany okres istnienia.

> [!NOTE]
> To podejście wymaga licencji Azure AD — wersja Premium licencji. Jeśli jesteś klientem Access Control i potrzebujesz licencji Premium w celu skonfigurowania logowania pojedynczego dla aplikacji, skontaktuj się z nami. Z przyjemnością udostępnimy licencje dewelopera do użycia.

Alternatywnym podejściem jest obserwowanie [tego przykładowego kodu](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), który zawiera nieco inne instrukcje dotyczące konfigurowania usługi WS-Federation. Ten przykładowy kod nie używa programu WIF, ale raczej oprogramowania pośredniczącego OWIN ASP.NET 4.5. Jednak instrukcje dotyczące rejestracji aplikacji są prawidłowe dla aplikacji korzystających z programu WIF i nie wymagają licencji Azure AD — wersja Premium aplikacji. 

Jeśli wybierzesz to podejście, musisz zrozumieć przewłaczanie klucza [podpisywania w usłudze Azure AD.](../develop/active-directory-signing-key-rollover.md) W tym podejściu tokeny są wystawiane przy użyciu globalnego klucza podpisywania usługi Azure AD. Domyślnie program WIF nie odświeża automatycznie kluczy podpisywania. Gdy usługa Azure AD obraca swoje globalne klucze podpisywania, implementacja programu WIF musi być przygotowana do zaakceptowania zmian. Aby uzyskać więcej informacji, zobacz [Ważne informacje dotyczące przechowania klucza podpisywania w usłudze Azure AD.](/previous-versions/azure/dn641920(v=azure.100))

Jeśli możesz zintegrować usługę z usługą Azure AD za pośrednictwem OpenID Connect lub protokołu OAuth, zalecamy wykonanie tej pracy. Mamy obszerną dokumentację i wskazówki dotyczące sposobu integracji usługi Azure AD z aplikacją internetową, które są dostępne w naszym [przewodniku dewelopera usługi Azure AD.](../develop/index.yml)

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migrowanie do Azure Active Directory B2C

Inną ścieżką migracji, którą należy wziąć pod uwagę, jest Azure AD B2C. Azure AD B2C to usługa uwierzytelniania w chmurze, która, podobnie jak usługa Access Control, umożliwia deweloperom zewnętrzne źródło logiki uwierzytelniania i zarządzania tożsamościami do usługi w chmurze. Jest to płatna usługa (w warstwach Bezpłatna i Premium) przeznaczona dla aplikacji przeznaczonych dla konsumentów, które mogą mieć nawet miliony aktywnych użytkowników.

Podobnie jak Access Control, jedną z najbardziej atrakcyjnych funkcji Azure AD B2C jest to, że obsługuje ona wiele różnych typów kont. Dzięki Azure AD B2C możesz logować użytkowników przy użyciu kont konto Microsoft, Facebook, Google, LinkedIn, GitHub lub Yahoo i nie tylko. Azure AD B2C obsługuje również "konta lokalne" lub nazwę użytkownika i hasła, które użytkownicy tworzą specjalnie dla Aplikacji. Azure AD B2C zapewnia również rozbudowaną rozszerzalność, która umożliwia dostosowywanie przepływów logowania. 

Jednak Azure AD B2C nie obsługuje wielu protokołów uwierzytelniania i formatów tokenów, których mogą Access Control klienci. Nie można również użyć usługi Azure AD B2C tokenów i zapytania o dodatkowe informacje o użytkowniku od dostawcy tożsamości firmy Microsoft lub w inny sposób.

W poniższej tabeli porównano funkcje aplikacji Access Control które są istotne dla aplikacji internetowych, z tymi, które są dostępne w Azure AD B2C. Na wysokim poziomie Azure AD B2C prawdopodobnie właściwym wyborem dla migracji, jeśli aplikacja jest skierowana do konsumentów lub jeśli obsługuje wiele *różnych typów kont.*

| Możliwość | Access Control pomocy technicznej | Azure AD B2C pomocy technicznej |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Konta służbowe Microsoft | Obsługiwane | Obsługiwane za pośrednictwem zasad niestandardowych  |
| Konta z Windows Server Active Directory i AD FS | Obsługiwane za pośrednictwem federacji bezpośredniej z AD FS | Obsługiwane za pośrednictwem federacji SAML przy użyciu zasad niestandardowych |
| Konta z innych systemów zarządzania tożsamościami przedsiębiorstwa | Obsługiwane za pośrednictwem federacji bezpośredniej za pośrednictwem WS-Federation | Obsługiwane za pośrednictwem federacji SAML przy użyciu zasad niestandardowych |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane | 
| Konta Facebook, Google, Yahoo | Obsługiwane | Serwisy Facebook i Google są obsługiwane natywnie, firma Yahoo jest obsługiwana OpenID Connect federacji przy użyciu zasad niestandardowych |
| **Zgodność protokołów i zestawu SDK** | | |
| Windows Identity Foundation (WIF) | Obsługiwane | Nieobsługiwane |
| WS-Federation | Obsługiwane | Nieobsługiwane |
| OAuth 2.0 | Obsługa wersji roboczej 13 | Obsługa najnowszej specyfikacji RFC 6749 |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Formaty tokenów** | | |
| JWT | Obsługiwane w wersji beta | Obsługiwane |
| SAML 1.1 | Obsługiwane | Nieobsługiwane |
| SAML 2.0 | Obsługiwane | Nieobsługiwane |
| Swt | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Dostosowywalny interfejs użytkownika odnajdywania/wybierania kont w macierzy głównej | Kod do pobrania, który można włączyć do aplikacji | W pełni dostosowywalny interfejs użytkownika za pośrednictwem niestandardowego kodu CSS |
| Przekazywanie niestandardowych certyfikatów podpisywania tokenu | Obsługiwane | Niestandardowe klucze podpisywania, a nie certyfikaty, obsługiwane za pośrednictwem zasad niestandardowych |
| Dostosowywanie oświadczeń w tokenach |- Pass through input claims from identity providers (Przekaż oświadczenia wejściowe od dostawców tożsamości)<br />- Get access token from identity provider as a claim (Uzyskiwanie tokenu dostępu od dostawcy tożsamości jako oświadczenie)<br />- Wydawanie oświadczeń wyjściowych na podstawie wartości oświadczeń wejściowych<br />- Wydawanie oświadczeń wyjściowych z wartościami stałymi |- Może przekazywać oświadczenia od dostawców tożsamości; zasady niestandardowe wymagane dla niektórych oświadczeń<br />- Nie można uzyskać tokenu dostępu od dostawcy tożsamości jako oświadczenia<br />- Może wystawiać oświadczenia wyjściowe na podstawie wartości oświadczeń wejściowych za pośrednictwem zasad niestandardowych<br />- Może wystawiać oświadczenia wyjściowe ze stałymi wartościami za pośrednictwem zasad niestandardowych |
| **Automatyzacja** | | |
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane za pośrednictwem usługi Access Control Management Service |— Tworzenie użytkowników dozwolonych przy użyciu interfejsu API Microsoft Graph API<br />— Nie można programowo tworzyć dzierżaw, aplikacji lub zasad B2C |

Jeśli zdecydujesz, Azure AD B2C jest najlepszą ścieżką migracji dla Twoich aplikacji i usług, zacznij od następujących zasobów:

- [Azure AD B2C dokumentacji](../../active-directory-b2c/overview.md)
- [Zasady niestandardowe usługi Azure AD B2C](../../active-directory-b2c/custom-policy-overview.md)
- [Azure AD B2C cen](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrowanie do tożsamości ping lub uwierzytelniania Auth0

W niektórych przypadkach może się okazać, że usługi Azure AD i Azure AD B2C nie wystarczają do zastąpienia Access Control w aplikacjach internetowych bez dokonywania istotnych zmian w kodzie. Oto kilka typowych przykładów:

- Aplikacje internetowe, które używają programu WIF WS-Federation do logowania się za pomocą dostawców tożsamości społecznościowych, takich jak Google lub Facebook.
- Aplikacje internetowe, które wykonują federację bezpośrednią z dostawcą tożsamości przedsiębiorstwa za pośrednictwem WS-Federation protokołu.
- Aplikacje internetowe, które wymagają tokenu dostępu wystawionego przez dostawcę tożsamości społecznościowych (takiego jak Google lub Facebook) jako oświadczenie w tokenach wystawionych przez Access Control.
- Aplikacje internetowe ze złożonymi regułami przekształcania tokenów, których usługa Azure AD Azure AD B2C nie może odtworzyć.
- Wielodostępne aplikacje internetowe, które używają usług ACS do centralnego zarządzania federacją wielu różnych dostawców tożsamości

W takich przypadkach warto rozważyć migrowanie aplikacji internetowej do innej usługi uwierzytelniania w chmurze. Zalecamy zapoznanie się z następującymi opcjami. Każda z następujących opcji oferuje możliwości podobne do Access Control:

![Ten obraz przedstawia logo Auth0](./media/active-directory-acs-migration/rsz-auth0.png) 

[Auth0](https://auth0.com/acs) to elastyczna usługa tożsamości w chmurze, która utworzyła wytyczne dotyczące migracji wysokiego poziomu dla klientów usługi [Access Control](https://auth0.com/acs)i obsługuje niemal każdą funkcję, która jest dostępna w usłudze ACS.

![Ten obraz przedstawia logo tożsamości ping](./media/active-directory-acs-migration/rsz-ping.png)

[Tożsamość ping](https://www.pingidentity.com) oferuje dwa rozwiązania podobne do ACS. PingOne to usługa tożsamości w chmurze, która obsługuje wiele takich samych funkcji jak usługi ACS, a PingFederate to podobny produkt do obsługi tożsamości lokalnych, który oferuje większą elastyczność. Aby uzyskać więcej informacji na temat korzystania z tych produktów, zapoznaj się ze wskazówkami usługi Ping w zakresie wycofania usług ACS.

Naszym celem w pracy z tożsamością Ping i Auth0 jest zapewnienie, że wszyscy klienci usługi Access Control mają ścieżkę migracji dla swoich aplikacji i usług, która minimalizuje ilość pracy wymaganej do przeniesienia z Access Control.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Usługi sieci Web, które używają aktywnego uwierzytelniania

W przypadku usług sieci Web zabezpieczonych za pomocą tokenów wystawionych przez Access Control Access Control oferuje następujące funkcje i możliwości:

- Możliwość zarejestrowania co najmniej jednej *tożsamości usługi* w twojej Access Control nazw. Tożsamości usługi mogą służyć do żądania tokenów.
- Obsługa protokołów OAuth WRAP i OAuth 2.0 Draft 13 na żądania tokenów przy użyciu następujących typów poświadczeń:
    - Proste hasło utworzone dla tożsamości usługi
    - Podpisany SWT przy użyciu klucza symetrycznego lub certyfikatu X509
    - Token SAML wystawiony przez zaufanego dostawcę tożsamości (zazwyczaj jest to AD FS wystąpienie)
- Obsługa następujących formatów tokenów: JWT, SAML 1.1, SAML 2.0 i SWT.
- Proste reguły przekształcania tokenów.

Tożsamości usług w Access Control są zwykle używane do implementowania uwierzytelniania serwer-serwer. 

#### <a name="migrate-to-azure-active-directory"></a>Migrowanie do Azure Active Directory

Naszym zaleceniem dla tego typu przepływu uwierzytelniania jest migracja do [usługi Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Usługa Azure AD to oparty na chmurze dostawca tożsamości dla kont służbowych Microsoft. Usługa Azure AD to dostawca tożsamości dla Microsoft 365, platformy Azure i wielu innych. 

Usługi Azure AD można również używać do uwierzytelniania serwer-serwer przy użyciu implementacji usługi Azure AD udzielania poświadczeń klienta OAuth. W poniższej tabeli porównano możliwości Access Control uwierzytelniania serwer-serwer z tymi, które są dostępne w usłudze Azure AD.

| Możliwość | Access Control pomocy technicznej | Obsługa usługi Azure AD |
| ---------- | ----------- | ---------------- |
| Jak zarejestrować usługę internetową | Tworzenie jednostki zależnej w portalu Access Control zarządzania | Tworzenie aplikacji internetowej usługi Azure AD w Azure Portal |
| Jak zarejestrować klienta | Tworzenie tożsamości usługi w Access Control portalu zarządzania | Tworzenie innej aplikacji internetowej usługi Azure AD w Azure Portal |
| Używany protokół |- Protokół OAuth WRAP<br />— Przyznawanie poświadczeń klienta protokołu OAuth 2.0 Draft 13 | Przyznawanie poświadczeń klienta OAuth 2.0 |
| Metody uwierzytelniania klientów |— Proste hasło<br />- Podpisany SWT<br />— Token SAML od dostawcy tożsamości federacji |— Proste hasło<br />— Podpisany JWT |
| Formaty tokenów |- JWT<br />— SAML 1.1<br />— SAML 2.0<br />- SWT<br /> | Tylko JWT |
| Przekształcanie tokenu |— Dodawanie oświadczeń niestandardowych<br />- Prosta logika wystawiania oświadczenia if-then | Dodawanie oświadczeń niestandardowych | 
| Automatyzowanie zadań konfiguracji i zarządzania | Obsługiwane za pośrednictwem usługi Access Control Management Service | Obsługiwane przy użyciu interfejsu API Microsoft Graph API |

Aby uzyskać wskazówki dotyczące implementowania scenariuszy serwer-serwer, zobacz następujące zasoby:

- Sekcja Service-to-Service przewodnika dewelopera [usługi Azure AD](../develop/index.yml)
- [Przykładowy kod demona przy użyciu prostych poświadczeń klienta haseł](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Przykładowy kod demona przy użyciu poświadczeń klienta certyfikatu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrowanie do tożsamości ping lub uwierzytelniania Auth0

W niektórych przypadkach może się okazać, że poświadczenia klienta usługi Azure AD i implementacja przyznawania OAuth nie wystarczają do zastąpienia Access Control w architekturze bez istotnych zmian kodu. Niektóre typowe przykłady mogą obejmować:

- Uwierzytelnianie serwer-serwer przy użyciu formatów tokenów innych niż JWT.
- Uwierzytelnianie serwer-serwer przy użyciu tokenu wejściowego dostarczonego przez zewnętrznego dostawcę tożsamości.
- Uwierzytelnianie serwer-serwer przy użyciu reguł przekształcania tokenów, których usługa Azure AD nie może odtworzyć.

W takich przypadkach można rozważyć uwierzytelnianie aplikacji internetowej do innej usługi uwierzytelniania w chmurze. Zalecamy zapoznanie się z następującymi opcjami. Każda z następujących opcji oferuje możliwości podobne do Access Control:

![Ten obraz przedstawia logo Auth0](./media/active-directory-acs-migration/rsz-auth0.png)

[Auth0](https://auth0.com/acs) to elastyczna usługa tożsamości w chmurze, która utworzyła wskazówki dotyczące migracji wysokiego poziomu dla klientów usługi [Access Control](https://auth0.com/acs)i obsługuje niemal każdą funkcję, która jest dostępna w usłudze ACS.

![Na tej ilustracji przedstawiono, że tożsamość ping tożsamości z logo ](./media/active-directory-acs-migration/rsz-ping.png)
 [usługi Ping](https://www.pingidentity.com) oferuje dwa rozwiązania podobne do ACS. PingOne to usługa tożsamości w chmurze, która obsługuje wiele tych samych funkcji co usługa ACS, a PingFederate to podobny produkt tożsamości lokalnej, który oferuje większą elastyczność. Aby uzyskać więcej informacji na temat korzystania z tych produktów, zapoznaj się ze wskazówkami usługi Ping w zakresie wycofania usług ACS.

Naszym celem w pracy z tożsamością Ping i Auth0 jest zapewnienie, że wszyscy klienci usługi Access Control mają ścieżkę migracji dla swoich aplikacji i usług, która minimalizuje ilość pracy wymaganej do przeniesienia z Access Control.

#### <a name="passthrough-authentication"></a>Uwierzytelnianie za pomocą przekazania

W przypadku uwierzytelniania passthrough z dowolną transformacją tokenu nie ma równoważnej technologii firmy Microsoft z usługami ACS. Jeśli tego potrzebują twoi klienci, Auth0 może być tym, który zapewnia najbliższe rozwiązanie przybliżenia.

## <a name="questions-concerns-and-feedback"></a>Pytania, wątpliwości i opinie

Rozumiemy, że Access Control wielu klientów nie znajdzie jasnej ścieżki migracji po przeczytaniu tego artykułu. Może być potrzebna pomoc lub wskazówki dotyczące określania właściwego planu. Jeśli chcesz omówić scenariusze migracji i pytania, pozostaw komentarz na tej stronie.