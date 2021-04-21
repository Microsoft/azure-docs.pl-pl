---
title: Azure Key Vault monitorowania i alertów | Microsoft Docs
description: Utwórz pulpit nawigacyjny, aby monitorować kondycję magazynu kluczy i konfigurować alerty.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 03/31/2021
ms.author: mbaldwin
ms.openlocfilehash: f8f9dd6d51b974ebd31804daf0402ca5535ffc92
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107751584"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitorowanie i alerty dotyczące Azure Key Vault

## <a name="overview"></a>Omówienie

Po użyciu magazynu kluczy do przechowywania produkcyjnych wpisów tajnych należy monitorować kondycję magazynu kluczy, aby upewnić się, że usługa działa zgodnie z przeznaczeniem. Gdy zaczniesz skalować usługę, liczba żądań wysyłanych do magazynu kluczy wzrośnie. Może to zwiększyć opóźnienie żądań, a w skrajnych przypadkach spowodować ograniczenie żądań, co będzie miało wpływ na wydajność usługi. Musisz również być powiadamiany, jeśli magazyn kluczy wysyła nietypową liczbę kodów błędów, aby można było szybko powiadamiać o wszelkich problemach z zasadami dostępu lub konfiguracją zapory. Ten dokument obejmuje następujące tematy:

+ Podstawowe Key Vault do monitorowania
+ Jak skonfigurować metryki i utworzyć pulpit nawigacyjny
+ Jak tworzyć alerty z określonymi progami

Azure Monitor dla Key Vault łączy zarówno dzienniki, jak i metryki w celu zapewnienia globalnego rozwiązania do monitorowania. [Dowiedz się więcej o Azure Monitor dla Key Vault tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview#introduction-to-azure-monitor-for-key-vault)

## <a name="basic-key-vault-metrics-to-monitor"></a>Podstawowe Key Vault do monitorowania

+ Dostępność magazynu  
+ Nasycenie magazynu 
+ Opóźnienie interfejsu API usługi 
+ Łączna liczba trafień interfejsu API usługi (filtruj według typu działania) 
+ Kody błędów (filtruj według kodu stanu) 

**Dostępność magazynu** — ta metryka powinna zawsze mieć wartość 100% i jest to ważna metryka do monitorowania, ponieważ może ona szybko pokazać, czy w magazynie kluczy wystąpiła błąd. 

**Nasycenie** magazynu — liczba żądań na sekundę, które magazyn kluczy może obsługiwać, zależy od typu wykonywanej operacji. Niektóre operacje magazynu mają niższy próg żądań na sekundę. Ta metryka agreguje łączne użycie magazynu kluczy we wszystkich typach operacji, aby wymyślić wartość procentową, która wskazuje bieżące użycie magazynu kluczy. Aby uzyskać pełną listę limitów usługi Key Vault, zobacz następujący dokument. [Limity usługi Azure Key Vault](service-limits.md)

**Opóźnienie interfejsu API** usługi — ta metryka przedstawia średnie opóźnienie wywołań magazynu kluczy mierzone w usłudze. Nie obejmuje czasu używanego przez klienta ani przez sieć między klientem a usługą.

**Łączna liczba trafień** interfejsu API — ta metryka przedstawia wszystkie wywołania do magazynu kluczy. Pomoże to określić, które aplikacje wywołują magazyn kluczy. 

**Kody błędów** — ta metryka pokazuje, czy w magazynie kluczy występuje nietypowa liczba błędów. Aby uzyskać pełną listę kodów błędów i wskazówki dotyczące rozwiązywania problemów, zobacz następujący dokument. [Azure Key Vault błędów interfejsu API REST](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Jak skonfigurować metryki i utworzyć pulpit nawigacyjny

1. Zaloguj się w witrynie Azure Portal
2. Przejdź do swojego Key Vault
3. Wybierz **pozycję Metryki w** obszarze **Monitorowanie** 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający opcję Metryki w sekcji Monitorowanie.](../media/alert-1.png)

4. Zaktualizuj tytuł wykresu do tego, co chcesz zobaczyć na pulpicie nawigacyjnym. 
5. Wybierz zakres. W tym przykładzie wybierzemy pojedynczy magazyn kluczy. 
6. Wybierz ogólną **dostępność magazynu metryk i** średnią **agregacji** 
7. Zaktualizuj zakres czasu na ostatnie 24 godziny i zaktualizuj poziom szczegółowości czasu na 1 minutę. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający metrykę Ogólna dostępność magazynu.](../media/alert-2.png)

8. Powtórz powyższe kroki dla metryk Nasycenie magazynu i Opóźnienie interfejsu API usługi. Wybierz **pozycję Przypnij** do pulpitu nawigacyjnego, aby zapisać metryki na pulpicie nawigacyjnym. 

> [!IMPORTANT]
> Wybierz pozycję "Przypnij do pulpitu nawigacyjnego" i zapisz każdą skonfigurowaną metrykę. Jeśli opuścisz stronę i wrócisz do niego bez zapisywania, zmiany konfiguracji zostaną utracone. 

9. Aby monitorować wszystkie typy operacji w magazynie kluczy, użyj metryki Łączna liczba trafień interfejsu **API** usługi i wybierz pozycję Zastosuj **podział według typu działania**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający przycisk Zastosuj podział.](../media/alert-3.png)

10. Aby monitorować kody błędów w magazynie kluczy, użyj metryki Total Service API Results Metric (Łączna liczba wyników **interfejsu API** usługi) i wybierz pozycję Apply **Splitting by Activity Type (Zastosuj podział według typu działania)**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający wybraną metrykę Total Service API Results (Łączna liczba wyników interfejsu API usługi).](../media/alert-4.png)

Teraz będziesz mieć pulpit nawigacyjny, który wygląda następująco. Możesz kliknąć 3 kropki w prawym górnym rogu każdego kafelka, aby zmienić rozmieszczenie kafelków i zmienić ich rozmiar zgodnie z potrzebami. 

Po zapisaniu i opublikowaniu pulpitu nawigacyjnego utworzy on nowy zasób w subskrypcji platformy Azure. Będzie można go wyświetlić w dowolnym momencie, wyszukując "udostępniony pulpit nawigacyjny". 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający opublikowany pulpit nawigacyjny.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Jak skonfigurować alerty na Key Vault 

W tej sekcji opisano sposób konfigurowania alertów w magazynie kluczy, dzięki czemu można powiadamiać zespół o podjęciu natychmiastowej akcji, jeśli magazyn kluczy jest w złej kondycji. Możesz skonfigurować alerty, które wysyłają wiadomość e-mail, najlepiej do zespołu DL, wywołują powiadomienie usługi Event Grid lub dzwonią lub wysyłają wiadomość SMS na numer telefonu. Możesz również wybrać alerty statyczne oparte na stałej wartości lub alert dynamiczny, który będzie cię powiadamiać, jeśli monitorowana metryka przekroczy średni limit magazynu kluczy określoną liczbę razy w określonym zakresie czasu. 

> [!IMPORTANT]
> Pamiętaj, że rozpoczęcie wysyłania powiadomień przez nowo skonfigurowane alerty może potrwać do 10 minut. 

### <a name="configure-an-action-group"></a>Konfigurowanie grupy akcji 

Grupa akcji to konfigurowalna lista powiadomień i właściwości.

1. Zaloguj się w witrynie Azure Portal
2. Wyszukiwanie **alertów** w polu wyszukiwania
3. Wybieranie **opcji Zarządzaj akcjami**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający przycisk Zarządzaj akcjami.](../media/alert-6.png)

4. Wybierz **pozycję + Dodaj grupę akcji**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający przycisk + Dodaj grupę akcji.](../media/alert-7.png)

5. Wybierz typ **akcji dla** grupy akcji. W tym przykładzie utworzymy alert e-mail.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający pola wymagane do dodania grupy akcji.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający, co jest potrzebne do dodania alertu e-mail lub wiadomości SMS.](../media/alert-9.png)

6. Kliknij przycisk **OK** w dolnej części strony. Pomyślnie utworzono grupę akcji. 

Po skonfigurowaniu grupy akcji skonfigurujemy progi alertów magazynu kluczy. 

### <a name="configure-alert-thresholds"></a>Konfigurowanie progów alertów 

1. Wybierz zasób magazynu kluczy w chmurze Azure Portal wybierz pozycję **Alerty w** obszarze **Monitorowanie**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający opcję menu Alerty w sekcji Monitorowanie.](../media/alert-10.png)

2. Wybierz pozycję **Nowa reguła alertu**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający przycisk + Nowa reguła alertu.](../media/alert-11.png)

3. Wybierz zakres reguły alertu. Możesz wybrać pojedynczy magazyn lub wiele magazynów. 

> [!IMPORTANT]
> Należy pamiętać, że w przypadku wybrania wielu magazynów dla zakresu alertów wszystkie wybrane magazyny muszą znajdować się w tym samym regionie. Należy skonfigurować oddzielne reguły alertów dla magazynów w różnych regionach. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający sposób wybierania magazynu.](../media/alert-12.png)

4. Wybierz warunki dla alertów. Możesz wybrać dowolny z poniższych sygnałów i zdefiniować logikę dla alertów. Zespół Key Vault zaleca skonfigurowanie następujących progów alertów. 

    + Key Vault dostępność spada poniżej 100% (próg statyczny)
    + Key Vault opóźnienie jest większe niż 500 m (próg statyczny) 
    + Ogólne nasycenie magazynu jest większe niż 75% (próg statyczny) 
    + Ogólne nasycenie magazynu przekracza średnią (próg dynamiczny)
    + Łączna liczba kodów błędów większa niż średnia (próg dynamiczny) 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający miejsce wybierania warunków dla alertów.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Przykład 1: Konfigurowanie statycznego progu alertu dla opóźnienia

Wybierz **pozycję Overall Service API Latency (Ogólne** opóźnienie interfejsu API usługi) jako nazwę sygnału
> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający nazwę sygnału Ogólne opóźnienie interfejsu API usługi.](../media/alert-14.png)

Zobacz następujące parametry konfiguracji.

+ Ustaw próg na **statyczny** 
+ Ustaw operator na **większe niż**
+ Ustaw typ agregacji na **średnią**
+ Ustaw wartość progową na **500**
+ Ustaw okres agregacji na **5 minut**
+ Ustaw częstotliwość oceny na **1 minutę**
+ Wybierz pozycję **Gotowe**  

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający skonfigurowaną logikę alertów.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Przykład 2: Konfigurowanie dynamicznego progu alertu dla nasycenia magazynu 

W przypadku korzystania z alertu dynamicznego można wyświetlić dane historyczne wybranego magazynu kluczy. Niebieski obszar reprezentuje średnie użycie magazynu kluczy. Czerwony obszar pokazuje skoki, które spowodowałyby wyzwolenie alertu, jeśli zostały spełnione inne kryteria w konfiguracji alertu. Czerwone kropki pokazują wystąpienia naruszeń, w których kryteria alertu zostały spełnione w zagregowanym oknie czasowym. Możesz ustawić alert do działania po określonej liczbie naruszeń w określonym czasie. Jeśli nie chcesz uwzględniać wcześniejszych danych, możesz wykluczyć stare dane poniżej w ustawieniach zaawansowanych. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający wykres całkowitego nasycenia magazynu.](../media/alert-16.png)

Zobacz następujące parametry konfiguracji.

+ Ustaw wartość progową na **dynamiczny** 
+ Ustaw operator na **wartość Większe niż**
+ Ustaw typ agregacji na **średnią**
+ Ustaw czułość progową na **średnią**
+ Ustaw okres agregacji na **5 minut**
+ Ustaw częstotliwość oceny na **1 minutę**
+ **Opcjonalne** Konfigurowanie ustawień zaawansowanych 
+ Wybierz pozycję **Gotowe**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający Azure Portal](../media/alert-17.png)

5. Dodawanie skonfigurowanej grupy akcji

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający sposób dodawania grupy akcji.](../media/alert-18.png)

6. Włączanie alertu i przypisywanie ważności

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający miejsce włączenia alertu i przypisania ważności.](../media/alert-19.png)

7. Tworzenie alertu 

### <a name="example-email-alert"></a>Przykładowy alert e-mail 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający informacje potrzebne do skonfigurowania alertu e-mail.](../media/alert-20.png)

## <a name="next-steps"></a>Następne kroki

Gratulacje, udało Ci się utworzyć pulpit nawigacyjny monitorowania i skonfigurować alerty dla magazynu kluczy. Po zakończeniu wszystkich powyższych kroków alerty e-mail powinny być odbierane, gdy magazyn kluczy spełnia skonfigurowane kryteria alertu. Przykład przedstawiono poniżej. Użyj narzędzi, które zostały ustawione w tym artykule, aby aktywnie monitorować kondycję magazynu kluczy. 


