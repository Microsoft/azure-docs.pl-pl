---
title: Interfejsy API klienta Java Service Fabric platformy Azure
description: Generowanie i używanie Service Fabric interfejsów API klienta Java za pomocą Service Fabric specyfikacji interfejsu API REST klienta
author: rapatchi
ms.topic: conceptual
ms.date: 11/27/2017
ms.custom: devx-track-java
ms.author: rapatchi
ms.openlocfilehash: 24ee4a3d8109626bf93e01fbd10c00140762c9c6
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "87324628"
---
# <a name="azure-service-fabric-java-client-apis"></a>Interfejsy API klienta Java Service Fabric platformy Azure

Service Fabric interfejsy API klienta umożliwiają wdrażanie aplikacji i kontenerów opartych na mikrousługach oraz zarządzanie nimi w klastrze Service Fabric na platformie Azure, lokalnie, na lokalnym komputerze deweloperskim lub w innej chmurze. W tym artykule opisano sposób generowania i używania interfejsów API klienta Service Fabric Java na podstawie interfejsów API REST klienta Service Fabric

## <a name="generate-the-client-code-using-autorest"></a>Generowanie kodu klienta przy użyciu funkcji AutoRest

[AutoRest](https://github.com/Azure/autorest) to narzędzie, które generuje biblioteki klienckie umożliwiające dostęp do usług sieci Web RESTful. Dane wejściowe do AutoRest to specyfikacja opisująca interfejs API REST przy użyciu formatu specyfikacji OpenAPI. [Interfejsy API REST klienta Service Fabric](https://github.com/Azure/azure-rest-api-specs/tree/master/specification/servicefabric/data-plane) są zgodne z tą specyfikacją.

Wykonaj kroki opisane poniżej, aby wygenerować Service Fabric kod klienta Java za pomocą narzędzia AutoRest.

1. Zainstaluj środowisko NodeJs i menedżera NPM na swojej maszynie

    Jeśli używasz systemu Linux, to:
    ```bash
    sudo apt-get install npm
    sudo apt install nodejs
    ```
    Jeśli używasz Mac OS X then:
    ```bash
    brew install node
    ```

2. Zainstaluj program AutoRest przy użyciu NPM.
    ```bash
    npm install -g autorest
    ```

3. Rozwidlenie i klonowanie repozytorium [specyfikacji platformy Azure-REST-API-specyfikacje](https://github.com/Azure/azure-rest-api-specs)  na maszynie lokalnej i przejście do sklonowanej lokalizacji z terminalu maszyny.


4. Przejdź do lokalizacji wymienionej poniżej w sklonowanym repozytorium.
    ```bash
    cd specification\servicefabric\data-plane\Microsoft.ServiceFabric\stable\6.0
    ```

    > [!NOTE]
    > Jeśli wersja klastra nie jest 6,0. * Przejdź do odpowiedniego katalogu w folderze stabilnym.
    >   

5. Uruchom następujące polecenie AutoRest w celu wygenerowania kodu klienta Java.
    
    ```bash
    autorest --input-file= servicefabric.json --java --output-folder=[output-folder-name] --namespace=[namespace-of-generated-client]
    ```
   Poniżej znajduje się przykład pokazujący użycie AutoRest.
   
    ```bash
    autorest --input-file=servicefabric.json --java --output-folder=java-rest-api-code --namespace=servicefabricrest
    ```
   
   Następujące polecenie pobiera ``servicefabric.json`` plik specyfikacji jako dane wejściowe i generuje kod klienta Java w ``java-rest-api-     code`` folderze i umieszcza kod w  ``servicefabricrest`` przestrzeni nazw. Po wykonaniu tego kroku znajdziesz dwa foldery ``models`` ``implementation`` i dwa pliki ``ServiceFabricClientAPIs.java`` i ``package-info.java`` wygenerowane w ``java-rest-api-code`` folderze.


## <a name="include-and-use-the-generated-client-in-your-project"></a>Uwzględnianie wygenerowanego klienta w projekcie i korzystanie z niego

1. Dodaj wygenerowany kod odpowiednio do projektu. Zalecamy utworzenie biblioteki przy użyciu wygenerowanego kodu i dołączenie tej biblioteki do projektu.
2. W przypadku tworzenia biblioteki należy uwzględnić w projekcie biblioteki następujący zależność. Jeśli używasz innego podejścia, należy odpowiednio uwzględnić zależność.

    ```
        GroupId:  com.microsoft.rest
        Artifactid: client-runtime
        Version: 1.2.1
    ```
    Na przykład jeśli używasz Maven build system w ``pom.xml`` pliku:

    ```xml
        <dependency>
          <groupId>com.microsoft.rest</groupId>
          <artifactId>client-runtime</artifactId>
          <version>1.2.1</version>
        </dependency>
    ```

3. Utwórz RestClient przy użyciu następującego kodu:

    ```java
        RestClient simpleClient = new RestClient.Builder()
            .withBaseUrl("http://<cluster-ip or name:port>")
            .withResponseBuilderFactory(new ServiceResponseBuilder.Factory())
            .withSerializerAdapter(new JacksonAdapter())
            .build();
        ServiceFabricClientAPIs client = new ServiceFabricClientAPIsImpl(simpleClient);
    ```
4. Użyj obiektu klienta i wykonaj odpowiednie wywołania zgodnie z potrzebami. Poniżej przedstawiono kilka przykładów demonstrujących użycie obiektu klienta. Przyjęto założenie, że pakiet aplikacji został skompilowany i przekazany do magazynu obrazów przed użyciem poniższego interfejsu API.
    * Inicjowanie obsługi administracyjnej aplikacji
    
        ```java
            ApplicationTypeImageStorePath imageStorePath = new ApplicationTypeImageStorePath();
            imageStorePath.withApplicationTypeBuildPath("<application-path-in-image-store>");
            client.provisionApplicationType(imageStorePath);
        ```
    * Tworzenie aplikacji

        ```java
            ApplicationDescription applicationDescription = new ApplicationDescription();
            applicationDescription.withName("<application-uri>");
            applicationDescription.withTypeName("<application-type>");
            applicationDescription.withTypeVersion("<application-version>");
            client.createApplication(applicationDescription);
        ```

## <a name="understanding-the-generated-code"></a>Zrozumienie wygenerowanego kodu
Dla każdego interfejsu API znajdują się cztery przeciążenia implementacji. Jeśli istnieją parametry opcjonalne, można znaleźć cztery więcej wariantów, w tym te parametry opcjonalne. Na przykład rozważmy interfejs API ``removeReplica`` .
 1. **Public void removeReplica (ciąg nodename, UUID partitionId, String replicaId, Boolean forceRemove, długi limit czasu)**
    * Jest to synchroniczny wariant wywołania interfejsu API removeReplica
 2. **Public servicefuture \<Void> removeReplicaAsync (ciąg nodename, UUID partitionId, String replicaId, Boolean forceRemove, długi limit czasu, końcowe servicecallback servicecallback \<Void> )**
    * Tego wariantu wywołania interfejsu API można użyć, jeśli chcesz wykorzystać przyszłe programowanie asynchroniczne i użyć wywołania zwrotnego
 3. **publiczny zauważalny \<Void> removeReplicaAsync (ciąg nodename, UUID partitionId, ciąg replicaId)**
    * Tego wariantu wywołania interfejsu API można użyć, jeśli chcesz użyć reaktywnego programowania asynchronicznego
 4. **publiczne zauważalne \<ServiceResponse\<Void>> removeReplicaWithServiceResponseAsync (ciąg nodename, UUID partitionId, ciąg replicaId)**
    * Tego wariantu wywołania interfejsu API można użyć, jeśli chcesz używać reaktywnego programowania asynchronicznego i obsługiwać niesformatowaną odpowiedź REST

## <a name="next-steps"></a>Następne kroki
* Dowiedz się więcej na temat [Service Fabric interfejsów API REST](/rest/api/servicefabric/)
