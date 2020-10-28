---
title: Generowanie żądań HTTP przy użyciu usługi API Management
description: Dowiedz się, jak używać zasad żądań i odpowiedzi w API Management do wywoływania usług zewnętrznych z poziomu interfejsu API
services: api-management
documentationcenter: ''
author: vladvino
manager: erikre
editor: ''
ms.assetid: 4539c0fa-21ef-4b1c-a1d4-d89a38c242fa
ms.service: api-management
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 12/15/2016
ms.author: apimpm
ms.openlocfilehash: 2f4bd040d7e5858fd561444f56dbce7b3f940d9a
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92742388"
---
# <a name="using-external-services-from-the-azure-api-management-service"></a>Korzystanie z usług zewnętrznych z usługi Azure API Management
Zasady dostępne w usłudze Azure API Management mogą wykonywać szeroką gamę przydatnych działań na przykład na żądanie przychodzące, odpowiedź wychodzącą i podstawowe informacje o konfiguracji. Jednak możliwość współpracy z usługami zewnętrznymi z poziomu zasad API Management otwiera dużo więcej szans.

Wcześniej dowiesz się, jak korzystać z [usługi Azure Event Hub na potrzeby rejestrowania, monitorowania i analizy](api-management-log-to-eventhub-sample.md). W tym artykule przedstawiono zasady, które umożliwiają współpracującie z dowolną zewnętrzną usługą opartą na protokole HTTP. Te zasady mogą służyć do wyzwalania zdarzeń zdalnych lub do pobierania informacji, które są używane do manipulowania pierwotnym żądaniem i odpowiedzi w jakiś sposób.

## <a name="send-one-way-request"></a>Wyślij-jednokierunkowe-żądanie
Prawdopodobnie najprostsza interakcja zewnętrzna jest stylem żądania, który pozwala na powiadamianie usługi zewnętrznej o jakimś rodzaju ważnych wydarzeniach. Zasady przepływu sterowania `choose` mogą służyć do wykrywania dowolnego rodzaju warunku, który Cię interesuje.  Jeśli warunek jest spełniony, można wykonać zewnętrzne żądanie HTTP przy użyciu zasad [wysyłania jednokierunkowego](./api-management-advanced-policies.md#SendOneWayRequest) . Może to być żądanie do systemu obsługi komunikatów, takiego jak HipChat lub zapasowy, lub interfejs API poczty, taki jak SendGrid lub MailChimp, lub dla krytycznych zdarzeń pomocy technicznej, takich jak usługi PagerDuty. Wszystkie te systemy obsługi komunikatów mają proste interfejsy API protokołu HTTP, które mogą być wywoływane.

### <a name="alerting-with-slack"></a>Alerty z zapasem czasu
W poniższym przykładzie pokazano, jak wysłać komunikat do pokoju rozmów zapasowych, jeśli kod stanu odpowiedzi HTTP jest większy lub równy 500. Błąd zakresu 500 wskazuje na problem z interfejsem API zaplecza, który nie może zostać rozwiązany przez klienta interfejsu API. Zwykle wymaga pewnego rodzaju interwencji na API Management część.  

```xml
<choose>
    <when condition="@(context.Response.StatusCode >= 500)">
      <send-one-way-request mode="new">
        <set-url>https://hooks.slack.com/services/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</set-url>
        <set-method>POST</set-method>
        <set-body>@{
                return new JObject(
                        new JProperty("username","APIM Alert"),
                        new JProperty("icon_emoji", ":ghost:"),
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",
                                                context.Request.Method,
                                                context.Request.Url.Path + context.Request.Url.QueryString,
                                                context.Request.Url.Host,
                                                context.Response.StatusCode,
                                                context.Response.StatusReason,
                                                context.User.Email
                                                ))
                        ).ToString();
            }</set-body>
      </send-one-way-request>
    </when>
</choose>
```

Zapasowy element ma koncepcję przychodzących elementów sieci Web. Podczas konfigurowania przychodzącego elementu Hook sieci Web funkcja zapasowa generuje specjalny adres URL, dzięki czemu można wykonać proste żądanie POST i przekazać komunikat do kanału zapasowego. Utworzona treść JSON jest oparta na formacie zdefiniowanym za pomocą zapasu.

![Element webhook w sieci Web](./media/api-management-sample-send-request/api-management-slack-webhook.png)

### <a name="is-fire-and-forget-good-enough"></a>Czy jest to dobre i zapomniane?
W przypadku korzystania z stylu żądania "Uruchom i zapomnij" istnieją pewne kompromisy. Jeśli z jakiegoś powodu żądanie nie powiedzie się, błąd nie zostanie zgłoszony. W tej sytuacji złożoność posiadania dodatkowego systemu raportowania awarii i dodatkowego kosztu wydajności oczekiwania na odpowiedź nie jest uzasadnione. W przypadku scenariuszy, w których konieczna jest kontrola odpowiedzi, zasady [wysyłania żądań](./api-management-advanced-policies.md#SendRequest) są lepszym rozwiązaniem.

## <a name="send-request"></a>Send-Request
`send-request`Zasady umożliwiają użycie usługi zewnętrznej do wykonywania złożonych funkcji przetwarzania i zwracania danych do usługi API Management, która może być używana do dalszej przetwarzania zasad.

### <a name="authorizing-reference-tokens"></a>Autoryzowanie tokenów odwołania
Główną funkcją API Management jest ochrona zasobów zaplecza. Jeśli serwer autoryzacji używany przez interfejs API tworzy [tokeny JWT](https://jwt.io/) w ramach przepływu OAuth2, jako [Azure Active Directory](../active-directory/hybrid/whatis-hybrid-identity.md) , można użyć `validate-jwt` zasad, aby zweryfikować ważność tokenu. Niektóre serwery autoryzacji tworzą elementy o nazwie [tokeny odwołania](https://leastprivilege.com/2015/11/25/reference-tokens-and-introspection/) , których nie można zweryfikować bez wywołania zwrotnego do serwera autoryzacji.

### <a name="standardized-introspection"></a>Standardowe introspekcji
W przeszłości nie było standardowego sposobu weryfikowania tokenu odwołania z serwerem autoryzacji. Jednak ostatnio zaproponowana standardowa [Specyfikacja RFC 7662](https://tools.ietf.org/html/rfc7662) została opublikowana przez grupę IETF, która definiuje, w jaki sposób serwer zasobów może weryfikować ważność tokenu.

### <a name="extracting-the-token"></a>Wyodrębnianie tokenu
Pierwszym krokiem jest wyodrębnienie tokenu z nagłówka autoryzacji. Wartość nagłówka powinna być sformatowana przy użyciu `Bearer` schematu autoryzacji, pojedynczej spacji, a następnie tokenu autoryzacji zgodnie z [dokumentem RFC 6750](https://tools.ietf.org/html/rfc6750#section-2.1). Niestety, istnieją przypadki, w których schemat autoryzacji został pominięty. Aby uwzględnić to podczas analizowania, API Management dzieli wartość nagłówka w miejscu i wybiera ostatni ciąg z zwróconej tablicy ciągów. Zapewnia to obejście dla nieprawidłowo sformatowanych nagłówków autoryzacji.

```xml
<set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />
```

### <a name="making-the-validation-request"></a>Wykonywanie żądania weryfikacji
Gdy API Management ma token autoryzacji, API Management może wykonać żądanie zweryfikowania tokenu. Specyfikacja RFC 7662 wywołuje ten proces introspekcji i wymaga `POST` formularza HTML do zasobu introspekcji. Formularz HTML musi zawierać co najmniej parę klucz/wartość z kluczem `token` . To żądanie należy również uwierzytelnić na serwerze autoryzacji, aby upewnić się, że złośliwi klienci nie będą mogli nawiązać prawidłowego tokenu.

```xml
<send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
  <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
  <set-method>POST</set-method>
  <set-header name="Authorization" exists-action="override">
    <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
  </set-header>
  <set-header name="Content-Type" exists-action="override">
    <value>application/x-www-form-urlencoded</value>
  </set-header>
  <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
</send-request>
```

### <a name="checking-the-response"></a>Sprawdzanie odpowiedzi
Ten `response-variable-name` atrybut służy do udzielania dostępu do zwróconej odpowiedzi. Nazwa zdefiniowana w tej właściwości może być używana jako klucz w `context.Variables` słowniku w celu uzyskania dostępu do `IResponse` obiektu.

Z obiektu Response można pobrać treść i dokument RFC 7622 informuje API Management, że odpowiedź musi być obiektem JSON i musi zawierać co najmniej właściwość o nazwie `active` , która jest wartością logiczną. Gdy `active` ma wartość true, token jest uznawany za ważny.

Alternatywnie, jeśli serwer autoryzacji nie zawiera pola "aktywne", aby wskazać, czy token jest prawidłowy, użyj narzędzia takiego jak Poster, aby określić, jakie właściwości są ustawiane w prawidłowym tokenie. Na przykład jeśli prawidłowa odpowiedź tokenu zawiera właściwość o nazwie "expires_in", sprawdź, czy ta nazwa właściwości istnieje na serwerze autoryzacji w następujący sposób:

<, gdy warunek = "@ ((IResponse) kontekst. Zmienne ["tokenstate"]). Body.As <JObject> (). Właściwość ("expires_in") = = null) ">

### <a name="reporting-failure"></a>Raportowanie niepowodzeń
Zasad można użyć `<choose>` do wykrycia, czy token jest nieprawidłowy, a jeśli tak, zwracają odpowiedź 401.

```xml
<choose>
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
    <return-response response-variable-name="existing response variable">
      <set-status code="401" reason="Unauthorized" />
      <set-header name="WWW-Authenticate" exists-action="override">
        <value>Bearer error="invalid_token"</value>
      </set-header>
    </return-response>
  </when>
</choose>
```

Zgodnie z opisem w [dokumencie RFC 6750](https://tools.ietf.org/html/rfc6750#section-3) , który opisuje sposób `bearer` używania tokenów, API Management zwraca również `WWW-Authenticate` nagłówek z odpowiedzią 401. WWW-Authenticate jest przeznaczony do nakazuje klientowi utworzenie prawidłowo autoryzowanego żądania. Ze względu na szeroką gamę metod OAuth2 Framework trudno jest komunikować się ze wszystkimi wymaganymi informacjami. Na szczęście istnieją wysiłki, aby pomóc [klientom w ustaleniu, jak prawidłowo autoryzować żądania do serwera zasobów](https://tools.ietf.org/html/draft-jones-oauth-discovery-00).

### <a name="final-solution"></a>Ostateczne rozwiązanie
Na końcu uzyskasz następujące zasady:

```xml
<inbound>
  <!-- Extract Token from Authorization header parameter -->
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />

  <!-- Send request to Token Server to validate token (see RFC 7662) -->
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>
    <set-method>POST</set-method>
    <set-header name="Authorization" exists-action="override">
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>
    </set-header>
    <set-header name="Content-Type" exists-action="override">
      <value>application/x-www-form-urlencoded</value>
    </set-header>
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>
  </send-request>

  <choose>
          <!-- Check active property in response -->
          <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">
              <!-- Return 401 Unauthorized with http-problem payload -->
              <return-response response-variable-name="existing response variable">
                  <set-status code="401" reason="Unauthorized" />
                  <set-header name="WWW-Authenticate" exists-action="override">
                      <value>Bearer error="invalid_token"</value>
                  </set-header>
              </return-response>
          </when>
      </choose>
  <base />
</inbound>
```

Jest to tylko jeden z wielu przykładów tego, jak `send-request` zasady mogą służyć do integrowania przydatnych usług zewnętrznych w procesie żądań i odpowiedzi przepływających przez usługę API Management.

## <a name="response-composition"></a>Kompozycja odpowiedzi
`send-request`Zasady te mogą służyć do ulepszania podstawowego żądania do systemu zaplecza, jak pokazano w poprzednim przykładzie lub można go użyć jako pełnego zastąpienia dla wywołania zaplecza. Korzystając z tej techniki, można łatwo tworzyć zasoby złożone, które są agregowane z wielu różnych systemów.

### <a name="building-a-dashboard"></a>Kompilowanie pulpitu nawigacyjnego
Czasami chcesz mieć możliwość uwidaczniania informacji, które istnieją w wielu systemach zaplecza, na przykład w celu kierowania pulpitu nawigacyjnego. Wskaźniki KPI pochodzą ze wszystkich różnych zapleczów, ale wolisz, aby nie zapewnić bezpośredniego dostępu do nich i byłoby to dobre, jeśli wszystkie informacje można pobrać w jednym żądaniu. Być może niektóre informacje o zapleczu wymagają pewnego wycinka i grupowanie, a najpierw zostanie nieco oczyszczone. Możliwość buforowania tego zasobu złożonego jest przydatna do zredukowania obciążenia zaplecza, ponieważ użytkownicy mają wykonywaćy do wydzielenia przez siebie, aby sprawdzić, czy ich przeprowadzenie może ulec zmianie.    

### <a name="faking-the-resource"></a>Faking zasób
Pierwszym krokiem tworzenia zasobu pulpitu nawigacyjnego jest skonfigurowanie nowej operacji w Azure Portal. Jest to operacja zastępcza użyta do skonfigurowania zasad kompozycji w celu skompilowania zasobu dynamicznego.

![Operacja pulpitu nawigacyjnego](./media/api-management-sample-send-request/api-management-dashboard-operation.png)

### <a name="making-the-requests"></a>Wykonywanie żądań
Po utworzeniu operacji można skonfigurować zasady przeznaczone dla tej operacji. 

![Zrzut ekranu przedstawiający ekran zakresu zasad.](./media/api-management-sample-send-request/api-management-dashboard-policy.png)

Pierwszym krokiem jest wyodrębnienie wszystkich parametrów zapytania z przychodzącego żądania, dzięki czemu można je przesłać dalej do zaplecza. W tym przykładzie pulpit nawigacyjny wyświetla informacje w oparciu o pewien czas i dlatego ma `fromDate` `toDate` parametr i. Możesz użyć zasad, `set-variable` Aby wyodrębnić informacje z adresu URL żądania.

```xml
<set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
<set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">
```

Po uzyskaniu tych informacji można wykonać żądania do wszystkich systemów zaplecza. Każde żądanie konstruuje nowy adres URL z informacjami o parametrach i wywołuje odpowiedni serwer i zapisuje odpowiedź w zmiennej kontekstowej.

```xml
<send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
  <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
  <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>

<send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
<set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
  <set-method>GET</set-method>
</send-request>
```

Te żądania są wykonywane w kolejności, która nie jest idealnym rozwiązaniem. 

### <a name="responding"></a>Zwan
Do skonstruowania odpowiedzi złożonej można użyć zasad [powrotu odpowiedzi](./api-management-advanced-policies.md#ReturnResponse) . `set-body`Element może użyć wyrażenia, aby utworzyć nowy `JObject` ze wszystkimi reprezentacjami składników osadzonych jako właściwości.

```xml
<return-response response-variable-name="existing response variable">
  <set-status code="200" reason="OK" />
  <set-header name="Content-Type" exists-action="override">
    <value>application/json</value>
  </set-header>
  <set-body>
    @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                  new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                  new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                  new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                  ).ToString())
  </set-body>
</return-response>
```

Pełne zasady wyglądają następująco:

```xml
<policies>
    <inbound>

  <set-variable name="fromDate" value="@(context.Request.Url.Query["fromDate"].Last())">
  <set-variable name="toDate" value="@(context.Request.Url.Query["toDate"].Last())">

    <send-request mode="new" response-variable-name="revenuedata" timeout="20" ignore-error="true">
      <set-url>@($"https://accounting.acme.com/salesdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="materialdata" timeout="20" ignore-error="true">
      <set-url>@($"https://inventory.acme.com/materiallevels?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="throughputdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/throughput?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <send-request mode="new" response-variable-name="accidentdata" timeout="20" ignore-error="true">
    <set-url>@($"https://production.acme.com/accidentdata?from={(string)context.Variables["fromDate"]}&to={(string)context.Variables["fromDate"]}")"</set-url>
      <set-method>GET</set-method>
    </send-request>

    <return-response response-variable-name="existing response variable">
      <set-status code="200" reason="OK" />
      <set-header name="Content-Type" exists-action="override">
        <value>application/json</value>
      </set-header>
      <set-body>
        @(new JObject(new JProperty("revenuedata",((IResponse)context.Variables["revenuedata"]).Body.As<JObject>()),
                      new JProperty("materialdata",((IResponse)context.Variables["materialdata"]).Body.As<JObject>()),
                      new JProperty("throughputdata",((IResponse)context.Variables["throughputdata"]).Body.As<JObject>()),
                      new JProperty("accidentdata",((IResponse)context.Variables["accidentdata"]).Body.As<JObject>())
                      ).ToString())
      </set-body>
    </return-response>
    </inbound>
    <backend>
        <base />
    </backend>
    <outbound>
        <base />
    </outbound>
</policies>
```

W konfiguracji operacji symbolu zastępczego można skonfigurować zasób pulpitu nawigacyjnego, który ma zostać zbuforowany przez co najmniej godzinę. 

## <a name="summary"></a>Podsumowanie
Usługa Azure API Management zapewnia elastyczne zasady, które można wybiórczo stosować do ruchu HTTP i umożliwiają składanie usług zaplecza. Bez względu na to, czy chcesz ulepszyć bramę interfejsu API za pomocą funkcji alertów, weryfikacji, możliwości sprawdzania poprawności lub tworzyć nowe zasoby złożone na podstawie wielu usług zaplecza, `send-request` i powiązane zasady otwierają na świecie możliwości.
