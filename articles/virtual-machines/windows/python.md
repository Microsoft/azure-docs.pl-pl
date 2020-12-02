---
title: Tworzenie maszyny wirtualnej z systemem Windows i zarządzanie nią na platformie Azure przy użyciu języka Python
description: Dowiedz się, jak za pomocą języka Python utworzyć maszynę wirtualną z systemem Windows i zarządzać nią na platformie Azure.
author: cynthn
ms.service: virtual-machines-windows
ms.workload: infrastructure
ms.topic: how-to
ms.date: 06/22/2017
ms.author: cynthn
ms.custom: devx-track-python
ms.openlocfilehash: a7117d30eb1774753f21e82ad5f812a7309d8386
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96483100"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi na platformie Azure przy użyciu języka Python

[Maszyna wirtualna platformy Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) wymaga kilku dodatkowych zasobów platformy Azure. W tym artykule opisano tworzenie i usuwanie zasobów maszyn wirtualnych oraz zarządzanie nimi przy użyciu języka Python. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu programu Visual Studio
> * Zainstaluj pakiety
> * Utwórz poświadczenia
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

Wykonanie tych kroków trwa około 20 minut.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Jeśli jeszcze tego nie zrobiono, zainstaluj [program Visual Studio](/visualstudio/install/install-visual-studio). Wybierz pozycję Programowanie w języku **Python** na stronie obciążenia, a następnie kliknij przycisk **Zainstaluj**. Podsumowanie zawiera automatycznie wybrane dla Ciebie środowisko **Python 3 64-bit (3.6.0)** . Jeśli masz już zainstalowany program Visual Studio, możesz dodać obciążenie języka Python za pomocą programu Visual Studio.
2. Po zainstalowaniu i uruchomieniu programu Visual Studio kliknij pozycję **plik**  >  **Nowy**  >  **projekt**.
3. Kliknij kolejno pozycje **Szablony**  >  **Python**  >  **aplikacja** Python, wprowadź *myPythonProject* jako nazwę projektu, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-packages"></a>Zainstaluj pakiety

1. W Eksplorator rozwiązań w obszarze *myPythonProject* kliknij prawym przyciskiem myszy pozycję **środowiska Python**, a następnie wybierz polecenie **Dodaj środowisko wirtualne**.
2. Na ekranie Dodawanie środowiska wirtualnego zaakceptuj domyślną nazwę *ENV*, upewnij się, że dla interpretera podstawowego wybrano język *Python 3,6 (64-bitowy)* , a następnie kliknij przycisk **Utwórz**.
3. Kliknij prawym przyciskiem myszy utworzone środowisko *ENV* , kliknij polecenie **Zainstaluj pakiet języka Python**, w polu wyszukiwania wpisz *Azure* , a następnie naciśnij klawisz ENTER.

Powinny zostać wyświetlone okna danych wyjściowych, dla których pomyślnie zainstalowano pakiety platformy Azure. 

## <a name="create-credentials"></a>Utwórz poświadczenia

Przed rozpoczęciem tego kroku upewnij się, że masz [nazwę główną usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, które są potrzebne w późniejszym kroku.

1. Otwórz plik *myPythonProject.py* , który został utworzony, a następnie Dodaj ten kod, aby umożliwić uruchomienie aplikacji:

    ```python
    if __name__ == "__main__":
    ```

2. Aby zaimportować wymagany kod, Dodaj te instrukcje na początku pliku. PR:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Następnie w pliku. PR Dodaj zmienne po instrukcjach import, aby określić wspólne wartości używane w kodzie:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Zastąp **Identyfikator Subscription ID** identyfikatorem subskrypcji.

4. Aby utworzyć Active Directory poświadczenia, które należy wykonać, Dodaj tę funkcję po zmiennych w pliku. PR:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Zastąp wartości **Identyfikator aplikacji**, **klucz uwierzytelniania** i **Identyfikator dzierżawy** wartościami, które zostały wcześniej zebrane podczas tworzenia nazwy głównej usługi Azure Active Directory.

5. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Tworzenie zasobów
 
### <a name="initialize-management-clients"></a>Inicjowanie klientów zarządzania

Klienci zarządzania są musieli tworzyć zasoby i zarządzać nimi za pomocą zestawu SDK języka Python na platformie Azure. Aby utworzyć klientów zarządzania programu, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

```python
resource_group_client = ResourceManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
network_client = NetworkManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
compute_client = ComputeManagementClient(
    credentials,
    SUBSCRIPTION_ID
)
```

### <a name="create-the-vm-and-supporting-resources"></a>Tworzenie maszyny wirtualnej i zasobów pomocniczych

Wszystkie zasoby muszą być zawarte w [grupie zasobów](../../azure-resource-manager/management/overview.md).

1. Aby utworzyć grupę zasobów, Dodaj tę funkcję po zmiennych w pliku. PR:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Zestawy dostępności](tutorial-availability-sets.md) ułatwiają zarządzanie maszynami wirtualnymi używanymi przez aplikację.

1. Aby utworzyć zestaw dostępności, należy dodać tę funkcję po zmiennych w pliku. PR:
   
    ```python
    def create_availability_set(compute_client):
        avset_params = {
            'location': LOCATION,
            'sku': { 'name': 'Aligned' },
            'platform_fault_domain_count': 3
        }
        availability_set_result = compute_client.availability_sets.create_or_update(
            GROUP_NAME,
            'myAVSet',
            avset_params
        )
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

[Publiczny adres IP](../../virtual-network/public-ip-addresses.md) jest wymagany do komunikowania się z maszyną wirtualną.

1. Aby utworzyć publiczny adres IP dla maszyny wirtualnej, Dodaj tę funkcję po zmiennych w pliku. PR:

    ```python
    def create_public_ip_address(network_client):
        public_ip_addess_params = {
            'location': LOCATION,
            'public_ip_allocation_method': 'Dynamic'
        }
        creation_result = network_client.public_ip_addresses.create_or_update(
            GROUP_NAME,
            'myIPAddress',
            public_ip_addess_params
        )

        return creation_result.result()
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Maszyna wirtualna musi znajdować się w podsieci [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Aby utworzyć sieć wirtualną, należy dodać tę funkcję po zmiennych w pliku. PR:

    ```python
    def create_vnet(network_client):
        vnet_params = {
            'location': LOCATION,
            'address_space': {
                'address_prefixes': ['10.0.0.0/16']
            }
        }
        creation_result = network_client.virtual_networks.create_or_update(
            GROUP_NAME,
            'myVNet',
            vnet_params
        )
        return creation_result.result()
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Aby dodać podsieć do sieci wirtualnej, Dodaj tę funkcję po zmiennych w pliku. PR:
    
    ```python
    def create_subnet(network_client):
        subnet_params = {
            'address_prefix': '10.0.0.0/24'
        }
        creation_result = network_client.subnets.create_or_update(
            GROUP_NAME,
            'myVNet',
            'mySubnet',
            subnet_params
        )

        return creation_result.result()
    ```
        
4. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Maszyna wirtualna musi mieć interfejs sieciowy, aby komunikować się z siecią wirtualną.

1. Aby utworzyć interfejs sieciowy, Dodaj tę funkcję po zmiennych w pliku. PR:

    ```python
    def create_nic(network_client):
        subnet_info = network_client.subnets.get(
            GROUP_NAME, 
            'myVNet', 
            'mySubnet'
        )
        publicIPAddress = network_client.public_ip_addresses.get(
            GROUP_NAME,
            'myIPAddress'
        )
        nic_params = {
            'location': LOCATION,
            'ip_configurations': [{
                'name': 'myIPConfig',
                'public_ip_address': publicIPAddress,
                'subnet': {
                    'id': subnet_info.id
                }
            }]
        }
        creation_result = network_client.network_interfaces.create_or_update(
            GROUP_NAME,
            'myNic',
            nic_params
        )

        return creation_result.result()
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Teraz, gdy wszystkie zasoby pomocnicze zostały utworzone, można utworzyć maszynę wirtualną.

1. Aby utworzyć maszynę wirtualną, należy dodać tę funkcję po zmiennych w pliku. PR:
   
    ```python
    def create_vm(network_client, compute_client):  
        nic = network_client.network_interfaces.get(
            GROUP_NAME, 
            'myNic'
        )
        avset = compute_client.availability_sets.get(
            GROUP_NAME,
            'myAVSet'
        )
        vm_parameters = {
            'location': LOCATION,
            'os_profile': {
                'computer_name': VM_NAME,
                'admin_username': 'azureuser',
                'admin_password': 'Azure12345678'
            },
            'hardware_profile': {
                'vm_size': 'Standard_DS1'
            },
            'storage_profile': {
                'image_reference': {
                    'publisher': 'MicrosoftWindowsServer',
                    'offer': 'WindowsServer',
                    'sku': '2012-R2-Datacenter',
                    'version': 'latest'
                }
            },
            'network_profile': {
                'network_interfaces': [{
                    'id': nic.id
                }]
            },
            'availability_set': {
                'id': avset.id
            }
        }
        creation_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm_parameters
        )
    
        return creation_result.result()
    ```

    > [!NOTE]
    > Ten samouczek tworzy maszynę wirtualną z uruchomioną wersją systemu operacyjnego Windows Server. Aby dowiedzieć się więcej na temat wybierania innych obrazów, zobacz [nawigowanie i wybieranie obrazów maszyn wirtualnych platformy Azure za pomocą programu Windows PowerShell i interfejsu wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Wykonywanie zadań zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto możesz chcieć utworzyć kod w celu zautomatyzowania powtarzalnych lub złożonych zadań.

### <a name="get-information-about-the-vm"></a>Pobierz informacje o maszynie wirtualnej

1. Aby uzyskać informacje o maszynie wirtualnej, należy dodać tę funkcję po zmiennych w pliku. PR:

    ```python
    def get_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME, expand='instanceView')
        print("hardwareProfile")
        print("   vmSize: ", vm.hardware_profile.vm_size)
        print("\nstorageProfile")
        print("  imageReference")
        print("    publisher: ", vm.storage_profile.image_reference.publisher)
        print("    offer: ", vm.storage_profile.image_reference.offer)
        print("    sku: ", vm.storage_profile.image_reference.sku)
        print("    version: ", vm.storage_profile.image_reference.version)
        print("  osDisk")
        print("    osType: ", vm.storage_profile.os_disk.os_type.value)
        print("    name: ", vm.storage_profile.os_disk.name)
        print("    createOption: ", vm.storage_profile.os_disk.create_option.value)
        print("    caching: ", vm.storage_profile.os_disk.caching.value)
        print("\nosProfile")
        print("  computerName: ", vm.os_profile.computer_name)
        print("  adminUsername: ", vm.os_profile.admin_username)
        print("  provisionVMAgent: {0}".format(vm.os_profile.windows_configuration.provision_vm_agent))
        print("  enableAutomaticUpdates: {0}".format(vm.os_profile.windows_configuration.enable_automatic_updates))
        print("\nnetworkProfile")
        for nic in vm.network_profile.network_interfaces:
            print("  networkInterface id: ", nic.id)
        print("\nvmAgent")
        print("  vmAgentVersion", vm.instance_view.vm_agent.vm_agent_version)
        print("    statuses")
        for stat in vm_result.instance_view.vm_agent.statuses:
            print("    code: ", stat.code)
            print("    displayStatus: ", stat.display_status)
            print("    message: ", stat.message)
            print("    time: ", stat.time)
        print("\ndisks");
        for disk in vm.instance_view.disks:
            print("  name: ", disk.name)
            print("  statuses")
            for stat in disk.statuses:
                print("    code: ", stat.code)
                print("    displayStatus: ", stat.display_status)
                print("    time: ", stat.time)
        print("\nVM general status")
        print("  provisioningStatus: ", vm.provisioning_state)
        print("  id: ", vm.id)
        print("  name: ", vm.name)
        print("  type: ", vm.type)
        print("  location: ", vm.location)
        print("\nVM instance status")
        for stat in vm.instance_view.statuses:
            print("  code: ", stat.code)
            print("  displayStatus: ", stat.display_status)
    ```
2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Można zatrzymać maszynę wirtualną i zachować wszystkie jej ustawienia, ale w dalszym ciągu korzystać z niej lub można zatrzymać maszynę wirtualną i cofnąć jej przydział. Po cofnięciu przydziału maszyny wirtualnej wszystkie skojarzone z nią zasoby są również cofane i rozliczane.

1. Aby zatrzymać maszynę wirtualną bez cofania jej przydziału, należy dodać tę funkcję po zmiennych w pliku. PR:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Jeśli chcesz cofnąć alokację maszyny wirtualnej, Zmień wywołanie power_off w tym kodzie:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej

1. Aby uruchomić maszynę wirtualną, należy dodać tę funkcję po zmiennych w pliku. PR:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Zmiana rozmiaru maszyny wirtualnej

Podczas wybierania rozmiaru maszyny wirtualnej należy wziąć pod uwagę wiele aspektów wdrożenia. Aby uzyskać więcej informacji, zobacz [rozmiary maszyn wirtualnych](../sizes.md).

1. Aby zmienić rozmiar maszyny wirtualnej, Dodaj tę funkcję po zmiennych w pliku. PR:

    ```python
    def update_vm(compute_client):
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        vm.hardware_profile.vm_size = 'Standard_DS3'
        update_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME, 
            VM_NAME, 
            vm
        )

    return update_result.result()
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Dodawanie dysku danych do maszyny wirtualnej

Maszyny wirtualne mogą mieć co najmniej jeden [dysk z danymi](../managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) , które są przechowywane jako wirtualne dyski twarde.

1. Aby dodać dysk z danymi do maszyny wirtualnej, Dodaj tę funkcję po zmiennych w pliku. PR: 

    ```python
    def add_datadisk(compute_client):
        disk_creation = compute_client.disks.create_or_update(
            GROUP_NAME,
            'myDataDisk1',
            {
                'location': LOCATION,
                'disk_size_gb': 1,
                'creation_data': {
                    'create_option': DiskCreateOption.empty
                }
            }
        )
        data_disk = disk_creation.result()
        vm = compute_client.virtual_machines.get(GROUP_NAME, VM_NAME)
        add_result = vm.storage_profile.data_disks.append({
            'lun': 1,
            'name': 'myDataDisk1',
            'create_option': DiskCreateOption.attach,
            'managed_disk': {
                'id': data_disk.id
            }
        })
        add_result = compute_client.virtual_machines.create_or_update(
            GROUP_NAME,
            VM_NAME,
            vm)

        return add_result.result()
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Usuwanie zasobów

Ze względu na to, że opłaty są naliczone za zasoby używane na platformie Azure, zawsze dobrym sposobem jest usunięcie zasobów, które nie są już potrzebne. Jeśli chcesz usunąć maszyny wirtualne i wszystkie zasoby pomocnicze, wystarczy usunąć grupę zasobów.

1. Aby usunąć grupę zasobów i wszystkie zasoby, należy dodać tę funkcję po zmiennych w pliku. PR:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Aby wywołać funkcję, która została wcześniej dodana, Dodaj ten kod pod instrukcją **if** na końcu pliku. PR:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Zapisz *myPythonProject.py*.

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Aby uruchomić aplikację konsolową, kliknij przycisk **Start** w programie Visual Studio.

2. Naciśnij klawisz **Enter** po zwróceniu stanu każdego zasobu. W informacjach o stanie powinien zostać wyświetlony stan aprowizacji **zakończony pomyślnie** . Po utworzeniu maszyny wirtualnej można usunąć wszystkie zasoby, które zostały utworzone. Przed naciśnięciem klawisza **Enter** w celu rozpoczęcia usuwania zasobów można zweryfikować jego tworzenie w Azure Portal. Jeśli masz otwarty Azure Portal, może być konieczne odświeżenie bloku w celu wyświetlenia nowych zasobów.  

    Ukończenie działania tej aplikacji konsolowej od początku do końca trwa około pięciu minut. Po zakończeniu działania aplikacji może upłynąć kilka minut, zanim wszystkie zasoby i grupy zasobów zostaną usunięte.


## <a name="next-steps"></a>Następne kroki

- Jeśli wystąpiły problemy ze wdrożeniem, następnym krokiem jest zapoznaj się z [tematem Rozwiązywanie problemów z wdrożeniami grup zasobów za pomocą Azure Portal](../../azure-resource-manager/templates/deployment-history.md)
- Dowiedz się więcej o [bibliotece języka Python platformy Azure](/python/api/overview/azure/?view=azure-python)