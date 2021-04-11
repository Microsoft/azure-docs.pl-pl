---
title: Usługa Azure HDInsight — często zadawane pytania
description: Często zadawane pytania dotyczące usługi HDInsight
keywords: Często zadawane pytania
author: Ramakoni1
ms.author: ramakoni
ms.service: hdinsight
ms.custom: hdinsightactive,seoapr2020
ms.topic: conceptual
ms.date: 11/20/2019
ms.openlocfilehash: 47f0ea34fa650371d39252ce6b0ee6a15f3124d6
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "104871184"
---
# <a name="azure-hdinsight-frequently-asked-questions"></a>Azure HDInsight: Często zadawane pytania

Ten artykuł zawiera odpowiedzi na niektóre często zadawane pytania dotyczące uruchamiania [usługi Azure HDInsight](https://azure.microsoft.com/services/hdinsight/).

## <a name="creating-or-deleting-hdinsight-clusters"></a>Tworzenie lub usuwanie klastrów usługi HDInsight

### <a name="how-do-i-provision-an-hdinsight-cluster"></a>Jak mogę zainicjować obsługę administracyjną klastra usługi HDInsight?

Aby przejrzeć typy klastrów usługi HDInsight i metody udostępniania, zobacz [Konfigurowanie klastrów w usłudze HDInsight przy użyciu Apache Hadoop, Apache Spark, Apache Kafka i innych](./hdinsight-hadoop-provision-linux-clusters.md).

### <a name="how-do-i-delete-an-existing-hdinsight-cluster"></a>Jak mogę usunąć istniejący klaster usługi HDInsight?

Aby dowiedzieć się więcej na temat usuwania klastra, gdy nie jest już używany, zobacz [usuwanie klastra usługi HDInsight](hdinsight-delete-cluster.md).

Spróbuj pozostawić co najmniej 30 do 60 minut między operacjami tworzenia i usuwania. W przeciwnym razie operacja może zakończyć się niepowodzeniem z następującym komunikatem o błędzie:

``Conflict (HTTP Status Code: 409) error when attempting to delete a cluster immediately after creation of a cluster. If you encounter this error, wait until the newly created cluster is in operational state before attempting to delete it.``

### <a name="how-do-i-select-the-correct-number-of-cores-or-nodes-for-my-workload"></a>Jak mogę wybrać poprawną liczbę rdzeni lub węzłów dla mojego obciążenia?

Odpowiednia liczba rdzeni i inne opcje konfiguracji zależą od różnych czynników.

Aby uzyskać więcej informacji, zobacz [Planowanie pojemności klastrów usługi HDInsight](./hdinsight-capacity-planning.md).

### <a name="what-are-the-various-types-of-nodes-in-an-hdinsight-cluster"></a>Jakie są różne typy węzłów w klastrze usługi HDInsight?

Zobacz [typy zasobów w klastrach usługi Azure HDInsight](hdinsight-virtual-network-architecture.md#resource-types-in-azure-hdinsight-clusters).

### <a name="what-are-the-best-practices-for-creating-large-hdinsight-clusters"></a>Jakie są najlepsze rozwiązania dotyczące tworzenia dużych klastrów usługi HDInsight?

1. Zalecamy skonfigurowanie klastrów usługi HDInsight za pomocą [niestandardowej bazy danych Ambari](./hdinsight-custom-ambari-db.md) w celu zwiększenia skalowalności klastra.
2. Użyj [Azure Data Lake Storage Gen2](./hdinsight-hadoop-use-data-lake-storage-gen2.md) , aby utworzyć klastry usługi HDInsight, aby wykorzystać wyższą przepustowość i inne cechy wydajności Azure Data Lake Storage Gen2.
3. Węzłów głównych powinny być wystarczająco duże, aby zapewnić obsługę wielu usług głównych uruchomionych w tych węzłach.
4. Niektóre określone obciążenia, takie jak zapytanie interaktywne, również będą potrzebować większych węzłów dozorcy. Należy rozważyć minimalnie 8 podstawowych maszyn wirtualnych.
5. W przypadku gałęzi Hive i Spark Użyj [magazyn metadanych Hive zewnętrznych](./hdinsight-use-external-metadata-stores.md).

## <a name="individual-components"></a>Pojedyncze składniki

### <a name="can-i-install-additional-components-on-my-cluster"></a>Czy mogę zainstalować dodatkowe składniki w klastrze?

Tak. Aby zainstalować dodatkowe składniki lub dostosować konfigurację klastra, użyj:

- Skrypty podczas tworzenia lub po nim. Skrypty są wywoływane za pośrednictwem [akcji skryptu](./hdinsight-hadoop-customize-cluster-linux.md). Akcja skryptu to opcja konfiguracji, której można użyć z Azure Portal, poleceń cmdlet programu Windows PowerShell usługi HDInsight lub zestawu .NET SDK usługi HDInsight. Tej opcji konfiguracji można użyć z Azure Portal, poleceń cmdlet programu Windows PowerShell usługi HDInsight lub zestawu .NET SDK usługi HDInsight.

- [Platforma aplikacji HDInsight](https://azure.microsoft.com/services/hdinsight/partner-ecosystem/) do instalowania aplikacji.

Aby uzyskać listę obsługiwanych składników, zobacz [co to są składniki Apache Hadoop i wersje dostępne w usłudze HDInsight?](./hdinsight-component-versioning.md)

### <a name="can-i-upgrade-the-individual-components-that-are-pre-installed-on-the-cluster"></a>Czy mogę uaktualnić poszczególne składniki, które są wstępnie zainstalowane w klastrze?

Jeśli uaktualniasz wbudowane składniki lub aplikacje, które są wstępnie zainstalowane w klastrze, powstała konfiguracja nie będzie obsługiwana przez firmę Microsoft. Te konfiguracje systemu nie zostały przetestowane przez firmę Microsoft. Spróbuj użyć innej wersji klastra usługi HDInsight, która może mieć już zainstalowaną uaktualnioną wersję składnika.

Na przykład uaktualnienie programu Hive jako pojedynczego składnika nie jest obsługiwane. HDInsight jest usługą zarządzaną, a wiele usług jest zintegrowanych z serwerem Ambari i testowany. Samodzielna aktualizacja programu Hive spowoduje zmianę indeksowanych plików binarnych innych składników i spowoduje problemy z integracją składników w klastrze.

### <a name="can-spark-and-kafka-run-on-the-same-hdinsight-cluster"></a>Czy platforma Spark i Kafka działają w tym samym klastrze usługi HDInsight?

Nie, nie można uruchamiać Apache Kafka i Apache Spark w tym samym klastrze usługi HDInsight. Utwórz oddzielne klastry dla Kafka i Spark, aby uniknąć problemów z rywalizacją o zasoby.

### <a name="how-do-i-change-timezone-in-ambari"></a>Jak mogę zmienić strefę czasową w Ambari?

1. Otwórz interfejs użytkownika sieci Web Ambari w lokalizacji `https://CLUSTERNAME.azurehdinsight.net` , gdzie ClusterName jest nazwą klastra.
2. W prawym górnym rogu wybierz pozycję Administrator | Ustawienia. 

   :::image type="content" source="media/hdinsight-faq/ambari-settings.png" alt-text="Ustawienia Ambari":::

3. W oknie Ustawienia użytkownika wybierz nową strefę czasową z listy rozwijanej strefa czasowa, a następnie kliknij przycisk Zapisz.

   :::image type="content" source="media/hdinsight-faq/ambari-user-settings.png" alt-text="Ambari ustawienia użytkownika":::

## <a name="metastore"></a>Magazyn metadanych

### <a name="how-can-i-migrate-from-the-existing-metastore-to-azure-sql-database"></a>Jak przeprowadzić migrację z istniejącego magazynu metadanych do Azure SQL Database? 

Aby przeprowadzić migrację z SQL Server do Azure SQL Database, zobacz [Samouczek: migrowanie SQL Server do pojedynczej bazy danych lub bazy danych w puli w Azure SQL Database offline przy użyciu usługi DMS](../dms/tutorial-sql-server-to-azure-sql.md).

### <a name="is-the-hive-metastore-deleted-when-the-cluster-is-deleted"></a>Czy magazyn metadanych Hive został usunięty po usunięciu klastra?

Jest to zależne od typu magazynu metadanych, który jest skonfigurowany przez klaster.

Dla domyślnego magazynu metadanych: domyślny magazyn metadanych jest częścią cyklu życia klastra. Po usunięciu klastra są również usuwane odpowiednie magazyny i metadane.

W przypadku niestandardowego magazynu metadanych: cykl życia magazynu metadanych nie jest powiązany z cyklem życia klastra. Dzięki temu można tworzyć i usuwać klastry bez utraty metadanych. Metadane, takie jak schematy Hive, są utrwalane nawet po usunięciu i ponownym utworzeniu klastra usługi HDInsight.

Aby uzyskać więcej informacji, zobacz [Korzystanie z zewnętrznych magazynów metadanych w usłudze Azure HDInsight](hdinsight-use-external-metadata-stores.md).

### <a name="does-migrating-a-hive-metastore-also-migrate-the-default-policies-of-the-ranger-database"></a>Czy migracja magazyn metadanych Hive również migruje domyślne zasady bazy danych Ranger?

Nie, definicja zasad znajduje się w bazie danych Ranger, dlatego Migrowanie jej zasad zostanie zmigrowane przez bazę danych Ranger.

### <a name="can-you-migrate-a-hive-metastore-from-an-enterprise-security-package-esp-cluster-to-a-non-esp-cluster-and-the-other-way-around"></a>Czy można migrować magazyn metadanych Hive z klastra pakiet Enterprise Security (ESP) do klastra non-ESP i w inny sposób?

Tak, można migrować magazyn metadanych Hive z partycji ESP do klastra innego niż ESP.

### <a name="how-can-i-estimate-the-size-of-a-hive-metastore-database"></a>Jak oszacować rozmiar bazy danych magazyn metadanych Hive?

Magazyn metadanych Hive służy do przechowywania metadanych dla źródeł danych, które są używane przez serwer programu Hive. Wymagania dotyczące rozmiaru są zależne od liczby i złożoności źródeł danych programu Hive. Te elementy nie mogą być szacowane na początku. Jak opisano w [magazyn metadanych Hive wytycznych](hdinsight-use-external-metadata-stores.md#hive-metastore-guidelines), można rozpocząć od warstwy S2. Warstwa zapewnia 50 jednostek DTU i 250 GB miejsca w magazynie, a jeśli widzisz wąskie gardła, Skaluj w górę bazy danych.

### <a name="do-you-support-any-other-database-other-than-azure-sql-database-as-an-external-metastore"></a>Czy są obsługiwane inne bazy danych inne niż Azure SQL Database jako zewnętrzny magazyn metadanych?

Nie, firma Microsoft obsługuje tylko Azure SQL Database jako zewnętrzny niestandardowy magazyn metadanych.

### <a name="can-i-share-a-metastore-across-multiple-clusters"></a>Czy mogę udostępnić magazyn metadanych w wielu klastrach?

Tak, możesz udostępnić niestandardowy magazyn metadanych w wielu klastrach, o ile są one używane w tej samej wersji usługi HDInsight.

## <a name="connectivity-and-virtual-networks"></a>Łączność i sieci wirtualne  

### <a name="what-are-the-implications-of-blocking-ports-22-and-23-on-my-network"></a>Jakie są implikacje blokowania portów 22 i 23 w mojej sieci?

Jeśli zablokujesz porty 22 i 23, nie będziesz mieć dostępu SSH do klastra. Te porty nie są używane przez usługę HDInsight.

Więcej informacji można znaleźć w następujących dokumentach:

- [Porty używane przez usługi Apache Hadoop w usłudze HDInsight](./hdinsight-hadoop-port-settings-for-services.md)

- [Zabezpieczanie ruchu przychodzącego do klastrów usługi HDInsight w sieci wirtualnej za pomocą prywatnego punktu końcowego](https://azure.microsoft.com/blog/secure-incoming-traffic-to-hdinsight-clusters-in-a-vnet-with-private-endpoint/)

- [Adresy IP zarządzania usługą HDInsight](./hdinsight-management-ip-addresses.md)

### <a name="can-i-deploy-an-additional-virtual-machine-within-the-same-subnet-as-an-hdinsight-cluster"></a>Czy mogę wdrożyć dodatkową maszynę wirtualną w tej samej podsieci co klaster usługi HDInsight?

Tak, można wdrożyć dodatkową maszynę wirtualną w tej samej podsieci, w której znajduje się klaster usługi HDInsight. Możliwe są następujące konfiguracje:

- Węzły brzegowe: można dodać kolejny węzeł krawędzi do klastra, zgodnie z opisem w temacie [używanie pustych węzłów brzegowych w klastrach Apache Hadoop w usłudze HDInsight](hdinsight-apps-use-edge-node.md).

- Węzły autonomiczne: można dodać autonomiczną maszynę wirtualną do tej samej podsieci i uzyskać dostęp do klastra z tej maszyny wirtualnej przy użyciu prywatnego punktu końcowego `https://<CLUSTERNAME>-int.azurehdinsight.net` . Aby uzyskać więcej informacji, zobacz [sterowanie ruchem sieciowym](./control-network-traffic.md).

### <a name="should-i-store-data-on-the-local-disk-of-an-edge-node"></a>Czy należy przechowywać dane na dysku lokalnym węzła krawędzi?

Nie, przechowywanie danych na dysku lokalnym nie jest dobrym pomysłem. Jeśli węzeł ulegnie awarii, wszystkie dane przechowywane lokalnie zostaną utracone. Zalecamy przechowywanie danych w usłudze Azure Data Lake Storage Gen2 lub Azure Blob Storage albo zainstalowanie udziału Azure Files do przechowywania danych.


### <a name="can-i-add-an-existing-hdinsight-cluster-to-another-virtual-network"></a>Czy mogę dodać istniejący klaster usługi HDInsight do innej sieci wirtualnej?

Nie. Sieć wirtualną należy określić w chwili aprowizacji. Jeśli podczas aprowizacji nie zostanie określona żadna Sieć wirtualna, wdrożenie spowoduje utworzenie sieci wewnętrznej, która nie jest dostępna z zewnątrz. Aby uzyskać więcej informacji, zobacz [Dodawanie usługi HDInsight do istniejącej sieci wirtualnej](hdinsight-plan-virtual-network-deployment.md#existingvnet).

## <a name="security-and-certificates"></a>Zabezpieczenia i certyfikaty

### <a name="what-are-the-recommendations-for-malware-protection-on-azure-hdinsight-clusters"></a>Jakie są zalecenia dotyczące ochrony przed złośliwym oprogramowaniem w klastrach usługi Azure HDInsight?

Aby uzyskać informacje na temat ochrony przed złośliwym oprogramowaniem, zobacz Microsoft ochrona przed złośliwym kodem [dla platformy Azure Cloud Services i Virtual Machines](../security/fundamentals/antimalware.md).

### <a name="how-do-i-create-a-keytab-for-an-hdinsight-esp-cluster"></a>Jak mogę utworzyć plik KEYTAB dla klastra usługi HDInsight ESP?

Utwórz plik KEYTAB Kerberos dla nazwy użytkownika domeny. Później można użyć tego plik KEYTAB do uwierzytelniania w zdalnych klastrach przyłączonych do domeny bez wprowadzania hasła. Nazwa domeny jest wielką literą:

```shell
ktutil
ktutil: addent -password -p <username>@<DOMAIN.COM> -k 1 -e RC4-HMAC
Password for <username>@<DOMAIN.COM>: <password>
ktutil: wkt <username>.keytab
ktutil: q
```

### <a name="can-i-use-an-existing-azure-active-directory-tenant-to-create-an-hdinsight-cluster-that-has-the-esp"></a>Czy można użyć istniejącej dzierżawy Azure Active Directory do utworzenia klastra usługi HDInsight z partycją ESP?

Włącz Azure Active Directory Domain Services (AD DS platformy Azure), aby utworzyć klaster usługi HDInsight z użyciem ESP. Usługa Hadoop Open Source opiera się na protokole Kerberos na potrzeby uwierzytelniania (w przeciwieństwie do protokołu OAuth).

Aby dołączyć maszyny wirtualne do domeny, musisz mieć kontroler domeny. Usługa Azure AD DS jest zarządzanym kontrolerem domeny i jest uznawana za rozszerzenie Azure Active Directory. Usługa Azure AD DS zapewnia wszystkie wymagania dotyczące protokołu Kerberos w celu utworzenia bezpiecznego klastra usługi Hadoop w sposób zarządzany. Usługa HDInsight jest zintegrowana z usługą Azure AD DS w celu zapewnienia bezpieczeństwa.

### <a name="can-i-use-a-self-signed-certificate-in-an-aad-ds-secure-ldap-setup-and-provision-an-esp-cluster"></a>Czy można użyć certyfikatu z podpisem własnym w instalacji bezpiecznego protokołu LDAP usługi AAD-DS i zainicjować klaster ESP?

Zalecane jest użycie certyfikatu wystawionego przez urząd certyfikacji. Jednak przy użyciu protokołu ESP jest również obsługiwany certyfikat z podpisem własnym. Aby uzyskać więcej informacji, zobacz:

- [Włączanie usług Azure Active Directory Domain Services](domain-joined/apache-domain-joined-configure-using-azure-adds.md#enable-azure-ad-ds)

- [Samouczek: Konfigurowanie bezpiecznego protokołu LDAP dla domeny zarządzanej Azure Active Directory Domain Services](../active-directory-domain-services/tutorial-configure-ldaps.md)

### <a name="how-can-i-pull-login-activity-shown-in-ranger"></a>Jak można ściągnąć aktywność logowania pokazana w Ranger?

W przypadku wymagań inspekcji firma Microsoft zaleca włączenie dzienników Azure Monitor zgodnie z opisem w temacie [Korzystanie z dzienników Azure monitor do monitorowania klastrów usługi HDInsight](./hdinsight-hadoop-oms-log-analytics-tutorial.md).

### <a name="can-i-disable-clamscan-on-my-cluster"></a>Czy mogę wyłączyć `Clamscan` w moim klastrze?

`Clamscan` Program jest oprogramowaniem antywirusowym uruchamianym w klastrze usługi HDInsight i jest używany przez zabezpieczenia platformy Azure (azsecd) do ochrony klastrów przed atakami z wykorzystaniem wirusów. Firma Microsoft zdecydowanie zaleca, aby użytkownicy nie mogli wprowadzać zmian do `Clamscan` konfiguracji domyślnej.

Ten proces nie zakłóca działania ani nie podejmuje żadnych cykli od innych procesów. Zawsze będzie ona podlegać innemu procesowi. Wartości skoków procesora CPU z `Clamscan` powinny być widoczne tylko wtedy, gdy system jest w stanie bezczynności.  

W scenariuszach, w których należy kontrolować harmonogram, można wykonać następujące czynności:

1. Wyłącz automatyczne wykonywanie przy użyciu następującego polecenia:
   
  `usr/local/bin/azsecd config -s clamav -d Disabled`azsecd ponowne uruchomienie usługi sudo sudo 
   
1. Dodaj zadanie firmy CRONUS, które uruchamia następujące polecenie jako element główny:
   
   `/usr/local/bin/azsecd manual -s clamav`

Aby uzyskać więcej informacji o konfigurowaniu i uruchamianiu zadania firmy CRONUS, zobacz [Jak mogę skonfigurować zadanie firmy CRONUS](https://askubuntu.com/questions/2368/how-do-i-set-up-a-cron-job)?

### <a name="why-is-llap-available-on-spark-esp-clusters"></a>Dlaczego LLAP są dostępne w klastrach Spark ESP?
LLAP jest włączona ze względów bezpieczeństwa (Apache Ranger), a nie wydajności. Używaj większych maszyn wirtualnych węzłów, aby obsłużyć użycie zasobów LLAP (na przykład minimum D13V2). 

### <a name="how-can-i-add-additional-aad-groups-after-creating-an-esp-cluster"></a>Jak mogę dodać dodatkowe grupy usługi AAD po utworzeniu klastra ESP?
Istnieją dwa sposoby osiągnięcia tego celu: 1 — można utworzyć ponownie klaster i dodać dodatkową grupę podczas tworzenia klastra. Jeśli używasz synchronizacji w zakresie w usłudze AAD-DS, upewnij się, że Grupa B jest uwzględniona w synchronizacji z zakresem.
2. Dodaj grupę jako zagnieżdżoną podgrupę poprzedniej grupy, która została użyta do utworzenia klastra ESP. Na przykład, jeśli utworzono klaster ESP z grupą `A` , można później dodać grupę `B` jako zagnieżdżoną podgrupę `A` i po upływie około godziny, będzie ona synchronizowana i dostępna w klastrze automatycznie. 

## <a name="storage"></a>Storage

### <a name="can-i-add-an-azure-data-lake-storage-gen2-to-an-existing-hdinsight-cluster-as-an-additional-storage-account"></a>Czy można dodać Azure Data Lake Storage Gen2 do istniejącego klastra usługi HDInsight jako dodatkowe konto magazynu?

Nie, obecnie nie można dodać konta magazynu Azure Data Lake Storage Gen2 do klastra, który ma magazyn obiektów BLOB jako magazyn podstawowy. Aby uzyskać więcej informacji, zobacz [porównanie opcji magazynu](hdinsight-hadoop-compare-storage-options.md).

### <a name="how-can-i-find-the-currently-linked-service-principal-for-a-data-lake-storage-account"></a>Jak znaleźć nazwę główną połączonej usługi dla konta magazynu Data Lake?

Ustawienia można znaleźć w **Data Lake Storage Gen1 dostęp do** właściwości klastra w Azure Portal. Aby uzyskać więcej informacji, zobacz [Weryfikowanie konfiguracji klastra](../data-lake-store/data-lake-store-hdinsight-hadoop-use-portal.md#verify-cluster-set-up).
 
### <a name="how-can-i-calculate-the-usage-of-storage-accounts-and-blob-containers-for-my-hdinsight-clusters"></a>Jak można obliczyć użycie kont magazynu i kontenerów obiektów BLOB dla moich klastrów usługi HDInsight?

Wykonaj jedną z następujących czynności:

- [Korzystanie z programu PowerShell](../storage/scripts/storage-blobs-container-calculate-size-powershell.md)

- Znajdź rozmiar */User/Hive/. Kosz/* folder w klastrze usługi HDInsight przy użyciu następującego wiersza polecenia:
  
  `hdfs dfs -du -h /user/hive/.Trash/`

### <a name="how-can-i-set-up-auditing-for-my-blob-storage-account"></a>Jak skonfigurować inspekcję dla mojego konta usługi BLOB Storage?

Aby przeprowadzić inspekcję kont usługi BLOB Storage, należy skonfigurować monitorowanie za pomocą procedury w obszarze [Monitorowanie konta magazynu w Azure Portal](../storage/common/manage-storage-analytics-logs.md). Dziennik inspekcji systemu plików HDFS zawiera tylko informacje o inspekcji tylko dla lokalnego systemu HDFS (hdfs://mycluster).  Nie obejmują operacji wykonywanych w magazynie zdalnym.

### <a name="how-can-i-transfer-files-between-a-blob-container-and-an-hdinsight-head-node"></a>Jak mogę przenieść pliki między kontenerem obiektów blob i węzłem głównym usługi HDInsight?

Uruchom skrypt podobny do następującego skryptu powłoki w węźle głównym:

```shell
for i in cat filenames.txt
do
   hadoop fs -get $i <local destination>
done
```
 
> [!NOTE]
> Plik *filenames.txt* będzie zawierać ścieżkę bezwzględną plików w kontenerach obiektów BLOB.
 
### <a name="are-there-any-ranger-plugins-for-storage"></a>Czy istnieją jakieś wtyczki Ranger dla magazynu?

Obecnie nie istnieje wtyczka Ranger dla magazynu obiektów blob i Azure Data Lake Storage Gen1 lub Gen2. W przypadku klastrów ESP należy używać Azure Data Lake Storage. Można co najmniej ręcznie ustawić uprawnienia szczegółowe na poziomie systemu plików przy użyciu narzędzi HDFS. Ponadto w przypadku korzystania z Azure Data Lake Storage klastry ESP przeprowadziją część kontroli dostępu do systemu plików przy użyciu Azure Active Directory na poziomie klastra. 

Zasady dostępu do danych można przypisywać do grup zabezpieczeń użytkowników przy użyciu Eksplorator usługi Azure Storage. Aby uzyskać więcej informacji, zobacz:

- [Jak mogę ustawić uprawnienia dla użytkowników usługi Azure AD do wykonywania zapytań dotyczących danych w Data Lake Storage Gen2 przy użyciu programu Hive lub innych usług?](hdinsight-hadoop-use-data-lake-storage-gen2.md#how-do-i-set-permissions-for-azure-ad-users-to-query-data-in-data-lake-storage-gen2-by-using-hive-or-other-services)
- [Ustawianie uprawnień na poziomie plików i katalogów przy użyciu Eksploratora usługi Azure Storage w usłudze Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-explorer.md)

### <a name="can-i-increase-hdfs-storage-on-a-cluster-without-increasing-the-disk-size-of-worker-nodes"></a>Czy mogę zwiększyć Magazyn systemu plików HDFS w klastrze bez zwiększania rozmiaru dysku węzłów procesu roboczego?

Nie. Nie można zwiększyć rozmiaru dysku dowolnego węzła procesu roboczego. Aby zwiększyć rozmiar dysku, należy usunąć klaster i utworzyć go ponownie z większą ilością maszyn wirtualnych procesów roboczych. Nie należy używać systemu plików HDFS do przechowywania danych z usługi HDInsight, ponieważ dane są usuwane po usunięciu klastra. Zamiast tego należy przechowywać dane na platformie Azure. Skalowanie klastra może również zwiększyć pojemność klastra usługi HDInsight.

## <a name="edge-nodes"></a>Węzły brzegowe

### <a name="can-i-add-an-edge-node-after-the-cluster-has-been-created"></a>Czy mogę dodać węzeł brzegowy po utworzeniu klastra?

Zobacz [używanie pustych węzłów brzegowych w klastrach Apache Hadoop w usłudze HDInsight](hdinsight-apps-use-edge-node.md).

### <a name="how-can-i-connect-to-an-edge-node"></a>Jak połączyć się z węzłem brzegowym?

Po utworzeniu węzła brzegowego można nawiązać z nim połączenie przy użyciu protokołu SSH na porcie 22. Nazwę węzła krawędzi można znaleźć w portalu klastra. Zazwyczaj nazwy kończą się od *-Ed*.

### <a name="why-are-persisted-scripts-not-running-automatically-on-newly-created-edge-nodes"></a>Dlaczego utrwalone skrypty nie są automatycznie uruchamiane w nowo utworzonych węzłach brzegowych?

Za pomocą utrwalonych skryptów można dostosować nowe węzły procesu roboczego dodawane do klastra za pomocą operacji skalowania. Utrwalone skrypty nie mają zastosowania do węzłów brzegowych.

## <a name="rest-api"></a>Interfejs API REST

### <a name="what-are-the-rest-api-calls-to-pull-a-tez-query-view-from-the-cluster"></a>Jakie są wywołania interfejsu API REST w celu ściągnięcia tez widoku zapytania z klastra?

Aby ściągnąć niezbędne informacje w formacie JSON, można użyć następujących punktów końcowych REST. Użyj nagłówków uwierzytelniania podstawowego, aby wykonać żądania.

- `Tez Query View`: *https: \/ / \<cluster name> . azurehdinsight.NET/WS/V1/Timeline/HIVE_QUERY_ID/*
- `Tez Dag View`: *https: \/ / \<cluster name> . azurehdinsight.NET/WS/V1/Timeline/TEZ_DAG_ID/*

### <a name="how-do-i-retrieve-the-configuration-details-from-hdi-cluster-by-using-an-azure-active-directory-user"></a>Jak mogę pobrać szczegółów konfiguracji z klastra HDI przy użyciu użytkownika Azure Active Directory?

Aby wynegocjować odpowiednie tokeny uwierzytelniania z użytkownikiem usługi AAD, przejdź przez bramę, używając następującego formatu:

* https:// `<cluster dnsname>` . azurehdinsight.NET/API/V1/Clusters/testclusterdem/stack_versions/1/repository_versions/1 

### <a name="how-do-i-use-ambari-restful-api-to-monitor-yarn-performance"></a>Jak mogę używać Ambari RESTful API do monitorowania wydajności PRZĘDZy?

Jeśli wywołasz polecenie zwinięcie w tej samej sieci wirtualnej lub równorzędnej sieci wirtualnej, polecenie to:

```curl
curl -u <cluster login username> -sS -G
http://<headnodehost>:8080/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
```
 
Jeśli wywołasz polecenie z spoza sieci wirtualnej lub z sieci wirtualnej niepołączonej z usługą równorzędną, format polecenia to:

- W przypadku klastra non-ESP:
  
  ```curl
  curl -u <cluster login username> -sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

- W przypadku klastra ESP:
  
  ```curl
  curl -u <cluster login username>-sS -G 
  https://<ClusterName>.azurehdinsight.net/api/v1/clusters/<ClusterName>/services/YARN/components/NODEMANAGER?fields=metrics/cpu
  ```

> [!NOTE]
> Zwinięcie wyświetli monit o podanie hasła. Wprowadź prawidłowe hasło dla nazwy użytkownika logowania klastra.

## <a name="billing"></a>Rozliczenia

### <a name="how-much-does-it-cost-to-deploy-an-hdinsight-cluster"></a>Ile kosztuje Wdrożenie klastra usługi HDInsight?

Aby uzyskać więcej informacji na temat cen i często zadawanych pytań dotyczących rozliczeń, zobacz stronę [cennika usługi Azure HDInsight](https://azure.microsoft.com/pricing/details/hdinsight/) .

### <a name="when-does-hdinsight-billing-start--stop"></a>Kiedy rozliczanie usługi HDInsight rozpocznie się & Zatrzymaj?

Naliczanie opłat rozpoczyna się w momencie utworzenia klastra usługi HDInsight i kończy się wraz z jego usunięciem. Opłaty są naliczane proporcjonalnie do liczby minut.

### <a name="how-do-i-cancel-my-subscription"></a>Jak mogę anulować subskrypcję?

Aby uzyskać informacje o sposobach anulowania subskrypcji, zobacz [Anulowanie subskrypcji platformy Azure](../cost-management-billing/manage/cancel-azure-subscription.md).

### <a name="for-pay-as-you-go-subscriptions-what-happens-after-i-cancel-my-subscription"></a>Co dzieje się w przypadku subskrypcji z opcją płatność zgodnie z rzeczywistym użyciem, co się stanie po anulowaniu subskrypcji?

Aby uzyskać informacje o subskrypcji po jej anulowaniu, zobacz [co się stanie po usunięciu subskrypcji?](../cost-management-billing/manage/cancel-azure-subscription.md)

## <a name="hive"></a>Hive

### <a name="why-does-the-hive-version-appear-as-121000-instead-of-21-in-the-ambari-ui-even-though-im-running-an-hdinsight-36-cluster"></a>Dlaczego wersja programu Hive jest wyświetlana jako 1.2.1000 zamiast 2,1 w interfejsie użytkownika Ambari, mimo że jest uruchomiony klaster HDInsight 3,6?

Chociaż w interfejsie użytkownika Ambari pojawia się tylko 1,2, Usługa HDInsight 3,6 zawiera zarówno gałąź 1,2, jak i gałąź 2,1.

## <a name="other-faq"></a>Inne często zadawane pytania

### <a name="what-does-hdinsight-offer-for-real-time-stream-processing-capabilities"></a>Co to jest oferta usługi HDInsight dla możliwości przetwarzania strumieniowego w czasie rzeczywistym?

Informacje o możliwościach integracji przetwarzania strumieniowego znajdują się [w temacie Wybieranie technologii przetwarzania strumieniowego na platformie Azure](/azure/architecture/data-guide/technology-choices/stream-processing).

### <a name="is-there-a-way-to-dynamically-kill-the-head-node-of-the-cluster-when-the-cluster-is-idle-for-a-specific-period"></a>Czy istnieje sposób dynamicznego kasowania węzła głównego klastra, gdy klaster jest bezczynny przez określony okres?

Nie można wykonać tej akcji w przypadku klastrów usługi HDInsight. W tych scenariuszach można używać Azure Data Factory.

### <a name="what-compliance-offerings-does-hdinsight-offer"></a>Jakie oferty zgodności są oferowane przez usługi HDInsight?

Aby uzyskać informacje o zgodności, zobacz [Centrum zaufania firmy Microsoft](https://www.microsoft.com/trust-center) i [Omówienie zgodności Microsoft Azure](https://gallery.technet.microsoft.com/Overview-of-Azure-c1be3942).