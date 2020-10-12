---
title: Rozwiązanie VMware platformy Azure — migracja przy użyciu Azure Data Box
description: Jak używać Azure Data Box do zbiorczego migrowania danych do rozwiązania Azure VMware.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "86507669"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrowanie danych do rozwiązania VMware platformy Azure przy użyciu Azure Data Box

Rozwiązanie Microsoft Azure urządzenie Data Box w chmurze umożliwia szybkie, niedrogie i niezawodne wysyłanie terabajtów (TBs) danych do platformy Azure. Bezpieczny transfer danych jest przyspieszany przez wysłanie do Ciebie należącego do firmy Microsoft urządzenia magazynującego Data Box. Każde urządzenie magazynujące ma maksymalną przydatną pojemność magazynu wynoszącą 80 TB i jest transportowana do centrum danych przez przewoźnika regionalnego. Urządzenie ma odporność na ochronę i zabezpieczanie danych podczas przesyłania.

Korzystając z urządzenie Data Box, można zbiorczo migrować dane programu VMware do chmury prywatnej. Dane z lokalnego środowiska VMware vSphere są kopiowane do urządzenie Data Box za pośrednictwem protokołu sieciowego systemu plików (NFS). Migracja danych zbiorczych polega na zapisywaniu kopii maszyn wirtualnych, konfiguracji i skojarzonych danych w danym momencie do urządzenie Data Box, a następnie ich ręcznego dostarczania na platformę Azure.

W tym artykule omówiono następujące informacje:

* Konfigurowanie urządzenie Data Box.
* Kopiowanie danych z lokalnego środowiska VMware do urządzenie Data Box za pomocą systemu plików NFS.
* Przygotowywanie do powrotu urządzenie Data Box.
* Przygotowywanie danych obiektów BLOB do kopiowania do rozwiązania VMware platformy Azure.
* Kopiowanie danych z platformy Azure do chmury prywatnej.

## <a name="scenarios"></a>Scenariusze

Aby przeprowadzić migrację danych zbiorczych, użyj urządzenie Data Box w następujących scenariuszach:

* Migrowanie dużej ilości danych z rozwiązań lokalnych do platformy Azure VMware. Ta metoda ustala linię bazową i synchronizuje różnice w sieci.
* Aby migrować dużą liczbę maszyn wirtualnych, które są wyłączone (zimne maszyny wirtualne).
* Aby przeprowadzić migrację danych maszyny wirtualnej w celu skonfigurowania środowisk deweloperskich i testowych.
* Migracja dużej liczby szablonów maszyn wirtualnych, plików ISO i dysków maszyny wirtualnej.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

* Sprawdź wymagania wstępne i [kolejność urządzenie Data Box](../databox/data-box-deploy-ordered.md) przy użyciu Azure Portal. W trakcie procesu zamówienia należy wybrać konto magazynu, które umożliwia korzystanie z magazynu obiektów BLOB. Po otrzymaniu urządzenia urządzenie Data Box Połącz je z siecią lokalną i [Skonfiguruj urządzenie](../databox/data-box-deploy-set-up.md) przy użyciu adresu IP, który jest dostępny z sieci zarządzania vSphere.

* Utwórz sieć wirtualną i konto magazynu w tym samym regionie, w którym zainicjowano rozwiązanie VMware platformy Azure.

* Utwórz [połączenie sieci wirtualnej platformy Azure](cloudsimple-azure-network-connection.md) z chmury prywatnej do sieci wirtualnej, w której utworzono konto magazynu, wykonując czynności opisane w temacie [łączenie usługi Azure Virtual Network do CloudSimple przy użyciu ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Konfigurowanie urządzenie Data Box dla systemu plików NFS

Połącz się z urządzenie Data Box lokalnym interfejsem użytkownika sieci Web, wykonując kroki opisane w sekcji "łączenie z urządzeniem" w [samouczku: kabel i Połącz się z Azure Data Box](../databox/data-box-deploy-set-up.md).  Skonfiguruj urządzenie Data Box tak, aby zezwalała na dostęp do klientów NFS:

1. W lokalnym interfejsie użytkownika sieci Web przejdź do strony **łączenie i kopiowanie** . W obszarze **Ustawienia systemu plików NFS**wybierz pozycję **dostęp z klienta systemu plików NFS**. 

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 1](media/nfs-client-access.png)

2. Wprowadź adres IP VMware ESXi hostów i wybierz pozycję **Dodaj**. Można skonfigurować dostęp dla wszystkich hostów w klastrze vSphere, powtarzając ten krok. Kliknij przycisk **OK**.

    ![Konfigurowanie dostępu klienta do sieciowego systemu plików 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Zawsze należy utworzyć w udziale folder na pliki, które chcesz skopiować, a następnie skopiować pliki do tego folderu**. Folder utworzony w ramach udziałów blokowych obiektów blob i stronicowych obiektów blob reprezentuje kontener, do którego dane są przekazywane w postaci obiektów blob. Nie można kopiować plików bezpośrednio do folderu *głównego* na koncie magazynu.

W obszarze udziałów blokowych obiektów blob i stronicowych obiektów blob jednostki pierwszego poziomu są kontenerami, a jednostki drugiego poziomu są obiektami blob. W obszarze udziały dla Azure Files jednostki pierwszego poziomu są udziałami, a jednostki drugiego poziomu są plikami.

W poniższej tabeli przedstawiono ścieżkę UNC do udziałów na urządzeniu Data Box i adres URL ścieżki w usłudze Azure Storage, pod który przekazywane są dane. Ostateczny adres URL w usłudze Azure Storage można uzyskać ze ścieżki udziału UNC.
 
| Obiekty blob i pliki | Ścieżka i adres URL |
|---------------- | ------------ |
| Blokowe obiekty blob platformy Azure | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Stronicowe obiekty blob platformy Azure  | <li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Ścieżka UNC do udziałów: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adres URL w usłudze Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> Użyj blokowych obiektów blob platformy Azure do kopiowania danych programu VMware.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Zainstaluj udział NFS jako magazyn danych w lokalnym klastrze vCenter i skopiuj dane

Udział w systemie plików NFS z urządzenie Data Box musi być zainstalowany jako magazyn danych na lokalnym klastrze vCenter lub na hoście VMware ESXi, aby można było skopiować dane do magazynu danych systemu plików NFS:

1. Zaloguj się do lokalnego serwera vCenter.

2. Kliknij prawym przyciskiem myszy pozycję **Datacenter**, wybierz pozycję Magazyn, wybierz pozycję **nowy magazyn** **danych**, a następnie wybierz pozycję **dalej**.

   ![Dodaj nowy magazyn danych](media/databox-migration-add-datastore.png)

3. W kroku 1 Kreatora dodawania magazynu danych wybierz pozycję System **plików NFS** w obszarze **Typ**.

   ![Dodawanie nowego magazynu danych — typ](media/databox-migration-add-datastore-type.png)

4. W kroku 2 kreatora wybierz opcję System **plików NFS 3** jako wersję systemu plików NFS, a następnie wybierz przycisk **dalej**.

   ![Dodawanie nowego magazynu danych — wersja systemu plików NFS](media/databox-migration-add-datastore-nfs-version.png)

5. W kroku 3 kreatora określ nazwę magazynu danych, ścieżkę i serwer. Można użyć adresu IP urządzenie Data Box serwera programu. Ścieżka folderu będzie w `/<StorageAccountName_BlockBlob>/<ContainerName>/` formacie.

   ![Dodawanie nowego magazynu danych — konfiguracja systemu plików NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. W kroku 4 kreatora wybierz hosty ESXi, w których chcesz zainstalować magazyn danych, a następnie wybierz pozycję **dalej**.  W klastrze wybierz wszystkie hosty, aby zapewnić migrację maszyn wirtualnych.

   ![Dodawanie nowego magazynu danych — wybierz hosty](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. W kroku 5 kreatora przejrzyj podsumowanie i wybierz pozycję **Zakończ**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Kopiuj dane do magazynu danych systemu urządzenie Data Box NFS

Maszyny wirtualne można migrować lub sklonować do nowego magazynu danych.  Wszystkie nieużywane maszyny wirtualne, które mają zostać poddane migracji, można migrować do magazynu danych systemu plików NFS urządzenie Data Box przy użyciu opcji **Storage vMotion** . Aktywne maszyny wirtualne można sklonować do magazynu danych systemu urządzenie Data Box NFS.

* Zidentyfikuj i wystaw maszyny wirtualne, które mogą być **przenoszone**.
* Zidentyfikuj i wystaw maszyny wirtualne, które muszą zostać **sklonowane**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Przenoszenie maszyny wirtualnej do urządzenie Data Box magazynu danych

1. Kliknij prawym przyciskiem myszy maszynę wirtualną, którą chcesz przenieść do magazynu danych urządzenie Data Box, a następnie wybierz pozycję **Migruj**.

    ![Migruj maszynę wirtualną](media/databox-migration-vm-migrate.png)

2. Wybierz pozycję **Zmień magazyn tylko** dla typu migracji, a następnie wybierz przycisk **dalej**.

    ![Migruj tylko magazyn maszyny wirtualnej](media/databox-migration-vm-migrate-change-storage.png)

3. Wybierz pozycję **DATAbox — magazyn** danych jako lokalizację docelową, a następnie wybierz pozycję **dalej**.

    ![Migrowanie maszyny wirtualnej — Wybieranie magazynu danych](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Przejrzyj informacje i wybierz pozycję **Zakończ**.

5. Powtórz kroki od 1 do 4 w przypadku dodatkowych maszyn wirtualnych.

> [!TIP]
> Można wybrać wiele maszyn wirtualnych, które są w tym samym stanie (włączone lub wyłączone) i przeprowadzić migrację zbiorczą.

Maszyna wirtualna zostanie zmigrowana do magazynu danych systemu plików NFS z urządzenie Data Box. Po przeprowadzeniu migracji wszystkich maszyn wirtualnych można wyłączyć (wyłączyć) aktywne maszyny wirtualne w ramach przygotowania do migracji danych do rozwiązania VMware platformy Azure.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Klonowanie maszyny wirtualnej lub szablonu maszyny wirtualnej do urządzenie Data Box magazynu danych

1. Kliknij prawym przyciskiem myszy maszynę wirtualną lub szablon maszyny wirtualnej, który ma zostać sklonowany. Wybierz **klon**klonowania  >  **do maszyny wirtualnej**.

    ![Klonowanie maszyny wirtualnej](media/databox-migration-vm-clone.png)

2. Wybierz nazwę sklonowanej maszyny wirtualnej lub szablonu maszyny wirtualnej.

3. Wybierz folder, w którym chcesz umieścić Sklonowany obiekt, a następnie wybierz przycisk **dalej**.

4. Wybierz klaster lub pulę zasobów, w której chcesz umieścić Sklonowany obiekt, a następnie wybierz przycisk **dalej**.

5. Wybierz pozycję **DATAbox — magazyn** danych jako lokalizację magazynu, a następnie wybierz pozycję **dalej**.

    ![Klonowanie maszyny wirtualnej — Wybieranie magazynu danych](media/databox-migration-vm-clone-select-datastore.png)

6. Aby dostosować opcje dla sklonowanego obiektu, wybierz opcje dostosowywania, a następnie wybierz przycisk **dalej**.

7. Przejrzyj konfiguracje i wybierz pozycję **Zakończ**.

8. Powtórz kroki od 1 do 7 w przypadku dodatkowych maszyn wirtualnych lub szablonów maszyn wirtualnych.

Maszyny wirtualne zostaną sklonowane i zapisane w magazynie danych systemu plików NFS z urządzenie Data Box. Po sklonowaniu maszyn wirtualnych upewnij się, że są one wyłączone podczas przygotowywania migracji danych do rozwiązania VMware platformy Azure.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Kopiuj pliki ISO do urządzenie Data Box magazynu danych

1. W lokalnym interfejsie użytkownika vCenter sieci Web, przejdź do obszaru **Magazyn**.  Wybierz pozycję **DATAbox — magazyn** danych, a następnie wybierz pozycję **pliki**. Utwórz nowy folder do przechowywania plików ISO.

    ![Kopiuj ISO — Utwórz nowy folder](media/databox-migration-create-folder.png)

2. Podaj nazwę folderu, w którym będą przechowywane pliki ISO.

3. Kliknij dwukrotnie nowo utworzony folder, aby go otworzyć.

4. Wybierz pozycję **Przekaż pliki** , a następnie wybierz pliki ISO, które chcesz przekazać.
    
    ![Kopiuj pliki z przekazywaniem ISO](media/databox-migration-upload-iso.png)

> [!TIP]
> Jeśli masz już pliki ISO w lokalnym magazynie danych, możesz wybrać pliki i **skopiować** je do programu w celu skopiowania plików do magazynu danych systemu plików NFS urządzenie Data Box.


## <a name="prepare-data-box-for-return"></a>Przygotuj urządzenie Data Box do zwrócenia

Po skopiowaniu wszystkich danych maszyny wirtualnej, danych szablonu maszyny wirtualnej i jakichkolwiek plików ISO do magazynu danych systemu urządzenie Data Box NFS można odłączyć magazyn danych od programu vCenter. Przed rozłączeniem magazynu danych wszystkie maszyny wirtualne i szablony maszyn wirtualnych muszą zostać usunięte ze spisu.

### <a name="remove-objects-from-inventory"></a>Usuwanie obiektów ze spisu

1. W lokalnym interfejsie użytkownika vCenter sieci Web, przejdź do obszaru **Magazyn**. Wybierz pozycję **DATAbox — magazyn** danych, a następnie wybierz pozycję **maszyny wirtualne**.

    ![Usuwanie maszyn wirtualnych z spisu — wyłączone](media/databox-migration-select-databox-vm.png)

2. Upewnij się, że wszystkie maszyny wirtualne są zamknięte.

3. Wybierz pozycję Wszystkie maszyny wirtualne, kliknij prawym przyciskiem myszy, a następnie wybierz pozycję **Usuń ze spisu**.

    ![Usuwanie maszyn wirtualnych ze spisu](media/databox-migration-remove-vm-from-inventory.png)

4. Wybierz pozycję **szablony maszyn wirtualnych w folderach** , a następnie powtórz krok 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Usuwanie urządzenie Data Box magazynu danych systemu plików NFS z programu vCenter

Magazyn danych systemu urządzenie Data Box NFS musi zostać odłączony od hostów VMware ESXi przed przygotowaniem do powrotu.

1. W lokalnym interfejsie użytkownika vCenter sieci Web, przejdź do obszaru **Magazyn**.

2. Kliknij prawym przyciskiem myszy pozycję **DATAbox-datastore** i wybierz pozycję **Odinstaluj magazyn**danych.

    ![Odinstalowanie urządzenie Data Box magazynu danych](media/databox-migration-unmount-datastore.png)

3. Zaznacz wszystkie hosty ESXi, w których jest zainstalowany magazyn danych, a następnie wybierz **przycisk OK**.

    ![Odinstalowywanie urządzenie Data Box magazynu danych — wybierz hosty](media/databox-migration-unmount-datastore-select-hosts.png)

4. Przejrzyj i Zaakceptuj wszystkie ostrzeżenia i wybierz **przycisk OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Przygotuj urządzenie Data Box do zwrócenia, a następnie zwróć ją

Wykonaj kroki opisane w artykule [zwróć Azure Data Box i Zweryfikuj przekazywanie danych do platformy Azure](../databox/data-box-deploy-picked-up.md) , aby zwrócić urządzenie Data Box. Sprawdź stan kopiowania danych na konto usługi Azure Storage. Gdy stan zostanie wyświetlony jako zakończony, możesz sprawdzić dane na koncie usługi Azure Storage.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Kopiowanie danych z usługi Azure Storage do rozwiązania VMware platformy Azure

Dane skopiowane na urządzenie urządzenie Data Box będą dostępne na koncie usługi Azure Storage, gdy stan zamówienia urządzenie Data Box zostanie wyświetlony jako zakończony. Dane można teraz skopiować do rozwiązania VMware platformy Azure. Dane na koncie magazynu muszą zostać skopiowane do magazynu danych sieci vSAN w chmurze prywatnej przy użyciu protokołu NFS. 

Najpierw skopiuj dane usługi BLOB Storage na dysk zarządzany na maszynie wirtualnej z systemem Linux na platformie Azure przy użyciu **AzCopy**. Udostępnij dysk zarządzany za pomocą systemu plików NFS, zainstaluj udział NFS jako magazyn danych w chmurze prywatnej, a następnie skopiuj dane. Ta metoda umożliwia szybszą kopię danych w chmurze prywatnej.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Skopiuj dane do chmury prywatnej za pomocą maszyny wirtualnej z systemem Linux i dysków zarządzanych, a następnie wyeksportuj jako udział NFS

1. Utwórz [maszynę wirtualną z systemem Linux](../virtual-machines/linux/quick-create-portal.md) na platformie Azure w tym samym regionie, w którym utworzono konto magazynu i ma połączenie z siecią wirtualną platformy Azure z chmurą prywatną.

2. Utwórz dysk zarządzany, którego pojemność magazynu jest większa niż ilość danych obiektów blob, a następnie [Dołącz je do maszyny wirtualnej z systemem Linux](../virtual-machines/linux/attach-disk-portal.md).  Jeśli ilość danych obiektów BLOB jest większa niż pojemność największego dostępnego dysku zarządzanego, dane muszą zostać skopiowane w wielu krokach lub przy użyciu wielu dysków zarządzanych.

3. Nawiąż połączenie z maszyną wirtualną z systemem Linux i zainstaluj dysk zarządzany.

4. Zainstaluj program [AzCopy na maszynie wirtualnej z systemem Linux](../storage/common/storage-use-azcopy-v10.md).

5. Pobierz dane z magazynu obiektów blob platformy Azure na dysk zarządzany przy użyciu AzCopy.  Składnia polecenia: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Zastąp ciąg `<storage-account-name>` nazwą konta usługi Azure Storage i `<container-name>` kontenerem zawierającym dane skopiowane za pomocą urządzenie Data Box.

6. Zainstaluj serwer NFS na maszynie wirtualnej z systemem Linux:

    - W dystrybucji Ubuntu/Debian: `sudo apt install nfs-kernel-server` .
    - W dystrybucji systemu Linux w przedsiębiorstwie: `sudo yum install nfs-utils` .

7. Zmień uprawnienia folderu na dysku zarządzanym, w którym skopiowano dane z usługi Azure Blob Storage.  Zmień uprawnienia dla wszystkich folderów, które mają zostać wyeksportowane jako udział w systemie plików NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Przypisz uprawnienia do adresów IP klientów, aby uzyskać dostęp do udziału NFS, edytując `/etc/exports` plik.

    ```bash
    sudo vi /etc/exports
    ```
    
    Wprowadź następujące wiersze w pliku dla każdego adresu IP hosta ESXi w chmurze prywatnej.  Jeśli tworzysz udziały dla wielu folderów, Dodaj wszystkie foldery.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Wyeksportuj udziały NFS przy użyciu `sudo exportfs -a` polecenia.

10. Uruchom ponownie serwer jądra systemu plików NFS przy użyciu `sudo systemctl restart nfs-kernel-server` polecenia.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Zainstaluj udział NFS maszyny wirtualnej z systemem Linux jako magazyn danych w prywatnym klastrze vCenter w chmurze, a następnie skopiuj dane

Udział w systemie plików NFS z maszyny wirtualnej z systemem Linux musi być zainstalowany jako magazyn danych w klastrze programu vCenter w chmurze prywatnej. Po zainstalowaniu programu dane można kopiować z magazynu danych systemu plików NFS do magazynu sieci vSAN danych w chmurze prywatnej.

1. Zaloguj się do swojego prywatnego serwera usługi Cloud vCenter.

2. Kliknij prawym przyciskiem myszy pozycję **Datacenter**, wybierz pozycję Magazyn, wybierz pozycję **nowy magazyn** **danych**, a następnie wybierz pozycję **dalej**.

   ![Dodaj nowy magazyn danych](media/databox-migration-add-datastore.png)

3. W kroku 1 Kreatora dodawania magazynu danych wybierz typ **systemu plików NFS** .

   ![Dodawanie nowego magazynu danych — typ](media/databox-migration-add-datastore-type.png)

4. W kroku 2 kreatora wybierz opcję System **plików NFS 3** jako wersję systemu plików NFS, a następnie wybierz przycisk **dalej**.

   ![Dodawanie nowego magazynu danych — wersja systemu plików NFS](media/databox-migration-add-datastore-nfs-version.png)

5. W kroku 3 kreatora określ nazwę magazynu danych, ścieżkę i serwer.  Na potrzeby serwera można użyć adresu IP maszyny wirtualnej z systemem Linux.  Ścieżka folderu będzie w `/<folder>/<subfolder>/` formacie.

   ![Dodawanie nowego magazynu danych — konfiguracja systemu plików NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. W kroku 4 kreatora wybierz hosty ESXi, w których chcesz zainstalować magazyn danych, a następnie wybierz pozycję **dalej**.  W klastrze wybierz wszystkie hosty, aby zapewnić migrację maszyn wirtualnych.

   ![Dodawanie nowego magazynu danych — wybierz hosty](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. W kroku 5 kreatora przejrzyj podsumowanie, a następnie wybierz pozycję **Zakończ**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Dodawanie maszyn wirtualnych i szablonów maszyn wirtualnych z magazynu danych systemu plików NFS do spisu

1. W prywatnym interfejsie użytkownika sieci Web vCenter w chmurze przejdź do obszaru **Magazyn**.  Wybierz magazyn danych NFS maszyny wirtualnej z systemem Linux, a następnie wybierz pozycję **pliki**.

    ![Wybieranie plików z magazynu danych NFS](media/databox-migration-datastore-select-files.png)

2. Wybierz folder zawierający maszynę wirtualną lub szablon maszyny wirtualnej.  W okienku szczegółów wybierz plik. vmx dla maszyny wirtualnej lub pliku. vmtx dla szablonu maszyny wirtualnej.

3. Wybierz pozycję **zarejestruj maszynę** wirtualną w celu zarejestrowania maszyny wirtualnej w programie vCenter w chmurze prywatnej.

    ![Zarejestruj maszynę wirtualną](media/databox-migration-datastore-register-vm.png)

4. Wybierz centrum danych, folder i klaster/pulę zasobów, w których maszyna wirtualna ma zostać zarejestrowana.

4. Powtórz kroki 3 i 4 dla wszystkich maszyn wirtualnych i szablonów maszyn wirtualnych.

5. Przejdź do folderu, który zawiera pliki ISO.  Wybierz pliki ISO, a następnie wybierz pozycję **Kopiuj do** , aby skopiować pliki do folderu w magazynie danych sieci vSAN.

Maszyny wirtualne i szablony maszyn wirtualnych są teraz dostępne w chmurze prywatnej programu vCenter. Te maszyny wirtualne należy przenieść z magazynu danych systemu plików NFS do magazynu danych sieci vSAN przed ich włączeniem. Można użyć opcji **vMotion magazynu** i wybrać magazyn danych sieci vSAN jako element docelowy dla maszyn wirtualnych.

Szablony maszyn wirtualnych muszą być sklonowane z magazynu danych systemu plików NFS maszyny wirtualnej z systemem Linux do magazynu danych sieci vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Wyczyść maszynę wirtualną z systemem Linux

Po skopiowaniu wszystkich danych do chmury prywatnej można usunąć magazyn danych systemu plików NFS z chmury prywatnej:

1. Upewnij się, że wszystkie maszyny wirtualne i szablony są przenoszone i sklonowane do magazynu danych sieci vSAN.

2. Usuń ze spisu wszystkie szablony maszyn wirtualnych z magazynu danych systemu plików NFS.

3. Odinstaluj magazyn danych maszyny wirtualnej systemu Linux z chmury prywatnej vCenter.

4. Usuń maszynę wirtualną i dysk zarządzany z platformy Azure.

5. Jeśli nie chcesz przechowywać danych transferowanych przez urządzenie Data Box na koncie magazynu, Usuń konto usługi Azure Storage.  
    


## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [urządzenie Data Box](../databox/data-box-overview.md).
* Dowiedz się więcej na temat różnych opcji [migrowania obciążeń do chmury prywatnej](migrate-workloads.md).
