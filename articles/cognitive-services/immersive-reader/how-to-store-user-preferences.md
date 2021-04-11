---
title: Zapisywanie preferencji użytkownika
titleSuffix: Azure Cognitive Services
description: W tym artykule przedstawiono sposób przechowywania preferencji użytkownika.
author: metanMSFT
manager: guillasi
ms.service: cognitive-services
ms.subservice: immersive-reader
ms.topic: how-to
ms.date: 06/29/2020
ms.author: metang
ms.openlocfilehash: 16ecd2166604d29fbc2242229f625b30ffd684e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "102617696"
---
# <a name="how-to-store-user-preferences"></a>Jak przechowywać preferencje użytkownika

W tym artykule przedstawiono sposób przechowywania ustawień interfejsu użytkownika, które są znane jako **Preferencje użytkownika**, za pomocą opcji [-Preferences](./reference.md#options) i [-onPreferencesChanged](./reference.md#options) dbreader zestawu SDK.

Gdy opcja zestawu [CookiePolicy](./reference.md#cookiepolicy-options) SDK jest ustawiona na wartość *włączone*, aplikacja czytnika immersyjny przechowuje **Preferencje użytkownika** (rozmiar tekstu, kolor motywu, czcionkę itd.) w plikach cookie, które są lokalne dla konkretnej przeglądarki i urządzenia. Za każdym razem, gdy użytkownik uruchamia czytnik immersyjny w tej samej przeglądarce i urządzeniu, zostanie on otwarty z preferencjami użytkownika z ostatniej sesji na tym urządzeniu. Jeśli jednak użytkownik otworzy czytnik immersyjny w innej przeglądarce lub urządzeniu, ustawienia zostaną początkowo skonfigurowane z domyślnymi ustawieniami czytnika immersyjny, a użytkownik będzie musiał ponownie ustawić swoje preferencje i tak dalej dla każdego używanego urządzenia. `-preferences` `-onPreferencesChanged` Opcje zestawu SDK czytnika immersyjny umożliwiają aplikacjom mobilny dostęp do preferencji użytkownika w różnych przeglądarkach i urządzeniach, dzięki czemu użytkownik ma spójne środowisko, wszędzie tam, gdzie korzystają z aplikacji.

Po pierwsze, dostarczając `-onPreferencesChanged` opcję zestawu SDK wywołania zwrotnego podczas uruchamiania aplikacji czytnik immersyjny, czytnik immersyjny wyśle ciąg z `-preferences` powrotem do aplikacji hosta za każdym razem, gdy użytkownik zmieni Preferencje podczas sesji czytnika. Aplikacja hosta jest odpowiedzialna za przechowywanie preferencji użytkownika w ich własnym systemie. Następnie, gdy ten sam użytkownik ponownie uruchomi czytnik immersyjny, aplikacja hosta może pobrać preferencje tego użytkownika z magazynu, a następnie podać je jako `-preferences` opcję zestawu ciąg SDK podczas uruchamiania aplikacji czytnika immersyjny, aby przywrócić preferencje użytkownika.

Ta funkcja może być używana jako alternatywna metoda przechowywania **preferencji użytkownika** w przypadku, gdy używanie plików cookie nie jest pożądane lub możliwe.

> [!CAUTION]
> **Ważne** Nie należy podejmować próby programistycznej zmiany wartości `-preferences` ciągu wysyłanego do i z aplikacji czytnika immersyjny, ponieważ może to spowodować nieoczekiwane zachowanie w przypadku użytkowników. Aplikacje hosta nigdy nie powinny przypisywać wartości niestandardowych do ciągu ani manipulować nimi `-preferences` . Korzystając z `-preferences` opcji String, należy używać tylko dokładnej wartości, która została zwrócona z `-onPreferencesChanged` opcji wywołania zwrotnego.

## <a name="how-to-enable-storing-user-preferences"></a>Jak włączyć przechowywanie preferencji użytkownika

parametr [launchAsync](./reference.md#launchasync) zestawu SDK czytnika immersyjny `options` zawiera `-onPreferencesChanged` wywołanie zwrotne. Ta funkcja będzie wywoływana, gdy użytkownik zmieni swoje preferencje. `value`Parametr zawiera ciąg, który reprezentuje bieżące preferencje użytkownika. Ten ciąg jest następnie przechowywany dla tego użytkownika przez aplikację hosta.

```typescript
const options = {
    onPreferencesChanged: (value: string) => {
        // Store user preferences here
    }
};

ImmersiveReader.launchAsync(YOUR_TOKEN, YOUR_SUBDOMAIN, YOUR_DATA, options);
```

## <a name="how-to-load-user-preferences-into-the-immersive-reader"></a>Jak załadować preferencje użytkownika do czytnika immersyjny

Przekaż preferencje użytkownika do czytnika immersyjny przy użyciu `-preferences` opcji. Oto uproszczony przykład przechowywania i ładowania preferencji użytkownika:

```typescript
const storedUserPreferences = localStorage.getItem("USER_PREFERENCES");
let userPreferences = storedUserPreferences === null ? null : storedUserPreferences;
const options = {
    preferences: userPreferences,
    onPreferencesChanged: (value: string) => {
        userPreferences = value;
        localStorage.setItem("USER_PREFERENCES", userPreferences);
    }
};
```

## <a name="next-steps"></a>Następne kroki

* Poznaj [Kompendium zestawu SDK czytnika immersyjny](./reference.md)