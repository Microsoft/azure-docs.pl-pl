---
title: Jak używać interfejsów API z włączoną funkcją ciągłej oceny dostępu w aplikacjach | Azure
titleSuffix: Microsoft identity platform
description: Jak zwiększyć bezpieczeństwo i odporność aplikacji, dodając obsługę ciągłej oceny dostępu, zapewniając długotrwałe tokeny dostępu, które można odwołać na podstawie krytycznych zdarzeń i oceny zasad.
services: active-directory
author: knicholasa
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 10/06/2020
ms.author: nichola
ms.reviewer: ''
ms.openlocfilehash: f6ce792b3db0100d7356884bbc6ee2696580df10
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97652062"
---
# <a name="how-to-use-continuous-access-evaluation-enabled-apis-in-your-applications"></a>Jak używać interfejsów API z włączoną funkcją oceny dostępu ciągłego w aplikacjach

[Ciągły dostęp do wersji ewaluacyjnej](../conditional-access/concept-continuous-access-evaluation.md) (CAE) to powstający w branży Standard, który umożliwia odwoływanie tokenów dostępu na podstawie [krytycznych zdarzeń](../conditional-access/concept-continuous-access-evaluation.md#critical-event-evaluation) i [oceny zasad](../conditional-access/concept-continuous-access-evaluation.md#conditional-access-policy-evaluation-preview) , a nie poleganie na wygaśnięciu tokenu na podstawie okresu istnienia. W przypadku niektórych interfejsów API zasobów, ze względu na to, że ryzyko i zasady są oceniane w czasie rzeczywistym, może to zwiększyć okres istnienia tokenu do 28 godzin. Te tokeny długotrwałe będą odświeżane w usłudze Microsoft Authentication Library (MSAL), co zwiększa odporność aplikacji.

W tym artykule opisano sposób używania interfejsów API z obsługą CAE w aplikacjach.

## <a name="implementation-considerations"></a>Istotne informacje dotyczące implementacji

Aby można było korzystać z oceny dostępu ciągłego, zarówno aplikacja, jak i interfejs API zasobów, do którego uzyskuje dostęp, muszą być włączone CAE. Jednak przygotowanie kodu do korzystania z CAE z włączonym zasobem nie uniemożliwi używania interfejsów API, które nie są CAE włączone.

Jeśli interfejs API zasobów implementuje CAE, a aplikacja deklaruje, że może obsłużyć CAE, aplikacja uzyska tokeny CAE dla tego zasobu. Z tego powodu, Jeśli deklarujesz aplikację CAE gotowe, aplikacja musi obsłużyć wyzwanie żądania CAE dla wszystkich interfejsów API zasobów, które akceptują tokeny dostępu do tożsamości firmy Microsoft. Jeśli odpowiedzi CAE nie są obsługiwane w ramach tych wywołań interfejsu API, aplikacja może zakończyć się w pętli ponawianie wywołania interfejsu API z tokenem, który jest nadal w zwróconym cykl życia tokenu, ale został odwołany z powodu CAE.

## <a name="the-code"></a>Kod

Pierwszym krokiem jest dodanie kodu do obsługi odpowiedzi z interfejsu API zasobu odrzucanie wywołania z powodu CAE. W przypadku CAE interfejsy API zwróci stan 401 i nagłówek WWW-Authenticate, gdy token dostępu został odwołany lub interfejs API wykryje zmianę w używanym adresie IP. Nagłówek WWW-Authenticate zawiera wyzwanie dla oświadczeń, za pomocą którego aplikacja może uzyskać nowy token dostępu.

Na przykład:

```console
HTTP 401; Unauthorized
WWW-Authenticate=Bearer
  authorization_uri="https://login.windows.net/common/oauth2/authorize",
  error="insufficient_claims",
  claims="eyJhY2Nlc3NfdG9rZW4iOnsibmJmIjp7ImVzc2VudGlhbCI6dHJ1ZSwgInZhbHVlIjoiMTYwNDEwNjY1MSJ9fX0="
```

Aplikacja sprawdzi się pod kątem:

- wywołanie interfejsu API zwracające stan 401
- istnienie nagłówka WWW-Authenticate zawierającego:
  - parametr "Error" z wartością "insufficient_claims"
  - parametr "oświadczenia"

Po spełnieniu tych warunków aplikacja może wyodrębnić i zdekodować wyzwanie oświadczeń.

```csharp
if (APIresponse.IsSuccessStatusCode)
{
    // ...
}
else
{
    if (APIresponse.StatusCode == System.Net.HttpStatusCode.Unauthorized
        && APIresponse.Headers.WwwAuthenticate.Any())
    {
        AuthenticationHeaderValue bearer = APIresponse.Headers.WwwAuthenticate.First
            (v => v.Scheme == "Bearer");
        IEnumerable<string> parameters = bearer.Parameter.Split(',').Select(v => v.Trim()).ToList();
        var error = GetParameter(parameters, "error");

        if (null != error && "insufficient_claims" == error)
        {
            var claimChallengeParameter = GetParameter(parameters, "claims");
            if (null != claimChallengeParameter)
            {
                var claimChallengebase64Bytes = System.Convert.FromBase64String(claimChallengeParameter);
                var claimChallenge = System.Text.Encoding.UTF8.GetString(claimChallengebase64Bytes);
                var newAccessToken = await GetAccessTokenWithClaimChallenge(scopes, claimChallenge);
```

Następnie aplikacja będzie używać wyzwania oświadczeń, aby uzyskać nowy token dostępu dla zasobu.

```csharp
try
{
    authResult = await _clientApp.AcquireTokenSilent(scopes, firstAccount)
        .WithClaims(claimChallenge)
        .ExecuteAsync()
        .ConfigureAwait(false);
}
catch (MsalUiRequiredException)
{
    try
    {
        authResult = await _clientApp.AcquireTokenInteractive(scopes)
            .WithClaims(claimChallenge)
            .WithAccount(firstAccount)
            .ExecuteAsync()
            .ConfigureAwait(false);
    }
    // ...
```

Gdy aplikacja będzie gotowa do obsługi żądania roszczeń zwróconego przez zasób z włączonym CAE, możesz powiedzieć tożsamość firmy Microsoft, że aplikacja jest CAE gotowa. Aby to zrobić w aplikacji MSAL, skompiluj klienta publicznego przy użyciu funkcji klienta "CP1".

```csharp
_clientApp = PublicClientApplicationBuilder.Create(App.ClientId)
    .WithDefaultRedirectUri()
    .WithAuthority(authority)
    .WithClientCapabilities(new [] {"cp1"})
    .Build();
```

Możesz przetestować aplikację, logując się do aplikacji przy użyciu Azure Portal do odwoływania sesji użytkownika. Następnym razem, gdy aplikacja wywoła interfejs API z włączonym CAE, użytkownik zostanie poproszony o ponowne uwierzytelnienie.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej, zobacz [ocenę dostępu ciągłego](../conditional-access/concept-continuous-access-evaluation.md).
