---
title: Tworzenie maszyny Wirtualnej systemu Windows na platformie Azure i zarządzanie nim przy użyciu języka Python
description: Dowiedz się, jak używać języka Python do tworzenia maszyny Wirtualnej systemu Windows i zarządzania nią na platformie Azure.
services: virtual-machines-windows
documentationcenter: ''
author: cynthn
manager: gwallace
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-windows
ms.workload: na
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 06/22/2017
ms.author: cynthn
ms.openlocfilehash: 670c369e25639c859f6a8d8b3c65e329b5cf7f04
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458185"
---
# <a name="create-and-manage-windows-vms-in-azure-using-python"></a>Tworzenie maszyn wirtualnych z systemem Windows i zarządzanie nimi na platformie Azure przy użyciu języka Python

[Maszyna wirtualna platformy Azure](overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) (VM) potrzebuje kilku zasobów platformy Azure. W tym artykule opisano tworzenie, zarządzanie i usuwanie zasobów maszyn wirtualnych przy użyciu języka Python. Omawiane kwestie:

> [!div class="checklist"]
> * Tworzenie projektu programu Visual Studio
> * Instalowanie pakietów
> * Tworzenie poświadczeń
> * Tworzenie zasobów
> * Wykonywanie zadań zarządzania
> * Usuwanie zasobów
> * Uruchamianie aplikacji

To trwa około 20 minut, aby wykonać te kroki.

## <a name="create-a-visual-studio-project"></a>Tworzenie projektu programu Visual Studio

1. Jeśli jeszcze tego nie zrobiłeś, zainstaluj [program Visual Studio](https://docs.microsoft.com/visualstudio/install/install-visual-studio). Wybierz pozycję **Programistyczne języka Python** na stronie Obciążenia, a następnie kliknij pozycję **Zainstaluj**. W podsumowaniu widać, że **Python 3 64-bit (3.6.0)** jest automatycznie wybierany dla Ciebie. Jeśli program Visual Studio został już zainstalowany, można dodać obciążenie języka Python przy użyciu programu Visual Studio Launcher.
2. Po zainstalowaniu i uruchomieniu programu Visual Studio kliknij pozycję **Plik** > **nowego** > **projektu**.
3. Kliknij **pozycję Szablony** > **aplikacji Python** > **Python**, wprowadź *myPythonProject* dla nazwy projektu, wybierz lokalizację projektu, a następnie kliknij przycisk **OK**.

## <a name="install-packages"></a>Instalowanie pakietów

1. W Eksploratorze rozwiązań w obszarze *myPythonProject*kliknij prawym przyciskiem myszy **pozycję Środowiska języka Python,** a następnie wybierz polecenie **Dodaj środowisko wirtualne**.
2. Na ekranie Dodaj środowisko wirtualne zaakceptuj domyślną nazwę *env*, upewnij się, że dla tłumacza podstawowego jest wybrany *język Python 3.6 (64-bitowy),* a następnie kliknij przycisk **Utwórz**.
3. Kliknij prawym przyciskiem myszy utworzone środowisko *env,* kliknij polecenie **Zainstaluj pakiet Python**, wprowadź *lazur* w polu wyszukiwania, a następnie naciśnij klawisz Enter.

Powinieneś zobaczyć w oknach wyjściowych, że pakiety platformy Azure zostały pomyślnie zainstalowane. 

## <a name="create-credentials"></a>Tworzenie poświadczeń

Przed rozpoczęciem tego kroku upewnij się, że masz [jednostkę usługi Active Directory](../../active-directory/develop/howto-create-service-principal-portal.md). Należy również zarejestrować identyfikator aplikacji, klucz uwierzytelniania i identyfikator dzierżawy, które są potrzebne w późniejszym kroku.

1. Otwórz *plik myPythonProject.py,* który został utworzony, a następnie dodaj ten kod, aby umożliwić uruchamianie aplikacji:

    ```python
    if __name__ == "__main__":
    ```

2. Aby zaimportować potrzebny kod, dodaj te instrukcje do górnej części pliku py:

    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.compute import ComputeManagementClient
    from azure.mgmt.network import NetworkManagementClient
    from azure.mgmt.compute.models import DiskCreateOption
    ```

3. Następnie w pliku py dodaj zmienne po instrukcjach importu, aby określić wspólne wartości używane w kodzie:
   
    ```
    SUBSCRIPTION_ID = 'subscription-id'
    GROUP_NAME = 'myResourceGroup'
    LOCATION = 'westus'
    VM_NAME = 'myVM'
    ```

    Zamień **identyfikator subskrypcji** na identyfikator subskrypcji.

4. Aby utworzyć poświadczenia usługi Active Directory, które należy wykonać, należy dodać tę funkcję po zmiennych w pliku py:

    ```python
    def get_credentials():
        credentials = ServicePrincipalCredentials(
            client_id = 'application-id',
            secret = 'authentication-key',
            tenant = 'tenant-id'
        )

        return credentials
    ```

    Zastąp **identyfikator aplikacji,** **klucz uwierzytelniania**i **identyfikator dzierżawy** wartościami, które zostały wcześniej zebrane podczas tworzenia jednostki usługi Azure Active Directory.

5. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    credentials = get_credentials()
    ```

## <a name="create-resources"></a>Tworzenie zasobów
 
### <a name="initialize-management-clients"></a>Inicjowanie klientów zarządzania

Klienci zarządzania są potrzebne do tworzenia zasobów i zarządzania nimi przy użyciu zestawu SDK języka Python na platformie Azure. Aby utworzyć klientów zarządzania, dodaj ten kod pod instrukcją **if** na końcu pliku py:

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

Wszystkie zasoby muszą znajdować się w [grupie zasobów](../../azure-resource-manager/management/overview.md).

1. Aby utworzyć grupę zasobów, dodaj tę funkcję po zmiennych w pliku py:

    ```python
    def create_resource_group(resource_group_client):
        resource_group_params = { 'location':LOCATION }
        resource_group_result = resource_group_client.resource_groups.create_or_update(
            GROUP_NAME, 
            resource_group_params
        )
    ```

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    create_resource_group(resource_group_client)
    input('Resource group created. Press enter to continue...')
    ```

[Zestawy dostępności](tutorial-availability-sets.md) ułatwiają utrzymanie maszyn wirtualnych używanych przez aplikację.

1. Aby utworzyć zestaw dostępności, dodaj tę funkcję po zmiennych w pliku py:
   
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

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    create_availability_set(compute_client)
    print("------------------------------------------------------")
    input('Availability set created. Press enter to continue...')
    ```

[Publiczny adres IP](../../virtual-network/virtual-network-ip-addresses-overview-arm.md) jest potrzebny do komunikowania się z maszyną wirtualną.

1. Aby utworzyć publiczny adres IP dla maszyny wirtualnej, dodaj tę funkcję po zmiennych w pliku py:

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

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    creation_result = create_public_ip_address(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Maszyna wirtualna musi znajdować się w podsieci [sieci wirtualnej](../../virtual-network/virtual-networks-overview.md).

1. Aby utworzyć sieć wirtualną, dodaj tę funkcję po zmiennych w pliku py:

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

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:
   
    ```python
    creation_result = create_vnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

3. Aby dodać podsieć do sieci wirtualnej, dodaj tę funkcję po zmiennych w pliku py:
    
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
        
4. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:
   
    ```python
    creation_result = create_subnet(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Maszyna wirtualna potrzebuje interfejsu sieciowego do komunikowania się w sieci wirtualnej.

1. Aby utworzyć interfejs sieciowy, dodaj tę funkcję po zmiennych w pliku py:

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

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    creation_result = create_nic(network_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

Teraz, gdy utworzono wszystkie zasoby pomocnicze, można utworzyć maszynę wirtualną.

1. Aby utworzyć maszynę wirtualną, dodaj tę funkcję po zmiennych w pliku py:
   
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
    > W tym samouczku utworzy się maszynę wirtualną z uruchomieniem wersji systemu operacyjnego Windows Server. Aby dowiedzieć się więcej na temat wybierania innych obrazów, zobacz [Nawigowanie i wybieranie obrazów maszyn wirtualnych platformy Azure za pomocą programu Windows PowerShell i interfejsu wiersza polecenia platformy Azure](../linux/cli-ps-findimage.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
    > 
    > 

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    creation_result = create_vm(network_client, compute_client)
    print("------------------------------------------------------")
    print(creation_result)
    input('Press enter to continue...')
    ```

## <a name="perform-management-tasks"></a>Wykonywanie zadań zarządzania

W trakcie cyklu życia maszyny wirtualnej można uruchamiać zadania zarządzania, takie jak uruchamianie, zatrzymywanie lub usuwanie maszyny wirtualnej. Ponadto można utworzyć kod do automatyzacji powtarzających się lub złożonych zadań.

### <a name="get-information-about-the-vm"></a>Uzyskaj informacje o maszynie wirtualnej

1. Aby uzyskać informacje o maszynie wirtualnej, dodaj tę funkcję po zmiennych w pliku py:

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
2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    get_vm(compute_client)
    print("------------------------------------------------------")
    input('Press enter to continue...')
    ```

### <a name="stop-the-vm"></a>Zatrzymywanie maszyny wirtualnej

Można zatrzymać maszynę wirtualną i zachować wszystkie jej ustawienia, ale nadal być naliczane za nią lub można zatrzymać maszynę wirtualną i przydzielić ją. Gdy maszyna wirtualna jest cofnięta alokacja, wszystkie zasoby skojarzone z nią są również cofnięte i kończy się rozliczenia dla niego.

1. Aby zatrzymać maszynę wirtualną bez jej alokacji, dodaj tę funkcję po zmiennych w pliku py:

    ```python
    def stop_vm(compute_client):
        compute_client.virtual_machines.power_off(GROUP_NAME, VM_NAME)
    ```

    Jeśli chcesz zmienić alokację maszyny wirtualnej, zmień power_off wywołania tego kodu:

    ```python
    compute_client.virtual_machines.deallocate(GROUP_NAME, VM_NAME)
    ```

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    stop_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="start-the-vm"></a>Uruchamianie maszyny wirtualnej

1. Aby uruchomić maszynę wirtualną, dodaj tę funkcję po zmiennych w pliku py:

    ```python
    def start_vm(compute_client):
        compute_client.virtual_machines.start(GROUP_NAME, VM_NAME)
    ```

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    start_vm(compute_client)
    input('Press enter to continue...')
    ```

### <a name="resize-the-vm"></a>Ponowne rozmiary maszyny Wirtualnej

Wiele aspektów wdrażania należy wziąć pod uwagę przy podejmowaniu decyzji o rozmiarze maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [Rozmiary maszyn wirtualnych](sizes.md).

1. Aby zmienić rozmiar maszyny wirtualnej, dodaj tę funkcję po zmiennych w pliku py:

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

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    update_result = update_vm(compute_client)
    print("------------------------------------------------------")
    print(update_result)
    input('Press enter to continue...')
    ```

### <a name="add-a-data-disk-to-the-vm"></a>Dodawanie dysku danych do maszyny wirtualnej

Maszyny wirtualne mogą mieć jeden lub więcej [dysków danych,](managed-disks-overview.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json) które są przechowywane jako dyski wirtualne.

1. Aby dodać dysk danych do maszyny wirtualnej, dodaj tę funkcję po zmiennych w pliku py: 

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

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:

    ```python
    add_result = add_datadisk(compute_client)
    print("------------------------------------------------------")
    print(add_result)
    input('Press enter to continue...')
    ```

## <a name="delete-resources"></a>Usuwanie zasobów

Ponieważ są naliczane opłaty za zasoby używane na platformie Azure, zawsze jest dobrą praktyką, aby usunąć zasoby, które nie są już potrzebne. Jeśli chcesz usunąć maszyny wirtualne i wszystkie zasoby pomocnicze, wszystko, co musisz zrobić, to usunąć grupę zasobów.

1. Aby usunąć grupę zasobów i wszystkie zasoby, dodaj tę funkcję po zmiennych w pliku py:
   
    ```python
    def delete_resources(resource_group_client):
        resource_group_client.resource_groups.delete(GROUP_NAME)
    ```

2. Aby wywołać funkcję, którą wcześniej dodano, dodaj ten kod pod instrukcją **if** na końcu pliku py:
   
    ```python
    delete_resources(resource_group_client)
    ```

3. Zapisz *myPythonProject.py*.

## <a name="run-the-application"></a>Uruchamianie aplikacji

1. Aby uruchomić aplikację konsoli, kliknij przycisk **Start** w programie Visual Studio.

2. Naciśnij **klawisz Enter** po zwrócenia stanu każdego zasobu. W informacji o stanie powinien **Succeeded** zostać wyświetlony pomyślnie aprowizowania stanu. Po utworzeniu maszyny wirtualnej można usunąć wszystkie utworzone zasoby. Przed naciśnięciem **klawisza Enter,** aby rozpocząć usuwanie zasobów, może upłynąć kilka minut, aby zweryfikować ich tworzenie w witrynie Azure portal. Jeśli masz otwarty portal Azure, może być trzeba odświeżyć blok, aby wyświetlić nowe zasoby.  

    Powinno upłynąć około pięciu minut, aby ta aplikacja konsoli działała całkowicie od początku do końca. Może upłynąć kilka minut po zakończeniu aplikacji, zanim wszystkie zasoby i grupa zasobów zostaną usunięte.


## <a name="next-steps"></a>Następne kroki

- Jeśli wystąpiły problemy z wdrożeniem, następnym krokiem będzie przyjrzenie się [wdrażaniu grup zasobów rozwiązywania problemów z witryną Azure portal](../../resource-manager-troubleshoot-deployments-portal.md)
- Dowiedz się więcej o [bibliotece języka Azure Python](https://docs.microsoft.com/python/api/overview/azure/?view=azure-python)

