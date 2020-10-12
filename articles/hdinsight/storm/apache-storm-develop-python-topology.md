---
title: Apache Storm ze składnikami języka Python — Azure HDInsight
description: Dowiedz się, jak utworzyć topologię Apache Storm, która używa składników języka Python w usłudze Azure HDInsight
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: how-to
ms.custom: hdinsightactive,hdiseo17may2017, devx-track-python
ms.date: 12/16/2019
ms.openlocfilehash: 1f062a8f358ac311b53e657fb5714583458bd9b5
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "87872586"
---
# <a name="develop-apache-storm-topologies-using-python-on-hdinsight"></a>Opracowywanie topologii Apache Storm przy użyciu języka Python w usłudze HDInsight

Dowiedz się, jak utworzyć topologię [Apache Storm](https://storm.apache.org/) , która używa składników języka Python. Apache Storm obsługuje wiele języków, nawet umożliwiając Łączenie składników z kilku języków w jednej topologii. Platforma [strumień](https://storm.apache.org/releases/current/flux.html) (wprowadzona przy użyciu burzy 0.10.0) umożliwia łatwe tworzenie rozwiązań wykorzystujących składniki języka Python.

> [!IMPORTANT]  
> Informacje przedstawione w tym dokumencie zostały przetestowane przy użyciu burzy w usłudze HDInsight 3,6.

## <a name="prerequisites"></a>Wymagania wstępne

* Klaster Apache Storm w usłudze HDInsight. Zobacz [Tworzenie klastrów Apache Hadoop przy użyciu Azure Portal](../hdinsight-hadoop-create-linux-clusters-portal.md) i wybierz pozycję **burza** dla **typu klastra**.

* Lokalne środowisko programistyczne (opcjonalnie). Lokalne środowisko burzowe jest wymagane tylko wtedy, gdy chcesz uruchomić topologię lokalnie. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

* [Python 2,7 lub nowszy](https://www.python.org/downloads/).

* [Java developer Kit (JDK) w wersji 8](https://aka.ms/azure-jdks).

* Pakiet [Apache Maven](https://maven.apache.org/download.cgi) został prawidłowo [zainstalowany](https://maven.apache.org/install.html) zgodnie z usługą Apache.  Maven to system kompilacji projektu dla projektów języka Java.

## <a name="storm-multi-language-support"></a>Obsługa wielu języków

Apache Storm został zaprojektowany z myślą o pracy ze składnikami napisane przy użyciu dowolnego języka programowania. Składniki muszą zrozumieć sposób pracy z definicją Thrift dla burzy. W przypadku języka Python moduł jest dostarczany jako część projektu Apache Storm, który umożliwia łatwe interfejsowanie z burzą. Ten moduł można znaleźć pod adresem [https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py](https://github.com/apache/storm/blob/master/storm-multilang/python/src/main/resources/resources/storm.py) .

Burza jest procesem języka Java, który działa na wirtualna maszyna Java (JVM). Składniki w innych językach są wykonywane jako podprocesy. Burza komunikuje się z tymi podprocesami przy użyciu komunikatów JSON wysyłanych za pośrednictwem stdin/stdout. Więcej informacji na temat komunikacji między składnikami można znaleźć w dokumentacji [protokołu z wieloma językami](https://storm.apache.org/releases/current/Multilang-protocol.html) .

## <a name="python-with-the-flux-framework"></a>Python z platformą strumień

Platforma strumień umożliwia definiowanie topologii burzy niezależnie od składników. Struktura strumienia używa YAML do definiowania topologii burzy. Poniższy tekst przedstawia przykład sposobu odwoływania się do składnika języka Python w dokumencie YAML:

```yaml
# Spout definitions
spouts:
  - id: "sentence-spout"
    className: "org.apache.storm.flux.wrappers.spouts.FluxShellSpout"
    constructorArgs:
      # Command line
      - ["python", "sentencespout.py"]
      # Output field(s)
      - ["sentence"]
    # parallelism hint
    parallelism: 1
```

Klasa `FluxShellSpout` jest używana do uruchomienia `sentencespout.py` skryptu, który implementuje elementu Spout.

Strumień oczekuje, że skrypty języka Python mają znajdować się w `/resources` katalogu wewnątrz pliku JAR, który zawiera topologię. W tym przykładzie są przechowywane skrypty języka Python w `/multilang/resources` katalogu. `pom.xml`Obejmuje następujący plik XML:

```xml
<!-- include the Python components -->
<resource>
    <directory>${basedir}/multilang</directory>
    <filtering>false</filtering>
</resource>
```

Jak wspomniano wcześniej, istnieje `storm.py` plik, który implementuje definicję Thrift dla burzy. Platforma strumień `storm.py` jest automatycznie tworzona podczas kompilowania projektu, więc nie trzeba martwić się o dołączenie do niego.

## <a name="build-the-project"></a>Kompilowanie projektu

1. Pobierz projekt z [https://github.com/Azure-Samples/hdinsight-python-storm-wordcount](https://github.com/Azure-Samples/hdinsight-python-storm-wordcount) .

1. Otwórz wiersz polecenia i przejdź do katalogu głównego projektu: `hdinsight-python-storm-wordcount-master` . Wprowadź następujące polecenie:

    ```cmd
    mvn clean compile package
    ```

    To polecenie tworzy `target/WordCount-1.0-SNAPSHOT.jar` plik, który zawiera skompilowaną topologię.

## <a name="run-the-storm-topology-on-hdinsight"></a>Uruchamianie topologii burzy w usłudze HDInsight

1. Użyj [polecenia SSH](../hdinsight-hadoop-linux-use-ssh-unix.md) , aby skopiować `WordCount-1.0-SNAPSHOT.jar` plik do burzy w klastrze usługi HDInsight. Edytuj poniższe polecenie, zastępując wartość CLUSTERname nazwą klastra, a następnie wprowadź polecenie:

    ```cmd
    scp target/WordCount-1.0-SNAPSHOT.jar sshuser@CLUSTERNAME-ssh.azurehdinsight.net:
    ```

1. Po przekazaniu pliku Połącz się z klastrem przy użyciu protokołu SSH:

    ```cmd
    ssh sshuser@CLUSTERNAME-ssh.azurehdinsight.net
    ```

1. W sesji SSH Użyj następującego polecenia, aby uruchomić topologię w klastrze:

    ```bash
    storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -r -R /topology.yaml
    ```

    Po uruchomieniu topologia burza jest uruchamiana do momentu zatrzymania.

1. Użyj interfejsu użytkownika burzy, aby wyświetlić topologię w klastrze. Interfejs użytkownika burzy znajduje się w lokalizacji `https://CLUSTERNAME.azurehdinsight.net/stormui` . Zastąp `CLUSTERNAME` nazwą klastra.

1. Zatrzymaj topologię burzy. Użyj następującego polecenia, aby zatrzymać topologię klastra:

    ```bash
    storm kill wordcount
    ```

    Alternatywnie można użyć interfejsu użytkownika burzy. W obszarze **Akcje topologii** dla topologii wybierz pozycję **Kasuj**.

## <a name="run-the-topology-locally"></a>Uruchamianie topologii lokalnie

Aby uruchomić topologię lokalnie, użyj następującego polecenia:

```bash
storm jar WordCount-1.0-SNAPSHOT.jar org.apache.storm.flux.Flux -l -R /topology.yaml
```

> [!NOTE]  
> To polecenie wymaga lokalnego środowiska projektowego burzy. Aby uzyskać więcej informacji, zobacz [Konfigurowanie środowiska deweloperskiego](https://storm.apache.org/releases/current/Setting-up-development-environment.html).

Po uruchomieniu topologii emitują informacje do konsoli lokalnej podobne do następującego tekstu:

```output
24302 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24302 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting the
24302 [Thread-28] INFO  o.a.s.t.ShellBolt - ShellLog pid:2437, name:counter-bolt Emitting years:160
24302 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=the, count=599}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=seven, count=302}
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=dwarfs, count=143}
24303 [Thread-25-sentence-spout-executor[4 4]] INFO  o.a.s.s.ShellSpout - ShellLog pid:2436, name:sentence-spout Emiting the cow jumped over the moon
24303 [Thread-30] INFO  o.a.s.t.ShellBolt - ShellLog pid:2438, name:splitter-bolt Emitting cow
24303 [Thread-17-log-executor[3 3]] INFO  o.a.s.f.w.b.LogInfoBolt - {word=four, count=160}
```

Aby zatrzymać topologię, użyj __kombinacji klawiszy CTRL + C__.

## <a name="next-steps"></a>Następne kroki

Zapoznaj się z następującymi dokumentami, aby poznać inne sposoby korzystania z języka Python z usługą HDInsight: [jak używać funkcji zdefiniowanych przez użytkownika w języku Python w programie Apache świni i Apache Hive](../hadoop/python-udf-hdinsight.md).
