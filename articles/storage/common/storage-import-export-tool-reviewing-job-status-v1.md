---
title: Przeglądanie stanu zadania importowania/eksportowania platformy Azure — Wersja 1 | Microsoft Docs
description: Dowiedz się, jak używać plików dziennika utworzonych przez zadanie importu lub eksportu w celu wyświetlenia stanu zadania.
author: alkohli
services: storage
ms.service: storage
ms.topic: how-to
ms.date: 01/26/2017
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: be421cc0bb00018b32ee63f2b486c11300627a01
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92488558"
---
# <a name="reviewing-azure-importexport-job-status-with-copy-log-files"></a>Przeglądanie stanu zadania importowania/eksportowania platformy Azure przy użyciu plików dziennika kopiowania
Gdy usługa Microsoft Azure Import/Export przetwarza dyski skojarzone z zadaniem importu lub eksportu, zapisuje pliki dziennika kopiowania na koncie magazynu użytym do zaimportowania lub wyeksportowania obiektów BLOB. Plik dziennika zawiera szczegółowy stan każdego importowanego lub wyeksportowanego pliku. Usługa zwraca adres URL każdego pliku dziennika kopiowania podczas wykonywania zapytania o stan ukończonego zadania. Aby uzyskać więcej informacji, zobacz [pobieranie zadania](/rest/api/storageimportexport/Jobs/Get).  

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
