---
title: Samouczek do kopiowania danych do Azure Data Box Heavy za pomocą systemu plików NFS | Microsoft Docs
description: W tym samouczku dowiesz się, jak nawiązać połączenie i skopiować dane z komputera hosta do Azure Data Box Heavy przy użyciu systemu plików NFS z lokalnym interfejsem użytkownika sieci Web.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: a8199039ea803d5919e168edccaaa03c18985725
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87921031"
---
# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-nfs"></a>Samouczek: kopiowanie danych do Azure Data Box Heavy za pośrednictwem systemu plików NFS

W tym samouczku opisano sposób nawiązywania połączenia z komputerem hosta i kopiowania danych z niego przy użyciu lokalnego interfejsu użytkownika sieci Web do Azure Data Box Heavy.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy
> * Kopiowanie danych na urządzenie Data Box Heavy

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Samouczek został ukończony [: skonfiguruj Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Otrzymano Data Box Heavy, a stan zamówienia w portalu jest **dostarczany**.
3. Masz komputer-host zawierający dane, które mają zostać skopiowane na urządzenie Data Box Heavy. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-heavy-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Aby uzyskać największe szybkości kopiowania, można użyć dwóch równoległych połączeń 40-GbE (po jednym na węzeł). Jeśli nie masz dostępnego połączenia 40-GbE, zalecamy skorzystanie z co najmniej dwóch połączeń 10-GbE (po jednym na węzeł). 

## <a name="connect-to-data-box-heavy"></a>Nawiązywanie połączenia z urządzeniem Data Box Heavy

W zależności od wybranego konta magazynu dla urządzenia Data Box Heavy są tworzone następujące elementy:
- Maksymalnie trzy udziały dla każdego skojarzonego konta magazynu (GPv1 i GPv2).
- Jeden udział w usłudze Premium Storage.
- Jeden udział dla konta magazynu obiektów blob.

Te udziały są tworzone w obu węzłach urządzenia.

W przypadku udziałów blokowych i stronicowych obiektów blob:
- Jednostki pierwszego poziomu są kontenerami.
- Jednostki drugiego poziomu to obiekty blob.

W przypadku udziałów usługi Azure Files:
- Jednostki pierwszego poziomu są udziałami.
- Jednostki drugiego poziomu to pliki.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na urządzeniu Data Box Heavy i adres URL ścieżki w usłudze Azure Storage, na który przekazywane są dane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
| Magazyn           | Ścieżka UNC                                                                       |
|-------------------|--------------------------------------------------------------------------------|
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Jeśli używasz komputera hosta z systemem Linux, wykonaj następujące kroki, aby skonfigurować urządzenie w celu zezwalania na dostęp do klientów NFS.

1. Podaj adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Połącz i skopiuj**. W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**. 

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/data-box-deploy-copy-data/nfs-client-access.png)

2. Podaj adres IP klienta sieciowego systemu plików i kliknij przycisk **Dodaj**. Powtarzając ten krok, możesz skonfigurować dostęp dla wielu klientów sieciowego systemu plików. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Upewnij się, że na komputerze-hoście z systemem Linux zainstalowano [obsługiwaną wersję](data-box-system-requirements.md) klienta sieciowego systemu plików. Użyj konkretnej wersji dla określonej dystrybucji systemu Linux. 

3. Po zainstalowaniu klienta sieciowego systemu plików użyj następującego polecenia, aby zainstalować udział sieciowego systemu plików na Twoim urządzeniu Data Box:

    `sudo mount <Data Box Heavy device IP>:/<NFS share on Data Box Heavy device> <Path to the folder on local Linux computer>`

    Poniższy przykład pokazuje, jak nawiązać połączenie za pomocą systemu plików NFS z udziałem Data Box Heavy. Data Box Heavy IP to `10.161.23.130` , udział `Mystoracct_Blob` jest instalowany na ubuntuVM, punktu instalacji `/home/databoxheavyubuntuhost/databoxheavy` .

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`
    
    W przypadku klientów z komputerami Mac musisz w następujący sposób dodać dodatkową opcję: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxheavyubuntuhost/databoxheavy`

    **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych na urządzenie Data Box Heavy

Po nawiązaniu połączenia z udziałami Data Box Heavy następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Skopiuj wirtualne dyski twarde do stronicowych obiektów blob. Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
-  Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z limitami rozmiaru opisanymi w [limitach usługi Azure Storage i Data Box Heavy](data-box-heavy-limits.md). 
- Jeśli dane przekazywane przy użyciu urządzenia Data Box Heavy będą jednocześnie przekazywane przez inne aplikacje, poza urządzeniem Data Box Heavy, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.
- Nie zaleca się jednoczesnego używania protokołu SMB i sieciowego systemu plików ani kopiowania tych samych danych do tego samego końcowego miejsca docelowego na platformie Azure. W takich przypadkach nie można określić ostatecznego wyniku.
- **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.
- W przypadku pozyskania nazw plików z uwzględnieniem wielkości liter i katalogów z udziału NFS do systemu plików NFS na Data Box Heavy: 
    - Wielkość liter jest zachowywana w nazwie.
    - W plikach nie jest rozróżniana wielkość liter.
    
    Na przykład, jeśli kopiowanie `SampleFile.txt` i `Samplefile.Txt` , wielkość liter zostanie zachowana w nazwie podczas kopiowania do urządzenia, ale drugi plik zastąpi pierwsze, ponieważ są one uznawane za ten sam plik.


Jeśli korzystasz z komputera-hosta z systemem Linux, użyj narzędzia do kopiowania podobnego do narzędzia Robocopy. W systemie Linux są dostępne na przykład narzędzia [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) lub [Ultracopier](https://ultracopier.first-world.info/).  

Polecenie `cp` jest jedną z najlepszych opcji do kopiowania katalogów. Aby uzyskać więcej informacji dotyczących użycia, przejdź do [stron man narzędzia cp](http://man7.org/linux/man-pages/man1/cp.1.html).

W przypadku korzystania z opcji rsync na potrzeby kopiowania wielowątkowego należy przestrzegać następujących wytycznych:

 - Zainstaluj pakiet **CIFS Utils** lub **NFS Utils** w zależności od systemu plików używanego przez Twojego klienta systemu Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

 -  Zainstaluj narzędzia **Rsync** i **Parallel** (polecenia różnią się w zależności od wersji dystrybucji systemu Linux).

    `sudo apt-get install rsync`
   
    `sudo apt-get install parallel` 

 - Utwórz punkt instalacji.

    `sudo mkdir /mnt/databoxheavy`

 - Zainstaluj wolumin.

    `sudo mount -t NFS4  //Databox-heavy-IP-Address/share_name /mnt/databoxheavy` 

 - Zdubluj strukturę katalogów folderów.  

    `rsync -za --include='*/' --exclude='*' /local_path/ /mnt/databoxheavy`

 - Skopiuj pliki. 

    `cd /local_path/; find -L . -type f | parallel -j X rsync -za {} /mnt/databoxheavy/{}`

     gdzie j określa liczbę przetwarzań równoległych, X = liczba równoległych kopii

     Na początku zaleca się użycie 16 równoległych kopii i zwiększanie liczby wątków w zależności od dostępności zasobów.

> [!IMPORTANT]
> Następujące typy plików systemu Linux nie są obsługiwane: linki symboliczne, pliki znaków, pliki blokowe, gniazda i potoki. Te typy plików spowodują błędy podczas kroku **przygotowanie do wysłania** .

Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki. Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki z błędami, które pomogą w rozwiązywaniu problemów. Aby uzyskać więcej informacji, zobacz [Wyświetlanie dzienników błędów podczas kopiowania danych na urządzenie Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Aby uzyskać szczegółową listę błędów występujących podczas kopiowania danych, zobacz [Rozwiązywanie problemów z urządzeniem Data Box Heavy](data-box-troubleshoot.md).

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
   ![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Heavy, takie jak:

> [!div class="checklist"]
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy
> * Kopiowanie danych na urządzenie Data Box Heavy


Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box Heavy do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

