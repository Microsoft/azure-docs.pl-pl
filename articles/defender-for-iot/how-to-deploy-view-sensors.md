---
title: Wyświetlanie czujników dołączanych i zarządzanie nimi w usłudze Azure Defender dla IoT
description: W tym artykule opisano sposób wyświetlania i usuwania czujników dołączanych, a także pobierania nowych plików aktywacji dla czujników dołączanych w usłudze Azure Defender dla IoT.
author: rkarlin
ms.author: rkarlin
ms.date: 10/10/2020
ms.topic: article
ms.service: azure
ms.openlocfilehash: 6aec19d413e1730bc1599f6cbac1c62e9b304ecd
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92094404"
---
# <a name="view-and-manage-onboarded-sensors"></a>Wyświetlanie czujników dołączanych i zarządzanie nimi

W tym artykule opisano sposób wyświetlania i usuwania czujników dołączanych, a także pobierania nowych plików aktywacji dla czujników dołączanych.

## <a name="update-sensor-management-mode"></a>Aktualizuj tryb zarządzania czujnikami

Możesz chcieć zaktualizować tryb zarządzany przez czujnik. Po wykonaniu tej czynności należy usunąć bieżący czujnik ze strony zarządzania czujnikami i przekazać nowy plik aktywacji.

- **Pracuj w trybie zarządzanym w chmurze, a nie w trybie zarządzanym lokalnie**: Zaktualizuj plik aktywacji dla lokalnego czujnika zarządzanego przy użyciu pliku aktywacji dla czujnika zarządzanego w chmurze. Po ponownym uaktywnieniu wykrywanie czujników są wyświetlane zarówno w portalu, jak i na platformie Azure Defender for IoT. Po pomyślnym przekazaniu pliku reaktywacji nowo wykryte informacje o alertach są wysyłane do platformy Azure.

- **Pracuj w trybie zarządzanym lokalnie, a nie w trybie zarządzanym w chmurze**: Zaktualizuj plik aktywacji dla czujnika zarządzanego w chmurze przy użyciu pliku aktywacji dla lokalnego czujnika zarządzanego. Po ponownej aktywacji informacje wykrywania czujnika są wyświetlane tylko w czujniku.

- **Skojarz czujnik z nowym IoT Hub**: możesz chcieć skojarzyć czujnik z nowym IoT Hub. W tym celu należy ponownie zarejestrować czujnik i przekazać nowy plik aktywacji.

**Aby ponownie uaktywnić Czujnik:**

1. Przejdź do strony Azure Defender for IoT, **zarządzanie czujnikiem** .

2. Wybierz czujnik, dla którego chcesz przekazać nowy plik aktywacji.

3. Usuń go.

4. Ponownie Dołącz czujnik **z strony** dołączania w trybie nowy lub z nowym IoT Hub.

5. Pobierz plik aktywacji ze strony **pobierania pliku aktywacji** .

6. Zaloguj się do konsoli czujnika usługi Azure Defender for IoT.

7. W konsoli czujnika wybierz pozycję **Ustawienia systemu** , a następnie wybierz pozycję **ponowna aktywacja**.

   ![Zrzut ekranu przedstawiający widok ponownej aktywacji](media/updates/image14.png)

8. Wybierz pozycję **Przekaż** i wybierz zapisany plik.

9. Wybierz pozycję **Aktywuj**.
 
## <a name="delete-sensors"></a>Usuń czujniki

W przypadku usunięcia czujnika zarządzanego w chmurze informacje nie będą wysyłane do IoT Hub.

Usuń zarządzane lokalnie czujniki, gdy nie będziesz już pracować z nimi.

**Aby usunąć czujniki:**

1. Przejdź do strony **zarządzania czujnikami** usługi Azure Defender for IoT.

2. Wybierz czujniki, które chcesz usunąć.

3. Wybierz pozycję **Usuń czujnik**.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej o opcjach konfiguracji, przejdź do przewodnika krok po kroku dotyczący konfiguracji modułu.
> [!div class="nextstepaction"]
> [Przewodnik po konfiguracji modułu](./how-to-agent-configuration.md)
