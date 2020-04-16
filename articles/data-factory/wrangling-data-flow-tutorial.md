---
title: Wprowadzenie do przepływu danych wysunięcie danych w usłudze Azure Data Factory
description: Samouczek dotyczący sposobu przygotowania danych w usłudze Azure Data Factory przy użyciu przepływu danych wranglingu
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81409006"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Przygotowywanie danych za pomocą przepływu danych wranglingu

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Tworzenie przepływu danych wrangling

Istnieją dwa sposoby tworzenia przepływu danych wysuwalenia w usłudze Azure Data Factory. Jednym ze sposobów jest kliknięcie ikony plus i **wybranie przepływu danych** w okienku zasobów fabrycznych.

![Wrangling](media/wrangling-data-flow/tutorial7.png)

Druga metoda znajduje się w okienku działań kanwy potoku. Otwórz akordeon **Przenieś i Przekształć** i przeciągnij działanie **Przepływ danych** na kanwę.

W obu metodach w otwierane okienko boczne wybierz pozycję **Utwórz nowy przepływ danych** i wybierz pozycję **Przepływ danych Wrangling**. Kliknij przycisk OK.

![Wrangling](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Tworzenie przepływu danych wrangling

Dodaj **źródłowy zestaw danych** dla przepływu danych. Można wybrać istniejący zestaw danych lub utworzyć nowy. Można również wybrać zestaw danych ujścia. Można wybrać jeden lub więcej źródłowych zestawów danych, ale tylko jeden obiekt sink jest dozwolone w tej chwili. Wybranie zestawu danych ujścia jest opcjonalne, ale wymagany jest co najmniej jeden źródłowy zestaw danych.

> [!NOTE]
> Tylko tekst rozdzielany ADLS Gen 2 jest obsługiwany w przypadku ograniczonego podglądu. 

![Wrangling](media/wrangling-data-flow/tutorial4.png)

Kliknij **przycisk Utwórz,** aby otworzyć edytor mashup usługi Power Query Online.

![Wrangling](media/wrangling-data-flow/tutorial5.png)

Autor przepływu danych wrangling przy użyciu przygotowania danych bez kodu. Aby uzyskać listę dostępnych funkcji, zobacz [funkcje transformacji](wrangling-data-flow-functions.md)/

![Wrangling](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Uruchamianie i monitorowanie przepływu danych wranglingu

Aby wykonać przebieg debugowania potoku przepływu danych wrangling, kliknij przycisk **Debugowanie** w kanwie potoku. Po opublikowaniu przepływu danych Trigger wykonuje **teraz** przebieg na żądanie ostatniego opublikowanego potoku. Wrangling przepływów danych można zaplanować ze wszystkimi istniejącymi wyzwalaczami usługi Azure Data Factory.

![Wrangling](media/wrangling-data-flow/tutorial3.png)

Przejdź do **monitoru** karty, aby wizualizować dane wyjściowe wyzwalanego przebiegu działania przepływu danych.

![Wrangling](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć przepływ danych mapowania](tutorial-data-flow.md).