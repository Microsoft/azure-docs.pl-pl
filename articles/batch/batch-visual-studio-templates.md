---
title: Tworzenie rozwiązań za pomocą szablonów programu Visual Studio
description: Dowiedz się, jak szablony projektów programu Visual Studio mogą pomóc w implementacji i uruchomieniu obciążeń intensywnie korzystających z obliczeń na Azure Batch.
ms.topic: how-to
ms.date: 02/27/2017
ms.custom: seodec18
ms.openlocfilehash: 9332684008b45aea39e07d8225bae6450ba57de5
ms.sourcegitcommit: a9784a3fd208f19c8814fe22da9e70fcf1da9c93
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/22/2020
ms.locfileid: "83779514"
---
# <a name="use-visual-studio-project-templates-to-jump-start-batch-solutions"></a>Korzystanie z szablonów projektów programu Visual Studio do szybkiego uruchamiania rozwiązań wsadowych

Szablony programu **Menedżer zadań** i **zadań w programie Visual Studio** dla usługi Batch udostępniają kod, który ułatwia implementowanie i uruchamianie obciążeń intensywnie korzystających z obliczeń w usłudze Batch z mniejszą ilością wysiłku. W tym dokumencie opisano te szablony i przedstawiono wskazówki dotyczące korzystania z nich.

> [!IMPORTANT]
> W tym artykule omówiono tylko informacje dotyczące tych dwóch szablonów i przyjęto założenie, że znasz usługę Batch i kluczowe pojęcia związane z działem IT: pule, węzły obliczeniowe, zadania i zadania, zadania Menedżera zadań, zmienne środowiskowe i inne istotne informacje. Więcej informacji można znaleźć w [temacie Podstawowe informacje o](batch-technical-overview.md) [przepływie pracy i zasobach usługi Batch](batch-service-workflow-features.md)Azure Batch. 

## <a name="high-level-overview"></a>Ogólne omówienie
Szablony Menedżera zadań i procesora zadań mogą służyć do tworzenia dwóch przydatnych składników:

* Zadanie Menedżera zadań, które implementuje rozdzielacz zadań, który może spowodować przerwanie zadania w wielu zadaniach, które mogą być uruchamiane niezależnie, równolegle.
* Procesor zadań, który może służyć do wykonywania wstępnego przetwarzania i przetwarzania końcowego wokół wiersza polecenia aplikacji.

Na przykład w scenariuszu renderowania filmu rozdzielacz zadania może przekształcić pojedyncze zadanie filmowe w setki lub tysiące oddzielnych zadań, które przetwarzą poszczególne ramki osobno. Następnie procesor zadań wywoła aplikację renderowania i wszystkie zależne procesy, które są potrzebne do renderowania każdej ramki, a także wykonują dodatkowe akcje (na przykład kopiując renderowane ramki do lokalizacji magazynu).

> [!NOTE]
> Szablony Menedżera zadań i procesora zadań są niezależne od siebie, więc można wybrać opcję użycia lub tylko jednego z nich, w zależności od wymagań zadania obliczeniowego i preferencji.
> 
> 

Jak pokazano na poniższym diagramie, zadanie obliczeniowe korzystające z tych szablonów przejdzie przez trzy etapy:

1. Kod klienta (np. aplikacja, usługa sieci Web itd.) przesyła zadanie do usługi Batch na platformie Azure, określając jako zadanie Menedżera zadań zadania podrzędnego Menedżera zadań.
2. Usługa Batch uruchamia zadanie Menedżera zadań w węźle obliczeniowym, a rozdzielacz zadania uruchamia określoną liczbę zadań procesora zadań na tyle, ile węzłów obliczeniowych jest wymaganych, na podstawie parametrów i specyfikacji w kodzie rozdzielacza zadania.
3. Zadania procesora zadań uruchamiają się niezależnie, równolegle, przetwarzać dane wejściowe i generować dane wyjściowe.

![Diagram przedstawiający sposób interakcji kodu klienta z usługą Batch][diagram01]

## <a name="prerequisites"></a>Wymagania wstępne
Aby korzystać z szablonów wsadowych, potrzebne są następujące elementy:

* Komputer z zainstalowanym programem Visual Studio 2015. Szablony wsadowe są obecnie obsługiwane tylko dla programu Visual Studio 2015.
* Szablony usługi Batch, które są dostępne w [Galerii programu Visual Studio][vs_gallery] jako rozszerzenia programu Visual Studio. Istnieją dwa sposoby pobrania szablonów:
  
  * Zainstaluj szablony przy użyciu okna dialogowego **rozszerzenia i aktualizacje** w programie Visual Studio (Aby uzyskać więcej informacji, zobacz [Znajdowanie i używanie rozszerzeń programu Visual Studio][vs_find_use_ext]). W oknie dialogowym **rozszerzenia i aktualizacje** Wyszukaj i pobierz następujące dwa rozszerzenia:
    
    * Menedżer zadań Azure Batch z rozdzielaczem zadań
    * Procesor zadań Azure Batch
  * Pobierz szablony z galerii online dla programu Visual Studio: [Microsoft Azure Batch szablonów projektu][vs_gallery_templates]
* Jeśli planujesz użycie funkcji [pakietów aplikacji](batch-application-packages.md) do wdrożenia Menedżera zadań i procesora zadań w węzłach obliczeniowych wsadowych, musisz połączyć konto magazynu z kontem w usłudze Batch.

## <a name="preparation"></a>Przygotowanie
Zalecamy utworzenie rozwiązania, które może zawierać Menedżera zadań oraz procesora zadań, ponieważ może to ułatwić udostępnianie kodu między menedżerem zadań i programami procesora zadań. Aby utworzyć to rozwiązanie, wykonaj następujące kroki:

1. Otwórz program Visual Studio i wybierz pozycję **plik**  >  **Nowy**  >  **projekt**.
2. W obszarze **Szablony**rozwiń węzeł **Inne typy projektów**, kliknij pozycję **rozwiązania programu Visual Studio**, a następnie wybierz pozycję **puste rozwiązanie**.
3. Wpisz nazwę opisującą aplikację i cel tego rozwiązania (np. "LitwareBatchTaskPrograms").
4. Aby utworzyć nowe rozwiązanie, kliknij przycisk **OK**.

## <a name="job-manager-template"></a>Szablon Menedżera zadań
Szablon Menedżera zadań ułatwia zaimplementowanie zadania Menedżera zadań, które może wykonywać następujące czynności:

* Podziel zadanie na wiele zadań.
* Prześlij te zadania do uruchomienia w usłudze Batch.

> [!NOTE]
> Aby uzyskać więcej informacji o zadaniach Menedżera zadań, zobacz [zadania i zadania](jobs-and-tasks.md#job-manager-task).
> 
> 

### <a name="create-a-job-manager-using-the-template"></a>Tworzenie Menedżera zadań przy użyciu szablonu
Aby dodać Menedżera zadań do utworzonego wcześniej rozwiązania, wykonaj następujące kroki:

1. Otwórz istniejące rozwiązanie w programie Visual Studio.
2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, a następnie kliknij pozycję **Dodaj**  >  **Nowy projekt**.
3. W obszarze **Visual C#** kliknij pozycję **chmura**, a następnie kliknij pozycję **Azure Batch Menedżer zadań z rozdzielaczem zadania**.
4. Wpisz nazwę opisującą aplikację i zidentyfikuj ten projekt jako Menedżera zadań (np. "LitwareJobManager").
5. Aby utworzyć projekt, kliknij przycisk **OK**.
6. Na koniec Skompiluj projekt, aby wymusić, że program Visual Studio załaduje wszystkie pakiety NuGet, do których istnieją odwołania, i sprawdź, czy projekt jest prawidłowy przed rozpoczęciem modyfikowania.

### <a name="job-manager-template-files-and-their-purpose"></a>Pliki szablonów Menedżera zadań i ich przeznaczenie
Podczas tworzenia projektu przy użyciu szablonu Menedżera zadań generuje on trzy grupy plików kodu:

* Główny plik programu (Program.cs). Zawiera ona punkt wejścia programu i obsługę wyjątków najwyższego poziomu. Zwykle nie trzeba go modyfikować.
* Katalog struktury. Zawiera ona pliki odpowiedzialne za "typowe" działania wykonywane przez program Menedżer zadań — rozpakowywanie parametrów, dodanie zadań do zadania wsadowego itd. Zwykle nie trzeba modyfikować tych plików.
* Plik rozdzielacza zadania (JobSplitter.cs). W tym miejscu zostanie umieszczona logika specyficzna dla aplikacji służąca do dzielenia zadania na zadania.

Oczywiście można dodać dodatkowe pliki wymagane do obsługi kodu rozdzielacza zadania, w zależności od złożoności logiki podziału zadania.

Szablon generuje również standardowe pliki projektu .NET, takie jak plik. csproj, App. config, Packages. config itd.

W pozostałej części tej sekcji opisano różne pliki i ich struktury kodu oraz wyjaśniono, co robi każda klasa.

![Program Visual Studio Eksplorator rozwiązań pokazujący rozwiązanie szablonu Menedżera zadań][solution_explorer01]

**Pliki struktury**

* `Configuration.cs`: Hermetyzuje ładowanie danych konfiguracji zadania, takich jak szczegóły konta, powiązane poświadczenia konta magazynu, informacje o zadaniach i zadaniach oraz parametry zadań. Zapewnia również dostęp do zmiennych środowiskowych zdefiniowanych przez partię (zobacz Ustawienia środowiska dla zadań, w dokumentacji usługi Batch) za pośrednictwem klasy Configuration. zmiennych środowiskowych.
* `IConfiguration.cs`: Jest abstrakcyjna implementacja klasy konfiguracji, dzięki czemu można testować rozdzielacz zadania przy użyciu obiektu konfiguracji fałszywego lub makiety.
* `JobManager.cs`: Organizuje składniki programu Menedżer zadań. Jest on odpowiedzialny za Inicjowanie rozdzielacza zadań, wywoływanie rozdzielacza zadania i wysyłanie zadań zwróconych przez rozdzielacz zadania do osoby przesyłającej zadanie podrzędne.
* `JobManagerException.cs`: Reprezentuje błąd, który wymaga przerwania Menedżera zadań. JobManagerException jest używany do zawijania "oczekiwanych" błędów, w przypadku których konkretne informacje diagnostyczne mogą być dostarczane jako część zakończenia.
* `TaskSubmitter.cs`: Ta klasa jest odpowiedzialna za Dodawanie zadań zwracanych przez rozdzielacz zadania do zadania wsadowego. Klasa JobManager agreguje sekwencję zadań do partii w celu wydajnego, ale jednocześnie dodaje do zadania, a następnie wywołuje TaskSubmitter. SubmitTasks w wątku w tle dla każdej partii.

**Rozdzielacz zadań**

`JobSplitter.cs`: Ta klasa zawiera logikę specyficzną dla aplikacji służącą do dzielenia zadania na zadania. Struktura wywołuje metodę JobSplitter. Split, aby uzyskać sekwencję zadań, którą dodaje do zadania, jak metoda zwraca je. Jest to Klasa, w której zostanie wprowadzona logika zadania. Zaimplementuj metodę Split, aby zwrócić sekwencję wystąpień CloudTask reprezentujących zadania, w których chcesz podzielić zadanie.

**Standardowe pliki projektu wiersza polecenia platformy .NET**

* `App.config`: Standardowy plik konfiguracji aplikacji .NET.
* `Packages.config`: Standardowy plik zależności pakietu NuGet.
* `Program.cs`: Zawiera punkt wejścia programu i obsługę wyjątków najwyższego poziomu.

### <a name="implementing-the-job-splitter"></a>Implementowanie rozdzielacza zadań
Po otwarciu projektu szablonu Menedżera zadań projekt będzie miał domyślnie otwarty plik JobSplitter.cs. Można zaimplementować logikę podziału dla zadań w obciążeniu przy użyciu metody Split (), pokazane poniżej:

```csharp
/// <summary>
/// Gets the tasks into which to split the job. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The job manager framework invokes the Split method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation should return tasks lazily, for example using a C#
/// iterator and the "yield return" statement; this allows tasks to be added
/// and to start running while splitting is still in progress.
/// </summary>
/// <returns>The tasks to be added to the job. Tasks are added automatically
/// by the job manager framework as they are returned by this method.</returns>
public IEnumerable<CloudTask> Split()
{
    // Your code for the split logic goes here.
    int startFrame = Convert.ToInt32(_parameters["StartFrame"]);
    int endFrame = Convert.ToInt32(_parameters["EndFrame"]);

    for (int i = startFrame; i <= endFrame; i++)
    {
        yield return new CloudTask("myTask" + i, "cmd /c dir");
    }
}
```

> [!NOTE]
> Sekcja z adnotacjami w tej `Split()` metodzie jest jedyną sekcją kodu szablonu Menedżera zadań, która jest przeznaczona do modyfikacji przez dodanie logiki w celu podzielenia zadań na różne zadania. Aby zmodyfikować inną sekcję szablonu, należy się upewnić, że wiesz już, jak działa przetwarzanie wsadowe, i wypróbować kilka [przykładów kodu partii][github_samples].
> 
> 

Implementacja Split () ma dostęp do:

* Parametry zadania, za pomocą `_parameters` pola.
* Obiekt CloudJob reprezentujący zadanie, za pośrednictwem `_job` pola.
* Obiekt CloudTask reprezentujący zadanie Menedżera zadań, za pomocą `_jobManagerTask` pola.

`Split()`Implementacja nie musi bezpośrednio dodawać zadań do zadania. Zamiast tego kod powinien zwrócić sekwencję obiektów CloudTask i zostaną one dodane do zadania automatycznie przez klasy struktur, które wywołują rozdzielacz zadań. Często można używać funkcji iteratora języka C# ( `yield return` ) w celu zaimplementowania rozdzielaczów zadań, co umożliwia uruchamianie zadań tak szybko, jak to możliwe, a nie w oczekiwaniu na obliczenia wszystkich zadań.

**Niepowodzenie rozdzielacza zadania**

Jeśli rozdzielacz zadania napotka błąd, powinien:

* Przerwij sekwencję przy użyciu instrukcji języka C# `yield break` , w tym przypadku Menedżer zadań będzie traktowany jako pomyślne; lub
* Zgłoś wyjątek, w takim przypadku Menedżer zadań będzie traktowany jako niepowodzenie i może być ponowiony w zależności od tego, jak klient został skonfigurowany.

W obu przypadkach każde zadanie, które zostało już zwrócone przez rozdzielacz zadania i dodane do zadania usługi Batch, będzie kwalifikować się do uruchomienia. Jeśli nie chcesz tego zrobić, możesz:

* Zakończ zadanie przed powrotem z rozdzielacza zadań
* Formułowanie całej kolekcji zadań przed jej zwróceniem (oznacza to, że Zwróć `ICollection<CloudTask>` lub `IList<CloudTask>` zamiast implementowania rozdzielacza zadania przy użyciu iteratora języka C#)
* Użyj zależności zadań, aby wykonać wszystkie zadania zależą od pomyślnego ukończenia Menedżera zadań

**Ponowne próby Menedżera zadań**

Jeśli Menedżer zadań nie powiedzie się, może to być ponowiona przez usługę Batch w zależności od ustawień ponownych prób klienta. Ogólnie rzecz biorąc, jest to bezpieczne, ponieważ gdy struktura dodaje zadania do zadania, ignoruje wszystkie zadania, które już istnieją. Jeśli jednak Obliczanie zadań jest kosztowne, nie można ponieść kosztów ponownego obliczania zadań, które zostały już dodane do zadania; z drugiej strony, jeśli ponowne uruchomienie nie jest gwarantowane do wygenerowania tych samych identyfikatorów zadań, zachowanie "Zignoruj duplikaty" nie zostanie uruchomione. W takich przypadkach należy zaprojektować rozdzielacz zadania w celu wykrywania wykonanej pracy, a nie powtarzania jej, na przykład przez wykonanie CloudJob. ListTasks przed rozpoczęciem wykonywania zadań.

### <a name="exit-codes-and-exceptions-in-the-job-manager-template"></a>Kody zakończenia i wyjątki w szablonie Menedżera zadań
Kody i wyjątki wyjściowe zapewniają mechanizm określania wyniku działania programu i mogą pomóc w zidentyfikowaniu wszelkich problemów z wykonywaniem programu. Szablon Menedżera zadań implementuje kody zakończenia i wyjątki opisane w tej sekcji.

Zadanie Menedżera zadań zaimplementowane przy użyciu szablonu Menedżera zadań może zwracać trzy możliwe kody zakończenia:

| Kod | Opis |
| --- | --- |
| 0 |Menedżer zadań zakończył pracę pomyślnie. Kod rozdzielacza zadania został zrealizowany i wszystkie zadania zostały dodane do zadania. |
| 1 |Zadanie Menedżera zadań nie powiodło się, ponieważ wystąpił wyjątek w części "oczekiwanej" programu. Wyjątek został przetłumaczony na JobManagerException z informacjami diagnostycznymi oraz, gdzie to możliwe, sugestie dotyczące rozwiązania błędu. |
| 2 |Zadanie Menedżera zadań nie powiodło się z powodu nieoczekiwanego wyjątku. Wyjątek został zarejestrowany w standardowym wyjściu, ale Menedżer zadań nie mógł dodać żadnych dodatkowych informacji diagnostycznych ani korygowanych. |

W przypadku niepowodzenia zadania Menedżera zadań niektóre zadania mogły być nadal dodawane do usługi przed wystąpieniem błędu. Te zadania będą wykonywane w zwykły sposób. Zobacz "Niepowodzenie rozdzielacza zadania" powyżej, aby poznać dyskusję o tej ścieżce kodu.

Wszystkie informacje zwrócone przez wyjątki są zapisywane w plikach stdout. txt i stderr. txt. Aby uzyskać więcej informacji, zobacz [Obsługa błędów](error-handling.md).

### <a name="client-considerations"></a>Uwagi dotyczące klienta
W tej sekcji opisano niektóre wymagania dotyczące implementacji klienta podczas wywoływania Menedżera zadań w oparciu o ten szablon. Zobacz [, jak przekazać parametry i zmienne środowiskowe z kodu klienta,](#pass-environment-settings) Aby uzyskać szczegółowe informacje na temat przekazywania parametrów i ustawień środowiska.

**Obowiązkowe poświadczenia**

Aby można było dodać zadania do zadania Azure Batch, zadanie Menedżera zadań wymaga adresu URL i klucza konta Azure Batch. Należy przekazać je w zmiennych środowiskowych o nazwie YOUR_BATCH_URL i YOUR_BATCH_KEY. Można je ustawić w ustawieniach środowiska zadania Menedżera zadań. Na przykład w kliencie C#:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    new EnvironmentSetting("YOUR_BATCH_URL", "https://account.region.batch.azure.com"),
    new EnvironmentSetting("YOUR_BATCH_KEY", "{your_base64_encoded_account_key}"),
};
```
**Poświadczenia magazynu**

Zwykle klient nie musi podawać poświadczeń konta połączonego magazynu do zadania Menedżera zadań, ponieważ (a) większość menedżerów zadań nie musi jawnie uzyskiwać dostępu do połączonego konta magazynu i (b) połączone konto magazynu jest często udostępniane wszystkim zadaniom w ramach typowego ustawienia środowiska dla tego zadania. Jeśli nie podasz połączonego konta magazynu za pomocą wspólnych ustawień środowiska, a Menedżer zadań wymaga dostępu do połączonego magazynu, należy podać następujące poświadczenia połączonego magazynu:

```csharp
job.JobManagerTask.EnvironmentSettings = new [] {
    /* other environment settings */
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

**Ustawienia zadania Menedżera zadań**

Klient powinien ustawić flagę *killJobOnCompletion* Menedżera zadań na **wartość false**.

Jest to zwykle bezpieczne, aby klient ustawił *runExclusive* na **wartość false**.

Klient powinien użyć kolekcji *resourceFiles* lub *applicationPackageReferences* , aby można było wdrożyć plik wykonywalny Menedżera zadań (i jego wymagane biblioteki dll) w węźle obliczeniowym.

Domyślnie Menedżer zadań nie zostanie ponowiony, jeśli zakończy się niepowodzeniem. W zależności od logiki Menedżera zadań klient może chcieć włączyć ponowną próbę przy użyciu *ograniczeń* / *maxTaskRetryCount*.

**Ustawienia zadania**

Jeśli rozdzielacz zadania emituje zadania z zależnościami, klient musi ustawić usesTaskDependencies dla zadania na wartość true.

W modelu rozdzielacza zadań niekiedy klienci chcą dodawać zadania do zadań powyżej i powyżej tego, co tworzy rozdzielacz zadania. W związku z tym klient powinien zwykle ustawić *onAllTasksComplete* zadania na **terminatejob**.

## <a name="task-processor-template"></a>Szablon procesora zadań
Szablon procesora zadań ułatwia zaimplementowanie procesora zadań, który może wykonywać następujące czynności:

* Skonfiguruj informacje wymagane przez każde zadanie wsadowe do uruchomienia.
* Uruchom wszystkie akcje wymagane przez każde zadanie wsadowe.
* Zapisz dane wyjściowe zadania w magazynie trwałym.

Mimo że procesor zadań nie jest wymagany do uruchamiania zadań w usłudze Batch, najważniejsze zalety korzystania z procesora zadań to, że oferuje otokę do implementowania wszystkich akcji wykonywania zadań w jednej lokalizacji. Na przykład, jeśli konieczne jest uruchomienie kilku aplikacji w kontekście każdego zadania, lub jeśli trzeba będzie skopiować dane do magazynu trwałego po wykonaniu każdego zadania.

Akcje wykonywane przez procesor zadań mogą być proste lub złożone oraz dowolną liczbę lub kilka, zgodnie z wymaganiami obciążenia. Ponadto, implementując wszystkie akcje zadań w jednym procesorze zadań, można łatwo zaktualizować lub dodać akcje na podstawie zmian w aplikacjach lub wymaganiach dotyczących obciążenia. Jednak w niektórych przypadkach procesor zadań może nie być najlepszym rozwiązaniem dla implementacji, ponieważ może to spowodować dodanie niepotrzebnej złożoności, na przykład podczas uruchamiania zadań, które można szybko uruchomić z prostego wiersza polecenia.

### <a name="create-a-task-processor-using-the-template"></a>Tworzenie procesora zadań przy użyciu szablonu
Aby dodać procesor zadań do utworzonego wcześniej rozwiązania, wykonaj następujące kroki:

1. Otwórz istniejące rozwiązanie w programie Visual Studio.
2. W Eksplorator rozwiązań kliknij prawym przyciskiem myszy rozwiązanie, kliknij polecenie **Dodaj**, a następnie kliknij pozycję **Nowy projekt**.
3. W obszarze **Visual C#** kliknij pozycję **chmura**, a następnie kliknij pozycję **Azure Batch procesora zadań**.
4. Wpisz nazwę opisującą aplikację i zidentyfikuj ten projekt jako procesor zadań (np. "LitwareTaskProcessor").
5. Aby utworzyć projekt, kliknij przycisk **OK**.
6. Na koniec Skompiluj projekt, aby wymusić, że program Visual Studio załaduje wszystkie pakiety NuGet, do których istnieją odwołania, i sprawdź, czy projekt jest prawidłowy przed rozpoczęciem modyfikowania.

### <a name="task-processor-template-files-and-their-purpose"></a>Pliki szablonów procesora zadań i ich przeznaczenie
Podczas tworzenia projektu przy użyciu szablonu procesora zadań generuje on trzy grupy plików kodu:

* Główny plik programu (Program.cs). Zawiera ona punkt wejścia programu i obsługę wyjątków najwyższego poziomu. Zwykle nie trzeba go modyfikować.
* Katalog struktury. Zawiera ona pliki odpowiedzialne za "typowe" działania wykonywane przez program Menedżer zadań — rozpakowywanie parametrów, dodanie zadań do zadania wsadowego itd. Zwykle nie trzeba modyfikować tych plików.
* Plik procesora zadań (TaskProcessor.cs). W tym miejscu zostanie umieszczona logika specyficzna dla aplikacji służąca do wykonywania zadania (zazwyczaj przez wywołanie do istniejącego pliku wykonywalnego). Kod poprzedzający i po przetworzeniu, taki jak pobieranie dodatkowych danych lub przekazywanie plików wynikowych, również przechodzą tutaj.

Oczywiście można dodać dodatkowe pliki wymagane do obsługi kodu procesora zadań, w zależności od złożoności logiki podziału zadania.

Szablon generuje również standardowe pliki projektu .NET, takie jak plik. csproj, App. config, Packages. config itd.

W pozostałej części tej sekcji opisano różne pliki i ich struktury kodu oraz wyjaśniono, co robi każda klasa.

![Program Visual Studio Eksplorator rozwiązań pokazujący rozwiązanie szablonu procesora zadań][solution_explorer02]

**Pliki struktury**

* `Configuration.cs`: Hermetyzuje ładowanie danych konfiguracji zadania, takich jak szczegóły konta, powiązane poświadczenia konta magazynu, informacje o zadaniach i zadaniach oraz parametry zadań. Zapewnia również dostęp do zmiennych środowiskowych zdefiniowanych przez partię (zobacz Ustawienia środowiska dla zadań, w dokumentacji usługi Batch) za pośrednictwem klasy Configuration. zmiennych środowiskowych.
* `IConfiguration.cs`: Jest abstrakcyjna implementacja klasy konfiguracji, dzięki czemu można testować rozdzielacz zadania przy użyciu obiektu konfiguracji fałszywego lub makiety.
* `TaskProcessorException.cs`: Reprezentuje błąd, który wymaga przerwania Menedżera zadań. TaskProcessorException jest używany do zawijania "oczekiwanych" błędów, w przypadku których konkretne informacje diagnostyczne mogą być dostarczane jako część zakończenia.

**Procesor zadań**

* `TaskProcessor.cs`: Uruchamia zadanie. Struktura wywołuje metodę TaskProcessor. Run. Jest to Klasa, w której zostanie wprowadzona logika specyficzna dla aplikacji. Zaimplementuj metodę Run, aby:
  * Analizowanie i weryfikowanie wszelkich parametrów zadań
  * Redaguj wiersz polecenia dla dowolnego programu zewnętrznego, który ma zostać wywołany
  * Rejestruj wszystkie informacje diagnostyczne, które mogą być wymagane do celów debugowania
  * Uruchamianie procesu przy użyciu tego wiersza polecenia
  * Poczekaj na zakończenie procesu
  * Przechwyć kod zakończenia procesu, aby określić, czy zakończył się powodzeniem, czy niepowodzeniem
  * Zapisz wszystkie pliki wyjściowe, które mają być przechowywane w magazynie trwałym

**Standardowe pliki projektu wiersza polecenia platformy .NET**

* `App.config`: Standardowy plik konfiguracji aplikacji .NET.
* `Packages.config`: Standardowy plik zależności pakietu NuGet.
* `Program.cs`: Zawiera punkt wejścia programu i obsługę wyjątków najwyższego poziomu.

## <a name="implementing-the-task-processor"></a>Implementowanie procesora zadań
Po otwarciu projektu szablonu procesora zadań projekt będzie miał domyślnie otwarty plik TaskProcessor.cs. Można zaimplementować logikę uruchamiania dla zadań w obciążeniu przy użyciu metody Run () pokazanej poniżej:

```csharp
/// <summary>
/// Runs the task processing logic. This is where you inject
/// your application-specific logic for decomposing the job into tasks.
///
/// The task processor framework invokes the Run method for you; you need
/// only to implement it, not to call it yourself. Typically, your
/// implementation will execute an external program (from resource files or
/// an application package), check the exit code of that program and
/// save output files to persistent storage.
/// </summary>
public async Task<int> Run()

{
    try
    {
        //Your code for the task processor goes here.
        var command = $"compare {_parameters["Frame1"]} {_parameters["Frame2"]} compare.gif";
        using (var process = Process.Start($"cmd /c {command}"))
        {
            process.WaitForExit();
            var taskOutputStorage = new TaskOutputStorage(
            _configuration.StorageAccount,
            _configuration.JobId,
            _configuration.TaskId
            );
            await taskOutputStorage.SaveAsync(
            TaskOutputKind.TaskOutput,
            @"..\stdout.txt",
            @"stdout.txt"
            );
            return process.ExitCode;
        }
    }
    catch (Exception ex)
    {
        throw new TaskProcessorException(
        $"{ex.GetType().Name} exception in run task processor: {ex.Message}",
        ex
        );
    }
}
```
> [!NOTE]
> Sekcja z adnotacją w metodzie Run () jest jedyną sekcją kodu szablonu procesora zadań, która jest przeznaczona do modyfikacji przez dodanie logiki uruchamiania dla zadań w obciążeniu. Jeśli chcesz zmodyfikować inną sekcję szablonu, najpierw zapoznaj się z informacjami o tym, jak działa sekwencja zadań wsadowych, przeglądając dokumentację dotyczącą programu Batch i próbując wykonać kilka przykładów kodu partii.
> 
> 

Metoda Run () jest odpowiedzialna za uruchamianie wiersza polecenia, uruchamianie jednego lub kilku procesów, oczekiwanie na zakończenie całego procesu, zapisywanie wyników i zwracanie kodu zakończenia. Metoda Run () służy do implementowania logiki przetwarzania dla zadań podrzędnych. Platforma procesora zadań wywołuje dla Ciebie metodę Run (). nie musisz samodzielnie dzwonić.

Implementacja Run () ma dostęp do:

* Parametry zadania, za pomocą `_parameters` pola.
* Identyfikatory zadań i zadań, za pomocą `_jobId` pól i `_taskId` .
* Konfiguracja zadania, za pomocą `_configuration` pola.

**Niepowodzenie zadania**

W przypadku awarii można wyjść z metody Run (), zgłaszając wyjątek, ale pozostawia obsługę wyjątków najwyższego poziomu w kontroli kodu zakończenia zadania. Jeśli musisz kontrolować kod zakończenia, aby można było rozróżnić różne typy błędów, na przykład dla celów diagnostycznych lub niektóre tryby awarii powinny kończyć zadanie, a inne nie powinny, a następnie należy zamknąć metodę Run (), zwracając kod zakończenia różny od zera. Zostanie to kod zakończenia zadania.

### <a name="exit-codes-and-exceptions-in-the-task-processor-template"></a>Kody zakończenia i wyjątki w szablonie procesora zadań
Kody i wyjątki wyjściowe zapewniają mechanizm określania wyniku działania programu i mogą pomóc w identyfikowaniu problemów z wykonywaniem programu. Szablon procesora zadań implementuje kody zakończenia i wyjątki opisane w tej sekcji.

Zadanie procesora zadań, które jest implementowane za pomocą szablonu procesora zadań, może zwracać trzy możliwe kody zakończenia:

| Kod | Opis |
| --- | --- |
| [Process. ExitCode][process_exitcode] |Procesor zadań działał do ukończenia. Należy zauważyć, że nie oznacza to, że wywoływany program zakończył się powodzeniem — tylko pomyślnie wywołany przez niego procesor zadań i przeprowadzono wszystkie przetwarzanie końcowe bez wyjątków. Znaczenie kodu zakończenia zależy od wywoływanego programu — zazwyczaj kod zakończenia 0 oznacza, że program zakończył się powodzeniem, a jakikolwiek inny kod zakończenia oznacza, że program nie powiódł się. |
| 1 |Procesor zadań nie powiódł się, ponieważ wystąpił wyjątek w części "oczekiwanej" programu. Wyjątek został przetłumaczony na `TaskProcessorException` informacje diagnostyczne i, gdzie to możliwe, sugestie dotyczące rozwiązania błędu. |
| 2 |Procesor zadań nie powiódł się z powodu nieoczekiwanego wyjątku. Wyjątek został zarejestrowany w standardowym wyjściu, ale procesor zadań nie mógł dodać żadnych dodatkowych informacji diagnostycznych ani korygowania. |

> [!NOTE]
> Jeśli wywoływany program używa kodów zakończenia 1 i 2 w celu wskazania konkretnych trybów awarii, wówczas użycie kodów zakończenia 1 i 2 dla błędów procesora zadań jest niejednoznaczne. Można zmienić te kody błędów procesora zadań na charakterystyczne kody wyjścia, edytując przypadki wyjątków w pliku Program.cs.
> 
> 

Wszystkie informacje zwrócone przez wyjątki są zapisywane w plikach stdout. txt i stderr. txt. Aby uzyskać więcej informacji, zobacz Obsługa błędów w dokumentacji usługi Batch.

### <a name="client-considerations"></a>Uwagi dotyczące klienta
**Poświadczenia magazynu**

Jeśli procesor zadań korzysta z usługi Azure Blob Storage w celu utrwalania danych wyjściowych, na przykład przy użyciu biblioteki pomocnika Konwencji plików, musi mieć dostęp do poświadczeń konta *magazynu w chmurze* *lub* do adresu URL kontenera obiektów blob, który zawiera sygnaturę dostępu współdzielonego (SAS). Szablon obejmuje obsługę udostępniania poświadczeń za pomocą wspólnych zmiennych środowiskowych. Klient może przekazać poświadczenia magazynu w następujący sposób:

```csharp
job.CommonEnvironmentSettings = new [] {
    new EnvironmentSetting("LINKED_STORAGE_ACCOUNT", "{storageAccountName}"),
    new EnvironmentSetting("LINKED_STORAGE_KEY", "{storageAccountKey}"),
};
```

Konto magazynu jest następnie dostępne w klasie TaskProcessor za pośrednictwem `_configuration.StorageAccount` właściwości.

Jeśli wolisz używać adresu URL kontenera z sygnaturą dostępu współdzielonego, możesz również przekazać je za pomocą typowego ustawienia środowiska zadania, ale szablon procesora zadania nie zawiera obecnie wbudowanej obsługi.

**Konfiguracja magazynu**

Zaleca się, aby zadanie klienta lub Menedżera zadań utworzyło wszystkie kontenery wymagane przez zadania przed dodaniem zadań do zadania. Jest to wymagane w przypadku używania adresu URL kontenera z sygnaturą dostępu współdzielonego, ponieważ taki adres URL nie obejmuje uprawnienia do tworzenia kontenera. Jest to zalecane nawet w przypadku przekazania poświadczeń konta magazynu, ponieważ zapisuje każde zadanie, które ma wywołać CloudBlobContainer. CreateIfNotExistsAsync na kontenerze.

## <a name="pass-parameters-and-environment-variables"></a>Przekazywanie parametrów i zmiennych środowiskowych
### <a name="pass-environment-settings"></a>Przekaż ustawienia środowiska
Klient może przekazać informacje do zadania Menedżera zadań w postaci ustawień środowiska. Te informacje mogą być następnie używane przez zadanie Menedżera zadań podczas generowania zadań procesora zadań, które zostaną uruchomione w ramach zadania obliczeniowego. Przykłady informacji, które można przekazać jako ustawienia środowiska:

* Nazwa konta magazynu i klucze konta
* Adres URL konta wsadowego
* Klucz konta w usłudze Batch

Usługa Batch ma prosty mechanizm przekazywania ustawień środowiska do zadania Menedżera zadań przy użyciu `EnvironmentSettings` właściwości w [Microsoft. Azure. Batch. JobManagerTask][net_jobmanagertask].

Na przykład aby uzyskać `BatchClient` wystąpienie dla konta usługi Batch, można przekazać jako zmienne środowiskowe z kodu klienta poświadczenia adresu URL i klucza współdzielonego dla konta usługi Batch. Analogicznie, aby uzyskać dostęp do konta magazynu połączonego z kontem wsadowym, można przekazać nazwę konta magazynu i klucz konta magazynu jako zmienne środowiskowe.

### <a name="pass-parameters-to-the-job-manager-template"></a>Przekazywanie parametrów do szablonu Menedżera zadań
W wielu przypadkach przydatne jest przekazywanie parametrów poszczególnych zadań do zadania Menedżera zadań w celu kontrolowania procesu dzielenia zadania lub konfigurowania zadań dla tego zadania. Można to zrobić, przekazując plik JSON o nazwie Parameters. JSON jako plik zasobów zadania Menedżera zadań. Parametry mogą następnie być dostępne w `JobSplitter._parameters` polu szablonu Menedżera zadań.

> [!NOTE]
> Wbudowana procedura obsługi parametrów obsługuje tylko słowniki ciągów do ciągu. Jeśli chcesz przekazać złożone wartości JSON jako wartości parametrów, musisz przekazać je jako ciągi i przeanalizować je w rozdzielaczu zadania lub zmodyfikować `Configuration.GetJobParameters` metodę struktury.
> 
> 

### <a name="pass-parameters-to-the-task-processor-template"></a>Przekazywanie parametrów do szablonu procesora zadań
Parametry można także przekazać do poszczególnych zadań wdrożonych przy użyciu szablonu procesora zadań. Podobnie jak w przypadku szablonu Menedżera zadań szablon procesora zadań szuka pliku zasobów o nazwie

Parameters. JSON i jeśli został znaleziony, ładuje go jako słownik parametrów. Istnieje kilka opcji przekazywania parametrów do zadań procesora zadań:

* Ponownie Użyj kodu JSON parametrów zadania. Jest to dobre rozwiązanie, jeśli jedyne parametry są zależne od zadania (na przykład wysokość i szerokość renderowania). Aby to zrobić, podczas tworzenia CloudTask w rozdzielaczu zadań Dodaj odwołanie do obiektu plik zasobu Parameters. JSON z zadania Menedżera zadań ResourceFiles ( `JobSplitter._jobManagerTask.ResourceFiles` ) do kolekcji ResourceFiles CloudTask.
* Generuj i przekazuj specyficzne dla zadania parametry. JSON jako część wykonywania zadania rozdzielacza i odwołują się do tego obiektu BLOB w kolekcji plików zasobów zadania. Jest to konieczne, jeśli różne zadania mają różne parametry. Przykładem może być scenariusz renderowania 3W, w którym indeks ramki jest przenoszona do zadania jako parametr.

> [!NOTE]
> Wbudowana procedura obsługi parametrów obsługuje tylko słowniki ciągów do ciągu. Jeśli chcesz przekazać złożone wartości JSON jako wartości parametrów, musisz przekazać je jako ciągi i przeanalizować je w procesorze zadań lub zmodyfikować `Configuration.GetTaskParameters` metodę struktury.
> 
> 

## <a name="next-steps"></a>Następne kroki
### <a name="persist-job-and-task-output-to-azure-storage"></a>Utrwalanie danych wyjściowych zadania i zadania w usłudze Azure Storage
Innym przydatnym narzędziem do tworzenia rozwiązań wsadowych jest [Azure Batch Konwencji plików][nuget_package]. Ta biblioteka klas .NET (obecnie dostępna w wersji zapoznawczej) w aplikacjach wsadowych platformy .NET umożliwia łatwe przechowywanie i pobieranie danych wyjściowych zadań do i z usługi Azure Storage. [Utrwalaj Azure Batch zadanie i dane wyjściowe zadania](batch-task-output.md) zawierają pełną dyskusję biblioteki i jej użycia.


[net_jobmanagertask]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.jobmanagertask.aspx
[github_samples]: https://github.com/Azure/azure-batch-samples
[nuget_package]: https://www.nuget.org/packages/Microsoft.Azure.Batch.Conventions.Files
[process_exitcode]: https://msdn.microsoft.com/library/system.diagnostics.process.exitcode.aspx
[vs_gallery]: https://visualstudiogallery.msdn.microsoft.com/
[vs_gallery_templates]: https://github.com/Azure/batch-extension-templates
[vs_find_use_ext]: https://msdn.microsoft.com/library/dd293638.aspx

[diagram01]: ./media/batch-visual-studio-templates/diagram01.png
[solution_explorer01]: ./media/batch-visual-studio-templates/solution_explorer01.png
[solution_explorer02]: ./media/batch-visual-studio-templates/solution_explorer02.png
