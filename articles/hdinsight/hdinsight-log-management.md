---
title: Zarządzanie dziennikami klastra usługi HDInsight — Azure HDInsight
description: Określanie typów, rozmiarów i zasad przechowywania dla plików dziennika aktywności usługi HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 02/05/2020
ms.openlocfilehash: 95472d53045e23741286188da004eb649570a965
ms.sourcegitcommit: 3bcce2e26935f523226ea269f034e0d75aa6693a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/23/2020
ms.locfileid: "92487232"
---
# <a name="manage-logs-for-an-hdinsight-cluster"></a>Zarządzanie dziennikami klastra usługi HDInsight

Klaster HDInsight An tworzy wiele plików dziennika. Na przykład Apache Hadoop i powiązane usługi, takie jak Apache Spark, tworzą szczegółowe dzienniki wykonywania zadań. Zarządzanie plikami dziennika jest częścią konserwacji klastra usługi HDInsight w dobrej kondycji. Mogą również istnieć wymagania prawne dotyczące archiwizowania dzienników.  Ze względu na liczbę i rozmiar plików dziennika Optymalizacja magazynu dzienników i archiwizacji ułatwia zarządzanie kosztami usługi.

Zarządzanie dziennikami klastrów usługi HDInsight obejmuje zachowywanie informacji o wszystkich aspektach środowiska klastra. Te informacje obejmują wszystkie skojarzone dzienniki usług platformy Azure, konfigurację klastra, informacje o wykonywaniu zadań, wszystkie Stany błędów i inne dane, zgodnie z wymaganiami.

Typowymi krokami w zarządzaniu dziennikiem usługi HDInsight są:

* Krok 1. ustalenie zasad przechowywania dzienników
* Krok 2. Zarządzanie dziennikami konfiguracji wersji usługi klastrowania
* Krok 3. zarządzanie plikami dziennika wykonywania zadań klastra
* Krok 4: wielkość magazynu i koszty przechowywania w dzienniku prognozy
* Krok 5. ustalenie zasad i procesów archiwum dzienników

## <a name="step-1-determine-log-retention-policies"></a>Krok 1. ustalenie zasad przechowywania dzienników

Pierwszym krokiem tworzenia strategii zarządzania dziennikami klastrów usługi HDInsight jest zbieranie informacji o scenariuszach biznesowych i wymaganiach dotyczących magazynu historii wykonywania zadań.

### <a name="cluster-details"></a>Szczegóły klastra

Poniższe szczegóły dotyczące klastra są przydatne w celu zebrania informacji w strategii zarządzania dziennikami. Zbierz te informacje ze wszystkich klastrów usługi HDInsight utworzonych w ramach określonego konta platformy Azure.

* Nazwa klastra
* Region klastra i strefa dostępności platformy Azure
* Stan klastra, w tym szczegóły ostatniej zmiany stanu
* Typ i liczba wystąpień usługi HDInsight określonych dla węzłów głównych, podstawowych i zadań

Większość tych informacji najwyższego poziomu można uzyskać przy użyciu Azure Portal.  Alternatywnie możesz użyć [interfejsu wiersza polecenia platformy Azure](/cli/azure/) , aby uzyskać informacje o klastrach usługi HDInsight:

```azurecli
az hdinsight list --resource-group <ResourceGroup>
az hdinsight show --resource-group <ResourceGroup> --name <ClusterName>
```

Możesz również użyć programu PowerShell, aby wyświetlić te informacje.  Aby uzyskać więcej informacji, zobacz artykuł [Apache Manage Hadoop w usłudze HDInsight przy użyciu Azure PowerShell](hdinsight-administer-use-powershell.md).

### <a name="understand-the-workloads-running-on-your-clusters"></a>Zrozumienie obciążeń uruchomionych w klastrach

Ważne jest, aby zrozumieć typy obciążeń działające w klastrach usługi HDInsight w celu zaprojektowania odpowiednich strategii rejestrowania dla każdego typu.

* Czy obciążenia są eksperymentalne (takie jak programowanie czy testowanie) czy jakość produkcji?
* Jak często są wykonywane obciążenia z jakością produkcyjną?
* Czy jakieś obciążenia są czasochłonne i/lub długotrwałe?
* Czy dowolne z obciążeń używa złożonego zestawu usług Hadoop, dla którego tworzone są wiele typów dzienników?
* Czy każde z obciążeń ma powiązane wymagania związane z wykonaniem przepisów prawnych?

### <a name="example-log-retention-patterns-and-practices"></a>Przykładowe wzorce i praktyki przechowywania dzienników

* Należy rozważyć utrzymywanie śledzenia elementu danych, dodając identyfikator do każdego wpisu dziennika lub przez inne techniki. Pozwala to na śledzenie pierwotnego źródła danych i operacji oraz wykonywanie danych na każdym etapie w celu zrozumienia jego spójności i ważności.

* Zastanów się nad tym, jak zbierać dzienniki z klastra lub z więcej niż jednego klastra, a następnie posortować je do celów, takich jak Inspekcja, monitorowanie, planowanie i alerty. Możesz użyć niestandardowego rozwiązania, aby regularnie uzyskiwać dostęp do plików dziennika i je pobierać, a następnie łączyć i analizować je w celu udostępnienia ekranu pulpitu nawigacyjnego. Możesz również dodać dodatkowe możliwości w celu uzyskania alertów dotyczących zabezpieczeń lub wykrywania niepowodzeń. Możesz tworzyć te narzędzia przy użyciu programu PowerShell, zestawów SDK usługi HDInsight lub kodu, który uzyskuje dostęp do klasycznego modelu wdrażania platformy Azure.

* Rozważ, czy rozwiązanie lub usługa monitorowania będzie przydatna. Program Microsoft System Center udostępnia [pakiet administracyjny usługi HDInsight](https://systemcenter.wiki/?Get_ManagementPackBundle=Microsoft.HDInsight.mpb&FileMD5=10C7D975C6096FFAA22C84626D211259). Do zbierania i scentralizowania dzienników można także używać narzędzi innych firm, takich jak Apache Chukwa i Ganglia. Wiele firm oferuje usługi do monitorowania rozwiązań danych Big Data opartych na usłudze Hadoop, na przykład: Center, Compuware APM, Sematext SPM i Zettaset Orchestrator.

## <a name="step-2-manage-cluster-service-versions-and-view-logs"></a>Krok 2. Zarządzanie wersjami usługi klastrowania i wyświetlanie dzienników

Typowy klaster usługi HDInsight używa kilku usług i pakietów oprogramowania typu Open Source (takich jak Apache HBase, Apache Spark itd.). W przypadku niektórych obciążeń, takich jak Bioinformatics, może być konieczne zachowanie historii dziennika konfiguracji usługi oprócz dzienników wykonywania zadań.

### <a name="view-cluster-configuration-settings-with-the-ambari-ui"></a>Wyświetlanie ustawień konfiguracji klastra za pomocą interfejsu użytkownika Ambari

Apache Ambari upraszcza zarządzanie, konfigurację i monitorowanie klastra usługi HDInsight przez udostępnienie internetowego interfejsu użytkownika i interfejsu API REST. Usługa Ambari jest dołączana do klastrów usługi HDInsight opartych na systemie Linux. Wybierz okienko **pulpit nawigacyjny klastra** na stronie Azure Portal HDInsight, aby otworzyć stronę łącza **pulpity nawigacyjne klastra** .  Następnie wybierz okienko **pulpit nawigacyjny klastra usługi HDInsight** , aby otworzyć interfejs użytkownika Ambari.  Zostanie wyświetlony monit o podanie poświadczeń logowania do klastra.

Aby otworzyć listę widoków usług, wybierz okienko **widoki Ambari** na stronie Azure portal usługi HDInsight.  Ta lista różni się w zależności od zainstalowanych bibliotek.  Na przykład można zobaczyć Menedżera kolejki PRZĘDZy, widok Hive i widok tez.  Wybierz łącze do usługi, aby wyświetlić informacje o konfiguracji i usłudze.  Na stronie **stos i wersja** interfejsu użytkownika Ambari dostępne są informacje na temat konfiguracji usługi klastra i historii wersji usługi. Aby przejść do tej sekcji interfejsu użytkownika Ambari, wybierz menu **administrator** , a następnie kliknij **stosy i wersje**.  Wybierz kartę **wersje** , aby wyświetlić informacje o wersji usługi.

![Stos i wersje administratora oprogramowania Apache Ambari](./media/hdinsight-log-management/ambari-stack-versions.png)

Za pomocą interfejsu użytkownika Ambari można pobrać konfigurację dla dowolnych (lub wszystkich) usług działających na określonym hoście (lub węźle) w klastrze.  Wybierz menu **hosty** , a następnie link do hosta zainteresowania. Na stronie tego hosta wybierz przycisk **Akcje hosta** , a następnie **Pobierz konfiguracji klienta**.

![Konfiguracja klienta hosta pobierania oprogramowania Apache Ambari](./media/hdinsight-log-management/download-client-configs.png)

### <a name="view-the-script-action-logs"></a>Wyświetlanie dzienników akcji skryptu

[Akcje skryptu](hdinsight-hadoop-customize-cluster-linux.md) usługi HDInsight uruchamiają skrypty w klastrze ręcznie lub po ich określeniu. Na przykład akcje skryptu mogą służyć do instalowania dodatkowego oprogramowania w klastrze lub do zmiany ustawień konfiguracji z wartości domyślnych. Dzienniki akcji skryptu mogą zapewniać wgląd w błędy, które wystąpiły podczas instalacji klastra, a także zmiany ustawień konfiguracji, które mogą mieć wpływ na wydajność i dostępność klastra.  Aby wyświetlić stan akcji skryptu, wybierz przycisk **Ops** w interfejsie użytkownika Ambari lub uzyskaj dostęp do dzienników stanu na domyślnym koncie magazynu. Dzienniki magazynu są dostępne pod adresem `/STORAGE_ACCOUNT_NAME/DEFAULT_CONTAINER_NAME/custom-scriptaction-logs/CLUSTER_NAME/DATE` .

### <a name="view-ambari-alerts-status-logs"></a>Wyświetlanie dzienników stanu alertów Ambari

Usługa Apache Ambari zapisuje zmiany stanu alertu `ambari-alerts.log` . Pełna ścieżka to `/var/log/ambari-server/ambari-alerts.log` . Aby włączyć debugowanie dziennika, Zmień właściwość w `/etc/ambari-server/conf/log4j.properties.` pozycji Zmień, a następnie pozycję `# Log alert state changes` od:

```
log4j.logger.alerts=INFO,alerts

to

log4j.logger.alerts=DEBUG,alerts
```

## <a name="step-3-manage-the-cluster-job-execution-log-files"></a>Krok 3. zarządzanie plikami dziennika wykonywania zadań klastra

Następnym krokiem jest przejrzenie plików dziennika wykonywania zadań dla różnych usług.  Usługi mogą obejmować Apache HBase, Apache Spark i wiele innych. Klaster Hadoop tworzy dużą liczbę pełnych dzienników, dzięki czemu można określić, które dzienniki są przydatne (i które nie są).  Zrozumienie systemu rejestrowania jest ważne w przypadku kierowania zarządzania plikami dziennika.  Poniższy obraz przedstawia przykładowy plik dziennika.

![Przykładowe dane wyjściowe przykładowego pliku dziennika usługi HDInsight](./media/hdinsight-log-management/hdi-log-file-example.png)

### <a name="access-the-hadoop-log-files"></a>Dostęp do plików dziennika usługi Hadoop

Pliki dzienników usługi HDInsight są przechowywane zarówno w systemie plików klastra, jak i w usłudze Azure Storage. Pliki dziennika można przeanalizować w klastrze, otwierając połączenie [SSH](hdinsight-hadoop-linux-use-ssh-unix.md) z klastrem i przeglądając system plików lub korzystając z portalu stanu przędzy usługi Hadoop na zdalnym serwerze węzła głównego. Pliki dziennika można przeanalizować w usłudze Azure Storage przy użyciu dowolnych narzędzi, które mogą uzyskiwać dostęp do danych z usługi Azure Storage i je pobierać. Przykłady to [AzCopy](../storage/common/storage-use-azcopy.md), [CloudXplorer](https://clumsyleaf.com/products/cloudxplorer)i Visual Studio Eksplorator serwera. Aby uzyskać dostęp do danych w usłudze Azure Blob Storage, można również użyć programu PowerShell i bibliotek klienckich usługi Azure Storage lub zestawów SDK platformy Azure.

Usługa Hadoop uruchamia zadania w trakcie wykonywania *zadań* w różnych węzłach w klastrze. Usługa HDInsight może inicjować podejmowanie spekulacyjnych prób zadań, kończąc wszystkie inne nieudane próby wykonania zadań. Spowoduje to wygenerowanie znaczącego działania, które jest rejestrowane w plikach dziennika kontrolera, stderr i dziennik systemowy na bieżąco. Ponadto jednocześnie jest uruchomionych wiele prób wykonywania zadań, ale w pliku dziennika mogą być wyświetlane tylko wyniki liniowe.

#### <a name="hdinsight-logs-written-to-azure-blob-storage"></a>Dzienniki usługi HDInsight zapisane w usłudze Azure Blob Storage

Klastry HDInsight są skonfigurowane do zapisywania dzienników zadań na koncie usługi Azure Blob Storage dla każdego zadania przesyłanego za pomocą poleceń cmdlet Azure PowerShell lub interfejsów API przesyłania zadań programu .NET.  W przypadku przesyłania zadań za pośrednictwem protokołu SSH do klastra informacje o rejestrowaniu wykonywania są przechowywane w tabelach platformy Azure zgodnie z opisem w poprzedniej sekcji.

Oprócz podstawowych plików dziennika wygenerowanych przez usługę HDInsight zainstalowane usługi, takie jak PRZĘDZa, generują również pliki dziennika wykonywania zadań.  Liczba i typ plików dziennika zależy od zainstalowanych usług.  Typowe usługi to Apache HBase, Apache Spark i tak dalej.  Zbadaj pliki wykonywania dziennika zadań dla każdej usługi, aby poznać ogólne pliki rejestrowania dostępne w klastrze.  Każda usługa ma własne unikalne metody rejestrowania i lokalizacji do przechowywania plików dziennika.  Przykładowo szczegółowe informacje dotyczące uzyskiwania dostępu do najpopularniejszych plików dziennika usług (z PRZĘDZki) zostały omówione w poniższej sekcji.

### <a name="hdinsight-logs-generated-by-yarn"></a>Dzienniki usługi HDInsight generowane przez PRZĘDZę

PRZĘDZa agreguje dzienniki we wszystkich kontenerach w węźle procesu roboczego i przechowuje te dzienniki jako jeden zagregowany plik dziennika na węzeł procesu roboczego. Ten dziennik jest przechowywany w domyślnym systemie plików po zakończeniu aplikacji. Twoja aplikacja może korzystać z setek lub tysięcy kontenerów, ale dzienniki dla wszystkich kontenerów, które są uruchamiane w jednym węźle procesu roboczego, są zawsze agregowane do pojedynczego pliku. Istnieje tylko jeden dziennik na węzeł procesu roboczego używany przez aplikację. Agregacja dzienników jest domyślnie włączona w klastrach usługi HDInsight w wersji 3,0 i nowszych. Zagregowane dzienniki znajdują się w domyślnym magazynie klastra.

```
/app-logs/<user>/logs/<applicationId>
```

Zagregowane dzienniki nie są odczytywane bezpośrednio, ponieważ są zapisywane w formacie binarnym TFile indeksowanym przez kontener. Użyj dzienników ResourceManager lub narzędzi interfejsu wiersza polecenia, aby wyświetlić te dzienniki jako zwykły tekst dla aplikacji lub kontenerów zainteresowania.

#### <a name="yarn-cli-tools"></a>Narzędzia interfejsu wiersza polecenia dla PRZĘDZy

Aby korzystać z narzędzi interfejsu wiersza polecenia, należy najpierw połączyć się z klastrem usługi HDInsight przy użyciu protokołu SSH. Określ `<applicationId>` , `<user-who-started-the-application>` , `<containerId>` , i `<worker-node-address>` informacje podczas uruchamiania tych poleceń. Dzienniki można wyświetlić jako zwykły tekst przy użyciu jednego z następujących poleceń:

```bash
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application>
yarn logs -applicationId <applicationId> -appOwner <user-who-started-the-application> -containerId <containerId> -nodeAddress <worker-node-address>
```

#### <a name="yarn-resourcemanager-ui"></a>Interfejs użytkownika narzędzia ResourceManager

Interfejs użytkownika programu ResourceManager jest uruchamiany w węźle głównym klastra i jest dostępny za pomocą interfejsu użytkownika sieci Web Ambari. Aby wyświetlić dzienniki PRZĘDZy, wykonaj następujące kroki:

1. W przeglądarce internetowej przejdź do adresu `https://CLUSTERNAME.azurehdinsight.net`. Zastąp CLUSTERNAME nazwą klastra usługi HDInsight:
2. Z listy usług po lewej stronie wybierz pozycję PRZĘDZa.
3. Z listy rozwijanej szybkie linki wybierz jeden z węzłów głównych klastra, a następnie wybierz pozycję **dzienniki zasobów źródłowych**. Zostanie wyświetlona lista linków do dzienników PRZĘDZy.

## <a name="step-4-forecast-log-volume-storage-sizes-and-costs"></a>Krok 4: wielkość magazynu i koszty przechowywania w dzienniku prognozy

Po wykonaniu poprzednich kroków można zrozumieć typy i woluminy plików dziennika, które są wytwarzane przez klastry usługi HDInsight.

Następnie Analizuj wolumin danych dziennika w lokalizacjach magazynu dzienników kluczy w danym okresie czasu. Na przykład można analizować wolumin i wzrost ponad 30-60-90 dni.  Zapisz te informacje w arkuszu kalkulacyjnym lub użyj innych narzędzi, takich jak Visual Studio, Eksplorator usługi Azure Storage lub Power Query dla programu Excel. Aby uzyskać więcej informacji, zobacz [Analizowanie dzienników usługi HDInsight](hdinsight-debug-jobs.md).  

Masz teraz wystarczającą ilość informacji, aby utworzyć strategię zarządzania dziennikami dla dzienników kluczy.  Użyj swojego arkusza kalkulacyjnego (lub wybranego narzędzia) do prognozowania zarówno wzrostu rozmiaru dziennika, jak i magazynu dzienników usługi Azure.  Należy również wziąć pod uwagę wszystkie wymagania dotyczące przechowywania dzienników dla zestawu dzienników, które są rozpatrywane.  Teraz można dokonać ponownej prognozy kosztów magazynu dzienników po określeniu, które pliki dziennika mogą zostać usunięte (jeśli istnieją) i które dzienniki należy zachować i zarchiwizować w tańszej usłudze Azure Storage.

## <a name="step-5-determine-log-archive-policies-and-processes"></a>Krok 5. ustalenie zasad i procesów archiwum dzienników

Po określeniu, które pliki dzienników można usunąć, można dostosować parametry rejestrowania dla wielu usług Hadoop w celu automatycznego usuwania plików dziennika po upływie określonego czasu.

W przypadku niektórych plików dziennika można użyć metody archiwizowania plików dziennika o niższej cenie. W przypadku Azure Resource Manager dzienników aktywności można eksplorować takie podejście przy użyciu Azure Portal.  Skonfiguruj archiwizowanie dzienników Menedżer zasobów, wybierając łącze **dziennika aktywności** w Azure Portal wystąpienia usługi HDInsight.  W górnej części strony Wyszukiwanie w dzienniku aktywności wybierz element menu **Eksportuj** , aby otworzyć okienko **Eksportuj dziennik aktywności** .  Wypełnij pola subskrypcja, region, czy chcesz eksportować do konta magazynu, a także liczbę dni przechowywania dzienników. W tym samym okienku można także wskazać, czy wyeksportować do centrum zdarzeń.

![Azure Portal eksportowanie dziennika aktywności](./media/hdinsight-log-management/hdi-export-log-files.png)

Alternatywnie można archiwizowanie dzienników skryptów przy użyciu programu PowerShell.  Przykładowy skrypt programu PowerShell znajduje się w temacie [Archiwizowanie dzienników Azure Automation na platformie Azure Blob Storage](https://gallery.technet.microsoft.com/scriptcenter/Archive-Azure-Automation-898a1aa8).

### <a name="accessing-azure-storage-metrics"></a>Uzyskiwanie dostępu do metryk usługi Azure Storage

Usługę Azure Storage można skonfigurować do przechowywania operacji magazynu i dostępu. Można używać tych bardzo szczegółowych dzienników do monitorowania i planowania pojemności oraz do inspekcji żądań do magazynu. Zarejestrowane informacje obejmują szczegóły opóźnienia, umożliwiając monitorowanie i dostosowywanie wydajności rozwiązań.
Zestawu .NET SDK dla usługi Hadoop można użyć do sprawdzenia plików dziennika wygenerowanych dla usługi Azure Storage, które przechowują dane dla klastra HDInsight.

### <a name="control-the-size-and-number-of-backup-indexes-for-old-log-files"></a>Kontroluj rozmiar i liczbę indeksów kopii zapasowych dla starych plików dziennika

Aby kontrolować rozmiar i liczbę przechowywanych plików dziennika, ustaw następujące właściwości `RollingFileAppender` :

* `maxFileSize` jest krytycznym rozmiarem pliku, powyżej którego plik jest rzutowany. Wartość domyślna to 10 MB.
* `maxBackupIndex` Określa liczbę plików kopii zapasowej, które mają zostać utworzone; wartość domyślna to 1.

### <a name="other-log-management-techniques"></a>Inne techniki zarządzania dziennikami

Aby uniknąć braku miejsca na dysku, można użyć niektórych narzędzi systemu operacyjnego, takich jak [logrotate](https://linux.die.net/man/8/logrotate) , do zarządzania obsługą plików dziennika. Można skonfigurować `logrotate` do codziennego uruchamiania, kompresowania plików dziennika i usuwania starych. Twoje podejście zależy od wymagań, takich jak czas przechowywania plików dziennika w węzłach lokalnych.  

Możesz również sprawdzić, czy rejestrowanie debugowania jest włączone dla jednej lub kilku usług, co znacznie zwiększa rozmiar dziennika wyjściowego.  

Aby zebrać dzienniki ze wszystkich węzłów do jednej centralnej lokalizacji, można utworzyć przepływ danych, taki jak pozyskiwanie wszystkich wpisów dziennika do Solr.

## <a name="next-steps"></a>Następne kroki

* [Rozwiązanie do monitorowania i rejestrowania w usłudze HDInsight](https://msdn.microsoft.com/library/dn749790.aspx)
* [Dostęp do Apache Hadoop dzienników aplikacji PRZĘDZy w usłudze HDInsight opartej na systemie Linux](hdinsight-hadoop-access-yarn-app-logs-linux.md)
* [Jak kontrolować rozmiar plików dziennika dla różnych składników Apache Hadoop](https://community.hortonworks.com/articles/8882/how-to-control-size-of-log-files-for-various-hdp-c.html)
