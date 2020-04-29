---
title: Ustaw zasady plików cookie czytnika immersyjny
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób ustawiania zasad dotyczących plików cookie dla czytnika immersyjny.
services: cognitive-services
author: pasta
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: conceptual
ms.date: 01/06/2020
ms.author: pasta
ms.openlocfilehash: 6de651f1eb51ea1bc941c2af675c8a8d5b0f9cd7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75946112"
---
# <a name="how-to-set-the-cookie-policy-for-the-immersive-reader"></a>Jak ustawić zasady dotyczące plików cookie dla czytnika immersyjny

Czytnik immersyjny domyślnie wyłącza użycie plików cookie. W przypadku włączenia użycia plików cookie, czytnik immersyjny może używać plików cookie do obsługi preferencji użytkownika i śledzenia użycia funkcji. W przypadku włączenia użycia plików cookie w czytniku immersyjny należy wziąć pod uwagę wymagania zasad zgodności plików cookie UE. Aplikacja hosta jest odpowiedzialna za uzyskanie wszelkich niezbędnych wyrazów zgody użytkownika zgodnie z zasadami zgodności plików cookie UE.

Zasady dotyczące plików cookie można ustawić za pomocą [opcji](../reference.md#options)czytnika immersyjny. Aby uzyskać więcej informacji, zobacz [Wyliczenie CookiePolicy](../reference.md#cookiepolicy-enum) .

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

* Zobacz [Przewodnik Szybki Start środowiska Node. js](../quickstart-nodejs.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK programu immersyjny przy użyciu środowiska Node. js.
* Obejrzyj [samouczek języka Python](../tutorial-python.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK dla czytnika immersyjny przy użyciu języka Python
* Obejrzyj [samouczek SWIFT](../tutorial-ios-picture-immersive-reader.md) , aby zobaczyć, co jeszcze można zrobić z zestawem SDK "immersyjny" przy użyciu SWIFT
* Poznaj [zestaw SDK czytnika immersyjny](https://github.com/microsoft/immersive-reader-sdk) i [Kompendium zestawu SDK czytnika immersyjny](../reference.md)