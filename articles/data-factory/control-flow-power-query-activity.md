---
title: Działanie Power Query w Azure Data Factory
description: Dowiedz się, jak używać działania Power Query dla funkcji przetwarzanie danych w potoku Data Factory
services: data-factory
author: kromerm
ms.author: makromer
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: 3314053e5b81c597d6d29015a5ebda6e171731d1
ms.sourcegitcommit: 484f510bbb093e9cfca694b56622b5860ca317f7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/21/2021
ms.locfileid: "98634228"
---
# <a name="power-query-activity-in-data-factory"></a>Działanie dodatku PowerShell w usłudze Data Factory

Działanie Power Query umożliwia kompilowanie i wykonywanie Power Query różnyów w celu wykonywania przetwarzanie danych na dużą skalę w Data Factory potoku. Można utworzyć nowe Power Query różny z poziomu opcji menu nowe zasoby lub przez dodanie do potoku działania dotyczącego mocy.

![Zrzut ekranu przedstawiający Power Query w okienku zasoby fabryki.](media/data-flow/power-query-wrangling.png)

Wcześniej przetwarzanie danych w Azure Data Factory został utworzony przy użyciu opcji menu przepływ danych. Ten element został zmieniony na tworzenie z poziomu nowego działania Power Query. Możesz współpracować bezpośrednio z Power Query edytora różny, aby wykonać interaktywną eksplorację danych, a następnie zapisać swoją służbę. Po zakończeniu możesz wykonać działanie Power Query i dodać je do potoku. Azure Data Factory automatycznie przeskaluje ją i operacjonalizować przetwarzanie danych za pomocą środowiska Spark przepływu danych Azure Data Factory.

## <a name="translation-to-data-flow-script"></a>Tłumaczenie na skrypt przepływu danych

Aby osiągnąć skalowanie za pomocą działania Power Query, Azure Data Factory służy do tłumaczenia ```M``` skryptu do skryptu przepływu danych, dzięki czemu można wykonać Power Query na dużą skalę przy użyciu środowiska Spark Azure Data Factory Flow. Twórz przepływ danych przetwarzanie przy użyciu przygotowywania danych bez kodu. Aby uzyskać listę dostępnych funkcji, zobacz [funkcje transformacji](wrangling-functions.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o pojęciach dotyczących przetwarzanie danych przy użyciu [Power Query w Azure Data Factory](wrangling-tutorial.md)
