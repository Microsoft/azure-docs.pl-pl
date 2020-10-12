---
title: Interfejs API REST konwersji zasobów
description: Opisuje sposób konwersji elementu zawartości za pomocą interfejsu API REST
author: florianborn71
ms.author: flborn
ms.date: 02/04/2020
ms.topic: how-to
ms.openlocfilehash: 5c638b434ceb31b57689b11971f48eb322b94726
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87985618"
---
# <a name="use-the-model-conversion-rest-api"></a>Używanie interfejsu API REST konwersji modelu

Usługa [konwersji modelu](model-conversion.md) jest kontrolowana za pomocą [interfejsu API REST](https://en.wikipedia.org/wiki/Representational_state_transfer). W tym artykule opisano szczegóły interfejsu API usługi konwersji.

## <a name="regions"></a>Regiony

Zapoznaj się z [listą regionów dostępnych](../../reference/regions.md) dla podstawowych adresów URL, do których mają być wysyłane żądania.

## <a name="common-headers"></a>Wspólne nagłówki

### <a name="common-request-headers"></a>Typowe nagłówki żądań

Te nagłówki muszą być określone dla wszystkich żądań:

- Nagłówek **autoryzacji** musi mieć wartość "Bearer [*token*]", gdzie [*token*] to [token dostępu do usługi](../tokens.md).

### <a name="common-response-headers"></a>Typowe nagłówki odpowiedzi

Wszystkie odpowiedzi zawierają następujące nagłówki:

- Nagłówek **MS-CV** zawiera unikatowy ciąg, którego można użyć do śledzenia wywołania w ramach usługi.

## <a name="endpoints"></a>Punkty końcowe

Usługa konwersji oferuje trzy punkty końcowe interfejsu API REST:

- Rozpocznij konwersję modelu przy użyciu konta magazynu połączonego z kontem renderowania zdalnego platformy Azure. 
- Rozpocznij konwersję modelu przy użyciu dostarczonych *sygnatur dostępu współdzielonego (SAS)*.
- Zbadaj stan konwersji

### <a name="start-conversion-using-a-linked-storage-account"></a>Rozpocznij konwersję przy użyciu połączonego konta magazynu
Twoje konto renderowania zdalnego platformy Azure musi mieć dostęp do podanego konta magazynu, wykonując czynności opisane w sekcji jak [połączyć konta magazynu](../create-an-account.md#link-storage-accounts).

| Punkt końcowy | Metoda |
|-----------|:-----------|
| /V1/accounts/**accountID**/conversions/Create | POST |

Zwraca identyfikator trwającej konwersji, opakowany w dokument JSON. Nazwa pola to "conversionId".

#### <a name="request-body"></a>Treść żądania

> [!NOTE]
> Wszystkie elementy w obszarze `input.folderPath` zostaną pobrane w celu przeprowadzenia konwersji na platformie Azure. Jeśli `input.folderPath` nie jest określony, cała zawartość kontenera zostanie pobrana. Wszystkie obiekty blob i foldery pobrane do pobrania muszą mieć [prawidłowe nazwy plików systemu Windows](https://docs.microsoft.com/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>"
    }
}
```
### <a name="start-conversion-using-provided-shared-access-signatures"></a>Rozpocznij konwersję przy użyciu dostarczonych sygnatur dostępu współdzielonego
Jeśli konto ARR nie jest połączone z kontem magazynu, ten interfejs REST umożliwia uzyskanie dostępu przy użyciu *sygnatur dostępu współdzielonego (SAS)*.

| Punkt końcowy | Metoda |
|-----------|:-----------|
| /V1/accounts/**accountID**/conversions/createWithSharedAccessSignature | POST |

Zwraca identyfikator trwającej konwersji, opakowany w dokument JSON. Nazwa pola to `conversionId` .

#### <a name="request-body"></a>Treść żądania

Treść żądania jest taka sama jak w przypadku wywołania Create REST powyżej, ale dane wejściowe i wyjściowe zawierają *tokeny sygnatur dostępu współdzielonego (SAS)*. Te tokeny zapewniają dostęp do konta magazynu na potrzeby odczytywania danych wejściowych i zapisywania wyników konwersji.

> [!NOTE]
> Te tokeny URI sygnatury dostępu współdzielonego to ciągi zapytania, a nie pełny identyfikator URI. 

> [!NOTE]
> Wszystkie elementy w obszarze `input.folderPath` zostaną pobrane w celu przeprowadzenia konwersji na platformie Azure. Jeśli `input.folderPath` nie jest określony, cała zawartość kontenera zostanie pobrana. Wszystkie obiekty blob i foldery pobrane do pobrania muszą mieć [prawidłowe nazwy plików systemu Windows](https://docs.microsoft.com/windows/win32/fileio/naming-a-file#naming-conventions).

```json
{
    "input":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container containing your input asset data>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the input blob container>",
        "inputAssetPath" : "<path to the model in the input blob container relative to the folderPath (or container root if no folderPath is specified)>",
        "containerReadListSas" : "<a container SAS token which gives read and list access to the given input blob container>"
    },
    "output":
    {
        "storageAccountname": "<the name of a connected storage account - this does not include the domain suffix (.blob.core.windows.net)>",
        "blobContainerName": "<the name of the blob container where the converted asset will be copied to>",
        "folderPath": "<optional: can be omitted or empty - a subpath in the output blob container. Will contain the asset and log files>",
        "outputAssetFileName": "<optional: can be omitted or empty. The filename of the converted asset. If provided the filename needs to end in .arrAsset>",
        "containerWriteSas" : "<a container SAS token which gives write access to the given output blob container>"
    }
}
```

### <a name="poll-conversion-status"></a>Stan konwersji sondy
Stan trwającej konwersji rozpoczętej z jednym z powyższych wywołań REST można zbadać przy użyciu następującego interfejsu:


| Punkt końcowy | Metoda |
|-----------|:-----------|
| /V1/accounts/**accountID**/conversions/**conversionId** | GET |

Zwraca dokument JSON z polem "status", które może mieć następujące wartości:

- Utworzony
- Uruchomion
- Prawnego
- Spraw

Jeśli stan ma wartość "Niepowodzenie", będzie istnieć dodatkowe pole "Error" z podpolem "Message" zawierającym informacje o błędzie. Dodatkowe dzienniki zostaną przekazane do kontenera danych wyjściowych.

## <a name="next-steps"></a>Następne kroki

- [Używanie usługi Azure Blob Storage do konwersji modelu](blob-storage.md)
- [Konwersja modelu](model-conversion.md)
