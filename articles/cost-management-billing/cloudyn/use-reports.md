---
title: Korzystanie z raportów platformy Cloudyn na platformie Azure
description: W tym artykule opisano przeznaczenie raportów usługi Cloudyn, które są dostępne w portalu usługi Cloudyn, aby ułatwić ich efektywne wykorzystanie.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 4bc68e851c4f3ba85f8ea18a91d86906e1f87887
ms.sourcegitcommit: 56cbd6d97cb52e61ceb6d3894abe1977713354d9
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/20/2020
ms.locfileid: "88685225"
---
# <a name="reports-available-in-the-cloudyn-portal"></a>Raporty dostępne w portalu usługi Cloudyn

W tym artykule opisano przeznaczenie raportów usługi Cloudyn, które są dostępne w portalu usługi Cloudyn. Przedstawiono także, jak efektywnie korzystać z raportów. Większość raportów jest intuicyjna i ma jednolity wygląd i działanie. Większość akcji, które można wykonać w ramach danego raportu, można także wykonać w innych raportach. Aby zapoznać się z omówieniem korzystania z raportów usługi Cloudyn, w tym dostosowywaniem, zapisywaniem lub planowaniem raportów, zobacz [Omówienie raportów dotyczących kosztów](understanding-cost-reports.md).

Usługa Azure Cost Management oferuje podobne funkcje jak rozwiązanie Cloudyn. Usługa Azure Cost Management to natywne rozwiązanie do zarządzania kosztami na platformie Azure. Ułatwia ona analizowanie kosztów, tworzenie budżetów i zarządzanie nimi, eksportowanie danych, a także zapoznawanie się z rekomendacjami dotyczącymi optymalizacji i ich wdrażanie w celu zaoszczędzenia pieniędzy. Aby uzyskać więcej informacji, zobacz [Azure Cost Management](../cost-management-billing-overview.md).

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-types"></a>Typy raportów

Istnieją trzy typy raportów usługi Cloudyn:

- Raporty w czasie. Na przykład raport Koszty w czasie. Raporty „w czasie” przedstawiają serię czasową danych w wybranym okresie ze wstępnie zdefiniowaną dokładnością i pokazują dane z tygodniową dokładnością dla ostatnich dwóch miesięcy. Można użyć grupowania i filtrowania, aby przybliżać różne punkty danych.
  - Raporty „w czasie” mogą ułatwić dostrzeżenie trendów i wykrycie nagłych zmian lub anomalii.
- Raporty analizy. Na przykład raport Analiza kosztów. Te raporty przedstawiają dane zagregowane w zdefiniowanym okresie oraz umożliwiają grupowanie i filtrowanie danych.
  - Raporty analizy mogą ułatwić wyświetlenie nagłych zmian i określenie głównych przyczyn anomalii oraz przedstawić szczegółowy podział danych.
- Raporty tabelaryczne. Dowolny raport można wyświetlić w postaci tabeli, lecz niektóre raporty są wyświetlane tylko w postaci tabeli. Te raporty udostępniają szczegółowe listy elementów.
  - Rekomendacje to raporty tabelaryczne — wizualizacje nie są dla nich dostępne. Można jednak wizualizować wyniki rekomendacji. Na przykład oszczędności w czasie.
  - Raporty tabelaryczne są przydatne jako listy akcji lub na potrzeby eksportu danych w celu dalszej obróbki. Na przykład raport obciążeń zwrotnych.

Raporty kosztów przedstawiają koszty _rzeczywiste_ lub _amortyzowane_.

Raporty kosztów rzeczywistych przedstawiają płatności dokonane w wybranym przedziale czasu. Na przykład wszystkie opłaty jednorazowe, takie jak zakupy wystąpień zarezerwowanych, są przedstawiane w raportach kosztów rzeczywistych jako nagłe wzrosty kosztów.

Raporty kosztów amortyzowanych rozkładają opłaty jednorazowe na okres, którego dotyczą. Na przykład jednorazowe opłaty za zakupy wystąpień zarezerwowanych są rozłożone na czas rezerwacji i nie są przedstawiane jako nagłe wzrosty wartości. Widok amortyzowany jest jedynym sposobem wyświetlania rzeczywistych trendów i tworzenia przewidywań kosztów.

W niektórych przypadkach amortyzacja jest prezentowana jako oddzielny raport. Przykłady to raporty Analiza kosztów i Analiza kosztów amortyzowanych. W innych przypadkach amortyzacja to zasady raportu takiego jak Alokacja kosztów i Analiza kosztów.

Można zaplanować każdy raport, aby zapewnić jego regularne dostarczanie. Raporty kosztów umożliwiają ustawienie progu, dlatego są przydatne na potrzeby alertów.

## <a name="cost-analysis-vs-cost-allocation"></a>Analiza kosztów a alokacja kosztów

Raporty _analizy kosztów_ umożliwiają wyświetlanie danych dotyczących rozliczeń pochodzących od dostawców chmury. Korzystając z raportów, można grupować i analizować różne segmenty danych zawierające elementy pochodzące z pliku rozliczeniowego. Raporty umożliwiają szczegółowe nawigowanie po kosztach za pomocą pierwotnych danych rozliczeń dostawcy chmury.

Niektóre raporty _analizy kosztów_ nie grupują kosztów według tagów zasobów. Informacje o rozliczeniach oparte na tagach pojawiają się w raportach dopiero po alokacji kosztów przez utworzenie modelu kosztów przy użyciu usługi [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

Raporty _alokacji kosztów_ są dostępne po utworzeniu modelu kosztów przy użyciu usługi [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs). Usługa Cloudyn przetwarza dane dotyczące kosztów i rozliczeń oraz _dopasowuje_ dane do danych użycia i tagów dla kont w chmurze. Aby móc dopasowywać dane, usługa Cloudyn wymaga dostępu do danych użycia. Jeśli masz konta bez poświadczeń, są one oznaczone jako _zasoby bez kategorii_.

## <a name="dashboards"></a>Pulpity nawigacyjne

Pulpity nawigacyjne w usłudze Cloudy zapewniają wysokopoziomowy widok raportów. Pulpity nawigacyjne składają się z widżetów, a każdy widżet jest zasadniczo miniaturą raportu. Podczas [dostosowywania raportów](understanding-cost-reports.md#save-and-schedule-reports) zapisuje się je w obszarze Moje raporty i są one dodawane do pulpitu nawigacyjnego. Aby uzyskać więcej informacji na temat pulpitów nawigacyjnych, zobacz [Wyświetlanie kluczowych metryk kosztów za pomocą pulpitów nawigacyjnych](dashboards.md).

## <a name="budget-information-in-reports"></a>Informacje o budżecie w raportach

Wiele raportów usługi Cloudyn przedstawia informacje o budżecie po jego ręcznym utworzeniu. Dlatego informacje o budżecie nie są wyświetlane w raportach do momentu utworzenia budżetu. Aby uzyskać więcej informacji, zobacz [Ustawienia zarządzania budżetem](#budget-management-settings).

## <a name="reports-and-reporting-features"></a>Raporty i funkcje raportowania

Usługa Cloudyn obejmuje następujące raporty i funkcje raportowania.

### <a name="cost-navigator-report"></a>Raport Nawigator kosztów

Raport Nawigator kosztów to szybki sposób wyświetlania zużycia rozliczeń przy użyciu widoku pulpitu nawigacyjnego. Zawiera podzbiór filtrów i podstawowych widoków, które natychmiastowo wyświetlają widok podsumowania dla kosztów organizacji. Koszty są pokazywane według daty. Ponieważ raport ma stanowić początkowy widok kosztów, nie jest on tak elastyczny ani kompletny jak wiele innych raportów lub niestandardowych pulpitów nawigacyjnych tworzonych samodzielnie.

Domyślnie główne widoki raportu pokazują:

- Koszty w czasie za pomocą widoku wykresu słupkowego dla tygodnia pracy Można zmienić ustawienie **Zakres dat**, aby zmienić zakres dat wykresu słupkowego.
- Wydatki według usługi przy użyciu wykresu kołowego
- Klasyfikacja zasobów według tagów przy użyciu wykresu kołowego
- Wydatki według jednostek kosztów przy użyciu wykresu kołowego
- Koszty łączne według dni w widoku listy

### <a name="cost-analysis-report"></a>Raport analizy kosztów

Raport Analiza kosztów stanowi wyliczenie prezentacji kosztów i obciążeń zwrotnych zgodnie z zasadami. Agreguje on użycie chmury w wybranym przedziale czasu po zastosowaniu wszystkich reguł alokacji do kosztów. Na przykład oblicza koszty według tagów, ponownie przypisuje koszty zasobów bez tagów i opcjonalnie alokuje wykorzystanie wystąpień zarezerwowanych.

Zasady ustawione w usłudze [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) są używane w raporcie Analiza kosztów, a wyniki są następnie łączone z informacjami pochodzącymi z pierwotnych danych dostawcy chmury.

Jak jest obliczany ten raport? Usługa Cloudyn zapewnia, że alokacja zachowuje integralność każdego połączonego konta, przez zastosowanie _koligacji konta_. Koligacja gwarantuje, że do konta, które nie korzysta z określonej usługi, nie są alokowane żadne koszty tej usługi. Koszty naliczane w ramach tego konta pozostają na tym koncie i nie są obliczane przez zasady alokacji. Na przykład może istnieć pięć połączonych kont. Jeśli tylko trzy z nich korzystają z usług magazynu, koszt usług magazynu jest alokowany tylko dla tagów na tych trzech kontach.

Raport Analiza kosztów umożliwia:

- Obliczenie prezentacji kosztów/obciążeń zwrotnych organizacji
- Klasyfikowanie wszystkich kosztów
- Wyświetlanie zagregowanego widoku całego wdrożenia dla określonego przedziału czasu
- Wyświetlanie kosztów według tagów na podstawie zasad utworzonych w modelu kosztów

Aby użyć raportu Analiza kosztów:

1. Wybierz zakres dat.
2. Dodaj tagi odpowiednio do potrzeb.
3. Dodaj grupy.
4. Wybierz utworzony wcześniej model kosztów.

### <a name="cost-over-time-report"></a>Raport kosztu w czasie

Raport Koszty w czasie przedstawia wyniki alokacji kosztów jako szeregi czasowe. Pozwala to obserwować trendy i wykrywać odstępstwa od normy w danym wdrożeniu. Zasadniczo pokazuje on koszty rozłożone na określony przedział czasu. Raport zawiera główne czynniki kosztów, w tym koszty bieżące oraz jednorazowe opłaty za wystąpienia zarezerwowane poniesione, w wybranym przedziale czasu. Zasady ustawione w usłudze [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) są używane w tym raporcie.

Raport Koszty w czasie umożliwia:

- Zobaczenie zmian w czasie i wpływu zmian z dnia na dzień (lub z okresu na okres)
- Analizowanie kosztów w czasie dla konkretnego wystąpienia
- Określenie przyczyny wzrostu kosztów dla konkretnego wystąpienia

Aby użyć raportu Koszty w czasie:

1. Wybierz zakres dat.
2. Dodaj tagi odpowiednio do potrzeb.
3. Dodaj grupy.
4. Wybierz utworzony wcześniej model kosztów.
5. Wybierz koszty rzeczywiste lub amortyzowane.
6. Zdecyduj, czy reguły alokacji mają zostać zastosowane do wyświetlania widoku pierwotnych danych rozliczeń, czy widoku ponownie obliczonych kosztów.

### <a name="actual-cost-analysis-report"></a>Raport Analiza kosztów rzeczywistych

Raport Analiza kosztów rzeczywistych przedstawia koszty dostawcy bez żadnych modyfikacji. Pokazuje on główne czynniki kosztów, w tym koszty bieżące i jednorazowe opłaty.

Raport może służyć do wyświetlania informacji o kosztach dla subskrypcji. W raporcie subskrypcje platformy Azure są wyświetlane jako **nazwa konta** i **numer konta**. **Połączone konta** przedstawiają subskrypcje usługi AWS. Aby wyświetlić koszty według subskrypcji z podziałem dla każdego konta, w obszarze **Grupy** wybierz typ posiadanej subskrypcji.

Raport Analiza kosztów rzeczywistych umożliwia:

- Analizowanie i monitorowanie pierwotnych kosztów dostawcy poniesionych w określonym przedziale czasu
- Planowanie alertu progu
- Analizowanie niezmodyfikowanych kosztów poniesionych przez konta i jednostki

### <a name="actual-cost-over-time-report"></a>Raport Koszty rzeczywiste w czasie

Raport Koszty rzeczywiste w czasie to standardowy raport analizy kosztów, który rozkłada koszty w zdefiniowanym okresie i ze zdefiniowaną dokładnością. Raport przedstawia wydatki w czasie, aby umożliwić zaobserwowanie trendów i wykrycie odstępstw od normy dotyczących wydatków. Ten raport pokazuje główne czynniki kosztów, w tym koszty bieżące oraz jednorazowe opłaty za wystąpienia zarezerwowane, dla wybranego przedziału czasu.

Raport Koszty rzeczywiste w czasie umożliwia:

- Zobaczenie trendu kosztów w czasie
- Wykrycie odstępstw od normy dotyczących kosztów
- Znalezienie wszystkich pytań dotyczących kosztów związanych z dostawcami chmury

### <a name="amortized-cost-reports"></a>Raporty kosztów amortyzowanych

Ten zestaw raportów kosztów amortyzowanych pokazuje liniowe opłaty za usługi nie oparte na użyciu lub koszty jednorazowych należności rozłożone równomiernie w okresie ich obowiązywania. Opłaty jednorazowe mogą na przykład obejmować:

- Roczne opłaty za pomoc techniczną
- Roczne opłaty za składniki zabezpieczeń
- Opłaty za zakup wystąpień zarezerwowanych
- Niektóre elementy z witryny Azure Marketplace

W pliku rozliczeniowym opłaty jednorazowe charakteryzują się takimi samymi datami rozpoczęcia i zakończenia użycia usługi (sygnatury czasowe są równe). Usługa Cloudyn rozpoznaje je jako jednorazowe opłaty, które są amortyzowane. Inne usługi oparte na użyciu z kosztami użycia na żądanie nie są amortyzowane.

Raporty kosztów amortyzowanych to:

- Analiza kosztów amortyzowanych
- Koszty amortyzowane w czasie

### <a name="cost-analysis-report"></a>Raport analizy kosztów

Raport Analiza kosztów zawiera szczegółowe informacje dotyczące użycia chmury i wydatków w wybranym przedziale czasu. Zasady ustawione w usłudze [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) są używane w raporcie Analiza kosztów.

Jak usługa Cloudyn oblicza ten raport?

Usługa Cloudyn zapewnia, że alokacja zachowuje integralność każdego połączonego konta, przez zastosowanie _koligacji konta_. Koligacja gwarantuje, że do konta, które nie korzysta z określonej usługi, nie są alokowane żadne koszty tej usługi. Koszty naliczane w ramach tego konta pozostają na tym koncie i nie są obliczane przez zasady alokacji. Na przykład może istnieć pięć połączonych kont. Jeśli tylko trzy z nich korzystają z usług magazynu, koszt usług magazynu jest alokowany tylko dla tagów na tych trzech kontach.

Raport Analiza kosztów umożliwia:

- Wyświetlanie zagregowanego widoku całego wdrożenia dla określonego przedziału czasu
- Wyświetlanie kosztów według tagów na podstawie zasad utworzonych w modelu kosztów

### <a name="cost-over-time-report"></a>Raport kosztu w czasie

Raport Koszty w czasie przedstawia wydatki w czasie, co umożliwia zobaczenie trendów i zauważenie odstępstw od normy we wdrożeniu. Zasadniczo pokazuje on koszty rozłożone na określony przedział czasu. Raport zawiera główne czynniki kosztów, w tym koszty bieżące oraz jednorazowe opłaty za wystąpienia zarezerwowane poniesione, w wybranym przedziale czasu. Zasady ustawione w usłudze [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs) są używane w tym raporcie.

Raport Koszty w czasie umożliwia:

- Zobaczenie zmian w czasie i wpływu zmian z dnia na dzień (lub z okresu na okres)
- Analizowanie kosztów w czasie dla konkretnego wystąpienia
- Określenie przyczyny wzrostu kosztów dla konkretnego wystąpienia

### <a name="custom-charges-report"></a>Raport Opłaty niestandardowe

Użytkownicy firmowi i dostawcy usług kryptograficznych często świadczą dodatkowe usługi dla swoich klientów zewnętrznych i wewnętrznych w dodatku do własnego użycia zasobów w chmurze. Możesz zdefiniować opłaty niestandardowe dla usług dodanych lub rabaty, które są dodawane do raportów rozliczeń lub obciążeń zwrotnych klientów w postaci pozycji niestandardowych.

Opłaty za usługi niestandardowe odzwierciedlają usługi, które zwykle nie są umieszczane na rachunku. Utworzone opłaty niestandardowe są następnie wyświetlane w raportach kosztów.

*Opłaty niestandardowe nie są cenami niestandardowymi*. Lista opłat niestandardowych nie zawiera różnych stawek, które mogą być naliczane. Na przykład opłaty rozliczeniowe za usługę AWS są wyświetlane tak, jak zostały naliczone.

Aby utworzyć opłatę niestandardową:

1. W obszarze **Opłaty niestandardowe** kliknij pozycję **Dodaj nową**. Zostanie wyświetlone okno dialogowe _Dodawanie nowej opłaty niestandardowej_.
2. W polu **Nazwa dostawcy** podaj nazwę dostawcy.
3. W polu **Nazwa usługi** podaj typ usługi.
4. W polu **Opis** dodaj opis opłaty niestandardowej.
5. W polu **Typ** wybierz wartość **Procentowo**, a następnie na liście rozwijanej Usługi wybierz usługi do dołączenia jako opłaty niestandardowe do raportów kosztów.
6. W polu **Płatność** wybierz, czy opłata jest jednorazowa, czy cykliczna. Jeśli opłata jest naliczana cyklicznie, wybierz opcję Amortyzowana, jeśli opłata ma być amortyzowana, i podaj liczbę miesięcy.
7. W obszarze **Daty** w polu **Data obowiązywania** podaj datę płatności opłaty, jeśli jest to opłata jednorazowa. Jeśli wybrano opcję Opłata cykliczna, podaj zakres dat, w tym datę początkową i datę końcową, dla opłaty.
8. W polu **Drzewo jednostek** wybierz jednostki, do których ma zostać zastosowana opłata, a następnie wybierz pozycję **Włączone**.

_Po przypisaniu opłat do jednostki użytkownicy nie mogą ich zmienić. Opłaty dodane przez administratora do jednostki nadrzędnej są tylko do odczytu._

Aby wyświetlić opłaty niestandardowe:

Opłaty niestandardowe są wyświetlane w raportach kosztów. Na przykład otwórz raport Analiza kosztów rzeczywistych, a następnie w obszarze **Filtry rozszerzone** wybierz opcję **Autonomiczne**. Następnie wykonaj filtrowanie, aby wyświetlić **opłaty niestandardowe**.

### <a name="cost-allocation-360"></a>Cost Allocation 360

Aby utworzyć niestandardowe modele alokacji kosztów na potrzeby przypisywania kosztów do użytych zasobów chmury, należy użyć usługi Cost Allocation 360. Wiele raportów przedstawia informacje z niestandardowych modeli kosztów utworzonych przy użyciu niestandardowych modeli kosztów. Niektóre raporty są wyświetlane tylko po utworzeniu niestandardowego modelu kosztów z alokacją kosztów.

Więcej informacji o tworzeniu niestandardowych modeli kosztów, zobacz [Samouczek: Zarządzanie kosztami przy użyciu rozwiązania Cloudyn](tutorial-manage-costs.md).

### <a name="cost-vs-budget-over-time-report"></a>Raport Koszty a budżet w czasie

Raport Koszty a budżet w czasie umożliwia porównanie głównych czynników kosztów z budżetem. Przypisany budżet jest wyświetlany w raporcie, aby można było zobaczyć zużycie budżetu (przekroczony/pozostało/w normie) w czasie. Korzystając z pól Pokaż/Ukryj u góry raportu, możesz wybrać opcję wyświetlenia kosztów, budżetu, kosztu skumulowanego i łącznego budżetu.

### <a name="current-month-projected-cost-report"></a>Raport Przewidywane koszty dla bieżącego miesiąca

Raport Przewidywane koszty dla bieżącego miesiąca zawiera szczegółowe informacje na temat podsumowania kosztów dla bieżącego miesiąca. Ten raport wyświetla koszty od początku miesiąca, od poprzedniego miesiąca i łączny przewidywany koszt dla bieżącego miesiąca. Przewidywany koszt dla bieżącego miesiąca jest obliczany jako suma miesięcznych kosztów do dnia bieżącego i kosztów przewidywanych na podstawie kosztów monitorowanych w ciągu ostatnich 30 dni.

Raport Przewidywane koszty dla bieżącego miesiąca umożliwia:

- Przewidywanie miesięcznych kosztów według usługi
- Przewidywanie miesięcznych kosztów według konta

### <a name="annual-projected-cost-report"></a>Raport Przewidywane koszty roczne

Raport Przewidywane koszty roczne umożliwia wyświetlenie przewidywanych rocznych kosztów na podstawie trendów wcześniejszych wydatków. Przedstawia on ogólnie przewidywane koszty dla następnych 12 miesięcy. Przewidywania są wykonywane za pomocą funkcji trendu ekstrapolowanej na następne 12 miesięcy na podstawie kosztów skojarzonych z użyciem w ciągu ostatnich 30 dni.

### <a name="budget-management-settings"></a>Ustawienia zarządzania budżetem

Funkcja zarządzania budżetem umożliwia określenie budżetu dla roku obrachunkowego.

Aby dodać budżet do jednostki:

1. Na stronie Budget Management (Zarządzanie budżetem) w obszarze **Entities** (Jednostki) wybierz jednostkę, w której chcesz utworzyć budżet.
2. W ramach roku budżetowego wybierz rok, w którym chcesz utworzyć budżet.
3. W każdym miesiącu ustaw budżet, a następnie kliknij pozycję **Zapisz**.

Aby zaimportować plik dla budżetu rocznego:

1. W obszarze **Actions** (Akcje) wybierz pozycję **Export** (Eksportuj), aby pobrać pusty szablon CSV, którego można użyć jako podstawy budżetu.
2. Wypełnij plik CSV pozycjami budżetu i zapisz go lokalnie.
3. W obszarze **Actions** (Akcje) wybierz pozycję **Import** (Importuj).
4. Wybierz zapisany plik, a następnie kliknij pozycję **OK**.

Aby wyeksportować ukończony budżet jako plik CSV, w obszarze **Actions** (Akcje) wybierz pozycję **Export** (Eksportuj), aby pobrać plik.

Po zakończeniu budżet jest pokazywany w raportach Analiza kosztów i Koszty a budżet w czasie. Możesz również zaplanować raporty na podstawie progów budżetu.

### <a name="azure-resource-explorer-report"></a>Raport witryny Azure Resource Explorer

Raport witryny Azure Resource Explorer zawiera zbiorczą listę wszystkich zasobów platformy Azure dostępnych w usłudze Cloudyn. Aby efektywnie korzystać z tego raportu, na kontach platformy Azure powinny być włączone metryki rozszerzone. Metryki rozszerzone umożliwiają usłudze Cloudyn dostęp do maszyn wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure](azure-vm-extended-metrics.md).

### <a name="azure-resources-over-time-report"></a>Raport Zasoby platformy Azure w czasie

Raport Zasoby platformy Azure w czasie przedstawia podział wszystkich zasobów uruchomionych w określonym przedziale czasu. Aby efektywnie korzystać z tego raportu, na kontach platformy Azure powinny być włączone metryki rozszerzone. Metryki rozszerzone umożliwiają usłudze Cloudyn dostęp do maszyn wirtualnych platformy Azure. Aby uzyskać więcej informacji, zobacz [Dodawanie rozszerzonych metryk dla maszyn wirtualnych platformy Azure](azure-vm-extended-metrics.md).

### <a name="instance-explorer-report"></a>Raport eksploratora wystąpień

Raport eksploratora wystąpień służy do wyświetlania różnych metryk dla elementów zawartości maszyn wirtualnych. Umożliwia przechodzenie do szczegółów dla określonych wystąpień w celu wyświetlenia informacji takich jak:
- Interwały uruchamiania wystąpień
- Cykl życia w wybranym okresie
- Wykorzystanie procesora
- Dane wejściowe sieci
- Ruch wyjściowy
- Aktywne dyski

Raport eksploratora wystąpień zbiera wszystkie interwały uruchamiania w określonym zakresie dat i agreguje odpowiednio dane. Aby wyświetlić każdy z interwałów uruchamiania w zakresie dat, rozwiń wystąpienie. Koszt każdego wystąpienia jest obliczany dla wybranego zakresu dat na podstawie cenników usług AWS i Azure. Rabaty nie są stosowane. Do raportu można dodać dodatkowe pola za pomocą pól Pokaż/Ukryj.

Raport eksploratora wystąpień umożliwia:

- Obliczenie szacowanego kosztu dla maszyny.
- Utworzenie pełnej listy, zawierającej także zagregowane godziny działania, wszystkich maszyn aktywnych w danym okresie
- Utworzenie listy według dostawcy usług w chmurze lub konta
- Wyświetlenie maszyn utworzonych lub usuniętych w danym okresie
- Wyświetlenie wszystkich aktualnie zatrzymanych maszyn
- Wyświetlenie tagów poszczególnych maszyn

### <a name="instances-over-time-report"></a>Raport Wystąpienia w czasie

Za pomocą raportu Wystąpienia w czasie można zobaczyć maksymalną liczbę aktywnych maszyn w wybranym okresie. Jeśli zdefiniowana dokładność to tydzień lub miesiąc, wyniki to maksymalna liczba maszyn aktywnych w dowolnym dniu danego miesiąca. Wybierz zakres dat, aby określić filtry, które mają być wyświetlane w raporcie.

### <a name="instance-utilization-over-time-report"></a>Raport Wykorzystanie wystąpień w czasie

Ten raport przedstawia podział użycia procesora lub pamięci w czasie dla wszystkich wystąpień.

### <a name="compute-power-cost-over-time-report"></a>Raport Koszty mocy obliczeniowej w czasie

Raport Koszty mocy obliczeniowej w czasie udostępnia podział mocy obliczeniowej w określonym zakresie dat. Mimo że inne raporty pokazują liczbę uruchomionych maszyn lub godzin pracy, ten raport przedstawia godziny pracy rdzeni, jednostek obliczeniowych lub gigabajtów pamięci RAM.

Ten raport umożliwia:

- Sprawdzenie mocy obliczeniowej w określonym zakresie dat
- Wyświetlenie czasów obliczeń na podstawie modeli alokacji kosztów

Ten raport jest połączony z zasadami usługi [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs), dlatego wyniki są wyświetlane na podstawie zdefiniowanych tagów i wybranych zasad kosztów. Jeśli nie ma utworzonych zasad, wyniki nie będą wyświetlane.

### <a name="compute-power-average-cost-over-time-report"></a>Raport Średnie koszty mocy obliczeniowej w czasie

Raport Średnie koszty mocy obliczeniowej w czasie umożliwia zobaczenie nie tylko kosztów dla każdej uruchomionej maszyny. Raport pokazuje średni koszt dla godziny pracy wystąpienia, rdzenia, jednostki obliczeniowej i gigabajta pamięci RAM. Raport zawiera szczegółowe informacje na temat wydajności wdrożenia.

Ten raport jest połączony z zasadami usługi [Cost Allocation 360](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs), dlatego wyniki są wyświetlane na podstawie zdefiniowanych tagów i wybranych zasad kosztów. Jeśli nie ma utworzonych zasad, wyniki nie będą wyświetlane.

### <a name="s3-cost-over-time-report"></a>Raport Koszty usługi S3 w czasie

Raport Koszty usługi S3 w czasie zawiera podział kosztów usługi Amazon Simple Storage Service (S3) na zasobnik w czasie dla określonego przedziału czasu. Ten raport ułatwia znalezienie zasobników, które są głównymi czynnikami kosztów, i pokazuje trendy dotyczące użycia i wydatków związanych z usługą S3.

### <a name="s3-distribution-of-cost-report"></a>Raport Dystrybucja kosztów usługi S3

Za pomocą tego raportu można analizować koszty usługi S3 w ostatnim miesiącu według zasobnika i klasy magazynu. Możesz użyć widoku wykresu kołowego, aby ustawić próg widoczności. Możesz także użyć widoku tabeli, aby wyświetlić sumy częściowe.

### <a name="s3-bucket-properties-report"></a>Raport Właściwości zasobnika usługi S3

Ten raport umożliwia wyświetlenie właściwości zasobnika usługi S3. Możesz użyć widoku wykresu kołowego, aby ustawić próg widoczności. Możesz także użyć widoku tabeli, aby wyświetlić sumy częściowe.

### <a name="rds-instances-over-time-report"></a>Raport Wystąpienia usługi RDS w czasie

Ten raport umożliwia wyświetlenie podziału wszystkich wystąpień usługi Amazon Relational Database Service (RDS) uruchomionych w określonym przedziale czasu.

### <a name="rds-active-instances-report"></a>Raport Aktywne wystąpienia usługi RDS

Ten raport umożliwia analizowanie aktywnych wystąpień usługi RDS. W raporcie rozwiń wiersz pozycji, aby wyświetlić dodatkowe informacje.

### <a name="azure-reserved-instances-report"></a>Raport Wystąpienia zarezerwowane platformy Azure

Raport Wystąpienia zarezerwowane platformy Azure udostępnia pojedynczy widok wszystkich Twoich wystąpień zarezerwowanych platformy Azure. Ten raport wyświetla każdy zakup jako osobny wiersz pozycji. Raport zawiera także szczegółowe informacje o zakupie, takie jak konto zakupu, typ zakupu i typ wystąpienia, pozostała liczba dni i tak dalej. Dane raportu możesz pokazać lub ukryć przy użyciu pól Pokaż/Ukryj.

Raport Wystąpienia zarezerwowane platformy Azure umożliwia wyświetlenie:

- Listy wszystkich rezerwacji według daty zakupu
- Czasu pozostałego do wygaśnięcia wystąpienia zarezerwowanego
- Opłat jednorazowych
- Konta użytego do zakupu wystąpień zarezerwowanych i dat zakupu

### <a name="aws-reserved-instances-report"></a>Raport Wystąpienia zarezerwowane usługi AWS

Raport Wystąpienia zarezerwowane usługi AWS udostępnia pojedynczy widok wszystkich wystąpień zarezerwowanych usługi AWS. Raport przedstawia każdy zakup jako osobny wiersz pozycji i zawiera szczegółowe informacje o zakupie, takie jak konto zakupu, typ zakupu i typ wystąpienia, pozostała liczba dni i tak dalej. Dane raportu możesz pokazać lub ukryć przy użyciu pól Pokaż/Ukryj.

Raport Wystąpienia zarezerwowane usługi AWS umożliwia wyświetlenie:

- Listy wszystkich rezerwacji według daty zakupu
- Czasu pozostałego do wygaśnięcia wystąpienia zarezerwowanego
- Opłat jednorazowych
- Oryginalnych identyfikatorów zakupu (identyfikatorów rezerwacji)
- Konta użytego do zakupu wystąpienia zarezerwowanego wraz z datą zakupu

### <a name="ec2-ri-buying-recommendations-report"></a>Raport Rekomendacje dotyczące zakupu wystąpień zarezerwowanych usługi EC2

Podstawą użycia zasobów w chmurze jest model na żądanie, w którym koszty zasobów są ponoszone tylko wtedy, gdy są one używane. Nie ma zobowiązań z góry — płacisz tylko za to, z czego korzystasz i kiedy korzystasz.

Usługa AWS oferuje alternatywny model cen dla usług Elastic Compute Cloud (EC2) — wystąpienie zarezerwowane. Ten model cen gwarantuje użytkownikom pojemność zawsze, gdy jest ona potrzebna, przez cały czas życia wystąpienia zarezerwowanego. Dla wystąpienia zarezerwowanego są oferowane znaczące rabaty cenowe w porównaniu z cenami na żądanie. Jednak użytkownicy podejmują zobowiązanie z góry dotyczące korzystania z wystąpienia wirtualnego. Zobowiązanie jest powiązane z określoną rodziną, rozmiarem, strefą dostępności i systemem operacyjnym w okresie zobowiązania (jeden rok lub trzy lata). Wystąpienie zarezerwowane umożliwia usłudze AWS efektywne planowanie przyszłej wydajności, a także pozyskanie od klientów zobowiązań do korzystania z usług.

Istnieją trzy opcje płatności dla wystąpień zarezerwowanych, przy czym wszystkie są opłatami z góry:

- Cała suma w dniu 0 z najwyższym rabatem
- Bez płatności z góry — wystąpienie zarezerwowane jest opłacane w miesięcznych ratach w okresie życia wystąpienia zarezerwowanego (najniższy rabat)
- Częściowo z góry — ¼ do ½ ceny jest płatne z góry, a pozostała część w miesięcznych ratach z rabatem mniejszym niż w przypadku płatności całej kwoty z góry, lecz zbliżonym

Usługa Cloudyn oblicza czas pracy każdej maszyny w ciągu ostatnich 30 dni. Usługa Cloudyn rekomenduje zakup wystąpień zarezerwowanych, jeśli bardziej ekonomiczne jest uruchomienie maszyny za pomocą wystąpienia zarezerwowanego przy bieżącym poziomie czasu pracy.

Raport przedstawia uzasadnienie rekomendacji mającej na celu zaoszczędzenie jak największej kwoty w ciągu roku. Rekomendacje sugerują zastąpienie wystąpień na żądanie za pomocą wystąpień zarezerwowanych. Wystąpienie zarezerwowane możesz kupić bezpośrednio z raportu.

Każda karta jest otwierana jako pełny raport. Istotne sekcje na kartach to:

- **Wpływ zakupu wystąpień zarezerwowanych usługi EC2** — ta sekcja zawiera symulację różnic między wystąpieniami zarezerwowanymi i na żądanie. Kliknij pozycję **Powiększ**, aby zobaczyć pełny raport Wpływ zakupu wystąpienia zarezerwowanego usługi EC2 z filtrami już zdefiniowanymi dla rekomendacji. Ten raport przedstawia wpływ wszystkich potencjalnych zakupów wystąpień zarezerwowanych. Możesz dostosować oczekiwany średni czas pracy, aby zobaczyć potencjalne oszczędności w przypadku zakupu wystąpień zarezerwowanych usługi EC2.

- **Analiza oszczędności** — ta sekcja zawiera informacje o potencjalnych oszczędnościach i miesiącu realizacji oszczędności w przypadku zastosowania rekomendacji usługi Cloudyn. Rzeczywiste oszczędności i wartość procentowa oszczędności są wyróżnione kolorem czerwonym.

- **Porównanie typów wystąpień zarezerwowanych usługi EC2** — w tej sekcji podkreślono wartość zwrotu z inwestycji dla wdrożenia rekomendowanego przez usługę Cloudyn, w tym wszystkich odpowiednich opcji. Na potrzeby wyników w tym raporcie przyjęto, że maszyna działa przez 100% czasu. Kliknij pozycję **Powiększ**, aby otworzyć szczegółowy raport.

- **Wystąpienia w czasie** — ta sekcja zawiera podział wszystkich wystąpień skojarzonych z rekomendacją, na żądanie, zarezerwowanych i typu spot. Kliknij pozycję **Powiększ**, aby otworzyć szczegółowy raport.
- **Punkty równowagi** — w tej sekcji przedstawiono tabelę wszystkich możliwych rekomendowanych wdrożeń, zwrot z inwestycji i miesiąc osiągnięcia zwrotu z inwestycji. Kliknij pozycję **Powiększ**, aby otworzyć szczegółowy raport.

### <a name="ec2-reservations-over-time-report"></a>Raport Rezerwacje usługi EC2 w czasie

Raport Rezerwacje usługi EC2 w czasie umożliwia śledzenie stanu użycia zakupionych wystąpień zarezerwowanych usługi EC2. Szczegółowość raportu można ustawić na godzinę, dzień lub tydzień.

Ten raport umożliwia:

- Wyświetlenie używanych i nieużywanych zakupionych rezerwacji
- Przejście do dokładności godzinowej w celu wyświetlenia użycia wystąpienia zarezerwowanego w poszczególnych godzinach

### <a name="savings-over-time-report"></a>Raport Oszczędności w czasie

Raport Oszczędności w czasie umożliwia wyświetlenie oszczędności osiągniętych za pomocą wystąpień zarezerwowanych i typu spot. Raport przedstawia zwrot z inwestycji w czasie wynikający z zakupów wystąpień zarezerwowanych.

Aby wyświetlić oszczędności wynikające z użycia wystąpienia zarezerwowanego, pogrupuj wyniki według pozycji **Model ceny**, a następnie wybierz pozycję **Rezerwacja**. Aby wyświetlić oszczędności związane z użyciem dla określonego konta lub typu wystąpienia, dodaj odpowiednie grupowanie i filtr do konta lub typu wystąpienia.

Aby wyświetlić oszczędności związane z użyciem wystąpienia spot, w pozycji **Model ceny** użyj filtru **Spot**. Domyślny filtr dla tego raportu dotyczy wystąpień zarezerwowanych i typu spot.

### <a name="rds-ri-buying-recommendations-report"></a>Raport Rekomendacje dotyczące zakupu wystąpień zarezerwowanych usługi RDS

Raport Rekomendacje dotyczące zakupu wystąpień zarezerwowanych usługi RDS określa, kiedy należy użyć wystąpień zarezerwowanych usługi RDS zamiast wystąpień na żądanie.

Każda karta jest otwierana jako pełny raport. Istotne sekcje na kartach to:

- **Wpływ zakupu wystąpień zarezerwowanych usługi RDS** — ta sekcja zawiera symulację różnic między wystąpieniami zarezerwowanymi i na żądanie. Kliknij pozycję **Powiększ**, aby zobaczyć pełny raport Wpływ zakupu wystąpień zarezerwowanych usługi RDS z filtrami już zdefiniowanymi dla rekomendacji. Ten raport przedstawia wpływ wszystkich potencjalnych zakupów wystąpień zarezerwowanych.  Można dostosować oczekiwany średni czas pracy i zobaczyć potencjalne oszczędności wynikające z zakupu wystąpień zarezerwowanych.
- **Analiza oszczędności** — ta sekcja zawiera informacje o potencjalnych oszczędnościach i miesiącu realizacji oszczędności w przypadku zastosowania rekomendacji usługi Cloudyn. Rzeczywiste oszczędności i wartość procentowa oszczędności są wyróżnione kolorem czerwonym.

- **Porównanie typów wystąpień zarezerwowanych usługi RDS** — w tej sekcji podkreślono wartość zwrotu z inwestycji dla rekomendowanego wdrożenia, w tym wszystkich odpowiednich opcji. Na potrzeby wyników w tym raporcie przyjęto, że maszyna działa przez 100% czasu. Kliknij pozycję **Powiększ**, aby otworzyć szczegółowy raport dla wybranej maszyny.
- **Wystąpienia w czasie** — ta sekcja zawiera podział wszystkich wystąpień skojarzonych z rekomendacją, na żądanie, zarezerwowanych i typu spot. Kliknij pozycję **Powiększ**, aby otworzyć szczegółowy raport.

- **Punkty równowagi** — w tej sekcji przedstawiono tabelę wszystkich możliwych rekomendowanych wdrożeń, zwrot z inwestycji i miesiąc osiągnięcia zwrotu z inwestycji. Kliknij pozycję **Powiększ**, aby otworzyć szczegółowy raport.

### <a name="rds-reservations-over-time-report"></a>Raport Rezerwacje usługi RDS w czasie

Report Rezerwacje usługi RDS w czasie umożliwia wyświetlenie podziału używanych i nieużywanych rezerwacji w określonym przedziale czasu.

### <a name="reserved-instance-purchase-impact-report"></a>Raport Wpływ zakupu wystąpień zarezerwowanych

Raport Wpływ zakupu wystąpień zarezerwowanych usługi ED2 umożliwia symulowanie kosztów wystąpienia zarezerwowanego w porównaniu z wystąpieniem na żądanie w czasie. Może on ułatwić podejmowanie lepszych decyzji dotyczących zakupów. Dostosuj filtry, takie jak średni czas pracy, okres, platforma i inne, aby podejmować świadome decyzje podczas rozpatrywania zakupów wystąpień zarezerwowanych.

### <a name="cost-effective-sizing-recommendations-report"></a>Raport Rekomendacje wyboru ekonomicznego rozmiaru

Raport Rekomendacje wyboru ekonomicznego rozmiaru udostępnia wyniki dla usługi AWS i platformy Azure. W przypadku użytkowników usługi AWS zakupy wystąpień zarezerwowanych są brane pod uwagę, a wyniki nie obejmują maszyn działających jako wystąpienia zarezerwowane. Ten raport zawiera listę niecałkowicie wykorzystanych wystąpień, które są kandydatami do zmniejszenia rozmiaru. Rekomendacje są oparte na danych użycia i wydajności z ostatnich 30 dni. Każda rekomendacja zawiera listę kandydatów do zmniejszenia rozmiaru, uzasadnienie tej akcji i link umożliwiający wyświetlenie pełnych szczegółów i metryk wydajności wystąpienia. A jeśli odpowiednie rekomendacje zalecają zmianę na typy wystąpienia nowszej generacji.

Nie można pobrać listy identyfikatorów wystąpień, dla których raport zaleca zmniejszenie rozmiaru. Aby pobrać identyfikatory wystąpień, użyj raportu Wszystkie rekomendacje dotyczące określania wielkości.

Rozważmy następujący przykład dla zmniejszania rozmiaru:

Masz sześć uruchomionych wystąpień m3.xlarge. Analiza usługi Cloudyn pokazuje, że wykorzystanie procesora jest niskie dla pięciu z nich. Rozważ zmniejszenie ich rozmiaru.

Pozycja Wpływ na koszty zawiera obliczony wpływ na koszty. W tym przykładzie rozwijając wiersz pozycji, można zobaczyć bieżącą cenę jednego wystąpienia m3.xlarge (Linux/UNIX) — 0,266 USD za godzinę — i jednego wystąpienia m3.large (Linux/UNIX) — 0,133 USD za godzinę. Roczny koszt wynosi 11 651 USD dla pięciu wystąpień m3.xlarge wykorzystanych w 100%. Roczny koszt wynosi 5825 USD dla pięciu wystąpień m3.large wykorzystanych w 100%. Potencjalne oszczędności to 5825 USD.

Aby wyświetlić uzasadnienia zmiany rozmiaru ze względów ekonomicznych, kliknij pozycję + w celu rozwinięcia wiersza. W obszarze **Szczegóły**:

- W sekcji **Uzasadnienie rekomendacji** jest wyświetlone bieżące wdrożenie i liczba wystąpień, dla których jest zalecane zmniejszenie rozmiaru.
- W sekcji **Wpływ na koszty**  są wyświetlone obliczenia użyte do określenia potencjalnych oszczędności.
- Sekcja **Potencjalne roczne oszczędności** zawiera potencjalne roczne oszczędności w przypadku zmniejszenia rozmiarów zgodnie z rekomendacją usługi Cloudyn.

### <a name="all-sizing-recommendations-report"></a>Raport Wszystkie rekomendacje dotyczące określania wielkości

Ten raport zawiera listę niecałkowicie wykorzystanych wystąpień, które są kandydatami do zmniejszenia rozmiaru. Rekomendacje są oparte na danych użycia i wydajności z ostatnich 30 dni. W każdej rekomendacji można wyświetlić pełne szczegóły i metryki wydajności wystąpienia.

Jeśli zakupiono wystąpienia zarezerwowane usługi AWS, ten raport zawiera wyniki dla wszystkich uruchomionych wystąpień, w tym wystąpień uruchomionych jako zarezerwowane.

Raport Wszystkie rekomendacje dotyczące określania wielkości umożliwia:

- Wyświetlenie listy wszystkich wystąpień będących kandydatami do zmniejszenia rozmiaru
- Wyeksportowanie listy raportów zawierającej nazwy i identyfikatory wystąpień

Aby wyświetlić szczegóły rekomendacji dla określonego wystąpienia, kliknij pozycję **+** , aby rozwinąć szczegóły. Sekcja Szczegóły rekomendacji zawiera omówienie rekomendacji.

Sekcja **Tagi** zawiera listę kluczy i wartości tagów dla wybranego wystąpienia. Użyj tagów w okienku po lewej stronie, aby przefiltrować sekcję.

Sekcja **Użycie procesora** zawiera wykorzystanie procesora przez wystąpienie dla każdego dnia ostatniego miesiąca.

Kliknij wykres, aby przejść do szczegółów i otworzyć raport Użycie procesora wystąpień w czasie w celu wyświetlenia podziału dla wystąpień.

- Użyj pól **Pokaż/Ukryj**, aby dodać lub usunąć pola: Sygnatura czasowa, CPU (średnio), CPU (minimalnie), CPU (maksymalnie).
- Użyj pola **Zakres dat**, aby podać datę lub zakres dat i przejść do szczegółów dla określonego identyfikatora wystąpienia.
- Użyj pola **Filtry rozszerzone**, aby pokazać wszystkie identyfikatory wystąpień lub tylko określony
- Kliknij pozycję **Powiększ**, aby otworzyć raport Wykorzystanie procesora

Jeśli wystąpienie nie było monitorowane przez 30 dni, wyświetlane dane są niepełne.

Sekcja **Wykorzystanie pamięci (GB)** zawiera informacje dotyczące użycia pamięci. W przypadku użytkowników usługi AWS metryki pamięci nie są automatycznie dostępne i trzeba je dodać dla wystąpienia za pomocą usługi AWS. Usługa AWS nalicza opłaty za włączenie metryk pamięci dla wystąpień usługi EC2.

W sekcji **Wykorzystanie pamięci (%)** jest wyświetlana procentowa część używanej pamięci.

Sekcja **Wejściowy ruch sieciowy** zawiera migawkę w czasie ruchu sieciowego (średniego i maksymalnego) dla wybranego wystąpienia. Umieść wskaźnik nad liniami, aby zobaczyć datę i maksymalny ruch w danym czasie. Kliknij pozycję **Powiększ**, aby otworzyć raport Wejściowy ruch sieciowy.

Sekcja **Wyjściowy ruch sieciowy** zawiera migawkę wyjściowego ruchu sieciowego dla wybranego wystąpienia. Umieść wskaźnik nad liniami, aby zobaczyć datę i maksymalny ruch w danym czasie. Kliknij pozycję **Powiększ**, aby otworzyć raport Wyjściowy ruch sieciowy.

### <a name="instance-metrics-explorer-report"></a>Raport eksploratora metryk wystąpienia

Raport eksploratora metryk wystąpienia przedstawia metryki wydajności dla wystąpienia obejmujące wiele chmur. Za pomocą tego raportu można wyświetlić wystąpienia, które są nadmiernie lub niecałkowicie wykorzystane, co jest określane na podstawie progów metryk procesora, pamięci i sieci.

Aby wyświetlić wydajność dla wystąpienia obejmującą wiele chmur:

1. W polu **Zakres dat** wybierz zakres dat, dla którego chcesz wyświetlić wydajność.
2. W polu **Tagi** wybierz wszystkie tagi, które chcesz wyświetlić.
3. W polu **Filtry** wybierz filtry, które mają zostać wyświetlone w raporcie.
4. W polu **Filtry rozszerzone** dostosuj progi raportu dla wartości:
    - CPU (średnio)
    - CPU (maksymalnie)
    - Pamięć (średnio)
    - Pamięć (maksymalnie)
5. W polu **Filtry rozszerzone** kliknij pozycję **Pokaż**, a następnie wybierz typ wystąpień do wyświetlenia.

Aby wyświetlić metryki określonego wystąpienia w czasie:

- Przejdź do raportu eksploratora metryk wystąpienia i kliknij pozycję **+** , aby wyświetlić szczegóły.

### <a name="rds-sizing-recommendations-report"></a>Raport Rekomendacje dotyczące określania wielkości dla usługi RDS

Raport Rekomendacje dotyczące określania wielkości dla usługi RDS zawiera rekomendacje dla rozmiarów usługi RDS dotyczące optymalizacji użycia chmury. Zawiera on listę niecałkowicie wykorzystanych wystąpień, które są kandydatami do zmniejszenia rozmiaru. Rekomendacje usługi Cloudyn są oparte na danych użycia i wydajności z ostatnich 30 dni. Rekomendacje można filtrować według nazwy konta, regionu, typu wystąpienia i stanu.

### <a name="sizing-threshold-manager-report"></a>Raport menedżera progów określania rozmiaru

Wbudowane rekomendacje usługi Cloudyn dotyczące rozmiarów są obliczane przy użyciu złożonego algorytmu, aby zapewnić dokładne sugestie dotyczące ustalania rozmiarów. Możesz dostosować wartości progowe dla rekomendacji dotyczących zmniejszania rozmiaru.

Aby ręcznie dopasować progi dla rekomendacji dotyczących określania rozmiaru:

1. W menedżerze progów określania rozmiaru dostosuj następujące progi odpowiednio do potrzeb:
    - Średnie użycie CPU (%)
    - Maksymalne użycie CPU (%)
    - Średnie użycie pamięci (%)
    - Maksymalne użycie pamięci (%)
3. Kliknij pozycję **Zastosuj**, aby zapisać zmiany.
4. Zmiany są stosowane natychmiast dla wszystkich rekomendacji.

Aby przywrócić progi domyślne:

- W menedżerze progów określania rozmiaru kliknij pozycję **Przywróć domyślne**.

### <a name="compute-instance-types-report"></a>Raport Typy wystąpień obliczeniowych

Raport Typy wystąpień umożliwia:

- Wyświetlenie typów wystąpień według usługi, rodziny, nazwy interfejsu API i nazwy
- Wyświetlenie szczegółów takich jak procesor, jednostki ECU, pamięć RAM i sieć

Aby znaleźć konkretne wiersze pozycji, możesz użyć pozycji **Wyszukaj**.

## <a name="next-steps"></a>Następne kroki

- Aby poznać sposoby używania raportów, w tym metody ich dostosowywania, zapisywania i planowania, zobacz [Omówienie raportów dotyczących kosztów](understanding-cost-reports.md).
- Aby dowiedzieć się więcej o pulpitach nawigacyjnych w usłudze Cloudyn i sposobach tworzenia własnych niestandardowych pulpitów nawigacyjnych, zobacz [Wyświetlanie kluczowych metryk kosztów za pomocą pulpitów nawigacyjnych](dashboards.md).
