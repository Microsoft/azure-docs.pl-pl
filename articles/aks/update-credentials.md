---
title: Resetowanie poświadczeń dla klastra
titleSuffix: Azure Kubernetes Service
description: Dowiedz się, jak zaktualizować lub zresetować jednostkę usługi lub poświadczenia aplikacji usługi AAD dla klastra Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 03/11/2019
ms.openlocfilehash: 0b750eb9af7dfd7bcbada7500b6ef71b015db11f
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767479"
---
# <a name="update-or-rotate-the-credentials-for-azure-kubernetes-service-aks"></a>Aktualizowanie lub obracanie poświadczeń dla Azure Kubernetes Service (AKS)

Klastry usługi AKS utworzone za pomocą jednostki usługi mają jednoroczny czas wygaśnięcia. Po upływie daty wygaśnięcia możesz zresetować poświadczenia, aby rozszerzyć jednostkę usługi o dodatkowy okres. Możesz również zaktualizować lub obrócić poświadczenia w ramach zdefiniowanych zasad zabezpieczeń. W tym artykule szczegółowo opisano sposób aktualizowania tych poświadczeń dla klastra usługi AKS.

Możesz również [zintegrować klaster usługi AKS][aad-integration]z usługą Azure Active Directory i używać go jako dostawcy uwierzytelniania dla klastra. W takim przypadku będziesz mieć jeszcze 2 tożsamości utworzone dla klastra, aplikacji serwera usługi AAD i aplikacji klienckiej usługi AAD, możesz również zresetować te poświadczenia.

Alternatywnie można użyć tożsamości zarządzanej dla uprawnień zamiast jednostki usługi. Tożsamości zarządzane są łatwiejsze do zarządzania niż jednostki usługi i nie wymagają aktualizacji ani rotacji. Aby uzyskać więcej informacji, zobacz [Use managed identities (Korzystanie z tożsamości zarządzanych).](use-managed-identity.md)

## <a name="before-you-begin"></a>Zanim rozpoczniesz

Musisz zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.0.65 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="update-or-create-a-new-service-principal-for-your-aks-cluster"></a>Aktualizowanie lub tworzenie nowej jednostki usługi dla klastra usługi AKS

Jeśli chcesz zaktualizować poświadczenia dla klastra usługi AKS, możesz wybrać jedną z opcji:

* Zaktualizuj poświadczenia dla istniejącej jednostki usługi.
* Utwórz nową jednostkę usługi i zaktualizuj klaster, aby używać tych nowych poświadczeń. 

> [!WARNING]
> Jeśli zdecydujesz się  utworzyć nową jednostkę usługi, zaktualizowanie dużego klastra usługi AKS w celu użycia tych poświadczeń może zająć dużo czasu.

### <a name="check-the-expiration-date-of-your-service-principal"></a>Sprawdzanie daty wygaśnięcia jednostki usługi

Aby sprawdzić datę wygaśnięcia jednostki usługi, użyj [polecenia az ad sp credential list.][az-ad-sp-credential-list] Poniższy przykład pobiera identyfikator jednostki usługi dla klastra o nazwie *myAKSCluster* w grupie zasobów *myResourceGroup* za pomocą [polecenia az aks show.][az-aks-show] Identyfikator jednostki usługi jest ustawiany jako zmienna o nazwie *SP_ID* do użycia z [poleceniem az ad sp credential list.][az-ad-sp-credential-list]

```azurecli
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
az ad sp credential list --id $SP_ID --query "[].endDate" -o tsv
```

### <a name="reset-the-existing-service-principal-credential"></a>Resetowanie istniejącego poświadczenia jednostki usługi

Aby zaktualizować poświadczenia dla istniejącej jednostki usługi, pobierz identyfikator jednostki usługi klastra za pomocą [polecenia az aks show.][az-aks-show] Poniższy przykład pobiera identyfikator klastra o nazwie *myAKSCluster* w grupie zasobów *myResourceGroup.* Identyfikator jednostki usługi jest ustawiany jako zmienna o *nazwie SP_ID* do użycia w dodatkowym poleceniu. Te polecenia używają składni powłoki Bash.

> [!WARNING]
> Podczas resetowania poświadczeń klastra w klastrze usługi AKS, [][node-image-upgrade] który używa usługi Azure Virtual Machine Scale Sets, jest wykonywane uaktualnienie obrazu węzła w celu zaktualizowania węzłów przy użyciu nowych informacji o poświadczeniach.

```azurecli-interactive
SP_ID=$(az aks show --resource-group myResourceGroup --name myAKSCluster \
    --query servicePrincipalProfile.clientId -o tsv)
```

Przy użyciu zestawu zmiennych zawierającego identyfikator jednostki usługi zresetuj poświadczenia za pomocą [narzędzia az ad sp credential reset][az-ad-sp-credential-reset]. W poniższym przykładzie platforma Azure może wygenerować nowy bezpieczny klucz tajny dla jednostki usługi. Ten nowy bezpieczny klucz tajny jest również przechowywany jako zmienna.

```azurecli-interactive
SP_SECRET=$(az ad sp credential reset --name $SP_ID --query password -o tsv)
```

Teraz kontynuuj aktualizowanie klastra [usługi AKS przy użyciu nowych poświadczeń jednostki usługi.](#update-aks-cluster-with-new-service-principal-credentials) Ten krok jest niezbędny do odzwierciedlenia zmian jednostki usługi w klastrze usługi AKS.

### <a name="create-a-new-service-principal"></a>Tworzenie nowej jednostki usługi

Jeśli w poprzedniej sekcji wybrano aktualizację istniejących poświadczeń jednostki usługi, pomiń ten krok. Kontynuuj aktualizowanie [klastra usługi AKS przy użyciu nowych poświadczeń jednostki usługi.](#update-aks-cluster-with-new-service-principal-credentials)

Aby utworzyć jednostkę usługi, a następnie zaktualizować klaster usługi AKS w celu użycia tych nowych poświadczeń, użyj polecenia [az ad sp create-for-rbac.][az-ad-sp-create] W poniższym przykładzie parametr `--skip-assignment` zapobiega przypisaniu jakichkolwiek dodatkowych przypisań:

```azurecli-interactive
az ad sp create-for-rbac --skip-assignment
```

Dane wyjściowe będą podobne do poniższego przykładu. Zanotuj własne wartości `appId` i `password`. Te wartości zostaną użyte w następnym kroku.

```json
{
  "appId": "7d837646-b1f3-443d-874c-fd83c7c739c5",
  "name": "7d837646-b1f3-443d-874c-fd83c7c739c",
  "password": "a5ce83c9-9186-426d-9183-614597c7f2f7",
  "tenant": "a4342dc8-cd0e-4742-a467-3129c469d0e5"
}
```

Teraz zdefiniuj zmienne dla identyfikatora jednostki usługi i tajnego klienta przy użyciu danych wyjściowych z własnego polecenia [az ad sp create-for-rbac,][az-ad-sp-create] jak pokazano w poniższym przykładzie. Adres *SP_ID* to *appId,* a SP_SECRET *to* *hasło:*

```console
SP_ID=7d837646-b1f3-443d-874c-fd83c7c739c5
SP_SECRET=a5ce83c9-9186-426d-9183-614597c7f2f7
```

Teraz kontynuuj aktualizowanie klastra [usługi AKS przy użyciu nowych poświadczeń jednostki usługi.](#update-aks-cluster-with-new-service-principal-credentials) Ten krok jest niezbędny, aby zmiany jednostki usługi odzwierciedlić w klastrze usługi AKS.

## <a name="update-aks-cluster-with-new-service-principal-credentials"></a>Aktualizowanie klastra usługi AKS przy użyciu nowych poświadczeń jednostki usługi

> [!IMPORTANT]
> W przypadku dużych klastrów aktualizowanie klastra usługi AKS przy użyciu nowej jednostki usługi może zająć dużo czasu.

Niezależnie od tego, czy wybrano zaktualizowanie poświadczeń dla istniejącej jednostki usługi, czy utworzenie jednostki usługi, teraz zaktualizujesz klaster usługi AKS przy użyciu nowych poświadczeń za pomocą polecenia [az aks update-credentials.][az-aks-update-credentials] Używane są zmienne *--service-principal* *i --client-secret:*

```azurecli-interactive
az aks update-credentials \
    --resource-group myResourceGroup \
    --name myAKSCluster \
    --reset-service-principal \
    --service-principal $SP_ID \
    --client-secret $SP_SECRET
```

W przypadku małych i średnich klastrów zaktualizowanie poświadczeń jednostki usługi w usłudze AKS może potrwać kilka minut.

## <a name="update-aks-cluster-with-new-aad-application-credentials"></a>Aktualizowanie klastra usługi AKS przy użyciu nowych poświadczeń aplikacji usługi AAD

Możesz utworzyć nowe aplikacje klienckie i serwer usługi AAD, korzystając z kroków [integracji usługi AAD.][create-aad-app] Lub zresetuj istniejące aplikacje usługi AAD zgodnie z taką samą metodą jak w [przypadku resetowania jednostki usługi](#reset-the-existing-service-principal-credential). Następnie wystarczy zaktualizować poświadczenia aplikacji usługi AAD klastra przy użyciu tego samego polecenia [az aks update-credentials,][az-aks-update-credentials] ale przy użyciu zmiennych *--reset-aad.*

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

W tym artykule zaktualizowano jednostkę usługi dla samego klastra usługi AKS i aplikacji integracji usługi AAD. Aby uzyskać więcej informacji na temat zarządzania tożsamościami dla obciążeń w klastrze, zobacz [Best practices for authentication and authorization in AKS][best-practices-identity](Najlepsze rozwiązania dotyczące uwierzytelniania i autoryzacji w u usługi AKS).

<!-- LINKS - internal -->
[install-azure-cli]: /cli/azure/install-azure-cli
[az-aks-show]: /cli/azure/aks#az_aks_show
[az-aks-update-credentials]: /cli/azure/aks#az_aks_update_credentials
[best-practices-identity]: operator-best-practices-identity.md
[aad-integration]: ./azure-ad-integration-cli.md
[create-aad-app]: ./azure-ad-integration-cli.md#create-azure-ad-server-component
[az-ad-sp-create]: /cli/azure/ad/sp#az_ad_sp_create_for_rbac
[az-ad-sp-credential-list]: /cli/azure/ad/sp/credential#az_ad_sp_credential_list
[az-ad-sp-credential-reset]: /cli/azure/ad/sp/credential#az_ad_sp_credential_reset
[node-image-upgrade]: ./node-image-upgrade.md
