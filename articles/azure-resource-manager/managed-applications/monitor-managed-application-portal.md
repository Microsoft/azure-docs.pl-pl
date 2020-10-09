---
title: Używanie Azure Portal do monitorowania zarządzanej aplikacji
description: Pokazuje, w jaki sposób używać Azure Portal do monitorowania dostępności i alertów dla aplikacji zarządzanej.
author: tfitzmac
ms.topic: conceptual
ms.date: 10/04/2018
ms.author: tomfitz
ms.openlocfilehash: afe78dd00ecebdc54b6d73c4c8324729e117d95b
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75651750"
---
# <a name="monitor-a-deployed-instance-of-a-managed-application"></a>Monitorowanie wdrożonego wystąpienia aplikacji zarządzanej

Po wdrożeniu aplikacji zarządzanej w ramach subskrypcji platformy Azure warto sprawdzić stan aplikacji. W tym artykule przedstawiono opcje w Azure Portal sprawdzania stanu. Dostępność zasobów w aplikacji zarządzanej można monitorować. Możesz również skonfigurować alerty i wyświetlać je.

## <a name="view-resource-health"></a>Wyświetlanie kondycji zasobu

1. Wybierz wystąpienie aplikacji zarządzanej.

   ![Wybierz aplikację zarządzaną](./media/monitor-managed-application-portal/select-managed-application.png)

1. Wybierz pozycję **Kondycja zasobów**.

   ![Wybierz kondycję zasobu](./media/monitor-managed-application-portal/select-resource-health.png)

1. Wyświetl dostępność zasobów w aplikacji zarządzanej.

   ![Wyświetlanie kondycji zasobu](./media/monitor-managed-application-portal/view-health.png)

## <a name="view-alerts"></a>Wyświetlanie alertów

1. Wybierz pozycję **alerty**.

   ![Wybierz alerty](./media/monitor-managed-application-portal/select-alerts.png)

1. Jeśli masz skonfigurowane reguły alertów, zobaczysz informacje o alertach, które zostały zgłoszone.

   ![Wyświetlanie alertów](./media/monitor-managed-application-portal/view-alerts.png)

1. Aby dodać reguły alertów, wybierz pozycję **+ Nowa reguła alertu**.

   ![Tworzenie alertu](./media/monitor-managed-application-portal/create-new-alert.png)

Możesz tworzyć alerty dla wystąpienia aplikacji zarządzanej lub zasobów w zarządzanej aplikacji. Aby uzyskać informacje na temat tworzenia alertów, zobacz [Omówienie alertów w Microsoft Azure](../../azure-monitor/platform/alerts-overview.md).

## <a name="next-steps"></a>Następne kroki

* Aby zapoznać się z przykładami aplikacji zarządzanych, zobacz [przykładowe projekty dla aplikacji zarządzanych przez platformę Azure](sample-projects.md).
* Aby wdrożyć aplikację zarządzaną, zobacz [wdrażanie aplikacji katalogu usług za pomocą Azure Portal](deploy-service-catalog-quickstart.md).