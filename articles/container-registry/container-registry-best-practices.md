---
title: Najlepsze praktyki w zakresie rejestru
description: Dowiedz się, jak efektywnie korzystać z usługi Azure Container Registry dzięki zastosowaniu tych najlepszych rozwiązań.
ms.topic: article
ms.date: 01/07/2021
ms.openlocfilehash: 0811cc4a5bffc21ffba19e64a3887eab6bc36fbb
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107784141"
---
# <a name="best-practices-for-azure-container-registry"></a>Najlepsze rozwiązania dla usługi Azure Container Registry

Zastosowanie tych najlepszych rozwiązań pozwala zmaksymalizować wydajność i ekonomiczne wykorzystanie rejestru prywatnego na platformie Azure do przechowywania i wdrażania obrazów kontenerów i innych artefaktów.

Aby uzyskać podstawowe informacje na temat pojęć dotyczących rejestru, zobacz About registries, repositories, and images (Informacje o [rejestrach, repozytoriach i obrazach).](container-registry-concepts.md) Zobacz też [zalecenia dotyczące tagowania i wersjonarowania](container-registry-image-tag-version.md) obrazów kontenerów, aby uzyskać informacje na temat strategii tagowania i wersjowania obrazów w rejestrze. 

## <a name="network-close-deployment"></a>Wdrażanie w pobliskiej sieci

Utwórz rejestr kontenerów w tym samym regionie świadczenia usług Azure, w którym zostały wdrożone kontenery. Dzięki umieszczeniu rejestru w regionie blisko sieci, w której hostowane są kontenery, można zmniejszyć opóźnienia i obniżyć koszty.

Wdrożenie w pobliskiej sieci jest jednym z głównych powodów używania prywatnego rejestru kontenerów. Obrazy platformy Docker mają wydajną [konstrukcję warstw](https://docs.docker.com/engine/userguide/storagedriver/imagesandcontainers/), która umożliwia przeprowadzanie wdrożeń przyrostowych. Jednak nowe węzły muszą ściągać wszystkie warstwy wymagane dla danego obrazu. Ten początkowy `docker pull` może szybko powiększyć się do wielu gigabajtów. Posiadanie prywatnego rejestru blisko wdrożenia minimalizuje opóźnienia sieci.
Ponadto wszystkich chmury publiczne, w tym Azure, stosują opłaty za transfer danych wychodzących z sieci. Ściąganie obrazów z jednego centrum danych do innego skutkuje dodatkowymi opłatami za dane wychodzące z sieci, a poza tym zwiększa opóźnienia.

## <a name="geo-replicate-multi-region-deployments"></a>Replikacja geograficzna wdrożeń w wielu regionach

Użyj funkcji [replikacji geograficznej](container-registry-geo-replication.md) usługi Azure Container Registry, jeśli wdrażasz kontenery w wielu regionach. Bez względu na to, czy obsługujesz globalnych klientów z lokalnych centrów danych, czy też Twój zespół deweloperów znajduje się w różnych lokalizacjach, możesz uprościć zarządzanie rejestrem i zminimalizować opóźnienia dzięki replikacji geograficznej rejestru. Można również skonfigurować regionalne [webhook,](container-registry-webhook.md) aby powiadamiać o zdarzeniach w określonych replikach, takich jak wypychanie obrazów.

Replikacja geograficzna jest dostępna w [rejestrach Premium.](container-registry-skus.md) Aby dowiedzieć się, jak korzystać z replikacji geograficznej, zobacz trzyczęściowy samouczek [Replikacja geograficzna w usłudze Azure Container Registry](container-registry-tutorial-prepare-registry.md).

## <a name="maximize-pull-performance"></a>Maksymalizuj wydajność ściągania

Oprócz umieszczania obrazów w pobliżu wdrożeń charakterystyka samych obrazów może mieć wpływ na wydajność ściągania.

* **Rozmiar obrazu** — minimalizowanie rozmiarów obrazów przez usunięcie niepotrzebnych [warstw](container-registry-concepts.md#manifest) lub zmniejszenie rozmiaru warstw. Jednym ze sposobów zmniejszenia rozmiaru obrazu jest zastosowanie wieloetapowego podejścia do kompilacji platformy [Docker](https://docs.docker.com/develop/develop-images/multistage-build/) w celu dołączania tylko niezbędnych składników środowiska uruchomieniowego. 

  Sprawdź również, czy obraz może zawierać jaśniejszy podstawowy obraz systemu operacyjnego. Jeśli korzystasz ze środowiska wdrażania, takiego jak Azure Container Instances buforuje niektóre obrazy podstawowe, sprawdź, czy można zamienić warstwę obrazu na jeden z obrazów buforowanych. 
* **Liczba warstw —** zrównoważyć liczbę używanych warstw. Jeśli jest ich zbyt mało, ponowne użycie warstwy i buforowanie na hoście nie jest korzystne. Zbyt wiele, a środowisko wdrażania poświęca więcej czasu na ściąganie i dekompresowanie. Optymalne jest od pięciu do 10 warstw.

Wybierz również warstwę [usługi o Azure Container Registry](container-registry-skus.md) która spełnia Twoje wymagania dotyczące wydajności. Warstwa Premium zapewnia największą przepustowość i najwyższy stopień współbieżnych operacji odczytu i zapisu w przypadku wdrożeń o dużej ilości danych.

## <a name="repository-namespaces"></a>Przestrzenie nazw repozytoriów

Korzystając z przestrzeni nazw repozytorium, można zezwolić na udostępnianie pojedynczego rejestru w wielu grupach w organizacji. Rejestry mogą być współużytkowane przez wdrożenia i zespoły. Usługa Azure Container Registry obsługuje zagnieżdżone przestrzenie nazw, umożliwiając izolację grup. Jednak rejestr zarządza wszystkimi repozytoriami niezależnie, a nie jako hierarchią.

Rozważmy na przykład poniższe znaczniki obrazów kontenera. Obrazy, które są używane dla całej firmy, takie jak , są umieszczane w głównej przestrzeni nazw, podczas gdy obrazy kontenerów należące do grup Products i Marketing używają `aspnetcore` własnych przestrzeni nazw.

- *contoso.azurecr.io/aspnetcore:2.0*
- *contoso.azurecr.io/products/widget/web:1*
- *contoso.azurecr.io/products/bettermousetrap/refundapi:12.3*
- *contoso.azurecr.io/marketing/2017-fall/concertpromotions/campaign:218.42*

## <a name="dedicated-resource-group"></a>Dedykowana grupa zasobów

Ponieważ rejestry kontenerów są zasobami używanymi na wielu hostach kontenerów, rejestr powinien znajdować się we własnej grupie zasobów.

Mimo że możesz eksperymentować z określonym typem hosta, takim jak [Azure Container Instances](../container-instances/container-instances-overview.md), po jego usunięciu prawdopodobnie będziesz chcieć usunąć wystąpienie kontenera. Można jednak również zachować kolekcję obrazów, które zostały wypchnięte do usługi Azure Container Registry. Dzięki umieszczeniu rejestru w jego własnej grupie zasobów można zminimalizować ryzyko przypadkowego usunięcia kolekcji obrazów w rejestrze podczas usuwania grupy zasobów wystąpienia kontenera.

## <a name="authentication-and-authorization"></a>Uwierzytelnianie i autoryzacja

Istnieją dwa podstawowe scenariusze uwierzytelniania w usłudze Azure Container Registry: uwierzytelnianie indywidualne i uwierzytelnianie usługi („bezobsługowe”). Poniższa tabela zawiera krótkie omówienie tych scenariuszy i wskazuje zalecaną metodę uwierzytelniania w przypadku każdego z nich.

| Typ | Przykładowy scenariusz | Zalecana metoda |
|---|---|---|
| Indywidualne tożsamości | Deweloper ściągający obrazy na swoją maszynę deweloperską lub wypychający z niej obrazy. | Polecenie [az acr login](/cli/azure/acr#az_acr_login) |
| Bezobsługowe/tożsamość usługi | Potoki kompilacji i wdrażania, w których użytkownik nie bierze bezpośrednio udziału. | [Jednostka usługi](container-registry-authentication.md#service-principal) |

Aby uzyskać szczegółowe informacje na temat tych i innych scenariuszy uwierzytelniania Azure Container Registry, zobacz Authenticate with an Azure container registry (Uwierzytelnianie [za pomocą usługi Azure Container Registry).](container-registry-authentication.md)

Azure Container Registry obsługuje rozwiązania w zakresie zabezpieczeń w organizacji w celu rozdzielania obowiązków i uprawnień do różnych tożsamości. Za [pomocą kontroli dostępu opartej na rolach](container-registry-roles.md)przypisz odpowiednie uprawnienia różnym użytkownikom, jednostkom usługi lub innym tożsamościom, które wykonują różne operacje rejestru. Na przykład przypisz uprawnienia wypychania do jednostki usługi używanej w potoku kompilacji i przypisz uprawnienia do ściągania do innej tożsamości używanej do wdrożenia. Tworzenie [tokenów](container-registry-repository-scoped-permissions.md) w celu uzyskania szczegółowego, ograniczonego czasowo dostępu do określonych repozytoriów.

## <a name="manage-registry-size"></a>Zarządzanie rozmiarem rejestru      

Ograniczenia magazynu poszczególnych [][container-registry-skus] warstw usług rejestru kontenerów mają być zgodne z typowym scenariuszem:  Podstawowa dla rozpoczynania pracy, **Standardowa** dla większości aplikacji produkcyjnych i **Premium** na temat wydajności hiperskali i replikacji [geograficznej.][container-registry-geo-replication] W ciągu cyklu życia rejestru należy zarządzać jego rozmiarem, okresowo usuwając nieużywaną zawartość.

Użyj polecenia interfejsu wiersza polecenia platformy Azure [az acr show-usage,][az-acr-show-usage] aby wyświetlić bieżący rozmiar rejestru:

```azurecli
az acr show-usage --resource-group myResourceGroup --name myregistry --output table
```

```output
NAME      LIMIT         CURRENT VALUE    UNIT
--------  ------------  ---------------  ------
Size      536870912000  185444288        Bytes
Webhooks  100                            Count
```

Bieżący magazyn używany w przeglądzie  rejestru można również znaleźć w Azure Portal:

![Informacje o użyciu rejestru w witrynie Azure Portal][registry-overview-quotas]

### <a name="delete-image-data"></a>Usuwanie danych obrazu

Azure Container Registry obsługuje kilka metod usuwania danych obrazu z rejestru kontenerów. Możesz usuwać obrazy według tagu lub skrótu manifestu albo usuwać całe repozytorium.

Aby uzyskać szczegółowe informacje na temat usuwania danych obrazów z rejestru, w tym obrazów nieoznakowanych (czasami nazywanych "zwisającym" lub "oddzielonym"), zobacz Usuwanie obrazów kontenerów w Azure Container Registry [.](container-registry-delete.md)

## <a name="next-steps"></a>Następne kroki

Azure Container Registry jest dostępna w kilku warstwach (nazywanych również jednostkami SKU), które zapewniają różne możliwości. Aby uzyskać szczegółowe informacje na temat dostępnych warstw usług, [zobacz Azure Container Registry usługi](container-registry-skus.md).

Aby uzyskać zalecenia dotyczące poprawy stanu zabezpieczeń rejestrów kontenerów, zobacz Punkt odniesienia zabezpieczeń platformy Azure dla [Azure Container Registry](security-baseline.md).

<!-- IMAGES -->
[delete-repository-portal]: ./media/container-registry-best-practices/delete-repository-portal.png
[registry-overview-quotas]: ./media/container-registry-best-practices/registry-overview-quotas.png

<!-- LINKS - Internal -->
[az-acr-repository-delete]: /cli/azure/acr/repository#az_acr_repository_delete
[az-acr-show-usage]: /cli/azure/acr#az_acr_show_usage
[azure-cli]: /cli/azure
[azure-portal]: https://portal.azure.com
[container-registry-geo-replication]: container-registry-geo-replication.md
[container-registry-skus]: container-registry-skus.md