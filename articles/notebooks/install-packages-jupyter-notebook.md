---
title: Instalowanie pakietów w notesach Jupyter Azure Notebooks — wersja zapoznawcza
description: 'Dowiedz się, jak zainstalować pakiety Python, R i F # z poziomu notesu Jupyter działającego na platformie Azure.'
ms.topic: how-to
ms.date: 12/04/2018
ms.openlocfilehash: 43d38c9f3a4b0095967ab3e103ea729ec86ea2bd
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85831220"
---
# <a name="install-packages-from-within-azure-notebooks-preview"></a>Zainstaluj pakiety z poziomu programu Azure Notebooks Preview

[!INCLUDE [notebooks-status](../../includes/notebooks-status.md)]

Mimo że można skonfigurować [środowisko dla notesu na poziomie projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment), można zainstalować pakiety bezpośrednio w ramach danego notesu.

Pakiety zainstalowane w notesie dotyczą tylko bieżącej sesji serwera. Instalacje pakietów nie są utrwalane po zamknięciu serwera.

## <a name="python"></a>Python

Pakiety w języku Python można instalować przy użyciu PIP lub Conda przy użyciu poleceń w komórkach kodu:

```bash
!pip install <package_name>

!conda install <package_name> -y
```

Jeśli dane wyjściowe polecenia wskazują, że wymaganie jest już spełnione, Azure Notebooks może zawierać pakiet domyślnie. Pakiet może być również instalowany za pomocą [kroku konfiguracji środowiska projektu](configure-manage-azure-notebooks-projects.md#configure-the-project-environment).

## <a name="r"></a>R

Pakiety w języku R można instalować z CRAN lub GitHub przy użyciu `install.packages` funkcji w komórce kodu:

```r
install.packages("package_name")
```

Możesz również zainstalować wersje wstępne i inne pakiety deweloperskie z usługi GitHub przy użyciu biblioteki devtools:

```r
options(unzip = 'internal')
library(devtools)
install_github('<user>/<repo>')
```

## <a name="f"></a>F#

Pakiety w języku F # można instalować z [NuGet.org](https://www.nuget.org) przez wywołanie Menedżera zależności Paket z poziomu komórek kodu. Najpierw załaduj program Paket Manager:

```fsharp
#load "Paket.fsx"
```

Następnie zainstaluj pakiety:

```fsharp
Paket.Package
  [ "MathNet.Numerics"
    "MathNet.Numerics.FSharp"
  ]
```

Następnie załaduj Generator Paket:
```fsharp
#load "Paket.Generated.Refs.fsx"
```

Otwórz bibliotekę:
```fsharp
open MathNet.Numerics
```

## <a name="next-steps"></a>Następne kroki

- [Instrukcje: Konfigurowanie projektów i zarządzanie nimi](configure-manage-azure-notebooks-projects.md)
- [Instrukcje: prezentowanie pokazu slajdów](present-jupyter-notebooks-slideshow.md)
