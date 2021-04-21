---
title: Wdrażanie aplikacji Java za pomocą programu Open Rozwiązania/WebSphere Zasób w klastrze Azure Red Hat OpenShift 4
description: Wdrażanie aplikacji w języku Java za pomocą programu Open Rozwiązania/WebSphere Zasób w klastrze Azure Red Hat OpenShift 4.
author: jiangma
ms.author: jiangma
ms.service: azure-redhat-openshift
ms.topic: conceptual
ms.date: 10/30/2020
keywords: java, jakartaee, javaee, microprofile, open-system, websphere-z, aro, openshift, red hat
ms.openlocfilehash: 2a308c7de754f395a3ef8a1bd97ed2441d27d21d
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107783579"
---
# <a name="deploy-a-java-application-with-open-libertywebsphere-liberty-on-an-azure-red-hat-openshift-4-cluster"></a>Wdrażanie aplikacji Java za pomocą programu Open Rozwiązania/WebSphere Zasób w klastrze Azure Red Hat OpenShift 4

W tym przewodniku pokazano, jak uruchomić aplikację Java, Java EE, [Jakarta EE](https://jakarta.ee/)lub [MicroProfile](https://microprofile.io/) w środowisku uruchomieniowym open-how/webSphere, a następnie wdrożyć konteneryzowane aplikacje w klastrze Azure Red Hat OpenShift (ARO) 4 przy użyciu operatora Open Rozwiązania. Ten artykuł zawiera 25 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000 000. W tym artykule  Aby uzyskać więcej informacji na temat programu Open Ramach, [zobacz stronę projektu Open List.](https://openliberty.io/) Aby uzyskać więcej informacji na temat programu IBM WebSphere System, [zobacz stronę produktu WebSphere ].](https://www.ibm.com/cloud/websphere-liberty)

[!INCLUDE [aro-support](includes/aro-support.md)]

## <a name="prerequisites"></a>Wymagania wstępne

Aby pomyślnie przejść przez ten przewodnik, należy spełnić następujące wymagania wstępne.

> [!NOTE]
> Azure Red Hat OpenShift do utworzenia i uruchomienia klastra OpenShift wymagane jest co najmniej 40 rdzeni. Domyślny limit przydziału zasobów platformy Azure dla nowej subskrypcji platformy Azure nie spełnia tego wymagania. Aby zażądać zwiększenia limitu zasobów, zobacz [Standard quota: Increase limits by VM series (Standardowy limit przydziału: zwiększanie limitów według serii maszyn wirtualnych).](../azure-portal/supportability/per-vm-quota-requests.md) Pamiętaj, że subskrypcja bezpłatnej wersji próbnej nie kwalifikuje się do zwiększenia limitu przydziału. Przechodz do subskrypcji z płatnością zgodnie z [tyg.](../cost-management-billing/manage/upgrade-azure-subscription.md) przed żądaniem zwiększenia limitu przydziału.

1. Przygotuj maszynę lokalną z zainstalowanym systemem operacyjnym Unix (na przykład Ubuntu, macOS).
1. Zainstaluj implementację środowiska Java SE (na przykład [AdoptOpenJDK OpenJDK 8 LTS/OpenJ9).](https://adoptopenjdk.net/?variant=openjdk8&jvmVariant=openj9)
1. Zainstaluj [program Maven](https://maven.apache.org/download.cgi) 3.5.0 lub wyższy.
1. Zainstaluj [platformę Docker](https://docs.docker.com/get-docker/) dla swojego systemu operacyjnego.
1. Zainstaluj [interfejs wiersza polecenia platformy Azure](/cli/azure/install-azure-cli) w wersji 2.0.75 lub nowszej.
1. Sprawdź i zainstaluj [`envsubst`](https://command-not-found.com/envsubst) program , jeśli nie jest wstępnie zainstalowany w systemie operacyjnym.
1. Sklonuj kod dla tego przykładu w systemie lokalnym. Przykład znajduje się w [witrynie GitHub.](https://github.com/Azure-Samples/open-liberty-on-aro)
1. Postępuj zgodnie z instrukcjami [w te Azure Red Hat OpenShift klastra 4.](./tutorial-create-cluster.md)

   Chociaż krok "Pobierz klucz tajny ściągania red hat" jest oznaczony jako opcjonalny, jest wymagany **w tym artykule.**  Klucz tajny ściągania umożliwia klastrowi Azure Red Hat OpenShift znajdowanie operatora Open ByD.

   Jeśli planujesz uruchamianie aplikacji intensywnie korzystających z pamięci w klastrze, określ odpowiedni rozmiar maszyny wirtualnej dla węzłów procesu roboczego przy użyciu `--worker-vm-size` parametru . Na przykład to minimalny rozmiar maszyny wirtualnej do zainstalowania operatora `Standard_E4s_v3` Elasticsearch w klastrze. Aby uzyskać więcej informacji, zobacz:

   * [Interfejs wiersza polecenia platformy Azure do tworzenia klastra](/cli/azure/aro#az_aro_create)
   * [Obsługiwane rozmiary maszyn wirtualnych zoptymalizowane pod kątem pamięci](./support-policies-v4.md#memory-optimized)
   * [Wymagania wstępne dotyczące instalowania operatora programu Elasticsearch](https://docs.openshift.com/container-platform/4.3/logging/cluster-logging-deploying.html#cluster-logging-deploy-eo-cli_cluster-logging-deploying)

1. Połącz się z klastrem, korzystając z kroków z tematu Nawiązywanie połączenia [z klastrem Azure Red Hat OpenShift 4.](./tutorial-connect-cluster.md)
   * Pamiętaj, aby wykonać kroki opisane w części "Instalowanie interfejsu wiersza polecenia openshift", ponieważ użyjemy `oc` polecenia w dalszej części tego artykułu.
   * Zapisz adres URL konsoli klastra, który wygląda `https://console-openshift-console.apps.<random>.<region>.aroapp.io/` następująco: .
   * Zanotuj `kubeadmin` poświadczenia.

1. Sprawdź, czy możesz zalogować się do interfejsu wiersza polecenia openshift przy użyciu tokenu dla użytkownika `kubeadmin` .

### <a name="enable-the-built-in-container-registry-for-openshift"></a>Włączanie wbudowanego rejestru kontenerów dla openshift

Kroki opisane w tym samouczku tworzą obraz platformy Docker, który należy wypchnąć do rejestru kontenerów dostępnego dla platformy OpenShift. Najprostszą opcją jest użycie wbudowanego rejestru udostępnianego przez usługę OpenShift. Aby włączyć wbudowany rejestr kontenerów, wykonaj kroki opisane w te [tematu Configure built-in container registry for Azure Red Hat OpenShift 4](built-in-container-registry.md)(Konfigurowanie wbudowanego rejestru kontenerów dla usługi Azure Red Hat OpenShift 4). W tym artykule są używane trzy elementy z tych kroków.

* Nazwa użytkownika i hasło użytkownika usługi Azure AD do logowania się do konsoli sieci Web platformy OpenShift.
* Dane wyjściowe polecenia `oc whoami` po zakończeniu procedury logowania się do interfejsu wiersza polecenia openshift.  Ta wartość jest **nazywana aad-user do** dyskusji.
* Adres URL rejestru kontenerów.

Zanotuj te elementy podczas kroków włączania wbudowanego rejestru kontenerów.

### <a name="create-an-openshift-namespace-for-the-java-app"></a>Tworzenie przestrzeni nazw OpenShift dla aplikacji Java

1. Zaloguj się do konsoli internetowej OpenShift z przeglądarki przy użyciu `kubeadmin` poświadczeń.
2. Przejdź do obszaru **Administracyjne**  >  **przestrzenie nazw Utwórz** przestrzeń  >  **nazw**.
3. Wypełnij pole `open-liberty-demo` Nazwa **i** wybierz **pozycję Utwórz**, jak pokazano poniżej.

   ![tworzenie przestrzeni nazw](./media/howto-deploy-java-liberty-app/create-namespace.png)

### <a name="create-an-administrator-for-the-demo-project"></a>Tworzenie administratora projektu demonstracyjnego

Oprócz zarządzania obrazami użytkownik **aad-user** ma również uprawnienia administracyjne do zarządzania zasobami w projekcie pokazowym klastra ARO 4.  Zaloguj się do interfejsu wiersza polecenia openshift i przyznaj użytkownikowi **aad-user** niezbędne uprawnienia, korzystając z tych kroków.

1. Zaloguj się do konsoli internetowej OpenShift z przeglądarki przy użyciu `kubeadmin` poświadczeń.
1. W prawym górnym rogu konsoli sieci Web rozwiń menu kontekstowe zalogowaowego użytkownika, a następnie wybierz pozycję **Kopiuj polecenie logowania**.
1. W razie potrzeby zaloguj się do nowego okna karty przy użyciu tego samego użytkownika.
1. Wybierz **pozycję Wyświetl token**.
1. Skopiuj wartość wymienioną poniżej **Login with this token** to the clipboard (Zaloguj się przy użyciu tego tokenu) do schowka i uruchom ją w powłoki, jak pokazano poniżej.
1. Wykonaj następujące polecenia, aby `admin` udzielić roli użytkownikowi **aad-user** w przestrzeni nazw `open-liberty-demo` .

   ```bash
   # Switch to project "open-liberty-demo"
   oc project open-liberty-demo
   Now using project "open-liberty-demo" on server "https://api.x8xl3f4y.eastus.aroapp.io:6443".
   # Note: replace "<aad-user>" with the one noted by executing the steps in
   # Configure built-in container registry for Azure Red Hat OpenShift 4
   oc adm policy add-role-to-user admin <aad-user>
   clusterrole.rbac.authorization.k8s.io/admin added: "kaaIjx75vFWovvKF7c02M0ya5qzwcSJ074RZBfXUc34"
   ```

### <a name="install-the-open-liberty-openshift-operator"></a>Instalowanie operatora openshift OpenShift

Po utworzeniu klastra i nałączeniu się z klastrem zainstaluj operator Open - Operator.  Główna strona początkowa dla operatora Open ByD znajduje się w witrynie [GitHub](https://github.com/OpenLiberty/open-liberty-operator).

1. Zaloguj się do konsoli internetowej OpenShift w przeglądarce przy użyciu `kubeadmin` poświadczeń.
2. Przejdź do strony  >  **OperatoryHub i** wyszukaj operator **Open Operator ( Otwórz operator).**
3. Wybierz **pozycję Open By Operator (Otwórz operator),** aby wybrać pozycję Open By (Otwórz operator) w wynikach wyszukiwania.
4. Wybierz pozycję **Zainstaluj**.
5. W oknie podręcznym Create Operator Subscription (Utwórz **subskrypcję** operatora) zaznacz pole All namespaces on the cluster (default) (Wszystkie przestrzenie nazw w klastrze **(ustawienie domyślne)** dla ustawień **Tryb** instalacji, **Beta** dla kanału **aktualizacji** i **Automatic** for Approval Strategy (Automatyczna strategia **zatwierdzania):**

   ![tworzenie subskrypcji operatora dla operatora Open Operator](./media/howto-deploy-java-liberty-app/install-operator.png)
6. Wybierz **pozycję Subskrybuj** i zaczekaj minutę lub dwie, aż zostanie wyświetlony operator Open - Operator.
7. Obserwuj operator Open By Ze stanem "Succeeded" (Powodzenie).  Jeśli nie, przed kontynuowaniem zdiagnozuj i rozwiąż problem.
   :::image type="content" source="media/howto-deploy-java-liberty-app/open-liberty-operator-installed.png" alt-text="Zainstalowano operatory pokazujące, że zainstalowano program OpenInstalator.":::

## <a name="prepare-the-liberty-application"></a>Przygotowywanie aplikacji Dosyć

Jako przykładu w tym przewodniku użyjemy aplikacji Java EE 8. Open Zamów to [serwer zgodny z pełnym profilem Java EE 8,](https://javaee.github.io/javaee-spec/javadocs/) dzięki czemu można łatwo uruchomić aplikację.  Program Open Zamość jest również zgodny z pełnym profilem [jak Dżaarta EE 8.](https://jakarta.ee/specifications/platform/8/apidocs/)

### <a name="run-the-application-on-open-liberty"></a>Uruchamianie aplikacji w programie Open Pogoń

Aby uruchomić aplikację na platformie Open Rozwiązania, należy utworzyć plik konfiguracji serwera Open By, aby wtyczka [Maven Maven](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin) spakła aplikację do wdrożenia. Wtyczka Rozwiązania Maven nie jest wymagana do wdrożenia aplikacji na openshift.  Jednak w tym przykładzie użyjemy go w trybie dewelopera (dewelopera) open-ina.  Tryb dewelopera umożliwia łatwe uruchamianie aplikacji lokalnie. Wykonaj następujące kroki na komputerze lokalnym.

1. Skopiuj `2-simple/src/main/liberty/config/server.xml` do pliku , `1-start/src/main/liberty/config` nadpisując istniejący plik o zerowej długości. Umożliwia `server.xml` to skonfigurowanie serwera Open Funkcji w języku Java EE.
1. Skopiuj `2-simple/pom.xml` do `1-start/pom.xml` .  Ten krok powoduje `liberty-maven-plugin` dodanie do pom.
1. Zmień katalog na `1-start` klona lokalnego.
1. Uruchom `mvn clean package` w konsoli, aby wygenerować pakiet war `javaee-cafe.war` w katalogu `./target` .
1. Uruchom , `mvn liberty:dev` aby uruchomić program Open Mode w trybie dewelopera.
1. Zaczekaj na rozpoczęcie serwera. Dane wyjściowe konsoli powinny zakończyć się następującym komunikatem:

   ```Text
   [INFO] CWWKM2015I: Match number: 1 is [6/10/20 10:26:09:517 CST] 00000022 com.ibm.ws.kernel.feature.internal.FeatureManager            A CWWKF0011I: The defaultServer server is ready to run a smarter planet. The defaultServer server started in 6.447 seconds..
   [INFO] Press the Enter key to run tests on demand. To stop the server and quit dev mode, use Ctrl-C or type 'q' and press the Enter key.
   [INFO] Source compilation was successful.
   ```

1. Otwórz `http://localhost:9080/` w przeglądarce, aby odwiedzić stronę główną aplikacji. Aplikacja będzie wyglądać podobnie do poniższej ilustracji:

   ![JavaEE z interfejsem użytkownika sieci Web](./media/howto-deploy-java-liberty-app/javaee-cafe-web-ui.png)
1. Naciśnij **klawisze Control-C,** aby zatrzymać aplikację i otworzyć serwer Open Kontrolka.

Katalog `2-simple` klonu lokalnego pokazuje projekt Maven z już zastosowanymi powyższymi zmianami.

## <a name="prepare-the-application-image"></a>Przygotowywanie obrazu aplikacji

Aby wdrożyć i uruchomić aplikację Rozwiązania w klastrze ARO 4, konteneryzuj aplikację jako obraz platformy Docker przy użyciu obrazów kontenerów [Open Rozwiązania](https://github.com/OpenLiberty/ci.docker) lub obrazów kontenerów [WebSphere Element.](https://github.com/WASdev/ci.docker)

### <a name="build-application-image"></a>Kompilowanie obrazu aplikacji

Aby skompilować obraz aplikacji, wykonaj następujące kroki:

1. Zmień katalog na `2-simple` klona lokalnego.
2. Uruchom `mvn clean package` , aby spakować aplikację.
3. Uruchom jedno z poniższych poleceń, aby skompilować obraz aplikacji.
   * Kompilowanie za pomocą obrazu podstawowego Open Przechowaj:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary Open Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull .
     ```

   * Kompilowanie przy użyciu obrazu podstawowego WebSphereSphere:

     ```bash
     # Build and tag application image. This will cause Docker to pull the necessary WebSphere Liberty base images.
     docker build -t javaee-cafe-simple:1.0.0 --pull --file=Dockerfile-wlp .
     ```

### <a name="run-the-application-locally-with-docker"></a>Lokalne uruchamianie aplikacji przy użyciu platformy Docker

Przed wdrożeniem konteneryzowanej aplikacji w klastrze zdalnym uruchom program z lokalną platformą Docker, aby sprawdzić, czy działa:

1. Uruchom `docker run -it --rm -p 9080:9080 javaee-cafe-simple:1.0.0` w konsoli.
2. Zaczekaj na uruchomienie serwera w jęz. i pomyślne wdrożenie aplikacji.
3. Otwórz `http://localhost:9080/` w przeglądarce, aby odwiedzić stronę główną aplikacji.
4. Naciśnij **klawisz Control-C,** aby zatrzymać aplikację i serwer Wc.

### <a name="push-the-image-to-the-container-image-registry"></a>Wypychanie obrazu do rejestru obrazów kontenerów

Jeśli stan aplikacji jest zadowalający, wypchń ją do wbudowanego rejestru obrazów kontenerów, zgodnie z poniższymi instrukcjami.

#### <a name="log-in-to-the-openshift-cli-as-the-azure-ad-user"></a>Zaloguj się do interfejsu wiersza polecenia platformy OpenShift jako użytkownik usługi Azure AD

1. Zaloguj się do konsoli internetowej platformy OpenShift z przeglądarki przy użyciu poświadczeń użytkownika usługi Azure AD.

   1. Użyj funkcji InPrivate, Incognito lub innej równoważnej funkcji okna przeglądarki, aby zalogować się do konsoli.
   1. Wybierz **pozycję openid**

   > [!NOTE]
   > Zanotuj nazwę użytkownika i hasło, których używasz do logowania się tutaj. Ta nazwa użytkownika i hasło będą działać jako administrator dla innych akcji w tym i innych artykułach.
1. Zaloguj się przy użyciu interfejsu wiersza polecenia openshift, korzystając z poniższych kroków.  Do dyskusji ten proces jest znany jako `oc login` .
   1. W prawym górnym rogu konsoli sieci Web rozwiń menu kontekstowe zalogowaowego użytkownika, a następnie wybierz polecenie **Kopiuj polecenie logowania.**
   1. W razie potrzeby zaloguj się do nowego okna karty przy użyciu tego samego użytkownika.
   1. Wybierz **pozycję Wyświetl token**.
   1. Skopiuj wartość wymienioną poniżej **Login with this token** to the clipboard (Zaloguj się przy użyciu tego tokenu) do schowka i uruchom ją w powłoki, jak pokazano poniżej.

       ```bash
       oc login --token=XOdASlzeT7BHT0JZW6Fd4dl5EwHpeBlN27TAdWHseob --server=https://api.aqlm62xm.rnfghf.aroapp.io:6443
       Logged into "https://api.aqlm62xm.rnfghf.aroapp.io:6443" as "kube:admin" using the token provided.

       You have access to 57 projects, the list has been suppressed. You can list all projects with 'oc projects'

       Using project "default".
       ```

#### <a name="push-the-container-image-to-the-container-registry-for-openshift"></a>Wypychanie obrazu kontenera do rejestru kontenerów dla openshift

Wykonaj te polecenia, aby wypchnąć obraz do rejestru kontenerów dla openshift.

```bash
# Note: replace "<Container_Registry_URL>" with the fully qualified name of the registry
Container_Registry_URL=<Container_Registry_URL>

# Create a new tag with registry info that refers to source image
docker tag javaee-cafe-simple:1.0.0 ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0

# Sign in to the built-in container image registry
docker login -u $(oc whoami) -p $(oc whoami -t) ${Container_Registry_URL}
```

Pomyślne dane wyjściowe będą wyglądać podobnie do następujących.

```bash
WARNING! Using --password via the CLI is insecure. Use --password-stdin.
Login Succeeded
```

Wypchniesz obraz do wbudowanego rejestru obrazów kontenerów za pomocą następującego polecenia.

```bash

docker push ${Container_Registry_URL}/open-liberty-demo/javaee-cafe-simple:1.0.0
```

## <a name="deploy-application-on-the-aro-4-cluster"></a>Wdrażanie aplikacji w klastrze ARO 4

Teraz możesz wdrożyć przykładową aplikację Rozwiązania w klastrze Azure Red Hat OpenShift 4 utworzonym wcześniej podczas pracy z warunkami wstępnymi.

### <a name="deploy-the-application-from-the-web-console"></a>Wdrażanie aplikacji z konsoli sieci Web

Ponieważ używamy operatora Open Rozwiązania do zarządzania aplikacjami ZO, musimy utworzyć wystąpienie jej niestandardowej definicji zasobu *o* typie "OpenLibertyApplication". Następnie operator zajmie się wszystkimi aspektami zarządzania zasobami openshift wymaganymi do wdrożenia.

1. Zaloguj się do konsoli internetowej platformy OpenShift z przeglądarki przy użyciu poświadczeń użytkownika usługi Azure AD.
1. Rozwiń **pozycję Strona** główna, **wybierz pozycję Projekty**  >  **open-do-demo.**
1. Przejdź do **operatora Zainstalowane**  >  **operatory**.
1. W środku strony wybierz pozycję **Otwórz operator w jęz.**
1. W środku strony wybierz pozycję **Otwórz aplikację w 2018 roku.**  Nawigacja elementów w interfejsie użytkownika odzwierciedla rzeczywistą hierarchię zawierania technologii w użyciu.
   <!-- Diagram source https://github.com/Azure-Samples/open-liberty-on-aro/blob/master/diagrams/aro-java-containment.vsdx -->
   ![Zawieranie języka Java ARO](./media/howto-deploy-java-liberty-app/aro-java-containment.png)
1. Wybierz **pozycję Create OpenLibertyApplication (Utwórz openlibertyApplication)**
1. Zastąp wygenerowany kod yaml twoim kodem yaml, który znajduje się `<path-to-repo>/2-simple/openlibertyapplication.yaml` w .
1. Wybierz przycisk **Utwórz**. Powrócisz do listy OpenLibertyApplications.
1. Wybierz **pozycję javaee-do-simple.**
1. W środku strony wybierz pozycję **Zasoby**.
1. W tabeli wybierz link **javaee-do-simple z** **rodzajem** **trasy**.
1. Na stronie, która zostanie otwarta, wybierz link poniżej **lokalizacji**.

W przeglądarce zostanie otwarta strona główna aplikacji.

### <a name="delete-the-application-from-the-web-console"></a>Usuwanie aplikacji z konsoli sieci Web

Po pracy z aplikacją wykonaj następujące kroki, aby usunąć aplikację z programu Open Shift.

1. W okienku nawigacji po lewej stronie rozwiń wpis **Operatory**.
1. Wybierz **pozycję Zainstalowane operatory.**
1. Wybierz **pozycję Otwórz operator w jęz.**
1. W środku strony wybierz pozycję **Otwórz aplikację w szicu**.
1. Wybierz pionowy wielokropek (trzy pionowe kropki), a następnie wybierz **pozycję Usuń aplikację OpenLiberty.**

### <a name="deploy-the-application-from-cli"></a>Wdrażanie aplikacji z interfejsu wiersza polecenia

Zamiast korzystać z graficznego interfejsu użytkownika konsoli sieci Web, możesz wdrożyć aplikację z interfejsu wiersza polecenia. Jeśli jeszcze tego nie zrobiono, pobierz i zainstaluj narzędzie wiersza polecenia, korzystając z dokumentacji systemu Red Hat Wprowadzenie `oc` [interfejsu wiersza polecenia.](https://docs.openshift.com/container-platform/4.2/cli_reference/openshift_cli/getting-started-cli.html)

1. Zaloguj się do konsoli internetowej platformy OpenShift z przeglądarki przy użyciu poświadczeń użytkownika usługi Azure AD.
2. Zaloguj się do interfejsu wiersza polecenia platformy OpenShift przy użyciu tokenu dla użytkownika usługi Azure AD.
3. Zmień katalog na lokalny klon i uruchom następujące polecenia, aby wdrożyć aplikację `2-simple` Rozwiązania w klastrze ARO 4.  Dane wyjściowe polecenia są również wyświetlane w tekście.

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

4. Przed kontynuowaniem `1/1` sprawdź, czy `READY` pod kolumną.  Jeśli nie, zbadaj i rozwiąż problem przed kontynuowaniem.
5. Odnajduj hosta trasy do aplikacji za pomocą `oc get route` polecenia , jak pokazano tutaj.

   ```bash
   # Get host of the route
   HOST=$(oc get route javaee-cafe-simple --template='{{ .spec.host }}')
   echo "Route Host: $HOST"

   Route Host: javaee-cafe-simple-open-liberty-demo.apps.aqlm62xm.rnfghf.aroapp.io
   ```

   Po uruchomieniu aplikacji z systemem Windows  otwórz dane wyjściowe hosta trasy w przeglądarce, aby odwiedzić stronę główną aplikacji.

### <a name="delete-the-application-from-cli"></a>Usuwanie aplikacji z interfejsu wiersza polecenia

Usuń aplikację z interfejsu wiersza polecenia, wykonując to polecenie.

```bash
oc delete -f openlibertyapplication.yaml
```

## <a name="clean-up-resources"></a>Czyszczenie zasobów

Usuń klaster ARO, zgodnie z instrukcjami w [samouczku: usuwanie klastra Azure Red Hat OpenShift 4](./tutorial-delete-cluster.md)

## <a name="next-steps"></a>Następne kroki

W tym przewodniku opisano sposób:
> [!div class="checklist"]
>
> * Przygotowywanie aplikacji Zduchem
> * Kompilowanie obrazu aplikacji
> * Uruchamianie aplikacji konteneryzowanej w klastrze ARO 4 przy użyciu graficznego interfejsu użytkownika i interfejsu wiersza polecenia

Więcej informacji można znaleźć w odwołaniach używanych w tym przewodniku:

* [Otwórz Okno](https://openliberty.io/)
* [Azure Red Hat OpenShift](https://azure.microsoft.com/services/openshift/)
* [Open Liberty Operator](https://github.com/OpenLiberty/open-liberty-operator)
* [Otwórz konfigurację serwera w 2017 r.](https://openliberty.io/docs/ref/config/)
* [Wtyczka Maven dla siły](https://github.com/OpenLiberty/ci.maven#liberty-maven-plugin)
* [Otwieranie obrazów kontenerów w łańcie](https://github.com/OpenLiberty/ci.docker)
* [Obrazy kontenerów WebSphere i IoT](https://github.com/WASdev/ci.docker)