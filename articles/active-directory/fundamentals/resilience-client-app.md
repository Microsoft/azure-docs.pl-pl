---
title: Zwiększanie odporności uwierzytelniania i autoryzacji w aplikacjach klienckich, które tworzysz
titleSuffix: Microsoft identity platform
description: Wskazówki dotyczące zwiększenia odporności uwierzytelniania i autoryzacji w aplikacji klienckiej przy użyciu platformy tożsamości firmy Microsoft
services: active-directory
ms.service: active-directory
ms.subservice: fundamentals
ms.workload: identity
ms.topic: how-to
author: knicholasa
ms.author: nichola
manager: martinco
ms.date: 11/23/2020
ms.openlocfilehash: b32f9dd10d9bd03a7e446616d9941e7bd1a9c3ed
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98724912"
---
# <a name="increase-the-resilience-of-authentication-and-authorization-in-client-applications-you-develop"></a>Zwiększanie odporności uwierzytelniania i autoryzacji w aplikacjach klienckich, które tworzysz

Ta sekcja zawiera wskazówki dotyczące tworzenia odporności na aplikacje klienckie korzystające z platformy tożsamości firmy Microsoft oraz Azure Active Directory logowania użytkowników i wykonywania działań w imieniu użytkowników.

## <a name="use-the-microsoft-authentication-library-msal"></a>Korzystanie z biblioteki uwierzytelniania firmy Microsoft (MSAL)

[Biblioteka Microsoft Authentication Library (MSAL)](../develop/msal-overview.md) jest kluczową częścią [platformy tożsamości firmy Microsoft](../develop/index.yml). Upraszcza to i zarządza uzyskiwaniem, zarządzaniem, buforowaniem i odświeżaniem tokenów oraz używa najlepszych rozwiązań dotyczących odporności. MSAL zaprojektowano tak, aby umożliwić bezpieczne rozwiązanie bez deweloperów, którzy nie muszą martwić się o szczegóły implementacji.

MSAL buforuje tokeny i używa wzorca pozyskiwania tokenów dyskretnych. Automatycznie serializacji pamięć podręczną tokenów na platformach, które natywnie zapewniają bezpieczny magazyn, taki jak Windows platformy UWP, iOS i Android. Deweloperzy mogą dostosowywać zachowanie serializacji w przypadku używania [Microsoft. Identity. Web](https://github.com/AzureAD/microsoft-identity-web/wiki/token-cache-serialization), [MSAL.NET](../develop/msal-net-token-cache-serialization.md), [MSAL for Java](../develop/msal-java-token-cache-serialization.md)i [MSAL dla języka Python](../develop/msal-python-token-cache-serialization.md).

![Obraz urządzenia i aplikacji używającej MSAL do wywołania tożsamości firmy Microsoft](media/resilience-client-app/resilience-with-microsoft-authentication-library.png)

W przypadku korzystania z MSAL, buforowanie tokenów, odświeżanie i pozyskiwanie dyskretne jest obsługiwane automatycznie. Możesz użyć prostych wzorców, aby uzyskać tokeny niezbędne do nowoczesnego uwierzytelniania. Obsługujemy wiele języków i możesz znaleźć przykład pasujący do Twojego języka i scenariusza na naszej stronie z [przykładami](../develop/sample-v2-code.md) .

## <a name="c"></a>[C#](#tab/csharp)

```csharp
try
{
    result = await app.AcquireTokenSilent(scopes, account).ExecuteAsync();
}
catch(MsalUiRequiredException ex)
{
    result = await app.AcquireToken(scopes).WithClaims(ex.Claims).ExecuteAsync()
}
```

## <a name="javascript"></a>[JavaScript](#tab/javascript)

```javascript
return myMSALObj.acquireTokenSilent(request).catch(error => {
    console.warn("silent token acquisition fails. acquiring token using redirect");
    if (error instanceof msal.InteractionRequiredAuthError) {
        // fallback to interaction when silent call fails
        return myMSALObj.acquireTokenPopup(request).then(tokenResponse => {
            console.log(tokenResponse);

            return tokenResponse;
        }).catch(error => {
            console.error(error);
        });
    } else {
        console.warn(error);
    }
});
```

---

MSAL może w niektórych przypadkach aktywnie odświeżać tokeny. Gdy tożsamość firmy Microsoft wystawia token długotrwały, może wysłać do klienta informacje o optymalnym czasie odświeżania tokenu ("Odśwież \_ w"). MSAL odświeży token na podstawie tych informacji. Aplikacja będzie nadal działać, gdy stary token jest prawidłowy, ale będzie miał dłuższy przedział czasu, w którym zostanie wykonane kolejne pomyślne pozyskiwanie tokenu.

### <a name="stay-up-to-date"></a>Bądź na bieżąco

Deweloperzy powinni mieć proces aktualizacji do najnowszej wersji MSAL. Uwierzytelnianie jest częścią zabezpieczeń aplikacji, a Twoja aplikacja musi pozostać aktualna dzięki udoskonaleniom zabezpieczeń zawartym w nowych wersjach MSAL. Jest to ogólnie dobre rozwiązanie w przypadku bibliotek, które są w fazie ciągłej programistycznej. dzięki temu będzie można uzyskać najbardziej aktualny kod w odniesieniu do odporności aplikacji. W miarę jak tożsamość firmy Microsoft kontynuuje wprowadzanie innowacji, aby aplikacje były bardziej odporne, aplikacje, które używają najnowszych MSALów, będą najbardziej przygotowane do kompilowania tych innowacji.

[Zapoznaj się z najnowszą wersją MSAL.js i informacje o wersji](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Zapoznaj się z najnowszą wersją programu MSAL .NET i informacje o wersji](https://github.com/AzureAD/microsoft-authentication-library-for-dotnet/releases)

[Zapoznaj się z najnowszą wersją MSAL języka Python i informacje o wersji](https://github.com/AzureAD/microsoft-authentication-library-for-python/releases)

[Zapoznaj się z najnowszą wersją języka Java MSAL i informacje o wersji](https://github.com/AzureAD/microsoft-authentication-library-for-java/releases)

[Zapoznaj się z najnowszymi wersjami MSAL iOS i macOS oraz informacjami o wersji](https://github.com/AzureAD/microsoft-authentication-library-for-objc/releases)

[Zapoznaj się z najnowszą wersją systemu Android MSAL i informacje o wersji](https://github.com/AzureAD/microsoft-authentication-library-for-android/releases)

[Sprawdź najnowszą wersję kątową MSAL i informacje o wersji](https://github.com/AzureAD/microsoft-authentication-library-for-js/releases)

[Zapoznaj się z najnowszą wersją Microsoft. Identity. Web i informacje o wersji](https://github.com/AzureAD/microsoft-identity-web/releases)

## <a name="use-resilient-patterns-for-token-handling"></a>Używanie odpornych wzorców na potrzeby obsługi tokenów

Jeśli nie korzystasz z programu MSAL, możesz użyć tych odpornych wzorców do obsługi tokenów. Te najlepsze rozwiązania są implementowane automatycznie przez bibliotekę MSAL. 

Ogólnie rzecz biorąc, Aplikacja korzystająca z nowoczesnego uwierzytelniania wywoła punkt końcowy do pobrania tokenów uwierzytelniających użytkownika lub autoryzuje aplikację w celu wywołania chronionych interfejsów API. MSAL jest przeznaczona do obsługi szczegółów uwierzytelniania i implementuje kilka wzorców w celu zwiększenia odporności tego procesu. Wskazówki zawarte w tej sekcji służą do implementowania najlepszych rozwiązań, jeśli zdecydujesz się użyć biblioteki innej niż MSAL. W przypadku korzystania z MSAL wszystkie te najlepsze rozwiązania są bezpłatne, ponieważ MSAL implementuje je automatycznie.

### <a name="cache-tokens"></a>Tokeny pamięci podręcznej

Aplikacje powinny prawidłowo buforować tokeny otrzymane od tożsamości firmy Microsoft. Gdy aplikacja otrzymuje tokeny, odpowiedź HTTP, która zawiera tokeny, zawiera również właściwość "expires_in", która informuje aplikację o tym, jak długo buforuje i ponownie używa token. W takim przypadku aplikacje używają właściwości "expires_in" do określenia cykl życia tokenu. Aplikacja nigdy nie musi próbować zdekodować tokenu dostępu interfejsu API.

![Aplikacja wywołująca tożsamość firmy Microsoft, ale wywołanie przechodzi przez pamięć podręczną tokenów na urządzeniu, na którym działa aplikacja](media/resilience-client-app/token-cache.png)

Użycie buforowanego tokenu zapobiega niepotrzebnemu ruchowi między aplikacją i tożsamością firmy Microsoft i sprawia, że aplikacja jest mniej podatna na błędy pozyskiwania tokenu poprzez zmniejszenie liczby wywołań pozyskiwania tokenu. Tokeny w pamięci podręcznej poprawiają również wydajność aplikacji, ponieważ aplikacja musi blokować pobieranie tokenów mniej więcej. Użytkownik może pozostać zalogowany do aplikacji przez czas istnienia tego tokenu.

### <a name="serialize-and-persist-tokens"></a>Serializować i utrwalanie tokenów

Aplikacje powinny bezpiecznie serializować ich pamięć podręczną tokenów, aby zachować tokeny między wystąpieniami aplikacji. Tokeny mogą być ponownie używane, o ile znajdują się w ich prawidłowym okresie istnienia. [Odśwież tokeny](../develop/v2-oauth2-auth-code-flow.md#refresh-the-access-token), a w coraz większym stopniu [tokeny dostępu](../develop/access-tokens.md)są wydawane przez wiele godzin. Ten prawidłowy czas może obejmować wiele razy użytkowników, którzy uruchamiają aplikację. Po uruchomieniu aplikacji należy sprawdzić, czy jest dostępny prawidłowy token dostępu lub odświeżania, który może być używany. Spowoduje to zwiększenie odporności i wydajności aplikacji, ponieważ pozwala to uniknąć niepotrzebnych połączeń z tożsamością firmy Microsoft.

![Aplikacja wywołująca tożsamość firmy Microsoft, ale wywołanie przechodzi przez pamięć podręczną tokenu oraz magazyn tokenów na urządzeniu, na którym działa aplikacja.](media/resilience-client-app/token-store.png)

Magazyn trwałych tokenów powinien mieć dostęp kontrolowany i zaszyfrowany do tożsamości użytkownika lub procesu będącego właścicielem. Na platformach, takich jak Mobile, Windows i Mac, deweloper powinien korzystać z wbudowanych możliwości przechowywania poświadczeń.

### <a name="acquire-tokens-silently"></a>Uzyskaj tokeny dyskretnie

Proces uwierzytelniania użytkownika lub pobieranie autoryzacji w celu wywołania interfejsu API może wymagać wielu kroków w ramach tożsamości firmy Microsoft. Na przykład gdy użytkownik loguje się po raz pierwszy, może zajść konieczność wprowadzenia poświadczeń i przeprowadzenia uwierzytelniania wieloskładnikowego za pomocą wiadomości tekstowej. Każdy krok dodaje zależność od zasobu, który udostępnia tę usługę. Najlepszym doświadczeniem dla użytkownika, a jednym z najmniej zależności, jest próba uzyskania tokenu dyskretnego, aby uniknąć tych dodatkowych kroków przed zażądaniem interakcji z użytkownikiem.

![Diagram przedstawiający różne usługi w ramach tożsamości firmy Microsoft, które mogą wymagać uruchomienia w celu ukończenia procesu uwierzytelniania lub autoryzowania użytkownika](media/resilience-client-app/external-dependencies.png)

Uzyskanie tokenu w trybie dyskretnym rozpoczyna się od używania prawidłowego tokenu z pamięci podręcznej tokenów aplikacji. Jeśli nie jest dostępny żaden prawidłowy token, aplikacja powinna próbować uzyskać token przy użyciu tokenu odświeżania, jeśli jest dostępny, i punktu końcowego tokenu. Jeśli żadna z tych opcji nie jest dostępna, aplikacja powinna uzyskać token przy użyciu parametru "Prompt = none". Spowoduje to użycie punktu końcowego autoryzacji, ale nie pokaże żadnego interfejsu użytkownika użytkownikowi. Jeśli tożsamość firmy Microsoft może dostarczyć token do aplikacji bez konieczności kontaktowania się z użytkownikiem, będzie to możliwe. Jeśli żadna z tych metod nie spowoduje tokenu, użytkownik będzie musiał ponownie uwierzytelnić się interaktywnie.

> [!NOTE]
> Ogólnie rzecz biorąc, aplikacje powinny unikać używania takich samych opcji, jak "login" i "Zgoda", ponieważ będą wymuszać interakcję użytkownika, nawet jeśli nie jest wymagana żadna interakcja.

## <a name="handle-service-responses-properly"></a>Obsługa prawidłowych odpowiedzi usługi

Chociaż aplikacje powinny obsługiwać wszystkie odpowiedzi na błędy, istnieją pewne odpowiedzi, które mogą wpływać na odporność. Jeśli aplikacja otrzymuje kod odpowiedzi HTTP 429, zbyt wiele żądań, tożsamość firmy Microsoft ogranicza żądania. Jeśli aplikacja będzie w dalszym ciągu wykonywać zbyt wiele żądań, nadal będzie ograniczać, uniemożliwiając aplikacji otrzymywanie tokenów. Aplikacja nie powinna próbować uzyskać tokenu ponownie, dopóki nie upłynie czas w sekundach, w polu odpowiedź Retry-After. Odebranie odpowiedzi 429 jest często oznaką, że aplikacja nie jest buforowana i wielokrotnie używa tokenów. Deweloperzy powinni sprawdzić, w jaki sposób tokeny są buforowane i ponownie używane w aplikacji.

Gdy aplikacja otrzymuje kod odpowiedzi HTTP 5xx, aplikacja nie może wprowadzać szybkiej pętli ponawiania. Gdy jest obecny, aplikacja powinna przestrzegać tego samego Retry-After obsługi, co w przypadku odpowiedzi 429. Jeśli odpowiedź nie zawiera nagłówka Retry-After, zalecamy zaimplementowanie operacji wycofywania wykładniczego przy pierwszym ponowieniu próby co najmniej 5 sekund po odpowiedzi.

Gdy żądanie przetrwania aplikacji przez czas, nie należy natychmiast ponowić próby. Zaimplementuj wycofywanie wykładnicze przy pierwszym ponowieniu próby co najmniej 5 sekund po odpowiedzi.

## <a name="evaluate-options-for-retrieving-authorization-related-information"></a>Oceń opcje pobierania informacji związanych z autoryzacją

Wiele aplikacji i interfejsów API potrzebuje określonych informacji o użytkowniku w celu podejmowania decyzji dotyczących autoryzacji. Istnieje kilka sposobów, aby aplikacja mogła uzyskać te informacje. Każda metoda ma swoje zalety i wady. Deweloperzy powinni zaważyć te rozwiązanie, aby określić, która strategia jest Najlepsza dla odporności w aplikacji.

### <a name="tokens"></a>Tokeny

Tokeny tożsamości (ID) i tokeny dostępu zawierają standardowe oświadczenia, które zawierają informacje o temacie. Są one udokumentowane w tokenach [identyfikatorów platformy tożsamości firmy Microsoft](../develop/id-tokens.md) i [tokeny dostępu platformy tożsamości firmy Microsoft](../develop/access-tokens.md). Jeśli informacje, których potrzebuje aplikacja, są już w tokenie, najbardziej wydajną techniką pobierania tych danych jest użycie oświadczeń tokenów, ponieważ spowoduje to zaoszczędzenie przewidzianych dodatkowych wywołań sieciowych w celu pobrania informacji osobno. Mniejsza liczba wywołań sieciowych oznacza wyższą ogólną odporność aplikacji.

> [!NOTE]
> Niektóre aplikacje wywołują punkt końcowy UserInfo, aby pobrać oświadczenia dotyczące uwierzytelnionego użytkownika. Informacje dostępne w tokenie identyfikatora, które mogą zostać odebrane przez aplikację, to nadzbiór informacji, które może pobrać z punktu końcowego UserInfo. Aplikacja powinna używać tokenu identyfikatora, aby uzyskać informacje o użytkowniku zamiast wywoływania punktu końcowego UserInfo.

Deweloper aplikacji może rozszerzyć standardowe oświadczenia tokenów z [opcjonalnymi oświadczeniami](../develop/active-directory-optional-claims.md). Jednym z typowych opcjonalnych roszczeń są [grupy](../develop/active-directory-optional-claims.md#configuring-groups-optional-claims). Istnieje kilka sposobów dodawania oświadczeń grup. Opcja "Grupa aplikacji" obejmuje tylko grupy przypisane do aplikacji. Opcje "wszystkie" lub "grupy zabezpieczeń" obejmują grupy ze wszystkich aplikacji w tej samej dzierżawie, które mogą dodawać wiele grup do tokenu. Ważne jest, aby oszacować efekt w Twoim przypadku, ponieważ może on Negate wydajność uzyskaną przez żądanie grup w tokenie przez spowodowanie tokenów przeładowanie, a nawet wymaganie dodatkowych wywołań, aby uzyskać pełną listę grup.

Zamiast korzystać z grup w tokenie, możesz zamiast tego użyć i dołączyć role aplikacji. Deweloperzy mogą definiować [role aplikacji](../develop/howto-add-app-roles-in-azure-ad-apps.md) dla swoich aplikacji i interfejsów API, którymi klient może zarządzać ze swojego katalogu przy użyciu portalu lub interfejsów API. Specjaliści IT mogą następnie przypisać role do różnych użytkowników i grup w celu kontrolowania, kto ma dostęp do zawartości i funkcjonalności. Po wystawieniu tokenu dla aplikacji lub interfejsu API role przypisane do użytkownika będą dostępne w ramach roszczeń ról w tokenie. Uzyskanie tych informacji bezpośrednio w tokenie może spowodować zapisanie dodatkowych wywołań interfejsów API.

Na koniec Administratorzy IT mogą również dodawać oświadczenia na podstawie określonych informacji w dzierżawie. Na przykład przedsiębiorstwo może mieć rozszerzenie mające identyfikator użytkownika specyficzny dla przedsiębiorstwa.

We wszystkich przypadkach Dodawanie informacji z katalogu bezpośrednio do tokenu może być wydajne i zwiększyć odporność aplikacji dzięki zmniejszeniu liczby zależności aplikacji. Z drugiej strony nie dotyczy żadnych problemów z odpornością od nie można uzyskać tokenu. Opcjonalne oświadczenia należy dodawać tylko dla głównych scenariuszy aplikacji. Jeśli aplikacja wymaga informacji tylko dla funkcji administratora, najlepiej, aby aplikacja mogła uzyskać te informacje tylko w razie potrzeby.

### <a name="microsoft-graph"></a>Microsoft Graph

Microsoft Graph zapewnia ujednolicony punkt końcowy interfejsu API do uzyskiwania dostępu do danych Microsoft 365, które opisują wzorce produktywności, tożsamości i zabezpieczeń w organizacji. Aplikacje korzystające z Microsoft Graph mogą potencjalnie używać dowolnych informacji w Microsoft 365 w celu podejmowania decyzji dotyczących autoryzacji.

Aplikacje wymagają tylko jednego tokenu, aby uzyskać dostęp do wszystkich Microsoft 365. Jest to bardziej odporne niż używanie starszych interfejsów API, które są specyficzne dla Microsoft 365 składników, takich jak Microsoft Exchange lub Microsoft SharePoint, gdzie są wymagane wiele tokenów.

W przypadku korzystania z Microsoft Graph interfejsów API zaleca się użycie [zestawu Microsoft Graph SDK](/graph/sdks/sdks-overview). Zestawy SDK Microsoft Graph są zaprojektowane w celu uproszczenia tworzenia wysokiej jakości, wydajnych i odpornych aplikacji, które mają dostęp Microsoft Graph.

W celu podejmowania decyzji dotyczących autoryzacji deweloperzy powinni wziąć pod uwagę, kiedy używać oświadczeń dostępnych w tokenie jako alternatywy dla niektórych wywołań Microsoft Graph. Jak wspomniano powyżej, deweloperzy mogą zażądać grup, ról aplikacji i opcjonalnych oświadczeń w ich tokenach. W odniesieniu do odporności użycie Microsoft Graph do autoryzacji wymaga dodatkowych wywołań sieciowych polegających na tożsamości firmy Microsoft (w celu uzyskania tokenu dostępu Microsoft Graph), a także Microsoft Graph samego siebie. Jeśli jednak aplikacja korzysta już z Microsoft Graph jako warstwy danych, polega na grafie autoryzacji nie jest dodatkowym ryzykiem.

## <a name="use-broker-authentication-on-mobile-devices"></a>Używanie uwierzytelniania brokera na urządzeniach przenośnych

Na urządzeniach przenośnych użycie brokera uwierzytelniania, takiego jak Microsoft Authenticator, spowoduje zwiększenie odporności. Broker dodaje korzyści powyżej tego, co jest dostępne z innymi opcjami, takimi jak przeglądarka systemowa lub osadzony widok WebView. Broker uwierzytelniania może wykorzystać [podstawowy token odświeżania](../devices/concept-primary-refresh-token.md) (PRT), który zawiera oświadczenia dotyczące użytkownika i urządzenia, i może służyć do uzyskiwania tokenów uwierzytelniania w celu uzyskania dostępu do innych aplikacji z urządzenia. Gdy PRT jest używana do żądania dostępu do aplikacji, jej urządzenie i oświadczenia MFA są zaufane przez usługę Azure AD. Zwiększa to odporność, unikając dodatkowych kroków w celu ponownego uwierzytelnienia urządzenia. Użytkownicy nie będą monitowani o wiele monitów usługi MFA na tym samym urządzeniu, dlatego zwiększanie odporności przez zredukowanie zależności od usług zewnętrznych i ulepszenie środowiska użytkownika.

![Aplikacja wywołująca tożsamość firmy Microsoft, ale wywołanie przechodzi przez pamięć podręczną tokenu oraz magazyn tokenów i brokera uwierzytelniania na urządzeniu, na którym działa aplikacja.](media/resilience-client-app/authentication-broker.png)

Uwierzytelnianie brokera jest automatycznie obsługiwane przez MSAL. Więcej informacji na temat używania uwierzytelniania obsługiwanego przez brokera można znaleźć na następujących stronach:

- [Konfigurowanie logowania jednokrotnego w systemach macOS i iOS](../develop/single-sign-on-macos-ios.md#sso-through-authentication-broker-on-ios)
- [Jak włączyć logowanie jednokrotne dla wielu aplikacji w systemie Android przy użyciu MSAL](../develop/msal-android-single-sign-on.md)

## <a name="adopt-continuous-access-evaluation"></a>Przyjmowanie oceny ciągłego dostępu

[Ciągły dostęp do wersji ewaluacyjnej (CAE)](../conditional-access/concept-continuous-access-evaluation.md) to ostatnie programowanie, które może zwiększyć bezpieczeństwo aplikacji i odporność z długimi tokenami. CAE to powstający w branży Standard, który jest opracowywany w grupie roboczej udostępnione sygnały i zdarzenia OpenID Connect Foundation. Za pomocą CAE token dostępu można odwołać na podstawie [krytycznych zdarzeń](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) i [oceny zasad](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview), zamiast polegać na krótkim okresie istnienia tokenu. W przypadku niektórych interfejsów API zasobów, ze względu na to, że ryzyko i zasady są oceniane w czasie rzeczywistym, CAE może znacząco zwiększyć okres istnienia tokenu do 28 godzin. Jako że interfejsy API zasobów i aplikacje przyjmują CAE, tożsamość firmy Microsoft będzie mogła wydawać tokeny dostępu revocable i są ważne przez dłuższy czas. Te tokeny długotrwałe zostaną uaktywnione ponownie przez MSAL.

Choć CAE jest w wczesnych fazach, istnieje możliwość [tworzenia aplikacji klienckich, które będą korzystać z usługi CAE](../develop/app-resilience-continuous-access-evaluation.md) , gdy zasoby (interfejsy API) używane przez aplikację przyjmują CAE. Ponieważ więcej zasobów przyjmuje CAE, aplikacja będzie mogła również uzyskiwać tokeny z włączonym CAE dla tych zasobów. Interfejs Microsoft Graph API i [zestawy sdk Microsoft Graph](/graph/sdks/sdks-overview), zobaczą CAE możliwości wczesne 2021. Jeśli chcesz wziąć udział w publicznej wersji zapoznawczej Microsoft Graph z usługą CAE, możesz poinformować nas, że interesuje Cię tutaj: [https://aka.ms/GraphCAEPreview](https://aka.ms/GraphCAEPreview) .

W przypadku tworzenia interfejsów API zasobów zachęcamy do wzięcia udziału w [wspólnych sygnałach i zdarzeniach wg](https://openid.net/wg/sse/). Pracujemy nad tą grupą, aby umożliwić udostępnianie zdarzeń zabezpieczeń między tożsamościami i dostawcami zasobów firmy Microsoft.

## <a name="next-steps"></a>Następne kroki

- [Jak używać interfejsów API z włączoną funkcją oceny dostępu ciągłego w aplikacjach](../develop/app-resilience-continuous-access-evaluation.md)
- [Tworzenie odporności na aplikacje demona](resilience-daemon-app.md)
- [Tworzenie odporności w infrastrukturze zarządzania tożsamościami i dostępem](resilience-in-infrastructure.md)
- [Odporność na kompilacje w systemach CIAM](resilience-b2c.md)