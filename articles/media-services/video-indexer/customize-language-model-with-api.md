---
title: Dostosowywanie modelu języka za pomocą interfejsu API Video Indexer
titlesuffix: Azure Media Services
description: Dowiedz się, jak dostosować model języka za pomocą interfejsu API Video Indexer.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 02/04/2020
ms.author: kumud
ms.openlocfilehash: f373afae03357ffb65eb459f806fe441e29b21b9
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "87047081"
---
# <a name="customize-a-language-model-with-the-video-indexer-api"></a>Dostosowywanie modelu języka za pomocą interfejsu API Video Indexer

Video Indexer umożliwia tworzenie niestandardowych modeli języka w celu dostosowania rozpoznawania mowy przez przekazywanie tekstu adaptacji, czyli tekstu z domeny, do którego słownika ma być dołączany aparat. Po przeprowadzeniu szkolenia modelu zostaną rozpoznane nowe wyrazy pojawiające się w tekście adaptacyjnym.

Aby zapoznać się z szczegółowym omówieniem i najlepszymi rozwiązaniami dotyczącymi niestandardowych modeli języków, zobacz [Dostosowywanie modelu języka za pomocą Video Indexer](customize-language-model-overview.md).

Za pomocą interfejsów API Video Indexer można tworzyć i edytować niestandardowe modele języka na koncie, zgodnie z opisem w tym temacie. Możesz również użyć witryny sieci Web, zgodnie z opisem w temacie [Dostosowywanie modelu języka za pomocą witryny sieci web video Indexer](customize-language-model-with-api.md).

## <a name="create-a-language-model"></a>Tworzenie modelu języka

Interfejs API [tworzenia modelu języka](https://api-portal.videoindexer.ai/docs/services/Operations/operations/Create-Language-Model?) tworzy nowy niestandardowy model języka na określonym koncie. Można przekazać pliki dla modelu języka w tym wywołaniu. Alternatywnie możesz utworzyć model języka tutaj i przekazać pliki dla modelu później, aktualizując model języka.

> [!NOTE]
> Należy nadal przeszkolić model z włączonymi plikami dla modelu, aby poznać jego zawartość. Wskazówki dotyczące szkolenia języka znajdują się w następnej sekcji.

Aby przekazać pliki, które mają zostać dodane do modelu języka, należy przekazać pliki w treści przy użyciu FormData, oprócz podania wartości wymaganych parametrów. Istnieją dwa sposoby wykonania tego zadania:

* Klucz będzie nazwą pliku, a wartością będzie plik txt.
* Kluczem będzie nazwa pliku, a wartość będzie adresem URL pliku txt.

### <a name="response"></a>Reakcja

Odpowiedź zawiera metadane dotyczące nowo utworzonego modelu języka wraz z metadanymi w każdym pliku modelu, zgodnie z formatem tego przykładowego wyjścia JSON:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}

```

## <a name="train-a-language-model"></a>Uczenie modelu języka

Interfejs API [uczenia modelu języka](https://api-portal.videoindexer.ai/docs/services/operations/operations/Train-Language-Model?&pattern=train) pociąga za siebie niestandardowy model języka na określonym koncie z zawartością plików, które zostały przekazane do i włączone w modelu języka.

> [!NOTE]
> Najpierw należy utworzyć model języka i przekazać jego pliki. Pliki można przekazać podczas tworzenia modelu języka lub przez aktualizację modelu języka.

### <a name="response"></a>Reakcja

Odpowiedź zawiera metadane dotyczące nowo przeszkolonego modelu języka wraz z metadanymi w każdym pliku modelu, zgodnie z formatem tego przykładowego wyjścia JSON:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": false,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Zwrócony `id` jest unikatowy identyfikator służący do rozróżniania modeli języka, podczas gdy `languageModelId` jest używany do [przekazywania wideo do indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Upload-video?) i ponownego [indeksowania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Re-index-video?) interfejsów API wideo (nazywanych także `linguisticModelId` w Video Indexer interfejsów API przekazywania/ponownego indeksowania).

## <a name="delete-a-language-model"></a>Usuwanie modelu języka

Interfejs API [usuwania modelu języka](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model?&pattern=delete) usuwa niestandardowy model języka z określonego konta. Wszystkie filmy wideo, które były używane w modelu języka, będą zachować ten sam indeks do momentu ponownego indeksowania wideo. Jeśli ponownie indeksujesz wideo, możesz przypisać nowy model języka do wideo. W przeciwnym razie Video Indexer będzie używać domyślnego modelu do ponownego indeksowania wideo.

### <a name="response"></a>Reakcja

Po pomyślnym usunięciu modelu języka nie jest zwracana zawartość.

## <a name="update-a-language-model"></a>Aktualizowanie modelu języka

Interfejs API [aktualizacji modelu języka](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model?&pattern=update) aktualizuje niestandardowy model osoby językowej na określonym koncie.

> [!NOTE]
> Musisz już utworzyć model języka. To wywołanie służy do włączania lub wyłączania wszystkich plików w ramach modelu, aktualizowania nazwy modelu języka i przekazywania plików do dodania do modelu języka.

Aby przekazać pliki, które mają zostać dodane do modelu języka, należy przekazać pliki w treści przy użyciu FormData, oprócz podania wartości wymaganych parametrów. Istnieją dwa sposoby wykonania tego zadania:

* Klucz będzie nazwą pliku, a wartością będzie plik txt.
* Kluczem będzie nazwa pliku, a wartość będzie adresem URL pliku txt.

### <a name="response"></a>Reakcja

Odpowiedź zawiera metadane dotyczące nowo przeszkolonego modelu języka wraz z metadanymi w każdym pliku modelu, zgodnie z formatem tego przykładowego wyjścia JSON:

```json
{
    "id": "41464adf-e432-42b1-8e09-f52905d7e29d",
    "name": "TestModel",
    "language": "En-US",
    "state": "Waiting",
    "languageModelId": "531e5745-681d-4e1d-b124-12e5ab57a891",
    "files": [
    {
        "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
        "name": "RenamedFile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.5233333"
    },
    {
        "id": "9ac35b4b-1381-49c4-9fe4-8234bfdd0f50",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-27T20:10:10.68"
    }
    ]
}
```

Użyj `id` plików zwracanych w odpowiedzi na potrzeby pobrania zawartości pliku.

## <a name="update-a-file-from-a-language-model"></a>Aktualizowanie pliku z modelu języka

[Aktualizacja pliku](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Language-Model-file?&pattern=update) umożliwia zaktualizowanie nazwy i `enable` stanu pliku w niestandardowym modelu języka na określonym koncie.

### <a name="response"></a>Reakcja

Odpowiedź zawiera metadane pliku, który został zaktualizowany zgodnie z formatem przykładowych danych wyjściowych JSON poniżej.

```json
{
  "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
  "name": "RenamedFile",
  "enable": false,
  "creator": "John Doe",
  "creationTime": "2018-04-27T20:10:10.5233333"
}
```

Użyj `id` pliku zwróconego w odpowiedzi, aby pobrać zawartość pliku.

## <a name="get-a-specific-language-model"></a>Uzyskiwanie określonego modelu języka

Interfejs [Get](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model?&pattern=get) API zwraca informacje dotyczące określonego modelu języka w określonym koncie, takim jak język i pliki, które znajdują się w modelu języka.

### <a name="response"></a>Reakcja

Odpowiedź zawiera metadane w określonym modelu języka wraz z metadanymi w każdym pliku modelu, zgodnie z formatem tego przykładowego wyjścia JSON:

```json
{
    "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
    "name": "TestModel",
    "language": "En-US",
    "state": "None",
    "languageModelId": "00000000-0000-0000-0000-000000000000",
    "files": [
    {
        "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
        "name": "hellofile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.6733333"
    },
    {
        "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
        "name": "worldfile",
        "enable": true,
        "creator": "John Doe",
        "creationTime": "2018-04-28T11:55:34.86"
    }
    ]
}
```

Użyj `id` pliku zwróconego w odpowiedzi, aby pobrać zawartość pliku.

## <a name="get-all-the-language-models"></a>Pobierz wszystkie modele języka

Interfejs [Get All](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Models?&pattern=get) zwraca wszystkie niestandardowe modele języka na określonym koncie na liście.

### <a name="response"></a>Reakcja

Odpowiedź zawiera listę wszystkich modeli języka na Twoim koncie oraz wszystkich metadanych i plików, które są zgodne z formatem tego przykładowego wyjścia JSON:

```json
[
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a891",
        "name": "TestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000000",
        "files": [
        {
            "id": "25be7c0e-b6a6-4f48-b981-497e920a0bc9",
            "name": "hellofile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.6733333"
        },
        {
            "id": "33025f5b-2354-485e-a50c-4e6b76345ca7",
            "name": "worldfile",
            "enable": true,
            "creator": "John Doe",
            "creationTime": "2018-04-28T11:55:34.86"
        }
        ]
    },
    {
        "id": "dfae5745-6f1d-4edd-b224-42e1ab57a892",
        "name": "AnotherTestModel",
        "language": "En-US",
        "state": "None",
        "languageModelId": "00000000-0000-0000-0000-000000000001",
        "files": []
    }
]
```

## <a name="delete-a-file-from-a-language-model"></a>Usuwanie pliku z modelu języka

Interfejs API [usuwania](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Language-Model-File?&pattern=delete) usuwa określony plik z określonego modelu języka w określonym koncie.

### <a name="response"></a>Reakcja

Nie jest zwracana zawartość, gdy plik zostanie pomyślnie usunięty z modelu języka.

## <a name="get-metadata-on-a-file-from-a-language-model"></a>Pobieranie metadanych z pliku z modelu języka

[Pobieranie metadanych](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Language-Model-File-Data?&pattern=get%20language%20model) interfejsu API plików zwraca zawartość i metadane w określonym pliku z wybranego modelu języka na Twoim koncie.

### <a name="response"></a>Reakcja

Odpowiedź zawiera zawartość i metadane pliku w formacie JSON, podobnie jak w przypadku tego przykładu:

```json
{
    "content": "hello\r\nworld",
    "id": "84fcf1ac-1952-48f3-b372-18f768eedf83",
    "name": "Hello",
    "enable": true,
    "creator": "John Doe",
    "creationTime": "2018-04-27T20:10:10.5233333"
}
```

> [!NOTE]
> Zawartość tego przykładowego pliku są słowami "Hello" i "World" w dwóch oddzielnych wierszach.

## <a name="download-a-file-from-a-language-model"></a>Pobieranie pliku z modelu języka

Plik interfejsu API [pobierania plików](https://api-portal.videoindexer.ai/docs/services/operations/operations/Download-Language-Model-File-Content?) pobiera plik tekstowy zawierający zawartość określonego pliku z określonego modelu języka w określonym koncie. Ten plik tekstowy powinien być zgodny z zawartością pliku tekstowego, który został pierwotnie przekazany.

### <a name="response"></a>Reakcja

Odpowiedź będzie pobierać plik tekstowy z zawartością pliku w formacie JSON.

## <a name="next-steps"></a>Następne kroki

[Dostosowywanie modelu języka za pomocą witryny sieci Web](customize-language-model-with-website.md)
