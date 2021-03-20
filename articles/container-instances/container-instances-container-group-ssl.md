---
title: Włączanie protokołu TLS z kontenerem przyczepki
description: Utwórz punkt końcowy SSL lub TLS dla grupy kontenerów działającej w Azure Container Instances przez uruchomienie Nginx w kontenerze przyczepki
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 6587a84e7cbe655c509f74e9e39e93010e7058be
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "96558083"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Włączanie punktu końcowego TLS w kontenerze przyczepki

W tym artykule pokazano, jak utworzyć [grupę kontenerów](container-instances-container-groups.md) z kontenerem aplikacji i kontenerem przyczepki z uruchomionym dostawcą protokołu TLS/SSL. Konfigurując grupę kontenerów za pomocą oddzielnego punktu końcowego TLS, należy włączyć połączenia TLS dla aplikacji bez konieczności zmiany kodu aplikacji.

Należy skonfigurować przykładową grupę kontenerów składającą się z dwóch kontenerów:
* Kontener aplikacji, w którym działa prosta aplikacja internetowa korzystająca z publicznego obrazu programu Microsoft [ACI-HelloWorld](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) . 
* Kontener przyczep z uruchomionym publicznym obrazem [Nginx](https://hub.docker.com/_/nginx) skonfigurowany do korzystania z protokołu TLS. 

W tym przykładzie grupa kontenerów uwidacznia tylko port 443 dla Nginx z jego publicznym adresem IP. Nginx przekierowuje żądania HTTPS do aplikacji sieci Web towarzyszącej, która nasłuchuje wewnętrznie na porcie 80. Możesz dostosować przykład dla aplikacji kontenera, które nasłuchują na innych portach. 

Zobacz [następne kroki](#next-steps) , aby uzyskać inne podejścia do włączania protokołu TLS w grupie kontenerów.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga wersji 2.0.55 lub nowszej interfejsu wiersza polecenia platformy Azure. W przypadku korzystania z Azure Cloud Shell Najnowsza wersja jest już zainstalowana.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Aby skonfigurować Nginx jako dostawcę TLS, wymagany jest certyfikat TLS/SSL. W tym artykule pokazano, jak utworzyć i skonfigurować certyfikat TLS/SSL z podpisem własnym. W przypadku scenariuszy produkcyjnych należy uzyskać certyfikat z urzędu certyfikacji.

Aby utworzyć certyfikat TLS/SSL z podpisem własnym, użyj narzędzia [OpenSSL](https://www.openssl.org/) dostępnego w Azure Cloud Shell i wielu dystrybucji systemu Linux lub użyj narzędzia do porównywania w systemie operacyjnym.

Najpierw Utwórz żądanie certyfikatu (plik CSR) w lokalnym katalogu roboczym:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Postępuj zgodnie z monitami, aby dodać informacje identyfikacyjne. W polu Nazwa pospolita wprowadź nazwę hosta skojarzoną z certyfikatem. Po wyświetleniu monitu o hasło naciśnij klawisz ENTER bez wpisywania, aby pominąć Dodawanie hasła.

Uruchom następujące polecenie, aby utworzyć certyfikat z podpisem własnym (plik CRT) z żądania certyfikatu. Na przykład:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

Powinny teraz być widoczne trzy pliki w katalogu: żądanie certyfikatu ( `ssl.csr` ), klucz prywatny ( `ssl.key` ) i certyfikat z podpisem własnym ( `ssl.crt` ). Używasz `ssl.key` i `ssl.crt` w dalszych krokach.

## <a name="configure-nginx-to-use-tls"></a>Konfigurowanie Nginx do korzystania z protokołu TLS

### <a name="create-nginx-configuration-file"></a>Utwórz plik konfiguracji Nginx

W tej sekcji utworzysz plik konfiguracyjny Nginx do korzystania z protokołu TLS. Zacznij od skopiowania następującego tekstu do nowego pliku o nazwie `nginx.conf` . W Azure Cloud Shell można użyć Visual Studio Code, aby utworzyć plik w katalogu roboczym:

```console
code nginx.conf
```

`location`Upewnij się, że ustawiono `proxy_pass` prawidłowy port dla aplikacji. W tym przykładzie ustawimy port 80 dla `aci-helloworld` kontenera.

```console
# nginx Configuration File
# https://wiki.nginx.org/Configuration

# Run as a less privileged user for security reasons.
user nginx;

worker_processes auto;

events {
  worker_connections 1024;
}

pid        /var/run/nginx.pid;

http {

    #Redirect to https, using 307 instead of 301 to preserve post data

    server {
        listen [::]:443 ssl;
        listen 443 ssl;

        server_name localhost;

        # Protect against the BEAST attack by not using SSLv3 at all. If you need to support older browsers (IE6) you may need to add
        # SSLv3 to the list of protocols below.
        ssl_protocols              TLSv1.2;

        # Ciphers set to best allow protection from Beast, while providing forwarding secrecy, as defined by Mozilla - https://wiki.mozilla.org/Security/Server_Side_TLS#Nginx
        ssl_ciphers                ECDHE-RSA-AES128-GCM-SHA256:ECDHE-ECDSA-AES128-GCM-SHA256:ECDHE-RSA-AES256-GCM-SHA384:ECDHE-ECDSA-AES256-GCM-SHA384:DHE-RSA-AES128-GCM-SHA256:DHE-DSS-AES128-GCM-SHA256:kEDH+AESGCM:ECDHE-RSA-AES128-SHA256:ECDHE-ECDSA-AES128-SHA256:ECDHE-RSA-AES128-SHA:ECDHE-ECDSA-AES128-SHA:ECDHE-RSA-AES256-SHA384:ECDHE-ECDSA-AES256-SHA384:ECDHE-RSA-AES256-SHA:ECDHE-ECDSA-AES256-SHA:DHE-RSA-AES128-SHA256:DHE-RSA-AES128-SHA:DHE-DSS-AES128-SHA256:DHE-RSA-AES256-SHA256:DHE-DSS-AES256-SHA:DHE-RSA-AES256-SHA:AES128-GCM-SHA256:AES256-GCM-SHA384:ECDHE-RSA-RC4-SHA:ECDHE-ECDSA-RC4-SHA:AES128:AES256:RC4-SHA:HIGH:!aNULL:!eNULL:!EXPORT:!DES:!3DES:!MD5:!PSK;
        ssl_prefer_server_ciphers  on;

        # Optimize TLS/SSL by caching session parameters for 10 minutes. This cuts down on the number of expensive TLS/SSL handshakes.
        # The handshake is the most CPU-intensive operation, and by default it is re-negotiated on every new/parallel connection.
        # By enabling a cache (of type "shared between all Nginx workers"), we tell the client to re-use the already negotiated state.
        # Further optimization can be achieved by raising keepalive_timeout, but that shouldn't be done unless you serve primarily HTTPS.
        ssl_session_cache    shared:SSL:10m; # a 1mb cache can hold about 4000 sessions, so we can hold 40000 sessions
        ssl_session_timeout  24h;


        # Use a higher keepalive timeout to reduce the need for repeated handshakes
        keepalive_timeout 300; # up from 75 secs default

        # remember the certificate for a year and automatically connect to HTTPS
        add_header Strict-Transport-Security 'max-age=31536000; includeSubDomains';

        ssl_certificate      /etc/nginx/ssl.crt;
        ssl_certificate_key  /etc/nginx/ssl.key;

        location / {
            proxy_pass http://localhost:80; # TODO: replace port if app listens on port other than 80
            
            proxy_set_header Connection "";
            proxy_set_header Host $host;
            proxy_set_header X-Real-IP $remote_addr;
            proxy_set_header X-Forwarded-For $remote_addr;
        }
    }
}
```

### <a name="base64-encode-secrets-and-configuration-file"></a>Pliki tajne kodowania base64 i plik konfiguracji

Base64 — kodowanie pliku konfiguracji Nginx, certyfikatu TLS/SSL i klucza TLS. W następnej sekcji wprowadzasz zakodowaną zawartość w pliku YAML używanym do wdrażania grupy kontenerów.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Wdróż grupę kontenerów

Teraz Wdróż grupę kontenerów, określając konfiguracje kontenerów w [pliku YAML](container-instances-multi-container-yaml.md).

### <a name="create-yaml-file"></a>Utwórz plik YAML

Skopiuj następujący YAML do nowego pliku o nazwie `deploy-aci.yaml` . W Azure Cloud Shell można użyć Visual Studio Code, aby utworzyć plik w katalogu roboczym:

```console
code deploy-aci.yaml
```

Wprowadź zawartość plików zakodowanych algorytmem Base64, gdzie wskazano w `secret` . Na przykład `cat` każdy plik kodowany algorytmem Base64, aby wyświetlić jego zawartość. Podczas wdrażania te pliki są dodawane do [woluminu tajnego](container-instances-volume-secret.md) w grupie kontenerów. W tym przykładzie wolumin tajny jest instalowany w kontenerze Nginx.

```YAML
api-version: 2019-12-01
location: westus
name: app-with-ssl
properties:
  containers:
  - name: nginx-with-ssl
    properties:
      image: nginx
      ports:
      - port: 443
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
      volumeMounts:
      - name: nginx-config
        mountPath: /etc/nginx
  - name: my-app
    properties:
      image: mcr.microsoft.com/azuredocs/aci-helloworld
      ports:
      - port: 80
        protocol: TCP
      resources:
        requests:
          cpu: 1.0
          memoryInGB: 1.5
  volumes:
  - secret:
      ssl.crt: <Enter contents of base64-ssl.crt here>
      ssl.key: <Enter contents of base64-ssl.key here>
      nginx.conf: <Enter contents of base64-nginx.conf here>
    name: nginx-config
  ipAddress:
    ports:
    - port: 443
      protocol: TCP
    type: Public
  osType: Linux
tags: null
type: Microsoft.ContainerInstance/containerGroups
```

### <a name="deploy-the-container-group"></a>Wdróż grupę kontenerów

Utwórz grupę zasobów za pomocą polecenia [AZ Group Create](/cli/azure/group#az-group-create) :

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Wdróż grupę kontenerów za pomocą polecenia [AZ Container Create](/cli/azure/container#az-container-create) , przekazując plik YAML jako argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Wyświetl stan wdrożenia

Aby wyświetlić stan wdrożenia, użyj następującego polecenia [AZ Container show](/cli/azure/container#az-container-show) :

```azurecli
az container show --resource-group <myResourceGroup> --name app-with-ssl --output table
```

W przypadku pomyślnego wdrożenia dane wyjściowe są podobne do następujących:

```console
Name          ResourceGroup    Status    Image                                                    IP:ports             Network    CPU/Memory       OsType    Location
------------  ---------------  --------  -------------------------------------------------------  -------------------  ---------  ---------------  --------  ----------
app-with-ssl  myresourcegroup  Running   nginx, mcr.microsoft.com/azuredocs/aci-helloworld        52.157.22.76:443     Public     1.0 core/1.5 gb  Linux     westus
```

## <a name="verify-tls-connection"></a>Weryfikowanie połączenia TLS

Użyj przeglądarki, aby przejść do publicznego adresu IP grupy kontenerów. Adres IP przedstawiony w tym przykładzie to `52.157.22.76` , więc adres URL to **https://52.157.22.76** . Aby wyświetlić uruchomioną aplikację, należy użyć protokołu HTTPS z powodu konfiguracji serwera Nginx. Próba nawiązania połączenia za pośrednictwem protokołu HTTP kończy się niepowodzeniem.

![Zrzut ekranu przedstawiający aplikację uruchomioną w wystąpieniu kontenera platformy Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Ponieważ w tym przykładzie używa certyfikatu z podpisem własnym, a nie z urzędu certyfikacji, przeglądarka wyświetla ostrzeżenie o zabezpieczeniach podczas nawiązywania połączenia z witryną za pośrednictwem protokołu HTTPS. Może być konieczne zaakceptowanie ostrzeżenia lub dostosowanie ustawień przeglądarki lub certyfikatu, aby przejoć do strony. Takie zachowanie jest oczekiwane.

>

## <a name="next-steps"></a>Następne kroki

W tym artykule pokazano, jak skonfigurować kontener Nginx, aby umożliwić nawiązywanie połączeń TLS z aplikacją sieci Web działającą w grupie kontenerów. Możesz dostosować ten przykład dla aplikacji, które nasłuchują na portach innych niż port 80. Można również zaktualizować plik konfiguracji Nginx w celu automatycznego przekierowania połączeń serwera na porcie 80 (HTTP), aby używać protokołu HTTPS.

W tym artykule jest używany Nginx w przyczepie, można użyć innego dostawcy TLS, takiego jak [Caddy](https://caddyserver.com/).

Jeśli grupa kontenerów jest wdrażana w [sieci wirtualnej platformy Azure](container-instances-vnet.md), można rozważyć inne opcje, aby włączyć punkt końcowy protokołu TLS dla wystąpienia kontenera zaplecza, w tym:

* [serwery proxy usługi Azure Functions](../azure-functions/functions-proxies.md)
* [Usługa Azure API Management](../api-management/api-management-key-concepts.md)
* [Application Gateway platformy Azure](../application-gateway/overview.md) — Zobacz przykładowy [szablon wdrożenia](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet).
