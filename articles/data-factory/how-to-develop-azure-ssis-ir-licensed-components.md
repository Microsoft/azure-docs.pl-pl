---
title: Instalowanie licencjonowanych składników dla środowiska wykonawczego integracji usługi Azure-SSIS
description: Dowiedz się, jak niezależny niezależny niezależny od użytkownika może tworzyć i instalować płatne lub licencjonowane składniki niestandardowe dla środowiska wykonawczego integracji usługi Azure-SSIS
services: data-factory
ms.service: data-factory
ms.workload: data-services
author: swinarko
ms.author: sawinark
manager: mflasko
ms.reviewer: douglasl
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 08/01/2019
ms.openlocfilehash: 3a4c1f8b95c66c26944d455a2ab6163d1da72731
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81414336"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalowanie płatnych lub licencjonowanych składników niestandardowych w środowisku Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano, jak niezależny niezależny numer niezależny może tworzyć i instalować płatne lub licencjonowane składniki niestandardowe dla pakietów usług integracji programu SQL Server (SSIS), które są uruchamiane na platformie Azure w czasie wykonywania integracji platformy Azure-SSIS.

## <a name="the-problem"></a>Problem

Charakter środowiska wykonawczego integracji azure-SSIS przedstawia kilka wyzwań, które sprawiają, że typowe metody licencjonowania używane do instalacji lokalnej składników niestandardowych są niewystarczające. W rezultacie azure-SSIS IR wymaga innego podejścia.

-   Węzły usługi Azure-SSIS IR są nietrwałe i mogą być przydzielane lub zwalniane w dowolnym momencie. Na przykład można uruchomić lub zatrzymać węzły, aby zarządzać kosztem lub skalować w górę iw dół za pośrednictwem różnych rozmiarów węzłów. W rezultacie powiązanie licencji składnika innej firmy z określonym węzłem przy użyciu informacji specyficznych dla komputera, takich jak adres MAC lub identyfikator procesora CPU, nie jest już opłacalne.

-   Można również skalować ir azure-SSIS w lub na zewnątrz, tak aby liczba węzłów można zmniejszyć lub rozwinąć w dowolnym momencie.

## <a name="the-solution"></a>Rozwiązanie

W wyniku ograniczeń tradycyjnych metod licencjonowania opisanych w poprzedniej sekcji azure-SSIS IR zapewnia nowe rozwiązanie. To rozwiązanie używa zmiennych środowiskowych systemu Windows i zmiennych systemowych SSIS do wiązania licencji i sprawdzania poprawności składników innych firm. Niezależni dostawcy oprogramowania mogą używać tych zmiennych do uzyskiwania unikatowych i trwałych informacji dla usługi Azure-SSIS IR, takich jak identyfikator klastra i liczba węzłów klastra. Dzięki tym informacjom niezależni dostawcy oprogramowania mogą następnie powiązać licencję dla swojego składnika z usługą Azure-SSIS IR *jako klaster.* To powiązanie używa identyfikatora, który nie zmienia się, gdy klienci rozpoczynają lub zatrzymują, skalują w górę lub w dół, skalują się lub wychodzą, ani w jakikolwiek sposób konfigurują ir azure-SSIS.

Na poniższym diagramie przedstawiono typowe przepływy instalacji, aktywacji i licencji oraz przepływy sprawdzania poprawności dla składników innych firm, które używają tych nowych zmiennych:

![Instalacja licencjonowanych komponentów](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instrukcje
1. Dostawcy oprogramowania mogą oferować swoje licencjonowane składniki w różnych jednostkach SKU lub warstwach (na przykład pojedynczy węzeł, do 5 węzłów, do 10 węzłów itd.). Isv zapewnia odpowiedni klucz produktu, gdy klienci kupują produkt. Niezależny numer zięcia sieci owego może również dostarczyć kontener obiektów blob usługi Azure Storage zawierający skrypt instalatora niezależnego systemu i skojarzone pliki. Klienci mogą kopiować te pliki do własnego kontenera magazynu i modyfikować `IsvSetup.exe -pid xxxx-xxxx-xxxx`je za pomocą własnego klucza produktu (na przykład przez uruchomienie). Klienci mogą następnie aprowizować lub ponownie skonfigurować identyfikator Azure-SSIS IR przy za pomocą identyfikatora URI sygnatury dostępu Współdzielonego ich kontenera jako parametru. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Gdy usługa Azure-SSIS IR jest aprowizowana lub ponownie skonfigurowana, Instalator isv `SSIS_CLUSTERID` uruchamia `SSIS_CLUSTERNODECOUNT`się w każdym węźle, aby wysyłać zapytania do zmiennych środowiskowych systemu Windows i . . Następnie usługa Azure-SSIS IR przesyła swój identyfikator klastra i klucz produktu licencjonowanego do serwera aktywacji niezależnego systemu isv w celu wygenerowania klucza aktywacyjnego.

3. Po otrzymaniu klucza aktywacji Instalator isv może przechowywać klucz lokalnie w każdym węźle (na przykład w rejestrze).

4. Gdy klienci uruchamiają pakiet, który używa licencjonowanego składnika isv w węźle usługi Azure-SSIS IR, pakiet odczytuje lokalnie przechowywany klucz aktywacji i sprawdza poprawność go względem identyfikatora klastra węzła. Pakiet może również opcjonalnie zgłosić liczbę węzłów klastra do serwera aktywacji isv.

    Oto przykład kodu, który sprawdza poprawność klucza aktywacji i zgłasza liczbę węzłów klastra:

    ```csharp
    public override DTSExecResult Validate(Connections, VariableDispenser, IDTSComponentEvents componentEvents, IDTSLogging log) 
                                                                                                                               
    {                                                                                                                             
                                                                                                                               
    Variables vars = null;                                                                                                        
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterID");                                                                           
                                                                                                                               
    variableDispenser.LockForRead("System::ClusterNodeCount");                                                                    
                                                                                                                               
    variableDispenser.GetVariables(ref vars);                                                                                     
                                                                                                                               
    // Validate Activation Key with ClusterID                                                                                     
                                                                                                                               
    // Report on ClusterNodeCount                                                                                                 
                                                                                                                               
    vars.Unlock();                                                                                                                
                                                                                                                               
    return base.Validate(connections, variableDispenser, componentEvents, log);                                                   
                                                                                                                               
    }
    ```

## <a name="isv-partners"></a>Partnerzy isv

Listę partnerów dostawców usług internetowych, którzy dostosowali swoje składniki i rozszerzenia do usługi Azure-SSIS IR, można znaleźć na końcu tego wpisu w blogu — [Enterprise Edition, Custom Setup i 3rd Party Extensibility for SSIS in ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Następne kroki

-   [Niestandardowa konfiguracja środowiska wykonawczego integracji azure-SSIS](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Wersja enterprise środowiska wykonawczego integracji azure-SSIS](how-to-configure-azure-ssis-ir-enterprise-edition.md)
