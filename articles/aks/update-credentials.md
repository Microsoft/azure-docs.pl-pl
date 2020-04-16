---
title: Resetowanie poświadczeń dla klastra
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zaktualizować lub zresetować dane uwierzytelniające usługi lub poświadczenia aplikacji usługi AAD dla klastra usługi Azure Kubernetes (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 8420771e32aa792aa79a07fdf4362ad0d9b45d48
ms.sourcegitcommit: d6e4eebf663df8adf8efe07deabdc3586616d1e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/15/2020
ms.locfileid: "81392629"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aktualizowanie lub obracanie poświadczeń usługi Azure Kubernetes Service (AKS)

Domyślnie klastry AKS są tworzone z jednostką usługi, która ma jeden rok czasu wygaśnięcia. W pobliżu daty wygaśnięcia można zresetować poświadczenia, aby przedłużyć jednostkę usługi na dodatkowy okres czasu. Można również zaktualizować lub obrócić poświadczenia jako część zdefiniowanych zasad zabezpieczeń. W tym artykule opisano sposób aktualizowania tych poświadczeń dla klastra AKS.

Być może masz również [zintegrowany klaster AKS z usługą Azure Active Directory][aad-integration]i użyj go jako dostawcy uwierzytelniania dla klastra. W takim przypadku będziesz mieć 2 więcej tożsamości utworzonych dla klastra, aplikacji serwera AAD i aplikacji klienta usługi AAD, można również zresetować te poświadczenia.

Alternatywnie można użyć tożsamości zarządzanej dla uprawnień zamiast jednostki usługi. Tożsamości zarządzane są łatwiejsze do zarządzania niż jednostki usługi i nie wymagają aktualizacji ani rotacji. Aby uzyskać więcej informacji, zobacz [Używanie tożsamości zarządzanych](use-managed-identity.md).

## <a name="before-you-begin"></a>Przed rozpoczęciem

Potrzebujesz zainstalowanego i skonfigurowany interfejsu wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie  `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aktualizowanie lub tworzenie nowego podmiotu zabezpieczeń usługi dla klastra AKS

Aby zaktualizować poświadczenia klastra usługi AKS, można wybrać:

* zaktualizować poświadczenia dla istniejącej jednostki usługi używanej przez klaster lub
* utworzyć jednostkę usługi i zaktualizować klaster, aby użyć tych nowych poświadczeń.

### <a name="reset-existing-service-principal-credential"></a>Resetowanie istniejących poświadczeń głównego usługi

Aby zaktualizować poświadczenia dla istniejącego podmiotu usługi, pobierz identyfikator jednostki usługi klastra za pomocą polecenia [az aks show.][az-aks-show] W poniższym przykładzie zostanie o nazwie myAKSCluster w grupie zasobów *myResourceGroup* identyfikator klastra o nazwie *myAKSCluster.* Identyfikator jednostki usługi jest ustawiany jako zmienna o nazwie *SP_ID* do użycia w poleceniach dodatkowych.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Dzięki zestawowi zmiennym, który zawiera identyfikator jednostki usługi, teraz resetuj poświadczenia przy użyciu [resetowania poświadczeń az ad sp.][az-ad-sp-credential-reset] Poniższy przykład umożliwia platformie Azure generowanie nowego bezpiecznego klucza tajnego dla jednostki usługi. Ten nowy bezpieczny klucz tajny jest również przechowywany jako zmienna.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Teraz kontynuuj [aktualizowanie klastra AKS o nowe poświadczenia głównej usługi](#update-aks-cluster-with-new-service-principal-credentials). Ten krok jest niezbędny do zmiany jednostki usługi do refleksji na temat klastra AKS.

### <a name="create-a-new-service-principal"></a>Tworzenie nowego podmiotu usługi

Jeśli w poprzedniej sekcji zostanie wybrana opcja aktualizacji istniejących poświadczeń głównej usługi, pomiń ten krok. Kontynuuj [aktualizowanie klastra AKS przy użyciu nowych poświadczeń głównej usługi](#update-aks-cluster-with-new-service-principal-credentials).

Aby utworzyć jednostkę usługi, a następnie zaktualizować klaster AKS w celu użycia tych nowych poświadczeń, należy użyć polecenia [az ad sp create-for-rbac.][az-ad-sp-create] W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu jakichkolwiek dodatkowych przypisań:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj własne wartości `appId` i `password`. Te wartości są używane w następnym kroku.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Teraz zdefiniuj zmienne dla identyfikatora jednostki usługi i klucza tajnego klienta przy użyciu danych wyjściowych z własnego polecenia [az ad sp create-for-rbac,][az-ad-sp-create] jak pokazano w poniższym przykładzie. *SP_ID* jest *twój appId*, a *SP_SECRET* to *hasło:*

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Teraz kontynuuj [aktualizowanie klastra AKS o nowe poświadczenia głównej usługi](#update-aks-cluster-with-new-service-principal-credentials). Ten krok jest niezbędny do zmiany jednostki usługi do refleksji na temat klastra AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aktualizowanie klastra AKS przy użyciu nowych poświadczeń jednostki usługi

Niezależnie od tego, czy wybrano zaktualizowanie poświadczeń dla istniejącego podmiotu usługi, czy też utworzenie jednostki usługi, klaster AKS jest teraz aktualizowany przy użyciu nowych poświadczeń przy użyciu polecenia [az aks update-credentials.][az-aks-update-credentials] Używane są zmienne dla *--service-principal* i *--client-secret:*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

Aktualizacja poświadczeń jednostki usługi w usłudze AKS zajmuje kilka chwil.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aktualizowanie klastra AKS przy użyciu nowych poświadczeń aplikacji AAD

Możesz utworzyć nowe aplikacje serwera AAD i klienckie, wykonując [kroki integracji usługi AAD][create-aad-app]. Lub zresetować istniejące aplikacje AAD zgodnie z [tą samą metodą, jak w przypadku resetowania głównej usługi](#reset-existing-service-principal-credential). Następnie wystarczy zaktualizować poświadczenia aplikacji AAD klastra przy użyciu tego samego polecenia [az aks update-credentials,][az-aks-update-credentials] ale przy użyciu *zmiennych --reset-aad.*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-aad \
    --aad-server-app-id <SERVER APPLICATION ID> \
    --aad-server-app-secret <SERVER APPLICATION SECRET> \
    --aad-client-app-id <CLIENT APPLICATION ID>
```


## <a name="next-steps"></a>Następne kroki

W tym artykule jednostki usługi dla samego klastra AKS i aplikacji integracji usługi AAD zostały zaktualizowane. Aby uzyskać więcej informacji na temat zarządzania tożsamością obciążeń w klastrze, zobacz [Najważniejsze wskazówki dotyczące uwierzytelniania i autoryzacji w u. AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: azure-ad-integration.md
[create-aad-app]: azure-ad-integration.md#create-the-server-application
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
