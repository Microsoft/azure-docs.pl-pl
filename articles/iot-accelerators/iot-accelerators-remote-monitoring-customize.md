---
title: Dostosowywanie interfejsu użytkownika rozwiązania do monitorowania zdalnego — Azure | Microsoft Docs
description: Ten artykuł zawiera informacje o tym, jak uzyskać dostęp do kodu źródłowego dla interfejsu użytkownika akceleratora rozwiązania do monitorowania zdalnego i wprowadzić pewne dostosowania.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/09/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 2789ed642979616a4491a61d146d8468552ec2e5
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318470"
---
# <a name="customize-the-remote-monitoring-solution-accelerator"></a>Dostosowywanie akceleratora rozwiązania do monitorowania zdalnego

Ten artykuł zawiera informacje o tym, jak uzyskać dostęp do kodu źródłowego i dostosować interfejs użytkownika akceleratora rozwiązania do monitorowania zdalnego.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prepare-a-local-development-environment-for-the-ui"></a>Przygotuj lokalne środowisko programistyczne dla interfejsu użytkownika

Kod interfejsu użytkownika akceleratora rozwiązania do monitorowania zdalnego jest implementowany przy użyciu struktury React.js. Kod źródłowy można znaleźć w repozytorium GitHub [Azure-IoT-PC-Remote-Monitoring-WebUI](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) .

Aby wprowadzić zmiany w interfejsie użytkownika, można uruchomić jego kopię lokalnie. Aby wykonać akcje, takie jak pobieranie telemetrii, lokalna kopia nawiązuje połączenie ze wdrożonym wystąpieniem rozwiązania.

Poniższe kroki przedstawiają proces konfigurowania środowiska lokalnego do tworzenia interfejsu użytkownika:

1. Wdróż **podstawowe** wystąpienie akceleratora rozwiązania przy użyciu interfejsu wiersza polecenia **komputerów** . Zanotuj nazwę wdrożenia i poświadczenia podane dla maszyny wirtualnej. Aby uzyskać więcej informacji, zobacz [wdrażanie przy użyciu interfejsu wiersza polecenia](iot-accelerators-remote-monitoring-deploy-cli.md).

1. Aby włączyć dostęp SSH do maszyny wirtualnej, która obsługuje mikrousługi w rozwiązaniu, użyj Azure Portal lub Azure Cloud Shell. Na przykład:

    ```azurecli-interactive
    az network nsg rule update --name SSH --nsg-name {your solution name}-nsg --resource-group {your solution name} --access Allow
    ```

    Włącz dostęp SSH tylko podczas testowania i programowania. Po włączeniu protokołu SSH należy [go wyłączyć zaraz po zakończeniu korzystania z niego](../security/fundamentals/network-best-practices.md#disable-rdpssh-access-to-virtual-machines).

1. Użyj Azure Portal lub Azure Cloud Shell, aby znaleźć nazwę i publiczny adres IP maszyny wirtualnej. Na przykład:

    ```azurecli-interactive
    az resource list --resource-group {your solution name} -o table
    az vm list-ip-addresses --name {your vm name from previous command} --resource-group {your solution name} -o table
    ```

1. Użyj protokołu SSH, aby nawiązać połączenie z maszyną wirtualną. Użyj adresu IP z poprzedniego kroku i poświadczeń podanych podczas uruchamiania **komputerów** w celu wdrożenia rozwiązania. `ssh`Polecenie jest dostępne w Azure Cloud Shell.

1. Aby zezwolić na połączenie lokalnego środowiska użytkownika, uruchom następujące polecenia w powłoce bash na maszynie wirtualnej:

    ```sh
    cd /app
    sudo ./start.sh --unsafe
    ```

1. Po wyświetleniu polecenia i uruchomieniu witryny sieci Web można rozłączyć się z maszyną wirtualną.

1. W lokalnej kopii repozytorium [Azure-IoT-PC-Remote-Monitoring-WebUI](https://github.com/Azure/azure-iot-pcs-remote-monitoring-webui) Edytuj plik **ENV** , aby dodać adres URL wdrożonego rozwiązania:

    ```config
    NODE_PATH = src/
    REACT_APP_BASE_SERVICE_URL=https://{your solution name}.azurewebsites.net/
    ```

1. W wierszu polecenia przejdź do lokalnej kopii `azure-iot-pcs-remote-monitoring-webui` folderu.

1. Aby zainstalować wymagane biblioteki i uruchomić interfejs użytkownika lokalnie, uruchom następujące polecenia:

    ```cmd/sh
    npm install
    npm start
    ```

1. Poprzednie polecenie uruchamia interfejs użytkownika lokalnie na http: \/ /localhost: 3000/pulpit nawigacyjny. Można edytować kod, gdy lokacja jest uruchomiona i wyświetlać ją dynamicznie.

## <a name="customize-the-layout"></a>Dostosowywanie układu

Każda Strona w rozwiązaniu do monitorowania zdalnego składa się z zestawu kontrolek, które są określane jako *panele* w kodzie źródłowym. Strona **pulpitu nawigacyjnego** składa się z pięciu paneli: Omówienie, mapa, alerty, dane telemetryczne i analiza. Kod źródłowy, który definiuje każdą stronę i jej panele, można znaleźć w repozytorium na [komputerach PC-Remote-Monitoring-WebUI](https://github.com/Azure/pcs-remote-monitoring-webui) . Na przykład kod definiujący stronę **pulpitu nawigacyjnego** , jego układ i panele na stronie znajduje się w folderze [src/Components/Pages/pulpitu nawigacyjnego](https://github.com/Azure/pcs-remote-monitoring-webui/tree/master/src/components/pages/dashboard) .

Ponieważ panele zarządzają własnym układem i rozmiarem, można łatwo zmodyfikować układ strony. Wprowadź następujące zmiany do elementu **PageContent** w `src/components/pages/dashboard/dashboard.js` pliku, aby:

* Zamień położenia paneli mapy i telemetrii.
* Zmień względne szerokości paneli mapy i analizy.

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-6">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

![Zmień układ panelu](./media/iot-accelerators-remote-monitoring-customize/layout.png)

Możesz również dodać kilka wystąpień tego samego panelu lub kilka wersji, jeśli [duplikujesz i dostosowasz panel](#duplicate-and-customize-an-existing-control). Poniższy przykład pokazuje, jak dodać dwa wystąpienia panelu telemetrii. Aby wprowadzić te zmiany, Edytuj `src/components/pages/dashboard/dashboard.js` plik:

```javascript
<PageContent className="dashboard-container">
  <Grid>
    <Cell className="col-1 devices-overview-cell">
      <OverviewPanel
        activeDeviceGroup={activeDeviceGroup}
        openWarningCount={openWarningCount}
        openCriticalCount={openCriticalCount}
        onlineDeviceCount={onlineDeviceCount}
        offlineDeviceCount={offlineDeviceCount}
        isPending={analyticsIsPending || devicesIsPending}
        error={deviceGroupError || devicesError || analyticsError}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <TelemetryPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        telemetry={telemetry}
        isPending={telemetryIsPending}
        lastRefreshed={lastRefreshed}
        error={deviceGroupError || telemetryError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    <Cell className="col-3">
      <AlertsPanel
        alerts={currentActiveAlertsWithName}
        isPending={analyticsIsPending || rulesIsPending}
        error={rulesError || analyticsError}
        t={t}
        deviceGroups={deviceGroups} />
    </Cell>
    <Cell className="col-4">
      <PanelErrorBoundary msg={t('dashboard.panels.map.runtimeError')}>
        <MapPanel
          analyticsVersion={analyticsVersion}
          azureMapsKey={azureMapsKey}
          devices={devices}
          devicesInAlert={devicesInAlert}
          mapKeyIsPending={azureMapsKeyIsPending}
          isPending={devicesIsPending || analyticsIsPending}
          error={azureMapsKeyError || devicesError || analyticsError}
          t={t} />
      </PanelErrorBoundary>
    </Cell>
    <Cell className="col-6">
      <AnalyticsPanel
        timeSeriesExplorerUrl={timeSeriesParamUrl}
        topAlerts={topAlertsWithName}
        alertsPerDeviceId={alertsPerDeviceType}
        criticalAlertsChange={criticalAlertsChange}
        isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
        error={devicesError || rulesError || analyticsError}
        theme={theme}
        colors={chartColorObjects}
        t={t} />
    </Cell>
    {
      Config.showWalkthroughExamples &&
      <Cell className="col-4">
        <ExamplePanel t={t} />
      </Cell>
    }
  </Grid>
</PageContent>
```

Następnie można wyświetlić różne dane telemetryczne na każdym panelu:

![Wiele paneli telemetrii](./media/iot-accelerators-remote-monitoring-customize/multiple-telemetry.png)

## <a name="duplicate-and-customize-an-existing-control"></a>Duplikuj i dostosowuj istniejący formant

Poniższe kroki przedstawiają sposób duplikowania istniejącego panelu, modyfikowania go, a następnie używania zmodyfikowanej wersji. W tym kroku użyjesz panelu **alerty** jako przykładu:

1. W lokalnej kopii repozytorium Utwórz kopię folderu **alertów** w `src/components/pages/dashboard/panels` folderze. Nazwij nową kopię **cust_alerts**.

1. W pliku **alertsPanel.js** w folderze **cust_alerts** Edytuj nazwę klasy, która ma być **CustAlertsPanel**:

    ```javascript
    export class CustAlertsPanel extends Component {
    ```

1. Dodaj następujący wiersz do `src/components/pages/dashboard/panels/index.js` pliku:

    ```javascript
    export * from './cust_alerts';
    ```

1. Zamień `alertsPanel` na `CustAlertsPanel` w `src/components/pages/dashboard/dashboard.js` pliku:

    ```javascript
    import {
      OverviewPanel,
      CustAlertsPanel,
      TelemetryPanel,
      KpisPanel,
      MapPanel,
      transformTelemetryResponse,
      chartColors
    } from './panels';

    ...

    <Cell className="col-3">
      <CustAlertsPanel
        alerts={currentActivealertsWithName}
        isPending={kpisIsPending || rulesIsPending}
        error={rulesError || kpisError}
        t={t} />
    </Cell>
    ```

Oryginalny panel **alerty** został już zastąpiony kopią o nazwie **CustAlerts**. Ta kopia jest taka sama jak wersja oryginalna. Teraz można zmodyfikować kopię. Na przykład, aby zmienić kolejność kolumn w panelu **alerty** :

1. Otwórz plik `src/components/pages/dashboard/panels/cust_alerts/alertsPanel.js`.

1. Zmodyfikuj definicje kolumn, jak pokazano w poniższym fragmencie kodu:

    ```javascript
    this.columnDefs = [
      rulesColumnDefs.severity,
      {
        headerName: 'rules.grid.count',
        field: 'count'
      },
      {
        ...rulesColumnDefs.ruleName,
        minWidth: 200
      },
      rulesColumnDefs.explore
    ];
    ```

Poniższy zrzut ekranu przedstawia nową wersję panelu **alerty** :

![Zaktualizowano panel alertów](./media/iot-accelerators-remote-monitoring-customize/reorder-columns.png)

## <a name="customize-the-telemetry-chart"></a>Dostosowywanie wykresu telemetrii

Pliki w `src/components/pages/dashboard/panels/telemtry` folderze definiują wykres telemetrii na stronie **pulpitu nawigacyjnego** . Interfejs użytkownika pobiera dane telemetryczne z zaplecza rozwiązania w `src/services/telemetryService.js` pliku. Poniższe kroki pokazują, jak zmienić okres wyświetlany na wykresie telemetrii od 15 do 5 minut:

1. W `src/services/telemetryService.js` pliku Znajdź funkcję o nazwie **getTelemetryByDeviceIdP15M**. Utwórz kopię tej funkcji i zmodyfikuj kopię w następujący sposób:

    ```javascript
    static getTelemetryByDeviceIdP5M(devices = []) {
      return TelemetryService.getTelemetryByMessages({
        from: 'NOW-PT5M',
        to: 'NOW',
        order: 'desc',
        devices
      });
    }
    ```

1. Aby użyć tej nowej funkcji do wypełnienia wykresu telemetrii, Otwórz `src/components/pages/dashboard/dashboard.js` plik. Znajdź wiersz inicjujący strumień danych telemetrycznych i zmodyfikuj go w następujący sposób:

    ```javascript
    const getTelemetryStream = ({ deviceIds = [] }) => TelemetryService.getTelemetryByDeviceIdP5M(deviceIds)
    ```

Wykres telemetrii przedstawia teraz pięć minut danych telemetrii:

![Wykres telemetrii przedstawiający jeden dzień](./media/iot-accelerators-remote-monitoring-customize/telemetry-period.png)

## <a name="add-a-new-kpi"></a>Dodaj nowy kluczowy wskaźnik wydajności

Na stronie **pulpit nawigacyjny** są wyświetlane wskaźniki KPI w panelu **Analiza** . Te wskaźniki KPI są obliczane w `src/components/pages/dashboard/dashboard.js` pliku. Wskaźniki KPI są renderowane przez `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` plik. Poniższe kroki opisują sposób obliczania i renderowania nowej wartości kluczowego wskaźnika wydajności na stronie **pulpitu nawigacyjnego** . Pokazanym przykładem jest dodanie nowej zmiany procentowej wskaźnika KPI alertów ostrzegawczych:

1. Otwórz plik `src/components/pages/dashboard/dashboard.js`. Zmodyfikuj obiekt **InitialState** , aby zawierał Właściwość **warningAlertsChange** w następujący sposób:

    ```javascript
    const initialState = {
      ...

      // Analytics data
      analyticsVersion: 0,
      currentActiveAlerts: [],
      topAlerts: [],
      alertsPerDeviceId: {},
      criticalAlertsChange: 0,
      warningAlertsChange: 0,
      analyticsIsPending: true,
      analyticsError: null

      ...
    };
    ```

1. Zmodyfikuj obiekt **currentAlertsStats** , aby zawierał **totalWarningCount** jako właściwość:

    ```javascript
    return {
      openWarningCount: (acc.openWarningCount || 0) + (isWarning && isOpen ? 1 : 0),
      openCriticalCount: (acc.openCriticalCount || 0) + (isCritical && isOpen ? 1 : 0),
      totalWarningCount: (acc.totalWarningCount || 0) + (isWarning ? 1 : 0),
      totalCriticalCount: (acc.totalCriticalCount || 0) + (isCritical ? 1 : 0),
      alertsPerDeviceId: updatedAlertsPerDeviceId
    };
    ```

1. Oblicz Nowy wskaźnik KPI. Znajdź obliczenia dla liczby alertów krytycznych. Duplikuj kod i zmodyfikuj kopię w następujący sposób:

    ```javascript
    // ================== Warning Alerts Count - START
    const currentWarningAlerts = currentAlertsStats.totalWarningCount;
    const previousWarningAlerts = previousAlerts.reduce(
      (cnt, { severity }) => severity === Config.ruleSeverity.warning ? cnt + 1 : cnt,
      0
    );
    const warningAlertsChange = ((currentWarningAlerts - previousWarningAlerts) / currentWarningAlerts * 100).toFixed(2);
    // ================== Warning Alerts Count - END
    ```

1. Dołącz nowy kluczowy wskaźnik wydajności **warningAlertsChange** w strumieniu KPI:

    ```javascript
    return ({
      analyticsIsPending: false,
      analyticsVersion: this.state.analyticsVersion + 1,

      // Analytics data
      currentActiveAlerts,
      topAlerts,
      criticalAlertsChange,
      warningAlertsChange,
      alertsPerDeviceId: currentAlertsStats.alertsPerDeviceId,

      ...
    });
    ```

1. Uwzględnij Nowy wskaźnik KPI **warningAlertsChange** w danych stanu używanych do renderowania interfejsu użytkownika:

    ```javascript
    const {
      ...

      analyticsVersion,
      currentActiveAlerts,
      topAlerts,
      alertsPerDeviceId,
      criticalAlertsChange,
      warningAlertsChange,
      analyticsIsPending,
      analyticsError,

      ...
    } = this.state;
    ```

1. Zaktualizuj dane przesyłane do panelu KPI:

    ```javascript
    <AnalyticsPanel
      timeSeriesExplorerUrl={timeSeriesParamUrl}
      topAlerts={topAlertsWithName}
      alertsPerDeviceId={alertsPerDeviceType}
      criticalAlertsChange={criticalAlertsChange}
      warningAlertsChange={warningAlertsChange}
      isPending={analyticsIsPending || rulesIsPending || devicesIsPending}
      error={devicesError || rulesError || analyticsError}
      theme={theme}
      colors={chartColorObjects}
      t={t} />
    ```

Zmiany w pliku zostały już zakończone `src/components/pages/dashboard/dashboard.js` . Poniższe kroki opisują zmiany, które należy wprowadzić w `src/components/pages/dashboard/panels/analytics/analyticsPanel.js` pliku, aby wyświetlić nowy wskaźnik KPI:

1. Zmodyfikuj następujący wiersz kodu, aby pobrać nową wartość kluczowego wskaźnika wydajności w następujący sposób:

    ```javascript
    const { t, isPending, criticalAlertsChange, warningAlertsChange, alertsPerDeviceId, topAlerts, timeSeriesExplorerUrl, error } = this.props;
    ```

1. Zmodyfikuj adiustację, aby wyświetlić nową wartość kluczowego wskaźnika wydajności w następujący sposób:

    ```javascript
    <div className="analytics-cell">
      <div className="analytics-header">{t('dashboard.panels.analytics.criticalAlerts')}</div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(criticalAlertsChange) ? criticalAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
      <div className="critical-alerts">
        {
          !showOverlay &&
            <div className="analytics-percentage-container">
              <div className="analytics-value">{ !isNaN(warningAlertsChange) ? warningAlertsChange : 0 }</div>
              <div className="analytics-percentage-sign">%</div>
            </div>
        }
      </div>
    </div>
    ```

Na stronie **pulpit nawigacyjny** zostanie wyświetlona nowa wartość kluczowego wskaźnika wydajności:

![Ostrzegawczy wskaźnik wydajności](./media/iot-accelerators-remote-monitoring-customize/new-kpi.png)

## <a name="customize-the-map"></a>Dostosuj mapę

Aby uzyskać szczegółowe informacje na temat składników mapy w rozwiązaniu, zobacz stronę [Dostosowywanie mapy](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/wiki/Developer-Reference-Guide#upgrade-map-key-to-see-devices-on-a-dynamic-map) w witrynie GitHub.

<!--
### Connect an external visualization tool

See the [Connect an external visualization tool](https://github.com/Azure/azure-iot-pcs-remote-monitoring-dotnet/) page in GitHub for details of how to connect an external visualization tool.

-->

## <a name="other-customization-options"></a>Inne opcje dostosowywania

Aby dodatkowo zmodyfikować prezentację i warstwę wizualizacji w rozwiązaniu do zdalnego monitorowania, można edytować kod. Odpowiednie repozytoria GitHub są następujące:

* [Mikrousługa konfiguracji dla rozwiązań usługi Azure IoT (.NET)](https://github.com/Azure/remote-monitoring-services-dotnet/tree/master/config)
* [Mikrousługa konfiguracji dla rozwiązań usługi Azure IoT (Java)](https://github.com/Azure/remote-monitoring-services-java/tree/master/config)
* [Interfejs użytkownika sieci Web monitorowania zdalnego usługi Azure IoT PC](https://github.com/Azure/pcs-remote-monitoring-webui)

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o dostępnych zasobach, które ułatwiają Dostosowywanie interfejsu użytkownika sieci Web w akceleratorze rozwiązania do monitorowania zdalnego. Aby dowiedzieć się więcej o dostosowywaniu interfejsu użytkownika, zobacz następujące artykuły:

* [Dodawanie strony niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-page.md)
* [Dodawanie niestandardowej usługi do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-service.md)
* [Dodaj niestandardową siatkę do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-grid.md)
* [Dodawanie niestandardowego okna wysuwanego do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-flyout.md)
* [Dodawanie niestandardowego panelu do pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-panel.md)

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md) .

Aby uzyskać więcej informacji na temat dostosowywania mikrousług rozwiązania do monitorowania zdalnego, zobacz [Dostosowywanie i ponowne wdrażanie mikrousługi](iot-accelerators-microservices-example.md).
<!-- Next tutorials in the sequence -->
