---
title: Używanie Azure Policy do zabezpieczania klastra
description: Użyj Azure Policy, aby zabezpieczyć klaster Azure Kubernetes Service (AKS).
ms.service: container-service
ms.topic: how-to
ms.date: 02/17/2021
ms.custom: template-how-to
ms.openlocfilehash: 6462c2987155925b7df5241d8fb6aa13c1e37b89
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107777729"
---
# <a name="secure-your-cluster-with-azure-policy"></a>Zabezpieczanie klastra przy użyciu Azure Policy

Aby zwiększyć bezpieczeństwo klastra usługi Azure Kubernetes Service (AKS), można stosować i wymuszać wbudowane zasady zabezpieczeń w klastrze przy użyciu Azure Policy. [Azure Policy][azure-policy] wymuszać standardy organizacji i oceniać zgodność na dużą skalę. Po zainstalowaniu Azure Policy dla usługi [AKS][kubernetes-policy-reference]można zastosować do klastra poszczególne definicje zasad lub grupy definicji zasad nazywane inicjatywami (czasami nazywanymi zestawami zasad). Zobacz [Azure Policy definicji wbudowanych dla usług AKS,][aks-policies] aby uzyskać pełną listę definicji zasad i inicjatyw AKS.

W tym artykule pokazano, jak zastosować definicje zasad do klastra i sprawdzić, czy te przypisania są wymuszane.

## <a name="prerequisites"></a>Wymagania wstępne

- Istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub [interfejsu Azure Portal][aks-quickstart-portal].
- Dodatek Azure Policy dla usługi AKS zainstalowany w klastrze usługi AKS. Wykonaj następujące [kroki, aby zainstalować Azure Policy dodatek][azure-policy-addon].

## <a name="assign-a-built-in-policy-definition-or-initiative"></a>Przypisywanie wbudowanej definicji zasad lub inicjatywy

Aby zastosować definicję zasad lub inicjatywę, użyj Azure Portal.

1. Przejdź do usługi Azure Policy w Azure Portal.
1. W okienku po lewej stronie Azure Policy wybierz pozycję **Definicje**.
1. W **obszarze Kategorie wybierz** pozycję `Kubernetes` .
1. Wybierz definicję zasad lub inicjatywę, którą chcesz zastosować. W tym przykładzie wybierz `Kubernetes cluster pod security baseline standards for Linux-based workloads` inicjatywę.
1. Wybierz opcję **Przypisz**.
1. Ustaw zakres **na** grupę zasobów klastra usługi AKS z włączonym Azure Policy AKS.
1. Wybierz stronę **Parametry** i zaktualizuj pozycję **Efekt** z na , aby zablokować nowe `audit` wdrożenia `deny` naruszające inicjatywę linii bazowej. Możesz również dodać dodatkowe przestrzenie nazw do wykluczenia z oceny. W tym przykładzie zachowaj wartości domyślne.
1. Wybierz **pozycję Przejrzyj i utwórz,** a następnie pozycję **Utwórz,** aby przesłać przypisanie zasad.

## <a name="validate-a-azure-policy-is-running"></a>Weryfikowanie uruchomionego Azure Policy

Upewnij się, że przypisania zasad są stosowane do klastra, uruchamiając następujące elementy:

```azurecli-interactive
kubectl get constrainttemplates
```

> [!NOTE]
> Synchronizacja przypisań zasad z każdym klastrem może potrwać do [20][azure-policy-assign-policy] minut.

Dane wyjściowe powinny być podobne do:

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

### <a name="validate-rejection-of-a-privileged-pod"></a>Weryfikowanie odrzucenia uprzywilejowanego zasobnika

Najpierw przetestujmy, co się stanie, gdy zaplanujemy zasobnik z kontekstem zabezpieczeń `privileged: true` . Ten kontekst zabezpieczeń eskaluje uprawnienia zasobnika. Inicjatywa nie zezwala na uprzywilejowane zasobniki, więc żądanie zostanie odrzucone, co spowoduje odrzucenie wdrożenia.

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

Utwórz zasobnik za pomocą [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f nginx-privileged.yaml
```

Zgodnie z oczekiwaniami nie można zaplanować zasobnika, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl apply -f privileged.yaml

Error from server ([denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}): error when creating "privileged.yaml": admission webhook "validation.gatekeeper.sh" denied the request: [denied by azurepolicy-container-no-privilege-00edd87bf80f443fa51d10910255adbc4013d590bec3d290b4f48725d4dfbdf9] Privileged container is not allowed: nginx-privileged, securityContext: {"privileged": true}
```

Zasobnik nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przejściem.

### <a name="test-creation-of-an-unprivileged-pod"></a>Testowanie tworzenia nieuprawniania zasobnika

W poprzednim przykładzie obraz kontenera automatycznie próbował użyć katalogu głównego do powiązania NGINX z portem 80. To żądanie zostało odrzucone przez inicjatywę zasad, więc nie można uruchomić zasobnika. Spróbujmy teraz uruchomić ten sam zasobnik NGINX bez uprzywilejowanego dostępu.

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

Utwórz zasobnik przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f nginx-unprivileged.yaml
```

Zasobnik został pomyślnie zaplanowany. Gdy sprawdzasz stan zasobnika za pomocą polecenia [kubectl get pods,][kubectl-get] zasobnik ma stan *Running (Uruchomiona):*

```console
$ kubectl get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          18s
```

W tym przykładzie przedstawiono inicjatywę odniesienia, która ma wpływ tylko na wdrożenia naruszające zasady w kolekcji. Dozwolone wdrożenia nadal działają.

Usuń nieuprawnniany zasobnik NGINX za pomocą polecenia [kubectl delete][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl delete -f nginx-unprivileged.yaml
```

## <a name="disable-a-policy-or-initiative"></a>Wyłączanie zasad lub inicjatywy

Aby usunąć inicjatywę odniesienia:

1. Przejdź do okienka Zasady na Azure Portal.
1. Wybierz **pozycję Przypisania** w okienku po lewej stronie.
1. Kliknij przycisk **...** obok `Kubernetes cluster pod security baseline standards for Linux-based workloads` inicjatywy.
1. Wybierz **pozycję Usuń przypisanie.**

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat Azure Policy działa:

- [Przegląd zasad platformy Azure][azure-policy]
- [Azure Policy i policys for AKS][aks-policies]
- Usuń [Azure Policy dodaj.][azure-policy-addon-remove]

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
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[kubernetes-policy-reference]: ../governance/policy/concepts/policy-for-kubernetes.md
