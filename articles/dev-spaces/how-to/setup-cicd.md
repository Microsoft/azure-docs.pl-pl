---
title: Używanie ciągłej integracji/ciągłego wdrażania w ramach usługi Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Dowiedz się, jak skonfigurować ciągłą integrację/ciągłe wdrażanie przy użyciu usługi Azure DevOps z Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Azure Container Service, kontenery
ms.openlocfilehash: c7b3eba0bea85082dbb4e39d108af9471d5dc45e
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "88080270"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Korzystanie z potoków ciągłej integracji/ciągłego wdrażania za pomocą obszarów Azure Dev Spaces

Ten artykuł przeprowadzi Cię przez proces konfigurowania ciągłej integracji/ciągłego wdrażania (CI/CD) w usłudze Azure Kubernetes Service (AKS) z włączonymi opcjami dev Spaces. Ciągłej integracji/ciągłego wdrażania z AKS umożliwia automatyczne wdrażanie aktualizacji aplikacji przy każdym wypchnięciu przekazanego kodu do repozytorium źródłowego. Używanie ciągłej integracji/ciągłego wdrażania z klastrem z obsługą funkcji "dev Spaces" jest przydatne, ponieważ może to spowodować, że linia bazowa aplikacji jest aktualna dla zespołu.

![Przykładowy diagram ciągłej integracji/ciągłego wdrażania](../media/common/ci-cd-simple.png)

Mimo że ten artykuł prowadzi użytkownika przez usługę Azure DevOps, te same koncepcje dotyczą systemów ciągłej integracji/ciągłego wdrażania, takich jak Jenkins, TeamCity itd.

## <a name="prerequisites"></a>Wymagania wstępne
* Klaster usługi Azure Kubernetes Service (AKS) z włączonym Azure Dev Spaces
* [Zainstalowano interfejs wiersza polecenia Azure Dev Spaces](upgrade-tools.md)
* [Organizacja usługi Azure DevOps z projektem](/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Dostępne są Azure Container Registry szczegóły [konta administratora](../../container-registry/container-registry-authentication.md#admin-account)
* [Autoryzuj klaster AKS do ściągania z Azure Container Registry](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Pobierz przykładowy kod
Na przykład utworzymy rozwidlenie naszego przykładowego repozytorium GitHub. Przejdź do https://github.com/Azure/dev-spaces i wybierz **rozwidlenie**. Po zakończeniu procesu rozwidlenia ponownie **Sklonuj** swoją wersję rozwidlenia repozytorium lokalnie. Domyślnie gałąź _główna_ zostanie wyewidencjonowana, ale dodaliśmy pewne zmiany czasu w gałęzi _azds_updates_ , które powinny być również przetransferowane podczas rozwidlenia. Gałąź _azds_updates_ zawiera aktualizacje, z których prosimy o ręczne wprowadzenie w sekcjach samouczka dotyczącej miejsc deweloperskich, a także niektórych wstępnie wprowadzonych plików YAML i JSON w celu usprawnienia wdrożenia systemu ciągłej integracji/ciągłego wdrażania. Możesz użyć polecenia, `git checkout -b azds_updates origin/azds_updates` aby wyewidencjonować gałąź _azds_updates_ w lokalnym repozytorium.

## <a name="dev-spaces-setup"></a>Konfiguracja miejsc dev
Utwórz nowe miejsce o nazwie _dev_ przy użyciu `azds space select` polecenia. Miejsce _dev_ będzie używane przez potok ciągłej integracji/ciągłego wdrażania, aby wypchnąć zmiany kodu. Będzie również używany do tworzenia _miejsc potomnych_ opartych na _dev_.

```cmd
azds space select -n dev
```

Po wyświetleniu monitu o wybranie nadrzędnego obszaru deweloperskiego wybierz opcję _\<none\>_ .

Po utworzeniu obszaru deweloperskiego należy określić sufiks hosta. Użyj `azds show-context` polecenia, aby wyświetlić sufiks hosta Azure dev Spacesego kontrolera transferu danych przychodzących.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

W powyższym przykładzie sufiks hosta to _fedcba098.EUS.azds.IO_. Ta wartość jest używana później podczas tworzenia definicji wydania.

Przestrzeń _dev_ zawsze będzie zawierać najnowszy stan repozytorium, linię bazową, dzięki czemu deweloperzy mogą tworzyć _miejsca podrzędne_ od _deweloperskiego_ , aby testować ich izolowane zmiany w kontekście większej aplikacji. Koncepcje te zostały omówione bardziej szczegółowo w samouczkach dotyczących funkcji miejsca do magazynowania.

## <a name="creating-the-build-definition"></a>Tworzenie definicji kompilacji
Otwórz projekt zespołowy platformy Azure DevOps w przeglądarce internetowej i przejdź do sekcji _potoki_ . Najpierw kliknij zdjęcie profilu w prawym górnym rogu witryny Azure DevOps, Otwórz okienko funkcje w wersji zapoznawczej i Wyłącz _nowe środowisko tworzenia potoku YAML_:

![Otwieranie okienka funkcji podglądu](../media/common/preview-feature-open.png)

Opcja wyłączenia:

![Nowy opcja środowiska tworzenia potoku YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Funkcja wersji zapoznawczej _tworzenia potoku_ usługi Azure DEVOPS New YAML jest w tej chwili w konflikcie z tworzeniem wstępnie zdefiniowanych potoków kompilacji. Musisz ją wyłączyć teraz, aby wdrożyć wstępnie zdefiniowany potok kompilacji.

W gałęzi _azds_updates_ dodaliśmy PROSTą [YAML potoku platformy Azure](/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) , która definiuje kroki kompilacji wymagane dla *mywebapi* i *webfrontonu*.

W zależności od wybranego języka potok YAML został zaewidencjonowany w ścieżce podobnej do: `samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Aby utworzyć potok z tego pliku:
1. Na stronie głównej projektu DevOps przejdź do potoków > kompilacje.
1. Wybierz opcję utworzenia **nowego** potoku kompilacji.
1. W razie potrzeby wybierz pozycję **GitHub** jako źródło, Autoryzuj z kontem usługi GitHub, a następnie wybierz gałąź _azds_updates_ z wersji z rozwidleniem repozytorium aplikacji przykładowej dla _deweloperów_ .
1. Wybierz pozycję **Konfiguracja jako kod**lub **YAML**, jako szablon.
1. Zostanie wyświetlona strona konfiguracji potoku kompilacji. Jak wspomniano powyżej, przejdź do ścieżki **pliku YAML** , używając przycisku.. **.** . Na przykład `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Przejdź do karty **zmienne** .
1. Ręcznie Dodaj _dockerId_ jako zmienną, która jest nazwą użytkownika [konta administratora Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). (Wymienione w artykule wymagania wstępne artykułu)
1. Ręcznie Dodaj _dockerPassword_ jako zmienną, która jest hasłem [konta administratora Azure Container Registry](../../container-registry/container-registry-authentication.md#admin-account). Pamiętaj, aby określić _dockerPassword_ jako wpis tajny (wybierając ikonę kłódki) ze względów bezpieczeństwa.
1. Wybierz pozycję **zapisz & kolejkę**.

Masz teraz rozwiązanie CI, które automatycznie kompiluje *mywebapi* i *webfronton* dla każdej aktualizacji wypychanej do gałęzi _azds_updates_ Twojego rozwidlenia w serwisie GitHub. Możesz sprawdzić, czy obrazy platformy Docker zostały wypchnięte, przechodząc do Azure Portal, wybierając Azure Container Registry i przeglądając kartę **repozytoria** . Kompilowanie obrazów i wyświetlanie ich w rejestrze kontenerów może potrwać kilka minut.

![Repozytoria Azure Container Registry](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Tworzenie definicji wydania

1. Na stronie głównej projektu DevOps, przejdź do potoków > wersje
1. Jeśli pracujesz w projekcie DevOps, który nie zawiera jeszcze definicji wersji, musisz najpierw utworzyć pustą definicję wydania przed kontynuowaniem. Opcja importowania nie jest wyświetlana w interfejsie użytkownika, dopóki nie masz istniejącej definicji wydania.
1. Po lewej stronie kliknij przycisk **+ Nowy** , a następnie kliknij przycisk **Importuj potok**.
1. Kliknij przycisk **Przeglądaj** i wybierz `samples/release.json` z projektu.
1. Kliknij przycisk **OK**. Zwróć uwagę, że okienko potoku zostało załadowane ze stroną edytowania definicji wersji. Zauważ również, że istnieją czerwone ikony ostrzeżeń wskazujące szczegóły dotyczące klastra, które nadal muszą zostać skonfigurowane.
1. Po lewej stronie okienka potoku kliknij pozycję Dodaj dymek **artefaktu** .
1. Z listy rozwijanej **Źródło** wybierz utworzony wcześniej potok kompilacji.
1. W przypadku **wersji domyślnej**wybierz pozycję **Najnowsza z gałęzi domyślnej potok kompilacji z tagami**.
1. Pozostaw **Tagi** puste.
1. Ustaw **alias źródłowy** na `drop` . Wartość **aliasu Source** jest używana przez wstępnie zdefiniowane zadania wydania, aby musiała zostać ustawiona.
1. Kliknij pozycję **Dodaj**.
1. Teraz kliknij ikonę błyskawicy dla nowo utworzonego `drop` źródła artefaktu, jak pokazano poniżej:

    ![Konfiguracja ciągłego wdrażania artefaktu wydania](../media/common/release-artifact-cd-setup.png)
1. Włącz **wyzwalacz ciągłego wdrażania**.
1. Umieść kursor na karcie **zadania** obok pozycji **potok** i kliknij pozycję _dev_ (Programowanie), aby edytować zadania _deweloperskie_ .
1. Sprawdź, czy w obszarze **Typ połączenia** wybrano **Azure Resource Manager** . zobaczysz trzy kontrolki listy rozwijanej wyróżnione kolorem czerwonym: ![ Konfiguracja definicji wydania](../media/common/release-setup-tasks.png)
1. Wybierz subskrypcję platformy Azure, której używasz, Azure Dev Spaces. Może być również konieczne kliknięcie przycisku **Autoryzuj**.
1. Wybierz grupę zasobów i klaster, z których korzystasz, Azure Dev Spaces.
1. Kliknij pozycję **zadanie agenta**.
1. Wybierz pozycję **hostowane Ubuntu 1604** w obszarze **Pula agentów**.
1. Umieść kursor nad selektorem **zadań** u góry, kliknij pozycję _produkcyjny_ , aby edytować _zadania etapów_ produkcji.
1. Sprawdź, czy w obszarze **Typ połączenia** wybrano **Azure Resource Manager** . i wybierz subskrypcję platformy Azure, grupę zasobów i klaster, z których korzystasz, Azure Dev Spaces.
1. Kliknij pozycję **zadanie agenta**.
1. Wybierz pozycję **hostowane Ubuntu 1604** w obszarze **Pula agentów**.
1. Kliknij kartę **zmienne** , aby zaktualizować zmienne dla wydania.
1. Zaktualizuj wartość **DevSpacesHostSuffix** z **UPDATE_ME** do sufiksu hosta. Sufiks hosta jest wyświetlany, gdy `azds show-context` polecenie zostało wcześniej uruchomione.
1. Kliknij przycisk **Zapisz** w prawym górnym rogu i **OK**.
1. Kliknij pozycję **+ wersja** (obok przycisku Zapisz) i **Utwórz wydanie**.
1. W obszarze **artefakty**Sprawdź, czy jest wybrana Najnowsza kompilacja z potoku kompilacji.
1. Kliknij przycisk **Utwórz**.

Proces zautomatyzowanej wersji rozpocznie się teraz, wdrażając wykresy *mywebapi* i *webfrontonu* w klastrze Kubernetes w _obszarze najwyższego_ poziomu. Postęp wydania można monitorować w portalu sieci Web usługi Azure DevOps:

1. Przejdź do sekcji **wersje** w obszarze **potoki**.
1. Kliknij potok wydania dla przykładowej aplikacji.
1. Kliknij nazwę najnowszej wersji.
1. Umieść kursor nad polem **dev** w obszarze **etapy** , a następnie kliknij pozycję **dzienniki**.

Wydanie jest wykonywane po zakończeniu wszystkich zadań.

> [!TIP]
> Jeśli wydanie nie powiedzie się, komunikat o błędzie, na przykład *uaktualnienie nie powiodło się: przekroczono limit czasu podczas oczekiwania na warunek*, spróbuj przeprowadzić inspekcję w klastrze [za pomocą pulpitu nawigacyjnego Kubernetes](../../aks/kubernetes-dashboard.md). Jeśli zobaczysz, że nie można rozpocząć pracy z identyfikatorami, na przykład z komunikatami o błędach *nie można ściągnąć obrazu "azdsexample.azurecr.IO/mywebapi:122": błąd wywołania RPC: Code = nieznany DESC = odpowiedź na błąd z demona: Get https: \/ /azdsexample.azurecr.IO/v2/mywebapi/Manifests/122: unautoryzowane: wymagane jest uwierzytelnienie*, może to być spowodowane tym, że klaster nie uzyskał autoryzacji do ściągania z Azure Container Registry. Upewnij się, że wykonano [autoryzację klastra AKS do ściągania z poziomu](../../aks/cluster-container-registry-integration.md) wymagań wstępnych Azure Container Registry.

Teraz masz w pełni zautomatyzowany potok ciągłej integracji/ciągłego wdrażania dla rozwidlenia w serwisie GitHub przykładowych aplikacji. Za każdym razem, gdy zatwierdzisz i wypychasz kod, potok kompilacji utworzy i wypycha obrazy *mywebapi* i *webfrontonu* do wystąpienia niestandardowego ACR. Następnie potok wersji wdroży wykres Helm dla każdej aplikacji w obszarze _dev_ w klastrze z obsługą miejsc dev Spaces.

## <a name="accessing-your-_dev_-services"></a>Uzyskiwanie dostępu do usług _deweloperskich_
Po wdrożeniu można uzyskać dostęp do _deweloperskiej_ wersji programu *webfrontonu* z publicznym adresem URL, takim jak: `http://dev.webfrontend.fedcba098.eus.azds.io` . Ten adres URL można znaleźć, uruchamiając `azds list-uri` polecenie: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Wdrażanie w środowisku produkcyjnym

Aby ręcznie podnieść szczególną wersję do _prod_ przy użyciu systemu ciągłej integracji/ciągłego tworzenia w tym samouczku:
1. Przejdź do sekcji **wersje** w obszarze **potoki**.
1. Kliknij potok wydania dla przykładowej aplikacji.
1. Kliknij nazwę najnowszej wersji.
1. Umieść kursor nad polem **prod** w obszarze **etapy** , a następnie kliknij pozycję **Wdróż**.
    ![Podwyższanie poziomu do środowiska promocyjnego](../media/common/prod-promote.png)
1. W obszarze **etapy** ponownie umieść wskaźnik myszy nad polem **produkcyjnym** , a następnie kliknij pozycję **dzienniki**.

Wydanie jest wykonywane po zakończeniu wszystkich zadań.

Etap _produkcyjny_ potoku ciągłej integracji i ciągłego wdrażania korzysta z modułu równoważenia obciążenia, a nie z poziomu kontrolera usługi miejsca dev Spaces w celu zapewnienia dostępu do usług produkcji _produkcyjnej_ . Usługi wdrożone na etapie _produkcyjnym_ są dostępne jako adresy IP zamiast nazw DNS. W środowisku produkcyjnym można utworzyć własny kontroler transferu danych przychodzących, aby hostować usługi na podstawie własnej konfiguracji DNS.

Aby określić adres IP usługi webfrontonu, kliknij krok  **Drukuj publiczny adres IP webfrontonu** , aby rozwinąć dane wyjściowe dziennika. Użyj adresu IP wyświetlanego w danych wyjściowych dziennika, aby uzyskać dostęp do aplikacji **webfrontonu** .

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Instrumentacja miejsc dev w środowisku produkcyjnym
Mimo że Instrumentacja Spaces dev została zaprojektowana tak, aby _nie_ była w normalnym działaniu aplikacji, zalecamy uruchamianie obciążeń produkcyjnych w przestrzeni nazw Kubernetes, która nie jest włączona z funkcją dev Spaces. Użycie tego typu przestrzeni nazw Kubernetes oznacza, że należy utworzyć produkcyjną przestrzeń nazw przy użyciu `kubectl` interfejsu wiersza polecenia lub zezwolić systemowi ciągłej integracji/ciągłego tworzenia go podczas pierwszego wdrożenia Helm. _Zaznaczanie_ lub Tworzenie miejsca przy użyciu narzędzi do tworzenia miejsc dev spowoduje dodanie do tej przestrzeni nazw Instrumentacji miejsca do magazynowania.

Poniżej znajduje się przykładowa struktura przestrzeni nazw, która obsługuje programowanie funkcji, środowisko deweloperskie _i_ produkcyjne wszystkie w jednym klastrze Kubernetes:

![Przykładowa struktura przestrzeni nazw](../media/common/cicd-namespaces.png)

> [!Tip]
> Jeśli już utworzono `prod` miejsce, a po prostu chcesz wykluczyć je z Instrumentacji miejsca do magazynowania (bez usuwania tego elementu), możesz to zrobić za pomocą następującego polecenia:
>
> `azds space remove -n prod --no-delete`
>
> Może być konieczne usunięcie wszystkich zasobników w `prod` przestrzeni nazw po wykonaniu tej czynności, aby można było je odtworzyć bez Instrumentacji funkcji Spaces.

## <a name="next-steps"></a>Następne kroki

Dowiedz się więcej o tym, jak działa Azure Dev Spaces.

> [!div class="nextstepaction"]
> [Jak działa usługa Azure Dev Spaces](../how-dev-spaces-works.md)
