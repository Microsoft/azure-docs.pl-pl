---
title: Przykładowe typy klas w Azure Lab Services | Microsoft Docs
description: Zawiera typy klas, dla których można skonfigurować laboratoria przy użyciu Azure Lab Services.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 5a90fb128f5954f3eb713714ff22ff40a3beab36
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "99627437"
---
# <a name="class-types-overview---azure-lab-services"></a>Przegląd typów klas — Azure Lab Services

Azure Lab Services umożliwia szybkie Konfigurowanie środowisk laboratoryjnych w chmurze. Artykuły w tej sekcji zawierają wskazówki dotyczące konfigurowania kilku typów laboratoriów przy użyciu Azure Lab Services.

## <a name="arcgis"></a>ArcGIS
[ArcGIS](https://www.esri.com/en-us/arcgis/products/arcgis-solutions/overview) to typ geograficznego systemu informacji (GIS).  Można skonfigurować laboratorium, które używa różnych aplikacji ArcGIS Desktop, takich jak [ArcMap](https://desktop.arcgis.com/en/arcmap/latest/map/main/what-is-arcmap-.htm) , aby tworzyć, edytować i analizować mapy 2D.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie programu Lab for ArcMap\ArcGIS Desktop](class-type-arcgis.md).

## <a name="big-data-analytics"></a>Analiza danych big data
Można skonfigurować laboratorium procesora GPU do uczenia klasy analizy danych Big Data. Z tego typu klasy studenci mogą dowiedzieć się, jak obsługiwać duże ilości danych, i stosować algorytmy uczenia maszynowego i statystycznego w celu uzyskania szczegółowych informacji. Najważniejszym celem dla studentów jest Nauka korzystania z narzędzi analitycznych danych, takich jak pakiet oprogramowania typu open source Apache Hadoop, który oferuje narzędzia do przechowywania i przetwarzania danych Big Data oraz zarządzania nimi. 

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium na potrzeby analizy danych Big Data za pomocą platformy Docker Deployment HortonWorks Data Platform](class-type-big-data-analytics.md).

## <a name="database-management"></a>Zarządzanie bazami danych
Pojęcia dotyczące baz danych są jednym z kursów wprowadzających w większości działów nauki komputerowych w szkole. W Azure Lab Services można skonfigurować laboratorium dla podstawowej klasy zarządzania bazami danych. Na przykład można skonfigurować szablon maszyny wirtualnej w laboratorium z serwerem bazy danych [MySQL](https://www.mysql.com/) lub serwerem [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) .

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia zarządzania bazami danych dla relacyjnych baz danych](class-type-database-management.md).

## <a name="deep-learning-in-natural-language-processing"></a>Uczenie głębokie w ramach przetwarzania języka naturalnego
Można skonfigurować laboratorium ukierunkowane na głębokie uczenie w ramach przetwarzania języka naturalnego (NLP) przy użyciu Azure Lab Services. Przetwarzanie języka naturalnego (NLP) jest formą sztucznej analizy (AI), która umożliwia komputerom z translacją, rozpoznawanie mowy i innymi funkcjami interpretacji języka. Studenci tworzący klasę NLPą mogą uzyskać maszynę wirtualną z systemem Linux, aby dowiedzieć się, jak zastosować algorytmy sieciowe neuronowych w celu opracowania modeli uczenia głębokiego, które są używane do analizowania zapisanych języków ludzkich.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium ukierunkowanego na głębokie uczenie w zakresie przetwarzania w języku naturalnym przy użyciu Azure Lab Services](class-type-deep-learning-natural-language-processing.md).

## <a name="ethical-hacking"></a>Etyczne działania hakerskie
Można skonfigurować laboratorium dla klasy, która koncentruje się na dowodowych etycznej działanie hakerskie. Testowanie penetracji, metoda stosowana przez społeczność etyczną działanie hakerskie, występuje, gdy ktoś próbuje uzyskać dostęp do systemu lub sieci w celu zademonstrowania luk w zabezpieczeniach.

W klasie etycznej działanie hakerskie studenci mogą uczyć się nowoczesnych technik obrony przed lukami w zabezpieczeniach. Każdy student otrzymuje maszynę wirtualną hosta systemu Windows Server, która ma dwie zagnieżdżone maszyny wirtualne — jedną maszynę wirtualną z obrazem [Metasploitable3](https://github.com/rapid7/metasploitable3) i inną maszynę z obrazem [Kali Linux](https://www.kali.org/) . Maszyna wirtualna Metasploitable jest używana do wykorzystania w celach.  Maszyna wirtualna z systemem Kali Linux zapewnia dostęp do narzędzi wymaganych do wykonywania zadań śledczej.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia klasy etycznej działanie hakerskie](class-type-ethical-hacking.md).

## <a name="matlab"></a>MATLAB
Program [MATLAB](https://www.mathworks.com/products/matlab.html), który jest przeznaczony dla laboratorium Matrix, jest platformą programistyczną [MathWorks](https://www.mathworks.com/).  Łączy moc obliczeniową i wizualizację, co sprawia, że jest to popularne narzędzie w dziedzinach matematycznych, inżynieryjnych, fizyki i chemii.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia programu MATLAB](class-type-matlab.md).

## <a name="networking-with-gns3"></a>Sieć z GNS3
Można skonfigurować laboratorium dla klasy, która koncentruje się na umożliwieniu uczniów do emulowania, konfigurowania, testowania i rozwiązywania problemów z sieciami wirtualnymi i rzeczywistymi przy użyciu oprogramowania [GNS3](https://www.gns3.com/) . 

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia klasy sieci](class-type-networking-gns3.md).

## <a name="project-lead-the-way-pltw"></a>Projekt potencjalnym liderem (PLTW)
[Potencjalni klienci projektu w ten sposób (PLTW)](https://www.pltw.org/) to organizacja non profit, która zapewnia PreK-12 dla Stany Zjednoczone w nauce technologicznej, inżynieryjnej i biomedycznej.  W każdej klasie PLTW studenci korzystają z różnych aplikacji w ramach praktycznego doświadczenia.

Aby uzyskać szczegółowe informacje na temat konfigurowania tych typów laboratoriów, zobacz [Konfigurowanie laboratoriów dla projektu lider klasy](class-type-pltw.md).

## <a name="python-and-jupyter-notebooks"></a>Notesy Python i Jupyter
Można skonfigurować maszynę szablonu w Azure Lab Services z narzędziami wymaganymi do nauki uczniów, jak korzystać z [notesów Jupyter](http://jupyter-notebook.readthedocs.io). Notesy Jupyter to projekt typu "open source", który umożliwia łatwe łączenie tekstu sformatowanego i wykonywalnego kodu źródłowego języka [Python](https://www.python.org/) na jednej kanwie o nazwie Notes. Uruchamianie notesu skutkuje liniowym rekordem danych wejściowych i wyjściowych.  Mogą one obejmować tekst, tabele informacji, wykresy punktowe i wiele innych.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia danych przy użyciu notesów Python i Jupyter](class-type-jupyter-notebook.md).

## <a name="shell-scripting-on-linux"></a>Skrypty powłoki w systemie Linux
Można skonfigurować laboratorium do uczenia skryptów powłoki w systemie Linux. Obsługa skryptów jest przydatną częścią administrowania systemem, która umożliwia administratorom uniknięcie powtarzających się zadań. W tym przykładowym scenariuszu Klasa obejmuje tradycyjne skrypty bash oraz ulepszone skrypty. Ulepszone skrypty to skrypty, które łączą bash polecenia i Ruby. Takie podejście umożliwia używanie języka Ruby do przekazywania danych wokół i bash poleceń w celu współdziałania z powłoką.

Studenci korzystający z tych klas skryptów otrzymują maszynę wirtualną z systemem Linux, aby poznać podstawy systemu Linux, a także zapoznać się z obsługą skryptów powłoki bash. Na maszynie wirtualnej z systemem Linux jest włączona funkcja dostępu do pulpitu zdalnego z zainstalowanym edytorem tekstu w języku [regedit](https://help.gnome.org/users/gedit/stable/) i [Visual Studio Code](https://code.visualstudio.com/) .

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Skrypty powłoki w systemie Linux](class-type-shell-scripting-linux.md).

## <a name="solidworks-computer-aided-design-cad"></a>Oprogramowanie SolidWorks do projektowania wspomaganego komputerem (CAD)
Można skonfigurować laboratorium procesora GPU, które daje studentom inżynierów dostęp do [SolidWorks](https://www.solidworks.com/).  SolidWorks udostępnia środowisko CAD 3D do modelowania obiektów stałych.  Dzięki SolidWorks inżynierowie mogą łatwo tworzyć, wizualizować, symulować i dokumentować projekty.

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium dla klas inżynierii przy użyciu SolidWorks](class-type-solidworks.md).

## <a name="sql-database-and-management"></a>Baza danych SQL i zarządzanie
Structured Query Language (SQL) to standardowy język zarządzania relacyjnymi bazami danych, w tym dodawanie, uzyskiwanie dostępu do zawartości i zarządzanie nią w bazie danych.  Można skonfigurować laboratorium do uczenia koncepcji baz danych przy użyciu serwera bazy danych [MySQL](https://www.mysql.com/) i serwera [SQL Server 2019](https://www.microsoft.com/sql-server/sql-server-2019) .

Aby uzyskać szczegółowe informacje na temat sposobu konfigurowania tego typu laboratorium, zobacz [Konfigurowanie laboratorium do uczenia zarządzania bazami danych dla relacyjnych baz danych](class-type-database-management.md).

## <a name="next-steps"></a>Następne kroki
Zobacz następujące artykuły:

- [Skonfiguruj laboratorium ukierunkowane na głębokie uczenie się w przetwarzaniu języka naturalnego przy użyciu Azure Lab Services](class-type-deep-learning-natural-language-processing.md)
- [Skrypty powłoki w systemie Linux](class-type-shell-scripting-linux.md)
- [Etyczne działania hakerskie](class-type-ethical-hacking.md)