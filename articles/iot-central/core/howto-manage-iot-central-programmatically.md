---
title: Zarządzanie IoT Central programowo | Microsoft Docs
description: W tym artykule opisano sposób programowego tworzenia IoT Central i zarządzania nim. Można wyświetlać, modyfikować i usuwać aplikacje przy użyciu wielu zestawów SDK języka, takich jak JavaScript, Python, C#, Ruby i go.
services: iot-central
ms.service: iot-central
author: dominicbetts
ms.author: dobett
ms.date: 12/23/2020
ms.topic: how-to
ms.openlocfilehash: 2feb4d85e7be4076eef8b2796d15b622f6b17c3a
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104658305"
---
# <a name="manage-iot-central-programmatically"></a>Zarządzanie IoT Central programowo

[!INCLUDE [iot-central-selector-manage](../../../includes/iot-central-selector-manage.md)]

Zamiast tworzyć aplikacje IoT Central i zarządzać nimi w witrynie sieci Web programu [azure IoT Central Application Manager](https://aka.ms/iotcentral) , możesz programowo zarządzać aplikacjami przy użyciu zestawów SDK platformy Azure. Obsługiwane języki to JavaScript, Python, C#, Ruby i go.

## <a name="install-the-sdk"></a>Instalacja zestawu SDK

Poniższa tabela zawiera listę repozytoriów zestawu SDK i poleceń instalacji pakietów:

| Repozytorium zestawu SDK | Instalowanie pakietu |
| -------------- | ------------ |
| [Azure IotCentralClient SDK dla języka JavaScript](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/iotcentral/arm-iotcentral) | `npm install @azure/arm-iotcentral` |
| [Zestaw SDK Microsoft Azure dla języka Python](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/iothub/azure-mgmt-iotcentral/azure/mgmt/iotcentral) | `pip install azure-mgmt-iotcentral` |
| [Zestaw Azure SDK dla platformy .NET](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/iotcentral/Microsoft.Azure.Management.IotCentral) | `dotnet add package Microsoft.Azure.Management.IotCentral` |
| [Microsoft Azure SDK dla zarządzania zasobami Ruby (wersja zapoznawcza)](https://github.com/Azure/azure-sdk-for-ruby/tree/master/management/azure_mgmt_iot_central/lib/2018-09-01/generated/azure_mgmt_iot_central) | `gem install azure_mgmt_iot_central` |
| [Zestaw Azure SDK dla języka Java](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/iotcentral) | [Pakiet Maven](https://search.maven.org/search?q=a:azure-mgmt-iotcentral) |
| [Zestaw Azure SDK dla języka Go](https://github.com/Azure/azure-sdk-for-go/tree/master/services/iotcentral/mgmt/2018-09-01/iotcentral) | [Wersje pakietów](https://github.com/Azure/azure-sdk-for-go/releases) |

## <a name="samples"></a>Samples

Repozytorium [przykładów zestawu SDK usługi Azure IoT Central ARM](/samples/azure-samples/azure-iot-central-arm-sdk-samples/azure-iot-central-arm-sdk-samples/) zawiera przykłady kodu dla wielu języków programowania, w których pokazano, jak tworzyć, aktualizować, wyświetlać i usuwać aplikacje IoT Central platformy Azure.

[!INCLUDE [Warning About Access Required](../../../includes/iot-central-warning-contribitorrequireaccess.md)]

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zarządzać aplikacjami IoT Central platformy Azure programowo, zalecanym następnym krokiem jest dowiedzenie więcej na temat usługi [Azure Resource Manager](../../azure-resource-manager/management/overview.md) .