---
title: Zarządzanie domyślną warstwą dostępu konta usługi Azure Storage
description: Dowiedz się, jak zmienić domyślną warstwę dostępu konta GPv2 lub Blob Storage
author: mhopkins-msft
ms.author: mhopkins
ms.date: 01/11/2021
ms.service: storage
ms.subservice: common
ms.topic: how-to
ms.reviewer: klaasl
ms.openlocfilehash: 637f748882b3ac84127c8b71761a06629e1e0957
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100653836"
---
# <a name="manage-the-default-access-tier-of-an-azure-storage-account"></a>Zarządzanie domyślną warstwą dostępu konta usługi Azure Storage

Każde konto usługi Azure Storage ma domyślną warstwę dostępu, gorącą lub chłodną. Warstwa dostępu jest przypisywana podczas tworzenia konta magazynu. Domyślna Warstwa dostępu to gorąca.

Domyślną warstwę konta można zmienić, ustawiając atrybut **Warstwa dostępu** na koncie magazynu. Zmiana warstwy konta ma zastosowanie do wszystkich obiektów przechowywanych na koncie, które nie mają jawnie ustawionej warstwy. Przełączenie warstwy konta z gorąca na chłodnie powoduje operacje zapisu (na 10 000) dla wszystkich obiektów Blob bez zestawu warstwy tylko na kontach GPv2 i przełączanie z chłodnej na gorącą operację odczytu (za 10 000) i pobieranie danych (za GB) dla wszystkich obiektów BLOB w ramach kont Blob Storage i GPv2.

W przypadku obiektów blob z zestawem warstwy na poziomie obiektu warstwa konta nie ma zastosowania. Warstwę Archiwum można stosować tylko na poziomie obiektu. W każdej chwili można przełączać się między warstwami dostępu.

Domyślną warstwę dostępu można zmienić po utworzeniu konta magazynu, wykonując poniższe kroki.

## <a name="change-the-default-account-access-tier-of-a-gpv2-or-blob-storage-account"></a>Zmień domyślną warstwę dostępu konta GPv2 lub konta Blob Storage

W poniższych scenariuszach użyto Azure Portal lub programu PowerShell:

# <a name="portal"></a>[Portal](#tab/portal)

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. W Azure Portal Wyszukaj i wybierz pozycję **wszystkie zasoby**.

1. Wybierz swoje konto magazynu.

1. W obszarze **Ustawienia** wybierz pozycję **Konfiguracja** , aby wyświetlić i zmienić konfigurację konta.

1. Wybierz odpowiednią warstwę dostępu do własnych potrzeb: Ustaw **warstwę dostępu** na **chłodna** lub **gorąca**.

1. Kliknij przycisk **Zapisz** u góry.

![Zmień domyślną warstwę konta w Azure Portal](media/manage-account-default-access-tier/account-tier.png)

# <a name="powershell"></a>[Program PowerShell](#tab/powershell)

Za pomocą poniższego skryptu programu PowerShell można zmienić warstwę konta. `$rgName`Zmienna musi być zainicjowana przy użyciu nazwy grupy zasobów. `$accountName`Zmienna musi zostać zainicjowana przy użyciu nazwy konta magazynu.

```powershell
#Initialize the following with your resource group and storage account names
$rgName = ""
$accountName = ""

#Change the storage account tier to hot
Set-AzStorageAccount -ResourceGroupName $rgName -Name $accountName -AccessTier Hot
```

---

## <a name="next-steps"></a>Następne kroki

- [Jak zarządzać warstwą obiektu BLOB na koncie usługi Azure Storage](../blobs/manage-access-tier.md)
- [Określanie, czy wydajność Premium będzie korzystna dla aplikacji](../blobs/storage-blob-performance-tiers.md)
- [Ocena użycia bieżących kont magazynu przez włączenie metryk usługi Azure Storage](../blobs/monitor-blob-storage.md)
