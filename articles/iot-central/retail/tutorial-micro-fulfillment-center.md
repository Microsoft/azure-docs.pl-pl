---
title: Samouczek szablonu aplikacji centrum Micro-zrealizować | Microsoft Docs
description: Samouczek dotyczący szablonu aplikacji centrum mikrorealizacji dla systemu Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 01/09/2020
ms.openlocfilehash: ab4d609b1638b47ad73b6f1143adee2870577305
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100390474"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Samouczek: wdrażanie i przechodzenie przez szablon aplikacji Centrum programu Micro-zrealizować

W tym samouczku zostanie użyty szablon aplikacji Centrum programu Azure IoT Central Micro-zrealizować, aby utworzyć rozwiązanie detaliczne.

W ramach tego samouczka nauczysz się:

> [!div class="checklist"]
> * Jak wdrożyć szablon aplikacji
> * Jak używać szablonu aplikacji

## <a name="prerequisites"></a>Wymagania wstępne
Do ukończenia tej serii samouczków potrzebna jest subskrypcja platformy Azure. Opcjonalnie możesz użyć bezpłatnej 7-dniowej wersji próbnej. Jeśli nie masz subskrypcji platformy Azure, możesz ją utworzyć na [stronie rejestracji na platformie Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Tworzenie aplikacji 
W tej sekcji utworzysz nową aplikację usługi Azure IoT Central na podstawie szablonu. Ta aplikacja zostanie użyta w całej serii samouczków, aby skompilować kompletne rozwiązanie.

Aby utworzyć nową aplikację usługi Azure IoT Central:

1. Przejdź do witryny sieci Web programu [Azure IoT Central Application Manager](https://aka.ms/iotcentral) .
1. Jeśli masz subskrypcję platformy Azure, zaloguj się przy użyciu poświadczeń, których używasz, aby uzyskać do niej dostęp. W przeciwnym razie zaloguj się przy użyciu konto Microsoft:

   ![Zrzut ekranu przedstawiający okno dialogowe logowania konto Microsoft](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Aby rozpocząć tworzenie nowej aplikacji usługi Azure IoT Central, wybierz pozycję **Nowa aplikacja**.

1. Wybierz pozycję **sprzedaż detaliczna**.  Na stronie detalicznej są wyświetlane kilka szablonów aplikacji sieci Web.

Aby utworzyć nową aplikację centrum o wysokiej realizacji, która korzysta z funkcji w wersji zapoznawczej:  
1. Wybierz szablon aplikacja **centrum mikrorealizacji** . Ten szablon zawiera szablony urządzeń dla wszystkich urządzeń używanych w samouczku. Szablon zawiera również pulpit nawigacyjny operatora do monitorowania warunków w centrum realizacji, a także warunki dla przewoźników zautomatyzowanych. 

    ![Zrzut ekranu przedstawiający platformę Azure IoT Central Kompilowanie strony aplikacji IoT](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Opcjonalnie możesz wybrać przyjazną **nazwę aplikacji**. Szablon aplikacji jest oparty na fikcyjnej firmie Northwind Traders. 

    >[!NOTE]
    >Jeśli używasz przyjaznej nazwy aplikacji, nadal musisz użyć unikatowej wartości dla adresu URL aplikacji.

1. Jeśli masz subskrypcję platformy Azure, wprowadź swój katalog, subskrypcję platformy Azure i region. Jeśli nie masz subskrypcji, możesz włączyć 7-dniową bezpłatną wersję próbną i uzupełnić wymagane informacje kontaktowe.  

    Aby uzyskać więcej informacji na temat katalogów i subskrypcji, zobacz [Tworzenie aplikacji](../core/quick-deploy-iot-central.md) — Szybki Start.

1. Wybierz przycisk **Utwórz**.

    ![Zrzut ekranu strony nowej aplikacji IoT Central platformy Azure](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Przechodzenie przez aplikację 

Po pomyślnym wdrożeniu szablonu aplikacji zobaczysz **pulpit nawigacyjny centrum danych Northwind Traders**. Northwind Traders to fikcyjny sprzedawca detaliczny, który ma centrum mikrorealizacji zarządzane w tej aplikacji IoT Central platformy Azure. Na tym pulpicie nawigacyjnym operatora widoczne są informacje i dane telemetryczne dotyczące urządzeń w tym szablonie, a także zestaw poleceń, zadań i akcji, które można wykonać. Pulpit nawigacyjny jest podzielony logicznie na dwie sekcje. Po lewej stronie można monitorować warunki środowiska w ramach struktury realizacji i po prawej stronie można monitorować kondycję operatora zautomatyzowanego w ramach tej funkcji.  

Z poziomu pulpitu nawigacyjnego można:
   * Zobacz dane telemetryczne urządzenia, takie jak Liczba pobrań, Liczba przetworzonych zamówień i właściwości, takie jak struktura stanu systemu.  
   * Zapoznaj się z planem pomieszczeń i lokalizacją przewoźników zautomatyzowanych w ramach struktury realizacji.
   * Wyzwalaj polecenia, takie jak resetowanie systemu kontroli, aktualizowanie oprogramowania układowego operatora i ponowne konfigurowanie sieci.

     ![Zrzut ekranu przedstawiający górną połowę pulpitu nawigacyjnego centrum danych Northwind Traders.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Zapoznaj się z przykładem pulpitu nawigacyjnego, którego operator może użyć do monitorowania warunków w centrum realizacji. 
   * Monitoruj kondycję ładunków, które są uruchomione na urządzeniu bramy w centrum realizacji.    

     ![Zrzut ekranu przedstawiający połowę bottonu pulpitu nawigacyjnego centrum danych Northwind Traders.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Szablon urządzenia
W przypadku wybrania karty szablony urządzeń widoczne są dwa różne typy urządzeń, które są częścią szablonu: 
   * **Przewoźnik zautomatyzowany**: ten szablon urządzenia reprezentuje definicję działającego operatora zautomatyzowanego, który został wdrożony w strukturze realizacji, i wykonuje odpowiednie operacje związane z magazynowaniem i pobieraniem. Jeśli wybierzesz szablon, zobaczysz, że robot wysyła dane urządzenia, takie jak położenie temperatury i osi oraz właściwości, takie jak stan nośnej. 
   * **Monitorowanie warunków struktury**: ten szablon urządzenia reprezentuje kolekcję urządzeń, która pozwala monitorować warunek środowiska, a także urządzenie bramy obsługujące różne obciążenia brzegowe umożliwiające zarządzanie centrum realizacji. Urządzenie wysyła dane telemetryczne, takie jak temperatura, Liczba pobrań i liczba zamówień. Wysyła również informacje o stanie i kondycji obciążeń obliczeniowych uruchomionych w danym środowisku. 

     ![Szablony urządzeń centrum Micro-zrealizować](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Jeśli wybierzesz kartę grupy urządzeń, zobaczysz również, że te szablony urządzeń automatycznie mają utworzone grupy urządzeń.

## <a name="rules"></a>Reguły
Na karcie **reguły** zostanie wyświetlona Przykładowa reguła, która istnieje w szablonie aplikacji, aby monitorować warunki temperatury dla operatora zautomatyzowanego. Ta reguła może być używana do powiadamiania operatora o przepełnieniu konkretnej robotu i musi zostać przełączona w tryb offline w celu obsługi. 

Użyj przykładowej reguły jako inspiracji, aby zdefiniować reguły, które są bardziej odpowiednie dla funkcji firmy.

![Zrzut ekranu przedstawiający kartę reguły](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli nie chcesz nadal korzystać z tej aplikacji, Usuń szablon aplikacji. Przejdź do pozycji **Administracja**  >  **Ustawienia aplikacji**, a następnie wybierz pozycję **Usuń**.

![Zrzut ekranu przedstawiający stronę ustawień aplikacji Centrum programu Micro-zrealizować](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na następujące tematy:

> [!div class="nextstepaction"]
> [Architektura rozwiązania Centrum rozwiązań](./architecture-micro-fulfillment-center.md)
