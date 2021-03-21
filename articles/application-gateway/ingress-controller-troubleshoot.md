---
title: Application Gateway Rozwiązywanie problemów z kontrolerem transferu danych przychodzących
description: Ten artykuł zawiera dokumentację dotyczącą rozwiązywania typowych pytań i/lub problemów z Application Gatewaym kontrolerem danych przychodzących.
services: application-gateway
author: caya
ms.service: application-gateway
ms.topic: troubleshooting
ms.date: 06/18/2020
ms.author: caya
ms.openlocfilehash: f2b9f79f0914e645c736f8a577c46baa42587332
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94874614"
---
# <a name="troubleshoot-common-questions-or-issues-with-ingress-controller"></a>Rozwiązywanie typowych pytań lub problemów z kontrolerem transferu danych przychodzących

[Azure Cloud Shell](https://shell.azure.com/) jest najwygodniejszym sposobem rozwiązywania problemów z instalacją AKS i AGIC. Uruchom powłokę z [Shell.Azure.com](https://shell.azure.com/) lub klikając łącze:

[![Uruchamianie osadzane](https://shell.azure.com/images/launchcloudshell.png "Uruchamianie usługi Azure Cloud Shell")](https://shell.azure.com)


## <a name="test-with-a-simple-kubernetes-app"></a>Testowanie przy użyciu prostej aplikacji Kubernetes

W poniższych krokach przyjęto, że:
  - Masz klaster AKS z włączoną funkcją zaawansowanej sieci
  - AGIC został zainstalowany w klastrze AKS
  - Masz już Application Gateway w sieci wirtualnej udostępnionej w klastrze AKS

Aby sprawdzić, czy instalacja Application Gateway + AKS + AGIC została skonfigurowana prawidłowo, wdróż najprostszą możliwą aplikację:

```bash
cat <<EOF | kubectl apply -f -
apiVersion: v1
kind: Pod
metadata:
  name: test-agic-app-pod
  labels:
    app: test-agic-app
spec:
  containers:
  - image: "mcr.microsoft.com/dotnet/core/samples:aspnetapp"
    name: aspnetapp-image
    ports:
    - containerPort: 80
      protocol: TCP
---
apiVersion: v1
kind: Service
metadata:
  name: test-agic-app-service
spec:
  selector:
    app: test-agic-app
  ports:
  - protocol: TCP
    port: 80
    targetPort: 80
---
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
  name: test-agic-app-ingress
  annotations:
    kubernetes.io/ingress.class: azure/application-gateway
spec:
  rules:
    - host: test.agic.contoso.com
      http:
        paths:
          - path: /
            backend:
              serviceName: test-agic-app-service
              servicePort: 80
EOF
```

Skopiuj i Wklej wszystkie wiersze jednocześnie ze skryptu powyżej do [Azure Cloud Shell](https://shell.azure.com/). Upewnij się, że całe polecenie zostało skopiowane — rozpoczyna się od `cat` i włącznie z ostatnim `EOF` .

![apply](./media/application-gateway-ingress-controller-troubleshooting/tsg--apply-config.png)

Po pomyślnym wdrożeniu aplikacji powyżej AKS klaster będzie miał nową usługę pod, usługą i ruchem przychodzącym.

Pobierz listę zasobników z [Cloud Shell](https://shell.azure.com/): `kubectl get pods -o wide` .
Oczekujemy, że element pod nazwą "test-agic-App-pod" został utworzony. Będzie on miał adres IP. Ten adres musi znajdować się w sieci wirtualnej Application Gateway, która jest używana z AKS.

![Zrzut ekranu okna bash w Azure Cloud Shell przedstawiający listę zasobników zawierających test-agic-App-pod na liście.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-pods.png)

Pobierz listę usług: `kubectl get services -o wide` . Oczekujemy, że zostanie wyświetlona usługa o nazwie "test-agic-App-Service".

![Zrzut ekranu okna bash w Azure Cloud Shell przedstawiający listę usług, które zawierają test-agic-App-pod listy.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-services.png)

Pobierz listę ingresses: `kubectl get ingress` . Oczekujemy, że zasób transferu danych przychodzących o nazwie "test-agic-App-Ingres" został utworzony. Zasób będzie miał nazwę hosta "test.agic.contoso.com".

![Zrzut ekranu okna bash w Azure Cloud Shell przedstawiający listę ingresses zawierających test-agic-App-in in list.](./media/application-gateway-ingress-controller-troubleshooting/tsg--get-ingress.png)

Jednym z tych zasobników będzie AGIC. `kubectl get pods` zostanie wyświetlona lista wielowymiarowa, z których jedna będzie zaczynać się od "ruchu przychodzącego z platformy Azure". Pobierz wszystkie dzienniki tego programu pod względem programu, `kubectl logs <name-of-ingress-controller-pod>` Aby sprawdzić, czy zakończono pomyślnie wdrożenie. Pomyślne wdrożenie dodaliśmy następujące wiersze do dziennika:
```
I0927 22:34:51.281437       1 process.go:156] Applied Application Gateway config in 20.461335266s
I0927 22:34:51.281585       1 process.go:165] cache: Updated with latest applied config.
I0927 22:34:51.282342       1 process.go:171] END AppGateway deployment
```

Alternatywnie, z [Cloud Shell](https://shell.azure.com/) można pobrać tylko wiersze wskazujące pomyślne Application Gateway konfigurację z `kubectl logs <ingress-azure-....> | grep 'Applied App Gateway config in'` , gdzie `<ingress-azure....>` powinna być dokładna nazwa AGIC pod.

Application Gateway zostanie zastosowana następująca konfiguracja:

- Odbiornik: ![ odbiornik](./media/application-gateway-ingress-controller-troubleshooting/tsg--listeners.png)

- Reguła routingu: ![ routing_rule](./media/application-gateway-ingress-controller-troubleshooting/tsg--rule.png)

- Pula zaplecza:
  - W puli adresów zaplecza będzie dostępny jeden adres IP, który będzie pasował do adresu IP pod zaobserwowanej wcześniej usługą `kubectl get pods -o wide` 
 ![ backend_pool](./media/application-gateway-ingress-controller-troubleshooting/tsg--backendpools.png)


Na koniec możemy użyć `cURL` polecenia z poziomu [Cloud Shell](https://shell.azure.com/) , aby NAwiązać połączenie HTTP z nowo wdrożoną aplikacją:

1. Użyj, `kubectl get ingress` Aby uzyskać publiczny adres IP Application Gateway
2. Korzystanie z polecenia `curl -I -H 'test.agic.contoso.com' <publitc-ip-address-from-previous-command>`

![Zrzut ekranu okna bash w Azure Cloud Shell pokazujący polecenie zwinięcie pomyślnie nawiązać połączenie HTTP z aplikacją testową.](./media/application-gateway-ingress-controller-troubleshooting/tsg--curl.png)

Wynik `HTTP/1.1 200 OK` wskazuje, że system Application Gateway i AKS + AGIC działa zgodnie z oczekiwaniami.


## <a name="inspect-kubernetes-installation"></a>Inspekcja instalacji Kubernetes

### <a name="pods-services-ingress"></a>, Usługi, ruch przychodzący
Application Gateway kontroler usług przychodzących (AGIC) nieustannie monitoruje następujące zasoby Kubernetes: [wdrożenie](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#creating-a-deployment) lub [pod](https://kubernetes.io/docs/concepts/workloads/pods/pod/#what-is-a-pod), [Usługa](https://kubernetes.io/docs/concepts/services-networking/service/), [ruch](https://kubernetes.io/docs/concepts/services-networking/ingress/) przychodzący


Aby AGIC działał zgodnie z oczekiwaniami, należy wykonać następujące czynności:
  1. AKS musi mieć co najmniej jeden z prawidłowych **zasobników**.
     Sprawdź poprawność tego elementu [Cloud Shell](https://shell.azure.com/) w `kubectl get pods -o wide --show-labels` przypadku, gdy posiadasz element z `apsnetapp` , dane wyjściowe mogą wyglądać następująco:
     ```bash
     delyan@Azure:~$ kubectl get pods -o wide --show-labels

     NAME                   READY   STATUS    RESTARTS   AGE   IP          NODE                       NOMINATED NODE   READINESS GATES   LABELS
     aspnetapp              1/1     Running   0          17h   10.0.0.6    aks-agentpool-35064155-1   <none>           <none>            app=aspnetapp
     ```

  2. Co najmniej jedna **Usługa**, odwołująca się do powyższych zasobników za pośrednictwem pasujących `selector` etykiet.
     Sprawdź poprawność tego [Cloud Shell](https://shell.azure.com/) z `kubectl get services -o wide`
     ```bash
     delyan@Azure:~$ kubectl get services -o wide --show-labels

     NAME                TYPE        CLUSTER-IP     EXTERNAL-IP   PORT(S)   AGE   SELECTOR        LABELS
     aspnetapp           ClusterIP   10.2.63.254    <none>        80/TCP    17h   app=aspnetapp   <none>     
     ```

  3. Ruch przychodzący **, z** adnotacjami `kubernetes.io/ingress.class: azure/application-gateway` , odwołujący się do usługi powyżej, Weryfikuj ten element z [Cloud Shell](https://shell.azure.com/) z`kubectl get ingress -o wide --show-labels`
     ```bash
     delyan@Azure:~$ kubectl get ingress -o wide --show-labels

     NAME        HOSTS   ADDRESS   PORTS   AGE   LABELS
     aspnetapp   *                 80      17h   <none>
     ```

  4. Wyświetl adnotacje powyższych danych wejściowych: (Zastąp nazwą swojego ruchu przychodzącego `kubectl get ingress aspnetapp -o yaml` `aspnetapp` )
     ```bash
     delyan@Azure:~$ kubectl get ingress aspnetapp -o yaml

     apiVersion: extensions/v1beta1
     kind: Ingress
     metadata:
       annotations:
         kubernetes.io/ingress.class: azure/application-gateway
       name: aspnetapp
     spec:
       backend:
         serviceName: aspnetapp
         servicePort: 80
     ```

     Do zasobu transferu danych przychodzących należy dodać adnotację `kubernetes.io/ingress.class: azure/application-gateway` .
 

### <a name="verify-observed-namespace"></a>Sprawdź zaobserwowany obszar nazw

* Pobierz istniejące przestrzenie nazw w klastrze Kubernetes. W jakim obszarze nazw jest uruchomiona aplikacja? Czy AGIC ogląda tę przestrzeń nazw? Zapoznaj się z dokumentacją dotyczącą [obsługi wielu przestrzeni nazw](./ingress-controller-multiple-namespace-support.md#enable-multiple-namespace-support) , aby poprawnie skonfigurować obserwowane obszary nazw.

    ```bash
    # What namespaces exist on your cluster
    kubectl get namespaces
    
    # What pods are currently running
    kubectl get pods --all-namespaces -o wide
    ```


* AGIC pod musi znajdować się w `default` przestrzeni nazw (zobacz kolumna `NAMESPACE` ). W kolumnie znajduje się w dobrej kondycji `Running` `STATUS` . Powinien być co najmniej jeden AGIC pod.

    ```bash
    # Get a list of the Application Gateway Ingress Controller pods
    kubectl get pods --all-namespaces --selector app=ingress-azure
    ```


* Jeśli AGIC pod nie jest w dobrej kondycji ( `STATUS` kolumna z powyższego polecenia nie jest `Running` ):
  - Pobierz dzienniki, aby zrozumieć, dlaczego: `kubectl logs <pod-name>`
  - dla poprzedniego wystąpienia: `kubectl logs <pod-name> --previous`
  - opisz element pod, aby uzyskać więcej kontekstu: `kubectl describe pod <pod-name>`


* Czy masz [usługę](https://kubernetes.io/docs/concepts/services-networking/service/) [Kubernetes i zasoby](https://kubernetes.io/docs/concepts/services-networking/ingress/) dotyczące transferu danych przychodzących?
    
    ```bash
    # Get all services across all namespaces
    kubectl get service --all-namespaces -o wide
    
    # Get all ingress resources across all namespaces
    kubectl get ingress --all-namespaces -o wide
    ```


* [Czy Twoje](https://kubernetes.io/docs/concepts/services-networking/ingress/) przychodzące adnotacje są z: `kubernetes.io/ingress.class: azure/application-gateway` ? AGIC będzie oglądać tylko dla zasobów przychodzących Kubernetes, które mają tę adnotację.
    
    ```bash
    # Get the YAML definition of a particular ingress resource
    kubectl get ingress --namespace  <which-namespace?>  <which-ingress?>  -o yaml
    ```


* AGIC emituje zdarzenia Kubernetes dla pewnych błędów krytycznych. Można je wyświetlić:
  - w terminalu za pośrednictwem `kubectl get events --sort-by=.metadata.creationTimestamp`
  - w przeglądarce przy użyciu [interfejsu użytkownika sieci Web Kubernetes (pulpit nawigacyjny)](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/)


## <a name="logging-levels"></a>Poziomy rejestrowania

AGIC ma 3 poziomy rejestrowania. Poziom 1 jest domyślnym i zawiera minimalną liczbę wierszy dziennika.
Poziom 5, z drugiej strony, będzie wyświetlał wszystkie dzienniki, w tym oczyszczoną zawartość konfiguracji zastosowanej do ARM.

Społeczność Kubernetes ustanowiła 9 poziomów rejestrowania dla narzędzia [polecenia kubectl](https://kubernetes.io/docs/reference/kubectl/cheatsheet/#kubectl-output-verbosity-and-debugging) . W tym repozytorium wykorzystujemy 3 z nich z podobną semantyką:


| Szczegółowość | Opis |
|-----------|-------------|
|  1        | Domyślny poziom rejestrowania; pokazuje szczegóły uruchamiania, ostrzeżenia i błędy |
|  3        | Rozszerzone informacje o zdarzeniach i zmianach; listy utworzonych obiektów |
|  5        | Rejestruje obiekty organizowane; pokazuje oczyszczoną konfigurację JSON zastosowaną do ARM |


Poziomy szczegółowości są ustawiane za pośrednictwem `verbosityLevel` zmiennej w pliku [Helm-config. YAML](#sample-helm-config-file) . Zwiększ poziom szczegółowości, aby `5` uzyskać konfigurację JSON wysłaną do [ARM](../azure-resource-manager/management/overview.md):
  - Dodaj `verbosityLevel: 5` bezpośrednio w wierszu w [Helm-config. YAML](#sample-helm-config-file) i ponownie zainstaluj
  - Pobierz dzienniki z `kubectl logs <pod-name>`

### <a name="sample-helm-config-file"></a>Przykładowy plik konfiguracji Helm
```yaml
# This file contains the essential configs for the ingress controller helm chart

# Verbosity level of the App Gateway Ingress Controller
verbosityLevel: 3

################################################################################
# Specify which application gateway the ingress controller will manage
#
appgw:
    subscriptionId: <subscriptionId>
    resourceGroup: <resourceGroupName>
    name: <applicationGatewayName>

    # Setting appgw.shared to "true" will create an AzureIngressProhibitedTarget CRD.
    # This prohibits AGIC from applying config for any host/path.
    # Use "kubectl get AzureIngressProhibitedTargets" to view and change this.
    shared: false

################################################################################
# Specify which kubernetes namespace the ingress controller will watch
# Default value is "default"
# Leaving this variable out or setting it to blank or empty string would
# result in Ingress Controller observing all acessible namespaces.
#
# kubernetes:
#   watchNamespace: <namespace>

################################################################################
# Specify the authentication with Azure Resource Manager
#
# Two authentication methods are available:
# - Option 1: AAD-Pod-Identity (https://github.com/Azure/aad-pod-identity)
armAuth:
    type: aadPodIdentity
    identityResourceID: <identityResourceId>
    identityClientID:  <identityClientId>

## Alternatively you can use Service Principal credentials
# armAuth:
#    type: servicePrincipal
#    secretJSON: <<Generate this value with: "az ad sp create-for-rbac --subscription <subscription-uuid> --sdk-auth | base64 -w0" >>

################################################################################
# Specify if the cluster is Kubernetes RBAC enabled or not
rbac:
    enabled: false # true/false

# Specify aks cluster related information. THIS IS BEING DEPRECATED.
aksClusterConfiguration:
    apiServerAddress: <aks-api-server-address>
```