---
title: Opracowywanie akcji skryptu w celu dostosowania klastrów usługi Azure HDInsight
description: Dowiedz się, jak dostosowywać klastry usługi HDInsight przy użyciu skryptów bash. Akcje skryptu umożliwiają uruchamianie skryptów podczas tworzenia klastra lub po nim, aby zmienić ustawienia konfiguracji klastra lub zainstalować dodatkowe oprogramowanie.
ms.service: hdinsight
ms.topic: how-to
ms.date: 11/28/2019
ms.openlocfilehash: b6705728fddc9a5a3c9cb8eb2f1811412fb3a290
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98945481"
---
# <a name="script-action-development-with-hdinsight"></a>Tworzenie akcji skryptu za pomocą usługi HDInsight

Dowiedz się, jak dostosować klaster usługi HDInsight przy użyciu skryptów bash. Akcje skryptu są sposobem na dostosowanie usługi HDInsight podczas tworzenia klastra lub po nim.

## <a name="what-are-script-actions"></a>Co to są akcje skryptu

Akcje skryptu to skrypty bash uruchamiane przez platformę Azure w węzłach klastra w celu wprowadzenia zmian konfiguracji lub zainstalowania oprogramowania. Akcja skryptu jest wykonywana jako element główny i zapewnia pełne prawa dostępu do węzłów klastra.

Akcje skryptu można stosować przy użyciu następujących metod:

| Użyj tej metody, aby zastosować skrypt... | Podczas tworzenia klastra... | Na uruchomionym klastrze... |
| --- |:---:|:---:|
| Azure Portal |✓ |✓ |
| Azure PowerShell |✓ |✓ |
| Klasyczny interfejs wiersza polecenia platformy Azure |&nbsp; |✓ |
| Zestaw SDK usługi HDInsight dla platformy .NET |✓ |✓ |
| Szablon usługi Azure Resource Manager |✓ |&nbsp; |

Aby uzyskać więcej informacji o używaniu tych metod do zastosowania akcji skryptu, zobacz [Dostosowywanie klastrów usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="best-practices-for-script-development"></a><a name="bestPracticeScripting"></a>Najlepsze rozwiązania dotyczące tworzenia skryptów

Podczas tworzenia niestandardowego skryptu dla klastra usługi HDInsight należy pamiętać o kilku najlepszych rozwiązaniach:

* [Docelowa wersja Apache Hadoop](#bPS1)
* [Docelowa wersja systemu operacyjnego](#bps10)
* [Zapewnianie stabilnych linków do zasobów skryptów](#bPS2)
* [Korzystanie z wstępnie skompilowanych zasobów](#bPS4)
* [Upewnij się, że skrypt dostosowywania klastra jest idempotentne](#bPS3)
* [Zapewnienie wysokiej dostępności architektury klastra](#bPS5)
* [Konfigurowanie niestandardowych składników do korzystania z usługi Azure Blob Storage](#bPS6)
* [Zapisz informacje w STDOUT i STDERR](#bPS7)
* [Zapisuj pliki jako ASCII z końcami wierszy LF](#bps8)
* [Użyj logiki ponawiania, aby odzyskać błędy przejściowe](#bps9)

> [!IMPORTANT]  
> Akcje skryptu muszą zakończyć się w ciągu 60 minut lub proces kończy się niepowodzeniem. Podczas aprowizacji węzła skrypt jest uruchamiany równolegle z innymi procesami instalacji i konfiguracji. Konkurs zasobów, takich jak czas procesora CPU lub przepustowość sieci, może spowodować, że wykonanie skryptu trwa dłużej niż w środowisku deweloperskim.

### <a name="target-the-apache-hadoop-version"></a><a name="bPS1"></a>Docelowa wersja Apache Hadoop

Różne wersje usługi HDInsight mają zainstalowane różne wersje usług Hadoop i składników. Jeśli skrypt oczekuje określonej wersji usługi lub składnika, należy używać skryptu tylko z wersją programu HDInsight, która zawiera wymagane składniki. Informacje o wersjach składników zawartych w usłudze HDInsight można znaleźć za pomocą dokumentu [wersji składnika usługi HDInsight](hdinsight-component-versioning.md) .

### <a name="checking-the-operating-system-version"></a>Sprawdzanie wersji systemu operacyjnego

Różne wersje usługi HDInsight polegają na określonych wersjach programu Ubuntu. W skrypcie mogą występować różnice między wersjami systemu operacyjnego, które należy wyszukać. Na przykład może być konieczne zainstalowanie pliku binarnego, który jest powiązany z wersją programu Ubuntu.

Aby sprawdzić wersję systemu operacyjnego, użyj programu `lsb_release` . Na przykład poniższy skrypt pokazuje, jak odwoływać się do określonego pliku TAR w zależności od wersji systemu operacyjnego:

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

### <a name="target-the-operating-system-version"></a><a name="bps10"></a> Docelowa wersja systemu operacyjnego

Usługa HDInsight jest oparta na dystrybucji Ubuntu Linux. Różne wersje usługi HDInsight są zależne od różnych wersji programu Ubuntu, co może zmienić sposób działania skryptu. Na przykład Usługa HDInsight 3,4 i wcześniejsza wersja bazują na wersjach Ubuntu korzystających z oprogramowania. Wersje 3,5 i nowsze są oparte na Ubuntu 16,04, który korzysta z systemu. System i początek są zależne od różnych poleceń, dlatego należy napisać skrypt do pracy z obydwoma.

Kolejną istotną różnicą między usługą HDInsight 3,4 i 3,5 jest `JAVA_HOME` teraz wskazuje Java 8. Poniższy kod ilustruje sposób ustalania, czy skrypt działa w Ubuntu 14 czy 16:

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

Pełny skrypt zawierający te fragmenty kodu można znaleźć pod adresem https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh .

W przypadku wersji programu Ubuntu używanej przez usługi HDInsight zapoznaj się z dokumentem [wersja składnika usługi HDInsight](hdinsight-component-versioning.md) .

Aby zrozumieć różnice między systemem i początkiem, zobacz [systemed dla użytkowników przeduruchomieniowych](https://wiki.ubuntu.com/SystemdForUpstartUsers).

### <a name="provide-stable-links-to-script-resources"></a><a name="bPS2"></a>Zapewnianie stabilnych linków do zasobów skryptów

Skrypt i powiązane zasoby muszą pozostać dostępne przez cały okres istnienia klastra. Te zasoby są wymagane, jeśli podczas operacji skalowania do klastra dodawane są nowe węzły.

Najlepszym rozwiązaniem jest pobranie i zarchiwizowanie wszystkich elementów na koncie usługi Azure Storage w ramach Twojej subskrypcji.

> [!IMPORTANT]  
> Używane konto magazynu musi być domyślnym kontem magazynu dla klastra lub publicznym kontenerem tylko do odczytu na dowolnym innym koncie magazynu.

Przykładowo przykłady dostarczone przez firmę Microsoft są przechowywane na [https://hdiconfigactions.blob.core.windows.net/](https://hdiconfigactions.blob.core.windows.net/) koncie magazynu. Ta lokalizacja jest publicznym, tylko do odczytu kontenerem obsługiwanym przez zespół usługi HDInsight.

### <a name="use-pre-compiled-resources"></a><a name="bPS4"></a>Korzystanie z wstępnie skompilowanych zasobów

Aby skrócić czas potrzebny na uruchomienie skryptu, należy unikać operacji, które kompilują zasoby z kodu źródłowego. Na przykład wstępne Kompilowanie zasobów i przechowywanie ich w obiekcie blob konta usługi Azure Storage w tym samym centrum danych co Usługa HDInsight.

### <a name="ensure-that-the-cluster-customization-script-is-idempotent"></a><a name="bPS3"></a>Upewnij się, że skrypt dostosowywania klastra jest idempotentne

Skrypty muszą być idempotentne. Jeśli skrypt zostanie uruchomiony wiele razy, powinien on zwrócić klaster do tego samego stanu za każdym razem.

Na przykład skrypt, który modyfikuje pliki konfiguracyjne, nie powinien dodawać zduplikowanych wpisów, jeśli są uruchamiane wiele razy.

### <a name="ensure-high-availability-of-the-cluster-architecture"></a><a name="bPS5"></a>Zapewnienie wysokiej dostępności architektury klastra

Klastry usługi HDInsight oparte na systemie Linux zapewniają dwa węzły główne, które są aktywne w klastrze, a akcje skryptów są uruchamiane na obu węzłach. Jeśli instalowane składniki oczekują tylko jednego węzła głównego, nie instaluj składników w obu węzłach głównych.

> [!IMPORTANT]  
> Usługi dostarczane jako część programu HDInsight są przeznaczone do trybu failover między dwoma węzłami głównymi, zgodnie z wymaganiami. Ta funkcja nie jest rozszerzona do składników niestandardowych zainstalowanych za pomocą akcji skryptu. Jeśli potrzebujesz wysokiej dostępności dla składników niestandardowych, musisz zaimplementować własny mechanizm pracy awaryjnej.

### <a name="configure-the-custom-components-to-use-azure-blob-storage"></a><a name="bPS6"></a>Konfigurowanie niestandardowych składników do korzystania z usługi Azure Blob Storage

Składniki instalowane w klastrze mogą mieć konfigurację domyślną korzystającą z magazynu Apache Hadoop rozproszony system plików (HDFS). Usługa HDInsight używa usługi Azure Storage lub Data Lake Storage jako magazynu domyślnego. Oba zapewniają system plików zgodny z systemem HDFS, który utrzymuje dane nawet w przypadku usunięcia klastra. Może być konieczne skonfigurowanie instalowanych składników do korzystania z WASB lub ADL zamiast systemu plików HDFS.

W przypadku większości operacji nie trzeba określać systemu plików. Na przykład poniższy kod kopiuje plik JAR Hadoop-Common z lokalnego systemu plików do magazynu klastra:

```bash
hdfs dfs -put /usr/hdp/current/hadoop-client/hadoop-common.jar /example/jars/
```

W tym przykładzie polecenie w sposób `hdfs` przezroczysty używa domyślnego magazynu klastra. W przypadku niektórych operacji może zajść potrzeba określenia identyfikatora URI. Na przykład dla `adl:///example/jars` Azure Data Lake Storage Gen1, `abfs:///example/jars` dla Data Lake Storage Gen2 lub `wasb:///example/jars` dla usługi Azure Storage.

### <a name="write-information-to-stdout-and-stderr"></a><a name="bPS7"></a>Zapisz informacje w STDOUT i STDERR

Usługa HDInsight rejestruje dane wyjściowe skryptu zapisane w strumieniach STDOUT i STDERR. Te informacje można wyświetlić za pomocą interfejsu użytkownika sieci Web Ambari.

> [!NOTE]  
> Apache Ambari jest dostępny tylko wtedy, gdy klaster został utworzony pomyślnie. Jeśli podczas tworzenia klastra używasz akcji skryptu, a tworzenie nie powiedzie się, zobacz [Rozwiązywanie problemów z skryptami](./troubleshoot-script-action.md) w celu uzyskania innych sposobów uzyskiwania dostępu do zarejestrowanych informacji.

Większość narzędzi i pakietów instalacyjnych już zapisują informacje w strumieniach STDOUT i STDERR, jednak możesz chcieć dodać dodatkowe rejestrowanie. Aby wysłać tekst do STDOUT, użyj `echo` . Na przykład:

```bash
echo "Getting ready to install Foo"
```

Domyślnie program `echo` wysyła ciąg do stdout. Aby skierować go do STDERR, Dodaj `>&2` przed `echo` . Na przykład:

```bash
>&2 echo "An error occurred installing Foo"
```

Przekierowuje informacje zapisywane w strumieniu STDOUT do STDERR (2). Aby uzyskać więcej informacji na temat przekierowania we/wy, zobacz [https://www.tldp.org/LDP/abs/html/io-redirection.html](https://www.tldp.org/LDP/abs/html/io-redirection.html) .

Aby uzyskać więcej informacji na temat wyświetlania informacji rejestrowanych przez akcje skryptu, zobacz [Rozwiązywanie problemów z skryptami](./troubleshoot-script-action.md).

### <a name="save-files-as-ascii-with-lf-line-endings"></a><a name="bps8"></a> Zapisuj pliki jako ASCII z końcami wierszy LF

Skrypty bash powinny być przechowywane w formacie ASCII, z wierszami zakończonymi znakami LF. Pliki, które są przechowywane jako UTF-8, lub użycie CRLF jako zakończenia wiersza mogą zakończyć się niepowodzeniem z powodu następującego błędu:

```
$'\r': command not found
line 1: #!/usr/bin/env: No such file or directory
```

### <a name="use-retry-logic-to-recover-from-transient-errors"></a><a name="bps9"></a> Użyj logiki ponawiania, aby odzyskać błędy przejściowe

Podczas pobierania plików, instalowania pakietów przy użyciu funkcji apt-get lub innych akcji, które przesyłają dane za pośrednictwem Internetu, działanie może zakończyć się niepowodzeniem z powodu przejściowych błędów sieci. Na przykład zasób zdalny, z którym nawiązujesz połączenie, może być w trakcie przełączenia w tryb failover do węzła kopii zapasowej.

Aby skrypt odporny na błędy przejściowe, można zaimplementować logikę ponawiania. Poniższa funkcja demonstruje sposób implementacji logiki ponawiania. Ponawia próbę wykonania operacji trzy razy przed niepowodzeniem.

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

W poniższych przykładach pokazano, jak używać tej funkcji.

```bash
retry ls -ltr foo

retry wget -O ./tmpfile.sh https://hdiconfigactions.blob.core.windows.net/linuxhueconfigactionv02/install-hue-uber-v02.sh
```

## <a name="helper-methods-for-custom-scripts"></a><a name="helpermethods"></a>Metody pomocnika dla skryptów niestandardowych

Metody pomocnika akcji skryptu to narzędzia, których można używać podczas pisania skryptów niestandardowych. Te metody są zawarte w [https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh](https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh) skrypcie. Użyj poniższego elementu, aby pobrać i użyć ich jako części skryptu:

```bash
# Import the helper method module.
wget -O /tmp/HDInsightUtilities-v01.sh -q https://hdiconfigactions.blob.core.windows.net/linuxconfigactionmodulev01/HDInsightUtilities-v01.sh && source /tmp/HDInsightUtilities-v01.sh && rm -f /tmp/HDInsightUtilities-v01.sh
```

Następujące pomocnicy są dostępni do użycia w skrypcie:

| Użycie pomocnika | Opis |
| --- | --- |
| `download_file SOURCEURL DESTFILEPATH [OVERWRITE]` |Pobiera plik ze źródłowego identyfikatora URI do określonej ścieżki pliku. Domyślnie nie zastępuje istniejącego pliku. |
| `untar_file TARFILE DESTDIR` |Wyodrębnia plik tar (przy użyciu `-xf` ) do katalogu docelowego. |
| `test_is_headnode` |Jeśli skrypt uruchomiono w węźle głównym klastra, zwróć 1; w przeciwnym razie 0. |
| `test_is_datanode` |Jeśli bieżący węzeł jest węzłem danych (Worker), zwróć 1; w przeciwnym razie 0. |
| `test_is_first_datanode` |Jeśli bieżącym węzłem jest pierwszy węzeł danych (Worker) (o nazwie workernode0), zwróć 1; w przeciwnym razie 0. |
| `get_headnodes` |Zwróć w pełni kwalifikowaną nazwę domeny węzłów głównych w klastrze. Nazwy są rozdzielane przecinkami. Podczas błędu zwracany jest pusty ciąg. |
| `get_primary_headnode` |Pobiera w pełni kwalifikowaną nazwę domeny głównej węzła głównego. Podczas błędu zwracany jest pusty ciąg. |
| `get_secondary_headnode` |Pobiera w pełni kwalifikowaną nazwę domeny pomocniczej węzła głównego. Podczas błędu zwracany jest pusty ciąg. |
| `get_primary_headnode_number` |Pobiera liczbowy sufiks węzła głównego podstawowego. Podczas błędu zwracany jest pusty ciąg. |
| `get_secondary_headnode_number` |Pobiera liczbowy sufiks pomocniczej węzła głównego. Podczas błędu zwracany jest pusty ciąg. |

## <a name="common-usage-patterns"></a><a name="commonusage"></a>Typowe wzorce użycia

Ta sekcja zawiera wskazówki dotyczące wdrażania niektórych typowych wzorców użycia, które można napotkać podczas pisania własnego skryptu niestandardowego.

### <a name="passing-parameters-to-a-script"></a>Przekazywanie parametrów do skryptu

W niektórych przypadkach skrypt może wymagać parametrów. Na przykład może być wymagane hasło administratora dla klastra podczas korzystania z interfejsu API REST Ambari.

Parametry przekazane do skryptu są znane jako *parametry pozycyjne* i są przypisywane do `$1` pierwszego parametru, `$2` dla drugiego i tak dalej. `$0` zawiera nazwę samego skryptu.

Wartości przesłane do skryptu jako parametry powinny być ujęte w cudzysłów ("). To gwarantuje, że przeniesiona wartość jest traktowana jako literał.

### <a name="setting-environment-variables"></a>Ustawianie zmiennych środowiskowych

Ustawienie zmiennej środowiskowej jest wykonywane przez następującą instrukcję:

```bash
VARIABLENAME=value
```

W poprzednim przykładzie `VARIABLENAME` jest nazwą zmiennej. Aby uzyskać dostęp do zmiennej, użyj `$VARIABLENAME` . Na przykład, aby przypisać wartość dostarczoną przez parametr pozycyjny jako zmienną środowiskową o nazwie PASSWORD, należy użyć następującej instrukcji:

```bash
PASSWORD=$1
```

Następnie można użyć kolejnego dostępu do informacji `$PASSWORD` .

Zmienne środowiskowe ustawione w skrypcie znajdują się tylko w zakresie skryptu. W niektórych przypadkach może być konieczne dodanie zmiennych środowiskowych dla całego systemu, które będą utrwalane po zakończeniu działania skryptu. Aby dodać zmienne środowiskowe dla całego systemu, Dodaj zmienną do `/etc/environment` . Na przykład następująca instrukcja dodaje `HADOOP_CONF_DIR` :

```bash
echo "HADOOP_CONF_DIR=/etc/hadoop/conf" | sudo tee -a /etc/environment
```

### <a name="access-to-locations-where-the-custom-scripts-are-stored"></a>Dostęp do lokalizacji, w których są przechowywane skrypty niestandardowe

Skrypty używane do dostosowywania klastra muszą być przechowywane w jednej z następujących lokalizacji:

* __Konto magazynu platformy Azure__ skojarzone z klastrem.

* __Dodatkowe konto magazynu__ skojarzone z klastrem.

* __Publicznie czytelny identyfikator URI__. Na przykład adres URL do danych przechowywanych w usłudze OneDrive, Dropbox lub w innej usłudze hostingu plików.

* __Konto Azure Data Lake Storage__ skojarzone z klastrem usługi HDInsight. Aby uzyskać więcej informacji na temat używania Azure Data Lake Storage z usługą HDInsight, zobacz [Szybki Start: Konfigurowanie klastrów w usłudze HDInsight](./hdinsight-hadoop-provision-linux-clusters.md).

    > [!NOTE]  
    > Główna Usługa HDInsight używa do uzyskiwania dostępu do Data Lake Storage musi mieć dostęp do odczytu do skryptu.

Zasoby używane przez skrypt również muszą być dostępne publicznie.

Przechowywanie plików na koncie usługi Azure Storage lub Azure Data Lake Storage zapewnia szybki dostęp w sieci platformy Azure.

> [!NOTE]  
> Format identyfikatora URI używany do odwoływania się do skryptu różni się w zależności od używanej usługi. W przypadku kont magazynu skojarzonych z klastrem usługi HDInsight Użyj `wasb://` lub `wasbs://` . W przypadku publicznie czytelnych identyfikatorów URI Użyj `http://` lub `https://` . Aby uzyskać Data Lake Storage, użyj `adl://` .

## <a name="checklist-for-deploying-a-script-action"></a><a name="deployScript"></a>Lista kontrolna wdrażania akcji skryptu

Poniżej przedstawiono kroki, które należy wykonać podczas przygotowywania do wdrożenia skryptu:

* Umieść pliki, które zawierają skrypty niestandardowe w miejscu dostępnym dla węzłów klastra podczas wdrażania. Na przykład domyślnym magazynem klastra. Pliki mogą być również przechowywane w publicznie czytelnych usługach hostingu.
* Sprawdź, czy skrypt jest idempotentne. Dzięki temu skrypt może być wykonywany wiele razy w tym samym węźle.
* Użyj tymczasowego katalogu plików/tmp, aby zachować pobrane pliki używane przez skrypty, a następnie Oczyść je po wykonaniu skryptów.
* W przypadku zmiany ustawień na poziomie systemu operacyjnego lub plików konfiguracji usługi Hadoop możesz chcieć ponownie uruchomić usługi HDInsight.

## <a name="how-to-run-a-script-action"></a><a name="runScriptAction"></a>Jak uruchomić akcję skryptu

Za pomocą akcji skryptu można dostosować klastry usługi HDInsight przy użyciu następujących metod:

* Azure Portal
* Azure PowerShell
* Szablony usługi Azure Resource Manager
* Zestaw SDK usługi HDInsight dla platformy .NET.

Aby uzyskać więcej informacji na temat korzystania z każdej z tych metod, zobacz [jak używać akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md).

## <a name="custom-script-samples"></a><a name="sampleScripts"></a>Przykłady skryptów niestandardowych

Firma Microsoft oferuje przykładowe skrypty do instalowania składników w klastrze usługi HDInsight. Zobacz [Instalowanie i używanie odcienia w klastrach usługi HDInsight](hdinsight-hadoop-hue-linux.md) jako przykładowej akcji skryptu.

## <a name="troubleshooting"></a>Rozwiązywanie problemów

Poniżej przedstawiono błędy, które można napotkać podczas korzystania z utworzonych skryptów:

**Błąd**: `$'\r': command not found` . Czasami następuje `syntax error: unexpected end of file` .

*Przyczyna*: ten błąd występuje, gdy wiersze w skrypcie kończą się znakiem CRLF. Systemy UNIX oczekują tylko LF jako zakończenia wiersza.

Ten problem najczęściej występuje, gdy skrypt jest tworzony w środowisku systemu Windows, ponieważ CRLF jest typowym wierszem kończącym się w przypadku wielu edytorów tekstu w systemie Windows.

*Rozwiązanie*: Jeśli jest to opcja w edytorze tekstu, wybierz pozycję Format systemu UNIX lub LF dla końca wiersza. Możesz również użyć następujących poleceń w systemie UNIX, aby zmienić wartość CRLF na LF:

> [!NOTE]  
> Następujące polecenia są w przybliżeniu równoważne, które powinny zmienić końce linii CRLF na LF. Wybierz jeden na podstawie narzędzi dostępnych w systemie.

| Polecenie | Uwagi |
| --- | --- |
| `unix2dos -b INFILE` |Kopia zapasowa oryginalnego pliku została utworzona za pomocą. Rozszerzenie BAK |
| `tr -d '\r' < INFILE > OUTFILE` |PLIK jest w wersji zawierającej tylko końce LF |
| `perl -pi -e 's/\r\n/\n/g' INFILE` | Modyfikuje plik bezpośrednio |
| ```sed 's/$'"/`echo \\\r`/" INFILE > OUTFILE``` |PLIK jest w wersji zawierającej tylko końce LF. |

**Błąd**: `line 1: #!/usr/bin/env: No such file or directory` .

*Przyczyna*: ten błąd występuje, gdy skrypt został zapisany jako UTF-8 z oznaczeniem kolejności bajtów (BOM).

*Rozwiązanie*: Zapisz plik jako ASCII lub UTF-8 bez BOM. Możesz również użyć następującego polecenia w systemie Linux lub UNIX, aby utworzyć plik bez BOM:

```bash
awk 'NR==1{sub(/^\xef\xbb\xbf/,"")}{print}' INFILE > OUTFILE
```

Zamień na `INFILE` plik zawierający BOM. `OUTFILE` powinna być nową nazwą pliku, która zawiera skrypt bez BOM.

## <a name="next-steps"></a><a name="seeAlso"></a>Następne kroki

* Dowiedz się, jak [dostosować klastry usługi HDInsight za pomocą akcji skryptu](hdinsight-hadoop-customize-cluster-linux.md)
* Aby dowiedzieć się więcej o tworzeniu aplikacji platformy .NET, które zarządzają usługą HDInsight, Skorzystaj z [dokumentacji usługi HDINSIGHT SDK dla platformy .NET](/dotnet/api/overview/azure/hdinsight)
* Użyj [interfejsu API REST usługi HDInsight](/rest/api/hdinsight/) , aby dowiedzieć się, jak używać funkcji REST do wykonywania akcji zarządzania w klastrach usługi HDInsight.