---
title: Usługa metadanych wystąpienia platformy Azure
description: Interfejs RESTful, aby uzyskać informacje o zdarzeniach obliczeniowych, sieci i nadchodzących zdarzeń konserwacji maszyn wirtualnych.
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
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81870081"
---
# <a name="azure-instance-metadata-service"></a>Usługa metadanych wystąpienia platformy Azure

Usługa metadanych wystąpienia platformy Azure (IMDS) zawiera informacje o aktualnie uruchomionych wystąpieniach maszyn wirtualnych i może służyć do zarządzania i konfigurowania maszyn wirtualnych.
Podane informacje obejmują jednostkę SKU, konfigurację sieci i nadchodzące zdarzenia konserwacji. Aby uzyskać pełną listę dostępnych danych, zobacz [interfejsy API metadanych](#metadata-apis).

Usługa metadanych wystąpienia platformy Azure to punkt końcowy REST dostępny dla wszystkich maszyn wirtualnych IaaS utworzonych za pośrednictwem [usługi Azure Resource Manager.](https://docs.microsoft.com/rest/api/resources/)
Punkt końcowy jest dostępny pod dobrze znanym nie rutowalnym adresem IP (`169.254.169.254`), do który można uzyskać tylko z poziomu maszyny Wirtualnej.

> [!IMPORTANT]
> Ta usługa jest **ogólnie dostępna** we wszystkich regionach platformy Azure.  Regularnie otrzymuje aktualizacje, aby udostępnić nowe informacje o wystąpieniach maszyny wirtualnej. Ta strona odzwierciedla aktualne dostępne [interfejsy API metadanych.](#metadata-apis)

## <a name="service-availability"></a>Dostępność usług

Usługa jest dostępna w ogólnie dostępnych regionach platformy Azure. Nie wszystkie wersje interfejsu API mogą być dostępne we wszystkich regionach platformy Azure.

Regiony                                        | Dostępność?                                 | Obsługiwane wersje
-----------------------------------------------|-----------------------------------------------|-----------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure w Chinach — 21Vianet](https://www.azure.cn/)                                            | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | Ogólnie dostępne | 2017-04-02, 2017-08-01, 2017-12-01, 2018-02-01, 2018-04-02, 2018-10-01, 2019-02-01, 2019-03-11, 2019-04-30, 2019-06-01, 2019-06-04, 2019-08-01, 2019-08-15

Wersja 2019-11-01 jest obecnie wdrażana i może nie być dostępna we wszystkich regionach.

Ta tabela jest aktualizowana, gdy dostępne są aktualizacje usługi i/lub dostępne są nowe obsługiwane wersje.

Aby wypróbować usługę metadanych wystąpienia, utwórz maszynę wirtualną z [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/) lub [witrynę Azure portal](https://portal.azure.com) w powyższych regionach i postępuj zgodnie z poniższymi przykładami.
Dalsze przykłady sposobu wykonywania zapytań o usługi IMDS można znaleźć w [przykładach metadanych wystąpienia platformy Azure](https://github.com/microsoft/azureimds)

## <a name="usage"></a>Sposób użycia

### <a name="versioning"></a>Przechowywanie wersji

Usługa metadanych wystąpienia jest wersjona, a określenie wersji interfejsu API w żądaniu HTTP jest obowiązkowe.

Najnowsze wersje można zobaczyć na liście w tej [tabeli dostępności](#service-availability).

Po dodaniu nowszych wersji starsze wersje są nadal dostępne w celu zapewnienia zgodności, jeśli skrypty mają zależności od określonych formatów danych.

Jeśli nie określono wersji, zwracany jest błąd z listą najnowszych obsługiwanych wersji.

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższa przykładowa odpowiedź jest dość wydrukowana w celu uzyskania czytelności.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance"
```

**Odpowiedzi**

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

### <a name="using-headers"></a>Korzystanie z nagłówków

Podczas kwerendy usługi metadanych wystąpienia, `Metadata: true` należy podać nagłówek, aby upewnić się, że żądanie nie zostało przypadkowo przekierowane.

### <a name="retrieving-metadata"></a>Pobieranie metadanych

Metadane wystąpienia są dostępne do uruchamiania maszyn wirtualnych utworzonych/zarządzanych przy użyciu [usługi Azure Resource Manager](https://docs.microsoft.com/rest/api/resources/). Dostęp do wszystkich kategorii danych dla wystąpienia maszyny wirtualnej przy użyciu następującego żądania:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01"
```

> [!NOTE]
> Wszystkie kwerendy metadanych wystąpienia są rozróżniane.

### <a name="data-output"></a>Dane wyjściowe

Domyślnie usługa metadanych wystąpienia zwraca dane`Content-Type: application/json`w formacie JSON ( ). Jednak różne interfejsy API zwracają dane w różnych formatach, jeśli jest to wymagane.
Poniższa tabela jest odwołaniem do innych formatów danych, które mogą obsługiwać interfejsy API.

Interfejs API | Domyślny format danych | Inne formaty
--------|---------------------|--------------
/instance | json | tekst
/scheduledevents | json | brak
/potwierdzone | json | brak

Aby uzyskać dostęp do formatu odpowiedzi nie domyślnej, należy określić żądany format jako parametr ciągu zapytania w żądaniu. Przykład:

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2017-08-01&format=text"
```

> [!NOTE]
> W przypadku węzłów liścia `format=json` nie działa. Dla tych `format=text` kwerend musi być jawnie określony, jeśli domyślny format jest json.

### <a name="security"></a>Zabezpieczenia

Punkt końcowy usługi metadanych wystąpienia jest dostępny tylko z poziomu uruchomionego wystąpienia maszyny wirtualnej na nierutowalnym adresie IP. Ponadto każde żądanie z `X-Forwarded-For` nagłówkiem jest odrzucane przez usługę.
Żądania muszą również `Metadata: true` zawierać nagłówek, aby upewnić się, że rzeczywiste żądanie było bezpośrednio zamierzone, a nie częścią niezamierzonego przekierowania.

### <a name="error"></a>Błąd

Jeśli nie znaleziono elementu danych lub nieprawidłowo sformułowane żądanie, usługa metadanych wystąpienia zwraca standardowe błędy HTTP. Przykład:

Kod stanu HTTP | Przyczyna
----------------|-------
200 ok. |
400 Zła prośba | Brak `Metadata: true` nagłówka lub brak formatu podczas wykonywania kwerendy w węźle liścia
404 — Nie znaleziono | Żądany element nie istnieje
405 Metoda niedozwolona | Obsługiwane `GET` są tylko żądania
410 Odszedł | Ponów próbę po pewnym czasie na maksymalnie 70 sekund
429 Zbyt wiele żądań | Interfejs API obsługuje obecnie maksymalnie 5 zapytań na sekundę
Błąd usługi 500     | Ponów próbę po pewnym czasie

### <a name="examples"></a>Przykłady

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami JSON. Wszystkie poniższe przykładowe odpowiedzi są dość wydrukowane dla czytelności.

#### <a name="retrieving-network-information"></a>Pobieranie informacji o sieci

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/network?api-version=2017-08-01"
```

**Odpowiedzi**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższa przykładowa odpowiedź jest dość wydrukowana w celu uzyskania czytelności.

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

#### <a name="retrieving-all-metadata-for-an-instance"></a>Pobieranie wszystkich metadanych dla wystąpienia

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance?api-version=2019-06-01"
```

**Odpowiedzi**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższa przykładowa odpowiedź jest dość wydrukowana w celu uzyskania czytelności.

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

#### <a name="retrieving-metadata-in-windows-virtual-machine"></a>Pobieranie metadanych na maszynie wirtualnej systemu Windows

**Żądanie**

Metadane instancji można pobrać w `curl` systemie Windows za pośrednictwem programu:

```powershell
curl -H @{'Metadata'='true'} http://169.254.169.254/metadata/instance?api-version=2019-06-01 | select -ExpandProperty Content
```

Lub za `Invoke-RestMethod` pośrednictwem polecenia cmdlet programu PowerShell:

```powershell

Invoke-RestMethod -Headers @{"Metadata"="true"} -URI http://169.254.169.254/metadata/instance?api-version=2019-06-01 -Method get
```

**Odpowiedzi**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższa przykładowa odpowiedź jest dość wydrukowana w celu uzyskania czytelności.

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

Następujące interfejsy API są dostępne za pośrednictwem punktu końcowego metadanych:

Dane | Opis | Wprowadzono wersję
-----|-------------|-----------------------
Potwierdzone | Zobacz [potwierdzone dane](#attested-data) | 2018-10-01
identity | Tożsamości zarządzane dla zasobów platformy Azure. Zobacz [uzyskiwanie tokenu dostępu](../../active-directory/managed-identities-azure-resources/how-to-use-vm-token.md) | 2018-02-01
Wystąpienie | Zobacz [interfejs API wystąpienia](#instance-api) | 2017-04-02
terminy | Zobacz [zaplanowane wydarzenia](scheduled-events.md) | 2017-08-01

### <a name="instance-api"></a>Interfejs API wystąpienia

Następujące kategorie obliczeń są dostępne za pośrednictwem interfejsu API wystąpienia:

> [!NOTE]
> Za pośrednictwem punktu końcowego metadanych dostępne są następujące kategorie za pośrednictwem wystąpienia/obliczeń

Dane | Opis | Wprowadzono wersję
-----|-------------|-----------------------
azŚrodowisk | Środowisko platformy Azure, w którym maszyna wirtualna jest uruchomiona | 2018-10-01
Customdata | Ta funkcja jest obecnie wyłączona i zaktualizujemy tę dokumentację, gdy stanie się dostępna | 2019-02-01
location | Region platformy Azure, w który działa maszyna wirtualna | 2017-04-02
name | Nazwa maszyny Wirtualnej | 2017-04-02
offer | Informacje o ofercie dla obrazu maszyny Wirtualnej i są dostępne tylko dla obrazów wdrożonych z galerii obrazów platformy Azure | 2017-04-02
osType | Linux lub Windows | 2017-04-02
placementGroupId (ład grupy) | [Grupa umieszczania](../../virtual-machine-scale-sets/virtual-machine-scale-sets-placement-groups.md) zestawu skalowania maszyny wirtualnej | 2017-08-01
plan | [Plan](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) zawierający nazwę, produkt i wydawcę maszyny Wirtualnej, jeśli jest to obraz portalu Azure Marketplace | 2018-04-02
platformaUpdateDomain |  [Aktualizuj domenę,](manage-availability.md) w | 2017-04-02
platformaFaultDomain | [Domena błędów,](manage-availability.md) w | 2017-04-02
Dostawca | Dostawca maszyny Wirtualnej | 2018-10-01
klawisze publiczne | [Kolekcja kluczy publicznych](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#sshpublickey) przypisanych do maszyny Wirtualnej i ścieżek | 2018-04-02
wydawca | Wydawca obrazu maszyny Wirtualnej | 2017-04-02
resourceGroupName | [Grupa zasobów](../../azure-resource-manager/management/overview.md) dla maszyny wirtualnej | 2017-08-01
resourceId | [W pełni kwalifikowany](https://docs.microsoft.com/rest/api/resources/resources/getbyid) identyfikator zasobu | 2019-03-11
sku | Określona jednostka SKU dla obrazu maszyny Wirtualnej | 2017-04-02
przechowywanieProfile | Zobacz [Profil pamięci masowej](#storage-profile) | 2019-06-01
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej | 2017-08-01
tags | [Tagi](../../azure-resource-manager/management/tag-resources.md) dla maszyny wirtualnej  | 2017-08-01
lista tagów | Znaczniki sformatowane jako tablica JSON dla łatwiejszego analizowania programowego  | 2019-06-04
version | Wersja obrazu maszyny Wirtualnej | 2017-04-02
Mieszek maszyn wirtualnych | [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) maszyny Wirtualnej | 2017-04-02
Nazwa zestawu vmScaleSet | [Zestaw skalowania maszyny wirtualnej Nazwa](../../virtual-machine-scale-sets/virtual-machine-scale-sets-overview.md) zestawu skalowania maszyny wirtualnej | 2017-12-01
vmSize | [Rozmiar maszyny Wirtualnej](sizes.md) | 2017-04-02
strefa | [Strefa dostępności](../../availability-zones/az-overview.md) maszyny wirtualnej | 2017-12-01

Następujące kategorie sieci są dostępne za pośrednictwem interfejsu API wystąpienia:

> [!NOTE]
> Za pośrednictwem punktu końcowego metadanych dostępne są następujące kategorie za pośrednictwem wystąpienia/sieci/interfejsu

Dane | Opis | Wprowadzono wersję
-----|-------------|-----------------------
ipv4/privateIpAddress | Lokalny adres IPv4 maszyny Wirtualnej | 2017-04-02
ipv4/publicIpAddress | Publiczny adres IPv4 maszyny Wirtualnej | 2017-04-02
podsieci/adres | Adres podsieci maszyny Wirtualnej | 2017-04-02
podsieć/prefiks | Prefiks podsieci, przykład 24 | 2017-04-02
ipv6/ipAddress | Lokalny adres IPv6 maszyny Wirtualnej | 2017-04-02
macAddress | Adres mac maszyny Wirtualnej | 2017-04-02

## <a name="attested-data"></a>Potwierdzone dane

Część scenariusza obsługiwane przez usługę metadanych wystąpienia jest zapewnienie gwarancji, że dostarczone dane pochodzą z platformy Azure. Podpisujemy część tych informacji, aby obrazy portalu Marketplace mogły mieć pewność, że jest to ich obraz uruchomiony na platformie Azure.

### <a name="example-attested-data"></a>Przykładowe potwierdzone dane

> [!NOTE]
> Wszystkie odpowiedzi interfejsu API są ciągami JSON. Poniższe przykładowe odpowiedzi są dość wydrukowane dla czytelności.

 **Żądanie**

 ```bash
curl -H Metadata:true "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890"

```

Wersja api jest polem obowiązkowym. Zobacz [sekcję dostępności usług](#service-availability) dla obsługiwanych wersji interfejsu API.
Nonce jest opcjonalnym ciągiem 10-cyfrowym. Jeśli nie podano, imds zwraca bieżącą sygnaturę czasną UTC w jego miejsce. Ze względu na mechanizm buforowania IMDS wcześniej buforowane wartości nonce mogą być zwracane.

 **Odpowiedzi**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższa przykładowa odpowiedź jest dość wydrukowana w celu uzyskania czytelności.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Obiekt blob podpisu jest podpisaną wersją dokumentu [podpisaną przez pkcs7.](https://aka.ms/pkcs7) Zawiera certyfikat używany do podpisywania wraz ze szczegółami maszyny Wirtualnej, takimi jak vmId, sku, nonce, subscriptionId, timeStamp do tworzenia i wygaśnięcia dokumentu oraz informacje o planie obrazu. Informacje o planie są wypełniane tylko dla obrazów miejsc usługi Azure Market. Certyfikat można wyodrębnić z odpowiedzi i służy do sprawdzania poprawności, że odpowiedź jest prawidłowa i pochodzi z platformy Azure.

#### <a name="retrieving-attested-metadata-in-windows-virtual-machine"></a>Pobieranie pouczonych metadanych na maszynie wirtualnej systemu Windows

 **Żądanie**

Metadane wystąpienia można pobrać w systemie Windows `curl`za pomocą narzędzia PowerShell:

 ```powershell
curl -H @{'Metadata'='true'} "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" | select -ExpandProperty Content
```

 Lub przez `Invoke-RestMethod` polecenie cmdlet:

 ```powershell
Invoke-RestMethod -Headers @{"Metadata"="true"} -URI "http://169.254.169.254/metadata/attested/document?api-version=2018-10-01&nonce=1234567890" -Method get
```

Wersja api jest polem obowiązkowym. Zobacz sekcję dostępności usług dla obsługiwanych wersji interfejsu API.
Nonce jest opcjonalnym ciągiem 10-cyfrowym. Jeśli nie podano, imds zwraca bieżącą sygnaturę czasną UTC w jego miejsce. Ze względu na mechanizm buforowania IMDS wcześniej buforowane wartości nonce mogą być zwracane.

 **Odpowiedzi**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższa przykładowa odpowiedź jest dość wydrukowana w celu uzyskania czytelności.

 ```json
{
 "encoding":"pkcs7","signature":"MIIEEgYJKoZIhvcNAQcCoIIEAzCCA/8CAQExDzANBgkqhkiG9w0BAQsFADCBugYJKoZIhvcNAQcBoIGsBIGpeyJub25jZSI6IjEyMzQ1NjY3NjYiLCJwbGFuIjp7Im5hbWUiOiIiLCJwcm9kdWN0IjoiIiwicHVibGlzaGVyIjoiIn0sInRpbWVTdGFtcCI6eyJjcmVhdGVkT24iOiIxMS8yMC8xOCAyMjowNzozOSAtMDAwMCIsImV4cGlyZXNPbiI6IjExLzIwLzE4IDIyOjA4OjI0IC0wMDAwIn0sInZtSWQiOiIifaCCAj8wggI7MIIBpKADAgECAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBBAUAMCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tMB4XDTE4MTEyMDIxNTc1N1oXDTE4MTIyMDIxNTc1NlowKzEpMCcGA1UEAxMgdGVzdHN1YmRvbWFpbi5tZXRhZGF0YS5henVyZS5jb20wgZ8wDQYJKoZIhvcNAQEBBQADgY0AMIGJAoGBAML/tBo86ENWPzmXZ0kPkX5dY5QZ150mA8lommszE71x2sCLonzv4/UWk4H+jMMWRRwIea2CuQ5RhdWAHvKq6if4okKNt66fxm+YTVz9z0CTfCLmLT+nsdfOAsG1xZppEapC0Cd9vD6NCKyE8aYI1pliaeOnFjG0WvMY04uWz2MdAgMBAAGjYDBeMFwGA1UdAQRVMFOAENnYkHLa04Ut4Mpt7TkJFfyhLTArMSkwJwYDVQQDEyB0ZXN0c3ViZG9tYWluLm1ldGFkYXRhLmF6dXJlLmNvbYIQZ8VuSofHbJRAQNBNpiASdDANBgkqhkiG9w0BAQQFAAOBgQCLSM6aX5Bs1KHCJp4VQtxZPzXF71rVKCocHy3N9PTJQ9Fpnd+bYw2vSpQHg/AiG82WuDFpPReJvr7Pa938mZqW9HUOGjQKK2FYDTg6fXD8pkPdyghlX5boGWAMMrf7bFkup+lsT+n2tRw2wbNknO1tQ0wICtqy2VqzWwLi45RBwTGB6DCB5QIBATA/MCsxKTAnBgNVBAMTIHRlc3RzdWJkb21haW4ubWV0YWRhdGEuYXp1cmUuY29tAhBnxW5Kh8dslEBA0E2mIBJ0MA0GCSqGSIb3DQEBCwUAMA0GCSqGSIb3DQEBAQUABIGAld1BM/yYIqqv8SDE4kjQo3Ul/IKAVR8ETKcve5BAdGSNkTUooUGVniTXeuvDj5NkmazOaKZp9fEtByqqPOyw/nlXaZgOO44HDGiPUJ90xVYmfeK6p9RpJBu6kiKhnnYTelUk5u75phe5ZbMZfBhuPhXmYAdjc7Nmw97nx8NnprQ="
}
```

Obiekt blob podpisu jest podpisaną wersją dokumentu [podpisaną przez pkcs7.](https://aka.ms/pkcs7) Zawiera certyfikat używany do podpisywania wraz ze szczegółami maszyny Wirtualnej, takimi jak vmId, sku, nonce, subscriptionId, timeStamp do tworzenia i wygaśnięcia dokumentu oraz informacje o planie obrazu. Informacje o planie są wypełniane tylko dla obrazów miejsc usługi Azure Market. Certyfikat można wyodrębnić z odpowiedzi i służy do sprawdzania poprawności, że odpowiedź jest prawidłowa i pochodzi z platformy Azure.

## <a name="example-scenarios-for-usage"></a>Przykładowe scenariusze użycia  

### <a name="tracking-vm-running-on-azure"></a>Śledzenie maszyny wirtualnej działającej na platformie Azure

Jako dostawca usług może być konieczne śledzenie liczby maszyn wirtualnych z oprogramowaniem lub agentów, którzy muszą śledzić unikatowość maszyny Wirtualnej. Aby uzyskać unikatowy identyfikator maszyny Wirtualnej, użyj `vmId` pola z usługi metadanych wystąpienia.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/vmId?api-version=2017-08-01&format=text"
```

**Odpowiedzi**

```text
5c08b38e-4d57-4c23-ac45-aca61037f084
```

### <a name="placement-of-containers-data-partitions-based-faultupdate-domain"></a>Umieszczanie kontenerów, domena błędów/aktualizacji bazująca na partycjach danych

W niektórych scenariuszach rozmieszczenie replik różnych danych ma pierwszorzędne znaczenie. Na przykład [umieszczenie repliki HDFS](https://hadoop.apache.org/docs/stable/hadoop-project-dist/hadoop-hdfs/HdfsDesign.html#Replica_Placement:_The_First_Baby_Steps) lub umieszczanie kontenera za `platformUpdateDomain` pośrednictwem [koordynatora](https://kubernetes.io/docs/user-guide/node-selection/) może wymagać `platformFaultDomain` wiedzieć, a maszyna wirtualna jest uruchomiona.
Do [podejmowania](../../availability-zones/az-overview.md) tych decyzji można również użyć stref dostępności dla wystąpień.
Można zbadać te dane bezpośrednio za pośrednictwem usługi metadanych wystąpienia.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/platformFaultDomain?api-version=2017-08-01&format=text"
```

**Odpowiedzi**

```text
0
```

### <a name="getting-more-information-about-the-vm-during-support-case"></a>Uzyskiwanie dodatkowych informacji o maszynie wirtualnej podczas przetwarzania zgłoszenia do pomocy technicznej

Jako dostawca usług możesz uzyskać połączenie pomocy technicznej, w którym chcesz uzyskać więcej informacji na temat maszyny Wirtualnej. Proszenie klienta o udostępnienie metadanych obliczeń może dostarczyć podstawowych informacji dla pracownika pomocy technicznej, aby wiedzieć o rodzaju maszyny Wirtualnej na platformie Azure.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute?api-version=2019-06-01"
```

**Odpowiedzi**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższa przykładowa odpowiedź jest dość wydrukowana w celu uzyskania czytelności.

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

Platforma Azure ma różne suwerenne chmury, takie jak [Azure Government.](https://azure.microsoft.com/overview/clouds/government/) Czasami potrzebujesz środowiska platformy Azure, aby podjąć pewne decyzje dotyczące środowiska uruchomieniowego. W poniższym przykładzie pokazano, jak można osiągnąć to zachowanie.

**Żądanie**
```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/azEnvironment?api-version=2018-10-01&format=text"
```

**Odpowiedzi**
```bash
AzurePublicCloud
```

Regiony i wartości środowiska platformy Azure są wymienione poniżej.

 Regiony | Środowisko platformy Azure
---------|-----------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | Usługa AzurePublicCloud
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | AzureUSGovernmentCloud
[Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china)          | AzureChinaCloud
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | AzureGermanCloud

### <a name="getting-the-tags-for-the-vm"></a>Uzyskiwanie tagów dla maszyny Wirtualnej

Tagi mogły zostać zastosowane do maszyny Wirtualnej platformy Azure, aby logicznie zorganizować je w taksonomię. Tagi przypisane do maszyny Wirtualnej można pobrać przy użyciu poniższego żądania.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tags?api-version=2018-10-01&format=text"
```

**Odpowiedzi**

```text
Department:IT;Environment:Test;Role:WebRole
```

Pole `tags` jest ciągiem z tagami rozdzielanych średnikami. Może to być problem, jeśli średniki są używane w samych tagach. Jeśli analizator jest zapisywany do programowo wyodrębnić tagi, `tagsList` należy polegać na polu, które jest tablicą JSON bez ograniczników, a w konsekwencji łatwiejsze do przeanalizowania.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/tagsList?api-version=2019-06-04&format=JSON"
```

**Odpowiedzi**

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

Dostawcy portalu Marketplace chcą mieć pewność, że ich oprogramowanie jest licencjonowane do uruchamiania tylko na platformie Azure. Jeśli ktoś kopiuje wirtualny dysk wideo do lokalnego, powinien mieć możliwość wykrycia tego. Wywołując usługę metadanych wystąpienia, dostawcy portalu Marketplace mogą uzyskać podpisane dane, które gwarantują odpowiedź tylko z platformy Azure.

> [!NOTE]
> Wymaga jq do zainstalowania.

**Żądanie**

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

 **Odpowiedzi**

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
nonce | Użytkownik dostarczył opcjonalny ciąg z żądaniem. Jeśli w żądaniu nie podano żadnych eksce, zwracany jest bieżący znacznik czasu UTC
plan | [Planowanie](https://docs.microsoft.com/rest/api/compute/virtualmachines/createorupdate#plan) maszyny wirtualnej w obrazie portalu Azure Marketplace zawiera nazwę, produkt i wydawcę
sygnatura czasowa/createdOn | Sygnatura czasowa UTC, przy której utworzono pierwszy podpisany dokument
sygnatura czasowa/data wygaśnięciaOn | Sygnatura czasowa UTC, na której podpisany dokument wygasa
Mieszek maszyn wirtualnych |  [Unikatowy identyfikator](https://azure.microsoft.com/blog/accessing-and-using-azure-vm-unique-id/) maszyny Wirtualnej
subscriptionId | Subskrypcja platformy Azure dla maszyny wirtualnej, wprowadzona w`2019-04-30`
sku | Określona jednostka SKU dla obrazu maszyny Wirtualnej,`2019-11-01`

#### <a name="verifying-the-signature"></a>Weryfikowanie podpisu

Po uzyskaniu podpisu powyżej, można sprawdzić, czy podpis jest od firmy Microsoft. Można również zweryfikować certyfikat pośredni i łańcuch certyfikatów. Na koniec można sprawdzić, czy identyfikator subskrypcji jest poprawny.

> [!NOTE]
> Certyfikat dla chmury publicznej i suwerennej chmury będzie inny.

 Chmura | Certyfikat
---------|-----------------
[Wszystkie ogólnie dostępne globalne regiony platformy Azure](https://azure.microsoft.com/regions/)     | *.metadata.azure.com
[Azure Government](https://azure.microsoft.com/overview/clouds/government/)              | *.metadata.azure.us
[Azure w Chinach — 21Vianet](https://azure.microsoft.com/global-infrastructure/china/)         | *.metadata.azure.cn
[Azure (Niemcy)](https://azure.microsoft.com/overview/clouds/germany/)                    | *.metadata.microsoftazure.de

Istnieje znany problem wokół certyfikatu używanego do podpisywania. Certyfikaty mogą nie mieć `metadata.azure.com` dokładnego dopasowania dla chmury publicznej. W związku z tym sprawdzanie poprawności `.metadata.azure.com` certyfikacji należy zezwolić na nazwę pospolitą z dowolnej poddomeny.

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

W przypadkach, gdy nie można pobrać certyfikatu pośredniego z powodu ograniczeń sieciowych podczas sprawdzania poprawności, certyfikat pośredni można przypiąć. Jednak platforma Azure będzie przewracać certyfikaty zgodnie ze standardową praktyką infrastruktury kluczy publicznych. Przypięte certyfikaty będą musiały zostać zaktualizowane, gdy nastąpi przerzucie. Za każdym razem, gdy planowana jest zmiana w celu zaktualizowania certyfikatu pośredniego, blog platformy Azure zostanie zaktualizowany, a klienci platformy Azure zostaną powiadomieni. Certyfikaty pośrednie można znaleźć [tutaj](https://www.microsoft.com/pki/mscorp/cps/default.htm). Certyfikaty pośrednie dla każdego z regionów mogą być różne.

> [!NOTE]
> Certyfikat pośredni dla platformy Azure China 21Vianet będzie pochodził z głównego urzędu certyfikacji DigiCert zamiast z Baltimore.
Również jeśli przypięte certyfikaty pośrednie dla platformy Azure w Chinach w ramach zmiany urzędu łańcucha głównego, certyfikaty pośrednie będą musiały zostać zaktualizowane.

### <a name="failover-clustering-in-windows-server"></a>Klastrowanie trybu failover w systemie Windows Server

W przypadku niektórych scenariuszy podczas wykonywania zapytań usługi metadanych wystąpienia za pomocą klastrowania trybu failover konieczne jest dodanie trasy do tabeli routingu.

1. Otwórz wiersz polecenia z uprawnieniami administratora.

2. Uruchom następujące polecenie i zanotuj adres`0.0.0.0`interfejsu dla miejsca docelowego sieci ( ) w tabeli trasy IPv4.

```bat
route print
```

> [!NOTE]
> Poniższy przykładowy wynik z maszyny Wirtualnej systemu Windows Server z włączonym klastrem trybu failover zawiera tylko tabelę tras IPv4 dla uproszczenia.

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

1. Uruchom następujące polecenie i użyj adresu interfejsu dla`0.0.0.0`miejsca docelowego sieci ( ), który jest (`10.0.1.10`) w tym przykładzie.

```bat
route add 169.254.169.254/32 10.0.1.10 metric 1 -p
```

### <a name="storage-profile"></a>Profil magazynu

Usługa metadanych wystąpienia może podać szczegółowe informacje o dyskach magazynu skojarzonych z maszyną wirtualną. Te dane można znaleźć w wystąpieniu/compute/storagePunkt końcowy profilu.

Profil magazynu maszyny Wirtualnej jest podzielony na trzy kategorie - odwołanie do obrazu, dysk systemu operacyjnego i dyski z danymi.

Obiekt odniesienia do obrazu zawiera następujące informacje o obrazie systemu operacyjnego:

Dane    | Opis
--------|-----------------
id      | Identyfikator zasobu
offer   | Oferta platformy lub wizerunku rynku
wydawca | Wydawca obrazu
sku     | Obraz sku
version | Wersja platformy lub obrazu na rynku

Obiekt dysku systemu operacyjnego zawiera następujące informacje o dysku systemu operacyjnego używanym przez maszynę wirtualną:

Dane    | Opis
--------|-----------------
Buforowanie | Wymagania dotyczące buforowania
tworzenieopcji | Informacje o tym, jak utworzono maszynę wirtualną
diffDiskSettings | Ustawienia dysku efemerycznego
dyskSizeGB | Rozmiar dysku w GB
image   | Źródłowy obraz użytkownika wirtualny dysk twardy
Jednostki lun     | Numer jednostki logicznej dysku
zarządzanyDisk | Parametry dysku zarządzanego
name    | Nazwa dysku
Vhd     | Wirtualny dysk twardy
writeAcceleratorEnabled | Czy writeAccelerator jest włączony na dysku

Tablica dysków danych zawiera listę dysków danych dołączonych do maszyny Wirtualnej. Każdy obiekt dysku danych zawiera następujące informacje:

Dane    | Opis
--------|-----------------
Buforowanie | Wymagania dotyczące buforowania
tworzenieopcji | Informacje o tym, jak utworzono maszynę wirtualną
diffDiskSettings | Ustawienia dysku efemerycznego
dyskSizeGB | Rozmiar dysku w GB
szyfrowanieStawy | Ustawienia szyfrowania dysku
image   | Źródłowy obraz użytkownika wirtualny dysk twardy
zarządzanyDisk | Parametry dysku zarządzanego
name    | Nazwa dysku
osType  | Typ systemu operacyjnego dołączonego do dysku
Vhd     | Wirtualny dysk twardy
writeAcceleratorEnabled | Czy writeAccelerator jest włączony na dysku

Poniżej przedstawiono przykład sposobu wykonywania zapytań o informacje magazynu maszyny Wirtualnej.

**Żądanie**

```bash
curl -H Metadata:true "http://169.254.169.254/metadata/instance/compute/storageProfile?api-version=2019-06-01"
```

**Odpowiedzi**

> [!NOTE]
> Odpowiedź jest ciągiem JSON. Poniższa przykładowa odpowiedź jest dość wydrukowana w celu uzyskania czytelności.

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

### <a name="examples-of-calling-metadata-service-using-different-languages-inside-the-vm"></a>Przykłady wywoływania usługi metadanych przy użyciu różnych języków wewnątrz maszyny Wirtualnej 

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
Perl       | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.pl
Java       | https://github.com/Microsoft/azureimds/blob/master/imdssample.java
Visual Basic | https://github.com/Microsoft/azureimds/blob/master/IMDSSample.vb
Puppet | https://github.com/keirans/azuremetadata

## <a name="faq"></a>Często zadawane pytania

1. Pojawia się błąd `400 Bad Request, Required metadata header not specified`. Co to oznacza?
   * Usługa metadanych wystąpienia `Metadata: true` wymaga, aby nagłówek był przekazywany w żądaniu. Przekazywanie tego nagłówka w wywołaniu REST umożliwia dostęp do usługi metadanych wystąpienia.
2. Dlaczego nie oszukuję informacji obliczeniowych dla mojej maszyny Wirtualnej?
   * Obecnie usługa metadanych wystąpienia obsługuje tylko wystąpienia utworzone za pomocą usługi Azure Resource Manager. W przyszłości może zostać dodana obsługa maszyn wirtualnych usługi w chmurze.
3. Utworzono maszynę wirtualną za pośrednictwem usługi Azure Resource Manager jakiś czas temu. Dlaczego nie widzę informacji o metadanych obliczeniowych?
   * W przypadku wszystkich maszyn wirtualnych utworzonych po wrześniu 2016 r. dodaj [tag,](../../azure-resource-manager/management/tag-resources.md) aby rozpocząć wyświetlanie metadanych obliczeń. W przypadku starszych maszyn wirtualnych (utworzonych przed wrześniem 2016 r.) dodaj/usuń rozszerzenia lub dyski danych do maszyny Wirtualnej, aby odświeżyć metadane.
4. Nie widzę wszystkich danych wypełnionych dla nowej wersji
   * W przypadku wszystkich maszyn wirtualnych utworzonych po wrześniu 2016 r. dodaj [tag,](../../azure-resource-manager/management/tag-resources.md) aby rozpocząć wyświetlanie metadanych obliczeń. W przypadku starszych maszyn wirtualnych (utworzonych przed wrześniem 2016 r.) dodaj/usuń rozszerzenia lub dyski danych do maszyny Wirtualnej, aby odświeżyć metadane.
5. Dlaczego pojawia się `500 Internal Server Error`błąd?
   * Ponów próbę żądania na podstawie wykładniczego wycofywania systemu. Jeśli problem będzie się powtarzał, skontaktuj się z pomocą techniczną platformy Azure.
6. Gdzie mogę udostępnić dodatkowe pytania/komentarze?
   * Wyślij swoje https://feedback.azure.comkomentarze na .
7. Czy to działa dla wystąpienia zestawu skalowania maszyny wirtualnej?
   * Usługa Yes Metadata jest dostępna dla wystąpień zestawu skalowania.
8. Jak uzyskać pomoc techniczną dla usługi?
   * Aby uzyskać pomoc techniczną dla usługi, utwórz problem z pomocą techniczną w witrynie Azure portal dla maszyny Wirtualnej, gdzie nie można uzyskać odpowiedzi na metadane po długich ponownych prób.
9. Otrzymuję czas na moje połączenie z serwisem?
   * Wywołania metadanych muszą być nawiązywały z podstawowego adresu IP przypisanego do podstawowej karty sieciowej maszyny Wirtualnej, a ponadto w przypadku zmiany tras musi istnieć trasa dla adresu 169.254.0.0/16 z karty sieciowej.
10. Zaktualizowałem tagi w zestawie skalowania maszyny wirtualnej, ale nie są one wyświetlane w wystąpieniach w przeciwieństwie do maszyn wirtualnych?
    * Obecnie dla ScaleSets tagi tylko pokazać maszynie wirtualnej na ponowne uruchomienie/reimage/lub zmiany dysku do wystąpienia.

    ![Obsługa metadanych wystąpień](./media/instance-metadata-service/InstanceMetadata-support.png)

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [zaplanowanych wydarzeniach](scheduled-events.md)
