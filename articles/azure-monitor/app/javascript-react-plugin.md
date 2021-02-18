---
title: Wtyczka dla Application Insights JavaScript SDK
description: Jak zainstalować i używać wtyczki reaguje dla Application Insights JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 07/28/2020
ms.openlocfilehash: 4970cacb0995678bdad87f14ba971b8fb88ffa09
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593650"
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

Zainicjuj połączenie z Application Insights:

```javascript
// AppInsights.js
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactPlugin } from '@microsoft/applicationinsights-react-js';
import { createBrowserHistory } from 'history';

const browserHistory = createBrowserHistory({ basename: '' });
const reactPlugin = new ReactPlugin();
const appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [reactPlugin],
        extensionConfig: {
          [reactPlugin.identifier]: { history: browserHistory }
        }
    }
});
appInsights.loadAppInsights();
export { reactPlugin, appInsights };
```

Otoczyć składnik funkcją składnika wyższej kolejności, aby umożliwić Application Insights na nim:

```javascript
import React from 'react';
import { withAITracking } from '@microsoft/applicationinsights-react-js';
import { reactPlugin, appInsights } from './AppInsights';

// To instrument various React components usage tracking, apply the `withAITracking` higher-order
// component function.

class MyComponent extends React.Component {
    ...
}

// withAITracking takes 4 parameters ( reactPlugin, Component, ComponentName, className) 
// the first two are required and the other two are optional.

export default withAITracking(reactPlugin, MyComponent);
```

## <a name="configuration"></a>Konfigurowanie

| Nazwa    | Domyślne | Opis                                                                                                    |
|---------|---------|----------------------------------------------------------------------------------------------------------------|
| historia | null    | Reagowanie na historię routera. Aby uzyskać więcej informacji, zobacz [dokumentację pakietu do reagowania na routery](https://reactrouter.com/web/api/history). Aby dowiedzieć się, jak uzyskać dostęp do obiektu historii poza składnikami, zobacz [często zadawane pytania dotyczące routera](https://github.com/ReactTraining/react-router/blob/master/FAQ.md#how-do-i-access-the-history-object-outside-of-components)    |

### <a name="react-components-usage-tracking"></a>Śledzenie użycia składników reagowania

Aby instrumentować różne śledzenie użycia składników reakcji, Zastosuj `withAITracking` funkcję składnika wyższego rzędu.

Będzie ona mierzyć czas od `ComponentDidMount` zdarzenia za pośrednictwem `ComponentWillUnmount` zdarzenia. Jednak w celu poprawnego polepszenia będzie to odjęcie czasu, w którym użytkownik był w stanie bezczynności `React Component Engaged Time = ComponentWillUnmount timestamp - ComponentDidMount timestamp - idle time` .

Aby wyświetlić tę metrykę w Azure Portal należy przejść do zasobu Application Insights, wybierz kartę "metryki" i skonfiguruj puste wykresy, aby wyświetlić niestandardową nazwę metryki "czas trwania działania składnika (sekundy)", wybierz opcję agregacja (suma, średnia itp.) metryki i Zastosuj podział na "Nazwa składnika".

![Zrzut ekranu przedstawiający wykres pokazujący metrykę niestandardową "czas trwania działania składnika (sekundy)" podzieloną przez "nazwę składnika"](./media/javascript-react-plugin/chart.png)

Możesz również uruchamiać niestandardowe zapytania, aby podzielić Application Insights dane w celu generowania raportów i wizualizacji zgodnie z wymaganiami. W Azure Portal przejdź do zasobu Application Insights, wybierz pozycję "Analiza" z górnego menu karty Przegląd i uruchom zapytanie.

![Zrzut ekranu przedstawiający niestandardowe wyniki zapytania metryki.](./media/javascript-react-plugin/query.png)

> [!NOTE]
> Nowe metryki niestandardowe mogą pojawić się w witrynie Azure Portal dopiero po 10 minutach.

## <a name="using-react-hooks"></a>Korzystanie z punktów zaczepienia reagowania

[Punkty zaczepienia reagowania](https://reactjs.org/docs/hooks-reference.html) to podejście do stanu i zarządzania cyklem życia w aplikacji reagującej bez polegania na składnikach reakcji opartych na klasie. Wtyczka reaguje Application Insights zapewnia wiele integracji podzespołów, które działają w podobny sposób do podejścia do wyższego rzędu.

### <a name="using-react-context"></a>Korzystanie z kontekstu reagowania

Punkty zaczepienia reakcji dla Application Insights są przeznaczone do korzystania z [kontekstu reakcji](https://reactjs.org/docs/context.html) jako zawierającego go aspektu. Aby użyć kontekstu, zainicjuj Application Insights jak powyżej, a następnie zaimportuj obiekt kontekstu:

```javascript
import React from "react";
import { AppInsightsContext } from "@microsoft/applicationinsights-react-js";
import { reactPlugin } from "./AppInsights";

const App = () => {
    return (
        <AppInsightsContext.Provider value={reactPlugin}>
            /* your application here */
        </AppInsightsContext.Provider>
    );
};
```

Ten dostawca kontekstu udostępni Application Insights jako punkt `useContext` zaczepienia we wszystkich składnikach podrzędnych.

```javascript
import React from "react";
import { useAppInsightsContext } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    
    appInsights.trackMetric("Component 'MyComponent' is in use");
    
    return (
        <h1>My Component</h1>
    );
}
export default MyComponent;
```

### `useTrackMetric`

Punkt `useTrackMetric` zaczepienia replikuje funkcje `withAITracking` składnika wyższej kolejności, bez dodawania dodatkowego składnika do struktury składnika. Punkt zaczepienia przyjmuje dwa argumenty, najpierw jest wystąpieniem Application Insights (które można uzyskać z punktu `useAppInsightsContext` zaczepienia) i identyfikatorem składnika do śledzenia (na przykład jego nazwy).

```javascript
import React from "react";
import { useAppInsightsContext, useTrackMetric } from "@microsoft/applicationinsights-react-js";

const MyComponent = () => {
    const appInsights = useAppInsightsContext();
    const trackComponent = useTrackMetric(appInsights, "MyComponent");
    
    return (
        <h1 onHover={trackComponent} onClick={trackComponent}>My Component</h1>
    );
}
export default MyComponent;
```

Będzie działać jak składnik wyższej kolejności, ale reaguje na Podłączanie zdarzeń cyklu życia, a nie cyklu życia składnika. Punkt zaczepienia musi być jawnie udostępniony dla zdarzeń użytkownika, jeśli istnieje potrzeba uruchomienia w określonych interakcjach.

### `useTrackEvent`

`useTrackEvent`Punkt zaczepienia służy do śledzenia dowolnego zdarzenia niestandardowego, które może być potrzebne do śledzenia aplikacji, takich jak kliknięcie przycisku lub inne wywołanie interfejsu API. Przyjmuje dwa argumenty, pierwsze jest wystąpieniem Application Insights (które można uzyskać z punktu `useAppInsightsContext` zaczepienia) i nazwą zdarzenia.

```javascript
import React, { useState, useEffect } from "react";
import { useAppInsightsContext, useTrackEvent } from "@microsoft/applicationinsights-react-js";

const ProductCart = () => {
    const appInsights = useAppInsightsContext();
    const trackCheckout = useTrackEvent(appInsights, "Checkout");
    const trackCartUpdate = useTrackEvent(appInsights, "Cart Updated");
    const [cart, setCart] = useState([]);
    
    useEffect(() => {
        trackCartUpdate({ cartCount: cart.length });
    }, [cart]);
    
    const performCheckout = () => {
        trackCheckout();
        // submit data
    };
    
    return (
        <div>
            <ul>
                <li>Product 1 <button onClick={() => setCart([...cart, "Product 1"])}>Add to Cart</button>
                <li>Product 2 <button onClick={() => setCart([...cart, "Product 2"])}>Add to Cart</button>
                <li>Product 3 <button onClick={() => setCart([...cart, "Product 3"])}>Add to Cart</button>
                <li>Product 4 <button onClick={() => setCart([...cart, "Product 4"])}>Add to Cart</button>
            </ul>
            <button onClick={performCheckout}>Checkout</button>
        </div>
    );
}
export default MyComponent;
```

Gdy punkt zaczepienia jest używany, do tego celu można dodać do zdarzenia dodatkowe dane, gdy jest on przechowywany w Application Insights.

## <a name="react-error-boundaries"></a>Reagowanie na granice błędów

[Granice błędów](https://reactjs.org/docs/error-boundaries.html) umożliwiają bezpieczne obsłużenie wyjątku, gdy występuje w aplikacji do reagowania, a w przypadku wystąpienia takiego błędu prawdopodobnie należy zarejestrować wyjątek. Wtyczka do reagowania na Application Insights zawiera składnik granicy błędu, który spowoduje automatyczne zarejestrowanie błędu, gdy wystąpi.

```javascript
import React from "react";
import { reactPlugin } from "./AppInsights";
import { AppInsightsErrorBoundary } from "@microsoft/applicationinsights-react-js";

const App = () => {
    return (
        <AppInsightsErrorBoundary onError={() => <h1>I believe something went wrong</h1>} appInsights={reactPlugin}>
            /* app here */
        </AppInsightsErrorBoundary>
    );
};
```

Do tego celu są `AppInsightsErrorBoundary` wymagane dwa propy, `ReactPlugin` wystąpienie utworzone dla aplikacji i składnika, które mają być renderowane po wystąpieniu błędu. Gdy wystąpi nieobsługiwany błąd, `trackException` jest wywoływany z informacjami podanymi w granicy błędu, a `onError` składnik jest wyświetlany.

## <a name="sample-app"></a>Przykładowa aplikacja

Zapoznaj się z [pokazem Application Insights reagują](https://github.com/Azure-Samples/application-insights-react-demo).

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript, zapoznaj się z [dokumentacją zestawu SDK w Application Insights JavaScript](javascript.md).
- Aby dowiedzieć się więcej na temat języka zapytań Kusto i wykonywania zapytań dotyczących danych w Log Analytics, zobacz [Omówienie zapytania dziennika](../../azure-monitor/logs/log-query-overview.md).
