---
title: Konwersja modelu
description: Opisuje proces konwersji modelu do renderowania
author: jakrams
ms.author: jakras
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: e899b249261ea3238695a2e2be6001cb6a9bc763
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "91318062"
---
# <a name="convert-models"></a>Konwertowanie modeli

Zdalne renderowanie na platformie Azure umożliwia renderowanie bardzo złożonych modeli. Aby osiągnąć maksymalną wydajność, należy wstępnie przetworzyć dane tak, aby były w optymalnym formacie. W zależności od ilości danych ten krok może chwilę potrwać. Nie byłoby to praktycznie przydatne, jeśli ten czas był poświęcany podczas ładowania modelu. Ponadto wasteful się powtórzyć ten proces dla wielu sesji. Z tego względu usługa ARR oferuje dedykowaną *usługę konwersji*, którą można uruchomić wcześniej.
Po przekonwertowaniu model można załadować z konta usługi Azure Storage.

## <a name="supported-source-formats"></a>Obsługiwane formaty źródła

Usługa konwersji obsługuje następujące formaty:

- **FBX**  (wersja 2011 do wersji 2020)
- **GLTF** / **GLB** (wersja 2. x)

Istnieją niewielkie różnice między formatami w odniesieniu do konwersji właściwości materiału, jak wymieniono w [mapowaniu materiału rozdziału dla formatów modelu](../../reference/material-mapping.md).

## <a name="the-conversion-process"></a>Proces konwersji

1. [Przygotuj dwa kontenery usługi Azure Blob Storage](blob-storage.md): jeden dla danych wejściowych, jeden dla danych wyjściowych
1. Przekaż model do kontenera wejściowego (opcjonalnie pod ścieżką podrzędną)
1. Wyzwól proces konwersji za pomocą [interfejsu API REST konwersji modelu](conversion-rest-api.md)
1. Sondowanie usługi pod kątem postępu konwersji
1. Po zakończeniu Załaduj model
    - z połączonego konta magazynu (zobacz sekcję "Łączenie kont magazynu" na stronie [Tworzenie konta](../create-an-account.md#link-storage-accounts) , aby połączyć konto magazynu)
    - lub dostarczając *sygnaturę dostępu współdzielonego (SAS)*.

Wszystkie dane modelu (dane wejściowe i wyjściowe) są przechowywane w usłudze Azure Blob Storage. Zdalne renderowanie na platformie Azure zapewnia pełną kontrolę nad zarządzaniem zasobami.

## <a name="pricing"></a>Ceny

Informacje o cenach konwersji można znaleźć na stronie [cennika renderowania zdalnego](https://azure.microsoft.com/pricing/details/remote-rendering) .


## <a name="conversion-parameters"></a>Parametry konwersji

Aby poznać różne opcje konwersji, zobacz [ten rozdział](configure-model-conversion.md).

## <a name="examples"></a>Przykłady

- [Szybki Start: konwertowanie modelu na potrzeby renderowania](../../quickstarts/convert-model.md) to krok po kroku, jak przekonwertować model.
- [Przykładowe skrypty programu PowerShell](../../samples/powershell-example-scripts.md), które demonstrują korzystanie z usługi konwersji, znajdują się w [repozytorium przykładów ARR](https://github.com/Azure/azure-remote-rendering) w folderze *skrypty* .

## <a name="next-steps"></a>Następne kroki

- [Używanie usługi Azure Blob Storage do konwersji modelu](blob-storage.md)
- [Interfejs API REST konwersji modelu](conversion-rest-api.md)
- [Konfigurowanie konwersji modelu](configure-model-conversion.md)
- [Ustalanie układu plików na potrzeby konwersji](layout-files-for-conversion.md)
- [Mapowanie materiałów dla formatów modelu](../../reference/material-mapping.md)
