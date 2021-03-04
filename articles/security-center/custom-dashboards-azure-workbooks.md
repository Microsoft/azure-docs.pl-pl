---
title: Galeria skoroszytów w Azure Security Center
description: Dowiedz się, jak tworzyć rozbudowane i interaktywne raporty dotyczące danych Azure Security Center za pomocą Galerii zintegrowanych skoroszytów Azure Monitor
author: memildin
ms.author: memildin
manager: rkarlin
ms.service: security-center
ms.topic: conceptual
ms.date: 03/04/2021
ms.openlocfilehash: 198702f619e490e8000e4430aab23a7f6bfb6d85
ms.sourcegitcommit: 4b7a53cca4197db8166874831b9f93f716e38e30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/04/2021
ms.locfileid: "102107978"
---
# <a name="create-rich-interactive-reports-of-security-center-data"></a>Twórz rozbudowane, interaktywne raporty dotyczące danych Security Center

[Azure monitor skoroszyty](../azure-monitor/visualize/workbooks-overview.md) zapewniają elastyczną kanwę do analizy danych i tworzenia zaawansowanych raportów wizualnych w ramach Azure Portal. Umożliwiają one naciskanie na wiele źródeł danych z platformy Azure i łączenie ich w ujednolicone interaktywne środowiska.

Skoroszyty zapewniają bogaty zestaw funkcji wizualizacji danych platformy Azure. Aby uzyskać szczegółowe przykłady poszczególnych typów wizualizacji, zobacz [przykłady wizualizacji i dokumentację](../azure-monitor/visualize/workbooks-text-visualizations.md). 

W ramach Azure Security Center można uzyskać dostęp do wbudowanych raportów, aby śledzić stan zabezpieczeń organizacji. Możesz również utworzyć raporty niestandardowe, aby wyświetlić szeroką gamę danych z Security Center lub innych obsługiwanych źródeł danych.

:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-snip.png" alt-text="Raport dotyczący oceny wyników w czasie":::

## <a name="availability"></a>Dostępność

| Aspekt                          | Szczegóły                                                                                                                                      |
|---------------------------------|:---------------------------------------------------------------------------------------------------------------------------------------------|
| Stan wydania:                  | Wersja zapoznawcza<br>[!INCLUDE [Legalese](../../includes/security-center-preview-legal-text.md)]                                                       |
| Wpisaną                        | Bezpłatna                                                                                                                                         |
| Wymagane role i uprawnienia: | Aby zapisać skoroszyty, musisz mieć co najmniej uprawnienia współautora skoroszytu w docelowej grupie zasobów                                      |
| Połączeń                         | ![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Tak](./media/icons/yes-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov) |
|                                 |                                                                                                                                              |

## <a name="workbooks-gallery-in-azure-security-center"></a>Galeria skoroszytów w Azure Security Center

Dzięki zintegrowanym funkcjom skoroszytów platformy Azure Azure Security Center ułatwia tworzenie własnych, interaktywnych raportów. Security Center obejmuje również galerię skoroszytów z następującymi raportami gotowymi do dostosowania:

- **Zabezpiecz ocenę w czasie w** celu śledzenia wyników subskrypcji i zmian w zaleceniach dotyczących zasobów
- **Aktualizacje systemu** — wyświetlanie brakujących aktualizacji systemu według zasobów, systemu operacyjnego, ważności i innych
- **Wyniki oceny luk w zabezpieczeniach** — wyświetlanie wyników skanowania zasobów platformy Azure w ramach luk w zabezpieczeniach

:::image type="content" source="media/custom-dashboards-azure-workbooks/workbooks-gallery-security-center.png" alt-text="Galeria wbudowanych skoroszytów w Azure Security Center":::

Wybierz jeden z dostarczonych raportów lub Utwórz własny.

> [!TIP]
> Użyj przycisku **Edytuj** , aby dostosować dowolne dostarczone raporty do swoich oczekiwań. Po zakończeniu edycji wybierz pozycję **Zapisz** , a zmiany zostaną zapisane w nowym skoroszycie.
> 
> :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-supplied-workbooks.png" alt-text="Edytowanie dostarczonych skoroszytów w celu dostosowania ich do konkretnych potrzeb":::

### <a name="use-the-secure-score-over-time-report"></a>Użyj raportu "wynik" w czasie "

Ten raport używa bezpiecznych danych oceny z obszaru roboczego Log Analytics. Te dane muszą zostać wyeksportowane z narzędzia eksportu ciągłego, zgodnie z opisem w artykule [Konfigurowanie eksportu ciągłego ze stron Security Center w Azure Portal](continuous-export.md?tabs=azure-portal).

Podczas konfigurowania eksportu ciągłego należy ustawić częstotliwość eksportowania na **przesyłanie strumieniowe aktualizacji** i **migawek**.

:::image type="content" source="media/custom-dashboards-azure-workbooks/export-frequency-both.png" alt-text="W przypadku skoroszytu o bezpiecznym stanie w czasie należy wybrać obie opcje z ustawień częstotliwości eksportu w konfiguracji eksportu ciągłego.":::

> [!NOTE]
> Migawki są eksportowane co tydzień, dlatego przed wyświetleniem danych w tym raporcie należy odczekać co najmniej jeden tydzień na wyeksportowanie pierwszej migawki.

> [!TIP]
> Aby skonfigurować ciągły eksport w całej organizacji, należy użyć podanych Azure Policy zasad "DeployIfNotExist" opisanych w temacie [Konfigurowanie eksportu ciągłego na dużą skalę](continuous-export.md?tabs=azure-policy).

Raport o bezpiecznych ocenach w czasie obejmuje pięć wykresów do raportowania subskrypcji do wybranych obszarów roboczych:


|Graph  |Przykład  |
|---------|---------|
|**Trendy oceny dla ostatniego tygodnia i miesiąca**<br>Ta sekcja służy do monitorowania bieżącego wyniku i ogólnych trendów wyników subskrypcji.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-1.png" alt-text="Trendy dotyczące bezpiecznego oceny raportu wbudowanego":::|
|**Zagregowany wynik dla wszystkich wybranych subskrypcji**<br>Umieść wskaźnik myszy nad dowolnym punktem w linii trendu, aby zobaczyć zagregowany wynik w dowolnym dniu w wybranym zakresie czasu.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-2.png" alt-text="Zagregowany wynik dla wszystkich wybranych subskrypcji":::|
|**Zalecenia z największą ilością zasobów w złej kondycji**<br>Ta tabela ułatwia Klasyfikacja zaleceń, w przypadku których większość zasobów uległa zmianie w złej kondycji w wybranym okresie.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-3.png" alt-text="Zalecenia z największą ilością zasobów w złej kondycji":::|
|**Wyniki dla określonych kontrolek zabezpieczeń**<br>Formanty zabezpieczeń Security Center są logicznymi grupami rekomendacji. Ten wykres pokazuje, w skrócie, cotygodniowe wyniki dla wszystkich kontrolek.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-4.png" alt-text="Wyniki dla kontrolek zabezpieczeń w wybranym okresie":::|
|**Zmiany zasobów**<br>W tym miejscu wymieniono zalecenia dotyczące większości zasobów ze zmienionym stanem (w dobrej kondycji, w nieprawidłowym stanie lub nie dotyczy) w wybranym okresie. Wybierz z listy dowolne zalecenie, aby otworzyć nową tabelę zawierającą określone zasoby.|:::image type="content" source="media/custom-dashboards-azure-workbooks/secure-score-over-time-table-5.png" alt-text="Zalecenia dotyczące większości zasobów o zmienionym stanie kondycji":::|

### <a name="use-the-system-updates-report"></a>Użyj raportu "aktualizacje systemu"

Ten raport jest oparty na zaleceniach dotyczących zabezpieczeń "aktualizacje systemu powinny być zainstalowane na Twoich maszynach".

Ten raport pomaga identyfikować komputery z oczekującymi aktualizacjami.

Możesz wyświetlić sytuacje dla wybranych subskrypcji zgodnie z:

- Lista zasobów z oczekującymi aktualizacjami
- Lista aktualizacji, których brakuje w Twoich zasobach

:::image type="content" source="media/custom-dashboards-azure-workbooks/system-updates-report.png" alt-text="Raport dotyczący aktualizacji systemu Security Center w oparciu o brakujące aktualizacje zabezpieczeń":::

### <a name="use-the-vulnerability-assessment-findings-report"></a>Użyj raportu "oceny luk w zabezpieczeniach"

Security Center obejmuje skanery luk w zabezpieczeniach dla maszyn, kontenerów w rejestrach kontenerów i serwerów SQL.

Dowiedz się więcej o korzystaniu z tych skanerów:

- [Skanowanie maszyn przy użyciu zintegrowanego skanera VA](deploy-vulnerability-assessment-vm.md)
- [Skanowanie obrazów w rejestrze pod kątem luk w zabezpieczeniach](defender-for-container-registries-usage.md)
- [Skanuj zasoby SQL pod kątem luk w zabezpieczeniach](defender-for-sql-on-machines-vulnerability-assessment.md)

W przypadku każdego z tych skanerów są raportowane oddzielne zalecenia:

- Luki w zabezpieczeniach maszyn wirtualnych należy skorygować
- Luki w zabezpieczeniach Azure Container Registry obrazów powinny być skorygowane (obsługiwane przez Qualys)
- Oceny luk w zabezpieczeniach baz danych SQL należy skorygować
- Oceny luk w zabezpieczeniach na serwerach SQL na maszynach należy skorygować

Ten raport gromadzi te wyniki i organizuje je według ważności, typu zasobu i kategorii.

:::image type="content" source="media/custom-dashboards-azure-workbooks/vulnerability-assessment-findings-report.png" alt-text="Raport dotyczący ocen Security Center oceny luk w zabezpieczeniach":::


## <a name="import-workbooks-from-other-workbook-galleries"></a>Importuj skoroszyty z innych galerii skoroszytów

Jeśli skompilowano skoroszyty w innych usługach platformy Azure i chcesz je przenieść do galerii skoroszytów Azure Security Center:

1. Otwórz skoroszyt docelowy.

1. Na pasku narzędzi wybierz pozycję **Edytuj**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks.png" alt-text="Edytowanie Azure Monitor skoroszytu":::

1. Na pasku narzędzi wybierz opcję, **</>** Aby wprowadzić Edytor zaawansowany.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-advanced-editor.png" alt-text="Uruchamianie edytora zaawansowanego, aby uzyskać kod JSON szablonu galerii":::

1. Skopiuj plik JSON szablonu galerii skoroszytu.

1. Galeria otwartych skoroszytów w Security Center i na pasku menu wybierz pozycję **Nowy**.
1. Wybierz, **</>** Aby wprowadzić Edytor zaawansowany.
1. Wklej w całym pliku JSON szablonu galerii.
1. Wybierz przycisk **Zastosuj**.
1. Na pasku narzędzi wybierz pozycję **Zapisz jako**.

    :::image type="content" source="media/custom-dashboards-azure-workbooks/editing-workbooks-save-as.png" alt-text="Zapisywanie skoroszytu w galerii w Security Center":::

1. Wprowadź wymagane szczegóły zapisywania skoroszytu:
   1. Nazwa skoroszytu
   2. Żądany region
   3. Subskrypcja, Grupa zasobów i udostępnianie, zgodnie z potrzebami.

Zapisany skoroszyt zostanie znaleziony w kategorii **ostatnio modyfikowane skoroszyty** .


## <a name="next-steps"></a>Następne kroki

W tym artykule opisano Security Center strony ze skoroszytami zintegrowanymi Azure Monitor z wbudowanymi raportami i opcjami tworzenia własnych, interaktywnych raportów.

- Dowiedz się więcej o [Azure monitor skoroszytach](../azure-monitor/visualize/workbooks-overview.md)
- Wbudowane raporty pobierają swoje dane z Security Center zaleceń. Poznaj wiele zaleceń dotyczących zabezpieczeń w [zaleceniach dotyczących zabezpieczeń — Przewodnik referencyjny](recommendations-reference.md)