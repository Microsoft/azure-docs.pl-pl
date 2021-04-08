---
title: StorageBlobSelector — element interfejsu użytkownika
description: Opisuje element interfejsu użytkownika Microsoft. Storage. StorageBlobSelector dla Azure Portal.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/27/2020
ms.author: tomfitz
ms.openlocfilehash: 1085b70df67a3f16a7f7f8c5ae85c9ab271b62ac
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "92754531"
---
# <a name="microsoftstoragestorageblobselector-ui-element"></a>Element interfejsu użytkownika Microsoft. Storage. StorageBlobSelector

Kontrolka służąca do wybierania obiektu BLOB z konta usługi Azure Storage.

## <a name="ui-sample"></a>Przykładowy interfejs użytkownika

Użytkownik zobaczy opcję przeglądania dostępnych obiektów blob magazynu.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-browse.png" alt-text="Microsoft. Storage. StorageBlobSelector — przeglądanie":::

Po wybraniu **przycisku Przeglądaj** użytkownik może wybrać konto magazynu.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-select.png" alt-text="Microsoft. Storage. StorageBlobSelector — wybór magazynu":::

Użytkownik zobaczy kontenery na koncie magazynu i wybierze je.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-containers.png" alt-text="Microsoft. Storage. StorageBlobSelector — Wybieranie kontenera":::

Z kontenera użytkownik może wybrać plik.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-file.png" alt-text="Microsoft. Storage. StorageBlobSelector — plik":::

Formant zostanie zaktualizowany, aby wyświetlić wybraną nazwę pliku.

:::image type="content" source="./media/managed-application-elements/microsoft-storage-storageblobselector-result.png" alt-text="Microsoft. Storage. StorageBlobSelector — Pokaż wybrany plik":::

## <a name="schema"></a>Schemat

```json
{
  "name": "storageBlobSelection",
  "type": "Microsoft.Storage.StorageBlobSelector",
  "visible": true,
  "toolTip": "Select storage blob",
  "label": "Package (.zip, .cspkg)",
  "options": {
    "text": "Select Package"
  },
  "constraints": {
    "allowedFileExtensions": [ "zip", "cspkg" ]
  }
}
```

## <a name="sample-output"></a>Przykładowe dane wyjściowe

```json
{
  "blobName": "test.zip",
  "sasUri": "https://azstorageaccnt1.blob.core.windows.net/container1/test.zip?sp=r&se=2020-10-10T07:46:22Z&sv=2019-12-12&sr=b&sig=X4EL8ZsRmiP1TVxkVfTcGyMj2sHg1zCbFBXsDmnNOyg%3D"
}

```

## <a name="remarks"></a>Uwagi

Właściwość **CONSTRAINT. allowedFileExtensions** Określa dozwolone typy plików.

## <a name="next-steps"></a>Następne kroki

* Wprowadzenie do tworzenia definicji interfejsu użytkownika można znaleźć w temacie [wprowadzenie do CreateUiDefinition](create-uidefinition-overview.md).
* Opis wspólnych właściwości elementów interfejsu użytkownika można znaleźć w temacie [CreateUiDefinition elementy](create-uidefinition-elements.md).
