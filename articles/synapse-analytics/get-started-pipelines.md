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
ms.date: 12/31/2020
ms.openlocfilehash: 22631cfd872ed226fc78a97d38c423fbc300f78e
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107304718"
---
# <a name="integrate-with-pipelines"></a>Integracja z potokami

W tym samouczku dowiesz się, jak zintegrować potoki i działania przy użyciu programu Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Tworzenie potoku i Dodawanie działania notesu



1. W programie Synapse Studio przejdź do centrum **integracji** .
1. Wybierz pozycję **+**  >  **potok** , aby utworzyć nowy potok. Kliknij nowy obiekt potoku, aby otworzyć projektanta potoku.
1. W obszarze **działania** rozwiń folder **Synapse** i przeciągnij obiekt **notesu** do projektanta.
1. Wybierz kartę **Ustawienia** właściwości działania notesu. Użyj listy rozwijanej, aby wybrać dowolny Notes z bieżącego obszaru roboczego Synapse.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Zaplanuj uruchamianie potoku co godzinę

1. W potoku wybierz pozycję **Dodaj wyzwalacz**  >  **Nowy/Edytuj**.
1. W obszarze **Wybierz wyzwalacz** wybierz pozycję **Nowy** i ustaw **cykl** na wartość "co 1 godzinę".
1. Wybierz przycisk **OK**. 
1. Wybierz pozycję **Opublikuj wszystkie**. 


## <a name="monitor-pipeline"></a>Monitorowanie potoku

1. Po opublikowaniu potoku, aby uruchomić potok natychmiast, bez czekania na następną godzinę, wybierz pozycję **Dodaj wyzwalacz wyzwalacza**  >  **teraz**.
1. W programie Synapse Studio przejdź do centrum **monitora** i wybierz pozycję **uruchomienia potoków** , aby monitorować postęp wykonywania potoku.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wizualizacja danych przy użyciu usługi Power BI](get-started-visualize-power-bi.md)
