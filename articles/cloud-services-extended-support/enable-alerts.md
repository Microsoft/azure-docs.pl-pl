---
title: Włącz monitorowanie w Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal
description: Włącz monitorowanie wystąpień Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal
ms.topic: how-to
ms.service: cloud-services-extended-support
author: gachandw
ms.author: gachandw
ms.reviewer: mimckitt
ms.date: 10/13/2020
ms.custom: ''
ms.openlocfilehash: 4591253e1a305632b7f41afe692f297d71366382
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98744858"
---
# <a name="enable-monitoring-for-cloud-services-extended-support-using-the-azure-portal"></a>Włącz monitorowanie dla Cloud Services (obsługa rozszerzona) przy użyciu Azure Portal

W tym artykule wyjaśniono, jak włączyć alerty dla istniejących wdrożeń usługi w chmurze (obsługa rozszerzona). 

## <a name="add-monitoring-rules"></a>Dodaj reguły monitorowania
1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com). 
2. Wybierz wdrożenie usługi w chmurze (obsługa rozszerzona), dla którego chcesz włączyć alerty. 
3. Wybierz blok **alerty** . 

    :::image type="content" source="media/enable-alerts-1.png" alt-text="Obraz przedstawia Wybieranie karty Alerty w Azure Portal.":::

4. Wybierz ikonę **nowego alertu** .
     :::image type="content" source="media/enable-alerts-2.png" alt-text="Obraz pokazuje wybranie opcji Dodaj nowy Alert.":::

5. Wprowadź odpowiednie warunki i wymagane akcje na podstawie metryk, które chcesz śledzić. Można zdefiniować reguły na podstawie poszczególnych metryk lub dziennika aktywności. 

     :::image type="content" source="media/enable-alerts-3.png" alt-text="Obraz pokazuje, gdzie należy dodać warunki do alertów.":::

     :::image type="content" source="media/enable-alerts-4.png" alt-text="Obraz pokazuje Konfigurowanie logiki sygnałów.":::

     :::image type="content" source="media/enable-alerts-5.png" alt-text="Obraz pokazuje Konfigurowanie logiki grupy akcji.":::

6. Po zakończeniu konfigurowania alertów Zapisz zmiany i na podstawie skonfigurowanych metryk zostanie wyświetlony blok **alerty** w czasie.

## <a name="next-steps"></a>Następne kroki 
- Zapoznaj się z [często zadawanymi pytaniami](faq.md) dotyczącymi Cloud Services (obsługa rozszerzona).
- Wdróż usługę w chmurze (obsługę rozszerzoną) przy użyciu [Azure Portal](deploy-portal.md), [programu PowerShell](deploy-powershell.md), [szablonu](deploy-template.md) lub [programu Visual Studio](deploy-visual-studio.md).