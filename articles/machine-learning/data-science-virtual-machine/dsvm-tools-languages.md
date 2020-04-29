---
title: Obsługiwane języki
titleSuffix: Azure Data Science Virtual Machine
description: Obsługiwane języki programu i narzędzia pokrewne wstępnie zainstalowane w Data Science Virtual Machine.
keywords: data science tools, data science virtual machine, tools for data science, linux data science
services: machine-learning
ms.service: machine-learning
ms.subservice: data-science-vm
author: lobrien
ms.author: laobri
ms.topic: conceptual
ms.date: 12/12/2019
ms.openlocfilehash: e7b32579712e89c0d5595303ee7e03d8b2462607
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79283656"
---
# <a name="languages-supported-on-the-data-science-virtual-machine"></a>Języki obsługiwane na Data Science Virtual Machine 

Data Science Virtual Machine (DSVM) zawiera kilka wstępnie utworzonych języków i narzędzi programistycznych do tworzenia aplikacji sztucznej inteligencji (AI). Poniżej przedstawiono niektóre z nich.

## <a name="python-windows-server-2016-edition"></a>Python (Windows Server 2016 Edition)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Python 2,7 i 3,7 |
| Obsługiwane wersje DSVM      | Windows Server 2016     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Tworzone są `conda` dwa środowiska globalne: <br /> * `root` Środowisko zlokalizowane w `/anaconda/` programie to Python 3,7. <br/> * `python2` Środowisko zlokalizowane w `/anaconda/envs/python2` programie to Python 2,7.       |
| Linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka Python.     |
| Narzędzia pokrewne na DSVM      | PySpark, R, Julia.      |

> [!NOTE]
> Kompilacje systemu Windows Server 2016, które zostały utworzone przed marca 2018, zawierają Python 3,5 i Python 2,7. Python 2,7 to środowisko **Główne** Conda, a **Py37** to środowisko Python 3,7.

### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

* Uruchom w wierszu polecenia:

  Otwórz wiersz polecenia i użyj jednej z następujących metod, w zależności od wersji języka Python, który chcesz uruchomić:

    ```
    # To run Python 2.7
    activate python2
    python --version
    
    # To run Python 3.7
    activate 
    python --version 
    ```
    
* Użyj w środowisku IDE:

  Użyj Python Tools for Visual Studio (PTVS) zainstalowanej w programie Visual Studio Community Edition. Domyślnie jedynym środowiskiem, które jest konfigurowane automatycznie w PTVS, jest Python 3,6. 

    > [!NOTE]
    > Aby wskazać PTVS w języku Python 2,7, należy utworzyć środowisko niestandardowe w PTVS. Aby ustawić tę ścieżkę środowiska w programie Visual Studio Community Edition, przejdź do pozycji **Narzędzia** -> **Python Tools** -> **Python** environments i wybierz pozycję **+ Custom (niestandardowe**). Następnie Ustaw lokalizację na **c:\anaconda\envs\python2** i wybierz pozycję **Autowykrywanie**.

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako _Python [Conda root]_ dla języka Python 3,7 i _Python [Conda env: python2]_ dla języka Python 2,7.

* Zainstaluj pakiety języka Python:

  Domyślne środowiska języka Python na DSVM są środowiskami globalnymi, które są odczytywane przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, Aktywuj je w środowisku głównym lub python2 za pomocą `activate` polecenia jako administrator. Następnie można użyć Menedżera pakietów, takiego jak `conda` lub `pip` , aby zainstalować lub zaktualizować pakiety.

## <a name="python-linux-edition"></a>Python (wersja Linux)

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Python 2,7 i 3,5 |
| Obsługiwane wersje DSVM      | Linux   |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Tworzone są `conda` dwa środowiska globalne: <br /> * `root`środowisko zlokalizowane w `/anaconda/` programie to Python 2,7. <br/> * `py35`środowisko zlokalizowane w `/anaconda/envs/py35`programie to Python 3,5.       |
| Linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka Python.     |
| Narzędzia pokrewne na DSVM      | PySpark, R, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

* Uruchom w terminalu:

  Otwórz Terminal i wykonaj jedną z następujących czynności, w zależności od wersji języka Python, który chcesz uruchomić:

    ```
    # To run Python 2.7
    source activate 
    python --version
    
    # To run Python 3.5
    source activate py35
    python --version
    
    ```
* Użyj w środowisku IDE:

  Użyj platformy PyCharm itd zainstalowanego w programie Visual Studio Community Edition. 

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **Python [Conda root]** dla języka Python 2,7 i **Python [Conda env: py35]** dla środowiska Python 3,5. 

* Zainstaluj pakiety języka Python:

  Domyślne środowiska języka Python na DSVM są środowiskami globalnymi możliwymi do odczytania przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, Aktywuj je w środowisku głównym lub py35 za pomocą `source activate` polecenia jako administrator lub jako użytkownik z uprawnieniami sudo. Następnie można użyć Menedżera pakietów, takiego jak `conda` lub `pip` , aby zainstalować lub zaktualizować pakiety.


## <a name="r"></a>R

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | Microsoft R Open 3. x (100% zgodne z CRAN-R)<br /> Microsoft R Server 9. x Developer Edition (skalowalna platforma języka R oparta na przedsiębiorstwie)|
| Obsługiwane wersje DSVM      | Linux, Windows     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Systemy`C:\Program Files\Microsoft\ML Server\R_SERVER` <br />System`/usr/lib64/microsoft-r/3.3/lib64/R`    |
| Linki do przykładów      | Dołączono przykładowe notesy Jupyter dla języka R.     |
| Narzędzia pokrewne na DSVM      | Spark, Python, Julia      |
### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

**System Windows**:

* Uruchom w wierszu polecenia:

  Otwórz wiersz polecenia i wpisz `R`.

* Użyj w środowisku IDE:

  Użyj programu RTools for Visual Studio (RTVS) zainstalowanego w programie Visual Studio Community Edition lub RStudio. Są one dostępne w menu Start lub jako ikona pulpitu. 

* Użyj w Jupyter

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **R** , aby używać jądra Jupyter R (IRKernel).

* Zainstaluj pakiety języka R:

  Język R jest instalowany na DSVM w środowisku globalnym, który jest możliwy do odczytania przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom polecenie R przy użyciu jednej z powyższych metod. Następnie można uruchomić Menedżera `install.packages()` pakietów języka R, aby zainstalować lub zaktualizować pakiety.

System **Linux**:

* Uruchom w terminalu:

  Otwórz Terminal i uruchom `R`go.  

* Użyj w środowisku IDE:

  Użyj RStudio zainstalowanego w systemie Linux DSVM.  

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **R** , aby używać jądra Jupyter R (IRKernel). 

* Zainstaluj pakiety języka R:

  Język R jest instalowany na DSVM w środowisku globalnym, który jest możliwy do odczytania przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom polecenie R przy użyciu jednej z powyższych metod. Następnie można uruchomić Menedżera `install.packages()` pakietów języka R, aby zainstalować lub zaktualizować pakiety.


## <a name="julia"></a>Julia

|    |           |
| ------------- | ------------- |
| Obsługiwane wersje językowe | 0,6 |
| Obsługiwane wersje DSVM      | Linux, Windows     |
| Jak jest on skonfigurowany/instalowany w DSVM?  | Windows: zainstalowano o`C:\JuliaPro-VERSION`<br /> Linux: zainstalowano o`/opt/JuliaPro-VERSION`    |
| Linki do przykładów      | Dołączono przykładowe notesy Jupyter dla Julia.     |
| Narzędzia pokrewne na DSVM      | Python, R      |
### <a name="how-to-use-and-run-it"></a>Jak używać i uruchamiać    

**System Windows**:

* Uruchom z wiersza polecenia

  Otwórz wiersz polecenia i uruchom `julia`polecenie.
* Użyj w środowisku IDE:

  Używany `Juno` z Julia IDE zainstalowanym w DSVM i dostępny jako skrót na pulpicie.

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **wersję Julia**.

* Zainstaluj pakiety Julia:

  Domyślną lokalizacją Julia jest środowisko globalne, które jest odczytywane przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom program Julia przy użyciu jednej z powyższych metod. Następnie można uruchomić polecenia Menedżera pakietów Julia, takie jak `Pkg.add()` Instalowanie lub aktualizowanie pakietów.


System **Linux**:
* Uruchom w terminalu:

  Otwórz Terminal i uruchom `julia`go.
* Użyj w środowisku IDE:

  Użyj `Juno`, z Julia IDE zainstalowanym w DSVM i dostępne jako skrót menu **aplikacji** .

* Użyj w Jupyter:

  Otwórz Jupyter i wybierz pozycję **New (nowy** ), aby utworzyć nowy Notes. Typ jądra można ustawić jako **wersję Julia**.

* Zainstaluj pakiety Julia:

  Domyślną lokalizacją Julia jest środowisko globalne, które jest odczytywane przez wszystkich użytkowników. Ale tylko Administratorzy mogą zapisywać i instalować pakiety globalne. Aby zainstalować pakiety w środowisku globalnym, uruchom program Julia przy użyciu jednej z powyższych metod. Następnie można uruchomić polecenia Menedżera pakietów Julia, takie jak `Pkg.add()` Instalowanie lub aktualizowanie pakietów.

## <a name="other-languages"></a>Inne języki

**C#**: dostępne w systemie Windows i dostępne za pomocą programu Visual Studio Community Edition lub `Developer Command Prompt for Visual Studio`w miejscu, w którym można `csc` uruchomić polecenie.

**Java**: OpenJDK jest dostępny zarówno w wersjach systemu Linux, jak i Windows DSVM i są ustawione na ścieżce. Aby użyć języka Java, wpisz `javac` polecenie `java` lub w wierszu polecenia w systemie Windows lub w powłoce bash na komputerze z systemem Linux.

**Node. js**: Node. js jest dostępny zarówno w wersjach systemu Linux, jak i Windows DSVM i są ustawione na ścieżce. Aby uzyskać dostęp do środowiska Node. js `node` , `npm` wpisz polecenie lub w wierszu polecenia w systemie Windows lub w powłoce bash na komputerze z systemem Linux. W systemie Windows jest zainstalowane rozszerzenie programu Visual Studio dla narzędzi Node. js, aby zapewnić graficzne środowisko IDE do tworzenia aplikacji node. js.

**F #**: dostępne w systemie Windows i dostępne za pomocą programu Visual Studio Community Edition lub `Developer Command Prompt for Visual Studio`w a, gdzie można uruchomić `fsc` polecenie.
