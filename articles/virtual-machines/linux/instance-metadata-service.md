---
title: Azure Instance Metadata Service
description: Dowiedz się więcej na temat Instance Metadata Service platformy Azure oraz informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych.
services: virtual-machines
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 04/29/2020
ms.author: sukumari
ms.reviewer: azmetadatadev
ms.openlocfilehash: 79a583575acc6e3b3b2551046d57355bbf74a663
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94966806"
---
# <a name="azure-instance-metadata-service-imds"></a>Instance Metadata Service platformy Azure (IMDS)

Usługa Azure Instance Metadata Service (IMDS) zawiera informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych i może służyć do zarządzania maszynami wirtualnymi i ich konfigurowania.
Te informacje obejmują jednostki SKU, magazyn, konfiguracje sieci i nadchodzące zdarzenia konserwacji. Pełną listę dostępnych danych można znaleźć w temacie [Metadata API](#metadata-apis).
Instance Metadata Service jest dostępny do uruchamiania wystąpień maszyny wirtualnej i zestawu skalowania maszyn wirtualnych. Wszystkie interfejsy API obsługują maszyny wirtualne utworzone/zarządzane przy użyciu [Azure Resource Manager](/rest/api/resources/). Tylko zaświadczone i sieciowe punkty końcowe obsługują klasyczne maszyny wirtualne (inne niż ARM) i zaświadczą, że tylko w ograniczonym zakresie.

IMDS platformy Azure to punkt końcowy REST, który jest dostępny w dobrze znanym adresie IP bez obsługi routingu ( `169.254.169.254` ), można uzyskać do niego dostęp tylko z poziomu maszyny wirtualnej. Komunikacja między maszyną wirtualną i IMDS nigdy nie opuszcza hosta.
Najlepszym rozwiązaniem jest, aby klienci HTTP pomijali serwery proxy sieci Web w ramach maszyny wirtualnej podczas wykonywania zapytań w IMDS i traktować `169.254.169.254` je tak samo jak [`168.63.129.16`](../../virtual-network/what-is-ip-address-168-63-129-16.md) .

## <a name="security"></a>Zabezpieczenia

Punkt końcowy Instance Metadata Service jest dostępny tylko w ramach uruchomionego wystąpienia maszyny wirtualnej w adresie IP bez obsługi routingu. Ponadto wszystkie żądania z `X-Forwarded-For` nagłówkiem są odrzucane przez usługę.
Żądania muszą również zawierać `Metadata: true` nagłówek, aby upewnić się, że rzeczywiste żądanie było bezpośrednio przeznaczone i nie jest częścią niezamierzonego przekierowania.

> [!IMPORTANT]
> Instance Metadata Service nie jest kanałem dla poufnych danych. Punkt końcowy jest otwarty dla wszystkich procesów na maszynie wirtualnej. Informacje uwidocznione za pomocą tej usługi powinny być traktowane jako informacje udostępnione wszystkim aplikacjom działającym w ramach maszyny wirtualnej.

## <a name="usage"></a>Użycie

### <a name="accessing-azure-instance-metadata-service"></a>Uzyskiwanie dostępu do usługi Azure Instance Metadata Service

Aby uzyskać dostęp do Instance Metadata Service, Utwórz maszynę wirtualną z poziomu [Azure Resource Manager](/rest/api/resources/) lub [Azure Portal](https://portal.azure.com)i postępuj zgodnie z poniższymi przykładami.
Więcej przykładów dotyczących zapytań IMDS można znaleźć w przykładach [metadanych wystąpienia platformy Azure](https://github.com/microsoft/azureimds).

Poniżej znajduje się przykładowy kod służący do pobierania wszystkich metadanych wystąpienia, aby uzyskać dostęp do określonego źródła danych, zobacz sekcję [API metadanych](#metadata-apis) . 

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2020-09-01"
```

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

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

Domyślnie Instance Metadata Service zwraca dane w formacie JSON ( `Content-Type: application/json` ). Niektóre interfejsy API mogą jednak zwracać dane w różnych formatach, jeśli jest to wymagane.
Poniższa tabela zawiera odwołania do innych formatów danych, które mogą być obsługiwane przez interfejsy API.

Interfejs API | Domyślny format danych | Inne formaty
--------|---------------------|--------------
/attested | json | brak
/Identity | json | brak
/instance | json | tekst
/scheduledevents | json | brak

Aby uzyskać dostęp do formatu niedomyślnej odpowiedzi, należy określić żądany format jako parametr ciągu zapytania w żądaniu. Przykład:

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> W przypadku węzłów liści w/Metadata/instance `format=json` nie działa. Dla tych zapytań `format=text` należy jawnie określić, ponieważ format domyślny to JSON.

### <a name="versioning"></a>Przechowywanie wersji

Instance Metadata Service ma wersję, a określenie wersji interfejsu API w żądaniu HTTP jest obowiązkowe.

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
> Wersja 2020-10-01 jest obecnie wdrażana i może nie być jeszcze dostępna w każdym regionie.

W miarę dodawania nowszych wersji nadal można uzyskać dostęp do starszych wersji, jeśli skrypty są zależne od określonych formatów danych.

Jeśli żadna wersja nie zostanie określona, zostanie zwrócony błąd z listą najnowszych obsługiwanych wersji.

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy przykład wskazuje stan błędu, gdy wersja nie jest określona, odpowiedź jest całkiem wydrukowana pod kątem czytelności.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance"
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

Metadata Service zawiera wiele interfejsów API reprezentujących różne źródła danych.

Interfejs API | Opis | Wprowadzona wersja
----|-------------|-----------------------
/attested | Zobacz [zaświadczone dane](#attested-data) | 2018-10-01
/Identity | Zobacz [pozyskiwanie tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
/instance | Zobacz [interfejs API wystąpienia](#instance-api) | 2017-04-02
/scheduledevents | Zobacz [Scheduled Events](scheduled-events.md) | 2017-08-01

## <a name="instance-api"></a>Interfejs API wystąpienia

Interfejs API wystąpienia uwidacznia ważne metadane dla wystąpień maszyn wirtualnych, w tym maszyn wirtualnych, sieci i magazynu. Dostęp do następujących kategorii można uzyskać za pomocą wystąpienia/obliczenia:

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
azEnvironment | Środowisko platformy Azure, w którym jest uruchomiona maszyna wirtualna | 2018-10-01
customData | Ta funkcja jest obecnie wyłączona. Ta dokumentacja zostanie zaktualizowana, gdy zostanie udostępniona | 2019-02-01
isHostCompatibilityLayerVm | Określa, czy maszyna wirtualna jest uruchamiana na warstwie zgodności hosta | 2020-06-01
licenseType | Typ licencji dla [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Należy zauważyć, że ta funkcja jest obecna tylko w przypadku maszyn wirtualnych z obsługą AHB | 2020-09-01
location | Region platformy Azure, w którym działa maszyna wirtualna | 2017-04-02
name | Nazwa maszyny wirtualnej | 2017-04-02
offer | Informacje o ofercie dla obrazu maszyny wirtualnej i dostępne tylko dla obrazów wdrożonych z galerii obrazów platformy Azure | 2017-04-02
osProfile.adminUsername | Określa nazwę konta administratora | 2020-07-15
osProfile. computerName | Określa nazwę komputera | 2020-07-15
osProfile.disablePasswordAuthentication | Określa, czy uwierzytelnianie hasła jest wyłączone. Należy zauważyć, że jest to obecne tylko w przypadku maszyn wirtualnych z systemem Linux | 2020-10-01
osType | System Linux lub Windows | 2017-04-02
placementGroupId | [Grupa umieszczania](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) zestawu skalowania maszyn wirtualnych | 2017-08-01
plan | [Planowanie](/rest/api/compute/virtualmachines/createorupdate#plan) zawierające nazwę, produkt i wydawcę maszyny wirtualnej, jeśli jest to obraz portalu Azure Marketplace | 2018-04-02
platformUpdateDomain |  [Aktualizuj domenę](../manage-availability.md) , w której działa maszyna wirtualna | 2017-04-02
platformFaultDomain | [Domena błędów](../manage-availability.md) , w której działa maszyna wirtualna | 2017-04-02
dostawcy | Dostawca maszyny wirtualnej | 2018-10-01
publicKeys | [Kolekcja kluczy publicznych](/rest/api/compute/virtualmachines/createorupdate#sshpublickey) przypisanych do maszyny wirtualnej i ścieżek | 2018-04-02
publisher | Wydawca obrazu maszyny wirtualnej | 2017-04-02
resourceGroupName | [Grupa zasobów](../../azure-resource-manager/management/overview.md) dla maszyny wirtualnej | 2017-08-01
resourceId | W [pełni kwalifikowany](/rest/api/resources/resources/getbyid) identyfikator zasobu | 2019-03-11
sku | Określona jednostka SKU dla obrazu maszyny wirtualnej | 2017-04-02
securityProfile. secureBootEnabled | Określa, czy na maszynie wirtualnej jest włączony bezpieczny rozruch z interfejsem UEFI | 2020-06-01
securityProfile.virtualTpmEnabled | Określa, czy wirtualny moduł TPM (TPM) jest włączony na maszynie wirtualnej | 2020-06-01
Obszarze storageprofile | Zobacz [profil magazynu](#storage-metadata) | 2019-06-01
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej | 2017-08-01
tags | [Tagi](../../azure-resource-manager/management/tag-resources.md) dla maszyny wirtualnej  | 2017-08-01
tagsList | Tagi sformatowane jako tablica JSON dla łatwiejszego analizowania programistycznego  | 2019-06-04
Wersja | Wersja obrazu maszyny wirtualnej | 2017-04-02
vmId | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny wirtualnej | 2017-04-02
vmScaleSetName | [Nazwa zestawu skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/overview.md) w zestawie skalowania maszyn wirtualnych | 2017-12-01
vmSize | [Rozmiar maszyny wirtualnej](../sizes.md) | 2017-04-02
strefa | [Strefa dostępności](../../availability-zones/az-overview.md) maszyny wirtualnej | 2017-12-01

### <a name="sample-1-tracking-vm-running-on-azure"></a>Przykład 1: śledzenie maszyny wirtualnej działającej na platformie Azure

Jako usługodawca może być konieczne śledzenie liczby maszyn wirtualnych korzystających z oprogramowania lub agentów, którzy muszą śledzić unikatowość maszyny wirtualnej. Aby można było uzyskać unikatowy identyfikator dla maszyny wirtualnej, użyj `vmId` pola z instance Metadata Service.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Odpowiedź**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="sample-2-placement-of-containers-data-partitions-based-faultupdate-domain"></a>Przykład 2: umieszczanie kontenerów, oparte na partycjach danych domena błędów/aktualizacji

W niektórych scenariuszach umieszczanie różnych replik danych ma podstawowe znaczenie. Na przykład [umieszczenie repliki](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) systemu plików HDFS lub umieszczenie kontenera za pośrednictwem programu [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać znajomości, `platformFaultDomain` a `platformUpdateDomain` maszyna wirtualna jest uruchomiona.
Można również użyć [strefy dostępności](../../availability-zones/az-overview.md) , aby wystąpienia mogły podejmować te decyzje.
Możesz wysyłać zapytania o te dane bezpośrednio za pośrednictwem Instance Metadata Service.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Odpowiedź**

```text
0
```

### <a name="sample-3-getting-more-information-about-the-vm-during-support-case"></a>Przykład 3: uzyskiwanie dodatkowych informacji o maszynie wirtualnej w przypadku pomocy technicznej

Jako dostawca usług możesz uzyskać informacje o pomocy technicznej, w których chcesz poznać więcej informacji o maszynie wirtualnej. Prośba o udostępnienie metadanych obliczeniowych przez klienta może stanowić podstawowe informacje dla specjalistów pomocy technicznej dotyczące rodzaju maszyny wirtualnej na platformie Azure.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute?api-version=2020-09-01"
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

### <a name="sample-4-getting-azure-environment-where-the-vm-is-running"></a>Przykład 4: uzyskiwanie środowiska platformy Azure, w którym jest uruchomiona maszyna wirtualna

Platforma Azure ma rozmaite suwerenne chmury, takie jak [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Czasami konieczne jest środowisko platformy Azure, aby podejmować pewne decyzje dotyczące środowiska uruchomieniowego. Poniższy przykład pokazuje, jak można to osiągnąć.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Odpowiedź**

```text
AzurePublicCloud
```

Poniżej przedstawiono chmurę i wartości środowiska platformy Azure.

 Chmura   | Środowisko platformy Azure
---------|-----------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | AzureChinaCloud
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

## <a name="network-metadata"></a>Metadane sieci 

Metadane sieciowe są częścią interfejsu API wystąpienia. Następujące kategorie sieci są dostępne za pomocą punktu końcowego wystąpienia/sieci.

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
IPv4/privateIpAddress | Lokalny adres IPv4 maszyny wirtualnej | 2017-04-02
IPv4/publicIpAddress | Publiczny adres IPv4 maszyny wirtualnej | 2017-04-02
podsieć/adres | Adres podsieci maszyny wirtualnej | 2017-04-02
podsieć/prefiks | Prefiks podsieci, przykład 24 | 2017-04-02
IPv6/ipAddress | Lokalny adres IPv6 maszyny wirtualnej | 2017-04-02
macAddress | Adres MAC maszyny wirtualnej | 2017-04-02

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami JSON. Wszystkie poniższe przykładowe odpowiedzi są całkiem drukowane w celu zapewnienia czytelności.

#### <a name="sample-1-retrieving-network-information"></a>Przykład 1: pobieranie informacji o sieci

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

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

#### <a name="sample-2-retrieving-public-ip-address"></a>Przykład 2: pobieranie publicznego adresu IP

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

## <a name="storage-metadata"></a>Metadane magazynu

Metadane magazynu są częścią interfejsu API wystąpienia w punkcie końcowym wystąpienie/Obliczanie/obszarze storageprofile.
Zawiera szczegółowe informacje o dyskach magazynu skojarzonych z maszyną wirtualną. 

Profil magazynu maszyny wirtualnej jest podzielony na trzy kategorie: odwołanie do obrazu, dysk systemu operacyjnego i dyski z danymi.

Obiekt odwołanie do obrazu zawiera następujące informacje o obrazie systemu operacyjnego:

Dane    | Opis
--------|-----------------
identyfikator      | Identyfikator zasobu
offer   | Oferta platformy lub obrazu z witryny Marketplace
publisher | Wydawca obrazu
sku     | Jednostka SKU obrazu
Wersja | Wersja platformy lub obrazu witryny Marketplace

Obiekt dysku systemu operacyjnego zawiera następujące informacje na temat dysku systemu operacyjnego używanego przez maszynę wirtualną:

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
writeAcceleratorEnabled | Czy writeAccelerator jest włączona na dysku

Tablica dyski danych zawiera listę dysków danych podłączonych do maszyny wirtualnej. Każdy obiekt dysku danych zawiera następujące informacje:

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
writeAcceleratorEnabled | Czy writeAccelerator jest włączona na dysku

Poniższy przykład pokazuje, jak zbadać informacje o magazynowaniu maszyny wirtualnej.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
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

Tagi maszyn wirtualnych są dołączone do interfejsu API wystąpienia w punkcie końcowym wystąpienia/obliczenia/Tagi.
Tagi mogły zostać zastosowane do maszyny wirtualnej platformy Azure, aby logicznie zorganizować je w taksonomię. Tagi przypisane do maszyny wirtualnej można pobrać przy użyciu poniższego żądania.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Odpowiedź**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags`Pole jest ciągiem zawierającym znaczniki rozdzielane średnikami. Ten wynik może być problemem, jeśli w samych tagach użyto średników. Jeśli parser jest Zapisano w celu programistycznego wyodrębnienia tagów, należy zastanowić się nad tym `tagsList` polem. `tagsList`Pole jest tablicą JSON bez ograniczników i w związku z tym ułatwia analizowanie.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04"
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

Częścią scenariusza obsługiwanego przez Instance Metadata Service jest zapewnienie gwarancji, że dostarczone dane pochodzą z platformy Azure. Firma Microsoft rejestruje część tych informacji, aby obrazy z portalu Marketplace mogły mieć pewność, że jest to obraz działający na platformie Azure.

### <a name="sample-1-getting-attested-data"></a>Przykład 1: pobieranie zaświadczania danych

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami JSON. Poniższe przykładowe odpowiedzi są dość drukowane w celu zapewnienia czytelności.

**Żądanie**

```bash
curl -H Metadata:true --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"
```

Interfejs API-Version jest polem obowiązkowym. Zapoznaj się z [sekcją użycie](#usage) dla obsługiwanych wersji interfejsu API.
Identyfikator jednorazowy jest opcjonalnym 10-cyfrowym ciągiem. Jeśli nie zostanie podany, IMDS zwraca bieżącą sygnaturę czasową UTC w swoim miejscu.

> [!NOTE]
> Ze względu na mechanizm buforowania IMDS, można zwrócić wcześniej buforowaną wartość nonce.

**Odpowiedź**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Obiekt BLOB sygnatury jest podpisanym przez [PKCS7](https://aka.ms/pkcs7) wersją dokumentu. Zawiera certyfikat używany do podpisywania wraz z konkretnymi szczegółami dotyczącymi maszyn wirtualnych. W przypadku maszyn wirtualnych ARM obejmuje to identyfikator maszyny wirtualnej, jednostkę SKU, nonce, Identyfikator subskrypcji, sygnaturę czasową do utworzenia i wygaśnięcia dokumentu oraz informacje o planie obrazu. Informacje o planie są wypełniane tylko dla obrazów w portalu Azure Marketplace. W przypadku maszyn wirtualnych z systemem klasycznym (innym niż ARM) należy wypełnić tylko identyfikator maszyny wirtualnej. Certyfikat może zostać wyodrębniony z odpowiedzi i użyty do zweryfikowania, że odpowiedź jest prawidłowa i pochodzi z platformy Azure.
Dokument zawiera następujące pola:

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
licenseType | Typ licencji dla [korzyść użycia hybrydowego platformy Azure](https://azure.microsoft.com/pricing/hybrid-benefit). Należy zauważyć, że ta funkcja jest obecna tylko w przypadku maszyn wirtualnych z obsługą AHB | 2020-09-01
jednorazow | Ciąg, który może być opcjonalnie dostarczony z żądaniem. Jeśli nie podano żadnego identyfikatora, zostanie użyta bieżąca sygnatura czasowa UTC | 2018-10-01
plan | [Plan obrazu portalu Azure Marketplace](/rest/api/compute/virtualmachines/createorupdate#plan). Zawiera identyfikator planu (nazwę), obraz produktu lub ofertę (produkt) oraz identyfikator wydawcy (wydawcy). | 2018-10-01
Sygnatura czasowa/createdOn | Sygnatura czasowa UTC dla momentu utworzenia podpisanego dokumentu | 2018-20-01
Sygnatura czasowa/expiresOn | Sygnatura czasowa UTC dla momentu wygaśnięcia podpisanego dokumentu | 2018-10-01
vmId |  [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny wirtualnej | 2018-10-01
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej | 2019-04-30
sku | Określona jednostka SKU dla obrazu maszyny wirtualnej | 2019-11-01

> [!NOTE]
> W przypadku maszyn wirtualnych z systemem klasycznym (innym niż ARM) należy wypełnić tylko identyfikator maszyny wirtualnej.

### <a name="sample-2-validating-that-the-vm-is-running-in-azure"></a>Przykład 2: Weryfikowanie, czy maszyna wirtualna jest uruchomiona na platformie Azure

Dostawcy portalu Marketplace chcą mieć pewność, że ich oprogramowanie jest licencjonowane do uruchamiania tylko na platformie Azure. Jeśli ktoś kopiuje dysk VHD do lokalnego, powinien on mieć możliwość wykrycia tej funkcji. Dzwoniąc do Instance Metadata Service, dostawcy portalu Marketplace mogą uzyskać podpisane dane, które gwarantują odpowiedź tylko z platformy Azure.

> [!NOTE]
> Wymaga zainstalowania JQ.

**Żądanie**

```bash
# Get the signature
curl --silent -H Metadata:True --noproxy "*" "http://169.254.169.254/metadata/attested/document?api-version=2020-09-01" | jq -r '.["signature"]' > signature
# Decode the signature
base64 -d signature > decodedsignature
# Get PKCS7 format
openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
# Get Public key out of pkc7
openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
# Get the intermediate certificate
curl -s -o intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
openssl x509 -inform der -in intermediate.cer -out intermediate.pem
# Verify the contents
openssl smime -verify -in sign.pk7 -inform pem -noverify
```

**Odpowiedź**

```json
Verification successful
{
  "nonce": "20181128-001617",
  "plan":
    {
      "name": "",
      "product": "",
      "publisher": ""
    },
  "timeStamp":
    {
      "createdOn": "11/28/18 00:16:17 -0000",
      "expiresOn": "11/28/18 06:16:17 -0000"
    },
  "vmId": "d3e0e374-fda6-4649-bbc9-7f20dc379f34",
  "licenseType": "Windows_Client",  
  "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
  "sku": "RS3-Pro"
}
```

Sprawdź, czy sygnatura pochodzi z Microsoft Azure i sprawdź, czy w łańcuchu certyfikatów występują błędy.

```bash
# Verify the subject name for the main certificate
openssl x509 -noout -subject -in signer.pem
# Verify the issuer for the main certificate
openssl x509 -noout -issuer -in signer.pem
#Validate the subject name for intermediate certificate
openssl x509 -noout -subject -in intermediate.pem
# Verify the issuer for the intermediate certificate
openssl x509 -noout -issuer -in intermediate.pem
# Verify the certificate chain, for Azure China 21Vianet the intermediate certificate will be from DigiCert Global Root CA
openssl verify -verbose -CAfile /etc/ssl/certs/Baltimore_CyberTrust_Root.pem -untrusted intermediate.pem signer.pem
```

> [!NOTE]
> Ze względu na mechanizm buforowania IMDS, można zwrócić wcześniej buforowaną wartość nonce.

Identyfikator jednorazowy w podpisanym dokumencie można porównać, jeśli podano parametr nonce w początkowym żądaniu.

> [!NOTE]
> Certyfikat dla chmury publicznej i suwerennej chmury będzie różny.

Chmura | Certyfikat
------|------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/) | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)          | *. metadata.azure.us
[Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)     | *. metadata.azure.cn
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                | *. metadata.microsoftazure.de

> [!NOTE]
> Istnieje znany problem związany z certyfikatem używanym do podpisywania. Certyfikaty mogą nie mieć dokładnego dopasowania `metadata.azure.com` dla chmury publicznej. W związku z tym weryfikacja certyfikacji powinna zezwalać na wspólną nazwę z dowolnej `.metadata.azure.com` poddomeny.

W przypadkach, gdy nie można pobrać certyfikatu pośredniego z powodu ograniczeń sieci podczas weryfikacji, certyfikat pośredni może zostać przypięty. Jednak platforma Azure przywróci certyfikaty zgodnie z zasadami standardowej infrastruktury kluczy publicznych. Przypięte certyfikaty należy zaktualizować, gdy nastąpi przerzucanie. Za każdym razem, gdy planowana jest zmiana aktualizacji certyfikatu pośredniego, blog platformy Azure zostanie zaktualizowany i klienci platformy Azure zostaną powiadomieni. Certyfikaty pośrednie można znaleźć [tutaj](https://www.microsoft.com/pki/mscorp/cps/default.htm). Certyfikaty pośrednie dla każdego regionu mogą być różne.

> [!NOTE]
> Certyfikat pośredni dla usług Azure Chiny dla Chin będzie z DigiCert globalnego głównego urzędu certyfikacji, a nie Baltimore.
Ponadto, jeśli przypięto certyfikaty pośrednie dla Chin platformy Azure w ramach zmiany urzędu łańcucha głównego, certyfikaty pośrednie będą musiały zostać zaktualizowane.

## <a name="managed-identity-via-metadata-service"></a>Tożsamość zarządzana za pośrednictwem Metadata Service

Tożsamość zarządzaną przypisaną przez system można włączyć na maszynie wirtualnej lub do maszyny wirtualnej można przypisać co najmniej jedną tożsamość zarządzaną przez użytkownika.
Tokeny zarządzanych tożsamości można następnie żądać od Instance Metadata Service. Tokeny te mogą służyć do uwierzytelniania za pomocą innych usług platformy Azure, takich jak Azure Key Vault.

Aby uzyskać szczegółowe instrukcje dotyczące włączania tej funkcji, zobacz [pozyskiwanie tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).

## <a name="scheduled-events-via-metadata-service"></a>Scheduled Events za pośrednictwem Metadata Service
Możesz uzyskać stan zaplanowanych zdarzeń za pośrednictwem usługi metadanych, a następnie użytkownik może określić zestaw akcji do wykonania w przypadku tych zdarzeń.  Aby uzyskać szczegółowe informacje, zobacz [Scheduled Events](scheduled-events.md) . 

## <a name="regional-availability"></a>Dostępność regionalna

Usługa jest **ogólnie dostępna** we wszystkich chmurach platformy Azure.

## <a name="sample-code-in-different-languages"></a>Przykładowy kod w różnych językach

Przykłady wywoływania usługi metadanych przy użyciu różnych języków w ramach maszyny wirtualnej:

Język      | Przykład
--------------|----------------
Bash          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
C#            | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
Przejdź            | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Java          | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
NodeJS        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
Perl          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
PowerShell    | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Puppet        | https://github.com/keirans/azuremetadata
Python        | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
Ruby          | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb

## <a name="error-and-debugging"></a>Błąd i debugowanie

Jeśli nie odnaleziono elementu danych lub nieprawidłowo sformułowane żądanie, Instance Metadata Service zwraca standardowe błędy HTTP. Przykład:

Kod stanu HTTP | Przyczyna
----------------|-------
200 OK |
400 Nieprawidłowe żądanie | Brak `Metadata: true` nagłówka lub brak parametru `format=json` podczas wykonywania zapytania o węzeł liścia
404 — Nie znaleziono | Żądany element nie istnieje
Metoda 405 nie jest dozwolona | `GET`Obsługiwane są tylko żądania
410 | Spróbuj ponownie za jakiś czas przez maksymalnie 70 sekund
429 Zbyt wiele żądań | Interfejs API obsługuje obecnie maksymalnie 5 zapytań na sekundę.
Błąd usługi 500     | Ponów próbę za jakiś czas

### <a name="known-issues-and-faq"></a>Znane problemy i często zadawane pytania

1. Pojawia się błąd `400 Bad Request, Required metadata header not specified` . Co to oznacza?
   * Instance Metadata Service wymaga `Metadata: true` przekazanie nagłówka w żądaniu. Przekazanie tego nagłówka w wywołaniu REST umożliwia dostęp do Instance Metadata Service.
1. Dlaczego nie otrzymuję informacji o obliczeniach dla mojej maszyny wirtualnej?
   * Obecnie Instance Metadata Service obsługuje tylko wystąpienia utworzone przy użyciu Azure Resource Manager.
1. Moja maszyna wirtualna została utworzona przez Azure Resource Manager a. Dlaczego nie widzę informacji o metadanych obliczeń?
   * Dla wszystkich maszyn wirtualnych utworzonych po SEP 2016 Dodaj [tag](../../azure-resource-manager/management/tag-resources.md) , aby rozpocząć wyświetlanie metadanych obliczeniowych. W przypadku starszych maszyn wirtualnych (utworzonych przed SEP 2016) Dodaj/Usuń rozszerzenia lub dyski danych do wystąpień maszyn wirtualnych w celu odświeżenia metadanych.
1. Nie widzę wszystkich danych wypełnionych dla nowej wersji
   * Dla wszystkich maszyn wirtualnych utworzonych po SEP 2016 Dodaj [tag](../../azure-resource-manager/management/tag-resources.md) , aby rozpocząć wyświetlanie metadanych obliczeniowych. W przypadku starszych maszyn wirtualnych (utworzonych przed SEP 2016) Dodaj/Usuń rozszerzenia lub dyski danych do wystąpień maszyn wirtualnych w celu odświeżenia metadanych.
1. Dlaczego otrzymuję błąd `500 Internal Server Error` lub `410 Resource Gone` ?
   * Ponów żądanie w oparciu o wykładniczy system wycofywania lub inne metody opisane w temacie [przejściowa obsługa błędów](/azure/architecture/best-practices/transient-faults). Jeśli problem będzie nadal występować, Utwórz problem z pomocą techniczną w Azure Portal dla maszyny wirtualnej.
1. Czy ta funkcja będzie działała dla wystąpień zestawu skalowania maszyn wirtualnych?
   * Tak, aby usługa metadanych była dostępna dla wystąpień zestawu skalowania.
1. Moje Tagi zostały zaktualizowane w Virtual Machine Scale Sets ale nie pojawiają się w wystąpieniach, w przeciwieństwie do maszyn wirtualnych z pojedynczym wystąpieniem?
   * Obecnie Tagi dla zestawów skalowania są widoczne tylko dla maszyny wirtualnej po ponownym uruchomieniu, odniesieniu obrazu lub zmianie dysku na wystąpienie.
1. Upłynął limit czasu żądania dla wywołania usługi?
   * Wywołania metadanych muszą pochodzić z podstawowego adresu IP przypisanego do podstawowej karty sieciowej maszyny wirtualnej. Ponadto w przypadku zmiany tras w lokalnej tabeli routingu maszyny wirtualnej musi istnieć trasa dla adresu 169.254.169.254/32.
   * <details>
        <summary>Weryfikowanie tabeli routingu</summary>

        1. Zrzuć lokalną tabelę routingu za pomocą polecenia, takiego jak `netstat -r` i Wyszukaj wpis IMDS (np.):
            ```console
            ~$ netstat -r
            Kernel IP routing table
            Destination     Gateway         Genmask         Flags   MSS Window  irtt Iface
            default         _gateway        0.0.0.0         UG        0 0          0 eth0
            168.63.129.16   _gateway        255.255.255.255 UGH       0 0          0 eth0
            169.254.169.254 _gateway        255.255.255.255 UGH       0 0          0 eth0
            172.16.69.0     0.0.0.0         255.255.255.0   U         0 0          0 eth0
            ```
        1. Sprawdź, czy trasa istnieje dla `169.254.169.254` i zanotuj odpowiedni interfejs sieciowy (np. `eth0` ).
        1. Zrzuć konfigurację interfejsu dla odpowiedniego interfejsu w tabeli routingu (Zwróć uwagę na to, że dokładna nazwa pliku konfiguracji może się różnić)
            ```console
            ~$ cat /etc/netplan/50-cloud-init.yaml
            network:
            ethernets:
                eth0:
                    dhcp4: true
                    dhcp4-overrides:
                        route-metric: 100
                    dhcp6: false
                    match:
                        macaddress: 00:0d:3a:e4:c7:2e
                    set-name: eth0
            version: 2
            ```
        1. Jeśli używasz dynamicznego adresu IP, Zanotuj adres MAC. Jeśli używasz statycznego adresu IP, możesz zanotować wymienione adresy IP i/lub adres MAC.
        1. Upewnij się, że interfejs odpowiada podstawowej karcie sieciowej i podstawowemu adresowi IP maszyny wirtualnej. Podstawowa karta sieciowa/adres IP można znaleźć, przeglądając konfigurację sieci w Azure Portal lub sprawdzając ją [przy użyciu interfejsu wiersza polecenia platformy Azure](/cli/azure/vm/nic?view=azure-cli-latest#az-vm-nic-show). Zwróć uwagę na publiczne i prywatne adresy IP (oraz adres MAC, jeśli jest używany interfejs wiersza polecenia). Przykład interfejsu wiersza polecenia programu PowerShell:
            ```powershell
            $ResourceGroup = '<Resource_Group>'
            $VmName = '<VM_Name>'
            $NicNames = az vm nic list --resource-group $ResourceGroup --vm-name $VmName | ConvertFrom-Json | Foreach-Object { $_.id.Split('/')[-1] }
            foreach($NicName in $NicNames)
            {
                $Nic = az vm nic show --resource-group $ResourceGroup --vm-name $VmName --nic $NicName | ConvertFrom-Json
                Write-Host $NicName, $Nic.primary, $Nic.macAddress
            }
            # Output: ipexample606 True 00-0D-3A-E4-C7-2E
            ```
        1. Jeśli nie są zgodne, zaktualizuj tabelę routingu w taki sposób, aby podstawowa karta sieciowa/adres IP były wskazywane.
    </details>

## <a name="support-and-feedback"></a>Pomoc techniczna i opinie

Prześlij swoją opinię i komentarze https://feedback.azure.com .

Aby uzyskać pomoc techniczną dotyczącą usługi, Utwórz problem pomocy technicznej w Azure Portal dla maszyny wirtualnej, na której nie można uzyskać odpowiedzi na metadane po długotrwałych próbach.
Użyj typu problemu `Management` i wybierz `Instance Metadata Service` jako kategorię.

![Obsługa metadanych wystąpienia](./media/instance-metadata-service/InstanceMetadata-support.png "Zrzut ekranu: Otwieranie przypadku pomocy technicznej w przypadku problemów z usługą Instance Metadata Service")

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej:
1. [Uzyskaj token dostępu dla maszyny wirtualnej](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md).
1. [Scheduled Events](scheduled-events.md)