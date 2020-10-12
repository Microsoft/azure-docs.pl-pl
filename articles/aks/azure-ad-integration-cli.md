---
title: Integracja Azure Active Directory z usługą Azure Kubernetes Service (starsza wersja)
description: Dowiedz się, jak za pomocą interfejsu wiersza polecenia platformy Azure utworzyć klaster usługi Azure Kubernetes Service (AKS) i Azure Active Directory go (starsza wersja)
services: container-service
author: TomGeske
ms.topic: article
ms.date: 07/20/2020
ms.author: thomasge
ms.openlocfilehash: ab25ec5406c75316aaa1ee8efd0192dc0207ad79
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88612422"
---
# <a name="integrate-azure-active-directory-with-azure-kubernetes-service-using-the-azure-cli-legacy"></a>Integrowanie Azure Active Directory z usługą Azure Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure (starsza wersja)

Usługę Azure Kubernetes Service (AKS) można skonfigurować do korzystania z Azure Active Directory (AD) do uwierzytelniania użytkowników. W tej konfiguracji można zalogować się do klastra AKS przy użyciu tokenu uwierzytelniania usługi Azure AD. Operatory klastra mogą również skonfigurować kontrolę dostępu opartą na rolach (RBAC) Kubernetes na podstawie tożsamości użytkownika lub członkostwa w grupie katalogów.

W tym artykule pokazano, jak utworzyć wymagane składniki usługi Azure AD, a następnie wdrożyć klaster z obsługą usługi Azure AD i utworzyć podstawową rolę RBAC w klastrze AKS.

Aby zapoznać się z kompletnym przykładowym skryptem używanym w tym artykule, zobacz [przykłady interfejsu wiersza polecenia platformy Azure — integracja AKS z usługą Azure AD][complete-script].

> [!Important]
> AKS ma nowe Ulepszone środowisko [usługi Azure AD zarządzane przez AKS][managed-aad] , które nie wymaga zarządzania aplikacją serwera lub klienta. Jeśli chcesz przeprowadzić migrację, postępuj zgodnie z instrukcjami znajdującymi się [tutaj][managed-aad-migrate].

## <a name="the-following-limitations-apply"></a>Obowiązują następujące ograniczenia:

- Usługę Azure AD można włączyć tylko w przypadku klastra z włączoną funkcją RBAC.
- Starsza integracja z usługą Azure AD może być włączana tylko podczas tworzenia klastra.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.61 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

Przejdź do [https://shell.azure.com](https://shell.azure.com) okna, aby otworzyć Cloud Shell w przeglądarce.

Aby zapewnić spójność i pomóc w uruchamianiu poleceń w tym artykule, należy utworzyć zmienną dla żądanej nazwy klastra AKS. Poniższy przykład używa nazwy *myakscluster*:

```console
aksname="myakscluster"
```

## <a name="azure-ad-authentication-overview"></a>Omówienie uwierzytelniania usługi Azure AD

Uwierzytelnianie usługi Azure AD jest udostępniane Klastrom AKS z OpenID Connect Connect. OpenID Connect Connect to warstwa tożsamości utworzona na podstawie protokołu OAuth 2,0. Aby uzyskać więcej informacji na temat OpenID Connect Connect, zapoznaj [się z dokumentacją dotyczącą otwartych identyfikatorów][open-id-connect].

W ramach klastra Kubernetes uwierzytelnianie tokenu elementu webhook służy do weryfikowania tokenów uwierzytelniania. Uwierzytelnianie za pomocą tokenu elementu webhook jest konfigurowane i zarządzane w ramach klastra AKS. Aby uzyskać więcej informacji na temat uwierzytelniania tokenu elementu webhook, zobacz [dokumentację uwierzytelniania elementu webhook][kubernetes-webhook].

> [!NOTE]
> Podczas konfigurowania uwierzytelniania usługi Azure AD for AKS są konfigurowane dwie aplikacje usługi Azure AD. Ta operacja musi zostać wykonana przez administratora dzierżawy platformy Azure.

## <a name="create-azure-ad-server-component"></a>Utwórz składnik serwera usługi Azure AD

Aby przeprowadzić integrację z usługą AKS, tworzysz i używasz aplikacji usługi Azure AD, która działa jako punkt końcowy dla żądań tożsamości. Pierwsza aplikacja usługi Azure AD, której potrzebujesz, otrzymuje członkostwo w grupie usługi Azure AD dla użytkownika.

Utwórz składnik aplikacji serwera za pomocą polecenia [AZ AD App Create][az-ad-app-create] , a następnie zaktualizuj oświadczenia członkostwa w grupie za pomocą polecenia [AZ AD App Update][az-ad-app-update] . Poniższy przykład używa zmiennej *aksname* zdefiniowanej w sekcji [przed rozpoczęciem](#before-you-begin) i tworzy zmienną

```azurecli-interactive
# Create the Azure AD application
serverApplicationId=$(az ad app create \
    --display-name "${aksname}Server" \
    --identifier-uris "https://${aksname}Server" \
    --query appId -o tsv)

# Update the application group membership claims
az ad app update --id $serverApplicationId --set groupMembershipClaims=All
```

Teraz Utwórz nazwę główną usługi dla aplikacji serwerowych za pomocą polecenia [AZ AD Sp Create][az-ad-sp-create] . Ta nazwa główna usługi jest używana do samodzielnego uwierzytelnienia w ramach platformy Azure. Następnie należy uzyskać klucz tajny usługi przy użyciu polecenia [AZ AD Sp Credential Reset][az-ad-sp-credential-reset] i przypisać go do zmiennej o nazwie *serverApplicationSecret* do użycia w jednym z następujących kroków:

```azurecli-interactive
# Create a service principal for the Azure AD application
az ad sp create --id $serverApplicationId

# Get the service principal secret
serverApplicationSecret=$(az ad sp credential reset \
    --name $serverApplicationId \
    --credential-description "AKSPassword" \
    --query password -o tsv)
```

Jednostka usługi Azure AD musi mieć uprawnienia do wykonywania następujących czynności:

* Odczyt danych katalogu
* Logowanie i odczyt profilu użytkownika

Przypisz te uprawnienia za pomocą polecenia [AZ AD App uprawnienie Add][az-ad-app-permission-add] :

```azurecli-interactive
az ad app permission add \
    --id $serverApplicationId \
    --api 00000003-0000-0000-c000-000000000000 \
    --api-permissions e1fe6dd8-ba31-4d61-89e7-88639da4683d=Scope 06da0dbc-49e2-44d2-8312-53f166ab848a=Scope 7ab1d382-f21e-4acd-a863-ba3e13f7da61=Role
```

Na koniec Przydziel uprawnienia przypisane w poprzednim kroku dla aplikacji serwera za pomocą polecenia [AZ AD App Permission Grant][az-ad-app-permission-grant] . Ten krok zakończy się niepowodzeniem, jeśli bieżące konto nie jest administratorem dzierżawy. Musisz również dodać uprawnienia do aplikacji usługi Azure AD, aby zażądać informacji, które w przeciwnym razie wymagają zgody z uprawnieniami administracyjnymi za pomocą polecenia [AZ AD App administrator — wyrażanie zgody][az-ad-app-permission-admin-consent]:

```azurecli-interactive
az ad app permission grant --id $serverApplicationId --api 00000003-0000-0000-c000-000000000000
az ad app permission admin-consent --id  $serverApplicationId
```

## <a name="create-azure-ad-client-component"></a>Tworzenie składnika klienta usługi Azure AD

Druga aplikacja usługi Azure AD jest używana, gdy użytkownik loguje się do klastra AKS przy użyciu interfejsu wiersza polecenia Kubernetes ( `kubectl` ). Ta aplikacja kliencka pobiera żądanie uwierzytelnienia od użytkownika i weryfikuje ich poświadczenia i uprawnienia. Utwórz aplikację usługi Azure AD dla składnika klienta za pomocą polecenia [AZ AD App Create][az-ad-app-create] :

```azurecli-interactive
clientApplicationId=$(az ad app create \
    --display-name "${aksname}Client" \
    --native-app \
    --reply-urls "https://${aksname}Client" \
    --query appId -o tsv)
```

Utwórz nazwę główną usługi dla aplikacji klienckiej za pomocą polecenia [AZ AD Sp Create][az-ad-sp-create] :

```azurecli-interactive
az ad sp create --id $clientApplicationId
```

Uzyskaj identyfikator oAuth2 dla aplikacji serwera, aby umożliwić przepływ uwierzytelniania między dwoma składnikami aplikacji za pomocą polecenia [AZ AD App Show][az-ad-app-show] . Ten identyfikator oAuth2 jest używany w następnym kroku.

```azurecli-interactive
oAuthPermissionId=$(az ad app show --id $serverApplicationId --query "oauth2Permissions[0].id" -o tsv)
```

Dodaj uprawnienia dla składników aplikacji klienta i serwera, aby użyć przepływu komunikacji oAuth2 za pomocą polecenia [AZ AD App uprawnienie Add][az-ad-app-permission-add] . Następnie Udziel uprawnień aplikacji klienckiej do komunikacji z aplikacją serwera za pomocą polecenia [AZ AD App Permission Grant][az-ad-app-permission-grant] :

```azurecli-interactive
az ad app permission add --id $clientApplicationId --api $serverApplicationId --api-permissions ${oAuthPermissionId}=Scope
az ad app permission grant --id $clientApplicationId --api $serverApplicationId
```

## <a name="deploy-the-cluster"></a>Wdróż klaster

Po utworzeniu dwóch aplikacji usługi Azure AD teraz Utwórz klaster AKS. Najpierw utwórz grupę zasobów za pomocą polecenia [AZ Group Create][az-group-create] . Poniższy przykład tworzy grupę zasobów w regionie *wschodnim* :

Utwórz grupę zasobów dla klastra:

```azurecli-interactive
az group create --name myResourceGroup --location EastUS
```

Uzyskaj identyfikator dzierżawy subskrypcji platformy Azure za pomocą polecenia [AZ Account show][az-account-show] . Następnie utwórz klaster AKS przy użyciu polecenia [AZ AKS Create][az-aks-create] . Polecenie utworzenia klastra AKS zapewnia identyfikatory aplikacji serwera i klienta, klucz tajny głównej usługi aplikacji serwera i identyfikator dzierżawy:

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

Na koniec Uzyskaj poświadczenia administratora klastra przy użyciu polecenia [AZ AKS Get-Credentials][az-aks-get-credentials] . W jednym z poniższych kroków otrzymujesz regularne poświadczenia klastra *użytkownika* , aby zobaczyć przepływ uwierzytelniania usługi Azure AD w akcji.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --admin
```

## <a name="create-rbac-binding"></a>Tworzenie powiązania RBAC

Aby można było używać konta Azure Active Directory z klastrem AKS, należy utworzyć powiązanie roli lub powiązania roli klastra. *Role* definiują uprawnienia do udzielenia, a *powiązania* stosują je do wybranych użytkowników. Te przypisania można zastosować do danej przestrzeni nazw lub całego klastra. Aby uzyskać więcej informacji, zobacz [Korzystanie z autoryzacji RBAC][rbac-authorization].

Pobierz główną nazwę użytkownika (UPN) dla użytkownika, który jest aktualnie zalogowany za pomocą polecenia [AZ AD zalogowałd-User show][az-ad-signed-in-user-show] . W następnym kroku dla tego konta użytkownika włączono integrację z usługą Azure AD.

```azurecli-interactive
az ad signed-in-user show --query userPrincipalName -o tsv
```

> [!IMPORTANT]
> Jeśli użytkownik, któremu przyznano powiązanie RBAC, jest w tej samej dzierżawie usługi Azure AD, przypisz uprawnienia na podstawie elementu *userPrincipalName*. Jeśli użytkownik znajduje się w innej dzierżawie usługi Azure AD, zapytaj i Użyj zamiast niego właściwości *objectid* .

Utwórz manifest YAML o nazwie `basic-azure-ad-binding.yaml` i wklej poniższą zawartość. W ostatnim wierszu Zastąp *userPrincipalName_or_objectId*  nazwą UPN lub identyfikatorem obiektu wyjściowym z poprzedniego polecenia:

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

Utwórz ClusterRoleBinding za pomocą polecenia [polecenia kubectl Apply][kubectl-apply] i określ nazwę pliku manifestu YAML:

```console
kubectl apply -f basic-azure-ad-binding.yaml
```

## <a name="access-cluster-with-azure-ad"></a>Dostęp do klastra przy użyciu usługi Azure AD

Teraz Przetestujmy integrację uwierzytelniania usługi Azure AD dla klastra AKS. Ustaw `kubectl` kontekst konfiguracji w celu używania zwykłych poświadczeń użytkownika. Ten kontekst przekazuje wszystkie żądania uwierzytelniania z powrotem za pomocą usługi Azure AD.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name $aksname --overwrite-existing
```

Teraz Użyj [polecenia kubectl GetBinding][kubectl-get] , aby wyświetlić wszystkie przestrzenie nazw:

```console
kubectl get pods --all-namespaces
```

Zostanie wyświetlony monit logowania służący do uwierzytelniania przy użyciu poświadczeń usługi Azure AD przy użyciu przeglądarki sieci Web. Po pomyślnym uwierzytelnieniu `kubectl` polecenie wyświetla zasobniki w KLASTRZE AKS, jak pokazano w następujących przykładowych danych wyjściowych:

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

Token uwierzytelniania odebrany dla `kubectl` jest w pamięci podręcznej. Zostanie wyświetlony monit o zalogowanie się tylko po wygaśnięciu tokenu lub ponownym utworzeniu pliku konfiguracji Kubernetes.

Jeśli zostanie wyświetlony komunikat o błędzie autoryzacji po pomyślnym zalogowaniu się przy użyciu przeglądarki sieci Web, jak w poniższym przykładzie danych wyjściowych, sprawdź następujące możliwe problemy:

```output
error: You must be logged in to the server (Unauthorized)
```

* W zależności od tego, czy konto użytkownika znajduje się w tej samej dzierżawie usługi Azure AD, jest zdefiniowany odpowiedni identyfikator obiektu, czy nazwa UPN.
* Użytkownik nie jest członkiem więcej niż 200 grup.
* Wpis tajny zdefiniowany w rejestracji aplikacji dla serwera jest zgodny z wartością skonfigurowaną przy użyciu `--aad-server-app-secret`

## <a name="next-steps"></a>Następne kroki

Aby zapoznać się z kompletnym skryptem zawierającym polecenia przedstawione w tym artykule, zobacz [skrypt integracji usługi Azure AD w repozytorium przykładów AKS][complete-script].

Aby używać użytkowników i grup usługi Azure AD do kontrolowania dostępu do zasobów klastra, zobacz [Kontrola dostępu do zasobów klastra przy użyciu kontroli dostępu opartej na rolach i tożsamości usługi Azure AD w AKS][azure-ad-rbac].

Aby uzyskać więcej informacji na temat zabezpieczania klastrów Kubernetes, zobacz [Opcje dostępu i tożsamości dla AKS)][rbac-authorization].

Najlepsze rozwiązania dotyczące tożsamości i kontroli zasobów można znaleźć [w temacie najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w programie AKS][operator-best-practices-identity].

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubectl-get]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#get
[complete-script]: https://github.com/Azure-Samples/azure-cli-samples/tree/master/aks/azure-ad-integration/azure-ad-integration.sh

<!-- LINKS - internal -->
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]: ../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[az-ad-app-create]: /cli/azure/ad/app#az-ad-app-create
[az-ad-app-update]: /cli/azure/ad/app#az-ad-app-update
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create
[az-ad-app-permission-add]: /cli/azure/ad/app/permission#az-ad-app-permission-add
[az-ad-app-permission-grant]: /cli/azure/ad/app/permission#az-ad-app-permission-grant
[az-ad-app-permission-admin-consent]: /cli/azure/ad/app/permission#az-ad-app-permission-admin-consent
[az-ad-app-show]: /cli/azure/ad/app#az-ad-app-show
[az-group-create]: /cli/azure/group#az-group-create
[az-account-show]: /cli/azure/account#az-account-show
[az-ad-signed-in-user-show]: /cli/azure/ad/signed-in-user#az-ad-signed-in-user-show
[install-azure-cli]: /cli/azure/install-azure-cli
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[rbac-authorization]: concepts-identity.md#kubernetes-role-based-access-control-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[managed-aad]: managed-aad.md
[managed-aad-migrate]: managed-aad.md#upgrading-to-aks-managed-azure-ad-integration
