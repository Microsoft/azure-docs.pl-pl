---
title: Odwołanie do schematu elementu webhook rejestru
description: Dokumentacja ładunku JSON dla żądań elementu webhook w rejestrze kontenerów platformy Azure, które są generowane, gdy elementy webhook są włączone dla zdarzeń wypychania lub usuwania artefaktów
ms.topic: article
ms.date: 03/05/2019
ms.openlocfilehash: 8354ef9db24d5825238155ac567d5d829f9b0d7f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74455962"
---
# <a name="azure-container-registry-webhook-reference"></a>Azure Container Registry odwołanie elementu webhook

Można [skonfigurować elementy webhook](container-registry-webhook.md) dla rejestru kontenerów, które generują zdarzenia, gdy są wykonywane pewne akcje. Na przykład Włącz elementy webhook wyzwalane, gdy obraz kontenera lub wykres Helm jest wypychany do rejestru lub został usunięty. Po wyzwoleniu elementu webhook Azure Container Registry wystawia żądanie HTTP lub HTTPS zawierające informacje o zdarzeniu do określonego punktu końcowego. Punkt końcowy może następnie przetworzyć element webhook i odpowiednio wykonać odpowiednie działania.

W poniższych sekcjach szczegółowo opisano schemat żądań elementu webhook generowanych przez obsługiwane zdarzenia. Sekcje zdarzeń zawierają schemat ładunku dla typu zdarzenia, przykładowego ładunku żądania i jednego lub więcej przykładowych poleceń, które spowodują wywołanie elementu webhook.

Informacje o konfigurowaniu elementów webhook dla usługi Azure Container Registry można znaleźć w temacie [Using Azure Container Registry webhooks](container-registry-webhook.md).

## <a name="webhook-requests"></a>Żądania elementu webhook

### <a name="http-request"></a>Żądanie HTTP

Wyzwolony element webhook wysyła `POST` żądanie HTTP do punktu końcowego adresu URL, który został określony podczas konfigurowania elementu webhook.

### <a name="http-headers"></a>Nagłówki HTTP

Żądania elementu webhook zawierają element, `Content-Type` `application/json` Jeśli nie określono `Content-Type` niestandardowego nagłówka elementu webhook.

Żadne inne nagłówki nie są dodawane do żądania poza tymi niestandardowymi nagłówkami, które mogły zostać określone dla elementu webhook.

## <a name="push-event"></a>Zdarzenie wypychania

Element webhook wyzwolony, gdy obraz kontenera jest wypychany do repozytorium.

### <a name="push-event-payload"></a>Ładunek zdarzenia wypychania

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której wyzwolono zdarzenie elementu webhook.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[obiektów](#target)|Typ złożony|Obiekt docelowy zdarzenia, które wyzwoliło zdarzenie elementu webhook.|
|[żądając](#request)|Typ złożony|Żądanie, które spowodowało wygenerowanie zdarzenia elementu webhook.|

### <a name="target"></a><a name="target"></a>obiektów

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME obiektu, do którego istnieje odwołanie.|
|`size`|Int32|Liczba bajtów zawartości. Takie samo jak długość pola.|
|`digest`|Ciąg|Podsumowanie zawartości zgodnie ze specyfikacją interfejsu API protokołu HTTP w rejestrze w wersji 2.|
|`length`|Int32|Liczba bajtów zawartości. Takie samo jak rozmiar pola.|
|`repository`|Ciąg|Nazwa repozytorium.|
|`tag`|Ciąg|Nazwa tagu obrazu.|

### <a name="request"></a><a name="request"></a>żądając

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|Ciąg|Identyfikator żądania, który zainicjował zdarzenie.|
|`host`|Ciąg|Dostępna zewnętrznie nazwa hosta wystąpienia rejestru określona przez nagłówek hosta HTTP w żądaniach przychodzących.|
|`method`|Ciąg|Metoda żądania, która wygenerowała zdarzenie.|
|`useragent`|Ciąg|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-image-push-event"></a>Przykład ładunku: zdarzenie wypychania obrazu

```JSON
{
  "id": "cb8c3971-9adc-488b-xxxx-43cbb4974ff5",
  "timestamp": "2017-11-17T16:52:01.343145347Z",
  "action": "push",
  "target": {
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "size": 524,
    "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
    "length": 524,
    "repository": "hello-world",
    "tag": "v1"
  },
  "request": {
    "id": "3cbb6949-7549-4fa1-xxxx-a6d5451dffc7",
    "host": "myregistry.azurecr.io",
    "method": "PUT",
    "useragent": "docker/17.09.0-ce go/go1.8.3 git-commit/afdb6d4 kernel/4.10.0-27-generic os/linux arch/amd64 UpstreamClient(Docker-Client/17.09.0-ce \\(linux\\))"
  }
}
```

Przykładowe polecenie [interfejsu wiersza polecenia platformy Docker](https://docs.docker.com/engine/reference/commandline/cli/) wyzwalające element webhook zdarzenia **push** :

```bash
docker push myregistry.azurecr.io/hello-world:v1
```

## <a name="chart-push-event"></a>Zdarzenie wypychania wykresu

Element webhook wyzwalany podczas wypychania wykresu Helm do repozytorium.

### <a name="chart-push-event-payload"></a>Ładunek zdarzeń wypychania wykresu

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której wyzwolono zdarzenie elementu webhook.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[obiektów](#helm_target)|Typ złożony|Obiekt docelowy zdarzenia, które wyzwoliło zdarzenie elementu webhook.|

### <a name="target"></a><a name="helm_target"></a>obiektów

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME obiektu, do którego istnieje odwołanie.|
|`size`|Int32|Liczba bajtów zawartości.|
|`digest`|Ciąg|Podsumowanie zawartości zgodnie ze specyfikacją interfejsu API protokołu HTTP w rejestrze w wersji 2.|
|`repository`|Ciąg|Nazwa repozytorium.|
|`tag`|Ciąg|Nazwa tagu wykresu.|
|`name`|Ciąg|Nazwa wykresu.|
|`version`|Ciąg|Wersja wykresu.|

### <a name="payload-example-chart-push-event"></a>Przykład ładunku: zdarzenie push wykresu

```JSON
{
  "id": "6356e9e0-627f-4fed-xxxx-d9059b5143ac",
  "timestamp": "2019-03-05T23:45:31.2614267Z",
  "action": "chart_push",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Przykładowe polecenie [interfejsu wiersza polecenia platformy Azure](/cli/azure/acr) wyzwalające element webhook zdarzenia **chart_push** :

```azurecli
az acr helm push wordpress-5.4.0.tgz --name MyRegistry
```

## <a name="delete-event"></a>Usuń zdarzenie

Element webhook wyzwalany po usunięciu repozytorium lub manifestu obrazu. Niewyzwalane, gdy tag zostanie usunięty.

### <a name="delete-event-payload"></a>Usuń ładunek zdarzenia

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której wyzwolono zdarzenie elementu webhook.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[obiektów](#delete_target)|Typ złożony|Obiekt docelowy zdarzenia, które wyzwoliło zdarzenie elementu webhook.|
|[żądając](#delete_request)|Typ złożony|Żądanie, które spowodowało wygenerowanie zdarzenia elementu webhook.|

### <a name="target"></a><a name="delete_target"></a> obiektów

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME obiektu, do którego istnieje odwołanie.|
|`digest`|Ciąg|Podsumowanie zawartości zgodnie ze specyfikacją interfejsu API protokołu HTTP w rejestrze w wersji 2.|
|`repository`|Ciąg|Nazwa repozytorium.|

### <a name="request"></a><a name="delete_request"></a> żądając

|Element|Typ|Opis|
|------------------|----------|-----------|
|`id`|Ciąg|Identyfikator żądania, który zainicjował zdarzenie.|
|`host`|Ciąg|Dostępna zewnętrznie nazwa hosta wystąpienia rejestru określona przez nagłówek hosta HTTP w żądaniach przychodzących.|
|`method`|Ciąg|Metoda żądania, która wygenerowała zdarzenie.|
|`useragent`|Ciąg|Nagłówek agenta użytkownika żądania.|

### <a name="payload-example-image-delete-event"></a>Przykład ładunku: zdarzenie usunięcia obrazu

```JSON
{
    "id": "afc359ce-df7f-4e32-xxxx-1ff8aa80927b",
    "timestamp": "2017-11-17T16:54:53.657764628Z",
    "action": "delete",
    "target": {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "digest": "sha256:xxxxd5c8786bb9e621a45ece0dbxxxx1cdc624ad20da9fe62e9d25490f33xxxx",
      "repository": "hello-world"
    },
    "request": {
      "id": "3d78b540-ab61-4f75-xxxx-7ca9ecf559b3",
      "host": "myregistry.azurecr.io",
      "method": "DELETE",
      "useragent": "python-requests/2.18.4"
    }
  }
```

Przykład poleceń [interfejsu wiersza polecenia platformy Azure](/cli/azure/acr) , które wyzwalają element webhook zdarzenia **delete** :

```azurecli
# Delete repository
az acr repository delete --name MyRegistry --repository MyRepository

# Delete image
az acr repository delete --name MyRegistry --image MyRepository:MyTag
```

## <a name="chart-delete-event"></a>Zdarzenie usuwania wykresu

Element webhook wyzwalany po usunięciu wykresu lub repozytorium Helm. 

### <a name="chart-delete-event-payload"></a>Usuń ładunek zdarzenia z wykresu

|Element|Typ|Opis|
|-------------|----------|-----------|
|`id`|Ciąg|Identyfikator zdarzenia elementu webhook.|
|`timestamp`|DateTime|Godzina, o której wyzwolono zdarzenie elementu webhook.|
|`action`|Ciąg|Akcja, która wyzwoliła zdarzenie elementu webhook.|
|[obiektów](#chart_delete_target)|Typ złożony|Obiekt docelowy zdarzenia, które wyzwoliło zdarzenie elementu webhook.|

### <a name="target"></a><a name="chart_delete_target"></a> obiektów

|Element|Typ|Opis|
|------------------|----------|-----------|
|`mediaType`|Ciąg|Typ MIME obiektu, do którego istnieje odwołanie.|
|`size`|Int32|Liczba bajtów zawartości.|
|`digest`|Ciąg|Podsumowanie zawartości zgodnie ze specyfikacją interfejsu API protokołu HTTP w rejestrze w wersji 2.|
|`repository`|Ciąg|Nazwa repozytorium.|
|`tag`|Ciąg|Nazwa tagu wykresu.|
|`name`|Ciąg|Nazwa wykresu.|
|`version`|Ciąg|Wersja wykresu.|

### <a name="payload-example-chart-delete-event"></a>Przykład ładunku: zdarzenie usunięcia wykresu

```JSON
{
  "id": "338a3ef7-ad68-4128-xxxx-fdd3af8e8f67",
  "timestamp": "2019-03-06T00:10:48.1270754Z",
  "action": "chart_delete",
  "target": {
    "mediaType": "application/vnd.acr.helm.chart",
    "size": 25265,
    "digest": "sha256:xxxx8075264b5ba7c14c23672xxxx52ae6a3ebac1c47916e4efe19cd624dxxxx",
    "repository": "repo",
    "tag": "wordpress-5.4.0.tgz",
    "name": "wordpress",
    "version": "5.4.0.tgz"
  }
}
```

Przykładowe polecenie [interfejsu wiersza polecenia platformy Azure](/cli/azure/acr) wyzwalające element webhook zdarzenia **chart_delete** :

```azurecli
az acr helm delete wordpress --version 5.4.0 --name MyRegistry
```

## <a name="next-steps"></a>Następne kroki

[Używanie Azure Container Registry elementów webhook](container-registry-webhook.md)