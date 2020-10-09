---
title: Samouczek dotyczący rozpakowywania, podłączania i odblokowywania urządzenia Azure Data Box Disk | Microsoft Docs
description: W tym samouczku dowiesz się, jak rozpakować urządzenie Azure Data Box Disk, nawiązać połączenie z dyskami, uzyskać klucz dostępu i odblokować dyski na klientach z systemem Windows i Linux.
services: databox
author: alkohli
ms.service: databox
ms.subservice: disk
ms.topic: tutorial
ms.date: 09/04/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: 77c09385c69e4a2922fa3b2249143e6e969bfc5a
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91759908"
---
::: zone target="docs"

# <a name="tutorial-unpack-connect-and-unlock-azure-data-box-disk"></a>Samouczek: Rozpakowywanie, podłączanie i odblokowywanie urządzenia Azure Data Box Disk

W tym samouczku opisano, jak rozpakować, podłączyć i odblokować urządzenie Azure Data Box Disk.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia Data Box Disk
> * Nawiązywanie połączenia z dyskami i uzyskiwanie klucza dostępu
> * Odblokowywanie dysków na komputerze klienckim z systemem Windows
> * Odblokowywanie dysków na komputerze klienckim z systemem Linux

::: zone-end

::: zone target="chromeless"

## <a name="unpack-connect-and-unlock-azure-data-box-disk"></a>Rozpakowywanie, podłączanie i odblokowywanie urządzenia Azure Data Box Disk

::: zone-end

::: zone target="docs"

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: zamawianie urządzenia Azure Data Box Disk](data-box-disk-deploy-ordered.md).
2. Dyski zostały do Ciebie dostarczone, a stan zadania w portalu zmienił się na **Dostarczono**.
3. Masz komputer kliencki, na którym możesz zainstalować narzędzie do odblokowywania dysków Data Box Disk. Na komputerze klienckim wymagane jest:
    - Korzystanie z [obsługiwanego systemu operacyjnego](data-box-disk-system-requirements.md#supported-operating-systems-for-clients).
    - Zainstalowanie innego [wymaganego oprogramowania](data-box-disk-system-requirements.md#other-required-software-for-windows-clients) w przypadku klienta z systemem Windows.  

## <a name="unpack-your-disks"></a>Rozpakowywanie dysków

 Aby rozpakować dyski, wykonaj następujące czynności.

1. Urządzenia Data Box Disk są przesyłane w niewielkim opakowaniu wysyłkowym. Otwórz opakowanie i wyjmij zawartość. Sprawdź, czy opakowanie zawiera od 1 do 5 dysków półprzewodnikowych (SSD) oraz jeden kabel połączeniowy USB dla każdego dysku. Sprawdź, czy opakowanie nie ma widocznych uszkodzeń lub innych śladów naruszenia.

    ![Opakowanie wysyłkowe urządzeń Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-ship-package1.png)

2. Jeśli opakowanie wysyłkowe nosi ślady naruszenia lub jest poważnie uszkodzone, nie otwieraj go. Skontaktuj się z pracownikiem pomocy technicznej firmy Microsoft, który pomoże Ci ocenić, czy dyski są w dobrym stanie, czy konieczne jest wysłanie nowych.
3. Sprawdź, czy opakowanie zawiera przezroczystą koszulkę z etykietą wysyłkową do wysyłki zwrotnej (pod aktualną etykietą). W przypadku utraty lub uszkodzenia etykiety zwrotnej możesz wydrukować nową etykietę pobraną z witryny Azure Portal.

    ![Etykieta wysyłkowa urządzenia Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-package-ship-label.png)

4. Zachowaj opakowanie i piankę wypełniającą do wysyłki zwrotnej dysków.

## <a name="connect-to-disks-and-get-the-passkey"></a>Nawiązywanie połączenia z dyskami i uzyskiwanie klucza dostępu

1. Za pomocą dołączonego kabla podłącz dysk do komputera klienckiego z obsługiwanym systemem operacyjnym, wymienionym w wymaganiach wstępnych.

    ![Podłączanie urządzenia Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-connect-unlock.png)

2. W witrynie Azure Portal przejdź do zamówienia urządzenia Data Box Disk. Wyszukaj je, wybierając pozycję **Ogólne > Wszystkie zasoby**, a następnie wybierz zamówienie urządzenia Data Box Disk. Skopiuj kod dostępu, używając ikony kopiowania. Ten klucz dostępu będzie służyć do odblokowywania dysków.

    ![Klucz dostępu do odblokowywania dysków Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-get-passkey.png)

Kroki odblokowywania dysków zależą od tego, czy masz połączenie z klientem z systemem Windows czy Linux.

## <a name="unlock-disks-on-windows-client"></a>Odblokowywanie dysków na komputerze klienckim z systemem Windows

Aby podłączyć i odblokować dyski, wykonaj następujące czynności.

1. W witrynie Azure Portal przejdź do zamówienia urządzenia Data Box Disk. Wyszukaj je, wybierając pozycję **Ogólne > Wszystkie zasoby**, a następnie wybierz zamówienie urządzenia Data Box Disk.
2. Pobierz zestaw narzędzi dla dysków Data Box Disk przeznaczony dla klienta z systemem Windows. Ten zestaw narzędzi zawiera trzy narzędzia: narzędzie do odblokowywania dysków Data Box Disk, narzędzie do weryfikacji dysków Data Box Disk oraz narzędzie do dzielenia skopiowanych dysków Data Box Disk.

    W procedurze poniżej będziesz używać wyłącznie narzędzia do odblokowywania dysków Data Box Disk. Pozostałe dwa narzędzia zostaną użyte później.

    > [!div class="nextstepaction"]
    > [Pobierz zestaw narzędzi dla dysków Data Box Disk dla systemu Windows](https://aka.ms/databoxdisktoolswin)

3. Wyodrębnij zestaw narzędzi na komputerze, którego użyjesz do skopiowania danych.
4. Otwórz okno wiersza polecenia lub uruchom program Windows PowerShell jako administrator na tym samym komputerze.
5. (Opcjonalnie) Aby sprawdzić, czy komputer, którego używasz do odblokowania dysku, spełnia wymagania dotyczące systemu operacyjnego, uruchom polecenie SystemCheck. Poniżej pokazano przykładowe dane wyjściowe.

    ```powershell
    Windows PowerShell
    Copyright (C) Microsoft Corporation. All rights reserved.

    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /SystemCheck
    Successfully verified that the system can run the tool.
    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ``` 

6. Uruchom plik `DataBoxDiskUnlock.exe` i podaj klucz dostępu uzyskany w sekcji [Nawiązywanie połączenia z dyskami i uzyskiwanie klucza dostępu](#connect-to-disks-and-get-the-passkey). Zostanie wyświetlona litera dysku przypisana do danego dysku. Poniżej pokazano przykładowe dane wyjściowe.

    ```powershell
    PS C:\WINDOWS\system32> cd C:\DataBoxDiskUnlockTool\DiskUnlock
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe
    Enter the passkey :
    testpasskey1

    Following volumes are unlocked and verified.
    Volume drive letters: D:

    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```

7. Kroki odblokowywania należy powtórzyć po podłączeniu każdego kolejnego dysku. Użyj polecenia `help`, jeśli potrzebujesz pomocy podczas korzystania z narzędzia do odblokowywania dysków Data Box Disk.

    ```powershell
    PS C:\DataBoxDiskUnlockTool\DiskUnlock> .\DataBoxDiskUnlock.exe /help
    USAGE:
    DataBoxUnlock /PassKey:<passkey_from_Azure_portal>

    Example: DataBoxUnlock /PassKey:<your passkey>
    Example: DataBoxUnlock /SystemCheck
    Example: DataBoxUnlock /Help

    /PassKey:        Get this passkey from Azure DataBox Disk order. The passkey unlocks your disks.
    /SystemCheck:    This option checks if your system meets the requirements to run the tool.
    /Help:           This option provides help on cmdlet usage and examples.

    PS C:\DataBoxDiskUnlockTool\DiskUnlock>
    ```  

8. Po odblokowaniu dysku możesz wyświetlić jego zawartość.

    ![Zawartość urządzenia Data Box Disk](media/data-box-disk-deploy-set-up/data-box-disk-content.png)

Jeśli podczas odblokowywania dysków wystąpią jakiekolwiek problemy, zobacz jak [rozwiązywać problemy z odblokowywaniem](data-box-disk-troubleshoot-unlock.md).

## <a name="unlock-disks-on-linux-client"></a>Odblokowywanie dysków na komputerze klienckim z systemem Linux

1. W witrynie Azure Portal przejdź do pozycji **Ogólne > Szczegóły urządzenia**.
2. Pobierz zestaw narzędzi dla dysków Data Box Disk przeznaczony dla klienta z systemem Linux.  

    > [!div class="nextstepaction"]
    > [Pobierz zestaw narzędzi dla dysków Data Box Disk dla systemu Linux](https://aka.ms/databoxdisktoolslinux)

3. Otwórz terminal na komputerze klienckim z systemem Linux. Przejdź do folderu, do którego pobrano oprogramowanie. Zmień uprawnienia do plików, aby umożliwić ich uruchomienie. Wpisz następujące polecenie:

    `chmod +x DataBoxDiskUnlock_x86_64` 

    `chmod +x DataBoxDiskUnlock_Prep.sh` 

    Poniżej pokazano przykładowe dane wyjściowe. Po uruchomieniu polecenia chmod możesz sprawdzić, czy uprawnienia do plików zostały zmienione, uruchamiając polecenie `ls`. 
 
    ```
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_x86_64  
        [user@localhost Downloads]$ chmod +x DataBoxDiskUnlock_Prep.sh   
        [user@localhost Downloads]$ ls -l  
        -rwxrwxr-x. 1 user user 1152664 Aug 10 17:26 DataBoxDiskUnlock_x86_64  
        -rwxrwxr-x. 1 user user 795 Aug 5 23:26 DataBoxDiskUnlock_Prep.sh
    ```

4. Uruchom skrypt, który instaluje wszystkie pliki binarne wymagane przez oprogramowanie do odblokowywania dysków Data Box Disk. Użyj programu `sudo`, aby uruchomić polecenie jako użytkownik główny. Po pomyślnym zainstalowaniu plików binarnych na terminalu zostanie wyświetlony odpowiedni komunikat.

    `sudo ./DataBoxDiskUnlock_Prep.sh`

    Na początku skrypt sprawdzi, czy na komputerze klienckim jest uruchomiony obsługiwany system operacyjny. Poniżej pokazano przykładowe dane wyjściowe. 
 
    ```
    [user@localhost Documents]$ sudo ./DataBoxDiskUnlock_Prep.sh 
        OS = CentOS Version = 6.9 
        Release = CentOS release 6.9 (Final) 
        Architecture = x64 

        The script will install the following packages and dependencies. 
        epel-release 
        dislocker 
        ntfs-3g 
        fuse-dislocker 
        Do you wish to continue? y|n :|
    ```

 
5. Wpisz tekst `y`, aby kontynuować instalację. Skrypt zainstaluje następujące pakiety: 
   - **epel-release** — repozytorium zawierające następujące trzy pakiety. 
   - **dislocker i fuse-dislocker** — te narzędzia ułatwiają odszyfrowywanie dysków zaszyfrowanych za pomocą funkcji BitLocker. 
   - **ntfs-3g** — pakiet ułatwiający instalowanie woluminów NTFS. 
 
     Po pomyślnym zainstalowaniu pakietów na terminalu pojawi się odpowiednie powiadomienie.     
     ```
     Dependency Installed: compat-readline5.x86 64 0:5.2-17.I.el6 dislocker-libs.x86 64 0:0.7.1-8.el6 mbedtls.x86 64 0:2.7.4-l.el6        ruby.x86 64 0:1.8.7.374-5.el6 
     ruby-libs.x86 64 0:1.8.7.374-5.el6 
     Complete! 
     Loaded plugins: fastestmirror, refresh-packagekit, security 
     Setting up Remove Process 
     Resolving Dependencies 
     --> Running transaction check 
     ---> Package epel-release.noarch 0:6-8 will be erased --> Finished Dependency Resolution 
     Dependencies Resolved 
     Package        Architecture        Version        Repository        Size 
     Removing:  epel-release        noarch         6-8        @extras        22 k 
     Transaction Summary                                 
     Remove        1 Package(s) 
     Installed size: 22 k 
     Downloading Packages: 
     Running rpmcheckdebug 
     Running Transaction Test 
     Transaction Test Succeeded 
     Running Transaction 
     Erasing : epel-release-6-8.noarch 
     Verifying : epel-release-6-8.noarch 
     Removed: 
     epel-release.noarch 0:6-8 
     Complete! 
     Dislocker is installed by the script. 
     OpenSSL is already installed.
     ```

6. Uruchom narzędzie do odblokowywania dysków Data Box Disk. Podaj klucz dostępu z witryny Azure Portal uzyskany w sekcji [Nawiązywanie połączenia z dyskami i uzyskiwanie klucza dostępu](#connect-to-disks-and-get-the-passkey). Opcjonalnie możesz podać listę woluminów zaszyfrowanych za pomocą funkcji BitLocker, które chcesz odblokować. Klucz dostępu i listę woluminów należy umieścić w apostrofach. 

    Wpisz następujące polecenie.
 
    `sudo ./DataBoxDiskUnlock_x86_64 /PassKey:'<Your passkey from Azure portal>'          

    Poniżej pokazano przykładowe dane wyjściowe. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Passkey:'qwerqwerqwer'  
    
    START: Mon Aug 13 14:25:49 2018 
    Volumes: /dev/sdbl 
    Passkey: qwerqwerqwer 
    
    Volumes for data copy : 
    /dev/sdbl: /mnt/DataBoxDisk/mountVoll/ 
    END: Mon Aug 13 14:26:02 2018
    ```
    Zostanie wyświetlony punkt instalacji woluminu, na który możesz skopiować dane.

7. Kroki odblokowywania należy powtórzyć po podłączeniu każdego kolejnego dysku. Użyj polecenia `help`, jeśli potrzebujesz pomocy podczas korzystania z narzędzia do odblokowywania dysków Data Box Disk. 
    
    `sudo ./DataBoxDiskUnlock_x86_64 /Help` 

    Poniżej pokazano przykładowe dane wyjściowe. 
 
    ```
    [user@localhost Downloads]$ sudo ./DataBoxDiskUnlock_x86_64 /Help  
    START: Mon Aug 13 14:29:20 2018 
    USAGE: 
    sudo DataBoxDiskUnlock /PassKey:'<passkey from Azure_portal>' 
    
    Example: sudo DataBoxDiskUnlock /PassKey:'passkey' 
    Example: sudo DataBoxDiskUnlock /PassKey:'passkey' /Volumes:'/dev/sdbl' 
    Example: sudo DataBoxDiskUnlock /Help Example: sudo DataBoxDiskUnlock /Clean 
    
    /PassKey: This option takes a passkey as input and unlocks all of your disks. 
    Get the passkey from your Data Box Disk order in Azure portal. 
    /Volumes: This option is used to input a list of BitLocker encrypted volumes. 
    /Help: This option provides help on the tool usage and examples. 
    /Unmount: This option unmounts all the volumes mounted by this tool. 
   
    END: Mon Aug 13 14:29:20 2018 [user@localhost Downloads]$
    ```
    
8. Po odblokowaniu dysku możesz przejść do punktu instalacji i wyświetlić zawartość dysku. Możesz teraz przystąpić do kopiowania danych do folderów *BlockBlob* lub *PageBlob*. 

    ![Zawartość urządzenia Data Box Disk 2](media/data-box-disk-deploy-set-up/data-box-disk-content-linux.png)


Jeśli podczas odblokowywania dysków wystąpią jakiekolwiek problemy, zobacz jak [rozwiązywać problemy z odblokowywaniem](data-box-disk-troubleshoot-unlock.md). 

::: zone-end

::: zone target="chromeless"

1. Rozpakuj dyski i użyj dołączonego kabla, aby podłączyć dysk do komputera klienckiego.
2. Pobierz i wyodrębnij zestaw narzędzi dla dysków Data Box Disk na komputerze, którego użyjesz do skopiowania danych.

    > [!div class="nextstepaction"]
    > [Pobierz zestaw narzędzi dla dysków Data Box Disk dla systemu Windows](https://aka.ms/databoxdisktoolswin)

    lub
    > [!div class="nextstepaction"]
    > [Pobierz zestaw narzędzi dla dysków Data Box Disk dla systemu Linux](https://aka.ms/databoxdisktoolslinux) 

3. Aby odblokować dyski na kliencie z systemem Windows, otwórz okno wiersza polecenia lub uruchom program Windows PowerShell jako administrator na tym samym komputerze:

    - Wpisz następujące polecenie w tym samym folderze, w którym zainstalowano narzędzie do odblokowywania dysków Data Box Disk.

        ``` 
        .\DataBoxDiskUnlock.exe
        ```
    -  Pobierz klucz dostępu z obszaru **Ogólne > Szczegóły urządzenia** w witrynie Azure Portal i podaj go tutaj. Zostanie wyświetlona litera dysku przypisana do danego dysku. 
4. Aby odblokować dyski na kliencie z systemem Linux, otwórz terminal. Przejdź do folderu, do którego pobrano oprogramowanie. Wpisz następujące polecenia, aby zmienić uprawnienia do pliku, aby można było wykonać następujące pliki: 

    ```
    chmod +x DataBoxDiskUnlock_x86_64
    chmod +x DataBoxDiskUnlock_Prep.sh
    ``` 
    Wykonaj skrypt, aby zainstalować wszystkie wymagane pliki binarne.

    ```
    sudo ./DataBoxDiskUnlock_Prep.sh
    ```
    Uruchom narzędzie do odblokowywania dysków Data Box Disk. Pobierz klucz dostępu z obszaru **Ogólne > Szczegóły urządzenia** w witrynie Azure Portal i podaj go tutaj. Opcjonalnie możesz podać umieszczoną w apostrofach listę woluminów zaszyfrowanych za pomocą funkcji BitLocker, które chcesz odblokować.

    ```
    sudo ./DataBoxDiskUnlock_x86_64 /PassKey:'<Your passkey from Azure portal>'
    ```      
5. Kroki odblokowywania należy powtórzyć po podłączeniu każdego kolejnego dysku. Użyj polecenia „help”, jeśli potrzebujesz pomocy podczas korzystania z narzędzia do odblokowywania dysków Data Box.

Po odblokowaniu dysku możesz wyświetlić jego zawartość.

Aby uzyskać więcej informacji na temat konfigurowania i odblokowywania dysków, przejdź do artykułu [Konfigurowanie urządzenia Data Box Disk](data-box-disk-deploy-set-up.md).

::: zone-end

::: zone target="docs"

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Disk, takie jak:

> [!div class="checklist"]
> * Rozpakowywanie urządzenia Data Box Disk
> * Nawiązywanie połączenia z dyskami i uzyskiwanie klucza dostępu
> * Odblokowywanie dysków na komputerze klienckim z systemem Windows
> * Odblokowywanie dysków na komputerze klienckim z systemem Linux


Przejdź do następnego samouczka, aby dowiedzieć się, jak skopiować dane na urządzenie Data Box Disk.

> [!div class="nextstepaction"]
> [Kopiowanie danych na urządzenie Data Box Disk](./data-box-disk-deploy-copy-data.md)

::: zone-end

