---
title: Migrowanie aplikacji IoT Central platformy Azure do wersji v3 | Microsoft Docs
description: Skontaktuj się z administratorem, aby dowiedzieć się, jak przeprowadzić migrację aplikacji platformy IoT Central Azure w wersji 2 na v3
author: troyhopwood
ms.author: troyhop
ms.date: 01/18/2021
ms.topic: conceptual
ms.service: iot-central
services: iot-central
ms.openlocfilehash: 3f81ae72af48ec934d1c2c2567ebdd212d8e0499
ms.sourcegitcommit: 3c3ec8cd21f2b0671bcd2230fc22e4b4adb11ce7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/25/2021
ms.locfileid: "98763466"
---
# <a name="migrate-your-v2-iot-central-application-to-v3"></a>Migrowanie aplikacji IoT Central v2 do wersji 3

*Ten artykuł ma zastosowanie do administratorów programu.*

Obecnie podczas tworzenia nowej aplikacji IoT Central jest to aplikacja w wersji 3. Jeśli wcześniej utworzono aplikację, a następnie w zależności od tego, kiedy ją utworzono, może to być wersja 2. W tym artykule opisano sposób migrowania wersji 2 do aplikacji v3, aby upewnić się, że korzystasz z najnowszych funkcji IoT Central.

Aby dowiedzieć się, jak zidentyfikować wersję aplikacji IoT Central, zobacz [Informacje o aplikacji](howto-get-app-info.md).

Kroki migracji aplikacji z wersji 2 do V3:

1. Utwórz nową aplikację v3 z poziomu aplikacji w wersji 2.
1. Skonfiguruj aplikację v3.
1. Usuń do aplikacji w wersji 2.

## <a name="create-a-new-v3-application"></a>Tworzenie nowej aplikacji v3

Użyj Kreatora migracji, aby utworzyć nową aplikację v3.

IoT Central nie obsługuje migracji do istniejącej aplikacji v3. Aby automatycznie przenieść istniejące urządzenia, użyj Kreatora migracji w celu utworzenia aplikacji w wersji 3.

Kreator migracji:

- Tworzy nową aplikację v3.
- Sprawdza zgodność szablonów urządzeń z wersjami v3.
- Kopiuje wszystkie szablony urządzeń do nowej aplikacji v3.
- Kopiuje wszystkich użytkowników i przypisania ról do nowej aplikacji v3.

> [!NOTE]
> Aby zapewnić zgodność urządzeń z wersjami v3, typy pierwotne w szablonie urządzenia stają się właściwościami obiektu. Nie musisz wprowadzać żadnych zmian w kodzie urządzenia.

Aby przeprowadzić migrację aplikacji do wersji v3, musisz być administratorem.

1. W menu **Administracja** wybierz pozycję **Migruj do aplikacji v3**:

    :::image type="content" source="media/howto-migrate/migrate-menu.png" alt-text="Zrzut ekranu przedstawiający Kreatora migracji aplikacji":::

1. Wprowadź nową **nazwę aplikacji** i opcjonalnie Zmień automatycznie wygenerowany  **adres URL**. Adres URL nie może być taki sam jak bieżący adres URL aplikacji w wersji 2. Można jednak zmienić adres URL później po usunięciu aplikacji w wersji 2.

1. Wybierz pozycję **Utwórz nową aplikację v3**.

    :::image type="content" source="media/howto-migrate/create-app.png" alt-text="Zrzut ekranu przedstawiający opcje w Kreatorze migracji aplikacji":::

    W zależności od liczby i złożoności szablonów urządzeń ten proces może potrwać kilka minut.

    > [!Warning]
    > Tworzenie aplikacji v3 zakończy się niepowodzeniem, jeśli którykolwiek szablon urządzenia zawiera pola, które zaczynają się od liczby lub zawierają jeden z następujących znaków (,,,,,,,,, `+` `*` ,, `?` `^` `$` `(` `)` `[` `]` `{` `}` `|` `\` ). Schemat szablonu urządzenia DTDL używany przez aplikacje V3 nie zezwala na te znaki. Zaktualizuj szablon urządzenia, aby rozwiązać ten problem przed migracją do wersji 3.

1. Gdy nowa aplikacja V3 jest gotowa, wybierz pozycję **Otwórz swoją nową aplikację** , aby ją otworzyć.

    :::image type="content" source="media/howto-migrate/open-app.png" alt-text="Zrzut ekranu przedstawiający Kreatora migracji aplikacji po migracji aplikacji":::

## <a name="configure-the-v3-application"></a>Konfigurowanie aplikacji v3

Po utworzeniu nowej aplikacji v3 Wprowadź wszelkie zmiany konfiguracji przed przeniesieniem urządzeń z aplikacji v2 do aplikacji v3.

> [!TIP]
> Poświęć chwilę na [zapoznanie się z wersją 3](overview-iot-central-tour.md#navigate-your-application) , ponieważ zawiera ona pewne różnice w poprzedniej wersji.

Poniżej przedstawiono kilka zalecanych czynności konfiguracyjnych, które należy wziąć pod uwagę:

- [Konfigurowanie pulpitów nawigacyjnych](howto-add-tiles-to-your-dashboard.md)
- [Konfigurowanie eksportu danych](howto-export-data.md)
- [Konfigurowanie reguł i akcji](quick-configure-rules.md)
- [Dostosowywanie interfejsu użytkownika aplikacji](howto-customize-ui.md)

Gdy aplikacja V3 jest skonfigurowana do zaspokajania Twoich potrzeb, możesz przenieść swoje urządzenia z aplikacji w wersji 2 do aplikacji v3.

## <a name="move-your-devices-to-the-v3-application"></a>Przenoszenie urządzeń do aplikacji v3

Po zakończeniu tego kroku wszystkie urządzenia komunikują się tylko z nową aplikacją v3.

> [!IMPORTANT]
> Przed przeniesieniem urządzeń do aplikacji v3 Usuń wszystkie urządzenia, które zostały utworzone w aplikacji v3.

Ten krok przenosi wszystkie istniejące urządzenia do nowej aplikacji v3. Dane urządzenia nie są kopiowane.

Wybierz pozycję **Przenieś wszystkie urządzenia** , aby rozpocząć przenoszenie urządzeń. Wykonanie tego kroku może potrwać kilka minut.

:::image type="content" source="media/howto-migrate/move-devices.png" alt-text="Zrzut ekranu przedstawiający Kreatora migracji aplikacji z opcją przenoszenia urządzeń":::

Po zakończeniu przenoszenia Uruchom ponownie wszystkie urządzenia, aby upewnić się, że łączą się z nową aplikacją v3.

> [!WARNING]
> Nie usuwaj aplikacji v3, ponieważ aplikacja w wersji 2 nie działa teraz.

## <a name="delete-your-old-v2-application"></a>Usuwanie starej aplikacji w wersji 2

Po sprawdzeniu, że wszystko działa zgodnie z oczekiwaniami w nowej aplikacji v3, Usuń starszą aplikację w wersji 2. Ten krok zapewnia, że nie są naliczane opłaty za aplikację, której już nie używasz.

> [!Note]
> Aby usunąć aplikację, musisz mieć uprawnienia do usuwania zasobów w ramach subskrypcji platformy Azure wybranej podczas tworzenia aplikacji. Aby dowiedzieć się więcej, zobacz [Korzystanie z kontroli dostępu opartej na rolach w celu zarządzania dostępem do zasobów subskrypcji platformy Azure](../../active-directory/role-based-access-control-configure.md).

1. W aplikacji w wersji 2 Wybierz kartę **Administracja** w menu.
2. Wybierz pozycję **Usuń** , aby trwale usunąć aplikację IoT Central. Ta opcja trwale usuwa wszystkie dane skojarzone z tą aplikacją.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak przeprowadzić migrację aplikacji, sugerowanym następnym krokiem jest zapoznanie się z [interfejsem użytkownika usługi Azure IoT Central](overview-iot-central-tour.md) , aby zobaczyć, co zmieniło się w wersji 3.
