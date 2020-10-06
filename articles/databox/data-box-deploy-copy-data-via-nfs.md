---
title: Samouczek do kopiowania danych do Azure Data Box za pomocą systemu plików NFS | Microsoft Docs
description: W tym samouczku dowiesz się, jak nawiązać połączenie i skopiować dane z komputera hosta do Azure Data Box przy użyciu systemu plików NFS z lokalnym interfejsem użytkownika sieci Web.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2020
ms.author: alkohli
ms.openlocfilehash: eee5119336be02621a27b315cb26ca8dd1fd9cb4
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766261"
---
# <a name="tutorial-copy-data-to-azure-data-box-via-nfs"></a>Samouczek: kopiowanie danych do Azure Data Box za pośrednictwem systemu plików NFS

W tym samouczku opisano sposób nawiązywania połączenia i kopiowania danych z komputera-hosta za pomocą lokalnego internetowego interfejsu użytkownika.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych na urządzenie Data Box

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [samouczek dotyczący konfigurowania urządzenia Azure Data Box](data-box-deploy-set-up.md).
2. Urządzenie Data Box zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Masz komputer-host zawierający dane, które mają zostać skopiowane na urządzenie Data Box. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, można użyć połączenia danych 1 GbE, ale będzie miało to wpływ na szybkość kopiowania. 

## <a name="connect-to-data-box"></a>Nawiązywanie połączenia z urządzeniem Data Box

W zależności od wybranego konta magazynu dla urządzenia Data Box są tworzone następujące elementy:
- Maksymalnie trzy udziały dla każdego skojarzonego konta magazynu (GPv1 i GPv2).
- Jeden udział w usłudze Premium Storage. 
- Jeden udział dla konta magazynu obiektów blob. 

W obszarze udziałów blokowych obiektów blob i stronicowych obiektów blob jednostki pierwszego poziomu są kontenerami, a jednostki drugiego poziomu są obiektami blob. W obszarze udziałów dla usługi Azure Files jednostki pierwszego poziomu są udziałami, a jednostki drugiego poziomu są plikami.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na urządzeniu Data Box i adres URL ścieżki w usłudze Azure Storage, pod który przekazywane są dane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
| Typ magazynu usługi Azure Storage| Udziały urządzenia Data Box                                       |
|-------------------|--------------------------------------------------------------------------------|
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

Jeśli używasz komputera-hosta z systemem Linux, wykonaj następujące czynności, aby skonfigurować urządzenie Data Box na potrzeby zezwalania na dostęp do klientów sieciowego systemu plików.

1. Podaj adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Połącz i skopiuj**. W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**. 

    ![Konfigurowanie dostępu klienta NFS](media/data-box-deploy-copy-data/nfs-client-access-1.png)

2. Podaj adres IP klienta sieciowego systemu plików i kliknij przycisk **Dodaj**. Powtarzając ten krok, możesz skonfigurować dostęp dla wielu klientów sieciowego systemu plików. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/data-box-deploy-copy-data/nfs-client-access2.png)

2. Upewnij się, że na komputerze-hoście z systemem Linux zainstalowano [obsługiwaną wersję](data-box-system-requirements.md) klienta sieciowego systemu plików. Użyj konkretnej wersji dla określonej dystrybucji systemu Linux. 

3. Po zainstalowaniu klienta sieciowego systemu plików użyj następującego polecenia, aby zainstalować udział sieciowego systemu plików na Twoim urządzeniu Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

    Poniższy przykład pokazuje, jak nawiązać połączenie z urządzeniem Data Box za pomocą sieciowego systemu plików. Adres IP urządzenia Data Box: IP `10.161.23.130`. Udział `Mystoracct_Blob` jest zainstalowany na maszynie ubuntuVM. Punkt instalacji: `/home/databoxubuntuhost/databox`.

    `sudo mount -t nfs 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`
    
    W przypadku klientów z komputerami Mac musisz w następujący sposób dodać dodatkową opcję: 
    
    `sudo mount -t nfs -o sec=sys,resvport 10.161.23.130:/Mystoracct_Blob /home/databoxubuntuhost/databox`

    **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

## <a name="copy-data-to-data-box"></a>Kopiowanie danych na urządzenie Data Box

Po nawiązaniu połączenia z udziałami urządzenia Data Box następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

* Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Skopiuj wirtualne dyski twarde do stronicowych obiektów blob. Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
*  Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z limitami rozmiaru określonymi w [limitach rozmiaru konta usługi Azure Storage](data-box-limits.md#azure-storage-account-size-limits).
* Jeśli dane przekazywane przy użyciu urządzenia Data Box będą jednocześnie przekazywane przez inne aplikacje, poza urządzeniem Data Box, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.
* Nie zaleca się jednoczesnego używania protokołu SMB i sieciowego systemu plików ani kopiowania tych samych danych do tego samego końcowego miejsca docelowego na platformie Azure. W takich przypadkach nie można określić ostatecznego wyniku.
* **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.
* W przypadku pozyskania nazw plików z uwzględnieniem wielkości liter i katalogów z udziału NFS do systemu plików NFS na urządzenie Data Box:
  * Wielkość liter jest zachowywana w nazwie.
  * W plikach nie jest rozróżniana wielkość liter.

    Na przykład, jeśli kopiowanie `SampleFile.txt` i `Samplefile.Txt` , wielkość liter zostanie zachowana w nazwie podczas kopiowania do urządzenie Data Box ale drugi plik zastąpi pierwsze, ponieważ są one uznawane za ten sam plik.

> [!IMPORTANT]
> Pamiętaj, aby zachować kopię danych źródłowych do czasu potwierdzenia, że usługa Data Box przeniosła Twoje dane do usługi Azure Storage.

Jeśli korzystasz z komputera-hosta z systemem Linux, użyj narzędzia do kopiowania podobnego do narzędzia Robocopy. W systemie Linux są dostępne na przykład narzędzia [rsync](https://rsync.samba.org/), [FreeFileSync](https://www.freefilesync.org/), [Unison](https://www.cis.upenn.edu/~bcpierce/unison/) lub [Ultracopier](https://ultracopier.first-world.info/).  

Polecenie `cp` jest jedną z najlepszych opcji do kopiowania katalogów. Aby uzyskać więcej informacji dotyczących użycia, przejdź do [stron man narzędzia cp](http://man7.org/linux/man-pages/man1/cp.1.html).

W przypadku korzystania z opcji rsync na potrzeby kopiowania wielowątkowego należy przestrzegać następujących wytycznych:

* Zainstaluj pakiet **CIFS Utils** lub **NFS Utils** w zależności od systemu plików używanego przez Twojego klienta systemu Linux.

    `sudo apt-get install cifs-utils`

    `sudo apt-get install nfs-utils`

* Zainstaluj **rsync** i **Parallel** (w zależności od rozproszonej wersji systemu Linux).

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

Jeśli podczas kopiowania wystąpią błędy, zostanie wyświetlone powiadomienie.

![Błędy pobierania i wyświetlania na stronie Połącz i skopiuj](media/data-box-deploy-copy-data/view-errors-1.png)

Wybierz pozycję **Pobierz listę problemów**.

![Pobierz listę problemów z błędem kopiowania](media/data-box-deploy-copy-data/view-errors-2.png)

Otwórz listę, aby wyświetlić szczegóły błędu, a następnie wybierz adres URL rozwiązania, aby wyświetlić zalecane rozwiązanie.

![Problemy z listą problemów dotyczących błędów kopiowania](media/data-box-deploy-copy-data/view-errors-3.png)

Aby uzyskać więcej informacji, zobacz [Wyświetlanie dzienników błędów podczas kopiowania danych na urządzenie Data Box](data-box-logs.md#view-error-log-during-data-copy). Aby uzyskać szczegółową listę błędów występujących podczas kopiowania danych, zobacz [Rozwiązywanie problemów z urządzeniem Data Box](data-box-troubleshoot.md).

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.

   ![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące usługi Azure Data Box, takie jak:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych na urządzenie Data Box

Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box do firmy Microsoft](./data-box-deploy-picked-up.md)
