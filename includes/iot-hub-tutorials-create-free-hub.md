---
title: Plik dyrektywy include
description: Plik dyrektywy include
services: iot-hub
author: dominicbetts
ms.service: iot-hub
ms.topic: include
ms.date: 04/19/2018
ms.author: dobett
ms.custom: include file
ms.openlocfilehash: 9151a84aa03e9b87b02278672622d458fbc5281e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99538780"
---
Aby utworzyć usługę IoT Hub przy użyciu witryny Azure Portal:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).

1. Na stronie głównej platformy Azure wybierz pozycję **Utwórz zasób**, a następnie wprowadź *IoT Hub* w **polu Wyszukaj w portalu Marketplace**.

1. Wybierz **IoT Hub** z wyników wyszukiwania, a następnie wybierz pozycję **Utwórz**.

1. Na karcie **podstawowe** Wypełnij pola w następujący sposób:

   - **Subskrypcja**: wybierz subskrypcję, która ma być używana przez centrum.

   - **Grupa zasobów**: Wybierz grupę zasobów lub Utwórz nową. Aby utworzyć nowy, wybierz pozycję **Utwórz nową** i wprowadź nazwę, której chcesz użyć. Aby użyć istniejącej grupy zasobów, wybierz tę grupę zasobów. Aby uzyskać więcej informacji, zobacz [Zarządzanie grupami zasobów usługi Azure Resource Manager](../articles/azure-resource-manager/management/manage-resource-groups-portal.md). W tym samouczku użyto nazwy **tutorials-iot-hub-rg**.

   - **Region**: Wybierz region, w którym ma się znajdować centrum. Wybierz lokalizację znajdującą się najbliżej siebie. Niektóre funkcje, takie jak [IoT Hub strumienie urządzeń](../articles/iot-hub/iot-hub-device-streams-overview.md), są dostępne tylko w określonych regionach. W przypadku tych ograniczonych funkcji należy wybrać jeden z obsługiwanych regionów. W tym samouczku jest stosowany region **zachodnie stany USA** .

   - **Nazwa IoT Hub**: Wprowadź nazwę centrum. Ta nazwa musi być unikatowa w skali globalnej. W tym samouczku są wykorzystywane **samouczki — IoT-Hub**. Podczas tworzenia centrum należy wybrać własną unikatową nazwę.

   [!INCLUDE [iot-hub-pii-note-naming-hub](iot-hub-pii-note-naming-hub.md)]

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-basics.png" alt-text="Tworzenie centrum w Azure Portal":::

1. Wybierz pozycję **Dalej: sieć** , aby kontynuować tworzenie centrum.

   Wybierz punkty końcowe, które mogą łączyć się z IoT Hub. Można wybrać ustawienie domyślne **publiczny punkt końcowy (wszystkie sieci)** lub wybrać **publiczny punkt końcowy (wybrane zakresy adresów IP)** lub **prywatny punkt końcowy**. Zaakceptuj domyślne ustawienie dla tego samouczka.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-networking.png" alt-text="Wybierz punkty końcowe, które mogą nawiązać połączenie.":::

1. Wybierz pozycję **Dalej: Zarządzanie** , aby kontynuować tworzenie centrum.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-management.png" alt-text="Ustaw rozmiar i skalę nowego centrum przy użyciu Azure Portal.":::

   W tym miejscu możesz zaakceptować ustawienia domyślne. W razie potrzeby można zmodyfikować dowolne z następujących pól:

   - **Cennik i warstwa skalowania**: wybrana warstwa. Wybierz warstwę bezpłatna. Warstwa Bezpłatna służy do testowania i oceny. Umożliwia podłączenie 500 urządzeń do centrum oraz do 8 000 komunikatów dziennie. Każda subskrypcja platformy Azure może utworzyć jedno Centrum IoT Hub w warstwie Bezpłatna.

   - **Jednostki centrum IoT**: liczba dozwolonych komunikatów na jednostkę dziennie zależy od warstwy cenowej centrum. Na przykład jeśli chcesz, aby koncentrator obsługiwał ruch przychodzący z 700 000 komunikatów, wybierz dwie jednostki warstwy S1.
   Każda subskrypcja platformy Azure może utworzyć jedno Centrum IoT Hub w warstwie Bezpłatna. Aby uzyskać szczegółowe informacje na temat innych opcji warstw, zobacz [Choosing the right IoT Hub tier](../articles/iot-hub/iot-hub-scaling.md) (Wybieranie właściwej warstwy usługi IoT Hub).

   - **Defender for IoT**: Włącz tę opcję, aby dodać dodatkową warstwę ochrony przed zagrożeniami do IoT i urządzeń. Ta opcja jest niedostępna dla centrów w warstwie Bezpłatna. Aby uzyskać więcej informacji na temat tej funkcji, zobacz [Azure Security Center for IoT](/azure/asc-for-iot/).

   - **Ustawienia zaawansowane**  >  **Partycje między urządzeniami a chmurą**: Ta właściwość wiąże komunikaty z urządzenia z chmurą z liczbą jednoczesnych czytników komunikatów. Większość centrów potrzebuje tylko czterech partycji. Koncentrator warstwy Bezpłatna jest ograniczony do dwóch partycji.

1. Wybierz pozycję **Dalej: Tagi** , aby przejść do następnego ekranu.

   Tagi to pary nazwa/wartość. Można przypisać ten sam tag do wielu zasobów i grup zasobów, aby klasyfikować zasoby i konsolidować rozliczenia. Aby uzyskać więcej informacji, zobacz [Używanie tagów do organizowania zasobów platformy Azure](../articles/azure-resource-manager/management/tag-resources.md).

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-tags.png" alt-text="Przypisz Tagi dla centrum przy użyciu Azure Portal.":::

1. Wybierz pozycję **Dalej: Przejrzyj i Utwórz** , aby przejrzeć wybrane opcje. Zobaczysz coś podobnego do tego ekranu, ale z wartościami wybranymi podczas tworzenia centrum.

   :::image type="content" source="media/iot-hub-tutorials-create-free-hub/hub-definition-create.png" alt-text="Przejrzyj informacje dotyczące tworzenia nowego centrum.":::

1. Zanotuj nazwę wybranego centrum IoT. Będzie potrzebna w dalszej części samouczka.
