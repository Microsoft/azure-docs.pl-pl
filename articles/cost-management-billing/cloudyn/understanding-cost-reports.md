---
title: Interpretacja raportów dotyczących zarządzania kosztami w usłudze Cloudyn na platformie Azure
description: Ten artykuł ułatwia zapoznanie się z podstawowymi strukturami i funkcjami raportów dotyczących zarządzania kosztami w usłudze Cloudyn.
author: bandersmsft
ms.author: banders
ms.date: 03/12/2020
ms.topic: conceptual
ms.service: cost-management-billing
ms.subservice: cloudyn
ms.reviewer: benshy
ms.custom: seodec18
ROBOTS: NOINDEX
ms.openlocfilehash: 9afe34097d005daeedf46fbaf30b37e53a8e3658
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132044"
---
# <a name="understanding-cloudyn-cost-management-reports"></a>Interpretacja raportów dotyczących zarządzania kosztami w usłudze Cloudyn

Ten artykuł ułatwia zapoznanie się z podstawowymi strukturami i funkcjami raportów dotyczących zarządzania kosztami w usłudze Cloudyn. Większość raportów usługi Cloudyn jest intuicyjna i ma jednolity wygląd i sposób działania. Zapoznanie się z tym artykułem pozwoli Ci używać wszystkich raportów dotyczących zarządzania kosztami. Wiele standardowych funkcji jest dostępnych w różnych raportach, co pozwoli Ci na łatwe nawigowanie po raportach. Możesz dostosowywać raporty i wybierać z kilku opcji obliczania i wyświetlania wyników.

[!INCLUDE [cloudyn-note](../../../includes/cloudyn-note.md)]

## <a name="report-fields-and-options"></a>Pola i opcje raportu

Oto przykład raportu Koszt w czasie. Większość raportów usługi Cloudyn ma podobny układ.

![Przykład raportu Koszt w czasie z ponumerowanymi obszarami odpowiadającymi opisom](./media/understanding-cost-reports/sample-report.png)

Każdy ponumerowany obszar na powyższym obrazie został szczegółowo opisany za pomocą następujących informacji:

1. **Zakres dat**

    Użyj listy Zakres dat, aby zdefiniować interwał czasu raportowania za pomocą ustawień wstępnych lub niestandardowych.
2. **Zapisany filtr**

    Użyj listy Zapisany filtr, aby zapisać bieżące grupy i filtry stosowane względem raportu. Zapisane filtry są dostępne w raportach dotyczących kosztów i wydajności, takich jak:

      - Analiza kosztów
      - Alokacja
      - Zarządzanie zasobami
      - Optymalizacja

   Wpisz nazwę filtru, a następnie kliknij pozycję **Zapisz**.

3. **Tagi**

    Użyj obszaru Tagi, aby przeprowadzić grupowanie według kategorii tagów. Tagi wymienione w menu to dział platformy Azure lub tagi centrum kosztów. Tagi mogą również dotyczyć jednostek kosztów i subskrypcji usługi Cloudyn. Wybierz tagi, aby przefiltrować wyniki. Aby przefiltrować wyniki, możesz również wpisać nazwę tagu (słowo kluczowe).

    ![Przykładowa lista tagów, według których mają być filtrowane wyniki](./media/understanding-cost-reports/select-options.png)

    Kliknij pozycję **Dodaj**, aby dodać nowy filtr.

    ![Dodaj pole filtru zawierające opcje i warunki, według których ma być przeprowadzone filtrowanie](./media/understanding-cost-reports/add-filter.png)

    Grupowanie lub filtrowanie tagów nie odnosi się do tagów zasobów ani grup zasobów platformy Azure.

    Grupowanie i filtrowanie tagów alokacji kosztów jest dostępne w ramach opcji menu **Grupy**.

4. **Grupy w raportach**

    Użyj grup w raportach Analiza kosztów, aby wyświetlić w raporcie standardowe, wyszczególnione kategorie na podstawie danych rozliczeń.  Grupy w raportach Alokacja kosztów zawierają jednak kategorie wyświetlania oparte na tagach. Kategorie oparte na tagach są definiowane w ramach modelu alokacji kosztów i standardowych wyszczególnionych kategoriach na podstawie danych rozliczeń.

    ![Pierwsza przykładowa lista tagów, według których można przeprowadzić grupowanie](./media/understanding-cost-reports/groups-tags01.png)

    ![Druga przykładowa lista tagów, według których można przeprowadzić grupowanie](./media/understanding-cost-reports/groups-tags02.png)

    W raportach Alokacja kosztów grupy w kategoriach grup opartych na tagach mogą zawierać:
      - Tagi
      - Tagi grupy zasobów
      - Tagi jednostek kosztów usługi Cloudyn
      - Kategorie tagów subskrypcji na potrzeby alokacji kosztów

   Przykłady mogą obejmować:
   - Centrum kosztu
   - Dział
   - Aplikacja
   - Środowisko
   - Kod kosztu

     Oto lista wbudowanych grup dostępnych w raportach:

     - **Typ kosztu**
     - Wybierz typ kosztu, wiele typów kosztów lub wybierz wszystko. Typy kosztów obejmują:
       - Opłata jednorazowa
       - Pomoc techniczna
       - Koszt użycia
     - **Klient**
       - Wybierz określonego klienta, wielu klientów lub wszystkich klientów.
     - **Nazwa konta**
       - Nazwa konta lub subskrypcji. Na platformie Azure jest to nazwa subskrypcji platformy Azure.
     - **Nr konta**
       - Wybierz konto, wiele kont lub wszystkie konta. Na platformie Azure jest to identyfikator GUID subskrypcji platformy Azure.
     - **Konto nadrzędne**
       - Wybierz konto nadrzędne, wiele kont lub wybierz wszystko.
     - **Usługa**
       - Wybierz usługę, wiele usług lub wybierz wszystkie usługi.
     - **Dostawca**
       - Dostawca usług w chmurze, dla którego zasoby i wydatki są skojarzone.
     - **Region**
       - Region, w którym zasób jest hostowany.
     - **Strefa dostępności**
       - Izolowane lokalizacje platformy AWS w regionie.
     - **Typ zasobu**
       - Typ używanego zasobu.
     - **Podtyp**
       - Wybierz podtyp.
     - **Operacja**
       - Wybierz operację lub wybierz pozycję **Pokaż wszystko**.
     - **Model cenowy**
       - Wszystkie koszty z góry
       - Brak kosztów z góry
       - Częściowe koszty z góry
       - Na żądanie
       - Rezerwacja
       - Spot
     - **Typ opłaty**
       - Wybierz typ opłaty Ujemna lub Dodatnia (bądź oba).
     - **Dzierżawa**
       - Określa, czy komputer jest uruchomiony jako dedykowany.
     - **Typ użycia**
       - Z typem użycia mogą być związane opłaty jednorazowe lub cykliczne.

5. **Filtry**

    Użyj filtrów pojedynczego lub wielokrotnego wyboru, aby ustawić zakresy na wybrane wartości. Aby ustawić filtr, kliknij pozycję **Dodaj**, a następnie wybierz kategorie i wartości filtrowania.

6. **Model kosztów**

    Użyj modelu kosztów, aby wybrać model kosztów, który został wcześniej utworzony przy użyciu usługi Cost Allocation 360. Możesz mieć wiele modeli kosztów usługi Cloudyn w zależności od wymagań dotyczących alokacji kosztów. Niektóre z Twoich zespołów organizacyjnych mogą mieć wymagania dotyczące alokacji kosztów, które różnią się od wymagań innych zespołów. Każdy zespół może mieć własny dedykowany model kosztów.

    Aby uzyskać informacje na temat tworzenia definicji modelu alokacji kosztów, zobacz [Przydzielanie kosztów za pomocą tagów niestandardowych](tutorial-manage-costs.md#use-custom-tags-to-allocate-costs).

7. **Amortyzacja**

    Użyj raportów Amortyzacja w alokacji kosztów do wyświetlania opłat za usługę nieopartych na użyciu lub kosztów jednorazowych należności i rozłożyć je równomiernie w ich okresie obowiązywania. Opłaty jednorazowe mogą na przykład obejmować:
    - Roczne opłaty za pomoc techniczną
    - Roczne opłaty za składniki zabezpieczeń
    - Opłaty za zakup wystąpień zarezerwowanych
    - Niektóre elementy w witrynie Azure Marketplace.

   W obszarze Amortyzacja wybierz pozycję **Koszt amortyzowany** lub **Koszt rzeczywisty**.

8. **Rozdzielczość**

    Użyj pozycji Rozdzielczość, aby wybrać rozdzielczość czasu w ramach wybranego zakresu dat. Rozdzielczość czasu określa, w jaki sposób jednostki są wyświetlane w raporcie. Może być następująca:
    - Dzienna
    - Tygodniowa
    - Co miesiąc
    - Kwartalna
    - Roczna

9. **Reguły alokacji**

    Użyj pozycji Reguły alokacji, aby zastosować lub wyłączyć ponowne obliczanie alokacji kosztów. Dla danych rozliczeń możesz włączyć lub wyłączyć ponowne obliczanie alokacji kosztów. Ponowne obliczanie dotyczy wybranych kategorii w raporcie. Umożliwia ocenę wpływu ponownego obliczania alokacji kosztów na pierwotne dane rozliczeń.

10. **Bez kategorii**

    Użyj pozycji Bez kategorii, aby uwzględnić lub wykluczyć nieskategoryzowane koszty w raporcie.

11. **Pokaż/ukryj pola**

    Opcja Pokaż/ukryj pola nie ma żadnego wpływu na raporty.

12. **Formaty wyświetlania**

    Użyj pozycji Formaty wyświetlania, aby wybrać różne widoki wykresu lub tabeli.

    ![Symbole formatów wyświetlania, które możesz wybrać](./media/understanding-cost-reports/display-formats.png)

13. **Wiele kolorów**

    Użyj pozycji Wiele kolorów, aby ustawić kolor wykresów w raporcie.

14. **Akcje**

    Użyj pozycji Akcje, aby zapisać, wyeksportować lub zaplanować raport.

15. **Zasady**

    Chociaż nie zostało to przedstawione, niektóre raporty uwzględniają zasady obliczania przewidywanych kosztów. W tych raportach zasady **Skonsolidowane** przedstawiają rekomendacje dotyczące wszystkich kont i subskrypcji w ramach bieżącej jednostki, takiej jak rejestracja firmy Microsoft lub płatnik platformy AWS. Zasady **Autonomiczne** przedstawiają rekomendacje dotyczące jednego konta lub subskrypcji, jak gdyby żadne inne subskrypcje nie istniały. Wybrane zasady różnią się w zależności od strategii optymalizacji używanej przez organizację. Projekcje kosztów są oparte na ostatnich 30 dniach użytkowania.

## <a name="save-and-schedule-reports"></a>Zapisywanie i planowanie raportów

Po utworzeniu raportu możesz go zapisać w celu użycia w przyszłości. Zapisane raporty są dostępne po wybraniu pozycji **Moje narzędzia** > **Moje raporty**. Jeśli wprowadzisz zmiany w istniejącym raporcie i zapiszesz go, raport zostanie zapisany jako nowa wersja. Możesz również zapisać go jako nowy raport.

### <a name="save-a-report-to-the-cloudyn-portal"></a>Zapisywanie raportu w portalu Cloudyn

Podczas przeglądania dowolnego raportu kliknij pozycję **Akcje**, a następnie wybierz pozycję **Zapisz w moich raportach**. Nazwij raport, a następnie dodaj własny adres URL lub użyj automatycznie utworzonego adresu URL. Opcjonalnie możesz **udostępnić** raport publicznie innym osobom w organizacji lub udostępnić go w swojej jednostce. Jeśli raport nie zostanie udostępniony, będzie on raportem osobistym możliwym do wyświetlenia tylko przez Ciebie. Zapisz raport.


### <a name="save-a-report-to-cloud-provider-storage"></a>Zapisywanie raportu w magazynie dostawcy chmury

Aby możliwe było zapisanie raportu u dostawcy usług w chmurze, należy wcześniej skonfigurować konto magazynu. Podczas przeglądania dowolnego raportu kliknij pozycję **Akcje**, a następnie wybierz pozycję **Zaplanuj raport**. Nazwij raport, a następnie dodaj własny adres URL lub użyj automatycznie utworzonego adresu URL. Wybierz pozycję **Zapisz do magazynu**, a następnie wybierz konto magazynu lub dodaj nowe. Wprowadź prefiks, który zostanie dołączony do nazwy pliku raportu. Wybierz format pliku CSV lub JSON, a następnie zapisz raport.

### <a name="schedule-a-report"></a>Planowanie raportu

Raporty możesz uruchamiać w zaplanowanych interwałach i wysyłać je do listy adresatów lub konta magazynu dostawcy usług w chmurze. Podczas przeglądania dowolnego raportu kliknij pozycję **Akcje**, a następnie wybierz pozycję **Zaplanuj raport**. Raport możesz wysłać za pomocą poczty e-mail i zapisać na koncie magazynu. W obszarze **Planowanie** wybierz interwał (codziennie, co tydzień lub co miesiąc). W przypadku interwału tygodniowego i miesięcznego wybierz dzień lub daty dostarczania oraz godzinę. Zapisz zaplanowany raport. W przypadku wybrania formatu raportu programu Excel zostanie on wysłany jako załącznik. W przypadku wybrania formatu zawartości wiadomości e-mail wyniki raportów wyświetlane w formacie wykresu będą dostarczane jako graf.

### <a name="export-a-report-as-a-csv-file"></a>Eksportowanie raportu jako pliku CSV

Podczas przeglądania dowolnego raportu kliknij pozycję **Akcje**, a następnie wybierz pozycję **Eksportuj wszystkie dane raportów**. Zostanie wyświetlone okno podręczne i zostanie pobrany plik CSV.

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się więcej o raportach dostępnych w usłudze Cloudyn, zobacz [Raporty platformy Cloudyn](./use-reports.md).
- Dowiedz się, jak używać raportów do tworzenia [pulpitów nawigacyjnych](./dashboards.md).