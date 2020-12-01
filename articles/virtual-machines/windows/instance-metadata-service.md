---
title: Instance Metadata Service platformy Azure dla systemu Windows
description: Dowiedz się więcej na temat Instance Metadata Service platformy Azure oraz informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych w systemie Windows.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: cc0d47807101a3cfbb26e7ea69cc7d117d3f9b31
ms.sourcegitcommit: 5e5a0abe60803704cf8afd407784a1c9469e545f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/01/2020
ms.locfileid: "96435233"
---
# <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

Usługa Azure Instance Metadata Service (IMDS) zawiera informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych. Służy do zarządzania maszynami wirtualnymi i ich konfigurowania.
Te informacje obejmują jednostki SKU, magazyn, konfiguracje sieci i nadchodzące zdarzenia konserwacji. Pełną listę dostępnych danych można znaleźć w temacie [Metadata API](#metadata-apis).


IMDS jest dostępny dla uruchomionych wystąpień maszyn wirtualnych i wystąpień zestawów skalowania maszyn wirtualnych. Wszystkie interfejsy API obsługują maszyny wirtualne tworzone i zarządzane przy użyciu [Azure Resource Manager](/rest/api/resources/). Tylko zaświadczone i sieciowe punkty końcowe obsługują maszyny wirtualne utworzone przy użyciu klasycznego modelu wdrażania. Zaświadczanie punktu końcowego działa tylko w ograniczonym zakresie.

IMDS jest punktem końcowym REST, który jest dostępny w dobrze znanym adresie IP bez obsługi routingu ( `169.254.169.254` ). Dostęp do niego można uzyskać tylko z poziomu maszyny wirtualnej. Komunikacja między maszyną wirtualną i IMDS nigdy nie opuszcza hosta.
Klienci korzystający z protokołu HTTP pomijają serwery proxy sieci Web w ramach maszyny wirtualnej podczas wykonywania zapytań do IMDS i traktują te `169.254.169.254` same dane co [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Zabezpieczenia

Punkt końcowy IMDS jest dostępny tylko w ramach uruchomionego wystąpienia maszyny wirtualnej w adresie IP bez obsługi routingu. Ponadto wszystkie żądania z `X-Forwarded-For` nagłówkiem są odrzucane przez usługę.
Żądania muszą również zawierać `Metadata: true` nagłówek, aby upewnić się, że rzeczywiste żądanie było bezpośrednio przeznaczone i nie jest częścią niezamierzonego przekierowania.

> [!IMPORTANT]
> IMDS nie jest kanałem dla poufnych danych. Punkt końcowy jest otwarty dla wszystkich procesów na maszynie wirtualnej. Weź pod uwagę informacje udostępniane przez tę usługę jako udostępnione informacje wszystkim aplikacjom działającym w ramach maszyny wirtualnej.

## <a name="usage"></a>Użycie

### <a name="access-azure-instance-metadata-service"></a>Dostęp do usługi Azure Instance Metadata Service

Aby uzyskać dostęp do usługi IMDS, Utwórz maszynę wirtualną na podstawie [Azure Resource Manager](/rest/api/resources/) lub [Azure Portal](https://portal.azure.com)i użyj następujących przykładów.
Aby uzyskać więcej przykładów, zobacz [przykłady metadanych wystąpienia platformy Azure](https://github.com/microsoft/azureimds).

Oto przykładowy kod umożliwiający pobranie wszystkich metadanych wystąpienia. Aby uzyskać dostęp do określonego źródła danych, zapoznaj się z sekcją [API metadanych](#metadata-apis) . 

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance?api-version=2020-09-01 | ConvertTo-Json
```
> [!NOTE]
> `-NoProxy`Flaga jest dostępna tylko w programie PowerShell 6 lub nowszym. Możesz pominąć flagę, jeśli nie masz konfiguracji serwera proxy.

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Potoku zapytania REST za pomocą `ConvertTo-Json` polecenia cmdlet w celu uzyskania częściowego drukowania.

```json
{
    "compute": {
        "azEnvironment": "AZUREPUBLICCLOUD",
        "isHostCompatibilityLayerVm": "true",
        "licenseType":  "Windows_Client",
        "location": "westus",
        "name": "examplevmname",
        "offer": "Windows",
        "osProfile": {
            "adminUsername": "admin",
            "computerName": "examplevmname",
            "disablePasswordAuthentication": "true"
        },
        "osType": "linux",
        "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
        "plan": {
            "name": "planName",
            "product": "planProduct",
            "publisher": "planPublisher"
        },
        "platformFaultDomain": "36",
        "platformUpdateDomain": "42",
        "publicKeys": [{
                "keyData": "ssh-rsa 0",
                "path": "/home/user/.ssh/authorized_keys0"
            },
            {
                "keyData": "ssh-rsa 1",
                "path": "/home/user/.ssh/authorized_keys1"
            }
        ],
        "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
        "resourceGroupName": "macikgo-test-may-23",
        "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
        "securityProfile": {
            "secureBootEnabled": "true",
            "virtualTpmEnabled": "false"
        },
        "sku": "Windows-Server-2012-R2-Datacenter",
        "storageProfile": {
            "dataDisks": [{
                "caching": "None",
                "createOption": "Empty",
                "diskSizeGB": "1024",
                "image": {
                    "uri": ""
                },
                "lun": "0",
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampledatadiskname",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }],
            "imageReference": {
                "id": "",
                "offer": "UbuntuServer",
                "publisher": "Canonical",
                "sku": "16.04.0-LTS",
                "version": "latest"
            },
            "osDisk": {
                "caching": "ReadWrite",
                "createOption": "FromImage",
                "diskSizeGB": "30",
                "diffDiskSettings": {
                    "option": "Local"
                },
                "encryptionSettings": {
                    "enabled": "false"
                },
                "image": {
                    "uri": ""
                },
                "managedDisk": {
                    "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                    "storageAccountType": "Standard_LRS"
                },
                "name": "exampleosdiskname",
                "osType": "Linux",
                "vhd": {
                    "uri": ""
                },
                "writeAcceleratorEnabled": "false"
            }
        },
        "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
        "tags": "baz:bash;foo:bar",
        "version": "15.05.22",
        "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
        "vmScaleSetName": "crpteste9vflji9",
        "vmSize": "Standard_A3",
        "zone": ""
    },
    "network": {
        "interface": [{
            "ipv4": {
               "ipAddress": [{
                    "privateIpAddress": "10.144.133.132",
                    "publicIpAddress": ""
                }],
                "subnet": [{
                    "address": "10.144.133.128",
                    "prefix": "26"
                }]
            },
            "ipv6": {
                "ipAddress": [
                 ]
            },
            "macAddress": "0011AAFFBB22"
        }]
    }
}
```

### <a name="data-output"></a>Dane wyjściowe

Domyślnie IMDS zwraca dane w formacie JSON ( `Content-Type: application/json` ). Niektóre interfejsy API mogą jednak zwracać dane w różnych formatach, jeśli jest to wymagane.
W poniższej tabeli wymieniono inne formaty danych, które mogą być obsługiwane przez interfejsy API.

Interfejs API | Domyślny format danych | Inne formaty
--------|---------------------|--------------
/attested | json | brak
/Identity | json | brak
/instance | json | tekst
/scheduledevents | json | brak

Aby uzyskać dostęp do formatu niedomyślnej odpowiedzi, należy określić żądany format jako parametr ciągu zapytania w żądaniu. Przykład:

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> W przypadku węzłów liści w `/metadata/instance` , element `format=json` nie działa. Dla tych zapytań należy `format=text` jawnie określić, ponieważ format domyślny to JSON.

### <a name="version"></a>Wersja

IMDS jest w wersji i określanie wersji interfejsu API w żądaniu HTTP jest obowiązkowe.

Obsługiwane wersje interfejsu API to: 
- 2017-03-01
- 2017-04-02
- 2017-08-01 
- 2017-10-01
- 2017-12-01 
- 2018-02-01
- 2018-04-02
- 2018-10-01
- 2019-02-01
- 2019-03-11
- 2019-04-30
- 2019-06-01
- 2019-06-04
- 2019-08-01
- 2019-08-15
- 2019-11-01
- 2020-06-01
- 2020-07-15
- 2020-09-01
- 2020-10-01

> [!NOTE]
> Wersja 2020-10-01 może jeszcze nie być dostępna w każdym regionie.

W miarę dodawania nowszych wersji można nadal uzyskać dostęp do starszych wersji, jeśli skrypty są zależne od określonych formatów danych.

Jeśli nie określisz wersji, zostanie wyświetlony komunikat o błędzie z listą najnowszych obsługiwanych wersji.

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy przykład wskazuje warunek błędu, gdy wersja nie jest określona. Odpowiedź jest całkiem wydrukowana pod kątem czytelności.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance
```

**Odpowiedź**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2020-10-01",
        "2020-09-01",
        "2020-07-15"
    ]
}
```

## <a name="metadata-apis"></a>Interfejsy API metadanych

IMDS zawiera wiele interfejsów API reprezentujących różne źródła danych.

Interfejs API | Opis | Wprowadzona wersja
----|-------------|-----------------------
/attested | Zobacz [zaświadczone dane](#attested-data) | 2018-10-01
/Identity | Zobacz [pozyskiwanie tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | Zobacz [interfejs API wystąpienia](#instance-api) | 2017-04-02
/scheduledevents | Zobacz [zaplanowane zdarzenia](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Interfejs API wystąpienia

Interfejs API wystąpienia uwidacznia ważne metadane dla wystąpień maszyn wirtualnych, w tym maszyn wirtualnych, sieci i magazynu. Dostęp do następujących kategorii można uzyskać za pomocą `instance/compute` :

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
azEnvironment | Środowisko platformy Azure, w którym działa maszyna wirtualna. | 2018-10-01
customData | Ta funkcja jest obecnie wyłączona. | 2019-02-01
isHostCompatibilityLayerVm | Określa, czy maszyna wirtualna jest uruchamiana na warstwie zgodności hosta. | 2020-06-01
licenseType | Typ licencji dla [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Należy zauważyć, że ta funkcja jest obecna tylko w przypadku maszyn wirtualnych z obsługą AHB. | 2020-09-01
location | Region świadczenia usługi Azure, w którym jest uruchomiona maszyna wirtualna. | 2017-04-02
name | Nazwa maszyny wirtualnej. | 2017-04-02
offer | Informacje o ofercie dla obrazu maszyny wirtualnej. Ta wartość jest dostępna tylko dla obrazów wdrożonych z galerii obrazów systemu Azure. | 2017-04-02
osProfile.adminUsername | Określa nazwę konta administratora. | 2020-07-15
osProfile. computerName | Określa nazwę komputera. | 2020-07-15
osProfile.disablePasswordAuthentication | Określa, czy uwierzytelnianie hasła jest wyłączone. Należy zauważyć, że jest to obecne tylko w przypadku maszyn wirtualnych z systemem Linux. | 2020-10-01
osType | Linux lub Windows. | 2017-04-02
placementGroupId | [Grupa umieszczania](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) zestawu skalowania maszyn wirtualnych. | 2017-08-01
plan | [Zaplanuj](/rest/api/compute/virtualmachines/createorupdate#plan) , zawierający nazwę, produkt i wydawcę dla maszyny wirtualnej, jeśli jest to obraz portalu Azure Marketplace. | 2018-04-02
platformUpdateDomain |  [Zaktualizuj domenę](../manage-availability.md) , w której działa maszyna wirtualna. | 2017-04-02
platformFaultDomain | [Domena błędów](../manage-availability.md) , w której URUCHOMIONo maszynę wirtualną. | 2017-04-02
dostawcy | Dostawca maszyny wirtualnej. | 2018-10-01
publicKeys | [Kolekcja kluczy publicznych](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) przypisanych do maszyny wirtualnej i ścieżki. | 2018-04-02
publisher | Wydawca obrazu maszyny wirtualnej. | 2017-04-02
resourceGroupName | [Grupa zasobów](../../azure-resource-manager/management/overview.md) dla maszyny wirtualnej. | 2017-08-01
resourceId | W [pełni kwalifikowany](/rest/api/resources/resources/getbyid) identyfikator zasobu. | 2019-03-11
sku | Określona jednostka SKU dla obrazu maszyny wirtualnej. | 2017-04-02
securityProfile. secureBootEnabled | Określa, czy na maszynie wirtualnej jest włączony bezpieczny rozruch w interfejsie UEFI. | 2020-06-01
securityProfile.virtualTpmEnabled | Określa, czy wirtualny moduł TPM (TPM) jest włączony na maszynie wirtualnej. | 2020-06-01
Obszarze storageprofile | Zobacz [profil magazynu](#storage-metadata). | 2019-06-01
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej. | 2017-08-01
tags | [Tagi](../../azure-resource-manager/management/tag-resources.md) dla maszyny wirtualnej.  | 2017-08-01
tagsList | Tagi sformatowane jako tablica JSON ułatwiające analizowanie programistyczne.  | 2019-06-04
Wersja | Wersja obrazu maszyny wirtualnej. | 2017-04-02
vmId | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny wirtualnej. | 2017-04-02
vmScaleSetName | [Nazwa zestawu skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/overview.md) w zestawie skalowania maszyn wirtualnych. | 2017-12-01
vmSize | Zapoznaj się z [rozmiarem maszyny wirtualnej](../sizes.md). | 2017-04-02
strefa | [Strefa dostępności](../../availability-zones/az-overview.md) maszyny wirtualnej. | 2017-12-01

### <a name="sample-1-track-a-vm-running-on-azure"></a>Przykład 1: śledzenie maszyny wirtualnej działającej na platformie Azure

Jako dostawca usług może być konieczne śledzenie liczby maszyn wirtualnych korzystających z oprogramowania lub agentów, którzy muszą śledzić unikatowość maszyny wirtualnej. Aby można było uzyskać unikatowy identyfikator dla maszyny wirtualnej, użyj `vmId` pola z IMDS.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Odpowiedź**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-different-data-replicas"></a>Przykład 2: umieszczanie różnych replik danych

W niektórych scenariuszach umieszczanie różnych replik danych ma podstawowe znaczenie. Na przykład [umieszczenie repliki](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) systemu plików HDFS lub umieszczenie kontenera za pośrednictwem programu [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać znajomości, `platformFaultDomain` a `platformUpdateDomain` maszyna wirtualna jest uruchomiona.
Można również użyć [strefy dostępności](../../availability-zones/az-overview.md) , aby wystąpienia mogły podejmować te decyzje.
Możesz wysyłać zapytania o dane bezpośrednio za pośrednictwem IMDS.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Odpowiedź**

```text
0
```

### <a name="sample-3-get-more-information-about-the-vm-during-support-case"></a>Przykład 3: Uzyskaj więcej informacji na temat maszyny wirtualnej w przypadku pomocy technicznej

Jako dostawca usług możesz uzyskać informacje na temat pomocy technicznej, w której chcesz poznać więcej informacji o maszynie wirtualnej. Zaproszenie klienta o udostępnienie metadanych obliczeniowych może być przydatne w tym przypadku.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01
```

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

```json
{
    "azEnvironment": "AZUREPUBLICCLOUD",
    "isHostCompatibilityLayerVm": "true",
    "licenseType":  "Windows_Client",
    "location": "westus",
    "name": "examplevmname",
    "offer": "Windows",
    "osProfile": {
        "adminUsername": "admin",
        "computerName": "examplevmname",
        "disablePasswordAuthentication": "true"
    },
    "osType": "linux",
    "placementGroupId": "f67c14ab-e92c-408c-ae2d-da15866ec79a",
    "plan": {
        "name": "planName",
        "product": "planProduct",
        "publisher": "planPublisher"
    },
    "platformFaultDomain": "36",
    "platformUpdateDomain": "42",
    "publicKeys": [{
            "keyData": "ssh-rsa 0",
            "path": "/home/user/.ssh/authorized_keys0"
        },
        {
            "keyData": "ssh-rsa 1",
            "path": "/home/user/.ssh/authorized_keys1"
        }
    ],
    "publisher": "RDFE-Test-Microsoft-Windows-Server-Group",
    "resourceGroupName": "macikgo-test-may-23",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/virtualMachines/examplevmname",
    "securityProfile": {
        "secureBootEnabled": "true",
        "virtualTpmEnabled": "false"
    },
    "sku": "Windows-Server-2012-R2-Datacenter",
    "storageProfile": {
        "dataDisks": [{
            "caching": "None",
            "createOption": "Empty",
            "diskSizeGB": "1024",
            "image": {
                "uri": ""
            },
            "lun": "0",
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampledatadiskname",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }],
        "imageReference": {
            "id": "",
            "offer": "UbuntuServer",
            "publisher": "Canonical",
            "sku": "16.04.0-LTS",
            "version": "latest"
        },
        "osDisk": {
            "caching": "ReadWrite",
            "createOption": "FromImage",
            "diskSizeGB": "30",
            "diffDiskSettings": {
                "option": "Local"
            },
            "encryptionSettings": {
                "enabled": "false"
            },
            "image": {
                "uri": ""
            },
            "managedDisk": {
                "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
                "storageAccountType": "Standard_LRS"
            },
            "name": "exampleosdiskname",
            "osType": "Linux",
            "vhd": {
                "uri": ""
            },
            "writeAcceleratorEnabled": "false"
        }
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "baz:bash;foo:bar",
    "version": "15.05.22",
    "vmId": "02aab8a4-74ef-476e-8182-f6d2ba4166a6",
    "vmScaleSetName": "crpteste9vflji9",
    "vmSize": "Standard_A3",
    "zone": ""
}
```

### <a name="sample-4-get-the-azure-environment-where-the-vm-is-running"></a>Przykład 4: uzyskiwanie środowiska platformy Azure, w którym jest uruchomiona maszyna wirtualna

Platforma Azure oferuje różne suwerenne chmury, takie jak [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Czasami konieczne jest środowisko platformy Azure, aby podejmować pewne decyzje dotyczące środowiska uruchomieniowego. Poniższy przykład pokazuje, jak można to osiągnąć.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Odpowiedź**

```text
AzurePublicCloud
```

Chmura i wartości środowiska platformy Azure są wymienione tutaj.

 Chmura   | Środowisko platformy Azure
---------|-----------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadane sieci 

Metadane sieciowe są częścią interfejsu API wystąpienia. Następujące kategorie sieci są dostępne za pomocą `instance/network` punktu końcowego.

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
IPv4/privateIpAddress | Lokalny adres IPv4 maszyny wirtualnej. | 2017-04-02
IPv4/publicIpAddress | Publiczny adres IPv4 maszyny wirtualnej. | 2017-04-02
podsieć/adres | Adres podsieci maszyny wirtualnej. | 2017-04-02
podsieć/prefiks | Prefiks podsieci. Przykład: 24 | 2017-04-02
IPv6/ipAddress | Lokalny adres IPv6 maszyny wirtualnej. | 2017-04-02
macAddress | Adres MAC maszyny wirtualnej. | 2017-04-02

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami JSON. Wszystkie poniższe przykładowe odpowiedzi są całkiem drukowane w celu zapewnienia czytelności.

#### <a name="sample-1-retrieve-network-information"></a>Przykład 1: pobieranie informacji o sieci

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/network?api-version=2017-08-01
```

**Odpowiedź**

```json
{
  "interface": [
    {
      "ipv4": {
        "ipAddress": [
          {
            "privateIpAddress": "10.1.0.4",
            "publicIpAddress": "X.X.X.X"
          }
        ],
        "subnet": [
          {
            "address": "10.1.0.0",
            "prefix": "24"
          }
        ]
      },
      "ipv6": {
        "ipAddress": []
      },
      "macAddress": "000D3AF806EC"
    }
  ]
}

```

#### <a name="sample-2-retrieve-public-ip-address"></a>Przykład 2: pobieranie publicznego adresu IP

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metadane magazynu

Metadane magazynu są częścią interfejsu API wystąpienia w `instance/compute/storageProfile` punkcie końcowym.
Zawiera szczegółowe informacje o dyskach magazynu skojarzonych z maszyną wirtualną. 

Profil magazynu maszyny wirtualnej jest podzielony na trzy kategorie: odwołanie do obrazu, dysk systemu operacyjnego i dyski z danymi.

Obiekt odwołanie do obrazu zawiera następujące informacje o obrazie systemu operacyjnego:

Dane    | Opis
--------|-----------------
identyfikator      | Identyfikator zasobu
offer   | Oferta platformy lub obrazu
publisher | Wydawca obrazu
sku     | Jednostka SKU obrazu
Wersja | Wersja platformy lub obrazu

Obiekt dysku systemu operacyjnego zawiera następujące informacje na temat dysku systemu operacyjnego używanego przez maszynę wirtualną:

Dane    | Opis
--------|-----------------
pamięć | Wymagania dotyczące buforowania
Opcja | Informacje na temat sposobu tworzenia maszyny wirtualnej
diffDiskSettings | Ustawienia dysku tymczasowych
diskSizeGB | Rozmiar dysku w GB
encryptionSettings | Ustawienia szyfrowania dysku
image (obraz)   | Wirtualny dysk twardy obrazu użytkownika źródłowego
managedDisk | Parametry dysku zarządzanego
name    | Nazwa dysku
osType  | Typ systemu operacyjnego znajdującego się na dysku
VHD     | Wirtualny dysk twardy
writeAcceleratorEnabled | Czy `writeAccelerator` jest włączona na dysku

Tablica dyski danych zawiera listę dysków danych podłączonych do maszyny wirtualnej. Każdy obiekt dysku danych zawiera następujące informacje:

Dane    | Opis
--------|-----------------
pamięć | Wymagania dotyczące buforowania
Opcja | Informacje na temat sposobu tworzenia maszyny wirtualnej
diffDiskSettings | Ustawienia dysku tymczasowych
diskSizeGB | Rozmiar dysku w GB
image (obraz)   | Wirtualny dysk twardy obrazu użytkownika źródłowego
tworzona     | Numer jednostki logicznej dysku
managedDisk | Parametry dysku zarządzanego
name    | Nazwa dysku
VHD     | Wirtualny dysk twardy
writeAcceleratorEnabled | Czy `writeAccelerator` jest włączona na dysku

Poniższy przykład pokazuje, jak zbadać informacje o magazynowaniu maszyny wirtualnej.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01
```

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

```json
{
    "dataDisks": [
      {
        "caching": "None",
        "createOption": "Empty",
        "diskSizeGB": "1024",
        "image": {
          "uri": ""
        },
        "lun": "0",
        "managedDisk": {
          "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampledatadiskname",
          "storageAccountType": "Standard_LRS"
        },
        "name": "exampledatadiskname",
        "vhd": {
          "uri": ""
        },
        "writeAcceleratorEnabled": "false"
      }
    ],
    "imageReference": {
      "id": "",
      "offer": "UbuntuServer",
      "publisher": "Canonical",
      "sku": "16.04.0-LTS",
      "version": "latest"
    },
    "osDisk": {
      "caching": "ReadWrite",
      "createOption": "FromImage",
      "diskSizeGB": "30",
      "diffDiskSettings": {
        "option": "Local"
      },
      "encryptionSettings": {
        "enabled": "false"
      },
      "image": {
        "uri": ""
      },
      "managedDisk": {
        "id": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/macikgo-test-may-23/providers/Microsoft.Compute/disks/exampleosdiskname",
        "storageAccountType": "Standard_LRS"
      },
      "name": "exampleosdiskname",
      "osType": "Linux",
      "vhd": {
        "uri": ""
      },
      "writeAcceleratorEnabled": "false"
    }
}
```

## <a name="vm-tags"></a>Tagi maszyn wirtualnych

Tagi maszyn wirtualnych są dołączone do interfejsu API wystąpienia w `instance/compute/tags` punkcie końcowym.
Tagi mogły zostać zastosowane do maszyny wirtualnej platformy Azure, aby logicznie zorganizować je w taksonomię. Tagi przypisane do maszyny wirtualnej można pobrać przy użyciu następującego żądania.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Odpowiedź**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags`Pole jest ciągiem zawierającym znaczniki rozdzielane średnikami. Ten wynik może być problemem, jeśli w samych tagach użyto średników. Jeśli parser jest Zapisano w celu programistycznego wyodrębnienia tagów, należy zastanowić się nad tym `tagsList` polem. `tagsList`Pole jest tablicą JSON bez ograniczników i w związku z tym jest łatwiej przeanalizować.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04
```

**Odpowiedź**

```json
[
  {
    "name": "Department",
    "value": "IT"
  },
  {
    "name": "Environment",
    "value": "Test"
  },
  {
    "name": "Role",
    "value": "WebRole"
  }
]
```

## <a name="attested-data"></a>Zaświadczone dane

IMDS pomaga zapewnić gwarancję, że dostarczone dane pochodzą z platformy Azure. Firma Microsoft podpisuje część tych informacji, więc można potwierdzić, że obraz w witrynie Azure Marketplace jest używany przez Ciebie na platformie Azure.

### <a name="sample-1-get-attested-data"></a>Przykład 1: pobieranie zaświadczania danych

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami JSON. Poniższe przykładowe odpowiedzi są dość drukowane w celu zapewnienia czytelności.

**Żądanie**

```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

> [!NOTE]
> Ze względu na mechanizm buforowania IMDS, można zwrócić wcześniej buforowaną `nonce` wartość.

`Api-version` jest polem obowiązkowym. Zapoznaj się z [sekcją użycie](#usage) dla obsługiwanych wersji interfejsu API.
`Nonce` jest opcjonalnym, 10-cyfrowym ciągiem. Jeśli nie jest podany, IMDS zwraca bieżący skoordynowany uniwersalny czas oczekiwania w miejscu.

**Odpowiedź**

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Obiekt BLOB sygnatury jest podpisaną przez [PKCS7](https://aka.ms/pkcs7)wersją dokumentu. Zawiera certyfikat używany do podpisywania, a także określone szczegóły dotyczące maszyn wirtualnych. 

W przypadku maszyn wirtualnych utworzonych za pomocą Azure Resource Manager obejmuje to,,,, `vmId` `sku` `nonce` `subscriptionId` `timeStamp` w celu utworzenia i wygaśnięcia dokumentu oraz planuje informacje o obrazie. Informacje o planie są wypełniane tylko dla obrazów w portalu Azure Marketplace. 

W przypadku maszyn wirtualnych utworzonych przy użyciu klasycznego modelu wdrażania `vmId` zagwarantowane jest tylko wypełnienie. Można wyodrębnić certyfikat z odpowiedzi i użyć go w celu potwierdzenia, że odpowiedź jest prawidłowa i pochodzi z platformy Azure.

Dokument zawiera następujące pola:

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
licenseType | Typ licencji dla [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Należy zauważyć, że ta funkcja jest obecna tylko w przypadku maszyn wirtualnych z obsługą AHB. | 2020-09-01
jednorazow | Ciąg, który może być opcjonalnie dostarczony z żądaniem. Jeśli nie `nonce` podano tego, jest używana bieżąca koordynowana sygnatura czasowa. | 2018-10-01
plan | [Plan obrazu portalu Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Zawiera identyfikator planu (nazwę), obraz produktu lub ofertę (produkt) oraz identyfikator wydawcy (wydawcy). | 2018-10-01
Sygnatura czasowa/createdOn | Uniwersalny czas koordynowany czasu dla tworzenia podpisanego dokumentu. | 2018-20-01
Sygnatura czasowa/expiresOn | Uniwersalny czas koordynowany czasu dla momentu wygaśnięcia podpisanego dokumentu. | 2018-10-01
vmId |  [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny wirtualnej. | 2018-10-01
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej. | 2019-04-30
sku | Określona jednostka SKU dla obrazu maszyny wirtualnej. | 2019-11-01

### <a name="sample-2-validate-that-the-vm-is-running-in-azure"></a>Przykład 2: Weryfikowanie, czy maszyna wirtualna jest uruchomiona na platformie Azure

Dostawcy w witrynie Azure Marketplace chcą mieć pewność, że ich oprogramowanie jest licencjonowane do uruchamiania tylko na platformie Azure. Jeśli ktoś skopiuje wirtualny dysk twardy do środowiska lokalnego, musi być w stanie wykryć ten plik. Dzięki IMDS te dostawcy mogą uzyskać podpisane dane, które gwarantują odpowiedź tylko z platformy Azure.

```powershell
# Get the signature
$attestedDoc = Invoke-RestMethod -Headers @{"Metadata"="true"} -Method GET -NoProxy -Uri http://169.254.169.254/metadata/attested/document?api-version=2020-09-01
# Decode the signature
$signature = [System.Convert]::FromBase64String($attestedDoc.signature)
```

Sprawdź, czy sygnatura pochodzi z Microsoft Azure i sprawdź, czy w łańcuchu certyfikatów występują błędy.

```powershell
# Get certificate chain
$cert = [System.Security.Cryptography.X509Certificates.X509Certificate2]($signature)
$chain = New-Object -TypeName System.Security.Cryptography.X509Certificates.X509Chain
$chain.Build($cert)
# Print the Subject of each certificate in the chain
foreach($element in $chain.ChainElements)
{
    Write-Host $element.Certificate.Subject
}

# Get the content of the signed document
Add-Type -AssemblyName System.Security
$signedCms = New-Object -TypeName System.Security.Cryptography.Pkcs.SignedCms
$signedCms.Decode($signature);
$content = [System.Text.Encoding]::UTF8.GetString($signedCms.ContentInfo.Content)
Write-Host "Attested data: " $content
$json = $content | ConvertFrom-Json
# Do additional validation here
```

> [!NOTE]
> Ze względu na mechanizm buforowania IMDS, można zwrócić wcześniej buforowaną `nonce` wartość.

`nonce`W polu w podpisanym dokumencie można porównać, jeśli podano `nonce` parametr w początkowym żądaniu.

> [!NOTE]
> Certyfikat dla chmury publicznej i każdej z nich jest inny.

Chmura | Certyfikat
------|------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/) | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *. metadata.azure.us
[Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *. metadata.azure.cn
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                | *. metadata.microsoftazure.de

> [!NOTE]
> Certyfikaty mogą nie być dokładnie zgodne z `metadata.azure.com` chmurą publiczną. Z tego powodu weryfikacja certyfikacji powinna zezwalać na wspólną nazwę z dowolnej `.metadata.azure.com` poddomeny.

W przypadkach, gdy nie można pobrać certyfikatu pośredniego z powodu ograniczeń sieci podczas weryfikacji, można przypiąć certyfikat pośredni. Należy pamiętać, że platforma Azure korzysta z certyfikatów, które są standardową zasadą infrastruktury kluczy publicznych. Należy zaktualizować przypięte certyfikaty, gdy nastąpi przerzucanie. Za każdym razem, gdy planowana jest zmiana aktualizacji certyfikatu pośredniego, blog platformy Azure zostanie zaktualizowany i klienci platformy Azure zostaną powiadomieni. 

Certyfikaty pośrednie można znaleźć w [repozytorium PKI](https://www.microsoft.com/pki/mscorp/cps/default.htm). Certyfikaty pośrednie dla każdego regionu mogą być różne.

> [!NOTE]
> Certyfikat pośredni dla produktu Azure Chiny 21Vianet pochodzi z globalnego głównego urzędu certyfikacji DigiCert zamiast Baltimore.
Jeśli przypięto certyfikaty pośrednie dla Chin platformy Azure w ramach zmiany urzędu łańcucha głównego, należy zaktualizować certyfikaty pośrednie.

## <a name="failover-clustering-in-windows-server"></a>Klaster trybu failover w systemie Windows Server

Podczas wykonywania zapytania dotyczącego usługi IMDS z klastrem trybu failover czasami konieczne jest dodanie trasy do tabeli routingu. Oto kroki tej procedury:

1. Otwórz wiersz polecenia z uprawnieniami administratora.

1. Uruchom następujące polecenie i Zanotuj adres interfejsu dla miejsca docelowego sieci ( `0.0.0.0` ) w tabeli tras IPv4.

```bat
route print
```

> [!NOTE]
> Następujące przykładowe dane wyjściowe pochodzą z maszyny wirtualnej z systemem Windows Server z włączoną funkcją klaster trybu failover. Dla uproszczenia dane wyjściowe zawierają tylko tabelę tras IPv4.

```text
IPv4 Route Table
===========================================================================
Active Routes:
Network Destination        Netmask          Gateway       Interface  Metric
          0.0.0.0          0.0.0.0         10.0.1.1        10.0.1.10    266
         10.0.1.0  255.255.255.192         On-link         10.0.1.10    266
        10.0.1.10  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.15  255.255.255.255         On-link         10.0.1.10    266
        10.0.1.63  255.255.255.255         On-link         10.0.1.10    266
        127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
        127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
  127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
      169.254.0.0      255.255.0.0         On-link     169.254.1.156    271
    169.254.1.156  255.255.255.255         On-link     169.254.1.156    271
  169.254.255.255  255.255.255.255         On-link     169.254.1.156    271
        224.0.0.0        240.0.0.0         On-link         127.0.0.1    331
        224.0.0.0        240.0.0.0         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

Uruchom następujące polecenie i użyj adresu interfejsu dla miejsca docelowego sieci ( `0.0.0.0` ), czyli ( `10.0.1.10` ) w tym przykładzie.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

## <a name="managed-identity"></a>Tożsamość zarządzana

Tożsamość zarządzana przypisana przez system można włączyć na maszynie wirtualnej. Do maszyny wirtualnej można także przypisać co najmniej jedną tożsamość zarządzaną przez użytkownika.
Następnie można zażądać tokenów dla tożsamości zarządzanych z IMDS. Te tokeny służą do uwierzytelniania za pomocą innych usług platformy Azure, takich jak Azure Key Vault.

Aby uzyskać szczegółowe instrukcje dotyczące włączania tej funkcji, zobacz [pozyskiwanie tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events"></a>Zaplanowane zdarzenia
Stan zaplanowanych zdarzeń można uzyskać za pomocą IMDS. Następnie użytkownik może określić zestaw akcji do uruchomienia w przypadku tych zdarzeń. Aby uzyskać więcej informacji, zobacz [zaplanowane zdarzenia](scheduled-events.md). 

## <a name="regional-availability"></a>Dostępność regionalna

Usługa jest ogólnie dostępna we wszystkich chmurach platformy Azure.

## <a name="sample-code-in-different-languages"></a>Przykładowy kod w różnych językach

W poniższej tabeli przedstawiono przykłady wywoływania IMDS przy użyciu różnych języków w ramach maszyny wirtualnej:

Język      | Przykład
--------------|----------------
C++ (Windows) | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Przejdź            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Visual Basic  | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb

## <a name="errors-and-debugging"></a>Błędy i debugowanie

Jeśli nie odnaleziono elementu danych lub nieprawidłowo sformułowane żądanie, IMDS zwraca standardowe błędy HTTP. Przykład:

Kod stanu HTTP | Przyczyna
-----------------|-------
200 OK |
400 Nieprawidłowe żądanie | Brak `Metadata: true` nagłówka lub brak parametru `format=json` podczas wykonywania zapytania o węzeł liścia.
404 — Nie znaleziono  | Żądany element nie istnieje.
Metoda 405 nie jest dozwolona | `GET`Obsługiwane są tylko żądania.
410 | Spróbuj ponownie za jakiś czas przez maksymalnie 70 sekund.
429 Zbyt wiele żądań | Interfejs API obsługuje obecnie maksymalnie 5 zapytań na sekundę.
Błąd usługi 500     | Spróbuj ponownie za jakiś czas.

### <a name="frequently-asked-questions"></a>Często zadawane pytania

**Pojawia się błąd `400 Bad Request, Required metadata header not specified` . Co to znaczy?**

IMDS wymaga, `Metadata: true` Aby nagłówek został przesłany w żądaniu. Przekazanie tego nagłówka w wywołaniu REST umożliwia dostęp do IMDS.

**Dlaczego nie otrzymuję informacji o obliczeniach dla mojej maszyny wirtualnej?**

Obecnie IMDS obsługuje tylko wystąpienia utworzone przy użyciu Azure Resource Manager.

**Moja maszyna wirtualna została utworzona przez Azure Resource Manager jakiś czas temu. Dlaczego nie widzę informacji o metadanych obliczeń?**

Jeśli maszyna wirtualna została utworzona po wrzesień 2016, Dodaj [tag](../../azure-resource-manager/management/tag-resources.md) , aby rozpocząć wyświetlanie metadanych obliczeniowych. Jeśli maszyna wirtualna została utworzona przed 2016 września, Dodaj lub usuń rozszerzenia lub dyski danych do wystąpienia maszyny wirtualnej w celu odświeżenia metadanych.

**Dlaczego nie widzę wszystkich danych wypełnionych dla nowej wersji?**

Jeśli maszyna wirtualna została utworzona po wrzesień 2016, Dodaj [tag](../../azure-resource-manager/management/tag-resources.md) , aby rozpocząć wyświetlanie metadanych obliczeniowych. Jeśli maszyna wirtualna została utworzona przed 2016 września, Dodaj lub usuń rozszerzenia lub dyski danych do wystąpienia maszyny wirtualnej w celu odświeżenia metadanych.

**Dlaczego otrzymuję błąd `500 Internal Server Error` lub `410 Resource Gone` ?**

Ponów żądanie. Aby uzyskać więcej informacji, zobacz [Obsługa błędów przejściowych](/azure/architecture/best-practices/transient-faults). Jeśli problem będzie nadal występować, Utwórz problem z pomocą techniczną w Azure Portal dla maszyny wirtualnej.

**Czy ta funkcja będzie działała dla wystąpień zestawu skalowania maszyn wirtualnych?**

Tak, IMDS jest dostępny dla wystąpień zestawu skalowania maszyn wirtualnych.

**Zaktualizowaliśmy moje Tagi w zestawach skalowania maszyn wirtualnych, ale nie są one wyświetlane w wystąpieniach (w przeciwieństwie do maszyn wirtualnych z pojedynczym wystąpieniem). Czy coś się nie robi?**

Obecnie Tagi dla zestawów skalowania maszyn wirtualnych są widoczne tylko dla maszyny wirtualnej po ponownym uruchomieniu, odniesieniu obrazu lub zmianie dysku na wystąpienie.

**Dlaczego upłynął limit czasu żądania dla mojego wywołania usługi?**

Wywołania metadanych muszą pochodzić z podstawowego adresu IP przypisanego do podstawowej karty sieciowej maszyny wirtualnej. Ponadto w przypadku zmiany tras w lokalnej tabeli routingu maszyny wirtualnej musi istnieć trasa dla adresu 169.254.169.254/32.
   * <details>
        <summary>Weryfikowanie tabeli routingu</summary>

        1. Zrzuć lokalną tabelę routingu i poszukaj wpisu IMDS. Przykład:
            ```console
            > route print
            IPv4 Route Table
            ===========================================================================
            Active Routes:
            Network Destination        Netmask          Gateway       Interface  Metric
                      0.0.0.0          0.0.0.0      172.16.69.1      172.16.69.7     10
                    127.0.0.0        255.0.0.0         On-link         127.0.0.1    331
                    127.0.0.1  255.255.255.255         On-link         127.0.0.1    331
              127.255.255.255  255.255.255.255         On-link         127.0.0.1    331
                168.63.129.16  255.255.255.255      172.16.69.1      172.16.69.7     11
              169.254.169.254  255.255.255.255      172.16.69.1      172.16.69.7     11
            ... (continues) ...
            ```
        1. Sprawdź, czy trasa istnieje dla `169.254.169.254` i zanotuj odpowiedni interfejs sieciowy (na przykład `172.16.69.7` ).
        1. Zrzuć konfigurację interfejsu i Znajdź interfejs, który odnosi się do tego, do którego odwołuje się tabela routingu, zwracając adres MAC (fizyczny).
            ```console
            > ipconfig /all
            ... (continues) ...
            Ethernet adapter Ethernet:

               Connection-specific DNS Suffix  . : xic3mnxjiefupcwr1mcs1rjiqa.cx.internal.cloudapp.net
               Description . . . . . . . . . . . : Microsoft Hyper-V Network Adapter
               Physical Address. . . . . . . . . : 00-0D-3A-E5-1C-C0
               DHCP Enabled. . . . . . . . . . . : Yes
               Autoconfiguration Enabled . . . . : Yes
               Link-local IPv6 Address . . . . . : fe80::3166:ce5a:2bd5:a6d1%3(Preferred)
               IPv4 Address. . . . . . . . . . . : 172.16.69.7(Preferred)
               Subnet Mask . . . . . . . . . . . : 255.255.255.0
            ... (continues) ...
            ```
        1. Upewnij się, że interfejs odpowiada podstawowej karcie sieciowej i podstawowemu adresowi IP maszyny wirtualnej. Podstawowa karta sieciowa i adres IP można znaleźć, przeglądając konfigurację sieci w Azure Portal lub sprawdzając ją przy użyciu interfejsu wiersza polecenia platformy Azure. Zwróć uwagę na publiczne i prywatne adresy IP (oraz adres MAC, jeśli używasz interfejsu wiersza polecenia). Oto przykład interfejsu wiersza polecenia programu PowerShell:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: wintest767 True 00-0D-3A-E5-1C-C0
            ```
        1. Jeśli nie są zgodne, zaktualizuj tabelę routingu, tak aby podstawowa karta sieciowa i adres IP były wskazywane.
    </details>

## <a name="support"></a>Pomoc techniczna

Jeśli nie możesz uzyskać odpowiedzi na metadane po wielu próbach, możesz utworzyć problem z pomocą techniczną w Azure Portal.
W obszarze **typ problemu** wybierz pozycję **Zarządzanie**. W obszarze **Kategoria** wybierz pozycję **instance Metadata Service**.

![Zrzut ekranu przedstawiający obsługę Instance Metadata Service](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Następne kroki

[Uzyskaj token dostępu dla maszyny wirtualnej](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md)

[Zaplanowane zdarzenia](scheduled-events.md)
