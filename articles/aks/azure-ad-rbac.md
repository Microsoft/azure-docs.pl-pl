---
title: Używanie kontroli RBAC usługi Azure AD i platformy Kubernetes dla klastrów
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak za pomocą członkostwa w grupie Azure Active Directory ograniczyć dostęp do zasobów klastra przy użyciu kontroli dostępu opartej na rolach (RBAC) usługi Kubernetes w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 0d5171e9e9a5d7f033ff615a3f1205b8dc93966f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769557"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Kontrola dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości Azure Active Directory Kubernetes w Azure Kubernetes Service

Azure Kubernetes Service (AKS) można skonfigurować do używania usługi Azure Active Directory (AD) do uwierzytelniania użytkowników. W tej konfiguracji logujesz się do klastra usługi AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Można również skonfigurować kontrolę dostępu opartą na rolach (RBAC) na platformie Kubernetes, aby ograniczyć dostęp do zasobów klastra na podstawie tożsamości użytkownika lub członkostwa w grupie.

W tym artykule pokazano, jak za pomocą członkostwa w grupie usługi Azure AD kontrolować dostęp do przestrzeni nazw i zasobów klastra przy użyciu kontroli dostępu na podstawie ról platformy Kubernetes w klastrze usługi AKS. Przykładowe grupy i użytkownicy są tworzone w usłudze Azure AD, a następnie role i rolebindings są tworzone w klastrze usługi AKS w celu udzielenia odpowiednich uprawnień do tworzenia i wyświetlania zasobów.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz istniejący klaster usługi AKS włączony z integracją z usługą Azure AD. Jeśli potrzebujesz klastra usługi AKS, zobacz [Integrate Azure Active Directory with AKS (Integrowanie aplikacji z Azure Active Directory AKS).][azure-ad-aks-cli]

Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Tworzenie grup demonstracyjnych w usłudze Azure AD

W tym artykule utworzymy dwie role użytkownika, których można użyć do pokazania, jak kontrola dostępu na podstawie ról platformy Kubernetes i usługa Azure AD kontrolują dostęp do zasobów klastra. Używane są następujące dwie przykładowe role:

* **deweloper aplikacji**
    * Użytkownik o *nazwie aksdev,* który jest częścią *grupy appdev.*
* **Inżynier niezawodności lokacji**
    * Użytkownik o *nazwie akssre,* który jest częścią *grupy opssre.*

W środowiskach produkcyjnych można używać istniejących użytkowników i grup w dzierżawie usługi Azure AD.

Najpierw pobierz identyfikator zasobu klastra usługi AKS za pomocą [polecenia az aks show.][az-aks-show] Przypisz identyfikator zasobu do zmiennej o *nazwie AKS_ID,* aby można było się do niego odwoływać w dodatkowych poleceniach.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Utwórz pierwszą przykładową grupę w usłudze Azure AD dla deweloperów aplikacji przy użyciu [polecenia az ad group create.][az-ad-group-create] Poniższy przykład tworzy grupę o nazwie *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Teraz utwórz przypisanie roli platformy Azure dla *grupy appdev* za pomocą [polecenia az role assignment create.][az-role-assignment-create] To przypisanie umożliwia dowolnym elementom członkowskim grupy interakcję z klastrem usługi AKS przez przyznanie mu `kubectl` Azure Kubernetes Service roli użytkownika *klastra*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Jeśli wystąpi błąd, taki jak , zaczekaj kilka sekund na propagację identyfikatora obiektu grupy usługi Azure AD w katalogu, `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` a następnie spróbuj ponownie wykonać `az role assignment create` polecenie.

Utwórz drugą przykładową grupę dla SRE o nazwie *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Ponownie utwórz przypisanie roli platformy Azure, aby udzielić członkom grupy *Azure Kubernetes Service roli użytkownika klastra:*

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Tworzenie użytkowników demonstracyjnych w usłudze Azure AD

W przypadku dwóch przykładowych grup utworzonych w usłudze Azure AD dla naszych deweloperów aplikacji i S SRE utwórzmy teraz dwóch przykładowych użytkowników. Aby przetestować integrację kontroli RBAC na platformie Kubernetes na końcu tego artykułu, zaloguj się do klastra usługi AKS przy użyciu tych kont.

Ustaw główną nazwę użytkownika (UPN) i hasło dla deweloperów aplikacji. Następujące polecenie wyświetli monit o upn i ustawi ją na *AAD_DEV_UPN* do użycia w późniejszym poleceniu (pamiętaj, że polecenia w tym artykule są wprowadzane do powłoki BASH). Nazwa UPN musi zawierać zweryfikowaną nazwę domeny dzierżawy, na przykład `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

Następujące polecenie wyświetli monit o hasło i ustawia je na AAD_DEV_PW *do* użycia w późniejszym poleceniu.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Utwórz pierwsze konto użytkownika w usłudze Azure AD za pomocą [polecenia az ad user create.][az-ad-user-create]

Poniższy przykład tworzy użytkownika o nazwie wyświetlanej *AKS Dev* oraz nazwie UPN i zabezpieczonych hasłach przy użyciu wartości w polach *AAD_DEV_UPN* *i AAD_DEV_PW*:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Teraz dodaj użytkownika do grupy *appdev* utworzonej w poprzedniej sekcji przy użyciu polecenia [az ad group member add:][az-ad-group-member-add]

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Ustaw nazwy UPN i hasło dla S SRE. Następujące polecenie wyświetla monit o upn i ustawia ją na *AAD_SRE_UPN* do użycia w późniejszym poleceniu (pamiętaj, że polecenia w tym artykule są wprowadzane do powłoki BASH). Nazwa UPN musi zawierać zweryfikowaną nazwę domeny dzierżawy, na przykład `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

Następujące polecenie wyświetla monit o hasło i ustawia je na *AAD_SRE_PW* do użycia w późniejszym poleceniu.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Utwórz drugie konto użytkownika. Poniższy przykład tworzy użytkownika o nazwie wyświetlanej *AKS SRE* oraz nazwie UPN i zabezpieczonych hasłach przy użyciu wartości w polach *AAD_SRE_UPN* *i AAD_SRE_PW*:

```azurecli-interactive
# Create a user for the SRE role
AKSSRE_ID=$(az ad user create \
  --display-name "AKS SRE" \
  --user-principal-name $AAD_SRE_UPN \
  --password $AAD_SRE_PW \
  --query objectId -o tsv)

# Add the user to the opssre Azure AD group
az ad group member add --group opssre --member-id $AKSSRE_ID
```

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Tworzenie zasobów klastra usługi AKS dla deweloperów aplikacji

Grupy i użytkownicy usługi Azure AD są teraz tworzone. Utworzono przypisania ról platformy Azure, aby członkowie grupy połączyli się z klastrem usługi AKS jako zwykły użytkownik. Teraz skonfigurujmy klaster usługi AKS, aby umożliwić różnym grupom dostęp do określonych zasobów.

Najpierw pobierz poświadczenia administratora klastra za pomocą [polecenia az aks get-credentials.][az-aks-get-credentials] W jednej z poniższych sekcji przedstawiono  poświadczenia zwykłego klastra użytkowników, aby zobaczyć przepływ uwierzytelniania usługi Azure AD w działaniu.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Utwórz przestrzeń nazw w klastrze usługi AKS za pomocą [polecenia kubectl create namespace.][kubectl-create] Poniższy przykład tworzy nazwę przestrzeni nazw *dev*:

```console
kubectl create namespace dev
```

Na stronie Kubernetes *role* definiują uprawnienia do udzielenia, a *roleBindings* stosują je do żądanych użytkowników lub grup. Te przypisania można zastosować do danej przestrzeni nazw lub do całego klastra. Aby uzyskać więcej informacji, zobacz [Using Kubernetes RBAC authorization (Używanie autoryzacji RBAC na platformie Kubernetes).][rbac-authorization]

Najpierw utwórz rolę dla przestrzeni nazw *dev.* Ta rola przyznaje pełne uprawnienia do przestrzeni nazw. W środowiskach produkcyjnych można określić bardziej szczegółowe uprawnienia dla różnych użytkowników lub grup.

Utwórz plik o nazwie `role-dev-namespace.yaml` i wklej następujący manifest YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-full-access
  namespace: dev
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Utwórz rolę przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Następnie pobierz identyfikator zasobu dla grupy *appdev* za pomocą [polecenia az ad group show.][az-ad-group-show] Ta grupa zostanie ustawiona jako temat obiektu RoleBinding w następnym kroku.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Teraz utwórz grupę RoleBinding dla *grupy appdev,* aby użyć wcześniej utworzonej roli na użytek dostępu do przestrzeni nazw. Utwórz plik o nazwie `rolebinding-dev-namespace.yaml` i wklej następujący manifest YAML. W ostatnim wierszu zastąp *identyfikator groupObjectId*  identyfikatorem obiektu grupy z poprzedniego polecenia:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: dev-user-access
  namespace: dev
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: dev-user-full-access
subjects:
- kind: Group
  namespace: dev
  name: groupObjectId
```

Utwórz parametr RoleBinding przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Tworzenie zasobów klastra AKS dla S SRE

Teraz powtórz poprzednie kroki, aby utworzyć przestrzeń nazw, rolę i pocięcie ról dla serwerów S SRE.

Najpierw utwórz przestrzeń nazw *sre* za pomocą [polecenia kubectl create namespace:][kubectl-create]

```console
kubectl create namespace sre
```

Utwórz plik o nazwie `role-sre-namespace.yaml` i wklej następujący manifest YAML:

```yaml
kind: Role
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-full-access
  namespace: sre
rules:
- apiGroups: ["", "extensions", "apps"]
  resources: ["*"]
  verbs: ["*"]
- apiGroups: ["batch"]
  resources:
  - jobs
  - cronjobs
  verbs: ["*"]
```

Utwórz rolę przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Pobierz identyfikator zasobu dla *grupy opssre* za pomocą [polecenia az ad group show:][az-ad-group-show]

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Utwórz elementu RoleBinding dla *grupy opssre,* aby użyć wcześniej utworzonej roli na użytek dostępu do przestrzeni nazw. Utwórz plik o nazwie `rolebinding-sre-namespace.yaml` i wklej następujący manifest YAML. W ostatnim wierszu zastąp *identyfikator groupObjectId*  identyfikatorem obiektu grupy z poprzedniego polecenia:

```yaml
kind: RoleBinding
apiVersion: rbac.authorization.k8s.io/v1
metadata:
  name: sre-user-access
  namespace: sre
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: Role
  name: sre-user-full-access
subjects:
- kind: Group
  namespace: sre
  name: groupObjectId
```

Utwórz parametr RoleBinding przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Interakcja z zasobami klastra przy użyciu tożsamości usługi Azure AD

Teraz przetestujmy oczekiwane uprawnienia podczas tworzenia zasobów i zarządzania nimi w klastrze usługi AKS. W tych przykładach planujesz i wyświetlasz zasobniki w przestrzeni nazw przypisanej przez użytkownika. Następnie próbujesz zaplanować i wyświetlić zasobniki poza przypisaną przestrzenią nazw.

Najpierw *zresetuj kontekst kubeconfig* przy użyciu [polecenia az aks get-credentials.][az-aks-get-credentials] W poprzedniej sekcji ustawisz kontekst przy użyciu poświadczeń administratora klastra. Administrator pomija monity logowania do usługi Azure AD. Bez parametru stosowany jest kontekst użytkownika, który wymaga uwierzytelnienia wszystkich żądań `--admin` przy użyciu usługi Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Zaplanuj podstawowy zasobnik NGINX przy użyciu [polecenia kubectl run][kubectl-run] w przestrzeni *nazw dev:*

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

W wierszu polecenia logowania wprowadź poświadczenia dla własnego konta utworzonego `appdev@contoso.com` na początku artykułu. Po pomyślnym zalogowaniu token konta jest buforowany dla przyszłych `kubectl` poleceń. NGINX został pomyślnie zaplanowany, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Teraz użyj polecenia [kubectl get pods,][kubectl-get] aby wyświetlić zasobniki w przestrzeni *nazw dev.*

```console
kubectl get pods --namespace dev
```

Jak pokazano w poniższych przykładowych danych wyjściowych, zasobnik NGINX pomyślnie *działa:*

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Tworzenie i wyświetlanie zasobów klastra poza przypisaną przestrzenią nazw

Teraz spróbuj wyświetlić zasobniki poza przestrzenią *nazw dev.* Użyj ponownie [polecenia kubectl get pods,][kubectl-get] tym razem, aby `--all-namespaces` zobaczyć w następujący sposób:

```console
kubectl get pods --all-namespaces
```

Członkostwo użytkownika w grupie nie ma roli Kubernetes, która zezwala na tę akcję, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

W ten sam sposób spróbuj zaplanować zasobnik w innej przestrzeni nazw, takiej jak *przestrzeń nazw sre.* Członkostwo użytkownika w grupie nie jest zgodne z rolą Kubernetes i jej pocięciem w celu udzielenia tych uprawnień, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testowanie dostępu SRE do zasobów klastra AKS

Aby upewnić się, że członkostwo w grupie usługi Azure AD i kontroli RBAC platformy Kubernetes działają prawidłowo między różnymi użytkownikami i grupami, wypróbuj poprzednie polecenia po zalogowaniu się jako *użytkownik opssre.*

*Zresetuj kontekst kubeconfig* przy użyciu polecenia [az aks get-credentials,][az-aks-get-credentials] które wyczyści wcześniej buforowany token uwierzytelniania dla użytkownika *aksdev:*

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Spróbuj zaplanować i wyświetlić zasobniki w przypisanej przestrzeni *nazw sre.* Po wyświetleniu monitu zaloguj się przy użyciu własnych `opssre@contoso.com` poświadczeń utworzonych na początku artykułu:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Jak pokazano w poniższych przykładowych danych wyjściowych, możesz pomyślnie utworzyć i wyświetlić zasobniki:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Teraz spróbuj wyświetlić lub zaplanować zasobniki poza przypisaną przestrzenią nazw SRE:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Te `kubectl` polecenia nie powiodą się, jak pokazano w poniższych przykładowych danych wyjściowych. Członkostwo w grupie użytkownika oraz rola Kubernetes i roleBindings nie przyznają uprawnień do tworzenia lub menedżera zasobów w innych przestrzeniach nazw:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule utworzono zasoby w klastrze usługi AKS oraz użytkowników i grupy w usłudze Azure AD. Aby wyczyścić wszystkie te zasoby, uruchom następujące polecenia:

```azurecli-interactive
# Get the admin kubeconfig context to delete the necessary cluster resources
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin

# Delete the dev and sre namespaces. This also deletes the pods, Roles, and RoleBindings
kubectl delete namespace dev
kubectl delete namespace sre

# Delete the Azure AD user accounts for aksdev and akssre
az ad user delete --upn-or-object-id $AKSDEV_ID
az ad user delete --upn-or-object-id $AKSSRE_ID

# Delete the Azure AD groups for appdev and opssre. This also deletes the Azure role assignments.
az ad group delete --group appdev
az ad group delete --group opssre
```

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat zabezpieczania klastrów Kubernetes, zobacz [Access and identity options for AKS (Opcje dostępu i tożsamości dla usługi AKS).][rbac-authorization]

Aby uzyskać najlepsze rozwiązania dotyczące tożsamości i kontroli zasobów, zobacz [Best practices for authentication and authorization in AKS (Najlepsze rozwiązania][operator-best-practices-identity]dotyczące uwierzytelniania i autoryzacji w u usługi AKS).

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-ad-group-create]: /cli/azure/ad/group#az_ad_group_create
[az-role-assignment-create]: /cli/azure/role/assignment#az_role_assignment_create
[az-ad-user-create]: /cli/azure/ad/user#az_ad_user_create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az_ad_group_member_add
[az-ad-group-show]: /cli/azure/ad/group#az_ad_group_show
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
