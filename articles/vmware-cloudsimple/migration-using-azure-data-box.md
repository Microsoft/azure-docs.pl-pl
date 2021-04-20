---
title: Azure VMware Solution — migracja przy użyciu Azure Data Box
description: Jak używać Azure Data Box do zbiorczej migracji danych do Azure VMware Solution.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741936"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrowanie danych do usługi Azure VMware Solution przy użyciu Azure Data Box

Rozwiązanie Microsoft Azure urządzenie Data Box w chmurze umożliwia wysyłanie terabajtów danych na platformę Azure w szybki, niedrogi i niezawodny sposób. Bezpieczny transfer danych jest przyspieszany przez wysłanie do Ciebie należącego do firmy Microsoft urządzenia magazynującego Data Box. Każde urządzenie magazynujące ma maksymalną pojemność magazynu do wykorzystania 80 TB i jest transportowane do centrum danych przez regionalnego przewoźnika. Urządzenie ma nierówną obudowę, która chroni i zabezpiecza dane podczas przesyłania.

Korzystając z urządzenie Data Box, można przeprowadzić migrację zbiorczą danych programu VMware do chmury prywatnej. Dane z lokalnego środowiska VMware vSphere są kopiowane do usługi urządzenie Data Box za pośrednictwem protokołu sieciowego systemu plików (NFS). Migracja danych zbiorczych obejmuje zapisanie kopii maszyn wirtualnych, konfiguracji i skojarzonych danych w punkcie w czasie w celu urządzenie Data Box a następnie ręcznego wysyłki na platformę Azure.

Ten artykuł zawiera informacje na temat:

* Konfigurowanie urządzenie Data Box.
* Kopiowanie danych z lokalnego środowiska VMware do sieci urządzenie Data Box za pośrednictwem systemu plików NFS.
* Przygotowanie do powrotu urządzenie Data Box.
* Przygotowywanie danych obiektów blob do kopiowania do Azure VMware Solution.
* Kopiowanie danych z platformy Azure do chmury prywatnej.

## <a name="scenarios"></a>Scenariusze

Użyj urządzenie Data Box w następujących scenariuszach migracji danych zbiorczych:

* Migrowanie dużej ilości danych ze środowisk lokalnych do Azure VMware Solution. Ta metoda ustanawia punkt odniesienia i synchronizuje różnice w sieci.
* Migrowanie dużej liczby wyłączonych maszyn wirtualnych (zimnych maszyn wirtualnych).
* Migrowanie danych maszyny wirtualnej w celu skonfigurowania środowisk deweloperskich i testowych.
* Migrowanie dużej liczby szablonów maszyn wirtualnych, plików ISO i dysków maszyn wirtualnych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Sprawdź wymagania wstępne i [kolejność urządzenie Data Box](../databox/data-box-deploy-ordered.md) za pośrednictwem Azure Portal. Podczas procesu zamawiania należy wybrać konto magazynu, które włącza magazyn obiektów blob. Po otrzymaniu urządzenia urządzenie Data Box połącz je z siecią lokalną i skonfiguruj urządzenie przy użyciu adresu IP dostępnego z sieci zarządzania vSphere. [](../databox/data-box-deploy-set-up.md)

* Utwórz sieć wirtualną i konto magazynu w tym samym regionie, w którym Azure VMware Solution aprowizowana.

* Utwórz połączenie [sieci wirtualnej](cloudsimple-azure-network-connection.md) platformy Azure z chmury prywatnej do sieci wirtualnej, w której tworzone jest konto magazynu, zgodnie z instrukcjami w tesłudze [Connect Azure virtual network to CloudSimple using ExpressRoute](virtual-network-connection.md)(Łączenie sieci wirtualnej platformy Azure z usługą CloudSimple przy użyciu usługi ExpressRoute).

## <a name="set-up-data-box-for-nfs"></a>Konfigurowanie systemu urządzenie Data Box dla systemu plików NFS

Połącz się z lokalnym internetowym interfejsem użytkownika urządzenie Data Box, korzystając z instrukcji w sekcji "Łączenie z urządzeniem" artykułu [Samouczek:](../databox/data-box-deploy-set-up.md)podłączanie kabli i nawiązywanie połączenia z urządzeniem Azure Data Box .  Skonfiguruj urządzenie Data Box, aby zezwolić na dostęp do klientów systemu plików NFS:

1. W lokalnym internetowym interfejsie użytkownika przejdź do strony **Łączenie i kopiowanie.** W **obszarze Ustawienia systemu plików NFS** wybierz pozycję Dostęp klienta **NFS.** 

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/nfs-client-access.png)

2. Wprowadź adres IP hostów VMware ESXi i wybierz pozycję **Dodaj**. Możesz skonfigurować dostęp dla wszystkich hostów w klastrze vSphere, powtarzając ten krok. Wybierz przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Nie można kopiować plików bezpośrednio do *folderu głównego* na koncie magazynu.

W obszarze udziałów blokowych obiektów blob i stronicowych obiektów blob jednostki pierwszego poziomu są kontenerami, a jednostki drugiego poziomu są obiektami blob. W obszarze udziałów Azure Files jednostki pierwszego poziomu są udziałami, a jednostki drugiego poziomu są plikami.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na urządzeniu Data Box i adres URL ścieżki w usłudze Azure Storage, pod który przekazywane są dane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
| Obiekty blob i pliki | Ścieżka i adres URL |
|---------------- | ------------ |
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Użyj blokowych obiektów blob platformy Azure do kopiowania danych VMware.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Zainstaluj udział NFS jako magazyn danych w lokalnym klastrze programu vCenter i skopiuj dane

Udział NFS z systemu plików urządzenie Data Box musi być zainstalowany jako magazyn danych na lokalnym klastrze programu vCenter lub hoście VMware ESXi, aby skopiować dane do magazynu danych systemu plików NFS:

1. Zaloguj się do lokalnego serwera vCenter.

2. Kliknij prawym przyciskiem myszy **pozycję Centrum danych,** wybierz pozycję **Magazyn,** wybierz **pozycję Nowy magazyn danych,** a następnie wybierz pozycję **Dalej.**

   ![Dodawanie nowego magazynu danych](media/databox-migration-add-datastore.png)

3. W kroku 1 Kreatora dodawania magazynu danych wybierz pozycję **NFS** w obszarze **Typ**.

   ![Dodawanie nowego magazynu danych — typ](media/databox-migration-add-datastore-type.png)

4. W kroku 2 kreatora wybierz **NFS 3** jako wersję systemu plików NFS, a następnie wybierz przycisk **Dalej.**

   ![Dodawanie nowego magazynu danych — wersja systemu plików NFS](media/databox-migration-add-datastore-nfs-version.png)

5. W kroku 3 kreatora określ nazwę magazynu danych, ścieżkę i serwer. Możesz użyć adresu IP serwera urządzenie Data Box serwera. Ścieżka folderu będzie w `/<StorageAccountName_BlockBlob>/<ContainerName>/` formacie .

   ![Dodawanie nowego magazynu danych — konfiguracja systemu plików NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. W kroku 4 kreatora wybierz hosty ESXi, na których ma zostać zainstalowany magazyn danych, a następnie wybierz przycisk **Dalej.**  W klastrze wybierz wszystkie hosty, aby zapewnić migrację maszyn wirtualnych.

   ![Dodawanie nowego magazynu danych — wybieranie hostów](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. W kroku 5 kreatora przejrzyj podsumowanie i wybierz pozycję **Zakończ.**

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Kopiowanie danych do magazynu urządzenie Data Box NFS

Maszyny wirtualne można migrować lub klonować do nowego magazynu danych.  Wszystkie nieużywane maszyny wirtualne, które chcesz zmigrować, można zmigrować do magazynu urządzenie Data Box NFS przy użyciu opcji **storage vMotion.** Aktywne maszyny wirtualne można sklonować do magazynu urządzenie Data Box NFS.

* Zidentyfikuj i wymień maszyny wirtualne, które można **przenosić.**
* Zidentyfikuj i wymień maszyny wirtualne, które muszą **zostać sklonowane.**

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Przenoszenie maszyny wirtualnej do magazynu urządzenie Data Box danych

1. Kliknij prawym przyciskiem myszy maszynę wirtualną, którą chcesz przenieść do magazynu urządzenie Data Box, a następnie wybierz polecenie **Migruj.**

    ![Migrowanie maszyny wirtualnej](media/databox-migration-vm-migrate.png)

2. Wybierz **pozycję Zmień magazyn tylko** dla typu migracji, a następnie wybierz pozycję **Dalej.**

    ![Migrowanie maszyny wirtualnej — tylko magazyn](media/databox-migration-vm-migrate-change-storage.png)

3. Wybierz **pozycję Databox-Datastore** jako miejsce docelowe, a następnie wybierz pozycję **Dalej.**

    ![Migrowanie maszyny wirtualnej — wybieranie magazynu danych](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Przejrzyj informacje i wybierz pozycję **Zakończ.**

5. Powtórz kroki od 1 do 4 dla dodatkowych maszyn wirtualnych.

> [!TIP]
> Możesz wybrać wiele maszyn wirtualnych w tym samym stanie zasilania (włączonych lub wyłączonych) i przeprowadzić ich migrację zbiorczą.

Maszyna wirtualna zostanie zmigrowana do magazynu danych systemu plików NFS z urządzenie Data Box. Po migracji wszystkich maszyn wirtualnych można wyłączyć (wyłączyć) aktywne maszyny wirtualne w ramach przygotowania do migracji danych do Azure VMware Solution.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Klonowanie maszyny wirtualnej lub szablonu maszyny wirtualnej do magazynu urządzenie Data Box danych

1. Kliknij prawym przyciskiem myszy maszynę wirtualną lub szablon maszyny wirtualnej, którą chcesz sklonować. Wybierz **pozycję Clone** Clone to Virtual Machine  >  **(Sklonuj klonowanie do maszyny wirtualnej).**

    ![Klonowanie maszyny wirtualnej](media/databox-migration-vm-clone.png)

2. Wybierz nazwę sklonowanej maszyny wirtualnej lub szablonu maszyny wirtualnej.

3. Wybierz folder, w którym chcesz umieścić sklonowany obiekt, a następnie wybierz pozycję **Dalej.**

4. Wybierz klaster lub pulę zasobów, w której chcesz umieścić sklonowany obiekt, a następnie wybierz pozycję **Dalej.**

5. Wybierz **pozycję Databox-Datastore** jako lokalizację magazynu, a następnie wybierz pozycję **Dalej.**

    ![Klonowanie maszyny wirtualnej — wybieranie magazynu danych](media/databox-migration-vm-clone-select-datastore.png)

6. Jeśli chcesz dostosować jakiekolwiek opcje dla sklonowanego obiektu, wybierz opcje dostosowywania, a następnie wybierz pozycję **Dalej.**

7. Przejrzyj konfiguracje i wybierz pozycję **Zakończ.**

8. Powtórz kroki od 1 do 7 dla dodatkowych maszyn wirtualnych lub szablonów maszyn wirtualnych.

Maszyny wirtualne zostaną sklonowane i zapisane w magazynze danych systemu plików NFS z urządzenie Data Box. Po sklonowanych maszynach wirtualnych upewnij się, że zostały one zamknięte w ramach przygotowania do migracji danych do Azure VMware Solution.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Kopiowanie plików ISO do urządzenie Data Box danych

1. Z lokalnego internetowego interfejsu użytkownika programu vCenter przejdź do **magazynu**.  Wybierz **pozycję Databox-Datastore,** a następnie wybierz **pozycję Pliki.** Utwórz nowy folder do przechowywania plików ISO.

    ![Kopiowanie obrazu ISO — tworzenie nowego folderu](media/databox-migration-create-folder.png)

2. Podaj nazwę folderu, w którym będą przechowywane pliki ISO.

3. Kliknij dwukrotnie nowo utworzony folder, aby go otworzyć.

4. Wybierz **pozycję Przekaż pliki,** a następnie wybierz pliki ISO, które chcesz przekazać.
    
    ![Kopiowanie obrazu ISO — przekazywanie plików](media/databox-migration-upload-iso.png)

> [!TIP]
> Jeśli masz już pliki ISO w lokalnym magazynze danych,  możesz wybrać pliki i skopiować do, aby skopiować pliki do magazynu urządzenie Data Box NFS.


## <a name="prepare-data-box-for-return"></a>Przygotowywanie urządzenie Data Box do zwrotu

Po skopiowaniu wszystkich danych maszyny wirtualnej, danych szablonu maszyny wirtualnej i wszelkich plików ISO do magazynu danych systemu plików NFS systemu plików urządzenie Data Box można odłączyć magazyn danych od programu vCenter. Przed rozłączeniem magazynu danych należy usunąć wszystkie maszyny wirtualne i szablony maszyn wirtualnych ze spisu.

### <a name="remove-objects-from-inventory"></a>Usuwanie obiektów ze spisu

1. Z lokalnego internetowego interfejsu użytkownika programu vCenter przejdź do **magazynu**. Wybierz **pozycję Databox-Datastore,** a następnie **wybierz pozycję Maszyny wirtualne.**

    ![Usuwanie maszyn wirtualnych ze spisu — wyłączone](media/databox-migration-select-databox-vm.png)

2. Upewnij się, że wszystkie maszyny wirtualne zostały zamknięte.

3. Zaznacz wszystkie maszyny wirtualne, kliknij prawym przyciskiem myszy, a następnie wybierz **polecenie Usuń ze spisu**.

    ![Usuwanie maszyn wirtualnych ze spisu](media/databox-migration-remove-vm-from-inventory.png)

4. Wybierz **pozycję Szablony maszyn wirtualnych w folderach,** a następnie powtórz krok 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Usuwanie magazynu urządzenie Data Box NFS z programu vCenter

Magazyn urządzenie Data Box NFS musi zostać odłączony od VMware ESXi przed przygotowaniem do powrotu.

1. Z lokalnego internetowego interfejsu użytkownika programu vCenter przejdź do **magazynu**.

2. Kliknij prawym przyciskiem **myszy pozycję Databox-Datastore i** wybierz polecenie **Odinstaluj magazyn danych.**

    ![Odinstaluj urządzenie Data Box magazynu danych](media/databox-migration-unmount-datastore.png)

3. Wybierz wszystkie hosty ESXi, na których jest zainstalowany magazyn danych, a następnie wybierz przycisk **OK.**

    ![Odinstaluj urządzenie Data Box magazynu danych — wybierz hosty](media/databox-migration-unmount-datastore-select-hosts.png)

4. Przejrzyj i zaakceptuj wszelkie ostrzeżenia, a następnie wybierz przycisk **OK.**

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Przygotowanie urządzenie Data Box do zwrotu, a następnie zwrócenie go

Wykonaj kroki opisane w artykule Return Azure Data Box and verify data upload to Azure (Zwracanie danych i weryfikowanie przekazania danych na platformę [Azure),](../databox/data-box-deploy-picked-up.md) aby urządzenie Data Box. Sprawdź stan kopiowania danych na konto usługi Azure Storage. Gdy stan będzie mieć stan ukończony, możesz zweryfikować dane na koncie usługi Azure Storage.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Kopiowanie danych z usługi Azure Storage do Azure VMware Solution

Dane skopiowane na urządzenie urządzenie Data Box będą dostępne na koncie usługi Azure Storage po tym, jak stan zamówienia urządzenia urządzenie Data Box jako ukończony. Dane można teraz skopiować do Azure VMware Solution. Dane na koncie magazynu muszą zostać skopiowane do magazynu danych sieci vSAN chmury prywatnej przy użyciu protokołu NFS. 

Najpierw skopiuj dane usługi Blob Storage na dysk zarządzany na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu **narzędzia AzCopy.** Udostępnij dysk zarządzany za pośrednictwem systemu plików NFS, zainstaluj udział NFS jako magazyn danych w chmurze prywatnej, a następnie skopiuj dane. Ta metoda umożliwia szybsze kopiowanie danych do chmury prywatnej.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Kopiowanie danych do chmury prywatnej przy użyciu maszyny wirtualnej z systemem Linux i dysków zarządzanych, a następnie eksportowanie jako udziału NFS

1. Utwórz [maszynę wirtualną z](../virtual-machines/linux/quick-create-portal.md) systemem Linux na platformie Azure w tym samym regionie, w którym utworzono konto magazynu i który ma połączenie sieci wirtualnej platformy Azure z chmurą prywatną.

2. Utwórz dysk zarządzany, którego pojemność magazynu jest większa niż ilość danych obiektów blob, i dołącz go do maszyny [wirtualnej z systemem Linux.](../virtual-machines/linux/attach-disk-portal.md)  Jeśli ilość danych obiektów blob jest większa niż pojemność największego dostępnego dysku zarządzanego, dane muszą zostać skopiowane w wielu krokach lub przy użyciu wielu dysków zarządzanych.

3. Połącz się z maszyną wirtualną z systemem Linux i zainstaluj dysk zarządzany.

4. Zainstaluj [program AzCopy na maszynie wirtualnej z systemem Linux.](../storage/common/storage-use-azcopy-v10.md)

5. Pobierz dane z usługi Azure Blob Storage na dysk zarządzany przy użyciu narzędzia AzCopy.  Składnia polecenia: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Zastąp `<storage-account-name>` nazwą konta usługi Azure Storage i `<container-name>` kontenerem, który przechowuje dane skopiowane za pośrednictwem urządzenie Data Box.

6. Zainstaluj serwer NFS na maszynie wirtualnej z systemem Linux:

    - W dystrybucji Ubuntu/Debian: `sudo apt install nfs-kernel-server` .
    - W dystrybucji systemu Linux w przedsiębiorstwie: `sudo yum install nfs-utils` .

7. Zmień uprawnienie folderu na dysku zarządzanym, do którego skopiowano dane z usługi Azure Blob Storage.  Zmień uprawnienia dla wszystkich folderów, które chcesz wyeksportować jako udział NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Przypisz uprawnienia dla adresów IP klientów, aby uzyskać dostęp do udziału NFS, edytując `/etc/exports` plik.

    ```bash
    sudo vi /etc/exports
    ```
    
    Wprowadź następujące wiersze w pliku dla każdego adresu IP hosta ESXi w chmurze prywatnej.  Jeśli tworzysz udziały dla wielu folderów, dodaj wszystkie foldery.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Wyeksportuj udziały NFS za pomocą `sudo exportfs -a` polecenia .

10. Uruchom ponownie serwer jądra NFS za pomocą `sudo systemctl restart nfs-kernel-server` polecenia .


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Zainstaluj udział systemu plików NFS maszyny wirtualnej z systemem Linux jako magazyn danych w klastrze vCenter chmury prywatnej, a następnie skopiuj dane

Udział NFS z maszyny wirtualnej z systemem Linux musi być zainstalowany jako magazyn danych w klastrze vCenter chmury prywatnej. Po jego zakończeniu dane można skopiować z magazynu danych systemu plików NFS do magazynu danych sieci vSAN w chmurze prywatnej.

1. Zaloguj się do serwera vCenter w chmurze prywatnej.

2. Kliknij prawym przyciskiem myszy **pozycję Centrum danych,** wybierz pozycję **Magazyn,** wybierz **pozycję Nowy magazyn danych,** a następnie wybierz pozycję **Dalej.**

   ![Dodawanie nowego magazynu danych](media/databox-migration-add-datastore.png)

3. W kroku 1 Kreatora dodawania magazynu danych wybierz typ **NFS.**

   ![Dodawanie nowego magazynu danych — typ](media/databox-migration-add-datastore-type.png)

4. W kroku 2 kreatora wybierz **NFS 3** jako wersję systemu plików NFS, a następnie wybierz przycisk **Dalej.**

   ![Dodawanie nowego magazynu danych — wersja systemu plików NFS](media/databox-migration-add-datastore-nfs-version.png)

5. W kroku 3 kreatora określ nazwę magazynu danych, ścieżkę i serwer.  Możesz użyć adresu IP maszyny wirtualnej z systemem Linux dla serwera.  Ścieżka folderu będzie w `/<folder>/<subfolder>/` formacie .

   ![Dodawanie nowego magazynu danych — konfiguracja systemu plików NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. W kroku 4 kreatora wybierz hosty ESXi, na których ma zostać zainstalowany magazyn danych, a następnie wybierz przycisk **Dalej.**  W klastrze wybierz wszystkie hosty, aby zapewnić migrację maszyn wirtualnych.

   ![Dodawanie nowego magazynu danych — wybieranie hostów](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. W kroku 5 kreatora przejrzyj podsumowanie, a następnie wybierz pozycję **Zakończ.**

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Dodawanie maszyn wirtualnych i szablonów maszyn wirtualnych z magazynu danych systemu plików NFS do spisu

1. Z internetowego interfejsu użytkownika programu vCenter w chmurze prywatnej przejdź **do** magazynu .  Wybierz magazyn danych systemu plików NFS maszyny wirtualnej z systemem Linux, a następnie wybierz pozycję **Pliki.**

    ![Wybieranie plików z magazynu danych systemu plików NFS](media/databox-migration-datastore-select-files.png)

2. Wybierz folder zawierający maszynę wirtualną lub szablon maszyny wirtualnej.  W okienku szczegółów wybierz plik vmx dla maszyny wirtualnej lub plik vmtx szablonu maszyny wirtualnej.

3. Wybierz **pozycję Zarejestruj maszynę wirtualną,** aby zarejestrować maszynę wirtualną w programie vCenter w chmurze prywatnej.

    ![Rejestrowanie maszyny wirtualnej](media/databox-migration-datastore-register-vm.png)

4. Wybierz centrum danych, folder i klaster/pulę zasobów, w których ma zostać zarejestrowana maszyna wirtualna.

4. Powtórz kroki 3 i 4 dla wszystkich maszyn wirtualnych i szablonów maszyn wirtualnych.

5. Przejdź do folderu zawierającego pliki ISO.  Wybierz pliki ISO, a następnie wybierz pozycję **Kopiuj do,** aby skopiować pliki do folderu w twoim magazynze danych sieci vSAN.

Maszyny wirtualne i szablony maszyn wirtualnych są teraz dostępne w chmurze prywatnej vCenter. Te maszyny wirtualne należy przenieść z magazynu danych systemu plików NFS do magazynu danych sieci vSAN przed ich włączeniem. Możesz użyć opcji **storage vMotion** i wybrać magazyn danych vSAN jako obiekt docelowy dla maszyn wirtualnych.

Szablony maszyn wirtualnych muszą zostać sklonowane z magazynu danych systemu plików NFS maszyny wirtualnej z systemem Linux do magazynu danych sieci vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Czyszczenie maszyny wirtualnej z systemem Linux

Po skopiowaniu wszystkich danych do chmury prywatnej można usunąć magazyn danych NFS z chmury prywatnej:

1. Upewnij się, że wszystkie maszyny wirtualne i szablony zostały przeniesione i sklonowane do magazynu danych sieci vSAN.

2. Usuń ze spisu wszystkie szablony maszyn wirtualnych z magazynu danych systemu plików NFS.

3. Odinstaluj magazyn danych maszyny wirtualnej z systemem Linux z chmury prywatnej vCenter.

4. Usuń maszynę wirtualną i dysk zarządzany z platformy Azure.

5. Jeśli nie chcesz przechowywać danych, które zostały przeniesione przez usługę urządzenie Data Box na koncie magazynu, usuń konto usługi Azure Storage.  
    


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [urządzenie Data Box](../databox/data-box-overview.md).
* Dowiedz się więcej o różnych opcjach [migracji obciążeń do chmury prywatnej.](migrate-workloads.md)
