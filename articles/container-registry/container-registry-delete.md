---
title: Usuwanie zasobów obrazu
description: Szczegółowe informacje na temat efektywnego zarządzania rozmiarem rejestru przez usunięcie danych obrazu kontenera przy użyciu poleceń interfejsu wiersza polecenia platformy Azure.
ms.topic: article
ms.date: 07/31/2019
ms.openlocfilehash: af277d0c02960c989b4e9119f2ecbfd8f6d7ce07
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783993"
---
# <a name="delete-container-images-in-azure-container-registry-using-the-azure-cli"></a>Usuwanie obrazów kontenerów w usłudze Azure Container Registry użyciu interfejsu wiersza polecenia platformy Azure

Aby zachować rozmiar rejestru kontenerów platformy Azure, należy okresowo usuwać nieaktualne dane obrazu. Niektóre obrazy kontenerów wdrożone w środowisku produkcyjnym mogą wymagać przechowywania długoterminowego, ale inne zazwyczaj można usunąć szybciej. Na przykład w scenariuszu zautomatyzowanej kompilacji i testowania rejestr może szybko zapełniać obrazy, które mogą nigdy nie zostać wdrożone, i może zostać przeczyszony wkrótce po zakończeniu kompilacji i przebiegu testowego.

Ponieważ dane obrazu można usuwać na kilka różnych sposobów, ważne jest, aby zrozumieć, jak każda operacja usuwania wpływa na użycie magazynu. W tym artykule odano kilka metod usuwania danych obrazu:

* Usuń [repozytorium:](#delete-repository)usuwa wszystkie obrazy i wszystkie unikatowe warstwy w repozytorium.
* Usuń według [tagu:](#delete-by-tag)usuwa obraz, tag, wszystkie unikatowe warstwy, do których odwołuje się obraz, oraz wszystkie inne tagi skojarzone z obrazem.
* Usuń za [pomocą skrótu](#delete-by-manifest-digest)manifestu: usuwa obraz, wszystkie unikatowe warstwy, do których odwołuje się obraz, oraz wszystkie tagi skojarzone z obrazem.

Dostępne są przykładowe skrypty, które ułatwiają automatyzowanie operacji usuwania.

Aby uzyskać wprowadzenie do tych pojęć, zobacz About registries, repositories, and images (Informacje o [rejestrach, repozytoriach i obrazach).](container-registry-concepts.md)

## <a name="delete-repository"></a>Usuwanie repozytorium

Usunięcie repozytorium powoduje usunięcie wszystkich obrazów w repozytorium, w tym wszystkich tagów, unikatowych warstw i manifestów. Usunięcie repozytorium powoduje odzyskanie miejsca do magazynowania używanego przez obrazy odwołujące się do unikatowych warstw w tym repozytorium.

Następujące polecenie interfejsu wiersza polecenia platformy Azure usuwa repozytorium "acr-helloworld" oraz wszystkie tagi i manifesty w repozytorium. Jeśli warstwy, do których odwołują się usunięte manifesty, nie są przywołyne przez inne obrazy w rejestrze, usuwane są również ich dane warstw, co odzyskuje miejsce do magazynowania.

```azurecli
 az acr repository delete --name myregistry --repository acr-helloworld
```

## <a name="delete-by-tag"></a>Usuń według tagu

Poszczególne obrazy można usunąć z repozytorium, określając nazwę i tag repozytorium w operacji usuwania. Usunięcie według tagu powoduje odzyskanie miejsca do magazynowania używanego przez wszelkie unikatowe warstwy obrazu (warstwy nieudzielone przez inne obrazy w rejestrze).

Aby usunąć według tagu, użyj [az acr repository delete][az-acr-repository-delete] i określ nazwę obrazu w `--image` parametrze . Wszystkie warstwy unikatowe dla obrazu oraz wszystkie inne tagi skojarzone z obrazem zostaną usunięte.

Na przykład usunięcie obrazu "acr-helloworld:latest" z rejestru "myregistry":

```azurecli
az acr repository delete --name myregistry --image acr-helloworld:latest
```

```output
This operation will delete the manifest 'sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108' and all the following images: 'acr-helloworld:latest', 'acr-helloworld:v3'.
Are you sure you want to continue? (y/n):
```

> [!TIP]
> Usuwanie *według tagu* nie powinno być mylone z usuwaniem tagu (usuwanie tagu). Tag możesz usunąć za pomocą polecenia interfejsu wiersza polecenia platformy Azure [az acr repository untag][az-acr-repository-untag]. Po coktagowanie obrazu nie jest wolne miejsce, ponieważ [jego dane manifestu](container-registry-concepts.md#manifest) i warstwy pozostają w rejestrze. Usuwane jest tylko samo odwołanie do tagu.

## <a name="delete-by-manifest-digest"></a>Usuwanie za pomocą skrótu manifestu

Skrót [manifestu](container-registry-concepts.md#manifest-digest) można skojarzyć z jednym, żadnym lub wieloma tagami. Po usunięciu za pomocą skrótu wszystkie tagi, do których odwołuje się manifest, są usuwane, podobnie jak dane warstw dla dowolnych warstw unikatowych dla obrazu. Dane warstwy udostępnionej nie są usuwane.

Aby usunąć za pomocą skrótu, najpierw należy wyświetlić listę skrótów manifestu dla repozytorium zawierającego obrazy, które chcesz usunąć. Na przykład:

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

Następnie określ skrót, który chcesz usunąć, za pomocą [polecenia az acr repository delete.][az-acr-repository-delete] Format polecenia jest następujący:

```azurecli
az acr repository delete --name <acrName> --image <repositoryName>@<digest>
```

Aby na przykład usunąć ostatni manifest wymieniony w poprzednich danych wyjściowych (z tagiem "v2"):

```azurecli
az acr repository delete --name myregistry --image acr-helloworld@sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57
```

```output
This operation will delete the manifest 'sha256:3168a21b98836dda7eb7a846b3d735286e09a32b0aa2401773da518e7eba3b57' and all the following images: 'acr-helloworld:v2'.
Are you sure you want to continue? (y/n): 
```

Obraz zostanie usunięty z rejestru, podobnie jak wszystkie dane `acr-helloworld:v2` warstwy unikatowe dla tego obrazu. Jeśli manifest jest skojarzony z wieloma tagami, wszystkie skojarzone tagi również zostaną usunięte.

## <a name="delete-digests-by-timestamp"></a>Usuwanie skrótów według znacznika czasu

Aby zachować rozmiar repozytorium lub rejestru, może być konieczne okresowe usuwanie skrótów manifestu starszych niż określoną datę.

Następujące polecenie interfejsu wiersza polecenia platformy Azure wyświetla wszystkie podsumowanie manifestu w repozytorium starszym niż określony znacznik czasu w kolejności rosnącej. Zastąp `<acrName>` wartości `<repositoryName>` i wartościami odpowiednimi dla Twojego środowiska. Znacznik czasu może być pełnym wyrażeniem daty/godziny lub datą, jak w tym przykładzie.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> \
--orderby time_asc -o tsv --query "[?timestamp < '2019-04-05'].[digest, timestamp]"
```

Po zidentyfikowaniu nieaktualnych skrótów manifestu możesz uruchomić następujący skrypt powłoki Bash, aby usunąć skróty manifestu starsze niż określony znacznik czasu. Wymaga interfejsu wiersza polecenia platformy Azure **i usługi xargs.** Domyślnie skrypt nie wykonuje usuwania. Zmień wartość `ENABLE_DELETE` na , `true` aby włączyć usuwanie obrazu.

> [!WARNING]
> Użyj następującego przykładowego skryptu z rozwagą — usunięte dane obrazu są NIEODWRACALNE. Jeśli masz systemy, które ściągają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy uruchamiać tych skryptów. Usunięcie skrótów manifestu uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągać według manifestu, rozważ przyjęcie *unikatowego* schematu tagowania, co jest [zalecanym najlepszym rozwiązaniem.](container-registry-image-tag-version.md) 

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

## <a name="delete-untagged-images"></a>Usuwanie nieoznakowanych obrazów

Jak wspomniano w sekcji Podsumowanie [manifestu,](container-registry-concepts.md#manifest-digest) wypchanie zmodyfikowanego obrazu przy użyciu istniejącego tagu spowoduje **untagowanie** wcześniej wypchniętego obrazu, co spowoduje, że obraz oddzielony (lub "dangling"). Manifest wcześniej wypchniętego obrazu — i jego dane warstwy — pozostaje w rejestrze. Rozważmy następującą sekwencję zdarzeń:

1. Wypychanie obrazu *acr-helloworld z* tagiem **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Sprawdź manifesty repozytorium *acr-helloworld:*

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

1. Modyfikowanie *pliku Dockerfile acr-helloworld*
1. Wypychanie obrazu *acr-helloworld z* tagiem **latest**: `docker push myregistry.azurecr.io/acr-helloworld:latest`
1. Sprawdź manifesty repozytorium *acr-helloworld:*

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

Jak widać w danych wyjściowych ostatniego kroku w sekwencji, istnieje teraz oddzielony manifest, którego właściwość `"tags"` jest pustą listą. Ten manifest nadal istnieje w rejestrze wraz z dowolnymi unikatowymi danymi warstwy, do których się odwołuje. **Aby usunąć takie oddzielone obrazy i ich dane warstw, należy usunąć za pomocą skrótu manifestu**.

## <a name="delete-all-untagged-images"></a>Usuwanie wszystkich nieoznakowanych obrazów

Wszystkie nieoznakowane obrazy w repozytorium można wyświetlić za pomocą następującego polecenia interfejsu wiersza polecenia platformy Azure. Zastąp `<acrName>` wartości i `<repositoryName>` wartościami odpowiednimi dla Twojego środowiska.

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName> --query "[?tags[0]==null].digest"
```

Za pomocą tego polecenia w skrypcie można usunąć wszystkie nieoznakowane obrazy w repozytorium.

> [!WARNING]
> Użyj następujących przykładowych skryptów z rozwagą — usunięte dane obrazu są NIEODWRACALNE. Jeśli masz systemy, które ściągają obrazy za pomocą skrótu manifestu (w przeciwieństwie do nazwy obrazu), nie należy uruchamiać tych skryptów. Usunięcie nieoznakowanych obrazów uniemożliwi tym systemom ściąganie obrazów z rejestru. Zamiast ściągać według manifestu, rozważ przyjęcie *unikatowego* schematu tagowania, co jest [zalecanym najlepszym rozwiązaniem.](container-registry-image-tag-version.md)

**Interfejs wiersza polecenia platformy Azure w powłoce Bash**

Poniższy skrypt powłoki Bash usuwa wszystkie nieoznakowane obrazy z repozytorium. Wymaga interfejsu wiersza polecenia platformy Azure **i usługi xargs.** Domyślnie skrypt nie wykonuje usuwania. Zmień wartość `ENABLE_DELETE` na , `true` aby włączyć usuwanie obrazu.

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

Poniższy skrypt programu PowerShell usuwa wszystkie nieoznakowane obrazy z repozytorium. Wymaga programu PowerShell i interfejsu wiersza polecenia platformy Azure. Domyślnie skrypt nie wykonuje usuwania. Zmień wartość `$enableDelete` na , `$TRUE` aby włączyć usuwanie obrazu.

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

Zamiast skrypować polecenia interfejsu wiersza polecenia platformy Azure, uruchom zadanie usługi ACR na żądanie lub zaplanowane w celu usunięcia wszystkich tagów, które są starsze niż określony czas trwania lub są zgodne z określonym filtrem nazw. Aby uzyskać więcej informacji, zobacz [Automatyczne przeczyszczanie obrazów z rejestru kontenerów platformy Azure.](container-registry-auto-purge.md)

Opcjonalnie ustaw zasady [przechowywania](container-registry-retention-policy.md) dla każdego rejestru, aby zarządzać manifestami bez flag. Po włączeniu zasad przechowywania manifesty obrazów w rejestrze, które nie mają żadnych skojarzonych tagów i bazowych danych warstwy, są automatycznie usuwane po upływie ustawionego czasu.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji na temat magazynu obrazów w Azure Container Registry zobacz Container image storage in Azure Container Registry ( Magazyn obrazów [kontenerów w u Azure Container Registry](container-registry-storage.md).

<!-- IMAGES -->
[manifest-digest]: ./media/container-registry-delete/01-manifest-digest.png

<!-- LINKS - External -->
[docker-manifest-inspect]: https://docs.docker.com/edge/engine/reference/commandline/manifest/#manifest-inspect
[portal]: https://portal.azure.com

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
[az-acr-repository-untag]: /cli/azure/acr/repository#az_acr_repository_untag
