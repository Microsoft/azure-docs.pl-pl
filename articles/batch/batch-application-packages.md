---
title: Wdróż pakiety aplikacji w węzłach obliczeniowych
description: Funkcja pakietów aplikacji programu Azure Batch umożliwia łatwe zarządzanie wieloma aplikacjami i wersjami do zainstalowania w węzłach obliczeniowych usługi Batch.
ms.topic: how-to
ms.date: 04/26/2019
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 61e94ade21d8dd6fad2ba10dff87d4ba10333e3a
ms.sourcegitcommit: 6fd8dbeee587fd7633571dfea46424f3c7e65169
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 05/21/2020
ms.locfileid: "83726880"
---
# <a name="deploy-applications-to-compute-nodes-with-batch-application-packages"></a>Wdrażanie aplikacji w węzłach obliczeniowych za pomocą pakietów aplikacji wsadowych

Funkcja pakietów aplikacji Azure Batch umożliwia łatwe zarządzanie aplikacjami zadań i ich wdrażanie w węzłach obliczeniowych w puli. Pakiety aplikacji umożliwiają przekazywanie i zarządzanie wieloma wersjami aplikacji uruchamianych przez zadania, w tym ich pliki pomocnicze. Następnie można automatycznie wdrożyć co najmniej jedną z tych aplikacji w węzłach obliczeniowych w puli.

W tym artykule dowiesz się, jak przekazywać pakiety aplikacji i zarządzać nimi w Azure Portal. Następnie dowiesz się, jak zainstalować je w węzłach obliczeniowych puli z biblioteką [programu .NET Batch][api_net] .

> [!NOTE]
> Pakiety aplikacji są obsługiwane we wszystkich pulach usługi Batch utworzonych po 5 lipca 2017 r. W pulach usługi Batch utworzonych między 10 marca 2016 r. a 5 lipca 2017 r. są one obsługiwane tylko w przypadku, gdy pula została utworzona za pomocą konfiguracji usługi w chmurze. Pule usługi Batch utworzone przed 10 marca 2016 r. nie obsługują pakietów aplikacji.
>
> Interfejsy API służące do tworzenia pakietów aplikacji i zarządzania nimi są częścią biblioteki [zarządzania usługą Batch dla platformy .NET][api_net_mgmt] . Interfejsy API służące do instalowania pakietów aplikacji w węźle obliczeniowym są częścią biblioteki usługi [Batch .NET][api_net] . Porównywalne funkcje są dostępne dla dostępnych interfejsów API usługi Batch dla innych języków. 
>
> Opisana tutaj funkcja pakietów aplikacji zastępuje funkcję usługi Batch dostępną w poprzednich wersjach usługi.

## <a name="application-package-requirements"></a>Wymagania pakietu aplikacji
Aby można było korzystać z pakietów aplikacji, należy [połączyć konto usługi Azure Storage](#link-a-storage-account) z kontem w usłudze Batch.

## <a name="about-applications-and-application-packages"></a>Aplikacje i pakiety aplikacji — informacje
W Azure Batch *aplikacja* odwołuje się do zestawu plików binarnych z wersjami, które mogą być automatycznie pobierane do węzłów obliczeniowych w puli. *Pakiet aplikacji* odwołuje się do *określonego zestawu* plików binarnych i reprezentuje daną *wersję* aplikacji.

![Diagram wysokiego poziomu aplikacji i pakietów aplikacji][1]

### <a name="applications"></a>Aplikacje
Aplikacja w usłudze Batch zawiera co najmniej jeden pakiet aplikacji i określa opcje konfiguracji aplikacji. Na przykład aplikacja może określić domyślną wersję pakietu aplikacji do zainstalowania w węzłach obliczeniowych i czy pakiety mogą być aktualizowane lub usuwane.

### <a name="application-packages"></a>Pakiety aplikacji
Pakiet aplikacji to plik. zip zawierający pliki binarne aplikacji i pliki pomocnicze, które są wymagane do uruchamiania aplikacji przez zadania. Każdy pakiet aplikacji reprezentuje określoną wersję aplikacji.

Możesz określić pakiety aplikacji na poziomach puli i zadania. Możesz określić co najmniej jeden z tych pakietów i (opcjonalnie) wersję podczas tworzenia puli lub zadania.

* **Pakiety aplikacji puli** są wdrażane w *każdym* węźle w puli. Aplikacje są wdrażane, gdy węzeł jest przyłączany do puli, a kiedy jest ponownie uruchamiany lub odtwarzany z obrazu.
  
    Pakiety aplikacji puli są odpowiednie, gdy wszystkie węzły w puli wykonują zadania zadania podrzędnego. Podczas tworzenia puli można określić jeden lub więcej pakietów aplikacji, a także dodać lub zaktualizować istniejące pakiety puli. W przypadku aktualizacji pakietów aplikacji istniejącej puli należy ponownie uruchomić jej węzły, aby zainstalować nowy pakiet.
* **Pakiety aplikacji zadania** są wdrażane tylko w węźle obliczeniowym zaplanowanym do uruchomienia zadania, tuż przed uruchomieniem wiersza polecenia zadania podrzędnego. Jeśli określony pakiet aplikacji i wersja znajduje się już w węźle, nie jest wdrażany ponownie i używany jest istniejący pakiet.
  
    Pakiety aplikacji zadań są przydatne w środowiskach puli udostępnionej, w których poszczególne zadania są uruchamiane w jednej puli, a pula nie jest usuwana po zakończeniu zadania. Jeśli liczba zadań podrzędnych w zadaniu jest mniejsza niż liczba węzłów w puli, pakiety aplikacji zadania podrzędnego mogą minimalizować ilość transferowanych danych, ponieważ aplikacja jest wdrażana tylko dla węzłów, w których odbywa się uruchamianie zadań podrzędnych.
  
    Inne scenariusze, które mogą korzystać z pakietów aplikacji zadań, to zadania, na których działa duża aplikacja, ale tylko dla kilku zadań. Na przykład etap wstępnego przetwarzania lub zadanie scalania, w przypadku których aplikacja przed przetwarzaniem lub scalaniem jest ciężkim, może korzystać z pakietów aplikacji zadań.

> [!IMPORTANT]
> Istnieją ograniczenia dotyczące liczby aplikacji i pakietów aplikacji w ramach konta wsadowego oraz maksymalnego rozmiaru pakietu aplikacji. Szczegółowe informacje o tych limitach można znaleźć w temacie [limity przydziału i limity dla usługi Azure Batch](batch-quota-limit.md) .
> 
> 

### <a name="benefits-of-application-packages"></a>Zalety pakietów aplikacji
Pakiety aplikacji mogą uprościć kod w rozwiązaniu do obsługi partii i obniżyć obciążenie wymagane do zarządzania aplikacjami uruchomionymi przez zadania.

W przypadku pakietów aplikacji zadanie uruchamiania puli nie musi określać długiej listy poszczególnych plików zasobów do zainstalowania w węzłach. Nie trzeba ręcznie zarządzać wieloma wersjami plików aplikacji w usłudze Azure Storage ani w węzłach. Nie musisz martwić się o generowanie [adresów URL SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) , aby zapewnić dostęp do plików na koncie magazynu. Usługa Batch działa w tle w usłudze Azure Storage do przechowywania pakietów aplikacji i wdrażania ich w węzłach obliczeniowych.

> [!NOTE] 
> Całkowity rozmiar zadania podrzędnego uruchamiania musi wynosić 32 768 znaków, w tym pliki zasobów lub zmienne środowiskowe, lub być mniejszy. Jeśli zadanie uruchomieniowe przekroczy ten limit, za pomocą pakietów aplikacji jest kolejną opcją. Możesz również utworzyć spakowane Archiwum zawierające pliki zasobów, przekazać je jako obiekt BLOB do usługi Azure Storage, a następnie rozpakować ją z wiersza polecenia zadania uruchamiania. 
>
>

## <a name="upload-and-manage-applications"></a>Przekazywanie aplikacji i zarządzanie nimi
Za pomocą [Azure Portal][portal] lub interfejsów API zarządzania usługą Batch można zarządzać pakietami aplikacji na koncie w usłudze Batch. W następnych kilku sekcjach najpierw pokazano, jak połączyć konto magazynu, a następnie omówić Dodawanie aplikacji i pakietów oraz zarządzanie nimi za pomocą portalu.

### <a name="link-a-storage-account"></a>Łączenie konta magazynu
Aby można było korzystać z pakietów aplikacji, musisz najpierw połączyć [konto usługi Azure Storage](batch-api-basics.md#azure-storage-account) z kontem w usłudze Batch. Jeśli konto magazynu nie zostało jeszcze skonfigurowane, Azure Portal wyświetla ostrzeżenie przy pierwszym kliknięciu **aplikacji** na koncie w usłudze Batch.



![Ostrzeżenie "nie skonfigurowano konta magazynu" w Azure Portal][9]

Usługa Batch używa skojarzonego konta magazynu do przechowywania pakietów aplikacji. Po połączeniu dwóch kont program Batch może automatycznie wdrożyć pakiety przechowywane na połączonym koncie magazynu w węzłach obliczeniowych. Aby połączyć konto magazynu z kontem w usłudze Batch, kliknij pozycję **konto magazynu** w oknie **ostrzeżenia** , a następnie ponownie kliknij pozycję **konto magazynu** .

![Wybierz blok konta magazynu w Azure Portal][10]

Zalecamy utworzenie konta magazynu *przeznaczonego* do użycia z kontem usługi Batch i wybranie go w tym miejscu. Po utworzeniu konta magazynu można połączyć je z kontem usługi Batch przy użyciu okna **konto magazynu** .

> [!IMPORTANT] 
> - Obecnie nie można używać pakietów aplikacji z kontem usługi Azure Storage skonfigurowanym przy użyciu [reguł zapory](../storage/common/storage-network-security.md).
> - Konta usługi Azure Storage z **hierarchiczną przestrzenią nazw** ustawioną na wartość **Enabled** nie można używać dla pakietów aplikacji.

Usługa Batch używa usługi Azure Storage do przechowywania pakietów aplikacji jako blokowych obiektów BLOB. Opłaty są [naliczone jako normalne][storage_pricing] dla danych blokowych obiektów blob, a rozmiar każdego pakietu nie może przekroczyć maksymalnego rozmiaru bloku obiektów BLOB. Aby uzyskać więcej informacji, zobacz [cele dotyczące skalowalności i wydajności usługi Azure Storage dla kont magazynu](../storage/blobs/scalability-targets.md). Należy wziąć pod uwagę rozmiar i liczbę pakietów aplikacji oraz okresowe usuwanie przestarzałych pakietów w celu zminimalizowania kosztów.

### <a name="view-current-applications"></a>Wyświetl bieżące aplikacje
Aby wyświetlić aplikacje na koncie w usłudze Batch, kliknij element menu **aplikacje** w menu po lewej stronie podczas przeglądania konta w usłudze **Batch**.

![Kafelek aplikacje][2]

Wybranie tej opcji menu spowoduje otwarcie okna **aplikacje** :

![Wyświetlanie listy aplikacji][3]

W tym oknie jest wyświetlany identyfikator każdej aplikacji na Twoim koncie oraz następujące właściwości:

* **Pakiety**: liczba wersji skojarzonych z tą aplikacją.
* **Wersja domyślna**: zainstalowana wersja aplikacji, jeśli nie wskazano wersji podczas określania aplikacji dla puli. To ustawienie jest opcjonalne.
* **Zezwalaj na aktualizacje**: wartość określająca, czy są dozwolone aktualizacje pakietów, usunięcia i dodatki. Jeśli to ustawienie ma wartość **nie**, aktualizacje i usunięcia pakietu są wyłączone dla aplikacji. Można dodawać tylko nowe wersje pakietów aplikacji. Wartość domyślna to **Tak**.

Jeśli chcesz zobaczyć strukturę plików pakietu aplikacji w węźle obliczeniowym, przejdź do swojego konta w usłudze Batch w portalu. Na koncie usługi Batch przejdź do **pul**. Wybierz pulę zawierającą węzły obliczeniowe, które Cię interesują.

![Węzły w puli][13]

Po wybraniu puli przejdź do węzła obliczeniowego, na którym jest zainstalowany pakiet aplikacji. W tym miejscu szczegóły pakietu aplikacji znajdują się w folderze **aplikacje** . Dodatkowe foldery w węźle obliczeniowym zawierają inne pliki, takie jak zadania uruchamiania, pliki wyjściowe, dane wyjściowe błędów itp.

![Pliki w węźle][14]

### <a name="view-application-details"></a>Wyświetl szczegóły aplikacji
Aby wyświetlić szczegóły dotyczące aplikacji, wybierz aplikację w oknie **aplikacje** .

![Szczegóły aplikacji][4]

W szczegółach aplikacji można skonfigurować następujące ustawienia dla aplikacji.

* **Zezwalaj na aktualizacje**: Określ, czy pakiety aplikacji mogą być aktualizowane lub usuwane. Zobacz sekcję "Aktualizowanie lub usuwanie pakietu aplikacji" w dalszej części tego artykułu.
* **Wersja domyślna**: Określ domyślny pakiet aplikacji do wdrożenia w węzłach obliczeniowych.
* **Nazwa wyświetlana**: Określ przyjazną nazwę, która może być używana przez rozwiązanie usługi Batch podczas wyświetlania informacji o aplikacji, na przykład w interfejsie użytkownika usługi udostępnianej klientom przez usługę Batch.

### <a name="add-a-new-application"></a>Dodaj nową aplikację
Aby utworzyć nową aplikację, Dodaj pakiet aplikacji i określ nowy, unikatowy identyfikator aplikacji. Pierwszy pakiet aplikacji dodany wraz z nowym IDENTYFIKATORem aplikacji również tworzy nową aplikację.

Kliknij pozycję **aplikacje**  >  **Dodaj**.

![Blok nowej aplikacji w Azure Portal][5]

Okno **Nowa aplikacja** zawiera następujące pola umożliwiające określenie ustawień nowej aplikacji i pakietu aplikacji.

**Identyfikator aplikacji**

To pole określa identyfikator nowej aplikacji, który podlega standardowym regułom walidacji identyfikatora Azure Batch. Reguły dotyczące podawania identyfikatora aplikacji są następujące:

* W węzłach systemu Windows identyfikator może zawierać dowolną kombinację znaków alfanumerycznych, łączników i podkreśleń. W węzłach systemu Linux dozwolone są tylko znaki alfanumeryczne i podkreślenia.
* Nie może zawierać więcej niż 64 znaków.
* Musi być unikatowa w ramach konta wsadowego.
* Uwzględnia wielkość liter i uwzględnia wielkość liter.

**Wersja**

To pole określa wersję przekazywanego pakietu aplikacji. Ciągi wersji podlegają następującym regułom walidacji:

* W węzłach systemu Windows ciąg wersji może zawierać dowolną kombinację znaków alfanumerycznych, łączników, podkreśleń i okresów. W węzłach systemu Linux ciąg wersji może zawierać tylko znaki alfanumeryczne i podkreślenia.
* Nie może zawierać więcej niż 64 znaków.
* Musi być unikatowa w obrębie aplikacji.
* Uwzględnia wielkość liter i uwzględnia wielkość liter.

**Pakiet aplikacji**

To pole określa plik. zip, który zawiera pliki binarne aplikacji i pliki pomocnicze wymagane do wykonania aplikacji. Kliknij pole **Wybierz plik** lub ikonę folderu, aby przejść do pliku zip, który zawiera pliki aplikacji.

Po wybraniu pliku kliknij przycisk **OK** , aby rozpocząć przekazywanie do usługi Azure Storage. Po zakończeniu operacji przekazywania w portalu zostanie wyświetlone powiadomienie. W zależności od rozmiaru przekazywanego pliku i szybkości połączenia sieciowego ta operacja może zająć trochę czasu.

> [!WARNING]
> Nie zamykaj **nowego okna aplikacji** przed ukończeniem operacji przekazywania. Wykonanie tej czynności spowoduje zatrzymanie procesu przekazywania.
> 
> 

### <a name="add-a-new-application-package"></a>Dodaj nowy pakiet aplikacji
Aby dodać wersję pakietu aplikacji dla istniejącej aplikacji, wybierz aplikację w oknach **aplikacje** , a następnie kliknij pozycję **pakiety**  >  **Dodaj**.

![Dodaj blok pakietu aplikacji w Azure Portal][8]

Jak widać, pola są zgodne z tymi, które są wyświetlane w oknie **Nowa aplikacja** , ale pole **Identyfikator aplikacji** jest wyłączone. Tak jak w przypadku nowej aplikacji, określ **wersję** nowego pakietu, przejdź do pliku **pakietu aplikacji** . zip, a następnie kliknij przycisk **OK** , aby przekazać pakiet.

### <a name="update-or-delete-an-application-package"></a>Aktualizowanie lub usuwanie pakietu aplikacji
Aby zaktualizować lub usunąć istniejący pakiet aplikacji, Otwórz szczegóły aplikacji, kliknij pozycję **pakiety**, kliknij **wielokropek** w wierszu pakietu aplikacji, który chcesz zmodyfikować, a następnie wybierz akcję, którą chcesz wykonać.

![Aktualizowanie lub usuwanie pakietu w Azure Portal][7]

**Aktualizacja**

Po kliknięciu przycisku **Aktualizuj**zostanie wyświetlony **pakiet aktualizacji** okna. To okno jest podobne do okna **nowego pakietu aplikacji** , ale tylko pole wyboru pakietu jest włączone, co pozwala na określenie nowego pliku zip do przekazania.

![Blok aktualizacji pakietu w Azure Portal][11]

**Usuwanie**

Po kliknięciu przycisku **Usuń**zostanie wyświetlona prośba o potwierdzenie usunięcia wersji pakietu, a usługa Batch usunie pakiet z usługi Azure Storage. Jeśli usuniesz domyślną wersję aplikacji, **domyślne ustawienie wersji** zostanie usunięte dla aplikacji.

![Usuń aplikację][12]

## <a name="install-applications-on-compute-nodes"></a>Instalowanie aplikacji w węzłach obliczeniowych
Teraz, gdy wiesz już, jak zarządzać pakietami aplikacji przy użyciu Azure Portal, możemy omówić sposób wdrażania ich w węzłach obliczeniowych i uruchamiania ich przy użyciu zadań wsadowych.

### <a name="install-pool-application-packages"></a>Zainstaluj pakiety aplikacji puli
Aby zainstalować pakiet aplikacji na wszystkich węzłach obliczeniowych w puli, określ co najmniej jedno *odwołanie* do pakietu aplikacji dla puli. Pakiety aplikacji określone dla puli są instalowane w każdym węźle obliczeniowym, gdy ten węzeł zostanie przyłączony do puli, oraz gdy węzeł zostanie uruchomiony ponownie lub ponownie utworzony z obrazu.

W usłudze Batch .NET Określ co najmniej jeden [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref] podczas tworzenia nowej puli lub dla istniejącej puli. Klasa [ApplicationPackageReference][net_pkgref] określa identyfikator i wersję aplikacji do zainstalowania w węzłach obliczeniowych puli.

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
> Jeśli z jakiegoś powodu nie powiedzie się wdrożenie pakietu aplikacji, usługa Batch oznaczy węzeł jako [bezużyteczny][net_nodestate]i nie zaplanowano wykonywania zadań w tym węźle. W takim przypadku należy **ponownie uruchomić** węzeł w celu ponownego zainicjowania wdrożenia pakietu. Ponowne uruchomienie węzła umożliwia również ponowne planowanie zadań w węźle.
> 
> 

### <a name="install-task-application-packages"></a>Zainstaluj pakiety aplikacji zadania
Podobnie jak w przypadku puli, należy określić *odwołania* pakietu aplikacji dla zadania. Gdy zadanie jest zaplanowane do uruchomienia w węźle, pakiet zostanie pobrany i wyodrębniony tuż przed wykonaniem wiersza polecenia zadania podrzędnego. Jeśli określony pakiet i wersja zostały już zainstalowane w węźle, pakiet nie zostanie pobrany i zostanie użyty istniejący pakiet.

Aby zainstalować pakiet aplikacji zadania, skonfiguruj [CloudTask][net_cloudtask]zadania. Właściwość [ApplicationPackageReferences][net_cloudtask_pkgref] :

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

`APPLICATIONID`i `version` to wartości, które odpowiadają wersji aplikacji i pakietu określonej do wdrożenia. Na przykład jeśli określono, że w węzłach systemu Windows zostanie zainstalowana wersja 2,7 programu Application *Blend* , wiersze poleceń zadań użyją tej zmiennej środowiskowej, aby uzyskać dostęp do swoich plików:

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
> Aby uzyskać więcej informacji na temat ustawień środowiska węzłów obliczeniowych, zobacz [Ustawienia środowiska dla zadań](batch-api-basics.md#environment-settings-for-tasks) w temacie [Omówienie funkcji usługi Batch](batch-api-basics.md) .
> 
> 

## <a name="update-a-pools-application-packages"></a>Aktualizowanie pakietów aplikacji puli
Jeśli istniejąca pula została już skonfigurowana przy użyciu pakietu aplikacji, można określić nowy pakiet dla puli. W przypadku określenia nowego odwołania do pakietu dla puli należy zastosować następujące czynności:

* Usługa Batch instaluje nowo określony pakiet na wszystkich nowych węzłach dołączanych do puli i w każdym istniejącym węźle, który jest ponownie uruchamiany lub odtwarzany z obrazu.
* Węzły obliczeniowe, które znajdują się już w puli, po zaktualizowaniu odwołań do pakietów nie instalują automatycznie nowego pakietu aplikacji. Te węzły obliczeniowe muszą być ponownie uruchomione lub odtwarzane z obrazu, aby otrzymać nowy pakiet.
* Po wdrożeniu nowego pakietu utworzone zmienne środowiskowe odzwierciedlają nowe odwołania do pakietu aplikacji.

W tym przykładzie istniejąca pula ma wersję 2,7 aplikacji *Blend* , która została skonfigurowana jako jedna z jej [CloudPool][net_cloudpool]. [ApplicationPackageReferences][net_cloudpool_pkgref]. Aby zaktualizować węzły puli przy użyciu wersji 2.76 b, określ nowy [ApplicationPackageReference][net_pkgref] z nową wersją i zatwierdź zmianę.

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

Teraz, gdy nowa wersja została skonfigurowana, usługa Batch instaluje wersję 2.76 b w dowolnym *nowym* węźle dołączanym do puli. Aby zainstalować 2.76 b w węzłach, które znajdują się *już* w puli, uruchom je ponownie lub Odtwórz z obrazu. Należy zauważyć, że ponownie uruchomione węzły przechowują pliki z wcześniejszych wdrożeń pakietu.

## <a name="list-the-applications-in-a-batch-account"></a>Wyświetlanie listy aplikacji na koncie wsadowym
Aplikacje i ich pakiety można wyświetlić na koncie usługi Batch za pomocą [ApplicationOperations][net_appops]. [ListApplicationSummaries][net_appops_listappsummaries] .

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

## <a name="wrap-up"></a>Zawijaj w górę
Pakiety aplikacji umożliwiają klientom Wybieranie aplikacji do zadań i określanie dokładnej wersji, która ma być używana podczas przetwarzania zadań z użyciem usługi Batch. Możesz również umożliwić klientom przekazywanie i śledzenie własnych aplikacji w usłudze.

## <a name="next-steps"></a>Następne kroki
* [Interfejs API REST usługi Batch][api_rest] zapewnia również obsługę pracy z pakietami aplikacji. Na przykład zobacz element [applicationPackageReferences][rest_add_pool_with_packages] w temacie [Dodawanie puli do konta][rest_add_pool] , aby uzyskać informacje na temat określania pakietów do zainstalowania przy użyciu interfejsu API REST. Aby uzyskać szczegółowe informacje na temat uzyskiwania informacji o aplikacji przy użyciu interfejsu API REST usługi Batch, zobacz [aplikacje][rest_applications] .
* Dowiedz się, jak programowo [zarządzać kontami i przydziałami Azure Batch przy użyciu programu Batch Management .NET](batch-management-dotnet.md). Biblioteka [platformy .NET zarządzania usługą Batch][api_net_mgmt] może włączyć funkcje tworzenia i usuwania kont dla aplikacji lub usługi Batch.

[api_net]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/client?view=azure-dotnet
[api_net_mgmt]: https://docs.microsoft.com/dotnet/api/overview/azure/batch/management?view=azure-dotnet
[api_rest]: https://docs.microsoft.com/rest/api/batchservice/
[batch_mgmt_nuget]: https://www.nuget.org/packages/Microsoft.Azure.Management.Batch/
[github_samples]: https://github.com/Azure/azure-batch-samples
[storage_pricing]: https://azure.microsoft.com/pricing/details/storage/
[net_appops]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.aspx
[net_appops_listappsummaries]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationoperations.listapplicationsummaries.aspx
[net_cloudpool]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.aspx
[net_cloudpool_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.cloudpool.applicationpackagereferences.aspx
[net_cloudtask]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.aspx
[net_cloudtask_pkgref]: https://msdn.microsoft.com/library/microsoft.azure.batch.cloudtask.applicationpackagereferences.aspx
[net_nodestate]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.computenode.state.aspx
[net_pkgref]: https://msdn.microsoft.com/library/azure/microsoft.azure.batch.applicationpackagereference.aspx
[portal]: https://portal.azure.com
[rest_applications]: https://msdn.microsoft.com/library/azure/mt643945.aspx
[rest_add_pool]: https://msdn.microsoft.com/library/azure/dn820174.aspx
[rest_add_pool_with_packages]: https://msdn.microsoft.com/library/azure/dn820174.aspx#bk_apkgreference

[1]: ./media/batch-application-packages/app_pkg_01.png "Diagramy wysokiego poziomu aplikacji"
[2]: ./media/batch-application-packages/app_pkg_02.png "Kafelek aplikacje w Azure Portal"
[3]: ./media/batch-application-packages/app_pkg_03.png "Blok aplikacje w Azure Portal"
[4]: ./media/batch-application-packages/app_pkg_04.png "Blok szczegóły aplikacji w Azure Portal"
[5]: ./media/batch-application-packages/app_pkg_05.png "Blok nowej aplikacji w Azure Portal"
[7]: ./media/batch-application-packages/app_pkg_07.png "Lista rozwijana aktualizacji lub usuwania pakietów w Azure Portal"
[8]: ./media/batch-application-packages/app_pkg_08.png "Blok nowego pakietu aplikacji w Azure Portal"
[9]: ./media/batch-application-packages/app_pkg_09.png "Brak alertu połączonego konta magazynu"
[10]: ./media/batch-application-packages/app_pkg_10.png "Wybierz blok konta magazynu w Azure Portal"
[11]: ./media/batch-application-packages/app_pkg_11.png "Blok aktualizacji pakietu w Azure Portal"
[12]: ./media/batch-application-packages/app_pkg_12.png "Okno dialogowe usuwania potwierdzenia pakietu w Azure Portal"
[13]: ./media/batch-application-packages/package-file-structure.png "Informacje o węźle obliczeniowym w Azure Portal"
[14]: ./media/batch-application-packages/package-file-structure-node.png "Pliki w węźle obliczeniowym wyświetlanym w Azure Portal"
