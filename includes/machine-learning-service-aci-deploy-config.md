---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: 4983c1e1e7f235fa7a5b748a0ce5b1c79176c849
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "102511069"
---
Wpisy w dokumencie są `deploymentconfig.json` mapowane na parametry [AciWebservice.deploy_configuration](/python/api/azureml-core/azureml.core.webservice.aci.aciservicedeploymentconfiguration). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `computeType` | NA | Docelowy zasób obliczeniowy. Dla ACI wartość musi być `ACI` . |
| `containerResourceRequirements` | NA | Kontener dla jednostek procesora i pamięci. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Liczba rdzeni procesora CPU do przydzielenia. Wartości domyślne `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Ilość pamięci (w GB) do przydzielenia dla tej usługi sieci Web. Wartooć `0.5` |
| `location` | `location` | Region świadczenia usługi Azure, w którym ma zostać wdrożona ta usługa sieci Web. Jeśli nie zostanie określona, zostanie użyta Lokalizacja obszaru roboczego. Więcej szczegółów na temat dostępnych regionów można znaleźć tutaj: [regiony ACI](https://azure.microsoft.com/global-infrastructure/services/?regions=all&products=container-instances) |
| `authEnabled` | `auth_enabled` | Określa, czy włączyć uwierzytelnianie dla tej usługi sieci Web. Wartość domyślna to false |
| `sslEnabled` | `ssl_enabled` | Określa, czy włączyć protokół SSL dla tej usługi sieci Web. Wartością domyślną jest false. |
| `appInsightsEnabled` | `enable_app_insights` | Określa, czy włączyć AppInsights dla tej usługi sieci Web. Wartość domyślna to false |
| `sslCertificate` | `ssl_cert_pem_file` | Plik certyfikatu wymagany w przypadku włączenia protokołu SSL |
| `sslKey` | `ssl_key_pem_file` | Plik klucza wymagany w przypadku włączenia protokołu SSL |
| `cname` | `ssl_cname` | Rekord CNAME dla jeśli jest włączony protokół SSL |
| `dnsNameLabel` | `dns_name_label` | Etykieta nazwy DNS dla punktu końcowego oceniania. Jeśli nie zostanie określona, zostanie wygenerowana unikatowa etykieta nazwy DNS dla punktu końcowego oceniania. |

Poniższy kod JSON jest przykładową konfiguracją wdrożenia do użycia z interfejsem wiersza polecenia:

```json
{
    "computeType": "aci",
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    },
    "authEnabled": true,
    "sslEnabled": false,
    "appInsightsEnabled": false
}
```