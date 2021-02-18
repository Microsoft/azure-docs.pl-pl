---
title: Poufne węzły obliczeniowe w usłudze Azure Kubernetes Service (AKS)
description: Poufne węzły obliczeniowe w AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 2/08/2021
ms.author: amgowda
ms.openlocfilehash: 9ca98c032a7c8bd1820a92bff77079a61c515d65
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653384"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service"></a>Poufne węzły obliczeniowe w usłudze Azure Kubernetes Service

Dane [poufne platformy Azure](overview.md) umożliwiają ochronę poufnych danych, gdy są używane. Źródłowa, poufna infrastruktura obliczeniowa chroni te dane od innych aplikacji, administratorów i dostawców chmury przy użyciu sprzętowych środowisk kontenera zaufanych wykonań. Dodanie chronionych węzłów obliczeniowych umożliwia kierowanie aplikacji kontenera do uruchamiania w izolowanym, sprzętowym i zaświadczeniu.

## <a name="overview"></a>Omówienie

Usługa Azure Kubernetes Service (AKS) obsługuje dodawanie [niejawnych węzłów obliczeniowych DCsv2](confidential-computing-enclaves.md) obsługiwanych przez SGX firmy Intel. Te węzły umożliwiają uruchamianie poufnych obciążeń w ramach sprzętowego środowiska wykonawczego (TEE). TEE umożliwiają użycie kodu na poziomie użytkownika z kontenerów w celu przydzielenia prywatnych regionów pamięci, aby wykonać kod bezpośrednio z użyciem procesora CPU. Te prywatne regiony pamięci, które są wykonywane bezpośrednio z procesorem CPU, są nazywane enclaves. Enclaves chronić poufność danych, integralność danych i integralność kodu z innych procesów uruchomionych w tych samych węzłach. Model wykonywania Intel SGX również usuwa warstwy pośrednie systemu operacyjnego gościa, system operacyjny hosta i funkcję hypervisor, co zmniejsza obszar narażony na ataki. *Sprzęt oparty na odizolowanym modelu wykonywania* w węźle umożliwia aplikacjom bezpośrednie wykonywanie przy użyciu procesora, zachowując specjalny blok pamięci zaszyfrowany na kontener. Poufne rozwiązania obliczeniowe z kontenerami poufnymi są doskonałym uzupełnieniem planowania zabezpieczeń bez zaufania i strategii kontenerów z kompleksową ochroną.

![SGX węzeł — Omówienie](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Funkcje AKS poufne węzły

- Izolacja kontenerów na poziomie sprzętu i procesu za pośrednictwem środowiska SGX Trusted Execution Environment (TEE) 
- Klastry puli węzłów heterogenicznych (mieszane poufne i niepoufne pule węzłów)
- Planowanie oparte na pamięci podręcznej stronicowanej strony (EPC) (wymaga dodatku)
- Wstępnie zainstalowany sterownik Intel SGX DCAP
- Użycie procesora CPU bazujące na skalowaniu automatycznym i automatycznym skalowaniu klastra
- Obsługa kontenerów systemu Linux w węzłach procesu roboczego maszyn wirtualnych Ubuntu 18,04 Gen 2

## <a name="confidential-computing-add-on-for-aks"></a>Dodatek do przetwarzania poufnego dla AKS
Funkcja dodatku umożliwia dodatkowe możliwości AKS w przypadku uruchamiania w klastrze pul węzłów zawierających poufne dane. Ten dodatek umożliwia korzystanie z poniższych funkcji.

#### <a name="azure-device-plugin-for-intel-sgx"></a>Wtyczka urządzenia Azure dla platformy Intel SGX <a id="sgx-plugin"></a>

Wtyczka urządzenia implementuje interfejs wtyczki urządzenia Kubernetes na potrzeby pamięci podręcznej (SYGNATURy dostępu współszyfrowanej) i udostępnia sterowniki urządzeń z węzłów. Efektywnie Ta wtyczka sprawia, że pamięć sygnatury EPC jako inny typ zasobu w Kubernetes. Użytkownicy mogą określić limity dla tego zasobu, tak jak inne zasoby. Oprócz funkcji planowania wtyczka urządzenia pomaga przypisać uprawnienia sterownika urządzenia Intel SGX do poufnych kontenerów obciążenia. Przy użyciu tej wtyczki deweloper może uniknąć instalowania woluminów sterowników Intel SGX w plikach wdrożenia. Przykładowa implementacja przykładowego wdrożenia opartego na pamięci sygnatury EPC ( `kubernetes.azure.com/sgx_epc_mem_in_MiB` ) jest [tutaj](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)


## <a name="programming-models"></a>Modele programowania

### <a name="confidential-containers"></a>Kontenery poufne

[Kontenery poufne](confidential-containers.md) ułatwiają uruchamianie istniejących niezmodyfikowanych aplikacji kontenerów dla najczęściej **używanych środowisk programowania** (Python, Node, Java itp.). Ten model pakietów nie wymaga modyfikacji kodu źródłowego ani ponownej kompilacji. Jest to najszybsza metoda poufności, która może zostać osiągnięta przez pakowanie standardowych kontenerów platformy Docker z projektami Open-Source lub rozwiązaniami partnerskimi platformy Azure. W tym pakiecie pakowania i modelu wykonywania wszystkie części aplikacji kontenera są ładowane w zaufanej granicy (enklawy). Ten model dobrze sprawdza się w przypadku aplikacji kontenera półek dostępnych na rynku lub niestandardowych aplikacji aktualnie uruchomionych w węzłach ogólnego przeznaczenia.

### <a name="enclave-aware-containers"></a>Kontenery obsługujące enklawy
Węzły przetwarzania poufnego w systemie AKS obsługują również kontenery, które są programowane do uruchomienia w enklawy do korzystania z **specjalnego zestawu instrukcji** dostępnego z procesora CPU. Ten model programowania umożliwia ściślejszą kontrolę przepływu wykonywania i wymaga użycia specjalnych zestawów SDK i platform. Ten model programowania zapewnia większość kontroli nad przepływem aplikacji z najniższą zaufaną bazą obliczeniową (TCB). Programowanie kontenerów z obsługą enklawy obejmuje niezaufane i zaufane części aplikacji kontenera, dzięki czemu można zarządzać regularną pamięcią i zaszyfrowaną pamięcią pamięci podręcznej (EPC), w której jest wykonywane enklawy. [Przeczytaj więcej](enclave-aware-containers.md) na temat kontenerów z obsługą enklawy.

## <a name="next-steps"></a>Następne kroki

[Wdróż klaster AKS z węzłami w postaci poufnego przetwarzania](./confidential-nodes-aks-get-started.md)

[Szybkie początkowe przykłady kontenera](https://github.com/Azure-Samples/confidential-container-samples)

[Lista jednostek SKU DCsv2](../virtual-machines/dcv2-series.md)

[Kompleksowa ochrona przy użyciu kontenerów poufności seminarium internetowe sesji](https://www.youtube.com/watch?reload=9&v=FYZxtHI_Or0&feature=youtu.be)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
