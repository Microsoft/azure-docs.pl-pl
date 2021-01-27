---
title: Włącz nietrwałe usuwanie udziałów plików platformy Azure
description: Dowiedz się, jak włączyć usuwanie nietrwałe w udziałach plików platformy Azure na potrzeby odzyskiwania danych i uniemożliwiając Przypadkowe usunięcie.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 12/01/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 22e4d0998cde14d4461141a53f05cbc19d1ab671
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98878973"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Włącz usuwanie nietrwałe w udziałach plików platformy Azure

Usługa Azure Storage oferuje nietrwałe usuwanie udziałów plików, dzięki czemu można łatwiej odzyskać dane w przypadku ich błędnego usunięcia przez aplikację lub innego użytkownika konta magazynu. Aby dowiedzieć się więcej na temat usuwania nietrwałego, zobacz [jak zapobiec przypadkowemu usunięciu udziałów plików platformy Azure](storage-files-prevent-file-share-deletion.md).

W poniższych sekcjach pokazano, jak włączyć i użyć usuwania nietrwałego dla udziałów plików platformy Azure na istniejącym koncie magazynu:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Wprowadzenie

1. Zaloguj się do [Azure Portal](https://portal.azure.com/).
1. Przejdź do konta magazynu i wybierz pozycję **usuwanie nietrwałe** w obszarze **Usługa plików**.
1. Wybierz opcję **włączone** dla **trwałego usunięcia udziału plików**.
1. Wybierz **okres przechowywania udziału plików w dniach** i wprowadź wybraną liczbę.
1. Wybierz pozycję **Zapisz** , aby potwierdzić ustawienia przechowywania danych.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Zrzut ekranu przedstawiający okienko ustawień usuwania nietrwałego konta magazynu. Wyróżnianie sekcji udziały plików, Włączanie przełącznika, Ustawianie okresu przechowywania i zapisywanie. Spowoduje to włączenie usuwania nietrwałego dla wszystkich udziałów plików na koncie magazynu.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenia cmdlet usuwania nietrwałego są dostępne w wersji 2.1.3 i nowszej [modułu interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli?view=azure-cli-latest).

## <a name="getting-started-with-cli"></a>Wprowadzenie do interfejsu wiersza polecenia

Aby włączyć usuwanie nietrwałe, należy zaktualizować właściwości usługi klienta pliku. Poniższy przykład włącza usuwanie nietrwałe dla wszystkich udziałów plików na koncie magazynu:

```azurecli
az storage account file-service-properties update --enable-delete-retention true -n yourStorageaccount -g yourResourceGroup
```

Można sprawdzić, czy funkcja usuwania nietrwałego jest włączona i wyświetlić jej zasady przechowywania przy użyciu następującego polecenia:

```azurecli
az storage account file-service-properties show -n yourStorageaccount -g yourResourceGroup
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Wymaganie wstępne

Polecenia cmdlet usuwania nietrwałego są dostępne w 4.8.0 i nowszych wersjach modułu AZ. Storage. 

## <a name="getting-started-with-powershell"></a>Wprowadzenie do programu PowerShell

Aby włączyć usuwanie nietrwałe, należy zaktualizować właściwości usługi klienta pliku. Poniższy przykład włącza usuwanie nietrwałe dla wszystkich udziałów plików na koncie magazynu:

```azurepowershell-interactive
$rgName = "yourResourceGroupName"
$accountName = "yourStorageAccountName"

Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $true -ShareRetentionDays 7
```

Można sprawdzić, czy funkcja usuwania nietrwałego jest włączona i wyświetlić jej zasady przechowywania przy użyciu następującego polecenia:

```azurepowershell-interactive
Get-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName
```
---

## <a name="restore-soft-deleted-file-share"></a>Przywracanie nietrwałego usuniętego udziału plików

# <a name="portal"></a>[Portal](#tab/azure-portal)

Aby przywrócić nietrwały, usunięty udział plików:

1. Przejdź do konta magazynu i wybierz pozycję **udziały plików**.
1. W bloku udział plików Włącz opcję **Pokaż usunięte udziały** , aby wyświetlić wszystkie udziały, które zostały usunięte.

    Spowoduje to wyświetlenie wszystkich udziałów w stanie **usunięte** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="W kolumnie Stan kolumna obok kolumny Nazwa jest ustawiona na wartość usunięte, a następnie udział plików jest w stanie nietrwałego usunięcia. I zostaną trwale usunięte po upływie określonego okresu przechowywania.":::

1. Wybierz udział i wybierz pozycję **Cofnij usunięcie**. spowoduje to przywrócenie udziału.

    Można potwierdzić, że udział zostanie przywrócony, ponieważ jego stan zmieni się na **aktywny**.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Jeśli kolumna stan, kolumna obok kolumny Nazwa, jest ustawiona na aktywny, udział plików został przywrócony.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenia cmdlet usuwania nietrwałego są dostępne w wersji 2.1.3 interfejsu wiersza polecenia platformy Azure. Aby przywrócić nietrwały usunięty udział plików, należy najpierw pobrać `--deleted-version` wartość udziału. Aby uzyskać tę wartość, użyj następującego polecenia, aby wyświetlić listę wszystkich usuniętych udziałów dla konta magazynu:

```azurecli
az storage share-rm list --storage-account yourStorageaccount --include-deleted
```

Po zidentyfikowaniu udziału, który chcesz przywrócić, można go użyć przy użyciu następującego polecenia, aby go przywrócić:

```azurecli
az storage share-rm restore -n deletedshare --deleted-version 01D64EB9886F00C4 -g yourResourceGroup --storage-account yourStorageaccount
```

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Polecenia cmdlet usuwania nietrwałego są dostępne w 4.8.0 i nowszych wersjach modułu AZ. Storage. Aby przywrócić nietrwały usunięty udział plików, należy najpierw pobrać `-DeletedShareVersion` wartość udziału. Aby uzyskać tę wartość, użyj następującego polecenia, aby wyświetlić listę wszystkich usuniętych udziałów dla konta magazynu:

```azurepowershell-interactive
Get-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -IncludeDeleted
```

Po zidentyfikowaniu udziału, który chcesz przywrócić, można go użyć przy użyciu następującego polecenia, aby go przywrócić:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Wyłącz usuwanie nietrwałe

Jeśli chcesz zatrzymać korzystanie z funkcji usuwania nietrwałego lub trwale usunąć udział plików, postępuj zgodnie z następującymi instrukcjami:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu i wybierz pozycję **usuwanie nietrwałe** w obszarze **Ustawienia**.
1. W obszarze **udziały plików** wybierz pozycję **wyłączone** dla opcji **Usuń nietrwałe dla udziałów plików**.
1. Wybierz pozycję **Zapisz** , aby potwierdzić ustawienia przechowywania danych.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Wyłączenie usuwania nietrwałego umożliwi natychmiastowe i trwałe usunięcie wszystkich udziałów plików na koncie magazynu.":::

# <a name="azure-cli"></a>[Interfejs wiersza polecenia platformy Azure](#tab/azure-cli)

Polecenia cmdlet usuwania nietrwałego są dostępne w wersji 2.1.3 interfejsu wiersza polecenia platformy Azure. Aby wyłączyć usuwanie nietrwałe na koncie magazynu, można użyć następującego polecenia:

```azurecli
az storage account file-service-properties update --enable-delete-retention false -n yourStorageaccount -g yourResourceGroup
```
# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

Polecenia cmdlet usuwania nietrwałego są dostępne w 4.8.0 i nowszych wersjach modułu AZ. Storage. Aby wyłączyć usuwanie nietrwałe na koncie magazynu, można użyć następującego polecenia:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innej formie ochrony i odzyskiwania danych, zapoznaj się z artykułem [Omówienie migawek udziałów dla Azure Files](storage-snapshots-files.md).