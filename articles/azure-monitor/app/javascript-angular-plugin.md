---
title: Wtyczka kątowa dla Application Insights JavaScript SDK
description: Jak zainstalować wtyczkę kątową dla Application Insights JavaScript SDK i korzystać z niej.
services: azure-monitor
author: lgayhardt
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 10/07/2020
ms.author: lagayhar
ms.openlocfilehash: d45d8bed328dc91dfeeabd6ce878074fa1218623
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101737022"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Wtyczka kątowa dla Application Insights JavaScript SDK

Wtyczka kątowa dla Application Insights JavaScript SDK umożliwia:

- Śledzenie zmian w routerze

> [!WARNING]
> Wtyczka kątowa nie jest zgodna z językiem ECMAScript 3 (ES3).

## <a name="getting-started"></a>Wprowadzenie

Zainstaluj pakiet npm:

```bash
npm install @microsoft/applicationinsights-angularplugin-js
```

## <a name="basic-usage"></a>Podstawowy sposób użycia

Skonfiguruj wystąpienie Application Insights w składniku wprowadzania w aplikacji:

```js
import { Component } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent {
    constructor(
        private router: Router
    ){
        var angularPlugin = new AngularPlugin();
        const appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
        appInsights.loadAppInsights();
    }
}
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript, zobacz [dokumentację zestawu SDK w Application Insights JavaScript](javascript.md)
- [Wtyczka kątowa w serwisie GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)
