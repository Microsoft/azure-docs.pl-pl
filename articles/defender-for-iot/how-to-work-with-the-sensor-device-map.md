---
title: Praca z mapą urządzeń czujników
description: Mapa urządzenia oferuje graficzną reprezentację wykrytych urządzeń sieciowych. Za pomocą mapy można analizować informacje o urządzeniach, wycinki sieci i generować raporty oraz zarządzać nimi.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 1/7/2021
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: fd1721060bdc4b18f324a94f7c367bacde6ed4e8
ms.sourcegitcommit: 8f0803d3336d8c47654e119f1edd747180fe67aa
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/07/2021
ms.locfileid: "97976762"
---
# <a name="investigate-sensor-detections-in-the-device-map"></a>Badanie wykrywania czujników na mapie urządzeń

Mapa urządzenia oferuje graficzną reprezentację wykrytych urządzeń sieciowych. Użyj mapy, aby:

  - Pobieranie i analizowanie informacji o urządzeniu oraz zarządzanie nimi.

  - Analizowanie wycinków sieci, na przykład określonych grup zainteresowań lub Purdue warstw.

  - Generowanie raportów, na przykład eksportowanie szczegółów i podsumowań urządzeń.

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Zrzut ekranu przedstawiający mapę urządzeń.":::

Aby uzyskać dostęp do mapy:

  - Wybierz pozycję **Mapa urządzenia** na głównym ekranie konsoli.

## <a name="map-search-and-layout-tools"></a>Narzędzia do wyszukiwania map i układu

Poniższe narzędzia służą do pracy na mapie.

Rola użytkownika określa, które narzędzia są dostępne w oknie Mapa urządzeń. Aby uzyskać szczegółowe informacje o rolach użytkowników, zobacz temat [Tworzenie użytkowników i zarządzanie nimi](how-to-create-and-manage-users.md) .

| Symbol | Opis |
|---|---|
| :::image type="icon" source="media/how-to-work-with-maps/search-bar-icon-v2.png" border="false":::| Wyszukaj według adresu IP lub adresu MAC dla określonego urządzenia. W polu tekstowym wprowadź adres IP lub adres MAC. Mapa wyświetla urządzenie, które zostało wyszukane, przy użyciu podłączonych urządzeń. |
| Wyróżnianie grup i filtry <br /> :::image type="content" source="media/how-to-work-with-maps/group-highlight-and-filters-v2.png" alt-text="Zrzut ekranu przedstawiający elementy wyróżnione i filtry."::: | Filtrowanie lub wyróżnianie mapy na podstawie domyślnych i niestandardowych grup urządzeń. |
| :::image type="icon" source="media/how-to-work-with-maps/collapse-view-icon.png" border="false"::: | Widok jest zwijany, aby umożliwić widok z fokusem na urządzeniach, i zgrupować urządzenia IT.  |
| :::image type="icon" source="media/how-to-work-with-maps/device-management-icon.png" border="false"::: | Obsługuj bieżące rozmieszczenie urządzenia na mapie. Jeśli na przykład przeciągniesz urządzenia do nowych lokalizacji na mapie, urządzenia pozostaną w tych lokalizacjach podczas zamykania mapy. |
| :::image type="icon" source="media/how-to-work-with-maps/fit-to-screen-icon.png" border="false"::: | Dopasuj do ekranu |
| :::image type="icon" source="media/how-to-work-with-maps/layer-icon.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/layouts-icon-v2.png" border="false"::: | — Wyświetl warstwę Purdue identyfikowaną dla tego urządzenia, w tym automatyczne sterowanie procesami, nadzór i przedsiębiorstwo <br /> — Wyświetlanie połączeń między urządzeniami.|
| :::image type="icon" source="media/how-to-work-with-maps/broadcast-icon.png" border="false"::: | Pokaż lub Ukryj między emisją i multiemisją. |
| :::image type="icon" source="media/how-to-work-with-maps/time-icon.png" border="false"::: | Odfiltruj urządzenia na mapie według czasu ostatniej komunikacji z innymi urządzeniami. |
| :::image type="icon" source="media/how-to-work-with-maps/notifications-icon.png" alt-text="złożone" border="false"::: | Wyświetl powiadomienia o urządzeniu. Na przykład jeśli wykryto nowy adres IP dla urządzenia przy użyciu istniejącego adresu MAC |
| :::image type="icon" source="media/how-to-work-with-maps/export-import.png" alt-text="Eksportowanie" border="false"::: | Eksportuj/importuj informacje o urządzeniu. |
| :::image type="icon" source="media/how-to-work-with-maps/properties-icon.png" alt-text="aœciwoœci" border="false"::: | Wyświetl podstawowe właściwości urządzenia dla wybranych urządzeń. |
| :::image type="icon" source="media/how-to-work-with-maps/zoom-in-icon-v2.png" alt-text="Powiększ" border="false"::: lub :::image type="icon" source="media/how-to-work-with-maps/zoom-out-icon-v2.png" alt-text="Pomniejsz" border="false"::: | Powiększ lub Zmniejsz liczbę urządzeń na mapie. |

## <a name="view-ot-elements-only"></a>Wyświetl tylko elementy

Domyślnie urządzenia IT są automatycznie agregowane według podsieci, aby widok mapy był skoncentrowany na sieciach z nieot i ICS. Prezentacja elementów sieci IT jest zwijana do minimum, co zmniejsza łączną liczbę urządzeń prezentowanych na mapie i zawiera jasne zdjęcie elementów sieci OT i ICS.

Każda podsieć jest prezentowana jako pojedyncza jednostka na mapie urządzeń, w tym Interaktywna funkcja zwijania i rozwijania, aby zapoznać się ze szczegółami podsieci IT i z powrotem.

Poniższy rysunek przedstawia zwinięty podsieć IT z 27 elementów sieci IT.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="zwinięty podsieć IT z 27 elementów sieci IT":::

Aby włączyć funkcję zwijania sieci IT:

- W oknie Ustawienia systemu upewnij się, że funkcja sieci IT jest włączona.

:::image type="content" source="media/how-to-work-with-maps/shrunk-it-subnet-v2.png" alt-text="Okno Ustawienia systemu":::

Aby rozwinąć podsieć IT:

1. Aby rozróżnić między sieciami IT i niezależnymi, na ekranie Ustawienia systemu wybierz pozycję **podsieci**.

   > [!NOTE]
   > Zaleca się, aby nazwy każdej podsieci z istotnymi nazwami użytkownika można było łatwo zidentyfikować w celu rozróżnienia między nimi i niezależnymi sieciami.

   :::image type="content" source="media/how-to-work-with-maps/subnet-list.png" alt-text="Konfiguracja podsieci":::

2. W oknie konfiguracji edytowanie podsieci Wyczyść podsieć ICS dla każdej podsieci, która ma zostać zdefiniowana jako podsieć IT. Podsieci IT są wyświetlane jako zwinięte na mapie urządzeń z powiadomieniami dla urządzeń z ICS, takimi jak kontroler lub PLC, w sieciach IT.

   :::image type="content" source="media/how-to-work-with-maps/edit-config.png" alt-text="Edytuj konfigurację podsieci":::

3. Aby rozwinąć sieć IT na mapie, w oknie urządzenia kliknij ją prawym przyciskiem myszy i wybierz polecenie **Rozwiń sieć**.

   :::image type="content" source="media/how-to-work-with-maps/expand-network.png" alt-text="Rozwiń swój Widok sieci.":::

4. Zostanie wyświetlone okno potwierdzenia z informacją o tym, że nie można ponownie wykonać zmiany układu.

5. Wybierz przycisk **OK**. Elementy podsieci IT są wyświetlane na mapie.

   :::image type="content" source="media/how-to-work-with-maps/fixed-map.png" alt-text="OK":::

Aby zwinąć podsieć IT:

1.  W okienku po lewej stronie wybierz pozycję **urządzenia**.

2. W oknie urządzenia wybierz ikonę Zwiń. Liczba w kolorze czerwonym wskazuje, jak wiele rozwiniętych podsieci IT jest obecnie wyświetlanych na mapie.

   :::image type="content" source="media/how-to-work-with-maps/devices-notifications.png" alt-text="Okno urządzenia":::

3. Wybierz podsieci, które chcesz zwinąć, lub wybierz pozycję **Zwiń wszystko**. Wybrana podsieć zostanie rozwinięta na mapie.

   :::image type="content" source="media/how-to-work-with-maps/close-all-subnets.png" alt-text="Zwiń wszystko":::

Ikona zwijania zostanie zaktualizowana o zaktualizowaną liczbę rozbudowanych podsieci IT.

## <a name="view-or-highlight-device-groups"></a>Wyświetlanie lub wyróżnianie grup urządzeń

Można dostosować wyświetlanie mapy na podstawie grup urządzeń. Na przykład grupy urządzeń skojarzone z określoną siecią VLAN lub podsiecią. Dostępne są wstępnie zdefiniowane grupy i można tworzyć grupy niestandardowe.

Wyświetl grupy według:

  - **Wyróżnianie:** Zaznacz urządzenia należące do określonej grupy w kolorze niebieskim.

  - **Filtrowanie:** Wyświetlaj tylko urządzenia na mapie, które należą do określonej grupy.

:::image type="content" source="media/how-to-work-with-maps/port-standard.png" alt-text="Standardowy widok portu":::

Dostępne są następujące wstępnie zdefiniowane grupy:

| Nazwa grupy | Opis |
|--|--|
| **Znane aplikacje lub porty inne niż standardowe (domyślnie)** | Urządzenia korzystające z portów zarezerwowanych, takich jak TCP. Urządzenia używające niestandardowych portów lub portów, które nie przypisały aliasu. |
| **Protokoły (domyślnie)** | Urządzenia, które obsługują ruch z niedostępnym. |
| **Autoryzacja (domyślnie)** | Urządzenia, które zostały wykryte w sieci podczas procesu uczenia lub zostały oficjalnie dodane do sieci |
| **Filtry spisu urządzeń** | Urządzenia pogrupowane zgodnie z filtrami zapisywanymi w tabeli spisu urządzeń. |
| **Interwały sondowania** | Urządzenia pogrupowane według interwałów sondowania. Interwały sondowania są generowane automatycznie według kanałów cyklicznych lub okresów. Na przykład 15,0 sekund, 3,0 sekund, 1,5 sekund lub interwału. Przejrzenie tych informacji pomaga sprawdzić, czy systemy są zbyt szybko lub wolno sondować. |
| **Użytkow** | Stacje inżynieryjne i kontrolery z zaprogramowanymi |
| **Podsieci** | Urządzenia należące do określonej podsieci. |
| **OKREŚLONE** | Urządzenia skojarzone z konkretnym IDENTYFIKATORem sieci VLAN. |
| **Połączenie między podsieciami** | Urządzenia skojarzone z połączeniem między podsieciami. |
| **Przypięte alerty** | Urządzenia, dla których użytkownik przypięty alert. |
| **Symulowanie symulacji wektora** | Zagrożone urządzenia wykryte w raportach wektorów ataków. Aby wyświetlić te urządzenia na mapie, zaznacz pole wyboru **Wyświetl w mapie urządzenia** podczas generowania wektora ataku. :::image type="content" source="media/how-to-work-with-maps/add-attack-v2.png" alt-text="Dodawanie symulowania wektorów ataków"::: |
| **Ostatnio widziano** | Urządzenia pogrupowane według przedziału czasowego, które były ostatnio widoczne, na przykład: jedna godzina, sześć godzin, jeden dzień, siedem dni. |
| **Nie w Active Directory** | Wszystkie urządzenia inne niż PLC, które nie komunikują się z Active Directory. |

Aby wyróżnić lub filtrować urządzenia:

1. Wybierz pozycję **Mapa urządzenia** w menu po stronie.

2. Wybierz ikonę filtru. :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="Menu":::

3. W okienku grupy wybierz grupę, w której chcesz wyróżnić lub filtrować urządzenia.

4. Wybierz opcję **Wyróżnij** lub **Filtruj**.

## <a name="define-custom-groups"></a>Definiowanie grup niestandardowych

Oprócz wyświetlania wstępnie zdefiniowanych grup można definiować grupy niestandardowe. Grupy są wyświetlane w raportach Mapa urządzeń, spis urządzeń i wyszukiwanie danych.

> [!NOTE]
> Można także tworzyć grupy z spisu urządzeń.

Aby utworzyć grupę:

1. Wybierz pozycję **urządzenia** w menu po stronie. Zostanie wyświetlona mapa urządzenia.

2. Wybierz pozycję :::image type="content" source="media/how-to-work-with-maps/menu-icon.png" alt-text="ustawienie grupy"::: , aby wyświetlić ustawienia grup.

3. Wybierz :::image type="content" source="media/how-to-work-with-maps/create-group-v2.png" alt-text="grupy"::: , aby utworzyć nową grupę niestandardową.

:::image type="content" source="media/how-to-work-with-maps/custom-group-v2.png" alt-text="Utwórz ekran grupy niestandardowej":::

4. Dodaj nazwę grupy, użyj maksymalnie 30 znaków.

5. Wybierz odpowiednie urządzenia w następujący sposób:

   - Dodaj urządzenia z tego menu, wybierając je z listy (wybierz na przycisku strzałki),<br /> Lub: <br /> 
   - Dodaj urządzenia z tego menu, kopiując je z wybranej grupy (wybierz przycisk strzałki)

6. Wybierz pozycję **Dodaj grupę**.

### <a name="add-devices-to-a-custom-group"></a>Dodawanie urządzeń do grupy niestandardowej

Możesz dodać urządzenia do grupy niestandardowej lub utworzyć nową grupę niestandardową i urządzenie.

1. Kliknij prawym przyciskiem myszy urządzenia na mapie.

2. Wybierz pozycję **Dodaj do grupy**.

3. Wprowadź nazwę grupy w polu Grupa i wybierz pozycję +. Zostanie wyświetlona nowa grupa. Jeśli grupa już istnieje, zostanie dodana do istniejącej grupy niestandardowej.

   :::image type="content" source="media/how-to-work-with-maps/groups-section-v2.png" alt-text="Nazwa grupy":::

4. Dodaj urządzenia do grupy przez powtórzenie kroków 1-3.

## <a name="map-zoom-views"></a>Widoki powiększenia mapy

Praca z widokami mapy pomaga przyspieszyć dowodowych podczas analizowania dużych sieci.

Można wyświetlić trzy widoki szczegółów urządzeń:

  - [Widok oka ptaka](#birds-eye-view)

  - [Typ urządzenia i widok połączenia](#device-type-and-connection-view)

  - [Widok szczegółowy](#detailed-view)

### <a name="birds-eye-view"></a>Widok oka ptaka

Ten widok zapewnia szybki przegląd urządzeń przedstawionych w następujący sposób:

  - Czerwone kropki wskazują urządzenia z alertami

  - Oznaczona gwiazdkami kropek wskazuje urządzenia oznaczone jako ważne

  - Czarne kropki oznaczają urządzenia bez alertów

:::image type="content" source="media/how-to-work-with-maps/colored-dots-v2.png" alt-text="Widok oka ptaka":::

### <a name="device-type-and-connection-view"></a>Typ urządzenia i widok połączenia 

Ten widok przedstawia urządzenia reprezentowane jako ikony na mapie w celu wyróżnienia urządzeń z alertami, typami urządzeń i podłączonymi urządzeniami.

  - Urządzenia z alertami są wyświetlane z czerwonym pierścieniem

  - Urządzenia bez alertów są wyświetlane z szarym pierścieniem

  - Urządzenia wyświetlane jako gwiazdki zostały oznaczone jako ważne

Ikona typu urządzenia jest pokazywana z podłączonymi urządzeniami.

:::image type="content" source="media/how-to-work-with-maps/colored-rings.png" alt-text="Widok połączenia":::

### <a name="detailed-view"></a>Widok szczegółowy 

Widok szczegółowy przedstawia urządzenia i etykiety urządzeń oraz wskaźniki z następującymi informacjami:

:::image type="content" source="media/how-to-work-with-maps/device-map-v2.png" alt-text="Widok szczegółowy":::

### <a name="control-the-zoom-view"></a>Kontrolowanie widoku powiększenia

Wyświetlany widok mapy zależy od poziomu powiększenia mapy. Przełączanie między widokami mapy odbywa się przez zmianę poziomów powiększenia.

:::image type="content" source="media/how-to-work-with-maps/zoom-in-out.png" alt-text="Kontrolowanie widoku powiększenia":::

### <a name="enable-simplified-zoom-views"></a>Włącz uproszczone widoki powiększenia

Administratorzy, którzy chcą uzyskiwać dostęp do widoków połączeń ptaków i urządzeń i typów połączenia z dostępem do ptaków oraz z typu danych, powinny włączyć opcję widoku uproszczonego.

Aby włączyć uproszczone widoki mapy:

  - Wybierz pozycję **Ustawienia systemowe** , a następnie Przełącz opcję **uproszczony widok mapy** .

:::image type="content" source="media/how-to-work-with-maps/simplify-view-v2.png" alt-text="Uprość widok mapy":::

## <a name="learn-more-about-devices"></a>Dowiedz się więcej o urządzeniach

Dostępny jest szeroki zakres narzędzi, aby dowiedzieć się więcej na temat urządzeń w formie mapy urządzeń:

- [Etykiety i wskaźniki urządzeń](#device-labels-and-indicators)

- [Szybkie widoki urządzeń](#device-quick-views)

- [Wyświetl właściwości urządzenia i zarządzaj nimi](#view-and-manage-device-properties)

- [Wyświetlanie typów urządzeń](#view-device-types)

- [Środowiska IDE](#backplane-properties)

- [Wyświetlanie osi czasu zdarzeń dla urządzenia](#view-a-timeline-of-events-for-the-device)

- [Analizowanie szczegółów i zmian programistycznych](#analyze-programming-details-and-changes)

### <a name="device-labels-and-indicators"></a>Etykiety i wskaźniki urządzeń

Na urządzeniach na mapie mogą pojawić się następujące etykiety i wskaźniki:

| Etykieta urządzenia | Opis |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/host-v2.png" alt-text="Nazwa hosta IP"::: | Nazwa hosta adresu IP i adres IP lub adresy podsieci |
| :::image type="content" source="media/how-to-work-with-maps/amount-alerts-v2.png" alt-text="Liczba alertów"::: | Liczba alertów skojarzonych z urządzeniem |
| :::image type="icon" source="media/how-to-work-with-maps/type-v2.png" border="false"::: | Ikona typu urządzenia, na przykład Storage, PLC lub Historian. |
| :::image type="content" source="media/how-to-work-with-maps/grouped-v2.png" alt-text="urządzenia pogrupowane"::: | Liczba urządzeń zgrupowanych w podsieci w sieci IT. W tym przykładzie 8. |
| :::image type="content" source="media/how-to-work-with-maps/not-authorized-v2.png" alt-text="okres uczenia urządzenia"::: | Urządzenie wykryte po okresie uczenia i nie zostało autoryzowane jako urządzenie sieciowe. |
| Linia ciągła | Połączenie logiczne między urządzeniami |
| :::image type="content" source="media/how-to-work-with-maps/new-v2.png" alt-text="Nowe urządzenie"::: | Nowe urządzenie odnalezione po zakończeniu uczenia się. |

### <a name="device-quick-views"></a>Szybkie widoki urządzeń

Dostęp do właściwości i połączeń urządzeń z poziomu mapy.

Aby otworzyć menu Szybkie właściwości:

  - Wybierz menu Szybkie właściwości menu :::image type="content" source="media/how-to-work-with-maps/properties.png" alt-text="szybkiego":::okna.

#### <a name="quick-device-properties"></a>Szybkie właściwości urządzenia

Wybierz urządzenie lub wiele urządzeń, gdy ekran Szybkie właściwości jest otwarty, aby zobaczyć najważniejsze informacje o tych urządzeniach:

:::image type="content" source="media/how-to-work-with-maps/device-information.png" alt-text="Szybkie właściwości urządzenia":::

#### <a name="quick-connection-properties"></a>Właściwości szybkiego połączenia

Wybierz połączenie, gdy ekran Szybkie właściwości jest otwarty, aby zobaczyć protokoły, które są używane w tym połączeniu i kiedy były ostatnio widoczne:

:::image type="content" source="media/how-to-work-with-maps/connection-details-v2.png" alt-text="Właściwości szybkiego połączenia":::

## <a name="view-and-manage-device-properties"></a>Wyświetl właściwości urządzenia i zarządzaj nimi

Można wyświetlić właściwości urządzeń dla każdego urządzenia wyświetlanego na mapie. Na przykład nazwa urządzenia, typ lub system operacyjny lub oprogramowanie układowe lub dostawca.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="Wyświetl właściwości urządzenia i zarządzaj nimi":::

Poniższe informacje można aktualizować ręcznie. Informacje wprowadzane ręcznie przesłonią informacje wykryte przez usługę Defender for IoT.

  - Nazwa

  - Typ

  - System operacyjny

  - Warstwa Purdue

  - Opis

| Element | Opis |
|--|--|
| Informacje podstawowe | Podstawowe informacje są zbędne. |
| Nazwa | Nazwa urządzenia. <br /> Domyślnie czujnik odnajduje nazwę urządzenia określoną w sieci. Na przykład nazwa zdefiniowana na serwerze DNS. <br /> Jeśli nie zdefiniowano takich nazw, w tym polu jest wyświetlany adres IP urządzenia. <br /> Nazwę urządzenia można zmienić ręcznie. Nadaj swoim urządzeniom znaczące nazwy, które odzwierciedlają ich funkcjonalność. |
| Typ | Typ urządzenia wykryty przez czujnik. <br /> Aby uzyskać więcej informacji, zobacz [Wyświetlanie typów urządzeń](#view-device-types). |
| Dostawca | Dostawca urządzenia. |
| System operacyjny | System operacyjny urządzenia. |
| Warstwa Purdue | Warstwa Purdue określona przez czujnik dla tego urządzenia, w tym: <br /> -Automatyczne <br /> — Kontrola procesu <br /> -Nadzorcze <br /> - Enterprise |
| Opis | Bezpłatne pole tekstowe. <br /> Dodaj więcej informacji o urządzeniu. |
| Atrybuty | Wszystkie dodatkowe informacje, które zostały odnalezione na urządzeniu w okresie uczenia i nie należą do innych kategorii, są wyświetlane w sekcji atrybuty. <br /> Informacje są typu RO. |
| Ustawienia | Ustawienia urządzenia można zmienić ręcznie, aby zapobiec zwracaniu fałszywych wartości dodatnich: <br /> - **Autoryzowane urządzenie**: w okresie uczenia wszystkie urządzenia wykryte w sieci są identyfikowane jako autoryzowane urządzenia. Gdy urządzenie zostanie odnalezione po okresie szkoleniowym, domyślnie pojawia się jako nieautoryzowane urządzenie. Tę definicję można zmienić ręcznie. <br /> - **Znane jako skaner**: Włącz tę opcję, Jeśli wiesz, że to urządzenie jest znane jako skaner i nie musisz otrzymywać alertów o tym urządzeniu. <br /> - **Urządzenie programistyczne**: Włącz tę opcję, Jeśli wiesz, że to urządzenie jest znane jako urządzenie programistyczne i nie musisz otrzymywać alertów o tym urządzeniu. |
| Grupy niestandardowe | Grupy niestandardowe na mapie urządzeń, w której uczestniczą to urządzenie. |
| Stan | Stan zabezpieczeń i autoryzacji urządzenia: <br /> — Stan to brak `Secured` alertów <br /> — W przypadku alertów dotyczących urządzenia zostanie wyświetlona liczba alertów <br /> — Stan `Unauthorized` jest wyświetlany w przypadku urządzeń, które zostały dodane do sieci po okresie szkoleniowym. Urządzenie można zdefiniować ręcznie jako `Authorized Device` Ustawienia <br /> -W przypadku, gdy adres tego urządzenia jest zdefiniowany jako adres dynamiczny, `DHCP` zostanie dodany do stanu. |


| Sieć | Opis |
|--|--|
| Interfejsy | Interfejsy urządzeń. Pole typu RO. |
| Protokoły | Protokoły używane przez urządzenie. Pole typu RO. |
| Oprogramowanie układowe | Jeśli informacje o planie dostępności są dostępne, informacje oprogramowania układowego nie będą wyświetlane. |
| Adres | Adres IP urządzenia. |
| Kolejną | Numer seryjny urządzenia. |
| Adres modułu | Model urządzenia i numer gniazda lub identyfikator. |
| Model | Numer modelu urządzenia. |
| Wersja oprogramowania układowego | Numer wersji oprogramowania układowego. |

Aby wyświetlić informacje o urządzeniu:

1. Wybierz pozycję **urządzenia** w menu po stronie.

2. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Wyświetl właściwości**. Zostanie wyświetlona okno Właściwości urządzenia.

3. Aby wyświetlić szczegółowe informacje o alertach dla tego urządzenia, wybierz pozycję na wymaganym alercie u dołu tego okna.

### <a name="view-device-types"></a>Wyświetlanie typów urządzeń

Typ urządzenia jest automatycznie identyfikowany przez czujnik podczas procesu odnajdywania urządzenia. Typ można zmienić ręcznie.

:::image type="content" source="media/how-to-work-with-maps/type-of-device.png" alt-text="Wyświetlanie typów urządzeń":::

W poniższej tabeli przedstawiono wszystkie typy w systemie:

| Kategoria | Typ urządzenia |
|--|--|
| SIECIOWEGO | Stacja inżynieryjna <br /> PLC <br />Historian <br />HMI <br />IED <br />Kontroler DCS <br />RTU <br />System pakowania przemysłowego <br />Skala przemysłowa <br />Robot branżowy <br />Gniazdo <br />Miernik <br />Zmienna częstotliwość dysku  <br />Kontroler robotów <br />Dysk SERVO <br />Urządzenie pneumatyczne <br />Wygaszacz |
| IT | Kontroler domeny <br />Serwer bazy danych <br />Stacja robocza <br />Serwer <br />Stacja terminalowa <br />Magazyn <br />Telefon inteligentny <br />Tablet <br />Serwer kopii zapasowej |
| IoT | Kamera IP <br />Drukarka  <br />Zegar dziurkowania <br />Bankomaty <br />Inteligentna telewizja <br />Konsola gry <br />DVR <br />Panel sterowania drzwiami <br />HVAC <br />Sterownika <br />Alarm ognia <br />Inteligentne oświetlenie <br />Przełącznik inteligentny <br />Wykrywanie ognia <br />Telefon IP <br />System alarmów <br />Siren alarmu <br />Wykrywanie ruchu <br />Opuścić <br />Czujnik wilgotności <br />Skaner kodów kreskowych <br />Zasilacz awaryjny UPS <br />System licznika osób <br />Intercom <br />Poprzeczk |
| Sieć | Punkt dostępu bezprzewodowego <br />Router <br />Przełącznik <br />Firewall <br />VPN Gateway <br />Serwer NTP <br />Ananasy WiFi <br />Lokalizacja fizyczna <br />Adapter we/wy <br /> Konwerter protokołów |

Aby wyświetlić informacje o urządzeniu:

1.  Wybierz pozycję **urządzenia** w menu po stronie.

2. Kliknij prawym przyciskiem myszy urządzenie, a następnie wybierz polecenie **Wyświetl właściwości**. Zostanie wyświetlona okno Właściwości urządzenia.

3. Aby wyświetlić szczegółowe informacje o alertach dla tego urządzenia, wybierz pozycję na wymaganym alercie u dołu tego okna.

### <a name="backplane-properties"></a>Właściwości planu

Jeśli PLC zawiera wiele modułów rozdzielonych na stojaki i gniazda, Charakterystyka może się różnić między kartami modułów. Na przykład jeśli adres IP i adres MAC są takie same, oprogramowanie układowe może się różnić.

Możesz użyć opcji nadplanu, aby przejrzeć wiele kontrolerów/kart i ich urządzeń zagnieżdżonych jako jedną jednostkę z różnymi definicjami. Każde gniazdo w widoku planu wieloplanowego reprezentuje urządzenia podstawowe — urządzenia, które zostały w nim wykryte.

:::image type="content" source="media/how-to-work-with-maps/backplane-image-v2.png" alt-text="Właściwości planu":::

:::image type="content" source="media/how-to-work-with-maps/backplane-details-v2.png" alt-text="Właściwości urządzenia ze planu":::

Plan zapasowy może zawierać do 30 kart kontrolera i maksymalnie 30 jednostek stojaków. Łączna liczba urządzeń uwzględnionych na wielu poziomach może należeć do 200 urządzeń.

Gdy zostaną wykryte szczegóły planu, w obszarze okno Właściwości urządzenia zostanie wyświetlona okienko nieplanu.

Każde miejsce jest wyświetlane wraz z liczbą podstawowych urządzeń i ikoną, która wyświetla typ modułu.

| Ikona | Typ modułu |
|--|--|
| :::image type="content" source="media/how-to-work-with-maps/power.png" alt-text="Zasilacz"::: | Zasilacz |
| :::image type="content" source="media/how-to-work-with-maps/analog.png" alt-text="Analogowe we/wy"::: | Analogowe we/wy |
| :::image type="content" source="media/how-to-work-with-maps/comms.png" alt-text="Adapter komunikacji"::: | Adapter komunikacji |
| :::image type="content" source="media/how-to-work-with-maps/digital.png" alt-text="Cyfrowe we/wy"::: | Cyfrowe we/wy |
| :::image type="content" source="media/how-to-work-with-maps/computer-processor.png" alt-text="Procesor CPU"::: | Procesor CPU |
| :::image type="content" source="media/how-to-work-with-maps/HMI-icon.png" alt-text="HMI"::: | HMI |
| :::image type="content" source="media/how-to-work-with-maps/average.png" alt-text="Ogólny"::: | Ogólny |

Po wybraniu gniazda pojawią się szczegóły dotyczące gniazda:

:::image type="content" source="media/how-to-work-with-maps/slot-selection-v2.png" alt-text="Wybierz gniazdo":::

Aby wyświetlić podstawowe urządzenia za miejscem, wybierz pozycję **widok na mapie**. Gniazdo jest prezentowane na mapie urządzenia ze wszystkimi podłączonymi do nich modułami i urządzeniami.

:::image type="content" source="media/how-to-work-with-maps/map-appearance-v2.png" alt-text="WYŚWIETL NA MAPIE":::

## <a name="view-a-timeline-of-events-for-the-device"></a>Wyświetlanie osi czasu zdarzeń dla urządzenia

Wyświetlanie osi czasu zdarzeń skojarzonych z urządzeniem.

Aby wyświetlić oś czasu:

1. Kliknij prawym przyciskiem myszy urządzenie na mapie.

2. Wybierz pozycję **Pokaż zdarzenia**. Zostanie otwarte okno oś czasu zdarzeń z informacjami o zdarzeniach wykrytych dla wybranego urządzenia.

Aby uzyskać szczegółowe informacje, zobacz [oś czasu zdarzeń](#event-timeline) .

## <a name="analyze-programming-details-and-changes"></a>Analizowanie szczegółów i zmian programistycznych

Zwiększ dowodowych, wyświetlając zdarzenia programistyczne wykonywane na urządzeniach sieciowych i analizując zmiany kodu. Te informacje ułatwiają wykrywanie podejrzanych działań programistycznych, na przykład:

  - Błąd ludzki: inżynier służy do programowania niewłaściwego urządzenia.

  - Uszkodzona Automatyzacja programowania: programowanie jest błędnie realizowane z powodu błędu automatyzacji.

  - Systemy zaatakowana: nieautoryzowani użytkownicy zalogowani na urządzeniu programistycznym.

Można wyświetlić program z zaprogramowanym urządzeniem i przewinąć różne zmiany w programowaniu wykonywane przez inne urządzenia.

Wyświetl kod, który został dodany, zmieniony, usunięty lub niezmieniony przez urządzenie programistyczne. Wyszukaj zmiany programistyczne oparte na typach plików, datach lub porach zainteresowania.

### <a name="when-to-review-programming-activity"></a>Kiedy należy przeglądać działania programistyczne 

Może być konieczne przejrzenie działań programistycznych:

  - Po wyświetleniu alertu dotyczącego nieautoryzowanego programowania

  - Po planowanej aktualizacji kontrolerów

  - Gdy proces lub maszyna nie działa prawidłowo (aby zobaczyć, kto przeprowadził ostatnią aktualizację i kiedy)

:::image type="content" source="media/how-to-work-with-maps/differences.png" alt-text="Programowy dziennik zmian":::

Dodatkowe opcje pozwalają:

  - Oznacz zdarzenia zainteresowania gwiazdką.

  - Pobierz plik *. txt z bieżącym kodem.

### <a name="about-authorized-vs-unauthorized-programming-events"></a>Informacje o autoryzowanych zdarzeniach programistycznych vs 

Nieautoryzowane zdarzenia programistyczne są wykonywane przez urządzenia, które nie zostały uzyskane lub ręcznie zdefiniowane jako urządzenia programistyczne. Autoryzowane zdarzenia programistyczne są wykonywane przez urządzenia, które zostały rozpoznane lub ręcznie zdefiniowane jako urządzenia programistyczne.

W oknie analizy programowania są wyświetlane zarówno autoryzowane, jak i nieautoryzowane zdarzenia programistyczne.

### <a name="accessing-programming-details-and-changes"></a>Uzyskiwanie dostępu do szczegółów i zmian programistycznych

Dostęp do okna analizy programowania z:

- [Oś czasu zdarzeń](#event-timeline)

- [Nieautoryzowane alerty programistyczne](#unauthorized-programming-alerts)

### <a name="event-timeline"></a>Oś czasu zdarzeń

Użyj osi czasu zdarzeń, aby wyświetlić oś czasu zdarzeń, w których zostały wykryte zmiany programistyczne.

:::image type="content" source="media/how-to-work-with-maps/timeline.png" alt-text="Widok osi czasu zdarzeń.":::

### <a name="unauthorized-programming-alerts"></a>Nieautoryzowane alerty programistyczne

Alerty są wyzwalane, gdy nieautoryzowane urządzenia programistyczne wykonują działania programistyczne.

:::image type="content" source="media/how-to-work-with-maps/unauthorized.png" alt-text="Nieautoryzowane alerty programistyczne":::

> [!NOTE]
> Możesz również wyświetlić podstawowe informacje dotyczące programowania w okno Właściwości urządzeń i spisie urządzeń. Zobacz [informacje dotyczące programowania urządzeń: dodatkowe lokalizacje](#device-programming-information-additional-locations) , aby uzyskać szczegółowe informacje.

### <a name="working-in-the-programming-timeline-window"></a>Praca w oknie programowanie osi czasu

W tej sekcji opisano sposób wyświetlania plików programistycznych i porównywania wersji. Wyszukiwanie określonych plików wysyłanych na urządzenie z zaprogramowanym programem. Wyszukaj pliki na podstawie:

  - Data

  - Typ pliku

:::image type="content" source="media/how-to-work-with-maps/timeline-view.png" alt-text="okno programowania osi czasu":::

|Typ osi czasu programowania | Opis |
|--|--|
| Urządzenie z zaprogramowanym | Zawiera szczegółowe informacje o urządzeniu, na którym zainstalowano program, w tym nazwę hosta i plik. |
| Ostatnie zdarzenia | Przedstawia 50 ostatnich zdarzeń wykrytych przez czujnik. <br />Aby zaznaczyć zdarzenie, umieść je na nim i kliknij gwiazdkę. :::image type="icon" source="media/how-to-work-with-maps/star.png" border="false"::: <br /> Ostatnie zdarzenia 50 można wyświetlić. |
| Pliki | Wyświetla pliki wykryte dla wybranej daty i rozmiaru pliku na urządzeniu zaprogramowanym. <br /> Domyślnie maksymalna liczba plików dostępnych do wyświetlenia na urządzenie to 300. <br /> Domyślnie maksymalny rozmiar pliku dla każdego pliku wynosi 15 MB. |
| Stan pliku :::image type="icon" source="media/how-to-work-with-maps/status-v2.png" border="false"::: | Etykiety plików wskazują stan pliku na urządzeniu, w tym: <br /> **Dodano**: plik został dodany do punktu końcowego w wybranym dniu lub o wybranej godzinie. <br /> **Zaktualizowano**: plik został zaktualizowany w wybranym dniu lub o wybranej godzinie. <br /> **Usunięto**: ten plik został usunięty. <br /> **Brak etykiety**: plik nie został zmieniony.   |
| Programowanie urządzenia | Urządzenie, które dokonało zmiany programistycznej. Wiele urządzeń mogło przebiegać zmiany programistyczne na jednym z programów z zaprogramowanym programem. Zostanie wyświetlona nazwa hosta, Data lub godzina zmiany i zalogowanego użytkownika. |
| :::image type="icon" source="media/how-to-work-with-maps/current.png" border="false"::: | Wyświetla bieżący plik zainstalowany na zaprogramowanym urządzeniu. |
| :::image type="icon" source="media/how-to-work-with-maps/download-text.png" border="false"::: | Pobierz plik tekstowy z wyświetlanym kodem. |
| :::image type="icon" source="media/how-to-work-with-maps/compare.png" border="false"::: | Porównaj bieżący plik z plikiem wykrytym w wybranej dacie. |

### <a name="choose-a-file-to-review"></a>Wybierz plik do przejrzenia

W tej sekcji opisano, jak wybrać plik do przejrzenia.

Aby wybrać plik do przejrzenia:

1. Wybierz zdarzenie z okienka **ostatnie zdarzenia**

2. Wybierz plik formularza w okienku plik. Plik zostanie wyświetlony w bieżącym okienku.

:::image type="content" source="media/how-to-work-with-maps/choose-file.png" alt-text="Wybierz plik, z którym chcesz współpracować.":::

### <a name="compare-files"></a>Porównaj pliki

W tej sekcji opisano sposób porównywania plików programistycznych.

Aby porównać:

1. Wybierz zdarzenie z okienka ostatnie zdarzenia.

2. Wybierz plik z okienka plik. Plik zostanie wyświetlony w bieżącym okienku. Ten plik można porównać z innymi plikami.

3. Wybierz wskaźnik porównania.

   :::image type="content" source="media/how-to-work-with-maps/compare.png" alt-text="Porównaj wskaźnik":::

   W oknie zostaną wyświetlone wszystkie daty wykryte przez wybrany plik na urządzeniu zaprogramowanym przez program. Plik mógł zostać zaktualizowany na urządzeniu zaprogramowanym przez wiele urządzeń programistycznych.

   Liczba wykrytych różnic jest wyświetlana w prawym górnym rogu okna. Może być konieczne przewinięcie w dół w celu wyświetlenia różnic.

   :::image type="content" source="media/how-to-work-with-maps/scroll.png" alt-text="Przewiń w dół do wybranego obszaru":::

   Liczba jest obliczana na podstawie sąsiadujących wierszy zmienionego tekstu. Na przykład, jeśli osiem kolejnych wierszy kodu zostało zmienionych (usunięto, Zaktualizowano lub dodano), zostanie ono obliczone jako jedna różnica.

   :::image type="content" source="media/how-to-work-with-maps/program-timeline.png" alt-text="Widok osi czasu programu.":::

4. Wybierz datę. Plik wykryty w wybranej dacie pojawi się w oknie.

5. Plik wybrany z okienka ostatnie zdarzenia/pliki zawsze pojawia się po prawej stronie.

### <a name="device-programming-information-additional-locations"></a>Informacje o programowaniu urządzeń: dodatkowe lokalizacje

Oprócz szczegółów na osi czasu programowania można uzyskać dostęp do informacji programistycznych na urządzeniu okno Właściwości i spisie urządzeń.

| Typ urządzenia | Opis |
|--|--|
| Właściwości urządzenia | Okno właściwości urządzenia zawiera informacje o ostatnim zdarzeniu programistycznym wykrytym w device\. :::image type="content" source="media/how-to-work-with-maps/information-from-device-v2.png" alt-text="Właściwości urządzenia"::: |
| Spis urządzeń | Spis urządzeń wskazuje, czy urządzenie jest device\. programistyczne :::image type="content" source="media/how-to-work-with-maps/inventory-v2.png" alt-text="Spis urządzeń"::: |

## <a name="manage-device-information-from-the-map"></a>Zarządzanie informacjami o urządzeniu z mapy

Czujnik nie aktualizuje ani nie wpływa na urządzenia bezpośrednio w sieci. Zmiany wprowadzone w tym miejscu mają wpływ tylko na sposób analizowania urządzenia.

### <a name="delete-devices"></a>Usuwanie urządzeń

Jeśli uzyskane informacje nie są istotne, można usunąć urządzenie. Przykład:

  - Wykonawca partnerski na stacji roboczej inżynieryjnej nawiązuje połączenie z aktualizacjami konfiguracji. Po zakończeniu zadania urządzenie nie powinno już być monitorowane.

  - Ze względu na zmiany w sieci niektóre urządzenia nie są już połączone.

Jeśli urządzenie nie zostanie usunięte, czujnik będzie kontynuował monitorowanie go. Po 60 dniach zostanie wyświetlone powiadomienie z zaleceniem usunięcia.

Może zostać wyświetlony alert informujący o tym, że urządzenie nie odpowiada, jeśli inne urządzenie próbuje uzyskać do niego dostęp. W takim przypadku sieć może być niepoprawnie skonfigurowana.

Urządzenie zostanie usunięte z raportów dotyczących mapowania urządzeń, spisu urządzeń i wyszukiwania danych. Inne informacje, na przykład: informacje przechowywane w elementach widget zostaną zachowane.

Urządzenie musi być aktywne przez co najmniej 10 minut, aby można było je usunąć.

Aby usunąć urządzenie z mapy urządzeń:

1. Wybierz pozycję **urządzenia** w menu po stronie.

2. Kliknij prawym przyciskiem myszy urządzenie i wybierz polecenie **Usuń**.

### <a name="merge-devices"></a>Scalanie urządzeń

W pewnych okolicznościach może być konieczne scalenie urządzeń. Może to być wymagane, jeśli czujnik wykrył oddzielne jednostki sieciowe, które są jednym unikatowym urządzeniem. Przykład:

  - PLC z czterema kartami sieciowymi

  - Laptop z kartą sieci Wi-Fi i fizyczną

Podczas scalania należy polecić czujnikowi łączenie właściwości urządzenia z dwoma urządzeniami w jeden. Po wykonaniu tej czynności raporty dotyczące okno Właściwości i czujników urządzenia zostaną zaktualizowane o nowe szczegóły właściwości urządzenia.

Na przykład w przypadku scalania dwóch urządzeń z adresem IP oba adresy IP będą wyświetlane jako oddzielne interfejsy na urządzeniu okno Właściwości. Można scalać tylko autoryzowanych urządzeń.

:::image type="content" source="media/how-to-work-with-maps/device-properties-v2.png" alt-text="okno Właściwości urządzenia":::

Oś czasu zdarzeń przedstawia zdarzenie scalania.

:::image type="content" source="media/how-to-work-with-maps/events-time.png" alt-text="Oś czasu zdarzeń ze scalonymi zdarzeniami.":::

Nie można cofnąć scalania urządzeń. Jeśli usunięto ponownie dwa urządzenia, Usuń urządzenie i poczekaj na ponowne odnalezienie czujnika.

Aby scalić urządzenia:

1. Wybierz dwa urządzenia i kliknij prawym przyciskiem myszy jeden z nich.

2. Wybierz pozycję **Scal** , aby scalić urządzenia. Scalanie może potrwać do 2 minut.

3. W oknie dialogowym ustaw atrybuty urządzenia Merge wybierz nazwę urządzenia.

   :::image type="content" source="media/how-to-work-with-maps/name-the-device-v2.png" alt-text="okno dialogowe atrybuty":::

4. Wybierz pozycję **Zapisz**.

### <a name="authorize-and-unauthorize-devices"></a>Autoryzuj i Wycofaj autoryzację urządzeń

W okresie uczenia wszystkie urządzenia wykryte w sieci są identyfikowane jako autoryzowane urządzenia. Etykieta **autoryzowana** nie jest wyświetlana na tych urządzeniach na mapie urządzeń.

Gdy urządzenie zostanie odnalezione po okresie szkoleniowym, pojawia się jako nieautoryzowane urządzenie. Oprócz wyświetlania nieautoryzowanych urządzeń na mapie można je również zobaczyć w spisie urządzeń.

:::image type="content" source="media/how-to-work-with-maps/inventory-icon.png" alt-text="Spis urządzeń":::

**Nowe urządzenie a nieautoryzowane**

Nowe urządzenia wykryte po okresie uczenia pojawią się z `New` `Unauthorized` etykietą i.

Jeśli przeniesiesz urządzenie na mapę lub ręcznie zmienisz właściwości urządzenia, `New` etykieta zostanie usunięta ze ikony urządzenia.

#### <a name="unauthorized-devices---attack-vectors-and-risk-assessment-reports"></a>Nieautoryzowane urządzenia — wektory ataków i raporty oceny ryzyka

Nieautoryzowane urządzenia są obliczane w raportach oceny ryzyka i w raportach wektorów ataków.

- **Raporty wektorów ataków:** Urządzenia oznaczone jako nieautoryzowane są rozwiązywane w wektorze ataków jako podejrzane urządzenia, które mogą stanowić zagrożenie dla sieci.

   :::image type="content" source="media/how-to-work-with-maps/attack-vector-reports.png" alt-text="Widok raporty wektorowe ataków":::

- **Raporty oceny ryzyka:** Urządzenia oznaczone jako nieautoryzowane:

  - Zidentyfikowane w raportach oceny ryzyka

Aby ręcznie autoryzować lub wycofać autoryzację urządzeń:

1. Kliknij prawym przyciskiem myszy urządzenie na mapie i wybierz pozycję Anuluj **autoryzację**

### <a name="mark-devices-as-important"></a>Oznaczanie urządzeń jako ważne

Można oznaczyć znaczące urządzenia sieciowe jako ważne, na przykład serwery krytyczne dla działania firmy. Te urządzenia są oznaczone gwiazdką na mapie. Gwiazdka różni się w zależności od poziomu powiększenia mapy.

:::image type="icon" source="media/how-to-work-with-maps/star-one.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-two.png" border="false"::: :::image type="icon" source="media/how-to-work-with-maps/star-3.png" border="false":::

### <a name="important-devices---attack-vectors-and-risk-assessment-reports"></a>Ważne urządzenia — wektory ataków i raporty oceny ryzyka

Ważne urządzenia są obliczane podczas generowania raportów oceny ryzyka i raportów wektorów ataków.

  - W przypadku ataku typu "ważne" raporty wektorów ataków są rozwiązywane w zależności od ataku. 

  - Raporty oceny ryzyka: urządzenia oznaczone jako ważne są obliczane podczas przekazywania wyniku zabezpieczeń w raporcie oceny ryzyka.

## <a name="generate-activity-reports-from-the-map"></a>Generowanie raportów aktywności z mapy

Generuj raport działań dla wybranego urządzenia w zakresie od 1 do 6, 12 lub 24 godzin. Dostępne są następujące informacje:

  - Kategoria: podstawowe informacje o wykrywaniu oparte na scenariuszach ruchu.

  - Urządzenia źródłowe i docelowe

  - Dane: dodatkowe informacje są wadliwe.

  - Data i godzina ostatniego wyświetlenia.

Raport można zapisać jako plik programu Microsoft Excel lub Word.

:::image type="content" source="media/how-to-work-with-maps/historical-information.png" alt-text="Ostatnia aktywność":::

Aby wygenerować raport aktywności dla urządzenia:

1. Kliknij prawym przyciskiem myszy urządzenie na mapie.

2. Wybierz raport działania.

   :::image type="content" source="media/how-to-work-with-maps/activity-report.png" alt-text="Wyświetl raport dotyczący działania.":::

## <a name="generate-attack-vector-reports-from-the-map"></a>Generowanie raportów wektorów ataków z mapy

Symuluj raport wektora ataków, aby dowiedzieć się, czy urządzenie na wybranej mapie jest zagrożonym celem ataku.

Raporty wektorów ataków zapewniają graficzną reprezentację łańcucha luk w zabezpieczeniach, które są możliwe do wykorzystania. Te luki w zabezpieczeniach mogą dać atakującemu dostęp do kluczowych urządzeń sieciowych. Symulator wektora ataku oblicza wektory ataków w czasie rzeczywistym i analizuje wszystkie wektory ataków na konkretną wartość docelową.

Aby wyświetlić urządzenie w raportach wektorów ataków:

1. Kliknij prawym przyciskiem myszy urządzenie na mapie.

2. Wybierz **symulowanie wektorów ataków**. Zostanie otwarte okno dialogowe wektor ataku z urządzeniem, które zostało wybrane jako cel ataku.

   :::image type="content" source="media/how-to-work-with-maps/simulation.png" alt-text="Dodawanie symulacji wektora ataków":::

3. Dodaj pozostałe parametry do okna dialogowego i wybierz pozycję **Dodaj symulację**.

## <a name="export-device-information-from-the-map"></a>Eksportuj informacje o urządzeniu z mapy

Wyeksportuj następujące informacje o urządzeniu z mapy.

  - Szczegóły urządzenia (Microsoft Excel)

  - Podsumowanie urządzenia (Microsoft Excel)

  - Plik programu Word z grupami (Microsoft Word)

Aby wyeksportować:

1. Wybierz ikonę eksportowania z mapy.

1. Wybierz opcję eksportowania.

## <a name="see-also"></a>Zobacz też

[Badanie wykrywania czujników w spisie urządzeń](how-to-investigate-sensor-detections-in-a-device-inventory.md)
