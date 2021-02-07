---
title: Przenieś swój własny ML do usługi Azure wskaźnikowej | Microsoft Docs
description: W tym artykule wyjaśniono, jak tworzyć własne algorytmy uczenia maszynowego i korzystać z nich na potrzeby analizy danych na platformie Azure.
services: sentinel
cloud: na
documentationcenter: na
author: yelevin
manager: rkarlin
ms.assetid: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 09/23/2020
ms.author: yelevin
ms.openlocfilehash: f8d795dcf64230140e1dd07e3f30ca3aa6825ab4
ms.sourcegitcommit: 8245325f9170371e08bbc66da7a6c292bbbd94cc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/07/2021
ms.locfileid: "99807007"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Przenieś własne Machine Learning (ML) na platformę Azure — wskaźnik

Machine Learning (ML) to jeden z głównych podnoszenia wartości wskaźnikowej platformy Azure, a jeden z głównych atrybutów, które je ustawi. Usługa Azure Learning oferuje ML w kilku środowiskach: wbudowaną w aparat korelacji [Fusion](fusion.md) i notesy Jupyter oraz nowo dostępną platformę do tworzenia ml (BYOD ml). 

Modele wykrywania ML można dostosowywać do poszczególnych środowisk i zmieniać zachowanie użytkowników, aby ograniczyć fałszywe pozytywne i identyfikować zagrożenia, które nie były dostępne w tradycyjnych podejściach. Wiele organizacji zabezpieczeń rozumie wartość ML w celu zapewnienia bezpieczeństwa, chociaż nie wiele z nich ma możliwość zaprojektowania profesjonalistów, którzy wiedzą zarówno w zakresie bezpieczeństwa, jak i w ML. Platforma została zaprojektowana w tym miejscu dla organizacji i specjalistów ds. zabezpieczeń w swojej podróży. Organizacje nowe do ML lub bez wiedzy z niezbędną wiedzą mogą uzyskać znaczącą wartość ochrony z wbudowanych możliwości platformy Azure wskaźnikowej.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="Platforma uczenia maszynowego":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Co to jest platforma przenoszenia własnych Machine Learning (BYOD-ML)?

W przypadku organizacji, które mają zasoby usługi ML i chcesz utworzyć dostosowane modele ML do ich unikatowych potrzeb firmy, oferujemy **platformę BYOD-ml**. Platforma wykorzystuje [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / środowiska[Apache Spark](http://spark.apache.org/) i notesów Jupyter do tworzenia środowiska ml. Dostępne są następujące składniki:

- Pakiet BYOD-ML, który obejmuje biblioteki ułatwiające dostęp do danych i wypychanie wyników z powrotem do Log Analytics (LA), dzięki czemu można zintegrować wyniki z wykrywaniem, badaniem i łowiectwem. 

- Szablony algorytmów ML umożliwiające dostosowanie się do określonych problemów z zabezpieczeniami w organizacji. 

- Przykładowe notesy umożliwiające uczenie modelu i planowanie oceniania modeli. 

Oprócz tego można przenieść własne modele ML i/lub własne środowisko Spark, aby zintegrować z platformą Azure — wskaźnikiem.

Dzięki platformie BYOD-ML możesz uzyskać szybkie rozpoczęcie tworzenia własnych modeli ML: 

- Notes z przykładowymi danymi ułatwia uzyskanie praktycznego doświadczenia, bez obaw o obsługę danych produkcyjnych.

- Pakiet zintegrowany ze środowiskiem Spark zmniejsza wyzwania i problemy związane z zarządzaniem infrastrukturą.

- Biblioteki obsługują ruchy danych. Notesy szkoleniowe i oceniające pokazują kompleksowe środowisko i stanowią szablon, który można dostosować do środowiska.

### <a name="use-cases"></a>Przypadki zastosowań
 
Platforma BYOD i pakiet w znacznym stopniu skracają czas i nakłady pracy potrzebne do stworzenia własnych wykryć i umożliwiają rozwiązywanie określonych problemów z zabezpieczeniami na platformie Azure. Platforma obsługuje następujące przypadki użycia:

**Przeszkol algorytm ml, aby uzyskać dostosowany model:** Możesz utworzyć istniejący algorytm ML (udostępniony przez firmę Microsoft lub przez społeczność użytkowników) i łatwo wyszkolić go na własnych danych, aby uzyskać dostosowany model ML, który lepiej pasuje do danych i środowiska.

**Zmodyfikuj szablon algorytmu ml, aby uzyskać dostosowany model:** Możesz zmodyfikować szablon algorytmu ML (udostępniony przez firmę Microsoft lub przez społeczność użytkowników) i szkolić zmodyfikowany algorytm na własnych danych, aby utworzyć dostosowany model, który będzie pasował do określonego problemu.

**Utwórz własny model:** Utwórz własny model od podstaw, korzystając z platformy BYOD i narzędzi platformy Azure.

**Zintegruj swoje kostki danych/środowisko Spark:** Zintegruj istniejące kostki danych/środowisko Spark z platformą Azure, a następnie użyj bibliotek i szablonów BYOD-ML do tworzenia modeli ML do ich unikatowych sytuacji.

**Zaimportuj własny model ml:** Możesz zaimportować własne modele ML i użyć platformy BYOD i narzędzi, aby zintegrować je ze wskaźnikiem na platformie Azure.

**Udostępnianie algorytmu ml:** Udostępnij algorytm ML dla społeczności, aby przyjąć i dostosować.

**Użyj ml do upoważnienia SecOP:** Użyj własnego NIESTANDARDOWEGO modelu ml i wyników dla polowania, wykryć, badania i odpowiedzi.

W tym artykule przedstawiono składniki platformy BYOD-ML oraz sposób korzystania z platformy i algorytmu nietypowego dostępu do zasobów w celu zapewnienia dostosowanego wykrywania ML przy użyciu platformy Azure.

## <a name="azure-databricksspark-environment"></a>Środowisko Azure Databricks/Spark

[Apache Spark™](http://spark.apache.org/) przejściu w przód w uproszczeniu danych Big Data, zapewniając ujednoliconą platformę do tworzenia potoków danych. Azure Databricks zajmie to więcej, dostarczając platformę w chmurze o zerowym zarządzaniu, która jest oparta na platformie Spark. Zalecamy używanie kostek danych dla platformy BYOD-ML, dzięki czemu możesz skupić się na znajdowaniu odpowiedzi, które natychmiast wpływają na Twoją firmę, zamiast wykorzystywać potoki i problemy z platformą.
Jeśli masz już kostki lub inne środowisko Spark i wolisz korzystać z istniejącej instalacji, pakiet BYOD-ML będzie również działał prawidłowo. 

## <a name="byo-ml-package"></a>Pakiet BYOD-ML

Pakiet BYOD ML zawiera najlepsze rozwiązania i badania firmy Microsoft na frontonie ML pod kątem bezpieczeństwa. W tym pakiecie udostępniamy następującą listę narzędzi, notesów i szablonów algorytmów związanych z zabezpieczeniami.

| Nazwa pliku | Opis |
| --------- | ----------- |
| azure_sentinel_utilities. WHL | Zawiera narzędzia do odczytywania obiektów blob z platformy Azure i zapisywania do Log Analytics. |
| AnomalousRASampleData | W notesie przedstawiono użycie modelu nietypowego dostępu do zasobów w wskaźniku platformy Azure z wygenerowanym szkoleniem i testowaniem przykładowych danych. |
| AnomalousRATraining. ipynb | Notes do uczenia algorytmu, tworzenia i zapisywania modeli. |
| AnomalousRAScoring. ipynb | Notes, aby zaplanować uruchomienie modelu, wizualizować wynik i pisać wynik z powrotem do platformy Azure. |
|

Pierwszy zaoferowany przez nas szablon algorytmu ML jest przeznaczony do [wykrywania anomalii dostępu do zasobów](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML). Jest on oparty na algorytmie filtrowania do współpracy i jest szkolony z dziennikami dostępu do udziału plików systemu Windows (zdarzenia zabezpieczeń z IDENTYFIKATORem zdarzenia 5140). Kluczowe informacje potrzebne dla tego modelu w dzienniku to parowanie użytkowników i zasobów, do których uzyskuje się dostęp. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Przykładowe wskazówki: wykrywanie nietypowego dostępu do udziału plików 

Teraz, gdy znasz już najważniejsze składniki platformy BYOD-ML, Oto przykład przedstawiający sposób użycia platformy i składników do zapewnienia dostosowanego wykrywania ML.

### <a name="setup-the-databricksspark-environment"></a>Konfigurowanie środowiska datakostki/platformy Spark

Jeśli jeszcze tego nie zrobiono, należy skonfigurować własne środowisko danych. Aby uzyskać instrukcje, zapoznaj się z dokumentem [szybkiego startu](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal) .

### <a name="auto-export-instruction"></a>Instrukcja Autoexport

Aby utworzyć niestandardowe modele ML na podstawie własnych danych na platformie Azure, musisz wyeksportować dane z Log Analytics do magazynu obiektów blob lub zasobu centrum zdarzeń, aby model ML mógł uzyskać do niego dostęp z poziomu kostek. Dowiedz się, jak pozyskiwanie [danych do usługi Azure wskaźnikowej](connect-data-sources.md).

Na potrzeby tego przykładu należy mieć dane szkoleniowe dotyczące dziennika dostępu do udziału plików w usłudze Azure Blob Storage. Format danych jest udokumentowany w notesie i w bibliotekach.

Możesz automatycznie wyeksportować dane z Log Analytics przy użyciu [interfejsu wiersza polecenia platformy Azure (CLI)](/cli/azure/monitor/log-analytics). 

Aby uruchamiać polecenia, musisz mieć przypisaną rolę **współautor** w obszarze roboczym log Analytics, Twoim koncie magazynu i zasobem EventHub. 

Oto przykładowy zestaw poleceń do skonfigurowania automatycznego eksportowania:

```azurecli

az –version

# Login with Azure CLI
 az login

# List all Log Analytics clusters
 az monitor log-analytics cluster list

# Set to specific subscription
 az account set --subscription "SUBSCRIPTION_NAME"
 
# Export to Storage - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIStr --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent
 
# Export to EventHub - all tables
 az monitor log-analytics workspace data-export create --resource-group "RG_NAME" --workspace-name "WS_NAME" -n LAExportCLIEH --destination “DESTINATION_NAME" --enable "true" --tables SecurityEvent Heartbeat"]

# List export settings
az monitor log-analytics workspace data-export list --resource-group "RG_NAME" --workspace-name "WS_NAME"

# Delete export setting
 az monitor log-analytics workspace data-export delete --resource-group "RG_NAME" --workspace-name "WS_NAME" --name "NAME"
```

### <a name="export-custom-data"></a>Eksportuj dane niestandardowe

W przypadku danych niestandardowych, które nie są obsługiwane przez Log Analytics autoeksport, można użyć aplikacji logiki lub innych rozwiązań do przenoszenia danych. Możesz odwołać się do blogu " [eksportowanie log Analytics danych do magazynu obiektów BLOB](https://www.borninthecloud.com/exporting-log-analytics-data-to-blob-store/?preview=true) i skryptu.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Skorelowanie z danymi poza wskaźnikiem kontrolnym platformy Azure

Dane spoza centrów danych platformy Azure można również przenieść do magazynu obiektów blob lub centrum zdarzeń, a następnie skorelować z danymi wskaźnikowymi platformy Azure w celu utworzenia modeli sieci. 
 
### <a name="copy-and-install-the-related-packages"></a>Skopiuj i zainstaluj powiązane pakiety

Skopiuj pakiet BYOD-ML z repozytorium GitHub usługi Azure Learning, którego wspomniano powyżej, do środowiska danych. Następnie otwórz Notesy i postępuj zgodnie z instrukcjami w notesie, aby zainstalować wymagane biblioteki w klastrach.

### <a name="model-training-and-scoring"></a>Model szkoleń i oceniania

Postępuj zgodnie z instrukcjami w dwóch notesach, aby zmienić konfiguracje zgodnie z własnym środowiskiem i zasobami, wykonaj kroki, aby przeprowadzić uczenie i kompilowanie modelu, a następnie Zaplanuj model do oceny przychodzących dzienników dostępu do udziału plików.

### <a name="write-results-to-log-analytics"></a>Wyniki zapisu do Log Analytics

Po zaplanowaniu planowanej oceny możesz użyć modułu w notesie oceniania, aby napisać wyniki oceny do obszaru roboczego Log Analytics skojarzonego z wystąpieniem usługi Azure.

### <a name="check-results-in-azure-sentinel"></a>Sprawdź wyniki na platformie Azure — wskaźnik

Aby zobaczyć wyniki oceny wraz ze szczegółowymi informacjami dotyczącymi dziennika, Wróć do portalu Azure wskaźnikowego. W **dziennikach** > dziennikach niestandardowych wyniki zostaną wyświetlone w tabeli **AnomalousResourceAccessResult_CL** (lub własnej nazwie tabeli niestandardowej). Możesz użyć tych wyników, aby wzmocnić badanie i doświadczenia z łowiectwa.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="nietypowe dzienniki dostępu do zasobów":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Utwórz niestandardową regułę analizy z wynikami ML

Po potwierdzeniu, że wyniki z tablicy są w niestandardowym spisie i są zadowalające, można utworzyć wykrywanie na podstawie wyników. Przejdź do pozycji **Analiza** z portalu Azure wskaźnikowego i [Utwórz nową regułę wykrywania](tutorial-detect-threats-custom.md). Poniżej przedstawiono przykład wyświetlania zapytania używanego do tworzenia wykrywania.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="Utwórz niestandardową regułę analizy dla wykryć B Y O M L":::

### <a name="view-and-respond-to-incidents"></a>Wyświetlanie zdarzeń i reagowanie na nie
Po skonfigurowaniu reguły analizy na podstawie wyników z tablicy, jeśli wyniki przekraczają wartość progową ustawioną w zapytaniu, zdarzenie zostanie wygenerowane i naliczane na stronie **incydenty** na platformie Azure. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie przedstawiono sposób korzystania z platformy BYOD-ML na platformie Azure na potrzeby tworzenia i importowania własnych algorytmów uczenia maszynowego w celu analizowania danych i wykrywania zagrożeń.

- Zobacz wpisy dotyczące uczenia maszynowego i wielu innych tematów dostępnych w [blogu dotyczącym platformy Azure](https://aka.ms/azuresentinelblog).