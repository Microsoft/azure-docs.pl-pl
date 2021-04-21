---
title: Korzystanie z usługi Azure AD w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać usługi Azure AD w Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 3db9f8d895b4c13b5f969859f422e7b566722ffc
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783075"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integracja aplikacji zarządzanych przez Azure Active Directory AKS

Integracja usługi Azure AD zarządzana przez usługę AKS została zaprojektowana w celu uproszczenia środowiska integracji usługi Azure AD, w którym użytkownicy wcześniej byli zobowiązani do utworzenia aplikacji klienckiej, aplikacji serwera i wymagali od dzierżawy usługi Azure AD przyznania uprawnień do odczytu katalogu. W nowej wersji dostawca zasobów usługi AKS zarządza aplikacjami klienta i serwera.

## <a name="azure-ad-authentication-overview"></a>Omówienie uwierzytelniania usługi Azure AD

Administratorzy klastra mogą skonfigurować kontrolę dostępu opartą na rolach (RBAC) na platformie Kubernetes na podstawie tożsamości użytkownika lub członkostwa w grupie katalogów. Uwierzytelnianie usługi Azure AD jest udostępniane klastrom usługi AKS za OpenID Connect. OpenID Connect to warstwa tożsamości zbudowana na podstawie protokołu OAuth 2.0. Aby uzyskać więcej informacji na OpenID Connect, zobacz [dokumentację programu Open ID Connect.][open-id-connect]

Dowiedz się więcej na temat przepływu integracji z usługą Azure AD [w dokumentacji Azure Active Directory integracji.](concepts-identity.md#azure-ad-integration)

## <a name="limitations"></a>Ograniczenia 

* Nie można wyłączyć integracji usługi Azure AD zarządzanej przez usługę AKS
* Zmiana klastra zintegrowanego usługi Azure AD zarządzanego przez usługę AKS na starszą usługę AAD nie jest obsługiwana
* Klastry bez kontroli RBAC na platformie Kubernetes nie są obsługiwane w przypadku integracji usługi Azure AD zarządzanej przez usługę AKS
* Zmiana dzierżawy usługi Azure AD skojarzonej z integracją usługi Azure AD zarządzaną przez usługę AKS nie jest obsługiwana

## <a name="prerequisites"></a>Wymagania wstępne

* Interfejs wiersza polecenia platformy Azure w wersji 2.11.0 lub nowszej
* Kubectl z minimalną wersją [1.18.1](https://github.com/kubernetes/kubernetes/blob/master/CHANGELOG/CHANGELOG-1.18.md#v1181) lub [kubelogin](https://github.com/Azure/kubelogin)
* Jeśli używasz programu [Helm,](https://github.com/helm/helm)minimalna wersja narzędzia Helm to 3.3.

> [!Important]
> Należy użyć kubectl z minimalną wersją 1.18.1 lub kubelogin. Różnica między wersjami pomocniczym kubernetes i kubectl nie powinna być większa niż wersja 1. Jeśli nie używasz poprawnej wersji, zauważysz problemy z uwierzytelnianiem.

Aby zainstalować kubectl i kubelogin, użyj następujących poleceń:

```azurecli-interactive
sudo az aks install-cli
kubectl version --client
kubelogin --version
```

Użyj [tych instrukcji dla](https://kubernetes.io/docs/tasks/tools/install-kubectl/) innych systemów operacyjnych.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W przypadku klastra potrzebna jest grupa usługi Azure AD. Ta grupa jest potrzebna jako grupa administratorów dla klastra w celu udzielenia uprawnień administratora klastra. Możesz użyć istniejącej grupy usługi Azure AD lub utworzyć nową. Zanotuj identyfikator obiektu grupy usługi Azure AD.

```azurecli-interactive
# List existing groups in the directory
az ad group list --filter "displayname eq '<group-name>'" -o table
```

Aby utworzyć nową grupę usługi Azure AD dla administratorów klastra, użyj następującego polecenia:

```azurecli-interactive
# Create an Azure AD group
az ad group create --display-name myAKSAdminGroup --mail-nickname myAKSAdminGroup
```

## <a name="create-an-aks-cluster-with-azure-ad-enabled"></a>Tworzenie klastra usługi AKS z włączoną usługą Azure AD

Utwórz klaster usługi AKS przy użyciu następujących poleceń interfejsu wiersza polecenia.

Utwórz grupę zasobów platformy Azure:

```azurecli-interactive
# Create an Azure resource group
az group create --name myResourceGroup --location centralus
```

Tworzenie klastra usługi AKS i włączanie dostępu administracyjnego dla grupy usługi Azure AD

```azurecli-interactive
# Create an AKS-managed Azure AD cluster
az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Pomyślne utworzenie klastra usługi Azure AD zarządzanego przez usługę AKS zawiera następującą sekcję w treści odpowiedzi
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

Po utworzeniu klastra możesz rozpocząć uzyskiwanie do niego dostępu.

## <a name="access-an-azure-ad-enabled-cluster"></a>Uzyskiwanie dostępu do klastra z obsługą usługi Azure AD

Aby wykonać poniższe [kroki, Azure Kubernetes Service](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-user-role) wbudowaną rolę użytkownika klastra.

Pobierz poświadczenia użytkownika w celu uzyskania dostępu do klastra:
 
```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```
Postępuj zgodnie z instrukcjami, aby się zalogować.

Użyj polecenia kubectl get nodes, aby wyświetlić węzły w klastrze:

```azurecli-interactive
kubectl get nodes

NAME                       STATUS   ROLES   AGE    VERSION
aks-nodepool1-15306047-0   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-1   Ready    agent   102m   v1.15.10
aks-nodepool1-15306047-2   Ready    agent   102m   v1.15.10
```
Skonfiguruj [kontrolę dostępu opartą na rolach (RBAC)](./azure-ad-rbac.md) platformy Azure, aby skonfigurować dodatkowe grupy zabezpieczeń dla klastrów.

## <a name="troubleshooting-access-issues-with-azure-ad"></a>Rozwiązywanie problemów z dostępem w usłudze Azure AD

> [!Important]
> Poniższe kroki pomijają normalne uwierzytelnianie grupy usługi Azure AD. Należy ich używać tylko w sytuacjach awaryjnych.

Jeśli dostęp do prawidłowej grupy usługi Azure AD z dostępem do klastra zostanie trwale zablokowany, nadal możesz uzyskać poświadczenia administratora, aby uzyskać bezpośredni dostęp do klastra.

Aby wykonać te kroki, musisz mieć dostęp do wbudowanej [Azure Kubernetes Service administratora](../role-based-access-control/built-in-roles.md#azure-kubernetes-service-cluster-admin-role) klastra.

```azurecli-interactive
az aks get-credentials --resource-group myResourceGroup --name myManagedCluster --admin
```

## <a name="enable-aks-managed-azure-ad-integration-on-your-existing-cluster"></a>Włączanie integracji usługi Azure AD zarządzanej przez usługę AKS w istniejącym klastrze

Możesz włączyć integrację usługi Azure AD zarządzaną przez usługę AKS w istniejącym klastrze Z obsługą kontroli RBAC na platformie Kubernetes. Upewnij się, że ustawiono grupę administratorów, aby zachować dostęp do klastra.

```azurecli-interactive
az aks update -g MyResourceGroup -n MyManagedCluster --enable-aad --aad-admin-group-object-ids <id-1> [--aad-tenant-id <id>]
```

Pomyślna aktywacja klastra usługi Azure AD zarządzanego przez usługę AKS zawiera następującą sekcję w treści odpowiedzi

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

Pobierz ponownie poświadczenia użytkownika, aby uzyskać dostęp do klastra, zgodnie z instrukcjami w [tym miejscu.][access-cluster]

## <a name="upgrading-to-aks-managed-azure-ad-integration"></a>Uaktualnianie do integracji usługi Azure AD zarządzanej przez usługę AKS

Jeśli klaster korzysta ze starszej integracji z usługą Azure AD, możesz uaktualnić go do integracji usługi Azure AD zarządzanej przez usługę AKS.

```azurecli-interactive
az aks update -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <id> [--aad-tenant-id <id>]
```

Pomyślna migracja klastra usługi Azure AD zarządzanego przez usługę AKS zawiera następującą sekcję w treści odpowiedzi

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

Jeśli chcesz uzyskać dostęp do klastra, wykonaj kroki opisane [tutaj.][access-cluster]

## <a name="non-interactive-sign-in-with-kubelogin"></a>Logowanie nieinterakcyjne przy użyciu usługi kubelogin

Istnieją pewne nieinterakcyjne scenariusze, takie jak potoki ciągłej integracji, które nie są obecnie dostępne w przypadku narzędzia kubectl. Za pomocą funkcji można uzyskać dostęp do klastra za pomocą logowania jednostki usługi [`kubelogin`](https://github.com/Azure/kubelogin) nieinterakcyjnej.

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Używanie dostępu warunkowego z usługami Azure AD i AKS

Podczas integrowania usługi Azure AD z klastrem [][aad-conditional-access] usługi AKS można również kontrolować dostęp do klastra za pomocą dostępu warunkowego.

> [!NOTE]
> Dostęp warunkowy usługi Azure AD to Azure AD — wersja Premium możliwości.

Aby utworzyć przykładowe zasady dostępu warunkowego do użycia z usługą AKS, wykonaj następujące kroki:

1. W górnej części strony Azure Portal wyszukaj i wybierz pozycję Azure Active Directory.
1. W menu aplikacji Azure Active Directory po lewej stronie wybierz pozycję *Aplikacje dla przedsiębiorstw.*
1. W menu aplikacji dla przedsiębiorstw po lewej stronie wybierz pozycję *Dostęp warunkowy.*
1. W menu dostępu warunkowego po lewej stronie wybierz pozycję *Zasady, a* następnie *pozycję Nowe zasady.*
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Dodawanie zasad dostępu warunkowego":::
1. Wprowadź nazwę zasad, taką jak *aks-policy.*
1. Wybierz *pozycję Użytkownicy i grupy,* a następnie w obszarze *Uwzględnij* *wybierz pozycję Wybierz użytkowników i grupy.* Wybierz użytkowników i grupy, do których chcesz zastosować zasady. W tym przykładzie wybierz tę samą grupę usługi Azure AD, która ma dostęp administracyjny do klastra.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Wybieranie użytkowników lub grup do zastosowania zasad dostępu warunkowego":::
1. Wybierz *pozycję Aplikacje w chmurze lub akcje*, a następnie w obszarze *Uwzględnij* wybierz pozycję Wybierz *aplikacje.* Wyszukaj pozycję *Azure Kubernetes Service* i wybierz *pozycję Azure Kubernetes Service AAD Server.*
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Wybieranie Azure Kubernetes Service AD Server do stosowania zasad dostępu warunkowego":::
1. W obszarze *Kontrole dostępu* wybierz pozycję *Udziel*. Wybierz *pozycję U przyznaj dostęp,* a następnie pozycję *Wymagaj, aby urządzenie było oznaczone jako zgodne.*
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Wybranie opcji zezwalania tylko na zgodne urządzenia dla zasad dostępu warunkowego":::
1. W *obszarze Włącz zasady* wybierz pozycję *Wł.,* a następnie *pozycję Utwórz.*
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Włączanie zasad dostępu warunkowego":::

Pobierz poświadczenia użytkownika, aby uzyskać dostęp do klastra, na przykład:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Postępuj zgodnie z instrukcjami, aby się zalogować.

Użyj polecenia `kubectl get nodes` , aby wyświetlić węzły w klastrze:

```azurecli-interactive
kubectl get nodes
```

Postępuj zgodnie z instrukcjami, aby zalogować się ponownie. Zwróć uwagę, że jest wyświetlany komunikat o błędzie z informacją, że użytkownik został pomyślnie zalogowany, ale administrator wymaga, aby urządzenie żądające dostępu było zarządzane przez usługę Azure AD w celu uzyskania dostępu do zasobu.

W Azure Portal przejdź do Azure Active Directory, wybierz pozycję Aplikacje  dla przedsiębiorstw, a *następnie* w obszarze Działanie wybierz *pozycję Logowania.* Zwróć uwagę na wpis w  górnej części strony *ze stanem Niepowodzenie* i *dostępem warunkowym* *sukcesu.* Wybierz wpis, a następnie wybierz *pozycję Dostęp warunkowy w* pozycji *Szczegóły.* Zwróć uwagę, że na liście są wyświetlane zasady dostępu warunkowego.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Nieudany wpis logowania z powodu zasad dostępu warunkowego":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Konfigurowanie dostępu just in time do klastra za pomocą usług Azure AD i AKS

Inną opcją kontroli dostępu do klastra jest użycie Privileged Identity Management (PIM) dla żądań just in time.

>[!NOTE]
> Usługa PIM jest Azure AD — wersja Premium wymagającą sku SKU Premium P2. Aby uzyskać więcej informacji na temat jednostki SKU usługi Azure AD, zobacz [przewodnik po cenach][aad-pricing].

Aby zintegrować żądania dostępu just in time z klastrem AKS przy użyciu integracji usługi Azure AD zarządzanej przez usługę AKS, wykonaj następujące kroki:

1. W górnej części strony Azure Portal wyszukaj i wybierz pozycję Azure Active Directory.
1. Zanotuj identyfikator dzierżawy, określany w pozostałych instrukcjach jako W przeglądarce internetowej ekran usługi Azure Portal dla usługi Azure Active Directory jest wyświetlany z wyróżniony identyfikatorem `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="dzierżawy.":::
1. W menu aplikacji Azure Active Directory po lewej stronie w obszarze Zarządzanie *wybierz* pozycję *Grupy,* a następnie pozycję *Nowa grupa.*
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Wyświetla ekran Azure Portal grup usługi Active Directory z wyróżniona opcją &quot;Nowa grupa&quot;.":::
1. Upewnij się, że wybrano opcję *Typ grupy* zabezpieczeń i wprowadź nazwę grupy, taką jak *myJITGroup.* W *obszarze Role usługi Azure AD można przypisać do tej grupy (wersja zapoznawcza)* wybierz pozycję *Tak.* Na koniec wybierz pozycję *Utwórz*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Pokazuje ekran Azure Portal tworzenia nowej grupy.":::
1. Powrócisz do strony *Grupy.* Wybierz nowo utworzoną grupę i zanotuj identyfikator obiektu, który w pozostałych instrukcjach jest określany jako `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Wyświetla Azure Portal ekranu właśnie utworzonej grupy z wyróżnieniem identyfikatora obiektu":::
1. Wdrażanie klastra AKS z integracją usługi Azure AD zarządzaną przez usługę AKS przy użyciu `<tenant-id>` wartości i z wcześniejszych `<object-id>` wersji:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Po powrocie do Azure Portal w menu  Działanie po lewej stronie wybierz pozycję *Dostęp uprzywilejowany (wersja zapoznawcza)* i wybierz pozycję *Włącz dostęp uprzywilejowany.*
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Zostanie Azure Portal strona dostępu uprzywilejowanego (wersja zapoznawcza) aplikacji z wyróżnionem oknie &quot;Włącz dostęp uprzywilejowany&quot;":::
1. Wybierz *pozycję Dodaj przypisania,* aby rozpocząć udzielanie dostępu.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Po Azure Portal zostanie wyświetlony ekran Dostęp uprzywilejowany (wersja zapoznawcza). Opcja &quot;Dodaj przypisania&quot; jest wyróżniona.":::
1. Wybierz rolę członka *, a* następnie wybierz użytkowników i grupy, którym chcesz udzielić dostępu do klastra. Te przypisania mogą być modyfikowane w dowolnym momencie przez administratora grupy. Gdy wszystko będzie gotowe do pracy, wybierz pozycję *Dalej.*
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Zostanie Azure Portal ekran Dodawanie przypisań członkostwa z wybranym przykładowym użytkownikiem, który zostanie dodany jako członek. Opcja &quot;Dalej&quot; jest wyróżniona.":::
1. Wybierz typ przypisania *Aktywny*, żądany czas trwania i podaj uzasadnienie. Gdy wszystko będzie gotowe do kontynuowania, wybierz pozycję *Przypisz*. Aby uzyskać więcej informacji na temat typów przypisań, zobacz Przypisywanie uprawnień do uprzywilejowanej grupy dostępu [(wersja zapoznawcza)][aad-assignments]w Privileged Identity Management .
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Zostanie Azure Portal ekran Dodaj ustawienia przypisań. Wybierany jest typ przypisania &quot;Aktywny&quot; i podane zostało przykładowe uzasadnienie. Opcja &quot;Przypisz&quot; jest wyróżniona.":::

Po przypisaniu sprawdź, czy dostęp just in time działa, korzystając z klastra. Na przykład:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Postępuj zgodnie z instrukcjami, aby się zalogować.

Użyj polecenia `kubectl get nodes` , aby wyświetlić węzły w klastrze:

```azurecli-interactive
kubectl get nodes
```

Zanotuj wymaganie dotyczące uwierzytelniania i postępuj zgodnie z instrukcjami w celu uwierzytelnienia. Jeśli to się powiedzie, powinny zostać wyświetlony dane wyjściowe podobne do następujących:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli `kubectl get nodes` zwraca błąd podobny do następującego:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Upewnij się, że administrator grupy zabezpieczeń przypisał Twoje konto aktywne *przypisanie.*

## <a name="next-steps"></a>Następne kroki

* Dowiedz się więcej [na temat integracji kontroli RBAC platformy Azure dla autoryzacji Kubernetes][azure-rbac-integration]
* Dowiedz się więcej o [integracji usługi Azure AD z kontroli RBAC na platformie Kubernetes.][azure-ad-rbac]
* Użyj [narzędzia kubelogin,](https://github.com/Azure/kubelogin) aby uzyskać dostęp do funkcji uwierzytelniania platformy Azure, które nie są dostępne w usłudze kubectl.
* Dowiedz się więcej o [pojęciach związanych z tożsamościami WKS i Kubernetes.][aks-concepts-identity]
* Użyj [Azure Resource Manager (ARM) do ][aks-arm-template] tworzenia klastrów usługi Azure AD zarządzanych przez usługę AKS.

<!-- LINKS - external -->
[kubernetes-webhook]:https://kubernetes.io/docs/reference/access-authn-authz/authentication/#webhook-token-authentication
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[aks-arm-template]: /azure/templates/microsoft.containerservice/managedclusters
[aad-pricing]: https://azure.microsoft.com/pricing/details/active-directory/

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
[azure-rbac-integration]: manage-azure-rbac.md
[aks-concepts-identity]: concepts-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[az-aks-create]: /cli/azure/aks#az_aks_create
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[az-group-create]: /cli/azure/group#az_group_create
[open-id-connect]:../active-directory/develop/v2-protocols-oidc.md
[az-ad-user-show]: /cli/azure/ad/user#az_ad_user_show
[rbac-authorization]: concepts-identity.md#role-based-access-controls-rbac
[operator-best-practices-identity]: operator-best-practices-identity.md
[azure-ad-rbac]: azure-ad-rbac.md
[azure-ad-cli]: azure-ad-integration-cli.md
[access-cluster]: #access-an-azure-ad-enabled-cluster
[aad-migrate]: #upgrading-to-aks-managed-azure-ad-integration
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
