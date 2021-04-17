---
title: Wdrażanie zasad niestandardowych za pomocą Azure Pipelines
titleSuffix: Azure AD B2C
description: Dowiedz się, jak Azure AD B2C zasad niestandardowych w potoku ci/CD przy użyciu Azure Pipelines w Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 913f21b90043209cae1ec9963619389bcb452781
ms.sourcegitcommit: 49b2069d9bcee4ee7dd77b9f1791588fe2a23937
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2021
ms.locfileid: "107529433"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Wdrażanie zasad niestandardowych za pomocą Azure Pipelines

Za pomocą potoku ciągłej integracji/ciągłego dostarczania (CI/CD) ustawionego w programie Azure Pipelines można uwzględnić zasady niestandardowe usługi [Azure AD B2C][devops-pipelines]w automatyzacji dostarczania oprogramowania i sterowania kodem. W przypadku wdrażania w różnych środowiskach Azure AD B2C, na przykład w środowiskach deweloperskich, testowych i produkcyjnych, zalecamy usunięcie procesów ręcznych i przeprowadzenie testów automatycznych przy użyciu Azure Pipelines.

Istnieją trzy podstawowe kroki wymagane do włączenia Azure Pipelines zarządzania zasadami niestandardowymi w ramach Azure AD B2C:

1. Tworzenie rejestracji aplikacji internetowej w dzierżawie Azure AD B2C dzierżawy
1. Konfigurowanie usługi Azure Repo
1. Konfigurowanie potoku platformy Azure

> [!IMPORTANT]
> Zarządzanie Azure AD B2C zasadami niestandardowymi za pomocą usługi Azure Pipeline aktualnie używa operacji w wersji **zapoznawczej** dostępnych w punkcie końcowym Microsoft Graph `/beta` API. Te interfejsy API nie są obsługiwane w zastosowaniach produkcyjnych. Aby uzyskać więcej informacji, zobacz Microsoft Graph api REST beta endpoint reference (Dokumentacja [punktu końcowego interfejsu API REST w wersji beta).](/graph/api/overview?toc=.%2fref%2ftoc.json&view=graph-rest-beta&preserve-view=true)

## <a name="prerequisites"></a>Wymagania wstępne

* [Azure AD B2C dzierżawy](tutorial-create-tenant.md)i poświadczeń dla użytkownika w katalogu z rolą [administratora zasad IEF B2C](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Zasady niestandardowe przekazane](tutorial-create-user-flows.md?pivots=b2c-custom-policy) do dzierżawy
* [Aplikacja do zarządzania](microsoft-graph-get-started.md) zarejestrowana w dzierżawie przy użyciu uprawnień Microsoft Graph API *Policy.ReadWrite.TrustFramework*
* [Azure Pipeline](https://azure.microsoft.com/services/devops/pipelines/)i dostęp do Azure DevOps Services [projektu][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Przepływ udzielania poświadczeń klienta

W scenariuszu opisanym w tym miejscu są używane wywołania między usługami Azure Pipelines i Azure AD B2C przy [](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)użyciu przepływu udzielania poświadczeń klienta OAuth 2.0. Ten przepływ udzielania zezwala usłudze internetowej, takiej jak Azure Pipelines (klient poufny), na używanie własnych poświadczeń zamiast personifikacji użytkownika w celu uwierzytelnienia podczas wywoływania innej usługi internetowej (w tym przypadku interfejsu API usługi Microsoft Graph). Azure Pipelines uzyskuje token w sposób nieinterakcyjny, a następnie wykonuje żądania do Microsoft Graph API.

## <a name="register-an-application-for-management-tasks"></a>Rejestrowanie aplikacji do zadań zarządzania

Jak wspomniano [](#prerequisites)w wymaganiach wstępnych, potrzebna jest rejestracja aplikacji, która jest wykonywana przez skrypty programu PowerShell Azure Pipelines do uzyskiwania dostępu do zasobów w dzierżawie.

Jeśli masz już rejestrację aplikacji używaną do zadań automatyzacji, upewnij się, że przyznano jej uprawnienie **Microsoft Graph**  >    >  **Policy.ReadWrite.TrustFramework** w ramach uprawnień interfejsu **API** rejestracji aplikacji.

Aby uzyskać instrukcje dotyczące rejestrowania aplikacji do zarządzania, zobacz [Zarządzanie Azure AD B2C pomocą Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Konfigurowanie usługi Azure Repo

Po zarejestrowaniu aplikacji do zarządzania możesz skonfigurować repozytorium dla plików zasad.

1. Zaloguj się do swojej Azure DevOps Services organizacji.
1. [Utwórz nowy projekt lub][devops-create-project] wybierz istniejący projekt.
1. W projekcie przejdź do opcji **Repos** i wybierz **stronę** Pliki. Wybierz istniejące repozytorium lub utwórz je na to ćwiczenie.
1. Utwórz folder o *nazwie B2CAssets*. Nazwij wymagany plik zastępczy *README.md* **i Zateń** plik. Jeśli chcesz, możesz usunąć ten plik później.
1. Dodaj pliki Azure AD B2C do folderu *B2CAssets.* Dotyczy to *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml,* *SignUpOrSignin.xml,* *ProfileEdit.xml,* *PasswordReset.xml* i innych utworzonych zasad. Zanotuj nazwę każdego pliku zasad Azure AD B2C do użycia w późniejszym kroku (są one używane jako argumenty skryptu programu PowerShell).
1. Utwórz folder o nazwie *Scripts* (Skrypty) w katalogu głównym repozytorium, nadaj plikowi zastępczego *nazwęDeployToB2c.ps1*. Nie zatwierdzaj pliku na tym etapie. Zrobisz to w późniejszym kroku.
1. Wklej następujący skrypt programu PowerShell do *DeployToB2c.ps1*, a następnie **zatnij** plik. Skrypt uzyskuje token z usługi Azure AD i wywołuje interfejs API usługi Microsoft Graph w celu przekazania zasad w folderze *B2CAssets* do dzierżawy Azure AD B2C dzierżawy.

    ```PowerShell
    [Cmdletbinding()]
    Param(
        [Parameter(Mandatory = $true)][string]$ClientID,
        [Parameter(Mandatory = $true)][string]$ClientSecret,
        [Parameter(Mandatory = $true)][string]$TenantId,
        [Parameter(Mandatory = $true)][string]$PolicyId,
        [Parameter(Mandatory = $true)][string]$PathToFile
    )

    try {
        $body = @{grant_type = "client_credentials"; scope = "https://graph.microsoft.com/.default"; client_id = $ClientID; client_secret = $ClientSecret }

        $response = Invoke-RestMethod -Uri https://login.microsoftonline.com/$TenantId/oauth2/v2.0/token -Method Post -Body $body
        $token = $response.access_token

        $headers = New-Object "System.Collections.Generic.Dictionary[[String],[String]]"
        $headers.Add("Content-Type", 'application/xml')
        $headers.Add("Authorization", 'Bearer ' + $token)

        $graphuri = 'https://graph.microsoft.com/beta/trustframework/policies/' + $PolicyId + '/$value'
        $policycontent = Get-Content $PathToFile
        $response = Invoke-RestMethod -Uri $graphuri -Method Put -Body $policycontent -Headers $headers

        Write-Host "Policy" $PolicyId "uploaded successfully."
    }
    catch {
        Write-Host "StatusCode:" $_.Exception.Response.StatusCode.value__

        $_

        $streamReader = [System.IO.StreamReader]::new($_.Exception.Response.GetResponseStream())
        $streamReader.BaseStream.Position = 0
        $streamReader.DiscardBufferedData()
        $errResp = $streamReader.ReadToEnd()
        $streamReader.Close()

        $ErrResp

        exit 1
    }

    exit 0
    ```

## <a name="configure-your-azure-pipeline"></a>Konfigurowanie potoku platformy Azure

Po zainicjowaniu repozytorium i wypełnieniu go niestandardowymi plikami zasad możesz skonfigurować potok wydania.

### <a name="create-pipeline"></a>Tworzenie potoku

1. Zaloguj się do swojej Azure DevOps Services i przejdź do projektu.
1. W projekcie wybierz pozycję **Potoki**  >  **Wydania Nowy**  >  **potok.**
1. W **obszarze Wybierz szablon wybierz** pozycję Puste **zadanie.**
1. Wprowadź nazwę **etapu,** na przykład *DeployCustomPolicies,* a następnie zamknij okienko.
1. Wybierz **pozycję Add an artifact (Dodaj** artefakt), a następnie w obszarze Source type **(Typ źródła)** **wybierz pozycję Azure Repository (Repozytorium Azure).**
    1. Wybierz repozytorium źródłowe zawierające folder *Skrypty* wypełniony skryptem programu PowerShell.
    1. Wybierz **gałąź domyślną**. Jeśli w poprzedniej sekcji utworzono nowe repozytorium, gałąź domyślna jest *gałęzią master.*
    1. Pozostaw **ustawienie Wersja domyślna** *w opcji Najnowsza z gałęzi domyślnej*.
    1. Wprowadź **alias źródła** dla repozytorium. Na przykład *policyRepo*. Nie należy uwzględniać żadnych spacji w nazwie aliasu.
1. Wybierz pozycję **Dodaj**.
1. Zmień nazwę potoku, aby odzwierciedlić jego intencję. Na przykład wdrażanie *potoku zasad niestandardowych*.
1. Wybierz **pozycję Zapisz,** aby zapisać konfigurację potoku.

### <a name="configure-pipeline-variables"></a>Konfigurowanie zmiennych potoku

1. Wybierz **kartę Zmienne.**
1. Dodaj następujące zmienne w obszarze **Zmienne potoku** i ustaw ich wartości zgodnie z podanymi wartościami:

    | Nazwa | Wartość |
    | ---- | ----- |
    | `clientId` | **Identyfikator aplikacji (klienta)** zarejestrowanej wcześniej aplikacji. |
    | `clientSecret` | Wartość utworzonego **wcześniej tajnego** klienta. <br /> Zmień typ zmiennej na **wpis tajny** (wybierz ikonę kłódki). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, *gdzie your-b2c-tenant* to nazwa dzierżawy Azure AD B2C dzierżawy. |

1. Wybierz **pozycję Zapisz,** aby zapisać zmienne.

### <a name="add-pipeline-tasks"></a>Dodawanie zadań potoku

Następnie dodaj zadanie w celu wdrożenia pliku zasad.

1. Wybierz **kartę** Zadania.
1. Wybierz **pozycję Zadanie** agenta , a następnie wybierz znak plus ( ), aby dodać zadanie do zadania **+** Agent.
1. Wyszukaj i wybierz pozycję **PowerShell.** Nie wybieraj pozycji "Azure PowerShell", "PowerShell na maszynach docelowych" ani innego wpisu programu PowerShell.
1. Wybierz nowo dodane **zadanie Skrypt programu PowerShell.**
1. Wprowadź następujące wartości dla zadania Skrypt programu PowerShell:
    * **Wersja zadania:** 2.*
    * **Nazwa wyświetlana:** nazwa zasad, które powinny zostać przesłane przez to zadanie. Na przykład *B2C_1A_TrustFrameworkBase*.
    * **Typ:** Ścieżka pliku
    * **Ścieżka skryptu:** wybierz wielokropek (**_..._* _), przejdź do folderu _Scripts*, a następnie wybierz *DeployToB2C.ps1* plik.
    * **Argumenty:**

        Wprowadź następujące wartości dla **argumentów**. Zastąp `{alias-name}` wartość aliasem określonym w poprzedniej sekcji.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Jeśli na przykład określony alias to *policyRepo,* wiersz argumentu powinien być:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Wybierz **pozycję Zapisz,** aby zapisać zadanie Agent.

Właśnie dodane zadanie przekaże *jeden plik* zasad do Azure AD B2C. Przed przystąpieniem do pracy ręcznie wyzwolij zadanie **(Utwórz** wydanie), aby upewnić się, że zostało ono ukończone pomyślnie przed utworzeniem dodatkowych zadań.

Jeśli zadanie zostanie pomyślnie ukończone, dodaj zadania wdrażania, wykonując powyższe kroki dla każdego z niestandardowych plików zasad. `-PolicyId` `-PathToFile` Zmodyfikuj wartości argumentów i dla każdej zasady.

To wartość znaleziona na początku pliku `PolicyId` zasad XML w węźle TrustFrameworkPolicy. Na przykład w `PolicyId` poniższym pliku XML zasad jest *B2C_1A_TrustFrameworkBase*:

```xml
<TrustFrameworkPolicy
xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
xmlns:xsd="http://www.w3.org/2001/XMLSchema"
xmlns="http://schemas.microsoft.com/online/cpim/schemas/2013/06"
PolicySchemaVersion="0.3.0.0"
TenantId="contoso.onmicrosoft.com"
PolicyId= "B2C_1A_TrustFrameworkBase"
PublicPolicyUri="http://contoso.onmicrosoft.com/B2C_1A_TrustFrameworkBase">
```

Podczas uruchamiania agentów i przekazywania plików zasad upewnij się, że zostały one przekazane w tej kolejności:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Struktura Identity Experience Framework wymusza tę kolejność, ponieważ struktura plików jest zbudowana na łańcuchu hierarchicznym.

## <a name="test-your-pipeline"></a>Testowanie potoku

Aby przetestować potok wydania:

1. Wybierz **pozycję Potoki,** a następnie **pozycję Wydania.**
1. Wybierz utworzony wcześniej potok, na przykład *DeployCustomPolicies.*
1. Wybierz **pozycję Utwórz wydanie,** a następnie wybierz **pozycję Utwórz,** aby kolejkować wydanie.

Powinien zostać wyświetlony transparent powiadomienia z komunikatem, że wydanie zostało w kolejce. Aby wyświetlić jego stan, wybierz link na banerze powiadomienia lub wybierz go na liście na **karcie Wydania.**

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na następujące tematy:

* [Wywołania typu usługa-usługa przy użyciu poświadczeń klienta](../active-directory/azuread-dev/v1-oauth2-client-creds-grant-flow.md)
* [Usługa Azure DevOps Services](/azure/devops/user-guide/)

<!-- LINKS - External -->
[devops]: /azure/devops/
[devops-create-project]:  /azure/devops/organizations/projects/create-project
[devops-pipelines]: /azure/devops/pipelines
