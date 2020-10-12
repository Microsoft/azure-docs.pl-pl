---
title: Sterownik systemu plików obiektów blob platformy Azure dla Azure Data Lake Storage Gen2
description: Dowiedz się więcej na temat sterownika usługi Azure Blob FileSystem (ABFS) — dedykowanego sterownika magazynu platformy Azure dla usługi Hadoop. Dostęp do danych w Azure Data Lake Storage Gen2 przy użyciu tego sterownika.
author: normesta
ms.topic: conceptual
ms.author: normesta
ms.reviewer: jamesbak
ms.date: 12/06/2018
ms.service: storage
ms.subservice: data-lake-storage-gen2
ms.openlocfilehash: e9c56f59d6dd64002632a5f74e9f39c51293caab
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87836364"
---
# <a name="the-azure-blob-filesystem-driver-abfs-a-dedicated-azure-storage-driver-for-hadoop"></a>Sterownik systemu plików obiektów blob platformy Azure (ABFS): dedykowany sterownik magazynu platformy Azure dla usługi Hadoop

Jedną z podstawowych metod dostępu do danych w Azure Data Lake Storage Gen2 jest za pośrednictwem systemu [plików Hadoop](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/index.html). Data Lake Storage Gen2 umożliwia użytkownikom platformy Azure Blob Storage dostęp do nowego sterownika, sterownika systemu plików obiektów blob platformy Azure lub `ABFS` . ABFS jest częścią Apache Hadoop i jest dołączany do wielu komercyjnych dystrybucji usługi Hadoop. Za pomocą tego sterownika wiele aplikacji i platform może uzyskiwać dostęp do danych na platformie Azure Blob Storage bez bezpośredniego odwoływania się do Data Lake Storage Gen2.

## <a name="prior-capability-the-windows-azure-storage-blob-driver"></a>Wcześniejsza możliwość: Sterownik Azure Storage Blob systemu Windows

Sterownik Azure Storage Blob systemu Windows lub [Sterownik WASB](https://hadoop.apache.org/docs/current/hadoop-azure/index.html) dostarczył oryginalnego wsparcia dla BLOB Storage platformy Azure. Ten sterownik wykonał złożone zadanie mapowania semantyki systemu plików (zgodnie z wymaganiami interfejsu system plików Hadoop) do interfejsu stylu magazynu obiektów uwidocznionego przez usługę Azure Blob Storage. Ten sterownik nadal obsługuje ten model, zapewniając wysoką wydajność dostępu do danych przechowywanych w obiektach Blob, ale zawiera znaczną ilość kodu wykonującego to mapowanie, utrudniając jego konserwację. Ponadto niektóre operacje, takie jak [FileSystem. Rename ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_renamePath_src_Path_d) i [FileSystem. Delete ()](https://hadoop.apache.org/docs/current/hadoop-project-dist/hadoop-common/filesystem/filesystem.html#boolean_deletePath_p_boolean_recursive) w przypadku zastosowania do katalogów, wymagają, aby sterownik wykonywał ogromną liczbę operacji (ze względu na to, że obiekt przechowuje brak obsługi katalogów), co często prowadzi do obniżenia wydajności. Sterownik ABFS został zaprojektowany w celu pokonania nieodłącznych wad WASB.

## <a name="the-azure-blob-file-system-driver"></a>Sterownik systemu plików obiektów blob platformy Azure

[Interfejs REST Azure Data Lake Storage](https://docs.microsoft.com/rest/api/storageservices/data-lake-storage-gen2) jest przeznaczony do obsługi semantyki systemu plików za pośrednictwem usługi Azure Blob Storage. Mając na względzie, że system plików Hadoop jest również przeznaczony do obsługi tej samej semantyki, nie istnieje wymóg mapowania złożonego w sterowniku. W ten sposób sterownik systemu plików (lub ABFS) usługi Azure BLOB jest po prostu podkładką dla interfejsu API REST.

Jednak niektóre funkcje, które muszą być nadal wykonywane przez sterownik:

### <a name="uri-scheme-to-reference-data"></a>Schemat identyfikatora URI do danych referencyjnych

Spójne z innymi implementacjami systemu plików w ramach usługi Hadoop, sterownik ABFS definiuje własny schemat identyfikatorów URI, aby zasoby (Katalogi i pliki) mogły być rozdzielone odrębnie. Schemat identyfikatora URI jest udokumentowany w [użyciu identyfikatora uri Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md). Struktura identyfikatora URI: `abfs[s]://file_system@account_name.dfs.core.windows.net/<path>/<path>/<file_name>`

Korzystając z powyższego formatu identyfikatora URI, można użyć standardowych narzędzi i struktur usługi Hadoop do odwoływania się do tych zasobów:

```bash
hdfs dfs -mkdir -p abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data
hdfs dfs -put flight_delays.csv abfs://fileanalysis@myanalytics.dfs.core.windows.net/tutorials/flightdelays/data/
```

Wewnętrznie sterownik ABFS tłumaczy zasoby określone w identyfikatorze URI na pliki i katalogi, a następnie nawiązuje Azure Data Lake Storage wywołania interfejsu API REST z tymi odwołaniami.

### <a name="authentication"></a>Uwierzytelnianie

Sterownik ABFS obsługuje dwie metody uwierzytelniania, dzięki czemu aplikacja Hadoop może bezpiecznie uzyskiwać dostęp do zasobów znajdujących się w ramach konta obsługującego Data Lake Storage Gen2. Szczegółowe informacje o dostępnych schematach uwierzytelniania znajdują się w [przewodniku po zabezpieczeniach usługi Azure Storage](security-recommendations.md). Są to:

- **Klucz współużytkowany:** Pozwala to użytkownikom na dostęp do wszystkich zasobów na koncie. Klucz jest szyfrowany i przechowywany w konfiguracji usługi Hadoop.

- **Azure Active Directory token okaziciela OAuth:** Tokeny okaziciela usługi Azure AD są uzyskiwane i odświeżane przez sterownik przy użyciu tożsamości użytkownika końcowego lub skonfigurowanej jednostki usługi. Przy użyciu tego modelu uwierzytelniania cały dostęp jest autoryzowany dla każdego wywołania przy użyciu tożsamości skojarzonej z podanym tokenem i oceniany względem przypisanej listy Access Control POSIX (ACL).

   > [!NOTE]
   > Azure Data Lake Storage Gen2 obsługuje tylko punkty końcowe usługi Azure AD v 1.0.

### <a name="configuration"></a>Konfiguracja

Wszystkie konfiguracje sterownika ABFS są przechowywane w <code>core-site.xml</code> pliku konfiguracji. W przypadku dystrybucji usługi Hadoop z [Ambari](https://ambari.apache.org/), konfiguracja może być również zarządzana przy użyciu portalu internetowego lub interfejsu API REST Ambari.

Szczegóły wszystkich obsługiwanych wpisów konfiguracji są określone w [oficjalnej dokumentacji usługi Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html).

### <a name="hadoop-documentation"></a>Dokumentacja usługi Hadoop

Sterownik ABFS jest w pełni udokumentowany w [oficjalnej dokumentacji usługi Hadoop](https://hadoop.apache.org/docs/stable/hadoop-azure/abfs.html)

## <a name="next-steps"></a>Następne kroki

- [Tworzenie klastra Azure Databricks](./data-lake-storage-quickstart-create-databricks-account.md)
- [Korzystanie z identyfikatora URI usługi Azure Data Lake Storage Gen2](./data-lake-storage-introduction-abfs-uri.md)
