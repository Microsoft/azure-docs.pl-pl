---
title: Korzystanie z pulpitu nawigacyjnego połączonej fabryki — Azure | Microsoft Docs
description: W tym artykule opisano, jak używać funkcji pulpitu nawigacyjnego połączonej fabryki do monitorowania i zarządzania urządzeniami przemysłowymi IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 797bd97940aa49db87b4ca4dd96f140208cd69b6
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/25/2020
ms.locfileid: "96011221"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Korzystanie z funkcji na pulpicie nawigacyjnym akceleratora rozwiązania połączonej fabryki

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

[Wdrożenie rozwiązania w chmurze do zarządzania moimi urządzeniami przemysłowymi IoT —](quickstart-connected-factory-deploy.md) szybki start przedstawia sposób nawigowania po pulpicie nawigacyjnym i reagowanie na alarmy. Ten przewodnik zawiera informacje na temat dodatkowych funkcji pulpitu nawigacyjnego, których można użyć do monitorowania i zarządzania urządzeniami przemysłowymi IoT.

## <a name="apply-filters"></a>Stosowanie filtrów

Informacje wyświetlane na pulpicie nawigacyjnym można filtrować w panelu **lokalizacje fabryki** lub w panelu **alarmy** :

1. Kliknij ikonę **lejka**, aby wyświetlić listę dostępnych filtrów na panelu lokalizacji fabryk lub panelu alarmów.

1. Zostanie wyświetlony panel Filtry:

    [![Filtry akceleratora rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Wybierz wymagany filtr, a następnie kliknij przycisk **Zastosuj**. Można również wpisać dowolny tekst w polach filtru.

1. Filtr zostanie następnie zastosowany. Dodatkowa ikona lejka wskazuje, że filtr jest stosowany:

    [![Zastosowano filtr akceleratora rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Aktywny filtr nie ma wpływu na wyświetlane wartości OEE i kluczowego wskaźnika wydajności, tylko filtruje zawartość listy.

1. Aby wyczyścić filtr, kliknij lejek, a następnie kliknij przycisk **Wyczyść** w panelu filtru.

## <a name="browse-an-opc-ua-server"></a>Przeglądanie serwera OPC UA

Podczas wdrażania akceleratora rozwiązań automatycznie jest inicjowany zestaw symulowanych serwerów OPC UA, które można przeglądać z poziomu pulpitu nawigacyjnego. Symulowane serwery ułatwiają eksperymentowanie z akceleratorem rozwiązań bez konieczności wdrażania prawdziwych serwerów.

1. Kliknij **ikonę przeglądarki** na pasku nawigacyjnym pulpitu nawigacyjnego:

    [![Przeglądarka serwerów w akceleratorze rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Wybierz z listy jeden z serwerów, który zawiera listę serwerów wdrożonych dla Ciebie w akceleratorze rozwiązania:

    [![Lista serwerów akceleratorów rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Kliknij przycisk **Połącz**. Zostanie wyświetlone okno dialogowe zabezpieczeń. W przypadku symulacji można bezpiecznie kliknąć przycisk **Przejdź**.

1. Aby rozwinąć dowolny węzeł w drzewie serwerów, kliknij go. Węzły, które publikują dane telemetryczne, mają znacznik wyboru obok nich:

    [![Drzewo serwerów w akceleratorze rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Kliknij element prawym przyciskiem myszy, aby odczytać, zapisać, opublikować lub wywołać ten węzeł. Dostępne akcje zależą od uprawnień posiadanych przez użytkownika i atrybutów węzła. Wybranie opcji odczytu powoduje wyświetlenie panelu kontekstowego z wartością konkretnego węzła. Wybranie opcji zapisu powoduje wyświetlenie panelu kontekstowego, w którym można wprowadzić nową wartość. Wybranie opcji wywołania powoduje wyświetlenie węzła i umożliwia wprowadzenie parametrów wywołania.

## <a name="publish-a-node"></a>Publikowanie węzła

Podczas przeglądania *symulowanego serwera OPC UA* można również opublikować nowe węzły. W rozwiązaniu można przeanalizować dane telemetryczne z tych węzłów. Te *symulowane serwery OPC UA* ułatwiają eksperymentowanie z akceleratorem rozwiązań bez wdrażania rzeczywistych urządzeń:

1. W drzewie przeglądarki serwerów OPC UA przejdź do węzła, który chcesz opublikować.

1. Kliknij węzeł prawym przyciskiem myszy. Kliknij przycisk **Publikuj**:

    [![Węzeł publikacji akceleratora rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Wyświetlony panel kontekstowy informuje, że publikowanie zakończyło się pomyślnie. Węzeł zostanie wyświetlony w widoku poziomu stacji z znacznikiem wyboru obok niego:

    [![Powodzenie publikowania akceleratora rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Sterowanie i kontrola

Połączona fabryka umożliwia sterowanie urządzeniami przemysłowymi i kontrolowanie ich bezpośrednio z chmury. Tej funkcji można używać do reagowania na alarmy generowane przez urządzenie. Na przykład można wysłać polecenie do urządzenia, aby otworzyć zawór zwolnienia ciśnieniowego. Dostępne polecenia można znaleźć w węźle **StationCommands** w drzewie przeglądarki serwerów OPC UA. W tym scenariuszu otwierasz zawór bezpieczeństwa na stanowisku montażowym linii produkcyjnej w Monachium. Aby korzystać z funkcji poleceń i kontroli, musisz mieć rolę **administratora** dla wdrożenia akceleratora rozwiązań:

1. Przejdź do węzła **StationCommands** w drzewie przeglądarki serwerów OPC UA dla wartości Monachium, wiersz produkcyjny 0, stacja zestawu.

1. Wybierz polecenie, którego chcesz użyć. Kliknij prawym przyciskiem myszy węzeł **OpenPressureReleaseValve** . Kliknij pozycję **Wywołaj**:

    [![Polecenie wywoływania w akceleratorze rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Zostanie wyświetlony panel kontekstowy z informacją o tym, która metoda zostanie wywołana, i dowolnych szczegółowych informacji o parametrach. Kliknij pozycję **Wywołaj**:

    [![Parametry wywołań akceleratora połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Panel kontekstowy zostanie zaktualizowany o informacje o powodzeniu wywołania metody. Powodzenie wywołania metody można sprawdzić, odczytując zaktualizowaną w wyniku wywołania wartość węzła ciśnienia.

    [![Powodzenie wywołania w akceleratorze rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Za kulisami

Podczas wdrażania akceleratora rozwiązania jest tworzonych wiele zasobów w ramach wybranej subskrypcji platformy Azure. Można je wyświetlić w witrynie Azure [Portal](https://portal.azure.com). W procesie wdrażania jest tworzona **grupa zasobów** o nazwie odpowiadającej nazwie akceleratora rozwiązania:

[![Grupa zasobów akceleratora rozwiązania połączonej fabryki](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Aby wyświetlić ustawienia danego zasobu, wybierz go z listy w grupie zasobów.

Możesz również wyświetlić kod źródłowy akceleratora rozwiązania w repozytorium [Azure-IoT-Connected-Factory](https://github.com/Azure/azure-iot-connected-factory) w serwisie GitHub.

Gdy skończysz, możesz usunąć Akcelerator rozwiązania z subskrypcji platformy Azure w witrynie [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) . Ta witryna umożliwia łatwe usunięcie wszystkich zasobów, które zostały aprowizowane po utworzeniu akceleratora rozwiązania.

> [!NOTE]
> Aby upewnić się, że usunięto wszystkie elementy związane z akceleratorem rozwiązania, usuń je w witrynie [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) . Nie należy usuwać grupy zasobów w portalu.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy został wdrożony akcelerator rozwiązania, który działa, możesz kontynuować poznawanie akceleratorów rozwiązań IoT, czytając następujące artykuły:

* [Skonfiguruj Akcelerator rozwiązania połączonej fabryki](iot-accelerators-connected-factory-configure.md)
* [Uprawnienia w witrynie azureiotsolutions.com](iot-accelerators-permissions.md)
