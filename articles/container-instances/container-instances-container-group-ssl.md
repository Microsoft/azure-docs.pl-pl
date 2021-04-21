---
title: Włączanie obsługi TLS z kontenerem sidecar
description: Tworzenie punktu końcowego protokołu SSL lub TLS dla grupy kontenerów uruchomionej w Azure Container Instances przez uruchomienie serwera Nginx w kontenerze sidecar
ms.topic: article
ms.date: 07/02/2020
ms.openlocfilehash: 906a1f239d7050ea17fd7d1425138049ebf045c1
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107790981"
---
# <a name="enable-a-tls-endpoint-in-a-sidecar-container"></a>Włączanie punktu końcowego TLS w kontenerze sidecar

W tym artykule pokazano, jak utworzyć [grupę kontenerów](container-instances-container-groups.md) z kontenerem aplikacji i kontenerem sidecar z uruchomionym dostawcą protokołu TLS/SSL. Skonfigurowanie grupy kontenerów przy użyciu oddzielnego punktu końcowego TLS umożliwia włączanie połączeń TLS dla aplikacji bez konieczności zmieniania kodu aplikacji.

Możesz skonfigurować przykładową grupę kontenerów składającą się z dwóch kontenerów:
* Kontener aplikacji, który uruchamia prostą aplikację internetową przy użyciu publicznego obrazu [aci-helloworld firmy](https://hub.docker.com/_/microsoft-azuredocs-aci-helloworld) Microsoft. 
* Kontener kontenera bocznego z publicznym obrazem [Nginx](https://hub.docker.com/_/nginx) skonfigurowanym do używania TLS. 

W tym przykładzie grupa kontenerów uwidacznia tylko port 443 dla serwera Nginx ze swoim publicznym adresem IP. Serwer Nginx kieruje żądania HTTPS do towarzyszącej aplikacji internetowej, która nasłuchuje wewnętrznie na porcie 80. Przykład można dostosować do aplikacji kontenera, które nasłuchują na innych portach. 

Zobacz [Następne kroki,](#next-steps) aby uzyskać informacje o innych podejściach do włączania TLS w grupie kontenerów.

[!INCLUDE [azure-cli-prepare-your-environment.md](../../includes/azure-cli-prepare-your-environment.md)]

- Ten artykuł wymaga interfejsu wiersza polecenia platformy Azure w wersji 2.0.55 lub nowszej. Jeśli używasz Azure Cloud Shell, najnowsza wersja jest już zainstalowana.

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym

Aby skonfigurować usługę Nginx jako dostawcę protokołu TLS, potrzebny jest certyfikat TLS/SSL. W tym artykule pokazano, jak utworzyć i skonfigurować certyfikat TLS/SSL z podpisem własnym. W przypadku scenariuszy produkcyjnych należy uzyskać certyfikat od urzędu certyfikacji.

Aby utworzyć certyfikat TLS/SSL z podpisem własnym, użyj narzędzia [OpenSSL](https://www.openssl.org/) dostępnego w programie Azure Cloud Shell i wielu dystrybucjach systemu Linux lub użyj porównywalnego narzędzia klienckiego w systemie operacyjnym.

Najpierw utwórz żądanie certyfikatu (plik CSR) w lokalnym katalogu pracy:

```console
openssl req -new -newkey rsa:2048 -nodes -keyout ssl.key -out ssl.csr
```

Postępuj zgodnie z monitami, aby dodać informacje identyfikacyjne. W polach Nazwa pospolita wprowadź nazwę hosta skojarzoną z certyfikatem. Po wyświetleniu monitu o hasło naciśnij klawisz Enter bez wpisywania, aby pominąć dodawanie hasła.

Uruchom następujące polecenie, aby utworzyć certyfikat z podpisem własnym (plik crt) z żądania certyfikatu. Na przykład:

```console
openssl x509 -req -days 365 -in ssl.csr -signkey ssl.key -out ssl.crt
```

W katalogu powinny być teraz dostępne trzy pliki: żądanie certyfikatu ( ), klucz prywatny ( ) i certyfikat z `ssl.csr` `ssl.key` podpisem własnym ( `ssl.crt` ). `ssl.key`Użyjemy plików i w `ssl.crt` kolejnych krokach.

## <a name="configure-nginx-to-use-tls"></a>Konfigurowanie serwera Nginx do korzystania z TLS

### <a name="create-nginx-configuration-file"></a>Tworzenie pliku konfiguracji serwera Nginx

W tej sekcji utworzysz plik konfiguracji dla serwera Nginx w celu korzystania z zabezpieczeń TLS. Zacznij od skopiowania poniższego tekstu do nowego pliku o nazwie `nginx.conf` . W Azure Cloud Shell można użyć Visual Studio Code, aby utworzyć plik w katalogu roboczy:

```console
code nginx.conf
```

W `location` programie upewnij się, że `proxy_pass` ustawiono odpowiedni port dla aplikacji. W tym przykładzie ustawiono port 80 dla `aci-helloworld` kontenera.

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

### <a name="base64-encode-secrets-and-configuration-file"></a>Zakodowane w formacie Base64 wpisy tajne i plik konfiguracji

Zakoduj plik konfiguracji serwera Nginx, certyfikat TLS/SSL i klucz TLS w formacie Base64. W następnej sekcji wprowadź zakodowaną zawartość w pliku YAML używanym do wdrożenia grupy kontenerów.

```console
cat nginx.conf | base64 > base64-nginx.conf
cat ssl.crt | base64 > base64-ssl.crt
cat ssl.key | base64 > base64-ssl.key
```

## <a name="deploy-container-group"></a>Wdrażanie grupy kontenerów

Teraz wdróż grupę kontenerów, określając konfiguracje kontenerów w pliku [YAML.](container-instances-multi-container-yaml.md)

### <a name="create-yaml-file"></a>Tworzenie pliku YAML

Skopiuj następujący kod YAML do nowego pliku o nazwie `deploy-aci.yaml` . W Azure Cloud Shell można użyć Visual Studio Code, aby utworzyć plik w katalogu roboczy:

```console
code deploy-aci.yaml
```

Wprowadź zawartość plików zakodowanych w formacie base64 w miejscu wskazanym w obszarze `secret` . Na przykład każdy plik zakodowany w `cat` formacie base64, aby wyświetlić jego zawartość. Podczas wdrażania te pliki są dodawane do [woluminu tajnego](container-instances-volume-secret.md) w grupie kontenerów. W tym przykładzie wolumin tajny jest zainstalowany w kontenerze Nginx.

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

### <a name="deploy-the-container-group"></a>Wdrażanie grupy kontenerów

Utwórz grupę zasobów za pomocą [polecenia az group create:](/cli/azure/group#az_group_create)

```azurecli-interactive
az group create --name myResourceGroup --location westus
```

Wdróż grupę kontenerów za pomocą polecenia [az container create,](/cli/azure/container#az_container_create) przekazując plik YAML jako argument.

```azurecli
az container create --resource-group <myResourceGroup> --file deploy-aci.yaml
```

### <a name="view-deployment-state"></a>Wyświetlanie stanu wdrożenia

Aby wyświetlić stan wdrożenia, użyj następującego [polecenia az container show:](/cli/azure/container#az_container_show)

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

Użyj przeglądarki, aby przejść do publicznego adresu IP grupy kontenerów. Adres IP pokazany w tym przykładzie to `52.157.22.76` , więc adres URL to **https://52.157.22.76** . Aby wyświetlić uruchamianą aplikację, należy użyć protokołu HTTPS ze względu na konfigurację serwera Nginx. Próby nawiązania połączenia za pośrednictwem protokołu HTTP nie powiodą się.

![Zrzut ekranu przedstawiający aplikację uruchomioną w wystąpieniu kontenera platformy Azure](./media/container-instances-container-group-ssl/aci-app-ssl-browser.png)

> [!NOTE]
> Ponieważ w tym przykładzie użyto certyfikatu z podpisem własnym, a nie certyfikatu z urzędu certyfikacji, podczas nawiązywania połączenia z witryną za pośrednictwem protokołu HTTPS w przeglądarce zostanie wyświetlone ostrzeżenie o zabezpieczeniach. Może być konieczne zaakceptowanie ostrzeżenia lub dostosowanie ustawień przeglądarki lub certyfikatu, aby przejść do strony. Takie zachowanie jest oczekiwane.

>

## <a name="next-steps"></a>Następne kroki

W tym artykule popisano, jak skonfigurować kontener Nginx w celu włączenia połączeń TLS z aplikacją internetową uruchamianą w grupie kontenerów. Ten przykład można dostosować do aplikacji, które nasłuchują na portach innych niż port 80. Można również zaktualizować plik konfiguracji serwera Nginx w celu automatycznego przekierowywania połączeń serwera na porcie 80 (HTTP) w celu używania protokołu HTTPS.

Chociaż w tym artykule użyto usługi Nginx w sidecar, możesz użyć innego dostawcy TLS, takiego [jak Caddy.](https://caddyserver.com/)

Jeśli wdrożysz grupę kontenerów w sieci wirtualnej platformy [Azure,](container-instances-vnet.md)możesz rozważyć inne opcje włączenia punktu końcowego TLS dla wystąpienia kontenera zaplecza, w tym:

* [serwery proxy usługi Azure Functions](../azure-functions/functions-proxies.md)
* [Usługa Azure API Management](../api-management/api-management-key-concepts.md)
* [Azure Application Gateway](../application-gateway/overview.md) — zobacz przykładowy [szablon wdrożenia.](https://github.com/Azure/azure-quickstart-templates/tree/master/201-aci-wordpress-vnet)
