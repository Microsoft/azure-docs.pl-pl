---
title: Ciągłe wdrażanie za pomocą usługi Azure DevOps (wersja zapoznawcza)
titleSuffix: Azure Cognitive Services
description: W tym artykule dowiesz się, jak skonfigurować ciągłe wdrażanie dla aplikacji poleceń niestandardowych. Tworzysz skrypty do obsługi przepływów pracy ciągłego wdrażania.
services: cognitive-services
author: xiaojul
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 06/18/2020
ms.author: xiaojul
ms.openlocfilehash: bd1ab5110313380c90e71ed161935c7274a845b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91839252"
---
# <a name="continuous-deployment-with-azure-devops"></a>Ciągłe wdrażanie za pomocą usługi Azure DevOps

W tym artykule dowiesz się, jak skonfigurować ciągłe wdrażanie dla aplikacji poleceń niestandardowych. Skrypty obsługujące przepływ pracy CI/CD są udostępniane.

## <a name="prerequisite"></a>Wymaganie wstępne
> [!div class = "checklist"]
> * Aplikacja poleceń niestandardowych do programowania (DEV)
> * Aplikacja poleceń niestandardowych dla środowiska produkcyjnego (PROD)
> * Zarejestruj się, aby uzyskać [Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started/pipelines-sign-up?view=azure-devops)

## <a name="exportimportpublish"></a>Eksportuj/importuj/Publikuj

Skrypty są hostowane w [Cognitive Services Asystencie głosu — poleceniami niestandardowymi](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Sklonuj skrypty znajdujące się w katalogu bash w repozytorium. Upewnij się, że utrzymywana jest taka sama ścieżka.

### <a name="set-up-a-pipeline"></a>Konfigurowanie potoku 

1. Przejdź do **usługi Azure DevOps — potoki** i kliknij pozycję "nowy potok".
1. W sekcji **Połącz** wybierz lokalizację repozytorium, w którym znajdują się te skrypty
1. W obszarze **Wybierz** wybierz repozytorium
1. W sekcji **Konfigurowanie** wybierz pozycję "potok początkowy"
1. Następnie otrzymasz Edytor z plikiem YAML, Zastąp sekcję "kroki" tym skryptem.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Export source app'
      inputs:
        targetType: filePath
        filePath: ./bash/export.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(SourceAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Import to target app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ExportedDialogModel.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish target app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```
    
1. Należy pamiętać, że te skrypty założono, że używasz regionu `westus2` , jeśli nie jest to przypadek, Aktualizuj argumenty zadań

    > [!div class="mx-imgBorder"]
    > ![Zrzut ekranu, który podświetla wartość regionu w argumentach.](media/custom-commands/cicd-new-pipeline-yaml.png)

1. Na przycisku "Zapisz i uruchom" Otwórz listę rozwijaną, a następnie kliknij pozycję "Zapisz".

### <a name="hook-up-the-pipeline-with-your-application"></a>Podłączanie potoku do aplikacji

1. Przejdź do strony głównej potoku.
1. W prawym górnym rogu listy rozwijanej wybierz pozycję **Edytuj potok**. Umożliwia to edytor YAML. 
1. W prawym górnym rogu obok przycisku "Uruchom" Wybierz pozycję **zmienne**. Kliknij pozycję **Nowa zmienna**.
1. Dodaj następujące zmienne:
    
    | Zmienna | Opis |
    | ------- | --------------- | ----------- |
    | SourceAppId | Identyfikator aplikacji DEWELOPERSKIej |
    | TargetAppId | Identyfikator aplikacji PRODUKCYJNEj |
    | SubscriptionKey | Klucz subskrypcji używany w obu aplikacjach |
    | Kultura | Kultura aplikacji (tj. en-US) |

    > [!div class="mx-imgBorder"]
    > ![Wyślij ładunek aktywności](media/custom-commands/cicd-edit-pipeline-variables.png)

1. Kliknij pozycję "Uruchom", a następnie kliknij pozycję "zadanie", które jest uruchomione. 

    Powinna zostać wyświetlona lista zadań, które zawierają: "Eksportuj aplikację źródłową", "Importuj do aplikacji docelowej" & "uczenie i publikowanie aplikacji docelowej"

## <a name="deploy-from-source-code"></a>Wdróż z kodu źródłowego

W przypadku, gdy chcesz zachować definicję aplikacji w repozytorium, udostępniamy skrypty dla wdrożeń z kodu źródłowego. Ponieważ skrypty są w bash, w przypadku korzystania z systemu Windows należy zainstalować [podsystem Linux](https://docs.microsoft.com/windows/wsl/install-win10).

Skrypty są hostowane w [Cognitive Services Asystencie głosu — poleceniami niestandardowymi](https://github.com/Azure-Samples/Cognitive-Services-Voice-Assistant/tree/master/custom-commands). Sklonuj skrypty znajdujące się w katalogu bash w repozytorium. Upewnij się, że utrzymywana jest taka sama ścieżka.

### <a name="prepare-your-repository"></a>Przygotowywanie repozytorium

1. Utwórz katalog dla aplikacji, w naszym przykładzie Utwórz jeden o nazwie "Apps" (aplikacje).
1. Zaktualizuj argumenty skryptu bash, a następnie uruchom polecenie. Spowoduje to zaimportowanie modelu okna dialogowego aplikacji do pliku myapp.jsna
    ```BASH
    bash/export.sh -r <region> -s <subscriptionkey> -c en-us -a <appid> -f apps/myapp.json
    ```
    | Argumenty | Opis |
    | ------- | --------------- | ----------- |
    | region | Region aplikacji, np. westus2. |
    | subscriptionkey | klucz subskrypcji zasobu mowy. |
    | AppID | Identyfikator aplikacji poleceń niestandardowych, który chcesz wyeksportować. |

1. Wypchnij te zmiany do repozytorium.

### <a name="set-up-a-pipeline"></a>Konfigurowanie potoku 

1. Przejdź do **usługi Azure DevOps — potoki** i kliknij pozycję "nowy potok".
1. W sekcji **Połącz** wybierz lokalizację repozytorium, w którym znajdują się te skrypty
1. W obszarze **Wybierz** wybierz repozytorium
1. W sekcji **Konfigurowanie** wybierz pozycję "potok początkowy"
1. Następnie otrzymasz Edytor z plikiem YAML, Zastąp sekcję "kroki" tym skryptem.

    ```YAML
    steps:
    - task: Bash@3
      displayName: 'Import app'
      inputs:
        targetType: filePath
        filePath: ./bash/import.sh
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId) -f ../apps/myapp.json'
        workingDirectory: bash
        failOnStderr: true
    
    - task: Bash@3
      displayName: 'Train and Publish app'
      inputs:
        targetType: filePath
        filePath: './bash/train-and-publish.sh'
        arguments: '-r westus2 -s $(SubscriptionKey) -c $(Culture) -a $(TargetAppId)'
        workingDirectory: bash
        failOnStderr: true
    ```

    > [!NOTE]
    > w tych skryptach przyjęto założenie, że używasz regionu westus2, jeśli tak nie jest, aktualizacja argumentów zadań odpowiednio

1. Na przycisku "Zapisz i uruchom" Otwórz listę rozwijaną, a następnie kliknij pozycję "Zapisz".

### <a name="hook-up-the-pipeline-with-your-target-applications"></a>Podłączanie potoku do aplikacji docelowych

1. Przejdź do strony głównej potoku.
1. W prawym górnym rogu listy rozwijanej wybierz pozycję **Edytuj potok**. Umożliwia to edytor YAML. 
1. W prawym górnym rogu obok przycisku "Uruchom" Wybierz pozycję **zmienne**. Kliknij pozycję **Nowa zmienna**.
1. Dodaj następujące zmienne:

    | Zmienna | Opis |
    | ------- | --------------- | ----------- |
    | TargetAppId | Identyfikator aplikacji PRODUKCYJNEj |
    | SubscriptionKey | Klucz subskrypcji używany w obu aplikacjach |
    | Kultura | Kultura aplikacji (tj. en-US) |

1. Kliknij pozycję "Uruchom", a następnie kliknij pozycję "zadanie", które jest uruchomione.
    Powinna zostać wyświetlona lista zadań, które zawierają: "Importuj aplikację" & "uczenie i publikowanie aplikacji"

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Zobacz przykłady w witrynie GitHub](https://aka.ms/speech/cc-samples)