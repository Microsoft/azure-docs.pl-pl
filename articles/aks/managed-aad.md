---
title: Korzystanie z usługi Azure AD w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać usługi Azure AD w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 08/26/2020
ms.author: thomasge
ms.openlocfilehash: f229075d0bad4f9522e02e30bdabc1d42bb086cf
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94684189"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integracja Azure Active Directory zarządzanej przez AKS

Integracja z usługą Azure AD zarządzaną przez usługę AKS jest przeznaczona do uproszczenia środowiska integracji z usługą Azure AD, w przypadku których użytkownicy wcześniej musieli utworzyć aplikację kliencką, aplikację serwera i wymaganą dzierżawą usługi Azure AD w celu udzielenia uprawnień do odczytu katalogu. W nowej wersji dostawca zasobów AKS zarządza aplikacjami klienta i serwera.

## <a name="azure-ad-authentication-overview"></a>Omówienie uwierzytelniania usługi Azure AD

Administratorzy klastra mogą konfigurować Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) na podstawie tożsamości użytkownika lub członkostwa w grupie katalogów. Uwierzytelnianie usługi Azure AD jest udostępniane Klastrom AKS z OpenID Connect Connect. OpenID Connect Connect to warstwa tożsamości utworzona na podstawie protokołu OAuth 2,0. Aby uzyskać więcej informacji na temat OpenID Connect Connect, zapoznaj [się z dokumentacją dotyczącą otwartych identyfikatorów][open-id-connect].

Więcej informacji o przepływie integracji usługi Azure AD znajduje się w dokumentacji dotyczącej [pojęć dotyczących integracji Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Ograniczenia 

* Nie można wyłączyć integracji usługi Azure AD zarządzanego przez usługę AKS
* klastry z obsługą kontroli RBAC Kubernetes nie są obsługiwane w przypadku integracji z usługą Azure AD zarządzanej przez usługę AKS
* Zmiana dzierżawy usługi Azure AD skojarzonej z integracją usługi Azure AD zarządzanego przez usługę AKS nie jest obsługiwana

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure w wersji 2.11.0 lub nowszej
* Polecenia kubectl z minimalną wersją [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) lub [kubelogin](https://github.com/Azure/kubelogin)
* Jeśli używasz [Helm](https://github.com/helm/helm), minimalna wersja Helm 3,3.

> [!Important]
> Musisz użyć polecenia kubectl z minimalną wersją 1.18.1 lub kubelogin. Jeśli nie używasz poprawnej wersji, zobaczysz problemy z uwierzytelnianiem.

Aby zainstalować polecenia kubectl i kubelogin, użyj następujących poleceń:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Użyj [tych instrukcji](https://kubernetes.io/docs/tasks/tools/install-kubectl/) dla innych systemów operacyjnych.


## <a name="before-you-begin"></a>Zanim rozpoczniesz

W przypadku klastra potrzebna jest grupa usługi Azure AD. Ta grupa jest wymagana jako grupa administratorów dla klastra, aby przyznać uprawnienia administratora klastra. Możesz użyć istniejącej grupy usługi Azure AD lub utworzyć nową. Zapisz identyfikator obiektu grupy usługi Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Aby utworzyć nową grupę usługi Azure AD dla administratorów klastra, użyj następującego polecenia:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Tworzenie klastra AKS z włączoną usługą Azure AD

Utwórz klaster AKS przy użyciu następujących poleceń interfejsu wiersza polecenia.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Utwórz klaster AKS i Włącz dostęp administracyjny do grupy usługi Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Pomyślne utworzenie klastra usługi Azure AD zarządzanego przez usługę AKS ma następującą sekcję w treści odpowiedzi
```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Po utworzeniu klastra można rozpocząć uzyskiwanie do niego dostępu.

## <a name="access-an-azure-ad-enabled-cluster"></a>Dostęp do klastra z obsługą usługi Azure AD

Aby wykonać poniższe kroki, potrzebna jest wbudowana rola [użytkownika klastra usługi Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) .

Uzyskaj poświadczenia użytkownika w celu uzyskania dostępu do klastra:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Postępuj zgodnie z instrukcjami, aby się zalogować.

Aby wyświetlić węzły w klastrze, użyj polecenia polecenia kubectl Get nodes:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Konfigurowanie [kontroli dostępu opartej na rolach (Azure RBAC)](./azure-ad-rbac.md) na platformie Azure w celu skonfigurowania dodatkowych grup zabezpieczeń dla klastrów.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Rozwiązywanie problemów z dostępem za pomocą usługi Azure AD

> [!Important]
> Opisane poniżej kroki służą do pomijania normalnego uwierzytelniania grupy usługi Azure AD. Używaj ich tylko w sytuacji awaryjnej.

Jeśli użytkownik jest trwale zablokowany przez brak dostępu do prawidłowej grupy usługi Azure AD z dostępem do klastra, można nadal uzyskać poświadczenia administratora, aby uzyskać bezpośredni dostęp do klastra.

Aby wykonać te czynności, musisz mieć dostęp do wbudowanej roli [administratora klastra usługi Azure Kubernetes](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Włącz integrację usługi Azure AD zarządzaną przez usługę AKS w istniejącym klastrze

Możesz włączyć integrację usługi Azure AD zarządzaną przez usługę AKS w istniejącym klastrze z obsługą RBAC Kubernetes. Upewnij się, że grupa administratorów ma mieć uprawnienia dostępu do klastra.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Pomyślne uaktywnienie klastra usługi Azure AD zarządzanego przez usługę AKS ma następującą sekcję w treści odpowiedzi

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Pobierz ponownie poświadczenia użytkownika, aby uzyskać dostęp do klastra, wykonując kroki opisane [tutaj][access-cluster].

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Uaktualnianie do integracji z usługą Azure AD zarządzanego przez usługę AKS

Jeśli klaster korzysta ze starszej integracji usługi Azure AD, możesz uaktualnić program do integracji z usługą Azure AD AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Pomyślne Migrowanie klastra usługi Azure AD zarządzanego przez usługę AKS ma następującą sekcję w treści odpowiedzi

```output
"AADProfile": {
    "adminGroupObjectIds": [
      "5d24****-****-****-****-****afa27aed"
    ],
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Jeśli chcesz uzyskać dostęp do klastra, wykonaj kroki opisane [tutaj][access-cluster].

## <a name="non-interactive-sign-in-with-kubelogin"></a>Logowanie nieinterakcyjne za pomocą kubelogin

Istnieją nieinteraktywne scenariusze, takie jak potoki ciągłej integracji, które nie są obecnie dostępne w polecenia kubectl. Możesz użyć, [`kubelogin`](https://github.com/Azure/kubelogin) Aby uzyskać dostęp do klastra przy użyciu logowania jednokrotnego podmiotu zabezpieczeń usługi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [integracji z usługą Azure RBAC na potrzeby autoryzacji Kubernetes][azure-rbac-integration]
* Poznaj [integrację usługi Azure AD z usługą KUBERNETES RBAC][azure-ad-rbac].
* Użyj [kubelogin](https://github.com/Azure/kubelogin) , aby uzyskać dostęp do funkcji uwierzytelniania platformy Azure, które nie są dostępne w polecenia kubectl.
* Dowiedz się więcej o [pojęciach dotyczących tożsamości AKS i Kubernetes][aks-concepts-identity].
* Użyj [szablonów Azure Resource Manager (ARM) ][aks-arm-template] do tworzenia klastrów z obsługą usługi Azure AD, które są zarządzane przez AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters

<!-- LINKS - Internal -->
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks?view=azure-cli-latest#az-aks-create
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[az-group-create]: /cli/azure/group#az-group-create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az-ad-user-show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
