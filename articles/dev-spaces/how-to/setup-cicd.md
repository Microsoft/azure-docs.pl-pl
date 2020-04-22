---
title: Używanie ciągłej integracji/ciągłego wdrażania w ramach usługi Azure Dev Spaces
services: azure-dev-spaces
author: DrEsteban
ms.author: stevenry
ms.date: 12/17/2018
ms.topic: conceptual
manager: gwallace
description: Dowiedz się, jak skonfigurować ciągłą integrację/ciągłe wdrażanie przy użyciu usługi Azure DevOps za pomocą usługi Azure Dev Spaces
keywords: Docker, Kubernetes, Azure, AKS, Usługa kontenera azure, kontenery
ms.openlocfilehash: f2eb9449518b32ab74f2dbbca6b5489aed325db7
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81685634"
---
# <a name="use-cicd-with-azure-dev-spaces"></a>Korzystanie z potoków ciągłej integracji/ciągłego wdrażania za pomocą obszarów Azure Dev Spaces

W tym artykule przewodnik po konfigurowaniu ciągłej integracji/ciągłego wdrażania (CI/CD) do usługi Azure Kubernetes Service (AKS) z włączoną funkcją Dev Spaces. Ci/CD do AKS umożliwia automatyczne wdrażanie aktualizacji aplikacji za każdym razem, gdy zatwierdzony kod jest wypychany do repozytorium źródłowego. Korzystanie z ciągłej integracji/ciągłego wdrażania w połączeniu z klastrem włączonym przestrzeniami deweloperskimi jest przydatne, ponieważ może on aktualizować plan bazowy aplikacji dla zespołu.

![Przykładowy diagram ciągłej integracji/ciągłego wdrażania](../media/common/ci-cd-simple.png)

Chociaż w tym artykule przewodnik po platformie Azure DevOps, te same pojęcia będą miały zastosowanie do systemów ciągłej integracji/ciągłego wdrażania, takich jak Jenkins, TeamCity itp.

## <a name="prerequisites"></a>Wymagania wstępne
* [Klaster usługi Azure Kubernetes (AKS) z włączoną usługą Azure Dev Spaces](../get-started-netcore.md)
* [Zainstalowano narzędzie CLI usługi Azure Dev Spaces](upgrade-tools.md)
* [Organizacja Azure DevOps z projektem](https://docs.microsoft.com/azure/devops/user-guide/sign-up-invite-teammates?view=vsts)
* [Usługa Azure Container Registry (ACR)](../../container-registry/container-registry-get-started-azure-cli.md)
    * Dostępne szczegóły [konta administratora](../../container-registry/container-registry-authentication.md#admin-account) rejestru kontenerów platformy Azure
* [Autoryzowanie klastra AKS do ściągania z rejestru kontenerów platformy Azure](../../aks/cluster-container-registry-integration.md)

## <a name="download-sample-code"></a>Pobierz przykładowy kod
Ze względu na czas utwórzmy rozwidlony nasz przykładowy kod repozytorium GitHub. Przejdź https://github.com/Azure/dev-spaces do i wybierz pozycję **Widelec**. Po zakończeniu procesu rozwidlenia **sklonuj** rozwidloną wersję repozytorium lokalnie. Domyślnie gałąź _główna_ zostanie wyewidencjonowana, ale uwzględniliśmy pewne oszczędzające czas zmiany w _gałęzi azds_updates,_ które również powinny zostać przeniesione podczas rozwidlacza. Gałąź _azds_updates_ zawiera aktualizacje, o których ręcznie prosimy w sekcjach samouczków Dev Spaces, a także niektóre wstępnie wykonane pliki YAML i JSON w celu usprawnienia wdrażania systemu ciągłej integracji/ciągłego wdrażania. Za pomocą polecenia `git checkout -b azds_updates origin/azds_updates` można wyewidencjonować gałąź _azds_updates_ w lokalnym repozytorium.

## <a name="dev-spaces-setup"></a>Konfiguracja przestrzeni deweloperskich
Utwórz nową _dev_ przestrzeń o `azds space select` nazwie dev za pomocą polecenia. Przestrzeń _deweloperska_ będzie używana przez potok ciągłej integracji/ciągłego wdrażania do wypychania zmian kodu. Będzie również używany do tworzenia _przestrzeni podrzędnych_ na podstawie _dev_.

```cmd
azds space select -n dev
```

Po wyświetleniu monitu o wybranie nadrzędnego obszaru dewelopera wybierz _ \<brak\>_.

Po utworzeniu miejsca dewelopera należy określić sufiks hosta. Użyj `azds show-context` polecenia, aby wyświetlić sufiks hosta kontrolera transferu danych przychodzących usługi Azure Dev Spaces.

```cmd
$ azds show-context
Name   ResourceGroup    DevSpace  HostSuffix
-----  ---------------  --------  ----------------------
MyAKS  MyResourceGroup  dev       fedcba098.eus.azds.io
```

W powyższym przykładzie sufiks hosta jest _fedcba098.eus.azds.io_. Ta wartość jest używana później podczas tworzenia definicji wersji.

Przestrzeń _dewelopera_ zawsze będzie zawierać najnowszy stan repozytorium, linii bazowej, dzięki czemu deweloperzy mogą tworzyć _przestrzenie podrzędne_ z _dev,_ aby przetestować ich izolowane zmiany w kontekście większej aplikacji. Ta koncepcja została omówiona bardziej szczegółowo w samouczkach Dev Spaces.

## <a name="creating-the-build-definition"></a>Tworzenie definicji kompilacji
Otwórz projekt zespołu programu Azure DevOps w przeglądarce sieci Web i przejdź do sekcji _Potoki._ Najpierw kliknij zdjęcie profilowe w prawym górnym rogu witryny Azure DevOps, otwórz okienko funkcji podglądu i wyłącz _środowisko tworzenia potoku New YAML:_

![Okienko otwierania funkcji podglądu](../media/common/preview-feature-open.png)

Opcja wyłączenia:

![Nowa opcja tworzenia potoku YAML](../media/common/yaml-pipeline-preview-feature.png)

> [!Note]
> Usługa Azure DevOps Nowe środowisko _tworzenia YAML_ w wersji zapoznawczej powoduje konflikty z tworzeniem wstępnie zdefiniowanych potoków kompilacji w tej chwili. Musisz wyłączyć go na razie, aby wdrożyć nasz wstępnie zdefiniowany potok kompilacji.

W gałęzi _azds_updates_ uwzględniliśmy prosty [yaml usługi Azure Pipeline,](https://docs.microsoft.com/azure/devops/pipelines/yaml-schema?view=vsts&tabs=schema) który definiuje kroki kompilacji wymagane dla *mywebapi* i *webfrontend*.

W zależności od wybranego języka, potok YAML został zaewidencjonowany na ścieżce podobnej do:`samples/dotnetcore/getting-started/azure-pipelines.dotnetcore.yml`

Aby utworzyć potok z tego pliku:
1. Na stronie głównej projektu DevOps przejdź do pozycji Potoki > kompilacje.
1. Wybierz opcję, aby utworzyć **potok nowej** kompilacji.
1. Wybierz **GitHub** jako źródło, autoryzuj za pomocą konta GitHub, jeśli to konieczne, i wybierz gałąź _azds_updates_ z rozwidlonej wersji repozytorium aplikacji aplikacji dla _deweloperów._
1. Wybierz **opcję Konfiguracja jako kod**lub **YAML**, jako szablon.
1. Teraz zostanie wyświetlona strona konfiguracji dla potoku kompilacji. Jak wspomniano powyżej, przejdź do ścieżki specyficznej dla języka **dla ścieżki pliku YAML** za pomocą przycisku **....** Na przykład `samples/dotnetcore/getting-started/azure-pipelines.dotnet.yml`.
1. Przejdź do karty **Zmienne.**
1. Ręcznie dodaj _dockerId_ jako zmienną, która jest nazwą użytkownika [konta administratora rejestru kontenerów platformy Azure.](../../container-registry/container-registry-authentication.md#admin-account) (Wymienione w artykule wymagania wstępne)
1. Ręcznie dodaj _dockerPassword_ jako zmienną, która jest hasłem [konta administratora rejestru kontenerów platformy Azure.](../../container-registry/container-registry-authentication.md#admin-account) Pamiętaj, aby określić _dockerPassword_ jako klucz tajny (wybierając ikonę kłódki) ze względów bezpieczeństwa.
1. Wybierz **pozycję Zapisz & kolejkę**.

Masz teraz rozwiązanie ciągłej integracji, które automatycznie będzie tworzyć *mywebapi* i *webfrontend* dla każdej aktualizacji wypchnięte do _azds_updates_ gałęzi rozwidla GitHub. Możesz sprawdzić, czy obrazy platformy Docker zostały wypchnięte, przechodząc do witryny Azure portal, wybierając rejestr kontenerów platformy Azure i przeglądając kartę **Repozytoria.** Tworzenie obrazów i wyświetlenie ich w rejestrze kontenerów może potrwać kilka minut.

![Repozytoria rejestru kontenerów platformy Azure](../media/common/ci-cd-images-verify.png)

## <a name="creating-the-release-definition"></a>Tworzenie definicji wersji

1. Na stronie głównej projektu DevOps przejdź do pozycji Potoki > wydania
1. Jeśli pracujesz w zupełnie nowym projekcie DevOps, który nie zawiera jeszcze definicji wydania, musisz najpierw utworzyć pustą definicję wydania przed kontynuowaniem. Opcja Importuj nie jest wyświetlana w interfejsie użytkownika, dopóki nie pojawi się istniejąca definicja wersji.
1. Po lewej stronie kliknij przycisk **+ Nowy,** a następnie kliknij pozycję **Importuj potok**.
1. Kliknij **pozycję Przeglądaj** i wybierz `samples/release.json` z projektu.
1. Kliknij przycisk **OK**. Zwróć uwagę, że okienko potoku zostało załadowane ze stroną edycji definicji wersji. Należy również zauważyć, że istnieją czerwone ikony ostrzegawcze wskazujące szczegóły specyficzne dla klastra, które nadal muszą być skonfigurowane.
1. Po lewej stronie okienka Potok kliknij dymek **Dodaj artefakt.**
1. Z listy rozwijanej **Źródło** wybierz potok kompilacji utworzony wcześniej.
1. W przypadku **wersji domyślnej**wybierz pozycję **Najnowsze z domyślnej gałęzi potoku kompilacji ze znacznikami**.
1. Pozostaw **tagi** puste.
1. Ustaw **alias źródłowy** na `drop`. Wartość **aliasu źródłowego** jest używana przez wstępnie zdefiniowane zadania zwalniania, więc musi być ustawiona.
1. Kliknij pozycję **Add** (Dodaj).
1. Teraz kliknij ikonę błyskawicy `drop` na nowo utworzonym źródle artefaktów, jak pokazano poniżej:

    ![Instalacja ciągłego wdrażania artefaktu wydania](../media/common/release-artifact-cd-setup.png)
1. Włącz **wyzwalacz ciągłego wdrażania**.
1. Umieść wskaźnik myszy na karcie **Zadania** obok **pozycji Potok** i kliknij pozycję _Dev,_ aby edytować zadania etapu _dewelopera._
1. Sprawdź, czy **usługa Azure Resource Manager** jest zaznaczona w obszarze Typ **połączenia.** i zobaczysz trzy formanty rozwijane ![wyróżnione na czerwono: Konfiguracja definicji wersji](../media/common/release-setup-tasks.png)
1. Wybierz subskrypcję platformy Azure, której używasz w usłudze Azure Dev Spaces. Może być również konieczne **kliknięcie przycisku Autoryzuj**.
1. Wybierz grupę zasobów i klaster, którego używasz w usłudze Azure Dev Spaces.
1. Kliknij **zadanie agenta**.
1. Wybierz **hostowane Ubuntu 1604** w obszarze **Puli agentów**.
1. Umieść wskaźnik myszy na selektorze **Zadania** u góry, kliknij _prod,_ aby edytować zadania _prod_ etapie.
1. Sprawdź, czy **usługa Azure Resource Manager** jest zaznaczona w obszarze Typ **połączenia.** i wybierz subskrypcję, grupę zasobów i klaster platformy Azure, którego używasz w usłudze Azure Dev Spaces.
1. Kliknij **zadanie agenta**.
1. Wybierz **hostowane Ubuntu 1604** w obszarze **Puli agentów**.
1. Kliknij kartę **Zmienne,** aby zaktualizować zmienne dla wydania.
1. Zaktualizuj wartość **DevSpacesHostSuffix** z **UPDATE_ME** do sufiksu hosta. Sufiks hosta jest wyświetlany `azds show-context` po wcześniejszym uruchomieniu polecenia.
1. Kliknij **pozycję Zapisz** w prawym górnym rogu i przycisk **OK**.
1. Kliknij **+ Zwolnij** (obok przycisku Zapisz) i **Utwórz zwolnienie**.
1. W obszarze **Artefakty**sprawdź, czy zostanie wybrana najnowsza kompilacja z potoku kompilacji.
1. Kliknij przycisk **Utwórz**.

Teraz rozpocznie się zautomatyzowany proces wydawania, wdrażając wykresy *mywebapi* i *webfrontend* w klastrze Kubernetes w przestrzeni _deweloperów_ najwyższego poziomu. Możesz monitorować postęp wydania w portalu internetowym Azure DevOps:

1. Przejdź do sekcji **Zwalnia w** obszarze **Potoki**.
1. Kliknij potok wersji dla przykładowej aplikacji.
1. Kliknij nazwę najnowszej wersji.
1. Umieść wskaźnik myszy na polu **deweloperów** w obszarze **Etapy** i kliknij pozycję **Dzienniki**.

Wydanie jest wykonywane po zakończeniu wszystkich zadań.

> [!TIP]
> Jeśli wydanie nie powiedzie się z komunikatem o błędzie, takim jak *UPGRADE NIE POWIODŁO SIĘ: limit czasu oczekiwania na warunek*, spróbuj sprawdzić zasobników w klastrze za pomocą [pulpitu nawigacyjnego Kubernetes](../../aks/kubernetes-dashboard.md). Jeśli widzisz zasobników nie można uruchomić z komunikatami o błędach, takich jak *Nie można wyciągnąć obraz "azdsexample.azurecr.io/mywebapi:122": rpc error:\/code = Unknown desc = Odpowiedź na błąd z demona: Pobierz https: /azdsexample.azurecr.io/v2/mywebapi/manifests/122: nieautoryzowane: wymagane uwierzytelnianie*, może to być spowodowane klastra nie został upoważniony do ściągania z rejestru kontenera Azure. Upewnij się, że [ukończono autoryzowanie klastra AKS do wycofania z](../../aks/cluster-container-registry-integration.md) wymaganego rejestru kontenerów platformy Azure.

Masz teraz w pełni zautomatyzowany potok ciągłej integracji/ciągłego wdrażania dla rozwidlenia GitHub przykładowych aplikacji Dev Spaces. Za każdym razem, gdy zatwierdzasz i wypychasz kod, potok kompilacji będzie tworzyć i wypychać obrazy *mywebapi* i *webfrontend* do niestandardowego wystąpienia usługi ACR. Następnie potok wersji wdroży wykres Helm dla każdej aplikacji w przestrzeni _deweloperów_ w klastrze z obsługą przestrzeni deweloperskich.

## <a name="accessing-your-_dev_-services"></a>Uzyskiwanie dostępu do usług _deweloperskich_
Po wdrożeniu można uzyskać dostęp do wersji _dewelopera_ `http://dev.webfrontend.fedcba098.eus.azds.io` *webfrontendu* za pomocą publicznego adresu URL, takiego jak: . Ten adres URL można `azds list-uri` znaleźć, uruchamiając polecenie: 

```cmd
$ azds list-uris

Uri                                           Status
--------------------------------------------  ---------
http://dev.webfrontend.fedcba098.eus.azds.io  Available
```

## <a name="deploying-to-production"></a>Wdrażanie w środowisku produkcyjnym

Aby ręcznie promować określonej wersji _prod_ przy użyciu systemu CIĄGŁEGO/CD utworzonego w tym samouczku:
1. Przejdź do sekcji **Zwalnia w** obszarze **Potoki**.
1. Kliknij potok wersji dla przykładowej aplikacji.
1. Kliknij nazwę najnowszej wersji.
1. Umieść wskaźnik myszy na polu **prod** w obszarze **Etapy** i kliknij pozycję **Wdrażanie**.
    ![Podwyższanie poziomu do środowiska promocyjnego](../media/common/prod-promote.png)
1. Umieść wskaźnik myszy na polu **prod** ponownie w obszarze **Etapy** i kliknij pozycję **Dzienniki**.

Wydanie jest wykonywane po zakończeniu wszystkich zadań.

Etap _prod_ potoku ciągłej integracji/ciągłego wdrażania używa modułu równoważenia obciążenia zamiast kontrolera transferu danych przychodzących przestrzeni deweloperskich w celu zapewnienia dostępu do usług _prod._ Usługi wdrożone na etapie _prod_ są dostępne jako adresy IP zamiast nazw DNS. W środowisku produkcyjnym można utworzyć własny kontroler transferu danych przychodzących do obsługi usług na podstawie własnej konfiguracji DNS.

Aby określić adres IP usługi webfrontend, kliknij print **webfrontend publiczny adres IP** krok, aby rozwinąć dane wyjściowe dziennika. Użyj adresu IP wyświetlanego w danych wyjściowych dziennika, aby uzyskać dostęp do aplikacji **webfrontend.**

```cmd
...
2019-02-25T22:53:02.3237187Z webfrontend can be accessed at http://52.170.231.44
2019-02-25T22:53:02.3320366Z ##[section]Finishing: Print webfrontend public IP
...
```

## <a name="dev-spaces-instrumentation-in-production"></a>Oprzyrządowanie Dev Spaces w produkcji
Chociaż instrumentacja obszarów deweloperskich została zaprojektowana tak, aby _nie_ przeszkadzała w normalnym działaniu aplikacji, zaleca się uruchamianie obciążeń produkcyjnych w obszarze nazw usługi Kubernetes, który nie jest włączony w obszarze deweloperów. Używanie tego typu obszaru nazw kubernetes oznacza, że należy `kubectl` utworzyć produkcyjną przestrzeń nazw przy użyciu interfejsu wiersza polecenia lub zezwolić systemowi ciągłej integracji/ciągłego wdrażania, aby utworzyć go podczas pierwszego wdrożenia helm. _Wybranie_ lub utworzenie w inny sposób miejsca za pomocą narzędzi Miejsca deweloperów spowoduje dodanie instrumentacji obszarów deweloperskich do tego obszaru nazw.

Oto przykładowa struktura obszaru nazw, która obsługuje tworzenie funkcji, środowisko "dev" _i_ produkcję, a wszystko to w jednym klastrze Kubernetes:

![Przykładowa struktura obszaru nazw](../media/common/cicd-namespaces.png)

> [!Tip]
> Jeśli już utworzono `prod` spację i chcesz ją po prostu wykluczyć z instrumentacji Miejsca dewelopera (bez usuwania go!), można to zrobić za pomocą następującego polecenia Dev Spaces CLI:
>
> `azds space remove -n prod --no-delete`
>
> Może być konieczne usunięcie wszystkich `prod` zasobników w obszarze nazw po wykonaniu tej funkcji, aby można je było odtworzyć bez instrumentacji obszarów deweloperskich.

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Dowiedz się więcej o tworzeniu zespołu przy użyciu usługi Azure Dev Spaces](../team-development-netcore.md)