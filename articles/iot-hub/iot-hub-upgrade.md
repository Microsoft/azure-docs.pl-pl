---
title: Uaktualnianie usługi Azure IoT Hub | Microsoft Docs
description: Zmień Cennik i warstwę skalowania IoT Hub, aby uzyskać więcej możliwości zarządzania wiadomościami i urządzeniami.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 02/20/2019
ms.author: robinsh
ms.openlocfilehash: 96c3a7b2cfda23f173f4caeff4fb7a92b1ddc438
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "61440237"
---
# <a name="how-to-upgrade-your-iot-hub"></a>Jak uaktualnić centrum IoT

Wraz z rozwojem rozwiązania IoT usługa Azure IoT Hub jest gotowa do ułatwienia skalowania w górę. Usługa Azure IoT Hub oferuje dwie warstwy: podstawowe (B) i standardowe, aby pomieścić klientów, którzy chcą korzystać z różnych funkcji. W każdej warstwie są trzy rozmiary (1, 2 i 3) określające liczbę komunikatów, które mogą być wysyłane każdego dnia.

Jeśli masz więcej urządzeń i potrzebujesz więcej możliwości, istnieją trzy sposoby dostosowania Centrum IoT Hub do własnych potrzeb:

* Dodawanie jednostek w centrum IoT Hub. Na przykład każda dodatkowa jednostka w obszarze B1 IoT Hub zezwala na dodatkowe 400 000 komunikatów dziennie.

* Zmień rozmiar Centrum IoT. Na przykład Migruj z warstwy B1 do warstwy B2, aby zwiększyć liczbę komunikatów, które każda jednostka może obsłużyć dziennie.

* Uaktualnij do wyższego poziomu. Na przykład uaktualnienie z warstwy B1 do warstwy S1 w celu uzyskania dostępu do zaawansowanych funkcji z tą samą pojemnością obsługi wiadomości.

Te zmiany mogą wystąpić bez przerywania istniejących operacji.

Jeśli chcesz obniżyć wersję Centrum IoT Hub, możesz usunąć jednostki i zmniejszyć rozmiar Centrum IoT, ale nie możesz zmienić warstwy na niższą. Na przykład można przenieść z warstwy S2 do warstwy S1, ale nie z warstwy S2 do warstwy B1. Na IoT Hub można wybrać tylko jeden typ [wersji Centrum IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/) w warstwie. Można na przykład utworzyć IoT Hub z wieloma jednostkami S1, ale nie z różnymi jednostkami, takimi jak S1 i B3, lub S1 i S2.

Te przykłady mają na celu ułatwienie zrozumienia sposobu dostosowywania Centrum IoT w miarę wprowadzania zmian w rozwiązaniu. Aby uzyskać szczegółowe informacje o możliwościach poszczególnych warstw, zawsze należy zapoznać się z [cennikiem usługi Azure IoT Hub](https://azure.microsoft.com/pricing/details/iot-hub/).

## <a name="upgrade-your-existing-iot-hub"></a>Uaktualnianie istniejącego Centrum IoT Hub

1. Zaloguj się do [Azure Portal](https://portal.azure.com/) i przejdź do centrum IoT Hub.

2. Wybierz pozycję **Cennik i skala**.

   ![Ceny i skala](./media/iot-hub-upgrade/pricing-scale.png)

3. Aby zmienić warstwę dla centrum, wybierz pozycję **Cennik i warstwa skalowania**. Wybierz nową warstwę, a następnie kliknij pozycję **Wybierz**.

   ![Warstwa cenowa i warstwa skali](./media/iot-hub-upgrade/select-tier.png)

4. Aby zmienić liczbę jednostek w centrum, wprowadź nową wartość w obszarze **jednostki IoT Hub**.

5. Wybierz przycisk **Zapisz**, aby zapisać zmiany.

Twoje Centrum IoT jest teraz dostosowane, a konfiguracje nie są zmieniane.

Maksymalny limit partycji IoT Hub warstwy Podstawowa i IoT Hub to 32. Większość centrów IoT potrzebuje tylko 4 partycji. Limit partycji jest wybierany podczas tworzenia IoT Hub i wiąże komunikaty z urządzenia do chmury z liczbą jednoczesnych czytników tych komunikatów. Ta wartość pozostaje niezmieniona podczas migracji z warstwy Podstawowa do warstwy Standardowa.

## <a name="next-steps"></a>Następne kroki

Uzyskaj więcej szczegółowych informacji [na temat wybierania odpowiedniej warstwy IoT Hub](iot-hub-scaling.md).