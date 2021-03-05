---
title: Korzystanie z usługi Azure AD w usłudze Azure Kubernetes Service
description: Dowiedz się, jak używać usługi Azure AD w usłudze Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 02/1/2021
ms.author: miwithro
ms.openlocfilehash: 070b2e3b88c1ff20b52d2c5cade7ad42c6d4d734
ms.sourcegitcommit: dac05f662ac353c1c7c5294399fca2a99b4f89c8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102122429"
---
# <a name="aks-managed-azure-active-directory-integration"></a>Integracja Azure Active Directory zarządzanej przez AKS

Integracja z usługą Azure AD zarządzaną przez usługę AKS jest przeznaczona do uproszczenia środowiska integracji z usługą Azure AD, w przypadku których użytkownicy wcześniej musieli utworzyć aplikację kliencką, aplikację serwera i wymaganą dzierżawą usługi Azure AD w celu udzielenia uprawnień do odczytu katalogu. W nowej wersji dostawca zasobów AKS zarządza aplikacjami klienta i serwera.

## <a name="azure-ad-authentication-overview"></a>Omówienie uwierzytelniania usługi Azure AD

Administratorzy klastra mogą konfigurować Kubernetes kontroli dostępu opartej na rolach (Kubernetes RBAC) na podstawie tożsamości użytkownika lub członkostwa w grupie katalogów. Uwierzytelnianie usługi Azure AD jest udostępniane Klastrom AKS z OpenID Connect Connect. OpenID Connect Connect to warstwa tożsamości utworzona na podstawie protokołu OAuth 2,0. Aby uzyskać więcej informacji na temat OpenID Connect Connect, zapoznaj [się z dokumentacją dotyczącą otwartych identyfikatorów][open-id-connect].

Więcej informacji o przepływie integracji usługi Azure AD znajduje się w dokumentacji dotyczącej [pojęć dotyczących integracji Azure Active Directory](concepts-identity.md#azure-active-directory-integration).

## <a name="limitations"></a>Ograniczenia 

* Nie można wyłączyć integracji usługi Azure AD zarządzanego przez usługę AKS
* Zmiana zintegrowanego klastra usługi Azure AD zarządzanego przez usługę AKS na starszą wersję AAD nie jest obsługiwana
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

## <a name="use-conditional-access-with-azure-ad-and-aks"></a>Korzystanie z dostępu warunkowego w usłudze Azure AD i AKS

Podczas integrowania usługi Azure AD z klastrem AKS można także korzystać z [dostępu warunkowego][aad-conditional-access] w celu kontrolowania dostępu do klastra.

> [!NOTE]
> Dostęp warunkowy usługi Azure AD jest możliwością Azure AD — wersja Premium.

Aby utworzyć przykładowe zasady dostępu warunkowego, które mają być używane z usługą AKS, wykonaj następujące czynności:

1. W górnej części Azure Portal Wyszukaj i wybierz pozycję Azure Active Directory.
1. W menu dla Azure Active Directory po lewej stronie wybierz pozycję *aplikacje dla przedsiębiorstw*.
1. W menu dla aplikacji dla przedsiębiorstw po lewej stronie wybierz pozycję *dostęp warunkowy*.
1. W menu dla dostępu warunkowego po lewej stronie, wybierz pozycję *zasady* , a następnie *nowe zasady*.
    :::image type="content" source="./media/managed-aad/conditional-access-new-policy.png" alt-text="Dodawanie zasad dostępu warunkowego":::
1. Wprowadź nazwę zasad, takich jak *AKS-Policy*.
1. Wybierz pozycję *Użytkownicy i grupy*, a następnie w obszarze *dołączanie* wybierz *pozycję Wybierz użytkowników i grupy*. Wybierz użytkowników i grupy, w których chcesz zastosować zasady. Na potrzeby tego przykładu wybierz grupę usługi Azure AD, która ma dostęp administracyjny do klastra.
    :::image type="content" source="./media/managed-aad/conditional-access-users-groups.png" alt-text="Wybieranie użytkowników lub grup w celu zastosowania zasad dostępu warunkowego":::
1. Wybierz pozycję *aplikacje w chmurze lub akcje*, a następnie w obszarze *dołączanie* wybierz pozycję *Wybierz aplikacje*. Wyszukaj *usługę Azure Kubernetes* i wybierz pozycję *Azure Kubernetes Service AAD Server*.
    :::image type="content" source="./media/managed-aad/conditional-access-apps.png" alt-text="Wybieranie serwera usługi AD Kubernetes w celu zastosowania zasad dostępu warunkowego":::
1. W obszarze *Kontrole dostępu* wybierz pozycję *Udziel*. Wybierz pozycję *Udziel dostępu* i *Wymagaj, aby urządzenie było oznaczone jako zgodne*.
    :::image type="content" source="./media/managed-aad/conditional-access-grant-compliant.png" alt-text="Wybieranie, aby zezwalać tylko na zgodne urządzenia dla zasad dostępu warunkowego":::
1. W obszarze *Włączanie zasad* wybierz pozycję *włączone* i *Utwórz*.
    :::image type="content" source="./media/managed-aad/conditional-access-enable-policy.png" alt-text="Włączanie zasad dostępu warunkowego":::

Uzyskaj poświadczenia użytkownika, aby uzyskać dostęp do klastra, na przykład:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Postępuj zgodnie z instrukcjami, aby się zalogować.

Użyj `kubectl get nodes` polecenia, aby wyświetlić węzły w klastrze:

```azurecli-interactive
kubectl get nodes
```

Postępuj zgodnie z instrukcjami, aby zalogować się ponownie. Zwróć uwagę na komunikat o błędzie informujący, że logowanie zostało pomyślnie zakończone, ale administrator wymaga, aby urządzenie zażądało dostępu do tego zasobu przez usługę Azure AD.

W Azure Portal przejdź do pozycji Azure Active Directory, wybierz pozycję *aplikacje dla przedsiębiorstw* , a następnie w obszarze *działania* wybierz pozycję *logowania*. Zwróć uwagę na wpis u góry ze *stanem* *Niepowodzenie* i *dostęp warunkowy* *sukcesu*. Wybierz wpis, a następnie wybierz pozycję *dostęp warunkowy* w obszarze *szczegóły*. Zwróć uwagę na to, że zasady dostępu warunkowego są wymienione na liście.

:::image type="content" source="./media/managed-aad/conditional-access-sign-in-activity.png" alt-text="Nieudany wpis logowania ze względu na zasady dostępu warunkowego":::

## <a name="configure-just-in-time-cluster-access-with-azure-ad-and-aks"></a>Konfigurowanie dostępu just in Time do klastra za pomocą usługi Azure AD i AKS

Kolejną opcją dla kontroli dostępu do klastra jest użycie Privileged Identity Management (PIM) dla żądań just-in-Time.

>[!NOTE]
> PIM jest możliwością Azure AD — wersja Premium wymagającą jednostki SKU Premium P2. Więcej informacji o jednostkach SKU usługi Azure AD znajduje się w [przewodniku po cenach][aad-pricing].

Aby zintegrować żądania dostępu just in Time z klastrem AKS przy użyciu integracji usługi Azure AD zarządzanej przez usługę AKS, wykonaj następujące czynności:

1. W górnej części Azure Portal Wyszukaj i wybierz pozycję Azure Active Directory.
1. Zanotuj identyfikator dzierżawy, do którego odnosi się pozostała część tych instrukcji jak `<tenant-id>` :::image type="content" source="./media/managed-aad/jit-get-tenant-id.png" alt-text="w przeglądarce internetowej, Azure Portal ekranie dla Azure Active Directory zostanie wyświetlony z WYRÓŻNIONym identyfikatorem dzierżawy.":::
1. W menu dla Azure Active Directory po lewej stronie, w obszarze *Zarządzaj* Wybieranie *grup* , a następnie kliknij pozycję *Nowa grupa*.
    :::image type="content" source="./media/managed-aad/jit-create-new-group.png" alt-text="Wyświetla ekran Azure Portal grupy Active Directory z wyróżnioną opcją &quot;Nowa grupa&quot;.":::
1. Upewnij się, że wybrano typ grupy *zabezpieczeń* , a następnie wprowadź nazwę grupy, taką jak *myJITGroup*. W obszarze *role usługi Azure AD można przypisać do tej grupy (wersja zapoznawcza)*, wybierz pozycję *tak*. Na koniec wybierz pozycję *Utwórz*.
    :::image type="content" source="./media/managed-aad/jit-new-group-created.png" alt-text="Wyświetla ekran tworzenia nowej grupy Azure Portal.":::
1. Nastąpi powrót do strony *grup* . Wybierz nowo utworzoną grupę i zanotuj identyfikator obiektu, do którego odnosi się pozostała część tych instrukcji jako `<object-id>` .
    :::image type="content" source="./media/managed-aad/jit-get-object-id.png" alt-text="Wyświetla ekran Azure Portal dla grupy po prostu, wyróżnianie identyfikatora obiektu":::
1. Wdróż klaster AKS z integracją usługi Azure AD zarządzanej za pomocą funkcji AKS przy użyciu `<tenant-id>` `<object-id>` wartości i z wcześniejszych:
    ```azurecli-interactive
    az aks create -g myResourceGroup -n myManagedCluster --enable-aad --aad-admin-group-object-ids <object-id> --aad-tenant-id <tenant-id>
    ```
1. Wróć do Azure Portal, w menu dla *działania* po lewej stronie, wybierz pozycję *dostęp uprzywilejowany (wersja zapoznawcza)* i wybierz pozycję *Włącz dostęp uprzywilejowany*.
    :::image type="content" source="./media/managed-aad/jit-enabling-priv-access.png" alt-text="Zostanie wyświetlona strona uprzywilejowany dostęp do Azure Portal (wersja zapoznawcza) z wyróżnioną opcją &quot;Włącz dostęp uprzywilejowany&quot;":::
1. Wybierz pozycję *Dodaj przypisania* , aby rozpocząć udzielanie dostępu.
    :::image type="content" source="./media/managed-aad/jit-add-active-assignment.png" alt-text="Zostanie wyświetlony ekran uprzywilejowany dostęp (wersja zapoznawcza) Azure Portal. Opcja &quot;Dodaj przypisania&quot; została wyróżniona.":::
1. Wybierz rolę *elementu członkowskiego* i wybierz użytkowników i grupy, do których chcesz udzielić dostępu do klastra. Te przypisania można modyfikować w dowolnym momencie przez administratora grupy. Gdy wszystko będzie gotowe do przejścia, wybierz pozycję *dalej*.
    :::image type="content" source="./media/managed-aad/jit-adding-assignment.png" alt-text="Zostanie wyświetlony ekran dodawaj przypisanie do Azure Portal, z przykładowym użytkownikiem wybranym do dodania jako element członkowski. Opcja &quot;Next&quot; została wyróżniona.":::
1. Wybierz typ przypisania *aktywny*, żądany czas trwania i podaj uzasadnienie. Gdy wszystko będzie gotowe, wybierz pozycję *Przypisz*. Aby uzyskać więcej informacji na temat typów przypisań, zobacz [przypisywanie uprawnień do uprzywilejowanej grupy dostępu (wersja zapoznawcza) w Privileged Identity Management][aad-assignments].
    :::image type="content" source="./media/managed-aad/jit-set-active-assignment-details.png" alt-text="Zostanie wyświetlony ekran Ustawienia Dodawanie przypisań Azure Portal. Wybrano typ przypisania &quot;aktywny&quot; i podano przykładowe uzasadnienie. Opcja &quot;Assign&quot; została wyróżniona.":::

Po wykonaniu przypisań Sprawdź, czy dostęp just in Time działa przez dostęp do klastra. Na przykład:

```azurecli-interactive
 az aks get-credentials --resource-group myResourceGroup --name myManagedCluster
```

Postępuj zgodnie z instrukcjami, aby się zalogować.

Użyj `kubectl get nodes` polecenia, aby wyświetlić węzły w klastrze:

```azurecli-interactive
kubectl get nodes
```

Zanotuj wymagania dotyczące uwierzytelniania i postępuj zgodnie z instrukcjami w celu uwierzytelnienia. Jeśli to się powiedzie, powinny pojawić się dane wyjściowe podobne do następujących:

```output
To sign in, use a web browser to open the page https://microsoft.com/devicelogin and enter the code AAAAAAAAA to authenticate.
NAME                                STATUS   ROLES   AGE     VERSION
aks-nodepool1-61156405-vmss000000   Ready    agent   6m36s   v1.18.14
aks-nodepool1-61156405-vmss000001   Ready    agent   6m42s   v1.18.14
aks-nodepool1-61156405-vmss000002   Ready    agent   6m33s   v1.18.14
```

### <a name="troubleshooting"></a>Rozwiązywanie problemów

Jeśli `kubectl get nodes` zwraca błąd podobny do poniższego:

```output
Error from server (Forbidden): nodes is forbidden: User "aaaa11111-11aa-aa11-a1a1-111111aaaaa" cannot list resource "nodes" in API group "" at the cluster scope
```

Upewnij się, że administrator grupy zabezpieczeń przyznał *aktywne* przypisanie do konta.

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
[aad-pricing]: /azure/pricing/details/active-directory

<!-- LINKS - Internal -->
[aad-conditional-access]: ../active-directory/conditional-access/overview.md
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
[aad-assignments]: ../active-directory/privileged-identity-management/groups-assign-member-owner.md#assign-an-owner-or-member-of-a-group
