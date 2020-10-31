---
title: Włącz nietrwałe usuwanie udziałów plików platformy Azure
description: Dowiedz się, jak włączyć usuwanie nietrwałe w udziałach plików platformy Azure na potrzeby odzyskiwania danych i uniemożliwiając Przypadkowe usunięcie.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 05/28/2020
ms.author: rogarana
ms.subservice: files
services: storage
ms.openlocfilehash: 7defa8611080027a67a0d1db1daa4c4a9d44edfe
ms.sourcegitcommit: 857859267e0820d0c555f5438dc415fc861d9a6b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93126145"
---
# <a name="enable-soft-delete-on-azure-file-shares"></a>Włącz usuwanie nietrwałe w udziałach plików platformy Azure

Usługa Azure Storage oferuje nietrwałe usuwanie udziałów plików, dzięki czemu można łatwiej odzyskać dane w przypadku ich błędnego usunięcia przez aplikację lub innego użytkownika konta magazynu. Aby dowiedzieć się więcej na temat usuwania nietrwałego, zobacz [jak zapobiec przypadkowemu usunięciu udziałów plików platformy Azure](storage-files-prevent-file-share-deletion.md).

W poniższych sekcjach pokazano, jak włączyć i użyć usuwania nietrwałego dla udziałów plików platformy Azure na istniejącym koncie magazynu:

# <a name="portal"></a>[Portal](#tab/azure-portal)

## <a name="getting-started"></a>Wprowadzenie

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).
1. Przejdź do konta magazynu i wybierz pozycję **usuwanie nietrwałe** w obszarze **Usługa plików** .
1. Wybierz opcję **włączone** dla **trwałego usunięcia udziału plików** .
1. Wybierz **okres przechowywania udziału plików w dniach** i wprowadź wybraną liczbę.
1. Wybierz pozycję **Zapisz** , aby potwierdzić ustawienia przechowywania danych.

:::image type="content" source="media/storage-how-to-recover-deleted-account/enable-soft-delete-files.png" alt-text="Zrzut ekranu przedstawiający okienko ustawień usuwania nietrwałego konta magazynu. Wyróżnianie sekcji udziały plików, Włączanie przełącznika, Ustawianie okresu przechowywania i zapisywanie. Spowoduje to włączenie usuwania nietrwałego dla wszystkich udziałów plików na koncie magazynu.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

## <a name="prerequisite"></a>Wymaganie wstępne

Polecenia cmdlet usuwania nietrwałego są dostępne w wersji [3.0.0](https://www.powershellgallery.com/packages/Az.Storage/3.0.0) modułu AZ. Storage. 

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

1. Przejdź do konta magazynu i wybierz pozycję **udziały plików** .
1. W bloku udział plików Włącz opcję **Pokaż usunięte udziały** , aby wyświetlić wszystkie udziały, które zostały usunięte.

    Spowoduje to wyświetlenie wszystkich udziałów w stanie **usunięte** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/undelete-file-share.png" alt-text="Zrzut ekranu przedstawiający okienko ustawień usuwania nietrwałego konta magazynu. Wyróżnianie sekcji udziały plików, Włączanie przełącznika, Ustawianie okresu przechowywania i zapisywanie. Spowoduje to włączenie usuwania nietrwałego dla wszystkich udziałów plików na koncie magazynu.":::

1. Wybierz udział i wybierz pozycję **Cofnij usunięcie** . spowoduje to przywrócenie udziału.

    Można potwierdzić, że udział zostanie przywrócony, ponieważ jego stan zmieni się na **aktywny** .

    :::image type="content" source="media/storage-how-to-recover-deleted-account/restored-file-share.png" alt-text="Zrzut ekranu przedstawiający okienko ustawień usuwania nietrwałego konta magazynu. Wyróżnianie sekcji udziały plików, Włączanie przełącznika, Ustawianie okresu przechowywania i zapisywanie. Spowoduje to włączenie usuwania nietrwałego dla wszystkich udziałów plików na koncie magazynu.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Polecenia cmdlet usuwania nietrwałego są dostępne w wersji 3.0.0 modułu AZ. Storage. Aby przywrócić nietrwały, usunięty udział plików, użyj następującego polecenia:

```azurepowershell-interactive
Restore-AzRmStorageShare -ResourceGroupName $rgname -StorageAccountName $accountName -DeletedShareVersion 01D5E2783BDCDA97
```
---

## <a name="disable-soft-delete"></a>Wyłącz usuwanie nietrwałe

Jeśli chcesz zatrzymać korzystanie z funkcji usuwania nietrwałego lub trwale usunąć udział plików, postępuj zgodnie z następującymi instrukcjami:

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do konta magazynu i wybierz pozycję **usuwanie nietrwałe** w obszarze **Ustawienia** .
1. W obszarze **udziały plików** wybierz pozycję **wyłączone** dla opcji **Usuń nietrwałe dla udziałów plików** .
1. Wybierz pozycję **Zapisz** , aby potwierdzić ustawienia przechowywania danych.

    :::image type="content" source="media/storage-how-to-recover-deleted-account/disable-soft-delete-files.png" alt-text="Zrzut ekranu przedstawiający okienko ustawień usuwania nietrwałego konta magazynu. Wyróżnianie sekcji udziały plików, Włączanie przełącznika, Ustawianie okresu przechowywania i zapisywanie. Spowoduje to włączenie usuwania nietrwałego dla wszystkich udziałów plików na koncie magazynu.":::

# <a name="powershell"></a>[PowerShell](#tab/azure-powershell)

Polecenia cmdlet usuwania nietrwałego są dostępne w wersji 3.0.0 modułu AZ. Storage. Aby wyłączyć usuwanie nietrwałe na koncie magazynu, można użyć następującego polecenia:

```azurepowershell-interactive
Update-AzStorageFileServiceProperty -ResourceGroupName $rgName -StorageAccountName $accountName -EnableShareDeleteRetentionPolicy $false
```
---

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o innej formie ochrony i odzyskiwania danych, zapoznaj się z artykułem [Omówienie migawek udziałów dla Azure Files](storage-snapshots-files.md).
