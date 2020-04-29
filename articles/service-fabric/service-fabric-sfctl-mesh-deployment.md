---
title: Interfejs wiersza polecenia platformy Azure Service Fabric — wdrożenie siatki sfctl
description: Dowiedz się więcej na temat sfctl, interfejsu wiersza polecenia platformy Azure Service Fabric. Zawiera listę poleceń do tworzenia zasobów siatki Service Fabric.
author: jeffj6123
ms.topic: reference
ms.date: 1/16/2020
ms.author: jejarry
ms.openlocfilehash: 108389407221779ed20e81310f084b7b5c23b8c7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76906025"
---
# <a name="sfctl-mesh-deployment"></a>sfctl mesh deployment
Utwórz zasoby siatki Service Fabric.

## <a name="commands"></a>Polecenia

|Polecenie|Opis|
| --- | --- |
| create | Tworzy wdrożenie zasobów siatki Service Fabric. |

## <a name="sfctl-mesh-deployment-create"></a>Tworzenie wdrożenia siatki sfctl
Tworzy wdrożenie zasobów siatki Service Fabric.

### <a name="arguments"></a>Argumenty

|Argument|Opis|
| --- | --- |
| --Input-YAML-Files [wymagane] | Względne lub bezwzględne ścieżki plików dla wszystkich plików YAML lub względnej lub bezwzględnej ścieżki katalogu (rekursywnie), które zawierają pliki YAML. |
| --parametry | Ścieżka względna lub bezwzględna do pliku YAML lub obiektu JSON, który zawiera parametry, które muszą zostać zastąpione. |

### <a name="global-arguments"></a>Argumenty globalne

|Argument|Opis|
| --- | --- |
| --Debug | Zwiększ Szczegółowość rejestrowania, aby pokazać wszystkie dzienniki debugowania. |
| --Help-h | Pokaż ten komunikat pomocy i Zakończ. |
| --Wyjście-o | Format danych wyjściowych.  Dozwolone wartości\: JSON, jsonc, Table, TSV.  Domyślny\: kod JSON. |
| --zapytanie | Ciąg zapytania JMESPath. Aby uzyskać\:więcej informacji i przykładów, zobacz http//jmespath.org/. |
| --verbose | Zwiększ Szczegółowość rejestrowania. Użyj--Debug dla pełnych dzienników debugowania. |

### <a name="examples"></a>Przykłady

Konsoliduje i wdraża wszystkie zasoby w klastrze, zastępując parametry wymienione w pliku YAML
``` 
sfctl mesh deployment create --input-yaml-files ./app.yaml,./network.yaml --parameters  
./param.yaml    
```

Konsoliduje i wdraża wszystkie zasoby w katalogu w klastrze, zastępując parametry wymienione w pliku YAML

``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters ./param.yaml
```

Konsoliduje i wdraża wszystkie zasoby w katalogu w klastrze przez zastąpienie parametrów, które są przesyłane bezpośrednio jako obiekt JSON
``` 
sfctl mesh deployment create --input-yaml-files ./resources --parameters "{ 'my_param' :    
{'value' : 'my_value'} }"   
```

## <a name="next-steps"></a>Następne kroki
- [Skonfiguruj](service-fabric-cli.md) interfejs wiersza polecenia Service Fabric.
- Dowiedz się, jak używać interfejsu wiersza polecenia Service Fabric przy użyciu [przykładowych skryptów](/azure/service-fabric/scripts/sfctl-upgrade-application).
