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
ms.openlocfilehash: 513d98ae274c668197b066c742119055248f37f9
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911719"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Tworzenie zestawu danych szkoleniowych dla modelu niestandardowego

W przypadku korzystania z niestandardowego modelu aparatu rozpoznawania formularzy należy zapewnić własne dane szkoleniowe do niestandardowej operacji na [modelu uczenia](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) , dzięki czemu model będzie mógł nauczyć się na formularzach specyficznych dla branży. Postępuj zgodnie z tym przewodnikiem, aby dowiedzieć się, jak zbierać i przygotowywać dane w celu skutecznego uczenia modelu.

W przypadku szkoleń bez etykiet ręcznych można użyć pięciu wypełnionych formularzy lub pustego formularza (w nazwie pliku musi znajdować się słowo "Empty") oraz dwie wypełnione formularze. Nawet jeśli masz wystarczającą liczbę wypełnionych formularzy, dodanie pustego formularza do zestawu danych szkoleniowych może poprawić dokładność modelu.

Jeśli chcesz używać ręcznie etykietowanych danych szkoleniowych, musisz zacząć od co najmniej pięciu wypełnionych formularzy tego samego typu. Oprócz wymaganego zestawu danych można nadal używać formularzy bez etykiet i pustego formularza.

## <a name="custom-model-input-requirements"></a>Wymagania dotyczące danych wejściowych modelu niestandardowego

Najpierw upewnij się, że zestaw danych szkoleniowych spełnia wymagania wejściowe dla aparatu rozpoznawania formularzy.

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="training-data-tips"></a>Wskazówki dotyczące danych szkoleniowych

Postępuj zgodnie z tymi dodatkowymi wskazówkami, aby dodatkowo zoptymalizować zestaw danych do szkoleń.

* Jeśli to możliwe, użyj tekstowych dokumentów PDF zamiast dokumentów opartych na obrazie. Skanowane pliki PDF są obsługiwane jako obrazy.
* W przypadku formularzy wypełnionych Użyj przykładów, w których wypełniono wszystkie pola.
* Używaj formularzy z różnymi wartościami w każdym polu.
* Jeśli obrazy formularzy mają niższą jakość, użyj większego zestawu danych (na przykład obrazów 10-15).

## <a name="upload-your-training-data"></a>Przekaż swoje dane szkoleniowe

Po umieszczeniu zestawu dokumentów formularzy, które będą używane do szkoleń, należy przekazać je do kontenera magazynu obiektów blob platformy Azure. Jeśli nie wiesz, jak utworzyć konto usługi Azure Storage za pomocą kontenera, zapoznaj się z [przewodnikiem Szybki Start dla usługi Azure Storage, aby uzyskać Azure Portal](../../storage/blobs/storage-quickstart-blobs-portal.md). Użyj warstwy wydajności standardowa.

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

* [Uczenie modelu i wyodrębnianie danych formularza przy użyciu biblioteki klienckiej](./quickstarts/client-library.md)
* [Uczenie modelu i wyodrębnianie danych formularza przy użyciu Zwinięciea](./quickstarts/curl-train-extract.md)
* [Uczenie modelu i wyodrębnianie danych formularza przy użyciu interfejsu API REST i języka Python](./quickstarts/python-train-extract.md)
* [Uczenie z etykietami przy użyciu narzędzia do etykietowania przykładowego](./quickstarts/label-tool.md)
* [Trenowanie z użyciem etykiet za pomocą interfejsu API REST i języka Python](./quickstarts/python-labeled-data.md)

## <a name="see-also"></a>Zobacz także

* [Co to jest rozpoznawanie formularzy?](./overview.md)