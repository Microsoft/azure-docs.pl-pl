---
title: Azure Service Bus — wygaśnięcie komunikatu
description: W tym artykule wyjaśniono, jak wygasa i czas na żywo komunikatów Azure Service Bus. Po upływie tego terminu wiadomość nie zostanie już dostarczona.
ms.topic: conceptual
ms.date: 02/17/2021
ms.openlocfilehash: 505a041d2f6129b159166e9f99ce7fef779e1e66
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101698369"
---
# <a name="message-expiration-time-to-live"></a>Wygaśnięcie komunikatu (czas wygaśnięcia)
Ładunek w wiadomości lub polecenie lub zapytanie wysyłane przez komunikat do odbiorcy jest prawie zawsze uzależnione od formy wygaśnięcia na poziomie aplikacji. Po upływie tego terminu zawartość nie jest już dostarczana lub żądana operacja nie jest już wykonywana.

W przypadku środowisk deweloperskich i testowych, w których kolejki i tematy są często używane w kontekście częściowych przebiegów aplikacji lub części aplikacji, pożądane jest również, aby przełączenie komunikatów testowych zostało wykonane automatycznie, aby następny przebieg testu mógł rozpocząć czyszczenie.

Wygaśnięcie poszczególnych komunikatów może być kontrolowane przez ustawienie właściwości System **Time-to-Live** , która określa względny czas trwania. Wygaśnięcie jest bezwzględnym chwilą, gdy wiadomość zostanie przejdzie do kolejki w jednostce. Po upływie tego czasu Właściwość **wygasa-at-UTC** przyjmuje wartość w **kolejce czasu UTC**-Time-  +  **to-Live**. Ustawienie czasu wygaśnięcia (TTL) dla komunikatu obsługiwanego przez brokera nie jest wymuszane w przypadku, gdy żaden klient aktywnie nasłuchuje.

Po **upływie czasu wygaśnięcia-at-UTC** wiadomości stają się nieodpowiednie do pobrania. Wygaśnięcie nie ma wpływu na komunikaty, które są aktualnie zablokowane do dostarczenia. Te komunikaty są nadal obsługiwane normalnie. Jeśli blokada wygaśnie lub komunikat zostanie porzucony, wygaśnięcie będzie obowiązywać natychmiast.

Gdy wiadomość jest zablokowana, aplikacja może być w posiadaniu wiadomości, która wygasła. Bez względu na to, czy aplikacja jest gotowa do przetworzenia, czy też decyduje o odrzuceniu komunikatu do realizatora.

Zalecamy ustawienie wartości **czasu wygaśnięcia** w wiadomości, która będzie w godzinach lub dniach. Jeśli ustawisz ją na niską wartość w sekundach lub milisekundach, komunikat może wygasnąć, zanim klienci mają szansę na jej używanie. 

## <a name="entity-level-expiration"></a>Wygaśnięcie na poziomie jednostki
Wszystkie komunikaty wysyłane do kolejki lub tematu podlegają domyślnym wygaśnięciu ustawionym na poziomie jednostki. Można ją również ustawić w portalu podczas tworzenia i zmieniać się później. Domyślne wygaśnięcie jest używane dla wszystkich komunikatów wysyłanych do jednostki, w których czas wygaśnięcia nie jest jawnie ustawiony. Domyślne wygaśnięcie również działa jako pułap dla wartości czasu wygaśnięcia. Komunikaty, które mają dłuższy czas wygaśnięcia, od wartości domyślnej są przywracane w trybie dyskretnym do wartości domyślnej komunikat Time-to-Live.

> [!NOTE]
> Domyślna wartość czasu wygaśnięcia dla komunikatu obsługiwanego przez brokera to największa możliwa wartość dla pojedynczej 64-bitowej liczby całkowitej, jeśli nie określono inaczej.
>
> W przypadku jednostek obsługi komunikatów (kolejek i tematów) domyślny czas wygaśnięcia jest również największą wartością dla wielowartościowej 64-bitowej liczby całkowitej dla warstw Service Bus standardowa i Premium. W przypadku warstwy **podstawowa** domyślny czas wygaśnięcia (również maksymalny) to **14 dni**.

Wygasłe komunikaty można opcjonalnie przenieść do [kolejki utraconych](service-bus-dead-letter-queues.md)wiadomości. To ustawienie można skonfigurować programowo lub za pomocą Azure Portal. Jeśli opcja jest wyłączona, wygasłe komunikaty są usuwane. Wygasłe wiadomości przenoszone do kolejki utraconych wiadomości można odróżnić od innych utraconych komunikatów, oceniając Właściwość [Przyczyna utraconych](service-bus-dead-letter-queues.md#moving-messages-to-the-dlq) wiadomości, którą przechowuje Broker w sekcji właściwości użytkownika. 

W powyższym przypadku, gdy komunikat jest chroniony przed wygaśnięciem, a w obszarze Zablokuj i jeśli flaga jest ustawiona w jednostce, komunikat jest przenoszony do kolejki utraconych wiadomości, ponieważ blokada została porzucona lub wygaśnie. Nie jest on jednak przenoszony w przypadku pomyślnego rozliczenia komunikatu, a następnie zakłada, że aplikacja została pomyślnie obsłużona, a nie na mocy nominalnej.

Kombinacja czasu wygaśnięcia i automatycznych (i transakcyjnych) utraconych wiadomości, które są ważne, są cennym narzędziem służącym do ustanowienia pewności, czy zadanie przekazane do procedury obsługi lub grupy programów obsługi w ramach terminu ostatecznego jest pobierane do przetwarzania po osiągnięciu terminu ostatecznego.

Rozważmy na przykład witrynę sieci Web, która wymaga niezawodnego wykonywania zadań w zasobie z ograniczeniami skalowalnymi, a czasami powoduje, że ruch jest osiągany w zależności od dostępności tego zaplecza. W regularnych przypadkach program obsługi po stronie serwera dla przesłanych danych użytkownika wypycha informacje do kolejki, a następnie otrzymuje odpowiedź potwierdzającą pomyślne obsłudze transakcji w kolejce odpowiedzi. W przypadku przepełnienia ruchu, gdy program obsługi zaplecza nie może przetworzyć swoich elementów zaległości w czasie, wygasłe zadania są zwracane w kolejce utraconych wiadomości. Użytkownik interaktywny może zostać powiadomiony o tym, że żądana operacja potrwa nieco dłużej niż zwykle, a żądanie może zostać umieszczone w innej kolejce dla ścieżki przetwarzania, w której wyniki przetwarzania ostatecznego są wysyłane do użytkownika za pośrednictwem poczty e-mail. 


## <a name="temporary-entities"></a>Jednostki tymczasowe

Service Bus kolejkami, tematami i subskrypcjami można utworzyć jako jednostki tymczasowe, które są automatycznie usuwane, gdy nie były używane przez określony czas.
 
Automatyczne czyszczenie jest przydatne w scenariuszach deweloperskich i testowych, w których jednostki są tworzone dynamicznie i nie są czyszczone po ich użyciu z powodu przerwy w działaniu testu lub debugowania. Jest on również przydatny, gdy aplikacja tworzy jednostki dynamiczne, takie jak kolejka odpowiedzi, do odbierania odpowiedzi z powrotem do procesu serwera sieci Web lub do innego stosunkowo krótkotrwałego obiektu, w którym trudno wyczyścić te jednostki, gdy wystąpienie obiektu zniknie.

Funkcja jest włączona przy użyciu właściwości **autodelete dla bezczynnej** przestrzeni nazw. Ta właściwość jest ustawiona na czas trwania, przez który jednostka musi być bezczynna (nieużywana), zanim zostanie automatycznie usunięta. Minimalna wartość tej właściwości to 5.
 
## <a name="idleness"></a>Bezczynność

Poniżej przedstawiono informacje, które są uważane za bezczynne jednostki (kolejki, tematy i subskrypcje):

- Kolejki
    - Brak wysłanych  
    - Nie odebrano  
    - Brak aktualizacji kolejki  
    - Brak zaplanowanych komunikatów  
    - Brak przeglądania/wglądu 
- Tematy  
    - Brak wysłanych  
    - Brak aktualizacji tematu  
    - Brak zaplanowanych komunikatów 
- Subskrypcje
    - Nie odebrano  
    - Brak aktualizacji subskrypcji  
    - Nie dodano żadnych nowych reguł do subskrypcji  
    - Brak przeglądania/wglądu  
 

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
