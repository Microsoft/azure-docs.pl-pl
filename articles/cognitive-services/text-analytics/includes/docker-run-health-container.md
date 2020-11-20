---
title: Przykład uruchomienia kontenera dla polecenia Docker Run
titleSuffix: Azure Cognitive Services
description: Polecenie Docker Run dla kontenera kondycji
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 11/12/2020
ms.author: aahi
ms.openlocfilehash: b19fb3f86be46a5db60fb87f9c7f5c3e28ac6428
ms.sourcegitcommit: cd9754373576d6767c06baccfd500ae88ea733e4
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/20/2020
ms.locfileid: "94965150"
---
## <a name="install-the-container"></a>Instalowanie kontenera

Istnieje wiele sposobów instalowania i uruchamiania analiza tekstu dla kontenera kondycji. 

- Użyj [Azure Portal](../how-tos/text-analytics-how-to-install-containers.md?tabs=healthcare) , aby utworzyć zasób analiza tekstu i użyć platformy Docker do pobrania kontenera.
- Aby zautomatyzować wdrażanie zasobów i konfigurację kontenera, należy użyć następujących skryptów interfejsu wiersza polecenia programu PowerShell i platformy Azure.

### <a name="run-the-container-locally"></a>Uruchamianie kontenera w środowisku lokalnym

Aby uruchomić kontener we własnym środowisku po pobraniu obrazu kontenera, Znajdź jego identyfikator obrazu:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Wykonaj następujące `docker run` polecenie. Zastąp Poniższe symbole zastępcze własnymi wartościami:

| Symbol zastępczy | Wartość | Format lub przykład |
|-------------|-------|---|
| **{API_KEY}** | Klucz dla zasobu analiza tekstu. Można je znaleźć na stronie **klucz zasobu i punkt końcowy** na Azure Portal. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | Punkt końcowy do uzyskiwania dostępu do interfejs API analizy tekstu. Można je znaleźć na stronie **klucz zasobu i punkt końcowy** na Azure Portal. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | Identyfikator obrazu dla kontenera. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | Katalog wejściowy dla kontenera. | Systemy `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

To polecenie:

- Przyjęto, że katalog wejściowy istnieje na komputerze hosta
- Uruchamia analiza tekstu dla kontenera kondycji z obrazu kontenera
- Przydziela 6 rdzeni procesora CPU i 12 gigabajtów (GB) pamięci
- Udostępnia port TCP 5000 i przydziela pseudo-TTY dla kontenera
- Automatycznie usuwa kontener po zakończeniu. Obraz kontenera jest nadal dostępny na komputerze-hoście.

### <a name="demo-ui-to-visualize-output"></a>Demonstracja interfejsu użytkownika do wizualizacji danych wyjściowych

> [!NOTE]
> Demonstracja jest dostępna tylko w przypadku kontenera analiza tekstu dla usługi kondycji.

Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań.  W kontenerze, który jest dostępny przez dołączenie do punktu końcowego kontenera, udostępniono również narzędzie do wizualizacji `/demo` . Przykład:

```
http://<serverURL>:5000/demo
```

Użyj przykładowego żądania zwinięcie poniżej, aby przesłać zapytanie do wdrożonego kontenera zastępującego `serverURL` zmienną odpowiednią wartością.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

### <a name="install-the-container-using-azure-web-app-for-containers"></a>Instalowanie kontenera przy użyciu usługi Azure Web App for Containers

Azure [Web App for Containers](https://azure.microsoft.com/services/app-service/containers/) to zasób platformy Azure przeznaczony do uruchamiania kontenerów w chmurze. Oferuje ona wbudowane funkcje, takie jak Skalowanie automatyczne, obsługa kontenerów platformy Docker i tworzenie rozwiązań Docker, obsługa protokołu HTTPS i wiele innych.

> [!NOTE]
> Przy użyciu aplikacji sieci Web platformy Azure automatycznie otrzymasz domenę w postaci `<appservice_name>.azurewebsites.net`

Uruchom ten skrypt programu PowerShell przy użyciu interfejsu wiersza polecenia platformy Azure, aby utworzyć Web App for Containers przy użyciu subskrypcji i obrazu kontenera za pośrednictwem protokołu HTTPS. Poczekaj na zakończenie wykonywania skryptu (około 25-30 minut) przed przesłaniem pierwszego żądania.

```bash
$subscription_name = ""                    # THe name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           #    and AppSerivce to be attached to.
$resources_location = ""                   # This is the location you wish the AppServicePlan to be deployed to.
                                           #    You can use the "az account list-locations -o table" command to
                                           #    get the list of available locations and location code names.
$appservice_plan_name = ""                 # This is the AppServicePlan name you wish to have.
$appservice_name = ""                      # This is the AppService resource name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az appservice plan create -n $appservice_plan_name -g $resource_group_name --is-linux -l $resources_location --sku P3V2
az webapp create -g $resource_group_name -p $appservice_plan_name -n $appservice_name -i $DOCKER_IMAGE_NAME -s $DOCKER_REGISTRY_SERVER_USERNAME -w $DOCKER_REGISTRY_SERVER_PASSWORD
az webapp config appsettings set -g $resource_group_name -n $appservice_name --settings Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: https://<appservice_name>.azurewebsites.net
```

### <a name="install-the-container-using-azure-container-instance"></a>Instalowanie kontenera przy użyciu usługi Azure Container instance

Możesz również użyć wystąpienia kontenera platformy Azure (ACI), aby ułatwić wdrażanie. ACI to zasób, który umożliwia uruchamianie kontenerów platformy Docker na żądanie w zarządzanym, bezserwerowym środowisku platformy Azure. 

Zapoznaj się z tematem [jak używać Azure Container Instances](../how-tos/text-analytics-how-to-use-container-instances.md) kroków dotyczących wdrażania zasobu ACI przy użyciu Azure Portal. Możesz też użyć poniższego skryptu programu PowerShell przy użyciu interfejsu wiersza polecenia platformy Azure, który spowoduje utworzenie ACI w subskrypcji przy użyciu obrazu kontenera.  Poczekaj na zakończenie wykonywania skryptu (około 25-30 minut) przed przesłaniem pierwszego żądania.  Ze względu na limit maksymalnej liczby procesorów CPU na ACI zasób nie należy zaznaczać tej opcji, jeśli oczekuje się przesłania więcej niż 5 dużych dokumentów (około 5000 znaków) na żądanie.
Aby uzyskać informacje o dostępności, zobacz artykuł dotyczący [pomocy regionalnej ACI](../../../container-instances/container-instances-region-availability.md) . 

> [!NOTE] 
> Azure Container Instances nie Uwzględniaj obsługi protokołu HTTPS dla domen wbudowanych. Jeśli potrzebujesz protokołu HTTPS, konieczne będzie jego ręczne skonfigurowanie, w tym utworzenie certyfikatu i zarejestrowanie domeny. Instrukcje można znaleźć w NGINX poniżej.

```bash
$subscription_name = ""                    # The name of the subscription you want you resource to be created on.
$resource_group_name = ""                  # The name of the resource group you want the AppServicePlan
                                           # and AppService to be attached to.
$resources_location = ""                   # This is the location you wish the web app to be deployed to.
                                           # You can use the "az account list-locations -o table" command to
                                           # Get the list of available locations and location code names.
$azure_container_instance_name = ""        # This is the AzureContainerInstance name you wish to have.
$TEXT_ANALYTICS_RESOURCE_API_KEY = ""      # This should be taken from the Text Analytics resource.
$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT = "" # This should be taken from the Text Analytics resource.
$DOCKER_REGISTRY_SERVER_PASSWORD = ""      # This will be provided separately.
$DOCKER_REGISTRY_SERVER_USERNAME = ""      # This will be provided separately.
$DNS_LABEL = ""                            # This is the DNS label name you wish your ACI will have
$DOCKER_REGISTRY_LOGIN_SERVER = "containerpreview.azurecr.io"
$DOCKER_IMAGE_NAME = "containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest"

az login
az account set -s $subscription_name
az container create --resource-group $resource_group_name --name $azure_container_instance_name --image $DOCKER_IMAGE_NAME --cpu 4 --memory 12 --registry-login-server $DOCKER_REGISTRY_LOGIN_SERVER --registry-username $DOCKER_REGISTRY_SERVER_USERNAME --registry-password $DOCKER_REGISTRY_SERVER_PASSWORD --port 5000 --dns-name-label $DNS_LABEL --environment-variables Eula=accept Billing=$TEXT_ANALYTICS_RESOURCE_API_ENDPOINT ApiKey=$TEXT_ANALYTICS_RESOURCE_API_KEY

# Once deployment complete, the resource should be available at: http://<unique_dns_label>.<resource_group_region>.azurecontainer.io:5000
```

### <a name="secure-aci-connectivity"></a>Bezpieczna łączność ACI

Domyślnie w przypadku korzystania z ACI z interfejsem API kontenera nie są dostępne żadne zabezpieczenia. Wynika to z faktu, że zwykle kontenery będą uruchamiane jako część elementu, który jest chroniony przez mostek sieciowy. Można jednak zmodyfikować kontener z składnikiem frontonu, zachowując prywatny punkt końcowy kontenera. W poniższych przykładach użyto [Nginx](https://www.nginx.com) jako bramy ruchu przychodzącego do obsługi protokołu HTTPS/SSL i uwierzytelniania za pomocą certyfikatu klienta.

> [!NOTE]
> NGINX to serwer HTTP, który jest serwerem proxy o wysokiej wydajności i serwerze. Kontener NGINX może służyć do kończenia połączenia TLS dla jednego kontenera. Dostępne są również bardziej złożone rozwiązania do zakończenia protokołu TLS oparte na protokole NGINX.

#### <a name="set-up-nginx-as-an-ingress-gateway"></a>Skonfiguruj NGINX jako bramę transferu danych przychodzących

NGINX używa [plików konfiguracji](https://docs.nginx.com/nginx/admin-guide/basic-functionality/managing-configuration-files/) do włączania funkcji w czasie wykonywania. Aby włączyć zakończenie protokołu TLS dla innej usługi, należy określić certyfikat SSL, aby zakończyć połączenie TLS i  `proxy_pass` określić adres usługi. Poniżej przedstawiono przykład.


> [!NOTE]
> `ssl_certificate` oczekuje ścieżki, która ma być określona w lokalnym systemie plików kontenera NGINX. Adres określony dla elementu `proxy_pass` musi być dostępny w sieci kontenera Nginx.

Kontener NGINX będzie ładować wszystkie pliki w `_.conf_` folderze, które są zainstalowane w `/etc/nginx/conf.d/` ścieżce konfiguracji http.

```nginx
server {
  listen              80;
  return 301 https://$host$request_uri;
}
server {
  listen              443 ssl;
  # replace with .crt and .key paths
  ssl_certificate     /cert/Local.crt;
  ssl_certificate_key /cert/Local.key;

  location / {
    proxy_pass http://cognitive-service:5000;
    proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
    proxy_set_header X-Real-IP  $remote_addr;
  }
}
```

#### <a name="example-docker-compose-file"></a>Przykładowy plik redagowania platformy Docker

Poniższy przykład pokazuje, jak można utworzyć plik [redagowania platformy Docker](https://docs.docker.com/compose/reference/overview) w celu wdrożenia NGINX i analiza tekstu dla kontenerów kondycji:

```yaml
version: "3.7"
services:
  cognitive-service:
    image: {IMAGE_ID}
    ports:
      - 5000:5000
    environment:
      - eula=accept
      - billing={ENDPOINT_URI}
      - apikey={API_KEY}
      - Logging:Disk:Format=json
    volumes:
        # replace with path to logs folder
      - <path-to-logs-folder>:/output
  nginx:
    image: nginx
    ports:
      - 443:443
    volumes:
        # replace with paths for certs and conf folders
      - <path-to-certs-folder>:/cert
      - <path-to-conf-folder>:/etc/nginx/conf.d/
```

Aby zainicjować ten plik do redagowania platformy Docker, wykonaj następujące polecenie w konsoli na poziomie głównym pliku:

```bash
docker-compose up
```

Aby uzyskać więcej informacji, zobacz dokumentację NGINX na temat [Nginx protokołu SSL](https://docs.nginx.com/nginx/admin-guide/security-controls/terminating-ssl-http/).

