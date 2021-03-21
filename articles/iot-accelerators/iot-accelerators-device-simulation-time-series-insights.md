---
title: Wizualizacja symulowanych danych telemetrycznych za pomocą Time Series Insights — Azure | Microsoft Docs
description: Dowiedz się, jak skonfigurować środowisko Time Series Insights do eksplorowania i analizowania danych telemetrycznych generowanych przez Akcelerator rozwiązania do symulacji urządzeń.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: e1409b43f0ce1fc0d8c622dda79e857ac6abdd33
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96854567"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Użyj Time Series Insights, aby wizualizować dane telemetryczne wysyłane z akceleratora rozwiązania do symulacji urządzenia

Akcelerator rozwiązania do symulacji urządzeń umożliwia generowanie danych telemetrycznych z symulowanych urządzeń do testowania rozwiązań IoT. Ten przewodnik przedstawia sposób wizualizacji i analizowania symulowanych danych telemetrycznych przy użyciu środowiska Time Series Insightsowego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz mieć aktywną subskrypcję platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

Kroki opisane w tym przewodniku założono, że wdrożono Akcelerator rozwiązania dla symulacji urządzenia w ramach subskrypcji platformy Azure. Jeśli nie wdrożono jeszcze symulacji urządzenia, zobacz [wdrażanie symulacji urządzenia](https://github.com/Azure/device-simulation-dotnet/blob/master/README.md) w serwisie GitHub.

W tym artykule założono, że nazwa akceleratora rozwiązania to **contoso-symulacja**. Zastąp **symulację firmy Contoso** nazwą akceleratora rozwiązania, wykonując następujące kroki.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Aby przesyłać dane telemetryczne do Time Series Insights, należy utworzyć dedykowaną grupę odbiorców w centrum IoT Hub. Źródło zdarzenia w Time Series Insights powinno mieć wyłączne korzystanie z IoT Hub grupy odbiorców.

Poniższe kroki używają interfejsu wiersza polecenia platformy Azure w Azure Cloud Shell, aby utworzyć grupę konsumentów:

1. Centrum IoT jest jednym z kilku zasobów utworzonych podczas wdrażania akceleratora rozwiązania do symulacji urządzeń. Wykonaj następujące polecenie, aby znaleźć nazwę Centrum IoT Hub — Pamiętaj, aby użyć nazwy akceleratora rozwiązania:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Centrum IoT jest zasobem typu **Microsoft. Devices/IotHubs**.

1. Dodaj grupę odbiorców o nazwie **devicesimulationtsi** do centrum. W poniższym poleceniu użyj nazwy Twojego koncentratora i akceleratora rozwiązania:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Możesz teraz zamknąć Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Utwórz nowe środowisko Time Series Insights

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) to w pełni zarządzana usługa analizy, magazynu i wizualizacji do zarządzania danymi szeregów czasowych w chmurze w skali IoT. Aby utworzyć nowe środowisko Time Series Insights:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **Utwórz zasób**  >  **Internet rzeczy**  >  **Time Series Insights**:

    ![Nowe Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko Time Series Insights w tej samej grupie zasobów co Akcelerator rozwiązania, użyj wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **contoso-TSI**. Po zakończeniu tego kroku wybierz własną unikatową nazwę. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **firma Contoso — symulacja**. Użyj nazwy akceleratora rozwiązania. |
    | Lokalizacja | W tym przykładzie jest stosowane **Wschodnie stany USA**. Utwórz swoje środowisko w tym samym regionie, w którym znajduje się akcelerator symulacji urządzenia. |
    | SKU |**S1** |
    | Pojemność | **1** |

    ![Tworzenie usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Dodanie środowiska Time Series Insights do tej samej grupy zasobów co Akcelerator rozwiązania oznacza, że jest usuwany po usunięciu akceleratora rozwiązania.

1. Kliknij pozycję **Utwórz**. Utworzenie środowiska może potrwać kilka minut.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzenia w celu nawiązania połączenia z usługą IoT Hub. Użyj grupy odbiorców utworzonej w poprzednich krokach. Źródło zdarzenia Time Series Insights wymaga, aby dedykowana Grupa odbiorców nie była używana przez inną usługę.

1. W Azure Portal przejdź do nowego środowiska szeregów czasowych.

1. Po lewej stronie kliknij pozycję **źródła zdarzeń**:

    ![Wyświetlanie źródeł zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij przycisk **Dodaj**:

    ![Dodaj źródło zdarzenia](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować Centrum IoT Hub jako nowe źródło zdarzeń, użyj wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa źródła zdarzenia | Poniższy zrzut ekranu używa nazwy **contoso-IoT-Hub**. Po wykonaniu tego kroku Użyj własnej unikatowej nazwy. |
    | Element źródłowy | **IoT Hub** |
    | Opcja importu | **Korzystanie z IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa Centrum IoT Hub | **contoso-simulation7d894**. Użyj nazwy Centrum IoT Hub z akceleratora rozwiązania do symulacji urządzeń. |
    | Nazwa zasady IoT Hub | **iothubowner** |
    | Klucz zasad Centrum IoT | To pole jest wypełniane automatycznie. |
    | Grupa użytkowników IoT Hub | **devicesimulationtsi** |
    | Format serializacji zdarzeń | **JSON** |
    | Nazwa właściwości znacznika czasu | Pozostaw puste. |

    ![Utwórz źródło zdarzenia](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Można [przyznać dodatkowym użytkownikom dostęp](../time-series-insights/concepts-access-policies.md#grant-data-access) do Eksploratora Time Series Insights.

## <a name="start-a-simulation"></a>Rozpocznij symulację

Przed rozpoczęciem korzystania z Eksploratora Time Series Insights Skonfiguruj Akcelerator rozwiązania do symulacji urządzeń w celu wygenerowania danych telemetrycznych. Poniższy zrzut ekranu przedstawia uruchomioną symulację z 10 urządzeniami chłodzenia:

![Symulacja uruchamiania urządzenia](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Eksplorator Time Series Insights jest aplikacją internetową, której można użyć do wizualizacji danych telemetrycznych.

1. W Azure Portal wybierz kartę **przegląd** Time Series Insights.

1. Aby otworzyć aplikację sieci Web programu Time Series Insights Explorer, kliknij pozycję **Przejdź do środowiska**:

    ![Eksplorator usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. W panelu wybór czasu wybierz pozycję **ostatnie 30 minut** z menu szybkie godziny, a następnie kliknij pozycję **Wyszukaj**:

    ![Wyszukiwanie w Eksploratorze Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. Na panelu warunki po lewej stronie wybierz pozycję **temperatura** jako **miarę** i iothub- **Connection-ID** jako wartość **Split by** :

    ![Zrzut ekranu przedstawiający panel "warunki" Time Series Insights z wyróżnionymi wartościami "Measure" i "Split by".](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Kliknij prawym przyciskiem myszy wykres i wybierz polecenie **Eksploruj zdarzenia**:

    ![Zdarzenia Eksploratora Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Dane zdarzenia są wyświetlane w siatce:

    ![Tabela Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Kliknij przycisk widok perspektywy:

    ![Perspektywa Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kliknij, **+** Aby dodać nowe zapytanie do perspektywy:

    ![Dodawanie zapytania do Time Series Insights Explorer](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Wybierz **pozycję ostatnie 30 minut** jako przedział czasu, **wilgotność** jako **miarę** i **Iothub-Connection-ID** jako **Split by** .

    ![Zapytanie Eksploratora Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kliknij przycisk widok perspektywy, aby wyświetlić pulpit nawigacyjny telemetrii urządzenia:

    ![Pulpit nawigacyjny Eksploratora Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz dowiedzieć się więcej, pozostaw wdrożony akcelerator rozwiązań.

Jeśli akcelerator rozwiązania nie jest już potrzebny, usuń go ze strony [wstępnie zainicjowanych rozwiązań](https://www.azureiotsolutions.com/Accelerators#dashboard) , wybierając ją, a następnie klikając pozycję **Usuń rozwiązanie**.

Jeśli środowisko Time Series Insights zostało dodane do grupy zasobów akceleratora rozwiązania, zostanie automatycznie usunięte po usunięciu akceleratora rozwiązania. W przeciwnym razie musisz ręcznie usunąć środowisko Time Series Insights z Azure Portal.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat eksplorowania i wykonywania zapytań dotyczących danych w Eksploratorze Time Series Insights, zobacz [eksplorator Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).