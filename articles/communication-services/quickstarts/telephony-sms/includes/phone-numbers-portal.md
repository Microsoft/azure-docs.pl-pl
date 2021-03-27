---
ms.openlocfilehash: 82e475c97aa2c68a33a48b04fe3e45fb13728b88
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105629402"
---

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zasób aktywnych usług komunikacyjnych.](../../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Pobierz numer telefonu

Aby rozpocząć Inicjowanie obsługi numerów, przejdź do zasobu usług komunikacyjnych w [Azure Portal](https://portal.azure.com).

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

### <a name="search-for-available-phone-numbers"></a>Wyszukaj dostępne numery telefonów

Przejdź do bloku **numery telefonów** w menu zasób.

:::image type="content" source="../../media/manage-phone-azure-portal-phone-page.png" alt-text="Zrzut ekranu przedstawiający stronę telefonu zasobu usług komunikacyjnych.":::

Naciśnij przycisk **Get (Pobierz** ), aby uruchomić kreatora. Kreator w bloku **numery telefonów** przeprowadzi Cię przez szereg pytań, które ułatwiają wybranie numeru telefonu, który najlepiej odpowiada Twojemu scenariuszowi. 

Najpierw musisz wybrać **kraj/region** , w którym chcesz zainicjować obsługę administracyjną numeru telefonu. Po wybraniu kraju/regionu będziesz musiał wybrać **przypadek użycia** , który najlepiej odpowiada Twoim potrzebom. 

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers.png" alt-text="Zrzut ekranu przedstawiający widok pobieranie numerów telefonów.":::

### <a name="select-your-phone-number-features"></a>Wybierz funkcje numeru telefonu

Konfigurowanie numeru telefonu jest podzielone na dwa kroki: 

1. Wybór [typu liczby](../../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Wybór [liczby możliwości](../../../concepts/telephony-sms/plan-solution.md#phone-number-capabilities-in-azure-communication-services)

Możesz wybrać jedną z dwóch typów numerów telefonów: **geograficznej** i **bezpłatnej**. Po wybraniu typu liczbowego można wybrać funkcję.

W naszym przykładzie wybieramy **bezpłatny** typ numeru z funkcjami **wywołania wychodzącego** i **wychodzącego SMS** .

:::image type="content" source="../../media/manage-phone-azure-portal-select-plans.png" alt-text="Zrzut ekranu przedstawiający Widok wybierz funkcje.":::

W tym miejscu kliknij przycisk **Dalej: cyfry** w dolnej części strony, aby dostosować numery telefonów, które chcesz udostępnić.

### <a name="customizing-phone-numbers"></a>Dostosowywanie numerów telefonów

Na stronie **liczby** są dostosowywane numery telefonów, które chcesz udostępnić.

:::image type="content" source="../../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Zrzut ekranu przedstawiający stronę wybór liczb.":::

> [!NOTE]
> Ten przewodnik Szybki Start przedstawia przepływ dostosowywania **typu numeru** bezpłatnego. Środowisko może być nieco inne w przypadku wybrania typu numeru **geograficznego** , ale wynik końcowy będzie taki sam.

Wybierz **kod obszaru** z listy dostępnych kodów obszaru i wprowadź liczbę, którą chcesz udostępnić, a następnie kliknij przycisk **Wyszukaj** , aby znaleźć numery, które spełniają wybrane wymagania. Numery telefonów, które spełniają Twoje wymagania, będą wyświetlane wraz z miesięcznymi kosztami.

:::image type="content" source="../../media/manage-phone-azure-portal-found-numbers.png" alt-text="Zrzut ekranu przedstawiający stronę wybór liczb z zastrzeżonymi numerami.":::

> [!NOTE]
> Dostępność zależy od typu liczby, lokalizacji i funkcji, które zostały wybrane.
> Liczby są zarezerwowane przez krótki czas przed wygaśnięciem transakcji. Jeśli transakcja wygaśnie, należy ponownie wybrać liczby.

Aby wyświetlić podsumowanie zakupu i złożyć zamówienie, kliknij przycisk **Dalej: Podsumowanie** w dolnej części strony.

### <a name="purchase-phone-numbers"></a>Zakupy numerów telefonów

Na stronie Podsumowanie zostanie zastosowany numer telefonu, funkcje, numery telefonów i łączny koszt miesięczny w celu udostępnienia numerów telefonów.

> [!NOTE]
> Podane ceny to **miesięczne opłaty cykliczne** , które obejmują koszt leasingu wybranego numeru telefonu. Nieuwzględnione w tym widoku to **koszty płatność zgodnie z rzeczywistym** użyciem, które są naliczane podczas wykonywania lub odbierania wywołań. Cennik są [dostępne tutaj](../../../concepts/pricing.md). Te koszty zależą od typu i lokalizacji docelowych o nazwie. Na przykład cena na minutę w przypadku wywołania od numeru regionalnego Seattle do numeru regionalnego w Nowym Jorku, a wywołanie z tego samego numeru do numeru komórkowego Zjednoczonego Królestwa może się różnić.

Na koniec kliknij pozycję **Umieść zamówienie** w dolnej części strony, aby potwierdzić.

:::image type="content" source="../../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Zrzut ekranu przedstawiający stronę Podsumowanie z widocznym typem numeru, funkcjami, numerami telefonów i łącznym kosztem miesięcznym.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Znajdź numery telefonów na Azure Portal

Przejdź do zasobu komunikacyjnego platformy Azure na [Azure Portal](https://portal.azure.com):

:::image type="content" source="../../media/manage-phone-azure-portal-start.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

Wybierz blok numery telefonów w menu, aby zarządzać numerami telefonów.

:::image type="content" source="../../media/manage-phone-azure-portal-phones.png" alt-text="Zrzut ekranu przedstawiający stronę numeru telefonu zasobu usług komunikacyjnych.":::

> [!NOTE]
> Wyświetlanie numerów aprowizacji na tej stronie może potrwać kilka minut.


### <a name="update-phone-number-capabilities"></a>Aktualizowanie możliwości numeru telefonu

Na stronie **liczby** możesz wybrać numer telefonu, aby go skonfigurować.

:::image type="content" source="../../media/manage-phone-azure-portal-capability-update.png" alt-text="Zrzut ekranu przedstawiający stronę aktualizowanie funkcji.":::

Wybierz funkcje z dostępnych opcji, a następnie kliknij przycisk **Potwierdź** , aby zastosować wybór.

### <a name="release-phone-number"></a>Numer telefonu wydania

Na stronie **liczby** można zwolnić numery telefonów.

:::image type="content" source="../../media/manage-phone-azure-portal-release-number.png" alt-text="Zrzut ekranu przedstawiający stronę numerów telefonów zwolnienia.":::

Wybierz numer telefonu, który ma zostać zwolniony, a następnie kliknij przycisk **Zwolnij** .