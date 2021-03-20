---
title: Ustaw zasady plików cookie czytnika immersyjny
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób ustawiania zasad dotyczących plików cookie dla czytnika immersyjny.
services: cognitive-services
author: nitinme
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: nitinme
ms.custom: devx-track-js
ms.openlocfilehash: 02901e6b4a75257b2cda8dee84dbe3438dc15c18
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "91332376"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Jak ustawić zasady dotyczące plików cookie dla czytnika immersyjny

Czytnik immersyjny domyślnie wyłącza użycie plików cookie. W przypadku włączenia użycia plików cookie, czytnik immersyjny może używać plików cookie do obsługi preferencji użytkownika i śledzenia użycia funkcji. W przypadku włączenia użycia plików cookie w czytniku immersyjny należy wziąć pod uwagę wymagania zasad zgodności plików cookie UE. Aplikacja hosta jest odpowiedzialna za uzyskanie wszelkich niezbędnych wyrazów zgody użytkownika zgodnie z zasadami zgodności plików cookie UE.

Zasady dotyczące plików cookie można ustawić za pomocą [opcji](../reference.md#options)czytnika immersyjny.

## <a name="enable-cookie-usage"></a>Włącz użycie plików cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Enable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="disable-cookie-usage"></a>Wyłącz użycie plików cookie

```javascript
var options = {
    'cookiePolicy': ImmersiveReader.CookiePolicy.Disable
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="next-steps"></a>Następne kroki

* Wyświetl [Node.js przewodnika Szybki Start](../quickstarts/client-libraries.md?pivots=programming-language-nodejs) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK "immersyjny" przy użyciu Node.js
* Obejrzyj [Samouczek systemu Android](../tutorial-android.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK "immersyjny" przy użyciu języka Java lub Kotlin dla systemu Android
* Wyświetl [Samouczek systemu iOS](../tutorial-ios.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK "immersyjny" przy użyciu SWIFT dla systemu iOS
* Obejrzyj [samouczek języka Python](../tutorial-python.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK dla czytnika immersyjny przy użyciu języka Python
* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](../reference.md)