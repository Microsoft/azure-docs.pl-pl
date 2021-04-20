---
title: Włączanie usuwania nie softowego — udziały plików platformy Azure
description: Dowiedz się, jak włączyć usuwanie nieumyślne w udziałach plików platformy Azure w celu odzyskiwania danych i zapobiegania przypadkowemu usunięciu.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 04/05/2021
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: a0dff310ce4a40b7a66cc548f3c77213f4a10e00
ms.sourcegitcommit: 79c9c95e8a267abc677c8f3272cb9d7f9673a3d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107717027"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Włączanie usuwania nie soft w udziałach plików platformy Azure

Usługa Azure Storage oferuje usuwanie nie soft dla udziałów plików, dzięki czemu można łatwiej odzyskać dane w przypadku ich omyłego usunięcia przez aplikację lub innego użytkownika konta magazynu. Aby dowiedzieć się więcej na temat usuwania nie softowego, zobacz How to prevent accidental deletion of Azure file shares (Jak [zapobiec przypadkowemu usunięciu udziałów plików platformy Azure).](storage-files-prevent-file-share-deletion.md)

W poniższych sekcjach przedstawiono sposób włączania i używania usuwania nie softu dla udziałów plików platformy Azure na istniejącym koncie magazynu:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Wprowadzenie

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
1. Przejdź do konta magazynu i wybierz **pozycję Udziały plików w** obszarze Magazyn **danych.**
1. Wybierz **pozycję Włączone** obok opcji Usuwanie nie **softowe.**
1. Wybierz **opcję Włączone** dla opcji Usuwanie nie **softowe dla wszystkich udziałów plików.**
1. Wybierz pozycję Okres przechowywania udziału plików **w dniach** i wprowadź wybrane liczby.
1. Wybierz **pozycję Zapisz,** aby potwierdzić ustawienia przechowywania danych.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-enable-soft-delete-new-ui.png" alt-text="Zrzut ekranu przedstawiający okienko ustawień usuwania nie softowego konta magazynu. Wyróżnij sekcję usuwanie nie soft delete udziałów plików, włącz przełącznik, ustaw okres przechowywania i zapisz. Spowoduje to włączenie usuwania nie soft dla wszystkich udziałów plików na koncie magazynu.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenia cmdlet usuwania nie softowego są dostępne w wersji 2.1.3 i nowszej modułu interfejsu wiersza [polecenia platformy Azure.](/cli/azure/install-azure-cli)

## <a name="getting-started-with-cli"></a>Wprowadzenie do interfejsu wiersza polecenia

Aby włączyć usuwanie nie soft, należy zaktualizować właściwości usługi klienta plików. Poniższy przykład umożliwia usuwanie nie softowe dla wszystkich udziałów plików na koncie magazynu:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Możesz sprawdzić, czy usuwanie nie softowe jest włączone, i wyświetlić jego zasady przechowywania za pomocą następującego polecenia:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Wymaganie wstępne

Polecenia cmdlet usuwania nie softowego są dostępne w wersji 4.8.0 i nowszej modułu Az.Storage. 

## <a name="getting-started-with-powershell"></a>Wprowadzenie do programu PowerShell

Aby włączyć usuwanie nie soft, należy zaktualizować właściwości usługi klienta plików. Poniższy przykład umożliwia usuwanie nie softowe dla wszystkich udziałów plików na koncie magazynu:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Możesz sprawdzić, czy usuwanie nie jest włączone, i wyświetlić jego zasady przechowywania za pomocą następującego polecenia:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Przywracanie niealgotowego usuniętego udziału plików

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby przywrócić nieużytkalnie usunięty udział plików:

1. Przejdź do konta magazynu i wybierz **pozycję Udziały plików.**
1. W bloku udziału plików włącz opcję Pokaż **usunięte udziały,** aby wyświetlić wszystkie udziały, które zostały usunięte nie soft.

    Spowoduje to wyświetlenie wszystkich udziałów, które są obecnie **w stanie Usunięte.**

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Jeśli kolumna status (kolumna obok kolumny name) jest ustawiona na wartość Deleted (Usunięto), udział plików jest w stanie usunięcia nieustałego. Elementy i zostaną trwale usunięte po określonym okresie przechowywania.":::

1. Wybierz udział, a następnie **cofniesz jego** przywrócenie.

    Możesz potwierdzić, że udział został przywrócony, ponieważ jego stan zmienia się na **Aktywny.**

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Jeśli kolumna status (kolumna obok kolumny name) jest ustawiona na wartość Active (Aktywna), udział plików został przywrócony.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenia cmdlet usuwania nie softowego są dostępne w wersji 2.1.3 interfejsu wiersza polecenia platformy Azure. Aby przywrócić nieustanie usunięty udział plików, należy najpierw `--deleted-version` uzyskać jego wartość. Aby uzyskać wartość , użyj następującego polecenia, aby wyświetlić listę wszystkich usuniętych udziałów dla konta magazynu:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Po zidentyfikowaniu udziału, który chcesz przywrócić, możesz użyć go z następującym poleceniem, aby go przywrócić:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Polecenia cmdlet usuwania nie softowego są dostępne w wersji 4.8.0 i nowszej modułu Az.Storage. Aby przywrócić nieustanie usunięty udział plików, należy najpierw `-DeletedShareVersion` uzyskać jego wartość. Aby uzyskać wartość , użyj następującego polecenia, aby wyświetlić listę wszystkich usuniętych udziałów dla konta magazynu:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Po zidentyfikowaniu udziału, który chcesz przywrócić, możesz użyć go z następującym poleceniem, aby go przywrócić:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Wyłączanie usuwania nie soft

Jeśli chcesz przestać używać usuwania nie soft, postępuj zgodnie z tymi instrukcjami. Aby trwale usunąć udział plików, który został usunięty nietrwałych, należy usunąć go, wyłączyć usuwanie nietrwałych, a następnie usunąć go ponownie. 

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu i wybierz **pozycję Udziały plików w** obszarze Magazyn **danych.**
1. Wybierz link obok opcji **Usuwanie nieaduchomienie**.
1. Wybierz **opcję Wyłączone** dla opcji Usuwanie nie **softowe dla wszystkich udziałów plików.**
1. Wybierz **pozycję Zapisz,** aby potwierdzić ustawienia przechowywania danych.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/files-disable-soft-delete.png" alt-text="Wyłączenie usuwania nie softowego umożliwi natychmiastowe i trwałe usunięcie wszystkich udziałów plików na koncie magazynu zgodnie z potrzebami.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenia cmdlet usuwania nie softowego są dostępne w wersji 2.1.3 interfejsu wiersza polecenia platformy Azure. Aby wyłączyć usuwanie nie softowe na koncie magazynu, możesz użyć następującego polecenia:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Polecenia cmdlet usuwania nie softowego są dostępne w wersji 4.8.0 i nowszej modułu Az.Storage. Aby wyłączyć usuwanie nie softowe na koncie magazynu, możesz użyć następującego polecenia:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat innej formy ochrony i odzyskiwania danych, zobacz nasz artykuł [Overview of share snapshots for Azure Files](storage-snapshots-files.md)( Omówienie migawek udziałów dla Azure Files .