---
title: Poufne węzły obliczeniowe w usłudze Azure Kubernetes Service (AKS) — publiczna wersja zapoznawcza
description: Poufne węzły obliczeniowe w AKS
services: virtual-machines
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: 1b945ac9f656a227bcc3335cb0ec995626f98f77
ms.sourcegitcommit: 04fb3a2b272d4bbc43de5b4dbceda9d4c9701310
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94564178"
---
# <a name="confidential-computing-nodes-on-azure-kubernetes-service-public-preview"></a>Poufne węzły obliczeniowe w usłudze Azure Kubernetes Service (publiczna wersja zapoznawcza)

Dane [poufne platformy Azure](overview.md) umożliwiają ochronę poufnych danych, gdy są używane. Podstawowe infrastruktury chronią te dane od innych aplikacji, administratorów i dostawców chmury przy użyciu sprzętowych środowisk kontenera zaufanych wykonań.

## <a name="overview"></a>Omówienie

Usługa Azure Kubernetes Service (AKS) obsługuje dodawanie [niejawnych węzłów obliczeniowych DCsv2](confidential-computing-enclaves.md) obsługiwanych przez SGX firmy Intel. Te węzły uruchamiają program mogą uruchamiać poufne obciążenia w ramach sprzętowego środowiska wykonawczego (TEE), umożliwiając kod na poziomie użytkownika do przydzielenia prywatnych regionów pamięci. Te prywatne regiony pamięci są nazywane enclaves. Enclaves zaprojektowano ochronę kodu i danych z procesów uruchomionych na wyższym poziomie uprawnień. Model wykonywania SGX usuwa warstwy pośrednie systemu operacyjnego gościa, system operacyjny hosta i funkcję hypervisor. Model *wykonywania izolowanego sprzętu na kontener* umożliwia aplikacjom bezpośrednie wykonywanie z użyciem procesora CPU przy zachowaniu specjalnego bloku pamięci zaszyfrowanej. Węzły do przetwarzania poufnego pomagają w ogólnym stan zabezpieczeń aplikacji kontenera na AKS, a także za pomocą doskonałej strategii dotyczącej kontenera. 

![SGX węzeł — Omówienie](./media/confidential-nodes-aks-overview/sgxaksnode.jpg)

## <a name="aks-confidential-nodes-features"></a>Funkcje AKS poufne węzły

- Izolacja kontenerów na poziomie sprzętu i procesu za pośrednictwem SGXego środowiska wykonawczego (TEE) 
- Klastry puli węzłów heterogenicznych (mieszane poufne i niepoufne pule węzłów)
- Planowanie oparte na pamięci podręcznej stronicowanej strony (EPC)
- Wstępnie zainstalowany sterownik SGX DCAP
- Preinstalowano poprawkę Intel FSGS
- Obsługuje użycie procesora CPU w poziomie na podstawie skalowania automatycznego i automatycznego skalowania klastra
- Pomocnik zaświadczania o procesie za poorednictwem AKS elementu daemonset
- Obsługa kontenerów systemu Linux w węzłach procesu roboczego maszyn wirtualnych Ubuntu 18,04 Gen 2

## <a name="aks-provided-daemon-sets-addon"></a>Zestawy demonów AKS (dodatek)

#### <a name="sgx-device-plugin"></a>Wtyczka urządzenia SGX <a id="sgx-plugin"></a>

Wtyczka urządzenia SGX implementuje interfejs wtyczki urządzenia Kubernetes dla pamięci sygnatury EPC. Efektywnie Ta wtyczka sprawia, że pamięć sygnatury EPC jest dodatkowym typem zasobu w Kubernetes. Użytkownicy mogą określić limity dla tego zasobu, tak jak inne zasoby. Oprócz funkcji planowania wtyczka urządzenia pomaga przypisać uprawnienia sterownika urządzenia SGX do poufnych kontenerów obciążenia. Przykładowa implementacja przykładowego wdrożenia opartego na pamięci sygnatury EPC ( `kubernetes.azure.com/sgx_epc_mem_in_MiB` ) jest [tutaj](https://github.com/Azure-Samples/confidential-computing/blob/main/containersamples/helloworld/helm/templates/helloworld.yaml)

#### <a name="sgx-quote-helper-service"></a>Usługa pomocnika cytatu SGX <a id="sgx-quote"></a>

Enklawy aplikacje, które wykonują zaświadczanie zdalne, muszą generować oferty. Oferta zapewnia kryptograficzną tożsamość i stan aplikacji oraz środowisko, w którym enklawy jest uruchomiona. Generowanie oferty polega na pewnych zaufanych składnikach oprogramowania firmy Intel, które są częścią oprogramowania SGX platform Components (PSW/DCAP). Ten PSW jest spakowany jako zestaw demonów, który działa na węzeł. Można go wykorzystać podczas żądania oferty zaświadczania z aplikacji enklawy. Użycie usługi AKS zapewnia lepszą zgodność między PSW i innymi składnikami oprogramowania SW na hoście. [Przeczytaj więcej](confidential-nodes-out-of-proc-attestation.md) na temat jego użycia i szczegółów funkcji.

## <a name="programming--application-models"></a>Programowanie & modeli aplikacji

### <a name="confidential-containers"></a>Kontenery poufne

[Kontenery poufne](confidential-containers.md) uruchamiają istniejące programy i najpopularniejsze środowiska uruchomieniowe **języka programowania** (Python, Node, Java itp.) wraz z istniejącymi zależnościami biblioteki, bez konieczności modyfikacji lub ponownej kompilacji kodu źródłowego. Ten model to najszybszy model do poufności z włączoną obsługą projektów open source, & partnerzy platformy Azure. Obrazy kontenerów, które są tworzone jako gotowe do uruchomienia w enclaves Secure, są określane jako kontenery poufne.

### <a name="enclave-aware-containers"></a>Kontenery obsługujące enklawy

AKS obsługuje aplikacje, które są programowane do uruchamiania w węzłach poufnych i wykorzystują **specjalny zestaw instrukcji** udostępniony za pomocą zestawów SDK i struktur. Ten model aplikacji zapewnia największą kontrolę nad aplikacjami przy użyciu najmniejszej zaufanej bazy obliczeniowej (TCB). [Przeczytaj więcej](enclave-aware-containers.md) na temat kontenerów z obsługą enklawy.

## <a name="next-steps"></a>Następne kroki

[Wdróż klaster AKS z węzłami w postaci poufnego przetwarzania](./confidential-nodes-aks-get-started.md)

[Szybkie początkowe przykłady kontenera](https://github.com/Azure-Samples/confidential-container-samples)

[Lista jednostek SKU DCsv2](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions