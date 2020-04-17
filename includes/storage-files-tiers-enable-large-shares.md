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
ms.openlocfilehash: cd7b889560acbe484581f065b641375c222f7ca8
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81536498"
---
Domyślnie standardowe udziały plików mogą obejmować tylko do 5 TiB, chociaż limit udziału można zwiększyć do 100 TiB. Aby to zrobić, funkcja *dużego udziału plików* musi być włączona na poziomie konta magazynu. Konta magazynu w warstwie Premium (konta magazynu*FileStorage)* nie mają flagi funkcji dużego udziału plików, ponieważ wszystkie udziały plików premium są już włączone do inicjowania obsługi administracyjnej do pełnej pojemności 100 TiB.

Duże udziały plików można włączyć tylko na lokalnie nadmiarowych lub strefowych standardowych kontach magazynu. Po włączeniu flagi funkcji dużego udziału plików nie można zmienić poziomu nadmiarowości na magazyn geograficznie nadmiarowy lub magazyn geograficznie nadmiarowy.

Aby włączyć duże udziały plików na istniejącym koncie magazynu, przejdź do widoku **Konfiguracja** w spisie treści konta magazynu i przełącz przełącznik rocker dużego udziału plików na włączony:

![Zrzut ekranu przedstawiający przełącznik rocker udostępniania dużych plików w witrynie Azure portal](media/storage-files-tiers-enable-large-shares/enable-lfs-0.png)

Można również włączyć 100 udziałów plików [`Set-AzStorageAccount`](https://docs.microsoft.com/powershell/module/az.storage/set-azstorageaccount) TiB za pomocą [`az storage account update`](https://docs.microsoft.com/cli/azure/storage/account#az-storage-account-update) polecenia cmdlet programu PowerShell i interfejsu wiersza polecenia interfejsu wiersza polecenia platformy Azure. Aby uzyskać szczegółowe instrukcje dotyczące włączania udziałów dużych plików, zobacz [włączanie i tworzenie dużych udziałów plików](../articles/storage/files/storage-files-how-to-create-large-file-share.md).

Aby dowiedzieć się więcej o tworzeniu udziałów plików na nowych kontach magazynu, zobacz [tworzenie udziału plików platformy Azure](../articles/storage/files/storage-how-to-create-file-share.md).
