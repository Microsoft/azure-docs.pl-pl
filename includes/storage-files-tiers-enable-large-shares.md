---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: storage
author: roygara
ms.service: storage
ms.topic: include
ms.date: 12/27/2019
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 327e86642041a607ada7c1173bb053b12a41832c
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011727"
---
Domyślnie standardowe udziały plików mogą obejmować maksymalnie 5 TiB, chociaż limit udostępniania można zwiększyć do 100 TiB. W tym celu należy włączyć funkcję *dużego udziału plików* na poziomie konta magazynu. Konta usługi Premium Storage (konta magazynu *FileStorage* ) nie mają flagi dużej funkcji udostępniania plików, ponieważ wszystkie udziały plików w warstwie Premium są już włączone do obsługi pełnej pojemności 100 TIB.

Duże udziały plików można włączyć tylko na lokalnie nadmiarowe lub nadmiarowe konta magazynu w warstwie Standardowa. Po włączeniu flagi dużej funkcji udostępniania plików nie można zmienić poziomu nadmiarowości na magazyn Geograficznie nadmiarowy lub geograficznie nadmiarowy.

Aby włączyć duże udziały plików na istniejącym koncie magazynu, przejdź do widoku **Konfiguracja** w spisie treści konta magazynu, a następnie Przełącz Rocker Przełącz duży udział plików na włączony:

![Zrzut ekranu przedstawiający Przełącznik Rocker o dużym udziale plików w Azure Portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Możesz również włączyć udziały plików 100 TiB za pomocą polecenia [`Set-AzStorageAccount`](/powershell/module/az.storage/set-azstorageaccount) cmdlet programu PowerShell i [`az storage account update`](/cli/azure/storage/account#az-storage-account-update) interfejsu CLI platformy Azure. Aby uzyskać szczegółowe instrukcje dotyczące włączania udziałów dużych plików, zobacz [Włączanie i tworzenie dużych udziałów plików](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Aby dowiedzieć się więcej na temat tworzenia udziałów plików na nowych kontach magazynu, zobacz [Tworzenie udziału plików platformy Azure](../articles/storage/files/storage-how-to-create-file-share.md).