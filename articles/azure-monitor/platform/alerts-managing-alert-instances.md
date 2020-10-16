---
title: Zarządzanie wystąpieniami alertów w Azure Monitor
description: Zarządzanie wystąpieniami alertów na platformie Azure
ms.subservice: alerts
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 82905cba5f391365ada13f4e5df5ad139f4c121e
ms.sourcegitcommit: ae6e7057a00d95ed7b828fc8846e3a6281859d40
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92102874"
---
# <a name="manage-alert-instances-with-unified-alerts"></a>Zarządzanie wystąpieniami alertów za pomocą ujednoliconych alertów

Korzystając z [funkcji ujednoliconych alertów](./alerts-overview.md) w Azure monitor, można zobaczyć wszystkie różne typy alertów na platformie Azure. Obejmuje to wiele subskrypcji w jednym okienku. W tym artykule pokazano, jak można wyświetlać wystąpienia alertów oraz jak znaleźć określone wystąpienia alertów w celu rozwiązywania problemów.

> [!NOTE]
> Można uzyskać dostęp tylko do alertów wygenerowanych w ciągu ostatnich 30 dni.

## <a name="go-to-the-alerts-page"></a>Przejdź do strony alerty

Można przejść do strony alerty w dowolny z następujących sposobów:

- W [Azure Portal](https://portal.azure.com/)wybierz pozycję **Monitoruj**  >  **alerty**.  

     ![Zrzut ekranu przedstawiający alerty monitora](media/alerts-managing-alert-instances/monitoring-alerts-managing-alert-instances-toc.jpg)
  
- Użyj kontekstu określonego zasobu. Otwórz zasób, przejdź do sekcji **monitorowanie** i wybierz pozycję **alerty**. Strona docelowa jest wstępnie filtrowana pod kątem alertów dotyczących tego konkretnego zasobu.

     ![Zrzut ekranu alertów dotyczących monitorowania zasobów](media/alerts-managing-alert-instances/alert-resource.JPG)

- Użyj kontekstu określonej grupy zasobów. Otwórz grupę zasobów, przejdź do sekcji **monitorowanie** , a następnie wybierz pozycję **alerty**. Strona docelowa jest wstępnie filtrowana dla alertów w danej grupie zasobów.    

     ![Zrzut ekranu alertów monitorowania grupy zasobów](media/alerts-managing-alert-instances/alert-rg.JPG)

## <a name="find-alert-instances"></a>Znajdź wystąpienia alertów

Strona **Podsumowanie alertów** zawiera przegląd wszystkich wystąpień alertów na platformie Azure. Widok podsumowania można zmodyfikować, wybierając **wiele subskrypcji** (maksymalnie 5) lub przez filtrowanie między **grupami zasobów**, określonymi **zasobami**lub **zakresami czasu**. Wybierz pozycję **całkowita liczba alertów**lub dowolny z poziomów ważności, aby przejść do widoku listy alertów.     

![Zrzut ekranu przedstawiający stronę podsumowania alertów](media/alerts-managing-alert-instances/alerts-summary.jpg)
 
Na stronie **wszystkie alerty** zostaną wyświetlone wszystkie wystąpienia alertów na platformie Azure. W przypadku przełączenia się do portalu z poziomu powiadomienia o alercie można użyć dostępnych filtrów, aby zawęzić w tym konkretnym wystąpieniu alertu.

> [!NOTE]
> Jeśli strona została przełączona na stronę, wybierając dowolny z poziomów ważności, lista jest wstępnie filtrowana pod kątem ważności.

Oprócz filtrów dostępnych na poprzedniej stronie można również filtrować według podstaw monitorowania usługi (na przykład Platform for Metrics), warunku monitorowania (wywoływanego lub rozwiązanego), ważności, stanu alertu (nowy/potwierdzony/zamknięty) lub identyfikatora grupy inteligentnej.

![Zrzut ekranu przedstawiający stronę wszystkie alerty](media/alerts-managing-alert-instances/all-alerts.jpg)

> [!NOTE]
> Jeśli strona została przełączona na stronę, wybierając dowolny z poziomów ważności, lista jest wstępnie filtrowana pod kątem ważności.

Po wybraniu dowolnego wystąpienia alertu zostanie otwarta strona **szczegóły alertu** , która umożliwia wyświetlenie szczegółowych informacji o tym konkretnym wystąpieniu alertu.   

![Zrzut ekranu przedstawiający stronę szczegółów alertu](media/alerts-managing-alert-instances/alert-details.jpg)