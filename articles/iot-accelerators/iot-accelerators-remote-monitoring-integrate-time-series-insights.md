---
title: Integrowanie Time Series Insights ze zdalnym monitorowaniem — Azure | Microsoft Docs
description: W tym przewodniku dowiesz się, jak skonfigurować Time Series Insights istniejącego rozwiązania do monitorowania zdalnego, które nie zawiera jeszcze Time Series Insights.
author: Philmea
manager: timlt
ms.author: philmea
ms.date: 09/12/2018
ms.topic: conceptual
ms.service: iot-accelerators
services: iot-accelerators
ms.openlocfilehash: 8b6219c82dc23deb467e87a6866839b8eace8b5d
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92072649"
---
# <a name="integrate-azure-time-series-insights-with-remote-monitoring"></a>Integrowanie usługi Azure Time Series Insights za pomocą zdalnego monitorowania

Azure Time Series Insights to w pełni zarządzana usługa analizy, magazynu i wizualizacji do zarządzania danymi szeregów czasowych w chmurze w skali IoT. Za pomocą Time Series Insights można przechowywać dane szeregów czasowych i zarządzać nimi, badać i wizualizować zdarzenia jednocześnie, przeprowadzać analizę głównych przyczyn i porównywać wiele lokacji i zasobów.

Akcelerator rozwiązania do monitorowania zdalnego zapewnia teraz automatyczne wdrażanie i integrację z usługą Time Series Insights. W tym przewodniku dowiesz się, jak skonfigurować Time Series Insights istniejącego rozwiązania do monitorowania zdalnego, które nie zawiera jeszcze Time Series Insights.

> [!NOTE]
> Time Series Insights nie jest obecnie dostępna w chmurze platformy Azure w Chinach. Nowe wdrożenia akceleratora rozwiązań do monitorowania zdalnego w przypadku chmury platformy Azure w Chinach Użyj Cosmos DB dla wszystkich magazynów.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać te czynności, musisz już wdrożyć rozwiązanie do monitorowania zdalnego:

* [Wdróż Akcelerator rozwiązania do monitorowania zdalnego](quickstart-remote-monitoring-deploy.md)

## <a name="create-a-consumer-group"></a>Tworzenie grupy odbiorców

Utwórz dedykowaną grupę odbiorców w IoT Hub, która będzie używana do przesyłania strumieniowego danych do Time Series Insights.

> [!NOTE]
> Grupy konsumentów są używane przez aplikacje do ściągania danych z usługi Azure IoT Hub. Każda grupa odbiorców pozwala maksymalnie pięciu odbiorcom danych wyjściowych. Należy utworzyć nową grupę odbiorców dla każdego pięciu ujść wyjściowych i utworzyć maksymalnie 32 grup odbiorców.

1. W Azure Portal kliknij przycisk Cloud Shell.

1. Wykonaj następujące polecenie, aby utworzyć nową grupę odbiorców. Użyj nazwy Centrum IoT w ramach wdrożenia monitorowania zdalnego i nazwy wdrożenia monitorowania zdalnego jako nazwy grupy zasobów:

```azurecli-interactive
az iot hub consumer-group create --hub-name contosorm30526 --name timeseriesinsights --resource-group ContosoRM
```

## <a name="deploy-time-series-insights"></a>Wdróż Time Series Insights

Następnie wdróż Time Series Insights jako dodatkowy zasób w rozwiązaniu do zdalnego monitorowania i podłącz go do centrum IoT.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **Utwórz zasób**  >  **Internet rzeczy**  >  **Time Series Insights**.

    ![Nowe Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights.png)

1. Aby utworzyć środowisko Time Series Insights, użyj wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa środowiska | Poniższy zrzut ekranu używa nazwy **contorosrmtsi**. Po zakończeniu tego kroku wybierz własną unikatową nazwę. |
    | Subskrypcja | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Grupa zasobów | **Użyj istniejącej**. Wybierz nazwę istniejącej grupy zasobów do monitorowania zdalnego. |
    | Lokalizacja | Korzystamy z **Wschodnie stany USA**. Utwórz środowisko w tym samym regionie, w którym znajduje się rozwiązanie do monitorowania zdalnego, jeśli jest to możliwe. |
    | SKU |**S1** |
    | Pojemność | **1** |

    ![Tworzenie usługi Time Series Insights](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/new-time-series-insights-create.png)

1. Kliknij pozycję **Utwórz**. Utworzenie środowiska może chwilę potrwać.

## <a name="create-event-source"></a>Tworzenie źródła zdarzeń

Utwórz nowe źródło zdarzenia w celu nawiązania połączenia z usługą IoT Hub. Upewnij się, że używasz grupy odbiorców utworzonej w poprzednich krokach. Time Series Insights wymaga, aby każda usługa miała dedykowaną grupę odbiorców, która nie jest używana przez inną usługę.

1. Przejdź do nowego środowiska Time Series Insights.

1. Po lewej stronie wybierz pozycję **źródła zdarzeń**.

    ![Wyświetlanie źródeł zdarzeń](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources.png)

1. Kliknij pozycję **Dodaj**.

    ![Dodaj źródło zdarzenia](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-sources-add.png)

1. Aby skonfigurować Centrum IoT Hub jako nowe źródło zdarzeń, użyj wartości z poniższej tabeli:

    | Ustawienie | Wartość |
    | ------- | ----- |
    | Nazwa źródła zdarzeń | Poniższy zrzut ekranu używa nazwy **contosorm-IoT-Hub**. Po wykonaniu tego kroku Użyj własnej unikatowej nazwy. |
    | Element źródłowy | **IoT Hub** |
    | Opcja importu | **Korzystanie z IoT Hub z dostępnych subskrypcji** |
    | Identyfikator subskrypcji | Z listy rozwijanej wybierz subskrypcję platformy Azure. |
    | Nazwa Centrum IoT Hub | **contosorma57a6**. Użyj nazwy Centrum IoT Hub z rozwiązania do zdalnego monitorowania. |
    | Nazwa zasady IoT Hub | **iothubowner** Upewnij się, że używane zasady są zasadami właściciela. |
    | Klucz zasad Centrum IoT | To pole jest wypełniane automatycznie. |
    | Grupa użytkowników IoT Hub | **timeseriesinsights** |
    | Format serializacji zdarzeń | **JSON**     | 
    | Nazwa właściwości znacznika czasu | Pozostaw puste. |

    ![Utwórz źródło zdarzenia](./media/iot-accelerators-remote-monitoring-integrate-time-series-insights/time-series-insights-event-source-create.png)

1. Kliknij pozycję **Utwórz**.

## <a name="configure-the-data-access-policy"></a>Konfigurowanie zasad dostępu do danych

Aby mieć pewność, że wszyscy użytkownicy, którzy mają dostęp do rozwiązania do monitorowania zdalnego, będą mogli eksplorować dane w Eksploratorze Time Series Insights, Dodaj aplikację i użytkowników w obszarze zasady dostępu do danych w Azure Portal. 

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz grupę zasobów **ContosoRM** .

1. Wybierz pozycję **contosormtsi** na liście zasobów platformy Azure.

1. Wybierz pozycję **zasady dostępu do danych** , aby wyświetlić bieżącą listę przypisań ról.

1. Wybierz pozycję **Dodaj** , aby otworzyć okienko **Wybieranie reguły użytkownika** .

   Jeśli nie masz uprawnień do przypisywania ról, nie widzisz opcji **Dodaj** .

1. Z listy rozwijanej **rola** wybierz rolę, taką jak **czytelnik** i **współautor**.

1. Z listy **Wybierz** wybierz użytkownika, grupę lub aplikację. Jeśli nie widzisz podmiotu zabezpieczeń na liście, możesz wpisać tekst w polu **Wybierz**, aby wyszukać w katalogu nazwy wyświetlane, adresy e-mail i identyfikatory obiektów.

1. Wybierz pozycję **Zapisz**, aby utworzyć przypisanie roli. Po kilku chwilach podmiot zabezpieczeń ma przypisaną rolę w zasadach dostępu do danych.

> [!NOTE]
> Jeśli musisz udzielić dodatkowych użytkownikom dostępu do Eksploratora Time Series Insights, możesz wykonać te kroki, aby [udzielić dostępu do danych](../time-series-insights/concepts-access-policies.md#grant-data-access).

## <a name="configure-azure-stream-analytics"></a>Konfigurowanie Azure Stream Analytics 

Następnym krokiem jest skonfigurowanie mikrousługi Azure Stream Analytics Manager w celu zaprzestania wysyłania komunikatów do Cosmos DB i zapisania ich tylko w Time Series Insights. Pomiń ten krok, jeśli chcesz zduplikować wiadomości w Cosmos DB.

1. Na liście nawigacji wybierz pozycję **Grupy zasobów**.

1. Wybierz grupę zasobów **ContosoRM** .

1. Znajdź zadanie przesyłania strumieniowego Azure Stream Analytics (ASA) na liście zasobów. Nazwa zasobu zaczyna się od **streamingjobs-**.

1. W górnej części kliknij przycisk, aby zatrzymać zadania przesyłania strumieniowego ASA.

1. Edytuj zapytanie ASA i Usuń klauzule **SELECT**, **into**i **from** wskazujące strumień komunikatów w Cosmos DB. Te klauzule powinny znajdować się w dolnej części zapytania i wyglądać jak w poniższym przykładzie:

    ```sql
    SELECT
            CONCAT(T.IoTHub.ConnectionDeviceId, ';', CAST(DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) AS nvarchar(max))) as id,
            1 as [doc.schemaVersion],
            'd2cmessage' as [doc.schema],
            T.IoTHub.ConnectionDeviceId as [device.id],
            'device-sensors;v1' as [device.msg.schema],
            'StreamingJobs' as [data.schema],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', System.Timestamp) as [device.msg.created],
            DATEDIFF(millisecond, '1970-01-01T00:00:00Z', T.EventEnqueuedUtcTime) as [device.msg.received],
            udf.removeUnusedProperties(T) as Data
    INTO
        Messages
    FROM
        DeviceTelemetry T PARTITION BY PartitionId TIMESTAMP BY T.EventEnqueuedUtcTime
    ```

6. Uruchom ponownie Azure Stream Analytics zadania przesyłania strumieniowego.

7. Pobierz najnowsze zmiany do mikrousługi Azure Stream Analytics Manager, wpisując następujące polecenie w wierszu polecenia:

.NET: 

```cmd/sh
docker pull azureiotpcs/asa-manager-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/asa-manager-java:1.0.2
```

## <a name="configure-the-telemetry-microservice"></a>Konfigurowanie mikrousługi telemetrii

Pobierz najnowszą mikrousługę telemetrii, wpisując następujące polecenie w wierszu polecenia:

.NET:

```cmd/sh
docker pull azureiotpcs/telemetry-dotnet:1.0.2
```

Java:

```cmd/sh
docker pull azureiotpcs/telemetry-java:1.0.2
```

## <a name="optional-configure-the-web-ui-to-link-to-the-time-series-insights-explorer"></a>*[Opcjonalne]* Skonfiguruj interfejs użytkownika sieci Web do łączenia z Eksploratorem Time Series Insights

Aby łatwo wyświetlać dane w Eksploratorze Time Series Insights, zalecamy dostosowanie interfejsu użytkownika do łatwego łączenia ze środowiskiem. W tym celu należy ściągnąć najnowsze zmiany w interfejsie użytkownika sieci Web przy użyciu następującego polecenia:

```cmd/sh
docker pull azureiotpcs/pcs-remote-monitoring-webui:1.0.2
```

## <a name="configure-the-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Aby wykonać integrację Time Series Insights, należy skonfigurować środowisko wdrożenia dla zaktualizowanych mikrousług.

### <a name="basic-deployments"></a>Podstawowe wdrożenia

Skonfiguruj środowisko `basic` wdrożenia zaktualizowanych mikrousług.

1. W Azure Portal kliknij kartę **Azure Active Directory** w panelu po lewej stronie.

1. Kliknij **rejestracje aplikacji**.

1. Wyszukaj aplikację **ContosoRM** i kliknij ją.

1. Przejdź do **ustawień**  >  **klucze** , a następnie utwórz nowy klucz dla aplikacji. Skopiuj wartość klucza do bezpiecznej lokalizacji.

1. Pobierz [najnowszy plik YAML platformy Docker](https://github.com/Azure/pcs-cli/tree/5a9b4e0dbe313172eff19236e54a4d461d4f3e51/solutions/remotemonitoring/single-vm) z repozytorium GitHub przy użyciu najnowszego tagu. 

1. Aby włączyć protokół SSH do maszyny wirtualnej, wykonaj kroki opisane w sekcji [jak utworzyć klucze SSH i korzystać](../virtual-machines/linux/ssh-from-windows.md)z nich.

1. Po nawiązaniu połączenia wpisz polecenie `cd /app` .

1. Dodaj następujące zmienne środowiskowe do każdej mikrousługi w pliku YAML platformy Docker i `env-setup` skryptu na maszynie wirtualnej:

    ```sh
    PCS_TELEMETRY_STORAGE_TYPE=tsi
    PCS_TSI_FQDN={TSI Data Access FQDN}
    PCS_AAD_TENANT={AAD Tenant Id}
    PCS_AAD_APPID={AAD application Id}
    PCS_AAD_APPSECRET={AAD application key}
    ```

1. Przejdź do **usługi telemetrii** , a także Edytuj plik redagowania platformy Docker, dodając te same zmienne środowiskowe powyżej.

1. Przejdź do **usługi ASA Manager** i edytuj plik redagowania platformy Docker przez dodanie `PCS_TELEMETRY_STORAGE_TYPE` .

1. Uruchom ponownie kontenery platformy Docker przy użyciu programu `sudo ./start.sh` z maszyny wirtualnej.

> [!NOTE]
> Powyższa konfiguracja zmiennych środowiskowych jest prawidłowa dla wersji monitorowania zdalnego przed 1.0.2

### <a name="standard-deployments"></a>Standardowe wdrożenia

Skonfiguruj środowisko `standard` wdrożenia dla zaktualizowanych Micro usług powyżej

1. W wierszu polecenia Uruchom polecenie `kubectl proxy` . Aby uzyskać więcej informacji, zobacz [Uzyskiwanie dostępu do interfejsu API Kubernetes](https://kubernetes.io/docs/reference/access-authn-authz/#using-kubectl-to-start-a-proxy-server).

1. Otwórz konsolę zarządzania Kubernetes.

1. Znajdź mapę konfiguracji, aby dodać następujące nowe zmienne środowiskowe dla TSI:

    ```yaml
    telemetry.storage.type: "tsi"
    telemetry.tsi.fqdn: "{TSI Data Access FQDN}"
    security.auth.serviceprincipal.secret: "{AAD application service principal secret}"
    ```

4. Edytuj plik YAML szablonu dla usługi telemetrii pod:

    ```yaml
    - name: PCS_AAD_TENANT
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.tenant
    - name: PCS_AAD_APPID
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.audience
    - name: PCS_AAD_APPSECRET
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: security.auth.serviceprincipal.secret
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    - name: PCS_TSI_FQDN
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.tsi.fqdn
    ```

5. Edytuj plik YAML szablonu dla usługi ASA Manager pod:

    ```yaml
    - name: PCS_TELEMETRY_STORAGE_TYPE
        valueFrom:
        configMapKeyRef:
            name: deployment-configmap
            key: telemetry.storage.type
    ```

## <a name="next-steps"></a>Następne kroki

* Aby dowiedzieć się, jak eksplorować dane i zdiagnozować alert w Eksploratorze Time Series Insights, zobacz nasz samouczek dotyczący [przeprowadzania analizy głównej przyczyny](iot-accelerators-remote-monitoring-root-cause-analysis.md).

* Aby dowiedzieć się, jak eksplorować i badać dane w Eksploratorze Time Series Insights, zapoznaj się z dokumentacją w [eksploratorze Azure Time Series Insights](../time-series-insights/time-series-insights-explorer.md).