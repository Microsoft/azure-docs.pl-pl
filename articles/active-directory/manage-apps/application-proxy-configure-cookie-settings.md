---
title: Ustawienia plików cookie serwera proxy aplikacji — Azure Active Directory | Microsoft Docs
description: Azure Active Directory (Azure AD) zawiera pliki cookie dostępu i sesji do uzyskiwania dostępu do aplikacji lokalnych za pomocą serwera proxy aplikacji. W tym artykule opisano sposób używania i konfigurowania ustawień plików cookie.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 01/16/2019
ms.author: kenwith
ms.reviewer: japere
ms.collection: M365-identity-device-management
ms.openlocfilehash: 62afe97b44f45bc0b7aa12b33b6a65dd94ecf095
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99252206"
---
# <a name="cookie-settings-for-accessing-on-premises-applications-in-azure-active-directory"></a>Ustawienia plików cookie do uzyskiwania dostępu do aplikacji lokalnych w Azure Active Directory

Azure Active Directory (Azure AD) zawiera pliki cookie dostępu i sesji do uzyskiwania dostępu do aplikacji lokalnych za pomocą serwera proxy aplikacji. Dowiedz się, jak używać ustawień plików cookie serwera proxy aplikacji. 

## <a name="what-are-the-cookie-settings"></a>Jakie są ustawienia plików cookie?

[Serwer proxy aplikacji](application-proxy.md) używa następujących ustawień dostępu i pliku cookie sesji.

| Ustawienie pliku cookie | Domyślne | Opis | Zalecenia |
| -------------- | ------- | ----------- | --------------- |
| Użyj pliku cookie tylko HTTP | **Nie** | **Wartość tak** umożliwia serwerowi proxy aplikacji dołączenie flagi HTTPOnly w nagłówkach odpowiedzi HTTP. Ta flaga zapewnia dodatkowe korzyści z zabezpieczeń, na przykład uniemożliwiają kopiowanie lub modyfikowanie plików cookie przez skrypty po stronie klienta.<br></br><br></br>Przed rozpoczęciem obsługi ustawień HTTP-Only serwer proxy aplikacji zaszyfrowany i przesyłający pliki cookie przez zabezpieczony kanał TLS w celu ochrony przed modyfikacją. | Użyj **tak** ze względu na dodatkowe korzyści z zabezpieczeń.<br></br><br></br>**Nie** należy używać klientów ani agentów użytkowników, którzy wymagają dostępu do pliku cookie sesji. Na przykład użyj **nie** dla klienta RDP lub MTSC, który łączy się z serwerem bramy pulpit zdalny za pomocą serwera proxy aplikacji.|
| Użyj bezpiecznego pliku cookie | **Nie** | **Wartość tak** umożliwia serwerowi proxy aplikacji uwzględnienie bezpiecznej flagi w nagłówkach odpowiedzi HTTP. Zabezpieczanie plików cookie zwiększa bezpieczeństwo przez przesyłanie plików cookie za pośrednictwem bezpiecznego kanału TLS, takiego jak HTTPS. Zapobiega to zastępowaniu plików cookie przez nieautoryzowane strony ze względu na przesłanie pliku cookie w postaci zwykłego tekstu. | Użyj **tak** ze względu na dodatkowe korzyści z zabezpieczeń.|
| Używaj trwałego pliku cookie | **Nie** | **Wartość tak** umożliwia serwerowi proxy aplikacji ustawianie plików cookie dostępu, które nie wygasną po zamknięciu przeglądarki sieci Web. Trwałość obowiązuje do momentu wygaśnięcia tokenu dostępu lub do momentu ręcznego usunięcia trwałych plików cookie przez użytkownika. | **Nie** należy używać ze względu na zagrożenie bezpieczeństwa związane z uwierzytelnianiem użytkowników.<br></br><br></br>Sugerujemy używanie **tak** dla starszych aplikacji, które nie mogą udostępniać plików cookie między procesami. Lepiej jest zaktualizować swoją aplikację, aby obsługiwała udostępnianie plików cookie między procesami zamiast korzystać z trwałych plików cookie. Można na przykład potrzebować trwałych plików cookie, aby użytkownicy mogli otwierać dokumenty pakietu Office w widoku Eksploratora z witryny programu SharePoint. Bez trwałych plików cookie ta operacja może zakończyć się niepowodzeniem, jeśli pliki cookie dostępu nie są współużytkowane przez przeglądarkę, proces Eksploratora i proces biura. |

## <a name="samesite-cookies"></a>Pliki cookie SameSite
Począwszy od wersji Chrome 80 i ostatecznie w przeglądarkach wykorzystujących chrom, pliki cookie, które nie określają atrybutu [SameSite](https://web.dev/samesite-cookies-explained) , będą traktowane tak, jakby były ustawione na **SameSite = swobodny**. Atrybut SameSite deklaruje, jak pliki cookie powinny być ograniczone do kontekstu w tym samym miejscu. Po ustawieniu na swobodny plik cookie jest wysyłany tylko do tych samych żądań witryny lub nawigacji najwyższego poziomu. Jednak serwer proxy aplikacji wymaga zachowania tych plików cookie w kontekście innej firmy, aby zapewnić użytkownikom prawidłowe logowanie w trakcie sesji. Z tego powodu wprowadzamy aktualizacje plików cookie dostępu do serwera proxy aplikacji i sesji w celu uniknięcia niekorzystnego wpływu na tę zmianę. Aktualizacje obejmują:

* Ustawianie atrybutu **SameSite** na **none**. Pozwala to na prawidłowe wysyłanie plików cookie do serwera proxy aplikacji i sesji w kontekście innej firmy.
* Ustawienie opcji **Użyj bezpiecznego pliku cookie** do użycia **tak** jako domyślnego. Program Chrome wymaga również plików cookie, aby można było określić flagę Secure lub zostanie odrzucona. Ta zmiana będzie miała zastosowanie do wszystkich istniejących aplikacji publikowanych za poorednictwem serwera proxy aplikacji. Należy pamiętać, że pliki cookie dostępu do serwera proxy aplikacji mają zawsze ustawioną wartość Secure i są przesyłane tylko za pośrednictwem protokołu HTTPS. Ta zmiana będzie dotyczyć tylko plików cookie sesji.

Te zmiany w plikach cookie aplikacji są uwzględniane w ciągu następnych kilku tygodni przed datą wydania Chrome 80.

Ponadto, jeśli aplikacja zaplecza zawiera pliki cookie, które muszą być dostępne w kontekście innej firmy, należy jawnie zadecydować o zmianie aplikacji tak, aby korzystała z SameSite = None dla tych plików cookie. Serwer proxy aplikacji tłumaczy nagłówek Set-Cookie na jego adresy URL i uwzględni ustawienia tych plików cookie ustawionych przez aplikację zaplecza.



## <a name="set-the-cookie-settings---azure-portal"></a>Ustawianie ustawień plików cookie — Azure Portal
Aby ustawić ustawienia plików cookie przy użyciu Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Przejdź do **Azure Active Directory**   >  **aplikacje dla przedsiębiorstw**   >  **wszystkie aplikacje**.
3. Wybierz aplikację, dla której chcesz włączyć ustawienie plików cookie.
4. Kliknij pozycję **serwer proxy aplikacji**.
5. W obszarze **Ustawienia dodatkowe** Ustaw dla ustawienia plik cookie **wartość tak** lub **nie**.
6. Aby zastosować zmiany, kliknij pozycję **Zapisz**. 

## <a name="view-current-cookie-settings---powershell"></a>Wyświetlanie bieżących ustawień plików cookie — PowerShell

Aby wyświetlić bieżące ustawienia plików cookie dla aplikacji, użyj tego polecenia programu PowerShell:  

```powershell
Get-AzureADApplicationProxyApplication -ObjectId <ObjectId> | fl * 
```

## <a name="set-cookie-settings---powershell"></a>Ustawianie ustawień plików cookie — PowerShell

W następujących poleceniach programu PowerShell ```<ObjectId>``` jest identyfikatorem objectid aplikacji. 

**Plik cookie dotyczący tylko protokołu http** 

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsHttpOnlyCookieEnabled $false 
```

**Bezpieczny plik cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsSecureCookieEnabled $false 
```

**Trwałe pliki cookie**

```powershell
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $true 
Set-AzureADApplicationProxyApplication -ObjectId <ObjectId> -IsPersistentCookieEnabled $false 
```
