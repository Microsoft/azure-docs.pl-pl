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
ms.openlocfilehash: 152ba4b1c8a4e09db0bce759f5b67f577ec5d584
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91844029"
---
# <a name="angular-plugin-for-application-insights-javascript-sdk"></a>Wtyczka kątowa dla Application Insights JavaScript SDK

Wtyczka kątowa dla Application Insights JavaScript SDK umożliwia:

- Śledzenie zmian w routerze
- Statystyki użycia składników kątowych

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
import { Component, OnInit } from '@angular/core';
import { ApplicationInsights } from '@microsoft/applicationinsights-web';
import { AngularPlugin, AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';
import { Router } from '@angular/router';

@Component({
  selector: 'app-root',
  templateUrl: './app.component.html',
  styleUrls: ['./app.component.css']
})
export class AppComponent implements OnInit {
    private appInsights;
    constructor(
        private router: Router,
        private angularPluginService: AngularPluginService 
    ){
        var angularPlugin = new AngularPlugin();
        this.angularPluginService.init(angularPlugin, this.router);
        this.appInsights = new ApplicationInsights({ config: {
        instrumentationKey: 'YOUR_INSTRUMENTATION_KEY_GOES_HERE',
        extensions: [angularPlugin],
        extensionConfig: {
            [angularPlugin.identifier]: { router: this.router }
        }
        } });
    }

    ngOnInit() {
        this.appInsights.loadAppInsights();
    }
}

```

Aby użyć `trackMetric` metody do śledzenia użycia składnika kątowego, Dodaj `AngularPluginService` jako dostawcę na liście dostawców w `app.module.ts` pliku.

```js
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@NgModule({
    ...
  providers: [ AngularPluginService ],
})
export class AppModule { }
```

Aby śledzić okres istnienia składnika, należy wywołać `trackMetric` metodę tego `ngOnDestroy` składnika. Gdy składnik zostanie zniszczony, zostanie wyzwolone `trackMetric` zdarzenie, które wysyła użytkownika na stronę i nazwę składnika.

```js
import { Component, OnDestroy, HostListener } from '@angular/core';
import { AngularPluginService } from '@microsoft/applicationinsights-angularplugin-js';

@Component({
  selector: 'app-test',
  templateUrl: './test.component.html',
  styleUrls: ['./test.component.css']
})
export class TestComponent implements OnDestroy {

  constructor(private angularPluginService: AngularPluginService) {}

  @HostListener('window:beforeunload')
  ngOnDestroy() {
    this.angularPluginService.trackMetric();
  }
}
```

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej na temat zestawu SDK języka JavaScript, zobacz [dokumentację zestawu SDK w Application Insights JavaScript](javascript.md)
- [Wtyczka kątowa w serwisie GitHub](https://github.com/microsoft/ApplicationInsights-JS/tree/master/extensions/applicationinsights-angularplugin-js)