---
title: 'Samouczek: Tworzenie aplikacji monitorującej panel słoneczny przy użyciu usługi Azure IoT Central'
description: 'Samouczek: informacje na temat tworzenia aplikacji panelu słonecznego za pomocą szablonów aplikacji IoT Central platformy Azure.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 509e31919dd974da253cd0478a70f889cc060fae
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "99831794"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Samouczek: Tworzenie i eksplorowanie szablonu aplikacji monitorującej panel słoneczny 

Ten samouczek przeprowadzi Cię przez proces tworzenia aplikacji monitorującej panel słoneczny, która obejmuje przykładowy model urządzenia z symulowanymi danymi. Z tego samouczka dowiesz się, jak wykonywać następujące czynności:


> [!div class="checklist"]
> * Utwórz bezpłatnie aplikację panelu słonecznego
> * Przechodzenie przez aplikację
> * Czyszczenie zasobów


Jeśli nie masz subskrypcji, [Utwórz konto bezpłatnej wersji próbnej](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Wymagania wstępne

Nie ma wymagań wstępnych dotyczących wykonywania kroków tego samouczka. Subskrypcja platformy Azure jest zalecana, ale nie jest wymagana.


## <a name="create-a-solar-panel-monitoring-app"></a>Tworzenie aplikacji do monitorowania paneli słonecznych 

Tę aplikację można utworzyć w trzech prostych krokach:

1. Przejdź do [usługi Azure IoT Central](https://apps.azureiotcentral.com). Aby utworzyć nową aplikację, wybierz opcję **Kompiluj**. 

1. Wybierz kartę **energia** . W obszarze **monitorowanie panelu słonecznego** wybierz pozycję **Utwórz aplikację**. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu opcji kompilacji IoT Central platformy Azure.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. W oknie dialogowym **Nowa aplikacja** Wypełnij żądane szczegóły, a następnie wybierz pozycję **Utwórz**:
    * **Nazwa aplikacji**: wybierz nazwę aplikacji IoT Central platformy Azure. 
    * **Adres URL**: Wybierz adres URL usługi Azure IoT Central. Platforma weryfikuje swoją unikatowość.
    * **Plan cenowy**: Jeśli masz już subskrypcję platformy Azure, zalecane jest ustawienie domyślne. Jeśli nie masz subskrypcji platformy Azure, Zacznij od bezpłatnej wersji próbnej.
    * **Informacje o rozliczeniach**: sama aplikacja jest bezpłatna. Szczegóły katalogu, subskrypcji platformy Azure i regionu są wymagane do aprowizacji zasobów aplikacji.
        ![Zrzut ekranu przedstawiający nową aplikację.](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Zrzut ekranu przedstawiający informacje o rozliczeniach.](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Weryfikowanie aplikacji i danych symulowanych

W dowolnym momencie możesz zmodyfikować swoją nową aplikację w panelu. Na razie upewnij się, że aplikacja została wdrożona i działa zgodnie z oczekiwaniami przed jej zmodyfikowaniem.

Aby sprawdzić Tworzenie aplikacji i symulację danych, przejdź do **pulpitu nawigacyjnego**. Jeśli widzisz kafelki z niektórymi danymi, wdrożenie aplikacji zakończyło się pomyślnie. Generowanie danych może potrwać kilka minut. 

## <a name="application-walk-through"></a>Przewodnik po aplikacji
Po pomyślnym wdrożeniu szablonu aplikacji warto poznać nieco aplikację. Należy zauważyć, że zawiera on przykładowe inteligentne urządzenie pomiarowe, model urządzenia i pulpit nawigacyjny.

Adatum to fikcyjna firma energetyczna, która monitoruje panele słoneczne i zarządza nimi. Na pulpicie nawigacyjnym monitorowania panelu słonecznym widoczne są właściwości, dane i przykładowe polecenia panelu. Ten pulpit nawigacyjny umożliwia członkom zespołu pomocy technicznej wykonywanie następujących działań aktywnie, zanim wszystkie problemy wymagają dodatkowej pomocy technicznej:
* Zapoznaj się z najnowszymi informacjami na temat panelu i jego zainstalowaną lokalizacją na mapie.
* Sprawdź stan panelu i stan połączenia.
* Przejrzyj trendy generacji energii i temperatury, aby przechwycić wszystkie nietypowe wzorce.
* Śledź łączną generację energii na potrzeby planowania i rozliczeń.
* Aktywuj panel i zaktualizuj wersję oprogramowania układowego, w razie potrzeby. W szablonie przyciski poleceń pokazują możliwe funkcje i nie wysyłają prawdziwych poleceń.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pulpitu nawigacyjnego szablonu monitorowania panelu słonecznego.](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Urządzenia
Aplikacja jest dostępna z przykładowym urządzeniem panelu słonecznym. Aby wyświetlić szczegóły urządzenia, wybierz pozycję **urządzenia**.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający urządzenia szablonu monitorowania w panelu słonecznym.](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Wybierz urządzenie przykładowe, **SP0123456789**. Na karcie **Właściwości aktualizacji** można zaktualizować właściwości z możliwością zapisu urządzenia i wyświetlić wizualizację zaktualizowanych wartości na pulpicie nawigacyjnym. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający kartę właściwości aktualizacji szablonu monitorowania.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Szablon urządzenia
Aby wyświetlić model urządzenia panelu słonecznego, wybierz kartę **Szablony urządzeń** . Model ma wstępnie zdefiniowane interfejsy dla danych, właściwości, poleceń i widoków.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu szablonu monitorowanie szablonów urządzeń.](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Czyszczenie zasobów
Jeśli zdecydujesz się nie korzystać z tej aplikacji, Usuń aplikację, wykonując następujące czynności:

1. W okienku po lewej stronie wybierz pozycję **Administracja**.
1. Wybierz pozycję **Ustawienia aplikacji**  >  **Usuń**. 

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu administrowania szablonem monitorowania w panelu słonecznym.](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Następne kroki
 
> [!div class="nextstepaction"]
> [Azure IoT Central — architektura aplikacji w panelu słonecznym](./concept-iot-central-solar-panel-app.md)

