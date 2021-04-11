---
title: Wdróż pakiety aplikacji w węzłach obliczeniowych
description: Funkcja pakietów aplikacji programu Azure Batch umożliwia łatwe zarządzanie wieloma aplikacjami i wersjami do zainstalowania w węzłach obliczeniowych usługi Batch.
ms.topic: how-to
ms.date: 03/24/2021
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: 17729b2b6a2c4faba0554339a382cad559e9894a
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210503"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji wsadowych

Pakiety aplikacji mogą uprościć kod w rozwiązaniu Azure Batch i ułatwić zarządzanie aplikacjami uruchomionymi przez zadania. Pakiety aplikacji umożliwiają przekazywanie i zarządzanie wieloma wersjami aplikacji uruchamianych przez zadania, w tym ich pliki pomocnicze. Następnie można automatycznie wdrożyć co najmniej jedną z tych aplikacji w węzłach obliczeniowych w puli.

Interfejsy API służące do tworzenia pakietów aplikacji i zarządzania nimi są częścią biblioteki [zarządzania usługą Batch dla platformy .NET](/dotnet/api/overview/azure/batch/management) . Interfejsy API służące do instalowania pakietów aplikacji w węźle obliczeniowym są częścią biblioteki usługi [Batch .NET](/dotnet/api/overview/azure/batch/client) . Porównywalne funkcje są dostępne dla dostępnych interfejsów API usługi Batch dla innych języków.

W tym artykule wyjaśniono, jak przekazywać pakiety aplikacji i zarządzać nimi w Azure Portal. Pokazano również, jak zainstalować je w węzłach obliczeniowych puli z biblioteką [programu .NET Batch](/dotnet/api/overview/azure/batch/client) .

## <a name="application-package-requirements"></a>Wymagania pakietu aplikacji

Aby można było korzystać z pakietów aplikacji, należy [połączyć konto usługi Azure Storage](#link-a-storage-account) z kontem w usłudze Batch.

Istnieją ograniczenia dotyczące liczby aplikacji i pakietów aplikacji w ramach konta wsadowego oraz maksymalnego rozmiaru pakietu aplikacji. Aby uzyskać więcej informacji, zobacz [limity przydziału i limity dla usługi Azure Batch](batch-quota-limit.md).

> [!NOTE]
> Pule usługi Batch utworzone przed 5 lipca 2017 nie obsługują pakietów aplikacji (chyba że zostały utworzone po 10 marca 2016, przy użyciu konfiguracji Cloud Services). Opisana tutaj funkcja pakietów aplikacji zastępuje funkcję usługi Batch dostępną w poprzednich wersjach usługi.

## <a name="understand-applications-and-application-packages"></a>Informacje o aplikacjach i pakietach aplikacji

W Azure Batch *aplikacja* odwołuje się do zestawu plików binarnych z wersjami, które mogą być automatycznie pobierane do węzłów obliczeniowych w puli. Aplikacja zawiera co najmniej jeden *pakiet aplikacji*, który reprezentuje różne wersje aplikacji.

Każdy *pakiet aplikacji* jest plikiem ZIP, który zawiera pliki binarne aplikacji i pliki pomocnicze. Obsługiwany jest tylko format ZIP.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagram przedstawiający ogólny widok aplikacji i pakietów aplikacji.":::

Możesz określić pakiety aplikacji na poziomie puli lub zadania.

- **Pakiety aplikacji puli** są wdrażane w każdym węźle w puli. Aplikacje są wdrażane, gdy węzeł jest przyłączany do puli, a kiedy jest ponownie uruchamiany lub odtwarzany z obrazu.
  
    Pakiety aplikacji puli są odpowiednie, gdy wszystkie węzły w puli będą wykonywać zadania zadania podrzędnego. Podczas tworzenia puli można określić co najmniej jeden pakiet aplikacji do wdrożenia. Możesz również dodać lub zaktualizować istniejące pakiety puli. Aby zainstalować nowy pakiet w istniejącej puli, należy ponownie uruchomić jego węzły.

- **Pakiety aplikacji zadania** są wdrażane tylko w węźle obliczeniowym zaplanowanym do uruchomienia zadania, tuż przed uruchomieniem wiersza polecenia zadania podrzędnego. Jeśli określony pakiet aplikacji i wersja znajduje się już w węźle, nie jest wdrażany ponownie i używany jest istniejący pakiet.
  
    Pakiety aplikacji zadań są przydatne w środowiskach puli udostępnionej, w których różne zadania są uruchamiane w jednej puli, a pula nie jest usuwana po zakończeniu zadania. Jeśli zadanie ma mniej zadań niż węzły w puli, pakiety aplikacji zadania mogą zminimalizować transfer danych, ponieważ aplikacja jest wdrażana tylko w węzłach, na których działają zadania.
  
    Inne scenariusze, które mogą korzystać z pakietów aplikacji zadań, to zadania, na których działa duża aplikacja, ale tylko dla kilku zadań. Na przykład aplikacje zadania mogą być przydatne w przypadku dużej fazy przetwarzania wstępnego lub zadania scalania.

W przypadku pakietów aplikacji zadanie uruchamiania puli nie musi określać długiej listy poszczególnych plików zasobów do zainstalowania w węzłach. Nie trzeba ręcznie zarządzać wieloma wersjami plików aplikacji w usłudze Azure Storage ani w węzłach. Nie musisz martwić się o generowanie [adresów URL SAS](../storage/common/storage-sas-overview.md) , aby zapewnić dostęp do plików na koncie magazynu. Usługa Batch działa w tle w usłudze Azure Storage do przechowywania pakietów aplikacji i wdrażania ich w węzłach obliczeniowych.

> [!NOTE]
> Całkowity rozmiar zadania podrzędnego uruchamiania musi wynosić 32 768 znaków, w tym pliki zasobów lub zmienne środowiskowe, lub być mniejszy. Jeśli zadanie uruchomieniowe przekracza ten limit, używanie pakietów aplikacji jest kolejną opcją. Możesz również utworzyć plik zip zawierający pliki zasobów, przekazać go jako obiekt BLOB do usługi Azure Storage, a następnie rozpakować go z wiersza polecenia zadania podrzędnego.

## <a name="upload-and-manage-applications"></a>Przekazywanie aplikacji i zarządzanie nimi

Za pomocą [Azure Portal](https://portal.azure.com) lub interfejsów API zarządzania usługą Batch można zarządzać pakietami aplikacji na koncie w usłudze Batch. W poniższych sekcjach wyjaśniono, jak połączyć konto magazynu oraz jak dodawać aplikacje i pakiety aplikacji oraz zarządzać nimi w Azure Portal.

> [!NOTE]
> Podczas definiowania wartości aplikacji w [Microsoft.Batch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts) zasobu [szablonu ARM](quick-create-template.md)nie jest obecnie możliwe używanie szablonu ARM do przekazywania pakietów aplikacji do użycia na koncie w usłudze Batch. Musisz przekazać je na połączone konto magazynu, zgodnie z [poniższym](#add-a-new-application)opisem.

### <a name="link-a-storage-account"></a>Łączenie konta magazynu

Aby można było korzystać z pakietów aplikacji, należy połączyć [konto usługi Azure Storage](accounts.md#azure-storage-accounts) z kontem w usłudze Batch. Usługa Batch będzie używać skojarzonego konta magazynu do przechowywania pakietów aplikacji. Zalecamy utworzenie konta magazynu przeznaczonego do użycia z kontem usługi Batch.

Jeśli konto magazynu nie zostało jeszcze skonfigurowane, Azure Portal wyświetli ostrzeżenie przy pierwszym wybraniu **aplikacji** na koncie w usłudze Batch. Aby połączyć konto magazynu z kontem w usłudze Batch, wybierz pozycję **konto magazynu** w oknie **ostrzeżenia** , a następnie ponownie wybierz pozycję **konto magazynu** .

Po połączeniu dwóch kont program Batch może automatycznie wdrożyć pakiety przechowywane na połączonym koncie magazynu w węzłach obliczeniowych.

> [!IMPORTANT]
> Nie można używać pakietów aplikacji z kontami usługi Azure Storage skonfigurowanymi przy użyciu [reguł zapory](../storage/common/storage-network-security.md)lub z **hierarchiczną przestrzenią nazw** ustawioną na wartość **włączone**.

Usługa Batch używa usługi Azure Storage do przechowywania pakietów aplikacji jako blokowych obiektów BLOB. Opłaty są [naliczone jako normalne](https://azure.microsoft.com/pricing/details/storage/) dla danych blokowych obiektów blob, a rozmiar każdego pakietu nie może przekroczyć maksymalnego rozmiaru bloku obiektów BLOB. Aby uzyskać więcej informacji, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage dla kont magazynu](../storage/blobs/scalability-targets.md). Aby zminimalizować koszty, należy wziąć pod uwagę rozmiar i liczbę pakietów aplikacji oraz okresowe usuwanie przestarzałych pakietów.

### <a name="view-current-applications"></a>Wyświetl bieżące aplikacje

Aby wyświetlić aplikacje na koncie w usłudze Batch, wybierz pozycję **aplikacje** w menu nawigacji po lewej stronie.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Zrzut ekranu przedstawiający element menu aplikacje w Azure Portal.":::

Wybranie tej opcji menu spowoduje otwarcie okna **aplikacje** . W tym oknie jest wyświetlany identyfikator każdej aplikacji na Twoim koncie oraz następujące właściwości:

- **Pakiety**: liczba wersji skojarzonych z tą aplikacją.
- **Wersja domyślna**: Jeśli ma zastosowanie, wersja aplikacji, która zostanie zainstalowana, jeśli nie zostanie określona żadna wersja podczas wdrażania aplikacji.
- **Zezwalaj na aktualizacje**: określa, czy aktualizacje i usunięcia pakietu są dozwolone.

Aby wyświetlić [strukturę plików](files-and-directories.md) pakietu aplikacji w węźle obliczeniowym, przejdź do konta partii w Azure Portal. Wybierz pozycję **Pule**. następnie wybierz pulę zawierającą węzeł obliczeniowy. Wybierz węzeł obliczeniowy, na którym jest zainstalowany pakiet aplikacji, a następnie otwórz folder **aplikacje** .

### <a name="view-application-details"></a>Wyświetl szczegóły aplikacji

Aby wyświetlić szczegóły dotyczące aplikacji, wybierz ją w oknie **aplikacje** . Można skonfigurować następujące ustawienia dla aplikacji.

- **Zezwalaj na aktualizacje**: wskazuje, czy pakiety aplikacji mogą być [aktualizowane lub usuwane](#update-or-delete-an-application-package). Wartość domyślna to **Tak**. Jeśli ustawisz wartość **nie**, nie można aktualizować ani usuwać istniejących pakietów aplikacji, ale nadal można dodawać nowe wersje pakietów aplikacji.
- **Wersja domyślna**: domyślny pakiet aplikacji, który ma być używany podczas wdrażania aplikacji, jeśli nie określono żadnej wersji.
- **Nazwa wyświetlana**: przyjazna nazwa, która może być używana przez rozwiązanie do przetwarzania wsadowego, gdy wyświetla informacje o aplikacji. Na przykład ta nazwa może być używana w interfejsie użytkownika usługi udostępnianej klientom w usłudze Batch.

### <a name="add-a-new-application"></a>Dodaj nową aplikację

Aby utworzyć nową aplikację, należy dodać pakiet aplikacji i określić unikatowy identyfikator aplikacji.

Na koncie wsadowym wybierz pozycję **aplikacje** , a następnie wybierz pozycję **Dodaj**.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Zrzut ekranu przedstawiający proces tworzenia nowej aplikacji w Azure Portal.":::

Wprowadź następujące informacje:

- **Identyfikator aplikacji**: identyfikator nowej aplikacji.
- **Wersja**": wersja pakietu aplikacji, który przekazujesz.
- **Pakiet aplikacji**: plik zip zawierający pliki binarne aplikacji i pliki pomocnicze, które są wymagane do wykonania aplikacji.

**Identyfikator aplikacji** i wprowadzona **wersja** muszą spełniać następujące wymagania:

- W węzłach systemu Windows identyfikator może zawierać dowolną kombinację znaków alfanumerycznych, łączników i podkreśleń. W węzłach systemu Linux dozwolone są tylko znaki alfanumeryczne i podkreślenia.
- Nie może zawierać więcej niż 64 znaków.
- Musi być unikatowa w ramach konta wsadowego.
- Identyfikatory uwzględniają wielkość liter i nie uwzględniają wielkości liter.

Gdy wszystko będzie gotowe, wybierz pozycję **Prześlij**. Po przekazaniu pliku zip na konto usługi Azure Storage Portal wyświetli powiadomienie. W zależności od rozmiaru przekazywanego pliku i szybkości połączenia sieciowego może to potrwać trochę czasu.

### <a name="add-a-new-application-package"></a>Dodaj nowy pakiet aplikacji

Aby dodać wersję pakietu aplikacji dla istniejącej aplikacji, wybierz aplikację w sekcji **aplikacje** na koncie usługi Batch, a następnie wybierz pozycję **Dodaj**.

Tak jak w przypadku nowej aplikacji, określ **wersję** nowego pakietu, Przekaż plik. zip w polu **pakiet aplikacji** , a następnie wybierz pozycję **Prześlij**.

### <a name="update-or-delete-an-application-package"></a>Aktualizowanie lub usuwanie pakietu aplikacji

Aby zaktualizować lub usunąć istniejący pakiet aplikacji, wybierz aplikację w sekcji **aplikacje** na koncie w usłudze Batch. Wybierz wielokropek w wierszu pakietu aplikacji, który chcesz zmodyfikować, a następnie wybierz akcję, którą chcesz wykonać.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Zrzut ekranu przedstawiający Opcje aktualizacji i usuwania pakietów aplikacji w Azure Portal.":::

W przypadku wybrania opcji **Aktualizuj** będzie możliwe przekazanie nowego pliku. zip. Spowoduje to zastąpienie poprzedniego pliku zip przekazanego dla tej wersji.

W przypadku wybrania opcji **Usuń** zostanie wyświetlony monit o potwierdzenie usunięcia tej wersji. Po wybraniu **przycisku OK** usługa Batch usunie plik zip z konta usługi Azure Storage. Jeśli usuniesz domyślną wersję aplikacji, **domyślne ustawienie wersji** zostanie usunięte dla tej aplikacji.

## <a name="install-applications-on-compute-nodes"></a>Instalowanie aplikacji w węzłach obliczeniowych

Teraz, gdy wiesz już, jak zarządzać pakietami aplikacji w Azure Portal, możemy omówić sposób wdrażania ich w węzłach obliczeniowych i uruchamiania ich przy użyciu zadań wsadowych.

### <a name="install-pool-application-packages"></a>Zainstaluj pakiety aplikacji puli

Aby zainstalować pakiet aplikacji na wszystkich węzłach obliczeniowych w puli, określ co najmniej jedno odwołanie do pakietu aplikacji dla puli. Pakiety aplikacji określone dla puli są instalowane na każdym węźle obliczeniowym przyłączanym do puli oraz w każdym węźle, który jest ponownie uruchamiany lub odtwarzany z obrazu.

W usłudze Batch .NET Określ co najmniej jedną [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) podczas tworzenia nowej puli lub dla istniejącej puli. Klasa [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) określa identyfikator i wersję aplikacji do zainstalowania w węzłach obliczeniowych puli.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        cloudServiceConfiguration: new CloudServiceConfiguration(osFamily: "5"));

// Specify the application and version to install on the compute nodes
myCloudPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "litware",
        Version = "1.1001.2b" }
};

// Commit the pool so that it's created in the Batch service. As the nodes join
// the pool, the specified application package is installed on each.
await myCloudPool.CommitAsync();
```

> [!IMPORTANT]
> Jeśli wdrożenie pakietu aplikacji nie powiedzie się, usługa Batch oznaczy węzeł jako [bezużyteczny](/dotnet/api/microsoft.azure.batch.computenode.state)i nie zaplanowano wykonywania zadań w tym węźle. W takim przypadku należy ponownie uruchomić węzeł, aby zainicjować wdrożenie pakietu. Ponowne uruchomienie węzła umożliwia również ponowne planowanie zadań w węźle.

### <a name="install-task-application-packages"></a>Zainstaluj pakiety aplikacji zadania

Podobnie jak w przypadku puli, należy określić odwołania pakietu aplikacji dla zadania. Gdy zadanie jest zaplanowane do uruchomienia w węźle, pakiet zostanie pobrany i wyodrębniony tuż przed wykonaniem wiersza polecenia zadania podrzędnego. Jeśli określony pakiet i wersja zostały już zainstalowane w węźle, pakiet nie zostanie pobrany i zostanie użyty istniejący pakiet.

Aby zainstalować pakiet aplikacji zadania, należy skonfigurować właściwość [CloudTask. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) zadania:

```csharp
CloudTask task =
    new CloudTask(
        "litwaretask001",
        "cmd /c %AZ_BATCH_APP_PACKAGE_LITWARE%\\litware.exe -args -here");

task.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference
    {
        ApplicationId = "litware",
        Version = "1.1001.2b"
    }
};
```

## <a name="execute-the-installed-applications"></a>Wykonaj zainstalowane aplikacje

Pakiety określone dla puli lub zadania są pobierane i wyodrębniane do nazwanego katalogu w `AZ_BATCH_ROOT_DIR` węźle. Wsadowe tworzy również zmienną środowiskową, która zawiera ścieżkę do nazwanego katalogu. Wiersze poleceń zadania używają tej zmiennej środowiskowej podczas odwoływania się do aplikacji w węźle.

W węzłach systemu Windows zmienna ma następujący format:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

W węzłach systemu Linux format jest nieco inny. Kropki (.), łączniki (-) i znaki liczbowe (#) są spłaszczone do podkreślenia w zmiennej środowiskowej. Należy również pamiętać, że sprawa identyfikatora aplikacji jest zachowywana. Na przykład:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` i `version` to wartości, które odpowiadają wersji aplikacji i pakietu określonej do wdrożenia. Na przykład jeśli określono, że w węzłach systemu Windows zostanie zainstalowana wersja 2,7 programu Application *Blend* , wiersze poleceń zadań użyją tej zmiennej środowiskowej, aby uzyskać dostęp do swoich plików:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

W węzłach systemu Linux określ zmienną środowiskową w tym formacie. Spłaszcz kropki (.), łączniki (-) i znaki numeru (#), aby podkreślić i zachować wielkość liter identyfikatora aplikacji:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Podczas przekazywania pakietu aplikacji można określić domyślną wersję do wdrożenia w węzłach obliczeniowych. Jeśli określono domyślną wersję aplikacji, można pominąć sufiks wersji podczas odwoływania się do aplikacji. Domyślną wersję aplikacji można określić w Azure Portal w oknie **aplikacje** , jak pokazano w temacie [przekazywanie aplikacji i zarządzanie nimi](#upload-and-manage-applications).

Na przykład jeśli ustawisz "2,7" jako wersję domyślną dla programu Application *Blend*, a zadania odwołują się do następującej zmiennej środowiskowej, węzły systemu Windows będą wykonywały wersję 2,7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Poniższy fragment kodu przedstawia przykładowy wiersz polecenia zadania, który uruchamia domyślną wersję aplikacji *Blend* :

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Aby uzyskać więcej informacji na temat ustawień środowiska węzłów obliczeniowych, zobacz [Ustawienia środowiska dla zadań](jobs-and-tasks.md#environment-settings-for-tasks).

## <a name="update-a-pools-application-packages"></a>Aktualizowanie pakietów aplikacji puli

Jeśli istniejąca pula została już skonfigurowana przy użyciu pakietu aplikacji, można określić nowy pakiet dla puli. Składają się na to następujące elementy:

- Usługa Batch instaluje nowo określony pakiet na wszystkich nowych węzłach dołączanych do puli i w każdym istniejącym węźle, który jest ponownie uruchamiany lub odtwarzany z obrazu.
- Węzły obliczeniowe, które znajdują się już w puli, po zaktualizowaniu odwołań do pakietów nie instalują automatycznie nowego pakietu aplikacji. Te węzły obliczeniowe muszą być ponownie uruchomione lub odtwarzane z obrazu, aby otrzymać nowy pakiet.
- Po wdrożeniu nowego pakietu utworzone zmienne środowiskowe odzwierciedlają nowe odwołania do pakietu aplikacji.

W tym przykładzie istniejąca pula ma wersję 2,7 aplikacji *Blend* , która została skonfigurowana jako jedna z jej [CloudPool. ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences). Aby zaktualizować węzły puli przy użyciu wersji 2.76 b, określ nowy [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) z nową wersją i zatwierdź zmianę.

```csharp
string newVersion = "2.76b";
CloudPool boundPool = await batchClient.PoolOperations.GetPoolAsync("myPool");
boundPool.ApplicationPackageReferences = new List<ApplicationPackageReference>
{
    new ApplicationPackageReference {
        ApplicationId = "blender",
        Version = newVersion }
};
await boundPool.CommitAsync();
```

Teraz, gdy nowa wersja została skonfigurowana, usługa Batch instaluje wersję 2.76 b w dowolnym nowym węźle dołączanym do puli. Aby zainstalować 2.76 b w węzłach, które znajdują się już w puli, uruchom je ponownie lub Odtwórz z obrazu. Należy zauważyć, że ponownie uruchomione węzły zachowują pliki z wcześniejszych wdrożeń pakietu.

## <a name="list-the-applications-in-a-batch-account"></a>Wyświetlanie listy aplikacji na koncie wsadowym

Aplikacje i ich pakiety można wyświetlić na koncie usługi Batch za pomocą metody [ApplicationOperations. ListApplicationSummaries](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries) .

```csharp
// List the applications and their application packages in the Batch account.
List<ApplicationSummary> applications = await batchClient.ApplicationOperations.ListApplicationSummaries().ToListAsync();
foreach (ApplicationSummary app in applications)
{
    Console.WriteLine("ID: {0} | Display Name: {1}", app.Id, app.DisplayName);

    foreach (string version in app.Versions)
    {
        Console.WriteLine("  {0}", version);
    }
}
```

## <a name="next-steps"></a>Następne kroki

- [Interfejs API REST usługi Batch](/rest/api/batchservice) zapewnia również obsługę pracy z pakietami aplikacji. Na przykład, zobacz element [applicationPackageReferences](/rest/api/batchservice/pool/add#applicationpackagereference) , aby określić pakiety do zainstalowania, oraz [aplikacje](/rest/api/batchservice/application) , dla których mają zostać uzyskane informacje o aplikacji.
- Dowiedz się, jak programowo [zarządzać kontami i przydziałami Azure Batch przy użyciu programu Batch Management .NET](batch-management-dotnet.md). Biblioteka [platformy .NET zarządzania usługą Batch](/dotnet/api/overview/azure/batch/management) może włączyć funkcje tworzenia i usuwania kont dla aplikacji lub usługi Batch.
