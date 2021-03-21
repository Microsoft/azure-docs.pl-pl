---
title: Utwórz zarządzany prywatny punkt końcowy, aby połączyć się z wynikami źródła danych
description: W tym artykule omówiono sposób tworzenia zarządzanego prywatnego punktu końcowego dla źródeł danych w obszarze roboczym usługi Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: e0309b4c96b2ae25eb568e390717ba76cfd84fa5
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96461314"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source"></a>Utwórz zarządzany prywatny punkt końcowy ze źródłem danych

W tym artykule omówiono sposób tworzenia zarządzanego prywatnego punktu końcowego dla źródła danych na platformie Azure. Aby dowiedzieć się więcej, zobacz [zarządzane prywatne punkty końcowe](./synapse-workspace-managed-private-endpoints.md) .

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 1. Otwieranie obszaru roboczego usługi Azure Synapse w Azure Portal

Można utworzyć zarządzany prywatny punkt końcowy do źródła danych z usługi Azure Synapse Studio. Wybierz kartę **Przegląd** w Azure Portal a następnie wybierz pozycję **Otwórz** na karcie Otwórz kartę Synapse Studio w sekcji wprowadzenie.

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Krok 2. przechodzenie do karty zarządzane sieci wirtualne w programie Synapse Studio

W usłudze Azure Synapse Studio wybierz kartę **Zarządzanie** na pasku nawigacyjnym po lewej stronie. Wybierz pozycję **zarządzane prywatne punkty końcowe** , a następnie wybierz pozycję **+ Nowy**.
![Utwórz nowy zarządzany prywatny punkt końcowy](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Krok 3. Wybierz typ źródła danych

Wybierz typ źródła danych. W takim przypadku docelowym źródłem danych jest konto ADLS Gen2. Wybierz opcję **Kontynuuj**.
![Wybierz docelowy typ źródła danych](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Krok 4. Wprowadzanie informacji o źródle danych

W następnym oknie wprowadź informacje o źródle danych. W tym przykładzie tworzymy zarządzany prywatny punkt końcowy do konta ADLS Gen2. Wprowadź **nazwę** zarządzanego prywatnego punktu końcowego. Podaj **subskrypcję platformy Azure** i **nazwę konta magazynu**. Wybierz przycisk **Utwórz**.
![Wprowadź szczegóły docelowego źródła danych](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Krok 5. Sprawdzanie, czy zarządzany prywatny punkt końcowy został pomyślnie utworzony

Po przesłaniu żądania zobaczysz jego stan. Aby sprawdzić, czy utworzono zarządzany prywatny punkt końcowy, sprawdź jego *stan aprowizacji*. Może być konieczne odczekanie 1 minuty, a następnie wybierz pozycję **Odśwież** , aby zaktualizować stan aprowizacji. Można zobaczyć, że zarządzany prywatny punkt końcowy do konta ADLS Gen2 został pomyślnie utworzony.

Możesz również sprawdzić, czy *stan zatwierdzenia* jest *oczekiwany*. Właściciel zasobu docelowego może zatwierdzić lub odrzucić prywatne żądanie połączenia z punktem końcowym. Jeśli właściciel zatwierdzi żądanie połączenia z prywatnym punktem końcowym, zostanie nawiązane połączenie prywatne. W przypadku odmowy nie zostanie nawiązane połączenie prywatne.
![Stan żądania utworzenia zarządzanego prywatnego punktu końcowego](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)