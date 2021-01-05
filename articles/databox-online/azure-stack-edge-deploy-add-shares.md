---
title: Samouczek dotyczący transferu danych do udziałów przy użyciu Azure Stack EDGE Pro | Microsoft Docs
description: W tym samouczku dowiesz się, jak dodawać udziały i łączyć się z nimi na urządzeniu z systemem Azure Stack EDGE Pro, dzięki czemu Azure Stack EDGE Pro może przesyłać dane na platformę Azure.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: tutorial
ms.date: 01/04/2021
ms.author: alkohli
Customer intent: As an IT admin, I need to understand how to add and connect to shares on Azure Stack Edge Pro so I can use it to transfer data to Azure.
ms.openlocfilehash: afedbe739e955499f94d00a8ced940e7a4e9c27c
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97898689"
---
# <a name="tutorial-transfer-data-with-azure-stack-edge-pro"></a>Samouczek: przesyłanie danych przy użyciu Azure Stack EDGE Pro

W tym samouczku opisano, jak dodawać i łączyć się z udziałami na urządzeniu Azure Stack EDGE Pro. Po dodaniu udziałów Azure Stack EDGE Pro może przesyłać dane na platformę Azure.

Wykonanie tej procedury może zająć około 10 minut.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie z udziałem

 
## <a name="prerequisites"></a>Wymagania wstępne

Przed dodaniem udziałów do Azure Stack EDGE Pro upewnij się, że:

- Urządzenie fizyczne zostało zainstalowane zgodnie z opisem w artykule [instalowanie Azure Stack EDGE Pro](azure-stack-edge-deploy-install.md).

- Urządzenie fizyczne zostało aktywowane zgodnie z opisem w artykule [łączenie, Konfigurowanie i aktywowanie Azure Stack EDGE Pro](azure-stack-edge-deploy-connect-setup-activate.md).


## <a name="add-a-share"></a>Dodawanie udziału

Aby utworzyć udział, wykonaj następującą procedurę:

1. W [Azure Portal](https://portal.azure.com/)wybierz Azure Stack zasobów brzegowych, a następnie przejdź do **omówienia**. Urządzenie powinno być w trybie online. Wybierz pozycję **brama magazynu w chmurze**.

   ![Urządzenie w trybie online](./media/azure-stack-edge-deploy-add-shares/device-online-1.png)

2. Wybierz pozycję **+ Dodaj udział** na pasku poleceń urządzenia.

   ![Dodawanie udziału](./media/azure-stack-edge-deploy-add-shares/select-add-share-1.png)

3. W okienku **Dodawanie udziału** wykonaj następującą procedurę:

    a. W polu **Nazwa** podaj unikatową nazwę udziału.  
    Nazwa udziału może zawierać tylko małe litery, cyfry i łączniki. Musi mieć od 3 do 63 znaków i zaczynać się literą lub cyfrą. Za i przed łącznikiem musi znajdować się litera lub cyfra.
    
    b. Wybierz **Typ** udziału.  
    Typ może być typu **SMB** lub **NFS** z domyślnym protokołem SMB. Typ SMB jest standardem dla klientów systemu Windows, natomiast typ NFS jest używany dla klientów systemu Linux.  
    W zależności od tego, czy wybrano udział SMB, czy NFS, pozostałe opcje nieco się różnią. 

    c. Podaj konto magazynu, na którym zostanie umieszczony udział. 

    > [!IMPORTANT]
    > Upewnij się, że konto usługi Azure Storage, którego używasz, nie ma ustawionych zasad niezmiennościymi, jeśli są używane z urządzeniem Azure Stack EDGE Pro lub Data Box Gateway. Aby uzyskać więcej informacji, zobacz [Ustawianie zasad niezmienności dla usługi BLOB Storage i zarządzanie nimi](../storage/blobs/storage-blob-immutability-policies-manage.md).
    
    d. Na liście rozwijanej **Usługa magazynu** wybierz pozycję **Blokowy obiekt blob**, **Stronicowy obiekt blob** lub **Pliki**.  
    Wybrany typ usługi zależy od tego, jaki format mają mieć dane do użycia na platformie Azure. W tym przykładzie, ponieważ chcemy przechowywać dane jako blokowe obiekty blob na platformie Azure, wybieramy **blokowy obiekt BLOB**. W przypadku wybrania opcji **stronicowego obiektu BLOB** upewnij się, że dane są wyrównane do 512 bajtów. Na przykład dysk VHDX jest zawsze wyrównany o 512 bajtów.

    e. Utwórz nowy kontener obiektów blob lub Użyj istniejącego z listy rozwijanej. W przypadku tworzenia kontenera obiektów BLOB Podaj nazwę kontenera. Jeśli kontener jeszcze nie istnieje, zostanie on utworzony z nowo utworzoną nazwą udziału na koncie magazynu.
   
    f. W zależności od tego, czy utworzono udział SMB, czy udział NFS, wykonaj jeden z następujących kroków: 
     
    - **Udział SMB**: w obszarze **wszystkie uprawnienia użytkownika lokalnego** wybierz pozycję **Utwórz nowy** lub **Użyj istniejącej**. Jeśli tworzysz nowego użytkownika lokalnego, wprowadź nazwę użytkownika i hasło, a następnie potwierdź hasło. Ta akcja spowoduje przypisanie uprawnień do użytkownika lokalnego. Po przypisaniu uprawnień w tym miejscu możesz modyfikować je przy użyciu Eksploratora plików.

        W przypadku zaznaczenia pola wyboru **Zezwalaj tylko na operacje odczytu** dla danych tego udziału można określić użytkowników tylko do odczytu.

        ![Dodawanie udziału SMB](./media/azure-stack-edge-deploy-add-shares/add-share-smb-1.png)
   
    - **Udział NFS**: wprowadź adresy IP dozwolonych klientów, którzy mogą uzyskiwać dostęp do udziału.

        ![Dodawanie udziału NFS](./media/azure-stack-edge-deploy-add-shares/add-share-nfs-1.png)
   
4. Wybierz pozycję **Utwórz**, aby utworzyć udział.
    
    Zostanie wyświetlone powiadomienie, że trwa tworzenie udziału. Po utworzeniu udziału przy użyciu określonych ustawień kafelek **udziały** aktualizuje się w celu odzwierciedlenia nowego udziału.
    

## <a name="connect-to-the-share"></a>Łączenie z udziałem

Teraz możesz połączyć się z jednym lub kilkoma udziałami utworzonymi w ostatnim kroku. W zależności od tego, czy masz udział SMB, czy NFS, kroki mogą się różnić.

### <a name="connect-to-an-smb-share"></a>Łączenie z udziałem SMB

Na kliencie z systemem Windows Server połączonym z urządzeniem Azure Stack EDGE Pro Połącz się z udziałem SMB, wprowadzając następujące polecenia:


1. W oknie polecenia wpisz:

    `net use \\<IP address of the device>\<share name>  /u:<user name for the share>`

2. Po wyświetleniu monitu wprowadź hasło dla udziału.  
   Poniżej przedstawiono przykładowe dane wyjściowe dla tego polecenia.

    ```powershell
    Microsoft Windows [Version 10.0.16299.192)
    (c) 2017 Microsoft Corporation. All rights reserved.
    
    C: \Users\DataBoxEdgeUser>net use \\10.10.10.60\newtestuser /u:Tota11yNewUser
    Enter the password for 'TotallyNewUser' to connect to '10.10.10.60':
    The command completed successfully.
    
    C: \Users\DataBoxEdgeUser>
    ```   


3. Na klawiaturze naciśnij klawisze Windows + R.

4. W oknie **uruchamiania** Określ wartość `\\<device IP address>` , a następnie wybierz przycisk **OK**.  
   Zostanie otwarty Eksplorator plików. Teraz utworzone przez Ciebie udziały powinny być widoczne jako foldery. W Eksploratorze plików kliknij dwukrotnie udział (folder) w celu wyświetlenia jego zawartości.
 
    ![Łączenie z udziałem SMB](./media/azure-stack-edge-deploy-add-shares/connect-to-share2.png)

    Dane są zapisywane w tych udziałach podczas ich generowania, a urządzenie wypycha dane do chmury.

### <a name="connect-to-an-nfs-share"></a>Łączenie z udziałem NFS

Na kliencie z systemem Linux połączonym z urządzeniem Azure Stack EDGE Pro wykonaj następującą procedurę:

1. Upewnij się, że na kliencie zainstalowano klienta NFSv4. Aby zainstalować klienta NFS, użyj następującego polecenia:

   `sudo apt-get install nfs-common`

    Aby uzyskać więcej informacji, przejdź do [Install NFSv4 Client](https://help.ubuntu.com/community/SettingUpNFSHowTo#NFSv4_client).

2. Po zainstalowaniu klienta NFS Zainstaluj udział NFS, który został utworzony na urządzeniu Azure Stack EDGE Pro, przy użyciu następującego polecenia:

   `sudo mount -t nfs -o sec=sys,resvport <device IP>:/<NFS shares on device> /home/username/<Folder on local Linux computer>`

    > [!IMPORTANT]
    > Użycie `sync` opcji przy instalowaniu udziałów zwiększa szybkość transferu dużych plików.
    > Zanim zainstalujesz udział, upewnij się, że katalogi, które będą pełnić rolę punktów instalacji na komputerze lokalnym, zostały już utworzone. Te katalogi nie powinny zawierać żadnych plików ani podfolderów.

    Poniższy przykład pokazuje, jak nawiązać połączenie za pomocą systemu plików NFS z udziałem na urządzeniu Azure Stack EDGE Pro. Adres IP urządzenia to `10.10.10.60`. Udział `mylinuxshare2` jest instalowany w systemie ubuntuVM. Punkt instalacji udziału to `/home/databoxubuntuhost/edge`.

    `sudo mount -t nfs -o sec=sys,resvport 10.10.10.60:/mylinuxshare2 /home/databoxubuntuhost/Edge`

> [!NOTE] 
> W tej wersji mają zastosowanie następujące zastrzeżenia:
> - Po utworzeniu pliku w udziale zmiana nazwy pliku nie jest obsługiwana. 
> - Usunięcie pliku z udziału nie powoduje usunięcia wpisu na koncie magazynu.

## <a name="next-steps"></a>Następne kroki

W tym samouczku omówiono następujące tematy Azure Stack EDGE Pro:

> [!div class="checklist"]
> * Dodawanie udziału
> * Łączenie się z udziałem

Aby dowiedzieć się, jak przekształcić dane przy użyciu Azure Stack EDGE Pro, przejdź do następnego samouczka:

> [!div class="nextstepaction"]
> [Przekształcanie danych za pomocą Azure Stack EDGE Pro](./azure-stack-edge-deploy-configure-compute.md)