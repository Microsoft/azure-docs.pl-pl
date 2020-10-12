---
title: Dodawanie siatki do interfejsu użytkownika rozwiązania do monitorowania zdalnego — Azure | Microsoft Docs
description: W tym artykule opisano sposób dodawania nowego identyfikatora GID na stronie w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/04/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: 7fc878e0c9e099b201264c1c3981c603668214d3
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318385"
---
# <a name="add-a-custom-grid-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodaj niestandardową siatkę do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego

W tym artykule opisano sposób dodawania nowej siatki na stronie w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego. W tym artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nową siatkę do strony w interfejsie użytkownika sieci Web.

W przykładowej siatce w tym artykule przedstawiono dane z usługi, które [dodają usługę niestandardową do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-service.md) , jak dodać.

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz zainstalować następujące oprogramowanie na lokalnym komputerze deweloperskim:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać czynności opisane w następujących artykułach:

- [Dodaj niestandardową stronę do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-page.md).
- [Dodawanie niestandardowej usługi do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-service.md)

## <a name="add-a-grid"></a>Dodawanie siatki

Aby dodać siatkę do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe, które definiują siatkę i zmodyfikować niektóre istniejące pliki, aby interfejs użytkownika sieci Web miał świadomość nowego składnika.

### <a name="add-the-new-files-that-define-the-grid"></a>Dodaj nowe pliki, które definiują siatkę

Aby rozpocząć pracę, folder **src/Przewodnik/składniki/strony/pageWithGrid/exampleGrid** zawiera pliki, które definiują siatkę:

**exampleGrid.js**

[!code-javascript[Example grid](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGrid.js?name=grid "Example grid")]

**exampleGridConfig.js**

[!code-javascript[Example grid configuration](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithGrid/exampleGrid/exampleGridConfig.js?name=gridconfig "Example grid configuration")]

Skopiuj folder **src/Instruktaż/Components/Pages/pageWithGrid/exampleGrid** do folderu **src/Components/Pages/example** .

### <a name="add-the-grid-to-the-page"></a>Dodaj siatkę do strony

Zmodyfikuj **elementy src/Components/Pages/example/basicPage.container.js** w następujący sposób, aby zaimportować definicje usługi:

```js
import { connect } from 'react-redux';
import { translate } from 'react-i18next';

import {
  epics as exampleEpics,
  getExamples,
  getExamplesError,
  getExamplesLastUpdated,
  getExamplesPendingStatus
} from 'store/reducers/exampleReducer';
import { BasicPage } from './basicPage';

// Pass the data
const mapStateToProps = state => ({
  data: getExamples(state),
  error: getExamplesError(state),
  isPending: getExamplesPendingStatus(state),
  lastUpdated: getExamplesLastUpdated(state)
});

// Wrap the dispatch method
const mapDispatchToProps = dispatch => ({
  fetchData: () => dispatch(exampleEpics.actions.fetchExamples())
});

export const BasicPageContainer = translate()(connect(mapStateToProps, mapDispatchToProps)(BasicPage));
```

Zmodyfikuj element **src/Components/Pages/example/basicPage.js** w następujący sposób, aby dodać siatkę:

```js
// Copyright (c) Microsoft. All rights reserved.

import React, { Component } from 'react';

import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';

import './basicPage.css';

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null };
  }

  componentDidMount() {
    const { isPending, lastUpdated, fetchData } = this.props;
    if (!lastUpdated && !isPending) fetchData();
  }

  onGridReady = gridReadyEvent => this.gridApi = gridReadyEvent.api;

  onContextMenuChange = contextBtns => this.setState({ contextBtns });

  render() {
    const { t, data, error, isPending, lastUpdated, fetchData } = this.props;
    const gridProps = {
      onGridReady: this.onGridReady,
      rowData: isPending ? undefined : data || [],
      onContextMenuChange: this.onContextMenuChange,
      t: this.props.t
    };

    return [
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
      </ContextMenu>,
      <PageContent className="basic-page-container" key="page-content">
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
    ];
  }
}
```

Zmodyfikuj element **src/Components/Pages/example/basicPage.test.js** w następujący sposób, aby zaktualizować testy:

```js
// Copyright (c) Microsoft. All rights reserved.

import React from 'react';
import { shallow } from 'enzyme';
import 'polyfills';

import { BasicPage } from './basicPage';

describe('BasicPage Component', () => {
  it('Renders without crashing', () => {

    const fakeProps = {
      data: undefined,
      error: undefined,
      isPending: false,
      lastUpdated: undefined,
      fetchData: () => { },
      t: () => { },
    };

    const wrapper = shallow(
      <BasicPage {...fakeProps} />
    );
  });
});
```

## <a name="test-the-grid"></a>Testowanie siatki

Jeśli interfejs użytkownika sieci Web nie jest jeszcze uruchomiony lokalnie, uruchom następujące polecenie w folderze głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejs użytkownika lokalnie w `http://localhost:3000/dashboard` . Przejdź do **przykładowej** strony, aby wyświetlić dane z usługi Grid.

## <a name="select-rows"></a>Zaznacz wiersze

Dostępne są dwie opcje umożliwiające użytkownikowi wybranie wierszy w siatce:

### <a name="hard-select-rows"></a>Zaznaczanie wierszy

Jeśli użytkownik musi działać na wielu wierszach w tym samym czasie, Użyj pól wyboru w wierszach:

1. Włącz twarde Wybieranie wierszy, dodając **checkboxColumn** do **columnDefs** dostarczonego do siatki. **checkboxColumn** jest zdefiniowana w **/src/Components/Shared/pcsGrid/pcsGrid.js**:

    ```js
    this.columnDefs = [
      checkboxColumn,
      exampleColumnDefs.id,
      exampleColumnDefs.description
    ];
    ```

1. Aby uzyskać dostęp do wybranych elementów, uzyskasz odwołanie do wewnętrznego interfejsu API siatki:

    ```js
    onGridReady = gridReadyEvent => {
      this.gridApi = gridReadyEvent.api;
      // Call the onReady props if it exists
      if (isFunc(this.props.onGridReady)) {
        this.props.onGridReady(gridReadyEvent);
      }
    };
    ```

1. Udostępnij przyciski kontekstowe na stronie, gdy wiersz w siatce jest wybrany na stałe:

    ```js
    this.contextBtns = [
      <Btn key="context-btn-1" svg={svgs.reconfigure} onClick={this.doSomething()}>Button 1</Btn>,
      <Btn key="context-btn-2" svg={svgs.trash} onClick={this.doSomethingElse()}>Button 2</Btn>
    ];
    ```

    ```js
    onHardSelectChange = (selectedObjs) => {
      const { onContextMenuChange, onHardSelectChange } = this.props;
      // Show the context buttons when there are rows checked.
      if (isFunc(onContextMenuChange)) {
        onContextMenuChange(selectedObjs.length > 0 ? this.contextBtns : null);
      }
      //...
    }
    ```

1. Po kliknięciu przycisku kontekstowego Pobierz twarde wybrane elementy, aby wykonać swoją służbę:

    ```js
    doSomething = () => {
      //Just for demo purposes. Don't console log in a real grid.
      console.log('Hard selected rows', this.gridApi.getSelectedRows());
    };
    ```

### <a name="soft-select-rows"></a>Niewygładzone Zaznaczanie wierszy

Jeśli użytkownik musi działać tylko na jednym wierszu, skonfiguruj link niezależny dla jednej lub kilku kolumn w **columnDefs**.

1. W **exampleGridConfig.js**Dodaj **SoftSelectLinkRenderer** jako **cellRendererFramework** dla **columnDef**.

    ```js
    export const exampleColumnDefs = {
      id: {
        headerName: 'examples.grid.name',
        field: 'id',
        sort: 'asc',
        cellRendererFramework: SoftSelectLinkRenderer
      }
    };
    ```

1. Po kliknięciu łącza Soft-SELECT wyzwala zdarzenie **onSoftSelectChange** . Wykonaj dowolną akcję odpowiednią dla tego wiersza, na przykład otwierając okno wysuwane szczegółów. Ten przykład po prostu zapisuje dane w konsoli:

    ```js
    onSoftSelectChange = (rowId, rowData) => {
      //Note: only the Id is reliable, rowData may be out of date
      const { onSoftSelectChange } = this.props;
      if (rowId) {
        //Just for demo purposes. Don't console log a real grid.
        console.log('Soft selected', rowId);
        this.setState({ softSelectedId: rowId });
      }
      if (isFunc(onSoftSelectChange)) {
        onSoftSelectChange(rowId, rowData);
      }
    }
    ```

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o dostępnych zasobach, które ułatwiają dodawanie i dostosowywanie stron w interfejsie użytkownika sieci Web w akceleratorze rozwiązania do zdalnego monitorowania.

Po zdefiniowaniu siatki następnym krokiem jest [dodanie niestandardowego menu wysuwanego do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-flyout.md) , który jest wyświetlany na przykładowej stronie.

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
