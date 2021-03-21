---
title: Korzystanie z usługi Azure AD i Kubernetes RBAC dla klastrów
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak korzystać z członkostwa w grupie Azure Active Directory, aby ograniczyć dostęp do zasobów klastra przy użyciu kontroli dostępu opartej na rolach (Kubernetes RBAC) w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/17/2021
ms.openlocfilehash: 72b2c456d62b899f2b04041929434da668cad82d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104583534"
---
# <a name="control-access-to-cluster-resources-using-kubernetes-role-based-access-control-and-azure-active-directory-identities-in-azure-kubernetes-service"></a>Kontrola dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach Kubernetes i tożsamości Azure Active Directory w usłudze Azure Kubernetes Service

Usługę Azure Kubernetes Service (AKS) można skonfigurować do korzystania z Azure Active Directory (AD) do uwierzytelniania użytkowników. W tej konfiguracji użytkownik loguje się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Można również skonfigurować kontrolę dostępu opartą na rolach Kubernetes (Kubernetes RBAC), aby ograniczyć dostęp do zasobów klastra na podstawie tożsamości użytkownika lub członkostwa w grupie.

W tym artykule pokazano, jak za pomocą członkostwa w grupach usługi Azure AD kontrolować dostęp do przestrzeni nazw i zasobów klastra przy użyciu Kubernetes RBAC w klastrze AKS. Przykładowe grupy i użytkownicy są tworzone w usłudze Azure AD, a następnie role i RoleBindings są tworzone w klastrze AKS w celu przyznania odpowiednich uprawnień do tworzenia i wyświetlania zasobów.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W tym artykule przyjęto założenie, że masz włączony klaster AKS z integracją z usługą Azure AD. Jeśli potrzebujesz klastra AKS, zobacz [integrowanie Azure Active Directory z AKS][azure-ad-aks-cli].

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="create-demo-groups-in-azure-ad"></a>Tworzenie grup demonstracyjnych w usłudze Azure AD

W tym artykule utworzyszmy dwie role użytkowników, których można użyć do określenia sposobu, w jaki Kubernetes RBAC i usługa Azure AD kontrolują dostęp do zasobów klastra. Używane są następujące dwa przykładowe role:

* **Deweloper aplikacji**
    * Użytkownik o nazwie *aksdev* , który jest częścią grupy *appdev* .
* **Inżynier niezawodności lokacji**
    * Użytkownik o nazwie *akssre* , który jest częścią grupy *opssre* .

W środowiskach produkcyjnych można używać istniejących użytkowników i grup w ramach dzierżawy usługi Azure AD.

Najpierw Pobierz identyfikator zasobu klastra AKS przy użyciu polecenia [AZ AKS show][az-aks-show] . Przypisz identyfikator zasobu do zmiennej o nazwie *AKS_ID* , aby można było odwoływać się do niej w dodatkowych poleceniach.

```azurecli-interactive
AKS_ID=$(az aks show \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --query id -o tsv)
```

Utwórz pierwszą przykładową grupę w usłudze Azure AD dla deweloperów aplikacji za pomocą polecenia [AZ AD Group Create][az-ad-group-create] . Poniższy przykład tworzy grupę o nazwie *appdev*:

```azurecli-interactive
APPDEV_ID=$(az ad group create --display-name appdev --mail-nickname appdev --query objectId -o tsv)
```

Teraz Utwórz przypisanie roli platformy Azure dla grupy *appdev* za pomocą polecenia [AZ role przypisanie Create][az-role-assignment-create] . To przypisanie umożliwia członkom grupy korzystanie z programu w `kubectl` celu współdziałania z klastrem AKS, przyznając im *rolę użytkownika klastra usługi Azure Kubernetes*.

```azurecli-interactive
az role assignment create \
  --assignee $APPDEV_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

> [!TIP]
> Jeśli wystąpi błąd, na przykład `Principal 35bfec9328bd4d8d9b54dea6dac57b82 does not exist in the directory a5443dcd-cd0e-494d-a387-3039b419f0d5.` , odczekaj kilka sekund, aby identyfikator obiektu grupy usługi Azure AD został rozpropagowany przez katalog, a następnie spróbuj `az role assignment create` ponownie wykonać polecenie.

Utwórz drugą przykładową grupę, która dla SREs o nazwie *opssre*:

```azurecli-interactive
OPSSRE_ID=$(az ad group create --display-name opssre --mail-nickname opssre --query objectId -o tsv)
```

Ponownie utwórz przypisanie roli platformy Azure, aby przyznać członkom grupy *rolę użytkownika klaster usługi Azure Kubernetes*:

```azurecli-interactive
az role assignment create \
  --assignee $OPSSRE_ID \
  --role "Azure Kubernetes Service Cluster User Role" \
  --scope $AKS_ID
```

## <a name="create-demo-users-in-azure-ad"></a>Tworzenie użytkowników demonstracyjnych w usłudze Azure AD

W przypadku dwóch przykładowych grup utworzonych w usłudze Azure AD dla naszych deweloperów aplikacji i SREs, teraz można utworzyć dwóch przykładowych użytkowników. Aby przetestować integrację Kubernetes RBAC na końcu artykułu, należy zalogować się do klastra AKS przy użyciu tych kont.

Ustaw główną nazwę użytkownika (UPN) i hasło dla deweloperów aplikacji. Następujące polecenie wyświetla monit o nazwę UPN i ustawia go na *AAD_DEV_UPN* do użycia w późniejszym poleceniu (należy pamiętać, że polecenia w tym artykule są wprowadzane do powłoki bash). Nazwa UPN musi zawierać zweryfikowaną nazwę domeny dzierżawy, na przykład `aksdev@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for application developers: " && read AAD_DEV_UPN
```

Następujące polecenie poprosi o hasło i ustawia je na *AAD_DEV_PW* do użycia w późniejszym poleceniu.

```azurecli-interactive
echo "Please enter the secure password for application developers: " && read AAD_DEV_PW
```

Utwórz pierwsze konto użytkownika w usłudze Azure AD za pomocą polecenia [AZ AD User Create][az-ad-user-create] .

Poniższy przykład tworzy użytkownika o nazwie wyświetlanej *AKS dev* oraz nazwy UPN i Secure Password przy użyciu wartości w *AAD_DEV_UPN* i *AAD_DEV_PW*:

```azurecli-interactive
AKSDEV_ID=$(az ad user create \
  --display-name "AKS Dev" \
  --user-principal-name $AAD_DEV_UPN \
  --password $AAD_DEV_PW \
  --query objectId -o tsv)
```

Teraz Dodaj użytkownika do grupy *appdev* utworzonej w poprzedniej sekcji za pomocą polecenia [AZ AD Group member Add][az-ad-group-member-add] :

```azurecli-interactive
az ad group member add --group appdev --member-id $AKSDEV_ID
```

Ustaw nazwę UPN i hasło dla SREs. Następujące polecenie wyświetla monit o nazwę UPN i ustawia go na *AAD_SRE_UPN* do użycia w późniejszym poleceniu (należy pamiętać, że polecenia w tym artykule są wprowadzane do powłoki bash). Nazwa UPN musi zawierać zweryfikowaną nazwę domeny dzierżawy, na przykład `akssre@contoso.com` .

```azurecli-interactive
echo "Please enter the UPN for SREs: " && read AAD_SRE_UPN
```

Następujące polecenie poprosi o hasło i ustawia je na *AAD_SRE_PW* do użycia w późniejszym poleceniu.

```azurecli-interactive
echo "Please enter the secure password for SREs: " && read AAD_SRE_PW
```

Utwórz drugie konto użytkownika. Poniższy przykład tworzy użytkownika o nazwie wyświetlanej *AKS SRE* i nazwę UPN oraz bezpieczne hasło przy użyciu wartości w *AAD_SRE_UPN* i *AAD_SRE_PW*:

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

## <a name="create-the-aks-cluster-resources-for-app-devs"></a>Tworzenie zasobów klastra AKS dla aplikacji deweloperzy

Grupy i użytkownicy usługi Azure AD są teraz tworzone. Utworzono przypisania ról platformy Azure dla członków grupy, którzy mają łączyć się z klastrem AKS jako zwykły użytkownik. Teraz Skonfigurujmy klaster AKS, aby umożliwić tym różnym grupom dostęp do określonych zasobów.

Najpierw Uzyskaj poświadczenia administratora klastra przy użyciu polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] . W jednej z poniższych sekcji otrzymujesz regularne poświadczenia klastra *użytkownika* , aby zobaczyć przepływ uwierzytelniania usługi Azure AD w akcji.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --admin
```

Utwórz przestrzeń nazw w klastrze AKS przy użyciu polecenia [Create Namespace polecenia kubectl][kubectl-create] . Poniższy przykład tworzy nazwę przestrzeni nazw *dev*:

```console
kubectl create namespace dev
```

W programie Kubernetes *role* definiują uprawnienia do udzielenia, a *RoleBindings* stosują je do żądanych użytkowników lub grup. Te przypisania można zastosować do danej przestrzeni nazw lub całego klastra. Aby uzyskać więcej informacji, zobacz [using KUBERNETES RBAC Authorization][rbac-authorization].

Najpierw Utwórz rolę dla przestrzeni nazw *dev* . Ta rola przyznaje pełne uprawnienia do przestrzeni nazw. W środowisku produkcyjnym można określić bardziej szczegółowe uprawnienia dla różnych użytkowników lub grup.

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

Utwórz rolę przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f role-dev-namespace.yaml
```

Następnie Pobierz identyfikator zasobu dla grupy *appdev* za pomocą polecenia [AZ AD Group Show][az-ad-group-show] . Ta grupa jest ustawiana jako temat Rolibinding w następnym kroku.

```azurecli-interactive
az ad group show --group appdev --query objectId -o tsv
```

Teraz Utwórz Rolębinding dla grupy *appdev* , aby używać wcześniej utworzonej roli do uzyskiwania dostępu do przestrzeni nazw. Utwórz plik o nazwie `rolebinding-dev-namespace.yaml` i wklej następujący manifest YAML. W ostatnim wierszu Zamień *groupObjectId*  na dane wyjściowe identyfikatora obiektu grupy z poprzedniego polecenia:

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

Utwórz Rolębinding przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f rolebinding-dev-namespace.yaml
```

## <a name="create-the-aks-cluster-resources-for-sres"></a>Tworzenie zasobów klastra AKS dla SREs

Teraz powtórz poprzednie kroki, aby utworzyć przestrzeń nazw, rolę i Rolębinding dla SREs.

Najpierw utwórz przestrzeń nazw dla *SRE* za pomocą polecenia [polecenia kubectl Create Namespace][kubectl-create] :

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

Utwórz rolę przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f role-sre-namespace.yaml
```

Pobierz identyfikator zasobu dla grupy *opssre* za pomocą polecenia [AZ AD Group Show][az-ad-group-show] :

```azurecli-interactive
az ad group show --group opssre --query objectId -o tsv
```

Utwórz Rolębinding dla grupy *opssre* , aby używać wcześniej utworzonej roli do uzyskiwania dostępu do przestrzeni nazw. Utwórz plik o nazwie `rolebinding-sre-namespace.yaml` i wklej następujący manifest YAML. W ostatnim wierszu Zamień *groupObjectId*  na dane wyjściowe identyfikatora obiektu grupy z poprzedniego polecenia:

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

Utwórz Rolębinding przy użyciu polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f rolebinding-sre-namespace.yaml
```

## <a name="interact-with-cluster-resources-using-azure-ad-identities"></a>Korzystanie z zasobów klastra przy użyciu tożsamości usługi Azure AD

Teraz Przetestuj oczekiwane uprawnienia podczas tworzenia zasobów i zarządzania nimi w klastrze AKS. W tych przykładach planujesz i przeglądasz w przypisanej do użytkownika obszarze nazw. Następnie próbujesz zaplanować i wyświetlić wartości z obszaru na zewnątrz przypisanej przestrzeni nazw.

Najpierw Zresetuj kontekst *kubeconfig* za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] . W poprzedniej sekcji ustawiasz kontekst przy użyciu poświadczeń administratora klastra. Użytkownik administracyjny pomija wyświetlane w usłudze Azure AD polecenia logowania. Bez `--admin` parametru kontekst użytkownika jest stosowany, który wymaga uwierzytelnienia wszystkich żądań za pomocą usługi Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Zaplanuj podstawowe NGINX pod za pomocą polecenia [polecenia kubectl Run][kubectl-run] w przestrzeni nazw *dev* :

```console
kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

Jako monit logowania wprowadź poświadczenia dla własnego `appdev@contoso.com` konta utworzonego na początku artykułu. Po pomyślnym zalogowaniu token konta zostanie zapisany w pamięci podręcznej dla przyszłych `kubectl` poleceń. Pomyślnie zaplanowano NGINX, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code B24ZD6FP8 to authenticate.

pod/nginx-dev created
```

Teraz Użyj [polecenia kubectl GetBinding][kubectl-get] , aby wyświetlić w przestrzeni nazw *dev* .

```console
kubectl get pods --namespace dev
```

Jak pokazano w poniższym przykładzie danych wyjściowych, pomyślnie *działa* Nginx pod:

```console
$ kubectl get pods --namespace dev

NAME        READY   STATUS    RESTARTS   AGE
nginx-dev   1/1     Running   0          4m
```

### <a name="create-and-view-cluster-resources-outside-of-the-assigned-namespace"></a>Tworzenie i wyświetlanie zasobów klastra poza przypisaną przestrzenią nazw

Teraz spróbuj wyświetlić wartości podst. poza przestrzenią nazw *dev* . Użyj ponownie polecenia [polecenia kubectl Pobierz][kubectl-get] te godziny, aby zobaczyć `--all-namespaces` w następujący sposób:

```console
kubectl get pods --all-namespaces
```

Członkostwo w grupie użytkownika nie ma roli Kubernetes, która umożliwia wykonanie tej akcji, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl get pods --all-namespaces

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot list resource "pods" in API group "" at the cluster scope
```

W ten sam sposób spróbuj zaplanować element pod w różnych przestrzeniach nazw, takich jak przestrzeń nazw *SRE* . Członkostwo w grupie użytkownika nie jest wyrównane z rolą Kubernetes i Roląbinding, aby przyznać te uprawnienia, jak pokazano w następujących przykładowych danych wyjściowych:

```console
$ kubectl run nginx-dev --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

Error from server (Forbidden): pods is forbidden: User "aksdev@contoso.com" cannot create resource "pods" in API group "" in the namespace "sre"
```

### <a name="test-the-sre-access-to-the-aks-cluster-resources"></a>Testowanie dostępu SRE do zasobów klastra AKS

Aby upewnić się, że nasze członkostwo w grupach usługi Azure AD i Kubernetes RBAC działają prawidłowo między różnymi użytkownikami i grupami, wypróbuj poprzednie polecenia podczas logowania jako użytkownik *opssre* .

Zresetuj kontekst *kubeconfig* za pomocą polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] , które czyści poprzednio buforowany token uwierzytelniania dla użytkownika *aksdev* :

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myAKSCluster --overwrite-existing
```

Spróbuj zaplanować i wyświetlić w przypisanej przestrzeni nazw *SRE* . Po wyświetleniu monitu zaloguj się przy użyciu własnych `opssre@contoso.com` poświadczeń utworzonych na początku artykułu:

```console
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre
kubectl get pods --namespace sre
```

Jak pokazano w poniższych przykładowych danych wyjściowych, można pomyślnie utworzyć i wyświetlić zasobniki:

```console
$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace sre

To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BM4RHP3FD to authenticate.

pod/nginx-sre created

$ kubectl get pods --namespace sre

NAME        READY   STATUS    RESTARTS   AGE
nginx-sre   1/1     Running   0
```

Teraz spróbuj wyświetlić lub zaplanować SRE poza przypisaną przestrzenią nazw:

```console
kubectl get pods --all-namespaces
kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
```

`kubectl`Polecenia te kończą się niepowodzeniem, jak pokazano w poniższych przykładowych danych wyjściowych. Członkostwo w grupach użytkowników i rola Kubernetes oraz RoleBindings nie udzielają uprawnień do tworzenia i zarządzania zasobami w innych obszarach nazw:

```console
$ kubectl get pods --all-namespaces
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot list pods at the cluster scope

$ kubectl run nginx-sre --image=mcr.microsoft.com/oss/nginx/nginx:1.15.5-alpine --namespace dev
Error from server (Forbidden): pods is forbidden: User "akssre@contoso.com" cannot create pods in the namespace "dev"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

W tym artykule opisano tworzenie zasobów w klastrze AKS oraz użytkowników i grup w usłudze Azure AD. Aby wyczyścić wszystkie te zasoby, uruchom następujące polecenia:

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

Aby uzyskać więcej informacji na temat zabezpieczania klastrów Kubernetes, zobacz [Opcje dostępu i tożsamości dla AKS)][rbac-authorization].

Najlepsze rozwiązania dotyczące tożsamości i kontroli zasobów można znaleźć [w temacie najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w programie AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubectl-create]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#create
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[kubectl-run]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#run

<!-- LINKS - internal -->
[az-aks-get-credentials]: /cli/azure/aks#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[azure-ad-aks-cli]: azure-ad-integration-cli.md
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-ad-group-create]: /cli/azure/ad/group#az-ad-group-create
[az-role-assignment-create]: /cli/azure/role/assignment#az-role-assignment-create
[az-ad-user-create]: /cli/azure/ad/user#az-ad-user-create
[az-ad-group-member-add]: /cli/azure/ad/group/member#az-ad-group-member-add
[az-ad-group-show]: /cli/azure/ad/group#az-ad-group-show
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
