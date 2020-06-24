---
title: Konfigurowanie platform frontonu przy użyciu usługi Azure static Web Apps Preview
description: Ustawienia dla popularnych platform frontonu, które są niezbędne dla Web Apps statycznej platformy Azure
services: static-web-apps
author: craigshoemaker
ms.service: static-web-apps
ms.topic: conceptual
ms.date: 06/10/2020
ms.author: cshoe
ms.openlocfilehash: 41f0c44ad65af5fdf2560da3f977a28f135af878
ms.sourcegitcommit: 9bfd94307c21d5a0c08fe675b566b1f67d0c642d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84976714"
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
| [Platforma Meteor](https://www.meteor.com/) | `bundle` | nie dotyczy |
| [Mithril](https://mithril.js.org/) | `dist` | nie dotyczy |
| [Polymer](https://www.polymer-project.org/) | `build/default` | nie dotyczy |
| [Preact](https://preactjs.com/) | `build` | nie dotyczy |
| [React](https://reactjs.org/) | `build` | nie dotyczy |
| [Wzornik](https://stenciljs.com/) | `www` | nie dotyczy |
| [Svelte](https://svelte.dev/) | `public` | nie dotyczy |
| [Three.js](https://threejs.org/) | `/` | nie dotyczy |
| [TypeScript](https://www.typescriptlang.org/) | `dist` | nie dotyczy |
| [VUE](http://vuejs.com/) | `dist` | nie dotyczy |

<sup>1</sup> Powyższa tabela nie jest kompletną listą struktur i bibliotek, które współpracują z usługą Azure static Web Apps.

<sup>2</sup> nie dotyczy

## <a name="next-steps"></a>Następne kroki

- [Konfiguracja kompilowania i przepływu pracy](github-actions-workflow.md)
