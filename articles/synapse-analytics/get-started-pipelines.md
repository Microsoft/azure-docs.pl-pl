---
title: 'Samouczek: wprowadzenie do integracji z potokami'
description: W tym samouczku dowiesz się, jak zintegrować potoki i działania przy użyciu programu Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/27/2020
ms.openlocfilehash: af01d5b5e424dd5ea229115f7aa3570d0b7cd511
ms.sourcegitcommit: 8c7f47cc301ca07e7901d95b5fb81f08e6577550
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/27/2020
ms.locfileid: "92744919"
---
# <a name="integrate-with-pipelines"></a>Integracja z potokami

W tym samouczku dowiesz się, jak zintegrować potoki i działania przy użyciu programu Synapse Studio. 

## <a name="overview"></a>Omówienie

W usłudze Azure Synapse można zintegrować wiele różnych zadań.

1. W programie Synapse Studio przejdź do centrum **integracji** .
1. Wybierz pozycję **+**  >  **potok** , aby utworzyć nowy potok.
1. Przejdź do centrum **opracowywania** i wybierz jeden z utworzonych wcześniej notesów.
1. Przeciągnij ten Notes do potoku ( **Uwaga** : Dodaj moduł importowania modułów w notesie zgodnie z opisem w [dokumencie](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace), który jest wymagany podczas uruchamiania z potoku)
1. W potoku wybierz pozycję **Dodaj wyzwalacz**  >  **Nowy/Edytuj** .
1. W obszarze **Wybierz wyzwalacz** wybierz pozycję **Nowy** i ustaw **cykl** na wartość "co 1 godzinę".
1. Wybierz przycisk **OK** . 
1. Wybierz pozycję **Opublikuj wszystkie** .
1. Aby potok był uruchamiany natychmiast, bez czekania na następną godzinę, wybierz pozycję **Dodaj wyzwalacz wyzwalacza**  >  **teraz** .



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wizualizacja danych przy użyciu usługi Power BI](get-started-visualize-power-bi.md)
                                 
