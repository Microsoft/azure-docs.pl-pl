---
title: Problemy z programem Internet Explorer (MSAL. js) | Azure
titleSuffix: Microsoft identity platform
description: Użyj biblioteki uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) przy użyciu przeglądarki Internet Explorer.
services: active-directory
author: navyasric
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 05/16/2019
ms.author: nacanuma
ms.reviewer: saeeda
ms.custom: aaddev
ms.openlocfilehash: c4f3c4153e1404a5576427be7ef218f5a669387e
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "76695861"
---
# <a name="known-issues-on-internet-explorer-and-microsoft-edge-browsers-msaljs"></a>Znane problemy w programie Internet Explorer i przeglądarki Microsoft Edge (MSAL. js)

Biblioteka uwierzytelniania firmy Microsoft dla języka JavaScript (MSAL. js) jest generowana na potrzeby [języka JavaScript ES5](https://fr.wikipedia.org/wiki/ECMAScript#ECMAScript_Edition_5_.28ES5.29) , dzięki czemu można uruchamiać ją w programie Internet Explorer. Istnieje jednak kilka rzeczy, które należy znać.

## <a name="run-an-app-in-internet-explorer"></a>Uruchamianie aplikacji w programie Internet Explorer
Jeśli zamierzasz używać MSAL. js w aplikacjach, które mogą być uruchamiane w programie Internet Explorer, musisz dodać odwołanie do wypełnienia obietnicą przed odwołaniem do skryptu MSAL. js.

```html
<script src="https://cdnjs.cloudflare.com/ajax/libs/bluebird/3.3.4/bluebird.min.js" class="pre"></script>
```

Wynika to z faktu, że program Internet Explorer nie obsługuje natywnie niesie obietnice zwiększenia języka JavaScript.

## <a name="debugging-an-application-running-in-internet-explorer"></a>Debugowanie aplikacji działającej w programie Internet Explorer

### <a name="running-in-production"></a>Uruchamianie w środowisku produkcyjnym
Wdrażanie aplikacji w środowisku produkcyjnym (na przykład w usłudze Azure Web Apps) zwykle działa prawidłowo, pod warunkiem, że użytkownik końcowy zaakceptował okna podręczne. Przetestowano ją z programem Internet Explorer 11.

### <a name="running-locally"></a>Uruchamianie lokalne
Jeśli chcesz uruchomić i debugować lokalnie aplikację działającą w programie Internet Explorer, musisz pamiętać o następujących kwestiach (Załóżmy, że chcesz uruchomić aplikację jako *http://localhost:1234*):

- Program Internet Explorer ma mechanizm zabezpieczeń o nazwie "tryb chroniony", który uniemożliwia poprawne działanie MSAL. js. Po zalogowaniu się do http://localhost:1234/nullstrony mogą zostać przekierowane między objawy.

- Aby uruchomić i debugować aplikację lokalnie, należy wyłączyć ten tryb chroniony. Dla tego:

    1. Kliknij pozycję **Narzędzia** programu Internet Explorer (ikona koła zębatego).
    1. Wybierz pozycję **Opcje internetowe** , a następnie kartę **zabezpieczenia** .
    1. Kliknij strefę **Internet** , a następnie usuń zaznaczenie pola wyboru **Włącz tryb chroniony (wymaga ponownego uruchomienia programu Internet Explorer)**. Program Internet Explorer ostrzega o tym, że komputer nie jest już chroniony. Kliknij przycisk **OK**.
    1. Uruchom ponownie program Internet Explorer.
    1. Uruchamianie i debugowanie aplikacji.

Gdy wszystko będzie gotowe, Przywróć ustawienia zabezpieczeń programu Internet Explorer.  Wybierz pozycję **Ustawienia** -> **Opcje** -> internetowe**zabezpieczenia** -> **Zresetuj wszystkie strefy do poziomu domyślnego**.

## <a name="next-steps"></a>Następne kroki
Dowiedz się więcej o [znanych problemach dotyczących używania MSAL. js w programie Internet Explorer](msal-js-use-ie-browser.md).