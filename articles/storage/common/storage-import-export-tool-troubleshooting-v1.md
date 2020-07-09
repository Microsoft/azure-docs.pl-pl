---
title: Rozwiązywanie problemów z narzędziem Azure Import/Export | Microsoft Docs
description: Poznaj niektóre typowe problemy występujące podczas korzystania z narzędzia Azure Import/Export oraz jak je obsługiwać.
author: twooley
services: storage
ms.service: storage
ms.topic: troubleshooting
ms.date: 01/15/2017
ms.author: twooley
ms.subservice: common
ms.openlocfilehash: bc9d338579385001d33669ed06ff71e590571502
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "85514153"
---
# <a name="troubleshooting-the-azure-importexport-tool"></a>Rozwiązywanie problemów z narzędziem Azure Import/Export
Narzędzie Microsoft Azure Import/Export zwraca komunikaty o błędach, jeśli występują problemy. W tym temacie wymieniono niektóre typowe problemy, które użytkownicy mogą uruchamiać.  

## <a name="a-copy-session-fails-what-i-should-do"></a>Sesja kopiowania kończy się niepowodzeniem, co należy zrobić?  
 Gdy sesja kopiowania nie powiedzie się, dostępne są dwie opcje:  

 Jeśli błąd zostanie ponowiony, na przykład jeśli udział sieciowy był w trybie offline przez krótki czas, a teraz jest w trybie online, można wznowić sesję kopiowania. Jeśli błąd nie zostanie ponowiony, na przykład jeśli w parametrach wiersza polecenia określono nieprawidłowy katalog pliku źródłowego, należy przerwać sesję kopiowania. Aby uzyskać więcej informacji na temat wznawiania i przerywania sesji kopiowania, zobacz [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md) .  

## <a name="i-cant-resume-or-abort-a-copy-session"></a>Nie można wznowić lub przerwać sesji kopiowania.  
 Jeśli sesja kopiowania jest pierwszą sesją kopiowania dysku, komunikat o błędzie powinien zawierać następujący stan: "Pierwsza sesja kopiowania nie może zostać wznowiona ani przerwana". W takim przypadku można usunąć stary plik dziennika i ponownie uruchomić polecenie.  

 Jeśli sesja kopiowania nie jest pierwszą dla dysku, zawsze może zostać wznowiona lub przerwana.  

## <a name="i-lost-the-journal-file-can-i-still-create-the-job"></a>Mimo że plik dziennika został utracony, można utworzyć zadanie?  
 Plik dziennika dla dysku zawiera pełne informacje o kopiowaniu danych na ten dysk i koniecznie dodać więcej plików do dysku i zostanie użyty do utworzenia zadania importu. Jeśli plik dziennika zostanie utracony, należy ponownie wykonać wszystkie sesje kopiowania dla dysku.  

## <a name="next-steps"></a>Następne kroki

* [Konfigurowanie narzędzia Azure Import/Export](../storage-import-export-tool-setup-v1.md)   
* [Przygotowywanie dysków twardych do zadania importu](../storage-import-export-tool-preparing-hard-drives-import-v1.md)   
* [Sprawdzanie stanu zadania za pomocą plików dziennika kopiowania](../storage-import-export-tool-reviewing-job-status-v1.md)   
* [Naprawianie zadania importu](../storage-import-export-tool-repairing-an-import-job-v1.md)   
* [Naprawianie zadania eksportu](../storage-import-export-tool-repairing-an-export-job-v1.md)
