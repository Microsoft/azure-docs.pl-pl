---
title: Usuwanie zasobów obrazu
description: Szczegółowe informacje na temat efektywnego zarządzania rozmiarem rejestru przez usunięcie danych obrazu kontenera za pomocą poleceń interfejsu wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: 449a1c09bf88e3e0e0aeca4d3b687371d2a6b91a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "78403342"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Usuwanie obrazów kontenera w Azure Container Registry przy użyciu interfejsu wiersza polecenia platformy Azure

Aby zachować rozmiar rejestru kontenerów platformy Azure, należy okresowo usuwać stare dane obrazu. Niektóre obrazy kontenerów wdrożone w środowisku produkcyjnym mogą wymagać długoterminowego przechowywania, ale inne mogą być zwykle usuwane szybciej. Na przykład w zautomatyzowanym scenariuszu kompilowania i testowania rejestr może szybko wypełniać obrazy, które nigdy nie zostały wdrożone, i mogą zostać przeczyszczone wkrótce po zakończeniu kompilacji i przebiegu testowego.

Ponieważ dane obrazu można usuwać na kilka różnych sposobów, ważne jest, aby zrozumieć, w jaki sposób każda operacja usuwania ma wpływ na użycie magazynu. W tym artykule opisano kilka metod usuwania danych obrazu:

* Usuwanie [repozytorium](#delete-repository): usuwa wszystkie obrazy i wszystkie unikatowe warstwy w repozytorium.
* Usuń przez [tag](#delete-by-tag): usuwa obraz, tag, wszystkie unikatowe warstwy, do których odwołuje się obraz, oraz wszystkie inne Tagi skojarzone z obrazem.
* Usuń według [skrótu manifestu](#delete-by-manifest-digest): usuwa obraz, wszystkie unikatowe warstwy, do których odwołuje się obraz, oraz wszystkie Tagi skojarzone z obrazem.

Przykładowe skrypty są udostępniane, aby pomóc zautomatyzować operacje usuwania.

Aby zapoznać się z wprowadzeniem do tych pojęć, zobacz [Informacje o rejestrach, repozytoriach i obrazach](container-registry-concepts.md).

## <a name="delete-repository"></a>Usuń repozytorium

Usunięcie repozytorium powoduje usunięcie wszystkich obrazów w repozytorium, w tym wszystkich tagów, unikatowych warstw i manifestów. Po usunięciu repozytorium odzyskuje miejsce do magazynowania używane przez obrazy, które odwołują się do unikatowych warstw w tym repozytorium.

Następujące polecenie interfejsu wiersza polecenia platformy Azure usuwa repozytorium "ACR-HelloWorld" i wszystkie Tagi i manifesty w repozytorium. Jeśli do warstw, do których odwołuje się usunięte manifesty, nie odwołują się żadne inne obrazy w rejestrze, ich dane warstwy są również usuwane i odzyskiwane jest miejsce do magazynowania.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Usuń przez tag

Można usunąć poszczególne obrazy z repozytorium, określając nazwę i tag repozytorium w operacji usuwania. Gdy usuniesz przez tag, odzyskasz miejsce do magazynowania używane przez dowolne unikatowe warstwy w obrazie (warstwy, które nie są współużytkowane przez żadne inne obrazy w rejestrze).

Aby usunąć przez tag, użyj [AZ ACR Repository Delete][az-acr-repository-delete] i określ nazwę obrazu w `--image` parametrze. Wszystkie warstwy są unikatowe dla obrazu, a wszystkie inne Tagi skojarzone z obrazem zostaną usunięte.

Na przykład usuwanie obrazu "ACR-HelloWorld: Najnowsza" z rejestru "Moje rejestr":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Usuń *przez tag* nie należy mylić z usuwaniem tagu (bez znakowania). Tag można usunąć za pomocą polecenia interfejsu CLI platformy Azure [AZ ACR Repository UNTAG][az-acr-repository-untag]. Nie zwalnia się miejsca po UNTAG obrazu, ponieważ jego [manifest](container-registry-concepts.md#manifest) i dane warstwy pozostają w rejestrze. Tylko odwołanie do tagu zostanie usunięte.

## <a name="delete-by-manifest-digest"></a>Usuń według skrótu manifestu

[Podsumowanie manifestu](container-registry-concepts.md#manifest-digest) może być skojarzone z jednym, brakiem lub wieloma tagami. Po usunięciu przez Digest wszystkie Tagi, do których odwołuje się manifest, są usuwane, tak jak dane warstwy dla każdej warstwy, które są unikatowe dla obrazu. Dane warstwy udostępnionej nie są usuwane.

Aby usunąć plik przez podsumowanie, najpierw Wyświetl skróty manifestu zawierające obrazy, które chcesz usunąć. Na przykład:

```azurecli
az acr repository show-manifests --name myregistry --repository acr-helloworld
```

```output
[
  {
    "digest": "sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108",
    "tags": [
      "latest",
      "v3"
    ],
    "timestamp": "2018-07-12T15:52:00.2075864Z"
  },
  {
    "digest": "sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57",
    "tags": [
      "v2"
    ],
    "timestamp": "2018-07-12T15:50:53.5372468Z"
  }
]
```

Następnie określ skrót, który chcesz usunąć, za pomocą polecenia [AZ ACR Repository Delete][az-acr-repository-delete] . Format polecenia jest następujący:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Na przykład, aby usunąć ostatni manifest wymieniony w poprzednich danych wyjściowych (ze znacznikiem "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

`acr-helloworld:v2`Obraz jest usuwany z rejestru, podobnie jak wszystkie dane warstwy, które są unikatowe dla tego obrazu. Jeśli manifest jest skojarzony z wieloma tagami, wszystkie skojarzone Tagi również zostaną usunięte.

## <a name="delete-digests-by-timestamp"></a>Usuń Skróty według sygnatury czasowej

Aby zachować rozmiar repozytorium lub rejestru, może być konieczne okresowe usunięcie z nich podsumowania manifestu niż określona data.

Następujące polecenie interfejsu wiersza polecenia platformy Azure wyświetla wszystkie skróty manifestu w repozytorium starszym niż określona sygnatura czasowa w kolejności rosnącej. Zamień `<acrName>` i `<repositoryName>` na wartości odpowiednie dla danego środowiska. Sygnatura czasowa może być pełnym wyrażeniem daty i godziny lub datą, jak w tym przykładzie.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Po zidentyfikowaniu starych szyfrowanych manifestów można uruchomić następujący skrypt bash, aby usunąć skróty manifestu starsze niż określona sygnatura czasowa. Wymaga interfejsu wiersza polecenia platformy Azure i **xargs**. Domyślnie skrypt nie wykonuje operacji usuwania. Zmień `ENABLE_DELETE` wartość na, aby `true` włączyć usuwanie obrazu.

> [!WARNING]
> Użyj następującego przykładowego skryptu z przestrogą — usunięte dane obrazu są NIEODWRACALNe. Jeśli masz systemy, które pobierają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy uruchamiać tych skryptów. Usunięcie skrótu manifestu uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągania według manifestu należy rozważyć przyjęcie *unikatowego schematu znakowania* , [zalecane najlepsze rozwiązanie](container-registry-image-tag-version.md). 

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
# TIMESTAMP can be a date-time string such as 2019-03-15T17:55:00.
REGISTRY=myregistry
REPOSITORY=myrepository
TIMESTAMP=2019-04-05  

# Delete all images older than specified timestamp.

if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
    --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY \
   --orderby time_asc --query "[?timestamp < '$TIMESTAMP'].[digest, timestamp]" -o tsv
fi
```

## <a name="delete-untagged-images"></a>Usuń nieoznakowane obrazy

Jak wspomniano w sekcji [Podsumowanie manifestu](container-registry-concepts.md#manifest-digest) , wypychanie zmodyfikowanego obrazu przy użyciu istniejącego tagu spowoduje **nieoznakowanie** wcześniej wypchnięcia obrazu, co spowoduje powstanie oddzielonego obrazu (lub "zawieszonego"). Plik manifestu wcześniej wypchniętego obrazu — i jego dane warstwy — pozostają w rejestrze. Należy wziąć pod uwagę następującą sekwencję zdarzeń:

1. Wypychanie obrazu *ACR — HelloWorld* z tagiem **najnowszy**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Sprawdź manifesty dla repozytorium *ACR-HelloWorld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   
   ```
   
   ```output
   [
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

1. Modyfikuj *ACR — HelloWorld* pliku dockerfile
1. Wypychanie obrazu *ACR — HelloWorld* z tagiem **najnowszy**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Sprawdź manifesty dla repozytorium *ACR-HelloWorld*:

   ```azurecli
   az acr repository show-manifests --name myregistry --repository acr-helloworld
   ```
   
   ```output
   [
     {
       "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
       "tags": [
         "latest"
       ],
       "timestamp": "2018-07-11T21:38:35.9170967Z"
     },
     {
       "digest": "sha256:d2bdc0c22d78cde155f53b4092111d7e13fe28ebf87a945f94b19c248000ceec",
       "tags": [],
       "timestamp": "2018-07-11T21:32:21.1400513Z"
     }
   ]
   ```

Jak widać w danych wyjściowych ostatniego kroku sekwencji, istnieje teraz oddzielony manifest, którego `"tags"` Właściwość jest pustą listą. Ten manifest nadal istnieje w rejestrze wraz ze wszystkimi unikatowymi danymi warstwy, do których się odwołuje. **Aby usunąć obrazy oddzielone i ich dane warstwowe, należy usunąć je za pomocą skrótu manifestu**.

## <a name="delete-all-untagged-images"></a>Usuń wszystkie obrazy nieoznakowane

Możesz wyświetlić listę wszystkich nieoznakowanych obrazów w repozytorium za pomocą następującego polecenia platformy Azure. Zamień `<acrName>` i `<repositoryName>` na wartości odpowiednie dla danego środowiska.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Korzystając z tego polecenia w skrypcie, można usunąć wszystkie nieoznaczone obrazy w repozytorium.

> [!WARNING]
> W następujących przykładowych skryptach należy zachować ostrożność — usunięte dane obrazu są NIEODWRACALNe. Jeśli masz systemy, które pobierają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy uruchamiać tych skryptów. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągania według manifestu należy rozważyć przyjęcie *unikatowego schematu znakowania* , [zalecane najlepsze rozwiązanie](container-registry-image-tag-version.md).

**Interfejs wiersza polecenia platformy Azure w bash**

Poniższy skrypt bash usuwa wszystkie nieoznakowane obrazy z repozytorium. Wymaga interfejsu wiersza polecenia platformy Azure i **xargs**. Domyślnie skrypt nie wykonuje operacji usuwania. Zmień `ENABLE_DELETE` wartość na, aby `true` włączyć usuwanie obrazu.

```bash
#!/bin/bash

# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to 'true' to enable image delete
ENABLE_DELETE=false

# Modify for your environment
REGISTRY=myregistry
REPOSITORY=myrepository

# Delete all untagged (orphaned) images
if [ "$ENABLE_DELETE" = true ]
then
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY  --query "[?tags[0]==null].digest" -o tsv \
    | xargs -I% az acr repository delete --name $REGISTRY --image $REPOSITORY@% --yes
else
    echo "No data deleted."
    echo "Set ENABLE_DELETE=true to enable image deletion of these images in $REPOSITORY:"
    az acr repository show-manifests --name $REGISTRY --repository $REPOSITORY --query "[?tags[0]==null]" -o tsv
fi
```

**Interfejs wiersza polecenia platformy Azure w programie PowerShell**

Poniższy skrypt programu PowerShell usuwa wszystkie nieoznakowane obrazy z repozytorium. Wymaga programu PowerShell i interfejsu wiersza polecenia platformy Azure. Domyślnie skrypt nie wykonuje operacji usuwania. Zmień `$enableDelete` wartość na, aby `$TRUE` włączyć usuwanie obrazu.

```powershell
# WARNING! This script deletes data!
# Run only if you do not have systems
# that pull images via manifest digest.

# Change to '$TRUE' to enable image delete
$enableDelete = $FALSE

# Modify for your environment
$registry = "myregistry"
$repository = "myrepository"

if ($enableDelete) {
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null].digest" -o tsv `
    | %{ az acr repository delete --name $registry --image $repository@$_ --yes }
} else {
    Write-Host "No data deleted."
    Write-Host "Set `$enableDelete = `$TRUE to enable image deletion."
    az acr repository show-manifests --name $registry --repository $repository --query "[?tags[0]==null]" -o tsv
}
```


## <a name="automatically-purge-tags-and-manifests-preview"></a>Automatyczne przeczyszczanie tagów i manifestów (wersja zapoznawcza)

Alternatywą dla tworzenia skryptów poleceń interfejsu wiersza polecenia platformy Azure jest uruchomienie zadania ACR lub zaplanowanego na żądanie w celu usunięcia wszystkich tagów, które są starsze niż określony czas trwania lub pasują do określonego filtru nazw. Aby uzyskać więcej informacji, zobacz [Automatyczne przeczyszczanie obrazów z usługi Azure Container Registry](container-registry-auto-purge.md).

Opcjonalnie można ustawić [zasady przechowywania](container-registry-retention-policy.md) dla każdego rejestru, aby zarządzać nieoznakowanymi manifestami. Po włączeniu zasad przechowywania manifesty obrazu w rejestrze, które nie mają skojarzonych tagów, a dane warstwy źródłowej, są automatycznie usuwane po upływie określonego czasu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat magazynu obrazów w Azure Container Registry zobacz [Magazyn obrazów kontenerów w Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az-acr-repository-untag
