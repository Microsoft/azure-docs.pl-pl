---
title: Service Fabric Backup and Restore (Tworzenie kopii zapasowej i przywracanie w usłudze Service Fabric)
description: Dokumentacja dotycząca pojęć dotyczących Service Fabric tworzenia i przywracania kopii zapasowych oraz usługi do konfigurowania kopii zapasowych niezawodnych usług stanowych i Reliable Actors.
author: mcoskun
ms.topic: conceptual
ms.date: 10/29/2018
ms.author: mcoskun
ms.custom: devx-track-csharp
ms.openlocfilehash: 2674d1285544e4bc9b6fcb3d0b2e6f4b607786a2
ms.sourcegitcommit: a055089dd6195fde2555b27a84ae052b668a18c7
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/26/2021
ms.locfileid: "98791615"
---
# <a name="backup-and-restore-reliable-services-and-reliable-actors"></a>Tworzenie kopii zapasowych i przywracanie Reliable Services i Reliable Actors
Azure Service Fabric to platforma o wysokiej dostępności, która replikuje stan w wielu węzłach w celu zapewnienia wysokiej dostępności.  W takim przypadku, nawet jeśli jeden węzeł w klastrze ulegnie awarii, usługi będą nadal dostępne. Chociaż ta wbudowana nadmiarowość dostarczana przez platformę może być wystarczająca dla niektórych, w niektórych przypadkach jest pożądane, aby usługa mogła tworzyć kopie zapasowe danych (w magazynie zewnętrznym).

> [!NOTE]
> Ważne jest, aby utworzyć kopię zapasową i przywrócić dane (i sprawdzić, czy działa ona zgodnie z oczekiwaniami), aby umożliwić odzyskanie danych z różnych scenariuszy.
> 

> [!NOTE]
> Firma Microsoft zaleca używanie [okresowych kopii zapasowych i przywracania](service-fabric-backuprestoreservice-quickstart-azurecluster.md) w celu konfigurowania kopii zapasowych danych o niezawodnych usługach stanowych i Reliable Actors. 
> 


Na przykład usługa może chcieć utworzyć kopię zapasową danych w celu ochrony przed następującymi scenariuszami:

- W przypadku trwałej utraty całego klastra Service Fabric.
- Stała utrata większości replik partycji usługi
- Błędy administracyjne, według których stan zostanie przypadkowo usunięty lub uszkodzony. Na przykład może się tak zdarzyć, jeśli administrator z odpowiednimi uprawnieniami błędnie usunie usługę.
- Usterki w usłudze, które powodują uszkodzenie danych. Na przykład może się to zdarzyć, gdy uaktualnienie kodu usługi rozpocznie zapisywanie uszkodzonych danych do niezawodnej kolekcji. W takim przypadku kod i dane mogą być przywracane do wcześniejszego stanu.
- Przetwarzanie danych w trybie offline. Może być wygodne przetwarzanie danych w trybie offline w celu przeprowadzenia analizy biznesowej niezależnie od usługi, która generuje dane.

Funkcja tworzenia kopii zapasowej/przywracania umożliwia usługom opartym na interfejsie API Reliable Services tworzenia i przywracania kopii zapasowych. Interfejsy API tworzenia kopii zapasowych zapewniane przez platformę umożliwiają wykonywanie kopii zapasowych stanu partycji usługi bez blokowania operacji odczytu i zapisu. Interfejsy API przywracania umożliwiają przywrócenie stanu partycji usługi z wybranej kopii zapasowej.

## <a name="types-of-backup"></a>Typy kopii zapasowych
Dostępne są dwie opcje tworzenia kopii zapasowej: pełne i przyrostowe.
Pełna kopia zapasowa to kopia zapasowa zawierająca wszystkie dane wymagane do odtworzenia stanu repliki: punkty kontrolne i wszystkie rekordy dzienników.
Ponieważ ma punkty kontrolne i dziennik, pełna kopia zapasowa może być przywracana przez samą siebie.

Problem z pełnymi kopiami zapasowymi powstaje, gdy punkty kontrolne są duże.
Na przykład replika o 16 GB stanie będzie miała punkty kontrolne, które są dodawane do 16 GB.
Jeśli mamy cel punktu odzyskiwania 5 minut, replika musi mieć kopię zapasową co pięć minut.
Za każdym razem należy utworzyć kopię zapasową 16 GB punktów kontrolnych oprócz 50 MB (konfigurowalne użycie `CheckpointThresholdInMB` ).

![Przykład pełnej kopii zapasowej.](media/service-fabric-reliable-services-backup-restore/FullBackupExample.PNG)

Rozwiązaniem tego problemu jest przyrostowe kopie zapasowe, gdzie kopia zapasowa zawiera tylko zmienione rekordy dziennika od momentu utworzenia ostatniej kopii zapasowej.

![Przykład przyrostowej kopii zapasowej.](media/service-fabric-reliable-services-backup-restore/IncrementalBackupExample.PNG)

Ponieważ przyrostowe kopie zapasowe są zmieniane tylko od czasu ostatniej kopii zapasowej (nie obejmują punktów kontrolnych), są one szybsze, ale nie można ich przywrócić.
Aby przywrócić przyrostową kopię zapasową, wymagany jest cały łańcuch kopii zapasowych.
Łańcuch kopii zapasowych jest łańcuchem kopii zapasowych, rozpoczynając od pełnej kopii zapasowej, a następnie przez wiele ciągłego przyrostowych kopii zapasowych.

## <a name="backup-reliable-services"></a>Reliable Services kopii zapasowej
Autor usługi ma pełną kontrolę nad tym, kiedy tworzyć kopie zapasowe i gdzie będą przechowywane kopie zapasowe.

Aby rozpocząć tworzenie kopii zapasowej, usługa musi wywołać dziedziczoną funkcję członkowską `BackupAsync` .  
Kopie zapasowe mogą być wykonywane tylko z replik podstawowych i wymagają przypisywania stanu zapisu.

Jak pokazano poniżej, `BackupAsync` przyjmuje w `BackupDescription` obiekcie, gdzie jeden może określać pełną lub przyrostową kopię zapasową, a także funkcję wywołania zwrotnego, `Func<< BackupInfo, CancellationToken, Task<bool>>>` która jest wywoływana, gdy folder kopii zapasowej został utworzony lokalnie i jest gotowy do przeniesienia do pewnego magazynu zewnętrznego.

```csharp

BackupDescription myBackupDescription = new BackupDescription(BackupOption.Incremental,this.BackupCallbackAsync);

await this.BackupAsync(myBackupDescription);

```

Żądanie wykonania przyrostowej kopii zapasowej może zakończyć się niepowodzeniem z `FabricMissingFullBackupException` . Ten wyjątek wskazuje, że występuje jedna z następujących rzeczy:

- Replika nigdy nie zajęła pełnej kopii zapasowej, ponieważ stała się podstawowa.
- niektóre rekordy dziennika od momentu ostatniej kopii zapasowej zostały obcięte lub
- Replika przekazała `MaxAccumulatedBackupLogSizeInMB` limit.

Użytkownicy mogą zwiększyć prawdopodobieństwo, że można wykonywać przyrostowe kopie zapasowe przez skonfigurowanie `MinLogSizeInMB` lub `TruncationThresholdFactor` .
Zwiększenie tych wartości zwiększa użycie dysku na replikę.
Aby uzyskać więcej informacji, zobacz [konfiguracja Reliable Services](service-fabric-reliable-services-configuration.md)

`BackupInfo` zawiera informacje dotyczące kopii zapasowej, w tym lokalizację folderu, w którym środowisko uruchomieniowe zapisało kopię zapasową ( `BackupInfo.Directory` ). Funkcja wywołania zwrotnego może przenieść `BackupInfo.Directory` do magazynu zewnętrznego lub innej lokalizacji.  Ta funkcja zwraca również wartość logiczną, która wskazuje, czy można pomyślnie przenieść folder kopii zapasowej do lokalizacji docelowej.

Poniższy kod ilustruje sposób `BackupCallbackAsync` użycia metody do przekazywania kopii zapasowej do usługi Azure Storage:

```csharp
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo, CancellationToken cancellationToken)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, cancellationToken);

    return true;
}
```

W poprzednim przykładzie `ExternalBackupStore` jest klasą przykładową, która jest używana do tworzenia interfejsów z magazynem obiektów blob platformy Azure, i `UploadBackupFolderAsync` jest metodą, która kompresuje folder i umieszcza go w magazynie obiektów blob platformy Azure.

Należy pamiętać, że:

  - W danym momencie może istnieć tylko jedna operacja tworzenia kopii zapasowej w locie na replikę. Co więcej niż jedno `BackupAsync` wywołanie w czasie spowoduje `FabricBackupInProgressException` przekroczenie limitu numerów porządkowych określających kopii zapasowych.
  - Jeśli replika zostanie przełączona w tryb failover w trakcie wykonywania kopii zapasowej, wykonywanie kopii zapasowej mogło nie zostać ukończone. W takim przypadku po zakończeniu pracy w trybie failover usługa jest odpowiedzialna za ponowne uruchomienie kopii zapasowej przez wywołanie jej `BackupAsync` w razie potrzeby.

## <a name="restore-reliable-services"></a>Reliable Services przywracania
Ogólnie rzecz biorąc, sytuacje, w których konieczne może być wykonanie operacji przywracania, należą do jednej z następujących kategorii:

  - Partycja usługi utraciła dane. Na przykład dysk dla dwóch z trzech replik partycji (łącznie z repliką podstawową) jest uszkodzony lub wyczyszczony. Nowe podstawowe może być konieczne przywrócenie danych z kopii zapasowej.
  - Cała usługa zostanie utracona. Na przykład administrator usuwa całą usługę, a tym samym usługę i dane muszą zostać przywrócone.
  - Usługa replikuje uszkodzone dane aplikacji (na przykład z powodu błędu aplikacji). W takim przypadku usługa musi zostać uaktualniona lub przywrócona, aby usunąć przyczynę uszkodzenia, i należy przywrócić nieuszkodzone dane.

Chociaż istnieje wiele metod, oferujemy kilka przykładów użycia `RestoreAsync` programu w celu odzyskania z powyższych scenariuszy.

## <a name="partition-data-loss-in-reliable-services"></a>Utrata danych partycji w Reliable Services
W takim przypadku środowisko uruchomieniowe automatycznie wykryje utratę danych i wywołało `OnDataLossAsync` interfejs API.

Aby odzyskać, autor usługi musi wykonać następujące czynności:

  - Zastąp metodę wirtualnej klasy bazowej `OnDataLossAsync` .
  - Znajdź najnowszą kopię zapasową w lokalizacji zewnętrznej, która zawiera kopie zapasowe usługi.
  - Pobierz najnowszą kopię zapasową (i dekompresuj kopię zapasową do folderu kopii zapasowej, jeśli została skompresowana).
  - `OnDataLossAsync`Metoda udostępnia metodę `RestoreContext` . Wywołaj `RestoreAsync` interfejs API na podany `RestoreContext` .
  - Zwraca wartość true, jeśli przywracanie zakończyło się pomyślnie.

Poniżej przedstawiono przykładową implementację `OnDataLossAsync` metody:

```csharp
protected override async Task<bool> OnDataLossAsync(RestoreContext restoreCtx, CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    var restoreDescription = new RestoreDescription(backupFolder);

    await restoreCtx.RestoreAsync(restoreDescription);

    return true;
}
```

`RestoreDescription` przekazanie do `RestoreContext.RestoreAsync` wywołania zawiera element członkowski o nazwie `BackupFolderPath` .
Podczas przywracania pojedynczej pełnej kopii zapasowej `BackupFolderPath` należy ustawić ścieżkę lokalną folderu zawierającego pełną kopię zapasową.
Podczas przywracania pełnej kopii zapasowej i wielu przyrostowych kopii zapasowych `BackupFolderPath` należy ustawić ścieżkę lokalną folderu, który nie tylko zawiera pełną kopię zapasową, ale również wszystkie przyrostowe kopie zapasowe.
`RestoreAsync` Wywołanie może zgłosić `FabricMissingFullBackupException` , czy `BackupFolderPath` podane nie zawiera pełnej kopii zapasowej.
Może również zgłosić, `ArgumentException` Jeśli `BackupFolderPath` ma przerwany łańcuch przyrostowych kopii zapasowych.
Na przykład, jeśli zawiera pełną kopię zapasową, pierwsze przyrostowe i trzecią przyrostową kopię zapasową, ale nie drugą przyrostową kopię zapasową.

> [!NOTE]
> RestorePolicy jest domyślnie ustawiona na bezpieczny.  Oznacza to, że `RestoreAsync` interfejs API nie powiedzie się z argumentem, jeśli wykryje, że folder kopii zapasowej zawiera stan starszy niż lub równy stanowi zawartemu w tej replice.  `RestorePolicy.Force` może służyć do pomijania tego sprawdzania bezpieczeństwa. Jest to określone jako część `RestoreDescription` .
> 

## <a name="deleted-or-lost-service"></a>Usunięta lub utracona usługa
W przypadku usunięcia usługi należy najpierw ponownie utworzyć usługę, aby można było przywrócić dane.  Ważne jest, aby utworzyć usługę z tą samą konfiguracją, na przykład schemat partycjonowania, dzięki czemu można bezproblemowo przywrócić dane.  Po uruchomieniu usługi, interfejs API do przywracania danych ( `OnDataLossAsync` powyżej) musi być wywoływany na każdej partycji tej usługi. Jednym ze sposobów osiągnięcia tego jest użycie [FabricClient. TestManagementClient. StartPartitionDataLossAsync](/dotnet/api/system.fabric.fabricclient.testmanagementclient#System_Fabric_FabricClient_TestManagementClient_StartPartitionDataLossAsync_System_Guid_System_Fabric_PartitionSelector_System_Fabric_DataLossMode_) na każdej partycji.  

W tym momencie implementacja jest taka sama jak w powyższym scenariuszu. Każda partycja musi przywrócić najnowszą odpowiednią kopię zapasową ze sklepu zewnętrznego. Jedno zastrzeżenie polega na tym, że identyfikator partycji może ulec zmianie, ponieważ środowisko uruchomieniowe tworzy dynamicznie identyfikatory partycji. W tym celu usługa musi przechowywać odpowiednie informacje o partycji i nazwę usługi w celu zidentyfikowania poprawnej najnowszej kopii zapasowej, która ma zostać przywrócona dla każdej partycji.

> [!NOTE]
> Nie zaleca się używania `FabricClient.ServiceManager.InvokeDataLossAsync` na każdej partycji w celu przywrócenia całej usługi, ponieważ może to spowodować uszkodzenie stanu klastra.
> 

## <a name="replication-of-corrupt-application-data"></a>Replikacja uszkodzonych danych aplikacji
Jeśli nowo wdrożone uaktualnienie aplikacji zawiera usterkę, która może spowodować uszkodzenie danych. Na przykład uaktualnienie aplikacji może zacząć aktualizować każdy rekord numeru telefonu w niezawodnym słowniku przy użyciu nieprawidłowego numeru kierunkowego.  W takim przypadku nieprawidłowe numery telefonów zostaną zreplikowane, ponieważ Service Fabric nie ma informacji o charakterze przechowywanych danych.

Pierwsza czynność do wykonania po wykryciu takiej usterki egregious, która powoduje uszkodzenie danych, jest zablokowanie usługi na poziomie aplikacji i, jeśli to możliwe, uaktualnienie do wersji kodu aplikacji, która nie ma usterki.  Jednak nawet po usunięciu kodu usługi dane mogą być uszkodzone i w ten sposób może być konieczne przywrócenie danych.  W takich przypadkach może być niewystarczające do przywrócenia najnowszej kopii zapasowej, ponieważ najnowsze kopie zapasowe mogą być również uszkodzone.  W tym celu należy znaleźć ostatnią kopię zapasową, która została wprowadzona przed uszkodzeniem danych.

Jeśli nie masz pewności, które kopie zapasowe są uszkodzone, możesz wdrożyć nowy klaster Service Fabric i przywrócić kopie zapasowe partycji, których to dotyczy, tak jak powyżej powyżej scenariusza "usunięte lub utracone usługi".  W przypadku każdej partycji Rozpocznij przywracanie kopii zapasowych od najnowszych do najmniejszych. Po znalezieniu kopii zapasowej, która nie ma uszkodzenia, Przenieś/Usuń wszystkie kopie zapasowe tej partycji, które były nowsze (niż w przypadku tej kopii zapasowej). Powtórz ten proces dla każdej partycji. Teraz, gdy `OnDataLossAsync` jest wywoływana na partycji w klastrze produkcyjnym, Ostatnia kopia zapasowa znaleziona w sklepie zewnętrznym będzie pobrana przez powyższy proces.

Teraz kroki opisane w sekcji "usunięte lub utracone usługi" mogą zostać użyte do przywrócenia stanu usługi do stanu przed uszkodzeniem przez kod debugowania stanu.

Należy pamiętać, że:

  - Podczas przywracania istnieje prawdopodobieństwo, że przywracana kopia zapasowa jest starsza niż stan partycji przed utratą danych. W związku z tym należy przywrócić tylko jako ostatnią możliwość odzyskania możliwie największej ilości danych.
  - Ciąg reprezentujący ścieżkę folderu kopii zapasowej i ścieżki plików w folderze kopii zapasowej może być większy niż 255 znaków, w zależności od długości ścieżki FabricDataRoot i nazwy typu aplikacji. Może to spowodować, że niektóre metody .NET, takie jak `Directory.Move` , wyrzucają `PathTooLongException` wyjątek. Obejście polega na bezpośrednim wywołaniu interfejsów API Kernel32, takich jak `CopyFile` .

## <a name="back-up-and-restore-reliable-actors"></a>Tworzenie kopii zapasowych i przywracanie Reliable Actors


Platforma Reliable Actors jest oparta na Reliable Services. ActorService, który hostuje aktorów, to usługa stanowa niezawodna. W związku z tym wszystkie funkcje tworzenia kopii zapasowych i przywracania dostępne w Reliable Services są również dostępne dla Reliable Actors (oprócz zachowań, które są specyficzne dla dostawcy stanu). Ponieważ kopie zapasowe zostaną wykonane na partycji, zostaną utworzone kopie zapasowe Stanów wszystkich aktorów tej partycji (a przywracanie będzie podobne i nastąpi w oparciu o partycję). Aby wykonać kopię zapasową/przywrócić, właściciel usługi powinien utworzyć niestandardową klasę usługi aktora pochodzącą z klasy ActorService, a następnie wykonać kopię zapasową/przywracanie podobną do Reliable Services, jak opisano powyżej w poprzednich sekcjach.

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo)
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Podczas tworzenia niestandardowej klasy usługi aktora należy zarejestrować ją również podczas rejestrowania aktora.

```csharp
ActorRuntime.RegisterActorAsync<MyActor>(
    (context, typeInfo) => new MyCustomActorService(context, typeInfo)).GetAwaiter().GetResult();
```

Domyślnym dostawcą stanu dla Reliable Actors jest `KvsActorStateProvider` . Przyrostowa kopia zapasowa nie jest domyślnie włączona w programie `KvsActorStateProvider` . Możesz włączyć przyrostową kopię zapasową, tworząc `KvsActorStateProvider` przy użyciu odpowiedniego ustawienia w jego konstruktorze, a następnie przekazując go do konstruktora ActorService, jak pokazano w poniższym fragmencie kodu:

```csharp
class MyCustomActorService : ActorService
{
    public MyCustomActorService(StatefulServiceContext context, ActorTypeInformation actorTypeInfo)
          : base(context, actorTypeInfo, null, null, new KvsActorStateProvider(true)) // Enable incremental backup
    {
    }
    
    //
    // Method overrides and other code.
    //
}
```

Po włączeniu przyrostowej kopii zapasowej tworzenie przyrostowej kopii zapasowej może zakończyć się niepowodzeniem z FabricMissingFullBackupException z jednego z następujących powodów i należy wykonać pełną kopię zapasową przed utworzeniem przyrostowych kopii zapasowych:

  - Replika nigdy nie zajęła pełnej kopii zapasowej, ponieważ stała się podstawowa.
  - Niektóre rekordy dziennika zostały obcięte od czasu utworzenia ostatniej kopii zapasowej.

Gdy przyrostowa kopia zapasowa jest włączona, program nie `KvsActorStateProvider` użyje buforu cyklicznego do zarządzania rekordami dziennika i okresowo obcina go. Jeśli użytkownik nie pobiera kopii zapasowej przez okres 45 minut, system automatycznie obciąć rekordy dziennika. Ten interwał można skonfigurować przez określenie `logTruncationIntervalInMinutes` w `KvsActorStateProvider` konstruktorze (podobnie jak w przypadku włączania przyrostowych kopii zapasowych). Rekordy dziennika mogą również zostać obcięte, jeśli replika podstawowa musi utworzyć kolejną replikę, wysyłając wszystkie jej dane.

W przypadku przywracania z łańcucha kopii zapasowych, podobnie jak Reliable Services, BackupFolderPath powinien zawierać podkatalogi z jednym podkatalog zawierającym pełną kopię zapasową i inne podkatalogi zawierające przyrostowe kopie zapasowe. Jeśli weryfikacja łańcucha kopii zapasowych nie powiedzie się, interfejs API przywracania zwróci wyjątek Fabricexception z odpowiednim komunikatem o błędzie. 

> [!NOTE]
> `KvsActorStateProvider` obecnie ignoruje opcję RestorePolicy. Safe. Obsługa tej funkcji jest planowana w przyszłej wersji.
> 

## <a name="testing-back-up-and-restore"></a>Testowanie tworzenia i przywracania kopii zapasowych
Ważne jest, aby zapewnić tworzenie kopii zapasowych krytycznych danych i można je przywrócić z. Można to zrobić, wywołując `Start-ServiceFabricPartitionDataLoss` polecenie cmdlet w programie PowerShell, które może powodować utratę danych w określonej partycji, aby sprawdzić, czy funkcja tworzenia kopii zapasowych i przywracania danych w usłudze działa zgodnie z oczekiwaniami.  Możliwe jest również programowo wywoływanie utraty danych i ich przywrócenia z tego zdarzenia.

> [!NOTE]
> Przykładową implementację funkcji tworzenia kopii zapasowych i przywracania można znaleźć w aplikacji Internet Reference w witrynie GitHub. Więcej informacji można znaleźć w `Inventory.Service` usłudze.
> 
> 

## <a name="under-the-hood-more-details-on-backup-and-restore"></a>Pod okapem: więcej szczegółów dotyczących tworzenia kopii zapasowych i przywracania
Poniżej przedstawiono więcej informacji na temat tworzenia kopii zapasowych i przywracania.

### <a name="backup"></a>Backup
Niezawodny Menedżer stanu zapewnia możliwość tworzenia spójnych kopii zapasowych bez blokowania operacji odczytu i zapisu. W tym celu wykorzystuje mechanizm tworzenia punktów kontrolnych i trwałości dziennika.  Niezawodny Menedżer stanu przyjmuje rozmyte (lekkie) punkty kontrolne w określonych punktach, aby zwolnić nacisk z dziennika transakcji i skrócić czas odzyskiwania.  Gdy `BackupAsync` jest wywoływana, Menedżer niezawodnego stanu instruuje wszystkie niezawodne obiekty do skopiowania ich najnowszych plików punktów kontrolnych do lokalnego folderu kopii zapasowej.  Następnie niezawodny Menedżer stanów kopiuje wszystkie rekordy dziennika, zaczynając od "Start wskaźnika" do folderu kopii zapasowej.  Ponieważ wszystkie rekordy dziennika do najnowszego rekordu dziennika są zawarte w kopii zapasowej, a niezawodny Menedżer stanu zachowuje rejestrowanie z wyprzedzeniem, Menedżer niezawodnego stanu gwarantuje, że wszystkie transakcje, które zostały zatwierdzone ( `CommitAsync` zostały pomyślnie zwrócone), są uwzględnione w kopii zapasowej.

Wszystkie transakcje, które zostały zatwierdzono po `BackupAsync` wywołaniu, mogą nie być w kopii zapasowej.  Gdy lokalny folder kopii zapasowej zostanie wypełniony przez platformę (to oznacza, że lokalna kopia zapasowa jest wykonywana przez środowisko uruchomieniowe), wywoływana jest wywołanie zwrotne kopii zapasowej usługi.  To wywołanie zwrotne jest odpowiedzialne za przeniesienie folderu kopii zapasowej do lokalizacji zewnętrznej, takiej jak usługa Azure Storage.

### <a name="restore"></a>Przywracanie
Niezawodny Menedżer stanu umożliwia przywracanie z kopii zapasowej przy użyciu `RestoreAsync` interfejsu API.  
`RestoreAsync`Metodę dla `RestoreContext` można wywołać tylko wewnątrz `OnDataLossAsync` metody.
Wartość logiczna zwrócona przez `OnDataLossAsync` wskazuje, czy usługa przywróciła swój stan ze źródła zewnętrznego.
Jeśli `OnDataLossAsync` zwraca wartość true, Service Fabric będzie odbudował wszystkie pozostałe repliki z tego podstawowego. Service Fabric zapewnia, że repliki, które będą otrzymywać `OnDataLossAsync` wywołania pierwszego przejścia do roli głównej, ale nie mają uprawnień do odczytu lub zapisu stanu.
Oznacza to, że dla realizatorów klasy statefulservice `RunAsync` nie będzie wywoływana do momentu `OnDataLossAsync` pomyślnego zakończenia.
Następnie `OnDataLossAsync` zostanie wywołana na nowym serwerze podstawowym.
Dopóki usługa nie ukończy pomyślnie tego interfejsu API (zwracając wartość true lub false) i zakończy odpowiednią ponowną konfigurację, interfejs API będzie nadal wywoływana pojedynczo.

`RestoreAsync` najpierw porzuca wszystkie istniejące Stany w replice podstawowej, w której została wywołana. Następnie Menedżer niezawodnego stanu tworzy wszystkie niezawodne obiekty, które znajdują się w folderze kopii zapasowej. Następnie niezawodne obiekty są nakazuje przywracanie z punktów kontrolnych w folderze kopii zapasowej. Na koniec niezawodny Menedżer stanu odzyskuje swój własny stan z rekordów dziennika w folderze kopii zapasowej i wykonuje odzyskiwanie. W ramach procesu odzyskiwania operacje zaczynające się od "punktu początkowego", które mają zatwierdzone rekordy dziennika w folderze kopii zapasowej, są odtwarzane w niezawodnych obiektach. Ten krok zapewnia, że odzyskany stan jest spójny.

## <a name="next-steps"></a>Następne kroki
  - [Elementy Reliable Collections](service-fabric-work-with-reliable-collections.md)
  - [Reliable Services — Szybki Start](service-fabric-reliable-services-quick-start.md)
  - [Powiadomienia Reliable Services](service-fabric-reliable-services-notifications.md)
  - [Konfiguracja Reliable Services](service-fabric-reliable-services-configuration.md)
  - [Dokumentacja dla deweloperów dla niezawodnych kolekcji](/dotnet/api/microsoft.servicefabric.data.collections#microsoft_servicefabric_data_collections)
  - [Okresowe wykonywanie kopii zapasowej i przywracanie w usłudze Azure Service Fabric](service-fabric-backuprestoreservice-quickstart-azurecluster.md)
