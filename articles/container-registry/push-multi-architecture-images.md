---
title: Obrazy o wieloarchitekturze w rejestrze
description: Korzystanie z usługi Azure Container Registry do kompilowania, importowania, przechowywania i wdrażania obrazów wieloarchitekturowych (o wiele rdzeni)
ms.topic: article
ms.date: 02/07/2021
ms.custom: ''
ms.openlocfilehash: 520518a275e0a9d191ae770e560a0e6e8278eae2
ms.sourcegitcommit: ba3a4d58a17021a922f763095ddc3cf768b11336
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/23/2021
ms.locfileid: "104802458"
---
# <a name="multi-architecture-images-in-your-azure-container-registry"></a>Obrazy o wieloarchitekturze w usłudze Azure Container Registry

W tym artykule przedstawiono obrazy z *obsługą wieloarchitekturą* (*wieloosiową*) oraz sposób używania funkcji Azure Container Registry, które ułatwiają tworzenie, przechowywanie i używanie. 

Obraz z różnymi procesorami to typ obrazu kontenera, który może łączyć różne warianty dla różnych architektur i czasami dla różnych systemów operacyjnych. W przypadku uruchamiania obrazu z obsługą wieloarchitektur klienci kontenerów automatycznie wybierają wariant obrazu zgodny z systemem operacyjnym i architekturą.

## <a name="manifests-and-manifest-lists"></a>Manifesty i listy manifestów

Obrazy z obsługą wielodostępności są oparte na manifestach obrazów i listach manifestów.

### <a name="manifest"></a>Manifest

Każdy obraz kontenera jest reprezentowany przez [manifest](container-registry-concepts.md#manifest). Manifest to plik JSON, który jednoznacznie identyfikuje obraz, odwołujący się do jego warstw i odpowiednich rozmiarów. 

Podstawowy manifest obrazu systemu Linux `hello-world` wygląda podobnie do poniższego:

  ```json
  {
    "schemaVersion": 2,
    "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
    "config": {
        "mediaType": "application/vnd.docker.container.image.v1+json",
        "size": 1510,
        "digest": "sha256:fbf289e99eb9bca977dae136fbe2a82b6b7d4c372474c9235adc1741675f587e"
      },
    "layers": [
        {
          "mediaType": "application/vnd.docker.image.rootfs.diff.tar.gzip",
          "size": 977,
          "digest": "sha256:2c930d010525941c1d56ec53b97bd057a67ae1865eebf042686d2a2d18271ced"
        }
      ]
  }
  ```
    
Można wyświetlić manifest w Azure Container Registry przy użyciu Azure Portal lub narzędzi, takich jak polecenie [AZ ACR Repository show-Manifests](/cli/azure/acr/repository#az_acr_repository_show_manifests) w interfejsie wiersza polecenia platformy Azure.

### <a name="manifest-list"></a>Lista manifestów

*Lista manifestów* dla obrazu z wieloma przełożeniami (powszechnie znana jako [indeks obrazu](https://github.com/opencontainers/image-spec/blob/master/image-index.md) dla obrazów OCI) jest kolekcją (indeksem) obrazów i można ją utworzyć, określając jedną lub więcej nazw obrazów. Zawiera szczegółowe informacje o każdym z tych obrazów, takie jak obsługiwane systemy operacyjne i architektura, rozmiar i szyfrowanie manifestu. Lista manifestów może być używana w taki sam sposób jak nazwa obrazu w `docker pull` `docker run` poleceniach i. 

`docker`Interfejs wiersza polecenia zarządza manifestami i listami manifestu za pomocą narzędzia [Docker manifestu](https://docs.docker.com/engine/reference/commandline/manifest/) .

> [!NOTE]
> Obecnie `docker manifest` polecenie i podpolecenia są eksperymentalne. Zobacz dokumentację platformy Docker, aby uzyskać szczegółowe informacje na temat korzystania z eksperymentalnych poleceń.

Listę manifestów można wyświetlić za pomocą `docker manifest inspect` polecenia. Poniżej znajdują się dane wyjściowe obrazu z obsługą wielu rdzeni `mcr.microsoft.com/mcr/hello-world:latest` , który ma trzy manifesty: dwie dla architektury systemu operacyjnego Linux i jeden dla architektury systemu Windows.
```json
{
  "schemaVersion": 2,
  "mediaType": "application/vnd.docker.distribution.manifest.list.v2+json",
  "manifests": [
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 524,
      "digest": "sha256:83c7f9c92844bbbb5d0a101b22f7c2a7949e40f8ea90c8b3bc396879d95e899a",
      "platform": {
        "architecture": "amd64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 525,
      "digest": "sha256:873612c5503f3f1674f315c67089dee577d8cc6afc18565e0b4183ae355fb343",
      "platform": {
        "architecture": "arm64",
        "os": "linux"
      }
    },
    {
      "mediaType": "application/vnd.docker.distribution.manifest.v2+json",
      "size": 1124,
      "digest": "sha256:b791ad98d505abb8c9618868fc43c74aa94d08f1d7afe37d19647c0030905cae",
      "platform": {
        "architecture": "amd64",
        "os": "windows",
        "os.version": "10.0.17763.1697"
      }
    }
  ]
}
```

Gdy lista manifestów wieloskładnikowych jest przechowywana w Azure Container Registry, można także wyświetlić listę manifestów za pomocą Azure Portal lub z narzędziami takimi jak [AZ ACR Repository show-Manifests](/cli/azure/acr/repository#az_acr_repository_how_manifests) .

## <a name="import-a-multi-arch-image"></a>Importowanie obrazu z obsługą wielodostępności 

Istniejący obraz z obsługą wielodostępności można zaimportować do usługi Azure Container Registry za pomocą polecenia [AZ ACR import](/cli/azure/acr#az_acr_import) . Składnia importu obrazu jest taka sama jak w przypadku obrazu o pojedynczej architekturze. Podobnie jak w przypadku importowania obrazu o pojedynczej architekturze, importowanie obrazu z obsługą wielodostępności nie korzysta z poleceń platformy Docker. 

Aby uzyskać szczegółowe informacje, zobacz [Importowanie obrazów kontenera do rejestru kontenerów](container-registry-import-images.md).

## <a name="push-a-multi-arch-image"></a>Wypychanie obrazu z obsługą wielodostępności

W przypadku tworzenia obrazów kontenerów dla różnych architektur przy użyciu przepływów pracy kompilacji wykonaj następujące kroki, aby wypchnąć obraz z obsługą wielu rdzeni do usługi Azure Container Registry.

1. Oznacz i wypchnij każdy obraz specyficzny dla architektury do rejestru kontenerów. W poniższym przykładzie założono dwie architektury systemu Linux: arm64 i amd64. 

   ```console
   docker tag myimage:arm64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:arm64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:arm64
 
   docker tag myimage:amd64 \
     myregistry.azurecr.io/multi-arch-samples/myimage:amd64

   docker push myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ``` 

1. Uruchom `docker manifest create` , aby utworzyć listę manifestów, aby połączyć powyższe obrazy w obraz z obsługą wielodostępności.

   ```console
   docker manifest create myregistry.azurecr.io/multi-arch-samples/myimage:multi \
    myregistry.azurecr.io/multi-arch-samples/myimage:arm64 \
    myregistry.azurecr.io/multi-arch-samples/myimage:amd64
   ```

1. Wypchnij manifest do rejestru kontenerów za pomocą polecenia `docker manifest push` :

   ```console
   docker manifest push myregistry.azurecr.io/multi-arch-samples/myimage:multi
   ```

1. Użyj `docker manifest inspect` polecenia, aby wyświetlić listę manifestów. Przykład danych wyjściowych polecenia przedstawiono w poprzedniej sekcji.

Po wypchnięciu manifestu z obsługą wielodostępności do rejestru Pracuj z obrazem wielowymiarowym w taki sam sposób, jak w przypadku obrazu o pojedynczej architekturze. Na przykład Pobierz obraz przy użyciu `docker pull` polecenia [AZ ACR Repository](/cli/azure/acr/repository#az_acr_repository) Commands, aby wyświetlić Tagi, manifesty i inne właściwości obrazu.

## <a name="build-and-push-a-multi-arch-image"></a>Kompilowanie i wypychanie obrazu z obsługą wielodostępności

Za pomocą funkcji [zadań ACR](container-registry-tasks-overview.md)można kompilować i wypchnąć obraz z obsługą wielodostępności do usługi Azure Container Registry. Na przykład Zdefiniuj [zadanie wieloetapowe](container-registry-tasks-multi-step.md) w pliku YAML, który kompiluje obraz z obsługą wielodostępności systemu Linux.

W poniższym przykładzie założono, że masz oddzielne wieloetapowe dockerfile dla dwóch architektur, arm64 i amd64. Kompiluje i wypychaje obrazy charakterystyczne dla architektury, a następnie tworzy i wypychanie manifestu wieloosiowego, który ma `latest` tag:

```yml
version: v1.1.0

steps:
- build: -t {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64 -f dockerfile.arm64 . 
- build: -t {{.Run.Registry}}/multi-arch-samples/myyimage:{{.Run.ID}}-arm64 -f dockerfile.amd64 . 
- push: 
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    - {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: >
    docker manifest create
    {{.Run.Registry}}/multi-arch-samples/myimage:latest
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-arm64
    {{.Run.Registry}}/multi-arch-samples/myimage:{{.Run.ID}}-amd64
- cmd: docker manifest push --purge {{.Run.Registry}}/multi-arch-samples/myimage:latest
- cmd: docker manifest inspect {{.Run.Registry}}/multi-arch-samples/myimage:latest
```

## <a name="next-steps"></a>Następne kroki

* Użyj [Azure Pipelines](/azure/devops/pipelines/get-started/what-is-azure-pipelines) , aby skompilować obrazy kontenerów dla różnych architektur.
* Dowiedz się więcej na temat tworzenia obrazów na wielu platformach za pomocą eksperymentalnej wtyczki [Buildx](https://docs.docker.com/buildx/working-with-buildx/) Docker.

<!-- LINKS - external -->
[docker-linux]: https://docs.docker.com/engine/installation/#supported-platforms
[docker-mac]: https://docs.docker.com/docker-for-mac/
[docker-windows]: https://docs.docker.com/docker-for-windows/
