---
title: Instalowanie udziału plików platformy Azure za pomocą protokołu SMB w systemie macOS | Microsoft Docs
description: Dowiedz się, jak zainstalować udział plików platformy Azure za pośrednictwem protokołu SMB z usługą macOS przy użyciu narzędzia Finder lub terminalu. Azure Files to łatwy w użyciu system plików w chmurze firmy Microsoft.
author: roygara
ms.service: storage
ms.topic: how-to
ms.date: 09/23/2020
ms.author: rogarana
ms.subservice: files
ms.openlocfilehash: 119f4c0ea434bc431b40c905d9142e187b7d9474
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "91326069"
---
# <a name="mount-azure-file-share-over-smb-with-macos"></a>Instalowanie udziału plików platformy Azure za pomocą protokołu SMB w systemie macOS
[Azure Files](storage-files-introduction.md) to łatwy w użyciu system plików w chmurze firmy Microsoft. Udziały plików platformy Azure można instalować przy użyciu standardowego protokołu SMB 3 przez macOS High Sierra 10.13 +. W tym artykule przedstawiono dwa różne sposoby instalowania udziału plików platformy Azure w systemie macOS: za pomocą interfejsu użytkownika programu Finder i Terminalu.

## <a name="prerequisites-for-mounting-an-azure-file-share-on-macos"></a>Wymagania wstępne dotyczące instalowania udziału plików platformy Azure w systemie macOS
* **Nazwa konta magazynu**: Aby zainstalować udział plików platformy Azure, potrzebna będzie nazwa konta magazynu.

* **Klucz konta magazynu**: Aby zainstalować udział plików platformy Azure, konieczne będzie posiadanie podstawowego (lub dodatkowego) klucza magazynu. Klucze sygnatur dostępu współdzielonego nie są aktualnie obsługiwane na potrzeby instalowania.

* **Otwarty port 445**: Protokół SMB komunikuje się za pośrednictwem portu TCP 445. Na komputerze klienckim (komputerze Mac) upewnij się, że zapora nie blokuje portu TCP 445.

## <a name="mount-an-azure-file-share-via-finder"></a>Instalowanie udziału plików platformy Azure za pomocą programu Finder
1. **Otwórz program Finder**: Program Finder jest domyślnie otwarty w systemie macOS, ale możesz się upewnić, że jest on aktualnie wybraną aplikacją, klikając „ikonę twarzy systemu macOS” w Docku:  
    ![Ikona twarzy systemu macOS](./media/storage-how-to-use-files-mac/mount-via-finder-1.png)

2. **Wybierz pozycję "Połącz z serwerem" z menu "Przejdź"**: Używanie ścieżki UNC z wymagań wstępnych, Konwertuj początkowy podwójny ukośnik odwrotny ( `\\` ) na `smb://` i wszystkie inne ukośniki odwrotne ( `\` ), aby przekazywać ukośniki ( `/` ). Twój link powinien wyglądać podobnie do następującego: ![Okno dialogowe „Łączenie z serwerem”](./media/storage-how-to-use-files-mac/mount-via-finder-2.png)

3. **Użyj nazwy konta magazynu i klucza konta magazynu w wyświetlonym monicie o podanie nazwy użytkownika i hasła**: Po kliknięciu przycisku „Połącz” w oknie dialogowym „Łączenie z serwerem” zostanie wyświetlony monit o podanie nazwy użytkownika i hasła (pole zostanie automatycznie wypełnione nazwą użytkownika systemu macOS). Istnieje możliwość umieszczenia nazwy konta magazynu / klucza konta magazynu w pęku kluczy systemu macOS.

4. **Użyj udziału plików platformy Azure zgodnie z potrzebami**: po podpisaniu nazwy udziału i klucza konta magazynu w polu Nazwa użytkownika i hasło, udział zostanie zainstalowany. Udziału można używać tak samo jak lokalnego folderu / udziału plików. Obsługiwane jest też przeciąganie i upuszczanie plików do udziału plików:

    ![Migawka przedstawiająca zainstalowany udział plików platformy Azure](./media/storage-how-to-use-files-mac/mount-via-finder-3.png)

## <a name="mount-an-azure-file-share-via-terminal"></a>Instalowanie udziału plików platformy Azure za pomocą Terminalu
1. Zamień `<storage-account-name>` , `<storage-account-key>` i `<share-name>` z odpowiednimi wartościami dla danego środowiska.

    ```
    open smb://<storage-account-name>:<storage-account-key>@<storage-account-name>.file.core.windows.net/<share-name>
    ```

2. **Użyj udziału plików platformy Azure zgodnie z potrzebami**: udział plików platformy Azure zostanie zainstalowany w punkcie instalacji określonym przez poprzednie polecenie.  

    ![Migawka przedstawiająca zainstalowany udział plików platformy Azure](./media/storage-how-to-use-files-mac/mount-via-terminal-1.png)

## <a name="next-steps"></a>Następne kroki
* [Łączenie komputerów Mac z udostępnionymi komputerami i serwerami — pomoc techniczna firmy Apple](https://support.apple.com/guide/mac-help/connect-mac-shared-computers-servers-mchlp1140/mac)