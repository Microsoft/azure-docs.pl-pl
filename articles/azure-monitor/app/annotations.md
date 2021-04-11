---
title: Adnotacje wydania dla Application Insights | Microsoft Docs
description: Dodaj znaczniki wdrożenia lub kompilacji do wykresów Eksploratora metryk w Application Insights.
ms.topic: conceptual
ms.date: 08/14/2020
ms.openlocfilehash: 9132e65e4705fd9125d97a5e095fe5f0850229a2
ms.sourcegitcommit: 6ed3928efe4734513bad388737dd6d27c4c602fd
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/07/2021
ms.locfileid: "107011054"
---
# <a name="annotations-on-metric-charts-in-application-insights"></a>Adnotacje na wykresach metryk w Application Insights

Adnotacje pokazują, gdzie wdrożono nową kompilację lub inne istotne zdarzenia. Adnotacje ułatwiają sprawdzenie, czy zmiany miały wpływ na wydajność aplikacji. Mogą być tworzone automatycznie przez system kompilacji [Azure Pipelines](/azure/devops/pipelines/tasks/) . Możesz również utworzyć adnotacje, aby oflagować dowolne wydarzenie, tworząc je za pomocą programu PowerShell.

## <a name="release-annotations-with-azure-pipelines-build"></a>Adnotacje wersji z kompilacją Azure Pipelines

Adnotacje wydań to funkcja Azure Pipelines opartej na chmurze usługi Azure DevOps.

### <a name="install-the-annotations-extension-one-time"></a>Zainstaluj rozszerzenie adnotacji (jednorazowo)

Aby można było tworzyć adnotacje wersji, należy zainstalować jeden z wielu rozszerzeń platformy Azure DevOps dostępnych w Visual Studio Marketplace.

1. Zaloguj się do projektu [usługi Azure DevOps](https://azure.microsoft.com/services/devops/) .
   
1. Na stronie [rozszerzenia Visual Studio Marketplace wersje adnotacji](https://marketplace.visualstudio.com/items/ms-appinsights.appinsightsreleaseannotations) wybierz organizację usługi Azure DevOps, a następnie wybierz pozycję **Zainstaluj** , aby dodać rozszerzenie do organizacji usługi Azure DevOps.
   
   ![Wybierz organizację usługi Azure DevOps, a następnie wybierz pozycję Zainstaluj.](./media/annotations/1-install.png)
   
Wystarczy zainstalować rozszerzenie tylko raz dla organizacji usługi Azure DevOps. Teraz można skonfigurować adnotacje wydania dla dowolnego projektu w organizacji.

### <a name="configure-release-annotations"></a>Konfigurowanie adnotacji wersji

Utwórz oddzielny klucz interfejsu API dla każdego z Azure Pipelines szablonów wydań.

1. Zaloguj się do [Azure Portal](https://portal.azure.com) i otwórz zasób Application Insights, który monitoruje aplikację. Lub jeśli go nie masz, [Utwórz nowy zasób Application Insights](./app-insights-overview.md).
   
1. Otwórz kartę **dostęp do interfejsu API** i skopiuj **Identyfikator Application Insights**.
   
   ![W obszarze dostęp do interfejsu API Skopiuj identyfikator aplikacji.](./media/annotations/2-app-id.png)

1. W osobnym oknie przeglądarki Otwórz lub Utwórz szablon zlecenia, który zarządza wdrożeniami Azure Pipelines.
   
1. Wybierz pozycję **Dodaj zadanie**, a następnie w menu wybierz zadanie **adnotacji Application Insights wydania** .
   
   ![Wybierz pozycję Dodaj zadanie i wybierz pozycję Application Insights adnotacja wydania.](./media/annotations/3-add-task.png)

   > [!NOTE]
   > Zadanie adnotacji wydania obecnie obsługuje tylko agentów opartych na systemie Windows; nie będzie działać w systemach Linux, macOS i innych typach agentów.
   
1. W obszarze **Identyfikator aplikacji** wklej identyfikator Application Insights skopiowany z karty dostęp do **interfejsu API** .
   
   ![Wklej identyfikator Application Insights](./media/annotations/4-paste-app-id.png)
   
1. Wróć do okna Application Insights **dostęp do interfejsu API** wybierz pozycję **Utwórz klucz interfejsu API**. 
   
   ![Na karcie dostęp do interfejsu API wybierz pozycję Utwórz klucz interfejsu API.](./media/annotations/5-create-api-key.png)
   
1. W oknie **Tworzenie klucza interfejsu API** wpisz opis, wybierz pozycję **Zapisz adnotacje**, a następnie wybierz pozycję **Generuj klucz**. Skopiuj nowy klucz.
   
   ![W oknie Tworzenie klucza interfejsu API wpisz opis, wybierz pozycję Zapisz adnotacje, a następnie wybierz pozycję Generuj klucz.](./media/annotations/6-create-api-key.png)
   
1. W oknie szablon zlecenia na karcie **zmienne** wybierz pozycję **Dodaj** , aby utworzyć definicję zmiennej dla nowego klucza interfejsu API.

1. W polu **Nazwa** wprowadź `ApiKey` i w obszarze **wartość** Wklej klucz interfejsu API skopiowany z karty dostęp do **interfejsu API** .
   
   ![Na karcie zmienne usługi Azure DevOps wybierz pozycję Dodaj, nazwij zmienną ApiKey i Wklej klucz interfejsu API w polu wartość.](./media/annotations/7-paste-api-key.png)
   
1. Wybierz pozycję **Zapisz** w oknie głównym szablonu zlecenia, aby zapisać szablon.


   > [!NOTE]
   > Limity dla kluczy interfejsu API są opisane w [dokumentacji limitów szybkości interfejsu API REST](https://dev.applicationinsights.io/documentation/Authorization/Rate-limits).

## <a name="view-annotations"></a>Wyświetl adnotacje


   > [!NOTE]
   > Adnotacje wersji nie są obecnie dostępne w okienku metryki Application Insights

Teraz za każdym razem, gdy użyjesz szablonu zlecenia do wdrożenia nowej wersji, adnotacja zostanie wysłana do Application Insights. Adnotacje można przeglądać w następujących lokalizacjach:

Okienko **użycie** , w którym można także ręcznie tworzyć adnotacje wersji:

![Zrzut ekranu przedstawiający wykres słupkowy z liczbą wizyt użytkowników wyświetlaną w danym okresie czasu. Adnotacje wersji są wyświetlane jako zielone znaczniki wyboru powyżej wykresu wskazujące moment, w którym wystąpiło wydanie](./media/annotations/usage-pane.png)

W każdym zapytaniu w skoroszycie opartym na dzienniku, gdzie wizualizacja wyświetla czas wzdłuż osi x.

![Zrzut ekranu przedstawiający okienko skoroszytów z kwerendą opartą na dzienniku szeregów czasowych z wyświetlanymi adnotacjami](./media/annotations/workbooks-annotations.png)

Aby włączyć adnotacje w skoroszycie, przejdź do pozycji **Ustawienia zaawansowane** i wybierz pozycję **Pokaż adnotacje**.

![Zrzut ekranu przedstawiający menu Ustawienia zaawansowane z wyrazami Pokaż adnotacje z wyróżnionym znacznikiem wyboru obok ustawienia, aby je włączyć.](./media/annotations/workbook-show-annotations.png)

Wybierz dowolny znacznik adnotacji, aby otworzyć szczegółowe informacje o wydaniu, w tym żądający, gałąź kontroli źródła, potok wersji i środowisko.

## <a name="create-custom-annotations-from-powershell"></a>Tworzenie adnotacji niestandardowych w programie PowerShell
Za pomocą skryptu programu PowerShell CreateReleaseAnnotation można tworzyć adnotacje z dowolnego procesu, który ma być używany, bez korzystania z usługi Azure DevOps.

> [!IMPORTANT]
> Jeśli używasz programu PowerShell 7,1, Dodaj `-SkipHttpErrorCheck` na końcu wiersza 26. Na przykład: `$request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore -SkipHttpErrorCheck`.

1. Wykonaj kopię lokalną CreateReleaseAnnotation.ps1:

    ```powershell
    
    # Copyright (c) Microsoft Corporation. All rights reserved. 
    # Licensed under the MIT License. See License.txt in the project root for license information. 
    
    # Sample usage .\CreateReleaseAnnotation.ps1 -applicationId "<appId>" -apiKey "<apiKey>" -releaseFilePath "<path to .exe with file version>" -releaseProperties @{"ReleaseDescription"="Release with annotation";"TriggerBy"="John Doe"}
    param(
        [parameter(Mandatory = $true)][string]$applicationId,
        [parameter(Mandatory = $true)][string]$apiKey,
        [parameter(Mandatory = $true)][string]$releaseFilePath,
        [parameter(Mandatory = $false)]$releaseProperties
    )
    
    $releaseName = (Get-Item $releaseFilePath).VersionInfo.FileVersion
    Write-Host "Creating release annotation $releaseName in ApplicationInsights" -ForegroundColor Cyan
    
    # background info on how fwlink works: After you submit a web request, many sites redirect through a series of intermediate pages before you finally land on the destination page.
    # So when calling Invoke-WebRequest, the result it returns comes from the final page in any redirect sequence. Hence, I set MaximumRedirection to 0, as this prevents the call to 
    # be redirected. By doing this, we get a response with status code 302, which indicates that there is a redirection link from the response body. We grab this redirection link and 
    # construct the url to make a release annotation.
    # Here's how this logic is going to works
    # 1. Client send http request, such as:  http://go.microsoft.com/fwlink/?LinkId=625115
    # 2. FWLink get the request and find out the destination URL for it, such as:  http://www.bing.com
    # 3. FWLink generate a new http response with status code “302” and with destination URL “http://www.bing.com”. Send it back to Client.
    # 4. Client, such as a powershell script, knows that status code “302” means redirection to new a location, and the target location is “http://www.bing.com”
    function GetRequestUrlFromFwLink($fwLink)
    {
        $request = Invoke-WebRequest -Uri $fwLink -MaximumRedirection 0 -UseBasicParsing -ErrorAction Ignore
        if ($request.StatusCode -eq "302") {
            return $request.Headers.Location
        }
        
        return $null
    }
    
    function CreateAnnotation($grpEnv)
    {
        $retries = 1
        $success = $false
        while (!$success -and $retries -lt 6) {
            $location = "$grpEnv/applications/$applicationId/Annotations?api-version=2015-11"
                
            Write-Host "Invoke a web request for $location to create a new release annotation. Attempting $retries"
            set-variable -Name createResultStatus -Force -Scope Local -Value $null
            set-variable -Name createResultStatusDescription -Force -Scope Local -Value $null
            set-variable -Name result -Force -Scope Local
    
            try {
                $result = Invoke-WebRequest -Uri $location -Method Put -Body $bodyJson -Headers $headers -ContentType "application/json; charset=utf-8" -UseBasicParsing
            } catch {
                if ($_.Exception){
                    if($_.Exception.Response) {
                        $createResultStatus = $_.Exception.Response.StatusCode.value__
                        $createResultStatusDescription = $_.Exception.Response.StatusDescription
                    }
                    else {
                        $createResultStatus = "Exception"
                        $createResultStatusDescription = $_.Exception.Message
                    }
                }
            }
    
            if ($result -eq $null) {
                if ($createResultStatus -eq $null) {
                    $createResultStatus = "Unknown"
                }
                if ($createResultStatusDescription -eq $null) {
                    $createResultStatusDescription = "Unknown"
                }
            }
            else {
                    $success = $true                     
            }
    
            if ($createResultStatus -eq 409 -or $createResultStatus -eq 404 -or $createResultStatus -eq 401) # no retry when conflict or unauthorized or not found
            {
                break
            }
    
            $retries = $retries + 1
            sleep 1
        }
    
        $createResultStatus
        $createResultStatusDescription
        return
    }
    
    # Need powershell version 3 or greater for script to run
    $minimumPowershellMajorVersion = 3
    if ($PSVersionTable.PSVersion.Major -lt $minimumPowershellMajorVersion) {
       Write-Host "Need powershell version $minimumPowershellMajorVersion or greater to create release annotation"
       return
    }
    
    $currentTime = (Get-Date).ToUniversalTime()
    $annotationDate = $currentTime.ToString("MMddyyyy_HHmmss")
    set-variable -Name requestBody -Force -Scope Script
    $requestBody = @{}
    $requestBody.Id = [GUID]::NewGuid()
    $requestBody.AnnotationName = $releaseName
    $requestBody.EventTime = $currentTime.GetDateTimeFormats("s")[0] # GetDateTimeFormats returns an array
    $requestBody.Category = "Deployment"
    
    if ($releaseProperties -eq $null) {
        $properties = @{}
    } else {
        $properties = $releaseProperties    
    }
    $properties.Add("ReleaseName", $releaseName)
    
    $requestBody.Properties = ConvertTo-Json($properties) -Compress
    
    $bodyJson = [System.Text.Encoding]::UTF8.GetBytes(($requestBody | ConvertTo-Json))
    $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
    $headers.Add("X-AIAPIKEY", $apiKey)
    
    set-variable -Name createAnnotationResult1 -Force -Scope Local -Value $null
    set-variable -Name createAnnotationResultDescription -Force -Scope Local -Value ""
    
    # get redirect link from fwlink
    $requestUrl = GetRequestUrlFromFwLink("http://go.microsoft.com/fwlink/?prd=11901&pver=1.0&sbp=Application%20Insights&plcid=0x409&clcid=0x409&ar=Annotations&sar=Create%20Annotation")
    if ($requestUrl -eq $null) {
        $output = "Failed to find the redirect link to create a release annotation"
        throw $output
    }
    
    $createAnnotationResult1, $createAnnotationResultDescription = CreateAnnotation($requestUrl)
    if ($createAnnotationResult1) 
    {
         $output = "Failed to create an annotation with Id: {0}. Error {1}, Description: {2}." -f $requestBody.Id, $createAnnotationResult1, $createAnnotationResultDescription
         throw $output
    }
    
    $str = "Release annotation created. Id: {0}." -f $requestBody.Id
    Write-Host $str -ForegroundColor Green
    
    ```
   
1. Wykonaj kroki opisane w poprzedniej procedurze, aby pobrać identyfikator Application Insights i utworzyć klucz interfejsu API z karty Application Insights **dostęp do interfejsu API** .
   
1. Wywołaj skrypt programu PowerShell przy użyciu następującego kodu, zastępując symbole zastępcze w nawiasach kwadratowych wartościami. `-releaseProperties`Są opcjonalne. 
   
   ```powershell
   
        .\CreateReleaseAnnotation.ps1 `
         -applicationId "<applicationId>" `
         -apiKey "<apiKey>" `
         -releaseName "<releaseName>" `
         -releaseProperties @{
             "ReleaseDescription"="<a description>";
             "TriggerBy"="<Your name>" }
   ```

Możesz zmodyfikować skrypt, na przykład aby utworzyć adnotacje dla przeszłości.


## <a name="next-steps"></a>Następne kroki

* [Tworzenie elementów roboczych](./diagnostic-search.md#create-work-item)
* [Automatyzacja przy użyciu programu PowerShell](./powershell.md)
