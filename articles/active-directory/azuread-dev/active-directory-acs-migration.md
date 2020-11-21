---
title: Migrowanie z usługi Azure Access Control Service | Microsoft Docs
description: Dowiedz się więcej na temat opcji przeniesienia aplikacji i usług z usługi Azure Access Control Service (ACS).
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
ms.openlocfilehash: eda648a4d00a0ab4a51c66510060ce16421972ff
ms.sourcegitcommit: 10d00006fec1f4b69289ce18fdd0452c3458eca5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/21/2020
ms.locfileid: "95020014"
---
# <a name="how-to-migrate-from-the-azure-access-control-service"></a>Instrukcje: Migrowanie z usługi Azure Access Control Service

[!INCLUDE [active-directory-azuread-dev](../../../includes/active-directory-azuread-dev.md)]

Microsoft Azure Access Control Service (ACS), usługa Azure Active Directory (Azure AD) zostanie wycofana w dniu 7 listopada 2018. Aplikacje i usługi, które obecnie używają Access Control muszą być w pełni migrowane do innego mechanizmu uwierzytelniania, przez program. W tym artykule opisano zalecenia dla bieżących klientów, ponieważ planowane jest wycofanie korzystania z Access Control. Jeśli obecnie nie używasz Access Control, nie musisz podejmować żadnych działań.

## <a name="overview"></a>Omówienie

Access Control to usługa uwierzytelniania w chmurze, która umożliwia łatwe uwierzytelnianie i Autoryzowanie użytkowników w celu uzyskania dostępu do aplikacji i usług sieci Web. Umożliwia ona rozdanie kodu w wielu funkcjach uwierzytelniania i autoryzacji. Access Control jest używany głównie przez deweloperów i architektów Microsoft .NET klientów, ASP.NET aplikacji sieci Web i usług sieci Web Windows Communication Foundation (WCF).

Przypadki użycia dla Access Control mogą być podzielone na trzy główne kategorie:

- Uwierzytelnianie w określonych usługach firmy Microsoft w chmurze, w tym Azure Service Bus i Dynamics CRM. Aplikacje klienckie uzyskują tokeny z Access Control, aby uwierzytelniać się w tych usługach w celu wykonywania różnych akcji.
- Dodawanie uwierzytelniania do aplikacji sieci Web, zarówno niestandardowych, jak i nieopakowanych (takich jak program SharePoint). Korzystając z Access Control "pasywne", aplikacje sieci Web mogą obsługiwać logowanie za pomocą konto Microsoft (dawniej identyfikator Live ID) oraz z kontami z usług Google, Facebook, Yahoo, Azure AD i Active Directory Federation Services (AD FS).
- Zabezpieczanie niestandardowych usług sieci Web przy użyciu tokenów wystawionych przez Access Control. Korzystając z uwierzytelniania "Active", usługi sieci Web mogą zapewnić, że zezwalają na dostęp tylko znanym klientom uwierzytelnionym przy użyciu Access Control.

Każdy z tych przypadków użycia i zalecane strategie migracji zostały omówione w poniższych sekcjach.

> [!WARNING]
> W większości przypadków istotne zmiany kodu są wymagane do migrowania istniejących aplikacji i usług do nowszych technologii. Zalecamy natychmiastowe rozpoczęcie planowania i wykonywania migracji, aby uniknąć ewentualnych awarii lub przestojów.

Access Control ma następujące składniki:

- Usługa Secure Token Service (STS), która odbiera żądania uwierzytelniania i wystawia tokeny zabezpieczające w programie Return.
- Klasyczny portal Azure, w którym można tworzyć, usuwać i włączać i wyłączać Access Control przestrzenie nazw.
- Osobny Portal zarządzania Access Control, w którym można dostosowywać i konfigurować Access Control przestrzenie nazw.
- Usługa zarządzania, której można użyć do zautomatyzowania funkcji portalu.
- Aparat reguł przekształcania tokenów, którego można użyć do zdefiniowania złożonej logiki w celu manipulowania formatem danych wyjściowych tokenów, które Access Controlą problemy.

Aby użyć tych składników, należy utworzyć co najmniej jeden Access Control przestrzeni nazw. *Przestrzeń nazw* jest dedykowanym wystąpieniem Access Control, do którego komunikują się aplikacje i usługi. Przestrzeń nazw jest odizolowana od wszystkich innych klientów Access Control. Inni klienci Access Control korzystają z własnych przestrzeni nazw. Przestrzeń nazw w Access Control ma dedykowany adres URL, który wygląda następująco:

```HTTP
https://<mynamespace>.accesscontrol.windows.net
```

Cała komunikacja z operacjami usługi STS i zarządzania odbywa się przy użyciu tego adresu URL. Różne ścieżki są używane do różnych celów. Aby określić, czy aplikacje lub usługi używają Access Control, Monitoruj dla każdego ruchu do https:// &lt; przestrzeń nazw &gt; . AccessControl.Windows.NET. Każdy ruch do tego adresu URL jest obsługiwany przez Access Control i musi być wycofany. 

Wyjątkiem jest dowolny ruch do `https://accounts.accesscontrol.windows.net` . Ruch do tego adresu URL jest już obsługiwany przez inną usługę i **nie ma** wpływ na przestarzałą Access Control. 

Aby uzyskać więcej informacji na temat Access Control, zobacz [Access Control Service 2,0 (Archiwalny)](/previous-versions/azure/azure-services/hh147631(v=azure.100)).

## <a name="find-out-which-of-your-apps-will-be-impacted"></a>Dowiedz się, których aplikacji dotyczy problem

Postępuj zgodnie z instrukcjami w tej sekcji, aby dowiedzieć się, które aplikacje mają wpływ na wycofanie usługi ACS.

### <a name="download-and-install-acs-powershell"></a>Pobieranie i Instalowanie programu PowerShell ACS

1. Przejdź do Galeria programu PowerShell i Pobierz [ACS. Namespaces](https://www.powershellgallery.com/packages/Acs.Namespaces/1.0.2).
2. Zainstaluj moduł, uruchamiając

    ```powershell
    Install-Module -Name Acs.Namespaces
    ```

3. Pobierz listę wszystkich możliwych poleceń, uruchamiając polecenie

    ```powershell
    Get-Command -Module Acs.Namespaces
    ```

    Aby uzyskać pomoc dotyczącą określonego polecenia, uruchom polecenie:

    ```
     Get-Help [Command-Name] -Full
    ```
    
    gdzie `[Command-Name]` to nazwa polecenia ACS.

### <a name="list-your-acs-namespaces"></a>Wyświetlanie listy przestrzeni nazw ACS

1. Połącz się z usługą ACS przy użyciu polecenia cmdlet **Connect-AcsAccount** .
  
    Może być konieczne uruchomienie programu `Set-ExecutionPolicy -ExecutionPolicy Bypass` przed wykonaniem poleceń i być administratorem tych subskrypcji, aby można było wykonać polecenia.

2. Wyświetl listę dostępnych subskrypcji platformy Azure za pomocą polecenia cmdlet **Get-AcsSubscription** .
3. Utwórz listę przestrzeni nazw ACS przy użyciu polecenia cmdlet **Get-AcsNamespace** .

### <a name="check-which-applications-will-be-impacted"></a>Sprawdź, które aplikacje mają wpływ

1. Użyj przestrzeni nazw z poprzedniego kroku i przejdź do `https://<namespace>.accesscontrol.windows.net`

    Na przykład jeśli jedna z przestrzeni nazw jest contoso-test, przejdź do `https://contoso-test.accesscontrol.windows.net`

2. W obszarze **relacje zaufania** wybierz pozycję **aplikacje jednostki uzależnionej** , aby wyświetlić listę aplikacji, na które WPŁYNIE wycofanie usługi ACS.
3. Powtórz kroki 1-2 dla wszystkich innych przestrzeni nazw ACS, które posiadasz.

## <a name="retirement-schedule"></a>Harmonogram wycofania

Od listopada 2017 wszystkie składniki Access Control są w pełni obsługiwane i operacyjne. Jedynym ograniczeniem jest to, że [nie można tworzyć nowych przestrzeni nazw Access Control za pośrednictwem klasycznego portalu Azure](https://azure.microsoft.com/blog/acs-access-control-service-namespace-creation-restriction/).

Oto harmonogram dla przestarzałych składników Access Control:

- **Listopad 2017**: środowisko administratora usługi Azure AD w klasycznym portalu Azure [zostało wycofane](https://blogs.technet.microsoft.com/enterprisemobility/2017/09/18/marching-into-the-future-of-the-azure-ad-admin-experience-retiring-the-azure-classic-portal/). W tym momencie Zarządzanie przestrzenią nazw dla Access Control jest dostępne pod nowym, dedykowanym adresem URL: `https://manage.windowsazure.com?restoreClassic=true` . Użyj tego adresu URl, aby wyświetlić istniejące przestrzenie nazw, włączyć i wyłączyć obszary nazw oraz usunąć przestrzenie nazw, jeśli wybierzesz opcję.
- **2 kwietnia 2018**: klasyczny portal Azure jest całkowicie wycofywany, co oznacza, że Access Control Zarządzanie przestrzenią nazw nie jest już dostępne za pośrednictwem żadnego adresu URL. W tym momencie nie można wyłączyć ani włączyć, usunąć ani wyliczyć przestrzeni nazw Access Control. Portal zarządzania Access Control będzie jednak w pełni funkcjonalny i znajdować się w lokalizacji `https://\<namespace\>.accesscontrol.windows.net` . Wszystkie inne składniki Access Control nadal działają normalnie.
- **7 listopada 2018**: wszystkie składniki Access Control są trwale zamknięte. Obejmuje to portal zarządzania Access Control, usługę zarządzania, STS i aparat reguły przekształcania tokenów. W tym momencie wszystkie żądania wysyłane do Access Control (znajdujące się pod adresem \<namespace\> . AccessControl.Windows.NET) kończą się niepowodzeniem. Wszystkie istniejące aplikacje i usługi powinny być migrowane do innych technologii przed tym terminem.

> [!NOTE]
> Zasada wyłącza przestrzenie nazw, które nie zażądały tokenu przez pewien czas. Począwszy od początku września 2018, ten okres czasu wynosi obecnie 14 dni braku aktywności, ale zostanie on skrócony do 7 dni braku aktywności w najbliższych tygodniach. Jeśli istnieją Access Control obszary nazw, które są obecnie wyłączone, można [pobrać i zainstalować usługę ACS PowerShell](#download-and-install-acs-powershell) , aby ponownie włączyć przestrzenie nazw.

## <a name="migration-strategies"></a>Strategie migracji

W poniższych sekcjach opisano zalecenia wysokiego poziomu dotyczące migrowania z Access Control do innych technologii firmy Microsoft.

### <a name="clients-of-microsoft-cloud-services"></a>Klienci usług w chmurze firmy Microsoft

Każda usługa w chmurze firmy Microsoft, która akceptuje tokeny wystawiane przez Access Control obsługuje teraz co najmniej jedną alternatywną formę uwierzytelniania. Prawidłowy mechanizm uwierzytelniania zależy od poszczególnych usług. Zalecamy zapoznanie się z określoną dokumentacją dla każdej usługi w celu uzyskania oficjalnych wskazówek. Dla wygody każdego zestawu dokumentacji można znaleźć tutaj:

| Usługa | Wskazówki |
| ------- | -------- |
| Azure Service Bus | [Migrowanie do sygnatur dostępu współdzielonego](../../service-bus-messaging/service-bus-migrate-acs-sas.md) |
| Przekaźnik Azure Service Bus | [Migrowanie do sygnatur dostępu współdzielonego](../../azure-relay/relay-migrate-acs-sas.md) |
| Zarządzana pamięć podręczna Azure | [Migrowanie do usługi Azure Cache for Redis](../../azure-cache-for-redis/cache-faq.md) |
| Azure DataMarket | [Migrowanie do interfejsy API usług Cognitive Services](https://azure.microsoft.com/services/cognitive-services/) |
| BizTalk Services | [Migruj do Logic Apps funkcji Azure App Service](https://azure.microsoft.com/services/cognitive-services/) |
| Azure Media Services | [Migrowanie do uwierzytelniania usługi Azure AD](https://azure.microsoft.com/blog/azure-media-service-aad-auth-and-acs-deprecation/) |
| Azure Backup | [Uaktualnij agenta Azure Backup](../../backup/backup-azure-file-folder-backup-faq.md) |

<!-- Dynamics CRM: Migrate to new SDK, Dynamics team handling privately -->
<!-- Azure RemoteApp deprecated in favor of Citrix: https://www.zdnet.com/article/microsoft-to-drop-azure-remoteapp-in-favor-of-citrix-remoting-technologies/ -->
<!-- Exchange push notifications are moving, customers don't need to move -->
<!-- Retail federation services are moving, customers don't need to move -->
<!-- Azure StorSimple: TODO -->
<!-- Azure SiteRecovery: TODO -->

### <a name="sharepoint-customers"></a>Klienci programu SharePoint

Klienci programu SharePoint 2013, 2016 i SharePoint Online mogą korzystać z usługi ACS na potrzeby uwierzytelniania w środowiskach lokalnych i hybrydowych. W przypadku niektórych funkcji i zastosowań programu SharePoint wpłynie to na wycofanie usług ACS, a inne nie. Poniższa tabela zawiera podsumowanie wskazówek dotyczących migracji dla niektórych najpopularniejszych funkcji programu SharePoint, które wykorzystują usługę ACS:

| Obiekt feature | Wskazówki |
| ------- | -------- |
| Uwierzytelnianie użytkowników z usługi Azure AD | Wcześniej usługa Azure AD nie obsługiwała tokenów SAML 1,1 wymaganych przez program SharePoint na potrzeby uwierzytelniania, a składnik ACS został użyty jako pośrednik, który udostępnił program SharePoint w formatach tokenów usługi Azure AD. Teraz możesz [połączyć program SharePoint bezpośrednio z usługą Azure AD za pomocą galerii aplikacja usługi Azure AD SharePoint w aplikacji lokalnej](../saas-apps/sharepoint-on-premises-tutorial.md). |
| [Uwierzytelnianie aplikacji & uwierzytelnianie między serwerami w programie SharePoint lokalnie](/SharePoint/security-for-sharepoint-server/authentication-overview) | Nie ma to wpływ na wycofanie usługi ACS; nie trzeba zmieniać żadnych zmian. | 
| [Niewielka autoryzacja autoryzacji dla dodatków programu SharePoint (hostowanych przez dostawcę i SharePoint)](/sharepoint/dev/sp-add-ins/three-authorization-systems-for-sharepoint-add-ins) | Nie ma to wpływ na wycofanie usługi ACS; nie trzeba zmieniać żadnych zmian. |
| [Wyszukiwanie hybrydowe w chmurze programu SharePoint](/archive/blogs/spses/cloud-hybrid-search-service-application) | Nie ma to wpływ na wycofanie usługi ACS; nie trzeba zmieniać żadnych zmian. |

### <a name="web-applications-that-use-passive-authentication"></a>Aplikacje sieci Web używające uwierzytelniania pasywnego

W przypadku aplikacji sieci Web, które używają Access Control do uwierzytelniania użytkowników, Access Control udostępnia następujące funkcje i możliwości dla deweloperów aplikacji sieci Web i architektów:

- Ścisła integracja z programem Windows Identity Foundation (WIF).
- Federacyjny z kontami Google, Facebook, Yahoo, Azure Active Directory i AD FS i kontami Microsoft.
- Obsługa następujących protokołów uwierzytelniania: uwierzytelnianie OAuth 2,0 w wersji 13, WS-Trust i federacyjnych usług sieci Web (WS-Federation).
- Obsługa następujących formatów tokenu: token sieci Web JSON (JWT), SAML 1,1, SAML 2,0 i Simple Web token (SWT).
- Środowisko odnajdowania obszaru macierzystego, zintegrowane z WIF, które umożliwia użytkownikom wybranie typu konta używanego do logowania. To środowisko jest hostowane przez aplikację sieci Web i jest w pełni dostosowywalne.
- Przekształcanie tokenów umożliwiające zaawansowane dostosowywanie oświadczeń odbieranych przez aplikację sieci Web z Access Control, w tym:
    - Przekazuj oświadczenia od dostawców tożsamości.
    - Dodawanie dodatkowych oświadczeń niestandardowych.
    - Prosta logika if-then do wystawiania oświadczeń w określonych warunkach.

Niestety, nie ma żadnej usługi oferującej wszystkie te funkcje równoważne. Należy sprawdzić, które możliwości Access Control są potrzebne, a następnie wybrać między [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/), [Azure Active Directory B2C](https://azure.microsoft.com/services/active-directory-b2c/) (Azure AD B2C) lub inną usługą uwierzytelniania w chmurze.

#### <a name="migrate-to-azure-active-directory"></a>Migruj do Azure Active Directory

Ścieżka do rozważenia polega na integrowaniu aplikacji i usług bezpośrednio z usługą Azure AD. Usługa Azure AD to dostawca tożsamości oparty na chmurze dla kont służbowych firmy Microsoft. Usługa Azure AD jest dostawcą tożsamości dla Microsoft 365, platformy Azure i wielu innych. Zapewnia ona podobne możliwości uwierzytelniania federacyjnego do Access Control, ale nie obsługuje wszystkich Access Control funkcji. 

Podstawowym przykładem jest Federacja z dostawcami tożsamości społecznościowych, takimi jak Facebook, Google i Yahoo. Jeśli użytkownicy logują się przy użyciu tych typów poświadczeń, usługa Azure AD nie jest rozwiązaniem dla Ciebie. 

Usługa Azure AD nie musi również obsługiwać dokładnie tych samych protokołów uwierzytelniania co Access Control. Na przykład chociaż zarówno Access Control, jak i usługa Azure AD obsługują uwierzytelnianie OAuth, istnieją drobne różnice między każdą implementacją. Różne implementacje wymagają modyfikacji kodu w ramach migracji.

Jednak usługa Azure AD zapewnia kilka potencjalnych korzyści Access Control klientom. Natywnie obsługuje konta służbowe firmy Microsoft hostowane w chmurze, które są powszechnie używane przez Access Control klientów. 

Dzierżawa usługi Azure AD może być również federacyjny do jednego lub większej liczby wystąpień Active Directory lokalnych za pośrednictwem AD FS. Dzięki temu aplikacja może uwierzytelniać użytkowników opartych na chmurze i użytkowników hostowanych lokalnie. Obsługuje ona również protokół WS-Federation, który utrudnia integrację z aplikacją sieci Web za pomocą WIF.

Poniższa tabela zawiera porównanie funkcji Access Control, które są istotne dla aplikacji sieci Web z tymi funkcjami, które są dostępne w usłudze Azure AD. 

Na wysokim poziomie *Azure Active Directory jest prawdopodobnie najlepszym wyborem dla migracji, Jeśli zezwolisz użytkownikom na logowanie się tylko przy użyciu kont służbowych firmy Microsoft*.

| Możliwość | Obsługa Access Control | Obsługa usługi Azure AD |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Konta służbowe lub szkolne firmy Microsoft | Obsługiwane | Obsługiwane |
| Konta z systemu Windows Server Active Directory i AD FS |-Obsługiwane za pośrednictwem federacji z dzierżawą usługi Azure AD <br />-Obsługiwane za pośrednictwem bezpośredniej Federacji z AD FS | Obsługiwane tylko za pośrednictwem federacji z dzierżawą usługi Azure AD | 
| Konta z innych systemów zarządzania tożsamościami przedsiębiorstwa |-Możliwe za pośrednictwem federacji z dzierżawą usługi Azure AD <br />-Obsługiwane za pośrednictwem Federacji bezpośredniej | Możliwe za pośrednictwem federacji z dzierżawą usługi Azure AD |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane za pośrednictwem protokołu uwierzytelniania OAuth usługi Azure AD 2.0, ale nie za pośrednictwem żadnych innych protokołów | 
| Konta w serwisie Facebook, Google, Yahoo | Obsługiwane | Nieobsługiwane |
| **Protokoły i zgodność z zestawem SDK** | | |
| WIF | Obsługiwane | Obsługiwane, ale są dostępne ograniczone instrukcje |
| WS-Federation | Obsługiwane | Obsługiwane |
| OAuth 2.0 | Obsługa wersji 3 | Obsługa specyfikacji RFC 6749, najbardziej nowoczesnej specyfikacji |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Formaty tokenów** | | |
| JWT | Obsługiwane w wersji beta | Obsługiwane |
| ELEMENT SAML 1,1 | Obsługiwane | Wersja zapoznawcza |
| SAML 2.0 | Obsługiwane | Obsługiwane |
| SWT | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Dostosowywalny interfejs użytkownika odnajdywania/wybierania obszaru głównego | Kod do pobrania, który można włączyć do aplikacji | Nieobsługiwane |
| Przekazywanie niestandardowych certyfikatów podpisywania tokenu | Obsługiwane | Obsługiwane |
| Dostosuj oświadczenia w tokenach |-Przekazuj oświadczenia wejściowe od dostawców tożsamości<br />— Uzyskiwanie tokenu dostępu od dostawcy tożsamości jako żądania<br />-Wygeneruj oświadczenia wyjściowe na podstawie wartości oświadczeń wejściowych<br />-Wygeneruj oświadczenia wyjściowe z wartościami stałymi |— Nie można przekazać oświadczeń od dostawców tożsamości federacyjnych<br />-Nie można uzyskać tokenu dostępu od dostawcy tożsamości jako żądania<br />-Nie można wydać oświadczeń wyjściowych na podstawie wartości oświadczeń wejściowych<br />-Może wydawać oświadczenia wyjściowe z wartościami stałymi<br />— Może wydawać oświadczenia wyjściowe na podstawie właściwości użytkowników zsynchronizowanych z usługą Azure AD |
| **Automatyzacja** | | |
| Automatyzowanie zadań związanych z konfiguracją i zarządzaniem | Obsługiwane przez usługę Access Control Management | Obsługiwane przy użyciu interfejsu API Microsoft Graph |

Jeśli zdecydujesz, że usługa Azure AD jest najlepszą ścieżką migracji aplikacji i usług, należy pamiętać o dwóch sposobach integracji aplikacji z usługą Azure AD.

Aby użyć WS-Federation lub WIF do integracji z usługą Azure AD, zalecamy wykonanie podejścia opisanego w temacie [Konfigurowanie federacyjnego logowania jednokrotnego dla aplikacji spoza galerii](../manage-apps/configure-saml-single-sign-on.md). Artykuł dotyczy konfigurowania usługi Azure AD na potrzeby logowania jednokrotnego opartego na protokole SAML, ale również w przypadku konfigurowania usługi WS-Federation. Poniższe podejście wymaga licencji Azure AD — wersja Premiumej. Takie podejście ma dwie zalety:

- Możesz uzyskać pełną elastyczność dostosowywania tokenów usługi Azure AD. Oświadczenia, które są wystawiane przez usługę Azure AD, można dostosować do oświadczeń wystawionych przez Access Control. Dotyczy to zwłaszcza identyfikatora użytkownika lub nazwy żądania. Aby nadal otrzymywać spójne identyfikatory użytkowników dla użytkowników po zmianie technologii, należy się upewnić, że identyfikatory użytkowników wystawione przez usługę Azure AD są zgodne z tymi wystawionymi przez Access Control.
- Można skonfigurować certyfikat podpisywania tokenu, który jest specyficzny dla Twojej aplikacji, i z okresem istnienia, który kontrolujesz.

> [!NOTE]
> To podejście wymaga licencji Azure AD — wersja Premiumej. Jeśli jesteś Access Control klientem i potrzebujesz licencji Premium na potrzeby konfigurowania logowania jednokrotnego dla aplikacji, skontaktuj się z nami. Będziemy zadowoleni z udostępnienia licencji dewelopera.

Alternatywnym podejściem jest wykonanie [tego przykładu kodu](https://github.com/Azure-Samples/active-directory-dotnet-webapp-wsfederation), który zapewnia nieco różne instrukcje dotyczące konfigurowania usługi WS-Federation. Ten przykład kodu nie używa WIF, ale raczej oprogramowania pośredniczącego ASP.NET 4,5 OWIN. Jednak instrukcje dotyczące rejestracji aplikacji są prawidłowe dla aplikacji korzystających z usługi WIF i nie wymagają licencji Azure AD — wersja Premium. 

W przypadku wybrania tej metody należy zrozumieć [Przerzucanie klucza podpisywania w usłudze Azure AD](../develop/active-directory-signing-key-rollover.md). To podejście używa globalnego klucza podpisywania usługi Azure AD do wystawiania tokenów. Domyślnie program WIF nie odświeża automatycznie kluczy podpisywania. Gdy usługa Azure AD obraca swoje globalne klucze podpisywania, wdrożenie WIF musi zostać przygotowane w celu zaakceptowania zmian. Aby uzyskać więcej informacji, zobacz [Ważne informacje dotyczące przerzucania klucza podpisywania w usłudze Azure AD](/previous-versions/azure/dn641920(v=azure.100)).

Jeśli można przeprowadzić integrację z usługą Azure AD za pomocą protokołów OpenID Connect Connect lub OAuth, zalecamy wykonanie tej czynności. Mamy obszerną dokumentację i wskazówki dotyczące sposobu integrowania usługi Azure AD z aplikacją sieci Web dostępną w [przewodniku dewelopera usługi Azure AD](../develop/index.yml).

#### <a name="migrate-to-azure-active-directory-b2c"></a>Migruj do Azure Active Directory B2C

Druga ścieżka migracji do rozważenia jest Azure AD B2C. Azure AD B2C to usługa uwierzytelniania w chmurze Access Control, która umożliwia deweloperom tworzenie źródeł uwierzytelniania i logiki zarządzania tożsamościami w usłudze w chmurze. Jest to płatna usługa (z warstwami bezpłatna i Premium), która została zaprojektowana dla aplikacji przeznaczonych dla klientów, które mogą mieć do milionów aktywnych użytkowników.

Podobnie jak Access Control, jedną z najbardziej atrakcyjnych funkcji Azure AD B2C jest obsługa wielu różnych typów kont. Korzystając z Azure AD B2C, możesz logować użytkowników przy użyciu ich konto Microsoft, usługi Facebook, Google, LinkedIn, GitHub lub Yahoo i nie tylko. Azure AD B2C obsługuje także "konta lokalne", nazwy użytkownika i hasła, które użytkownicy tworzą dla aplikacji. Azure AD B2C zapewnia także rozbudowane rozszerzalności, których można użyć do dostosowania przepływów logowania. 

Jednak Azure AD B2C nie obsługuje szerokiego zakresu protokołów uwierzytelniania i formatów tokenów, które mogą być wymagane przez Access Control klientów. Nie można również używać Azure AD B2C do uzyskiwania tokenów i zapytań w celu uzyskania dodatkowych informacji o użytkowniku od dostawcy tożsamości, firmy Microsoft lub innych.

Poniższa tabela zawiera porównanie funkcji Access Control, które są istotne dla aplikacji sieci Web, które są dostępne w Azure AD B2C. Na wysokim poziomie *Azure AD B2C jest prawdopodobnie właściwy wybór dla migracji, jeśli Twoja aplikacja jest połączona z klientem lub obsługuje wiele różnych typów kont.*

| Możliwość | Obsługa Access Control | Obsługa Azure AD B2C |
| ---------- | ----------- | ---------------- |
| **Typy kont** | | |
| Konta służbowe lub szkolne firmy Microsoft | Obsługiwane | Obsługiwane przez zasady niestandardowe  |
| Konta z systemu Windows Server Active Directory i AD FS | Obsługiwane za pośrednictwem bezpośredniej Federacji z AD FS | Obsługiwane za pośrednictwem Federacji SAML przy użyciu zasad niestandardowych |
| Konta z innych systemów zarządzania tożsamościami przedsiębiorstwa | Obsługiwane za pośrednictwem bezpośredniej Federacji za pośrednictwem WS-Federation | Obsługiwane za pośrednictwem Federacji SAML przy użyciu zasad niestandardowych |
| Konta Microsoft do użytku osobistego | Obsługiwane | Obsługiwane | 
| Konta w serwisie Facebook, Google, Yahoo | Obsługiwane | Serwis Facebook i Google obsługują natywnie usługi Yahoo obsługiwane za pośrednictwem Federacji OpenID Connect Connect przy użyciu zasad niestandardowych |
| **Protokoły i zgodność z zestawem SDK** | | |
| Windows Identity Foundation (WIF) | Obsługiwane | Nieobsługiwane |
| WS-Federation | Obsługiwane | Nieobsługiwane |
| OAuth 2.0 | Obsługa wersji 3 | Obsługa specyfikacji RFC 6749, najbardziej nowoczesnej specyfikacji |
| WS-Trust | Obsługiwane | Nieobsługiwane |
| **Formaty tokenów** | | |
| JWT | Obsługiwane w wersji beta | Obsługiwane |
| ELEMENT SAML 1,1 | Obsługiwane | Nieobsługiwane |
| SAML 2.0 | Obsługiwane | Nieobsługiwane |
| SWT | Obsługiwane | Nieobsługiwane |
| **Dostosowania** | | |
| Dostosowywalny interfejs użytkownika odnajdywania/wybierania obszaru głównego | Kod do pobrania, który można włączyć do aplikacji | W pełni dostosowywalny interfejs użytkownika za pośrednictwem niestandardowego arkusza CSS |
| Przekazywanie niestandardowych certyfikatów podpisywania tokenu | Obsługiwane | Niestandardowe klucze podpisywania, nie certyfikaty, obsługiwane za pośrednictwem zasad niestandardowych |
| Dostosuj oświadczenia w tokenach |-Przekazuj oświadczenia wejściowe od dostawców tożsamości<br />— Uzyskiwanie tokenu dostępu od dostawcy tożsamości jako żądania<br />-Wygeneruj oświadczenia wyjściowe na podstawie wartości oświadczeń wejściowych<br />-Wygeneruj oświadczenia wyjściowe z wartościami stałymi |-Może przechodzić przez oświadczenia od dostawców tożsamości; zasady niestandardowe wymagane dla niektórych oświadczeń<br />-Nie można uzyskać tokenu dostępu od dostawcy tożsamości jako żądania<br />-Może wydawać oświadczenia wyjściowe na podstawie wartości oświadczeń wejściowych za pośrednictwem zasad niestandardowych<br />-Może wydawać oświadczenia wyjściowe z wartościami stałymi za pośrednictwem zasad niestandardowych |
| **Automatyzacja** | | |
| Automatyzowanie zadań związanych z konfiguracją i zarządzaniem | Obsługiwane przez usługę Access Control Management |-Tworzenie użytkowników dozwolonych za pomocą interfejsu API Microsoft Graph<br />— Nie można programowo tworzyć dzierżawców B2C, aplikacji ani zasad |

Jeśli zdecydujesz, że Azure AD B2C jest najlepszą ścieżką migracji dla aplikacji i usług, Zacznij od następujących zasobów:

- [Dokumentacja Azure AD B2C](../../active-directory-b2c/overview.md)
- [Zasady niestandardowe usługi Azure AD B2C](../../active-directory-b2c/custom-policy-overview.md)
- [Cennik Azure AD B2C](https://azure.microsoft.com/pricing/details/active-directory-b2c/)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrowanie do usługi ping Identity lub rozwiązanie Auth0

W niektórych przypadkach może się okazać, że usługa Azure AD i Azure AD B2C nie są wystarczające do zastępowania Access Control w aplikacjach sieci Web bez konieczności wprowadzania istotnych zmian w kodzie. Niektóre typowe przykłady mogą obejmować:

- Aplikacje sieci Web, które używają WIF lub WS-Federation do logowania się przy użyciu dostawców tożsamości społecznościowych, takich jak Google lub Facebook.
- Aplikacje sieci Web, które wykonują bezpośrednie Federacji z dostawcą tożsamości przedsiębiorstwa za pośrednictwem protokołu WS-Federation.
- Aplikacje sieci Web, które wymagają tokenu dostępu wystawionego przez dostawcę tożsamości społecznościowej (np. Google lub Facebook) jako roszczeń w tokenach wystawionych przez Access Control.
- Aplikacje sieci Web ze złożonymi regułami transformacji tokenów, których usługa Azure AD lub Azure AD B2C nie może odtworzyć.
- Wielodostępne aplikacje sieci Web, które używają usług ACS do scentralizowanego zarządzania federacyjnymi z wieloma różnymi dostawcami tożsamości

W takich przypadkach warto rozważyć Migrowanie aplikacji sieci Web do innej usługi uwierzytelniania w chmurze. Zalecamy Eksplorowanie następujących opcji. Każda z poniższych opcji oferuje funkcje podobne do Access Control:

![Ten obraz pokazuje logo rozwiązanie Auth0](./media/active-directory-acs-migration/rsz-auth0.png) 

[Rozwiązanie Auth0](https://auth0.com/acs) to elastyczna usługa tożsamości w chmurze, która stworzyła [wskazówki dotyczące migracji wysokiego poziomu dla klientów Access Control](https://auth0.com/acs)i obsługuje niemal każdą funkcję, którą robi Usługa ACS.

![Ten obraz pokazuje logo Identity ping](./media/active-directory-acs-migration/rsz-ping.png)

[Polecenie ping Identity](https://www.pingidentity.com) oferuje dwa rozwiązania podobne do usługi ACS. PingOne to usługa tożsamości w chmurze, która obsługuje wiele takich samych funkcji, jak ACS, a serwera pingfederate jest podobnym produktem tożsamości, który oferuje większą elastyczność. Aby uzyskać więcej informacji na temat korzystania z tych produktów, zobacz [wskazówki dotyczące wycofania usługi ACS](https://www.pingidentity.com/en/company/blog/posts/2017/migrating-from-microsoft-acs-to-ping-identity.html) dla usługi ping.

Naszym celem w pracy z usługą ping Identity i rozwiązanie Auth0 jest upewnienie się, że wszyscy klienci Access Control mają ścieżkę migracji dla swoich aplikacji i usług, które minimalizują ilość pracy wymaganej do przeniesienia z Access Control.

<!--

## SharePoint 2010, 2013, 2016

TODO: Azure AD only, use Azure AD SAML 1.1 tokens, when we bring it back online.
Other IDPs: use Auth0? https://auth0.com/docs/integrations/sharepoint.

-->

### <a name="web-services-that-use-active-authentication"></a>Usługi sieci Web używające uwierzytelniania aktywnego

W przypadku usług sieci Web zabezpieczonych za pomocą tokenów wystawionych przez Access Control Access Control oferuje następujące funkcje i możliwości:

- Możliwość rejestrowania co najmniej jednej *tożsamości usługi* w przestrzeni nazw Access Control. Tożsamości usługi mogą służyć do żądania tokenów.
- Obsługa obsługi protokołu OAuth i 3-lub 2,0ych protokołów uwierzytelniania OAuth w celu żądania tokenów przy użyciu następujących typów poświadczeń:
    - Proste hasło, które zostało utworzone dla tożsamości usługi
    - Podpisany SWT przy użyciu klucza symetrycznego lub certyfikatu x509
    - Token SAML wystawiony przez zaufanego dostawcę tożsamości (zwykle wystąpienie AD FS)
- Obsługa następujących formatów tokenów: JWT, SAML 1,1, SAML 2,0 i SWT.
- Proste reguły przekształcania tokenów.

Tożsamości usług w Access Control są zwykle używane do implementowania uwierzytelniania serwer-serwer. 

#### <a name="migrate-to-azure-active-directory"></a>Migruj do Azure Active Directory

Nasze rekomendacje dotyczące tego typu przepływu uwierzytelniania są migrowane do [Azure Active Directory](https://azure.microsoft.com/develop/identity/signin/). Usługa Azure AD to dostawca tożsamości oparty na chmurze dla kont służbowych firmy Microsoft. Usługa Azure AD jest dostawcą tożsamości dla Microsoft 365, platformy Azure i wielu innych. 

Możesz również użyć usługi Azure AD do uwierzytelniania serwer-serwer przy użyciu wdrożenia usługi Azure AD w celu przyznania poświadczeń klienta OAuth. W poniższej tabeli porównano możliwości Access Control w ramach uwierzytelniania serwer-serwer z tymi, które są dostępne w usłudze Azure AD.

| Możliwość | Obsługa Access Control | Obsługa usługi Azure AD |
| ---------- | ----------- | ---------------- |
| Jak zarejestrować usługę sieci Web | Tworzenie jednostki uzależnionej w portalu zarządzania Access Control | Tworzenie aplikacji sieci Web usługi Azure AD w Azure Portal |
| Jak zarejestrować klienta | Tworzenie tożsamości usługi w portalu zarządzania Access Control | Utwórz kolejną aplikację sieci Web usługi Azure AD w Azure Portal |
| Używany protokół |-Protokół ZAWIJAnia OAuth<br />-OAuth 2,0, wersja 13. przyznanie poświadczeń klienta | Przyznawanie poświadczeń klienta OAuth 2.0 |
| Metody uwierzytelniania klientów |-Proste hasło<br />-Podpisane SWT<br />-Token SAML od dostawcy tożsamości federacyjnych |-Proste hasło<br />-Z podpisem JWT |
| Formaty tokenów |-JWT<br />-SAML 1,1<br />-SAML 2,0<br />-SWT<br /> | Tylko JWT |
| Przekształcenie tokenu |-Dodawanie oświadczeń niestandardowych<br />-Prosta logika wystawiania | Dodawanie oświadczeń niestandardowych | 
| Automatyzowanie zadań związanych z konfiguracją i zarządzaniem | Obsługiwane przez usługę Access Control Management | Obsługiwane przy użyciu interfejsu API Microsoft Graph |

Wskazówki dotyczące implementowania scenariuszy serwer-serwer można znaleźć w następujących zasobach:

- Sekcja Service to Service [przewodnika dewelopera usługi Azure AD](../develop/index.yml)
- [Przykład kodu demona przy użyciu prostych poświadczeń klienta hasła](https://github.com/Azure-Samples/active-directory-dotnet-daemon)
- [Przykład kodu demona przy użyciu poświadczeń klienta certyfikatu](https://github.com/Azure-Samples/active-directory-dotnet-daemon-certificate-credential)

#### <a name="migrate-to-ping-identity-or-auth0"></a>Migrowanie do usługi ping Identity lub rozwiązanie Auth0

W niektórych przypadkach może się okazać, że poświadczenia klienta usługi Azure AD i implementacja dotacji OAuth nie są wystarczające, aby zastąpić Access Control w architekturze bez konieczności wprowadzania poważnych zmian w kodzie. Niektóre typowe przykłady mogą obejmować:

- Uwierzytelnianie serwer-serwer przy użyciu formatów tokenów innych niż JWTs.
- Uwierzytelnianie serwer-serwer przy użyciu tokenu wejściowego dostarczonego przez zewnętrznego dostawcę tożsamości.
- Uwierzytelnianie między serwerami i regułami transformacji tokenów, których usługa Azure AD nie może odtworzyć.

W takich przypadkach można rozważyć Migrowanie aplikacji sieci Web do innej usługi uwierzytelniania w chmurze. Zalecamy Eksplorowanie następujących opcji. Każda z poniższych opcji oferuje funkcje podobne do Access Control:

![Ten obraz pokazuje logo rozwiązanie Auth0](./media/active-directory-acs-migration/rsz-auth0.png)

[Rozwiązanie Auth0](https://auth0.com/acs) to elastyczna usługa tożsamości w chmurze, która stworzyła [wskazówki dotyczące migracji wysokiego poziomu dla klientów Access Control](https://auth0.com/acs)i obsługuje niemal każdą funkcję, którą robi Usługa ACS.

![Ten obraz przedstawia polecenie ping Identity logo ](./media/active-directory-acs-migration/rsz-ping.png)
 [ping](https://www.pingidentity.com) , które oferuje dwa rozwiązania podobne do usługi ACS. PingOne to usługa tożsamości w chmurze, która obsługuje wiele takich samych funkcji, jak ACS, a serwera pingfederate jest podobnym produktem tożsamości, który oferuje większą elastyczność. Aby uzyskać więcej informacji na temat korzystania z tych produktów, zobacz [wskazówki dotyczące wycofania usługi ACS](https://www.pingidentity.com/en/company/blog/posts/2017/migrating-from-microsoft-acs-to-ping-identity.html) dla usługi ping.

Naszym celem w pracy z usługą ping Identity i rozwiązanie Auth0 jest upewnienie się, że wszyscy klienci Access Control mają ścieżkę migracji dla swoich aplikacji i usług, które minimalizują ilość pracy wymaganej do przeniesienia z Access Control.

#### <a name="passthrough-authentication"></a>Przekazywanie uwierzytelniania

W przypadku przekazywania uwierzytelniania z dowolnymi przekształceniami tokenu nie istnieje równoważna technologia firmy Microsoft dla usług ACS. Jeśli jest to wymagane przez klientów, rozwiązanie Auth0 może być tym, kto oferuje najbliższe rozwiązanie do zbliżania.

## <a name="questions-concerns-and-feedback"></a>Pytania, uwagi i opinie

Firma Microsoft zdaje sobie sprawę, że wielu Access Control klienci nie będą mogli znaleźć pustej ścieżki migracji po przeczytaniu tego artykułu. W celu określenia właściwego planu może być potrzebna pomoc lub wskazówki. Jeśli chcesz omówić scenariusze migracji i pytania, pozostaw komentarz na tej stronie.