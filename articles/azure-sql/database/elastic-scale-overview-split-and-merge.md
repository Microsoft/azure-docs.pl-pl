---
title: Przenoszenie danych między skalowanymi bazami danych w chmurze
description: Wyjaśnia, jak manipulować fragmentów i przenosić dane za pośrednictwem samodzielnej usługi przy użyciu interfejsów API Elastic Database.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: stevestein
ms.author: sstein
ms.reviewer: ''
ms.date: 03/12/2019
ms.openlocfilehash: 9303d84b2862b556a9ccc286ffa118bf1e52b715
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "84047524"
---
# <a name="moving-data-between-scaled-out-cloud-databases"></a>Przenoszenie danych między skalowanymi bazami danych w chmurze
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Jeśli jesteś oprogramowaniem jako deweloperem usługi, a nagle aplikacja przejdzie na ogromne zapotrzebowanie, musisz pomieścić rozwój. Dodaj więcej baz danych (fragmentów). Jak przeprowadzić ponowną dystrybucję danych do nowych baz danych bez zakłócania integralności danych? Użyj **Narzędzia Split-Merge** , aby przenieść dane z ograniczonych baz danych do nowych baz danych.  

Narzędzie do dzielenia i scalania działa jako usługa sieci Web platformy Azure. Administrator lub deweloper używa narzędzia do przenoszenia podfragmentów (dane z fragmentu) między różnymi bazami danych (fragmentów). Narzędzie używa zarządzania mapami fragmentu do obsługi bazy danych metadanych usługi i zapewnia spójne mapowania.

![Omówienie][1]

## <a name="download"></a>Pobierz

[Microsoft. Azure. SQLDatabase. ElasticScale. Service. SplitMerge](https://www.nuget.org/packages/Microsoft.Azure.SqlDatabase.ElasticScale.Service.SplitMerge/)

## <a name="documentation"></a>Dokumentacja

1. [Samouczek narzędzia do dzielenia i scalania Elastic Database](elastic-scale-configure-deploy-split-and-merge.md)
2. [Konfiguracja zabezpieczeń Split-Merge](elastic-scale-split-merge-security-configuration.md)
3. [Zagadnienia dotyczące zabezpieczeń z podziałem na scalanie](elastic-scale-split-merge-security-configuration.md)
4. [Zarządzanie mapami fragmentów](elastic-scale-shard-map-management.md)
5. [Migrowanie istniejących baz danych w celu skalowania w poziomie](elastic-convert-to-use-elastic-tools.md)
6. [Narzędzia Elastic Database](elastic-scale-introduction.md)
7. [Słownik narzędzi elastycznych baz danych](elastic-scale-glossary.md)

## <a name="why-use-the-split-merge-tool"></a>Dlaczego warto używać narzędzia do dzielenia i scalania

- **Zasady**

  Aplikacje muszą rozciągnąć elastycznie poza limity pojedynczej bazy danych w Azure SQL Database. Użyj narzędzia, aby przenieść dane zgodnie z wymaganiami do nowych baz danych, zachowując integralność.

- **Podziel do wzrostu**

  Aby zwiększyć ogólną pojemność do obsłużenia wzrostu wybuchu, należy utworzyć dodatkową pojemność przez fragmentowania danych i rozpowszechnić je w przyrostowo większej liczbie baz danych, dopóki nie zostaną spełnione potrzeby pojemności. Jest to przykładowa funkcja **Split** .

- **Scal, aby zmniejszyć**

  Pojemność wymaga zmniejszenia ze względu na sezonowy charakter firmy. Narzędzie umożliwia skalowanie w dół do mniejszej liczby jednostek skalowania, gdy firma jest niska. Funkcja "merge" w usłudze elastycznego skalowania Split-Merge obejmuje to wymaganie.

- **Zarządzaj hotspotami, przenosząc podfragmentów**

  W przypadku wielu dzierżawców na bazę danych alokacja podfragmentów do fragmentów może prowadzić do wąskich gardeł wydajności w przypadku niektórych fragmentów. Wymaga to ponownej alokacji podfragmentów lub przeniesieniu podfragmentów zajętego na nowe lub mniej wykorzystane fragmentów.

## <a name="concepts--key-features"></a>Pojęcia dotyczące kluczowych funkcji &

- **Usługi hostowane przez klienta**

  Funkcja Split-Scale jest dostarczana jako usługa hostowana przez klienta. Musisz wdrożyć usługę i hostować ją w ramach subskrypcji Microsoft Azure. Pakiet pobrany z narzędzia NuGet zawiera szablon konfiguracji, który można wykonać przy użyciu informacji dotyczących określonego wdrożenia. Aby uzyskać szczegółowe informacje, zobacz [samouczek Split-Merge](elastic-scale-configure-deploy-split-and-merge.md) . Ponieważ usługa jest uruchomiona w ramach subskrypcji platformy Azure, można kontrolować i konfigurować większość aspektów zabezpieczeń usługi. Szablon domyślny zawiera opcje konfigurowania protokołu TLS, uwierzytelniania klientów opartych na certyfikatach, szyfrowania przechowywanych poświadczeń, ochrony systemu DoS i ograniczeń adresów IP. Więcej informacji na temat aspektów zabezpieczeń można znaleźć w poniższej [konfiguracji zabezpieczeń Split-Merge](elastic-scale-split-merge-security-configuration.md).

  Domyślna wdrożona usługa działa z jednym procesem roboczym i jedną rolą sieci Web. Każdy z nich używa rozmiaru maszyny wirtualnej a1 w usłudze Azure Cloud Services. Podczas wdrażania pakietu nie można modyfikować tych ustawień, ale można je zmienić po pomyślnym wdrożeniu w uruchomionej usłudze w chmurze (za pomocą Azure Portal). Należy pamiętać, że roli proces roboczy nie można skonfigurować dla więcej niż jednego wystąpienia ze względów technicznych.

- **Integracja mapy fragmentu**

  Usługa Split-Merge współdziała z mapą fragmentu aplikacji. W przypadku korzystania z usługi Split-Merge do dzielenia lub scalania zakresów lub przenoszenia podfragmentów między fragmentów usługa automatycznie utrzymuje aktualną mapę fragmentu. W tym celu usługa nawiązuje połączenie z bazą danych Menedżera mapy fragmentu aplikacji i utrzymuje zakresy i mapowania jako postęp operacji dzielenia/scalania/przenoszenia. Dzięki temu Mapa fragmentu zawsze przedstawia aktualny widok, gdy trwa operacja dzielenia i scalania. Operacje dzielenia, scalania i podfragmentu przenoszenia są implementowane przez przeniesienie partii podfragmentów z fragmentu źródłowego do docelowego fragmentu. Podczas operacji przenoszenia podfragmentu element podfragmentów, który podlega bieżącej partii, jest oznaczany jako offline w mapie fragmentu i nie jest dostępny dla połączeń routingu zależnych od danych przy użyciu interfejsu API **OpenConnectionForKey** .

- **Spójne połączenia podfragmentu**

  Po rozpoczęciu przenoszenia danych dla nowej partii podfragmentów wszystkie powiązane z danymi połączenia routingu opartego na danych do fragmentu przechowywania podfragmentu są zabijane, a kolejne połączenia z interfejsów API mapy fragmentu do podfragmentów są blokowane, gdy trwa przenoszenie danych w celu uniknięcia niespójności. Połączenia z innymi podfragmentówami w tym samym fragmentu również zostaną zabite, ale pozostaną natychmiast ponownie po ponowieniu próby. Po przeniesieniu partii podfragmentów są ponownie oznaczane online dla docelowego fragmentu, a dane źródłowe zostaną usunięte z fragmentu źródłowego. Usługa przechodzi przez te kroki dla każdej partii, dopóki nie zostaną przesunięte wszystkie podfragmentów. Spowoduje to wykonanie kilku operacji kasowania połączenia w trakcie trwania operacji dzielenia/scalania/przenoszenia.  

- **Zarządzanie dostępnością podfragmentu**

  Ograniczenie odstrzału połączenia do bieżącej partii podfragmentów, jak wspomniano powyżej, ogranicza zakres niedostępności do jednej partii podfragmentów w danym momencie. Ta metoda jest preferowana w przypadku, gdy kompletny fragmentu pozostanie w trybie offline dla wszystkich podfragmentów w trakcie operacji dzielenia lub scalania. Rozmiar partii, zdefiniowany jako liczba odrębnych podfragmentów do przeniesienia w danym momencie, jest parametrem konfiguracji. Można ją zdefiniować dla każdej operacji dzielenia i scalania, w zależności od wymagań dotyczących dostępności i wydajności aplikacji. Należy zauważyć, że zakres, który jest zablokowany w mapie fragmentu, może być większy niż określony rozmiar partii. Wynika to z faktu, że usługa wybiera rozmiar zakresu, tak że rzeczywista liczba wartości klucza fragmentowania w danych jest w przybliżeniu zgodna z rozmiarem partii. Jest to ważne, aby pamiętać, że szczególnie w przypadku rozrzedzonie wypełnionych kluczy fragmentowania.

- **Magazyn metadanych**

  Usługa Split-Merge używa bazy danych do przechowywania stanu i przechowywania dzienników podczas przetwarzania żądania. Użytkownik tworzy tę bazę danych w swojej subskrypcji i dostarcza dla niej parametry połączenia w pliku konfiguracyjnym wdrożenia usługi. Administratorzy z organizacji użytkownika mogą także połączyć się z tą bazą danych, aby sprawdzić postęp żądania i zbadać szczegółowe informacje dotyczące potencjalnych awarii.

- **Fragmentowania — świadomość**

  Usługa Split-Merge rozróżnia (1) tabele podzielonej na fragmenty, (2) tabele odwołań i (3) normalne tabele. Semantyka operacji Split/Merge/Move zależy od typu używanej tabeli i są zdefiniowane w następujący sposób:

  - **Tabele podzielonej na fragmenty**

    Operacje dzielenia, scalania i przenoszenia umożliwiają przeniesienie podfragmentów z lokalizacji źródłowej na fragmentu. Po pomyślnym zakończeniu żądania całkowitego te podfragmentów nie są już obecne w źródle. Zwróć uwagę, że tabele docelowe muszą istnieć w docelowym fragmentu i nie mogą zawierać danych w zakresie docelowym przed przetworzeniem operacji.

  - **Tabele odwołań**

    W przypadku tabel referencyjnych operacje dzielenia, scalania i przenoszenia kopiują dane ze źródła do docelowego fragmentu. Należy jednak pamiętać, że żadne zmiany nie nastąpiły w docelowym fragmentu dla danej tabeli, jeśli którykolwiek wiersz jest już obecny w tej tabeli w miejscu docelowym. Tabela musi być pusta dla każdej operacji kopiowania tabeli odwołań do przetworzenia.

  - **Inne tabele**

    Inne tabele mogą być obecne na źródłowym lub docelowym operacji dzielenia i scalania. Usługa Split-Merge nie uwzględnia tych tabel w przypadku operacji przenoszenia lub kopiowania danych. Należy jednak zauważyć, że mogą one zakłócać te operacje w przypadku ograniczeń.

    Informacje dotyczące tabel Reference i podzielonej na fragmenty są udostępniane przez `SchemaInfo` interfejsy API na mapie fragmentu. Poniższy przykład ilustruje użycie tych interfejsów API w danym obiekcie Menedżera mapy fragmentu:

    ```csharp
    // Create the schema annotations
    SchemaInfo schemaInfo = new SchemaInfo();

    // reference tables
    schemaInfo.Add(new ReferenceTableInfo("dbo", "region"));
    schemaInfo.Add(new ReferenceTableInfo("dbo", "nation"));

    // sharded tables
    schemaInfo.Add(new ShardedTableInfo("dbo", "customer", "C_CUSTKEY"));
    schemaInfo.Add(new ShardedTableInfo("dbo", "orders", "O_CUSTKEY"));

    // publish
    smm.GetSchemaInfoCollection().Add(Configuration.ShardMapName, schemaInfo);
    ```

    Tabele "region" i "kraj" są zdefiniowane jako tabele referencyjne i zostaną skopiowane za pomocą operacji Split/Merge/Move. z kolei "Customer" i "Orders" są zdefiniowane jako tabele podzielonej na fragmenty. `C_CUSTKEY` i `O_CUSTKEY` stanowią klucz fragmentowania.

- **Integralność referencyjna**

  Usługa Split-Merge analizuje zależności między tabelami i używa relacji klucza obcego klucza głównego w celu przygotowania operacji do przeniesienia tabel odwołań i podfragmentów. Ogólnie rzecz biorąc, tabele odwołań są kopiowane najpierw w kolejności zależności, a następnie podfragmentów są kopiowane w kolejności ich zależności w ramach każdej partii. Jest to konieczne, aby ograniczenia klucza FK-PK na fragmentu docelowym były uznawane za nowe dane.

- **Fragmentu spójność mapy i ostateczne uzupełnianie**

  W przypadku awarii usługa Split-Merge wznawia operacje po wystąpieniu awarii i ma na celu ukończenie wszelkich żądań w toku. Jednak mogą istnieć nieodwracalne sytuacje, na przykład gdy docelowy fragmentu zostanie utracony lub naruszony po naprawieniu. W tych okolicznościach niektóre podfragmentów, które miały zostać przeniesione, mogą nadal znajdować się na źródłowym fragmentu. Usługa zapewnia, że mapowania podfragmentu są aktualizowane dopiero po pomyślnym skopiowaniu wymaganych danych do obiektu docelowego. Podfragmentów są usuwane tylko w źródle, gdy wszystkie jego dane zostały skopiowane do obiektu docelowego, a odpowiednie mapowania zostały pomyślnie zaktualizowane. Operacja usuwania odbywa się w tle, podczas gdy zakres jest już w trybie online w docelowym fragmentu. Usługa Split-Merge zawsze gwarantuje prawidłowość mapowań przechowywanych na mapie fragmentu.

## <a name="the-split-merge-user-interface"></a>Interfejs użytkownika z podziałem i scalaniem

Pakiet usługi Split-Merge obejmuje rolę procesu roboczego i rolę sieci Web. Rola sieci Web służy do przesyłania żądań Split-Merge w sposób interaktywny. Główne składniki interfejsu użytkownika są następujące:

- **Typ operacji**

  Typ operacji to przycisk radiowy, który steruje rodzajem operacji wykonywanej przez usługę dla tego żądania. Możesz wybrać między scenariuszami Split, Merge i Move. Możesz również anulować wcześniej przesłaną operację. Możesz użyć żądań Split, Merge i Move dla zakresu fragmentu Maps. Lista mapy fragmentu obsługuje tylko operacje przenoszenia.

- **Mapa fragmentu**

  Następna sekcja parametrów żądania zawiera informacje na temat mapy fragmentu i bazy danych, która hostuje mapę fragmentu. W szczególności należy podać nazwę serwera i bazy danych hostujący shardmap, poświadczenia, aby połączyć się z bazą danych mapowania fragmentu i na końcu nazwę mapy fragmentu. Obecnie operacja akceptuje tylko jeden zestaw poświadczeń. Te poświadczenia muszą mieć wystarczające uprawnienia do wykonywania zmian mapy fragmentu oraz danych użytkownika w fragmentów.

- **Zakres źródłowy (dzielenie i scalanie)**

  Operacja dzielenia i scalania przetwarza zakres przy użyciu jego klucza niskiego i wysokiego. Aby określić operację z niepowiązaną wartością klucza wyższego poziomu, zaznacz pole wyboru "wysoki klucz jest Max" i pozostaw pole wysokiego klucza puste. Określone wartości klucza zakresu nie wymagają dokładnego dopasowania mapowania i jego granic na mapie fragmentu. Jeśli nie określisz żadnych granic zakresu w całej usłudze, będzie ona automatycznie wywnioskować najbliższy zakres. Za pomocą skryptu GetMappings.ps1 PowerShell można pobrać bieżące mapowania w danej mapie fragmentu.

- **Zachowanie podziału źródła (podzielone)**

  Dla operacji podziału Zdefiniuj punkt, w którym ma zostać podzielony zakres źródłowy. W tym celu należy podać klucz fragmentowania, w którym ma nastąpić podział. Użyj przycisku radiowego Określ, czy chcesz, aby dolna część zakresu (z wyłączeniem klucza podziału) była przenoszona, czy chcesz, aby Górna część była przenoszona (łącznie z kluczem podziału).

- **Podfragmentu źródłowa (Przenieś)**

  Operacje przenoszenia różnią się od operacji dzielenia lub scalania, ponieważ nie wymagają one zakresu do opisywania źródła. Źródło do przeniesienia jest po prostu identyfikowane przez wartość klucza fragmentowania, która ma zostać przeniesiona.

- **Docelowa fragmentu (Split)**

  Po podaniu informacji w źródle operacji podziału należy określić, gdzie mają być kopiowane dane, podając nazwę serwera i bazy danych dla celu.

- **Zakres docelowy (Scalanie)**

  Operacje scalania Przenieś podfragmentów do istniejącego fragmentu. Istniejący fragmentu można zidentyfikować, podając granice zakresu istniejącego zakresu, z którym chcesz się połączyć.

- **Rozmiar wsadu**

  Rozmiar wsadu określa liczbę podfragmentów, które będą przełączane do trybu offline w danym momencie podczas przenoszenia danych. Jest to wartość całkowita, w której można używać mniejszych wartości, gdy jest to poufne dla długich okresów przestoju dla podfragmentów. Większe wartości spowodują wydłużenie czasu, w którym dana podfragmentu jest w trybie offline, ale może zwiększyć wydajność.

- **Identyfikator operacji (Anuluj)**

  Jeśli masz trwającą operację, która nie jest już wymagana, możesz anulować operację, podając jej Identyfikator operacji w tym polu. Identyfikator operacji można pobrać z tabeli stan żądania (patrz sekcja 8,1) lub z danych wyjściowych w przeglądarce sieci Web, w której zostało przesłane żądanie.

## <a name="requirements-and-limitations"></a>Wymagania i ograniczenia

Bieżącą implementację usługi Split-Merge podlegają następujące wymagania i ograniczenia:

- Fragmentów musi istnieć i być zarejestrowany na mapie fragmentu przed wykonaniem operacji Split-Scale na tych fragmentów.
- Usługa nie tworzy tabel ani żadnych innych obiektów bazy danych automatycznie w ramach operacji. Oznacza to, że schemat dla wszystkich tabel podzielonej na fragmenty i tabel referencyjnych musi istnieć w docelowym fragmentu przed operacją Split/Merge/Move. Tabele podzielonej na fragmenty w szczególności muszą być puste w zakresie, w którym nowe podfragmentów mają być dodawane przez operację Split/Merge/Move. W przeciwnym razie operacja zakończy się niepowodzeniem po wstępnym sprawdzeniu spójności w docelowym fragmentu. Należy również zauważyć, że dane referencyjne są kopiowane tylko wtedy, gdy tabela referencyjna jest pusta i nie ma gwarancji spójności w odniesieniu do innych współbieżnych operacji zapisu w tabelach referencyjnych. Zalecamy to: podczas wykonywania operacji dzielenia/scalania nie są wprowadzane żadne inne operacje zapisu w tabelach referencyjnych.
- Usługa korzysta z tożsamości wiersza ustanowionej przy użyciu unikatowego indeksu lub klucza, który zawiera klucz fragmentowania, aby zwiększyć wydajność i niezawodność dla dużych podfragmentówów. Dzięki temu usługa może przenosić dane z jeszcze większą szczegółowością niż wartość klucza fragmentowania. Pozwala to zmniejszyć maksymalną ilość miejsca w dzienniku i blokad, które są wymagane podczas operacji. Rozważ utworzenie unikatowego indeksu lub klucza podstawowego, w tym klucza fragmentowania w danej tabeli, jeśli chcesz użyć tej tabeli z żądaniami Split/Merge/Move. Ze względu na wydajność klucz fragmentowania powinien być początkową kolumną w kluczu lub indeksem.
- W trakcie przetwarzania żądania niektóre dane podfragmentu mogą być obecne zarówno na źródłowym, jak i docelowym fragmentu. Jest to konieczne do ochrony przed błędami podczas przenoszenia podfragmentu. Integracja z funkcją Split-Merge z mapą fragmentu gwarantuje, że połączenia za pośrednictwem interfejsów API routingu zależnego od danych za pomocą metody **OpenConnectionForKey** na mapie fragmentu nie widzą żadnych niespójnych stanów pośrednich. Jednak podczas nawiązywania połączenia ze źródłem lub docelowym fragmentów bez użycia metody **OpenConnectionForKey** , niespójne Stany pośrednie mogą być widoczne po przejściu na żądania dzielenia/scalania/przenoszenia. Te połączenia mogą pokazać częściowe lub zduplikowane wyniki w zależności od chronometrażu lub fragmentugo połączenia. To ograniczenie obejmuje obecnie połączenia wykonywane przez wiele fragmentu kwerend.
- Baza danych metadanych dla usługi Split-Merge nie może być współdzielona między różnymi rolami. Na przykład rola usługi Split-Merge działającej w ramach przemieszczania musi wskazywać inną bazę danych metadanych niż rola produkcyjna.

## <a name="billing"></a>Rozliczenia

Usługa Split-Merge działa jako usługa w chmurze w ramach subskrypcji Microsoft Azure. W związku z tym opłaty za usługi w chmurze mają zastosowanie do Twojego wystąpienia usługi. Jeśli operacje dzielenia/scalania/przenoszenia nie są często wykonywane, zalecamy usunięcie usługi w chmurze z podziałem. Pozwala to zaoszczędzić koszty uruchamiania lub wdrażania wystąpień usługi w chmurze. Możesz ponownie wdrożyć i rozpocząć swoją możliwy do uruchomienia konfigurację, ilekroć trzeba wykonać operacje dzielenia lub scalania.

## <a name="monitoring"></a>Monitorowanie

### <a name="status-tables"></a>Tabele stanu

Usługa Split-Merge oferuje tabelę **stanem żądania** w bazie danych magazynu metadanych na potrzeby monitorowania ukończonych i bieżących żądań. W tabeli przedstawiono wiersz dla każdego żądania Split-Merge, które zostało przesłane do tego wystąpienia usługi Split-Merge. Podaje następujące informacje dotyczące każdego żądania:

- **Timestamp**

  Godzina i Data rozpoczęcia żądania.

- **OperationId**

  Identyfikator GUID, który jednoznacznie identyfikuje żądanie. Tego żądania można także użyć do anulowania operacji, gdy nadal trwa.

- **Stan**

  Bieżący stan żądania. W przypadku bieżących żądań jest również wyświetlana bieżąca faza, w której jest to żądanie.

- **CancelRequest**

  Flaga wskazująca, czy żądanie zostało anulowane.

- **Postęp**

  Procentowe oszacowanie ukończenia operacji. Wartość 50 wskazuje, że operacja jest około 50%.

- **Szczegóły**

  Wartość XML, która zapewnia bardziej szczegółowy raport postępu. Raport postępu jest okresowo aktualizowany, ponieważ zestawy wierszy są kopiowane z lokalizacji źródłowej do docelowej. W przypadku awarii lub wyjątków ta kolumna zawiera również bardziej szczegółowe informacje o błędzie.

### <a name="azure-diagnostics"></a>Diagnostyka Azure

Usługa Split-Merge używa Diagnostyka Azure w oparciu o zestaw Azure SDK 2,5 na potrzeby monitorowania i diagnostyki. Możesz kontrolować konfigurację diagnostyki, jak wyjaśniono tutaj: [Włączanie diagnostyki na platformie Azure Cloud Services i Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md). Pakiet pobierania obejmuje dwie konfiguracje diagnostyczne — jedną dla roli sieci Web i jedną dla roli proces roboczy. Zawiera definicje służące do rejestrowania liczników wydajności, dzienników usług IIS, dzienników zdarzeń systemu Windows i dzienników zdarzeń aplikacji dzielących scalanie.

## <a name="deploy-diagnostics"></a>Wdróż diagnostykę

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

> [!IMPORTANT]
> Moduł Azure Resource Manager programu PowerShell jest nadal obsługiwany, ale wszystkie przyszłe Programowanie dla modułu AZ. SQL. W przypadku tych poleceń cmdlet zobacz [AzureRM. SQL](https://docs.microsoft.com/powershell/module/AzureRM.Sql/). Argumenty poleceń polecenia AZ module i w modułach AzureRm są zasadniczo identyczne.

Aby włączyć monitorowanie i diagnostykę przy użyciu konfiguracji diagnostyki dla ról sieci Web i procesów roboczych dostarczonych przez pakiet NuGet, uruchom następujące polecenia przy użyciu Azure PowerShell:

```powershell
$storageName = "<azureStorageAccount>"
$key = "<azureStorageAccountKey"
$storageContext = New-AzStorageContext -StorageAccountName $storageName -StorageAccountKey $key
$configPath = "<filePath>\SplitMergeWebContent.diagnostics.xml"
$serviceName = "<cloudServiceName>"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWeb"

Set-AzureServiceDiagnosticsExtension -StorageContext $storageContext `
    -DiagnosticsConfigurationPath $configPath -ServiceName $serviceName `
    -Slot Production -Role "SplitMergeWorker"
```

Więcej informacji na temat sposobu konfigurowania i wdrażania ustawień diagnostycznych można znaleźć tutaj: [Włączanie diagnostyki na platformie Azure Cloud Services i Virtual Machines](../../cloud-services/cloud-services-dotnet-diagnostics.md).

## <a name="retrieve-diagnostics"></a>Pobierz diagnostykę

Możesz łatwo uzyskać dostęp do danych diagnostycznych z programu Visual Studio Eksplorator serwera w części Azure drzewa Eksplorator serwera. Otwórz wystąpienie programu Visual Studio, a następnie na pasku menu kliknij pozycję Widok, a Eksplorator serwera. Kliknij ikonę platformy Azure, aby nawiązać połączenie z subskrypcją platformy Azure. Następnie przejdź do usługi Azure-> Storage-> `<your storage account>` -> tabele — > WADLogsTable. Aby uzyskać więcej informacji, zobacz [Eksplorator serwera](https://msdn.microsoft.com/library/x603htbk.aspx).

![WADLogsTable][2]

WADLogsTable wyróżniony na powyższym rysunku zawiera szczegółowe zdarzenia z dziennika aplikacji usługi Split-Merge. Należy pamiętać, że domyślna konfiguracja pobranego pakietu jest ukierunkowana na wdrożenie produkcyjne. W związku z tym Interwał pobierania dzienników i liczników z wystąpień usługi jest duży (5 minut). W celu testowania i opracowywania Obniż interwał przez dostosowanie ustawień diagnostycznych sieci Web lub roli procesu roboczego do Twoich potrzeb. Kliknij prawym przyciskiem myszy rolę w Eksplorator serwera programu Visual Studio (patrz powyżej), a następnie dostosuj okres transferu w oknie dialogowym dla ustawień konfiguracji diagnostyki:

![Konfiguracja][3]

## <a name="performance"></a>Wydajność

Ogólnie rzecz biorąc, lepsza wydajność jest oczekiwana w przypadku wyższych, bardziej wydajnych warstw usług. Wyższe ilości operacji we/wy, procesora CPU i pamięci dla wyższych warstw usług korzystają z operacji kopiowania zbiorczego i usuwania, których używa Usługa Split-Merge. Z tego powodu należy zwiększyć warstwę usługi tylko dla tych baz danych przez zdefiniowany, ograniczony okres.

Usługa wykonuje również zapytania weryfikacyjne w ramach zwykłych operacji. Te zapytania weryfikacyjne sprawdzają nieoczekiwaną obecność danych w zakresie docelowym i zapewniają, że wszystkie operacje dzielenia/scalania/przenoszenia zaczynają się od spójnego stanu. Wszystkie te zapytania przekraczają zakresy fragmentowania klucze zdefiniowane przez zakres operacji oraz rozmiar wsadu podany w ramach definicji żądania. Zapytania te działają najlepiej, gdy istnieje indeks, który ma klucz fragmentowania jako początkową kolumnę.

Ponadto Właściwość unikatowości z kluczem fragmentowania jako wiodąca kolumna umożliwi usłudze użycie zoptymalizowanego podejścia, które ogranicza użycie zasobów w odniesieniu do przestrzeni dziennika i pamięci. Ta właściwość unikatowości jest wymagana do przenoszenia dużych rozmiarów danych (zwykle przekracza 1 GB).

## <a name="how-to-upgrade"></a>Jak uaktualnić

1. Wykonaj kroki opisane w temacie [wdrażanie usługi Split-Merge](elastic-scale-configure-deploy-split-and-merge.md).
2. Zmień plik konfiguracji usługi w chmurze dla wdrożenia Split-Merge, aby odzwierciedlał nowe parametry konfiguracji. Nowy wymagany parametr to informacje o certyfikacie używanym do szyfrowania. W łatwy sposób można porównać nowy plik szablonu konfiguracji z pobrania z istniejącą konfiguracją. Upewnij się, że dodano ustawienia dla elementu "DataEncryptionPrimaryCertificateThumbprint" i "DataEncryptionPrimary" dla roli sieć Web i proces roboczy.
3. Przed wdrożeniem aktualizacji na platformie Azure upewnij się, że wszystkie aktualnie uruchomione operacje dzielenia i scalania zostały zakończone. Można to łatwo zrobić, wykonując zapytania dotyczące tabel stanem żądania i PendingWorkflows w bazie danych metadanych Split-Merge dla bieżących żądań.
4. Zaktualizuj istniejące wdrożenie usługi w chmurze na potrzeby dzielenia i scalania w ramach subskrypcji platformy Azure z nowym pakietem i zaktualizowanym plikiem konfiguracji usługi.

Nie trzeba udostępniać nowej bazy danych metadanych na potrzeby operacji Split-Merge do uaktualnienia. Nowa wersja automatycznie uaktualni istniejącą bazę danych metadanych do nowej wersji.

## <a name="best-practices--troubleshooting"></a>Najlepsze rozwiązania i rozwiązywanie problemów

- Zdefiniuj dzierżawę testową i realizuj najważniejsze operacje dzielenia/scalania/przenoszenia przy użyciu dzierżawy testowej w kilku fragmentów. Upewnij się, że wszystkie metadane są poprawnie zdefiniowane na mapie fragmentu i że operacje nie naruszają ograniczeń lub kluczy obcych.
- Zachowaj test rozmiaru danych dzierżawy powyżej maksymalnego rozmiaru danych największej dzierżawy, aby upewnić się, że nie występują problemy związane z rozmiarem danych. Dzięki temu można ocenić górną granicę czasu potrzebnego na przeniesienie pojedynczej dzierżawy.
- Upewnij się, że schemat zezwala na usuwanie. Usługa Split-Merge wymaga możliwości usunięcia danych z fragmentu źródłowego, gdy dane zostaną pomyślnie skopiowane do obiektu docelowego. Na przykład **Usuń wyzwalacze** mogą uniemożliwić usunięcie danych ze źródła przez usługę, co może spowodować niepowodzenie operacji.
- Klucz fragmentowania powinien być początkową kolumną w kluczu podstawowym lub unikatowym definicją indeksu. Zapewnia to najlepszą wydajność dla zapytań dotyczących podzielenia lub scalania, a także dla rzeczywistych operacji przenoszenia i usuwania danych, które zawsze działają względem zakresów kluczy fragmentowania.
- Kolokacja usługi Split-Merge w regionie i centrum danych, w którym znajdują się bazy danych.

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]

<!--Anchors-->
<!--Image references-->
[1]:./media/elastic-scale-overview-split-and-merge/split-merge-overview.png
[2]:./media/elastic-scale-overview-split-and-merge/diagnostics.png
[3]:./media/elastic-scale-overview-split-and-merge/diagnostics-config.png
