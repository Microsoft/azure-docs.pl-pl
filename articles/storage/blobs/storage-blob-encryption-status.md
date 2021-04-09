---
title: Sprawdzanie stanu szyfrowania obiektu BLOB w usłudze Azure Storage
description: Dowiedz się, jak używać Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby sprawdzić, czy dany obiekt BLOB jest szyfrowany. Jeśli obiekt BLOB nie jest zaszyfrowany, Dowiedz się, jak wymusić szyfrowanie przez pobranie i ponowne przekazanie obiektu BLOB przy użyciu AzCopy.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 11/26/2019
ms.author: tamram
ms.reviewer: ozgun
ms.subservice: common
ms.openlocfilehash: ee54357250e3f31ef9db633d933d897fff362f48
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98878565"
---
# <a name="check-the-encryption-status-of-a-blob"></a>Sprawdzanie stanu szyfrowania obiektu BLOB

Każdy blokowy obiekt BLOB, dołączany obiekt BLOB lub stronicowy obiekt BLOB, który został zapisany w usłudze Azure Storage po 20 października 2017, jest szyfrowany przy użyciu szyfrowania usługi Azure Storage. Obiekty blob utworzone przed tą datą nadal są szyfrowane przez proces w tle.

W tym artykule pokazano, jak ustalić, czy dany obiekt BLOB został zaszyfrowany.

## <a name="check-a-blobs-encryption-status"></a>Sprawdź stan szyfrowania obiektu BLOB

Użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, aby określić, czy obiekt BLOB jest szyfrowany bez kodu.

### <a name="azure-portal"></a>[Witryna Azure Portal](#tab/portal)

Aby użyć Azure Portal do sprawdzenia, czy obiekt BLOB został zaszyfrowany, wykonaj następujące kroki:

1. W witrynie Azure Portal przejdź do swojego konta magazynu.
1. Wybierz **kontenery** , aby przejść do listy kontenerów na koncie.
1. Znajdź obiekt BLOB i Wyświetl jego kartę **Przegląd** .
1. Wyświetl Właściwość **zaszyfrowaną serwer** . Jeśli **wartość jest równa true**, jak pokazano na poniższej ilustracji, obiekt BLOB jest szyfrowany. Zwróć uwagę, że właściwości obiektu BLOB zawierają również datę i godzinę utworzenia obiektu BLOB.

    ![Zrzut ekranu przedstawiający sposób sprawdzania właściwości zaszyfrowanej serwera w Azure Portal](media/storage-blob-encryption-status/blob-encryption-property-portal.png)

### <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Aby użyć programu PowerShell do sprawdzenia, czy obiekt BLOB został zaszyfrowany, sprawdź Właściwość **IsServerEncrypted** obiektu BLOB. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```powershell
$account = Get-AzStorageAccount -ResourceGroupName <resource-group> `
    -Name <storage-account>
$blob = Get-AzStorageBlob -Context $account.Context `
    -Container <container> `
    -Blob <blob>
$blob.ICloudBlob.Properties.IsServerEncrypted
```

Aby określić, kiedy obiekt BLOB został utworzony, sprawdź wartość **utworzonej** właściwości:

```powershell
$blob.ICloudBlob.Properties.IsServerEncrypted
```

### <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/cli)

Aby użyć interfejsu wiersza polecenia platformy Azure do sprawdzenia, czy obiekt BLOB został zaszyfrowany, sprawdź Właściwość **IsServerEncrypted** obiektu BLOB. Pamiętaj, aby zastąpić wartości symboli zastępczych w nawiasach ostrych własnymi wartościami:

```azurecli-interactive
az storage blob show \
    --account-name <storage-account> \
    --container-name <container> \
    --name <blob> \
    --query "properties.serverEncrypted"
```

Aby określić, kiedy obiekt BLOB został utworzony, sprawdź wartość **utworzonej** właściwości.

---

## <a name="force-encryption-of-a-blob"></a>Wymuś szyfrowanie obiektu BLOB

Jeśli obiekt BLOB, który został utworzony przed 20 października 2017, nie został jeszcze zaszyfrowany przez proces w tle, można wymusić natychmiastowe szyfrowanie, pobierając i ponownie przekazując obiekt BLOB. Prostym sposobem wykonania tej czynności jest AzCopy.

Aby pobrać obiekt BLOB do lokalnego systemu plików przy użyciu AzCopy, użyj następującej składni:

```
azcopy copy 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-path>' '<local-file-path>'

Example:
azcopy copy 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt' 'C:\temp\blob1.txt'
```

Aby przekazać obiekt BLOB do usługi Azure Storage za pomocą AzCopy, użyj następującej składni:

```
azcopy copy '<local-file-path>' 'https://<storage-account-name>.<blob or dfs>.core.windows.net/<container-name>/<blob-name>'

Example:
azcopy copy 'C:\temp\blob1.txt' 'https://storagesamples.blob.core.windows.net/sample-container/blob1.txt'
```

Aby uzyskać więcej informacji o korzystaniu z AzCopy do kopiowania danych obiektów blob, zobacz [transfer danych za pomocą AzCopy i usługi BLOB Storage](../common/storage-use-azcopy-v10.md#transfer-data).

## <a name="next-steps"></a>Następne kroki

[Szyfrowanie w usłudze Azure Storage dla danych magazynowanych](../common/storage-service-encryption.md)
