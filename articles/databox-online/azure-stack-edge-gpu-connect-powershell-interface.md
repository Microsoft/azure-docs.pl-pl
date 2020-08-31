---
title: Nawiązywanie połączenia z urządzeniem Microsoft Azure Stack Edge i zarządzanie nim za pomocą interfejsu programu Windows PowerShell | Microsoft Docs
description: Opisuje sposób nawiązywania połączenia z Azure Stack Edge i zarządzania nim za pomocą interfejsu programu Windows PowerShell.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 26d4476217d5a6f6abea6093f453cd52d654911e
ms.sourcegitcommit: 656c0c38cf550327a9ee10cc936029378bc7b5a2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/28/2020
ms.locfileid: "89086156"
---
# <a name="manage-an-azure-stack-edge-gpu-device-via-windows-powershell"></a>Zarządzanie urządzeniem GPU Azure Stack Edge przy użyciu programu Windows PowerShell

Rozwiązanie Azure Stack Edge pozwala na przetwarzanie danych i wysyłanie ich przez sieć do platformy Azure. W tym artykule opisano niektóre zadania związane z konfiguracją i zarządzaniem dla Azure Stack urządzenia brzegowego. Aby zarządzać urządzeniem, można użyć Azure Portal, lokalnego interfejsu użytkownika sieci Web lub interfejsu programu Windows PowerShell.

Ten artykuł koncentruje się na sposobach łączenia się z interfejsem programu PowerShell urządzenia i zadaniami, które można wykonać za pomocą tego interfejsu. 


## <a name="connect-to-the-powershell-interface"></a>Nawiązywanie połączenia z interfejsem programu PowerShell

[!INCLUDE [Connect to admin runspace](../../includes/data-box-edge-gateway-connect-minishell.md)]

## <a name="create-a-support-package"></a>Tworzenie pakietu dla pomocy technicznej

[!INCLUDE [Create a support package](../../includes/data-box-edge-gateway-create-support-package.md)]

## <a name="upload-certificate"></a>Przekazywanie certyfikatu

[!INCLUDE [Upload certificate](../../includes/data-box-edge-gateway-upload-certificate.md)]

Można również przekazać certyfikaty IoT Edge, aby umożliwić bezpieczne połączenie między urządzeniem IoT Edge i urządzeniami podrzędnymi, które mogą się z nim połączyć. Istnieją trzy IoT Edge certyfikaty (format*PEM* ), które należy zainstalować:

- Certyfikat głównego urzędu certyfikacji lub urząd certyfikacji właściciela
- Certyfikat urzędu certyfikacji urządzenia
- Certyfikat klucza urządzenia

W poniższym przykładzie pokazano użycie tego polecenia cmdlet w celu zainstalowania IoT Edge certyfikatów:

```
Set-HcsCertificate -Scope IotEdge -RootCACertificateFilePath "\\hcfs\root-ca-cert.pem" -DeviceCertificateFilePath "\\hcfs\device-ca-cert.pem\" -DeviceKeyFilePath "\\hcfs\device-key-cert.pem" -Credential "username"
```
Po uruchomieniu tego polecenia cmdlet zostanie wyświetlony monit o podanie hasła dla udziału sieciowego.

Aby uzyskać więcej informacji na temat certyfikatów, przejdź do pozycji [Azure IoT Edge Certificates](https://docs.microsoft.com/azure/iot-edge/iot-edge-certs) lub [Zainstaluj certyfikaty na bramie](https://docs.microsoft.com/azure/iot-edge/how-to-create-transparent-gateway).

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

Usługa wieloprocesowa (MP) w procesorach GPU firmy NVIDIA udostępnia mechanizm, w którym procesory GPU mogą być współużytkowane przez wiele zadań, gdzie każde zadanie jest przydzielone pewną część zasobów procesora GPU. Aby włączyć MPS na urządzeniu Azure Stack Edge, wykonaj następujące kroki:

1. Przed rozpoczęciem upewnij się, że: to 

    1. [Urządzenie Azure Stack Edge](azure-stack-edge-gpu-deploy-activate.md) zostało skonfigurowane i aktywowane przy użyciu zasobu Azure Stack edge/Data Box Gateway na platformie Azure.
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



## <a name="debug-kubernetes-issues-related-to-iot-edge"></a>Debugowanie problemów Kubernetes związanych z IoT Edge

Po utworzeniu klastra Kubernetes `aseuser` tworzony jest również domyślny użytkownik skojarzony z przestrzenią nazw System `iotedge` . Aby debugować wszelkie problemy związane z IoT Edge, można użyć tego użytkownika i systemowej przestrzeni nazw.  

### <a name="create-config-file-for-system-namespace"></a>Utwórz plik konfiguracji dla przestrzeni nazw systemu

Aby rozwiązać problem, najpierw Utwórz `config` plik odpowiadający `iotedge` przestrzeni nazw za pomocą `aseuser` .

Uruchom `Get-HcsKubernetesUserConfig -AseUser` polecenie i Zapisz dane wyjściowe jako `config` plik (bez rozszerzenia pliku). Zapisz plik w `.kube` folderze profilu użytkownika na komputerze lokalnym.

Poniżej znajduje się przykładowe dane wyjściowe `Get-HcsKubernetesUserConfig` polecenia.

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

Na urządzeniu brzegowym Azure Stack, na którym skonfigurowano rolę obliczeniową, wszystkie `kubectl` polecenia są dostępne do monitorowania lub rozwiązywania problemów z modułami. Aby wyświetlić listę dostępnych poleceń, uruchom `kubectl --help` polecenie z okna poleceń.

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

### <a name="to-get-ip-of-service-or-module-exposed-outside-of-kubernetes-cluster"></a>Aby uzyskać dostęp do adresu IP usługi lub modułu dostępnego poza klastrem Kubernetes

Aby uzyskać adres IP usługi równoważenia obciążenia lub modułów uwidocznionych poza Kubernetes, uruchom następujące polecenie:

`kubectl get svc -n iotedge`

Poniżej znajduje się przykładowe dane wyjściowe wszystkich usług lub modułów, które są widoczne poza klastrem Kubernetes. 


```powershell
C:\Users\user>kubectl get svc -n iotedge
NAME           TYPE           CLUSTER-IP      EXTERNAL-IP     PORT(S)                                       AGE
edgehub        LoadBalancer   10.103.52.225   10.128.44.243   443:31987/TCP,5671:32336/TCP,8883:30618/TCP   34h
iotedged       ClusterIP      10.107.236.20   <none>          35000/TCP,35001/TCP                           3d8h
webserverapp   LoadBalancer   10.105.186.35   10.128.44.244   8080:30976/TCP                                16h

C:\Users\user>
```
Adres IP w kolumnie zewnętrzny adres IP odpowiada zewnętrznemu punktowi końcowemu usługi lub modułu. [Zewnętrzny adres IP można również uzyskać na pulpicie nawigacyjnym Kubernetes](azure-stack-edge-gpu-monitor-kubernetes-dashboard.md#get-ip-address-for-services-or-modules).


### <a name="to-check-if-module-deployed-successfully"></a>Aby sprawdzić, czy moduł został wdrożony pomyślnie

Moduły obliczeniowe są kontenerami, w których zaimplementowano logikę biznesową. Kubernetes pod może mieć uruchomionych wiele kontenerów. Aby sprawdzić, czy moduł obliczeniowy został pomyślnie wdrożony, uruchom `get pods` polecenie i sprawdź, czy kontener (odpowiadający modułowi obliczeniowemu) jest uruchomiony.

Aby uzyskać listę wszystkich elementów w określonym obszarze nazw, uruchom następujące polecenie:

`get pods -n <namespace>`

Poniżej znajduje się przykładowe dane wyjściowe wszystkich zasobników uruchomionych w `iotedge` przestrzeni nazw.

```
C:\Users\myuser>kubectl get pods -n iotedge
NAME                        READY   STATUS    RESTARTS   AGE
edgeagent-cf6d4ffd4-q5l2k   2/2     Running   0          20h
edgehub-8c9dc8788-2mvwv     2/2     Running   0          56m
filemove-66c49984b7-h8lxc   2/2     Running   0          56m
iotedged-675d7f4b5f-9nml4   1/1     Running   0          20h

C:\Users\myuser>
```

**Stan stanu wskazuje,** że wszystkie zasobniki w przestrzeni nazw działają i **gotowe** wskazuje liczbę kontenerów wdrożonych w obszarze. W powyższym przykładzie wszystkie moduły są uruchomione i wszystkie wdrożone w każdym z tych zasobników są uruchomione. 

Aby uzyskać pełne dane wyjściowe określonego elementu pod dla danego obszaru nazw, można uruchomić następujące polecenie:

`kubectl describe pod <pod name> -n <namespace>` 

Przykładowe dane wyjściowe są wyświetlane w tym miejscu.

```
C:\Users\myuser>kubectl describe pod filemove-66c49984b7 -n iotedge
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


C:\Users\myuser>
```

### <a name="to-get-container-logs"></a>Aby uzyskać dzienniki kontenerów

Aby pobrać dzienniki dla modułu, uruchom następujące polecenie:

`kubectl logs <pod_name> -n <namespace> --all-containers` 

Ponieważ `all-containers` Flaga spowoduje zrzut wszystkich dzienników dla wszystkich kontenerów, dobrym sposobem na wyświetlenie ostatnich błędów jest użycie opcji `--tail 10` .

Poniżej znajduje się przykładowe dane wyjściowe. 

```
C:\Users\myuser>kubectl logs filemove-66c49984b7-h8lxc -n iotedge --all-containers --tail 10
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

C:\Users\myuser>
```

## <a name="exit-the-remote-session"></a>Zakończ sesję zdalną

Aby wyjść z zdalnej sesji programu PowerShell, Zamknij okno programu PowerShell.

## <a name="next-steps"></a>Następne kroki

- Wdróż [Azure Stack Edge](azure-stack-edge-gpu-deploy-prep.md) w Azure Portal.
