---
title: Wdrażanie pakietów aplikacji w węzłach obliczeniowych
description: Funkcja pakietów aplikacji usługi Azure Batch umożliwia łatwe zarządzanie wieloma aplikacjami i wersjami na użytek instalacji w węzłach obliczeniowych usługi Batch.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom:
- H1Hack27Feb2017
- devx-track-csharp
- contperf-fy21q1
ms.openlocfilehash: 9c4b40f0e99475fc0b19ec94a14f67af131e5f59
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389387"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji usługi Batch

Pakiety aplikacji mogą uprościć kod w Azure Batch i ułatwić zarządzanie aplikacjami uruchamianymi przez zadania. Za pomocą pakietów aplikacji można przekazywać wiele wersji aplikacji uruchamianych przez zadania oraz zarządzać nimi, w tym ich plikami obsługi. Następnie możesz automatycznie wdrożyć co najmniej jedną z tych aplikacji w węzłach obliczeniowych w puli.

Interfejsy API do tworzenia pakietów aplikacji i zarządzania nimi są częścią biblioteki .NET zarządzania [usługą Batch.](/dotnet/api/overview/azure/batch/management) Interfejsy API do instalowania pakietów aplikacji w węźle obliczeniowym są częścią biblioteki [.NET usługi Batch.](/dotnet/api/overview/azure/batch/client) Porównywalne funkcje znajdują się w dostępnych interfejsach API usługi Batch dla innych języków.

W tym artykule wyjaśniono, jak przekazywać pakiety aplikacji i zarządzać nimi w Azure Portal. Pokazano również, jak zainstalować je w węzłach obliczeniowych puli przy użyciu biblioteki [usługi Batch dla programu .NET.](/dotnet/api/overview/azure/batch/client)

## <a name="application-package-requirements"></a>Wymagania dotyczące pakietu aplikacji

Aby korzystać z pakietów aplikacji, musisz [połączyć konto usługi Azure Storage z](#link-a-storage-account) kontem usługi Batch.

Istnieją ograniczenia dotyczące liczby aplikacji i pakietów aplikacji w ramach konta usługi Batch oraz maksymalnego rozmiaru pakietu aplikacji. Aby uzyskać więcej informacji, zobacz [Limity przydziału i limity dla Azure Batch usługi](batch-quota-limit.md).

> [!NOTE]
> Pule usługi Batch utworzone przed 5 lipca 2017 r. nie obsługują pakietów aplikacji (chyba że zostały utworzone po 10 marca 2016 r. przy użyciu usługi Cloud Services Configuration). Opisana tutaj funkcja pakietów aplikacji umożliwia nadsyłanie funkcji aplikacji usługi Batch dostępnej w poprzednich wersjach usługi.

## <a name="understand-applications-and-application-packages"></a>Opis aplikacji i pakietów aplikacji

W Azure Batch *aplikacja* odwołuje się do zestawu wersjonarowanych plików binarnych, które mogą być automatycznie pobierane do węzłów obliczeniowych w puli. Aplikacja zawiera co najmniej jeden *pakiet aplikacji*, który reprezentuje różne wersje aplikacji.

Każdy *pakiet aplikacji* jest plikiem zip zawierającym pliki binarne aplikacji i wszelkie pliki obsługi. Obsługiwany jest tylko format zip.

:::image type="content" source="media/batch-application-packages/app_pkg_01.png" alt-text="Diagram przedstawiający widok wysokiego poziomu aplikacji i pakietów aplikacji.":::

Pakiety aplikacji można określić na poziomie puli lub zadania.

- **Pakiety aplikacji puli** są wdrażane w każdym węźle w puli. Aplikacje są wdrażane po dołączeniu węzła do puli oraz ponownym uruchomieniu lub ponownym uruchomieniu.
  
    Pakiety aplikacji puli są odpowiednie, gdy wszystkie węzły w puli będą wykonywać zadania podrzędne zadania. Podczas tworzenia puli można określić co najmniej jeden pakiet aplikacji do wdrożenia. Możesz również dodać lub zaktualizować pakiety istniejącej puli. Aby zainstalować nowy pakiet w istniejącej puli, należy ponownie uruchomić jego węzły.

- **Pakiety aplikacji zadań są** wdrażane tylko w węźle obliczeniowym zaplanowanym do uruchomienia zadania, tuż przed uruchomieniem wiersza polecenia zadania. Jeśli określony pakiet aplikacji i jego wersja znajduje się już w węźle, nie zostanie on ponownie przelodowany i zostanie użyty istniejący pakiet.
  
    Pakiety aplikacji zadań są przydatne w środowiskach z udostępnioną pulą, w których różne zadania są uruchamiane w jednej puli, a pula nie jest usuwana po zakończeniu zadania. Jeśli zadanie ma mniej zadań podrzędnych niż węzły w puli, pakiety aplikacji zadań podrzędnych mogą zminimalizować transfer danych, ponieważ aplikacja jest wdrażana tylko w węzłach, w których są uruchamiane zadania podrzędne.
  
    Inne scenariusze, które mogą skorzystać z pakietów aplikacji zadań, to zadania z dużą aplikacją, ale tylko dla kilku zadań. Na przykład aplikacje zadań mogą być przydatne w przypadku etapu przetwarzania wstępnego z dużym obciążeniem lub zadania scalania.

W przypadku pakietów aplikacji zadanie uruchamiania puli nie musi określać długiej listy pojedynczych plików zasobów do zainstalowania w węzłach. Nie trzeba ręcznie zarządzać wieloma wersjami plików aplikacji w usłudze Azure Storage ani w węzłach. Nie musisz też martwić się o generowanie adresów [URL sygnatur](../storage/common/storage-sas-overview.md) dostępu współdzielonego w celu zapewnienia dostępu do plików na koncie usługi Storage. Usługa Batch działa w tle z usługą Azure Storage, aby przechowywać pakiety aplikacji i wdrażać je w węzłach obliczeniowych.

> [!NOTE]
> Całkowity rozmiar zadania podrzędnego uruchamiania musi wynosić 32 768 znaków, w tym pliki zasobów lub zmienne środowiskowe, lub być mniejszy. Jeśli zadanie uruchamiania przekroczy ten limit, użycie pakietów aplikacji jest inną opcją. Możesz również utworzyć plik zip zawierający pliki zasobów, przekazać go jako obiekt blob do usługi Azure Storage, a następnie rozpakować z wiersza polecenia zadania uruchamiania.

## <a name="upload-and-manage-applications"></a>Przekazywanie aplikacji i zarządzanie nimi

Do zarządzania pakietami aplikacji [na koncie usługi](https://portal.azure.com) Batch można użyć interfejsu API zarządzania usługą Azure Portal Batch. W poniższych sekcjach wyjaśniono, jak połączyć konto magazynu oraz jak dodawać aplikacje i pakiety aplikacji oraz zarządzać nimi w Azure Portal.

> [!NOTE]
> Chociaż wartości aplikacji można definiować w zasobie [ ch/batchAccounts](/azure/templates/microsoft.batch/batchaccounts) usługiMicrosoft.Batszablonu [usługi ARM,](quick-create-template.md)obecnie nie jest możliwe użycie szablonu usługi ARM do przekazywania pakietów aplikacji do użycia na koncie usługi Batch. Należy przekazać je do połączonego konta magazynu zgodnie z poniższym [opisem.](#add-a-new-application)

### <a name="link-a-storage-account"></a>Łączenie konta magazynu

Aby korzystać z pakietów aplikacji, musisz połączyć konto [usługi Azure Storage](accounts.md#azure-storage-accounts) z kontem usługi Batch. Usługa Batch będzie używać skojarzonego konta magazynu do przechowywania pakietów aplikacji. Zalecamy utworzenie konta magazynu przeznaczonego specjalnie do użytku z kontem usługi Batch.

Jeśli konto magazynu nie zostało jeszcze skonfigurowane, podczas Azure Portal przy pierwszym wybraniu opcji Aplikacje **na** koncie usługi Batch zostanie wyświetlone ostrzeżenie. Aby połączyć konto magazynu z kontem usługi Batch, wybierz pozycję **Konto magazynu** w oknie **Ostrzeżenie,** a następnie ponownie wybierz **pozycję Konto** magazynu.

Po nawiązyniu tych dwóch kont usługa Batch może automatycznie wdrożyć pakiety przechowywane na połączonym koncie magazynu w węzłach obliczeniowych.

> [!IMPORTANT]
> Nie można używać pakietów aplikacji z kontami [](../storage/common/storage-network-security.md)usługi Azure Storage skonfigurowanymi przy użyciu reguł zapory lub z ustawieniem **Hierarchiczna** przestrzeń nazw ustawioną na **wartość Włączone.**

Usługa Batch używa usługi Azure Storage do przechowywania pakietów aplikacji jako blokowych obiektów blob. Opłaty za [dane blokowych obiektów](https://azure.microsoft.com/pricing/details/storage/) blob są naliczane normalnie, a rozmiar każdego pakietu nie może przekraczać maksymalnego rozmiaru blokowych obiektów blob. Aby uzyskać więcej informacji, zobacz [Azure Storage scalability and performance targets for storage accounts (Cele](../storage/blobs/scalability-targets.md)dotyczące skalowalności i wydajności usługi Azure Storage dla kont magazynu). Aby zminimalizować koszty, należy wziąć pod uwagę rozmiar i liczbę pakietów aplikacji oraz okresowo usuwać przestarzałe pakiety.

### <a name="view-current-applications"></a>Wyświetlanie bieżących aplikacji

Aby wyświetlić aplikacje na koncie usługi Batch, wybierz pozycję **Aplikacje** w menu nawigacji po lewej stronie.

:::image type="content" source="media/batch-application-packages/app_pkg_02.png" alt-text="Zrzut ekranu przedstawiający element menu Aplikacje w Azure Portal.":::

Wybranie tej opcji menu powoduje otwarcie **okna** Aplikacje. W tym oknie są wyświetlane identyfikatory poszczególnych aplikacji na Twoim koncie i następujące właściwości:

- **Pakiety:** liczba wersji skojarzonych z tą aplikacją.
- **Wersja domyślna:** jeśli ma to zastosowanie, wersja aplikacji, która zostanie zainstalowana, jeśli podczas wdrażania aplikacji nie zostanie określona żadna wersja.
- **Zezwalaj na aktualizacje:** określa, czy aktualizacje i usunięcia pakietów są dozwolone.

Aby wyświetlić [strukturę plików pakietu](files-and-directories.md) aplikacji w węźle obliczeniowym, przejdź do konta usługi Batch w Azure Portal. Wybierz **pozycję Pule.** następnie wybierz pulę zawierającą węzeł obliczeniowy. Wybierz węzeł obliczeniowy, na którym zainstalowano pakiet aplikacji, i otwórz folder **applications.**

### <a name="view-application-details"></a>Wyświetlanie szczegółów aplikacji

Aby wyświetlić szczegóły aplikacji, wybierz ją w **oknie** Aplikacje. Dla aplikacji można skonfigurować następujące ustawienia.

- **Zezwalaj na** aktualizacje: wskazuje, czy można aktualizować lub [usuwać pakiety aplikacji.](#update-or-delete-an-application-package) Wartość domyślna to **Tak**. W przypadku ustawienia **wartości Nie** nie można aktualizować ani usuwać istniejących pakietów aplikacji, ale nadal można dodawać nowe wersje pakietów aplikacji.
- **Wersja domyślna:** domyślny pakiet aplikacji do użycia podczas wdrażania aplikacji, jeśli nie określono wersji.
- **Nazwa wyświetlana:** przyjazna nazwa, z których rozwiązanie usługi Batch może korzystać podczas wyświetlania informacji o aplikacji. Na przykład tej nazwy można użyć w interfejsie użytkownika usługi, która jest dostarczana klientom za pośrednictwem usługi Batch.

### <a name="add-a-new-application"></a>Dodawanie nowej aplikacji

Aby utworzyć nową aplikację, należy dodać pakiet aplikacji i określić unikatowy identyfikator aplikacji.

Na koncie usługi Batch wybierz pozycję **Aplikacje,** a następnie wybierz pozycję **Dodaj**.

:::image type="content" source="media/batch-application-packages/app_pkg_05.png" alt-text="Zrzut ekranu przedstawiający proces tworzenia nowej aplikacji w Azure Portal.":::

Wprowadź następujące informacje:

- **Identyfikator aplikacji:** identyfikator nowej aplikacji.
- **Wersja**": wersja pakietu aplikacji, który jest przesyłany.
- **Pakiet aplikacji:** plik zip zawierający pliki binarne aplikacji i pliki obsługi wymagane do wykonania aplikacji.

W **wprowadzanych identyfikatorach** **aplikacji** i wersji muszą być spełnione następujące wymagania:

- W węzłach systemu Windows identyfikator może zawierać dowolną kombinację znaków alfanumerycznych, łączników i podkreśleń. W węzłach systemu Linux dozwolone są tylko znaki alfanumeryczne i podkreślenia.
- Nie może zawierać więcej niż 64 znaków.
- Musi być unikatowa w obrębie konta usługi Batch.
- Identyfikatory są zachowywane i bez uwzględniania liter.

Gdy wszystko będzie gotowe, wybierz pozycję **Prześlij**. Po przesłaniu pliku zip na konto usługi Azure Storage w portalu zostanie wyświetlone powiadomienie. W zależności od rozmiaru pliku, który jest przesyłany, i szybkości połączenia sieciowego może to zająć trochę czasu.

### <a name="add-a-new-application-package"></a>Dodawanie nowego pakietu aplikacji

Aby dodać wersję pakietu aplikacji dla istniejącej aplikacji, wybierz aplikację w sekcji **Aplikacje** na koncie usługi Batch, a następnie wybierz pozycję **Dodaj**.

Podobnie jak w przypadku nowej  aplikacji, określ wersję nowego pakietu, przekaż plik zip w polu **Pakiet** aplikacji, a następnie wybierz pozycję **Prześlij**.

### <a name="update-or-delete-an-application-package"></a>Aktualizowanie lub usuwanie pakietu aplikacji

Aby zaktualizować lub usunąć istniejący pakiet aplikacji, wybierz aplikację w sekcji **Aplikacje** na koncie usługi Batch. Wybierz wielokropek w wierszu pakietu aplikacji, który chcesz zmodyfikować, a następnie wybierz akcję, którą chcesz wykonać.

:::image type="content" source="media/batch-application-packages/app_pkg_07.png" alt-text="Zrzut ekranu przedstawiający opcje aktualizacji i usuwania pakietów aplikacji w Azure Portal.":::

W przypadku **wybrania opcji** Aktualizuj będzie można przekazać nowy plik zip. Spowoduje to zastąpienie poprzedniego pliku zip przekazanego dla tej wersji.

W przypadku wybrania **opcji Usuń** zostanie wyświetlony monit o potwierdzenie usunięcia tej wersji. Po wybraniu **przycisku OK** usługa Batch usunie plik zip z konta usługi Azure Storage. Jeśli usuniesz domyślną wersję aplikacji, ustawienie Wersja domyślna zostanie usunięte dla tej aplikacji. 

## <a name="install-applications-on-compute-nodes"></a>Instalowanie aplikacji w węzłach obliczeniowych

Teraz, gdy już wiesz, jak zarządzać pakietami aplikacji na Azure Portal, możemy omówić sposób wdrażania ich w węzłach obliczeniowych i uruchamiania ich za pomocą zadań usługi Batch.

### <a name="install-pool-application-packages"></a>Instalowanie pakietów aplikacji puli

Aby zainstalować pakiet aplikacji na wszystkich węzłach obliczeniowych w puli, określ co najmniej jedno odwołania do pakietu aplikacji dla puli. Pakiety aplikacji określone dla puli są instalowane w każdym węźle obliczeniowym, który jest przyłączony do puli, oraz na każdym węźle, który jest ponownie uruchamiany lub uruchamiany z obrazu.

W programie .NET usługi Batch określ co najmniej jeden element [CloudPool.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) podczas tworzenia nowej puli lub dla istniejącej puli. Klasa [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) określa identyfikator aplikacji i wersję do zainstalowania w węzłach obliczeniowych puli.

```csharp
// Create the unbound CloudPool
CloudPool myCloudPool =
    batchClient.PoolOperations.CreatePool(
        poolId: "myPool",
        targetDedicatedComputeNodes: 1,
        virtualMachineSize: "standard_d1_v2",
        VirtualMachineConfiguration: new VirtualMachineConfiguration(
            imageReference: new ImageReference(
                                publisher: "MicrosoftWindowsServer",
                                offer: "WindowsServer",
                                sku: "2019-datacenter-core",
                                version: "latest"),
            nodeAgentSkuId: "batch.node.windows amd64");

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
> Jeśli wdrożenie pakietu aplikacji zakończy się niepowodzeniem, usługa Batch oznacza węzeł jako nienadatny do [użytku,](/dotnet/api/microsoft.azure.batch.computenode.state)a w tym węźle nie są zaplanowane żadne zadania. W takim przypadku uruchom ponownie węzeł, aby ponownie uruchomić wdrożenie pakietu. Ponowne uruchomienie węzła umożliwia również ponowne planowanie zadań w węźle.

### <a name="install-task-application-packages"></a>Instalowanie pakietów aplikacji zadań

Podobnie jak w przypadku puli, należy określić odwołania do pakietu aplikacji dla zadania. Gdy zadanie jest zaplanowane do uruchomienia w węźle, pakiet jest pobierany i wyodrębniony tuż przed wykonaniem wiersza polecenia zadania. Jeśli określony pakiet i wersja są już zainstalowane w węźle, pakiet nie zostanie pobrany i zostanie użyty istniejący pakiet.

Aby zainstalować pakiet aplikacji podzadań, skonfiguruj właściwość [CloudTask.ApplicationPackageReferences](/dotnet/api/microsoft.azure.batch.cloudtask.applicationpackagereferences) zadania:

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

## <a name="execute-the-installed-applications"></a>Wykonywanie zainstalowanych aplikacji

Pakiety określone dla puli lub zadania zostaną pobrane i wyodrębnione do nazwanego katalogu w `AZ_BATCH_ROOT_DIR` węźle . Usługa Batch tworzy również zmienną środowiskową zawierającą ścieżkę do nazwanego katalogu. Wiersze poleceń zadań używają tej zmiennej środowiskowej podczas odwoływania się do aplikacji w węźle.

W węzłach systemu Windows zmienna ma następujący format:

```
Windows:
AZ_BATCH_APP_PACKAGE_APPLICATIONID#version
```

W węzłach z systemem Linux format jest nieco inny. Kropki (.), łączniki (-) i znaki liczb (#) są spłaszczone do podkreśleń w zmiennej środowiskowej. Należy również pamiętać, że przypadek identyfikatora aplikacji jest zachowywany. Na przykład:

```
Linux:
AZ_BATCH_APP_PACKAGE_applicationid_version
```

`APPLICATIONID` i `version` są wartościami, które odpowiadają aplikacji i wersji pakietu, które zostały określone dla wdrożenia. Jeśli na przykład określono, że w węzłach systemu Windows powinna być zainstalowana wersja 2.7 programu *Blender* aplikacji, w wierszach polecenia zadania do uzyskiwania dostępu do jego plików będzie używać tej zmiennej środowiskowej:

```
Windows:
AZ_BATCH_APP_PACKAGE_BLENDER#2.7
```

W węzłach systemu Linux określ zmienną środowiskową w tym formacie. Spłaszcz kropki (.), łączniki (-) i znaki liczb (#) do podkreśleń i zachowaj przypadek identyfikatora aplikacji:

```
Linux:
AZ_BATCH_APP_PACKAGE_blender_2_7
```

Podczas przekazywania pakietu aplikacji można określić domyślną wersję do wdrożenia w węzłach obliczeniowych. Jeśli określono domyślną wersję dla aplikacji, można pominąć sufiks wersji podczas odwołania się do aplikacji. Domyślną wersję aplikacji można określić w oknie  Azure Portal, jak pokazano w tece [Przekazywanie aplikacji i zarządzanie nimi.](#upload-and-manage-applications)

Jeśli na przykład ustawisz wartość "2.7" jako wersję domyślną programu *Blender* aplikacji, a zadania odwołują się do następującej zmiennej środowiskowej, węzły systemu Windows będą wykonywać wersję 2.7:

`AZ_BATCH_APP_PACKAGE_BLENDER`

Poniższy fragment kodu przedstawia przykładowy wiersz polecenia zadania, który uruchamia domyślną wersję aplikacji *blender:*

```csharp
string taskId = "blendertask01";
string commandLine =
    @"cmd /c %AZ_BATCH_APP_PACKAGE_BLENDER%\blender.exe -args -here";
CloudTask blenderTask = new CloudTask(taskId, commandLine);
```

> [!TIP]
> Aby uzyskać więcej informacji na temat ustawień środowiska węzła obliczeniowego, zobacz [Environment settings for tasks (Ustawienia środowiska dla zadań).](jobs-and-tasks.md#environment-settings-for-tasks)

## <a name="update-a-pools-application-packages"></a>Aktualizowanie pakietów aplikacji puli

Jeśli istniejąca pula została już skonfigurowana przy użyciu pakietu aplikacji, możesz określić nowy pakiet dla puli. Składają się na to następujące elementy:

- Usługa Batch instaluje nowo określony pakiet na wszystkich nowych węzłach, które dołączają do puli, oraz na każdym istniejącym węźle, który został ponownie uruchomiony lub uruchomiony z obrazu.
- Węzły obliczeniowe, które znajdują się już w puli podczas aktualizowania odwołań do pakietu, nie instalują automatycznie nowego pakietu aplikacji. Aby otrzymać nowy pakiet, należy ponownie uruchomić te węzły obliczeniowe lub ponownie uruchomić je z obrazu.
- Po wdrożeniu nowego pakietu utworzone zmienne środowiskowe odzwierciedlają odwołania do nowego pakietu aplikacji.

W tym przykładzie istniejąca pula ma wersję 2.7 aplikacji *blendera* skonfigurowaną jako jedną z jej pul [CloudPool.ApplicationPackageReferences.](/dotnet/api/microsoft.azure.batch.cloudpool.applicationpackagereferences) Aby zaktualizować węzły puli przy użyciu wersji 2.76b, określ nową wersję [ApplicationPackageReference](/dotnet/api/microsoft.azure.batch.applicationpackagereference) i zateń zmianę.

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

Teraz, gdy nowa wersja została skonfigurowana, usługa Batch instaluje wersję 2.76b we wszystkich nowych węzłach, które dołączają do puli. Aby zainstalować 2,76b na węzłach, które znajdują się już w puli, uruchom je ponownie lub odtąd odtąd. Należy pamiętać, że węzły z ponownym rozruchem zachowują pliki z poprzednich wdrożeń pakietów.

## <a name="list-the-applications-in-a-batch-account"></a>Lista aplikacji na koncie usługi Batch

Listę aplikacji i ich pakietów można wyświetlić na koncie usługi Batch przy użyciu metody [ApplicationOperations.ListApplicationSummaries.](/dotnet/api/microsoft.azure.batch.applicationoperations.listapplicationsummaries)

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

- Interfejs [API REST usługi Batch](/rest/api/batchservice) zapewnia również obsługę pracy z pakietami aplikacji. Na przykład zobacz element [applicationPackageReferences,](/rest/api/batchservice/pool/add#applicationpackagereference) aby dowiedzieć się, jak określać pakiety do zainstalowania, i aplikacje, aby uzyskać informacje o aplikacji. [](/rest/api/batchservice/application)
- Dowiedz się, jak programowo zarządzać [kontami Azure Batch przydziałami za pomocą programu .NET zarządzania usługą Batch.](batch-management-dotnet.md) Biblioteka [.NET zarządzania usługą Batch](/dotnet/api/overview/azure/batch/management) umożliwia tworzenie i usuwanie kont dla aplikacji lub usługi Batch.
