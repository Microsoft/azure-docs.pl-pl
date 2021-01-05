---
title: Generowanie raportów
description: Uzyskaj wgląd w aktywność sieci, ryzyka, ataki i trendy, korzystając z usługi Defender for IoT Reporting Tools.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/17/2020
ms.topic: how-to
ms.service: azure
ms.openlocfilehash: d0c3f531ede0a590a6ba7bb21c6edbd768c08069
ms.sourcegitcommit: 8be279f92d5c07a37adfe766dc40648c673d8aa8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/31/2020
ms.locfileid: "97842566"
---
# <a name="generate-reports"></a>Generowanie raportów

Uzyskaj wgląd w aktywność sieci, ryzyka, ataki i trendy, korzystając z usługi Azure Defender dla narzędzi do raportowania IoT. Dostępne są narzędzia do generowania raportów:

- Na podstawie działania wykrytego przez poszczególne czujniki.
- Na podstawie działania wykrytego przez wszystkie czujniki. 

Te raporty są generowane na podstawie lokalnej konsoli zarządzania.

## <a name="reports-for-sensor-risk-assessment"></a>Raporty dotyczące oceny ryzyka czujnika

Raportowanie oceny ryzyka pozwala generować ocenę zabezpieczeń dla każdego urządzenia sieciowego, a także ogólną ocenę zabezpieczeń sieci. Wynik ogólny przedstawia wartość procentową zabezpieczeń 100%.

Ten wynik jest oparty na wynikach inspekcji pakietów, aparatów modelowania zachowań i projekcie maszyn z określonym stanem SCADA. Dostępny jest szeroki zakres innych informacji, takich jak:

- Problemy z konfiguracją

- Usterka urządzenia z priorytetem według poziomu zabezpieczeń

- Problemy z zabezpieczeniami sieci

- Problemy operacyjne sieci

- Wektory ataków

- Połączenia z sieciami ICS

- Połączenia internetowe

- Wskaźniki złośliwego oprogramowania

- Problemy dotyczące protokołu

  - Zabezpieczanie urządzeń: urządzenia z wynikiem zabezpieczeń powyżej 90%.

- **Zabezpieczanie urządzeń**: urządzenia z wynikiem zabezpieczeń powyżej 90%.

- **Zagrożone urządzenia**: urządzenia z wynikiem zabezpieczeń poniżej 70 procent.

- **Urządzenia wymagające ulepszeń**: urządzenia z oceną zabezpieczeń w zakresie od 70% do 89%.

Aby utworzyć raport:

1. Wybierz pozycję **Ocena ryzyka** w menu po stronie.
2. Wybierz czujnik z listy rozwijanej **Wybieranie czujnika** .
3. Wybierz pozycję **Generuj raport**.
4. Wybierz pozycję **Pobierz** w sekcji zarchiwizowane raporty.

:::image type="content" source="media/how-to-generate-reports/risk-assessment.png" alt-text="Widok oceny ryzyka.":::

Aby zaimportować logo firmy:

Aby zaimportować logo firmy:

- Wybierz pozycję **Importuj logo**.

## <a name="reports-for-sensor-attack-vector"></a>Raporty dotyczące wektora ataków typu czujnik

Raporty wektorów ataków zapewniają graficzną reprezentację łańcucha luk w zabezpieczeniach, które są możliwe do wykorzystania. Te luki w zabezpieczeniach mogą dać atakującemu dostęp do kluczowych urządzeń sieciowych. Symulator wektora ataku oblicza wektory ataków w czasie rzeczywistym i analizuje wszystkie wektory ataków dla określonego celu.

Współdziałanie z wektorem ataków pozwala oszacować efekt działań zaradczych w sekwencji ataków. Następnie można określić, na przykład, Jeśli uaktualnienie systemu zakłóca ścieżkę osoby atakującej przez przerwanie łańcucha ataków lub alternatywną ścieżkę ataku. Te informacje ułatwiają określanie priorytetów działań naprawczych i zaradczych.

:::image type="content" source="media/how-to-generate-reports/control-center.png" alt-text="Wyświetl alerty w centrum sterowania.":::

> [!NOTE]
> Administratorzy i analityki zabezpieczeń mogą wykonać procedury opisane w tej sekcji.

Aby utworzyć symulację wektora ataku:

1. Wybierz pozycję :::image type="content" source="media/how-to-generate-reports/plus.png" alt-text="znak plus":::, aby dodać symulację.

 :::image type="content" source="media/how-to-generate-reports/vector.png" alt-text="Symulacja wektora ataku.":::

2. Wprowadź właściwości symulacji:

   - **Name**: Nazwa symulacji.

   - **Maksymalne wektory**: Maksymalna liczba wektorów w pojedynczej symulacji.

   - **Pokaż na mapie urządzeń**: Pokaż wektor ataku jako filtr na mapie urządzeń.

   - **Wszystkie urządzenia źródłowe**: wektor ataków będzie traktować wszystkie urządzenia jako źródło ataku.

   - **Źródło ataku**: w przypadku ataku na ataki należy wziąć pod uwagę tylko określone urządzenia.

   - **Wszystkie urządzenia docelowe**: wektor ataków uwzględnia wszystkie urządzenia jako cel ataku.

   - **Obiekt docelowy ataku**: atakujący może uwzględnić tylko określone urządzenia jako cel ataku.

   - **Wyklucz urządzenia**: określone urządzenia zostaną wykluczone z symulacji wektora ataku.

   - **Wyklucz podsieci**: określone podsieci zostaną wykluczone z symulacji wektora ataku.

3. Wybierz pozycję **Dodaj symulację**. Symulacja zostanie dodana do listy symulacje.

   :::image type="content" source="media/how-to-generate-reports/new-simulation.png" alt-text="Dodaj nową symulację.":::

4. Wybierz, :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: Jeśli chcesz edytować symulację.

   Wybierz, :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: czy chcesz usunąć symulację.

   Zaznacz, :::image type="icon" source="media/how-to-generate-reports/make-a-favorite-icon.png" border="false"::: Jeśli chcesz oznaczyć symulację jako ulubioną.

5. Zostanie wyświetlona lista wektorów ataków z uwzględnieniem wyniku wektora (z 100), ataku urządzenia źródłowego i ataku na urządzenie docelowe. Wybierz konkretny atak na potrzeby graficznego przedstawiania wektorów ataków.

   :::image type="content" source="media/how-to-generate-reports/sample-attack-vectors.png" alt-text="Wektory ataków.":::

## <a name="sensor-data-mining-queries"></a>Dane czujnika — zapytania wyszukiwania

Narzędzia do wyszukiwania danych pozwalają generować kompleksowe i szczegółowe informacje o urządzeniach sieciowych w różnych warstwach. Na przykład można utworzyć zapytanie na podstawie:

- Okresy czasu

- Połączenia z Internetem

- Porty

- Protokoły

- Wersje oprogramowania układowego

- Polecenia programistyczne

- Nieaktywność urządzenia

Możesz dostosować raport na podstawie filtrów. Można na przykład zbadać określoną podsieć, w której oprogramowanie układowe zostało zaktualizowane.

:::image type="content" source="media/how-to-generate-reports/active-device-list-v2.png" alt-text="Lista aktywnych urządzeń.":::

Dostępne są różne narzędzia do zarządzania zapytaniami. Można na przykład wyeksportować i zapisać.

> [!NOTE]
> Administratorzy i analitycy zabezpieczeń mają dostęp do opcji wyszukiwania danych.

### <a name="dynamic-updates"></a>Aktualizacje dynamiczne

Utworzone zapytania wyszukiwania danych są aktualizowane dynamicznie przy każdym ich otwarciu. Na przykład:

- Jeśli utworzysz Raport dla wersji oprogramowania układowego na urządzeniach z 1 czerwca i otworzysz raport ponownie w dniu 10 czerwca, ten raport zostanie zaktualizowany o informacje, które są dokładne dla 10 czerwca.

- Jeśli utworzysz raport w celu wykrywania nowych urządzeń odnalezionych w ciągu ostatnich 30 dni od 1 czerwca, a raport zostanie otwarty 30 czerwca, wyniki będą wyświetlane w ciągu ostatnich 30 dni.

### <a name="data-mining-use-cases"></a>Przypadki użycia wyszukiwania danych

Za pomocą zapytań można obsługiwać szeroką gamę potrzeb związanych z zabezpieczeniami dla różnych zespołów zabezpieczeń:

- **Odpowiedź na zdarzenia SOC**: generowanie raportu w czasie rzeczywistym, aby pomóc w rozproszeniu natychmiastowego reagowania na zdarzenia. Na przykład Wygeneruj raport zawierający listę urządzeń, które mogą wymagać zastosowania poprawek.

- **Dowodowych**: generowanie raportu opartego na danych historycznych dla raportów dochodzeniowych.

- **Integralność sieci IT**: generowanie raportu, który pomaga ulepszyć ogólne zabezpieczenia sieci. Na przykład Wygeneruj raport zawierający listę urządzeń z słabymi poświadczeniami uwierzytelniania.

- **Widoczność**: Generuj raport obejmujący wszystkie elementy zapytania, aby wyświetlić wszystkie parametry linii bazowej sieci.

### <a name="data-mining-storage"></a>Magazyn wyszukiwania danych

Informacje dotyczące wyszukiwania danych są zapisywane i przechowywane w sposób ciągły, z wyjątkiem sytuacji, gdy urządzenie zostało usunięte. Wyniki wyszukiwania danych można eksportować i przechowywać zewnętrznie na bezpiecznym serwerze. Ponadto czujnik wykonuje Automatyczne codzienne kopie zapasowe, aby zapewnić ciągłość systemu i przechowywanie danych.

### <a name="data-mining-and-reports"></a>Wyszukiwanie i raporty danych 

Regularne raporty, do których można uzyskać dostęp z opcji **raporty** , to wstępnie zdefiniowane raporty wyszukiwania danych. Nie są to zapytania dynamiczne, które są dostępne podczas wyszukiwania danych, ale statyczna reprezentacja wyników zapytania wyszukiwania danych.

Wyniki zapytania wyszukiwania danych nie są dostępne dla użytkowników typu RO. Administratorzy i analitycy zabezpieczeń, którzy chcą, aby użytkownicy typu RO mieli dostęp do informacji generowanych przez zapytania wyszukiwania danych, powinni zapisywać informacje jako raport.

### <a name="predefined-queries"></a>Wstępnie zdefiniowane zapytania

Dostępne są następujące wstępnie zdefiniowane zapytania. Te zapytania są generowane w czasie rzeczywistym.

- **CVEs**: wykryto listę urządzeń z znanymi lukami w zabezpieczeniach w ciągu ostatnich 24 godzin.

- **Wykluczone CVEs**: Lista wszystkich CVEs, które zostały ręcznie wykluczone. Aby uzyskać dokładniejsze wyniki w raportach z dokładnością i nosicielami ataków, można ręcznie dostosować listę CVE, dołączając i wykluczając CVEs.

- **Aktywność internetowa**: urządzenia połączone z Internetem.

- **Nieaktywne urządzenia**: urządzenia, które nie zostały przekazane przez ostatnie siedem dni.

- **Urządzenia aktywne**: aktywne urządzenia sieciowe w ciągu ostatnich 24 godzin.

- **Dostęp zdalny**: urządzenia komunikujące się za poorednictwem protokołów sesji zdalnej.

- **Polecenia programistyczne**: urządzenia, które wysyłają programowanie przemysłowe.

Te raporty są automatycznie dostępne na ekranie **raporty** , w którym użytkownicy typu ro i inni użytkownicy mogą je wyświetlać. Użytkownicy typu RO nie mogą uzyskać dostępu do raportów wyszukiwania danych.

:::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Ekran wyszukiwania danych.":::

### <a name="create-a-data-mining-report"></a>Tworzenie raportu wyszukiwania danych

Aby utworzyć raport wyszukiwania danych:

1. Wybierz pozycję **wyszukiwanie danych** z menu po stronie. Wstępnie zdefiniowane sugerowane raporty są wyświetlane automatycznie.

 :::image type="content" source="media/how-to-generate-reports/data-mining-screeshot-v2.png" alt-text="Wybierz pozycję wyszukiwanie danych z okienka bocznego.":::

2. Wybierz pozycję :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false":::.

3. Wybierz pozycję **Nowy raport** i zdefiniuj raport.

   :::image type="content" source="media/how-to-generate-reports/create-new-report-screen.png" alt-text="Utwórz nowy raport, wypełniając ten ekran.":::

   Dostępne są następujące parametry:

   - Podaj nazwę i opis raportu.

   - W obszarze Kategorie wybierz jedną z opcji:

      - **Kategorie (wszystkie)** , aby wyświetlić wszystkie wyniki raportów o wszystkich urządzeniach w sieci.

      - **Ogólne** , aby wybrać standardowe kategorie.

   - Wybierz określone parametry raportu, które są dla Ciebie ważne.

   - Wybierz kolejność sortowania (**order by**). Sortuj wyniki w oparciu o działanie lub kategorię.

   - Wybierz pozycję **Zapisz na stronach raportu** , aby zapisać wyniki raportu jako raport dostępny na stronie **raportu** . Spowoduje to włączenie dla użytkowników typu RO utworzonego raportu.

   - Wybierz pozycję **filtry (Dodaj)** , aby dodać więcej filtrów. Obsługiwane są żądania symboli wieloznacznych.

   - Określ grupę urządzeń (zdefiniowaną w mapie urządzeń).

   - Określ adres IP.

   - Określ port.

   - Określ adres MAC.

4. Wybierz pozycję **Zapisz**. Wyniki raportu są otwierane na stronie **wyszukiwania danych** .

:::image type="content" source="media/how-to-generate-reports/data-mining-page.png" alt-text="Zgłoś wyniki jako widoczne na stronie wyszukiwania danych.":::

### <a name="manage-data-mining-reports"></a>Zarządzanie raportami wyszukiwania danych

W poniższej tabeli opisano opcje zarządzania wyszukiwaniem danych:

| Obraz ikony | Opis |
|--|--|
| :::image type="icon" source="media/how-to-generate-reports/edit-a-simulation-icon.png" border="false"::: | Edytuj parametry raportu. |
| :::image type="icon" source="media/how-to-generate-reports/export-as-pdf-icon.png" border="false"::: | Eksportuj jako plik PDF. |
| :::image type="icon" source="media/how-to-generate-reports/csv-export-icon.png" border="false"::: |Eksportuj jako plik CSV. |
| :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: | Pokaż dodatkowe informacje, takie jak Data ostatniej modyfikacji. Użyj tej funkcji, aby utworzyć migawkę wyników zapytania. Może to być konieczne w celu dalszej analizy za pomocą liderów zespołów lub analityków SOC, na przykład. |
| :::image type="icon" source="media/how-to-generate-reports/pin-icon.png" border="false"::: | Wyświetl na stronie **raporty** lub Ukryj na stronie **raporty** . :::image type="content" source="media/how-to-generate-reports/hide-reports-page.png" alt-text="Ukryj lub Odsłoń raporty."::: |
| :::image type="icon" source="media/how-to-generate-reports/delete-simulation-icon.png" border="false"::: | Usuń raport. |

#### <a name="create-customized-directories"></a>Tworzenie dostosowanych katalogów 

Można organizować rozbudowane informacje dla zapytań wyszukiwania danych, tworząc katalogi dla kategorii. Można na przykład utworzyć katalogi dla protokołów lub lokalizacji.

Aby utworzyć nowy katalog:

1. Wybierz :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: , aby dodać nowy katalog.

2. Wybierz pozycję **Nowy katalog** , aby wyświetlić nowy formularz katalogu.

3. Nazwij nowy katalog.

4. Przeciągnij wymagane raporty do nowego katalogu. W dowolnym momencie możesz przeciągnąć Raport z powrotem do widoku głównego.

5. Kliknij prawym przyciskiem myszy nowy katalog, aby go otworzyć, edytować lub usunąć.

#### <a name="create-snapshots-of-report-results"></a>Tworzenie migawek wyników raportu

Może być konieczne zapisanie niektórych wyników zapytania w celu dalszej analizy. Na przykład może być konieczne udostępnienie wyników różnym zespołom ds. zabezpieczeń.

Migawki są zapisywane w wynikach raportu i nie generują zapytań dynamicznych.

:::image type="content" source="media/how-to-generate-reports/report-results-report.png" alt-text="Migawki.":::

Aby utworzyć migawkę:

1. Otwórz wymagany raport.

2. Wybierz ikonę informacji :::image type="icon" source="media/how-to-generate-reports/information-icon.png" border="false"::: .

3. Wybierz pozycję **Zrób nowe**.

4. Wprowadź nazwę migawki i wybierz pozycję **Zapisz**.

:::image type="content" source="media/how-to-generate-reports/take-a-snapshot.png" alt-text="Utwórz migawkę.":::

#### <a name="customize-the-cve-list"></a>Dostosowywanie listy CVE

Listę CVE można ręcznie dostosować w następujący sposób:

  - Uwzględnij/Wyklucz CVEs

  - Zmień ocenę CVE

Aby wykonać ręczne zmiany w raporcie CVE:

1.  Z menu po stronie wybierz pozycję **wyszukiwanie danych**.

2. Wybierz :::image type="icon" source="media/how-to-generate-reports/plus-icon.png" border="false"::: w lewym górnym rogu okna **wyszukiwania danych** . Następnie wybierz pozycję **Nowy raport**.

   :::image type="content" source="media/how-to-generate-reports/create-a-new-report-screen.png" alt-text="Utwórz nowy raport.":::

3. W okienku po lewej stronie wybierz jedną z następujących opcji:

   - **Znane luki w zabezpieczeniach**: wybiera obie opcje i prezentuje wyniki w dwóch tabelach, jeden z CVEs i drugi z wykluczoną CVEs.

   - **CVEs**: Wybierz tę opcję, aby przedstawić listę wszystkich CVEs.

   - **Wykluczone CVEs**: Wybierz tę opcję, aby wyświetlić listę wszystkich wykluczonych CVEs.

4. Wypełnij pola **Nazwa** i **Opis** , a następnie wybierz pozycję **Zapisz**. Nowy raport pojawi się w oknie **wyszukiwania danych** .

5. Aby wykluczyć CVEs, Otwórz raport wyszukiwania danych dla CVEs. Zostanie wyświetlona lista wszystkich CVEs.

   :::image type="content" source="media/how-to-generate-reports/cves.png" alt-text="Raport C + E.":::

6. Aby włączyć Wybieranie elementów z listy, wybierz :::image type="icon" source="media/how-to-generate-reports/enable-selecting-icon.png" border="false"::: i wybierz CVEs, które chcesz dostosować. Pasek **operacji** pojawia się u dołu.

   :::image type="content" source="media/how-to-generate-reports/operations-bar-appears.png" alt-text="Zrzut ekranu przedstawiający pasek operacji wyszukiwania danych.":::

7. Wybierz CVEs, które chcesz wykluczyć, a następnie wybierz pozycję **Usuń rekordy**. Wybrany CVEs nie pojawia się na liście CVEs i pojawi się na liście wykluczonych CVEs podczas generowania.

8. Aby dołączyć wykluczony CVEs na liście CVEs, wygeneruj Raport dla wykluczonych CVEs i Usuń z listy elementów, które chcesz dołączyć z powrotem na liście CVEs.

9. Wybierz CVEs, w którym chcesz zmienić ocenę, a następnie wybierz pozycję **Aktualizuj ocenę CVE**.

   :::image type="content" source="media/how-to-generate-reports/set-new-score-screen.png" alt-text="Zaktualizuj ocenę CVE.":::

10. Wprowadź nowy wynik i wybierz **przycisk OK**. Zaktualizowany wynik zostanie wyświetlony w wybranym CVEs.

### <a name="reports-based-on-data-mining"></a>Raporty na podstawie wyszukiwania danych

Raporty odzwierciedlają informacje generowane przez wyniki zapytania wyszukiwania danych. Obejmuje to domyślne raporty wyszukiwania danych, które są dostępne w widoku raporty. Analitycy administratorów i zabezpieczeń mogą również generować niestandardowe zapytania wyszukiwania danych i zapisywać je jako raporty. Te raporty są również dostępne dla użytkowników typu RO.

Aby wygenerować raport:

1. Wybierz pozycję **raporty** w menu po stronie.

2. Wybierz wymagany raport do wyświetlenia. Wybór może być **niestandardowym** lub **automatycznie generowanymi** raportami, takimi jak **polecenia programowania** i **dostęp zdalny**.

3. Raport można wyeksportować, wybierając jedną z ikon w prawym górnym rogu ekranu:

   :::image type="icon" source="media/how-to-generate-reports/export-to-pdf-icon.png" border="false"::: Eksportuj do pliku PDF.

   :::image type="icon" source="media/how-to-generate-reports/export-to-csv-icon.png" border="false"::: Eksportuj do pliku CSV.

> [!NOTE] 
> Użytkownik typu RO może zobaczyć tylko raporty utworzone dla nich.

:::image type="content" source="media/how-to-generate-reports/select-a-report-screen.png" alt-text="Wybierz raport do wygenerowania.":::

:::image type="content" source="media/how-to-generate-reports/remote-access-report.png" alt-text="Wygenerowano raport dostępu zdalnego.":::

## <a name="reports-for-sensor-trends-and-statistics"></a>Raporty dotyczące trendów czujnika i statystyk

Można tworzyć wykresy widget i wykresy kołowe, aby uzyskać wgląd w trendy sieci i statystyki. Widżety można grupować w obszarze pulpity nawigacyjne zdefiniowane przez użytkownika.

> [!NOTE]
> Procedury przedstawione w tej sekcji mogą wykonywać tylko Administratorzy i analitycy zabezpieczeń.

Aby wyświetlić pulpity nawigacyjne i widżety, wybierz pozycję **trendy & dane statystyczne** w menu po stronie.

:::image type="content" source="media/how-to-generate-reports/investigation-screenshot.png" alt-text="Zrzut ekranu przedstawiający badanie.":::

Pulpit nawigacyjny składa się z widżetów, które opisują graficznie następujące rodzaje informacji:

- Ruch według portu
- Przepustowość kanału
- Całkowita przepustowość
- Aktywne połączenie TCP
- Devices (Urządzenia):
  - Nowe urządzenia
  - Zajęte urządzenia
  - Urządzenia według dostawcy
  - Urządzenia według systemu operacyjnego
  - Odłączone urządzenia
- Usuwanie łączności według godzin
- Alerty dla incydentów według typu
- Dostęp do tabeli bazy danych
- Elementy widget sekcji protokołu
- Sieć Ethernet i adres IP:
  - Ruch sieciowy sieci Ethernet i IP według usługi PRZELEWu
  - Sieć Ethernet i ruch adresów IP według klasy przelew
  - Ruch sieciowy i adres IP według polecenia
- OPC
  - OPC najważniejsze operacje zarządzania
  - OPC operacji we/wy
- S7 firmy Siemens:
  - Ruch S7 przez funkcję sterowania
  - Ruch S7 przez funkcję podfunkcji
- SRTP:
  - Ruch SRTP według kodu usługi
  - SRTP błędów według dnia
- SuiteLink:
  - SuiteLink najważniejsze Tagi zapytania
  - Zachowanie tagów numerycznych SuiteLink
- Ruch IEC-60870 przez ASDU
- Ruch DNP3 przez funkcję
- Ruch MMS według usługi
- Ruch Modbus przez funkcję
- Ruch OPC-UA według usługi

> [!NOTE]
>  Czas w widżetach jest ustawiany na podstawie czasu czujnika.

## <a name="reports-for-the-on-premises-management-console"></a>Raporty dotyczące lokalnej konsoli zarządzania

Lokalna Konsola zarządzania umożliwia generowanie raportów dla każdej podłączonej do niej czujnika. Raporty opierają się na zapytaniach wyszukiwania danych czujników, które są wykonywane.

Można generować następujące raporty:

- **Aktywne urządzenia (ostatnie 24 godziny)**: przedstawia listę urządzeń, które pokazują aktywność sieci w okresie 24 godzin.

- **Urządzenia nieaktywne (ostatnie 7 dni)**: przedstawia listę urządzeń, które nie pokazują aktywności sieciowej w ciągu ostatnich siedmiu dni.

- **Polecenia programistyczne**: przedstawia listę urządzeń, które wysłały polecenia programistyczne w ciągu ostatnich 24 godzin.

- **Dostęp zdalny**: przedstawia listę urządzeń, do których można uzyskać dostęp zdalny w ciągu ostatnich 24 godzin.

:::image type="content" source="media/how-to-generate-reports/reports-view.png" alt-text="Zrzut ekranu przedstawiający widok raporty.":::

Po wybraniu czujnika z lokalnej konsoli zarządzania wszystkie raporty niestandardowe skonfigurowane na tym czujniku są wyświetlane na liście raportów. Dla każdego czujnika można wygenerować domyślny raport lub niestandardowy raport skonfigurowany dla tego czujnika.

Aby wygenerować raport:

1. W okienku po lewej stronie wybierz pozycję **raporty**. Zostanie wyświetlone okno **raporty** .

2. Z listy rozwijanej **czujniki** wybierz czujnik, dla którego chcesz wygenerować raport.

   :::image type="content" source="media/how-to-generate-reports/sensor-drop-down-list.png" alt-text="Zrzut ekranu przedstawiający widok czujników.":::

3. Z listy rozwijanej po prawej stronie wybierz raport, który chcesz wygenerować.

4. Aby utworzyć plik PDF z wynikami raportu, wybierz opcję :::image type="icon" source="media/how-to-generate-reports/pdf-report-icon.png" border="false"::: .

## <a name="risk-assessment-reports-for-the-on-premises-management-console"></a>Raporty oceny ryzyka dotyczące lokalnej konsoli zarządzania

Wygeneruj raport oceny ryzyka dla każdego czujnika połączonego z lokalną konsolą zarządzania. Ten raport zapewnia wgląd w wszystkie sieci, które są monitorowane przez czujniki.

Raporty oceny ryzyka pozwalają generować ocenę zabezpieczeń dla każdego urządzenia sieciowego, a także ogólną ocenę zabezpieczeń sieci. Ogólny wynik polega na przeniesieniu głębokiej inspekcji pakietów, aparatom modelowania zachowań i projektowaniu maszyn z określonym stanem SCADA. Dostępny jest szeroki zakres innych informacji. Na przykład:

- Problemy z konfiguracją

- Usterka urządzenia z priorytetem według poziomu zabezpieczeń

- Problemy z zabezpieczeniami sieci

- Problemy operacyjne sieci

- Wektory ataków

- Połączenia z sieciami ICS

- Połączenia internetowe

- Wskaźniki złośliwego oprogramowania

- Problemy dotyczące protokołu

Raport zawiera zalecenia zaradcze, które pomogą ulepszyć ocenę zabezpieczeń.

- Zabezpieczanie urządzeń: urządzenia z wynikiem zabezpieczeń powyżej 90%.

- **Zagrożone urządzenia**: urządzenia z wynikiem zabezpieczeń poniżej 70 procent.

- **Urządzenia wymagające ulepszeń**: urządzenia z oceną zabezpieczeń w zakresie od 70% do 89%.

Aby utworzyć raport:

1. Wybierz pozycję **Ocena ryzyka** w menu po stronie.

2. Wybierz czujnik z listy rozwijanej **Wybieranie czujnika** .

3. Wybierz pozycję **Generuj raport**.

4. Wybierz pozycję **Pobierz** w sekcji **zarchiwizowane raporty** .

Aby zaimportować logo firmy:

- Wybierz pozycję **Importuj logo**.

:::image type="content" source="media/how-to-generate-reports/import-logo-screenshot.png" alt-text="Zaimportuj logo za pomocą widoku oceny ryzyka.":::

## <a name="see-also"></a>Zobacz także
[Pracuj z widokami mapy witryny](how-to-gain-insight-into-global-regional-and-local-threats.md#work-with-site-map-views)
