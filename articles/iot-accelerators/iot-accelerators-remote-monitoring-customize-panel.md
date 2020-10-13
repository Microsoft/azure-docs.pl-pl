---
title: Dodawanie panelu do interfejsu użytkownika rozwiązania do monitorowania zdalnego — Azure | Microsoft Docs
description: W tym artykule pokazano, jak dodać nowy panel do pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 1dcca8409022ba4cf1f988b7c777e3a1fa511060
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318368"
---
# <a name="add-a-custom-panel-to-the-dashboard-in-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie niestandardowego panelu do pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego

W tym artykule opisano sposób dodawania nowego panelu na stronie pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego. W tym artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nowy panel do strony pulpitu nawigacyjnego w interfejsie użytkownika sieci Web.

Panel przykładowy w tym artykule jest wyświetlany na istniejącej stronie pulpitu nawigacyjnego.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz zainstalować następujące oprogramowanie na lokalnym komputerze deweloperskim:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać kroki opisane w artykule [Dodawanie niestandardowej strony do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-page.md) .

## <a name="add-a-panel"></a>Dodawanie panelu

Aby dodać panel do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe, które definiują panel, a następnie zmodyfikować pulpit nawigacyjny w taki sposób, aby wyświetlał panel.

### <a name="add-the-new-files-that-define-the-panel"></a>Dodaj nowe pliki, które definiują panel

Aby rozpocząć pracę, folder **src/Przewodnik/składniki/strony/pulpit nawigacyjny/panele/examplePanel** zawiera pliki definiujące panel, w tym:

**examplePanel.js**

[!code-javascript[Example panel](~/remote-monitoring-webui/src/walkthrough/components/pages/dashboard/panels/examplePanel/examplePanel.js?name=panel "Example panel")]

Skopiuj folder **src/Instruktaż/Components/Pages/Dashboard/Paneles/examplePanel** do folderu **src/Components/Pages/Dashboard/Paneles** .

Dodaj następujący eksport do pliku **src/Przewodnik/Components/Pages/Dashboard/Paneles/index.js** :

```js
export * from './examplePanel';
```

### <a name="add-the-panel-to-the-dashboard"></a>Dodawanie panelu do pulpitu nawigacyjnego

Zmodyfikuj element **src/Components/Pages/Dashboard/dashboard.js** , aby dodać panel.

Dodaj przykładowy panel do listy importy z paneli:

```js
import {
  OverviewPanel,
  AlertsPanel,
  TelemetryPanel,
  AnalyticsPanel,
  MapPanel,
  transformTelemetryResponse,
  chartColorObjects,
  ExamplePanel
} from './panels';
```

Dodaj następującą definicję komórki do siatki w zawartości strony:

```js
          <Cell className="col-2">
            <ExamplePanel t={t} />
          </Cell>
```

## <a name="test-the-flyout"></a>Testowanie okna wysuwanego

Jeśli interfejs użytkownika sieci Web nie jest jeszcze uruchomiony lokalnie, uruchom następujące polecenie w folderze głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejs użytkownika lokalnie w `http://localhost:3000/dashboard` . Przejdź do strony **pulpitu nawigacyjnego** , aby wyświetlić nowy panel.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o dostępnych zasobach, które ułatwiają dodawanie i dostosowywanie pulpitów nawigacyjnych w interfejsie użytkownika sieci Web w akceleratorze rozwiązania do zdalnego monitorowania.

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
