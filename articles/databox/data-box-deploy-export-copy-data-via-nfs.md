---
title: Samouczek do kopiowania danych z Azure Data Box za pośrednictwem systemu plików NFS | Microsoft Docs
description: Dowiedz się, jak kopiować dane z Azure Data Box za pośrednictwem systemu plików NFS
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 12/18/2020
ms.author: alkohli
ms.openlocfilehash: 64bb5e94c4b18626d1f85d7e61252aae74202eb9
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97680621"
---
# <a name="tutorial-copy-data-from-azure-data-box-via-nfs"></a>Samouczek: kopiowanie danych z Azure Data Box za pośrednictwem systemu plików NFS

W tym samouczku opisano sposób nawiązywania połączenia z lokalnym interfejsem użytkownika sieci Web urządzenie Data Box i kopiowania ich z niego do lokalnego serwera danych za pośrednictwem systemu plików NFS. Dane w urządzenie Data Box zostaną wyeksportowane z konta usługi Azure Storage.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych z urządzenia Data Box

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

Jeśli używasz komputera-hosta z systemem Linux, wykonaj następujące czynności, aby skonfigurować urządzenie Data Box na potrzeby zezwalania na dostęp do klientów sieciowego systemu plików. Urządzenie Data Box mogą łączyć się maksymalnie pięć klientów NFS jednocześnie.

1. Podaj adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału:

    1.  W lokalnym interfejsie użytkownika sieci Web przejdź do strony **łączenie i kopiowanie** . W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**. 

        ![Otwórz dostęp klienta NFS](media/data-box-deploy-export-copy-data/nfs-client-access-1.png)

    1. Aby dodać klienta NFS, podaj adres IP klienta i kliknij przycisk **Dodaj**. Urządzenie Data Box mogą łączyć się maksymalnie pięć klientów NFS jednocześnie. Po zakończeniu kliknij przycisk **OK**.

         ![Dodawanie klienta NFS](media/data-box-deploy-export-copy-data/nfs-client-access-2.png)

2. Upewnij się, że na komputerze-hoście z systemem Linux zainstalowano [obsługiwaną wersję](data-box-system-requirements.md) klienta sieciowego systemu plików. Użyj konkretnej wersji dla określonej dystrybucji systemu Linux. 

3. Po zainstalowaniu klienta sieciowego systemu plików użyj następującego polecenia, aby zainstalować udział sieciowego systemu plików na Twoim urządzeniu Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Poniższy przykład pokazuje, jak nawiązać połączenie z urządzeniem Data Box za pomocą sieciowego systemu plików. Adres IP urządzenia Data Box: IP `10.161.23.130`. Udział `Mystoracct_Blob` jest zainstalowany na maszynie ubuntuVM. Punkt instalacji: `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    W przypadku klientów z komputerami Mac musisz w następujący sposób dodać dodatkową opcję: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

## <a name="copy-data-from-data-box"></a>Kopiowanie danych z urządzenia Data Box

Po nawiązaniu połączenia z udziałami urządzenia Data Box następnym krokiem jest skopiowanie danych.

[!INCLUDE [data-box-export-review-logs](../../includes/data-box-export-review-logs.md)]

 Teraz można rozpocząć kopiowanie danych. Jeśli korzystasz z komputera-hosta z systemem Linux, użyj narzędzia do kopiowania podobnego do narzędzia Robocopy. Niektóre alternatywy dostępne w systemie Linux to [`rsync`](https://rsync.samba.org/) , [FreeFileSync](https://www.freefilesync.org/), [artykułem](https://www.cis.upenn.edu/~bcpierce/unison/)lub [Ultracopier](https://ultracopier.first-world.info/).  

Polecenie `cp` jest jedną z najlepszych opcji do kopiowania katalogów. Aby uzyskać więcej informacji dotyczących użycia, przejdź do [stron man narzędzia cp](http://man7.org/linux/man-pages/man1/cp.1.html).

W przypadku korzystania z `rsync` opcji kopiowania wielowątkowego postępuj zgodnie z następującymi wskazówkami:

* Zainstaluj pakiet **CIFS Utils** lub **NFS Utils** w zależności od systemu plików używanego przez Twojego klienta systemu Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Instaluj `rsync` i **Parallel** (w zależności od wersji rozproszonej systemu Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

* Utwórz punkt instalacji.

    `sudo mkdir /mnt/databox`

* Zainstaluj wolumin.

    `sudo mount -t NFS4  //Databox IP Address/share_name /mnt/databox` 

* Zdubluj strukturę katalogów folderów.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databox`

* Skopiuj pliki.

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databox/{}`

     gdzie j określa liczbę przetwarzań równoległych, X = liczba równoległych kopii

     Na początku zaleca się użycie 16 równoległych kopii i zwiększanie liczby wątków w zależności od dostępności zasobów.

> [!IMPORTANT]
> Następujące typy plików systemu Linux nie są obsługiwane: linki symboliczne, pliki znaków, pliki blokowe, gniazda i potoki. Te typy plików spowodują błędy podczas kroku **przygotowanie do wysłania** .

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
