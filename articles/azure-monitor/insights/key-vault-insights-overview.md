---
title: Monitoruj Key Vault przy użyciu Azure Monitor dla Key Vault | Microsoft Docs
description: W tym artykule opisano Azure Monitor dla magazynów kluczy.
services: azure-monitor
ms.topic: conceptual
author: mrbullwinkle
ms.author: mbullwin
ms.date: 09/10/2020
ms.openlocfilehash: 4b91a9a73035b3add309e72ce544375520cf279e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91278621"
---
# <a name="monitoring-your-key-vault-service-with-azure-monitor-for-key-vault"></a>Monitorowanie usługi magazynu kluczy za pomocą Azure Monitor dla Key Vault
Azure Monitor dla Key Vault oferuje kompleksowe monitorowanie Twoich magazynów kluczy, udostępniając ujednolicony widok żądań, wydajności, niepowodzeń i opóźnień Key Vault.
Ten artykuł pomoże Ci zrozumieć, jak dołączyć i dostosować środowisko Azure Monitor w Key Vault.

## <a name="introduction-to-azure-monitor-for-key-vault"></a>Wprowadzenie do Azure Monitor dla Key Vault

Przed przechodzeniem do środowiska należy zrozumieć, jak prezentuje i wizualizuje informacje.
-    **W perspektywie skalowania** przedstawia widok migawki wydajności na podstawie żądań, podziałów błędów oraz przeglądów operacji i opóźnień.
-   **Przechodzenie do szczegółów analizy** określonego magazynu kluczy w celu przeprowadzenia szczegółowej analizy.
-    **Dostosowywalne** miejsce, w którym można zmienić metryki, które mają być wyświetlane, zmodyfikować lub ustawić progi, które są wyrównane z limitami, i zapisać własny skoroszyt. Wykresy w skoroszycie można przypinać do pulpitów nawigacyjnych platformy Azure.

Azure Monitor Key Vault łączy zarówno dzienniki, jak i metryki, aby zapewnić globalne rozwiązanie do monitorowania. Wszyscy użytkownicy mogą uzyskać dostęp do danych monitorowania opartych na metrykach, jednak dołączenie wizualizacji opartych na dziennikach może wymagać od użytkowników [umożliwienia użytkownikom rejestrowania ich Azure Key Vault](../../key-vault/general/logging.md).

## <a name="view-from-azure-monitor"></a>Wyświetl od Azure Monitor

Z poziomu Azure Monitor można wyświetlić szczegóły żądania, opóźnienia i niepowodzenia z wielu magazynów kluczy w ramach subskrypcji, a także pomóc w identyfikowaniu problemów z wydajnością i ograniczania przepustowości.

Aby wyświetlić wykorzystanie i operacje Twoich magazynów kluczy we wszystkich subskrypcjach, wykonaj następujące czynności:

1. Zaloguj się do [Azure Portal](https://portal.azure.com/)

2. Wybierz pozycję **monitor** z okienka po lewej stronie w Azure Portal i w sekcji szczegółowe informacje wybierz pozycję **magazyny kluczy**.

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

Aby lepiej zrozumieć, co reprezentuje każdy kod stanu, zalecamy zapoznanie się z dokumentacją dotyczącą [Azure Key Vault stanu i kodów odpowiedzi](../../key-vault/general/authentication-requests-and-responses.md).

## <a name="view-from-a-key-vault-resource"></a>Wyświetl z zasobu Key Vault

Aby uzyskać dostęp do Azure Monitor Key Vault bezpośrednio z magazynu kluczy:

1. W Azure Portal wybierz pozycję magazyny kluczy.

2. Z listy wybierz magazyn kluczy. W sekcji monitorowanie wybierz pozycję szczegółowe dane.

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
> Należy pamiętać, że użytkownicy muszą mieć włączone ustawienia diagnostyczne, aby wyświetlić ten skoroszyt. Aby dowiedzieć się więcej na temat włączania ustawień diagnostycznych, Przeczytaj więcej na temat [rejestrowania Azure Key Vault](../../key-vault/general/logging.md).

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

1. Wybierz pozycję **monitor** z portalu, a następnie wybierz pozycję **magazyny kluczy** w okienku po lewej stronie.
2. W skoroszycie **Przegląd** , na pasku poleceń wybierz pozycję **Edytuj**.
3. Z listy rozwijanej **subskrypcje** wybierz co najmniej jedną subskrypcję, która ma być yo używana jako domyślna. Pamiętaj, że skoroszyt obsługuje wybieranie maksymalnie 10 subskrypcji.
4. Wybierz z listy rozwijanej **magazyny kluczy** jeden lub więcej kont, które mają być używane domyślnie jako domyślne. Pamiętaj, że skoroszyt obsługuje wybieranie maksymalnie 200 kont magazynu.
5. Wybierz pozycję **Zapisz jako** na pasku poleceń, aby zapisać kopię skoroszytu z własnymi dostosowaniami, a następnie kliknij pozycję **Zakończono edycję** , aby powrócić do trybu odczytu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Ogólne wskazówki dotyczące rozwiązywania problemów można znaleźć w artykule dotyczącym szczegółowych informacji o [rozwiązywaniu problemów](troubleshoot-workbooks.md)opartych na skoroszycie.

Ta sekcja pomoże Ci w diagnozowaniu i rozwiązywaniu problemów z typowymi problemami, które mogą wystąpić podczas korzystania z Azure Monitor Key Vault. Skorzystaj z poniższej listy, aby znaleźć informacje istotne dla danego problemu.

### <a name="resolving-performance-issues-or-failures"></a>Rozwiązywanie problemów z wydajnością lub niepowodzeń

Aby ułatwić rozwiązywanie problemów związanych z magazynem kluczy, które można zidentyfikować za pomocą Azure Monitor dla Key Vault, zobacz [dokumentację Azure Key Vault](../../key-vault/index.yml).

### <a name="why-can-i-only-see-200-key-vaults"></a>Dlaczego można zobaczyć tylko magazyny kluczy 200

Istnieje limit 200 magazynów kluczy, które można wybrać i wyświetlić. Bez względu na liczbę wybranych subskrypcji liczba wybranych magazynów kluczy ma limit wynoszący 200.

### <a name="why-dont-i-see-all-my-subscriptions-in-the-subscription-picker"></a>Dlaczego nie widzę wszystkich moich subskrypcji w selektorze subskrypcji

Wyświetlamy tylko te subskrypcje, które zawierają magazyny kluczy, wybrane z wybranego filtru subskrypcji, które są wybrane w "katalogu i subskrypcji" w nagłówku Azure Portal.

![Zrzut ekranu filtru subskrypcji](./media/key-vaults-insights-overview/Subscriptions.png)

### <a name="i-want-to-make-changes-or-add-additional-visualizations-to-key-vault-insights-how-do-i-do-so"></a>Chcę wprowadzić zmiany lub dodać dodatkowe wizualizacje do Key Vault szczegółowych informacji, jak to zrobić

Aby wprowadzić zmiany, wybierz pozycję "Edytuj tryb", aby zmodyfikować skoroszyt, a następnie Zapisz swoją służbę jako nowy skoroszyt powiązany z określoną subskrypcją i grupą zasobów.

### <a name="what-is-the-time-grain-once-we-pin-any-part-of-the-workbooks"></a>Co to jest ziarno czasu, gdy przypinam wszystkie części skoroszytów

Wykorzystujemy ziarno czasu "Auto", dlatego zależy od tego, jaki zakres czasu jest wybrany.

### <a name="what-is-the-time-range-when-any-part-of-the-workbook-is-pinned"></a>Jaki jest zakres czasu, gdy dowolna część skoroszytu jest przypięta

Zakres czasu zależy od ustawień pulpitu nawigacyjnego.

### <a name="what-if-i-want-to-see-other-data-or-make-my-own-visualizations-how-can-i-make-changes-to-the-key-vault-insights"></a>Co zrobić, jeśli chcę zobaczyć inne dane lub utworzyć własne wizualizacje? Jak mogę wprowadzić zmiany w usłudze Key Vault Insights

Istniejący skoroszyt można edytować za pomocą trybu edycji, a następnie zapisać swoją służbę jako nowy skoroszyt, który będzie zawierał wszystkie nowe zmiany.

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jakie scenariusze skoroszyty są przeznaczone do obsługi, jak tworzyć nowe i dostosowywać istniejące raporty, a inne dzięki przeglądowi [Tworzenie interaktywnych raportów przy użyciu skoroszytów Azure monitor](../platform/workbooks-overview.md).
