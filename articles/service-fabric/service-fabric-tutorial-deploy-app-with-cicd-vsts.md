---
title: Wdróż aplikację za pomocą elementu CI i Azure Pipelines
description: Z tego samouczka dowiesz się, jak skonfigurować ciągłą integrację i wdrażanie dla aplikacji usługi Service Fabric przy użyciu usługi Azure Pipelines.
ms.topic: tutorial
ms.date: 07/22/2019
ms.custom: mvc
ms.openlocfilehash: a26cfaca466e01b154c65b27895f3004f6320e5d
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "91326341"
---
# <a name="tutorial-deploy-an-application-with-cicd-to-a-service-fabric-cluster"></a>Samouczek: wdrażanie aplikacji przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric

Niniejszy samouczek jest czwartą częścią serii i zawiera opis sposobu konfigurowania ciągłej integracji i ciągłego wdrażania aplikacji usługi Service Fabric za pomocą usługi Azure Pipelines.  Wymagana jest istniejąca aplikacja usługi Service Fabric. Na potrzeby tego samouczka za przykład posłużyła aplikacja utworzona w temacie [Tworzenie aplikacji .NET](service-fabric-tutorial-create-dotnet-app.md).

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie potoku kompilacji w usłudze Azure Pipelines
> * Tworzenie potoku wydania w usłudze Azure Pipelines
> * Automatyczne wdrażanie i uaktualnianie aplikacji

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:
> [!div class="checklist"]
> * [Tworzenie aplikacji platformy .NET w usłudze Service Fabric](service-fabric-tutorial-create-dotnet-app.md)
> * [Wdrażanie aplikacji w klastrze zdalnym](service-fabric-tutorial-deploy-app-to-party-cluster.md)
> * [Dodawanie punktu końcowego protokołu HTTPS do usługi frontonu platformy ASP.NET Core](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * Konfigurowanie ciągłej integracji/ciągłego wdrażania za pomocą usługi Azure Pipelines
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Zainstaluj program Visual Studio 2019](https://www.visualstudio.com/) i zainstaluj obciążenia związane z programowaniem i **programowaniem** na **platformie Azure** .
* [Instalowanie zestawu SDK usługi Service Fabric](service-fabric-get-started.md)
* Utwórz klaster usługi Service Fabric z systemem Windows na platformie Azure, na przykład [postępując zgodnie z tym samouczkiem](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* Utwórz [organizację usługi Azure DevOps](/azure/devops/organizations/accounts/create-organization-msa-or-work-student). Umożliwia to utworzenie projektu w usłudze Azure DevOps i korzystanie z usługi Azure Pipelines.

## <a name="download-the-voting-sample-application"></a>Pobieranie przykładowej aplikacji do głosowania

Jeśli nie skompilowano przykładowej aplikacji do głosowania w [pierwszej części tej serii samouczków](service-fabric-tutorial-create-dotnet-app.md), można ją pobrać. W oknie polecenia uruchom następujące polecenie, aby sklonować przykładowe repozytorium aplikacji na komputer lokalny.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="prepare-a-publish-profile"></a>Przygotowywanie profilu publikowania

Po [utworzeniu aplikacji](service-fabric-tutorial-create-dotnet-app.md) i [wdrożeniu jej na platformie Azure](service-fabric-tutorial-deploy-app-to-party-cluster.md) wszystko jest gotowe do skonfigurowania ciągłej integracji.  Najpierw przygotuj profil publikowania w aplikacji na potrzeby procesu wdrażania wykonywanego w ramach usługi Azure Pipelines.  Profil publikowania należy skonfigurować pod kątem klastra, który został wcześniej utworzony.  Uruchom program Visual Studio i otwórz istniejący projekt aplikacji usługi Service Fabric.  W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Opublikuj...**.

Wybierz profil docelowy w ramach projektu aplikacji na potrzeby przepływu pracy ciągłej integracji, na przykład chmurę.  Określ punkt końcowy połączenia klastra.  Zaznacz pole wyboru **Uaktualnij aplikację**, aby uaktualnić aplikację dla każdego wdrożenia w usłudze Azure DevOps.  Kliknij hiperlink **Zapisz**, aby zapisać ustawienia w profilu publikowania, a następnie kliknij przycisk **Anuluj** w celu zamknięcia okna dialogowego.

![Profil wypychania][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Udostępnianie rozwiązania programu Visual Studio w nowym repozytorium Git w usłudze Azure DevOps

Udostępnij pliki źródłowe aplikacji w projekcie zespołowym usługi Azure DevOps, aby umożliwić generowanie kompilacji.

Utwórz nowe lokalne repozytorium git dla projektu, wybierając pozycję **Dodaj do kontroli źródła**  ->  **git** na pasku stanu w prawym dolnym rogu programu Visual Studio.

W widoku **Wypychanie** w programie **Team Explorer** wybierz przycisk **Opublikuj repozytorium Git** w obszarze **Wypychanie do usługi Azure DevOps**.

![Zrzut ekranu okna synchronizacja Team Explorer w programie Visual Studio. Przycisk Publikuj w repozytorium git jest wyróżniony w obszarze wypychanie do usługi Azure DevOps.][push-git-repo]

Sprawdź swój adres e-mail i z listy rozwijanej **Domena usługi Azure DevOps** wybierz swoje konto. Wprowadź nazwę repozytorium i wybierz polecenie **Publikuj repozytorium**.

![Zrzut ekranu przedstawiający ustawienia wypychania do platformy Azure DevOps z wyróżnionym przyciskiem Poczta E-mail, konto, Nazwa repozytorium i publikowanie repozytorium.][publish-code]

Opublikowanie repozytorium powoduje utworzenie nowego projektu na Twoim koncie o takiej samej nazwie co lokalne repozytorium. Aby utworzyć repozytorium w istniejącym projekcie, kliknij pozycję **Zaawansowane** obok pola **Nazwa repozytorium**, a następnie wybierz projekt. Aby wyświetlić kod w Internecie, wybierz polecenie **Wyświetl w Internecie**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Konfigurowanie ciągłego dostarczania za pomocą usługi Azure Pipelines

Potok kompilacji Azure Pipelines opisuje przepływ pracy, który składa się z zestawu kroków kompilacji, które są wykonywane sekwencyjnie. Utwórz potok kompilacji, który spowoduje wygenerowanie pakietu aplikacji usługi Service Fabric i innych artefaktów, na potrzeby wdrożenia w klastrze usługi Service Fabric. Dowiedz się więcej o [potokach kompilacji usługi Azure Pipelines](https://www.visualstudio.com/docs/build/define/create). 

Potok wydania usługi Azure Pipelines opisuje przepływ pracy, który wdraża pakiet aplikacji w klastrze. Jednoczesne użycie potoku kompilacji i potoku wydania powoduje wykonanie całego przepływu pracy, zaczynając od plików źródłowych, a kończąc na aplikacji uruchomionej w klastrze. Dowiedz się więcej na temat [potoków wydań Azure Pipelines](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition).

### <a name="create-a-build-pipeline"></a>Tworzenie potoku kompilacji

Otwórz przeglądarkę internetową i przejdź do nowego projektu na stronie: [https://&lt;moje_konto&gt;.visualstudio.com/Voting/Voting%20Team/_git/Voting](https://myaccount.visualstudio.com/Voting/Voting%20Team/_git/Voting).

Wybierz kartę **Potoki**, wybierz pozycję **Kompilacje**, a następnie pozycję **Nowy potok**.

![Nowy potok][new-pipeline]

Wybierz pozycję **Azure Repos Git** jako źródło, projekt zespołowy **Voting**, repozytorium **Voting** i domyślną gałąź **master** dla kompilacji ręcznych i zaplanowanych.  Następnie kliknij przycisk **Kontynuuj**.

![Wybieranie repozytorium][select-repo]

W obszarze **Wybierz szablon**wybierz szablon **aplikacja Azure Service Fabric** i kliknij przycisk **Zastosuj**.

![Wybieranie szablonu kompilacji][select-build-template]

W obszarze **Zadania** wprowadź wyrażenie „Hostowany program VS2017” jako **Pulę agentów**.

![Wybieranie zadań][save-and-queue]

W obszarze **Wyzwalacze** włącz ciągłą integrację, zaznaczając pozycję **Włącz ciągłą integrację**. W obszarze **Filtry gałęzi** wartość domyślna pozycji **Specyfikacja gałęzi** jest ustawiona na **master**. Wybierz pozycję **Zapisz i dodaj do kolejki**, aby ręcznie uruchomić kompilację.

![Wybieranie wyzwalaczy][save-and-queue2]

Kompilacje można również wyzwalać w ramach procesu wypychania lub ewidencjonowania. Aby sprawdzić postęp kompilacji, przejdź do karty **kompilacje** .  Po sprawdzeniu, czy kompilacja została wykonana pomyślnie, zdefiniuj potok wydania, który wdraża aplikację w klastrze.

### <a name="create-a-release-pipeline"></a>Tworzenie potoku wydania

Wybierz kartę **Potoki**, wybierz pozycję **Wydania**, a następnie wybierz pozycję **+ Nowy potok**.  W obszarze **Wybieranie szablonu** wybierz z listy szablon **Wdrożenie usługi Azure Service Fabric**, a następnie przycisk **Zastosuj**.

![Wybieranie szablonu wydania][select-release-template]

Wybierz pozycję **zadania** -> **środowisko 1** i pozycję **+ Nowy** , aby dodać nowe połączenie klastra.

![Dodawanie połączenia klastra][add-cluster-connection]

W widoku **Dodawanie nowego połączenia z usługą Service Fabric** wybierz uwierzytelnianie **Na podstawie certyfikatu** lub **Azure Active Directory**.  Określ nazwę połączenia dla klastra „mysftestcluster” i następujący punkt końcowy klastra „tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000” (lub punkt końcowy klastra, w którym przeprowadzane jest wdrożenie).

W przypadku uwierzytelniania opartego na certyfikatach Dodaj **odcisk palca certyfikatu serwera** , który został użyty do utworzenia klastra.  W obszarze **Certyfikat klienta** dodaj plik certyfikatu klienta zakodowany przy użyciu kodowania base-64. Zobacz pomoc podręczną dla tego pola, aby uzyskać informacje dotyczące sposobu uzyskania reprezentacji certyfikatu w kodowaniu base-64. Dodaj również **Hasło** certyfikatu.  Jeśli nie masz osobnego certyfikatu klienta, możesz użyć certyfikatu klastra lub serwera.

W przypadku poświadczeń usługi Azure Active Directory dodaj **Odcisk palca certyfikatu serwera** dla certyfikatu serwera użytego do utworzenia klastra oraz poświadczenia w polach **Nazwa użytkownika** i **Hasło**, których chcesz użyć w celu nawiązania połączenia z klastrem.

Kliknij przycisk **Dodaj**, aby zapisać połączenie klastra.

Następnie dodaj artefakt kompilacji do potoku, aby umożliwić potokowi wydania odnalezienie danych wyjściowych kompilacji. Wybierz pozycję **Potok**, a następnie **Artefakty**->**+ Dodaj**.  W obszarze **Źródło (definicja kompilacji)** wybierz wcześniej utworzony potok kompilacji.  Kliknij przycisk **Dodaj**, aby zapisać artefakt kompilacji.

![Dodawanie artefaktu][add-artifact]

Włącz wyzwalacz ciągłego wdrażania, aby umożliwić automatyczne tworzenie wydania po zakończeniu kompilacji. Kliknij ikonę pioruna w artefakcie, włącz wyzwalacz, a następnie kliknij przycisk **Zapisz** w celu zapisania potoku wydania.

![Włączanie wyzwalacza][enable-trigger]

Wybierz pozycję **+ wydanie**  ->  **Utwórz wersję**  ->  **Utwórz** , aby ręcznie utworzyć wydanie. Postęp wydawania możesz monitorować na karcie **Wydania**.

Sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja została uruchomiona w klastrze.  Otwórz przeglądarkę internetową i przejdź pod adres `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Zwróć uwagę na wersję aplikacji. W tym przykładzie jest to „1.0.0.20170616.3”.

## <a name="commit-and-push-changes-trigger-a-release"></a>Zatwierdzanie i wypychanie zmian, wyzwalanie wydania

Aby upewnić się, że potok ciągłej integracji działa prawidłowo, zaewidencjonuj zmiany kodu w usłudze Azure DevOps.

Podczas pisania kodu zmiany są automatycznie śledzone przez program Visual Studio. Zatwierdź zmiany w lokalnym repozytorium Git, wybierając ikonę oczekujących zmian (![Ikona oczekujących zmian zawiera ołówek i numer.][pending]) na pasku stanu w prawym dolnym rogu.

W widoku **Zmiany** programu Team Explorer dodaj komunikat opisujący aktualizację i zatwierdź wprowadzone zmiany.

![Zatwierdź wszystko][changes]

Wybierz ikonę paska stanu nieopublikowanych zmian (![Nieopublikowane zmiany][unpublished-changes]) lub widok synchronizacji w programie Team Explorer. Wybierz pozycję **Wypchnij**, aby zaktualizować kod w usłudze Azure Pipelines.

![Wypychanie zmian][push]

Wypychanie zmian do usługi Azure Pipelines automatycznie wyzwala kompilację.  Po pomyślnym zakończeniu potoku kompilacji automatycznie tworzone jest wydanie, które rozpoczyna uaktualnianie aplikacji w klastrze.

Aby sprawdzić postęp kompilacji, przejdź do karty **Kompilacje** modułu **Team Explorer** w programie Visual Studio.  Po upewnieniu się, że kompilacja jest wykonywana prawidłowo, zdefiniuj potok wydania, który wdraża aplikację w klastrze.

Sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja została uruchomiona w klastrze.  Otwórz przeglądarkę internetową i przejdź pod adres `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Zwróć uwagę na wersję aplikacji. W tym przykładzie jest to „1.0.0.20170815.3”.

![Zrzut ekranu aplikacji do głosowania w Service Fabric Explorer uruchomiony w oknie przeglądarki. Zostanie wyświetlona wersja aplikacji "1.0.0.20170815.3".][sfx1]

## <a name="update-the-application"></a>Aktualizowanie aplikacji

Wprowadź zmiany w kodzie aplikacji.  Zapisz i zatwierdź zmiany, postępując zgodnie z poprzednimi krokami.

Po rozpoczęciu uaktualnienia aplikacji możesz obserwować jego postęp w narzędziu Service Fabric Explorer:

![Zrzut ekranu aplikacji do głosowania w Service Fabric Explorer. Zostanie wyróżniony komunikat o stanie "uaktualnienie" i "uaktualnienie w toku".][sfx2]

Uaktualnianie aplikacji może potrwać kilka minut. Po zakończeniu uaktualniania aplikacja zostanie uruchomiona w następnej wersji.  W tym przykładzie „1.0.0.20170815.4”.

![Zrzut ekranu aplikacji do głosowania w Service Fabric Explorer uruchomiony w oknie przeglądarki. Zostanie wyświetlona zaktualizowana wersja aplikacji "1.0.0.20170815.4".][sfx3]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie potoku kompilacji
> * Tworzenie potoku wydania
> * Automatyczne wdrażanie i uaktualnianie aplikacji

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-monitoring-aspnet.md)

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewPipeline.png
[select-repo]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectRepo.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectBuildTemplate.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue.png
[save-and-queue2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SaveAndQueue2.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddClusterConnection.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/Push.png
[continuous-delivery-with-AzureDevOpsServices]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-app-with-cicd-vsts/NewServiceEndpointDialog.png
