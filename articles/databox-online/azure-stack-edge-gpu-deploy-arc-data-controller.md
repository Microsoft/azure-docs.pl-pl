---
title: Wdrażanie kontrolera danych usługi Azure Arc na urządzeniu z systemem Azure Stack Edge Microsoft Docs
description: Zawiera opis sposobu wdrażania kontrolera danych usługi Azure Arc i usługi Azure Data Services na urządzeniu z systemem Azure Stack EDGE Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 03/08/2021
ms.author: alkohli
ms.openlocfilehash: 53058d27e94c9fdf18d726369f6a1b75a9f34db9
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105567546"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Wdrażanie usługi Azure Data Services na urządzeniu z systemem Azure Stack EDGE Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano proces tworzenia kontrolera danych usługi Azure ARC, a następnie wdrażania Data Services platformy Azure na urządzeniu GPU Azure Stack Edge. 

Azure Arc Data Controller to lokalna płaszczyzna kontroli, która umożliwia korzystanie z platformy Azure Data Services w środowiskach zarządzanych przez klienta. Po utworzeniu kontrolera danych usługi Azure Arc w klastrze Kubernetes działającym na urządzeniu z systemem Azure Stack EDGE Pro można wdrożyć Data Services platformy Azure, takie jak wystąpienie zarządzane SQL (wersja zapoznawcza) na tym kontrolerze danych.

Procedura tworzenia kontrolera danych, a następnie wdrażania wystąpienia zarządzanego SQL polega na użyciu programu PowerShell i `kubectl` -Narzędzia natywnego, które zapewnia dostęp z wiersza polecenia do klastra Kubernetes na urządzeniu.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Masz dostęp do urządzenia z usługą Azure Stack Edge, a Twoje urządzenie zostało aktywowane zgodnie z opisem w artykule [aktywuj Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-activate.md).

1. Na urządzeniu została włączona rola obliczeniowa. Klaster Kubernetes został również utworzony na urządzeniu podczas konfigurowania obliczeń na urządzeniu zgodnie z instrukcjami w temacie [Konfigurowanie obliczeń na urządzeniu Azure Stack EDGE Pro](azure-stack-edge-gpu-deploy-configure-compute.md).

1. Masz punkt końcowy interfejsu API Kubernetes ze strony **urządzenie** w lokalnym interfejsie użytkownika sieci Web. Aby uzyskać więcej informacji, zapoznaj się z instrukcjami w artykule [pobieranie punktu końcowego interfejsu API Kubernetes](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints).

1. Masz dostęp do klienta, który będzie łączył się z urządzeniem. 
    1. W tym artykule jest używany system klienta systemu Windows z uruchomionym programem PowerShell 5,0 lub nowszym w celu uzyskania dostępu do urządzenia. Można użyć dowolnego innego klienta z [obsługiwanym systemem operacyjnym](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device). 
    1. Zainstaluj program `kubectl` na kliencie. W przypadku wersji klienta:
        1. Określ wersję serwera Kubernetes zainstalowanego na urządzeniu. W lokalnym interfejsie użytkownika urządzenia przejdź do strony **aktualizacje oprogramowania** . Zanotuj **wersję serwera Kubernetes** na tej stronie.
        1. Pobierz klienta, który odbiega od wersji głównej nie więcej niż o jedną wersję podrzędną. Wersja klienta, ale może prowadzić do programu głównego do jednej wersji pomocniczej. Na przykład wzorzec v 1.3 powinien współpracować z węzłami v 1.1, v 1.2 i v 1.3 i powinien współpracować z klientami w wersji 1.2, v 1.3 i v 1.4. Aby uzyskać więcej informacji na temat wersji klienta Kubernetes, zobacz [zasady obsługi systemu Kubernetes wersja i wersja](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew).
    
1. Opcjonalnie [Zainstaluj narzędzia klienckie do wdrażania usług danych z obsługą usługi Azure Arc i zarządzania nimi](../azure-arc/data/install-client-tools.md). Te narzędzia nie są wymagane, ale zalecane.  
1. Upewnij się, że masz wystarczającą ilość zasobów dostępnych na urządzeniu, aby zainicjować obsługę administracyjną kontrolera danych i jednego wystąpienia zarządzanego SQL. W przypadku kontrolera danych i jednego wystąpienia zarządzanego SQL będzie wymagane co najmniej 16 GB pamięci RAM i 4 rdzenie procesora. Aby uzyskać szczegółowe wskazówki, przejdź do [minimalnych wymagań dla wdrożenia usług danych z obsługą usługi Azure Arc](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements).


## <a name="configure-kubernetes-external-service-ips"></a>Skonfiguruj adresy IP usług zewnętrznych Kubernetes

1. Przejdź do lokalnego interfejsu użytkownika sieci Web urządzenia, a następnie przejdź do obszaru **obliczenia**.
1. Wybierz sieć włączoną do obliczenia. 

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Upewnij się, że podajesz trzy dodatkowe adresy IP usług zewnętrznych Kubernetes (oprócz adresów IP, które zostały już skonfigurowane dla innych usług lub kontenerów zewnętrznych). Kontroler danych będzie używać dwóch adresów IP usługi, a trzeci adres IP jest używany podczas tworzenia wystąpienia zarządzanego SQL. Dla każdej kolejnej usługi danych zostanie wdrożony jeden adres IP. 

    ![Strona obliczeniowa w lokalnym interfejsie użytkownika 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Zastosuj ustawienia, a nowe adresy IP zaczną obowiązywać od już istniejącego klastra Kubernetes. 


## <a name="deploy-azure-arc-data-controller"></a>Wdróż kontroler danych usługi Azure Arc

Przed wdrożeniem kontrolera danych należy utworzyć przestrzeń nazw.

### <a name="create-namespace"></a>Create namespace 

Utwórz nową, dedykowaną przestrzeń nazw, w której zostanie wdrożony kontroler danych. Utworzysz również użytkownika, a następnie przyznasz użytkownikowi dostęp do utworzonego obszaru nazw. 

> [!NOTE]
> Dla nazw i nazw użytkowników stosowane są [konwencje nazewnictwa domen DNS](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) .

1. [Nawiąż połączenie z interfejsem programu PowerShell](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface).
1. Tworzenie przestrzeni nazw. Wpisz:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Utwórz użytkownika. Wpisz: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Plik konfiguracji jest wyświetlany w postaci zwykłego tekstu. Skopiuj ten plik i Zapisz go jako plik *konfiguracji* . 

    > [!IMPORTANT]
    > Nie zapisuj pliku konfiguracji jako pliku *txt* , Zapisz plik bez rozszerzenia pliku.

1. Plik konfiguracji powinien znajdować się na żywo w `.kube` folderze profilu użytkownika na komputerze lokalnym. Skopiuj plik do tego folderu w profilu użytkownika.

    ![Lokalizacja pliku konfiguracji na kliencie](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Przyznaj użytkownikowi dostęp do utworzonego obszaru nazw. Wpisz: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Oto przykładowe dane wyjściowe powyższych poleceń. W tym przykładzie utworzysz `myadstest` przestrzeń nazw, `myadsuser` użytkownika i przyznano użytkownikowi dostęp do przestrzeni nazw.
    
    ```powershell
    [10.100.10.10]: PS>New-HcsKubernetesNamespace -Namespace myadstest
    [10.100.10.10]: PS>New-HcsKubernetesUser -UserName myadsuser
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: LS0tLS1CRUdJTiBD=======//snipped//=======VSVElGSUNBVEUtLS0tLQo=
        server: https://compute.myasegpudev.wdshcsso.com:6443
      name: kubernetes
    contexts:
    - context:
        cluster: kubernetes
        user: myadsuser
      name: myadsuser@kubernetes
    current-context: myadsuser@kubernetes
    kind: Config
    preferences: {}
    users:
    - name: myadsuser
      user:
        client-certificate-data: LS0tLS1CRUdJTiBDRV=========//snipped//=====EE9PQotLS0kFURSBLRVktLS0tLQo=
    
    [10.100.10.10]: PS>Grant-HcsKubernetesNamespaceAccess -Namespace myadstest -UserName myadsuser
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS>
    ```
1. Dodaj wpis DNS do pliku hosts w systemie. 

    1. Uruchom program Notepad jako administrator i Otwórz `hosts` plik znajdujący się w lokalizacji `C:\windows\system32\drivers\etc\hosts` . 
    2. Użyj informacji zapisanych na stronie **urządzenia** w lokalnym interfejsie użytkownika (wymaganie wstępne), aby utworzyć wpis w pliku Hosts. 

        Na przykład skopiuj ten punkt końcowy, `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` Aby utworzyć następujący wpis z adresem IP urządzenia i domeną DNS: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Aby sprawdzić, czy można nawiązać połączenie z Kubernetesmi, Uruchom wiersz polecenia lub sesję programu PowerShell. Wpisz:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Teraz można wdrożyć aplikacje i dane usług danych w przestrzeni nazw, a następnie wyświetlić aplikacje i ich dzienniki.

### <a name="create-data-controller"></a>Tworzenie kontrolera danych

Kontroler danych jest zbiorem zasobników wdrożonych w klastrze Kubernetes w celu udostępnienia interfejsu API, usługi kontrolera, programu inicjującego oraz baz danych i pulpitów nawigacyjnych monitorowania. Wykonaj następujące kroki, aby utworzyć kontroler danych w klastrze Kubernetes, który istnieje na urządzeniu z systemem Azure Stack Edge w utworzonym wcześniej obszarze nazw.   

1. Zbierz następujące informacje, które będą potrzebne do utworzenia kontrolera danych:

    
    |Column1  |Kolumna2  |
    |---------|---------|
    |Nazwa kontrolera danych     |Opisowa nazwa kontrolera danych. Na przykład `arctestdatacontroller`.         |
    |Nazwa użytkownika kontrolera danych     |Dowolna nazwa użytkownika administratora kontrolera danych. Nazwa użytkownika i hasło kontrolera danych są używane do uwierzytelniania w interfejsie API kontrolera danych w celu wykonywania funkcji administracyjnych.          |
    |Hasło kontrolera danych     |Hasło administratora kontrolera danych. Wybierz bezpieczne hasło i udostępnij je tylko osobom, które muszą mieć uprawnienia administratora klastra.         |
    |Nazwa przestrzeni nazw Kubernetes     |Nazwa przestrzeni nazw Kubernetes, w której ma zostać utworzony kontroler danych.         |
    |Identyfikator subskrypcji platformy Azure     |Identyfikator GUID subskrypcji platformy Azure, dla którego ma zostać utworzony zasób kontrolera danych na platformie Azure.         |
    |Nazwa grupy zasobów platformy Azure     |Nazwa grupy zasobów, w której ma zostać utworzony zasób kontrolera danych na platformie Azure.         |
    |Lokalizacja platformy Azure     |Lokalizacja platformy Azure, w której metadane zasobów kontrolera danych będą przechowywane na platformie Azure. Aby uzyskać listę dostępnych regionów, zobacz globalna infrastruktura/produkty platformy Azure według regionów.|


1. Nawiąż połączenie z interfejsem programu PowerShell. Aby utworzyć kontroler danych, wpisz: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Oto przykładowe dane wyjściowe powyższych poleceń.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Wdrożenie może potrwać około 5 minut.

    > [!NOTE]
    > Kontroler danych utworzony w klastrze Kubernetes na urządzeniu z systemem Azure Stack Edge działa tylko w trybie rozłączenia w bieżącej wersji.

### <a name="monitor-data-creation-status"></a>Monitorowanie stanu tworzenia danych

1. Otwórz inne okno programu PowerShell.
1. Użyj następującego `kubectl` polecenia, aby monitorować stan tworzenia kontrolera danych. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Po utworzeniu kontrolera należy mieć stan `Ready` .
    Oto przykładowe dane wyjściowe poprzedniego polecenia:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Aby zidentyfikować adresy IP przypisane do usług zewnętrznych uruchomionych na kontrolerze danych, użyj `kubectl get svc -n <namespace>` polecenia. Oto przykładowe dane wyjściowe:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get svc -n myadstest
    NAME                      TYPE           CLUSTER-IP       EXTERNAL-IP   PORT(S)                                       AGE
    controldb-svc             ClusterIP      172.28.157.130   <none>        1433/TCP,8311/TCP,8411/TCP                    3d21h
    controller-svc            ClusterIP      172.28.123.251   <none>        443/TCP,8311/TCP,8301/TCP,8411/TCP,8401/TCP   3d21h
    controller-svc-external   LoadBalancer   172.28.154.30    10.57.48.63   30080:31090/TCP                               3d21h
    logsdb-svc                ClusterIP      172.28.52.196    <none>        9200/TCP,8300/TCP,8400/TCP                    3d20h
    logsui-svc                ClusterIP      172.28.85.97     <none>        5601/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdb-svc             ClusterIP      172.28.255.103   <none>        8086/TCP,8300/TCP,8400/TCP                    3d20h
    metricsdc-svc             ClusterIP      172.28.208.191   <none>        8300/TCP,8400/TCP                             3d20h
    metricsui-svc             ClusterIP      172.28.158.163   <none>        3000/TCP,8300/TCP,8400/TCP                    3d20h
    mgmtproxy-svc             ClusterIP      172.28.228.229   <none>        443/TCP,8300/TCP,8311/TCP,8400/TCP,8411/TCP   3d20h
    mgmtproxy-svc-external    LoadBalancer   172.28.166.214   10.57.48.64   30777:30621/TCP                               3d20h
    sqlex-svc                 ClusterIP      None             <none>        1433/TCP                                      3d20h
    PS C:\WINDOWS\system32>
    ```

## <a name="deploy-sql-managed-instance"></a>Wdróż wystąpienie zarządzane SQL

Po pomyślnym utworzeniu kontrolera danych można użyć szablonu do wdrożenia wystąpienia zarządzanego SQL na kontrolerze danych.

### <a name="deployment-template"></a>Szablon wdrożenia

Poniższy szablon wdrożenia służy do wdrażania wystąpienia zarządzanego SQL w kontrolerze danych na urządzeniu.

```yml
apiVersion: v1
data:
    password: UGFzc3dvcmQx
    username: bXlhZHN1c2Vy
kind: Secret
metadata:
    name: sqlex-login-secret
type: Opaque
---
apiVersion: sql.arcdata.microsoft.com/v1alpha1
kind: sqlmanagedinstance
metadata:
    name: sqlex
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
    service:
    type: LoadBalancer
    storage:
    backups:
        className: ase-node-local
        size: 5Gi
    data:
        className: ase-node-local
        size: 5Gi
    datalogs:
        className: ase-node-local
        size: 5Gi
    logs:
        className: ase-node-local
        size: 1Gi
```


#### <a name="metadata-name"></a>Nazwa metadanych
    
Nazwa metadanych to nazwa wystąpienia zarządzanego SQL. Skojarzona pod poprzednim `deployment.yaml` znakiem będzie nazwana jako `sqlex-n` ( `n` jest liczbą numerów skojarzonych z aplikacją). 
    
#### <a name="password-and-username-data"></a>Hasło i dane nazwy użytkownika

Nazwa użytkownika i hasło kontrolera danych są używane do uwierzytelniania w interfejsie API kontrolera danych w celu wykonywania funkcji administracyjnych. Wpis tajny Kubernetes dla nazwy użytkownika i hasła kontrolera danych w szablonie wdrożenia są zakodowane w formacie base64. 

Możesz użyć narzędzia online do kodowania base64 żądanej nazwy użytkownika i hasła lub użyć wbudowanych narzędzi interfejsu wiersza polecenia w zależności od platformy. W przypadku korzystania z narzędzia kodowania w trybie online Base64 Podaj nazwę użytkownika i hasło (wprowadzone podczas tworzenia kontrolera danych) w narzędziu, a narzędzie wygeneruje odpowiednie zakodowane w formacie base64 ciągi.
    
#### <a name="service-type"></a>Typ usługi

Typ usługi powinien być ustawiony na `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Nazwa klasy magazynu

Można zidentyfikować klasę magazynu na urządzeniu z systemem Azure Stack Edge, które będzie używane przez wdrożenie na potrzeby danych, kopii zapasowych, dzienników danych i dzienników. Użyj  `kubectl get storageclass` polecenia, aby uzyskać klasę magazynu wdrożoną na urządzeniu.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
W powyższym przykładzie danych wyjściowych, Klasa magazynu `ase-node-local` na urządzeniu powinna być określona w szablonie.
 
#### <a name="spec"></a>Zapisywarka

Aby utworzyć wystąpienie zarządzane SQL na urządzeniu Azure Stack Edge, możesz określić wymagania dotyczące pamięci i procesora w sekcji specyfikacji `deployment.yaml` . Każde wystąpienie zarządzane SQL musi zażądać co najmniej 2 GB pamięci i 1 rdzenia procesora, jak pokazano w poniższym przykładzie. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Aby uzyskać szczegółowe wskazówki dotyczące ustalania wielkości dla kontrolera danych i 1 wystąpienia zarządzanego SQL, przejrzyj [szczegóły dotyczące ustalania wielkości wystąpienia zarządzanego SQL](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details).

### <a name="run-deployment-template"></a>Uruchom szablon wdrażania

Uruchom `deployment.yaml` polecenie przy użyciu następującego polecenia:

```powershell
kubectl create -n <Name of namespace that you created> -f <Path to the deployment yaml> 
```

Oto przykładowe dane wyjściowe następującego polecenia:

```powershell
PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
No resources found.
PS C:\WINDOWS\system32> 
PS C:\WINDOWS\system32> kubectl create -n myadstest -f C:\azure-arc-data-services\sqlex.yaml  secret/sqlex-login-secret created
sqlmanagedinstance.sql.arcdata.microsoft.com/sqlex created
PS C:\WINDOWS\system32> kubectl get pods --namespace myadstest
NAME                 READY   STATUS    RESTARTS   AGE
bootstrapper-mv2cd   1/1     Running   0          83m
control-w9s9l        2/2     Running   0          78m
controldb-0          2/2     Running   0          78m
controlwd-4bmc5      1/1     Running   0          64m
logsdb-0             1/1     Running   0          64m
logsui-wpmw2         1/1     Running   0          64m
metricsdb-0          1/1     Running   0          64m
metricsdc-fb5r5      1/1     Running   0          64m
metricsui-56qzs      1/1     Running   0          64m
mgmtproxy-2ckl7      2/2     Running   0          64m
sqlex-0              3/3     Running   0          13m
PS C:\WINDOWS\system32>
```

Wartość `sqlex-0` pod w przykładowym wyjściu wskazuje stan wystąpienia zarządzanego SQL.

## <a name="remove-data-controller"></a>Usuń kontroler danych

Aby usunąć kontroler danych, Usuń dedykowaną przestrzeń nazw, w której został wdrożony.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Następne kroki

- [Wdróż bezstanową aplikację na Azure Stack Edge](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).