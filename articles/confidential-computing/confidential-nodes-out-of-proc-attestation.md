---
title: Obsługa zaświadczania poza procesem przy użyciu funkcji Intel SGX Quote Helper elementu daemonset na platformie Azure (wersja zapoznawcza)
description: Elementu daemonset do generowania oferty poza procesem aplikacji SGX. W tym artykule wyjaśniono, jak funkcja zaświadczania out-of-proc jest rovided dla poufnych obciążeń uruchomionych wewnątrz kontenera.
ms.service: container-service
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 5d872032ea5b4e08c3f436dd3bfc202786d8514d
ms.sourcegitcommit: 7edadd4bf8f354abca0b253b3af98836212edd93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/10/2021
ms.locfileid: "102553259"
---
# <a name="platform-software-management-with-sgx-quote-helper-daemon-set-preview"></a>Zarządzanie oprogramowaniem platformy przy użyciu zestawu demona pomocnika cytatu SGX (wersja zapoznawcza)

[Enklawy aplikacje](confidential-computing-enclaves.md) , które wykonują zaświadczanie zdalne, wymagają wygenerowania oferty. Ta oferta zapewnia kryptograficzną weryfikację tożsamości i stanu aplikacji oraz środowisko, w którym enklawy jest uruchomiona. Generowanie oferty wymaga składników oprogramowania zaufanych, które są częścią oprogramowania platformy firmy Intel (PSW).

## <a name="overview"></a>Omówienie
 
Intel obsługuje dwa tryby zaświadczania, aby uruchomić generowanie cytatu:
- **w** procesie: hostuje składniki zaufanego oprogramowania w ramach procesu aplikacji enklawy

- **out-of-proc**: udostępnia zaufane składniki oprogramowania spoza aplikacji enklawy.
 
Aplikacje SGX utworzone przy użyciu zestawu SDK open enklawy domyślnie używają trybu zaświadczania w procesie. Aplikacje oparte na SGX umożliwiają korzystanie z usługi i wymagają dodatkowego hostingu i uwidaczniają wymagane składniki, takie jak enklawy architektury Service Manager (AESM), zewnętrzne dla aplikacji.

Korzystanie z tej funkcji jest **zdecydowanie zalecane**, ponieważ zwiększa to czas pracy aplikacji enklawy podczas aktualizacji platformy Intel lub aktualizacji sterowników DCAP.

Aby włączyć tę funkcję w klastrze AKS, należy zmodyfikować polecenie Add--Enable-sgxquotehelper w interfejsie wiersza polecenia podczas włączania dodatku do obsługi poufnych danych. Szczegółowe instrukcje interfejsu wiersza [polecenia są następujące](confidential-nodes-aks-get-started.md): 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

## <a name="why-and-what-are-the-benefits-of-out-of-proc"></a>Dlaczego i jakie są korzyści z używania poza procesem?

-   Żadne aktualizacje nie są wymagane dla składników generacji oferty PSW dla każdej aplikacji kontenera: w przypadku braku procesu właściciele kontenera nie muszą zarządzać aktualizacjami w ramach ich kontenera. Właściciele kontenera zamiast tego korzystają z interfejsu dostarczonego przez dostawcę, który wywołuje scentralizowaną usługę poza kontenerem, która będzie aktualizowana i zarządzana przez dostawcę.

-   Nie trzeba martwić się o błędy zaświadczania ze względu na nieaktualne składniki PSW: generowanie oferty obejmuje zaufane składniki oprogramowania SW — Quote enklawy (zapytań) & certyfikat aprowizacji enklawy (PCE), który jest częścią zaufanej bazy danych (TCB). Te składniki programu SW muszą być aktualne, aby zachować wymagania dotyczące zaświadczania. Ponieważ dostawca zarządza aktualizacjami tych składników, klienci nie będą musieli zajmować się niepowodzeniami zaświadczania ze względu na nieaktualne składniki programu SW w ramach kontenera.

-   Lepsze wykorzystanie pamięci sygnatury EPC w trybie zaświadczania w procesie, każda aplikacja enklawy musi utworzyć wystąpienie kopii zapytań i PCE dla zdalnego zaświadczania. W przypadku braku procedury nie ma potrzeby, aby kontener obsługiwał te enclaves i w ten sposób nie zużywał pamięci enklawy z przydziału kontenera.

-   Zabezpieczenia przed wymuszeniem jądra, gdy sterownik SGX jest przesyłany strumieniowo do jądra systemu Linux, nastąpi wymuszenie dla enklawy o wyższym poziomie uprawnień. To uprawnienie umożliwia enklawy wywoływanie edytora PCE, co spowoduje uszkodzenie aplikacji enklawy działającej w trybie w procesie. Domyślnie enclaves nie pobieraj tego uprawnienia. Przyznanie tego uprawnienia aplikacji enklawy wymaga wprowadzenia zmian w procesie instalacji aplikacji. Jest to obsługiwane w łatwy sposób jako dostawca usługi, który obsługuje żądania out-of-proc, upewnij się, że usługa jest zainstalowana z tym uprawnieniem.

-   Nie trzeba sprawdzać zgodności z poprzednimi wersjami z PSW & DCAP. Aktualizacje składników generacji oferty PSW są weryfikowane pod kątem zgodności z poprzednimi wersjami przez dostawcę przed aktualizacją. Pomoże to w obsłudze problemów ze zgodnością przed wdrożeniem aktualizacji dla poufnych obciążeń.

## <a name="how-does-the-out-of-proc-attestation-mode-work-for-confidential-workloads-scenario"></a>Jak działa tryb zaświadczania poza procesem dla scenariusza poufności informacji?

Projekt wysokiego poziomu jest zgodny z modelem, w którym obiekt żądający oferty i generowanie oferty są wykonywane oddzielnie, ale na tym samym komputerze fizycznym. Generowanie ofert zostanie wykonane w sposób scentralizowany i będzie zawierać żądania dla CUDZYSŁOWów ze wszystkich jednostek. Interfejs musi być odpowiednio zdefiniowany i wykrywalny dla każdej jednostki, aby zażądać cudzysłowu.

![pomocnik oferty SGX aesm](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Powyższy model abstrakcyjny ma zastosowanie do scenariusza obciążenia poufnego, przez skorzystanie z już dostępnej usługi AESM. AESM jest kontenerem i wdrożonym jako elementu daemonset w klastrze Kubernetes. Kubernetes gwarantuje pojedyncze wystąpienie kontenera usługi AESM, opakowane w pod, do wdrożenia w każdym węźle agenta. Nowy SGX cytat elementu daemonset będzie zależny od SGX-Device-plugin elementu daemonset, ponieważ kontener usługi AESM żąda pamięci sygnatury EPC od SGX-Device-plugin do uruchamiania zapytań i PCE enclaves.

Każdy kontener musi zadecydować, aby użyć generacji cytatu out-of-proc, ustawiając zmienną środowiskową **SGX_AESM_ADDR = 1** podczas tworzenia. Kontener powinien również obejmować pakiet libsgx-Quote-ex, który jest odpowiedzialny za kierowanie żądania do domyślnego gniazda domeny UNIX

Aplikacja może nadal używać zaświadczania w procesie tak jak wcześniej, ale w ramach aplikacji nie można jednocześnie używać zarówno w procesie, jak i poza nią. Infrastruktura out-of-proc jest dostępna domyślnie i zużywa zasoby.

## <a name="sample-implementation"></a>Przykładowa implementacja

Poniżej znajduje się przykładowy plik platformy Docker dla otwartej aplikacji opartej na enklawy. Ustaw zmienną środowiskową SGX_AESM_ADDR = 1 w pliku Docker lub ustaw ją w pliku wdrożenia. Postępuj zgodnie z poniższym przykładem, aby uzyskać szczegółowe informacje dotyczące pliku Docker i wdrożenia YAML. 

  > [!Note] 
  > **Libsgx — oferta —** od firmy Intel musi być spakowana w kontenerze aplikacji w celu zapewnienia prawidłowego działania zaświadczania poza procesem.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for detail
FROM ubuntu:18.04 as sgx_base
RUN apt-get update && apt-get install -y \
    wget \
    gnupg

# Add the repository to sources, and add the key to the list of
# trusted keys used by the apt to authenticate packages
RUN echo "deb [arch=amd64] https://download.01.org/intel-sgx/sgx_repo/ubuntu bionic main" | tee /etc/apt/sources.list.d/intel-sgx.list \
    && wget -qO - https://download.01.org/intel-sgx/sgx_repo/ubuntu/intel-sgx-deb.key | apt-key add -
# Add Microsoft repo for az-dcap-client
RUN echo "deb [arch=amd64] https://packages.microsoft.com/ubuntu/18.04/prod bionic main" | tee /etc/apt/sources.list.d/msprod.list \
    && wget -qO - https://packages.microsoft.com/keys/microsoft.asc | apt-key add -

FROM sgx_base as sgx_sample
RUN apt-get update && apt-get install -y \
    clang-7 \
    libssl-dev \
    gdb \
    libprotobuf10 \
    libsgx-dcap-ql \
    libsgx-quote-ex \
    az-dcap-client \
    open-enclave
WORKDIR /opt/openenclave/share/openenclave/samples/remote_attestation
RUN . /opt/openenclave/share/openenclave/openenclaverc \
    && make build
# this sets the flag for out of proc attestation mode. alternatively you can set this flag on the deployment files
ENV SGX_AESM_ADDR=1 

CMD make run
```
Alternatywnie można ustawić tryb zaświadczania poza procesem w pliku YAML wdrożenia, jak pokazano poniżej

```yaml
apiVersion: batch/v1
kind: Job
metadata:
  name: sgx-test
spec:
  template:
    spec:
      containers:
      - name: sgxtest
        image: <registry>/<repository>:<version>
        env:
        - name: SGX_AESM_ADDR
          value: 1
        resources:
          limits:
            kubernetes.azure.com/sgx_epc_mem_in_MiB: 10
        volumeMounts:
        - name: var-run-aesmd
          mountPath: /var/run/aesmd
      restartPolicy: "Never"
      volumes:
      - name: var-run-aesmd
        hostPath:
          path: /var/run/aesmd
```

## <a name="next-steps"></a>Następne kroki
[Inicjowanie obsługi węzłów poufnych (DCsv2-Series) w witrynie AKS](./confidential-nodes-aks-get-started.md)

[Szybkie przykładowe kontenery poufne](https://github.com/Azure-Samples/confidential-container-samples)

[Lista jednostek SKU DCsv2](../virtual-machines/dcv2-series.md)

<!-- LINKS - external -->
[Azure Attestation]: ../attestation/index.yml


<!-- LINKS - internal -->
[DC Virtual Machine]: /confidential-computing/virtual-machine-solutions
