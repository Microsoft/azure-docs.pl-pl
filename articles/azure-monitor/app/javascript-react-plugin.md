---
title: Wtyczka dla Application Insights JavaScript SDK
description: Jak zainstalować i używać wtyczki reaguje dla Application Insights JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 3a11f77384c520bed9824841269be4ad998adba4
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90056204"
---
# <a name="react-plugin-for-application-insights-javascript-sdk"></a>Wtyczka dla Application Insights JavaScript SDK

Dodatek plug-in do Application Insights JavaScript SDK, umożliwia:

- Śledzenie zmian trasy
- Statystyka użycia składników reagowania

## <a name="getting-started"></a>Wprowadzenie

Zainstaluj pakiet npm:

```bash

npm install @microsoft/applicationinsights-react-js

```

## <a name="basic-usage"></a>Podstawowy sposób użycia

```javascript
import React from 'react';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin, withAITracking } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from "history";

const browserHistory = createBrowserHistory({ basename: '' });
var reactPlugin = new ReactPlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

export default withAITracking(reactPlugin,appInsights, MyComponent);

```

## <a name="configuration"></a>Konfiguracja

| Nazwa    | Domyślne | Opis                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| historia | wartość null    | Reagowanie na historię routera. Aby uzyskać więcej informacji, zobacz [dokumentację pakietu do reagowania na routery](https://reactrouter.com/web/api/history). Aby dowiedzieć się, jak uzyskać dostęp do obiektu historii poza składnikami, zobacz [często zadawane pytania dotyczące routera](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Śledzenie użycia składników reagowania

Aby instrumentować różne śledzenie użycia składników reakcji, Zastosuj `withAITracking` funkcję składnika wyższego rzędu.

Będzie ona mierzyć czas od `ComponentDidMount` zdarzenia za pośrednictwem `ComponentWillUnmount` zdarzenia. Jednak w celu poprawnego polepszenia będzie to odjęcie czasu, w którym użytkownik był w stanie bezczynności `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Aby wyświetlić tę metrykę w Azure Portal należy przejść do zasobu Application Insights, wybierz kartę "metryki" i skonfiguruj puste wykresy, aby wyświetlić niestandardową nazwę metryki "czas trwania działania składnika (sekundy)", wybierz opcję agregacja (suma, średnia itp.) metryki i Zastosuj podział na "Nazwa składnika".

![Zrzut ekranu przedstawiający wykres pokazujący metrykę niestandardową "czas trwania działania składnika (sekundy)" podzieloną przez "nazwę składnika"](./media/javascript-react-plugin/chart.png)

Możesz również uruchamiać niestandardowe zapytania, aby podzielić Application Insights dane w celu generowania raportów i wizualizacji zgodnie z wymaganiami. W Azure Portal przejdź do zasobu Application Insights, wybierz pozycję "Analiza" z górnego menu karty Przegląd i uruchom zapytanie.

![Zrzut ekranu przedstawiający niestandardowe wyniki zapytania metryki.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Nowe metryki niestandardowe mogą pojawić się w witrynie Azure Portal dopiero po 10 minutach.

## <a name="sample-app"></a>Przykładowa aplikacja

Zapoznaj się z [pokazem Application Insights reagują](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript, zapoznaj się z [dokumentacją zestawu SDK w Application Insights JavaScript](javascript.md).
- Aby dowiedzieć się więcej na temat języka zapytań Kusto i wykonywania zapytań dotyczących danych w Log Analytics, zobacz [Omówienie zapytania dziennika](../../azure-monitor/log-query/log-query-overview.md).
