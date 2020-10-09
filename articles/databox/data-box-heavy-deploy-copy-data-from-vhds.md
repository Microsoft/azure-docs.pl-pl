---
title: 'Samouczek: kopiowanie z dysków VHD do dysków zarządzanych'
titleSuffix: Azure Data Box Heavy
description: Informacje o kopiowaniu danych z dysków VHD z lokalnych obciążeń maszyn wirtualnych do Azure Data Box Heavy
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 07/03/2019
ms.author: alkohli
ms.openlocfilehash: 01031159d1894c7cb5f36b48f268186dff21fd22
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "77471333"
---
# <a name="tutorial-use-data-box-heavy-to-import-data-as-managed-disks-in-azure"></a>Samouczek: używanie Data Box Heavy do importowania danych jako dysków zarządzanych na platformie Azure

W tym samouczku opisano, jak używać Azure Data Box Heavy do migrowania lokalnych dysków VHD do dysków zarządzanych na platformie Azure. Wirtualne dyski twarde z lokalnych maszyn wirtualnych są kopiowane do Data Box Heavy jako stronicowe obiekty blob i są przekazywane do platformy Azure jako dyski zarządzane. Te dyski zarządzane można następnie dołączyć do maszyn wirtualnych platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przegląd wymagań wstępnych
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy
> * Kopiowanie danych na urządzenie Data Box Heavy


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Konfigurowanie urządzenia Azure Data Box Heavy](data-box-heavy-deploy-set-up.md).
2. Urządzenie Data Box Heavy zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Dysponujesz szybkim połączeniem sieciowym. Aby uzyskać największe szybkości kopiowania, można użyć dwóch równoległych połączeń 40-GbE (po jednym na węzeł). Jeśli nie masz dostępnego połączenia 40-GbE, zalecamy skorzystanie z co najmniej dwóch połączeń 10-GbE (po jednym na węzeł). 
4. Sprawdzono:

    - Obsługiwane [rozmiary dysków zarządzanych w limitach rozmiaru obiektów platformy Azure](data-box-heavy-limits.md#azure-object-size-limits).
    - [Wprowadzenie do usługi Azure Managed disks](/azure/virtual-machines/windows/managed-disks-overview). 

## <a name="connect-to-data-box-heavy"></a>Nawiązywanie połączenia z urządzeniem Data Box Heavy

Na podstawie określonych grup zasobów Data Box Heavy tworzy jeden udział dla każdej skojarzonej grupy zasobów dla każdego węzła. Na przykład, jeśli `mydbmdrg1` i `mydbmdrg2` zostały utworzone podczas umieszczania zamówienia, tworzone są następujące udziały:

- `mydbmdrg1_MDisk`
- `mydbmdrg2_MDisk`

W każdym udziale tworzone są następujące trzy foldery, które odnoszą się do kontenerów na koncie magazynu.

- Dysk SSD w warstwie Premium
- Dysk HDD w warstwie Standardowa
- Dysk SSD w warstwie Standardowa

W poniższej tabeli przedstawiono ścieżki UNC do udziałów na Data Box Heavy.
 
|        Protokół połączenia           |             Ścieżka UNC do udziału                                               |
|-------------------|--------------------------------------------------------------------------------|
| SMB |`\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Premium SSD>\file1.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard HDD>\file2.vhd`<br> `\\<DeviceIPAddress>\<ResourceGroupName_MDisk>\<Standard SSD>\file3.vhd` |  
| NFS |`//<DeviceIPAddress>/<ResourceGroup1_MDisk>/<Premium SSD>/file1.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard HDD>/file2.vhd`<br> `//<DeviceIPAddress>/<ResourceGroupName_MDisk>/<Standard SSD>/file3.vhd` |

W zależności od tego, czy do łączenia się z udziałami Data Box Heavy są używane SMB, czy NFS, kroki do połączenia są różne.

> [!NOTE]
> - Łączenie za pośrednictwem REST nie jest obsługiwane w przypadku tej funkcji.
> - Powtórz instrukcje łączenia, aby nawiązać połączenie z drugim węzłem Data Box Heavy.

### <a name="connect-to-data-box-heavy-via-smb"></a>Nawiązywanie połączenia z Data Box Heavy za pośrednictwem protokołu SMB

Jeśli używasz komputera-hosta z systemem Windows Server, wykonaj następujące kroki, aby nawiązać połączenie z urządzeniem Data Box Heavy.

1. Pierwszym krokiem jest uwierzytelnienie i uruchomienie sesji. Przejdź do pozycji **Połącz i skopiuj**. Kliknij przycisk **Pobierz poświadczenia** , aby uzyskać poświadczenia dostępu dla udziałów skojarzonych z Twoją grupą zasobów. Możesz również uzyskać poświadczenia dostępu z **szczegółów urządzenia** w Azure Portal.

    > [!NOTE]
    > Poświadczenia dla wszystkich udziałów dla dysków zarządzanych są identyczne.

    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials1.png)

2. W oknie dialogowym Udostępnianie i kopiowanie danych Skopiuj **nazwę użytkownika** i **hasło** dla udziału. Kliknij przycisk **OK**.
    
    ![Pobieranie poświadczeń udziału 1](media/data-box-deploy-copy-data-from-vhds/get-share-credentials2.png)

3. Aby uzyskać dostęp do udziałów skojarzonych z zasobem (*mydbmdrg1* w poniższym przykładzie) z komputera hosta, Otwórz okno wiersza polecenia. W wierszu polecenia wpisz polecenie:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

    W tym przykładzie ścieżki udziału UNC są następujące:

    - `\\169.254.250.200\mydbmdrg1_MDisk`
    - `\\169.254.250.200\mydbmdrg2_MDisk`
    
4. Po wyświetleniu monitu wprowadź hasło dla udziału. W poniższym przykładzie pokazano nawiązywanie połączenia z udziałem za pomocą poprzedniego polecenia.

    ```
    C:\>net use \\169.254.250.200\mydbmdrgl_MDisk /u:mdisk
    Enter the password for ‘mdisk’ to connect to '169.254.250.200':
    The command completed successfully.
    C: \>
    ```

4. Naciśnij klawisze Windows + R. W oknie **Uruchamianie** podaj `\\<device IP address>\<ShareName>`. Kliknij przycisk **OK**, aby otworzyć Eksploratora plików.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer1.png)

    W poszczególnych udziałach powinny być teraz widoczne następujące utworzone foldery.
    
    ![Nawiązywanie połączenia z udziałem za pomocą Eksploratora plików 2](media/data-box-deploy-copy-data-from-vhds/connect-shares-file-explorer2.png)


### <a name="connect-to-data-box-heavy-via-nfs"></a>Łączenie się z usługą Data Box Heavy za pośrednictwem systemu plików NFS

Jeśli używasz komputera hosta z systemem Linux, wykonaj następujące kroki, aby skonfigurować urządzenie w celu zezwalania na dostęp do klientów NFS.

1. Podaj adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Połącz i skopiuj**. W obszarze **Ustawienia sieciowego systemu plików** kliknij przycisk **Dostęp klienta do sieciowego systemu plików**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/data-box-deploy-copy-data-from-vhds/nfs-client-access1.png)

2. Podaj adres IP klienta sieciowego systemu plików i kliknij przycisk **Dodaj**. Powtarzając ten krok, możesz skonfigurować dostęp dla wielu klientów sieciowego systemu plików. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/data-box-deploy-copy-data-from-vhds/nfs-client-access2.png)

2. Upewnij się, że na komputerze-hoście z systemem Linux zainstalowano [obsługiwaną wersję](data-box-system-requirements.md) klienta sieciowego systemu plików. Użyj konkretnej wersji dla określonej dystrybucji systemu Linux.

3. Po zainstalowaniu klienta NFS Użyj następującego polecenia, aby zainstalować udział NFS na urządzeniu:

    `sudo mount <Data Box or Data Box Heavy IP>:/<NFS share on Data Box or Data Box Heavy device> <Path to the folder on local Linux computer>`

    Poniższy przykład pokazuje, jak nawiązać połączenie za pośrednictwem systemu plików NFS z udziałem urządzenie Data Box lub Data Box Heavy. Urządzenie Data Box lub Data Box Heavy IP urządzenia to `169.254.250.200` , udział `mydbmdrg1_MDisk` jest zainstalowany w ubuntuVM, punkt instalacji `/home/databoxubuntuhost/databox` .

    `sudo mount -t nfs 169.254.250.200:/mydbmdrg1_MDisk /home/databoxubuntuhost/databox`


## <a name="copy-data-to-data-box-heavy"></a>Kopiowanie danych na urządzenie Data Box Heavy

Po nawiązaniu połączenia z serwerem danych następnym krokiem jest skopiowanie danych. Plik VHD jest kopiowany do konta magazynu przemieszczania jako obiekt BLOB strony. Obiekt BLOB stronicowania jest następnie konwertowany na dysk zarządzany i przenoszony do grupy zasobów.

Przed rozpoczęciem kopiowania danych zapoznaj się z następującymi kwestiami:

- Zawsze kopiuj wirtualne dyski twarde do jednego ze wstępnie utworzonych folderów. W przypadku kopiowania dysków VHD poza te foldery lub w utworzonym folderze wirtualne dyski twarde zostaną przekazane do konta usługi Azure Storage jako stronicowe obiekty blob i dyski niezarządzane.
- Na potrzeby tworzenia dysków zarządzanych można przekazywać tylko stałe wirtualne dyski twarde. Pliki VHDX lub dynamiczne i różnicowe dyski VHD nie są obsługiwane.
- Można mieć tylko jeden dysk zarządzany o danej nazwie w grupie zasobów we wszystkich pretworzonych folderach. Oznacza to, że wirtualne dyski twarde przekazane do wstępnie utworzonych folderów powinny mieć unikatowe nazwy. Upewnij się, że dana nazwa nie jest zgodna z nazwą już istniejącego dysku zarządzanego w grupie zasobów.
- Zapoznaj się z limitami dysku zarządzanego w obszarze [limity rozmiaru obiektów platformy Azure](data-box-heavy-limits.md#azure-object-size-limits).

W zależności od tego, czy łączysz się za pośrednictwem protokołu SMB czy NFS, możesz użyć:

- [Kopiowanie danych za pośrednictwem protokołu SMB](data-box-heavy-deploy-copy-data.md#copy-data-to-data-box-heavy)
- [Kopiowanie danych za pośrednictwem sieciowego systemu plików](data-box-heavy-deploy-copy-data-via-nfs.md#copy-data-to-data-box-heavy)

Poczekaj na zakończenie zadań kopiowania. Upewnij się, że zadania kopiowania zakończyły się bez błędów przed przejściem do następnego kroku.

![Brak błędów na stronie **Połącz i skopiuj**](media/data-box-deploy-copy-data-from-vhds/verify-no-errors-connect-and-copy.png)

Jeśli wystąpią błędy podczas procesu kopiowania, Pobierz dzienniki ze strony **Połącz i Kopiuj** .

- Jeśli skopiowano plik, który nie jest wyrównany do 512 bajtów, plik nie zostanie przekazany jako obiekt BLOB strony do konta magazynu tymczasowego. W dziennikach zostanie wyświetlony komunikat o błędzie. Usuń plik i skopiuj plik o 512 bajtów wyrównanych.

- Jeśli skopiowano plik VHDX (te pliki nie są obsługiwane) z długą nazwą, w dziennikach zostanie wyświetlony komunikat o błędzie.

    ![Błąd w dziennikach z programu * * Connect i Copy * * Page](media/data-box-deploy-copy-data-from-vhds/errors-connect-and-copy.png)

    Przed przejściem do następnego kroku Usuń błędy.

W celu zapewnienia integralności danych podczas kopiowania obliczana jest suma kontrolna. Po zakończeniu kopiowania sprawdź ilość używanego i wolnego miejsca na urządzeniu.
    
![Sprawdzanie wolnego i używanego miejsca na pulpicie nawigacyjnym](media/data-box-deploy-copy-data-from-vhds/verify-used-space-dashboard.png)

Po zakończeniu zadania kopiowania możesz przejść do sekcji **Przygotowanie do wysłania**.


## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Heavy, takie jak:

> [!div class="checklist"]
> * Przegląd wymagań wstępnych
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy
> * Kopiowanie danych na urządzenie Data Box Heavy


Przejdź do następnego samouczka, aby dowiedzieć się, jak odesłać urządzenie Data Box Heavy do firmy Microsoft.

> [!div class="nextstepaction"]
> [Wysyłka urządzenia Azure Data Box Heavy do firmy Microsoft](./data-box-heavy-deploy-picked-up.md)

