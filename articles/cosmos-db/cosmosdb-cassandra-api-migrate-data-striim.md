---
title: Migrowanie danych do konta Azure Cosmos DB interfejs API Cassandra za pomocą Striim
description: Dowiedz się, w jaki sposób używać Striim do migrowania danych z bazy danych Oracle do konta interfejs API Cassandra Azure Cosmos DB.
author: SnehaGunda
ms.service: cosmos-db
ms.subservice: cosmosdb-cassandra
ms.topic: how-to
ms.date: 07/22/2019
ms.author: sngun
ms.reviewer: sngun
ms.openlocfilehash: 64fb37fb4a57b3ee397ffe8814c76c7dca2fb9b3
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334617"
---
# <a name="migrate-data-to-azure-cosmos-db-cassandra-api-account-using-striim"></a>Migrowanie danych do konta Azure Cosmos DB interfejs API Cassandra za pomocą Striim
[!INCLUDE[appliesto-cassandra-api](includes/appliesto-cassandra-api.md)]

Obraz Striim w portalu Azure Marketplace oferuje ciągły ruch danych w czasie rzeczywistym z magazynów danych i baz danych na platformę Azure. Podczas przemieszczania danych można wykonać denormalizację w trybie online, Przekształcanie danych, włączyć analizy w czasie rzeczywistym i scenariusze raportowania danych. Rozpoczęcie pracy z usługą Striim w celu ciągłego przenoszenia danych przedsiębiorstwa do interfejs API Cassandra Azure Cosmos DB. Platforma Azure udostępnia ofertę w witrynie Marketplace, która ułatwia wdrażanie Striim i Migrowanie danych do Azure Cosmos DB. 

W tym artykule przedstawiono sposób korzystania z programu Striim do migrowania danych z **bazy danych programu Oracle** do **konta interfejs API Cassandra Azure Cosmos DB**.

## <a name="prerequisites"></a>Wymagania wstępne

* Jeśli nie masz [subskrypcji platformy Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), przed rozpoczęciem Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) .

* Baza danych Oracle działająca lokalnie z niektórymi danymi.

## <a name="deploy-the-striim-marketplace-solution"></a>Wdróż rozwiązanie w witrynie Striim Marketplace

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com/).

1. Wybierz pozycję **Utwórz zasób** i Wyszukaj **Striim** w portalu Azure Marketplace. Wybierz pierwszą opcję i **Utwórz**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-azure-marketplace.png" alt-text="Znajdź element witryny Marketplace Striim":::

1. Następnie wprowadź właściwości konfiguracji wystąpienia Striim. Środowisko Striim jest wdrożone na maszynie wirtualnej. W okienku **podstawowe** wprowadź **nazwę użytkownika maszyny wirtualnej** , **hasło maszyny wirtualnej** (to hasło jest używane do obsługi protokołu SSH na maszynie wirtualnej). Wybierz swoją **subskrypcję** , **grupę zasobów** i **szczegółowe informacje o lokalizacji** , w których chcesz wdrożyć Striim. Po zakończeniu wybierz **przycisk OK**.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-configure-basic-settings.png" alt-text="Skonfiguruj podstawowe ustawienia dla Striim":::


1. W okienku **Ustawienia klastra Striim** wybierz typ wdrożenia Striim i rozmiar maszyny wirtualnej.

   |Ustawienie | Wartość | Opis |
   | ---| ---| ---|
   |Typ wdrożenia Striim |Autonomiczna | Striim można uruchamiać w **autonomicznych lub niezależnych** typach wdrożenia **klastra** . Tryb autonomiczny wdroży serwer Striim na jednej maszynie wirtualnej i będzie można wybrać rozmiar maszyn wirtualnych w zależności od ilości danych. W trybie klastra zostanie wdrożony serwer Striim na co najmniej dwóch maszynach wirtualnych o wybranym rozmiarze. Środowiska klastra z więcej niż 2 węzłami oferują automatyczną wysoką dostępność i tryb failover.</br></br> W tym samouczku można wybrać opcję autonomiczną. Użyj domyślnej maszyny wirtualnej o rozmiarze "Standard_F4s". | 
   | Nazwa klastra Striim|    <Striim_cluster_Name>|  Nazwa klastra Striim.|
   | Striim hasło klastra|   <Striim_cluster_password>|  Hasło do klastra.|

   Po wypełnieniu formularza wybierz **przycisk OK** , aby kontynuować.

1. W okienku **Ustawienia dostępu Striim** Skonfiguruj **publiczny adres IP** (wybierz wartości domyślne), **nazwę domeny dla Striim** , **hasło administratora** , którego chcesz użyć do logowania się do interfejsu użytkownika Striim. Konfigurowanie sieci wirtualnej i podsieci (wybierz wartości domyślne). Po wypełnieniu szczegółów wybierz przycisk **OK** , aby kontynuować.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-access-settings.png" alt-text="Ustawienia dostępu Striim":::

1. Platforma Azure sprawdzi poprawność wdrożenia i upewni się, że wszystko wygląda dobrze. Sprawdzanie poprawności może potrwać kilka minut. Po zakończeniu walidacji wybierz **przycisk OK**.
  
1. Na koniec zapoznaj się z warunkami użytkowania i wybierz pozycję **Utwórz** , aby utworzyć wystąpienie Striim. 

## <a name="configure-the-source-database"></a>Konfigurowanie źródłowej bazy danych

W tej sekcji należy skonfigurować bazę danych Oracle jako źródło do przenoszenia danych.  Do nawiązania połączenia z bazą danych Oracle potrzebny jest [Sterownik Oracle JDBC](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) . Aby odczytać zmiany ze źródłowej bazy danych Oracle, można użyć [interfejsów API](https://docs.oracle.com/cd/E11882_01/server.112/e16545/xstrm_intro.htm#XSTRM72647) [LogMiner](https://www.oracle.com/technetwork/database/features/availability/logmineroverview-088844.html) lub Xstream. Sterownik Oracle JDBC musi znajdować się w ścieżce klas Java Striim w celu odczytywania, zapisywania lub utrwalania danych z bazy danych Oracle.

Pobierz sterownik [ojdbc8. jar](https://www.oracle.com/technetwork/database/features/jdbc/jdbc-ucp-122-3110062.html) na maszynę lokalną. Zainstalujesz go w klastrze Striim później.

## <a name="configure-target-database"></a>Skonfiguruj docelową bazę danych

W tej sekcji skonfigurujesz Azure Cosmos DB konto interfejs API Cassandra jako cel przenoszenia danych.

1. Utwórz [konto interfejs API Cassandra Azure Cosmos DB](create-cassandra-dotnet.md#create-a-database-account) przy użyciu Azure Portal.

1. Przejdź do okienka **Eksplorator danych** na koncie usługi Azure Cosmos. Wybierz pozycję **Nowa tabela** , aby utworzyć nowy kontener. Załóżmy, że migrujesz *produkty* i *porządkują* dane z bazy danych Oracle Database do Azure Cosmos DB. Utwórz nową przestrzeń kluczy o nazwie **StriimDemo** z kontenerem Orders. Zainicjuj obsługę kontenera przy użyciu **1000 jednostek ru** (w tym przykładzie użyto 1000 jednostek ru, ale należy użyć przepływności szacowanej dla obciążenia) i **/ORDER_ID** jako klucz podstawowy. Te wartości będą się różnić w zależności od danych źródłowych. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-cassandra-api-account.png" alt-text="Utwórz konto interfejs API Cassandra":::

## <a name="configure-oracle-to-azure-cosmos-db-data-flow"></a>Konfigurowanie przepływu danych Azure Cosmos DB firmy Oracle

1. Teraz Wróćmy do Striim. Przed rozpoczęciem współpracy z programem Striim Zainstaluj pobrany wcześniej sterownik Oracle JDBC.

1. Przejdź do wystąpienia Striim, które zostało wdrożone w Azure Portal. Wybierz przycisk **Połącz** na górnym pasku menu i na karcie **SSH** , skopiuj adres URL w polu **Zaloguj przy użyciu lokalnego konta maszyny wirtualnej** .

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/get-ssh-url.png" alt-text="Pobieranie adresu URL protokołu SSH":::

1. Otwórz nowe okno terminalu i uruchom polecenie SSH skopiowane z Azure Portal. W tym artykule jest używany terminal w MacOS, można wykonać podobne instrukcje przy użyciu instrukcji podano lub innego klienta SSH na komputerze z systemem Windows. Po wyświetleniu monitu wpisz **wartość tak** , aby kontynuować, a następnie wprowadź **hasło** ustawione dla maszyny wirtualnej w poprzednim kroku.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-vm-connect.png" alt-text="Nawiązywanie połączenia z maszyną wirtualną Striim":::

1. Teraz otwórz nową kartę terminala, aby skopiować pobrany wcześniej plik **ojdbc8. jar** . Aby skopiować plik JAR z komputera lokalnego do folderu tmp wystąpienia Striim uruchomionego na platformie Azure, użyj następującego polecenia SCP:

   ```bash
   cd <Directory_path_where_the_Jar_file_exists> 
   scp ojdbc8.jar striimdemo@striimdemo.westus.cloudapp.azure.com:/tmp
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-jar-file.png" alt-text="Skopiuj plik JAR z maszyny Location do Striim":::

1. Następnie przejdź z powrotem do okna, w którym zostało wykonane połączenie SSH z wystąpieniem Striim i zaloguj się jako sudo. Przenieś plik **jar ojdbc8** z katalogu **/tmp** do katalogu **lib** wystąpienia Striim za pomocą następujących poleceń:

   ```bash
   sudo su
   cd /tmp
   mv ojdbc8.jar /opt/striim/lib
   chmod +x ojdbc8.jar
   ```

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/move-jar-file.png" alt-text="Przenieś plik jar do folderu lib":::


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

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/copy-public-ip-address.png" alt-text="Kopiuj adres IP maszyny wirtualnej Striim":::

1. Aby przejść do interfejsu użytkownika sieci Web Striim, Otwórz nową kartę w przeglądarce i skopiuj publiczny adres IP, a następnie: 9080. Zaloguj się przy użyciu nazwy użytkownika **administratora** oraz hasła administratora określonego w Azure Portal.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/striim-login-ui.png" alt-text="Zaloguj się do Striim":::

1. Teraz dotrzemy do strony głównej Striim. Istnieją trzy różne okienka — **pulpity nawigacyjne** , **aplikacje** i **SourcePreview**. Okienko pulpity nawigacyjne umożliwia przenoszenie danych w czasie rzeczywistym i wizualizację. Okienko aplikacje zawiera potoki danych przesyłania strumieniowego lub przepływy danych. Po prawej stronie strony jest SourcePreview, gdzie można wyświetlić podgląd danych przed ich przeniesieniem.

1. Wybierz okienko **aplikacje** . teraz będziemy skupić się na tym okienku. Istnieją różne przykładowe aplikacje, których można użyć w celu uzyskania informacji na temat Striim, ale w tym artykule utworzysz własne. Wybierz przycisk **Dodaj aplikację** w prawym górnym rogu.

   :::image type="content" source="./media/cosmosdb-sql-api-migrate-data-striim/add-striim-app.png" alt-text="Dodawanie aplikacji Striim":::

1. Istnieje kilka różnych sposobów tworzenia aplikacji Striim. Wybierz pozycję **Rozpocznij od początku** dla tego scenariusza.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-app-from-scratch.png" alt-text="Uruchamianie aplikacji od podstaw":::

1. Nadaj przyjazną nazwę aplikacji, podobną do **oraToCosmosDB** i wybierz pozycję **Zapisz**.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/create-new-application.png" alt-text="Tworzenie nowej aplikacji":::

1. Dotrzesz do projektanta przepływu, który umożliwia przeciąganie i upuszczanie łączników pól w celu utworzenia aplikacji przesyłania strumieniowego. Wpisz **Oracle** na pasku wyszukiwania, przeciągnij i upuść źródło danych **przechwytywania oprogramowania Oracle** na kanwę aplikacji.  

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/oracle-cdc-source.png" alt-text="Źródło danych przechwytywania oprogramowania Oracle":::

1. Wprowadź właściwości konfiguracji źródłowej wystąpienia programu Oracle. Nazwa źródła jest tylko konwencją nazewnictwa dla aplikacji Striim, można użyć nazwy, takiej jak  **src_onPremOracle**. Wprowadź również inne szczegóły, takie jak typ karty, adres URL połączenia, nazwa użytkownika, hasło, nazwa tabeli. Wybierz pozycję **Zapisz** , aby kontynuować.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-source-parameters.png" alt-text="Konfigurowanie parametrów źródłowych":::

1. Teraz kliknij ikonę Wave strumienia, aby połączyć wystąpienie elementu docelowego Azure Cosmos DB. 

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/connect-to-target.png" alt-text="Połącz z miejscem docelowym":::

1. Przed skonfigurowaniem elementu docelowego upewnij się, że dodano [certyfikat główny Baltimore do środowiska Java Striim](/azure/developer/java/sdk/java-sdk-add-certificate-ca-store#to-add-a-root-certificate-to-the-cacerts-store).

1. Wprowadź właściwości konfiguracji docelowego wystąpienia Azure Cosmos DB i wybierz pozycję **Zapisz** , aby kontynuować. Oto kluczowe parametry, które należy zwrócić uwagę:

   * **Adapter** — Użyj **DatabaseWriter**. Podczas zapisywania do Azure Cosmos DB interfejs API Cassandra jest wymagany DatabaseWriter. 3.6.0 sterownika Cassandra jest powiązany z Striim. Jeśli DatabaseWriter przekracza liczbę jednostek ru zainicjowanych w kontenerze usługi Azure Cosmos, wystąpi awaria aplikacji.

   * **Adres URL połączenia** — Określ adres URL połączenia usługi Azure Cosmos DB JDBC. Adres URL jest w formacie     `jdbc:cassandra://<contactpoint>:10350/<databaseName>?SSL=true`

   * Nazwa **użytkownika** — Określ nazwę konta usługi Azure Cosmos.
   
   * **Hasło** — Określ klucz podstawowy konta usługi Azure Cosmos.

   * **Tabele-obiekty** docelowe muszą mieć klucze podstawowe, a klucze podstawowe nie mogą zostać zaktualizowane.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters1.png" alt-text="Zrzut ekranu pokazujący konfigurowalne właściwości docelowe.":::

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/configure-target-parameters2.png" alt-text="Konfigurowanie właściwości docelowych":::

1. Teraz będziemy korzystać z aplikacji Striim. Na górnym pasku menu wybierz pozycję **utworzone** , a następnie **Wdróż aplikację**. W oknie wdrożenie możesz określić, czy chcesz uruchamiać niektóre części aplikacji w określonych częściach topologii wdrażania. Ponieważ korzystamy z prostej topologii wdrażania za pomocą platformy Azure, użyjemy opcji domyślnej.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/deploy-the-app.png" alt-text="Wdrażanie aplikacji":::


1. Teraz przejdziemy do wersji zapoznawczej strumienia, aby zobaczyć dane przepływające przez Striim. Kliknij ikonę Wave i kliknij ikonę oka obok niej. Po wdrożeniu można wyświetlić podgląd strumienia, aby wyświetlić dane przepływające przez program. Wybierz ikonę **Wave** i **Eyeball** obok niej. Wybierz przycisk **wdrożony** na górnym pasku menu, a następnie wybierz pozycję **Uruchom aplikację**.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/start-the-app.png" alt-text="Uruchom aplikację":::

1. Za pomocą czytnika funkcji **przechwytywania zmian danych** , Striim będzie pobierać tylko nowe zmiany w bazie danych. Jeśli dane są przepływane za poorednictwem tabel źródłowych, zobaczysz ją. Jednak ponieważ jest to Przykładowa tabela, źródło, które nie jest podłączone do żadnej aplikacji. W przypadku korzystania z przykładowego generatora danych można wstawić łańcuch zdarzeń do bazy danych programu Oracle.

1. Zobaczysz dane przepływające przez platformę Striim. Striim pobiera wszystkie metadane skojarzone z tabelą, co jest przydatne do monitorowania danych i upewnienia się, że dane są terenem właściwym.

   :::image type="content" source="./media/cosmosdb-cassandra-api-migrate-data-striim/setup-cdc-pipeline.png" alt-text="Konfigurowanie potoku przechwytywania zmian":::

1. Na koniec Zalogujmy się do platformy Azure i przejdź do swojego konta usługi Azure Cosmos. Odśwież Eksplorator danych i możesz zobaczyć, że dane zostały odebrane. 

Korzystając z rozwiązania Striim na platformie Azure, można w sposób ciągły migrować dane do Azure Cosmos DB z różnych źródeł, takich jak Oracle, Cassandra, MongoDB i różne, aby Azure Cosmos DB. Aby dowiedzieć się więcej, odwiedź witrynę [sieci Web Striim](https://www.striim.com/), [Pobierz bezpłatną 30-dniową wersję próbną Striim](https://go2.striim.com/download-free-trial)i w przypadku wszelkich problemów podczas konfigurowania ścieżki migracji przy użyciu Striim, Utwórz [żądanie pomocy technicznej.](https://go2.striim.com/request-support-striim)

## <a name="next-steps"></a>Następne kroki

* Jeśli migrujesz dane do Azure Cosmos DB SQL API, zobacz [jak migrować dane do konta interfejs API Cassandra przy użyciu Striim](cosmosdb-sql-api-migrate-data-striim.md)

* [Monitoruj i Debuguj dane za pomocą metryk Azure Cosmos DB](use-metrics.md)