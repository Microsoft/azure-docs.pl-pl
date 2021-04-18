---
title: Przyniesienie własnego uczenia maszynowego do Azure Sentinel | Microsoft Docs
description: W tym artykule wyjaśniono, jak tworzyć własne algorytmy uczenia maszynowego i używać ich do analizy danych w Azure Sentinel.
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
ms.openlocfilehash: 2164b8ac6e62b8826d5879da07384769c503bfb5
ms.sourcegitcommit: 950e98d5b3e9984b884673e59e0d2c9aaeabb5bb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/18/2021
ms.locfileid: "107598605"
---
# <a name="bring-your-own-machine-learning-ml-into-azure-sentinel"></a>Bring your own Machine Learning (ML) w Azure Sentinel

Machine Learning (ML) jest jedną z głównych podstaw Azure Sentinel i jednym z głównych atrybutów, które ją odróżniają. Azure Sentinel oferuje uczenia maszynowego w kilku doświadczeniach: [](fusion.md) wbudowany aparat korelacji fusion i notesy Jupyter oraz nowo dostępną platformę Build-Your-Own ML (BYO ML). 

Modele wykrywania uczenia maszynowego mogą dostosowywać się do poszczególnych środowisk i zmian w zachowaniu użytkowników, aby ograniczyć liczbę wyników fałszywie dodatnich i zidentyfikować zagrożenia, które nie zostaną znalezione przy użyciu tradycyjnego podejścia. Wiele organizacji ds. zabezpieczeń rozumie wartość uczenia maszynowego w zakresie bezpieczeństwa, chociaż nie wiele z nich ma wytłoki profesjonalistów, którzy mają wiedzę z zakresu zabezpieczeń i uczenia maszynowego. Zaprojektowaliśmy przedstawioną tutaj platformę dla organizacji ds. zabezpieczeń i specjalistów ds. zabezpieczeń, aby rozwijały się wraz z nami w ich drodze do uczenia maszynowego. Organizacje, które nie mają doświadczenia w zakresie uczenia maszynowego lub nie mają niezbędnej wiedzy, mogą uzyskać znaczącą wartość ochrony Azure Sentinel wbudowanych funkcji uczenia maszynowego firmy Microsoft.

:::image type="content" source="./media/bring-your-own-ml/machine-learning-framework.png" alt-text="machine learning framework":::

## <a name="what-is-the-bring-your-own-machine-learning-byo-ml-platform"></a>Co to jest platforma Bring Your Own Machine Learning (BYO-ML)?

Dla organizacji, które mają zasoby uczenia maszynowego i chciały tworzyć dostosowane modele uczenia maszynowego do swoich unikatowych potrzeb biznesowych, oferujemy **platformę BYO-ML.** Platforma korzysta ze środowiska [Azure Databricks](/azure/databricks/scenarios/what-is-azure-databricks) / [Apache Spark](http://spark.apache.org/) i notesów Jupyter w celu tworzenia środowiska uczenia maszynowego. Udostępnia on następujące składniki:

- Pakiet BYO-ML, który zawiera biblioteki, które ułatwiają dostęp do danych i wypychanie wyników z powrotem do usługi Log Analytics (LA), dzięki czemu można zintegrować wyniki z wykrywaniem, badaniem i wyszukiwaniami zagrożeń. 

- Szablony algorytmów uczenia maszynowego, które można dostosować, aby dopasować je do określonych problemów z zabezpieczeniami w organizacji. 

- przykładowe notesy do trenowania modelu i planowania oceny modelu. 

Oprócz tego można korzystać z własnych modeli uczenia maszynowego i/lub własnego środowiska Spark w celu integracji z Azure Sentinel.

Platforma BYO-ML pozwala szybko rozpocząć tworzenie własnych modeli uczenia maszynowego: 

- Notes z przykładami danych pomaga uzyskać praktyczne doświadczenie bez konieczności martwienia się o obsługę danych produkcyjnych.

- Pakiet zintegrowany ze środowiskiem Spark zmniejsza wyzwania i problemy związane z zarządzaniem infrastrukturą.

- Biblioteki obsługują ruchy danych. Notesy do trenowania i oceniania pokazują środowisko end-to-end i służą jako szablon do dostosowywania się do środowiska.

### <a name="use-cases"></a>Przypadki zastosowań
 
Platforma i pakiet BYO-ML znacznie skracają czas i nakład pracy potrzebny do tworzenia własnych funkcji wykrywania uczenia maszynowego, a także wyzwalają możliwość rozwiązania określonych problemów z zabezpieczeniami w Azure Sentinel. Platforma obsługuje następujące przypadki użycia:

**Trenowanie algorytmu uczenia maszynowego w celu uzyskania dostosowanego modelu:** Możesz użyć istniejącego algorytmu uczenia maszynowego (udostępnionego przez firmę Microsoft lub przez społeczność użytkowników) i łatwo wytszkolić go na własnych danych, aby uzyskać dostosowany model uczenia maszynowego, który lepiej pasuje do Twoich danych i środowiska.

**Zmodyfikuj szablon algorytmu uczenia maszynowego, aby uzyskać dostosowany model:** Możesz zmodyfikować szablon algorytmu uczenia maszynowego (udostępniony przez firmę Microsoft lub przez społeczność użytkowników) i wytszkolić zmodyfikowany algorytm na podstawie własnych danych, aby utworzyć dostosowany model dopasowany do konkretnego problemu.

**Utwórz własny model:** Utwórz własny model od podstaw przy użyciu platformy Azure Sentinel BYO-ML firmy.

**Zintegruj środowisko databricks/spark:** Zintegruj istniejące środowisko databricks/Spark Azure Sentinel i użyj bibliotek i szablonów BYO-ML do tworzenia modeli uczenia maszynowego w ich unikatowych sytuacjach.

**Zaimportuj własny model uczenia maszynowego:** Możesz zaimportować własne modele uczenia maszynowego i zintegrować je z platformą BYO-ML oraz narzędziami Azure Sentinel.

**Udostępnianie algorytmu uczenia maszynowego:** Udostępnij algorytm uczenia maszynowego, który będzie używany przez społeczność do przyjęcia i dostosowania.

**Użyj uczenia maszynowego, aby umożliwić secopsom:** użyj własnego niestandardowego modelu uczenia maszynowego i wyników do wyszukiwania zagrożeń, wykrywania, badania i reagowania.

W tym artykule przedstawiono składniki platformy BYO-ML oraz sposób wykorzystania platformy i algorytmu dostępu do zasobów anomalii w celu zapewnienia dostosowanego wykrywania uczenia maszynowego za pomocą Azure Sentinel.

## <a name="azure-databricksspark-environment"></a>Azure Databricks/Spark Environment

[Apache Spark™](http://spark.apache.org/) naprzód w upraszczaniu danych big data, zapewniając ujednoliconą platformę do tworzenia potoków danych. Azure Databricks to jeszcze bardziej, zapewniając platformę w chmurze bez zarządzania zbudowaną na platformie Spark. Zalecamy korzystanie z usługi Databricks na potrzeby platformy BYO-ML, dzięki czemu można skoncentrować się na znalezieniu odpowiedzi, które mają natychmiastowy wpływ na twoją firmę, a nie rozwiązywaniu problemów z potokami danych i platformą.
Jeśli masz już usługę Databricks lub inne środowisko Spark i wolisz korzystać z istniejącej konfiguracji, pakiet BYO-ML również będzie działać w ich przypadku. 

## <a name="byo-ml-package"></a>Pakiet BYO-ML

Pakiet BYO ML zawiera najlepsze rozwiązania i badania firmy Microsoft na frontonie uczenia maszynowego w zakresie zabezpieczeń. Ten pakiet zawiera listę narzędzi, notesów i szablonów algorytmów dla problemów z zabezpieczeniami.

| Nazwa pliku | Opis |
| --------- | ----------- |
| azure_sentinel_utilities.whl | Zawiera narzędzia do odczytywania obiektów blob z platformy Azure i zapisywania ich w usłudze Log Analytics. |
| AnomalieRASampleData | Notes przedstawia użycie modelu dostępu do zasobów anomalii na Azure Sentinel przy użyciu wygenerowanych przykładowych danych treningowych i testowych. |
| AnomalousRATraining.ipynb | Notes do trenowania algorytmu, kompilowania i zapisywania modeli. |
| AnomalousRAScoring.ipynb | Notes do planowania uruchomienia modelu, wizualizacji wyniku i zapisu wyniku z powrotem do Azure Sentinel. |
|

Pierwszym szablonem algorytmu uczenia maszynowego, który oferujemy, jest wykrywanie dostępu [do zasobów anomalii.](https://github.com/Azure/Azure-Sentinel/tree/master/BYOML) Jest on oparty na algorytmie filtrowania współpracy i jest trenowany za pomocą dzienników dostępu udziałów plików systemu Windows (zdarzenia zabezpieczeń o identyfikatorze zdarzenia 5140). Kluczowymi informacjami potrzebnymi dla tego modelu w dzienniku jest parowanie użytkowników i zasobów, do których uzyskano dostęp. 

## <a name="example-walkthrough-anomalous-file-share-access-detection"></a>Przykładowy przewodnik: wykrywanie dostępu do udziału plików anomalii 

Teraz, gdy zapoznasz się z kluczowymi składnikami platformy BYO-ML, oto przykład, aby pokazać, jak używać platformy i składników do dostarczania dostosowanego wykrywania uczenia maszynowego.

### <a name="setup-the-databricksspark-environment"></a>Konfigurowanie środowiska Databricks/Spark

Jeśli jeszcze go nie masz, musisz skonfigurować własne środowisko databricks. Aby uzyskać instrukcje, zapoznaj się z dokumentem Szybki start dotyczącym aplikacji [Databricks.](/azure/databricks/scenarios/quickstart-create-databricks-workspace-portal?tabs=azure-portal)

### <a name="auto-export-instruction"></a>Instrukcja autoeksportu

Aby tworzyć niestandardowe modele uczenia maszynowego na podstawie własnych danych w uciece Azure Sentinel, musisz wyeksportować dane z usługi Log Analytics do magazynu obiektów blob lub zasobu centrum zdarzeń, aby model uczenia maszynowego miał do nich dostęp z usługi Databricks. Dowiedz się, [jak pozysować dane do Azure Sentinel](connect-data-sources.md).

W tym przykładzie musisz mieć dane szkoleniowe dotyczące dziennika dostępu do udziału plików w usłudze Azure Blob Storage. Format danych jest udokumentowany w notesie i bibliotekach.

Dane z usługi Log Analytics można wyeksportować automatycznie przy użyciu interfejsu wiersza polecenia [platformy Azure.](/cli/azure/monitor/log-analytics) 

Aby uruchamiać polecenia, musisz mieć przypisaną rolę Współautor w obszarze roboczym usługi Log Analytics, koncie magazynu i zasobie usługi EventHub.  

Oto przykładowy zestaw poleceń służących do konfigurowania automatycznego eksportowania:

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

### <a name="export-custom-data"></a>Eksportowanie danych niestandardowych

W przypadku danych niestandardowych, które nie są obsługiwane przez automatyczne eksportowanie usługi Log Analytics, możesz użyć aplikacji logiki lub innych rozwiązań do przenoszenia danych. Możesz zapoznać się z blogiem [i skryptem Eksportowanie danych usługi Log Analytics do magazynu](https://techcommunity.microsoft.com/t5/azure-monitor/log-analytics-data-export-preview/ba-p/1783530) obiektów blob.

### <a name="correlate-with-data-outside-of-azure-sentinel"></a>Korelowanie z danymi spoza Azure Sentinel

Możesz również wprowadzić dane spoza Azure Sentinel do magazynu obiektów blob lub centrum zdarzeń i skorelować je z danymi Azure Sentinel w celu kompilowania modeli uczenia maszynowego. 
 
### <a name="copy-and-install-the-related-packages"></a>Kopiowanie i instalowanie powiązanych pakietów

Skopiuj pakiet BYO-ML z repozytorium Azure Sentinel GitHub wymienionego powyżej do środowiska usługi Databricks. Następnie otwórz notesy i postępuj zgodnie z instrukcjami w notesie, aby zainstalować wymagane biblioteki w klastrach.

### <a name="model-training-and-scoring"></a>Trenowanie i ocenianie modelu

Postępuj zgodnie z instrukcjami w dwóch notesach, aby zmienić konfiguracje zgodnie z własnym środowiskiem i zasobami, wykonaj kroki trenowania i tworzenia modelu, a następnie zaplanuj model oceny dzienników dostępu do przychodzących udziałów plików.

### <a name="write-results-to-log-analytics"></a>Zapis wyników do usługi Log Analytics

Po zaplanowaniu oceniania możesz użyć modułu w notesie oceniania, aby zapisać wyniki oceny w obszarze roboczym usługi Log Analytics skojarzonym z Azure Sentinel wystąpieniem.

### <a name="check-results-in-azure-sentinel"></a>Sprawdź wyniki w Azure Sentinel

Aby wyświetlić wyniki z wynikami wraz z powiązanymi szczegółami dziennika, wróć do Azure Sentinel portal. W **>** dziennikach niestandardowych zobaczysz wyniki w tabeli **AnomalousResourceAccessResult_CL** (lub własnej niestandardowej nazwie tabeli). Możesz użyć tych wyników, aby ulepszyć środowisko badania i wyszukiwania.

:::image type="content" source="./media/bring-your-own-ml/anomalous-resource-access-logs.png" alt-text="anomalie dzienniki dostępu do zasobów":::

### <a name="build-custom-analytics-rule-with-ml-results"></a>Tworzenie niestandardowej reguły analizy z wynikami uczenia maszynowego

Po potwierdzeniu, że wyniki uczenia maszynowego znajdują się w tabeli dzienników niestandardowych i dokładność wyników jest zadowala, można utworzyć wykrywanie na podstawie wyników. Przejdź do **strony Analiza** w Azure Sentinel portal i [utwórz nową regułę wykrywania.](tutorial-detect-threats-custom.md) Poniżej znajduje się przykład przedstawiający zapytanie użyte do utworzenia wykrywania.

:::image type="content" source="./media/bring-your-own-ml/create-byo-ml-analytics-rule.png" alt-text="tworzenie niestandardowej reguły analizy dla wykrywania B Y O M L":::

### <a name="view-and-respond-to-incidents"></a>Wyświetlanie zdarzeń i reagowanie na nie
Po skonfigurowaniu reguły analizy na podstawie wyników uczenia maszynowego, jeśli istnieją wyniki powyżej wartości progowej ustawionej w  zapytaniu, zdarzenie zostanie wygenerowane i na stronie Zdarzenia w Azure Sentinel. 

## <a name="next-steps"></a>Następne kroki

W tym dokumencie opisano, jak używać platformy BYO-ML firmy Azure Sentinel do tworzenia lub importowania własnych algorytmów uczenia maszynowego w celu analizowania danych i wykrywania zagrożeń.

- Zobacz wpisy dotyczące uczenia maszynowego i wiele innych istotnych tematów na blogu Azure Sentinel [bloga.](https://aka.ms/azuresentinelblog)
