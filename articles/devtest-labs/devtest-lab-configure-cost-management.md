---
title: Wyświetl szacunkowy miesięczny trend kosztów laboratorium w Azure DevTest Labs
description: Ten artykuł zawiera informacje na temat sposobu śledzenia kosztów laboratorium (miesięcznego szacunku trendu kosztu) w Azure DevTest Labs.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 220cbd1bc793e4bcfdf03a184d7463114506e6e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87283830"
---
# <a name="track-costs-associated-with-a-lab-in-azure-devtest-labs"></a>Śledź koszty związane z laboratorium w Azure DevTest Labs
Ten artykuł zawiera informacje dotyczące śledzenia kosztów laboratorium. Przedstawiono w nim sposób wyświetlania szacowanego trendu kosztu dla bieżącego miesiąca kalendarzowego dla laboratorium. W tym artykule przedstawiono również sposób wyświetlania kosztu miesiąca na zasób w laboratorium.

## <a name="view-the-monthly-estimated-lab-cost-trend"></a>Wyświetlanie trendu szacowanego miesięcznego kosztu laboratorium 
W tej sekcji dowiesz się, jak używać wykresu **trendu miesięcznego szacunku kosztów** , aby wyświetlić szacowany koszt bieżącego miesiąca kalendarzowego i przewidywany koszt końca miesiąca dla bieżącego miesiąca kalendarzowego. Dowiesz się również, jak zarządzać kosztami laboratorium, ustawiając cele wydatków i progi, które po osiągnięciu tej procedury wyzwalają DevTest Labs, aby raportować wyniki.

Aby wyświetlić wykres trendu szacowanych miesięcznych kosztów, wykonaj następujące kroki: 

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz laboratorium.  
4. W menu po lewej stronie wybierz pozycję **Konfiguracja i zasady** .  
4. Wybierz pozycję **trend kosztu** w sekcji **Śledzenie kosztów** w menu po lewej stronie. Poniższy zrzut ekranu przedstawia przykład wykresu kosztów. 
   
    ![Wykres kosztów](./media/devtest-lab-configure-cost-management/graph.png)

    **Szacowana wartość kosztu** jest szacowanym kosztem bieżącego miesiąca kalendarzowego. **Przewidywany koszt** jest szacowanym kosztem całego miesiąca kalendarzowego, obliczonym przy użyciu kosztu Lab dla ostatnich pięciu dni.

    Kwoty kosztów są zaokrąglane w górę do najbliższej liczby całkowitej. Na przykład: 

   * 5,01 zaokrągla do 6 
   * 5,50 zaokrągla do 6
   * 5,99 zaokrągla do 6

     W miarę jak Stany it powyżej wykresu koszty, które są wyświetlane domyślnie na wykresie, są *szacowane* kosztami przy użyciu stawek oferty [płatność zgodnie z rzeczywistym](https://azure.microsoft.com/offers/ms-azr-0003p/) użyciem. Możesz również ustawić własne cele wydatków, które są wyświetlane na wykresach, [zarządzając docelowymi kosztami dla laboratorium.](#managing-cost-targets-for-your-lab)

     W obliczeniach kosztów *nie* są uwzględniane następujące koszty:

   * Subskrypcje CSP i DreamSpark nie są obecnie obsługiwane, ponieważ Azure DevTest Labs używają [interfejsów API rozliczeń platformy Azure](../cost-management-billing/manage/usage-rate-card-overview.md) do obliczania kosztów laboratorium, które nie obsługują subskrypcji CSP ani DreamSpark.
   * Twoje stawki oferty. Obecnie nie można używać stawek oferty (pokazanych w ramach subskrypcji) wynegocjowanych przez firmę Microsoft lub partnerów firmy Microsoft. Używane są tylko stawki płatność zgodnie z rzeczywistym użyciem.
   * Twoje podatki
   * Twoje zniżki
   * Twoja waluta rozliczeniowa. Obecnie koszt laboratorium jest wyświetlany tylko w walucie USD.

### <a name="managing-cost-targets-for-your-lab"></a>Zarządzanie celami kosztów dla laboratorium
Usługa DevTest Labs umożliwia lepsze zarządzanie kosztami w laboratorium przez ustawienie celu wydatków, który można następnie wyświetlić na wykresie miesięcznego trendu szacowanych kosztów. DevTest Labs może również wysyłać powiadomienie, gdy zostanie osiągnięty określony cel wydatków lub progu. 

1. Na stronie **trend kosztu** wybierz pozycję **Zarządzaj miejscem docelowym**.

    ![Przycisk Zarządzaj elementem docelowym](./media/devtest-lab-configure-cost-management/cost-trend-manage-target.png)
2. Na stronie **Zarządzanie celem** Określ cel i progi wydatków. Można również określić, czy każdy wybrany próg jest raportowany na wykresie trendu kosztów, czy za pomocą powiadomienia elementu webhook.

    ![Zarządzaj okienkiem docelowym](./media/devtest-lab-configure-cost-management/cost-trend-manage-target-pane.png)

   - Wybierz przedział czasu, w którym chcesz śledzić cele kosztów.
      - **Co**miesiąc: wartości docelowe kosztów są śledzone miesięcznie.
      - **Naprawiono**: cele kosztów są śledzone dla zakresu dat określonego w datach rozpoczęcia i zakończenia. Zazwyczaj te wartości przedstawiają czas, przez jaki projekt jest zaplanowany do uruchomienia.
   - Określ **koszt docelowy**. Na przykład ilość planowanej pracy w tym laboratorium w zdefiniowanym okresie.
   - Wybierz, aby włączyć lub wyłączyć wszystkie zgłoszone progi — w przyrostach wynoszących 25% — do 125% określonego **kosztu docelowego**.
      - **Powiadom**: po spełnieniu tego progu otrzymasz powiadomienie przez określony adres URL elementu webhook.
      - Wykres **słupkowy**: po spełnieniu tego progu wyniki są wykreślane na wykresie trendu kosztów, który można wyświetlić, zgodnie z opisem w temacie Przeglądanie wykresu trendu miesięcznego szacowanego kosztu.
   - Jeśli zdecydujesz się na **powiadomienie** po spełnieniu progu, musisz określić adres URL elementu webhook. W obszarze integracje kosztów wybierz **pozycję kliknij tutaj, aby dodać integrację**. Wprowadź **adres URL elementu webhook** w okienku Konfigurowanie powiadomienia, a następnie wybierz przycisk **OK**.

       ![Konfigurowanie okienka powiadomień](./media/devtest-lab-configure-cost-management/configure-notification-new.png)

     - W przypadku określenia **powiadomienia**należy zdefiniować adres URL elementu webhook.
     - Analogicznie, jeśli zdefiniujesz adres URL elementu webhook, w okienku próg kosztu **należy ustawić wartość** **włączone** .
     - Musisz utworzyć element webhook przed wprowadzeniem go w tym miejscu.  

       Aby uzyskać więcej informacji na temat elementów webhook, zobacz [Tworzenie elementu webhook lub interfejsu API platformy Azure](../azure-functions/functions-bindings-http-webhook.md). 

## <a name="view-cost-by-resource"></a>Wyświetl koszt według zasobu 
Funkcja trendu kosztu miesięcznego w laboratoriach pozwala zobaczyć, jak dużo spędzasz w bieżącym miesiącu kalendarzowym. Przedstawia także rzutowanie wydatków do końca miesiąca, na podstawie wydatków w ciągu ostatnich siedmiu dni. Aby ułatwić zrozumienie, dlaczego wydatki w laboratorium są wczesne progi spotkań, można użyć funkcji **Koszt według zasobów** , która pokazuje, jaki jest miesięczny koszt **na zasób** w tabeli.

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Wybierz pozycję **wszystkie usługi**, a następnie z listy wybierz pozycję **DevTest Labs** .
3. Z listy laboratoriów wybierz odpowiednie laboratorium.  
4. W menu po lewej stronie wybierz pozycję **Konfiguracja i zasady** .
5. Wybierz pozycję **Koszt według zasobów** w sekcji **Śledzenie kosztów** w menu po lewej stronie. Zobaczysz koszty związane z poszczególnymi zasobami skojarzonymi z laboratorium. 

    ![Koszt według zasobu](./media/devtest-lab-configure-cost-management/cost-by-resource.png)

Ta funkcja pomaga łatwo identyfikować zasoby, które są najtańsze, dzięki czemu możesz podjąć działania w celu zmniejszenia wydatków laboratorium. Na przykład koszt maszyny wirtualnej zależy od rozmiaru maszyny wirtualnej. Im większy rozmiar maszyny wirtualnej, tym większa jest koszt. Możesz łatwo znaleźć rozmiar maszyny wirtualnej i właściciela, aby skontaktować się z właścicielem maszyny wirtualnej w celu zrozumienia przyczyny tego rozmiaru maszyny wirtualnej i określić, czy jest możliwe zmniejszenie rozmiaru.

[Zasady automatycznego zamykania](devtest-lab-set-lab-policy.md?#set-auto-shutdown-policy) ułatwiają obniżenie kosztów dzięki wykorzystaniu maszyn wirtualnych laboratorium w określonym czasie dnia. Jednak użytkownik laboratorium może zrezygnować z zasad zamykania, co zwiększa koszt działania maszyny wirtualnej. Możesz wybrać maszynę wirtualną w tabeli, aby sprawdzić, czy została ona wyświetlona z zasad automatycznego zamykania. W takim przypadku można skontaktować się z właścicielem maszyny wirtualnej w celu sprawdzenia, dlaczego maszyna wirtualna została wykorzystana z zasad.
 
## <a name="next-steps"></a>Następne kroki
Oto kilka rzeczy, aby spróbować dalej:

* [Definiowanie zasad laboratorium](devtest-lab-set-lab-policy.md) — informacje na temat ustawiania różnych zasad służących do zarządzania sposobem korzystania z laboratorium i jego maszyn wirtualnych. 
* [Tworzenie obrazu niestandardowego](devtest-lab-create-template.md) — podczas tworzenia maszyny wirtualnej należy określić podstawową, która może być obrazem niestandardowym lub obrazem portalu Marketplace. W tym artykule pokazano, jak utworzyć obraz niestandardowy na podstawie pliku VHD.
* [Konfigurowanie obrazów z portalu Marketplace](devtest-lab-configure-marketplace-images.md) — DevTest Labs obsługuje tworzenie maszyn wirtualnych na podstawie obrazów portalu Azure Marketplace. W tym artykule pokazano, jak określić, w jaki sposób można używać obrazów portalu Azure Marketplace podczas tworzenia maszyn wirtualnych w środowisku laboratoryjnym.
* [Tworzenie maszyny wirtualnej w laboratorium](devtest-lab-add-vm.md) — pokazuje, jak utworzyć maszynę wirtualną na podstawie obrazu podstawowego (niestandardowego lub Marketplace) oraz jak korzystać z artefaktów na maszynie wirtualnej.
