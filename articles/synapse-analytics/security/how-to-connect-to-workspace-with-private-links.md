---
title: Łączenie się z obszarem roboczym usługi Azure Synapse przy użyciu łączy prywatnych
description: W tym artykule dowiesz się, jak połączyć się z obszarem roboczym usługi Azure Synapse przy użyciu łączy prywatnych
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81432192"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Łączenie się z obszarem roboczym usługi Azure Synapse przy użyciu łączy prywatnych (wersja zapoznawcza)

W tym artykule dowiesz się, jak utworzyć prywatny punkt końcowy do obszaru roboczego usługi Azure Synapse. Aby dowiedzieć się więcej, [zobacz prywatne łącza i prywatne punkty końcowe.](https://docs.microsoft.com/azure/private-link/)

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 1: Otwórz obszar roboczy usługi Azure Synapse w witrynie Azure portal

Wybierz **pozycję Prywatne połączenie punktu końcowego** w obszarze **Zabezpieczenia,** a następnie wybierz pozycję **+ Prywatny punkt końcowy**.
![Otwieranie obszaru roboczego Usługi Azure Synapse w witrynie Azure portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Krok 2: Wybierz szczegóły subskrypcji i regionu

W obszarze **Karta Podstawy** w oknie Tworzenie prywatnego **punktu końcowego** wybierz pozycję **Subskrypcja** i **grupa zasobów**. Nadaj **nazwę** prywatnemu punktowi końcowemu, który chcesz utworzyć. Wybierz **region,** w którym chcesz utworzyć prywatny punkt końcowy.

Prywatne punkty końcowe są tworzone w podsieci. Wybrana subskrypcja, grupa zasobów i region filtrują prywatne podsieci punktów końcowych. Wybierz **dalej: >zasobu** po zakończeniu.
![Wybierz szczegóły subskrypcji i regionu](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Krok 3: Wybierz szczegóły obszaru roboczego Usługi Azure Synapse

Wybierz **pozycję Połącz z zasobem platformy Azure w moim katalogu** na karcie **Zasób.** Wybierz **subskrypcję** zawierającą obszar roboczy Usługi Azure Synapse. **Typ zasobu** do tworzenia prywatnych punktów końcowych w obszarze roboczym usługi Azure Synapse to *Obszary robocze Microsoft.Synapse/Workspaces*.

Wybierz obszar roboczy Usługi Azure Synapse jako **zasób**. Każdy obszar roboczy usługi Azure Synapse ma trzy **podsób docelowy,** który można utworzyć prywatny punkt końcowy do: Sql, SqlOnDemand i Dev.

Wybierz **przycisk Dalej:>konfiguracji,** aby przejść do następnej części konfiguracji.
![Wybierz szczegóły subskrypcji i regionu](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Na karcie **Konfiguracja** wybierz **sieć wirtualną** i **podsieć,** w której ma zostać utworzony prywatny punkt końcowy. Należy również utworzyć rekord DNS, który jest mapowany do prywatnego punktu końcowego.

Wybierz **opcję Tak,** **aby zintegrować z prywatną strefą DNS,** aby zintegrować prywatny punkt końcowy z prywatną strefą DNS. Jeśli z siecią wirtualną nie jest skojarzona prywatna strefa DNS, zostanie utworzona nowa prywatna strefa DNS. Wybierz **pozycję Recenzja + utwórz** po zakończeniu.

![Wybierz szczegóły subskrypcji i regionu](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Po zakończeniu wdrażania otwórz obszar roboczy usługi Azure Synapse w witrynie Azure portal i wybierz **prywatne połączenia z punktami końcowymi.** Zostanie wyświetlona nowa nazwa połączenia prywatnego punktu końcowego i prywatnego punktu końcowego skojarzonego z prywatnym punktem końcowym.

![Wybierz szczegóły subskrypcji i regionu](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [sieci wirtualnej zarządzanego obszaru roboczego](./synapse-workspace-managed-vnet.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych w źródłach danych](./how-to-create-managed-private-endpoints.md)
