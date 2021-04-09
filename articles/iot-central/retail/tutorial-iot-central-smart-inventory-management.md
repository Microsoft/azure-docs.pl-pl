---
title: Samouczek dotyczący zarządzania magazynem w usłudze IoT Smart | Microsoft Docs
description: Samouczek szablonu aplikacji do zarządzania magazynem inteligentnym dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 3dbb6ca64451cb60d5a8ec67ecdc528865a4438c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "101719087"
---
# <a name="tutorial-deploy-and-walk-through-a-smart-inventory-management-application-template"></a>Samouczek: wdrażanie i przechodzenie przez szablon aplikacji do zarządzania magazynem inteligentnym

W tym samouczku pokazano, jak rozpocząć pracę, wdrażając szablon IoT Central aplikacji do **zarządzania magazynem inteligentnym** . Dowiesz się, jak wdrożyć szablon, co jest dołączone do pola i co warto zrobić dalej.

W ramach tego samouczka nauczysz się, jak,

> [!div class="checklist"]
> * Tworzenie aplikacji do zarządzania magazynem inteligentnym 
> * Przechodzenie przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożenie tej aplikacji nie wymaga określonych wymagań wstępnych
* Zalecane jest posiadanie subskrypcji platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-smart-inventory-management-application-template"></a>Utwórz szablon aplikacji do zarządzania magazynem inteligentnym

Możesz utworzyć aplikację, wykonując następujące czynności

1. Przejdź do witryny sieci Web programu Azure IoT Central Application Manager. Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie kliknij kartę **sprzedaż detaliczna** .

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/iotc_retail_homepage.png" alt-text="Zrzut ekranu przedstawiający sposób wybierania szablonu aplikacji do zarządzania magazynem inteligentnym":::

2. Wybierz kartę **sprzedaż detaliczna** i wybierz pozycję **Utwórz aplikację** w obszarze **Zarządzanie magazynem inteligentnym**

3. **Utwórz aplikację** spowoduje otwarcie formularza nowej aplikacji i zapełnienie żądanych szczegółów, jak pokazano poniżej.
    **Nazwa aplikacji**: możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
    **Adres URL**: możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikatowy adres URL, który można dopamiętać. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. Możesz zacząć od 7-dniowego planu cenowego w wersji próbnej i wybrać konwersję do standardowego planu cenowego w dowolnym momencie przed wygaśnięciem okresu bezpłatnego.
    **Informacje o rozliczeniach**: katalog, subskrypcja platformy Azure i szczegółowe informacje o regionie są wymagane do aprowizacji zasobów.
    **Utwórz**: wybierz pozycję Utwórz w dolnej części strony, aby wdrożyć aplikację.

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_app_create.png" alt-text="Zrzut ekranu przedstawiający sposób tworzenia aplikacji na podstawie szablonu aplikacji do zarządzania magazynem inteligentnym":::

    :::image type="content" source="media/tutorial-iot-central-smart-inventory-management/smart-inventory-management-app-create-billinginfo.png" alt-text="Zrzut ekranu przedstawiający opcje rozliczania podczas tworzenia aplikacji":::

## <a name="walk-through-the-application"></a>Przechodzenie przez aplikację 

### <a name="dashboard"></a>Pulpit nawigacyjny 

Po pomyślnym wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest portalem ukierunkowanym przez operatora zarządzania magazynem inteligentnym. Podmiot gospodarczy Northwind jest fikcyjnym dostawcą magazynu inteligentnego, zarządzanym magazynem z technologią Bluetooth Low Energy (beli) i sklepem detalicznym z identyfikacją częstotliwości radiowych (RFID). Na tym pulpicie nawigacyjnym zostaną wyświetlone dwie różne bramy dostarczające dane telemetryczne dotyczące spisu wraz z skojarzonymi poleceniami, zadaniami i akcjami, które można wykonywać. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do zaprezentowania krytycznego działania urządzenia do zarządzania magazynem inteligentnym.
Pulpit nawigacyjny jest logicznie podzielony między dwie różne operacje zarządzania urządzeniami bramy. 
   * Magazyn jest wdrażany ze stałą bramą do obsługi spisu & znacznikiami na paletach na potrzeby śledzenia & śledzenia śladów w większej pojemności
   * Sklep detaliczny jest implementowany ze stałą bramą RFID & znaczniki RFID na indywidualnym poziomie elementu, aby śledzić i śledzić zapasy w punkcie sprzedaży sklepu
   * Wyświetlanie informacji o lokalizacji bramy, & stanu 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający górną połowę managementdashboardnego spisu inteligentnego](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard1.png)

   * Możesz łatwo śledzić łączną liczbę bram, aktywnych i nieznanych tagów.
   * Można wykonywać operacje związane z zarządzaniem urządzeniami, takie jak oprogramowanie układowe aktualizacji, wyłączenie czujnika, włączenie czujnika, próg czujnika aktualizacji, aktualizowanie interwałów telemetrii & aktualizowanie kontraktów usługi urządzenia
   * Urządzenia bramy mogą wykonywać zarządzanie spisem na żądanie przy użyciu pełnego lub przyrostowego skanowania.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający dolną połowę managementdashboardu inteligentnego spisu](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia
Kliknij kartę szablony urządzeń, a zobaczysz model możliwości bramy. Model możliwości jest strukturalny wokół dwóch różnych poleceń **& właściwości** i **bramy** bramy między bramami interfejsów

**Właściwość & danych telemetrycznych bramy** — ten interfejs reprezentuje wszystkie dane telemetryczne związane z czujnikami, lokalizacją, informacjami o urządzeniu i właściwościami sieci, takimi jak progi bramy i interwały aktualizacji.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający szablon urządzenia bramy spisu w aplikacji](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate1.png)


**Polecenia bramy** — ten interfejs organizuje wszystkie możliwości polecenia bramy

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający interfejs poleceń bramy w szablonie urządzenia bramy spisu](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_devicetemplate2.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguły, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Te reguły są skonfigurowane do wysyłania powiadomień e-mail do operatorów w celu przeprowadzenia dalszych badań.

**Brama w trybie offline**: Ta reguła zostanie wyzwolona, jeśli Brama nie zgłosi do chmury przez długi czas. Brama może nie odpowiadać z powodu trybu niskiego poziomu naładowania baterii, utraty łączności i kondycji urządzenia.

**Nieznane znaczniki**: niezwykle ważne jest, aby śledzić każde oznakowanie RFID & znaczniki związane z elementem zawartości. Jeśli Brama wykrywa zbyt wiele nieznanych tagów, jest to wskazanie wyzwań związanych z synchronizacją przy użyciu aplikacji zawierających Tagi.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę reguł w managementapplicationach inteligentnych spisu](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_rules.png)

## <a name="jobs"></a>Stanowiska
Wybierz kartę zadania, aby zobaczyć pięć różnych zadań, które istnieją w ramach tego szablonu aplikacji: funkcja zadań służy do wykonywania operacji na całym rozwiązaniu. W tym miejscu zadania zarządzania spisem służą do wykonywania zadań, takich jak,
   * Wyłączanie czytników na całej bramie
   * Modyfikowanie progu telemetrii między 
   * wykonaj skanowanie spisu na żądanie w całym rozwiązaniu.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający listę zadań w usłudze Smart Inventory managementapplication](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_jobs.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji, odwiedzając   >  **Ustawienia aplikacji** Administracja, a następnie kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający sposób usuwania aplikacji po jej zakończeniu](./media/tutorial-iot-central-smart-inventory-management/smart_inventory_management_cleanup.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o zarządzaniu magazynem inteligentnym:

> [!div class="nextstepaction"]
> [Koncepcja zarządzania magazynem inteligentnego](./architecture-smart-inventory-management.md)
