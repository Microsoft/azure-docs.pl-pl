---
title: Dodawanie usługi do interfejsu użytkownika rozwiązania do monitorowania zdalnego — Azure | Microsoft Docs
description: W tym artykule opisano sposób dodawania nowej usługi do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/02/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 6689a6d8a19dd690aa19aeecd29ead35ef6c2db1
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318351"
---
# <a name="add-a-custom-service-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie niestandardowej usługi do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego

W tym artykule opisano sposób dodawania nowej usługi do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego. W tym artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nową usługę do interfejsu użytkownika sieci Web.

Przykładowa usługa w tym artykule zawiera dane dla siatki, które [dodają niestandardową siatkę do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-grid.md) , który pokazuje, jak dodać.

W aplikacji reagującej usługa zazwyczaj współdziała z usługą zaplecza. Przykłady w akceleratorze rozwiązania do monitorowania zdalnego obejmują usługi, które współpracują z menedżerem IoT Hub i mikrousługami konfiguracji.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz zainstalować następujące oprogramowanie na lokalnym komputerze deweloperskim:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać kroki opisane w artykule [Dodawanie niestandardowej strony do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-page.md) .

## <a name="add-a-service"></a>Dodawanie usługi

Aby dodać usługę do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe, które definiują usługę, i zmodyfikować niektóre istniejące pliki, aby interfejs użytkownika sieci Web miał świadomość nowej usługi.

### <a name="add-the-new-files-that-define-the-service"></a>Dodawanie nowych plików, które definiują usługę

Aby rozpocząć pracę, folder **src/Instruktaż/Services** zawiera pliki, które definiują prostą usługę:

**exampleService.js**

[!code-javascript[Example service](~/remote-monitoring-webui/src/walkthrough/services/exampleService.js?name=service "Example service")]

Aby dowiedzieć się więcej o sposobie implementacji usług, zobacz [wprowadzenie do aktywnego programowania](https://gist.github.com/staltz/868e7e9bc2a7b8c1f754).

**Model/exampleModels.js**

[!code-javascript[Example model](~/remote-monitoring-webui/src/walkthrough/services/models/exampleModels.js?name=models "Example model")]

Skopiuj **exampleService.js** do folderu **src/services** i skopiuj **exampleModels.js** do folderu **src/Services/models** .

Zaktualizuj plik **index.js** w folderze **src/Services** w celu wyeksportowania nowej usługi:

```js
export * from './exampleService';
```

Zaktualizuj plik **index.js** w folderze **src/Services/models** , aby wyeksportować nowy model:

```js
export * from './exampleModels';
```

### <a name="set-up-the-calls-to-the-service-from-the-store"></a>Konfigurowanie wywołań usługi ze sklepu

Aby rozpocząć pracę, folder **src/Przewodnik/sklep/ograniczenia** zawiera przykładowy składnik:

**exampleReducer.js**

[!code-javascript[Example reducer](~/remote-monitoring-webui/src/walkthrough/store/reducers/exampleReducer.js?name=reducer "Example reducer")]

Skopiuj **exampleReducer.js** do folderu **src/Store/redukowaćs** .

Aby dowiedzieć się więcej o obniżyć i **epiki**, zobacz [Redux](https://redux-observable.js.org/).

### <a name="configure-the-middleware"></a>Konfigurowanie oprogramowania pośredniczącego

Aby skonfigurować oprogramowanie pośredniczące, Dodaj program do pliku **rootReducer.js** w folderze **src/Store** :

```js
import { reducer as exampleReducer } from './reducers/exampleReducer';

const rootReducer = combineReducers({
  ...appReducer,
  ...devicesReducer,
  ...rulesReducer,
  ...simulationReducer,
  ...exampleReducer
});
```

Dodaj epiki do pliku **rootEpics.js** w folderze **src/Store** :

```js
import { epics as exampleEpics } from './reducers/exampleReducer';

// Extract the epic function from each property object
const epics = [
  ...appEpics.getEpics(),
  ...devicesEpics.getEpics(),
  ...rulesEpics.getEpics(),
  ...simulationEpics.getEpics(),
  ...exampleEpics.getEpics()
];
```

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o dostępnych zasobach, które ułatwiają dodawanie i dostosowywanie usług w interfejsie użytkownika sieci Web w akceleratorze rozwiązania do zdalnego monitorowania.

Po zdefiniowaniu usługi, następnym krokiem jest [dodanie siatki niestandardowej do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-grid.md) , który wyświetla dane zwrócone przez usługę.

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
