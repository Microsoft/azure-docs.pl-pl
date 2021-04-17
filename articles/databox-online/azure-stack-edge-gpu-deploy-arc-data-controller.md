---
title: Wdrażanie kontrolera Azure Arc na urządzeniu Azure Stack Edge Pro GPU| Microsoft Docs
description: Opisuje sposób wdrażania kontrolera Azure Arc i usługi Azure Data Services na urządzeniu Azure Stack Edge Pro GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 04/15/2021
ms.author: alkohli
ms.openlocfilehash: d56e03cd650032a775c30b02d939cf934f384fae
ms.sourcegitcommit: 590f14d35e831a2dbb803fc12ebbd3ed2046abff
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107568611"
---
# <a name="deploy-azure-data-services-on-your-azure-stack-edge-pro-gpu-device"></a>Wdrażanie usługi Azure Data Services na urządzeniu Azure Stack Edge Pro GPU

[!INCLUDE [applies-to-GPU-and-pro-r-and-mini-r-skus](../../includes/azure-stack-edge-applies-to-gpu-pro-r-mini-r-sku.md)]

W tym artykule opisano proces tworzenia kontrolera danych Azure Arc, a następnie wdrażania usługi Azure Data Services na urządzeniu Azure Stack Edge Pro GPU. 

Azure Arc Data Controller to lokalna płaszczyzna sterowania, która umożliwia Data Services Azure w środowiskach zarządzanych przez klienta. Po utworzeniu kontrolera danych usługi Azure Arc w klastrze Kubernetes uruchomionym na urządzeniu z procesorem GPU z systemem Azure Stack Edge Pro możesz wdrożyć usługę Azure Data Services, taką jak SQL Managed Instance (wersja zapoznawcza) na tym kontrolerze danych.

Procedura tworzenia kontrolera danych, a następnie wdrażania usługi SQL Managed Instance obejmuje użycie programu PowerShell i — natywnego narzędzia, które zapewnia dostęp wiersza polecenia do klastra `kubectl` Kubernetes na urządzeniu.


## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Masz dostęp do urządzenia z Azure Stack Edge Pro GPU i aktywowano urządzenie zgodnie z opisem w te Azure Stack Edge Pro [.](azure-stack-edge-gpu-deploy-activate.md)

1. Na urządzeniu włączono rolę obliczeniową. Klaster Kubernetes został również utworzony na urządzeniu podczas konfigurowania obliczeń na urządzeniu zgodnie z instrukcjami w tece Configure compute on your Azure Stack Edge Pro GPU device (Konfigurowanie zasobów obliczeniowych na urządzeniu [Azure Stack Edge Pro GPU).](azure-stack-edge-gpu-deploy-configure-compute.md)

1. Punkt końcowy interfejsu API kubernetes na stronie **Urządzenie** lokalnego internetowego interfejsu użytkownika. Aby uzyskać więcej informacji, zobacz instrukcje w te [tematu Get Kubernetes API endpoint (Uzyskiwanie punktu końcowego interfejsu API platformy Kubernetes).](azure-stack-edge-gpu-deploy-configure-compute.md#get-kubernetes-endpoints)

1. Masz dostęp do klienta, który będzie łączyć się z urządzeniem. 
    1. W tym artykule do uzyskiwania dostępu do urządzenia używany jest system klienta Windows z programem PowerShell 5.0 lub nowszym. Z obsługiwanym systemem operacyjnym możesz użyć dowolnego innego [klienta.](azure-stack-edge-gpu-system-requirements.md#supported-os-for-clients-connected-to-device) 
    1. Zainstaluj `kubectl` na kliencie. W przypadku wersji klienta:
        1. Zidentyfikuj wersję serwera Kubernetes zainstalowaną na urządzeniu. W lokalnym interfejsie użytkownika urządzenia przejdź do **strony Aktualizacje** oprogramowania. Zwróć uwagę **na wersję serwera Kubernetes na** tej stronie.
        1. Pobierz klienta, który odbiega od wersji głównej nie więcej niż o jedną wersję podrzędną. Wersja klienta, ale może prowadzić główny przez maksymalnie jedną wersję pomocniczą. Na przykład węzeł główny w wersji 1.3 powinien współpracować z węzłami w wersji 1.1, 1.2 i 1.3 oraz z klientami w wersji 1.2, 1.3 i 1.4. Aby uzyskać więcej informacji na temat wersji klienta Kubernetes, zobacz Zasady obsługi niesyłania wersji i wersji usługi [Kubernetes.](https://kubernetes.io/docs/setup/release/version-skew-policy/#supported-version-skew)
    
1. Opcjonalnie zainstaluj [narzędzia klienckie do wdrażania aplikacji i zarządzania nimi usługi danych z obsługą usługi Azure Arc](../azure-arc/data/install-client-tools.md). Te narzędzia nie są wymagane, ale zalecane.  
1. Upewnij się, że masz wystarczającą ilość zasobów na urządzeniu, aby aprowizować kontroler danych i jeden SQL Managed Instance. W przypadku kontrolera danych SQL Managed Instance wymagane jest co najmniej 16 GB pamięci RAM i 4 rdzenie procesora CPU. Aby uzyskać szczegółowe wskazówki, przejdź do [tematu Minimalne wymagania usługi danych z obsługą usługi Azure Arc wdrożenia.](../azure-arc/data/sizing-guidance.md#minimum-deployment-requirements)


## <a name="configure-kubernetes-external-service-ips"></a>Konfigurowanie zewnętrznych ip usługi Kubernetes

1. Przejdź do lokalnego internetowego interfejsu użytkownika urządzenia, a następnie przejdź do usługi **Compute.**
1. Wybierz sieć z włączoną obsługą obliczeń. 

    ![Strona Obliczenia w lokalnym interfejsie użytkownika 2](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-1.png)

1. Upewnij się, że są dostępne trzy dodatkowe zewnętrzne ip usługi Kubernetes (oprócz tych, które zostały już skonfigurowane dla innych usług zewnętrznych lub kontenerów). Kontroler danych będzie używać dwóch adresów IP usługi, a trzeci adres IP jest używany podczas tworzenia SQL Managed Instance. Będziesz potrzebować jednego adresu IP dla każdej dodatkowej usługi danych, która zostanie wdrożona. 

    ![Strona Obliczenia w lokalnym interfejsie użytkownika 3](./media/azure-stack-edge-gpu-deploy-arc-data-controller/compute-network-2.png)

1. Zastosuj ustawienia, a te nowe ip od razu zajdą w życie w istniejącym klastrze Kubernetes. 


## <a name="deploy-azure-arc-data-controller"></a>Wdrażanie Azure Arc danych

Przed wdrożeniem kontrolera danych należy utworzyć przestrzeń nazw.

### <a name="create-namespace"></a>Create namespace 

Utwórz nową dedykowaną przestrzeń nazw, w której zostanie wdrożony kontroler danych. Utworzysz również użytkownika, a następnie przyznasz użytkownikowi dostęp do utworzonej przestrzeni nazw. 

> [!NOTE]
> Zarówno w przypadku nazw nazw, jak i nazw użytkowników stosowane są konwencje [nazewnictwa](https://kubernetes.io/docs/concepts/overview/working-with-objects/names/#dns-subdomain-names) domen poddomeny DNS.

1. [Połącz się z interfejsem programu PowerShell.](azure-stack-edge-gpu-connect-powershell-interface.md#connect-to-the-powershell-interface)
1. Tworzenie przestrzeni nazw. Wpisz:

    `New-HcsKubernetesNamespace -Namespace <Name of namespace>`

1. Utwórz użytkownika. Wpisz: 

    `New-HcsKubernetesUser -UserName <User name>`

1. Plik konfiguracji jest wyświetlany w postaci zwykłego tekstu. Skopiuj ten plik i zapisz go jako *plik* konfiguracji. 

    > [!IMPORTANT]
    > Nie zapisuj pliku konfiguracji jako *pliku txt,* zapisz plik bez żadnego rozszerzenia pliku.

1. Plik konfiguracji powinien zostać udostępniony `.kube` w folderze profilu użytkownika na komputerze lokalnym. Skopiuj plik do tego folderu w profilu użytkownika.

    ![Lokalizacja pliku konfiguracji na kliencie](media/azure-stack-edge-gpu-create-kubernetes-cluster/location-config-file.png)
1. Przyznaj użytkownikowi dostęp do utworzonej przestrzeni nazw. Wpisz: 

    `Grant-HcsKubernetesNamespaceAccess -Namespace <Name of namespace> -UserName <User name>`

    Oto przykładowe dane wyjściowe poprzednich poleceń. W tym przykładzie tworzymy przestrzeń nazw, czyli użytkownika, i udzielamy `myadstest` mu dostępu do przestrzeni `myadsuser` nazw.
    
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

    1. Uruchom Notatnik jako administrator i otwórz plik `hosts` znajdujący się pod . `C:\windows\system32\drivers\etc\hosts` 
    2. Użyj informacji zapisanych na stronie **Urządzenie** w lokalnym interfejsie użytkownika (wymaganie wstępne), aby utworzyć wpis w pliku hosts. 

        Na przykład skopiuj ten punkt końcowy, `https://compute.myasegpudev.microsoftdatabox.com/[10.100.10.10]` aby utworzyć następujący wpis z adresem IP urządzenia i domeną DNS: 

        `10.100.10.10 compute.myasegpudev.microsoftdatabox.com`

1. Aby sprawdzić, czy możesz nawiązać połączenie z zasobnikami kubernetes, uruchom wiersz polecenia lub sesję programu PowerShell. Wpisz:
    
    ```powershell
    PS C:\WINDOWS\system32> kubectl get pods -n "myadstest"
    No resources found.
    PS C:\WINDOWS\system32>
    ```
Teraz możesz wdrożyć aplikacje kontrolera danych i usług danych w przestrzeni nazw , a następnie wyświetlić aplikacje i ich dzienniki.

### <a name="create-data-controller"></a>Tworzenie kontrolera danych

Kontroler danych to kolekcja zasobników wdrożonych w klastrze Kubernetes w celu zapewnienia interfejsu API, usługi kontrolera, programu inicjatora oraz monitorujących baz danych i pulpitów nawigacyjnych. Wykonaj następujące kroki, aby utworzyć kontroler danych w klastrze Kubernetes, który istnieje na urządzeniu Azure Stack Edge w utworzonej wcześniej przestrzeni nazw.   

1. Zbierz następujące informacje potrzebne do utworzenia kontrolera danych:

    
    |Column1  |Kolumna2  |
    |---------|---------|
    |Nazwa kontrolera danych     |Opisowa nazwa kontrolera danych. Na przykład `arctestdatacontroller`.         |
    |Nazwa użytkownika kontrolera danych     |Dowolna nazwa użytkownika administratora kontrolera danych. Nazwa użytkownika i hasło kontrolera danych są używane do uwierzytelniania w interfejsie API kontrolera danych w celu wykonywania funkcji administracyjnych.          |
    |Hasło kontrolera danych     |Hasło użytkownika administratora kontrolera danych. Wybierz bezpieczne hasło i udostępnij je tylko tym, którzy muszą mieć uprawnienia administratora klastra.         |
    |Nazwa przestrzeni nazw kubernetes     |Nazwa przestrzeni nazw kubernetes, w której chcesz utworzyć kontroler danych.         |
    |Identyfikator subskrypcji platformy Azure     |Identyfikator GUID subskrypcji platformy Azure dla miejsca, w którym chcesz utworzyć zasób kontrolera danych na platformie Azure.         |
    |Nazwa grupy zasobów platformy Azure     |Nazwa grupy zasobów, w której ma zostać utworzony zasób kontrolera danych na platformie Azure.         |
    |Lokalizacja platformy Azure     |Lokalizacja platformy Azure, w której metadane zasobów kontrolera danych będą przechowywane na platformie Azure. Aby uzyskać listę dostępnych regionów, zobacz Globalna infrastruktura platformy Azure / Produkty według regionów.|


1. Nawiąż połączenie z interfejsem programu PowerShell. Aby utworzyć kontroler danych, wpisz: 

    ```powershell
    Set-HcsKubernetesAzureArcDataController -SubscriptionId <Subscription ID> -ResourceGroupName <Resource group name> -Location <Location without spaces> -UserName <User you created> -Password <Password to authenticate to Data Controller> -DataControllerName <Data Controller Name> -Namespace <Namespace you created>    
    ```
    Oto przykładowe dane wyjściowe poprzednich poleceń.

    ```powershell
    [10.100.10.10]: PS>Set-HcsKubernetesAzureArcDataController -SubscriptionId db4e2fdb-6d80-4e6e-b7cd-736098270664 -ResourceGroupName myasegpurg -Location "EastUS" -UserName myadsuser -Password "Password1" -DataControllerName "arctestcontroller" -Namespace myadstest
    [10.100.10.10]: PS> 
    ```
    
    Wdrożenie może potrwać około 5 minut.

    > [!NOTE]
    > Kontroler danych utworzony w klastrze Kubernetes na urządzeniu Azure Stack Edge Pro GPU działa tylko w trybie odłączonym w bieżącej wersji. Tryb odłączony jest dla kontrolera danych, a nie dla Twojego urządzenia.

### <a name="monitor-data-creation-status"></a>Monitorowanie stanu tworzenia danych

1. Otwórz inne okno programu PowerShell.
1. Użyj następującego `kubectl` polecenia, aby monitorować stan tworzenia kontrolera danych. 

    ```powershell
    kubectl get datacontroller/<Data controller name> --namespace <Name of your namespace>
    ```
    Po utworzeniu kontrolera powinien on mieć stan `Ready` .
    Oto przykładowe dane wyjściowe poprzedniego polecenia:

    ```powershell
    PS C:\WINDOWS\system32> kubectl get datacontroller/arctestcontroller --namespace myadstest
    NAME                STATE
    arctestcontroller   Ready
    PS C:\WINDOWS\system32>
    ```
1. Aby zidentyfikować ip przypisane do usług zewnętrznych uruchomionych na kontrolerze danych, użyj `kubectl get svc -n <namespace>` polecenia . Oto przykładowe dane wyjściowe:

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

## <a name="deploy-sql-managed-instance"></a>Wdrażanie wystąpienia zarządzanego SQL

Po pomyślnym utworzeniu kontrolera danych można użyć szablonu do wdrożenia SQL Managed Instance na kontrolerze danych.

### <a name="deployment-template"></a>Szablon wdrożenia

Użyj następującego szablonu wdrożenia, aby wdrożyć SQL Managed Instance na kontrolerze danych na urządzeniu.

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
    
Nazwa metadanych jest nazwą SQL Managed Instance. Skojarzony zasobnik w poprzednim pliku `deployment.yaml` będzie mieć nazwę o nazwie `sqlex-n` `n` (to liczba zasobników skojarzonych z aplikacją). 
    
#### <a name="password-and-username-data"></a>Dane hasła i nazwy użytkownika

Nazwa użytkownika i hasło kontrolera danych są używane do uwierzytelniania w interfejsie API kontrolera danych w celu wykonywania funkcji administracyjnych. Klucz tajny Kubernetes nazwy użytkownika i hasła kontrolera danych w szablonie wdrożenia to ciągi zakodowane w formacie base64. 

Możesz użyć narzędzia online, aby zakodować żądaną nazwę użytkownika i hasło w formacie base64, lub użyć wbudowanych narzędzi interfejsu wiersza polecenia w zależności od platformy. W przypadku korzystania z narzędzia kodowania Base64 w trybie online podaj w narzędziu nazwę użytkownika i ciągi hasła (wprowadzone podczas tworzenia kontrolera danych), a narzędzie wygeneruje odpowiednie ciągi zakodowane w formacie Base64.
    
#### <a name="service-type"></a>Typ usługi

Typ usługi powinien być ustawiony na `LoadBalancer` .
    
#### <a name="storage-class-name"></a>Nazwa klasy magazynu

Możesz zidentyfikować klasę magazynu na urządzeniu Azure Stack Edge, które będzie wykorzystywane przez wdrożenie do przechowywania danych, kopii zapasowych, dzienników danych i dzienników. Użyj polecenia  `kubectl get storageclass` , aby wdrożyć klasę magazynu na urządzeniu.

```powershell
PS C:\WINDOWS\system32> kubectl get storageclass
NAME             PROVISIONER      RECLAIMPOLICY  VOLUMEBINDINGMODE     ALLOWVOLUMEEXPANSION   AGE
ase-node-local   rancher.io/local-path   Delete  WaitForFirstConsumer  false                  5d23h
PS C:\WINDOWS\system32>
```
W poprzednich przykładowych danych wyjściowych w szablonie należy określić klasę magazynu `ase-node-local` na urządzeniu.
 
#### <a name="spec"></a>Spec

Aby utworzyć SQL Managed Instance urządzeniu Azure Stack Edge, możesz określić wymagania dotyczące pamięci i procesora CPU w sekcji specyfikacji w sekcji `deployment.yaml` . Każde wystąpienie zarządzane SQL musi żądać co najmniej 2 GB pamięci i 1 rdzenia procesora CPU, jak pokazano w poniższym przykładzie. 

```yml
spec:
    limits:
    memory: 4Gi
    vcores: "4"
    requests:
    memory: 2Gi
    vcores: "1"
```  

Aby uzyskać szczegółowe wskazówki dotyczące rozmiarów kontrolera danych i 1 SQL Managed Instance, zapoznaj się ze szczegółami [rozmiaru wystąpienia zarządzanego SQL.](../azure-arc/data/sizing-guidance.md#sql-managed-instance-sizing-details)

### <a name="run-deployment-template"></a>Uruchamianie szablonu wdrożenia

Uruchom polecenie `deployment.yaml` przy użyciu następującego polecenia:

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

Zasobnik `sqlex-0` w przykładowych danych wyjściowych wskazuje stan SQL Managed Instance.

## <a name="remove-data-controller"></a>Usuwanie kontrolera danych

Aby usunąć kontroler danych, usuń dedykowaną przestrzeń nazw, w której został wdrożony.


```powershell
kubectl delete ns <Name of your namespace>
```


## <a name="next-steps"></a>Następne kroki

- [Wdrażanie aplikacji bez stanowej na komputerze Azure Stack Edge Pro](./azure-stack-edge-gpu-deploy-stateless-application-kubernetes.md).
