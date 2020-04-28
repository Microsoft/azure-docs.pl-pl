---
author: cephalin
ms.service: app-service-web
ms.topic: include
ms.date: 11/03/2016
ms.author: cephalin
ms.openlocfilehash: 5bde217601d27129e044b64d90184727ea717950
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/26/2020
ms.locfileid: "67183039"
---
Przy użyciu usługi Azure Resource Manager można zdefiniować parametry dla wartości, które mają zostać uwzględnione podczas wdrażania szablonu. Szablon zawiera sekcję o nazwie Parameters, która zawiera wszystkie wartości parametrów.
Należy zdefiniować parametr dla tych wartości, które będą się różnić w zależności od wdrażanego projektu lub w oparciu o środowisko, w którym wdrażasz. Nie należy definiować parametrów dla wartości, które zawsze pozostają takie same. Każda wartość parametru używana w szablonie definiuje wdrażane zasoby. 

Podczas definiowania parametrów należy użyć pola **allowedValues** , aby określić wartości, które użytkownik może podać podczas wdrażania. Użyj pola **DefaultValue** , aby przypisać wartość do parametru, jeśli żadna wartość nie zostanie podana podczas wdrażania.

Opiszemy każdy parametr w szablonie.

### <a name="sitename"></a>siteName
Nazwa aplikacji sieci Web, którą chcesz utworzyć.

    "siteName":{
      "type":"string"
    }

### <a name="hostingplanname"></a>hostingPlanName
Nazwa planu App Service, który ma być używany do hostowania aplikacji sieci Web.

    "hostingPlanName":{
      "type":"string"
    }

### <a name="sku"></a>sku
Warstwa cenowa planu hostingu.

    "sku": {
      "type": "string",
      "allowedValues": [
        "F1",
        "D1",
        "B1",
        "B2",
        "B3",
        "S1",
        "S2",
        "S3",
        "P1",
        "P2",
        "P3",
        "P4"
      ],
      "defaultValue": "S1",
      "metadata": {
        "description": "The pricing tier for the hosting plan."
      }
    }

Szablon definiuje wartości, które są dozwolone dla tego parametru, i przypisuje wartość domyślną (S1), jeśli nie określono żadnej wartości.

### <a name="workersize"></a>workerSize
Rozmiar wystąpienia planu hostingu (mały, średni lub duży).

    "workerSize":{
      "type":"string",
      "allowedValues":[
        "0",
        "1",
        "2"
      ],
      "defaultValue":"0"
    }

Szablon definiuje wartości dozwolone dla tego parametru (0, 1 lub 2) i przypisuje wartość domyślną (0), jeśli nie określono wartości. Wartości odpowiadają małym, średnim i dużym.

