---
title: plik dołączania
description: plik dołączania
author: robinsh
ms.service: iot-hub
services: iot-hub
ms.topic: include
ms.date: 02/14/2020
ms.author: robinsh
ms.custom: include file
ms.openlocfilehash: d7ac494edfb9b1deeceafcac523ac58bccde96b3
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "85365152"
---
W tej sekcji opisano, jak utworzyć centrum IoT przy użyciu witryny [Azure Portal](https://portal.azure.com).

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na stronie głównej platformy Azure wybierz przycisk **+ Utwórz zasób** , a następnie wprowadź *IoT Hub* w polu Wyszukaj w **portalu Marketplace** .

1. Wybierz **IoT Hub** z wyników wyszukiwania, a następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Wypełnij pola w następujący sposób:

   - **Subskrypcja**: wybierz subskrypcję, która ma być używana przez centrum.

   - **Grupa zasobów**: Wybierz grupę zasobów lub Utwórz nową. Aby utworzyć nowy, wybierz pozycję **Utwórz nową** i wprowadź nazwę, której chcesz użyć. Aby użyć istniejącej grupy zasobów, wybierz tę grupę zasobów. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md).

   - **Region**: Wybierz region, w którym ma się znajdować centrum. Wybierz lokalizację znajdującą się najbliżej siebie. Niektóre funkcje, takie jak [IoT Hub strumienie urządzeń](../articles/iot-hub/iot-hub-device-streams-overview.md), są dostępne tylko w określonych regionach. W przypadku tych ograniczonych funkcji należy wybrać jeden z obsługiwanych regionów.

   - **Nazwa IoT Hub**: Wprowadź nazwę centrum. Ta nazwa musi być unikatowa w skali globalnej. Jeśli wprowadzona nazwa jest dostępna, zostanie wyświetlony zielony znacznik wyboru.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   ![Tworzenie centrum w Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-basics.png)

1. Wybierz pozycję **Dalej: rozmiar i skala** , aby kontynuować tworzenie centrum.

   ![Ustawianie rozmiaru i skali nowego centrum przy użyciu Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-screen-size-scale.png)

   W tym miejscu możesz zaakceptować ustawienia domyślne. W razie potrzeby można zmodyfikować dowolne z następujących pól: 

    - **Cennik i warstwa skalowania**: wybrana warstwa. Możesz wybrać jedną z wielu warstw, w zależności od liczby potrzebnych funkcji i liczby komunikatów wysyłanych przez Twoje rozwiązanie dziennie. Warstwa Bezpłatna służy do testowania i oceny. Umożliwia podłączenie 500 urządzeń do centrum oraz do 8 000 komunikatów dziennie. Każda subskrypcja platformy Azure może utworzyć jedno Centrum IoT Hub w warstwie Bezpłatna. 

      Jeśli pracujesz za pomocą usługi szybki start dla IoT Hub strumieni urządzeń, wybierz warstwę bezpłatna.

    - **Jednostki centrum IoT**: liczba dozwolonych komunikatów na jednostkę dziennie zależy od warstwy cenowej centrum. Na przykład jeśli chcesz, aby koncentrator obsługiwał ruch przychodzący z 700 000 komunikatów, wybierz dwie jednostki warstwy S1.
    Aby uzyskać szczegółowe informacje na temat innych opcji warstw, zobacz [Choosing the right IoT Hub tier](../articles/iot-hub/iot-hub-scaling.md) (Wybieranie właściwej warstwy usługi IoT Hub).

    - **Azure Security Center**: Włącz tę opcję, aby dodać dodatkową warstwę ochrony przed zagrożeniami do IoT i urządzeń. Ta opcja jest niedostępna dla centrów w warstwie Bezpłatna. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Azure Security Center for IoT](https://docs.microsoft.com/azure/asc-for-iot/).

    - **Ustawienia zaawansowane**  >  **Partycje między urządzeniami a chmurą**: Ta właściwość wiąże komunikaty z urządzenia z chmurą z liczbą jednoczesnych czytników komunikatów. Większość centrów potrzebuje tylko czterech partycji.

1.  Wybierz pozycję **Dalej: Tagi** , aby przejść do następnego ekranu.

    Tagi to pary nazwa/wartość. Można przypisać ten sam tag do wielu zasobów i grup zasobów, aby klasyfikować zasoby i konsolidować rozliczenia. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](../articles/azure-resource-manager/management/tag-resources.md).

    ![Przypisywanie tagów dla centrum przy użyciu Azure Portal](./media/iot-hub-include-create-hub/iot-hub-create-tabs.png)

1.  Wybierz pozycję **Dalej: Przejrzyj i Utwórz** , aby przejrzeć wybrane opcje. Zobaczysz coś podobnego do tego ekranu, ale z wartościami wybranymi podczas tworzenia centrum. 

    ![Przejrzyj informacje dotyczące tworzenia nowego centrum](./media/iot-hub-include-create-hub/iot-hub-create-review.png)

1.  Wybierz pozycję **Utwórz** , aby utworzyć nowe centrum. Utworzenie centrum zajmuje kilka minut.
