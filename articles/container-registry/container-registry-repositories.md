---
title: Wyświetlanie repozytoriów w portalu
description: Użyj Azure Portal, aby wyświetlić Azure Container Registry repozytoria, które hostuje obrazy kontenerów platformy Docker i inne obsługiwane artefakty.
ms.topic: article
ms.date: 01/05/2018
ms.openlocfilehash: 1da72706d2554610a685f71199ab14af5e30ce1a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "74456297"
---
# <a name="view-container-registry-repositories-in-the-azure-portal"></a>Wyświetlanie repozytoriów rejestru kontenera w Azure Portal

Azure Container Registry umożliwia przechowywanie obrazów kontenerów platformy Docker w repozytoriach. Przechowywanie obrazów w repozytoriach umożliwia przechowywanie grup obrazów (lub wersji obrazów) w środowiskach izolowanych. Te repozytoria można określić podczas wypychania obrazów do rejestru i wyświetlania ich zawartości w Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

* **Rejestr kontenerów**: Utwórz rejestr kontenerów w ramach subskrypcji platformy Azure. Na przykład użyj [Azure Portal](container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](container-registry-get-started-azure-cli.md).
* Interfejs wiersza polecenia **platformy Docker**: Zainstaluj [platformę Docker][docker-install] na komputerze lokalnym, która zapewnia program Docker.
* **Obraz kontenera**: wypchnij obraz do rejestru kontenerów. Aby uzyskać wskazówki dotyczące wypychania i ściągania obrazów, zobacz [wypychanie i ściąganie obrazu](container-registry-get-started-docker-cli.md).

## <a name="view-repositories-in-azure-portal"></a>Wyświetl repozytoria w Azure Portal

Można wyświetlić listę repozytoriów obsługujących obrazy, a także Tagi obrazu w Azure Portal.

Jeśli wykonano kroki opisane w sekcji [wypychanie i ściąganie obrazu](container-registry-get-started-docker-cli.md) (i dopiero po usunięciu obrazu), w rejestrze kontenerów powinien znajdować się obraz Nginx. Instrukcje zawarte w tym artykule podano po oznaczeniu obrazu za pomocą przestrzeni nazw, "Przykłady" w `/samples/nginx` . Jako odświeżacz, polecenie [Docker push][docker-push] określone w tym artykule było następujące:

```Bash
docker push myregistry.azurecr.io/samples/nginx
```

 Ponieważ Azure Container Registry obsługuje takie przestrzenie nazw wielopoziomowego repozytorium, można określać zakres kolekcji obrazów związanych z określoną aplikacją lub kolekcję aplikacji do różnych zespołów programistycznych lub operacyjnych. Aby dowiedzieć się więcej o repozytoriach w rejestrach kontenerów, zobacz [prywatne rejestry kontenerów platformy Docker na platformie Azure](container-registry-intro.md).

Aby wyświetlić repozytorium:

1. Zaloguj się do witryny [Azure Portal][portal].
1. Wybierz **Azure Container Registry** , do którego wypychasz obraz Nginx
1. Wybierz pozycję **repozytoria** , aby wyświetlić listę repozytoriów zawierających obrazy w rejestrze
1. Wybierz repozytorium, aby wyświetlić Tagi obrazu w tym repozytorium

Na przykład, jeśli wypychasz obraz Nginx zgodnie z instrukcją [push i ściąganiem obrazu](container-registry-get-started-docker-cli.md), zobaczysz coś podobnego do:

![Repozytoria w portalu](./media/container-registry-repositories/container-registry-repositories.png)

## <a name="next-steps"></a>Następne kroki

Teraz, gdy znasz podstawowe informacje na temat wyświetlania repozytoriów i pracy z nią w portalu, spróbuj użyć Azure Container Registry z klastrem [usługi Azure Kubernetes Service (AKS)](../aks/tutorial-kubernetes-prepare-app.md) .

<!-- LINKS - External -->
[docker-install]: https://docs.docker.com/engine/installation/
[docker-push]: https://docs.docker.com/engine/reference/commandline/push/
[portal]: https://portal.azure.com
