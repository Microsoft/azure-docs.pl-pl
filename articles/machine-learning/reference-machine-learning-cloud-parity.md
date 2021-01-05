---
title: Niezgodność między regionami publicznymi i suwerennymi
titleSuffix: Azure Machine Learning
description: W tym artykule przedstawiono dostępność funkcji między chmurą publiczną a Azure Government, Azure (Niemcy) i Azure (Chiny).
services: machine-learning
ms.service: machine-learning
ms.subservice: core
ms.topic: reference
ms.reviewer: larryfr
ms.author: andzha
author: Anurzeuii
ms.date: 12/21/2020
ms.custom: references_regions
ms.openlocfilehash: 88240f9b46997d11f1e7c2d93fa880b004615a11
ms.sourcegitcommit: a4533b9d3d4cd6bb6faf92dd91c2c3e1f98ab86a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/22/2020
ms.locfileid: "97725024"
---
# <a name="azure-machine-learning-sovereign-cloud-parity"></a>Azure Machine Learning z niezależną międzychmurową parzystością

Dowiedz się, jakie funkcje Azure Machine Learning są dostępne w regionach suwerennych w chmurze. 

Na liście globalnych regionów świadczenia usługi Azure istnieje kilka regionów "suwerennych", które obsługują określone rynki. Na przykład Azure Government oraz regionów firmy 21Vianet w Chinach. Obecnie Azure Machine Learning jest wdrażany w następujących regionach suwerennej chmury:

* Azure Government regiony **US-Arizona** i **US-Wirginia**.
* Azure Chińska — Chiny region **Chiny — wschód-2**.

> [!TIP]
> Aby odróżnić suwerenne i niesuwerenne regiony, ten artykuł będzie używać __publicznej chmury__ do odwoływania się do Niesuwerennych regionów.

Chcemy zapewnić maksymalną zgodność między chmurą publiczną i suwerennymi regionami. Wszystkie funkcje Azure Machine Learning będą dostępne w tych regionach w ciągu **30 dni od ga** (ogólna dostępność) w naszej chmurze publicznej. W tych regionach włączamy również wybór liczby funkcji w wersji zapoznawczej. Poniżej przedstawiono bieżące różnice w zakresie parzystości między naszymi suwerennymi i publicznymi chmurami.

## <a name="azure-government"></a>Azure Government 

| Cechy | Stan chmury publicznej  | US-Virginia | US-Arizona| 
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|:-------------:|
| **Zautomatyzowane uczenie maszynowe** | | | |
| Twórz i uruchamiaj eksperymenty w notesach                                    | Ogólna dostępność                   | TAK                | TAK         |
| Tworzenie i uruchamianie eksperymentów w środowisku sieci Web programu Studio                        | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| Wiodące w branży funkcje prognozowania                                  | Ogólna dostępność                   | TAK                | TAK         |
| Pomoc techniczna w zakresie uczenia głębokiego i innych zaawansowanych informacji                      | Ogólna dostępność                   | TAK                | TAK         |
| Obsługa dużych ilości danych (do 100 GB)                                          | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| Integracja Azure Databricks                                              | Ogólna dostępność                   | NO                 | NO          |
| Integracja z usługą SQL, CosmosDB i HDInsight                                   | Ogólna dostępność                   | TAK                | TAK         |
| **Potoki Machine Learning** |   |  | | 
| Tworzenie, uruchamianie i publikowanie potoków przy użyciu zestawu Azure ML SDK                   | Ogólna dostępność                   | TAK                | TAK         |
| Tworzenie punktów końcowych potoku przy użyciu zestawu Azure ML SDK                           | Ogólna dostępność                   | TAK                | TAK         |
| Tworzenie, edytowanie i usuwanie zaplanowanych przebiegów potoków przy użyciu zestawu Azure ML SDK | Ogólna dostępność                   | OPCJĘ               | OPCJĘ        |
| Wyświetl szczegóły uruchomienia potoku w programie Studio                                        | Ogólna dostępność                   | TAK                | TAK         |
| Tworzenie, uruchamianie, wizualizowanie i publikowanie potoków w programie Azure ML Designer          | Ogólna dostępność      | TAK                | TAK         |
| Integracja Azure Databricks z potokiem ML                             | Ogólna dostępność                   | NO                 | NO          |
| Tworzenie punktów końcowych potoku w programie Azure ML Designer                             | Ogólna dostępność      | TAK                | TAK         |
| **Zintegrowane notesy** |   |  | | 
| Notes obszaru roboczego i udostępnianie plików                                        | Ogólna dostępność                   | TAK                | TAK         |
| Obsługa języków R i Python                                                       | Ogólna dostępność                   | TAK                | TAK         |
| Obsługa sieci wirtualnej                                                    | Publiczna wersja zapoznawcza       | NO                 | NO          |
| **Wystąpienie obliczeniowe** |   |  | | 
| Zarządzane wystąpienia obliczeniowe dla zintegrowanych notesów                         | Ogólna dostępność                   | TAK                | TAK         |
| Jupyter, integracja z JupyterLab                                            | Ogólna dostępność                   | TAK                | TAK         |
| Obsługa Virtual Network (VNet)                                             | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| **Obsługa zestawu SDK** |  |  | | 
| Obsługa zestawu SDK języka R                                                              | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| Obsługa zestawu SDK języka Python                                                         | Ogólna dostępność                   | TAK                | TAK         |
| **Bezpieczeństwo** |   | | | 
| Virtual Network (VNet) obsługa szkoleń                                | Ogólna dostępność                   | TAK                | TAK         |
| Obsługa Virtual Network (VNet) na potrzeby wnioskowania                               | Ogólna dostępność                   | TAK                | TAK         |
| Ocenianie uwierzytelniania punktu końcowego                                            | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| Prywatny link do miejsca pracy                                                     | Publiczna wersja zapoznawcza       | NO                 | NO          |
| ACI za sieć wirtualną                                                            | Publiczna wersja zapoznawcza       | NO                 | NO          |
| ACR za sieć wirtualną                                                            | Publiczna wersja zapoznawcza       | NO                 | NO          |
| Prywatny adres IP klastra AKS                                                  | Publiczna wersja zapoznawcza       | NO                 | NO          |
| **Środowisko obliczeniowe** |   | | |
| Zarządzanie przydziałami w różnych obszarach roboczych                                         | Ogólna dostępność                   | TAK                | TAK         |
| **Dane do uczenia maszynowego** |   | | |
| Tworzenie, wyświetlanie i edytowanie zestawów danych i magazynów z zestawu SDK                  | Ogólna dostępność                   | TAK                | TAK         |
| Twórz, Wyświetlaj lub Edytuj zestawy danych i magazyny datastores z poziomu interfejsu użytkownika                   | Ogólna dostępność                   | TAK                | TAK         |
| Wyświetlanie, edytowanie lub usuwanie monitorów oddryfania zestawu danych z zestawu SDK                   | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| Wyświetlanie, edytowanie lub usuwanie monitorów oddryfania zestawu danych z interfejsu użytkownika                    | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| **Cykl życia Machine Learning** |   | | |
| Profilowanie modelu                                                            | Ogólna dostępność                   | TAK                | UWZGLĘDNIENI     |
| Rozszerzenie DevOps platformy Azure dla Machine Learning & Azure ML CLI         | Ogólna dostępność                   | TAK                | TAK         |
| Modele z przyspieszaniem sprzętowym oparte na FPGA                                     | Ogólna dostępność                   | NO                 | NO          |
| Integracja Visual Studio Code                                             | Publiczna wersja zapoznawcza       | NO                 | NO          |
| Integracja z usługą Event Grid                                                     | Publiczna wersja zapoznawcza       | NO                 | NO          |
| Integracja Azure Stream Analytics z usługą Azure Machine Learning               | Publiczna wersja zapoznawcza       | NO                 | NO          |
| **Etykietowania** |   | | |
| Etykietowanie portal zarządzania projektu                                        | Ogólna dostępność                   | TAK                | TAK         |
| Portal Labeler                                                            | Ogólna dostępność                   | TAK                | TAK         |
| Etykietowanie przy użyciu pracowników prywatnych                                          | Ogólna dostępność                   | TAK                | TAK         |
| Etykieta z asystą (Klasyfikacja obrazu i wykrywanie obiektów)           | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| **Odpowiedzialne uczenie maszynowe** |   | | |
| Wyjaśnienie w interfejsie użytkownika                                                       | Publiczna wersja zapoznawcza       | NO                 | NO          |
| Różnicowa SmartNoise — zestaw narzędzi                                    | OSS                  | NO                 | NO          |
| niestandardowe znaczniki w Azure Machine Learning do implementowania arkuszy danych              | Ogólna dostępność                   | NO                 | NO          |
| Integracja z programem Azure                                               | Publiczna wersja zapoznawcza       | NO                 | NO          |
| Zestaw SDK do interpretacji                                                      | Ogólna dostępność                   | TAK                | TAK         |
| **Szkolenia** |   | | |
| Przesyłanie strumieniowe dzienników eksperymentów                                              | Ogólna dostępność                   | TAK                | TAK         |
| Uczenie wzmacniające                                                     | Publiczna wersja zapoznawcza       | NO                 | NO          |
| Interfejs użytkownika eksperymentowania                                                         | Ogólna dostępność                   | TAK                | TAK         |
| .NET Integration ML.NET 1,0                                                | Ogólna dostępność                   | TAK                | TAK         |
| **Wnioskowania** |   | | |
| Inferencing Batch                                                          | Ogólna dostępność                   | TAK                | TAK         |
| Data Box Edge z FPGA                                                    | Publiczna wersja zapoznawcza       | NO                 | NO          |
| **Inne** |   | | |
| Open Datasets                                                              | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| Wyszukiwanie poznawcze niestandardowe                                                    | Publiczna wersja zapoznawcza       | TAK                | TAK         |
| Wiele modeli                                                                | Publiczna wersja zapoznawcza       | NO                 | NO          |


### <a name="azure-government-scenarios"></a>Scenariusze Azure Government

| Scenariusz                                                    | US-Virginia | US-Arizona| Ograniczenia  |
|----------------------------------------------------------------------------|:----------------------:|:--------------------:|-------------|
| **Ogólna Konfiguracja zabezpieczeń** |   | | |
| Komunikacja między usługami w sieci prywatnej                                     | NO | NO | Nie ma obecnie linku prywatnego | 
| Wyłącz/Kontroluj dostęp do Internetu (ruch przychodzący i wychodzący) oraz konkretną sieć wirtualną | UWZGLĘDNIENI| UWZGLĘDNIENI   | ACR za sieć wirtualną nie jest dostępna w Azure Government-podwójne sprawdzanie na ACI | 
| Umieszczanie wszystkich skojarzonych zasobów/usług  | TAK | TAK |  |
| Szyfrowanie w czasie spoczynku i podczas przesyłania.                                                 | TAK | TAK |  |
| Dostęp do zasobów obliczeniowych w katalogu głównym i SSH.                                          | TAK | TAK |  |
| Zapewnienie bezpieczeństwa wdrożonych systemów (wystąpień, punktów końcowych itp.), w tym programu Endpoint Protection, poprawek i rejestrowania |  UWZGLĘDNIENI|  UWZGLĘDNIENI |ACI za sieć wirtualną i prywatny punkt końcowy jest obecnie niedostępny |                                  
| Kontrolka (Wyłącz/Ogranicz/Ogranicz) użycie integracji ACI/AKS                    | UWZGLĘDNIENI| UWZGLĘDNIENI |ACI za sieć wirtualną i prywatny punkt końcowy jest obecnie niedostępny|
| Kontrola dostępu oparta na rolach (Azure RBAC) na platformie Azure — tworzenie ról niestandardowych                           | TAK | TAK |  |
| Kontrolowanie dostępu do obrazów ACR używanych przez usługę ML (udostępnione/obsługiwane przez platformę Azure zamiast niestandardowych)  |UWZGLĘDNIENI|  UWZGLĘDNIENI | ACR za prywatny punkt końcowy i Sieć wirtualna nie jest obsługiwana w Azure Government |
| **Ogólne użycie usługi Machine Learning** |  | | |
| Możliwość tworzenia modelu przez środowisko programistyczne, uczenie tego modelu, hostowania go jako punktu końcowego i korzystania z niego za pośrednictwem webapp     | TAK | TAK |  |
| Możliwość ściągania danych z ADLS (Data Lake Storage)                                 |TAK | TAK |  |
| Możliwość ściągania danych z usługi Azure Blob Storage                                       |TAK | TAK |  |



### <a name="additional-azure-government-limitations"></a>Dodatkowe ograniczenia Azure Government

* W przypadku wystąpień obliczeniowych Azure Machine Learning możliwość odświeżenia tokenu trwającego więcej niż 24 godziny nie jest dostępna w Azure Government.
* Profilowanie modelu nie obsługuje 4 procesorów CPU w regionie US-Arizona.   
* Przykładowe notesy mogą nie zadziałały w Azure Government, jeśli potrzebują dostępu do danych publicznych.
* Adresy IP: polecenie interfejsu wiersza polecenia używane w sieci [wirtualnej i Wymuszone tunelowanie](how-to-secure-training-vnet.md#forced-tunneling) nie zwraca zakresów adresów IP. W zamian użyj [zakresów adresów IP platformy Azure i tagów usługi dla Azure Government](https://www.microsoft.com/download/details.aspx?id=57063) .
* W przypadku zaplanowanych potoków udostępniamy również mechanizm wyzwalania oparty na obiektach Blob. Ten mechanizm nie jest obsługiwany w przypadku obszarów roboczych CMK. Aby włączyć wyzwalacz oparty na obiektach Blob dla obszarów roboczych CMK, należy wykonać dodatkowe czynności konfiguracyjne. Aby uzyskać więcej informacji, zobacz [wyzwalanie przebiegu potoku uczenia maszynowego z poziomu aplikacji logiki](how-to-trigger-published-pipeline.md).
* Zapory: w przypadku używania regionu Azure Government należy dodać następujące dodatkowe hosty do ustawienia zapory:

    * Do użycia w Arizona: `usgovarizona.api.ml.azure.us`
    * Do użycia w Wirginia: `usgovvirginia.api.ml.azure.us`
    * Dla obu: `graph.windows.net` 


## <a name="azure-china-21vianet"></a>Azure w Chinach — 21Vianet 

| Cechy                                       | Stan chmury publicznej | CH-wschód-2 | CH — północ-3 |
|----------------------------------------------------------------------------|:------------------:|:--------------------:|:-------------:|
| **Zautomatyzowane uczenie maszynowe** |    | | |
| Twórz i uruchamiaj eksperymenty w notesach                                    | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Tworzenie i uruchamianie eksperymentów w środowisku sieci Web programu Studio                        | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| Wiodące w branży funkcje prognozowania                                  | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Pomoc techniczna w zakresie uczenia głębokiego i innych zaawansowanych informacji                      | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Obsługa dużych ilości danych (do 100 GB)                                          | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| Integracja Azure Databricks                                              | Ogólna dostępność               | NO        | Nie dotyczy        |
| Integracja z usługą SQL, CosmosDB i HDInsight                                   | Ogólna dostępność               | TAK       | Nie dotyczy        |
| **Potoki Machine Learning** |    | | |
| Tworzenie, uruchamianie i publikowanie potoków przy użyciu zestawu Azure ML SDK                   | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Tworzenie punktów końcowych potoku przy użyciu zestawu Azure ML SDK                           | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Tworzenie, edytowanie i usuwanie zaplanowanych przebiegów potoków przy użyciu zestawu Azure ML SDK | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Wyświetl szczegóły uruchomienia potoku w programie Studio                                        | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Tworzenie, uruchamianie, wizualizowanie i publikowanie potoków w programie Azure ML Designer          | Ogólna dostępność  | TAK       | Nie dotyczy        |
| Integracja Azure Databricks z potokiem ML                             | Ogólna dostępność               | NO        | Nie dotyczy        |
| Tworzenie punktów końcowych potoku w programie Azure ML Designer                             | Ogólna dostępność   | TAK       | Nie dotyczy        |
| **Zintegrowane notesy** |   | | |
| Notes obszaru roboczego i udostępnianie plików                                        | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Obsługa języków R i Python                                                       | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Obsługa sieci wirtualnej                                                    | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| **Wystąpienie obliczeniowe** |    | | |
| Zarządzane wystąpienia obliczeniowe dla zintegrowanych notesów                         | Ogólna dostępność               | NO        | Nie dotyczy        |
| Jupyter, integracja z JupyterLab                                            | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Obsługa Virtual Network (VNet)                                             | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| **Obsługa zestawu SDK** |    | | |
| Obsługa zestawu SDK języka R                                                              | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| Obsługa zestawu SDK języka Python                                                         | Ogólna dostępność               | TAK       | Nie dotyczy        |
| **Bezpieczeństwo** |   | | |
| Virtual Network (VNet) obsługa szkoleń                                | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Obsługa Virtual Network (VNet) na potrzeby wnioskowania                               | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Ocenianie uwierzytelniania punktu końcowego                                            | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| Prywatny link do miejsca pracy                                                     | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| ACI za sieć wirtualną                                                            | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| ACR za sieć wirtualną                                                            | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| Prywatny adres IP klastra AKS                                                  | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| **Środowisko obliczeniowe** |   | | |
| Zarządzanie przydziałami w różnych obszarach roboczych                                         | Ogólna dostępność               | TAK       | Nie dotyczy        |
| **Dane do uczenia maszynowego** | | | |
| Tworzenie, wyświetlanie i edytowanie zestawów danych i magazynów z zestawu SDK                  | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Twórz, Wyświetlaj lub Edytuj zestawy danych i magazyny datastores z poziomu interfejsu użytkownika                   | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Wyświetlanie, edytowanie lub usuwanie monitorów oddryfania zestawu danych z zestawu SDK                   | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| Wyświetlanie, edytowanie lub usuwanie monitorów oddryfania zestawu danych z interfejsu użytkownika                    | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| **Cykl życia Machine Learning** |    | | |
| Profilowanie modelu                                                            | Ogólna dostępność               | UWZGLĘDNIENI   | Nie dotyczy        |
| Rozszerzenie DevOps platformy Azure dla Machine Learning & Azure ML CLI         | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Modele z przyspieszaniem sprzętowym oparte na FPGA                                     | Ogólna dostępność               | NO        | Nie dotyczy        |
| Integracja Visual Studio Code                                             | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| Integracja z usługą Event Grid                                                     | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| Integracja Azure Stream Analytics z usługą Azure Machine Learning               | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| **Etykietowania** |    | | |
| Etykietowanie portal zarządzania projektu                                        | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Portal Labeler                                                            | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Etykietowanie przy użyciu pracowników prywatnych                                          | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Etykieta z asystą (Klasyfikacja obrazu i wykrywanie obiektów)           | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| **Odpowiedzialne uczenie maszynowe** |    | | |
| Wyjaśnienie w interfejsie użytkownika                                                       | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| Różnicowa SmartNoise — zestaw narzędzi                                    | OSS              | NO        | Nie dotyczy        |
| niestandardowe znaczniki w Azure Machine Learning do implementowania arkuszy danych              | Ogólna dostępność               | NO        | Nie dotyczy        |
| Integracja z programem Azure                                               | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| Zestaw SDK do interpretacji                                                      | Ogólna dostępność               | TAK       | Nie dotyczy        |
| **Szkolenia** |    | | |
| Przesyłanie strumieniowe dzienników eksperymentów                                              | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Uczenie wzmacniające                                                     | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| Interfejs użytkownika eksperymentowania                                                         | Ogólna dostępność               | TAK       | Nie dotyczy        |
| .NET Integration ML.NET 1,0                                                | Ogólna dostępność               | TAK       | Nie dotyczy        |
| **Wnioskowania** |   | | |
| Inferencing Batch                                                          | Ogólna dostępność               | TAK       | Nie dotyczy        |
| Data Box Edge z FPGA                                                    | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |
| **Inne** |    | | |
| Open Datasets                                                              | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| Wyszukiwanie poznawcze niestandardowe                                                    | Publiczna wersja zapoznawcza   | TAK       | Nie dotyczy        |
| Wiele modeli                                                                | Publiczna wersja zapoznawcza   | NO        | Nie dotyczy        |



### <a name="additional-azure-china-limitations"></a>Dodatkowe ograniczenia dotyczące platformy Azure (Chiny)

* Chiny platformy Azure mają ograniczoną jednostkę SKU maszyn wirtualnych, szczególnie w przypadku jednostki SKU procesora GPU. Ma tylko rodzinę seria NCV3 (V100).
* Punkty końcowe interfejsu API REST są inne niż globalne platformy Azure. Skorzystaj z poniższej tabeli, aby znaleźć punkt końcowy interfejsu API REST dla regionów platformy Azure w Chinach:

    | Punkt końcowy REST                 | Globalny platformę Azure                                 | China-Government                           |
    |------------------|--------------------------------------------|--------------------------------------------|
    | Płaszczyzna zarządzania | `https://management.azure.com/`              | `https://management.chinacloudapi.cn/`       |
    | Płaszczyzna danych       | `https://{location}.experiments.azureml.net` | `https://{location}.experiments.ml.azure.cn` |
    | Usługa Azure Active Directory              | `https://login.microsoftonline.com`          | `https://login.chinacloudapi.cn`             |

* Przykładowy Notes może nie zadziałał, jeśli potrzebny jest dostęp do danych publicznych.
* Zakresy adresów IP: polecenie interfejsu wiersza polecenia używane w instrukcjach [wymuszonego tunelowania sieci wirtualnej](how-to-secure-training-vnet.md#forced-tunneling) nie zwraca zakresów adresów IP. Zamiast tego należy użyć [zakresów adresów IP i usług platformy Azure dla Chin](https://www.microsoft.com//download/details.aspx?id=57062) .
* Azure Machine Learning w wersji zapoznawczej wystąpienia obliczeniowe nie są obsługiwane w obszarze roboczym, w którym jest teraz włączone łącze prywatne, ale element CI będzie obsługiwany w następnym wdrożeniu dla rozszerzenia usługi do wszystkich regionów AML.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat regionów, w których usługa Azure Machine Learning jest dostępna w systemie, zobacz temat [produkty według regionów](https://azure.microsoft.com/global-infrastructure/services/).
