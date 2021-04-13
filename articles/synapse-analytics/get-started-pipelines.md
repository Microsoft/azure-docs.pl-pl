---
title: 'Samouczek: wprowadzenie do integracji z potokami'
description: Z tego samouczka dowiesz się, jak integrować potoki i działania przy użyciu Synapse Studio.
services: synapse-analytics
author: saveenr
ms.author: saveenr
manager: julieMSFT
ms.reviewer: jrasnick
ms.service: synapse-analytics
ms.subservice: pipeline
ms.topic: tutorial
ms.date: 12/31/2020
ms.openlocfilehash: 05c33db130bfa3fcc1a4f5d75935294fcc0ba1d7
ms.sourcegitcommit: dddd1596fa368f68861856849fbbbb9ea55cb4c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107365471"
---
# <a name="integrate-with-pipelines"></a>Integracja z potokami

Z tego samouczka dowiesz się, jak integrować potoki i działania przy użyciu Synapse Studio. 

## <a name="create-a-pipeline-and-add-a-notebook-activity"></a>Tworzenie potoku i dodawanie działania notesu



1. W Synapse Studio przejdź do centrum **Integracja.**
1. Wybierz **+**  >  **pozycję Potok,** aby utworzyć nowy potok. Kliknij nowy obiekt potoku, aby otworzyć projektanta potoku.
1. W **obszarze** Działania rozwiń folder **Synapse** i przeciągnij obiekt **Notes** do projektanta.
1. Wybierz **kartę Ustawienia** we właściwościach działania Notes. Użyj listy rozwijanej, aby wybrać dowolny notes z bieżącego obszaru roboczego synapse.

## <a name="schedule-the-pipeline-to-run-every-hour"></a>Planowanie uruchamiania potoku co godzinę

1. W potoku wybierz pozycję **Dodaj wyzwalacz**  >  **Nowy/edytuj**.
1. W **elektorze** Wybierz wyzwalacz wybierz pozycję **Nowy** i ustaw **opcję Cykl** na wartość "co 1 godzinę".
1. Wybierz przycisk **OK**. 
1. Wybierz pozycję **Opublikuj wszystkie**. 


## <a name="monitor-pipeline-execution"></a>Monitorowanie wykonywania potoku

1. Po opublikowaniu potoku, aby uruchomić potok natychmiast, bez oczekiwania na następną godzinę, wybierz pozycję **Dodaj wyzwalacz**  >  **teraz.**
1. W Synapse Studio przejdź do **centrum** Monitorowanie.
1. Wybierz **pozycję Uruchomienia potoków,** aby monitorować postęp wykonywania potoku.



## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Wizualizacja danych przy użyciu usługi Power BI](get-started-visualize-power-bi.md)
