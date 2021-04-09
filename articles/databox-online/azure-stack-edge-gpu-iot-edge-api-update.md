---
title: Azure Stack krawędź aktualizacji z dniem styczeń 2021 | Microsoft Docs
description: W tym artykule opisano wpływ IoT Edge zarządzania rolami na Azure Stack urządzenia brzegowe po zainstalowaniu aktualizacji 2021 stycznia.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: overview
ms.date: 10/26/2020
ms.author: alkohli
ms.openlocfilehash: f16f33e9aadcc01427602a1bd81f81cb0710e4dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "94578743"
---
# <a name="iot-edge-role-management-changes-for-your-azure-stack-edge-device"></a>IoT Edge zmiany zarządzania rolami dla Azure Stack urządzenia brzegowego

Aby uzyskać Azure IoT Edge Zarządzanie rolami dla urządzenia brzegowego Azure Stack, użyj zaktualizowanej wersji interfejsu API, zestawu SDK i Azure PowerShell zaplanowanych dla wydania styczeń 2021. 

W tym artykule opisano szczegółowo zmiany, które należy wprowadzić w przypadku korzystania z tej najnowszej wersji.

Aktualizacja z stycznia 2021 będzie dostępna tylko dla Azure Stack EDGE Pro-GPU, Azure Stack EDGE Pro R i Azure Stack Edge mini R urządzenia. Informacje zawarte w tym artykule dotyczą tylko tych urządzeń.

> [!NOTE]
> Nie jest wymagane uaktualnienie do wersji 2021 stycznia. Jeśli zdecydujesz się nadal korzystać z bieżącej wersji, nie ma to wpływu na Zarządzanie rolą IoT Edge. Jednak aby skorzystać z nowych funkcji i pomóc w zmniejszeniu zagrożenia bezpieczeństwa, zalecamy zainstalowanie nowszej wersji. 

## <a name="iot-edge-role-management-changes"></a>IoT Edge zmiany zarządzania rolami

Po zainstalowaniu opcjonalnej aktualizacji 2021 Azure Stack na urządzeniu brzegowym, należy użyć najnowszej wersji interfejsu API, zestawu SDK i poleceń cmdlet programu PowerShell do zarządzania rolami IoT Edge.

Następujące zmiany są wymagane tylko w przypadku zastosowania aktualizacji z stycznia 2021:

- Jeśli obecnie używasz interfejsu API zarządzania rolami &nbsp; w wersji 2019-08-01, przeprowadź uaktualnienie do wersji interfejsu API, która zostanie wydana w styczniu 2021. 
- Jeśli obecnie korzystasz z zarządzania rolami za pomocą zestawu SDK &nbsp; 1.0.0, Uaktualnij do wersji, która zostanie wydana w styczniu 2021.
- Jeśli używasz zarządzania rolami za pomocą poleceń cmdlet Azure PowerShell (wersja zapoznawcza), takich jak `Get-AzStackEdgeRole` ,, `New-AzStackEdgeRole` `Set-AzStackEdgeRole` , lub `Remove-AzStackEdgeRole` , poczekaj, aż nowe polecenia cmdlet zostaną wydane w lutym 2021.

## <a name="api-usage"></a>Użycie interfejsu API

Jeśli obecnie wykonujesz IoT Edge Zarządzanie rolami za pośrednictwem interfejsu API, Użyj nowego interfejsu API w wersji 2020-12-01, który zostanie opublikowany później. Jeśli korzystasz z interfejsu API bieżącej roli, po zainstalowaniu kolejnej wersji oprogramowania urządzenia należy przejść do roli PUT, GET lub DELETE Kubernetes, a następnie interfejsu API PUT dodatku IoT.

### <a name="for-the-put-method"></a>Dla metody PUT

#### <a name="the-current-http-request"></a>Bieżące żądanie HTTP 

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

#### <a name="the-upcoming-http-request"></a>Nadchodzące żądanie HTTP 

- Wywołania interfejsu API dla roli Kubernetes są nawiązywane przy użyciu następującego identyfikatora URI: 

  'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1?api-version=2020-12-01'

    Treść żądania będzie wyglądać następująco:

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

- Wywołania interfejsu API dla dodatku IoT Edge są wykonywane z następującym identyfikatorem URI: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

    Treść żądania będzie wyglądać następująco:

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


### <a name="for-the-get-method"></a>Dla metody GET

#### <a name="the-current-http-response"></a>Bieżąca odpowiedź HTTP

- Wywołania interfejsu API są nawiązywane przy użyciu następującego identyfikatora URI:

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

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

#### <a name="the-upcoming-http-response"></a>Nadchodząca odpowiedź HTTP

- Wywołania interfejsu API są nawiązywane przy użyciu następującego identyfikatora URI: 

   'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

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

### <a name="for-the-delete-method"></a>Dla metody DELETE

### <a name="the-current-api-calls"></a>Bieżące wywołania interfejsu API

Wywołania interfejsu API są nawiązywane przy użyciu następującego identyfikatora URI: 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/IoTRole1?api-version=2019-08-01'

### <a name="the-upcoming-api-calls"></a>Nadchodzące wywołania interfejsu API

Wywołania interfejsu API są nawiązywane przy użyciu następującego identyfikatora URI: 

'https://management.azure.com/subscriptions/4385cf00-2d3a-425a-832f-f4285b1c9dce/resourceGroups/GroupForEdgeAutomation/providers/Microsoft.DataBoxEdge/dataBoxEdgeDevices/testedgedevice/roles/KubernetesRole1/addons/iotaddon?api-version=2020-12-01'

## <a name="sdk-usage"></a>SDK usage (Użycie zestawu SDK)

Jeśli korzystasz z zestawu SDK, po zainstalowaniu aktualizacji z stycznia 2021 należy zmienić sposób konfigurowania roli IoT Edge, jak pokazano w poniższym przykładzie. Następnie pobierz i zainstaluj nadchodzący pakiet NuGet, aby przejść do nowego zestawu SDK, jak pokazano tutaj.

### <a name="the-current-sdk-sample"></a>Bieżący przykład zestawu SDK

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


### <a name="the-new-sdk-sample"></a>Nowy przykład zestawu SDK

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

Jeśli obecnie używasz `Get-AzStackEdgeRole` `New-AzStackEdgeRole` `Set-AzStackEdgeRole` polecenia cmdlet,, lub, musisz `Remove-AzStackEdgeRole` poczekać na nową wersję zaplanowaną dla wydania z lutego 2021.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Używam Azure Stack EDGE Pro-FPGA. Czy aktualizacja z stycznia 2021 ma wpływ na model FPGA?**

Nie. Aktualizacja z stycznia 2021 dotyczy tylko Azure Stack brzegów FPGA, Azure Stack EDGE Pro R i Azure Stack Edge mini R urządzeń. Ta aktualizacja nie ma wpływu na Azure Stack EDGE Pro-FPGA i nie wymaga żadnych zmian w IoT Edge zarządzania rolami.

**Czy po uaktualnieniu usługi Azure Stack EDGE Pro-GPU do nowego oprogramowania urządzenia w 2021 styczniu**

Nie. Po zainstalowaniu aktualizacji urządzenia z 2021 stycznia nie wpłynie to na skonfigurowane usługi.

**Jakie są zmiany wysokiego poziomu dotyczące interfejsu API zarządzania IoT Edge, zestawu SDK lub polecenia cmdlet?**

IoT Edge jest dodatkiem w ramach roli Kubernetes, co oznacza, że najpierw należy upewnić się, że Kubernetes jest skonfigurowany, a następnie wykonać IoT Edge konfigurację.


## <a name="next-steps"></a>Następne kroki

- [Dowiedz się, jak stosować aktualizacje](azure-stack-edge-gpu-install-update.md)

