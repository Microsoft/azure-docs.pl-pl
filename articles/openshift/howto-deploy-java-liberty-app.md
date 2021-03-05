---
title: Wdróż aplikację Java z otwartą wolnością/WebSphereą w klastrze Red Hat OpenShift 4
description: Wdróż aplikację Java z otwartą wolnością/WebSphereą w klastrze Red Hat OpenShift 4.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: Java, jakartaee, JavaEE, mikroprofil, Open-wolności, WebSphere-wolności, ARO, OpenShift, Red Hat
ms.openlocfilehash: 08fd3ab112498a983b438d5ba1f1f100816cbf5d
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/05/2021
ms.locfileid: "102212998"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Wdróż aplikację Java z otwartą wolnością/WebSphereą w klastrze Red Hat OpenShift 4

W tym przewodniku pokazano, jak uruchamiać aplikacje Java, Java EE, [Dżakarta EE](https://jakarta.ee/)lub [mikroprofilowanie](https://microprofile.io/) w środowisku uruchomieniowym z otwartym wolnością/WebSphere, a następnie wdrożyć aplikację kontenerową na platformie Azure Red Hat OpenShift (ARO) 4 przy użyciu operatora Open wolności. Ten artykuł przeprowadzi Cię przez proces przygotowywania aplikacji wolności, tworzenia obrazu platformy Docker i uruchamiania aplikacji kontenera w klastrze z 4.  Aby uzyskać więcej informacji na temat otwartej wolności, zobacz [stronę Otwórz projekt wolności](https://openliberty.io/). Aby uzyskać więcej informacji na temat usługi IBM WebSphere wolności [, zobacz stronę WebSphere wolności produktu](https://www.ibm.com/cloud/websphere-liberty).

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby pomyślnie zapoznać się z tym przewodnikiem, należy spełnić następujące wymagania wstępne.

> [!NOTE]
> Do utworzenia i uruchomienia klastra OpenShift na platformie Azure Red Hat OpenShift jest wymagany co najmniej 40 rdzeni. Domyślny przydział zasobów platformy Azure dla nowej subskrypcji platformy Azure nie spełnia tego wymagania. Aby zażądać zwiększenia limitu zasobów, zobacz [przydział standardowy: zwiększanie limitów przez serię maszyn wirtualnych](../azure-portal/supportability/per-vm-quota-requests.md). Pamiętaj, że bezpłatna subskrypcja wersji próbnej nie kwalifikuje się do zwiększenia limitu przydziału, [Przeprowadź uaktualnienie do subskrypcji z płatnością zgodnie z rzeczywistym](../cost-management-billing/manage/upgrade-azure-subscription.md) użyciem przed zażądaniem zwiększenia limitu przydziału.

1. Przygotuj maszynę lokalną z zainstalowanym systemem operacyjnym UNIX (na przykład Ubuntu, macOS).
1. Instalowanie implementacji języka Java SE (na przykład [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)).
1. Zainstaluj program [Maven](https://maven.apache.org/download.cgi) 3.5.0 lub nowszy.
1. Zainstaluj [platformę Docker](https://docs.docker.com/get-docker/) dla systemu operacyjnego.
1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) 2.0.75 lub nowszy.
1. Sprawdź i zainstaluj, [`envsubst`](https://command-not-found.com/envsubst) Jeśli nie jest wstępnie zainstalowany w systemie operacyjnym.
1. Sklonuj kod dla tego przykładu w systemie lokalnym. Przykład znajduje się w serwisie [GitHub](https://github.com/Azure-Samples/open-liberty-on-aro).
1. Postępuj zgodnie z instrukcjami w temacie [Tworzenie klastra usługi Azure Red Hat OpenShift 4](./tutorial-create-cluster.md).

   Mimo że krok "Pobierz klucz tajny w Red Hat" jest oznaczony jako opcjonalny, **jest to wymagane w tym artykule**.  Klucz tajny ściągania umożliwia klastrowi usługi Azure Red Hat OpenShift znalezienie operatora Open wolności.

   Jeśli planujesz uruchamiać aplikacje intensywnie korzystające z pamięci w klastrze, określ odpowiedni rozmiar maszyny wirtualnej dla węzłów procesu roboczego przy użyciu `--worker-vm-size` parametru. Na przykład `Standard_E4s_v3` jest minimalnym rozmiarem maszyny wirtualnej w celu zainstalowania operatora Elasticsearch w klastrze. Aby uzyskać więcej informacji, zobacz:

   * [Interfejs wiersza polecenia platformy Azure do tworzenia klastra](/cli/azure/aro#az-aro-create)
   * [Obsługiwane rozmiary maszyn wirtualnych dla zoptymalizowanej pod kątem pamięci](./support-policies-v4.md#memory-optimized)
   * [Wymagania wstępne dotyczące instalacji operatora Elasticsearch](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Połącz się z klastrem, wykonując kroki opisane w temacie [łączenie z klastrem usługi Azure Red Hat OpenShift 4](./tutorial-connect-cluster.md).
   * Upewnij się, że wykonano kroki opisane w temacie "Instalowanie interfejsu wiersza polecenia OpenShift", ponieważ użyjemy `oc` polecenia w dalszej części tego artykułu.
   * Zapisz adres URL konsoli klastra, który wygląda podobnie `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` .
   * Zanotuj `kubeadmin` poświadczenia.

1. Sprawdź, czy możesz zalogować się do interfejsu wiersza polecenia OpenShift przy użyciu tokenu dla użytkownika `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Włącz wbudowany rejestr kontenerów dla OpenShift

Kroki opisane w tym samouczku umożliwiają utworzenie obrazu platformy Docker, który należy wypchnąć do rejestru kontenerów dostępnego dla OpenShift. Najprostszą opcją jest użycie wbudowanego rejestru dostarczonego przez OpenShift. Aby włączyć wbudowany rejestr kontenerów, wykonaj kroki opisane w temacie [Konfigurowanie wbudowanego rejestru kontenerów dla usługi Azure Red Hat OpenShift 4](built-in-container-registry.md). W tym artykule są używane trzy elementy z tych kroków.

* Nazwa i hasło użytkownika usługi Azure AD do logowania się do konsoli sieci Web OpenShift.
* Dane wyjściowe `oc whoami` po wykonaniu czynności związanych z logowaniem do interfejsu wiersza polecenia OpenShift.  Ta wartość jest nazywana **AAD-User** for Discussion.
* Adres URL rejestru kontenerów.

Te elementy należy zanotować po wykonaniu kroków, aby włączyć wbudowany rejestr kontenerów.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Tworzenie przestrzeni nazw OpenShift dla aplikacji Java

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu `kubeadmin` poświadczeń.
2. Przejdź do   >  obszaru **nazw** Administracja  >  **Utwórz przestrzeń nazw**.
3. Wypełnij pola `open-liberty-demo` **Nazwa** i wybierz pozycję **Utwórz**, jak pokazano poniżej.

   ![Utwórz przestrzeń nazw](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Utwórz administratora dla projektu demonstracyjnego

Poza zarządzaniem obrazami, **użytkownik usługi AAD** będzie miał również uprawnienia administracyjne do zarządzania zasobami w projekcie demonstracyjnym klastra ARO 4.  Zaloguj się do interfejsu wiersza polecenia OpenShift i przyznaj **użytkownikowi usługi AAD** odpowiednie uprawnienia, wykonując następujące kroki.

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu `kubeadmin` poświadczeń.
1. W prawym górnym rogu konsoli sieci Web rozwiń menu kontekstowe zalogowanego użytkownika, a następnie wybierz **polecenie Kopiuj logowanie**.
1. Jeśli to konieczne, zaloguj się do nowego okna karty z tym samym użytkownikiem.
1. Wybierz pozycję **Wyświetl token**.
1. Skopiuj poniższą wartość w polu **Zaloguj się przy użyciu tego tokenu** do schowka i uruchom ją w powłoce, jak pokazano poniżej.
1. Wykonaj następujące polecenia, aby udzielić `admin` roli dla **użytkownika usługi AAD** w przestrzeni nazw `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Instalowanie operatora Open wolności OpenShift

Po utworzeniu i nawiązaniu połączenia z klastrem należy zainstalować operator Open wolności.  Główna Strona początkowa dla operatora Open wolności jest w serwisie [GitHub](https://github.com/OpenLiberty/open-liberty-operator).

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu `kubeadmin` poświadczeń.
2. Przejdź do **operatorów**  >  **OperatorHub** i wyszukaj ciąg " **Open wolności**".
3. Wybierz pozycję **Otwórz operator wolności** z wyników wyszukiwania.
4. Wybierz pozycję **Zainstaluj**.
5. W oknie podręcznym **Utwórz subskrypcję operatora** zaznacz **wszystkie przestrzenie nazw w klastrze (domyślnie)** dla opcji **tryb instalacji**, **wersja beta** dla **kanału aktualizacji** i **Automatyczne** dla **strategii zatwierdzania**:

   ![Utwórz subskrypcję operatora dla operatora Open wolności](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Wybierz pozycję **Subskrybuj** i poczekaj chwilę lub dwa, aż zostanie wyświetlony operator Open wolności.
7. Obserwuj operator "Open wolności" ze stanem "powodzenie".  Jeśli nie, zdiagnozowanie i rozwiązanie problemu przed kontynuowaniem.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Zainstalowane operatory pokazujące otwartą wolność są zainstalowane.":::

## <a name="prepare-the-liberty-application"></a>Przygotowywanie wolności aplikacji

W tym przewodniku będziemy używać aplikacji Java EE 8. Open wolności to serwer zgodny z [pełnym profilem w języku Java EE 8](https://javaee.github.io/javaee-spec/javadocs/) , dzięki czemu można łatwo uruchomić aplikację.  Open wolności jest również [zgodny z pełnym profilem Dżakarta EE 8](https://jakarta.ee/specifications/platform/8/apidocs/).

### <a name="run-the-application-on-open-liberty"></a>Uruchamianie aplikacji przy otwartym wolności

Aby uruchomić aplikację przy otwartym wolności, należy utworzyć otwarty plik konfiguracji serwera z systemem, aby [wtyczka Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) mogła spakować aplikację do wdrożenia. Wtyczka z Maveną wolności nie jest wymagana do wdrożenia aplikacji w OpenShift.  Będziemy jednak używać go w tym przykładzie z trybem dewelopera Open wolności (dev).  Tryb dewelopera umożliwia łatwe uruchamianie aplikacji lokalnie. Na komputerze lokalnym wykonaj następujące czynności.

1. Kopiuj `2-simple/src/main/liberty/config/server.xml` do `1-start/src/main/liberty/config` , zastępując istniejący plik o zerowej długości. Spowoduje to `server.xml` skonfigurowanie otwartego serwera z funkcjami Java EE.
1. Kopiuj `2-simple/pom.xml` do `1-start/pom.xml` .  Ten krok powoduje dodanie `liberty-maven-plugin` do pliku pom.
1. Zmień katalog na `1-start` lokalny klon.
1. Uruchom `mvn clean package` w konsoli programu, aby wygenerować pakiet War `javaee-cafe.war` w katalogu `./target` .
1. Uruchom `mvn liberty:dev` , aby rozpocząć otwieranie wolności w trybie deweloperskim.
1. Poczekaj na uruchomienie serwera. Dane wyjściowe konsoli powinny kończyć się następującym komunikatem:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Otwórz `http://localhost:9080/` w przeglądarce, aby odwiedzić stronę główną aplikacji. Aplikacja będzie wyglądać podobnie do poniższej ilustracji:

   ![Interfejs użytkownika sieci Web JavaEE Cafe](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Naciśnij klawisze **Control-C** , aby zatrzymać aplikację i otworzyć serwer wolności.

Katalog `2-simple` lokalnego klonu pokazuje projekt Maven z powyższymi zmianami, które zostały już zastosowane.

## <a name="prepare-the-application-image"></a>Przygotowywanie obrazu aplikacji

Aby wdrożyć i uruchomić aplikację z wolnością w klastrze z pakietem ARO + 4, konteneryzowanie aplikację jako obraz platformy Docker przy użyciu [otwartych obrazów kontenerów](https://github.com/OpenLiberty/ci.docker) lub [WebSphere wolności z kontenerów](https://github.com/WASdev/ci.docker).

### <a name="build-application-image"></a>Obraz aplikacji kompilacji

Wykonaj następujące kroki, aby skompilować obraz aplikacji:

1. Zmień katalog na `2-simple` lokalny klon.
2. Uruchom `mvn clean package` , aby spakować aplikację.
3. Uruchom jedno z następujących poleceń, aby skompilować obraz aplikacji.
   * Kompiluj z podstawowym obrazem z otwartą wolnością:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Kompiluj z WebSphereą wolności podstawy:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Uruchamianie aplikacji lokalnie przy użyciu platformy Docker

Przed wdrożeniem aplikacji kontenera do zdalnego klastra uruchom polecenie z lokalnym aparatem Docker, aby sprawdzić, czy działa:

1. Uruchom `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` w konsoli programu.
2. Poczekaj na uruchomienie serwera i aplikacji do wdrożenia.
3. Otwórz `http://localhost:9080/` w przeglądarce, aby odwiedzić stronę główną aplikacji.
4. Naciśnij klawisz **Control-C** , aby zatrzymać aplikację i serwer wolności.

### <a name="push-the-image-to-the-container-image-registry"></a>Wypchnij obraz do rejestru kontenerów

Po spełnieniu stanu aplikacji Wypchnij ją do wbudowanego rejestru kontenerów, postępując zgodnie z poniższymi instrukcjami.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Zaloguj się do interfejsu wiersza polecenia OpenShift jako użytkownik usługi Azure AD

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu poświadczeń użytkownika usługi Azure AD.

   1. Aby zalogować się do konsoli programu, należy użyć funkcji InPrivate, incognito lub innych odpowiedników okna przeglądarki.
   1. Wybierz **OpenID Connect**

   > [!NOTE]
   > Zanotuj nazwę użytkownika i hasło, których używasz do logowania się tutaj. Ta nazwa użytkownika i hasło będą działać jako administrator dla innych akcji w tym i innych artykułach.
1. Zaloguj się przy użyciu interfejsu wiersza polecenia OpenShift, wykonując następujące kroki.  W przypadku dyskusji ten proces jest znany jako `oc login` .
   1. W prawym górnym rogu konsoli sieci Web rozwiń menu kontekstowe zalogowanego użytkownika, a następnie wybierz **polecenie Kopiuj logowanie**.
   1. Jeśli to konieczne, zaloguj się do nowego okna karty z tym samym użytkownikiem.
   1. Wybierz pozycję **Wyświetl token**.
   1. Skopiuj poniższą wartość w polu **Zaloguj się przy użyciu tego tokenu** do schowka i uruchom ją w powłoce, jak pokazano poniżej.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Wypchnij obraz kontenera do rejestru kontenerów dla OpenShift

Wykonaj te polecenia, aby wypchnąć obraz do rejestru kontenerów dla OpenShift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

Pomyślne wyjście będzie wyglądać podobnie do poniższego.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Wypchnij obraz do wbudowanego rejestru kontenerów za pomocą poniższego polecenia.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Wdrażanie aplikacji w klastrze z pakietem ARO 4

Teraz można wdrożyć aplikację z przykładową wolnością w klastrze usługi Azure Red Hat OpenShift 4 utworzonym wcześniej podczas pracy z wymaganiami wstępnymi.

### <a name="deploy-the-application-from-the-web-console"></a>Wdrażanie aplikacji z poziomu konsoli sieci Web

Ze względu na to, że korzystamy z operatora Open wolności do zarządzania aplikacjami z wolnością, musimy utworzyć wystąpienie swojej *niestandardowej definicji zasobu* typu "OpenLibertyApplication". Następnie operator zajmie się wszystkimi aspektami zarządzania zasobami OpenShift wymaganymi do wdrożenia.

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu poświadczeń użytkownika usługi Azure AD.
1. Rozwiń węzeł **Strona główna**, wybierz pozycję **projekty**  >  **Open-wolność-Demonstracja**.
1. Przejdź do **operatorów**  >  **zainstalowanych** operatorów.
1. W środku strony wybierz pozycję **Otwórz operator wolności**.
1. Na środku strony wybierz pozycję **Otwórz aplikację wolności**.  Nawigacja elementów w interfejsie użytkownika odzwierciedla rzeczywistą hierarchię zawierania używanych technologii.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![Zawiera środowisko Java wyaro](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Wybierz pozycję **Utwórz OpenLibertyApplication**
1. Zastąp wygenerowany YAML własnym, który znajduje się w lokalizacji `<path-to-repo>/2-simple/openlibertyapplication.yaml` .
1. Wybierz przycisk **Utwórz**. Nastąpi powrót do listy OpenLibertyApplications.
1. Wybierz pozycję **JavaEE-kawiarni-Simple**.
1. W środku strony wybierz pozycję **zasoby**.
1. W tabeli wybierz link do **JavaEE-kawiarni-Simple** z **rodzajem** **trasy**.
1. Na stronie, która zostanie otwarta, wybierz link poniżej **lokalizacji**.

Zostanie wyświetlona strona główna aplikacji otwarta w przeglądarce.

### <a name="delete-the-application-from-the-web-console"></a>Usuwanie aplikacji z konsoli sieci Web

Po zakończeniu pracy z aplikacją wykonaj następujące kroki, aby usunąć aplikację z okna otwieranie zmian.

1. W okienku nawigacji po lewej stronie rozwiń pozycję **Operatory**.
1. Wybierz **zainstalowanych operatorów**.
1. Wybierz pozycję **Otwórz operator wolności**.
1. W środku strony wybierz pozycję **Otwórz aplikację wolności**.
1. Wybierz wielokropek pionowy (trzy kropki pionowe), a następnie wybierz pozycję **Usuń aplikację OpenLiberty**.

### <a name="deploy-the-application-from-cli"></a>Wdrażanie aplikacji z poziomu interfejsu wiersza polecenia

Zamiast korzystać z graficznego interfejsu użytkownika konsoli sieci Web, można wdrożyć aplikację z poziomu interfejsu wiersza polecenia. Jeśli jeszcze tego nie zrobiono, Pobierz i zainstaluj `oc` Narzędzie wiersza polecenia, postępując zgodnie z dokumentacją Red Hat [wprowadzenie z interfejsem CLI](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html).

1. Zaloguj się do konsoli sieci Web OpenShift z poziomu przeglądarki przy użyciu poświadczeń użytkownika usługi Azure AD.
2. Zaloguj się do interfejsu wiersza polecenia OpenShift przy użyciu tokenu dla użytkownika usługi Azure AD.
3. Zmień katalog na `2-simple` sklonowany, a następnie uruchom następujące polecenia, aby wdrożyć aplikację w KLASTRZE ARO 4.  Dane wyjściowe polecenia są również wyświetlane w tekście.

   ```bash
   # Switch to namespace "open-liberty-demo" where resources of demo app will belong to
   oc project open-liberty-demo

   Now using (or already on) project "open-liberty-demo" on server "https://api.aqlm62xm.rnfghf.aroapp.io:6443".

   # Create OpenLibertyApplication "javaee-cafe-simple"
   oc create -f openlibertyapplication.yaml

   openlibertyapplication.openliberty.io/javaee-cafe-simple created

   # Check if OpenLibertyApplication instance is created
   oc get openlibertyapplication javaee-cafe-simple

   NAME                 IMAGE                      EXPOSED   RECONCILED   AGE
   javaee-cafe-simple   javaee-cafe-simple:1.0.0   true      True         36s

   # Check if deployment created by Operator is ready
   oc get deployment javaee-cafe-simple

   NAME                 READY   UP-TO-DATE   AVAILABLE   AGE
   javaee-cafe-simple   1/1     1            0           102s
   ```

4. `1/1`Przed kontynuowaniem sprawdź, czy w obszarze kolumny znajduje się wartość `READY` .  Jeśli nie, zbadaj i rozwiąż problem przed kontynuowaniem.
5. Znajdź hosta trasy do aplikacji za pomocą `oc get route` polecenia, jak pokazano poniżej.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Po uruchomieniu i uruchomieniu wolności aplikacji otwórz dane wyjściowe **hosta trasy** w przeglądarce, aby odwiedzić stronę główną aplikacji.

### <a name="delete-the-application-from-cli"></a>Usuwanie aplikacji z interfejsu wiersza polecenia

Usuń aplikację z interfejsu wiersza polecenia, wykonując to polecenie.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usuń klaster ARO, wykonując czynności opisane w [samouczku: usuwanie klastra usługi Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku wyjaśniono, jak:
> [!div class="checklist"]
>
> * Przygotowywanie wolności aplikacji
> * Tworzenie obrazu aplikacji
> * Uruchamianie aplikacji kontenera w klastrze z systemem ARO 4, przy użyciu graficznego interfejsu użytkownika i interfejsu wiersza polecenia

Więcej informacji można znaleźć w tematach w tym przewodniku:

* [Open wolności](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Otwarty operator wolności](https://github.com/OpenLiberty/open-liberty-operator)
* [Otwórz konfigurację serwera z wolnością](https://openliberty.io/docs/ref/config/)
* [Wtyczka z Maveną wolności](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Otwieranie obrazów kontenera wolności](https://github.com/OpenLiberty/ci.docker)
* [WebSphereowe obrazy kontenerów](https://github.com/WASdev/ci.docker)