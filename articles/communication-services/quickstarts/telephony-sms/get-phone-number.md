---
title: Szybki Start — uzyskiwanie numeru telefonu z usług Azure Communications Services
description: Dowiedz się, jak kupić numer telefonu usług komunikacyjnych za pomocą Azure Portal.
author: prakulka
manager: nmurav
services: azure-communication-services
ms.author: prakulka
ms.date: 10/05/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: 49a5fd51a62dd6c90d7b1bac8d99296ddc81287f
ms.sourcegitcommit: a92fbc09b859941ed64128db6ff72b7a7bcec6ab
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/15/2020
ms.locfileid: "92070099"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Szybki Start: uzyskiwanie numeru telefonu przy użyciu Azure Portal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu Azure Portal, aby kupić numer telefonu.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zasób aktywnych usług komunikacyjnych.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Pobierz numer telefonu

Aby rozpocząć Inicjowanie obsługi numerów, przejdź do zasobu usług komunikacyjnych w [Azure Portal](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

### <a name="getting-new-phone-numbers"></a>Pobieranie nowych numerów telefonów

Przejdź do bloku numery telefonów w menu zasób.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

Naciśnij `Get` przycisk, aby uruchomić kreatora. Kreator w `Phone numbers` bloku przeprowadzi Cię przez szereg pytań, które ułatwiają wybranie numeru telefonu, który najlepiej pasuje do Twojego scenariusza. 

Najpierw musisz wybrać lokalizację, w `Country/region` której chcesz zainicjować obsługę administracyjną numeru telefonu. Po wybraniu kraju/regionu będziesz musiał wybrać, które najlepiej odpowiadają `use case` Twoim potrzebom. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

### <a name="select-your-phone-number-features"></a>Wybierz funkcje numeru telefonu

Konfigurowanie numeru telefonu jest podzielone na dwa kroki: 

1. Wybór [typu liczby](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Wybór [funkcji liczb](../../concepts/telephony-sms/plan-solution.md#phone-number-features-in-azure-communication-services)

Można wybrać jedną z dwóch typów numerów telefonów: `Geographic` , i `Toll-free` . Po wybraniu typu liczbowego można wybrać funkcję.

W naszym przykładzie wybrano `Toll-free` typ liczbowy z `Outbound calling` `Inbound and Outbound SMS` funkcjami i.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

W tym miejscu kliknij `Next: Numbers` przycisk w dolnej części strony, aby dostosować numery telefonów, które chcesz udostępnić.

### <a name="customizing-phone-numbers"></a>Dostosowywanie numerów telefonów

Na `Numbers` stronie zostaną dostosowane numery telefonów, które chcesz udostępnić.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

> [!NOTE]
> Ten przewodnik Szybki Start przedstawia `Toll-free` przepływ dostosowania typu liczby. Środowisko może być nieco inne, jeśli wybrano `Geographic` typ Number, ale wynik końcowy będzie taki sam.

Wybierz `Area code` z listy dostępnych kodów obszaru i wprowadź liczbę, którą chcesz udostępnić, a następnie kliknij, `Search` Aby znaleźć numery spełniające wybrane wymagania. Numery telefonów, które spełniają Twoje wymagania, będą wyświetlane wraz z miesięcznymi kosztami.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

> [!NOTE]
> Dostępność zależy od typu liczby, lokalizacji i funkcji, które zostały wybrane.
> Liczby są zarezerwowane przez krótki czas przed wygaśnięciem transakcji. Jeśli transakcja wygaśnie, należy ponownie wybrać liczby.

Aby wyświetlić podsumowanie zakupu i złożyć zamówienie, kliknij `Next: Summary` przycisk w dolnej części strony.

### <a name="place-order"></a>Umieść zamówienie

Na stronie Podsumowanie zostanie zastosowany numer telefonu, funkcje, numery telefonów i łączny koszt miesięczny w celu udostępnienia numerów telefonów.

> [!NOTE]
> Podane ceny to **miesięczne opłaty cykliczne** , które obejmują koszt leasingu wybranego numeru telefonu. Nieuwzględnione w tym widoku to **koszty płatność zgodnie z rzeczywistym** użyciem, które są naliczane podczas wykonywania lub odbierania wywołań. Cennik są [dostępne tutaj](../../concepts/pricing.md). Te koszty zależą od typu i lokalizacji docelowych o nazwie. Na przykład cena na minutę w przypadku wywołania od numeru regionalnego Seattle do numeru regionalnego w Nowym Jorku, a wywołanie z tego samego numeru do numeru komórkowego Zjednoczonego Królestwa może się różnić.

Na koniec kliknij przycisk `Place order` w dolnej części strony, aby potwierdzić.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Znajdź numery telefonów na Azure Portal

Przejdź do zasobu komunikacyjnego platformy Azure na [Azure Portal](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

Wybierz blok numery telefonów w menu, aby zarządzać numerami telefonów.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

> [!NOTE]
> Wyświetlanie numerów aprowizacji na tej stronie może potrwać kilka minut.


### <a name="customizing-phone-numbers"></a>Dostosowywanie numerów telefonów

Na `Numbers` stronie możesz wybrać numer telefonu, aby go skonfigurować.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

Wybierz funkcje z dostępnych opcji, a następnie kliknij, `Confirm` Aby zastosować wybór.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Często zadawane pytania i problemy:

- W tej chwili jest obsługiwane kupowanie numerów telefonów. Jest to oparte na adresie rozliczeniowym subskrypcji, z którą skojarzony jest zasób. W tej chwili nie można przenieść zasobu do innej subskrypcji.

- Po wydaniu numeru telefonu numer telefonu nie zostanie wykupiony lub nie można go wykupić do końca cyklu rozliczeniowego.

- Po usunięciu zasobu usług komunikacyjnych numery telefonów skojarzone z tym zasobem będą automatycznie wydawane w tym samym czasie.

## <a name="next-steps"></a>Następne kroki

W tym przewodniku szybki start zawarto informacje na temat wykonywania tych instrukcji:

> [!div class="checklist"]
> * Kup numer telefonu
> * Zarządzanie numerem telefonu
> * Zwolnij numer telefonu

> [!div class="nextstepaction"]
> [Wyślij wiadomość SMS](../telephony-sms/send.md) 
>  [Wprowadzenie do wywoływania](../voice-video-calling/getting-started-with-calling.md)
