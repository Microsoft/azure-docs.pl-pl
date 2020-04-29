---
title: Pobieranie informacji o przekonwertowanym modelu
description: Opis wszystkich parametrów konwersji modelu
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: d5f843add0649682bae8c472bc50b6beea33bf93
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80681521"
---
# <a name="get-information-about-a-converted-model"></a>Pobieranie informacji o przekonwertowanym modelu

Plik arrAsset utworzony przez usługę konwersji jest przeznaczony wyłącznie do użytku przez usługę renderowania. Mogą jednak wystąpić sytuacje, w których chcesz uzyskać dostęp do informacji o modelu bez rozpoczynania sesji renderowania. W związku z tym usługa konwersji umieszcza plik JSON obok pliku arrAsset w kontenerze danych wyjściowych. Na przykład jeśli plik `buggy.gltf` zostanie przekonwertowany, kontener wyjściowy będzie zawierać plik o nazwie `buggy.info.json` obok przekonwertowanego elementu zawartości `buggy.arrAsset`. Zawiera informacje o modelu źródłowym, przekonwertowanym modelu i o samej konwersji.

## <a name="example-info-file"></a>Przykładowy plik *informacyjny*

Oto przykładowy plik *informacyjny* tworzony przez konwersję pliku o nazwie `buggy.gltf`:

```JSON
{
    "files": {
        "input": "Buggy.gltf"
    },
    "conversionSettings": {
        "recenterToOrigin": true
    },
    "inputInfo": {
        "sourceAssetExtension": ".gltf",
        "sourceAssetFormat": "glTF2 Importer",
        "sourceAssetFormatVersion": "2.0",
        "sourceAssetGenerator": "COLLADA2GLTF"
    },
    "inputStatistics": {
        "numMeshes": 148,
        "numFaces": 308306,
        "numVertices": 245673,
        "numMaterial": 149,
        "numFacesSmallestMesh": 2,
        "numFacesBiggestMesh": 8764,
        "numNodes": 206,
        "numMeshUsagesInScene": 236,
        "maxNodeDepth": 3
    },
    "outputInfo": {
        "conversionToolVersion": "3b28d840de9916f9d628342f474d38c3ab949590",
        "conversionHash": "CCDB1F7A4C09F565"
    },
    "outputStatistics": {
        "numMeshPartsCreated": 236,
        "numMeshPartsInstanced": 88,
        "recenteringOffset": [
            -24.1,
            -50.9,
            -16.5974
        ],
        "boundingBox": {
            "min": [
                -43.52,
                -61.775,
                -79.6416
            ],
            "max": [
                43.52,
                61.775,
                79.6416
            ]
        }
    }
}
```

## <a name="information-in-the-info-file"></a>Informacje w pliku info

### <a name="the-files-section"></a>Sekcja *pliki*

Ta sekcja zawiera podane nazwy plików.

* `input`: Nazwa pliku źródłowego.
* `output`: Nazwa pliku wyjściowego, gdy użytkownik określił nazwę niedomyślną.

### <a name="the-conversionsettings-section"></a>Sekcja *conversionSettings*

Ta sekcja zawiera kopię [ConversionSettings](configure-model-conversion.md#settings-file) określoną podczas konwersji modelu.

### <a name="the-inputinfo-section"></a>Sekcja *inputInfo*

Ta sekcja rejestruje informacje o formacie pliku źródłowego.

* `sourceAssetExtension`: Rozszerzenie pliku źródłowego.
* `sourceAssetFormat`: Opis formatu pliku źródłowego.
* `sourceAssetFormatVersion`: Wersja formatu pliku źródłowego.
* `sourceAssetGenerator`: Nazwa narzędzia, które wygenerowało plik źródłowy, jeśli jest dostępny.

### <a name="the-inputstatistics-section"></a>Sekcja *inputStatistics*

Ta sekcja zawiera informacje dotyczące sceny źródłowej. Często występują rozbieżności między wartościami w tej sekcji a odpowiednikami wartości w narzędziu, które utworzyły Model źródłowy. Takie różnice są oczekiwane, ponieważ model jest modyfikowany podczas kroków eksportu i konwersji.

* `numMeshes`: Liczba części siatki, gdzie każda część może odwoływać się do pojedynczego materiału.
* `numFaces`: Całkowita liczba _trójkątów_ w całym modelu. Należy zauważyć, że siatka jest triangulacją podczas konwersji.
* `numVertices`: Całkowita liczba wierzchołków w całym modelu.
* `numMaterial`: Całkowita liczba materiałów w całym modelu.
* `numFacesSmallestMesh`: Liczba trójkątów w najmniejszej sieci modelu.
* `numFacesBiggestMesh`: Liczba trójkątów w największych oczkach modelu.
* `numNodes`: Liczba węzłów w grafie sceny modelu.
* `numMeshUsagesInScene`: Liczba siatek odwołań do węzłów. Więcej niż jeden węzeł może odwoływać się do tej samej siatki.
* `maxNodeDepth`: Maksymalna głębokość węzłów w grafie sceny.

### <a name="the-outputinfo-section"></a>Sekcja *outputInfo*

Ta sekcja rejestruje ogólne informacje o wygenerowanych danych wyjściowych.

* `conversionToolVersion`: Wersja konwertera modelu.
* `conversionHash`: Skrót danych w arrAsset, który może współtworzyć do renderowania. Można go użyć, aby zrozumieć, czy usługa konwersji wygenerowała inny wynik po ponownym uruchomieniu tego samego pliku.

### <a name="the-outputstatistics-section"></a>Sekcja *outputStatistics*

Ta sekcja rejestruje informacje obliczane na podstawie przekonwertowanego elementu zawartości.

* `numMeshPartsCreated`: Liczba siatek w arrAsset. Może się to różnić `numMeshes` w `inputStatistics` sekcji, ponieważ proces konwersji ma wpływ na tworzenie wystąpień.
* `numMeshPartsInstanced`: Liczba siatek, które są ponownie używane w arrAsset.
* `recenteringOffset`: Gdy `recenterToOrigin` opcja w [ConversionSettings](configure-model-conversion.md) jest włączona, ta wartość jest tłumaczeniem, który przeniesie przekonwertowany model z powrotem do oryginalnego położenia.
* `boundingBox`: Granice modelu.

## <a name="next-steps"></a>Następne kroki

* [Konwersja modelu](model-conversion.md)
* [Konfigurowanie konwersji modelu](configure-model-conversion.md)
