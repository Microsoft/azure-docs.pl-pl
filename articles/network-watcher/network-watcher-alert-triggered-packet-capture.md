---
title: Używanie funkcji przechwytywania pakietów do aktywnego monitorowania sieci z alertami — Azure Functions
titleSuffix: Azure Network Watcher
description: W tym artykule opisano sposób tworzenia alertu wyzwalającego przechwytywanie pakietów przy użyciu usługi Azure Network Watcher
services: network-watcher
documentationcenter: na
author: damendo
ms.assetid: 75e6e7c4-b3ba-4173-8815-b00d7d824e11
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: eefd67d4d150c0c8d152002a174c62d31fcb8b5f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90975063"
---
# <a name="use-packet-capture-for-proactive-network-monitoring-with-alerts-and-azure-functions"></a>Używanie funkcji przechwytywania pakietów do aktywnego monitorowania sieci z alertami i Azure Functions

Przechwytywanie pakietów Network Watcher tworzy sesje przechwytywania do śledzenia ruchu z maszyn wirtualnych i z nich. Plik przechwytywania może mieć filtr, który jest zdefiniowany do śledzenia tylko ruchu, który ma być monitorowany. Te dane są następnie przechowywane w magazynie obiektów blob lub lokalnie na maszynie gościa.

Tę możliwość można uruchomić zdalnie z innych scenariuszy automatyzacji, takich jak Azure Functions. Funkcja przechwytywania pakietów umożliwia uruchamianie aktywnych przechwytywania w oparciu o zdefiniowane anomalie sieci. Inne zastosowania obejmują gromadzenie danych statystycznych sieci, uzyskiwanie informacji na temat intruzów sieci, debugowanie komunikacji klient-serwer i nie tylko.

Zasoby wdrożone na platformie Azure — Uruchom 24/7. Ty i pracownicy nie mogą aktywnie monitorować stanu wszystkich zasobów 24/7. Na przykład co się stanie, jeśli wystąpi problem z 2 AM?

Korzystając z Network Watcher, alertów i funkcji z poziomu ekosystemu platformy Azure, możesz proaktywnie odpowiedzieć na dane i narzędzia, aby rozwiązać problemy w sieci.

![Na diagramie przedstawiono Network Watcher rozszerzenie na maszynie wirtualnej, które przenosi do segmentów T C P wysłanych > 100 błąd, który przepływa do Azure Functions, który przepływa do Network Watcher z powrotem do rozszerzenia Network Watcher.][scenario]


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Wymagania wstępne

* Najnowsza wersja [Azure PowerShell](/powershell/azure/install-Az-ps).
* Istniejące wystąpienie Network Watcher. Jeśli jeszcze tego nie masz, [Utwórz wystąpienie Network Watcher](network-watcher-create.md).
* Istniejąca maszyna wirtualna w tym samym regionie co Network Watcher z rozszerzeniem [systemu Windows](../virtual-machines/windows/extensions-nwa.md) lub [rozszerzeniem maszyny wirtualnej](../virtual-machines/linux/extensions-nwa.md)z systemem Linux.

## <a name="scenario"></a>Scenariusz

W tym przykładzie maszyna wirtualna wysyła więcej segmentów TCP niż zwykle i chcesz otrzymywać alerty. Segmenty TCP są używane na przykład w tym miejscu, ale można użyć dowolnego warunku alertu.

Po otrzymaniu alertu chcesz otrzymywać dane na poziomie pakietu, aby zrozumieć, dlaczego komunikacja została zwiększona. Następnie można wykonać kroki, aby przywrócić maszynę wirtualną do zwykłej komunikacji.

W tym scenariuszu przyjęto założenie, że masz istniejące wystąpienie Network Watcher i grupę zasobów z prawidłową maszyną wirtualną.

Poniższa lista zawiera omówienie przepływu pracy, który ma miejsce:

1. Alert jest wyzwalany na maszynie wirtualnej.
1. Alert wywołuje funkcję platformy Azure za pośrednictwem elementu webhook.
1. Funkcja platformy Azure przetwarza alert i uruchamia sesję przechwytywania pakietów Network Watcher.
1. Przechwytywanie pakietów działa na maszynie wirtualnej i zbiera ruch.
1. Plik przechwytywania pakietów zostanie przekazany do konta magazynu w celu przejrzenia i zdiagnozowania.

Aby zautomatyzować ten proces, tworzymy i łączymy alert na naszej maszynie wirtualnej w celu wyzwolenia po wystąpieniu zdarzenia. Utworzymy również funkcję do wywołania do Network Watcher.

W tym scenariuszu są przeprowadzane następujące czynności:

* Tworzy funkcję platformy Azure, która uruchamia przechwytywanie pakietu.
* Tworzy regułę alertu na maszynie wirtualnej i konfiguruje regułę alertu, aby wywoływać funkcję platformy Azure.

## <a name="create-an-azure-function"></a>Tworzenie funkcji platformy Azure

Pierwszym krokiem jest utworzenie funkcji platformy Azure w celu przetworzenia alertu i utworzenia przechwycenia pakietu.

1. W [Azure Portal](https://portal.azure.com)wybierz pozycję **Utwórz zasób**  >  **aplikacja funkcji obliczeniowy**  >  **Function App**.

    ![Tworzenie aplikacji funkcji][1-1]

2. W bloku **aplikacja funkcji** wprowadź następujące wartości, a następnie wybierz pozycję **OK** , aby utworzyć aplikację:

    |**Ustawienie** | **Wartość** | **Szczegóły** |
    |---|---|---|
    |**Nazwa aplikacji**|PacketCaptureExample|Nazwa aplikacji funkcji.|
    |**Subskrypcja**|[Twoja subskrypcja] Subskrypcja, dla której ma zostać utworzona aplikacja funkcji.||
    |**Grupa zasobów**|PacketCaptureRG|Grupa zasobów zawierająca aplikację funkcji.|
    |**Plan hostingu**|Plan zużycia| Typ planu używany przez aplikację funkcji. Opcje to użycie lub plan Azure App Service. |
    |**Lokalizacja**|Central US| Region, w którym ma zostać utworzona aplikacja funkcji.|
    |**Konto magazynu**|automatycznie generowanych| Konto magazynu, które Azure Functions potrzeby magazynowania ogólnego przeznaczenia.|

3. W bloku **aplikacje funkcji PacketCaptureExample** wybierz pozycję **funkcje**  >  **Funkcja niestandardowa**  > **+** .

4. Wybierz pozycję **HttpTrigger — PowerShell**, a następnie wprowadź pozostałe informacje. Na koniec aby utworzyć funkcję, wybierz pozycję **Utwórz**.

    |**Ustawienie** | **Wartość** | **Szczegóły** |
    |---|---|---|
    |**Scenariusz**|Eksperymentalne|Typ scenariusza|
    |**Nadawanie nazwy funkcji**|AlertPacketCapturePowerShell|Nazwa funkcji|
    |**Poziom autoryzacji**|Funkcja|Poziom autoryzacji dla funkcji|

![Przykład funkcji][functions1]

> [!NOTE]
> Szablon programu PowerShell jest eksperymentalny i nie ma pełnej obsługi.

Na potrzeby tego przykładu są wymagane dostosowania opisane w poniższych krokach.

### <a name="add-modules"></a>Dodaj moduły

Aby użyć Network Watcher poleceń cmdlet programu PowerShell, Przekaż najnowszy moduł programu PowerShell do aplikacji funkcji.

1. Na komputerze lokalnym z zainstalowanymi najnowszymi modułami Azure PowerShell uruchom następujące polecenie programu PowerShell:

    ```powershell
    (Get-Module Az.Network).Path
    ```

    W tym przykładzie przedstawiono ścieżkę lokalną modułów Azure PowerShell. Te foldery są używane w późniejszym kroku. Moduły, które są używane w tym scenariuszu, to:

   * Az.Network

   * Az.Accounts

   * Az.Resources

     ![Foldery programu PowerShell][functions5]

1. Wybierz pozycję **Ustawienia aplikacji funkcji**  >  **Przejdź do Edytor usługi App Service**.

    ![Ustawienia aplikacji funkcji][functions2]

1. Kliknij prawym przyciskiem myszy folder **AlertPacketCapturePowershell** , a następnie utwórz folder o nazwie **azuremodules**. 

4. Utwórz podfolder dla każdego wymaganego modułu.

    ![Folder i podfoldery][functions3]

    * Az.Network

    * Az.Accounts

    * Az.Resources

1. Kliknij prawym przyciskiem myszy folder **AZ. Network** , a następnie wybierz pozycję **Przekaż pliki**. 

6. Przejdź do modułów platformy Azure. W lokalnym folderze **AZ. Network** wybierz wszystkie pliki w folderze. Następnie wybierz przycisk **OK**. 

7. Powtórz te kroki dla **AZ. Accounts** i **AZ. resources**.

    ![Przekazywanie plików][functions6]

1. Po zakończeniu każdy folder powinien mieć pliki modułu programu PowerShell z komputera lokalnego.

    ![Pliki programu PowerShell][functions7]

### <a name="authentication"></a>Uwierzytelnianie

Aby użyć poleceń cmdlet programu PowerShell, należy się uwierzytelnić. Uwierzytelnianie można skonfigurować w aplikacji funkcji. Aby skonfigurować uwierzytelnianie, należy skonfigurować zmienne środowiskowe i przekazać zaszyfrowany plik klucza do aplikacji funkcji.

> [!NOTE]
> W tym scenariuszu przedstawiono tylko jeden przykład sposobu implementowania uwierzytelniania przy użyciu Azure Functions. Istnieją inne sposoby wykonania tej czynności.

#### <a name="encrypted-credentials"></a>Zaszyfrowane poświadczenia

Poniższy skrypt programu PowerShell tworzy plik klucza o nazwie **PassEncryptKey. Key**. Zapewnia także zaszyfrowaną wersję dostarczonego hasła. To hasło jest takie samo jak hasło, które jest zdefiniowane dla aplikacji Azure Active Directory używanej na potrzeby uwierzytelniania.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

W Edytor usługi App Service aplikacji funkcji Utwórz folder o nazwie **klucze** w obszarze **AlertPacketCapturePowerShell**. Następnie Przekaż plik **PassEncryptKey. Key** , który został utworzony w poprzednim przykładzie programu PowerShell.

![Klucz funkcji][functions8]

### <a name="retrieve-values-for-environment-variables"></a>Pobierz wartości zmiennych środowiskowych

Ostatnim wymaganiem jest skonfigurowanie zmiennych środowiskowych, które są niezbędne do uzyskania dostępu do wartości uwierzytelniania. Na poniższej liście przedstawiono zmienne środowiskowe, które są tworzone:

* AzureClientID

* AzureTenant

* AzureCredPassword


#### <a name="azureclientid"></a>AzureClientID

Identyfikator klienta to identyfikator aplikacji w Azure Active Directory.

1. Jeśli nie masz jeszcze aplikacji do użycia, uruchom poniższy przykład, aby utworzyć aplikację.

    ```powershell
    $app = New-AzADApplication -DisplayName "ExampleAutomationAccount_MF" -HomePage "https://exampleapp.com" -IdentifierUris "https://exampleapp1.com/ExampleFunctionsAccount" -Password "<same password as defined earlier>"
    New-AzADServicePrincipal -ApplicationId $app.ApplicationId
    Start-Sleep 15
    New-AzRoleAssignment -RoleDefinitionName Contributor -ServicePrincipalName $app.ApplicationId
    ```

   > [!NOTE]
   > Hasło, które ma być używane podczas tworzenia aplikacji, powinno być tym samym hasłem, które zostało utworzone wcześniej podczas zapisywania pliku klucza.

1. W Azure Portal wybierz pozycję **subskrypcje**. Wybierz subskrypcję, której chcesz użyć, a następnie wybierz pozycję **Kontrola dostępu (IAM)**.

    ![Funkcja IAM][functions9]

1. Wybierz konto, którego chcesz użyć, a następnie wybierz pozycję **Właściwości**. Skopiuj identyfikator aplikacji.

    ![Identyfikator aplikacji funkcji][functions10]

#### <a name="azuretenant"></a>AzureTenant

Uzyskaj identyfikator dzierżawy, uruchamiając następujące przykładowe polecenia programu PowerShell:

```powershell
(Get-AzSubscription -SubscriptionName "<subscriptionName>").TenantId
```

#### <a name="azurecredpassword"></a>AzureCredPassword

Wartość zmiennej środowiskowej AzureCredPassword to wartość, którą uzyskasz od uruchomienia poniższego przykładu programu PowerShell. Ten przykład jest taki sam, jak ten, który jest wyświetlany w poprzedniej sekcji **zaszyfrowane poświadczenia** . Wymagana wartość jest wartością wyjściową `$Encryptedpassword` zmiennej.  Jest to hasło nazwy głównej usługi, które zostało zaszyfrowane za pomocą skryptu programu PowerShell.

```powershell
#Variables
$keypath = "C:\temp\PassEncryptKey.key"
$AESKey = New-Object Byte[] 32
$Password = "<insert a password here>"

#Keys
[Security.Cryptography.RNGCryptoServiceProvider]::Create().GetBytes($AESKey) 
Set-Content $keypath $AESKey

#Get encrypted password
$secPw = ConvertTo-SecureString -AsPlainText $Password -Force
$AESKey = Get-content $KeyPath
$Encryptedpassword = $secPw | ConvertFrom-SecureString -Key $AESKey
$Encryptedpassword
```

### <a name="store-the-environment-variables"></a>Przechowuj zmienne środowiskowe

1. Przejdź do aplikacji funkcji. Następnie wybierz kolejno pozycje **Funkcja ustawienia aplikacji**  >  **Skonfiguruj ustawienia aplikacji**.

    ![Konfigurowanie ustawień aplikacji][functions11]

1. Dodaj zmienne środowiskowe i ich wartości do ustawień aplikacji, a następnie wybierz pozycję **Zapisz**.

    ![Ustawienia aplikacji][functions12]

### <a name="add-powershell-to-the-function"></a>Dodawanie programu PowerShell do funkcji

Teraz można wykonywać wywołania do Network Watcher z poziomu funkcji platformy Azure. W zależności od wymagań implementacja tej funkcji może się różnić. Jednak ogólny przepływ kodu jest następujący:

1. Przetwarzanie parametrów wejściowych.
2. Wykonaj zapytanie o istniejące przechwycenia pakietu, aby sprawdzić ograniczenia i rozwiązać konflikty nazw.
3. Utwórz przechwycenie pakietu z odpowiednimi parametrami.
4. Okresowe przechwytywanie pakietów sondy do momentu ukończenia.
5. Powiadom użytkownika, że sesja przechwytywania pakietów została ukończona.

Poniższy przykład to kod programu PowerShell, który może być używany w funkcji. Istnieją wartości, które muszą zostać zastąpione dla identyfikatora **subskrypcji**, **resourceGroupName**i **storageAccountName**.

```powershell
            #Import Azure PowerShell modules required to make calls to Network Watcher
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Accounts\Az.Accounts.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Network\Az.Network.psd1" -Global
            Import-Module "D:\home\site\wwwroot\AlertPacketCapturePowerShell\azuremodules\Az.Resources\Az.Resources.psd1" -Global

            #Process alert request body
            $requestBody = Get-Content $req -Raw | ConvertFrom-Json

            #Storage account ID to save captures in
            $storageaccountid = "/subscriptions/{subscriptionId}/resourceGroups/{resourceGroupName}/providers/Microsoft.Storage/storageAccounts/{storageAccountName}"

            #Packet capture vars
            $packetcapturename = "PSAzureFunction"
            $packetCaptureLimit = 10
            $packetCaptureDuration = 10

            #Credentials
            $tenant = $env:AzureTenant
            $pw = $env:AzureCredPassword
            $clientid = $env:AzureClientId
            $keypath = "D:\home\site\wwwroot\AlertPacketCapturePowerShell\keys\PassEncryptKey.key"

            #Authentication
            $secpassword = $pw | ConvertTo-SecureString -Key (Get-Content $keypath)
            $credential = New-Object System.Management.Automation.PSCredential ($clientid, $secpassword)
            Connect-AzAccount -ServicePrincipal -Tenant $tenant -Credential $credential #-WarningAction SilentlyContinue | out-null


            #Get the VM that fired the alert
            if($requestBody.context.resourceType -eq "Microsoft.Compute/virtualMachines")
            {
                Write-Output ("Subscription ID: {0}" -f $requestBody.context.subscriptionId)
                Write-Output ("Resource Group:  {0}" -f $requestBody.context.resourceGroupName)
                Write-Output ("Resource Name:  {0}" -f $requestBody.context.resourceName)
                Write-Output ("Resource Type:  {0}" -f $requestBody.context.resourceType)

                #Get the Network Watcher in the VM's region
                $networkWatcher = Get-AzResource | Where {$_.ResourceType -eq "Microsoft.Network/networkWatchers" -and $_.Location -eq $requestBody.context.resourceRegion}

                #Get existing packetCaptures
                $packetCaptures = Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher

                #Remove existing packet capture created by the function (if it exists)
                $packetCaptures | %{if($_.Name -eq $packetCaptureName)
                { 
                    Remove-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -PacketCaptureName $packetCaptureName
                }}

                #Initiate packet capture on the VM that fired the alert
                if ((Get-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher).Count -lt $packetCaptureLimit){
                    echo "Initiating Packet Capture"
                    New-AzNetworkWatcherPacketCapture -NetworkWatcher $networkWatcher -TargetVirtualMachineId $requestBody.context.resourceId -PacketCaptureName $packetCaptureName -StorageAccountId $storageaccountid -TimeLimitInSeconds $packetCaptureDuration
                    Out-File -Encoding Ascii -FilePath $res -inputObject "Packet Capture created on ${requestBody.context.resourceID}"
                }
            } 
 ``` 
#### <a name="retrieve-the-function-url"></a>Pobierz adres URL funkcji 
1. Po utworzeniu funkcji Skonfiguruj alert, aby wywoływać adres URL skojarzony z funkcją. Aby uzyskać tę wartość, skopiuj adres URL funkcji z aplikacji funkcji.

    ![Znajdowanie adresu URL funkcji][functions13]

2. Skopiuj adres URL funkcji dla aplikacji funkcji.

    ![Kopiowanie adresu URL funkcji][2]

Jeśli wymagane są właściwości niestandardowe w ładunku żądania POST elementu webhook, zapoznaj się z tematem [Konfigurowanie elementu webhook w ramach alertu dotyczącego metryki platformy Azure](../azure-monitor/platform/alerts-webhooks.md).

## <a name="configure-an-alert-on-a-vm"></a>Konfigurowanie alertu na maszynie wirtualnej

Alerty można skonfigurować w taki sposób, aby powiadamiać osoby, gdy określona Metryka przekroczy przypisany do niej próg. W tym przykładzie alert znajduje się na segmentach TCP, które są wysyłane, ale alert może być wyzwalany dla wielu innych metryk. W tym przykładzie alert jest skonfigurowany do wywoływania elementu webhook w celu wywołania funkcji.

### <a name="create-the-alert-rule"></a>Tworzenie reguły alertu

Przejdź do istniejącej maszyny wirtualnej, a następnie Dodaj regułę alertu. Bardziej szczegółową dokumentację dotyczącą konfigurowania alertów można znaleźć [w temacie Tworzenie alertów w Azure monitor dla usług platformy Azure — Azure Portal](../monitoring-and-diagnostics/insights-alerts-portal.md). Wprowadź następujące wartości w bloku **reguła alertu** , a następnie wybierz przycisk **OK**.

  |**Ustawienie** | **Wartość** | **Szczegóły** |
  |---|---|---|
  |**Nazwa**|TCP_Segments_Sent_Exceeded|Nazwa reguły alertu.|
  |**Opis**|Liczba wysłanych segmentów TCP przekroczyła próg|Opis reguły alertu.|
  |**Metryka**|Wysłane segmenty TCP| Metryka używana do wyzwalania alertu. |
  |**Warunek**|Większe niż| Warunek, który ma być używany podczas oceniania metryki.|
  |**Próg**|100| Wartość metryki wyzwalającej alert. Ta wartość powinna być ustawiona na prawidłową wartość dla danego środowiska.|
  |**Czasu**|W ciągu ostatnich pięciu minut| Określa okres, w którym ma być wyszukiwany próg dla metryki.|
  |**Element webhook**|[adres URL elementu webhook z aplikacji funkcji]| Adres URL elementu webhook z aplikacji funkcji, który został utworzony w poprzednich krokach.|

> [!NOTE]
> Metryka segmentów TCP nie jest domyślnie włączona. Dowiedz się więcej o tym, jak włączyć dodatkowe metryki, odwiedzając [funkcję monitorowanie i diagnostykę](../monitoring-and-diagnostics/insights-how-to-use-diagnostics.md).

## <a name="review-the-results"></a>Sprawdzanie wyników

Po zastosowaniu kryteriów dla wyzwalaczy alertów zostanie utworzone przechwytywanie pakietów. Przejdź do Network Watcher, a następnie wybierz pozycję **przechwytywanie pakietów**. Na tej stronie można wybrać łącze plik przechwytywania pakietów, aby pobrać funkcję przechwytywania pakietów.

![Wyświetl przechwycenie pakietu][functions14]

Jeśli plik przechwytywania jest przechowywany lokalnie, możesz go pobrać, logując się do maszyny wirtualnej.

Aby uzyskać instrukcje dotyczące pobierania plików z kont usługi Azure Storage, zobacz [Rozpoczynanie pracy z usługą Azure Blob Storage przy użyciu platformy .NET](../storage/blobs/storage-dotnet-how-to-use-blobs.md). Innym narzędziem, którego można użyć, jest [Eksplorator usługi Storage](https://storageexplorer.com/).

Po pobraniu przechwytywania można je wyświetlić przy użyciu dowolnego narzędzia, które może odczytać plik **. Cap** . Poniżej znajdują się linki do dwóch z następujących narzędzi:

- [Analizator komunikatów firmy Microsoft](https://technet.microsoft.com/library/jj649776.aspx)
- [WireShark](https://www.wireshark.org/)

## <a name="next-steps"></a>Następne kroki

Dowiedz się, jak wyświetlać przechwycone pakiety, odwiedzając [analizę przechwytywania pakietów za pomocą programu Wireshark](network-watcher-deep-packet-inspection.md).


[1]: ./media/network-watcher-alert-triggered-packet-capture/figure1.png
[1-1]: ./media/network-watcher-alert-triggered-packet-capture/figure1-1.png
[2]: ./media/network-watcher-alert-triggered-packet-capture/figure2.png
[3]: ./media/network-watcher-alert-triggered-packet-capture/figure3.png
[functions1]:./media/network-watcher-alert-triggered-packet-capture/functions1.png
[functions2]:./media/network-watcher-alert-triggered-packet-capture/functions2.png
[functions3]:./media/network-watcher-alert-triggered-packet-capture/functions3.png
[functions4]:./media/network-watcher-alert-triggered-packet-capture/functions4.png
[functions5]:./media/network-watcher-alert-triggered-packet-capture/functions5.png
[functions6]:./media/network-watcher-alert-triggered-packet-capture/functions6.png
[functions7]:./media/network-watcher-alert-triggered-packet-capture/functions7.png
[functions8]:./media/network-watcher-alert-triggered-packet-capture/functions8.png
[functions9]:./media/network-watcher-alert-triggered-packet-capture/functions9.png
[functions10]:./media/network-watcher-alert-triggered-packet-capture/functions10.png
[functions11]:./media/network-watcher-alert-triggered-packet-capture/functions11.png
[functions12]:./media/network-watcher-alert-triggered-packet-capture/functions12.png
[functions13]:./media/network-watcher-alert-triggered-packet-capture/functions13.png
[functions14]:./media/network-watcher-alert-triggered-packet-capture/functions14.png
[scenario]:./media/network-watcher-alert-triggered-packet-capture/scenario.png
