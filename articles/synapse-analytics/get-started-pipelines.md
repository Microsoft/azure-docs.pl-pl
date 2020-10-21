---
title: 'Samouczek: Rozpoczynanie organizowania przy użyciu potoków'
description: W tym samouczku dowiesz się, jak organizować potoki i działania przy użyciu programu Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 10/16/2020
ms.openlocfilehash: 42d2ac6cf6592f8e22b0a66aee84c3436d466572
ms.sourcegitcommit: 03713bf705301e7f567010714beb236e7c8cee6f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/21/2020
ms.locfileid: "92329887"
---
# <a name="orchestrate-with-pipelines"></a>Organizuj przy użyciu potoków

W tym samouczku dowiesz się, jak organizować potoki i działania przy użyciu programu Synapse Studio. 

## <a name="overview"></a>Omówienie

W usłudze Azure Synapse można organizować wiele różnych zadań.

1. W programie Synapse Studio przejdź do centrum **integracji** .
1. Wybierz pozycję **+**  >  **potok** , aby utworzyć nowy potok.
1. Przejdź do centrum **opracowywania** i wybierz jeden z utworzonych wcześniej notesów.
1. Przeciągnij ten Notes do potoku (**Uwaga**: Dodaj moduł importowania modułów w notesie, jak określono w [dokumencie](https://docs.microsoft.com/azure/synapse-analytics/spark/synapse-spark-sql-pool-import-export#transfer-data-to-or-from-a-sql-pool-attached-with-the-workspace) , który jest wymagany podczas uruchamiania z potoku)
1. W potoku wybierz pozycję **Dodaj wyzwalacz**  >  **Nowy/Edytuj**.
1. W obszarze **Wybierz wyzwalacz**wybierz pozycję **Nowy**i ustaw **cykl** na wartość "co 1 godzinę".
1. Wybierz przycisk **OK**. 
1. Wybierz pozycję **Opublikuj wszystkie**.
1. Aby potok był uruchamiany natychmiast, bez czekania na następną godzinę, wybierz pozycję **Dodaj wyzwalacz wyzwalacza**  >  **teraz**.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wizualizacja danych przy użyciu usługi Power BI](get-started-visualize-power-bi.md)
                                 
