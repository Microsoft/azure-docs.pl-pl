---
title: Tworzenie własnego środowiska Integration Runtime
description: Dowiedz się, jak utworzyć środowisko uruchomieniowe integracji hostowanego samodzielnie w usłudze Azure Data Factory, które umożliwia fabrykom danych dostęp do magazynów danych w sieci prywatnej.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 03/13/2020
ms.openlocfilehash: 6bc0f002c6927cfd9a314797663e1dabbac392b6
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81416638"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Tworzenie i konfigurowanie własnego środowiska Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Środowisko wykonawcze integracji (IR) to infrastruktura obliczeniowa używana przez usługę Azure Data Factory w celu zapewnienia możliwości integracji danych w różnych środowiskach sieciowych. Aby uzyskać szczegółowe informacje na temat środowiska IR, zobacz [Omówienie środowiska uruchomieniowego integracji](concepts-integration-runtime.md).

Środowisko wykonawcze integracji hostowanego samodzielnie może uruchamiać działania kopiowania między magazynem danych w chmurze a magazynem danych w sieci prywatnej. Może również wysyłać działania przekształcania względem zasobów obliczeniowych w sieci lokalnej lub sieci wirtualnej platformy Azure. Instalacja środowiska wykonawczego integracji hostowanego przez własny host wymaga komputera lokalnego lub maszyny wirtualnej w sieci prywatnej.  

W tym artykule opisano, jak można utworzyć i skonfigurować samodzielnie hostowane IR.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Konfigurowanie środowiska wykonawczego integracji hostowanego samodzielnie

Aby utworzyć i skonfigurować środowisko uruchomieniowe integracji hostowanego samodzielnie, należy użyć następujących procedur.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Tworzenie samodzielnego hostowania podczerwony za pośrednictwem programu Azure PowerShell

1. W tym zadaniu można użyć programu Azure PowerShell. Oto przykład:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Pobierz](https://www.microsoft.com/download/details.aspx?id=39717) i zainstaluj środowisko uruchomieniowe integracji hostowanego samodzielnie na komputerze lokalnym.

3. Pobierz klucz uwierzytelniania i zarejestruj środowisko uruchomieniowe integracji hostowanego samodzielnie za pomocą klucza. Oto przykład programu PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Tworzenie samodzielnego hostowanego podczerwonyej usługi IR za pośrednictwem interfejsu użytkownika usługi Azure Data Factory

Poniższe kroki można wykonać, aby utworzyć samodzielnie hostowane podczerwone podczerwone przy użyciu interfejsu użytkownika usługi Azure Data Factory.

1. Na stronie **Rozpocznijmy pracę** interfejsu użytkownika usługi Azure Data Factory, wybierz kartę **Autor** w lewym okienku.

   ![Przycisk Autor strony głównej](media/doc-common-process/get-started-page-author-button.png)

1. Wybierz **pozycję Połączenia** u dołu okienka znajdującego się po lewej stronie i wybierz pozycję Środowiska **wykonawcze integracji** w oknie **Połączenia.** Wybierz **+Nowy**.

   ![Tworzenie środowiska Integration Runtime](media/create-self-hosted-integration-runtime/new-integration-runtime.png)

1. Na stronie **Ustawienia środowiska wykonawczego integracji** wybierz pozycję **Azure, Self-Hosted**, a następnie wybierz pozycję **Kontynuuj**. 

1. Na następnej stronie wybierz **pozycję Hostowane samodzielnie,** aby utworzyć samodzielną ir, a następnie wybierz pozycję **Kontynuuj**.
   ![Tworzenie samodzielnego podczerwony](media/create-self-hosted-integration-runtime/new-selfhosted-ir.png)

1. Wprowadź nazwę podczerwonego konta IR i wybierz pozycję **Utwórz**.

1. Na stronie **Ustawienia środowiska wykonawczego integracji** wybierz łącze w obszarze **Opcja 1,** aby otworzyć konfigurację ekspresową na komputerze. Możesz też wykonać czynności opisane w **obszarze Opcja 2,** aby skonfigurować je ręcznie. Poniższe instrukcje są oparte na konfiguracji ręcznej:

   ![Instalacja środowiska Integration Runtime](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Skopiuj i wklej klucz uwierzytelniania. Wybierz **pozycję Pobierz i zainstaluj środowisko wykonawcze integracji**.

    1. Pobierz środowisko Integration Runtime (Self-hosted) na lokalną maszynę z systemem Windows. Uruchom instalatora.

    1. Na stronie **Zarejestruj środowisko uruchomieniowe integracji (hostowane samodzielnie)** wklej klucz zapisany wcześniej i wybierz pozycję **Zarejestruj**.
    
       ![Rejestrowanie środowiska Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Na stronie **Węzeł Nowy środowisko wykonawcze integracji wybierz** pozycję **Zakończ**.

1. Po pomyślnym zarejestrowaniu środowiska wykonawczego integracji hostowanego przez siebie zostanie wyświetlone następujące okno:

    ![Rejestracja zakończona powodzeniem](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Konfigurowanie samodzielnego hostowanego podczerwonu na maszynie wirtualnej platformy Azure za pomocą szablonu usługi Azure Resource Manager

Konfigurację samodzielnej podczerwony hostowanej można zautomatyzować na maszynie wirtualnej platformy Azure przy użyciu [szablonu Utwórz własny host podczerwony.](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime) Szablon zapewnia łatwy sposób, aby mieć w pełni funkcjonalne self-hosted IR wewnątrz sieci wirtualnej platformy Azure. Podczerwony ir ma funkcje wysokiej dostępności i skalowalności, tak długo, jak można ustawić liczbę węzłów na 2 lub więcej.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Konfigurowanie istniejącej samodzielnej usługi IR za pośrednictwem lokalnego programu PowerShell

Za pomocą wiersza polecenia można skonfigurować istniejącą, samodzielnie hostowane podczerwone podczerwone lub zarządzać nim. To użycie może szczególnie pomóc zautomatyzować instalację i rejestrację samodzielnie hostowanych węzłów podczerwonych.

Program Dmgcmd.exe jest zawarty w instalatorze hostowanym przez siebie. Zazwyczaj znajduje się w folderze C:\Program Files\Microsoft Integration Runtime\3.0\Shared\. Ta aplikacja obsługuje różne parametry i może być wywoływana za pomocą wiersza polecenia przy użyciu skryptów wsadowych do automatyzacji.

Użyj aplikacji w następujący sposób:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Oto szczegóły dotyczące parametrów i właściwości aplikacji: 

| Właściwość                                                    | Opis                                                  | Wymagany |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **ZarejestrujNewNode** "`<AuthenticationKey>`"                     | Zarejestruj węzeł środowiska uruchomieniowego integracji hostowanego samodzielnie przy użyciu określonego klucza uwierzytelniania. | Nie       |
| **ZarejestrujNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | Zarejestruj węzeł środowiska uruchomieniowego integracji hostowanego samodzielnie z określonym kluczem uwierzytelniania i nazwą węzła. | Nie       |
| **EnableRemoteAccess** "`<port>`" ["`<thumbprint>`"]            | Włącz dostęp zdalny w bieżącym węźle, aby skonfigurować klaster o wysokiej dostępności. Możesz też włączyć ustawianie poświadczeń bezpośrednio względem samodzielnie hostowanego podczerwona podczerwony bez przechodzenia przez usługę Azure Data Factory. Ten ostatni można wykonać przy użyciu polecenia cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredCredCredential** z komputera zdalnego w tej samej sieci. | Nie       |
| **EnableRemoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | Włącz zdalny dostęp do bieżącego węzła, gdy węzeł działa w kontenerze. | Nie       |
| **DisableRemoteAccess**                                         | Wyłącz zdalny dostęp do bieżącego węzła. Do konfiguracji wielokanek wymagany jest dostęp zdalny. Polecenie cmdlet **Programu New-AzDataFactoryV2LinkedServiceEncryptedCredential** PowerShell nadal działa nawet wtedy, gdy dostęp zdalny jest wyłączony. To zachowanie jest prawdziwe, tak długo, jak polecenie cmdlet jest wykonywane na tym samym komputerze co samodzielnie hostowany węzeł podczerwony. | Nie       |
| **Klucz** "`<AuthenticationKey>`"                                 | Zastąpuj lub zaktualizuj poprzedni klucz uwierzytelniania. Bądź ostrożny z tym działaniem. Poprzedni samodzielnie hostowany węzeł podczerwony można przejść do trybu offline, jeśli klucz jest z nowego środowiska wykonawczego integracji. | Nie       |
| **Plik generatebackup** "`<filePath>`" "`<password>`"            | Generowanie pliku kopii zapasowej dla bieżącego węzła. Plik kopii zapasowej zawiera klucz węzła i poświadczenia magazynu danych. | Nie       |
| **Plik ImportBackup** "`<filePath>`" "`<password>`"              | Przywracanie węzła z pliku kopii zapasowej.                          | Nie       |
| **Ponownie uruchomić**                                                     | Uruchom ponownie usługę hostowania środowiska uruchomieniowego integracji samodzielnie.   | Nie       |
| **Początek**                                                       | Uruchom usługę hostowania środowiska uruchomieniowego integracji samodzielnie hostowane.     | Nie       |
| **Zatrzymaj**                                                        | Zatrzymaj usługę hostowania środowiska uruchomieniowego integracji samodzielnie hostowane.        | Nie       |
| **Uruchomienie usługi**                                         | Uruchom usługę samodzielnego uaktualniania środowiska uruchomieniowego integracji.       | Nie       |
| **Usługa StopUpgradeService**                                          | Zatrzymaj usługę samodzielnego uaktualniania środowiska wykonawczego integracji.        | Nie       |
| **TurnOnAutoUpdate**                                            | Włącz autoaktualizę środowiska uruchomieniowego integracji hostowanego samodzielnie.        | Nie       |
| **TurnOffAutoUpdate**                                           | Wyłącz autoaktualizę środowiska uruchomieniowego integracji hostowanego samodzielnie.       | Nie       |
| **Konto usługi SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | Ustaw DIAHostService, aby działała jako nowe konto. Użyj pustego hasła "" dla kont systemowych i kont wirtualnych. | Nie       |


## <a name="command-flow-and-data-flow"></a>Przepływ poleceń i przepływ danych

Podczas przenoszenia danych między środowiskiem lokalnym a chmurą działanie używa środowiska uruchomieniowego integracji hostowanego przez siebie do przesyłania danych między lokalnym źródłem danych a chmurą.

Oto podsumowanie wysokiego poziomu kroków przepływu danych do kopiowania z samodzielnie hostowanym podczerwienią:

![Ogólny przegląd przepływu danych na wysokim poziomie](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Deweloper danych tworzy środowisko uruchomieniowe integracji hostowanego samodzielnie w fabryce danych platformy Azure przy użyciu polecenia cmdlet programu PowerShell. Obecnie witryna Azure portal nie obsługuje tej funkcji.
1. Deweloper danych tworzy połączony serwis dla lokalnego magazynu danych. Deweloper robi to, określając wystąpienie środowiska uruchomieniowego integracji hostowanego samodzielnie, którego usługa powinna używać do łączenia się z magazynami danych.
1. Węzeł środowiska uruchomieniowego integracji hostowanej samodzielnie szyfruje poświadczenia przy użyciu interfejsu DPAPI (Data Protection Application Programming Interface) systemu Windows i zapisuje poświadczenia lokalnie. Jeśli wiele węzłów są ustawione dla wysokiej dostępności, poświadczenia są dalej synchronizowane między innymi węzłami. Każdy węzeł szyfruje poświadczenia przy użyciu dpapi i przechowuje je lokalnie. Synchronizacja poświadczeń jest niewidoczny dla dewelopera danych i jest obsługiwany przez samodzielnie hostowane IR.
1. Usługa Azure Data Factory komunikuje się ze środowiskom uruchomieniowym integracji hostowanego przez własny host, aby zaplanować zadania i zarządzać nimi. Komunikacja odbywa się za pośrednictwem kanału sterowania, który używa udostępnionego połączenia [usługi Azure Service Bus Relay.](https://docs.microsoft.com/azure/service-bus-relay/relay-what-is-it#wcf-relay) Gdy zadanie działania musi zostać uruchomione, usługa Data Factory kolejkuje żądanie wraz z wszelkimi informacjami o poświadczeniach. Robi to w przypadku, gdy poświadczenia nie są już przechowywane w czasie wykonywania integracji hostowanej samodzielnie. Środowisko uruchomieniowe integracji hostowanego samodzielnie uruchamia zadanie po sondacji kolejki.
1. Środowisko wykonawcze integracji hostowane samodzielnie kopiuje dane między magazynem lokalnym a magazynem w chmurze. Kierunek kopiowania zależy od sposobu konfigurowania działania kopiowania w potoku danych. W tym kroku środowisko wykonawcze integracji hostowanego samodzielnie komunikuje się bezpośrednio z usługami magazynu opartego na chmurze, takimi jak magazyn obiektów Blob platformy Azure za pośrednictwem bezpiecznego kanału HTTPS.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Zagadnienia dotyczące korzystania z samodzielnego hostowanego podczerwony

- Można użyć jednego środowiska uruchomieniowego integracji hostowanego samodzielnie dla wielu lokalnych źródeł danych. Można również udostępnić go innej fabryce danych w ramach tej samej dzierżawy usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [Udostępnianie środowiska uruchomieniowego integracji hostowanego samodzielnie.](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory)
- Można zainstalować tylko jedno wystąpienie środowiska uruchomieniowego integracji hostowanego samodzielnie na dowolnym komputerze. Jeśli masz dwie fabryki danych, które muszą uzyskać dostęp do lokalnych źródeł danych, użyj [samodzielnej funkcji udostępniania podczerwonych,](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) aby udostępnić samodzielnie hostowane podczerwone podczerwone lub zainstaluj samodzielnie hostowane podczerwony na dwóch komputerach lokalnych, po jednym dla każdej fabryki danych.  
- Środowisko uruchomieniowe integracji hostowanego samodzielnie nie musi znajdować się na tym samym komputerze co źródło danych. Jednak posiadanie środowiska uruchomieniowego integracji hostowanego samodzielnie w pobliżu źródła danych skraca czas, w czasie, w czasie wykonywania integracji hostowanego samodzielnie, aby połączyć się ze źródłem danych. Zaleca się zainstalowanie środowiska uruchomieniowego integracji hostowanego samodzielnie na komputerze, który różni się od tego, który obsługuje lokalne źródło danych. Gdy środowisko uruchomieniowe integracji hostowanego samodzielnie i źródło danych znajdują się na różnych komputerach, środowisko uruchomieniowe integracji hostowanego samodzielnie nie konkuruje ze źródłem danych dla zasobów.
- Można mieć wiele samodzielnie hostowanych środowisk uruchomieniowych integracji na różnych komputerach, które łączą się z tym samym lokalnym źródłem danych. Na przykład jeśli masz dwa środowiska wykonawcze integracji hostowane samodzielnie, które obsługują dwie fabryki danych, to samo lokalne źródło danych może być zarejestrowane w obu fabrykach danych.
- Jeśli masz już zainstalowaną bramę na komputerze do obsługi scenariusza usługi Power BI, zainstaluj oddzielny własny środowisko wykonawcze integracji dla usługi Data Factory na innym komputerze.
- Użyj środowiska uruchomieniowego integracji hostowanego samodzielnie do obsługi integracji danych w sieci wirtualnej platformy Azure.
- Traktuj źródło danych jako lokalne źródło danych, które znajduje się za zaporą, nawet podczas korzystania z usługi Azure ExpressRoute. Użyj środowiska uruchomieniowego integracji hostowanego samodzielnie, aby połączyć usługę ze źródłem danych.
- Użyj środowiska uruchomieniowego integracji hostowanego samodzielnie, nawet jeśli magazyn danych znajduje się w chmurze na maszynie wirtualnej infrastruktury azure jako usługi (IaaS).
- Zadania mogą zakończyć się niepowodzeniem w czasie uruchomieniowym integracji hostowanego samodzielnie, który został zainstalowany na serwerze Windows, dla którego jest włączone szyfrowanie zgodne ze standardem FIPS. Aby obejść ten problem, należy wyłączyć szyfrowanie zgodne ze standardem FIPS na serwerze. Aby wyłączyć szyfrowanie zgodne ze standardem FIPS, zmień wartość następującego podklucza `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled`rejestru z 1 (włączone) na 0 (wyłączone): .

## <a name="prerequisites"></a>Wymagania wstępne

- Obsługiwane wersje systemu Windows to:
  + Windows 7 z dodatkiem Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 R2
  + Windows Server 2016
  + Windows Server 2019
   
   Instalacja środowiska wykonawczego integracji hostowanego samodzielnie na kontrolerze domeny nie jest obsługiwana.
- .NET Framework 4.6.1 lub nowsze jest wymagane. Jeśli instalujesz środowisko wykonawcze integracji hostowanego samodzielnie na komputerze z systemem Windows 7, zainstaluj program .NET Framework 4.6.1 lub nowszą. Szczegółowe informacje można znaleźć w [wymaganiach systemowych programu .NET Framework.](/dotnet/framework/get-started/system-requirements)
- Zalecana minimalna konfiguracja dla samodzielnego współzastawu integracyjnego to procesor 2 GHz z 4 rdzeniami, 8 GB pamięci RAM i 80 GB dostępnego miejsca na dysku twardym.
- Jeśli komputer-host hibernuje, środowisko uruchomieniowe integracji hostowanego samodzielnie nie odpowiada na żądania danych. Skonfiguruj odpowiedni plan zasilania na komputerze przed zainstalowaniem środowiska wykonawczego integracji hostowanego samodzielnie. Jeśli komputer jest skonfigurowany do hibernacji, instalator środowiska uruchomieniowego integracji hostowanego samodzielnie monituje o komunikat.
- Aby pomyślnie zainstalować i skonfigurować środowisko uruchomieniowe integracji hostowanego samodzielnie, musisz być administratorem na komputerze.
- Przebiegi działania kopiowania odbywa się z określoną częstotliwością. Użycie procesora i pamięci RAM na komputerze odbywa się w tym samym wzorze z czasem szczytowym i bezczynności. Użycie zasobów zależy również w dużej mierze od ilości danych, które są przenoszone. Gdy wiele zadań kopiowania są w toku, widać użycie zasobów iść w górę w godzinach szczytu.
- Zadania mogą zakończyć się niepowodzeniem podczas wyodrębniania danych w formatach Parkiet, ORC lub Avro. Aby uzyskać więcej informacji na temat parkietu, zobacz [Format parkietu w usłudze Azure Data Factory](https://docs.microsoft.com/azure/data-factory/format-parquet#using-self-hosted-integration-runtime). Tworzenie plików jest uruchamiane na komputerze integracji hostowanego samodzielnie. Aby działać zgodnie z oczekiwaniami, tworzenie plików wymaga następujących wymagań wstępnych:
    - [Visual C++ 2010 redystrybucyjny](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Pakiet (x64)
    - Java Runtime (JRE) w wersji 8 od dostawcy środowiska JRE, takiego jak [Adopt OpenJDK](https://adoptopenjdk.net/). Upewnij się, że ustawiona `JAVA_HOME` jest zmienna środowiskowa.

## <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji

Środowisko wykonawcze integracji hostowanej samodzielnie można zainstalować, pobierając pakiet konfiguracji tożsamości zarządzanej z [Centrum pobierania Firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Zobacz artykuł [Przenoszenie danych między środowiskiem lokalnym a chmurą,](tutorial-hybrid-copy-powershell.md) aby uzyskać instrukcje krok po kroku.

- Skonfiguruj plan zasilania na komputerze-hoście dla środowiska wykonawczego integracji hostowanego samodzielnie, aby maszyna nie hibernowała. Jeśli komputer-host przechodzi w stan hibernacji, środowisko wykonawcze integracji hostowanego samodzielnie przechodzi w tryb offline.
- Regularnie śmiwaluj zapasy poświadczeń skojarzonych ze środowiska uruchomieniowego integracji hostowanego samodzielnie.
- Aby zautomatyzować samodzielne operacje konfiguracji podczerwonej, zapoznaj [się z artykułem Konfigurowanie istniejącej samodzielnej obsługi podczerwonej za pośrednictwem programu PowerShell.](#setting-up-a-self-hosted-integration-runtime)  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Instalowanie i rejestrowanie samodzielnego podczerwonyego podczerwony z Centrum pobierania Microsoft

1. Przejdź do [strony pobierania środowiska wykonawczego integracji firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=39717).
1. Wybierz **pobierz**, wybierz wersję 64-bitową i wybierz **pozycję Dalej**. Wersja 32-bitowa nie jest obsługiwana.
1. Uruchom plik tożsamości zarządzanej bezpośrednio lub zapisz go na dysku twardym i uruchom go.
1. W oknie **Powitalny** wybierz język i wybierz pozycję **Dalej**.
1. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft i wybierz pozycję **Dalej**.
1. Wybierz **folder,** aby zainstalować środowisko uruchomieniowe integracji hostowanego samodzielnie, a następnie wybierz pozycję **Dalej**.
1. Na stronie **Gotowe do zainstalowania** wybierz pozycję **Zainstaluj**.
1. Wybierz **zakończ,** aby zakończyć instalację.
1. Pobierz klucz uwierzytelniania przy użyciu programu PowerShell. Oto przykład programu PowerShell do pobierania klucza uwierzytelniania:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. W oknie **Zarejestruj środowisko uruchomieniowe integracji (hostowane samodzielnie)** programu Microsoft Integration Runtime Configuration Manager uruchomionego na komputerze, należy wykonać następujące kroki:

    1. Wklej klucz uwierzytelniania w obszarze tekstowym.

    1. Opcjonalnie wybierz pozycję **Pokaż klucz uwierzytelniania,** aby wyświetlić tekst klucza.

    1. Wybierz pozycję **Zarejestruj**.

## <a name="high-availability-and-scalability"></a>Wysoka dostępność i skalowalność

Środowisko wykonawcze integracji hostowanych samodzielnie można skojarzyć z wieloma maszynami lokalnymi lub maszynami wirtualnymi na platformie Azure. Maszyny te są nazywane węzłami. Może mieć maksymalnie cztery węzły skojarzone z samodzielnym środowiskom wykonywania integracji. Korzyści z posiadania wielu węzłów na komputerach lokalnych, które mają zainstalowaną bramę dla bramy logicznej są następujące:

* Większa dostępność środowiska uruchomieniowego integracji hostowanego samodzielnie, dzięki czemu nie jest już pojedynczym punktem awarii rozwiązania big data lub integracji danych w chmurze z usługą Data Factory. Ta dostępność pomaga zapewnić ciągłość podczas korzystania z maksymalnie czterech węzłów.
* Zwiększona wydajność i przepływność podczas przenoszenia danych między magazynami danych lokalnych i chmurowych. Uzyskaj więcej informacji na temat [porównań wydajności](copy-activity-performance.md).

Wiele węzłów można skojarzyć, instalując oprogramowanie wykonawcze integracji hostowanego samodzielnie z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717). Następnie zarejestruj go przy użyciu jednego z kluczy uwierzytelniania, które zostały uzyskane z polecenia cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey,** zgodnie z opisem w [samouczku](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Nie trzeba tworzyć nowego środowiska uruchomieniowego integracji hostowanego samodzielnie, aby skojarzyć każdy węzeł. Środowisko uruchomieniowe integracji hostowanego samodzielnie można zainstalować na innym komputerze i zarejestrować je przy użyciu tego samego klucza uwierzytelniania.

> [!NOTE]
> Przed dodaniem innego węzła o wysokiej dostępności i skalowalności upewnij się, że opcja **Zdalny dostęp do intranetu** jest włączona w pierwszym węźle. Aby to zrobić, wybierz pozycję Microsoft **Integration Runtime Configuration Manager** > **Settings** > **Remote access to intranet**.

### <a name="scale-considerations"></a>Zagadnienia dotyczące skali

#### <a name="scale-out"></a>Skalowanie w poziomie

Gdy użycie procesora jest wysokie, a ilość dostępnej pamięci jest mało na własny hostowany podczerwień, dodaj nowy węzeł, aby ułatwić skalowanie obciążenia na różnych komputerach. Jeśli działania nie powiodą się, ponieważ przesączą limit czasu lub samodzielnie hostowany węzeł podczerwieni jest w trybie offline, pomaga to w dodaniu węzła do bramy.

#### <a name="scale-up"></a>Skalowanie w górę

Gdy procesor i dostępna pamięć RAM nie są dobrze wykorzystywane, ale wykonywanie równoczesnych zadań osiąga limity węzła, skalowanie w górę przez zwiększenie liczby równoczesnych zadań, które węzeł może działać. Można również skalować w górę, gdy działania limit czasu, ponieważ samodzielnie hostowane IR jest przeciążony. Jak pokazano na poniższej ilustracji, można zwiększyć maksymalną pojemność węzła:  

![Zwiększenie liczby równoczesnych zadań, które można uruchamiać w węźle](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Wymagania dotyczące certyfikatów TLS/SSL

Oto wymagania dotyczące certyfikatu TLS/SSL używanego do zabezpieczania komunikacji między węzłami środowiska wykonawczego integracji:

- Certyfikat musi być publicznie zaufanym certyfikatem X509 v3. Zaleca się używanie certyfikatów wystawianych przez urząd certyfikacji partnera publicznego(CA).
- Każdy węzeł środowiska wykonawczego integracji musi ufać temu certyfikatowi.
- Nie zalecamy certyfikatów Nazwa alternatywna podmiotu (SAN), ponieważ używany jest tylko ostatni element sieci SAN. Wszystkie inne elementy sieci SAN są ignorowane. Na przykład, jeśli masz certyfikat SAN, którego sieci SAN są **node1.domain.contoso.com** i **node2.domain.contoso.com,** można użyć tego certyfikatu tylko na komputerze, którego w pełni kwalifikowana nazwa domeny (FQDN) jest **node2.domain.contoso.com**.
- Certyfikat może używać dowolnego rozmiaru klucza obsługiwanego przez system Windows Server 2012 R2 dla certyfikatów TLS/SSL.
- Certyfikaty korzystające z kluczy CNG nie są obsługiwane.  

> [!NOTE]
> Ten certyfikat jest używany:
>
> - Aby zaszyfrować porty w samodzielnym węźle podczerwony.
> - W przypadku komunikacji węzeł-węzeł do synchronizacji stanu, która obejmuje synchronizację poświadczeń połączonych usług między węzłami.
> - Gdy polecenie cmdlet programu PowerShell jest używane do ustawień poświadczeń usługi połączonej z poziomu sieci lokalnej.
>
> Zalecamy użycie tego certyfikatu, jeśli środowisko sieci prywatnej nie jest bezpieczne lub jeśli chcesz zabezpieczyć komunikację między węzłami w sieci prywatnej.
>
> Przenoszenie danych podczas przesyłania z samodzielnego hostowanego podczerwona do innych magazynów danych zawsze odbywa się w zaszyfrowanym kanale, niezależnie od tego, czy ten certyfikat jest ustawiony.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Tworzenie udostępnionego środowiska wykonawczego integracji hostowanego samodzielnie w usłudze Azure Data Factory

Można ponownie użyć istniejącej infrastruktury środowiska uruchomieniowego integracji hostowanego samodzielnie, która została już skonfigurowana w fabryce danych. To ponowne użycie umożliwia utworzenie połączonego środowiska wykonawczego integracji hostowanego samodzielnie w innej fabryce danych, odwołując się do istniejącego współdzielonego środowiska IR.

Aby zobaczyć wprowadzenie i demonstrację tej funkcji, obejrzyj następujący 12-minutowy film:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **Współdzielona podczerwona:** oryginalna samoorganizowana iR, która działa na infrastrukturze fizycznej.  
- **Połączone IR:** IR, który odwołuje się do innego udostępnionego IR. Połączone podczerwone podczerwone jest logiczne IR i korzysta z infrastruktury innego współużytkowanego iR hostowane.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Metody udostępniania środowiska wykonawczego integracji hostowanego samodzielnie

Aby udostępnić środowisko uruchomieniowe integracji hostowane samodzielnie z wieloma fabrykami danych, zobacz [Tworzenie udostępnionego środowiska wykonawczego integracji hostowanego samodzielnie,](create-shared-self-hosted-integration-runtime-powershell.md) aby uzyskać szczegółowe informacje.

### <a name="monitoring"></a>Monitorowanie

#### <a name="shared-ir"></a>Współdzielona ir

![Wybór w celu znalezienia wspólnego środowiska wykonawczego integracji](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitorowanie udostępnionego środowiska wykonawczego integracji](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Połączone podczerwony podczerwony

![Wybór w celu znalezienia połączonego środowiska wykonawczego integracji](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitorowanie połączonego środowiska wykonawczego integracji](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Znane ograniczenia samodzielnego udostępniania podczerwonych

* Fabryka danych, w której jest tworzona połączone podczerwony znak WER, musi mieć [tożsamość zarządzaną](https://docs.microsoft.com/azure/active-directory/managed-service-identity/overview). Domyślnie fabryki danych utworzone w witrynie Azure portal lub polecenia cmdlet programu PowerShell mają niejawnie utworzoną tożsamość zarządzaną. Ale gdy fabryka danych jest tworzona za pomocą szablonu usługi Azure Resource Manager lub zestawu SDK, należy ustawić **właściwość tożsamości** jawnie. To ustawienie gwarantuje, że Menedżer zasobów tworzy fabrykę danych zawierającą tożsamość zarządzaną.

* Zestaw SDK .NET, który obsługuje tę funkcję, musi mieć wersję 1.1.0 lub nowszą.

* Aby udzielić uprawnień, potrzebujesz roli Właściciel lub dziedziczonej roli Właściciel w fabryce danych, w której istnieje współużytkowana usługa IR.

* Funkcja udostępniania działa tylko w fabrykach danych w obrębie tej samej dzierżawy usługi Azure AD.

* W przypadku [użytkowników-gości usługi](https://docs.microsoft.com/azure/active-directory/governance/manage-guest-access-with-access-reviews)Azure AD funkcja wyszukiwania w interfejsie użytkownika, który wyświetla listę wszystkich fabryk danych przy użyciu słowa kluczowego wyszukiwania, [nie działa.](https://msdn.microsoft.com/library/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits) Ale tak długo, jak użytkownik-gość jest właścicielem fabryki danych, można udostępnić podczerwone środowisko bez funkcji wyszukiwania. W przypadku tożsamości zarządzanej fabryki danych, która musi współużytkować podczerwórkę, wprowadź tę tożsamość zarządzana w polu **Przypisz uprawnienia** i wybierz pozycję **Dodaj** w interfejsie użytkownika fabryki danych.

  > [!NOTE]
  > Ta funkcja jest dostępna tylko w układzie danych V2.

## <a name="notification-area-icons-and-notifications"></a>Ikony i powiadomienia o obszarze powiadomień

Jeśli przeniesiesz kursor nad ikoną lub komunikatem w obszarze powiadomień, zobaczysz szczegółowe informacje o stanie środowiska wykonawczego integracji hostowanego samodzielnie.

![Powiadomienia w obszarze powiadomień](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Porty i zapory sieciowe

Istnieją dwie zapory do rozważenia:

- *Zapora firmowa,* która działa na centralnym routerze organizacji
- *Zapora systemu Windows* skonfigurowana jako demon na komputerze lokalnym, na którym jest zainstalowane środowisko wykonawcze integracji hostowanego przez siebie

![Zapory sieciowe](media/create-self-hosted-integration-runtime/firewall.png)

Na poziomie zapory firmowej należy skonfigurować następujące domeny i porty wychodzące:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]

Na poziomie zapory systemu Windows lub komputera te porty wychodzące są zwykle włączone. Jeśli tak nie jest, można skonfigurować domeny i porty na komputerze środowiska wykonawczego integracji hostowanego samodzielnie.

> [!NOTE]
> Na podstawie źródła i pochłaniacze może być konieczne zezwolenie na dodatkowe domeny i porty wychodzące w zaporze firmowej lub zaporze systemu Windows.
>
> W przypadku niektórych baz danych w chmurze, takich jak Azure SQL Database i Azure Data Lake, może być konieczne zezwolenie na adresy IP komputerów uruchamiających integracji hostowanych samodzielnie w konfiguracji zapory.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiowanie danych ze źródła do ujścia

Upewnij się, że poprawnie włączyć reguły zapory na zaporze firmowej, zaporze systemu Windows na komputerze uruchomieniowym integracji hostowanego samodzielnie i samym magazynie danych. Włączenie tych reguł umożliwia samodzielne hostowanie środowiska wykonawczego integracji pomyślnie połączyć się ze źródłem i umywalką. Włącz reguły dla każdego magazynu danych, który jest zaangażowany w operację kopiowania.

Na przykład, aby skopiować z lokalnego magazynu danych do ujścia bazy danych SQL lub ujścia usługi Azure SQL Data Warehouse, należy wykonać następujące kroki:

1. Zezwalaj na wychodzącą komunikację TCP na porcie 1433 zarówno dla zapory systemu Windows, jak i zapory firmowej.
1. Skonfiguruj ustawienia zapory bazy danych SQL, aby dodać adres IP samodzielnego środowiska wykonawczego integracji do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli zapora nie zezwala na port wychodzący 1433, środowisko wykonawcze integracji hostowanego samodzielnie nie może uzyskać bezpośredniego dostępu do bazy danych SQL. W takim przypadku można użyć [kopii etapowej](copy-activity-performance.md) do bazy danych SQL i magazynu danych SQL. W tym scenariuszu do przenoszenia danych wymagany jest tylko protokół HTTPS (port 443).

## <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy

Jeśli środowisko sieci firmowe używa serwera proxy do uzyskiwania dostępu do Internetu, skonfiguruj środowisko uruchomieniowe integracji hostowanego samodzielnie, aby używało odpowiednich ustawień serwera proxy. Serwer proxy można ustawić podczas początkowej fazy rejestracji.

![Określ serwer proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Po skonfigurowaniu środowisko wykonawcze integracji hostowanego używa serwera proxy do łączenia się ze źródłem i miejscem docelowym usługi w chmurze (które używają protokołu HTTP lub HTTPS). Dlatego podczas wstępnej konfiguracji wybierasz **opcję Zmień łącze.**

![Ustawianie serwera proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Dostępne są trzy opcje konfiguracji:

- **Nie należy używać serwera proxy:** środowisko uruchomieniowe integracji hostowanego samodzielnie nie używa jawnie żadnego serwera proxy do łączenia się z usługami w chmurze.
- **Użyj serwera proxy systemu:** Środowisko uruchomieniowe integracji hostowanego samodzielnie używa ustawienia serwera proxy skonfigurowane w pliku diahost.exe.config i diawp.exe.config. Jeśli te pliki nie określają konfiguracji serwera proxy, środowisko uruchomieniowe integracji hostowanego samodzielnie łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy.
- **Użyj niestandardowego serwera proxy:** Skonfiguruj ustawienie serwera proxy HTTP do użycia w środowisku uruchomieniowym integracji hostowanego samodzielnie, zamiast używać konfiguracji w pliku diahost.exe.config i diawp.exe.config. **Wymagane** są wartości adresu i **portu.** **Wartości nazwy użytkownika** i **hasła** są opcjonalne, w zależności od ustawienia uwierzytelniania serwera proxy. Wszystkie ustawienia są szyfrowane za pomocą interfejsu DPAPI systemu Windows w środowisku uruchomieniowym integracji hostowanej przez własny gospodarz i przechowywane lokalnie na komputerze.

Usługa hosta środowiska wykonawczego integracji uruchamia się automatycznie po zapisaniu zaktualizowanych ustawień serwera proxy.

Po zarejestrowaniu środowiska wykonawczego integracji hostowanego samodzielnie, jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, użyj programu Microsoft Integration Runtime Configuration Manager.

1. Otwórz **program Microsoft Integration Runtime Configuration Manager**.
1. Wybierz kartę **Ustawienia**.
1. W obszarze **Serwer proxy HTTP**wybierz łącze **Zmień,** aby otworzyć okno dialogowe **Ustawianie serwera proxy HTTP.**
1. Wybierz opcję **Dalej**. Następnie zostanie wyświetlone ostrzeżenie z prośbą o uprawnienia do zapisania ustawienia serwera proxy i ponownego uruchomienia usługi hosta środowiska wykonawczego integracji.

Za pomocą narzędzia menedżera konfiguracji można wyświetlać i aktualizować serwer proxy HTTP.

![Wyświetlanie i aktualizowanie serwera proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> Jeśli skonfigurujesz serwer proxy z uwierzytelnianiem NTLM, usługa hosta środowiska wykonawczego integracji jest uruchamiana na koncie domeny. Jeśli później zmienisz hasło do konta domeny, pamiętaj, aby zaktualizować ustawienia konfiguracji usługi i ponownie uruchomić usługę. Z tego powodu zalecamy dostęp do serwera proxy przy użyciu dedykowanego konta domeny, które nie wymaga częstej aktualizacji hasła.

### <a name="configure-proxy-server-settings"></a>Konfigurowanie ustawień serwera proxy

Jeśli wybierzesz opcję **Użyj serwera proxy systemu** dla serwera proxy HTTP, środowisko wykonawcze integracji hostowanego samodzielnie używa ustawień serwera proxy w diahost.exe.config i diawp.exe.config. Gdy te pliki nie określają serwera proxy, środowisko uruchomieniowe integracji hostowanego samodzielnie łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizowania pliku diahost.exe.config:

1. W Eksploratorze plików utwórz bezpieczną kopię folderu C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config jako kopię zapasową oryginalnego pliku.
1. Otwórz Notatnik jako administrator.
1. W Notatniku otwórz plik tekstowy C:\Program Files\Microsoft Integration Runtime\3.0\Shared\diahost.exe.config.
1. Znajdź domyślny znacznik **system.net,** jak pokazano w poniższym kodzie:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Następnie można dodać szczegóły serwera proxy, jak pokazano w poniższym przykładzie:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Tag serwera proxy umożliwia określenie dodatkowych właściwości, takich jak `scriptLocation`. Zobacz [ \<\> element serwera proxy (Ustawienia sieciowe)](https://msdn.microsoft.com/library/sa91de1e.aspx) dla składni.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Zapisz plik konfiguracyjny w oryginalnej lokalizacji. Następnie uruchom ponownie usługę hostowania środowiska uruchomieniowego integracji, która pobiera zmiany.

   Aby ponownie uruchomić usługę, użyj apletu usług z Panelu sterowania. Lub z programu Integration Runtime Configuration Manager wybierz przycisk **Zatrzymaj usługę,** a następnie wybierz pozycję **Uruchom usługę**.

   Jeśli usługa nie zostanie uruchomiony, prawdopodobnie dodano niepoprawną składnię znacznika XML w edytowanym pliku konfiguracji aplikacji.

> [!IMPORTANT]
> Nie zapomnij zaktualizować zarówno diahost.exe.config i diawp.exe.config.

Należy również upewnić się, że platforma Microsoft Azure znajduje się na liście dozwolonych firmy. Listę prawidłowych adresów IP platformy Azure można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Możliwe symptomy problemów związanych z zaporą i serwerem proxy

Jeśli są widoczne komunikaty o błędach, takie jak następujące, prawdopodobną przyczyną jest nieprawidłowa konfiguracja zapory lub serwera proxy. Taka konfiguracja uniemożliwia samodzielne hostowane środowisko wykonawcze integracji przed nawiązaniem połączenia z fabryką danych w celu uwierzytelnienia. Aby upewnić się, że zapora i serwer proxy są poprawnie skonfigurowane, zapoznaj się z poprzednią sekcją.

* Podczas próby zarejestrowania środowiska uruchomieniowego integracji hostowanego samodzielnie pojawia się następujący komunikat o błędzie: "Nie można zarejestrować tego węzła środowiska uruchomieniowego integracji! Upewnij się, że klucz uwierzytelniania jest prawidłowy, a usługa hosta usługi integracji jest uruchomiona na tym komputerze."
* Po otwarciu programu Integration Runtime Configuration Manager jest widoczna stan **Rozłączone** lub **Łączenie**. Podczas wyświetlania dzienników zdarzeń systemu Windows w obszarze**Dzienniki** > aplikacji i usług **Podglądu** > zdarzeń**Microsoft Integration Runtime**są wyświetlane komunikaty o błędach, takie jak ten:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Włączanie dostępu zdalnego z intranetu

Jeśli program PowerShell służy do szyfrowania poświadczeń z komputera sieciowego innego niż miejsce zainstalowania środowiska wykonawczego integracji hostowanego samodzielnie, można włączyć opcję **Dostęp zdalny z intranetu.** Jeśli program PowerShell jest uruchamiany w celu szyfrowania poświadczeń na komputerze, na którym zainstalowano środowisko uruchomieniowe integracji hostowanej samodzielnie, nie można włączyć **dostępu zdalnego z intranetu**.

Włącz **dostęp zdalny z intranetu** przed dodaniem innego węzła dla wysokiej dostępności i skalowalności.  

Po uruchomieniu instalacji wykonawczej integracji hostowanej samodzielnie w wersji 3.3 lub nowszej domyślnie instalator środowiska wykonawczego integracji hostowanej samodzielnie wyłącza **dostęp zdalny z intranetu** na komputerze uruchamianym integracji hostowanej samodzielnie.

Korzystając z zapory od partnera lub innych osób, można ręcznie otworzyć port 8060 lub port skonfigurowany przez użytkownika. Jeśli podczas konfigurowania środowiska uruchomieniowego integracji hostowanego samodzielnie występuje problem z zaporą, użyj następującego polecenia, aby zainstalować środowisko uruchomieniowe integracji hostowanego samodzielnie bez konfigurowania zapory:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Jeśli nie chcesz otwierać portu 8060 na komputerze środowiska wykonawczego integracji hostowanego samodzielnie, użyj mechanizmów innych niż aplikacja Ustawianie poświadczeń, aby skonfigurować poświadczenia magazynu danych. Na przykład można użyć polecenia cmdlet **Programu PowerShell New-AzDataFactoryV2LinkedServiceEncryptCredential** PowerShell.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: Kopiowanie danych lokalnych do chmury](tutorial-hybrid-copy-powershell.md).
