---
title: Przegląd rachunku za umowę Enterprise platformy Azure
description: Dowiedz się, jak czytać i rozumieć dane użycia oraz rachunek za umowę Enterprise platformy Azure.
author: bandersmsft
ms.reviewer: amberb
tags: billing
ms.service: cost-management-billing
ms.subservice: enterprise
ms.topic: tutorial
ms.date: 08/20/2020
ms.author: banders
ms.openlocfilehash: d707051e20b4ce9d1289557aec6f8256c2e6bfba
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92150076"
---
# <a name="understand-your-azure-enterprise-agreement-bill"></a>Omówienie rachunku za umowę Enterprise platformy Azure

Klienci mający umowę Enterprise platformy Azure otrzymują fakturę w przypadku przekroczenia dostępnych środków w organizacji lub korzystania z usług, które nie są pokrywane z tych środków.

Środki dostępne w organizacji obejmują jej zobowiązanie pieniężne. Zobowiązanie pieniężne to kwota zapłacona z góry przez organizację na poczet korzystania z usług platformy Azure. Można dodać środki ze zobowiązania pieniężnego do umowy Enterprise, kontaktując się z kierownikiem ds. klientów w firmie Microsoft lub odsprzedawcą.

Ten samouczek dotyczy tylko klientów platformy Azure z umową Enterprise platformy Azure.

Ten samouczek zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przeglądanie zafakturowanych opłat
> * Przeglądanie opłat za nadwyżkowe użycie usług
> * Przeglądanie faktur z witryny Marketplace

## <a name="prerequisites"></a>Wymagania wstępne

Aby przejrzeć i zweryfikować opłaty uwzględnione na fakturze, musisz być administratorem przedsiębiorstwa. Aby uzyskać więcej informacji, zobacz [Omówienie ról administracyjnych dla umowy Azure Enterprise Agreement na platformie Azure](../manage/understand-ea-roles.md). Jeśli nie wiesz, kto jest administratorem przedsiębiorstwa w Twojej organizacji, [skontaktuj się z zespołem pomocy technicznej](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade).

## <a name="review-invoiced-charges-for-most-customers"></a>Przeglądanie zafakturowanych opłat dla większości klientów

Ta sekcja nie dotyczy klientów platformy Azure w Australii, Japonii i Singapurze.

Otrzymasz fakturę za korzystanie z platformy Azure, jeśli w okresie rozliczeniowym nastąpi dowolne z poniższych zdarzeń:

- **Nadwyżkowe użycie usług**: Opłaty za użycie w organizacji przekraczają saldo dostępnych środków.
- **Opłaty rozliczane oddzielnie**: Niektóre usługi używane przez organizację nie są pokrywane z dostępnych środków. Niezależnie od salda środków otrzymasz faktury za następujące usługi:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - Openlogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise (miesięczny)
    - Visual Studio Enterprise (roczny)
    - Visual Studio Professional (miesięczny)
    - Visual Studio Professional (roczny)
- **Opłaty z platformy Marketplace**: Zakup i korzystanie z usług z platformy Azure Marketplace nie są pokrywane ze środków organizacji. Dlatego niezależnie od salda środków otrzymasz faktury za opłaty z platformy Marketplace. W witrynie Enterprise Portal administrator przedsiębiorstwa może włączać i wyłączać zakupy na platformie Marketplace.

Na fakturze zostaną będą najpierw wyświetlane opłaty za użycie platformy Azure wraz ze skojarzonymi z nimi kosztami, a po nich opłaty za usługi witryny Marketplace. Jeśli masz saldo środków, jest ono stosowane do użycia platformy Azure, a na fakturze będzie wyświetlane użycie platformy Azure i użycie witryny Marketplace bez żadnego kosztu.

Możesz porównać łączną kwotę widoczną w obszarze **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z kwotą na fakturze za platformę Azure. Kwoty w raporcie **Podsumowanie użycia** nie zawierają podatku.

Zaloguj się do witryny [Azure EA Portal](https://ea.azure.com). Następnie wybierz pozycję **Raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu przedstawiający opcje M i C w raporcie Podsumowanie użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Suma kwot **Łączne użycie** i **Azure Marketplace** powinna odpowiadać **łącznej wartości** na fakturze. Aby uzyskać więcej informacji na temat opłat, wybierz pozycję **Pobierz zestawienie użycia**.  

![Zrzut ekranu przedstawiający kartę Pobierz zestawienie użycia](./media/review-enterprise-agreement-bill/ea-portal-download-usage.png)

## <a name="review-invoiced-charges-for-other-customers"></a>Przeglądanie zafakturowanych opłat dla pozostałych klientów

Ta sekcja dotyczy tylko klientów platformy Azure w Australii, Japonii lub Singapurze.

Otrzymasz co najmniej jedną fakturę za korzystanie z platformy Azure, jeśli nastąpi dowolne z poniższych zdarzeń:

- **Nadwyżkowe użycie usług**: Opłaty za użycie w organizacji przekraczają saldo dostępnych środków.
- **Opłaty rozliczane oddzielnie**: Niektóre usługi używane przez organizację nie są pokrywane z dostępnych środków. Otrzymasz fakturę za następujące usługi:
    - Canonical
    - Citrix XenApp Essentials
    - Citrix XenDesktop
    - Registered User
    - Openlogic
    - Remote Access Rights XenApp Essentials Registered User
    - Ubuntu Advantage
    - Visual Studio Enterprise (miesięczny)
    - Visual Studio Enterprise (roczny)
    - Visual Studio Professional (miesięczny)
    - Visual Studio Professional (roczny)
- **Opłaty z platformy Marketplace**: Zakup i korzystanie z usług z platformy Azure Marketplace nie są pokrywane ze środków organizacji i są fakturowane osobno. W witrynie Enterprise Portal administrator przedsiębiorstwa może włączać i wyłączać zakupy na platformie Marketplace.

Jeśli w okresie rozliczeniowym zostaną naliczone opłaty za nadwyżkowe użycie usług oraz opłaty rozliczane oddzielnie, otrzymasz jedną fakturę. Będzie ona zawierać oba typy opłat. Opłaty z platformy Marketplace są zawsze fakturowane osobno.

## <a name="review-service-overage-charges-for-other-customers"></a>Przeglądanie opłat za użycie nadwyżkowe usług dla pozostałych klientów

Ta sekcja ma zastosowanie tylko w przypadku klientów w Australii, Japonii lub Singapurze.

Możesz porównać łączną kwotę za użycie widoczną w obszarze **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z kwotą na fakturze za użycie nadwyżkowe. Faktura za użycie nadwyżkowe uwzględnia opłaty za użycie przekraczające dostępne środki organizacji oraz za użycie usług, które nie są pokrywane z dostępnych środków. Kwoty w raporcie **Podsumowanie użycia** nie zawierają podatku.

Zaloguj się do witryny [Azure EA Portal](https://ea.azure.com), a następnie wybierz pozycję **Raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu przedstawiający opcje M i C w raporcie Podsumowanie użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)

Kwota **Łączne użycie** powinna odpowiadać **łącznej wartości** na fakturze za nadwyżkowe użycie usług. Aby uzyskać więcej informacji o opłatach, przejdź do pozycji **Pobierz zestawienie użycia** > **Pobierz raport zaawansowany**. Ten raport nie obejmuje podatków, opłat za rezerwacje ani opłat z platformy Marketplace.  

![Zrzut ekranu przedstawiający pozycję Pobierz raport zaawansowany na karcie Pobierz zestawienie użycia.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-advanced.png)

W poniższej tabeli przedstawiono i wyjaśniono terminy używane na fakturze oraz w raporcie **Podsumowanie użycia** w witrynie Enterprise Portal:

|Termin na fakturze|Termin w raporcie Podsumowanie użycia|Opis|
|---|---|---|
|Łączna wartość|Łączne użycie|Łączna opłata za użycie w danym okresie przed zastosowaniem środków, bez uwzględnienia podatku.|
|Użycie zobowiązania|Użycie zobowiązania|Środki zastosowane w danym okresie.|
|Łączna sprzedaż|Łączna nadwyżka|Łączna opłata za użycie przekraczające kwotę dostępnych środków. Ta kwota nie obejmuje podatku.|
|Kwota podatku|Nie dotyczy|Podatek naliczony od łącznej kwoty sprzedaży w danym okresie.|
|Łączna kwota|Nie dotyczy|Kwota do zapłaty z tytułu faktury, po zastosowaniu środków i uwzględnieniu podatku.|

### <a name="review-marketplace-invoice"></a>Przeglądanie faktur z witryny Marketplace

Ta sekcja ma zastosowanie tylko w przypadku klientów w Australii, Japonii lub Singapurze.

Porównaj sumę opłat z platformy Azure Marketplace w sekcji **Raporty** > **Podsumowanie użycia** w witrynie Enterprise Portal z fakturą dotyczącą platformy Marketplace. Faktura witryny Marketplace dotyczy tylko zakupów w witrynie Azure Marketplace i jej użycia. Kwoty w raporcie **Podsumowanie użycia** obejmują już podatek, który jest określany przez wydawcę.

Zaloguj się do witryny [Enterprise Portal](https://ea.azure.com), a następnie wybierz pozycję **Raporty**. W prawym górnym rogu karty przełącz widok z **M** na **C** i dopasuj okres na fakturze.  

![Zrzut ekranu przedstawiający opcje M i C w raporcie Podsumowanie użycia.](./media/review-enterprise-agreement-bill/ea-portal-usage-sumary-cm-option.png)  

Łączna kwota w polu **Azure Marketplace** powinna odpowiadać **łącznej sprzedaży** na fakturze dotyczącej platformy Marketplace. Aby uzyskać więcej informacji o opłatach za użycie, przejdź do pozycji **Pobierz zestawienie użycia**. W obszarze **Opłaty za korzystanie z witryny Marketplace** wybierz pozycję **Pobierz**. Cena w witrynie Marketplace obejmuje podatek określony przez wydawcę. Klienci nie otrzymają oddzielnej faktury od wydawcy w celu pobrania podatku od transakcji.

![Zrzut ekranu przedstawiający opcję pobierania w obszarze opłat za korzystanie z witryny Marketplace.](./media/review-enterprise-agreement-bill/ea-portal-download-usage-marketplace.png)

## <a name="view-price-sheet-information"></a>Wyświetlanie informacji o cenniku

Administratorzy przedsiębiorstwa mogą wyświetlać cennik usług platformy Azure przypisany do ich rejestracji.

Aby wyświetlić bieżący cennik:

1. W witrynie Azure Enterprise Portal wybierz sekcję **Raporty**, a następnie pozycję **Arkusz cen**.
2. Wyświetl arkusz cen lub wybierz pozycję **Pobierz**.

![Przykład przedstawiający informacje o cenniku](./media/review-enterprise-agreement-bill/ea-create-view-price-sheet-information.png)

Aby pobrać cennik historyczny:

1. W witrynie Azure Enterprise Portal wybierz sekcję **Raporty**, a następnie pozycję **Pobierz zestawienie użycia**.
2. Pobierz arkusz cen.

![Przykład pokazujący, jak pobrać starszy arkusz cen](./media/review-enterprise-agreement-bill/create-ea-view-price-sheet-download-historical-price-list.png)

Niektóre przyczyny różnic w cenach:

- Cennik mógł ulec zmianie między poprzednią rejestracją a nową rejestracją. Zmiany cen mogą następować, ponieważ ceny zależą od umowy związanej z konkretną rejestracją i obowiązują od daty rozpoczęcia umowy do daty jej zakończenia.
- Po przejściu do nowej rejestracji ceny zmieniają się zgodnie z nową umową. Ceny są definiowane w arkuszu cen, którego poziom w przypadku nowej rejestracji może być wyższy.
- Cennik zmienia się też w momencie przedłużenia rejestracji na czas nieokreślony. Zaczyna wówczas obowiązywać cennik płatności zgodnie z rzeczywistym użyciem.

## <a name="request-detailed-usage-information"></a>Żądanie szczegółowych informacji o użyciu

Administratorzy przedsiębiorstwa mogą wyświetlić podsumowanie danych użycia, użytego zobowiązania pieniężnego i opłat związanych z dodatkowym użyciem w witrynie Azure Enterprise Portal. Opłaty są wyświetlane na poziomie podsumowania dla wszystkich kont i subskrypcji.

Aby wyświetlić szczegółowe informacje o użyciu na określonych kontach, pobierz raport Szczegóły użycia, przechodząc do pozycji **Raporty** > **Pobierz zestawienie użycia**.

> [!NOTE]
> Raport ze szczegółami użycia nie zawiera żadnych należnych podatków. Między momentem, w którym wystąpiło użycie, a odzwierciedleniem tego użycia w raporcie może wystąpić opóźnienie do ośmiu godzin.

W przypadku rejestracji pośrednich partner musi włączyć funkcję marży, zanim będzie można wyświetlać informacje związane z kosztami.

## <a name="reports"></a>Raporty

Administratorzy przedsiębiorstwa mogą wyświetlić podsumowanie danych użycia, użytego zobowiązania pieniężnego i opłat związanych z dodatkowym użyciem w witrynie Azure Enterprise Portal. Opłaty są wyświetlane na poziomie podsumowania dla wszystkich kont i subskrypcji.

### <a name="azure-enterprise-reports"></a>Raporty usługi Azure Enterprise

- Podsumowanie i wykresy użycia
- Raport użycia usługi
- Raport Saldo i opłaty
- Raport Szczegóły użycia
- Raport opłat związanych z witryną Azure Marketplace
- Arkusz cen
- Zaawansowane pobieranie raportu
- Formatowanie raportu CSV

### <a name="to-view-the-usage-summary-reports-and-graphs"></a>Aby wyświetlić raporty i wykresy podsumowania użycia:

1. Przejdź do witryny Azure Enterprise Portal.
1. W okienku po lewej stronie wybierz pozycję **Raporty**.
1. Wybierz kartę **Podsumowanie użycia**.
1. Wybierz termin zobowiązania z menu zakresów dat w lewym górnym rogu.
1. Wybierz okres lub miesiąc na wykresie, aby wyświetlić dodatkowe szczegóły.
1. Co można zrobić na tej karcie:
   - Wyświetl wykres użycia „miesiąc do miesiąca” z podziałem na użycie, nadpłatę za usługę, opłaty naliczane osobno i opłaty związane z witryną Azure Marketplace.
   - Filtruj według działów, kont i subskrypcji poniżej wykresu.
   - Przełącz się między podziałami **Opłata według usług** i **Opłata według hierarchii**.
   - Wyświetl szczegóły dotyczące usług platformy Azure, opłat naliczonych osobno i opłat związanych z witryną Azure Marketplace.

## <a name="service-usage-report"></a>Raport użycia usługi

Strona Raport użycia usługi umożliwia administratorowi przedsiębiorstwa wyświetlenie podsumowania danych użycia usługi. Użycie jest wyświetlane na poziomie podsumowania dla wszystkich kont i subskrypcji. Aby wyświetlić szczegółowe użycie, możesz filtrować raport według kont lub subskrypcji.

> [!NOTE]
> Może wystąpić maksymalnie pięciodniowe opóźnienie między datą rzeczywistego użycia a odzwierciedleniem tego użycia w raporcie.

### <a name="to-view-the-report"></a>Aby wyświetlić raport:

1. Zaloguj się w witrynie Azure Enterprise Portal.
1. Wybierz pozycję **Raporty** w obszarze nawigacji po lewej.
1. Wybierz kartę **Podsumowanie użycia**.
1. Wybierz zakres dat.
1. Wybierz konta lub subskrypcje do wyświetlenia.
1. Opcjonalnie:
   - Przełącz widok między **Opłaty według usług** i **Opłaty według hierarchii**, aby wyświetlić różne podziały.
   - Wyświetl szczegółowe informacje, w tym nazwę usługi, jednostkę miary, użyte jednostki, efektywną stawkę i koszt rozszerzony.

## <a name="download-csv-reports"></a>Pobieranie raportów CSV

Administratorzy przedsiębiorstwa mogą używać strony Pobieranie raportu miesięcznego do pobierania kilku raportów jako plików CSV. Dostępne są następujące raporty do pobrania:

- Raport Saldo i opłaty
- Raport Szczegóły użycia
- Raport opłat związanych z witryną Azure Marketplace
- Arkusz cen

### <a name="to-download-reports"></a>Aby pobrać raporty:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Raport**.
1. Wybierz pozycję **Pobierz użycie** na górnej wstążce.
1. Wybierz pozycję **Pobierz** obok raportu za odpowiedni miesiąc.

### <a name="csv-report-formatting-issues"></a>Problemy z formatowaniem raportów CSV

Klienci przeglądający raporty CSV z kwotami w euro w witrynie Azure Enterprise Portal mogą napotkać problemy z formatowaniem w odniesieniu do przecinków i kropek.

Możesz na przykład zobaczyć następujące dane:

| **ServiceResource** | **ResourceQtyConsumed** | **ResourceRate** | **ExtendedCost** |
| --- | --- | --- | --- |
| Godziny | 24 | 0,0535960591133005 | 12,863,054,187,192,100,000,000 |

Powinien zostać wyświetlony następujący ekran:

| ServiceResource | ResourceQtyConsumed | ResourceRate | ExtendedCost |
| --- | --- | --- | --- |
| Godziny | 24 | 0,0535960591133005 | 1,2863054187192120000000 |

Ten problem z formatowaniem występuje z powodu ustawień domyślnych funkcji importowania w programie Excel. Program Excel importuje wszystkie pola jako ogólny tekst i zakłada, że liczby są oddzielone za pomocą zapisu matematycznego. Przykład: „1,000.00”.

Waluta europejska używająca kropki (.) jako separatora tysięcy i przecinka (,) jako separatora miejsc dziesiętnych będzie wyświetlana nieprawidłowo. Przykład: „1.000,00”. Wyniki importowania mogą być zróżnicowane w zależności od ustawień regionalnych dla języka.

### <a name="to-import-the-csv-file-without-formatting-issues"></a>Aby zaimportować plik CSV bez problemów z formatowaniem:

1. W programie Microsoft Excel przejdź do obszaru **Plik** > **Otwórz**.
   Zostanie wyświetlony kreator importowania tekstu.
1. W obszarze **Typ danych źródłowych** wybierz wartość **Rozdzielany**.  Wartość domyślna to **Stała szerokość**.
1. Wybierz opcję **Dalej**.
1. W obszarze Ograniczniki zaznacz pole wyboru **Przecinek**. Jeśli jest wybrana opcja **Tabulator**, wyczyść ją.
1. Wybierz opcję **Dalej**.
1. Przewiń do kolumn **ResourceRate** i **ExtendedCost**.
1. Wybierz kolumnę **ResourceRate**. Zostanie ona wyróżniona jako czarna.
1. W polu **Format danych w kolumnie** wybierz opcję **Tekst** zamiast **Ogólny**. Nagłówek kolumny zmieni się z **Ogólny** na **Tekst**.
1. Powtórz kroki 8 i 9 dla kolumny **Extended Cost**, a następnie wybierz pozycję **Zakończ**.

> [!TIP]
> Jeśli skonfigurowano automatyczne otwieranie plików CSV w programie Excel, musisz zamiast tego użyć funkcji **Otwórz** w programie Excel. W programie Excel przejdź do obszaru **Plik** > **Otwórz**.

## <a name="balance-and-charge-report"></a>Raport Saldo i opłaty

Raport Saldo i opłaty oferuje miesięczne podsumowanie informacji o saldach, nowych zakupach, opłatach związanych z witryną Azure Marketplace, korektach i opłatach za użycie nadwyżkowe.

Wszystkie wiersze w tabeli Zobowiązanie usługi platformy Azure pozostaną bez zmian w ciągu miesiąca. Szczegóły dotyczące wszystkich zakupów i korekt będą wyświetlane w sekcjach **Szczegóły nowych zakupów** i **Szczegóły korekty**.

### <a name="download-the-balance-and-charge-report"></a>Pobieranie raportu Saldo i opłaty

1. Zaloguj się w witrynie Azure Enterprise Portal jako administrator przedsiębiorstwa.
1. W okienku po lewej stronie wybierz pozycję **Raporty**.
1. Wybierz kartę **Pobieranie raportu**.
1. Wybierz odpowiedni miesiąc w kolumnie **Saldo i opłaty** i wybierz opcję pobrania raportu.

## <a name="usage-detail-report"></a>Raport Szczegóły użycia

Raport Szczegóły użycia zawiera miesięczne podsumowanie dotyczące usług i ilości użytych w ramach rejestracji. Zawiera informacje o nazwach mierników, typach mierników i użytych ilościach.

### <a name="download-the-usage-detail-report"></a>Pobieranie raportu Szczegóły użycia

1. Zaloguj się w witrynie Azure Enterprise Portal jako administrator przedsiębiorstwa.
1. Wybierz pozycję **Raporty** w obszarze nawigacji po lewej.
1. Wybierz kartę **Pobierz zestawienie użycia**.
1. Wybierz odpowiedni miesiąc w kolumnie **Szczegóły użycia** i wybierz opcję pobrania raportu.

## <a name="azure-marketplace-charges-in-azure-enterprise-portal-reports"></a>Raporty opłat związanych z witryną Azure Marketplace w witrynie Azure Enterprise Portal

Istnieją dwa typy opłat związanych z witryną Azure Marketplace:

- **Na podstawie użycia:** Produkty mierzone w jednostkach transakcyjnych.  Na przykład opłaty za maszyny wirtualne są naliczane co godzinę, a opłaty za wyszukiwanie za pomocą interfejsu API usługi Bing są naliczane według liczby wyszukiwań.
- **Opłata miesięczna:** Rozliczana miesięcznie na podstawie użycia lub dostępu.

Aby uzyskać więcej informacji na temat opłat związanych z witryną Azure Marketplace, zobacz [Witryna Azure Marketplace — często zadawane pytania](https://azure.microsoft.com/marketplace/faq/).

Aby wyświetlić różne opłaty w witrynie Azure Enterprise Portal:

- **Raport Podsumowanie użycia**: Pokazuje opłaty naliczane na podstawie użycia **oraz** miesięczne opłaty związane z witryną Azure Marketplace.
- **Raport opłat związanych z witryną Marketplace**: Zawiera **tylko** opłaty związane z witryną Azure Marketplace naliczone na podstawie użycia.  Opłaty jednorazowe nie są podawane.

> [!NOTE]
> Witryna Azure Marketplace nie jest dostępna dla rejestracji w ramach umowy MPSA.

## <a name="advanced-report-download"></a>Zaawansowane pobieranie raportu

W przypadku raportowania dla określonych zakresów dat lub kont można użyć funkcji zaawansowanego pobierania raportu. Od 30 sierpnia 2016 r. formatem pliku wyjściowego jest CSV w celu przystosowania do większych zestawów rekordów.

1. W witrynie Azure Enterprise Portal wybierz pozycję **Pobierz raport zaawansowany**.
1. Wybierz pozycję **Odpowiedni zakres dat**.
1. Wybierz pozycję **Odpowiednie konta**.
1. Wybierz pozycję **Żądaj danych użycia**.
1. Wybieraj przycisk **Odśwież** do momentu aktualizacji stanu raportu na **Pobierz**.
1. Pobierz raport.

## <a name="reporting-for-non-enterprise-administrators"></a>Raportowanie dla użytkowników innych niż administratorzy przedsiębiorstwa

Administratorzy przedsiębiorstwa mogą udzielić administratorom działu i właścicielom konta uprawnień do wyświetlania opłat w ramach rejestracji. Właściciele konta z dostępem mogą pobierać raporty w formacie CSV specyficzne dla ich konta i subskrypcji. Mogą również wyświetlać informacje w formie graficznej w sekcji raportowania w witrynie Azure Enterprise Portal.

### <a name="to-enable-access"></a>Aby włączyć dostęp:

 1. Zaloguj się w witrynie Azure Enterprise Portal jako administrator przedsiębiorstwa.
 1. Wybierz pozycję **Zarządzaj** w obszarze nawigacji po lewej.
 1. Wybierz kartę **Rejestracja**.
 1. W sekcji **Szczegóły rejestracji** wybierz ikonę ołówka obok pozycji **Administrator oddziału — wyświetl opłaty** lub **Właściciel konta — wyświetl opłaty**.
 1. Wybierz pozycję **Włączone**.
 1. Wybierz pozycję **Zapisz**.

### <a name="to-view-reports"></a>Aby wyświetlić raporty:

1. Zaloguj się w witrynie Azure Enterprise Portal jako administrator działu lub właściciel konta.
1. Wybierz pozycję **Raporty** w obszarze nawigacji po lewej.
1. Wybierz kartę **Podsumowanie użycia**, aby wyświetlić informacje dotyczące konta i subskrypcji.
1. Wybierz pozycję **Pobieranie użycia**, aby wyświetlić raporty CSV.

Administratorzy działu i właściciele konta nie mogą wyświetlić opłat za pomocą funkcji **Zaawansowane pobieranie raportu**. Kwota kosztów będzie wyświetlana jako 0 USD.

Uprawnienia do wyświetlania opłat przez właściciela konta dotyczą właściciela konta i wszystkich użytkowników, którzy mają uprawnienia do skojarzonych subskrypcji. Jeśli jesteś klientem pośrednim, funkcje kosztów muszą zostać włączone przez partnera handlowego.

## <a name="power-bi-reporting"></a>Raportowanie usługi Power BI

Funkcja raportowania usługi Power BI jest dostępna dla bezpośrednich klientów umowy EA, partnerów i klientów pośrednich, którzy mają dostęp do wyświetlania informacji dotyczących rozliczeń.

### <a name="power-bi-pro"></a>Power BI Pro

Usługa Power BI Pro jest dostępna dla klientów umowy EA. Za pomocą usługi Power BI Pro można generować i udostępniać raporty w celu efektywnego zarządzania danymi kosztów. Udostępnia ona też dodatkowe funkcje współpracy i odświeżania danych. Usługa Power BI Pro zapewnia wyższe limity pojemności danych i przesyłania strumieniowego danych.

<!--We plan to add new cost management features for Azure Enterprise customers.

Current Power BI (free) users who use the Microsoft Azure Consumption Insights content pack can get a 60-day free trial of Power BI Pro. After the trial is over, you can continue using Power BI Pro by adding a license.

To sign up for the free Power BI Pro trial:

1. From the gear icon in Power BI, select **Manage personal storage**.
1. Select **Try Pro for free** on the right.

See [Power BI self-service sign up](https://powerbi.microsoft.com/documentation/powerbi-service-self-service-signup-for-power-bi/#power-bi-pro-60-day-trial) for more information on the Power BI Pro free trial.

### Azure EA Power BI Pro trial terms

- **General purpose**: The extended Power BI Pro for the "Microsoft Azure Enterprise" content pack trial offer (the "Offer") is available to existing qualified users during the term of the Offer, to allow them to access certain insights related to their Azure consumption through the use of a specific Power BI content pack.
- **Eligibility**: Users under an Enterprise Agreement (EA) can participate in the Offer if they have a function related to their organization's Azure billing, service, or cost management.
- **Exclusions**:
  - Users already participating in the Extended Power BI Pro trial will continue to qualify under the pre-existing offer and can't enter into the Azure EA Power BI Pro trial offer.
  - Users participating in the Offer can only use Power BI Pro with the Microsoft Azure Enterprise content pack. Any other use of Power BI Pro is prohibited.
  - Term: The Offer began on June 1, 2017 and ended on May 31, 2018.  Acceptance can occur at any time during the 12-month period, though the offer will terminate on May 31, 2018 for all users regardless of when they accepted the Offer.
  -->

### <a name="to-access-microsoft-azure-consumption-insights"></a>Aby uzyskać dostęp do składnika Microsoft Azure Consumption Insights:

1. Przejdź do pozycji [Microsoft Azure Consumption Insights](https://app.powerbi.com/getdata/services/azureconsumption?cpcode=MicrosoftAzureConsumptionInsights&amp;getDataForceConnect=true&amp;WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Wybierz pozycję **Pobierz teraz**.
1. Podaj numer rejestracji i liczbę miesięcy, a następnie wybierz pozycję **Dalej**.
1. Podaj klucz dostępu interfejsu API, aby nawiązać połączenie. Klucz rejestracji można znaleźć w witrynie [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
1. Wybierz pozycję **Zaloguj**, aby automatycznie rozpocząć proces importowania.
1. Po jego zakończeniu w okienku nawigacji pojawią się nowy pulpit nawigacyjny, raport i model. Wybierz pulpit nawigacyjny, aby wyświetlić zaimportowane dane.

> [!TIP]
>
> - Aby dowiedzieć się, jak wygenerować klucz interfejsu API dla rejestracji, zobacz plik pomocy dotyczący raportów interfejsu API w witrynie [Enterprise Portal](https://ea.azure.com/?WT.mc_id=azurebg_email_Trans_33675_1378_Service_Notice_EA_Customer_Power_BI_EA_Content_Pack_Apr26).
> - Aby uzyskać więcej informacji na temat łączenia usługi Power BI ze użyciem platformy Azure, zobacz [Microsoft Azure Consumption Insights](/power-bi/desktop-connect-azure-cost-management).

### <a name="to-access-the-legacy-power-bi-ea-content-pack"></a>Aby uzyskać dostęp do starszego pakietu zawartości usługi Power BI w ramach umowy EA:

1. Przejdź do [witryny internetowej usługi Power BI](https://app.powerbi.com/getdata/services/azure-enterprise).
1. Zaloguj się za pomocą ważnego konta służbowego.

   Konto służbowe może być takie samo jak użyte w celu uzyskania dostępu do rejestracji za pomocą witryny Azure Enterprise Portal lub inne.
1. Na pulpicie nawigacyjnym usług wybierz pozycję **Microsoft Azure Enterprise**, a następnie wybierz pozycję **Połącz**.
1. Na ekranie **Nawiązywanie połączenia z platformą Azure Enterprise** wypełnij pola:
    - Adres URL środowiska platformy Azure: [https://ea.azure.com](https://ea.azure.com/)
    - Liczba miesięcy: od 1 do 36
    - Numer rejestracji: numer Twojej rejestracji
1. Wybierz opcję **Dalej**.
1. W polu **Klucz uwierzytelniania** podaj klucz interfejsu API.

    Klucz interfejsu API możesz uzyskać w witrynie Azure Enterprise Portal na karcie **Pobierz zestawienie użycia**. Wybierz pozycję **Klucz dostępu interfejsu API**, a następnie wklej ten klucz w polu **Klucz konta**.
1. Załadowanie danych do usługi Power BI trwa około 5-30 minut w zależności od rozmiaru zestawów danych.

## <a name="reports-faq"></a>Raporty — często zadawane pytania

Ta sekcja zawiera typowe pytania dotyczące raportów.

### <a name="why-is-my-cost-showing-as-0"></a>Dlaczego mój koszt jest wyświetlany jako 0 USD?

W przypadku klientów z **rejestracjami bezpośrednimi** administratorzy przedsiębiorstwa mogą zapewnić właścicielom i kont i administratorom działu dostęp do informacji o kosztach/cenach w raportach użycia. Wykonaj następujące kroki:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Zarządzanie** w obszarze nawigacji po lewej stronie.
1. Wybierz symbol niebieskiego ołówka obok opłat w widoku administratora działu.
1. Wybierz pozycję **Włączono** i zapisz.
1. Wybierz symbol niebieskiego ołówka obok opłat w widoku właściciela konta.
1. Wybierz pozycję **Włączono** i zapisz.

> [!NOTE]
> Jeśli jesteś właścicielem konta lub administratorem działu, skontaktuj się z administratorem przedsiębiorstwa, aby włączyć funkcję cennika.

W przypadku klientów z **rejestracjami pośrednimi** należy sprawdzić u partnera, czy włączył on dla klienta funkcję cennika. Może to zrobić tylko partner. Po włączeniu tej funkcji można wyświetlać koszt i ceny w rejestracji za pomocą konta administratora przedsiębiorstwa.

Partnerzy chcący włączyć funkcję wyświetlania opłat dla właściciela konta lub administratora działu powinni wykonać kroki wymienione w **części dotyczącej rejestracji bezpośredniej**.

### <a name="why-is-there-no-sku-information-on-my-usage-detail-report"></a>Dlaczego w raporcie Szczegóły użycia nie ma informacji o jednostkach SKU?

Raport Szczegóły użycia nie zawiera informacji o jednostkach SKU. Raport ten zawiera jednak informacje o użyciu, dzięki czemu można pobrać raport z arkuszem cen, aby uzyskać informacje o jednostkach SKU.

### <a name="why-doesnt-the-total-amount-on-azure-marketplace-match-the-reports-for-usage-summary-and-detail"></a>Dlaczego łączna kwota dla witryny Azure Marketplace jest niezgodna z raportami z podsumowaniem i szczegółami użycia?

Raport opłat związanych z witryną Azure Marketplace zawiera tylko opłaty naliczone na podstawie użycia. Opłaty jednorazowe nie są podawane. Aby uzyskać najaktualniejsze informacje o opłatach na podstawie użycia i jednorazowych, zapoznaj się ze stroną Podsumowanie użycia.

### <a name="why-is-there-no-information-on-my-api-report"></a>Dlaczego raport interfejsu API nie zawiera informacji?

Klucze interfejsu API wygasają co sześć miesięcy. Jeśli wystąpi problem, administrator przedsiębiorstwa powinien wygenerować nowy klucz interfejsu API. Pamiętaj, aby postępować zgodnie z instrukcjami podanymi w często zadawanych pytaniach dotyczących raportów interfejsu API.

### <a name="why-isnt-my-power-bi-report-working"></a>Dlaczego raport usługi Power BI nie działa?

W przypadku problemów dotyczących usługi Power BI zarejestruj bilet skierowany do [zespołu pomocy technicznej usługi Power BI](https://support.powerbi.com).

### <a name="why-dont-my-resource-tags-show-on-my-reports"></a>Dlaczego w raportach nie ma tagów zasobów?

Tagami zasobów zarządza się w witrynie Azure Portal. Możesz skontaktować się z zespołem subskrypcji platformy Azure w witrynie [Azure Portal](https://portal.azure.com). Wykonaj kroki opisane w artykule [Jak utworzyć żądanie pomocy technicznej dla platformy Azure](../../azure-portal/supportability/how-to-create-azure-support-request.md).

### <a name="why-does-my-resource-rate-change-every-day"></a>Dlaczego moja stawka zasobu zmienia się codziennie?

Stawka zasobu podana w szczegółowym raporcie użycia jest wartością obliczaną. Reprezentuje średnią miesięczną stawkę naliczaną dla usługi. Ta stawka zasobu jest obliczana za pomocą średniej miesięcznego zobowiązania i miesięcznych opłat nadwyżkowych za jednostkę usługi. Część użycia naliczona dla zobowiązania i stawki nadwyżkowe zmieniają się do dnia zamknięcia miesiąca. W związku z tym podana stawka zasobu także zmienia się w ciągu miesiąca. Stawka zasobu jest blokowana piątego dnia po końcu miesiąca.

### <a name="glossary-of-processes-for-calculating-the-resource-rate"></a>Słownik procesów obliczania stawki zasobu

- **Łączna liczba NIEPRZETWORZONYCH jednostek:** Użyta ilość w szczegółowym raporcie użycia.
- **Zasobów MOCP na jednostkę:** Nadrzędny system użycia generuje użycie dla każdej usługi w innych jednostkach. (Ustawienie wstępne)
- **Użycie na jednostkę:** Jednostka miary usługi Azure Enterprise. (Ustawienie wstępne)
- **Cena:** Cena jednostkowa z witryny Azure Enterprise Portal.
- **Łączny koszt:** Rozszerzony koszt ze szczegółowego raportu użycia lub użycia zobowiązania i nadwyżki z witryny Azure Enterprise Portal.

### <a name="charges-calculations"></a>Obliczanie opłat

- **Konwersja na zużyte zasoby MOCP** = `ROUND(Total RAW Units * MOCP Resource Per Unit,4)`
- **Konwersja na zużyte jednostki** = `Consumed MOCP Resources / Consumption per Unit`
- **Obliczenie łącznego kosztu** = `Consumed Units * Price`

### <a name="logic-in-the-usage-calculation-logic"></a>Logika obliczeń użycia

**Stawka zasobu** = `Total Cost /(Total RAW Units / MOCP Resource Per Unit)`

Stawka zasobu jest określana na podstawie opłat. Może być niezgodna z rzeczywistą ceną jednostkową w arkuszu cen.

W pobranym raporcie danych użycia można sprawdzić nieprzetworzone użycie zasobów podawane z dokładnością do sześciu miejsc dziesiętnych. Te dane są używane do obliczania opłat nadwyżkowych. Jednak dane użycia przedstawiane w witrynie Azure Enterprise Portal są zaokrąglane do czterech miejsc dziesiętnych dla jednostek zobowiązania i do wartości całkowitych dla jednostek nadwyżki. W witrynie Azure Enterprise Portal całe użycie nadwyżkowe jest naliczane tylko za pełne jednostki. Może występować znaczna różnica między ceną jednostkową a stawką zasobu za użycie naliczaną jako nadwyżka lub w mieszanych miesiącach.

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Przeglądanie zafakturowanych opłat
> * Przeglądanie opłat za nadwyżkowe użycie usług
> * Przeglądanie faktur z witryny Marketplace

Przejdź do następnego artykułu, aby dowiedzieć się więcej na temat korzystania z witryny Azure EA Portal.

> [!div class="nextstepaction"]
> [Wprowadzenie do witryny Azure EA Portal](../manage/ea-portal-get-started.md)