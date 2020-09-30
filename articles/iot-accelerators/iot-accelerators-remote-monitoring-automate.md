---
title: Korzystanie z rozwiązania do zdalnego monitorowania w celu wykrywania problemów z urządzeniami — platforma Azure | Microsoft Docs
description: W tym samouczku pokazano, w jaki sposób korzystać z reguł i akcji do automatycznego wykrywania problemów z urządzeniami dotyczących wartości progowych w rozwiązaniu do monitorowania zdalnego.
author: dominicbetts
manager: timlt
ms.author: dobett
ms.service: iot-accelerators
services: iot-accelerators
ms.date: 11/08/2018
ms.topic: tutorial
ms.custom: mvc
ms.openlocfilehash: 695d1b1124c042b61837f0a1257ac2a201ae4860
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91539405"
---
# <a name="tutorial-detect-issues-with-devices-connected-to-your-monitoring-solution"></a>Samouczek: wykrywanie problemów z urządzeniami połączonymi z rozwiązaniem do monitorowania

W tym samouczku skonfigurujesz akcelerator rozwiązania do monitorowania zdalnego, aby wykryć problemy z połączonymi urządzeniami IoT. Aby wykryć problemy z urządzeniami, dodasz reguły alertów generujące alerty na pulpicie nawigacyjnym rozwiązania.

Aby wprowadzić reguły i alerty, w tym samouczku używane jest symulowane urządzenie: chłodnia. Chłodnia jest zarządzana przez organizację o nazwie Contoso i jest połączona z akceleratorem rozwiązania do monitorowania zdalnego. Firma Contoso ma już regułę, która generuje alert krytyczny, gdy ciśnienie w chłodni przekracza wartość 298 PSI. Jako operator w firmie Contoso chcesz zidentyfikować chłodnie, w których mogą występować problemy z czujnikami, szukając skoków ciśnienia. Aby zidentyfikować takie urządzenia, dodajesz regułę, która wygeneruje alert ostrzegawczy, gdy ciśnienie w chłodni przekroczy wartość 150 PSI.

Poproszono Cię również o utworzenie alertu krytycznego dla chłodni w sytuacji, gdy w ciągu ostatnich pięciu minut średnia wilgotność wewnątrz urządzenia przekroczyła 80%, a temperatura urządzenia przekroczyła 75 stopni Fahrenheita.

W tym samouczku zostały wykonane następujące czynności:

>[!div class="checklist"]
> * Wyświetlanie reguł w rozwiązaniu
> * Tworzenie reguły
> * Tworzenie reguły z wieloma warunkami
> * Edytowanie istniejącej reguły
> * Włączanie i wyłącznie reguł

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [iot-accelerators-tutorial-prereqs](../../includes/iot-accelerators-tutorial-prereqs.md)]

## <a name="review-the-existing-rules"></a>Przeglądanie istniejących reguł

Na stronie **Reguły** akceleratora rozwiązań wyświetlana jest lista wszystkich bieżących reguł:

[![Strona reguł](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactions_v2-expanded.png#lightbox)

Aby wyświetlić tylko te reguły, które dotyczą chłodni, zastosuj filtr. Aby wyświetlić więcej informacji na temat reguły i edytować ją, wybierz ją na liście:

[![Wyświetl szczegóły reguły](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdetail_v2-expanded.png#lightbox)

## <a name="create-a-rule"></a>Tworzenie reguły

Aby utworzyć regułę, która wygeneruje ostrzeżenie, gdy ciśnienie w chłodni przekroczy wartość 150 PSI, kliknij przycisk **Nowa reguła**. Użyj następujących wartości, aby utworzyć regułę:

| Ustawienie          | Wartość                                 |
| ---------------- | ------------------------------------- |
| Nazwa reguły        | Ostrzeżenie o chłodni                       |
| Opis      | Ciśnienie w chłodni przekroczyło wartość 150 PSI |
| Grupa urządzeń     | Grupa urządzeń **Chillers** (Chłodnie)             |
| Obliczenia      | Błyskawiczne                               |
| Warunek 1 — pole| ciśnienie                              |
| Warunek 1 — operator | Większe niż                      |
| Warunek 1 — wartość    | 150                               |
| Poziom ważności  | Ostrzeżenie                               |

[![Utwórz regułę ostrzeżenia](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_v2-expanded.png#lightbox)

Aby zapisać nową regułę, kliknij przycisk **Zastosuj**.

Gdy reguła zostanie wyzwolona, zobaczysz to na stronie **Reguły** lub na stronie **Pulpit nawigacyjny**:

[![Wyzwolono regułę ostrzeżenia](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/warningruletriggered-expanded.png#lightbox)

## <a name="create-an-advanced-rule"></a>Tworzenie zaawansowanej reguły

Aby utworzyć regułę z wieloma warunkami, która wygeneruje alert krytyczny, gdy średnia wilgotność w chłodni z ostatnich pięciu minut przekroczy 80%, a średnia temperatura przekroczy 75 stopni Fahrenheita, kliknij przycisk **Nowa reguła**. Użyj następujących wartości, aby utworzyć regułę:

| Ustawienie          | Wartość                                 |
| ---------------- | ------------------------------------- |
| Nazwa reguły        | Wilgotność i temperatura chłodni krytyczne    |
| Opis      | Wartości wilgotności i temperatury są krytyczne |
| Grupa urządzeń     | Grupa urządzeń **Chillers** (Chłodnie)             |
| Obliczenia      | Średnia                               |
| Okres      | 5                                     |
| Warunek 1 — pole| humidity                              |
| Warunek 1 — operator | Większe niż                      |
| Warunek 1 — wartość    | 80                                |
| Poziom ważności  | Krytyczne                              |

[![Utwórz jedną część reguły z wieloma warunkami](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_v2-expanded.png#lightbox)

Aby dodać drugi warunek, kliknij pozycję „+ Dodaj warunek”. Użyj następujących wartości dla nowego warunku:

| Ustawienie          | Wartość                                 |
| ---------------- | ------------------------------------- |
| Warunek 2 — pole| temperature                           |
| Warunek 2 — operator | Większe niż                      |
| Warunek 2 — wartość    | 75                                |

[![Utwórz dwie części reguły z wieloma warunkami](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsnewrule_mult_cond2_v2-expanded.png#lightbox)

Aby zapisać nową regułę, kliknij przycisk **Zastosuj**.

Gdy reguła zostanie wyzwolona, zobaczysz to na stronie **Reguły** lub na stronie **Pulpit nawigacyjny**:

[![Wyzwolono wiele reguł warunków](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-inline.png)](./media/iot-accelerators-remote-monitoring-automate/criticalruletriggered-expanded.png#lightbox)

## <a name="edit-an-existing-rule"></a>Edytowanie istniejącej reguły

Aby wprowadzić zmiany w istniejącej regule, wybierz ją z listy reguł, a następnie kliknij przycisk **Edytuj**:

[![Edytuj regułę](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsedit_v2-expanded.png#lightbox)

## <a name="disable-a-rule"></a>Wyłączanie reguły

Aby tymczasowo wyłączyć regułę, możesz wyłączyć ją na liście reguł. Wybierz regułę, którą chcesz wyłączyć, a następnie wybierz polecenie **Wyłącz**. Pozycja **Stan** reguły na liście zmienia się, wskazując, że reguła jest teraz wyłączona. Możesz ponownie włączyć wcześniej wyłączoną regułę, wykonując te same czynności.

[![Wyłącz regułę](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdisable-expanded.png#lightbox)

Możesz włączać i wyłączać wiele reguł jednocześnie, wybierając wiele reguł na liście.

## <a name="delete-a-rule"></a>Usuwanie reguły

Aby trwale usunąć regułę, możesz usunąć ją na liście reguł. Wybierz regułę, którą chcesz usunąć, a następnie wybierz polecenie **Usuń**.

[![Zrzut ekranu przedstawiający stronę "reguły" z wyróżnionym oknem "Usuń regułę".](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdelete-expanded.png#lightbox)

Po potwierdzeniu, że chcesz usunąć regułę, istnieje możliwość usunięcia wszelkich alertów skojarzonych z tą regułą ze strony **Konserwacja**.

[![Usuń regułę](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-inline.png)](./media/iot-accelerators-remote-monitoring-automate/rulesactionsdeletetidy-expanded.png#lightbox)

Jednocześnie można usunąć tylko jedną regułę.

[!INCLUDE [iot-accelerators-tutorial-cleanup](../../includes/iot-accelerators-tutorial-cleanup.md)]

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono sposób korzystania ze strony **Reguły** w akceleratorze rozwiązania do monitorowania zdalnego w celu tworzenia reguł, które wywołują alerty w rozwiązaniu, i zarządzania nimi. Aby dowiedzieć się, w jaki sposób korzystać z akceleratora rozwiązań w celu konfigurowania połączonych urządzeń i zarządzania nimi, przejdź do kolejnego samouczka.

> [!div class="nextstepaction"]
> [Konfigurowanie urządzeń połączonych z rozwiązaniem do monitorowania i zarządzanie nimi](iot-accelerators-remote-monitoring-manage.md)