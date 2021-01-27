---
title: Często zadawane pytania dotyczące obsługi węzłów poufnych w usłudze Azure Kubernetes Service (AKS)
description: Znajdź odpowiedzi na niektóre często zadawane pytania dotyczące usługi Azure Kubernetes Service (AKS) & obsługa węzłów usługi Azure CONFIDENTIAL (ACC).
author: agowdamsft
ms.service: container-service
ms.topic: conceptual
ms.date: 09/22/2020
ms.author: amgowda
ms.openlocfilehash: 51e2095b0df8fdc965926c6c612c45f582c9b9d2
ms.sourcegitcommit: aaa65bd769eb2e234e42cfb07d7d459a2cc273ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/27/2021
ms.locfileid: "98874488"
---
# <a name="frequently-asked-questions-about-confidential-computing-nodes-on-azure-kubernetes-service-aks"></a>Często zadawane pytania dotyczące węzłów danych poufnych w usłudze Azure Kubernetes Service (AKS)

W tym artykule opisano często zadawane pytania dotyczące węzłów obliczeniowych w usłudze Azure Kubernetes Service (AKS) opartych na technologii Intel SGX. Jeśli masz jakieś pytania, Wyślij wiadomość e-mail acconaks@microsoft.com .

## <a name="what-service-level-agreement-sla-and-azure-support-is-provided-during-the-preview"></a>Jakie Umowa dotycząca poziomu usług (SLA) i pomoc techniczna systemu Azure są udostępniane w ramach wersji zapoznawczej? 

Umowa SLA nie jest dostępna w wersji zapoznawczej produktu zgodnie z definicją w [tym miejscu](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). Jednak pomoc techniczna jest świadczona w ramach pomocy technicznej platformy Azure.

## <a name="what-is-attestation-and-how-can-we-do-attestation-of-apps-running-in-enclaves"></a>Co to jest zaświadczanie i jak można zaświadczać aplikacje działające w programie enclaves? 

Zaświadczanie to proces demonstrujący i sprawdzający, czy część oprogramowania została prawidłowo utworzona na określonej platformie sprzętowej. Gwarantuje również, że jego dowody są zweryfikowane w celu zapewnienia pewności, że jest on uruchomiony na bezpiecznej platformie i nie został naruszony. [Przeczytaj więcej](attestation.md) na temat tego, jak Zakończono świadczenie aplikacji enklawy.

## <a name="can-i-enable-accelerated-networking-with-azure-confidential-computing-aks-clusters"></a>Czy mogę włączyć przyspieszone sieci z użyciem poufnego przetwarzania klastrów AKS na platformie Azure? 

Nie. Przyspieszona sieć nie jest obsługiwana w węzłach w trybie poufnego w systemie AKS. Upewnij się, że przyspieszona sieć jest wyłączona we wdrożeniu. 

## <a name="can-i-bring-my-existing-containerized-applications-and-run-it-on-aks-with-azure-confidential-computing"></a>Czy mogę przenieść istniejące aplikacje z kontenerami i uruchamiać je na AKS z użyciem poufnego przetwarzania danych platformy Azure? 

Tak, przejrzyj [stronę kontenery poufne](confidential-containers.md) , aby uzyskać więcej informacji o obiektach zależnych od platformy.

## <a name="what-intel-sgx-driver-version-is-installed-in-the-aks-image"></a>Jaka wersja sterownika Intel SGX jest zainstalowana w obrazie AKS? 

Obecnie maszyny wirtualne DCSv2 na platformie Azure poufne są instalowane z technologią Intel SGX DCAP 1,33. 

## <a name="can-i-open-an-azure-support-ticket-if-i-run-into-issues"></a>Czy mogę otworzyć bilet pomocy technicznej systemu Azure, jeśli wystąpią problemy? 

Tak. Pomoc techniczna platformy Azure jest dostępna w ramach wersji zapoznawczej. Nie jest dołączona umowa SLA, ponieważ produkt jest na etapie wersji zapoznawczej.

## <a name="can-i-inject-post-install-scriptscustomize-drivers-to-the-nodes-provisioned-by-aks"></a>Czy można wstrzyknąć skrypty po instalacji/dostosować sterowniki do węzłów udostępnianych przez AKS? 

Nie. [AKS — poufne węzły obliczeniowe](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) obsługują poufne węzły obliczeniowe, które zezwalają na instalacje niestandardowe.

## <a name="should-i-be-using-a-docker-base-image-to-get-started-on-enclave-applications"></a>Czy należy używać obrazu podstawowego platformy Docker, aby rozpocząć pracę z aplikacjami enklawy? 

Różne opcje obsługi (dostawcy ISV i projekty OSS) zapewniają sposoby włączania kontenerów poufnych. Przejrzyj [stronę kontenery poufne](confidential-containers.md) , aby uzyskać więcej szczegółów i poszczególnych odwołań do implementacji.

## <a name="can-i-run-acc-nodes-with-other-standard-aks-skus-build-a-heterogenous-node-pool-cluster"></a>Czy można uruchamiać węzły kont z innymi standardowymi jednostkami SKU AKS (Tworzenie klastra puli węzłów heterogenicznych)? 

Tak, można uruchamiać różne pule węzłów w ramach tego samego klastra AKS, w tym węzłów konta. Aby kierować aplikacje enklawy na określoną pulę węzłów, rozważ dodanie selektorów węzłów lub zastosowanie limitów EPC. Więcej informacji można znaleźć na stronie Szybki Start na węzłach poufnych [tutaj](confidential-nodes-aks-get-started.md).

## <a name="can-i-run-windows-nodes-and-windows-containers-with-acc"></a>Czy można uruchamiać węzły systemu Windows i kontenery systemu Windows przy użyciu konta? 

Nie w tej chwili. Skontaktuj się z nami, jeśli masz węzły systemu Windows lub kontenery. 

## <a name="what-if-my-container-size-is-more-than-available-epc-memory"></a>Co zrobić, jeśli rozmiar kontenera jest większy niż dostępna pamięć sygnatury EPC? 

Pamięć sygnatury EPC dotyczy części aplikacji, która jest zaprogramowana do wykonania w enklawy. Łączny rozmiar kontenera nie jest prawidłowym sposobem porównywania go z maksymalną dostępną pamięcią sygnatury EPC. W rzeczywistości maszyny DCSv2 z SGXem zezwalają na maksymalną ilość pamięci maszyny wirtualnej wynoszącą 32 GB, w której będzie używany niezaufany składnik aplikacji. Jeśli jednak kontener zużywa więcej niż dostępna pamięć sygnatury EPC, może to mieć wpływ na wydajność części programu uruchomionej w enklawy.

Aby lepiej zarządzać pamięcią EPC w węzłach procesu roboczego, należy wziąć pod uwagę zarządzanie ograniczeniami pamięci SYGNATURy czasowej na podstawie Kubernetes. Postępuj zgodnie z poniższym przykładem jako odwołanie

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
  labels:
    app: sgx-test
spec:
  template:
    metadata:
      labels:
        app: sgx-test
    spec:
      containers:
      - name: sgxtest
        image: oeciteam/sgx-test:1.0
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10 # This limit will automatically place the job into confidential computing node. Alternatively you can target deployment to nodepools
      restartPolicy: Never
  backoffLimit: 0
```

## <a name="what-happens-if-my-enclave-consumes-more-than-maximum-available-epc-memory"></a>Co się stanie, jeśli my enklawy zużywa więcej niż maksymalną dostępną pamięć sygnatury EPC? 

Łączna dostępna pamięć sygnatury EPC jest współdzielona przez aplikacje enklawy na tych samych maszynach wirtualnych lub w węzłach procesu roboczego. Jeśli aplikacja używa pamięci sygnatury EPC więcej niż dostępna, może to mieć wpływ na wydajność aplikacji. Z tego powodu zalecamy ustawienie tolerowania dla aplikacji w pliku YAML wdrożenia w celu lepszego zarządzania dostępną pamięcią sygnatury EPC na węzły procesu roboczego, jak pokazano w powyższych przykładach. Alternatywnie można zawsze wybrać pozycję Przenieś w górę w obszarze rozmiary maszyn wirtualnych puli węzłów procesu roboczego lub dodać więcej węzłów. 

## <a name="why-cant-i-do-forks--and-exec-to-run-multiple-processes-in-my-enclave-application"></a>Dlaczego nie mogę robić rozwidleniów () i exec, aby uruchamiać wiele procesów w mojej aplikacji enklawy? 

Obecnie maszyny wirtualne DCsv2 SKU dotyczące maszyn wirtualnych platformy Azure obsługują jedną przestrzeń adresową dla programu wykonywanego w enklawy. Pojedynczy proces to bieżące ograniczenie, które zaprojektowano w celu zapewnienia wysokiego poziomu zabezpieczeń. Jednakże poufne elementy do obsługi kontenerów mogą mieć alternatywne implementacje w celu pokonania tego ograniczenia.

## <a name="do-you-automatically-install-any-additional-daemonsets-to-expose-the-sgx-drivers"></a>Czy w celu udostępnienia sterowników SGX automatycznie instalowane są dodatkowe daemonsets? 

Tak. Nazwa elementu daemonset to SGX-Device-plugin i SGX-Quote-Helper. Więcej informacji można znaleźć w odpowiednich [celach.](confidential-nodes-aks-overview.md)  

## <a name="what-is-the-vm-sku-i-should-be-choosing-for-confidential-computing-nodes"></a>Co to jest jednostka SKU maszyny wirtualnej, którą należy wybrać w przypadku węzłów z możliwością poufności? 

Jednostki SKU DCSv2. [Jednostki SKU DCSv2](../virtual-machines/dcv2-series.md) są dostępne w [obsługiwanych regionach](https://azure.microsoft.com/global-infrastructure/services/?products=virtual-machines&regions=all).

## <a name="can-i-still-schedule-and-run-non-enclave-containers-on-confidential-computing-nodes"></a>Czy nadal można planować i uruchamiać kontenery inne niż enklawy na poufnych węzłach obliczeniowych? 

Tak. Maszyny wirtualne mają również zwykłe pamięci, które mogą uruchamiać standardowe obciążenia kontenerów. Przed podjęciem decyzji o modelach wdrażania należy rozważyć model zabezpieczeń i zagrożeń aplikacji.

## <a name="can-i-provision-aks-with-dcsv2-node-pools-through-azure-portal"></a>Czy mogę zainicjować AKS z pulami węzłów DCSv2 za pomocą Azure Portal? 

Tak. Interfejs wiersza polecenia platformy Azure może być również używany jako alternatywa w opisany [tutaj](confidential-nodes-aks-get-started.md)sposób.

## <a name="what-ubuntu-version-and-vm-generation-is-supported"></a>Jaka wersja Ubuntu i generacja maszyny wirtualnej są obsługiwane? 

18,04 w generacji 2. 

## <a name="can-we-change-the-current-intel-sgx-dcap-diver-version-on-aks"></a>Czy można zmienić bieżącą wersję firmy Intel SGX DCAP installloadtestaddin na AKS? 

Nie. Aby przeprowadzić instalację niestandardową, zalecamy wybranie opcji [AKS-Engine — poufne wdrożenia węzłów procesu roboczego](https://github.com/Azure/aks-engine/blob/master/docs/topics/sgx.md) . 

## <a name="what-version-of-kubernetes-do-you-support-and-recommend"></a>Która wersja Kubernetes jest obsługiwana i zalecana? 

Firma Microsoft obsługuje i zaleca Kubernetes w wersji 1,16 lub nowszej 

## <a name="what-are-the-known-current-limitation-or-technical-limitations-of-the-product-in-preview"></a>Jakie są znane bieżące ograniczenia lub ograniczenia techniczne produktu w wersji zapoznawczej? 

- Obsługuje tylko węzły maszyny wirtualnej Ubuntu 18,04 Gen 2 
- Brak obsługi węzłów systemu Windows lub obsługa kontenerów systemu Windows
- Skalowanie automatyczne oparte na pamięci sygnatury EPC nie jest obsługiwane. Obsługiwane jest standardowe skalowanie procesora i pamięci.
- Obszary deweloperów w usłudze AKS dla aplikacji poufnych nie są obecnie obsługiwane

## <a name="next-steps"></a>Następne kroki
Przejrzyj [stronę kontenery poufne](confidential-containers.md) , aby uzyskać więcej informacji na temat kontenerów poufnych.