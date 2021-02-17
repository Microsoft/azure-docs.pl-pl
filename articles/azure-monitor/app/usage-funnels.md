---
title: Lejki Application Insights platformy Azure
description: Dowiedz się, jak można użyć lejków, aby poznać, jak klienci korzystają z aplikacji.
ms.topic: conceptual
author: NumberByColors
ms.author: daviste
ms.date: 07/17/2017
ms.reviewer: mbullwin
ms.openlocfilehash: d0e5cb5097a2f8833b10fec16a99b67c5c109fc8
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100583455"
---
# <a name="discover-how-customers-are-using-your-application-with-application-insights-funnels"></a>Dowiedz się, jak klienci korzystają z aplikacji za pomocą lejków Application Insights

Zrozumienie środowiska klienta ma największe znaczenie dla Twojej firmy. Jeśli aplikacja obejmuje wiele etapów, należy wiedzieć, czy większość klientów postępuje w całym procesie, czy też kończy proces w pewnym momencie. Postęp przez serię kroków aplikacji sieci Web jest znany jako *lejek*. Możesz użyć lejków Application Insights platformy Azure, aby uzyskać wgląd w użytkowników i monitorować kursy krok po kroku. 

## <a name="create-your-funnel"></a>Tworzenie lejka
Przed utworzeniem lejka wybierz pytanie, na które chcesz odpowiedzieć. Na przykład możesz chcieć wiedzieć, jak wielu użytkowników przegląda stronę główną, wyświetlając profil klienta i tworząc bilet. W tym przykładzie właściciele firmy Fabrikam Fiber chcą znać odsetek klientów, którzy pomyślnie utworzyli bilet klienta.

Poniżej przedstawiono kroki, które należy wykonać w celu utworzenia lejka.

1. W narzędziu Lejki Application Insights wybierz pozycję **Nowy**.
1. Z menu rozwijanego **zakres czasu** wybierz pozycję **ostatnie 90 dni**. Wybierz dowolne z **lejków** lub **wspólnych lejków**.
1. Z listy rozwijanej **krok 1** wybierz pozycję **indeks**. 
1. Z listy **krok 2** wybierz pozycję **Klient**.
1. Z listy **krok 3** wybierz pozycję **Utwórz**.
1. Dodaj nazwę do lejka, a następnie wybierz pozycję **Zapisz**.

Poniższy zrzut ekranu przedstawia przykład rodzaju danych generowanych przez narzędzie Lejki. Właściciele firmy Fabrikam mogą zobaczyć, że w ciągu ostatnich 90 dni, 54,3 procent klientów, którzy odwiedzili stronę główną, utworzyły bilet klienta. Mogą również zobaczyć, że 2 700 klientów jest w indeksie ze strony głównej. Może to wskazywać na problem z odświeżaniem.


![Zrzut ekranu przedstawiający narzędzie lejków z danymi](media/usage-funnels/funnel1.png)

### <a name="funnels-features"></a>Funkcje lejkowe
Poprzedni zrzut ekranu zawiera pięć wyróżnionych obszarów. Są to funkcje lejków. Poniższa lista zawiera więcej informacji o każdym odpowiednim obszarze zrzutu ekranu:
1. Jeśli Twoja aplikacja zostanie pobrana, zobaczysz baner z próbką. Wybranie transparentu powoduje otwarcie okienka kontekstowego, w którym wyjaśniono, jak wyłączyć próbkowanie. 
2. Możesz wyeksportować lejek do [Power BI](./export-power-bi.md).
3. Wybierz krok, aby wyświetlić więcej szczegółów po prawej stronie. 
4. Wykres konwersji historycznej przedstawia kursy konwersji w ciągu ostatnich 90 dni. 
5. Lepiej zrozumieć swoich użytkowników, uzyskując dostęp do narzędzia users. W każdym kroku można używać filtrów. 

## <a name="next-steps"></a>Następne kroki
  * [Przegląd użycia](usage-overview.md)
  * [Użytkownicy, sesje i zdarzenia](usage-segmentation.md)
  * [Noszą](usage-retention.md)
  * [Skoroszyty](../visualize/workbooks-overview.md)
  * [Dodawanie kontekstu użytkownika](usage-send-user-context.md)
  * [Eksportowanie do usługi Power BI](./export-power-bi.md)

