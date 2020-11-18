---
title: Zarządzanie klastrem usługi Azure Kubernetes Service przy użyciu pulpitu nawigacyjnego sieci Web
description: Dowiedz się, jak za pomocą wbudowanego pulpitu nawigacyjnego interfejsu użytkownika sieci Web Kubernetes zarządzać klastrem usługi Azure Kubernetes Service (AKS)
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: a80082ac524a4777b3b5ee32d946e9db8ec6e7f5
ms.sourcegitcommit: c157b830430f9937a7fa7a3a6666dcb66caa338b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/17/2020
ms.locfileid: "94681622"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Dostęp do pulpitu nawigacyjnego sieci Web Kubernetes w usłudze Azure Kubernetes Service (AKS)

Kubernetes zawiera internetowy pulpit nawigacyjny, który może być używany do podstawowych operacji zarządzania. Ten pulpit nawigacyjny pozwala wyświetlić podstawowy stan kondycji i metryki dla aplikacji, utworzyć i wdrożyć usługi oraz edytować istniejące aplikacje. W tym artykule pokazano, jak uzyskać dostęp do pulpitu nawigacyjnego Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure, a następnie przeprowadzi Cię przez niektóre podstawowe operacje pulpitu nawigacyjnego.

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web Kubernetes][kubernetes-dashboard]. AKS używa wersji 2,0 i większej z pulpitu nawigacyjnego typu open source.

> [!WARNING]
> **Dodatek pulpitu nawigacyjnego AKS jest ustawiony na przestarzałe. Zamiast tego użyj [widoku zasobów Kubernetes w Azure Portal (wersja zapoznawcza)][kubernetes-portal] .** 
> * Pulpit nawigacyjny Kubernetes jest domyślnie włączony w przypadku klastrów z systemem Kubernetes w wersji niższej niż 1,18.
> * Dodatek pulpitu nawigacyjnego zostanie domyślnie wyłączony dla wszystkich nowych klastrów utworzonych w systemie Kubernetes 1,18 lub nowszym. 
 > * Począwszy od Kubernetes 1,19 w wersji zapoznawczej, AKS nie będzie już obsługiwał instalacji zarządzanego dodatku pulpitu nawigacyjnego polecenia. 
 > * Nie wpłynie to na istniejące klastry z włączonym dodatkiem. Użytkownicy będą nadal mogli ręcznie instalować pulpit nawigacyjny "open source" jako oprogramowanie zainstalowane przez użytkownika.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach przedstawionych w tym dokumencie przyjęto założenie, że utworzono klaster AKS i nawiązano `kubectl` połączenie z klastrem. Jeśli musisz utworzyć klaster AKS, zobacz [Szybki Start: Wdrażanie klastra usługi Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure][aks-quickstart].

Konieczne jest również zainstalowanie i skonfigurowanie interfejsu wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Wyłącz pulpit nawigacyjny Kubernetes

Dodatek polecenia-Dashboard jest **domyślnie włączony w klastrach starszych niż K8s 1,18**. Dodatek można wyłączyć, uruchamiając następujące polecenie.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Uruchamianie pulpitu nawigacyjnego Kubernetes

Aby uruchomić Pulpit nawigacyjny Kubernetes w klastrze, użyj polecenia [AZ AKS Browse][az-aks-browse] . To polecenie wymaga instalacji dodatku polecenia-pulpitu nawigacyjnego w klastrze, który jest domyślnie uwzględniony w klastrach korzystających z dowolnej wersji starszej niż Kubernetes 1,18.

W poniższym przykładzie zostanie otwarty pulpit nawigacyjny dla klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *zasobów*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

To polecenie tworzy serwer proxy między systemem deweloperskim i interfejsem API Kubernetes, a następnie otwiera przeglądarkę internetową na pulpicie nawigacyjnym Kubernetes. Jeśli przeglądarka sieci Web nie jest otwarta na pulpicie nawigacyjnym Kubernetes, skopiuj i wklej adres URL zanotowany w interfejsie wiersza polecenia platformy Azure, zazwyczaj `http://127.0.0.1:8001` .

> [!NOTE]
> Jeśli pulpit nawigacyjny nie jest widoczny w programie `http://127.0.0.1:8001` , można ręcznie kierować do poniższych adresów. Klastry w 1,16 lub większe używają protokołu HTTPS i wymagają oddzielnego punktu końcowego.
> * K8s 1,16 lub nowszy: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1,15 i poniżej: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

<!--
![The login page of the Kubernetes web dashboard](./media/kubernetes-dashboard/dashboard-login.png)

You have the following options to sign in to your cluster's dashboard:

* A [kubeconfig file][kubeconfig-file]. You can generate a kubeconfig file using [az aks get-credentials][az-aks-get-credentials].
* A token, such as a [service account token][aks-service-accounts] or user token. On [AAD-enabled clusters][aad-cluster], this token would be an AAD token. You can use `kubectl config view` to list the tokens in your kubeconfig file. For more details on creating an AAD token for use with an AKS cluster see [Integrate Azure Active Directory with Azure Kubernetes Service using the Azure CLI][aad-cluster].
* The default dashboard service account, which is used if you click *Skip*.

> [!WARNING]
> Never expose the Kubernetes dashboard publicly, regardless of the authentication method used.
> 
> When setting up authentication for the Kubernetes dashboard, it is recommended that you use a token over the default dashboard service account. A token allows each user to use their own permissions. Using the default dashboard service account may allow a user to bypass their own permissions and use the service account instead.
> 
> If you do choose to use the default dashboard service account and your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your Kubernetes RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use Kubernetes RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.

> [!IMPORTANT]
> If your AKS cluster uses Kubernetes RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays Kubernetes RBAC access errors. The Kubernetes dashboard does not currently support user-provided credentials to determine the level of access, rather it uses the roles granted to the service account. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
> 
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command. The following example shows how to create a sample binding, however, this sample binding does not apply any additional authentication components and may lead to insecure use. The Kubernetes dashboard is open to anyone with access to the URL. Do not expose the Kubernetes dashboard publicly.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].
-->

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Zaloguj się do pulpitu nawigacyjnego (Kubernetes 1.16 +)

> [!IMPORTANT]
> Począwszy od wersji [1.10.1 pulpitu nawigacyjnego Kubernetes](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) lub Kubernetes v 1.16 + konto usługi "Kubernetes-Dashboard" nie może być już używane do pobierania zasobów ze względu na [poprawkę zabezpieczeń w tej wersji](https://github.com/kubernetes/dashboard/pull/3400). W związku z tym żądania bez informacji o uwierzytelnianiu zwracają [nieautoryzowany błąd 401](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998). Token okaziciela pobrany z konta usługi nadal może być używany jako przykład w tym [przykładzie pulpitu nawigacyjnego Kubernetes](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui), ale ma to wpływ na przepływ logowania dodatku pulpitu nawigacyjnego w porównaniu ze starszymi wersjami.
>
>Jeśli w dalszym ciągu jest uruchomiona wersja wcześniejsza niż 1,16, nadal możesz udzielić uprawnień do konta usługi "Kubernetes-Dashboard", ale **nie** jest to zalecane:
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

Przedstawiony ekran początkowy wymaga elementu kubeconfig lub tokenu. Obie opcje wymagają uprawnień zasobów do wyświetlania tych zasobów na pulpicie nawigacyjnym.

![ekran logowania](./media/kubernetes-dashboard/login.png)

**Użyj kubeconfig**

W przypadku klastrów z włączoną obsługą usługi Azure AD i nienależących do usługi Azure AD można przekazywać kubeconfig. Upewnij się, że tokeny dostępu są prawidłowe, jeśli Twoje tokeny wygasły, możesz odświeżać tokeny za pośrednictwem polecenia kubectl.

1. Ustaw kubeconfig administratora z `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Wybierz `Kubeconfig` i kliknij, `Choose kubeconfig file` Aby otworzyć selektor plików
1. Wybierz plik kubeconfig (domyślnie $HOME/.Kube/config)
1. Kliknij pozycję `Sign In`.

**Użyj tokenu**

1. W przypadku **klastra z włączoną obsługą usługi Azure AD** Uruchom `kubectl config view` i skopiuj token skojarzony z kontem użytkownika klastra.
1. Wklej do opcji tokenu podczas logowania.    
1. Kliknij pozycję `Sign In`.

W przypadku klastrów z obsługą usługi Azure AD Pobierz swój token AAD przy użyciu poniższego polecenia. Sprawdź poprawność wymiany grupy zasobów i nazwy klastra w poleceniu.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Po pomyślnym zakończeniu zostanie wyświetlona strona podobna do poniższego.

![Strona przegląd pulpitu nawigacyjnego sieci Web Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Tworzenie aplikacji

Poniższe kroki wymagają, aby użytkownik miał uprawnienia do odpowiednich zasobów. 

Aby zobaczyć, jak pulpit nawigacyjny Kubernetes może zmniejszyć złożoność zadań związanych z zarządzaniem, Utwórz aplikację. Możesz utworzyć aplikację z poziomu pulpitu nawigacyjnego Kubernetes, dostarczając tekst wejściowy, plik YAML lub za pośrednictwem graficznego kreatora.

Aby utworzyć aplikację, wykonaj następujące czynności:

1. Wybierz przycisk **Utwórz** w prawym górnym oknie.
1. Aby użyć Kreatora graficznego, wybierz opcję **utworzenia aplikacji**.
1. Podaj nazwę wdrożenia, na przykład *Nginx*
1. Wprowadź nazwę obrazu kontenera, który ma być używany, na przykład *Nginx: 1.15.5*
1. Aby udostępnić port 80 dla ruchu w sieci Web, należy utworzyć usługę Kubernetes. W obszarze **Usługa** wybierz opcję **zewnętrzny**, a następnie wprowadź **80** dla portu i portu docelowego.
1. Gdy wszystko będzie gotowe, wybierz pozycję **Wdróż** , aby utworzyć aplikację.

![Wdrażanie aplikacji na pulpicie nawigacyjnym sieci Web Kubernetes](./media/kubernetes-dashboard/create-app.png)

Aby można było przypisać publiczny zewnętrzny adres IP do usługi Kubernetes, trwa minutę lub dwie. W obszarze rozmiar po lewej stronie w obszarze **odnajdywanie i równoważenie obciążenia** wybierz pozycję **usługi**. Zostanie wyświetlona Usługa aplikacji, łącznie z *zewnętrznymi punktami końcowymi*, jak pokazano w następującym przykładzie:

![Wyświetl listę usług i punktów końcowych](./media/kubernetes-dashboard/view-services.png)

Wybierz adres punktu końcowego, aby otworzyć okno przeglądarki sieci Web na domyślnej stronie NGINX:

![Wyświetlanie domyślnej strony NGINX wdrożonej aplikacji](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Wyświetl informacje pod

Pulpit nawigacyjny Kubernetes może zapewniać Podstawowe metryki monitorowania i informacje dotyczące rozwiązywania problemów, takie jak dzienniki.

Aby wyświetlić więcej informacji o zasobnikach aplikacji, wybierz pozycję **zasobniki** w menu po lewej stronie. Zostanie wyświetlona lista dostępnych zasobników. Wybierz *Nginx* pod, aby wyświetlić informacje, takie jak użycie zasobów:

![Wyświetl informacje pod](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Edytowanie aplikacji

Oprócz tworzenia i wyświetlania aplikacji Pulpit nawigacyjny Kubernetes może służyć do edytowania i aktualizowania wdrożeń aplikacji. Aby zapewnić dodatkową nadmiarowość aplikacji, Zwiększmy liczbę replik NGINX.

Aby edytować wdrożenie:

1. Wybierz pozycję **wdrożenia** w menu po lewej stronie, a następnie wybierz wdrożenie *Nginx* .
1. Wybierz pozycję **Edytuj** w prawym górnym pasku nawigacyjnym.
1. Znajdź `spec.replica` wartość w około 20 wierszach. Aby zwiększyć liczbę replik dla aplikacji, należy zmienić tę wartość z *1* na *3*.
1. Wybierz pozycję **Aktualizuj** , gdy wszystko będzie gotowe.

![Edytowanie wdrożenia w celu zaktualizowania liczby replik](./media/kubernetes-dashboard/edit-deployment.png)

Utworzenie nowych wartości w zestawie replik trwa kilka minut. W menu po lewej stronie wybierz pozycję **zestawy replik**, a następnie wybierz zestaw replik *Nginx* . Lista zasobników odzwierciedla teraz zaktualizowaną liczbę replik, jak pokazano w następujących przykładowych danych wyjściowych:

![Wyświetl informacje o zestawie replik](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web Kubernetes][kubernetes-dashboard].

<!-- LINKS - external -->
[dashboard-authentication]: https://github.com/kubernetes/dashboard/wiki/Access-control
[kubeconfig-file]: https://kubernetes.io/docs/tasks/access-application-cluster/configure-access-multiple-clusters/
[kubectl-create-clusterrolebinding]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#-em-clusterrolebinding-em-
[kubectl-apply]: https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands#apply
[kubernetes-dashboard]: https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/

<!-- LINKS - internal -->
[aad-cluster]: ./azure-ad-integration-cli.md
[aks-quickstart]: ./kubernetes-walkthrough.md
[aks-service-accounts]: ./concepts-identity.md#kubernetes-service-accounts
[az-account-get-access-token]: /cli/azure/account?view=azure-cli-latest#az-account-get-access-token
[az-aks-browse]: /cli/azure/aks#az-aks-browse
[az-aks-get-credentials]: /cli/azure/aks?view=azure-cli-latest#az-aks-get-credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
