---
title: Opublikuj zdarzenie w temacie Azure Event Grid niestandardowego
description: W tym artykule opisano sposób publikowania zdarzenia w temacie niestandardowym. Pokazuje format danych post i zdarzenia.
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: 197d8eb1963300bc6576e664c7c3fd470cf70bb2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108264"
---
# <a name="post-to-custom-topic-for-azure-event-grid"></a>Opublikuj w temacie niestandardowym dla Azure Event Grid

W tym artykule opisano sposób publikowania zdarzenia w temacie niestandardowym. Pokazuje format danych post i zdarzenia. [Umowa dotycząca poziomu usług (SLA)](https://azure.microsoft.com/support/legal/sla/event-grid/v1_0/) dotyczy tylko wpisów, które pasują do oczekiwanego formatu.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="endpoint"></a>Punkt końcowy

Podczas wysyłania wpisu HTTP do niestandardowego tematu Użyj formatu identyfikatora URI: `https://<topic-endpoint>?api-version=2018-01-01` .

Na przykład prawidłowy identyfikator URI to: `https://exampletopic.westus2-1.eventgrid.azure.net/api/events?api-version=2018-01-01` .

Aby uzyskać punkt końcowy dla niestandardowego tematu za pomocą interfejsu wiersza polecenia platformy Azure, użyj:

```azurecli-interactive
az eventgrid topic show --name <topic-name> -g <topic-resource-group> --query "endpoint"
```

Aby uzyskać punkt końcowy dla niestandardowego tematu z Azure PowerShell, użyj:

```powershell
(Get-AzEventGridTopic -ResourceGroupName <topic-resource-group> -Name <topic-name>).Endpoint
```

## <a name="header"></a>Header

W żądaniu Dołącz wartość nagłówka o nazwie `aeg-sas-key` , która zawiera klucz do uwierzytelniania.

Na przykład prawidłowa wartość nagłówka to `aeg-sas-key: VXbGWce53249Mt8wuotr0GPmyJ/nDT4hgdEj9DpBeRr38arnnm5OFg==` .

Aby uzyskać klucz niestandardowego tematu za pomocą interfejsu wiersza polecenia platformy Azure, użyj polecenia:

```azurecli
az eventgrid topic key list --name <topic-name> -g <topic-resource-group> --query "key1"
```

Aby uzyskać klucz tematu niestandardowego przy użyciu programu PowerShell, użyj polecenia:

```powershell
(Get-AzEventGridTopicKey -ResourceGroupName <topic-resource-group> -Name <topic-name>).Key1
```

## <a name="event-data"></a>Dane zdarzenia

W przypadku niestandardowych tematów dane najwyższego poziomu zawierają te same pola co standardowe zdarzenia zdefiniowane przez zasób. Jedną z tych właściwości jest właściwość danych, która zawiera właściwości unikatowe dla tematu niestandardowego. Jako wydawca zdarzeń należy określić właściwości tego obiektu danych. Użyj następującego schematu:

```json
[
  {
    "id": string,    
    "eventType": string,
    "subject": string,
    "eventTime": string-in-date-time-format,
    "data":{
      object-unique-to-each-publisher
    },
    "dataVersion": string
  }
]
```

Aby uzyskać opis tych właściwości, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md). Podczas ogłaszania zdarzeń w temacie Event Grid tablica może mieć łączny rozmiar do 1 MB. Każde zdarzenie w tablicy jest ograniczone do 64 KB (ogólna dostępność) lub 1 MB (wersja zapoznawcza).

> [!NOTE]
> Zdarzenie o rozmiarze do 64 KB jest objęte ogólnym dostępnością Umowa dotycząca poziomu usług (SLA). Obsługa zdarzeń o rozmiarze do 1 MB jest obecnie dostępna w wersji zapoznawczej. Zdarzenia powyżej 64 KB są naliczone w przyrostach 64 KB. 

Na przykład prawidłowy schemat danych zdarzenia to:

```json
[{
  "id": "1807",
  "eventType": "recordInserted",
  "subject": "myapp/vehicles/motorcycles",
  "eventTime": "2017-08-10T21:03:07+00:00",
  "data": {
    "make": "Ducati",
    "model": "Monster"
  },
  "dataVersion": "1.0"
}]
```

## <a name="response"></a>Reakcja

Po opublikowaniu w punkcie końcowym tematu otrzymujesz odpowiedź. Odpowiedź jest standardowym kodem odpowiedzi HTTP. Niektóre typowe odpowiedzi to:

|Wynik  |Reakcja  |
|---------|---------|
|Powodzenie  | 200 OK  |
|Dane zdarzenia mają niepoprawny format | 400 Nieprawidłowe żądanie |
|Nieprawidłowy klucz dostępu | 401 Brak autoryzacji |
|Nieprawidłowy punkt końcowy | 404 — Nie znaleziono |
|Tablica lub zdarzenie przekraczają limity rozmiaru | ładunek 413 zbyt duży |

W przypadku błędów treść komunikatu ma następujący format:

```json
{
    "error": {
        "code": "<HTTP status code>",
        "message": "<description>",
        "details": [{
            "code": "<HTTP status code>",
            "message": "<description>"
    }]
  }
}
```

## <a name="next-steps"></a>Następne kroki

* Aby uzyskać informacje na temat monitorowania dostarczania zdarzeń, zobacz [monitorowanie Event Grid dostarczania komunikatów](monitor-event-delivery.md).
* Aby uzyskać więcej informacji na temat klucza uwierzytelniania, zobacz [Event Grid zabezpieczenia i uwierzytelnianie](security-authentication.md).
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
