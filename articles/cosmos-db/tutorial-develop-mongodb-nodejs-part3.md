---
title: Tworzenie interfejsu użytkownika aplikacji kątowej z interfejsem API Azure Cosmos DB dla MongoDB (part3)
description: Część 3 z serii samouczków o tworzeniu aplikacji bazy danych MongoDB za pomocą platformy Angular i języka Node w usłudze Azure Cosmos DB przy użyciu dokładnie tych samych interfejsów API, które były używane dla bazy danych MongoDB.
author: johnpapa
ms.service: cosmos-db
ms.subservice: cosmosdb-mongo
ms.devlang: nodejs
ms.topic: tutorial
ms.date: 12/26/2018
ms.author: jopapa
ms.custom: seodec18, devx-track-js
ms.reviewer: sngun
ms.openlocfilehash: c16d7ccba24680604bc7f083f7242edd53abe102
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91282905"
---
# <a name="create-an-angular-app-with-azure-cosmos-dbs-api-for-mongodb---build-the-ui-with-angular"></a>Tworzenie aplikacji Angular przy użyciu interfejsu API usługi Azure Cosmos DB dla bazy danych MongoDB — kompilowanie interfejsu użytkownika za pomocą aplikacji Angular

Ten wieloczęściowy samouczek pokazuje, jak utworzyć nową aplikację napisaną w środowisku Node.js za pomocą programu Express i platformy Angular, a następnie podłączyć ją do [konta usługi Cosmos skonfigurowanego przy użyciu interfejsu API usługi Cosmos DB dla bazy danych MongoDB](mongodb-introduction.md).

Część 3 samouczka jest oparta na [części 2](tutorial-develop-mongodb-nodejs-part2.md) i obejmuje następujące zadania:

> [!div class="checklist"]
> * Zbudowanie interfejsu użytkownika platformy Angular
> * Wykorzystanie arkuszy CSS, aby ustawić wygląd i działanie
> * Lokalne przetestowanie aplikacji

## <a name="video-walkthrough"></a>Przewodnik wideo

> [!VIDEO https://www.youtube.com/embed/MnxHuqcJVoM]

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tej części samouczka upewnij się, że zostały wykonane czynności opisane w [części 2](tutorial-develop-mongodb-nodejs-part2.md) samouczka.

> [!TIP]
> Ten samouczek przedstawia instrukcje tworzenia aplikacji krok po kroku. Jeśli chcesz pobrać gotowy projekt, ukończoną aplikację znajdziesz w [repozytorium angular-cosmosdb](https://github.com/Azure-Samples/angular-cosmosdb) w witrynie GitHub.

## <a name="build-the-ui"></a>Tworzenie interfejsu użytkownika

1. W Visual Studio Code kliknij przycisk Zatrzymaj, :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/stop-button.png"::: Aby zatrzymać aplikację węzła. 

2. W oknie wiersza polecenia systemu Windows lub oknie terminalu systemu Mac wprowadź następujące polecenie, aby wygenerować składnik heroes. W tym kodzie g = wygeneruj, c = składnik, heroes = nazwa składnika, a ponadto jest używana struktura prostych plików (— flat), aby dla tego składnika nie został utworzony podfolder.

    ```
    ng g c heroes --flat 
    ```

    Okno terminalu pokazuje potwierdzenie dla nowych składników.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/install-heros-component.png" alt-text="Instalowanie składnika hero":::

    Spójrzmy na pliki, które zostały utworzone i zaktualizowane. 

3. W programie Visual Studio Code w okienku **Eksplorator** przejdź do nowego folderu **src\app** i otwórz nowy plik **heroes.component.ts** wygenerowany w folderze aplikacji. Ten plik składnika TypeScript został utworzony przez poprzednie polecenie.

    > [!TIP]
    > Jeśli folder aplikacji nie jest wyświetlany w programie Visual Studio Code, naciśnij klawisze CMD + SHIFT + P na komputerze Mac lub Ctrl + Shift + P w systemie Windows, aby otworzyć paletę poleceń, a następnie wpisz polecenie *Reload Window*, aby pobrać zmianę z systemu.

4. W tym samym folderze otwórz plik **app.module.ts** i zwróć uwagę, że do deklaracji w wierszu 5 dodano element `HeroesComponent` oraz zaimportowano go również w wierszu 10.

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/app-module-file.png" alt-text="Instalowanie składnika hero":::

5. Wróć do pliku **heroes.component.html** i skopiuj do niego ten kod. Element `<div>` jest kontenerem całej strony. Wewnątrz tego kontenera znajduje się lista elementów hero, które musimy utworzyć w taki sposób, aby po kliknięciu jednego z nich można było go zaznaczyć, a następnie edytować lub usunąć w interfejsie użytkownika. Następnie w kodzie HTML mamy trochę zmieniania stylu, aby było wiadomo, który element został wybrany. Jest tam też obszar edycji, aby można było dodać nowy element hero lub edytować istniejący. 

    ```html
    <div>
      <ul class="heroes">
        <li *ngFor="let hero of heroes" (click)="onSelect(hero)" [class.selected]="hero === selectedHero">
          <button class="delete-button" (click)="deleteHero(hero)">Delete</button>
          <div class="hero-element">
            <div class="badge">{{hero.id}}</div>
            <div class="name">{{hero.name}}</div>
            <div class="saying">{{hero.saying}}</div>
          </div>
        </li>
      </ul>
      <div class="editarea">
        <button (click)="enableAddMode()">Add New Hero</button>
        <div *ngIf="selectedHero">
          <div class="editfields">
            <div>
              <label>id: </label>
              <input [(ngModel)]="selectedHero.id" placeholder="id" *ngIf="addingHero" />
              <label *ngIf="!addingHero" class="value">{{selectedHero.id}}</label>
            </div>
            <div>
              <label>name: </label>
              <input [(ngModel)]="selectedHero.name" placeholder="name" />
            </div>
            <div>
              <label>saying: </label>
              <input [(ngModel)]="selectedHero.saying" placeholder="saying" />
            </div>
          </div>
          <button (click)="cancel()">Cancel</button>
          <button (click)="save()">Save</button>
        </div>
      </div>
    </div>
    ```

7. Teraz, gdy mamy już kod HTML, musimy dodać go do pliku **heroes.component.ts**, aby umożliwić interakcję z szablonem. Poniższy kod dodaje szablon do pliku składnika. Został dodany konstruktor, który pobiera niektóre elementy hero i inicjuje składnik usługi hero, aby pobrać wszystkie dane. Ten kod dodaje również wszystkie metody niezbędne do obsługi zdarzeń w interfejsie użytkownika. Możesz skopiować następujący kod w miejsce istniejącego kodu w pliku **heroes.component.ts**. W obszarach Hero i HeroService można spodziewać się błędów, ponieważ odpowiednie składniki nie zostały jeszcze zaimportowane. Te błędy zostaną naprawione w następnej sekcji. 

    ```ts
    import { Component, OnInit } from '@angular/core';

    @Component({
      selector: 'app-heroes',
      templateUrl: './heroes.component.html',
      styleUrls: ['./heroes.component.scss']
    })
    export class HeroesComponent implements OnInit {
      addingHero = false;
      heroes: any = [];
      selectedHero: Hero;
    
      constructor(private heroService: HeroService) {}
    
      ngOnInit() {
       this.getHeroes();
      }

      cancel() {
        this.addingHero = false;
        this.selectedHero = null;
      }

      deleteHero(hero: Hero) {
        this.heroService.deleteHero(hero).subscribe(res => {
          this.heroes = this.heroes.filter(h => h !== hero);
          if (this.selectedHero === hero) {
            this.selectedHero = null;
          }
        });
      }

      getHeroes() {
        return this.heroService.getHeroes().subscribe(heroes => {
          this.heroes = heroes;
        });
      }

      enableAddMode() {
        this.addingHero = true;
        this.selectedHero = new Hero();
      }

      onSelect(hero: Hero) {
        this.addingHero = false;
        this.selectedHero = hero;
      }

      save() {
        if (this.addingHero) {
          this.heroService.addHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
            this.heroes.push(hero);
          });
        } else {
          this.heroService.updateHero(this.selectedHero).subscribe(hero => {
            this.addingHero = false;
            this.selectedHero = null;
          });
        }
      }
    }
    ```

8. W okienku **Eksplorator** otwórz plik **app/app.module.ts** i zaktualizuj sekcję importów, aby dodać import dla modułu `FormsModule`. Sekcja importu powinna teraz wyglądać tak jak poniżej:

    ```
    imports: [
      BrowserModule,
      FormsModule
    ],
    ```

9. W pliku **app/app.module.ts** dodaj import dla nowego modułu FormsModule w wierszu 3. 

    ```
    import { BrowserModule } from '@angular/platform-browser';
    import { NgModule } from '@angular/core';
    import { FormsModule } from '@angular/forms';
    ```

## <a name="use-css-to-set-the-look-and-feel"></a>Wykorzystanie arkuszy CSS, aby ustawić wygląd i działanie

1. W okienku Eksplorator otwórz plik **src/styles.scss**.

2. Skopiuj następujący kod do pliku **styles.scss**, zastępując istniejącą zawartość pliku.

    ```css
    /* You can add global styles to this file, and also import other style files */

    * {
      font-family: Arial;
    }
    h2 {
      color: #444;
      font-weight: lighter;
    }
    body {
      margin: 2em;
    }
    
    body,
    input[text],
    button {
      color: #888;
      // font-family: Cambria, Georgia;
    }
    button {
      font-size: 14px;
      font-family: Arial;
      background-color: #eee;
      border: none;
      padding: 5px 10px;
      border-radius: 4px;
      cursor: pointer;
      cursor: hand;
      &:hover {
        background-color: #cfd8dc;
      }
      &.delete-button {
        float: right;
        background-color: gray !important;
        background-color: rgb(216, 59, 1) !important;
        color: white;
        padding: 4px;
        position: relative;
        font-size: 12px;
      }
    }
    div {
      margin: .1em;
    }

    .selected {
      background-color: #cfd8dc !important;
      background-color: rgb(0, 120, 215) !important;
      color: white;
    }

    .heroes {
      float: left;
      margin: 0 0 2em 0;
      list-style-type: none;
      padding: 0;
      li {
        cursor: pointer;
        position: relative;
        left: 0;
        background-color: #eee;
        margin: .5em;
        padding: .5em;
        height: 3.0em;
        border-radius: 4px;
        width: 17em;
        &:hover {
          color: #607d8b;
          color: rgb(0, 120, 215);
          background-color: #ddd;
          left: .1em;
        }
        &.selected:hover {
          /*background-color: #BBD8DC !important;*/
          color: white;
        }
      }
      .text {
        position: relative;
        top: -3px;
      }
      .saying {
        margin: 5px 0;
      }
      .name {
        font-weight: bold;
      }
      .badge {
        /* display: inline-block; */
        float: left;
        font-size: small;
        color: white;
        padding: 0.7em 0.7em 0 0.5em;
        background-color: #607d8b;
        background-color: rgb(0, 120, 215);
        background-color:rgb(134, 183, 221);
        line-height: 1em;
        position: relative;
        left: -1px;
        top: -4px;
        height: 3.0em;
        margin-right: .8em;
        border-radius: 4px 0 0 4px;
        width: 1.2em;
      }
    }

    .header-bar {
      background-color: rgb(0, 120, 215);
      height: 4px;
      margin-top: 10px;
      margin-bottom: 10px;
    }

    label {
      display: inline-block;
      width: 4em;
      margin: .5em 0;
      color: #888;
      &.value {
        margin-left: 10px;
        font-size: 14px;
      }
    }

    input {
      height: 2em;
      font-size: 1em;
      padding-left: .4em;
      &::placeholder {
          color: lightgray;
          font-weight: normal;
          font-size: 12px;
          letter-spacing: 3px;
      }
    }

    .editarea {
      float: left;
      input {
        margin: 4px;
        height: 20px;
        color: rgb(0, 120, 215);
      }
      button {
        margin: 8px;
      }
      .editfields {
        margin-left: 12px;
      }
    }
    ``` 
3. Zapisz plik. 

## <a name="display-the-component"></a>Wyświetlanie składnika

Teraz, gdy mamy składnik, jak możemy doprowadzić do wyświetlenia go na ekranie? Zmodyfikujmy domyślne składniki w pliku **app.component.ts**.

1. W okienku Eksplorator otwórz plik **/app/app.component.ts**, zmień tytuł na Heroes, a następnie umieść nazwę składnika utworzonego w pliku **heroes.components.ts** (app-heroes) tak, aby odwoływał się do tego nowego składnika. Zawartość pliku powinna teraz wyglądać tak: 

    ```ts
    import { Component } from '@angular/core';

    @Component({
      selector: 'app-root',
      templateUrl: './app.component.html',
      styleUrls: ['./app.component.scss'],
      template: `
      <h1>Heroes</h1>
      <div class="header-bar"></div>
      <app-heroes></app-heroes>
    `
    })
    export class AppComponent {
      title = 'app';
    }

    ```

2. Istnieją inne składniki w pliku **heroes.components.ts**, do których się odwołujemy, takie jak składnik Hero, więc musimy je też utworzyć. W interfejsie wiersza polecenia platformy Angular użyj następującego polecenia, aby utworzyć model składnika hero i plik o nazwie **hero.ts**, gdzie g = wygeneruj, cl = klasa i hero = nazwa klasy.

    ```bash
    ng g cl hero
    ```

3. W okienku Eksplorator otwórz plik **src\app\hero.ts**. W pliku **hero.ts** zastąp zawartość pliku następującym kodem, który dodaje klasę Hero zawierającą identyfikator, nazwę i powiedzenie.

    ```ts
      export class Hero {
      id: number;
      name: string;
      saying: string;
    }
    ```

4. Wróć do pliku **heroes.components.ts** i zwróć uwagę, że w wierszu `selectedHero: Hero;` (wiersz 10) składnik `Hero` jest podkreślony czerwoną linią. 

5. Kliknij lewym przyciskiem myszy termin `Hero`, a program Visual Studio wyświetli ikonę żarówki po lewej stronie bloku kodu. 

    :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/light-bulb.png" alt-text="Instalowanie składnika hero":::

6. Kliknij ikonę żarówki, a następnie kliknij pozycję **Importuj element Hero z folderu „/app/hero”** lub **Importuj element Hero z folderu „./hero”**. (Komunikat zmienia się w zależności od konfiguracji).

    W wierszu 2 pojawi się nowy wiersz kodu. Jeśli wiersz 2 odwołuje się do folderu /app/hero, zmodyfikuj go tak, aby odwoływał się do pliku hero z folderu lokalnego (./hero). Wiersz 2 powinien wyglądać następująco:

   ```
   import { Hero } from "./hero";
   ``` 

    Mamy już załatwioną sprawę modelu, ale nadal musimy utworzyć usługę.

## <a name="create-the-service"></a>Tworzenie usługi

1. W interfejsie wiersza polecenia platformy Angular wprowadź następujące polecenie, aby utworzyć usługę hero w pliku **app.module.ts**, gdzie g = wygeneruj, s = usługa, hero = nazwa usługi, -m = umieść w app.module.

    ```bash
    ng g s hero -m app.module
    ```

2. W programie Visual Studio Code wróć do pliku **heroes.components.ts**. Zwróć uwagę, że w wierszu `constructor(private heroService: HeroService) {}` (wiersz 13) element `HeroService` jest podkreślony czerwoną linią. Kliknij pozycję `HeroService`, a z lewej strony bloku kodu pojawi się żarówka. Kliknij żarówkę, a następnie kliknij pozycję **Importuj element HeroService z folderu „./hero.service ”** lub **Importuj element HeroService z folderu „/app/hero.service”**.

    Kliknięcie żarówki powoduje wstawienie nowego wiersza kodu w wierszu 2. Jeśli wiersz 2 odwołuje się do folderu /app/hero.service, zmodyfikuj go tak, aby odwoływał się do pliku hero z folderu lokalnego (./hero.service). Wiersz 2 powinien wyglądać następująco:
    
    ```javascript
    import { HeroService } from "./hero.service"
    ```

3. W programie Visual Studio Code otwórz plik **hero.service.ts** i skopiuj do niego następujący kod, zastępując zawartość pliku.

    ```ts
    import { Injectable } from '@angular/core';
    import { HttpClient } from '@angular/common/http';
    
    import { Hero } from './hero';
    
    const api = '/api';

    @Injectable()
    export class HeroService {
      constructor(private http: HttpClient) {}

      getHeroes() {
        return this.http.get<Array<Hero>>(`${api}/heroes`)
      }

      deleteHero(hero: Hero) {
        return this.http.delete(`${api}/hero/${hero.id}`);
      }

      addHero(hero: Hero) {
        return this.http.post<Hero>(`${api}/hero/`, hero);
      }

      updateHero(hero: Hero) {
        return this.http.put<Hero>(`${api}/hero/${hero.id}`, hero);
      }
    }
    ```

    Ten kod wykorzystuje najnowszą wersję klienta HttpClient oferowaną przez platformę Angular, który jest modułem, który musisz dostarczyć, więc zrobimy to w następnym kroku.

4. W programie Visual Studio Code otwórz plik **app.module.ts** i zaimportuj moduł HttpClientModule, aktualizując sekcję importu tak, aby zawierała moduł HttpClientModule.

    ```ts
    imports: [
      BrowserModule,
      FormsModule,
      HttpClientModule
    ],
    ```

5. W pliku **app.module.ts** dodaj instrukcję importu modułu HttpClientModule do listy importów.

    ```ts
    import { HttpClientModule } from '@angular/common/http';
    ```

6. Zapisz wszystkie pliki w programie Visual Studio Code.

## <a name="build-the-app"></a>Tworzenie aplikacji

1. W wierszu polecenia wprowadź następujące polecenie, aby skompilować aplikację platformy Angular. 

    ```bash
    ng b
    ``` 

    Jeśli wystąpią jakiekolwiek problemy, w oknie terminala zostaną wyświetlone informacje o plikach, które należy naprawić. Po zakończeniu kompilacji nowe pliki trafią do folderu **dist**. Jeśli chcesz, możesz przejrzeć nowe pliki w folderze **dist**.

    Teraz uruchommy aplikację.

2. W Visual Studio Code kliknij przycisk **Debuguj** po :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part2/debug-button.png"::: lewej stronie, a następnie kliknij przycisk **Rozpocznij debugowanie** :::image type="icon" source="./media/tutorial-develop-mongodb-nodejs-part3/start-debugging-button.png"::: .

3. Teraz otwórz przeglądarkę internetową, przejdź pod adres **localhost:3000** i obejrzyj lokalnie uruchomioną aplikację.

     :::image type="content" source="./media/tutorial-develop-mongodb-nodejs-part3/azure-cosmos-db-mongodb-mean-app.png" alt-text="Instalowanie składnika hero":::

## <a name="next-steps"></a>Następne kroki

W tej części samouczka zostały wykonane następujące zadania:

> [!div class="checklist"]
> * Zbudowano interfejs użytkownika platformy Angular
> * Lokalnie przetestowano aplikację

Możesz przejść do następnej części samouczka, aby utworzyć konto usługi Azure Cosmos DB.

> [!div class="nextstepaction"]
> [Tworzenie konta usługi Azure Cosmos DB przy użyciu interfejsu wiersza polecenia platformy Azure](tutorial-develop-mongodb-nodejs-part4.md)
