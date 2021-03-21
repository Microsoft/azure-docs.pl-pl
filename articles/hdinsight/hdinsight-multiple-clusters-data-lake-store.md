---
title: Wiele klastrów usługi HDInsight & jedno konto Azure Data Lake Storage
description: Dowiedz się, jak używać więcej niż jednego klastra usługi HDInsight z jednym kontem Data Lake Storage
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive
ms.date: 12/18/2019
ms.openlocfilehash: 6e220592f53103320c3bdb586fcbd0106219bfed
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98939534"
---
# <a name="use-multiple-hdinsight-clusters-with-an-azure-data-lake-storage-account"></a>Używanie wielu klastrów usługi HDInsight z kontem Azure Data Lake Storage

Począwszy od usługi HDInsight w wersji 3,5, można tworzyć klastry usługi HDInsight z kontami Azure Data Lake Storage jako domyślny system plików.
Data Lake Storage obsługuje nieograniczony magazyn, który sprawia, że nie tylko hostuje duże ilości danych. ale także do hostowania wielu klastrów usługi HDInsight, które współużytkują pojedyncze konto Data Lake Storage. Aby uzyskać instrukcje dotyczące sposobu tworzenia klastra usługi HDInsight z Data Lake Storage jako magazyn, zobacz [Szybki Start: Konfigurowanie klastrów w usłudze HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

Ten artykuł zawiera zalecenia dla administratora Data Lake Storage w celu skonfigurowania jednego i udostępnionego konta Data Lake Storage, które może być używane w wielu **aktywnych** klastrach usługi HDInsight. Te zalecenia dotyczą hostingu wielu bezpiecznych i niezabezpieczonych klastrów Apache Hadoop na współużytkowanym koncie Data Lake Storage.

## <a name="data-lake-storage-file-and-folder-level-acls"></a>Data Lake Storage listy ACL na poziomie plików i folderów

W pozostałej części tego artykułu założono, że masz dobrą wiedzę na temat list ACL na poziomie plików i folderów w Azure Data Lake Storage, które opisano szczegółowo w temacie [Kontrola dostępu w Azure Data Lake Storage](../data-lake-store/data-lake-store-access-control.md).

## <a name="data-lake-storage-setup-for-multiple-hdinsight-clusters"></a>Konfiguracja Data Lake Storage dla wielu klastrów usługi HDInsight

Poinformuj nas o dwóch poziomach hierarchii folderów, aby wyjaśnić zalecenia dotyczące używania wielu klastrów usługi HDInsight z kontem Data Lake Storage. Weź pod uwagę, że masz konto Data Lake Storage o strukturze folderów **/Clusters/Finance**. W tej strukturze wszystkie klastry wymagane przez organizację finansową mogą używać/Clusters/Finance jako lokalizacji przechowywania. W przyszłości, jeśli inna organizacja, powiedzmy, chce utworzyć klastry usługi HDInsight przy użyciu tego samego konta Data Lake Storage, mogą tworzyć/Clusters/Marketing. Na razie Użyjmy już **/Clusters/Finance**.

Aby umożliwić efektywne korzystanie z tej struktury folderów przez klastry usługi HDInsight, administrator Data Lake Storage musi przypisać odpowiednie uprawnienia, zgodnie z opisem w tabeli. Uprawnienia wyświetlane w tabeli odpowiadają listom kontroli dostępu i nie są domyślnymi listami ACL.

|Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Nazwany użytkownik | Uprawnienia nazwanego użytkownika | Nazwana grupa | Uprawnienia grupy nazwanej |
|---------|---------|---------|---------|---------|---------|---------|---------|
|/ | rwxr-x--x  |administrator |administrator  |Jednostka usługi |--x  |FINGRP   |r-x         |
|/clusters | rwxr-x--x |administrator |administrator |Jednostka usługi |--x  |FINGRP |r-x         |
|/clusters/finance | rwxr-x--t |administrator |FINGRP  |Jednostka usługi |RWX  |-  |-     |

W tabeli,

- **administrator** jest twórcą i administratorem konta Data Lake Storage.
- Nazwa **główna usługi** jest jednostką usługi Azure Active Directory (AAD) skojarzoną z kontem.
- **FINGRP** to grupa użytkowników utworzona w usłudze AAD, która zawiera użytkowników z organizacji finansów.

Aby uzyskać instrukcje dotyczące sposobu tworzenia aplikacji usługi AAD (która również tworzy jednostkę nazwy głównej), zobacz [Tworzenie aplikacji w usłudze AAD](../active-directory/develop/howto-create-service-principal-portal.md#register-an-application-with-azure-ad-and-create-a-service-principal). Aby uzyskać instrukcje dotyczące sposobu tworzenia grupy użytkowników w usłudze AAD, zobacz [Zarządzanie grupami w Azure Active Directory](../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).

Niektóre kluczowe kwestie, które należy wziąć pod uwagę.

- **Aby można było używać konta** magazynu dla klastrów, należy utworzyć i zainicjować obsługę Data Lake Storage administracyjną w ramach struktury folderów dwóch poziomów (**/Clusters/Finance/**). Ta struktura nie jest tworzona automatycznie podczas tworzenia klastrów.
- W powyższym przykładzie zaleca się ustawienie grupy będącej właścicielem **/Clusters/Finance** jako **FINGRP** i umożliwienie dostępu do **języka r-x** FINGRP do całej hierarchii folderów, rozpoczynając od elementu głównego. Dzięki temu członkowie FINGRP mogą nawigować po strukturze folderów, rozpoczynając od elementu głównego.
- W przypadku, gdy różne jednostki usługi AAD mogą tworzyć klastry w obszarze **/Clusters/Finance**, to bit (w folderze **finansowym** ) gwarantuje, że foldery utworzone przez jedną jednostkę usługi nie mogą zostać usunięte.
- Po utworzeniu struktury folderów i uprawnień proces tworzenia klastra usługi HDInsight tworzy lokalizację magazynu specyficzną dla klastra w obszarze **/Clusters/Finance/**. Na przykład magazyn dla klastra o nazwie fincluster01 może być **/Clusters/Finance/fincluster01**. W poniższej tabeli przedstawiono własność i uprawnienia dla folderów utworzonych przez klaster usługi HDInsight.

    |Folder  |Uprawnienia  |Użytkownik będący właścicielem  |Grupa będąca właścicielem  | Nazwany użytkownik | Uprawnienia nazwanego użytkownika | Nazwana grupa | Uprawnienia grupy nazwanej |
    |---------|---------|---------|---------|---------|---------|---------|---------|
    |/clusters/finanace/ fincluster01 | rwxr-x---  |Jednostka usługi |FINGRP  |- |-  |-   |-  |

## <a name="recommendations-for-job-input-and-output-data"></a>Zalecenia dotyczące danych wejściowych i wyjściowych zadania

Firma Microsoft zaleca, aby dane wejściowe do zadania i wyniki z zadania były przechowywane w folderze poza **/Clusters**. Pozwala to zagwarantować, że nawet jeśli folder specyficzny dla klastra zostanie usunięty w celu Odbierz miejsce do magazynowania, dane wejściowe i wyjściowe zadania są nadal dostępne do użycia w przyszłości. W takim przypadku upewnij się, że hierarchia folderów służąca do przechowywania danych wejściowych i wyjściowych zadań umożliwia odpowiedni poziom dostępu dla jednostki usługi.

## <a name="limit-on-clusters-sharing-a-single-storage-account"></a>Ograniczenie klastrów współużytkujących pojedyncze konto magazynu

Limit liczby klastrów, które mogą współużytkować pojedyncze konto Data Lake Storage, zależy od obciążenia uruchamianego w tych klastrach. Zbyt wiele klastrów lub bardzo duże obciążenia klastrów, które współużytkują konto magazynu, mogą spowodować ograniczenie przepustowości konta magazynu/ruchu wychodzącego.

## <a name="support-for-default-acls"></a>Obsługa Default-ACLs

Podczas tworzenia jednostki usługi z dostępem do określonego użytkownika (jak pokazano w powyższej tabeli) zaleca się, aby **nie** dodawać użytkownika nazwanego z domyślnym listą ACL. Inicjowanie dostępu użytkownika o nazwie przy użyciu domyślnych list ACL skutkuje przypisaniem uprawnień 770 dla użytkownika, grupy będącej właścicielem i innych użytkowników. Ta wartość domyślna 770 nie ma uprawnień od użytkownika będącego właścicielem (7) lub należących do grupy (7), więc odbierze wszystkie uprawnienia dla innych (0). W wyniku tego występuje znany problem z konkretnym przypadkiem użycia, który został szczegółowo omówiony w sekcji [znane problemy i obejścia](#known-issues-and-workarounds) .

## <a name="known-issues-and-workarounds"></a>Znane problemy i rozwiązania

Ta sekcja zawiera listę znanych problemów dotyczących używania usługi HDInsight z usługą Data Lake Storage i ich obejścia.

### <a name="publicly-visible-localized-apache-hadoop-yarn-resources"></a>Publicznie widoczne zlokalizowane zasoby PRZĘDZy Apache Hadoop

Po utworzeniu nowego konta Azure Data Lake Storage katalog główny zostanie automatycznie zainicjowany przy użyciu bitów uprawnień dostępu-ACL ustawionej na 770. Użytkownik będący właścicielem folderu głównego jest ustawiony na użytkownika, który utworzył konto (administrator Data Lake Storage), a grupa będąca właścicielem jest ustawiona na grupę podstawową użytkownika, który utworzył konto. Nie podano dostępu dla "innych".

Te ustawienia mają wpływ na jeden przypadek użycia usługi HDInsight przechwytywany w [przędze 247](https://hwxmonarch.atlassian.net/browse/YARN-247). Przesyłanie zadań może zakończyć się niepowodzeniem z komunikatem o błędzie podobnym do przedstawionego poniżej:

```output
Resource XXXX is not publicly accessible and as such cannot be part of the public cache.
```

Jak określono w połączonej wcześniej JIRA PRZĘDZy, podczas lokalizowania zasobów publicznych lokalizator sprawdza, czy wszystkie żądane zasoby są rzeczywiście publiczne, sprawdzając ich uprawnienia do zdalnego systemu plików. Wszystkie LocalResource, które nie pasują do tego warunku, są odrzucane dla lokalizacji. Sprawdzanie uprawnień, obejmuje dostęp do odczytu do pliku dla "inne". Ten scenariusz nie działa w przypadku hostowania klastrów usługi HDInsight na Azure Data Lake, ponieważ Azure Data Lake nie zezwala na dostęp do innych użytkowników na poziomie folderu głównego.

#### <a name="workaround"></a>Obejście

Ustaw uprawnienia do odczytu dla **innych użytkowników** w hierarchii, na przykład, **/** **/Clusters** i **/Clusters/Finance** , jak pokazano w powyższej tabeli.

## <a name="see-also"></a>Zobacz też

- [Szybki start: konfigurowanie klastrów w usłudze HDInsight](./hdinsight-hadoop-provision-linux-clusters.md)
- [Korzystanie z usługi Azure Data Lake Storage Gen2 w połączeniu z klastrami usługi Azure HDInsight](hdinsight-hadoop-use-data-lake-storage-gen2.md)