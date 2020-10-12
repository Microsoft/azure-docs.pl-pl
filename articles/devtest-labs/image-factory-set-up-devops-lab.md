---
title: Uruchamianie fabryki obrazu z usługi Azure DevOps w Azure DevTest Labs
description: W tym artykule opisano wszystkie przygotowania, które są konieczne do uruchomienia fabryki obrazu z usługi Azure DevOps (dawniej Visual Studio Team Services).
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: fa7050bae1ff8681e04b6ab38220be9eaf38a64a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "85476142"
---
# <a name="run-an-image-factory-from-azure-devops"></a>Uruchamianie fabryki obrazów z usługi Azure DevOps
W tym artykule opisano wszystkie przygotowania, które są konieczne do uruchomienia fabryki obrazu z usługi Azure DevOps (dawniej Visual Studio Team Services).

> [!NOTE]
> Zostanie zadziałał dowolny aparat aranżacji. Usługa Azure DevOps nie jest obowiązkowa. Fabryka obrazów jest uruchamiana przy użyciu skryptów Azure PowerShell, więc można ją uruchomić ręcznie, używając systemu Windows Harmonogram zadań, innych systemów ciągłej integracji/ciągłego wdrażania i tak dalej.

## <a name="create-a-lab-for-the-image-factory"></a>Tworzenie laboratorium dla fabryki obrazu
Pierwszym krokiem w konfigurowaniu fabryki obrazów jest utworzenie laboratorium w Azure DevTest Labs. To laboratorium jest laboratorium fabryki obrazu, w którym tworzymy maszyny wirtualne i zapisujesz obrazy niestandardowe. To laboratorium jest traktowane jako część ogólnego procesu fabryki obrazu. Po utworzeniu laboratorium upewnij się, że nazwa została zapisana, ponieważ będzie potrzebna później.

## <a name="scripts-and-templates"></a>Skrypty i szablony
Następnym krokiem w przyjęciu fabryki obrazu dla zespołu jest zrozumienie, co jest dostępne. Skrypty i szablony fabryki obrazów są dostępne publicznie w [repozytorium GitHub DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory). Oto konspekt elementów:

- Fabryka obrazów. Jest to folder główny.
    - Skonfigurować. Dane wejściowe do fabryki obrazu
        - GoldenImages. Ten folder zawiera pliki JSON, które reprezentują definicje obrazów niestandardowych.
        - Labs.js. Plik, w którym zespoły rejestrują się w celu otrzymywania określonych obrazów niestandardowych.
- Znajduj. Aparat dla fabryki obrazu.

Artykuły w tej sekcji zawierają więcej szczegółowych informacji o tych skryptach i szablonach.

## <a name="create-an-azure-devops-team-project"></a>Utwórz projekt zespołowy usługi Azure DevOps
Usługa Azure DevOps umożliwia przechowywanie kodu źródłowego i uruchamianie Azure PowerShell w jednym miejscu. Można zaplanować cykliczne uruchomienia, aby zachować aktualność obrazów. Istnieją dobre funkcje rejestrowania wyników w celu zdiagnozowania wszelkich problemów.  Korzystanie z usługi Azure DevOps nie jest jednak wymagane, można użyć dowolnego programu lub aparatu, który może nawiązać połączenie z platformą Azure i można uruchomić Azure PowerShell.

Jeśli masz istniejące konto DevOps lub projekt, którego chcesz użyć zamiast tego, Pomiń ten krok.

Aby rozpocząć, utwórz bezpłatne konto w usłudze Azure DevOps. Odwiedź stronę https://www.visualstudio.com/ i wybierz pozycję **Rozpocznij za darmo** bezpośrednio w obszarze **Azure DEVOPS** (dawniej VSTS). Musisz wybrać unikatową nazwę konta i upewnić się, że chcesz zarządzać kodem przy użyciu narzędzia Git. Po utworzeniu należy zapisać adres URL projektu zespołowego. Oto przykładowy adres URL: `https://<accountname>.visualstudio.com/MyFirstProject` .

## <a name="check-in-the-image-factory-to-git"></a>Zaewidencjonuj fabrykę obrazów do usługi git
Wszystkie środowiska programu PowerShell, szablony i konfiguracje dla fabryki obrazów znajdują się w [repozytorium Public DevTest Labs](https://github.com/Azure/azure-devtestlab/tree/master/samples/DevTestLabs/Scripts/ImageFactory)w witrynie GitHub. Najszybszym sposobem na uzyskanie kodu do nowego projektu zespołowego jest zaimportowanie repozytorium. Spowoduje to ściągnięcie całego repozytorium DevTest Labs (dzięki czemu uzyskasz dodatkowe dokumenty i przykłady).

1. Przejdź do projektu DevOps platformy Azure, który został utworzony w poprzednim kroku (adres URL wygląda podobnie do **protokołu https: \/ / \<accountname> . VisualStudio.com/MyFirstProject**).
2. Wybierz pozycję **Importuj repozytorium**.
3. Wprowadź **adres URL klonowania** repozytorium DevTest Labs: `https://github.com/Azure/azure-devtestlab` .
4. Wybierz pozycję **Importuj**.

    ![Importuj repozytorium git](./media/set-up-devops-lab/import-git-repo.png)

Jeśli zdecydujesz się tylko na to, co jest potrzebne (pliki fabryki obrazów), wykonaj kroki opisane [tutaj](https://www.visualstudio.com/en-us/docs/git/share-your-code-in-git-vs) , aby sklonować repozytorium git i wypchnąć tylko te pliki, które znajdują się w katalogu **scripts/ImageFactory** .

## <a name="create-a-build-and-connect-to-azure"></a>Tworzenie kompilacji i nawiązywanie połączenia z platformą Azure
W tym momencie pliki źródłowe są przechowywane w repozytorium Git w usłudze Azure DevOps. Teraz musisz skonfigurować potok, aby uruchomić Azure PowerShell. Istnieje wiele opcji wykonywania tych kroków. W tym artykule opisano, jak używać definicji kompilacji dla uproszczenia, ale współpracuje z DevOps kompilacją, wydaniem DevOps (pojedynczym lub wieloma środowiskami), innymi aparatami wykonywania, takimi jak Windows Harmonogram zadań lub dowolnym innym programem, który można wykonać Azure PowerShell.

> [!NOTE]
> Należy pamiętać, że niektóre pliki programu PowerShell są czasochłonne, gdy istnieje dużo (10 +) obrazów niestandardowych do utworzenia. Bezpłatny agenci DevOps kompilacji/wydania mają limit czasu 30 minut, więc nie można użyć bezpłatnego hostowanego agenta po rozpoczęciu tworzenia wielu obrazów. To żądanie przekroczenia limitu czasu ma zastosowanie do dowolnego zespołu, który ma być używany, warto sprawdzić, czy można przedłużyć typowe limity czasu dla długotrwałych skryptów Azure PowerShell. W przypadku usługi Azure DevOps można użyć płatnych agentów hostowanych lub użyć własnego agenta kompilacji.

1. Aby rozpocząć, wybierz pozycję **Skonfiguruj kompilację** na stronie głównej projektu DevOps:

    ![Przycisk Kompilacja instalacji](./media/set-up-devops-lab/setup-build-button.png)
2. Określ **nazwę** kompilacji (na przykład: Kompiluj i dostarczaj obrazy do DevTest Labs).
3. Wybierz **pustą** definicję kompilacji, a następnie wybierz pozycję **Zastosuj** , aby utworzyć kompilację.
4. Na tym etapie możesz wybrać pozycję **hostowane** dla agenta kompilacji.
5. **Zapisz** definicję kompilacji.

    ![Definicja kompilacji](./media/set-up-devops-lab/build-definition.png)

## <a name="configure-the-build-variables"></a>Skonfiguruj zmienne kompilacji
Aby uprościć parametry wiersza polecenia, należy hermetyzować wartości klucza, które tworzą fabrykę obrazu, do zestawu zmiennych kompilacji. Wybierz kartę **zmienne** , a zobaczysz listę kilku domyślnych zmiennych. Poniżej znajduje się lista zmiennych do wprowadzenia do usługi Azure DevOps:


| Nazwa zmiennej | Wartość | Uwagi |
| ------------- | ----- | ----- |
| ConfigurationLocation | /Scripts/ImageFactory/Configuration | To jest pełna ścieżka do folderu **konfiguracji** w repozytorium. W przypadku zaimportowania całego repozytorium powyżej wartość z lewej strony jest poprawna. W przeciwnym razie Aktualizuj, aby wskazywała lokalizację konfiguracji. |
| DevTestLabName | MyImageFactory | Nazwa laboratorium w Azure DevTest Labs używana jako fabryka do tworzenia obrazów. Jeśli go nie masz, utwórz go. Upewnij się, że laboratorium znajduje się w tej samej subskrypcji, do której ma dostęp punkt końcowy usługi. |
| ImageRetention | 1 | Liczba obrazów, które mają zostać zapisane dla każdego typu. Ustaw wartość domyślną na 1. |
| MachinePassword | ******* | Wbudowane hasło konta administratora dla maszyn wirtualnych. Jest to konto przejściowe, dlatego upewnij się, że jest bezpieczne. Wybierz małą ikonę kłódki po prawej stronie, aby upewnić się, że jest to bezpieczny ciąg. |
| MachineUserName | ImageFactoryUser | Nazwa użytkownika wbudowanego konta administratora dla maszyn wirtualnych. Jest to konto przejściowe. |
| StandardTimeoutMinutes | 30 | Limit czasu oczekiwania na regularne operacje na platformie Azure. |
| SubscriptionId |  0000000000-0000-0000-0000-0000000000000 | Identyfikator subskrypcji, w której istnieje laboratorium i że punkt końcowy usługi ma dostęp do programu. |
| VMSize | Standardowa_A3 | Rozmiar maszyny wirtualnej, która ma zostać użyta na potrzeby kroku **tworzenia** . Utworzone maszyny wirtualne są przejściowe. Rozmiar musi być tym, który jest [włączony dla laboratorium](devtest-lab-set-lab-policy.md). Upewnij się, że osiągnięto [limit przydziału rdzeni dla subskrypcji](../azure-resource-manager/management/azure-subscription-service-limits.md).

![Zmienne kompilacji](./media/set-up-devops-lab/configure-build-variables.png)

## <a name="connect-to-azure"></a>Nawiązywanie połączenia z usługą Azure
Następnym krokiem jest skonfigurowanie nazwy głównej usługi. Jest to tożsamość w Azure Active Directory, która umożliwia agentowi kompilacji DevOps działanie na platformie Azure w imieniu użytkownika. Aby je skonfigurować, Zacznij od dodania najpierw Azure PowerShell kroku kompilacji.

1. Wybierz pozycję **Dodaj zadanie**.
2. Wyszukaj **Azure PowerShell**.
3. Po jego znalezieniu wybierz pozycję **Dodaj** , aby dodać zadanie do kompilacji. Gdy to zrobisz, zobaczysz, że zadanie pojawia się po lewej stronie jako dodane.

![Konfigurowanie kroku programu PowerShell](./media/set-up-devops-lab/set-up-powershell-step.png)

Najszybszą metodą skonfigurowania jednostki usługi jest umożliwienie usłudze Azure DevOps jej dla nas.

1. Wybierz właśnie dodane **zadanie** .
2. W obszarze **Typ połączenia platformy Azure**wybierz pozycję **Azure Resource Manager**.
3. Wybierz link **Zarządzaj** , aby skonfigurować nazwę główną usługi.

Aby uzyskać więcej informacji, zobacz ten [wpis w blogu](https://devblogs.microsoft.com/devops/automating-azure-resource-group-deployment-using-a-service-principal-in-visual-studio-online-buildrelease-management/). Po wybraniu linku **Zarządzanie** zostanie wystawione odpowiednie miejsce w DevOps (drugi zrzut ekranu w wpisie w blogu), aby skonfigurować połączenie z platformą Azure. Pamiętaj o wybraniu **Azure Resource Manager punktu końcowego usługi** podczas konfigurowania tego ustawienia.

## <a name="complete-the-build-task"></a>Ukończ zadanie kompilacji
W przypadku wybrania zadania kompilacji w okienku po prawej stronie zostaną wyświetlone wszystkie szczegóły, które powinny zostać wypełnione.

1. Najpierw Nazwij zadanie kompilacji: **utwórz Virtual Machines**.
2. Wybierz jednostkę **usługi** utworzoną przez wybranie **Azure Resource Manager**
3. Wybierz **punkt końcowy usługi**.
4. W obszarze **ścieżka skryptu**wybierz pozycję **... (wielokropek)** po prawej stronie.
5. Przejdź do **MakeGoldenImageVMs.ps1** skryptu.
6. Parametry skryptu powinny wyglądać następująco: `-ConfigurationLocation $(System.DefaultWorkingDirectory)$(ConfigurationLocation) -DevTestLabName $(DevTestLabName) -vmSize $(VMSize) -machineUserName $(MachineUserName) -machinePassword (ConvertTo-SecureString -string '$(MachinePassword)' -AsPlainText -Force) -StandardTimeoutMinutes $(StandardTimeoutMinutes)`

    ![Ukończ definicję kompilacji](./media/set-up-devops-lab/complete-build-definition.png)


## <a name="queue-the-build"></a>Kolejkowanie kompilacji
Sprawdźmy, czy wszystko jest poprawnie skonfigurowane, aby kolejkować nową kompilację. Gdy kompilacja jest uruchomiona, przełącz się do [Azure Portal](https://portal.azure.com) i wybierz pozycję **wszystkie Virtual Machines** w laboratorium fabryki obrazu, aby upewnić się, że wszystko działa poprawnie. Należy zobaczyć trzy maszyny wirtualne, które zostaną utworzone w laboratorium.

![Maszyny wirtualne w laboratorium](./media/set-up-devops-lab/vms-in-lab.png)

## <a name="next-steps"></a>Następne kroki
Pierwszy krok konfigurowania fabryki obrazów na podstawie Azure DevTest Labs został ukończony. W następnym artykule z tej serii te maszyny wirtualne są uogólnione i zapisywane w obrazach niestandardowych. Następnie są one dystrybuowane do wszystkich innych laboratoriów. Zapoznaj się z następnym artykułem z serii: [Zapisywanie obrazów niestandardowych i dystrybucja do wielu laboratoriów](image-factory-save-distribute-custom-images.md).
