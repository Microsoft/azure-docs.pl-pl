---
title: Samouczek dotyczący kopiowania danych za pośrednictwem protokołu SMB z urządzenia Azure Data Box | Microsoft Docs
description: Dowiedz się, jak skopiować dane na urządzenie Azure Data Box za pośrednictwem protokołu SMB
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 07/10/2020
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: ecc6e1e1a543f3190e9f73512ca0b9ae45cc3fe9
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335217"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-smb-preview"></a>Samouczek: Kopiowanie danych z urządzenia Azure Data Box za pośrednictwem protokołu SMB (wersja zapoznawcza)

W tym samouczku opisano sposób nawiązywania połączenia i kopiowania danych z urządzenia Data Box na serwer lokalny za pomocą lokalnego internetowego interfejsu użytkownika. Urządzenie Data Box zawiera dane wyeksportowane z konta usługi Azure Storage.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych z urządzenia Data Box

[!INCLUDE [Data Box feature is in preview](../../includes/data-box-feature-is-preview-info.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Złożono zamówienie na urządzenie Azure Data Box.
    - Aby uzyskać zamówienie importu, zobacz [Samouczek: zamawianie urządzenia Azure Data Box](data-box-deploy-ordered.md).
    - Aby uzyskać zamówienie eksportu, zobacz [Samouczek: zamawianie urządzenia Azure Data Box](data-box-deploy-export-ordered.md).
2. Urządzenie Data Box zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Masz komputer-host, na który chcesz skopiować dane z urządzenie Data Box. Na komputerze hosta wymagane jest:
   * Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
   * Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, użyj połączenia danych 1 GbE (będzie to miało negatywny wpływ na szybkość kopiowania).

## <a name="connect-to-data-box"></a>Nawiązywanie połączenia z urządzeniem Data Box

[!INCLUDE [data-box-shares](../../includes/data-box-shares.md)]

Jeśli używasz komputera-hosta z systemem Windows Server, wykonaj następujące kroki, aby nawiązać połączenie z urządzeniem Data Box.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Wybierz pozycję **Pobierz poświadczenia** , aby pobrać poświadczenia dostępu do udziałów skojarzonych z kontem magazynu. 

    ![Pobieranie poświadczeń udziału](media/data-box-deploy-export-copy-data/get-share-credentials-1.png)

2. W oknie dialogowym Uzyskiwanie dostępu do udziału i kopiowanie danych skopiuj wartości pól **Nazwa użytkownika** i **Hasło** odpowiedniego udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału, uzyskiwanie dostępu do udziału i kopiowanie danych](media/data-box-deploy-export-copy-data/get-share-credentials-2.png)

3. Aby uzyskać dostęp do udziałów skojarzonych z kontem magazynu ( *exportbvtdataset2* w poniższym przykładzie) z komputera-hosta, otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    W zależności od formatu danych ścieżki udziałów są następujące:
    - Blokowe obiekty blob platformy Azure — `\\169.254.143.85\exportbvtdataset2_BlockBlob`
    - Stronicowe obiekty blob platformy Azure — `\\169.254.143.85\exportbvtdataset2_PageBlob`
    - Pliki platformy Azure — `\\169.254.143.85\exportbvtdataset2_AzFile`

4. Po wyświetleniu monitu wprowadź hasło dla udziału. W poniższym przykładzie pokazano nawiązywanie połączenia z udziałem za pomocą poprzedniego polecenia.

    ```
    C:\Users\Databoxuser>net use \\169.254.143.85\exportbvtdataset2_BlockBlob /u:exportbvtdataset2
    Enter the password for 'exportbvtdataset2' to connect to '169.254.143.85':
    The command completed successfully.
    ```

5. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>`. Wybierz przycisk **OK** , aby otworzyć Eksploratora plików.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików, wprowadzanie adresu IP urządzenia](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-1.png)

    Teraz udziały powinny być widoczne jako foldery.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików, wyświetlanie udziałów](media/data-box-deploy-export-copy-data/connect-shares-file-explorer-2.png)

    
W przypadku korzystania z klienta systemu Linux użyj następującego polecenia, aby zainstalować udział SMB. Parametr „vers” poniżej to wersja protokołu SMB obsługiwana przez Twój host z systemem Linux. Podłącz odpowiednią wersję w poleceniu poniżej. W przypadku wersji protokołu SMB obsługiwanych przez urządzenia Data Box zobacz [Obsługiwane systemy plików dla klientów systemu Linux](./data-box-system-requirements.md#supported-file-transfer-protocols-for-clients) 

```console
sudo mount -t nfs -o vers=2.1 169.254.143.85:/exportbvtdataset2_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-from-data-box"></a>Kopiowanie danych z urządzenia Data Box

Po nawiązaniu połączenia z udziałami urządzenia Data Box następnym krokiem jest skopiowanie danych.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]


 Po nawiązaniu połączenia z udziałem SMB rozpocznij kopiowanie danych. Do kopiowania danych możesz użyć dowolnego narzędzia kopiowania plików zgodnego z protokołem SMB, na przykład narzędzia Robocopy. Za pomocą narzędzia Robocopy można zainicjować wiele zadań kopiowania. 


Aby uzyskać więcej informacji na temat polecenia Robocopy, przejdź do artykułu [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Polecenie Robocopy i kilka przykładów).

Po zakończeniu kopiowania przejdź do **pulpitu nawigacyjnego** i sprawdź ilość używanego i wolnego miejsca na urządzeniu.

Teraz możesz przystąpić do dostarczania urządzenie Data Box do firmy Microsoft.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych z urządzenia Data Box

Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box do firmy Microsoft](./data-box-deploy-export-picked-up.md)