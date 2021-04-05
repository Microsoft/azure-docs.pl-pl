---
title: Wdrażanie aplikacji Java w klastrze Service Fabric na platformie Azure
description: W tym samouczku przedstawiono sposób wdrażania aplikacji Java usługi Service Fabric w klastrze usługi Azure Service Fabric.
ms.topic: tutorial
ms.date: 02/26/2018
ms.custom: mvc, devx-track-java, devx-track-azurecli
ms.openlocfilehash: 48cb7df49fb918ebd30883edc573cc63b6e35e4a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98789400"
---
# <a name="tutorial-deploy-a-java-application-to-a-service-fabric-cluster-in-azure"></a>Samouczek: wdrażanie aplikacji Java w klastrze usługi Service Fabric na platformie Azure

Niniejszy samouczek jest trzecią częścią serii. Przedstawiono w nim sposób wdrażania aplikacji usługi Service Fabric w klastrze na platformie Azure.

Część trzecia serii zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bezpiecznego klastra z systemem Linux na platformie Azure
> * Wdrażanie aplikacji w klastrze

Ta seria samouczków zawiera informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * [Kompilowanie aplikacji Java usług Reliable Services w usłudze Service Fabric](service-fabric-tutorial-create-java-app.md)
> * [Wdrażanie i debugowanie aplikacji w klastrze lokalnym](service-fabric-tutorial-debug-log-local-cluster.md)
> * Wdrażanie aplikacji w klastrze platformy Azure
> * [Konfigurowanie monitorowania i diagnostyki dla aplikacji](service-fabric-tutorial-java-elk.md)
> * [Konfigurowanie ciągłej integracji/ciągłego dostarczania](service-fabric-tutorial-java-jenkins.md)

## <a name="prerequisites"></a>Wymagania wstępne

Przed rozpoczęciem tego samouczka:

* Jeśli nie masz subskrypcji platformy Azure, Utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Instalowanie interfejsu wiersza polecenia platformy Azure](/cli/azure/install-azure-cli)
* Zainstaluj zestaw SDK usługi Service Fabric dla komputera [Mac](service-fabric-get-started-mac.md) lub [systemu Linux](service-fabric-get-started-linux.md)
* [Instalowanie środowiska języka Python 3](https://wiki.python.org/moin/BeginnersGuide/Download)

## <a name="create-a-service-fabric-cluster-in-azure"></a>Tworzenie klastra usługi Service Fabric na platformie Azure

Poniższe kroki powodują utworzenie niezbędnych zasobów wymaganych do wdrożenia aplikacji w klastrze usługi Service Fabric. Ponadto są konfigurowane zasoby niezbędne do monitorowania kondycji rozwiązania za pomocą stosu ELK (Elasticsearch, Logstash, Kibana). W szczególności usługa [Event Hubs](https://azure.microsoft.com/services/event-hubs/) jest używana jako obiekt sink dla dzienników z usługi Service Fabric. Jest on konfigurowana do wysyłania dzienników z klastra usługi Service Fabric do wystąpienia Logstash.

1. Otwórz terminal i pobierz poniższy pakiet, który zawiera niezbędne skrypty pomocnika oraz szablony umożliwiające tworzenie zasobów na platformie Azure

    ```bash
    git clone https://github.com/Azure-Samples/service-fabric-java-quickstart.git
    ```

2. Zaloguj się do swojego konta platformy Azure

    ```azurecli
    az login
    ```

3. Ustaw subskrypcję platformy Azure, której chcesz używać do tworzenia zasobów

    ```azurecli
    az account set --subscription [SUBSCRIPTION-ID]
    ```

4. Z poziomu folderu *service-fabric-java-quickstart/AzureCluster* uruchom poniższe polecenie, aby utworzyć certyfikat klastra w usłudze Key Vault. Ten certyfikat służy do zabezpieczania klastra usługi Service Fabric. Określ region (musi być taki sam jak klaster usługi Service Fabric), nazwę grupy zasobów magazynu kluczy, nazwę magazynu kluczy, hasło certyfikatu i nazwę DNS klastra.

    ```bash
    ./new-service-fabric-cluster-certificate.sh [REGION] [KEY-VAULT-RESOURCE-GROUP] [KEY-VAULT-NAME] [CERTIFICATE-PASSWORD] [CLUSTER-DNS-NAME-FOR-CERTIFICATE]

    Example: ./new-service-fabric-cluster-certificate.sh 'westus' 'testkeyvaultrg' 'testkeyvault' '<password>' 'testservicefabric.westus.cloudapp.azure.com'
    ```

    Poprzednie polecenie zwraca następujące informacje, które należy zapisać do późniejszego użycia.

    ```output
    Source Vault Resource Id: /subscriptions/<subscription_id>/resourceGroups/testkeyvaultrg/providers/Microsoft.KeyVault/vaults/<name>
    Certificate URL: https://<name>.vault.azure.net/secrets/<cluster-dns-name-for-certificate>/<guid>
    Certificate Thumbprint: <THUMBPRINT>
    ```

5. Utwórz grupę zasobów dla konta magazynu, w którym są przechowywane dzienniki

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name teststorageaccountrg
    ```

6. Utwórz konto magazynu, które będzie używane do przechowywania tworzonych dzienników

    ```azurecli
    az storage account create -g [RESOURCE-GROUP-NAME] -l [REGION] --name [STORAGE-ACCOUNT-NAME] --kind Storage

    Example: az storage account create -g teststorageaccountrg -l westus --name teststorageaccount --kind Storage
    ```

7. Uzyskaj dostęp do witryny [Azure Portal](https://portal.azure.com) i przejdź na kartę **Sygnatura dostępu współdzielonego** dla konta usługi Storage. Wygeneruj token sygnatury dostępu współdzielonego w przedstawiony poniżej sposób.

    ![Generowanie sygnatury dostępu współdzielonego dla magazynu](./media/service-fabric-tutorial-java-deploy-azure/storagesas.png)

8. Skopiuj adres URL sygnatury dostępu współdzielonego konta i pozostaw go do użycia podczas tworzenia klastra usługi Service Fabric. Jest on podobny do następującego adresu URL:

    ```output
    ?sv=2017-04-17&ss=bfqt&srt=sco&sp=rwdlacup&se=2018-01-31T03:24:04Z&st=2018-01-30T19:24:04Z&spr=https,http&sig=IrkO1bVQCHcaKaTiJ5gilLSC5Wxtghu%2FJAeeY5HR%2BPU%3D
    ```

9. Utwórz grupę zasobów, która zawiera zasoby usługi Event Hubs. Usługa Event Hubs służy do wysyłania komunikatów z usługi Service Fabric do serwera z uruchomionymi zasobami ELK.

    ```azurecli
    az group create --location [REGION] --name [RESOURCE-GROUP-NAME]

    Example: az group create --location westus --name testeventhubsrg
    ```

10. Utwórz zasób usługi Event Hubs przy użyciu poniższego polecenia. Postępuj zgodnie z monitami, aby wprowadzić następujące szczegóły: namespaceName, eventHubName, consumerGroupName, sendAuthorizationRule i receiveAuthorizationRule.

    ```azurecli
    az deployment group create -g [RESOURCE-GROUP-NAME] --template-file eventhubsdeploy.json

    Example:
    az deployment group create -g testeventhubsrg --template-file eventhubsdeploy.json
    Please provide string value for 'namespaceName' (? for help): testeventhubnamespace
    Please provide string value for 'eventHubName' (? for help): testeventhub
    Please provide string value for 'consumerGroupName' (? for help): testeventhubconsumergroup
    Please provide string value for 'sendAuthorizationRuleName' (? for help): sender
    Please provide string value for 'receiveAuthorizationRuleName' (? for help): receiver
    ```

    Skopiuj zawartość pola **danych wyjściowych** w danych wyjściowych JSON z poprzedniego polecenia. Informacje o nadawcy są używane podczas tworzenia klastra usługi Fabric Service. Nazwę i klucz odbiornika należy zapisać do użycia w następnym samouczku, gdy usługa Logstash będzie konfigurowana do odbierania wiadomości z centrum Event Hub. Następujący obiekt blob zawiera przykładowe dane wyjściowe JSON:

    ```json
    "outputs": {
        "receiver Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "receiver Name": {
            "type": "String",
            "value": "receiver"
        },
        "sender Key": {
            "type": "String",
            "value": "[KEY]"
        },
        "sender Name": {
            "type": "String",
            "value": "sender"
        }
    }
    ```

11. Uruchom skrypt *eventhubssastoken.py* w celu wygenerowania adresu URL sygnatury dostępu współdzielonego dla utworzonego zasobu EventHubs. Ten adres URL sygnatury dostępu współdzielonego jest używany przez klaster usługi Service Fabric do wysyłania dzienników do usługi Event Hubs. W wyniku zasady **nadawcy** są używane do generowania adresu URL. Skrypt zwraca adres URL sygnatury dostępu współdzielonego dla zasobu usługi Event Hubs, który jest używany w następnym kroku:

    ```python
    python3 eventhubssastoken.py 'testeventhubs' 'testeventhubs' 'sender' '[PRIMARY-KEY]'
    ```

    Skopiuj wartość pola **sr** pole do zwróconego kodu JSON. Wartość pola **sr** tokenu sygnatury dostępu współdzielonego dla usługi EventHubs. Następujący adres URL jest przykładem pola **sr**:

    ```output
    https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender
    ```

    Adres URL sygnatury dostępu współdzielonego dla EventHubs jest zgodny ze strukturą: `https://<namespacename>.servicebus.windows.net/<eventhubsname>?sr=<sastoken>` . Na przykład `https://testeventhubnamespace.servicebus.windows.net/testeventhub?sr=https%3A%2F%testeventhub.servicebus.windows.net%testeventhub&sig=7AlFYnbvEm%2Bat8ALi54JqHU4i6imoFxkjKHS0zI8z8I%3D&se=1517354876&skn=sender`

12. Otwórz plik *sfdeploy.parameters.json* i zastąp następującą zawartość w oparciu o poprzednie kroki. Wartość [SAS-URL-STORAGE-ACCOUNT] oznaczono w kroku 8. Wartość [SAS-URL-EVENT-HUBS] oznaczono w kroku 11.

    ```json
    "applicationDiagnosticsStorageAccountName": {
        "value": "teststorageaccount"
    },
    "applicationDiagnosticsStorageAccountSasToken": {
        "value": "[SAS-URL-STORAGE-ACCOUNT]"
    },
    "loggingEventHubSAS": {
        "value": "[SAS-URL-EVENT-HUBS]"
    }
    ```

13. Zostanie otwarty plik **sfdeploy.parameters.json**. Zmień następujące parametry, a następnie zapisz plik.
    - **clusterName**. Należy używać tylko małych liter i cyfr.
    - **adminUserName** (wartość parametru nie może być pusta)
    - **adminPassword** (wartość parametru nie może być pusta)

14. Uruchom następujące polecenie, aby utworzyć klaster usługi Service Fabric

    ```azurecli
    az sf cluster create --location 'westus' --resource-group 'testlinux' --template-file sfdeploy.json --parameter-file sfdeploy.parameters.json --secret-identifier <certificate_url_from_step4>
    ```

## <a name="deploy-your-application-to-the-cluster"></a>Wdrażanie aplikacji w klastrze

1. Przed wdrożeniem aplikacji dodaj następujący fragment kodu do pliku *Voting/VotingApplication/ApplicationManifest.xml*. Pole **X509FindValue** to odcisk palca zwrócony z kroku 4 sekcji **Tworzenie klastra usługi Service Fabric na platformie Azure**. Ten fragment kodu jest zagnieżdżony w polu **ApplicationManifest** (pole główne).

    ```xml
    <Certificates>
          <SecretsCertificate X509FindType="FindByThumbprint" X509FindValue="[CERTIFICATE-THUMBPRINT]" />
    </Certificates>
    ```

2. Aby wdrożyć aplikację w tym klastrze, należy użyć interfejsu SFCTL w celu nawiązania połączenia z klastrem. Aby nawiązać połączenie z klastrem, interfejs SFCTL wymaga pliku PEM zarówno z kluczem publicznym jak i prywatnym. Uruchom następujące polecenie, aby utworzyć plik PEM zarówno z kluczem publicznym jak i prywatnym. 

    ```bash
    openssl pkcs12 -in <clustername>.<region>.cloudapp.azure.com.pfx -out sfctlconnection.pem -nodes -passin pass:<password>
    ```

3. Uruchom następujące polecenie, aby połączyć się z klastrem.

    ```bash
    sfctl cluster select --endpoint https://<clustername>.<region>.cloudapp.azure.com:19080 --pem sfctlconnection.pem --no-verify
    ```

4. Aby wdrożyć aplikację, przejdź do folderu *Voting/Scripts* i uruchom skrypt **install.sh**.

    ```bash
    ./install.sh
    ```

5. Aby uzyskać dostęp do programu Service Fabric Explorer, otwórz przeglądarkę ulubionych i wpisz ciąg `https://testlinuxcluster.westus.cloudapp.azure.com:19080`. Z magazynu certyfikatów wybierz certyfikat do użycia w celu połączenia z tym punktem końcowym. Jeśli używasz maszyny z systemem Linux, certyfikaty wygenerowane przez skrypt *new-service-fabric-cluster-certificate.sh* należy zaimportować do programu Chrome, aby wyświetlić program Service Fabric Explorer. Jeśli używasz komputera Mac, musisz zainstalować plik PFX w łańcuchu kluczy. Zauważysz, że aplikacja została zainstalowana w klastrze.

    ![SFX — platforma Java Azure](./media/service-fabric-tutorial-java-deploy-azure/sfxjavaonazure.png)

6. Aby uzyskać dostęp do aplikacji, wpisz ciąg `https://testlinuxcluster.westus.cloudapp.azure.com:8080`

    ![Aplikacja do głosowania — platforma Java Azure](./media/service-fabric-tutorial-java-deploy-azure/votingappjavaazure.png)

7. Aby odinstalować aplikację z klastra, uruchom skrypt *uninstall.sh* w folderze **Skrypty**

    ```bash
    ./uninstall.sh
    ```

## <a name="next-steps"></a>Następne kroki

W niniejszym samouczku zawarto informacje na temat wykonywania następujących czynności:

> [!div class="checklist"]
> * Tworzenie bezpiecznego klastra z systemem Linux na platformie Azure
> * Tworzenie zasobów niezbędnych do monitorowania za pomocą rozwiązania ELK

Przejdź do następnego samouczka:
> [!div class="nextstepaction"]
> [Konfigurowanie monitorowania i diagnostyki](service-fabric-tutorial-java-elk.md)
