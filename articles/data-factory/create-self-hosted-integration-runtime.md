---
title: Tworzenie własnego środowiska Integration Runtime
description: Dowiedz się, jak utworzyć własne środowisko Integration Runtime w Azure Data Factory, co umożliwia fabrykom danych dostęp do magazynów danych w sieci prywatnej.
services: data-factory
documentationcenter: ''
ms.service: data-factory
ms.workload: data-services
ms.topic: conceptual
author: nabhishek
ms.author: abnarain
manager: anandsub
ms.custom: seo-lt-2019
ms.date: 11/25/2020
ms.openlocfilehash: 22155083a71a9cbf615293a4f86a179aaefce2a9
ms.sourcegitcommit: 6a770fc07237f02bea8cc463f3d8cc5c246d7c65
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/24/2020
ms.locfileid: "96023361"
---
# <a name="create-and-configure-a-self-hosted-integration-runtime"></a>Tworzenie i konfigurowanie własnego środowiska Integration Runtime

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

Środowisko Integration Runtime (IR) to infrastruktura obliczeniowa, która Azure Data Factory używa do zapewniania możliwości integracji danych w różnych środowiskach sieciowych. Aby uzyskać szczegółowe informacje o IR, zobacz [Omówienie środowiska Integration Runtime](concepts-integration-runtime.md).

Własne środowisko Integration Runtime może uruchamiać działania kopiowania między magazynem danych w chmurze i magazynem danych w sieci prywatnej. Może również wysyłać działania transformacji dotyczące zasobów obliczeniowych w sieci lokalnej lub sieci wirtualnej platformy Azure. Instalacja własnego środowiska Integration Runtime wymaga maszyny lokalnej lub maszyny wirtualnej znajdującej się w sieci prywatnej.  

W tym artykule opisano, jak można utworzyć i skonfigurować samoobsługowe środowisko IR.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="setting-up-a-self-hosted-integration-runtime"></a>Konfigurowanie własnego środowiska Integration Runtime

Aby utworzyć i skonfigurować własne środowisko Integration Runtime, należy wykonać poniższe procedury.

### <a name="create-a-self-hosted-ir-via-azure-powershell"></a>Tworzenie własnego środowiska IR za pośrednictwem Azure PowerShell

1. Dla tego zadania można użyć Azure PowerShell. Oto przykład:

    ```powershell
    Set-AzDataFactoryV2IntegrationRuntime -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName -Type SelfHosted -Description "selfhosted IR description"
    ```
  
2. [Pobierz](https://www.microsoft.com/download/details.aspx?id=39717) i zainstaluj własne środowisko Integration Runtime na komputerze lokalnym.

3. Pobierz klucz uwierzytelniania i zarejestruj własne środowisko Integration Runtime przy użyciu klucza. Oto przykład środowiska PowerShell:

    ```powershell

    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntimeName  

    ```

### <a name="create-a-self-hosted-ir-via-azure-data-factory-ui"></a>Tworzenie własnego środowiska IR za pośrednictwem interfejsu użytkownika Azure Data Factory

Wykonaj następujące kroki, aby utworzyć środowisko IR samodzielnego przy użyciu Azure Data Factory interfejsu użytkownika.

1. Na stronie Wprowadzenie Azure Data Factory interfejsu użytkownika wybierz [kartę Zarządzanie](./author-management-hub.md) w okienku po **lewej stronie.**

   ![Przycisk zarządzania stroną główną](media/doc-common-process/get-started-page-manage-button.png)

1. W lewym okienku wybierz pozycję **Integration Runtimes** , a następnie wybierz pozycję **+ Nowy**.

   ![Tworzenie środowiska Integration Runtime](media/doc-common-process/manage-new-integration-runtime.png)

1. Na stronie **Konfiguracja środowiska Integration Runtime** wybierz pozycję **Azure, pozycję samodzielny**, a następnie wybierz pozycję **Kontynuuj**. 

1. Na poniższej stronie wybierz pozycję **samodzielna** , aby utworzyć Self-Hosted IR, a następnie wybierz pozycję **Kontynuuj**.
   ![Tworzenie selfhosted IR](media/create-self-hosted-integration-runtime/new-selfhosted-integration-runtime.png)

1. Wprowadź nazwę dla swojego środowiska IR, a następnie wybierz pozycję **Utwórz**.

1. Na stronie **Konfiguracja środowiska Integration Runtime** wybierz link w obszarze **Opcja 1** , aby otworzyć program Express Setup na komputerze. Lub wykonaj kroki opisane w sekcji **Opcja 2** , aby skonfigurować ręcznie. Poniższe instrukcje są zależne od konfiguracji ręcznej:

   ![Instalacja środowiska Integration Runtime](media/create-self-hosted-integration-runtime/integration-runtime-setting-up.png)

    1. Skopiuj i Wklej klucz uwierzytelniania. Wybierz pozycję **Pobierz i zainstaluj środowisko Integration Runtime**.

    1. Pobierz środowisko Integration Runtime (Self-hosted) na lokalną maszynę z systemem Windows. Uruchom instalatora.

    1. Na stronie **zarejestruj Integration Runtime (Self-Hosted)** wklej wcześniej zapisany klucz, a następnie wybierz pozycję **zarejestruj**.
    
       ![Rejestrowanie środowiska Integration Runtime](media/create-self-hosted-integration-runtime/register-integration-runtime.png)

    1. Na stronie **nowy węzeł Integration Runtime (Self-Hosted)** wybierz pozycję **Zakończ**.

1. Po pomyślnym zarejestrowaniu własnego środowiska Integration Runtime zostanie wyświetlone następujące okno:

    ![Rejestracja zakończona powodzeniem](media/create-self-hosted-integration-runtime/registered-successfully.png)

### <a name="set-up-a-self-hosted-ir-on-an-azure-vm-via-an-azure-resource-manager-template"></a>Konfigurowanie własnego środowiska IR na maszynie wirtualnej platformy Azure za pośrednictwem szablonu Azure Resource Manager

Możesz zautomatyzować konfigurację samodzielnego środowiska IR na maszynie wirtualnej platformy Azure przy użyciu [szablonu Utwórz samoobsługowy proces IR](https://github.com/Azure/azure-quickstart-templates/tree/master/101-vms-with-selfhost-integration-runtime). Szablon umożliwia łatwą w pełni funkcjonalną obsługę środowiska IR w sieci wirtualnej platformy Azure. Środowisko IR ma funkcje wysokiej dostępności i skalowalności, o ile liczba węzłów jest ustawiona na wartość 2 lub wyższą.

### <a name="set-up-an-existing-self-hosted-ir-via-local-powershell"></a>Konfigurowanie istniejącego własnego środowiska IR za pośrednictwem lokalnego programu PowerShell

Za pomocą wiersza polecenia można skonfigurować lub zarządzać istniejącym własnym środowiskoem IR. To użycie może pomóc w zaautomatyzowaniu instalacji i rejestracji samodzielnych węzłów IR.

Dmgcmd.exe jest dołączony do samodzielnego instalatora. Zazwyczaj znajduje się on w folderze C:\Program Files\Microsoft Integration Runtime\4.0\Shared\. Ta aplikacja obsługuje różne parametry i może być wywoływana za pomocą wiersza polecenia przy użyciu skryptów wsadowych do automatyzacji.

Użyj aplikacji w następujący sposób:

```powershell
dmgcmd [ -RegisterNewNode "<AuthenticationKey>" -EnableRemoteAccess "<port>" ["<thumbprint>"] -EnableRemoteAccessInContainer "<port>" ["<thumbprint>"] -DisableRemoteAccess -Key "<AuthenticationKey>" -GenerateBackupFile "<filePath>" "<password>" -ImportBackupFile "<filePath>" "<password>" -Restart -Start -Stop -StartUpgradeService -StopUpgradeService -TurnOnAutoUpdate -TurnOffAutoUpdate -SwitchServiceAccount "<domain\user>" ["<password>"] -Loglevel <logLevel> ]
```

Poniżej znajdują się szczegółowe informacje o parametrach i właściwościach aplikacji: 

| Właściwość                                                    | Opis                                                  | Wymagane |
| ----------------------------------------------------------- | ------------------------------------------------------------ | -------- |
| **RegisterNewNode** "`<AuthenticationKey>`"                     | Zarejestruj własny węzeł Integration Runtime z określonym kluczem uwierzytelniania. | Nie       |
| **RegisterNewNode** "`<AuthenticationKey>`" "`<NodeName>`"      | Zarejestruj własny węzeł Integration Runtime z określonym kluczem uwierzytelniania i nazwą węzła. | Nie       |
| **EnableRemoteAccess** "`<port>`" ["`<thumbprint>`"]            | Włącz dostęp zdalny w bieżącym węźle, aby skonfigurować klaster o wysokiej dostępności. Lub Włącz ustawienia poświadczeń bezpośrednio dla samoobsługowego środowiska IR bez przechodzenia przez Azure Data Factory. Można to zrobić za pomocą polecenia cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** z komputera zdalnego w tej samej sieci. | Nie       |
| **EnableRemoteAccessInContainer** "`<port>`" ["`<thumbprint>`"] | Włącz dostęp zdalny do bieżącego węzła, gdy węzeł jest uruchomiony w kontenerze. | Nie       |
| **DisableRemoteAccess**                                         | Wyłącz dostęp zdalny do bieżącego węzła. Dostęp zdalny jest wymagany w przypadku konfiguracji wielowęzłowej. Polecenie cmdlet **New-AzDataFactoryV2LinkedServiceEncryptedCredential** programu PowerShell nadal działa nawet wtedy, gdy dostęp zdalny jest wyłączony. To zachowanie jest prawdziwe, o ile polecenie cmdlet jest wykonywane na tym samym komputerze co węzeł samodzielnego środowiska IR. | Nie       |
| **Klucz** "`<AuthenticationKey>`"                                 | Zastąp lub zaktualizuj poprzedni klucz uwierzytelniania. Należy zachować ostrożność w przypadku tej akcji. Poprzedni udostępniony przez siebie węzeł IR może przejść do trybu offline, jeśli klucz jest nowym środowiskiem Integration Runtime. | Nie       |
| **GenerateBackupFile** "`<filePath>`" "`<password>`"            | Generuj plik kopii zapasowej dla bieżącego węzła. Plik kopii zapasowej zawiera klucze węzła i poświadczenia magazynu danych. | Nie       |
| **ImportBackupFile** "`<filePath>`" "`<password>`"              | Przywróć węzeł z pliku kopii zapasowej.                          | Nie       |
| **Uruchom ponownie**                                                     | Uruchom ponownie funkcję samodzielnego hosta Integration Runtime.   | Nie       |
| **Początek**                                                       | Uruchom samohostowaną usługę hosta Integration Runtime.     | Nie       |
| **Zatrzymaj**                                                        | Zatrzymaj samohostowaną usługę hosta Integration Runtime.        | Nie       |
| **StartUpgradeService**                                         | Uruchom samohostowaną usługę uaktualniania środowiska Integration Runtime.       | Nie       |
| **StopUpgradeService**                                          | Zatrzymaj usługę uaktualniania środowiska Integration Runtime (Auto-Hosted).        | Nie       |
| **TurnOnAutoUpdate**                                            | Włącz samoobsługową automatyczną aktualizację środowiska Integration Runtime.        | Nie       |
| **TurnOffAutoUpdate**                                           | Wyłącz samoobsługową automatyczną aktualizację środowiska Integration Runtime.       | Nie       |
| **SwitchServiceAccount** "`<domain\user>`" ["`<password>`"]           | Ustaw DIAHostService do uruchamiania jako nowe konto. Użyj pustego hasła "" dla kont systemowych i kont wirtualnych. | Nie       |


## <a name="command-flow-and-data-flow"></a>Przepływ poleceń i przepływ danych

Gdy przenosisz dane między środowiskiem lokalnym i chmurą, działanie korzysta z własnego środowiska Integration Runtime do przenoszenia danych między lokalnym źródłem danych i chmurą.

Poniżej znajduje się podsumowanie etapów przepływu danych do kopiowania przy użyciu samodzielnego środowiska IR:

![Ogólne omówienie przepływu danych](media/create-self-hosted-integration-runtime/high-level-overview.png)

1. Deweloper danych tworzy własne środowisko Integration Runtime w ramach fabryki danych platformy Azure przy użyciu polecenia cmdlet programu PowerShell. Obecnie Azure Portal nie obsługuje tej funkcji.
1. Deweloper danych tworzy połączoną usługę dla lokalnego magazynu danych. Deweloper robi to przez określenie wystąpienia środowiska Integration Runtime, które ma być używane przez usługę do łączenia z magazynami danych.
1. Własny węzeł środowiska Integration Runtime szyfruje poświadczenia za pomocą interfejsu programowania aplikacji ochrony danych systemu Windows (DPAPI) i zapisuje poświadczenia lokalnie. Jeśli skonfigurowano wiele węzłów w celu zapewnienia wysokiej dostępności, poświadczenia są dodatkowo synchronizowane między innymi węzłami. Każdy węzeł szyfruje poświadczenia przy użyciu funkcji DPAPI i przechowuje je lokalnie. Synchronizacja poświadczeń jest niewidoczna dla deweloperów danych i jest obsługiwana przez samoobsługowe środowisko IR.
1. Azure Data Factory komunikuje się z własnym hostowanym środowiskiem Integration Runtime w celu planowania zadań i zarządzania nimi. Komunikacja odbywa się za pośrednictwem kanału kontrolnego korzystającego z udostępnionego połączenia usługi [Azure Service Bus Relay](../azure-relay/relay-what-is-it.md#wcf-relay) . Gdy zadanie działania wymaga uruchomienia, Data Factory kolejkuje żądanie wraz z informacjami o poświadczeniach. Jest to konieczne w przypadku, gdy poświadczenia nie są już przechowywane w środowisku Integration Runtime. Własne środowisko Integration Runtime uruchamia zadanie po sondowaniu kolejki.
1. Własne środowisko Integration Runtime kopiuje dane między magazynem lokalnym i magazynem w chmurze. Kierunek kopiowania zależy od konfiguracji działania kopiowania w potoku danych. W tym kroku własne środowisko Integration Runtime bezpośrednio komunikuje się z usługami magazynu opartymi na chmurze, takimi jak Azure Blob Storage, za pośrednictwem bezpiecznego kanału HTTPS.

## <a name="considerations-for-using-a-self-hosted-ir"></a>Zagadnienia dotyczące korzystania z samodzielnego środowiska IR

- Możesz użyć pojedynczego środowiska Integration Runtime dla wielu lokalnych źródeł danych. Można go również udostępnić innym fabrykom danych w ramach tej samej dzierżawy usługi Azure Active Directory (Azure AD). Aby uzyskać więcej informacji, zobacz [udostępnianie własnego środowiska Integration Runtime](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory).
- Na każdym komputerze można zainstalować tylko jedno wystąpienie środowiska Integration Runtime (Single-Hosted). Jeśli istnieją dwa fabryki danych, które muszą uzyskać dostęp do lokalnych źródeł danych, użyj [funkcji samodzielnego udostępniania](#create-a-shared-self-hosted-integration-runtime-in-azure-data-factory) środowiska IR do udostępniania samodzielnego środowiska IR lub zainstaluj samodzielnie hostowane środowisko IR na dwóch komputerach lokalnych, po jednym dla każdej fabryki danych.  
- Własne środowisko Integration Runtime nie musi znajdować się na tym samym komputerze co źródło danych. Jednak środowisko Integration Runtime w pobliżu źródła danych skraca czas, w którym środowisko Integration Runtime jest połączone ze źródłem danych. Zalecamy zainstalowanie własnego środowiska Integration Runtime na komputerze, który jest inny niż ten, który jest hostem lokalnego źródła danych. Gdy własne środowisko Integration Runtime i źródło danych znajdują się na różnych komputerach, własne środowisko Integration Runtime nie konkuruje ze źródłem danych dla zasobów.
- Na różnych komputerach, które łączą się z tym samym lokalnym źródłem danych, można mieć wiele środowisk Integration Runtime. Na przykład jeśli masz dwa własne środowiska Integration Runtime, które obsługują dwa fabryki danych, to samo lokalne źródło danych może być zarejestrowane w obu fabrykach danych.
- Użyj własnego środowiska Integration Runtime do obsługi integracji danych w ramach sieci wirtualnej platformy Azure.
- Traktuj źródło danych jako lokalne źródło danych znajdujące się za zaporą, nawet jeśli korzystasz z usługi Azure ExpressRoute. Użyj własnego środowiska Integration Runtime, aby połączyć usługę ze źródłem danych.
- Użyj własnego środowiska Integration Runtime, nawet jeśli magazyn danych znajduje się w chmurze na maszynie wirtualnej infrastruktury platformy Azure jako usługi (IaaS).
- Zadania mogą zakończyć się niepowodzeniem w ramach własnego środowiska Integration Runtime zainstalowanego w systemie Windows Server, dla którego włączono szyfrowanie zgodne ze standardem FIPS. Aby obejść ten problem, masz dwie opcje: Przechowuj poświadczenia/wartości tajne w Azure Key Vault lub wyłącz szyfrowanie zgodne ze standardem FIPS na serwerze. Aby wyłączyć szyfrowanie zgodne ze standardem FIPS, należy zmienić wartość następującego podklucza rejestru z 1 (włączone) na 0 (wyłączone): `HKLM\System\CurrentControlSet\Control\Lsa\FIPSAlgorithmPolicy\Enabled` . Jeśli używasz [własnego środowiska Integration Runtime jako serwera proxy dla programu SSIS Integration Runtime](https://docs.microsoft.com/azure/data-factory/self-hosted-integration-runtime-proxy-ssis), szyfrowanie zgodne ze standardem FIPS może być włączone i będzie używane podczas przenoszenia danych z lokalizacji lokalnej na platformę Azure Blob Storage jako obszaru przejściowego.

## <a name="prerequisites"></a>Wymagania wstępne

- Obsługiwane wersje systemu Windows to:
  + Windows 7 z dodatkiem Service Pack 1
  + Windows 8.1
  + Windows 10
  + Windows Server 2008 R2 SP1
  + Windows Server 2012
  + Windows Server 2012 z dodatkiem R2
  + Windows Server 2016
  + Windows Server 2019
   
   Instalacja własnego środowiska Integration Runtime na kontrolerze domeny nie jest obsługiwana.
- Wymagany jest .NET Framework 4.6.1 lub nowszy. Jeśli instalujesz własne środowisko Integration Runtime na komputerze z systemem Windows 7, Zainstaluj .NET Framework 4.6.1 lub nowszy. Aby uzyskać szczegółowe informacje, zobacz [wymagania systemowe .NET Framework](/dotnet/framework/get-started/system-requirements) .
- Zalecana minimalna konfiguracja dla maszyn wirtualnych środowiska Integration Runtime to procesor 2 GHz z 4 rdzeniami, 8 GB pamięci RAM i 80 GB dostępnego miejsca na dysku twardym.
- Jeśli maszyna hosta jest w stanie hibernacji, własne środowisko Integration Runtime nie odpowiada na żądania danych. Przed zainstalowaniem własnego środowiska Integration Runtime należy skonfigurować odpowiedni plan na komputerze. Jeśli komputer jest skonfigurowany do hibernacji, samodzielny Instalator środowiska Integration Runtime monituje o komunikat.
- Aby pomyślnie zainstalować i skonfigurować środowisko Integration Runtime, musisz mieć uprawnienia administratora na komputerze.
- Przebiegi kopiowania działania są wykonywane z określoną częstotliwością. Użycie procesora i pamięci RAM na komputerze jest zgodne z tym samym wzorcem, w którym są czasy szczytowe i bezczynne. Użycie zasobów również zależy znacznie od ilości przeniesionych danych. Gdy trwa wykonywanie wielu zadań kopiowania, zostanie wyświetlone użycie zasobów w godzinach szczytu.
- Zadania mogą kończyć się niepowodzeniem podczas wyodrębniania danych w formatach Parquet, ORC i Avro. Aby uzyskać więcej informacji na temat Parquet, zobacz [Parquet format w Azure Data Factory](./format-parquet.md#using-self-hosted-integration-runtime). Tworzenie plików jest uruchamiane na samoobsługowej maszynie integracji. Aby program działał zgodnie z oczekiwaniami, tworzenie plików wymaga następujących wymagań wstępnych:
    - [Pakiet redystrybucyjny Visual C++ 2010](https://download.microsoft.com/download/3/2/2/3224B87F-CFA0-4E70-BDA3-3DE650EFEBA5/vcredist_x64.exe) Pakiet (x64)
    - Środowisko uruchomieniowe języka Java (JRE) w wersji 8 od dostawcy środowiska JRE, takiego jak [przyjęcie OpenJDK](https://adoptopenjdk.net/). Upewnij się, że `JAVA_HOME` zmienna środowiskowa jest ustawiona.

## <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji

Możesz zainstalować własne środowisko Integration Runtime, pobierając pakiet instalacji zarządzanej tożsamości z [Centrum pobierania firmy Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Aby uzyskać instrukcje krok po kroku, zobacz artykuł [przenoszenie danych między środowiskiem lokalnym i chmurą](tutorial-hybrid-copy-powershell.md) .

- Skonfiguruj plan dodatku na komputerze hosta dla własnego środowiska Integration Runtime, aby komputer nie był w stanie hibernacji. Jeśli maszyna hosta przechodzi w stan hibernacji, środowisko Integration Runtime jest w trybie offline.
- Regularne tworzenie kopii zapasowej poświadczeń skojarzonych z własnym środowiskiem Integration Runtime.
- Aby zautomatyzować operacje konfiguracji samoobsługowego środowiska IR, zapoznaj się z tematem [Konfigurowanie istniejącego samoobsługowego IR za pośrednictwem programu PowerShell](#setting-up-a-self-hosted-integration-runtime).  

## <a name="install-and-register-a-self-hosted-ir-from-microsoft-download-center"></a>Instalowanie i rejestrowanie samodzielnego środowiska IR z centrum pobierania firmy Microsoft

1. Przejdź do [strony pobierania środowiska Microsoft Integration Runtime](https://www.microsoft.com/download/details.aspx?id=39717).
1. Wybierz pozycję **Pobierz**, wybierz wersję 64-bitową i wybierz pozycję **dalej**. Wersja 32-bitowa nie jest obsługiwana.
1. Uruchom bezpośrednio plik tożsamości zarządzanej lub Zapisz go na dysku twardym i uruchom go.
1. W oknie **Zapraszamy** wybierz język i wybierz pozycję **dalej**.
1. Zaakceptuj postanowienia licencyjne dotyczące oprogramowania firmy Microsoft i wybierz pozycję **dalej**.
1. Wybierz **folder** , aby zainstalować środowisko Integration Runtime (własne), a następnie wybierz przycisk **dalej**.
1. Na stronie **gotowy do instalacji** wybierz pozycję **Zainstaluj**.
1. Wybierz pozycję **Zakończ** , aby zakończyć instalację.
1. Pobierz klucz uwierzytelniania przy użyciu programu PowerShell. Oto przykład programu PowerShell służący do pobierania klucza uwierzytelniania:

    ```powershell
    Get-AzDataFactoryV2IntegrationRuntimeKey -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Name $selfHostedIntegrationRuntime
    ```

1. W oknie **Integration Runtime (Self-Hosted) rejestru** Microsoft Integration Runtime Configuration Manager uruchomionego na maszynie wykonaj następujące czynności:

    1. Wklej klucz uwierzytelniania w obszarze tekstowym.

    1. Opcjonalnie wybierz pozycję **Pokaż klucz uwierzytelniania** , aby wyświetlić tekst klucza.

    1. Wybierz pozycję **Zarejestruj**.

## <a name="high-availability-and-scalability"></a>Wysoka dostępność i skalowalność

Możesz skojarzyć własne środowisko Integration Runtime z wieloma maszynami lokalnymi lub maszynami wirtualnymi na platformie Azure. Te komputery są nazywane węzłami. Możesz mieć do czterech węzłów skojarzonych z własnym środowiskiem Integration Runtime. Zalety posiadania wielu węzłów na maszynach lokalnych, na których zainstalowano bramę dla bramy logicznej, to:

* Wyższa dostępność środowiska Integration Runtime (samodzielne środowisko uruchomieniowe), dzięki czemu nie jest już single point of failure w rozwiązaniu danych Big Data ani integracji danych w chmurze z Data Factory. Ta dostępność pomaga zapewnić ciągłość w przypadku korzystania z maksymalnie czterech węzłów.
* Zwiększona wydajność i przepływność podczas przenoszenia danych między lokalnymi i magazynami danych w chmurze. Uzyskaj więcej informacji na temat [porównań wydajności](copy-activity-performance.md).

Możesz skojarzyć wiele węzłów, instalując własne oprogramowanie Integration Runtime z [Centrum pobierania](https://www.microsoft.com/download/details.aspx?id=39717). Następnie zarejestruj go przy użyciu dowolnego klucza uwierzytelniania uzyskanego z polecenia cmdlet **New-AzDataFactoryV2IntegrationRuntimeKey** , zgodnie z opisem w [samouczku](tutorial-hybrid-copy-powershell.md).

> [!NOTE]
> Nie musisz tworzyć nowego środowiska Integration Runtime, aby skojarzyć każdy węzeł. Możesz zainstalować własne środowisko Integration Runtime na innym komputerze i zarejestrować je przy użyciu tego samego klucza uwierzytelniania.

> [!NOTE]
> Przed dodaniem kolejnego węzła w celu zapewnienia wysokiej dostępności i skalowalności upewnij się, że opcja **dostęp zdalny do sieci intranet** jest włączona w pierwszym węźle. W tym celu wybierz pozycję **Microsoft Integration Runtime Configuration Manager**  >  **Ustawienia**  >  **dostęp zdalny do intranetu**.

### <a name="scale-considerations"></a>Zagadnienia dotyczące skalowania

#### <a name="scale-out"></a>Skalowanie w poziomie

Gdy użycie procesora jest wysokie, a ilość dostępnej pamięci jest niska w przypadku samodzielnego środowiska IR, Dodaj nowy węzeł, aby ułatwić skalowanie obciążenia między maszynami. Jeśli działania zakończą się niepowodzeniem z powodu przekroczenia limitu czasu lub samodzielnego węzła IR jest w trybie offline, może to spowodować dodanie węzła do bramy.

#### <a name="scale-up"></a>Skalowanie w górę

Gdy procesor i dostępna pamięć RAM nie są dobrze wykorzystane, ale wykonywanie współbieżnych zadań osiąga limity węzła, Skaluj w górę, zwiększając liczbę współbieżnych zadań, które można uruchomić w węźle. Możesz również skalować w górę, gdy działania przekroczą limit czasu, ponieważ środowisko IR obsługiwane przez siebie jest przeciążone. Jak pokazano na poniższej ilustracji, można zwiększyć maksymalną pojemność węzła:  

![Zwiększ liczbę współbieżnych zadań, które mogą być uruchamiane w węźle](media/create-self-hosted-integration-runtime/scale-up-self-hosted-IR.png)

### <a name="tlsssl-certificate-requirements"></a>Wymagania dotyczące certyfikatu TLS/SSL

Poniżej przedstawiono wymagania dotyczące certyfikatu TLS/SSL, który służy do zabezpieczania komunikacji między węzłami Integration Runtime:

- Certyfikat musi być publicznie zaufanym certyfikatem x509 v3. Zalecamy używanie certyfikatów wystawionych przez publiczny urząd certyfikacji partnera (CA).
- Każdy węzeł Integration Runtime musi ufać temu certyfikatowi.
- Nie zalecamy certyfikatów alternatywnej nazwy podmiotu (SAN), ponieważ jest używany tylko ostatni element sieci SAN. Wszystkie inne elementy sieci SAN są ignorowane. Jeśli na przykład masz certyfikat sieci SAN, którego sieci San są **Node1.domain.contoso.com** i **Node2.domain.contoso.com**, możesz użyć tego certyfikatu tylko na komputerze, którego w pełni KWALIFIKOWANA nazwa domeny (FQDN) to **Node2.domain.contoso.com**.
- Certyfikat może używać dowolnego rozmiaru klucza obsługiwanego przez system Windows Server 2012 R2 dla certyfikatów TLS/SSL.
- Certyfikaty używające kluczy CNG nie są obsługiwane.  

> [!NOTE]
> Ten certyfikat jest używany:
>
> - Do szyfrowania portów w węźle samoobsługowego środowiska IR.
> - W przypadku komunikacji między węzłami w przypadku synchronizacji stanu, która obejmuje synchronizację poświadczeń połączonych usług między węzłami.
> - Gdy w sieci lokalnej jest używane polecenie cmdlet programu PowerShell dla ustawień poświadczeń połączenia z usługą.
>
> Sugerujemy użycie tego certyfikatu, jeśli środowisko sieci prywatnej nie jest zabezpieczone lub jeśli chcesz zabezpieczyć komunikację między węzłami w sieci prywatnej.
>
> Przenoszenie danych z własnego środowiska IR do innych magazynów danych zawsze odbywa się w ramach zaszyfrowanego kanału, niezależnie od tego, czy ten certyfikat jest ustawiony.

## <a name="create-a-shared-self-hosted-integration-runtime-in-azure-data-factory"></a>Utwórz udostępnione środowisko Integration Runtime w Azure Data Factory

Można ponownie użyć istniejącej infrastruktury środowiska Integration Runtime, która została już skonfigurowana w fabryce danych. To ponowne użycie pozwala utworzyć połączone środowisko Integration Runtime w innej fabryce danych, odwołując się do istniejącego udostępnionego własnego środowiska IR.

Aby zapoznać się z wprowadzeniem i pokazem tej funkcji, Obejrzyj następujący 12-minutowy film wideo:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Hybrid-data-movement-across-multiple-Azure-Data-Factories/player]

### <a name="terminology"></a>Terminologia

- **Udostępnione środowisko IR**: oryginalne środowisko IR, które działa w ramach infrastruktury fizycznej.  
- **Połączone środowisko IR**: IR, który odwołuje się do innego udostępnionego środowiska IR. Połączone środowisko IR jest logicznym portem IR i używa infrastruktury innego udostępnionego samodzielnego środowiska IR.

### <a name="methods-to-share-a-self-hosted-integration-runtime"></a>Metody udostępniania środowiska Integration Runtime (własne)

Aby udostępnić środowisko Integration Runtime z wieloma fabrykami danych, zobacz artykuł [Tworzenie udostępnionego środowiska Integration Runtime](create-shared-self-hosted-integration-runtime-powershell.md) w celu uzyskania szczegółowych informacji.

### <a name="monitoring"></a>Monitorowanie

#### <a name="shared-ir"></a>Udostępniony IR

![Opcje znajdowania udostępnionego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-shared-IR.png)

![Monitorowanie udostępnionego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/contoso-shared-ir-monitoring.png)

#### <a name="linked-ir"></a>Połączony IR

![Opcje znajdowania połączonego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir.png)

![Monitorowanie połączonego środowiska Integration Runtime](media/create-self-hosted-integration-runtime/Contoso-linked-ir-monitoring.png)

### <a name="known-limitations-of-self-hosted-ir-sharing"></a>Znane ograniczenia dotyczące samodzielnego udostępniania środowiska IR

* Fabryka danych, w której jest tworzone połączone środowisko IR, musi mieć [zarządzaną tożsamość](../active-directory/managed-identities-azure-resources/overview.md). Domyślnie fabryki danych utworzone w Azure Portal lub polecenia cmdlet programu PowerShell mają niejawnie utworzoną tożsamość zarządzaną. Jednak gdy Fabryka danych jest tworzona za pomocą szablonu Azure Resource Manager lub zestawu SDK, należy jawnie ustawić właściwość **Identity** . To ustawienie zapewnia, że Menedżer zasobów tworzy fabrykę danych, która zawiera zarządzaną tożsamość.

* Zestaw Data Factory .NET SDK, który obsługuje tę funkcję, musi być w wersji 1.1.0 lub nowszej.

* Aby udzielić uprawnień, potrzebna jest rola właściciela lub dziedziczona rola właściciela w fabryce danych, w której istnieje udostępniony środowisko IR.

* Funkcja udostępniania działa tylko dla fabryk danych w ramach tej samej dzierżawy usługi Azure AD.

* W przypadku [użytkowników Gości](../active-directory/governance/manage-guest-access-with-access-reviews.md)usługi Azure AD funkcja wyszukiwania w interfejsie użytkownika, która wyświetla listę wszystkich fabryk danych za pomocą słowa kluczowego wyszukiwania, [nie działa](/previous-versions/azure/ad/graph/howto/azure-ad-graph-api-permission-scopes#SearchLimits). Jednak o ile użytkownik-Gość jest właścicielem fabryki danych, możesz udostępnić środowisko IR bez funkcji wyszukiwania. W przypadku zarządzanej tożsamości fabryki danych, która musi udostępniać środowisko IR, wprowadź tę tożsamość zarządzaną w polu **Przypisz uprawnienia** i wybierz pozycję **Dodaj** w interfejsie użytkownika Data Factory.

  > [!NOTE]
  > Ta funkcja jest dostępna tylko w Data Factory v2.

## <a name="notification-area-icons-and-notifications"></a>Ikony i powiadomienia obszaru powiadomień

Jeśli umieścisz kursor nad ikoną lub komunikatem w obszarze powiadomień, zobaczysz szczegóły dotyczące stanu własnego środowiska Integration Runtime.

![Powiadomienia w obszarze powiadomień](media/create-self-hosted-integration-runtime/system-tray-notifications.png)

## <a name="ports-and-firewalls"></a>Porty i zapory

Istnieją dwie zapory, które należy wziąć pod uwagę:

- *Firmowa Zapora* działająca na centralnym routerze organizacji
- *Zapora systemu Windows* skonfigurowana jako demon na komputerze lokalnym, na którym jest zainstalowany własny środowisko Integration Runtime

![Zapory](media/create-self-hosted-integration-runtime/firewall.png)

Na poziomie zapory firmowej należy skonfigurować następujące domeny i porty wychodzące:

[!INCLUDE [domain-and-outbound-port-requirements](../../includes/domain-and-outbound-port-requirements.md)]


Na poziomie zapory systemu Windows lub na poziomie komputera te porty wychodzące są zwykle włączone. Jeśli nie, możesz skonfigurować domeny i porty na własnym komputerze Integration Runtime.

> [!NOTE]
> W oparciu o źródło i ujścia, może być konieczne zezwolenie na dodatkowe domeny i porty wychodzące w zaporze firmowej lub zaporze systemu Windows.
>
> W przypadku niektórych baz danych w chmurze, takich jak Azure SQL Database i Azure Data Lake, może być konieczne zezwolenie na adresy IP maszyn wirtualnych środowiska Integration Runtime w konfiguracji zapory.

### <a name="copy-data-from-a-source-to-a-sink"></a>Kopiowanie danych ze źródła do ujścia

Upewnij się, że reguły zapory zostały prawidłowo włączone w zaporze firmowej, w zaporze systemu Windows środowiska Integration Runtime (własna) i w samym magazynie danych. Włączenie tych reguł umożliwia pomyślne połączenie środowiska Integration Runtime ze źródłem i ujściam. Włącz reguły dla wszystkich magazynów danych, które są związane z operacją kopiowania.

Na przykład, aby skopiować z lokalnego magazynu danych do ujścia SQL Database lub ujścia usługi Azure Synapse Analytics (dawniej SQL Data Warehouse), wykonaj następujące czynności:

1. Zezwalaj na wychodzącą komunikację TCP na porcie 1433 zarówno dla zapory systemu Windows, jak i zapory firmowej.
1. Skonfiguruj ustawienia zapory SQL Database, aby dodać adres IP maszyny środowiska Integration Runtime (samodzielne) do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli Zapora nie zezwala na port wychodzący 1433, własne środowisko Integration Runtime nie może bezpośrednio uzyskać dostępu do bazy danych SQL. W takim przypadku można użyć [kopii przygotowanej](copy-activity-performance.md) do SQL Database i usługi Azure Synapse Analytics. W tym scenariuszu do przenoszenia danych wymagane jest tylko HTTPS (port 443).

## <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy

Jeśli firmowe środowisko sieciowe używa serwera proxy w celu uzyskania dostępu do Internetu, należy skonfigurować własne środowisko Integration Runtime, aby korzystało z odpowiednich ustawień serwera proxy. Serwer proxy można ustawić podczas początkowej fazy rejestracji.

![Określ serwer proxy](media/create-self-hosted-integration-runtime/specify-proxy.png)

Po skonfigurowaniu środowisko Integration runtime używa serwera proxy do łączenia się ze źródłem i miejscem docelowym usługi w chmurze (korzystając z protokołu HTTP lub HTTPS). To dlatego, że podczas początkowej konfiguracji wybierasz pozycję **Zmień link** .

![Ustawianie serwera proxy](media/create-self-hosted-integration-runtime/set-http-proxy.png)

Dostępne są trzy opcje konfiguracji:

- **Nie używaj serwera proxy**: własne środowisko Integration Runtime nie używa jawnie żadnego serwera proxy do nawiązywania połączenia z usługami w chmurze.
- **Użyj systemowego serwera proxy**: własne środowisko Integration runtime używa ustawień serwera proxy skonfigurowanych w diahost.exe.config i diawp.exe.config. Jeśli te pliki nie określają konfiguracji serwera proxy, własne środowisko Integration Runtime łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy.
- **Użyj niestandardowego serwera proxy**: Skonfiguruj ustawienia serwera proxy HTTP, które ma być używane dla własnego środowiska Integration Runtime, zamiast korzystać z konfiguracji w diahost.exe.config i diawp.exe.config. Wartości **Address** i **port** są wymagane. Wartości **Nazwa użytkownika** i **hasło** są opcjonalne, w zależności od ustawienia uwierzytelniania serwera proxy. Wszystkie ustawienia są szyfrowane za pomocą funkcji DPAPI systemu Windows w ramach własnego środowiska Integration Runtime i przechowywane lokalnie na komputerze.

Usługa hosta Integration Runtime jest uruchamiana automatycznie po zapisaniu zaktualizowanych ustawień serwera proxy.

Po zarejestrowaniu własnego środowiska Integration Runtime, jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, użyj Microsoft Integration Runtime Configuration Manager.

1. Otwórz **Configuration Manager Microsoft Integration Runtime**.
1. Wybierz kartę **Ustawienia**.
1. W obszarze **serwer proxy HTTP** wybierz łącze **Zmień** , aby otworzyć okno dialogowe **Ustawianie serwera proxy HTTP** .
1. Wybierz pozycję **Dalej**. Następnie zostanie wyświetlone ostrzeżenie z prośbą o zgodę na zapisanie ustawienia serwera proxy i ponowne uruchomienie usługi hosta Integration Runtime.

Za pomocą narzędzia Configuration Manager można wyświetlać i aktualizować serwer proxy HTTP.

![Wyświetl i zaktualizuj serwer proxy](media/create-self-hosted-integration-runtime/view-proxy.png)

> [!NOTE]
> W przypadku skonfigurowania serwera proxy z uwierzytelnianiem NTLM usługa hosta Integration Runtime jest uruchamiana w ramach konta domeny. Jeśli później zmienisz hasło dla konta domeny, pamiętaj o zaktualizowaniu ustawień konfiguracji usługi i ponownym uruchomieniu usługi. Ze względu na to wymaganie sugerujemy dostęp do serwera proxy przy użyciu dedykowanego konta domeny, które nie wymaga częstego aktualizowania hasła.

### <a name="configure-proxy-server-settings"></a>Skonfiguruj ustawienia serwera proxy

W przypadku wybrania opcji **Użyj serwera proxy** dla serwera proxy HTTP, własne środowisko Integration runtime używa ustawień serwera proxy w diahost.exe.config i diawp.exe.config. Gdy te pliki nie określają serwera proxy, własne środowisko Integration Runtime łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizowania pliku diahost.exe.config:

1. W Eksploratorze plików wykonaj bezpieczną kopię folderu C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config jako kopię zapasową oryginalnego pliku.
1. Otwórz Notatnik uruchomiony jako administrator.
1. W Notatniku otwórz plik tekstowy C:\Program Files\Microsoft Integration Runtime\4.0\Shared\diahost.exe.config.
1. Znajdź domyślny tag **System.NET** , jak pokazano w poniższym kodzie:

    ```xml
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```
    Następnie można dodać Szczegóły serwera proxy, jak pokazano w następującym przykładzie:

    ```xml
    <system.net>
        <defaultProxy enabled="true">
              <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Tag serwera proxy umożliwia dodawanie wymaganych ustawień, takich jak `scriptLocation` . Zobacz [ \<proxy\> element (Ustawienia sieci)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) dla składni.

    ```xml
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
1. Zapisz plik konfiguracji w jego pierwotnej lokalizacji. Następnie uruchom ponownie usługę hosta Integration Runtime (samodzielne), która pobiera zmiany.

   Aby ponownie uruchomić usługę, użyj apletu usługi w panelu sterowania. Lub z Integration Runtime Configuration Manager wybierz przycisk **Zatrzymaj usługę** , a następnie wybierz pozycję **Uruchom usługę**.

   Jeśli usługa nie zostanie uruchomiona, prawdopodobnie dodano niepoprawną składnię tagu XML w edytowanym pliku konfiguracyjnym aplikacji.

> [!IMPORTANT]
> Nie zapomnij zaktualizować obu diahost.exe.config i diawp.exe.config.

Należy również upewnić się, że Microsoft Azure znajduje się na liście dozwolonych w firmie. Listę prawidłowych adresów IP platformy Azure można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

### <a name="possible-symptoms-for-issues-related-to-the-firewall-and-proxy-server"></a>Ewentualne objawy problemów związanych z zaporą i serwerem proxy

W przypadku wyświetlenia komunikatów o błędach, takich jak następujące, prawdopodobnie przyczyną jest niepoprawna konfiguracja zapory lub serwera proxy. Taka konfiguracja uniemożliwia samodzielne środowisko Integration Runtime łączenie się z usługą Data Factory w celu samodzielnego uwierzytelnienia. Aby upewnić się, że Zapora i serwer proxy są prawidłowo skonfigurowane, zapoznaj się z poprzednią sekcją.

* Podczas próby zarejestrowania własnego środowiska Integration Runtime zostanie wyświetlony następujący komunikat o błędzie: "nie można zarejestrować tego węzła Integration Runtime. Upewnij się, że klucz uwierzytelniania jest prawidłowy, a na tym komputerze jest uruchomiona usługa hosta usługi integracji.
* Po otwarciu Integration Runtime Configuration Manager zostanie wyświetlony stan **odłączony** lub **nawiązanie połączenia**. Podczas wyświetlania dzienników zdarzeń systemu Windows w obszarze **Podgląd zdarzeń**  >  **Dzienniki aplikacji i usług**  >  **Microsoft Integration Runtime** są wyświetlane komunikaty o błędach podobne do tego:

    ```
    Unable to connect to the remote server
    A component of Integration Runtime has become unresponsive and restarts automatically. Component name: Integration Runtime (Self-hosted).
    ```

### <a name="enable-remote-access-from-an-intranet"></a>Włącz dostęp zdalny z intranetu

W przypadku korzystania z programu PowerShell w celu szyfrowania poświadczeń z komputera w sieci, poza miejscem, w którym zainstalowano własne środowisko Integration Runtime, można włączyć opcję **dostęp zdalny z intranetu** . Jeśli program PowerShell zostanie uruchomiony w celu szyfrowania poświadczeń na komputerze, na którym zainstalowano własne środowisko Integration Runtime, nie można włączyć **dostępu zdalnego z intranetu**.

Włącz **dostęp zdalny z intranetu** przed dodaniem kolejnego węzła w celu zapewnienia wysokiej dostępności i skalowalności.  

W przypadku uruchomienia samodzielnego środowiska Integration Runtime w wersji 3,3 lub nowszej, domyślnie samodzielny Instalator środowiska Integration Runtime wyłącza **dostęp zdalny z intranetu** na własnym komputerze Integration Runtime.

W przypadku korzystania z zapory z poziomu partnera lub innych osób można ręcznie otworzyć port 8060 lub port skonfigurowany przez użytkownika. Jeśli masz problem z zaporą podczas konfigurowania własnego środowiska Integration Runtime, użyj następującego polecenia, aby zainstalować własne środowisko Integration Runtime bez konfigurowania zapory:

```
msiexec /q /i IntegrationRuntime.msi NOFIREWALL=1
```

Jeśli nie zdecydujesz się na otwarcie portu 8060 na komputerze, na którym działa środowisko Integration Runtime, użyj mechanizmów innych niż ustawienia aplikacja poświadczeń, aby skonfigurować poświadczenia magazynu danych. Można na przykład użyć polecenia cmdlet **New-AzDataFactoryV2LinkedServiceEncryptCredential** programu PowerShell.

## <a name="next-steps"></a>Następne kroki

Aby uzyskać instrukcje krok po kroku, zobacz [Samouczek: kopiowanie danych lokalnych do chmury](tutorial-hybrid-copy-powershell.md).
