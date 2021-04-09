---
title: Działanie Power Query w Azure Data Factory
description: Dowiedz się, jak używać działania Power Query dla funkcji przetwarzanie danych w potoku Data Factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: fc4f20db55f8e7e0b2f92cb8309c1c128b235089
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100385459"
---
# <a name="power-query-activity-in-data-factory"></a>Działanie dodatku PowerShell w usłudze Data Factory

Działanie Power Query umożliwia kompilowanie i wykonywanie Power Query różnyów w celu wykonywania przetwarzanie danych na dużą skalę w Data Factory potoku. Można utworzyć nowe Power Query różny z poziomu opcji menu nowe zasoby lub przez dodanie do potoku działania dotyczącego mocy.

![Zrzut ekranu przedstawiający Power Query w okienku zasoby fabryki.](media/data-flow/power-query-wrangling.png)

Wcześniej przetwarzanie danych w Azure Data Factory został utworzony przy użyciu opcji menu przepływ danych. Ten element został zmieniony na tworzenie z poziomu nowego działania Power Query. Możesz współpracować bezpośrednio z Power Query edytora różny, aby wykonać interaktywną eksplorację danych, a następnie zapisać swoją służbę. Po zakończeniu możesz wykonać działanie Power Query i dodać je do potoku. Azure Data Factory automatycznie przeskaluje ją i operacjonalizować przetwarzanie danych za pomocą środowiska Spark przepływu danych Azure Data Factory.

> [!VIDEO https://www.microsoft.com/en-us/videoplayer/embed/RE4MFYn]

## <a name="translation-to-data-flow-script"></a>Tłumaczenie na skrypt przepływu danych

Aby osiągnąć skalowanie za pomocą działania Power Query, Azure Data Factory służy do tłumaczenia ```M``` skryptu do skryptu przepływu danych, dzięki czemu można wykonać Power Query na dużą skalę przy użyciu środowiska Spark Azure Data Factory Flow. Twórz przepływ danych przetwarzanie przy użyciu przygotowywania danych bez kodu. Aby uzyskać listę dostępnych funkcji, zobacz [funkcje transformacji](wrangling-functions.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o pojęciach dotyczących przetwarzanie danych przy użyciu [Power Query w Azure Data Factory](wrangling-tutorial.md)
