---
title: Nawiązywanie połączenia z obszarem roboczym usługi Synapse przy użyciu linków prywatnych
description: W tym artykule opisano sposób nawiązywania połączenia z obszarem roboczym usługi Azure Synapse za pomocą linków prywatnych
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: ee915346a93a600ea352be42dc14e0464525c025
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312256"
---
# <a name="connect-to-your-azure-synapse-workspace-using-private-links-preview"></a>Nawiązywanie połączenia z obszarem roboczym usługi Azure Synapse za pomocą linków prywatnych (wersja zapoznawcza)

W tym artykule przedstawiono sposób tworzenia prywatnego punktu końcowego w obszarze roboczym usługi Azure Synapse. Aby dowiedzieć się więcej [, zobacz prywatne linki i prywatne punkty końcowe](https://docs.microsoft.com/azure/private-link/) .

## <a name="step-1-register-network-resource-provider"></a>Krok 1. rejestrowanie dostawcy zasobów sieciowych

Jeśli jeszcze tego nie zrobiono, zarejestruj dostawcę zasobów sieciowych. Rejestracja dostawcy zasobów umożliwia skonfigurowanie subskrypcji do pracy z dostawcą zasobów. Po [zarejestrowaniu](https://docs.microsoft.com/azure/azure-resource-manager/management/resource-providers-and-types)wybierz pozycję *Microsoft. Network* z listy dostawców zasobów. Jeśli dostawca zasobów sieciowych jest już zarejestrowany, przejdź do kroku 2.

## <a name="step-2-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 2. Otwieranie obszaru roboczego usługi Azure Synapse w Azure Portal

Wybierz pozycję **połączenie prywatne punktu końcowego** w obszarze **zabezpieczenia**. 
![Otwórz obszar roboczy usługi Azure Synapse w Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1.png)

Na następnym ekranie wybierz pozycję **+ prywatny punkt końcowy**.

![Otwórz prywatny punkt końcowy w Azure Portal](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-1a.png)

## <a name="step-3-select-your-subscription-and-region-details"></a>Krok 3. Wybieranie szczegółów dotyczących subskrypcji i regionu

Na karcie **podstawy** w oknie **Tworzenie prywatnego punktu końcowego** wybierz swoją **subskrypcję** i **grupę zasobów**. Nadaj **nazwę** do prywatnego punktu końcowego, który chcesz utworzyć. Wybierz **region** , w którym ma zostać utworzony prywatny punkt końcowy.

Prywatne punkty końcowe są tworzone w podsieci. Wybrana subskrypcja, Grupa zasobów i region odfiltruje podsieci prywatnych punktów końcowych. Wybierz pozycję **Dalej: zasób >** po zakończeniu.
![Wybierz szczegóły subskrypcji i regionu 1](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-2.png)

## <a name="step-4-select-your-azure-synapse-workspace-details"></a>Krok 4. Wybierz szczegóły obszaru roboczego usługi Azure Synapse

Wybierz pozycję **Połącz z zasobem platformy Azure w moim katalogu** na karcie **zasób** . Wybierz **subskrypcję** zawierającą obszar roboczy usługi Azure Synapse. **Typ zasobu** służący do tworzenia prywatnych punktów końcowych w obszarze roboczym usługi Azure Synapse to *Microsoft. Synapse/Workspaces*.

Wybierz obszar roboczy usługi Azure Synapse jako **zasób**. Każdy obszar roboczy usługi Azure Synapse ma trzy **docelowe zasoby podrzędne** , do których można utworzyć prywatny punkt końcowy: SQL, SqlOnDemand i dev.

Wybierz pozycję **Dalej: konfiguracja>** , aby przejść do następnej części Instalatora.
![Wybierz szczegóły subskrypcji i regionu 2](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-3.png)

Na karcie **Konfiguracja** wybierz **sieć wirtualną** i **podsieć** , w której ma zostać utworzony prywatny punkt końcowy. Należy również utworzyć rekord DNS, który jest mapowany na prywatny punkt końcowy.

Wybierz opcję **tak** dla **integracji z prywatną strefą DNS** , aby zintegrować prywatny punkt końcowy z prywatną strefą DNS. Jeśli nie masz skojarzonej ze swoim Microsoft Azure Virtual Network prywatnej strefy DNS, zostanie utworzona nowa prywatna strefa DNS. Wybierz pozycję **Przegląd + Utwórz** po zakończeniu.

![Wybierz szczegóły subskrypcji i regionu 3](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-4.png)

Po zakończeniu wdrażania Otwórz obszar roboczy usługi Azure Synapse w obszarze Azure Portal i wybierz pozycję **połączenia prywatne punktów końcowych**. Zostanie wyświetlony nowy prywatny punkt końcowy i nazwa połączenia prywatnego punktu końcowego skojarzone z prywatnym punktem końcowym.

![Wybierz szczegóły subskrypcji i regionu 4](./media/how-to-connect-to-workspace-with-private-links/private-endpoint-5.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzanym obszarze roboczym Virtual Network](./synapse-workspace-managed-vnet.md)

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)

[Tworzenie zarządzanych prywatnych punktów końcowych dla źródeł danych](./how-to-create-managed-private-endpoints.md)
