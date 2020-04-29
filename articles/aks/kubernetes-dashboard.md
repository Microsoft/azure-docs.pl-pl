---
title: Zarządzanie klastrem usługi Azure Kubernetes Service przy użyciu pulpitu nawigacyjnego sieci Web
description: Dowiedz się, jak za pomocą wbudowanego pulpitu nawigacyjnego interfejsu użytkownika sieci Web Kubernetes zarządzać klastrem usługi Azure Kubernetes Service (AKS)
services: container-service
ms.topic: article
ms.date: 10/08/2018
ms.openlocfilehash: 15fcf765be0a754575713eebcdaa7d68e1c299b9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "77595352"
---
# <a name="access-the-kubernetes-web-dashboard-in-azure-kubernetes-service-aks"></a>Dostęp do pulpitu nawigacyjnego sieci Web Kubernetes w usłudze Azure Kubernetes Service (AKS)

Kubernetes zawiera internetowy pulpit nawigacyjny, który może być używany do podstawowych operacji zarządzania. Ten pulpit nawigacyjny pozwala wyświetlić podstawowy stan kondycji i metryki dla aplikacji, utworzyć i wdrożyć usługi oraz edytować istniejące aplikacje. W tym artykule pokazano, jak uzyskać dostęp do pulpitu nawigacyjnego Kubernetes przy użyciu interfejsu wiersza polecenia platformy Azure, a następnie przeprowadzi Cię przez niektóre podstawowe operacje pulpitu nawigacyjnego.

Aby uzyskać więcej informacji na temat pulpitu nawigacyjnego Kubernetes, zobacz [pulpit nawigacyjny interfejsu użytkownika sieci Web Kubernetes][kubernetes-dashboard].

## <a name="before-you-begin"></a>Przed rozpoczęciem

W krokach przedstawionych w tym dokumencie przyjęto założenie, że utworzono klaster AKS i `kubectl` nawiązano połączenie z klastrem. Jeśli musisz utworzyć klaster AKS, zapoznaj się z [przewodnikiem Szybki Start][aks-quickstart]dotyczącym AKS.

Musisz również mieć zainstalowany i skonfigurowany interfejs wiersza polecenia platformy Azure w wersji 2.0.46 lub nowszej. Uruchom polecenie  `az --version` , aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczne będzie przeprowadzenie instalacji lub uaktualnienia, zobacz  [Instalowanie interfejsu wiersza polecenia platformy Azure][install-azure-cli].

## <a name="start-the-kubernetes-dashboard"></a>Uruchamianie pulpitu nawigacyjnego Kubernetes

Aby uruchomić Pulpit nawigacyjny Kubernetes, użyj polecenia [AZ AKS Browse][az-aks-browse] . W poniższym przykładzie zostanie otwarty pulpit nawigacyjny dla klastra o nazwie *myAKSCluster* w grupie zasobów o nazwie Moja *zasobów*:

```azurecli
az aks browse --resource-group myResourceGroup --name myAKSCluster
```

To polecenie tworzy serwer proxy między systemem deweloperskim i interfejsem API Kubernetes, a następnie otwiera przeglądarkę internetową na pulpicie nawigacyjnym Kubernetes. Jeśli przeglądarka sieci Web nie jest otwarta na pulpicie nawigacyjnym Kubernetes, skopiuj i wklej adres URL zanotowany w interfejsie `http://127.0.0.1:8001`wiersza polecenia platformy Azure, zazwyczaj.

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
> If you do choose to use the default dashboard service account and your AKS cluster uses RBAC, a *ClusterRoleBinding* must be created before you can correctly access the dashboard. By default, the Kubernetes dashboard is deployed with minimal read access and displays RBAC access errors. A cluster administrator can choose to grant additional access to the *kubernetes-dashboard* service account, however this can be a vector for privilege escalation. You can also integrate Azure Active Directory authentication to provide a more granular level of access.
>
> To create a binding, use the [kubectl create clusterrolebinding][kubectl-create-clusterrolebinding] command as shown in the following example. **This sample binding does not apply any additional authentication components and may lead to insecure use.**
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> You can now access the Kubernetes dashboard in your RBAC-enabled cluster. To start the Kubernetes dashboard, use the [az aks browse][az-aks-browse] command as detailed in the previous step.
>
> If your cluster does not use RBAC, it is not recommended to create a *ClusterRoleBinding*.
> 
> For more information on using the different authentication methods, see the Kubernetes dashboard wiki on [access controls][dashboard-authentication].

After you choose a method to sign in, the Kubernetes dashboard is displayed. If you chose to use *token* or *skip*, the Kubernetes dashboard will use the permissions of the currently logged in user to access the cluster.
-->

> [!IMPORTANT]
> Jeśli klaster AKS używa RBAC, należy utworzyć *ClusterRoleBinding* , aby można było prawidłowo uzyskać dostęp do pulpitu nawigacyjnego. Domyślnie pulpit nawigacyjny Kubernetes jest wdrażany z minimalnym dostępem do odczytu i wyświetla błędy dostępu RBAC. Pulpit nawigacyjny Kubernetes nie obsługuje obecnie poświadczeń dostarczonych przez użytkownika w celu określenia poziomu dostępu, a nie używa ról przyznanych dla konta usługi. Administrator klastra może zdecydować się na przyznanie dodatkowego dostępu do konta usługi *Kubernetes-pulpitu nawigacyjnego* , jednak może to być wektor dla eskalacji uprawnień. Możesz także zintegrować Azure Active Directory uwierzytelnianie, aby zapewnić bardziej szczegółowy poziom dostępu.
> 
> Aby utworzyć powiązanie, użyj polecenia [polecenia kubectl Create clusterrolebinding][kubectl-create-clusterrolebinding] . Poniższy przykład pokazuje, jak utworzyć przykładowe powiązanie, ale to przykładowe powiązanie nie stosuje żadnych dodatkowych składników uwierzytelniania i może prowadzić do niebezpiecznego użycia. Pulpit nawigacyjny Kubernetes jest otwarty dla każdego, kto ma dostęp do adresu URL. Nie ujawniaj publicznie pulpitu nawigacyjnego Kubernetes.
>
> ```console
> kubectl create clusterrolebinding kubernetes-dashboard --clusterrole=cluster-admin --serviceaccount=kube-system:kubernetes-dashboard
> ```
> 
> Aby uzyskać więcej informacji na temat korzystania z różnych metod uwierzytelniania, zobacz wiki Kubernetes pulpitu nawigacyjnego na stronie [kontroli dostępu][dashboard-authentication].

![Strona przegląd pulpitu nawigacyjnego sieci Web Kubernetes](./media/kubernetes-dashboard/dashboard-overview.png)

## <a name="create-an-application"></a>Tworzenie aplikacji

Aby zobaczyć, jak pulpit nawigacyjny Kubernetes może zmniejszyć złożoność zadań związanych z zarządzaniem, Utwórz aplikację. Możesz utworzyć aplikację z poziomu pulpitu nawigacyjnego Kubernetes, dostarczając tekst wejściowy, plik YAML lub za pośrednictwem graficznego kreatora.

Aby utworzyć aplikację, wykonaj następujące czynności:

1. Wybierz przycisk **Utwórz** w prawym górnym oknie.
1. Aby użyć Kreatora graficznego, wybierz opcję **utworzenia aplikacji**.
1. Podaj nazwę wdrożenia, na przykład *Nginx*
1. Wprowadź nazwę obrazu kontenera, który ma być używany, na przykład *Nginx: 1.15.5*
1. Aby udostępnić port 80 dla ruchu w sieci Web, należy utworzyć usługę Kubernetes. W obszarze **Usługa**wybierz opcję **zewnętrzny**, a następnie wprowadź **80** dla portu i portu docelowego.
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
