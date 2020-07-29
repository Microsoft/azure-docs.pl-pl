---
title: Jak utworzyć zestaw danych szkoleniowych dla niestandardowego aparatu rozpoznawania formularzy modelu
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak upewnić się, że zestaw danych szkoleniowych jest zoptymalizowany pod kątem szkolenia modelu aparatu rozpoznawania formularzy.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: c6780d952b9ce6ea58fc6c8a2509a4526add7149
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028268"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego

W przypadku korzystania z niestandardowego modelu aparatu rozpoznawania formularzy należy zapewnić własne dane szkoleniowe, aby model mógł nauczyć się na formularzach specyficznych dla branży. 

W przypadku szkoleń bez etykiet ręcznych można użyć pięciu wypełnionych formularzy lub pustego formularza (w nazwie pliku musi znajdować się słowo "Empty") oraz dwie wypełnione formularze. Nawet jeśli masz wystarczającą liczbę wypełnionych formularzy, dodanie pustego formularza do zestawu danych szkoleniowych może poprawić dokładność modelu.

Jeśli chcesz używać ręcznie etykietowanych danych szkoleniowych, musisz zacząć od co najmniej pięciu wypełnionych formularzy tego samego typu. Oprócz wymaganego zestawu danych można nadal używać formularzy bez etykiet i pustego formularza.

## <a name="training-data-tips"></a>Wskazówki dotyczące danych szkoleniowych

Ważne jest, aby użyć zestawu danych, który jest zoptymalizowany do szkoleń. Skorzystaj z poniższych wskazówek, aby uzyskać najlepsze wyniki operacji dotyczącej [niestandardowego modelu uczenia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) :

* Jeśli to możliwe, użyj tekstowych dokumentów PDF zamiast dokumentów opartych na obrazie. Skanowane pliki PDF są obsługiwane jako obrazy.
* W przypadku formularzy wypełnionych Użyj przykładów, w których wypełniono wszystkie pola.
* Używaj formularzy z różnymi wartościami w każdym polu.
* Jeśli obrazy formularzy mają niższą jakość, użyj większego zestawu danych (na przykład obrazów 10-15).
* Łączny rozmiar zestawu danych szkoleniowych może należeć do 500 stron.

## <a name="general-input-requirements"></a>Ogólne wymagania dotyczące danych wejściowych

Upewnij się, że zestaw danych szkoleniowych jest również zgodny z wymaganiami wejściowymi dla całej zawartości aparatu rozpoznawania formularzy. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>Przekaż swoje dane szkoleniowe

Po umieszczeniu zestawu dokumentów formularzy, które będą używane do szkoleń, należy przekazać je do kontenera magazynu obiektów blob platformy Azure. Jeśli nie wiesz, jak utworzyć konto usługi Azure Storage za pomocą kontenera, zapoznaj się z [przewodnikiem Szybki Start dla usługi Azure Storage, aby uzyskać Azure Portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

Jeśli chcesz używać ręcznie oznaczonych danych, musisz również przekazać *.labels.js* i *.ocr.jsna* plikach, które odpowiadają Twoim dokumentom szkoleniowym. Do wygenerowania tych plików można użyć [przykładowego narzędzia do etykietowania](./quickstarts/label-tool.md) (lub własnego interfejsu użytkownika).

### <a name="organize-your-data-in-subfolders-optional"></a>Organizuj dane w podfolderach (opcjonalnie)

Domyślnie interfejs API sieci [uczenia niestandardowego](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) będzie używać tylko dokumentów formularzy znajdujących się w katalogu głównym kontenera magazynu. Można jednak nauczyć się z danymi w podfolderach, jeśli określisz ją w wywołaniu interfejsu API. Zwykle treść wywołania [niestandardowego modelu uczenia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) ma następujący format, gdzie `<SAS URL>` jest adresem URL sygnatury dostępu współdzielonego kontenera:

```json
{
  "source":"<SAS URL>"
}
```

Jeśli dodasz następującą zawartość do treści żądania, interfejs API będzie szkolić z dokumentami znajdującymi się w podfolderach. `"prefix"`Pole jest opcjonalne i ograniczy zestaw danych szkoleniowych do plików, których ścieżki zaczynają się podanym ciągiem. Dlatego wartość `"Test"` , na przykład, spowoduje przeszukanie przez interfejs API tylko plików lub folderów, które zaczynają się od słowa "test".

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak utworzyć zestaw danych szkoleniowych, postępuj zgodnie z przewodnikiem Szybki Start, aby przeprowadzić uczenie niestandardowego modelu aparatu rozpoznawania formularzy i rozpocząć korzystanie z niego w formularzach.

* [Uczenie modelu i wyodrębnianie danych formularza przy użyciu Zwinięciea](./quickstarts/curl-train-extract.md)
* [Uczenie modelu i wyodrębnianie danych formularza przy użyciu interfejsu API REST i języka Python](./quickstarts/python-train-extract.md)
* [Uczenie z etykietami przy użyciu narzędzia do etykietowania przykładowego](./quickstarts/label-tool.md)
* [Trenowanie z użyciem etykiet za pomocą interfejsu API REST i języka Python](./quickstarts/python-labeled-data.md)
