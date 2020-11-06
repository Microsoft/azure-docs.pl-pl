---
title: Azure Stack krawędź aktualizacji z dniem styczeń 2021 | Microsoft Docs
description: Opisuje wpływ IoT Edge zarządzania rolami na Azure Stack urządzenia brzegowe po zainstalowaniu aktualizacji z stycznia 2021.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: 4b54f75b7d90e4b3a0a11d2ecdc676bb48eeee99
ms.sourcegitcommit: 2a8a53e5438596f99537f7279619258e9ecb357a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/06/2020
ms.locfileid: "94335686"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge"></a>IoT Edge zmiany zarządzania rolami dla Azure Stack Edge

Aby uzyskać IoT Edge Zarządzanie rolami dla urządzenia brzegowego Azure Stack, użyj najnowszej wersji interfejsu API, zestawu SDK i Azure PowerShell z powodu wydania w styczniu 2021. W tym artykule szczegółowo opisano zmiany wymagane w przypadku korzystania z najnowszej wersji interfejsu API, zestawu SDK i poleceń cmdlet programu PowerShell na potrzeby IoT Edge zarządzania rolami na Azure Stack brzegowej.

Aktualizacja z stycznia 2021 jest dostępna tylko dla Azure Stack EDGE Pro-GPU, Azure Stack EDGE Pro R i Azure Stack Edge mini R, a informacje przedstawione w tym artykule mają zastosowanie tylko do tych urządzeń. 

## <a name="iot-edge-role-management-changes"></a>IoT Edge zmiany zarządzania rolami

Po zainstalowaniu opcjonalnej wersji oprogramowania urządzenia z styczeń 2021 na urządzeniu z systemem Azure Stack Edge wymagane jest użycie najnowszej wersji interfejsu API, zestawu SDK i poleceń cmdlet programu PowerShell do zarządzania rolami IoT Edge.

- Jeśli używasz interfejsu API zarządzania rolami z wersją 2019-08-01, Uaktualnij do wersji interfejsu API w styczniu 2021. 
- Jeśli używasz zarządzania rolami za pomocą zestawu SDK 1.0.0, Uaktualnij do wersji w styczniu 2021.
- Jeśli używasz zarządzania rolami za pomocą poleceń cmdlet Azure PowerShell (wersja zapoznawcza) `Get-AzStackEdgeRole` , takich jak,, `New-AzStackEdgeRole` `Set-AzStackEdgeRole` lub, musisz `Remove-AzStackEdgeRole` poczekać, aż nowe polecenia cmdlet zostaną wydane w lutym 2021.

Powyższe zmiany są wymagane tylko w przypadku zastosowania aktualizacji z stycznia 2021. Jeśli pozostaniesz w istniejącej wersji oprogramowania urządzenia, nie ma to wpływu na Zarządzanie rolą IoT Edge. Zaleca się jednak Aktualizowanie urządzenia pod kątem nowych funkcji i ulepszeń zabezpieczeń. 


## <a name="api-usage"></a>Użycie interfejsu API

Jeśli wykonano IoT Edge Zarządzanie rolami za pomocą interfejsu API, należy użyć nowej wersji interfejsu API (2020-12-01), która zostanie opublikowana później. Jeśli korzystasz z interfejsu API usługi Current i masz zainstalowaną przyszłą wersję oprogramowania urządzenia, musisz przejść do roli PUT, GET, DELETE Kubernetes oraz umieścić interfejs API dodatku IoT.


### <a name="for-put-method"></a>Dla metody PUT

#### <a name="current-http-request"></a>Bieżące żądanie http 

- Wywołania interfejsu API są nawiązywane przy użyciu tego identyfikatora URI: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 "

- Treść żądania wygląda następująco:

    ```json
    {
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        }
    }
    ```

#### <a name="upcoming-http-request"></a>Nadchodzące żądanie http 

- Wywołania interfejsu API dla roli Kubernetes są nawiązywane przy użyciu następującego identyfikatora URI: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01 "

    Treść żądania wygląda następująco:

    ```json
    {
        "kind": "Kubernetes",
        "properties": {
            "hostPlatform": "Linux",
            "kubernetesClusterInfo": {
                "version": "v1.17.3"
            },
            "kubernetesRoleResources": {
                "storage": {
                    "endpoints": []
                },
                "compute": {
                    "vmProfile": "DS1_v2"
                }
            }
        }
    }
    ```

- Wywołania interfejsu API dla dodatku IoT Edge są nawiązywane przy użyciu następującego identyfikatora URI: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 "


    Treść żądania wygląda następująco:

    ```json
    {
        "kind": "IoT",
        "properties": {
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotDevice;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "348586569999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {
                        "connectionString": {
                            "value": "Encrypted<<HostName=iothub.azure-devices.net;DeviceId=iotEdge;SharedAccessKey=2C750FscEas3JmQ8Bnui5yQWZPyml0/UiRt1bQwd8=>>",
                            "encryptionCertThumbprint": "1245475856069999244",
                            "encryptionAlgorithm": "AES256"
                        }
                    }
                }
            }
        }
    }
    ```


### <a name="for-get-method"></a>For GET — Metoda

#### <a name="current-http-response"></a>Bieżąca odpowiedź http

- Wywołania interfejsu API są nawiązywane przy użyciu następującego identyfikatora URI: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 "


- Treść odpowiedzi wygląda następująco:

    ```json
        "kind": "IOT",
        "properties": {
            "hostPlatform": "Linux",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "shareMappings": [],
            "roleStatus": "Enabled"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1",
        "name": "IoTRole1",
        "type": "dataBoxEdgeDevices/roles"
    }    
    ```

#### <a name="upcoming-http-response"></a>Nadchodząca odpowiedź http

- Wywołania interfejsu API są nawiązywane przy użyciu następującego identyfikatora URI: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 "
- Treść odpowiedzi wygląda następująco: 

    ```json
    {
        "kind": "IoT",
        "properties": {
            "provisioningState": "Creating",
            "ioTDeviceDetails": {
                "deviceId": "iotdevice",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "ioTEdgeDeviceDetails": {
                "deviceId": "iotEdge",
                "ioTHostHub": "iothub.azure-devices.net",
                "ioTHostHubId": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/Microsoft.Devices/IotHubs/testrxiothub",
                "authentication": {
                    "symmetricKey": {}
                }
            },
            "version": "0.1.0-beta10"
        },
        "id": "/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/res1/roles/kubernetesRole/addons/iotName",
        "name": " iotName",
        "type": "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addon",
    }
    ```

### <a name="for-delete-method"></a>For DELETE — Metoda

### <a name="current"></a>Current

Wywołania interfejsu API są nawiązywane przy użyciu następującego identyfikatora URI: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01 "

### <a name="upcoming"></a>Nowego

Wywołania interfejsu API są nawiązywane przy użyciu następującego identyfikatora URI: " https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01 "


## <a name="sdk-usage"></a>SDK usage (Użycie zestawu SDK)

Jeśli używasz zestawu SDK i zainstalowano aktualizację urządzenia z styczeń 2021, należy zmienić sposób konfigurowania roli IoT Edge, jak pokazano w poniższej tabeli. Następnie należy pobrać i zainstalować nadchodzący pakiet NuGet, aby przejść do nowego zestawu SDK, jak pokazano w poniższym przykładzie.

### <a name="current-sdk-sample"></a>Bieżący przykład zestawu SDK

```csharp
var iotRoleStatus = "Enabled";
var iotHostPlatform = "Linux";
var id = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/iotrole"; 
var name = "iotrole";
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var iotRoleName = "iotrole";
var ioTDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeDeviceDetails = new IoTDeviceInfo(...);
var ioTEdgeAgentInfo = new IoTEdgeAgentInfo(...);
var shareMappings = new List<MountPointMap>(...);

var role = new IoTRole(roleStatus, 
    hostPlatform, 
    shareMappings, 
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    id, 
    name, 
    type);

DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, iotRoleName, role, resourceGroup);
```


### <a name="new-sdk-sample"></a>Nowy przykład zestawu SDK

```csharp
var k8sRoleStatus = "Enabled";
var k8sHostPlatform = "Linux";
var k8sId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole"; 
var k8sRoleName = "KubernetesRole";
var k8sClusterVersion = "v1.17.3"; //Final values will be updated here around January 2021
var k8sVmProfile = "DS1_v2"; //Final values will be updated here around January 2021
var type = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/role";
var k8sRole = new KubernetesRole(
    roleStatus,
    hostPlatform,
    shareMappings,
    k8sClusterVersion,
    k8sVmProfile,
    k8sId,
    k8sRoleName,
    type
);
DataBoxEdgeManagementClient.Roles.CreateOrUpdate(deviceName, k8sRoleName, k8sRole, resourceGroup); //Final usage will be updated here around January 2021

var ioTId = $@"/subscriptions/546ec571-2d7f-426f-9cd8-0d695fa7edba/resourceGroups/resourceGroup/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/deviceName/roles/KubernetesRole/addons/iotaddon";
var ioTAddonName = "iotaddon";
var ioTAddonType = "Microsoft.DataBoxEdge/dataBoxEdgeDevices/roles/addons";
var addon = new IoTAddon(
    ioTDeviceDetails, 
    ioTEdgeDeviceDetails, 
    ioTEdgeAgentInfo, 
    ioTId, 
    ioTAddonName, 
    ioTAddonType);
DataBoxEdgeManagementClient.AddOns.CreateOrUpdate(deviceName, k8sRoleName, addonName, addon, resourceGroup); //Final usage will be updated here around January 2021
```

## <a name="cmdlet-usage"></a>Użycie polecenia cmdlet

W przypadku korzystania z `Get-AzStackEdgeRole` `New-AzStackEdgeRole` `Set-AzStackEdgeRole` poleceń cmdlet,, lub `Remove-AzStackEdgeRole` , należy oczekiwać na zaplanowanie nowej wersji w lutym 2021.

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

#### <a name="i-am-using-azure-stack-edge-pro--fpga-does-the-january-2021-update-affect-the-fpga-model"></a>Używam programu Azure Stack EDGE Pro – FPGA. Czy aktualizacja z stycznia 2021 ma wpływ na model FPGA?

Nie. Aktualizacja z stycznia 2021 ma zastosowanie tylko do Azure Stack Edge — GPU, Azure Stack EDGE Pro R i Azure Stack. Ta aktualizacja nie ma wpływ na Azure Stack EDGE Pro – FPGA i nie wymaga żadnej zmiany IoT Edge zarządzaniem rolami.

#### <a name="after-i-update-my-azure-stack-edge-pro---gpu-to-the-new-device-software-in-january-2021-are-any-of-the-existing-services-affected"></a>Czy po zaktualizowaniu usługi Azure Stack EDGE Pro-GPU do nowego oprogramowania urządzenia w 2021 styczniu

Nie. Po zainstalowaniu aktualizacji urządzenia z lipca 2021 nie wpłynie to na skonfigurowane usługi. <!--check w/ Anoob, what existing services you are talking about in this question-->

#### <a name="what-are-the-high-level-changes-to-iot-edge-management-api-sdk-or-cmdlet"></a>Jakie są zmiany wysokiego poziomu do IoT Edge Management API, SDK lub polecenia CmdLet?

IoT Edge jest dodatkiem w ramach roli Kubernetes. To oznacza, że trzeba upewnić się, że Kubernetes jest skonfigurowany jako pierwszy, a następnie wykonać konfigurację IoT Edge.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [stosować aktualizacje](azure-stack-edge-gpu-install-update.md).

