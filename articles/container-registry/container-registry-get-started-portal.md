---
title: Szybki Start — tworzenie rejestru w portalu
description: Szybka nauka tworzenia prywatnego rejestru kontenerów platformy Azure przy użyciu Azure Portal.
ms.topic: quickstart
ms.date: 08/04/2020
ms.custom: seodec18, mvc
ms.openlocfilehash: ff74232edf5c96f091082f8486c44fed6125b0b1
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97825847"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Szybki Start: Tworzenie usługi Azure Container Registry przy użyciu Azure Portal

Usługa Azure Container Registry jest prywatnym rejestrem platformy Docker na platformie Azure, w którym można przechowywać prywatne obrazy kontenerów platformy Docker i powiązane artefakty oraz zarządzać nimi. W tym przewodniku Szybki start utworzysz rejestr kontenerów za pomocą witryny Azure Portal. Następnie za pomocą poleceń platformy Docker wypchniesz obraz kontenera do rejestru, a na koniec ściągniesz i uruchomisz obraz z rejestru.

Aby zalogować się do rejestru i pracować z obrazami kontenerów w tym przewodniku Szybki start, musisz korzystać z interfejsu wiersza polecenia platformy Azure (zalecana jest wersja 2.0.55 lub nowsza). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Musisz mieć również zainstalowane lokalnie środowisko Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [Mac][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Wybierz pozycję **Utwórz**  >  **kontenery** zasobów  >  **Container Registry**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Przejdź do rejestru kontenerów w portalu":::

Na karcie **podstawy** wprowadź wartości dla **grupy zasobów** i **nazwy rejestru**. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. Na potrzeby tego przewodnika Szybki start utwórz w lokalizacji `West US` nową grupę zasobów o nazwie `myResourceGroup`, a dla opcji **SKU** wybierz wartość „Podstawowa”.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Tworzenie rejestru kontenerów w portalu":::

Zaakceptuj wartości domyślne pozostałych ustawień. Następnie wybierz pozycję **Przegląd + Utwórz**. Po przejrzeniu ustawień wybierz pozycję **Utwórz**.

W tym przewodniku Szybki start utworzysz rejestr *Podstawowy*, który jest zoptymalizowaną pod względem kosztów opcją dla deweloperów poznających usługę Azure Container Registry. Aby uzyskać szczegółowe informacje na temat dostępnych warstw usług (SKU), zobacz temat [warstwy usługi Container Registry][container-registry-skus].

Kiedy pojawi się komunikat **Wdrażanie zakończyło się pomyślnie**, wybierz rejestr kontenerów w portalu. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Przegląd rejestru kontenerów w portalu":::

Zanotuj nazwę rejestru i wartość **serwera logowania**. Te wartości są używane w poniższych krokach podczas wypychania i ściągania obrazów przy użyciu platformy Docker.

## <a name="log-in-to-registry"></a>Logowanie do rejestru

Przed wypychaniem i ściąganiem obrazów kontenerów należy zalogować się do wystąpienia rejestru. [Zaloguj się do interfejsu wiersza polecenia platformy Azure][get-started-with-azure-cli] na komputerze lokalnym, a następnie uruchom polecenie [AZ ACR login][az-acr-login] . Po zalogowaniu się za pomocą interfejsu wiersza polecenia platformy Azure podaj tylko nazwę rejestru. Nie używaj nazwy serwera logowania, która zawiera sufiks domeny, taki jak `azurecr.io` .

```azurecli
az acr login --name <registry-name>
```

Przykład:

```azurecli
az acr login --name mycontainerregistry
```

Po ukończeniu polecenie zwraca ciąg `Login Succeeded`. 

[!INCLUDE [container-registry-quickstart-docker-push](../../includes/container-registry-quickstart-docker-push.md)]

## <a name="list-container-images"></a>Tworzenie listy obrazów kontenerów

Aby wyświetlić listę obrazów w rejestrze, przejdź do rejestru w portalu i wybierz pozycję **repozytoria**, a następnie wybierz repozytorium  **Hello-World** utworzone za pomocą `docker push` .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Wyświetlanie listy obrazów kontenerów w portalu":::

Po wybraniu repozytorium **Hello-World** zobaczysz `v1` oznakowany obraz w obszarze **Tagi**.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby, przejdź do grupy zasobów **myResourceGroup** w portalu. Po załadowaniu grupy zasobów kliknij pozycję **Usuń grupę zasobów** , aby usunąć grupę zasobów, rejestr kontenerów oraz zapisane w niej obrazy kontenerów.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Usuwanie grupy zasobów w portalu":::


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono usługę Azure Container Registry w witrynie Azure Portal, wypchnięto obraz kontenera oraz ściągnięto i uruchomiono obraz z rejestru. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [Samouczki zadań Azure Container Registry][container-registry-tutorial-quick-task]

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-pull]: https://docs.docker.com/engine/reference/commandline/pull/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[docker-rmi]: https://docs.docker.com/engine/reference/commandline/rmi/
[docker-run]: https://docs.docker.com/engine/reference/commandline/run/
[docker-tag]: https://docs.docker.com/engine/reference/commandline/tag/
[docker-windows]: https://docs.docker.com/docker-for-windows/

<!-- LINKS - internal -->
[container-registry-tutorial-prepare-registry]: container-registry-tutorial-prepare-registry.md
[container-registry-skus]: container-registry-skus.md
[azure-cli]: /cli/azure/install-azure-cli
[get-started-with-azure-cli]: /cli/azure/get-started-with-azure-cli
[az-acr-login]: /cli/azure/acr#az-acr-login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
