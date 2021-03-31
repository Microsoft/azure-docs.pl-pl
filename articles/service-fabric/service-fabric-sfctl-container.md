---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — kontener sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń kontenerów.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: f82883b68ab911fb0b89fc117d9a9d77e05a781a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "86245895"
---
# <a name="sfctl-container"></a>sfctl container
Uruchom polecenia związane z kontenerem w węźle klastra.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| Invoke-API | Wywołaj interfejs API kontenera dla kontenera wdrożonego w węźle Service Fabric dla danego pakietu kodu. |
| dzienniki | Pobiera dzienniki kontenerów dla kontenera wdrożonego w węźle Service Fabric. |

## <a name="sfctl-container-invoke-api"></a>Wywołanie kontenera sfctl — interfejs API
Wywołaj interfejs API kontenera dla kontenera wdrożonego w węźle Service Fabric dla danego pakietu kodu.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Code-Package-instance-ID [wymagane] | Identyfikator, który jednoznacznie identyfikuje wystąpienie pakietu kodu wdrożone w węźle usługi Service Fabric. <br><br> Może być pobrany przez "kod usługi — lista pakietów". |
| --Code-package-name [wymagane] | Nazwa pakietu kodu określona w manifeście usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --Container-API-URI-Path [wymagane] | Ścieżka identyfikatora URI interfejsu API REST kontenera, użyj "{ID}" zamiast nazwy kontenera/identyfikatora. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Service-manifest-Name [wymagane] | Nazwa manifestu usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --kontener — treść | Treść żądania HTTP dla interfejsu API REST kontenera. |
| --Container-API-Content-Type | Typ zawartości dla interfejsu API REST kontenera — wartość domyślna to "Application/JSON". |
| --Container-API-http-Verb | Zlecenie HTTP dla interfejsu API REST kontenera; wartość domyślna to GET. |
| --timeout-t | Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-container-logs"></a>sfctl dzienników kontenerów
Pobiera dzienniki kontenerów dla kontenera wdrożonego w węźle Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --ID aplikacji [wymagane] | Tożsamość aplikacji. <br><br> Jest to zazwyczaj pełna nazwa aplikacji bez schematu identyfikatora URI "Sieć szkieletowa \: ". Począwszy od wersji 6,0, hierarchiczne nazwy są rozdzielane \~ znakami "". Na przykład jeśli nazwa aplikacji to "Fabric \: /MyApp/APP1", tożsamość aplikacji byłaby "MojaApl \~ APP1" w wersji 6.0 + i "MojaApl/APP1" w poprzednich wersjach. |
| --Code-Package-instance-ID [wymagane] | Identyfikator wystąpienia pakietu kodu, który może zostać pobrany przez "kod usługi — pakiet-lista". |
| --Code-package-name [wymagane] | Nazwa pakietu kodu określona w manifeście usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --Node-Name [wymagane] | Nazwa węzła. |
| --Service-manifest-Name [wymagane] | Nazwa manifestu usługi zarejestrowana w ramach typu aplikacji w klastrze Service Fabric. |
| --tail | Liczba wierszy do wyświetlenia na końcu dzienników. Wartość domyślna to 100. "wszystkie", aby wyświetlić kompletne dzienniki. |
| --timeout-t | Wartość domyślna \: 60. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |


## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](./scripts/sfctl-upgrade-application.md).
