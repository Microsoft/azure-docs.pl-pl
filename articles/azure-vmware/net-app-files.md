---
title: NetApp pliki dla rozwiązań VMware platformy Azure
description: Używaj Azure NetApp Files z maszynami wirtualnymi VMware platformy Azure do migrowania i synchronizowania danych między serwerami lokalnymi, maszynami wirtualnymi rozwiązań VMware platformy Azure i infrastrukturami chmury.
ms.topic: how-to
ms.date: 09/17/2020
ms.openlocfilehash: 993a67f82d5af971a4c4a0010bd9d19e2a3113b2
ms.sourcegitcommit: a422b86148cba668c7332e15480c5995ad72fa76
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/30/2020
ms.locfileid: "91573926"
---
# <a name="netapp-files-for-azure-vmware-solution"></a>NetApp pliki dla rozwiązań VMware platformy Azure

W tym artykule omówiono kroki integrowania Azure NetApp Files z obciążeniami opartymi na rozwiązaniach VMware platformy Azure. [Azure NetApp Files](../azure-netapp-files/azure-netapp-files-introduction.md) to usługa platformy Azure do migrowania i uruchamiania obciążeń plików przedsiębiorstwa w chmurze bez zmian w kodzie. Umożliwia łatwą migrację danych między infrastrukturami lokalnymi i w chmurze. Szybka synchronizacja danych z ulepszonymi zabezpieczeniami upraszcza migrację i DevOps scenariusze z możliwościami, takimi jak natychmiastowa migawka, przywracanie i integracja Active Directory.

## <a name="topology"></a>Topologia

W tym scenariuszu w celu przetestowania i sprawdzenia udziału puli Azure NetApp Files Azure NetApp Files jest konfigurowana na platformie Azure z pulą pojemności, pulą woluminów i podsiecią. Korzystamy z protokołu NFS. W tym samym regionie świadczenia usługi Azure są tworzone zarówno Azure NetApp Files, jak i rozwiązanie VMware platformy Azure. Połączenie z rozwiązaniem VMware platformy Azure odbywa się za pośrednictwem usługi Azure ExpressRoute.

![Pliki NetApp dla topologii rozwiązań VMware platformy Azure.](media/net-app-files/net-app-files-topology.png)

## <a name="prerequisites"></a>Wymagania wstępne 

> [!div class="checklist"]
> * Subskrypcja platformy Azure z Azure NetApp Files
> * Podsieć dla pliku NetApp platformy Azure
> * Maszyna wirtualna z systemem Linux na platformie Azure VMware
> * Maszyna wirtualna z systemem Windows w rozwiązaniu VMware platformy Azure

## <a name="verify-configuration-of-azure-netapp-files"></a>Weryfikowanie konfiguracji Azure NetApp Files 

Najpierw sprawdź konfigurację Azure NetApp Files utworzonego na platformie Azure na dysku Premium.

1. W Azure Portal w obszarze **Magazyn**wybierz pozycję **Azure NetApp Files**. Zostanie wyświetlona lista skonfigurowanych Azure NetApp Files.

    :::image type="content" source="media/net-app-files/azure-net-app-files-list.png" alt-text="Lista Azure NetApp Files."::: 

2. W przypadku wybrania konta NetApp można wyświetlić różne ustawienia. Na przykład po wybraniu opcji **contoso-anf2**zobaczysz jej ustawienia. 

    :::image type="content" source="media/net-app-files/net-app-settings.png" alt-text="Lista Azure NetApp Files."::: 

3. Wybierz pozycję **Pule pojemności** , aby zweryfikować skonfigurowaną pulę. 

    :::image type="content" source="media/net-app-files/capacity-pools.png" alt-text="Lista Azure NetApp Files.":::

    Można zobaczyć, że Pula magazynów jest skonfigurowana jako 4 TiB z dyskiem Premium.

4. Wybierz pozycję **woluminy** (Zobacz zrzut ekranu z kroku 2), aby wyświetlić woluminy utworzone w ramach puli pojemności.  

    :::image type="content" source="media/net-app-files/azure-net-app-volumes.png" alt-text="Lista Azure NetApp Files.":::

5. Wybierz wolumin, aby wyświetlić jego konfigurację.  

    :::image type="content" source="media/net-app-files/configuration-of-volume.png" alt-text="Lista Azure NetApp Files.":::

    Można zobaczyć, że Pula woluminów anfpool z 200 GiB została utworzona jako udział plików NetApp. Prywatna sieć wirtualna IP została utworzona dla Azure NetApp Files, która zapewnia, że ścieżka systemu plików NFS ma zostać zainstalowana na maszynach wirtualnych rozwiązań VMware platformy Azure. 

## <a name="protocols-supported-by-azure-netapp-files"></a>Protokoły obsługiwane przez Azure NetApp Files  

Azure NetApp Files obsługuje mapowanie udziałów SMB (Server Message Block) i Network File System (NFS). 

- **Udział SMB**: aby WDROŻYĆ wolumin SMB, należy najpierw utworzyć połączenie Active Directory. Określone kontrolery domeny muszą być dostępne przez delegowaną podsieć Azure NetApp Files w celu pomyślnego nawiązania połączenia. Gdy Active Directory zostanie skonfigurowany w ramach konta Azure NetApp Files, będzie on wyświetlany jako element, który można wybrać podczas tworzenia woluminów SMB. 

- **Udział NFS**: Azure NetApp Files przyczynia się do tworzenia woluminów przy użyciu systemu plików NFS (NFSv3 i nfsv 4.1), SMBv3 lub Dual Protocol (NFSV3 i SMB). Użycie pojemności woluminu jest liczone jako użycie aprowizowanej pojemności puli. System plików NFS można zainstalować na serwerze z systemem Linux przy użyciu wiersza polecenia.

## <a name="create-azure-netapp-files"></a>Utwórz Azure NetApp Files 

1. Zaloguj się do witryny [Azure Portal](https://rc.portal.azure.com/#home). W obszarze usługi platformy Azure wybierz pozycję **Azure NetApp Files**. 

2. Wybierz pozycję **+ Dodaj** , aby utworzyć nowy wolumin Azure NetApp Files. 

3. Wypełnij pola wymagane (nazwa, subskrypcja, Grupa zasobów i lokalizacja), a następnie wybierz pozycję **Utwórz**. 

## <a name="add-capacity-pools-into-azure-netapp-files"></a>Dodawanie pul pojemności do Azure NetApp Files 

1. W Azure Portal wybierz pozycję **Azure NetApp Files**, wybierz pozycję **Pule pojemności** i **+ Dodaj pulę**. 

2. Wprowadź wymagane szczegóły dotyczące nazwy puli woluminów, poziomu usługi i rozmiaru dysku (FQDN), adresu IP i wagi. Wybierz pozycję **Dodaj**.

    :::image type="content" source="media/net-app-files/add-capacity-pool.png" alt-text="Lista Azure NetApp Files.":::

3. Aby utworzyć woluminy w puli pojemności, wybierz pozycję **woluminy** w okienku wyszukiwania, a następnie wybierz pozycję **+ Dodaj wolumin**. 
 
4. Wypełnij wymagane pola, jak pokazano na poniższym zrzucie ekranu.

    :::image type="content" source="media/net-app-files/create-a-volume.png" alt-text="Lista Azure NetApp Files.":::

5. Na następnej stronie wybierz pozycję typ protokołu udział. Wybierz wersje, jeśli jest to udział NFS, a następnie wybierz domenę Active Directory, jeśli jest to udział SMB.  

6. Jeśli jest to udział NFS, Dodaj źródłowy adres IP, z którego będzie uzyskiwany dostęp do udziału typu protokołu. Wybierz pozycję **Przeglądanie + tworzenie**. 

    :::image type="content" source="media/net-app-files/select-volume-details.png" alt-text="Lista Azure NetApp Files.":::
 
    W obszarze Azure NetApp Files w Azure Portal udział NFS jest gotowy do użycia.

    :::image type="content" source="media/net-app-files/share-ready.png" alt-text="Lista Azure NetApp Files.":::

## <a name="mount-an-nfs-file-share-on-your-azure-vmware-solution-vms"></a>Instalowanie udziału plików NFS na maszynach wirtualnych rozwiązania Azure VMware

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-windows-vm"></a>Instalowanie udziału plików NFS na maszynie wirtualnej z rozwiązaniem VMware platformy Azure

- Otwórz wiersz polecenia i uruchom polecenie, aby zmapować udział plików NFS. Poniższe zrzuty ekranu przedstawiają udział dysku zmapowanego na maszynę wirtualną z systemem Windows w rozwiązaniu VMware platformy Azure.  

    :::image type="content" source="media/net-app-files/share-mapped-to-windows-vm.png" alt-text="Lista Azure NetApp Files.":::
 
    :::image type="content" source="media/net-app-files/mapped-to-windows-drive.png" alt-text="Lista Azure NetApp Files.":::

### <a name="mount-an-nfs-file-share-on-an-azure-vmware-solution-linux-vm"></a>Instalowanie udziału plików NFS na maszynie wirtualnej z rozwiązaniem Azure VMware

#### <a name="setting-up-your-azure-instance"></a>Konfigurowanie wystąpienia platformy Azure

1. W Azure Portal Skojarz wystąpienie platformy Azure z podsiecią zdefiniowaną w tej samej sieci wirtualnej co wolumin.

    > [!NOTE]
    > Podsieć wymaga reguły sieciowej grupy zabezpieczeń, która zezwala na ruch na portach NFS (2049, 111), UDP i TCP.

2. Otwórz klienta SSH i Połącz się z wystąpieniem platformy Azure. Aby uzyskać więcej informacji, zobacz [jak używać kluczy SSH w systemie Windows na platformie Azure](../virtual-machines/linux/ssh-from-windows.md).

3. Zainstaluj klienta NFS w wystąpieniu platformy Azure:
   - W wystąpieniu Red Hat Enterprise Linux lub SUSE Linux: `sudo yum install -y nfs-utils`
   - W wystąpieniu Ubuntu lub Debian: `sudo apt-get install nfs-common` 

#### <a name="mounting-your-file-system"></a>Instalowanie systemu plików

1. Utwórz nowy katalog w wystąpieniu platformy Azure: `sudo mkdir ANFPOOL`

2. Wybierz docelowy adres IP instalacji.

3. Zainstaluj system plików: `sudo mount -t nfs -o rw,hard,rsize=1048576,wsize=1048576,vers=3,tcp 10.22.3.4:/ANFPOOL ANFPOOL`

    :::image type="content" source="media/net-app-files/root-test.png" alt-text="Lista Azure NetApp Files.":::

## <a name="create-an-active-directory-connection-for-an-smb-share"></a>Utwórz połączenie Active Directory dla udziału SMB

1. Wybierz konto NetApp w Azure Portal.

2. W obszarze Azure NetApp Files wybierz pozycję **połączenia Active Directory**.

    :::image type="content" source="media/net-app-files/active-directory-connections.png" alt-text="Lista Azure NetApp Files."::: 

3. Wybierz pozycję **Dołącz** , aby dołączyć udział SMB do Active Directory. Poniższy zrzut ekranu przedstawia szczegóły domeny udziału SMB.

    :::image type="content" source="media/net-app-files/active-directory-connect-details.png" alt-text="Lista Azure NetApp Files."::: 

    Udział plików SMB platformy Azure jest gotowy do użycia.  

    :::image type="content" source="media/net-app-files/smb-file-share-details.png" alt-text="Lista Azure NetApp Files."::: 

4. Zamapuj udział SMB na maszynę wirtualną rozwiązania VMware platformy Microsoft Azure. Użyj ścieżki instalacji SMB, jak pokazano na poprzednim zrzucie ekranu. Aby uzyskać więcej informacji, zobacz [Mapowanie dysku sieciowego w systemie Windows 10](https://support.microsoft.com/help/4026635/windows-10-map-a-network-drive) .

## <a name="next-steps"></a>Następne kroki
- Dowiedz się więcej o [hierarchii magazynu Azure NetApp Files](../azure-netapp-files/azure-netapp-files-understand-storage-hierarchy.md).
- Zobacz [Zarządzanie cyklem życia maszyn wirtualnych rozwiązań VMware platformy Azure](lifecycle-management-of-azure-vmware-solution-vms.md)
- Zobacz [integrowanie rozwiązania Azure VMware w architekturze gwiazdy i szprych](concepts-hub-and-spoke.md)
