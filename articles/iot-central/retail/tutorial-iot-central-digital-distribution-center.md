---
title: Samouczek centrum dystrybucji cyfrowej IoT | Microsoft Docs
description: Samouczek szablonu aplikacji Digital Distribution Center dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 2b8ed417de966f8731542e8f5ca12a6a05e2dc8a
ms.sourcegitcommit: 2501fe97400e16f4008449abd1dd6e000973a174
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/08/2021
ms.locfileid: "99820283"
---
# <a name="tutorial-deploy-and-walk-through-a-digital-distribution-center-application-template"></a>Samouczek: wdrażanie i przechodzenie przez szablon aplikacji centrum dystrybucji cyfrowej

W tym samouczku pokazano, jak rozpocząć pracę, wdrażając szablon aplikacji IoT Central **Digital Distribution Center** . Dowiesz się, jak wdrożyć szablon, co jest dołączone do pola i co warto zrobić dalej.

W ramach tego samouczka nauczysz się, jak, 

> [!div class="checklist"]
> Tworzenie aplikacji centrum dystrybucji cyfrowej za pomocą aplikacji 

## <a name="prerequisites"></a>Wymagania wstępne
* Wdrożenie tej aplikacji nie wymaga określonych wymagań wstępnych
* Zalecane jest posiadanie subskrypcji platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-digital-distribution-center-application-template"></a>Utwórz szablon aplikacji centrum dystrybucji cyfrowej

Możesz utworzyć aplikację, wykonując następujące czynności

1. Przejdź do witryny sieci Web programu Azure IoT Central Application Manager. Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie kliknij kartę **sprzedaż detaliczna** .

    :::image type="content" source="media/tutorial-iot-central-ddc/iotc-retail-homepage.png" alt-text="Szablon aplikacji centrum dystrybucji cyfrowej":::
1. Wybierz kartę **sprzedaż detaliczna** i wybierz pozycję **Utwórz aplikację** w obszarze **aplikacja centrum dystrybucji cyfrowej**

1. **Utwórz aplikację** spowoduje otwarcie formularza nowej aplikacji i zapełnienie żądanych szczegółów, jak pokazano poniżej.
   **Nazwa aplikacji**: możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
   **Adres URL**: możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikatowy adres URL, który można dopamiętać. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. Możesz zacząć od 7-dniowego planu cenowego w wersji próbnej i wybrać konwersję do standardowego planu cenowego w dowolnym momencie przed wygaśnięciem okresu bezpłatnego.
   **Informacje o rozliczeniach**: katalog, subskrypcja platformy Azure i szczegółowe informacje o regionie są wymagane do aprowizacji zasobów.
   **Utwórz**: wybierz pozycję Utwórz w dolnej części strony, aby wdrożyć aplikację.

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia aplikacji z szablonu aplikacji Digital Distribution Center":::

   :::image type="content" source="media/tutorial-iot-central-ddc/ddc-create-billinginfo.png" alt-text="Zrzut ekranu przedstawiający opcje rozliczania podczas tworzenia aplikacji":::

## <a name="walk-through-the-application-dashboard"></a>Przechodzenie przez pulpit nawigacyjny aplikacji 

Po pomyślnym wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest portalem ukierunkowanym przez operatora centrum dystrybucji. Podmiot gospodarczy Northwind to fikcyjny dostawca rozwiązań centrum dystrybucji Zarządzający systemami przenośników. 

Na tym pulpicie nawigacyjnym zostanie wyświetlona jedna brama i aparat działający jako urządzenie IoT. Brama udostępnia dane telemetryczne dotyczące pakietów, takich jak prawidłowy, nieprawidłowy, nieidentyfikowany i rozmiar wraz ze skojarzonymi z nią właściwościami z sznurka urządzenia. Wszystkie polecenia podrzędne są wykonywane na urządzeniach IoT, takich jak aparat. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do zaprezentowania krytycznego działania centrum dystrybucji.

Pulpit nawigacyjny jest logicznie zorganizowany w celu pokazania możliwości zarządzania urządzeniami w usłudze Azure IoT Gateway i urządzeniu IoT.  
   * Można wykonywać polecenia bramy & zadania sterujące
   * Zarządzaj wszystkimi aparatami, które są częścią rozwiązania. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający pulpit nawigacyjny centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-dashboard.png)

## <a name="device-template"></a>Szablon urządzenia

Kliknij kartę szablony urządzeń, a zobaczysz model możliwości bramy. Model możliwości ma strukturę wokół dwóch różnych **aparatów** interfejsów i **bramy dystrybucji cyfrowej**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający szablon urządzenia bramy dystrybucji cyfrowej w aplikacji](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)

**Aparat fotograficzny** — ten interfejs organizuje wszystkie możliwości poleceń specyficznych dla aparatu 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający interfejs aparatu w szablonie urządzenia bramy dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-camera.png)

**Brama dystrybucji cyfrowej** — ten interfejs reprezentuje wszystkie dane telemetryczne pochodzące z aparatu, zdefiniowane przez chmurę właściwości i informacje o bramie.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający interfejs bramy dystrybucji cyfrowej w szablonie urządzenia bramy dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-devicetemplate1.png)


## <a name="gateway-commands"></a>Polecenia bramy
Ten interfejs organizuje wszystkie możliwości polecenia bramy

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający interfejs poleceń bramy w szablonie urządzenia bramy dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-camera.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguły, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Te reguły są skonfigurowane do wysyłania powiadomień e-mail do operatorów w celu przeprowadzenia dalszych badań.

 **Alert dotyczący zbyt wielu nieprawidłowych pakietów** — ta reguła jest wyzwalana, gdy aparat wykryje dużą liczbę nieprawidłowych pakietów przepływających przez system przenośnika.
 
**Duży pakiet** — ta reguła zostanie wyzwolona, jeśli aparat wykryje ogromny pakiet, którego nie można sprawdzić pod kątem jakości. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę reguł w aplikacji centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-rules.png)

## <a name="jobs"></a>Stanowiska
Wybierz kartę zadania, aby zobaczyć pięć różnych zadań, które istnieją w ramach tego szablonu aplikacji: można wykorzystać funkcję zadań do wykonywania operacji na całym rozwiązaniu. W tym miejscu zadania centrum dystrybucji cyfrowej służą do wykonywania zadań, takich jak, & z użyciem technologii bliźniaczych.
   * Kalibrowanie aparatu przed zainicjowaniem wykrywania pakietu 
   * okresowe aktualizowanie oprogramowania układowego aparatu
   * Modyfikowanie interwału telemetrii w celu zarządzania przekazywaniem danych

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę zadań w aplikacji centrum dystrybucji cyfrowej](./media/tutorial-iot-central-ddc/ddc-jobs.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji, odwiedzając   >  **Ustawienia aplikacji** Administracja, a następnie kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający sposób usuwania aplikacji po jej zakończeniu](./media/tutorial-iot-central-ddc/ddc-cleanup.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o architekturze rozwiązania centrum dystrybucji cyfrowej 
> [!div class="nextstepaction"]
> [koncepcja centrum dystrybucji cyfrowej](./architecture-digital-distribution-center.md)
* Dowiedz się więcej na temat innych [szablonów detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o IoT Central zapoznaj się z [omówieniem IoT Central](../core/overview-iot-central.md)
