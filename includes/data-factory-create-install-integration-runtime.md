---
author: linda33wj
ms.service: data-factory
ms.topic: include
ms.date: 11/09/2018
ms.author: jingwang
ms.openlocfilehash: c0a0b44b3de088539a070f1182248c4e6db7303f
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "97510281"
---
## <a name="create-a-self-hosted-integration-runtime"></a>Tworzenie własnego środowiska Integration Runtime

W tej sekcji utworzysz własne środowisko Integration Runtime i skojarzysz je z maszyną lokalną za pomocą bazy danych programu SQL Server. Własne środowisko Integration Runtime jest składnikiem, który kopiuje dane z SQL Server na komputerze, aby Azure SQL Database. 

1. Utwórz zmienną dla nazwy środowiska Integration Runtime. Użyj unikatowej nazwy i zanotuj ją. Będziesz jej używać w dalszej części tego samouczka. 

    ```powershell
   $integrationRuntimeName = "ADFTutorialIR"
    ```
2. Utwórz własne środowisko Integration Runtime. 

   ```powershell
   Set-AzDataFactoryV2IntegrationRuntime -Name $integrationRuntimeName -Type SelfHosted -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName
   ```

   Oto przykładowe dane wyjściowe:

   ```console
    Name              : <Integration Runtime name>
    Type              : SelfHosted
    ResourceGroupName : <ResourceGroupName>
    DataFactoryName   : <DataFactoryName>
    Description       : 
    Id                : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroupName>/providers/Microsoft.DataFactory/factories/<DataFactoryName>/integrationruntimes/ADFTutorialIR
    ```
  
3. Uruchom następujące polecenie, aby pobrać stan utworzonego środowiska Integration Runtime. Upewnij się, że właściwość **State** ma ustawioną wartość **NeedRegistration**. 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntime -name $integrationRuntimeName -ResourceGroupName $resourceGroupName -DataFactoryName $dataFactoryName -Status
   ```

   Oto przykładowe dane wyjściowe:

   ```console  
   State                     : NeedRegistration
   Version                   : 
   CreateTime                : 9/24/2019 6:00:00 AM
   AutoUpdate                : On
   ScheduledUpdateDate       : 
   UpdateDelayOffset         : 
   LocalTimeZoneOffset       : 
   InternalChannelEncryption : 
   Capabilities              : {}
   ServiceUrls               : {eu.frontend.clouddatahub.net}
   Nodes                     : {}
   Links                     : {}
   Name                      : ADFTutorialIR
   Type                      : SelfHosted
   ResourceGroupName         : <ResourceGroup name>
   DataFactoryName           : <DataFactory name>
   Description               : 
   Id                        : /subscriptions/<subscription ID>/resourceGroups/<ResourceGroup name>/providers/Microsoft.DataFactory/factories/<DataFactory name>/integrationruntimes/<Integration Runtime name>
   ```

4. Uruchom następujące polecenie, aby pobrać klucze uwierzytelniania używane do zarejestrowania własnego środowiska Integration Runtime za pomocą usługi Azure Data Factory w chmurze: 

   ```powershell
   Get-AzDataFactoryV2IntegrationRuntimeKey -Name $integrationRuntimeName -DataFactoryName $dataFactoryName -ResourceGroupName $resourceGroupName | ConvertTo-Json
   ```

   Oto przykładowe dane wyjściowe:

   ```json
   {
    "AuthKey1": "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx=",
    "AuthKey2":  "IR@0000000000-0000-0000-0000-000000000000@xy0@xy@yyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyyy="
   }
   ```    

5. Skopiuj jeden z kluczy (pomijając cudzysłowy) używanych do rejestracji własnego środowiska Integration Runtime, które zainstalujesz na swojej maszynie w kolejnych krokach.  

## <a name="install-the-integration-runtime-tool"></a>Zainstaluj narzędzie Integration Runtime

1. Jeśli środowisko Integration Runtime jest już zainstalowane na maszynie, odinstaluj je za pomocą apletu **Dodaj lub usuń programy**. 

2. [Pobierz](https://www.microsoft.com/download/details.aspx?id=39717) środowisko Integration Runtime (Self-hosted) na lokalną maszynę z systemem Windows. Uruchom instalację.

3. Na **stronie powitalnej instalatora środowiska Microsoft Integration Runtime** wybierz przycisk **Dalej**.

4. Na stronie **Umowa Licencyjna Użytkownika Oprogramowania** zaakceptuj warunki i umowę licencyjną, a następnie wybierz przycisk **Dalej**.

5. Na stronie **Folder docelowy** wybierz pozycję **Dalej**.

6. Na stronie **Gotowe do zainstalowania środowiska Microsoft Integration Runtime** wybierz pozycję **Zainstaluj**.

7. N astronie **zakończenia pracy z Kreatorem instalacji środowiska Microsoft Integration Runtime** wybierz pozycję **Zakończ**.

8. Na stronie **Rejestrowanie środowiska Integration Runtime (Self-hosted)** wklej klucz zapisany w poprzedniej sekcji i wybierz pozycję **Zarejestruj**. 

    ![Rejestrowanie środowiska Integration Runtime](media/data-factory-create-install-integration-runtime/register-integration-runtime.png)

9. Na stronie **nowy węzeł Integration Runtime (Self-Hosted)** wybierz pozycję **Zakończ**. 

10. Gdy własne środowisko Integration Runtime zostanie pomyślnie zarejestrowane, zostanie wyświetlony następujący komunikat:

    ![Pomyślnie zarejestrowano](media/data-factory-create-install-integration-runtime/registered-successfully.png)

14. Na stronie **Rejestrowanie produktu Integration Runtime (Self-hosted)** kliknij pozycję **Uruchom program Configuration Manager**.

15. Gdy węzeł zostanie połączony z usługą w chmurze, zostanie wyświetlona następująca strona:

    ![Strona Węzeł jest połączony](media/data-factory-create-install-integration-runtime/node-is-connected.png)

16. Teraz przetestuj łączność z bazą danych programu SQL Server.

    ![Karta Diagnostyka](media/data-factory-create-install-integration-runtime/config-manager-diagnostics-tab.png)   

    a. Na stronie programu **Configuration Manager** przejdź na kartę **Diagnostyka**.

    b. Wybierz typ źródła danych **SqlServer**.

    c. Wprowadź nazwę serwera.

    d. Wprowadź nazwę bazy danych.

    e. Wybierz tryb uwierzytelniania.

    f. Wprowadź nazwę użytkownika.

    przykład Wprowadź hasło, które jest skojarzone z nazwą użytkownika.

    h. Wybierz przycisk **Testuj**, aby upewnić się, że środowisko Integration Runtime może połączyć się z wystąpieniem programu SQL Server. Jeśli połączenie zostanie pomyślnie nawiązane, zostanie wyświetlony zielony znacznik wyboru. Jeśli nawiązywanie połączenia nie powiedzie się, zostanie wyświetlony komunikat o błędzie. Rozwiąż wszelkie problemy i upewnij się, że środowisko Integration Runtime może połączyć się z programem SQL Server.    

    > [!NOTE]
    > Zapisz wartości: typ uwierzytelniania, serwer, baza danych, użytkownik i hasło. Będziesz ich używać w dalszej części tego samouczka.
