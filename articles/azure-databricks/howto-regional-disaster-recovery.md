---
title: Regionalne odzyskiwanie po awarii dla Azure Databricks
description: W tym artykule opisano sposób przeprowadzania odzyskiwania po awarii w Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 03/13/2019
ms.openlocfilehash: 599dc950cf286520e3f099966786d836ad05ba94
ms.sourcegitcommit: 6571e34e609785e82751f0b34f6237686470c1f3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 06/15/2020
ms.locfileid: "84789331"
---
# <a name="regional-disaster-recovery-for-azure-databricks-clusters"></a>Regionalne odzyskiwanie po awarii dla klastrów Azure Databricks

W tym artykule opisano architekturę odzyskiwania po awarii, przydatną dla klastrów Azure Databricks, oraz czynności, które należy wykonać w celu osiągnięcia tego projektu.

## <a name="azure-databricks-architecture"></a>Architektura Azure Databricks

Na wysokim poziomie, podczas tworzenia obszaru roboczego Azure Databricks z Azure Portal, [urządzenie zarządzane](../azure-resource-manager/managed-applications/overview.md) zostanie wdrożone jako zasób platformy Azure w ramach subskrypcji w wybranym regionie świadczenia usługi Azure (na przykład zachodnie stany USA). To urządzenie jest wdrażane w usłudze [azure Virtual Network](../virtual-network/virtual-networks-overview.md) z [grupą zabezpieczeń sieci](../virtual-network/manage-network-security-group.md) i kontem usługi Azure Storage, które są dostępne w ramach Twojej subskrypcji. Sieć wirtualna zapewnia zabezpieczenia na poziomie obwodu obszaru roboczego datakostki i jest chroniona za pośrednictwem sieciowej grupy zabezpieczeń. W obszarze roboczym można tworzyć klastry datarangi, dostarczając typ maszyny wirtualnej i w wersji środowiska uruchomieniowego. Utrwalone dane są dostępne na koncie magazynu, które może być Blob Storage platformy Azure lub Azure Data Lake Storage. Po utworzeniu klastra można uruchamiać zadania za pośrednictwem notesów, interfejsów API REST, punktów końcowych ODBC/JDBC, dołączając je do określonego klastra.

Płaszczyzna kontroli datakostki zarządza i monitoruje środowisko obszaru roboczego datakostki. Każda operacja zarządzania, taka jak tworzenie klastra, zostanie zainicjowana z poziomu płaszczyzny kontroli. Wszystkie metadane, takie jak zaplanowane zadania, są przechowywane w bazie danych platformy Azure z replikacją geograficzną na potrzeby odporności na uszkodzenia.

![Architektura datakostek](media/howto-regional-disaster-recovery/databricks-architecture.png)

Jedną z zalet tej architektury jest to, że użytkownicy mogą łączyć Azure Databricks z dowolnym zasobem magazynu na swoim koncie. Najważniejsze korzyści polegają na tym, że zarówno obliczeniowe (Azure Databricks), jak i magazyn mogą być skalowane niezależnie od siebie.

## <a name="how-to-create-a-regional-disaster-recovery-topology"></a>Jak utworzyć regionalną topologię odzyskiwania po awarii

W przypadku powyższego opisu architektury istnieje wiele składników służących do potoku danych Big Data, Azure Databricks: Azure Storage, Azure Database i inne źródła danych. Azure Databricks to *Obliczanie* dla potoku danych Big Data. Ma ona charakter dobrowolny, *co oznacza,* że w przypadku, gdy dane są nadal dostępne w usłudze Azure Storage, można zakończyć działanie *obliczeniowe* (Azure Databricks klaster), dzięki czemu nie trzeba płacisz za obliczenia, gdy nie jest to potrzebne. Zasoby *obliczeniowe* (Azure Databricks) i magazyn muszą znajdować się w tym samym regionie, co sprawia, że zadania nie będą mieć dużego opóźnienia.  

Aby utworzyć własną regionalną topologię odzyskiwania po awarii, wykonaj następujące wymagania:

   1. Udostępnianie wielu obszarów roboczych Azure Databricks w oddzielnych regionach świadczenia usługi Azure. Na przykład Utwórz podstawowy obszar roboczy Azure Databricks w regionie Wschodnie stany USA 2. Utwórz pomocniczy obszar roboczy Azure Databricks odzyskiwania po awarii w osobnym regionie, takim jak zachodnie stany USA.

   2. Używaj [magazynu geograficznie nadmiarowego](../storage/common/storage-redundancy.md). Dane skojarzone Azure Databricks są domyślnie przechowywane w usłudze Azure Storage. Wyniki zadań z datakostki są również przechowywane w usłudze Azure Blob Storage, aby przetworzone dane były trwałe i pozostawały wysoce dostępne po zakończeniu pracy z klastrem. Ponieważ klaster magazynu i magazynów danych znajduje się na tym samym miejscu, należy użyć magazynu geograficznie nadmiarowego, aby umożliwić dostęp do danych w regionie pomocniczym, jeśli region podstawowy nie jest już dostępny.

   3. Po utworzeniu regionu pomocniczego należy przeprowadzić migrację użytkowników, folderów użytkowników, notesów, konfiguracji klastra, konfiguracji zadań, bibliotek, magazynu, skryptów init i ponownie skonfigurować kontrolę dostępu. Dodatkowe szczegóły zostały opisane w poniższej sekcji.

## <a name="detailed-migration-steps"></a>Szczegółowe kroki migracji

1. **Konfigurowanie interfejsu wiersza polecenia datakostki na komputerze**

   W tym artykule przedstawiono kilka przykładów kodu, które używają interfejsu wiersza polecenia w przypadku większości zautomatyzowanych kroków, ponieważ jest to łatwa w obsłudze otoka dla interfejsu API REST Azure Databricks.

   Przed przeprowadzeniem jakichkolwiek kroków migracji należy zainstalować na komputerze stacjonarnym lub maszynie wirtualnej, w której planujesz wykonać prace. Aby uzyskać więcej informacji, zobacz [Installing datakostks — interfejs wiersza polecenia](/azure/databricks/dev-tools/databricks-cli)

   ```bash
   pip install databricks-cli
   ```

   > [!NOTE]
   > Wszystkie skrypty języka Python podane w tym artykule powinny współpracować z językiem Python 2.7 + < 3. x.

2. **Skonfiguruj dwa profile.**

   Skonfiguruj jeden dla podstawowego obszaru roboczego, a drugi dla pomocniczego obszaru roboczego:

   ```bash
   databricks configure --profile primary --token
   databricks configure --profile secondary --token
   ```

   Bloki kodu w tym artykule przełączają się między profilami w każdym kolejnym kroku przy użyciu odpowiedniego polecenia obszaru roboczego. Upewnij się, że nazwy tworzonych profilów są zastępowane w każdym bloku kodu.

   ```python
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   ```

   W razie konieczności można ręcznie przełączać się w wierszu polecenia:

   ```bash
   databricks workspace ls --profile primary
   databricks workspace ls --profile secondary
   ```

3. **Migrowanie Azure Active Directory użytkowników**

   Ręcznie Dodaj tych samych Azure Active Directory użytkowników do pomocniczego obszaru roboczego, który istnieje w podstawowym obszarze roboczym.

4. **Migrowanie folderów i notesów użytkowników**

   Użyj poniższego kodu w języku Python, aby zmigrować środowiska użytkownika w trybie piaskownicy, w tym zagnieżdżoną strukturę folderów i notesy na użytkownika.

   > [!NOTE]
   > Biblioteki nie są kopiowane w ramach tego kroku, ponieważ źródłowy interfejs API nie obsługuje tych funkcji.

   Skopiuj i Zapisz następujący skrypt w języku Python do pliku i uruchom go w wierszu polecenia danych. Na przykład `python scriptname.py`.

   ```python
   import sys
   import os
   import subprocess
   from subprocess import call, check_output
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Get a list of all users
   user_list_out = check_output(["databricks", "workspace", "ls", "/Users", "--profile", EXPORT_PROFILE])
   user_list = (user_list_out.decode(encoding="utf-8")).splitlines()
   
   print (user_list)
   
   # Export sandboxed environment(folders, notebooks) for each user and import into new workspace.
   #Libraries are not included with these APIs / commands.
   
   for user in user_list:
     #print("Trying to migrate workspace for user ".decode() + user)
     print (("Trying to migrate workspace for user ") + user)
   
     subprocess.call(str("mkdir -p ") + str(user), shell = True)
     export_exit_status = call("databricks workspace export_dir /Users/" + str(user) + " ./" + str(user) + " --profile " + EXPORT_PROFILE, shell = True)
   
     if export_exit_status==0:
       print ("Export Success")
       import_exit_status = call("databricks workspace import_dir ./" + str(user) + " /Users/" + str(user) + " --profile " + IMPORT_PROFILE, shell=True)
       if import_exit_status==0:
         print ("Import Success")
       else:
         print ("Import Failure")
     else:
       print ("Export Failure")
   print ("All done")
   ```

5. **Migrowanie konfiguracji klastra**

   Po migracji notesów można opcjonalnie migrować konfiguracje klastra do nowego obszaru roboczego. Jest to niemal całkowicie zautomatyzowany krok przy użyciu datakosteks-CLI, chyba że chcesz przeprowadzić selektywną migrację konfiguracji klastra, a nie wszystkie.

   > [!NOTE]
   > Niestety, nie ma punktu końcowego konfiguracji klastra tworzenia i ten skrypt próbuje utworzyć każdy klaster od razu. Jeśli w subskrypcji nie ma wystarczającej liczby rdzeni, tworzenie klastra może zakończyć się niepowodzeniem. Błąd można zignorować, dopóki konfiguracja została pomyślnie przekazana.

   Poniższy skrypt wyświetla mapowanie ze starych do nowych identyfikatorów klastra, które mogą być używane do późniejszej migracji zadań (w przypadku zadań skonfigurowanych do korzystania z istniejących klastrów).

   Skopiuj i Zapisz następujący skrypt w języku Python do pliku i uruchom go w wierszu polecenia danych. Na przykład `python scriptname.py`.

   ```python
   import sys
   import os
   import subprocess
   import json
   from subprocess import call, check_output
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Get all clusters info from old workspace
   clusters_out = check_output(["databricks", "clusters", "list",    "--profile", EXPORT_PROFILE])
   clusters_info_list = str(clusters_out.decode(encoding="utf-8")).   splitlines()
   print("Printting Cluster info List")
   print(clusters_info_list)
   
   # Create a list of all cluster ids
   clusters_list = []
   ##for cluster_info in clusters_info_list: clusters_list.append   (cluster_info.split(None, 1)[0])
   
   for cluster_info in clusters_info_list:
      if cluster_info != '':
         clusters_list.append(cluster_info.split(None, 1)[0])
   
   # Optionally filter cluster ids out manually, so as to create only required ones in new workspace

   # Create a list of mandatory / optional create request elements
   cluster_req_elems = ["num_workers","autoscale","cluster_name","spark_version","spark_conf","node_type_id","driver_node_type_id","custom_tags","cluster_log_conf","spark_env_vars","autotermination_minutes","enable_elastic_disk"]
   print("Printing Cluster element List")
   print (cluster_req_elems)
   print(str(len(clusters_list)) + " clusters found in the primary site" )
   
   print ("---------------------------------------------------------")
   # Try creating all / selected clusters in new workspace with same config as in old one.
   cluster_old_new_mappings = {}
   i = 0
   for cluster in clusters_list:
      i += 1
      print("Checking cluster " + str(i) + "/" + str(len(clusters_list)) + " : " +str(cluster))
      cluster_get_out_f = check_output(["databricks", "clusters", "get", "--cluster-id", str(cluster), "--profile", EXPORT_PROFILE])
      cluster_get_out=str(cluster_get_out_f.decode(encoding="utf-8"))
      print ("Got cluster config from old workspace")
      print (cluster_get_out)
       # Remove extra content from the config, as we need to build create request with allowed elements only
      cluster_req_json = json.loads(cluster_get_out)
      cluster_json_keys = cluster_req_json.keys()

      #Don't migrate Job clusters
      if cluster_req_json['cluster_source'] == u'JOB' :
         print ("Skipping this cluster as it is a Job cluster : " + cluster_req_json['cluster_id'] )
         print ("---------------------------------------------------------")
         continue

         #cluster_req_json.pop(key, None)
         for key in cluster_json_keys:
           if key not in cluster_req_elems:
            print (cluster_req_json)
            #cluster_del_item=cluster_json_keys .keys()
            cluster_req_json.popitem(key, None)
  
      # Create the cluster, and store the mapping from old to new cluster ids

      #Create a temp file to store the current cluster info as JSON
      strCurrentClusterFile = "tmp_cluster_info.json"

      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) :
         os.remove(strCurrentClusterFile)
   
      fClusterJSONtmp = open(strCurrentClusterFile,"w+")
      fClusterJSONtmp.write(json.dumps(cluster_req_json))
      fClusterJSONtmp.close()
   
      #cluster_create_out = check_output(["databricks", "clusters", "create", "--json", json.dumps(cluster_req_json), "--profile", IMPORT_PROFILE])
      cluster_create_out = check_output(["databricks", "clusters", "create", "--json-file", strCurrentClusterFile , "--profile", IMPORT_PROFILE])
      cluster_create_out_json = json.loads(cluster_create_out)
      cluster_old_new_mappings[cluster] = cluster_create_out_json['cluster_id']
   
      print ("Cluster create request sent to secondary site workspace successfully")
      print ("---------------------------------------------------------")
   
      #delete the temp file if exists
      if os.path.exists(strCurrentClusterFile) :
         os.remove(strCurrentClusterFile)
   
   print ("Cluster mappings: " + json.dumps(cluster_old_new_mappings))
   print ("All done")
   print ("P.S. : Please note that all the new clusters in your secondary site are being started now!")
   print ("       If you won't use those new clusters at the moment, please don't forget terminating your new clusters to avoid charges")
   ```
   
6. **Migrowanie konfiguracji zadań**

   Jeśli w poprzednim kroku zostały zmigrowane konfiguracje klastra, można wybrać migrację konfiguracji zadań do nowego obszaru roboczego. Jest to w pełni zautomatyzowany krok przy użyciu datakosteks-CLI, chyba że chcesz przeprowadzić selektywną migrację konfiguracji zadań, zamiast wykonywać ją dla wszystkich zadań.

   > [!NOTE]
   > Konfiguracja zaplanowanego zadania zawiera również informacje o harmonogramie, więc domyślnie rozpocznie pracę zgodnie z skonfigurowanym chronometrażem zaraz po migracji. W związku z tym Poniższy blok kodu usuwa wszelkie informacje o harmonogramie podczas migracji (aby uniknąć zduplikowanych przebiegów w starym i nowym obszarze roboczym). Skonfiguruj harmonogramy dla takich zadań, gdy wszystko będzie gotowe do uruchomienie produkcyjne.

   Konfiguracja zadania wymaga ustawień dla nowego lub istniejącego klastra. W przypadku korzystania z istniejącego klastra skrypt/Code poniżej podejmie próbę zastąpienia starego identyfikatora klastra nowym IDENTYFIKATORem klastra.

   Skopiuj i Zapisz Poniższy skrypt w języku Python do pliku. Zastąp wartość dla `old_cluster_id` i `new_cluster_id` , z danymi wyjściowymi z migracji klastra wykonaną w poprzednim kroku. Uruchom ją w wierszu polecenia datakosteks-CLI, na przykład `python scriptname.py` .

   ```python
   import sys
   import os
   import subprocess
   import json
   from subprocess import call, check_output
   
   
   EXPORT_PROFILE = "primary"
   IMPORT_PROFILE = "secondary"
   
   # Please replace the old to new cluster id mappings from cluster migration output
   cluster_old_new_mappings = {"0227-120427-tryst214": "0229-032632-paper88"}
   
   # Get all jobs info from old workspace
   try:
     jobs_out = check_output(["databricks", "jobs", "list", "--profile", EXPORT_PROFILE])
     jobs_info_list = jobs_out.splitlines()
   except:
     print("No jobs to migrate")
     sys.exit(0)
   
   # Create a list of all job ids
   jobs_list = []
   for jobs_info in jobs_info_list:
     jobs_list.append(jobs_info.split(None, 1)[0])
   
   # Optionally filter job ids out manually, so as to create only required ones in new workspace
   
   # Create each job in the new workspace based on corresponding settings in the old workspace
   
   for job in jobs_list:
     print("Trying to migrate ") + job
   
     job_get_out = check_output(["databricks", "jobs", "get", "--job-id", job, "--profile", EXPORT_PROFILE])
     print("Got job config from old workspace")
   
     job_req_json = json.loads(job_get_out)  
     job_req_settings_json = job_req_json['settings']
   
     # Remove schedule information so job doesn't start before proper cutover
     job_req_settings_json.pop('schedule', None)
   
     # Replace old cluster id with new cluster id, if job configured to run against an existing cluster
     if 'existing_cluster_id' in job_req_settings_json:
       if job_req_settings_json['existing_cluster_id'] in cluster_old_new_mappings:
         job_req_settings_json['existing_cluster_id'] = cluster_old_new_mappings[job_req_settings_json['existing_cluster_id']]
       else:
         print("Mapping not available for old cluster id ") + job_req_settings_json['existing_cluster_id']
         continue
   
     call(["databricks", "jobs", "create", "--json", json.dumps(job_req_settings_json), "--profile", IMPORT_PROFILE])
     print("Sent job create request to new workspace successfully")
   
   print("All done")
   ```

7. **Migrowanie bibliotek**

   Obecnie nie ma prostego sposobu migracji bibliotek z jednego obszaru roboczego do innego. Zamiast tego ponownie zainstaluj te biblioteki ręcznie w nowym obszarze roboczym. Można zautomatyzować korzystanie z kombinacji [interfejsu wiersza polecenia DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples) w celu przekazywania bibliotek niestandardowych do obszaru roboczego i [bibliotek interfejsu wiersza polecenia](https://github.com/databricks/databricks-cli#libraries-cli).

8. **Migrowanie instalacji usługi Azure Blob Storage i Azure Data Lake Storage**

   Ręcznie zainstaluj ponownie wszystkie punkty instalacji [usługi Azure Blob Storage](/azure/databricks/data/data-sources/azure/azure-storage) i [Azure Data Lake Storage (Gen 2)](/azure/databricks/data/data-sources/azure/azure-datalake-gen2) przy użyciu rozwiązań opartych na notesie. Zasoby magazynu zostaną zainstalowane w podstawowym obszarze roboczym i muszą zostać powtórzone w pomocniczym obszarze roboczym. Nie istnieje zewnętrzny interfejs API dla instalacji.

9. **Migruj skrypty inicjowania klastra**

   Wszystkie skrypty inicjowania klastrów można migrować ze starego do nowego obszaru roboczego za pomocą [interfejsu wiersza polecenia DBFS](https://github.com/databricks/databricks-cli#dbfs-cli-examples). Najpierw skopiuj odpowiednie skrypty z programu `dbfs:/dat abricks/init/..` do pulpitu lokalnego lub maszyny wirtualnej. Następnie skopiuj te skrypty do nowego obszaru roboczego w tej samej ścieżce.

   ```bash
   // Primary to local
   dbfs cp -r dbfs:/databricks/init ./old-ws-init-scripts --profile primary

   // Local to Secondary workspace
   dbfs cp -r old-ws-init-scripts dbfs:/databricks/init --profile secondary
   ```

10. **Ręcznie skonfiguruj i ponownie Zastosuj kontrolę dostępu.**

    Jeśli istniejący podstawowy obszar roboczy jest skonfigurowany do korzystania z warstwy Premium (SKU), prawdopodobnie korzystasz również z [funkcji Access Control](/azure/databricks/administration-guide/access-control/index).

    W przypadku korzystania z funkcji Access Control ręcznie ponownie zastosować kontrolę dostępu do zasobów (notesy, klastry, zadania, tabele).

## <a name="disaster-recovery-for-your-azure-ecosystem"></a>Odzyskiwanie po awarii dla ekosystemu platformy Azure

Jeśli używasz innych usług platformy Azure, pamiętaj o tym również zaimplementować najlepsze rozwiązania dotyczące odzyskiwania po awarii. Na przykład jeśli zdecydujesz się na korzystanie z wystąpienia zewnętrznego magazyn metadanych Hive, należy rozważyć możliwość odzyskiwania po awarii dla [Azure SQL Database](../azure-sql/database/disaster-recovery-guidance.md), [usługi Azure HDInsight](../hdinsight/hdinsight-high-availability-linux.md)i/lub [Azure Database for MySQL](../mysql/concepts-business-continuity.md). Aby uzyskać ogólne informacje na temat odzyskiwania po awarii, zobacz [odzyskiwanie po awarii dla aplikacji platformy Azure](https://docs.microsoft.com/azure/architecture/resiliency/disaster-recovery-azure-applications).

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz [dokumentację Azure Databricks](index.yml).
