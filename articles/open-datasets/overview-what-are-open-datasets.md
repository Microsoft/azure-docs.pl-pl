---
title: Co to są otwarte zestawy danych? Nadzorowane publiczne zestawy danych
titleSuffix: Azure Open Datasets
description: Informacje o otwartych zestawach danych platformy Azure, nadzorowanych zestawach danych z domeny publicznej, takich jak pogoda, spis, święta i lokalizacja do wzbogacania rozwiązań predykcyjnych.
ms.service: open-datasets
ms.topic: overview
author: cjgronlund
ms.author: cgronlun
ms.date: 05/06/2020
ms.openlocfilehash: 24f5a671076a44db382ac37e2f6532e5f0f2a3ae
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "82930390"
---
# <a name="what-are-azure-open-datasets-and-how-can-you-use-them"></a>Co to są otwarte zestawy danych platformy Azure i jak można z nich korzystać?

[Otwarte zestawy danych platformy Azure](https://azure.microsoft.com/services/open-datasets/) mają nadzorowane zestawy danych, których można użyć do dodawania funkcji specyficznych dla scenariusza do rozwiązań uczenia maszynowego w celu uzyskania dokładniejszych modeli. Otwarte zestawy danych znajdują się w chmurze w Microsoft Azure i są zintegrowane z Azure Machine Learning i są łatwo dostępne do Azure Databricks i Machine Learning Studio (klasyczne). Możesz również uzyskać dostęp do zestawów danych za pomocą interfejsów API i używać ich w innych produktach, takich jak Power BI i Azure Data Factory.

Zestawy danych obejmują dane z domeny publicznej na potrzeby pogodowych, spisu, świąt, bezpieczeństwa publicznego i lokalizacji, które ułatwiają uczenie modeli uczenia maszynowego i wzbogacanie rozwiązań predykcyjnych. Możesz również udostępnić publiczne zestawy danych na platformie Azure Otwórz zestaw danych. 

![Składniki usługi Azure Open DataSets](./media/overview-what-are-open-datasets/open-datasets-components.png)

## <a name="curated-prepared-datasets"></a>Nadzorowane, przygotowane zestawy danych
Nadzorowane otwieranie publicznych zestawów danych w systemie Azure otwartych zestawów danych jest zoptymalizowane pod kątem użycia w przepływach pracy uczenia maszynowego. 

Aby wyświetlić wszystkie dostępne zestawy danych, przejdź do [katalogu otwarte zestawy danych platformy Azure](https://azure.microsoft.com/services/open-datasets/catalog/).

Naukowcy danych często spędzają większością czasu na oczyszczenie i przygotowanie danych do zaawansowanej analizy. Otwarte zestawy danych są kopiowane do chmury platformy Azure i wstępnie przetworzone w celu zaoszczędzenia czasu. W regularnych odstępach czasu dane są pobierane ze źródeł, takich jak połączenie FTP z krajowym administracją oceaniczną i ciśnieniem atmosferycznym (NOAA). Następnie dane są analizowane w formacie strukturalnym, a następnie wzbogacone odpowiednio o funkcje, takie jak kod pocztowy lub lokalizacja najbliższej stacji pogodowej.

Zestawy danych są współhostowane z obliczeniami w chmurze na platformie Azure, co ułatwia dostęp i manipulowanie.  

Poniżej przedstawiono przykłady dostępnych zestawów danych. 

### <a name="weather-data"></a>Dane pogodowe
 
|Zestaw danych         | Notebooks     | Opis                                    |
|----------------|---------------|------------------------------------------------|
|[NOAA zintegrowane dane powierzchniowe (ISD)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-integrated-surface-data/?tab=data-access#AzureDatabricks) | Ogólnoświatowe i godzinowe dane pogody z NOAA z optymalnym pokryciem przestrzennym w Ameryka Północna, Europie, Australii i części Azji. Zaktualizowano codziennie. |
|[Globalny system prognozowania NOAA (GFS)](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/noaa-global-forecast-system/?tab=data-access#AzureDatabricks) | 15-dniowe dane prognozy pogody o godzinie NOAA. Zaktualizowano codziennie. |

### <a name="calendar-data"></a>Dane kalendarza

|Zestaw danych         | Notebooks     | Opis                                    |
|----------------|---------------|------------------------------------------------|
|[Dni wolne od pracy](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/) | [Azure Notebooks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureNotebooks) <br> [Azure Databricks](https://azure.microsoft.com/services/open-datasets/catalog/public-holidays/?tab=data-access#AzureDatabricks) | Ogólnoświatowe publiczne dane urlopowe obejmujące 41 krajów lub regionów od 1970 do 2099. Obejmuje kraj oraz to, czy większość osób ma płatne czasowo. |

## <a name="access-to-datasets"></a>Dostęp do zestawów danych  
Za pomocą konta platformy Azure możesz uzyskać dostęp do otwartych zestawów danych za pomocą kodu lub za pośrednictwem interfejsu usługi platformy Azure. Dane są współzlokalizowane z zasobami obliczeniowymi w chmurze platformy Azure do użycia w rozwiązaniu do uczenia maszynowego.  

Otwarte zestawy danych są dostępne za pomocą interfejsu użytkownika Azure Machine Learning i zestawu SDK. Otwarte zestawy danych udostępniają również Azure Notebooks i Azure Databricks notesy, których można użyć do łączenia danych z Azure Machine Learning i Azure Databricks. Do zestawów danych można także uzyskać dostęp za pomocą zestawu SDK języka Python. 

Nie jest jednak potrzebne konto platformy Azure, aby uzyskać dostęp do otwartych zestawów danych; Możesz uzyskać do nich dostęp z dowolnego środowiska języka Python z platformą Spark lub bez niej.

## <a name="request-or-contribute-datasets"></a>Żądanie lub zestawy danych współtworzenia

Jeśli nie możesz znaleźć żądanych danych, Wyślij do nas wiadomość e-mail z [prośbą o zestaw](mailto:aod@microsoft.com?Subject=Request%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A) danych lub [Współtworzenie zestawu danych](mailto:aod@microsoft.com?Subject=Contribute%20dataset%3A%20%3Creplace%20with%20dataset%20name%3E&Body=%0AYour%20name%20and%20institution%3A%20%0A%0ADataset%20name%3A%0A%20%0ADataset%20description%3A%20%0A%3Cfill%20in%20a%20brief%20description%20and%20share%20any%20web%20links%20of%20the%20dataset%3E%20%0A%0ADataset%20size%3A%20%0A%3Chow%20much%20space%20does%20the%20dataset%20need%20today%20and%20how%20much%20is%20it%20expected%20to%20grow%20each%20year%3E%20%0A%0ADataset%20file%20formats%3A%20%0A%3Ccurrent%20dataset%20file%20formats%2C%20and%20optionally%2C%20any%20formats%20that%20the%20dataset%20must%20be%20transformed%20to%20for%20easy%20access%3E%0A%0ALicense%3A%20%0A%3Cwhat%20is%20the%20license%20or%20terms%20and%20conditions%20governing%20the%20distribution%20of%20this%20dataset%3E%0A%0AUse%20cases%3A%20%0A%3CExplain%20some%20common%20use%20of%20the%20dataset.%20E.g.%20weather%20dataset%20can%20be%20useful%20in%20demand%20forecasting%20and%20predictive%20maintenance%20scenarios%3E%20%0A%0AAny%20additional%20information%20you%20want%20us%20to%20know%3A%0A). 

## <a name="next-steps"></a>Następne kroki
* [Przykładowy Notes](samples.md)
* [Samouczek: Modelowanie regresji z danymi o taksówkach](/azure/machine-learning/tutorial-auto-train-models?context=azure/open-datasets/context/open-datasets-context)
* [Zestaw SDK języka Python dla otwartych zestawów danych](/python/api/azureml-opendatasets/?view=azure-ml-py)
