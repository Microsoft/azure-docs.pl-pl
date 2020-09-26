---
title: Omówienie izolacji i prywatności sieci wirtualnej
titleSuffix: Azure Machine Learning
description: Użyj izolowanego Virtual Network platformy Azure z Azure Machine Learning, aby zabezpieczyć zasoby obszaru roboczego i środowiska obliczeniowe.
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.reviewer: larryfr
ms.author: aashishb
author: aashishb
ms.date: 09/25/2020
ms.topic: conceptual
ms.custom: how-to, devx-track-python, references_regions
ms.openlocfilehash: 1690d4b236fce53e033f08fa6825eefe6359d9e9
ms.sourcegitcommit: d95cab0514dd0956c13b9d64d98fdae2bc3569a0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91362218"
---
# <a name="virtual-network-isolation-and-privacy-overview"></a>Omówienie izolacji i prywatności sieci wirtualnej

W tym artykule dowiesz się, jak używać sieci wirtualnych (sieci wirtualnych) do zabezpieczania komunikacji sieciowej w Azure Machine Learning. W tym artykule opisano sposób konfigurowania kompletnej sieci wirtualnej przy użyciu przykładowego scenariusza.

Ten artykuł jest częścią serii składającej się z pięciu części, która przeprowadzi Cię przez proces zabezpieczania przepływów pracy Azure Machine Learning. Zdecydowanie zalecamy zapoznanie się z tym artykułem przeglądu, aby poznać pojęcia w pierwszej kolejności. 

Oto inne artykuły w tej serii:

**1. Sieć wirtualna — Omówienie**  >  [2. Zabezpiecz obszar roboczy](how-to-secure-workspace-vnet.md)  >  [3. Zabezpiecz środowisko szkoleniowe](how-to-secure-training-vnet.md)  >  [4. Zabezpiecz środowisko inferencing](how-to-secure-inferencing-vnet.md)  >  [5. Włącz funkcje programu Studio](how-to-enable-studio-virtual-network.md)

## <a name="prerequisites"></a>Wymagania wstępne

W tym artykule przyjęto założenie, że masz wiedzę o następujących tematach:
+ [Sieci wirtualne platformy Azure](https://docs.microsoft.com/azure/virtual-network/virtual-networks-overview)
+ [Sieć IP](https://docs.microsoft.com/azure/virtual-network/virtual-network-ip-addresses-overview-arm)
+ [Link prywatny platformy Azure](how-to-configure-private-link.md)
+ [Sieciowe grupy zabezpieczeń (sieciowej grupy zabezpieczeń)](../virtual-network/security-overview.md)
+ [Zapory sieciowe](../firewall/overview.md)

## <a name="example-scenario"></a>Przykładowy scenariusz

W tej sekcji dowiesz się, w jaki sposób typowy scenariusz sieci jest skonfigurowany do zabezpieczenia Azure Machine Learning komunikacji z prywatnymi adresami IP.

W poniższej tabeli porównano, w jaki sposób usługi uzyskują dostęp do różnych części sieci Azure Machine Learning zarówno za pomocą sieci wirtualnej, jak i bez sieci wirtualnej.

| Scenariusz | Workspace | Skojarzone zasoby | Szkolenie środowiska obliczeniowego | Środowisko obliczeniowe Inferencing |
|-|-|-|-|-|-|
|**Brak sieci wirtualnej**| Publiczny adres IP | Publiczny adres IP | Publiczny adres IP | Publiczny adres IP |
|**Zabezpieczanie zasobów w sieci wirtualnej**| Prywatny adres IP (prywatny punkt końcowy) | Publiczny adres IP (punkt końcowy usługi) <br> **oraz** <br> Prywatny adres IP (prywatny punkt końcowy) | Prywatny adres IP | Prywatny adres IP  | 

* **Obszar roboczy** — Utwórz prywatny punkt końcowy w sieci wirtualnej, aby nawiązać połączenie z prywatnym linkiem w obszarze roboczym. Prywatny punkt końcowy nawiązuje połączenie obszaru roboczego z siecią wirtualną za pomocą kilku prywatnych adresów IP.
* **Skojarzone** zasoby — umożliwia korzystanie z punktów końcowych usługi lub prywatnych punktów końcowych w celu łączenia się z zasobami obszaru roboczego, takimi jak Azure storage, Azure Key Vault i Azure Container Services.
    * **Punkty końcowe usługi** zapewniają tożsamość sieci wirtualnej dla usługi platformy Azure. Po włączeniu punktów końcowych usługi w sieci wirtualnej możesz dodać regułę sieci wirtualnej, aby zabezpieczyć zasoby usługi platformy Azure w sieci wirtualnej. Punkty końcowe usługi używają publicznych adresów IP.
    * **Prywatne punkty końcowe** są interfejsami sieciowymi, które bezpiecznie łączą się z usługą za pomocą prywatnego linku platformy Azure. Prywatny punkt końcowy używa prywatnego adresu IP z sieci wirtualnej, efektywnie przenosząc usługę do sieci wirtualnej.
* **Szkolenia związane z dostępem do obliczeń** — cele obliczeniowe, takie jak Azure Machine Learning wystąpienia obliczeniowe i Azure Machine Learning klastrów obliczeniowych, bezpiecznie z prywatnymi adresami IP. 
* **Inferencing COMPUTE Access** — dostęp do klastrów obliczeniowych usługi Azure Kubernetes Services (AKS) z prywatnymi adresami IP.


W następnych pięciu sekcjach pokazano, jak zabezpieczyć scenariusz sieci opisany powyżej. Aby zabezpieczyć sieć, musisz:

1. Zabezpiecz [**obszar roboczy i skojarzone zasoby**](#secure-the-workspace-and-associated-resources).
1. Zabezpiecz [**środowisko szkoleniowe**](#secure-the-training-environment).
1. Zabezpiecz [**środowisko inferencing**](#secure-the-inferencing-environment).
1. Opcjonalnie: [**Włącz funkcje programu Studio**](#optional-enable-studio-functionality).
1. Konfigurowanie [ **ustawień zapory**](#configure-firewall-settings)

## <a name="secure-the-workspace-and-associated-resources"></a>Zabezpieczanie obszaru roboczego i skojarzonych zasobów

Wykonaj następujące kroki, aby zabezpieczyć obszar roboczy i skojarzone zasoby. Te kroki umożliwiają komunikację usług w sieci wirtualnej.

1. Utwórz [prywatny obszar roboczy z obsługą linków](how-to-secure-workspace-vnet.md#secure-the-workspace-with-private-endpoint) , aby umożliwić komunikację między siecią wirtualną i obszarem roboczym.
1. Dodaj Azure Key Vault do sieci wirtualnej za pomocą [punktu końcowego usługi](../key-vault/general/overview-vnet-service-endpoints.md) lub [prywatnego punktu końcowego](../key-vault/general/private-link-service.md). Ustaw Key Vault na ["Zezwalaj zaufanym usługom firmy Microsoft na ominięcie tej zapory"](how-to-secure-workspace-vnet.md#secure-azure-key-vault).
1. Dodawanie konta usługi Azure Storage do sieci wirtualnej przy użyciu [punktu końcowego usługi](how-to-secure-workspace-vnet.md#secure-azure-storage-accounts) lub [prywatnego punktu końcowego](../storage/common/storage-private-endpoints.md)
1. [Skonfiguruj Azure Container Registry do korzystania z prywatnego punktu końcowego](how-to-secure-workspace-vnet.md#enable-azure-container-registry-acr) i [Włącz delegowanie podsieci w programie Azure Container Instances](how-to-secure-inferencing-vnet.md#enable-azure-container-instances-aci).

![Diagram architektury pokazujący, jak obszar roboczy i powiązane zasoby komunikują się ze sobą za pośrednictwem punktów końcowych usługi lub prywatnych punktów końcowych wewnątrz sieci wirtualnej](./media/how-to-network-security-overview/secure-workspace-resources.png)

Aby uzyskać szczegółowe instrukcje dotyczące wykonywania tych kroków, zobacz temat [zabezpieczanie Azure Machine Learning obszaru roboczego](how-to-secure-workspace-vnet.md). 

### <a name="limitations"></a>Ograniczenia

Zabezpieczanie obszaru roboczego i skojarzonych z nim zasobów w ramach sieci wirtualnej ma następujące ograniczenia:
- Link prywatny obszaru roboczego jest dostępny tylko w następujących regionach:
    - **East US**
    - **South Central US**
    - **Zachodnie stany USA**
    - **Zachodnie stany USA 2**
    - **Kanada środkowa**
    - **Southeast Asia**
    - **Japan East**
    - **Europa Północna**
    - **Australia Wschodnia**
    - **Południowe Zjednoczone Królestwo**
    
    To ograniczenie nie ma zastosowania do skojarzonych zasobów. Na przykład możesz włączyć sieć wirtualną dla magazynu w dowolnym Azure Machine Learning regionie.
- Wszystkie zasoby muszą należeć do tej samej sieci wirtualnej. Jednak podsieci w tej samej sieci wirtualnej są dozwolone.

## <a name="secure-the-training-environment"></a>Zabezpiecz środowisko szkoleniowe

W tej sekcji dowiesz się, jak zabezpieczyć środowisko szkoleniowe w Azure Machine Learning. Dowiesz się również, jak Azure Machine Learning wykonuje zadanie szkoleniowe, aby zrozumieć, jak działają konfiguracje sieci.

Aby zabezpieczyć środowisko szkoleniowe, wykonaj następujące czynności:

1. Utwórz [wystąpienie obliczeniowe Azure Machine Learning i klaster komputera w sieci wirtualnej](how-to-secure-training-vnet.md#compute-instance) , aby uruchomić zadanie szkoleniowe.
1. [Zezwalaj na komunikację przychodzącą z usługi Azure Batch](how-to-secure-training-vnet.md#mlcports) , aby usługa Batch mogła przesyłać zadania do zasobów obliczeniowych. 

![Diagram architektury przedstawiający sposób zabezpieczania zarządzanych klastrów obliczeniowych i wystąpień](./media/how-to-network-security-overview/secure-training-environment.png)

Aby uzyskać szczegółowe instrukcje dotyczące wykonywania tych kroków, zobacz temat [zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md). 

### <a name="example-training-job-submission"></a>Przykładowe przesyłanie zadania szkoleniowego 

W tej sekcji dowiesz się, jak Azure Machine Learning bezpiecznie komunikuje się między usługami w celu przesłania zadania szkoleniowego. Przedstawiono w nim sposób, w jaki wszystkie konfiguracje współpracują ze sobą w celu zabezpieczenia komunikacji.

1. Klient przekazuje skrypty szkoleniowe i dane szkoleniowe do kont magazynu zabezpieczonych za pomocą usługi lub prywatnego punktu końcowego.

1. Klient przesyła zadanie szkolenia do obszaru roboczego Azure Machine Learning za pomocą prywatnego punktu końcowego.

1. Usługa Azure Batch Services odbierze zadanie z obszaru roboczego i prześle zadanie szkolenia do środowiska obliczeniowego za pośrednictwem publicznego modułu równoważenia obciążenia, który został zainicjowany przy użyciu zasobu obliczeniowego. 

1. Zasób obliczeniowy otrzymuje zadanie i rozpocznie szkolenie. Zasoby obliczeniowe uzyskują dostęp do kont bezpiecznego magazynu, aby pobierać pliki szkoleniowe i przesyłać dane wyjściowe. 

![Diagram architektury pokazujący, jak zostanie przesłane zadanie szkolenia Azure Machine Learning podczas korzystania z sieci wirtualnej](./media/how-to-network-security-overview/secure-training-job-submission.png)


### <a name="limitations"></a>Ograniczenia

- Wystąpienia obliczeniowe platformy Azure i Klastry obliczeniowe platformy Azure muszą znajdować się w tej samej sieci wirtualnej, regionie i subskrypcji, co obszar roboczy i skojarzone z nią zasoby. 

## <a name="secure-the-inferencing-environment"></a>Zabezpiecz środowisko inferencing

Ta sekcja zawiera informacje o opcjach dostępnych w celu zabezpieczenia środowiska inferencing. Zalecamy używanie klastrów usługi Azure Kubernetes Services (AKS) na potrzeby wdrożeń produkcyjnych o dużej skali.

Dostępne są dwie opcje klastrów AKS w sieci wirtualnej:

- Wdróż lub Dołącz domyślny klaster AKS do sieci wirtualnej.
- Dołącz prywatny klaster AKS do sieci wirtualnej.

**Domyślne klastry AKS** mają płaszczyznę kontroli z publicznymi adresami IP. Można dodać domyślny klaster AKS do sieci wirtualnej podczas wdrażania lub dołączyć klaster po jego utworzeniu.

**Prywatne klastry AKS** mają płaszczyznę kontroli, do której można uzyskać dostęp tylko za pomocą prywatnych adresów IP. Prywatne klastry AKS muszą być dołączone po utworzeniu klastra.

Aby uzyskać szczegółowe instrukcje dotyczące dodawania klastrów domyślnych i prywatnych, zobacz temat [zabezpieczanie środowiska inferencing](how-to-secure-inferencing-vnet.md). 

Poniższy diagram sieciowy przedstawia zabezpieczony obszar roboczy Azure Machine Learning z prywatnym klastrem AKS podłączonym do sieci wirtualnej.

![Diagram architektury przedstawiający sposób dołączania prywatnego klastra AKS do sieci wirtualnej. Płaszczyzna kontroli AKS jest umieszczana poza siecią wirtualną klienta](./media/how-to-network-security-overview/secure-inferencing-environment.png)

### <a name="limitations"></a>Ograniczenia
- Klastry AKS muszą należeć do tej samej sieci wirtualnej, co obszar roboczy i skojarzone z nią zasoby. 

## <a name="optional-enable-studio-functionality"></a>Opcjonalne: Włącz funkcje programu Studio

[Zabezpieczanie obszaru roboczego](#secure-the-workspace-and-associated-resources)  >  [Zabezpiecz środowisko](#secure-the-training-environment)  >  szkoleniowe [Zabezpiecz środowisko inferencing](#secure-the-inferencing-environment)  >  **Włącz funkcje**  >  programu Studio [Konfigurowanie ustawień zapory](#configure-firewall-settings)

Chociaż Studio może uzyskać dostęp do danych na koncie magazynu skonfigurowanym za pomocą punktu końcowego usługi, niektóre funkcje są domyślnie wyłączone:

* Podgląd danych w Studio.
* Wizualizowanie danych w projektancie.
* Prześlij eksperyment AutoML.
* Rozpocznij projekt etykietowania.

Aby włączyć pełną funkcjonalność podczas korzystania z punktu końcowego usługi magazynu, zobacz Korzystanie z programu [Azure Machine Learning Studio w sieci wirtualnej](how-to-enable-studio-virtual-network.md#access-data-using-the-studio). Studio obsługuje punkty końcowe usługi i prywatne punkty końcowe dla kont magazynu.

### <a name="limitations"></a>Ograniczenia
- Studio nie może uzyskać dostępu do danych na kontach magazynu skonfigurowanych do używania prywatnych punktów końcowych. Aby uzyskać pełną funkcjonalność, należy użyć punktów końcowych usługi dla magazynu i użyć tożsamości zarządzanej.

## <a name="configure-firewall-settings"></a>Konfigurowanie ustawień zapory

Skonfiguruj zaporę, aby kontrolować dostęp do zasobów obszaru roboczego Azure Machine Learning i publicznego Internetu. Mimo że zalecamy korzystanie z zapory platformy Azure, do zabezpieczenia sieci należy używać innych produktów zapory. Jeśli masz pytania dotyczące sposobu zezwalania na komunikację za pośrednictwem zapory, zapoznaj się z dokumentacją używanej zapory.

Aby uzyskać więcej informacji na temat ustawień zapory, zobacz [Używanie obszaru roboczego za zaporą](how-to-access-azureml-behind-firewall.md).

## <a name="next-steps"></a>Następne kroki

Ten artykuł jest częścią jednej z czterech części serii sieci wirtualnych. Zapoznaj się z pozostałymi artykułami, aby dowiedzieć się, jak zabezpieczyć sieć wirtualną:

* [Część 2: Omówienie usługi Virtual Network](how-to-secure-workspace-vnet.md)
* [Część 3: Zabezpieczanie środowiska szkoleniowego](how-to-secure-training-vnet.md)
* [Część 4: Zabezpieczanie środowiska inferencing](how-to-secure-inferencing-vnet.md)
* [Część 5. Włączanie funkcji programu Studio](how-to-enable-studio-virtual-network.md)
