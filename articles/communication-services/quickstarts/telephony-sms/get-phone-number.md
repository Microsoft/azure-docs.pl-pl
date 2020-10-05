---
title: Szybki Start — uzyskiwanie numeru telefonu z usług Azure Communications Services
description: Dowiedz się, jak kupić numer telefonu usług komunikacyjnych za pomocą Azure Portal.
author: ddematheu2
manager: nimag
services: azure-communication-services
ms.author: dademath
ms.date: 07/09/2020
ms.topic: quickstart
ms.service: azure-communication-services
ms.custom: references_regions
ms.openlocfilehash: f5cf8f8ef004dacc9fe2bbdd1b815f2ae5275311
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91298120"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Szybki Start: uzyskiwanie numeru telefonu przy użyciu Azure Portal

[!INCLUDE [Private Preview Notice](../../includes/private-preview-include.md)]

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

Najpierw musisz wybrać lokalizację, w `Country/region` której chcesz zainicjować obsługę administracyjną numeru telefonu. Po wybraniu kraju/regionu będziesz musiał wybrać, które najlepiej odpowiadają `phone plan` Twoim potrzebom. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

### <a name="select-a-phone-plan"></a>Wybierz plan telefonu

Wybór planu telefonu jest podzielony na dwa kroki: 

1. Wybór [typu liczby](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Wybór [planu](../../concepts/telephony-sms/plan-solution.md#plans)

Oferujemy obecnie dwa typy liczb: `Geographic` , i `Toll-free` . Po wybraniu typu liczbowego będziesz oferować kilka planów, z których możesz wybrać.

> [!NOTE]
> Obecnie obsługujemy wybieranie numerów telefonów z obsługą ruchu przychodzącego lub wychodzącego. Można jednak zakupić numer telefonu z włączonym wywołaniem przychodzącym, a następnie skonfigurować identyfikator rozmówcy wychodzącego w taki sposób, aby odpowiadał liczbie numeru telefonu z włączoną obsługą ruchu przychodzącego (co widzą użytkownicy po wywołaniu ich w aplikacji usług komunikacyjnych).
> Ma to zastosowanie tylko do dwukierunkowego wywoływania. Dwukierunkowa wiadomość SMS jest obsługiwana natywnie.

W naszym przykładzie wybrano `Toll-free` typ numeru z `Outbound calling` planem.

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

### <a name="declare-purpose"></a>Zadeklaruj cel

Następnie Kreator wyświetli zapytanie w celu użycia liczby. Firma Microsoft zbiera te informacje w celu zastosowania odpowiednich przepisów podatkowych i regulacji dotyczących wywołań awaryjnych.

:::image type="content" source="../media/quickstart-search-and-acquire-bot-or-human.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

W tym miejscu kliknij `Next: Numbers` przycisk w dolnej części strony, aby dostosować numery telefonów, które chcesz udostępnić.

### <a name="customizing-phone-numbers"></a>Dostosowywanie numerów telefonów

Na `Numbers` stronie zostaną dostosowane numery telefonów, które chcesz udostępnić.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

> [!NOTE]
> Ten przewodnik Szybki Start przedstawia `Toll-free` przepływ dostosowania typu liczby. Środowisko może być nieco inne, jeśli wybrano `Geographic` typ Number, ale wynik końcowy będzie taki sam.

Wybierz `Area code` z listy dostępnych kodów obszaru i wprowadź liczbę, którą chcesz udostępnić, a następnie kliknij, `Search` Aby znaleźć numery spełniające wybrane wymagania. Numery telefonów, które spełniają Twoje wymagania, będą wyświetlane wraz z miesięcznymi kosztami.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

> [!NOTE]
> Dostępność zależy od typu liczby, lokalizacji i wybranego planu.
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

Wybierz kartę numery telefonów w menu, aby zarządzać numerami telefonów.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

> [!NOTE]
> Wyświetlanie numerów aprowizacji na tej stronie może potrwać kilka minut.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Często zadawane pytania i problemy:

- W tej chwili jest obsługiwane kupowanie numerów telefonów. Jest to oparte na adresie rozliczeniowym subskrypcji, z którą skojarzony jest zasób. W tej chwili nie można przenieść zasobu do innej subskrypcji.

- Po usunięciu numeru telefonu numer telefonu nie zostanie opublikowany lub nie można go wykupić do końca cyklu rozliczeniowego.

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
