---
title: Azure RBAC dla klastrów Kubernetes z obsługą usługi Azure Arc
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Korzystanie z usługi Azure RBAC do sprawdzania autoryzacji w klastrach Kubernetes z obsługą usługi Azure Arc
ms.openlocfilehash: 0ee5f86ce12a39d86754d2e6e88263d8a03a012b
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304209"
---
# <a name="integrate-azure-active-directory-with-azure-arc-enabled-kubernetes-clusters"></a>Integrowanie Azure Active Directory z obsługą klastrów Kubernetes z użyciem usługi Azure Arc

Typy obiektów Kubernetes [ClusterRoleBinding i rolebinding](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#rolebinding-and-clusterrolebinding) ułatwiają Definiowanie autoryzacji w Kubernetes natywnie. Korzystając z tej funkcji, można użyć przypisań Azure Active Directory i ról na platformie Azure do kontrolowania kontroli autoryzacji w klastrze. Oznacza to, że można teraz użyć przypisań ról platformy Azure, aby szczegółowo kontrolować, kto może odczytywać, zapisywać i usuwać obiekty Kubernetes, takie jak wdrażanie, pod i usługa

Przegląd pojęć dotyczących tej funkcji jest dostępny w artykule [RBAC platformy Azure — Kubernetes](conceptual-azure-rbac.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Wymagania wstępne

- [Instalowanie lub uaktualnianie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) do wersji >= 2.16.0

- Zainstaluj `connectedk8s` rozszerzenie interfejsu wiersza polecenia platformy Azure w wersji >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
    
    Jeśli `connectedk8s` rozszerzenie jest już zainstalowane, można je zaktualizować do najnowszej wersji za pomocą następującego polecenia: 

    ```azurecli
    az extension update --name connectedk8s
    ```

- Istniejący klaster z włączonym usługą Azure Arc Kubernetes.
    - Jeśli klaster nie został jeszcze połączony, Skorzystaj z naszego [przewodnika Szybki Start](quickstart-connect-cluster.md).
    - [Uaktualnij agentów](agent-upgrade.md#manually-upgrade-agents) do wersji >= 1.1.0.

> [!NOTE]
> Tej funkcji nie można skonfigurować do zarządzanych ofert Kubernetes dostawców chmury, takich jak usługa Elastic Kubernetes lub Aparat Google Kubernetes, gdzie użytkownik nie ma dostępu do `apiserver` klastra. W przypadku klastrów usługi Azure Kubernetes Service (AKS) ta [Funkcja jest dostępna natywnie](../../aks/manage-azure-rbac.md) i nie wymaga połączenia klastra AKS z usługą Azure Arc.

## <a name="set-up-azure-ad-applications"></a>Konfigurowanie aplikacji usługi Azure AD

### <a name="create-server-application"></a>Tworzenie aplikacji serwera

1. Utwórz nową aplikację usługi Azure AD i Pobierz jej `appId` wartość, która jest używana w kolejnych krokach `serverApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Server" --identifier-uris "https://<clusterName>Server" --query appId -o tsv
    ```

1. Aktualizowanie oświadczeń członkostwa w grupie aplikacji:

    ```azurecli
    az ad app update --id <serverApplicationId> --set groupMembershipClaims=All
    ```

1. Utwórz nazwę główną usługi i Pobierz jej `password` wartość pola, która jest wymagana później w `serverApplicationSecret` przypadku włączania tej funkcji w klastrze:

    ```azurecli
    az ad sp create --id <serverApplicationId>
    az ad sp credential reset --name <serverApplicationId> --credential-description "ArcSecret" --query password -o tsv
    ```

1. Przyznaj uprawnienia interfejsu API aplikacji:

    ```azurecli
    az ad app permission add --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000 --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope
    az ad app permission grant --id <serverApplicationId> --api 00000003-0000-0000-c000-000000000000
    ```

    > [!NOTE]
    > * Ten krok musi być wykonywany przez administratora dzierżawy platformy Azure.
    > * Aby można było użyć tej funkcji w środowisku produkcyjnym, zaleca się utworzenie innej aplikacji serwerowej dla każdego klastra.

### <a name="create-client-application"></a>Tworzenie aplikacji klienckiej

1. Utwórz nową aplikację usługi Azure AD i Pobierz jej wartość "appId", która jest używana w kolejnych krokach `clientApplicationId` :

    ```azurecli
    az ad app create --display-name "<clusterName>Client" --native-app --reply-urls "https://<clusterName>Client" --query appId -o tsv
    ```

2. Utwórz nazwę główną usługi dla tej aplikacji klienckiej:

    ```azurecli
    az ad sp create --id <clientApplicationId>
    ```

3. Pobierz `oAuthPermissionId` dla aplikacji serwerowej:

    ```azurecli
    az ad app show --id <serverApplicationId> --query "oauth2Permissions[0].id" -o tsv
    ```

4. Przyznaj uprawnienia wymagane dla aplikacji klienckiej:

    ```azurecli
    az ad app permission add --id <clientApplicationId> --api <serverApplicationId> --api-permissions <oAuthPermissionId>=Scope
    az ad app permission grant --id <clientApplicationId> --api <serverApplicationId>
    ```

## <a name="create-a-role-assignment-for-the-server-application"></a>Utwórz przypisanie roli dla aplikacji serwera

Aplikacja serwera musi mieć `Microsoft.Authorization/*/read` uprawnienia do sprawdzenia, czy użytkownik żądający żądania jest autoryzowany na obiektach Kubernetes, które są częścią żądania.

1. Utwórz plik o nazwie accessCheck.jsz następującą zawartością:

    ```json
    {
      "Name": "Read authorization",
      "IsCustom": true,
      "Description": "Read authorization",
      "Actions": ["Microsoft.Authorization/*/read"],
      "NotActions": [],
      "DataActions": [],
      "NotDataActions": [],
      "AssignableScopes": [
        "/subscriptions/<subscription-id>"
      ]
    }
    ```

    Zastąp wartość `<subscription-id>` identyfikatorem rzeczywistej subskrypcji.

2. Wykonaj następujące polecenie, aby utworzyć nową rolę niestandardową:

    ```azurecli
    az role definition create --role-definition ./accessCheck.json
    ```

3. W danych wyjściowych powyższego polecenia przechowuj wartość `id` pola, która jest używana w kolejnych krokach jako `roleId` .

4. Utwórz przypisanie roli w aplikacji serwera jako osoba przydzielona przy użyciu utworzonej powyżej roli:

    ```azurecli
    az role assignment create --role <roleId> --assignee <serverApplicationId> --scope /subscriptions/<subscription-id>
    ```

## <a name="enable-azure-rbac-on-cluster"></a>Włącz funkcję RBAC platformy Azure w klastrze

1. Włącz funkcję RBAC platformy Azure w klastrze Kubernetes z włączoną funkcją ARC, uruchamiając następujące polecenie:

    ```console
    az connectedk8s enable-features -n <clusterName> -g <resourceGroupName> --features azure-rbac --app-id <serverApplicationId> --app-secret <serverApplicationSecret>
    ```
    
    > [!NOTE]
    > 1. Przed uruchomieniem powyższego polecenia upewnij się, że `kubeconfig` plik na komputerze wskazuje na klaster, na którym ma zostać włączona funkcja RBAC platformy Azure.
    > 2. Użyj `--skip-azure-rbac-list` polecenia z powyższym poleceniem, aby uzyskać listę nazw użytkowników/wiadomości e-mail/OID z rozdzieloną przecinkami przy użyciu Kubernetes natywnych obiektów ClusterRoleBinding i rolebinding zamiast funkcji kontroli dostępu platformy Azure.

### <a name="for-a-generic-cluster-where-there-is-no-reconciler-running-on-apiserver-specification"></a>W przypadku klastra ogólnego w specyfikacji apiserver nie jest uruchomiona żadna uzgadnianie:

1. Użyj protokołu SSH w każdym głównym węźle klastra i wykonaj następujące czynności:

    1. Otwórz `apiserver` manifest w trybie edycji:
        
        ```console
        sudo vi /etc/kubernetes/manifests/kube-apiserver.yaml
        ```

    1. Dodaj następującą specyfikację w obszarze `volumes` :
        
        ```yml
        - name: azure-rbac
          secret:
            secretName: azure-arc-guard-manifests
        ```

    1. Dodaj następującą specyfikację w obszarze `volumeMounts` :

        ```yml
        - mountPath: /etc/guard
          name: azure-rbac
          readOnly: true
        ```

    1. Dodaj następujące `apiserver` argumenty:

        ```yml
        - --authentication-token-webhook-config-file=/etc/guard/guard-authn-webhook.yaml
        - --authentication-token-webhook-cache-ttl=5m0s
        - --authorization-webhook-cache-authorized-ttl=5m0s
        - --authorization-webhook-config-file=/etc/guard/guard-authz-webhook.yaml
        - --authorization-webhook-version=v1
        - --authorization-mode=Node,Webhook,RBAC
        ```
    
        Jeśli klaster Kubernetes jest w wersji >= 1.19.0, należy `apiserver argument` również ustawić następujące elementy:

        ```yml
        - --authentication-token-webhook-version=v1
        ```

    1. Zapisz i wyjdź z edytora, aby zaktualizować `apiserver` pod.


### <a name="for-a-cluster-created-using-cluster-api"></a>W przypadku klastra utworzonego przy użyciu interfejsu API klastra

1. Skopiuj wpis tajny Guard zawierający pliki konfiguracyjne elementu webhook uwierzytelniania i autoryzacji `from the workload cluster` na komputerze:

    ```console
    kubectl get secret azure-arc-guard-manifests -n kube-system -o yaml > azure-arc-guard-manifests.yaml
    ```

1. Zmień `namespace` pole w `azure-arc-guard-manifests.yaml` pliku na przestrzeń nazw w klastrze zarządzania, w której stosowane są zasoby niestandardowe do tworzenia klastrów obciążeń.

1. Zastosuj ten manifest:

    ```console
    kubectl apply -f azure-arc-guard-manifests.yaml
    ```

1. Edytuj `KubeadmControlPlane` obiekt, wykonując `kubectl edit kcp <clustername>-control-plane` :
    
    1. Dodaj następujący fragment kodu w obszarze `files:` :
    
        ```console
        - contentFrom:
            secret:
              key: guard-authn-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authn-webhook.yaml
          permissions: "0644"
        - contentFrom:
            secret:
              key: guard-authz-webhook.yaml
              name: azure-arc-guard-manifests
          owner: root:root
          path: /etc/kubernetes/guard-authz-webhook.yaml
          permissions: "0644"
        ```

    1. Dodaj następujący fragment kodu w obszarze `apiServer:`  ->  `extraVolumes:` :
    
        ```console
        - hostPath: /etc/kubernetes/guard-authn-webhook.yaml
            mountPath: /etc/guard/guard-authn-webhook.yaml
            name: guard-authn
            readOnly: true
        - hostPath: /etc/kubernetes/guard-authz-webhook.yaml
            mountPath: /etc/guard/guard-authz-webhook.yaml
            name: guard-authz
            readOnly: true
        ```

    1. Dodaj następujący fragment kodu w obszarze `apiServer:`  ->  `extraArgs:` :
    
        ```console
        authentication-token-webhook-cache-ttl: 5m0s
        authentication-token-webhook-config-file: /etc/guard/guard-authn-webhook.yaml
        authentication-token-webhook-version: v1
        authorization-mode: Node,Webhook,RBAC
        authorization-webhook-cache-authorized-ttl: 5m0s
        authorization-webhook-config-file: /etc/guard/guard-authz-webhook.yaml
        authorization-webhook-version: v1
        ```

    1. Zapisz i wyjdź, aby zaktualizować `KubeadmControlPlane` obiekt. Zaczekaj na zrealizowanie tych zmian w klastrze obciążeń.


## <a name="create-role-assignments-for-users-to-access-the-cluster"></a>Tworzenie przypisań ról dla użytkowników w celu uzyskania dostępu do klastra

Właściciele zasobu Kubernetes z obsługą usługi Azure Arc mogą używać wbudowanych ról lub ról niestandardowych w celu udzielania innym użytkownikom dostępu do klastra Kubernetes.

### <a name="built-in-roles"></a>Wbudowane role

| Rola | Opis |
|---|---|
| [Usługa Azure Arc Kubernetes Viewer](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-viewer) | Zezwala na dostęp tylko do odczytu do wyświetlania większości obiektów w przestrzeni nazw. Ta rola nie zezwala na wyświetlanie wpisów tajnych. Wynika to z faktu `read` , że uprawnienia do wpisów tajnych spowodują włączenie dostępu do `ServiceAccount` poświadczeń w przestrzeni nazw, co z kolei umożliwia dostęp do interfejsu API za pomocą tego `ServiceAccount` (formy eskalacji uprawnień). |
| [Składnik zapisywania usługi Azure Arc Kubernetes](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-writer) | Zezwala na dostęp do odczytu/zapisu do większości obiektów w przestrzeni nazw. Ta rola nie zezwala na przeglądanie ani modyfikowanie ról ani powiązań ról. Jednak ta rola umożliwia dostęp do wpisów tajnych i uruchamianie zasobników jako dowolnych `ServiceAccount` w przestrzeni nazw, dzięki czemu może służyć do uzyskania poziomów dostępu interfejsu API dowolnego `ServiceAccount` w przestrzeni nazw. |
| [Administrator usługi Azure Arc Kubernetes](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-admin) | Zezwala na dostęp administratora. Przeznaczone do przyznania w przestrzeni nazw za pomocą elementu Rolebinding. Jeśli jest używany w Rolibinding, zezwala na dostęp do odczytu/zapisu do większości zasobów w przestrzeni nazw, w tym możliwość tworzenia ról i powiązań ról w przestrzeni nazw. Ta rola nie zezwala na dostęp do zapisu do przydziału zasobów ani do samego obszaru nazw. |
| [Administrator klastra usługi Azure Arc Kubernetes](../../role-based-access-control/built-in-roles.md#azure-arc-kubernetes-cluster-admin) | Zezwala administratorowi na wykonywanie dowolnej akcji dla dowolnego zasobu. W przypadku użycia w ClusterRoleBinding zapewnia pełną kontrolę nad każdym zasobem w klastrze i we wszystkich przestrzeniach nazw. W przypadku użycia w Rolibinding zapewnia pełną kontrolę nad każdym zasobem w przestrzeni nazw powiązania roli, w tym przestrzeni nazw.|

Można utworzyć przypisania roli do zakresu Kubernetes klastra z włączonym łukiem w `Access Control (IAM)` bloku zasobu klastra na Azure Portal. Możesz również użyć poleceń interfejsu wiersza polecenia platformy Azure, jak pokazano poniżej:

```azurecli
az role assignment create --role "Azure Arc Kubernetes Cluster Admin" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID
```

gdzie `AZURE-AD-ENTITY-ID` może to być nazwa użytkownika (na przykład testuser@mytenant.onmicrosoft.com ), a nawet wartość `appId` główna usługi.

Oto inny przykład tworzenia przypisania roli do określonego obszaru nazw w klastrze —

```azurecli
az role assignment create --role "Azure Arc Kubernetes Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
```

> [!NOTE]
> Chociaż przypisania ról należące do zakresu klastra można utworzyć przy użyciu Azure Portal lub interfejsu wiersza polecenia, przydziały ról należące do przestrzeni nazw mogą być tworzone tylko przy użyciu interfejsu wiersza polecenia.

### <a name="custom-roles"></a>Role niestandardowe

Można utworzyć własną definicję roli do użycia w przypisaniach ról.

Zapoznaj się z poniższym przykładem definicji roli, która umożliwia użytkownikowi tylko odczytywanie wdrożeń. Aby uzyskać więcej informacji, zobacz [pełną listę akcji danych, których można użyć do konstruowania definicji roli](../../role-based-access-control/resource-provider-operations.md#microsoftkubernetes).

Skopiuj poniższy obiekt JSON do pliku o nazwie custom-role.json. Zastąp `<subscription-id>` symbol zastępczy identyfikatorem rzeczywistej subskrypcji. Poniższa rola niestandardowa korzysta z jednej z akcji danych i umożliwia wyświetlenie wszystkich wdrożeń z zakresu (klastra/przestrzeni nazw), w którym utworzono przypisanie roli.

```json
{
    "Name": "Arc Deployment Viewer",
    "Description": "Lets you view all deployments in cluster/namespace.",
    "Actions": [],
    "NotActions": [],
    "DataActions": [
        "Microsoft.Kubernetes/connectedClusters/apps/deployments/read"
    ],
    "NotDataActions": [],
    "assignableScopes": [
        "/subscriptions/<subscription-id>"
    ]
}
```

1. Utwórz definicję roli, uruchamiając poniższe polecenie z folderu, w którym zapisano `custom-role.json` :

    ```bash
    az role definition create --role-definition @custom-role.json
    ```

1. Utwórz przypisanie roli przy użyciu tej definicji roli niestandardowej:

    ```bash
    az role assignment create --role "Arc Deployment Viewer" --assignee <AZURE-AD-ENTITY-ID> --scope $ARM_ID/namespaces/<namespace-name>
    ```

## <a name="configure-kubectl-with-user-credentials"></a>Konfigurowanie polecenia kubectl przy użyciu poświadczeń użytkownika

Istnieją dwa sposoby uzyskania `kubeconfig` pliku wymaganego w celu uzyskania dostępu do klastra:
1. Użyj funkcji [Connect klastrów](cluster-connect.md) ( `az connectedk8s proxy` ) klastra Kubernetes z włączoną funkcją Azure Arc.
1. Administrator klastra udostępnia `kubeconfig` plik każdemu innemu użytkownikowi.

### <a name="if-you-are-accessing-cluster-using-cluster-connect-feature"></a>Jeśli uzyskujesz dostęp do klastra przy użyciu funkcji Połącz z klastrem

Wykonaj następujące polecenie, aby uruchomić proces proxy:

```console
az connectedk8s proxy -n <clusterName> -g <resourceGroupName>
```

Po uruchomieniu procesu proxy można otworzyć kolejną kartę w konsoli programu, aby [rozpocząć wysyłanie żądań do klastra](#sending-requests-to-cluster).

### <a name="if-cluster-admin-shared-the-kubeconfig-file-with-you"></a>Jeśli administrator klastra udostępnił `kubeconfig` Ci plik 

1. Wykonaj następujące polecenie, aby ustawić poświadczenia dla użytkownika:

    ```console
    kubectl config set-credentials <testuser>@<mytenant.onmicrosoft.com> \
    --auth-provider=azure \
    --auth-provider-arg=environment=AzurePublicCloud \
    --auth-provider-arg=client-id=<clientApplicationId> \
    --auth-provider-arg=tenant-id=<tenantId> \
    --auth-provider-arg=apiserver-id=<serverApplicationId>
    ```

1. Otwórz `kubeconfig` utworzony wcześniej plik. W obszarze `contexts` Sprawdź poprawność kontekstu skojarzonego z punktami klastra do poświadczeń użytkownika utworzonych w poprzednim kroku.

1. Dodaj ustawienie **trybu konfiguracji** w obszarze Konfiguracja użytkownika:
  
    ```console
    name: testuser@mytenant.onmicrosoft.com
    user:
        auth-provider:
        config:
            apiserver-id: $SERVER_APP_ID
            client-id: $CLIENT_APP_ID
            environment: AzurePublicCloud
            tenant-id: $TENANT_ID
            config-mode: "1"
        name: azure
    ```

## <a name="sending-requests-to-cluster"></a>Wysyłanie żądań do klastra

1. Uruchom dowolne `kubectl` polecenie. Na przykład:
  * `kubectl get nodes` 
  * `kubectl get pods`

1. Po wyświetleniu monitu o uwierzytelnienie w przeglądarce Skopiuj adres URL logowania do urządzenia `https://microsoft.com/devicelogin` i otwórz go w przeglądarce sieci Web.

1. Wprowadź kod wydrukowany w konsoli programu, skopiuj i wklej kod w terminalu w monicie wejściowym dotyczącym uwierzytelniania urządzenia.

1. Wprowadź nazwę użytkownika ( testuser@mytenant.onmicrosoft.com ) i skojarzone hasło.

1. Jeśli zobaczysz komunikat o błędzie podobny do tego, oznacza to, że nie masz uprawnień do uzyskania dostępu do żądanego zasobu:

    ```console
    Error from server (Forbidden): nodes is forbidden: User "testuser@mytenant.onmicrosoft.com" cannot list resource "nodes" in API group "" at the cluster scope: User doesn't have access to the resource in Azure. Update role assignment to allow access.
    ```

    Administrator musi utworzyć nowe przypisanie roli upoważniające tego użytkownika do uzyskiwania dostępu do zasobu.


## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Bezpieczne nawiązywanie połączenia z klastrem przy użyciu funkcji [Połącz z klastrem](cluster-connect.md)