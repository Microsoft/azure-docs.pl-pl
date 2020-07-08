---
title: Przeglądanie stanu zadania importowania/eksportowania platformy Azure — Wersja 1 | Microsoft Docs
description: Dowiedz się, jak używać plików dziennika utworzonych podczas wykonywania zadania importowania lub eksportowania, aby zobaczyć stan zadania importu/eksportu.
author: twooley
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: 5ab0dd7f0e0ed90c205c37ddbb7ea3a9fca74ae9
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85512256"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Przeglądanie stanu zadania importowania/eksportowania platformy Azure przy użyciu plików dziennika kopiowania
Gdy usługa Microsoft Azure Import/Export przetwarza dyski skojarzone z zadaniem importu lub eksportu, zapisuje pliki dziennika kopiowania na koncie magazynu lub z którego są importowane lub eksportowane obiekty blob. Plik dziennika zawiera szczegółowy stan każdego importowanego lub wyeksportowanego pliku. Adres URL każdego pliku dziennika kopiowania jest zwracany podczas wykonywania zapytania o stan ukończonego zadania; Zobacz [pobieranie zadania](https://docs.microsoft.com/rest/api/storageimportexport/Jobs/Get) , aby uzyskać więcej informacji.  

## <a name="example-urls"></a>Przykładowe adresy URL

Poniżej przedstawiono przykładowe adresy URL dla plików dziennika kopiowania dla zadania importowania z dwoma dyskami:  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM35C2V_20130921-034307-902_error.xml`  

 `http://myaccount.blob.core.windows.net/ImportExportStatesPath/waies/myjob_9WM45A6Q_20130921-042122-021_error.xml`  

 Zobacz Format [pliku dziennika usługi Import/Export](../storage-import-export-file-format-log.md) , aby zapoznać się z formatem kopiowania dzienników i pełną listą kodów stanu.  

## <a name="next-steps"></a>Następne kroki

 * [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup-v1.md)   
 * [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
 * [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
 * [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)   
 * [Rozwiązywanie problemów z narzędziem Azure Import/Export](storage-import-export-tool-troubleshooting-v1.md)
