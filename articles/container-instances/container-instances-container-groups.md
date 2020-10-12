---
title: Wprowadzenie do grup kontenerów
description: Więcej informacji na temat grup kontenerów w Azure Container Instances, kolekcja wystąpień, które współużytkują cykl życia i zasoby, takie jak procesory CPU, magazyn i sieć
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: 72ebe6186da179bc5a1effddcc14327455eb7557
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89612410"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupy kontenerów w usłudze Azure Container Instances

Zasób najwyższego poziomu w Azure Container Instances jest *grupą kontenerów*. W tym artykule opisano, co to są grupy kontenerów i jakie typy scenariuszy są włączane.

## <a name="what-is-a-container-group"></a>Co to jest Grupa kontenerów?

Grupa kontenerów to kolekcja kontenerów, które są planowane na tym samym komputerze-hoście. Kontenery w grupie kontenerów współdzielą cykl życia, zasoby, sieć lokalną i woluminy magazynu. Jest to podobne w *koncepcji w* [Kubernetes][kubernetes-pod].

Na poniższym diagramie przedstawiono przykład grupy kontenerów zawierającej wiele kontenerów:

![Diagram grup kontenerów][container-groups-example]

Ta przykładowa Grupa kontenerów:

* Zaplanowano na pojedynczym komputerze-hoście.
* Ma przypisaną etykietę nazw DNS.
* Udostępnia jeden publiczny adres IP z jednym uwidocznionym portem.
* Składa się z dwóch kontenerów. Jeden kontener nasłuchuje na porcie 80, podczas gdy drugi nasłuchuje na porcie 5000.
* Obejmuje dwa udziały plików platformy Azure jako instalacje woluminów, a każdy kontener instaluje jeden z udziałów lokalnie.

> [!NOTE]
> Grupy wielokontenerowe obsługują obecnie tylko kontenery systemu Linux. W przypadku kontenerów systemu Windows Azure Container Instances obsługuje tylko wdrażanie jednego wystąpienia kontenera. Gdy pracujemy nad przełączeniem wszystkich funkcji do kontenerów systemu Windows, w [przeglądzie](container-instances-overview.md#linux-and-windows-containers)usługi można znaleźć bieżące różnice między platformami.

## <a name="deployment"></a>Wdrożenie

Poniżej przedstawiono dwa typowe sposoby wdrażania grupy wielokontenerowej: Użyj [szablonu Menedżer zasobów][resource-manager template] lub [pliku YAML][yaml-file]. Szablon Menedżer zasobów jest zalecany, gdy konieczne jest wdrożenie dodatkowych zasobów usługi platformy Azure (na przykład [udziału Azure Files][azure-files]) podczas wdrażania wystąpień kontenerów. Ze względu na bardziej zwięzły YAML formatu pliku YAML zaleca się, gdy wdrożenie obejmuje tylko wystąpienia kontenerów. Aby uzyskać szczegółowe informacje na temat właściwości, które można ustawić, zapoznaj się z dokumentacją dotyczącą [szablonu Menedżer zasobów](/azure/templates/microsoft.containerinstance/containergroups) lub dokumentacji [referencyjnej YAML](container-instances-reference-yaml.md) .

Aby zachować konfigurację grupy kontenerów, można wyeksportować konfigurację do pliku YAML przy użyciu polecenia [AZ Container Export][az-container-export]na platformie Azure. Eksport umożliwia przechowywanie konfiguracji grupy kontenerów w kontroli wersji dla "konfiguracji jako kodu". Można też użyć wyeksportowanego pliku jako punktu wyjścia podczas tworzenia nowej konfiguracji w programie YAML.



## <a name="resource-allocation"></a>Alokacja zasobów

Azure Container Instances przydziela zasoby, takie jak procesory CPU, pamięć i opcjonalnie [procesory GPU][gpus] (wersja zapoznawcza) do grupy wielokontenerowej przez dodanie [żądań zasobów][resource-requests] wystąpień w grupie. Jeśli na przykład utworzysz grupę kontenerów z dwoma wystąpieniami kontenerów, każdy z nich żąda 1 procesora CPU, Grupa kontenerów zostanie przypisana 2 procesory CPU.

### <a name="resource-usage-by-container-instances"></a>Użycie zasobów według wystąpień kontenera

Każde wystąpienie kontenera w grupie ma przydzieloną zasoby określone w żądaniu zasobu. Jednak maksymalne zasoby używane przez wystąpienie kontenera w grupie mogą być różne w przypadku skonfigurowania jej opcjonalnej właściwości [limitu zasobów][resource-limits] . Limit zasobów wystąpienia kontenera musi być większy lub równy właściwości obowiązkowego [żądania zasobu][resource-requests] .

* Jeśli nie określisz limitu zasobów, maksymalne użycie zasobów przez wystąpienie kontenera będzie takie samo jak jego żądanie zasobu.

* Jeśli określisz limit dla wystąpienia kontenera, maksymalne użycie wystąpienia może być większe niż żądanie, do ustawionego limitu. Analogicznie, użycie zasobów przez inne wystąpienia kontenerów w grupie może ulec zmniejszeniu. Maksymalny limit zasobów, który można ustawić dla wystąpienia kontenera, to łączna liczba zasobów przypisywanych do grupy.
    
Na przykład w grupie z dwoma wystąpieniami kontenerów każdy żądający 1 procesor CPU jeden z kontenerów może uruchamiać obciążenie, które wymaga więcej niż drugiego procesora CPU.

W tym scenariuszu można ustawić limit zasobów równy 2 procesor CPU dla wystąpienia kontenera. Ta konfiguracja pozwala wystąpieniu kontenera na używanie do 2 procesorów CPU, jeśli jest dostępna.

> [!NOTE]
> Niewielka ilość zasobów grupy kontenerów jest używana przez podstawową infrastrukturę usługi. Kontenery będą mogły uzyskać dostęp do większości, ale nie wszystkich zasobów przydzielono do grupy. Z tego powodu należy zaplanować mały bufor zasobów podczas żądania zasobów dla kontenerów w grupie.

### <a name="minimum-and-maximum-allocation"></a>Minimalna i maksymalna alokacja

* Przydziel **co najmniej** 1 procesor CPU i 1 GB pamięci do grupy kontenerów. Można obsługiwać poszczególne wystąpienia kontenerów w grupie przy użyciu mniej niż 1 procesora CPU i 1 GB pamięci. 

* Aby uzyskać **maksymalną liczbę** zasobów w grupie kontenerów, zapoznaj się z tematem [dostępność zasobów][region-availability] dla Azure Container Instances w regionie wdrożenia.

## <a name="networking"></a>Networking

Grupy kontenerów mogą współużytkować zewnętrzny adres IP, co najmniej jeden port na tym adresie IP oraz etykietę DNS z w pełni kwalifikowaną nazwą domeny (FQDN). Aby umożliwić klientom zewnętrznym dotarcie do kontenera w grupie, należy uwidocznić port w adresie IP i z kontenera. Po usunięciu grupy kontenerów jest wydawany adres IP i nazwa FQDN grupy kontenerów. 

W obrębie grupy kontenerów wystąpienia kontenerów mogą się łączyć ze sobą za pomocą hosta lokalnego na dowolnym porcie, nawet jeśli te porty nie są udostępniane zewnętrznie na adres IP grupy lub z kontenera.

Opcjonalnie można wdrożyć grupy kontenerów w [sieci wirtualnej platformy Azure][virtual-network] , aby umożliwić bezpiecznym komunikację kontenerów z innymi zasobami w sieci wirtualnej.

## <a name="storage"></a>Magazyn

Możesz określić woluminy zewnętrzne do zainstalowania w obrębie grupy kontenerów. Obsługiwane są następujące woluminy:
* [Udział plików platformy Azure][azure-files]
* [Wpis tajny][secret]
* [Pusty katalog][empty-directory]
* [Sklonowane repozytorium git][volume-gitrepo]

Te woluminy można mapować na określone ścieżki w poszczególnych kontenerach w grupie. 

## <a name="common-scenarios"></a>Typowe scenariusze

Grupy wielu kontenerów są przydatne w przypadkach, w których chcesz podzielić pojedyncze zadanie funkcjonalne na niewielką liczbę obrazów kontenerów. Te obrazy mogą następnie zostać dostarczone przez różne zespoły i mieć oddzielne wymagania dotyczące zasobów.

Przykładowe użycie może obejmować:

* Kontener obsługujący aplikację sieci Web i kontener ściągający najnowszą zawartość z kontroli źródła.
* Kontener aplikacji i kontener rejestrowania. Kontener rejestrowania zbiera dzienniki i metryki dane wyjściowe przez główną aplikację i zapisuje je do magazynu długoterminowego.
* Kontener aplikacji i kontener monitorowania. Kontener monitorujący okresowo wysyła żądanie do aplikacji, aby upewnić się, że działa i odpowiada poprawnie, i zgłasza alert, jeśli nie jest.
* Kontener frontonu i kontener zaplecza. Fronton może obtworzyć aplikację sieci Web przy użyciu zaplecza z uruchomioną usługą w celu pobierania danych. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wdrożyć wielokontenerową grupę kontenerów z szablonem Azure Resource Manager:

> [!div class="nextstepaction"]
> [Wdrażanie grupy kontenerów][resource-manager template]

<!-- IMAGES -->
[container-groups-example]: ./media/container-instances-container-groups/container-groups-example.png

<!-- LINKS - External -->
[dcos-pod]: https://dcos.io/docs/1.10/deploying-services/pods/
[kubernetes-pod]: https://kubernetes.io/docs/concepts/workloads/pods/

<!-- LINKS - Internal -->
[resource-manager template]: container-instances-multi-container-group.md
[yaml-file]: container-instances-multi-container-yaml.md
[region-availability]: container-instances-region-availability.md
[resource-requests]: /rest/api/container-instances/containergroups/createorupdate#resourcerequests
[resource-limits]: /rest/api/container-instances/containergroups/createorupdate#resourcelimits
[resource-requirements]: /rest/api/container-instances/containergroups/createorupdate#resourcerequirements
[azure-files]: container-instances-volume-azure-files.md
[virtual-network]: container-instances-virtual-network-concepts.md
[secret]: container-instances-volume-secret.md
[volume-gitrepo]: container-instances-volume-gitrepo.md
[gpus]: container-instances-gpu.md
[empty-directory]: container-instances-volume-emptydir.md
[az-container-export]: /cli/azure/container#az-container-export
