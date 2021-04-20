---
title: Implementowanie widżetów w portalu dla deweloperów
titleSuffix: Azure API Management
description: Dowiedz się, jak implementować widżety, które zużywają dane z zewnętrznych interfejsów API i wyświetlać je w API Management portalu dla deweloperów.
author: dlepow
ms.author: apimpm
ms.date: 04/15/2021
ms.service: api-management
ms.topic: how-to
ms.openlocfilehash: 4bda136a1abe8f9ffb443973731ebbe13b56ac3b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741855"
---
# <a name="implement-widgets-in-the-developer-portal"></a>Implementowanie widżetów w portalu dla deweloperów

W tym samouczku zaim implementuje się widżet, który zużywa dane z zewnętrznego interfejsu API i wyświetla go w API Management portalu dla deweloperów.

Widżet pobierze opisy sesji z przykładowego interfejsu [API konferencji](https://conferenceapi.azurewebsites.net/?format=json). Identyfikator sesji zostanie ustawiony za pośrednictwem wyznaczonego edytora widżetów.

Aby ułatwić ci proces tworzenia aplikacji, zapoznaj się z ukończonym widżetem w folderze repozytorium `examples` [GitHub](https://github.com/Azure/api-management-developer-portal/)API Management portal deweloperów: `/examples/widgets/conference-session` .

:::image type="content" source="media/developer-portal-implement-widgets/widget-published.png" alt-text="Zrzut ekranu przedstawiający opublikowany widżet":::

## <a name="prerequisites"></a>Wymagania wstępne

* Skonfiguruj środowisko [lokalne dla](developer-portal-self-host.md#step-1-set-up-local-environment) najnowszej wersji portalu dla deweloperów.

* Należy zrozumieć [anatomię widżetu Paperbits](https://paperbits.io/wiki/widget-anatomy).


## <a name="copy-the-scaffold"></a>Kopiowanie szkieletu

Użyj `widget` szkieletu z `/scaffolds` folderu jako punktu wyjścia do skompilowania nowego widżetu.

1. Skopiuj folder `/scaffolds/widget` do folderu `/community/widgets` .
1. Zmień nazwę folderu na `conference-session` .

## <a name="rename-exported-module-classes"></a>Zmienianie nazwy wyeksportowanych klas modułów

Zmień nazwę wyeksportowanych klas modułów, zastępując `Widget` prefiks `ConferenceSession` prefiksem w tych plikach:

- `widget.design.module.ts`

- `widget.publish.module.ts`

- `widget.runtime.module.ts`
    
Na przykład w `widget.design.module.ts` pliku zmień na `WidgetDesignModule` `ConferenceSessionDesignModule` :
    
```typescript
export class WidgetDesignModule implements IInjectorModule {
```
na wartość 
    
```typescript
export class ConferenceSessionDesignModule implements IInjectorModule {
```
    
   
## <a name="register-the-widget"></a>Rejestrowanie widżetu

Zarejestruj moduły widżetu w modułach głównych portalu, dodając następujące wiersze w odpowiednich plikach:

1. `src/apim.design.module.ts` — moduł, który rejestruje zależności czasu projektowania.

   ```typescript
   import { ConferenceSessionDesignModule } from "../community/widgets/conference-session/widget.design.module";
   
   ...
   injector.bindModule(new ConferenceSessionDesignModule());
   ```
1. `src/apim.publish.module.ts` — moduł, który rejestruje zależności czasu publikowania.

   ```typescript
   import { ConferenceSessionPublishModule } from "../community/widgets/conference-session/widget.publish.module";

    ...

    injector.bindModule(new ConferenceSessionPublishModule());
    ```

1. `src/apim.runtime.module.ts` — zależności środowiska uruchomieniowego.

    ```typescript
    import { ConferenceSessionRuntimeModule } from "../community/widgets/conference-session/widget.runtime.module";

    ...

    injector.bindModule(new ConferenceSessionRuntimeModule());
    ```

## <a name="place-the-widget-in-the-portal"></a>Umieść widżet w portalu

Teraz możesz podłączyć zduplikowany szkielet i użyć go w portalu dla deweloperów.

1. Uruchom polecenie `npm start`.

1. Podczas ładowania aplikacji umieść nowy widżet na stronie. Możesz go znaleźć pod nazwą w `Your widget` kategorii `Community` w selektorze widżetów.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-selector.png" alt-text="Zrzut ekranu przedstawiający selektor widżetów":::

1. Zapisz stronę, naciskając klawisze **Ctrl** + **S** (lub **⌘** + **S w** systemie macOS).

    > [!NOTE]
    > W czasie projektowania nadal możesz wchodzić w interakcje z witryną internetową, przytrzymując klawisz **Ctrl** (lub **⌘**).

## <a name="add-custom-properties"></a>Dodawanie właściwości niestandardowych

Aby widżet pobierał opisy sesji, musi wiedzieć o identyfikatorze sesji. Dodaj właściwość `Session ID` do odpowiednich interfejsów i klas:

Aby widżet pobierał opis sesji, musi mieć świadomość identyfikatora sesji. Dodaj właściwość identyfikatora sesji do odpowiednich interfejsów i klas:

1. `widgetContract.ts` — kontrakt danych (warstwa danych) definiujący sposób utrwalania konfiguracji widżetu.

    ```typescript
    export interface WidgetContract extends Contract {
        sessionNumber: string;
    }
    ```

1. `widgetModel.ts` - model (warstwa biznesowa) — podstawowa reprezentacja widżetu w systemie. Jest on aktualizowany przez edytory i renderowany przez warstwę prezentacji.

    ```typescript
    export class WidgetModel {
        public sessionNumber: string;
    }
    ```

1. `ko/widgetViewModel.ts` - viewmodel (warstwa prezentacji) — obiekt specyficzny dla struktury interfejsu użytkownika renderowany w portalu dla deweloperów przy użyciu szablonu HTML.

    > [!NOTE]
    > Nie trzeba niczego zmieniać w tym pliku.

## <a name="configure-binders"></a>Konfigurowanie binderów

Włącz przepływ elementu ze `sessionNumber` źródła danych do prezentacji widżetu. Edytuj `ModelBinder` `ViewModelBinder` jednostki i :

1. `widgetModelBinder.ts` ułatwia przygotowanie modelu przy użyciu danych opisanych w kontrakcie.

    ```typescript
    export class WidgetModelBinder implements IModelBinder<WidgetModel> {
        public async contractToModel(contract: WidgetContract): Promise<WidgetModel> {
            model.sessionNumber = contract.sessionNumber || "107"; // 107 is the default session id
            ...
        }
    
        public modelToContract(model: WidgetModel): Contract {
            const contract: WidgetContract = {
                sessionNumber: model.sessionNumber
                ...
            };
            ...
        }
    }
    ```

1. `ko/widgetViewModelBinder.ts` wie, jak portal deweloperów musi przedstawiać model (jako model widoku) w określonej platformie interfejsu użytkownika.

    ```typescript
    ...
    public async updateViewModel(model: WidgetModel, viewModel: WidgetViewModel): Promise<void> {
            viewModel.runtimeConfig(JSON.stringify({
                sessionNumber: model.sessionNumber
            }));
        }
    }
    ...
    ```

## <a name="adjust-design-time-widget-template"></a>Dostosowywanie szablonu widżetu czasu projektowania

Składniki każdego zakresu działają niezależnie. Mają oddzielne kontenery wstrzykiwania zależności, własną konfigurację, cykl życia itp. Mogą one być nawet obsługiwane przez różne struktury interfejsu użytkownika (w tym przykładzie jest to Knockout JS).

Z perspektywy czasu projektowania każdy składnik środowiska uruchomieniowego jest po prostu tagiem HTML z określonymi atrybutami i/lub zawartością. Konfiguracja w razie potrzeby jest przekazywana ze zwykłymi znacznikami. W prostych przypadkach, podobnie jak w tym przykładzie, parametr jest przekazywany w atrybutze . Jeśli konfiguracja jest bardziej złożona, można użyć identyfikatora wymaganych ustawień pobranych przez wyznaczonego dostawcę konfiguracji (na przykład `ISettingsProvider` ).

1. Zaktualizuj `ko/widgetView.html` plik:

    ```html
    <widget-runtime data-bind="attr: { params: runtimeConfig }"></widget-runtime>
    ```

    Gdy portal deweloperów uruchamia powiązanie w czasie projektowania lub w `attr` *czasie publikowania,* wynikowy kod HTML jest: 

    ```html
    <widget-runtime params="{ sessionNumber: 107 }"></widget-runtime>
    ```

    Następnie w środowisku uruchomieniowym składnik odczyta go i `widget-runtime` `sessionNumber` użyje w kodzie inicjowania (zobacz poniżej).

1. Zaktualizuj `widgetHandlers.ts` plik, aby przypisać identyfikator sesji podczas tworzenia:

    ```typescript
    ...
    createModel: async () => {
        var model = new ConferenceSessionModel();
        model.sessionNumber = "107";
            return model;
        }
    ...
    ```

## <a name="revise-runtime-view-model"></a>Poprawianie modelu widoku środowiska uruchomieniowego

Składniki środowiska uruchomieniowego to kod uruchomiony w samej witrynie internetowej. Na przykład w portalu dla deweloperów API Management są to skrypty związane ze składnikami dynamicznymi (na przykład szczegóły interfejsu *API,* konsola interfejsu *API),* obsługa operacji, takich jak generowanie przykładów kodu, wysyłanie żądań itp.

Model widoku składnika środowiska uruchomieniowego musi mieć następujące metody i właściwości:

- Właściwość (oznaczona dekoratorem) używana jako parametr wejściowy składnika przekazywana z zewnątrz (znacznik wygenerowany w czasie `sessionNumber` `Param` projektowania; zobacz poprzedni krok).
- Właściwość `sessionDescription` powiązana z szablonem widżetu (zobacz `widget-runtime.html` w dalszej części tego artykułu).
- Metoda `initialize` (z `OnMounted` dekoratorem) wywoływana po utworzeniu widżetu i przypisaniu wszystkich jego parametrów. Jest to dobre miejsce do odczytywania i wywoływania `sessionNumber` interfejsu API przy użyciu metody `HttpClient` . to `HttpClient` zależność wstrzyknięta przez kontener IoC (inversion of Control).

- Najpierw portal dla deweloperów tworzy widżet i przypisuje wszystkie jego parametry. Następnie wywołuje metodę `initialize` .

    ```typescript
    ...
    import * as ko from "knockout";
    import { Component, RuntimeComponent, OnMounted, OnDestroyed, Param } from "@paperbits/common/ko/decorators";
    import { HttpClient, HttpRequest } from "@paperbits/common/http";
    ...

    export class WidgetRuntime {
        public readonly sessionDescription: ko.Observable<string>;

        constructor(private readonly httpClient: HttpClient) {
            ...
            this.sessionNumber = ko.observable();
            this.sessionDescription = ko.observable();
            ...
        }

        @Param()
        public readonly sessionNumber: ko.Observable<string>;

        @OnMounted()
        public async initialize(): Promise<void> {
            ...
            const sessionNumber = this.sessionNumber();

            const request: HttpRequest = {
                url: `https://conferenceapi.azurewebsites.net/session/${sessionNumber}`,
                method: "GET"
            };

            const response = await this.httpClient.send<string>(request);
            const sessionDescription = response.toText();
    
            this.sessionDescription(sessionDescription);
            ...
        }
        ...
    }
    ```

## <a name="tweak-the-widget-template"></a>Dostosowanie szablonu widżetu

Zaktualizuj widżet, aby wyświetlić opis sesji.

Użyj tagu akapitu i `markdown` powiązania (lub `text` ) w `ko/runtime/widget-runtime.html` pliku, aby renderować opis:

```html
<p data-bind="markdown: sessionDescription"></p>
```

## <a name="add-the-widget-editor"></a>Dodawanie edytora widżetów

Widżet jest teraz skonfigurowany do pobierania opisu sesji `107` . W kodzie `107` określono sesję domyślną. Aby sprawdzić, czy wszystko działało dobrze, uruchom i upewnij się, że `npm start` w portalu dla deweloperów jest przedstawiany opis na stronie.

Teraz należy wykonać następujące kroki, aby umożliwić użytkownikowi skonfigurowanie identyfikatora sesji za pomocą edytora widżetów:

1. Zaktualizuj `ko/widgetEditorViewModel.ts` plik:

    ```typescript
    export class WidgetEditor implements WidgetEditor<WidgetModel> {
        public readonly sessionNumber: ko.Observable<string>;

        constructor() {
            this.sessionNumber = ko.observable();
        }

        @Param()
        public model: WidgetModel;

        @Event()
        public onChange: (model: WidgetModel) => void;

        @OnMounted()
        public async initialize(): Promise<void> {
            this.sessionNumber(this.model.sessionNumber);
            this.sessionNumber.subscribe(this.applyChanges);
        }

        private applyChanges(): void {
            this.model.sessionNumber = this.sessionNumber();
            this.onChange(this.model);
        }
    }
    ```

    Model widoku edytora używa tego samego podejścia, co wcześniej, ale istnieje nowa właściwość `onChange` , dekorowany za pomocą . `@Event()` Stanowi on połączenie zwrotne w celu powiadomienia odbiorników (w tym przypadku edytora zawartości) o zmianach w modelu.

1. Zaktualizuj `ko/widgetEditorView.html` plik:

    ```html
    <input type="text" class="form-control" data-bind="textInput: sessionNumber" />
    ```

1. Uruchom ponownie polecenie `npm start`. Powinna być możliwość zmiany w `sessionNumber` edytorze widżetów. Zmień identyfikator na `108` , zapisz zmiany i odśwież kartę przeglądarki. Jeśli występują problemy, może być konieczne ponowne dodanie widżetu na stronie.

    :::image type="content" source="media/developer-portal-implement-widgets/widget-editor.png" alt-text="Zrzut ekranu przedstawiający edytor widżetów":::

## <a name="rename-the-widget"></a>Zmienianie nazwy widżetu

Zmień nazwę widżetu w `constants.ts` pliku:

```typescript
...
export const widgetName = "conference-session";
export const widgetDisplayName = "Conference session";
...
```

> [!NOTE]
> Jeśli współtworzesz widżet w repozytorium, element musi być taki sam jak nazwa folderu i musi pochodzić od nazwy wyświetlanej (małe litery i spacje zastąpione `widgetName` kreskami). Kategoria powinna pozostać `Community` .

## <a name="next-steps"></a>Następne kroki


Dowiedz się więcej o portalu dla deweloperów:

- [Portal deweloperów usługi Azure API Management — omówienie](api-management-howto-developer-portal.md)

- [Widżety współtwomentuj](developer-portal-widget-contribution-guidelines.md) — zapraszamy i zachęcamy do współtwonia przez społeczność.

- Zobacz [Korzystanie z widżetów społeczności,](developer-portal-use-community-widgets.md) aby dowiedzieć się, jak korzystać z widżetów współtwonych przez społeczność.
