---
title: Co to jest Data Science Virtual Machine platformy Azure
titleSuffix: Azure Data Science Virtual Machine
description: Omówienie usługi Azure Data Science Virtual Machine — można łatwo utworzyć maszynę wirtualną i korzystać z niej na platformie Azure w chmurze z wstępnie zainstalowanymi i skonfigurowanymi narzędziami i bibliotekami służącymi do analizy danych i opracowywania inteligentnych aplikacji.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 03bfee258fe96d90c32b6a305b99856a11d9a087
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "80754981"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co to jest Data Science Virtual Machine platformy Azure dla systemów Linux i Windows?

Data Science Virtual Machine (DSVM) to dostosowany obraz maszyny wirtualnej na platformie Azure Cloud Platform zbudowany specjalnie na potrzeby analizy danych. Ma wiele popularnych narzędzi do nauki o danych i wstępnie skonfigurowanych do szybko Rozpocznij pracę tworzenia inteligentnych aplikacji na potrzeby zaawansowanej analizy. 

DSVM jest dostępny w:

+ **Windows Server 2019**
+ **Ubuntu 18,04 LTS**
+ Windows Server 2016
+ Ubuntu 16.04 LTS

> [!NOTE]
> Wszystkie narzędzia maszyny wirtualnej do uczenia głębokiego zostały złożone w Data Science Virtual Machine. 

## <a name="why-choose-the-dsvm"></a>Dlaczego warto wybrać DSVM?

Celem Data Science Virtual Machine jest zapewnienie pracownikom danych wszystkich poziomów umiejętności i między branżami ze wstępnie skonfigurowanym środowiskiem nauki o danych. Zamiast samodzielnie wdrażać porównywalny obszar roboczy, możesz udostępnić DSVM. Ten wybór może zaoszczędzić dni lub nawet _tygodnie_ w procesach instalacji, konfiguracji i zarządzania pakietami. Po przydzieleniu maszyny wirtualnej do nauki o danych można natychmiast rozpocząć pracę nad projektem nauki o danych.

## <a name="sample-use-cases"></a>Przykładowe przypadki użycia

Poniżej przedstawiono niektóre typowe przypadki użycia dla klientów DSVM.

### <a name="moving-data-science-workloads-to-the-cloud"></a>Przeniesienie obciążeń analizy danych do chmury

DSVM zawiera konfigurację linii bazowej dla zespołów nauki danych, które chcą zamienić swoje komputery lokalne na zarządzane pulpity w chmurze, dzięki czemu wszyscy analitykowie danych w zespole mają spójną konfigurację, z którą można weryfikować eksperymenty i wspierać współpracę. Obniża ona również koszty, zmniejszając obciążenie administratora systemu. Zmniejszenie obciążenia pozwala zaoszczędzić czas wymagany do oszacowania, zainstalowania i konserwacji pakietów oprogramowania na potrzeby zaawansowanej analizy.

### <a name="data-science-training-and-education"></a>Szkolenia i edukacja w zakresie nauki o danych

Instruktorzy i nauczyciele, którzy uczą klasy nauki o danych, zwykle udostępniają obraz maszyny wirtualnej. Obraz ten gwarantuje, że uczniowie mają spójną konfigurację oraz że próbki przewidywalnie działają. 

DSVM tworzy środowisko na żądanie z spójną konfiguracją, która ułatwia problemy z obsługą i niezgodnością. To rozwiązanie oferuje znaczące korzyści w sytuacjach, w których jest konieczne częste tworzenie takich środowisk (zwłaszcza w przypadku krótszych szkoleń).

### <a name="on-demand-elastic-capacity-for-large-scale-projects"></a>Elastyczna wydajność na żądanie dla projektów w dużej skali

Imprezy rozpoczynająa i konkursy danych na dużą skalę, dzięki czemu można przeskalować wydajność sprzętu, zazwyczaj przez krótki czas trwania. DSVM może ułatwić replikację środowiska nauki o danych na żądanie, na skalowalnych w poziomie serwerach, które umożliwiają eksperymenty, w których można uruchamiać zasoby obliczeniowe o wysokiej jakości.

### <a name="custom-compute-power-for-azure-notebooks"></a>Niestandardowa moc obliczeniowa dla Azure Notebooks

[Azure Notebooks](../../notebooks/azure-notebooks-overview.md) to bezpłatna usługa hostowana, która umożliwia tworzenie, uruchamianie i udostępnianie notesów Jupyter w chmurze bez konieczności instalacji. Bezpłatna warstwa usługi jest ograniczona do 4 GB pamięci i 1 GB danych. 

Aby zwolnić wszystkie limity, można dołączyć projekt notesów do DSVM lub innej maszyny wirtualnej działającej na serwerze Jupyter. Jeśli zalogujesz się do Azure Notebooks przy użyciu konta za pomocą Azure Active Directory (na przykład konta firmowego), notesy automatycznie pokazują DSVMs w każdej subskrypcji skojarzonej z tym kontem. Możesz [dołączyć DSVM do Azure Notebooks](../../notebooks/configure-manage-azure-notebooks-projects.md#compute-tier) , aby rozwinąć dostępną moc obliczeniową.

### <a name="short-term-experimentation-and-evaluation"></a>Krótkoterminowe eksperymenty i ocena

Możesz użyć DSVM, aby oszacować lub poznać nowe [Narzędzia](./tools-included.md)do nauki o danych, szczególnie poprzez przechodzenie między opublikowanymi [przykładami i przewodnikami](./dsvm-samples-and-walkthroughs.md).


### <a name="deep-learning-with-gpus"></a>Uczenie głębokie za pomocą procesorów GPU

W DSVM modele szkoleniowe mogą korzystać z algorytmów uczenia głębokiego na sprzęcie opartym na procesorach GPU. Korzystając z możliwości skalowania maszyn wirtualnych platformy Azure, DSVM ułatwia korzystanie z sprzętu opartego na procesorze GPU w chmurze zgodnie z potrzebami. Możesz przełączyć się na maszynę wirtualną opartą na procesorze GPU podczas uczenia dużych modeli lub gdy potrzebujesz obliczeń o dużej szybkości przy zachowaniu tego samego dysku systemu operacyjnego. Można wybrać dowolną jednostkę SKU maszyn wirtualnych z serii N z funkcją DSVM. Zanotuj bezpłatne konta platformy Azure nie obsługują jednostek SKU maszyn wirtualnych obsługujących procesor GPU.

Wersje systemu Windows DSVM są wstępnie instalowane ze sterownikami procesora GPU, platformami i wersjami procesora GPU dla platform edukacyjnych. W wersji systemu Linux uczenie głębokie na procesorach GPU jest włączone na Ubuntu DSVMs. 

Możesz również wdrożyć Ubuntu lub wersje systemu Windows DSVM na maszynie wirtualnej platformy Azure, która nie jest oparta na procesorach GPU. W takim przypadku wszystkie platformy uczenia głębokiego zostaną przywrócone do trybu procesora CPU.

[Dowiedz się więcej o dostępnych strukturach głębokiego uczenia i AI](dsvm-tools-deep-learning-frameworks.md).

<a name="included"></a>
## <a name="whats-included-on-the-dsvm"></a>Co zawiera DSVM?

Zapoznaj się z pełną listą narzędzi w systemie Windows i Linux DSVM [.](tools-included.md)

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej z następujących artykułów:

+ W systemie Windows:
  + [Konfigurowanie maszyny DSVM z systemem Windows](provision-vm.md)
  + [Dziesięć rzeczy, które można wykonać na DSVM systemu Windows](vm-do-ten-things.md)

+ W systemie Linux:
  + [Konfigurowanie systemu Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Analiza danych w systemie Linux DSVM](linux-dsvm-walkthrough.md)
