---
title: Wtyczka do obsługi natywnej reakcji dla Application Insights JavaScript SDK
description: Jak zainstalować i korzystać z natywnej wtyczki obsługi w programie Application Insights JavaScript SDK.
services: azure-monitor
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/06/2020
ms.openlocfilehash: 0c122a21fc7149e9943825cafbed77069b7919f3
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100593590"
---
# <a name="native-react-plugin-for-application-insights-javascript-sdk"></a>Wtyczka do obsługi natywnej reakcji dla Application Insights JavaScript SDK

Natywna wtyczka do reagowania na Application Insights JavaScript SDK zbiera informacje o urządzeniu, domyślnie ten Wtyczka automatycznie zbiera:

- **Unikatowy identyfikator urządzenia** (znany również jako identyfikator instalacji).
- **Nazwa modelu urządzenia** (na przykład iPhone X, oprogramowanie Samsung Galaxy, Huawei P30 Pro itp.)
- **Typ urządzenia** (na przykład słuchawka, tablet itp.)

## <a name="requirements"></a>Wymagania

Musisz używać wersji >= 2.0.0 z `@microsoft/applicationinsights-web` . Ta wtyczka będzie działała tylko w aplikacjach aplikacji natywnych. Nie będzie ona działała z [aplikacjami korzystającymi z platformy atrakcyjnymi](https://docs.expo.io/), dlatego nie będzie działała z aplikacją do tworzenia aplikacji natywnych.

## <a name="getting-started"></a>Wprowadzenie

Zainstaluj i Połącz pakiet reinstallation- [Native-info](https://www.npmjs.com/package/react-native-device-info) Aktualizuj `react-native-device-info` pakiet, aby zebrać najnowsze nazwy urządzeń za pomocą aplikacji.

```zsh

npm install --save @microsoft/applicationinsights-react-native @microsoft/applicationinsights-web
npm install --save react-native-device-info
react-native link react-native-device-info

```

## <a name="initializing-the-plugin"></a>Inicjowanie wtyczki

Aby użyć tej wtyczki, należy skonstruować wtyczkę i dodać ją jako `extension` do istniejącego wystąpienia Application Insights.

```typescript
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { ReactNativePlugin } from '@microsoft/applicationinsights-react-native';

var RNPlugin = new ReactNativePlugin();
var appInsights = new ApplicationInsights({
    config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [RNPlugin]
    }
});
appInsights.loadAppInsights();

```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript, zapoznaj się z [dokumentacją zestawu SDK w Application Insights JavaScript](javascript.md).
- Aby dowiedzieć się więcej na temat języka zapytań Kusto i wykonywania zapytań dotyczących danych w Log Analytics, zobacz [Omówienie zapytania dziennika](../../azure-monitor/logs/log-query-overview.md).
