---
title: Informacje o rejestrach, repozytoriach, obrazach i artefaktach
description: Wprowadzenie do kluczowych pojęć dotyczących rejestrów kontenerów platformy Azure, repozytoriów, obrazów kontenerów i innych artefaktów.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 64ab3812b3f23a7b3a480d3530c82bd39f2d29a5
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784087"
---
# <a name="about-registries-repositories-and-artifacts"></a>Informacje o rejestrach, repozytoriach i artefaktach

W tym artykule oprowadzono kluczowe pojęcia dotyczące rejestrów kontenerów, repozytoriów, obrazów kontenerów i powiązanych artefaktów. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Rejestr, repozytoria i artefakty":::

## <a name="registry"></a>Rejestr

Rejestr *kontenerów to* usługa, która przechowuje i dystrybuuje obrazy kontenerów i powiązane artefakty. Docker Hub to przykład publicznego rejestru kontenerów, który służy jako ogólny wykaz obrazów kontenerów platformy Docker. Azure Container Registry zapewnia użytkownikom bezpośrednią kontrolę nad zawartością kontenera dzięki [](container-registry-geo-replication.md) zintegrowanemu uwierzytelnianiu, replikacji geograficznej, która obsługuje globalną dystrybucję i niezawodność wdrożeń w pobliżu sieci, konfiguracji sieci wirtualnej z [Private Link,](container-registry-image-lock.md)blokowaniem tagów i wieloma innymi [rozszerzonymi](container-registry-private-link.md)funkcjami. 

Oprócz obrazów kontenerów zgodnych z platformą Docker program Azure Container Registry różne artefakty zawartości, w tym wykresy Helm i formaty obrazów Open Container Initiative (OCI). [](container-registry-image-formats.md)

## <a name="repository"></a>Repozytorium

Repozytorium *to* kolekcja obrazów kontenerów lub innych artefaktów w rejestrze, które mają taką samą nazwę, ale różne tagi. Na przykład następujące trzy obrazy znajdują się w `acr-helloworld` repozytorium:

- *acr-helloworld:latest*
- *acr-helloworld:v1*
- *acr-helloworld:v2*

Nazwy repozytoriów mogą również obejmować [przestrzenie nazw](container-registry-best-practices.md#repository-namespaces). Przestrzenie nazw umożliwiają identyfikowanie powiązanych repozytoriów i własności artefaktów w organizacji przy użyciu nazw rozdzielanych ukośnikami. Jednak rejestr zarządza wszystkimi repozytoriami niezależnie, a nie jako hierarchią. Na przykład:

- *marketing/campaign10-18/web:v2*
- *marketing/campaign10-18/api:v3*
- *marketing/campaign10-18/email-sender:v2*
- *product-returns/web-submission:20180604*
- *product-returns/legacy-integrator:20180715*

Nazwy repozytorium mogą zawierać tylko małe litery alfanumeryczne, kropki, kreski, podkreślenia i ukośniki. 

Aby uzyskać pełne reguły nazewnictwa repozytorium, zobacz [Specyfikacja dystrybucji open container initiative distribution](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="artifact"></a>Artefakt

Obraz kontenera lub inny artefakt w rejestrze jest skojarzony z co najmniej jednym tagiem, ma co najmniej jedną warstwę i jest identyfikowany przez manifest. Zrozumienie relacji między tymi składnikami może pomóc w efektywnym zarządzaniu rejestrem.

### <a name="tag"></a>Tag

Tag *obrazu* lub innego artefaktu określa jego wersję. Pojedyncze artefakty w repozytorium mogą być przypisane do jednego lub wielu tagów, a także mogą być "nieotagowane". Oznacza to, że można usunąć wszystkie tagi z obrazu, podczas gdy dane obrazu (jego warstwy) pozostają w rejestrze.

Repozytorium (lub repozytorium i przestrzeń nazw) oraz tag definiują nazwę obrazu. Możesz wypchnąć i ściągnąć obraz, określając jego nazwę w operacji wypychania lub ściągania. Tag jest używany domyślnie, jeśli nie powiesz go `latest` w poleceniach platformy Docker.

Sposób tagów obrazów kontenerów jest kierowany przez scenariusze ich opracowywania lub wdrażania. Na przykład stabilne tagi są zalecane do obsługi obrazów podstawowych i unikatowe tagi do wdrażania obrazów. Aby uzyskać więcej informacji, zobacz [Zalecenia dotyczące tagowania i wersjonarowania obrazów kontenerów.](container-registry-image-tag-version.md)

Aby uzyskać informacje na temat reguł nazewnictwa tagów, zobacz [dokumentację platformy Docker.](https://docs.docker.com/engine/reference/commandline/tag/)

### <a name="layer"></a>Warstwa

Obrazy kontenerów i artefakty składa się z co najmniej jednej *warstwy*. Różne typy artefaktów definiują warstwy w różny sposób. Na przykład w obrazie kontenera platformy Docker każda warstwa odpowiada wierszowi w pliku Dockerfile, który definiuje obraz:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Warstwy obrazu kontenera":::

Artefakty w rejestrze współdzielą typowe warstwy, zwiększając wydajność magazynu. Na przykład kilka obrazów w różnych repozytoriach może mieć wspólną warstwę podstawową ASP.NET Core, ale w rejestrze jest przechowywana tylko jedna kopia tej warstwy. Udostępnianie warstw optymalizuje również dystrybucję warstw do węzłów, a wiele artefaktów współużytkuje wspólne warstwy. Jeśli obraz już w węźle zawiera warstwę ASP.NET Core jako bazę, kolejne ściągnięcie innego obrazu odwołującego się do tej samej warstwy nie spowoduje przeniesienia warstwy do węzła. Zamiast tego odwołuje się do warstwy istniejącej już w węźle.

Aby zapewnić bezpieczną izolację i ochronę przed potencjalnym manipulowaniem warstwami, warstwy nie są udostępniane w rejestrach.

### <a name="manifest"></a>Manifest

Każdy obraz kontenera lub artefakt wypchniętą do rejestru kontenerów jest skojarzony z *manifestem*. Manifest generowany przez rejestr podczas wypychania zawartości unikatowo identyfikuje artefakty i określa warstwy. Manifesty dla repozytorium można wyświetlić za pomocą polecenia [az acr repository show-manifests][az-acr-repository-show-manifests]interfejsu wiersza polecenia platformy Azure. 

Podstawowy manifest dla obrazu systemu Linux `hello-world` wygląda podobnie do następującego:

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

Manifesty dla repozytorium można wyświetlić za pomocą polecenia [az acr repository show-manifests][az-acr-repository-show-manifests]interfejsu wiersza polecenia platformy Azure:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Na przykład wyliczysz manifesty dla repozytorium "acr-helloworld":

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
  },
  {
    "digest": "sha256:7ca0e0ae50c95155dbb0e380f37d7471e98d2232ed9e31eece9f9fb9078f2728",
    "tags": [
      "v1"
    ],
    "timestamp": "2018-07-11T21:38:35.9170967Z"
  }
]
```

### <a name="manifest-digest"></a>Podsumowanie manifestu

Manifesty są identyfikowane przez unikatowy skrót SHA-256 lub *skrót manifestu*. Każdy obraz lub artefakt — niezależnie od tego, czy został otagowany, czy nie — jest identyfikowany przez jego podsumowanie. Wartość skrótu jest unikatowa, nawet jeśli dane warstwy artefaktu są identyczne z danymi innego artefaktu. Ten mechanizm umożliwia wielokrotne wypychanie identycznych obrazów oznaczonych tagami do rejestru. Na przykład można wielokrotnie wypychać do rejestru bez błędu, ponieważ każdy obraz jest identyfikowany przez `myimage:latest` jego unikatowy skrót.

Możesz ściągnąć artefakt z rejestru, określając jego skrót w operacji ściągania. Niektóre systemy mogą być skonfigurowane do ściągania za pomocą skrótu, ponieważ gwarantuje to ściąganie wersji obrazu, nawet jeśli identyczny otagowany obraz zostanie później wypchnięty do rejestru.

> [!IMPORTANT]
> Jeśli wielokrotnie wypychasz zmodyfikowane artefakty z identycznymi tagami, możesz utworzyć "oddzielone" artefakty, które są nieotagowane, ale nadal zużywają miejsce w rejestrze. Nieotagowane obrazy nie są wyświetlane w interfejsie wiersza polecenia platformy Azure ani w Azure Portal podczas wyświetlania lub wyświetlania obrazów według tagów. Jednak ich warstwy nadal istnieją i zużywają miejsce w rejestrze. Usunięcie obrazu bez flagi usuwa miejsce w rejestrze, gdy manifest jest jedynym lub ostatnim, co wskazują na określoną warstwę. Aby uzyskać informacje o usuwaniu miejsca używanego przez nieoznakowane obrazy, zobacz [Usuwanie obrazów kontenerów](container-registry-delete.md)w Azure Container Registry .

## <a name="addressing-an-artifact"></a>Adresowanie artefaktu

Aby adresować artefakt rejestru dla operacji wypychania i ściągania przy użyciu platformy Docker lub innych narzędzi klienckich, połącz w pełni kwalifikowaną nazwę rejestru, nazwę repozytorium (w tym ścieżkę przestrzeni nazw, jeśli ma zastosowanie) oraz tag artefaktu lub podsumowanie manifestu. Opisy tych terminów można znaleźć w poprzednich sekcjach.

  **Adres według tagu:**`[loginServerUrl]/[repository][:tag]`
    
  **Adres według skrótu:**`[loginServerUrl]/[repository@sha256][:digest]`  

W przypadku używania platformy Docker lub innych narzędzi klienckich do ściągania lub wypychania artefaktów do rejestru kontenerów platformy Azure użyj w pełni kwalifikowanego adresu URL rejestru, nazywanego również nazwą *serwera* logowania. W chmurze platformy Azure w pełni kwalifikowany adres URL rejestru kontenerów platformy Azure ma format `myregistry.azurecr.io` (wszystkie małe litery).

> [!NOTE]
> * W adresie URL serwera logowania rejestru nie można określić numeru portu, takiego jak `myregistry.azurecr.io:443` . 
> * Tag jest używany domyślnie, jeśli nie powiesz `latest` tagu w poleceniu.  

   
### <a name="push-by-tag"></a>Wypychanie według tagu

Przykłady: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Ściąganie według tagu

Przykład: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Ściąganie według skrótu manifestu


Przykład:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [magazynie rejestru i](container-registry-storage.md) [obsługiwanych formatach zawartości](container-registry-image-formats.md) w Azure Container Registry.

Dowiedz się, jak [wypychać i ściągać obrazy](container-registry-get-started-docker-cli.md) z Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az_acr_repository_show_manifests
