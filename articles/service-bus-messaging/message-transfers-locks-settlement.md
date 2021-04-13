---
title: Azure Service Bus transfery, blokady i rozliczanie komunikatów
description: Ten artykuł zawiera omówienie Azure Service Bus transferów komunikatów, blokad i operacji rozliczeń.
ms.topic: article
ms.date: 04/12/2021
ms.custom: devx-track-csharp
ms.openlocfilehash: 6fbbcbf4a1920ee0e66a956443dcfb8a6a17af43
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107306776"
---
# <a name="message-transfers-locks-and-settlement"></a>Transferowanie, blokady i uzgadnianie komunikatów

Centralną możliwością brokera komunikatów, takiego jak Service Bus, jest akceptowanie komunikatów do kolejki lub tematu i przechowywanie ich w celu późniejszego pobrania. *Send* to termin, który jest często używany do przesyłania wiadomości do brokera komunikatów. *Odbieranie* jest terminem często używanym do przesyłania wiadomości do klienta pobierającego.

Gdy klient wysyła komunikat, zazwyczaj chce wiedzieć, czy wiadomość została prawidłowo przetransferowana do i zaakceptowana przez brokera, czy też w przypadku wystąpienia pewnego błędu. To pozytywne lub ujemne potwierdzenie rozliczy klienta i brokera o stanie transferu wiadomości. Tak więc jest to termin *rozliczenia*.

Podobnie, gdy Broker przesyła komunikat do klienta, Broker i klient chcą ustalić, czy komunikat został pomyślnie przetworzony i w związku z tym może zostać usunięty lub czy dostarczanie lub przetwarzanie komunikatu nie powiodło się, a w związku z tym komunikat może być dostarczony ponownie.

## <a name="settling-send-operations"></a>Rozliczanie operacji wysyłania

Przy użyciu dowolnego z obsługiwanych Service Bus klientów interfejsu API operacje wysyłania do Service Bus są zawsze jawnie rozliczane, co oznacza, że operacja interfejsu API czeka na nadejście wyniku Service Bus, a następnie kończy operację wysyłania.

Jeśli komunikat zostanie odrzucony przez Service Bus, odrzucenie zawiera wskaźnik błędu i tekst z **identyfikatorem śledzenia** . Odrzucanie zawiera również informacje o tym, czy można ponowić próbę wykonania operacji z oczekiwaniami. W kliencie te informacje są włączane do wyjątku i wywoływane do obiektu wywołującego operacji wysyłania. Jeśli wiadomość została zaakceptowana, operacja zostanie dyskretnie ukończona.

W przypadku korzystania z protokołu AMQP, który jest wyłącznym protokołem dla klientów .NET Standard, Java, JavaScript, Python i go, i [opcją dla klienta .NET Framework](service-bus-amqp-dotnet.md), transfery komunikatów i rozliczenia są potoku i asynchroniczne. Zalecamy używanie wariantów interfejsu API modelu programowania asynchronicznego.

Nadawca może umieścić kilka komunikatów w sieci w krótkim czasie, bez konieczności oczekiwania na potwierdzenie poszczególnych komunikatów, tak jak w przypadku protokołu SBMP lub HTTP 1,1. Operacje wysyłania asynchronicznego są ukończone, gdy odpowiednie komunikaty są akceptowane i przechowywane, w jednostkach partycjonowanych lub gdy operacje wysyłania do różnych jednostek nakładają się na siebie. Ukończenie może również wystąpić z oryginalnego zamówienia wysyłania.

Strategia obsługi wyników operacji wysyłania może mieć bezpośredni i znaczący wpływ na wydajność aplikacji. Przykłady w tej sekcji są zapisywane w języku C# i stosują się do przyszłości Java, języka Java mono, języka JavaScript niesie obietnice zwiększenia i równoważnych koncepcji w innych językach.

Jeśli aplikacja generuje szereg komunikatów, które zostały przedstawione w tym miejscu za pomocą zwykłej pętli, i musiało oczekiwać na ukończenie każdej operacji wysyłania przed wysłaniem kolejnego komunikatu, synchronicznego lub asynchronicznego obiektu API

Przy założeniu, że liczba opóźnień rozruchowych protokołu TCP (70-milisekundy) z lokacji lokalnej do Service Bus i nastąpi zaledwie 10 ms Service Bus do akceptowania i przechowywania poszczególnych komunikatów, następująca pętla zajmie co najmniej 8 sekund, nie zlicza czasu transferu ładunku lub potencjalnych efektów przeciążenia trasy:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Jeśli aplikacja uruchamia 10 asynchronicznych operacji wysyłania w bezpośrednim pomyślnym sukcesie i oczekuje na ich zakończenie oddzielnie, czas rundy dla tych 10 operacji wysyłania nakłada się na siebie. 10 komunikatów jest przesyłanych natychmiast, potencjalnie nawet przez udostępnienie ramek TCP, a całkowity czas przesyłania jest w dużym stopniu zależny od czasu potrzebnego na sieć do uzyskania komunikatów przesyłanych do brokera.

W przypadku takich samych założeń jak dla poprzedniej pętli łączny czas wykonywania dla następującej pętli może pozostać w jednej sekundzie:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Należy pamiętać, że wszystkie asynchroniczne modele programowania korzystają z pewnej kolejki pracy w pamięci, która przechowuje oczekujące operacje. Po powrocie interfejsu API wysyłania zadanie Wyślij jest umieszczane w kolejce w tej kolejce roboczej, ale gest protokołu zaczyna się tylko wtedy, gdy zadanie zostanie uruchomione. W przypadku kodu, który ma na celu wypychanie obciążeń komunikatów i w przypadku, gdy niezawodność jest istotna, należy zadbać o to, aby nie było zbyt wiele komunikatów jednocześnie "w locie", ponieważ wszystkie wysłane komunikaty zajmują pamięć, dopóki nie zostaną rzeczywiście umieszczone w sieci.

Semafory, jak pokazano w poniższym fragmencie kodu w języku C#, są obiektami synchronizacji, które umożliwiają takie Ograniczanie poziomu aplikacji w razie konieczności. Użycie semafora pozwala na co najwyżej 10 komunikatów jednocześnie. Jeden z 10 dostępnych blokad semaforów jest pobierany przed wysłaniem i zostaje ogłoszony jako zakończenie wysyłania. 11 przechodzenie przez pętlę czeka do momentu, aż co najmniej jeden z wcześniejszych operacji wysłania zakończył pracę, a następnie udostępnił blokadę:

```csharp
var semaphore = new SemaphoreSlim(10);

var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  await semaphore.WaitAsync();

  tasks.Add(client.SendAsync(…).ContinueWith((t)=>semaphore.Release()));
}
await Task.WhenAll(tasks);
```

Aplikacje **nigdy nie** powinny inicjować asynchronicznej operacji wysyłania w sposób "pożar i zapomnij" bez pobierania wyniku operacji. Może to spowodować załadowanie wewnętrznej i niewidocznej kolejki zadań do wyczerpania pamięci i uniemożliwienie aplikacji wykrywania błędów wysyłania:

```csharp
for (int i = 0; i < 100; i++)
{

  client.SendAsync(message); // DON’T DO THIS
}
```

Za pomocą klienta AMQP niskiego poziomu, Service Bus również akceptuje "wstępnie rozliczane" transfery. Przeprowadzony transfer jest operacją typu "Uruchom i zapomnij", dla której wynik nie jest raportowany z powrotem do klienta, a komunikat jest traktowany jako rozliczony podczas wysyłania. Brak informacji zwrotnych dla klienta oznacza również, że nie ma dostępnych danych do wykonania dla celów diagnostycznych, co oznacza, że ten tryb nie kwalifikuje się do pomocy za pośrednictwem pomocy technicznej platformy Azure.

## <a name="settling-receive-operations"></a>Rozliczanie operacji odbioru

W przypadku operacji odbioru Service Bus klienci interfejsu API włączają dwa różne tryby jawne: *Receive-and-DELETE* i *Peek-Lock*.

### <a name="receiveanddelete"></a>ReceiveAndDelete

Tryb **odbierania i usuwania** instruuje brokera, aby uwzględniał wszystkie komunikaty wysyłane do klienta odbierającego jako rozliczone podczas wysyłania. Oznacza to, że wiadomość jest uważana za zużytą zaraz po umieszczeniu jej w sieci przez brokera. Jeśli transfer komunikatów zakończy się niepowodzeniem, komunikat zostanie utracony.

W tym trybie jest to, że odbiornik nie musi podejmować dalszych działań w komunikacie i nie jest również wolniejszy przez oczekiwanie na wynik rozliczenia. Jeśli dane zawarte w poszczególnych wiadomościach mają niską wartość i/lub mają tylko znaczenie dla bardzo krótkiego czasu, ten tryb jest rozsądny.

### <a name="peeklock"></a>PeekLock

Tryb **blokady wglądu** instruuje brokera, że klient odbierający chce jawnie rozliczyć odebrane komunikaty. Komunikat jest udostępniany do przetworzenia przez odbiorcę, a poza wyłączną blokadą w usłudze, tak że inne, konkurujące odbiorcy nie mogą go zobaczyć. Czas trwania blokady jest początkowo zdefiniowany na poziomie kolejki lub subskrypcji i może zostać rozszerzony przez klienta będącego właścicielem blokady. Aby uzyskać szczegółowe informacje na temat odnawiania blokad, zobacz sekcję [Odnów blokady](#renew-locks) w tym artykule. 

Po zablokowaniu komunikatu inni klienci odbierający z tej samej kolejki lub subskrypcji mogą wykonać blokadę i pobrać kolejne dostępne komunikaty, które nie są objęte blokadą aktywną. Gdy blokada komunikatu jest jawnie wydawana lub gdy blokada wygaśnie, komunikat jest wyświetlany na początku lub w najbliższym miejscu kolejności pobierania na potrzeby ponownej dostawy.

Gdy komunikat jest wielokrotnie wydawany przez odbiorniki lub zezwolił na zablokowanie przez określoną liczbę razy ([Maksymalna liczba dostaw](service-bus-dead-letter-queues.md#maximum-delivery-count)), wiadomość zostanie automatycznie usunięta z kolejki lub subskrypcji i umieszczona w skojarzonej kolejce utraconych wiadomości.

Klient odbierający inicjuje rozliczenie otrzymanej wiadomości z pozytywnym potwierdzeniem, gdy wywołuje `Complete` interfejs API dla wiadomości. Wskazuje brokerowi, że wiadomość została pomyślnie przetworzona, a wiadomość zostanie usunięta z kolejki lub subskrypcji. Broker odpowiada na zamiar rozliczenia odbiorcy z odpowiedzią wskazującą, czy można wykonać rozliczenie.

Gdy klient otrzymujący nie może przetworzyć komunikatu, ale chce, aby komunikat został ponownie dostarczony, może jawnie zażądać, aby komunikat został wystawiony i odblokowany natychmiast, przez wywołanie `Abandon` interfejsu API dla wiadomości lub wykonanie operacji blokowania.

Jeśli klient odbierający nie może przetworzyć komunikatu i wie, że ponowne dostarczenie komunikatu i ponowienie próby wykonania operacji nie powiedzie się, może odrzucić komunikat, który przenosi go do kolejki utraconych wiadomości, wywołując `DeadLetter` interfejs API w komunikacie, co umożliwia również ustawienie niestandardowej właściwości, w tym kodu przyczyny, który można pobrać z wiadomości z kolejki utraconych wiadomości.

Szczególnym przypadkiem rozliczenia jest odroczenie, który został omówiony w [osobnym artykule](message-deferral.md).

`Complete`Operacje, `Deadletter` , lub `RenewLock` mogą kończyć się niepowodzeniem z powodu problemów z siecią, jeśli zatrzymywana blokada wygasła lub istnieją inne warunki po stronie usługi, które uniemożliwiają rozliczenie. W jednym z tych przypadków usługa wysyła negatywne potwierdzenie, które powierzchnie jako wyjątek w klientach interfejsu API. Jeśli powodem jest przerwane połączenie sieciowe, blokada zostanie porzucona, ponieważ Service Bus nie obsługuje odzyskiwania istniejących linków AMQP w innym połączeniu.

Jeśli to `Complete` się nie powiedzie, to jest zwykle na bardzo zakończenie obsługi komunikatów, a w niektórych przypadkach po upływie minut działania przetwarzania aplikacja otrzymująca może zdecydować, czy zachowuje stan pracy i ignoruje ten sam komunikat, gdy zostanie dostarczony po raz pierwszy, czy też tosses wynik pracy i ponawia próbę po ponownym dostarczeniu komunikatu.

Typowym mechanizmem służącym do identyfikowania powielonych dostaw komunikatów jest sprawdzenie identyfikatora komunikatu, który może i powinien być ustawiony przez nadawcę na unikatową wartość, ewentualnie wyrównany przy użyciu identyfikatora z procesu źródłowego. Harmonogram zadań prawdopodobnie ustawi Identyfikator komunikatu na identyfikator zadania, które ma zostać przypisane do procesu roboczego przez danego pracownika, a proces roboczy zignoruje drugie wystąpienie przypisania zadania, jeśli to zadanie zostało już wykonane.

> [!IMPORTANT]
> Należy pamiętać, że blokada, która PeekLock uzyskuje w komunikacie, jest nietrwała i może zostać utracona w następujących warunkach
>   * Aktualizacja usługi
>   * Aktualizacja systemu operacyjnego
>   * Zmiana właściwości jednostki (kolejki, tematu, subskrypcji) podczas utrzymywania blokady.
>
> Gdy blokada zostanie utracona, Azure Service Bus wygeneruje element LockLostException, który zostanie nadany w kodzie aplikacji klienta. W tym przypadku domyślna logika ponowienia klienta powinna być automatycznie uruchamiana i spróbuj ponownie wykonać operację.

## <a name="renew-locks"></a>Odnów blokady
Wartość domyślna dla czasu trwania blokady wynosi **30 sekund**. Możesz określić inną wartość czasu trwania blokady na poziomie kolejki lub subskrypcji. Klient, który posiada blokadę, może odnowić blokadę wiadomości za pomocą metod w obiekcie odbiornika. Zamiast tego można użyć funkcji automatycznego odblokowywania, w której można określić czas trwania, dla którego ma zostać zastosowana blokada. 

## <a name="next-steps"></a>Następne kroki
- Szczególnym przypadkiem rozliczenia jest odroczenie. Aby uzyskać szczegółowe informacje, zobacz [odroczenie komunikatu](message-deferral.md) . 
- Aby dowiedzieć się więcej o utraconych wiadomościach, zobacz [kolejki utraconych wiadomości](service-bus-dead-letter-queues.md).
- Aby dowiedzieć się więcej na temat Service Bus komunikatów ogólnie, zobacz sekcję [Service Bus Queues, tematy i subskrypcje](service-bus-queues-topics-subscriptions.md)