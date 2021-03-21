---
title: Omówienie usługi Azure Storage w usłudze HDInsight
description: Omówienie usługi Azure Storage w usłudze HDInsight.
ms.service: hdinsight
ms.topic: conceptual
ms.custom: seoapr2020
ms.date: 04/21/2020
ms.openlocfilehash: f171ab9619f2bcb8ecf15c4bfb3b17146ab5a0ff
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98938953"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Omówienie usługi Azure Storage w usłudze HDInsight

Usługa Azure Storage to niezawodne rozwiązanie do magazynowania ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Za pomocą interfejsu HDFS pełny zestaw składników w usłudze HDInsight może działać bezpośrednio na danych ze strukturą lub bez struktury przechowywanych jako obiekty blob.

Zalecamy używanie oddzielnych kontenerów magazynu dla domyślnego magazynu klastra i danych firmowych. Rozdzielenie polega na odizolowaniu dzienników usługi HDInsight i plików tymczasowych z własnych danych firmowych. Zalecamy również usunięcie domyślnego kontenera obiektów blob, który zawiera Dzienniki aplikacji i systemu po każdym użyciu, aby zmniejszyć koszty magazynu. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Jeśli wybierzesz opcję zabezpieczenia konta magazynu za pomocą ograniczeń **zapory i sieci wirtualnych** w **wybranych sieciach**, należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft.**... Wyjątek polega na tym, że Usługa HDInsight może uzyskać dostęp do konta magazynu.

## <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight

Na poniższym diagramie przedstawiono abstrakcyjny widok architektury usługi HDInsight w usłudze Azure Storage:

!["HDInsight Storage Architecture"](./media/overview-azure-storage/storage-architecture.png "Architektura HDInsight Storage")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

`hdfs://<namenodehost>/<path>`

Za pomocą usługi HDInsight możesz również uzyskiwać dostęp do danych w usłudze Azure Storage. Składnia wygląda następująco:

`wasb://<containername>@<accountname>.blob.core.windows.net/<path>`

W przypadku korzystania z konta usługi Azure Storage z klastrami HDInsight należy wziąć pod uwagę następujące zasady:

* **Kontenery w ramach kont magazynu, które są podłączone do klastra:** ponieważ nazwa konta i klucz są kojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów blob w tych kontenerach.

* **Kontenery publiczne lub publiczne obiekty blob na kontach magazynu, które nie są połączone z klastrem:** Masz uprawnienia tylko do odczytu obiektów BLOB w kontenerach.
  
  > [!NOTE]  
  > Kontenery publiczne umożliwiają uzyskanie listy wszystkich obiektów blob, które są dostępne w tym kontenerze i uzyskiwania metadanych kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem anonimowym do odczytu do kontenerów i obiektów BLOB](../storage/blobs/anonymous-read-access-configure.md).

* **Prywatne kontenery na kontach magazynu, które nie są połączone z klastrem:** Nie można uzyskać dostępu do obiektów BLOB w kontenerach, chyba że zostanie zdefiniowane konto magazynu podczas przesyłania zadań WebHCat.

Konta magazynu definiowane w procesie tworzenia oraz ich klucze są przechowywane w pliku %HADOOP_HOME%/conf/core-site.xml w węzłach klastra. Domyślnie Usługa HDInsight używa kont magazynu zdefiniowanych w pliku core-site.xml. To ustawienie można zmodyfikować za pomocą usługi [Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive. I MapReduce, Apache Hadoop streaming i Apache świni, zawierają opis kont magazynu i metadanych. (Ten aspekt jest obecnie prawdziwy dla trzody chlewnej z kontami magazynu, ale nie dla metadanych). Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra usługi HDInsight z alternatywnymi kontami magazynu i magazynami](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx).

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów BLOB przechowują dane jako pary klucz/wartość i nie mają hierarchii katalogów. Jednak nazwa klucza może zawierać znak ukośnika (/), aby pojawił się tak, jakby plik był przechowywany w strukturze katalogów. Na przykład klucz obiektu BLOB może być `input/log1.txt` . Nie `input` istnieje rzeczywisty katalog, ale z powodu znaku ukośnika w nazwie klucza, klucz wygląda jak ścieżka do pliku.

## <a name="benefits-of-azure-storage"></a>Korzyści z usługi Azure Storage

W przypadku klastrów obliczeniowych i zasobów magazynu, które nie znajdują się na tym samym kosztem wydajności. Te koszty są korygowane przez sposób tworzenia klastrów obliczeniowych w pobliżu zasobów konta magazynu w regionie świadczenia usługi Azure. W tym regionie węzły obliczeniowe mogą efektywnie uzyskiwać dostęp do danych za pośrednictwem szybkiej sieci w usłudze Azure Storage.

W przypadku przechowywania danych w usłudze Azure Storage zamiast systemu plików HDFS uzyskasz kilka korzyści:

* **Udostępnianie i ponowne użycie danych:** dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Z kolei dostęp do danych w usłudze Azure Storage można uzyskać za pomocą interfejsów API systemu plików HDFS lub interfejsów API REST usługi BLOB Storage. W związku z tym, większy zestaw aplikacji (w tym inne klastry usługi HDInsight) i narzędzia mogą służyć do tworzenia i korzystania z danych.

* **Archiwizowanie danych:** Gdy dane są przechowywane w usłudze Azure Storage, klastry usługi HDInsight używane do obliczeń można bezpiecznie usunąć bez utraty danych użytkownika.

* **Koszt magazynowania danych:** Przechowywanie danych w systemie plików DFS przez długi czas jest droższe niż przechowywanie danych w usłudze Azure Storage. Ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure Storage. Ponadto, ponieważ nie trzeba ponownie ładować danych dla każdej generacji klastra obliczeniowego, są również zapisywane koszty ładowania danych.

* **Elastyczne skalowanie w poziomie:** chociaż system plików HDFS zapewnia skalowanie w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może być bardziej skomplikowana niż możliwości skalowania elastycznego automatycznie dostępne w usłudze Azure Storage.

* **Replikacja geograficzna:** Usługa Azure Storage może być replikowana geograficznie. Chociaż replikacja geograficzna umożliwia odzyskiwanie geograficzne i nadmiarowość danych, przejście w tryb failover do lokalizacji z replikacją geograficzną poważnie wpływa na wydajność i może pociągnąć za sobą dodatkowe koszty. Dlatego należy ostrożnie wybierać replikację geograficzną i tylko wtedy, gdy wartość danych uzasadnia dodatkowy koszt.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, które nie powinny być przechowywane w usłudze Azure Storage. W takim przypadku można wybrać przechowywanie danych w lokalnym systemie plików HDFS. Usługa HDInsight używa systemu plików DFS dla kilku wyników pośrednich w zadaniach Hive i innych procesach.

> [!NOTE]  
> Większość poleceń systemu plików HDFS (na przykład, `ls` `copyFromLocal` i `mkdir` ) działają zgodnie z oczekiwaniami w usłudze Azure Storage. Tylko polecenia specyficzne dla natywnej implementacji systemu plików HDFS (zwanej systemem DFS), takie jak `fschk` i `dfsadmin` , pokazują inne zachowanie w usłudze Azure Storage.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
* [Omówienie usługi Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)