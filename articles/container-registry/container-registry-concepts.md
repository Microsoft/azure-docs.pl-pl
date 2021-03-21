---
title: Rejestry, repozytoria, obrazy i artefakty — informacje
description: Wprowadzenie do kluczowych pojęć dotyczących rejestrów kontenerów platformy Azure, repozytoriów, obrazów kontenerów i innych artefaktów.
ms.topic: article
ms.date: 01/29/2021
ms.openlocfilehash: 991be79b10b6061f2034eb19e4e139af65aef3cf
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100578108"
---
# <a name="about-registries-repositories-and-artifacts"></a>Rejestry, repozytoria i artefakty — informacje

W tym artykule przedstawiono kluczowe pojęcia dotyczące rejestrów kontenerów, repozytoriów i obrazów kontenerów oraz powiązanych artefaktów. 

:::image type="content" source="media/container-registry-concepts/registry-elements.png" alt-text="Rejestr, repozytoria i artefakty":::

## <a name="registry"></a>Rejestr

*Rejestr* kontenerów to usługa, która przechowuje i dystrybuuje obrazy kontenerów i powiązane artefakty. Centrum platformy Docker to przykład publicznego rejestru kontenerów, który służy jako ogólny katalog obrazów kontenera Docker. Azure Container Registry zapewnia użytkownikom bezpośrednią kontrolę nad zawartością kontenera, z uwierzytelnianiem zintegrowanym, [replikacją geograficzną](container-registry-geo-replication.md) obsługującą globalną dystrybucję i niezawodność wdrożeń w zamknięciu sieci, [konfiguracji sieci wirtualnej z linkiem prywatnym](container-registry-private-link.md), [blokowaniem tagów](container-registry-image-lock.md)i wieloma innymi ulepszonymi funkcjami. 

Oprócz obrazów kontenerów zgodnych z platformą Docker Azure Container Registry obsługuje zakres [artefaktów zawartości](container-registry-image-formats.md) , w tym wykresy Helm i formaty obrazów Open Container INITIATIVE (OCI).

## <a name="repository"></a>Repozytorium

*Repozytorium* jest kolekcją obrazów kontenerów lub innych artefaktów w rejestrze, które mają taką samą nazwę, ale różne Tagi. Na przykład następujące trzy obrazy znajdują się w `acr-helloworld` repozytorium:

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

## <a name="artifact"></a>Artefakt

Obraz kontenera lub inny artefakt w rejestrze jest skojarzony z co najmniej jednym tagiem, ma jedną lub więcej warstw i jest identyfikowany przez manifest. Zrozumienie, jak te składniki są powiązane ze sobą, mogą pomóc w skutecznym zarządzaniu rejestrem.

### <a name="tag"></a>Tag

*Tag* obrazu lub innego artefaktu określa jego wersję. Pojedynczym artefaktem w repozytorium może być przypisany jeden lub wiele tagów i może być również "nieoznakowany". Oznacza to, że można usunąć wszystkie Tagi z obrazu, podczas gdy dane obrazu (jego warstwy) pozostają w rejestrze.

Repozytorium (lub repozytorium i przestrzeń nazw) oraz tag definiują nazwę obrazu. Można wypchnąć i ściągnąć obraz, określając jego nazwę w operacji wypychania lub ściągania. Tag `latest` jest używany domyślnie, jeśli nie podano go w poleceniach platformy Docker.

Sposób oznaczania obrazów kontenerów jest kierowany przez scenariusze w celu ich opracowania lub wdrożenia. Na przykład, stałe Tagi są zalecane do obsługi obrazów podstawowych i unikatowych tagów służących do wdrażania obrazów. Aby uzyskać więcej informacji, zobacz [zalecenia dotyczące tagowania i przechowywania wersji obrazów kontenerów](container-registry-image-tag-version.md).

Aby zapoznać się z regułami nazewnictwa tagów, zobacz [dokumentację platformy Docker](https://docs.docker.com/engine/reference/commandline/tag/).

### <a name="layer"></a>Warstwa

Obrazy kontenerów i artefakty składają się z co najmniej jednej *warstwy*. Różne typy artefaktów definiują warstwy w różny sposób. Na przykład w obrazie kontenera Docker każda warstwa odpowiada wierszowi w pliku dockerfile, który definiuje obraz:

:::image type="content" source="media/container-registry-concepts/container-image-layers.png" alt-text="Warstwy obrazu kontenera":::

Artefakty w rejestrze udostępniają typowe warstwy, zwiększając wydajność magazynu. Na przykład kilka obrazów w różnych repozytoriach może mieć wspólną ASP.NET Core warstwę bazową, ale w rejestrze jest przechowywana tylko jedna kopia tej warstwy. Udostępnianie warstwowe optymalizuje także dystrybucję warstw do węzłów z wieloma artefaktami udostępniającymi wspólne warstwy. Jeśli obraz już znajdujący się w węźle zawiera warstwę ASP.NET Coreną jako podstawową, kolejna ściąganie innego obrazu odwołującego się do tej samej warstwy nie przenosi warstwy do węzła. Zamiast tego odwołuje się do warstwy już istniejącej w węźle.

Aby zapewnić bezpieczną izolację i ochronę przed potencjalną manipulacją warstwową, warstwy nie są udostępniane w rejestrach.

### <a name="manifest"></a>Manifest

Każdy obraz kontenera lub artefakt wypychany do rejestru kontenerów jest skojarzony z *manifestem*. Manifest generowany przez rejestr podczas wypychania zawartości, jednoznacznie identyfikuje artefakty i określa warstwy. Można wyświetlić listę manifestów dla repozytorium za pomocą poleceń interfejsu wiersza polecenia platformy Azure [AZ ACR Repository show-Manifests][az-acr-repository-show-manifests]. 

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

Manifesty są identyfikowane przez unikatowy skrót SHA-256 lub *skrót manifestu*. Każdy obraz lub artefakt — wskazuje, czy jest oznaczony lub nie--jest identyfikowany przez jego skrót. Wartość skrótu jest unikatowa, nawet jeśli dane warstwy artefaktu są takie same jak w przypadku innego artefaktu. Ten mechanizm pozwala na wielokrotne wypychanie oznakowanych identycznie obrazów do rejestru. Na przykład można wielokrotnie wypchnąć `myimage:latest` do rejestru bez błędu, ponieważ każdy obraz jest identyfikowany przez jego unikatowy skrót.

Artefakt można ściągnąć z rejestru, określając jego skrót w operacji ściągania. Niektóre systemy można skonfigurować do ściągania przez szyfrowanie, ponieważ gwarantuje ściąganie wersji obrazu, nawet jeśli do rejestru zostanie przesunięty identyczny obraz oznakowany.

> [!IMPORTANT]
> Jeśli wielokrotnie wypychasz zmodyfikowane artefakty z identycznymi tagami, możesz utworzyć "oddzielone"--artefakty, które są nieoznakowane, ale nadal zużywają miejsce w rejestrze. Obrazy nieoznakowane nie są wyświetlane w interfejsie wiersza polecenia platformy Azure ani w Azure Portal, gdy wyświetlają lub wyświetlają obrazy według tagów. Jednak ich warstwy nadal istnieją i zużywają miejsce w rejestrze. Usunięcie nieoznaczonego obrazu zwalnia miejsce w rejestrze, gdy manifest jest jedynym, lub ostatnim, wskazując konkretną warstwę. Aby uzyskać informacje o zwalnianiu miejsca używanego przez nieoznakowane obrazy, zobacz [usuwanie obrazów kontenerów w Azure Container Registry](container-registry-delete.md).

## <a name="addressing-an-artifact"></a>Adresowanie artefaktu

Aby rozwiązać artefakt rejestru dla operacji wypychania i ściągania za pomocą platformy Docker lub innych narzędzi klienckich, należy połączyć w pełni kwalifikowaną nazwę rejestru, nazwę repozytorium (w tym ścieżkę przestrzeni nazw, jeśli ma zastosowanie), oraz tag artefaktu lub skrót manifestu. Zobacz poprzednie sekcje, aby poznać wyjaśnienie tych warunków.

  **Adres według tagu**: `[loginServerUrl]/[repository][:tag]`
    
  **Adres według skrótu**: `[loginServerUrl]/[repository@sha256][:digest]`  

Korzystając z narzędzia Docker lub innych narzędzi klienckich do ściągania lub wypychania artefaktów do usługi Azure Container Registry, należy użyć w pełni kwalifikowanego adresu URL w rejestrze, nazywanego również nazwą *serwera logowania* . W chmurze platformy Azure w pełni kwalifikowany adres URL usługi Azure Container Registry jest w formacie `myregistry.azurecr.io` (wszystkie małe litery).

> [!NOTE]
> * Nie można określić numeru portu w adresie URL serwera logowania rejestru, takiego jak `myregistry.azurecr.io:443` . 
> * Tag `latest` jest używany domyślnie, jeśli nie podano znacznika w poleceniu.  

   
### <a name="push-by-tag"></a>Wypchnij przez tag

Przykłady: 

   `docker push myregistry.azurecr.io/samples/myimage:20210106`

   `docker push myregistry.azurecr.io/marketing/email-sender`

### <a name="pull-by-tag"></a>Ściągaj przez tag

Przykład: 

  `docker pull myregistry.azurecr.io/marketing/campaign10-18/email-sender:v2`

### <a name="pull-by-manifest-digest"></a>Ściągnij przez podsumowanie manifestu


Przykład:

  `docker pull myregistry.azurecr.io/acr-helloworld@sha256:0a2e01852872580b2c2fea9380ff8d7b637d3928783c55beb3f21a6e58d5d108`



## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [magazynie rejestru](container-registry-storage.md) i [obsługiwanych formatach zawartości](container-registry-image-formats.md) w Azure Container Registry.

Informacje na temat [wypychania i ściągania obrazów](container-registry-get-started-docker-cli.md) z Azure Container Registry.

<!-- LINKS - Internal -->
[az-acr-repository-show-manifests]: /cli/azure/acr/repository#az-acr-repository-show-manifests


