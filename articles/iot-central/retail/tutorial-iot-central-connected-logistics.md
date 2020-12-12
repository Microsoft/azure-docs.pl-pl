---
title: Samouczek dotyczący rozwiązanej logistyki IoT | Microsoft Docs
description: Samouczek dotyczący połączonego szablonu aplikacji logistycznej dla IoT Central
author: KishorIoT
ms.author: nandab
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 10/20/2019
ms.openlocfilehash: 398441123d5ad309d9c2d506a75b80b58a6d56d0
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346218"
---
# <a name="tutorial-deploy-and-walk-through-a-connected-logistics-application-template"></a>Samouczek: wdrażanie i przechodzenie przez połączony szablon aplikacji logistycznej

W tym samouczku pokazano, jak rozpocząć pracę z szablonem IoT Central *połączonej aplikacji logistycznej* . Dowiesz się, jak wdrożyć szablon i korzystać z niego.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie połączonej aplikacji logistycznej.
> * Użyj najważniejszych funkcji w aplikacji.
> * Użyj pulpitu nawigacyjnego, aby wyświetlić krytyczne działania związane z działaniem urządzenia.
> * Użyj szablonu urządzenia
> * Przestrzeganie reguł
> * Korzystanie z zadań

## <a name="prerequisites"></a>Wymagania wstępne

* Wdrożenie tej aplikacji nie wymaga określonych wymagań wstępnych.
* Możesz użyć bezpłatnego planu cenowego lub skorzystać z subskrypcji platformy Azure.

## <a name="create-connected-logistics-application"></a>Tworzenie połączonej aplikacji logistycznej

Utwórz aplikację, wykonując następujące czynności:

1. Przejdź do witryny [Azure IoT Central Build](https://aka.ms/iotcentral) . Następnie zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft. Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie wybierz kartę **detaliczna** :

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png" alt-text="Połączony szablon logistyczny":::

1. Wybierz pozycję **Utwórz aplikację** w obszarze **podłączona aplikacja logistyczna**.

1. **Utwórz aplikację** otwiera formularz **nowej aplikacji** . Wprowadź następujące wartości:


    * **Nazwa aplikacji**: możesz użyć domyślnej sugerowanej nazwy lub wprowadzić przyjazną nazwę aplikacji.
    * **Adres URL**: możesz użyć sugerowanego domyślnego adresu URL lub wprowadzić przyjazny unikatowy adres URL, który można dopamiętać. Następnie ustawienie domyślne jest zalecane, jeśli masz już subskrypcję platformy Azure. Możesz zacząć od 7-dniowego planu cenowego w wersji próbnej i wybrać konwersję do standardowego planu cenowego w dowolnym momencie przed wygaśnięciem okresu bezpłatnego.
    * **Informacje o rozliczeniach**: katalog, subskrypcja platformy Azure i szczegółowe informacje o regionie są wymagane do aprowizacji zasobów.
    * **Utwórz**: wybierz pozycję Utwórz w dolnej części strony, aby wdrożyć aplikację.

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png" alt-text="Szablon połączonej aplikacji logistycznej":::

    :::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png" alt-text="Informacje o rozwiązaniu dotyczące rozliczeń logistycznych":::

## <a name="walk-through-the-application"></a>Przechodzenie przez aplikację

Poniżej znajduje się zrzut ekranu przedstawiający sposób wybierania szablonu aplikacji połączonej logistyki.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający sposób wybierania szablonu aplikacji połączonej logistyki](./media/tutorial-iot-central-connected-logistics/iotc-retail-homepage.png)

W poniższych sekcjach omówiono najważniejsze funkcje aplikacji.

### <a name="dashboard"></a>Pulpit nawigacyjny

Po wdrożeniu szablonu aplikacji domyślny pulpit nawigacyjny jest podłączonym ukierunkowanym operatorem logistyki. Podmiot gospodarczy Northwind to fikcyjny dostawca logistyczny zarządzający flotą za ładunki na morzu i na lądzie. Na tym pulpicie nawigacyjnym są widoczne dwie różne bramy dostarczające dane telemetryczne z wysyłek, wraz z skojarzonymi poleceniami, zadaniami i akcjami.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający sposób tworzenia aplikacji na podstawie szablonu połączonej aplikacji logistycznej](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create.png)

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający opcje rozliczania podczas tworzenia aplikacji](./media/tutorial-iot-central-connected-logistics/connected-logistics-app-create-billinginfo.png)

Ten pulpit nawigacyjny jest wstępnie skonfigurowany do wyświetlania krytycznej aktywności logistycznej operacji urządzenia.

Pulpit nawigacyjny włącza dwie różne operacje zarządzania urządzeniami bramy:

* Wyświetlaj trasy logistyczne dla wysyłek samochodowych i szczegóły lokalizacji wysyłek oceanu.
* Wyświetl stan bramy i inne istotne informacje.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard1.png" alt-text="Połączony pulpit nawigacyjny logistyki":::

* Można śledzić łączną liczbę bram, aktywnych i nieznanych tagów.
* Możesz wykonywać operacje związane z zarządzaniem urządzeniami, takimi jak: aktualizowanie oprogramowania układowego, wyłączanie i włączanie czujników, aktualizowanie progu czujnika, aktualizowanie interwałów telemetrii i aktualizowanie kontraktów usługi urządzeń.
* Wyświetl zużycie baterii urządzenia.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-dashboard2.png" alt-text="Stan połączonego pulpitu nawigacyjnego logistyki":::

#### <a name="device-template"></a>Szablon urządzenia

Wybierz pozycję **Szablony urządzeń** , aby wyświetlić model możliwości bramy. Model możliwości jest strukturalny wokół interfejsów polecenia **& danych telemetrycznych bramy** i **poleceń bramy** .

**Właściwość & danych telemetrycznych bramy** — ten interfejs definiuje wszystkie dane telemetryczne związane z czujnikami, lokalizacją i informacjami o urządzeniu. Interfejs definiuje również funkcje właściwości sieci bliźniaczych, takie jak progi czujnika i interwały aktualizacji.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate1.png" alt-text="Telemetria i interfejs właściwości":::

**Polecenia bramy** — ten interfejs organizuje wszystkie możliwości polecenia bramy:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-devicetemplate2.png" alt-text="Interfejs poleceń bramy":::

### <a name="rules"></a>Reguły

Wybierz kartę **reguły** do reguł w tym szablonie aplikacji. Te reguły są skonfigurowane do wysyłania powiadomień e-mail do operatorów w celu przeprowadzenia dalszych badań:

**Alert dotyczący kradzieży bramy**: Ta zasada wyzwala, gdy w trakcie podróży wystąpi nieoczekiwane wykrywanie oświetlenia. W celu zbadania potencjalnego kradzieży należy natychmiast powiadomić operatorów.

**Brama nieodpowiadająca**: Ta reguła jest wyzwalana, jeśli Brama nie zgłosi do chmury przez długi czas. Brama może nie odpowiadać z powodu niskiej baterii, utraty łączności lub uszkodzenia urządzenia.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-rules.png" alt-text="Definicje reguł":::

### <a name="jobs"></a>Stanowiska

Wybierz kartę **zadania** , aby wyświetlić zadania w tej aplikacji:

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-jobs.png" alt-text="Zadania do uruchomienia":::

Zadań można użyć do wykonywania operacji na poziomie aplikacji. Zadania w tej aplikacji wykorzystują polecenia i funkcje przędzy do wykonywania zadań, takich jak wyłączenie określonych czujników na wszystkich bramach lub modyfikowanie progu czujnika w zależności od trybu wysyłki i trasy:

* Jest to standardowa operacja, która umożliwia wyłączenie czujników wstrząsów podczas wysyłki z oceanu w celu zachowania progu baterii lub dolnej temperatury podczas transportowania zimnego łańcucha.

* Zadania umożliwiają wykonywanie operacji na całym systemie, takich jak aktualizowanie oprogramowania układowego na bramach lub aktualizowanie kontraktu usługi, aby zachować bieżące informacje o działaniach konserwacyjnych.

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji, odwiedzając   >  **Ustawienia aplikacji** Administracja i wybierz pozycję **Usuń**.

:::image type="content" source="media/tutorial-iot-central-connected-logistics/connected-logistics-cleanup.png" alt-text="Czyszczenie szablonu":::

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej o 
> [!div class="nextstepaction"]
> [Połączona koncepcja logistyczna](./architecture-connected-logistics.md)
* Dowiedz się więcej na temat innych [szablonów detalicznych IoT Central](./overview-iot-central-retail.md)
* Dowiedz się więcej o [IoT Central przegląd](../core/overview-iot-central.md)
