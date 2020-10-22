---
title: Wdrażanie zasad niestandardowych przy użyciu Azure Pipelines
titleSuffix: Azure AD B2C
description: Informacje na temat wdrażania zasad niestandardowych Azure AD B2C w potoku ciągłej integracji/ciągłego dostarczania przy użyciu Azure Pipelines w Azure DevOps Services.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: 0dba5f96d90304418d7ebd297419c1f36244f868
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/22/2020
ms.locfileid: "92363933"
---
# <a name="deploy-custom-policies-with-azure-pipelines"></a>Wdrażanie zasad niestandardowych przy użyciu Azure Pipelines

Za pomocą potoku ciągłej integracji i dostarczania (CI/CD), który został skonfigurowany w [Azure Pipelines][devops-pipelines], można uwzględnić zasady niestandardowe Azure AD B2C do dostarczania oprogramowania i automatyzacji kontroli kodu. Podczas wdrażania do różnych środowisk Azure AD B2C, na przykład dev, test i Production, zalecamy usunięcie ręcznych procesów i przeprowadzenie testowania automatycznego przy użyciu Azure Pipelines.

Aby umożliwić Azure Pipelines Zarządzanie zasadami niestandardowymi w ramach usługi Azure AD B2C, należy wykonać trzy podstawowe czynności:

1. Tworzenie rejestracji aplikacji sieci Web w dzierżawie Azure AD B2C
1. Konfigurowanie repozytorium platformy Azure
1. Konfigurowanie potoku platformy Azure

> [!IMPORTANT]
> Zarządzanie zasadami niestandardowymi Azure AD B2C przy użyciu potoku platformy Azure obecnie używa operacji w **wersji zapoznawczej** dostępnych w `/beta` punkcie końcowym interfejsu API Microsoft Graph. Te interfejsy API nie są obsługiwane w zastosowaniach produkcyjnych. Aby uzyskać więcej informacji, zobacz [Informacje o punkcie końcowym interfejsu API REST](https://docs.microsoft.com/graph/api/overview?toc=./ref/toc.json&view=graph-rest-beta)w usłudze Microsoft Graph.

## <a name="prerequisites"></a>Wymagania wstępne

* [Azure AD B2C dzierżawy](tutorial-create-tenant.md)i poświadczenia dla użytkownika w katalogu z rolą [administratora zasad B2C IEF](../active-directory/roles/permissions-reference.md#b2c-ief-policy-administrator)
* [Zasady niestandardowe](custom-policy-get-started.md) przekazane do dzierżawy
* [Aplikacja zarządzania](microsoft-graph-get-started.md) zarejestrowana w dzierżawie przy użyciu zasad uprawnień interfejsu API Microsoft Graph *. ReadWrite. TrustFramework*
* [Potok platformy Azure](https://azure.microsoft.com/services/devops/pipelines/)i dostęp do [projektu Azure DevOps Services][devops-create-project]

## <a name="client-credentials-grant-flow"></a>Przepływ przyznania poświadczeń klienta

W tym scenariuszu opisano sposób korzystania z wywołań między usługami Azure Pipelines i Azure AD B2C za pomocą [przepływu przydzielenia poświadczeń klienta](../active-directory/develop/v1-oauth2-client-creds-grant-flow.md)OAuth 2,0. Ten przepływ grantu zezwala usłudze sieci Web, takiej jak Azure Pipelines (klient poufny) na używanie własnych poświadczeń zamiast personifikowania użytkownika w celu uwierzytelnienia podczas wywoływania innej usługi sieci Web (w tym przypadku interfejsu API Microsoft Graph). Azure Pipelines uzyskuje token nieinteraktywnie, a następnie wysyła żądania do interfejsu API Microsoft Graph.

## <a name="register-an-application-for-management-tasks"></a>Rejestrowanie aplikacji na potrzeby zadań zarządzania

Jak wspomniano w [wymaganiach wstępnych](#prerequisites), potrzebna jest rejestracja aplikacji, którą skrypty programu PowerShell — wykonywane przez Azure Pipelines — mogą używać do uzyskiwania dostępu do zasobów w dzierżawie.

Jeśli masz już rejestrację aplikacji używaną na potrzeby zadań automatyzacji, upewnij się, że udzielono uprawnienia **Microsoft Graph**  >  **Policy**  >  **Policy zasad. ReadWrite. TrustFramework** w ramach **uprawnień interfejsu API** rejestracji aplikacji.

Instrukcje dotyczące rejestrowania aplikacji zarządzania programu znajdują się w temacie [zarządzanie Azure AD B2C przy użyciu Microsoft Graph](microsoft-graph-get-started.md).

## <a name="configure-an-azure-repo"></a>Konfigurowanie repozytorium platformy Azure

Po zarejestrowaniu aplikacji zarządzania można skonfigurować repozytorium dla plików zasad.

1. Zaloguj się do swojej organizacji Azure DevOps Services.
1. [Utwórz nowy projekt][devops-create-project] lub wybierz istniejący projekt.
1. W projekcie przejdź do **repozytoriów** i wybierz stronę **pliki** . Wybierz istniejące repozytorium lub utwórz je dla tego ćwiczenia.
1. Utwórz folder o nazwie *B2CAssets*. Nazwij wymagany plik zastępczy *README.MD* i **Zatwierdź** plik. Możesz usunąć ten plik później, jeśli chcesz.
1. Dodaj pliki zasad Azure AD B2C do folderu *B2CAssets* . Obejmuje to *TrustFrameworkBase.xml*, *TrustFrameWorkExtensions.xml*, *SignUpOrSignin.xml*, *ProfileEdit.xml*, *PasswordReset.xml*i innych utworzonych zasad. Zapisz nazwę pliku Azure AD B2C zasad do użycia w późniejszym kroku (są one używane jako argumenty skryptu programu PowerShell).
1. Utwórz folder o nazwie *scripts* w katalogu głównym repozytorium, Nazwij plik symbolu zastępczego *DeployToB2c.ps1*. Nie zatwierdzaj pliku w tym momencie, należy to zrobić w późniejszym kroku.
1. Wklej następujący skrypt programu PowerShell do *DeployToB2c.ps1*, a następnie **Zatwierdź** plik. Skrypt uzyskuje token z usługi Azure AD i wywołuje interfejs API Microsoft Graph, aby przekazać zasady w folderze *B2CAssets* do dzierżawy usługi Azure AD B2C.

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

Gdy Twoje repozytorium zostało zainicjowane i uzupełnione o niestandardowe pliki zasad, możesz rozpocząć konfigurowanie potoku wydania.

### <a name="create-pipeline"></a>Tworzenie potoku

1. Zaloguj się do organizacji Azure DevOps Services i przejdź do projektu.
1. W projekcie wybierz pozycję **potoki**  >  **zwalnia**  >  **Nowy potok**.
1. W obszarze **Wybierz szablon**wybierz pozycję **puste zadanie**.
1. Wprowadź **nazwę etapu**, na przykład *DeployCustomPolicies*, a następnie zamknij okienko.
1. Wybierz pozycję **Dodaj artefakt**i w obszarze **Typ źródła**wybierz pozycję **Azure Repository**.
    1. Wybierz repozytorium źródłowe zawierające folder *skryptów* , który został wypełniony skryptem programu PowerShell.
    1. Wybierz **gałąź domyślną**. Jeśli utworzono nowe repozytorium w poprzedniej sekcji, gałąź domyślna to *Master*.
    1. Pozostaw **domyślne ustawienie wersji** *najnowsze z gałęzi domyślnej*.
    1. Wprowadź **alias źródła** dla repozytorium. Na przykład *policyRepo*. Nazwa aliasu nie zawiera żadnych spacji.
1. Wybierz pozycję **Dodaj**
1. Zmień nazwę potoku, aby odzwierciedlić jego zamiar. Na przykład *Wdróż potok zasad niestandardowych*.
1. Wybierz pozycję **Zapisz** , aby zapisać konfigurację potoku.

### <a name="configure-pipeline-variables"></a>Konfiguruj zmienne potoku

1. Wybierz kartę **zmienne** .
1. Dodaj następujące zmienne w obszarze **zmienne potokowe** i ustaw ich wartości w określony sposób:

    | Nazwa | Wartość |
    | ---- | ----- |
    | `clientId` | **Identyfikator aplikacji (klienta)** , która została zarejestrowana wcześniej. |
    | `clientSecret` | Wartość **wpisu tajnego klienta** , który został utworzony wcześniej. <br /> Zmień typ zmiennej na **wpis tajny** (wybierz ikonę kłódki). |
    | `tenantId` | `your-b2c-tenant.onmicrosoft.com`, gdzie *B2C-dzierżawca* to nazwa dzierżawy Azure AD B2C. |

1. Wybierz pozycję **Zapisz** , aby zapisać zmienne.

### <a name="add-pipeline-tasks"></a>Dodawanie zadań potoku

Następnie Dodaj zadanie, aby wdrożyć plik zasad.

1. Wybierz kartę **zadania** .
1. Wybierz **zadanie agenta**, a następnie wybierz znak plus ( **+** ), aby dodać zadanie do zadania agenta.
1. Wyszukaj i wybierz pozycję **PowerShell**. Nie wybieraj opcji "Azure PowerShell," programu PowerShell na komputerach docelowych, "lub innego wpisu programu PowerShell.
1. Wybierz nowo dodane zadanie **skryptu programu PowerShell** .
1. Wprowadź następujące wartości dla zadania skryptu programu PowerShell:
    * **Wersja zadania**: 2. *
    * **Nazwa wyświetlana**: Nazwa zasad, które mają zostać przekazane przez to zadanie. Na przykład *B2C_1A_TrustFrameworkBase*.
    * **Typ**: ścieżka pliku
    * **Ścieżka skryptu**: wybierz wielokropek (**_..._* _) przejdź do folderu _Scripts *, a następnie wybierz plik *DeployToB2C.ps1* .
    * **Argumentu**

        Wprowadź następujące wartości dla **argumentów**. Zamień na `{alias-name}` alias określony w poprzedniej sekcji.

        ```PowerShell
        # Before
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/{alias-name}/B2CAssets/TrustFrameworkBase.xml
        ```

        Na przykład, jeśli określony alias to *policyRepo*, wiersz argumentu powinien mieć wartość:

        ```PowerShell
        # After
        -ClientID $(clientId) -ClientSecret $(clientSecret) -TenantId $(tenantId) -PolicyId B2C_1A_TrustFrameworkBase -PathToFile $(System.DefaultWorkingDirectory)/policyRepo/B2CAssets/TrustFrameworkBase.xml
        ```

1. Wybierz pozycję **Zapisz** , aby zapisać zadanie agenta.

Właśnie dodane zadanie przekazuje *jeden* plik zasad do Azure AD B2C. Przed kontynuowaniem ręcznie Wyzwól zadanie (**Utwórz wydanie**), aby upewnić się, że zostało pomyślnie zakończone przed utworzeniem dodatkowych zadań.

Jeśli zadanie zostało ukończone pomyślnie, Dodaj zadania wdrażania, wykonując powyższe kroki dla każdego z niestandardowych plików zasad. Zmodyfikuj `-PolicyId` `-PathToFile` wartości argumentów i dla każdej zasady.

`PolicyId`Jest to wartość znaleziona na początku pliku zasad XML w węźle TrustFrameworkPolicy. Na przykład, `PolicyId` w poniższym kodzie XML zasad jest *B2C_1A_TrustFrameworkBase*:

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

Podczas uruchamiania agentów i przekazywania plików zasad upewnij się, że są one przekazywane w następującej kolejności:

1. *TrustFrameworkBase.xml*
1. *TrustFrameworkExtensions.xml*
1. *SignUpOrSignin.xml*
1. *ProfileEdit.xml*
1. *PasswordReset.xml*

Platforma obsługi tożsamości wymusza tę kolejność, ponieważ struktura pliku jest oparta na hierarchicznym łańcuchu.

## <a name="test-your-pipeline"></a>Testowanie potoku

Aby przetestować potok wydania:

1. Wybierz **potoki** , a następnie **wersje**.
1. Wybierz utworzony wcześniej potok, na przykład *DeployCustomPolicies*.
1. Wybierz pozycję **Utwórz wydanie**, a następnie wybierz pozycję **Utwórz** , aby kolejkować wydanie.

Powinien zostać wyświetlony baner powiadomienia informujący o tym, że wersja została umieszczona w kolejce. Aby wyświetlić stan, wybierz link na banerze powiadomienia lub wybierz go z listy na karcie **wydania** .

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej na następujące tematy:

* [Wywołania między usługami przy użyciu poświadczeń klienta](https://docs.microsoft.com/azure/active-directory/develop/v1-oauth2-client-creds-grant-flow)
* [Usługa Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/?view=azure-devops)

<!-- LINKS - External -->
[devops]: https://docs.microsoft.com/azure/devops/?view=azure-devops
[devops-create-project]:  https://docs.microsoft.com/azure/devops/organizations/projects/create-project?view=azure-devops
[devops-pipelines]: https://docs.microsoft.com/azure/devops/pipelines
