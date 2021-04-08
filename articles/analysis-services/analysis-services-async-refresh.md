---
title: Odświeżanie asynchroniczne dla modeli Azure Analysis Services | Microsoft Docs
description: Opisuje sposób używania interfejsu API REST Azure Analysis Services do kodu asynchronicznego odświeżania danych modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: e9fd20fd42e9fe1eb0e98766798e5c759c974c97
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92013903"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Odświeżanie asynchroniczne za pomocą interfejsu API REST

Używając dowolnego języka programowania, który obsługuje wywołania REST, można wykonywać asynchroniczne operacje odświeżania danych na Azure Analysis Services modeli tabelarycznych. Obejmuje to synchronizację replik tylko do odczytu dla skalowania zapytań w poziomie. 

Operacje odświeżania danych mogą zająć trochę czasu w zależności od liczby czynników, w tym ilości danych, poziomu optymalizacji przy użyciu partycji itd. Te operacje są tradycyjnie wywoływane z istniejącymi metodami, takimi jak użycie metody " [Tomasz](/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) " (model obiektów tabelarycznych), poleceń cmdlet [programu PowerShell](/analysis-services/powershell/analysis-services-powershell-reference) lub [TMSL](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (język skryptów modelu tabelarycznego). Jednak te metody mogą wymagać często niezawodnych, długotrwałych połączeń HTTP.

Interfejs API REST dla Azure Analysis Services umożliwia wykonywanie operacji odświeżania danych asynchronicznie. Za pomocą interfejsu API REST, długotrwałe połączenia HTTP z aplikacji klienckich nie są konieczne. Istnieją również inne wbudowane funkcje zapewniające niezawodność, takie jak autoponawianie prób i zatwierdzanie wsadowe.

## <a name="base-url"></a>Podstawowy adres URL

Podstawowy adres URL jest następujący:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Na przykład rozważmy model o nazwie AdventureWorks na serwerze o nazwie `myserver` , który znajduje się w regionie platformy Azure zachodnie stany USA. Nazwa serwera:

```
asazure://westus.asazure.windows.net/myserver 
```

Podstawowy adres URL dla tej nazwy serwera:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Korzystając z podstawowego adresu URL, można dołączać zasoby i operacje w oparciu o następujące parametry: 

![Odświeżanie asynchroniczne](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Wszystkie elementy, które kończą się w **s** , są kolekcjami.
- Wszystkie elementy kończące się znakiem **()** są funkcją.
- Coś innego jest zasób/obiekt.

Na przykład możesz użyć zlecenia POST w kolekcji rerefreshs, aby wykonać operację odświeżania:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Wszystkie wywołania muszą zostać uwierzytelnione z prawidłowym tokenem Azure Active Directory (OAuth 2) w nagłówku autoryzacji i muszą spełniać następujące wymagania:

- Token musi być tokenem użytkownika lub podmiotem usługi aplikacji.
- Token musi mieć odpowiednich odbiorców `https://*.asazure.windows.net` .
- Użytkownik lub aplikacja musi mieć wystarczające uprawnienia na serwerze lub modelu, aby wykonać żądane wywołanie. Poziom uprawnień jest określany przez role należące do modelu lub grupy administratorów na serwerze.

    > [!IMPORTANT]
    > Obecnie wymagane są uprawnienia roli **administratora serwera** .

## <a name="post-refreshes"></a>Opublikuj/refreshes

Aby wykonać operację odświeżania, użyj zlecenia POST w kolekcji/refreshes, aby dodać nowy element Refresh do kolekcji. Nagłówek lokalizacji w odpowiedzi zawiera identyfikator odświeżenia. Aplikacja kliencka może odłączać i sprawdzać stan później, jeśli jest to wymagane, ponieważ jest asynchroniczna.

Tylko jedna operacja odświeżania jest akceptowana jednocześnie dla modelu. Jeśli jest aktualnie uruchomiona operacja odświeżania i zostanie przesłana inna, zwracany jest kod stanu HTTP 409.

Treść może wyglądać następująco:

```
{
    "Type": "Full",
    "CommitMode": "transactional",
    "MaxParallelism": 2,
    "RetryCount": 2,
    "Objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer"
        },
        {
            "table": "DimDate"
        }
    ]
}
```

### <a name="parameters"></a>Parametry

Określanie parametrów nie jest wymagane. Ustawienie domyślne jest stosowane.

| Nazwa             | Typ  | Opis  |Domyślne  |
|------------------|-------|--------------|---------|
| `Type`           | Wyliczenie  | Typ przetwarzania do wykonania. Typy są wyrównane z typami [poleceń Refresh](/analysis-services/tmsl/refresh-command-tmsl) TMSL: Full, clearValues, Oblicz, dataonly, Automatic i defragmentowania. Dodawanie typu nie jest obsługiwane.      |   automatyczne      |
| `CommitMode`     | Wyliczenie  | Określa, czy obiekty będą zatwierdzane w partiach, czy tylko po zakończeniu. Tryby to: default, transakcyjna, partialBatch.  |  transakcyjna       |
| `MaxParallelism` | int   | Ta wartość określa maksymalną liczbę wątków, w których uruchamianie poleceń przetwarzania jest równoległe. Ta wartość jest wyrównana z właściwością MaxParallelism, którą można ustawić w TMSL [Sequence polecenia](/analysis-services/tmsl/sequence-command-tmsl) lub przy użyciu innych metod.       | 10        |
| `RetryCount`     | int   | Wskazuje liczbę ponownych prób wykonania operacji przed zakończeniem się niepowodzeniem.      |     0    |
| `Objects`        | Tablica | Tablica obiektów do przetworzenia. Każdy obiekt zawiera: "Tabela" podczas przetwarzania całej tabeli lub "tabeli" i "partycji" podczas przetwarzania partycji. Jeśli nie określono żadnych obiektów, cały model zostanie odświeżony. |   Przetwórz cały model      |

Wartość CommitMode jest równa partialBatch. Jest on używany podczas wstępnego ładowania dużych zestawów danych, które mogą zająć kilka godzin. Jeśli operacja odświeżania nie powiedzie się po pomyślnym zatwierdzeniu jednej lub większej liczby partii, pomyślnie przekazane partie pozostaną zatwierdzone (nie zostaną wycofane pomyślnie przekazane partie).

> [!NOTE]
> W czasie pisania rozmiar wsadu jest wartością MaxParallelism, ale można zmienić tę wartość.

### <a name="status-values"></a>Wartości stanu

|Wartość stanu  |Opis  |
|---------|---------|
|`notStarted`    |   Operacja nie została jeszcze uruchomiona.      |
|`inProgress`     |   Operacja w toku.      |
|`timedOut`     |    Przekroczono limit czasu operacji na podstawie określonego przez użytkownika limitu czasu.     |
|`cancelled`     |   Operacja została anulowana przez użytkownika lub system.      |
|`failed`     |   Operacja nie powiodła się.      |
|`succeeded`      |   Operacja zakończyła się pomyślnie.      |

## <a name="get-refreshesrefreshid"></a>Pobierz/refreshes/\<refreshId>

Aby sprawdzić stan operacji odświeżania, użyj zlecenia GET dla identyfikatora odświeżania. Oto przykład treści odpowiedzi. Jeśli operacja jest w toku, `inProgress` jest zwracana w stanie.

```
{
    "startTime": "2017-12-07T02:06:57.1838734Z",
    "endTime": "2017-12-07T02:07:00.4929675Z",
    "type": "full",
    "status": "succeeded",
    "currentRefreshType": "full",
    "objects": [
        {
            "table": "DimCustomer",
            "partition": "DimCustomer",
            "status": "succeeded"
        },
        {
            "table": "DimDate",
            "partition": "DimDate",
            "status": "succeeded"
        }
    ]
}
```

## <a name="get-refreshes"></a>Pobierz/refreshes

Aby uzyskać listę operacji odświeżania historycznego dla modelu, użyj zlecenia GET w kolekcji/refreshes. Oto przykład treści odpowiedzi. 

> [!NOTE]
> W momencie zapisu ostatnie 30 dni operacji odświeżania są przechowywane i zwracane, ale ta liczba może ulec zmianie.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-07T02:06:57.1838734Z",
        "endTime": "2017-12-07T02:07:00.4929675Z",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T01:05:54.157324Z",
        "endTime": "2017-12-07T01:05:57.353371Z",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>Usuń/refreshes/\<refreshId>

Aby anulować operację odświeżania w toku, użyj czasownika DELETE dla identyfikatora odświeżania.

## <a name="post-sync"></a>Opublikuj/Sync

Po wykonaniu operacji odświeżania może być konieczne zsynchronizowanie nowych danych z replikami w celu skalowania zapytań w poziomie. Aby wykonać operację synchronizacji dla modelu, należy użyć czasownika POST dla funkcji/Sync. Nagłówek lokalizacji w odpowiedzi zawiera identyfikator operacji synchronizacji.

## <a name="get-sync-status"></a>Pobierz stan/Sync

Aby sprawdzić stan operacji synchronizacji, użyj metody GET, która przekazuje identyfikator operacji jako parametr. Oto przykład treści odpowiedzi:

```
{
    "operationId": "cd5e16c6-6d4e-4347-86a0-762bdf5b4875",
    "database": "AdventureWorks2",
    "UpdatedAt": "2017-12-09T02:44:26.18",
    "StartedAt": "2017-12-09T02:44:20.743",
    "syncstate": 2,
    "details": null
}
```

Wartości dla `syncstate` :

- 0: Replikowanie. Pliki bazy danych są replikowane do folderu docelowego.
- 1: ponownego wypełniania. Baza danych jest usuwana w wystąpieniach serwera tylko do odczytu.
- 2: ukończono. Operacja synchronizacji zakończyła się pomyślnie.
- 3: nie powiodło się. Operacja synchronizacji nie powiodła się.
- 4: finalizowanie. Operacja synchronizacji została ukończona, ale wykonuje kroki czyszczenia.

## <a name="code-sample"></a>Przykład kodu

Oto przykład kodu w języku C#, aby rozpocząć pracę, [RestApiSample w witrynie GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Aby użyć przykładu kodu

1.    Klonuj lub Pobierz repozytorium. Otwórz rozwiązanie RestApiSample.
2.    Znajdź klienta wiersza **. BaseAddress =...** wprowadź [podstawowy adres URL](#base-url).

Przykładowy kod używa uwierzytelniania [nazwy głównej usługi](#service-principal) .

### <a name="service-principal"></a>Jednostka usługi

Aby uzyskać więcej informacji na temat konfigurowania nazwy głównej usługi i przypisywania do niej wymaganych uprawnień na platformie Azure, zobacz [Tworzenie jednostki usługi — Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md) i [Dodawanie jednostki usługi do roli administratora serwera](analysis-services-addservprinc-admins.md) . Po wykonaniu kroków wykonaj następujące dodatkowe czynności:

1.    W przykładzie kodu Znajdź **ciąg Authority =...**, Zamień **wspólny** z identyfikatorem dzierżawy w Twojej organizacji.
2.    Comment/uncomment, aby Klasa ClientCredential była używana do tworzenia wystąpienia obiektu poświadczeń. Upewnij się, że \<App ID> \<App Key> wartości i są dostępne w bezpieczny sposób lub Użyj uwierzytelniania opartego na certyfikatach dla podmiotów usługi.
3.    Uruchom przykład.


## <a name="see-also"></a>Zobacz też

[Badan](analysis-services-samples.md)   
[Interfejs API REST](/rest/api/analysisservices/servers)