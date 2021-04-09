---
title: Wdrażanie aplikacji kontenera przy użyciu ciągłej integracji/ciągłego dostarczania
description: W ramach tego samouczka nauczysz się konfigurować ciągłą integrację i ciągłe wdrażanie aplikacji kontenera usługi Azure Service Fabric za pomocą programu Visual Studio i usługi Azure DevOps.
ms.topic: tutorial
ms.date: 08/29/2018
ms.custom: mvc
ms.openlocfilehash: bb0eb9226a99f139ff10a8da12a1e22017536c67
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "96018855"
---
# <a name="tutorial-deploy-a-container-application-with-cicd-to-a-service-fabric-cluster"></a>Samouczek: wdrażanie aplikacji kontenera przy użyciu ciągłej integracji/ciągłego wdrażania w klastrze usługi Service Fabric

Niniejszy samouczek jest drugą częścią serii i zawiera opis sposobu konfiguracji ciągłej integracji i ciągłego wdrażania aplikacji kontenera usługi Azure Service Fabric za pomocą programu Visual Studio i usługi Azure DevOps.  Wymagana jest istniejąca aplikacja usługi Service Fabric. Na potrzeby tego samouczka za przykład posłużyła aplikacja utworzona w temacie [Samouczek: wdrażanie aplikacji .NET w kontenerze systemu Windows w usłudze Azure Service Fabric](service-fabric-host-app-in-a-container.md).

Część druga serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie definicji kompilacji w programie Visual Studio Team Explorer
> * Tworzenie definicji wydania w programie Visual Studio Team Explorer
> * Automatyczne wdrażanie i uaktualnianie aplikacji

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Mieć klaster na platformie Azure lub [utworzyć go za pomocą tego samouczka](service-fabric-tutorial-create-vnet-and-windows-cluster.md)
* [Wdrożyć w nim konteneryzowaną aplikację](service-fabric-host-app-in-a-container.md)

## <a name="prepare-a-publish-profile"></a>Przygotowywanie profilu publikowania

Teraz po [wdrożeniu aplikacji kontenera](service-fabric-host-app-in-a-container.md) wszystko jest gotowe do skonfigurowania ciągłej integracji.  Najpierw przygotuj profil publikowania w aplikacji na potrzeby procesu wdrażania wykonywanego w ramach usługi Azure DevOps.  Profil publikowania należy skonfigurować pod kątem klastra, który został wcześniej utworzony.  Uruchom program Visual Studio i otwórz istniejący projekt aplikacji usługi Service Fabric.  W **Eksploratorze rozwiązań** kliknij prawym przyciskiem myszy aplikację i wybierz polecenie **Opublikuj...**.

Wybierz profil docelowy w ramach projektu aplikacji na potrzeby przepływu pracy ciągłej integracji, na przykład chmurę.  Określ punkt końcowy połączenia klastra.  Zaznacz pole wyboru **Uaktualnij aplikację**, aby uaktualnić aplikację dla każdego wdrożenia w usłudze Azure DevOps.  Kliknij hiperlink **Zapisz**, aby zapisać ustawienia w profilu publikowania, a następnie kliknij przycisk **Anuluj** w celu zamknięcia okna dialogowego.

![Profil wypychania][publish-app-profile]

## <a name="share-your-visual-studio-solution-to-a-new-azure-devops-git-repo"></a>Udostępnianie rozwiązania programu Visual Studio w nowym repozytorium Git w usłudze Azure DevOps

Udostępnij pliki źródłowe swojej aplikacji w projekcie zespołowym usługi Azure DevOps, aby umożliwić generowanie kompilacji.

Utwórz nowe lokalne repozytorium git dla projektu, wybierając pozycję **Dodaj do kontroli źródła**  ->  **git** na pasku stanu w prawym dolnym rogu programu Visual Studio.

W widoku **Wypychanie** w programie **Team Explorer** wybierz przycisk **Opublikuj repozytorium Git** w obszarze **Wypychanie do usługi Azure DevOps**.

![Zrzut ekranu okna synchronizacja Team Explorer w programie Visual Studio. W obszarze wypychanie do usługi Azure DevOps jest wyróżniony przycisk Publikuj w repozytorium git.][push-git-repo]

Sprawdź swój adres e-mail i wybierz swoją organizację z listy rozwijanej **Konta**. Jeśli jeszcze nie masz organizacji, będzie trzeba ją skonfigurować. Wprowadź nazwę repozytorium i wybierz polecenie **Publikuj repozytorium**.

![Zrzut ekranu przedstawiający okno wypychania do usługi Azure DevOps. Ustawienia poczty E-mail, konta, nazwy repozytorium i repozytorium publikowania są wyróżnione.][publish-code]

Opublikowanie repozytorium powoduje utworzenie nowego projektu zespołowego na Twoim koncie o takiej samej nazwie jak lokalne repozytorium. Aby utworzyć repozytorium w istniejącym projekcie zespołowym, kliknij pozycję **Zaawansowane** obok nazwy **Repozytorium**, a następnie wybierz projekt zespołowy. Aby wyświetlić kod w Internecie, wybierz polecenie **Wyświetl w Internecie**.

## <a name="configure-continuous-delivery-with-azure-pipelines"></a>Konfigurowanie ciągłego dostarczania za pomocą usługi Azure Pipelines

Definicja kompilacji usługi Azure DevOps zawiera opis przepływu pracy, który składa się z zestawu kroków kompilacji wykonywanych sekwencyjnie. Utwórz definicję kompilacji, która spowoduje wygenerowanie pakietu aplikacji usługi Service Fabric i innych artefaktów, na potrzeby wdrożenia w klastrze usługi Service Fabric. Dowiedz się więcej o [definicjach kompilacji usługi Azure DevOps](https://www.visualstudio.com/docs/build/define/create). 

Definicja wydania usługi Azure DevOps opisuje przepływ pracy, który wdraża pakiet aplikacji w klastrze. Jednoczesne użycie definicji kompilacji i definicji wydania powoduje wykonanie całego przepływu pracy, zaczynając od plików źródłowych, a kończąc na aplikacji uruchomionej w klastrze. Dowiedz się więcej o [definicjach wydania](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) usługi Azure DevOps.

### <a name="create-a-build-definition"></a>Tworzenie definicji kompilacji

Otwórz nowy projekt zespołowy, przechodząc pod adres https://dev.azure.com w przeglądarce internetowej i wybierając swoją organizację, a następnie nowy projekt. 

Wybierz opcję **Potoki** na panelu po lewej, a następnie kliknij pozycję **Nowy potok**.

>[!NOTE]
>Jeśli nie widzisz szablonu definicji kompilacji, upewnij się, że funkcja **Nowe środowisko tworzenia potoków przy użyciu kodu YAML** została wyłączona. Ta funkcja jest konfigurowana w sekcji **Funkcje w wersji zapoznawczej** konta metodyki DevOps.

![Nowy potok][new-pipeline]

Wybierz pozycję **Azure Repos Git** jako źródło, nazwę Twojego projektu zespołowego, Twoje repozytorium projektu i domyślną gałąź **master** lub kompilacje ręczne i zaplanowane.  Następnie kliknij przycisk **Kontynuuj**.

W obszarze **Wybieranie szablonu** wybierz szablon **Aplikacja usługi Azure Service Fabric z obsługą platformy Docker**, a następnie kliknij przycisk **Zastosuj**.

![Wybieranie szablonu kompilacji][select-build-template]

W obszarze **Zadania** wybierz **Hostowany program VS2017** jako **Pulę agentów**.

![Wybieranie zadań][task-agent-pool]

Kliknij opcję **Tagowanie obrazów**.

W obszarze **Typ rejestru kontenerów** wybierz pozycję **Azure Container Registry**. Wybierz wartość pola **Subskrypcja platformy Azure**, a następnie kliknij opcję **Autoryzuj**. Wybierz wartość pola **Azure Container Registry**.

![Wybranie opcji Tagowanie obrazów platformy Docker][select-tag-images]

Kliknij opcję **Wypychanie obrazów**.

W obszarze **Typ rejestru kontenerów** wybierz pozycję **Azure Container Registry**. Wybierz wartość pola **Subskrypcja platformy Azure**, a następnie kliknij opcję **Autoryzuj**. Wybierz wartość pola **Azure Container Registry**.

![Wybranie opcji Wypychanie obrazów platformy Docker][select-push-images]

Na karcie **Wyzwalacze** włącz ciągłą integrację, zaznaczając pozycję **Włącz ciągłą integrację**. W obszarze **Filtry gałęzi** kliknij pozycję **+ Dodaj**, a wartość domyślna pozycji **Specyfikacja gałęzi** zostanie ustawiona na **master**.

W oknie dialogowym **Zapisywanie potoku i kolejki kompilacji** kliknij pozycję **Zapisz i umieść w kolejce**, aby ręcznie uruchomić kompilację.

![Wybieranie wyzwalaczy][save-and-queue]

Kompilacje można również wyzwalać w ramach procesu wypychania lub ewidencjonowania. Aby sprawdzić postęp kompilacji, przejdź do karty **kompilacje** .  Po sprawdzeniu, czy kompilacja została wykonana pomyślnie, zdefiniuj definicję wydania, która wdraża aplikację w klastrze.

### <a name="create-a-release-definition"></a>Tworzenie definicji wydania

Wybierz opcję **Potoki** na panelu po lewej, a następnie pozycje **Wydania** i **+ Nowy potok**.  W obszarze **Wybieranie szablonu** wybierz z listy szablon **Wdrożenie usługi Azure Service Fabric**, a następnie przycisk **Zastosuj**.

![Wybieranie szablonu wydania][select-release-template]

Wybierz pozycję **Zadania**, **Środowisko 1**, a następnie pozycję **+Nowe**, aby dodać nowe połączenie klastra.

![Dodawanie połączenia klastra][add-cluster-connection]

W widoku **Dodawanie nowego połączenia z usługą Service Fabric** wybierz uwierzytelnianie **Na podstawie certyfikatu** lub **Azure Active Directory**.  Określ nazwę połączenia dla klastra „mysftestcluster” i następujący punkt końcowy klastra „tcp://mysftestcluster.southcentralus.cloudapp.azure.com:19000” (lub punkt końcowy klastra, w którym przeprowadzane jest wdrożenie).

W przypadku uwierzytelniania opartego na certyfikatach dodaj **Odcisk palca certyfikatu serwera** dla certyfikatu serwera, którego użyto do utworzenia klastra.  W obszarze **Certyfikat klienta** dodaj plik certyfikatu klienta zakodowany przy użyciu kodowania base-64. Zobacz pomoc podręczną dla tego pola, aby uzyskać informacje dotyczące sposobu uzyskania reprezentacji certyfikatu w kodowaniu base-64. Dodaj również **Hasło** certyfikatu.  Jeśli nie masz osobnego certyfikatu klienta, możesz użyć certyfikatu klastra lub serwera.

W przypadku poświadczeń usługi Azure Active Directory dodaj **Odcisk palca certyfikatu serwera** dla certyfikatu serwera użytego do utworzenia klastra oraz poświadczenia w polach **Nazwa użytkownika** i **Hasło**, których chcesz użyć w celu nawiązania połączenia z klastrem.

Kliknij przycisk **Dodaj**, aby zapisać połączenie klastra.



W obszarze Faza agenta, kliknij opcję **Wdrażanie aplikacji usługi Service Fabric**.
Kliknij przycisk **Ustawienia platformy Docker**, a następnie opcję **Konfiguruj ustawienia platformy Docker**. W obszarze **Źródło poświadczeń rejestru** wybierz opcję **Połączenie usługi Azure Resource Manager**. Następnie wybierz swoją **subskrypcję platformy Azure**.

![Agent potoku wydawania][release-pipeline-agent]

Następnie dodaj artefakt kompilacji do potoku, aby umożliwić definicji wydania odnalezienie danych wyjściowych kompilacji. Wybierz pozycję **Potok**, a następnie **Artefakty**->**+ Dodaj**.  W obszarze **Źródło (definicja kompilacji)** wybierz wcześniej utworzoną definicję kompilacji.  Kliknij przycisk **Dodaj**, aby zapisać artefakt kompilacji.

![Dodawanie artefaktu][add-artifact]

Włącz wyzwalacz ciągłego wdrażania, aby umożliwić automatyczne tworzenie wydania po zakończeniu kompilacji. Kliknij ikonę pioruna w artefakcie, włącz wyzwalacz, a następnie kliknij przycisk **Zapisz** w celu zapisania definicji wydania.

![Włączanie wyzwalacza][enable-trigger]

Wybierz pozycję **+ wydanie**  ->  **Utwórz wersję**  ->  **Utwórz** , aby ręcznie utworzyć wydanie. Postęp wydawania możesz monitorować na karcie **Wydania**.

Sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja została uruchomiona w klastrze.  Otwórz przeglądarkę internetową i przejdź pod adres `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Zwróć uwagę na wersję aplikacji. W tym przykładzie jest to „1.0.0.20170616.3”.

## <a name="commit-and-push-changes-trigger-a-release"></a>Zatwierdzanie i wypychanie zmian, wyzwalanie wydania

Aby upewnić się, że potok ciągłej integracji działa prawidłowo, zaewidencjonuj zmiany kodu w usłudze Azure DevOps.

Podczas pisania kodu zmiany są automatycznie śledzone przez program Visual Studio. Zatwierdź zmiany w lokalnym repozytorium Git, wybierając ikonę oczekujących zmian (![Ikona oczekujących zmian zawiera ołówek i numer.][pending]) na pasku stanu w prawym dolnym rogu.

W widoku **Zmiany** programu Team Explorer dodaj komunikat opisujący aktualizację i zatwierdź wprowadzone zmiany.

![Zatwierdź wszystko][changes]

Wybierz ikonę paska stanu nieopublikowanych zmian (![Nieopublikowane zmiany][unpublished-changes]) lub widok synchronizacji w programie Team Explorer. Wybierz pozycję **Wypchnij**, aby zaktualizować kod w usłudze Azure DevOps Services.

![Wypychanie zmian][push]

Wypychanie zmian do usługi Azure DevOps automatycznie wyzwala kompilację.  Po pomyślnym zakończeniu definicji kompilacji automatycznie tworzone jest wydanie, które rozpoczyna uaktualnianie aplikacji w klastrze.

Aby sprawdzić postęp kompilacji, przejdź do karty **Kompilacje** modułu **Team Explorer** w programie Visual Studio.  Po upewnieniu się, że kompilacja jest wykonywana prawidłowo, określ definicję wydania, która wdraża aplikację w klastrze.

Sprawdź, czy wdrożenie zakończyło się pomyślnie, a aplikacja została uruchomiona w klastrze.  Otwórz przeglądarkę internetową i przejdź pod adres `http://mysftestcluster.southcentralus.cloudapp.azure.com:19080/Explorer/`.  Zwróć uwagę na wersję aplikacji. W tym przykładzie jest to „1.0.0.20170815.3”.

![Zrzut ekranu aplikacji do głosowania w Service Fabric Explorer. Na karcie podstawy zostanie wyróżniona wersja aplikacji "1.0.0.20170815.3".][sfx1]

## <a name="update-the-application"></a>Aktualizowanie aplikacji

Wprowadź zmiany w kodzie aplikacji.  Zapisz i zatwierdź zmiany, postępując zgodnie z poprzednimi krokami.

Po rozpoczęciu uaktualnienia aplikacji możesz obserwować jego postęp w narzędziu Service Fabric Explorer:

![Zrzut ekranu aplikacji do głosowania w Service Fabric Explorer. Komunikat "Uaktualnianie w toku" jest wyróżniony, a aplikacja jest w stanie "Uaktualnij".][sfx2]

Uaktualnianie aplikacji może potrwać kilka minut. Po zakończeniu uaktualniania aplikacja zostanie uruchomiona w następnej wersji.  W tym przykładzie „1.0.0.20170815.4”.

![Zrzut ekranu aplikacji do głosowania w Service Fabric Explorer. Na karcie podstawy zostanie wyróżniona zaktualizowana wersja aplikacji "1.0.0.20170815.4".][sfx3]

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Dodawanie kontroli źródła do projektu
> * Tworzenie definicji kompilacji
> * Tworzenie definicji wydania
> * Automatyczne wdrażanie i uaktualnianie aplikacji

W następnej części tego samouczka dowiesz się, jak skonfigurować [monitorowanie kontenera](service-fabric-tutorial-monitoring-wincontainers.md).

<!-- Image References -->
[publish-app-profile]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishAppProfile.png
[push-git-repo]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishGitRepo.png
[publish-code]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/PublishCode.png
[new-pipeline]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewPipeline.png
[select-build-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectBuildTemplate.png
[task-agent-pool]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/TaskAgentPool.png
[save-and-queue]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SaveAndQueue.png
[select-tag-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerTagImages.png
[select-push-images]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/DockerPushImages.png
[select-release-template]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SelectReleaseTemplate.png
[set-continuous-integration]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SetContinuousIntegration.png
[add-cluster-connection]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddClusterConnection.png
[release-pipeline-agent]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/ReleasePipelineAgent.png
[add-artifact]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/AddArtifact.png
[enable-trigger]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/EnableTrigger.png
[sfx1]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX1.png
[sfx2]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX2.png
[sfx3]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/SFX3.png
[pending]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Pending.png
[changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Changes.png
[unpublished-changes]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/UnpublishedChanges.png
[push]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/Push.png
[continuous-delivery-with-VSTS]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/VSTS-Dialog.png
[new-service-endpoint]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpoint.png
[new-service-endpoint-dialog]: ./media/service-fabric-tutorial-deploy-container-app-with-cicd-vsts/NewServiceEndpointDialog.png
