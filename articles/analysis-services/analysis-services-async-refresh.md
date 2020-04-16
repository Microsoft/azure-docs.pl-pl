---
title: Odświeżanie asynchroniczne dla modeli usług Azure Analysis Services | Dokumenty firmy Microsoft
description: W tym artykule opisano, jak używać interfejsu API REST usług Azure Analysis Services do kodowania asynchronicznej odświeżania danych modelu.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: c5f6cec8b7fd1169a4f04649fcaf7bb7ada33833
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81406282"
---
# <a name="asynchronous-refresh-with-the-rest-api"></a>Odświeżanie asynchroniczne za pomocą interfejsu API REST

Przy użyciu dowolnego języka programowania, który obsługuje wywołania REST, można wykonać asynchroniczne operacje odświeżania danych na modelach tabelaryczne usług Azure Analysis Services. Obejmuje to synchronizację replik tylko do odczytu dla kwerendy skalowane w poziomie. 

Operacje odświeżania danych mogą zająć trochę czasu w zależności od wielu czynników, w tym ilości danych, poziomu optymalizacji przy użyciu partycji itp. Te operacje zostały tradycyjnie wywołane przy użyciu istniejących metod, takich jak przy użyciu [TOM](https://docs.microsoft.com/analysis-services/tom/introduction-to-the-tabular-object-model-tom-in-analysis-services-amo) (model obiektu tabelarycznego), polecenia cmdlet [programu PowerShell](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) lub [TMSL](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (język skryptów modelu tabelarycznego). Jednak te metody mogą wymagać często zawodne, długotrwałe połączenia HTTP.

Interfejs API REST dla usług Azure Analysis Services umożliwia wykonywanie operacji odświeżania danych asynchronicznie. Za pomocą interfejsu API REST długotrwałe połączenia HTTP z aplikacji klienckich nie są konieczne. Istnieją również inne wbudowane funkcje niezawodności, takie jak automatyczne ponownych prób i zatwierdzeń wsadowych.

## <a name="base-url"></a>Podstawowy adres URL

Podstawowy adres URL jest zgodny z następującym formatem:

```
https://<rollout>.asazure.windows.net/servers/<serverName>/models/<resource>/
```

Rozważmy na przykład model o nazwie `myserver`AdventureWorks na serwerze o nazwie , znajduje się w regionie West US Azure. Nazwa serwera to:

```
asazure://westus.asazure.windows.net/myserver 
```

Podstawowy adres URL dla tej nazwy serwera to:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/ 
```

Za pomocą podstawowego adresu URL zasoby i operacje można dołączać na podstawie następujących parametrów: 

![Odświeżanie asynchronii](./media/analysis-services-async-refresh/aas-async-refresh-flow.png)

- Wszystko, co kończy się w **s** jest zbiorem.
- Wszystko, co kończy się **()** jest funkcją.
- Wszystko inne jest zasobem/obiektem.

Na przykład można użyć zlecenia POST w kolekcji Odświeża, aby wykonać operację odświeżania:

```
https://westus.asazure.windows.net/servers/myserver/models/AdventureWorks/refreshes
```

## <a name="authentication"></a>Authentication

Wszystkie wywołania muszą być uwierzytelnione przy użyciu prawidłowego tokenu usługi Azure Active Directory (OAuth 2) w nagłówku autoryzacji i muszą spełniać następujące wymagania:

- Token musi być tokenem użytkownika lub jednostką usługi aplikacji.
- Token musi mieć ustawioną `https://*.asazure.windows.net`właściwą grupę odbiorców na .
- Użytkownik lub aplikacja musi mieć wystarczające uprawnienia na serwerze lub modelu, aby nawiązać żądane wywołanie. Poziom uprawnień jest określany przez role w modelu lub grupie administracyjnej na serwerze.

    > [!IMPORTANT]
    > Obecnie wymagane są uprawnienia do roli **administratora serwera.**

## <a name="post-refreshes"></a>POST /odświeża

Aby wykonać operację odświeżania, użyj zlecenia POST w /refreshes kolekcji, aby dodać nowy element odświeżania do kolekcji. Nagłówek Lokalizacja w odpowiedzi zawiera identyfikator odświeżania. Aplikacja kliencka może rozłączyć i sprawdzić stan później, jeśli jest to wymagane, ponieważ jest asynchroniczne.

Tylko jedna operacja odświeżania jest akceptowana w czasie dla modelu. Jeśli istnieje bieżąca operacja uruchamiania odświeżania i przesłana jest inna, zwracany jest kod stanu 409 Konflikt HTTP.

Ciało może przypominać następujące:

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

Określanie parametrów nie jest wymagane. Zostanie zastosowana wartość domyślna.

| Nazwa             | Typ  | Opis  |Domyślne  |
|------------------|-------|--------------|---------|
| `Type`           | Wyliczenie  | Typ przetwarzania do wykonania. Typy są wyrównane z typami [poleceń odświeżania](https://docs.microsoft.com/analysis-services/tmsl/refresh-command-tmsl) TMSL: pełna, clearValues, calculate, dataOnly, automatic i defragmentacja. Dodaj typ nie jest obsługiwany.      |   automatyczne      |
| `CommitMode`     | Wyliczenie  | Określa, czy obiekty zostaną zatwierdzone w partiach lub tylko po zakończeniu. Tryby obejmują: domyślne, transakcyjne, częścioweBatch.  |  Transakcyjnych       |
| `MaxParallelism` | int   | Ta wartość określa maksymalną liczbę wątków, na których mają być uruchamiane polecenia przetwarzania równolegle. Ta wartość wyrównana z właściwością MaxParallelism, którą można ustawić w [poleceniu Sekwencja](https://docs.microsoft.com/analysis-services/tmsl/sequence-command-tmsl) TMSL lub przy użyciu innych metod.       | 10        |
| `RetryCount`     | int   | Wskazuje, ile razy operacja zostanie ponowić próbę przed niepowodzeniem.      |     0    |
| `Objects`        | Tablica | Tablica obiektów do przetworzenia. Każdy obiekt zawiera: "table" podczas przetwarzania całej tabeli lub "tabela" i "partycja" podczas przetwarzania partycji. Jeśli nie określono żadnych obiektów, cały model jest odświeżany. |   Przetwarzanie całego modelu      |

CommitMode jest równa partialBatch. Jest on używany podczas wykonywania początkowego obciążenia dużych zestawów danych, które mogą potrwać wiele godzin. Jeśli operacja odświeżania nie powiedzie się po pomyślnym załączeniu jednej lub więcej partii, pomyślnie zatwierdzone partie pozostaną zatwierdzone (nie wycofa pomyślnie zatwierdzonych partii).

> [!NOTE]
> W momencie pisania rozmiar partii jest MaxParallelism wartość, ale ta wartość może ulec zmianie.

### <a name="status-values"></a>Wartości stanu

|Wartość stanu  |Opis  |
|---------|---------|
|`notStarted`    |   Operacja jeszcze się nie rozpoczęła.      |
|`inProgress`     |   Operacja w toku.      |
|`timedOut`     |    Limit czasu operacji na podstawie limitu czasu określonego przez użytkownika.     |
|`cancelled`     |   Operacja anulowana przez użytkownika lub system.      |
|`failed`     |   Operacja nie powiodła się.      |
|`succeeded`      |   Operacja zakończyła się sukcesem.      |

## <a name="get-refreshesrefreshid"></a>POBIERZ /refreshes/\<refreshId>

Aby sprawdzić stan operacji odświeżania, użyj zlecenia GET w identyfikatorze odświeżania. Oto przykład treści odpowiedzi. Jeśli operacja jest w `inProgress` toku, jest zwracany w stanie.

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

## <a name="get-refreshes"></a>POBIERZ /odświeża

Aby uzyskać listę historycznych operacji odświeżania dla modelu, należy użyć zlecenia GET w /refreshes kolekcji. Oto przykład treści odpowiedzi. 

> [!NOTE]
> W momencie pisania ostatnich 30 dni operacji odświeżania są przechowywane i zwracane, ale ta liczba może ulec zmianie.

```
[
    {
        "refreshId": "1344a272-7893-4afa-a4b3-3fb87222fdac",
        "startTime": "2017-12-09T01:58:04.76",
        "endTime": "2017-12-09T01:58:12.607",
        "status": "succeeded"
    },
    {
        "refreshId": "474fc5a0-3d69-4c5d-adb4-8a846fa5580b",
        "startTime": "2017-12-07T02:05:48.32",
        "endTime": "2017-12-07T02:05:54.913",
        "status": "succeeded"
    }
]
```

## <a name="delete-refreshesrefreshid"></a>USUŃ /refreshes/\<refreshId>

Aby anulować operację odświeżania w toku, użyj zlecenia DELETE w identyfikatorze odświeżania.

## <a name="post-sync"></a>POST /synchronizacja

Po wykonaniu operacji odświeżania może być konieczne zsynchronizowanie nowych danych z replikami dla skalowanych w poziomie kwerend. Aby wykonać operację synchronizacji dla modelu, należy użyć zlecenia POST w funkcji /sync. Nagłówek Lokalizacja w odpowiedzi zawiera identyfikator operacji synchronizacji.

## <a name="get-sync-status"></a>GET /stan synchronizacji

Aby sprawdzić stan operacji synchronizacji, należy użyć zlecenia GET przekazującego identyfikator operacji jako parametr. Oto przykład treści odpowiedzi:

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

Wartości `syncstate`dla:

- 0: Replikowanie. Pliki bazy danych są replikowane do folderu docelowego.
- 1: Nawadnianie. Baza danych jest nawodniona na serwerach tylko do odczytu.
- 2: Zakończono. Operacja synchronizacji została pomyślnie ukończona.
- 3: Nie powiodło się. Operacja synchronizacji nie powiodła się.
- 4: Finalizacja. Operacja synchronizacji została ukończona, ale wykonuje kroki oczyszczania.

## <a name="code-sample"></a>Przykład kodu

Oto przykład kodu C#, aby rozpocząć, [RestApiSample na GitHub](https://github.com/Microsoft/Analysis-Services/tree/master/RestApiSample).

### <a name="to-use-the-code-sample"></a>Aby użyć przykładu kodu

1.    Klonuj lub pobieraj repozytorium. Otwórz rozwiązanie RestApiSample.
2.    Znajdź **klienta linii. BaseAddress = ...** i podaj [podstawowy adres URL](#base-url).

Przykładowy kod używa uwierzytelniania [jednostkowego usługi.](#service-principal)

### <a name="service-principal"></a>Jednostka usługi

Zobacz [Tworzenie jednostki usługi — witryna Azure portal](../active-directory/develop/howto-create-service-principal-portal.md) i dodawanie [jednostki usługi do roli administratora serwera,](analysis-services-addservprinc-admins.md) aby uzyskać więcej informacji na temat konfigurowania jednostki usługi i przypisywania niezbędnych uprawnień w usłudze Azure AS. Po wykonaniu tych czynności wykonaj następujące czynności:

1.    W przykładzie kodu znajdź **urząd ciąg = ...**, zastąpić **wspólne** z identyfikatorem dzierżawy organizacji.
2.    Komentarz/uncomment więc ClientCredential klasy jest używany do tworzenia wystąpienia obiektu cred. Upewnij \<się, że> \<identyfikatora aplikacji i> wartości klucza aplikacji są dostępne w bezpieczny sposób lub użyj uwierzytelniania opartego na certyfikatach dla podmiotów korzystających z usługi.
3.    Uruchom przykład.


## <a name="see-also"></a>Zobacz też

[Próbki](analysis-services-samples.md)   
[Interfejs API REST](https://docs.microsoft.com/rest/api/analysisservices/servers)   


