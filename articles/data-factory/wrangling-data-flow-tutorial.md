---
title: Wprowadzenie do przepływu danych przetwarzanie w Azure Data Factory
description: Samouczek dotyczący przygotowywania danych w Azure Data Factory przy użyciu przepływu danych przetwarzanie
author: dcstwh
ms.author: weetok
ms.reviewer: gamal
ms.service: data-factory
ms.topic: conceptual
ms.date: 11/01/2019
ms.openlocfilehash: 5ee1069ca24dc16a52d30450c0f2163130f549cb
ms.sourcegitcommit: d60976768dec91724d94430fb6fc9498fdc1db37
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/02/2020
ms.locfileid: "96494906"
---
# <a name="prepare-data-with-wrangling-data-flow"></a>Przygotowywanie danych za pomocą przepływu danych przetwarzanie

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

> [!NOTE]
> Przepływ danych przetwarzanie jest obecnie dostępnych w publicznej wersji zapoznawczej

## <a name="create-a-wrangling-data-flow"></a>Tworzenie przepływu danych przetwarzanie

Istnieją dwa sposoby tworzenia przepływu danych przetwarzanie w Azure Data Factory. Jednym ze sposobów jest kliknięcie ikony znaku plus i wybranie opcji **przepływ danych** w okienku zasoby fabryki.

![Zrzut ekranu przedstawiający przepływ danych w okienku zasoby fabryki.](media/wrangling-data-flow/tutorial7.png)

Druga metoda znajduje się w okienku działania kanwy potoku. Otwórz przystawkę **Przenieś i Przekształć** i przeciągnij aktywność **przepływu danych** na kanwę.

W obu metodach, w okienku bocznym, które się otworzy, wybierz pozycję **Utwórz nowy przepływ danych** i wybierz pozycję **przepływ danych przetwarzanie**. Kliknij przycisk OK.

![Zrzut ekranu, który podświetla opcję przepływu danych przetwarzanie.](media/wrangling-data-flow/tutorial1.png)

## <a name="author-a-wrangling-data-flow"></a>Tworzenie przepływu danych przetwarzanie

Dodaj **źródłowy zestaw danych** dla przepływu danych przetwarzanie. Możesz wybrać istniejący zestaw danych lub utworzyć nowy. Możesz również wybrać zestaw danych ujścia. Można wybrać jeden lub więcej źródłowych zestawów danych, ale w tej chwili dozwolony jest tylko jeden obiekt sink. Wybór zestawu danych ujścia jest opcjonalny, ale wymagany jest co najmniej jeden źródłowy zestaw danych.

> [!NOTE]
> Tylko tekst rozdzielony ADLS generacji 2 jest obsługiwany w przypadku ograniczonej wersji zapoznawczej. 

![Przetwarzanie](media/wrangling-data-flow/tutorial4.png)

Kliknij przycisk **Utwórz** , aby otworzyć Edytor zestawu połączonych Online w Power Query.

![Zrzut ekranu pokazujący przycisk Utwórz, który otwiera Edytor zestawu połączonych online Power Query.](media/wrangling-data-flow/tutorial5.png)

Twórz przepływ danych przetwarzanie przy użyciu przygotowywania danych bez kodu. Aby uzyskać listę dostępnych funkcji, zobacz [funkcje transformacji](wrangling-data-flow-functions.md).

![Zrzut ekranu pokazujący proces tworzenia przepływu danych przetwarzanie.](media/wrangling-data-flow/tutorial6.png)

## <a name="running-and-monitoring-a-wrangling-data-flow"></a>Uruchamianie i monitorowanie przepływu danych przetwarzanie

Aby wykonać przebieg debugowania potoku w przepływie danych przetwarzanie, kliknij pozycję **Debuguj** na kanwie potoku. Po opublikowaniu przepływu danych **Wyzwól teraz** wykonywane jest uruchomienie na żądanie ostatniego opublikowanego potoku. Przepływy danych przetwarzanie można zaplanować przy użyciu wszystkich istniejących wyzwalaczy Azure Data Factory.

![Zrzut ekranu pokazujący sposób dodawania przepływu danych przetwarzanie.](media/wrangling-data-flow/tutorial3.png)

Przejdź do karty **monitorowanie** , aby wizualizować dane wyjściowe uruchomionego uruchomienia działania przepływu danych przetwarzanie.

![Zrzut ekranu pokazujący dane wyjściowe uruchomionego uruchomienia działania przepływu danych przetwarzanie.](media/wrangling-data-flow/tutorial2.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak [utworzyć przepływ danych mapowania](tutorial-data-flow.md).