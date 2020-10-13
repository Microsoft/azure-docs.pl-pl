---
title: Samouczek — Tworzenie ciągłej aplikacji do monitorowania pacjenta przy użyciu usługi Azure IoT Central | Microsoft Docs
description: W ramach tego samouczka nauczysz się tworzyć ciągłe aplikacje do monitorowania pacjenta przy użyciu szablonów aplikacji platformy Azure IoT Central.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 1967a2fb5adebe01ef4bff8d58f7832bffe95762
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90531274"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Samouczek: wdrażanie i Instruktaż szablonu aplikacji do monitorowania ciągłego pacjenta

W tym samouczku pokazano, jak uzyskać Konstruktor rozwiązań, jak rozpocząć pracę, wdrażając szablon aplikacji do monitorowania ciągłego pacjenta IoT Central. Dowiesz się, jak wdrożyć szablon i korzystać z niego.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie szablonu aplikacji
> * Przechodzenie przez szablon aplikacji

## <a name="create-an-application-template"></a>Tworzenie szablonu aplikacji

Przejdź do [witryny sieci Web programu Azure IoT Central Application Manager](https://apps.azureiotcentral.com/). Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie wybierz kartę **opieka medyczna** .

:::image type="content" source="media/app-manager-health.png" alt-text="Szablon aplikacji Healthcre":::

Wybierz przycisk **Utwórz aplikację** , aby rozpocząć tworzenie aplikacji, a następnie zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft. Spowoduje to przejście do strony **nowej aplikacji** .

![Tworzenie opieki zdrowotnej aplikacji](media/app-manager-health-create.png)

![Utwórz informacje dotyczące rozliczeń w usłudze Application opieka](media/app-manager-health-create-billinginfo.png)

Aby utworzyć aplikację:

1. Usługa Azure IoT Central automatycznie sugeruje nazwę aplikacji w oparciu o wybrany szablon. Możesz zaakceptować tę nazwę lub wprowadzić własną przyjazną nazwę aplikacji, na przykład **ciągłe monitorowanie pacjenta**. Usługa Azure IoT Central generuje również unikatowy prefiks adresu URL na podstawie nazwy aplikacji. Jeśli chcesz, możesz zmienić ten prefiks adresu URL na bardziej zapamiętanie.

2. Możesz wybrać, czy chcesz utworzyć aplikację, korzystając z *bezpłatnego* planu cenowego, czy też jednego z *standardowych* planów cenowych. Aplikacje tworzone za pomocą planu bezpłatnego są bezpłatne przez siedem dni przed ich wygaśnięciem i zezwalają na maksymalnie pięć bezpłatnych urządzeń. Możesz przenieść aplikację z planu bezpłatnego do standardowego planu cen w dowolnym momencie przed jego wygaśnięciem. Jeśli wybierzesz plan bezpłatny, musisz wprowadzić swoje informacje kontaktowe i zdecydować, czy otrzymywać informacje i porady od firmy Microsoft. Aplikacje tworzone przy użyciu planu standardowego obsługują do dwóch bezpłatnych urządzeń i wymagają wprowadzenia informacji o subskrypcji platformy Azure na potrzeby rozliczania.

3. Wybierz pozycję **Utwórz** w dolnej części strony, aby wdrożyć aplikację.

## <a name="walk-through-the-application-template"></a>Przechodzenie przez szablon aplikacji

### <a name="dashboards"></a>Pulpity nawigacyjne

Po wdrożeniu szablonu aplikacji należy najpierw **obstawić pulpit nawigacyjny monitorowania Lamna**. Lamna opieka medyczna to fikcyjny system szpitaly, który zawiera dwie szpitale: Szpital szpital i Burkville. Na pulpicie nawigacyjnym operatora szpitala Woodgrove można:

* Zobacz dane telemetryczne i właściwości urządzenia, takie jak **poziom baterii** urządzenia lub stan **łączności** .

* Wyświetlanie **planu pomieszczeń** i lokalizacji urządzenia z poprawkami inteligentnymi.

* Ponownie **Udostępnij** poprawkę inteligentnych cech dla nowego pacjenta.

* Zapoznaj się z przykładem **pulpitu nawigacyjnego dostawcy** , który może zobaczyć zespół opieki szpitalowej w celu śledzenia ich pacjentów.

* Zmień **stan pacjenta** urządzenia, aby wskazać, czy urządzenie jest używane dla pacjenta lub zdalnego scenariusza.

:::image type="content" source="media/lamna-in-patient.png" alt-text="Szablon aplikacji Healthcre":::

Możesz również wybrać pozycję **Przejdź do pulpitu nawigacyjnego zdalnego pacjenta** , aby wyświetlić pulpit nawigacyjny operatora Burkville szpital. Ten pulpit nawigacyjny zawiera podobny zestaw akcji, telemetrii i informacji. Można także zobaczyć wiele urządzeń w użyciu i wybrać **aktualizację oprogramowania układowego** na każdym z nich.

:::image type="content" source="media/lamna-remote.png" alt-text="Szablon aplikacji Healthcre":::

### <a name="device-templates"></a>Szablony urządzeń

Jeśli wybierzesz pozycję **Szablony urządzeń**, zobaczysz dwa typy urządzeń w szablonie:

* **Poprawka inteligentnych elementów istotnych**: to urządzenie reprezentuje poprawkę, która mierzy różne istotne oznaki. Służy do monitorowania pacjentów w i poza szpitalem. Jeśli wybierzesz szablon, zobaczysz, że poprawka wysyła zarówno dane dotyczące urządzenia, jak i temperatury baterii oraz dane dotyczące kondycji pacjenta, takie jak szybkość oddechu i nacisk krwi.

* **Inteligentne nawiasy klamrowe**: to urządzenie reprezentuje nawiasy klamrowe używane podczas odzyskiwania z chirurgu do zastępowania kolan. Jeśli wybierzesz ten szablon, zobaczysz możliwości, takie jak dane urządzenia, zakres ruchu i przyspieszenie.

:::image type="content" source="media/smart-vitals-device-template.png" alt-text="Szablon aplikacji Healthcre":::

### <a name="device-groups"></a>Grupy urządzeń

Grupy urządzeń służą do logicznego grupowania zestawu urządzeń, a następnie uruchamiania zapytań zbiorczych lub operacji na nich.

W przypadku wybrania karty grupy urządzeń zostanie wyświetlona domyślna grupa urządzeń dla każdego szablonu urządzenia w aplikacji. Utworzono również dwie dodatkowe przykładowe grupy urządzeń o nazwie **Inicjowanie urządzeń** i **urządzeń z nieaktualnym oprogramowaniem układowym**. Tych przykładowych grup urządzeń można użyć jako danych wejściowych do uruchamiania niektórych [zadań](#jobs) w aplikacji.

### <a name="rules"></a>Reguły

Jeśli wybierzesz pozycję **reguły**, zobaczysz trzy reguły w szablonie:

* **Temperatura nawiasu klamrowego wysoka**: Ta zasada wyzwala, gdy temperatura urządzenia w nawiasach klamrowych jest większa niż 95 &deg; F w przypadku 5-minutowego okna. Ta reguła umożliwia wygenerowanie alertów dla pacjenta i zespołu opieki oraz zdalne chłodzenie urządzenia.

* **Wykryto spadek**: Ta reguła jest wyzwalana w przypadku wykrycia pacjenta. Ta reguła służy do konfigurowania akcji do wdrożenia zespołu operacyjnego w celu ułatwienia pacjentowi, który spadł.

* **Niski poziom baterii poprawek**: Ta reguła jest wyzwalana, gdy poziom baterii urządzenia spadnie poniżej 10%. Użyj tej reguły, aby wyzwolić powiadomienie dla pacjenta o naliczeniu urządzenia.

:::image type="content" source="media/brace-temp-rule.png" alt-text="Szablon aplikacji Healthcre":::

### <a name="jobs"></a>Stanowiska

Zadania umożliwiają uruchamianie operacji zbiorczych na zestawie urządzeń przy użyciu [grup urządzeń](#device-groups) jako danych wejściowych. Szablon aplikacji zawiera dwa przykładowe zadania, które mogą być uruchamiane przez operatora:

* **Aktualizowanie oprogramowania układowego w nawiasach klamrowych**: to zadanie umożliwia znalezienie urządzeń w grupie urządzeń **z nieaktualnym oprogramowaniem układowym** i uruchomienie polecenia w celu zaktualizowania tych urządzeń do najnowszej wersji oprogramowania układowego. W tym przykładowym zadaniu założono, że urządzenia mogą obsługiwać polecenie **Update** , a następnie pobierać pliki oprogramowania układowego z chmury.  

* **Ponowne Inicjowanie obsługi administracyjnej urządzeń**: masz zestaw urządzeń, które zostały ostatnio zwrócone do szpitala. To zadanie umożliwia odnalezienie urządzeń w **grupie urządzeń** i uruchomienie polecenia, aby ponownie zainicjować obsługę administracyjną dla następnego zestawu pacjentów.

### <a name="devices"></a>Devices

Wybierz kartę **urządzenia** , a następnie wybierz wystąpienie **inteligentnego nawiasu kolanowego**. Istnieją trzy widoki umożliwiające Eksplorowanie informacji o konkretnym wybranym urządzeniu. Te widoki są tworzone i publikowane podczas kompilowania szablonu urządzenia dla urządzenia. w związku z tym te widoki są spójne na wszystkich urządzeniach, które są połączone lub symulowane.

Widok **pulpitu nawigacyjnego** zawiera przegląd danych telemetrycznych i właściwości związanych z operatorem z urządzenia.

Karta **Właściwości** umożliwia edytowanie właściwości chmury i właściwości urządzenia do odczytu i zapisu.

Karta **polecenia** umożliwia uruchamianie poleceń na urządzeniu.

:::image type="content" source="media/knee-brace-dashboard.png" alt-text="Szablon aplikacji Healthcre":::

### <a name="data-export"></a>Eksportowanie danych

Eksport danych umożliwia ciągłe eksportowanie danych urządzenia do innych usług platformy Azure, w tym [interfejsu API platformy Azure dla usługi FHIR](concept-continuous-patient-monitoring-architecture.md#export-to-azure-api-for-fhir).

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń aplikację, odwiedzając pozycję **administracja > ustawienia aplikacji** , a następnie kliknij przycisk **Usuń**.

:::image type="content" source="media/admin-delete.png" alt-text="Szablon aplikacji Healthcre":::

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć pulpit nawigacyjny dostawcy łączący się z aplikacją IoT Central.

> [!div class="nextstepaction"]
> [Tworzenie pulpitu nawigacyjnego dostawcy](howto-health-data-triage.md)