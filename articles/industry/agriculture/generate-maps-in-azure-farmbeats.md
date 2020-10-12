---
title: Generowanie map
description: W tym artykule opisano sposób generowania map w usłudze Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 11/04/2019
ms.author: v-umha
ms.openlocfilehash: 92228c691c323bc0b9621dfc7413d86c5c2669e7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84709066"
---
# <a name="generate-maps"></a>Generowanie map

Za pomocą usługi Azure FarmBeats można generować następujące mapy przy użyciu obrazów satelitarnych i danych wejściowych czujników. Mapy ułatwiają przeglądanie lokalizacji geograficznej farmy i identyfikowanie odpowiedniej lokalizacji urządzeń.

  - **Mapa położenia czujnika**: zawiera zalecenia dotyczące liczby czujników do użycia i miejsca umieszczenia ich w farmie.
  - **Mapa indeksów satelitarnych**: pokazuje indeks roślinności i indeks wody dla farmy.
  - **Wilgotność gleby mapę cieplną**: pokazuje rozkład wilgoci przez odmowę danych satelitarnych i czujników.

## <a name="sensor-placement-map"></a>Mapa położenia czujnika

Mapa położenia czujnika FarmBeatsa pomaga w rozmieszczeniu czujników wilgoci. Dane wyjściowe mapy składają się z listy współrzędnych wdrożenia czujnika. Dane wejściowe z tych czujników są używane razem z obrazami satelitarnymi w celu wygenerowania wilgotności gleby mapę cieplną.

Ta mapa jest tworzona przez segmentację korony w ciągu kilku dat w roku. Nawet bCzy nie jest częścią korony. Można usunąć czujniki, które nie są wymagane w lokalizacji. Ta mapa jest ze wskazówkami i można zmienić pozycję i liczbę nieco w zależności od niestandardowej wiedzy. Dodanie czujników nie spowoduje odmapę cieplną wyników z wilgoci, ale istnieje możliwość pogorszenia w mapę cieplną, jeśli numer czujnika zostanie zmniejszony.

## <a name="before-you-begin"></a>Przed rozpoczęciem

Przed podjęciem próby wygenerowania mapy umieszczenia czujnika należy spełnić następujące wymagania wstępne:

- Rozmiar farmy musi być większy niż jeden Acre.
- Liczba scen ze wskaźnikami wolnych od chmury musi być większa niż sześć dla wybranego zakresu dat.
- Co najmniej sześć scen wskaźnikowych bez chmury musi mieć znormalizowany indeks odporności (NDVI) o wartości większej lub równej 0,3.

    > [!NOTE]
    > Wskaźnik kontrolny umożliwia tylko dwa współbieżne wątki na użytkownika. W związku z tym zadania są umieszczane w kolejce i może trwać dłużej.

### <a name="dependencies-on-sentinel"></a>Zależności dotyczące wskaźnikowego

Następujące zależności odnoszą się do kontrolki wskaźnikowej:

- W przypadku pobierania obrazów satelitarnych zależą od wydajności wskaźnikowej. Sprawdź stan wydajności wskaźnikowej i [działania](https://scihub.copernicus.eu/twiki/do/view/SciHubNews/WebHome)konserwacyjne.
- Wskaźnik kontrolny umożliwia tylko dwa współbieżnie [pobierane wątki](https://sentinels.copernicus.eu/web/sentinel/sentinel-data-access/typologies-and-services) na użytkownika.
- Na Generowanie mapy dokładności ma wpływ [pokrycie wskaźnikiem, a częstotliwość ponownego odwiedzania]( https://sentinel.esa.int/web/sentinel/user-guides/sentinel-2-msi/revisit-coverage).

## <a name="create-a-sensor-placement-map"></a>Tworzenie mapy umieszczania czujnika
Ta sekcja zawiera szczegółowe informacje na temat procedur tworzenia map umieszczania czujnika.

> [!NOTE]
> Mapę umieszczania czujnika można zainicjować ze strony **Maps** lub z menu rozwijanego **Generuj mapy dokładności** na stronie **szczegółów farmy** .

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.
2. Wybierz pozycję **Utwórz mapy**i wybierz pozycję **położenie czujnika** z menu rozwijanego.

    ![Wybieranie położenia czujnika](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-1.png)

3. Po wybraniu **położenia czujnika**zostanie wyświetlone okno **rozmieszczenie czujnika** .

    ![Okno położenia czujnika](./media/get-sensor-data-from-sensor-partner/sensor-placement-1.png)

4. Wybierz farmę z menu rozwijanego **farmy** .
   Aby wyszukać i wybrać farmę, można albo przewinąć listę rozwijaną, albo wprowadzić nazwę farmy w polu tekstowym.
5. Aby wygenerować mapę dla ostatniego roku, wybierz pozycję **zalecane**.
6. Aby wygenerować mapę dla niestandardowego zakresu dat, wybierz opcję **Wybierz zakres dat**. Wprowadź datę początkową i końcową, dla której chcesz wygenerować mapę umieszczania czujnika.
7. Wybierz pozycję **Generuj mapy**.
 Zostanie wyświetlony komunikat z potwierdzeniem zawierającym szczegóły zadania.

  Informacje o stanie zadania znajdują się w sekcji **Wyświetlanie zadań**. Jeśli stan zadania *nie powiodło się*, w etykietce narzędzia stanu *Niepowodzenie* pojawi się szczegółowy komunikat o błędzie. W takim przypadku powtórz poprzednie kroki i spróbuj ponownie.

  Jeśli problem będzie się powtarzać, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-azure-farmbeats.md) lub skontaktuj się z [Forum usługi Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) dotyczącą odpowiednich dzienników.

### <a name="view-and-download-a-sensor-placement-map"></a>Wyświetlanie i pobieranie mapy umieszczenia czujnika

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.

    ![Wybierz pozycję mapy z menu nawigacji po lewej stronie](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz opcję **Filtr** w lewym okienku nawigacji okna.
  W oknie **filtru** są wyświetlane kryteria wyszukiwania.

    ![Okno filtru](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Z menu rozwijanego wybierz pozycję wartości **Typ**, **Data**i **Nazwa** . Następnie wybierz pozycję **Zastosuj** , aby wyszukać mapę, którą chcesz wyświetlić.
  Data utworzenia zadania jest wyświetlana w formacie type_farmname_YYYY-MM-DD.
4. Przewiń listę dostępnych map przy użyciu pasków nawigacyjnych znajdujących się na końcu strony.
5. Wybierz mapę, którą chcesz wyświetlić. W oknie podręcznym zostanie wyświetlona wersja zapoznawcza wybranej mapy.
6. Wybierz pozycję **Pobierz**, a następnie Pobierz plik GEOJSON współrzędnych czujnika.

    ![Podgląd mapy położenia czujnika](./media/get-sensor-data-from-sensor-partner/download-sensor-placement-map-1.png)

## <a name="satellite-indices-map"></a>Mapa indeksów satelitarnych

W poniższych sekcjach opisano procedury dotyczące tworzenia i wyświetlania mapy indeksów satelitarnych.

> [!NOTE]
> Można zainicjować mapę indeksów satelitarnych ze strony **Maps** lub z menu rozwijanego **Generuj mapy dokładności** na stronie **szczegółów farmy** .

FarmBeats umożliwia generowanie NDVI, ulepszonego indeksu wegetacyjnego (EVI) i znormalizowanych różnic w postaci indeksu wodnego (NDWI) dla Farm. Te indeksy pomagają określić, w jaki sposób uprawa jest obecnie zwiększana, lub w przeszłości, a reprezentatywne poziomy wody w ziemi.


> [!NOTE]
> Obraz kontrolny jest wymagany w dniach, dla których jest generowana mapa.


## <a name="create-a-satellite-indices-map"></a>Tworzenie mapy indeksów satelitarnych

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.
2. Wybierz pozycję **Utwórz mapy**i z menu rozwijanego wybierz opcję **indeksy satelitarne** .

    ![Wybieranie indeksów satelitarnych z menu rozwijanego](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-satellite-indices-1.png)

3. Po wybraniu **indeksów satelitarnych**zostanie wyświetlone okno dialogowe **indeksy satelitarne** .

    ![Okno indeksów satelitarnych](./media/get-sensor-data-from-sensor-partner/satellitte-indices-1.png)

4. Wybierz farmę z menu rozwijanego.
   Aby wyszukać i wybrać farmę, możesz wykonać przewijanie na liście rozwijanej lub wprowadzić nazwę farmy.   
5. Aby wygenerować mapę dla ostatniego tygodnia, wybierz **ten tydzień**.
6. Aby wygenerować mapę dla niestandardowego zakresu dat, wybierz opcję **Wybierz zakres dat**. Wprowadź datę początkową i końcową, dla której chcesz wygenerować mapę indeksów satelitarnych.
7. Wybierz pozycję **Generuj mapy**.
    Zostanie wyświetlony komunikat z potwierdzeniem zawierającym szczegóły zadania.

    ![Komunikat potwierdzający mapowanie indeksów satelitarnych](./media/get-sensor-data-from-sensor-partner/successful-satellitte-indices-1.png)

    Informacje o stanie zadania znajdują się w sekcji **Wyświetlanie zadań**. Jeśli stan zadania *nie powiodło się*, w etykietce narzędzia stanu *Niepowodzenie* pojawi się szczegółowy komunikat o błędzie. W takim przypadku powtórz poprzednie kroki i spróbuj ponownie.

    Jeśli problem będzie się powtarzać, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-azure-farmbeats.md) lub skontaktuj się z [Forum usługi Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) dotyczącą odpowiednich dzienników.

### <a name="view-and-download-a-map"></a>Wyświetlanie i pobieranie mapy

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.

    ![Wybieranie map](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz opcję **Filtr** w lewym okienku nawigacji okna. W oknie **filtru** są wyświetlane kryteria wyszukiwania.

    ![Okno filtru wyświetla kryteria wyszukiwania](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3. Z menu rozwijanego wybierz pozycję wartości **Typ**, **Data**i **Nazwa** . Następnie wybierz pozycję **Zastosuj** , aby wyszukać mapę, którą chcesz wyświetlić.
  Data utworzenia zadania jest wyświetlana w formacie type_farmname_YYYY-MM-DD.

4. Przewiń listę dostępnych map przy użyciu pasków nawigacyjnych znajdujących się na końcu strony.
5. Dla każdej kombinacji nazwy i **daty** **farmy** dostępne są następujące trzy mapy:
    - NDVI
    - EVI
    - NDWI
6. Wybierz mapę, którą chcesz wyświetlić. W oknie podręcznym zostanie wyświetlona wersja zapoznawcza wybranej mapy.
7. Wybierz pozycję **Pobierz** z menu rozwijanego, aby wybrać format pobierania. Mapa zostanie pobrana i zapisana w folderze lokalnym na komputerze.

    ![Wybrane podglądy mapy dla wybranych indeksów satelitarnych](./media/get-sensor-data-from-sensor-partner/download-satellite-indices-map-1.png)

## <a name="soil-moisture-heatmap"></a>Mapę cieplną wilgoci

Wilgotność gleby jest wodą, która znajduje się w odstępach między cząstkami glebowymi.Wilgotność gleby mapę cieplną pomaga zrozumieć dane wilgoci w glebie na dowolnej głębokości, w wysokiej rozdzielczości w farmie. Aby wygenerować dokładną i przydatną wilgotność gleby mapę cieplną, wymagana jest jednolite wdrożenie czujników. Wszystkie czujniki muszą pochodzić z tego samego dostawcy. Różni dostawcy mają różnice w sposobie mierzenia wilgoci gleby wraz z różnicami w kalibracji. Mapę cieplną jest generowany z określoną głębokością przy użyciu czujników wdrożonych na tej głębokości.

### <a name="before-you-begin"></a>Przed rozpoczęciem

Przed podjęciem próby wygenerowania wilgoci mapę cieplną należy spełnić następujące wymagania wstępne:

- Należy wdrożyć co najmniej trzy czujniki wilgoci w glebie. Nie należy próbować utworzyć wilgotności gleby mapę cieplną przed wdrożeniem czujników i skojarzeniem ich z farmą.
- Wygenerowanie wilgoci gleby mapę cieplną ma wpływ na pokrycie ścieżki, pokrycie chmury i cień w chmurze. Co najmniej jedna scena wskaźnikowa bezpłatna w chmurze musi być dostępna dla ostatnich 120 dni, od dnia, dla którego zażądano mapę cieplną wody.
- Co najmniej połowa czujników wdrożonych w farmie musi być w trybie online i umożliwia przesyłanie strumieniowe danych do datahub.
- Mapę cieplną musi być wygenerowany przy użyciu miar czujników z tego samego dostawcy.


## <a name="create-a-soil-moisture-heatmap"></a>Tworzenie wilgotności gleby mapę cieplną

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie, aby wyświetlić stronę **mapy** .
2. Wybierz pozycję **Utwórz mapy**, a następnie wybierz pozycję **wilgotność gleby** z menu rozwijanego.

    ![Wybierz wilgotność gleby z menu rozwijanego](./media/get-sensor-data-from-sensor-partner/create-maps-drop-down-soil-moisture-1.png)

3. Po wybraniu **wilgoci gleby**zostanie wyświetlone okno **wilgotność gleby** .

    ![Okno wilgotności gleby](./media/get-sensor-data-from-sensor-partner/soil-moisture-1.png)

4. Wybierz farmę z menu rozwijanego **farmy** .
   Aby wyszukać i wybrać farmę, możesz wykonać przewijanie z listy rozwijanej lub wprowadzić nazwę farmy w menu rozwijanym **Wybieranie farmy** .
5. W menu rozwijanym **Wybierz pomiar czujnika gleby** , Wybierz miarę czujnika wilgoci gleby (głębokość), dla którego chcesz wygenerować mapę.
Aby znaleźć miarę czujnika, przejdź do **czujników**i wybierz dowolny czujnik wilgoci gleby. Następnie w sekcji **Właściwości czujnika** Użyj wartości w polu **nazwa miary**.
6. Aby wygenerować mapę dla **dzisiaj** lub w **tym tygodniu**, wybierz jedną z opcji.
7. Aby wygenerować mapę dla niestandardowego zakresu dat, wybierz opcję **Wybierz zakres dat**. Wprowadź datę początkową i końcową, dla której chcesz wygenerować wilgotność gleby mapę cieplną.
8. Wybierz pozycję **Generuj mapy**.
 Zostanie wyświetlony komunikat z potwierdzeniem zawierającym szczegóły zadania.

   ![Komunikat z potwierdzeniem mapy wilgoci gleby](./media/get-sensor-data-from-sensor-partner/successful-soil-moisture-1.png)

    Informacje o stanie zadania znajdują się w sekcji **Wyświetlanie zadań**. Jeśli stan zadania *nie powiodło się*, w etykietce narzędzia stanu *Niepowodzenie* pojawi się szczegółowy komunikat o błędzie. W takim przypadku powtórz poprzednie kroki i spróbuj ponownie.

    Jeśli problem będzie się powtarzać, zobacz sekcję [Rozwiązywanie problemów](troubleshoot-azure-farmbeats.md) lub skontaktuj się z [Forum usługi Azure FarmBeats, aby uzyskać pomoc techniczną](https://aka.ms/FarmBeatsMSDN) dotyczącą odpowiednich dzienników.

### <a name="view-and-download-a-map"></a>Wyświetlanie i pobieranie mapy

Wykonaj następujące kroki.

1. Na stronie głównej przejdź do pozycji **mapy** w menu nawigacji po lewej stronie.

    ![Przejdź do pozycji Maps](./media/get-sensor-data-from-sensor-partner/view-download-sensor-placement-map-1.png)

2. Wybierz opcję **Filtr** w lewym okienku nawigacji okna. Zostanie wyświetlone okno **filtru** z lokalizacji, w której można wyszukać mapy.

    ![Wybierz pozycję Filtruj po lewej stronie nawigacyjnej](./media/get-sensor-data-from-sensor-partner/view-download-filter-1.png)

3.  Z menu rozwijanego wybierz pozycję wartości **Typ**, **Data**i **Nazwa** . Następnie wybierz pozycję **Zastosuj** , aby wyszukać mapę, którą chcesz wyświetlić. Data utworzenia zadania jest wyświetlana w formacie type_farmname_YYYY-MM-DD.
4. Wybierz ikonę **sortowania** obok nagłówka tabeli, aby posortować dane zgodnie z **farmą**, **datą, Data** **utworzenia**, **identyfikatorem zadania**i **typem zadania**.
5. Przewiń listę dostępnych map, korzystając z przycisków nawigacyjnych znajdujących się na końcu strony.
6. Wybierz mapę, którą chcesz wyświetlić. W oknie podręcznym zostanie wyświetlona wersja zapoznawcza wybranej mapy.
7. Wybierz pozycję **Pobierz** z menu rozwijanego, aby wybrać format pobierania. Mapa zostanie pobrana i zapisana w określonym folderze.

    ![Mapę cieplną — Podgląd wilgoci](./media/get-sensor-data-from-sensor-partner/download-soil-moisture-map-1.png)
