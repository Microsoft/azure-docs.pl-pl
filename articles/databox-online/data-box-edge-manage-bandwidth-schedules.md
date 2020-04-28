---
title: Azure Data Box Edge zarządzanie harmonogramami przepustowości | Microsoft Docs
description: Opisuje sposób używania Azure Portal do zarządzania harmonogramami przepustowości na Azure Data Box Edge.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: article
ms.date: 03/22/2019
ms.author: alkohli
ms.openlocfilehash: f7b762d5502986c306de240519688aa639f58445
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "60756889"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-data-box-edge"></a>Użyj Azure Portal, aby zarządzać harmonogramami przepustowości na Azure Data Box Edge  

W tym artykule opisano sposób zarządzania użytkownikami na Azure Data Box Edge. Harmonogramy przepustowości pozwalają konfigurować użycie przepustowości sieci w wielu harmonogramach dotyczących pory dnia. Te harmonogramy można zastosować do operacji przekazywania i pobierania wykonywanych z urządzenia do chmury.

Harmonogramy przepustowości dla Data Box Edge można dodawać, modyfikować lub usuwać za pośrednictwem Azure Portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie harmonogramu
> * Modyfikowanie harmonogramu
> * Usuwanie harmonogramu


## <a name="add-a-schedule"></a>Dodawanie harmonogramu

Wykonaj następujące kroki w Azure Portal, aby dodać harmonogram.

1. W Azure Portal dla zasobu Data Box Edge przejdź do obszaru **przepustowość**.
2. W okienku po prawej stronie wybierz pozycję **+ Dodaj harmonogram**.

    ![Wybierz przepustowość](media/data-box-edge-manage-bandwidth-schedules/add-schedule-1.png)

3. W obszarze **Dodaj harmonogram**: 

   1. Podaj **Dzień rozpoczęcia**, **Dzień zakończenia**, **Godzinę rozpoczęcia** i **Godzinę zakończenia** uruchomienia harmonogramu.
   2. Zaznacz opcję **wszystkie dni** , jeśli ten harmonogram ma być uruchamiany cały dzień.
   3. **Współczynnik przepustowości** określa w megabitach na sekundę (Mb/s) przepustowość wykorzystywaną przez urządzenie w operacjach dotyczących chmury — zarówno przekazywania, jak i pobierania danych. Podaj liczbę z zakresu od 20 do 1 000 000 007 dla tego pola.
   4. Zaznacz pole **Nieograniczona** przepustowość, jeśli nie chcesz ustalać czasu przekazywania i pobierania.
   5. Wybierz pozycję **Dodaj**.

      ![Dodaj harmonogram](media/data-box-edge-manage-bandwidth-schedules/add-schedule-2.png)

3. Harmonogram zostanie utworzony przy użyciu wybranych parametrów. Następnie harmonogram zostanie wyświetlony w portalu na liście harmonogramów przepustowości.

    ![Zaktualizowana lista harmonogramów przepustowości](media/data-box-edge-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Edytowanie harmonogramu

Wykonaj poniższe czynności, aby edytować harmonogram przepustowości.

1. W Azure Portal przejdź do zasobu Data Box Edge, a następnie przejdź do obszaru **przepustowość**. 
2. Z listy harmonogramów przepustowości wybierz i wybierz harmonogram, który chcesz zmodyfikować.
    ![Wybieranie harmonogramu przepustowości](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-1.png)

3. Wprowadź i zapisz odpowiednie zmiany.

    ![Modyfikowanie harmonogramu](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-2.png)

4. Lista harmonogramów zostanie zaktualizowana w celu uwzględnienia zmodyfikowanego harmonogramu.

    ![Modyfikowanie harmonogramu](media/data-box-edge-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

Wykonaj poniższe czynności, aby usunąć harmonogram przepustowości skojarzony z urządzeniem Data Box Edge.

1. W Azure Portal przejdź do zasobu Data Box Edge, a następnie przejdź do obszaru **przepustowość**.  

2. Na liście harmonogramów przepustowości wybierz harmonogram, który chcesz usunąć. W obszarze **Edytuj harmonogram**wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

   ![Usuwanie użytkownika](media/data-box-edge-manage-bandwidth-schedules/delete-schedule-2.png)

3. Po usunięciu harmonogramu lista harmonogramów zostanie zaktualizowana.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać udziałami](data-box-edge-manage-shares.md).
