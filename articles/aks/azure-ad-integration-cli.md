---
title: Integracja Azure Active Directory z Azure Kubernetes Service (starsza wersja)
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure Azure Active Directory klastra Azure Kubernetes Service (starsza wersja) z włączoną obsługą usługi Azure Kubernetes Service (AKS)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: cb92f84560a88d406f0d519459c27b5d916ec5ad
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107769575"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integrowanie Azure Active Directory z Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure (starsza wersja)

Azure Kubernetes Service (AKS) można skonfigurować do używania usługi Azure Active Directory (AD) do uwierzytelniania użytkowników. W tej konfiguracji możesz zalogować się do klastra usługi AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Operatorzy klastrów mogą również skonfigurować kontrolę dostępu opartą na rolach (RBAC) na platformie Kubernetes na podstawie tożsamości użytkownika lub członkostwa w grupie katalogów.

W tym artykule pokazano, jak utworzyć wymagane składniki usługi Azure AD, a następnie wdrożyć klaster z obsługą usługi Azure AD i utworzyć podstawową rolę Kubernetes w klastrze usługi AKS.

Aby uzyskać kompletny przykładowy skrypt używany w tym artykule, zobacz Przykłady interfejsu wiersza polecenia platformy [Azure — integracja usługi AKS z usługą Azure AD.][complete-script]

> [!Important]
> Usługa AKS ma nowe, ulepszone środowisko usługi Azure AD zarządzane przez usługę [AKS,][managed-aad] które nie wymaga zarządzania serwerem ani aplikacją kliencyjną. Jeśli chcesz przeprowadzić migrację, postępuj zgodnie z instrukcjami [podanymi tutaj.][managed-aad-migrate]

## <a name="the-following-limitations-apply"></a>Obowiązują następujące ograniczenia:

- Usługę Azure AD można włączyć tylko w klastrze z włączoną obsługą kontroli RBAC na platformie Kubernetes.
- Starszą integrację usługi Azure AD można włączyć tylko podczas tworzenia klastra.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Przejdź do [https://shell.azure.com](https://shell.azure.com) strony , Cloud Shell w przeglądarce.

Aby zapewnić spójność i ułatwić uruchamianie poleceń w tym artykule, utwórz zmienną dla żądanej nazwy klastra usługi AKS. W poniższym przykładzie użyto nazwy *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Omówienie uwierzytelniania usługi Azure AD

Uwierzytelnianie usługi Azure AD jest udostępniane klastrom usługi AKS za OpenID Connect. OpenID Connect to warstwa tożsamości zbudowana na podstawie protokołu OAuth 2.0. Aby uzyskać więcej informacji na OpenID Connect, zobacz [dokumentację programu Open ID Connect.][open-id-connect]

W klastrze Kubernetes do weryfikowania tokenów uwierzytelniania jest używane uwierzytelnianie tokenów uwierzytelniania. Uwierzytelnianie tokenu element webhook jest konfigurowane i zarządzane w ramach klastra usługi AKS. Aby uzyskać więcej informacji na temat uwierzytelniania tokenu dla urządzenia webhook, zobacz dokumentację dotyczącą uwierzytelniania [w przypadku tego urządzenia.][kubernetes-webhook]

> [!NOTE]
> Podczas konfigurowania usługi Azure AD do uwierzytelniania usługi AKS są konfigurowane dwie aplikacje usługi Azure AD. Tę operację musi wykonać administrator dzierżawy platformy Azure.

## <a name="create-azure-ad-server-component"></a>Tworzenie składnika serwera usługi Azure AD

Aby zintegrować usługę z usługą AKS, należy utworzyć aplikację usługi Azure AD, która działa jako punkt końcowy dla żądań tożsamości. Pierwsza potrzebna aplikacja usługi Azure AD pobiera członkostwo w grupie usługi Azure AD dla użytkownika.

Utwórz składnik aplikacji serwera przy użyciu polecenia [az ad app create,][az-ad-app-create] a następnie zaktualizuj oświadczenia członkostwa w grupie przy użyciu [polecenia az ad app update.][az-ad-app-update] W poniższym przykładzie użyto zmiennej *aksname* zdefiniowanej w sekcji [Przed rozpoczęciem](#before-you-begin) i utworzysz zmienną

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Teraz utwórz jednostkę usługi dla aplikacji serwera przy użyciu [polecenia az ad sp create.][az-ad-sp-create] Ta jednostka usługi jest używana do uwierzytelniania się na platformie Azure. Następnie pobierz klucz tajny jednostki usługi za pomocą polecenia [az ad sp credential reset][az-ad-sp-credential-reset] i przypisz go do zmiennej o nazwie *serverApplicationSecret* do użycia w jednym z następujących kroków:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Podmiot zabezpieczeń usługi Azure AD musi mieć uprawnienia do wykonywania następujących akcji:

* Odczyt danych katalogu
* Logowanie i odczyt profilu użytkownika

Przypisz te uprawnienia za pomocą [polecenia az ad app permission add:][az-ad-app-permission-add]

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Na koniec udziel uprawnień przypisanych w poprzednim kroku dla aplikacji serwera przy użyciu polecenia [az ad app permission grant.][az-ad-app-permission-grant] Ten krok kończy się niepowodzeniem, jeśli bieżące konto nie jest administratorem dzierżawy. Należy również dodać uprawnienia dla aplikacji usługi Azure AD w celu żądania informacji, które w przeciwnym razie mogą wymagać zgody administracyjnej, przy użyciu wyrażenia zgody administratora aplikacji [az ad:][az-ad-app-permission-admin-consent]

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Tworzenie składnika klienta usługi Azure AD

Druga aplikacja usługi Azure AD jest używana, gdy użytkownik loguje się do klastra usługi AKS przy użyciu interfejsu wiersza polecenia kubernetes ( `kubectl` ). Ta aplikacja kliency pobiera żądanie uwierzytelnienia od użytkownika i weryfikuje jego poświadczenia i uprawnienia. Utwórz aplikację usługi Azure AD dla składnika klienta za pomocą [polecenia az ad app create:][az-ad-app-create]

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Utwórz jednostkę usługi dla aplikacji klienckiej za pomocą [polecenia az ad sp create:][az-ad-sp-create]

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Pobierz identyfikator oAuth2 dla aplikacji serwera, aby zezwolić na przepływ uwierzytelniania między dwoma składnikami aplikacji przy użyciu [polecenia az ad app show.][az-ad-app-show] Ten identyfikator oAuth2 zostanie użyty w następnym kroku.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Dodaj uprawnienia dla aplikacji klienckiej i składników aplikacji serwera do korzystania z przepływu komunikacji oAuth2 za pomocą [polecenia az ad app permission add.][az-ad-app-permission-add] Następnie przyznaj aplikacji klienckiej uprawnienia do komunikacji z aplikacją serwera za pomocą polecenia [az ad app permission grant:][az-ad-app-permission-grant]

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Wdrażanie klastra

Po utworzeniu dwóch aplikacji usługi Azure AD utwórz sam klaster usługi AKS. Najpierw utwórz grupę zasobów za pomocą [polecenia az group create.][az-group-create] Poniższy przykład tworzy grupę zasobów w regionie *EastUS:*

Utwórz grupę zasobów dla klastra:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Pobierz identyfikator dzierżawy subskrypcji platformy Azure za pomocą [polecenia az account show.][az-account-show] Następnie utwórz klaster usługi AKS za pomocą [polecenia az aks create.][az-aks-create] Polecenie tworzenia klastra usługi AKS udostępnia identyfikatory serwera i aplikacji klienckiej, klucz tajny jednostki usługi aplikacji serwera oraz identyfikator dzierżawy:

```azurecli-interactive
tenantId=$(az account show --query tenantId -o tsv)

az aks create \
    --resource-group myResourceGroup \
    --name $aksname \
    --node-count 1 \
    --generate-ssh-keys \
    --aad-server-app-id $serverApplicationId \
    --aad-server-app-secret $serverApplicationSecret \
    --aad-client-app-id $clientApplicationId \
    --aad-tenant-id $tenantId
```

Na koniec pobierz poświadczenia administratora klastra za pomocą [polecenia az aks get-credentials.][az-aks-get-credentials] W jednym z poniższych kroków uzyskaj  poświadczenia klastra zwykłych użytkowników, aby zobaczyć przepływ uwierzytelniania usługi Azure AD w działaniu.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-kubernetes-rbac-binding"></a>Tworzenie powiązania kontroli RBAC na kubernetes

Aby można było Azure Active Directory z klastrem usługi AKS, należy utworzyć powiązanie roli lub powiązanie roli klastra. *Role* definiują uprawnienia do udzielenia, a *powiązania stosują* je do żądanych użytkowników. Te przypisania można zastosować do danej przestrzeni nazw lub do całego klastra. Aby uzyskać więcej informacji, zobacz [Using Kubernetes RBAC authorization (Używanie autoryzacji RBAC na platformie Kubernetes).][rbac-authorization]

Pobierz główną nazwę użytkownika (UPN) dla aktualnie zalogowanego użytkownika za pomocą polecenia [az ad signed-in-user show.][az-ad-signed-in-user-show] To konto użytkownika zostanie włączone do integracji z usługą Azure AD w następnym kroku.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Jeśli użytkownik, dla których udzielasz powiązania kontroli RBAC platformy Kubernetes, znajduje się w tej samej dzierżawie usługi Azure AD, przypisz uprawnienia na podstawie *parametru userPrincipalName.* Jeśli użytkownik znajduje się w innej dzierżawie usługi Azure AD, zamiast tego użyj właściwości *objectId* i użyj jej.

Utwórz manifest YAML o nazwie `basic-azure-ad-binding.yaml` i wklej następującą zawartość. W ostatnim wierszu zastąp *userPrincipalName_or_objectId*  identyfikatorem UPN lub identyfikatorem obiektu z poprzedniego polecenia:

```yaml
apiVersion: rbac.authorization.k8s.io/v1
kind: ClusterRoleBinding
metadata:
  name: contoso-cluster-admins
roleRef:
  apiGroup: rbac.authorization.k8s.io
  kind: ClusterRole
  name: cluster-admin
subjects:
- apiGroup: rbac.authorization.k8s.io
  kind: User
  name: userPrincipalName_or_objectId
```

Utwórz klaster ClusterRoleBinding przy użyciu [polecenia kubectl apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Uzyskiwanie dostępu do klastra za pomocą usługi Azure AD

Teraz przetestujmy integrację uwierzytelniania usługi Azure AD dla klastra usługi AKS. Ustaw kontekst `kubectl` konfiguracji, aby używać zwykłych poświadczeń użytkownika. Ten kontekst przekazuje wszystkie żądania uwierzytelniania z powrotem za pośrednictwem usługi Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Teraz użyj polecenia [kubectl get pods,][kubectl-get] aby wyświetlić zasobniki we wszystkich przestrzeniach nazw:

```console
kubectl get pods --all-namespaces
```

Zostanie wyświetlony monit o zalogowanie się w celu uwierzytelnienia przy użyciu poświadczeń usługi Azure AD przy użyciu przeglądarki internetowej. Po pomyślnym uwierzytelnieniu polecenie wyświetla zasobniki w klastrze usługi AKS, jak pokazano `kubectl` w następujących przykładowych danych wyjściowych:

```console
kubectl get pods --all-namespaces
```

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code BYMK7UXVD to authenticate.

NAMESPACE     NAME                                    READY   STATUS    RESTARTS   AGE
kube-system   coredns-754f947b4-2v75r                 1/1     Running   0          23h
kube-system   coredns-754f947b4-tghwh                 1/1     Running   0          23h
kube-system   coredns-autoscaler-6fcdb7d64-4wkvp      1/1     Running   0          23h
kube-system   heapster-5fb7488d97-t5wzk               2/2     Running   0          23h
kube-system   kube-proxy-2nd5m                        1/1     Running   0          23h
kube-system   kube-svc-redirect-swp9r                 2/2     Running   0          23h
kube-system   kubernetes-dashboard-847bb4ddc6-trt7m   1/1     Running   0          23h
kube-system   metrics-server-7b97f9cd9-btxzz          1/1     Running   0          23h
kube-system   tunnelfront-6ff887cffb-xkfmq            1/1     Running   0          23h
```

Token uwierzytelniania otrzymany dla `kubectl` jest buforowany. Zostanie wznowione logowanie tylko w przypadku wygaśnięcia tokenu lub ponownego utworzenia pliku konfiguracji kubernetes.

Jeśli po pomyślnym zalogowaniu się przy użyciu przeglądarki internetowej zostanie wyświetlony komunikat o błędzie autoryzacji, jak w poniższych przykładowych danych wyjściowych, sprawdź następujące możliwe problemy:

```output
error: You must be logged in to the server (Unauthorized)
```

* Zdefiniowano odpowiedni identyfikator obiektu lub nazwę UPN w zależności od tego, czy konto użytkownika znajduje się w tej samej dzierżawie usługi Azure AD, czy nie.
* Użytkownik nie jest członkiem więcej niż 200 grup.
* Wpis tajny zdefiniowany w rejestracji aplikacji dla serwera odpowiada wartości skonfigurowanej przy użyciu `--aad-server-app-secret`

## <a name="next-steps"></a>Następne kroki

Aby uzyskać kompletny skrypt zawierający polecenia pokazane w tym artykule, zobacz skrypt integracji usługi [Azure AD w przykładowych magazynach usługi AKS.][complete-script]

Aby kontrolować dostęp do zasobów klastra przy użyciu użytkowników i grup usługi Azure AD, zobacz Control [access to cluster resources using Kubernetes role-based][azure-ad-rbac]access control and Azure AD identities in AKS (Kontrola dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach platformy Kubernetes i tożsamości usługi Azure AD w usłudze AKS).

Aby uzyskać więcej informacji na temat zabezpieczania klastrów Kubernetes, zobacz Opcje dostępu [i tożsamości dla usługi AKS).][rbac-authorization]

Aby uzyskać najlepsze rozwiązania dotyczące tożsamości i kontroli zasobów, zobacz [Best practices for authentication and authorization in AKS (Najlepsze rozwiązania dotyczące][operator-best-practices-identity]uwierzytelniania i autoryzacji w u usługi AKS).

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[az-ad-app-create]: /cli/azure/ad/app#az_ad_app_create
[az-ad-app-update]: /cli/azure/ad/app#az_ad_app_update
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az_ad_app_permission_add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az_ad_app_permission_grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az_ad_app_permission_admin_consent
[az-ad-app-show]: /cli/azure/ad/app#az_ad_app_show
[az-group-create]: /cli/azure/group#az_group_create
[az-account-show]: /cli/azure/account#az_account_show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az_ad_signed_in_user_show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[rbac-authorization]: concepts-identity.md#kubernetes-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
