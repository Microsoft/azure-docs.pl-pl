---
title: Używanie zasad zabezpieczeń na platformie Azure Kubernetes Service (AKS)
description: Dowiedz się, jak kontrolować przyjmowanie w systemie za pomocą PodSecurityPolicy w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 07/21/2020
ms.openlocfilehash: bec9c7b4be5c3c3e334a8e3cb3a8b2e0a7130de3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89669307"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Wersja zapoznawcza — Zabezpieczanie klastra przy użyciu zasad zabezpieczeń na platformie Azure Kubernetes Service (AKS)

> [!WARNING]
> **Funkcja opisana w tym dokumencie, zgodnie z zasadami zabezpieczeń (wersja zapoznawcza), jest ustawiana jako przestarzała i nie będzie już dostępna po 1 lutego 2021** na korzyść [Azure Policy dla AKS](use-pod-security-on-azure-policy.md). Data wycofania została rozszerzona od daty wcześniejszej 15 października 2020.
>
> Gdy zasady zabezpieczeń (wersja zapoznawcza) są przestarzałe, należy wyłączyć tę funkcję w przypadku wszystkich istniejących klastrów przy użyciu przestarzałej funkcji w celu przeprowadzania przyszłych uaktualnień klastra i pozostawania w ramach pomocy technicznej systemu Azure.
>
> Zdecydowanie zaleca się rozpoczęcie testowania scenariuszy z Azure Policy dla AKS, które oferują wbudowane zasady zabezpieczające i wbudowane inicjatywy, które są mapowane na zasady zabezpieczeń na poziomie. Kliknij tutaj, aby dowiedzieć się więcej na temat [migracji do Azure Policy z poziomu zasad zabezpieczeń (wersja zapoznawcza)](use-pod-security-on-azure-policy.md#migrate-from-kubernetes-pod-security-policy-to-azure-policy).

Aby zwiększyć bezpieczeństwo klastra AKS, możesz ograniczyć, co można zaplanować. Nie można uruchomić z nich zasobników, które żądają niedozwolonych zasobów w klastrze AKS. Ten dostęp można zdefiniować przy użyciu zasad zabezpieczeń pod. W tym artykule pokazano, jak za pomocą zasad zabezpieczeń w programie ograniczyć wdrażanie zasobników w AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster AKS. Jeśli potrzebujesz klastra AKS, zapoznaj się z przewodnikiem Szybki Start AKS [przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart-cli] lub [przy użyciu Azure Portal][aks-quickstart-portal].

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby można było korzystać z zasad zabezpieczeń na poziomie systemu, wymagany jest interfejs wiersza polecenia *AKS-Preview* w wersji 0.4.1 lub nowszej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure w *wersji zapoznawczej* przy użyciu poleceń [AZ Extension Add][az-extension-add] , a następnie wyszukaj wszystkie dostępne aktualizacje za pomocą polecenia [AZ Extension Update][az-extension-update] :

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Zarejestruj dostawcę funkcji zasad zabezpieczeń

**Ten dokument i funkcja są ustawiane jako przestarzałe 15 października 2020.**

Aby utworzyć lub zaktualizować klaster AKS na potrzeby używania zasad zabezpieczeń, należy najpierw włączyć flagę funkcji w ramach subskrypcji. Aby zarejestrować flagę funkcji *PodSecurityPolicyPreview* , użyj polecenia [AZ Feature Register][az-feature-register] , jak pokazano w następującym przykładzie:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Wyświetlenie stanu *rejestracji*może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list][az-feature-list] :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, Odśwież rejestrację dostawcy zasobów *Microsoft. ContainerService* za pomocą polecenia [AZ Provider Register][az-provider-register] :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Przegląd zasad zabezpieczeń pod

W klastrze Kubernetes kontroler przyjmowania jest używany do przechwytywania żądań do serwera interfejsu API, gdy zasób zostanie utworzony. Kontroler dopuszczenia może następnie *sprawdzić poprawność* żądania zasobu względem zestawu *reguł lub zmodyfikować zasób w celu* zmiany parametrów wdrożenia.

*PodSecurityPolicy* to kontroler przyjmowania, który sprawdza, czy specyfikacja pod spełnia określone wymagania. Wymagania te mogą ograniczać użycie kontenerów uprzywilejowanych, dostęp do niektórych typów magazynów lub użytkownika lub grupę, w których kontener może być uruchamiany. Podczas próby wdrożenia zasobu, w którym specyfikacje pod nie spełniają wymagań opisanych w zasadach zabezpieczeń pod, żądanie jest odrzucane. Możliwość kontrolowania, jakie elementy można zaplanować w klastrze AKS, uniemożliwiają pewne potencjalne luki w zabezpieczeniach lub eskalacji uprawnień.

Po włączeniu zasad zabezpieczeń w klastrze AKS są stosowane pewne zasady domyślne. Te zasady domyślne zapewniają wbudowane środowisko do definiowania, które z nich można zaplanować. Jednak użytkownicy klastrów mogą uruchamiać problemy ze wdrożeniem platformy, dopóki nie zostaną zdefiniowane własne zasady. Zalecanym podejściem jest:

* Tworzenie klastra AKS
* Definiowanie własnych zasad zabezpieczeń pod
* Włącz funkcję zasad zabezpieczeń pod

Aby pokazać, jak zasady domyślne ograniczają wdrożenia, w tym artykule należy najpierw włączyć funkcję zasad zabezpieczeń na stronie, a następnie utworzyć zasadę niestandardową.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Włączanie zasad zabezpieczeń pod względem klastra AKS

Korzystając z polecenia [AZ AKS Update][az-aks-update] , można włączać lub wyłączać zasady zabezpieczeń na zasadzie. Poniższy przykład włącza zasady zabezpieczeń pod nazwą klastra *myAKSCluster* w grupie zasobów o nazwie Moja *resourceName*.

> [!NOTE]
> W przypadku użycia w świecie rzeczywistym nie należy włączać zasad zabezpieczeń na poziomie, dopóki nie zostaną zdefiniowane własne zasady niestandardowe. W tym artykule opisano zasady zabezpieczeń pod względem pierwszego kroku, aby zobaczyć, jak domyślne zasady ograniczają wdrożenia.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Domyślne zasady AKS

Po włączeniu zasad zabezpieczeń na AKS tworzy jedną domyślną zasadę o nazwie *Privileged*. Nie Edytuj ani nie usuwaj zasad domyślnych. Zamiast tego należy utworzyć własne zasady definiujące ustawienia, które mają być kontrolowane. Najpierw Spójrzmy na to, jakie zasady domyślne wpływają na wdrożenia.

Aby wyświetlić dostępne zasady, użyj polecenia [polecenia kubectl Get PSP][kubectl-get] , jak pokazano w poniższym przykładzie.

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Zasady zabezpieczeń *uprzywilejowany* pod są stosowane do każdego uwierzytelnionego użytkownika w klastrze AKS. To przypisanie jest kontrolowane przez ClusterRoles i ClusterRoleBindings. Użyj polecenia [polecenia kubectl Get rolebindings][kubectl-get] i Wyszukaj *wartość default: Privileged:* Binding w przestrzeni nazw *polecenia-system* :

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Jak pokazano w następujących wąskich danych wyjściowych, wartość *PSP: Privileged* ClusterRole jest przypisywana do dowolnego *systemu: Użytkownicy uwierzytelnieni* . Ta możliwość zapewnia podstawowy poziom uprawnień bez definiowania własnych zasad.

```
apiVersion: rbac.authorization.k8s.io/v1
kind: RoleBinding
metadata:
  [...]
  name: default:privileged
  [...]
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp:privileged
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:masters
```

Ważne jest, aby zrozumieć, jak te domyślne zasady współdziałają z żądaniami użytkowników w celu zaplanowania planów przed rozpoczęciem tworzenia własnych zasad zabezpieczeń. W następnych kilku sekcjach Przyjrzyjmy się zaplanowaniu kilku zasobników, aby zobaczyć te domyślne zasady w działaniu.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Tworzenie użytkownika testowego w klastrze AKS

Domyślnie w przypadku używania polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] poświadczenia *administratora* dla klastra AKS są dodawane do `kubectl` konfiguracji. Użytkownik administracyjny pomija wymuszanie zasad zabezpieczeń pod. Jeśli używasz integracji Azure Active Directory dla klastrów AKS, możesz zalogować się przy użyciu poświadczeń użytkownika niebędącego administratorem, aby zobaczyć wymuszanie zasad w akcji. W tym artykule utworzyszmy konto użytkownika testowego w klastrze AKS, którego możesz użyć.

Utwórz przykładową przestrzeń nazw o nazwie *PSP-AKS* dla zasobów testowych przy użyciu polecenia [Utwórz przestrzeń nazw polecenia kubectl][kubectl-create] . Następnie utwórz konto usługi o nazwie *nieadministracyjne — użytkownik* korzystający z polecenia [polecenia kubectl Create Account][kubectl-create] :

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Następnie utwórz Rolębinding dla *użytkownika niebędącego administratorem* w celu wykonywania podstawowych akcji w przestrzeni nazw za pomocą polecenia [polecenia kubectl Create rolebinding][kubectl-create] :

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Utwórz polecenia aliasu dla administratora i użytkownika niebędącego administratorem

Aby wyróżnić różnicę między zwykłym użytkownikiem administratora podczas korzystania z programu `kubectl` i użytkownik niebędący administratorem utworzony w poprzednich krokach, Utwórz dwa aliasy wiersza polecenia:

* Alias **polecenia kubectl-admin** jest przeznaczony dla regularnego użytkownika administratora i jest objęty zakresem przestrzeni nazw *PSP-AKS* .
* Alias **polecenia kubectl-nonadminuser** jest przeznaczony dla *użytkownika niebędącego administratorem* utworzonego w poprzednim kroku i jest objęty zakresem przestrzeni nazw *PSP-AKS* .

Utwórz te dwa aliasy, jak pokazano w następujących poleceniach:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testowanie tworzenia uprzywilejowanego pod

Najpierw Przetestuj, co się dzieje, gdy planujesz go z kontekstem zabezpieczeń `privileged: true` . Ten kontekst zabezpieczeń przekazuje uprawnienia administratora. W poprzedniej sekcji, która wykazała domyślne zasady zabezpieczeń AKS pod, zasady *uprawnień* powinny odmówić tego żądania.

Utwórz plik o nazwie `nginx-privileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: nginx:1.14.2
      securityContext:
        privileged: true
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Nie można zaplanować, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przełączeniem.

## <a name="test-creation-of-an-unprivileged-pod"></a>Tworzenie testu dla nieuprzywilejowanego pod

W poprzednim przykładzie Specyfikacja "pod" zażądała eskalacji uprzywilejowanej. To żądanie jest odrzucane przez domyślne *uprawnienia* w ramach zasad zabezpieczeń, dlatego nie można zaplanować uruchomienia. Spróbujmy teraz uruchomić ten sam NGINX pod bez żądania eskalacji uprawnień.

Utwórz plik o nazwie `nginx-unprivileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Nie można zaplanować, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przełączeniem.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Tworzenie testów pod kątem określonego kontekstu użytkownika

W poprzednim przykładzie obraz kontenera automatycznie próbował użyć elementu głównego do powiązania NGINX z portem 80. To żądanie zostało odrzucone przez domyślne *uprawnienia* do zasad zabezpieczeń, dlatego nie można uruchomić. Spróbujmy teraz uruchomić te same NGINX pod kątem określonego kontekstu użytkownika, takich jak `runAsUser: 2000` .

Utwórz plik o nazwie `nginx-unprivileged-nonroot.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: nginx:1.14.2
      securityContext:
        runAsUser: 2000
```

Utwórz pod za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Nie można zaplanować, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przełączeniem.

## <a name="create-a-custom-pod-security-policy"></a>Tworzenie niestandardowych zasad zabezpieczeń pod

Po zapoznaniu się z zachowaniem domyślnych zasad zabezpieczeń na poziomie, pozwól, aby użytkownik niebędący *administratorem* pomyślnie zaplanował plan.

Utwórzmy zasady, aby odrzucić system, który zażądał uprzywilejowanego dostępu. Inne opcje, takie jak *runAsUser* lub dozwolone *woluminy*, nie są wyraźnie ograniczone. Ten typ zasad odrzuca żądanie dostępu uprzywilejowanego, ale w przeciwnym razie umożliwia klastrowi uruchomienie żądanych zasobników.

Utwórz plik o nazwie `psp-deny-privileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: policy/v1beta1
kind: PodSecurityPolicy
metadata:
  name: psp-deny-privileged
spec:
  privileged: false
  seLinux:
    rule: RunAsAny
  supplementalGroups:
    rule: RunAsAny
  runAsUser:
    rule: RunAsAny
  fsGroup:
    rule: RunAsAny
  volumes:
  - '*'
```

Utwórz zasady za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Aby wyświetlić dostępne zasady, użyj polecenia [polecenia kubectl Get PSP][kubectl-get] , jak pokazano w poniższym przykładzie. Porównaj zasady *PSP-Deny-Privileged* z domyślnymi zasadami *uprawnień* , które zostały wymuszone w poprzednich przykładach, aby utworzyć element pod. Zasady odrzucają tylko użycie eskalacji *priv* . Nie ma żadnych ograniczeń dla użytkownika lub grupy dla zasad *"PSP-Deny-Privileged* ".

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Zezwalaj kontu użytkownika na używanie zasad zabezpieczeń niestandardowych pod

W poprzednim kroku zostały utworzone zasady zabezpieczeń pod, aby odrzucić program, który zażądał uprzywilejowanego dostępu. Aby zezwolić na użycie zasad, należy utworzyć *rolę* lub *ClusterRole*. Następnie należy skojarzyć jedną z tych ról przy użyciu *rolibinding* lub *ClusterRoleBinding*.

Na potrzeby tego przykładu Utwórz element ClusterRole, który umożliwia *użycie* zasad *"PSP-Deny-Privileged* " utworzonych w poprzednim kroku. Utwórz plik o nazwie `psp-deny-privileged-clusterrole.yaml` i wklej następujący manifest YAML:

```yaml
kind: ClusterRole
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: psp-deny-privileged-clusterrole
rules:
- apiGroups:
  - extensions
  resources:
  - podsecuritypolicies
  resourceNames:
  - psp-deny-privileged
  verbs:
  - use
```

Utwórz ClusterRole za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Teraz Utwórz ClusterRoleBinding, aby użyć ClusterRole utworzonych w poprzednim kroku. Utwórz plik o nazwie `psp-deny-privileged-clusterrolebinding.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: psp-deny-privileged-clusterrolebinding
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: psp-deny-privileged-clusterrole
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: Group
  name: system:serviceaccounts
```

Utwórz ClusterRoleBinding za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> W pierwszym kroku tego artykułu włączono funkcję zasad zabezpieczeń pod względem klastra AKS. Zalecaną metodą jest włączenie funkcji zasad zabezpieczeń pod warunkiem po zdefiniowaniu własnych zasad. Jest to etap, w którym można włączyć funkcję zasad zabezpieczeń pod. Zdefiniowano co najmniej jedną zasadę niestandardową, a konta użytkowników zostały skojarzone z tymi zasadami. Teraz można bezpiecznie włączyć funkcję zasad zabezpieczeń na podstawie i zminimalizować problemy spowodowane przez zasady domyślne.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Ponownie przetestuj tworzenie nieuprzywilejowanego konta

Po zastosowaniu zasad zabezpieczeń niestandardowych pod kątem stosowania zasad i powiązania dla konta użytkownika w celu korzystania z nich spróbuj ponownie utworzyć nieuprzywilejowany element. Użyj tego samego `nginx-privileged.yaml` manifestu, aby utworzyć pod przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] :

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Pomyślnie zaplanowano zadanie pod. Po sprawdzeniu stanu usługi pod za pomocą [polecenia kubectl Get-zasobnikowego][kubectl-get] , pod jest *uruchomiony*:

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

Ten przykład pokazuje, jak można utworzyć zasady zabezpieczeń niestandardowych pod kątem definiowania dostępu do klastra AKS dla różnych użytkowników lub grup. Domyślne zasady AKS zapewniają ścisłą kontrolę nad tym, co mogą być uruchamiane, i utworzyć własne zasady niestandardowe, a następnie prawidłowo definiować wymagane ograniczenia.

Usuń NGINX nieuprzywilejowany pod za pomocą polecenia [Delete polecenia kubectl][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyłączyć zasady zabezpieczeń pod, ponownie Użyj polecenia [AZ AKS Update][az-aks-update] . W poniższym przykładzie zostały wyłączone zasady zabezpieczeń pod nazwą klastra *myAKSCluster* w grupie zasobów o nazwie Moja *zasobów*:

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Następnie usuń ClusterRole i ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Usuń zasady zabezpieczeń przy użyciu polecenia [polecenia kubectl Delete][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Na koniec Usuń przestrzeń nazw *PSP-AKS* :

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak utworzyć zasady zabezpieczeń pod, aby uniemożliwić korzystanie z uprzywilejowanego dostępu. Istnieje wiele funkcji, które mogą wymuszać zasady, takie jak typ woluminu lub użytkownika RunAs. Aby uzyskać więcej informacji na temat dostępnych opcji, zobacz [dokumenty referencyjne dotyczące zasad zabezpieczeń Kubernetes][kubernetes-policy-reference].

Aby uzyskać więcej informacji na temat ograniczania ruchu sieciowego, zobacz [bezpieczny ruch między jednostkami z użyciem zasad sieciowych w AKS][network-policies].

<!-- LINKS - external -->
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-delete]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#delete
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-describe]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#describe
[kubectl-logs]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#logs
[terms-of-use]: https://azure.microsoft.com/support/legal/preview-supplemental-terms/
[kubernetes-policy-reference]: https://kubernetes.io/docs/concepts/policy/pod-security-policy/#policy-reference

<!-- LINKS - internal -->
[aks-quickstart-cli]: kubernetes-walkthrough.md
[aks-quickstart-portal]: kubernetes-walkthrough-portal.md
[install-azure-cli]: /cli/azure/install-azure-cli
[network-policies]: use-network-policies.md
[az-feature-register]: /cli/azure/feature#az-feature-register
[az-feature-list]: /cli/azure/feature#az-feature-list
[az-provider-register]: /cli/azure/provider#az-provider-register
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az-extension-add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az-extension-add
[az-extension-update]: /cli/azure/extension#az-extension-update
