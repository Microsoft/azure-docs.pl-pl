---
title: Konfigurowalne okresy istnienia tokenu
titleSuffix: Microsoft identity platform
description: Dowiedz się, jak ustawić okresy istnienia dla dostępu, języka SAML i tokenów identyfikatorów wystawionych przez platformę tożsamości firmy Microsoft.
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.workload: identity
ms.topic: conceptual
ms.date: 04/08/2021
ms.author: ryanwi
ms.custom: aaddev, identityplatformtop40, content-perf, FY21Q1, contperf-fy21q1
ms.reviewer: hirsin, jlu, annaba
ms.openlocfilehash: e1753391c7b61b6e9bd9e6ac0d142b4ee94502d8
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107363975"
---
# <a name="configurable-token-lifetimes-in-the-microsoft-identity-platform-preview"></a>Konfigurowalne okresy istnienia tokenów na platformie tożsamości firmy Microsoft (wersja zapoznawcza)

Możesz określić okres istnienia dostępu, identyfikatora lub tokenu SAML wystawionego przez platformę tożsamości firmy Microsoft. Okresy istnienia tokenów można ustawić dla wszystkich aplikacji w organizacji, dla aplikacji wielodostępnych (dla wielu organizacji) lub dla określonej jednostki usługi w organizacji. Obecnie nie obsługujemy jednak konfigurowania okresów istnienia tokenu dla jednostki usługi tożsamości [zarządzanej.](../managed-identities-azure-resources/overview.md)

W usłudze Azure AD obiekt zasad reprezentuje zestaw reguł, które są wymuszane dla poszczególnych aplikacji lub wszystkich aplikacji w organizacji. Każdy typ zasad ma unikatową strukturę z zestawem właściwości, które są stosowane do obiektów, do których są przypisane.

Zasady można wyznaczyć jako zasady domyślne dla organizacji. Zasady są stosowane do każdej aplikacji w organizacji, o ile nie zostaną zastąpione przez zasady o wyższym priorytecie. Można również przypisać zasady do określonych aplikacji. Kolejność priorytetów zależy od typu zasad.

Przykłady można znaleźć [w przykładach konfigurowania okresów istnienia tokenu.](configure-token-lifetimes.md)

> [!NOTE]
> Zasady okresu istnienia tokenu konfigurowalnego mają zastosowanie tylko do klientów mobilnych i klasycznych, którzy mają dostęp do usługi SharePoint Online OneDrive dla Firm zasobów i nie mają zastosowania do sesji przeglądarki internetowej.
> Aby zarządzać okresem istnienia sesji przeglądarki internetowej dla usługi SharePoint Online OneDrive dla Firm, użyj funkcji okresu [istnienia sesji dostępu warunkowego.](../conditional-access/howto-conditional-access-session-lifetime.md) Zapoznaj się z [blogiem usługi SharePoint Online,](https://techcommunity.microsoft.com/t5/SharePoint-Blog/Introducing-Idle-Session-Timeout-in-SharePoint-and-OneDrive/ba-p/119208) aby dowiedzieć się więcej na temat konfigurowania limitów czasu bezczynności sesji.

## <a name="license-requirements"></a>Wymagania licencyjne

Korzystanie z tej funkcji wymaga licencji Azure AD — wersja Premium P1. Aby znaleźć odpowiednią licencję dla swoich wymagań, zobacz Porównanie ogólnie dostępnych funkcji w wersjach [Bezpłatna i Premium.](https://azure.microsoft.com/pricing/details/active-directory/)

Klienci z [Microsoft 365 Business mają](/office365/servicedescriptions/microsoft-365-service-descriptions/microsoft-365-business-service-description) również dostęp do funkcji dostępu warunkowego.

## <a name="token-lifetime-policies-for-access-saml-and-id-tokens"></a>Zasady okresu istnienia tokenów dotyczące dostępu, saml i tokenów identyfikatorów

Można ustawić zasady okresu istnienia tokenów dla tokenów dostępu, tokenów SAML i tokenów identyfikatorów.

### <a name="access-tokens"></a>Tokeny dostępu

Klienci używają tokenów dostępu do uzyskiwania dostępu do chronionego zasobu. Token dostępu może być używany tylko dla określonej kombinacji użytkownika, klienta i zasobu. Tokenów dostępu nie można odwołać i są ważne do czasu ich wygaśnięcia. Złośliwy aktor, który uzyskał token dostępu, może używać go przez cały okres istnienia. Dostosowanie okresu istnienia tokenu dostępu to różnica między zwiększeniem wydajności systemu i zwiększeniem czasu, przez który klient zachowuje dostęp po wyłączeniu konta użytkownika. Zwiększona wydajność systemu jest osiągana przez zmniejszenie liczby razy, gdy klient musi uzyskać nowy token dostępu.  Wartość domyślna różni się w zależności od aplikacji klienckiej żądającej tokenu. Na przykład klienci z możliwością ciągłej oceny dostępu (CAE, continuous access evaluation), którzy negocjują sesje z świadomość cae, zobaczą długi okres istnienia tokenu (do 28 godzin). Po wygaśnięciu tokenu klient musi użyć tokenu odświeżania, aby (zazwyczaj w trybie dyskretnym) uzyskać nowy token odświeżania i token dostępu.

### <a name="saml-tokens"></a>Tokeny języka SAML

Tokeny SAML są używane przez wiele internetowych aplikacji SaaS i są uzyskiwane przy użyciu Azure Active Directory protokołu SAML2 firmy . Są one również używane przez aplikacje korzystające z usługi WS-Federation. Domyślny okres istnienia tokenu to 1 godzina. Z perspektywy aplikacji okres ważności tokenu jest określony przez wartość NotOnOrAfter `<conditions …>` elementu w tokenie. Po zakończeniu okresu ważności tokenu klient musi zainicjować nowe żądanie uwierzytelniania, które często będzie spełnione bez logowania interakcyjnego w wyniku tokenu sesji logowania jednokrotnego.

Wartość NotOnOrAfter można zmienić przy użyciu `AccessTokenLifetime` parametru w parametrze `TokenLifetimePolicy` . Zostanie ona ustawiona na okres istnienia skonfigurowany w zasadach, jeśli taki jest, oraz współczynnik nieskonfigurowany zegara o pięciu minutach.

Konfiguracja okresu istnienia tokenu nie ma wpływu na potwierdzenie podmiotu NotOnOrAfter określone w `<SubjectConfirmationData>` elemencie. 

### <a name="id-tokens"></a>Tokeny identyfikatorów

Tokeny identyfikatorów są przekazywane do witryn internetowych i klientów natywnych. Tokeny identyfikatorów zawierają informacje o profilu użytkownika. Token identyfikatora jest powiązany z określoną kombinacją użytkownika i klienta. Tokeny identyfikatorów są uznawane za ważne do momentu ich wygaśnięcia. Zazwyczaj aplikacja internetowa dopasowuje okres istnienia sesji użytkownika w aplikacji do okresu istnienia tokenu identyfikatora wystawionego dla użytkownika. Okres istnienia tokenu identyfikatora można dostosować, aby kontrolować, jak często aplikacja internetowa wygasa w sesji aplikacji oraz jak często użytkownik musi być ponownie uwierzytelniany za pomocą platformy tożsamości firmy Microsoft (w trybie dyskretnym lub interaktywnym).

## <a name="token-lifetime-policies-for-refresh-tokens-and-session-tokens"></a>Zasady okresu istnienia tokenów dla tokenów odświeżania i tokenów sesji

Nie można ustawić zasad okresu istnienia tokenów dla tokenów odświeżania i tokenów sesji.

> [!IMPORTANT]
> Od 30 stycznia 2021 r. nie można konfigurować okresów istnienia tokenów odświeżania i sesji. Azure Active Directory już nie honoruje konfiguracji odświeżania i tokenu sesji w istniejących zasadach.  Nowe tokeny wystawione po wygaśnięciu istniejących tokenów są teraz ustawione na [konfigurację domyślną](#configurable-token-lifetime-properties). Po zakończeniu odświeżania i wycofania konfiguracji tokenu sesji można nadal konfigurować okresy istnienia tokenów dostępu, saml i identyfikatorów.
>
> Okres istnienia istniejącego tokenu nie zostanie zmieniony. Po wygaśnięciu nowy token zostanie wystawiony na podstawie wartości domyślnej.
>
> Jeśli musisz nadal definiować okres, po którym użytkownik zostanie poproszony o zalogowanie się ponownie, skonfiguruj częstotliwość logowania w dostępie warunkowym. Aby dowiedzieć się więcej na temat dostępu warunkowego, przeczytaj [konfigurowanie zarządzania sesją uwierzytelniania przy użyciu dostępu warunkowego.](../conditional-access/howto-conditional-access-session-lifetime.md)

## <a name="configurable-token-lifetime-properties"></a>Właściwości okresu istnienia tokenu z możliwością konfiguracji
Zasady okresu istnienia tokenu to typ obiektu zasad, który zawiera reguły okresu istnienia tokenu. Te zasady kontrolują, jak długo tokeny dostępu, saml i identyfikator dla tego zasobu są uznawane za prawidłowe. Nie można ustawić zasad okresu istnienia tokenów dla tokenów odświeżania i sesji. Jeśli żadne zasady nie są ustawione, system wymusza domyślną wartość okresu istnienia.

### <a name="access-id-and-saml2-token-lifetime-policy-properties"></a>Właściwości zasad okresu istnienia tokenu SAML2, identyfikatora i dostępu

Zmniejszenie właściwości Okresu istnienia tokenu dostępu zmniejsza ryzyko tokenu dostępu lub tokenu identyfikatora używanego przez złośliwy podmiot przez dłuższy czas. (Tych tokenów nie można odwołać). Ta niekorzystnie wpływa na wydajność, ponieważ tokeny muszą być wymieniane częściej.

Aby uzyskać przykład, zobacz [Create a policy for web sign-in](configure-token-lifetimes.md#create-a-policy-for-web-sign-in)(Tworzenie zasad logowania do sieci Web).

Na konfigurację tokenu SAML2, identyfikatora i dostępu mają wpływ następujące właściwości i ich odpowiednio ustawione wartości:

- **Właściwość:** Okres istnienia tokenu dostępu
- **Ciąg właściwości zasad:** AccessTokenLifetime
- **Dotyczy:** tokeny dostępu, tokeny identyfikatorów, tokeny SAML2
- **Wartość domyślna:**
    - Tokeny dostępu: różnią się w zależności od aplikacji klienckiej żądającej tokenu. Na przykład klienci z możliwością ciągłej oceny dostępu (CAE, continuous access evaluation), którzy negocjują sesje z świadomość cae, zobaczą długi okres istnienia tokenu (do 28 godzin).
    - Tokeny identyfikatorów, tokeny SAML2: 1 godzina
- **Minimum:** 10 minut
- **Maksymalna:** 1 dzień

### <a name="refresh-and-session-token-lifetime-policy-properties"></a>Właściwości zasad okresu istnienia tokenu odświeżania i sesji

Na konfigurację tokenu odświeżania i sesji mają wpływ następujące właściwości i ich odpowiednio ustawione wartości. Po wycofaniem konfiguracji odświeżania i tokenu sesji 30 stycznia 2021 r. usługa Azure AD będzie honorować tylko wartości domyślne opisane poniżej. Jeśli zdecydujesz się [](../conditional-access/howto-conditional-access-session-lifetime.md) nie używać dostępu warunkowego do zarządzania częstotliwością logowania, tokeny odświeżania i sesji zostaną ustawione na domyślną konfigurację w tym dniu i nie będzie można już zmieniać ich okresów istnienia.  

|Właściwość   |Ciąg właściwości zasad    |Wpływa |Domyślne |
|----------|-----------|------------|------------|
|Maksymalny czas nieaktywności tokenu odświeżania |MaxInactiveTime  |Odświeżanie tokenów |90 dni  |
|Single-Factor odświeżania tokenu maksymalny wiek  |MaxAgeSingleFactor  |Odświeżanie tokenów (dla wszystkich użytkowników)  |Do odwołania  |
|Maksymalny wiek tokenu odświeżania wieloskładnikowego  |MaxAgeMultiFactor  |Odświeżanie tokenów (dla wszystkich użytkowników) |Do odwołania  |
|Single-Factor maksymalny wiek tokenu sesji  |MaxAgeSessionSingleFactor |Tokeny sesji (trwałe i nietrwale nietrwale)  |Do odwołania |
|Maksymalny wiek tokenu sesji wieloskładnikowej  |MaxAgeSessionMultiFactor  |Tokeny sesji (trwałe i nietrwale nietrwale)  |Do odwołania |

Możesz użyć programu PowerShell, aby znaleźć zasady, na które będzie mieć wpływ wycofanie.  Użyj poleceń [cmdlet programu PowerShell,](configure-token-lifetimes.md#get-started) aby wyświetlić wszystkie zasady utworzone w organizacji lub dowiedzieć się, które aplikacje i jednostki usługi są połączone z określonymi zasadami.

## <a name="policy-evaluation-and-prioritization"></a>Ocena i priorytetyzacja zasad
Możesz utworzyć, a następnie przypisać zasady okresu istnienia tokenu do określonej aplikacji, do organizacji i do jednostki usługi. Do określonej aplikacji może mieć zastosowanie wiele zasad. Zasady okresu istnienia tokenu, które obowiązują, są następujące:

* Jeśli zasady są jawnie przypisane do jednostki usługi, są wymuszane.
* Jeśli żadna zasada nie jest jawnie przypisana do jednostki usługi, wymuszane są zasady jawnie przypisane do organizacji nadrzędnej jednostki usługi.
* Jeśli żadna zasada nie jest jawnie przypisana do jednostki usługi lub do organizacji, zasady przypisane do aplikacji są wymuszane.
* Jeśli do jednostki usługi, organizacji lub obiektu aplikacji nie przypisano żadnych zasad, wymuszane są wartości domyślne. (Zobacz tabelę we [właściwościach okresu istnienia tokenu konfigurowalnego).](#configurable-token-lifetime-properties)

Aby uzyskać więcej informacji na temat relacji między obiektami aplikacji i obiektami jednostki usługi, zobacz [Application and service principal objects in Azure Active Directory](app-objects-and-service-principals.md).

Ważność tokenu jest oceniana w czasie, gdy token jest używany. Zasady o najwyższym priorytecie dla aplikacji, do której jest uzyskiwany dostęp, są obowiązywać.

Wszystkie używane w tym miejscu czasy są formatowane zgodnie z obiektem [TimeSpan](/dotnet/api/system.timespan) języka C# — D.HH:MM:SS.  Zatem 80 dni i 30 minut to `80.00:30:00` .  Wiodącą wartość D można porzucić, jeśli wynosi zero, więc 90 minut to `00:90:00` .  

## <a name="cmdlet-reference"></a>Dokumentacja poleceń cmdlet

Są to polecenia cmdlet w module [Azure Active Directory PowerShell dla programu Graph w wersji zapoznawczej.](/powershell/module/azuread/?view=azureadps-2.0-preview&preserve-view=true#service-principals)

### <a name="manage-policies"></a>Zarządzanie zasadami

Następujące polecenia cmdlet mogą być służące do zarządzania zasadami.

| Polecenie cmdlet | Opis | 
| --- | --- |
| [New-AzureADPolicy](/powershell/module/azuread/new-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Tworzy nowe zasady. |
| [Get-AzureADPolicy](/powershell/module/azuread/get-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Pobiera wszystkie zasady usługi Azure AD lub określone zasady. |
| [Get-AzureADPolicyAppliedObject](/powershell/module/azuread/get-azureadpolicyappliedobject?view=azureadps-2.0-preview&preserve-view=true) | Pobiera wszystkie aplikacje i jednostki usługi połączone z zasadami. |
| [Set-AzureADPolicy](/powershell/module/azuread/set-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Aktualizuje istniejące zasady. |
| [Remove-AzureADPolicy](/powershell/module/azuread/remove-azureadpolicy?view=azureadps-2.0-preview&preserve-view=true) | Usuwa określone zasady. |

### <a name="application-policies"></a>Zasady aplikacji
W przypadku zasad aplikacji można użyć następujących polecenia cmdlet.</br></br>

| Polecenie cmdlet | Opis | 
| --- | --- |
| [Add-AzureADApplicationPolicy](/powershell/module/azuread/add-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Łączy określone zasady z aplikacją. |
| [Get-AzureADApplicationPolicy](/powershell/module/azuread/get-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Pobiera zasady przypisane do aplikacji. |
| [Remove-AzureADApplicationPolicy](/powershell/module/azuread/remove-azureadapplicationpolicy?view=azureadps-2.0-preview&preserve-view=true) | Usuwa zasady z aplikacji. |

### <a name="service-principal-policies"></a>Zasady jednostki usługi
Następujące polecenia cmdlet można użyć dla zasad jednostki usługi.

| Polecenie cmdlet | Opis | 
| --- | --- |
| [Add-AzureADServicePrincipalPolicy](/powershell/module/azuread/add-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Łączy określone zasady z jednostką usługi. |
| [Get-AzureADServicePrincipalPolicy](/powershell/module/azuread/get-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Pobiera wszystkie zasady połączone z określoną jednostką usługi.|
| [Remove-AzureADServicePrincipalPolicy](/powershell/module/azuread/remove-azureadserviceprincipalpolicy?view=azureadps-2.0-preview&preserve-view=true) | Usuwa zasady z określonej jednostki usługi.|

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, przeczytaj [przykłady konfigurowania okresów istnienia tokenu.](configure-token-lifetimes.md)
