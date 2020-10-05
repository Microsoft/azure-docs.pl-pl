---
title: 'Szybki Start: łączenie obszaru roboczego Power BI z obszarem roboczym Synapse'
description: Połącz obszar roboczy Power BI z obszarem roboczym usługi Azure Synapse Analytics, wykonując czynności opisane w tym przewodniku.
services: synapse-analytics
author: jocaplan
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: ''
ms.date: 04/29/2020
ms.author: jocaplan
ms.reviewer: jrasnick
ms.openlocfilehash: 9b4b9da37b753418773141f832f253003ad40014
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91260464"
---
# <a name="quickstart-linking-a-power-bi-workspace-to-a-synapse-workspace"></a>Szybki Start: łączenie obszaru roboczego Power BI z obszarem roboczym Synapse

W tym przewodniku szybki start dowiesz się, jak połączyć obszar roboczy Power BI z obszarem roboczym Synapse Analytics, aby utworzyć nowe raporty Power BI i zestawy danych z programu Synapse Studio (wersja zapoznawcza).

Jeśli nie masz subskrypcji platformy Azure, [przed rozpoczęciem utwórz bezpłatne konto](https://azure.microsoft.com/free/).

## <a name="prerequisites"></a>Wymagania wstępne

- [Tworzenie obszaru roboczego usługi Azure Synapse i skojarzonego konta magazynu](quickstart-create-workspace.md)
- [Obszar roboczy Power BI Professional lub Premium](https://docs.microsoft.com/power-bi/service-create-the-new-workspaces)

## <a name="link-power-bi-workspace-to-your-synapse-workspace"></a>Łączenie obszaru roboczego Power BI z obszarem roboczym Synapse

1. Rozpoczynając od Synapse Studio, kliknij przycisk **Zarządzaj**.

    ![Synapse Studio kliknij przycisk Zarządzaj.](media/quickstart-link-powerbi/synapse-studio-click-manage.png)

2. W obszarze **połączenia zewnętrzne**kliknij pozycję **połączone usługi**.

    ![Wyróżnione usługi połączone.](media/quickstart-link-powerbi/manage-click-linked-services.png)

3. Kliknij pozycję **+ Nowy**.

    ![+ Nowe połączone usługi są wyróżnione.](media/quickstart-link-powerbi/new-highlighted.png)

4. Kliknij **Power BI** a następnie kliknij przycisk **Kontynuuj**.

    ![Wyświetlanie Power BI połączonej usługi.](media/quickstart-link-powerbi/powerbi-linked-service.png)

5. Wprowadź nazwę połączonej usługi, a następnie wybierz obszar roboczy z listy rozwijanej.

    ![Wyświetlanie konfiguracji połączonej usługi Power BI.](media/quickstart-link-powerbi/workspace-link-dialog.png)

6. Kliknij pozycję **Utwórz**.

## <a name="view-power-bi-workspace-in-synapse-studio"></a>Wyświetlanie obszaru roboczego Power BI w programie Synapse Studio

Po połączeniu obszarów roboczych możesz przeglądać zestawy danych Power BI, edytować i tworzyć nowe raporty Power BI z programu Synapse Studio.

1. Kliknij pozycję **programowanie**.

    ![Synapse Studio kliknij pozycję Programowanie.](media/quickstart-link-powerbi/synapse-studio-click-develop.png)

2. Rozwiń węzeł Power BI i obszar roboczy, którego chcesz użyć.

    ![Rozwiń węzeł Power BI i obszar roboczy.](media/quickstart-link-powerbi/develop-expand-powerbi.png)

Nowe raporty można utworzyć, klikając **+** w górnej części karty **programowanie** . istniejące raporty można edytować, klikając nazwę raportu. Wszystkie zapisane zmiany zostaną zapisane z powrotem w obszarze roboczym Power BI.

![Wyświetlanie i edytowanie raportu Power BI.](media/quickstart-link-powerbi/powerbi-report.png)


## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej [na temat tworzenia raportu Power BI dla plików przechowywanych w usłudze Azure Storage](sql/tutorial-connect-power-bi-desktop.md).
