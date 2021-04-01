---
title: Odświeżanie modeli Azure Analysis Services przy użyciu Azure Automation | Microsoft Docs
description: W tym artykule opisano sposób odświeżenia modelu kodu dla Azure Analysis Services przy użyciu Azure Automation.
author: chrislound
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 12/01/2020
ms.author: chlound
ms.openlocfilehash: 7c801511b6f24cf5ef04d55bb195e3a4c62d7b6d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96491251"
---
# <a name="refresh-with-azure-automation"></a>Odświeżanie za pomocą usługi Azure Automation

Za pomocą Azure Automation i elementów Runbook programu PowerShell, można wykonywać automatyczne operacje odświeżania danych w modelach tabelarycznych usługi Azure Analysis.  

W przykładzie w tym artykule jest wykorzystywany [moduł SqlServer programu PowerShell](/powershell/module/sqlserver/?view=sqlserver-ps&preserve-view=true). Przykładowy element Runbook programu PowerShell, który demonstruje odświeżenie modelu w dalszej części tego artykułu.  

## <a name="authentication"></a>Authentication

Wszystkie wywołania muszą zostać uwierzytelnione z prawidłowym tokenem Azure Active Directory (OAuth 2).  W przykładzie w tym artykule jest wykorzystywana nazwa główna usługi (SPN) do uwierzytelniania w Azure Analysis Services. Aby dowiedzieć się więcej, zobacz [Tworzenie jednostki usługi przy użyciu Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

## <a name="prerequisites"></a>Wymagania wstępne

> [!IMPORTANT]
> W poniższym przykładzie przyjęto założenie, że Zapora Azure Analysis Services jest wyłączona. Jeśli Zapora jest włączona, publiczny adres IP inicjatora żądania musi być uwzględniony w regule zapory.

### <a name="install-sqlserver-modules-from-powershell-gallery"></a>Zainstaluj moduły SqlServer z galerii programu PowerShell.

1. Na koncie Azure Automation kliknij pozycję **moduły**, a następnie **Przeglądaj Galerię**.

2. Na pasku wyszukiwania Wyszukaj ciąg **SqlServer**.

    ![Wyszukaj moduły](./media/analysis-services-refresh-azure-automation/1.png)

3. Wybierz pozycję SqlServer, a następnie kliknij pozycję **Importuj**.
 
    ![Importuj moduł](./media/analysis-services-refresh-azure-automation/2.png)

4. Kliknij przycisk **OK**.
 
### <a name="create-a-service-principal-spn"></a>Tworzenie nazwy głównej usługi (SPN)

Aby dowiedzieć się więcej na temat tworzenia nazwy głównej usługi, zobacz [Tworzenie jednostki usługi przy użyciu Azure Portal](../active-directory/develop/howto-create-service-principal-portal.md).

### <a name="configure-permissions-in-azure-analysis-services"></a>Konfigurowanie uprawnień w Azure Analysis Services
 
Utworzona jednostka usługi musi mieć uprawnienia administratora serwera na serwerze. Aby dowiedzieć się więcej, zobacz [Dodawanie nazwy głównej usługi do roli administratora serwera](analysis-services-addservprinc-admins.md).

## <a name="design-the-azure-automation-runbook"></a>Projektowanie Azure Automation elementu Runbook

1. Na koncie usługi Automation Utwórz zasób **poświadczeń** , który będzie używany do bezpiecznego przechowywania nazwy głównej usługi.

    ![Zrzut ekranu przedstawiający stronę "poświadczenia" z wybraną akcją "Dodaj poświadczenie".](./media/analysis-services-refresh-azure-automation/6.png)

2. Wprowadź szczegóły poświadczenia. W polu **Nazwa użytkownika** wprowadź identyfikator aplikacji głównej usługi (AppID), a następnie w polu **hasło** wprowadź klucz tajny jednostki usługi.

    ![Utwórz poświadczenie](./media/analysis-services-refresh-azure-automation/7.png)

3. Zaimportuj element Runbook usługi Automation.

    ![Zrzut ekranu przedstawiający stronę "elementy Runbook" z wybraną akcją "Importuj element Runbook".](./media/analysis-services-refresh-azure-automation/8.png)

4. Wyszukaj plik [Refresh-Model.ps1](#sample-powershell-runbook) , podaj **nazwę** i **Opis**, a następnie kliknij przycisk **Utwórz**.

    > [!NOTE]
    > Użyj skryptu z sekcji [przykładowego elementu Runbook programu PowerShell](#sample-powershell-runbook) w dolnej części tego dokumentu, aby utworzyć plik o nazwie Refresh-Model.ps1 i zapisać go na komputerze lokalnym w celu zaimportowania do elementu Runbook.

    ![Importuj element Runbook](./media/analysis-services-refresh-azure-automation/9.png)

5. Po utworzeniu elementu Runbook zostanie on automatycznie przeszedł w tryb edycji.  Kliknij pozycję **Opublikuj**.

    ![Publikowanie elementu Runbook](./media/analysis-services-refresh-azure-automation/10.png)

    > [!NOTE]
    > Zasób poświadczenia, który został utworzony wcześniej, jest pobierany przez element Runbook za pomocą polecenia **Get-AutomationPSCredential** .  To polecenie jest następnie przesyłane do polecenia programu PowerShell **Invoke-ProcessASADatabase** w celu przeprowadzenia uwierzytelniania do Azure Analysis Services.

6. Przetestuj element Runbook, klikając przycisk **Start**.

    ![Zrzut ekranu pokazujący stronę "przegląd" z wybraną akcją "Start".](./media/analysis-services-refresh-azure-automation/11.png)

7. Wypełnij parametry **DatabaseName**, **ANALYSISSERVER** i **reodświeżony** , a następnie kliknij przycisk **OK**. Parametr **WEBHOOKDATA** nie jest wymagany, gdy element Runbook jest uruchamiany ręcznie.

    ![Uruchamianie elementu Runbook](./media/analysis-services-refresh-azure-automation/12.png)

Jeśli element Runbook został wykonany pomyślnie, otrzymasz dane wyjściowe podobne do następujących:

![Pomyślne uruchomienie](./media/analysis-services-refresh-azure-automation/13.png)

## <a name="use-a-self-contained-azure-automation-runbook"></a>Korzystanie z samodzielnego Azure Automation elementu Runbook

Element Runbook można skonfigurować tak, aby wyzwolił Azure Analysis Services odświeżanie modelu w harmonogramie.

Można to skonfigurować w następujący sposób:

1. W elemencie Runbook usługi Automation kliknij pozycję **harmonogramy**, a następnie **Dodaj harmonogram**.
 
    ![Utwórz harmonogram](./media/analysis-services-refresh-azure-automation/14.png)

2. Kliknij pozycję **harmonogram**  >  **Utwórz nowy harmonogram**, a następnie wprowadź szczegóły.

    ![Konfigurowanie harmonogramu](./media/analysis-services-refresh-azure-automation/15.png)

3. Kliknij pozycję **Utwórz**.

4. Wypełnij parametry harmonogramu. Zostaną one użyte przy każdym wyzwoleniu elementu Runbook. Parametr **WEBHOOKDATA** powinien pozostać pusty, gdy jest uruchamiany zgodnie z harmonogramem.

    ![Konfigurowanie parametrów](./media/analysis-services-refresh-azure-automation/16.png)

5. Kliknij przycisk **OK**.

## <a name="consume-with-data-factory"></a>Korzystanie z Data Factory

Aby korzystać z elementu Runbook przy użyciu Azure Data Factory, należy najpierw utworzyć element **webhook** dla elementu Runbook. **Element webhook** poda adres URL, który można wywoływać za pomocą Azure Data Factory działania sieci Web.

> [!IMPORTANT]
> Aby utworzyć element **webhook**, należy **opublikować** stan elementu Runbook.

1. W elemencie Runbook usługi Automation kliknij pozycję elementy **webhook**, a następnie kliknij pozycję **Dodaj element webhook**.

   ![Dodaj element webhook](./media/analysis-services-refresh-azure-automation/17.png)

2. Nadaj elementowi webhook nazwę i wygaśnięcie.  Nazwa identyfikuje tylko element webhook wewnątrz elementu Runbook usługi Automation, ale nie jest częścią adresu URL.

   >[!CAUTION]
   >Upewnij się, że adres URL został skopiowany przed zamknięciem kreatora, ponieważ nie można go odzyskać po zamknięciu.
    
   ![Konfigurowanie elementu webhook](./media/analysis-services-refresh-azure-automation/18.png)

    Parametry elementu webhook mogą pozostać puste.  Podczas konfigurowania Azure Data Factory działania sieci Web parametry mogą być przesyłane do treści wywołania sieci Web.

3. W Data Factory Skonfiguruj **działanie sieci Web**

### <a name="example"></a>Przykład

   ![Przykład działania sieci Web](./media/analysis-services-refresh-azure-automation/19.png)

**Adres URL** jest adresem URL utworzonym na podstawie elementu webhook.

**Treść** jest dokumentem JSON, który powinien zawierać następujące właściwości:


|Właściwość  |Wartość  |
|---------|---------|
|**AnalysisServicesDatabase**     |Nazwa bazy danych Azure Analysis Services <br/> Przykład: AdventureWorksDB         |
|**AnalysisServicesServer**     |Nazwa serwera Azure Analysis Services. <br/> Przykład: https: \/ /westus.asazure.Windows.NET/Servers/MyServer/models/AdventureWorks/         |
|**DatabaseRefreshType**     |Typ odświeżania do wykonania. <br/> Przykład: pełne         |

Przykładowa treść JSON:

```json
{
    "AnalysisServicesDatabaseName": "AdventureWorksDB",
    "AnalysisServicesServer": "asazure://westeurope.asazure.windows.net/MyAnalysisServer",
    "DatabaseRefreshType": "Full"
}
```

Te parametry są zdefiniowane w skrypcie elementu Runbook programu PowerShell.  Gdy działanie sieci Web zostanie wykonane, przesłany ładunek JSON to WEBHOOKDATA.

Jest to deserializowane i przechowywane jako parametry programu PowerShell, które są następnie używane przez polecenie Invoke-ProcesASDatabase PowerShell.

![Deserializowany element webhook](./media/analysis-services-refresh-azure-automation/20.png)

## <a name="use-a-hybrid-worker-with-azure-analysis-services"></a>Użyj hybrydowego procesu roboczego z Azure Analysis Services

Maszyna wirtualna platformy Azure ze statycznym publicznym adresem IP może być używana jako Azure Automation hybrydowy proces roboczy.  Ten publiczny adres IP można następnie dodać do zapory Azure Analysis Services.

> [!IMPORTANT]
> Upewnij się, że publiczny adres IP maszyny wirtualnej jest skonfigurowany jako statyczny.
>
>Aby dowiedzieć się więcej o konfigurowaniu Azure Automation hybrydowych procesów roboczych, zobacz [Instalacja hybrydowego procesu roboczego elementu Runbook](../automation/automation-hybrid-runbook-worker.md#hybrid-runbook-worker-installation).

Po skonfigurowaniu hybrydowego procesu roboczego Utwórz element webhook zgodnie z opisem w sekcji [Korzystanie z Data Factory](#consume-with-data-factory).  Jedyną różnicą jest wybranie opcji **Uruchom przy** użyciu  >  **hybrydowego procesu roboczego** podczas konfigurowania elementu webhook.

Przykładowy element webhook z użyciem hybrydowego procesu roboczego:

![Przykładowy element webhook hybrydowego procesu roboczego](./media/analysis-services-refresh-azure-automation/21.png)

## <a name="sample-powershell-runbook"></a>Przykładowy element Runbook programu PowerShell

Poniższy fragment kodu przedstawia przykład sposobu przeprowadzenia odświeżenia modelu Azure Analysis Services za pomocą elementu Runbook programu PowerShell.

```powershell
param
(
    [Parameter (Mandatory = $false)]
    [object] $WebhookData,

    [Parameter (Mandatory = $false)]
    [String] $DatabaseName,
    [Parameter (Mandatory = $false)]
    [String] $AnalysisServer,
    [Parameter (Mandatory = $false)]
    [String] $RefreshType
)

$_Credential = Get-AutomationPSCredential -Name "ServicePrincipal"

# If runbook was called from Webhook, WebhookData will not be null.
if ($WebhookData)
{ 
    # Retrieve AAS details from Webhook request body
    $atmParameters = (ConvertFrom-Json -InputObject $WebhookData.RequestBody)
    Write-Output "CredentialName: $($atmParameters.CredentialName)"
    Write-Output "AnalysisServicesDatabaseName: $($atmParameters.AnalysisServicesDatabaseName)"
    Write-Output "AnalysisServicesServer: $($atmParameters.AnalysisServicesServer)"
    Write-Output "DatabaseRefreshType: $($atmParameters.DatabaseRefreshType)"
    
    $_databaseName = $atmParameters.AnalysisServicesDatabaseName
    $_analysisServer = $atmParameters.AnalysisServicesServer
    $_refreshType = $atmParameters.DatabaseRefreshType
 
    Invoke-ProcessASDatabase -DatabaseName $_databaseName -RefreshType $_refreshType -Server $_analysisServer -ServicePrincipal -Credential $_credential
}
else 
{
    Invoke-ProcessASDatabase -DatabaseName $DatabaseName -RefreshType $RefreshType -Server $AnalysisServer -ServicePrincipal -Credential $_Credential
}
```


## <a name="next-steps"></a>Następne kroki

[Samples](analysis-services-samples.md)  
[Interfejs API REST](/rest/api/analysisservices/servers)