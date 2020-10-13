---
title: Zarządzanie współbieżnością
titleSuffix: Azure Storage
description: Dowiedz się, jak zarządzać współbieżnością w usłudze Azure Storage dla usług obiektów blob, kolejek, tabel i plików. Zapoznaj się z trzema głównymi używanymi strategiami współbieżności danych.
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: tamram
ms.subservice: common
ms.custom: devx-track-csharp
ms.openlocfilehash: 2732781d32e92c8ec03116988e33ec4fbe0b2330
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89021565"
---
# <a name="managing-concurrency-in-microsoft-azure-storage"></a>Zarządzanie współbieżnością w usłudze Microsoft Azure Storage

Nowoczesne aplikacje internetowe zwykle mają wielu użytkowników, którzy jednocześnie wyświetlają i aktualizują dane. Wymaga to, aby deweloperzy aplikacji mogli dokładnie rozważyć, jak zapewnić użytkownikom końcowym przewidywalne środowisko, szczególnie w przypadku scenariuszy, w których wielu użytkowników może aktualizować te same dane. Istnieją trzy główne strategie współbieżności danych, które zazwyczaj rozważają deweloperzy:  

1. Optymistyczna współbieżność — aplikacja wykonująca aktualizację będzie w ramach aktualizacji sprawdza, czy dane zostały zmienione od czasu ostatniego odczytu danych przez aplikację. Na przykład jeśli dwóch użytkowników przeglądających stronę typu wiki przetworzy aktualizację na tej samej stronie, platforma wiki musi upewnić się, że druga aktualizacja nie zastąpi pierwszej aktualizacji — oraz że wszyscy użytkownicy wiedzą, czy ich Aktualizacja zakończyła się pomyślnie. Ta strategia jest najczęściej używana w aplikacjach sieci Web.
2. Współbieżność pesymistyczna — aplikacja, która zamierza wykonać aktualizację, będzie blokować obiekt, uniemożliwiając innym użytkownikom aktualizowanie danych do momentu zwolnienia blokady. Na przykład w scenariuszu głównej/podrzędnej replikacji danych, w której tylko serwer główny wykona aktualizacje, wzorzec zwykle utrzymuje blokadę na wyłączność przez dłuższy czas od danych, aby upewnić się, że nikt inny nie będzie mógł go zaktualizować.
3. Ostatni składnik zapisywania usługi WINS — podejście zezwalające na wykonywanie jakichkolwiek operacji aktualizacji bez weryfikowania, czy inna aplikacja zaktualizował dane, ponieważ aplikacja najpierw odczytaje dane. Ta strategia (lub Brak formalnej strategii) jest zwykle używana w przypadku, gdy dane są partycjonowane w taki sposób, że wiele użytkowników będzie mieć dostęp do tych samych danych. Może być również przydatne, gdy trwa przetwarzanie strumieni danych o krótkim czasie.  

Ten artykuł zawiera omówienie sposobu, w jaki platforma Azure Storage upraszcza programowanie, zapewniając obsługę pierwszej klasy dla wszystkich trzech tych strategii współbieżności.  

## <a name="azure-storage-simplifies-cloud-development"></a>Usługa Azure Storage upraszcza programowanie w chmurze

Usługa Azure Storage obsługuje wszystkie trzy strategie, chociaż ma możliwość zapewnienia pełnej obsługi optymistycznej i pesymistycznej współbieżności, ponieważ została ona zaprojektowana w celu utworzenia silnego modelu spójności, który gwarantuje, że gdy usługa magazynu zatwierdza operację wstawiania lub aktualizacji danych, wszystkie dalsze dostęp do tych danych będą widoczne w najnowszej aktualizacji. Platformy magazynu, które używają modelu spójności ostatecznej, mają opóźnienie między, gdy zapis jest wykonywany przez jednego użytkownika, a zaktualizowane dane mogą być widoczne dla innych użytkowników w ten sposób, co komplikuje Tworzenie aplikacji klienckich w celu zapobieżenia niespójnościom wpływającym na użytkowników końcowych.  

Oprócz wyboru odpowiednich deweloperów strategii współbieżności należy również wiedzieć, jak platforma magazynowa izoluje zmiany — szczególnie zmiany w tym samym obiekcie w różnych transakcjach. Usługa Azure Storage używa izolacji migawek, aby umożliwić wykonywanie operacji odczytu współbieżnie przy użyciu operacji zapisu w ramach pojedynczej partycji. W przeciwieństwie do innych poziomów izolacji, izolacja migawki gwarantuje, że wszystkie odczyty widzą spójną migawkę danych, nawet w przypadku gdy aktualizacje są wykonywane — zasadniczo przez zwrócenie wartości ostatniego zatwierdzenia podczas przetwarzania transakcji aktualizacji.  

## <a name="managing-concurrency-in-blob-storage"></a>Zarządzanie współbieżnością w usłudze BLOB Storage

Można wybrać jednooptymistyczne lub pesymistyczne modele współbieżności, aby zarządzać dostępem do obiektów blob i kontenerów w Blob service. Jeśli nie określisz jawnie strategii Ostatnia zapisy usługi WINS jest to ustawienie domyślne.  

### <a name="optimistic-concurrency-for-blobs-and-containers"></a>Optymistyczna współbieżność dla obiektów blob i kontenerów

Usługa magazynu przypisuje identyfikator do każdego przechowywanego obiektu. Ten identyfikator jest aktualizowany za każdym razem, gdy operacja aktualizacji jest przeprowadzana na obiekcie. Identyfikator jest zwracany do klienta w ramach odpowiedzi HTTP GET przy użyciu nagłówka ETag (tag jednostki) zdefiniowanego w protokole HTTP. Użytkownik wykonujący aktualizację takiego obiektu może wysłać w oryginalnym elemencie ETag wraz z nagłówkiem warunkowym, aby upewnić się, że aktualizacja będzie miała miejsce tylko w przypadku spełnienia określonego warunku — w tym przypadku warunek jest nagłówkiem "If-Match", który wymaga usługi magazynu, aby upewnić się, że wartość elementu ETag określonego w żądaniu aktualizacji jest taka sama jak w przypadku przechowywania w usłudze magazynu.  

Ten proces jest następujący:  

1. Pobieranie obiektu BLOB z usługi magazynu, odpowiedź zawiera wartość nagłówka HTTP ETag, która identyfikuje bieżącą wersję obiektu w usłudze Storage.
2. Podczas aktualizowania obiektu BLOB należy uwzględnić wartość ETag odebraną w kroku 1 w nagłówku warunkowym **if-Match** żądania wysyłanego do usługi.
3. Usługa porównuje wartość ETag w żądaniu z bieżącą wartością ETag obiektu BLOB.
4. Jeśli bieżąca wartość ETag obiektu BLOB jest inna niż nazwa elementu ETag w nagłówku warunku **if-Match** w żądaniu, usługa zwróci błąd 412 do klienta. Wskazuje to klientowi, że inny proces zaktualizował obiekt BLOB od momentu jego pobrania przez klienta.
5. Jeśli bieżąca wartość ETag obiektu BLOB jest taka sama jak wersja elementu ETag w nagłówku warunku **if-Match** w żądaniu, usługa wykonuje żądaną operację i aktualizuje bieżącą wartość ETag obiektu BLOB, aby pokazać, że została utworzona nowa wersja.  

Poniższy fragment kodu w języku C# (przy użyciu biblioteki 4.2.0 magazynu klienta) pokazuje prosty przykład sposobu konstruowania **AccessCondition** w oparciu o wartość ETag, która jest dostępna we właściwościach obiektu BLOB, który został wcześniej pobrany lub wstawiony. Następnie używa obiektu **AccessCondition** podczas aktualizowania obiektu BLOB: obiekt **AccessCondition** dodaje nagłówek **if-Match** do żądania. Jeśli inny proces zaktualizował obiekt BLOB, Blob service zwraca komunikat o stanie HTTP 412 (niepowodzenie warunku wstępnego). Pełny przykład można pobrać tutaj: [Zarządzanie współbieżnością przy użyciu usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).  

```csharp
// Retrieve the ETag from the newly created blob
// Etag is already populated as UploadText should cause a PUT Blob call
// to storage Blob service which returns the ETag in response.
string originalETag = blockBlob.Properties.ETag;

// This code simulates an update by a third party.
string helloText = "Blob updated by a third party.";

// No ETag provided so original blob is overwritten (thus generating a new ETag)
blockBlob.UploadText(helloText);
Console.WriteLine("Blob updated. Updated ETag = {0}",
blockBlob.Properties.ETag);

// Now try to update the blob using the original ETag provided when the blob was created
try
{
    Console.WriteLine("Trying to update blob using original ETag to generate if-match access condition");
    blockBlob.UploadText(helloText,accessCondition:
    AccessCondition.GenerateIfMatchCondition(originalETag));
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
    {
        Console.WriteLine("Precondition failure as expected. Blob's original ETag no longer matches");
        // TODO: client can decide on how it wants to handle the 3rd party updated content.
    }
    else
        throw;
}  
```

Usługa Azure Storage obejmuje również obsługę dodatkowych nagłówków warunkowych, takich jak **If-Modified-** AS, **if-Unmodified-** a i **If-None-Match** , a także ich kombinacji. Aby uzyskać więcej informacji, zobacz [Określanie nagłówków warunkowych dla operacji usługi BLOB Service](https://msdn.microsoft.com/library/azure/dd179371.aspx).  

Poniższa tabela zawiera podsumowanie operacji kontenera akceptujących nagłówki warunkowe, takie jak **if-Match** w żądaniu i zwracające wartość ETag w odpowiedzi.  

| Operacja | Zwraca wartość ETag kontenera | Akceptuje nagłówki warunkowe |
|:--- |:--- |:--- |
| Tworzenie kontenera |Tak |Nie |
| Pobierz właściwości kontenera |Tak |Nie |
| Pobierz metadane kontenera |Tak |Nie |
| Ustawianie metadanych kontenera |Tak |Tak |
| Pobierz listę ACL kontenerów |Tak |Nie |
| Ustawianie listy ACL kontenerów |Tak |Tak (*) |
| Usuwanie kontenera |Nie |Tak |
| Kontener dzierżawy |Tak |Tak |
| Wyświetl listę obiektów BLOB |Nie |Nie |

(*) Uprawnienia zdefiniowane przez SetContainerACL są zapisywane w pamięci podręcznej i aktualizacje tych uprawnień trwają 30 sekund, podczas których aktualizacje nie będą gwarantowane.  

Poniższa tabela zawiera podsumowanie operacji obiektów blob, które akceptują nagłówki warunkowe, takie jak **if-Match** w żądaniu i zwracają wartość ETag w odpowiedzi.

| Operacja | Zwraca wartość ETag | Akceptuje nagłówki warunkowe |
|:--- |:--- |:--- |
| Wstawianie obiektu blob |Tak |Tak |
| Pobierz obiekt BLOB |Tak |Tak |
| Pobieranie właściwości obiektu blob |Tak |Tak |
| Ustawianie właściwości obiektu BLOB |Tak |Tak |
| Pobierz metadane obiektu BLOB |Tak |Tak |
| Ustawianie metadanych obiektu BLOB |Tak |Tak |
| Obiekt BLOB dzierżawy (*) |Tak |Tak |
| Wykonywanie migawki obiektu blob |Tak |Tak |
| Kopiowanie obiektu blob |Tak |Tak (dla źródłowego i docelowego obiektu BLOB) |
| Przerwij Kopiowanie obiektu BLOB |Nie |Nie |
| Usuwanie obiektu blob |Nie |Tak |
| Umieść blok |Nie |Nie |
| Umieść listę zablokowanych |Tak |Tak |
| Pobierz listę zablokowanych |Tak |Nie |
| Umieść stronę |Tak |Tak |
| Pobierz zakresy stron |Tak |Tak |

(*) Obiekt BLOB dzierżawy nie zmienia elementu ETag w obiekcie blob.  

### <a name="pessimistic-concurrency-for-blobs"></a>Współbieżność pesymistyczna dla obiektów BLOB

Aby zablokować obiekt BLOB do użytku wyłącznego, możesz uzyskać [dzierżawę](https://msdn.microsoft.com/library/azure/ee691972.aspx) . Podczas uzyskiwania dzierżawy należy określić, jak długo będzie potrzebna dzierżawa: może to być przez od 15 do 60 sekund lub nieskończoność, co powoduje zablokowanie wyłącznej blokady. Możesz odnowić dzierżawę, aby ją przedłużyć i można zwolnić dowolną dzierżawę po zakończeniu pracy z nią. Blob service automatycznie zwalnia skończone dzierżawy po ich wygaśnięciu.  

Dzierżawy umożliwiają obsługę różnych strategii synchronizacji, w tym wyłącznych odczyty zapisu/udostępniania, dostęp z wyłącznym zapisem/odczytem i udostępnianie zapisu/odczyty w trybie wyłączności. W przypadku istnienia dzierżawy usługa magazynu wymusza wykonywanie operacji zapisu wyłącznych (Put, Set i Delete), jednak zapewnienie wyłączności operacji odczytu wymaga od dewelopera zapewnienia, że wszystkie aplikacje klienckie używają identyfikatora dzierżawy i że tylko jeden klient w danym momencie ma prawidłowy identyfikator dzierżawy. Operacje odczytu, które nie zawierają identyfikatora dzierżawy, powodują odczyty udostępnione.  

Poniższy fragment kodu w języku C# przedstawia przykład uzyskiwania wyłącznej dzierżawy przez 30 sekund na obiekcie blob, aktualizowania zawartości obiektu BLOB, a następnie zwalniania dzierżawy. Jeśli w obiekcie blob istnieje już prawidłowa dzierżawa podczas próby uzyskania nowej dzierżawy, Blob service zwróci wynik stanu "HTTP (409). Poniższy fragment kodu używa obiektu **AccessCondition** do hermetyzacji informacji o dzierżawie, gdy zgłasza żądanie zaktualizowania obiektu BLOB w usłudze Storage.  Pełny przykład można pobrać tutaj: [Zarządzanie współbieżnością przy użyciu usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
// Acquire lease for 15 seconds
string lease = blockBlob.AcquireLease(TimeSpan.FromSeconds(15), null);
Console.WriteLine("Blob lease acquired. Lease = {0}", lease);

// Update blob using lease. This operation will succeed
const string helloText = "Blob updated";
var accessCondition = AccessCondition.GenerateLeaseCondition(lease);
blockBlob.UploadText(helloText, accessCondition: accessCondition);
Console.WriteLine("Blob updated using an exclusive lease");

//Simulate third party update to blob without lease
try
{
    // Below operation will fail as no valid lease provided
    Console.WriteLine("Trying to update blob without valid lease");
    blockBlob.UploadText("Update without lease, will fail");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == (int)HttpStatusCode.PreconditionFailed)
        Console.WriteLine("Precondition failure as expected. Blob's lease does not match");
    else
        throw;
}  
```

Jeśli podjęto próbę wykonania operacji zapisu na wydzierżawionym obiekcie blob bez przekazania identyfikatora dzierżawy, żądanie kończy się niepowodzeniem z błędem 412. Należy pamiętać, że jeśli dzierżawa wygaśnie przed wywołaniem metody **UploadText** , ale nadal zostanie przekazany identyfikator dzierżawy, żądanie również zakończy się niepowodzeniem z błędem **412** . Aby uzyskać więcej informacji na temat zarządzania czasem wygaśnięcia dzierżawy i identyfikatorami dzierżawy, zobacz dokumentację dotyczącą [dzierżawy obiektu BLOB](https://msdn.microsoft.com/library/azure/ee691972.aspx) .  

Następujące operacje BLOB mogą używać dzierżaw do zarządzania pesymistyczną współbieżnością:  

* Wstawianie obiektu blob
* Pobierz obiekt BLOB
* Pobieranie właściwości obiektu blob
* Ustawianie właściwości obiektu BLOB
* Pobierz metadane obiektu BLOB
* Ustawianie metadanych obiektu BLOB
* Usuwanie obiektu blob
* Umieść blok
* Umieść listę zablokowanych
* Pobierz listę zablokowanych
* Umieść stronę
* Pobierz zakresy stron
* Obiekt BLOB migawki — identyfikator dzierżawy opcjonalny w przypadku istnienia dzierżawy
* Kopiuj obiekt BLOB — identyfikator dzierżawy jest wymagany, jeśli dzierżawa istnieje w docelowym obiekcie blob
* Przerwij Kopiowanie obiektu BLOB — identyfikator dzierżawy jest wymagany, jeśli w docelowym obiekcie blob istnieje nieskończona dzierżawa
* Dzierżawienie obiektu blob  

### <a name="pessimistic-concurrency-for-containers"></a>Współbieżność pesymistyczna dla kontenerów

Dzierżawy w kontenerach umożliwiają korzystanie z tych samych strategii synchronizacji, jak w przypadku obiektów BLOB (wyłącznych odczyty zapisu/udostępniania, wyłączny zapis/odczyt i udostępnianie zapisu/wyłącznego odczytu), jednak w przeciwieństwie do obiektów BLOB usługa magazynu wymusza wyłączność operacji usuwania. Aby usunąć kontener z aktywną dzierżawą, klient musi uwzględnić aktywny identyfikator dzierżawy z żądaniem usuwania. Wszystkie inne operacje kontenera powiodły się w kontenerze dzierżawionym bez uwzględnienia identyfikatora dzierżawy, w takim przypadku są to operacje udostępnione. Jeśli wymagana jest niewyłączność operacji Update (put lub Set) lub odczytu, deweloperzy powinni upewnić się, że wszyscy klienci używają identyfikatora dzierżawy i że tylko jeden klient w danym momencie ma prawidłowy identyfikator dzierżawy.  

Następujące operacje kontenera mogą używać dzierżaw do zarządzania pesymistyczną współbieżnością:  

* Usuwanie kontenera
* Pobierz właściwości kontenera
* Pobierz metadane kontenera
* Ustawianie metadanych kontenera
* Pobierz listę ACL kontenerów
* Ustawianie listy ACL kontenerów
* Kontener dzierżawy  

Aby uzyskać więcej informacji, zobacz:  

* [Określanie nagłówków warunkowych dla operacji usługi Blob Service](https://msdn.microsoft.com/library/azure/dd179371.aspx)
* [Kontener dzierżawy](https://msdn.microsoft.com/library/azure/jj159103.aspx)
* [Dzierżawienie obiektu blob](https://msdn.microsoft.com/library/azure/ee691972.aspx)

## <a name="managing-concurrency-in-table-storage"></a>Zarządzanie współbieżnością w magazynie tabel

W Table service są stosowane optymistyczne kontrole współbieżności jako zachowanie domyślne podczas pracy z jednostkami, w przeciwieństwie do Blob service, w którym należy jawnie wybrać opcję sprawdzenia optymistycznych kontroli współbieżności. Druga różnica między usługami Table i BLOB Services polega na tym, że można zarządzać tylko zachowaniem współbieżności jednostek z Blob service można zarządzać współbieżnością zarówno kontenerów, jak i obiektów BLOB.  

Aby użyć optymistycznej współbieżności i sprawdzić, czy inny proces zmodyfikował jednostkę, ponieważ został pobrany z usługi Table Storage, można użyć wartości ETag otrzymanej, gdy usługa tabeli zwraca jednostkę. Ten proces jest następujący:  

1. Pobieranie jednostki z usługi Table Storage, odpowiedź zawiera wartość ETag identyfikującą bieżący identyfikator skojarzony z tą jednostką w usłudze Storage.
2. Podczas aktualizowania jednostki należy uwzględnić wartość ETag uzyskaną w kroku 1 w polu obowiązkowy nagłówek **if-Match** żądania wysyłanego do usługi.
3. Usługa porównuje wartość ETag w żądaniu z bieżącą wartością ETag obiektu.
4. Jeśli bieżąca wartość ETag obiektu jest różna od elementu ETag w obowiązkowym nagłówku **if-Match** w żądaniu, usługa zwróci błąd 412 do klienta. Wskazuje to klientowi, że inny proces zaktualizował jednostkę od momentu pobrania przez klienta.
5. Jeśli bieżąca wartość ETag jednostki jest taka sama jak element ETag w obowiązkowym nagłówku **if-Match** w żądaniu lub nagłówek **if-Match** zawiera symbol wieloznaczny (*), usługa wykonuje żądaną operację i aktualizuje bieżącą wartość ETag jednostki, aby pokazać, że została zaktualizowana.  

Należy pamiętać, że w przeciwieństwie do Blob service, usługa Table Service wymaga, aby klient dołączył nagłówek **if-Match** w żądaniach aktualizacji. Można jednak wymusić aktualizację bezwarunkową (Ostatnia strategia usługi WINS) i pominąć sprawdzanie współbieżności, jeśli klient ustawi nagłówek **if-Match** na symbol wieloznaczny (*) w żądaniu.  

Poniższy fragment kodu w języku C# przedstawia jednostkę klienta, która została wcześniej utworzona lub pobrana z adresu e-mail. Początkowa operacja wstawiania lub pobierania przechowuje wartość ETag w obiekcie klient, a ponieważ przykład używa tego samego wystąpienia obiektu, gdy wykonuje operację Zamień, automatycznie wysyła wartość ETag z powrotem do usługi Table Service, umożliwiając usłudze sprawdzanie naruszeń współbieżności. Jeśli inny proces zaktualizował jednostkę w magazynie tabel, usługa zwróci komunikat o stanie HTTP 412 (niepowodzenie warunku wstępnego).  Pełny przykład można pobrać tutaj: [Zarządzanie współbieżnością przy użyciu usługi Azure Storage](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114).

```csharp
try
{
    customer.Email = "updatedEmail@contoso.org";
    TableOperation replaceCustomer = TableOperation.Replace(customer);
    customerTable.Execute(replaceCustomer);
    Console.WriteLine("Replace operation succeeded.");
}
catch (StorageException ex)
{
    if (ex.RequestInformation.HttpStatusCode == 412)
        Console.WriteLine("Optimistic concurrency violation – entity has changed since it was retrieved.");
    else
        throw;
}  
```

Aby jawnie wyłączyć sprawdzanie współbieżności, przed wykonaniem operacji Zamień należy ustawić właściwość **ETag** obiektu **Employee** na wartość "*".  

```csharp
customer.ETag = "*";  
```

Poniższa tabela zawiera podsumowanie sposobu używania wartości ETag przez operacje jednostek tabeli:

| Operacja | Zwraca wartość ETag | Wymaga nagłówka żądania If-Match |
|:--- |:--- |:--- |
| Jednostki zapytań |Tak |Nie |
| Wstaw jednostkę |Tak |Nie |
| Aktualizuj jednostkę |Tak |Tak |
| Scal jednostkę |Tak |Tak |
| Usuń jednostkę |Nie |Tak |
| Wstaw lub Zamień jednostkę |Tak |Nie |
| Wstaw lub Scal jednostkę |Tak |Nie |

Należy zauważyć, że operacje **wstawiania i zastępowania jednostki** i **wstawiania lub scalania jednostek** *nie wykonują* żadnych kontroli współbieżności, ponieważ nie wysyłają wartości ETag do usługi Table Service.  

W przypadku ogólnych deweloperów korzystających z tabel należy polegać na optymistycznej współbieżności podczas tworzenia skalowalnych aplikacji. Jeśli zachodzi konieczność blokowania pesymistycznego, jeden z rozwiązań, które mogą podjąć podczas uzyskiwania dostępu do tabel, ma przypisywać wskazany obiekt BLOB dla każdej tabeli i próbować uzyskać dzierżawę obiektu BLOB przed rozpoczęciem korzystania z tabeli. Takie podejście wymaga, aby aplikacja zapewniała, że wszystkie ścieżki dostępu do danych uzyskują dzierżawę przed rozpoczęciem pracy w tabeli. Należy również pamiętać, że minimalny czas dzierżawy wynosi 15 sekund, co wymaga starannej uwagi na skalowalność.  

Aby uzyskać więcej informacji, zobacz:  

* [Operacje na jednostkach](https://msdn.microsoft.com/library/azure/dd179375.aspx)  

## <a name="managing-concurrency-in-the-queue-service"></a>Zarządzanie współbieżnością w usłudze Queue Service

Jednym z scenariuszy, w którym współbieżność jest istotność w usłudze kolejkowania, jest to, że wielu klientów pobiera komunikaty z kolejki. Gdy wiadomość zostanie pobrana z kolejki, odpowiedź zawiera komunikat i wartość paragonu pop, która jest wymagana do usunięcia wiadomości. Komunikat nie jest automatycznie usuwany z kolejki, ale po pobraniu nie jest widoczny dla innych klientów w przedziale czasu określonym przez parametr visibilitytimeout. Klient, który pobiera komunikat, powinien usunąć komunikat po przetworzeniu i przed upływem czasu określonego przez element TimeNextVisible odpowiedzi, który jest obliczany na podstawie wartości parametru visibilitytimeout. Wartość visibilitytimeout jest dodawana do momentu, w którym komunikat jest pobierany w celu określenia wartości TimeNextVisible.  

Usługa kolejki nie obsługuje optymistycznej lub pesymistycznej współbieżności. z tego powodu klienci przetwarzający komunikaty pobrane z kolejki powinni zapewnić, że komunikaty są przetwarzane w idempotentne sposób. W przypadku operacji aktualizacji, takich jak SetQueueServiceProperties, SetQueueMetaData, SetQueueACL i UpdateMessage, stosowana jest strategia ostatniego składnika zapisywania usługi WINS.  

Aby uzyskać więcej informacji, zobacz:  

* [Interfejs API REST usługi Queue (Kolejka)](https://msdn.microsoft.com/library/azure/dd179363.aspx)
* [Pobierz komunikaty](https://msdn.microsoft.com/library/azure/dd179474.aspx)  

## <a name="managing-concurrency-in-azure-files"></a>Zarządzanie współbieżnością w Azure Files

Dostęp do usługi plików można uzyskać przy użyciu dwóch różnych punktów końcowych protokołu — SMB i REST. Usługa REST nie obsługuje blokowania optymistycznego ani blokowania pesymistycznego, a wszystkie aktualizacje będą zgodne z ostatnim zainstalowaną strategią usługi WINS. Klienci SMB instalujący udziały plików mogą korzystać z mechanizmów blokowania systemu plików, aby zarządzać dostępem do udostępnionych plików, w tym możliwość wykonywania blokowania pesymistycznego. Gdy klient SMB otworzy plik, określa tryb dostępu do plików i udostępniania. Ustawienie opcji dostępu do pliku "zapis" lub "Odczyt/zapis" wraz z trybem udostępniania plików "Brak" spowoduje, że plik jest blokowany przez klienta SMB do momentu zamknięcia pliku. Jeśli podjęto próbę wykonania operacji REST na pliku, w którym klient SMB ma zablokowany plik, usługa REST zwróci kod stanu 409 (konflikt) z kodem błędu SharingViolation.  

Gdy klient SMB otwiera plik do usunięcia, oznacza plik jako oczekujący na usunięcie, dopóki wszystkie pozostałe uchwyty klienta SMB w tym pliku są zamknięte. Gdy plik jest oznaczony jako oczekujące na usunięcie, każda operacja REST w tym pliku zwróci kod stanu 409 (konflikt) z kodem błędu SMBDeletePending. Kod stanu 404 (nie znaleziono) nie jest zwracany, ponieważ jest możliwe, że klient SMB usunie flagę oczekującego usunięcia przed zamknięciem pliku. Innymi słowy, kod stanu 404 (nie znaleziono) jest oczekiwany tylko wtedy, gdy plik został usunięty. Należy pamiętać, że podczas gdy plik jest w stanie oczekiwania na usunięcie SMB, nie zostanie uwzględniony w wynikach listy plików. Należy również pamiętać, że operacje usuwania plików i usuwania reszty katalogu są bezdzielne i nie powodują oczekującego usunięcia.  

Aby uzyskać więcej informacji, zobacz:  

* [Zarządzanie blokadami plików](https://msdn.microsoft.com/library/azure/dn194265.aspx)  

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pełną przykładową aplikację, do której odwołuje się ten blog:  

* [Zarządzanie współbieżnością przy użyciu usługi Azure Storage — Przykładowa aplikacja](https://code.msdn.microsoft.com/Managing-Concurrency-using-56018114)  

Aby uzyskać więcej informacji na temat usługi Azure Storage, zobacz:  

* [Strona główna Microsoft Azure Storage](https://azure.microsoft.com/services/storage/)
* [Wprowadzenie do usługi Azure Storage](storage-introduction.md)
* Wprowadzenie magazynu dla [obiektów BLOB](../blobs/storage-dotnet-how-to-use-blobs.md), [tabel](../../cosmos-db/table-storage-how-to-use-dotnet.md),  [kolejek](../storage-dotnet-how-to-use-queues.md)i [plików](../storage-dotnet-how-to-use-files.md)
* Architektura magazynu — [Azure Storage: usługa magazynu w chmurze o wysokiej dostępności z silną spójnością](https://docs.microsoft.com/archive/blogs/windowsazurestorage/sosp-paper-windows-azure-storage-a-highly-available-cloud-storage-service-with-strong-consistency)

