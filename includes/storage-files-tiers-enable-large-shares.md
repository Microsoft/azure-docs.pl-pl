---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 02/03/2021
ms.author: fauhse
ms.custom: include file
ms.openlocfilehash: a086aae35c9a800c6a4cfc3e872a34438bc84095
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99569581"
---
Domyślnie standardowe udziały plików mogą obejmować maksymalnie 5 TiB, ale można zwiększyć limit udostępniania do 100 TiB. Aby zwiększyć limit udostępniania, Włącz **duży udział plików** na koncie magazynu. Konta usługi Premium Storage (konta magazynu *FileStorage* ) nie mają flagi dużej funkcji udostępniania plików, ponieważ wszystkie udziały plików w warstwie Premium są już włączone do obsługi pełnej pojemności 100-TIB.

Duże udziały plików można włączyć tylko na lokalnie nadmiarowe lub nadmiarowe konta magazynu w warstwie Standardowa. Po włączeniu flagi dużej funkcji udostępniania plików nie można zmienić poziomu nadmiarowości na magazyn Geograficznie nadmiarowy lub geograficznie nadmiarowy.

Aby włączyć duże udziały plików na istniejącym koncie magazynu, przejdź do obszaru **udziały plików** w spisie treści konta magazynu.
W tym bloku wybierz pozycję **Udostępnij pojemność**, Zmień pojemność udziału na **100 TIB** i wybierz pozycję **Zapisz**.

:::image type="content" source="media/storage-files-tiers-enable-large-shares/enable-lfs.png" alt-text="Zrzut ekranu przedstawiający ustawienie wyboru Włącz duże udziały plików w Azure Portal." lightbox="media/storage-files-tiers-enable-large-shares/increase-share-capacity.png":::

Możesz również włączyć udziały plików 100-TiB za pomocą polecenia [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) cmdlet programu PowerShell i [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) interfejsu CLI platformy Azure. Aby uzyskać szczegółowe instrukcje dotyczące włączania udziałów dużych plików, zobacz [Włączanie i tworzenie dużych udziałów plików](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Aby dowiedzieć się więcej na temat tworzenia udziałów plików na nowych kontach magazynu, zobacz [Tworzenie udziału plików platformy Azure](../articles/storage/files/storage-how-to-create-file-share.md).