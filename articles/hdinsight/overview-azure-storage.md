---
title: Omówienie usługi Azure Storage w usłudze HDInsight
description: Omówienie usługi Azure Storage w usłudze HDInsight.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 04/21/2020
ms.openlocfilehash: 43d948a2a98407bacc212ddc6e065c67a105f332
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81873383"
---
# <a name="azure-storage-overview-in-hdinsight"></a>Omówienie usługi Azure Storage w usłudze HDInsight

Usługa Azure Storage to niezawodne rozwiązanie magazynu ogólnego przeznaczenia, które bezproblemowo integruje się z usługą HDInsight. Usługa HDInsight może używać kontenera obiektów blob w usłudze Azure Storage jako domyślnego systemu plików dla klastra. Za pośrednictwem interfejsu HDFS pełny zestaw składników w programie HDInsight może działać bezpośrednio na ustrukturyzowanych lub nieustrukturyzowanych danych przechowywanych jako obiekty blob.

Zalecamy używanie oddzielnych kontenerów magazynu dla domyślnego magazynu klastra i danych biznesowych. Separacja polega na wyizolowaniu dzienników i plików tymczasowych HDInsight od własnych danych biznesowych. Zaleca się również usunięcie domyślnego kontenera obiektów blob, który zawiera dzienniki aplikacji i systemu, po każdym użyciu w celu zmniejszenia kosztów magazynowania. Koniecznie pobierz dzienniki przed usunięciem kontenera.

Jeśli zdecydujesz się zabezpieczyć swoje konto magazynu za pomocą **ograniczeń zapory i sieci wirtualnych** w **wybranych sieciach,** należy włączyć wyjątek **Zezwalaj na zaufane usługi firmy Microsoft...**. Wyjątkiem jest to, że HDInsight może uzyskać dostęp do konta magazynu.

## <a name="hdinsight-storage-architecture"></a>Architektura magazynu usługi HDInsight

Poniższy diagram zawiera abstrakcyjny widok architektury HDInsight usługi Azure Storage:

!["Architektura pamięci masowej HDInsight"](./media/overview-azure-storage/storage-architecture.png "Architektura pamięci masowej HDInsight")

Usługa HDInsight zapewnia dostęp do rozproszonego systemu plików, który jest lokalnie dołączony do węzłów obliczeniowych. Dostęp do tego systemu plików można uzyskać przy użyciu w pełni kwalifikowanego identyfikatora URI, na przykład:

    hdfs://<namenodehost>/<path>

Za pośrednictwem usługi HDInsight można również uzyskać dostęp do danych w usłudze Azure Storage. Składnia jest następująca:

    wasb://<containername>@<accountname>.blob.core.windows.net/<path>

Należy wziąć pod uwagę następujące zasady podczas korzystania z konta usługi Azure Storage z klastrami usługi HDInsight:

* **Kontenery w ramach kont magazynu, które są podłączone do klastra:** ponieważ nazwa konta i klucz są kojarzone z klastrem podczas tworzenia, masz pełny dostęp do obiektów blob w tych kontenerach.

* **Kontenery publiczne lub publiczne obiekty blob na kontach magazynu, które nie są połączone z klastrem:** Masz uprawnienia tylko do odczytu do obiektów blob w kontenerach.
  
  > [!NOTE]  
  > Kontenery publiczne umożliwiają uzyskanie listy wszystkich obiektów blob, które są dostępne w tym kontenerze i uzyskać metadane kontenera. Publiczne obiekty blob umożliwiają dostęp do obiektów blob jedynie osobom znającym dokładny adres URL. Aby uzyskać więcej informacji, zobacz [Zarządzanie anonimowym dostępem do odczytu kontenerów i obiektów blob.](../storage/blobs/storage-manage-access-to-resources.md)

* **Kontenery prywatne na kontach magazynu, które nie są połączone z klastrem:** Nie można uzyskać dostępu do obiektów blob w kontenerach, chyba że zdefiniujesz konto magazynu podczas przesyłania zadań WebHCat.

Konta magazynu definiowane w procesie tworzenia oraz ich klucze są przechowywane w pliku %HADOOP_HOME%/conf/core-site.xml w węzłach klastra. Domyślnie program HDInsight używa kont magazynu zdefiniowanych w pliku core-site.xml. To ustawienie można zmodyfikować za pomocą [programu Apache Ambari](./hdinsight-hadoop-manage-ambari.md).

Wiele zadań WebHCat, w tym Apache Hive. I MapReduce, Apache Hadoop streaming i Apache Pig, nosić opis kont pamięci masowej i metadanych. (Ten aspekt jest obecnie true dla Pig z kont magazynu, ale nie dla metadanych.) Aby uzyskać więcej informacji, zobacz [Korzystanie z klastra HDInsight z alternatywnymi kontami magazynu i sklepami.](https://social.technet.microsoft.com/wiki/contents/articles/23256.using-an-hdinsight-cluster-with-alternate-storage-accounts-and-metastores.aspx)

Obiekty blob mogą być używane z danymi ze strukturą i bez niej. Kontenery obiektów blob przechowują dane jako pary kluczy/wartości i nie mają hierarchii katalogów. Jednak nazwa klucza może zawierać znak ukośnika ( / ), aby wyglądał tak, jakby plik był przechowywany w strukturze katalogów. Na przykład klucz obiektu blob `input/log1.txt`może być . Nie `input` istnieje rzeczywisty katalog, ale ze względu na znak ukośnika w nazwie klucza wygląda jak ścieżka pliku.

## <a name="benefits-of-azure-storage"></a>Korzyści z usługi Azure Storage

Klastry obliczeniowe i zasoby magazynu, które nie są współlokowane, mają implikowane koszty wydajności. Koszty te są ograniczane przez sposób klastrów obliczeniowych są tworzone w pobliżu zasobów konta magazynu w regionie platformy Azure. W tym regionie węzły obliczeniowe mogą skutecznie uzyskiwać dostęp do danych za pośrednictwem sieci o dużej szybkości w usłudze Azure Storage.

Podczas przechowywania danych w usłudze Azure Storage zamiast hdfs, można uzyskać kilka korzyści:

* **Udostępnianie i ponowne użycie danych:** dane w systemie plików HDFS znajdują się wewnątrz klastra obliczeniowego. Tylko te aplikacje, które mają dostęp do klastra obliczeniowego mogą używać danych za pomocą interfejsów API systemu plików HDFS. Dane w usłudze Azure Storage, z drugiej strony, można uzyskać za pośrednictwem interfejsów API HDFS lub interfejsów API REST magazynu obiektów blob. Z powodu tego układu większy zestaw aplikacji (w tym innych klastrów HDInsight) i narzędzia mogą być używane do tworzenia i korzystania z danych.

* **Archiwizacja danych:** Gdy dane są przechowywane w usłudze Azure Storage, klastry HDInsight używane do obliczeń można bezpiecznie usunąć bez utraty danych użytkownika.

* **Koszt przechowywania danych:** Przechowywanie danych w dfs na dłuższą metę jest bardziej kosztowne niż przechowywanie danych w usłudze Azure Storage. Ponieważ koszt klastra obliczeniowego jest wyższy niż koszt usługi Azure Storage. Ponadto ponieważ dane nie muszą być ponownie ładowane dla każdej generacji klastra obliczeniowego, oszczędzasz również koszty ładowania danych.

* **Elastyczne skalowanie w poziomie:** chociaż system plików HDFS zapewnia skalowanie w poziomie, skala jest wyznaczana przez liczbę węzłów tworzonych dla klastra. Zmiana skali może być bardziej skomplikowana niż elastyczne możliwości skalowania, które można uzyskać automatycznie w usłudze Azure Storage.

* **Replikacja geograficzna:** Usługa Azure Storage może być replikowana geograficznie. Mimo że replikacja geograficzna zapewnia odzyskiwanie geograficzne i nadmiarowość danych, funkcja failover lokalizacji replikowanej geograficznie poważnie wpływa na wydajność i może ponieść dodatkowe koszty. Dlatego wybierz replikację geograficzną ostrożnie i tylko wtedy, gdy wartość danych uzasadnia dodatkowy koszt.

Niektóre zadania i pakiety MapReduce mogą tworzyć wyniki pośrednie, których nie chcesz przechowywać w usłudze Azure Storage. W takim przypadku można zapisać dane w lokalnym pliku HDFS. HdInsight używa systemu plików DFS dla kilku z tych wyników pośrednich w zadaniach hive i innych procesach.

> [!NOTE]  
> Większość poleceń HDFS (na `copyFromLocal`przykład `mkdir` `ls`, i ) działa zgodnie z oczekiwaniami w usłudze Azure Storage. Tylko polecenia, które są specyficzne dla implementacji natywnego systemu PLIKÓW HDFS (który jest określany jako DFS), takich jak `fschk` i `dfsadmin`, pokaż różne zachowanie w usłudze Azure Storage.

## <a name="next-steps"></a>Następne kroki

* [Wprowadzenie do usługi Azure Data Lake Storage Gen2](../storage/blobs/data-lake-storage-introduction.md)
* [Wprowadzenie do usługi Azure Storage](../storage/common/storage-introduction.md)
* [Omówienie usługi Azure Data Lake Storage Gen1](./overview-data-lake-storage-gen1.md)