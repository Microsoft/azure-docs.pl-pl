---
title: Korzystanie z zewnętrznego magazynu plików w usłudze Lab Services | Microsoft Docs
description: Dowiedz się, jak skonfigurować laboratorium, które używa zewnętrznego magazynu plików w usługach Lab Services.
author: emaher
ms.topic: article
ms.date: 03/30/2021
ms.author: enewman
ms.openlocfilehash: 888e04db76567051f8c5eae7cf94c77e684cb146
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/01/2021
ms.locfileid: "106111794"
---
# <a name="using-external-file-storage-in-lab-services"></a>Korzystanie z zewnętrznego magazynu plików w usługach Lab Services

W tym artykule omówiono niektóre opcje zewnętrznego magazynu plików przy użyciu Azure Lab Services.  [Azure Files](https://azure.microsoft.com/services/storage/files/) oferuje w pełni zarządzane udziały plików w chmurze [dostępne za pośrednictwem protokołów SMB 2,1 i SMB 3,0.](https://docs.microsoft.com/azure/storage/files/storage-how-to-use-files-windows)  Udział Azure Files może być połączony publicznie lub prywatnie w ramach sieci wirtualnej.  Ponadto można ją skonfigurować tak, aby korzystała z poświadczeń usługi AD ucznia do łączenia się z udziałem plików.  Używanie Azure NetApp Files z woluminami NFS dla maszyn z systemem Linux jest kolejną opcją dla zewnętrznego magazynu plików z Azure Lab Services.  

## <a name="deciding-which-solution-to-use"></a>Wybór rozwiązania do użycia

Każde rozwiązanie ma inne wymagania i możliwości.  W poniższej tabeli znajduje się lista ważnych punktów, które należy wziąć pod uwagę w przypadku poszczególnych rozwiązań.  

|     Rozwiązanie    |    Ważne, aby poznać    |
| -------------- | ------------------------ |
| [Azure Files udostępnić za pomocą publicznego punktu końcowego](#azure-files-share) | <ul><li>Wszyscy mają dostęp do odczytu i zapisu.</li><li>Nie jest wymagana Komunikacja równorzędna sieci wirtualnej.</li><li>Dostępne dla wszystkich maszyn wirtualnych, a nie tylko maszyn wirtualnych laboratorium.</li><li>W przypadku korzystania z systemu Linux studenci będą mieli dostęp do klucza konta magazynu.</li></ul> |
| [Azure Files udostępnić za pomocą prywatnego punktu końcowego](#azure-files-share) | <ul><li>Wszyscy mają dostęp do odczytu i zapisu.</li><li>Wymagana Komunikacja równorzędna sieci wirtualnej.</li><li>Dostępne tylko dla maszyn wirtualnych w tej samej sieci (lub sieci równorzędnej) co konto magazynu.</li><li>W przypadku korzystania z systemu Linux studenci będą mieli dostęp do klucza konta magazynu.</li></ul> |
| [Azure Files z autoryzacją podstawową tożsamości](#azure-files-with-identity-base-authorization) | <ul><li>Można ustawić uprawnienia dostępu do odczytu lub odczytu/zapisu dla folderu lub pliku.</li><li>Wymagana Komunikacja równorzędna sieci wirtualnej.</li><li>Konto magazynu musi być połączone z Active Directory.</li><li>Maszyny wirtualne laboratorium muszą być przyłączone do domeny.</li><li>Klucz konta magazynu nie jest używany przez uczniów do łączenia się z udziałem plików.</li></ul> |
| [Pliki NetApp z woluminami NFS](#netapp-files-with-nfs-volumes) | <ul><li>Dostęp do odczytu lub odczytu/zapisu można ustawić dla woluminów.</li><li>Uprawnienia są ustawiane przy użyciu adresu IP maszyny wirtualnej ucznia.</li><li>Wymagana Komunikacja równorzędna sieci wirtualnej.</li><li>Może być konieczne zarejestrowanie się w celu korzystania z usługi plików NetApp.</li><li>Tylko system Linux.</li></ul>

Koszt korzystania z magazynu zewnętrznego nie jest uwzględniany w kosztach używania Azure Lab Services.  Aby uzyskać więcej informacji na temat cen, zobacz [Azure Files ceny](https://azure.microsoft.com/pricing/details/storage/files/) i [Azure NetApp Files cennika](https://azure.microsoft.com/pricing/details/netapp/).

## <a name="azure-files-share"></a>Udział Azure Files

Do udziałów plików platformy Azure uzyskuje się dostęp przy użyciu publicznego lub prywatnego punktu końcowego.  Udziały plików platformy Azure są instalowane przy użyciu klucza konta magazynu jako hasła.  W przypadku tej metody wszyscy mają dostęp do odczytu i zapisu do udziału plików.

W przypadku używania publicznego punktu końcowego do udziału Azure Files należy pamiętać o tym, że:

- Sieć wirtualna dla konta magazynu nie musi być połączona z kontem laboratorium.  Udział plików można utworzyć w dowolnym momencie przed opublikowaniem maszyny wirtualnej szablonu.
- Dostęp do udziału plików można uzyskać z dowolnego komputera, jeśli użytkownik ma klucz konta magazynu.  
- Studenci systemu Linux mogą zobaczyć klucz konta magazynu.  Poświadczenia służące do instalowania udziału Azure Files są przechowywane `{file-share-name}.cred` na maszynach wirtualnych z systemem Linux i odczytywane przez sudo.  Ponieważ uczniowie uzyskują dostęp sudo domyślnie na maszynach wirtualnych usługi Azure Lab, mogą odczytywać klucz konta magazynu.  Jeśli punkt końcowy konta magazynu jest publiczny, uczniowie mogą uzyskać dostęp do udziału plików poza maszyną wirtualną ucznia.  Rozważ obrócenie klucza konta magazynu po zakończeniu klasy i używanie prywatnych udziałów plików.

W przypadku korzystania z prywatnego punktu końcowego do udziału Azure Files należy pamiętać, że:

- Dostęp jest ograniczony do ruchu pochodzącego z sieci prywatnej i nie można uzyskać do niego dostępu za pośrednictwem publicznego Internetu.  Tylko maszyny wirtualne w prywatnej sieci wirtualnej, maszyny wirtualne w sieci równorzędnej do prywatnej sieci wirtualnej lub maszyny połączone z siecią VPN dla sieci prywatnej, mogą uzyskiwać dostęp do udziału plików.  
- Studenci systemu Linux mogą zobaczyć klucz konta magazynu.  Poświadczenia służące do instalowania udziału Azure Files są przechowywane `{file-share-name}.cred` na maszynach wirtualnych z systemem Linux i odczytywane przez sudo.  Ponieważ uczniowie uzyskują dostęp sudo domyślnie na maszynach wirtualnych usługi Azure Lab, mogą odczytywać klucz konta magazynu.  Rozważ obrócenie klucza konta magazynu po zakończeniu klasy.
- Takie podejście wymaga komunikacji równorzędnej sieci wirtualnej udziału plików z kontem laboratorium.  Aby można było utworzyć laboratorium, Sieć wirtualna dla konta usługi Azure Storage musi być połączona z siecią wirtualną **dla konta laboratorium** .

> [!NOTE]
> Udziały plików większe niż 5TB są dostępne tylko dla [kont [lokalnie nadmiarowy magazyn (LRS)]](/azure/storage/files/storage-files-how-to-create-large-file-share#restrictions).

Wykonaj następujące kroki, aby utworzyć maszynę wirtualną połączoną z udziałem plików platformy Azure.

1. Utwórz [konto usługi Azure Storage](/azure/storage/files/storage-how-to-create-file-share). Na stronie "Metoda łączności" Wybierz pozycję publiczny punkt końcowy lub prywatny punkt końcowy.
2. W przypadku korzystania z programu Utwórz [prywatny punkt końcowy](/azure/private-link/create-private-endpoint-storage-portal) , aby udostępnić udziały plików z sieci wirtualnej.  Utwórz [prywatną strefę DNS](/azure/dns/private-dns-privatednszone) lub Użyj istniejącej. Prywatne strefy Azure DNS zapewniają rozpoznawanie nazw w obrębie sieci wirtualnej.
3. Utwórz [udział plików platformy Azure](/azure/storage/files/storage-how-to-create-file-share). Udział plików jest dostępny przez publiczną nazwę hosta konta magazynu.
4. Zainstaluj udział plików platformy Azure na maszynie wirtualnej szablonu:
    - [Systemy](/azure/storage/files/storage-how-to-use-files-windows)
    - [[Linux]](/azure/storage/files/storage-how-to-use-files-linux).  Zobacz [używanie Azure Files z systemem Linux](#using-azure-files-with-linux) , aby uniknąć problemów z instalowaniem na maszynach wirtualnych ucznia.
5. [Opublikuj](how-to-create-manage-template.md#publish-the-template-vm) maszynę wirtualną szablonu.

> [!IMPORTANT]
> Upewnij się, że Zapora nie blokuje wychodzącego połączenia SMB przez port 445. Domyślnie dla maszyn wirtualnych platformy Azure jest dozwolony protokół SMB.

### <a name="using-azure-files-with-linux"></a>Używanie Azure Files z systemem Linux

Jeśli użyjesz _domyślnych instrukcji_ , aby zainstalować udział plików platformy Azure, po opublikowaniu szablonu ten udział plików będzie znikał na maszynach wirtualnych ucznia.  Poniżej zmodyfikowano skrypt, który rozwiązuje ten problem.  

```bash
#!/bin/bash

# Assign variables values for your storage account and file share
storage_account_name=""
storage_account_key=""
fileshare_name=""

# Do not use 'mnt' for mount directory.
# Using ‘mnt’ will cause issues on student VMs.
mount_directory="prm-mnt" 

sudo mkdir /$mount_directory/$fileshare_name
if [ ! -d "/etc/smbcredentials" ]; then
    sudo mkdir /etc/smbcredentials
fi
if [ ! -f "/etc/smbcredentials/$storage_account_name.cred" ]; then
    sudo bash -c "echo ""username=$storage_account_name"" >> /etc/smbcredentials/$storage_account_name.cred"
    sudo bash -c "echo ""password=$storage_account_key"" >> /etc/smbcredentials/$storage_account_name.cred"
fi
sudo chmod 600 /etc/smbcredentials/$storage_account_name.cred

sudo bash -c "echo ""//$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name cifs nofail,vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino"" >> /etc/fstab"
sudo mount -t cifs //$storage_account_name.file.core.windows.net/$fileshare_name /$mount_directory/$fileshare_name -o vers=3.0,credentials=/etc/smbcredentials/$storage_account_name.cred,dir_mode=0777,file_mode=0777,serverino
```

Jeśli maszyna wirtualna szablonu została już opublikowana, która instaluje udział plików platformy Azure w `/mnt` katalogu, student może wykonać jedną z następujących czynności.

- Przenieś instrukcję, aby ją zainstalować `/mnt` na początku `/etc/fstab` pliku.  
- Zmodyfikuj instrukcję, aby zainstalować `/mnt/{file-share-name}` ją w innym katalogu, na przykład `/prm-mnt/{file-share-name}` .

Przed `mount -a` zainstalowaniem katalogów uczniów należy uruchomić je ponownie.

Aby uzyskać więcej ogólnych informacji na temat używania udziałów plików z systemem Linux, zobacz [używanie Azure Files z systemem Linux](/azure/storage/files/storage-how-to-use-files-linux).

## <a name="azure-files-with-identity-base-authorization"></a>Azure Files z autoryzacją podstawową tożsamości

Udziały Azure Files mogą być również dostępne przy użyciu uwierzytelniania usługi AD, jeśli

1. Maszyna wirtualna ucznia jest przyłączona do domeny.
2. Uwierzytelnianie usługi AD jest [włączone na koncie usługi Azure Storage](/azure/storage/files/storage-files-active-directory-overview) , które obsługuje udział plików.  

Dysk sieciowy jest instalowany na maszynie wirtualnej przy użyciu tożsamości użytkownika, a nie klucza do konta magazynu.  Dostęp do konta magazynu może korzystać z publicznych lub prywatnych punktów końcowych.

Przejdźmy do kilku ważnych punktów tego podejścia.

- Uprawnienia można ustawić na poziomie katalogu lub pliku.
- Bieżące poświadczenia użytkownika są używane do uwierzytelniania w udziale plików.

Jeśli używasz publicznego punktu końcowego do udziału Azure Files, pamiętaj o tym, że:

- Sieć wirtualna dla konta magazynu nie musi nawiązać komunikacji równorzędnej z kontem laboratorium.  Udział plików można utworzyć w dowolnym momencie przed opublikowaniem maszyny wirtualnej szablonu.

W przypadku korzystania z prywatnego punktu końcowego do udziału Azure Files należy pamiętać, że:

- Dostęp jest ograniczony do ruchu pochodzącego z sieci prywatnej i nie można uzyskać do niego dostępu za pośrednictwem publicznego Internetu.  Tylko maszyny wirtualne w prywatnej sieci wirtualnej, maszyny wirtualne w sieci równorzędnej do prywatnej sieci wirtualnej lub maszyny połączone z siecią VPN dla sieci prywatnej, mogą uzyskiwać dostęp do udziału plików.  
- Takie podejście wymaga komunikacji równorzędnej sieci wirtualnej udziału plików z kontem laboratorium.  Aby można było utworzyć laboratorium, Sieć wirtualna dla konta usługi Azure Storage musi być połączona z siecią wirtualną **dla konta laboratorium** .

Wykonaj poniższe kroki, aby utworzyć uwierzytelnianie usługi AD włączone Azure Files udział i domena przyłączać maszyny wirtualne laboratorium.

1. Utwórz [konto usługi Azure Storage](/azure/storage/files/storage-how-to-create-file-share).
2. W przypadku korzystania z programu Utwórz [prywatny punkt końcowy](/azure/private-link/create-private-endpoint-storage-portal) , aby udostępnić udziały plików z sieci wirtualnej.  Utwórz [prywatną strefę DNS](/azure/dns/private-dns-privatednszone) lub Użyj istniejącej. Prywatne strefy Azure DNS zapewniają rozpoznawanie nazw w obrębie sieci wirtualnej.
3. Utwórz [udział plików platformy Azure](/azure/storage/files/storage-how-to-create-file-share).
4. Wykonaj kroki, aby włączyć autoryzację opartą na tożsamości.  W przypadku korzystania z Premium usługi AD, która jest synchronizowana z usługą Azure AD, wykonaj kroki dla [lokalnego uwierzytelniania Active Directory Domain Services za pośrednictwem protokołu SMB dla udziałów plików platformy Azure](/azure/storage/files/storage-files-identity-auth-active-directory-enable).  Jeśli używasz tylko usługi Azure AD, postępuj zgodnie z instrukcjami, aby [włączyć uwierzytelnianie Azure Active Directory Domain Services w Azure Files](/azure/storage/files/storage-files-identity-auth-active-directory-domain-service-enable).
    >[!IMPORTANT]
    >Skontaktuj się z zespołem zarządzającym usługą AD, aby upewnić się, że spełniono wszystkie wymagania wstępne wymienione w instrukcjach.
5. Przypisywanie ról uprawnień udziału SMB na platformie Azure.  Aby uzyskać szczegółowe informacje o uprawnieniach przyznanych każdej roli, zobacz [uprawnienia na poziomie udziału](/azure/storage/files/storage-files-identity-ad-ds-assign-permissions).
    1. Do osoby lub grupy, która będzie konfigurować uprawnienia do zawartości udziału plików, musi być przypisana rola "współautora udziału SMB danych w pliku magazynu".
    2. Rola "Współautor udziału danych w pliku magazynu" powinna być przypisana do uczniów, którzy muszą dodać lub edytować pliki w udziale plików.
    3. Rolę "czytelnik udziału danych w pliku magazynu" należy przypisać do uczniów, którzy będą musieli tylko odczytywać pliki z udziału plików.
6. Skonfiguruj uprawnienia na poziomie katalogu/pliku dla udziału plików.  Uprawnienia muszą zostać skonfigurowane z komputera przyłączonego do domeny, który ma dostęp sieciowy do udziału plików.  **Aby zmodyfikować uprawnienia na poziomie katalogu/pliku, zainstaluj udział plików przy użyciu klucza magazynu, a nie poświadczeń usługi AAD.**  [Set-ACL](/powershell/module/microsoft.powershell.security/set-acl) Polecenia programu PowerShell lub [icacls](/windows-server/administration/windows-commands/icacls) są zalecanymi narzędziami do przypisywania uprawnień.
7. [Komunikacja równorzędna sieci wirtualnej](how-to-connect-peer-virtual-network.md) dla konta magazynu z kontem laboratorium.
8. [Utwórz laboratorium klasowe](how-to-manage-classroom-labs.md).
9. Zapisz skrypt na maszynie wirtualnej szablonu, którą uczniowie mogą uruchomić, aby połączyć się z dyskiem sieciowym.  Aby uzyskać przykładowy skrypt:
    1. Otwórz konto magazynu w witrynie Azure Portal.
    1. Wybierz pozycję **udziały plików** w obszarze **Usługa plików** w menu.
    1. Znajdź udział, z którym chcesz nawiązać połączenie, wybierz przycisk wielokropka po prawej stronie, a następnie wybierz pozycję **Połącz**.
    1. Zostanie otwarte okno **nawiązywanie połączenia** z instrukcjami dla systemów Windows, Linux i macOS.  W przypadku korzystania z systemu Windows Ustaw **metodę uwierzytelniania** na **Active Directory**.
    1. Skopiuj kod z przykładu i Zapisz go na komputerze szablonu w `.ps1` pliku dla systemu Windows lub plików w systemie `.sh` Linux.
10. Na komputerze szablonu Pobierz i uruchom skrypt, aby [dołączyć do domeny komputery ucznia](https://github.com/Azure/azure-devtestlab/blob/master/samples/ClassroomLabs/Scripts/ActiveDirectoryJoin/README.md#usage).  `Join-AzLabADTemplate`Skrypt automatycznie [opublikuje szablon maszyny wirtualnej](how-to-create-manage-template.md#publish-the-template-vm) .  
    > [!NOTE]
    > Komputer szablonu nie zostanie przyłączony do domeny. Instruktorzy powinni przypisać opublikowaną maszynę wirtualną dla uczniów, aby wyświetlić pliki w udziale.
11. Studenci korzystający z systemu Windows mogą łączyć się z udziałem Azure Files przy użyciu [Eksploratora plików](/azure/storage/files/storage-how-to-use-files-windows) wraz z poświadczeniami, gdy podano ścieżkę do udziału plików.  Alternatywnie studenci mogą uruchomić skrypt utworzony powyżej, aby połączyć się z dyskiem sieciowym.  W przypadku uczniów korzystających z systemu Linux Uruchom utworzony powyżej skrypt.

## <a name="netapp-files-with-nfs-volumes"></a>Pliki NetApp z woluminami NFS

[Azure NetApp Files](https://azure.microsoft.com/services/netapp/) to usługa magazynu plików o wysokiej wydajności klasy korporacyjnej.

- Zasady dostępu można ustawiać dla poszczególnych woluminów.
- Zasady uprawnień są oparte na adresach IP dla każdego woluminu.
- Jeśli uczniowie potrzebują własnego woluminu, do którego inni studenci nie mają dostępu, zasady uprawnień muszą być przypisane po opublikowaniu laboratorium.
- W kontekście Azure Lab Services obsługiwane są tylko maszyny z systemem Linux.
- Aby można było utworzyć laboratorium, Sieć wirtualna dla puli pojemności Azure NetApp Files musi być połączona z siecią wirtualną dla **konta laboratorium** .

Postępuj zgodnie z poniższymi instrukcjami, aby użyć udziału Azure NetApp Files w Azure Lab Services.

1. Dołączanie do [Azure NetApp Files](https://aka.ms/azurenetappfiles), jeśli jest to konieczne.
2. Aby utworzyć pulę pojemności plików NetApp i woluminy NFS, zobacz [Konfigurowanie woluminów Azure NetApp Files i NFS](/azure/azure-netapp-files/azure-netapp-files-quickstart-set-up-account-create-volumes).  Aby uzyskać informacje na temat poziomów usług, zobacz [poziomy usługi dla Azure NetApp Files](/azure/azure-netapp-files/azure-netapp-files-service-levels).
3. [Równorzędna Sieć wirtualna](how-to-connect-peer-virtual-network.md) dla puli pojemności plików NetApp na konto laboratorium.
4. [Utwórz laboratorium klasowe](how-to-manage-classroom-labs.md).
5. Na maszynie wirtualnej szablonu Zainstaluj niezbędne składniki, aby użyć udziałów plików NFS.
    - Ubuntu

        ```bash
        sudo apt update
        sudo apt install nfs-common
        ```

    - CentOS

        ```bash
        sudo yum install nfs-utils
        ```

6. Na maszynie wirtualnej szablonu Zapisz Poniższy skrypt, `mount_fileshare.sh` Aby [zainstalować udział plików NetApp](/azure/azure-netapp-files/azure-netapp-files-mount-unmount-volumes-for-virtual-machines).  Przypisz `capacity_pool_ipaddress` zmiennej docelowy adres IP instalacji puli pojemności.  Pobierz instrukcje instalacji dla woluminu, aby znaleźć odpowiednią wartość.  Skrypt oczekuje ścieżki/nazwy woluminu plików NetApp.  Nie zapomnij uruchomić `chmod u+x mount_fileshare.sh` programu, aby upewnić się, że skrypty mogą być wykonywane przez użytkowników.

    ```bash
    #!/bin/bash
    
    if [ $# -eq 0 ];  then
        echo "Must provide volume name."
        exit 1
    fi
    
    volume_name=$1
    capacity_pool_ipaddress=0.0.0.0 # IP address of capacity pool
    
    # Do not use 'mnt' for mount directory.
    # Using ‘mnt’ may cause issues on student VMs.
    mount_directory="prm-mnt" 
    
    sudo mkdir -p /$mount_directory
    sudo mkdir /$mount_directory/$folder_name
    
    sudo mount -t nfs -o rw,hard,rsize=65536,wsize=65536,vers=3,tcp $capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name
    sudo bash -c "echo ""$capacity_pool_ipaddress:/$volume_name /$mount_directory/$volume_name nfs bg,rw,hard,noatime,nolock,rsize=65536,wsize=65536,vers=3,tcp,_netdev 0 0"" >> /etc/fstab"
    ```

7. Jeśli wszyscy uczniowie udostępniają dostęp do tego samego woluminu plików NetApp, `mount_fileshare.sh` skrypt można uruchomić na komputerze szablonu przed opublikowaniem.  Jeśli uczniowie pobierają swój własny wolumin, Zapisz skrypt do uruchomienia później przez studenta.
8. [Opublikuj](how-to-create-manage-template.md#publish-the-template-vm) maszynę wirtualną szablonu.
9. [Skonfiguruj zasady](/azure/azure-netapp-files/azure-netapp-files-configure-export-policy) udziału plików.  Eksportowanie zasad może pozwolić na dostęp do woluminu dla jednej maszyny wirtualnej lub wielu maszyn wirtualnych.  Można udzielić dostępu tylko do odczytu lub odczytu i zapisu.
10. Studenci muszą uruchomić swoją maszynę wirtualną i uruchomić skrypt w celu zainstalowania udziału plików.  Wystarczy uruchomić skrypt tylko raz.  Polecenie będzie wyglądać następująco `./mount_fileshare.sh myvolumename` .

## <a name="next-steps"></a>Następne kroki

Następne kroki są wspólne do konfigurowania dowolnego laboratorium.

- [Tworzenie szablonu i zarządzanie nim](how-to-create-manage-template.md)
- [Dodaj użytkowników](tutorial-setup-classroom-lab.md#add-users-to-the-lab)
- [Ustaw przydział](how-to-configure-student-usage.md#set-quotas-for-users)
- [Ustawianie harmonogramu](tutorial-setup-classroom-lab.md#set-a-schedule-for-the-lab)
- [Linki do rejestracji w wiadomościach e-mail z uczniami](how-to-configure-student-usage.md#send-invitations-to-users)