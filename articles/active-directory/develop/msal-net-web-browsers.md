---
title: Korzystanie z przeglądarek internetowych (MSAL.NET) | Azure
titleSuffix: Microsoft identity platform
description: Dowiedz się więcej o konkretnych zagadnieniach dotyczących używania platformy Xamarin Android z biblioteką uwierzytelniania firmy Microsoft dla platformy .NET (MSAL.NET).
services: active-directory
author: mmacy
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/18/2020
ms.author: marsma
ms.reviewer: saeeda
ms.custom: devx-track-csharp, aaddev
ms.openlocfilehash: 4121d4b9ac73ed18da7dce0e397fe919589ac6f0
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107478764"
---
# <a name="using-web-browsers-msalnet"></a>Korzystanie z przeglądarek internetowych (MSAL.NET)

Przeglądarki internetowe są wymagane do uwierzytelniania interakcyjnego. Domyślnie program MSAL.NET systemowej [przeglądarki internetowej na](#system-web-browser-on-xamarinios-xamarinandroid) platformach Xamarin.iOS i Xamarin.Android. Można [jednak](#enable-embedded-webviews-on-ios-and-android) również włączyć osadzoną przeglądarkę internetową w zależności od wymagań (środowiska użytkownika, potrzeby logowania jednokrotnego, zabezpieczeń) w aplikacjach [Xamarin.iOS i](#choosing-between-embedded-web-browser-or-system-browser-on-xamarinios) [Xamarin.Android.](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) Możesz również wybrać [dynamiczną](#detecting-the-presence-of-custom-tabs-on-xamarinandroid) przeglądarkę internetową do użycia na podstawie obecności przeglądarki Chrome lub przeglądarki z niestandardowymi kartami przeglądarki Chrome w systemie Android. MSAL.NET obsługuje tylko przeglądarkę systemową w aplikacjach klasycznych .NET Core.

## <a name="web-browsers-in-msalnet"></a>Przeglądarki internetowe w MSAL.NET

### <a name="interaction-happens-in-a-web-browser"></a>Interakcja odbywa się w przeglądarce internetowej

Należy pamiętać, że podczas interaktywnego uzyskiwania tokenu zawartość okna dialogowego nie jest dostarczana przez bibliotekę, ale przez usługę STS (Security Token Service). Punkt końcowy uwierzytelniania wysyła z powrotem niektóre elementy HTML i JavaScript, które kontrolują interakcję, która jest renderowana w przeglądarce internetowej lub kontrolce internetowej. Zezwalanie u sts na obsługę interakcji HTML ma wiele zalet:

- Hasło (jeśli zostało wpisane) nigdy nie jest przechowywane przez aplikację ani przez bibliotekę uwierzytelniania.
- Umożliwia przekierowywanie do innych dostawców tożsamości (na przykład logowanie przy użyciu konta służbowego lub konta osobistego przy użyciu usługi MSAL lub konta społecznościowego z Azure AD B2C).
- Umożliwia ujmowanie kontroli dostępu warunkowego przez usługę STS, na przykład przez wprowadzenie przez użytkownika uwierzytelniania wieloskładnikowego [(MFA)](../authentication/concept-mfa-howitworks.md) podczas fazy uwierzytelniania (wprowadzanie numeru PIN usługi Windows Hello lub wywoływanie połączenia na telefonie lub w aplikacji uwierzytelniania na telefonie). W przypadkach, gdy wymagane uwierzytelnianie wieloskładnikowe nie zostało jeszcze ustawione, użytkownik może skonfigurować je dokładnie na czas w tym samym oknie dialogowym.  Użytkownik wprowadza swój numer telefonu komórkowego i jest przekierowyny w celu zainstalowania aplikacji uwierzytelniania i zeskanowania tagu QR w celu dodania konta. Ta interakcja sterowany przez serwer to doskonałe środowisko!
- Umożliwia użytkownikowi zmianę hasła w tym samym oknie dialogowym, gdy hasło wygasło (podając dodatkowe pola dla starego hasła i nowego hasła).
- Umożliwia znakowanie dzierżawy lub aplikacji (obrazów) kontrolowanej przez administratora dzierżawy/właściciela aplikacji usługi Azure AD.
- Umożliwia użytkownikom wyrażenie zgody na dostęp aplikacji do zasobów/zakresów w ich nazwie zaraz po uwierzytelnieniu.

### <a name="embedded-vs-system-web-ui"></a>Osadzony, a systemowy internetowy interfejs użytkownika

MSAL.NET jest biblioteką wieloplatformową i zawiera kod specyficzny dla platformy do hostowania przeglądarki w kontrolce interfejsu użytkownika (na przykład na platformie .NET Classic używa winForms, na platformie Xamarin używa natywnych kontrolek mobilnych itp.). Ta kontrolka jest nazywana `embedded` internetowym interfejsem użytkownika. Alternatywnie MSAL.NET może również wystartować z przeglądarką systemu operacyjnego.

Ogólnie rzecz biorąc, zaleca się użycie domyślnej platformy i zazwyczaj jest to przeglądarka systemowa. Przeglądarka systemowa lepiej zapamięta użytkowników, którzy zalogowali się wcześniej. Aby zmienić to zachowanie, użyj `WithUseEmbeddedWebView(bool)`

### <a name="at-a-glance"></a>W skrócie

| Framework        | Osadzone | System | Domyślne |
| ------------- |-------------| -----| ----- |
| .NET Classic     | Tak | Tak^ | Osadzone |
| .NET Core     | Nie | Tak^ | System |
| .NET Standard | Nie | Tak^ | System |
| Platforma UWP | Tak | Nie | Osadzone |
| Xamarin.Android | Tak | Tak  | System |
| Xamarin.iOS | Tak | Tak  | System |
| Xamarin.Mac| Tak | Nie | Osadzone |

^ Wymaga " http://localhost " przekierowania URI

## <a name="system-web-browser-on-xamarinios-xamarinandroid"></a>Systemowa przeglądarka sieci Web na platformie Xamarin.iOS, Xamarin.Android

Domyślnie program MSAL.NET systemowej przeglądarki sieci Web na platformach Xamarin.iOS, Xamarin.Android i .NET Core. Dla wszystkich platform, które zapewniają interfejs użytkownika (czyli nie .NET Core), biblioteka osadza kontrolkę przeglądarki internetowej w oknie dialogowym. MSAL.NET korzysta również z osadzonego widoku internetowego dla programu .NET Desktop i usługi WAB dla platformy UWP. Jednak domyślnie korzysta z systemowej przeglądarki internetowej **dla** aplikacji Xamarin iOS i Xamarin Android. W systemie iOS wybiera nawet widok internetowy do użycia w zależności od wersji systemu operacyjnego (iOS12, iOS11 i starszej).

Korzystanie z przeglądarki systemowej ma znaczącą zaletę udostępniania stanu logowania jednokrotnego innym aplikacjom i aplikacjom internetowym bez konieczności pośrednictwa (Portal firmy/Aplikacja Authenticator). Przeglądarka systemowa była domyślnie używana w programie MSAL.NET dla platform Xamarin iOS i Xamarin Android, ponieważ na tych platformach systemowa przeglądarka internetowa zajmuje cały ekran, a środowisko użytkownika jest lepsze. Systemowy widok internetowy nie można odróżnić od okna dialogowego. Jednak w systemie iOS użytkownik może być musiał wyrazić zgodę na wywołanie aplikacji przez przeglądarkę, co może być uciążliwe.

## <a name="system-browser-experience-on-net"></a>Środowisko przeglądarki systemu na .NET 

Na platformie .NET Core MSAL.NET uruchomić przeglądarkę systemu jako osobny proces. MSAL.NET nie ma kontroli nad tą przeglądarką, ale gdy użytkownik zakończy uwierzytelnianie, strona internetowa zostanie przekierowana w taki sposób, MSAL.NET przechwyci identyfikator URI.

Możesz również skonfigurować aplikacje napisane dla klasycznej wersji .NET lub .NET 5 do korzystania z tej przeglądarki, określając:

```csharp
await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
```

MSAL.NET może wykryć, czy użytkownik odchodzi lub po prostu zamyka przeglądarkę. Aplikacje korzystające z tej techniki są zachęcane do zdefiniowania limitu czasu (za pośrednictwem `CancellationToken` metody ). Zalecamy co najmniej kilkuminutowy limit czasu, aby uwzględnić przypadki, w których użytkownik jest monitowany o zmianę hasła lub przeprowadzenie uwierzytelniania wieloskładnikowego.

### <a name="how-to-use-the-default-os-browser"></a>Jak używać domyślnej przeglądarki systemu operacyjnego

MSAL.NET musi nasłuchiwać i przechwytywać kod, który wysyła aplikacja AAD po uwierzytelnieniu użytkownika (zobacz Kod autoryzacji, aby `http://localhost:port` uzyskać szczegółowe informacje) [](v2-oauth2-auth-code-flow.md)

Aby włączyć przeglądarkę systemu:

1. Podczas rejestracji aplikacji skonfiguruj jako `http://localhost` URI przekierowania (obecnie nie jest obsługiwany przez usługę B2C)
2. Podczas konstruowania aplikacji PublicClientApplication określ ten adres URI przekierowania:

```csharp
IPublicClientApplication pca = PublicClientApplicationBuilder
                            .Create("<CLIENT_ID>")
                             // or use a known port if you wish "http://localhost:1234"
                            .WithRedirectUri("http://localhost")  
                            .Build();
```

> [!Note]
> Jeśli skonfigurujesz program , MSAL.NET znajdzie losowy otwarty `http://localhost` port i użyje go.

### <a name="linux-and-mac"></a>Linux i MAC

W systemie Linux MSAL.NET domyślną przeglądarkę systemu operacyjnego za pomocą narzędzia xdg-open. Aby rozwiązać problemy, uruchom narzędzie z terminalu, na przykład `xdg-open "https://www.bing.com"` . Na komputerze Mac przeglądarka jest otwierana przez wywołania `open <url>` .

### <a name="customizing-the-experience"></a>Dostosowywanie obsługi

> [!NOTE]
> Dostosowywanie jest dostępne w MSAL.NET 4.1.0 lub nowszej.

MSAL.NET może odpowiedzieć za pomocą komunikatu HTTP w przypadku odebrania tokenu lub w przypadku wystąpienia błędu. Możesz wyświetlić komunikat HTML lub przekierować do wybranego adresu URL:

```csharp
var options = new SystemWebViewOptions() 
{
    HtmlMessageError = "<p> An error occured: {0}. Details {1}</p>",
    BrowserRedirectSuccess = new Uri("https://www.microsoft.com");
}

await pca.AcquireTokenInteractive(s_scopes)
         .WithUseEmbeddedWebView(false)
         .WithSystemWebViewOptions(options)
         .ExecuteAsync();
```

### <a name="opening-a-specific-browser-experimental"></a>Otwieranie określonej przeglądarki (eksperymentalne)

Możesz dostosować sposób, w jaki MSAL.NET otwiera przeglądarkę. Na przykład zamiast używać przeglądarki domyślnej, możesz wymusić otwarcie określonej przeglądarki:

```csharp
var options = new SystemWebViewOptions() 
{
    OpenBrowserAsync = SystemWebViewOptions.OpenWithEdgeBrowserAsync
}
```

### <a name="uwp-doesnt-use-the-system-webview"></a>UWP doesn't use the System Webview

Jednak w przypadku aplikacji klasycznych uruchomienie systemowej aplikacji Webview prowadzi do obsługi użytkownika podrzędnego, ponieważ użytkownik widzi przeglądarkę, w której mogą już być otwarte inne karty. A po uwierzytelnieniu użytkownicy pobierają stronę z prośbą o zamknięcie tego okna. Jeśli użytkownik nie zwróci uwagi, może zamknąć cały proces (w tym inne karty, które nie są związane z uwierzytelnianiem). Korzystanie z przeglądarki systemu na pulpicie wymagałoby również otwarcia portów lokalnych i nasłuchiwania na nich, co może wymagać zaawansowanych uprawnień dla aplikacji. Jako deweloper, użytkownik lub administrator możesz nie chcieć tego wymagania.

## <a name="enable-embedded-webviews-on-ios-and-android"></a>Włączanie osadzonych widoków internetowych w systemach iOS i Android

Osadzone widoku internetowe można również włączyć w aplikacjach Xamarin.iOS i Xamarin.Android. Począwszy od MSAL.NET 2.0.0 w wersji zapoznawczej, MSAL.NET również korzystać z opcji **osadzonego** widoku internetowego. W ADAL.NET osadzonego widoku internetowego jest jedyną obsługiwaną opcją.

Jako deweloper korzystający MSAL.NET platformie Xamarin możesz używać osadzonych widoków internetowych lub przeglądarek systemowych. Jest to wybór zależny od środowiska użytkownika i kwestii związanych z zabezpieczeniami, na które chcesz się kierować.

Obecnie MSAL.NET nie obsługuje jeszcze brokerów systemów Android i iOS. W związku z tym w celu zapewnienia logowania jednokrotnego (SSO) przeglądarka systemowa może być nadal lepszym rozwiązaniem. Brokerzy obsługujący z osadzoną przeglądarką internetową są na MSAL.NET listy prac.

### <a name="differences-between-embedded-webview-and-system-browser"></a>Różnice między osadzonym elementem webview i przeglądarką systemowa
Istnieją pewne różnice wizualne między osadzonym elementem webview i przeglądarką systemowa w MSAL.NET.

**Interakcyjne logowanie za pomocą MSAL.NET przy użyciu osadzonego widoku internetowego:**

![osadzona](media/msal-net-web-browsers/embedded-webview.png)

**Interakcyjne logowanie za pomocą MSAL.NET przy użyciu przeglądarki systemowej:**

![Przeglądarka systemu](media/msal-net-web-browsers/system-browser.png)

### <a name="developer-options"></a>Opcje dewelopera

Jako deweloper korzystający MSAL.NET masz kilka opcji wyświetlania interaktywnego okna dialogowego z usługi STS:

- **Przeglądarka systemu.** Przeglądarka systemowa jest domyślnie ustawiana w bibliotece. Jeśli korzystasz z systemu Android, zapoznaj się z [przeglądarkami systemowym,](msal-net-system-browser-android-considerations.md) aby uzyskać szczegółowe informacje o tym, które przeglądarki są obsługiwane w celu uwierzytelniania. W przypadku korzystania z przeglądarki systemowej w systemie Android zalecamy, aby na urządzeniu była przeglądarka obsługująca niestandardowe karty przeglądarki Chrome.  W przeciwnym razie uwierzytelnianie może się nie powieść.
- **Osadzony widok internetowy.** Aby używać tylko osadzonego widoku internetowego MSAL.NET, `AcquireTokenInteractively` konstruktor parametrów zawiera metodę `WithUseEmbeddedWebView()` .

    iOS

    ```csharp
    AuthenticationResult authResult;
    authResult = app.AcquireTokenInteractively(scopes)
                    .WithUseEmbeddedWebView(useEmbeddedWebview)
                    .ExecuteAsync();
    ```

    Android:

    ```csharp
    authResult = app.AcquireTokenInteractively(scopes)
                .WithParentActivityOrWindow(activity)
                .WithUseEmbeddedWebView(useEmbeddedWebview)
                .ExecuteAsync();
    ```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinios"></a>Wybieranie między osadzoną przeglądarką internetową a przeglądarką systemu na stronie Xamarin.iOS

W aplikacji systemu iOS w `AppDelegate.cs` programie można zainicjować dla `ParentWindow` . `null` Nie jest używana w systemie iOS

```csharp
App.ParentWindow = null; // no UI parent on iOS
```

#### <a name="choosing-between-embedded-web-browser-or-system-browser-on-xamarinandroid"></a>Wybieranie między osadzoną przeglądarką internetową a przeglądarką systemowa na platformie Xamarin.Android

W aplikacji systemu Android w programie można ustawić działanie nadrzędne, aby `MainActivity.cs` wynik uwierzytelniania został do niego ponownie ustawiony:

```csharp
 App.ParentWindow = this;
```

Następnie w programie `MainPage.xaml.cs` :

```csharp
authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(true)
                      .ExecuteAsync();
```

#### <a name="detecting-the-presence-of-custom-tabs-on-xamarinandroid"></a>Wykrywanie obecności niestandardowych kart na platformie Xamarin.Android

Jeśli chcesz włączyć logowanie jednokrotne z aplikacjami uruchomionymi w przeglądarce za pomocą systemowej przeglądarki internetowej, ale martwisz się o środowisko użytkownika dla urządzeń z systemem Android bez przeglądarki z obsługą kart niestandardowych, możesz zdecydować, wywołując metodę w . `IsSystemWebViewAvailable()` `IPublicClientApplication` Ta metoda zwraca wartość , jeśli packageManager wykryje niestandardowe karty i nie `true` `false` zostaną wykryte na urządzeniu.

Na podstawie wartości zwróconej przez tę metodę i wymagań możesz podjąć decyzję:

- Możesz zwrócić użytkownikowi niestandardowy komunikat o błędzie. Na przykład: "Zainstaluj przeglądarkę Chrome, aby kontynuować uwierzytelnianie" -OR-
- Możesz wrócić do opcji osadzonego widoku internetowego i uruchomić interfejs użytkownika jako osadzony element webview.

Poniższy kod przedstawia opcję osadzonego widoku internetowego:

```csharp
bool useSystemBrowser = app.IsSystemWebviewAvailable();

authResult = await App.PCA.AcquireTokenInteractive(App.Scopes)
                      .WithParentActivityOrWindow(App.ParentWindow)
                      .WithUseEmbeddedWebView(!useSystemBrowser)
                      .ExecuteAsync();
```

#### <a name="net-core-doesnt-support-interactive-authentication-with-an-embedded-browser"></a>Program .NET Core nie obsługuje interaktywnego uwierzytelniania za pomocą przeglądarki osadzonej

W przypadku programu .NET Core interaktywne pozyskiwanie tokenów jest dostępne tylko za pośrednictwem systemowej przeglądarki internetowej, a nie z osadzonymi widokami internetowymi. W rzeczywistości program .NET Core nie zapewnia jeszcze interfejsu użytkownika.
Jeśli chcesz dostosować środowisko przeglądania za pomocą systemowej przeglądarki internetowej, możesz zaimplementować interfejs [IWithCustomUI,](scenario-desktop-acquire-token.md#withcustomwebui) a nawet udostępnić własną przeglądarkę.
