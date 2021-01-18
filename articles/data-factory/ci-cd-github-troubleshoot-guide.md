---
title: Rozwiązywanie problemów z dyskami CD, DevOps i usługą GitHub w usłudze ADF
description: Skorzystaj z różnych metod, aby rozwiązywać problemy z dyskami CD w usłudze ADF.
author: ssabat
ms.author: susabat
ms.reviewer: susabat
ms.service: data-factory
ms.workload: data-services
ms.topic: troubleshooting
ms.date: 12/03/2020
ms.openlocfilehash: e5e1a4ff676a6677357638dc4b67dc94926adbd2
ms.sourcegitcommit: 6628bce68a5a99f451417a115be4b21d49878bb2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/18/2021
ms.locfileid: "98556311"
---
# <a name="troubleshoot-ci-cd-azure-devops-and-github-issues-in-adf"></a>Rozwiązywanie problemów z dyskami CD, DevOps i usługą GitHub w usłudze ADF 

[!INCLUDE[appliesto-adf-asa-md](includes/appliesto-adf-asa-md.md)]

W tym artykule przedstawiono typowe metody rozwiązywania problemów z ciągłym wdrażaniem Integration-Continuous (CI-CD), zagadnienia dotyczące platformy Azure DevOps i GitHub w programie Azure Data Factory.

Jeśli masz pytania lub problemy związane z używaniem kontroli źródła lub technik DevOps, Oto kilka artykułów, które mogą okazać się przydatne:

- Zapoznaj się z [kontrolą źródła w podajniku APD](source-control.md) , aby dowiedzieć się, w jaki sposób kontrola źródła jest zastosowana w ADF. 
- Aby dowiedzieć się więcej o tym, jak DevOps CI — na dysku ADF, zapoznaj  [się z informacjami](continuous-integration-deployment.md) dotyczącymi ciągłej integracji.
 
## <a name="common-errors-and-messages"></a>Typowe błędy i komunikaty

### <a name="connect-to-git-repository-failed-due-to-different-tenant"></a>Nawiązywanie połączenia z repozytorium Git nie powiodło się z powodu innej dzierżawy

#### <a name="issue"></a>Problem
    
Czasami występują problemy z uwierzytelnianiem, takie jak HTTP status 401. Szczególnie jeśli masz wiele dzierżawców z kontem Gościa, elementy mogą stać się bardziej skomplikowane.

#### <a name="cause"></a>Przyczyna

Zaobserwowano, że token został uzyskany z oryginalnej dzierżawy, ale funkcja ADF jest w dzierżawie gościa i próbuje użyć tokenu do odwiedzania DevOps w dzierżawie gościa. Nie jest to oczekiwane zachowanie.

#### <a name="recommendation"></a>Zalecenie

Zamiast tego należy użyć tokenu wystawionego przez dzierżawcę gościa. Na przykład trzeba przypisać ten sam Azure Active Directory jako dzierżawcę gościa, a także DevOps, dzięki czemu może poprawnie ustawić zachowanie tokenu i używać odpowiedniej dzierżawy.

### <a name="template-parameters-in-the-parameters-file-are-not-valid"></a>Parametry szablonu w pliku parametrów są nieprawidłowe

#### <a name="issue"></a>Problem

Jeśli usuniesz wyzwalacz w gałęzi deweloperskiej, który jest już dostępny w środowisku testowym lub produkcyjnym z **tą samą** konfiguracją (jak częstotliwość i interwał), wdrożenie potoku zostanie wykonane pomyślnie, a odpowiedni wyzwalacz zostanie usunięty w odpowiednich środowiskach. Ale jeśli istnieje **inna** konfiguracja (taka jak częstotliwość i interwał) dla wyzwalacza w środowiskach testowych/produkcyjnych i jeśli usuniesz ten sam wyzwalacz w środowisku deweloperskim, wdrożenie zakończy się niepowodzeniem z powodu błędu.

#### <a name="cause"></a>Przyczyna

Potok ciągłej integracji/ciągłego wdrażania kończy się niepowodzeniem z powodu następującego błędu:

`
2020-07-20T11:19:02.1276769Z ##[error]Deployment template validation failed: 'The template parameters 'Trigger_Salesforce_properties_typeProperties_recurrence_frequency, Trigger_Salesforce_properties_typeProperties_recurrence_interval, Trigger_Salesforce_properties_typeProperties_recurrence_startTime, Trigger_Salesforce_properties_typeProperties_recurrence_timeZone' in the parameters file are not valid; they are not present in the original template and can therefore not be provided at deployment time. The only supported parameters for this template are 'factoryName, PlanonDWH_connectionString, PlanonKeyVault_properties_typeProperties_baseUrl
`

#### <a name="recommendation"></a>Zalecenie

Ten błąd występuje, ponieważ często usuwa wyzwalacz, który jest sparametryzowane, dlatego parametry nie będą dostępne w szablonie ARM (ponieważ wyzwalacz już nie istnieje). Ponieważ parametr nie ma już szablonu ARM, należy zaktualizować zastąpione parametry w potoku DevOps. W przeciwnym razie, za każdym razem, gdy parametry w szablonie ARM zmienią się, muszą zaktualizować zastąpione parametry w potoku DevOps (w zadaniu wdrażania).

### <a name="updating-property-type-is-not-supported"></a>Aktualizowanie typu właściwości nie jest obsługiwane

#### <a name="issue"></a>Problem

Potok zwolnień ciągłej integracji/ciągłego wdrażania kończy się niepowodzeniem z powodu następującego błędu:

`
2020-07-06T09:50:50.8716614Z There were errors in your deployment. Error code: DeploymentFailed.
2020-07-06T09:50:50.8760242Z ##[error]At least one resource deployment operation failed. Please list deployment operations for details. Please see https://aka.ms/DeployOperations for usage details.
2020-07-06T09:50:50.8771655Z ##[error]Details:
2020-07-06T09:50:50.8772837Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8774148Z ##[error]DataFactoryPropertyUpdateNotSupported: Updating property type is not supported.
2020-07-06T09:50:50.8775530Z ##[error]Check out the troubleshooting guide to see if your issue is addressed: https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-resource-group-deployment#troubleshooting
2020-07-06T09:50:50.8776801Z ##[error]Task failed while creating or updating the template deployment.
`

#### <a name="cause"></a>Przyczyna

Jest to spowodowane Integration Runtime o tej samej nazwie w fabryki docelowej, ale z innym typem. Integration Runtime musi być tego samego typu podczas wdrażania.

#### <a name="recommendation"></a>Zalecenie

- Zapoznaj się z najlepszymi rozwiązaniami dotyczącymi ciągłej integracji/ciągłego wdrażania:

    https://docs.microsoft.com/azure/data-factory/continuous-integration-deployment#best-practices-for-cicd 
- Środowiska Integration Runtime nie zmieniają się często i są podobne do wszystkich etapów w ciągłej integracji/ciągłego wdrażania, dlatego Data Factory oczekuje, że masz taką samą nazwę i typ środowiska Integration Runtime dla wszystkich etapów ciągłej integracji/ciągłego wdrażania. Jeśli nazwa i typy & właściwości różnią się, upewnij się, że jest zgodna z konfiguracją źródłową i docelową, a następnie wdróż potok wersji.
- Jeśli chcesz udostępnić środowisko Integration Runtime na wszystkich etapach, rozważ użycie usługi Trzyelementowy Factory, aby zawierała udostępnione środowisko Integration Runtime. Tej fabryki udostępnionej można używać we wszystkich środowiskach jako połączonego typu środowiska Integration Runtime.

### <a name="document-creation-or-update-failed-because-of-invalid-reference"></a>Tworzenie lub aktualizowanie dokumentu nie powiodło się z powodu nieprawidłowego odwołania

#### <a name="issue"></a>Problem

Podczas próby opublikowania zmian w Data Factory pojawia się następujący komunikat o błędzie:

`
"error": {
        "code": "BadRequest",
        "message": "The document creation or update failed because of invalid reference '<entity>'.",
        "target": "/subscriptions/xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx/resourceGroups/<rgname>/providers/Microsoft.DataFactory/factories/<datafactory>/pipelines/<pipeline>",
        "details": null
    }
`

#### <a name="symptom"></a>Objaw

Odłączono konfigurację narzędzia Git i skonfigurujemy ją ponownie z wybraną flagą "Importuj zasoby", która ustawia Data Factory jako "w synchronizacji". Oznacza to, że nie wprowadzono żadnych zmian do opublikowania.

#### <a name="resolution"></a>Rozwiązanie

Odłącz konfigurację narzędzia Git i skonfiguruj ją ponownie, a następnie upewnij się, że nie zaznaczono pola wyboru "Importuj istniejące zasoby".

### <a name="data-factory-move-failing-from-one-resource-group-to-another"></a>Data Factory przenoszenie nie powiodło się z jednej grupy zasobów do innej

#### <a name="issue"></a>Problem

Nie można przenieść Data Factory z jednej grupy zasobów do innej, niepowodzeniem z powodu następującego błędu:

`
{
    "code": "ResourceMoveProviderValidationFailed",
    "message": "Resource move validation failed. Please see details. Diagnostic information: timestamp 'xxxxxxxxxxxxZ', subscription id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', tracking id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx', request correlation id 'xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx'.",
    "details": [
        {
            "code": "BadRequest",
            "target": "Microsoft.DataFactory/factories",
            "message": "One of the resources contain integration runtimes that are either SSIS-IRs in starting/started/stopping state, or Self-Hosted IRs which are shared with other resources. Resource move is not supported for those resources."
        }
    ]
}
`

#### <a name="resolution"></a>Rozwiązanie

Musisz usunąć SSIS-IR i udostępnione urzędy skarbowe, aby zezwolić na operację przenoszenia. Jeśli nie chcesz usunąć urzędu skarbowego, najlepszym sposobem jest wykonanie kopii i klonowanie dokumentu w celu wykonania kopii, a po jej zakończeniu Usuń starą fabrykę danych.

###  <a name="unable-to-export-and-import-arm-template"></a>Nie można wyeksportować i zaimportować szablonu ARM

#### <a name="issue"></a>Problem

Nie można wyeksportować i zaimportować szablonu ARM. W portalu nie ma żadnego błędu, ale w śledzeniu przeglądarki może zostać wyświetlony następujący błąd:

`Failed to load resource: the server responded with a status code of 401 (Unauthorized)`

#### <a name="cause"></a>Przyczyna

Utworzono rolę klienta jako użytkownika, która nie ma wystarczających uprawnień. Gdy fabryka jest załadowana w interfejsie użytkownika, sprawdzana jest seria wartości kontroli ekspozycji dla fabryki. W takim przypadku rola dostępu użytkownika nie ma uprawnień dostępu do interfejsu API *queryFeaturesValue* . Aby uzyskać dostęp do tego interfejsu API, funkcja parametrów globalnych jest wyłączona. Ścieżka kodu eksportu ARM jest częściowo oparta na funkcji Global Parameters.

#### <a name="resolution"></a>Rozwiązanie

Aby rozwiązać ten problem, należy dodać następujące uprawnienie do roli: *Microsoft. DataFactory/factorers/queryFeaturesValue/Action*. To uprawnienie powinno być domyślnie włączone w roli "Współautor Data Factory".

## <a name="next-steps"></a>Następne kroki

Aby uzyskać pomoc dotyczącą rozwiązywania problemów, wypróbuj następujące zasoby:

*  [Blog Data Factory](https://azure.microsoft.com/blog/tag/azure-data-factory/)
*  [Żądania funkcji Data Factory](https://feedback.azure.com/forums/270578-data-factory)
*  [Wideo dotyczące platformy Azure](https://azure.microsoft.com/resources/videos/index/?sort=newest&services=data-factory)
*  [Forum przepełnienia stosu dla Data Factory](https://stackoverflow.com/questions/tagged/azure-data-factory)
*  [Informacje o usłudze Twitter dotyczące Data Factory](https://twitter.com/hashtag/DataFactory)
