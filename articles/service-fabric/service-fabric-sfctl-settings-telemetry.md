---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — Telemetria ustawień sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń służących do konfigurowania telemetrii sfctl.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 6af5fa944ef399756f9e890ddd77a7f5f32e2bfb
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: pl-PL
ms.lasthandoff: 07/02/2020
ms.locfileid: "76903018"
---
# <a name="sfctl-settings-telemetry"></a>sfctl settings telemetry
Skonfiguruj ustawienia telemetrii lokalnie dla tego wystąpienia elementu sfctl.

Sfctl telemetrii zbiera nazwę polecenia bez parametrów dostarczonych lub ich wartości, wersję Sfctl, typ systemu operacyjnego, wersję języka Python, powodzenie lub niepowodzenie polecenia, zwracany komunikat o błędzie.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Set-Telemetria | Włączać lub wyłączać dane telemetryczne. |

## <a name="sfctl-settings-telemetry-set-telemetry"></a>zestaw telemetrii ustawień sfctl — Telemetria
Włączać lub wyłączać dane telemetryczne.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --off | Wyłącz telemetrię. |
| --na | Włącz telemetrię. Jest to wartość domyślna. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Wyłącz telemetrię.

```
sfctl settings telemetry set_telemetry --off
```

Włącz telemetrię.

```
sfctl settings telemetry set_telemetry --on
```


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).