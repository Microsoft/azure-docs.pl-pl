---
title: Tworzenie ciągłej aplikacji do monitorowania pacjenta przy użyciu usługi Azure IoT Central | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację do monitorowania ciągłego pacjenta przy użyciu szablonów aplikacji IoT Central platformy Azure.
author: philmea
ms.author: philmea
ms.date: 09/24/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: eliotgra
ms.openlocfilehash: 35ac39109bfcb4dc63b738c947d2ad8caf8ac0a6
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "77021291"
---
# <a name="tutorial-deploy-and-walkthrough-a-continuous-patient-monitoring-app-template"></a>Samouczek: wdrażanie i Instruktaż szablonu aplikacji do monitorowania ciągłego pacjenta



W tym samouczku pokazano, jak uzyskać Konstruktor rozwiązań, jak rozpocząć pracę, wdrażając szablon aplikacji do monitorowania ciągłego pacjenta IoT Central. Dowiesz się, jak wdrożyć szablon, co jest dołączone do pola i co możesz zrobić dalej.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie szablonu aplikacji
> * Przechodzenie przez szablon aplikacji

## <a name="create-an-application-template"></a>Tworzenie szablonu aplikacji

Przejdź do [witryny sieci Web programu Azure IoT Central Application Manager](https://apps.azureiotcentral.com/). Wybierz pozycję **kompilacja** na pasku nawigacyjnym po lewej stronie, a następnie kliknij kartę **opieka medyczna** . 

>[!div class="mx-imgBorder"] 
>![Opieka zdrowotna programu App Manager](media/app-manager-health.png)

Kliknij przycisk **Utwórz aplikację** , aby rozpocząć tworzenie aplikacji, a następnie zaloguj się przy użyciu konta osobistego, służbowego lub szkolnego firmy Microsoft. Spowoduje to przejście do strony **nowej aplikacji** .

![Tworzenie opieki zdrowotnej aplikacji](media/app-manager-health-create.png)

![Utwórz informacje dotyczące rozliczeń w usłudze Application opieka](media/app-manager-health-create-billinginfo.png)

Aby utworzyć aplikację:

1. Usługa Azure IoT Central automatycznie sugeruje nazwę aplikacji w oparciu o wybrany szablon. Możesz zaakceptować tę nazwę lub wprowadzić własną przyjazną nazwę aplikacji, na przykład **ciągłe monitorowanie pacjenta**. Usługa Azure IoT Central generuje również unikatowy prefiks adresu URL na podstawie nazwy aplikacji. Jeśli chcesz, możesz zmienić ten prefiks adresu URL na bardziej zapamiętanie.

2. Możesz wybrać, czy chcesz utworzyć aplikację, korzystając z *bezpłatnego* planu cenowego, czy też jednego z *standardowych* planów cenowych. Aplikacje tworzone za pomocą planu bezpłatnego są bezpłatne przez siedem dni przed ich wygaśnięciem i zezwalają na maksymalnie pięć bezpłatnych urządzeń. Możesz przenieść aplikację z planu bezpłatnego do standardowego planu cen w dowolnym momencie przed jego wygaśnięciem. Jeśli wybierzesz plan bezpłatny, musisz wprowadzić swoje informacje kontaktowe i zdecydować, czy otrzymywać informacje i porady od firmy Microsoft. Aplikacje tworzone przy użyciu planu standardowego obsługują do dwóch bezpłatnych urządzeń i wymagają wprowadzenia informacji o subskrypcji platformy Azure na potrzeby rozliczania.

3. Wybierz pozycję **Utwórz** w dolnej części strony, aby wdrożyć aplikację.

## <a name="walk-through-the-application-template"></a>Przechodzenie przez szablon aplikacji

### <a name="dashboards"></a>Pulpity nawigacyjne

Po wdrożeniu szablonu aplikacji należy najpierw **obstawić pulpit nawigacyjny monitorowania Lamna**. Lamna opieka medyczna to fikcyjny system szpitaly, który zawiera dwie szpitale: Szpital szpital i Burkville. Na tym pulpicie nawigacyjnym operatora dla szpitala Woodgrove zobaczysz informacje i dane telemetryczne dotyczące urządzeń w tym szablonie wraz z zestawem poleceń, zadań i akcji, które można wykonać. Na pulpicie nawigacyjnym możesz:

* Zobacz dane telemetryczne i właściwości urządzenia, takie jak **poziom baterii** urządzenia lub stan **łączności** .

* Wyświetlanie **planu pomieszczeń** i lokalizacji urządzenia z poprawkami inteligentnymi.

* Ponownie **Udostępnij** poprawkę inteligentnych cech dla nowego pacjenta.

* Zapoznaj się z przykładem **pulpitu nawigacyjnego dostawcy** , który może zobaczyć zespół opieki szpitalowej w celu śledzenia ich pacjentów.

* Zmień **stan pacjenta** urządzenia, aby wskazać, czy urządzenie jest używane dla pacjenta lub zdalnego scenariusza.

>[!div class="mx-imgBorder"] 
>![Lamna](media/lamna-in-patient.png)

Możesz również kliknąć przycisk **Przejdź do pulpitu nawigacyjnego zdalnego pacjenta** , aby zobaczyć drugi pulpit nawigacyjny operatora używany na potrzeby szpitala Burkville. Ten pulpit nawigacyjny zawiera podobny zestaw akcji, telemetrii i informacji. Ponadto można zobaczyć wiele używanych urządzeń i mieć możliwość **aktualizowania oprogramowania układowego** na każdym z nich.

>[!div class="mx-imgBorder"] 
>![Lamna zdalnego](media/lamna-remote.png)

Na obu pulpitach nawigacyjnych zawsze możesz połączyć się z tą dokumentacją.

### <a name="device-templates"></a>Szablony urządzeń

Po kliknięciu karty **Szablony urządzeń** zobaczysz, że istnieją dwa różne typy urządzeń, które są częścią szablonu:

* **Poprawka inteligentnych elementów istotnych**: to urządzenie reprezentuje poprawkę, która mierzy różne typy znaków istotnych. Może służyć do monitorowania pacjentów w i poza szpitalem. Jeśli klikniesz szablon, zobaczysz, że oprócz wysyłania danych urządzenia, takich jak poziom baterii i temperatura urządzenia, Poprawka również wysyła dane dotyczące kondycji pacjenta, takie jak szybkość układu oddechowego i nacisk krwi.

* **Inteligentny nawiasy klamrowe**: to urządzenie reprezentuje nawiasy klamrowe, które mogą być używane podczas odzyskiwania z chirurgu do zastępowania kolan. Po kliknięciu tego szablonu zobaczysz możliwości, takie jak zakres ruchu i przyspieszanie, oprócz danych urządzenia.

>[!div class="mx-imgBorder"] 
>![Szablon urządzenia z poprawkami inteligentnymi](media/smart-vitals-device-template.png)

Jeśli klikniesz kartę **grupy urządzeń** , zobaczysz również, że te szablony urządzeń automatycznie mają utworzone grupy urządzeń.

### <a name="rules"></a>Reguły

Gdy przejdziesz do karty reguły, zobaczysz trzy reguły, które istnieją w szablonie aplikacji:

* **Temperatura nawiasu klamrowego**: Ta reguła jest wyzwalana, gdy temperatura urządzenia w nawiasach klamrowych jest większa niż&deg;95 F w przypadku 5-minutowego okna. Ta reguła może być używana do powiadamiania pacjenta i zespołu opieki i zdalnego urządzenia.

* **Wykryto spadek**: Ta reguła jest wyzwalana w przypadku wykrycia pacjenta. Za pomocą tej reguły można skonfigurować akcję do wdrożenia zespołu operacyjnego, aby pomóc pacjenta, który spadł.

* **Niski poziom baterii poprawek**: Ta reguła jest wyzwalana, gdy poziom baterii urządzenia spadnie poniżej 10%. Możesz użyć tej reguły, aby wyzwolić powiadomienie dla pacjenta, aby obciążać swoje urządzenie.

>[!div class="mx-imgBorder"] 
>![Wysoka reguła temperatury nawiasów klamrowych](media/brace-temp-rule.png)

### <a name="devices"></a>Urządzenia

Kliknij kartę **urządzenia** , a następnie wybierz wystąpienie **inteligentnego nawiasu kolanowego**. Zobaczysz, że istnieją trzy widoki umożliwiające Eksplorowanie informacji o konkretnym wybranym urządzeniu. Te widoki są tworzone i publikowane podczas kompilowania szablonu urządzenia dla urządzenia, co oznacza, że będą one spójne na wszystkich urządzeniach, które są połączone lub symulowane.

Widok **pulpitu nawigacyjnego** zawiera przegląd danych telemetrycznych i właściwości, które pochodzą z urządzenia, które są zorientowane na operator.

Karta **Właściwości** umożliwia edytowanie właściwości chmury oraz właściwości urządzeń do odczytu i zapisu.

Karta **polecenia** umożliwia uruchamianie poleceń, które zostały modelowane jako część szablonu urządzenia.

>[!div class="mx-imgBorder"] 
>![Widoki w nawiasach klamrowych](media/knee-brace-dashboard.png)

## <a name="clean-up-resources"></a>Oczyszczanie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń aplikację, odwiedzając pozycję **administracja > ustawienia aplikacji** , a następnie kliknij przycisk **Usuń**.

>[!div class="mx-imgBorder"] 
>![Usuń aplikację](media/admin-delete.png)

## <a name="next-steps"></a>Następne kroki

Przejdź do następnego artykułu, aby dowiedzieć się, jak utworzyć pulpit nawigacyjny dostawcy łączący się z aplikacją IoT Central.

> [!div class="nextstepaction"]
> [Tworzenie pulpitu nawigacyjnego dostawcy](howto-health-data-triage.md)