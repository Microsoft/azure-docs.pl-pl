---
title: Samouczek dotyczący kopiowania danych za pośrednictwem protokołu SMB na urządzenie Azure Data Box | Microsoft Docs
description: W tym samouczku dowiesz się, jak nawiązać połączenie z urządzeniem Azure Data Box i skopiować na nie dane z komputera-hosta za pomocą protokołu SMB z lokalnym internetowym interfejsem użytkownika.
services: databox
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: tutorial
ms.date: 09/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: fcdc5d0e7254b8e491285baae6c2a1bc6979e437
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766314"
---
::: zone target="docs"

# <a name="tutorial-copy-data-to-azure-data-box-via-smb"></a>Samouczek: Kopiowanie danych na urządzenie Azure Data Box Disk za pośrednictwem protokołu SMB

::: zone-end

::: zone target="chromeless"

## <a name="copy-data-to-azure-data-box"></a>Kopiowanie danych na urządzenie Azure Data Box

::: zone-end

::: zone target="docs"

W tym samouczku opisano sposób nawiązywania połączenia i kopiowania danych z komputera-hosta za pomocą lokalnego internetowego interfejsu użytkownika.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
>
> * Wymagania wstępne
> * Nawiązywanie połączenia z urządzeniem Data Box
> * Kopiowanie danych na urządzenie Data Box

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: konfigurowanie usługi Azure Data Box](data-box-deploy-set-up.md).
2. Urządzenie Data Box zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Masz komputer-host zawierający dane, które mają zostać skopiowane na urządzenie Data Box. Na komputerze hosta wymagane jest:
   * Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
   * Połączenie z siecią o dużej szybkości. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. Jeśli połączenie 10 GbE nie jest dostępne, użyj połączenia danych 1 GbE (będzie to miało negatywny wpływ na szybkość kopiowania).

## <a name="connect-to-data-box"></a>Nawiązywanie połączenia z urządzeniem Data Box

W zależności od wybranego konta magazynu dla urządzenia Data Box są tworzone następujące elementy:

* Maksymalnie trzy udziały dla każdego skojarzonego konta magazynu (GPv1 i GPv2).
* Jeden udział w usłudze Premium Storage.
* Jeden udział dla konta magazynu obiektów blob.

W obszarze udziałów blokowych obiektów blob i stronicowych obiektów blob jednostki pierwszego poziomu są kontenerami, a jednostki drugiego poziomu są obiektami blob. W obszarze udziałów dla usługi Azure Files jednostki pierwszego poziomu są udziałami, a jednostki drugiego poziomu są plikami.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na urządzeniu Data Box i adres URL ścieżki w usłudze Azure Storage, pod który przekazywane są dane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
|Typy usługi Azure Storage  | Udziały urządzenia Data Box            |
|-------------------|--------------------------------------------------------------------------------|
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Jeśli używasz komputera-hosta z systemem Windows Server, wykonaj następujące kroki, aby nawiązać połączenie z urządzeniem Data Box.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Wybierz pozycję **SMB**, aby pobrać poświadczenia dostępu do udziałów skojarzonych z kontem magazynu. 

    ![Uzyskiwanie poświadczeń udziałów SMB](media/data-box-deploy-copy-data/get-share-credentials1.png)

2. W oknie dialogowym Uzyskiwanie dostępu do udziału i kopiowanie danych skopiuj wartości pól **Nazwa użytkownika** i **Hasło** odpowiedniego udziału. Kliknij przycisk **OK**.
    
    ![Uzyskiwanie nazwy użytkownika i hasła udziału](media/data-box-deploy-copy-data/get-share-credentials2.png)

3. Aby uzyskać dostęp do udziałów skojarzonych z kontem magazynu (*utsac1* w poniższym przykładzie) z komputera-hosta, otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    W zależności od formatu danych ścieżki udziałów są następujące:
    - Blokowe obiekty blob platformy Azure — `\\10.126.76.138\utSAC1_202006051000_BlockBlob`
    - Stronicowe obiekty blob platformy Azure — `\\10.126.76.138\utSAC1_202006051000_PageBlob`
    - Pliki platformy Azure — `\\10.126.76.138\utSAC1_202006051000_AzFile`

4. Po wyświetleniu monitu wprowadź hasło dla udziału. W poniższym przykładzie pokazano nawiązywanie połączenia z udziałem za pomocą poprzedniego polecenia.

    ```
    C:\Users\Databoxuser>net use \\10.126.76.138\utSAC1_202006051000_BlockBlob /u:testuser1
    Enter the password for 'testuser1' to connect to '10.126.76.138':
    The command completed successfully.
    ```

4. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>`. Wybierz przycisk **OK**, aby otworzyć Eksploratora plików.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików](media/data-box-deploy-copy-data/connect-shares-file-explorer1.png)

    Teraz udziały powinny być widoczne jako foldery.
    
    ![Udziały widoczne w Eksploratorze plików](media/data-box-deploy-copy-data/connect-shares-file-explorer2.png)

    **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.
    
W przypadku korzystania z klienta systemu Linux użyj następującego polecenia, aby zainstalować udział SMB. Parametr „vers” poniżej to wersja protokołu SMB obsługiwana przez Twój host z systemem Linux. Podłącz odpowiednią wersję w poleceniu poniżej. W przypadku wersji protokołu SMB obsługiwanych przez urządzenia Data Box zobacz [Obsługiwane systemy plików dla klientów systemu Linux](https://docs.microsoft.com/azure/databox/data-box-system-requirements#supported-file-systems-for-linux-clients) 

```console
sudo mount -t nfs -o vers=2.1 10.126.76.138:/utSAC1_202006051000_BlockBlob /home/databoxubuntuhost/databox
```

## <a name="copy-data-to-data-box"></a>Kopiowanie danych na urządzenie Data Box

Po nawiązaniu połączenia z udziałami urządzenia Data Box następnym krokiem jest skopiowanie danych. Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

* Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Skopiuj wirtualne dyski twarde do stronicowego obiektu blob. Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
* Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z ograniczeniami rozmiaru opisanymi w temacie [Limity rozmiaru konta magazynu na platformie Azure](data-box-limits.md#azure-storage-account-size-limits).
* Jeśli dane przekazywane przy użyciu urządzenia Data Box będą jednocześnie przekazywane przez inne aplikacje, poza urządzeniem Data Box, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.
* Zalecenia:
  * Nie używaj protokołów SMB i NFS w tym samym czasie.
  * Kopiuj te same dane do tego samego miejsca docelowego na platformie Azure.

  W takich przypadkach nie można określić ostatecznego wyniku.
* Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

> [!IMPORTANT]
> Pamiętaj, aby zachować kopię danych źródłowych do czasu potwierdzenia, że usługa Data Box przeniosła Twoje dane do usługi Azure Storage.

Po nawiązaniu połączenia z udziałem SMB rozpocznij kopiowanie danych. Do kopiowania danych możesz użyć dowolnego narzędzia kopiowania plików zgodnego z protokołem SMB, na przykład narzędzia Robocopy. Za pomocą narzędzia Robocopy można zainicjować wiele zadań kopiowania. Użyj następującego polecenia:

```console
robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
```

Atrybuty opisano w poniższej tabeli.
    
|Atrybut  |Opis  |
|---------|---------|
|/e     |Kopiuje podkatalogi, włącznie z pustymi katalogami.         |
|/r:     |Określa liczbę ponownych prób w przypadku niepowodzenia kopiowania.         |
|/w:     |Określa czas oczekiwania pomiędzy ponownymi próbami w sekundach.         |
|/is     |Zawiera te same pliki.         |
|/nfl     |Określa, że nazwy plików nie są rejestrowane.         |
|/ndl    |Określa, że nazwy katalogów nie są rejestrowane.        |
|/np     |Określa, że postęp operacji kopiowania (liczba skopiowanych do tej pory plików lub katalogów) nie będzie wyświetlany. Wyświetlanie postępu znacznie obniża wydajność.         |
|/MT     | Użyj wielowątkowości (zaleca się użycie 32 lub 64 wątków). Ta opcja nie jest używana z plikami zaszyfrowanymi. Może być konieczne oddzielne plików zaszyfrowanych od niezaszyfrowanych. Jednak kopiowanie jednowątkowe znacznie obniża wydajność.           |
|/fft     | Użyj, aby zmniejszyć stopień szczegółowości sygnatury czasu dla każdego systemu plików.        |
|/b     | Kopiuje pliki w trybie tworzenia kopii zapasowej.        |
|/z    | Kopiuje pliki w trybie ponownego uruchomienia. Użyj tego atrybutu w przypadku niestabilnego środowiska. Ta opcja powoduje zmniejszenie przepływności ze względu na dodatkowe rejestrowanie.      |
| /zb     | Używa trybu ponownego uruchomienia. W przypadku odmowy dostępu ta opcja używa trybu tworzenia kopii zapasowej. Ta opcja powoduje zmniejszenie przepływności ze względu na tworzenie punktów kontrolnych.         |
|/efsraw     | Kopiuje wszystkie zaszyfrowane pliki w surowym trybie EFS. Używaj tej opcji tylko względem zaszyfrowanych plików.         |
|log+:\<LogFile>| Dołącza dane wyjściowe do istniejącego pliku dziennika.|    
 
Poniższy przykład przedstawia dane wyjściowe polecenia kopiowania plików na urządzenie Data Box za pomocą narzędzia Robocopy.

```output
C:\Users>robocopy

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:53 PM
        Simple Usage :: ROBOCOPY source destination /MIR

        source :: Source Directory (drive:\path or \\server\share\path).
        destination :: Destination Dir  (drive:\path or \\server\share\path).
                /MIR :: Mirror a complete directory tree.

    For more usage information run ROBOCOPY /?

    ****  /MIR can DELETE files as well as copy them !

C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.126.76.172\devicemanagertest1_AzFile\templates /MT:32

    -------------------------------------------------------------------------------
    ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------

        Started : Thursday, March 8, 2018 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
            Dest : \\10.126.76.172\devicemanagertest1_AzFile\templates\

        Files : *.*

        Options : *.* /DCOPY:DA /COPY:DAT /MT:32 /R:5 /W:60

    ------------------------------------------------------------------------------

    100%        New File                 206        C:\Git\azure-docs-pr\contributor-guide\article-metadata.md
    100%        New File                 209        C:\Git\azure-docs-pr\contributor-guide\content-channel-guidance.md
    100%        New File                 732        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-index.md
    100%        New File                 199        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pr-criteria.md
                New File                 178        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-co100%  .md
                New File                 250        C:\Git\azure-docs-pr\contributor-guide\contributor-guide-pull-request-et100%  e.md
    100%        New File                 174        C:\Git\azure-docs-pr\contributor-guide\create-images-markdown.md
    100%        New File                 197        C:\Git\azure-docs-pr\contributor-guide\create-links-markdown.md
    100%        New File                 184        C:\Git\azure-docs-pr\contributor-guide\create-tables-markdown.md
    100%        New File                 208        C:\Git\azure-docs-pr\contributor-guide\custom-markdown-extensions.md
    100%        New File                 210        C:\Git\azure-docs-pr\contributor-guide\file-names-and-locations.md
    100%        New File                 234        C:\Git\azure-docs-pr\contributor-guide\git-commands-for-master.md
    100%        New File                 186        C:\Git\azure-docs-pr\contributor-guide\release-branches.md
    100%        New File                 240        C:\Git\azure-docs-pr\contributor-guide\retire-or-rename-an-article.md
    100%        New File                 215        C:\Git\azure-docs-pr\contributor-guide\style-and-voice.md
    100%        New File                 212        C:\Git\azure-docs-pr\contributor-guide\syntax-highlighting-markdown.md
    100%        New File                 207        C:\Git\azure-docs-pr\contributor-guide\tools-and-setup.md
    ------------------------------------------------------------------------------

                Total    Copied   Skipped  Mismatch    FAILED    Extras
    Dirs :         1         1         1         0         0         0
    Files :        17        17         0         0         0         0
    Bytes :     3.9 k     3.9 k         0         0         0         0
C:\Users>
```

Aby zoptymalizować wydajność, użyj poniższych parametrów polecenia robocopy podczas kopiowania danych.

|    Platforma    |    Przeważnie małe pliki < 512 KB                           |    Przeważnie średnie pliki od 512 KB do 1 MB                      |    Przeważnie duże pliki > 1 MB                             |   
|----------------|--------------------------------------------------------|--------------------------------------------------------|--------------------------------------------------------|
|    Data Box         |    2 sesje narzędzia Robocopy <br> 16 wątków na sesję    |    3 sesje narzędzia Robocopy <br> 16 wątków na sesję    |    2 sesje narzędzia Robocopy <br> 24 wątki na sesję    |

Aby uzyskać więcej informacji na temat polecenia Robocopy, przejdź do artykułu [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Polecenie Robocopy i kilka przykładów).

Jeśli podczas kopiowania wystąpią błędy, zostanie wyświetlone powiadomienie.

![Powiadomienie o błędzie kopiowania na stronie Łączenie i kopiowanie](media/data-box-deploy-copy-data/view-errors-1.png)

Wybierz pozycję **Pobierz listę problemów**.

![Błędy pobierania i wyświetlania na stronie Łączenie i kopiowanie 2](media/data-box-deploy-copy-data/view-errors-2.png)

Otwórz listę, aby wyświetlić szczegóły błędu, a następnie wybierz adres URL rozwiązania, aby wyświetlić zalecane rozwiązanie.

![Błędy pobierania i wyświetlania na stronie Łączenie i kopiowanie 3](media/data-box-deploy-copy-data/view-errors-3.png)

Aby uzyskać więcej informacji, zobacz [Wyświetlanie dzienników błędów podczas kopiowania danych na urządzenie Data Box](data-box-logs.md#view-error-log-during-data-copy). Aby uzyskać szczegółową listę błędów występujących podczas kopiowania danych, zobacz [Rozwiązywanie problemów z urządzeniem Data Box](data-box-troubleshoot.md).

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.

![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data/verify-used-space-dashboard.png)

::: zone-end

::: zone target="chromeless"

Dane z serwera źródłowego można kopiować na urządzenie Data Box za pośrednictwem protokołu SMB, sieciowego systemu plików, interfejsu REST, usługi kopiowania danych lub na dyski zarządzane.

Niezależnie od wybranej metody należy się upewnić, że nazwy udziałów i folderów oraz rozmiar danych są zgodne z instrukcjami przedstawionymi w temacie [Ograniczenia usług Azure Storage i Data Box](data-box-limits.md).

## <a name="copy-data-via-smb"></a>Kopiowanie danych za pośrednictwem protokołu SMB

Aby skopiować dane za pośrednictwem protokołu SMB:

1. Jeśli korzystasz z hosta z systemem Windows, użyj następującego polecenia w celu nawiązania połączenia z udziałami SMB:

    `\\<IP address of your device>\ShareName`

2. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **Connect & copy** (Połączenie i kopiowanie) w lokalnym internetowym interfejsie użytkownika urządzenia Data Box.
3. Do kopiowania danych do udziałów użyj dowolnego narzędzia kopiowania plików zgodnego z protokołem SMB, na przykład narzędzia Robocopy. 

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Kopiowanie danych na urządzenie Azure Data Box za pośrednictwem protokołu SMB](data-box-deploy-copy-data.md).

## <a name="copy-data-via-nfs"></a>Kopiowanie danych za pośrednictwem sieciowego systemu plików

Aby skopiować dane za pośrednictwem sieciowego systemu plików:

1. W przypadku korzystania z hosta sieciowego systemu plików (NFS) użyj następującego polecenia, aby zainstalować udziały NFS na urządzeniu Data Box:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **Connect & copy** (Połączenie i kopiowanie) w lokalnym internetowym interfejsie użytkownika urządzenia Data Box.
3. Użyj polecenia `cp` lub `rsync`, aby skopiować dane.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Kopiowanie danych na urządzenie Azure Data Box przy użyciu systemu plików NFS](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-rest"></a>Kopiowanie danych za pośrednictwem interfejsu REST

Aby skopiować dane za pośrednictwem interfejsu REST:

1. Aby skopiować dane przy użyciu magazynu obiektów blob usługi Data Box za pośrednictwem interfejsów API REST, możesz nawiązać połączenie za pośrednictwem protokołu *HTTP* lub *HTTPS*.
2. Do kopiowania danych do magazynu obiektów blob usługi Data Box można użyć programu AzCopy.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Kopiowanie danych do magazynu obiektów blob usługi Azure Data Box za pośrednictwem interfejsów API REST](data-box-deploy-copy-data-via-nfs.md).

## <a name="copy-data-via-data-copy-service"></a>Kopiowanie danych za pomocą usługi kopiowania danych

Aby skopiować dane za pomocą usługi kopiowania danych:

1. Aby skopiować dane za pomocą usługi kopiowania danych, należy utworzyć zadanie. W lokalnym internetowym interfejsie użytkownika urządzenia Data Box przejdź do pozycji **Manage > Copy data > Create** (Zarządzanie > Kopiowanie danych > Utwórz).
2. Podaj parametry i utwórz zadanie.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Korzystanie z usługi kopiowania danych w celu skopiowania danych na urządzenie Azure Data Box](data-box-deploy-copy-data-via-copy-service.md).

## <a name="copy-data-to-managed-disks"></a>Kopiowanie danych na dyski zarządzane

Aby skopiować zarządzane dyski danych:

1. Podczas zamawiania urządzenia Data Box należy wybrać dyski zarządzane jako miejsce docelowe magazynu.
2. Z urządzeniem Data Box możesz nawiązać połączenie za pośrednictwem udziałów SMB lub NFS.
3. Następnie można skopiować dane za pomocą narzędzi SMB lub NFS.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Korzystanie z urządzenia Data Box do importowania danych jako dysków zarządzanych na platformie Azure](data-box-deploy-copy-data-from-vhds.md).

::: zone-end

::: zone target="docs"

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

::: zone-end
