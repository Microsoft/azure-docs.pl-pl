---
title: Zmień ustawienia aplikacji platformy Azure IoT Central | Microsoft Docs
description: Jako administrator, jak zarządzać aplikacją IoT Central platformy Azure, zmieniając nazwę aplikacji, adres URL, przekazanie obrazu i Usuń aplikację
author: viv-liu
ms.author: viviali
ms.date: 11/27/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: peterpr
ms.openlocfilehash: 7b77ea9e0e1f322cb5ef0bc63885c3ccce1b76f6
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "80158689"
---
# <a name="change-iot-central-application-settings"></a>Zmień ustawienia IoT Central aplikacji



W tym artykule opisano, jak administrator może zarządzać aplikacją przez zmianę nazwy aplikacji i adresu URL, przekazanie obrazu i usunięcie aplikacji w aplikacji IoT Central platformy Azure.

Aby uzyskać dostęp do sekcji **Administracja** i korzystać z niej, musisz mieć rolę **administratora** dla aplikacji IoT Central platformy Azure. Jeśli utworzysz aplikację IoT Central platformy Azure, zostanie ona automatycznie przypisana do roli **administratora** dla tej aplikacji.

## <a name="change-application-name-and-url"></a>Zmień nazwę i adres URL aplikacji

Na stronie **Ustawienia aplikacji** można zmienić nazwę i adres URL aplikacji, a następnie wybrać pozycję **Zapisz**.

![Strona Ustawienia aplikacji](media/howto-administer/image0-a.png)

Jeśli administrator utworzy niestandardowy motyw dla aplikacji, ta strona zawiera opcję ukrycia **nazwy aplikacji** w interfejsie użytkownika. Ta opcja jest przydatna, jeśli logo aplikacji w motywie niestandardowym zawiera nazwę aplikacji. Aby uzyskać więcej informacji, zobacz [Dostosowywanie interfejsu użytkownika usługi Azure IoT Central](./howto-customize-ui.md).

> [!Note]
> Jeśli zmienisz adres URL, stary adres URL może zostać podjęty przez innego klienta IoT Central platformy Azure. Jeśli tak się stanie, nie jest już dostępny do użycia. Gdy zmienisz adres URL, stary adres URL nie będzie już działać i musisz powiadomić użytkowników o nowym adresie URL do użycia.

## <a name="delete-an-application"></a>Usuwanie aplikacji

Aby trwale usunąć aplikację IoT Central, użyj przycisku **Usuń** . Ta akcja powoduje trwałe usunięcie wszystkich danych, które są skojarzone z aplikacją.

> [!Note]
> Aby usunąć aplikację, musisz mieć również uprawnienia do usuwania zasobów w ramach subskrypcji platformy Azure wybranej podczas tworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [Korzystanie z kontroli dostępu opartej na rolach w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](https://docs.microsoft.com/azure/active-directory/role-based-access-control-configure).

## <a name="manage-programmatically"></a>Zarządzanie programistyczne

Pakiety SDK IoT Central Azure Resource Manager są dostępne dla węzłów, Python, C#, Ruby, Java i go. Za pomocą tych pakietów można tworzyć, wyświetlać, aktualizować i usuwać IoT Central aplikacje. Pakiety obejmują pomocników do zarządzania uwierzytelnianiem i obsługą błędów.

Przykłady użycia zestawów SDK Azure Resource Manager można znaleźć w witrynie [https://github.com/emgarten/iotcentral-arm-sdk-examples](https://github.com/emgarten/iotcentral-arm-sdk-examples).

Aby dowiedzieć się więcej, zobacz następujące repozytoria i pakiety usługi GitHub:

| Język | Repozytorium | Pakiet |
| ---------| ---------- | ------- |
| Węzeł | [https://github.com/Azure/azure-sdk-for-node](https://github.com/Azure/azure-sdk-for-node) | [https://www.npmjs.com/package/azure-arm-iotcentral](https://www.npmjs.com/package/azure-arm-iotcentral)
| Python |[https://github.com/Azure/azure-sdk-for-python](https://github.com/Azure/azure-sdk-for-python) | [https://pypi.org/project/azure-mgmt-iotcentral](https://pypi.org/project/azure-mgmt-iotcentral)
| C# | [https://github.com/Azure/azure-sdk-for-net](https://github.com/Azure/azure-sdk-for-net) | [https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral](https://www.nuget.org/packages/Microsoft.Azure.Management.IotCentral)
| Ruby | [https://github.com/Azure/azure-sdk-for-ruby](https://github.com/Azure/azure-sdk-for-ruby) | [https://rubygems.org/gems/azure_mgmt_iot_central](https://rubygems.org/gems/azure_mgmt_iot_central)
| Java | [https://github.com/Azure/azure-sdk-for-java](https://github.com/Azure/azure-sdk-for-java) | [https://search.maven.org/search?q=a:azure-mgmt-iotcentral](https://search.maven.org/search?q=a:azure-mgmt-iotcentral)
| Przejdź | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go) | [https://github.com/Azure/azure-sdk-for-go](https://github.com/Azure/azure-sdk-for-go)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak administrować aplikacją IoT Central platformy Azure, sugerowanym następnym krokiem jest zapoznanie się z tematem [Zarządzanie użytkownikami i rolami](howto-manage-users-roles.md) w usłudze Azure IoT Central.
