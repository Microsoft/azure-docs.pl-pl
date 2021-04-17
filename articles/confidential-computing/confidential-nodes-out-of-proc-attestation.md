---
title: Obsługa zaświadczenia za pomocą pomocnika oferty Intel SGX DaemonSet na platformie Azure (wersja zapoznawcza)
description: DaemonSet do generowania oferty poza procesem aplikacji Intel SGX. W tym artykule wyjaśniono, w jaki sposób zapewniana jest możliwość zaświadczenia poza procesem dla poufnych obciążeń uruchamianych wewnątrz kontenera.
ms.service: container-service
ms.subservice: confidential-computing
author: agowdamsft
ms.topic: overview
ms.date: 2/12/2021
ms.author: amgowda
ms.openlocfilehash: 849fd7afa3f9365f31ee8e03d9f9cc2174d64304
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107484408"
---
# <a name="platform-software-management-with-intel-sgx-quote-helper-daemonset-preview"></a>Zarządzanie oprogramowaniem platformy za pomocą pomocnika oferty Intel SGX DaemonSet (wersja zapoznawcza)

[Aplikacje enklawy,](confidential-computing-enclaves.md) które wykonują zdalne zaświadczenia, wymagają wygenerowanego cudzysłowu. Ten cytat zawiera kryptograficzne potwierdzenie tożsamości i stanu aplikacji, a także środowiska, w którym działa enklawa. Generowanie oferty wymaga zaufanych składników oprogramowania, które są częścią oprogramowania Intel Platform Software Components (PSW).

## <a name="overview"></a>Omówienie
 
Firma Intel obsługuje dwa tryby zaświadczenia, aby uruchomić generowanie oferty:

- *Proces w procesie* hostuje zaufane składniki oprogramowania w procesie aplikacji enklawy.

- *Poza procesem składniki* zaufanego oprogramowania hostują się poza aplikacją enklawy.
 
Aplikacje Intel Software Guard Extension (Intel SGX) sbudowaną przy użyciu zestawu SDK Open Enclave domyślnie używają trybu zaświadczenia w procesie. Aplikacje oparte na technologii Intel SGX umożliwiają tryb zaświadczenia poza procesem. Jeśli chcesz użyć tego trybu, potrzebujesz dodatkowego hostingu i musisz uwidocznić wymagane składniki, takie jak enklawa architektury Service Manager (AESM), poza aplikacją.

Ta funkcja zwiększa czas pracy aplikacji enklawy podczas aktualizacji platformy Intel lub aktualizacji sterowników DCAP. Z tego powodu zalecamy używanie go.

Aby włączyć tę funkcję w klastrze usług Azure Kubernetes Services (AKS), po włączeniu dodatku poufnego przetwarzania dodaj polecenie do interfejsu wiersza `--enable-sgxquotehelper` polecenia platformy Azure. 

```azurecli-interactive
# Create a new AKS cluster with system node pool with Confidential Computing addon enabled and SGX Quote Helper
az aks create -g myResourceGroup --name myAKSCluster --generate-ssh-keys --enable-addon confcom --enable-sgxquotehelper
```

Aby uzyskać więcej informacji, zobacz Szybki start: wdrażanie klastra AKS z poufnymi węzłami obliczeniowymi [przy użyciu interfejsu wiersza polecenia platformy Azure.](confidential-nodes-aks-get-started.md)

## <a name="benefits-of-the-out-of-process-mode"></a>Zalety trybu poza procesem

Na poniższej liście opisano niektóre główne zalety tego trybu zaświadczenia:

-   Żadne aktualizacje nie są wymagane dla składników generowania ofert programu PSW dla każdej konteneryzowanej aplikacji. Właściciele kontenerów nie muszą zarządzać aktualizacjami w kontenerze. Właściciele kontenerów zamiast tego polegają na interfejsie dostawcy, który wywołuje scentralizowaną usługę poza kontenerem. Dostawca aktualizuje kontener i zarządza tym kontenerem.

-   Nie musisz martwić się o błędy zaświadczenia z powodu aktualnych składników psw. Dostawca zarządza aktualizacjami tych składników.

-   Tryb poza procesem zapewnia lepsze wykorzystanie pamięci EPC niż tryb przetwarzania. W trybie przetwarzania każda aplikacja enklawy musi utworzyć wystąpienia kopii funkcji QE i PCE na potrzeby zdalnego zaświadczenia. W trybie poza procesem kontener nie musi hostować tych enklaw, dlatego nie zużywa pamięci enklawy z limitu przydziału kontenera.

-   Gdy przekierowywasz sterownik Intel SGX do jądra systemu Linux, istnieje wymuszanie, aby enklawa ma wyższe uprawnienia. To uprawnienie umożliwia enklawę wywoływanie pce, co spowoduje przerwania działania aplikacji enklawy w trybie przetwarzania. Domyślnie enklawy nie mają tego uprawnienia. Przyznanie tego uprawnienia aplikacji enklawy wymaga zmian w procesie instalacji aplikacji. Z kolei w trybie poza procesem dostawca usługi, który obsługuje żądania poza procesem, zapewnia, że usługa jest zainstalowana z tym uprawnieniem.

-   Nie musisz sprawdzać zgodności z poprzednimi wersjami z psw i DCAP. Aktualizacje składników generowania oferty programu PSW są weryfikowane pod kątem zgodności z poprzednimi wersjami przez dostawcę przed aktualizacją. Ułatwia to obsługę problemów ze zgodnością przed wdrożeniem aktualizacji dla poufnych obciążeń.

## <a name="confidential-workloads"></a>Poufne obciążenia

Żądanie oferty i generowanie oferty są uruchamiane oddzielnie, ale na tej samej maszynie fizycznej. Generowanie ofert jest scentralizowane i obsługuje żądania ofert ze wszystkich jednostek. Aby każda jednostka żądała cudzysłowów, interfejs musi być prawidłowo zdefiniowany i wykrywalny.

![Diagram przedstawiający relacje między żądacym oferty, generowaniem oferty i interfejsem.](./media/confidential-nodes-out-of-proc-attestation/aesmmanager.png)

Ten model abstrakcyjny ma zastosowanie do scenariusza poufnego obciążenia, korzystając z usługi AESM, która jest już dostępna. Usługa AESM jest konteneryzowana i wdrażana jako zestaw DaemonSet w klastrze Kubernetes. Usługa Kubernetes gwarantuje wdrożenie pojedynczego wystąpienia kontenera usługi AESM, opakowanego w zasobnik, w każdym węźle agenta. Nowy zestaw DaemonSet oferty Intel SGX będzie miał zależność od zestawu DaemonSet sgx-device-plugin, ponieważ kontener usługi AESM żąda pamięci EPC z wtyczki sgx-device-plugin w celu uruchamiania enklaw QE i PCE.

Każdy kontener musi wyrazić zgodę na użycie generowania oferty poza procesem przez ustawienie zmiennej środowiskowej `SGX_AESM_ADDR=1` podczas tworzenia. Kontener powinien również zawierać pakiet libsgx-quote-ex, który jest odpowiedzialny za kierowanie żądania do domyślnego gniazda domeny systemu Unix.

Aplikacja może nadal korzystać z zaświadczenia w procesie, tak jak wcześniej, ale nie można używać jednocześnie procesu i poza procesem w aplikacji. Infrastruktura poza procesem jest domyślnie dostępna i zużywa zasoby.

## <a name="sample-implementation"></a>Przykładowa implementacja

Poniższy plik platformy Docker jest przykładem aplikacji opartej na otwartej enklawie. Ustaw `SGX_AESM_ADDR=1` zmienną środowiskową w pliku platformy Docker lub ustawiając ją w pliku wdrożenia. Poniższy przykład zawiera szczegółowe informacje dotyczące pliku i wdrożenia platformy Docker. 

  > [!Note] 
  > Aby zaświadczenia poza procesem działały prawidłowo, biblioteka libsgx-quote-ex firmy Intel musi zostać spakowana w kontenerze aplikacji.
    
```yaml
# Refer to Intel_SGX_Installation_Guide_Linux for details
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
# This sets the flag for out-of-process attestation mode. Alternatively you can set this flag on the deployment files.
ENV SGX_AESM_ADDR=1 

CMD make run
```
Alternatywnie można ustawić tryb zaświadczenia poza procesem w pliku yaml wdrożenia. Oto kroki tej procedury:

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

[Szybki start: wdrażanie klastra usługi AKS z poufnymi węzłami obliczeniowymi przy użyciu interfejsu wiersza polecenia platformy Azure](./confidential-nodes-aks-get-started.md)

[Szybkie przykłady początkowe kontenerów poufnych](https://github.com/Azure-Samples/confidential-container-samples)

[Jednostki SKU DCsv2](../virtual-machines/dcv2-series.md)
