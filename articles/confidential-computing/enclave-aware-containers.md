---
title: Kontenery obsługujące enklawy na platformie Azure
description: Obsługa kontenerów aplikacji gotowych enklawy w usłudze Azure Kubernetes Service (AKS)
author: agowdamsft
ms.service: container-service
ms.topic: overview
ms.date: 9/22/2020
ms.author: amgowda
ms.openlocfilehash: b4a348bbc73e6cfb2fabaa8e401af0ddf7265354
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102561912"
---
# <a name="enclave-aware-containers"></a>Kontenery obsługujące enklawy

Enklawy jest chronionym regionem pamięci, który zapewnia poufność danych i wykonywania kodu. Jest to wystąpienie zaufanego środowiska wykonawczego (TEE), które jest chronione przez sprzęt. Węzły przetwarzania poufnego w systemie AKS używają [rozszerzeń funkcji firmy Intel (SGX)](https://software.intel.com/sgx) do tworzenia izolowanych środowisk enklawy w węzłach między każdą aplikacją kontenera.

Podobnie jak w przypadku maszyn wirtualnych Intel SGX, aplikacje kontenerów opracowane do działania w enclaves mają dwa składniki:

- Niezaufany składnik (nazywany hostem) i
- Zaufany składnik (o nazwie enklawy).

![Architektura kontenera enklawy aware](./media/enclave-aware-containers/enclaveawarecontainer.png)

Architektura aplikacji kontenerów z obsługą enklawyów zapewnia największą kontrolę nad implementacją, zachowując kod w enklawy niskim. Minimalizacja kodu, który jest uruchamiany w enklawy, pomaga zmniejszyć liczbę obszarów narażonych na ataki.   

## <a name="enablers"></a>Czynników umożliwiających istnienie

### <a name="open-enclave-sdk"></a>Open Enclave SDK
Open enklawy SDK to biblioteka typu "open source" sprzętu niezależny od, która umożliwia tworzenie aplikacji w języku C++, które wykorzystują sprzętowe środowiska wykonawcze oparte na sprzęcie. Bieżąca implementacja zapewnia obsługę technologii Intel SGX, a także obsługę wersji zapoznawczej dla [systemu operacyjnego op-tee w usłudze ARM TrustZone](https://optee.readthedocs.io/en/latest/general/about.html).

Zacznij korzystać z otwartej [aplikacji kontenera](https://github.com/openenclave/openenclave/tree/master/docs/GettingStartedDocs) opartej na enklawy

### <a name="intel-sgx-sdk"></a>Zestaw SDK Intel SGX
Intel obsługuje zestaw Software Development Kit do tworzenia aplikacji SGX dla obciążeń kontenera systemu Linux i Windows. Kontenery systemu Windows obecnie nieobsługiwane przez AKSe poufne komputery.

Rozpocznij pracę z aplikacjami opartymi na technologii Intel SGX [tutaj](https://software.intel.com/content/www/us/en/develop/topics/software-guard-extensions/sdk.html)

### <a name="confidential-consortium-framework-ccf"></a>Struktura konsorcjum poufnego (CCF)
Struktura konsorcjum poufnego (CCF) to platforma typu "open source" służąca do tworzenia nowej kategorii bezpiecznych, wysoko dostępnych i wydajnych aplikacji, które koncentrują się na obliczeniach i danych obejmujących wiele firm. CCF mogą włączać wysoce skalowane, poufne sieci spełniające podstawowe wymagania przedsiębiorstwa, co zapewnia możliwość przyspieszenia produkcji i wdrożenia korporacyjnego opartego na konsorcjum łańcucha bloków i wieloskładnikowej technologii obliczeniowej.

Zacznij korzystać z usługi Azure CONFIDENTIAL i CCF [tutaj](https://github.com/Microsoft/CCF)

### <a name="confidential-inferencing-onnx-runtime"></a>Środowisko uruchomieniowe poufnego wnioskowania ONNX

Środowisko uruchomieniowe ONNX (Open Source enklawy) ustanawia bezpieczny kanał między klientem a usługą wnioskowania — bez zapewnienia, że ani żądanie, ani odpowiedź nie mogą opuścić bezpiecznego enklawy. 

To rozwiązanie pozwala przenieść istniejący model UCZENIa maszynowego i uruchamiać je w sposób poufny, jednocześnie zapewniając zaufanie między klientem i serwerem przez zaświadczanie i weryfikacje. 

Wprowadzenie do podnoszenia modelu ML i przesunięcia do środowiska uruchomieniowego ONNX w [tym miejscu](https://aka.ms/confidentialinference)

### <a name="edgeless-rt"></a>RT

RT bezgraniczny jest projektem "open source", który kompiluje się w otwartym zestawie SDK enklawy. Dodaje obsługę języka go i dodatkowych funkcji C++. Zacznij korzystać z prostej aplikacji poufnej przy użyciu znajomego środowiska VS Code w [tym miejscu](https://github.com/edgelesssys/edgelessrt). W przypadku aplikacji bezbrzegowych na AKS wykonaj instrukcje [tutaj](https://github.com/edgelesssys/edgelessrt/blob/master/docs/ERTAzureAKSDeployment.md)


## <a name="container-based-sample-implementations"></a>Przykładowe implementacje oparte na kontenerach

[Przykłady platformy Azure dla kontenerów z obsługą enklawy na AKS](https://github.com/Azure-Samples/confidential-computing/tree/main/containersamples)

<!-- LINKS - external -->
[Azure Attestation](../attestation/overview.md)


<!-- LINKS - internal -->
[Maszyna](./virtual-machine-solutions.md) 
 wirtualna DC [Kontenery poufne](./confidential-containers.md)