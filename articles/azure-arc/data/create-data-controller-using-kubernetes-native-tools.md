---
title: Tworzenie kontrolera danych przy użyciu narzędzi Kubernetes
description: Tworzenie kontrolera danych przy użyciu narzędzi Kubernetes
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: twright-msft
ms.author: twright
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 9c5a2aa6ca75e352a824716c19af923c8628efde
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91345475"
---
# <a name="create-azure-arc-data-controller-using-kubernetes-tools"></a>Tworzenie kontrolera danych usługi Azure ARC przy użyciu narzędzi Kubernetes

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Zapoznaj się z tematem [Tworzenie kontrolera danych usługi Azure ARC,](create-data-controller.md) Aby uzyskać informacje na temat przeglądu.

Aby utworzyć kontroler danych usługi Azure ARC przy użyciu narzędzi Kubernetes, należy zainstalować narzędzia Kubernetes.  Przykłady w tym artykule będą używane `kubectl` , ale podobne podejścia mogą być używane z innymi narzędziami Kubernetes, takimi jak pulpit nawigacyjny Kubernetes, `oc` lub `helm` wiedząc, że narzędzia i Kubernetes YAML/JSON.

[Instalowanie narzędzia polecenia kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/)

> [!NOTE]
> Niektóre kroki tworzenia kontrolera danych usługi Azure Arc wskazane poniżej wymagają uprawnień administratora klastra Kubernetes.  Jeśli nie jesteś administratorem klastra Kubernetes, musisz mieć uprawnienia administratora klastra Kubernetes w Twoim imieniu.

## <a name="overview"></a>Omówienie

Tworzenie kontrolera danych usługi Azure Arc obejmuje następujące kroki wysokiego poziomu:
1. Utwórz niestandardowe definicje zasobów dla kontrolera danych ARC, wystąpienia zarządzanego Azure SQL i skalowania PostgreSQL. **[Wymaga uprawnień administratora klastra Kubernetes]**
2. Utwórz przestrzeń nazw, w której zostanie utworzony kontroler danych. **[Wymaga uprawnień administratora klastra Kubernetes]**
3. Utwórz usługę programu inicjującego, w tym zestaw replik, konto usługi, rolę i powiązanie roli.
4. Utwórz klucz tajny dla nazwy użytkownika i hasła administratora kontrolera danych.
5. Utwórz kontroler danych.
   
## <a name="create-the-custom-resource-definitions"></a>Tworzenie niestandardowych definicji zasobów

Uruchom następujące polecenie, aby utworzyć niestandardowe definicje zasobów.  **[Wymaga uprawnień administratora klastra Kubernetes]**

```console
kubectl create -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/custom-resource-definitions.yaml
```

## <a name="create-a-namespace-in-which-the-data-controller-will-be-created"></a>Tworzenie przestrzeni nazw, w której zostanie utworzony kontroler danych

Uruchom polecenie podobne do poniższego, aby utworzyć nową, dedykowaną przestrzeń nazw, w której zostanie utworzony kontroler danych.  W tym przykładzie i w pozostałej części przykładów w tym artykule `arc` zostanie użyta nazwa przestrzeni nazw. Jeśli zdecydujesz się użyć innej nazwy, Użyj tej samej nazwy w programie.

```console
kubectl create namespace arc
```

Jeśli inne osoby będą korzystać z tej przestrzeni nazw, które nie są administratorami klastrów, zalecamy utworzenie roli administratora przestrzeni nazw i przyznanie tej roli użytkownikom za pośrednictwem powiązania roli.  Administrator przestrzeni nazw powinien mieć pełne uprawnienia do przestrzeni nazw.  Więcej informacji można znaleźć w dalszej części tego, jak zapewnić użytkownikom bardziej szczegółowy dostęp oparty na rolach.

## <a name="create-the-bootstrapper-service"></a>Tworzenie usługi programu inicjującego

Usługa programu inicjującego obsługuje żądania przychodzące do tworzenia, edytowania i usuwania zasobów niestandardowych, takich jak kontroler danych, wystąpienie zarządzane SQL lub Grupa serwerów PostgreSQL.

Uruchom następujące polecenie, aby utworzyć usługę programu inicjującego, konto usługi dla usługi programu inicjującego oraz powiązanie roli i roli dla konta usługi programu inicjującego.

```console
kubectl create --namespace arc -f https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/bootstrapper.yaml
```

Sprawdź, czy program inicjujący jest uruchomiony przy użyciu następującego polecenia.  Może być konieczne uruchomienie go kilka razy, aż stan zmieni się na `Running` .

```console
kubectl get pod --namespace arc
```

Plik szablonu programu inicjującego. YAML domyślnie pobiera obraz kontenera programu inicjującego z Container Registry Microsoft (MCR).  Jeśli Twoje środowisko nie ma dostępu bezpośrednio do Container Registry firmy Microsoft, możesz wykonać następujące czynności:
- Postępuj zgodnie z instrukcjami, aby [ściągnąć obrazy kontenerów z Container Registry firmy Microsoft i wypchnąć je do prywatnego rejestru kontenerów](offline-deployment.md).
- [Utwórz klucz tajny ściągania obrazu](https://kubernetes.io/docs/tasks/configure-pod-container/pull-image-private-registry/#create-a-secret-by-providing-credentials-on-the-command-lin) dla prywatnego rejestru kontenerów.
- Dodaj klucz tajny ściągania obrazu do kontenera programu inicjującego. Zobacz przykład poniżej.
- Zmień lokalizację obrazu dla obrazu programu inicjującego. Zobacz przykład poniżej.

W poniższym przykładzie przyjęto założenie, że utworzono nazwę klucza tajnego ściągania obrazu `regcred` , jak wskazano w dokumentacji Kubernetes.

```yaml
#just showing only the relevant part of the bootstrapper.yaml template file here
containers:
      - env:
        - name: ACCEPT_EULA
          value: "Y"
        #image: mcr.microsoft.com/arcdata/arc-bootstrapper:public-preview-sep-2020 <-- template value to change
        image: <your registry DNS name or IP address>/<your repo>/arc-bootstrapper:<your tag>
        imagePullPolicy: IfNotPresent
        name: bootstrapper
        resources: {}
        securityContext:
          runAsUser: 21006
        terminationMessagePath: /dev/termination-log
        terminationMessagePolicy: File
      dnsPolicy: ClusterFirst
      imagePullSecrets:
      - name: regcred
      restartPolicy: Always
      schedulerName: default-scheduler
      securityContext: {}
      serviceAccount: sa-mssql-controller
      serviceAccountName: sa-mssql-controller
      terminationGracePeriodSeconds: 30

```

## <a name="create-a-secret-for-the-data-controller-administrator"></a>Tworzenie wpisu tajnego dla administratora kontrolera danych

Nazwa użytkownika i hasło administratora kontrolera danych są używane do uwierzytelniania w interfejsie API kontrolera danych w celu wykonywania funkcji administracyjnych.  Wybierz bezpieczne hasło i udostępnij je tylko osobom, które muszą mieć uprawnienia administratora klastra.

Wpis tajny Kubernetes jest przechowywany jako ciąg zakodowany w formacie base64 — jeden dla nazwy użytkownika i jeden dla hasła.

Możesz użyć narzędzia online do kodowania base64 żądanej nazwy użytkownika i hasła lub użyć wbudowanych narzędzi interfejsu wiersza polecenia w zależności od platformy.

PowerShell

```console
[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('<your string to encode here>'))

#Example
#[Convert]::ToBase64String([System.Text.Encoding]::Unicode.GetBytes('example'))

```

Linux/macOS

```console
echo '<your string to encode here>' | base64

#Example
# echo 'example' | base64
```

Po zakodowaniu nazwy użytkownika i hasła można utworzyć plik oparty na [pliku szablonu](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/controller-login-secret.yaml) i zastąpić wartości nazwy użytkownika i hasła własnym.

Następnie uruchom następujące polecenie, aby utworzyć wpis tajny.

```console
kubectl create --namespace arc -f <path to your data controller secret file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\controller-login-secret.yaml
```

## <a name="create-the-data-controller"></a>Tworzenie kontrolera danych

Teraz można przystąpić do tworzenia samego kontrolera danych.

Najpierw utwórz kopię [pliku szablonu](https://raw.githubusercontent.com/microsoft/azure_arc/master/arc_data_services/deploy/yaml/data-controller.yaml) lokalnie na komputerze, aby można było zmodyfikować niektóre ustawienia.

W razie konieczności Edytuj następujące elementy:

**Wymagane**
- **Lokalizacja**: Zmień to na lokalizację platformy Azure, w której będą przechowywane _metadane_ dotyczące kontrolera danych.  Listę dostępnych lokalizacji platformy Azure można znaleźć w artykule [Przegląd tworzenia kontrolera danych](create-data-controller.md) .
- Grupa **zasobów: usługa**Azure Resource Group, w której chcesz utworzyć zasób platformy Azure kontrolera danych w Azure Resource Manager.  Zwykle ta grupa zasobów powinna już istnieć, ale nie jest wymagana do czasu przekazania danych na platformę Azure.
- **subskrypcja**: identyfikator GUID subskrypcji platformy Azure dla subskrypcji, w której chcesz utworzyć zasoby platformy Azure.

**ZALECANE DO PRZEGLĄDANIA I EWENTUALNYCH ZMIAN DOMYŚLNYCH**
- **Magazyn... className**: Klasa magazynu, która ma być używana dla plików danych i dziennika kontrolera danych.  Jeśli nie masz pewności co do dostępnych klas magazynu w klastrze Kubernetes, możesz uruchomić następujące polecenie: `kubectl get storageclass` .  Wartość domyślna to `default` zakłada, że istnieje Klasa magazynu, która istnieje i ma nazwę, która `default` nie jest klasą magazynu, która _jest_ wartością domyślną.  Uwaga: Istnieją dwa ustawienia className, które mają zostać ustawione na żądaną klasę magazynu — jeden dla danych i jeden dla dzienników.
- **ServiceType**: Zmień typ usługi na, `NodePort` Jeśli nie używasz modułu równoważenia obciążenia.  Uwaga: Istnieją dwa ustawienia, które wymagają zmiany.

**OBOWIĄZKOWE**
- **Nazwa**: domyślna nazwa kontrolera danych to `arc` , ale można ją zmienić w razie potrzeby.
- **DisplayName**: Ustaw tę samą wartość, która jest taka sama jak nazwa atrybutu w górnej części pliku.
- **Rejestr**: wartość domyślna to Microsoft Container Registry.  W przypadku ściągania obrazów z Container Registry firmy Microsoft i [wypchnięcia ich do prywatnego rejestru kontenerów](offline-deployment.md)Wprowadź tutaj adres IP lub nazwę DNS rejestru.
- **dockerRegistry**: w razie potrzeby ściągają obrazy z prywatnego rejestru kontenerów.
- **repozytorium**: domyślne repozytorium w Container Registry firmy Microsoft to `arcdata` .  Jeśli używasz prywatnego rejestru kontenerów, wprowadź ścieżkę do folderu/repozytorium zawierającego obrazy kontenerów usług Azure ARR Data Services.
- **imageTag**: bieżący tag wersji jest domyślny w szablonie, ale możesz go zmienić, jeśli chcesz użyć starszej wersji.

Przykład ukończonego pliku YAML kontrolera danych:
```yaml
apiVersion: arcdata.microsoft.com/v1alpha1
kind: datacontroller
metadata:
  generation: 1
  name: arc
spec:
  credentials:
    controllerAdmin: controller-login-secret
    #dockerRegistry: mssql-private-registry - optional if you are using a private container registry that requires authentication using an image pull secret
    serviceAccount: sa-mssql-controller
  docker:
    imagePullPolicy: Always
    imageTag: public-preview-sep-2020
    registry: mcr.microsoft.com
    repository: arcdata
  security:
    allowDumps: true
    allowNodeMetricsCollection: true
    allowPodMetricsCollection: true
    allowRunAsRoot: false
  services:
  - name: controller
    port: 30080
    serviceType: LoadBalancer
  - name: serviceProxy
    port: 30777
    serviceType: LoadBalancer
  settings:
    ElasticSearch:
      vm.max_map_count: "-1"
    azure:
      connectionMode: Indirect
      location: eastus
      resourceGroup: myresourcegroup
      subscription: c82c901a-129a-435d-86e4-cc6b294590ae
    controller:
      displayName: arc
      enableBilling: "True"
      logs.rotation.days: "7"
      logs.rotation.size: "5000"
  storage:
    data:
      accessMode: ReadWriteOnce
      className: default
      size: 15Gi
    logs:
      accessMode: ReadWriteOnce
      className: default
      size: 10Gi
```

Zapisz edytowany plik na komputerze lokalnym i uruchom następujące polecenie, aby utworzyć kontroler danych:

```console
kubectl create --namespace arc -f <path to your data controller file>

#Example
kubectl create --namespace arc -f C:\arc-data-services\data-controller.yaml
```

## <a name="monitoring-the-creation-status"></a>Monitorowanie stanu tworzenia

Tworzenie kontrolera potrwa kilka minut. Postęp można monitorować w innym oknie terminalu przy użyciu następujących poleceń:

> [!NOTE]
>  W przykładowych poleceniach przyjęto założenie, że utworzono kontroler danych i Kubernetes przestrzeń nazw o nazwie `arc` .  Jeśli użyto innej nazwy obszaru nazw/kontrolera danych, możesz zamienić ją `arc` na nazwę.

```console
kubectl get datacontroller/arc --namespace arc
```

```console
kubectl get pods --namespace arc
```

Możesz również sprawdzić stan tworzenia dowolnego określonego obszaru pod, uruchamiając polecenie podobne do poniższego.  Jest to szczególnie przydatne w przypadku rozwiązywania problemów.

```console
kubectl describe po/<pod name> --namespace arc

#Example:
#kubectl describe po/control-2g7bl --namespace arc
```

Rozszerzenie Azure ARC dla Azure Data Studio zawiera Notes, który przeprowadzi Cię przez proces konfigurowania usługi Azure ARC z włączonym Kubernetes i skonfigurowania jej do monitorowania repozytorium git zawierającego przykładowy plik YAML wystąpienia zarządzanego SQL. Gdy wszystko jest połączone, nowe wystąpienie zarządzane SQL zostanie wdrożone w klastrze Kubernetes.

Zapoznaj się z artykułem **Wdrażanie wystąpienia zarządzanego SQL przy użyciu funkcji Azure Arc Kubernetes i strumieniowego** notesu w rozszerzeniu usługi Azure arc dla Azure Data Studio.

## <a name="troubleshooting-creation-problems"></a>Rozwiązywanie problemów z tworzeniem

W przypadku wystąpienia problemów z tworzeniem należy zapoznać się z [przewodnikiem rozwiązywania problemów](troubleshoot-guide.md).

## <a name="next-steps"></a>Następne kroki

- [Tworzenie wystąpienia zarządzanego SQL przy użyciu narzędzi Kubernetes-Native Tools](./create-sql-managed-instance-using-kubernetes-native-tools.md)
- [Tworzenie grupy serwerów PostgreSQL do skalowania przy użyciu narzędzi Kubernetes-Native](./create-postgresql-hyperscale-server-group-kubernetes-native-tools.md)
