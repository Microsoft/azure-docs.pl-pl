---
title: Konfigurowanie niestandardowego kontenera systemu Linux
description: Informacje dotyczące konfigurowania niestandardowego kontenera systemu Linux w Azure App Service. W tym artykule przedstawiono najczęstsze zadania konfiguracyjne.
ms.topic: article
ms.date: 03/28/2019
ms.openlocfilehash: 6baa1fbd4932aa83a54081ff166dcae7f258fff9
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "79280146"
---
# <a name="configure-a-custom-linux-container-for-azure-app-service"></a>Konfigurowanie niestandardowego kontenera systemu Linux dla Azure App Service

W tym artykule opisano sposób konfigurowania niestandardowego kontenera systemu Linux do uruchamiania na Azure App Service.

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dotyczące kontenerach aplikacji systemu Linux w App Service. Jeśli nie korzystasz z Azure App Service, postępuj zgodnie z instrukcjami w [sekcji szybki start](quickstart-docker-go.md) i [samouczek](tutorial-custom-docker-image.md) . Dostępna jest również funkcja [szybkiego startu](quickstart-multi-container.md) i [samouczka](tutorial-multi-container-app.md)aplikacji z obsługą kontenera.

## <a name="configure-port-number"></a>Konfiguruj numer portu

Serwer sieci Web w obrazie niestandardowym może używać portu innego niż 80. Poinformuj platformę Azure o porcie używanym przez kontener niestandardowy przy użyciu `WEBSITES_PORT` ustawienia aplikacji. Na stronie usługi GitHub dotyczącej [przykładowego kodu w języku Python w tym samouczku](https://github.com/Azure-Samples/docker-django-webapp-linux) przedstawiono, co jest potrzebne, aby ustawić opcję `WEBSITES_PORT` na wartość _8000_. Można to zrobić, uruchamiając [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenie w Cloud Shell. Przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

## <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Kontener niestandardowy może używać zmiennych środowiskowych, które muszą zostać dostarczone zewnętrznie. Można przekazać je za pomocą [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w Cloud Shell. Przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WORDPRESS_DB_HOST="myownserver.mysql.database.azure.com"
```

Ta metoda działa zarówno w przypadku aplikacji z jednym kontenerem, jak i aplikacji z obsługą kontenera, gdzie zmienne środowiskowe są określone w pliku *Docker-Compose. yml* .

## <a name="use-persistent-shared-storage"></a>Używanie trwałego magazynu udostępnionego

Możesz użyć katalogu */Home* w systemie plików aplikacji, aby utrwalać pliki przez ponowne uruchomienie i udostępnić je między wystąpieniami. Aby `/home` umożliwić aplikacji kontenera dostęp do magazynu trwałego, należy podać w Twojej aplikacji.

Gdy trwały magazyn jest wyłączony, operacje zapisu w `/home` katalogu nie są utrwalane między ponownymi uruchomieniami aplikacji ani między wieloma wystąpieniami. Jedynym wyjątkiem jest `/home/LogFiles` katalog, który jest używany do przechowywania dzienników platformy Docker i kontenerów. Gdy magazyn trwały jest włączony, wszystkie operacje zapisu `/home` w katalogu są utrwalane i mogą być dostępne we wszystkich wystąpieniach aplikacji skalowanej w poziomie.

Domyślnie magazyn trwały jest *włączony* , a ustawienie nie jest widoczne w ustawieniach aplikacji. Aby go wyłączyć, należy ustawić `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienie aplikacji przez uruchomienie [`az webapp config appsettings set`](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) polecenia w Cloud Shell. Przykład:

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=false
```

> [!NOTE]
> Możesz również [skonfigurować własny magazyn trwały](how-to-serve-content-from-azure-storage.md).

## <a name="enable-ssh"></a>Włączanie protokołu SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby kontener niestandardowy obsługiwał protokół SSH, należy dodać go do samego pliku dockerfile.

> [!TIP]
> Wszystkie wbudowane kontenery systemu Linux dodaliśmy instrukcje protokołu SSH w swoich repozytoriach obrazów. Aby zobaczyć, jak to jest włączone, możesz przejść przez następujące instrukcje dotyczące [repozytorium Node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) .

- Użyj instrukcji [Run](https://docs.docker.com/engine/reference/builder/#run) , aby zainstalować serwer SSH i ustawić hasło dla konta głównego `"Docker!"`. Na przykład dla obrazu opartego na systemie [Alpine Linux](https://hub.docker.com/_/alpine)wymagane są następujące polecenia:

    ```Dockerfile
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 
    ```

    Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Protokół SSH jest dostępny tylko `https://<app-name>.scm.azurewebsites.net` za pośrednictwem uwierzytelniania i uwierzytelniany przy użyciu poświadczeń publikowania.

- Dodaj [ten plik sshd_config](https://github.com/Azure-App-Service/node/blob/master/10.14/sshd_config) do repozytorium obrazów i użyj instrukcji [copy](https://docs.docker.com/engine/reference/builder/#copy) , aby skopiować plik do katalogu */etc/ssh/* . Aby uzyskać więcej informacji na temat plików *sshd_config* , zobacz [dokumentację OpenBSD](https://man.openbsd.org/sshd_config).

    ```Dockerfile
    COPY sshd_config /etc/ssh/
    ```

    > [!NOTE]
    > Plik *sshd_config* musi zawierać następujące elementy:
    > - Element `Ciphers` musi zawierać co najmniej jeden element z tej listy: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - Element `MACs` musi zawierać co najmniej jeden element z tej listy: `hmac-sha1,hmac-sha1-96`.

- Użyj instrukcji [uwidaczniania](https://docs.docker.com/engine/reference/builder/#expose) , aby otworzyć port 2222 w kontenerze. Mimo że hasło główne jest znane, port 2222 jest niedostępny z Internetu. Jest on dostępny tylko dla kontenerów w sieci mostkowej prywatnej sieci wirtualnej.

    ```Dockerfile
    EXPOSE 80 2222
    ```

- W skrypcie uruchamiania kontenera Uruchom serwer SSH.

    ```bash
    /usr/sbin/sshd
    ```

    Aby zapoznać się z przykładem, zobacz jak domyślny [kontener Node. js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14/startup/init_container.sh) uruchamia serwer SSH.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../../includes/app-service-web-logs-access-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurowanie aplikacji z obsługą kontenera

- [Korzystanie z magazynu trwałego w Docker Compose](#use-persistent-storage-in-docker-compose)
- [Ograniczenia wersji zapoznawczej](#preview-limitations)
- [Opcje Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Korzystanie z magazynu trwałego w Docker Compose

Aplikacje z obsługą kontenerów, takie jak WordPress, potrzebują trwałego magazynu w celu poprawnego działania. Aby ją włączyć, konfiguracja Docker Compose musi wskazywać lokalizację magazynu *poza* kontenerem. Lokalizacje magazynu wewnątrz kontenera nie utrwalają zmian poza ponownym uruchomieniem aplikacji.

Aby włączyć magazyn trwały, `WEBSITES_ENABLE_APP_SERVICE_STORAGE` należy ustawić ustawienie aplikacji przy użyciu polecenia [AZ webapp config appsettings Set](/cli/azure/webapp/config/appsettings?view=azure-cli-latest#az-webapp-config-appsettings-set) w Cloud Shell.

```azurecli-interactive
az webapp config appsettings set --resource-group <resource-group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

W pliku *Docker-Compose. yml* zamapuj `volumes` opcję na `${WEBAPP_STORAGE_HOME}`. 

`WEBAPP_STORAGE_HOME` to zmienna środowiskowa w usłudze App Service mapowana na magazyn trwały aplikacji. Przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Wiele kontenerów jest obecnie w wersji zapoznawczej. Następujące funkcje platformy App Service nie są obsługiwane:

- Uwierzytelnianie/autoryzacja
- Tożsamości zarządzane

### <a name="docker-compose-options"></a>Opcje Docker Compose

Na poniższych listach przedstawiono obsługiwane i nieobsługiwane Docker Compose opcje konfiguracji:

#### <a name="supported-options"></a>Obsługiwane opcje

- command
- entrypoint
- environment
- image
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Nieobsługiwane opcje

- build (niedozwolona)
- depends_on (ignorowana)
- networks (ignorowana)
- secrets (ignorowana)
- porty inne niż 80 i 8080 (zignorowano)

> [!NOTE]
> Wszystkie inne opcje, które nie są jawnie wywoływane, są ignorowane w publicznej wersji zapoznawczej.

## <a name="configure-vnet-integration"></a>Konfigurowanie integracji sieci wirtualnej

Używanie kontenera niestandardowego z integracją sieci wirtualnej może wymagać dodatkowej konfiguracji kontenera. Zobacz [Integrowanie aplikacji z usługą Azure Virtual Network](../web-sites-integrate-with-vnet.md).

[!INCLUDE [robots933456](../../../includes/app-service-web-configure-robots933456.md)]

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: wdrażanie z repozytorium kontenera prywatnego](tutorial-custom-docker-image.md)

> [!div class="nextstepaction"]
> [Samouczek: wielokontenerowa aplikacja WordPress](tutorial-multi-container-app.md)
