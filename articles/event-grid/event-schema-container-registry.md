---
title: Azure Container Registry jako źródło Event Grid
description: Opisuje właściwości, które są dostępne dla zdarzeń Container Registry z Azure Event Grid
ms.topic: conceptual
ms.date: 07/07/2020
ms.openlocfilehash: d216fe88ee6aaad33fbbe3b93b8c4f8a6e952a71
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86113721"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Azure Container Registry jako źródło Event Grid

Ten artykuł zawiera właściwości i schemat zdarzeń Container Registry.Aby zapoznać się z wprowadzeniem do schematów zdarzeń, zobacz [Azure Event Grid schemacie zdarzeń](event-schema.md).

## <a name="event-grid-event-schema"></a>Schemat zdarzeń usługi Event Grid

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Azure Container Registry emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft. ContainerRegistry. ImagePushed | Uruchamiany po wypchnięciu obrazu. |
| Microsoft. ContainerRegistry. ImageDeleted | Uruchamiany po usunięciu obrazu. |
| Microsoft. ContainerRegistry. ChartPushed | Uruchamiany po wypchnięciu wykresu Helm. |
| Microsoft. ContainerRegistry. ChartDeleted | Uruchamiany po usunięciu wykresu Helm. |

### <a name="example-event"></a>Przykładowe zdarzenie

W poniższym przykładzie przedstawiono schemat wypychanego zdarzenia obrazu: 

```json
[{
  "id": "831e1650-001e-001b-66ab-eeb76e069631",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld:v1",
  "eventType": "ImagePushed",
  "eventTime": "2018-04-25T21:39:47.6549614Z",
  "data": {
    "id": "31c51664-e5bd-416a-a5df-e5206bc47ed0",
    "timestamp": "2018-04-25T21:39:47.276585742Z",
    "action": "push",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 3023,
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "length": 3023,
      "repository": "aci-helloworld",
      "tag": "v1"
    },
    "request": {
      "id": "7c66f28b-de19-40a4-821c-6f5f6c0003a4",
      "host": "demo.azurecr.io",
      "method": "PUT",
      "useragent": "docker/18.03.0-ce go/go1.9.4 git-commit/0520e24 os/windows arch/amd64 UpstreamClient(Docker-Client/18.03.0-ce \\\\(windows\\\\))"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat dla usuniętego zdarzenia obrazu jest podobny:

```json
[{
  "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "aci-helloworld",
  "eventType": "ImageDeleted",
  "eventTime": "2018-04-26T17:56:01.8211268Z",
  "data": {
    "id": "f06e3921-301f-42ec-b368-212f7d5354bd",
    "timestamp": "2018-04-26T17:56:00.996603117Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:213bbc182920ab41e18edc2001e06abcca6735d87782d9cef68abd83941cf0e5",
      "repository": "aci-helloworld"
    },
    "request": {
      "id": "aeda5b99-4197-409f-b8a8-ff539edb7de2",
      "host": "demo.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat wypychanego zdarzenia wykresu jest podobny do schematu dla zdarzenia wypychanego z obrazem, ale nie zawiera obiektu żądania:

```json
[{
  "id": "ea3a9c28-5b17-40f6-a500-3f02b6829277",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartPushed",
  "eventTime": "2019-03-12T22:16:31.5164086Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:16:31.0087496+00:00",
    "action":"chart_push",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

Schemat usuniętego wykresu jest podobny do schematu dla zdarzenia usuniętego z obrazu, ale nie zawiera obiektu żądania:

```json
[{
  "id": "39136b3a-1a7e-416f-a09e-5c85d5402fca",
  "topic": "/subscriptions/<subscription-id>/resourceGroups/<resource-group-name>/providers/Microsoft.ContainerRegistry/registries/<name>",
  "subject": "mychart:1.0.0",
  "eventType": "Microsoft.ContainerRegistry.ChartDeleted",
  "eventTime": "019-03-12T22:42:08.7034064Z",
  "data": {
    "id":"ea3a9c28-5b17-40f6-a500-3f02b682927",
    "timestamp":"2019-03-12T22:42:08.3783775+00:00",
    "action":"chart_delete",
    "target":{
      "mediaType":"application/vnd.acr.helm.chart",
      "size":25265,
      "digest":"sha256:7f060075264b5ba7c14c23672698152ae6a3ebac1c47916e4efe19cd624d5fab",
      "repository":"repo",
      "tag":"mychart-1.0.0.tgz",
      "name":"mychart",
      "version":"1.0.0"
    }
  },
  "dataVersion": "1.0",
  "metadataVersion": "1"
}]
```

### <a name="event-properties"></a>Właściwości zdarzenia

Zdarzenie ma następujące dane najwyższego poziomu:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie umożliwia zapisu. Ta wartość jest podawana przez usługę Event Grid. |
| subject | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| eventType | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas generowania zdarzenia na podstawie czasu UTC dostawcy. |
| identyfikator | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | object | Dane zdarzenia magazynu obiektów BLOB. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| identyfikator | ciąg | Identyfikator zdarzenia. |
| sygnatura czasowa | ciąg | Godzina wystąpienia zdarzenia. |
| akcja | ciąg | Akcja obejmująca podane zdarzenie. |
| obiektów | object | Obiekt docelowy zdarzenia. |
| żądając | object | Żądanie, które spowodowało wygenerowanie zdarzenia. |

Obiekt docelowy ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| mediaType | ciąg | Typ MIME obiektu, do którego istnieje odwołanie. |
| size | liczba całkowita | Liczba bajtów zawartości. Takie samo jak długość pola. |
| digest | ciąg | Podsumowanie zawartości zgodnie ze specyfikacją interfejsu API protokołu HTTP w rejestrze w wersji 2. |
| length | liczba całkowita | Liczba bajtów zawartości. Takie samo jak rozmiar pola. |
| repozytorium | ciąg | Nazwa repozytorium. |
| tag | ciąg | Nazwa tagu. |
| name | ciąg | Nazwa wykresu. |
| Wersja | ciąg | Wersja wykresu. |

Obiekt żądania ma następujące właściwości:

| Właściwość | Type | Opis |
| -------- | ---- | ----------- |
| identyfikator | ciąg | Identyfikator żądania, który zainicjował zdarzenie. |
| adresowe | ciąg | Adres IP lub nazwa hosta oraz prawdopodobnie port połączenia klienta, który zainicjował zdarzenie. Ta wartość jest RemoteAddr z standardowego żądania HTTP. |
| host | ciąg | Dostępna zewnętrznie nazwa hosta wystąpienia rejestru określona przez nagłówek hosta HTTP w żądaniach przychodzących. |
| method | ciąg | Metoda żądania, która wygenerowała zdarzenie. |
| UserAgent | ciąg | Nagłówek agenta użytkownika żądania. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł |Opis  |
|---------|---------|
| [Szybki Start: wysyłanie zdarzeń rejestru kontenera](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, w jaki sposób używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń Container Registry. |


## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z wprowadzeniem do Azure Event Grid, zobacz [co to jest Event Grid?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji Azure Event Grid, zobacz [Event Grid schematu subskrypcji](subscription-creation-schema.md).
