---
title: Często zadawane pytania
description: Odpowiedzi na często zadawane pytania dotyczące usługi Azure Container Instances
author: macolso
ms.author: macolso
ms.topic: article
ms.date: 06/02/2020
ms.openlocfilehash: 1cedfb7464fd97249c5cd3fae73c9290365da6cd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91818481"
---
# <a name="frequently-asked-questions-about-azure-container-instances"></a>Często zadawane pytania dotyczące Azure Container Instances

W tym artykule opisano często zadawane pytania dotyczące Azure Container Instances.

## <a name="deployment"></a>Wdrożenie

### <a name="how-large-can-my-container-image-be"></a>Jak duży może być mój obraz kontenera?

Maksymalny rozmiar obrazu kontenera możliwego do wdrożenia w Azure Container Instances to 15 GB. Może być możliwe wdrożenie większych obrazów w zależności od dokładnej dostępności w momencie wdrażania, ale nie jest to gwarantowane.

Rozmiar obrazu kontenera ma wpływ na czas wdrażania, dlatego na ogół chcesz przechowywać obrazy kontenerów tak, jak to możliwe.

### <a name="how-can-i-speed-up-the-deployment-of-my-container"></a>Jak mogę przyspieszyć wdrażanie mojego kontenera?

Ponieważ jednym z głównych wyznaczników czasu wdrożenia jest rozmiar obrazu, poszukaj sposobów zmniejszenia rozmiaru. Usuń niepotrzebne warstwy lub Zmniejsz rozmiar warstw obrazu (poprzez wybranie jaśniejszego obrazu podstawowego systemu operacyjnego). Na przykład jeśli używasz kontenerów systemu Linux, rozważ użycie Alpine jako obrazu podstawowego, a nie całego serwera Ubuntu. Podobnie w przypadku kontenerów systemu Windows, jeśli jest to możliwe, należy użyć podstawowego obrazu serwera nano Server. 

Należy również sprawdzić listę wstępnie zbuforowanych obrazów w usłudze Azure Container images, która jest dostępna za pośrednictwem interfejsu API [buforowania list obrazów](/rest/api/container-instances/location/listcachedimages) . Może być możliwe przełączenie warstwy obrazu dla jednego z wstępnie zbuforowanych obrazów. 

Zobacz bardziej [szczegółowe wskazówki](container-instances-troubleshooting.md#container-takes-a-long-time-to-start) dotyczące skracania czasu uruchamiania kontenera.

### <a name="what-windows-base-os-images-are-supported"></a>Jakie podstawowe obrazy systemu operacyjnego Windows są obsługiwane?

> [!NOTE]
> Ze względu na problemy z poprzednią zgodnością po aktualizacji systemu Windows w wersji 2020, następujące wersje obrazu zawierają minimalny numer wersji, którego zalecamy użyć w obrazie podstawowym. Nie ma to wpływu na bieżące wdrożenia korzystające ze starszych wersji obrazu, ale nowe wdrożenia powinny być zgodne z poniższymi obrazami podstawowymi. 

#### <a name="windows-server-2016-base-images"></a>Podstawowe obrazy systemu Windows Server 2016

* [Serwer nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `sac2016` `10.0.14393.3506` lub nowszy
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2016` ,  `10.0.14393.3506` lub nowszy

> [!NOTE]
> Obrazy systemu Windows oparte na Semi-Annual kanale 1709 lub 1803 nie są obsługiwane.

#### <a name="windows-server-2019-and-client-base-images-preview"></a>Windows Server 2019 i obrazy podstawowe klienta (wersja zapoznawcza)

* [Serwer nano Server](https://hub.docker.com/_/microsoft-windows-nanoserver): `1809` `10.0.17763.1040` lub nowszy
* [Windows Server Core](https://hub.docker.com/_/microsoft-windows-servercore): `ltsc2019` , `1809` `10.0.17763.1040` lub nowszy
* [System Windows](https://hub.docker.com/_/microsoft-windows): `1809` `10.0.17763.1040` lub nowszy

### <a name="what-net-or-net-core-image-layer-should-i-use-in-my-container"></a>Jakiej warstwy obrazu platformy .NET lub platformy .NET Core należy używać w moim kontenerze? 

Użyj najmniejszego obrazu, który spełnia Twoje wymagania. W przypadku systemu Linux można użyć obrazu .NET Core *środowiska uruchomieniowego* , który jest obsługiwany od wersji programu .net Core 2,1. W przypadku systemu Windows, jeśli używasz pełnego .NET Framework, musisz użyć obrazu systemu Windows Server Core (obrazu tylko do środowiska uruchomieniowego, takiego jak  *4.7.2-windowsservercore-ltsc2016*). Obrazy tylko w środowisku uruchomieniowym są mniejsze, ale nie obsługują obciążeń, które wymagają zestawu .NET SDK.

## <a name="availability-and-quotas"></a>Dostępność i limity przydziału

### <a name="how-many-cores-and-memory-should-i-allocate-for-my-containers-or-the-container-group"></a>Ile rdzeni i pamięci należy przydzielić dla kontenerów lub grupy kontenerów?

Jest to naprawdę zależne od obciążenia. Rozpocznij małą i testową wydajność, aby zobaczyć, jak działa kontenery. [Monitoruj użycie zasobów procesora i pamięci](container-instances-monitor.md), a następnie Dodaj rdzenie lub pamięć na podstawie rodzaju procesów wdrożonych w kontenerze.

Upewnij się również, że Sprawdź [dostępność zasobów](container-instances-region-availability.md#availability---general) dla regionu, który jest wdrażany w ramach górnej granicy na rdzeniach procesora i dostępnej pamięci dla każdej grupy kontenerów. 

> [!NOTE]
> Niewielka ilość zasobów grupy kontenerów jest używana przez podstawową infrastrukturę usługi. Kontenery będą mogły uzyskać dostęp do większości, ale nie wszystkich zasobów przydzielono do grupy. Z tego powodu należy zaplanować mały bufor zasobów podczas żądania zasobów dla kontenerów w grupie.

### <a name="what-underlying-infrastructure-does-aci-run-on"></a>Do jakiej infrastruktury jest ACI uruchomiony system?

Azure Container Instances ma być kontenerem bez serwera — usługą na żądanie, więc chcemy skupić się na tworzeniu kontenerów i nie martwić się o infrastrukturę. Dla tych, które są chcesz wiedzieć lub chcą wykonywać porównania dotyczące wydajności, ACI działa na zestawach maszyn wirtualnych platformy Azure różnych jednostek SKU, głównie z serii F i D. Oczekujemy, że zmienimy się w przyszłości w miarę dalszego opracowywania i optymalizowania usługi. 

### <a name="i-want-to-deploy-thousand-of-cores-on-aci---can-i-get-my-quota-increased"></a>Chcę wdrożyć tysiące rdzeni w ACI — czy można zwiększyć limit przydziału?
 
Tak (czasami). Zapoznaj się z artykułem [przydziały i limity](container-instances-quotas.md) dla bieżących limitów, a limity można zwiększyć przez żądanie.

### <a name="can-i-deploy-with-more-than-4-cores-and-16-gb-of-ram"></a>Czy mogę wdrożyć z więcej niż 4 rdzeniami i 16 GB pamięci RAM?

Jeszcze nie. Obecnie są to wartości maksymalne dla grupy kontenerów. Skontaktuj się z pomocą techniczną platformy Azure z określonymi wymaganiami lub żądaniami. 

### <a name="when-will-aci-be-in-a-specific-region"></a>Kiedy ACI będzie w określonym regionie?

Dostępność bieżącego regionu jest publikowana [tutaj](container-instances-region-availability.md#availability---general). Jeśli masz wymóg dotyczący określonego regionu, skontaktuj się z pomocą techniczną platformy Azure.

## <a name="features-and-scenarios"></a>Funkcje i scenariusze

### <a name="how-do-i-scale-a-container-group"></a>Jak mogę skalować grupę kontenerów?

Obecnie skalowanie nie jest dostępne dla kontenerów lub grup kontenerów. Jeśli potrzebujesz uruchamiać więcej wystąpień, użyj naszego interfejsu API, aby zautomatyzować i utworzyć więcej żądań tworzenia grupy kontenerów w usłudze. 

### <a name="what-features-are-available-to-instances-running-in-a-custom-vnet"></a>Jakie funkcje są dostępne dla wystąpień uruchomionych w niestandardowej sieci wirtualnej?

Możesz [wdrożyć grupy kontenerów w wybranej sieci wirtualnej platformy Azure](container-instances-vnet.md) i delegować prywatne adresy IP do grup kontenerów, aby kierować ruchem sieciowym w sieci wirtualnej przez zasoby platformy Azure. W przypadku scenariuszy sieciowych i ograniczeń dotyczących Azure Container Instances, zobacz [scenariusze i zasoby sieci wirtualnej](container-instances-virtual-network-concepts.md).

## <a name="pricing"></a>Cennik

### <a name="when-does-the-meter-start-running"></a>Kiedy licznik zacznie działać?

Czas trwania grupy kontenerów jest obliczany od momentu, w którym rozpoczyna się ściąganie pierwszego kontenera (w przypadku nowego wdrożenia) lub gdy grupa kontenerów zostanie ponownie uruchomiona (jeśli została już wdrożona), dopóki Grupa kontenerów nie zostanie zatrzymana. Poniżej znajdują się szczegółowe informacje o [cenach Container Instances](https://azure.microsoft.com/pricing/details/container-instances/).

### <a name="do-i-stop-being-charged-when-my-containers-are-stopped"></a>Czy zatrzymana opłata jest naliczana, gdy moje kontenery są zatrzymane?

Liczniki przestają działać po zatrzymaniu całej grupy kontenerów. Tak długo, jak kontener w grupie kontenerów jest uruchomiony, utrzymujemy zasoby w przypadku, gdy chcesz ponownie uruchomić kontenery. 

## <a name="next-steps"></a>Następne kroki

* [Dowiedz się więcej](container-instances-overview.md) o Azure Container Instances.
* [Rozwiązywanie typowych problemów](container-instances-troubleshooting.md) w Azure Container Instances.
