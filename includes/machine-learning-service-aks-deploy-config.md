---
author: Blackmist
ms.service: machine-learning
ms.topic: include
ms.date: 03/16/2020
ms.author: larryfr
ms.openlocfilehash: c71f35a06d904b45cb014d5199197220b57cf230
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "79486060"
---
Wpisy w dokumencie są `deploymentconfig.json` mapowane na parametry [AksWebservice.deploy_configuration](https://docs.microsoft.com/python/api/azureml-core/azureml.core.webservice.aks.aksservicedeploymentconfiguration?view=azure-ml-py). W poniższej tabeli opisano mapowanie między jednostkami w dokumencie JSON a parametrami metody:

| Jednostka JSON | Parametr metody | Opis |
| ----- | ----- | ----- |
| `computeType` | Nie dotyczy | Docelowy zasób obliczeniowy. Dla AKS wartość musi być `aks` . |
| `autoScaler` | Nie dotyczy | Zawiera elementy konfiguracji skalowania automatycznego. Zobacz tabelę skalowania automatycznego. |
| &emsp;&emsp;`autoscaleEnabled` | `autoscale_enabled` | Określa, czy włączyć skalowanie automatyczne dla usługi sieci Web. Jeśli `numReplicas`  =  `0` `True` ; w przeciwnym razie, `False` . |
| &emsp;&emsp;`minReplicas` | `autoscale_min_replicas` | Minimalna liczba kontenerów, które mają być używane podczas automatycznego skalowania tej usługi sieci Web. Wartość domyślna `1` . |
| &emsp;&emsp;`maxReplicas` | `autoscale_max_replicas` | Maksymalna liczba kontenerów, które mają być używane podczas automatycznego skalowania tej usługi sieci Web. Wartość domyślna `10` . |
| &emsp;&emsp;`refreshPeriodInSeconds` | `autoscale_refresh_seconds` | Jak często automatyczne skalowanie próbuje skalować tę usługę sieci Web. Wartość domyślna `1` . |
| &emsp;&emsp;`targetUtilization` | `autoscale_target_utilization` | Użycie docelowe (w procentach z 100), które ma być podejmowane przez Autoskalowanie dla tej usługi sieci Web. Wartość domyślna `70` . |
| `dataCollection` | Nie dotyczy | Zawiera elementy konfiguracji do zbierania danych. |
| &emsp;&emsp;`storageEnabled` | `collect_model_data` | Określa, czy włączyć zbieranie danych modelu dla usługi sieci Web. Wartość domyślna `False` . |
| `authEnabled` | `auth_enabled` | Określa, czy należy włączyć uwierzytelnianie klucza dla usługi sieci Web. Oba `tokenAuthEnabled` i `authEnabled` nie mogą być `True` . Wartość domyślna `True` . |
| `tokenAuthEnabled` | `token_auth_enabled` | Określa, czy włączyć uwierzytelnianie tokenu dla usługi sieci Web. Oba `tokenAuthEnabled` i `authEnabled` nie mogą być `True` . Wartość domyślna `False` . |
| `containerResourceRequirements` | Nie dotyczy | Kontener dla jednostek procesora i pamięci. |
| &emsp;&emsp;`cpu` | `cpu_cores` | Liczba rdzeni procesora CPU do przydzielenia dla tej usługi sieci Web. Wartości domyślne `0.1` |
| &emsp;&emsp;`memoryInGB` | `memory_gb` | Ilość pamięci (w GB) do przydzielenia dla tej usługi sieci Web. Wartooć `0.5` |
| `appInsightsEnabled` | `enable_app_insights` | Określa, czy należy włączyć rejestrowanie Application Insights dla usługi sieci Web. Wartość domyślna `False` . |
| `scoringTimeoutMs` | `scoring_timeout_ms` | Limit czasu wymuszania dla wywołań oceniania do usługi sieci Web. Wartość domyślna `60000` . |
| `maxConcurrentRequestsPerContainer` | `replica_max_concurrent_requests` | Maksymalna liczba współbieżnych żądań na węzeł dla tej usługi sieci Web. Wartość domyślna `1` . |
| `maxQueueWaitMs` | `max_request_wait_time` | Maksymalny czas pozostawania żądania w kolejce Thee (w milisekundach) przed zwróceniem błędu 503. Wartość domyślna `500` . |
| `numReplicas` | `num_replicas` | Liczba kontenerów do przydzielenia dla tej usługi sieci Web. Brak wartości domyślnej. Jeśli ten parametr nie jest ustawiony, automatyczne skalowanie jest domyślnie włączone. |
| `keys` | Nie dotyczy | Zawiera elementy konfiguracji dla kluczy. |
| &emsp;&emsp;`primaryKey` | `primary_key` | Podstawowy klucz uwierzytelniania, który ma być używany dla tej usługi sieci Web |
| &emsp;&emsp;`secondaryKey` | `secondary_key` | Pomocniczy klucz uwierzytelniania, który będzie używany przez tę usługę sieci Web |
| `gpuCores` | `gpu_cores` | Liczba rdzeni procesora GPU (dla repliki kontenerów) do przydzielenia dla tej usługi sieci Web. Domyślna wartość wynosi 1. Obsługuje tylko wartości całkowite. |
| `livenessProbeRequirements` | Nie dotyczy | Zawiera elementy konfiguracji dla wymagań sondowania na żywo. |
| &emsp;&emsp;`periodSeconds` | `period_seconds` | Jak często (w sekundach) przeprowadzenia sondy na żywo. Wartość domyślna to 10 sekund. Wartość minimalna to 1. |
| &emsp;&emsp;`initialDelaySeconds` | `initial_delay_seconds` | Liczba sekund od momentu rozpoczęcia sondowania na żywo przez kontener. Wartość domyślna to 310 |
| &emsp;&emsp;`timeoutSeconds` | `timeout_seconds` | Liczba sekund, po upływie których limit czasu sondy jest aktywny. Wartość domyślna to 2 sekundy. Wartość minimalna to 1 |
| &emsp;&emsp;`successThreshold` | `success_threshold` | Minimalna liczba kolejnych sukcesów sondy na żywo, które mają być uznawane za pomyślne po zakończonym niepowodzeniem. Wartość domyślna to 1. Wartość minimalna to 1. |
| &emsp;&emsp;`failureThreshold` | `failure_threshold` | Gdy zostanie uruchomiony pod i sonda na żywo, Kubernetes spróbuje failureThreshold razy przed pokazaniem. Wartość domyślna to 3. Wartość minimalna to 1. |
| `namespace` | `namespace` | Przestrzeń nazw Kubernetes, w której wdrażana jest usługa WebService. Do 63 małych cyfr alfanumerycznych ("od-a", "0"-"9") i łączników ("-"). Pierwszy i ostatni znak nie mogą być łącznikami. |

Poniższy kod JSON jest przykładową konfiguracją wdrożenia do użycia z interfejsem wiersza polecenia:

```json
{
    "computeType": "aks",
    "autoScaler":
    {
        "autoscaleEnabled": true,
        "minReplicas": 1,
        "maxReplicas": 3,
        "refreshPeriodInSeconds": 1,
        "targetUtilization": 70
    },
    "dataCollection":
    {
        "storageEnabled": true
    },
    "authEnabled": true,
    "containerResourceRequirements":
    {
        "cpu": 0.5,
        "memoryInGB": 1.0
    }
}
```
