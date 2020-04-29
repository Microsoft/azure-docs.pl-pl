---
title: Nawiązywanie połączenia z obszarem roboczym usługi Azure Synapse za pomocą linków prywatnych
description: W tym artykule opisano sposób nawiązywania połączenia z obszarem roboczym usługi Azure Synapse za pomocą linków prywatnych
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 5a00fc44021278a8b910cf454b43b0bae2c3a1f9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "81432192"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Nawiązywanie połączenia z obszarem roboczym usługi Azure Synapse za pomocą linków prywatnych (wersja zapoznawcza)

W tym artykule przedstawiono sposób tworzenia prywatnego punktu końcowego w obszarze roboczym usługi Azure Synapse. Aby dowiedzieć się więcej [, zobacz prywatne linki i prywatne punkty końcowe](https://docs.microsoft.com/azure/private-link/) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 1. Otwieranie obszaru roboczego usługi Azure Synapse w Azure Portal

Wybierz pozycję **połączenie prywatne punktu końcowego** w obszarze **zabezpieczenia** , a następnie wybierz pozycję **+ prywatny punkt końcowy**.
![Otwórz obszar roboczy usługi Azure Synapse w Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

## <a name="step-2-select-your-subscription-and-region-details"></a>Krok 2. Wybieranie szczegółów dotyczących subskrypcji i regionu

Na karcie **podstawy** w oknie **Tworzenie prywatnego punktu końcowego** wybierz swoją **subskrypcję** i **grupę zasobów**. Nadaj **nazwę** do prywatnego punktu końcowego, który chcesz utworzyć. Wybierz **region** , w którym ma zostać utworzony prywatny punkt końcowy.

Prywatne punkty końcowe są tworzone w podsieci. Wybrana subskrypcja, Grupa zasobów i region odfiltruje podsieci prywatnych punktów końcowych. Wybierz pozycję **Dalej: zasób >** po zakończeniu.
![Wybierz szczegóły subskrypcji i regionu](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-3-select-your-azure-synapse-workspace-details"></a>Krok 3. Wybierz szczegóły obszaru roboczego usługi Azure Synapse

Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu** na karcie **zasób** . Wybierz **subskrypcję** zawierającą obszar roboczy usługi Azure Synapse. **Typ zasobu** służący do tworzenia prywatnych punktów końcowych w obszarze roboczym usługi Azure Synapse to *Microsoft. Synapse/Workspaces*.

Wybierz obszar roboczy usługi Azure Synapse jako **zasób**. Każdy obszar roboczy usługi Azure Synapse ma trzy **docelowe zasoby podrzędne** , do których można utworzyć prywatny punkt końcowy: SQL, SqlOnDemand i dev.

Wybierz pozycję **Dalej: konfiguracja>** , aby przejść do następnej części Instalatora.
![Wybierz szczegóły subskrypcji i regionu](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Na karcie **Konfiguracja** wybierz **sieć wirtualną** i **podsieć** , w której ma zostać utworzony prywatny punkt końcowy. Należy również utworzyć rekord DNS, który jest mapowany na prywatny punkt końcowy.

Wybierz opcję **tak** dla **integracji z prywatną strefą DNS** , aby zintegrować prywatny punkt końcowy z prywatną strefą DNS. Jeśli nie masz prywatnej strefy DNS skojarzonej z siecią wirtualną, zostanie utworzona nowa prywatna strefa DNS. Wybierz pozycję **Przegląd + Utwórz** po zakończeniu.

![Wybierz szczegóły subskrypcji i regionu](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Po zakończeniu wdrażania Otwórz obszar roboczy usługi Azure Synapse w obszarze Azure Portal i wybierz pozycję **połączenia prywatne punktów końcowych**. Zostanie wyświetlony nowy prywatny punkt końcowy i nazwa połączenia prywatnego punktu końcowego skojarzone z prywatnym punktem końcowym.

![Wybierz szczegóły subskrypcji i regionu](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o sieci [wirtualnej zarządzanej przestrzeni roboczej](./synapse-workspace-managed-vnet.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych dla źródeł danych](./how-to-create-managed-private-endpoints.md)
