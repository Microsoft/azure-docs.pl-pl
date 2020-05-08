---
title: Faktury za rejestracje w usłudze Azure Enterprise
description: W tym artykule wyjaśniono, jak zarządzać fakturami za usługę Azure Enterprise i wykonywać związane z nimi działania.
author: bandersmsft
ms.author: banders
ms.date: 05/01/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.reviewer: boalcsva
ms.openlocfilehash: 6f654e0b043cfc9f5a90fb84156269fecfbff5bf
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/01/2020
ms.locfileid: "82691193"
---
# <a name="azure-enterprise-enrollment-invoices"></a>Faktury za rejestracje w usłudze Azure Enterprise

W tym artykule wyjaśniono, jak zarządzać fakturami za usługę Azure Enterprise Agreement (Azure EA) i wykonywać związane z nimi działania. Faktura odzwierciedla naliczone opłaty. Przejrzyj ją pod kątem dokładności. Warto również zapoznać się z innymi zadaniami, które mogą być wymagane w związku z fakturami.

## <a name="change-a-po-number-for-an-overage-invoice"></a>Zmiana numeru zamówienia zakupu na fakturze nadwyżkowej

W witrynie Azure Enterprise Portal automatycznie generowany jest domyślny numer zamówienia zakupu, o ile administrator przedsiębiorstwa nie ustawił tego numeru przed datą wystawienia faktury. Administrator przedsiębiorstwa może zaktualizować numer zamówienia zakupu w ciągu siedmiu dni od otrzymania automatycznej wiadomości e-mail z powiadomieniem o fakturze.

### <a name="to-update-the-azure-services-purchase-order-number"></a>Aby zaktualizować numer zamówienia zakupu usług platformy Azure:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Raport** > **Podsumowanie użycia**.
1. Wybierz pozycję **Edytuj numery zamówień zakupu** w prawym górnym rogu.
1. Wybierz przycisk radiowy **Usługi platformy Azure**.
1. Wybierz pozycję **Okres faktury** z menu rozwijanego zakresów dat.

   Numer zamówienia zakupu można edytować w ciągu siedmiu dni od otrzymania powiadomienia o fakturze, ale przed jej zapłaceniem.
1. Podaj nowy numer zamówienia zakupu w polu **Numer zamówienia zakupu**.
1. Wybierz pozycję **Zapisz**, aby przesłać zmianę.

### <a name="to-update-the-azure-marketplace-purchase-order-number"></a>Aby zaktualizować numer zamówienia zakupu na platformie Azure Marketplace:

1. W witrynie Azure Enterprise Portal wybierz pozycję **Raport** > **Podsumowanie użycia**.
1. Wybierz pozycję **Edytuj numery zamówień zakupu** w prawym górnym rogu.
1. Wybierz przycisk radiowy **Marketplace**.
1. Wybierz pozycję **Okres faktury** z menu rozwijanego zakresów dat.

   Numer zamówienia zakupu można edytować w ciągu siedmiu dni od otrzymania powiadomienia o fakturze, ale przed jej zapłaceniem.
1. Podaj nowy numer zamówienia zakupu w polu **Numer zamówienia zakupu**.
1. Wybierz pozycję **Zapisz**, aby przesłać zmianę.

## <a name="cadence-of-azure-enterprise-billing"></a>Okresowość rozliczeń dla usługi Azure Enterprise

Firma Microsoft corocznie w dniu wejścia rejestracji w życie dokonuje rozliczeń dotyczących zakupów usług platformy Microsoft Azure w ramach zobowiązań. W przypadku użycia przekraczającego kwoty wynikające ze zobowiązań firma Microsoft rozlicza zaległości.

- Opłaty dla zobowiązania są określane na podstawie stawki miesięcznej i rozliczane rocznie z góry.
- Opłaty nadwyżkowe są obliczane w każdym miesiącu i rozliczane jako zaległości na końcu okresu rozliczeniowego.

### <a name="billing-intervals"></a>Interwał rozliczeń

Interwał rozliczeń zależy od wybranego sposobu dokonywania zakupów w ramach zobowiązania. Termin zobowiązania rocznego jest identyczny z jednym z następujących:

- Data rocznicy rejestracji
- Data rozpoczęcia obowiązywania jednorocznego aneksu subskrypcji.

Data otrzymania faktury nadwyżkowej zależy od daty rozpoczęcia rejestracji i konfiguracji:

- **Rejestracje bezpośrednie z datą początkową przed 1 maja 2018 r.** :
  - Klienci z bezpośrednią umową Enterprise Azure (EA) są rozliczani w rocznym okresie rozliczeniowym za usługi platformy Azure (bez usług Azure Marketplace). Okres rozliczeniowy zależy od daty rocznicy, czyli daty wejścia w życie umowy.
  - W przypadku przekroczenia wartości 150% progu zobowiązania pieniężnego, nastąpi automatyczna konwersja na kwartalny okres rozliczeniowy zgodnie z datą rocznicy. Otrzymasz również fakturę za nadwyżkowe korzystanie z usług platformy Azure.
  - Jeśli nie przekroczysz 150% progu zobowiązania pieniężnego, rejestracja pozostanie objęta rocznym okresem rozliczeniowym. Faktura nadwyżkowa zostanie wystawiona na koniec roku zobowiązania.

- **Rejestracje bezpośrednie z datą początkową po 1 maja 2018 r.** :
  - Faktury za użycie platformy Azure i opłaty rozliczane oddzielnie są objęte miesięcznym okresem rozliczeniowym.
  - Opłaty, które nie są objęte zobowiązaniem pieniężnym, są należne jako płatności nadwyżkowe.  

- **Rejestracje pośrednie z datą początkową przed 1 maja 2018 r.** :

  Klienci z pośrednią umową Enterprise Azure (EA) z datą rozpoczęcia wcześniejszą niż 1 maja 2018 r. mają skonfigurowany kwartalny okres rozliczeniowy. Otrzymują oni faktury bezpośrednio od współpracujących z nimi partnerów handlowych (CP, channel partner).  

- **Rejestracje pośrednie z datą początkową po 1 maja 2018 r.** :

  Korzystasz z miesięcznego okresu rozliczeniowego.  

### <a name="increase-your-monetary-commitment"></a>Zwiększanie zobowiązania pieniężnego

Zobowiązanie można zwiększyć w dowolnym momencie. Opłaty zostaną naliczone za liczbę miesięcy pozostałą dla okresu zobowiązania w danym roku. Jeśli na przykład zarejestrujesz się w ramach jednorocznego aneksu subskrypcji i zwiększysz swoje zobowiązanie w ciągu szóstego miesiąca, faktura zostanie wystawiona za pozostałe sześć miesięcy tego okresu. Ilości dla Twojego zobowiązania zostaną wtedy zaktualizowane na ostatnie sześć miesięcy okresu zobowiązania. Te nowe ilości posłużą do ustalenia wszelkich opłat za użycie nadwyżkowe.

### <a name="overage"></a>Nadwyżka

W przypadku nadwyżki opłaty są naliczane za użycie lub rezerwacje, które przekroczyły zobowiązanie w okresie rozliczeniowym. Aby wyświetlić szczegółowe informacje o sposobie obliczenia ilości nadwyżkowych dla poszczególnych pozycji, zapoznaj się z raportem podsumowania użycia lub skontaktuj się z partnerem handlowym.

Dla każdej pozycji na fakturze zobaczysz:

- **Wartość**: łączna opłata
- **Użycie zobowiązania**: kwota zobowiązania użyta do pokrycia opłat
- **Wartość netto**: kwota opłat przekraczających zobowiązanie

Odpowiednie podatki są obliczane tylko od kwoty netto przekroczenia zobowiązania.

Wystawianie faktur nadwyżkowych jest zautomatyzowane. Czas wysyłania powiadomień i faktur zależy od daty zakończenia okresu rozliczeniowego.

- Powiadomienie o nadwyżce jest zwykle wysyłane siedem dni po dacie zakończenia rozliczenia.
- Faktury nadwyżkowe są wysyłane od siedmiu do dziewięciu dni po powiadomieniu.
- W ciągu siedmiu dni między powiadomieniem i zafakturowaniem możesz przejrzeć opłaty i zaktualizować wygenerowane przez system numery zamówień zakupu.

### <a name="azure-marketplace"></a>Azure Marketplace

Od okresu rozliczeniowego przypadającego na kwiecień 2019 r. klienci zaczęli otrzymywać pojedynczą fakturę dla platformy Azure, ponieważ połączyliśmy wszystkie opłaty za platformę Azure i witrynę Azure Marketplace w ramach jednej faktury, która zastępuje dwie oddzielne faktury. Ta zmiana nie ma wpływu na klientów w Australii, Japonii ani Singapurze.

Podczas przejścia do faktury skonsolidowanej otrzymasz częściową fakturę za witrynę Azure Marketplace. Końcowa oddzielna faktura będzie zawierać opłaty związane z witryną Azure Marketplace sprzed daty aktualizacji rozliczeń. Opłaty związane z witryną Azure Marketplace po tej dacie będą widoczne na fakturze za platformę Azure. Po okresie przejściowym zobaczysz wszystkie opłaty za platformę Azure i witrynę Azure Marketplace na skonsolidowanej fakturze.  

## <a name="adjust-billing-frequency"></a>Zmiana częstotliwości fakturowania

Klienci otrzymują faktury w cyklu rocznym, kwartalnym lub miesięcznym. Cykl rozliczeniowy jest określany podczas podpisywania umowy przez klienta. Rozliczanie miesięczne to najkrótszy cykl rozliczeniowy.

- Aby zmienić okres rozliczeniowy z rocznego na kwartalny w przypadku rejestracji bezpośrednich, wymagane jest **zatwierdzenie** przez administratora przedsiębiorstwa. W przypadku rejestracji pośrednich wymagane jest zatwierdzenie przez administratora partnera. Zmiana zacznie obowiązywać po zakończeniu bieżącego kwartału rozliczeniowego.
- Aby zmienić roczny lub kwartalny okres rozliczeniowy na miesięczny, wymagana jest **poprawka** do umowy.  Każda zmiana aktualnego okresu rozliczeniowego dla rejestracji wymaga zatwierdzenia przez administratora przedsiębiorstwa lub przez osobę wskazaną jako „Kontakt odbiorcy faktury”.
- **Prześlij** zatwierdzenie do [pomocy technicznej witryny Azure Enterprise Portal](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c). Wybierz kategorię problemu: **Rozliczenia i fakturowanie**.

Zmiana zacznie obowiązywać po zakończeniu bieżącego kwartału rozliczeniowego.

W przypadku podpisania poprawki M503 można zmienić częstotliwość fakturowania dla dowolnej umowy na miesięczną. 

## <a name="credits-and-adjustments"></a>Środki i korekty

Wszystkie środki i korekty zastosowane do rejestracji są widoczne w sekcji **Raporty** w witrynie [Azure Enterprise Portal](https://ea.azure.com).

Aby wyświetlić środki:

1. W witrynie [Azure Enterprise Portal](https://ea.azure.com) wybierz sekcję **Raporty**.
1. Wybierz pozycję **Podsumowanie użycia**.
1. W prawym górnym rogu zmień widok **M** na **C**.
1. Rozwiń pole korekty w tabeli zobowiązań usług platformy Azure.
1. Są tu podane środki zastosowane do rejestracji i krótkie wyjaśnienie. Przykład: Środki dla umowy dotyczącej poziomu usług.

## <a name="request-an-invoice-copy"></a>Żądanie kopii faktury

Aby zażądać kopii faktury, skontaktuj się z partnerem.

## <a name="overage-offset"></a>Przesunięcie opłat za użycie nadwyżkowe

Aby zastosować zobowiązanie pieniężne do nadwyżek, należy spełnić następujące kryteria:

- Klient ma naliczone, ale nieuregulowane opłaty za nadwyżki, a od daty zakończenia rozliczanej usługi nie minął jeszcze jeden rok.
- Dostępna kwota zobowiązania pieniężnego pokrywa w całości naliczone opłaty, w tym wszystkie poprzednie nieuregulowane faktury za platformę Azure.
- Rozliczeniowy przedział czasu wybrany do uzupełnienia jest całkowicie zamknięty. Rozliczenia są całkowicie zamykane po 5. dniu każdego miesiąca.
- Okres rozliczeniowy wybrany do przesunięcia jest całkowicie zamknięty.
- Rabaty za zobowiązanie dotyczące platformy Azure (ACD) opierają się na rzeczywistej wysokości nowego zobowiązania pieniężnego, od której odejmuje się wszelkie środki przeznaczone na opłacenie poprzedniego użycia. To wymaganie dotyczy tylko naliczonych opłat za użycie nadwyżkowe. To rozwiązanie można zastosować tylko do usług rozliczanych ze zobowiązania pieniężnego — nie dotyczy ono opłat związanych z witryną Azure Marketplace. Opłaty związane z witryną Azure Marketplace są rozliczane osobno.

Aby sfinalizować przesunięcie opłat za użycie nadwyżkowe, klient lub zespół opiekujący się kontem może utworzyć wniosek o pomoc techniczną. Wymagane jest wysłane pocztą e-mail zatwierdzenie od administratora przedsiębiorstwa lub osoby wskazanej jako „Kontakt odbiorcy faktury”.

## <a name="view-price-sheet-information"></a>Wyświetlanie informacji o cenniku

Administratorzy przedsiębiorstwa mogą wyświetlać cennik usług platformy Azure przypisany do ich rejestracji.

Aby wyświetlić bieżący cennik:

1. W witrynie Azure Enterprise Portal wybierz sekcję **Raporty**, a następnie pozycję **Arkusz cen**.
2. Wyświetl arkusz cen lub wybierz pozycję **Pobierz**.

![Przykład przedstawiający informacje o cenniku](./media/ea-portal-enrollment-invoices/ea-create-view-price-sheet-information.png)

Aby pobrać cennik historyczny:

1. W witrynie Azure Enterprise Portal wybierz sekcję **Raporty**, a następnie pozycję **Pobierz zestawienie użycia**.
2. Pobierz arkusz cen.

![Przykład pokazujący, jak pobrać starszy arkusz cen](./media/ea-portal-enrollment-invoices/create-ea-view-price-sheet-download-historical-price-list.png)

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

## <a name="move-usage-data-to-another-enrollment"></a>Przenoszenie danych dotyczących użycia do innej rejestracji

Dane dotyczące użycia są przenoszone tylko wtedy, gdy przeniesienie ma datę wsteczną. Istnieją dwie opcje przenoszenia danych dotyczących użycia z jednej rejestracji do innej:

- Przeniesienie kont między rejestracjami.
- Przeniesienie rejestracji między rejestracjami.

W przypadku każdej z tych opcji należy przesłać [wniosek o pomoc techniczną](https://support.microsoft.com/supportrequestform/cf791efa-485b-95a3-6fad-3daf9cd4027c) do zespołu pomocy technicznej EA. 

## <a name="azure-ea-pricing-overview"></a>Omówienie cennika umowy Azure EA

Ta sekcja zawiera szczegółowe informacje na temat sposobu obliczania użycia. Zawiera też odpowiedzi na wiele często zadawanych pytań dotyczących opłat za różne usługi platformy Azure w ramach umowy Enterprise Agreement, w przypadku których obliczenia są bardziej złożone.

### <a name="price-protection"></a>Ochrona cen

Klienci i partnerzy handlowi mają gwarancję otrzymania cen nie wyższych niż określone w cenniku dla klienta lub obowiązujące w dniu wejścia w życie zakupu na platformie Azure. Cena ta jest określana jako cena bazowa. W przypadku usług wprowadzonych po dokonaniu zakupu na platformie Azure naliczana jest cena obowiązująca z rabatem odpowiedniego poziomu w momencie pierwszego wprowadzenia usługi. Ochrona cen ma zastosowanie w okresie obowiązywania zobowiązania, który może trwać od jednego do trzech lat w zależności od danej umowy Enterprise Agreement.

### <a name="price-changes"></a>Zmiany cen

Firma Microsoft może obniżyć bieżącą cenę w ramach umowy Enterprise Agreement dotyczącą poszczególnych usług platformy Azure w okresie rejestracji. Rozszerzymy te obniżone stawki na istniejących klientów w okresie obowiązywania niższej ceny. Jeśli stawki zostaną później zwiększone, cena dla rejestracji dotycząca usługi nie zostanie podwyższona ponad górny limit ochrony cen zdefiniowany powyżej. Jednak może zostać podwyższona względem poprzedniej, obniżonej ceny. W obu przypadkach firma Microsoft poinformuje klientów i pośrednich partnerów handlowych o nadchodzących zmianach cen za pośrednictwem wiadomości e-mail wysłanych do administratorów przedsiębiorstwa i partnerów skojarzonych z rejestracją.

### <a name="current-effective-pricing"></a>Aktualnie obowiązujące ceny

Klient i partnerzy handlowi mogą wyświetlić bieżące ceny dla rejestracji, logując się w witrynie [Azure Enterprise Portal](https://ea.azure.com/) i przechodząc do strony cennika dla rejestracji. W przypadku zakupu platformy Azure za pośrednictwem jednego z partnerów handlowych należy uzyskać aktualizacje cennika od partnera handlowego, jeśli nie włączył on wyświetlania narzutów cenowych dla rejestracji.

### <a name="introduction-of-new-azure-services"></a>Wprowadzenie nowych usług platformy Azure

Ciągle ulepszamy platformę Azure i okresowo dodajemy nowe usługi, które są wyceniane niezależnie od istniejących usług. Niektóre usługi w wersji zapoznawczej są dostępne automatycznie, a inne wymagają od klienta wykonania akcji za pośrednictwem witryny [Azure Account Portal](https://account.windowsazure.com/PreviewFeatures).

Niektóre usługi są wprowadzane z cenami promocyjnymi, które mogą zostać podwyższone w przyszłości.

Po przejściu usługi z wersji zapoznawczej do ogólnie dostępnej ceny mogą wzrosnąć ze względu na zastosowanie pełnych umów SLA dotyczących wydajności i niezawodności. Taka podwyżka nie jest ograniczona przez ochronę ceny bazowej. Opłaty za użycie tych usług są naliczane według zwiększonej stawki. Aby uniknąć naliczania opłat związanych z tymi nowymi usługami, należy zrezygnować z korzystania z nich.

### <a name="introduction-of-regional-pricing"></a>Wprowadzenie cen regionalnych

Poza wprowadzaniem nowych usług, także model usług okresowo zmienia się z pojedynczego globalnego na bardziej regionalny w miarę wzrostu regionalnego wsparcia dla poszczególnych usług.

Przy pierwszym wprowadzeniu regionów dla usługi ochrona ceny bazowej dotyczy nowych regionów. Jednak dodatkowe regiony wprowadzone później dla tej samej usługi są traktowane jako nowe usługi i oferowane za własne indywidualne stawki niezależnie od ochrony ceny bazowej.

### <a name="enterprise-devtest"></a>Enterprise — tworzenie i testowanie

Administratorzy przedsiębiorstwa mogą umożliwić właścicielom konta tworzenie subskrypcji opartych na ofercie Enterprise — tworzenie i testowanie. Właściciel konta musi skonfigurować subskrypcje Enterprise — tworzenie i testowanie wymagane dla bazowych subskrybentów. Ta konfiguracja umożliwia aktywnym subskrybentom programu Visual Studio uruchamianie obciążeń programistycznych i testowych na platformie Azure przy użyciu specjalnych stawek oferty Enterprise — tworzenie i testowanie. Aby uzyskać więcej informacji, zobacz [Enterprise — tworzenie i testowanie](https://azure.microsoft.com/offers/ms-azr-0148p/).

## <a name="enterprise-agreement-usage-calculations"></a>Obliczenia użycia umowy Enterprise Agreement

Zapoznaj się z [usługami platformy Azure](https://azure.microsoft.com/services/) i [cennikiem platformy Azure](https://azure.microsoft.com/pricing/), aby uzyskać podstawowe informacje o cenach publicznych i jednostkach miary oraz poznać często zadawane pytania i wskazówki dotyczące raportowania użycia dla poszczególnych usług. Więcej informacji na temat obliczeń dotyczących umowy EA można znaleźć w poniższych sekcjach.

### <a name="enterprise-agreement-units-of-measure"></a>Jednostki miary dotyczące umowy Enterprise Agreement

Jednostki miary w przypadku umowy Enterprise Agreement są często inne niż w przypadku innych programów, takich jak Microsoft Online Services Agreement (MOSA). Te różnice oznaczają, że dla pewnej liczby usług jednostka miary jest agregowana w celu zapewnienia znormalizowanych cen. Jednostka miary wyświetlana w widoku Podsumowanie użycia w witrynie Enterprise Portal jest zawsze miarą dla umowy Enterprise. Pełna lista bieżących jednostek miary i konwersji dla każdej usługi jest podana w pliku programu Excel [przyjaznych nazw usług](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx).

### <a name="rounding-rules"></a>Reguły zaokrąglania

Zaokrąglenia w witrynie Azure Enterprise Portal są wykonywane przy użyciu standardowej logiki zaokrąglania bankierskiego, czyli gaussowskiego, zgodnej z normą organizacji IEEE. W jej przypadku wartości połówkowe są zaokrąglane do najbliższej cyfry parzystej. Popularniejsze zaokrąglanie od połowy w górę zawsze zaokrągla wartość połówkową w górę do pełnej cyfry. Ta stosowana w witrynie Azure Enterprise Portal metoda daje dokładniejszą sumę łączną dla grupy w porównaniu ze standardową logiką programu Excel.

W celu zilustrowania: gdy pierwsza odrzucana cyfra to 5 i nie ma po niej cyfr lub są to zera, zaokrągla się do najbliższej parzystej cyfry. Na przykład liczby 2,315 i 2,325 po zaokrągleniu do najbliższej setnej to 2,32.

W poniższej tabeli przedstawiono formuły programu Excel, których można użyć do modelowania reguł witryny Azure Enterprise Portal na potrzeby zaokrąglania i konwersji:

| Scenariusz | Wzór dla logiki bankierskiej |
| --- | --- |
| Użycie zaokrąglania | =MROUND({_source_}, 0.0002) |
| Ceny zaokrąglone (2 miejsca dziesiętne) | =MROUND({_source_}, 0.02) |
| Ceny zaokrąglone (0 miejsc dziesiętnych) | =MROUND({_source_}, 2) |

### <a name="conversion-between-usage-detail-report-and-the-usage-summary-page"></a>Konwersja między raportem Szczegóły użycia a stroną Podsumowanie użycia

W pobranym raporcie danych użycia można sprawdzić nieprzetworzone użycie zasobów podawane z dokładnością do sześciu miejsc dziesiętnych. Jednak dane użycia przedstawiane w witrynie Azure Enterprise Portal są zaokrąglane do czterech miejsc dziesiętnych dla jednostek zobowiązania i do wartości całkowitych dla jednostek nadwyżki. Nieprzetworzone dane użycia są najpierw zaokrąglane do czterech cyfr przed konwersją na jednostki używane w witrynie Azure Enterprise Portal. Następnie przekonwertowane jednostki Enterprise są ponownie zaokrąglane do czterech cyfr. Rzeczywista liczba użytych godzin przed konwersją jest podana tylko w pobranym raporcie danych użycia, a nie w samej witrynie Azure Enterprise Portal.

Przykład: w raporcie Szczegóły użycia zgłoszono 694,533404 godziny rzeczywistego użycia programu SQL Server. Te jednostki są następnie konwertowane na 6,94533404 ze 100 godzin obliczeniowych, które są następnie zaokrąglane do wartości 6,9453 i wyświetlane w witrynie Azure Enterprise Portal.

- W celu określenia rozszerzonej kwoty rozliczenia te wyświetlane jednostki są następnie mnożone przez cenę jednostkową zobowiązania, a wynik jest obcinany do dwóch miejsc dziesiętnych. W przypadku jena japońskiego (JPY) i wona koreańskiego (KRW) rozszerzona kwota jest zaokrąglana do jedności.
- W przypadku nadwyżki rozliczane jednostki są obcinane do sześciu cyfr, a następnie mnożone przez cenę jednostki nadwyżki w celu określenia rozszerzonej kwoty rozliczenia.
- W przypadku rozliczeń dla dostawcy usług zarządzanych (MSP, Managed Service Provider) całe użycie skojarzone z działem oznaczonym jako dostawca usług zarządzanych jest zaokrąglane do jednostek po konwersji na jednostkę miary umowy EA. W wyniku suma tego użycia może być niższa niż łączna suma całego użycia zgłoszonego w witrynie Azure Enterprise Portal. Jest to zależne od tego, czy dostawca usług zarządzanych nadal mieści się w swoim saldzie zobowiązania pieniężnego, czy też jest już objęty nadwyżką.

### <a name="graduated-pricing"></a>Ceny stopniowane

Cennik umowy Enterprise Agreement nie obejmuje obecnie cen stopniowanych (gdzie opłata za jednostkę zmniejsza się w miarę wzrostu użycia). W ramach przechodzenia z programu MOSA z cenami stopniowanymi do umowy Enterprise Agreement ceny są określane proporcjonalnie do warstwy podstawowej usługi po uwzględnieniu wszelkich stosownych rabatów w ramach umowy Enterprise Agreement.

### <a name="partial-hour-billing"></a>Częściowe naliczanie godzinowe

Całe użycie jest rozliczane według minut przekonwertowanych na częściowe godziny w miejsce przyrostów godzinowych. Podane stawki godzinowe Enterprise są stosowane do łącznej liczby godzin i godzin częściowych.

### <a name="average-daily-consumption"></a>Średnie użycie dzienne

Opłaty za niektóre usługi dotyczą użycia miesięcznego, ale użycie jest raportowane codziennie. W tych przypadkach użycie jest oceniane raz dziennie, dzielone przez 31 i sumowane dla liczby dni w danym miesiącu rozliczeniowym. Powoduje to, że stawki nigdy nie są wyższe niż oczekiwane dla każdego miesiąca i nieco niższe dla miesięcy krótszych niż 31 dni.

### <a name="compute-hours-conversion"></a>Konwersja godzin obliczeniowych

Przed 28 stycznia 2016 r. informacje o użyciu maszyn wirtualnych i usług w chmurze opcji A0, A2, A3 i A4 w warstwach Standardowa i Podstawowa były generowane jako minuty mierników maszyn wirtualnych A1. Maszyny wirtualne A0 były zliczane jako ułamki maszyn wirtualnych A1, podczas gdy A2, A3 i A4 były konwertowane na wielokrotności. Było to czasem mylące dla klientów, więc wdrożyliśmy zmianę polegającą na przypisaniu użycia na minutę do dedykowanych mierników dla opcji A0, A2, A3 i A4.

Nowe pomiary zaczęły obowiązywać w okresie między 27 stycznia a 28 stycznia 2016 r. W pobranym pliku CSV z informacjami o użyciu w tym okresie przejściowym znajdują się obie pozycje:

- Wygenerowane użycie wskazane przez miernik A1.
- Wygenerowane użycie wskazane przez nowy, dedykowany miernik odpowiadający rozmiarowi wdrożenia.

| **Rozmiar wdrożenia** | **Użycie wygenerowane jako wielokrotność opcji A1 do 26 stycznia 2016 r.** | **Wygenerowane użycie wskazane przez dedykowany miernik od 27 stycznia 2016 r.** |
| --- | --- | --- |
| A0 | 0,25 godziny opcji A1 | 1 godzina opcji A0 |
| A2 | 2 godziny opcji A1 | 1 godzina opcji A2 |
| A3 | 4 godziny opcji A1 | 1 godzina opcji A3 |
| A4 | 8 godzin opcji A1 | 1 godzina opcji A4 |

### <a name="regions"></a>Regiony

Dla usług, w przypadku których strefa i region mają wpływ na ceny, poniższa tabela przedstawia mapowanie dla obszarów geograficznych i regionów:

| Obszar geograficzny | Regiony transferu danych | Regiony usługi CDN |
| --- | --- | --- |
| Strefa 1 | Europa Północna <br> Europa Zachodnia <br> Zachodnie stany USA <br> Wschodnie stany USA <br> Północno-środkowe stany USA <br> Południowo-środkowe stany USA <br> Wschodnie stany USA 2 <br> Środkowe stany USA | Ameryka Północna <br> Europa |
| Strefa 2 | Wschodni region Azji i Pacyfiku <br> Południowo-Wschodni region Azji i Pacyfiku <br> Japonia Wschodnia <br> Japonia Zachodnia <br> Australia Wschodnia <br> Australia Południowo-Wschodnia | Azja i Pacyfik <br> Japonia <br> Ameryka Łacińska <br> Bliski Wschód/Afryka <br> Australia Wschodnia <br> Australia Południowo-Wschodnia |
| Strefa 3 | Brazylia Południowa |   |

Nie ma opłat za ruch wychodzący między usługami hostowanymi w tym samym centrum danych. Na przykład między usługami Office 365 i platformy Azure.

### <a name="monetary-commitment-and-unbilled-usage"></a>Zobowiązanie pieniężne i nierozliczone opłaty za użycie

Zobowiązanie pieniężne platformy Azure to uiszczana z góry opłata za usługi platformy Azure. Zobowiązanie pieniężne jest wykorzystywane w miarę używania usług. Usługi platformy Azure dostarczane przez firmę Microsoft są rozliczane przy użyciu zobowiązania pieniężnego. Jednak niektóre opłaty są rozliczane oddzielnie, a usługi witryny Azure Marketplace nie zużywają zobowiązania pieniężnego.

### <a name="charges-billed-separately"></a>Opłaty rozliczane oddzielnie

Niektóre produkty i usługi udostępniane ze źródeł innych firm nie używają zobowiązania pieniężnego platformy Azure. Zamiast tego te pozycje są rozliczane osobno w ramach faktury nadwyżkowej dla standardowego okresu rozliczeniowego.

Połączyliśmy wszystkie opłaty za platformę Azure i witrynę Azure Marketplace w pojedynczą fakturę, której okres odpowiada okresowi rozliczeniowemu rejestracji. Połączone faktury nie mają zastosowania do klientów w Australii, Japonii ani Singapurze.

Na połączonej fakturze wykazane jest najpierw użycie platformy Azure, a następnie wszelkie opłaty związane z witryną Azure Marketplace. W przypadku klientów w Australii, Japonii lub Singapurze opłaty związane z witryną Azure Marketplace znajdują się na oddzielnej fakturze.

Jeśli na końcu standardowego okresu rozliczeniowego nie ma użycia nadwyżkowego, opłaty naliczane osobno znajdują się na oddzielnej fakturze. Ten proces ma zastosowanie do klientów w Australii, Japonii i Singapurze.

Następujące usługi są rozliczane oddzielnie:

- Canonical
- Citrix XenApp Essentials
- Citrix XenDesktop Registered User
- Openlogic
- Remote Access Rights XenApp Essentials Registered User
- Ubuntu Advantage
- Visual Studio Enterprise (miesięczny)
- Visual Studio Enterprise (roczny)
- Visual Studio Professional (miesięczny)
- Visual Studio Professional (roczny)

## <a name="what-to-expect-after-change-of-channel-partner"></a>Czego można oczekiwać po zmianie partnera kanału

Jeśli zmiana partnera kanału nastąpi w połowie miesiąca, klient otrzyma fakturę za użycie u poprzedniego powiązanego partnera i kolejną fakturę za użycie u nowego partnera.

Faktury zostaną wystawione po miesiącu, w którym kończy się okres rozliczeniowy. Jeśli rozliczenia są miesięczne, faktura za wrzesień zostanie wystawiona dla obu partnerów w październiku. Jeśli cykl rozliczeniowy jest kwartalny lub roczny, klient może oczekiwać faktury dla poprzedniego powiązanego partnera za użycie w okresie współpracy z nim, a reszta zostanie naliczona dla nowego partnera zgodnie z okresem rozliczeniowym.

## <a name="azure-marketplace-for-ea-customers"></a>Witryna Azure Marketplace dla klientów umowy EA

W przypadku klientów bezpośrednich opłaty związane z witryną Azure Marketplace są widoczne w witrynie Azure Enterprise Portal. Zakupy w witrynie Azure Marketplace i użycie są rozliczane kwartalnie lub miesięcznie poza zobowiązaniem pieniężnym jako zaległości.

Klienci pośredni mogą znaleźć swoje subskrypcje witryny Azure Marketplace na stronie **Zarządzanie subskrypcjami** w witrynie Azure Enterprise Portal, lecz ceny będą ukryte. Aby uzyskać informacje o opłatach związanych z witryną Azure Marketplace, klienci powinni skontaktować się z dostawcą rozwiązań licencjonowania (LSP, Licensing Solutions Provider).

Nowe cykliczne miesięczne lub roczne zakupy w witrynie Azure Marketplace są całkowicie rozliczane w okresie, w którym dokonano zakupów w tej witrynie. Te pozycje zostaną odnowione automatycznie w następnym okresie w dniu oryginalnego zakupu.

Istniejące miesięczne opłaty cykliczne będą w dalszym ciągu odnawiane w pierwszym dniu każdego miesiąca kalendarzowego. Opłaty roczne będą odnawiane w rocznicę daty zakupu.

Niektóre usługi odsprzedawców innych firm dostępne w witrynie Azure Marketplace używają teraz salda zobowiązania pieniężnego w ramach umowy Enterprise Agreement (EA). Wcześniej te usługi były rozliczane poza zobowiązaniem pieniężnym umowy EA i na oddzielnej fakturze. Zobowiązanie pieniężne w ramach umowy EA dla tych usług w witrynie Azure Marketplace upraszcza zarządzanie zakupami i płatnościami klientów. Aby uzyskać pełną listę usług, które obecnie używają zobowiązania pieniężnego, zobacz [aktualizację z 6 marca 2018 r. w witrynie platformy Azure](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment/).

### <a name="partners"></a>Partnerzy

Dostawca LSP może pobrać cennik witryny Azure Marketplace ze strony arkusza cen w witrynie Azure Enterprise Portal. Wybierz link **Cennik witryny Marketplace** w prawym górnym rogu. Cennik witryny Azure Marketplace zawiera wszystkie dostępne usługi i ich ceny.

Ab pobrać cennik:

1. W witrynie Azure Enterprise Portal przejdź do obszaru **Raporty** > **Arkusz cen**.
1. W prawym górnym rogu odszukaj link do cennika witryny Azure Marketplace pod Twoją nazwą użytkownika.
1. Kliknij prawym przyciskiem myszy link i wybierz polecenie **Zapisz element docelowy jako**.
1. W oknie **Zapisywanie** zmień tytuł dokumentu na `AzureMarketplacePricelist.zip`, co spowoduje zmianę pliku xlsx w plik zip.
1. Po zakończeniu pobierania będziesz mieć plik zip z cennikiem dla określonego kraju.
1. Dostawcy LSP powinni odwoływać się do pliku konkretnego kraju w celu określenia cen specyficznych dla kraju. Dostawcy LSP powinni sprawdzać jednostki SKU, które są całkiem nowe lub wycofane, za pomocą karty **Powiadomienia**.
1. Zmiany cen są rzadkie. W przypadku ich wystąpienia dostawcy LSP zostaną powiadomieni o podwyżkach oraz o zmianach kursów wymiany z 30-dniowym wyprzedzeniem za pomocą wiadomości e-mail.
1. Dostawcy LSP dostają jedną fakturę dla każdej kombinacji rejestracji, dostawcy ISV i kwartału.

### <a name="enabling-azure-marketplace-purchases"></a>Włączanie zakupów w witrynie Azure Marketplace

Administratorzy przedsiębiorstwa mogą „wyłączać” lub „włączać” zakupy w witrynie Azure Marketplace dla wszystkich subskrypcji platformy Azure w ramach rejestracji. Jeśli administrator przedsiębiorstwa wyłączy zakupy i istnieją subskrypcje platformy Azure, które już mają subskrypcje witryny Azure Marketplace, to te subskrypcje nie zostaną anulowane i pozostaną bez zmian.

Mimo że klienci mogą konwertować bezpośrednie subskrypcje platformy Azure na umowy Azure EA, kojarząc je ze swoją rejestracją w witrynie Azure Enterprise Portal, ta akcja nie powoduje automatycznej konwersji subskrypcji podrzędnych.

Aby włączyć zakupy w witrynie Azure Marketplace:

1. Zaloguj się w witrynie Azure Enterprise Portal jako administrator przedsiębiorstwa.
1. Przejdź do pozycji **Manage** (Zarządzaj).
1. W obszarze **Szczegóły rejestracji** wybierz ikonę ołówka obok pozycji **Azure Marketplace**.
1. Przełącz odpowiednio opcję **Włączone/Wyłączone** lub **Tylko jednostki SKU Bezpłatna/BYOL**.
1. Wybierz pozycję **Zapisz**.

> [!NOTE]
> Opcja Tylko jednostki SKU Bezpłatna/BYOL powoduje ograniczenie zakupów i nabywania jednostek SKU witryny Azure Marketplace do jednostek SKU bezpłatnych i typu BYOL.

Uzyskaj więcej informacji na ten temat: [Raporty opłat związanych z witryną Azure Marketplace w witrynie Azure Enterprise Portal](#azure-marketplace-charges-in-azure-enterprise-portal-reports).

### <a name="services-billed-hourly-for-azure-ea"></a>Usługi rozliczane godzinowo dla umowy Azure EA

Następujące usługi są rozliczane godzinowo w ramach umowy Enterprise Agreement zamiast według stawki miesięcznej w ramach programu MOSP:

- Application Delivery Network
- Zapora aplikacji internetowej

### <a name="azure-remoteapp"></a>Azure RemoteApp

Jeśli masz umowę Enterprise Agreement, płacisz za usługę Azure RemoteApp na podstawie poziomu cen umowy Enterprise Agreement. Nie są naliczane dodatkowe opłaty. Cena w wersji standardowej obejmuje początkowe 40 godzin. Cena w wersji nieograniczonej obejmuje początkowe 80 godzin. Usługa RemoteApp nie generuje użycia przez 80 godzin.

## <a name="azure-marketplace-faq"></a>Witryna Azure Marketplace — często zadawane pytania

W tej sekcji wyjaśniono, jak zobowiązanie pieniężne platformy Azure może mieć zastosowanie do niektórych usług odsprzedawców zewnętrznych w witrynie Azure Marketplace.

### <a name="what-changed-with-azure-marketplace-services-and-ea-monetary-commitment"></a>Co się zmieniło w związku z usługami witryny Azure Marketplace i zobowiązaniem pieniężnym w ramach umowy EA?

Od 1 marca 2018 niektóre usługi odsprzedawców zewnętrznych wykorzystują zobowiązanie pieniężne w ramach umowy EA. Z wyjątkiem wystąpień zarezerwowanych maszyn wirtualnych platformy Azure (RI, Reserved Instance), wcześniej te usługi były rozliczane poza zobowiązaniem pieniężnym w ramach umowy EA i na oddzielnej fakturze.

Rozszerzyliśmy korzystanie ze zobowiązania pieniężnego pod kątem niektórych często kupowanych usług w witrynie Azure Marketplace publikowanych przez dostawców zewnętrznych. Zobowiązanie pieniężne w ramach umowy EA dla tych usług w witrynie Azure Marketplace upraszcza klientom zarządzanie zakupami i płatnościami.

### <a name="why-did-we-make-this-change"></a>Dlaczego wprowadziliśmy tę zmianę?

Klienci nieustannie szukają dodatkowych sposobów korzystania z płatności z góry wniesionych w postaci zobowiązania pieniężnego. Klienci często prosili o wprowadzenie tej zmiany i ma ona wpływ na znaczną część klientów witryny Azure Marketplace.

### <a name="how-do-you-benefit"></a>Jak na tym zyskasz?

Ta zmiana zapewnia klientom prostsze rozliczenia i możliwość lepszego wykorzystywania zobowiązania pieniężnego w ramach umowy EA. Dzięki temu, że te usługi są objęte opłacanym z góry zobowiązaniem pieniężnym, to zobowiązanie w ramach umowy EA zyskuje na wartości.

### <a name="what-azure-marketplace-services-use-ea-monetary-commitment-and-how-do-i-know"></a>Jakie usługi witryny Azure Marketplace zużywają zobowiązanie pieniężne w ramach umowy EA i jak to ustalić?

Gdy kupujesz usługę, w przypadku której można wykorzystać zobowiązanie pieniężne, w witrynie Azure Marketplace jest prezentowane oświadczenie o zrzeczeniu się odpowiedzialności. Obsługiwane są niektóre usługi publikowane przez firmy Red Hat, SUSE, Autodesk i Oracle. Obecnie za usługi o podobnych nazwach publikowane przez innych dostawców nie można zapłacić, wykorzystując zobowiązanie pieniężne. Pełna lista jest dostępna na końcu sekcji często zadawanych pytań.

### <a name="what-if-my-ea-monetary-commitment-runs-out"></a>Co zrobić, jeśli zobowiązanie pieniężne w ramach umowy EA wyczerpie się?

Jeśli wykorzystasz całe swoje zobowiązanie pieniężne i pojawi się u Ciebie nadwyżka, opłaty związane z tymi usługami pojawią się na następnej fakturze nadwyżkowej wraz z pozostałym użyciem usług. Przed zmianą wprowadzoną 1 marca 2018 r. opłaty te były fakturowane z innymi usługami witryny Azure Marketplace.

### <a name="why-dont-all-azure-marketplaces-consume-ea-monetary-commitment"></a>Dlaczego nie wszystkie usługi witryny Azure Marketplace zużywają zobowiązanie pieniężne w ramach umowy EA?

Często pracujemy nad zapewnieniem dla klientów najlepszego środowiska związanego z zobowiązaniem pieniężnym w ramach umowy EA. Ta zmiana dotyczy dużej liczby klientów i znaczącej części sum wydawanych w witrynie Azure Marketplace. Inne usługi mogą zostać dodane w przyszłości.

### <a name="how-does-this-impact-indirect-enrollment-and-partners"></a>Jak wpływa to na rejestracje pośrednie i partnerów?

Nie ma to wpływu na klientów rejestracji pośredniej ani partnerów. Te usługi są objęte tymi samymi możliwościami zastosowania narzutu przez partnera co inne usługi oparte na użyciu. Jedyną zmianą jest to, że opłaty są uwzględniane w innej fakturze, a opłaty są odejmowane od zobowiązania pieniężnego w ramach umowy EA klienta.

### <a name="is-there-a-list-of-azure-marketplace-services-that-consume-ea-monetary-commitment"></a>Czy istnieje lista usług witryny Azure Marketplace, które zużywają zobowiązanie pieniężne w ramach umowy EA?

Określone oferty z witryny Azure Marketplace mogą zużywać środki zobowiązania pieniężnego. Sprawdź [usługi dostawców zewnętrznych zużywające zobowiązanie pieniężne](https://azure.microsoft.com/updates/azure-marketplace-third-party-reseller-services-now-use-azure-monetary-commitment), aby uzyskać pełną listę produktów biorących udział w tym programie.

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

Tagami zasobów zarządza się w witrynie Azure Portal. Możesz skontaktować się z zespołem subskrypcji platformy Azure w witrynie [Azure Portal](https://portal.azure.com). Wykonaj kroki opisane w artykule [Jak utworzyć żądanie pomocy technicznej dla platformy Azure](https://docs.microsoft.com/azure/azure-portal/supportability/how-to-create-azure-support-request).

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

- Następujące pliki programu Excel zawierają szczegółowe informacje dotyczące usług platformy Azure. Są one aktualizowane szóstego i dwudziestego dnia każdego miesiąca:

   | Tytuł | Opis | Nazwa pliku |
   | --- | --- | --- |
   | [Przyjazne nazwy usług](https://azurepricing.blob.core.windows.net/supplemental/Friendly_Service_Names.xlsx) | Lista wszystkich aktywnych usług. Obejmuje następujące informacje: <br>  <ul><li>Kategoria usługi</li>   <li>Przyjazna nazwa usługi</li>   <li>Nazwa zobowiązania i numer części</li> <li>Nazwa użycia i numer części</li>   <li>Jednostki miary</li>   <li>Współczynniki konwersji między raportowanym użyciem a prezentowanym użyciem witryny Enterprise Portal</li></ul> | Friendly\_Service\_Names.xlsx |
   | [Pola pobierania usługi](https://azurepricing.blob.core.windows.net/supplemental/Service_Download_Fields.xlsx) | Ten arkusz kalkulacyjny zawiera listę wszystkich możliwych kombinacji pól dotyczących usługi w pobieranym raporcie użycia. | Service\_Download\_Fields.xlsx |

- Aby uzyskać informacje na temat faktur i opłat, zobacz [Omówienie rachunku za umowę Azure Enterprise Agreement](../understand/review-enterprise-agreement-bill.md).
- Aby dowiedzieć się, jak rozpocząć korzystanie z witryny Azure Enterprise Portal, zobacz [Wprowadzenie do witryny Azure EA Portal](ea-portal-get-started.md).
