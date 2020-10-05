---
title: Co to jest Data Science Virtual Machine platformy Azure
titleSuffix: Azure Data Science Virtual Machine
description: Omówienie usługi Azure Data Science Virtual Machine — łatwą w obsłudze maszynę wirtualną na platformie Azure Cloud Platform z wstępnie zainstalowanymi i skonfigurowanymi narzędziami i bibliotekami służącymi do analizy danych.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: vijetajo
ms.author: vijetaj
ms.topic: overview
ms.date: 04/02/2020
ms.openlocfilehash: 2bfcdfcb01e7908c199054e793d82cdfa1b726c7
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "88816341"
---
# <a name="what-is-the-azure-data-science-virtual-machine-for-linux-and-windows"></a>Co to jest Data Science Virtual Machine platformy Azure dla systemów Linux i Windows?

Data Science Virtual Machine (DSVM) to dostosowany obraz maszyny wirtualnej na platformie Azure Cloud Platform zbudowany specjalnie na potrzeby analizy danych. Ma wiele popularnych narzędzi do nauki o danych i wstępnie skonfigurowanych, aby szybko rozpocząć tworzenie inteligentnych aplikacji na potrzeby zaawansowanej analizy.

DSVM jest dostępny w:

+ Windows Server 2019
+ Ubuntu 18,04 LTS

## <a name="comparison-with-azure-machine-learning"></a>Porównanie z Azure Machine Learning

DSVM to dostosowany obraz maszyny wirtualnej do nauki o danych, ale [Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/overview-what-is-azure-ml) (Azure) to kompleksowa platforma, która obejmuje:

+ W pełni zarządzane obliczenia
  + Wystąpienia obliczeniowe
  + Klastry obliczeniowe dla zadań rozproszonej ML
  + Klastry wnioskowania do oceniania w czasie rzeczywistym
+ Magazyny danych (na przykład BLOB, ADLS Gen2, SQL DB)
+ Śledzenie eksperymentu
+ Zarządzanie modelem
+ Notebooks
+ Środowiska (zarządzanie zależnościami Conda i R)
+ Etykietowania
+ Potoki (Automatyzuj kompleksowe przepływy pracy analizy danych)

### <a name="comparison-with-azureml-compute-instances"></a>Porównanie z wystąpieniami obliczeniowymi platformy Azure

[Wystąpienia obliczeniowe Azure Machine Learning](https://docs.microsoft.com/azure/machine-learning/concept-compute-instance) są w pełni skonfigurowanym i __zarządzanym__ obrazem maszyny wirtualnej, podczas gdy DSVM jest __niezarządzaną__ maszyną wirtualną.

Poniżej przedstawiono kluczowe różnice między tymi dwoma ofertami produktów:


|Cecha |Analiza danych<br>VM |AzureML<br>Wystąpienie obliczeniowe  | 
|---------|---------|---------|
| W pełni zarządzane | Nie        | Tak        |
|Obsługa języków     |  Python, R, Julia, SQL, C#,<br> Java, Node.js, F #       | Języki Python i R        |
|System operacyjny     | Ubuntu<br>Windows         |    Ubuntu     |
|Wstępnie skonfigurowana opcja procesora GPU     |  Tak       |    Tak     |
|Opcja skalowania w górę | Tak | Tak |
|Dostęp SSH    | Tak        |    Tak     |
|Dostęp RDP    | Tak        |     Nie    |
|Wbudowane<br>Hostowane notesy     |   Nie<br>(wymaga dodatkowej konfiguracji)      |      Tak   |
|Wbudowane Logowanie jednokrotne     | Nie <br>(wymaga dodatkowej konfiguracji)         |    Tak     |
|Wbudowana współpraca     | Nie         | Tak        |
|Wstępnie zainstalowane narzędzia     |  Jupyter (Lab), RStudio Server, programu vscode,<br> Visual Studio, platformy PyCharm itd, Juno,<br>Power BI Desktop, SSMS, <br>Microsoft Office 365, Funkcja drążenia Apache       |     Jupyter (Lab)<br> RStudio Server   |

## <a name="sample-use-cases"></a>Przykładowe przypadki użycia

Poniżej przedstawiono niektóre typowe przypadki użycia dla klientów DSVM.

### <a name="short-term-experimentation-and-evaluation"></a>Krótkoterminowe eksperymenty i ocena

Możesz użyć DSVM, aby oszacować lub poznać nowe [Narzędzia](./tools-included.md)do nauki o danych, szczególnie poprzez przechodzenie między opublikowanymi [przykładami i przewodnikami](./dsvm-samples-and-walkthroughs.md).

### <a name="deep-learning-with-gpus"></a>Uczenie głębokie za pomocą procesorów GPU

W DSVM modele szkoleniowe mogą korzystać z algorytmów uczenia głębokiego na sprzęcie opartym na procesorach GPU. Korzystając z możliwości skalowania maszyn wirtualnych platformy Azure, DSVM ułatwia korzystanie z sprzętu opartego na procesorze GPU w chmurze zgodnie z potrzebami. Można przełączyć się do maszyny wirtualnej opartej na procesorze GPU w przypadku trenowania dużych modeli lub przeprowadzania szybkich obliczeń i zachować ten sam dysk systemu operacyjnego. Można wybrać dowolną z maszyn wirtualnych serii N z obsługą procesorów GPU z DSVM. Uwaga jednostki SKU maszyny wirtualnej obsługujące procesor GPU nie są obsługiwane w przypadku bezpłatnych kont platformy Azure.

Wersje systemu Windows DSVM są wstępnie zainstalowane ze sterownikami procesora GPU, platformami i wersjami procesora GPU dla platform edukacyjnych. W wersjach systemu Linux uczenie głębokie na procesorach GPU jest włączone na Ubuntu DSVMs. 

Możesz również wdrożyć Ubuntu lub wersje systemu Windows DSVM na maszynie wirtualnej platformy Azure, która nie jest oparta na procesorach GPU. W takim przypadku wszystkie platformy uczenia głębokiego zostaną przywrócone do trybu procesora CPU.

[Dowiedz się więcej o dostępnych strukturach głębokiego uczenia i AI](dsvm-tools-deep-learning-frameworks.md).

### <a name="data-science-training-and-education"></a>Szkolenia i edukacja w zakresie nauki o danych

Instruktorzy i nauczyciele prowadzący zajęcia z zakresu nauki o danych zwykle udostępniają obraz maszyny wirtualnej. Obraz gwarantuje, że uczniowie mają spójną konfigurację oraz że próbki przewidywalnie działają.

DSVM tworzy środowisko na żądanie z spójną konfiguracją, która ułatwia problemy z obsługą i niezgodnością. To rozwiązanie oferuje znaczące korzyści w sytuacjach, w których jest konieczne częste tworzenie takich środowisk (zwłaszcza w przypadku krótszych szkoleń).


## <a name="whats-included-on-the-dsvm"></a>Co zawiera DSVM?

Zapoznaj się z pełną listą narzędzi w systemie Windows i Linux DSVMs [tutaj](tools-included.md).

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej z następujących artykułów:

+ W systemie Windows:
  + [Konfigurowanie maszyny DSVM z systemem Windows](provision-vm.md)
  + [Analiza danych w systemie Windows DSVM](vm-do-ten-things.md)

+ W systemie Linux:
  + [Konfigurowanie systemu Linux DSVM (Ubuntu)](dsvm-ubuntu-intro.md)
  + [Analiza danych w systemie Linux DSVM](linux-dsvm-walkthrough.md)
