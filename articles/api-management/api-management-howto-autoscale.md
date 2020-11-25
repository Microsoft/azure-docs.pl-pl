---
title: Konfigurowanie automatycznego skalowania wystąpienia usługi Azure API Management | Microsoft Docs
description: W tym temacie opisano sposób konfigurowania zachowania skalowania automatycznego dla wystąpienia usługi Azure API Management.
services: api-management
documentationcenter: ''
author: mikebudzynski
manager: anneta
editor: ''
ms.service: api-management
ms.workload: integration
ms.topic: article
ms.date: 06/20/2018
ms.author: apimpm
ms.openlocfilehash: 41a97c639d9148f2bff3e4f530363ea05a65a7b8
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96018255"
---
# <a name="automatically-scale-an-azure-api-management-instance"></a>Automatyczne skalowanie wystąpienia usługi Azure API Management  

Wystąpienie usługi Azure API Management można skalować automatycznie na podstawie zestawu reguł. To zachowanie można włączyć i skonfigurować za pomocą Azure Monitor i jest obsługiwane tylko w warstwach **standardowa** i **Premium** usługi Azure API Management.

Artykuł przeprowadzi Cię przez proces konfigurowania automatycznego skalowania i sugeruje optymalną konfigurację reguł skalowania automatycznego.

> [!NOTE]
> Usługa API Management w warstwie **zużycia** jest skalowana automatycznie na podstawie ruchu — bez konieczności dodatkowej konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym artykule, musisz:

+ Aktywna subskrypcja platformy Azure.
+ Posiadanie wystąpienia usługi Azure API Management. Aby uzyskać więcej informacji, zobacz [Tworzenie wystąpienia usługi Azure API Management](get-started-create-service-instance.md).
+ Zrozumienie koncepcji [pojemności wystąpienia usługi Azure API Management](api-management-capacity.md).
+ Poznaj [proces ręcznego skalowania wystąpienia API Management platformy Azure](upgrade-and-scale.md), w tym konsekwencje dotyczące kosztów.

[!INCLUDE [premium-standard.md](../../includes/api-management-availability-premium-standard.md)]

## <a name="azure-api-management-autoscale-limitations"></a>Ograniczenia skalowania automatycznego w usłudze Azure API Management

Przed skonfigurowaniem zachowania skalowania automatycznego należy wziąć pod uwagę pewne ograniczenia i konsekwencje podjęcia decyzji dotyczących skalowania.

+ Skalowanie automatyczne można włączyć tylko dla warstw **standardowa** i **Premium** usługi Azure API Management.
+ Warstwy cenowe określają również maksymalną liczbę jednostek dla wystąpienia usługi.
+ Proces skalowania zajmie co najmniej 20 minut.
+ Jeśli usługa jest zablokowana przez inną operację, żądanie skalowania zakończy się niepowodzeniem i zostanie ponowione automatycznie.
+ W przypadku usługi z wdrożeniami wieloregionalnymi można skalować tylko jednostki w **lokalizacji podstawowej** . Nie można skalować jednostek w innych lokalizacjach.

## <a name="enable-and-configure-autoscale-for-azure-api-management-service"></a>Włączanie i Konfigurowanie automatycznego skalowania dla usługi Azure API Management

Wykonaj poniższe kroki, aby skonfigurować automatyczne skalowanie dla usługi Azure API Management:

1. Przejdź do **monitorowania** wystąpienia w Azure Portal.

    ![Azure Monitor](media/api-management-howto-autoscale/01.png)

2. Wybierz pozycję **Skalowanie automatyczne** z menu po lewej stronie.

    ![Azure Monitor zasób automatycznego skalowania](media/api-management-howto-autoscale/02.png)

3. Znajdź usługę Azure API Management na podstawie filtrów w menu rozwijanych.
4. Wybierz żądane wystąpienie usługi Azure API Management.
5. W nowo otwartym obszarze kliknij przycisk **Włącz automatyczne skalowanie** .

    ![Włączanie automatycznego skalowania Azure Monitor](media/api-management-howto-autoscale/03.png)

6. W sekcji **reguły** kliknij pozycję **+ Dodaj regułę**.

    ![Reguła dodawania automatycznego skalowania Azure Monitor](media/api-management-howto-autoscale/04.png)

7. Zdefiniuj nową regułę skalowania w poziomie.

   Na przykład reguła skalowania w poziomie może wyzwolić dodanie jednostki API Management platformy Azure, gdy Metryka średniej wydajności w ciągu ostatnich 30 minut przekroczy 80%. Poniższa tabela zawiera konfigurację dla takiej reguły.

    | Parametr             | Wartość             | Uwagi                                                                                                                                                                                                                                                                           |
    |-----------------------|-------------------|---------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Źródło metryki         | Bieżący zasób  | Zdefiniuj regułę na podstawie bieżących metryk zasobów API Management platformy Azure.                                                                                                                                                                                                     |
    | *Kryteria*            |                   |                                                                                                                                                                                                                                                                                 |
    | Agregacja czasu      | Średnia           |                                                                                                                                                                                                                                                                                 |
    | Nazwa metryki           | Pojemność          | Metryka wydajności to metryka usługi Azure API Management, która odzwierciedla użycie zasobów wystąpienia usługi Azure API Management.                                                                                                                                                            |
    | Statystyka ziarna czasu  | Średnia           |                                                                                                                                                                                                                                                                                 |
    | Operator              | Większe niż      |                                                                                                                                                                                                                                                                                 |
    | Próg             | 80%               | Próg metryki średniej wydajności.                                                                                                                                                                                                                                 |
    | Czas trwania (w minutach) | 30                | Wartość przedziału czasu do średniej metryki wydajności jest specyficzna dla wzorców użycia. Im dłuższy czas polega na tym, że wygładzanie reakcji będzie nieprzerwanie wpływać na decyzje skalowalne w poziomie. Jednak również opóźnia wyzwalacz skalowalny w poziomie. |
    | *Akcja*              |                   |                                                                                                                                                                                                                                                                                 |
    | Operacja             | Zwiększ licznik o |                                                                                                                                                                                                                                                                                 |
    | Liczba wystąpień        | 1                 | Skaluj wystąpienie API Management platformy Azure o 1 jednostkę.                                                                                                                                                                                                                          |
    | Czas schładzania (minuty)   | 60                | Skalowanie w poziomie usługi Azure API Management trwa co najmniej 20 minut. W większości przypadków okres chłodzenia o 60 minut uniemożliwia wyzwolenie wielu skalowania w poziomie.                                                                                                  |

8. Kliknij przycisk **Dodaj** , aby zapisać regułę.

    ![Reguła skalowania w poziomie Azure Monitor](media/api-management-howto-autoscale/05.png)

9. Kliknij ponownie pozycję **+ Dodaj regułę**.

    Tym razem należy zdefiniować regułę skalowania w poziomie. Dzięki temu zasoby nie są tracone, gdy użycie interfejsów API zmniejszy się.

10. Zdefiniuj nową regułę skalowania w poziomie.

    Na przykład reguła skalowania może wyzwolić usunięcie jednostki API Management platformy Azure, gdy Metryka średniej wydajności w ciągu ostatnich 30 minut była mniejsza niż 35%. Poniższa tabela zawiera konfigurację dla takiej reguły.

    | Parametr             | Wartość             | Uwagi                                                                                                                                                                                                                                                                                                                                                                                                                                                                                               |
    |-----------------------|-------------------|-----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------|
    | Źródło metryki         | Bieżący zasób  | Zdefiniuj regułę na podstawie bieżących metryk zasobów API Management platformy Azure.                                                                                                                                                                                                                                                                                                                                                                                                                         |
    | *Kryteria*            |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Agregacja czasu      | Średnia           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Nazwa metryki           | Pojemność          | Taka sama Metryka jak wartość użyta w regule skalowania w poziomie.                                                                                                                                                                                                                                                                                                                                                                                                                                                 |
    | Statystyka ziarna czasu  | Średnia           |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operator              | Mniejsze niż         |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Próg             | 35%               | Podobnie jak reguła skalowania w poziomie, ta wartość jest intensywnie zależna od wzorców użytkowania API Management platformy Azure. |
    | Czas trwania (w minutach) | 30                | Taka sama jak wartość użyta w regule skalowania w poziomie.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | *Akcja*              |                   |                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                     |
    | Operacja             | Zmniejsz licznik o | Przeciwieństwem do tego, co zostało użyte w regule skalowania w poziomie.                                                                                                                                                                                                                                                                                                                                                                                                                                                   |
    | Liczba wystąpień        | 1                 | Taka sama jak wartość użyta w regule skalowania w poziomie.                                                                                                                                                                                                                                                                                                                                                                                                                                                  |
    | Czas schładzania (minuty)   | 90                | Skalowanie w poziomie powinno być bardziej ostrożne niż skalowanie w poziomie, dlatego okres chłodzenia powinien być dłuższy.                                                                                                                                                                                                                                                                                                                                                                                                    |

11. Kliknij przycisk **Dodaj** , aby zapisać regułę.

    ![Reguła skalowania Azure Monitor](media/api-management-howto-autoscale/06.png)

12. Ustaw **maksymalną** liczbę jednostek API Management platformy Azure.

    > [!NOTE]
    > Usługa Azure API Management ma limit jednostek, do których wystąpienie można skalować w poziomie. Limit zależy od warstwy usług.

    ![Zrzut ekranu przedstawiający miejsce ustawienia maksymalnej liczby jednostek API Management platformy Azure.](media/api-management-howto-autoscale/07.png)

13. Kliknij pozycję **Zapisz**. Twoje Skalowanie automatyczne zostało skonfigurowane.

## <a name="next-steps"></a>Następne kroki

- [Jak wdrożyć wystąpienie usługi Azure API Management w wielu regionach świadczenia usługi Azure](api-management-howto-deploy-multi-region.md)
- [Optymalizowanie i oszczędzanie wydatków w chmurze](../cost-management-billing/costs/quick-acm-cost-analysis.md?WT.mc_id=costmanagementcontent_docsacmhorizontal_-inproduct-learn)