---
title: Wdróż konsolę zarządzania w usłudze Azure Defender dla IoT
description: Dowiedz się, jak wdrożyć konsolę zarządzania w usłudze Azure Defender dla IoT.
services: defender-for-iot
ms.service: defender-for-iot
documentationcenter: na
author: rkarlin
manager: rkarlin
editor: ''
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/14/2020
ms.author: rkarlin
ms.openlocfilehash: 6af943951b2dba0c4a11b08ad1bcd961cf1c0fc2
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094414"
---
# <a name="deploy-the-management-console"></a>Wdróż konsolę zarządzania
W tym artykule opisano sposób wdrażania usługi Azure Defender for IoT w lokalnej konsoli zarządzania.

## <a name="the-on-premises-management-console"></a>Lokalna Konsola zarządzania

Lokalna Konsola zarządzania zapewnia skonsolidowany widok wszystkich zasobów sieci i zapewnia wgląd w informacje w czasie rzeczywistym dotyczące kluczowych rzeczy oraz wskaźników ryzyka i alertów we wszystkich swoich obiektach. Ścisła integracja z przepływami pracy SOC i uruchamianie książek umożliwia łatwe określanie priorytetów działań zaradczych i korelację między lokacjami.

- Kompleksowe — zmniejszenie złożoności dzięki pojedynczej ujednoliconej platformie na potrzeby zarządzania zasobami, ryzyka i zarządzania lukami w zabezpieczeniach oraz monitorowania zagrożeń z odpowiedzią na zdarzenia.

- Agregacja i korelacja — wyświetlanie, agregowanie i analizowanie danych i alertów zebranych ze wszystkich lokacji.

- Kontroluj wszystkie czujniki — Skonfiguruj i Monitoruj wszystkie czujniki z pojedynczej lokalizacji.

   ![Widok zarządzania witrynami usługi Azure Defender for IoT](media/updates/image2.png)

## <a name="deploy-the-on-premises-management-console-appliance"></a>Wdrażanie urządzenia lokalnej konsoli zarządzania

Ten proces wymaga pozyskiwania własnego sprzętu i instalowania oprogramowania. Rozwiązanie działa na certyfikowanych urządzeniach. Zapoznaj się ze [wskazówkami dotyczącymi specyfikacji sprzętu usługi Azure Defender for IoT](https://aka.ms/AzureDefenderforIoTBareMetalAppliance) jako odniesienia podczas zakupu certyfikowanego urządzenia.

Zapoznaj się z [przewodnikiem instalacji usługi Azure Defender for IoT](https://aka.ms/AzureDefenderforIoTInstallSensorISO) , aby uzyskać szczegółowe informacje na temat pobierania obrazu ISO i instalowania oprogramowania czujnika.

**Aby wdrożyć lokalną konsolę zarządzania:**

1. Przejdź do Microsoft Azure Defender for IoT.

2. Wybierz **lokalną konsolę zarządzania**.

   ![Widok lokalnej konsoli zarządzania usługi Azure Defender for IoT](media/updates/image15.png)

3. Wybierz wersję 3,1 z menu **Wybierz wersję** .

4. Wybierz pozycję **Pobierz** i Zapisz plik.

5. Po zainstalowaniu oprogramowania wykonaj zadania konfiguracji sieci. Szczegółowe informacje znajdują się w [przewodniku konfigurowania sieci usługi Azure Defender for IoT](https://aka.ms/AzureDefenderForIoTNetworkSetup) .

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do przewodnika krok po kroku dotyczący konfiguracji modułu.
> [!div class="nextstepaction"]
> [Przewodnik po konfiguracji modułu](./how-to-agent-configuration.md)
