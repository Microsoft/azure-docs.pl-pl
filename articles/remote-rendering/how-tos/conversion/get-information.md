---
title: Uzyskiwanie informacji o konwersjach
description: Uzyskiwanie informacji o konwersjach
author: malcolmtyrrell
ms.author: matyrr
ms.date: 03/05/2020
ms.topic: how-to
ms.openlocfilehash: 89ec0ad40822785457e988cf9e0f9bd6d00ed81f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91576629"
---
# <a name="get-information-about-conversions"></a>Uzyskiwanie informacji o konwersjach

## <a name="information-about-a-conversion-the-result-file"></a>Informacje o konwersji: plik wynikowy

Gdy usługa konwersji konwertuje element zawartości, zapisuje podsumowanie wszelkich problemów do pliku wynikowego. Na przykład jeśli plik `buggy.gltf` zostanie przekonwertowany, kontener wyjściowy będzie zawierać plik o nazwie `buggy.result.json` .

Plik wynik zawiera listę błędów i ostrzeżeń, które wystąpiły podczas konwersji i zawiera podsumowanie wyników, które są jednym z `succeeded` , `failed` lub `succeeded with warnings` .
Plik wynikowy jest strukturalny jako tablica obiektów JSON, z których każdy ma właściwość String, która jest jedną z `warning` , `error` ,, `internal warning` `internal error` i `result` . Wystąpił tylko jeden błąd (albo `error` `internal error` ) i będzie on zawsze taki `result` .

## <a name="example-result-file"></a>Przykładowy plik *wyników*

W poniższym przykładzie opisano konwersję, która pomyślnie wygenerowała arrAsset. Jednak ze względu na brak tekstury, arrAsset wyniki mogą nie być zgodne z oczekiwaniami.

```JSON
[
  {"warning":"4004","title":"Missing texture","details":{"texture":"buggy_baseColor.png","material":"buggy_col"}},
  {"result":"succeeded with warnings"}
]
```

## <a name="information-about-a-converted-model-the-info-file"></a>Informacje o przekonwertowanym modelu: plik info

Plik arrAsset utworzony przez usługę konwersji jest przeznaczony wyłącznie do użytku przez usługę renderowania. Mogą jednak wystąpić sytuacje, w których chcesz uzyskać dostęp do informacji o modelu bez rozpoczynania sesji renderowania. W celu obsługi tego przepływu pracy usługa konwersji umieszcza plik JSON obok pliku arrAsset w kontenerze danych wyjściowych. Na przykład jeśli plik `buggy.gltf` zostanie przekonwertowany, kontener wyjściowy będzie zawierać plik o nazwie `buggy.info.json` obok przekonwertowanego elementu zawartości `buggy.arrAsset` . Zawiera informacje o modelu źródłowym, przekonwertowanym modelu i o samej konwersji.

## <a name="example-info-file"></a>Przykładowy plik *informacyjny*

Oto przykładowy plik *informacyjny* tworzony przez konwersję pliku o nazwie `buggy.gltf` :

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
    "materialOverrides": {
        "numOverrides": 4,
        "numOverriddenMaterials": 4
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

### <a name="the-materialoverrides-section"></a>Sekcja *materialOverrides*

Ta sekcja zawiera informacje o [przesłanianiu materiału](override-materials.md) w przypadku, gdy plik zastąpień materiału został dostarczony do usługi konwersji.
Zawiera ona następujące informacje:
* `numOverrides`: Liczba wpisów zastąpień odczytanych z pliku zastąpienia materiału.
* `numOverriddenMaterials`: Liczba zasłoniętych materiałów.

### <a name="the-inputstatistics-section"></a>Sekcja *inputStatistics*

Ta sekcja zawiera informacje dotyczące sceny źródłowej. Często występują rozbieżności między wartościami w tej sekcji a odpowiednikami wartości w narzędziu, które utworzyły Model źródłowy. Takie różnice są oczekiwane, ponieważ model jest modyfikowany podczas kroków eksportu i konwersji.

* `numMeshes`: Liczba części siatki, gdzie każda część może odwoływać się do pojedynczego materiału.
* `numFaces`: Całkowita liczba _trójkątów_ w całym modelu. Należy zauważyć, że siatka jest triangulacją podczas konwersji. Ta liczba ma wpływ na limit wielokąta w [standardowym rozmiarze serwera renderowania](../../reference/vm-sizes.md#how-the-renderer-evaluates-the-number-of-polygons).
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

## <a name="deprecated-features"></a>Przestarzałe funkcje

Usługa konwersji zapisuje pliki `stdout.txt` i `stderr.txt` do kontenera wyjściowego, a te dane były jedynym źródłem ostrzeżeń i błędów.
Te pliki są obecnie przestarzałe. Zamiast tego należy użyć [plików wyników](#information-about-a-conversion-the-result-file) do tego celu.

## <a name="next-steps"></a>Następne kroki

* [Konwersja modelu](model-conversion.md)
* [Konfigurowanie konwersji modelu](configure-model-conversion.md)
