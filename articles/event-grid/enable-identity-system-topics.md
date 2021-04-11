---
title: Włącz tożsamość zarządzaną w temacie system Azure Event Grid
description: W tym artykule opisano sposób włączania tożsamości usługi zarządzanej dla tematu systemu Azure Event Grid.
ms.topic: how-to
ms.date: 03/25/2021
ms.openlocfilehash: 66b418787e5570dc5da06a5332dd834ccbfd4aef
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630520"
---
# <a name="assign-a-system-managed-identity-to-an-event-grid-system-topic"></a>Przypisywanie tożsamości zarządzanej przez system do tematu systemu Event Grid
W tym artykule dowiesz się, jak włączyć tożsamość zarządzaną przez system dla istniejącego tematu systemu Event Grid. Aby dowiedzieć się więcej o tożsamościach zarządzanych, zobacz [co to są tożsamości zarządzane dla zasobów platformy Azure](../active-directory/managed-identities-azure-resources/overview.md).  

> [!IMPORTANT]
> Obecnie nie można włączyć tożsamości zarządzanej przez system podczas tworzenia nowego tematu systemu, czyli podczas tworzenia subskrypcji zdarzeń dla zasobu platformy Azure, który obsługuje tematy systemowe. 


## <a name="use-azure-portal"></a>Korzystanie z witryny Azure Portal
Poniższa procedura pokazuje, jak włączyć tożsamość zarządzaną przez system dla tematu systemu. 

1. Przejdź do witryny [Azure Portal](https://portal.azure.com).
2. Wyszukaj **Tematy dotyczące systemu usługi Event Grid** na pasku wyszukiwania u góry.
3. Wybierz **temat systemowy** , dla którego chcesz włączyć zarządzaną tożsamość. 
4. W menu po lewej stronie wybierz pozycję **tożsamość** . Nie widzisz tej opcji w temacie systemowym, który znajduje się w lokalizacji globalnej. 
5. Włącz **przełącznik, aby** włączyć tożsamość. 
1. Wybierz pozycję **Zapisz** na pasku narzędzi, aby zapisać ustawienie. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic.png" alt-text="Strona tożsamości tematu systemu"::: 
1. W komunikacie potwierdzenia wybierz pozycję **tak** . 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-confirmation.png" alt-text="Przypisywanie tożsamości do tematu systemu — potwierdzenie"::: 
1. Upewnij się, że został wyświetlony identyfikator obiektu zarządzanej tożsamości przypisanej do systemu i zobacz link, aby przypisać role. 

    :::image type="content" source="./media/managed-service-identity/identity-existing-system-topic-completed.png" alt-text="Przypisywanie tożsamości do tematu systemu — ukończono"::: 

## <a name="global-azure-sources"></a>Globalne źródła platformy Azure
Tożsamość zarządzaną przez system można włączyć tylko dla regionalnych zasobów platformy Azure. Nie można go włączyć dla tematów systemowych skojarzonych z globalnymi zasobami platformy Azure, takimi jak subskrypcje platformy Azure, grupy zasobów lub Azure Maps. Tematy systemowe dla tych źródeł globalnych również nie są skojarzone z określonym regionem. Nie widzisz strony **tożsamości** dla tematu systemu, której lokalizacja jest ustawiona na **globalną**. 

:::image type="content" source="./media/managed-service-identity/system-topic-location-global.png" alt-text="Temat systemu z lokalizacją ustawioną na globalny"::: 



## <a name="next-steps"></a>Następne kroki
Dodaj tożsamość do odpowiedniej roli (na przykład Service Bus nadawcy danych) w miejscu docelowym (na przykład Kolejka Service Bus). Aby uzyskać szczegółowe instrukcje, zobacz [Dodawanie tożsamości do ról platformy Azure w miejscach docelowych](add-identity-roles.md). 