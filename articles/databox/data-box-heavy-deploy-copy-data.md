---
title: Samouczek dotyczący kopiowania danych za pośrednictwem protokołu SMB na urządzenie Azure Data Box Heavy | Microsoft Docs
description: W tym samouczku dowiesz się, jak nawiązać połączenie z urządzeniem Azure Data Box Heavy i skopiować na nie dane z komputera-hosta za pomocą protokołu SMB z lokalnym internetowym interfejsem użytkownika.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 08/29/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 77dc64d9660f9a0bf66559c4a5a976362cf1acd0
ms.sourcegitcommit: a2d8acc1b0bf4fba90bfed9241b299dc35753ee6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/12/2020
ms.locfileid: "91951642"
---
::: zone target = "docs"

# <a name="tutorial-copy-data-to-azure-data-box-heavy-via-smb"></a>Samouczek: Kopiowanie danych na urządzenie Azure Data Box Heavy za pośrednictwem protokołu SMB

::: zone-end

::: zone target = "chromeless"

## <a name="copy-data-to-azure-data-box-heavy"></a>Kopiowanie danych na urządzenie Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

W tym samouczku opisano sposób nawiązywania połączenia i kopiowania danych z komputera-hosta za pomocą lokalnego internetowego interfejsu użytkownika.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy
> * Kopiowanie danych na urządzenie Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Dane z serwera źródłowego można kopiować na urządzenie Data Box za pośrednictwem protokołu SMB, sieciowego systemu plików, interfejsu REST, usługi kopiowania danych lub na dyski zarządzane.

Niezależnie od wybranej metody należy się upewnić, że nazwy udziałów i folderów oraz rozmiar danych są zgodne z instrukcjami przedstawionymi w temacie [Ograniczenia usług Azure Storage i Data Box Heavy](data-box-heavy-limits.md).

::: zone-end

::: zone target = "docs"

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Konfigurowanie urządzenia Azure Data Box Heavy](data-box-deploy-set-up.md).
2. Urządzenie Data Box Heavy zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Masz komputer-host zawierający dane, które mają zostać skopiowane na urządzenie Data Box Heavy. Na komputerze hosta wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-system-requirements.md).
    - Połączenie z siecią o dużej szybkości. Aby uzyskać największe szybkości kopiowania, można użyć dwóch równoległych połączeń 40-GbE (po jednym na węzeł). Jeśli nie masz dostępnego połączenia 40-GbE, zalecamy skorzystanie z co najmniej dwóch połączeń 10-GbE (po jednym na węzeł).
   

## <a name="connect-to-data-box-heavy-shares"></a>Nawiązywanie połączenia z udziałami plików urządzenia Data Box Heavy

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
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddress>\<StorageAccountName_BlockBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_PageBlob>\<ContainerName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `\\<DeviceIPAddres>\<StorageAccountName_AzFile>\<ShareName>\files\a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |      

Czynności wymagane do nawiązania połączenia za pomocą klienta systemu Windows i Linux są różne.

> [!NOTE]
> Wykonaj te same czynności, aby jednocześnie nawiązać połączenie z obydwoma węzłami urządzenia.

### <a name="connect-on-a-windows-system"></a>Łączenie w systemie Windows

Jeśli używasz komputera-hosta z systemem Windows Server, wykonaj następujące kroki, aby nawiązać połączenie z urządzeniem Data Box Heavy.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Kliknij przycisk **Pobierz poświadczenia**, aby pobrać poświadczenia dostępu do udziałów skojarzonych z kontem magazynu.

    ![Pobieranie poświadczeń udziału](media/data-box-heavy-deploy-copy-data/get-share-credentials-1.png)

2. W oknie dialogowym Uzyskiwanie dostępu do udziału i kopiowanie danych skopiuj wartości pól **Nazwa użytkownika** i **Hasło** odpowiedniego udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału 2](media/data-box-heavy-deploy-copy-data/get-share-credentials-2.png)

3. Aby uzyskać dostęp do udziałów skojarzonych z kontem magazynu (*databoxe2etest* w poniższym przykładzie) z komputera-hosta, otwórz okno polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    W zależności od formatu danych ścieżki udziałów są następujące:
    - Blokowe obiekty blob platformy Azure — `\\10.100.10.100\databoxe2etest_BlockBlob`
    - Stronicowe obiekty blob platformy Azure — `\\10.100.10.100\databoxe2etest_PageBlob`
    - Pliki platformy Azure — `\\10.100.10.100\databoxe2etest_AzFile`
    
4. Po wyświetleniu monitu wprowadź hasło dla udziału. W poniższym przykładzie pokazano nawiązywanie połączenia z udziałem za pomocą poprzedniego polecenia.

    ```
    C:\Users\Databoxuser>net use \\10.100.10.100\databoxe2etest_BlockBlob /u:databoxe2etest
    Enter the password for 'databoxe2etest' to connect to '10.100.10.100':
    The command completed successfully.
    ```

4. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>`. Kliknij przycisk **OK**, aby otworzyć Eksploratora plików.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-1.png)

    Teraz udziały powinny być widoczne jako foldery.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-heavy-deploy-copy-data/connect-shares-file-explorer-2.png)

    **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.
    
### <a name="connect-on-a-linux-system"></a>Łączenie w systemie Linux

W przypadku korzystania z klienta systemu Linux użyj następującego polecenia, aby zainstalować udział SMB.

```
sudo mount -t nfs -o vers=2.1 10.126.76.172:/databoxe2etest_BlockBlob /home/databoxubuntuhost/databox
```

Parametr `vers` to wersja protokołu SMB obsługiwana przez Twój host z systemem Linux. Podłącz odpowiednią wersję w poleceniu powyżej.

W przypadku wersji protokołu SMB obsługiwanych przez urządzenia Data Box Heavy zobacz [Obsługiwane systemy plików dla klientów systemu Linux](data-box-heavy-system-requirements.md#supported-file-systems-for-linux-clients).

## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych na urządzenie Data Box Heavy

Po nawiązaniu połączenia z udziałami urządzenia Data Box Heavy następnym krokiem jest skopiowanie danych.

### <a name="copy-considerations"></a>Zagadnienia dotyczące kopiowania

Przed rozpoczęciem kopiowania danych należy uwzględnić następujące kwestie:

- Upewnij się, że dane są kopiowane do udziałów odpowiadających właściwym formatom danych. To znaczy na przykład, że dane blokowych obiektów blob są kopiowane do udziału dla blokowych obiektów blob. Skopiuj wirtualne dyski twarde do stronicowego obiektu blob.

    Jeśli format danych nie pasuje do odpowiedniego typu udziału, na późniejszym etapie przekazywanie danych na platformę Azure zakończy się niepowodzeniem.
-  Podczas kopiowania danych upewnij się, że rozmiar danych jest zgodny z ograniczeniami rozmiaru opisanymi w temacie [Limity usługi Azure Storage i urządzenia Data Box Heavy](data-box-heavy-limits.md).
- Jeśli dane przekazywane przy użyciu urządzenia Data Box Heavy będą jednocześnie przekazywane przez inne aplikacje, poza urządzeniem Data Box Heavy, skutkiem może być niepowodzenie zadania przekazywania oraz uszkodzenie danych.
- Zalecenia:
    - Nie używaj protokołów SMB i NFS w tym samym czasie.
    - Kopiuj te same dane do tego samego miejsca docelowego na platformie Azure.
     
  W takich przypadkach nie można określić ostatecznego wyniku.
- Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Plików nie można kopiować bezpośrednio do folderu *głównego* na koncie magazynu.

Po nawiązaniu połączenia z udziałem SMB rozpocznij kopiowanie danych.

1. Do kopiowania danych możesz użyć dowolnego narzędzia kopiowania plików zgodnego z protokołem SMB, na przykład narzędzia Robocopy. Za pomocą narzędzia Robocopy można zainicjować wiele zadań kopiowania. Użyj następującego polecenia:
    
    ```
    robocopy <Source> <Target> * /e /r:3 /w:60 /is /nfl /ndl /np /MT:32 or 64 /fft /Log+:<LogFile>
    ```
    Atrybuty opisano w poniższej tabeli.
    
    |Atrybut  |Opis  |
    |---------|---------|
    |/e      |Kopiuje podkatalogi, włącznie z pustymi katalogami.         |
    |/r:     |Określa liczbę ponownych prób w przypadku niepowodzenia kopiowania.         |
    |/w:     |Określa czas oczekiwania pomiędzy ponownymi próbami w sekundach.         |
    |/is     |Zawiera te same pliki.         |
    |/nfl    |Określa, że nazwy plików nie są rejestrowane.         |
    |/ndl    |Określa, że nazwy katalogów nie są rejestrowane.        |
    |/np     |Określa, że postęp operacji kopiowania (liczba skopiowanych do tej pory plików lub katalogów) nie będzie wyświetlany. Wyświetlanie postępu znacznie obniża wydajność.         |
    |/MT     | Użyj wielowątkowości (zaleca się użycie 32 lub 64 wątków). Ta opcja nie jest używana z plikami zaszyfrowanymi. Może być konieczne oddzielne plików zaszyfrowanych od niezaszyfrowanych. Jednak kopiowanie jednowątkowe znacznie obniża wydajność.           |
    |/fft    | Użyj, aby zmniejszyć stopień szczegółowości sygnatury czasu dla każdego systemu plików.        |
    |/b      | Kopiuje pliki w trybie tworzenia kopii zapasowej.        |
    |/z      | Kopiuje pliki w trybie ponownego uruchomienia. Użyj tego atrybutu w przypadku niestabilnego środowiska. Ta opcja powoduje zmniejszenie przepływności ze względu na dodatkowe rejestrowanie.      |
    | /zb    | Używa trybu ponownego uruchomienia. W przypadku odmowy dostępu ta opcja używa trybu tworzenia kopii zapasowej. Ta opcja powoduje zmniejszenie przepływności ze względu na tworzenie punktów kontrolnych.         |
    |/efsraw | Kopiuje wszystkie zaszyfrowane pliki w surowym trybie EFS. Używaj tej opcji tylko względem zaszyfrowanych plików.         |
    |log+:\<LogFile>| Dołącza dane wyjściowe do istniejącego pliku dziennika.|
    
 
    Poniższy przykład przedstawia dane wyjściowe polecenia kopiowania plików na urządzenie Data Box Heavy za pomocą narzędzia Robocopy.

    ```   
    C:\Users>Robocopy C:\Git\azure-docs-pr\contributor-guide \\10.100.10.100\devicemanagertest1_AzFile\templates /MT:24
    -------------------------------------------------------------------------------
        ROBOCOPY     ::     Robust File Copy for Windows
    -------------------------------------------------------------------------------
        Started : Thursday, April 4, 2019 2:34:58 PM
        Source : C:\Git\azure-docs-pr\contributor-guide\
        Dest : \\10.100.10.100\devicemanagertest1_AzFile\templates\
        Files : *.*
        Options : *.* /DCOPY:DA /COPY:DAT /MT:24 /R:5 /W:60
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

2. Aby zoptymalizować wydajność, użyj poniższych parametrów polecenia robocopy podczas kopiowania danych. Zamieszczone poniżej liczby reprezentują najlepsze scenariusze przypadku.

    | Platforma    | Przeważnie małe pliki < 512 KB    | Przeważnie średnie pliki od 512 KB do 1 MB  | Przeważnie duże pliki > 1 MB                             |
    |-------------|--------------------------------|----------------------------|----------------------------|
    | Data Box Heavy | 6 sesji narzędzia Robocopy <br> 24 wątki na sesję | 6 sesji narzędzia Robocopy <br> 16 wątków na sesję | 6 sesji narzędzia Robocopy <br> 16 wątków na sesję |


    Aby uzyskać więcej informacji na temat polecenia Robocopy, przejdź do artykułu [Robocopy and a few examples](https://social.technet.microsoft.com/wiki/contents/articles/1073.robocopy-and-a-few-examples.aspx) (Polecenie Robocopy i kilka przykładów).

3. Otwórz folder docelowy, aby wyświetlić i zweryfikować skopiowane pliki.

    ![Wyświetlanie skopiowanych plików](media/data-box-heavy-deploy-copy-data/view-copied-files-1.png)


4. Podczas kopiowania danych wykonywane są następujące operacje:

    - Nazwy, rozmiary i format plików są weryfikowane w celu upewnienia się, że spełniają one limity dotyczące obiektów i magazynów platformy Azure, a także że są zgodne z konwencjami nazewnictwa plików i kontenerów platformy Azure.
    - W celu zapewnienia integralności danych obliczana jest również suma kontrolna.

    Jeśli podczas procesu kopiowania wystąpiły jakiekolwiek błędy, pobierz pliki z błędami, które pomogą w rozwiązywaniu problemów. Wybierz ikonę strzałki, aby pobrać pliki z błędami.

    ![Pobieranie plików z błędami](media/data-box-heavy-deploy-copy-data/download-error-files.png)

    Aby uzyskać więcej informacji, zobacz [Wyświetlanie dzienników błędów podczas kopiowania danych na urządzenie Data Box Heavy](data-box-logs.md#view-error-log-during-data-copy). Aby uzyskać szczegółową listę błędów występujących podczas kopiowania danych, zobacz [Rozwiązywanie problemów z urządzeniem Data Box Heavy](data-box-troubleshoot.md).

5. Otwórz plik błędu w Notatniku. Następujący plik błędu wskazuje, że dane nie są prawidłowo wyrównane.

    ![Otwieranie pliku błędu](media/data-box-heavy-deploy-copy-data/open-error-file.png)
    
    W przypadku stronicowego obiektu blob dane muszą być wyrównane do 512 bajtów. Po usunięciu tych danych błąd nie będzie już wyświetlany, jak pokazano na poniższym zrzucie ekranu.

    ![Błąd usunięty](media/data-box-heavy-deploy-copy-data/error-resolved.png)

6. Po zakończeniu kopiowania przejdź do strony **Wyświetl pulpit nawigacyjny**. Sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
    ![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-heavy-deploy-copy-data/verify-used-space-dashboard.png)

Powtórz powyższe kroki, aby skopiować dane do drugiego węzła urządzenia.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Heavy, takie jak:

> [!div class="checklist"]
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy
> * Kopiowanie danych na urządzenie Data Box Heavy


Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box Heavy do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box Heavy do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

::: zone-end

::: zone target = "chromeless"

### <a name="copy-data-via-smb"></a>Kopiowanie danych za pośrednictwem protokołu SMB

1. Jeśli korzystasz z hosta z systemem Windows, użyj następującego polecenia w celu nawiązania połączenia z udziałami SMB:

    `\\<IP address of your device>\ShareName`

2. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **Connect & copy** (Połączenie i kopiowanie) w lokalnym internetowym interfejsie użytkownika urządzenia Data Box.

3. Do kopiowania danych do udziałów użyj dowolnego narzędzia kopiowania plików zgodnego z protokołem SMB, na przykład narzędzia Robocopy.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Kopiowanie danych na urządzenie Azure Data Box za pośrednictwem protokołu SMB](data-box-heavy-deploy-copy-data.md).

### <a name="copy-data-via-nfs"></a>Kopiowanie danych za pośrednictwem sieciowego systemu plików

1. W przypadku korzystania z hosta sieciowego systemu plików (NFS) użyj następującego polecenia, aby zainstalować udziały NFS:

    `sudo mount <Data Box device IP>:/<NFS share on Data Box device> <Path to the folder on local Linux computer>`

2. Aby uzyskać poświadczenia dostępu do udziału, przejdź do strony **Connect & copy** (Połączenie i kopiowanie) w lokalnym internetowym interfejsie użytkownika urządzenia Data Box Heavy.
3. Użyj polecenia `cp` lub `rsync`, aby skopiować dane. 
4. Powtórz te kroki, aby nawiązać połączenie z drugim węzłem urządzenia Data Box Heavy i skopiować z niego dane.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Kopiowanie danych na urządzenie Azure Data Box przy użyciu systemu plików NFS](data-box-heavy-deploy-copy-data-via-nfs.md).

### <a name="copy-data-via-rest"></a>Kopiowanie danych za pośrednictwem interfejsu REST

1. Aby skopiować dane przy użyciu magazynu obiektów blob usługi Data Box za pośrednictwem interfejsów API REST, możesz nawiązać połączenie za pośrednictwem protokołu *HTTP* lub *HTTPS*.
2. Do kopiowania danych do magazynu obiektów blob usługi Data Box można użyć programu AzCopy.
3. Powtórz te kroki, aby nawiązać połączenie z drugim węzłem urządzenia Data Box Heavy i skopiować z niego dane.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Kopiowanie danych do magazynu obiektów blob usługi Azure Data Box za pośrednictwem interfejsów API REST](data-box-heavy-deploy-copy-data-via-rest.md).

### <a name="copy-data-via-data-copy-service"></a>Kopiowanie danych za pomocą usługi kopiowania danych

1. Aby skopiować dane za pomocą usługi kopiowania danych, należy utworzyć zadanie. W lokalnym internetowym interfejsie użytkownika urządzenia Data Box Heavy przejdź do pozycji **Manage > Copy data > Create** (Zarządzanie > Kopiowanie danych > Utwórz).
2. Podaj parametry i utwórz zadanie.
3. Powtórz te kroki, aby nawiązać połączenie z drugim węzłem urządzenia Data Box Heavy i skopiować z niego dane.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Korzystanie z usługi kopiowania danych w celu skopiowania danych na urządzenie Azure Data Box Heavy](data-box-heavy-deploy-copy-data-via-copy-service.md).

### <a name="copy-data-to-managed-disks"></a>Kopiowanie danych na dyski zarządzane

1. Podczas zamawiania urządzenia Data Box Heavy należy wybrać dyski zarządzane jako miejsce docelowe magazynu.
2. Z urządzeniem Data Box Heavy możesz nawiązać połączenie za pośrednictwem udziałów SMB lub NFS.
3. Następnie można skopiować dane za pomocą narzędzi SMB lub NFS.
4. Powtórz te kroki, aby nawiązać połączenie z drugim węzłem urządzenia Data Box Heavy i skopiować z niego dane.

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Korzystanie z urządzenia Data Box Heavy do importowania danych jako dysków zarządzanych na platformie Azure](data-box-heavy-deploy-copy-data-from-vhds.md).

::: zone-end


