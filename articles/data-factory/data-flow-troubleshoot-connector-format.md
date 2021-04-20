---
title: Rozwiązywanie problemów z łącznikiem i formatem w przepływach mapowania danych
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych związane z łącznikiem i formatem w Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/20/2021
ms.openlocfilehash: 1991436e2cc890b5c6a339f79e42536ced2fbd36
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739480"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Rozwiązywanie problemów z łącznikiem i formatem w przepływach mapowania danych w Azure Data Factory


W tym artykule o mowa o metodach rozwiązywania problemów związanych z łącznikiem i formacie przepływów danych mapowania w Azure Data Factory (ADF).


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Obsługa dostosowanych schematów w źródle

#### <a name="symptoms"></a>Objawy
Jeśli chcesz użyć przepływu danych ADF do przenoszenia danych z plików Cosmos DB/JSON do innych magazynów danych, niektóre kolumny danych źródłowych mogą zostać pominięte. 

#### <a name="cause"></a>Przyczyna 
W przypadku łączników bez schematu (numer kolumny, nazwa kolumny i typ danych kolumny w każdym wierszu mogą się różnić w porównaniu z innymi), domyślnie w celu wywłasz tylko przykładowych wierszy (na przykład 100 lub 1000 wierszy) wywnioskowany wynik będzie używany jako schemat do odczytywania danych. Jeśli więc magazyny danych mają dodatkowe kolumny, które nie występują w przykładowych wierszach, dane tych dodatkowych kolumn nie są odczytywane, przenoszone ani przesyłane do magazynów danych ujścia.

#### <a name="recommendation"></a>Zalecenie
Aby zastąpić zachowanie domyślne i wprowadzić dodatkowe pola, usługi ADF udostępnia opcje dostosowywania schematu źródłowego. Możesz określić dodatkowe/brakujące kolumny, których może brakować w wyniku schematu w projekcji źródła przepływu danych, aby odczytać dane, i możesz zastosować jedną z następujących opcji, aby ustawić dostosowany schemat. Zazwyczaj **preferowaną opcją jest opcja 1.**

- **Opcja 1:** W porównaniu z oryginalnymi danymi źródłowymi, które mogą być jednym dużym plikiem, tabelą lub kontenerem zawierającym miliony wierszy ze złożonymi schematami, można utworzyć tymczasową tabelę/kontener z kilkoma wierszami zawierającymi wszystkie kolumny, które chcesz odczytać, a następnie przejść do następującej operacji: 

    1. Użyj ustawień debugowania źródła **przepływu danych,** aby uzyskać **projekcję importu** z przykładowymi plikami/tabelami, aby uzyskać pełny schemat. Możesz wykonać kroki opisane na poniższej ilustracji:<br/>

        ![Zrzut ekranu przedstawiający pierwszą część pierwszej opcji dostosowywania schematu źródłowego.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Wybierz **pozycję Ustawienia debugowania** na kanwie przepływu danych.
         1. W okienku podręcznym wybierz pozycję Przykładowa **tabela** na karcie **cosmosSource** i wprowadź nazwę tabeli w bloku **Tabela.**
         1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.
         1. Wybierz **pozycję Importuj projekcję**.<br/>  
    
    1. Zmień ustawienia **debugowania z powrotem,** aby używać źródłowego zestawu danych do pozostałego przemieszczenia/przekształcenia danych. Możesz przejść do kolejnych kroków na poniższej ilustracji:<br/>

        ![Zrzut ekranu przedstawiający drugą część pierwszej opcji dostosowywania schematu źródłowego.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Wybierz **pozycję Ustawienia debugowania** na kanwie przepływu danych.
         1. W okienku podręcznym wybierz pozycję **Źródłowy zestaw danych** na karcie **cosmosSource.**
         1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.<br/>
    
    Następnie środowisko uruchomieniowe przepływu danych usługi ADF będzie honorować i używać dostosowanego schematu do odczytywania danych z oryginalnego magazynu danych. <br/>

- **Opcja 2:** Jeśli znasz schemat i język DSL danych źródłowych, możesz ręcznie zaktualizować skrypt źródła przepływu danych, aby dodać dodatkowe/pominięte kolumny w celu odczytania danych. Przykład pokazano na poniższej ilustracji: 

    ![Zrzut ekranu przedstawiający drugą opcję dostosowywania schematu źródłowego.](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej pomocy dotyczącej rozwiązywania problemów, zobacz następujące zasoby:

*  [Rozwiązywanie problemów z przepływami danych mapowania w Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Data Factory bloga](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory dotyczące funkcji](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o Data Factory](https://twitter.com/hashtag/DataFactory)