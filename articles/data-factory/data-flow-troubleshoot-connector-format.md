---
title: Rozwiązywanie problemów z łącznikiem i formatem w przepływach mapowania danych
description: Dowiedz się, jak rozwiązywać problemy z przepływem danych związane z łącznikiem i formatem w Azure Data Factory.
author: linda33wj
ms.author: jingwang
ms.service: data-factory
ms.topic: troubleshooting
ms.date: 04/12/2021
ms.openlocfilehash: 7596bf3182f59758eeee66e248404e98c18da2e7
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107715543"
---
# <a name="troubleshoot-connector-and-format-issues-in-mapping-data-flows-in-azure-data-factory"></a>Rozwiązywanie problemów z łącznikiem i formatem w przepływach mapowania danych w Azure Data Factory


W tym artykule omykamy metody rozwiązywania problemów związane z łącznikiem i formatem przepływów danych mapowania w Azure Data Factory (ADF).


## <a name="cosmos-db--json"></a>Cosmos DB & JSON

### <a name="support-customized-schemas-in-the-source"></a>Obsługa niestandardowych schematów w źródle

#### <a name="symptoms"></a>Objawy
Jeśli chcesz użyć przepływu danych ADF do przenoszenia lub przesyłania danych z plików Cosmos DB/JSON do innych magazynów danych, niektóre kolumny danych źródłowych mogą zostać pominięte. 

#### <a name="cause"></a>Przyczyna 
W przypadku łączników bez schematu (numer kolumny, nazwa kolumny i typ danych kolumny w każdym wierszu mogą się różnić w porównaniu z innymi), domyślnie w celu wywłaszania schematu usługi ADF są używane przykładowe wiersze (na przykład 100 lub 1000 danych z 1000 wierszy), a wywnioskowany wynik będzie używany jako schemat do odczytywania danych. Jeśli więc magazyny danych mają dodatkowe kolumny, które nie są wyświetlane w przykładowych wierszach, dane tych dodatkowych kolumn nie są odczytywane, przenoszone ani przesyłane do magazynów danych ujścia.

#### <a name="recommendation"></a>Zalecenie
Aby zastąpić domyślne zachowanie i wprowadzić dodatkowe pola, usługi ADF udostępnia opcje dostosowywania schematu źródłowego. Możesz określić dodatkowe/brakujące kolumny, których może brakować w projekcji źródła przepływu danych w wyniku schematu, aby odczytać dane, i możesz zastosować jedną z następujących opcji, aby ustawić dostosowany schemat. Zazwyczaj **preferowaną opcją jest opcja 1.**

- **Opcja 1:** W porównaniu z oryginalnymi danymi źródłowymi, które mogą być jednym dużym plikiem, tabelą lub kontenerem zawierającym miliony wierszy ze złożonymi schematami, można utworzyć tymczasową tabelę/kontener z kilkoma wierszami zawierającymi wszystkie kolumny, które chcesz odczytać, a następnie przejść do następującej operacji: 

    1. Użyj ustawień debugowania źródła **przepływu danych,** aby uzyskać **projekcję importu** z przykładowymi plikami/tabelami, aby uzyskać pełny schemat. Możesz wykonać kroki opisane na poniższej ilustracji:<br/>

        ![Zrzut ekranu przedstawiający opcję 1-1 zalecenia](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-1.png)<br/>
         1. Wybierz **pozycję Ustawienia debugowania** na kanwie przepływu danych.
         1. W okienku podręcznym wybierz pozycję **Przykładowa tabela** na **karcie cosmosSource.** 
         1. Wprowadź nazwę tabeli w **bloku** Tabela.
         1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.
         1. Wybierz **pozycję Import projection (Importuj projekcję).**<br/>  
    
    1. Zmień ustawienia **debugowania z** powrotem, aby użyć źródłowego zestawu danych do pozostałego ruchu/przekształcenia danych. Możesz przejść dalej, aby wykonać kroki opisane na poniższej ilustracji:<br/>

        ![Zrzut ekranu przedstawiający opcję 1-2 zalecenia](./media/data-flow-troubleshoot-connector-format/customize-schema-option-1-2.png) <br/>   
         1. Wybierz **pozycję Ustawienia debugowania** na kanwie przepływu danych.
         1. W okienku podręcznym wybierz pozycję Źródłowy **zestaw danych** na **karcie cosmosSource.**
         1. Wybierz **pozycję Zapisz,** aby zapisać ustawienia.<br/>
    
    Następnie środowisko uruchomieniowe przepływu danych usługi ADF będzie honorować i używać dostosowanego schematu do odczytywania danych z oryginalnego magazynu danych. <br/>

- **Opcja 2:** Jeśli znasz schemat i język DSL danych źródłowych, możesz ręcznie zaktualizować skrypt źródła przepływu danych, aby dodać dodatkowe/pominięte kolumny w celu odczytania danych. Przykład przedstawiono na poniższej ilustracji: 

    ![Zrzut ekranu przedstawiający opcję 2 zalecenia](./media/data-flow-troubleshoot-connector-format/customize-schema-option-2.png)

## <a name="next-steps"></a>Następne kroki
Aby uzyskać więcej pomocy dotyczącej rozwiązywania problemów, zobacz następujące zasoby:

*  [Rozwiązywanie problemów z przepływami danych mapowania w Azure Data Factory](data-flow-troubleshoot-guide.md)
*  [Data Factory bloga](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Data Factory dotyczące funkcji](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Stack Overflow forum for Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Twitter information about Data Factory](https://twitter.com/hashtag/DataFactory)