---
title: Azure Key Vault monitorowania i alertów | Microsoft Docs
description: Utwórz pulpit nawigacyjny do monitorowania kondycji magazynu kluczy i konfigurowania alertów.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: 523f24a7d8131dda67244e4d48510d9e1cb7cdb4
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "104582633"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitorowanie i zgłaszanie alertów dla Azure Key Vault

## <a name="overview"></a>Omówienie

Po rozpoczęciu korzystania z magazynu kluczy do przechowywania własnych wpisów tajnych należy monitorować kondycję magazynu kluczy, aby upewnić się, że usługa działa zgodnie z oczekiwaniami. Po rozpoczęciu skalowania usługi liczba żądań wysyłanych do magazynu kluczy wzrośnie. Jest to możliwe, aby zwiększyć opóźnienie żądań i w skrajnych przypadkach, co spowoduje ograniczenie żądań, co wpłynie na wydajność usługi. Należy również otrzymywać alerty, jeśli Magazyn kluczy wysyła nietypową liczbę kodów błędów, dzięki czemu można szybko otrzymywać powiadomienia o wszelkich problemach z zasadami dostępu lub konfiguracją zapory. Ten dokument obejmuje następujące tematy:

+ Podstawowe metryki Key Vault do monitorowania
+ Jak skonfigurować metryki i utworzyć pulpit nawigacyjny
+ Jak utworzyć alerty w określonych progach

Azure Monitor Key Vault łączy zarówno dzienniki, jak i metryki, aby zapewnić globalne rozwiązanie do monitorowania. [Dowiedz się więcej na temat Azure Monitor usługi Key Vualt tutaj](https://docs.microsoft.com/azure/azure-monitor/insights/key-vault-insights-overview#introduction-to-azure-monitor-for-key-vault)

## <a name="basic-key-vault-metrics-to-monitor"></a>Podstawowe metryki Key Vault do monitorowania

+ Dostępność magazynu  
+ Nasycenie magazynu 
+ Opóźnienie interfejsu API usługi 
+ Całkowita liczba trafień interfejsu API usługi (filtrowanie według typu działania) 
+ Kody błędów (filtrowanie według kodu stanu) 

**Dostępność magazynu** — ta Metryka powinna zawsze wynosić 100% to jest ważna Metryka do monitorowania, ponieważ może ona szybko pokazać, Jeśli Twój Magazyn kluczy napotkał awarię. 

**Nasycenie magazynu** — liczba żądań na sekundę, które może obsłużyć Magazyn kluczy, zależy od typu wykonywanej operacji. Niektóre operacje magazynu mają niższą wartość progową żądań na sekundę. Ta Metryka agreguje całkowite użycie magazynu kluczy dla wszystkich typów operacji w celu uzyskania wartości procentowej, która wskazuje bieżące użycie magazynu kluczy. Aby uzyskać pełną listę limitów usługi magazynu kluczy, zobacz następujący dokument. [Limity usługi Azure Key Vault](service-limits.md)

**Opóźnienie interfejsu API usługi** — ta Metryka przedstawia średni czas oczekiwania wywołań magazynu kluczy mierzony przez usługę. Nie obejmuje czas zużyty przez klienta ani przez sieć między klientem i usługą.

**Całkowita liczba trafień interfejsu API** — ta Metryka przedstawia wszystkie wywołania w magazynie kluczy. Ułatwi to zidentyfikowanie aplikacji wywołujących Magazyn kluczy. 

**Kody błędów** — ta Metryka pokazuje, czy w magazynie kluczy występuje nietypowa ilość błędów. Aby zapoznać się z pełną listą kodów błędów i wskazówek dotyczących rozwiązywania problemów, zobacz następujący dokument. [Kody błędów interfejsu API REST Azure Key Vault](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Jak skonfigurować metryki i utworzyć pulpit nawigacyjny

1. Zaloguj się w witrynie Azure Portal
2. Przejdź do Key Vault
3. Wybierz **metryki** w obszarze **monitorowanie** 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, który podświetla opcję metryki w sekcji monitorowanie.](../media/alert-1.png)

4. Zaktualizuj tytuł wykresu, który ma być widoczny na pulpicie nawigacyjnym. 
5. Wybierz zakres. W tym przykładzie wybierzemy pojedynczy magazyn kluczy. 
6. Wybierz metrykę **ogólnej dostępności** **i agregacji** magazynu 
7. Zaktualizuj zakres czasu do ostatnich 24 godzin i zaktualizuj poziom szczegółowości czasu do 1 minuty. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający ogólną metrykę dostępności magazynu.](../media/alert-2.png)

8. Powtórz powyższe kroki w celu uzyskania nasycenia magazynu i metryk opóźnienia interfejsu API usługi. Wybierz pozycję **Przypnij do pulpitu nawigacyjnego** , aby zapisać metryki na pulpicie nawigacyjnym. 

> [!IMPORTANT]
> Wybierz pozycję "Przypnij do pulpitu nawigacyjnego" i Zapisz każdą skonfigurowaną metrykę. Jeśli opuścisz stronę i wrócisz do niej bez zapisywania, zmiany konfiguracji zostaną utracone. 

9. Aby monitorować wszystkie typy operacji w magazynie kluczy, użyj metryki **łącznej liczby trafień interfejsu API usługi** i wybierz pozycję **Zastosuj podział według typu działania**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący przycisk Zastosuj podział.](../media/alert-3.png)

10. Aby monitorować kody błędów w magazynie kluczy, użyj metryki **łącznej liczby wyników interfejsu API usługi** i wybierz pozycję **Zastosuj podział według typu działania**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający wybraną metrykę łącznej liczby wyników interfejsu API usługi.](../media/alert-4.png)

Teraz masz pulpit nawigacyjny, który będzie wyglądać następująco. Możesz kliknąć trzy kropki w prawym górnym rogu każdego kafelka i można zmienić ich rozmiar i rozmiary, gdy jest to potrzebne. 

Po zapisaniu i opublikowaniu pulpitu nawigacyjnego zostanie utworzony nowy zasób w ramach subskrypcji platformy Azure. Zobaczysz ją w dowolnym momencie, wyszukując "udostępniony pulpit nawigacyjny". 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający opublikowany pulpit nawigacyjny.](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Jak skonfigurować alerty na Key Vault 

W tej sekcji opisano sposób konfigurowania alertów w magazynie kluczy, dzięki czemu można ostrzec zespół o konieczności podjęcia działań natychmiast, jeśli Magazyn kluczy jest w złej kondycji. Można skonfigurować alerty, które wysyłają wiadomości e-mail, najlepiej do zespołu DL, wyzwalają powiadomienia w usłudze Event Grid lub dzwonić lub wysyłają tekst do numeru telefonu. Możesz również wybrać alerty statyczne na podstawie ustalonej wartości lub alertu dynamicznego, który będzie powiadamiać o tym, czy monitorowana Metryka przekracza średni limit czasu magazynu kluczy określoną liczbę razy w określonym zakresie. 

> [!IMPORTANT]
> Należy pamiętać, że nowo skonfigurowane alerty mogą zacząć wysyłać powiadomienia do 10 minut. 

### <a name="configure-an-action-group"></a>Konfigurowanie grupy akcji 

Grupa akcji to konfigurowalna lista powiadomień i właściwości.

1. Zaloguj się w witrynie Azure Portal
2. Wyszukaj **alerty** w polu wyszukiwania
3. Wybierz pozycję **Zarządzaj akcjami**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, który podświetla przycisk Zarządzaj akcjami.](../media/alert-6.png)

4. Wybierz pozycję **+ Dodaj grupę akcji**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, który wyróżnia przycisk + Dodaj grupę akcji.](../media/alert-7.png)

5. Wybierz **Typ akcji** dla grupy akcji. W tym przykładzie utworzymy alert e-mail.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, który wyróżnia pola niezbędne do dodania grupy akcji.](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący, co jest potrzebne do dodania alertu e-mail lub wiadomości SMS.](../media/alert-9.png)

6. Kliknij przycisk **OK** w dolnej części strony. Grupa akcji została pomyślnie utworzona. 

Teraz, po skonfigurowaniu grupy akcji, skonfigurujemy progi alertów magazynu kluczy. 

### <a name="configure-alert-thresholds"></a>Konfigurowanie progów alertów 

1. Wybierz zasób magazynu kluczy w Azure Portal i wybierz pozycję **alerty** w obszarze **monitorowanie**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający opcję menu alerty w sekcji monitorowanie.](../media/alert-10.png)

2. Wybierz **nową regułę alertu**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, na którym jest wyświetlany przycisk + Nowa reguła alertu.](../media/alert-11.png)

3. Wybierz zakres reguły alertu. Można wybrać pojedynczy magazyn lub wiele. 

> [!IMPORTANT]
> Należy pamiętać, że w przypadku wybrania wielu magazynów dla zakresu alertów wszystkie wybrane magazyny muszą znajdować się w tym samym regionie. Konieczne będzie skonfigurowanie osobnych reguł alertów dla magazynów w różnych regionach. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący, jak można wybrać magazyn.](../media/alert-12.png)

4. Wybierz warunki dla alertów. Można wybrać dowolny z następujących sygnałów i zdefiniować logikę do tworzenia alertów. Zespół Key Vault zaleca skonfigurowanie następujących progów alertów. 

    + Dostępność Key Vault spadnie poniżej 100% (próg statyczny)
    + Opóźnienie Key Vault jest większe niż 500 ms (próg statyczny) 
    + Całkowite nasycenie magazynu jest większe niż 75% (próg statyczny) 
    + Całkowite nasycenie magazynu przekracza średnią (próg dynamiczny)
    + Łączna liczba kodów błędów wyższa niż średnia (próg dynamiczny) 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący, gdzie wybierasz warunki dla alertów.](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Przykład 1: Konfigurowanie progu alertu statycznego dla opóźnienia

Wybierz **Ogólne opóźnienie interfejsu API usługi** jako nazwę sygnału
> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający ogólną nazwę sygnału opóźnienia interfejsu API usługi.](../media/alert-14.png)

Sprawdź następujące parametry konfiguracji.

+ Ustaw próg na **statyczny** 
+ Ustaw operator na wartość **większą niż**
+ Ustaw typ agregacji na wartość **średnia**
+ Ustaw wartość progu na **500**
+ Ustaw okres agregacji na **5 minut**
+ Ustaw częstotliwość oceny na **1 minutę**
+ Wybierz pozycję **gotowe**  

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, który wyróżnia skonfigurowaną logikę alertów.](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Przykład 2: Konfigurowanie dynamicznego progu alertu dla nasycenia magazynu 

Gdy używasz alertu dynamicznego, będziesz mieć możliwość wyświetlenia danych historycznych wybranego magazynu kluczy. Niebieski obszar reprezentuje średnie użycie magazynu kluczy. Czerwony obszar pokazuje skoki, które wywołały alert, pod warunkiem, że są spełnione inne kryteria konfiguracji alertów. Czerwona kropki pokazują wystąpienia naruszeń, w przypadku których kryteria alertu zostały spełnione w przedziale czasu. Można ustawić alert, aby uruchomić po określonej liczbie naruszeń w określonym czasie. Jeśli nie chcesz uwzględniać przeszłych danych, istnieje możliwość wykluczenia starych danych poniżej w ustawieniach zaawansowanych. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający wykres całkowitego nasycenia magazynu.](../media/alert-16.png)

Sprawdź następujące parametry konfiguracji.

+ Ustaw próg na **dynamiczny** 
+ Ustaw operator na wartość **większą niż**
+ Ustaw typ agregacji na wartość **średnia**
+ Ustaw wartość progową na **Średni**
+ Ustaw okres agregacji na **5 minut**
+ Ustaw częstotliwość oceny na **1 minutę**
+ **Opcjonalne** Skonfiguruj ustawienia zaawansowane 
+ Wybierz pozycję **gotowe**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający Azure Portal](../media/alert-17.png)

5. Dodaj skonfigurowaną grupę akcji

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący sposób dodawania grupy akcji.](../media/alert-18.png)

6. Włączanie alertu i przypisywanie ważności

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu pokazujący, gdzie włączyć alert i przypisać ważność.](../media/alert-19.png)

7. Tworzenie alertu 

### <a name="example-email-alert"></a>Przykładowy alert e-mail 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu, który wyróżnia informacje konieczne do skonfigurowania alertu e-mail.](../media/alert-20.png)

## <a name="next-steps"></a>Następne kroki

Gratulacje, pomyślnie utworzono pulpit nawigacyjny monitorowania i skonfigurowano alerty dla magazynu kluczy. Po wykonaniu wszystkich powyższych czynności należy otrzymywać alerty e-mail, gdy Twój Magazyn kluczy spełnia skonfigurowane kryteria alertu. Przykład przedstawiono poniżej. Skorzystaj z narzędzi, które zostały skonfigurowane w tym artykule, aby aktywnie monitorować kondycję magazynu kluczy. 


