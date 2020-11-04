---
title: Konfigurowanie środowisk nauki o danych na platformie Azure — proces nauki o danych zespołowych
description: Skonfiguruj środowiska nauki danych na platformie Azure do użycia w procesie nauki danych zespołu.
services: machine-learning
author: marktab
manager: marktab
editor: marktab
ms.service: machine-learning
ms.subservice: team-data-science-process
ms.topic: article
ms.date: 01/10/2020
ms.author: tdsp
ms.custom: seodec18, previous-author=deguhath, previous-ms.author=deguhath
ms.openlocfilehash: 557550627fe2c39571a848723e5d716324fee240
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/04/2020
ms.locfileid: "93321165"
---
# <a name="set-up-data-science-environments-for-use-in-the-team-data-science-process"></a>Konfigurowanie środowisk analizy danych do użytku w zespołowym przetwarzaniu danych dla celów naukowych
Proces nauki danych zespołu używa różnych środowisk analizy danych do przechowywania, przetwarzania i analizy danych. Obejmują one platformę Azure Blob Storage, kilka typów klastrów Azure Virtual Machines, HDInsight (Hadoop) i Azure Machine Learning obszarów roboczych. Decyzja o tym, które środowisko ma być używane, zależy od typu i ilości danych, które mają być modelowane, oraz docelowej lokalizacji danych w chmurze. 

* Aby uzyskać wskazówki dotyczące zagadnień, które należy wziąć pod uwagę podczas podejmowania decyzji, zobacz [Planowanie środowiska Azure Machine Learning nauki o danych](plan-your-environment.md). 
* Aby zapoznać się z wykazem niektórych scenariuszy, które można napotkać podczas przeprowadzania zaawansowanej analizy, zobacz [scenariusze dotyczące procesu nauki dotyczącego danych zespołu](plan-sample-scenarios.md)

W poniższych artykułach opisano, jak skonfigurować różne środowiska do nauki o danych, które są używane przez proces nauki danych zespołu.

* [Magazyn Azure — konto](../../storage/common/storage-account-create.md)
* [Klaster usługi HDInsight (Hadoop)](../../hdinsight/spark/apache-spark-jupyter-spark-sql.md)
* [Obszar roboczy Azure Machine Learning Studio (klasyczny)](../classic/create-workspace.md)

**Program Microsoft Data Science Virtual Machine (DSVM)** jest również dostępny jako obraz maszyny wirtualnej platformy Azure. Ta maszyna wirtualna jest wstępnie zainstalowana i skonfigurowana za pomocą kilku popularnych narzędzi, które są często używane do analizy danych i uczenia maszynowego. DSVM jest dostępna zarówno w systemie Windows, jak i Linux. Aby uzyskać więcej informacji, zobacz [wprowadzenie do Data Science Virtual Machine opartej na chmurze dla systemów Linux i Windows](../data-science-virtual-machine/overview.md).

Dowiedz się, jak utworzyć:

- [Maszyna DSVM z systemem Windows](../data-science-virtual-machine/provision-vm.md)
- [Maszyna DSVM z systemem Ubuntu](../data-science-virtual-machine/dsvm-ubuntu-intro.md)
- [Maszyna DSVM z systemem CentOS](../data-science-virtual-machine/release-notes.md)