---
title: Używanie zasad zabezpieczeń zasobników w Azure Kubernetes Service (AKS)
description: Dowiedz się, jak kontrolować najechy zasobników przy użyciu podsecuritypolicy w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/25/2021
ms.openlocfilehash: d70b8e8efbf96e50575845ac88993012fed936d5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767425"
---
# <a name="preview---secure-your-cluster-using-pod-security-policies-in-azure-kubernetes-service-aks"></a>Wersja zapoznawcza — zabezpieczanie klastra przy użyciu zasad zabezpieczeń zasobników Azure Kubernetes Service (AKS)

> [!WARNING]
> **Funkcja opisana w tym dokumencie, zasady zabezpieczeń [](https://kubernetes.io/blog/2021/04/06/podsecuritypolicy-deprecation-past-present-and-future/) zasobników (wersja zapoznawcza), rozpocznie wycofanie z kubernetes w wersji 1.21, po usunięciu w wersji 1.25.** Gdy rozwiązanie Kubernetes Upstream zbliża się do tego punktu kontrolnego, społeczność kubernetesów będzie pracować nad dokumentem alternatywnych rozwiązań. Poprzednie ogłoszenie dotyczące wyłączenia zostało ogłoszone w tym czasie, ponieważ nie było możliwej opcji dla klientów. Teraz, gdy społeczność kubernetes pracuje nad alternatywą, nie ma już konieczności wywłaszczania rozwiązania Kubernetes. 
>
> Po wycofaniu zasad zabezpieczeń zasobników (wersja zapoznawcza) należy wyłączyć tę funkcję we wszystkich istniejących klastrach przy użyciu przestarzałej funkcji w celu przeprowadzenia przyszłych uaktualnień klastra i pozostania w ramach pomocy technicznej platformy Azure.

Aby zwiększyć bezpieczeństwo klastra usługi AKS, można ograniczyć to, które zasobniki można zaplanować. Zasobników, które żądają zasobów, na które nie zezwalasz, nie można uruchomić w klastrze usługi AKS. Ten dostęp można zdefiniować przy użyciu zasad zabezpieczeń zasobników. W tym artykule pokazano, jak za pomocą zasad zabezpieczeń zasobników ograniczyć wdrażanie zasobników w u usługi AKS.

[!INCLUDE [preview features callout](./includes/preview/preview-callout.md)]

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS. Jeśli potrzebujesz klastra usługi AKS, zobacz przewodnik Szybki start usługi AKS przy użyciu interfejsu wiersza polecenia platformy [Azure][aks-quickstart-cli] lub Azure Portal [.][aks-quickstart-portal]

Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

### <a name="install-aks-preview-cli-extension"></a>Instalowanie rozszerzenia interfejsu wiersza polecenia aks-preview

Aby korzystać z zasad zabezpieczeń zasobników, potrzebne jest rozszerzenie interfejsu wiersza polecenia *aks-preview* w wersji 0.4.1 lub wyższej. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure *aks-preview* za pomocą [polecenia az extension add,][az-extension-add] a następnie sprawdź dostępne aktualizacje za pomocą [polecenia az extension update:][az-extension-update]

```azurecli-interactive
# Install the aks-preview extension
az extension add --name aks-preview

# Update the extension to make sure you have the latest version installed
az extension update --name aks-preview
```

### <a name="register-pod-security-policy-feature-provider"></a>Rejestrowanie dostawcy funkcji zasad zabezpieczeń zasobnika

**Ten dokument i funkcja zostały ustawione do końca 15 października 2020 r.**

Aby utworzyć lub zaktualizować klaster usługi AKS w celu używania zasad zabezpieczeń zasobników, najpierw włącz flagę funkcji w subskrypcji. Aby zarejestrować *flagę funkcji PodSecurityPolicyPreview,* użyj [polecenia az feature register,][az-feature-register] jak pokazano w poniższym przykładzie:

```azurecli-interactive
az feature register --name PodSecurityPolicyPreview --namespace Microsoft.ContainerService
```

Wyświetlanie stanu Zarejestrowane trwa kilka *minut.* Stan rejestracji można sprawdzić za pomocą [polecenia az feature list:][az-feature-list]

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/PodSecurityPolicyPreview')].{Name:name,State:properties.state}"
```

Gdy wszystko będzie gotowe, odśwież rejestrację dostawcy *zasobów Microsoft.ContainerService* za pomocą [polecenia az provider register:][az-provider-register]

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="overview-of-pod-security-policies"></a>Omówienie zasad zabezpieczeń zasobników

W klastrze Kubernetes kontroler danych służy do przechwytywania żądań do serwera interfejsu API podczas tworzenia zasobu. Kontroler danych może następnie *zweryfikować* żądanie zasobu względem zestawu reguł lub *zmień* zasób, aby zmienić parametry wdrożenia.

*PodSecurityPolicy* to kontroler danych, który sprawdza, czy specyfikacja zasobnika spełnia zdefiniowane wymagania. Te wymagania mogą ograniczać użycie kontenerów uprzywilejowanych, dostęp do niektórych typów magazynu lub użytkownika lub grupę, jako których może działać kontener. Podczas próby wdrożenia zasobu, w którym specyfikacje zasobników nie spełniają wymagań opisanych w zasadach zabezpieczeń zasobnika, żądanie jest odrzucone. Możliwość kontrolowania zasobników, które można zaplanować w klastrze usługi AKS, zapobiega niektórym możliwym lukom w zabezpieczeniach lub podwyższeniu poziomu uprawnień.

Po włączeniu zasad zabezpieczeń zasobników w klastrze usługi AKS są stosowane niektóre zasady domyślne. Te zasady domyślne zapewniają środowisko, które umożliwia definiowanie zasobników, które można zaplanować. Jednak użytkownicy klastra mogą mieć problemy z wdrażaniem zasobników do momentu zdefiniowania własnych zasad. Zalecanym podejściem jest:

* Tworzenie klastra AKS
* Definiowanie własnych zasad zabezpieczeń zasobników
* Włączanie funkcji zasad zabezpieczeń zasobnika

Aby pokazać, jak zasady domyślne ograniczają wdrożenia zasobników, w tym artykule najpierw włączymy funkcję zasad zabezpieczeń zasobnika, a następnie utworzymy zasady niestandardowe.

### <a name="behavior-changes-between-pod-security-policy-and-azure-policy"></a>Zmiany zachowania między zasadami zabezpieczeń zasobnika a Azure Policy

Poniżej znajduje się podsumowanie zmian zachowania między zasadami zabezpieczeń zasobników a Azure Policy.

|Scenariusz| Zasady zabezpieczeń zasobników | Azure Policy |
|---|---|---|
|Instalacja|Włączanie funkcji zasad zabezpieczeń zasobników |Włączanie Azure Policy dodatku
|Wdrażanie zasad| Wdrażanie zasobu zasad zabezpieczeń zasobnika| Przypisz zasady platformy Azure do zakresu subskrypcji lub grupy zasobów. Dodatek Azure Policy jest wymagany w przypadku aplikacji zasobów kubernetes.
| Zasady domyślne | Gdy zasady zabezpieczeń zasobników są włączone w u usługi AKS, są stosowane domyślne zasady Uprzywilejowane i Nieograniczone. | Nie są stosowane żadne domyślne zasady, włączając Azure Policy dodatku. Należy jawnie włączyć zasady w Azure Policy.
| Kto może tworzyć i przypisywać zasady | Administrator klastra tworzy zasób zasad zabezpieczeń zasobnika | Użytkownicy muszą mieć co najmniej uprawnienia "właściciel" lub "Współautor zasad zasobów" w grupie zasobów klastra AKS. — Za pomocą interfejsu API użytkownicy mogą przypisywać zasady w zakresie zasobów klastra usługi AKS. Użytkownik powinien mieć co najmniej uprawnienia "właściciel" lub "Współautor zasad zasobów" w zasobie klastra usługi AKS. — W Azure Portal można przypisać zasady na poziomie grupy zarządzania/subskrypcji/grupy zasobów.
| Autoryzowanie zasad| Użytkownicy i konta usług wymagają jawnych uprawnień do korzystania z zasad zabezpieczeń zasobników. | Do autoryzowania zasad nie jest wymagane żadne dodatkowe przypisanie. Po przypisaniu zasad na platformie Azure wszyscy użytkownicy klastra mogą używać tych zasad.
| Zastosowanie zasad | Administrator pomija wymuszanie zasad zabezpieczeń zasobników. | Wszyscy użytkownicy (administratorzy & bez uprawnień administratora) zobaczą te same zasady. Nie ma specjalnej wielkości obudowy opartej na użytkownikach. Aplikację zasad można wykluczyć na poziomie przestrzeni nazw.
| Zakres zasad | Zasady zabezpieczeń zasobników nie są przestrzeniami nazw | Szablony ograniczeń używane przez Azure Policy nie są przestrzeniami nazw.
| Akcja Odmów/Inspekcja/Akcja akcji | Zasady zabezpieczeń zasobników obsługują tylko akcje odmowy. Można to zrobić przy użyciu wartości domyślnych w żądaniach tworzenia. Walidację można wykonać podczas żądań aktualizacji.| Azure Policy obsługuje zarówno akcje odmowy & inspekcji. Jeszcze nie jest obsługiwane, ale planowane.
| Zgodność zasad zabezpieczeń zasobników | Nie ma wglądu w zgodność zasobników, które istniały przed włączeniem zasad zabezpieczeń zasobników. Niezgodne zasobniki utworzone po włączeniu zasad zabezpieczeń zasobników są odmowa. | Niezgodne zasobniki, które istniały przed zastosowaniem zasad platformy Azure, będą wyświetlane w przypadku naruszeń zasad. Niezgodne zasobniki utworzone po włączeniu zasad platformy Azure są odrzucane, jeśli zasady są ustawione z efektem odmowy.
| Jak wyświetlić zasady w klastrze | `kubectl get psp` | `kubectl get constrainttemplate` — Zwracane są wszystkie zasady.
| Standard zasad zabezpieczeń zasobnika — uprzywilejowany | Zasób zasad zabezpieczeń uprzywilejowanych zasobników jest tworzony domyślnie podczas włączania funkcji. | Tryb uprzywilejowany nie oznacza żadnych ograniczeń, w związku z tym jest odpowiednikiem nie mieć żadnych Azure Policy przypisania.
| [Standard zasad zabezpieczeń zasobnika — punkt odniesienia/wartość domyślna](https://kubernetes.io/docs/concepts/security/pod-security-standards/#baseline-default) | Użytkownik instaluje zasób linii bazowej zasad zabezpieczeń zasobnika. | Azure Policy udostępnia [wbudowaną inicjatywę linii bazowej, która](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2Fa8640138-9b0a-4a28-b8cb-1666c838647d) mapuje na zasady zabezpieczeń zasobników punktów odniesienia.
| [Standard zasad zabezpieczeń zasobników — ograniczone](https://kubernetes.io/docs/concepts/security/pod-security-standards/#restricted) | Użytkownik instaluje zasób z ograniczeniami zasad zabezpieczeń zasobnika. | Azure Policy udostępnia [wbudowaną inicjatywę](https://portal.azure.com/#blade/Microsoft_Azure_Policy/PolicyDetailBlade/definitionId/%2Fproviders%2FMicrosoft.Authorization%2FpolicySetDefinitions%2F42b8ef37-b724-4e24-bbc8-7a7708edfe00) z ograniczeniami, która mapuje na zasady zabezpieczeń zasobników z ograniczeniami.

## <a name="enable-pod-security-policy-on-an-aks-cluster"></a>Włączanie zasad zabezpieczeń zasobników w klastrze usługi AKS

Zasady zabezpieczeń zasobników można włączyć lub wyłączyć za pomocą [polecenia az aks update.][az-aks-update] Poniższy przykład włącza zasady zabezpieczeń zasobników w klastrze o nazwie *myAKSCluster* w grupie zasobów *o nazwie myResourceGroup.*

> [!NOTE]
> W rzeczywistych zastosowaniach nie należy włączać zasad zabezpieczeń zasobników, dopóki nie zdefiniuje się własnych zasad niestandardowych. W tym artykule włączysz zasady zabezpieczeń zasobników w pierwszym kroku, aby zobaczyć, jak zasady domyślne ograniczają wdrożenia zasobników.

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --enable-pod-security-policy
```

## <a name="default-aks-policies"></a>Domyślne zasady AKS

Po włączeniu zasad zabezpieczeń zasobników usługę AKS tworzy jedną domyślną zasady o *nazwie privileged*. Nie edytuj ani nie usuwaj zasad domyślnych. Zamiast tego utwórz własne zasady definiujące ustawienia, które chcesz kontrolować. Najpierw przyjrzyjmy się, jak te domyślne zasady wpływają na wdrożenia zasobników.

Aby wyświetlić dostępne zasady, użyj polecenia [kubectl get psp,][kubectl-get] jak pokazano w poniższym przykładzie

```console
$ kubectl get psp

NAME         PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged   true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *     configMap,emptyDir,projected,secret,downwardAPI,persistentVolumeClaim
```

Zasady *zabezpieczeń uprzywilejowanych* zasobników są stosowane do każdego uwierzytelnionego użytkownika w klastrze usługi AKS. To przypisanie jest kontrolowane przez klastry ClusterRoles i ClusterRoleBindings. Użyj [polecenia kubectl get rolebindings][kubectl-get] i wyszukaj powiązanie *default:privileged:* w przestrzeni *nazw kube-system:*

```console
kubectl get rolebindings default:privileged -n kube-system -o yaml
```

Jak pokazano w poniższych skróconych danych *wyjściowych, psp:privileged* ClusterRole jest przypisany do dowolnego *systemu:uwierzytelnionych* użytkowników. Ta możliwość zapewnia podstawowy poziom uprawnień bez konieczności tworzenia własnych zasad.

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

Ważne jest, aby zrozumieć, jak te zasady domyślne współdziałają z żądaniami użytkowników w celu zaplanowania zasobników przed rozpoczęciem tworzenia własnych zasad zabezpieczeń zasobników. W kilku następnych sekcjach zaplanujmy kilka zasobników, aby zobaczyć, jak te domyślne zasady są w działaniu.

## <a name="create-a-test-user-in-an-aks-cluster"></a>Tworzenie użytkownika testowego w klastrze usługi AKS

Domyślnie w przypadku użycia polecenia [az aks get-credentials][az-aks-get-credentials] poświadczenia administratora dla klastra usługi AKS są dodawane do  `kubectl` konfiguracji. Administrator pomija wymuszanie zasad zabezpieczeń zasobników. Jeśli używasz Azure Active Directory klastrów usługi AKS, możesz zalogować się przy użyciu poświadczeń użytkownika bez uprawnień administratora, aby zobaczyć wymuszanie zasad w działaniu. W tym artykule utworzymy konto użytkownika testowego w klastrze usługi AKS, z których można korzystać.

Utwórz przykładową przestrzeń nazw *o nazwie psp-aks* dla zasobów testowych przy użyciu [polecenia kubectl create namespace.][kubectl-create] Następnie utwórz konto usługi o nazwie *nonadmin-user* przy użyciu [polecenia kubectl create serviceaccount:][kubectl-create]

```console
kubectl create namespace psp-aks
kubectl create serviceaccount --namespace psp-aks nonadmin-user
```

Następnie utwórz pole roleBinding  dla użytkowników niebędących administratorami, aby wykonać podstawowe akcje w przestrzeni nazw przy użyciu [polecenia kubectl create rolebinding:][kubectl-create]

```console
kubectl create rolebinding \
    --namespace psp-aks \
    psp-aks-editor \
    --clusterrole=edit \
    --serviceaccount=psp-aks:nonadmin-user
```

### <a name="create-alias-commands-for-admin-and-non-admin-user"></a>Tworzenie poleceń aliasu dla administratora i użytkownika bez uprawnień administratora

Aby wyróżnić różnicę między zwykłym użytkownikiem administratora w przypadku korzystania z usługi i użytkownikiem niebędącym administratorem utworzonym w poprzednich `kubectl` krokach, utwórz dwa aliasy wiersza polecenia:

* Alias **kubectl-admin** jest dla zwykłego administratora i jest ograniczony do *przestrzeni nazw psp-aks.*
* Alias **kubectl-nonadminuser** jest dla użytkownika *nieadmin utworzonego* w poprzednim kroku i jest ograniczony do *przestrzeni nazw psp-aks.*

Utwórz te dwa aliasy, jak pokazano w następujących poleceniach:

```console
alias kubectl-admin='kubectl --namespace psp-aks'
alias kubectl-nonadminuser='kubectl --as=system:serviceaccount:psp-aks:nonadmin-user --namespace psp-aks'
```

## <a name="test-the-creation-of-a-privileged-pod"></a>Testowanie tworzenia zasobnika uprzywilejowanego

Najpierw przetestujmy, co się stanie, gdy zaplanujemy zasobnik z kontekstem zabezpieczeń `privileged: true` . Ten kontekst zabezpieczeń eskaluje uprawnienia zasobnika. W poprzedniej sekcji, w których były wyświetlane domyślne zasady zabezpieczeń zasobnika AKS, zasady *uprawnień* powinny odrzucać to żądanie.

Utwórz plik o nazwie `nginx-privileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-privileged
spec:
  containers:
    - name: nginx-privileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        privileged: true
```

Utwórz zasobnik przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-privileged.yaml
```

Nie można zaplanować zasobnika, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-privileged.yaml

Error from server (Forbidden): error when creating "nginx-privileged.yaml": pods "nginx-privileged" is forbidden: unable to validate against any pod security policy: []
```

Zasobnik nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przejściem.

## <a name="test-creation-of-an-unprivileged-pod"></a>Testowanie tworzenia nieuprawniania zasobnika

W poprzednim przykładzie specyfikacja zasobnika zażądała eskalacji uprzywilejowanej. To żądanie jest odmawiane przez domyślne *zasady zabezpieczeń* zasobnika uprawnień, więc nie można zaplanować zasobnika. Spróbujmy teraz uruchomić ten sam zasobnik NGINX bez żądania eskalacji uprawnień.

Utwórz plik o nazwie `nginx-unprivileged.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
```

Utwórz zasobnik przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Nie można zaplanować zasobnika, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged.yaml": pods "nginx-unprivileged" is forbidden: unable to validate against any pod security policy: []
```

Zasobnik nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przejściem do pracy.

## <a name="test-creation-of-a-pod-with-a-specific-user-context"></a>Testowanie tworzenia zasobnika z określonym kontekstem użytkownika

W poprzednim przykładzie obraz kontenera automatycznie próbował użyć katalogu głównego do powiązania NGINX z portem 80. To żądanie zostało odrzucone przez domyślne *zasady zabezpieczeń* zasobnika uprawnień, więc nie można uruchomić zasobnika. Spróbujmy teraz uruchomić ten sam zasobnik NGINX z określonym kontekstem użytkownika, takim jak `runAsUser: 2000` .

Utwórz plik o nazwie `nginx-unprivileged-nonroot.yaml` i wklej następujący manifest YAML:

```yaml
apiVersion: v1
kind: Pod
metadata:
  name: nginx-unprivileged-nonroot
spec:
  containers:
    - name: nginx-unprivileged
      image: mcr.microsoft.com/oss/nginx/nginx:1.14.2-alpine
      securityContext:
        runAsUser: 2000
```

Utwórz zasobnik przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml
```

Nie można zaplanować zasobnika, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl-nonadminuser apply -f nginx-unprivileged-nonroot.yaml

Error from server (Forbidden): error when creating "nginx-unprivileged-nonroot.yaml": pods "nginx-unprivileged-nonroot" is forbidden: unable to validate against any pod security policy: []
```

Zasobnik nie dociera do etapu planowania, dlatego nie ma żadnych zasobów do usunięcia przed przejściem do pracy.

## <a name="create-a-custom-pod-security-policy"></a>Tworzenie niestandardowych zasad zabezpieczeń zasobników

Teraz, gdy już wiesz, jak są stosowane domyślne zasady zabezpieczeń zasobników, udostępnijmy użytkownikom niebędącym administratorami sposób pomyślnego zaplanowania zasobników. 

Utwórzmy zasady odrzucania zasobników, które żądają uprzywilejowanego dostępu. Inne opcje, takie jak *runAsUser* lub dozwolone *woluminy,* nie są jawnie ograniczone. Ten typ zasad odrzuca żądanie uprzywilejowanego dostępu, ale w przeciwnym razie umożliwia klastrowi uruchomienie żądanych zasobników.

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

Utwórz zasady za pomocą [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged.yaml
```

Aby wyświetlić dostępne zasady, użyj [polecenia kubectl get psp,][kubectl-get] jak pokazano w poniższym przykładzie. Porównaj *zasady psp-deny-privileged*  z domyślnymi zasadami uprawnień, które były wymuszane w poprzednich przykładach w celu utworzenia zasobnika. Zasady nie mogą korzystać tylko z eskalacji *PRIV.* Nie ma żadnych ograniczeń dotyczących użytkownika lub grupy *dla zasad psp-deny-privileged.*

```console
$ kubectl get psp

NAME                  PRIV    CAPS   SELINUX    RUNASUSER          FSGROUP     SUPGROUP    READONLYROOTFS   VOLUMES
privileged            true    *      RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *
psp-deny-privileged   false          RunAsAny   RunAsAny           RunAsAny    RunAsAny    false            *          
```

## <a name="allow-user-account-to-use-the-custom-pod-security-policy"></a>Zezwalaj kontu użytkownika na używanie niestandardowych zasad zabezpieczeń zasobników

W poprzednim kroku utworzono zasady zabezpieczeń zasobników w celu odrzucania zasobników, które żądają uprzywilejowanego dostępu. Aby zezwolić na używane zasady, należy utworzyć *rolę lub* *klasterRola*. Następnie należy skojarzyć jedną z tych ról przy użyciu *powiązania roli* lub *clusterRoleBinding.*

W tym przykładzie utwórz klasterRola, która umożliwia *korzystanie* *z zasad psp-deny-privileged* utworzonych w poprzednim kroku. Utwórz plik o nazwie `psp-deny-privileged-clusterrole.yaml` i wklej następujący manifest YAML:

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

Utwórz klaster ClusterRole za pomocą [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrole.yaml
```

Teraz utwórz klaster ClusterRoleBinding, aby użyć roli ClusterRole utworzonej w poprzednim kroku. Utwórz plik o nazwie `psp-deny-privileged-clusterrolebinding.yaml` i wklej następujący manifest YAML:

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

Utwórz klaster ClusterRoleBinding przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę manifestu YAML:

```console
kubectl apply -f psp-deny-privileged-clusterrolebinding.yaml
```

> [!NOTE]
> W pierwszym kroku tego artykułu funkcja zasad zabezpieczeń zasobników została włączona w klastrze usługi AKS. Zalecaną praktyką było włączenie funkcji zasad zabezpieczeń zasobnika tylko po zdefiniowanych własnych zasadach. Jest to etap, na którym należy włączyć funkcję zasad zabezpieczeń zasobników. Zdefiniowano co najmniej jedną niestandardową zasady, a z tymi zasadami skojarzono konta użytkowników. Teraz możesz bezpiecznie włączyć funkcję zasad zabezpieczeń zasobników i zminimalizować problemy spowodowane przez zasady domyślne.

## <a name="test-the-creation-of-an-unprivileged-pod-again"></a>Ponownie przetestuj tworzenie nieupryzowanych zasobników

Po zastosowaniu niestandardowych zasad zabezpieczeń zasobnika i powiązaniu dla konta użytkownika w celu użycia zasad spróbujmy ponownie utworzyć zasobnik nieuprawniany. Użyj tego samego `nginx-privileged.yaml` manifestu, aby utworzyć zasobnik przy użyciu [polecenia kubectl apply:][kubectl-apply]

```console
kubectl-nonadminuser apply -f nginx-unprivileged.yaml
```

Zasobnik został pomyślnie zaplanowany. Gdy sprawdzasz stan zasobnika za pomocą polecenia [kubectl get pods,][kubectl-get] zasobnik ma stan *Running (Uruchomiona):*

```
$ kubectl-nonadminuser get pods

NAME                 READY   STATUS    RESTARTS   AGE
nginx-unprivileged   1/1     Running   0          7m14s
```

W tym przykładzie pokazano, jak można utworzyć niestandardowe zasady zabezpieczeń zasobników w celu zdefiniowania dostępu do klastra usługi AKS dla różnych użytkowników lub grup. Domyślne zasady usługi AKS zapewniają ścisłą kontrolę nad tym, jakie zasobniki można uruchamiać, więc utwórz własne zasady niestandardowe, aby prawidłowo zdefiniować potrzebne ograniczenia.

Usuń nieuprawnniany zasobnik NGINX za pomocą polecenia [kubectl delete][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl-nonadminuser delete -f nginx-unprivileged.yaml
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyłączyć zasady zabezpieczeń zasobników, ponownie użyj [polecenia az aks update.][az-aks-update] Poniższy przykład wyłącza zasady zabezpieczeń zasobników w nazwie klastra *myAKSCluster* w grupie zasobów *o nazwie myResourceGroup:*

```azurecli-interactive
az aks update \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --disable-pod-security-policy
```

Następnie usuń klastry ClusterRole i ClusterRoleBinding:

```console
kubectl delete -f psp-deny-privileged-clusterrolebinding.yaml
kubectl delete -f psp-deny-privileged-clusterrole.yaml
```

Usuń zasady zabezpieczeń za pomocą [polecenia kubectl delete][kubectl-delete] i określ nazwę manifestu YAML:

```console
kubectl delete -f psp-deny-privileged.yaml
```

Na koniec usuń *przestrzeń nazw psp-aks:*

```console
kubectl delete namespace psp-aks
```

## <a name="next-steps"></a>Następne kroki

W tym artykule popisano, jak utworzyć zasady zabezpieczeń zasobników, aby uniemożliwić korzystanie z uprzywilejowanego dostępu. Istnieje wiele funkcji, które zasady mogą wymuszać, takich jak typ woluminu lub użytkownik Uruchom jako. Aby uzyskać więcej informacji na temat dostępnych opcji, zobacz dokumenty referencyjne dotyczące zasad zabezpieczeń zasobnika [kubernetes][kubernetes-policy-reference].

Aby uzyskać więcej informacji na temat ograniczania ruchu sieciowego zasobników, zobacz Zabezpieczanie ruchu między zasobnikami przy użyciu zasad sieciowych w [u usługi AKS.][network-policies]

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
[az-feature-register]: /cli/azure/feature#az_feature_register
[az-feature-list]: /cli/azure/feature#az_feature_list
[az-provider-register]: /cli/azure/provider#az_provider_register
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-aks-update]: /cli/azure/ext/aks-preview/aks#ext-aks-preview-az-aks-update
[az-extension-add]: /cli/azure/extension#az_extension_add
[aks-support-policies]: support-policies.md
[aks-faq]: faq.md
[az-extension-add]: /cli/azure/extension#az_extension_add
[az-extension-update]: /cli/azure/extension#az_extension_update
[policy-samples]: ./policy-reference.md#microsoftcontainerservice
[azure-policy-add-on]: ../governance/policy/concepts/policy-for-kubernetes.md
