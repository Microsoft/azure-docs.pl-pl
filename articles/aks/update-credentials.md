---
title: Resetowanie poświadczeń dla klastra
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zaktualizować lub zresetować poświadczenia nazwy głównej usługi lub aplikacji AAD dla klastra usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 91d40a5a738737098b0941bf3e41e4edd515e6df
ms.sourcegitcommit: c8b50a8aa8d9596ee3d4f3905bde94c984fc8aa2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/28/2021
ms.locfileid: "105640485"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aktualizowanie lub obracanie poświadczeń usługi Azure Kubernetes Service (AKS)

Klastry AKS utworzone za pomocą jednostki usługi mają okres ważności jeden rok. Gdy zbliżasz się do daty wygaśnięcia, możesz zresetować poświadczenia, aby zwiększyć jednostkę usługi przez dodatkowy okres czasu. Możesz również zaktualizować lub obrócić poświadczenia w ramach zdefiniowanych zasad zabezpieczeń. W tym artykule szczegółowo opisano, jak zaktualizować te poświadczenia dla klastra AKS.

Możesz również [zintegrować klaster AKS z Azure Active Directory][aad-integration]i używać go jako dostawcy uwierzytelniania dla klastra. W takim przypadku dla klastra zostanie utworzonych 2 więcej tożsamości, aplikacja serwera usługi AAD i aplikacja kliencka usługi AAD mogą również zresetować te poświadczenia.

Alternatywnie można użyć tożsamości zarządzanej w celu uzyskania uprawnień zamiast nazwy głównej usługi. Zarządzane tożsamości są łatwiejsze do zarządzania niż nazwy główne usługi i nie wymagają aktualizacji ani rotacji. Aby uzyskać więcej informacji, zobacz [Korzystanie z tożsamości zarządzanych](use-managed-identity.md).

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Wymagany jest interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aktualizowanie lub tworzenie nowej nazwy głównej usługi dla klastra AKS

Jeśli chcesz zaktualizować poświadczenia dla klastra AKS, możesz wybrać jedną z opcji:

* Zaktualizuj poświadczenia dla istniejącej nazwy głównej usługi.
* Utwórz nową nazwę główną usługi i zaktualizuj klaster tak, aby korzystał z tych nowych poświadczeń. 

> [!WARNING]
> Jeśli zdecydujesz się utworzyć *nową* nazwę główną usługi, aktualizowanie dużego klastra AKS do korzystania z tych poświadczeń może zająć dużo czasu.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Sprawdzanie daty wygaśnięcia nazwy głównej usługi

Aby sprawdzić datę wygaśnięcia nazwy głównej usługi, użyj polecenia [AZ AD Sp Credential list][az-ad-sp-credential-list] . Poniższy przykład pobiera identyfikator jednostki usługi dla klastra o nazwie *myAKSCluster* w grupie zasobów zasobu *zasobów* przy użyciu polecenia [AZ AKS show][az-aks-show] . Identyfikator jednostki usługi jest ustawiany jako zmienna o nazwie *SP_ID* do użycia z poleceniem [AZ AD Sp Credential list][az-ad-sp-credential-list] .

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Zresetuj istniejące poświadczenia jednostki usługi

Aby zaktualizować poświadczenia dla istniejącej nazwy głównej usługi, Pobierz identyfikator jednostki usługi klastra za pomocą polecenia [AZ AKS show][az-aks-show] . Poniższy przykład pobiera identyfikator klastra o nazwie *myAKSCluster* w *grupie zasobów zasobu* . Identyfikator jednostki usługi jest ustawiany jako zmienna o nazwie *SP_ID* do użycia w dodatkowym poleceniu. Te polecenia używają składni bash.

> [!WARNING]
> Po zresetowaniu poświadczeń klastra w klastrze AKS, który korzysta z usługi Azure Virtual Machine Scale Sets, [uaktualnienie obrazu węzła][node-image-upgrade] zostanie wykonane w celu zaktualizowania węzłów przy użyciu nowych informacji o poświadczeniach.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

W przypadku zestawu zmiennych, który zawiera identyfikator jednostki usługi, teraz Zresetuj poświadczenia przy użyciu polecenia [AZ AD Sp Credential Reset][az-ad-sp-credential-reset]. Poniższy przykład umożliwia wygenerowanie nowego bezpiecznego wpisu dla jednostki usługi przez platformę Azure. Ten nowy bezpieczny klucz tajny jest również przechowywany jako zmienna.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Teraz Kontynuuj, aby [zaktualizować klaster AKS przy użyciu nowych poświadczeń jednostki usługi](#update-aks-cluster-with-new-service-principal-credentials). Ten krok jest niezbędny w przypadku zmiany nazwy głównej usługi w klastrze AKS.

### <a name="create-a-new-service-principal"></a>Utwórz nową nazwę główną usługi

Jeśli wybrano opcję zaktualizowania istniejących poświadczeń jednostki usługi w poprzedniej sekcji, Pomiń ten krok. Kontynuuj [Aktualizowanie klastra AKS przy użyciu nowych poświadczeń jednostki usługi](#update-aks-cluster-with-new-service-principal-credentials).

Aby utworzyć nazwę główną usługi, a następnie zaktualizować klaster AKS, aby korzystał z tych nowych poświadczeń, użyj polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create] . W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu jakichkolwiek dodatkowych przypisań:

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

Teraz Zdefiniuj zmienne dla identyfikatora jednostki usługi i wpisu tajnego klienta przy użyciu danych wyjściowych polecenia [AZ AD Sp Create-for-RBAC][az-ad-sp-create] , jak pokazano w poniższym przykładzie. *SP_ID* jest *identyfikatorem aplikacji*, a *SP_SECRET* to *hasło*:

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Teraz Kontynuuj, aby [zaktualizować klaster AKS przy użyciu nowych poświadczeń jednostki usługi](#update-aks-cluster-with-new-service-principal-credentials). Ten krok jest niezbędny w przypadku zmiany nazwy głównej usługi w klastrze AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aktualizowanie klastra AKS przy użyciu nowych poświadczeń jednostki usługi

> [!IMPORTANT]
> W przypadku dużych klastrów aktualizowanie klastra AKS przy użyciu nowej nazwy głównej usługi może zająć dużo czasu.

Niezależnie od tego, czy wybrano aktualizację poświadczeń dla istniejącej nazwy głównej usługi, czy też utworzyć nazwę główną usługi, można teraz zaktualizować klaster AKS przy użyciu nowych poświadczeń za pomocą polecenia [AZ AKS Update-Credentials][az-aks-update-credentials] . Zmienne dla *--Service-Principal* i *--Client-Secret* są używane:

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

W przypadku klastrów małych i średnich trwa kilka minut, aby poświadczenia nazwy głównej usługi zostały zaktualizowane w AKS.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aktualizowanie klastra AKS przy użyciu nowych poświadczeń aplikacji usługi AAD

Nowe aplikacje serwera i klienta usługi AAD można utworzyć, wykonując czynności związane z [integracją usługi AAD][create-aad-app]. Lub zresetuj istniejące aplikacje usługi AAD, postępując zgodnie z [tą samą metodą jak w przypadku resetowania nazwy głównej](#reset-the-existing-service-principal-credential). Po wykonaniu tej opcji wystarczy zaktualizować poświadczenia aplikacji usługi AAD klastra przy użyciu tego samego polecenia [AZ AKS Update-Credentials][az-aks-update-credentials] , ale przy użyciu zmiennych *--Reset-AAD* .

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

W tym artykule opisano nazwę główną usługi dla klastra AKS oraz aplikacje integracji w usłudze AAD. Aby uzyskać więcej informacji na temat zarządzania tożsamością dla obciążeń w klastrze, zobacz [najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w programie AKS][best-practices-identity].

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az-aks-show
[az-aks-update-credentials]: /cli/azure/aks#az-aks-update-credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az-ad-sp-create-for-rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az-ad-sp-credential-list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az-ad-sp-credential-reset
[node-image-upgrade]: ./node-image-upgrade.md
