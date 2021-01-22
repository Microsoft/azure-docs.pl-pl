---
title: Rozwiązywanie problemów z importowaniem i eksportowaniem w usłudze Azure Import/Export | Microsoft Docs
description: Dowiedz się, jak obsługiwać typowe problemy podczas korzystania z usługi Azure Import/Export.
author: v-dalc
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/19/2021
ms.author: alkohli
ms.subservice: common
ms.openlocfilehash: 555529b52d586078ba7e1832373ec126ba545c11
ms.sourcegitcommit: 75041f1bce98b1d20cd93945a7b3bd875e6999d0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/22/2021
ms.locfileid: "98706427"
---
# <a name="troubleshoot-issues-in-azure-importexport"></a>Rozwiązywanie problemów z usługą Azure Import/Export
W tym artykule opisano sposób rozwiązywania typowych problemów związanych z importowaniem i eksportowaniem danych w usłudze Azure Import/Export.

## <a name="a-copy-session-failed-what-i-should-do"></a>Sesja kopiowania nie powiodła się. Co mam zrobić?  

W przypadku niepowodzenia sesji kopiowania dostępne są dwie opcje:  
* Jeśli błąd może zostać ponowiony, na przykład jeśli udział sieciowy był w trybie offline przez krótki czas i jest teraz ponownie dostępny w trybie online — można wznowić sesję kopiowania.
* Jeśli błąd nie może zostać ponowiony — na przykład jeśli w parametrach wiersza polecenia określono nieprawidłowy katalog pliku źródłowego — należy przerwać sesję kopiowania.
 
<!--For information about resuming and aborting copy sessions, see [Preparing Hard Drives for an Import Job](../storage-import-export-tool-preparing-hard-drives-import-v1.md  - Article we removed from TOC. File remains.-->

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nie można wznowić lub przerwać sesji kopiowania.

Jeśli sesja kopiowania jest pierwszą sesją kopiowania dysku, komunikat o błędzie powinien zawierać następujący stan: "Pierwsza sesja kopiowania nie może zostać wznowiona ani przerwana". W takim przypadku można usunąć stary plik dziennika i ponownie uruchomić polecenie.  

Jeśli sesja kopiowania nie jest pierwszą dla dysku, sesja zawsze może zostać wznowiona lub przerwana.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Plik dziennika został utracony. Czy mimo to mogę utworzyć zadanie?

Plik dziennika dla dysku zawiera pełne informacje o sesji z kopii danych. Po dodaniu plików do dysku plik dziennika jest używany do tworzenia zadania importu. W przypadku utraty pliku dziennika należy ponownie wykonać wszystkie sesje kopiowania dla dysku.

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie narzędzia Azure Import/Export](storage-import-export-tool-setup-v1.md)
* [Przygotowywanie dysków twardych do zadania importu](storage-import-export-data-to-blobs.md#step-1-prepare-the-drives)
* [Przeglądanie stanu zadania za pomocą plików dziennika kopiowania](storage-import-export-tool-reviewing-job-status-v1.md)
* [Naprawianie zadania importu](storage-import-export-tool-repairing-an-import-job-v1.md)
* [Naprawa zadania eksportu](storage-import-export-tool-repairing-an-export-job-v1.md)