---
title: Ustalanie układu plików na potrzeby konwersji
description: Zalecenia dotyczące najlepszego umieszczania plików w kontenerze wejściowej.
author: MalcolmTyrrell
ms.author: matyrr
ms.date: 09/03/2020
ms.topic: how-to
ms.openlocfilehash: 9a094755dfb9381b1e4d6abdf1c0e6342d7427c1
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107308443"
---
# <a name="laying-out-files-for-conversion"></a>Ustalanie układu plików na potrzeby konwersji

Aby poprawnie przetworzyć element zawartości, usługa konwersji musi być w stanie znaleźć wszystkie pliki wejściowe.
Składają się one z pliku głównego zasobu, który jest konwertowany i zwykle inne pliki, do których odwołują się ścieżki w pliku zasobów.
Żądanie konwersji elementu zawartości ma dwa parametry, które określają, w jaki sposób usługa konwersji znajdzie te pliki: `settings.inputLocation.blobPrefix` (opcjonalnie) i `settings.inputLocation.relativeInputAssetPath` .
Są one w pełni udokumentowane na stronie [interfejsu API REST konwersji](conversion-rest-api.md) .
Na potrzeby ułożenia plików warto zwrócić uwagę na to, że program `BlobPrefix` określi kompletny zestaw plików, które są dostępne dla usługi konwersji podczas przetwarzania elementu zawartości.

> [!Note]
> Usługa pobierze wszystkie pliki w ramach danych wejściowych. Prefiksem obiektu BLOB. Upewnij się, że nazwy plików i ścieżki nie przekraczają [limitów długości ścieżki systemu Windows](https://docs.microsoft.com/windows/win32/fileio/maximum-file-path-limitation) , aby uniknąć problemów z usługą. 

## <a name="placing-files-so-they-can-be-found"></a>Umieszczanie plików, aby można je było znaleźć

Gdy zasób źródłowy korzysta z zewnętrznych plików, ścieżki do tych plików będą przechowywane w obrębie elementu zawartości.
Usługa konwersji musi interpretować te ścieżki w systemie plików, który różni się od oryginalnego systemu plików elementu zawartości.
Jeśli ścieżki są przechowywane jako ścieżki względne, a względna lokalizacja między źródłowym zasobem a plikiem, do którego się odwołuje, jest niezmieniona, a usługa konwersji może znaleźć plik, do którego istnieje odwołanie.

> [!Note]
> Zalecamy umieszczenie plików w kontenerze wejściowym, aby względne lokalizacje plików były takie same, jak podczas tworzenia elementu zawartości.

> [!Note]
> Wolisz tworzyć zasoby, które mają ścieżki względne.
> Samouczek dotyczący [konfigurowania materiałów dla 3ds Max](../../tutorials/modeling/3dsmax-material-setup.md) to 3ds Max przykład, aby upewnić się, że zasób używa ścieżek względnych.

## <a name="finding-textures"></a>Znajdowanie tekstur

Ze względu na wiele sposobów generowania elementów zawartości, usługa konwersji musi być elastyczna.
W szczególności musi obsługiwać sytuacje, w których ścieżki w elemencie zawartości i lokalizacji tekstury nie są dokładnie zgodne.
Przykładem jest wygenerowanie zasobów zawierających ścieżki bezwzględne, ponieważ te ścieżki nigdy nie są zgodne z systemem plików używanym przez usługę konwersji.
Aby obsłużyć tę sytuację między innymi, używamy najlepszego podejścia do znajdowania tekstur.

Algorytm lokalizowania tekstur jest następujący: w przypadku ścieżki przechowywanej w elemencie zawartości Znajdź najdłuższy sufiks ścieżki podrzędnej, który jest używany jako ścieżka względna z lokalizacji zasobu źródłowego, jako docelowy plik, który istnieje.
Jeśli Ścieżka podrzędna (w tym cała ścieżka) jest przeznaczona dla pliku, tekstura jest uważana za brakującą.

Rozważmy następujący system plików contrived: 
```
G:\CONVERSION
├───Assets
│   │   myAsset.fbx
│   │   myTexture.png            <- A
│   │
│   └───Textures
│       │   myTexture.png        <- B
│       │
│       └───MyAssetTextures
│               myTexture.png    <- C
│
└───Textures
    │   myTexture.png            <- D
    │
    └───MyAssetTextures
            myTexture.png        <- E
```
Jeśli element webasset. FBX odwołuje się do tekstury ze ścieżką względną `..\Textures\MyAssetTextures\myTexture.png` , usługa konwersji będzie używać pliku E. W rzeczywistości może użyć dowolnego z plików A, C i E, jeśli istnieją, a plik E jest preferowany, ponieważ znajduje się z najdłuższym sufiksem.
Pliki B i D nigdy nie będą używane, ponieważ `Textures\myTexture.png` nie jest częścią żadnego sufiksu przechowywanej ścieżki.
Jeśli element zawartości zamiast nich zawierał ścieżki `H:\Foo\Bar\Textures\MyAssetTextures\myTexture.png` lub `..\..\..\Foo\Bar\Textures\MyAssetTextures\myTexture.png` , usługa konwersji będzie mogła znaleźć pliki a i C, jeśli istnieją (przede wszystkim w c). Jednak E nie można znaleźć w ten sposób, a plik będzie musiał zostać przeniesiony.
Można to naprawić przez przeniesienie folderu tekstury obok elementu zawartości.

> [!Note]
> Jeśli tekstury nie są znalezione, możliwe jest zagwarantowanie, że element zawartości jest równorzędny dla niektórych poddrzewa zawierającego tekstury.

## <a name="next-steps"></a>Następne kroki

- [Konwersja modelu](model-conversion.md)
