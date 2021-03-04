---
title: Samouczek dotyczący konfigurowania urządzenia Azure Data Box Heavy | Microsoft Docs
description: W tym samouczku dowiesz się, jak podłączyć kable do urządzenia Azure Data Box Heavy, nawiązać z nim połączenie i włączyć.
services: databox
author: alkohli
ms.service: databox
ms.subservice: heavy
ms.topic: tutorial
ms.date: 09/03/2019
ms.author: alkohli
ms.localizationpriority: high
ms.openlocfilehash: c38ff7e642017afe5f220ae26d3a04c2c0b706ee
ms.sourcegitcommit: 4f1c7df04a03856a756856a75e033d90757bb635
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/07/2020
ms.locfileid: "87920946"
---
::: zone target = "docs"

# <a name="tutorial-cable-and-connect-to-your-azure-data-box-heavy"></a>Samouczek: Podłączanie kabli do urządzenia Azure Data Box Heavy i nawiązywanie z nim połączenia

::: zone-end

::: zone target = "chromeless"

## <a name="get-started-with-azure-data-box-heavy"></a>Rozpoczynanie pracy z urządzeniem Azure Data Box Heavy

::: zone-end

::: zone target = "docs"

W tym samouczku opisano, jak podłączyć kable do urządzenia Azure Data Box Heavy, włączyć je i nawiązać z nim połączenie.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Podłączanie kabli do urządzenia Data Box Heavy
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy

::: zone-end

::: zone target = "chromeless"

Ten przewodnik zawiera instrukcje dotyczące sprawdzania wymagań wstępnych, podłączania kabli do urządzenia i nawiązywania z nim połączenia, kopiowania danych i przekazywania ich na platformę Azure, a następnie weryfikowania przekazanych danych.

::: zone-end

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem upewnij się, że:

1. Ukończono [Samouczek: Zamawianie urządzenia Azure Data Box Heavy](data-box-heavy-deploy-ordered.md).
2. Urządzenie Data Box Heavy zostało do Ciebie dostarczone, a stan zamówienia w portalu to **Dostarczono**.
3. Zapoznano się z [wytycznymi bezpieczeństwa dotyczącymi urządzenia Data Box Heavy](data-box-safety.md).
4. Musisz mieć dostęp do płaskiej powierzchni w centrum danych znajdującej się blisko dostępnego połączenia sieciowego, gdzie umieścisz urządzenie. Tego urządzenia nie można zamontować w stojaku.
5. Odebrano cztery kable zasilające z uziemieniem do korzystania z urządzenia magazynującego.
6. Należy posiadać komputer hosta podłączony do sieci centrum danych. Za pomocą urządzenia Data Box Heavy zostaną skopiowane dane z tego komputera. Na komputerze hoście musi działać [obsługiwany system operacyjny](data-box-heavy-system-requirements.md).
7. Twoje centrum danych musi mieć dostęp do szybkiej sieci. Zdecydowanie zaleca się posiadanie co najmniej jednego połączenia 10 GbE. 
8. Aby połączyć się z lokalnym interfejsem użytkownika i skonfigurować urządzenie, potrzebny jest komputer przenośny i kabel RJ-45. Za pomocą komputera przenośnego skonfigurujesz jednorazowo poszczególne węzły na urządzeniu.
9. Potrzebny jest jeden kabel o przepustowości 40 Gb/s lub po jednym kablu o przepustowości 10 Gb/s dla każdego węzła urządzenia.
    - Wybierz kable, które są zgodne z interfejsem sieciowym [Mellanox MCX314A-BCCT](https://store.mellanox.com/products/mellanox-mcx314a-bcct-connectx-3-pro-en-network-interface-card-40-56gbe-dual-port-qsfp-pcie3-0-x8-8gt-s-rohs-r6.html).
    - W przypadku kabla o przepustowości 40 Gb/s po stronie urządzenia musi on być zgodny ze standardem QSFP+.
    - W przypadku kabla o przepustowości 10 Gb/s potrzebny jest kabel SFP+ zakończony z jednej strony wtyczką umożliwiającą podłączenie do przełącznika 10 Gb/s, a ze strony podłączanej do urządzenia zakończony adapterem QSFP+ do SFP+ (lub adapterem QSA).

::: zone target = "docs"

## <a name="cable-your-device-for-power"></a>Podłączanie kabli zasilających do urządzenia

Wykonaj poniższe kroki, aby podłączyć kable do urządzenia.

1. Sprawdź, czy urządzenie nie ma śladów naruszenia ani innych widocznych uszkodzeń. Jeśli urządzenie jest naruszone lub poważnie uszkodzone, nie kontynuuj. Od razu [skontaktuj się z pracownikiem pomocy technicznej firmy Microsoft](data-box-disk-contact-microsoft-support.md), który pomoże Ci ocenić, czy urządzenie jest w dobrym stanie, czy konieczne jest wysłanie nowego.
2. Przetransportuj urządzenie do miejsca instalacji.

    ![Miejsce instalacji urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-install-site.png)

3. Zablokuj tylne kółka urządzenia, jak pokazano poniżej.

    ![Zablokowane kółka urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-casters-locked.png)

4. Znajdź uchwyty, za pomocą których można odblokować przednie i tylne drzwiczki urządzenia. Odblokuj przednie drzwiczki i umieść je na boku urządzenia. Wykonaj te same czynności w przypadku tylnych drzwiczek.
    Drzwiczki muszą pozostawać otwarte podczas działania urządzenia, aby zapewnić optymalny przepływ powietrza od przodu do tyłu urządzenia.

    ![Otwarte drzwiczki urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-doors-open.png)

5. W zasobniku z tyłu urządzenia powinny znajdować się cztery kable zasilające. Wyjmij wszystkie kable z zasobnika.

    ![Kable zasilające urządzenia Data Box Heavy w zasobniku](media/data-box-heavy-deploy-set-up/data-box-heavy-power-cords-tray.png)

6. Następnym krokiem jest zidentyfikowanie różnych portów w tylnej części urządzenia. Urządzenie ma dwa węzły o nazwach **NODE1** i **NODE2**. Każdy węzeł ma cztery interfejsy sieciowe: **MGMT**, **DATA1**, **DATA2** i **DATA3**. Interfejs **MGMT** służy do konfigurowania zarządzania podczas początkowej konfiguracji urządzenia. Interfejsy **DATA1**-**DATA3** to porty danych. Porty **MGMT** i **DATA3** mają przepustowość 1 Gb/s, a interfejsy **DATA1** i **DATA2** mogą działać jako porty o przepustowości 40 Gb/s lub 10 Gb/s. Pod węzłami urządzenia znajdują się cztery jednostki zasilające (PSU), które są współużytkowane przez oba węzły urządzenia. Od lewej do prawej jednostki zasilające **PSU** mają oznaczenie **PSU1**, **PSU2**, **PSU3** i **PSU4**.

    ![Porty urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports.png)

7. Podłącz wszystkie cztery kable zasilające do jednostek zasilających urządzenia. Zielone diody LED włączą się i zaczną migać.
8. Włącz węzły urządzenia za pomocą przycisków zasilania na panelu przednim. Wciśnij i przytrzymaj przycisk zasilania przez kilka sekund, aż zaświecą się niebieskie wskaźniki. Wszystkie zielone diody LED jednostek zasilających z tyłu urządzenia powinny teraz przestać migać i zacząć świecić stałym światłem. Na przednim panelu operacyjnym urządzenia znajdują się również diody LED błędów. Świecąca dioda LED błędów oznacza niesprawną jednostkę zasilającą lub wentylator bądź wskazuje na problem z napędami dysków.  

    ![Przedni panel operacyjny urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-front-ops-panel.png)

## <a name="cable-first-node-for-network"></a>Podłączanie kabli sieciowych do pierwszego węzła

Na jednym z węzłów urządzenia wykonaj następujące kroki, aby podłączyć kable sieciowe.

1. Aby podłączyć komputer hosta do portu zarządzania 1 Gb/s, należy użyć kabla sieciowego CAT 6 RJ-45 (górny kabel z prawej strony na ilustracji, podłączony do wtyczki oznaczonej MGMT).
2. Za pomocą kabla QSFP+ (światłowodowego lub miedzianego) podłącz co najmniej jeden interfejs sieciowy o przepustowości 40 Gb/s (preferowany względem interfejsu 1 Gb/s) do przesyłania danych. W przypadku korzystania z przełącznika 10 Gb/s użyj kabla SFP+ z adapterem QSFP+ do SFP+ (adapter QSA) w celu podłączenia interfejsu sieciowego 40 Gb/s do przesyłania danych.

    ![Okablowanie portów urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-ports-cabled.png)

    > [!IMPORTANT]
    > Porty DATA1 i DATA2 są przełączone i nie odpowiadają portom wyświetlanym w lokalnym internetowym interfejsie użytkownika.
    > Adapter kabla 40 Gb/s umożliwi połączenie, jeśli zostanie podłączony w sposób pokazany poniżej.

    ![Adapter kabla 40 Gb/s urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-cable-adaptor.png)

## <a name="configure-first-node"></a>Konfigurowanie pierwszego węzła

Wykonaj następujące kroki, aby skonfigurować urządzenie przy użyciu konfiguracji lokalnej i witryny Azure Portal.

1. Pobierz poświadczenia urządzenia z portalu. Przejdź do obszaru **Ogólne > Szczegóły urządzenia**. Skopiuj wartość z pola **Hasło urządzenia**. Hasła są powiązane z określonym zamówieniem w portalu. Wyświetlane są dwa numery seryjne odpowiadające dwóm węzłom urządzenia Data Box Heavy. Hasło administratora urządzenia dla obu węzłów jest takie samo.

    ![Poświadczenia urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-device-credentials.png)

2. Podłącz kliencką stację roboczą do urządzenia za pośrednictwem kabla sieciowego CAT6 RJ-45.
3. Skonfiguruj kartę Ethernet na komputerze używanym do nawiązywania połączenia z urządzeniem za pomocą statycznego adresu IP `192.168.100.5` i podsieci `255.255.255.0`.

    ![Nawiązywanie połączenia z urządzeniem Data Box Heavy za pomocą lokalnego internetowego interfejsu użytkownika](media/data-box-heavy-deploy-set-up/data-box-heavy-connect-local-web-ui.png)

4. Połącz się z lokalnym internetowym interfejsem użytkownika urządzenia, korzystając z następującego adresu URL: `http://192.168.100.10`. Kliknij pozycję **Advanced** (Zaawansowane), a następnie pozycję **Proceed to 192.168.100.10 (unsafe)** (Przejdź do adresu 192.168.100.10 — niebezpieczne).
5. Zostanie wyświetlona strona **Logowanie** dla lokalnego internetowego interfejsu użytkownika.
    
    - Jeden z numerów seryjnych węzłów na tej stronie jest taki sam w interfejsie użytkownika portalu i w lokalnym internetowym interfejsie użytkownika. Zanotuj numer węzła, do którego należy ten numer seryjny. W portalu są wyświetlane dwa węzły i dwa numery seryjne urządzenia. To mapowanie pomaga zrozumieć, któremu węzłowi odpowiada konkretny numer seryjny.
    - Urządzenie jest w tym momencie zablokowane.
    - Podaj hasło administratora urządzenia uzyskane w poprzednim kroku, aby zalogować się do urządzenia. Kliknij pozycję **Zaloguj się**.

    ![Logowanie do lokalnego internetowego interfejsu użytkownika urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-unlock-device.png)

5. Na pulpicie nawigacyjnym upewnij się, że interfejsy sieciowe zostały skonfigurowane. Każdy węzeł urządzenia ma cztery interfejsy sieciowe: dwa o przepustowości 1 Gb/s i dwa o przepustowości 40 Gb/s. Jeden z interfejsów 1 Gb/s jest interfejsem zarządzania i dlatego nie można go konfigurować. Pozostałe trzy interfejsy sieciowe są przeznaczone do przesyłania danych i mogą być one konfigurowane przez użytkownika.

- Jeżeli w Twoim środowisku włączony jest protokół DHCP, interfejsy sieciowe są automatycznie konfigurowane.
- Jeśli protokół DHCP nie jest włączony, przejdź do obszaru Ustaw interfejsy sieciowe i przypisz w razie potrzeby statyczne adresy IP.

    ![Pulpit nawigacyjny węzła 1 urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-1.png)

## <a name="configure-second-node"></a>Konfigurowanie drugiego węzła

Skonfiguruj drugi węzeł urządzenia, wykonując kroki opisane w sekcji [Konfigurowanie pierwszego węzła](#configure-first-node).

![Pulpit nawigacyjny węzła 2 urządzenia Data Box Heavy](media/data-box-heavy-deploy-set-up/data-box-heavy-dashboard-2.png)

Po zakończeniu konfiguracji urządzenia możesz połączyć się z udziałami na urządzeniu i skopiować dane z komputera na urządzenie.

## <a name="next-steps"></a>Następne kroki

W tym samouczku przedstawiono zagadnienia dotyczące urządzenia Azure Data Box Heavy, takie jak:

> [!div class="checklist"]
> * Podłączanie kabli do urządzenia Data Box Heavy
> * Nawiązywanie połączenia z urządzeniem Data Box Heavy

Przejdź do następnego samouczka, aby dowiedzieć się, jak skopiować dane na urządzenie Data Box Heavy.

> [!div class="nextstepaction"]
> [Kopiowanie danych na urządzenie Azure Data Box](./data-box-heavy-deploy-copy-data.md)

::: zone-end
