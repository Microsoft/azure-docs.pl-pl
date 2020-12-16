---
title: 'Samouczek: Tworzenie aplikacji inteligentnego pomiaru analizy przy użyciu IoT Central'
description: 'Samouczek: informacje na temat tworzenia inteligentnego monitorowania miernika przy użyciu szablonów aplikacji IoT Central platformy Azure.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: a95f1befcdc9b59293eb97330ba7ceae3034a74b
ms.sourcegitcommit: 66479d7e55449b78ee587df14babb6321f7d1757
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/15/2020
ms.locfileid: "97516778"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Samouczek: Tworzenie i przechodzenie przez szablon aplikacji do monitorowania mierników inteligentnych 

Ten samouczek przeprowadzi Cię przez proces tworzenia aplikacji do monitorowania mierników inteligentnych, która obejmuje przykładowy model urządzenia z symulowanymi danymi. Niniejszy samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Utwórz bezpłatnie aplikację miernika inteligentnego
> * Przewodnik po aplikacji
> * Czyszczenie zasobów


Jeśli nie masz subskrypcji, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Wymagania wstępne
- Brak
- Zalecana jest subskrypcja platformy Azure, ale nie jest wymagana do wypróbowania

## <a name="create-a-smart-meter-monitoring-app"></a>Tworzenie aplikacji do monitorowania inteligentnych liczników 

Tę aplikację można utworzyć w trzech prostych krokach:

1. Otwórz [stronę główną usługi Azure IoT Central](https://apps.azureiotcentral.com) i kliknij przycisk **Kompiluj** , aby utworzyć nową aplikację. 
1. Wybierz kartę **energia** , a następnie kliknij pozycję **Utwórz aplikację** na kafelku aplikacji **inteligentnego monitorowania miernika** .

    > [!div class="mx-imgBorder"]
    > ![Kompiluj aplikację](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

1. **Utworzenie aplikacji** spowoduje otwarcie formularza **nowej aplikacji** . Wypełnij żądane szczegóły, jak pokazano na poniższej ilustracji:
    * **Nazwa aplikacji**: wybierz nazwę aplikacji IoT Central. 
    * **Adres URL**: Wybierz adres URL IoT Central, platforma sprawdzi swoją unikatowość.
    * **7-dniowa bezpłatna wersja próbna**: Jeśli masz już subskrypcję platformy Azure, zalecane jest ustawienie domyślne. Jeśli nie masz subskrypcji platformy Azure, Zacznij od bezpłatnej wersji próbnej.
    * **Informacje o rozliczeniach**: sama aplikacja jest bezpłatna. Szczegóły katalogu, subskrypcji platformy Azure i regionu są wymagane do aprowizacji zasobów aplikacji.
    * Kliknij przycisk **Utwórz** w dolnej części strony, a Twoja aplikacja zostanie utworzona na minutę lub w ten sposób.

        ![Formularz Nowa aplikacja](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Nowe informacje o rozliczeniu formularza aplikacji](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)

### <a name="verify-the-application-and-simulated-data"></a>Weryfikowanie aplikacji i danych symulowanych

Nowo utworzona aplikacja inteligentnego miernika jest aplikacją i można ją zmodyfikować w dowolnym momencie. Upewnij się, że aplikacja została wdrożona i działa zgodnie z oczekiwaniami przed jej zmodyfikowaniem.

Aby sprawdzić Tworzenie aplikacji i symulację danych, przejdź do **pulpitu nawigacyjnego**. Jeśli widzisz kafelki z niektórymi danymi, wdrożenie aplikacji zakończyło się pomyślnie. Generowanie danych przez symulację danych może potrwać kilka minut, więc Zapewnij 1-2 minut. 

## <a name="application-walk-through"></a>Przewodnik po aplikacji
Po pomyślnym wdrożeniu szablonu aplikacji jest on dostępny przy użyciu przykładowego urządzenia miernika inteligentnego, modelu urządzenia i pulpitu nawigacyjnego. 

Adatum to fikcyjna firma energetyczna, która monitoruje i zarządza inteligentnymi miernikami. Na pulpicie nawigacyjnym monitorowania mierników inteligentnych widoczne są właściwości, dane i przykładowe polecenia inteligentnego miernika. Umożliwia operatorom i zespołom pomocy technicznej przeprowadzenie aktywnego wykonywania następujących działań przed uwzględnieniem zdarzeń pomocy technicznej: 
* Przejrzyj najnowsze informacje o mierniku i jego zainstalowaną lokalizację na mapie
* Proaktywne sprawdzenie stanu sieci i połączenia 
* Monitoruj minimalne i maksymalne odczyty napięcia dla kondycji sieci 
* Przejrzyj trendy dotyczące energii, zasilania i napięcia, aby przechwytywać nietypowe wzorce 
* Śledź całkowite zużycie energii na potrzeby planowania i rozliczeń
* Operacje związane z poleceniami i kontrolami, takie jak ponowne łączenie licznika i aktualizacja wersji oprogramowania układowego. W szablonie przyciski poleceń pokazują możliwe funkcje i nie wysyłają prawdziwych poleceń. 

> [!div class="mx-imgBorder"]
> ![Pulpit nawigacyjny monitorowania mierników inteligentnych](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Urządzenia
Aplikacja jest dostarczana z przykładowym urządzeniem miernika inteligentnego. Aby wyświetlić szczegóły urządzenia, kliknij kartę **urządzenia** .

> [!div class="mx-imgBorder"]
> ![Urządzenia mierników inteligentnych](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Kliknij link przykładowe urządzenie **SM0123456789** , aby wyświetlić szczegóły urządzenia. Możesz zaktualizować właściwości z możliwością zapisu urządzenia na stronie **Właściwości aktualizacji** i wizualizować zaktualizowane wartości na pulpicie nawigacyjnym.

> [!div class="mx-imgBorder"]
> ![Właściwości miernika inteligentnego](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Szablon urządzenia
Kliknij kartę **Szablony urządzeń** , aby wyświetlić model urządzenia miernika inteligentnego. Model zawiera wstępnie zdefiniowane interfejsy dla danych, właściwości, poleceń i widoków.

> [!div class="mx-imgBorder"]
> ![Szablony urządzeń mierników inteligentnych](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli zdecydujesz, aby nie używać tej aplikacji, Usuń aplikację, wykonując następujące czynności:

1. W okienku po lewej stronie Otwórz kartę Administracja
1. Wybierz pozycję Ustawienia aplikacji, a następnie kliknij przycisk Usuń u dołu strony. 

    > [!div class="mx-imgBorder"]
    > ![Usuń aplikację](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o architekturze aplikacji mierników inteligentnych odwołują się do 
> [!div class="nextstepaction"]
> [Artykuł koncepcji](./concept-iot-central-smart-meter-app.md)
* Twórz bezpłatnie szablony aplikacji mierników inteligentnych: [inteligentnego pomiaru aplikacji](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Dowiedz się więcej na temat IoT Central, zobacz [omówienie IoT Central](../index.yml)
