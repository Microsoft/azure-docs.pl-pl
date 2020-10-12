---
title: Najlepsze praktyki w zakresie rejestru
description: Dowiedz się, jak efektywnie korzystać z usługi Azure Container Registry dzięki zastosowaniu tych najlepszych rozwiązań.
ms.topic: article
ms.date: 09/27/2018
ms.openlocfilehash: fc84fb8cb98f58e28570095370d55a7358ce3a99
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "83682688"
---
# <a name="best-practices-for-azure-container-registry"></a>Najlepsze rozwiązania dla usługi Azure Container Registry

Stosując te najlepsze rozwiązania, można osiągnąć maksymalną wydajność i oszczędność kosztów podczas korzystania z rejestru prywatnego platformy Docker na platformie Azure.

Zapoznaj się również z [zaleceniami dotyczącymi tagowania i przechowywania wersji obrazów kontenerów](container-registry-image-tag-version.md) w celu uzyskania strategii tagów i wersji obrazów w rejestrze. 

## <a name="network-close-deployment"></a>Wdrażanie w pobliskiej sieci

Utwórz rejestr kontenerów w tym samym regionie świadczenia usług Azure, w którym zostały wdrożone kontenery. Dzięki umieszczeniu rejestru w regionie blisko sieci, w której hostowane są kontenery, można zmniejszyć opóźnienia i obniżyć koszty.

Wdrożenie w pobliskiej sieci jest jednym z głównych powodów używania prywatnego rejestru kontenerów. Obrazy platformy Docker mają wydajną [konstrukcję warstw](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), która umożliwia przeprowadzanie wdrożeń przyrostowych. Jednak nowe węzły muszą ściągać wszystkie warstwy wymagane dla danego obrazu. Ten początkowy `docker pull` może szybko powiększyć się do wielu gigabajtów. Posiadanie prywatnego rejestru blisko wdrożenia minimalizuje opóźnienia sieci.
Ponadto wszystkich chmury publiczne, w tym Azure, stosują opłaty za transfer danych wychodzących z sieci. Ściąganie obrazów z jednego centrum danych do innego skutkuje dodatkowymi opłatami za dane wychodzące z sieci, a poza tym zwiększa opóźnienia.

## <a name="geo-replicate-multi-region-deployments"></a>Replikacja geograficzna wdrożeń w wielu regionach

Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) usługi Azure Container Registry, jeśli wdrażasz kontenery w wielu regionach. Bez względu na to, czy obsługujesz globalnych klientów z lokalnych centrów danych, czy też Twój zespół deweloperów znajduje się w różnych lokalizacjach, możesz uprościć zarządzanie rejestrem i zminimalizować opóźnienia dzięki replikacji geograficznej rejestru. Replikacja geograficzna jest dostępna tylko w przypadku rejestrów w wersji [Premium](container-registry-skus.md).

Aby dowiedzieć się, jak korzystać z replikacji geograficznej, zobacz trzyczęściowy samouczek [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md).

## <a name="repository-namespaces"></a>Przestrzenie nazw repozytoriów

Dzięki wykorzystaniu przestrzeni nazw repozytoriów można zezwolić na udostępnianie jednego rejestru w wielu grupach w organizacji. Rejestry mogą być współużytkowane przez wdrożenia i zespoły. Usługa Azure Container Registry obsługuje zagnieżdżone przestrzenie nazw, umożliwiając izolację grup.

Rozważmy na przykład poniższe znaczniki obrazów kontenera. Obrazy używane w całej firmie, takie jak `aspnetcore` , są umieszczane w głównej przestrzeni nazw, natomiast obrazy kontenerów należące do poszczególnych produktów i grup marketingowych używają własnych przestrzeni nazw.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedykowana grupa zasobów

Ponieważ rejestry kontenerów to zasoby, które są używane na wielu hostach kontenerów, Rejestr powinien znajdować się w własnej grupie zasobów.

Choć możesz eksperymentować z określonym typem hosta, na przykład z usługą Azure Container Instances, prawdopodobnie zechcesz usunąć wystąpienie kontenera po zakończeniu pracy z nim. Można jednak również zachować kolekcję obrazów, które zostały wypchnięte do usługi Azure Container Registry. Dzięki umieszczeniu rejestru w jego własnej grupie zasobów można zminimalizować ryzyko przypadkowego usunięcia kolekcji obrazów w rejestrze podczas usuwania grupy zasobów wystąpienia kontenera.

## <a name="authentication"></a>Uwierzytelnianie

Istnieją dwa podstawowe scenariusze uwierzytelniania w usłudze Azure Container Registry: uwierzytelnianie indywidualne i uwierzytelnianie usługi („bezobsługowe”). Poniższa tabela zawiera krótkie omówienie tych scenariuszy i wskazuje zalecaną metodę uwierzytelniania w przypadku każdego z nich.

| Type | Przykładowy scenariusz | Zalecana metoda |
|---|---|---|
| Indywidualne tożsamości | Deweloper ściągający obrazy na swoją maszynę deweloperską lub wypychający z niej obrazy. | Polecenie [az acr login](/cli/azure/acr?view=azure-cli-latest#az-acr-login) |
| Bezobsługowe/tożsamość usługi | Potoki kompilacji i wdrażania, w których użytkownik nie bierze bezpośrednio udziału. | [Nazwa główna usługi](container-registry-authentication.md#service-principal) |

Aby uzyskać szczegółowe informacje o uwierzytelnianiu w usłudze Azure Container Registry, zobacz [Authenticate with an Azure container registry](container-registry-authentication.md) (Uwierzytelnianie w usłudze Azure Container Registry).

## <a name="manage-registry-size"></a>Zarządzanie rozmiarem rejestru

Ograniczenia magazynu dla każdej [warstwy usługi Rejestr kontenerów][container-registry-skus] są przeznaczone do wyrównania w typowym scenariuszu: **podstawowe** dla rozpoczynania, **standardowego** dla większości aplikacji produkcyjnych oraz **Premium** w przypadku wydajności i [replikacji geograficznej][container-registry-geo-replication]funkcji Hyper-Skala. W ciągu cyklu życia rejestru należy zarządzać jego rozmiarem, okresowo usuwając nieużywaną zawartość.

Użyj interfejsu wiersza polecenia platformy Azure [AZ ACR show-Usage][az-acr-show-usage] , aby wyświetlić bieżący rozmiar rejestru:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Bieżący magazyn używany w **przeglądzie** rejestru można również znaleźć w Azure Portal:

![Informacje o użyciu rejestru w witrynie Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Usuń dane obrazu

Azure Container Registry obsługuje kilka metod usuwania danych obrazu z rejestru kontenerów. Można usuwać obrazy według tagów lub skrótu manifestu albo usuwać całe repozytorium.

Aby uzyskać szczegółowe informacje na temat usuwania danych obrazu z rejestru, w tym nieoznakowany (czasami nazywany "zawieszonego" lub "oddzielony"), zobacz [usuwanie kontenerów obrazów w Azure Container Registry](container-registry-delete.md).

## <a name="next-steps"></a>Następne kroki

Azure Container Registry jest dostępna w kilku warstwach (nazywanych również jednostkami SKU), które zapewniają różne możliwości. Aby uzyskać szczegółowe informacje o dostępnych warstwach usług, zobacz [Azure Container Registry warstwy usług](container-registry-skus.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az-acr-repository-delete
[az-acr-show-usage]: /cli/azure/acr#az-acr-show-usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md
