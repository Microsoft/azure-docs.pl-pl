---
title: Rejestrowanie i skanowanie Power BI za pomocą programu PowerShell (wersja zapoznawcza)
description: Dowiedz się, jak używać programu PowerShell do rejestrowania i skanowania dzierżawy Power BI w usłudze Azure kontrolą.
author: darrenparker
ms.author: hophan
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: how-to
ms.date: 10/12/2020
ms.openlocfilehash: f0b541baf49307006c18f07d92bd409763a29e3a
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/03/2020
ms.locfileid: "96553507"
---
# <a name="use-powershell-to-register-and-scan-power-bi-in-azure-purview-preview"></a>Rejestrowanie i skanowanie Power BI w usłudze Azure kontrolą za pomocą programu PowerShell (wersja zapoznawcza) 

W tym artykule przedstawiono sposób użycia programu PowerShell w celu skonfigurowania skanowania Power BI dzierżawy w katalogu usługi Azure kontrolą.

## <a name="power-bi-authentication-background"></a>Tło uwierzytelniania Power BI

Wykaz kontrolą musi nawiązać połączenie z interfejsem API administratora Power BI, aby skanować artefakty w dzierżawie Power BI. Interfejs API administratora Power BI obsługuje obecnie dwa typy uwierzytelniania:

- Tożsamość zarządzana (MSI).
- Uwierzytelnianie delegowane użytkownika.

> [!Note]
> Plik MSI jest zalecany, chyba że delegowane uwierzytelnianie użytkowników jest wymagane.

## <a name="create-a-security-group"></a>Tworzenie grupy zabezpieczeń

Każdy katalog kontrolą ma własną tożsamość zarządzaną przez system, która musi mieć dostęp do dzierżawy Power BI, aby umożliwić skanowanie. Nazwy wykazu można użyć, aby znaleźć swoją tożsamość na Azure Portal.

1. W [Azure Portal](https://portal.azure.com)wyszukaj ciąg Azure Active Directory.
1. Utwórz nową grupę zabezpieczeń w Azure Active Directory, wykonując następujące czynności: [Utwórz grupę podstawową i Dodaj członków przy użyciu Azure Active Directory](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal).

    > [!Tip]
    > Możesz pominąć ten krok, jeśli masz już grupę zabezpieczeń do użycia.

1. Upewnij się, że wybrano opcję zabezpieczenia jako **Typ grupy**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/security-group.png" alt-text="Typ grupy zabezpieczeń":::

1. Dodaj zarządzaną tożsamość wykazu do tej grupy zabezpieczeń, wybierając członków, a następnie **Dodaj członków**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-group-member.png" alt-text="Dodaj wystąpienie zarządzane wykazu do grupy":::

1. Wyszukaj swój wykaz i wybierz go.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/add-catalog-to-group-by-search.png" alt-text="Dodaj katalog, wyszukując go":::

1. Powinno zostać wyświetlone powiadomienie o powodzeniu informujące o tym, że zostało dodane.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/success-add-catalog-msi.png" alt-text="Pomyślnie dodano plik MSI katalogu":::

## <a name="associate-the-security-group-with-power-bi"></a>Skojarz grupę zabezpieczeń z Power BI

1. Zaloguj się do [portalu administracyjnego Power BI](https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1). Dołącz tę flagę funkcji:  `allowServicePrincipalsUseReadAdminAPIsUI=1` . Ta flaga włącza funkcję, która umożliwia skojarzenie grupy zabezpieczeń. Przykład:

    ```http
    https://app.powerbi.com/admin-portal/tenantSettings?allowServicePrincipalsUseReadAdminAPIsUI=1`
    ```

    > [!Important]
    > Musisz być administratorem Power BI, aby wyświetlić stronę Ustawienia dzierżawy.

1. Wybierz pozycję **Ustawienia dewelopera**  >  **Zezwalaj podmiotom usługi na korzystanie z interfejsów API tylko do odczytu Power BI (wersja zapoznawcza)**.
1. Wybierz **określone grupy zabezpieczeń**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/allow-service-principals-power-bi-admin.png" alt-text="Obraz przedstawiający sposób zezwalania podmiotom usługi na pobieranie tylko do odczytu uprawnień Power BI administratora ":::

    > [!Caution]
    > Gdy zezwolisz utworzoną przez Ciebie grupę zabezpieczeń (która ma tożsamość zarządzaną przez usługi Data Catalog jako członka), aby korzystać z interfejsów API administratora Power BI tylko do odczytu, możesz również zezwolić na dostęp do metadanych (np. nazw pulpitów nawigacyjnych i raportów, właścicieli, opisów itp.) dla wszystkich artefaktów Power BI w tej dzierżawie. Po pobraniu metadanych do usługi Azure kontrolą, uprawnienia kontrolą, a nie Power BI, określają, kto może zobaczyć te metadane.

    > [!Note]
    > Grupę zabezpieczeń można usunąć z ustawień dewelopera, ale wcześniej wyodrębnione metadane nie zostaną usunięte z konta kontrolą. Można je usunąć osobno.

## <a name="register-power-bi-and-set-up-a-scan"></a>Rejestrowanie Power BI i Konfigurowanie skanowania

Teraz, gdy masz uprawnienia katalogu do nawiązywania połączenia z interfejsem API administratora dzierżawy Power BI, musisz skonfigurować skanowanie w wykazie. W tym celu należy skonfigurować i uruchomić skrypt programu PowerShell.

1. Pobierz i Wyodrębnij polecenia cmdlet programu PowerShell środowiska ADC.
1. Skonfiguruj swój skrypt, podając wartości przydziałów w górnej części skryptu.

    ```PowerShell
    #Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false #Change to true if you need a new catalog to be created
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-msi-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    If ($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId  -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI  -Tenant $azuretenantId

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIManagedInstanceMsi
    ```

    > [!Note]
    > Musisz być współautorem lub właścicielem subskrypcji, w ramach której są uruchamiane polecenia.

1. Uruchom skanowanie, uruchamiając następujący skrypt:

    ```PowerShell
    Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
    ```

## <a name="register-and-scan-power-bi"></a>Rejestruj i Skanuj Power BI

Zalecaną metodą uwierzytelniania jest MSI. Jednak aby skanować dzierżawę Power BI, która znajduje się w innej dzierżawie platformy Azure niż katalog, Użyj uwierzytelniania delegowanego.

Aby przeprowadzić uwierzytelnianie delegowane, musisz mieć poświadczenia administratora, a także poświadczenia administratora Power BI. Musisz również utworzyć aplikację platformy Azure i udzielić jej Power BI dzierżawcy. uprawnienia do biblioteki.

1. Przejdź do [Azure Portal](https://portal.azure.com) i Wyszukaj **rejestracje aplikacji**.

1. W obszarze **rejestracje aplikacji** wybierz pozycję **+ Nowa rejestracja**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/new-app-registration.png" alt-text="Obraz przedstawiający sposób tworzenia nowej rejestracji aplikacji platformy Azure":::

1. Wprowadź nazwę aplikacji.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/register-new-app.png" alt-text="Zarejestruj nową aplikację":::

1. Po utworzeniu aplikacji wybierz pozycję **uprawnienia interfejsu API**, a następnie **Dodaj uprawnienie**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/app-permissions.png" alt-text="Obraz przedstawiający sposób dodawania uprawnień do aplikacji":::

1. Wybierz pozycję **usługa Power BI** na **uprawnieniach interfejsu API żądania**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/select-power-bi-service.png" alt-text="Obraz przedstawiający sposób wybierania usługi PBI":::

1. Wybierz **uprawnienia delegowane** i **dzierżawca. Read. All**. Następnie wybierz pozycję **Dodaj uprawnienia**.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/request-api-permissions.png" alt-text="Obraz przedstawiający sposób żądania uprawnień interfejsu API":::

1. Wybierz pozycję **Udziel zgody administratora**

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/grant-admin-perms.png" alt-text="Obraz przedstawiający sposób udzielania zgody administratora":::

1. Skopiuj wartości **identyfikatora aplikacji (klienta)** i **katalogu (dzierżawy)** .  Te wartości będą używane podczas konfigurowania skanowania.

    :::image type="content" source="./media/setup-power-bi-scan-PowerShell/copy-client-and-tenantid.png" alt-text="Obraz przedstawiający kopiowanie identyfikatorów klienta i dzierżawy":::

1. Skonfiguruj skanowanie w programie PowerShell. Skrypt wyświetli monit o podanie poświadczeń. Musisz mieć co najmniej rolę współautor i kontrolą rolę administratora źródła danych w subskrypcji platformy Azure, której zamierzasz używać.

    ```PowerShell
    # Purview Account Info
    $azuretenantId = '<Tenant Id>'
    $azuresubscriptionId = '<Tenant Id>'
    $azureResourceGroupName = '<Resource Group Name>'
    $azurePurviewAccountName = '<Catalog Name>'
    $createCatalog = $false
    $azureCatalogLocation = 'East Us' #The region of your account
    $dataSourceName = 'pbi-delegated-datasource01' #provide a unique data source name
    $dataScanName = 'scan01' #provide a unique scan name

    # Power BI Tenant Info
    $powerBITenantIdToScan = '<Power BI Tenant ID copied from above steps>'
    $ServicePrincipalId = '<Client ID copied from above steps>'
    $UserName = '<Power BI Admin emil ex: admin@firsttomarket.onmicrosoft.com>'
    $Password = '<Power BI Admin Password>'

    #PowerShell Command Module Path
    $ModulePath = '<Full path to where you extracted the PS zip files>\Microsoft.DataCatalog.Management.Commands.dll'
    Import-Module $ModulePath

    #Commands To Create catalog, Create DataSource, Create Datascan, Start Scan
    If($createCatalog -eq $true)
    {
      Connect-AzAccount -Tenant $azuretenantId -SubscriptionId $azuresubscriptionId
      Set-AzDataCatalog -ResourceGroupName $azureResourceGroupName -Name $azurePurviewAccountName -Location $azureCatalogLocation
    }

    Set-AzDataCatalogSessionSettings -DataCatalogSession -UserAuthentication -TenantId $azuretenantId   -DataCatalogAccountName $azurePurviewAccountName

    Set-AzDataCatalogDataSource -Name $dataSourceName -AccountType PowerBI -Tenant $powerBITenantIdToScan

    Set-AzDataCatalogScan -DataSourceName $dataSourceName -ScanName $dataScanName -PowerBIDelegated -ServicePrincipalId $ServicePrincipalId -UserName $UserName -Password $Password
    ```

1. Uruchom skanowanie.

      ```PowerShell
      Start-AzDataCatalogScan -DataSourceName $dataSourceName -Name $dataScanName -AsJob
      ```

## <a name="next-steps"></a>Następne kroki

Aby rozpocząć pracę z usługą Azure kontrolą, zobacz [Szybki Start: Tworzenie konta usługi Azure kontrolą](create-catalog-portal.md).
