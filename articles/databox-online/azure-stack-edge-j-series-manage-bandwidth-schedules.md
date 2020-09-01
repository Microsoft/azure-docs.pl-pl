---
title: Azure Stack Edge GPU zarządzanie harmonogramami przepustowości | Microsoft Docs
description: Opisuje, jak używać Azure Portal do zarządzania harmonogramami przepustowości na Azure Stack procesora GPU.
services: databox
author: alkohli
ms.service: databox
ms.subservice: edge
ms.topic: how-to
ms.date: 08/28/2020
ms.author: alkohli
ms.openlocfilehash: 92b9edb4f694a5ff7d15b06be41cc20db3b64b97
ms.sourcegitcommit: bcda98171d6e81795e723e525f81e6235f044e52
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/01/2020
ms.locfileid: "89265698"
---
# <a name="use-the-azure-portal-to-manage-bandwidth-schedules-on-your-azure-stack-edge-gpu"></a>Użyj Azure Portal do zarządzania harmonogramami przepustowości na Azure Stack brzegowej procesora GPU 

<!--[!INCLUDE [applies-to-skus](../../includes/azure-stack-edge-applies-to-all-sku.md)]-->

W tym artykule opisano sposób zarządzania harmonogramami przepustowości na Azure Stackej krawędzi. Harmonogramy przepustowości pozwalają konfigurować użycie przepustowości sieci w wielu harmonogramach dotyczących pory dnia. Te harmonogramy można zastosować do operacji przekazywania i pobierania wykonywanych z urządzenia do chmury.

Możesz dodawać, modyfikować i usuwać harmonogramy przepustowości dla Azure Stackej krawędzi za pośrednictwem Azure Portal.

W tym artykule omówiono sposób wykonywania następujących zadań:

> [!div class="checklist"]
> * Dodawanie harmonogramu
> * Modyfikowanie harmonogramu
> * Usuwanie harmonogramu


## <a name="add-a-schedule"></a>Dodawanie harmonogramu

Wykonaj następujące kroki w Azure Portal, aby dodać harmonogram.

1. W Azure Portal dla zasobu Azure Stack Edge przejdź do obszaru **przepustowość**.
2. W okienku po prawej stronie wybierz pozycję **+ Dodaj harmonogram**.

    ![Wybierz przepustowość](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-1.png)

3. W obszarze **Dodaj harmonogram**: 

   1. Podaj **Dzień rozpoczęcia**, **Dzień zakończenia**, **Godzinę rozpoczęcia** i **Godzinę zakończenia** uruchomienia harmonogramu.
   2. Zaznacz opcję **wszystkie dni** , jeśli ten harmonogram ma być uruchamiany cały dzień.
   3. **Współczynnik przepustowości** określa w megabitach na sekundę (Mb/s) przepustowość wykorzystywaną przez urządzenie w operacjach dotyczących chmury — zarówno przekazywania, jak i pobierania danych. Podaj liczbę z zakresu od 20 do 2 147 483 647 dla tego pola.
   4. Zaznacz pole **Nieograniczona** przepustowość, jeśli nie chcesz ustalać czasu przekazywania i pobierania.
   5. Wybierz pozycję **Dodaj**.

      ![Dodaj harmonogram](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-2.png)

3. Harmonogram zostanie utworzony przy użyciu wybranych parametrów. Następnie harmonogram zostanie wyświetlony w portalu na liście harmonogramów przepustowości.

    ![Zaktualizowana lista harmonogramów przepustowości](media/azure-stack-edge-j-series-manage-bandwidth-schedules/add-schedule-3.png)

## <a name="edit-schedule"></a>Edytowanie harmonogramu

Wykonaj poniższe czynności, aby edytować harmonogram przepustowości.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do obszaru **przepustowość**. 
2. Z listy harmonogramów przepustowości Wybierz harmonogram, który chcesz zmodyfikować.
    ![Wybieranie harmonogramu przepustowości](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-1.png)

3. Wprowadź i zapisz odpowiednie zmiany.

    ![Modyfikowanie harmonogramu](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-2.png)

4. Lista harmonogramów zostanie zaktualizowana w celu uwzględnienia zmodyfikowanego harmonogramu.

    ![Modyfikowanie harmonogramu](media/azure-stack-edge-j-series-manage-bandwidth-schedules/modify-schedule-3.png)


## <a name="delete-a-schedule"></a>Usuwanie harmonogramu

Wykonaj następujące kroki, aby usunąć harmonogram przepustowości skojarzony z urządzeniem Azure Stack Edge.

1. W Azure Portal przejdź do zasobu Azure Stack Edge, a następnie przejdź do obszaru **przepustowość**.  

2. Na liście harmonogramów przepustowości wybierz harmonogram, który chcesz usunąć. W obszarze **Edytuj harmonogram**wybierz pozycję **Usuń**. Po wyświetleniu monitu o potwierdzenie wybierz pozycję **tak**.

   ![Usuwanie użytkownika](media/azure-stack-edge-j-series-manage-bandwidth-schedules/delete-schedule-2.png)

3. Po usunięciu harmonogramu lista harmonogramów zostanie zaktualizowana.


## <a name="next-steps"></a>Następne kroki

- Dowiedz się, jak [zarządzać udziałami](azure-stack-edge-j-series-manage-shares.md).
