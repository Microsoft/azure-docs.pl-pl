---
title: Azure Service Bus transfery, blokady i rozliczanie komunikatów
description: Ten artykuł zawiera omówienie Azure Service Bus transferów komunikatów, blokad i operacji rozliczeń.
ms.topic: article
ms.date: 06/23/2020
ms.custom: devx-track-csharp
ms.openlocfilehash: fd71edd12e478bcd5f14815c105c14482cf7e2bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89020035"
---
# <a name="message-transfers-locks-and-settlement"></a>Transferowanie, blokady i uzgadnianie komunikatów

Centralną możliwością brokera komunikatów, takiego jak Service Bus, jest akceptowanie komunikatów do kolejki lub tematu i przechowywanie ich w celu późniejszego pobrania. *Send* to termin, który jest często używany do przesyłania wiadomości do brokera komunikatów. *Odbieranie* jest terminem często używanym do przesyłania wiadomości do klienta pobierającego.

Gdy klient wysyła komunikat, zazwyczaj chce wiedzieć, czy wiadomość została prawidłowo przetransferowana do i zaakceptowana przez brokera, czy też w przypadku wystąpienia pewnego błędu. To pozytywne lub ujemne potwierdzenie rozliczy klienta i brokera o stanie transferu wiadomości i jest to nazywane *rozliczeniami*.

Podobnie, gdy Broker przesyła komunikat do klienta, Broker i klient chcą ustalić, czy komunikat został pomyślnie przetworzony i w związku z tym może zostać usunięty lub czy dostarczanie lub przetwarzanie komunikatu nie powiodło się, a w związku z tym komunikat może być dostarczony ponownie.

## <a name="settling-send-operations"></a>Rozliczanie operacji wysyłania

Przy użyciu dowolnego z obsługiwanych Service Bus klientów interfejsu API operacje wysyłania do Service Bus są zawsze jawnie rozliczane, co oznacza, że operacja interfejsu API czeka na nadejście wyniku Service Bus, a następnie kończy operację wysyłania.

Jeśli komunikat zostanie odrzucony przez Service Bus, odrzucenie zawiera wskaźnik błędu i tekst z "identyfikatorem śledzenia" w tym miejscu. Odrzucanie zawiera również informacje o tym, czy można ponowić próbę wykonania operacji z oczekiwaniami. W kliencie te informacje są włączane do wyjątku i wywoływane do obiektu wywołującego operacji wysyłania. Jeśli wiadomość została zaakceptowana, operacja zostanie dyskretnie ukończona.

W przypadku korzystania z protokołu AMQP, który jest wyłącznym protokołem dla klienta .NET Standard i klienta Java, [który jest opcją dla klienta .NET Framework](service-bus-amqp-dotnet.md), transfery komunikatów i rozliczenia są potoku i są całkowicie asynchroniczne, a zaleca się używanie wariantów interfejsów API modelu programowania asynchronicznego.

Nadawca może umieścić kilka komunikatów w sieci w krótkim czasie, bez konieczności oczekiwania na potwierdzenie poszczególnych komunikatów, tak jak w przypadku protokołu SBMP lub HTTP 1,1. Operacje wysyłania asynchronicznego są ukończone, gdy odpowiednie komunikaty są akceptowane i przechowywane, w jednostkach partycjonowanych lub gdy operacje wysyłania do różnych jednostek nakładają się na siebie. Ukończenie może również wystąpić z oryginalnego zamówienia wysyłania.

Strategia obsługi wyników operacji wysyłania może mieć bezpośredni i znaczący wpływ na wydajność aplikacji. Przykłady w tej sekcji są zapisywane w języku C# i są stosowane w sposób odpowiedni dla przyszłość języka Java.

Jeśli aplikacja generuje szereg komunikatów, które zostały przedstawione w tym miejscu za pomocą zwykłej pętli, i musiało oczekiwać na ukończenie każdej operacji wysyłania przed wysłaniem kolejnego komunikatu, synchronicznego lub asynchronicznego obiektu API

Przy założeniu, że odległość czasu trwania komunikacji dwukierunkowej TCP (70 milisekund) z lokacji lokalnej do Service Bus i podania 10 ms dla Service Bus do akceptowania i przechowywania poszczególnych komunikatów, następująca pętla zajmie co najmniej 8 sekund, nie zlicza czasu transferu ładunku lub potencjalnych efektów przeciążenia trasy:

```csharp
for (int i = 0; i < 100; i++)
{
  // creating the message omitted for brevity
  await client.SendAsync(…);
}
```

Jeśli aplikacja uruchamia 10 asynchronicznych operacji wysyłania w bezpośrednim pomyślnym sukcesie i oczekuje na ich zakończenie oddzielnie, czas błądzenia dla tych 10 operacji wysyłania nakłada się na siebie. 10 komunikatów jest przesyłanych natychmiast, potencjalnie nawet przez udostępnienie ramek TCP, a całkowity czas przesyłania jest w dużym stopniu zależny od czasu potrzebnego na sieć do uzyskania komunikatów przesyłanych do brokera.

W przypadku takich samych założeń jak dla poprzedniej pętli łączny czas wykonywania dla następującej pętli może pozostać w jednej sekundzie:

```csharp
var tasks = new List<Task>();
for (int i = 0; i < 100; i++)
{
  tasks.Add(client.SendAsync(…));
}
await Task.WhenAll(tasks);
```

Należy pamiętać, że wszystkie asynchroniczne modele programowania korzystają z pewnej kolejki pracy w pamięci, która przechowuje oczekujące operacje. Gdy [SendAsync](/dotnet/api/microsoft.azure.servicebus.queueclient.sendasync#Microsoft_Azure_ServiceBus_QueueClient_SendAsync_Microsoft_Azure_ServiceBus_Message_) (C#) lub **send** (Java) zwraca, zadanie wysyłania jest umieszczane w kolejce w tej kolejce roboczej, ale gest protokołu jest uruchamiany tylko wtedy, gdy zadanie zostanie uruchomione. W przypadku kodu, który ma na celu wypychanie obciążeń komunikatów i w przypadku, gdy niezawodność jest istotna, należy zadbać o to, aby nie było zbyt wiele komunikatów jednocześnie "w locie", ponieważ wszystkie wysłane komunikaty zajmują pamięć, dopóki nie zostaną rzeczywiście umieszczone w sieci.

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

Tryb [odbierania i usuwania](/dotnet/api/microsoft.servicebus.messaging.receivemode) instruuje brokera, aby uwzględniał wszystkie komunikaty wysyłane do klienta odbierającego jako rozliczone podczas wysyłania. Oznacza to, że wiadomość jest uważana za zużytą zaraz po umieszczeniu jej w sieci przez brokera. Jeśli transfer komunikatów zakończy się niepowodzeniem, komunikat zostanie utracony.

W tym trybie jest to, że odbiornik nie musi podejmować dalszych działań w komunikacie i nie jest również wolniejszy przez oczekiwanie na wynik rozliczenia. Jeśli dane zawarte w poszczególnych wiadomościach mają niską wartość i/lub mają tylko znaczenie dla bardzo krótkiego czasu, ten tryb jest rozsądny.

### <a name="peeklock"></a>PeekLock

Tryb [blokady wglądu](/dotnet/api/microsoft.servicebus.messaging.receivemode) instruuje brokera, że klient odbierający chce jawnie rozliczyć odebrane komunikaty. Komunikat jest udostępniany do przetworzenia przez odbiorcę, a poza wyłączną blokadą w usłudze, tak że inne, konkurujące odbiorcy nie mogą go zobaczyć. Czas trwania blokady jest początkowo zdefiniowany na poziomie kolejki lub subskrypcji i może zostać rozszerzony przez klienta będącego właścicielem blokady za pośrednictwem operacji [RenewLock](/dotnet/api/microsoft.azure.servicebus.core.messagereceiver.renewlockasync#Microsoft_Azure_ServiceBus_Core_MessageReceiver_RenewLockAsync_System_String_) .

Po zablokowaniu komunikatu inni klienci odbierający z tej samej kolejki lub subskrypcji mogą wykonać blokadę i pobrać kolejne dostępne komunikaty, które nie są objęte blokadą aktywną. Gdy blokada komunikatu jest jawnie wydawana lub gdy blokada wygaśnie, komunikat jest wyświetlany na początku lub w najbliższym miejscu kolejności pobierania na potrzeby ponownej dostawy.

Gdy komunikat jest wielokrotnie wydawany przez odbiorniki lub zezwalają na zablokowanie przez zdefiniowaną liczbę razy ([maxDeliveryCount](/dotnet/api/microsoft.servicebus.messaging.queuedescription.maxdeliverycount#Microsoft_ServiceBus_Messaging_QueueDescription_MaxDeliveryCount)), wiadomość zostanie automatycznie usunięta z kolejki lub subskrypcji i umieszczona w skojarzonej kolejce utraconych wiadomości.

Klient odbierający inicjuje rozliczenie otrzymanej wiadomości z pozytywnym potwierdzeniem po [zakończeniu](/dotnet/api/microsoft.servicebus.messaging.queueclient.complete#Microsoft_ServiceBus_Messaging_QueueClient_Complete_System_Guid_) wywołania na poziomie interfejsu API. Wskazuje to brokerowi, że komunikat został pomyślnie przetworzony, a wiadomość zostanie usunięta z kolejki lub subskrypcji. Broker odpowiada na zamiar rozliczenia odbiorcy z odpowiedzią wskazującą, czy można wykonać rozliczenie.

Gdy klient otrzymujący nie może przetworzyć komunikatu, ale chce, aby komunikat został ponownie dostarczony, może jawnie poprosił o natychmiastowe zwolnienie i odblokowanie komunikatu przez wywołanie metody [Abandon](/dotnet/api/microsoft.servicebus.messaging.queueclient.abandon) lub wykonanie operacji blokowania.

Jeśli klient odbierający nie może przetworzyć komunikatu i wie, że ponowne dostarczenie komunikatu i ponowienie próby wykonania operacji nie powiedzie się, może odrzucić komunikat, który przenosi go do kolejki utraconych wiadomości przez wywołanie [utraconych](/dotnet/api/microsoft.servicebus.messaging.queueclient.deadletter)wiadomości, co umożliwia również ustawienie niestandardowej właściwości, w tym kodu przyczyny, który można pobrać z wiadomości z kolejki utraconych wiadomości.

Szczególnym przypadkiem rozliczenia jest odroczenie, który został omówiony w osobnym artykule.

**Wykonywanie operacji zakończonych** lub **utraconych** , a także operacji **RenewLock** może zakończyć się niepowodzeniem z powodu problemów z siecią, jeśli zatrzymywana blokada wygasła lub istnieją inne warunki po stronie usługi, które uniemożliwiają rozliczanie. W jednym z tych przypadków usługa wysyła negatywne potwierdzenie, które powierzchnie jako wyjątek w klientach interfejsu API. Jeśli powodem jest przerwane połączenie sieciowe, blokada zostanie porzucona, ponieważ Service Bus nie obsługuje odzyskiwania istniejących linków AMQP w innym połączeniu.

Jeśli operacja **zakończy** się niepowodzeniem, która zwykle odbywa się na bardzo końcu obsługi komunikatów, a w niektórych przypadkach po upływie kilku minut przetwarzania, aplikacja do odbioru może zdecydować, czy zachowuje stan pracy i ignoruje ten sam komunikat, gdy zostanie dostarczony po raz pierwszy, czy też tosses wynik pracy i ponawia próbę po ponownym dostarczeniu komunikatu.

Typowym mechanizmem służącym do identyfikowania powielonych dostaw komunikatów jest sprawdzenie identyfikatora komunikatu, który może i powinien być ustawiony przez nadawcę na unikatową wartość, ewentualnie wyrównany przy użyciu identyfikatora z procesu źródłowego. Harmonogram zadań prawdopodobnie ustawi Identyfikator komunikatu na identyfikator zadania, które ma zostać przypisane do procesu roboczego przez danego pracownika, a proces roboczy zignoruje drugie wystąpienie przypisania zadania, jeśli to zadanie zostało już wykonane.

> [!IMPORTANT]
> Należy pamiętać, że blokada, która PeekLock uzyskuje w komunikacie, jest nietrwała i może zostać utracona w następujących warunkach
>   * Aktualizacja usługi
>   * Aktualizacja systemu operacyjnego
>   * Zmiana właściwości jednostki (kolejki, tematu, subskrypcji) podczas utrzymywania blokady.
>
> Gdy blokada zostanie utracona, Azure Service Bus wygeneruje element LockLostException, który zostanie nadany w kodzie aplikacji klienta. W tym przypadku domyślna logika ponowienia klienta powinna być automatycznie uruchamiana i spróbuj ponownie wykonać operację.

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat Service Bus Messaging, zobacz następujące tematy:

* [Kolejki, tematy i subskrypcje usługi Service Bus](service-bus-queues-topics-subscriptions.md)
* [Wprowadzenie do kolejek usługi Service Bus](service-bus-dotnet-get-started-with-queues.md)
* [Jak używać tematów i subskrypcji usługi Service Bus](service-bus-dotnet-how-to-use-topics-subscriptions.md)
