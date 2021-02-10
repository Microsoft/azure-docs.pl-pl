---
title: Informacje o repozytoriach & obrazów
description: Wprowadzenie do kluczowych pojęć związanych z rejestrami kontenerów platformy Azure, repozytoriami i obrazami kontenerów.
ms.topic: article
ms.date: 06/16/2020
ms.openlocfilehash: 0cc7df22236c60bd473385d92c8db563be68f688
ms.sourcegitcommit: 49ea056bbb5957b5443f035d28c1d8f84f5a407b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/09/2021
ms.locfileid: "100008523"
---
# <a name="about-registries-repositories-and-images"></a>Rejestry, repozytoria i obrazy — informacje

W tym artykule przedstawiono kluczowe pojęcia dotyczące rejestrów kontenerów, repozytoriów i obrazów kontenerów oraz powiązanych artefaktów. 

## <a name="registry"></a>Rejestr

*Rejestr* kontenerów to usługa, która przechowuje i dystrybuuje obrazy kontenerów. Docker Hub to publiczny rejestr kontenerów, który obsługuje społeczność typu open source i służy jako ogólny katalog obrazów. Azure Container Registry zapewnia użytkownikom bezpośrednią kontrolę nad ich obrazami, z uwierzytelnianiem zintegrowanym, [replikacją geograficzną](container-registry-geo-replication.md) obsługującą globalną dystrybucję i niezawodność dla wdrożeń w zamknięciu sieci, [konfiguracji sieci wirtualnej i zapory](container-registry-vnet.md), [blokowania tagów](container-registry-image-lock.md)oraz wielu innych ulepszonych funkcji. 

Oprócz obrazów kontenerów platformy Docker Azure Container Registry obsługuje powiązane [artefakty zawartości](container-registry-image-formats.md) , w tym formaty obrazów Open Container INITIATIVE (OCI).

## <a name="content-addressable-elements-of-an-artifact"></a>Elementy do adresowania artefaktu

Adres artefaktu w rejestrze kontenerów platformy Azure obejmuje następujące elementy. 

`[loginUrl]/[repository:][tag]`

* **LoginUrl** — w pełni kwalifikowana nazwa hosta rejestru. Host rejestru w usłudze Azure Container Registry *ma format azurecr.IO*(wszystkie małe litery). Należy określić loginUrl podczas korzystania z platformy Docker lub innych narzędzi klienckich do ściągania i wypychania artefaktów do usługi Azure Container Registry. 
* **repozytorium** — nazwa logicznej grupy składającej się z co najmniej jednego powiązanego obrazu lub artefaktów — na przykład obrazy aplikacji lub podstawowego systemu operacyjnego. Może zawierać ścieżkę *przestrzeni nazw* . 
* **tag** -identyfikator określonej wersji obrazu lub artefaktu przechowywanego w repozytorium.

Na przykład Pełna nazwa obrazu w rejestrze kontenerów platformy Azure może wyglądać następująco:

*myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2*

Więcej informacji o tych elementach można znaleźć w poniższych sekcjach.

## <a name="repository-name"></a>Nazwa repozytorium

*Repozytorium* jest kolekcją obrazów kontenerów lub innych artefaktów o tej samej nazwie, ale różne Tagi. Na przykład następujące trzy obrazy znajdują się w repozytorium "ACR-HelloWorld":


- *ACR-HelloWorld: Najnowsze*
- *ACR — HelloWorld: V1*
- *ACR — HelloWorld: V2*

Nazwy repozytoriów mogą również zawierać [przestrzenie nazw](container-registry-best-practices.md#repository-namespaces). Przestrzenie nazw umożliwiają identyfikowanie powiązanych repozytoriów i własności artefaktów w organizacji przy użyciu nazw rozdzielanych ukośnikami. Jednak rejestr zarządza wszystkimi repozytoriami niezależnie, a nie jako hierarchią. Na przykład:

- *Marketing/campaign10 — 18/sieć Web: V2*
- *Marketing/campaign10 — 18/API: v3*
- *Marketing/campaign10-18/email-Sender: V2*
- *produkt — zwraca/przesyła do sieci Web: 20180604*
- *Product-Returns/Legacy-Integrator: 20180715*

Nazwy repozytoriów mogą zawierać tylko małe znaki alfanumeryczne, kropki, łączniki, podkreślenia i ukośniki. 

Aby zapoznać się z pełnymi regułami nazewnictwa repozytorium, zobacz artykuł [Specyfikacja dystrybucji z inicjatywy Open Container Initiative](https://github.com/docker/distribution/blob/master/docs/spec/api.md#overview).

## <a name="image"></a>Image (Obraz)

Obraz kontenera lub inny artefakt w rejestrze jest skojarzony z co najmniej jednym tagiem, ma jedną lub więcej warstw i jest identyfikowany przez manifest. Zrozumienie, jak te składniki są powiązane ze sobą, mogą pomóc w skutecznym zarządzaniu rejestrem.

### <a name="tag"></a>Tag

*Tag* obrazu lub innego artefaktu określa jego wersję. Pojedynczym artefaktem w repozytorium może być przypisany jeden lub wiele tagów i może być również "nieoznakowany". Oznacza to, że można usunąć wszystkie Tagi z obrazu, podczas gdy dane obrazu (jego warstwy) pozostają w rejestrze.

Repozytorium (lub repozytorium i przestrzeń nazw) oraz tag definiują nazwę obrazu. Można wypchnąć i ściągnąć obraz, określając jego nazwę w operacji wypychania lub ściągania. Tag `latest` jest używany domyślnie, jeśli nie podano go w poleceniach platformy Docker.

Sposób oznaczania obrazów kontenerów jest kierowany przez scenariusze w celu ich opracowania lub wdrożenia. Na przykład, stałe Tagi są zalecane do obsługi obrazów podstawowych i unikatowych tagów służących do wdrażania obrazów. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące tagowania i przechowywania wersji obrazów kontenerów](container-registry-image-tag-version.md).

Aby zapoznać się z regułami nazewnictwa tagów, zobacz [dokumentację platformy Docker](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Warstwa

Obrazy kontenerów składają się z jednej lub kilku *warstw*, z których każdy odpowiada wierszowi w pliku dockerfile, który definiuje obraz. Obrazy w rejestrze udostępniają typowe warstwy, zwiększając wydajność magazynu. Na przykład kilka obrazów w różnych repozytoriach może współużytkować tę samą warstwę bazową Alpine Linux, ale w rejestrze jest przechowywana tylko jedna kopia tej warstwy.

Udostępnianie warstwy optymalizuje także dystrybucję warstw do węzłów z wieloma obrazami udostępniającymi wspólne warstwy. Na przykład jeśli obraz już znajdujący się w węźle zawiera warstwę Alpine Linux jako podstawową, kolejna ściąganie innego obrazu odwołującego się do tej samej warstwy nie przenosi warstwy do węzła. Zamiast tego odwołuje się do warstwy już istniejącej w węźle.

Aby zapewnić bezpieczną izolację i ochronę przed potencjalną manipulacją warstwową, warstwy nie są udostępniane w rejestrach.

### <a name="manifest"></a>Manifest

Każdy obraz kontenera lub artefakt wypychany do rejestru kontenerów jest skojarzony z *manifestem*. Manifest generowany przez rejestr podczas wypychania obrazu, jednoznacznie identyfikuje obraz i określa jego warstwy. 

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

Można wyświetlić listę manifestów dla repozytorium za pomocą poleceń interfejsu wiersza polecenia platformy Azure [AZ ACR Repository show-Manifests][az-acr-repository-show-manifests]:

```azurecli
az acr repository show-manifests --name <acrName> --repository <repositoryName>
```

Na przykład Wyświetl listę manifestów dla repozytorium "ACR-HelloWorld":

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

### <a name="manifest-digest"></a>Szyfrowanie manifestu

Manifesty są identyfikowane przez unikatowy skrót SHA-256 lub *skrót manifestu*. Każdy obraz lub artefakt — wskazuje, czy jest oznaczony lub nie--jest identyfikowany przez jego skrót. Wartość skrótu jest unikatowa, nawet jeśli dane warstwy obrazu są takie same jak w przypadku innego obrazu. Ten mechanizm pozwala na wielokrotne wypychanie oznakowanych identycznie obrazów do rejestru. Na przykład można wielokrotnie wypchnąć `myimage:latest` do rejestru bez błędu, ponieważ każdy obraz jest identyfikowany przez jego unikatowy skrót.

Można ściągnąć obraz z rejestru, określając jego skrót w operacji ściągania. Niektóre systemy można skonfigurować do ściągania przez szyfrowanie, ponieważ gwarantuje ściąganie wersji obrazu, nawet jeśli obraz z identycznymi znacznikami jest następnie wypychany do rejestru.

Na przykład Pobierz obraz z repozytorium "ACR-HelloWorld" według skrótu manifestu:

`docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`

> [!IMPORTANT]
> W przypadku wielokrotnego wypychania zmodyfikowanych obrazów z identycznymi tagami można utworzyć oddzielone obrazy — obrazy, które są nieoznakowane, ale nadal zużywają miejsce w rejestrze. Obrazy nieoznakowane nie są wyświetlane w interfejsie wiersza polecenia platformy Azure ani w Azure Portal, gdy wyświetlają lub wyświetlają obrazy według tagów. Jednak ich warstwy nadal istnieją i zużywają miejsce w rejestrze. Usunięcie nieoznaczonego obrazu zwalnia miejsce w rejestrze, gdy manifest jest jedynym, lub ostatnim, wskazując konkretną warstwę. Aby uzyskać informacje o zwalnianiu miejsca używanego przez nieoznakowane obrazy, zobacz [usuwanie obrazów kontenerów w Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o usłudze [Image Storage](container-registry-storage.md) i [obsługiwanych formatach zawartości](container-registry-image-formats.md) w Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


