---
title: Wprowadzenie do przepływu danych przetwarzanie w Azure Data Factory
description: Samouczek dotyczący przygotowywania danych w Azure Data Factory przy użyciu przepływu danych przetwarzanie
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/19/2021
ms.openlocfilehash: 3d43a532f57feab361c6a3de79269991f46fc55d
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98684026"
---
# <a name="prepare-data-with-data-wrangling"></a>Przygotowywanie danych za pomocą przetwarzanie danych

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

Przetwarzanie danych w usłudze Fabryka danych pozwala na natywne Kompilowanie interaktywnych Power Query różnyów w ramach podajnika APD, a następnie wykonywanie ich na dużą skalę w ramach potoku ADF.

> [!NOTE]
> Power Query działaniach w usłudze ADF jest obecnie dostępnych w publicznej wersji zapoznawczej

## <a name="create-a-power-query-activity"></a>Tworzenie działania Power Query

Istnieją dwa sposoby tworzenia Power Query w Azure Data Factory. Jednym ze sposobów jest kliknięcie ikony znaku plus i wybranie opcji **przepływ danych** w okienku zasoby fabryki.

> [!NOTE]
> Wcześniej funkcja przetwarzanie danych została umieszczona w przepływie pracy przepływu danych. Teraz będziesz kompilować dane przetwarzanie różny ```New > Power query```

![Zrzut ekranu przedstawiający Power Query w okienku zasoby fabryki.](media/data-flow/power-query-wrangling.png)

Druga metoda znajduje się w okienku działania kanwy potoku. Otwórz **Power Query** stosownie i przeciągnij aktywność **Power Query** na kanwę.

![Zrzut ekranu, który podświetla opcję przetwarzanie danych.](media/data-flow/power-query-activity.png)

## <a name="author-a-power-query-data-wrangling-activity"></a>Tworzenie działania przetwarzanie danych Power Query

Dodaj **źródłowy zestaw danych** dla Power Query różny. Możesz wybrać istniejący zestaw danych lub utworzyć nowy. Możesz również wybrać zestaw danych ujścia. Można wybrać jeden lub więcej źródłowych zestawów danych, ale w tej chwili dozwolony jest tylko jeden obiekt sink. Wybór zestawu danych ujścia jest opcjonalny, ale wymagany jest co najmniej jeden źródłowy zestaw danych.

![Przetwarzanie](media/wrangling-data-flow/tutorial4.png)

Kliknij przycisk **Utwórz** , aby otworzyć Edytor zestawu połączonych Online w Power Query.

![Zrzut ekranu pokazujący przycisk Utwórz, który otwiera Edytor zestawu połączonych online Power Query.](media/wrangling-data-flow/tutorial5.png)

Utwórz przetwarzanie Power Query przy użyciu przygotowywania danych bez użycia kodu. Aby uzyskać listę dostępnych funkcji, zobacz [funkcje transformacji](wrangling-functions.md). Moduł ADF tłumaczy skrypt M na skrypt przepływu danych, dzięki czemu można wykonywać Power Query na dużą skalę przy użyciu środowiska Spark Azure Data Factory Flow.

![Zrzut ekranu pokazujący proces tworzenia Power Query danych przetwarzanie.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-power-query-data-wrangling-activity"></a>Uruchamianie i monitorowanie działania przetwarzanie danych Power Query

Aby wykonać przebieg debugowania potoku działania Power Query, kliknij pozycję **Debuguj** na kanwie potoku. Po opublikowaniu potoku **Wyzwól teraz** wykonywane jest uruchomienie na żądanie ostatniego opublikowanego potoku. Potoki Power Query mogą być zaplanowane ze wszystkimi istniejącymi wyzwalaczami Azure Data Factory.

![Zrzut ekranu pokazujący sposób dodawania Power Query danych działanie przetwarzanie.](media/wrangling-data-flow/tutorial3.png)

Przejdź do karty **monitorowanie** , aby wizualizować dane wyjściowe uruchomionego uruchomienia działania Power Query.

![Zrzut ekranu pokazujący dane wyjściowe uruchomionego uruchomienia działania przetwarzanie Power Query.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć przepływ danych mapowania](tutorial-data-flow.md).
