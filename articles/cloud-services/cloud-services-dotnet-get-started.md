---
title: Wprowadzenie do usług Azure Cloud Services i programu ASP.NET | Microsoft Docs
description: Dowiedz się, jak utworzyć aplikację wielowarstwową przy użyciu kontrolera ASP.NET MVC i platformy Azure. Aplikacja jest uruchamiana w usłudze w chmurze z rolą Sieć Web i Proces roboczy. Używa platformy Entity Framework, bazy danych SQL Database oraz obiektów blob i kolejek usługi Azure Storage.
services: cloud-services, storage
documentationcenter: .net
author: tgore03
manager: carmonm
ms.service: cloud-services
ms.devlang: dotnet
ms.custom: devx-track-csharp
ms.topic: conceptual
ms.date: 05/15/2017
ms.author: tagore
ms.openlocfilehash: a875c036c79419357f1134c32f62fdb060fec7c6
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/16/2020
ms.locfileid: "97562297"
---
# <a name="get-started-with-azure-cloud-services-and-aspnet"></a>Wprowadzenie do usług Azure Cloud Services i programu ASP.NET

## <a name="overview"></a>Omówienie
W tym samouczku wyjaśniono, jak utworzyć wielowarstwową aplikację .NET z frontonem ASP.NET MVC i wdrożyć ją w [usłudze w chmurze Azure](cloud-services-choose-me.md). Aplikacja używa [bazy danych Azure SQL Database](/previous-versions/azure/ee336279(v=azure.100)), [usługi obiektów blob platformy Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage) i [usługi kolejek platformy Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern). [Projekt programu Visual Studio można pobrać](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) z Galerii kodu MSDN.

W samouczku opisano, jak utworzyć i uruchomić aplikację lokalnie, jak wdrożyć ją na platformie Azure i uruchomić w chmurze oraz jak utworzyć ją od samego początku. Można również rozpocząć od kompilowania aplikacji od początku, a dopiero później przeprowadzić testowanie i wdrażanie.

## <a name="contoso-ads-application"></a>Aplikacja Contoso Ads
Ta aplikacja to reklamowa tablica ogłoszeń. Aby utworzyć reklamę, użytkownicy muszą wpisać tekst i przesłać obraz. Mogą przeglądać listę reklam z miniaturami obrazów, a także zobaczyć pełny obraz po kliknięciu reklamy w celu wyświetlenia jej szczegółów.

![Lista reklam](./media/cloud-services-dotnet-get-started/list.png)

Aplikacja korzysta z [przetwarzania kolejkowego](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/queue-centric-work-pattern), aby przekazać obciążające procesor zadania związane z tworzeniem miniatur do procesu zaplecza.

## <a name="alternative-architecture-app-service-and-webjobs"></a>Architektura alternatywna: App Service i Zadania WebJob
Ten samouczek pokazuje, jak uruchamiać fronton i zaplecze w usłudze w chmurze Azure. Alternatywą jest uruchomienie frontonu w usłudze [Azure App Service](../app-service/index.yml) i używanie funkcji zadań [WebJob](https://go.microsoft.com/fwlink/?LinkId=390226) dla zaplecza. Aby zapoznać się z samouczkiem korzystającym z zadań WebJob, zobacz artykuł [Wprowadzenie do zestawu SDK zadań WebJob na platformie Azure](https://github.com/Azure/azure-webjobs-sdk/wiki). Informacje o wybieraniu usług najlepiej spełniających potrzeby scenariusza zawiera artykuł [Porównanie usług Azure App Service, Cloud Services i Virtual Machines](/azure/architecture/guide/technology-choices/compute-decision-tree).

## <a name="what-youll-learn"></a>Zawartość
* Jak umożliwić tworzenie aplikacji platformy Azure na komputerze przez zainstalowanie zestawu Azure SDK.
* Jak utworzyć projekt usługi w chmurze programu Visual Studio z rolą Proces roboczy i rolą Sieć Web kontrolera ASP.NET MVC.
* Jak przetestować projekt usługi w chmurze lokalnie za pomocą emulatora usługi Azure Storage.
* Jak opublikować projekt chmury w usłudze w chmurze platformy Azure i przetestować go przy użyciu konta magazynu platformy Azure.
* Jak przekazywać pliki i przechowywać je w usłudze obiektów blob platformy Azure.
* Jak komunikować się między warstwami przy użyciu usługi kolejek platformy Azure.

## <a name="prerequisites"></a>Wymagania wstępne
W samouczku przyjęto założenie, że znasz [podstawowe pojęcia dotyczące usług w chmurze Azure](cloud-services-choose-me.md), takie jak terminologia związana z *rolą Sieć Web* i *rolą Proces roboczy*.  Założono również, że wiesz, jak pracować z projektami [ASP.NET MVC](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started) lub [formularzami sieci Web](https://www.asp.net/web-forms/tutorials/aspnet-45/getting-started-with-aspnet-45-web-forms/introduction-and-overview) w programie Visual Studio. Przykładowa aplikacja korzysta z kontrolera MVC, jednak większość treści samouczka odnosi się również do formularzy sieci Web.

Aplikację można uruchomić lokalnie bez subskrypcji platformy Azure, ale będzie ona potrzebna w celu wdrożenia aplikacji w chmurze. Jeśli nie masz konta, możesz [aktywować korzyści dla subskrybentów MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A55E3C668) lub [utworzyć konto bezpłatnej wersji próbnej](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A55E3C668).

Instrukcje dotyczące samouczka współpracują z dowolnym z następujących produktów:

* Visual Studio 2013
* Visual Studio 2015
* Visual Studio 2017
* Visual Studio 2019

Jeśli nie masz żadnego z tych programów, podczas instalowania zestawu Azure SDK program Visual Studio może zostać zainstalowany automatycznie.

## <a name="application-architecture"></a>Architektura aplikacji
Aplikacja przechowuje reklamy w bazie danych SQL oraz tworzy tabele i uzyskuje dostęp do danych za pomocą funkcji Code First platformy Entity Framework. W przypadku każdej reklamy baza danych zawiera dwa adresy URL: jeden do obrazu w pełnym rozmiarze i jeden do miniatury.

![Tabela reklam](./media/cloud-services-dotnet-get-started/adtable.png)

Gdy użytkownik przesyła obraz, fronton uruchomiony w roli Sieć Web zapisuje obraz w [obiekcie blob na platformie Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/unstructured-blob-storage), a informacje o reklamie — w bazie danych z adresem URL wskazującym na obiekt blob. W tym samym czasie w kolejce platformy Azure jest zapisywany komunikat. Proces zaplecza uruchomiony w roli Proces roboczy okresowo sonduje kolejkę w celu wyszukania nowych komunikatów. Po wyświetleniu nowego komunikatu rola Proces roboczy tworzy miniaturę obrazu i aktualizuje pole adresu URL miniatury dla danej reklamy. Na diagramie poniżej pokazano, jak współpracują ze sobą różne części aplikacji.

![Diagram przedstawiający sposób działania części aplikacji.](./media/cloud-services-dotnet-get-started/apparchitecture.png)

[!INCLUDE [install-sdk](../../includes/install-sdk-2017-2015-2013.md)]

## <a name="download-and-run-the-completed-solution"></a>Pobieranie i uruchamianie gotowego rozwiązania
1. Pobierz i rozpakuj [ukończone rozwiązanie](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4).
2. Uruchom program Visual Studio.
3. Z menu **Plik** wybierz polecenie **Otwórz projekt**, przejdź do lokalizacji pobranego rozwiązania, a następnie otwórz plik rozwiązania.
4. Naciśnij kombinację klawiszy CTRL+SHIFT+B w celu skompilowania rozwiązania.

    Domyślnie program Visual Studio automatycznie przywraca zawartość pakietu NuGet, która nie została uwzględniona w pliku *ZIP*. Jeśli pakiety nie zostaną przywrócone, zainstaluj je ręcznie, przechodząc do okna dialogowego **Zarządzanie pakietami NuGet dla rozwiązania** i klikając przycisk **Przywróć** w prawym górnym rogu ekranu.
5. Sprawdź w **Eksploratorze rozwiązań**, czy projekt **ContosoAdsCloudService** został wybrany jako projekt startowy.
6. Jeśli używasz programu Visual Studio 2015 lub nowszego, zmień parametry połączenia programu SQL Server w pliku *Web.config* projektu ContosoAdsWeb i w pliku *ServiceConfiguration.Local.cscfg* projektu ContosoAdsCloudService. W każdym przypadku zmień ciąg „(localdb)\v11.0” na „(localdb)\MSSQLLocalDB”.
7. Naciśnij klawisze CTRL+F5, aby uruchomić aplikację.

    Po uruchomieniu projektu usługi w chmurze w środowisku lokalnym program Visual Studio automatycznie wywołuje *emulator obliczeń* platformy Azure i *emulator magazynu* platformy Azure. Emulator obliczeń używa zasobów komputera do symulowania środowisk roli Sieć Web i roli Proces roboczy. Emulator magazynu używa bazy danych [SQL Server Express LocalDB](/sql/database-engine/configure-windows/sql-server-2016-express-localdb), aby symulować działanie magazynu w chmurze Azure.

    Podczas pierwszego uruchomienia projektu usługi w chmurze uruchomienie emulatorów może potrwać około minuty. Po zakończeniu uruchamiania emulatora w domyślnej przeglądarce otworzy się strona główna aplikacji.

    ![Architektura aplikacji Contoso Ads](./media/cloud-services-dotnet-get-started/home.png)
8. Kliknij przycisk **Create an Ad** (Utwórz reklamę).
9. Wprowadź dane testowe i wybierz obraz *JPG* do przekazania, a następnie kliknij przycisk **Create** (Utwórz).

    ![Tworzenie strony](./media/cloud-services-dotnet-get-started/create.png)

    Aplikacja przechodzi do strony indeksu, ale nie wyświetla miniatury nowej reklamy, ponieważ przetwarzanie nie zostało jeszcze przeprowadzone.
10. Zaczekaj chwilę, a następnie odśwież stronę indeksu, aby zobaczyć miniaturę.

     ![Strona indeksu](./media/cloud-services-dotnet-get-started/list.png)
11. Kliknij link **Details** (Szczegóły) obok reklamy, aby wyświetlić obraz w pełnym rozmiarze.

     ![Strona szczegółów](./media/cloud-services-dotnet-get-started/details.png)

Aplikacja działała całkowicie na komputerze lokalnym — bez połączenia z chmurą. Emulator magazynu przechowuje dane kolejek i obiektów blob w bazie danych SQL Server Express LocalDB, a aplikacja przechowuje dane reklam w innej bazie danych LocalDB. Platforma Entity Framework Code First automatycznie utworzyła bazę danych reklam, gdy aplikacja internetowa po raz pierwszy próbowała uzyskać do niej dostęp.

W poniższej sekcji skonfigurujesz rozwiązanie do użycia zasobów w chmurze Azure dla kolejek, obiektów blob i bazy danych aplikacji na potrzeby uruchamiania w chmurze. Możesz to zrobić, jeśli chcesz kontynuować uruchamianie lokalnie, ale korzystać z zasobów magazynu i bazy danych w chmurze. Jest to jedynie kwestia ustawienia parametrów połączenia — pokażemy, jak to zrobić.

## <a name="deploy-the-application-to-azure"></a>Wdrożenie aplikacji na platformie Azure
Aby uruchomić aplikację w chmurze, należy wykonać następujące kroki:

* Utworzenie usługi w chmurze platformy Azure.
* Utwórz bazę danych w Azure SQL Database.
* Utworzenie konta magazynu platformy Azure.
* Skonfiguruj rozwiązanie do korzystania z bazy danych, gdy zostanie ona uruchomiona na platformie Azure.
* Skonfigurowanie rozwiązania do używania konta magazynu Azure po uruchomieniu na platformie Azure.
* Wdrożenie projektu do usługi w chmurze platformy Azure.

### <a name="create-an-azure-cloud-service"></a>Tworzenie usługi w chmurze platformy Azure
Usługa w chmurze Azure to środowisko uruchamiania aplikacji.

1. W przeglądarce otwórz witrynę [Azure Portal](https://portal.azure.com).
2. Kliknij pozycję **Utwórz zasób > Compute > Usługa w chmurze**.

3. W polu wejściowym nazwy DNS wprowadź prefiks adresu URL dla usługi w chmurze.

    Adres URL musi być unikatowy.  Jeśli wybrany prefiks jest już używany, zostanie wyświetlony komunikat o błędzie.
4. Określ nową grupę zasobów dla usługi. Kliknij pozycję **Utwórz nowy**, a następnie w polu wejściowym Grupa zasobów wpisz nazwę, na przykład CS_contososadsRG.

5. Wybierz region, w którym chcesz wdrożyć aplikację.

    To pole określa centrum danych, w którym będzie hostowana usługa w chmurze. W przypadku aplikacji produkcyjnej warto wybrać region znajdujący się najbliżej klientów. Na potrzeby tego samouczka wybierz region znajdujący się najbliżej Ciebie.
5. Kliknij pozycję **Utwórz**.

    Na poniższej ilustracji usługa w chmurze jest tworzona przy użyciu adresu URL CSvccontosoads.cloudapp.net.

    ![Nowa usługa w chmurze](./media/cloud-services-dotnet-get-started/newcs.png)

### <a name="create-a-database-in-azure-sql-database"></a>Tworzenie bazy danych w Azure SQL Database
Aplikacja uruchomiona w chmurze będzie używać bazy danych opartej na chmurze.

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Utwórz zasób > Bazy danych > SQL Database**.
2. W polu **Nazwa bazy danych** wprowadź wartość *contosoads*.
3. W obszarze **Grupa zasobów** kliknij opcję **Użyj istniejącej** i wybierz grupę zasobów używaną dla usługi w chmurze.
4. Na poniższej ilustracji kliknij pozycje **Serwer — Skonfiguruj wymagane ustawienia** i **Utwórz nowy serwer**.

    ![Tunelowanie do serwera bazy danych](./media/cloud-services-dotnet-get-started/newdb.png)

    Alternatywnie — jeśli subskrypcja obejmuje już serwer — możesz wybrać ten serwer z listy rozwijanej.
5. W polu **Nazwa serwera** wpisz ciąg *csvccontosodbserver*.

6. Wprowadź wartość **Nazwa logowania** i **Hasło** dla administratora.

    W przypadku wybrania opcji **Utwórz nowy serwer** nie wprowadzasz w tym miejscu istniejącej nazwy i hasła. Podajesz nową nazwę i hasło, które definiujesz teraz do użycia w przyszłości podczas uzyskiwania dostępu do bazy danych. W przypadku wybrania wcześniej utworzonego serwera zostanie wyświetlony monit o hasło do już utworzonego konta użytkownika administracyjnego.
7. Wybierz taką samą **lokalizację** jak w usłudze w chmurze.

    Jeśli usługa w chmurze i baza danych są obsługiwane w różnych centrach danych (różnych regionach), zwiększy się opóźnienie i będą naliczane opłaty dotyczące przepustowości poza centrum danych. Przepustowość w centrum danych jest bezpłatna.
8. Zaznacz opcję **Zezwalaj usługom platformy Azure na dostęp do serwera**.
9. Kliknij przycisk **Wybierz** dla nowego serwera.

    ![Nowy serwer](./media/cloud-services-dotnet-get-started/newdbserver.png)
10. Kliknij pozycję **Utwórz**.

### <a name="create-an-azure-storage-account"></a>Tworzenie konta usługi Azure Storage
Konto magazynu platformy Azure udostępnia zasoby służące do przechowywania danych kolejek i obiektów blob w chmurze.

W rzeczywistych aplikacjach przeważnie tworzy się oddzielne konta dla danych aplikacji porównywanych z danymi rejestrowania oraz oddzielne konta dla danych testowych porównywanych z danymi produkcyjnymi. W tym samouczku będzie używane tylko jedno konto.

1. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Utwórz zasób > Magazyn > Konto magazynu — obiekt blob, plik, tabela, kolejka**.
2. W polu **Nazwa** wpisz prefiks adresu URL.

    Ten prefiks i tekst wyświetlony w polu będą stanowić unikatowy adres URL konta magazynu. Jeśli wprowadzony prefiks został już użyty przez innego użytkownika, musisz wybrać inny.
3. W obszarze **Model wdrażania** wybierz opcję *Klasyczny*.

4. Z listy rozwijanej **Replikacja** wybierz wartość **Magazyn lokalnie nadmiarowy**.

    Jeśli na koncie magazynu włączono replikację geograficzną, przechowywana zawartość jest replikowana do pomocniczego centrum danych. Pozwala to na przejście do trybu failover w przypadku wystąpienia poważnej awarii w lokalizacji głównej. Replikacja geograficzna może pociągnąć za sobą dodatkowe koszty. W przypadku kont testowych i projektowych przeważnie nie chcesz płacić za replikację geograficzną. Aby uzyskać więcej informacji, zobacz temat dotyczący [tworzenia i usuwania konta magazynu oraz zarządzania nim](../storage/common/storage-account-create.md).

5. W obszarze **Grupa zasobów** kliknij opcję **Użyj istniejącej** i wybierz grupę zasobów używaną dla usługi w chmurze.
6. Z listy rozwijanej **Lokalizacja** wybierz region wybrany wcześniej dla usługi w chmurze.

    Jeśli usługa w chmurze i konto magazynu są obsługiwane w różnych centrach danych (różnych regionach), zwiększy się opóźnienie i będą naliczane opłaty dotyczące przepustowości poza centrum danych. Przepustowość w centrum danych jest bezpłatna.

    Grupy koligacji Azure udostępniają mechanizm umożliwiający minimalizowanie odległości między zasobami w centrum danych, a przez to redukowanie opóźnienia. Ten samouczek nie korzysta z grup koligacji. Aby uzyskać więcej informacji, zobacz temat [Jak utworzyć grupę koligacji w Azure](/previous-versions/azure/reference/gg715317(v=azure.100)).
7. Kliknij pozycję **Utwórz**.

    ![Nowe konto usługi Storage](./media/cloud-services-dotnet-get-started/newstorage.png)

    Na ilustracji przedstawiono utworzone konto magazynu z adresem URL `csvccontosoads.core.windows.net`.

### <a name="configure-the-solution-to-use-your-database-in-azure-sql-database-when-it-runs-in-azure"></a>Skonfiguruj rozwiązanie do korzystania z bazy danych w Azure SQL Database, gdy zostanie ona uruchomiona na platformie Azure

Każdy projekt sieci Web i projekt roli proces roboczy ma własne parametry połączenia z bazą danych, a każdy z nich musi wskazać bazę danych w Azure SQL Database, gdy aplikacja działa na platformie Azure.

Dla roli Sieć Web będziesz używać [przekształcenia pliku Web.config](https://www.asp.net/mvc/tutorials/deployment/visual-studio-web-deployment/web-config-transformations), a dla roli Proces roboczy — ustawienia środowiska usługi w chmurze.

> [!NOTE]
> W tej i kolejnej sekcji poświadczenia są przechowywane w plikach projektu. [Nie należy przechowywać poufnych danych w publicznych repozytoriach kodów źródłowych](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/source-control#secrets).
>
>

1. W projekcie ContosoAdsWeb otwórz plik przekształcenia *Web.Release.config* dla pliku *Web.config* aplikacji, usuń blok komentarza zawierający element `<connectionStrings>` i wklej poniższy kod w jego miejscu.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="{connectionstring}"
        providerName="System.Data.SqlClient" xdt:Transform="SetAttributes" xdt:Locator="Match(name)"/>
    </connectionStrings>
    ```

    Zostaw plik otwarty do edycji.
2. W witrynie [Azure Portal](https://portal.azure.com) kliknij pozycję **Bazy danych SQL Database** w lewym okienku, kliknij bazę danych utworzoną w ramach tego samouczka, a następnie kliknij pozycję **Pokaż parametry połączeń**.

    ![Pokazywanie parametrów połączeń](./media/cloud-services-dotnet-get-started/showcs.png)

    Portal zawiera parametry połączeń oraz symbol zastępczy w miejscu hasła.

    ![Parametry połączeń](./media/cloud-services-dotnet-get-started/connstrings.png)
3. W pliku przekształcenia *Web.Release.config* usuń element `{connectionstring}`, a w jego miejscu wklej parametry połączenia ADO.NET z witryny Azure Portal.
4. W parametrach połączenia wklejonych w pliku przekształcenia *Web.Release.config* zastąp element `{your_password_here}` hasłem utworzonym dla nowej bazy danych SQL.
5. Zapisz plik.  
6. Wybierz i skopiuj parametry połączenia (bez otaczających je znaków cudzysłowu) do użycia w kolejnych krokach konfigurowania projektu roli Proces roboczy.
7. W **Eksploratorze rozwiązań** w obszarze **Role** w projekcie usługi w chmurze kliknij prawym przyciskiem myszy pozycję **ContosoAdsWorker**, a następnie kliknij polecenie **Właściwości**.

    ![Zrzut ekranu, który podświetla opcję menu właściwości.](./media/cloud-services-dotnet-get-started/rolepropertiesworker.png)
8. Kliknij kartę **Ustawienia**.
9. Zmień ustawienie **Konfiguracja usługi** na wartość **Chmura**.
10. Wybierz pole **Wartość** dla ustawienia `ContosoAdsDbConnectionString`, a następnie wklej parametry połączenia skopiowane z poprzedniej sekcji samouczka.

     ![Parametry połączenia bazy danych dla roli Proces roboczy](./media/cloud-services-dotnet-get-started/workerdbcs.png)
11. Zapisz zmiany.  

### <a name="configure-the-solution-to-use-your-azure-storage-account-when-it-runs-in-azure"></a>Konfigurowanie rozwiązania do używania konta magazynu Azure po uruchomieniu na platformie Azure
Parametry połączenia konta magazynu platformy Azure dla projektu roli Sieć Web i projektu roli Proces roboczy są przechowywane w ustawieniach środowiska w projekcie usługi w chmurze. Dla każdego projektu istnieje osobny zestaw ustawień, które będą używane po uruchomieniu aplikacji lokalnie lub w chmurze. Ustawienia środowiska chmury będą aktualizowane dla obu projektów: roli Sieć Web i roli Proces roboczy.

1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy pozycję **ContosoAdsWeb** w obszarze **Role** w projekcie **ContosoAdsCloudService**, a następnie kliknij polecenie **Właściwości**.

    ![Właściwości roli](./media/cloud-services-dotnet-get-started/roleproperties.png)
2. Kliknij kartę **Ustawienia** . W polu listy rozwijanej **Konfiguracja usługi** wybierz pozycję **chmura**.

    ![Konfiguracja chmury](./media/cloud-services-dotnet-get-started/sccloud.png)
3. Wybierz wpis **StorageConnectionString**. Na prawym końcu wiersza pojawi się przycisk z wielokropkiem (**...**). Kliknij przycisk z wielokropkiem, aby otworzyć okno dialogowe **Tworzenie parametrów połączenia konta usługi Storage**.

    ![Otwieranie okna Tworzenie parametrów połączenia](./media/cloud-services-dotnet-get-started/opencscreate.png)
4. W oknie dialogowym **Tworzenie parametrów połączenia** kliknij pozycję **Twoja subskrypcja**, wybierz utworzone wcześniej konto magazynu, a następnie kliknij przycisk **OK**. Jeśli nie zalogowano się, zostanie wyświetlony monit o podanie poświadczeń konta systemu platformy Azure.

    ![Tworzenie parametrów połączenia usługi Storage](./media/cloud-services-dotnet-get-started/createstoragecs.png)
5. Zapisz zmiany.
6. Wykonaj kroki tej samej procedury, która była używana na potrzeby parametrów połączenia `StorageConnectionString`, aby ustawić parametry połączenia `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString`.

    Te parametry są używane do rejestrowania.
7. Wykonaj kroki tej samej procedury, która była używana na potrzeby roli **ContosoAdsWeb**, aby ustawić parametry połączeń dla roli **ContosoAdsWorker**. Pamiętaj o ustawieniu pozycji **Konfiguracja usługi** na wartość **Chmura**.

Ustawienia środowiska roli, które skonfigurowano przy użyciu interfejsu użytkownika programu Visual Studio, są przechowywane w następujących plikach projektu ContosoAdsCloudService:

* *ServiceDefinition.csdef* — definiuje nazwy ustawień.
* *ServiceConfiguration.Cloud.cscfg* — udostępnia wartości w przypadku uruchamiania aplikacji w chmurze.
* *ServiceConfiguration.Local.cscfg* — udostępnia wartości w przypadku uruchamiania aplikacji w środowisku lokalnym.

Na przykład plik ServiceDefinition.csdef zawiera poniższe definicje:

```xml
<ConfigurationSettings>
    <Setting name="StorageConnectionString" />
    <Setting name="ContosoAdsDbConnectionString" />
</ConfigurationSettings>
```

A plik *ServiceConfiguration.Cloud.cscfg* zawiera wartości wprowadzone dla tych ustawień w programie Visual Studio.

```xml
<Role name="ContosoAdsWorker">
    <Instances count="1" />
    <ConfigurationSettings>
        <Setting name="StorageConnectionString" value="{yourconnectionstring}" />
        <Setting name="ContosoAdsDbConnectionString" value="{yourconnectionstring}" />
        <!-- other settings not shown -->

    </ConfigurationSettings>
    <!-- other settings not shown -->

</Role>
```

Ustawienie `<Instances>` określa liczbę maszyn wirtualnych, na których platforma Azure uruchomi kod roli Proces roboczy. W sekcji [Następne kroki](#next-steps) można znaleźć linki do dalszych informacji na temat skalowania usługi w chmurze w poziomie.

### <a name="deploy-the-project-to-azure"></a>Wdrażanie projektu na platformie Azure
1. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy projekt w chmurze **ContosoAdsCloudService**, a następnie wybierz polecenie **Opublikuj**.

   ![Menu Publikuj](./media/cloud-services-dotnet-get-started/pubmenu.png)
2. W kroku **Logowanie** kreatora **Publikowanie aplikacji platformy Azure** kliknij przycisk **Dalej**.

    ![Krok Logowanie](./media/cloud-services-dotnet-get-started/pubsignin.png)
3. W kroku **Ustawienia** kreatora kliknij przycisk **Dalej**.

    ![Krok Ustawienia](./media/cloud-services-dotnet-get-started/pubsettings.png)

    Domyślne ustawienia na karcie **Zaawansowane** są wystarczające w przypadku tego samouczka. Informacje o karcie Zaawansowane można znaleźć w temacie [Kreator publikowania aplikacji platformy Azure](/visualstudio/azure/vs-azure-tools-publish-azure-application-wizard).
4. W kroku **Podsumowanie** kliknij pozycję **Opublikuj**.

    ![Krok Podsumowanie](./media/cloud-services-dotnet-get-started/pubsummary.png)

   W programie Visual Studio zostanie otwarte okno **dziennika aktywności platformy Azure**.
5. Kliknij ikonę strzałki w prawo, aby rozwinąć szczegóły wdrożenia.

    Wdrożenie może potrwać 5 minut lub dłużej.

    ![Okno Dziennik aktywności platformy Azure](./media/cloud-services-dotnet-get-started/waal.png)
6. Gdy stan wdrożenia będzie wskazywać na jego ukończenie, kliknij pozycję **Adres URL aplikacji internetowej** w celu uruchomienia aplikacji.
7. Teraz możesz przetestować aplikację, tworząc, wyświetlając i edytując niektóre reklamy, tak jak w przypadku lokalnego uruchomienia aplikacji.

> [!NOTE]
> Po zakończeniu testowania usuń lub zatrzymaj usługę w chmurze. Nawet jeśli nie używasz usługi w chmurze, opłaty są naliczane, ponieważ zarezerwowano dla niej zasoby maszyny wirtualnej. Jeśli usługa będzie działać, każda osoba, która znajdzie adres URL, będzie mogła tworzyć i wyświetlać reklamy. W witrynie [Azure Portal](https://portal.azure.com) przejdź do karty **Przegląd** dla usługi w chmurze, a następnie kliknij przycisk **Usuń** u góry strony. Jeśli chcesz tylko czasowo uniemożliwić innym użytkownikom dostęp do witryny, kliknij przycisk **Zatrzymaj**. W takim przypadku opłaty będą nadal naliczane. Podobne kroki można wykonać w celu usunięcia konta magazynu i bazy danych SQL, jeśli nie są już potrzebne.
>
>

## <a name="create-the-application-from-scratch"></a>Tworzenie aplikacji od początku
Jeśli [ukończona aplikacja](https://code.msdn.microsoft.com/Simple-Azure-Cloud-Service-e01df2e4) nie została jeszcze pobrana, zrób to teraz. Pliki z pobranego projektu będą kopiowane do nowego projektu.

Tworzenie aplikacji Contoso Ads obejmuje następujące czynności:

* Tworzenie rozwiązania usługi w chmurze w programie Visual Studio.
* Aktualizowanie i dodawanie pakietów NuGet.
* Ustawianie odwołań do projektu.
* Konfigurowanie parametrów połączenia.
* Dodawanie plików kodu.

Po utworzeniu rozwiązania można przejrzeć kod unikatowy dla projektów usług w chmurze oraz kolejki i obiekty blob platformy Azure.

### <a name="create-a-cloud-service-visual-studio-solution"></a>Tworzenie rozwiązania usługi w chmurze w programie Visual Studio
1. W programie Visual Studio wybierz pozycję **Nowy projekt** z menu **Plik**.
2. W lewym okienku okna dialogowego **Nowy projekt** rozwiń węzeł **Visual C#** i wybierz szablony **Chmura**, a następnie wybierz szablon **Usługi w chmurze Azure**.
3. Nazwij projekt i rozwiązanie ContosoAdsCloudService, a następnie kliknij przycisk **OK**.

    ![Nowy projekt](./media/cloud-services-dotnet-get-started/newproject.png)
4. W oknie dialogowym **Nowa usługa w chmurze Azure** dodaj rolę sieci Web i rolę procesu roboczego. Nazwij rolę Sieć Web ContosoAdsWeb i rolę Proces roboczy ContosoAdsWorker. (Aby zmienić domyślne nazwy ról, użyj ikony ołówka w okienku po prawej stronie).

    ![Projekt nowej usługi w chmurze](./media/cloud-services-dotnet-get-started/newcsproj.png)
5. Po wyświetleniu okna dialogowego **Nowy projekt ASP.NET** dla roli Sieć Web wybierz szablon MVC, a następnie kliknij pozycję **Zmień uwierzytelnianie**.

    ![Zmień metodę uwierzytelniania](./media/cloud-services-dotnet-get-started/chgauth.png)
6. W oknie dialogowym **Zmienianie uwierzytelniania** wybierz pozycję **Bez uwierzytelniania**, a następnie kliknij przycisk **OK**.

    ![Bez uwierzytelniania](./media/cloud-services-dotnet-get-started/noauth.png)
7. W oknie dialogowym **Nowy projekt ASP.NET** kliknij przycisk **OK**.
8. W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy rozwiązanie (nie jeden z projektów) i wybierz pozycje **Dodaj — Nowy projekt**.
9. W oknie dialogowym **Dodawanie nowego projektu** wybierz pozycję **Windows** w obszarze **Visual C#** w lewym okienku, a następnie kliknij szablon **Biblioteka klas**.  
10. Nazwij projekt *ContosoAdsCommon*, a następnie kliknij przycisk **OK**.

    Z projektów ról Sieć Web i Proces roboczy należy odwoływać się do kontekstu platformy Entity Framework oraz modelu danych. Alternatywnie można zdefiniować klasy związane z platformą EF w projekcie roli Sieć Web i odwoływać się do tego projektu z projektu roli Proces roboczy. Jednak w przypadku zastosowania podejścia alternatywnego projekt roli Proces roboczy odwoływałby się do zestawów internetowych, których nie potrzebuje.

### <a name="update-and-add-nuget-packages"></a>Aktualizowanie i dodawanie pakietów NuGet
1. Otwórz okno dialogowe **Zarządzanie pakietami NuGet** dla rozwiązania.
2. W górnej części okna wybierz pozycję **Aktualizacje**.
3. Wyszukaj pakiet *WindowsAzure.Storage*. Jeśli znajduje się na liście, wybierz go, a następnie wybierz projekty sieci Web i procesu roboczego, w których chcesz go zaktualizować. Kliknij przycisk **Aktualizuj**.

    Biblioteka klienta magazynu jest aktualizowana częściej niż szablony projektów programu Visual Studio, dlatego często okazuje się, że wersja w nowo utworzonym projekcie wymaga aktualizacji.
4. W górnej części okna wybierz pozycję **Przeglądaj**.
5. Znajdź pakiet NuGet *EntityFramework*, a następnie zainstaluj go we wszystkich trzech projektach.
6. Znajdź pakiet NuGet *Microsoft.WindowsAzure.ConfigurationManager*, a następnie zainstaluj go w projekcie roli Proces roboczy.

### <a name="set-project-references"></a>Ustawianie odwołań do projektu
1. W projekcie ContosoAdsWeb ustaw odwołanie do projektu ContosoAdsCommon. Kliknij prawym przyciskiem myszy projekt ContosoAdsWeb, a następnie kliknij pozycję **odwołania**  -  **Dodaj odwołania**. W oknie dialogowym **Menedżer odwołań** wybierz pozycję **Rozwiązanie — projekty** w lewym okienku, wybierz pozycję **ContosoAdsCommon**, a następnie kliknij przycisk **OK**.
2. W projekcie ContosoAdsWorker ustaw odwołanie do projektu ContosoAdsCommon.

    Projekt ContosoAdsCommon będzie zawierać klasę kontekstu i model danych platformy Entity Framework, które będą używane dla frontonu i zaplecza.
3. W projekcie ContosoAdsWorker ustaw odwołanie do elementu `System.Drawing`.

    Ten zestaw jest używany przez zaplecze na potrzeby konwertowania obrazów na miniatury.

### <a name="configure-connection-strings"></a>Konfigurowanie parametrów połączenia
W tej sekcji będziesz konfigurować parametry połączenia usługi Azure Storage i danych SQL na potrzeby testowania lokalnego. Podane wcześniej w samouczku instrukcje dotyczące wdrażania wyjaśniają, w jaki sposób należy skonfigurować parametry połączenia aplikacji uruchomionej w chmurze.

1. W projekcie ContosoAdsWeb otwórz plik Web.config aplikacji i wstaw następujący element `connectionStrings` po elemencie `configSections`.

    ```xml
    <connectionStrings>
        <add name="ContosoAdsContext" connectionString="Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;" providerName="System.Data.SqlClient" />
    </connectionStrings>
    ```

    Jeśli korzystasz z programu Visual Studio 2015 lub nowszego, zastąp element „v11.0” elementem „MSSQLLocalDB”.
2. Zapisz zmiany.
3. W projekcie ContosoAdsCloudService kliknij prawym przyciskiem myszy pozycję ContosoAdsWeb w obszarze **Role**, a następnie kliknij polecenie **Właściwości**.

    ![Zrzut ekranu, który podświetla opcję menu właściwości w obszarze Role.](./media/cloud-services-dotnet-get-started/roleproperties.png)
4. W oknie właściwości **ContosoAdsWeb — [Rola]** kliknij kartę **Ustawienia**, a następnie kliknij pozycję **Dodaj ustawienie**.

    Pozostaw pozycję **Konfiguracja usługi** ustawioną na wartość **Wszystkie konfiguracje**.
5. Dodaj ustawienie o nazwie *StorageConnectionString*. Ustaw pozycję **Typ** na *ConnectionString*, a pozycję **Wartość** na *UseDevelopmentStorage = true*.

    ![Nowe parametry połączenia](./media/cloud-services-dotnet-get-started/scall.png)
6. Zapisz zmiany.
7. Postępuj zgodnie z tą samą procedurą, aby dodać parametry połączenia magazynu przy użyciu właściwości roli ContosoAdsWorker.
8. Pozostając w oknie właściwości **ContosoAdsWorker — [Rola]** dodaj inny ciąg połączenia:

   * Nazwa: ContosoAdsDbConnectionString
   * Typ: ciąg
   * Wartość: wklej parametry połączenia zastosowane już do projektu roli Sieć Web. (Poniższy przykład dotyczy programu Visual Studio 2013. Nie zapomnij zmienić źródła danych, jeśli kopiujesz ten przykład i korzystasz z programu Visual Studio 2015 lub nowszego).

       ```
       Data Source=(localdb)\v11.0; Initial Catalog=ContosoAds; Integrated Security=True; MultipleActiveResultSets=True;
       ```

### <a name="add-code-files"></a>Dodawanie plików kodu
W tej sekcji skopiujesz pliki kodu z pobranego rozwiązania do nowego rozwiązania. W poniższych sekcjach zostaną również pokazane i objaśnione części tego kodu.

Aby dodać pliki do projektu lub folderu, kliknij prawym przyciskiem myszy projekt lub folder, a następnie kliknij pozycję **Dodaj**  -  **istniejący element**. Wybierz pliki, a następnie kliknij pozycję **Dodaj**. Jeśli pojawi się pytanie, czy chcesz zastąpić istniejące pliki, kliknij pozycję **Tak**.

1. W projekcie ContosoAdsCommon usuń plik *Class1.cs* i dodaj w jego miejsce pliki *Ad.cs* i *ContosoAdscontext.cs* z pobranego projektu.
2. W projekcie ContosoAdsWeb dodaj poniższe pliki z pobranego projektu.

   * *Global.asax.cs*.  
   * W folderze *Views\Shared*: *\_Layout.cshtml*.
   * W folderze *Views\Home*: *Index.cshtml*.
   * W folderze *Controllers*: *AdController.cs*.
   * W folderze *Views\Ad* (najpierw utwórz ten folder): pięć plików *.cshtml*.
3. W projekcie ContosoAdsWorker dodaj plik *WorkerRole.cs* z pobranego projektu.

Teraz możesz skompilować i uruchomić aplikację zgodnie z instrukcjami podanymi wcześniej w samouczku. Aplikacja będzie używać lokalnych zasobów bazy danych i emulatora magazynu.

W poniższych sekcjach opisano kod powiązany z pracą z kolejkami, obiektami blob i środowiskiem platformy Azure. W tym samouczku nie objaśniono sposobu tworzenia widoków i kontrolerów MVC przy użyciu opcji tworzenia szkieletu, pisania kodu platformy Entity Framework współdziałającego z bazami danych programu SQL Server ani podstaw programowania asynchronicznego w programie ASP.NET 4.5. Aby uzyskać informacje dotyczące tych tematów, zobacz następujące zasoby:

* [Wprowadzenie do programu MVC 5](https://www.asp.net/mvc/tutorials/mvc-5/introduction/getting-started)
* [Wprowadzenie do programów EF 6 i MVC 5](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc)
* [Wprowadzenie do programowania asynchronicznego w programie .NET 4.5](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/web-development-best-practices#async).

### <a name="contosoadscommon---adcs"></a>ContosoAdsCommon — Ad.cs
Plik Ad.cs definiuje wyliczenia związane z kategoriami reklam i klasą jednostki POCO dla informacji o reklamach.

```csharp
public enum Category
{
    Cars,
    [Display(Name="Real Estate")]
    RealEstate,
    [Display(Name = "Free Stuff")]
    FreeStuff
}

public class Ad
{
    public int AdId { get; set; }

    [StringLength(100)]
    public string Title { get; set; }

    public int Price { get; set; }

    [StringLength(1000)]
    [DataType(DataType.MultilineText)]
    public string Description { get; set; }

    [StringLength(1000)]
    [DisplayName("Full-size Image")]
    public string ImageURL { get; set; }

    [StringLength(1000)]
    [DisplayName("Thumbnail")]
    public string ThumbnailURL { get; set; }

    [DataType(DataType.Date)]
    [DisplayFormat(DataFormatString = "{0:yyyy-MM-dd}", ApplyFormatInEditMode = true)]
    public DateTime PostedDate { get; set; }

    public Category? Category { get; set; }
    [StringLength(12)]
    public string Phone { get; set; }
}
```

### <a name="contosoadscommon---contosoadscontextcs"></a>ContosoAdsCommon — ContosoAdsContext.cs
Klasa ContosoAdsContext określa, czy klasa Ad jest używana w kolekcji DbSet przechowywanej przez platformę Entity Framework w bazie danych SQL.

```csharp
public class ContosoAdsContext : DbContext
{
    public ContosoAdsContext() : base("name=ContosoAdsContext")
    {
    }
    public ContosoAdsContext(string connString)
        : base(connString)
    {
    }
    public System.Data.Entity.DbSet<Ad> Ads { get; set; }
}
```

Klasa ma dwa konstruktory. Pierwszy z nich jest używany w projekcie sieci Web i określa nazwę parametrów połączenia, które są przechowywane w pliku Web.config. Drugi konstruktor umożliwia przekazywanie rzeczywistych parametrów połączenia używanych przez projekt roli Proces roboczy, ponieważ nie ma on pliku Web.config. Wcześniej przedstawiono lokalizację przechowywania tych parametrów połączenia. Dalej pokażemy, jak kod pobiera parametry połączenia podczas tworzenia wystąpień klasy DbContext.

### <a name="contosoadsweb---globalasaxcs"></a>ContosoAdsWeb — Global.asax.cs
Kod wywoływany z metody `Application_Start` umożliwia tworzenie kontenera obiektów blob *obrazów* i kolejki *obrazów*, jeśli jeszcze nie istnieją. Daje to gwarancję, że za każdym razem w przypadku rozpoczęcia pracy z nowym kontem magazynu lub przy użyciu emulatora magazynu na nowym komputerze wymagana kolejka i kontener obiektów blob zostaną utworzone automatycznie.

Kod uzyskuje dostęp do konta magazynu przy użyciu parametrów połączenia magazynu z pliku *.cscfg*.

```csharp
var storageAccount = CloudStorageAccount.Parse
    (RoleEnvironment.GetConfigurationSettingValue("StorageConnectionString"));
```

Następnie pobiera odwołanie do kontenera obiektów blob *obrazów*, tworzy kontener (jeśli jeszcze nie istnieje) oraz ustawia uprawnienia dostępu w obrębie nowego kontenera. Domyślnie nowe kontenery zezwalają na dostęp do obiektów blob tylko klientom z poświadczeniami konta magazynu. Obiekty blob w witrynie sieci Web muszą być publiczne, aby możliwe było wyświetlanie obrazów przy użyciu adresów URL wskazujących na obiekty blob obrazów.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
var imagesBlobContainer = blobClient.GetContainerReference("images");
if (imagesBlobContainer.CreateIfNotExists())
{
    imagesBlobContainer.SetPermissions(
        new BlobContainerPermissions
        {
            PublicAccess =BlobContainerPublicAccessType.Blob
        });
}
```

Podobny kod pobiera odwołanie do kolejki *obrazów* i tworzy nową kolejkę. W takim przypadku nie trzeba zmieniać uprawnień.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
var imagesQueue = queueClient.GetQueueReference("images");
imagesQueue.CreateIfNotExists();
```

### <a name="contosoadsweb---_layoutcshtml"></a>ContosoAdsWeb — \_Layout.cshtml
Plik *_Layout.cshtml* umożliwia ustawienie nazwy aplikacji w nagłówku i stopce oraz utworzenie wpisu menu „Ads”.

### <a name="contosoadsweb---viewshomeindexcshtml"></a>ContosoAdsWeb — Views\Home\Index.cshtml
Plik *Views\Home\Index.cshtml* umożliwia wyświetlanie linków kategorii na stronie głównej. Linki przekazują wartość całkowitą typu wyliczeniowego `Category` w zmiennej querystring na stronie indeksu reklam.

```razor
<li>@Html.ActionLink("Cars", "Index", "Ad", new { category = (int)Category.Cars }, null)</li>
<li>@Html.ActionLink("Real estate", "Index", "Ad", new { category = (int)Category.RealEstate }, null)</li>
<li>@Html.ActionLink("Free stuff", "Index", "Ad", new { category = (int)Category.FreeStuff }, null)</li>
<li>@Html.ActionLink("All", "Index", "Ad", null, null)</li>
```

### <a name="contosoadsweb---adcontrollercs"></a>ContosoAdsWeb — AdController.cs
W pliku *AdController.cs* konstruktor wywołuje metodę `InitializeStorage` w celu utworzenia obiektów biblioteki klienta usługi Azure Storage, które będą dostarczać interfejs API do pracy z kolejkami i obiektami blob.

Następnie kod pobiera odwołanie do kontenera obiektów blob *obrazów*, jak było to widać wcześniej w pliku *Global.asax.cs*. W tym czasie ustawiane są domyślne [zasady ponawiania](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/transient-fault-handling) odpowiednie dla aplikacji internetowej. Domyślnie wykładnicze zasady ponawiania wycofywania mogą spowodować, że aplikacja sieci Web przestanie odpowiadać przez dłużej niż minutę przy powtórzonych próbach w przypadku błędu przejściowego. Zasady ponawiania określone w tym miejscu powodują oczekiwanie przez trzy sekundy po każdej próbie. Maksymalna liczba prób to trzy.

```csharp
var blobClient = storageAccount.CreateCloudBlobClient();
blobClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesBlobContainer = blobClient.GetContainerReference("images");
```

Podobny kod pobiera odwołanie do kolejki *obrazów*.

```csharp
CloudQueueClient queueClient = storageAccount.CreateCloudQueueClient();
queueClient.DefaultRequestOptions.RetryPolicy = new LinearRetry(TimeSpan.FromSeconds(3), 3);
imagesQueue = queueClient.GetQueueReference("images");
```

Większość kodu kontrolera jest typowa dla pracy z modelem danych platformy Entity Framework za pomocą klasy DbContext. Wyjątkiem jest metoda HttpPost `Create`, która powoduje przekazanie pliku i zapisanie go w magazynie obiektów blob. Integrator modelu udostępnia obiekt [HttpPostedFileBase](/dotnet/api/system.web.httppostedfilebase) w metodzie.

```csharp
[HttpPost]
[ValidateAntiForgeryToken]
public async Task<ActionResult> Create(
    [Bind(Include = "Title,Price,Description,Category,Phone")] Ad ad,
    HttpPostedFileBase imageFile)
```

Jeśli użytkownik wybrał plik do przekazania, kod powoduje przekazanie pliku, zapisanie go w obiekcie blob i zaktualizowanie rekordu bazy danych Ad przy użyciu adresu URL, który wskazuje obiekt blob.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    blob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = blob.Uri.ToString();
}
```

Kod, który obsługuje przekazywanie, znajduje się w metodzie `UploadAndSaveBlobAsync`. Powoduje on utworzenie nazwy identyfikatora GUID dla obiektu blob, przekazanie i zapisanie pliku oraz zwraca odwołanie do zapisanego obiektu blob.

```csharp
private async Task<CloudBlockBlob> UploadAndSaveBlobAsync(HttpPostedFileBase imageFile)
{
    string blobName = Guid.NewGuid().ToString() + Path.GetExtension(imageFile.FileName);
    CloudBlockBlob imageBlob = imagesBlobContainer.GetBlockBlobReference(blobName);
    using (var fileStream = imageFile.InputStream)
    {
        await imageBlob.UploadFromStreamAsync(fileStream);
    }
    return imageBlob;
}
```

Po przekazaniu obiektu blob i zaktualizowaniu bazy danych przy użyciu metody HttpPost `Create` następuje utworzenie kolejki komunikatów w celu poinformowania procesu zaplecza, że obraz jest gotowy do konwersji na miniaturę.

```csharp
string queueMessageString = ad.AdId.ToString();
var queueMessage = new CloudQueueMessage(queueMessageString);
await queue.AddMessageAsync(queueMessage);
```

Kod metody HttpPost `Edit` jest podobny, z tą różnicą, że jeśli użytkownik wybierze nowy plik obrazu, należy usunąć wszystkie już istniejące obiekty blob.

```csharp
if (imageFile != null && imageFile.ContentLength != 0)
{
    await DeleteAdBlobsAsync(ad);
    imageBlob = await UploadAndSaveBlobAsync(imageFile);
    ad.ImageURL = imageBlob.Uri.ToString();
}
```

W kolejnym przykładzie pokazano kod, który służy do usuwania obiektów blob podczas usuwania reklamy.

```csharp
private async Task DeleteAdBlobsAsync(Ad ad)
{
    if (!string.IsNullOrWhiteSpace(ad.ImageURL))
    {
        Uri blobUri = new Uri(ad.ImageURL);
        await DeleteAdBlobAsync(blobUri);
    }
    if (!string.IsNullOrWhiteSpace(ad.ThumbnailURL))
    {
        Uri blobUri = new Uri(ad.ThumbnailURL);
        await DeleteAdBlobAsync(blobUri);
    }
}
private static async Task DeleteAdBlobAsync(Uri blobUri)
{
    string blobName = blobUri.Segments[blobUri.Segments.Length - 1];
    CloudBlockBlob blobToDelete = imagesBlobContainer.GetBlockBlobReference(blobName);
    await blobToDelete.DeleteAsync();
}
```

### <a name="contosoadsweb---viewsadindexcshtml-and-detailscshtml"></a>ContosoAdsWeb — Views\Ad\Index.cshtml i Details.cshtml
Plik *Index.cshtml* służy do wyświetlania miniatury z innymi danymi reklamy.

```razor
<img src="@Html.Raw(item.ThumbnailURL)" />
```

Plik *Details.cshtml* służy do wyświetlania obrazu w pełnym rozmiarze.

```razor
<img src="@Html.Raw(Model.ImageURL)" />
```

### <a name="contosoadsweb---viewsadcreatecshtml-and-editcshtml"></a>ContosoAdsWeb — Views\Ad\Create.cshtml i Edit.cshtml
Pliki *Create.cshtml* i *Edit.cshtml* określają kodowanie formularzy, które umożliwia kontrolerowi pobieranie obiektu `HttpPostedFileBase`.

```razor
@using (Html.BeginForm("Create", "Ad", FormMethod.Post, new { enctype = "multipart/form-data" }))
```

Element `<input>` informuje przeglądarkę o konieczności udostępnienia okna dialogowego wyboru pliku.

```razor
<input type="file" name="imageFile" accept="image/*" class="form-control fileupload" />
```

### <a name="contosoadsworker---workerrolecs---onstart-method"></a>ContosoAdsWorker — WorkerRole.cs — metoda OnStart
Środowisko roli procesu roboczego platformy Azure wywołuje metodę `OnStart` w klasie `WorkerRole` podczas uruchamiania roli Proces roboczy. Metoda `Run` jest wywoływana po zakończeniu działania metody `OnStart`.

Metoda `OnStart` pobiera parametry połączenia bazy danych z pliku *.cscfg* i przekazuje je do klasy Entity Framework DbContext. Nie trzeba określać dostawcy, ponieważ domyślnie jest wybierany SQLClient.

```csharp
var dbConnString = CloudConfigurationManager.GetSetting("ContosoAdsDbConnectionString");
db = new ContosoAdsContext(dbConnString);
```

Następnie metoda pobiera odwołanie do konta magazynu i tworzy kolejkę oraz kontener obiektów blob i kolejki, jeśli nie istnieją. Kod tej czynności działa podobnie do metody `Application_Start` roli Sieć Web.

### <a name="contosoadsworker---workerrolecs---run-method"></a>ContosoAdsWorker — WorkerRole.cs — metoda Run
Metoda `Run` jest wywoływana, gdy zakończy się inicjowanie przy użyciu metody `OnStart`. Metoda wykonuje nieskończoną pętlę, która oczekuje na nowe komunikaty w kolejce i przetwarza je po nadejściu.

```csharp
public override void Run()
{
    CloudQueueMessage msg = null;

    while (true)
    {
        try
        {
            msg = this.imagesQueue.GetMessage();
            if (msg != null)
            {
                ProcessQueueMessage(msg);
            }
            else
            {
                System.Threading.Thread.Sleep(1000);
            }
        }
        catch (StorageException e)
        {
            if (msg != null && msg.DequeueCount > 5)
            {
                this.imagesQueue.DeleteMessage(msg);
            }
            System.Threading.Thread.Sleep(5000);
        }
    }
}
```

Po każdej iteracji pętli, jeśli żaden komunikat nie zostanie znaleziony w kolejce, program zostanie uśpiony na sekundę. Zapobiega to nadmiernemu wykorzystaniu czasu procesora CPU oraz ponoszeniu zbyt wysokich kosztów transakcji magazynu powiązanych z rolą Proces roboczy. Zespół doradczy klientów firmy Microsoft opowiada historię o deweloperze, który zapomniał o tym, wdrożył aplikację i wyjechał na urlop. Po ich przekroczeniu koszt nadzoru jest wyższy niż urlop.

Czasami zawartość komunikatu w kolejce może powodować wystąpienie błędu podczas przetwarzania. Jest to tzw. *skażony komunikat*. Jeśli właśnie zarejestrowano błąd i ponownie uruchomiono pętlę, można w nieskończoność ponawiać próby przetworzenia takiego komunikatu.  Z tego względu blok catch zawiera instrukcję if, która sprawdza, ile razy aplikacja próbowała przetworzyć bieżący komunikat. Jeśli ta liczba przekracza 5, komunikat jest usuwany z kolejki.

`ProcessQueueMessage` — ten element jest wywoływany po znalezieniu komunikatu w kolejce.

```csharp
private void ProcessQueueMessage(CloudQueueMessage msg)
{
    var adId = int.Parse(msg.AsString);
    Ad ad = db.Ads.Find(adId);
    if (ad == null)
    {
        throw new Exception(String.Format("AdId {0} not found, can't create thumbnail", adId.ToString()));
    }

    CloudBlockBlob inputBlob = this.imagesBlobContainer.GetBlockBlobReference(ad.ImageURL);

    string thumbnailName = Path.GetFileNameWithoutExtension(inputBlob.Name) + "thumb.jpg";
    CloudBlockBlob outputBlob = this.imagesBlobContainer.GetBlockBlobReference(thumbnailName);

    using (Stream input = inputBlob.OpenRead())
    using (Stream output = outputBlob.OpenWrite())
    {
        ConvertImageToThumbnailJPG(input, output);
        outputBlob.Properties.ContentType = "image/jpeg";
    }

    ad.ThumbnailURL = outputBlob.Uri.ToString();
    db.SaveChanges();

    this.imagesQueue.DeleteMessage(msg);
}
```

Ten kod odczytuje bazę danych, aby uzyskać adres URL obrazu, konwertuje obraz na miniaturę, zapisuje miniaturę w obiekcie blob, aktualizuje bazę danych przy użyciu adresu URL obiektu blob miniatury i usuwa komunikat z kolejki.

> [!NOTE]
> Kod w metodzie `ConvertImageToThumbnailJPG` używa klas w przestrzeni nazw System.Drawing w celu uproszczenia działania. Jednak klasy w tej przestrzeni nazw zostały zaprojektowane do użytku z aplikacją Windows Forms. Nie są one obsługiwane w usłudze systemu Windows lub programu ASP.NET. Aby uzyskać więcej informacji o opcjach przetwarzania obrazu, zobacz [Dynamiczne generowanie obrazu](https://www.hanselman.com/blog/BackToBasicsDynamicImageGenerationASPNETControllersRoutingIHttpHandlersAndRunAllManagedModulesForAllRequests.aspx) i [Bezpośrednie zmienianie rozmiaru obrazu](https://www.hanselminutes.com/313/deep-inside-image-resizing-and-scaling-with-aspnet-and-iis-with-imageresizingnet-author-na).
>
>

## <a name="troubleshooting"></a>Rozwiązywanie problemów
Jeśli coś nie działa podczas wykonywania instrukcji podanych w tym samouczku, zapoznaj się z poniższym opisem niektórych typowych błędów i sposobów ich rozwiązywania.

### <a name="serviceruntimeroleenvironmentexception"></a>ServiceRuntime.RoleEnvironmentException
`RoleEnvironment`Obiekt jest udostępniany przez platformę Azure podczas uruchamiania aplikacji na platformie Azure lub uruchamiania lokalnego przy użyciu emulatora obliczeń platformy Azure.  Jeśli ten błąd wystąpi podczas pracy w środowisku lokalnym, upewnij się, że projekt ContosoAdsCloudService został ustawiony jako projekt startowy. Powoduje to skonfigurowanie projektu do uruchamiania przy użyciu emulatora obliczeń platformy Azure.

Jednym z celów użycia w aplikacji obiektu Azure RoleEnvironment jest uzyskanie wartości parametrów połączenia, które są przechowywane w pliku *.cscfg*. Ze względu na to inną przyczyną tego wyjątku jest brak parametrów połączenia. Upewnij się, że ustawienie StorageConnectionString zostało utworzone dla konfiguracji w chmurze i lokalnej w projekcie ContosoAdsWeb oraz że dla obydwu konfiguracji utworzono parametry połączenia w projekcie ContosoAdsWorker. W przypadku przeprowadzania wyszukiwania typu **Znajdź wszystkie** dla elementu StorageConnectionString w całym rozwiązaniu powinien on zostać znaleziony 9 razy w 6 plikach.

### <a name="cannot-override-to-port-xxx-new-port-below-minimum-allowed-value-8080-for-protocol-http"></a>Nie można zastąpić portu wartością xxx. Nowy port poniżej minimalnej dozwolonej wartości 8080 dla protokołu HTTP
Spróbuj zmienić numer portu używanego przez projekt sieci Web. Kliknij prawym przyciskiem myszy projekt ContosoAdsWeb, a następnie kliknij pozycję **Właściwości**. Kliknij kartę **Sieć Web**, a następnie zmień numer portu w ustawieniu **Adres URL projektu**.

Kolejny alternatywny sposób rozwiązania tego problemu opisano w poniższej sekcji.

### <a name="other-errors-when-running-locally"></a>Inne błędy po uruchomieniu w środowisku lokalnym
Domyślnie nowe projekty usług w chmurze używają usługi Azure COMPUTE emulator Express, aby symulować środowisko platformy Azure. Jest to uproszczona wersja pełnego emulatora obliczeń. W niektórych warunkach pełna wersja emulatora będzie działać, a wersja ekspresowa nie.  

Aby zmienić projekt tak, aby korzystał z pełnego emulatora, kliknij prawym przyciskiem myszy projekt ContosoAdsCloudService, a następnie kliknij polecenie **Właściwości**. W oknie **Właściwości** kliknij kartę **Sieć Web**, a następnie kliknij przycisk radiowy **Użyj pełnego emulatora**.

Aby można było uruchomić aplikację w pełnym emulatorze, należy otworzyć program Visual Studio z uprawnieniami administratora.

## <a name="next-steps"></a>Następne kroki
Aplikacja Contoso Ads została celowo uproszczona na potrzeby samouczka wprowadzającego. Na przykład: nie implementuje [wstrzykiwania zależności](https://www.asp.net/mvc/tutorials/hands-on-labs/aspnet-mvc-4-dependency-injection) ani [wzorców repozytorium i jednostki pracy](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/advanced-entity-framework-scenarios-for-an-mvc-web-application#repo), nie [używa interfejsu do rejestrowania](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/monitoring-and-telemetry#log), nie używa [migracji Code First platformy EF](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/migrations-and-deployment-with-the-entity-framework-in-an-asp-net-mvc-application) do zarządzania zmianami modelu danych ani [opcji elastyczności połączenia platformy EF](https://www.asp.net/mvc/tutorials/getting-started-with-ef-using-mvc/connection-resiliency-and-command-interception-with-the-entity-framework-in-an-asp-net-mvc-application) do zarządzania błędami sieci itd.

Poniżej przedstawiono niektóre przykładowe aplikacje usług w chmurze, w których zastosowano więcej rzeczywistych rozwiązań dotyczących kodowania. Zostały one uporządkowane w kolejności od mniej do bardziej złożonych:

* [PhluffyFotos](https://code.msdn.microsoft.com/PhluffyFotos-Sample-7ecffd31). Pomysł podobny do aplikacji Contoso Ads, ale z zaimplementowaną większą liczbą funkcji i rzeczywistych rozwiązań dotyczących kodowania.
* [Wielowarstwowa aplikacja usługi w chmurze Azure z tabelami, kolejkami i obiektami blob](https://code.msdn.microsoft.com/windowsazure/Windows-Azure-Multi-Tier-eadceb36). Obejmuje tabele usługi Azure Storage, a także obiekty blob i kolejki. Oparta na starszej wersji zestawu Azure SDK dla programu .NET aplikacja będzie wymagać pewnych modyfikacji, aby mogła współpracować z bieżącą wersją.

Aby uzyskać ogólne informacje o tworzeniu aplikacji w chmurze, zobacz [Tworzenie aplikacji w chmurze na platformie Azure](https://www.asp.net/aspnet/overview/developing-apps-with-windows-azure/building-real-world-cloud-apps-with-windows-azure/introduction).

Film wideo zawierający wprowadzenie do najlepszych rozwiązań i wzorców usługi Azure Storage można znaleźć w temacie [Microsoft Azure Storage — nowości, najlepsze rozwiązania i wzorce](https://channel9.msdn.com/Events/Build/2014/3-628).

Więcej informacji można znaleźć w następujących zasobach:

* [Azure Cloud Services, część 1: wprowadzenie](https://justazure.com/microsoft-azure-cloud-services-part-1-introduction/)
* [Jak zarządzać usługami Cloud Services](cloud-services-how-to-manage-portal.md)
* [Azure Storage](../storage/index.yml)
* [Jak wybrać dostawcę usług w chmurze](https://azure.microsoft.com/overview/choosing-a-cloud-service-provider/)
