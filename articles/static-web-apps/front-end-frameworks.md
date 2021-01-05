---
title: Konfigurowanie platform frontonu przy użyciu usługi Azure static Web Apps Preview
description: Ustawienia dla popularnych platform frontonu, które są niezbędne dla Web Apps statycznej platformy Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 07/18/2020
ms.author: cshoe
ms.openlocfilehash: 14564b0591ef0146131b3f9324556b613e25daac
ms.sourcegitcommit: 5e762a9d26e179d14eb19a28872fb673bf306fa7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/05/2021
ms.locfileid: "97901236"
---
# <a name="configure-front-end-frameworks-and-libraries-with-azure-static-web-apps-preview"></a>Konfigurowanie środowisk i bibliotek frontonu za pomocą usługi Azure static Web Apps Preview

Statyczna Web Apps platformy Azure wymaga, aby w [pliku konfiguracji kompilacji](github-actions-workflow.md) dla platformy lub biblioteki frontonu były dostępne odpowiednie wartości konfiguracyjne.

## <a name="configuration"></a>Konfigurowanie

W poniższej tabeli wymieniono ustawienia dla serii platform i bibliotek<sup>1</sup>.

Zamiarem kolumn tabeli jest wyjaśnienie następujących elementów:

- **Lokalizacja wyjściowa**: zawiera listę wartości dla parametru `output_location` , który jest [folderem dla skompilowanych wersji plików aplikacji](github-actions-workflow.md#build-and-deploy).

- **Niestandardowe polecenie kompilacji**: gdy struktura wymaga polecenia innego niż `npm run build` lub `npm run azure:build` , można zdefiniować [Niestandardowe polecenie kompilacji](github-actions-workflow.md#custom-build-commands).

| Framework | Lokalizacja artefaktu aplikacji | Niestandardowa kompilacja — polecenie |
|--|--|--|
| [Alpine.js](https://github.com/alpinejs/alpine/) | `/` | nie dotyczy <sup>2</sup> |
| [Angular](https://angular.io/) | `dist/<APP_NAME>` | `npm run build -- --prod` |
| [Kątowy uniwersalny](https://angular.io/guide/universal) | `dist/<APP_NAME>/browser` | `npm run prerender` |
| [Aurelia](https://aurelia.io/) | `dist` | n/d |
| [Backbone.js](https://backbonejs.org/) | `/` | n/d |
| [Blazor](https://dotnet.microsoft.com/apps/aspnet/web-apps/blazor) | `wwwroot` | n/d |
| [Ember](https://emberjs.com/) | `dist` | n/d |
| [Flutter](https://flutter.dev/) | `build/web` | `flutter build web` |
| [Framework7](https://framework7.io/) | `www` | `npm run build-prod` |
| [Glimmer](https://glimmerjs.com/) | `dist` | n/d |
| [HTML](https://developer.mozilla.org/docs/Web/HTML) | `/` | n/d |
| [Hyperapp](https://hyperapp.dev/) | `/` | n/d |
| [JavaScript](https://developer.mozilla.org/docs/Web/javascript) | `/` | n/d |
| [jQuery](https://jquery.com/) | `/` | n/d |
| [KnockoutJS](https://knockoutjs.com/) | `dist` | n/d |
| [LitElement](https://lit-element.polymer-project.org/) | `dist` | n/d |
| [Marko](https://markojs.com/) | `public` | n/d |
| [Meteor](https://www.meteor.com/) | `bundle` | n/d |
| [Mithril](https://mithril.js.org/) | `dist` | n/d |
| [Polymer](https://www.polymer-project.org/) | `build/default` | n/d |
| [Preact](https://preactjs.com/) | `build` | n/d |
| [React](https://reactjs.org/) | `build` | n/d |
| [Wzornik](https://stenciljs.com/) | `www` | n/d |
| [Svelte](https://svelte.dev/) | `public` | n/d |
| [Three.js](https://threejs.org/) | `/` | n/d |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | n/d |
| [Vue.js](https://vuejs.org/) | `dist` | n/d |

<sup>1</sup> Powyższa tabela nie jest kompletną listą struktur i bibliotek, które współpracują z usługą Azure static Web Apps.

<sup>2</sup> nie dotyczy

## <a name="next-steps"></a>Następne kroki

- [Konfiguracja kompilowania i przepływu pracy](github-actions-workflow.md)
