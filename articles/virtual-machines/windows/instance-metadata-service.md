---
title: Instance Metadata Service platformy Azure
description: Interfejs RESTful, aby uzyskać informacje na temat obliczeniowych, sieciowych i nadchodzących zdarzeń konserwacyjnych maszyn wirtualnych.
author: KumariSupriya
manager: paulmey
ms.service: virtual-machines
ms.subservice: monitoring
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 03/30/2020
ms.author: sukumari
ms.reviewer: azmetadata
ms.openlocfilehash: cb9453e1a25f4042c45d4e89229b555c996d4c8b
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81870081"
---
# <a name="azure-instance-metadata-service"></a>Usługa metadanych wystąpienia platformy Azure

Usługa Azure Instance Metadata Service (IMDS) zawiera informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych i może służyć do zarządzania maszynami wirtualnymi i ich konfigurowania.
Podane informacje obejmują jednostki SKU, konfigurację sieci i nadchodzące zdarzenia konserwacji. Pełną listę dostępnych danych można znaleźć w temacie [Metadata API](#metadata-apis).

Instance Metadata Service platformy Azure to punkt końcowy REST dostępny dla wszystkich maszyn wirtualnych IaaS utworzonych za pośrednictwem [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/).
Punkt końcowy jest dostępny w dobrze znanym adresie IP bez obsługi routingu (`169.254.169.254`), do którego można uzyskać dostęp tylko z poziomu maszyny wirtualnej.

> [!IMPORTANT]
> Ta usługa jest **ogólnie dostępna** we wszystkich regionach świadczenia usługi Azure.  Regularnie otrzymuje aktualizacje, aby uwidocznić nowe informacje o wystąpieniach maszyn wirtualnych. Ta strona przedstawia aktualne dostępne [interfejsy API metadanych](#metadata-apis) .

## <a name="service-availability"></a>Dostępność usługi

Usługa jest dostępna w ogólnie dostępnych regionach platformy Azure. Nie wszystkie wersje interfejsu API mogą być dostępne we wszystkich regionach świadczenia usługi Azure.

Regiony                                        | Opóźnienie?                                 | Obsługiwane wersje
-----------------------------------------------|-----------------------------------------------|-----------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | Ogólnie dostępna | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Ogólnie dostępna | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure w Chinach — 21Vianet](https://www.azure.cn/)                                            | Ogólnie dostępna | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | Ogólnie dostępna | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15

Wersja 2019-11-01 jest obecnie wdrażana i może nie być dostępna we wszystkich regionach.

Ta tabela jest aktualizowana, gdy istnieją aktualizacje usługi i/lub dostępne są nowe obsługiwane wersje.

Aby wypróbować Instance Metadata Service, Utwórz maszynę wirtualną z poziomu [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) lub [Azure Portal](https://portal.azure.com) w powyższych regionach i postępuj zgodnie z poniższymi przykładami.
Więcej przykładów dotyczących zapytań IMDS można znaleźć w przykładach [metadanych wystąpienia platformy Azure](https://github.com/microsoft/azureimds)

## <a name="usage"></a>Sposób użycia

### <a name="versioning"></a>Przechowywanie wersji

Instance Metadata Service ma wersję, a określenie wersji interfejsu API w żądaniu HTTP jest obowiązkowe.

W tej [tabeli dostępności](#service-availability)są wyświetlane najnowsze wersje.

W miarę dodawania nowszych wersji nadal można uzyskać dostęp do starszych wersji, jeśli skrypty są zależne od określonych formatów danych.

Jeśli żadna wersja nie zostanie określona, zostanie zwrócony błąd z listą najnowszych obsługiwanych wersji.

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Reakcji**

```json
{
    "error": "Bad request. api-version was not specified in the request. For more information refer to aka.ms/azureimds",
    "newest-versions": [
        "2018-10-01",
        "2018-04-02",
        "2018-02-01"
    ]
}
```

### <a name="using-headers"></a>Używanie nagłówków

Podczas wykonywania zapytania dotyczącego Instance Metadata Service należy podać nagłówek `Metadata: true` , aby upewnić się, że żądanie nie zostało przypadkowo przekierowane.

### <a name="retrieving-metadata"></a>Pobieranie metadanych

Metadane wystąpienia są dostępne do uruchamiania maszyn wirtualnych utworzonych/zarządzanych przy użyciu [Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Uzyskaj dostęp do wszystkich kategorii danych dla wystąpienia maszyny wirtualnej, korzystając z następującego żądania:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> We wszystkich kwerendach metadanych wystąpienia jest rozróżniana wielkość liter.

### <a name="data-output"></a>Dane wyjściowe

Domyślnie Instance Metadata Service zwraca dane w formacie JSON (`Content-Type: application/json`). Jednak inne interfejsy API zwracają dane w różnych formatach, jeśli jest to wymagane.
Poniższa tabela zawiera odwołania do innych formatów danych, które mogą być obsługiwane przez interfejsy API.

Interfejs API | Domyślny format danych | Inne formaty
--------|---------------------|--------------
/instance | json | tekst
/scheduledevents | json | brak
/attested | json | brak

Aby uzyskać dostęp do formatu niedomyślnej odpowiedzi, należy określić żądany format jako parametr ciągu zapytania w żądaniu. Przykład:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> W `format=json` przypadku węzłów liścia nie działa. W przypadku tych `format=text` zapytań należy jawnie określić, czy formatem domyślnym jest JSON.

### <a name="security"></a>Zabezpieczenia

Punkt końcowy Instance Metadata Service jest dostępny tylko w ramach uruchomionego wystąpienia maszyny wirtualnej w adresie IP bez obsługi routingu. Ponadto wszystkie żądania z `X-Forwarded-For` nagłówkiem są odrzucane przez usługę.
Żądania muszą również zawierać `Metadata: true` nagłówek, aby upewnić się, że rzeczywiste żądanie było bezpośrednio przeznaczone i nie jest częścią niezamierzonego przekierowania.

### <a name="error"></a>Error

Jeśli nie odnaleziono elementu danych lub nieprawidłowo sformułowane żądanie, Instance Metadata Service zwraca standardowe błędy HTTP. Przykład:

Kod stanu HTTP | Przyczyna
----------------|-------
200 OK |
400 Nieprawidłowe żądanie | Brak `Metadata: true` nagłówka lub brak formatu podczas wykonywania zapytania o węzeł liścia
404 — Nie znaleziono | Żądany element nie istnieje
Metoda 405 nie jest dozwolona | Obsługiwane `GET` są tylko żądania
410 | Spróbuj ponownie za jakiś czas przez maksymalnie 70 sekund
429 Zbyt wiele żądań | Interfejs API obsługuje obecnie maksymalnie 5 zapytań na sekundę.
Błąd usługi 500     | Ponów próbę za jakiś czas

### <a name="examples"></a>Przykłady

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami JSON. Wszystkie poniższe przykładowe odpowiedzi są całkiem drukowane w celu zapewnienia czytelności.

#### <a name="retrieving-network-information"></a>Pobieranie informacji o sieci

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Reakcji**

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

#### <a name="retrieving-public-ip-address"></a>Pobieranie publicznego adresu IP

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network/interface/0/ipv4/ipAddress/0/publicIpAddress?api-version=2017-08-01&format=text"
```

#### <a name="retrieving-all-metadata-for-an-instance"></a>Pobieranie wszystkich metadanych wystąpienia

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Reakcji**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.1.2.5",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.1.2.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "000D3A36DDED"
      }
    ]
  }
}
```

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Pobieranie metadanych w maszynie wirtualnej z systemem Windows

**Request**

Metadane wystąpienia można pobrać w systemie Windows za pośrednictwem `curl` programu:

```powershell
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content
```

Lub za pomocą `Invoke-RestMethod` polecenia cmdlet programu PowerShell:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-06-01 -Method get
```

**Reakcji**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

```json
{
  "compute": {
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
  },
  "network": {
    "interface": [
      {
        "ipv4": {
          "ipAddress": [
            {
              "privateIpAddress": "10.0.1.4",
              "publicIpAddress": "X.X.X.X"
            }
          ],
          "subnet": [
            {
              "address": "10.0.1.0",
              "prefix": "24"
            }
          ]
        },
        "ipv6": {
          "ipAddress": []
        },
        "macAddress": "002248020E1E"
      }
    ]
  }
}
```

## <a name="metadata-apis"></a>Interfejsy API metadanych

Następujące interfejsy API są dostępne za pomocą punktu końcowego metadanych:

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
zaświadczenia | Zobacz [zaświadczone dane](#attested-data) | 2018-10-01
identity | Zarządzane tożsamości dla zasobów platformy Azure. Zobacz [pozyskiwanie tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
np | Zobacz [interfejs API wystąpienia](#instance-api) | 2017-04-02
scheduledevents | Zobacz [Scheduled Events](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>Interfejs API wystąpienia

Następujące kategorie obliczeniowe są dostępne za pomocą interfejsu API wystąpienia:

> [!NOTE]
> Za pomocą punktu końcowego metadanych następujące kategorie są dostępne za pomocą wystąpień/obliczeń

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
azEnvironment | Środowisko platformy Azure, w którym jest uruchomiona maszyna wirtualna | 2018-10-01
customData | Ta funkcja jest obecnie wyłączona i będziemy aktualizować tę dokumentację, gdy staną się dostępne | 2019-02-01
location | Region platformy Azure, w którym działa maszyna wirtualna | 2017-04-02
name | Nazwa maszyny wirtualnej | 2017-04-02
offer | Informacje o ofercie dla obrazu maszyny wirtualnej i dostępne tylko dla obrazów wdrożonych z galerii obrazów platformy Azure | 2017-04-02
osType | System Linux lub Windows | 2017-04-02
placementGroupId | [Grupa umieszczania](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) zestawu skalowania maszyn wirtualnych | 2017-08-01
plan | [Planowanie](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) zawierające nazwę, produkt i wydawcę maszyny wirtualnej, jeśli jest to obraz portalu Azure Marketplace | 2018-04-02
platformUpdateDomain |  [Aktualizuj domenę](manage-availability.md) , w której działa maszyna wirtualna | 2017-04-02
platformFaultDomain | [Domena błędów](manage-availability.md) , w której działa maszyna wirtualna | 2017-04-02
dostawcy | Dostawca maszyny wirtualnej | 2018-10-01
publicKeys | [Kolekcja kluczy publicznych](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) przypisanych do maszyny wirtualnej i ścieżek | 2018-04-02
publisher | Wydawca obrazu maszyny wirtualnej | 2017-04-02
resourceGroupName | [Grupa zasobów](../../azure-resource-manager/management/overview.md) dla maszyny wirtualnej | 2017-08-01
resourceId | W [pełni kwalifikowany](https://docs.microsoft.com/rest/api/resources/resources/getbyid) identyfikator zasobu | 2019-03-11
sku | Określona jednostka SKU dla obrazu maszyny wirtualnej | 2017-04-02
Obszarze storageprofile | Zobacz [profil magazynu](#storage-profile) | 2019-06-01
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej | 2017-08-01
tags | [Tagi](../../azure-resource-manager/management/tag-resources.md) dla maszyny wirtualnej  | 2017-08-01
tagsList | Tagi sformatowane jako tablica JSON dla łatwiejszego analizowania programistycznego  | 2019-06-04
Wersja | Wersja obrazu maszyny wirtualnej | 2017-04-02
vmId | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny wirtualnej | 2017-04-02
vmScaleSetName | [Nazwa zestawu skalowania maszyn wirtualnych](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) w zestawie skalowania maszyn wirtualnych | 2017-12-01
vmSize | [Rozmiar maszyny wirtualnej](sizes.md) | 2017-04-02
strefa | [Strefa dostępności](../../availability-zones/az-overview.md) maszyny wirtualnej | 2017-12-01

Następujące kategorie sieci są dostępne za pomocą interfejsu API wystąpienia:

> [!NOTE]
> Za pomocą punktu końcowego metadanych następujące kategorie są dostępne za pomocą wystąpienia/sieci/interfejsu

Dane | Opis | Wprowadzona wersja
-----|-------------|-----------------------
IPv4/privateIpAddress | Lokalny adres IPv4 maszyny wirtualnej | 2017-04-02
IPv4/publicIpAddress | Publiczny adres IPv4 maszyny wirtualnej | 2017-04-02
podsieć/adres | Adres podsieci maszyny wirtualnej | 2017-04-02
podsieć/prefiks | Prefiks podsieci, przykład 24 | 2017-04-02
IPv6/ipAddress | Lokalny adres IPv6 maszyny wirtualnej | 2017-04-02
macAddress | Adres MAC maszyny wirtualnej | 2017-04-02

## <a name="attested-data"></a>Zaświadczone dane

Częścią scenariusza obsługiwanego przez Instance Metadata Service jest zapewnienie gwarancji, że dostarczone dane pochodzą z platformy Azure. Firma Microsoft rejestruje część tych informacji, aby obrazy z portalu Marketplace mogły mieć pewność, że jest to obraz działający na platformie Azure.

### <a name="example-attested-data"></a>Przykładowe dane zaświadczone

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami JSON. Poniższe przykładowe odpowiedzi są dość drukowane w celu zapewnienia czytelności.

 **Request**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Interfejs API-Version jest polem obowiązkowym. Obsługiwane wersje interfejsu API znajdują się w [sekcji dostępności usługi](#service-availability) .
Identyfikator jednorazowy jest opcjonalnym 10-cyfrowym ciągiem. Jeśli nie zostanie podany, IMDS zwraca bieżącą sygnaturę czasową UTC w swoim miejscu. Ze względu na mechanizm buforowania IMDS, można zwrócić wcześniej buforowaną wartość nonce.

 **Reakcji**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Obiekt BLOB sygnatury jest podpisanym przez [PKCS7](https://aka.ms/pkcs7) wersją dokumentu. Zawiera certyfikat używany do podpisywania wraz ze szczegółami maszyny wirtualnej, takimi jak identyfikator maszyny wirtualnej, SKU, nonce, Identyfikator subskrypcji, sygnatura czasowa do utworzenia i wygaśnięcia dokumentu oraz informacje o planie obrazu. Informacje o planie są wypełniane wyłącznie na potrzeby obrazów w miejscu na rynku platformy Azure. Certyfikat może zostać wyodrębniony z odpowiedzi i użyty do zweryfikowania, że odpowiedź jest prawidłowa i pochodzi z platformy Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Pobieranie zaświadczania metadanych na maszynie wirtualnej z systemem Windows

 **Request**

Metadane wystąpienia można pobrać w systemie Windows za pomocą narzędzia `curl`PowerShell:

 ```powershell
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Lub za pomocą `Invoke-RestMethod` polecenia cmdlet:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Interfejs API-Version jest polem obowiązkowym. Obsługiwane wersje interfejsu API znajdują się w sekcji dostępności usługi.
Identyfikator jednorazowy jest opcjonalnym 10-cyfrowym ciągiem. Jeśli nie zostanie podany, IMDS zwraca bieżącą sygnaturę czasową UTC w swoim miejscu. Ze względu na mechanizm buforowania IMDS, można zwrócić wcześniej buforowaną wartość nonce.

 **Reakcji**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Obiekt BLOB sygnatury jest podpisanym przez [PKCS7](https://aka.ms/pkcs7) wersją dokumentu. Zawiera certyfikat używany do podpisywania wraz ze szczegółami maszyny wirtualnej, takimi jak identyfikator maszyny wirtualnej, SKU, nonce, Identyfikator subskrypcji, sygnatura czasowa do utworzenia i wygaśnięcia dokumentu oraz informacje o planie obrazu. Informacje o planie są wypełniane wyłącznie na potrzeby obrazów w miejscu na rynku platformy Azure. Certyfikat może zostać wyodrębniony z odpowiedzi i użyty do zweryfikowania, że odpowiedź jest prawidłowa i pochodzi z platformy Azure.

## <a name="example-scenarios-for-usage"></a>Przykładowe scenariusze użycia  

### <a name="tracking-vm-running-on-azure"></a>Śledzenie maszyny wirtualnej działającej na platformie Azure

Jako usługodawca może być konieczne śledzenie liczby maszyn wirtualnych korzystających z oprogramowania lub agentów, którzy muszą śledzić unikatowość maszyny wirtualnej. Aby można było uzyskać unikatowy identyfikator dla maszyny wirtualnej, użyj `vmId` pola z instance Metadata Service.

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Reakcji**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Umieszczanie kontenerów, domena błędów/aktualizacji bazująca na partycjach danych

W niektórych scenariuszach umieszczanie różnych replik danych ma podstawowe znaczenie. Na przykład [umieszczenie repliki](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) systemu plików HDFS lub umieszczenie kontenera za pośrednictwem programu [Orchestrator](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać znajomości `platformFaultDomain` , `platformUpdateDomain` a maszyna wirtualna jest uruchomiona.
Można również użyć [strefy dostępności](../../availability-zones/az-overview.md) , aby wystąpienia mogły podejmować te decyzje.
Możesz wysyłać zapytania o te dane bezpośrednio za pośrednictwem Instance Metadata Service.

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Reakcji**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Uzyskiwanie dodatkowych informacji o maszynie wirtualnej podczas przetwarzania zgłoszenia do pomocy technicznej

Jako dostawca usług możesz uzyskać informacje o pomocy technicznej, w których chcesz poznać więcej informacji o maszynie wirtualnej. Prośba o udostępnienie metadanych obliczeniowych przez klienta może stanowić podstawowe informacje dla specjalistów pomocy technicznej dotyczące rodzaju maszyny wirtualnej na platformie Azure.

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Reakcji**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższy Przykładowa odpowiedź jest całkiem wydrukowana pod kątem czytelności.

```json
{
    "azEnvironment": "AzurePublicCloud",
    "customData": "",
    "location": "centralus",
    "name": "negasonic",
    "offer": "lampstack",
    "osType": "Linux",
    "placementGroupId": "",
    "plan": {
        "name": "5-6",
        "product": "lampstack",
        "publisher": "bitnami"
    },
    "platformFaultDomain": "0",
    "platformUpdateDomain": "0",
    "provider": "Microsoft.Compute",
    "publicKeys": [],
    "publisher": "bitnami",
    "resourceGroupName": "myrg",
    "resourceId": "/subscriptions/xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx/resourceGroups/myrg/providers/Microsoft.Compute/virtualMachines/negasonic",
    "sku": "5-6",
    "storageProfile": {
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
    },
    "subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
    "tags": "Department:IT;Environment:Test;Role:WebRole",
    "version": "7.1.1902271506",
    "vmId": "13f56399-bd52-4150-9748-7190aae1ff21",
    "vmScaleSetName": "",
    "vmSize": "Standard_A1_v2",
    "zone": "1"
}
```

### <a name="getting-azure-environment-where-the-vm-is-running"></a>Uzyskiwanie informacji o środowisku platformy Azure, w którym działa maszyna wirtualna

Platforma Azure ma rozmaite suwerenne chmury, takie jak [Azure Government](https://azure.microsoft.com/overview/clouds/government/). Czasami konieczne jest środowisko platformy Azure, aby podejmować pewne decyzje dotyczące środowiska uruchomieniowego. Poniższy przykład pokazuje, jak można to osiągnąć.

**Request**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Reakcji**
```bash
AzurePublicCloud
```

Poniżej znajdują się regiony i wartości środowiska platformy Azure.

 Regiony | Środowisko platformy Azure
---------|-----------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china)          | AzureChinaCloud
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Pobieranie tagów dla maszyny wirtualnej

Tagi mogły zostać zastosowane do maszyny wirtualnej platformy Azure, aby logicznie zorganizować je w taksonomię. Tagi przypisane do maszyny wirtualnej można pobrać przy użyciu poniższego żądania.

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Reakcji**

```text
Department:IT;Environment:Test;Role:WebRole
```

`tags` Pole jest ciągiem zawierającym znaczniki rozdzielane średnikami. Może to być problem, jeśli średniki są używane w samych tagach. Jeśli parser jest Zapisano w celu programowego wyodrębnienia tagów, należy polegać na `tagsList` polu, które jest tablicą JSON bez ograniczników, a w związku z tym łatwiejszym do przeanalizowania.

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=JSON"
```

**Reakcji**

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

### <a name="validating-that-the-vm-is-running-in-azure"></a>Weryfikowanie, czy maszyna wirtualna jest uruchomiona na platformie Azure

Dostawcy portalu Marketplace chcą mieć pewność, że ich oprogramowanie jest licencjonowane do uruchamiania tylko na platformie Azure. Jeśli ktoś kopiuje dysk VHD do lokalnego programu, powinien on mieć możliwość wykrycia tej funkcji. Dzwoniąc do Instance Metadata Service, dostawcy portalu Marketplace mogą uzyskać podpisane dane, które gwarantują odpowiedź tylko z platformy Azure.

> [!NOTE]
> Wymaga zainstalowania JQ.

**Request**

 ```bash
  # Get the signature
   curl  --silent -H Metadata:True http://169.254.169.254/metadata/attested/document?api-version=2019-04-30 | jq -r '.["signature"]' > signature
  # Decode the signature
  base64 -d signature > decodedsignature
  #Get PKCS7 format
  openssl pkcs7 -in decodedsignature -inform DER -out sign.pk7
  # Get Public key out of pkc7
  openssl pkcs7 -in decodedsignature -inform DER  -print_certs -out signer.pem
  #Get the intermediate certificate
  wget -q -O intermediate.cer "$(openssl x509 -in signer.pem -text -noout | grep " CA Issuers -" | awk -FURI: '{print $2}')"
  openssl x509 -inform der -in intermediate.cer -out intermediate.pem
  #Verify the contents
  openssl smime -verify -in sign.pk7 -inform pem -noverify
 ```

 **Reakcji**

```json
Verification successful
{"nonce":"20181128-001617",
  "plan":
    {
     "name":"",
     "product":"",
     "publisher":""
    },
"timeStamp":
  {
    "createdOn":"11/28/18 00:16:17 -0000",
    "expiresOn":"11/28/18 06:16:17 -0000"
  },
"vmId":"d3e0e374-fda6-4649-bbc9-7f20dc379f34",
"subscriptionId": "xxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxx",
"sku": "RS3-Pro"
}
```

Dane | Opis
-----|------------
jednorazow | Użytkownik podano opcjonalny ciąg z żądaniem. Jeśli w żądaniu nie podano identyfikatora jednorazowego, zwracana jest bieżąca sygnatura czasowa UTC
plan | [Zaplanuj](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) maszynę wirtualną w tym obrazie portalu Azure Marketplace, która zawiera nazwę, produkt i wydawcę
Sygnatura czasowa/createdOn | Sygnatura czasowa UTC, w której został utworzony pierwszy podpisany dokument
Sygnatura czasowa/expiresOn | Sygnatura czasowa UTC, z którą wygasa podpisany dokument
vmId |  [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) dla maszyny wirtualnej
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej, wprowadzona w`2019-04-30`
sku | Określona jednostka SKU dla obrazu maszyny wirtualnej wprowadzona w`2019-11-01`

#### <a name="verifying-the-signature"></a>Weryfikowanie podpisu

Po otrzymaniu powyższego podpisu możesz sprawdzić, czy podpis pochodzi od firmy Microsoft. Można także zweryfikować certyfikat pośredni i łańcuch certyfikatów. Na koniec możesz sprawdzić, czy identyfikator subskrypcji jest poprawny.

> [!NOTE]
> Certyfikat dla chmury publicznej i suwerennej chmury będzie różny.

 Chmurowa | Certyfikat
---------|-----------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | *. metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *. metadata.azure.us
[Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *. metadata.azure.cn
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | *. metadata.microsoftazure.de

Istnieje znany problem związany z certyfikatem używanym do podpisywania. Certyfikaty mogą nie mieć dokładnego dopasowania `metadata.azure.com` dla chmury publicznej. W związku z tym weryfikacja certyfikacji powinna zezwalać na wspólną `.metadata.azure.com` nazwę z dowolnej poddomeny.

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

W przypadkach, gdy nie można pobrać certyfikatu pośredniego z powodu ograniczeń sieci podczas weryfikacji, certyfikat pośredni może zostać przypięty. Jednak platforma Azure przywróci certyfikaty zgodnie z zasadami standardowej infrastruktury kluczy publicznych. Przypięte certyfikaty należy zaktualizować, gdy nastąpi przerzucanie. Za każdym razem, gdy planowana jest zmiana aktualizacji certyfikatu pośredniego, blog platformy Azure zostanie zaktualizowany i klienci platformy Azure zostaną powiadomieni. Certyfikaty pośrednie można znaleźć [tutaj](https://www.microsoft.com/pki/mscorp/cps/default.htm). Certyfikaty pośrednie dla każdego regionu mogą być różne.

> [!NOTE]
> Certyfikat pośredni dla usług Azure Chiny dla Chin będzie z DigiCert globalnego głównego urzędu certyfikacji, a nie Baltimore.
Ponadto, jeśli przypięto certyfikaty pośrednie dla Chin platformy Azure w ramach zmiany urzędu łańcucha głównego, certyfikaty pośrednie będą musiały zostać zaktualizowane.

### <a name="failover-clustering-in-windows-server"></a>Klaster trybu failover w systemie Windows Server

W przypadku niektórych scenariuszy podczas wykonywania zapytania dotyczącego Instance Metadata Service z klastrem trybu failover należy dodać trasę do tabeli routingu.

1. Otwórz wiersz polecenia z uprawnieniami administratora.

2. Uruchom następujące polecenie i Zanotuj adres interfejsu dla miejsca docelowego sieci (`0.0.0.0`) w tabeli tras IPv4.

```bat
route print
```

> [!NOTE]
> Następujące przykładowe dane wyjściowe z maszyny wirtualnej z systemem Windows Server z włączoną funkcją klaster trybu failover zawierają tylko tabelę tras IPv4 dla uproszczenia.

```bat
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
        224.0.0.0        240.0.0.0         On-link         10.0.1.10    266
  255.255.255.255  255.255.255.255         On-link         127.0.0.1    331
  255.255.255.255  255.255.255.255         On-link     169.254.1.156    271
  255.255.255.255  255.255.255.255         On-link         10.0.1.10    266
```

1. Uruchom następujące polecenie i użyj adresu interfejsu dla miejsca docelowego sieci (`0.0.0.0`), czyli (`10.0.1.10`) w tym przykładzie.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="storage-profile"></a>Profil magazynu

Instance Metadata Service można dostarczyć szczegółowych informacji o dyskach magazynu skojarzonych z maszyną wirtualną. Te dane można znaleźć w punkcie końcowym wystąpienie/Obliczanie/obszarze storageprofile.

Profil magazynu maszyny wirtualnej jest podzielony na trzy kategorie — odwołanie do obrazu, dysk systemu operacyjnego i dyski z danymi.

Obiekt odwołanie do obrazu zawiera następujące informacje o obrazie systemu operacyjnego:

Dane    | Opis
--------|-----------------
id      | Identyfikator zasobu
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
image   | Wirtualny dysk twardy obrazu użytkownika źródłowego
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
image   | Wirtualny dysk twardy obrazu użytkownika źródłowego
managedDisk | Parametry dysku zarządzanego
name    | Nazwa dysku
osType  | Typ systemu operacyjnego znajdującego się na dysku
VHD     | Wirtualny dysk twardy
writeAcceleratorEnabled | Czy writeAccelerator jest włączona na dysku

Poniżej przedstawiono przykład sposobu wykonywania zapytania dotyczącego informacji o magazynowaniu maszyny wirtualnej.

**Request**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Reakcji**

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Przykłady wywoływania usługi metadanych przy użyciu różnych języków w maszynie wirtualnej 

Język | Przykład
---------|----------------
Ruby     | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.rb
Przejdź  | https://github.com/Microsoft/azureimds/blob/master/imdssample.go
Python   | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.py
C++      | https://github.com/Microsoft/azureimds/blob/master/IMDSSample-windows.cpp
C#       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.cs
JavaScript | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.js
PowerShell | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.ps1
Bash       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.sh
Języku       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Najczęściej zadawane pytania

1. Pojawia się błąd `400 Bad Request, Required metadata header not specified`. Co to oznacza?
   * Instance Metadata Service wymaga przekazanie nagłówka `Metadata: true` w żądaniu. Przekazanie tego nagłówka w wywołaniu REST umożliwia dostęp do Instance Metadata Service.
2. Dlaczego nie otrzymuję informacji o obliczeniach dla mojej maszyny wirtualnej?
   * Obecnie Instance Metadata Service obsługuje tylko wystąpienia utworzone przy użyciu Azure Resource Manager. W przyszłości można dodać obsługę maszyn wirtualnych usługi w chmurze.
3. Moja maszyna wirtualna została utworzona przez Azure Resource Manager a. Dlaczego nie widzę informacji o metadanych obliczeń?
   * Dla wszystkich maszyn wirtualnych utworzonych po SEP 2016 Dodaj [tag](../../azure-resource-manager/management/tag-resources.md) , aby rozpocząć wyświetlanie metadanych obliczeniowych. W przypadku starszych maszyn wirtualnych (utworzonych przed SEP 2016) Dodaj/Usuń rozszerzenia lub dyski danych do maszyny wirtualnej w celu odświeżenia metadanych.
4. Nie widzę wszystkich danych wypełnionych dla nowej wersji
   * Dla wszystkich maszyn wirtualnych utworzonych po SEP 2016 Dodaj [tag](../../azure-resource-manager/management/tag-resources.md) , aby rozpocząć wyświetlanie metadanych obliczeniowych. W przypadku starszych maszyn wirtualnych (utworzonych przed SEP 2016) Dodaj/Usuń rozszerzenia lub dyski danych do maszyny wirtualnej w celu odświeżenia metadanych.
5. Dlaczego otrzymuję błąd `500 Internal Server Error`?
   * Ponów żądanie w oparciu o wykładniczy system wycofywania. Jeśli problem będzie się powtarzać, skontaktuj się z pomocą techniczną platformy Azure.
6. Gdzie mogę udostępnić dodatkowe pytania/Komentarze?
   * Wyślij komentarze https://feedback.azure.com.
7. Czy ta funkcja będzie działała w przypadku wystąpienia zestawu skalowania maszyn wirtualnych?
   * Tak, aby usługa metadanych była dostępna dla wystąpień zestawu skalowania.
8. Jak mogę uzyskać pomoc techniczną dla usługi?
   * Aby uzyskać pomoc techniczną dotyczącą usługi, Utwórz problem pomocy technicznej w Azure Portal dla maszyny wirtualnej, na której nie można uzyskać odpowiedzi na metadane po długotrwałych próbach.
9. Upłynął limit czasu żądania dla wywołania usługi?
   * Wywołania metadanych muszą pochodzić z podstawowego adresu IP przypisanego do podstawowej karty sieciowej maszyny wirtualnej. Ponadto w przypadku zmiany trasy musi istnieć trasa dla 169.254.0.0/16 adresów z karty sieciowej.
10. Zaktualizowaliśmy moje Tagi w zestawie skalowania maszyn wirtualnych, ale nie są one wyświetlane w wystąpieniach, w przeciwieństwie do maszyn wirtualnych?
    * Obecnie dla tagów ScaleSets są wyświetlane tylko na maszynie wirtualnej na ponownym uruchomieniu/reobrazie/lub dysku zmienionym na wystąpienie.

    ![Obsługa metadanych wystąpienia](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [Scheduled Events](scheduled-events.md)
