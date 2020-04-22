---
title: Tworzenie akcji skryptów w celu dostosowania klastrów usługi Azure HDInsight
description: Dowiedz się, jak dostosować klastry usługi HDInsight za pomocą skryptów Bash. Akcje skryptów umożliwiają uruchamianie skryptów podczas tworzenia klastra lub po jego zakończeniu w celu zmiany ustawień konfiguracji klastra lub zainstalowania dodatkowego oprogramowania.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.date: 11/28/2019
ms.openlocfilehash: db37a56ffbf0cb64530f8f7af38841bac72c77d4
ms.sourcegitcommit: d57d2be09e67d7afed4b7565f9e3effdcc4a55bf
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/22/2020
ms.locfileid: "81767540"
---
# <a name="script-action-development-with-hdinsight"></a>Tworzenie akcji skryptów za pomocą rozwiązania HDInsight

Dowiedz się, jak dostosować klaster HDInsight przy użyciu skryptów Bash. Akcje skryptów są sposobem dostosowania usługi HDInsight podczas tworzenia klastra lub po jego zakończeniu.

## <a name="what-are-script-actions"></a>Co to są akcje skryptu

Akcje skryptów to skrypty Bash, które platforma Azure jest uruchamiana w węzłach klastra w celu wprowadzania zmian konfiguracji lub instalowania oprogramowania. Akcja skryptu jest wykonywana jako główny i zapewnia pełne prawa dostępu do węzłów klastra.

Akcje skryptu można zastosować za pomocą następujących metod:

| Ta metoda służy do stosowania skryptu... | Podczas tworzenia klastra... | W uruchomionym klastrze... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Klasyczny interfejs wiersza polecenia platformy Azure |&nbsp; |✓ |
| HDInsight .NET SDK |✓ |✓ |
| Szablon usługi Azure Resource Manager |✓ |&nbsp; |

Aby uzyskać więcej informacji na temat używania tych metod do stosowania akcji skryptów, zobacz [Dostosowywanie klastrów HDInsight przy użyciu akcji skryptów](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Najważniejsze wskazówki dotyczące tworzenia skryptów

Podczas opracowywania skryptu niestandardowego dla klastra HDInsight, istnieje kilka najlepszych rozwiązań, o których należy pamiętać:

* [Wyceluj w wersję Apache Hadoop](#bPS1)
* [Kierowanie na wersję systemu operacyjnego](#bps10)
* [Zapewnianie stabilnych łączy do zasobów skryptów](#bPS2)
* [Korzystanie ze wstępnie skompilowanych zasobów](#bPS4)
* [Upewnij się, że skrypt dostosowywania klastra jest idempotentny](#bPS3)
* [Zapewnienie wysokiej dostępności architektury klastra](#bPS5)
* [Konfigurowanie składników niestandardowych do używania magazynu obiektów blob platformy Azure](#bPS6)
* [Zapis informacji do STDOUT i STDERR](#bPS7)
* [Zapisywanie plików jako ASCII z końcówkami linii LF](#bps8)
* [Użyj logiki ponawiania próby, aby odzyskać dane po błędach przejściowych](#bps9)

> [!IMPORTANT]  
> Akcje skryptu muszą zostać ukończone w ciągu 60 minut lub proces zakończy się niepowodzeniem. Podczas inicjowania obsługi administracyjnej węzła skrypt jest uruchamiany jednocześnie z innymi procesami konfiguracji i konfiguracji. Konkurencja o zasoby, takie jak czas procesora LUB przepustowość sieci, może spowodować, że skrypt zakończy się dłużej niż w środowisku programistycznym.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Wyceluj w wersję Apache Hadoop

Różne wersje hdinsight mają różne wersje usług i składników Hadoop zainstalowanych. Jeśli skrypt oczekuje określonej wersji usługi lub składnika, należy używać tylko skrypt z wersją programu HDInsight, która zawiera wymagane składniki. Informacje na temat wersji składników dołączonych do programu HDInsight można znaleźć za pomocą dokumentu [przechowywania wersji komponentu HDInsight.](hdinsight-component-versioning.md)

### <a name="checking-the-operating-system-version"></a>Sprawdzanie wersji systemu operacyjnego

Różne wersje HDInsight opierają się na określonych wersjach Ubuntu. Mogą występować różnice między wersjami systemu operacyjnego, które należy sprawdzić w skrypcie. Na przykład może być konieczne zainstalowanie pliku binarnego powiązanego z wersją Ubuntu.

Aby sprawdzić wersję systemu `lsb_release`operacyjnego, użyj programu . Na przykład poniższy skrypt pokazuje, jak odwoływać się do określonego pliku tar w zależności od wersji systemu operacyjnego:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
```

### <a name="target-the-operating-system-version"></a><a name="bps10"></a>Kierowanie na wersję systemu operacyjnego

HDInsight jest oparty na dystrybucji Ubuntu Linux. Różne wersje programu HDInsight opierają się na różnych wersjach Ubuntu, co może zmienić zachowanie skryptu. Na przykład HDInsight 3.4 i starsze są oparte na wersjach Ubuntu, które używają Upstart. Wersje 3.5 i nowsze są oparte na Ubuntu 16.04, który używa Systemd. Systemd i Upstart polegają na różnych poleceniach, więc skrypt powinien być napisany do pracy z obydwoma.

Inną ważną różnicą między HDInsight 3.4 i `JAVA_HOME` 3.5 jest to, że teraz wskazuje na Java 8. Poniższy kod pokazuje, jak ustalić, czy skrypt jest uruchomiony na Ubuntu 14 lub 16:

```bash
OS_VERSION=$(lsb_release -sr)
if [[ $OS_VERSION == 14* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-14-04."
    HUE_TARFILE=hue-binaries-14-04.tgz
elif [[ $OS_VERSION == 16* ]]; then
    echo "OS version is $OS_VERSION. Using hue-binaries-16-04."
    HUE_TARFILE=hue-binaries-16-04.tgz
fi
...
if [[ $OS_VERSION == 16* ]]; then
    echo "Using systemd configuration"
    systemctl daemon-reload
    systemctl stop webwasb.service    
    systemctl start webwasb.service
else
    echo "Using upstart configuration"
    initctl reload-configuration
    stop webwasb
    start webwasb
fi
...
if [[ $OS_VERSION == 14* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-7-openjdk-amd64
elif [[ $OS_VERSION == 16* ]]; then
    export JAVA_HOME=/usr/lib/jvm/java-8-openjdk-amd64
fi
```

Pełny skrypt zawierający te fragmenty kodu https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.shmożna znaleźć na stronie .

Dla wersji Ubuntu, który jest używany przez HDInsight, zobacz dokument [wersji składnika HDInsight.](hdinsight-component-versioning.md)

Aby zrozumieć różnice między systemd i upstart, zobacz [Systemd dla początkującym użytkowników](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Zapewnianie stabilnych łączy do zasobów skryptów

Skrypt i skojarzone zasoby muszą pozostać dostępne przez cały okres istnienia klastra. Te zasoby są wymagane, jeśli nowe węzły są dodawane do klastra podczas operacji skalowania.

Najlepszym rozwiązaniem jest pobieranie i archiwizowanie wszystkiego na koncie usługi Azure Storage w ramach subskrypcji.

> [!IMPORTANT]  
> Używane konto magazynu musi być domyślnym kontem magazynu dla klastra lub publicznym kontenerem tylko do odczytu na dowolnym innym koncie magazynu.

Na przykład przykład przykłady dostarczone przez [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) firmę Microsoft są przechowywane na koncie magazynu. Ta lokalizacja jest publiczny, tylko do odczytu kontenera obsługiwane przez zespół HDInsight.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Korzystanie ze wstępnie skompilowanych zasobów

Aby skrócić czas potrzebny do uruchomienia skryptu, należy unikać operacji, które skompilować zasoby z kodu źródłowego. Na przykład wstępnie skompilować zasoby i przechowywać je w obiekcie blob konta usługi Azure Storage w tym samym centrum danych co usługa HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Upewnij się, że skrypt dostosowywania klastra jest idempotentny

Skrypty muszą być idempotentne. Jeśli skrypt jest uruchamiany wiele razy, należy zwrócić klaster do tego samego stanu za każdym razem.

Na przykład skrypt, który modyfikuje pliki konfiguracyjne nie należy dodawać zduplikowane wpisy, jeśli był uruchomiony wiele razy.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Zapewnienie wysokiej dostępności architektury klastra

Klastry HDInsight oparte na systemie Linux zapewniają dwa węzły głównego, które są aktywne w klastrze, a akcje skryptów są uruchamiane w obu węzłach. Jeśli instalowane składniki oczekują tylko jednego węzła głównego, nie instaluj składników w obu węzłach głównego.

> [!IMPORTANT]  
> Usługi świadczone w ramach usługi HDInsight są przeznaczone do pracy awaryjnej między dwoma węzłami głównymi, zgodnie z potrzebami. Ta funkcja nie jest rozszerzana na składniki niestandardowe zainstalowane za pomocą akcji skryptu. Jeśli potrzebujesz wysokiej dostępności dla składników niestandardowych, należy zaimplementować własny mechanizm pracy awaryjnej.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Konfigurowanie składników niestandardowych do używania magazynu obiektów blob platformy Azure

Składniki instalowane w klastrze mogą mieć domyślną konfigurację, która używa magazynu HDFS (Apache Hadoop Distributed File System). Usługa HDInsight używa usługi Azure Storage lub Data Lake Storage jako magazynu domyślnego. Oba zapewniają system plików zgodny z systemem PLIKÓW HDFS, który utrzymuje dane, nawet jeśli klaster zostanie usunięty. Może być konieczne skonfigurowanie instalowane składniki do używania wasb lub ADL zamiast plików HDFS.

W przypadku większości operacji nie trzeba określać systemu plików. Na przykład następujące kopie plik hadoop-common.jar z lokalnego systemu plików do magazynu klastra:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

W tym przykładzie polecenie w `hdfs` sposób przejrzysty używa domyślnego magazynu klastra. W przypadku niektórych operacji może być konieczne określenie identyfikatora URI. Na przykład `adl:///example/jars` dla usługi Azure Data `abfs:///example/jars` Lake Storage Gen1 `wasb:///example/jars` dla usługi Data Lake Storage Gen2 lub usługi Azure Storage.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Zapis informacji do STDOUT i STDERR

HDInsight rejestruje dane wyjściowe skryptu, który jest zapisywany do STDOUT i STDERR. Informacje te można wyświetlić za pomocą interfejsu użytkownika sieci Web Ambari.

> [!NOTE]  
> Apache Ambari jest dostępny tylko wtedy, gdy klaster został pomyślnie utworzony. Jeśli używasz akcji skryptu podczas tworzenia klastra, a tworzenie zakończy się [niepowodzeniem, zobacz Rozwiązywanie problemów z akcjami skryptu,](./troubleshoot-script-action.md) aby uzyskać inne sposoby uzyskiwania dostępu do zarejestrowanych informacji.

Większość narzędzi i pakietów instalacyjnych już zapisuje informacje do STDOUT i STDERR, jednak można dodać dodatkowe rejestrowanie. Aby wysłać tekst do `echo`STDOUT, użyj pliku . Przykład:

```bash
echo "Getting ready to install Foo"
```

Domyślnie `echo` wysyła ciąg do STDOUT. Aby skierować go do `>&2` STDERR, dodaj przed `echo`. Przykład:

```bash
>&2 echo "An error occurred installing Foo"
```

Spowoduje to przekierowanie informacji zapisanych do STDOUT do STDERR (2). Aby uzyskać więcej informacji na [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html)temat przekierowania we/wy, zobacz .

Aby uzyskać więcej informacji na temat wyświetlania informacji zarejestrowanych przez akcje skryptu, zobacz [Rozwiązywanie problemów z akcjami skryptu](./troubleshoot-script-action.md).

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a>Zapisywanie plików jako ASCII z końcówkami linii LF

Skrypty Bash powinny być przechowywane w formacie ASCII, z liniami zakończonymi przez LF. Pliki przechowywane jako UTF-8 lub crlf jako zakończenie wiersza mogą zakończyć się niepowodzeniem z następującym błędem:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a>Użyj logiki ponawiania próby, aby odzyskać dane po błędach przejściowych

Podczas pobierania plików, instalowania pakietów przy użyciu apt-get lub innych akcji, które przesyłają dane przez Internet, akcja może zakończyć się niepowodzeniem z powodu przejściowych błędów sieciowych. Na przykład zdalny zasób, z którego komunikujesz się, może być w trakcie wykonywania awarii do węzła kopii zapasowej.

Aby skrypt był odporny na błędy przejściowe, można zaimplementować logikę ponawiania. Poniższa funkcja pokazuje, jak zaimplementować logikę ponawiania. Ponawia operację trzy razy przed niepowodzeniem.

```bash
#retry
MAXATTEMPTS=3

retry() {
    local -r CMD="$@"
    local -i ATTMEPTNUM=1
    local -i RETRYINTERVAL=2

    until $CMD
    do
        if (( ATTMEPTNUM == MAXATTEMPTS ))
        then
                echo "Attempt $ATTMEPTNUM failed. no more attempts left."
                return 1
        else
                echo "Attempt $ATTMEPTNUM failed! Retrying in $RETRYINTERVAL seconds..."
                sleep $(( RETRYINTERVAL ))
                ATTMEPTNUM=$ATTMEPTNUM+1
        fi
    done
}
```

Poniższe przykłady pokazują, jak korzystać z tej funkcji.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Metody pomocnicze skryptów niestandardowych

Metody pomocnicze akcji skryptu są narzędzia, które można używać podczas pisania skryptów niestandardowych. Metody te są zawarte [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) w skrypcie. Użyj następujących elementów, aby pobrać je i używać jako część skryptu:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Następujące pomoce są dostępne do użycia w skrypcie:

| Użycie pomocnika | Opis |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Pobiera plik ze źródłowego identyfikatora URI do określonej ścieżki pliku. Domyślnie nie zastępuje istniejącego pliku. |
| `untar_file TARFILE DESTDIR` |Wyodrębnia plik tar `-xf`(za pomocą ) do katalogu docelowego. |
| `test_is_headnode` |Jeśli jest uruchomiony w węźle głównym klastra, zwraca 1; w przeciwnym razie 0. |
| `test_is_datanode` |Jeśli bieżący węzeł jest węzłem danych (procesu roboczego), zwróć 1; w przeciwnym razie 0. |
| `test_is_first_datanode` |Jeśli bieżący węzeł jest pierwszym węzłem danych (roboczym) (o nazwie workernode0), zwróć 1; w przeciwnym razie 0. |
| `get_headnodes` |Zwraca w pełni kwalifikowaną nazwę domeny headnodes w klastrze. Nazwy są rozdzielane przecinkami. Pusty ciąg jest zwracany po błędzie. |
| `get_primary_headnode` |Pobiera w pełni kwalifikowaną nazwę domeny głównego. Pusty ciąg jest zwracany po błędzie. |
| `get_secondary_headnode` |Pobiera w pełni kwalifikowaną nazwę domeny pomocniczego headnode. Pusty ciąg jest zwracany po błędzie. |
| `get_primary_headnode_number` |Pobiera sufiks numeryczny głównegonia. Pusty ciąg jest zwracany po błędzie. |
| `get_secondary_headnode_number` |Pobiera numeryczny sufiks pomocniczego headnode. Pusty ciąg jest zwracany po błędzie. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Typowe wzorce użycia

Ta sekcja zawiera wskazówki dotyczące implementowania niektórych typowych wzorców użycia, które można napotkać podczas pisania własnego skryptu niestandardowego.

### <a name="passing-parameters-to-a-script"></a>Przekazywanie parametrów do skryptu

W niektórych przypadkach skrypt może wymagać parametrów. Na przykład może być potrzebne hasło administratora klastra podczas korzystania z interfejsu API AMbari REST.

Parametry przekazywane do skryptu są nazywane *parametrami pozycyjnymi*i są przypisywane do `$1` pierwszego parametru, `$2` dla drugiego i tak włączonego. `$0`zawiera nazwę samego skryptu.

Wartości przekazywane do skryptu jako parametry powinny być ujęte w pojedyncze cudzysłowy ('). Dzięki temu wartość przekazana jest traktowana jako literał.

### <a name="setting-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ustawienie zmiennej środowiskowej odbywa się za pomocą następującej instrukcji:

    VARIABLENAME=value

Gdzie VARIABLENAME jest nazwą zmiennej. Aby uzyskać dostęp `$VARIABLENAME`do zmiennej, użyj pliku . Na przykład, aby przypisać wartość dostarczoną przez parametr pozycyjny jako zmienną środowiskową o nazwie PASSWORD, należy użyć następującej instrukcji:

    PASSWORD=$1

Kolejny dostęp do informacji `$PASSWORD`może następnie użyć .

Zmienne środowiskowe ustawione w skrypcie istnieją tylko w zakresie skryptu. W niektórych przypadkach może być konieczne dodanie zmiennych środowiskowych dla całego systemu, które będą się utrwalić po zakończeniu skryptu. Aby dodać zmienne środowiskowe dla całego `/etc/environment`systemu, dodaj zmienną do . Na przykład następująca `HADOOP_CONF_DIR`instrukcja dodaje:

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Dostęp do lokalizacji, w których przechowywane są skrypty niestandardowe

Skrypty używane do dostosowywania klastra muszą być przechowywane w jednej z następujących lokalizacji:

* __Konto usługi Azure Storage__ skojarzone z klastrem.

* __Dodatkowe konto magazynu__ skojarzone z klastrem.

* __Publicznie czytelny identyfikator URI__. Na przykład adres URL do danych przechowywanych w usłudze OneDrive, Dropbox lub innej usłudze hostingu plików.

* __Konto usługi Azure Data Lake Storage__ skojarzone z klastrem USŁUGI HDInsight. Aby uzyskać więcej informacji na temat korzystania z usługi Azure Data Lake Storage z usługą HDInsight, zobacz [Szybki start: Konfigurowanie klastrów w programie HDInsight](../storage/data-lake-storage/quickstart-create-connect-hdi-cluster.md).

    > [!NOTE]  
    > Jednostkowy usługa HDInsight używa dostępu do magazynu usługi Lake musi mieć dostęp do odczytu do skryptu.

Zasoby używane przez skrypt muszą być również publicznie dostępne.

Przechowywanie plików na koncie usługi Azure Storage lub usłudze Azure Data Lake Storage zapewnia szybki dostęp, zarówno w sieci platformy Azure.

> [!NOTE]  
> Format identyfikatora URI używany do odwoływania się do skryptu różni się w zależności od używanej usługi. W przypadku kont magazynu skojarzonych z `wasb://` klastrem HDInsight należy użyć lub `wasbs://`użyć programu . W przypadku publicznie czytelnych identyfikatorów `https://`URI należy użyć lub . `http://` W przypadku magazynu `adl://`usługi Data Lake należy użyć pliku .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Lista kontrolna wdrażania akcji skryptu

Oto kroki podejmowane podczas przygotowywania do wdrożenia skryptu:

* Umieść pliki zawierające skrypty niestandardowe w miejscu, które jest dostępne dla węzłów klastra podczas wdrażania. Na przykład domyślny magazyn dla klastra. Pliki mogą być również przechowywane w publicznie czytelnych usługach hostingowych.
* Sprawdź, czy skrypt jest idempotentny. Dzięki temu skrypt ma być wykonywany wiele razy w tym samym węźle.
* Użyj tymczasowego katalogu plików /tmp, aby zachować pobrane pliki używane przez skrypty, a następnie oczyścić je po wykonaniu skryptów.
* Jeśli ustawienia na poziomie systemu operacyjnego lub pliki konfiguracyjne usługi Hadoop zostaną zmienione, można ponownie uruchomić usługi HDInsight.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Jak uruchomić akcję skryptu

Akcji skryptów można użyć do dostosowania klastrów usługi HDInsight przy użyciu następujących metod:

* Azure Portal
* Azure PowerShell
* Szablony usługi Azure Resource Manager
* Plik SDK HDInsight .NET.

Aby uzyskać więcej informacji na temat korzystania z każdej metody, zobacz [Jak używać akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Niestandardowe przykłady skryptów

Firma Microsoft udostępnia przykładowe skrypty do instalowania składników w klastrze HDInsight. Zobacz [Instalowanie i używanie barwy w klastrach HDInsight](hdinsight-hadoop-hue-linux.md) jako przykładowa akcja skryptu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono błędy, które mogą wystąpić podczas korzystania z opracowanych skryptów:

**Błąd** `$'\r': command not found`: . Czasami następuje `syntax error: unexpected end of file`.

*Przyczyna:* Ten błąd jest spowodowany, gdy wiersze w skrypcie kończą się CRLF. Systemy Unix oczekują tylko LF jako zakończenia linii.

Ten problem najczęściej występuje, gdy skrypt jest napisany w środowisku systemu Windows, ponieważ CRLF jest wspólnym wierszem kończącym się dla wielu edytorów tekstu w systemie Windows.

*Rozdzielczość:* Jeśli jest to opcja w edytorze tekstu, wybierz format Uniksa lub LF dla zakończenia wiersza. Można również użyć następujących poleceń w systemie Unix, aby zmienić CRLF na LF:

> [!NOTE]  
> Następujące polecenia są w przybliżeniu równoważne, ponieważ należy zmienić zakończenia wiersza CRLF na LF. Wybierz jeden na podstawie narzędzi dostępnych w systemie.

| Polecenie | Uwagi |
| --- | --- |
| `unix2dos -b INFILE` |Kopia zapasowa oryginalnego pliku jest zabezpieczonych programem . Rozszerzenie BAK |
| `tr -d '\r' < INFILE > OUTFILE` |OUTFILE zawiera wersję z tylko końcówkami LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modyfikuje plik bezpośrednio |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |OUTFILE zawiera wersję z tylko końcówkami LF. |

**Błąd** `line 1: #!/usr/bin/env: No such file or directory`: .

*Przyczyna:* Ten błąd występuje, gdy skrypt został zapisany jako UTF-8 z oznaczenia zamówienia bajtowego (BOM).

*Rozdzielczość*: Zapisz plik jako ASCII lub jako UTF-8 bez BOM.Resolution : Save the file either ascii, or as UTF-8 without a BOM. Do utworzenia pliku bez BOM-u można również użyć następującego polecenia w systemie Linux lub Unix:

    awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE

Zastąp `INFILE` plikiem zawierającym BOM. `OUTFILE`powinna być nowa nazwa pliku, która zawiera skrypt bez BOM.

## <a name="next-steps"></a><a name="seeAlso"></a>Następne kroki

* Dowiedz się, jak [dostosować klastry HDInsight przy użyciu akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
* Użyj [odwołania do sdk HDInsight .NET,](https://docs.microsoft.com/dotnet/api/overview/azure/hdinsight) aby dowiedzieć się więcej o tworzeniu aplikacji platformy .NET, które zarządzają programem HDInsight
* Użyj [interfejsu API REST usługi HDInsight,](https://msdn.microsoft.com/library/azure/mt622197.aspx) aby dowiedzieć się, jak używać rest do wykonywania akcji zarządzania w klastrach HDInsight.
