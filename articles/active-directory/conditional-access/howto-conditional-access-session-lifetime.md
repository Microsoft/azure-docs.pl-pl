---
title: Konfigurowanie zarządzania sesjami uwierzytelniania — Azure Active Directory
description: Dostosuj konfigurację sesji uwierzytelniania usługi Azure AD, w tym częstotliwość logowania użytkownika i trwałość sesji przeglądarki.
services: active-directory
ms.service: active-directory
ms.subservice: conditional-access
ms.topic: how-to
ms.date: 10/23/2020
ms.author: joflore
author: MicrosoftGuyJFlo
manager: daveba
ms.reviewer: jlu, calebb
ms.collection: M365-identity-device-management
ms.openlocfilehash: 6116ab543d6dfc886e44206c2a60e4456b39fbc9
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102558189"
---
# <a name="configure-authentication-session-management-with-conditional-access"></a>Konfigurowanie zarządzania sesjami uwierzytelniania przy użyciu dostępu warunkowego

W złożonych wdrożeniach organizacje mogą wymagać ograniczenia sesji uwierzytelniania. Niektóre scenariusze mogą obejmować:

* Dostęp do zasobów z urządzenia niezarządzanego lub udostępnionego
* Dostęp do poufnych informacji z sieci zewnętrznej
* Użytkownicy o dużym wpływie
* Krytyczne aplikacje biznesowe

Kontrole dostępu warunkowego umożliwiają tworzenie zasad przeznaczonych dla konkretnych przypadków użycia w organizacji bez wywierania wpływu na wszystkich użytkowników.

Przed wprowadzeniem szczegółowych informacji na temat sposobu konfigurowania zasad Sprawdźmy konfigurację domyślną.

## <a name="user-sign-in-frequency"></a>Częstotliwość logowania użytkownika

Częstotliwość logowania określa czas, po którym użytkownik zostanie poproszony o ponowne zalogowanie przy próbie uzyskania dostępu do zasobu.

Domyślna konfiguracja Azure Active Directory (Azure AD) dla częstotliwości logowania użytkownika jest oknem kroczącym 90 dni. Monitowanie użytkowników o poświadczenia często wygląda na to, że jest to rozsądne działanie, ale może się to wiązać z atakami: Użytkownicy, którzy są przeszkoleni, aby wprowadzić swoje poświadczenia bez względu na to, że mogą przypadkowo dostarczyć je do złośliwego monitu o poświadczenia.

Może to spowodować, że dźwięk nie zostanie poproszony o ponowne zalogowanie się użytkownika, w rzeczywistości wszystkie zasady IT odwołują sesję. Niektóre przykłady obejmują (ale nie ograniczone do) zmianę hasła, niezgodne urządzenie lub wyłączone konto. Możesz również jawnie [odwołać sesje użytkowników przy użyciu programu PowerShell](/powershell/module/azuread/revoke-azureaduserallrefreshtoken). Domyślna konfiguracja usługi Azure AD to "nie Monituj użytkowników o podanie swoich poświadczeń, jeśli zabezpieczenia stan ich sesji nie zostały zmienione".

Ustawienie częstotliwości logowania działa z aplikacjami, które mają wdrożone protokoły OAUTH2 lub OIDC zgodnie ze standardami. Większość aplikacji natywnych firmy Microsoft dla systemów Windows, Mac i Mobile, w tym z uwzględnieniem następujących aplikacji sieci Web, jest zgodnych z tym ustawieniem.

- Word, Excel, PowerPoint Online
- OneNote online
- Office.com
- Portal administracyjny Microsoft 365
- Exchange Online
- Program SharePoint i usługa OneDrive
- Klient sieci Web dla zespołów
- Dynamics CRM Online
- Azure Portal

Ustawienie częstotliwości logowania działa również z aplikacjami SAML, o ile nie porzucają własnych plików cookie i są przekierowywane z powrotem do usługi Azure AD w celu regularnego uwierzytelniania.

### <a name="user-sign-in-frequency-and-multi-factor-authentication"></a>Częstotliwość logowania użytkownika i uwierzytelnianie wieloskładnikowe

Częstotliwość logowania została wcześniej zastosowana tylko do uwierzytelniania pierwszego czynnika na urządzeniach, które zostały dołączone do usługi Azure AD, dołączona hybrydowa usługa Azure AD i zarejestrowana usługa Azure AD. W przypadku naszych klientów nie ma łatwego sposobu na ponowne Wymuszanie uwierzytelniania wieloskładnikowego (MFA) na tych urządzeniach. W oparciu o opinie klientów, częstotliwość logowania będzie również dotyczyć usługi MFA.

[![Częstotliwość logowania i uwierzytelnianie wieloskładnikowe](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart-small.png)](media/howto-conditional-access-session-lifetime/conditional-access-flow-chart.png#lightbox)

### <a name="user-sign-in-frequency-and-device-identities"></a>Częstotliwość logowania użytkownika i tożsamości urządzeń

Jeśli masz przyłączoną usługę Azure AD, dołączono hybrydową usługę Azure AD lub zarejestrowane urządzenia usługi Azure AD, gdy użytkownik odblokowuje swoje urządzenie lub loguje się interaktywnie, to zdarzenie będzie również spełniało zasady częstotliwości logowania. W poniższych dwóch przykładach częstotliwość logowania użytkownika jest ustawiona na 1 godzinę:

Przykład 1:

- W dniu 00:00 użytkownik loguje się do urządzenia dołączonego do usługi Azure AD systemu Windows 10 i zaczyna pracę nad dokumentem przechowywanym w usłudze SharePoint Online.
- Użytkownik kontynuuje pracę nad tym samym dokumentem na swoim urządzeniu przez godzinę.
- W dniu 01:00 użytkownik jest monitowany o ponowne zalogowanie się na podstawie wymagań dotyczących częstotliwości logowania w zasadach dostępu warunkowego skonfigurowanych przez administratora.

Przykład 2:

- W dniu 00:00 użytkownik loguje się do urządzenia dołączonego do usługi Azure AD systemu Windows 10 i zaczyna pracę nad dokumentem przechowywanym w usłudze SharePoint Online.
- O godzinie 00:30 użytkownik otrzymuje i zablokuje zablokowanie urządzenia.
- O 00:45 użytkownik wraca z ich przerw i odblokowuje urządzenie.
- W dniu 01:45 użytkownik jest monitowany o ponowne zalogowanie się na podstawie wymagań dotyczących częstotliwości logowania w zasadach dostępu warunkowego skonfigurowanych przez ich administratora od momentu ostatniego zalogowania się o 00:45.

## <a name="persistence-of-browsing-sessions"></a>Trwałość sesji przeglądania

W trwałej sesji przeglądarki użytkownicy mogą pozostać zalogowani po zamknięciu i ponownym otwarciu okna przeglądarki.

Wartość domyślna usługi Azure AD na potrzeby trwałości sesji przeglądarki umożliwia użytkownikom na urządzeniach osobistych wybieranie, czy sesja ma być utrwalana, pokazując "pozostania w rejestracji". Monituj po pomyślnym uwierzytelnieniu. Jeśli trwałość przeglądarki jest skonfigurowana w AD FS przy użyciu wskazówek zawartych w artykule [AD FS pojedyncze ustawienia Sign-On](/windows-server/identity/ad-fs/operations/ad-fs-single-sign-on-settings#enable-psso-for-office-365-users-to-access-sharepoint-online
), firma Microsoft będzie przestrzegać tych zasad i utrzymywać również sesję usługi Azure AD. Możesz również określić, czy użytkownicy w dzierżawie zobaczą "pozostania w Twojej rejestracji"? Monituj o zmianę odpowiedniego ustawienia w okienku znakowanie firmowe w Azure Portal przy użyciu wskazówek w artykule [Dostosowywanie strony logowania usługi Azure AD](../fundamentals/customize-branding.md).

## <a name="configuring-authentication-session-controls"></a>Konfigurowanie kontrolek sesji uwierzytelniania

Dostęp warunkowy jest możliwością Azure AD — wersja Premium i wymaga licencji Premium. Jeśli chcesz dowiedzieć się więcej na temat dostępu warunkowego, zobacz [co to jest dostęp warunkowy w Azure Active Directory?](overview.md#license-requirements)

> [!WARNING]
> Jeśli używasz funkcji [okresowego istnienia tokenu](../develop/active-directory-configurable-token-lifetimes.md) , który jest obecnie w publicznej wersji zapoznawczej, pamiętaj, że nie obsługujemy tworzenia dwóch różnych zasad dla tej samej kombinacji użytkownika lub aplikacji: jeden z tą funkcją i inną z konfigurowalną funkcją okresu istnienia tokenu. Firma Microsoft wycofał konfigurowalną funkcję okresu istnienia tokenu dla odświeżania i okresu istnienia tokenu sesji w dniu 30 stycznia 2021 i zastąpiła przy użyciu funkcji zarządzania sesjami uwierzytelniania dostępu warunkowego.  
>
> Przed włączeniem częstotliwości logowania upewnij się, że inne ustawienia ponownego uwierzytelniania są wyłączone w dzierżawie. Jeśli jest włączona funkcja "Pamiętaj MFA na zaufanych urządzeniach", pamiętaj, aby ją wyłączyć przed użyciem częstotliwości logowania, ponieważ te dwa ustawienia mogą prowadzić do nieoczekiwanego monitowania użytkowników. Aby dowiedzieć się więcej o ponownym uwierzytelnianiu i okresach istnienia sesji, zobacz artykuł, [Optymalizacja ponownych prób uwierzytelniania i informacje o okresie istnienia sesji dla Multi-Factor Authentication usługi Azure AD](../authentication/concepts-azure-multi-factor-authentication-prompts-session-lifetime.md).

### <a name="policy-1-sign-in-frequency-control"></a>Zasady 1: kontrola częstotliwości logowania

1. Utwórz nowe zasady
1. Wybierz wszystkie wymagane warunki dla środowiska klienta, w tym docelowe aplikacje w chmurze.

   > [!NOTE]
   > Zaleca się skonfigurowanie równej częstotliwości monitów uwierzytelniania dla Microsoft Office najważniejszych aplikacji, takich jak Exchange Online i SharePoint Online, w celu zapewnienia najlepszego środowiska użytkownika.

1. Przejdź do **sesji kontroli dostępu**  >   , a następnie kliknij pozycję **częstotliwość logowania**
1. Wprowadź wymaganą wartość dni i godziny w pierwszym polu tekstowym
1. Wybierz wartość **godzin** lub **dni** z listy rozwijanej
1. Zapisywanie zasad

![Zasady dostępu warunkowego skonfigurowane dla częstotliwości logowania](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-sign-in-frequency.png)

Na zarejestrowanych urządzeniach z systemem Windows w usłudze Azure AD Zaloguj się na urządzeniu jako monit. Jeśli na przykład skonfigurowano częstotliwość logowania na 24 godziny dla aplikacji pakietu Office, użytkownicy usługi Azure AD zarejestrowane urządzenia z systemem Windows będą spełniać zasady częstotliwości logowania, logując się na urządzeniu i nie będą monitowani ponownie podczas otwierania aplikacji pakietu Office.

### <a name="policy-2-persistent-browser-session"></a>Zasady 2: nietrwała sesja przeglądarki

1. Utwórz nowe zasady
1. Wybierz wszystkie wymagane warunki.

   > [!NOTE]
   > Należy pamiętać, że ta kontrolka wymaga wybrania opcji "wszystkie aplikacje w chmurze" jako warunek. Trwałość sesji przeglądarki jest kontrolowana przez token sesji uwierzytelniania. Wszystkie karty w sesji przeglądarki współdzielą jeden token sesji i w związku z tym wszystkie muszą mieć stan trwałości.

1. Przejdź do **sesji kontroli dostępu**  >   , a następnie kliknij pozycję **trwała sesja przeglądarki**
1. Wybierz wartość z listy rozwijanej
1. Zapisz zasady

![Zasady dostępu warunkowego skonfigurowane dla trwałej przeglądarki](media/howto-conditional-access-session-lifetime/conditional-access-policy-session-persistent-browser.png)

> [!NOTE]
> Trwała konfiguracja sesji przeglądarki w usłudze Azure AD dostęp warunkowy spowoduje zastąpienie "pozostać zalogowanym?" ustawienie w okienku znakowanie firmowe w Azure Portal dla tego samego użytkownika, jeśli skonfigurowano obie zasady.

## <a name="validation"></a>Walidacja

Użyj narzędzia What-If, aby symulować Logowanie użytkownika do aplikacji docelowej i innych warunków w zależności od sposobu skonfigurowania zasad. Kontrolki zarządzanie sesjami uwierzytelniania są wyświetlane w wyniku tego narzędzia.

![Wyniki narzędzia What If dostępu warunkowego](media/howto-conditional-access-session-lifetime/conditional-access-what-if-tool-result.png)

## <a name="policy-deployment"></a>Wdrażanie zasad

Aby upewnić się, że zasady działają zgodnie z oczekiwaniami, zalecanym najlepszym rozwiązaniem jest przetestowanie go przed wycofaniem do produkcji. Najlepiej użyć dzierżawy testowej, aby sprawdzić, czy nowe zasady działają zgodnie z oczekiwaniami. Aby uzyskać więcej informacji, zapoznaj się z artykułem [Planowanie wdrożenia dostępu warunkowego](plan-conditional-access.md).

## <a name="next-steps"></a>Następne kroki

* Aby skonfigurować zasady dostępu warunkowego dla środowiska, zapoznaj się z artykułem [Planowanie wdrożenia dostępu warunkowego](plan-conditional-access.md).