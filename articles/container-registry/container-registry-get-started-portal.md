---
title: Szybki start — tworzenie rejestru w portalu
description: Szybko naucz się tworzyć prywatny rejestr kontenerów platformy Azure przy użyciu Azure Portal.
ms.date: 08/04/2020
ms.topic: quickstart
ms.custom:
- mvc
- mode-portal
ms.openlocfilehash: 4618a83a43d9c27f44432d5d346fdb053cee7054
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107773589"
---
# <a name="quickstart-create-an-azure-container-registry-using-the-azure-portal"></a>Szybki start: tworzenie rejestru kontenerów platformy Azure przy użyciu Azure Portal

Rejestr kontenerów platformy Azure to prywatny rejestr platformy Docker na platformie Azure, w którym można przechowywać prywatne obrazy kontenerów platformy Docker i powiązane artefakty oraz zarządzać nimi. W tym przewodniku Szybki start utworzysz rejestr kontenerów za pomocą witryny Azure Portal. Następnie za pomocą poleceń platformy Docker wypchniesz obraz kontenera do rejestru, a na koniec ściągniesz i uruchomisz obraz z rejestru.

Aby zalogować się do rejestru i pracować z obrazami kontenerów w tym przewodniku Szybki start, musisz korzystać z interfejsu wiersza polecenia platformy Azure (zalecana jest wersja 2.0.55 lub nowsza). Uruchom polecenie `az --version`, aby dowiedzieć się, jaka wersja jest używana. Jeśli konieczna będzie instalacja lub uaktualnienie, zobacz [Instalowanie interfejsu wiersza polecenia platformy Azure][azure-cli].

Musisz mieć również zainstalowane lokalnie środowisko Docker. Środowisko Docker zawiera pakiety, które umożliwiają łatwe konfigurowanie platformy Docker w systemie [Mac][docker-mac], [Windows][docker-windows] lub [Linux][docker-linux].

## <a name="sign-in-to-azure"></a>Logowanie do platformy Azure

Zaloguj się do witryny Azure Portal pod adresem https://portal.azure.com.

## <a name="create-a-container-registry"></a>Tworzenie rejestru kontenerów

Wybierz **pozycję Utwórz zasób**  >  **Kontenery**  >  **Container Registry**.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-01.png" alt-text="Przejdź do rejestru kontenerów w portalu":::

Na karcie **Podstawowe wprowadź** wartości w polach **Grupa zasobów** i Nazwa **rejestru**. Nazwa rejestru musi być unikatowa w obrębie platformy Azure i może zawierać od 5 do 50 znaków alfanumerycznych. Na potrzeby tego przewodnika Szybki start utwórz w lokalizacji `West US` nową grupę zasobów o nazwie `myResourceGroup`, a dla opcji **SKU** wybierz wartość „Podstawowa”.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-03.png" alt-text="Tworzenie rejestru kontenerów w portalu":::

Zaakceptuj wartości domyślne dla pozostałych ustawień. Następnie wybierz **pozycję Przejrzyj i utwórz.** Po przejrzeniu ustawień wybierz pozycję **Utwórz**.

W tym przewodniku Szybki start utworzysz rejestr *Podstawowy*, który jest zoptymalizowaną pod względem kosztów opcją dla deweloperów poznających usługę Azure Container Registry. Aby uzyskać szczegółowe informacje na temat dostępnych warstw usług (SKU), zobacz [Warstwy usług rejestru kontenerów][container-registry-skus].

Kiedy pojawi się komunikat **Wdrażanie zakończyło się pomyślnie**, wybierz rejestr kontenerów w portalu. 

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-05.png" alt-text="Omówienie rejestru kontenerów w portalu":::

Zanotuj nazwę rejestru i wartość serwera **logowania**. Wartości te są wymagane w poniższych krokach podczas wypychania i ściągania obrazów za pomocą platformy Docker.

## <a name="log-in-to-registry"></a>Logowanie do rejestru

Przed wypchnięciem i ściągnięciem obrazów kontenerów należy zalogować się do wystąpienia rejestru. [Zaloguj się do interfejsu wiersza][get-started-with-azure-cli] polecenia platformy Azure na komputerze lokalnym, a następnie uruchom [polecenie az acr login.][az-acr-login] Podczas logowania się przy użyciu interfejsu wiersza polecenia platformy Azure określ tylko nazwę rejestru. Nie używaj nazwy serwera logowania, która zawiera sufiks domeny, na przykład `azurecr.io` .

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

Aby wyświetlić listę obrazów w rejestrze, przejdź do rejestru w portalu i wybierz pozycję **Repozytoria,** a następnie wybierz repozytorium  **hello-world** utworzone za `docker push` pomocą .

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-09.png" alt-text="Lista obrazów kontenerów w portalu":::

Po wybraniu **repozytorium hello-world** w obszarze Tagi zostanie wyświetlony obraz `v1` z **tagiem**.

[!INCLUDE [container-registry-quickstart-docker-pull](../../includes/container-registry-quickstart-docker-pull.md)]

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Aby wyczyścić zasoby, przejdź do grupy zasobów **myResourceGroup** w portalu. Po załadowaniu grupy zasobów  kliknij pozycję Usuń grupę zasobów, aby usunąć grupę zasobów, rejestr kontenerów i przechowywane w tym miejscu obrazy kontenerów.

:::image type="content" source="media/container-registry-get-started-portal/qs-portal-08.png" alt-text="Usuwanie grupy zasobów w portalu":::


## <a name="next-steps"></a>Następne kroki

W tym przewodniku Szybki start utworzono usługę Azure Container Registry w witrynie Azure Portal, wypchnięto obraz kontenera oraz ściągnięto i uruchomiono obraz z rejestru. Przejdź do samouczków usługi Azure Container Registry, aby dowiedzieć się więcej o tej usłudze.

> [!div class="nextstepaction"]
> [Samouczki dotyczące usługi Azure Container Registry][container-registry-tutorial-prepare-registry]

> [!div class="nextstepaction"]
> [zadania usługi Azure Container Registry samouczków][container-registry-tutorial-quick-task]

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
[az-acr-login]: /cli/azure/acr#az_acr_login
[container-registry-tutorial-quick-task]: container-registry-tutorial-quick-task.md
