---
title: Instalowanie aplikacji innych firm w usłudze Azure HDInsight
description: Dowiedz się, jak instalować aplikacje Apache Hadoop innych firm w usłudze Azure HDInsight.
ms.service: hdinsight
ms.custom: hdinsightactive
ms.topic: how-to
ms.date: 06/17/2019
ms.openlocfilehash: cfbbc1f11a24379561df26e2fb8d9da20b249317
ms.sourcegitcommit: 2f9f306fa5224595fa5f8ec6af498a0df4de08a8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/28/2021
ms.locfileid: "98942746"
---
# <a name="install-third-party-apache-hadoop-applications-on-azure-hdinsight"></a>Instalowanie aplikacji Apache Hadoop innych firm w usłudze Azure HDInsight

Dowiedz się, jak zainstalować aplikację [Apache Hadoop](https://hadoop.apache.org/) innej firmy w usłudze Azure HDInsight. Aby uzyskać instrukcje instalowania własnej aplikacji, zobacz [Install custom HDInsight applications](hdinsight-apps-install-custom-applications.md) (Instalowanie niestandardowych aplikacji usługi HDInsight).

Aplikacja HDInsight An jest aplikacją, którą użytkownicy mogą instalować w klastrze usługi HDInsight. Te aplikacje mogą być opracowane przez firmę Microsoft, niezależnych dostawców oprogramowania (ISV) lub samodzielnie.  

Na poniższej liście przedstawiono opublikowane aplikacje:

|Aplikacja |Typy klastrów | Opis |
|---|---|---|
|[Platforma analizy AtScale](https://aws.amazon.com/marketplace/pp/AtScale-AtScale-Intelligence-Platform/B07BWWHH18) |Hadoop |AtScale przekształca klaster usługi HDInsight w skalowalny w poziomie serwer OLAP, co umożliwia interakcyjne wykonywanie zapytań o miliardy wierszy danych przy użyciu narzędzi analizy biznesowej, które już znasz, własnych i miłości — od programu Microsoft Excel, Power BI, Tableau Software to QlikView. |
|[CDAP dla usługi HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/cask.cdap-for-hdinsight) |HBase |CDAP to pierwsza ujednolicona platforma integracji dla danych Big Data, która skraca czas do wartości dla usługi Hadoop i umożliwia udostępnienie danych samoobsługi. Polecenie Open Source i rozszerzalne, CDAP usuwa przeszkody dla innowacji. Wymagania: 4 regiony regionów, minimum D3 2. |
|[Datameer](https://azuremarketplace.microsoft.com/marketplace/apps/datameer.datameer) |Hadoop |Samoobsługowa platforma Datameer na potrzeby przygotowywania, eksplorowania i zarządzania danymi na potrzeby analiz przyspiesza przekształcanie złożonych danych z wielu źródeł w cenne informacje dostępne dla działalności firmy, dostarczając szybszy, inteligentniejszy wgląd w skalę przedsiębiorstwa. |
|[Dataiku DSS w usłudze HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/dataiku.dss-on-hdi) |Hadoop, Spark |Dataiku DSS na platformie do nauki o danych w przedsiębiorstwie, która umożliwia analitykom danych i specjalistom ds. danych współpracę w celu projektowania i uruchamiania nowych produktów i usług danych w bardziej wydajny sposób, dzięki czemu nieprzetworzone dane mają wpływ na przewidywania. |
|[Aplikacja WANdisco Fusion HDI](https://community.wandisco.com/s/article/Use-WANdisco-Fusion-for-parallel-operation-of-ADLS-Gen1-and-Gen2) |Hadoop, Spark, HBase, burza, Kafka |Utrzymywanie spójnych danych w środowisku rozproszonym jest wyzwaniem wielu operacji na danych. WANdisco Fusion, platforma oprogramowania klasy korporacyjnej, rozwiązuje ten problem przez włączenie niestrukturalnej spójności danych w dowolnym środowisku. |
|[H2O SparklingWater dla usługi HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/h2o-ai.h2o-sparklingwater) |platforma Spark |W przypadku wody musującej można używać następujących algorytmów rozproszonych: GLM, algorytmie Bayesa, rozproszony Las losowy, komputer do zwiększania gradientów, głębokie neuronowych sieci, uczenie głębokie, K-oznacza, PPW, uogólnione modele o niskiej rangi, wykrywanie anomalii, autokodery. |
|[Striim do integracji danych Real-Time z usługą HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/striim.striimbyol) |Hadoop, HBase, burza, Spark, Kafka |Striim (wymawiane "Stream") to kompleksowa integracja danych przesyłania strumieniowego i platforma analizy, umożliwiająca ciągłe pozyskiwanie, przetwarzanie i analizowanie różnorodnych strumieni danych. |
|[Jumbune Enterprise-Accelerating BigData Analytics](https://azuremarketplace.microsoft.com/marketplace/apps/impetus-infotech-india-pvt-ltd.impetus_jumbune) |Hadoop, Spark |Na wysokim poziomie Jumbune wspiera przedsiębiorstwa przez, 1. Przyspieszenie tez, & MapReduce, oparty na aparacie Spark, środowisko Java, Scala wydajność obciążeń. 2. Proaktywne monitorowanie klastra Hadoop, 3. Ustanawianie zarządzania jakością danych w rozproszonym systemie plików. |
|[Kyligence Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/kyligence.kyligence) |Hadoop, HBase, Spark |Obsługiwane przez Apache Kylin, usługa Kyligence Enterprise umożliwia korzystanie z analizy danych Big Data. Jako aparat OLAP przedsiębiorstwa na platformie Hadoop firma Kyligence Enterprise umożliwia analitykom biznesowym tworzenie architektów w usłudze Hadoop dzięki standardowym branżom danych i metodologiom analizy biznesowej. |
|[Gwiazda Presto dla usługi Azure HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/starburstdatainc1579800938563.starburst-presto?tab=Overview) |Hadoop |Presto to szybki i skalowalny, rozproszony aparat zapytań SQL. Zaprojektowane do rozdzielenia magazynu i obliczeń, Presto doskonale nadaje się do wykonywania zapytań dotyczących danych w Azure Data Lake Storage, Azure Blob Storage, SQL i NoSQL, a inne źródła danych. |
|[Moduł zbierający dane StreamSets dla chmury usługi HDInsight](https://azuremarketplace.microsoft.com/marketplace/apps/streamsets.streamsets-data-collector-hdinsight) |Hadoop, HBase, Spark, Kafka |Moduł zbierający dane StreamSets to lekki, zaawansowany aparat, który przesyła strumieniowo dane w czasie rzeczywistym. Użyj modułu zbierającego dane, aby kierować i przetwarzać dane w strumieniach danych. Jest on dostarczany z 30-dniową licencją próbną. |
|[Trifacta Wrangler Enterprise](https://azuremarketplace.microsoft.com/marketplace/apps/trifacta.trifacta-db?tab=Overview) |Hadoop, Spark, HBase |Usługa Trifacta Wrangler Enterprise for HDInsight obsługuje dane korporacyjne dla całego przedsiębiorstwa w przypadku dowolnej skali danych. Kosztem korzystania z Trifacta na platformie Azure jest kombinacja kosztów subskrypcji Trifacta plus koszty infrastruktury platformy Azure dla maszyn wirtualnych. |
|[Platforma danych Unifi](https://www.crunchbase.com/organization/unifi-software) |Hadoop, HBase, burza, Spark |Platforma danych Unifi to bezproblemowo zintegrowany pakiet narzędzi do samodzielnej obsługi danych, który umożliwia użytkownikom biznesowym sprostanie wyzwaniom związanym z danymi, które zwiększają zyski, obniżają koszty lub złożoność operacyjną. |
|[Unraveldata APM](https://azuremarketplace.microsoft.com/marketplace/apps/unravel-data.unravel-app) |platforma Spark |Aplikacja danych Unravel dla klastra usługi HDInsight Spark. |
|[AI-Drivena, Data Catalog](https://azuremarketplace.microsoft.com/marketplace/apps/waterline_data.waterline_data) |platforma Spark |Wykazy, organizują i zarządza danymi przy użyciu AI do autotagów danych z warunkami biznesowymi. W przypadku firmowych danych Literate Catalog jest krytycznym składnikiem sukcesu dla samoobsługowej analizy, zgodności i nadzoru oraz inicjatyw związanych z zarządzaniem IT. |

W instrukcjach podanych w tym artykule jest używana witryna Azure Portal. Możesz również wyeksportować szablon Azure Resource Manager z portalu lub uzyskać kopię szablonu Menedżer zasobów od dostawców, a następnie użyć Azure PowerShell oraz klasycznego interfejsu wiersza polecenia platformy Azure, aby wdrożyć szablon.  Zobacz [Tworzenie klastrów Apache Hadoop w usłudze HDInsight przy użyciu szablonów Menedżer zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md).

## <a name="prerequisites"></a>Wymagania wstępne
Jeśli chcesz zainstalować aplikacje usługi HDInsight w istniejącym klastrze usługi HDInsight, musisz dysponować klastrem usługi HDInsight. Aby go utworzyć, zobacz artykuł [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md). Możesz także zainstalować aplikacje usługi HDInsight podczas tworzenia klastra usługi HDInsight.

## <a name="install-applications-to-existing-clusters"></a>Instalowanie aplikacji w istniejących klastrach
Poniższa procedura pokazuje sposób instalacji aplikacji usługi HDInsight w istniejącym klastrze usługi HDInsight.

**Instalowanie aplikacji usługi HDInsight**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu po lewej stronie przejdź do strony **wszystkie usługi**  >  **analizy na platformie**  >  **HDInsight**.
3. Wybierz klaster usługi HDInsight z listy.  Jeśli nie masz klastra, musisz go najpierw utworzyć.  Zobacz [Tworzenie klastrów](hadoop/apache-hadoop-linux-tutorial-get-started.md).
4. W kategorii **Ustawienia** wybierz pozycję **aplikacje**. W oknie głównym można zobaczyć listę zainstalowanych aplikacji. 
   
    ![Aplikacje usługi HDInsight — menu portalu](./media/hdinsight-apps-install-applications/hdinsight-apps-portal-menu.png)
5. Wybierz pozycję **+ Dodaj** z menu. Zostanie wyświetlona lista dostępnych aplikacji.  Jeśli **dodatek + Add** jest wyszarzony, oznacza to, że nie ma żadnych aplikacji dla tej wersji klastra usługi HDInsight.
   
    ![Aplikacje usługi HDInsight — dostępne aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-list1.png)
6. Wybierz jedną z dostępnych aplikacji, a następnie postępuj zgodnie z instrukcjami, aby zaakceptować postanowienia prawne.

Możesz zobaczyć stan instalacji w powiadomieniach portalu (wybierz ikonę dzwonka w górnej części portalu). Po zainstalowaniu aplikacji aplikacja zostanie wyświetlona na liście zainstalowane aplikacje.

## <a name="install-applications-during-cluster-creation"></a>Instalowanie aplikacji podczas tworzenia klastra

Aplikacje usługi HDInsight możesz także zainstalować podczas tworzenia klastra. W trakcie procesu aplikacje usługi HDInsight są instalowane po utworzeniu i uruchomieniu klastra. Aby zainstalować aplikacje podczas tworzenia klastra przy użyciu Azure Portal, na karcie **Konfiguracja i Cennik** wybierz pozycję **+ Dodaj aplikację**.

![Azure Portal aplikacji konfiguracyjnych klastra](./media/hdinsight-apps-install-applications/azure-portal-cluster-configuration-applications.png)

## <a name="list-installed-hdinsight-apps-and-properties"></a>Lista zainstalowanych aplikacji HDInsight i ich właściwości
Portal zawiera listę zainstalowanych aplikacji usługi HDInsight dla klastra oraz właściwości poszczególnych zainstalowanych aplikacji.

**Wyświetlanie listy aplikacji i właściwości wyświetlania usługi HDInsight**

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
2. Z menu po lewej stronie przejdź do strony **wszystkie usługi**  >  **analizy na platformie**  >  **HDInsight**.
3. Wybierz klaster usługi HDInsight z listy.
4. W kategorii **Ustawienia** wybierz pozycję **aplikacje**. W oknie głównym można zobaczyć listę zainstalowanych aplikacji. 
   
    ![Aplikacje usługi HDInsight — zainstalowane aplikacje](./media/hdinsight-apps-install-applications/hdinsight-apps-installed-apps-with-apps.png)
5. Wybierz jedną z zainstalowanych aplikacji, aby wyświetlić właściwość. Lista właściwości:

    |Właściwość | Opis |
    |---|---|
    |Nazwa aplikacji |Nazwa aplikacji. |
    |Stan |Stan aplikacji. |
    |Stron |Adres URL aplikacji sieci Web wdrożonej w węźle brzegowym. Poświadczenie jest identyczne z poświadczeniami użytkownika protokołu HTTP skonfigurowanymi dla klastra. |
    |Punkt końcowy SSH |Do łączenia się z węzłem brzegowym można użyć protokołu SSH. Poświadczenia protokołu SSH są identyczne z poświadczeniami użytkownika protokołu SSH skonfigurowanymi dla klastra. Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md). |
    |Opis | Opis aplikacji. |

6. Aby usunąć aplikację, kliknij prawym przyciskiem myszy aplikację, a następnie kliknij polecenie **Usuń** z menu kontekstowego.

## <a name="connect-to-the-edge-node"></a>Łączenie z węzłem krawędzi
Z węzłem krawędzi możesz połączyć się przy użyciu protokołów HTTP i SSH. Informacje o punkcie końcowym można znaleźć w [portalu](#list-installed-hdinsight-apps-and-properties). Aby uzyskać informacje, zobacz [Używanie protokołu SSH w usłudze HDInsight](hdinsight-hadoop-linux-use-ssh-unix.md).

Poświadczenia punktu końcowego protokołu HTTP to poświadczenia użytkownika protokołu HTTP skonfigurowane dla klastra usługi HDInsight. Poświadczenia punktu końcowego protokołu SSH to poświadczenia SSH skonfigurowane dla klastra usługi HDInsight.

## <a name="troubleshoot"></a>Rozwiązywanie problemów
Zobacz [Troubleshoot the installation](hdinsight-apps-install-custom-applications.md#troubleshoot-the-installation) (Rozwiązywanie problemów z instalacją).

## <a name="next-steps"></a>Następne kroki
* [Instalowanie niestandardowych aplikacji usługi HDInsight](hdinsight-apps-install-custom-applications.md): Dowiedz się, jak wdrożyć nieopublikowaną aplikację usługi HDInsight w usłudze HDInsight.
* [Publikowanie aplikacji usługi HDInsight](hdinsight-apps-publish-applications.md): dowiedz się, jak opublikować niestandardowe aplikacje usługi HDInsight w portalu Azure Marketplace.
* [MSDN: instalowanie aplikacji usługi HDInsight](/rest/api/hdinsight/hdinsight-application): dowiedz się, jak zdefiniować aplikacje usługi HDInsight.
* [Dostosowywanie klastrów usługi HDInsight opartych na systemie Linux przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md): dowiedz się, jak instalować dodatkowe aplikacje za pomocą akcji skryptu.
* [Tworzenie klastrów Apache Hadoop opartych na systemie Linux w usłudze HDInsight przy użyciu szablonów Menedżer zasobów](hdinsight-hadoop-create-linux-clusters-arm-templates.md): informacje o sposobie wywoływania szablonów Menedżer zasobów do tworzenia klastrów usługi HDInsight.
* [Use empty edge nodes in HDInsight](hdinsight-apps-use-edge-node.md) (Używanie pustych węzłów krawędzi w usłudze HDInsight): dowiedz się, jak za pomocą pustego węzła krawędzi uzyskiwać dostęp do klastra usługi HDInsight oraz testować i hostować aplikacje usługi HDInsight.