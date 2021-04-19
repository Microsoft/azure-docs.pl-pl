---
title: Wizualizowanie symulowanych danych telemetrycznych za Time Series Insights — Azure | Microsoft Docs
description: Dowiedz się, jak skonfigurować środowisko Time Series Insights, aby eksplorować i analizować dane telemetryczne generowane przez akcelerator rozwiązania do symulacji urządzeń.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.date: 08/20/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 90a4b808daccc76e8cc9125973c69b13e8086fbf
ms.sourcegitcommit: 3ed0f0b1b66a741399dc59df2285546c66d1df38
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107713970"
---
# <a name="use-time-series-insights-to-visualize-telemetry-sent-from-the-device-simulation-solution-accelerator"></a>Wizu Time Series Insights alizowanie danych telemetrycznych wysyłanych z akceleratora rozwiązania symulacji urządzenia przy użyciu narzędzia do wizualizacji

Akcelerator rozwiązania do symulacji urządzeń umożliwia generowanie danych telemetrycznych z symulowanych urządzeń w celu przetestowania rozwiązań IoT. W tym przewodniku po instrukcjach pokazano, jak wizualizować i analizować symulowane dane telemetryczne przy użyciu Time Series Insights danych.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, potrzebujesz aktywnej subskrypcji platformy Azure. Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

W krokach w tym przewodniku założono, że akcelerator rozwiązania do symulacji urządzeń został wdrożony w subskrypcji platformy Azure. Jeśli symulacja urządzenia nie została jeszcze wdrożona, zobacz [Wdrażanie symulacji urządzenia w](https://github.com/Azure/azure-iot-pcs-device-simulation/blob/master/README.md) witrynie GitHub.

W tym artykule przyjęto założenie, że nazwa akceleratora rozwiązania to **contoso-simulation.** Zastąp **nazwę contoso-simulation** nazwą akceleratora rozwiązania podczas wykonania poniższych kroków.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Musisz utworzyć dedykowaną grupę odbiorców w centrum IoT, aby przesyłać strumieniowo dane telemetryczne do Time Series Insights. Źródło zdarzeń w Time Series Insights powinno mieć wyłączne zastosowanie do IoT Hub grupy odbiorców.

Poniższe kroki używają interfejsu wiersza polecenia platformy Azure w Azure Cloud Shell, aby utworzyć grupę odbiorców:

1. Centrum IoT hub jest jednym z kilku zasobów utworzonych podczas wdrażania akceleratora rozwiązania do symulacji urządzeń. Wykonaj następujące polecenie, aby znaleźć nazwę centrum IoT Hub — pamiętaj, aby użyć nazwy akceleratora rozwiązania:

    ```azurecli-interactive
    az resource list --resource-group contoso-simulation -o table
    ```

    Centrum IoT jest zasobem typu **Microsoft.Devices/IotHubs.**

1. Dodaj grupę odbiorców **o nazwie devicesimulationtsi** do centrum. W poniższym poleceniu użyj nazwy centrum i akceleratora rozwiązania:

    ```azurecli-interactive
    az iot hub consumer-group create --hub-name contoso-simulation7d894 --name devicesimulationtsi --resource-group contoso-simulation
    ```

    Teraz możesz zamknąć Azure Cloud Shell.

## <a name="create-a-new-time-series-insights-environment"></a>Tworzenie nowego Time Series Insights projektowego

[Azure Time Series Insights](../../articles/time-series-insights/time-series-insights-overview.md) to w pełni zarządzana usługa analizy, magazynu i wizualizacji do zarządzania danymi szeregów czasowych w skali IoT w chmurze. Aby utworzyć nowe środowisko Time Series Insights:

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz **pozycję Utwórz**  >  **zasób Internet rzeczy**  >  **Time Series Insights:**

    ![Nowe Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko Time Series Insights w tej samej grupie zasobów co akcelerator rozwiązań, użyj wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **Contoso-TSI.** Po ukończeniu tego kroku wybierz własną unikatową nazwę. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **contoso-simulation**. Użyj nazwy akceleratora rozwiązania. |
    | Lokalizacja | W tym przykładzie użyto **regionu Wschodnie usa.** Utwórz środowisko w tym samym regionie co akcelerator symulacji urządzenia. |
    | SKU |**S1** |
    | Pojemność | **1** |

    ![Tworzenie usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/new-time-series-insights-create.png)

    > [!NOTE]
    > Dodanie Time Series Insights do tej samej grupy zasobów co akcelerator rozwiązań oznacza, że zostanie ono usunięte po usunięciu akceleratora rozwiązania.

1. Kliknij pozycję **Utwórz**. Do utworzenia środowiska może potrwać kilka minut.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzeń, aby nawiązać połączenie z centrum IoT. Użyj grupy odbiorców utworzonej w poprzednich krokach. Źródło Time Series Insights wymaga dedykowanej grupy odbiorców, która nie jest w użyciu przez inną usługę.

1. W Azure Portal przejdź do nowego środowiska szeregów czasu.

1. Po lewej stronie kliknij pozycję **Źródła zdarzeń:**

    ![Wyświetlanie źródeł zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij **pozycję Dodaj:**

    ![Dodawanie źródła zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować centrum IoT hub jako nowe źródło zdarzeń, użyj wartości w poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa źródła zdarzeń | Poniższy zrzut ekranu używa nazwy **contoso-iot-hub**. Po ukończeniu tego kroku użyj własnej unikatowej nazwy. |
    | Element źródłowy | **IoT Hub** |
    | Opcja importu | **Używanie IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa centrum IoT | **contoso-simulation7d894**. Użyj nazwy centrum IoT Hub z akceleratora rozwiązania do symulacji urządzenia. |
    | Nazwa zasady IoT Hub | **iothubowner** |
    | Klucz zasad centrum IoT | To pole jest wypełniane automatycznie. |
    | Grupa użytkowników IoT Hub | **devicesimulationtsi** |
    | Format serializacji zdarzeń | **JSON** |
    | Nazwa właściwości znacznika czasu | Pozostaw puste. |

    ![Tworzenie źródła zdarzeń](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij pozycję **Utwórz**.

> [!NOTE]
> Możesz [przyznać dodatkowym użytkownikom dostęp](../time-series-insights/concepts-access-policies.md#grant-data-access) do Time Series Insights eksploratora.

## <a name="start-a-simulation"></a>Rozpoczynanie symulacji

Przed użyciem eksploratora Time Series Insights skonfiguruj akcelerator rozwiązania do symulacji urządzenia, aby wygenerować dane telemetryczne. Poniższy zrzut ekranu przedstawia uruchamianą symulację z 10 chłodniami:

![Uruchamianie symulacji urządzenia](./media/iot-accelerators-device-simulation-time-series-insights/running-simulation.png)

## <a name="time-series-insights-explorer"></a>Eksplorator usługi Time Series Insights

Eksplorator Time Series Insights to aplikacja internetowa, która umożliwia wizualizowanie telemetrii.

1. W Azure Portal wybierz kartę Time Series Insights **Przegląd.**

1. Aby otworzyć aplikację internetową Time Series Insights Explorer, kliknij **pozycję Przejdź do środowiska:**

    ![Eksplorator usługi Time Series Insights](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-environment.png)

1. W panelu wyboru czasu wybierz pozycję **Ostatnie 30 minut** z menu szybkich godzin, a następnie kliknij pozycję **Wyszukaj:**

    ![Time Series Insights eksploratora](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-search-time.png)

1. W panelu terminów po lewej stronie wybierz pozycję **temperature** jako **miarę** i **wartość iothub-connection-device-id** **jako wartość Podziel według:**

    ![Zrzut ekranu przedstawiający Time Series Insights "terms" (terminy) z wyróżnione wartości "Miara" i "Podziel według".](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query1.png)

1. Kliknij prawym przyciskiem myszy wykres i wybierz polecenie **Eksploruj zdarzenia:**

    ![Time Series Insights zdarzeń eksploratora](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explore-events.png)

1. Dane zdarzenia są w siatce:

    ![Time Series Insights tabeli eksploratora](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-table.png)

1. Kliknij przycisk widoku perspektywy:

    ![Time Series Insights perspektywy eksploratora](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-explorer-perspective.png)

1. Kliknij, **+** aby dodać nowe zapytanie do perspektywy:

    ![Time Series Insights explorer Add Query](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-new-query.png)

1. Wybierz **pozycję Ostatnie 30** minut jako zakres czasu, pozycję **Humidity** (Wilgotność) jako **miarę** i **wartość iothub-connection-device-id** **jako wartość Split By (Podziel według):**

    ![Time Series Insights eksploratora](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-query2.png)

1. Kliknij przycisk widoku perspektywy, aby wyświetlić pulpit nawigacyjny telemetrii urządzenia:

    ![Time Series Insights nawigacyjny Eksploratora aplikacji](./media/iot-accelerators-device-simulation-time-series-insights/time-series-insights-dashboard.png)

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Jeśli planujesz dalsze eksplorowanie, pozostaw wdrożony akcelerator rozwiązania.

Jeśli akcelerator rozwiązań nie jest już potrzebny, usuń go ze strony Aprowizowane [rozwiązania,](https://www.azureiotsolutions.com/Accelerators#dashboard) wybierając go, a następnie klikając pozycję **Usuń rozwiązanie.**

Jeśli dodano środowisko Time Series Insights do grupy zasobów akceleratora rozwiązań, zostanie ono automatycznie usunięte po usunięciu akceleratora rozwiązania. W przeciwnym razie należy ręcznie usunąć Time Series Insights z Azure Portal.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat eksplorowanie danych i wykonywanie na nich zapytań w eksploratorze Time Series Insights, zobacz [Azure Time Series Insights explorer](../time-series-insights/time-series-insights-explorer.md).