---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — usługa siatki sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń pobierania szczegółów usługi dla zasobu aplikacji.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 68ea876d9951b49a6683cc74df8b9107fd942e51
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86245674"
---
# <a name="sfctl-mesh-service"></a>sfctl mesh service
Pobierz szczegóły usługi i Wyświetl listę usług dla zasobu aplikacji.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| list | Wyświetla listę wszystkich zasobów usługi. |
| pokaż | Pobiera zasób usługi o podaną nazwę. |

## <a name="sfctl-mesh-service-list"></a>Lista usługi siatki sfctl
Wyświetla listę wszystkich zasobów usługi.

Pobiera informacje o wszystkich usługach zasobu aplikacji. Informacje obejmują opis i inne właściwości usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Name--Application-Name [wymagane] | Nazwa aplikacji. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości \: JSON, jsonc, Table, TSV.  Domyślny \: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać \: więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

## <a name="sfctl-mesh-service-show"></a>Pokaż usługę siatki sfctl
Pobiera zasób usługi o podaną nazwę.

Pobiera informacje o zasobie usługi o podanej nazwie. Informacje obejmują opis i inne właściwości usługi.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --App-Name--Application-Name [wymagane] | Nazwa aplikacji. |
| --Name-n [wymagane] | Nazwa usługi. |

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
