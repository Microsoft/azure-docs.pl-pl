---
title: Korzystanie z usługi Azure AD w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać usługi Azure AD w usłudze Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
author: mlearned
ms.topic: article
ms.date: 06/25/2020
ms.author: mlearned
ms.openlocfilehash: f22b79cb8a730fb9c28dd1a208ab672473218b79
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/08/2020
ms.locfileid: "86105952"
---
# <a name="aks-managed-azure-active-directory-integration-preview"></a>Integracja Azure Active Directory zarządzanej przez AKS (wersja zapoznawcza)

> [!NOTE]
> Nowe środowisko usługi Azure AD zarządzane przez usługę AKS nie ma wpływ na istniejące klastry AKS (Azure Kubernetes Service) z integracją z usługą Azure Active Directory (Azure AD).

Integracja z usługą Azure AD zarządzaną przez usługę AKS jest przeznaczona do uproszczenia środowiska integracji z usługą Azure AD, w przypadku których użytkownicy wcześniej musieli utworzyć aplikację kliencką, aplikację serwera i wymaganą dzierżawą usługi Azure AD w celu udzielenia uprawnień do odczytu katalogu. W nowej wersji dostawca zasobów AKS zarządza aplikacjami klienta i serwera.

## <a name="azure-ad-authentication-overview"></a>Omówienie uwierzytelniania usługi Azure AD

Administratorzy klastra mogą konfigurować kontrolę dostępu opartą na rolach (RBAC) Kubernetes na podstawie tożsamości użytkownika lub członkostwa w grupie katalogów. Uwierzytelnianie usługi Azure AD jest udostępniane Klastrom AKS z OpenID Connect Connect. OpenID Connect Connect to warstwa tożsamości utworzona na podstawie protokołu OAuth 2,0. Aby uzyskać więcej informacji na temat OpenID Connect Connect, zapoznaj [się z dokumentacją dotyczącą otwartych identyfikatorów][open-id-connect].

Więcej informacji o przepływie integracji usługi AAD znajduje się w [dokumentacji dotyczącej pojęć dotyczących integracji Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Ograniczenia

* Obecnie nie można uaktualnić istniejącego klastra zintegrowanego z usługą AKS Azure AD do nowego środowiska zarządzania usługą Azure AD AKS.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej AKS są dostępne w ramach samoobsługowego i samodzielnego wyboru. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wykluczone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o optymalny sposób. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz następujące artykuły pomocy technicznej:
>
> - [Zasady pomocy technicznej AKS](support-policies.md)
> - [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

* Znajdź swój identyfikator dzierżawy konta platformy Azure, przechodząc do Azure Portal i wybierając pozycję Azure Active Directory > właściwości > identyfikator katalogu

> [!Important]
> Musisz użyć polecenia kubectl z minimalną wersją 1,18

Wymagane są następujące zasoby:

- Interfejs wiersza polecenia platformy Azure w wersji 2.5.1 lub nowszej
- Rozszerzenie AKS-Preview 0.4.38
- Polecenia kubectl z minimalną wersją [1,18](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1180)

Aby zainstalować/zaktualizować rozszerzenie AKS-Preview lub później, użyj następujących poleceń interfejsu wiersza polecenia platformy Azure:

```azurecli
az extension add --name aks-preview
az extension list
```

```azurecli
az extension update --name aks-preview
az extension list
```

Aby zainstalować polecenia kubectl, użyj następujących poleceń:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Użyj [tych instrukcji](https://kubernetes.io/docs/tasks/tools/install-kubectl/) dla innych systemów operacyjnych.

```azurecli-interactive
az feature register --name AAD-V2 --namespace Microsoft.ContainerService
```

Wyświetlenie stanu jako **zarejestrowanego**może potrwać kilka minut. Stan rejestracji można sprawdzić za pomocą polecenia [AZ Feature list](https://docs.microsoft.com/cli/azure/feature?view=azure-cli-latest#az-feature-list) :

```azurecli-interactive
az feature list -o table --query "[?contains(name, 'Microsoft.ContainerService/AAD-V2')].{Name:name,State:properties.state}"
```

Gdy stan jest wyświetlany jako zarejestrowane, Odśwież rejestrację `Microsoft.ContainerService` dostawcy zasobów przy użyciu polecenia [AZ Provider Register](https://docs.microsoft.com/cli/azure/provider?view=azure-cli-latest#az-provider-register) :

```azurecli-interactive
az provider register --namespace Microsoft.ContainerService
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Tworzenie klastra AKS z włączoną usługą Azure AD

Utwórz klaster AKS przy użyciu następujących poleceń interfejsu wiersza polecenia.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Możesz użyć istniejącej grupy usługi Azure AD lub utworzyć nową. Potrzebujesz identyfikatora obiektu dla grupy usługi Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list
```

Aby utworzyć nową grupę usługi Azure AD dla administratorów klastra, użyj następującego polecenia:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name MyDisplay --mail-nickname MyDisplay
```

Utwórz klaster AKS i Włącz dostęp administracyjny do grupy usługi Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Pomyślne utworzenie klastra usługi Azure AD zarządzanego przez usługę AKS ma następującą sekcję w treści odpowiedzi
```
"AADProfile": {
    "adminGroupObjectIds": null,
    "clientAppId": null,
    "managed": true,
    "serverAppId": null,
    "serverAppSecret": null,
    "tenantId": "72f9****-****-****-****-****d011db47"
  }
```

Klaster jest tworzony w ciągu kilku minut.

## <a name="access-an-azure-ad-enabled-cluster"></a>Dostęp do klastra z obsługą usługi Azure AD

Aby wykonać poniższe kroki, potrzebna jest wbudowana rola [użytkownika klastra usługi Azure Kubernetes](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-user-role) .

Uzyskaj poświadczenia użytkownika w celu uzyskania dostępu do klastra:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
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
Skonfiguruj [Access Control oparte na rolach (RBAC)](https://docs.microsoft.com/azure/aks/azure-ad-rbac) , aby skonfigurować dodatkowe grupy zabezpieczeń dla klastrów.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Rozwiązywanie problemów z dostępem za pomocą usługi Azure AD

> [!Important]
> Opisane poniżej kroki służą do pomijania normalnego uwierzytelniania grupy usługi Azure AD. Używaj ich tylko w sytuacji awaryjnej.

Jeśli użytkownik jest trwale zablokowany przez brak dostępu do prawidłowej grupy usługi Azure AD z dostępem do klastra, można nadal uzyskać poświadczenia administratora, aby uzyskać bezpośredni dostęp do klastra.

Aby wykonać te czynności, musisz mieć dostęp do wbudowanej roli [administratora klastra usługi Azure Kubernetes](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles#azure-kubernetes-service-cluster-admin-role) .

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```

## <a name="non-interactive-sign-in-with-kubelogin"></a>Logowanie nieinterakcyjne za pomocą kubelogin

Istnieją nieinteraktywne scenariusze, takie jak potoki ciągłej integracji, które nie są obecnie dostępne w polecenia kubectl. Możesz użyć, [`kubelogin`](https://github.com/Azure/kubelogin) Aby uzyskać dostęp do klastra przy użyciu logowania jednokrotnego podmiotu zabezpieczeń usługi.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [integracji z usługą Azure RBAC na potrzeby autoryzacji Kubernetes][azure-rbac-integration]
* Poznaj [integrację usługi Azure AD z usługą KUBERNETES RBAC][azure-ad-rbac].
* Użyj [kubelogin](https://github.com/Azure/kubelogin) , aby uzyskać dostęp do funkcji uwierzytelniania platformy Azure, które nie są dostępne w polecenia kubectl.
* Dowiedz się więcej o [pojęciach dotyczących tożsamości AKS i Kubernetes][aks-concepts-identity].
* Użyj [szablonów Azure Resource Manager (ARM)][aks-arm-template] do tworzenia klastrów z obsługą usługi Azure AD, które są zarządzane przez AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: https://docs.microsoft.com/azure/templates/microsoft.containerservice/managedclusters

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

