---
title: Szablony Notification Hubs platformy Azure
description: Dowiedz się więcej o korzystaniu z szablonów dla usługi Azure Notification Hubs.
services: notification-hubs
documentationcenter: .net
author: sethmanheim
manager: femila
editor: jwargo
ms.assetid: a41897bb-5b4b-48b2-bfd5-2e3c65edc37e
ms.service: notification-hubs
ms.workload: mobile
ms.tgt_pltfrm: mobile-multiple
ms.devlang: multiple
ms.topic: article
ms.date: 01/04/2019
ms.author: sethm
ms.reviewer: jowargo
ms.lastreviewed: 01/04/2019
ms.openlocfilehash: c4c1c247d8fb248c5e6d548dd04af1c3d08a4e76
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100588331"
---
# <a name="notification-hubs-templates"></a>Szablony Notification Hubs

Szablony umożliwiają aplikacji klienckiej określenie dokładnego formatu powiadomień, które chce otrzymywać. Korzystając z szablonów, aplikacja może realizować kilka różnych korzyści, w tym następujące:

- Zaplecze platformy niezależny od
- Spersonalizowane powiadomienia
- Niezależność wersji klienta
- Łatwa lokalizacja

Ta sekcja zawiera dwa szczegółowe przykłady użycia szablonów do wysyłania powiadomień platformy niezależny od dla wszystkich urządzeń na różnych platformach i personalizowania powiadomień dotyczących emisji do poszczególnych urządzeń.

## <a name="using-templates-cross-platform"></a>Korzystanie z różnych platform szablonów

Standardowym sposobem wysyłania powiadomień wypychanych jest wysłanie dla każdego powiadomienia, które ma zostać wysłane, określonego ładunku do usług powiadomień platformy (WNS, APNS). Na przykład w celu wysłania alertu do usługi APNS ładunek jest obiektem JSON o następującej postaci:

```json
{"aps": {"alert" : "Hello!" }}
```

Aby wysłać podobny wyskakujący komunikat w aplikacji ze sklepu Windows, ładunek XML jest następujący:

```xml
<toast>
  <visual>
    <binding template=\"ToastText01\">
      <text id=\"1\">Hello!</text>
    </binding>
  </visual>
</toast>
```

Możesz tworzyć podobne ładunki dla platform usługi MPNS (Windows Phone) i FCM (Android).

Ten wymóg wymusza zaplecze aplikacji, aby generować różne ładunki dla każdej platformy i efektywnie sprawiać, że baza danych jest odpowiedzialna za część warstwy prezentacji aplikacji. Niektóre zagadnienia obejmują lokalizacje i układy graficzne (szczególnie w przypadku aplikacji ze sklepu Windows, które obejmują powiadomienia dla różnych typów kafelków).

Funkcja szablon Notification Hubs umożliwia aplikacji klienckiej tworzenie rejestracji specjalnych o nazwie Rejestracja szablonów, która obejmuje oprócz zestawu tagów, szablonu. Funkcja szablon Notification Hubs umożliwia aplikacji klienckiej kojarzenie urządzeń z szablonami niezależnie od tego, czy pracujesz z instalacjami (preferowanymi) czy rejestracjami. Ze względu na powyższe przykłady ładunku jedynymi informacjami niezależnymi od platformy jest rzeczywisty komunikat alertu (Hello!). Szablon to zestaw instrukcji dla centrum powiadomień, w którym można sformatować komunikat niezależny od platformy dla rejestracji tej konkretnej aplikacji klienckiej. W poprzednim przykładzie komunikat niezależny od platformy jest pojedynczą właściwością: `message = Hello!` .

Na poniższej ilustracji przedstawiono proces:

![Diagram przedstawiający proces korzystania z wieloplatformowych szablonów](./media/notification-hubs-templates/notification-hubs-hello.png)

Szablon rejestracji aplikacji klienckiej systemu iOS jest następujący:

```json
{"aps": {"alert": "$(message)"}}
```

Odpowiedni szablon aplikacji klienckiej sklepu Windows to:

```xml
<toast>
    <visual>
        <binding template=\"ToastText01\">
            <text id=\"1\">$(message)</text>
        </binding>
    </visual>
</toast>
```

Zwróć uwagę, że rzeczywisty komunikat jest zastępowany dla wyrażenia $ (Message). To wyrażenie instruuje centrum powiadomień, za każdym razem, gdy wysyła komunikat do tej konkretnej rejestracji, aby skompilować komunikat, który następuje po nim, i przełączników we wspólnej wartości.

W przypadku pracy z modelem instalacji klucz "Szablony" zawiera dane JSON wielu szablonów. Jeśli pracujesz z modelem rejestracji, aplikacja kliencka może utworzyć wiele rejestracji w celu użycia wielu szablonów. na przykład szablon komunikatów alertów i szablon aktualizacji kafelków. Aplikacje klienckie mogą również mieszać natywne rejestracje (rejestracje bez szablonu) i rejestracje szablonów.

Centrum powiadomień wysyła jedno powiadomienie dla każdego szablonu bez względu na to, czy należą do tej samej aplikacji klienckiej. Takie zachowanie może służyć do tłumaczenia niezależnych od platformy powiadomień w celu uzyskania większej liczby powiadomień. Na przykład ten sam komunikat niezależny od platformy do centrum powiadomień może być bezproblemowo tłumaczony w wyskakującym alercie i aktualizację kafelka, bez konieczności informowania o tym o nim. Niektóre platformy (na przykład iOS) mogą zwinąć wiele powiadomień do tego samego urządzenia, jeśli są wysyłane w krótkim czasie.

## <a name="using-templates-for-personalization"></a>Używanie szablonów do personalizacji

Kolejną zaletą korzystania z szablonów jest możliwość użycia Notification Hubs do przeprowadzenia personalizacji powiadomień dla każdej rejestracji. Rozważmy na przykład aplikację pogody, która wyświetla kafelek z warunkami pogodowymi w określonej lokalizacji. Użytkownik może wybrać między c i Fahrenheita stopni oraz jedną lub pięć dni prognozy. Przy użyciu szablonów Każda instalacja aplikacji klienckiej może zostać zarejestrowana w formacie wymagany (1-dniowy, 1-dniowy, 5 dni, Celsjusza, 5 dni w języku Fahrenheita), a zaplecze, który zawiera wszystkie informacje wymagane do wypełnienia tych szablonów (na przykład w przypadku prognozowania pięciu dni z c i Fahrenheita stopni).

Szablon jednodniowej prognozy z temperaturami c jest następujący:

```xml
<tile>
  <visual>
    <binding template="TileWideSmallImageAndText04">
      <image id="1" src="$(day1_image)" alt="alt text"/>
      <text id="1">Seattle, WA</text>
      <text id="2">$(day1_tempC)</text>
    </binding>  
  </visual>
</tile>
```

Komunikat wysłany do centrum powiadomień zawiera wszystkie następujące właściwości:

<table border="1">

<tr><td>day1_image</td><td>day2_image</td><td>day3_image</td><td>day4_image</td><td>day5_image</td></tr>

<tr><td>day1_tempC</td><td>day2_tempC</td><td>day3_tempC</td><td>day4_tempC</td><td>day5_tempC</td></tr>

<tr><td>day1_tempF</td><td>day2_tempF</td><td>day3_tempF</td><td>day4_tempF</td><td>day5_tempF</td></tr>
</table><br/>

Przy użyciu tego wzorca wewnętrzna baza danych wysyła tylko jeden komunikat bez konieczności przechowywania określonych opcji personalizacji dla użytkowników aplikacji. Na poniższej ilustracji przedstawiono ten scenariusz:

![Diagram przedstawiający sposób, w jaki zaplecza wysyła tylko jeden komunikat do każdej platformy.](./media/notification-hubs-templates/notification-hubs-registration-specific.png)

## <a name="how-to-register-templates"></a>Jak zarejestrować szablony

Aby zarejestrować się w szablonach przy użyciu modelu instalacji (preferowany) lub modelu rejestracji, zobacz [Zarządzanie rejestracją](notification-hubs-push-notification-registration-management.md).

## <a name="template-expression-language"></a>Język wyrażeń szablonu

Szablony są ograniczone do formatów dokumentu XML lub JSON. Ponadto można umieścić wyrażenia tylko w określonych miejscach; na przykład atrybuty węzła lub wartości dla XML, wartości właściwości ciągu dla formatu JSON.

W poniższej tabeli przedstawiono język dozwolony w szablonach:

| Wyrażenie       | Opis |
| ---------------- | --- |
| $ (PROP)          | Odwołanie do właściwości zdarzenia o podaną nazwę. W nazwach właściwości nie jest rozróżniana wielkość liter. To wyrażenie jest rozpoznawane jako wartość tekstowa właściwości lub do pustego ciągu, jeśli właściwość nie jest obecna. |
| $ (prop, n)       | Jak powyżej, ale tekst jest jawnie przycięty do n znaków, na przykład $ (title, 20) przycina zawartość Właściwości title do 20 znaków. |
| . (prop, n)       | Jak powyżej, ale tekst jest sufiksem z trzema kropkami, gdy jest obcinany. Łączny rozmiar ciągu przycinanego i sufiksu nie przekracza n znaków. . (tytuł, 20) z właściwością wejściową "jest to wiersz tytułu" w wyniku **tego jest to tytuł...** |
| % (PROP)          | Podobne do $ (Name), z tą różnicą, że dane wyjściowe są kodowane przy użyciu identyfikatora URI. |
| # (PROP)          | Używane w szablonach JSON (na przykład w przypadku szablonów dla systemów iOS i Android).<br><br>Ta funkcja działa dokładnie tak samo jak wartość $ (PROP), z wyjątkiem sytuacji, gdy są używane w szablonach JSON (na przykład szablony firmy Apple). W tym przypadku, jeśli ta funkcja nie jest ujęta w nawiasy "{", "}" (na przykład "myJsonProperty": "# (nazwa)"), a jej wartość jest równa liczbie w formacie JavaScript, na przykład RegExp: (0&#124; (&#91;1-9&#93;&#91;0-9&#93; *)) ( \.&#91;0-9&#93;+)? ( (e&#124;E) (+&#124;-)? &#91;0-9&#93;+)?, a następnie wyjściowy kod JSON jest liczbą.<br><br>Na przykład "znaczek: ' # (nazwa) ' zmieni się na" znaczek ": 40 (a nie" 40 "). |
| "text" lub "text" | Literał. Literały zawierają dowolny tekst w pojedynczym lub podwójnym cudzysłowie. |
| Wyr1 + expr2    | Operator łączenia łączący dwa wyrażenia w jeden ciąg. |

Wyrażenia mogą być dowolnym z powyższych formularzy.

W przypadku korzystania z łączenia, całe wyrażenie musi być ujęte w nawias `{}` . Na przykład `{$(prop) + ‘ - ’ + $(prop2)}`.

Na przykład następujący szablon nie jest prawidłowym szablonem XML:

```xml
<tile>
  <visual>
    <binding $(property)>
      <text id="1">Seattle, WA</text>
    </binding>  
  </visual>
</tile>
```

Jak wyjaśniono wcześniej, w przypadku korzystania z łączenia wyrażenia muszą być opakowane w nawiasy klamrowe. Na przykład:

```xml
<tile>
  <visual>
    <binding template="ToastText01">
      <text id="1">{'Hi, ' + $(name)}</text>
    </binding>  
  </visual>
</tile>
```

## <a name="next-steps"></a>Następne kroki

[Dowiedz się więcej o usłudze Azure Notification Hubs](notification-hubs-push-notification-overview.md)
