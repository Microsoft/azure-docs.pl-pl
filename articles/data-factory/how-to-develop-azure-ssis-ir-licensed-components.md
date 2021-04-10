---
title: Zainstaluj licencjonowane składniki dla środowiska Azure-SSIS Integration Runtime
description: Dowiedz się, w jaki sposób dostawca ISV może opracowywać i instalować płatne lub licencjonowane składniki niestandardowe dla środowiska Azure-SSIS Integration Runtime
ms.service: data-factory
author: swinarko
ms.author: sawinark
ms.topic: conceptual
ms.custom: seo-lt-2019
ms.date: 07/09/2020
ms.openlocfilehash: fd62822e111346ee9a81a5d1bcce55191b19da02
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100386292"
---
# <a name="install-paid-or-licensed-custom-components-for-the-azure-ssis-integration-runtime"></a>Instalowanie płatnych lub licencjonowanych składników niestandardowych w środowisku Azure-SSIS Integration Runtime

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

W tym artykule opisano, jak niezależnego dostawcy oprogramowania mogą opracowywać i instalować płatne lub licencjonowane składniki niestandardowe dla pakietów SQL Server Integration Services (SSIS) działających na platformie Azure w środowisku Azure-SSIS Integration Runtime.

## <a name="the-problem"></a>Problem

Charakter środowiska Azure-SSIS Integration Runtime zawiera kilka wyzwań, które sprawiają, że typowe metody licencjonowania używane na potrzeby instalacji lokalnych składników niestandardowych są nieodpowiednie. W związku z tym Azure-SSIS IR wymaga innego podejścia.

-   Węzły Azure-SSIS IR są nietrwałe i mogą być przydzielona lub wydawane w dowolnym momencie. Można na przykład uruchomić lub zatrzymać węzły, aby zarządzać kosztami, lub skalować w górę i w dół za pomocą różnych rozmiarów węzłów. W związku z tym powiązanie licencji składnika innej firmy z określonym węzłem przy użyciu informacji specyficznych dla komputera, takich jak adres MAC lub identyfikator procesora, nie jest już możliwe.

-   Możesz również skalować Azure-SSIS IR w lub na zewnątrz, tak aby liczba węzłów można było zmniejszyć lub rozszerzyć w dowolnym momencie.

## <a name="the-solution"></a>Rozwiązanie

W wyniku ograniczeń tradycyjnych metod licencjonowania opisanych w poprzedniej sekcji Azure-SSIS IR zawiera nowe rozwiązanie. To rozwiązanie używa zmiennych środowiskowych systemu Windows i zmiennych systemowych SSIS do powiązania licencji i weryfikacji składników innych firm. Dostawcy ISV mogą używać tych zmiennych do uzyskiwania unikatowych i trwałych informacji dla Azure-SSIS IR, takich jak identyfikator klastra i liczba węzłów klastra. Przy użyciu tych informacji dostawcy ISV mogą następnie powiązać licencję z składnikiem z Azure-SSIS IR *jako klaster*. To powiązanie używa identyfikatora, który nie zmienia się, gdy klienci zaczynają lub zatrzymują, skalować w górę lub w dół, skalować w górę lub w dół, a także ponownie skonfigurować Azure-SSIS IR w dowolny sposób.

Na poniższym diagramie przedstawiono typowe powiązania instalacji, aktywacji i licencji oraz przepływy walidacji dla składników innych firm korzystających z tych nowych zmiennych:

![Instalacja licencjonowanych składników](media/how-to-configure-azure-ssis-ir-licensed-components/licensed-component-installation.png)

## <a name="instructions"></a>Instrukcje
1. Niezależni dostawcy oprogramowania mogą oferować licencjonowane składniki w różnych jednostkach SKU i warstwach (na przykład pojedynczy węzeł, maksymalnie 5 węzłów, do 10 węzłów itd.). Dostawca oprogramowania dostarcza odpowiedni klucz produktu, gdy klienci kupują produkt. Dostawca niezależnego dostawcy oprogramowania może również udostępnić kontener obiektów BLOB usługi Azure Storage, który zawiera skrypt instalacji niezależnego dostawcy oprogramowania i skojarzone pliki. Klienci mogą kopiować te pliki do własnego kontenera magazynu i modyfikować je przy użyciu własnego klucza produktu (na przykład przez uruchomienie `IsvSetup.exe -pid xxxx-xxxx-xxxx` ). Następnie klienci mogą inicjować lub zmieniać konfigurację Azure-SSIS IR przy użyciu identyfikatora URI sygnatury dostępu współdzielonego kontenera jako parametru. Aby uzyskać więcej informacji, zobacz [Niestandardowa konfiguracja środowiska Azure SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md).

2. Po zainicjowaniu lub ponownym skonfigurowaniu Azure-SSIS IR Instalator niezależnego dostawcy oprogramowania uruchamia zapytanie o zmienne środowiskowe systemu Windows `SSIS_CLUSTERID` i `SSIS_CLUSTERNODECOUNT` . Następnie Azure-SSIS IR przesyła swój identyfikator klastra i klucz produktu licencjonowanego produktu do serwera aktywacji niezależnego dostawcy oprogramowania w celu wygenerowania klucza aktywacji.

3. Po otrzymaniu klucza aktywacji program instalacyjny ISV może przechowywać klucz lokalnie w każdym węźle (na przykład w rejestrze).

4. Gdy klienci uruchamiają pakiet, który używa licencjonowanego składnika niezależnego dostawcy oprogramowania w węźle Azure-SSIS IR, pakiet odczytuje lokalnie zapisany klucz aktywacji i weryfikuje go względem identyfikatora klastra węzła. Pakiet może również opcjonalnie zgłosić liczbę węzłów klastra do serwera aktywacji niezależnego dostawcy oprogramowania.

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

## <a name="isv-partners"></a>Partnerzy niezależnego dostawcy oprogramowania

Listę partnerów niezależnych dostawców oprogramowania, którzy dostosowali składniki i rozszerzenia dla Azure-SSIS IR na końcu tego wpisu w blogu — [Enterprise Edition, Konfiguracja niestandardowa i rozszerzalność innych firm dla usług SSIS w podajniku ADF](https://techcommunity.microsoft.com/t5/SQL-Server-Integration-Services/Enterprise-Edition-Custom-Setup-and-3rd-Party-Extensibility-for/ba-p/388360).

## <a name="next-steps"></a>Następne kroki

-   [Konfiguracja niestandardowa środowiska Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-custom-setup.md)

-   [Wersja Enterprise Azure-SSIS Integration Runtime](how-to-configure-azure-ssis-ir-enterprise-edition.md)
