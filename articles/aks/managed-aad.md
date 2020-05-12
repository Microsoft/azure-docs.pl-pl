---
title: Korzystanie z usługi Azure AD w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać usługi Azure AD w usłudze Azure Kubernetes Service (AKS)
services: container-service
manager: gwallace
ms.topic: article
ms.date: 05/11/2020
ms.openlocfilehash: 67f5f707ad2971551e3c9623dd5c07ad880afcf2
ms.sourcegitcommit: a8ee9717531050115916dfe427f84bd531a92341
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/12/2020
ms.locfileid: "83211147"
---
# <a name="integrate-azure-ad-in-azure-kubernetes-service-preview"></a>Integrowanie usługi Azure AD w usłudze Azure Kubernetes Service (wersja zapoznawcza)

> [!Note]
> Do istniejących klastrów AKS z integracją usługi AAD (Azure Active Directory) nie mają wpływ nowe środowisko AAD zarządzane przez AKS.

Integracja usługi Azure AD z usługą AAD zarządzaną przez AKS jest przeznaczona do uproszczenia środowiska integracji z usługą Azure AD, w której użytkownicy musieli wcześniej utworzyć aplikację kliencką, aplikację serwera i wymaganą dzierżawą usługi Azure AD w celu udzielenia uprawnień do odczytu katalogu. W nowej wersji dostawca zasobów AKS zarządza aplikacjami klienta i serwera.

## <a name="limitations"></a>Ograniczenia

* Obecnie nie jest możliwe uaktualnienie istniejącego klastra zintegrowanego AKS usługi AAD do nowego środowiska usługi AAD zarządzanego przez program AKS.

> [!IMPORTANT]
> Funkcje w wersji zapoznawczej AKS są dostępne w ramach samoobsługowego i samodzielnego wyboru. Wersje zapoznawcze są udostępniane w postaci "AS-IS" i "jako dostępne" i są wykluczone z umów dotyczących poziomu usług i ograniczonej rękojmi. Wersje zapoznawcze AKS są częściowo objęte obsługą klienta w oparciu o optymalny sposób. W związku z tym te funkcje nie są przeznaczone do użytku produkcyjnego. Aby uzyskać więcej informacji, zobacz następujące artykuły pomocy technicznej:
>
> - [Zasady pomocy technicznej AKS](support-policies.md)
> - [Pomoc techniczna platformy Azure — często zadawane pytania](faq.md)

## <a name="before-you-begin"></a>Przed rozpoczęciem

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

Aby zainstalować polecenia kubectl, należy wykonać następujące czynności:

```azurecli
sudo az aks install-cli
kubectl version --client
```

Użyj [tych instrukcji](https://kubernetes.io/docs/tasks/tools/install-kubectl/) dla innych systemów operacyjnych.

> [!CAUTION]
> Po zarejestrowaniu funkcji w ramach subskrypcji nie można obecnie wyrejestrować tej funkcji. Po włączeniu niektórych funkcji w wersji zapoznawczej mogą być stosowane wartości domyślne dla wszystkich klastrów AKS utworzonych później w ramach subskrypcji. Nie włączaj funkcji w wersji zapoznawczej w ramach subskrypcji produkcyjnych. Zamiast tego należy użyć oddzielnej subskrypcji do testowania funkcji w wersji zapoznawczej i zebrania opinii.

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

Teraz można utworzyć klaster AKS przy użyciu następujących poleceń interfejsu wiersza polecenia.

Najpierw utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Następnie utwórz klaster AKS:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad
```
Powyższe polecenie tworzy AKS klaster z trzema węzłami, ale użytkownik, który utworzył klaster, domyślnie nie jest członkiem grupy, która ma dostęp do tego klastra. Ten użytkownik musi utworzyć grupę usługi Azure AD, dodać siebie jako członka grupy, a następnie zaktualizować klaster, jak pokazano poniżej. Postępuj zgodnie z instrukcjami [tutaj](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal)

Po utworzeniu grupy i dodaniu jej do siebie (i innych) jako członka można zaktualizować klaster za pomocą grupy usługi Azure AD za pomocą następującego polecenia

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```
Alternatywnie, jeśli najpierw utworzysz grupę i dodasz członków, możesz włączyć grupę usługi Azure AD w czasie tworzenia przy użyciu następującego polecenia:

```azurecli-interactive
az aks create -g MyResourceGroup -n MyManagedCluster --enable-aad [--aad-admin-group-object-ids <id>] [--aad-tenant-id <id>]
```

Pomyślne utworzenie klastra usługi AAD zarządzanego przez AKS ma następującą sekcję w treści odpowiedzi
```
"Azure ADProfile": {
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
Aby uzyskać poświadczenia administratora w celu uzyskania dostępu do klastra:

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster --admin
```
Teraz użyj polecenia polecenia kubectl Get nodes, aby wyświetlić węzły w klastrze:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```

Aby uzyskać poświadczenia użytkownika w celu uzyskania dostępu do klastra:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name MyManagedCluster
```
Postępuj zgodnie z instrukcjami, aby się zalogować.

Otrzymujesz: **użytkownik musi być zalogowany na serwerze (bez autoryzacji)**

Powyższy użytkownik pobiera błąd, ponieważ użytkownik nie jest częścią grupy, która ma dostęp do klastra.

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej o [Access Control opartych na rolach w usłudze Azure AD][azure-ad-rbac].
* Użyj [kubelogin](https://github.com/Azure/kubelogin) , aby uzyskać dostęp do funkcji uwierzytelniania platformy Azure, które nie są dostępne w polecenia kubectl.

<!-- LINKS - Internal -->
[azure-ad-rbac]: azure-ad-rbac.md
