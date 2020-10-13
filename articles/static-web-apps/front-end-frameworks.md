---
title: Konfigurowanie platform frontonu przy użyciu usługi Azure static Web Apps Preview
description: Ustawienia dla popularnych platform frontonu, które są niezbędne dla Web Apps statycznej platformy Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 4b1bc58b6b4a87cd6e5e09e83020a38261b8746f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90905367"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Konfigurowanie środowisk i bibliotek frontonu za pomocą usługi Azure static Web Apps Preview

Statyczna Web Apps platformy Azure wymaga, aby w [pliku konfiguracji kompilacji](github-actions-workflow.md) dla platformy lub biblioteki frontonu były dostępne odpowiednie wartości konfiguracyjne.

## <a name="configuration"></a>Konfiguracja

W poniższej tabeli wymieniono ustawienia dla serii platform i bibliotek<sup>1</sup>.

Zamiarem kolumn tabeli jest wyjaśnienie następujących elementów:

- **Lokalizacja artefaktu aplikacji**: Lista wartości dla programu `app_artifact_location` , czyli [folderu dla skompilowanych wersji plików aplikacji](github-actions-workflow.md#build-and-deploy).

- **Niestandardowe polecenie kompilacji**: gdy struktura wymaga polecenia innego niż `npm run build` lub `npm run azure:build` , można zdefiniować [Niestandardowe polecenie kompilacji](github-actions-workflow.md#custom-build-commands).

| Framework | Lokalizacja artefaktu aplikacji | Niestandardowa kompilacja — polecenie |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | nie dotyczy <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Kątowy uniwersalny](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | nie dotyczy |
| [Backbone.js](https://backbonejs.org/) | `/` | nie dotyczy |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | nie dotyczy |
| [Ember](https://emberjs.com/) | `dist` | nie dotyczy |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | nie dotyczy |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | nie dotyczy |
| [Hyperapp](https://hyperapp.dev/) | `/` | nie dotyczy |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | nie dotyczy |
| [jQuery](https://jquery.com/) | `/` | nie dotyczy |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | nie dotyczy |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | nie dotyczy |
| [Marko](https://markojs.com/) | `public` | nie dotyczy |
| [Meteor](https://www.meteor.com/) | `bundle` | nie dotyczy |
| [Mithril](https://mithril.js.org/) | `dist` | nie dotyczy |
| [Polymer](https://www.polymer-project.org/) | `build/default` | nie dotyczy |
| [Preact](https://preactjs.com/) | `build` | nie dotyczy |
| [React](https://reactjs.org/) | `build` | nie dotyczy |
| [Wzornik](https://stenciljs.com/) | `www` | nie dotyczy |
| [Svelte](https://svelte.dev/) | `public` | nie dotyczy |
| [Three.js](https://threejs.org/) | `/` | nie dotyczy |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | nie dotyczy |
| [Vue.js](https://vuejs.org/) | `dist` | nie dotyczy |

<sup>1</sup> Powyższa tabela nie jest kompletną listą struktur i bibliotek, które współpracują z usługą Azure static Web Apps.

<sup>2</sup> nie dotyczy

## <a name="next-steps"></a>Następne kroki

- [Konfiguracja kompilowania i przepływu pracy](github-actions-workflow.md)
