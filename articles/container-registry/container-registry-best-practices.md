---
title: Najlepsze praktyki w zakresie rejestru
description: Dowiedz się, jak efektywnie korzystać z usługi Azure Container Registry dzięki zastosowaniu tych najlepszych rozwiązań.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 01c8c7f547be9dd225022fb3315a4bdecc48c2bf
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100578137"
---
# <a name="best-practices-for-azure-container-registry"></a>Najlepsze rozwiązania dla usługi Azure Container Registry

Korzystając z tych najlepszych rozwiązań, można zmaksymalizować wydajność i ekonomiczne użycie rejestru prywatnego na platformie Azure do przechowywania i wdrażania obrazów kontenerów i innych artefaktów.

Aby zapoznać się z pojęciami dotyczącymi rejestru, zobacz [Informacje o rejestrach, repozytoriach i obrazach](container-registry-concepts.md). Zapoznaj się również z [zaleceniami dotyczącymi tagowania i przechowywania wersji obrazów kontenerów](container-registry-image-tag-version.md) w celu uzyskania strategii tagów i wersji obrazów w rejestrze. 

## <a name="network-close-deployment"></a>Wdrażanie w pobliskiej sieci

Utwórz rejestr kontenerów w tym samym regionie świadczenia usług Azure, w którym zostały wdrożone kontenery. Dzięki umieszczeniu rejestru w regionie blisko sieci, w której hostowane są kontenery, można zmniejszyć opóźnienia i obniżyć koszty.

Wdrożenie w pobliskiej sieci jest jednym z głównych powodów używania prywatnego rejestru kontenerów. Obrazy platformy Docker mają wydajną [konstrukcję warstw](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), która umożliwia przeprowadzanie wdrożeń przyrostowych. Jednak nowe węzły muszą ściągać wszystkie warstwy wymagane dla danego obrazu. Ten początkowy `docker pull` może szybko powiększyć się do wielu gigabajtów. Posiadanie prywatnego rejestru blisko wdrożenia minimalizuje opóźnienia sieci.
Ponadto wszystkich chmury publiczne, w tym Azure, stosują opłaty za transfer danych wychodzących z sieci. Ściąganie obrazów z jednego centrum danych do innego skutkuje dodatkowymi opłatami za dane wychodzące z sieci, a poza tym zwiększa opóźnienia.

## <a name="geo-replicate-multi-region-deployments"></a>Replikacja geograficzna wdrożeń w wielu regionach

Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) usługi Azure Container Registry, jeśli wdrażasz kontenery w wielu regionach. Bez względu na to, czy obsługujesz globalnych klientów z lokalnych centrów danych, czy też Twój zespół deweloperów znajduje się w różnych lokalizacjach, możesz uprościć zarządzanie rejestrem i zminimalizować opóźnienia dzięki replikacji geograficznej rejestru. Możesz również skonfigurować regionalne elementy [webhook](container-registry-webhook.md) , aby powiadamiać o zdarzeniach w określonych replikach, takich jak podczas wypychania obrazów.

Replikacja geograficzna jest dostępna w rejestrach w [warstwie Premium](container-registry-skus.md) . Aby dowiedzieć się, jak korzystać z replikacji geograficznej, zobacz trzyczęściowy samouczek [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md).

## <a name="maximize-pull-performance"></a>Maksymalizuj wydajność ściągania

Oprócz umieszczania obrazów blisko wdrożeń, cechy samego obrazu mogą mieć wpływ na wydajność ściągania.

* **Rozmiar obrazu** — Minimalizuj rozmiary obrazów, usuwając zbędne [warstwy](container-registry-concepts.md#manifest) lub zmniejszając rozmiar warstw. Jednym ze sposobów zmniejszenia rozmiaru obrazu jest użycie podejścia do [tworzenia wieloetapowej kompilacji platformy Docker](https://docs.docker.com/develop/develop-images/multistage-build/) w celu uwzględnienia tylko niezbędnych składników środowiska uruchomieniowego. 

  Sprawdź również, czy obraz może zawierać jaśniejszy podstawowy obraz systemu operacyjnego. Jeśli używasz środowiska wdrażania, takiego jak Azure Container Instances, które buforuje niektóre obrazy podstawowe, sprawdź, czy możesz zamienić warstwę obrazu dla jednego z buforowanych obrazów. 
* **Liczba warstw** — pozwala zrównoważyć liczbę używanych warstw. Jeśli masz zbyt mało, nie możesz korzystać z użycia warstwowego na hoście. Zbyt wiele, a środowisko wdrażania poświęca więcej czasu na ściąganie i dekompresowanie. Optymalna jest warstwa od pięciu do 10.

Należy również wybrać [warstwę usług](container-registry-skus.md) Azure Container Registry, która spełnia wymagania dotyczące wydajności. Warstwa Premium zapewnia największą przepustowość i największą częstotliwość jednoczesnych operacji odczytu i zapisu w przypadku wdrożeń o dużej pojemności.

## <a name="repository-namespaces"></a>Przestrzenie nazw repozytoriów

Za pomocą przestrzeni nazw repozytorium można zezwolić na udostępnianie pojedynczego rejestru w wielu grupach w organizacji. Rejestry mogą być współużytkowane przez wdrożenia i zespoły. Usługa Azure Container Registry obsługuje zagnieżdżone przestrzenie nazw, umożliwiając izolację grup. Jednak rejestr zarządza wszystkimi repozytoriami niezależnie, a nie jako hierarchią.

Rozważmy na przykład poniższe znaczniki obrazów kontenera. Obrazy używane w całej firmie, takie jak `aspnetcore` , są umieszczane w głównej przestrzeni nazw, natomiast obrazy kontenerów należące do poszczególnych produktów i grup marketingowych używają własnych przestrzeni nazw.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedykowana grupa zasobów

Ponieważ rejestry kontenerów to zasoby, które są używane na wielu hostach kontenerów, Rejestr powinien znajdować się w własnej grupie zasobów.

Chociaż można eksperymentować z konkretnym typem hosta, takim jak [Azure Container Instances](../container-instances/container-instances-overview.md), prawdopodobnie zechcesz usunąć wystąpienie kontenera po zakończeniu. Można jednak również zachować kolekcję obrazów, które zostały wypchnięte do usługi Azure Container Registry. Dzięki umieszczeniu rejestru w jego własnej grupie zasobów można zminimalizować ryzyko przypadkowego usunięcia kolekcji obrazów w rejestrze podczas usuwania grupy zasobów wystąpienia kontenera.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Istnieją dwa podstawowe scenariusze uwierzytelniania w usłudze Azure Container Registry: uwierzytelnianie indywidualne i uwierzytelnianie usługi („bezobsługowe”). Poniższa tabela zawiera krótkie omówienie tych scenariuszy i wskazuje zalecaną metodę uwierzytelniania w przypadku każdego z nich.

| Typ | Przykładowy scenariusz | Zalecana metoda |
|---|---|---|
| Indywidualne tożsamości | Deweloper ściągający obrazy na swoją maszynę deweloperską lub wypychający z niej obrazy. | Polecenie [az acr login](/cli/azure/acr#az-acr-login) |
| Bezobsługowe/tożsamość usługi | Potoki kompilacji i wdrażania, w których użytkownik nie bierze bezpośrednio udziału. | [Jednostka usługi](container-registry-authentication.md#service-principal) |

Aby uzyskać szczegółowe informacje na temat tych i innych scenariuszy uwierzytelniania Azure Container Registry, zobacz [uwierzytelnianie przy użyciu usługi Azure Container Registry](container-registry-authentication.md).

Azure Container Registry obsługuje praktyki bezpieczeństwa w organizacji w celu dystrybucji obowiązków i uprawnień do różnych tożsamości. Przy użyciu [kontroli dostępu opartej na rolach](container-registry-roles.md)Przypisz odpowiednie uprawnienia do różnych użytkowników, podmiotów usługi lub innych tożsamości, które wykonują różne operacje na rejestrze. Na przykład przypisać uprawnienia push do jednostki usługi używanej w potoku kompilacji i przypisać uprawnienia do ściągania do innej tożsamości używanej do wdrożenia. Utwórz [tokeny](container-registry-repository-scoped-permissions.md) dla szczegółowych, ograniczonych czasowo dostępu do określonych repozytoriów.

## <a name="manage-registry-size"></a>Zarządzanie rozmiarem rejestru      

Ograniczenia magazynu dla każdej [warstwy usługi Rejestr kontenerów][container-registry-skus] są przeznaczone do wyrównania w typowym scenariuszu: **podstawowa** dla rozpoczynania, **standardowego** dla większości aplikacji produkcyjnych i **Premium** w celu zapewnienia wydajności i [replikacji geograficznej][container-registry-geo-replication]. W ciągu cyklu życia rejestru należy zarządzać jego rozmiarem, okresowo usuwając nieużywaną zawartość.

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

Aby uzyskać zalecenia dotyczące usprawnienia stan zabezpieczeń rejestrów kontenerów, zobacz [podstawy zabezpieczeń platformy Azure dla Azure Container Registry](security-baseline.md).

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