---
title: Zasady weryfikacji API Management platformy Azure | Microsoft Docs
description: Informacje o zasadach, których można używać w usłudze Azure API Management do sprawdzania poprawności żądań i odpowiedzi.
services: api-management
documentationcenter: ''
author: dlepow
ms.service: api-management
ms.topic: article
ms.date: 03/12/2021
ms.author: apimpm
ms.openlocfilehash: 1a835d26b4c41c92b9849856a2f31b3550947bd8
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104801897"
---
# <a name="api-management-policies-to-validate-requests-and-responses"></a>Zasady API Management do weryfikowania żądań i odpowiedzi

Ten artykuł zawiera informacje dotyczące następujących zasad API Management. Aby uzyskać informacje na temat dodawania i konfigurowania zasad, zobacz [zasady w API Management](./api-management-policies.md).

Zasady walidacji służą do weryfikowania żądań i odpowiedzi interfejsu API względem schematu OpenAPI oraz ochrony przed lukami w zabezpieczeniach, takich jak iniekcja nagłówków lub ładunku. Chociaż nie zastępuje się zaporą aplikacji sieci Web, zasady sprawdzania poprawności zapewniają elastyczność reagowania na dodatkową klasę zagrożeń, które nie są objęte przez produkty zabezpieczające, które są zależne od statycznych, wstępnie zdefiniowanych reguł.

## <a name="validation-policies"></a>Zasady walidacji

- [Sprawdzanie poprawności zawartości](#validate-content) — weryfikuje rozmiar lub schemat JSON żądania lub treści odpowiedzi względem schematu interfejsu API. 
- [Sprawdzanie poprawności parametrów](#validate-parameters) — sprawdza poprawność nagłówka żądania, zapytania lub parametrów ścieżki względem schematu interfejsu API.
- [Walidacja nagłówków](#validate-headers) — sprawdza poprawność nagłówków odpowiedzi względem schematu interfejsu API.
- [Walidacja kodu stanu](#validate-status-code) — sprawdza poprawność kodów stanu HTTP w odpowiedziach względem schematu interfejsu API.

> [!NOTE]
> Maksymalny rozmiar schematu interfejsu API, który może być używany przez zasady walidacji, to 4 MB. Jeśli schemat przekroczy ten limit, zasady walidacji będą zwracać błędy w czasie wykonywania. Aby go zwiększyć, skontaktuj się z [pomocą techniczną](https://azure.microsoft.com/support/options/). 

## <a name="actions"></a>Akcje

Każda zasada walidacji zawiera atrybut, który określa akcję, która API Management wykonywana podczas walidacji jednostki w żądaniu interfejsu API lub odpowiedzi względem schematu interfejsu API. Akcja może być określona dla elementów, które są reprezentowane w schemacie interfejsu API i, w zależności od zasad, dla elementów, które nie są reprezentowane w schemacie interfejsu API. Akcja określona w elemencie podrzędnym zasad przesłania akcję określoną dla jego elementu nadrzędnego.

Dostępne akcje:

| Akcja         | Opis          |                                                                                                                         
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- |
| ignoruj | Pomiń weryfikację. |
| brania | Zablokuj przetwarzanie żądania lub odpowiedzi, rejestruj pełny błąd walidacji i zwróć błąd. Przetwarzanie jest przerywane po wykryciu pierwszego zestawu błędów. |
| detect | Błędy sprawdzania poprawności dziennika, bez przerywania przetwarzania żądań lub odpowiedzi. |

## <a name="logs"></a>Dzienniki

Szczegóły błędów walidacji podczas wykonywania zasad są rejestrowane w zmiennej `context.Variables` określonej w `errors-variable-name` atrybucie w elemencie głównym zasad. Po skonfigurowaniu w `prevent` akcji błąd walidacji blokuje dalsze przetwarzanie żądania lub odpowiedzi, a także jest propagowany do `context.LastError` właściwości. 

Aby zbadać błędy, należy użyć zasad [śledzenia](api-management-advanced-policies.md#Trace) do rejestrowania błędów z zmiennych kontekstowych do [Application Insights](api-management-howto-app-insights.md).

## <a name="performance-implications"></a>Implikacje dotyczące wydajności

Dodanie zasad sprawdzania poprawności może wpłynąć na przepływność interfejsu API. Obowiązują następujące zasady ogólne:
* Im większy rozmiar schematu interfejsu API, tym niższa będzie przepływność. 
* Im większy ładunek w żądaniu lub odpowiedzi, tym mniejsza jest przepływność. 
* Rozmiar schematu interfejsu API ma większy wpływ na wydajność niż rozmiar ładunku. 
* Walidacja względem schematu interfejsu API o rozmiarze kilku megabajtów może spowodować przekroczenie limitu czasu żądania lub odpowiedzi w pewnych warunkach. Ten efekt jest bardziej widoczny w warstwach  **użycia** i **dewelopera** usługi. 

Zalecamy przeprowadzanie testów obciążeniowych z oczekiwanymi obciążeniami produkcyjnymi, aby ocenić wpływ zasad weryfikacji na przepływność interfejsu API.

## <a name="validate-content"></a>Weryfikuj zawartość

`validate-content`Zasady weryfikują rozmiar lub schemat JSON żądania lub treści odpowiedzi względem schematu interfejsu API. Formaty inne niż JSON nie są obsługiwane.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<validate-content unspecified-content-type-action="ignore|prevent|detect" max-size="size in bytes" size-exceeded-action="ignore|prevent|detect" errors-variable-name="variable name">
    <content type="content type string, for example: application/json, application/hal+json" validate-as="json" action="ignore|prevent|detect" />
</validate-content>
```

### <a name="example"></a>Przykład

W poniższym przykładzie ładunek JSON w żądaniach i odpowiedzi jest zweryfikowany w trybie wykrywania. Komunikaty z ładunkiem większym niż 100 KB są blokowane. 

```xml
<validate-content unspecified-content-type-action="prevent" max-size="102400" size-exceeded-action="prevent" errors-variable-name="requestBodyValidation">
    <content type="application/json" validate-as="json" action="detect" />
    <content type="application/hal+json" validate-as="json" action="detect" />
</validate-content>

```

### <a name="elements"></a>Elementy

| Nazwa         | Opis                                                                                                                                   | Wymagane |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Weryfikuj — zawartość | Element główny.                                                                                                                               | Tak      |
| zawartość | Dodaj co najmniej jeden z tych elementów, aby sprawdzić poprawność typu zawartości w żądaniu lub w odpowiedzi i wykonać określoną akcję.  | Nie |

### <a name="attributes"></a>Atrybuty

| Nazwa                       | Opis                                                                                                                                                            | Wymagane | Domyślne |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| nieokreślony typ zawartości — akcja | [Akcja](#actions) do wykonania dla żądań lub odpowiedzi z typem zawartości, który nie jest określony w schemacie interfejsu API. |  Tak     | Nie dotyczy   |
| maks. rozmiar | Maksymalna długość treści żądania lub odpowiedzi (w bajtach), która została sprawdzona względem `Content-Length` nagłówka. Jeśli treść żądania lub treść odpowiedzi jest skompresowana, ta wartość jest długością skompresowaną. Maksymalna dopuszczalna wartość: 102 400 bajtów (100 KB).  | Tak       | Nie dotyczy   |
| rozmiar — przekroczono — akcja | [Akcja](#actions) do wykonania dla żądań lub odpowiedzi, których treść przekracza rozmiar określony w `max-size` . |  Tak     | Nie dotyczy   |
| Błędy — zmienna-nazwa | Nazwa zmiennej w programie, `context.Variables` Aby rejestrować błędy walidacji.  |   Tak    | Nie dotyczy   |
| typ | Typ zawartości, dla której ma zostać wykonane Walidacja treści, sprawdzona względem `Content-Type` nagłówka. W tej wartości wielkość liter nie jest uwzględniana. Jeśli wartość jest pusta, ma zastosowanie do każdego typu zawartości określonego w schemacie interfejsu API. |   Nie    |  Nie dotyczy  |
| Weryfikuj jako | Aparat sprawdzania poprawności używany do walidacji treści żądania lub odpowiedzi z pasującym typem zawartości. Obecnie jedyną obsługiwaną wartością jest "JSON".   |  Tak     |  Nie dotyczy  |
| akcja | [Akcja](#actions) do wykonania dla żądań lub odpowiedzi, których treść nie jest zgodna z określonym typem zawartości.  |  Tak      | Nie dotyczy   |

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące, wychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="validate-parameters"></a>Weryfikuj parametry

`validate-parameters`Zasady weryfikują nagłówek, kwerendę lub parametry ścieżki w żądaniach względem schematu interfejsu API.

> [!IMPORTANT]
> Jeśli zaimportowano interfejs API z wersją interfejsu API zarządzania wcześniejszą niż `2021-01-01-preview` , `validate-parameters` zasady mogą nie funkcjonować. Może być konieczne ponowne [zaimportowanie interfejsu API](/rest/api/apimanagement/2021-01-01-preview/apis/createorupdate) przy użyciu wersji interfejsu API zarządzania `2021-01-01-preview` lub nowszej.


### <a name="policy-statement"></a>Instrukcja zasad

```xml
<validate-parameters specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect" errors-variable-name="variable name"> 
    <headers specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </headers>
    <query specified-parameter-action="ignore|prevent|detect" unspecified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </query>
    <path specified-parameter-action="ignore|prevent|detect">
        <parameter name="parameter name" action="ignore|prevent|detect" />
    </path>
</validate-parameters>
```

### <a name="example"></a>Przykład

W tym przykładzie wszystkie parametry zapytania i ścieżki są weryfikowane w trybie zapobiegania i w nagłówkach w trybie wykrywania. Walidacja została przesłonięta dla kilku parametrów nagłówka:

```xml
<validate-parameters specified-parameter-action="prevent" unspecified-parameter-action="prevent" errors-variable-name="requestParametersValidation"> 
    <headers specified-parameter-action="detect" unspecified-parameter-action="detect">
        <parameter name="Authorization" action="prevent" />
        <parameter name="User-Agent" action="ignore" />
        <parameter name="Host" action="ignore" />
        <parameter name="Referrer" action="ignore" />
    </headers>   
</validate-parameters>
```

### <a name="elements"></a>Elementy

| Nazwa         | Opis                                                                                                                                   | Wymagane |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Validate — parametry | Element główny. Określa domyślne akcje walidacji dla wszystkich parametrów w żądaniach.                                                                                                                              | Tak      |
| nagłówka | Dodaj ten element, aby zastąpić domyślne akcje walidacji dla parametrów nagłówka w żądaniach.   | Nie |
| query | Dodaj ten element, aby zastąpić domyślne akcje walidacji parametrów zapytania w żądaniach.  | Nie |
| path | Dodaj ten element, aby zastąpić domyślne akcje walidacji dla parametrów ścieżki URL w żądaniach.  | Nie |
| parametr | Dodaj jeden lub więcej elementów dla nazwanych parametrów, aby przesłonić konfigurację wyższego poziomu akcji walidacji. | Nie |

### <a name="attributes"></a>Atrybuty

| Nazwa                       | Opis                                                                                                                                                            | Wymagane | Domyślne |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| określona akcja parametru-Parameter | [Akcja](#actions) do wykonania dla parametrów żądania określonych w schemacie interfejsu API. <br/><br/> Gdy `headers` `query` element,, lub `path` , wartość zastępuje wartość `specified-parameter-action` w `validate-parameters` elemencie.  |  Tak     | Nie dotyczy   |
| nieokreślony parametr-akcja | [Akcja](#actions) do wykonania dla parametrów żądania, które nie są określone w schemacie interfejsu API. <br/><br/>W przypadku podanej w `headers` `query` elemencie lub wartość zastępuje wartość `unspecified-parameter-action` `validate-parameters` elementu w elemencie. |  Tak     | Nie dotyczy   |
| Błędy — zmienna-nazwa | Nazwa zmiennej w programie, `context.Variables` Aby rejestrować błędy walidacji.  |   Tak    | Nie dotyczy   |
| name | Nazwa parametru, dla którego ma zostać przesłonięta akcja walidacji. W tej wartości wielkość liter nie jest uwzględniana.  | Tak | Nie dotyczy |
| akcja | [Akcja](#actions) do wykonania dla parametru o pasującej nazwie. Jeśli parametr jest określony w schemacie interfejsu API, ta wartość zastępuje konfigurację wyższego poziomu `specified-parameter-action` . Jeśli parametr nie jest określony w schemacie interfejsu API, ta wartość zastępuje konfigurację wyższego poziomu `unspecified-parameter-action` .| Tak | Nie dotyczy | 

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** przychodzące

-   **Zakresy zasad:** wszystkie zakresy

## <a name="validate-headers"></a>Weryfikuj nagłówki

`validate-headers`Zasady sprawdzają poprawność nagłówków odpowiedzi względem schematu interfejsu API.

> [!IMPORTANT]
> Jeśli zaimportowano interfejs API z wersją interfejsu API zarządzania wcześniejszą niż `2021-01-01-preview` , `validate-headers` zasady mogą nie funkcjonować. Może być konieczne ponowne zaimportowanie interfejsu API przy użyciu wersji interfejsu API zarządzania `2021-01-01-preview` lub nowszej.

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<validate-headers specified-header-action="ignore|prevent|detect" unspecified-header-action="ignore|prevent|detect" errors-variable-name="variable name">
    <header name="header name" action="ignore|prevent|detect" />
</validate-headers>
```

### <a name="example"></a>Przykład

```xml
<validate-headers specified-header-action="ignore" unspecified-header-action="prevent" errors-variable-name="responseHeadersValidation" />
```
### <a name="elements"></a>Elementy

| Nazwa         | Opis                                                                                                                                   | Wymagane |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Walidacja — nagłówki | Element główny. Określa domyślne akcje walidacji dla wszystkich nagłówków w odpowiedzi.                                                                                                                              | Tak      |
| header | Dodaj jeden lub więcej elementów dla nazwanych nagłówków, aby zastąpić domyślne akcje walidacji dla nagłówków w odpowiedziach. | Nie |

### <a name="attributes"></a>Atrybuty

| Nazwa                       | Opis                                                                                                                                                            | Wymagane | Domyślne |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| określony-header-akcja | [Akcja](#actions) do wykonania dla nagłówków odpowiedzi określonych w schemacie interfejsu API.  |  Tak     | Nie dotyczy   |
| nieokreślony-nagłówek-akcja | [Akcja](#actions) do wykonania dla nagłówków odpowiedzi, które nie są określone w schemacie interfejsu API.  |  Tak     | Nie dotyczy   |
| Błędy — zmienna-nazwa | Nazwa zmiennej w programie, `context.Variables` Aby rejestrować błędy walidacji.  |   Tak    | Nie dotyczy   |
| name | Nazwa nagłówka, dla którego ma zostać przesłonięta akcja walidacji. W tej wartości wielkość liter nie jest uwzględniana. | Tak | Nie dotyczy |
| akcja | [Akcja](#actions) do wykonania dla nagłówka o pasującej nazwie. Jeśli nagłówek jest określony w schemacie interfejsu API, ta wartość zastępuje wartość `specified-header-action` w `validate-headers` elemencie. W przeciwnym razie zastępuje wartość `unspecified-header-action` elementu w elemencie Validate-Heads. | Tak | Nie dotyczy | 

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** wychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy

## <a name="validate-status-code"></a>Sprawdzanie poprawności kodu stanu

`validate-status-code`Zasady sprawdzają poprawność kodów stanu HTTP w odpowiedziach względem schematu interfejsu API. Te zasady mogą służyć do zapobiegania wyciekowi błędów zaplecza, które mogą zawierać ślady stosu. 

### <a name="policy-statement"></a>Instrukcja zasad

```xml
<validate-status-code unspecified-status-code-action="ignore|prevent|detect" errors-variable-name="variable name">
    <status-code code="HTTP status code number" action="ignore|prevent|detect" />
</validate-status-code>
```

### <a name="example"></a>Przykład

```xml
<validate-status-code unspecified-status-code-action="prevent" errors-variable-name="responseStatusCodeValidation" />
```

### <a name="elements"></a>Elementy

| Nazwa         | Opis                                                                                                                                   | Wymagane |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------- | -------- |
| Walidacja — stan-kod | Element główny.                                                                                                | Tak      |
| stan — kod | Dodaj co najmniej jeden element dla kodów stanu HTTP, aby zastąpić domyślną akcję walidacji kodów stanu w odpowiedziach. | Nie |

### <a name="attributes"></a>Atrybuty

| Nazwa                       | Opis                                                                                                                                                            | Wymagane | Domyślne |
| -------------------------- | ---------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------- | ------- |
| nieokreślony stan-kod-akcja | [Akcja](#actions) do wykonania dla kodów stanu HTTP w odpowiedziach, które nie są określone w schemacie interfejsu API.  |  Tak     | Nie dotyczy   |
| Błędy — zmienna-nazwa | Nazwa zmiennej w programie, `context.Variables` Aby rejestrować błędy walidacji.  |   Tak    | Nie dotyczy   |
| kod | Kod stanu HTTP do przesłonięcia akcji walidacji. | Tak | Nie dotyczy |
| akcja | [Akcja](#actions) do wykonania dla zgodnego kodu stanu, który nie jest określony w schemacie interfejsu API. Jeśli kod stanu jest określony w schemacie interfejsu API, to przesłonięcie nie zacznie obowiązywać. | Tak | Nie dotyczy | 

### <a name="usage"></a>Użycie

Tych zasad można używać w następujących [sekcjach](./api-management-howto-policies.md#sections) i [zakresach](./api-management-howto-policies.md#scopes)zasad.

-   **Sekcje zasad:** wychodzące, w przypadku błędu

-   **Zakresy zasad:** wszystkie zakresy


## <a name="validation-errors"></a>Błędy walidacji
W poniższej tabeli wymieniono wszystkie możliwe błędy zasad walidacji. 

* **Szczegóły** — mogą służyć do badania błędów. Nie ma być udostępniane publicznie.
* **Odpowiedź publiczna** — błąd zwrócony klientowi. Nie powoduje przecieku szczegółów implementacji.

| **Nazwa**                             | **Typ**                                                        | **Reguła walidacji** | **Szczegóły**                                                                                                                                       | **Odpowiedź publiczna**                                                                                                                       | **Akcja**           |
|----|----|---|---|---|----|
| **Weryfikuj — zawartość** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| |Elemencie requestbody                                                     | SizeLimit           | Treść żądania ma długość {0} bajtów i przekracza skonfigurowany limit wynoszący {size} b.                                                       | Treść żądania ma długość {0} bajtów i przekracza limit wynoszący {DataSize} bajtów.                                                          | Wykryj/Zablokuj |
||ResponseBody                                                    | SizeLimit           | Treść odpowiedzi wynosi {size} bajtów i przekracza skonfigurowany limit wynoszący {0} b.                                                      | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| {messageContentType}                 | Elemencie requestbody                                                     | Nie określono         | Nieokreślony typ zawartości {messageContentType} jest niedozwolony.                                                                                     | Nieokreślony typ zawartości {messageContentType} jest niedozwolony.                                                                             | Wykryj/Zablokuj |
| {messageContentType}                 | ResponseBody                                                    | Nie określono         | Nieokreślony typ zawartości {messageContentType} jest niedozwolony.                                                                                     | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| | ApiSchema                                                       |                     | Schemat interfejsu API nie istnieje lub nie można go rozpoznać.                                                                                          | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
|                                      | ApiSchema                                                       |                     | Schemat interfejsu API nie określa definicji.                                                                                                        | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| {messageContentType}                 | Elemencie requestbody/ResponseBody                                      | MissingDefinition   | Schemat interfejsu API nie zawiera definicji {definitionname}, która jest skojarzona z typem zawartości {messageContentType}.                        | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| {messageContentType}                 | Elemencie requestbody                                                     | IncorrectMessage    | Treść żądania jest niezgodna z definicją definicji {definitionname}, która jest skojarzona z typem zawartości {messageContentType}.<br/><br/>{valError. Message} wiersz: {valError. LineNumber}, pozycja: {valError. LinePosition}                  | Treść żądania jest niezgodna z definicją definicji {definitionname}, która jest skojarzona z typem zawartości {messageContentType}.<br/><br/>{valError. Message} wiersz: {valError. LineNumber}, pozycja: {valError. LinePosition}            | Wykryj/Zablokuj |
| {messageContentType}                 | ResponseBody                                                    | IncorrectMessage    | Treść odpowiedzi nie jest zgodna z definicją definicji {definitionname}, która jest skojarzona z typem zawartości {messageContentType}.<br/><br/>{valError. Message} wiersz: {valError. LineNumber}, pozycja: {valError. LinePosition}                                       | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
|                                      | Elemencie requestbody                                                     | ValidationException | Nie można zweryfikować treści żądania dla typu zawartości {messageContentType}.<br/><br/>{Szczegóły wyjątku}                                                                | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
|                                      | ResponseBody                                                    | ValidationException | Nie można zweryfikować treści odpowiedzi dla typu zawartości {messageContentType}.<br/><br/>{Szczegóły wyjątku}                                                                | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| **Validate-Parameter/Validate-Heads** |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {ParamName}/{headerName}           | QueryParameter / PathParameter / RequestHeader                  | Nie określono         | Nieokreślony parametr {path Parameter/Parameter/header} {paramName} jest niedozwolony.                                                               | Nieokreślony parametr {path Parameter/Parameter/header} {paramName} jest niedozwolony.                                                       | Wykryj/Zablokuj |
| {headerName}                         | ResponseHeader                                                  | Nie określono         | Nieokreślony nagłówek {headerName} jest niedozwolony.                                                                                                   | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
|                                      |ApiSchema                                                       |                     | Schemat interfejsu API nie istnieje lub nie można go rozpoznać.                                                                                            | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
|                                       | ApiSchema                                                       |                     | Schemat interfejsu API nie określa definicji.                                                                                                          | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| ParamName                          | QueryParameter / PathParameter / RequestHeader / ResponseHeader | MissingDefinition   | Schemat interfejsu API nie zawiera definicji {definitionname}, która jest skojarzona z {parametrem/ścieżką/parametrem zapytania/nagłówek} {paramName}.  | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Żądanie nie może zawierać wielu wartości dla parametru {Parameter/Path Parameter/header} {paramName}.                                           | Żądanie nie może zawierać wielu wartości dla parametru {Parameter/Path Parameter/header} {paramName}.                                   | Wykryj/Zablokuj |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | Odpowiedź nie może zawierać wielu wartości nagłówka {headerName}.                                                                              | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Wartość parametru {Parameter/Path Parameter/header} {paramName} jest niezgodna z definicją.<br/><br/>{valError. Message} wiersz: {valError. LineNumber}, pozycja: {valError. LinePosition}                                          | Wartość parametru {Parameter/Path Parameter/header} {paramName} jest niezgodna z definicją.<br/><br/>{valError. Message} wiersz: {valError. LineNumber}, pozycja: {valError. LinePosition}                              | Wykryj/Zablokuj |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | Wartość nagłówka {headerName} nie jest zgodna z definicją.<br/><br/>{valError. Message} wiersz: {valError. LineNumber}, pozycja: {valError. LinePosition}                                                                              | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | IncorrectMessage    | Nie można przeanalizować wartości parametru {Parameter/Path Parameter/header} {paramName} zgodnie z definicją. <br/><br/>uprzedni. Pojawi                                | Nie można przeanalizować wartości parametru {Parameter/Path Parameter/header} {paramName} zgodnie z definicją. <br/><br/>uprzedni. Pojawi                      | Wykryj/Zablokuj |
| {headerName}                         | ResponseHeader                                                  | IncorrectMessage    | Nie można przeanalizować wartości nagłówka {headerName} zgodnie z definicją.                                                                  | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| ParamName                          | QueryParameter / PathParameter / RequestHeader                  | ValidationError     | {Parameter/parametr ścieżki/nagłówek} nie można zweryfikować produktu {paramName}.<br/><br/>{Szczegóły wyjątku}                                                                      | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| {headerName}                         | ResponseHeader                                                  | ValidationError     | Nie można sprawdzić poprawności nagłówka {headerName}.<br/><br/>{Szczegóły wyjątku}                                                                                                          | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |
| **Walidacja — stan-kod**             |                                                                 |                     |                                                                                                                                                   |                                                                                                                                           |                      |
| {status-Code}                        | Stanu                                                      | Nie określono         | Kod stanu odpowiedzi {status-Code} jest niedozwolony.                                                                                                | Nie można przetworzyć żądania z powodu błędu wewnętrznego. Skontaktuj się z właścicielem interfejsu API.                                                       | Wykryj/Zablokuj |


W poniższej tabeli wymieniono wszystkie możliwe wartości przyczyny błędu walidacji wraz z możliwymi wartościami komunikatów:

|  **Przyczyna**         |    **Wiadomość** |
|---|---|  
| Złe żądanie       |     {Szczegóły} dla zmiennej kontekstowej {publiczna odpowiedź} dla klienta|
| Niedozwolona odpowiedź  | {Szczegóły} dla zmiennej kontekstowej {publiczna odpowiedź} dla klienta |






## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pracy z zasadami, zobacz:

-   [Zasady w API Management](api-management-howto-policies.md)
-   [Przekształć interfejsy API](transform-api.md)
-   [Dokumentacja zasad](./api-management-policies.md) pełna lista instrukcji zasad i ich ustawień
-   [Przykłady zasad](./policy-reference.md)
-   [Obsługa błędów](./api-management-error-handling-policies.md)
