---
title: Monitoruj Key Vault z Azure Monitor dla Key Vault (wersja zapoznawcza) | Microsoft Docs
description: W tym artykule opisano Azure Monitor dla magazynów kluczy.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 04/13/2019
ms.openlocfilehash: 97bea90e67b9449a8f5fd7b333b9ac149abef2f8
ms.sourcegitcommit: 51977b63624dfd3b4f22fb9fe68761d26eed6824
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/17/2020
ms.locfileid: "84945464"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault-preview"></a>Monitorowanie usługi magazynu kluczy za pomocą Azure Monitor dla Key Vault (wersja zapoznawcza)
Azure Monitor dla Key Vault (wersja zapoznawcza) zapewnia kompleksowe monitorowanie magazynów kluczy, udostępniając ujednolicony widok żądań Key Vault, wydajności, błędów i opóźnień.
Ten artykuł pomoże Ci zrozumieć, jak dołączyć i dostosować środowisko Azure Monitor dla Key Vault (wersja zapoznawcza).

## <a name="introduction-to-azure-monitor-for-key-vault-preview"></a>Wprowadzenie do Azure Monitor dla Key Vault (wersja zapoznawcza)

Przed przechodzeniem do środowiska należy zrozumieć, jak prezentuje i wizualizuje informacje.
-    **W perspektywie skalowania** przedstawia widok migawki wydajności na podstawie żądań, podziałów błędów oraz przeglądów operacji i opóźnień.
-   **Przechodzenie do szczegółów analizy** określonego magazynu kluczy w celu przeprowadzenia szczegółowej analizy.
-    **Dostosowywalne** miejsce, w którym można zmienić metryki, które mają być wyświetlane, zmodyfikować lub ustawić progi, które są wyrównane z limitami, i zapisać własny skoroszyt. Wykresy w skoroszycie można przypinać do pulpitów nawigacyjnych platformy Azure.

Azure Monitor Key Vault łączy zarówno dzienniki, jak i metryki, aby zapewnić globalne rozwiązanie do monitorowania. Wszyscy użytkownicy mogą uzyskać dostęp do danych monitorowania opartych na metrykach, jednak dołączenie wizualizacji opartych na dziennikach może wymagać od użytkowników [umożliwienia użytkownikom rejestrowania ich Azure Key Vault](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="configuring-your-key-vaults-for-monitoring"></a>Konfigurowanie magazynów kluczy do monitorowania

> [!NOTE]
> Włączenie dzienników jest usługą płatną, która zapewnia dodatkowe możliwości monitorowania.

1. Karta czas oczekiwania & operacji pomaga określić, ile i które magazyny kluczy są włączone. Aby rozpocząć zbieranie, wybierz przycisk **Włącz** . spowoduje to przejście do oddzielnego skoroszytu zawierającego listę magazynów kluczy, które wymagają włączenia dzienników diagnostycznych.

    ![Zrzut ekranu przedstawiający operacje i kartę opóźnienia z wyświetlonym niebieskim przyciskiem włączania](./media/key-vaults-insights-overview/enable-logging.png)

2. Aby włączyć dzienniki diagnostyczne, kliknij link **Włącz** poniżej kolumny akcje i Utwórz nowe ustawienie diagnostyczne, które wysyła dzienniki do obszaru roboczego log Analytics. Zaleca się wysłanie wszystkich dzienników do tego samego obszaru roboczego.

3. Po zapisaniu ustawień diagnostycznych będzie można wyświetlić wszystkie wykresy i wizualizacje oparte na dziennikach poniżej Key Vault szczegółowych informacji. Zapełnianie dzienników może potrwać kilka minut.

4. Aby uzyskać dodatkową pomoc dotyczącą sposobu włączania dzienników diagnostycznych dla usługi Key Vault, Przeczytaj [Pełny przewodnik](https://docs.microsoft.com/azure/key-vault/key-vault-logging).

## <a name="view-from-azure-monitor"></a>Wyświetl od Azure Monitor

Z poziomu Azure Monitor można wyświetlić szczegóły żądania, opóźnienia i niepowodzenia z wielu magazynów kluczy w ramach subskrypcji, a także pomóc w identyfikowaniu problemów z wydajnością i ograniczania przepustowości.

Aby wyświetlić wykorzystanie i operacje związane z kontami magazynu we wszystkich subskrypcjach, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)

2. Wybierz pozycję **monitor** z okienka po lewej stronie w Azure Portal i w sekcji szczegółowe informacje wybierz pozycję **magazyny kluczy (wersja zapoznawcza)**.

![Zrzut ekranu przedstawiający środowisko omówienia z wieloma wykresami](./media/key-vaults-insights-overview/overview.png)

## <a name="overview-workbook"></a>Skoroszyt omówienia

W skoroszycie przegląd dla wybranej subskrypcji w tabeli są wyświetlane metryki magazynu kluczy interakcyjnych dla magazynów kluczy pogrupowanych w ramach subskrypcji. Wyniki można filtrować na podstawie opcji wybranych z następujących list rozwijanych:

* Subskrypcje — na liście są wyświetlane tylko subskrypcje, w których znajdują się magazyny kluczy.

* Magazyny kluczy — domyślnie tylko do 5 magazynów kluczy są wstępnie wybrane. W przypadku wybrania opcji wszystkie lub wiele magazynów kluczy w selektorze zakresu zostaną zwrócone maksymalnie 200 magazynów kluczy. Na przykład jeśli masz łącznie 573 magazynów kluczy w ramach trzech wybranych subskrypcji, zostaną wyświetlone tylko magazyny 200.

* Zakres czasu — domyślnie wyświetla ostatnie 24 godziny informacji na podstawie odpowiednich opcji.

Kafelek licznika, pod listą rozwijaną, rzutuje całkowitą liczbę magazynów kluczy w wybranych subskrypcjach i odzwierciedla liczbę wybranych elementów. Istnieją warunkowe map cieplnych kodowane kolorami dla kolumn skoroszytu, które zgłaszają żądania, błędy i metryki opóźnienia. Optymalny kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach.

## <a name="failures-workbook"></a>Skoroszyt błędów

Wybierz pozycję **Błędy** w górnej części strony, a zostanie otwarta karta błędy. Przedstawiono w nim trafień interfejsu API, częstotliwość w czasie wraz z ilością niektórych kodów odpowiedzi.

![Zrzut ekranu przedstawiający skoroszyt błędów](./media/key-vaults-insights-overview/failures.png)

Istnieje warunkowe kodowanie kolorami lub map cieplnych dla kolumn w skoroszycie, które zgłaszają metryki trafień interfejsu API do wartości niebieska. Optymalny kolor ma najwyższą wartość, a jaśniejszy kolor jest oparty na najniższych wartościach.

W tym skoroszycie są wyświetlane sukcesy (kody stanu 2xx), błędy uwierzytelniania (kody stanu 401/403), ograniczanie (kody stanu 429) i inne błędy (kody stanu 4xx).

Aby lepiej zrozumieć, co reprezentuje każdy kod stanu, zalecamy zapoznanie się z dokumentacją dotyczącą [Azure Key Vault stanu i kodów odpowiedzi](https://docs.microsoft.com/azure/key-vault/authentication-requests-and-responses).

## <a name="operations--latency-workbook"></a>Skoroszyt opóźnienia & operacji

Wybierz pozycję **operacje & opóźnienie** w górnej części strony, a następnie zostanie otwarta karta **czas oczekiwania & operacji** . Na tej karcie można dołączyć magazyny kluczy do monitorowania. Aby uzyskać bardziej szczegółowe instrukcje, zobacz sekcję [Konfigurowanie magazynów kluczy do monitorowania](#configuring-your-key-vaults-for-monitoring) .

Możesz zobaczyć, ile magazynów kluczy jest włączonych do rejestrowania. Jeśli co najmniej jeden magazyn został skonfigurowany prawidłowo, zobaczysz tabele, w których są wyświetlane kody operacji i stanu dla każdego z Twoich magazynów kluczy. Aby uzyskać dodatkowe informacje na temat poszczególnych operacji, można kliknąć sekcję Szczegóły dla wiersza.

![Zrzut ekranu przedstawiający operacje i wykresy opóźnień](./media/key-vaults-insights-overview/logs.png)

Jeśli nie widzisz żadnych danych dla tej sekcji, zapoznaj się z górną sekcją dotyczącą włączania dzienników dla Azure Key Vault lub Sprawdź poniższą sekcję rozwiązywania problemów.

## <a name="view-from-a-key-vault-resource"></a>Wyświetl z zasobu Key Vault

Aby uzyskać dostęp do Azure Monitor Key Vault bezpośrednio z magazynu kluczy:

1. W Azure Portal wybierz pozycję magazyny kluczy.

2. Z listy wybierz magazyn kluczy. W sekcji monitorowanie wybierz pozycję szczegółowe dane (wersja zapoznawcza).

Te widoki są również dostępne, wybierając nazwę zasobu magazynu kluczy w skoroszycie na poziomie Azure Monitor.

![Zrzut ekranu przedstawiający widok z zasobu magazynu kluczy](./media/key-vaults-insights-overview/key-vault-resource-view.png)

W skoroszycie z **omówieniem** magazynu kluczy przedstawiono kilka metryk wydajności, które ułatwiają szybkie oszacowanie:

- Interaktywne wykresy wydajności pokazujące najważniejsze szczegóły dotyczące transakcji, opóźnień i dostępności magazynu kluczy.

- Kafelki metryk i Stanów wyróżniają dostępność usługi, łączną liczbę transakcji z zasobem magazynu kluczy oraz ogólne opóźnienia.

Wybranie dowolnej z innych kart dla **niepowodzeń** lub **operacji** powoduje otwarcie odpowiednich skoroszytów.

![Zrzut ekranu przedstawiający widok błędów](./media/key-vaults-insights-overview/resource-failures.png)

Skoroszyt błędy powoduje podział wyników wszystkich żądań magazynu kluczy w wybranym przedziale czasowym i zapewnia kategoryzację sukcesów (2xx), błędy uwierzytelniania (401/403), ograniczenie przepustowości (429) i inne błędy.

![Zrzut ekranu przedstawiający widok operacji](./media/key-vaults-insights-overview/operations.png)

Skoroszyt operacji pozwala użytkownikom na głębokie szczegółowe w szczegółowe informacje o wszystkich transakcjach, które można filtrować według stanu wyniku przy użyciu kafelków najwyższego poziomu.

![Zrzut ekranu przedstawiający widok operacji](./media/key-vaults-insights-overview/info.png)

Użytkownicy mogą również przedefiniować zakres widoków w oparciu o określone typy transakcji w górnej tabeli, co dynamicznie aktualizuje niższą tabelę, gdzie użytkownicy mogą wyświetlać pełne szczegóły operacji w okienku kontekstowym okienka wyskakującego.

>[!NOTE]
> Należy pamiętać, że użytkownicy muszą mieć włączone ustawienia diagnostyczne, aby wyświetlić ten skoroszyt. Aby dowiedzieć się więcej na temat włączania ustawień diagnostycznych, Przeczytaj więcej na temat [rejestrowania Azure Key Vault](https://docs.microsoft.com/azure/key-vault/general/logging).

## <a name="pin-and-export"></a>Przypnij i Eksportuj

Możesz przypiąć każdą z sekcji metryk do pulpitu nawigacyjnego platformy Azure, wybierając ikonę pinezki w prawym górnym rogu sekcji.

Omówienie usługi wiele subskrypcji i magazynów kluczy lub skoroszytów obsługują Eksportowanie wyników w formacie programu Excel poprzez wybranie ikony pobierania z lewej strony ikony pinezki.

![Zrzut ekranu przedstawiający ikonę pinezki](./media/key-vaults-insights-overview/pin.png)

## <a name="customize-azure-monitor-for-key-vault"></a>Dostosuj Azure Monitor dla Key Vault

W tej sekcji przedstawiono typowe scenariusze edytowania skoroszytu w celu dostosowania ich do potrzeb związanych z analizą danych:
*  Ustaw zakres skoroszytu w taki sposób, aby zawsze wybierał określoną subskrypcję lub magazyny kluczy
* Zmień metryki w siatce
* Zmiana progu żądań
* Zmiana renderowania koloru

Możesz rozpocząć Dostosowywanie, włączając tryb edycji, wybierając przycisk **Dostosuj** na górnym pasku narzędzi.

![Zrzut ekranu przedstawiający przycisk Dostosuj](./media/key-vaults-insights-overview/customize.png)

Dostosowania są zapisywane w niestandardowym skoroszycie, aby zapobiec zastąpieniu konfiguracji domyślnej w naszym opublikowanym skoroszycie. Skoroszyty są zapisywane w grupie zasobów, w sekcji Moje raporty, która jest prywatna dla użytkownika lub w sekcji Raporty udostępnione dostępne dla wszystkich użytkowników mających dostęp do grupy zasobów. Po zapisaniu skoroszytu niestandardowego musisz przejść do galerii skoroszytów, aby go uruchomić.

![Zrzut ekranu przedstawiający galerię skoroszytów](./media/key-vaults-insights-overview/gallery.png)

### <a name="specifying-a-subscription-or-key-vault"></a>Określanie subskrypcji lub magazynu kluczy

Można skonfigurować wiele subskrypcji i omówienia magazynu kluczy lub skoroszyty błędów, aby określić zakres do określonych subskrypcji lub magazynów kluczy w każdym uruchomieniu, wykonując następujące czynności:

1. Wybierz pozycję **monitor** z portalu, a następnie wybierz pozycję **magazyny kluczy (wersja zapoznawcza)** w okienku po lewej stronie.
2. W skoroszycie **Przegląd** , na pasku poleceń wybierz pozycję **Edytuj**.
3. Z listy rozwijanej **subskrypcje** wybierz co najmniej jedną subskrypcję, która ma być yo używana jako domyślna. Pamiętaj, że skoroszyt obsługuje wybieranie maksymalnie 10 subskrypcji.
4. Wybierz z listy rozwijanej **magazyny kluczy** jeden lub więcej kont, które mają być używane domyślnie jako domyślne. Pamiętaj, że skoroszyt obsługuje wybieranie maksymalnie 200 kont magazynu.
5. Wybierz pozycję **Zapisz jako** na pasku poleceń, aby zapisać kopię skoroszytu z własnymi dostosowaniami, a następnie kliknij pozycję **Zakończono edycję** , aby powrócić do trybu odczytu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ogólne wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule dotyczącym szczegółowych informacji o [rozwiązywaniu problemów](troubleshoot-workbooks.md)opartych na skoroszycie.

Ta sekcja pomoże Ci w diagnozowaniu i rozwiązywaniu problemów z typowymi problemami, które mogą wystąpić podczas korzystania z Azure Monitor dla Key Vault (wersja zapoznawcza). Skorzystaj z poniższej listy, aby znaleźć informacje istotne dla danego problemu.

### <a name="resolving-performance-issues-or-failures"></a>Rozwiązywanie problemów z wydajnością lub niepowodzeń

Aby ułatwić rozwiązywanie problemów związanych z magazynem kluczy, które można zidentyfikować za pomocą Azure Monitor dla Key Vault (wersja zapoznawcza), zobacz [dokumentację Azure Key Vault](https://docs.microsoft.com/azure/key-vault/).

### <a name="why-can-i-only-see-200-key-vaults"></a>Dlaczego można zobaczyć tylko magazyny kluczy 200

Istnieje limit 200 magazynów kluczy, które można wybrać i wyświetlić. Bez względu na liczbę wybranych subskrypcji liczba wybranych magazynów kluczy ma limit wynoszący 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Dlaczego nie widzę wszystkich moich subskrypcji w selektorze subskrypcji

Wyświetlamy tylko te subskrypcje, które zawierają magazyny kluczy, wybrane z wybranego filtru subskrypcji, które są wybrane w "katalogu i subskrypcji" w nagłówku Azure Portal.

![Zrzut ekranu filtru subskrypcji](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-am-getting-an-error-message-that-the-query-exceeds-the-maximum-number-of-workspacesregions-allowed-what-to-do-now"></a>Otrzymuję komunikat o błędzie informujący o tym, że "zapytanie przekracza maksymalną dozwoloną liczbę obszarów roboczych/regionów", co zrobić teraz

Obecnie istnieje ograniczenie do 25 regionów i 200 obszarów roboczych, aby wyświetlić dane, należy zmniejszyć liczbę subskrypcji i/lub grup zasobów.

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Chcę wprowadzić zmiany lub dodać dodatkowe wizualizacje do Key Vault szczegółowych informacji, jak to zrobić

Aby wprowadzić zmiany, wybierz pozycję "Edytuj tryb", aby zmodyfikować skoroszyt, a następnie Zapisz swoją służbę jako nowy skoroszyt powiązany z określoną subskrypcją i grupą zasobów.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Co to jest ziarno czasu, gdy przypinam wszystkie części skoroszytów

Wykorzystujemy ziarno czasu "Auto", dlatego zależy od tego, jaki zakres czasu jest wybrany.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Jaki jest zakres czasu, gdy dowolna część skoroszytu jest przypięta

Zakres czasu zależy od ustawień pulpitu nawigacyjnego.

### <a name="why-do-i-not-see-any-data-for-my-key-vault-under-the-operations--latency-sections"></a>Dlaczego nie widzę żadnych danych dla Key Vault w sekcji opóźnienia & operacji

Aby wyświetlić dane oparte na dziennikach, należy włączyć dzienniki dla każdego z magazynów kluczy, które mają być monitorowane. Można to zrobić w ustawieniach diagnostycznych dla każdego magazynu kluczy. Konieczne będzie wysłanie danych do określonego obszaru roboczego Log Analytics.

### <a name="i-have-already-enabled-logs-for-my-key-vault-why-am-i-still-unable-to-see-my-data-under-operations--latency"></a>Mam już włączone dzienniki dla mojego Key Vault, dlaczego nadal nie mogę zobaczyć moich danych w obszarze opóźnienia & operacji

Obecnie dzienniki diagnostyczne nie działają z mocą wsteczną, więc dane zostaną uruchomione dopiero po wykonaniu akcji związanych z magazynami kluczy. W związku z tym może upłynąć trochę czasu od godzin do dnia, w zależności od aktywnego magazynu kluczy.

Ponadto jeśli masz wybraną dużą liczbę magazynów kluczy i subskrypcji, możesz nie być w stanie wyświetlać danych z powodu ograniczeń zapytania. Aby można było wyświetlić dane, może być konieczne zmniejszenie liczby wybranych subskrypcji lub magazynów kluczy. 

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Co zrobić, jeśli chcę zobaczyć inne dane lub utworzyć własne wizualizacje? Jak mogę wprowadzić zmiany w usłudze Key Vault Insights

Istniejący skoroszyt można edytować za pomocą trybu edycji, a następnie zapisać swoją służbę jako nowy skoroszyt, który będzie zawierał wszystkie nowe zmiany.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](https://docs.microsoft.com/azure/azure-monitor/platform/workbooks-overview).
