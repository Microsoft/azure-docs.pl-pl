---
title: Monitorowanie i ostrzeganie usługi Azure Key Vault | Dokumenty firmy Microsoft
description: Utwórz pulpit nawigacyjny, aby monitorować kondycję magazynu kluczy i konfigurować alerty.
services: key-vault
author: ShaneBala-keyvault
manager: ravijan
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: general
ms.topic: conceptual
ms.date: 04/06/2020
ms.author: sudbalas
Customer intent: As a key vault administrator, I want to learn the options available to monitor the health of my vaults
ms.openlocfilehash: cc0d969ff6eb76732768dfed2826762920ae9e62
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81726945"
---
# <a name="monitoring-and-alerting-for-azure-key-vault"></a>Monitorowanie i alerty dotyczące usługi Azure Key Vault

## <a name="overview"></a>Omówienie

Po rozpoczęciu używania magazynu kluczy do przechowywania wpisów tajnych produkcji, ważne jest, aby monitorować kondycję magazynu kluczy, aby upewnić się, że usługa działa zgodnie z przeznaczeniem. Po rozpoczęciu skalowania usługi liczba żądań wysłanych do magazynu kluczy wzrośnie. Ma to potencjał, aby zwiększyć opóźnienie żądań i w skrajnych przypadkach spowodować, że żądania mają być ograniczone, co wpłynie na wydajność usługi. Należy również otrzymywać alerty, jeśli magazyn kluczy wysyła niezwykłą liczbę kodów błędów, dzięki czemu można szybko otrzymywać powiadomienia o wszelkich problemach z zasadami dostępu lub konfiguracją zapory. Niniejszy dokument obejmie następujące tematy:

+ Podstawowe metryki usługi Key Vault do monitorowania
+ Jak skonfigurować metryki i utworzyć pulpit nawigacyjny 
+ Jak tworzyć alerty przy określonych progach 

## <a name="basic-key-vault-metrics-to-monitor"></a>Podstawowe metryki usługi Key Vault do monitorowania

+ Dostępność przechowalni  
+ Nasycenie skarbca 
+ Opóźnienie interfejsu API usługi 
+ Całkowita liczba trafień interfejsu API usługi (filtrowanie według typu działania) 
+ Kody błędów (filtrowanie według kodu stanu) 

**Dostępność magazynu** — ta metryka powinna być zawsze w 100% jest to ważna metryka do monitorowania, ponieważ może szybko pokazać, czy magazyn kluczy wystąpiła awaria. 

**Nasycenie przechowalni** — liczba żądań na sekundę, które może obsługiwać magazyn kluczy, zależy od typu wykonywanej operacji. Niektóre operacje magazynu mają niższy próg żądań na sekundę. Ta metryka agreguje całkowite użycie magazynu kluczy we wszystkich typach operacji, aby wymyślić wartość procentową, która wskazuje bieżące użycie magazynu kluczy. Aby uzyskać pełną listę limitów usług magazynu kluczy, zobacz następujący dokument. [Limity usługi Azure Key Vault](service-limits.md)

**Opóźnienie interfejsu API usługi** — ta metryka pokazuje średnie opóźnienie wywołania magazynu kluczy. Chociaż magazyn kluczy może mieszcząć się w granicach usług, wysokie wykorzystanie magazynu kluczy może wprowadzić opóźnienie, które powoduje, że aplikacje niższego rzędu nie powiedzie się. 

**Całkowita liczba trafień interfejsu API** — ta metryka pokazuje wszystkie wywołania wykonane do magazynu kluczy. Pomoże to określić, które aplikacje są wywoływane do magazynu kluczy. 

**Kody błędów** — ta metryka pokaże, jeśli magazyn kluczy występuje nietypową ilość błędów. Aby uzyskać pełną listę kodów błędów i wskazówek dotyczących rozwiązywania problemów, zobacz następujący dokument. [Kody błędów interfejsu API rest usługi Azure Key Vault](rest-error-codes.md)

## <a name="how-to-configure-metrics-and-create-a-dashboard"></a>Jak skonfigurować metryki i utworzyć pulpit nawigacyjny

1. Zaloguj się w witrynie Azure Portal
2. Przejdź do przechowalni kluczy
3. Wybierz **metryki** w obszarze **Monitorowanie** 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-1.png)

4. Zaktualizuj tytuł wykresu do tego, co chcesz zobaczyć na pulpicie nawigacyjnym. 
5. Wybierz zakres. W tym przykładzie wybierzemy pojedynczy magazyn kluczy. 
6. Wybierz metrykę **Ogólna dostępność i** średnia agregacji **Avg** magazynu 
7. Zaktualizuj zakres czasu do ostatnich 24 godzin i zaktualizuj szczegółowość czasu do 1 minuty. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-2.png)

8. Powtórz powyższe kroki dla metryk nasycenia przechyłki i opóźnienia interfejsu API usługi. Wybierz **pozycję Przypnij do pulpitu nawigacyjnego,** aby zapisać dane na pulpicie nawigacyjnym. 

> [!IMPORTANT]
> Wybierz "Przypnij do pulpitu nawigacyjnego" i zapisz każdą skonfigurowany wskaźnik. Jeśli opuścisz stronę i powrócisz do niej bez zapisywania, zmiany konfiguracji zostaną utracone. 

9. Aby monitorować wszystkie typy operacji w magazynie kluczy, użyj metryki Metryka **Całkowita liczba trafień interfejsu API usługi** i wybierz pozycję Zastosuj **dzielenie według typu działania**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-3.png)

10. Aby monitorować kody błędów w magazynie kluczy, użyj metryki **Wyniki interfejsu API usługi razem** i wybierz pozycję Zastosuj **dzielenie według typu działania**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-4.png)

Teraz będziesz miał pulpit nawigacyjny, który wygląda tak. Możesz kliknąć 3 kropki w prawym górnym rogu każdego kafelka i zmienić kolejność i rozmiar kafelków zgodnie z potrzebami. 

Po zapisaniu i opublikowaniu pulpitu nawigacyjnego utworzy nowy zasób w ramach subskrypcji platformy Azure. Będziesz mógł go zobaczyć w dowolnym momencie, wyszukując "udostępniony pulpit nawigacyjny". 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-5.png)

## <a name="how-to-configure-alerts-on-your-key-vault"></a>Jak skonfigurować alerty w magazynie kluczy 

W tej sekcji pokazano, jak skonfigurować alerty w magazynie kluczy, dzięki czemu można powiadomić zespół do podjęcia działań natychmiast, jeśli magazyn kluczy jest w stanie w złej kondycji. Można skonfigurować alerty, które wysyłają wiadomość e-mail, najlepiej do zespołu DL, uruchamiają powiadomienie o siatce zdarzeń lub dzwonią lub wysyłają SMS-y na numer telefonu. Można również wybrać alerty statyczne na podstawie stałej wartości lub alert dynamiczny, który będzie ostrzegał, jeśli monitorowana metryka przekracza średni limit magazynu kluczy określoną liczbę razy w zdefiniowanym zakresie czasu. 

> [!IMPORTANT]
> Należy pamiętać, że rozpoczęcie wysyłania powiadomień może potrwać do 10 minut. 

### <a name="configure-an-action-group"></a>Konfigurowanie grupy akcji 

Grupa akcji to konfigurowalna lista powiadomień i właściwości.

1. Zaloguj się w witrynie Azure Portal
2. Wyszukiwanie **alertów** w polu wyszukiwania
3. Wybierz pozycję **Zarządzaj akcjami**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-6.png)

4. Wybierz **+ Dodaj grupę akcji**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-7.png)

5. Wybierz **typ akcji** dla grupy akcji. W tym przykładzie utworzymy alert e-mail.

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-8.png)

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-9.png)

6. Kliknij przycisk **OK** w dolnej części strony. Pomyślnie utworzono grupę akcji. 

Po skonfigurowaniu grupy akcji skonfigurujemy progi alertów magazynu kluczy. 

### <a name="configure-alert-thresholds"></a>Konfigurowanie progów alertów 

1. Wybierz zasób magazynu kluczy w witrynie Azure portal i wybierz **pozycję Alerty** w obszarze **Monitorowanie**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-10.png)

2. Wybierz **nową regułę alertu**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-11.png)

3. Wybierz zakres reguły alertów. Można wybrać pojedynczy przechowalnię lub wiele. 

> [!IMPORTANT]
> Należy pamiętać, że podczas wybierania wielu magazynów dla zakresu alertów wszystkie wybrane magazyny muszą znajdować się w tym samym regionie. Należy skonfigurować oddzielne reguły alertów dla magazynów w różnych regionach. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-12.png)

4. Wybierz warunki alertów. Można wybrać dowolny z następujących sygnałów i zdefiniować logikę alertów. Zespół usługi Key Vault zaleca skonfigurowanie następujących progów alertów. 

    + Dostępność magazynu kluczy spada poniżej 100% (próg statyczny)
    + Opóźnienie przechowalni kluczy jest większe niż 500 m s (próg statyczny) 
    + Ogólne nasycenie przechowalni jest większe niż 75% (próg statyczny) 
    + Ogólne nasycenie przechyłki przechyłania przekracza średnią (próg dynamiczny)
    + Całkowite kody błędów wyższe niż średnia (próg dynamiczny) 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-13.png)

### <a name="example-1-configuring-a-static-alert-threshold-for-latency"></a>Przykład 1: Konfigurowanie statycznego progu alertu dla opóźnienia

Jako nazwę sygnału wybierz **pozycję Ogólne opóźnienie interfejsu API** usługi
> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-14.png)

Zobacz następujące parametry konfiguracji.

+ Ustawianie wartości progowej na **statyczną** 
+ Ustaw operatora na **większą niż**
+ Ustawianie typu agregacji na **średni**
+ Ustawianie wartości progowej na **500**
+ Ustawianie okresu agregacji na **5 minut**
+ Ustawianie częstotliwości oceny na **1 minutę**
+ Wybierz **gotowe**  

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-15.png)

### <a name="example-2-configuring-a-dynamic-alert-threshold-for-vault-saturation"></a>Przykład 2: Konfigurowanie dynamicznego progu alertu dla nasycenia przechowalni 

Korzystając z alertu dynamicznego, będą mogli zobaczyć dane historyczne wybranego magazynu kluczy. Niebieski obszar reprezentuje średnie użycie magazynu kluczy. Czerwony obszar pokazuje skoki, które wyzwoliłyby alert pod warunkiem spełnienia innych kryteriów w konfiguracji alertu. Czerwone kropki pokazują wystąpienia naruszeń, w których kryteria alertu zostały spełnione w zagregowanym oknie czasu. Alert można ustawić tak, aby uruchamiał się po określonej liczbie naruszeń w określonym czasie. Jeśli nie chcesz dołączać danych z przeszłości, istnieje możliwość wykluczenia starych danych poniżej w ustawieniach zaawansowanych. 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-16.png)

Zobacz następujące parametry konfiguracji.

+ Ustawianie wartości progowej na **dynamiczne** 
+ Ustaw operatora na **większą niż**
+ Ustawianie typu agregacji na **średni**
+ Ustawianie czułości progu na **średnią**
+ Ustawianie okresu agregacji na **5 minut**
+ Ustawianie częstotliwości oceny na **1 minutę**
+ **Opcjonalnie** Konfigurowanie ustawień zaawansowanych 
+ Wybierz **gotowe**

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-17.png)

5. Dodawanie skonfigurowanych grup akcji

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-18.png)

6. Włącz alert i przypisz ważność

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-19.png)

7. Tworzenie alertu 


## <a name="next-steps"></a>Następne kroki

Gratulacje, teraz pomyślnie utworzono pulpit nawigacyjny monitorowania i skonfigurowano alerty dla magazynu kluczy! Po wykonaniu wszystkich powyższych kroków należy otrzymywać alerty e-mail, gdy magazyn kluczy spełnia skonfigurowane kryteria alertów. Przykład przedstawiono poniżej. Użyj narzędzi skonfigurowanych w tym artykule, aby aktywnie monitorować kondycję magazynu kluczy. 

### <a name="example-email-alert"></a>Przykładowy alert e-mail 

> [!div class="mx-imgBorder"]
> ![Zrzut ekranu przedstawiający witrynę Azure portal](../media/alert-20.png)
