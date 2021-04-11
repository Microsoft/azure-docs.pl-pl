---
title: Łączenie z klastrami Kubernetes z obsługą usługi Azure ARC przy użyciu programu cluster Connect
services: azure-arc
ms.service: azure-arc
ms.date: 04/05/2021
ms.topic: article
author: shashankbarsin
ms.author: shasb
description: Używanie programu cluster Connect do bezpiecznego łączenia się z klastrami Kubernetes z obsługą usługi Azure Arc
ms.openlocfilehash: c6b6555c7d18c0aa0d2e7c94ad2c32353da19502
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/06/2021
ms.locfileid: "106451133"
---
# <a name="use-cluster-connect-to-connect-to-azure-arc-enabled-kubernetes-clusters"></a>Łączenie z klastrami Kubernetes z obsługą usługi Azure ARC przy użyciu programu cluster Connect

Za pomocą programu cluster Connect można bezpiecznie łączyć się z klastrami Kubernetes z obsługą usługi Azure Arc bez konieczności włączania żadnego portu wejściowego w zaporze. Dostęp do `apiserver` klastra z włączonym łukiem Kubernetes umożliwia wykonywanie następujących scenariuszy:
* Włącz interaktywny debugowanie i rozwiązywanie problemów.
* Zapewnianie dostępu klastra do usług platformy Azure dla [lokalizacji niestandardowych](custom-locations.md) i innych zasobów utworzonych na jej podstawie.

Przegląd koncepcyjny tej funkcji jest dostępny w artykule [łączenie z klastrem — Kubernetes](conceptual-cluster-connect.md) .

[!INCLUDE [preview features note](./includes/preview/preview-callout.md)]

## <a name="prerequisites"></a>Wymagania wstępne   

- [Instalowanie lub uaktualnianie interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/install-azure-cli) do wersji >= 2.16.0

- Zainstaluj `connectedk8s` rozszerzenie interfejsu wiersza polecenia platformy Azure w wersji >= 1.1.0:

    ```azurecli
    az extension add --name connectedk8s
    ```
  
    Jeśli rozszerzenie zostało już zainstalowane `connectedk8s` , zaktualizuj rozszerzenie do najnowszej wersji:
    
    ```azurecli
    az extension update --name connectedk8s
    ```

- Istniejący klaster z włączonym usługą Azure Arc Kubernetes.
    - Jeśli klaster nie został jeszcze połączony, Skorzystaj z naszego [przewodnika Szybki Start](quickstart-connect-cluster.md).
    - [Uaktualnij agentów](agent-upgrade.md#manually-upgrade-agents) do wersji >= 1.1.0.

- Włącz opcję Połącz klaster na dowolnym klastrze Kubernetes z obsługą usługi Azure ARC, uruchamiając następujące polecenie na komputerze, na którym `kubeconfig` jest wskazywany klaster dotyczący problemu:

    ```azurecli
    az connectedk8s enable-features --features cluster-connect -n <clusterName> -g <resourceGroupName>
    ```

- Włącz poniżej punkty końcowe dla dostępu wychodzącego oprócz tych wymienionych w obszarze [łączenie klastra Kubernetes z usługą Azure Arc](quickstart-connect-cluster.md#meet-network-requirements):

    | Punkt końcowy | Port |
    |----------------|-------|
    |`*.servicebus.windows.net` | 443 |
    |`*.guestnotificationservice.azure.com` | 443 |

## <a name="usage"></a>Użycie

Funkcja Połącz z klastrem obsługuje dwie opcje uwierzytelniania: 
* Azure Active Directory (Azure AD) 
* Token konta usługi

### <a name="option-1-azure-active-directory"></a>Opcja 1: Azure Active Directory

1. W `kubeconfig` pliku wskazującym na `apiserver` klaster Kubernetes Utwórz element ClusterRoleBinding lub rolebinding dla jednostki usługi Azure AD (nazwy głównej lub użytkownika), które wymagają dostępu:

    **Dla użytkownika:**
    
    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<testuser>@<mytenant.onmicrosoft.com>
    ```

    **Dla aplikacji usługi Azure AD:**

    1. Pobierz `objectId` skojarzenie z aplikacją usługi Azure AD:

        ```azurecli
        az ad sp show --id <id> --query objectId -o tsv
        ```

    1. Utwórz element ClusterRoleBinding lub Rolebinding dla jednostki usługi Azure AD (nazwy głównej lub użytkownika), która musi uzyskać dostęp do tego klastra:
       
        ```console
        kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --user=<objectId>
        ```

1. Po zalogowaniu się do interfejsu wiersza polecenia platformy Azure przy użyciu podmiotu zainteresowania usługi Azure AD Uzyskaj połączenie z klastrem `kubeconfig` potrzebne do komunikowania się z klastrem z dowolnego miejsca (od zapory otaczającej klaster):

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name>
    ```

1. Użyj `kubectl` do wysyłania żądań do klastra:

    ```console
    kubectl get pods
    ```
    
    Powinna zostać wyświetlona odpowiedź z klastra zawierającego listę wszystkich zasobników w `default` przestrzeni nazw.

### <a name="option-2-service-account-bearer-token"></a>Opcja 2: token okaziciela dla konta usługi

1. W `kubeconfig` pliku wskazującym na `apiserver` klaster Kubernetes Utwórz konto usługi w dowolnej przestrzeni nazw (następujące polecenie tworzy je w domyślnym obszarze nazw):

    ```console
    kubectl create serviceaccount admin-user
    ```

1. Utwórz ClusterRoleBinding lub Rolebinding, aby udzielić temu [kontu usługi odpowiednich uprawnień w klastrze](https://kubernetes.io/docs/reference/access-authn-authz/rbac/#kubectl-create-rolebinding):

    ```console
    kubectl create clusterrolebinding admin-user-binding --clusterrole cluster-admin --serviceaccount default:admin-user
    ```

1. Pobierz token konta usługi przy użyciu następujących poleceń

    ```console
    SECRET_NAME=$(kubectl get serviceaccount admin-user -o jsonpath='{$.secrets[0].name}')
    ```

    ```console
    TOKEN=$(kubectl get secret ${SECRET_NAME} -o jsonpath='{$.data.token}' | base64 -d | sed $'s/$/\\\n/g')
    ```

1. Aby nawiązać połączenie `kubeconfig` z klastrem z dowolnego miejsca (od zapory otaczającej klaster), należy pobrać z klastra

    ```azurecli
    az connectedk8s proxy -n <cluster-name> -g <resource-group-name> --token $TOKEN
    ```

1. Użyj `kubectl` do wysyłania żądań do klastra:

    ```console
    kubectl get pods
    ```

    Powinna zostać wyświetlona odpowiedź z klastra zawierającego listę wszystkich zasobników w `default` przestrzeni nazw.

## <a name="known-limitations"></a>Znane ograniczenia

W przypadku wykonywania żądań do klastra Kubernetes, jeśli używana jednostka usługi Azure AD jest częścią więcej niż 200 grup, zaobserwowano następujący błąd, ponieważ jest to znane ograniczenie:

```console
You must be logged in to the server (Error:Error while retrieving group info. Error:Overage claim (users with more than 200 group membership) is currently not supported. 
```

Aby obsłużyć ten błąd:
1. Utwórz nazwę [główną usługi](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli), która nie może być członkiem więcej niż 200 grup.
1. Przed uruchomieniem polecenia [Zaloguj](https://docs.microsoft.com/cli/azure/create-an-azure-service-principal-azure-cli#sign-in-using-a-service-principal) się do interfejsu wiersza polecenia platformy Azure przy użyciu nazwy głównej usługi `az connectedk8s proxy` .

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> Konfigurowanie [RBAC usługi Azure AD](azure-rbac.md) w klastrach