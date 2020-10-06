---
title: Nawiązywanie połączenia z urządzeniem GPU i zarządzanie nim za pomocą interfejsu programu Windows PowerShell (Microsoft Azure Stack Edge) Microsoft Docs
description: Zawiera opis sposobu nawiązywania połączenia z usługą Azure Stack Edge z procesorem GPU i zarządzania nią za pośrednictwem interfejsu programu Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 10/06/2020
ms.author: alkohli
ms.openlocfilehash: 59b2307bb2e1f4effce9f68530be86e9ca625c9c
ms.sourcegitcommit: d9ba60f15aa6eafc3c5ae8d592bacaf21d97a871
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/06/2020
ms.locfileid: "91766289"
---
# <a name="manage-an-azure-stack-edge-pro-gpu-device-via-windows-powershell"></a>Zarządzanie urządzeniem wieloprocesorowym Pro Azure Stack Edge za pośrednictwem programu Windows PowerShell

Rozwiązanie Azure Stack EDGE Pro pozwala przetwarzać dane i wysyłać je za pośrednictwem sieci do platformy Azure. W tym artykule opisano niektóre zadania związane z konfiguracją i zarządzaniem dla urządzenia z Azure Stack Edge. Aby zarządzać urządzeniem, można użyć Azure Portal, lokalnego interfejsu użytkownika sieci Web lub interfejsu programu Windows PowerShell.

Ten artykuł koncentruje się na sposobach łączenia się z interfejsem programu PowerShell urządzenia i zadaniami, które można wykonać za pomocą tego interfejsu. 


## <a name="connect-to-the-powershell-interface"></a>Nawiązywanie połączenia z interfejsem programu PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Tworzenie pakietu dla pomocy technicznej

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

<!--## Upload certificate

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

You can also upload IoT Edge certificates to enable a secure connection between your IoT Edge device and the downstream devices that may connect to it. There are three IoT Edge certificates (*.pem* format) that you need to install:

- Root CA certificate or the owner CA
- Device CA certificate
- Device key certificate

The following example shows the usage of this cmdlet to install IoT Edge certificates:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
When you run this cmdlet, you will be prompted to provide the password for the network share.

For more information on certificates, go to [Azure IoT Edge certificates](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) or [Install certificates on a gateway](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).-->

## <a name="view-device-information"></a>Wyświetl informacje o urządzeniu
 
[!INCLUDE [View device information](../../includes/data-box-edge-gateway-view-device-info.md)]

## <a name="view-gpu-driver-information"></a>Wyświetlanie informacji o sterowniku procesora GPU

Jeśli na urządzeniu skonfigurowano rolę obliczeniową, można także uzyskać informacje o sterowniku procesora GPU za pośrednictwem interfejsu programu PowerShell. 

1. [Nawiąż połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj, `Get-HcsGpuNvidiaSmi` Aby uzyskać informacje o sterowniku procesora GPU dla Twojego urządzenia.

    W poniższym przykładzie pokazano użycie tego polecenia cmdlet:

    ```powershell
    Get-HcsGpuNvidiaSmi
    ```
    Zanotuj informacje o sterowniku z przykładowych danych wyjściowych tego polecenia cmdlet.

    ```powershell    
    +-----------------------------------------------------------------------------+    
    | NVIDIA-SMI 440.64.00    Driver Version: 440.64.00    CUDA Version: 10.2     |    
    |-------------------------------+----------------------+----------------------+    
    | GPU  Name        Persistence-M| Bus-Id        Disp.A | Volatile Uncorr. ECC |    
    | Fan  Temp  Perf  Pwr:Usage/Cap|         Memory-Usage | GPU-Util  Compute M. |    
    |===============================+======================+======================|    
    |   0  Tesla T4            On   | 000029CE:00:00.0 Off |                    0 |    
    | N/A   60C    P0    29W /  70W |   1539MiB / 15109MiB |      0%      Default |    
    +-------------------------------+----------------------+----------------------+    
    |   1  Tesla T4           On  | 0000AD50:00:00.0 Off |                    0 |
    | N/A   58C    P0    29W /  70W |    330MiB / 15109MiB |      0%      Default |
    +-------------------------------+----------------------+----------------------+
    ```

## <a name="enable-multi-process-service-mps"></a>Włącz usługę wieloprocesową (MPS)

Usługa wieloprocesowa (MP) w procesorach GPU firmy NVIDIA udostępnia mechanizm, w którym procesory GPU mogą być współużytkowane przez wiele zadań, gdzie każde zadanie jest przydzielone pewną część zasobów procesora GPU. Pakiet MP to funkcja w wersji zapoznawczej na urządzeniu z procesorem GPU w Azure Stack Edge. Aby włączyć pakiety MP na urządzeniu, wykonaj następujące kroki:

1. Przed rozpoczęciem upewnij się, że: to 

    1. [Twoje urządzenie Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-activate.md) zostało skonfigurowane i aktywowane przy użyciu zasobu Azure Stack Edge pro/Data Box Gateway na platformie Azure.
    1. [Na tym urządzeniu skonfigurowano obliczenia w Azure Portal](azure-stack-edge-deploy-configure-compute.md#configure-compute).
    
1. [Nawiąż połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
1. Użyj poniższego polecenia, aby włączyć pakiety MP na urządzeniu.

    ```powershell
    Start-HcsGpuMPS
    ```

## <a name="reset-your-device"></a>Resetowanie urządzenia

[!INCLUDE [Reset your device](../../includes/data-box-edge-gateway-deactivate-device.md)]

## <a name="get-compute-logs"></a>Pobieranie dzienników obliczeniowych

Jeśli na urządzeniu skonfigurowano rolę obliczeniową, można także uzyskać dzienniki obliczeń za pomocą interfejsu programu PowerShell.

1. [Nawiąż połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).
2. Użyj, `Get-AzureDataBoxEdgeComputeRoleLogs` Aby pobrać dzienniki obliczeniowe dla Twojego urządzenia.

    W poniższym przykładzie pokazano użycie tego polecenia cmdlet:

    ```powershell
    Get-AzureDataBoxEdgeComputeRoleLogs -Path "\\hcsfs\logs\myacct" -Credential "username" -FullLogCollection    
    ```

    Poniżej znajduje się opis parametrów używanych dla polecenia cmdlet:
    - `Path`: Podaj ścieżkę sieciową do udziału, w którym chcesz utworzyć pakiet dziennika obliczeń.
    - `Credential`: Podaj nazwę użytkownika dla udziału sieciowego. Po uruchomieniu tego polecenia cmdlet konieczne będzie podanie hasła udziału.
    - `FullLogCollection`: Ten parametr zapewnia, że pakiet dziennika będzie zawierać wszystkie dzienniki obliczeń. Domyślnie pakiet dziennika zawiera tylko podzestaw dzienników.


## <a name="change-kubernetes-pod-and-service-subnets"></a>Zmień Kubernetes pod i podsieci usługi

Domyślnie Kubernetes na urządzeniu brzegowym Azure Stack używa podsieci 172.27.0.0/16 i 172.28.0.0/16 dla usług pod i usługi. Jeśli te podsieci są już używane w sieci, możesz uruchomić `Set-HcsKubeClusterNetworkInfo` polecenie cmdlet, aby zmienić te podsieci.

Tę konfigurację należy wykonać przed rozpoczęciem konfigurowania obliczeń z Azure Portal, ponieważ w tym kroku jest tworzony klaster Kubernetes.

1. [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](#connect-to-the-powershell-interface).
1. W interfejsie programu PowerShell urządzenia Uruchom polecenie:

    `Set-HcsKubeClusterNetworkInfo -PodSubnet <subnet details> -ServiceSubnet <subnet details>`

    Zastąp element <subnet details> zakresem podsieci, który ma być używany. 

1. Po uruchomieniu tego polecenia można użyć `Get-HcsKubeClusterNetworkInfo` polecenia, aby sprawdzić, czy podsieci pod i usługi uległy zmianie.

Oto przykładowe dane wyjściowe dla tego polecenia.

```powershell
[10.100.10.10]: PS>Set-HcsKubeClusterNetworkInfo -PodSubnet 10.96.0.1/16 -ServiceSubnet 10.97.0.1/16
[10.100.10.10]: PS>Get-HcsKubeClusterNetworkInfo

Id                                   PodSubnet    ServiceSubnet
--                                   ---------    -------------
6dbf23c3-f146-4d57-bdfc-76cad714cfd1 10.96.0.1/16 10.97.0.1/16
[10.100.10.10]: PS>
```


## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Debugowanie problemów Kubernetes związanych z IoT Edge

<!--When the Kubernetes cluster is created, there are two system namespaces created: `iotedge` and `azure-arc`. --> 

<!--### Create config file for system namespace

To troubleshoot, first create the `config` file corresponding to the `iotedge` namespace with `aseuser`.

Run the `Get-HcsKubernetesUserConfig -AseUser` command and save the output as `config` file (no file extension). Save the file in the `.kube` folder of your user profile on the local machine.

Following is the sample output of the `Get-HcsKubernetesUserConfig` command.

```PowerShell
[10.100.10.10]: PS>Get-HcsKubernetesUserConfig -AseUser
apiVersion: v1
clusters:
- cluster:
    certificate-authority-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUN5RENDQWJDZ0F3SUJBZ0lCQURBTkJna3Foa2lHOXcwQkFRc0ZBREFWTVJNd0VRWURWUVFERXdwcmRXSmwKY201bGRHVnpNQjRYRFRJd01EVXhNekl4TkRRME5sb1hEVE13TURVeE1USXhORFEwTmxvd0ZURVRNQkVHQTFVRQpBeE1LYTNWaVpYSnVaWFJsY3pDQ0FTSXdEUVlKS29aSWh2Y05BUUVCQlFBRGdnRVBBRENDQVFvQ2dnRUJBS0M1CjlJbzRSU2hudG90QUdxdjNTYmRjOVd4UmJDYlRzWXU5S0RQeU9xanVoZE1UUE9PcmROOGNoa0x4NEFyZkZaU1AKZithUmhpdWZqSE56bWhucnkvZlprRGdqQzQzRmV5UHZzcTZXeVVDV0FEK2JBdi9wSkJDbkg2MldoWGNLZ1BVMApqU1k0ZkpXenNFbzBaREhoeUszSGN3MkxkbmdmaEpEanBQRFJBNkRWb2pIaktPb29OT1J1dURvUHpiOTg2dGhUCkZaQXJMZjRvZXRzTEk1ZzFYRTNzZzM1YVhyU0g3N2JPYVVsTGpYTzFYSnpFZlZWZ3BMWE5xR1ZqTXhBMVU2b1MKMXVJL0d1K1ArY
===========CUT=========================================CUT===================
    server: https://compute.myasegpu1.wdshcsso.com:6443
    name: kubernetes
contexts:
- context:
    cluster: kubernetes
    user: aseuser
    name: aseuser@kubernetes
current-context: aseuser@kubernetes
kind: Config
preferences: {}
users:
- name: aseuser
    user:
    client-certificate-data: LS0tLS1CRUdJTiBDRVJUSUZJQ0FURS0tLS0tCk1JSUMwRENDQWJpZ0F3SUJBZ0lJY1hOTXRPU2VwbG93RFFZSktvWklodmNOQVFFTEJRQXdGVEVUTUJFR0ExVUUKQXhNS2EzVmlaWEp1WlhSbGN6QWVGdzB5TURBMU1UTXlNVFEwTkRaYUZ3MHlNVEExTVRNeU1UVXhNVEphTUJJeApFREFPQmdOVkJBTVRCMkZ6WlhWelpYSXdnZ0VpTUEwR0NTcUdTSWIzRFFFQkFRVUFBNElCRHdBd2dnRUtBb0lCCkFRRHVjQ1pKdm9qNFIrc0U3a1EyYmVjNEJkTXdpUEhmU2R2WnNDVVY0aTRRZGY1Yzd0dkE3OVRSZkRLQTY1d08Kd0h0QWdlK3lLK0hIQ1Qyd09RbWtNek1RNjZwVFEzUlE0eVdtRDZHR1cWZWMExBR1hFUUxWWHRuTUdGCi0tLS0tRU5EIFJTQSBQUklWQVRFIEtFWS0tLS0tCg==

[10.100.10.10]: PS>
```
-->

Na urządzeniu Azure Stack EDGE Pro z skonfigurowaną rolą obliczeniową można rozwiązywać problemy lub monitorować urządzenie przy użyciu dwóch różnych zestawów poleceń.

- Korzystanie z `iotedge` poleceń. Te polecenia są dostępne dla podstawowych operacji na urządzeniu.
- Korzystanie z `kubectl` poleceń. Te polecenia są dostępne dla obszernego zestawu operacji dla urządzenia.

Aby wykonać jeden z powyższych zestawów poleceń, należy [nawiązać połączenie z interfejsem programu PowerShell](#connect-to-the-powershell-interface).

### <a name="use-iotedge-commands"></a>Użyj `iotedge` poleceń

Aby wyświetlić listę dostępnych poleceń, Połącz się [z interfejsem programu PowerShell](#connect-to-the-powershell-interface) i Użyj `iotedge` funkcji.

```powershell
[10.100.10.10]: PS>iotedge -?                                                                                                                           
Usage: iotedge COMMAND

Commands:
   list
   logs
   restart

[10.100.10.10]: PS>
```

W poniższej tabeli znajduje się krótki opis poleceń dostępnych dla `iotedge` :

|command  |Opis |
|---------|---------|
|`list`     | Wyświetlanie listy modułów         |
|`logs`     | Pobieranie dzienników modułu        |
|`restart`     | Zatrzymywanie i ponowne uruchamianie modułu         |


Aby wyświetlić listę wszystkich modułów uruchomionych na urządzeniu, użyj `iotedge list` polecenia.

Oto przykładowe dane wyjściowe tego polecenia. To polecenie wyświetla listę wszystkich modułów, skojarzonej konfiguracji i zewnętrznych adresów IP skojarzonych z modułami. Na przykład możesz uzyskać dostęp do aplikacji **WebServer** w `https://10.128.44.244` . 


```powershell
[10.100.10.10]: PS>iotedge list

NAME                   STATUS  DESCRIPTION CONFIG                                             EXTERNAL-IP
----                   ------  ----------- ------                                             -----
gettingstartedwithgpus Running Up 10 days  mcr.microsoft.com/intelligentedge/solutions:latest
iotedged               Running Up 10 days  azureiotedge/azureiotedge-iotedged:0.1.0-beta10    <none>
edgehub                Running Up 10 days  mcr.microsoft.com/azureiotedge-hub:1.0             10.128.44.243
edgeagent              Running Up 10 days  azureiotedge/azureiotedge-agent:0.1.0-beta10
webserverapp           Running Up 10 days  nginx:stable                                       10.128.44.244

[10.100.10.10]: PS>
```


### <a name="use-kubectl-commands"></a>Użyj poleceń polecenia kubectl

Na urządzeniu Azure Stack EDGE Pro, na którym skonfigurowano rolę obliczeniową, wszystkie `kubectl` polecenia są dostępne do monitorowania lub rozwiązywania problemów z modułami. Aby wyświetlić listę dostępnych poleceń, uruchom `kubectl --help` polecenie z okna poleceń.

```PowerShell
C:\Users\myuser>kubectl --help

kubectl controls the Kubernetes cluster manager.

Find more information at: https://kubernetes.io/docs/reference/kubectl/overview/

Basic Commands (Beginner):
    create         Create a resource from a file or from stdin.
    expose         Take a replication controller, service, deployment or pod and expose it as a new Kubernetes Service
    run            Run a particular image on the cluster
    set            Set specific features on objects
    run-container  Run a particular image on the cluster. This command is deprecated, use "run" instead
==============CUT=============CUT============CUT========================

Usage:
    kubectl [flags] [options]

Use "kubectl <command> --help" for more information about a given command.
Use "kubectl options" for a list of global command-line options (applies to all commands).

C:\Users\myuser>
```

Aby uzyskać pełną listę `kubectl` poleceń, przejdź do [ `kubectl` Ściągawka](https://kubernetes.io/docs/reference/kubectl/cheatsheet/).


#### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Aby uzyskać dostęp do adresu IP usługi lub modułu dostępnego poza klastrem Kubernetes

Aby uzyskać adres IP usługi równoważenia obciążenia lub modułów uwidocznionych poza Kubernetes, uruchom następujące polecenie:

`kubectl get svc -n iotedge`

Poniżej znajduje się przykładowe dane wyjściowe wszystkich usług lub modułów, które są widoczne poza klastrem Kubernetes. 


```powershell
[10.100.10.10]: PS>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

[10.100.10.10]: PS>
```
Adres IP w kolumnie zewnętrzny adres IP odpowiada zewnętrznemu punktowi końcowemu usługi lub modułu. [Zewnętrzny adres IP można również uzyskać na pulpicie nawigacyjnym Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


#### <a name="to-check-if-module-deployed-successfully"></a>Aby sprawdzić, czy moduł został wdrożony pomyślnie

Moduły obliczeniowe są kontenerami, w których zaimplementowano logikę biznesową. Kubernetes pod może mieć uruchomionych wiele kontenerów. 

Aby sprawdzić, czy moduł obliczeniowy został pomyślnie wdrożony, Połącz się z interfejsem programu PowerShell urządzenia.
Uruchom `get pods` polecenie i sprawdź, czy kontener (odpowiadający modułowi COMPUTE) jest uruchomiony.

Aby uzyskać listę wszystkich elementów w określonym obszarze nazw, uruchom następujące polecenie:

`get pods -n <namespace>`

Aby sprawdzić moduły wdrożone za pośrednictwem IoT Edge, uruchom następujące polecenie:

`get pods -n iotedge`

Poniżej znajduje się przykładowe dane wyjściowe wszystkich zasobników uruchomionych w `iotedge` przestrzeni nazw.

```
[10.100.10.10]: PS>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

[10.100.10.10]: PS>
```

**Stan stanu wskazuje,** że wszystkie zasobniki w przestrzeni nazw działają i **gotowe** wskazuje liczbę kontenerów wdrożonych w obszarze. W powyższym przykładzie wszystkie moduły są uruchomione i wszystkie wdrożone w każdym z tych zasobników są uruchomione. 

Aby sprawdzić moduły wdrożone za pośrednictwem usługi Azure ARC, uruchom następujące polecenie:

`get pods -n azure-arc`

Alternatywnie można [nawiązać połączenie z pulpitem nawigacyjnym usługi Kubernetes, aby zobaczyć IoT Edge lub wdrożenia w usłudze Azure Arc](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#view-module-status).


Aby uzyskać pełne dane wyjściowe określonego elementu pod dla danego obszaru nazw, można uruchomić następujące polecenie:

`kubectl describe pod <pod name> -n <namespace>` 

Przykładowe dane wyjściowe są wyświetlane w tym miejscu.

```
[10.100.10.10]: PS>kubectl describe pod filemove-66c49984b7 -n iotedge
Name:           filemove-66c49984b7-h8lxc
Namespace:      iotedge
Priority:       0
Node:           k8s-1hwf613cl-1hwf613/10.139.218.12
Start Time:     Thu, 14 May 2020 12:46:28 -0700
Labels:         net.azure-devices.edge.deviceid=myasegpu-edge
                net.azure-devices.edge.hub=myasegpu2iothub.azure-devices.net
                net.azure-devices.edge.module=filemove
                pod-template-hash=66c49984b7
Annotations:    net.azure-devices.edge.original-moduleid: filemove
Status:         Running
IP:             172.17.75.81
IPs:            <none>
Controlled By:  ReplicaSet/filemove-66c49984b7
Containers:
    proxy:
    Container ID:   docker://fd7975ca78209a633a1f314631042a0892a833b7e942db2e7708b41f03e8daaf
    Image:          azureiotedge/azureiotedge-proxy:0.1.0-beta8
    Image ID:       docker://sha256:5efbf6238f13d24bab9a2b499e5e05bc0c33ab1587d6cf6f289cdbe7aa667563
    Port:           <none>
    Host Port:      <none>
    State:          Running
        Started:      Thu, 14 May 2020 12:46:30 -0700
    Ready:          True
    Restart Count:  0
    Environment:
        PROXY_LOG:  Debug
=============CUT===============================CUT===========================
Volumes:
    config-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-config
    Optional:  false
    trust-bundle-volume:
    Type:      ConfigMap (a volume populated by a ConfigMap)
    Name:      iotedged-proxy-trust-bundle
    Optional:  false
    myasesmb1local:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1local
    ReadOnly:   false
    myasesmb1:
    Type:       PersistentVolumeClaim (a reference to a PersistentVolumeClaim in the same namespace)
    ClaimName:  myasesmb1
    ReadOnly:   false
    filemove-token-pzvw8:
    Type:        Secret (a volume populated by a Secret)
    SecretName:  filemove-token-pzvw8
    Optional:    false
QoS Class:       BestEffort
Node-Selectors:  <none>
Tolerations:     node.kubernetes.io/not-ready:NoExecute for 300s
                    node.kubernetes.io/unreachable:NoExecute for 300s
Events:          <none>


[10.100.10.10]: PS>
```

#### <a name="to-get-container-logs"></a>Aby uzyskać dzienniki kontenerów

Aby pobrać dzienniki dla modułu, uruchom następujące polecenie w interfejsie programu PowerShell urządzenia:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Ponieważ `all-containers` Flaga spowoduje zrzut wszystkich dzienników dla wszystkich kontenerów, dobrym sposobem na wyświetlenie ostatnich błędów jest użycie opcji `--tail 10` .

Poniżej znajduje się przykładowe dane wyjściowe. 

```
[10.100.10.10]: PS>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
DEBUG 2020-05-14T20:40:42Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:40:44Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:12Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 1 events, 0.000s
DEBUG 2020-05-14T20:42:14Z: loop process - 0 events, 0.000s
05/14/2020 19:46:44: Info: Opening module client connection.
05/14/2020 19:46:45: Info: Open done.
05/14/2020 19:46:45: Info: Initializing with input: /home/input, output: /home/output, protocol: Amqp.
05/14/2020 19:46:45: Info: IoT Hub module client initialized.

[10.100.10.10]: PS>
```

## <a name="connect-to-bmc"></a>Połącz z kontrolerem BMC

Kontroler zarządzania płytą główną (BMC) służy do zdalnego monitorowania urządzenia i zarządzania nim. W tej sekcji opisano polecenia cmdlet, których można użyć do zarządzania konfiguracją kontrolera BMC. Przed uruchomieniem dowolnego z tych poleceń cmdlet [Nawiąż połączenie z interfejsem programu PowerShell urządzenia](#connect-to-the-powershell-interface).

- `Get-HcsNetBmcInterface`: Użyj tego polecenia cmdlet, aby uzyskać właściwości konfiguracji sieci kontrolera BMC, na przykład,,, `IPv4Address` `IPv4Gateway` `IPv4SubnetMask` `DhcpEnabled` : 
    
    Oto przykładowe dane wyjściowe:
    
    ```powershell
    [10.100.10.10]: PS>Get-HcsNetBmcInterface
    IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
    -----------   ----------- -------------- -----------
    10.128.53.186 10.128.52.1 255.255.252.0        False
    [10.100.10.10]: PS>
    ```
- `Set-HcsNetBmcInterface`: To polecenie cmdlet można użyć na dwa sposoby.

    - Użyj polecenia cmdlet, aby włączyć lub wyłączyć konfigurację DHCP dla kontrolera BMC przy użyciu odpowiedniej wartości `UseDhcp` parametru. 

        ```powershell
        Set-HcsNetBmcInterface -UseDhcp $true
        ```

        Oto przykładowe dane wyjściowe: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -UseDhcp $true
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address IPv4Gateway IPv4SubnetMask DhcpEnabled
        ----------- ----------- -------------- -----------
        10.128.54.8 10.128.52.1 255.255.252.0         True
        [10.100.10.10]: PS>
        ```

    - Użyj tego polecenia cmdlet, aby skonfigurować konfigurację statyczną dla kontrolera BMC. Możesz określić wartości dla `IPv4Address` , `IPv4Gateway` i `IPv4SubnetMask` . 
    
        ```powershell
        Set-HcsNetBmcInterface -IPv4Address "<IPv4 address of the device>" -IPv4Gateway "<IPv4 address of the gateway>" -IPv4SubnetMask "<IPv4 address for the subnet mask>"
        ```        
        
        Oto przykładowe dane wyjściowe: 

        ```powershell
        [10.100.10.10]: PS>Set-HcsNetBmcInterface -IPv4Address 10.128.53.186 -IPv4Gateway 10.128.52.1 -IPv4SubnetMask 255.255.252.0
        [10.100.10.10]: PS>Get-HcsNetBmcInterface
        IPv4Address   IPv4Gateway IPv4SubnetMask DhcpEnabled
        -----------   ----------- -------------- -----------
        10.128.53.186 10.128.52.1 255.255.252.0        False
        [10.100.10.10]: PS>
        ```    

- `Set-HcsBmcPassword`: To polecenie cmdlet służy do modyfikowania hasła BMC dla programu `EdgeUser` . 

    Oto przykładowe dane wyjściowe: 

    ```powershell
    [10.100.10.10]: PS> Set-HcsBmcPassword -NewPassword "Password1"
    [10.100.10.10]: PS>
    ```

## <a name="exit-the-remote-session"></a>Zakończ sesję zdalną

Aby wyjść z zdalnej sesji programu PowerShell, Zamknij okno programu PowerShell.

## <a name="next-steps"></a>Następne kroki

- Wdróż [Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-prep.md) w Azure Portal.
