---
title: Tworzenie kontenera dla platformy Apache Tomcat w systemie Linux
description: Utwórz kontener systemu Linux, aby udostępnić aplikację działającą na serwerze Apache Tomcat na platformie Azure Service Fabric. Utwórz obraz platformy Docker za pomocą aplikacji i serwera Apache Tomcat, wypchnij obraz do rejestru kontenerów, skompiluj i Wdróż Service Fabric aplikację kontenera.
ms.topic: conceptual
ms.date: 6/08/2018
ms.author: pepogors
ms.openlocfilehash: 1a699f3b35970270a9800162a6d8717682a168ae
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "75614421"
---
# <a name="create-service-fabric-container-running-apache-tomcat-server-on-linux"></a>Tworzenie kontenera Service Fabric z systemem Apache Tomcat Server w systemie Linux
Apache Tomcat to popularne wdrożenie typu "open source" technologii Java serwletu i Java Server. W tym artykule opisano sposób tworzenia kontenera za pomocą platformy Apache Tomcat i prostej aplikacji sieci Web, wdrażania kontenera w klastrze Service Fabric z systemem Linux i nawiązywania połączenia z aplikacją sieci Web.  

Aby dowiedzieć się więcej o platformie Apache Tomcat, zobacz [stronę główną Apache Tomcat](https://tomcat.apache.org/). 

## <a name="prerequisites"></a>Wymagania wstępne
* Komputer dewelopera z następującym oprogramowaniem:
  * [Service Fabric zestaw SDK i narzędzia](service-fabric-get-started-linux.md).
  * [Docker CE dla systemu Linux](https://docs.docker.com/engine/installation/#prior-releases). 
  * [Interfejs wiersza polecenia usługi Service Fabric](service-fabric-cli.md)

* Rejestr kontenerów w Azure Container Registry. Można utworzyć rejestr kontenerów w ramach subskrypcji platformy Azure przy użyciu [Azure Portal](../container-registry/container-registry-get-started-portal.md) lub [interfejsu wiersza polecenia platformy Azure](./service-fabric-tutorial-create-container-images.md#deploy-azure-container-registry). 

## <a name="build-a-tomcat-image-and-run-it-locally"></a>Tworzenie obrazu Tomcat i uruchamianie go lokalnie
Postępuj zgodnie z instrukcjami w tej sekcji, aby utworzyć obraz platformy Docker na podstawie obrazu Apache Tomcat i prostej aplikacji sieci Web, a następnie uruchomić go w kontenerze w systemie lokalnym. 
 
1. Sklonuj [Service Fabric wprowadzenie do repozytorium przykładów języka Java](https://github.com/Azure-Samples/service-fabric-java-getting-started) na komputerze deweloperskim.

   ```bash
   git clone https://github.com/Azure-Samples/service-fabric-java-getting-started.git
   ```

1. Przejdź do katalogu przykładowego serwera Apache Tomcat (*Service-Fabric-Java-Start-Started/Container-Apache-Tomcat-Web-Server-Sample*):

   ```bash
   cd service-fabric-java-getting-started/container-apache-tomcat-web-server-sample
   ```

1. Utwórz plik platformy Docker na podstawie oficjalnego [obrazu Tomcat](https://hub.docker.com/_/tomcat/) znajdującego się w centrum Docker i przykładowym serwerze tomcat. W katalogu *Service-Fabric-Java-Start-Started/Container-Apache-Tomcat-Web-Server-Sample* Utwórz plik o nazwie *pliku dockerfile* (bez rozszerzenia pliku). Dodaj następujący kod do pliku *Dockerfile* i zapisz zmiany:

   ```
   FROM library/tomcat

   EXPOSE 8080

   COPY ./ApacheTomcat /usr/local/tomcat
   ```

   Zobacz [odwołanie pliku dockerfile](https://docs.docker.com/engine/reference/builder/) , aby uzyskać więcej informacji.


4. Uruchom `docker build` polecenie, aby utworzyć obraz z uruchomioną aplikacją sieci Web:

   ```bash
   docker build . -t tomcattest
   ```

   To polecenie kompiluje nowy obraz przy użyciu instrukcji z pliku dockerfile, nazywania (-t tagowania) obrazu `tomcattest` . Aby skompilować obraz kontenera, podstawowy obraz jest najpierw pobierany z usługi Docker Hub, a aplikacja zostanie dodana do niej. 

   Po zakończeniu działania polecenia budowania uruchom polecenie `docker images`, aby wyświetlić informacje o nowym obrazie:

   ```bash
   $ docker images
    
   REPOSITORY                    TAG                 IMAGE ID            CREATED             SIZE
   tomcattest                    latest              86838648aab6        2 minutes ago       194 MB
   ```

5. Sprawdź, czy aplikacja kontenera działa lokalnie przed wypchnięciem jej w rejestrze kontenerów:
 
   ```bash
   docker run -itd --name tomcat-site -p 8080:8080 tomcattest.
   ```
   
   * `--name` nazwa kontenera, więc można odwołać się do niego przy użyciu przyjaznej nazwy, a nie jej identyfikatora.
   * `-p` Określa mapowanie portów między kontenerem i systemem operacyjnym hosta. 

   > [!Note]
   > Port otwarty za pomocą `-p` parametru powinien być portem, na którym aplikacja Tomcat nasłuchuje żądań. W bieżącym przykładzie istnieje łącznik skonfigurowany w pliku *ApacheTomcat/conf/server.xml* do nasłuchiwania na porcie 8080 dla żądań HTTP. Ten port jest mapowany na port 8080 na hoście. 

   Aby dowiedzieć się więcej o innych parametrach, zobacz [dokumentację platformy Docker](https://docs.docker.com/engine/reference/commandline/run/).

1. Aby przetestować kontener, Otwórz przeglądarkę i wprowadź jeden z następujących adresów URL. Zobaczysz wariant "Hello world!" ekran powitalny dla każdego adresu URL.

   - `http://localhost:8080/hello` 
   - `http://localhost:8080/hello/sayhello` 
   - `http://localhost:8080/hello/sayhi` 

   ![Witaj świecie/SayHi](./media/service-fabric-get-started-tomcat/hello.png)

2. Zatrzymaj kontener i usuń go z komputera deweloperskiego:

   ```bash
   docker stop tomcat-site
   docker rm tomcat-site
   ```

## <a name="push-the-tomcat-image-to-your-container-registry"></a>Wypchnij obraz Tomcat do rejestru kontenerów
Po zweryfikowaniu, że obraz Tomcat jest uruchamiany w kontenerze na komputerze deweloperskim, wypchnij go do repozytorium w rejestrze kontenerów. W tym artykule użyto Azure Container Registry do przechowywania obrazu, ale wraz z modyfikacją kroków można użyć dowolnego wybranego rejestru kontenerów. W tym artykule przyjęto, że nazwa rejestru to " *Registry* ", a pełna nazwa rejestru to myregistry.azurecr.IO. Zmień je odpowiednio do danego scenariusza. 

1. Uruchom `docker login` , aby zalogować się do rejestru kontenerów przy użyciu [poświadczeń rejestru](../container-registry/container-registry-authentication.md).

   Poniższy przykład przekazuje identyfikator i hasło [nazwy głównej usługi](../active-directory/develop/app-objects-and-service-principals.md) Azure Active Directory. Na przykład nazwę główną usługi można było przypisać do rejestru dla scenariusza automatyzacji. Możesz też zalogować się przy użyciu nazwy użytkownika i hasła do rejestru.

   ```bash
   docker login myregistry.azurecr.io -u xxxxxxxx-xxxx-xxxx-xxxx-xxxxxxxxxxxx -p myPassword
   ```

2. Następujące polecenie tworzy tag lub alias obrazu z w pełni kwalifikowaną ścieżką do Twojego rejestru. W tym przykładzie obraz jest umieszczany w przestrzeni nazw `samples`, aby uniknąć zaśmiecania katalogu głównego rejestru.

   ```bash
   docker tag tomcattest myregistry.azurecr.io/samples/tomcattest
   ```

3. Wypchnij obraz do rejestru kontenerów:

   ```bash
   docker push myregistry.azurecr.io/samples/tomcattest
   ```

## <a name="build-and-deploy-the-service-fabric-container-application"></a>Kompilowanie i wdrażanie aplikacji kontenera Service Fabric
Teraz, gdy obraz Tomcat został wypchnięci do rejestru kontenerów, można skompilować i wdrożyć aplikację kontenera Service Fabric, która pobiera obraz Tomcat z rejestru i uruchamia go jako usługę kontenera w klastrze. 

1. Utwórz nowy katalog poza lokalnym klonem (poza *usługą Service-Fabric-Java-Starting* Tree Directory). Przejdź do niego i użyj narzędzia Yeoman do utworzenia szkieletu dla aplikacji kontenera: 

   ```bash
   yo azuresfcontainer 
   ```
   Po wyświetleniu monitu wprowadź następujące wartości:

   * Nazwij aplikację: ServiceFabricTomcat
   * Nazwa usługi aplikacji: TomcatService
   * Wprowadź nazwę obrazu: Podaj adres URL obrazu kontenera w rejestrze kontenerów. na przykład myregistry.azurecr.io/samples/tomcattest.
   * Polecenia: pozostaw to pole puste. Ponieważ ten obraz ma zdefiniowany punkt wejścia obciążenia, nie musisz jawnie określić poleceń wejściowych (są to polecenia uruchamiane wewnątrz kontenera, które zapewnią działanie kontenera po uruchomieniu).
   * Liczba wystąpień aplikacji kontenera gościa: 1

   ![Generator Yeoman usługi Service Fabric dla kontenerów](./media/service-fabric-get-started-tomcat/yo-generator.png)

10. W manifeście usługi (*ServiceFabricTomcat/ServiceFabricTomcat/TomcatServicePkg/ServiceManifest.xml*) Dodaj następujący kod XML w głównym tagu **ServiceManfest** , aby otworzyć port, na którym aplikacja nasłuchuje żądań. Tag **punktu końcowego** deklaruje protokół i Port punktu końcowego. W tym artykule Usługa kontenerów nasłuchuje na porcie 8080: 

   ```xml
   <Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
       listen. Please note that if your service is partitioned, this port is shared with 
       replicas of different partitions that are placed in your code. -->
      <Endpoint Name="endpointTest" Port="8080" Protocol="tcp"/>
    </Endpoints>
   </Resources>
   ```

11. W manifeście aplikacji (*ServiceFabricTomcat/ServiceFabricTomcat/ApplicationManifest.xml*) pod tagiem **ServiceManifestImport** Dodaj następujący kod XML. Zastąp wartości **AccountName** i **Password** w tagu **RepositoryCredentials** nazwą rejestru kontenera i hasłem wymaganym do zalogowania się do niego.

   ```xml
   <Policies>
    <ContainerHostPolicies CodePackageRef="Code">
      <PortBinding ContainerPort="8080" EndpointRef="endpointTest"/>
      <RepositoryCredentials AccountName="myregistry" Password="=P==/==/=8=/=+u4lyOB=+=nWzEeRfF=" PasswordEncrypted="false"/>
    </ContainerHostPolicies>
   </Policies>
   ```

   Tag **ContainerHostPolicies** określa zasady dotyczące aktywowania hostów kontenerów.
    
   * Tag **portbinding** konfiguruje zasady mapowania portów portów do hosta. Atrybut **ContainerPort** jest ustawiony na 8080, ponieważ kontener uwidacznia port 8080, jak określono w pliku dockerfile. Atrybut **EndpointRef** jest ustawiony na wartość "endpointTest", punkt końcowy zdefiniowany w manifeście usługi w poprzednim kroku. W rezultacie żądania przychodzące do usługi na porcie 8080 są mapowane na port 8080 w kontenerze. 
   * Tag **RepositoryCredentials** określa poświadczenia wymagane przez kontener do uwierzytelnienia w repozytorium (prywatnym), w którym pobiera obraz z programu. Te zasady nie są potrzebne, jeśli obraz zostanie pobrany z repozytorium publicznego.
    

12. W folderze *ServiceFabricTomcat* Połącz się z klastrem usługi Service Fabric. 

   * Aby nawiązać połączenie z lokalnym klastrem Service Fabric, uruchom polecenie:

     ```bash
     sfctl cluster select --endpoint http://localhost:19080
     ```
    
   * Aby nawiązać połączenie z bezpiecznym klastrem platformy Azure, upewnij się, że certyfikat klienta znajduje się jako plik PEM w katalogu *ServiceFabricTomcat* i uruchom polecenie: 

     ```bash
     sfctl cluster select --endpoint https://PublicIPorFQDN:19080 -pem your-certificate.pem -no-verify
     ```
     W poprzednim poleceniu Zastąp `your-certificate.pem` wartość nazwą pliku certyfikatu klienta. W środowiskach deweloperskich i testowych certyfikat klastra jest często używany jako certyfikat klienta. Jeśli certyfikat nie jest podpisany z podpisem własnym, Pomiń ten `-no-verify` parametr. 
       
     Certyfikaty klastra są zwykle pobierane lokalnie jako pliki PFX. Jeśli nie masz jeszcze certyfikatu w formacie PEM, możesz uruchomić następujące polecenie, aby utworzyć plik PEM z pliku PFX:

     ```bash
     openssl pkcs12 -in your-certificate.pfx -out your-certificate.pem -nodes -passin pass:your-pfx-password
     ```

     Jeśli plik PFX nie jest chroniony hasłem, użyj `-passin pass:` dla ostatniego parametru.


13. Uruchom skrypt instalacji podany w szablonie, aby wdrożyć aplikację w klastrze. Skrypt kopiuje pakiet aplikacji do magazynu obrazów klastra, rejestruje typ aplikacji i tworzy wystąpienie aplikacji.

     ```bash
     ./install.sh
     ```

   Po uruchomieniu skryptu instalacji Otwórz przeglądarkę i przejdź do Service Fabric Explorer:
    
   * W klastrze lokalnym Użyj opcji `http://localhost:19080/Explorer` (Zastąp *localhost* jako prywatny adres IP maszyny wirtualnej, jeśli używasz Vagrant na Mac OS X).
   * W zabezpieczonym klastrze platformy Azure Użyj programu `https://PublicIPorFQDN:19080/Explorer` . 
    
   Rozwiń węzeł **aplikacje** i zwróć uwagę, że istnieje teraz wpis dla typu aplikacji, **ServiceFabricTomcatType**i drugiego dla pierwszego wystąpienia tego typu. Aby aplikacja mogła zostać całkowicie wdrożona, może upłynąć kilka minut.

   ![Service Fabric Explorer](./media/service-fabric-get-started-tomcat/service-fabric-explorer.png)


1. Aby uzyskać dostęp do aplikacji na serwerze Tomcat, Otwórz okno przeglądarki i wprowadź dowolny z następujących adresów URL. Jeśli wdrożono w klastrze lokalnym, użyj *localhost* dla *PublicIPorFQDN*. Zobaczysz wariant "Hello world!" ekran powitalny dla każdego adresu URL.

   * http://PublicIPorFQDN:8080/hello  
   * http://PublicIPorFQDN:8080/hello/sayhello
   * http://PublicIPorFQDN:8080/hello/sayhi

## <a name="clean-up"></a>Czyszczenie
Użyj skryptu dezinstalacji podanego w szablonie, aby usunąć wystąpienie aplikacji z klastra i wyrejestrować typ aplikacji.

```bash
./uninstall.sh
```

Po wypchnięciu obrazu do rejestru kontenerów można usunąć lokalny obraz z komputera dewelopera:

```
docker rmi tomcattest
docker rmi myregistry.azurecr.io/samples/tomcattest
```

## <a name="next-steps"></a>Następne kroki
* Aby zapoznać się z szybkimi krokami dotyczącymi dodatkowych funkcji kontenera systemu Linux, przeczytaj artykuł [Tworzenie pierwszej Service Fabric aplikacji kontenera w systemie Linux](service-fabric-get-started-containers-linux.md).
* Aby uzyskać bardziej szczegółowe instrukcje dotyczące kontenerów systemu Linux, zapoznaj się z samouczkiem [Tworzenie aplikacji kontenera systemu Linux](service-fabric-tutorial-create-container-images.md) .
* Dowiedz się więcej o uruchamianiu [kontenerów w usłudze Service Fabric](service-fabric-containers-overview.md).


