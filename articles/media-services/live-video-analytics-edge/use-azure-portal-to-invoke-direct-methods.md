---
title: Jak używać Azure Portal do wywoływania metod bezpośrednich
description: Ten artykuł zawiera omówienie używania Azure Portal do wywoływania metod bezpośrednich.
services: media-services
documentationcenter: ''
author: IngridAtMicrosoft
manager: femila
editor: ''
ms.service: media-services
ms.subservice: ''
ms.workload: ''
ms.topic: how-to
ms.custom: ''
ms.date: 07/24/2020
ms.author: inhenkel
ms.openlocfilehash: ddd6a94015829a0b5dd4053f4d3a2fd6d2650d32
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99492658"
---
# <a name="how-to-use-azure-portal-to-invoke-direct-methods"></a>Jak używać Azure Portal do wywoływania metod bezpośrednich

IoT Hub umożliwia wywoływanie [metod bezpośrednich](../../iot-hub/iot-hub-devguide-direct-methods.md#method-invocation-for-iot-edge-modules) na urządzeniach brzegowych z chmury. Usługa Live Video Analytics w IoT Edge (LVA) obejmuje kilka [metod bezpośrednich](./direct-methods.md) , których można użyć do definiowania, wdrażania i tworzenia wystąpienia różnych przepływów pracy na potrzeby analizowania wideo na żywo.

W tym artykule dowiesz się, jak wywołać bezpośrednie wywołania metod na żywo analizy wideo dla modułu IoT Edge za pośrednictwem Azure Portal.

## <a name="prerequisites"></a>Wymagania wstępne

* Masz uruchomioną analizę wideo na żywo na IoT Edge module uruchomionym na urządzeniu brzegowym, korzystając z metod opisanych w [przewodniku szybki start: Analiza filmów wideo na żywo na IoT Edge](./get-started-detect-motion-emit-events-quickstart.md) lub przy użyciu [portalu.](./deploy-iot-edge-device.md)

* Rozumiesz [analizę wideo na żywo](./overview.md) i [koncepcje grafu multimedialnego](./media-graph-concept.md).

## <a name="invoking-direct-methods-via-azure-portal"></a>Wywoływanie metod bezpośrednich za pośrednictwem Azure Portal

Każdą [metodę bezpośrednią](./direct-methods.md) uwidocznioną przez moduł LVA można wywołać za pośrednictwem Azure Portal. Poniższe kroki zawierają szczegóły jednej metody bezpośredniej. Inne metody bezpośrednie można wywołać przy użyciu podobnych kroków. Jednak każda metoda bezpośrednia wymaga określonej treści JSON.

Użyj `GraphTopologyList` wywołania metody, aby pobrać listę wszystkich topologii grafów aktualnie wdrożonych na żywo analizy wideo w module IoT Edge. Wykonaj następujące kroki, aby wywołać tę metodę bezpośrednią:

1. Zaloguj się do Azure Portal
1. Znajdź odpowiednią grupę zasobów na stronie głównej portalu, aby znaleźć IoT Hub, lub jeśli wiesz, że IoT Hub, wybierz ją.
    ![Grupa zasobów na stronie głównej portalu](media/use-azure-portal-to-invoke-directs-methods/portal-rg-home.png)
1. Na stronie IoT Hub wybierz pozycję IoT Edge w obszarze Automatyczne zarządzanie urządzeniami, aby wyświetlić listę różnych identyfikatorów urządzeń. Wybierz odpowiedni identyfikator urządzenia, aby wyświetlić listę modułów uruchomionych na urządzeniu.
    ![Strona Centrum IoT](media/use-azure-portal-to-invoke-directs-methods/iot-hub-page.png)
1. Wybierz moduł Analiza filmów wideo na żywo w IoT Edge module, aby wyświetlić jego stronę konfiguracji.<br><br>
    ![Wybierz moduł Analiza filmów wideo na żywo na IoT Edge, aby wyświetlić jego stronę konfiguracji](media/use-azure-portal-to-invoke-directs-methods/modules.png)
1. Wybierz opcję w menu Metoda bezpośrednia. <br><br>
    ![Kliknij opcję menu Metoda bezpośrednia](media/use-azure-portal-to-invoke-directs-methods/module-details.png)
    > [!NOTE]
    > Może być konieczne dodanie wartości w sekcjach parametrów połączenia, jak widać na bieżącej stronie. Nie trzeba ukrywać ani zmieniać żadnych elementów w sekcji Nazwa ustawienia. Jest to możliwe, aby pozwolić na publiczną.

1. Wpisz *GraphTopologyList* w polu **Nazwa metody** .
1. Skopiuj i wklej poniższy kod JSON w polu **ładunek** .
    ```json
    {
    "@apiVersion": "2.0"
    }
    ```
1. Wybierz przycisk **Wywołaj metodę** w górnej części strony.<br><br>
    ![przycisk wywołania metody](media/use-azure-portal-to-invoke-directs-methods/direct-method.png)
1. W obszarze **wynik** powinien zostać wyświetlony komunikat o stanie 200.<br><br>
    ![limit czasu połączenia](media/use-azure-portal-to-invoke-directs-methods/connection-timeout.png)

## <a name="responses"></a>Odpowiedzi

| Warunek             | Kod stanu | Szczegółowy kod błędu |
|-----------------------|-------------|---------------------|
| Powodzenie               | 200         | Nie dotyczy                 |
| Ogólne błędy użytkowników   | 400 zakresu   |                     |
| Ogólne błędy serwera | 500 zakresu   |                     |

## <a name="next-steps"></a>Następne kroki

Więcej metod bezpośrednich można znaleźć na stronie [metody bezpośrednie](./direct-methods.md) .

> [!NOTE]
> Wystąpienie grafu tworzy wystąpienia określonej topologii, dlatego przed utworzeniem wystąpienia grafu upewnij się, że masz odpowiedni zestaw topologii.

[Szybki Start: wykrywanie zdarzeń emisji ruchu](./get-started-detect-motion-emit-events-quickstart.md) jest dobrym odwołaniem do interpretacji dokładnej sekwencji wywołań metody bezpośredniej.
