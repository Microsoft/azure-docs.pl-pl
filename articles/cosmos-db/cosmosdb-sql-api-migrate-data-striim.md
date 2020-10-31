---
title: Migrowanie danych do Azure Cosmos DB konta interfejsu API SQL przy użyciu Striim
description: Dowiedz się, w jaki sposób używać Striim do migrowania danych z bazy danych Oracle do konta usługi SQL API Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: a77f039ea266e10130b6460855f989ab317a20ba
ms.sourcegitcommit: 3bdeb546890a740384a8ef383cf915e84bd7e91e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/30/2020
ms.locfileid: "93097672"
---
# <a name="migrate-data-to-azure-cosmos-db-sql-api-account-using-striim"></a>Migrowanie danych do Azure Cosmos DB konta interfejsu API SQL przy użyciu Striim
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]
 
Obraz Striim w portalu Azure Marketplace oferuje ciągły ruch danych w czasie rzeczywistym z magazynów danych i baz danych na platformę Azure. Podczas przemieszczania danych można wykonać denormalizację w trybie online, Przekształcanie danych, włączyć analizy w czasie rzeczywistym i scenariusze raportowania danych. Rozpoczęcie pracy z usługą Striim w celu ciągłego przenoszenia danych przedsiębiorstwa do Azure Cosmos DB interfejsu API SQL. Platforma Azure udostępnia ofertę w witrynie Marketplace, która ułatwia wdrażanie Striim i Migrowanie danych do Azure Cosmos DB. 

W tym artykule przedstawiono sposób korzystania z programu Striim do migrowania danych z **bazy danych programu Oracle** do **konta Azure Cosmos DB interfejsu API SQL** .

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz [subskrypcji platformy Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

* Baza danych Oracle działająca lokalnie z niektórymi danymi.

## <a name="deploy-the-striim-marketplace-solution"></a>Wdróż rozwiązanie w witrynie Striim Marketplace

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **Utwórz zasób** i Wyszukaj **Striim** w portalu Azure Marketplace. Wybierz pierwszą opcję i **Utwórz** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Następnie wprowadź właściwości konfiguracji wystąpienia Striim. Środowisko Striim jest wdrożone na maszynie wirtualnej. W okienku **podstawowe** wprowadź **nazwę użytkownika maszyny wirtualnej** , **hasło maszyny wirtualnej** (to hasło jest używane do obsługi protokołu SSH na maszynie wirtualnej). Wybierz swoją **subskrypcję** , **grupę zasobów** i **szczegółowe informacje o lokalizacji** , w których chcesz wdrożyć Striim. Po zakończeniu wybierz **przycisk OK** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. W okienku **Ustawienia klastra Striim** wybierz typ wdrożenia Striim i rozmiar maszyny wirtualnej.

   |Ustawienie | Wartość | Opis |
   | ---| ---| ---|
   |Typ wdrożenia Striim |Autonomiczna | Striim można uruchamiać w **autonomicznych lub niezależnych** typach wdrożenia **klastra** . Tryb autonomiczny wdroży serwer Striim na jednej maszynie wirtualnej i będzie można wybrać rozmiar maszyn wirtualnych w zależności od ilości danych. W trybie klastra zostanie wdrożony serwer Striim na co najmniej dwóch maszynach wirtualnych o wybranym rozmiarze. Środowiska klastra z więcej niż 2 węzłami oferują automatyczną wysoką dostępność i tryb failover.</br></br> W tym samouczku można wybrać opcję autonomiczną. Użyj domyślnej maszyny wirtualnej o rozmiarze "Standard_F4s".  | 
   | Nazwa klastra Striim|    <Striim_cluster_Name>|  Nazwa klastra Striim.|
   | Striim hasło klastra|   <Striim_cluster_password>|  Hasło do klastra.|

   Po wypełnieniu formularza wybierz **przycisk OK** , aby kontynuować.

1. W okienku **Ustawienia dostępu Striim** Skonfiguruj **publiczny adres IP** (wybierz wartości domyślne), **nazwę domeny dla Striim** , **hasło administratora** , którego chcesz użyć do logowania się do interfejsu użytkownika Striim. Konfigurowanie sieci wirtualnej i podsieci (wybierz wartości domyślne). Po wypełnieniu szczegółów wybierz przycisk **OK** , aby kontynuować.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Platforma Azure sprawdzi poprawność wdrożenia i upewni się, że wszystko wygląda dobrze. Sprawdzanie poprawności może potrwać kilka minut. Po zakończeniu walidacji wybierz **przycisk OK** .
  
1. Na koniec zapoznaj się z warunkami użytkowania i wybierz pozycję **Utwórz** , aby utworzyć wystąpienie Striim. 

## <a name="configure-the-source-database"></a>Konfigurowanie źródłowej bazy danych 

W tej sekcji należy skonfigurować bazę danych Oracle jako źródło do przenoszenia danych.  Do nawiązania połączenia z bazą danych Oracle potrzebny jest [Sterownik Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) . Aby odczytać zmiany ze źródłowej bazy danych Oracle, można użyć [interfejsów API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) lub Xstream. Sterownik Oracle JDBC musi znajdować się w ścieżce klas Java Striim w celu odczytywania, zapisywania lub utrwalania danych z bazy danych Oracle.

Pobierz sterownik [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) na maszynę lokalną. Zainstalujesz go w klastrze Striim później.

## <a name="configure-the-target-database"></a>Skonfiguruj docelową bazę danych

W tej sekcji skonfigurujesz Azure Cosmos DB konto interfejsu API SQL jako cel przenoszenia danych.

1. Utwórz [konto Azure Cosmos DB interfejsu API SQL](create-cosmosdb-resources-portal.md) przy użyciu Azure Portal.

1. Przejdź do okienka **Eksplorator danych** na koncie usługi Azure Cosmos. Wybierz pozycję **nowy kontener** , aby utworzyć nowy kontener. Załóżmy, że migrujesz *produkty* i *porządkują* dane z bazy danych Oracle Database do Azure Cosmos DB. Utwórz nową bazę danych o nazwie **StriimDemo** z kontenerem o nazwie **Orders** . Zainicjuj obsługę kontenera przy użyciu **1000 jednostek ru** (w tym przykładzie użyto 1000 jednostek ru, ale należy użyć przepływności szacowanej dla obciążenia) i **/ORDER_ID** jako klucza partycji. Te wartości będą się różnić w zależności od danych źródłowych. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/create-sql-api-account.png" alt-text="Znajdź element witryny Marketplace Striim":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurowanie przepływu danych Azure Cosmos DB firmy Oracle

1. Teraz Wróćmy do Striim. Przed rozpoczęciem współpracy z programem Striim Zainstaluj pobrany wcześniej sterownik Oracle JDBC.

1. Przejdź do wystąpienia Striim, które zostało wdrożone w Azure Portal. Wybierz przycisk **Połącz** na górnym pasku menu i na karcie **SSH** , skopiuj adres URL w polu **Zaloguj przy użyciu lokalnego konta maszyny wirtualnej** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Otwórz nowe okno terminalu i uruchom polecenie SSH skopiowane z Azure Portal. W tym artykule jest używany terminal w MacOS, można wykonać podobne instrukcje przy użyciu instrukcji podano lub innego klienta SSH na komputerze z systemem Windows. Po wyświetleniu monitu wpisz **wartość tak** , aby kontynuować, a następnie wprowadź **hasło** ustawione dla maszyny wirtualnej w poprzednim kroku.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Teraz otwórz nową kartę terminala, aby skopiować pobrany wcześniej plik **ojdbc8. jar** . Aby skopiować plik JAR z komputera lokalnego do folderu tmp wystąpienia Striim uruchomionego na platformie Azure, użyj następującego polecenia SCP:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Następnie przejdź z powrotem do okna, w którym zostało wykonane połączenie SSH z wystąpieniem Striim i zaloguj się jako sudo. Przenieś plik **jar ojdbc8** z katalogu **/tmp** do katalogu **lib** wystąpienia Striim za pomocą następujących poleceń:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Znajdź element witryny Marketplace Striim":::


1. W tym samym oknie terminalu ponownie uruchom serwer Striim, wykonując następujące polecenia:

   ```bash
   Systemctl stop striim-node
   Systemctl stop striim-dbms
   Systemctl start striim-dbms
   Systemctl start striim-node
   ```
 
1. Uruchomienie Striim potrwa minutę. Jeśli chcesz zobaczyć stan, uruchom następujące polecenie: 

   ```bash
   tail -f /opt/striim/logs/striim-node.log
   ```

1. Teraz wróć do platformy Azure i skopiuj publiczny adres IP maszyny wirtualnej Striim. 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Aby przejść do interfejsu użytkownika sieci Web Striim, Otwórz nową kartę w przeglądarce i skopiuj publiczny adres IP, a następnie: 9080. Zaloguj się przy użyciu nazwy użytkownika **administratora** oraz hasła administratora określonego w Azure Portal.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Teraz dotrzemy do strony głównej Striim. Istnieją trzy różne okienka — **pulpity nawigacyjne** , **aplikacje** i **SourcePreview** . Okienko pulpity nawigacyjne umożliwia przenoszenie danych w czasie rzeczywistym i wizualizację. Okienko aplikacje zawiera potoki danych przesyłania strumieniowego lub przepływy danych. Po prawej stronie strony jest SourcePreview, gdzie można wyświetlić podgląd danych przed ich przeniesieniem.

1. Wybierz okienko **aplikacje** . teraz będziemy skupić się na tym okienku. Istnieją różne przykładowe aplikacje, których można użyć w celu uzyskania informacji na temat Striim, ale w tym artykule utworzysz własne. Wybierz przycisk **Dodaj aplikację** w prawym górnym rogu.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Istnieje kilka różnych sposobów tworzenia aplikacji Striim. Wybierz pozycję **Rozpocznij od szablonu** , aby rozpocząć pracę z istniejącym szablonem.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-with-template.png" alt-text="Znajdź element witryny Marketplace Striim" i wybierz pozycję **target: Azure Cosmos DB** a następnie wybierz pozycję **baza danych Oracle do Azure Cosmos DB** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/oracle-cdc-cosmosdb.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Na następnej stronie Nadaj nazwę aplikacji. Możesz podać nazwę, np. **oraToCosmosDB** , a następnie wybrać pozycję **Zapisz** .

1. Następnie wprowadź konfigurację źródłową wystąpienia źródłowego programu Oracle. Wprowadź wartość dla **nazwy źródłowej** . Nazwa źródła jest tylko konwencją nazewnictwa dla aplikacji Striim. możesz użyć czegoś takiego jak **src_onPremOracle** . Wprowadź wartości dla pozostałej części **adresu URL** parametrów źródła, **username** , **Password** i **LogMiner** jako czytelnik odczytywania danych z firmy Oracle. Wybierz przycisk **Dalej** , aby kontynuować.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-source-parameters.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Striim sprawdzi środowisko i upewnij się, że może nawiązać połączenie ze źródłowym wystąpieniem programu Oracle, mieć odpowiednie uprawnienia i że baza danych została poprawnie skonfigurowana. Po zweryfikowaniu wszystkich wartości wybierz pozycję **dalej** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/validate-source-parameters.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Wybierz tabele z bazy danych Oracle, które chcesz zmigrować. Na przykład Wybierzmy tabelę Orders i wybierzemy **dalej** . 

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/select-source-tables.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Po wybraniu tabeli źródłowej można wykonywać bardziej skomplikowane operacje, takie jak mapowanie i filtrowanie. W takim przypadku wystarczy utworzyć replikę tabeli źródłowej w Azure Cosmos DB. Wybierz pozycję **dalej** , aby skonfigurować obiekt docelowy

1. Skonfigurujmy teraz obiekt docelowy:

   * **Nazwa docelowa** — Podaj przyjazną nazwę dla elementu docelowego. 
   * **Dane wejściowe z** — z listy rozwijanej wybierz strumień wejściowy z tego, który został utworzony w konfiguracji źródłowej programu Oracle. 
   * **Kolekcje** — wprowadź właściwości konfiguracji Azure Cosmos DB docelowej. Składnia kolekcji to **SourceSchema. sources, TargetDatabase. TargetContainer** . W tym przykładzie wartością może być "SYSTEM. ZAMÓWIENIA, StriimDemo. Orders. 
   * **AccessKey** — konto platformy Azure Cosmos.
   * **ServiceEndpoint** — identyfikator URI konta usługi Azure Cosmos. można go znaleźć w sekcji **klucze** w Azure Portal. 

   Wybierz pozycję **Zapisz** i **dalej** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-target-parameters.png" alt-text="Znajdź element witryny Marketplace Striim":::


1. Następnie dotrzesz do projektanta przepływu, który umożliwia przeciąganie i upuszczanie łączników pól w celu utworzenia aplikacji przesyłania strumieniowego. W tym momencie nie wprowadzisz żadnych modyfikacji przepływu. Aby kontynuować i wdrożyć aplikację, wybierz przycisk **Wdróż aplikację** .
 
   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-app.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. W oknie wdrożenie możesz określić, czy chcesz uruchamiać niektóre części aplikacji w określonych częściach topologii wdrażania. Ponieważ korzystamy z prostej topologii wdrażania za pomocą platformy Azure, użyjemy opcji domyślnej.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/deploy-using-default-option.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Po wdrożeniu można wyświetlić podgląd strumienia, aby wyświetlić dane przepływające przez program. Wybierz ikonę **Wave** i Eyeball obok niej. Wybierz przycisk **wdrożony** na górnym pasku menu, a następnie wybierz pozycję **Uruchom aplikację** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/start-app.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Za pomocą czytnika funkcji **przechwytywania zmian danych** , Striim będzie pobierać tylko nowe zmiany w bazie danych. Jeśli dane są przepływane za poorednictwem tabel źródłowych, zobaczysz ją. Jednak ponieważ jest to tabela demonstracyjna, źródło nie jest połączone z żadną aplikacją. W przypadku korzystania z przykładowego generatora danych można wstawić łańcuch zdarzeń do bazy danych programu Oracle.

1. Zobaczysz dane przepływające przez platformę Striim. Striim pobiera wszystkie metadane skojarzone z tabelą, co jest przydatne do monitorowania danych i upewnienia się, że dane są terenem właściwym.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/configure-cdc-pipeline.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Na koniec Zalogujmy się do platformy Azure i przejdź do swojego konta usługi Azure Cosmos. Odśwież Eksplorator danych i możesz zobaczyć, że dane zostały odebrane.  

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/portal-validate-results.png" alt-text="Znajdź element witryny Marketplace Striim":::

Korzystając z rozwiązania Striim na platformie Azure, można w sposób ciągły migrować dane do Azure Cosmos DB z różnych źródeł, takich jak Oracle, Cassandra, MongoDB i różne, aby Azure Cosmos DB. Aby dowiedzieć się więcej, odwiedź witrynę [sieci Web Striim](https://www.striim.com/), [Pobierz bezpłatną 30-dniową wersję próbną Striim](https://go2.striim.com/download-free-trial)i w przypadku wszelkich problemów podczas konfigurowania ścieżki migracji przy użyciu Striim, Utwórz [żądanie pomocy technicznej.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Następne kroki

* Jeśli migrujesz dane do Azure Cosmos DB SQL API, zobacz [jak migrować dane do konta interfejs API Cassandra przy użyciu Striim](cosmosdb-cassandra-api-migrate-data-striim.md)

* [Monitoruj i Debuguj dane za pomocą metryk Azure Cosmos DB](use-metrics.md)