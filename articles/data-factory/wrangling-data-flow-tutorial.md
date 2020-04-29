---
title: Wprowadzenie do przepływu danych przetwarzanie w Azure Data Factory
description: Samouczek dotyczący przygotowywania danych w Azure Data Factory przy użyciu przepływu danych przetwarzanie
author: djpmsft
ms.author: daperlov
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: a180a7a0c85b642ac09d1d027c95809c4638dee1
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81409006"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Przygotowywanie danych za pomocą przepływu danych przetwarzanie

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

## <a name="create-a-wrangling-data-flow"></a>Tworzenie przepływu danych przetwarzanie

Istnieją dwa sposoby tworzenia przepływu danych przetwarzanie w Azure Data Factory. Jednym ze sposobów jest kliknięcie ikony znaku plus i wybranie opcji **przepływ danych** w okienku zasoby fabryki.

![Przetwarzanie](media/wrangling-data-flow/tutorial7.png)

Druga metoda znajduje się w okienku działania kanwy potoku. Otwórz przystawkę **Przenieś i Przekształć** i przeciągnij aktywność **przepływu danych** na kanwę.

W obu metodach, w okienku bocznym, które się otworzy, wybierz pozycję **Utwórz nowy przepływ danych** i wybierz pozycję **przepływ danych przetwarzanie**. Kliknij przycisk OK.

![Przetwarzanie](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Tworzenie przepływu danych przetwarzanie

Dodaj **źródłowy zestaw danych** dla przepływu danych przetwarzanie. Możesz wybrać istniejący zestaw danych lub utworzyć nowy. Możesz również wybrać zestaw danych ujścia. Można wybrać jeden lub więcej źródłowych zestawów danych, ale w tej chwili dozwolony jest tylko jeden obiekt sink. Wybór zestawu danych ujścia jest opcjonalny, ale wymagany jest co najmniej jeden źródłowy zestaw danych.

> [!NOTE]
> Tylko tekst rozdzielony ADLS generacji 2 jest obsługiwany w przypadku ograniczonej wersji zapoznawczej. 

![Przetwarzanie](media/wrangling-data-flow/tutorial4.png)

Kliknij przycisk **Utwórz** , aby otworzyć Edytor zestawu połączonych Online w Power Query.

![Przetwarzanie](media/wrangling-data-flow/tutorial5.png)

Twórz przepływ danych przetwarzanie przy użyciu przygotowywania danych bez kodu. Aby uzyskać listę dostępnych funkcji, zobacz [funkcje transformacji](wrangling-data-flow-functions.md)/

![Przetwarzanie](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Uruchamianie i monitorowanie przepływu danych przetwarzanie

Aby wykonać przebieg debugowania potoku w przepływie danych przetwarzanie, kliknij pozycję **Debuguj** na kanwie potoku. Po opublikowaniu przepływu danych **Wyzwól teraz** wykonywane jest uruchomienie na żądanie ostatniego opublikowanego potoku. Przepływy danych przetwarzanie można zaplanować przy użyciu wszystkich istniejących wyzwalaczy Azure Data Factory.

![Przetwarzanie](media/wrangling-data-flow/tutorial3.png)

Przejdź do karty **monitorowanie** , aby wizualizować dane wyjściowe uruchomionego uruchomienia działania przepływu danych przetwarzanie.

![Przetwarzanie](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć przepływ danych mapowania](tutorial-data-flow.md).