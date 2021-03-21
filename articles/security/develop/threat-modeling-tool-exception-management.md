---
title: Zarządzanie wyjątkami-Microsoft Threat Modeling Tool na platformie Azure | Microsoft Docs
description: Więcej informacji na temat zarządzania wyjątkami znajduje się w Threat Modeling Tool. Zobacz informacje o ograniczeniach i Wyświetl przykłady kodu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: 41532e554623c47e9728c6ccab92d99500e42021
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517431"
---
# <a name="security-frame-exception-management--mitigations"></a>Ramka zabezpieczeń: Zarządzanie wyjątkami | Środki zaradcze 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **WCF** | <ul><li>[WCF — nie dołączaj węzła serviceDebug w pliku konfiguracyjnym](#servicedebug)</li><li>[WCF — nie dołączaj węzła ServiceMetadata w pliku konfiguracji](#servicemetadata)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że w interfejsie API sieci Web ASP.NET zainstalowano odpowiednią obsługę wyjątków](#exception)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Nie ujawniaj szczegółów zabezpieczeń w komunikatach o błędach](#messages)</li><li>[Zaimplementuj domyślną stronę obsługi błędów](#default)</li><li>[Ustaw metodę wdrażania na sprzedaż detaliczną w usługach IIS](#deployment)</li><li>[Wyjątki powinny być bezpiecznie bezpieczne](#fail)</li></ul> |

## <a name="wcf--do-not-include-servicedebug-node-in-configuration-file"></a><a id="servicedebug"></a>WCF — nie dołączaj węzła serviceDebug w pliku konfiguracyjnym

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_debug_information) |
| **Kroki** | Usługi Windows Communication Framework (WCF) można skonfigurować tak, aby ujawniać informacje o debugowaniu. Informacje o debugowaniu nie powinny być używane w środowiskach produkcyjnych. `<serviceDebug>`Tag określa, czy funkcja informacji debugowania jest włączona dla usługi WCF. Jeśli atrybut includeExceptionDetailInFaults ma wartość true, informacje o wyjątku z aplikacji zostaną zwrócone do klientów. Osoby atakujące mogą korzystać z dodatkowych informacji uzyskanych od debugowania danych wyjściowych w celu instalacji ataków przeznaczonych dla platformy, bazy danych lub innych zasobów używanych przez aplikację. |

### <a name="example"></a>Przykład
Następujący plik konfiguracji zawiera `<serviceDebug>` tag: 
```
<configuration> 
<system.serviceModel> 
<behaviors> 
<serviceBehaviors> 
<behavior name=""MyServiceBehavior""> 
<serviceDebug includeExceptionDetailInFaults=""True"" httpHelpPageEnabled=""True""/> 
... 
```
Wyłącz informacje o debugowaniu w usłudze. Można to osiągnąć przez usunięcie `<serviceDebug>` znacznika z pliku konfiguracyjnego aplikacji. 

## <a name="wcf--do-not-include-servicemetadata-node-in-configuration-file"></a><a id="servicemetadata"></a>WCF — nie dołączaj węzła ServiceMetadata w pliku konfiguracji

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Ogólne, NET Framework 3 |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_service_enumeration) |
| **Kroki** | Publiczne udostępnianie informacji o usłudze może zapewnić atakującemu cenne wgląd w sposób, w jaki mogą wykorzystać usługę. `<serviceMetadata>`Tag włącza funkcję publikowania metadanych. Metadane usługi mogą zawierać poufne informacje, które nie powinny być publicznie dostępne. Co najmniej należy zezwolić zaufanym użytkownikom na dostęp do metadanych i upewnić się, że niepotrzebne informacje nie są ujawniane. Jeszcze lepiej można całkowicie wyłączyć możliwość publikowania metadanych. Bezpieczna konfiguracja usługi WCF nie będzie zawierać `<serviceMetadata>` znacznika. |

## <a name="ensure-that-proper-exception-handling-is-done-in-aspnet-web-api"></a><a id="exception"></a>Upewnij się, że w interfejsie API sieci Web ASP.NET zainstalowano odpowiednią obsługę wyjątków

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC 5, MVC 6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Obsługa wyjątków w interfejsie API sieci web ASP.NET](https://www.asp.net/web-api/overview/error-handling/exception-handling), [Walidacja modelu w interfejsie api sieci Web ASP.NET](https://www.asp.net/web-api/overview/formats-and-model-binding/model-validation-in-aspnet-web-api) |
| **Kroki** | Domyślnie większość nieprzechwyconych wyjątków w interfejsie Web API ASP.NET są tłumaczone na odpowiedź HTTP z kodem stanu `500, Internal Server Error`|

### <a name="example"></a>Przykład
Aby kontrolować kod stanu zwracany przez interfejs API, `HttpResponseException` można użyć, jak pokazano poniżej: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        throw new HttpResponseException(HttpStatusCode.NotFound);
    }
    return item;
}
```

### <a name="example"></a>Przykład
Aby uzyskać więcej kontroli nad odpowiedzią na wyjątek, `HttpResponseMessage` można użyć klasy, jak pokazano poniżej: 
```csharp
public Product GetProduct(int id)
{
    Product item = repository.Get(id);
    if (item == null)
    {
        var resp = new HttpResponseMessage(HttpStatusCode.NotFound)
        {
            Content = new StringContent(string.Format("No product with ID = {0}", id)),
            ReasonPhrase = "Product ID Not Found"
        }
        throw new HttpResponseException(resp);
    }
    return item;
}
```
Aby przechwytywać Nieobsłużone wyjątki, które nie są typu `HttpResponseException` , można użyć filtrów wyjątków. Filtry wyjątków implementują `System.Web.Http.Filters.IExceptionFilter` interfejs. Najprostszym sposobem pisania filtru wyjątków jest pochodna `System.Web.Http.Filters.ExceptionFilterAttribute` klasy i przesłonięcie metody onException. 

### <a name="example"></a>Przykład
Oto filtr, który konwertuje `NotImplementedException` wyjątki na kod stanu HTTP `501, Not Implemented` : 
```csharp
namespace ProductStore.Filters
{
    using System;
    using System.Net;
    using System.Net.Http;
    using System.Web.Http.Filters;

    public class NotImplExceptionFilterAttribute : ExceptionFilterAttribute 
    {
        public override void OnException(HttpActionExecutedContext context)
        {
            if (context.Exception is NotImplementedException)
            {
                context.Response = new HttpResponseMessage(HttpStatusCode.NotImplemented);
            }
        }
    }
}
```

Istnieje kilka sposobów rejestrowania filtru wyjątków interfejsu API sieci Web:
- Według akcji
- Według kontrolera
- Globalnie

### <a name="example"></a>Przykład
Aby zastosować filtr do określonej akcji, Dodaj filtr jako atrybut do akcji: 
```csharp
public class ProductsController : ApiController
{
    [NotImplExceptionFilter]
    public Contact GetContact(int id)
    {
        throw new NotImplementedException("This method is not implemented");
    }
}
```
### <a name="example"></a>Przykład
Aby zastosować filtr do wszystkich akcji w `controller` , Dodaj filtr jako atrybut do `controller` klasy: 

```csharp
[NotImplExceptionFilter]
public class ProductsController : ApiController
{
    // ...
}
```

### <a name="example"></a>Przykład
Aby zastosować filtr globalnie do wszystkich kontrolerów internetowego interfejsu API, Dodaj wystąpienie filtru do `GlobalConfiguration.Configuration.Filters` kolekcji. Filtry wyjątków w tej kolekcji mają zastosowanie do dowolnej akcji kontrolera interfejsu API sieci Web. 
```csharp
GlobalConfiguration.Configuration.Filters.Add(
    new ProductStore.NotImplExceptionFilterAttribute());
```

### <a name="example"></a>Przykład
Aby można było zweryfikować model, stan modelu może być przekazana do metody CreateErrorResponse, jak pokazano poniżej: 
```csharp
public HttpResponseMessage PostProduct(Product item)
{
    if (!ModelState.IsValid)
    {
        return Request.CreateErrorResponse(HttpStatusCode.BadRequest, ModelState);
    }
    // Implementation not shown...
}
```

Zapoznaj się z linkami w sekcji References, aby uzyskać dodatkowe informacje o wyjątkowej obsłudze i weryfikacji modelu w interfejsie API sieci Web ASP.NET 

## <a name="do-not-expose-security-details-in-error-messages"></a><a id="messages"></a>Nie ujawniaj szczegółów zabezpieczeń w komunikatach o błędach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Ogólne komunikaty o błędach są podawane bezpośrednio użytkownikowi, bez uwzględnienia poufnych danych aplikacji. Przykładowe dane poufne to:</p><ul><li>Nazwy serwerów</li><li>Parametry połączeń</li><li>Nazw użytkowników</li><li>Hasła</li><li>Procedury SQL</li><li>Szczegóły dynamicznych błędów SQL</li><li>Ślad stosu i wiersze kodu</li><li>Zmienne przechowywane w pamięci</li><li>Lokalizacje dysków i folderów</li><li>Punkty instalacji aplikacji</li><li>Ustawienia konfiguracji hosta</li><li>Inne szczegóły wewnętrznej aplikacji</li></ul><p>Zablokowanie wszystkich błędów w aplikacji i dostarczenie ogólnych komunikatów o błędach, a także włączenie niestandardowych błędów w ramach usług IIS uniemożliwi ujawnienie informacji. SQL Server baza danych i obsługa wyjątków platformy .NET, między innymi architektura obsługi błędów, są szczególnie pełne i niezwykle przydatne w przypadku złośliwego użytkownika profilowania aplikacji. Nie wyświetlaj bezpośrednio zawartości klasy pochodzącej od klasy wyjątków .NET i upewnij się, że masz prawidłową obsługę wyjątków, aby nieoczekiwany wyjątek nie został przypadkowo podniesiony bezpośrednio do użytkownika.</p><ul><li>Dostarczanie ogólnych komunikatów o błędach bezpośrednio do użytkownika, które są określone przez streszczenie szczegółowych informacji bezpośrednio w komunikacie wyjątku/komunikatu o błędzie</li><li>Nie wyświetlaj zawartości klasy wyjątku platformy .NET bezpośrednio dla użytkownika</li><li>Zalewka wszystkich komunikatów o błędach i w razie potrzeby poinformowania użytkownika za pośrednictwem ogólnego komunikatu o błędzie wysyłanego do klienta aplikacji</li><li>Nie ujawniaj zawartości klasy wyjątku bezpośrednio użytkownikowi, szczególnie wartości zwracanej z `.ToString()` , lub wartości właściwości Message lub ślad stosu. Bezpiecznie Rejestruj te informacje i wyświetlaj bardziej niewielkiej ilości komunikat dla użytkownika</li></ul>|

## <a name="implement-default-error-handling-page"></a><a id="default"></a>Zaimplementuj domyślną stronę obsługi błędów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Okno dialogowe Edytowanie ustawień stron błędów ASP.NET](/previous-versions/windows/it-pro/windows-server-2008-R2-and-2008/dd569096(v=ws.10)) |
| **Kroki** | <p>Gdy aplikacja ASP.NET kończy się niepowodzeniem i powoduje błąd wewnętrzny serwera HTTP/1. x 500 lub Konfiguracja funkcji (na przykład filtrowanie żądań) uniemożliwia wyświetlenie strony, zostanie wygenerowany komunikat o błędzie. Administratorzy mogą określić, czy aplikacja powinna wyświetlać przyjazny komunikat do klienta, szczegółowy komunikat o błędzie do klienta lub szczegółowy komunikat o błędzie tylko do hosta lokalnego. `<customErrors>`Tag w web.config ma trzy tryby:</p><ul><li>**W dniu:** Określa, że błędy niestandardowe są włączone. Jeśli żaden atrybut defaultRedirect nie zostanie określony, użytkownicy zobaczą błąd ogólny. Błędy niestandardowe są wyświetlane dla klientów zdalnych i do hosta lokalnego.</li><li>**Wyłączone:** Określa, że błędy niestandardowe są wyłączone. Szczegółowe błędy ASP.NET są widoczne dla klientów zdalnych i do hosta lokalnego</li><li>**RemoteOnly:** Określa, że błędy niestandardowe są wyświetlane tylko dla klientów zdalnych, a błędy ASP.NET są wyświetlane na hoście lokalnym. Jest to wartość domyślna</li></ul><p>Otwórz `web.config` plik dla aplikacji/lokacji i upewnij się, że tag ma albo `<customErrors mode="RemoteOnly" />` `<customErrors mode="On" />` zdefiniowane.</p>|

## <a name="set-deployment-method-to-retail-in-iis"></a><a id="deployment"></a>Ustaw metodę wdrażania na sprzedaż detaliczną w usługach IIS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Element wdrożenia (Schemat ustawień ASP.NET)](/previous-versions/dotnet/netframework-2.0/ms228298(v=vs.80)) |
| **Kroki** | <p>`<deployment retail>`Przełącznik jest przeznaczony do użytku przez produkcyjne serwery usług IIS. Ten przełącznik służy do uruchamiania aplikacji z najlepszą możliwą wydajnością i minimalnymi wyciekami informacji o zabezpieczeniach przez wyłączenie możliwości tworzenia przez aplikację danych wyjściowych śledzenia na stronie, wyłączenie możliwości wyświetlania szczegółowych komunikatów o błędach dla użytkowników końcowych i wyłączenie przełącznika debugowania.</p><p>Często, przełączniki i opcje, które są skoncentrowane na deweloperach, takie jak śledzenie nieudanych żądań i debugowanie, są włączane podczas aktywnego programowania. Zaleca się, aby Metoda wdrożenia na dowolnym serwerze produkcyjnym była ustawiona na sprzedaż detaliczną. Otwórz plik machine.config i upewnij się, że `<deployment retail="true" />` jest ustawiony na wartość true.</p>|

## <a name="exceptions-should-fail-safely"></a><a id="fail"></a>Wyjątki powinny być bezpiecznie bezpieczne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Bezpieczne niepowodzenie](https://owasp.org/www-community/Fail_securely) |
| **Kroki** | Aplikacja powinna być bezpiecznie niemożliwa. Każda metoda zwracająca wartość logiczną, na podstawie której podejmowana jest pewna decyzja, powinna być starannie utworzona w bloku wyjątków. Istnieje wiele błędów logicznych, z powodu których problemy związane z zabezpieczeniami są wystawiane, gdy blok wyjątku jest zapisywana carelessly.|

### <a name="example"></a>Przykład
```csharp
        public static bool ValidateDomain(string pathToValidate, Uri currentUrl)
        {
            try
            {
                if (!string.IsNullOrWhiteSpace(pathToValidate))
                {
                    var domain = RetrieveDomain(currentUrl);
                    var replyPath = new Uri(pathToValidate);
                    var replyDomain = RetrieveDomain(replyPath);

                    if (string.Compare(domain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                    {
                        //// Adding additional check to enable CMS urls if they are not hosted on same domain.
                        if (!string.IsNullOrWhiteSpace(Utilities.CmsBase))
                        {
                            var cmsDomain = RetrieveDomain(new Uri(Utilities.Base.Trim()));
                            if (string.Compare(cmDomain, replyDomain, StringComparison.OrdinalIgnoreCase) != 0)
                            {
                                return false;
                            }
                            else
                            {
                                return true;
                            }
                        }

                        return false;
                    }
                }

                return true;
            }
            catch (UriFormatException ex)
            {
                LogHelper.LogException("Utilities:ValidateDomain", ex);
                return true;
            }
        }
```
Powyższa metoda zawsze zwróci wartość true, jeśli wystąpi jakiś wyjątek. Jeśli użytkownik końcowy poda nieprawidłowo sformułowany adres URL, jest to, że przeglądarka, ale `Uri()` nie, zostanie zgłoszony wyjątek, a ofiara zostanie pobrana za prawidłowy, ale nieprawidłowo sformułowany adres URL.