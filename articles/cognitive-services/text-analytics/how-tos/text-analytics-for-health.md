---
title: Jak używać analiza tekstu na potrzeby kondycji
titleSuffix: Azure Cognitive Services
description: Dowiedz się, jak wyodrębnić i oznaczyć informacje medyczne z niestrukturalnego tekstu klinicznego za pomocą analiza tekstu na potrzeby kondycji.
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: conceptual
ms.date: 08/06/2020
ms.author: aahi
ms.openlocfilehash: d9517eef8976e79db21fbe552861d0d59923e8ba
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/19/2020
ms.locfileid: "92173963"
---
# <a name="how-to-use-text-analytics-for-health-preview"></a>Instrukcje: korzystanie z analiza tekstu na potrzeby kondycji (wersja zapoznawcza)

> [!NOTE]
> Ostatnio Zaktualizowano analiza tekstu dla kontenera kondycji. Zobacz, [co nowego](../whats-new.md) , aby uzyskać więcej informacji na temat najnowszych zmian. Należy pamiętać o pociągnięciach najnowszych kontenerów, aby korzystać z aktualizacji.

> [!IMPORTANT] 
> Analiza tekstu dla kondycji to funkcja w wersji zapoznawczej udostępniona "AS IS" i "ze wszystkimi BŁĘDami". W związku z tym **Analiza tekstu dla kondycji (wersja zapoznawcza) nie należy implementować ani wdrażać w żadnym środowisku produkcyjnym.** Analiza tekstu dla kondycji nie są przeznaczone do użytku jako wyrób medyczny, pomoc techniczna kliniczna, narzędzie diagnostyczne ani inne technologie przeznaczone do celów diagnostycznych, pozyskiwania, łagodzenia, traktowania lub zapobiegania chorobom lub innym warunkom, a firma Microsoft nie udziela żadnych licencji ani prawa do korzystania z tej funkcji w taki sposób. Ta możliwość nie została zaprojektowana lub zaplanowana jako zamiennik do profesjonalnego doradztwa medycznego lub opinii o opieki zdrowotnej, diagnostyki, traktowania lub klinicznej oceny specjalisty ds. ochrony zdrowia i nie powinna być używana jako taka. Klient jest odpowiedzialny wyłącznie za korzystanie z analiza tekstu na potrzeby kondycji. Firma Microsoft nie gwarantuje, że analiza tekstu w zakresie kondycji lub materiały dostarczone w związku z tą możliwością będą wystarczające dla jakichkolwiek celów medycznych lub w inny sposób spełniają wymagania zdrowotne lub medyczne każdej osoby. 


Analiza tekstu dla kondycji jest usługą kontenerową, która wyodrębnia i etykietuje odpowiednie informacje medyczne z tekstów bez struktury, takich jak notatki lekarskie, podsumowania zrzutów, dokumenty kliniczne i rejestry kondycji elektronicznej.  

## <a name="features"></a>Funkcje

Analiza tekstu dla kontenera kondycji wykonuje obecnie nazwane rozpoznawanie jednostek (NER), wyodrębnianie relacji, negację jednostki oraz łączenie obiektów w języku angielskim w własnym środowisku programistycznym, które spełnia wymagania dotyczące zabezpieczeń i zarządzania danymi.

#### <a name="named-entity-recognition"></a>[Rozpoznawanie jednostek nazwanych](#tab/ner)

Rozpoznawanie jednostek nazwanych wykrywa słowa i frazy wymienione w tekście niestrukturalnym, które mogą być skojarzone z co najmniej jednym typem semantycznym, na przykład diagnostyką, nazwą leczenia, objawem/podpisem lub wiekiem.

> [!div class="mx-imgBorder"]
> ![NER kondycji](../media/ta-for-health/health-named-entity-recognition.png)

#### <a name="relation-extraction"></a>[Wyodrębnianie relacji](#tab/relation-extraction)

Wyodrębnienie relacji identyfikuje znaczące połączenia między pojęciami wymienionymi w tekście. Na przykład relacja "czas warunku" można znaleźć, kojarząc nazwę warunku z godziną. 

> [!div class="mx-imgBorder"]
> ![Kondycja](../media/ta-for-health/health-relation-extraction.png)


#### <a name="entity-linking"></a>[Łączenie jednostek](#tab/entity-linking)

Łączenie jednostek odróżnia odrębne jednostki, kojarząc nazwane jednostki wymienione w tekście z pojęciami znajdującymi się w wstępnie zdefiniowanej bazie danych koncepcji. Na przykład ujednolicony system językowy (UMLS).

> [!div class="mx-imgBorder"]
> ![Stan EL](../media/ta-for-health/health-entity-linking.png)

Analiza tekstu dla kondycji obsługuje łączenie z słownikami kondycji i biomedycznej, które znajdują się w źródle wiedzy o systemie Unified[UMLS](https://www.nlm.nih.gov/research/umls/sourcereleasedocs/index.html).

#### <a name="negation-detection"></a>[Wykrywanie negacji](#tab/negation-detection) 

Znaczenie dla zawartości medycznej ma duże wpływ na modyfikatory, takie jak Negacja, co może mieć krytyczne znaczenie w przypadku niezdiagnozowania. Analiza tekstu dla kondycji obsługuje wykrywanie negacji dla różnych jednostek wymienionych w tekście. 

> [!div class="mx-imgBorder"]
> ![Kondycja — minus](../media/ta-for-health/health-negation.png)

---

Aby uzyskać pełną listę obsługiwanych jednostek, zobacz [Kategorie jednostek](../named-entity-types.md?tabs=health) zwrócone przez analiza tekstu dla kondycji.

## <a name="supported-languages"></a>Obsługiwane języki

Analiza tekstu dla kondycji obsługuje tylko dokumenty w języku angielskim.

## <a name="request-access-to-the-container-registry"></a>Zażądaj dostępu do rejestru kontenerów

Wypełnij i prześlij [formularz żądania Cognitive Services kontenerów](https://aka.ms/csgate) , aby zażądać dostępu do kontenera. Obecnie nie są naliczane opłaty za analiza tekstu za użycie kondycji. 

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

[!INCLUDE [Authenticate to the container registry](../../../../includes/cognitive-services-containers-access-registry.md)]

## <a name="install-the-container"></a>Instalowanie kontenera

Istnieje wiele sposobów instalowania i uruchamiania kontenera. 

- Użyj [Azure Portal](text-analytics-how-to-install-containers.md?tabs=healthcare) , aby utworzyć zasób analiza tekstu i użyć platformy Docker do pobrania kontenera.
- Aby zautomatyzować konfigurację kontenera wdrażania zasobów, należy użyć następujących skryptów [interfejsu wiersza polecenia](https://docs.microsoft.com/cli/azure/?view=azure-cli-latest) programu PowerShell i platformy Azure.

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

Zapoznaj się z tematem [jak używać Azure Container Instances](text-analytics-how-to-use-container-instances.md) kroków dotyczących wdrażania zasobu ACI przy użyciu Azure Portal. Możesz też użyć poniższego skryptu programu PowerShell przy użyciu interfejsu wiersza polecenia platformy Azure, który spowoduje utworzenie ACI w subskrypcji przy użyciu obrazu kontenera.  Poczekaj na zakończenie wykonywania skryptu (około 25-30 minut) przed przesłaniem pierwszego żądania.  Ze względu na limit maksymalnej liczby procesorów CPU na ACI zasób nie należy zaznaczać tej opcji, jeśli oczekuje się przesłania więcej niż 5 dużych dokumentów (około 5000 znaków) na żądanie.
Aby uzyskać informacje o dostępności, zobacz artykuł dotyczący [pomocy regionalnej ACI](https://docs.microsoft.com/azure/container-instances/container-instances-region-availability) . 

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


## <a name="example-api-request"></a>Przykładowe żądanie interfejsu API
Kontener udostępnia oparte na interfejsie REST interfejsy API punktu końcowego przewidywania zapytań.  Udostępniono również narzędzie do wizualizacji w kontenerze, które jest dostępne przez dołączenie **pokazu** do punktu końcowego kontenera na przykład:

```bash
http://<serverURL>:5000/demo
```

Użyj przykładowego żądania zwinięcie poniżej, aby przesłać zapytanie do wdrożonego kontenera zastępującego `serverURL` zmienną odpowiednią wartością.

```bash
curl -X POST 'http://<serverURL>:5000/text/analytics/v3.2-preview.1/entities/health' --header 'Content-Type: application/json' --header 'accept: application/json' --data-binary @example.json

```

Poniższy kod JSON jest przykładem pliku JSON dołączonego do analiza tekstu dla treści wpisu żądania interfejsu API kondycji:

```json
example.json

{
  "documents": [
    {
      "language": "en",
      "id": "1",
      "text": "Patient reported itchy sores after swimming in the lake."
    },
    {
      "language": "en",
      "id": "2",
      "text": "Prescribed 50mg benadryl, taken twice daily."
    }
  ]
}
```

## <a name="api-response-body"></a>Treść odpowiedzi interfejsu API

Poniższy kod JSON jest przykładem treści odpowiedzi interfejsu API kondycji analiza tekstu:

```json
{
    "documents": [
        {
            "id": "1",
            "entities": [
                {
                    "id": "0",
                    "offset": 17,
                    "length": 11,
                    "text": "itchy sores",
                    "category": "SymptomOrSign",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ]
        },
        {
            "id": "2",
            "entities": [
                {
                    "id": "0",
                    "offset": 11,
                    "length": 4,
                    "text": "50mg",
                    "category": "Dosage",
                    "confidenceScore": 1.0,
                    "isNegated": false
                },
                {
                    "id": "1",
                    "offset": 16,
                    "length": 8,
                    "text": "benadryl",
                    "category": "MedicationName",
                    "confidenceScore": 1.0,
                    "isNegated": false,
                    "links": [
                        {
                            "dataSource": "UMLS",
                            "id": "C0700899"
                        },
                        {
                            "dataSource": "CHV",
                            "id": "0000044903"
                        },
                        {
                            "dataSource": "MMSL",
                            "id": "899"
                        },
                        {
                            "dataSource": "MSH",
                            "id": "D004155"
                        },
                        {
                            "dataSource": "NCI",
                            "id": "C300"
                        },
                        {
                            "dataSource": "NCI_DTP",
                            "id": "NSC0033299"
                        },
                        {
                            "dataSource": "PDQ",
                            "id": "CDR0000039163"
                        },
                        {
                            "dataSource": "PSY",
                            "id": "05760"
                        },
                        {
                            "dataSource": "RXNORM",
                            "id": "203457"
                        }
                    ]
                },
                {
                    "id": "2",
                    "offset": 32,
                    "length": 11,
                    "text": "twice daily",
                    "category": "Frequency",
                    "confidenceScore": 1.0,
                    "isNegated": false
                }
            ],
            "relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
        }
    ],
    "errors": [],
    "modelVersion": "2020-07-24"
}
```

### <a name="negation-detection-output"></a>Dane wyjściowe wykrywania negacji

W przypadku korzystania z wykrywania negacji w niektórych przypadkach pojedynczy termin negacji może zająć kilka warunków jednocześnie. Negacja rozpoznanej jednostki jest reprezentowana w danych wyjściowych JSON przez wartość logiczną `isNegated` flagi:

```json
{
  "id": "2",
  "offset": 90,
  "length": 10,
  "text": "chest pain",
  "category": "SymptomOrSign",
  "score": 0.9972,
  "isNegated": true,
  "links": [
    {
      "dataSource": "UMLS",
      "id": "C0008031"
    },
    {
      "dataSource": "CHV",
      "id": "0000023593"
    },
    ...
```

### <a name="relation-extraction-output"></a>Dane wyjściowe wyodrębniania relacji

Dane wyjściowe wyodrębniania relacji zawierają odwołania identyfikatora URI do *źródła* relacji i jego *celu*. Jednostki z rolą relacji `ENTITY` są przypisane do `target` pola. Jednostki z rolą relacji `ATTRIBUTE` są przypisane do `source` pola. Relacje skrótu zawierają dwukierunkowe `source` i `target` pola i `bidirectional` zostaną ustawione na `true` . 

```json
"relations": [
                {
                    "relationType": "DosageOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/0",
                    "target": "#/documents/1/entities/1"
                },
                {
                    "relationType": "FrequencyOfMedication",
                    "bidirectional": false,
                    "source": "#/documents/1/entities/2",
                    "target": "#/documents/1/entities/1"
                }
            ]
  },
...
]
```

## <a name="see-also"></a>Zobacz też

* [Przegląd analizy tekstu](../overview.md)
* [Kategorie nazwanych jednostek](../named-entity-types.md)
* [Co nowego](../whats-new.md)
