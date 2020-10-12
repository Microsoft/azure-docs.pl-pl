---
title: Korzystanie z interfejs API zarządzania usługami (Python) — Podręcznik funkcji
description: Dowiedz się, jak programowo wykonywać typowe zadania związane z zarządzaniem usługami z poziomu języka Python.
services: cloud-services
documentationcenter: python
author: tanmaygore
manager: vashan
editor: ''
ms.assetid: 61538ec0-1536-4a7e-ae89-95967fe35d73
ms.service: cloud-services
ms.workload: tbd
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 05/30/2017
ms.author: tagore
ms.custom: devx-track-python
ms.openlocfilehash: 35d0f77d5f4dde9e156d22e64881ba674189d327
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87851515"
---
# <a name="use-service-management-from-python"></a>Używanie zarządzania usługami w języku Python
W tym przewodniku pokazano, jak programowo wykonywać typowe zadania związane z zarządzaniem usługami z poziomu języka Python. Klasa **ServiceManagementService** w [zestawie Azure SDK dla języka Python](https://github.com/Azure/azure-sdk-for-python) obsługuje programistyczny dostęp do większości funkcji związanych z zarządzaniem usługami, które są dostępne w [Azure Portal][management-portal]. Ta funkcja służy do tworzenia, aktualizowania i usuwania usług w chmurze, wdrożeń, usług zarządzania danymi i maszyn wirtualnych. Ta funkcja może być przydatna podczas tworzenia aplikacji, które potrzebują dostępu programistycznego do zarządzania usługami.

## <a name="what-is-service-management"></a><a name="WhatIs"> </a>Co to jest zarządzanie usługami?
Usługa Azure interfejs API zarządzania usługami zapewnia programistyczny dostęp do większości funkcji zarządzania usługami dostępnych za pomocą [Azure Portal][management-portal]. Możesz użyć zestawu Azure SDK dla języka Python, aby zarządzać usługami w chmurze i kontami magazynu.

Aby użyć interfejs API zarządzania usługami, musisz [utworzyć konto platformy Azure](https://azure.microsoft.com/pricing/free-trial/).

## <a name="concepts"></a><a name="Concepts"> </a>Pojęcia
Zestaw Azure SDK dla języka Python otacza [interfejs API zarządzania usługami][svc-mgmt-rest-api], który jest interfejsem API REST. Wszystkie operacje interfejsu API są wykonywane za pośrednictwem protokołu TLS i uwierzytelniane obustronnie przy użyciu certyfikatów X. 509 v3. Dostęp do usługi zarządzania można uzyskać z poziomu usługi działającej na platformie Azure. Dostęp do niego można uzyskać również bezpośrednio przez Internet z dowolnej aplikacji, która może wysyłać żądanie HTTPS i odbierać odpowiedź HTTPS.

## <a name="installation"></a><a name="Installation"> </a>Instalacja
Wszystkie funkcje opisane w tym artykule są dostępne w `azure-servicemanagement-legacy` pakiecie, który można zainstalować za pomocą narzędzia PIP. Aby uzyskać więcej informacji na temat instalacji (na przykład jeśli jesteś nowym w języku Python), zobacz [Install Python and the Azure SDK](/azure/developer/python/azure-sdk-install).

## <a name="connect-to-service-management"></a><a name="Connect"> </a>Łączenie z zarządzaniem usługami
Aby nawiązać połączenie z punktem końcowym zarządzania usługami, musisz mieć identyfikator subskrypcji platformy Azure oraz prawidłowy certyfikat zarządzania. Identyfikator subskrypcji można uzyskać za pomocą [Azure Portal][management-portal].

> [!NOTE]
> Teraz można używać certyfikatów utworzonych za pomocą OpenSSL podczas uruchamiania w systemie Windows. Wymagany jest język Python 2.7.4 lub nowszy. Zalecamy używanie OpenSSL zamiast PFX, ponieważ w przyszłości prawdopodobnie zostanie usunięta obsługa certyfikatów PFX.
>
>

### <a name="management-certificates-on-windowsmaclinux-openssl"></a>Certyfikaty zarządzania w systemie Windows/Mac/Linux (OpenSSL)
Można użyć [OpenSSL](https://www.openssl.org/) do utworzenia certyfikatu zarządzania. Należy utworzyć dwa certyfikaty, jeden dla serwera ( `.cer` plik) i jeden dla klienta ( `.pem` plik). Aby utworzyć `.pem` plik, wykonaj następujące polecenie:

```console
openssl req -x509 -nodes -days 365 -newkey rsa:1024 -keyout mycert.pem -out mycert.pem
```

Aby utworzyć `.cer` certyfikat, wykonaj następujące polecenie:

```console
openssl x509 -inform pem -in mycert.pem -outform der -out mycert.cer
```

Aby uzyskać więcej informacji na temat certyfikatów platformy Azure, zobacz [Omówienie certyfikatów dla platformy azure Cloud Services](cloud-services-certs-create.md). Pełny opis parametrów OpenSSL można znaleźć w dokumentacji pod adresem [https://www.openssl.org/docs/apps/openssl.html](https://www.openssl.org/docs/apps/openssl.html) .

Po utworzeniu tych plików Przekaż `.cer` plik na platformę Azure. W [Azure Portal][management-portal]na karcie **Ustawienia** wybierz pozycję **Przekaż**. Zanotuj miejsce zapisania `.pem` pliku.

Po uzyskaniu identyfikatora subskrypcji Utwórz certyfikat i przekaż go `.cer` na platformę Azure, a następnie połącz się z punktem końcowym zarządzania platformy Azure. Nawiąż połączenie, przekazując Identyfikator subskrypcji i ścieżkę do `.pem` pliku do **ServiceManagementService**.

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = '<path_to_.pem_certificate>'

sms = ServiceManagementService(subscription_id, certificate_path)
```

W poprzednim przykładzie `sms` jest obiektem **ServiceManagementService** . Klasa **ServiceManagementService** jest klasą podstawową służącą do zarządzania usługami platformy Azure.

### <a name="management-certificates-on-windows-makecert"></a>Certyfikaty zarządzania w systemie Windows (MakeCert)
Na maszynie można utworzyć certyfikat zarządzania z podpisem własnym za pomocą programu `makecert.exe` . Otwórz **wiersz polecenia programu Visual Studio** jako **administrator** i użyj następującego polecenia, zastępując *AzureCertificate* nazwą certyfikatu, którego chcesz użyć:

```console
makecert -sky exchange -r -n "CN=AzureCertificate" -pe -a sha1 -len 2048 -ss My "AzureCertificate.cer"
```

Polecenie tworzy `.cer` plik i instaluje go w **osobistym** magazynie certyfikatów. Aby uzyskać więcej informacji, zobacz [Omówienie certyfikatów dla platformy Azure Cloud Services](cloud-services-certs-create.md).

Po utworzeniu certyfikatu Przekaż `.cer` plik na platformę Azure. W [Azure Portal][management-portal]na karcie **Ustawienia** wybierz pozycję **Przekaż**.

Po uzyskaniu identyfikatora subskrypcji Utwórz certyfikat i przekaż go `.cer` na platformę Azure, a następnie połącz się z punktem końcowym zarządzania platformy Azure. Połącz się, przekazując Identyfikator subskrypcji i lokalizację certyfikatu w **osobistym** magazynie certyfikatów do **ServiceManagementService** (ponownie Zastąp *AzureCertificate* nazwą certyfikatu).

```python
from azure import *
from azure.servicemanagement import *

subscription_id = '<your_subscription_id>'
certificate_path = 'CURRENT_USER\\my\\AzureCertificate'

sms = ServiceManagementService(subscription_id, certificate_path)
```

W poprzednim przykładzie `sms` jest obiektem **ServiceManagementService** . Klasa **ServiceManagementService** jest klasą podstawową służącą do zarządzania usługami platformy Azure.

## <a name="list-available-locations"></a><a name="ListAvailableLocations"> </a>Wyświetl dostępne lokalizacje
Aby wyświetlić listę lokalizacji dostępnych dla usług hostingu, użyj metody **list \_ Locations** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_locations()
for location in result:
    print(location.name)
```

Podczas tworzenia usługi w chmurze lub usługi magazynu należy podać prawidłową lokalizację. Metoda ** \_ lokalizacji listy** zawsze zwraca aktualną listę aktualnie dostępnych lokalizacji. W przypadku tego zapisu dostępne są następujące lokalizacje:

* West Europe
* Europa Północna
* Azja Południowo-Wschodnia
* Azja Wschodnia
* Central US
* Północno-środkowe stany USA
* South Central US
* Zachodnie stany USA
* East US
* Japonia Wschodnia
* Japonia Zachodnia
* Brazil South
* Australia Wschodnia
* Australia Południowo-Wschodnia

## <a name="create-a-cloud-service"></a><a name="CreateCloudService"> </a>Tworzenie usługi w chmurze
Gdy tworzysz aplikację i uruchamiasz ją na platformie Azure, kod i konfiguracja są nazywane [usługą w chmurze][cloud service]platformy Azure. (Była znana jako *usługa hostowana* we wcześniejszych wersjach platformy Azure). Za pomocą metody **create \_ hostowanej \_ usługi** można utworzyć nową usługę hostowaną. Utwórz usługę, podając nazwę usługi hostowanej (która musi być unikatowa na platformie Azure), etykietę (automatycznie zakodowaną w formacie base64), opis i lokalizację.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myhostedservice'
label = 'myhostedservice'
desc = 'my hosted service'
location = 'West US'

sms.create_hosted_service(name, label, desc, location)
```

Wszystkie usługi hostowane dla subskrypcji można wyświetlić za pomocą metody **list \_ \_ usług hostowanych** .

```python
result = sms.list_hosted_services()

for hosted_service in result:
    print('Service name: ' + hosted_service.service_name)
    print('Management URL: ' + hosted_service.url)
    print('Location: ' + hosted_service.hosted_service_properties.location)
    print('')
```

Aby uzyskać informacje dotyczące konkretnej usługi hostowanej, należy przekazać nazwę usługi hostowanej do metody **pobierania \_ \_ \_ właściwości usługi hostowanej** .

```python
hosted_service = sms.get_hosted_service_properties('myhostedservice')

print('Service name: ' + hosted_service.service_name)
print('Management URL: ' + hosted_service.url)
print('Location: ' + hosted_service.hosted_service_properties.location)
```

Po utworzeniu usługi w chmurze Wdróż swój kod w usłudze przy użyciu metody **tworzenia \_ wdrożenia** .

## <a name="delete-a-cloud-service"></a><a name="DeleteCloudService"> </a>Usuwanie usługi w chmurze
Możesz usunąć usługę w chmurze, przekazując nazwę usługi do metody **delete \_ hostowanej \_ usługi** .

```python
sms.delete_hosted_service('myhostedservice')
```

Aby można było usunąć usługę, należy najpierw usunąć wszystkie wdrożenia usługi. Aby uzyskać więcej informacji, zobacz [usuwanie wdrożenia](#DeleteDeployment).

## <a name="delete-a-deployment"></a><a name="DeleteDeployment"> </a>Usuwanie wdrożenia
Aby usunąć wdrożenie, należy użyć metody **usuwania \_ wdrożenia** . Poniższy przykład pokazuje, jak usunąć wdrożenie o nazwie `v1` :

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment('myhostedservice', 'v1')
```

## <a name="create-a-storage-service"></a><a name="CreateStorageService"> </a>Tworzenie usługi magazynu
[Usługa magazynu](../storage/common/storage-create-storage-account.md) zapewnia dostęp do [obiektów BLOB](../storage/blobs/storage-python-how-to-use-blob-storage.md), [tabel](../cosmos-db/table-storage-how-to-use-python.md)i [kolejek](../storage/queues/storage-python-how-to-use-queue-storage.md)platformy Azure. Aby utworzyć usługę magazynu, musisz mieć nazwę usługi (od 3 do 24 małych liter i unikatowy na platformie Azure). Potrzebny jest również opis, etykieta (do 100 znaków, automatycznie zakodowana w formacie base64) i lokalizacja. Poniższy przykład pokazuje, jak utworzyć usługę magazynu, określając lokalizację:

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mystorageaccount'
label = 'mystorageaccount'
location = 'West US'
desc = 'My storage account description.'

result = sms.create_storage_account(name, desc, label, location=location)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

W poprzednim przykładzie stan operacji **tworzenia \_ \_ konta magazynu** można pobrać, przekazując wynik zwrócony przez polecenie **Utwórz \_ \_ konto magazynu** do metody **pobierania \_ \_ stanu operacji** . 

Możesz wyświetlić listę kont magazynu i ich właściwości przy użyciu metody **list \_ \_ accounts** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_storage_accounts()
for account in result:
    print('Service name: ' + account.service_name)
    print('Location: ' + account.storage_service_properties.location)
    print('')
```

## <a name="delete-a-storage-service"></a><a name="DeleteStorageService"> </a>Usuwanie usługi magazynu
Aby usunąć usługę magazynu, należy przekazać nazwę usługi magazynu do metody **usuwania \_ \_ konta magazynu** . Usunięcie usługi magazynu powoduje usunięcie wszystkich danych przechowywanych w usłudze (obiektów blob, tabel i kolejek).

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_storage_account('mystorageaccount')
```

## <a name="list-available-operating-systems"></a><a name="ListOperatingSystems"> </a>Wyświetl dostępne systemy operacyjne
Aby wyświetlić listę systemów operacyjnych, które są dostępne dla usług hostingu, użyj metody z **listą \_ \_ systemów operacyjnych** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.list_operating_systems()

for os in result:
    print('OS: ' + os.label)
    print('Family: ' + os.family_label)
    print('Active: ' + str(os.is_active))
```

Alternatywnie można użyć metody z **listą \_ \_ \_ rodzin systemów operacyjnych** , która grupuje systemy operacyjne według rodziny.

```python
result = sms.list_operating_system_families()

for family in result:
    print('Family: ' + family.label)
    for os in family.operating_systems:
        if os.is_active:
            print('OS: ' + os.label)
            print('Version: ' + os.version)
    print('')
```

## <a name="create-an-operating-system-image"></a><a name="CreateVMImage"> </a>Tworzenie obrazu systemu operacyjnego
Aby dodać obraz systemu operacyjnego do repozytorium obrazów, użyj metody **Dodawanie obrazu systemu \_ operacyjnego \_ ** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'mycentos'
label = 'mycentos'
os = 'Linux' # Linux or Windows
media_link = 'url_to_storage_blob_for_source_image_vhd'

result = sms.add_os_image(label, media_link, name, os)

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

Aby wyświetlić listę dostępnych obrazów systemów operacyjnych, użyj metody **wyświetlania obrazów systemu \_ operacyjnego \_ ** . Zawiera wszystkie obrazy platformy i obrazy użytkowników.

```python
result = sms.list_os_images()

for image in result:
    print('Name: ' + image.name)
    print('Label: ' + image.label)
    print('OS: ' + image.os)
    print('Category: ' + image.category)
    print('Description: ' + image.description)
    print('Location: ' + image.location)
    print('Media link: ' + image.media_link)
    print('')
```

## <a name="delete-an-operating-system-image"></a><a name="DeleteVMImage"> </a>Usuwanie obrazu systemu operacyjnego
Aby usunąć obraz użytkownika, użyj metody **usuwania \_ \_ obrazu systemu operacyjnego** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

result = sms.delete_os_image('mycentos')

operation_result = sms.get_operation_status(result.request_id)
print('Operation status: ' + operation_result.status)
```

## <a name="create-a-virtual-machine"></a><a name="CreateVM"> </a>Tworzenie maszyny wirtualnej
Aby utworzyć maszynę wirtualną, należy najpierw utworzyć [usługę w chmurze](#CreateCloudService). Następnie Utwórz wdrożenie maszyny wirtualnej, korzystając z metody **tworzenia \_ \_ \_ wdrożenia maszyny wirtualnej** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

# Name of an os image as returned by list_os_images
image_name = 'OpenLogic__OpenLogic-CentOS-62-20120531-en-us-30GB.vhd'

# Destination storage account container/blob where the VM disk
# will be created
media_link = 'url_to_target_storage_blob_for_vm_hd'

# Linux VM configuration, you can use WindowsConfigurationSet
# for a Windows VM instead
linux_config = LinuxConfigurationSet('myhostname', 'myuser', 'mypassword', True)

os_hd = OSVirtualHardDisk(image_name, media_link)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=os_hd,
    role_size='Small')
```

## <a name="delete-a-virtual-machine"></a><a name="DeleteVM"> </a>Usuwa maszynę wirtualną
Aby usunąć maszynę wirtualną, należy najpierw usunąć wdrożenie przy użyciu metody **usuwania \_ wdrożenia** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

sms.delete_deployment(service_name='myvm',
    deployment_name='myvm')
```

Usługę w chmurze można następnie usunąć przy użyciu metody **usuwania \_ \_ usługi hostowanej** .

```python
sms.delete_hosted_service(service_name='myvm')
```

## <a name="create-a-virtual-machine-from-a-captured-virtual-machine-image"></a>Tworzenie maszyny wirtualnej na podstawie przechwyconego obrazu maszyny wirtualnej
Aby przechwycić obraz maszyny wirtualnej, należy najpierw wywołać **metodę \_ przechwytywania \_ obrazu maszyny wirtualnej** .

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

# replace the below three parameters with actual values
hosted_service_name = 'hs1'
deployment_name = 'dep1'
vm_name = 'vm1'

image_name = vm_name + 'image'
image = CaptureRoleAsVMImage    ('Specialized',
    image_name,
    image_name + 'label',
    image_name + 'description',
    'english',
    'mygroup')

result = sms.capture_vm_image(
        hosted_service_name,
        deployment_name,
        vm_name,
        image
    )
```

Aby upewnić się, że obraz został pomyślnie przechwycony, użyj interfejsu API **listy \_ \_ obrazów maszyn wirtualnych** . Upewnij się, że obraz jest wyświetlany w wynikach.

```python
images = sms.list_vm_images()
```

Aby na koniec utworzyć maszynę wirtualną przy użyciu przechwyconego obrazu, użyj metody ** \_ \_ \_ wdrożenia maszyny wirtualnej** tak jak wcześniej, ale ten czas zostanie przekazany w vm_image_name.

```python
from azure import *
from azure.servicemanagement import *

sms = ServiceManagementService(subscription_id, certificate_path)

name = 'myvm'
location = 'West US'

#Set the location
sms.create_hosted_service(service_name=name,
    label=name,
    location=location)

sms.create_virtual_machine_deployment(service_name=name,
    deployment_name=name,
    deployment_slot='production',
    label=name,
    role_name=name,
    system_config=linux_config,
    os_virtual_hard_disk=None,
    role_size='Small',
    vm_image_name = image_name)
```

Aby dowiedzieć się więcej o tym, jak przechwycić maszynę wirtualną z systemem Linux w klasycznym modelu wdrażania, zobacz [przechwytywanie maszyny wirtualnej z systemem Linux](../virtual-machines/linux/classic/capture-image-classic.md).

Aby dowiedzieć się więcej o tym, jak przechwycić maszynę wirtualną z systemem Windows w klasycznym modelu wdrażania, zobacz [przechwytywanie maszyny wirtualnej z systemem Windows](../virtual-machines/windows/classic/capture-image-classic.md).

## <a name="next-steps"></a><a name="What's Next"> </a>Następne kroki
Teraz, gdy znasz już podstawowe informacje dotyczące zarządzania usługami, możesz uzyskać dostęp do [kompletnej dokumentacji dotyczącej interfejsów API dla zestawu Azure Python SDK](https://azure-sdk-for-python.readthedocs.org/) i ułatwić wykonywanie złożonych zadań związanych z zarządzaniem aplikacją w języku Python.

Aby uzyskać więcej informacji, zobacz [Centrum deweloperów Python](https://azure.microsoft.com/develop/python/).

[What is service management?]: #WhatIs
[Concepts]: #Concepts
[Connect to service management]: #Connect
[List available locations]: #ListAvailableLocations
[Create a cloud service]: #CreateCloudService
[Delete a cloud service]: #DeleteCloudService
[Create a deployment]: #CreateDeployment
[Update a deployment]: #UpdateDeployment
[Move deployments between staging and production]: #MoveDeployments
[Delete a deployment]: #DeleteDeployment
[Create a storage service]: #CreateStorageService
[Delete a storage service]: #DeleteStorageService
[List available operating systems]: #ListOperatingSystems
[Create an operating system image]: #CreateVMImage
[Delete an operating system image]: #DeleteVMImage
[Create a virtual machine]: #CreateVM
[Delete a virtual machine]: #DeleteVM
[Next steps]: #NextSteps
[management-portal]: https://portal.azure.com/
[svc-mgmt-rest-api]: https://msdn.microsoft.com/library/windowsazure/ee460799.aspx


[cloud service]:/azure/cloud-services/
