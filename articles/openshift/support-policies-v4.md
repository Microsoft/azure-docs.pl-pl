---
title: Zasady obsługi klastrów w systemie Red Hat OpenShift 4
description: Informacje o wymaganiach dotyczących zasad pomocy technicznej dla Red Hat OpenShift 4
author: sakthi-vetrivel
ms.author: suvetriv
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 03/05/2021
ms.openlocfilehash: 30579536b8051e9a045c217751871287636a3976
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102454282"
---
# <a name="azure-red-hat-openshift-support-policy"></a>Zasady pomocy technicznej usługi Azure Red Hat OpenShift

Niektóre konfiguracje klastrów usługi Azure Red Hat OpenShift 4 mogą mieć wpływ na obsługę klastrów. Azure Red Hat OpenShift 4 umożliwia administratorom klastrów wprowadzanie zmian w wewnętrznych składnikach klastra, ale nie wszystkie zmiany są obsługiwane. Poniższe zasady pomocy technicznej udostępniają modyfikacje, które naruszają zasady, i unieważnią pomoc techniczną firmy Microsoft i Red Hat.

> [!NOTE]
> Funkcje oznaczone jako wersja zapoznawcza technologii OpenShift nie są obsługiwane na platformie Azure Red Hat OpenShift.

## <a name="cluster-configuration-requirements"></a>Wymagania dotyczące konfiguracji klastra

* Wszystkie operatory klastrów OpenShift muszą pozostać w stanie zarządzanym. Listę operatorów klastra można zwrócić przez uruchomienie `oc get clusteroperators` .
* Klaster musi mieć co najmniej trzy węzły procesu roboczego i trzy węzły Menedżera. Nie mają przypisań, które uniemożliwiają planowanie składników OpenShift. Nie Skaluj procesów roboczych klastra do zera lub spróbuj bezpieczniej zamknąć klaster.
* Nie usuwaj ani nie Modyfikuj usług Cluster Prometheus i Alertmanager.
* Nie usuwaj reguł usługi alertów usług.
* Nie usuwaj ani nie Modyfikuj sieciowych grup zabezpieczeń.
* Nie usuwaj ani nie Modyfikuj rejestrowania usługi Azure Red Hat OpenShift Service (procesem MDSD).
* Nie usuwaj ani nie Modyfikuj klucza tajnego ściągania klastra "arosvc.azurecr.io".
* Wszystkie maszyny wirtualne klastra muszą mieć bezpośredni dostęp do Internetu, co najmniej do punktów końcowych Azure Resource Manager (ARM) i rejestrowania usług (Genewa).  Nie jest obsługiwany żaden formularz proxy protokołu HTTPS.
* Nie należy modyfikować konfiguracji DNS sieci wirtualnej klastra. Należy użyć domyślnego programu rozpoznawania Azure DNS.
* Nie Przesłoń żadnego z obiektów MachineConfig klastra (na przykład konfiguracja kubelet) w jakikolwiek sposób.
* Nie ustawiaj żadnych opcji unsupportedConfigOverrides. Ustawienie tych opcji uniemożliwia uaktualnienia wersji pomocniczej.
* Usługa Azure Red Hat OpenShift uzyskuje dostęp do klastra za pośrednictwem usługi linku prywatnego.  Nie usuwaj ani nie Modyfikuj dostępu do usługi.
* Węzły obliczeniowe inne niż RHCOS nie są obsługiwane. Na przykład nie można użyć węzła obliczeniowego RHEL.

## <a name="supported-virtual-machine-sizes"></a>Obsługiwane rozmiary maszyn wirtualnych

Azure Red Hat OpenShift 4 obsługuje wystąpienia węzłów procesu roboczego na następujących rozmiarach maszyn wirtualnych:

### <a name="general-purpose"></a>Ogólnego przeznaczenia

|Seria|Rozmiar|Procesor wirtualny|Pamięć: GiB|
|-|-|-|-|
|Dasv4|Standard_D4as_v4|4|16|
|Dasv4|Standard_D8as_v4|8|32|
|Dasv4|Standard_D16as_v4|16|64|
|Dasv4|Standard_D32as_v4|32|128|
|Dsv3|Standardowa_D4s_v3|4|16|
|Dsv3|Standardowa_D8s_v3|8|32|
|Dsv3|Standardowa_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|

### <a name="memory-optimized"></a>Optymalizacja pod kątem pamięci

|Seria|Rozmiar|Procesor wirtualny|Pamięć: GiB|
|-|-|-|-|
|Esv3|Standardowa_E4s_v3|4|32|
|Esv3|Standardowa_E8s_v3|8|64|
|Esv3|Standardowa_E16s_v3|16|128|
|Esv3|Standardowa_E32s_v3|32|256|

### <a name="compute-optimized"></a>Optymalizacja pod kątem obliczeń

|Seria|Rozmiar|Procesor wirtualny|Pamięć: GiB|
|-|-|-|-|
|Fsv2|Standard_F4s_v2|4|8|
|Fsv2|Standard_F8s_v2|8|16|
|Fsv2|Standard_F16s_v2|16|32|
|Fsv2|Standard_F32s_v2|32|64|

### <a name="master-nodes"></a>Węzły główne

|Seria|Rozmiar|Procesor wirtualny|Pamięć: GiB|
|-|-|-|-|
|Dsv3|Standardowa_D8s_v3|8|32|
|Dsv3|Standardowa_D16s_v3|16|64|
|Dsv3|Standard_D32s_v3|32|128|
