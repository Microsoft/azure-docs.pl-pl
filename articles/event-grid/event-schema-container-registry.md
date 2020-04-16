---
title: Usługa Azure Container Registry jako źródło siatki zdarzeń
description: W tym artykule opisano właściwości, które są dostarczane dla zdarzeń rejestru kontenerów za pomocą usługi Azure Event Grid
services: event-grid
author: spelluru
manager: timlt
ms.service: event-grid
ms.topic: conceptual
ms.date: 04/09/2020
ms.author: spelluru
ms.openlocfilehash: 7e33feb04edf42f1e2a32b9b8c8e2fd214692f31
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81393358"
---
# <a name="azure-container-registry-as-an-event-grid-source"></a>Usługa Azure Container Registry jako źródło siatki zdarzeń

Ten artykuł zawiera właściwości i schemat zdarzeń rejestru kontenerów.Aby zapoznać się ze schematem zdarzeń, zobacz [Schemat zdarzeń usługi Azure Event Grid](event-schema.md).

## <a name="event-grid-event-schema"></a>Schemat zdarzenia w siatce zdarzeń

### <a name="available-event-types"></a>Dostępne typy zdarzeń

Usługa Azure Container Registry emituje następujące typy zdarzeń:

| Typ zdarzenia | Opis |
| ---------- | ----------- |
| Microsoft.ContainerRegistry.ImagePushed | Wywoływane po wypchnięciu obrazu. |
| Plik Microsoft.ContainerRegistry.ImageDeleted | Wywoływane po usunięciu obrazu. |
| Plik Microsoft.ContainerRegistry.ChartPushed | Wywoływane po naciśnięciu wykresu helm. |
| Plik Microsoft.ContainerRegistry.ChartDeleted | Wywoływane po usunięciu wykresu Helm. |

### <a name="example-event"></a>Przykładowe zdarzenie

W poniższym przykładzie przedstawiono schemat zdarzenia wypchniętego obrazu: 

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

Schemat zdarzenia usuniętego obrazu jest podobny:

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

Schemat zdarzenia wypychanego wykresu jest podobny do schematu dla sobrazowanego zdarzenia wypchniętego, ale nie zawiera obiektu żądania:

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

Schemat zdarzenia usuniętego wykresu jest podobny do schematu dla zdarzenia usuniętego z obrazem, ale nie zawiera obiektu żądania:

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

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| temat | ciąg | Pełna ścieżka zasobu do źródła zdarzeń. To pole nie jest zapisywalne. Ta wartość jest podawana przez usługę Event Grid. |
| Temat | ciąg | Zdefiniowana przez wydawcę ścieżka do tematu zdarzenia. |
| Eventtype | ciąg | Jeden z zarejestrowanych typów zdarzeń dla tego źródła zdarzeń. |
| eventTime | ciąg | Czas, w której zdarzenie jest generowane na podstawie czasu UTC dostawcy. |
| id | ciąg | Unikatowy identyfikator zdarzenia. |
| dane | obiekt | Dane zdarzenia magazynu obiektów blob. |
| dataVersion | ciąg | Wersja schematu obiektu danych. Wydawca definiuje wersję schematu. |
| metadataVersion | ciąg | Wersja schematu metadanych zdarzenia. Usługa Event Grid definiuje schemat właściwości najwyższego poziomu. Ta wartość jest podawana przez usługę Event Grid. |

Obiekt danych ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | ciąg | Identyfikator zdarzenia. |
| sygnatura czasowa | ciąg | Czas, w którym wystąpiło zdarzenie. |
| action | ciąg | Akcja, która obejmuje dostarczone zdarzenie. |
| Docelowego | obiekt | Cel zdarzenia. |
| Żądanie | obiekt | Żądanie, które wygenerowało zdarzenie. |

Obiekt docelowy ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| Mediatype | ciąg | Typ MIME obiektu, do którego istnieje odwołanie. |
| size | liczba całkowita | Liczba bajtów zawartości. Tak samo jak pole Długość. |
| digest | ciąg | Podsumowanie zawartości, zgodnie z definicją specyfikacji interfejsu API HTTP rejestru V2. |
| length | liczba całkowita | Liczba bajtów zawartości. Tak samo jak pole Rozmiar. |
| repozytorium | ciąg | Nazwa repozytorium. |
| tag | ciąg | Nazwa tagu. |
| name | ciąg | Nazwa wykresu. |
| version | ciąg | Wersja wykresu. |

Obiekt żądania ma następujące właściwości:

| Właściwość | Typ | Opis |
| -------- | ---- | ----------- |
| id | ciąg | Identyfikator żądania, które zainicjowało zdarzenie. |
| Addr | ciąg | Adres IP lub nazwa hosta i ewentualnie port połączenia klienta, który zainicjował zdarzenie. Ta wartość jest RemoteAddr od standardowego żądania http. |
| host | ciąg | Zewnętrznie dostępna nazwa hosta wystąpienia rejestru, określona przez nagłówek hosta http w żądaniach przychodzących. |
| method | ciąg | Metoda żądania, która wygenerowała zdarzenie. |
| Useragent | ciąg | Nagłówek agenta użytkownika żądania. |

## <a name="tutorials-and-how-tos"></a>Samouczki i poradniki
|Tytuł |Opis  |
|---------|---------|
| [Szybki start: wysyłanie zdarzeń rejestru kontenerów](../container-registry/container-registry-event-grid-quickstart.md?toc=%2fazure%2fevent-grid%2ftoc.json) | Pokazuje, jak używać interfejsu wiersza polecenia platformy Azure do wysyłania zdarzeń rejestru kontenerów. |


## <a name="next-steps"></a>Następne kroki

* Aby uzyskać wprowadzenie do usługi Azure Event Grid, zobacz [Co to jest siatka zdarzeń?](overview.md)
* Aby uzyskać więcej informacji na temat tworzenia subskrypcji usługi Azure Event Grid, zobacz [schemat subskrypcji usługi Event Grid](subscription-creation-schema.md).
