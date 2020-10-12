---
title: Dodawanie okna wysuwanego do interfejsu użytkownika rozwiązania do monitorowania zdalnego — Azure | Microsoft Docs
description: W tym artykule pokazano, jak dodać nowe menu wysuwane na stronie w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 10/05/2018
ms.topic: conceptual
ms.custom: devx-track-js
ms.openlocfilehash: afd8c5b01b9f36606bf6bd43fbed747ca83ba320
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91318419"
---
# <a name="add-a-custom-flyout-to-the-remote-monitoring-solution-accelerator-web-ui"></a>Dodawanie niestandardowego okna wysuwanego do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego

W tym artykule pokazano, jak dodać nowe menu wysuwane na stronie w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego. W tym artykule opisano:

- Jak przygotować lokalne środowisko programistyczne.
- Jak dodać nowe okno wysuwane do strony w interfejsie użytkownika sieci Web.

Przykładowa kontrolka wysuwana w tym artykule jest wyświetlana na stronie z siatką, w której dowiesz się, jak dodać [niestandardową siatkę do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-grid.md) .

## <a name="prerequisites"></a>Wymagania wstępne

Aby wykonać kroki opisane w tym przewodniku, musisz zainstalować następujące oprogramowanie na lokalnym komputerze deweloperskim:

- [Git](https://git-scm.com/downloads)
- [Node.js](https://nodejs.org/download/)

## <a name="before-you-start"></a>Przed rozpoczęciem

Przed kontynuowaniem należy wykonać czynności opisane w następujących artykułach:

- [Dodaj niestandardową stronę do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-page.md).
- [Dodawanie niestandardowej usługi do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-service.md)
- [Dodaj niestandardową siatkę do interfejsu użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-grid.md)

## <a name="add-a-flyout"></a>Dodawanie okna wysuwanego

Aby dodać okno wysuwania do interfejsu użytkownika sieci Web, należy dodać pliki źródłowe, które definiują menu wysuwane, i zmodyfikować niektóre istniejące pliki, aby interfejs użytkownika sieci Web miał świadomość nowego składnika.

### <a name="add-the-new-files-that-define-the-flyout"></a>Dodaj nowe pliki, które definiują menu wysuwane

Aby rozpocząć pracę, folder **src/Przewodnik/składniki/strony/pageWithFlyout/menu wysuwane/exampleFlyout** zawiera pliki, które definiują menu wysuwane:

**exampleFlyout.container.js**

[!code-javascript[Example flyout container](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.container.js?name=flyoutcontainer "Example flyout container")]

**exampleFlyout.js**

[!code-javascript[Example flyout](~/remote-monitoring-webui/src/walkthrough/components/pages/pageWithFlyout/flyouts/exampleFlyout/exampleFlyout.js?name=flyout "Example flyout")]

Skopiuj folder **src/Przewodnik/Components/Pages/pageWithFlyout/menu** podfoldery do folderu **src/Components/Pages/example** .

### <a name="add-the-flyout-to-the-page"></a>Dodawanie menu wysuwanego do strony

Zmodyfikuj element **src/Components/Pages/example/basicPage.js** , aby dodać menu wysuwane.

Dodaj **BTN** do importów z **komponentów/udostępnionych** i Dodaj Importy dla **svgs** i **ExampleFlyoutContainer**:

```js
import {
  AjaxError,
  ContextMenu,
  PageContent,
  RefreshBar,
  Btn
} from 'components/shared';
import { ExampleGrid } from './exampleGrid';
import { svgs } from 'utilities';
import { ExampleFlyoutContainer } from './flyouts/exampleFlyout';
```

Dodaj definicję **const** dla **closedFlyoutState** i Dodaj ją do stanu w konstruktorze:

```js
const closedFlyoutState = { openFlyoutName: undefined };

export class BasicPage extends Component {
  constructor(props) {
    super(props);
    this.state = { contextBtns: null, closedFlyoutState };
  }
```

Dodaj następujące funkcje do klasy **od basicpage** :

```js
  closeFlyout = () => this.setState(closedFlyoutState);

  openFlyout = (name) => () => this.setState({ openFlyoutName: name });
```

Dodaj następujące definicje **const** do funkcji **renderowania** :

```js
    const { openFlyoutName } = this.state;

    const isExampleFlyoutOpen = openFlyoutName === 'example';
```

Dodaj przycisk, aby otworzyć okno wysuwane do menu kontekstowego:

```js
      <ContextMenu key="context-menu">
        {this.state.contextBtns}
        <Btn svg={svgs.reconfigure} onClick={this.openFlyout('example')}>{t('walkthrough.pageWithFlyout.open')}</Btn>
      </ContextMenu>,
```

Dodaj tekst i kontener wysuwany do zawartości strony:

```js
      <PageContent className="basic-page-container" key="page-content">
        {t('walkthrough.pageWithFlyout.pageBody')}
        { isExampleFlyoutOpen && <ExampleFlyoutContainer onClose={this.closeFlyout} /> }
        <RefreshBar refresh={fetchData} time={lastUpdated} isPending={isPending} t={t} />
        {!!error && <AjaxError t={t} error={error} />}
        {!error && <ExampleGrid {...gridProps} />}
      </PageContent>
```

## <a name="test-the-flyout"></a>Testowanie okna wysuwanego

Jeśli interfejs użytkownika sieci Web nie jest jeszcze uruchomiony lokalnie, uruchom następujące polecenie w folderze głównym lokalnej kopii repozytorium:

```cmd/sh
npm start
```

Poprzednie polecenie uruchamia interfejs użytkownika lokalnie w `http://localhost:3000/dashboard` . Przejdź do strony **przykład** , a następnie kliknij pozycję **Otwórz okno wysuwane**.

## <a name="next-steps"></a>Następne kroki

Ten artykuł zawiera informacje o dostępnych zasobach, które ułatwiają dodawanie i dostosowywanie stron w interfejsie użytkownika sieci Web w akceleratorze rozwiązania do zdalnego monitorowania.

Po zdefiniowaniu menu wysuwanego na stronie, następnym krokiem jest [dodanie panelu do pulpitu nawigacyjnego w interfejsie użytkownika sieci Web akceleratora rozwiązania do monitorowania zdalnego](iot-accelerators-remote-monitoring-customize-panel.md).

Aby uzyskać więcej informacji o pojęciach dotyczących akceleratora rozwiązania do monitorowania zdalnego, zobacz [Architektura zdalnego monitorowania](iot-accelerators-remote-monitoring-sample-walkthrough.md).
