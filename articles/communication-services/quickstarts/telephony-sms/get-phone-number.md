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
ms.openlocfilehash: 4fda626581265b4b1f6541a3b453b164ccd27963
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101690451"
---
# <a name="quickstart-get-a-phone-number-using-the-azure-portal"></a>Szybki Start: uzyskiwanie numeru telefonu przy użyciu Azure Portal

[!INCLUDE [Public Preview Notice](../../includes/public-preview-include.md)]
[!INCLUDE [Regional Availability Notice](../../includes/regional-availability-include.md)]

Rozpocznij pracę z usługami Azure Communications Services przy użyciu Azure Portal, aby kupić numer telefonu.

## <a name="prerequisites"></a>Wymagania wstępne

- Konto platformy Azure z aktywną subskrypcją. [Utwórz konto bezpłatnie](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Zasób aktywnych usług komunikacyjnych.](../create-communication-resource.md)

## <a name="get-a-phone-number"></a>Pobierz numer telefonu

Aby rozpocząć Inicjowanie obsługi numerów, przejdź do zasobu usług komunikacyjnych w [Azure Portal](https://portal.azure.com).

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

### <a name="getting-new-phone-numbers"></a>Pobieranie nowych numerów telefonów

Przejdź do bloku **numery telefonów** w menu zasób.

:::image type="content" source="../media/manage-phone-azure-portal-phone-page.png" alt-text="Zrzut ekranu przedstawiający stronę telefonu zasobu usług komunikacyjnych.":::

Naciśnij przycisk **Get (Pobierz** ), aby uruchomić kreatora. Kreator w bloku **numery telefonów** przeprowadzi Cię przez szereg pytań, które ułatwiają wybranie numeru telefonu, który najlepiej odpowiada Twojemu scenariuszowi. 

Najpierw musisz wybrać **kraj/region** , w którym chcesz zainicjować obsługę administracyjną numeru telefonu. Po wybraniu kraju/regionu będziesz musiał wybrać **przypadek użycia** , który najlepiej odpowiada Twoim potrzebom. 

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers.png" alt-text="Zrzut ekranu przedstawiający widok pobieranie numerów telefonów.":::

### <a name="select-your-phone-number-features"></a>Wybierz funkcje numeru telefonu

Konfigurowanie numeru telefonu jest podzielone na dwa kroki: 

1. Wybór [typu liczby](../../concepts/telephony-sms/plan-solution.md#phone-number-types-in-azure-communication-services)
2. Wybór [funkcji liczb](../../concepts/telephony-sms/plan-solution.md#phone-number-features-in-azure-communication-services)

Możesz wybrać jedną z dwóch typów numerów telefonów: **geograficznej** i **bezpłatnej**. Po wybraniu typu liczbowego można wybrać funkcję.

W naszym przykładzie wybieramy **bezpłatny** typ numeru z funkcjami **wywołania wychodzącego** i **wychodzącego SMS** .

:::image type="content" source="../media/manage-phone-azure-portal-select-plans.png" alt-text="Zrzut ekranu przedstawiający Widok wybierz funkcje.":::

W tym miejscu kliknij przycisk **Dalej: cyfry** w dolnej części strony, aby dostosować numery telefonów, które chcesz udostępnić.

### <a name="customizing-phone-numbers"></a>Dostosowywanie numerów telefonów

Na stronie **liczby** są dostosowywane numery telefonów, które chcesz udostępnić.

:::image type="content" source="../media/manage-phone-azure-portal-select-numbers-start.png" alt-text="Zrzut ekranu przedstawiający stronę wybór liczb.":::

> [!NOTE]
> Ten przewodnik Szybki Start przedstawia przepływ dostosowywania **typu numeru** bezpłatnego. Środowisko może być nieco inne w przypadku wybrania typu numeru **geograficznego** , ale wynik końcowy będzie taki sam.

Wybierz **kod obszaru** z listy dostępnych kodów obszaru i wprowadź liczbę, którą chcesz udostępnić, a następnie kliknij przycisk **Wyszukaj** , aby znaleźć numery, które spełniają wybrane wymagania. Numery telefonów, które spełniają Twoje wymagania, będą wyświetlane wraz z miesięcznymi kosztami.

:::image type="content" source="../media/manage-phone-azure-portal-found-numbers.png" alt-text="Zrzut ekranu przedstawiający stronę wybór liczb z zastrzeżonymi numerami.":::

> [!NOTE]
> Dostępność zależy od typu liczby, lokalizacji i funkcji, które zostały wybrane.
> Liczby są zarezerwowane przez krótki czas przed wygaśnięciem transakcji. Jeśli transakcja wygaśnie, należy ponownie wybrać liczby.

Aby wyświetlić podsumowanie zakupu i złożyć zamówienie, kliknij przycisk **Dalej: Podsumowanie** w dolnej części strony.

### <a name="place-order"></a>Umieść zamówienie

Na stronie Podsumowanie zostanie zastosowany numer telefonu, funkcje, numery telefonów i łączny koszt miesięczny w celu udostępnienia numerów telefonów.

> [!NOTE]
> Podane ceny to **miesięczne opłaty cykliczne** , które obejmują koszt leasingu wybranego numeru telefonu. Nieuwzględnione w tym widoku to **koszty płatność zgodnie z rzeczywistym** użyciem, które są naliczane podczas wykonywania lub odbierania wywołań. Cennik są [dostępne tutaj](../../concepts/pricing.md). Te koszty zależą od typu i lokalizacji docelowych o nazwie. Na przykład cena na minutę w przypadku wywołania od numeru regionalnego Seattle do numeru regionalnego w Nowym Jorku, a wywołanie z tego samego numeru do numeru komórkowego Zjednoczonego Królestwa może się różnić.

Na koniec kliknij pozycję **Umieść zamówienie** w dolnej części strony, aby potwierdzić.

:::image type="content" source="../media/manage-phone-azure-portal-get-numbers-summary.png" alt-text="Zrzut ekranu przedstawiający stronę Podsumowanie z widocznym typem numeru, funkcjami, numerami telefonów i łącznym kosztem miesięcznym.":::

## <a name="find-your-phone-numbers-on-the-azure-portal"></a>Znajdź numery telefonów na Azure Portal

Przejdź do zasobu komunikacyjnego platformy Azure na [Azure Portal](https://portal.azure.com):

:::image type="content" source="../media/manage-phone-azure-portal-start.png" alt-text="Zrzut ekranu przedstawiający stronę główną zasobu usług komunikacyjnych.":::

Wybierz blok numery telefonów w menu, aby zarządzać numerami telefonów.

:::image type="content" source="../media/manage-phone-azure-portal-phones.png" alt-text="Zrzut ekranu przedstawiający stronę numeru telefonu zasobu usług komunikacyjnych.":::

> [!NOTE]
> Wyświetlanie numerów aprowizacji na tej stronie może potrwać kilka minut.


### <a name="customizing-phone-numbers"></a>Dostosowywanie numerów telefonów

Na stronie **liczby** możesz wybrać numer telefonu, aby go skonfigurować.

:::image type="content" source="../media/manage-phone-azure-portal-capability-update.png" alt-text="Zrzut ekranu przedstawiający stronę aktualizowanie funkcji.":::

Wybierz funkcje z dostępnych opcji, a następnie kliknij przycisk **Potwierdź** , aby zastosować wybór.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Często zadawane pytania i problemy:

- Telefon zakupów jest obsługiwany tylko w Stanach Zjednoczonych. Aby kupić numery telefonów, upewnij się, że:
  - Skojarzony adres rozliczeniowy subskrypcji platformy Azure znajduje się w Stany Zjednoczone. W tej chwili nie można przenieść zasobu do innej subskrypcji.
  - Zasób usług komunikacyjnych jest inicjowany w lokalizacji danych Stany Zjednoczone. W tej chwili nie można przenieść zasobu do innej lokalizacji danych.

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
