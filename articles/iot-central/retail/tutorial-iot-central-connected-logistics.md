---
title: Samouczek dotyczący rozwiązanej logistyki IoT | Microsoft Docs
description: Samouczek dotyczący połączonego szablonu aplikacji logistycznej dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 10/20/2019
ms.openlocfilehash: eac43ae68b10436b3e45452c6b1d03bec3ae4c9c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "81000560"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Samouczek: wdrażanie i przechodzenie przez połączony szablon aplikacji logistycznej



W tym samouczku pokazano, jak rozpocząć pracę, wdrażając IoT Central połączony szablon aplikacji **logistycznej** . Dowiesz się, jak wdrożyć szablon, co jest dołączone do pola i co warto zrobić dalej.

W ramach tego samouczka nauczysz się, jak,

* Tworzenie połączonej aplikacji logistycznej
* Przechodzenie przez aplikację 

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożenie tej aplikacji nie wymaga określonych wymagań wstępnych
* Zalecane jest posiadanie subskrypcji platformy Azure, ale możesz nawet spróbować bez niej

## <a name="create-connected-logistics-application-template"></a>Tworzenie szablonu połączonej aplikacji logistycznej

Możesz utworzyć aplikację, wykonując następujące czynności

1. Przejdź do witryny sieci Web programu Azure IoT Central Application Manager. Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie kliknij kartę **sprzedaż detaliczna** .

    > [!div class="mx-imgBorder"]
    > ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

2. Wybierz pozycję **Utwórz aplikację** w ramach **połączonej aplikacji logistycznej**

3. **Utwórz aplikację** spowoduje otwarcie formularza nowej aplikacji i zapełnienie żądanych szczegółów, jak pokazano poniżej.
   * **Nazwa aplikacji**: możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
   * **Adres URL**: możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikatowy adres URL, który można dopamiętać. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. Możesz zacząć od 7-dniowego planu cenowego w wersji próbnej i wybrać konwersję do standardowego planu cenowego w dowolnym momencie przed wygaśnięciem okresu bezpłatnego.
   * **Informacje o rozliczeniach**: katalog, subskrypcja platformy Azure i szczegółowe informacje o regionie są wymagane do aprowizacji zasobów.
   * **Utwórz**: wybierz pozycję Utwórz w dolnej części strony, aby wdrożyć aplikację.

    > [!div class="mx-imgBorder"]
    > ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

    > [!div class="mx-imgBorder"]
    > ![Informacje o rozwiązaniu dotyczące rozliczeń logistycznych](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

## <a name="walk-through-the-application"></a>Przechodzenie przez aplikację 

## <a name="dashboard"></a>Pulpit nawigacyjny

Po pomyślnym wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest podłączonym portalem ukierunkowanym na logistykę. Podmiot gospodarczy Northwind to fikcyjny dostawca logistyczny zarządzający flotą ładunku w oceanie i na lądzie. Na tym pulpicie nawigacyjnym zostaną wyświetlone dwie różne bramy dostarczające dane telemetryczne dotyczące wysyłek wraz z skojarzonymi poleceniami, zadaniami i akcjami, które można wykonać. Ten pulpit nawigacyjny jest wstępnie skonfigurowany do zaprezentowania krytycznych działań związanych z operacjami logistycznymi urządzenia.
Pulpit nawigacyjny jest logicznie podzielony między dwie różne operacje zarządzania urządzeniami bramy. 
   * Trasa logistyczna dla wysyłki i lokalizacji w układzie morskim jest istotnym elementem dla całego transportu wielomodalnego
   * Wyświetlanie stanu bramy & istotnych informacji 

> [!div class="mx-imgBorder"]
> ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png)

   * Możesz łatwo śledzić łączną liczbę bram, aktywnych i nieznanych tagów.
   * Można wykonywać operacje związane z zarządzaniem urządzeniami, takie jak oprogramowanie układowe aktualizacji, wyłączanie czujnika, Włączanie czujnika, próg czujnika aktualizacji, aktualizowanie interwałów telemetrii, & aktualizowanie umów usługi urządzenia.
   * Wyświetlanie zużycia baterii urządzenia

> [!div class="mx-imgBorder"]
> ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia

Kliknij kartę szablony urządzeń, a zobaczysz model możliwości bramy. Model możliwości jest strukturalny wokół dwóch różnych poleceń **& właściwości** i **bramy** bramy między bramami interfejsów

**Właściwość & danych telemetrycznych bramy** — ten interfejs reprezentuje wszystkie dane telemetryczne związane z czujnikami, lokalizacją i informacjami o urządzeniach, a także funkcję właściwości sieci bliźniaczych, takich jak progi czujników & interwały aktualizacji.

> [!div class="mx-imgBorder"]
> ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png)

**Polecenia bramy** — ten interfejs organizuje wszystkie możliwości polecenia bramy

> [!div class="mx-imgBorder"]
> ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png)

## <a name="rules"></a>Reguły
Wybierz kartę reguły, aby wyświetlić dwie różne reguły, które istnieją w tym szablonie aplikacji. Te reguły są skonfigurowane do wysyłania powiadomień e-mail do operatorów w celu przeprowadzenia dalszych badań.
 
**Alert dotyczący kradzieży bramy**: Ta reguła jest wyzwalana w przypadku nieoczekiwanego wykrywania oświetlenia przez czujniki w trakcie podróży. Operatory muszą zostać powiadomione JNW, aby zbadać potencjalne kradzieży.
 
**Brama nieodpowiadająca**: Ta reguła zostanie wyzwolona, jeśli Brama nie zgłosi do chmury przez długi czas. Brama może nie odpowiadać z powodu trybu niskiego poziomu naładowania baterii, utraty łączności i kondycji urządzenia.

> [!div class="mx-imgBorder"]
> ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png)

## <a name="jobs"></a>Stanowiska
Wybierz kartę zadania, aby zobaczyć pięć różnych zadań, które istnieją w ramach tego szablonu aplikacji:

> [!div class="mx-imgBorder"]
> ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png)

Funkcja zadania służy do wykonywania operacji w całym rozwiązaniu. W tym miejscu zadania są używane do wykonywania zadań, takich jak wyłączenie określonych czujników dla wszystkich bram lub modyfikowanie progu czujnika w zależności od trybu wysyłki i trasy. 
   * Jest to standardowa operacja, która umożliwia wyłączenie czujników wstrząsów podczas wysyłki w oceanie w celu zachowania baterii lub obniżenia progu temperatury podczas transportowania zimnego łańcucha. 
 
   * Zadania umożliwiają wykonywanie operacji na całym systemie, takich jak aktualizowanie oprogramowania układowego na bramach lub aktualizowanie kontraktu usługi, aby zachować bieżące informacje o działaniach konserwacyjnych.

## <a name="clean-up-resources"></a>Oczyszczanie zasobów
Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji, odwiedzając**Ustawienia aplikacji** **Administracja** > , a następnie kliknij przycisk **Usuń**.

> [!div class="mx-imgBorder"]
> ![Połączony pulpit nawigacyjny logistyki](./media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png)

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o [koncepcji rozwiązanej logistyki](./architecture-connected-logistics.md)
* Dowiedz się więcej na temat innych [szablonów detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o [IoT Central przegląd](../core/overview-iot-central.md)
