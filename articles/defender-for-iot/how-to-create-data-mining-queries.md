---
title: Tworzenie raportów wyszukiwania danych
description: Generuj kompletne i szczegółowe informacje o urządzeniach sieciowych w różnych warstwach, takich jak protokoły, wersje oprogramowania układowego lub polecenia programistyczne.
ms.date: 01/20/2021
ms.topic: how-to
ms.openlocfilehash: 99754959e7a3a08b4d763b85b0b9315476969774
ms.sourcegitcommit: f611b3f57027a21f7b229edf8a5b4f4c75f76331
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/22/2021
ms.locfileid: "104779073"
---
# <a name="sensor-data-mining-queries"></a>Zapytania wyszukiwania danych czujników

## <a name="about-sensor-data-mining-queries"></a>Zapytania wyszukiwania danych czujników — informacje

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

Tworzone zapytania wyszukiwania danych są aktualizowane dynamicznie przy każdym ich otwarciu. Na przykład:

- Jeśli utworzysz Raport dla wersji oprogramowania układowego na urządzeniach z 1 czerwca i otworzysz raport ponownie w dniu 10 czerwca, ten raport zostanie zaktualizowany o informacje, które są dokładne dla 10 czerwca.

- Jeśli utworzysz raport w celu wykrywania nowych urządzeń odnalezionych w ciągu ostatnich 30 dni od 1 czerwca, a raport zostanie otwarty 30 czerwca, wyniki będą wyświetlane w ciągu ostatnich 30 dni.

### <a name="data-mining-use-cases"></a>Przypadki użycia wyszukiwania danych

Za pomocą zapytań można obsługiwać szeroką gamę potrzeb związanych z zabezpieczeniami dla różnych zespołów zabezpieczeń:

- **Odpowiedź na zdarzenia SOC**: generowanie raportu w czasie rzeczywistym, aby pomóc w rozproszeniu natychmiastowego reagowania na zdarzenia. Na przykład wyszukiwanie danych może generować raport zawierający listę urządzeń, które mogą wymagać zastosowania poprawek.

- **Dowodowych**: generowanie raportu opartego na danych historycznych dla raportów dochodzeniowych.

- **Integralność sieci IT**: generowanie raportu, który pomaga ulepszyć ogólne zabezpieczenia sieci. Na przykład można wygenerować raport zawierający listę urządzeń z słabymi poświadczeniami uwierzytelniania.

- **Widoczność**: Generuj raport obejmujący wszystkie elementy zapytania, aby wyświetlić wszystkie parametry linii bazowej sieci.

## <a name="data-mining-storage"></a>Magazyn wyszukiwania danych

Informacje dotyczące wyszukiwania danych są zapisywane i przechowywane w sposób ciągły, z wyjątkiem sytuacji, gdy urządzenie zostało usunięte. Wyniki wyszukiwania danych można eksportować i przechowywać zewnętrznie na bezpiecznym serwerze. Ponadto czujnik wykonuje Automatyczne codzienne kopie zapasowe, aby zapewnić ciągłość systemu i przechowywanie danych.


## <a name="predefined-data-mining-queries"></a>Wstępnie zdefiniowane zapytania wyszukiwania danych

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

## <a name="create-a-data-mining-query"></a>Tworzenie zapytania wyszukiwania danych

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



## <a name="sensor-reports-based-on-data-mining"></a>Raporty czujnika na podstawie wyszukiwania danych

Regularne raporty, do których można uzyskać dostęp z opcji **raporty** , to wstępnie zdefiniowane raporty wyszukiwania danych. Nie są to zapytania dynamiczne, które są dostępne podczas wyszukiwania danych, ale statyczna reprezentacja wyników zapytania wyszukiwania danych.

Wyniki zapytania wyszukiwania danych nie są dostępne dla użytkowników tylko do odczytu. Administratorzy i analitycy zabezpieczeń, którzy chcą uzyskać dostęp do informacji generowanych przez zapytania wyszukiwania danych tylko do odczytu, powinni zapisać informacje jako raport.

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

## <a name="on-premises-management-console-reports-based-on-data-mining-reports"></a>Raporty lokalnej konsoli zarządzania oparte na raportach wyszukiwania danych

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
