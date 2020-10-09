---
title: Samouczek — wdrażanie aplikacji kontenera do wystąpienia kontenera
description: Samouczek usługi Azure Container Instances, część 3 z 3 — wdrażanie aplikacji kontenerowej w usłudze Azure Container Instances
ms.topic: tutorial
ms.date: 03/21/2018
ms.custom: seodec18, mvc, devx-track-azurecli
ms.openlocfilehash: 2ea3d285f00d38df84587d9a7c15242fff38453b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87500576"
---
# <a name="tutorial-deploy-a-container-application-to-azure-container-instances"></a>Samouczek: wdrażanie aplikacji kontenera do Azure Container Instances

To jest ostatni samouczek z serii składającej się z trzech części. Wcześniej w tej serii [utworzono obraz kontenera](container-instances-tutorial-prepare-app.md) i [przekazano go do usługi Azure Container Registry](container-instances-tutorial-prepare-acr.md). Ten artykuł stanowi zakończenie serii i dotyczy wdrażania kontenera w usłudze Azure Container Instances.

W tym samouczku zostały wykonane następujące czynności:

> [!div class="checklist"]
> * Wdrażanie kontenera z usługi Azure Container Registry do usługi Azure Container Instances
> * Wyświetlanie uruchomionej aplikacji w przeglądarce
> * Wyświetlanie dzienników kontenera

## <a name="before-you-begin"></a>Przed rozpoczęciem

[!INCLUDE [container-instances-tutorial-prerequisites](../../includes/container-instances-tutorial-prerequisites.md)]

## <a name="deploy-the-container-using-the-azure-cli"></a>Wdrażanie kontenera przy użyciu interfejsu wiersza polecenia platformy Azure

W tej sekcji używa się interfejsu wiersza polecenia platformy Azure do wdrożenia obrazu utworzonego w [pierwszym samouczku](container-instances-tutorial-prepare-app.md) oraz wypchniętego do usługi Azure Container Registry w [drugim samouczku](container-instances-tutorial-prepare-acr.md). Przed kontynuowaniem upewnij się, że zakończono te samouczki.

### <a name="get-registry-credentials"></a>Pobieranie poświadczeń rejestru

Podczas wdrażania obrazu hostowanego w prywatnym rejestrze kontenera platformy Azure, takim jak ten utworzony w [drugim samouczku](container-instances-tutorial-prepare-acr.md), musisz podać poświadczenia, aby uzyskać dostęp do rejestru. 

Najlepszym rozwiązaniem w przypadku wielu scenariuszy jest utworzenie i skonfigurowanie jednostki usługi Azure Active Directory z uprawnieniami *ściągania* do rejestru. Aby utworzyć jednostkę usługi z niezbędnymi uprawnieniami, zobacz [uwierzytelnianie za pomocą Azure Container Registry z Azure Container Instances](../container-registry/container-registry-auth-aci.md) . Zanotuj identyfikator jednostki *usługi* i *hasło nazwy głównej usługi*. Te poświadczenia są używane do uzyskiwania dostępu do rejestru podczas wdrażania kontenera.

Potrzebna jest również pełna nazwa serwera logowania rejestru kontenerów (element `<acrName>` należy zastąpić nazwą rejestru):

```azurecli
az acr show --name <acrName> --query loginServer
```

### <a name="deploy-container"></a>Wdrażanie kontenera

Teraz użyj polecenia [az container create][az-container-create], aby wdrożyć kontener. Zastąp element `<acrLoginServer>` wartością uzyskaną przy użyciu poprzedniego polecenia. Zastąp elementy `<service-principal-ID>` i `<service-principal-password>` identyfikatorem jednostki usługi i hasłem jednostki usługi, które umożliwiają dostęp do rejestru. Zastąp element `<aciDnsLabel>` odpowiednią nazwą DNS.

```azurecli
az container create --resource-group myResourceGroup --name aci-tutorial-app --image <acrLoginServer>/aci-tutorial-app:v1 --cpu 1 --memory 1 --registry-login-server <acrLoginServer> --registry-username <service-principal-ID> --registry-password <service-principal-password> --dns-name-label <aciDnsLabel> --ports 80
```

W ciągu kilku sekund powinna pojawić się początkowa odpowiedź z platformy Azure. Wartość `--dns-name-label` musi być unikatowa w regionie platformy Azure, w którym tworzysz wystąpienia kontenera. Zmodyfikuj wartość w poprzednim poleceniu, jeśli podczas wykonywania polecenia zostanie wyświetlony komunikat o błędzie **etykiety nazwy DNS**.

### <a name="verify-deployment-progress"></a>Sprawdzanie postępu wdrażania

Aby wyświetlić stan wdrożenia, użyj polecenia [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query instanceView.state
```

Powtarzaj polecenie [az container show][az-container-show], aż stan zmieni się z *Oczekujące* na *Uruchomiono*. Powinno to potrwać krócej niż minutę. Jeśli kontener ma stan *Uruchomiono*, przejdź do następnego kroku.

## <a name="view-the-application-and-container-logs"></a>Wyświetlanie dzienników aplikacji i kontenerów

Po pomyślnym wdrożeniu wyświetl w pełni kwalifikowaną nazwę domeny (FQDN) kontenera przy użyciu polecenia [az container show][az-container-show]:

```azurecli
az container show --resource-group myResourceGroup --name aci-tutorial-app --query ipAddress.fqdn
```

Na przykład:
```output
"aci-demo.eastus.azurecontainer.io"
```

Aby wyświetlić działającą aplikację, przejdź do wyświetlonej nazwy DNS w ulubionej przeglądarce:

![Aplikacja Hello World w przeglądarce][aci-app-browser]

Możesz również wyświetlić dane wyjściowe dziennika kontenera:

```azurecli
az container logs --resource-group myResourceGroup --name aci-tutorial-app
```

Przykładowe dane wyjściowe:

```output
listening on port 80
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET / HTTP/1.1" 200 1663 "-" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
::ffff:10.240.0.4 - - [21/Jul/2017:06:00:02 +0000] "GET /favicon.ico HTTP/1.1" 404 150 "http://aci-demo.eastus.azurecontainer.io/" "Mozilla/5.0 (Macintosh; Intel Mac OS X 10_12_5) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/59.0.3071.115 Safari/537.36"
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie potrzebujesz już zasobów utworzonych w tej serii samouczków, możesz wykonać polecenie [az group delete][az-group-delete], aby usunąć grupę zasobów i wszystkie należące do niej zasoby. To polecenie usuwa utworzony rejestr kontenerów, a także uruchomiony kontener i wszystkie powiązane zasoby.

```azurecli-interactive
az group delete --name myResourceGroup
```

## <a name="next-steps"></a>Następne kroki

W tym samouczku ukończono proces wdrażania kontenera w usłudze Azure Container Instances. Wykonano następujące czynności:

> [!div class="checklist"]
> * Wdrożono kontener z usługi Azure Container Registry przy użyciu interfejsu wiersza polecenia platformy Azure
> * Wyświetlono aplikację w przeglądarce
> * Wyświetlono dzienniki kontenera

Teraz, gdy znasz już podstawy, przejdź do informacji na temat usługi Azure Container Instances, np. dotyczących działania grup kontenerów:

> [!div class="nextstepaction"]
> [Grupy kontenerów w usłudze Azure Container Instances](container-instances-container-groups.md)

<!-- IMAGES -->
[aci-app-browser]: ./media/container-instances-quickstart/aci-app-browser.png

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-login]: https://docs.docker.com/engine/reference/commandline/login/
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[az-container-create]: /cli/azure/container#az-container-create
[az-container-show]: /cli/azure/container#az-container-show
[az-group-delete]: /cli/azure/group#az-group-delete
[azure-cli-install]: /cli/azure/install-azure-cli
[prepare-app]: ./container-instances-tutorial-prepare-app.md
