---
title: Wprowadzenie do grup kontenerów
description: Dowiedz się więcej o grupach kontenerów Azure Container Instances, czyli kolekcji wystąpień, które współdzielą cykl życia i zasoby, takie jak procesory CPU, magazyn i sieć
ms.topic: article
ms.date: 11/01/2019
ms.custom: mvc
ms.openlocfilehash: a2cb3eac5baa5b1035749d28b9fb99bbb45b9ee6
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790891"
---
# <a name="container-groups-in-azure-container-instances"></a>Grupy kontenerów w usłudze Azure Container Instances

Zasób najwyższego poziomu w Azure Container Instances to grupa *kontenerów*. W tym artykule opisano, czym są grupy kontenerów i jakie scenariusze umożliwiają.

## <a name="what-is-a-container-group"></a>Co to jest grupa kontenerów?

Grupa kontenerów to kolekcja kontenerów, które są planowane na tej samej maszynie hosta. Kontenery w grupie kontenerów współdzielą cykl życia, zasoby, sieć lokalną i woluminy magazynu. Jest on podobny do zasobnika *na platformie* [Kubernetes.][kubernetes-pod]

Na poniższym diagramie przedstawiono przykład grupy kontenerów, która zawiera wiele kontenerów:

![Diagram grup kontenerów][container-groups-example]

Ta przykładowa grupa kontenerów:

* Jest zaplanowane na jednej maszynie hosta.
* Ma przypisaną etykietę nazwy DNS.
* Uwidacznia jeden publiczny adres IP z jednym uwidocznioną portem.
* Składa się z dwóch kontenerów. Jeden kontener nasłuchuje na porcie 80, a drugi nasłuchuje na porcie 5000.
* Zawiera dwa udziały plików platformy Azure jako instalacji woluminów, a każdy kontener zainstaluje jeden z udziałów lokalnie.

> [!NOTE]
> Grupy z wieloma kontenerami obecnie obsługują tylko kontenery systemu Linux. W przypadku kontenerów Azure Container Instances obsługuje tylko wdrażanie pojedynczego wystąpienia kontenera. Podczas pracy nad dodaniem wszystkich funkcji do kontenerów systemu Windows można znaleźć bieżące różnice między platformami w tesłudze [— omówienie.](container-instances-overview.md#linux-and-windows-containers)

## <a name="deployment"></a>Wdrożenie

Oto dwa typowe sposoby wdrażania grupy z wieloma kontenerami: użycie szablonu Resource Manager [lub][resource-manager template] pliku [YAML.][yaml-file] Szablon Resource Manager jest zalecany, gdy konieczne jest wdrożenie dodatkowych zasobów usługi platformy Azure (na przykład udziału Azure Files [)][azure-files]podczas wdrażania wystąpień kontenera. Ze względu na bardziej zwięzły charakter formatu YAML zaleca się użycie pliku YAML, gdy wdrożenie obejmuje tylko wystąpienia kontenerów. Aby uzyskać szczegółowe informacje na temat właściwości, które można ustawić, zobacz dokumentację [Resource Manager szablonu](/azure/templates/microsoft.containerinstance/containergroups) lub [dokumentację referencyjną YAML.](container-instances-reference-yaml.md)

Aby zachować konfigurację grupy kontenerów, możesz wyeksportować konfigurację do pliku YAML przy użyciu polecenia [interfejsu][az-container-export]wiersza polecenia platformy Azure az container export . Eksport umożliwia przechowywanie konfiguracji grupy kontenerów w kontroli wersji dla "konfiguracji jako kodu". Możesz też użyć wyeksportowanego pliku jako punktu wyjścia podczas tworzenia nowej konfiguracji w pliku YAML.



## <a name="resource-allocation"></a>Alokacja zasobów

Azure Container Instances przydziela zasoby, takie jak procesory CPU, pamięć i opcjonalnie procesory [GPU][gpus] (wersja [][resource-requests] zapoznawcza) do grupy z wieloma kontenerami, dodając żądania zasobów wystąpień w grupie. Jeśli na przykład utworzysz grupę kontenerów z dwoma wystąpieniami kontenerów, z których każde żąda 1 procesora CPU, do grupy kontenerów zostaną przydzielone 2 procesory CPU.

### <a name="resource-usage-by-container-instances"></a>Użycie zasobów według wystąpień kontenerów

Do każdego wystąpienia kontenera w grupie są przydzielane zasoby określone w żądaniu zasobu. Jednak maksymalna ilość zasobów używanych przez wystąpienie kontenera w grupie może się różnić w przypadku skonfigurowania jej [opcjonalnej właściwości limitu][resource-limits] zasobów. Limit zasobów wystąpienia kontenera musi być większy lub równy obowiązkowej [właściwości żądania][resource-requests] zasobu.

* Jeśli nie określisz limitu zasobów, maksymalne użycie zasobów wystąpienia kontenera jest takie samo jak jego żądanie zasobu.

* Jeśli określisz limit dla wystąpienia kontenera, maksymalne użycie wystąpienia może być większe niż żądanie do ustawionego limitu. Odpowiednio użycie zasobów przez inne wystąpienia kontenerów w grupie może się zmniejszyć. Maksymalny limit zasobów, który można ustawić dla wystąpienia kontenera, to łączna liczba zasobów przydzielonych do grupy.
    
Na przykład w grupie z dwoma wystąpieniami kontenerów, z których każde żąda 1 procesora CPU, w jednym z kontenerów może być uruchomione obciążenie, które wymaga więcej procesorów niż pozostałe.

W tym scenariuszu można ustawić limit zasobów do 2 procesorów CPU dla wystąpienia kontenera. Ta konfiguracja umożliwia wystąpieniu kontenera użycie maksymalnie 2 procesorów CPU, jeśli są dostępne.

> [!NOTE]
> Niewielka ilość zasobów grupy kontenerów jest używana przez podstawową infrastrukturę usługi. Kontenery będą mogły uzyskać dostęp do większości, ale nie wszystkich zasobów przydzielonych do grupy. Z tego powodu należy zaplanować mały bufor zasobów podczas żądania zasobów dla kontenerów w grupie.

### <a name="minimum-and-maximum-allocation"></a>Alokacja minimalna i maksymalna

* Przydziel **co najmniej** 1 procesor CPU i 1 GB pamięci do grupy kontenerów. Pojedyncze wystąpienia kontenerów w grupie mogą być aprowowane z mniej niż 1 procesorem CPU i 1 GB pamięci. 

* Aby uzyskać **maksymalne** zasoby w grupie kontenerów, zobacz dostępność [zasobów][region-availability] dla Azure Container Instances w regionie wdrożenia.

## <a name="networking"></a>Sieć

Grupy kontenerów mogą współużytkować zewnętrzny adres IP, co najmniej jeden port na tym adresie IP i etykietę DNS z w pełni kwalifikowaną nazwą domeny (FQDN). Aby umożliwić klientom zewnętrznym dostęp do kontenera w grupie, należy uwidocznić port w adresie IP i z kontenera. Adres IP i FQDN grupy kontenerów są zwalniane po usunięciu grupy kontenerów. 

W ramach grupy kontenerów wystąpienia kontenerów mogą się ze sobą dotrzeć za pośrednictwem hosta lokalnego na dowolnym porcie, nawet jeśli te porty nie są widoczne zewnętrznie na adresIE IP grupy lub w kontenerze.

Opcjonalnie możesz wdrożyć grupy kontenerów w sieci [wirtualnej platformy Azure,][virtual-network] aby umożliwić kontenerom bezpieczne komunikowanie się z innymi zasobami w sieci wirtualnej.

## <a name="storage"></a>Storage

Można określić woluminy zewnętrzne do instalacji w grupie kontenerów. Obsługiwane woluminy obejmują:
* [Udział plików platformy Azure][azure-files]
* [Tajne][secret]
* [Pusty katalog][empty-directory]
* [Sklonowane repozytorium git][volume-gitrepo]

Te woluminy można mapować na określone ścieżki w poszczególnych kontenerach w grupie. 

## <a name="common-scenarios"></a>Typowe scenariusze

Grupy z wieloma kontenerami są przydatne w przypadkach, gdy chcesz podzielić jedno zadanie funkcjonalne na niewielką liczbę obrazów kontenerów. Te obrazy mogą być następnie dostarczane przez różne zespoły i mają oddzielne wymagania dotyczące zasobów.

Przykładowe użycie może obejmować:

* Kontener obsługujący aplikację internetową i kontener ściągający najnowszą zawartość z kontroli źródła.
* Kontener aplikacji i kontener rejestrowania. Kontener rejestrowania zbiera dane wyjściowe dzienników i metryk przez główną aplikację i zapisuje je w magazynie długoterminowym.
* Kontener aplikacji i kontener monitorowania. Kontener monitorowania okresowo wysyła żądanie do aplikacji, aby upewnić się, że działa i odpowiada prawidłowo, oraz zgłasza alert, jeśli tak nie jest.
* Kontener frontony i kontener frontony. Frontonie mogą obsługiwać aplikację internetową, a za jej pomocą działa usługa do pobierania danych. 

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wdrożyć grupę kontenerów z wieloma kontenerami przy użyciu Azure Resource Manager szablonu:

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
[az-container-export]: /cli/azure/container#az_container_export
