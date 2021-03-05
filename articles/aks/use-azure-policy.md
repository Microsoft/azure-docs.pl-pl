---
title: Używanie Azure Policy do zabezpieczania klastra
description: Użyj Azure Policy, aby zabezpieczyć klaster usługi Azure Kubernetes Service (AKS).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 46e92e6842204cd323992a2561e71302bb9cc722
ms.sourcegitcommit: 24a12d4692c4a4c97f6e31a5fbda971695c4cd68
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102193443"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Zabezpieczanie klastra za pomocą Azure Policy

Aby zwiększyć bezpieczeństwo klastra usługi Azure Kubernetes Service (AKS), możesz zastosować wbudowane zasady zabezpieczeń w klastrze przy użyciu Azure Policy. [Azure Policy][azure-policy] pomaga wymuszać standardy organizacyjne i oceniać zgodność na skalę. Po zainstalowaniu [dodatku Azure Policy dla AKS][kubernetes-policy-reference]można zastosować poszczególne definicje zasad lub grupy definicji zasad o nazwie inicjatywy (czasami nazywane policysets) do klastra. Zapoznaj się z [Azure Policy wbudowanymi definicjami AKS][aks-policies] , aby zapoznać się z pełną listą definicji zasad AKS.

W tym artykule pokazano, jak zastosować definicje zasad do klastra i upewnić się, że te przypisania są wymuszane.

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].
- Dodatek Azure Policy dla AKS zainstalowany w klastrze AKS. Wykonaj następujące [kroki, aby zainstalować dodatek Azure Policy][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Przypisywanie wbudowanej definicji zasad lub inicjatywy

Aby zastosować definicję lub inicjatywę zasad, użyj Azure Portal.

1. Przejdź do usługi Azure Policy w Azure Portal.
1. W lewym okienku strony Azure Policy wybierz pozycję **definicje**.
1. W obszarze **Kategorie** wybierz opcję `Kubernetes` .
1. Wybierz definicję lub inicjatywę zasad, którą chcesz zastosować. Na potrzeby tego przykładu wybierz `Kubernetes cluster pod security baseline standards for Linux-based workloads` inicjatywę.
1. Wybierz opcję **Przypisz**.
1. Ustaw **zakres** na grupę zasobów klastra AKS z włączonym dodatkiem Azure Policy.
1. Wybierz stronę **Parametry** i zaktualizuj **efekt** z `audit` programu do, `deny` Aby zablokować nowe wdrożenia naruszające inicjatywę bazową. Możesz również dodać dodatkowe przestrzenie nazw do wykluczenia z oceny. Na potrzeby tego przykładu Zachowaj wartości domyślne.
1. Wybierz kolejno pozycje **Przegląd + Utwórz i** **Utwórz, aby przesłać** przypisanie zasad.

## <a name="validate-a-azure-policy-is-running"></a>Sprawdź, czy Azure Policy jest uruchomiony

Upewnij się, że przypisania zasad są stosowane do klastra, uruchamiając następujące czynności:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Synchronizowanie zasad może potrwać [do 20 minut][azure-policy-assign-policy] .

Dane wyjściowe powinny wyglądać podobnie do:

```console
$ kubectl get constrainttemplate
NAME                                     AGE
k8sazureallowedcapabilities              23m
k8sazureallowedusersgroups               23m
k8sazureblockhostnamespace               23m
k8sazurecontainerallowedimages           23m
k8sazurecontainerallowedports            23m
k8sazurecontainerlimits                  23m
k8sazurecontainernoprivilege             23m
k8sazurecontainernoprivilegeescalation   23m
k8sazureenforceapparmor                  23m
k8sazurehostfilesystem                   23m
k8sazurehostnetworkingports              23m
k8sazurereadonlyrootfilesystem           23m
k8sazureserviceallowedports              23m
```

### <a name="validate-rejection-of-a-privileged-pod"></a>Weryfikuj odrzucanie uprzywilejowanego elementu pod

Najpierw Przetestuj, co się dzieje, gdy planujesz go z kontekstem zabezpieczeń `privileged: true` . Ten kontekst zabezpieczeń przekazuje uprawnienia administratora. Inicjatywa nie zezwala na dostęp do uprzywilejowanych zasobników, więc żądanie zostanie odrzucone, co spowoduje odrzucenie wdrożenia.

Utwórz plik o nazwie `nginx-privileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
      securityContext:
        privileged: true
```

Utwórz polecenie pod with [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Zgodnie z oczekiwaniami nie można zaplanować, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przełączeniem.

### <a name="test-creation-of-an-unprivileged-pod"></a>Tworzenie testu dla nieuprzywilejowanego pod

W poprzednim przykładzie obraz kontenera automatycznie próbował użyć elementu głównego do powiązania NGINX z portem 80. To żądanie zostało odrzucone przez inicjatywę zasad, więc uruchomienie nie powiodło się. Spróbujmy teraz korzystać z tego samego NGINX pod bez uprzywilejowanego dostępu.

Utwórz plik o nazwie `nginx-unprivileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Pomyślnie zaplanowano zadanie pod. Po sprawdzeniu stanu usługi pod za pomocą [polecenia kubectl Get-zasobnikowego][kubectl-get] , pod jest *uruchomiony*:

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

W tym przykładzie przedstawiono podstawową inicjatywę wpływającą wyłącznie na wdrożenia, które naruszają zasady w kolekcji. Dozwolone wdrożenia nadal działają.

Usuń NGINX nieuprzywilejowany pod za pomocą polecenia [Delete polecenia kubectl][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Wyłącz zasady lub inicjatywę

Aby usunąć inicjatywę bazową:

1. Przejdź do okienka zasady na Azure Portal.
1. Wybierz pozycję **przypisania** w okienku po lewej stronie.
1. Kliknij przycisk **...** obok `Kubernetes cluster pod security baseline standards for Linux-based workloads` inicjatywy.
1. Wybierz pozycję **Usuń przypisanie**.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat działania Azure Policy:

- [Przegląd zasad platformy Azure][azure-policy]
- [Azure Policy inicjatywami i zasadami dla AKS][aks-policies]
- Usuń [dodatek Azure Policy][azure-policy-addon-remove].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs

<!-- LINKS - internal -->
[aks-policies]: policy-reference.md
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[azure-policy]: ../governance/policy/overview.md
[azure-policy-addon]: ../governance/policy/concepts/policy-for-kubernetes.md#install-azure-policy-add-on-for-aks
[azure-policy-addon-remove]: ../governance/policy/concepts/policy-for-kubernetes.md#remove-the-add-on-from-aks
[azure-policy-assign-policy]: ../governance/policy/concepts/policy-for-kubernetes.md#assign-a-built-in-policy-definition
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
