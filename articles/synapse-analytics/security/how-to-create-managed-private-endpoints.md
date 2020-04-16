---
title: Utwórz zarządzany prywatny punkt końcowy, aby połączyć się z wynikami źródła danych.
description: W tym artykule dowiesz się, jak utworzyć zarządzany prywatny punkt końcowy do źródeł danych z obszaru roboczego Usługi Azure Synapse.
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.date: 04/15/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 363dc4d469d912c14f5f89ef6ff433a2243587e8
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81428903"
---
# <a name="create-a-managed-private-endpoint-to-your-data-source-preview"></a>Tworzenie zarządzanego prywatnego punktu końcowego w źródle danych (wersja zapoznawcza)

W tym artykule dowiesz się, jak utworzyć zarządzany prywatny punkt końcowy do źródła danych na platformie Azure. Zobacz [Zarządzane prywatne punkty końcowe,](./synapse-workspace-managed-private-endpoints.md) aby dowiedzieć się więcej.

## <a name="step-1-open-your-azure-synapse-workspace-in-azure-portal"></a>Krok 1: Otwórz obszar roboczy usługi Azure Synapse w witrynie Azure portal

Możesz utworzyć zarządzany prywatny punkt końcowy do źródła danych z usługi Azure Synapse Studio. Wybierz kartę **Przegląd** w witrynie Azure portal i wybierz pozycję **Uruchom studio Synapse Studio**.
![Uruchamianie usługi Azure Synapse Studio](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-1.png)

## <a name="step-2-navigate-to-the-managed-virtual-networks-tab-in-synapse-studio"></a>Krok 2: Przejdź do karty Zarządzane sieci wirtualne w Synapse Studio

W usłudze Azure Synapse Studio wybierz kartę **Zarządzaj** z lewej strony nawigacji. Wybierz **pozycję Zarządzane sieci wirtualne,** a następnie wybierz pozycję + **Nowy**.
![Tworzenie nowego zarządzanego prywatnego punktu końcowego](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-2.png)

## <a name="step-3-select-the-data-source-type"></a>Krok 3: Wybierz typ źródła danych

Wybierz typ źródła danych. W takim przypadku docelowym źródłem danych jest konto ADLS gen2. Wybierz przycisk **Kontynuuj**.
![Wybieranie docelowego typu źródła danych](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-3.png)

## <a name="step-4-enter-information-about-the-data-source"></a>Krok 4: Wprowadzanie informacji o źródle danych

W następnym oknie wprowadź informacje o źródle danych. W tym przykładzie tworzymy zarządzany prywatny punkt końcowy na koncie ADLS gen2. Wprowadź **nazwę** zarządzanego prywatnego punktu końcowego. Podaj **subskrypcję platformy Azure** i **nazwę konta magazynu**. Wybierz pozycję **Utwórz**.
![Wprowadzanie szczegółów docelowego źródła danych](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-4.png)

## <a name="step-5-verify-that-your-managed-private-endpoint-was-successfully-created"></a>Krok 5: Sprawdź, czy zarządzany prywatny punkt końcowy został pomyślnie utworzony

Po przesłaniu żądania zobaczysz jego stan. Aby sprawdzić pomyślne utworzenie zarządzanego prywatnego punktu końcowego, sprawdź jego *stan inicjowania obsługi administracyjnej*. Może być konieczne odczekanie 1 minuty i **wybranie opcji Odśwież,** aby zaktualizować stan inicjowania obsługi administracyjnej. Widać, że zarządzany prywatny punkt końcowy do konta ADLS gen2 został pomyślnie utworzony.

Można również sprawdzić, czy *stan zatwierdzenia* jest *oczekujący*. Właściciel zasobu docelowego może zatwierdzić lub odrzucić żądanie połączenia prywatnego punktu końcowego. Jeśli właściciel zatwierdzi żądanie połączenia prywatnego punktu końcowego, zostanie ustanowione łącze prywatne. Jeśli odmówiono, łącze prywatne nie zostanie ustanowione.
![Stan zarządzanego żądania utworzenia prywatnego punktu końcowego](./media/how-to-create-managed-private-endpoints/managed-private-endpoint-5.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o [zarządzanych prywatnych punktach końcowych](./synapse-workspace-managed-private-endpoints.md)