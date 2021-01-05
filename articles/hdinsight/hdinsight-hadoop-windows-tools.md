---
title: Korzystanie z komputera z systemem Windows z usługą Hadoop w usłudze HDInsight — Azure
description: Pracuj z komputera z systemem Windows w usłudze Hadoop w usłudze HDInsight. Zarządzaj klastrami i badaj je za pomocą narzędzi PowerShell, Visual Studio i Linux. Opracowywanie rozwiązań do obsługi danych Big Data za pomocą platformy .NET.
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.service: hdinsight
ms.topic: conceptual
ms.custom: hdinsightactive,hdiseo17may2017
ms.date: 12/20/2019
ms.openlocfilehash: 770c73cb6f1309e64cd44c5a1b1570899108e4e7
ms.sourcegitcommit: 28c93f364c51774e8fbde9afb5aa62f1299e649e
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/30/2020
ms.locfileid: "97821047"
---
# <a name="work-in-the-apache-hadoop-ecosystem-on-hdinsight-from-a-windows-pc"></a>Pracuj w ekosystemie Apache Hadoop w usłudze HDInsight z komputera z systemem Windows

Dowiedz się więcej na temat opcji programowania i zarządzania na komputerze z systemem Windows na potrzeby pracy w ekosystemie Apache Hadoop w usłudze HDInsight.

Usługa HDInsight jest oparta na składnikach Apache Hadoop i Hadoop, a technologie open source opracowano w systemie Linux. Usługa HDInsight w wersji 3,4 lub nowszej używa dystrybucji Ubuntu Linux jako bazowego systemu operacyjnego klastra. Można jednak korzystać z usługi HDInsight z klienta systemu Windows lub środowiska projektowego systemu Windows.

## <a name="use-powershell-for-deployment-and-management-tasks"></a>Korzystanie z programu PowerShell na potrzeby zadań wdrażania i zarządzania

Azure PowerShell to środowisko do obsługi skryptów, które służy do kontrolowania i automatyzowania zadań wdrażania i zarządzania w usłudze HDInsight z systemu Windows.

Przykłady zadań, które można wykonać za pomocą programu PowerShell:

* [Tworzenie klastrów przy użyciu programu PowerShell](hdinsight-hadoop-create-linux-clusters-azure-powershell.md).
* [Uruchamianie zapytań Apache Hive przy użyciu programu PowerShell](hadoop/apache-hadoop-use-hive-powershell.md).
* [Zarządzanie klastrami przy użyciu programu PowerShell](hdinsight-administer-use-powershell.md).

Wykonaj kroki, aby [zainstalować i skonfigurować program Azure PowerShell](/powershell/azure/install-az-ps) w celu uzyskania najnowszej wersji.

## <a name="utilities-you-can-run-in-a-browser"></a>Narzędzia, które można uruchomić w przeglądarce

Następujące narzędzia zawierają interfejs użytkownika sieci Web, który działa w przeglądarce:
* **[Azure Cloud Shell](../cloud-shell/overview.md)** to interaktywna powłoka wiersza polecenia uruchamiana w przeglądarce i z Azure Portal.

* **[Interfejs użytkownika sieci Web Apache Ambari](hdinsight-hadoop-manage-ambari.md)** to narzędzie do zarządzania i monitorowania dostępne w Azure Portal, które może służyć do zarządzania różnymi rodzajami zadań, takich jak:
    * [Korzystanie z platformy Apache Ambari z interfejsem API REST](hdinsight-hadoop-manage-ambari-rest-api.md)
    * [Widok Apache Hive w programie Apache Ambari](hadoop/apache-hadoop-use-hive-ambari-view.md)
    * [Widok Apache Tez w programie Apache Ambari](./index.yml)

## <a name="data-lake-hadoop-tools-for-visual-studio"></a>Narzędzia Data Lake (Hadoop) dla programu Visual Studio

Narzędzia Data Lake Tools for Visual Studio umożliwiają wdrażanie topologii burzy i zarządzanie nimi. Narzędzia Data Lake również instaluje zestaw SDK SCP.NET, który umożliwia tworzenie topologii burzy w języku C# za pomocą programu Visual Studio.

Przed przejściem do poniższych przykładów [Zainstaluj i wypróbuj Data Lake Tools for Visual Studio](hadoop/apache-hadoop-visual-studio-tools-get-started.md).

Przykłady zadań, które można wykonać za pomocą programu Visual Studio i narzędzi Data Lake Tools for Visual Studio:
* [Wdrażanie topologii burzy i zarządzanie nimi z poziomu programu Visual Studio](storm/apache-storm-deploy-monitor-topology-linux.md)
* [Tworzenie topologii języka C# na potrzeby burzy przy użyciu programu Visual Studio](storm/apache-storm-develop-csharp-visual-studio-topology.md). Bity obejmują przykładowe szablony topologii burzy, które można połączyć z bazami danych, takimi jak Azure Cosmos DB i SQL Database.

## <a name="visual-studio-and-the-net-sdk"></a>Visual Studio i zestaw SDK .NET

Możesz użyć programu Visual Studio z zestawem SDK platformy .NET do zarządzania klastrami i opracowywania aplikacji do obsługi danych Big Data. W przypadku następujących zadań można użyć innych środowisk IDE, ale przykłady są wyświetlane w programie Visual Studio.

Przykłady zadań, które można wykonać za pomocą zestawu .NET SDK w programie Visual Studio:
* [Zestaw SDK usługi Azure HDInsight dla platformy .NET](/dotnet/api/overview/azure/hdinsight?view=azure-dotnet&preserve-view=true).
* [Uruchamianie Apache Hive zapytań przy użyciu zestawu .NET SDK](hadoop/apache-hadoop-use-hive-dotnet-sdk.md).
* [Korzystanie z funkcji zdefiniowanych przez użytkownika w języku C# z Apache Hive i Apache wieprz streaming na Apache Hadoop](hadoop/apache-hadoop-hive-pig-udf-dotnet-csharp.md).

## <a name="intellij-idea-and-eclipse-ide-for-spark-clusters"></a>IntelliJ pomysł i zaćmienie IDE dla klastrów Spark

Zarówno [INTELLIJ pomysł](https://www.jetbrains.com/idea/download) , jak i [przezaćmienie IDE](https://www.eclipse.org/downloads/) mogą służyć do:
* Tworzenie i przesyłanie aplikacji Scala Spark w klastrze usługi HDInsight Spark.
* Dostęp do zasobów klastra Spark.
* Tworzenie i uruchamianie aplikacji Scala Spark lokalnie.

W tych artykułach pokazano, jak:
* POMYSŁ IntelliJ: [Twórz aplikacje Apache Spark przy użyciu zestawu Azure Toolkit dla wtyczki IntelliJ i zestawu SDK Scala.](spark/apache-spark-intellij-tool-plugin.md)
* Zaćmienie IDE lub Scala IDE for zaćmienie: [Utwórz aplikacje Apache Spark i Azure Toolkit for Eclipse](spark/apache-spark-eclipse-tool-plugin.md)

## <a name="notebooks-on-spark-for-data-scientists"></a>Notesy na platformie Spark dla analityków danych

Klastry Apache Spark w usłudze HDInsight obejmują Notesy i jądra platformy Apache Zeppelin, które mogą być używane z notesami Jupyter.

* [Dowiedz się, jak używać jądra w klastrach Apache Spark z notesami Jupyter do testowania aplikacji platformy Spark](spark/apache-spark-zeppelin-notebook.md)
* [Dowiedz się, jak używać notesów Apache Zeppelin w klastrach Apache Spark do uruchamiania zadań platformy Spark](spark/apache-spark-jupyter-notebook-kernels.md)

## <a name="run-linux-based-tools-and-technologies-on-windows"></a>Uruchamianie narzędzi i technologii opartych na systemie Linux systemu Windows

Jeśli pracujesz w sytuacji, w której musisz użyć narzędzia lub technologii, która jest dostępna tylko w systemie Linux, weź pod uwagę następujące opcje:

* **Bash on Ubuntu w systemie Windows 10** udostępnia podsystem Linux w systemie Windows. Bash umożliwia bezpośrednie uruchamianie narzędzi systemu Linux bez konieczności konserwowania dedykowanej instalacji systemu Linux. Aby uzyskać instrukcje dotyczące instalacji, zobacz artykuł [podsystem instalacji systemu Windows dla systemu Linux w systemie Windows 10](/windows/wsl/install-win10) .  Inne [powłoki systemu UNIX](https://www.gnu.org/software/bash/) również będą działały.
* **Docker for Windows** zapewnia dostęp do wielu narzędzi opartych na systemie Linux i można go uruchomić bezpośrednio z systemu Windows. Na przykład można użyć platformy Docker do uruchomienia klienta Z usługi Beeline dla programu Hive bezpośrednio z systemu Windows. Można również użyć platformy Docker do uruchamiania Jupyter Notebook lokalnego i zdalnego łączenia z platformą Spark w usłudze HDInsight. [Wprowadzenie do Docker for Windows](https://docs.docker.com/docker-for-windows/)
* **[MobaXTerm](https://mobaxterm.mobatek.net/)** umożliwia graficzne przeglądanie systemu plików klastra za pośrednictwem połączenia SSH.

## <a name="cross-platform-tools"></a>Narzędzia dla wielu platform

Interfejs wiersza polecenia platformy Azure to wieloplatformowe środowisko wiersza polecenia do zarządzania zasobami platformy Azure.  Aby uzyskać więcej informacji, zobacz [interfejs Azure Command-Line Interface (CLI)](/cli/azure/).

## <a name="next-steps"></a>Następne kroki

Jeśli dopiero zaczynasz pracę w klastrach opartych na systemie Linux, zobacz następujące artykuły:
* [Konfigurowanie Apache Hadoop, Apache Kafka, Apache Spark lub innych klastrów](hdinsight-hadoop-provision-linux-clusters.md)
* [Wskazówki dotyczące klastrów usługi HDInsight w systemie Linux](hdinsight-hadoop-linux-information.md)