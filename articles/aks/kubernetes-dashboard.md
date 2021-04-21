---
title: Zarządzanie klastrem Azure Kubernetes Service za pomocą internetowego pulpitu nawigacyjnego
description: Dowiedz się, jak zarządzać klastrem usługi Azure Kubernetes Service (AKS) za pomocą wbudowanego pulpitu nawigacyjnego internetowego interfejsu użytkownika Azure Kubernetes Service Kubernetes
services: container-service
author: mlearned
ms.topic: article
ms.date: 06/03/2020
ms.author: mlearned
ms.openlocfilehash: 0d872a60c4aea89e621fe25ade45697244a74fa8
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107779727"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Uzyskiwanie dostępu do internetowego pulpitu nawigacyjnego rozwiązania Kubernetes w Azure Kubernetes Service (AKS)

Na platformie Kubernetes znajduje się internetowy pulpit nawigacyjny, który może służyć do podstawowych operacji zarządzania. Ten pulpit nawigacyjny umożliwia wyświetlanie podstawowego stanu kondycji i metryk dla aplikacji, tworzenie i wdrażanie usług oraz edytowanie istniejących aplikacji. W tym artykule pokazano, jak uzyskać dostęp do pulpitu nawigacyjnego platformy Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure, a następnie przedstawiono podstawowe operacje pulpitu nawigacyjnego.

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego rozwiązania Kubernetes, zobacz [Kubernetes Web UI Dashboard (Pulpit nawigacyjny internetowego interfejsu użytkownika rozwiązania Kubernetes).][kubernetes-dashboard] AKS używa wersji 2.0 i większej pulpitu nawigacyjnego typu open source.

> [!WARNING]
> **Dodatek pulpitu nawigacyjnego usługi AKS został ustawiony do cofania. Zamiast tego [użyj widoku zasobów Kubernetes w Azure Portal (wersja zapoznawcza).][kubernetes-portal]** 
> * Pulpit nawigacyjny kubernetes jest domyślnie włączony dla klastrów z systemem Kubernetes w wersji mniejszej niż 1.18.
> * Dodatek pulpitu nawigacyjnego będzie domyślnie wyłączony dla wszystkich nowych klastrów utworzonych na platformie Kubernetes 1.18 lub nowszej. 
 > * Począwszy od usługi Kubernetes 1.19 w wersji zapoznawczej, usługi AKS nie będą już obsługiwać instalacji zarządzanego dodatku kube-dashboard. 
 > * Nie będzie to miało wpływu na istniejące klastry z włączonym dodatku. Użytkownicy będą nadal mogli ręcznie instalować pulpit nawigacyjny typu open source jako oprogramowanie zainstalowane przez użytkownika.

## <a name="before-you-begin"></a>Zanim rozpoczniesz

W krokach szczegółowo instrukcje opisane w tym dokumencie zakładają, że utworzono klaster usługi AKS i nawiązliśmy połączenie `kubectl` z klastrem. Jeśli musisz utworzyć klaster usługi AKS, zobacz Szybki start: wdrażanie klastra [Azure Kubernetes Service przy użyciu interfejsu wiersza polecenia platformy Azure.][aks-quickstart]

Musisz również zainstalować i skonfigurować interfejs wiersza polecenia platformy Azure w wersji 2.6.0 lub nowszej. Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="disable-the-kubernetes-dashboard"></a>Wyłączanie pulpitu nawigacyjnego rozwiązania Kubernetes

Dodatek kube-dashboard jest domyślnie włączony w klastrach starszych **niż K8s 1.18.** Dodatek można wyłączyć, uruchamiając następujące polecenie.

``` azurecli
az aks disable-addons -g myRG -n myAKScluster -a kube-dashboard
```

## <a name="start-the-kubernetes-dashboard"></a>Uruchamianie pulpitu nawigacyjnego rozwiązania Kubernetes

> [!WARNING]
> Dodatek pulpitu nawigacyjnego usługi AKS jest przestarzały w wersji 1.19 lub starszej. Zamiast tego użyj [widoku zasobów Kubernetes w Azure Portal (wersja zapoznawcza).][kubernetes-portal] 
> * Następujące polecenie spowoduje teraz otwarcie widoku zasobów witryny Azure Portal zamiast pulpitu nawigacyjnego kubernetes dla wersji 1.19 i starszych.

Aby uruchomić pulpit nawigacyjny kubernetes w klastrze, użyj [polecenia az aks browse.][az-aks-browse] To polecenie wymaga zainstalowania dodatku kube-dashboard w klastrze, który jest domyślnie uwzględniany w klastrach z dowolną wersją starszą niż Kubernetes 1.18.

Poniższy przykład otwiera pulpit nawigacyjny klastra o nazwie *myAKSCluster* w grupie zasobów *o nazwie myResourceGroup:*

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

To polecenie tworzy serwer proxy między systemem dewelopera a interfejsem API Kubernetes i otwiera w przeglądarce internetowej pulpit nawigacyjny platformy Kubernetes. Jeśli przeglądarka internetowa nie otworzy się na pulpicie nawigacyjnym platformy Kubernetes, skopiuj i wklej adres URL zanotony w interfejsie wiersza polecenia platformy Azure, zazwyczaj `http://127.0.0.1:8001` .

> [!NOTE]
> Jeśli nie widzisz pulpitu nawigacyjnego na stronie `http://127.0.0.1:8001` , możesz ręcznie wykonać trasę do następujących adresów. Klastry w programie 1.16 lub większym używają protokołu HTTPS i wymagają oddzielnego punktu końcowego.
> * K8s 1.16 lub większy: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/https:kubernetes-dashboard:/proxy`
> * K8s 1.15 i poniższe: `http://127.0.0.1:8001/api/v1/namespaces/kube-system/services/kubernetes-dashboard:/proxy`

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

## <a name="sign-in-to-the-dashboard-kubernetes-116"></a>Logowanie się do pulpitu nawigacyjnego (kubernetes 1.16+)

> [!IMPORTANT]
> Od wersji [1.10.1](https://github.com/kubernetes/dashboard/releases/tag/v1.10.1) pulpitu nawigacyjnego kubernetes lub kubernetes w wersji 1.16 lub wersji 1.16 lub wersji 1.16 nie można już używać konta usługi "kubernetes-dashboard" do pobierania zasobów z powodu poprawki zabezpieczeń w tej [wersji.](https://github.com/kubernetes/dashboard/pull/3400) W związku z tym żądania bez informacji o uwierzytelnieniach zwracają błąd [401 brak autoryzacji](https://github.com/Azure/AKS/issues/1573#issuecomment-703040998). Token okaziciela pobrany z konta usługi nadal może być używany tak jak w tym przykładzie pulpitu nawigacyjnego [Kubernetes,](https://kubernetes.io/docs/tasks/access-application-cluster/web-ui-dashboard/#accessing-the-dashboard-ui)ale ma to wpływ na przepływ logowania dodatku pulpitu nawigacyjnego w porównaniu ze starszymi wersjami.
>
>Jeśli nadal używasz wersji wcześniejszej niż 1.16, nadal możesz udzielić uprawnień do konta usługi "kubernetes-dashboard", ale nie jest to **zalecane:**
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```

Przedstawiony ekran początkowy wymaga pliku kubeconfig lub tokenu. Obie opcje wymagają uprawnień do wyświetlania tych zasobów na pulpicie nawigacyjnym.

![ekran logowania](./media/kubernetes-dashboard/login.png)

**Korzystanie z konfiguracji kubeconfig**

Zarówno w przypadku klastrów z włączoną usługą Azure AD, jak i klastrów bez tej usługi można dołączyć konfigurację kubeconfig. Upewnij się, że tokeny dostępu są prawidłowe. Jeśli tokeny wygasły, możesz odświeżyć tokeny za pomocą interfejsu kubectl.

1. Ustaw konfigurację kubeconfig administratora za pomocą `az aks get-credentials -a --resource-group <RG_NAME> --name <CLUSTER_NAME>`
1. Wybierz `Kubeconfig` i kliknij, `Choose kubeconfig file` aby otworzyć selektor plików
1. Wybierz plik kubeconfig (wartość domyślna to $HOME/.kube/config)
1. Kliknij pozycję `Sign In`.

**Używanie tokenu**

1. W **przypadku klastra bez usługi Azure AD** uruchom i skopiuj token skojarzony z kontem użytkownika `kubectl config view` klastra.
1. Wklej do opcji tokenu podczas logowania.    
1. Kliknij pozycję `Sign In`.

W przypadku klastrów z włączoną usługą Azure AD pobierz token usługi AAD za pomocą następującego polecenia. Sprawdź, czy w poleceniu zastąpiono nazwę grupy zasobów i klastra.

```
## Update <RESOURCE_GROUP and <AKS_NAME> with your input.

kubectl config view -o jsonpath='{.users[?(@.name == "clusterUser_<RESOURCE GROUP>_<AKS_NAME>")].user.auth-provider.config.access-token}'
```

Po pomyślnym zakończeniu zostanie wyświetlona strona podobna do poniższej.

![Strona przeglądu internetowego pulpitu nawigacyjnego rozwiązania Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Tworzenie aplikacji

Poniższe kroki wymagają, aby użytkownik miał uprawnienia do odpowiednich zasobów. 

Aby zobaczyć, jak pulpit nawigacyjny rozwiązania Kubernetes może zmniejszyć złożoność zadań zarządzania, utwórzmy aplikację. Aplikację można utworzyć z pulpitu nawigacyjnego kubernetes, podając tekstowe dane wejściowe, plik YAML lub za pomocą kreatora graficznego.

Aby utworzyć aplikację, wykonaj następujące czynności:

1. Wybierz przycisk **Utwórz** w prawym górnym oknie.
1. Aby użyć kreatora graficznego, wybierz pozycję **Utwórz aplikację.**
1. Podaj nazwę wdrożenia, na przykład *nginx*
1. Wprowadź nazwę obrazu kontenera do użycia, na przykład *nginx:1.15.5*
1. Aby uwidocznić port 80 dla ruchu internetowego, należy utworzyć usługę Kubernetes. W **obszarze Usługa** wybierz pozycję **Zewnętrzny,** a następnie wprowadź wartość **80** dla portu i portu docelowego.
1. Gdy wszystko będzie gotowe, wybierz **pozycję Wd** wdrażaj, aby utworzyć aplikację.

![Wdrażanie aplikacji na internetowym pulpicie nawigacyjnym rozwiązania Kubernetes](./media/kubernetes-dashboard/create-app.png)

Przypisanie publicznego zewnętrznego adresu IP do usługi Kubernetes zajmuje minutę lub dwie. Po lewej stronie w obszarze Odnajdywanie **i równoważenie obciążenia wybierz** pozycję **Usługi.** Zostanie wymieniona usługa aplikacji, łącznie z *zewnętrznymi punktami końcowymi*, jak pokazano w poniższym przykładzie:

![Wyświetlanie listy usług i punktów końcowych](./media/kubernetes-dashboard/view-services.png)

Wybierz adres punktu końcowego, aby otworzyć okno przeglądarki internetowej z domyślną stroną serwera NGINX:

![Wyświetlanie domyślnej strony NGINX wdrożonej aplikacji](./media/kubernetes-dashboard/default-nginx.png)

## <a name="view-pod-information"></a>Wyświetlanie informacji o zasobniku

Pulpit nawigacyjny rozwiązania Kubernetes może dostarczać podstawowe metryki monitorowania i informacje dotyczące rozwiązywania problemów, takie jak dzienniki.

Aby wyświetlić więcej informacji na temat zasobników aplikacji, wybierz pozycję **Zasobniki** w menu po lewej stronie. Zostanie wyświetlona lista dostępnych zasobników. Wybierz zasobnik *nginx,* aby wyświetlić informacje, takie jak zużycie zasobów:

![Wyświetlanie informacji o zasobniku](./media/kubernetes-dashboard/view-pod-info.png)

## <a name="edit-the-application"></a>Edytowanie aplikacji

Oprócz tworzenia i wyświetlania aplikacji pulpit nawigacyjny kubernetes może służyć do edytowania i aktualizowania wdrożeń aplikacji. Aby zapewnić dodatkową nadmiarowość dla aplikacji, zwiększmy liczbę replik NGINX.

Aby edytować wdrożenie:

1. Wybierz **pozycję Wdrożenia** w menu po lewej stronie, a następnie wybierz wdrożenie *nginx.*
1. Wybierz **pozycję Edytuj** na pasku nawigacyjnym w prawym górnym rogu.
1. Znajdź `spec.replica` wartość w pobliżu wiersza 20. Aby zwiększyć liczbę replik dla aplikacji, zmień tę wartość z *1* na *3.*
1. Wybierz **pozycję Aktualizuj,** gdy wszystko będzie gotowe.

![Edytowanie wdrożenia w celu zaktualizowania liczby replik](./media/kubernetes-dashboard/edit-deployment.png)

Tworzenie nowych zasobników w zestawie replik może potrwać kilka minut. W menu po lewej stronie wybierz pozycję **Zestawy replik,** a następnie wybierz *zestaw replik nginx.* Lista zasobników odzwierciedla teraz zaktualizowaną liczbę replik, jak pokazano w następujących przykładowych danych wyjściowych:

![Wyświetlanie informacji o zestawie replik](./media/kubernetes-dashboard/view-replica-set.png)

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego rozwiązania Kubernetes, zobacz Pulpit nawigacyjny internetowego interfejsu użytkownika [rozwiązania Kubernetes.][kubernetes-dashboard]

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
[az-account-get-access-token]: /cli/azure/account#az_account_get-access-token
[az-aks-browse]: /cli/azure/aks#az_aks_browse
[az-aks-get-credentials]: /cli/azure/aks#az_aks_get_credentials
[install-azure-cli]: /cli/azure/install-azure-cli
[kubernetes-portal]: ./kubernetes-portal.md
