---
title: Konfigurowanie kontenera niestandardowego
description: Dowiedz się, jak skonfigurować kontener niestandardowy w Azure App Service. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
ms.topic: article
ms.date: 02/23/2021
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 7bfebe318d93a544c964d70ea0a28144a7f0e43b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107764247"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Konfigurowanie niestandardowego kontenera dla usługi Azure App Service

W tym artykule pokazano, jak skonfigurować kontener niestandardowy do uruchamiania na Azure App Service.

::: zone pivot="container-windows"

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dotyczące konteneryzacji aplikacji systemu Windows w App Service. Jeśli nigdy wcześniej nie używaliśmy Azure App Service, najpierw wykonaj kroki samouczka i [przewodnika](quickstart-custom-container.md) Szybki start [dla](tutorial-custom-container.md) kontenerów niestandardowych.

::: zone-end

::: zone pivot="container-linux"

Ten przewodnik zawiera kluczowe pojęcia i instrukcje dotyczące konteneryzacji aplikacji systemu Linux w App Service. Jeśli nigdy wcześniej nie używaliśmy Azure App Service, najpierw wykonaj kroki samouczka i [przewodnika](quickstart-custom-container.md) Szybki start [dla](tutorial-custom-container.md) kontenerów niestandardowych. Istnieje również przewodnik Szybki start [i samouczek dotyczący](quickstart-multi-container.md) aplikacji z wieloma [kontenerami.](tutorial-multi-container-app.md)

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Obsługiwane obrazy nadrzędne

W przypadku niestandardowego obrazu systemu Windows należy wybrać odpowiedni obraz [nadrzędny (obraz podstawowy)](https://docs.docker.com/develop/develop-images/baseimages/) dla odpowiedniej struktury:

- Aby wdrożyć .NET Framework aplikacji, użyj obrazu nadrzędnego opartego na wersji systemu Windows Server Core [Long-Term Servicing Channel (LTSC).](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) 
- Aby wdrożyć aplikacje .NET Core, użyj obrazu nadrzędnego opartego na wersji Windows Server Nano [Semi-Annual Servicing Channel (SAC).](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) 

Pobieranie obrazu nadrzędnego podczas uruchamiania aplikacji może zająć trochę czasu. Można jednak skrócić czas uruchamiania, korzystając z jednego z następujących obrazów nadrzędnych, które już zostały zbuforowane w usłudze Azure App Service:

- [](https://hub.docker.com/_/microsoft-windows-servercore)mcr.microsoft.com/windows/servercore:2004
- [mcr.microsoft.com/windows/servercore:](https://hub.docker.com/_/microsoft-windows-servercore)ltsc2019
- [](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/)mcr.microsoft.com/dotnet/framework/aspnet:4.8-windowsservercore-2004
- [](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/)mcr.microsoft.com/dotnet/framework/aspnet:4.8-windowsservercore-ltsc2019
- [](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)mcr.microsoft.com/dotnet/core/runtime:3.1-nanoserver-2004
- [](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)mcr.microsoft.com/dotnet/core/runtime:3.1-nanoserver-1909
- [](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)mcr.microsoft.com/dotnet/core/runtime:3.1-nanoserver-1903
- [](https://hub.docker.com/_/microsoft-dotnet-core-runtime/)mcr.microsoft.com/dotnet/core/runtime:3.1-nanoserver-1809
- [](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)mcr.microsoft.com/dotnet/core/aspnet:3.1-nanoserver-2004
- [](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)mcr.microsoft.com/dotnet/core/aspnet:3.1-nanoserver-1909
- [](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)mcr.microsoft.com/dotnet/core/aspnet:3.1-nanoserver-1903
- [](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/)mcr.microsoft.com/dotnet/core/aspnet:3.1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Zmienianie obrazu platformy Docker kontenera niestandardowego

Aby zmienić istniejącą niestandardową aplikację kontenera z bieżącego obrazu platformy Docker na nowy obraz, użyj następującego polecenia:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Używanie obrazu z rejestru prywatnego

Aby użyć obrazu z rejestru prywatnego, takiego jak Azure Container Registry, uruchom następujące polecenie:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

W *\<username>* przypadku i należy podać poświadczenia logowania dla *\<password>* prywatnego konta rejestru.

## <a name="i-dont-see-the-updated-container"></a>Nie widzę zaktualizowanego kontenera

Jeśli zmienisz ustawienia kontenera platformy Docker tak, aby wskazać nowy kontener, może miej kilka minut, zanim aplikacja obsługuje żądania HTTP z nowego kontenera. Podczas ściągania i rozpoczynania pracy nowego kontenera App Service nadal obsługiwać żądania ze starego kontenera. Dopiero po uruchomieniu i przygotowaniu nowego kontenera do odbierania żądań App Service rozpocząć wysyłanie do niego żądań.

## <a name="how-container-images-are-stored"></a>Jak są przechowywane obrazy kontenerów

Przy pierwszym uruchomieniu niestandardowego obrazu platformy Docker w programie App Service App Service i ściąga `docker pull` wszystkie warstwy obrazu. Te warstwy są przechowywane na dysku, tak jak w przypadku korzystania z lokalnej platformy Docker. Przy każdym ponownym uruchomieniu aplikacji App Service obiekt , ale ściąga tylko te warstwy, `docker pull` które uległy zmianie. Jeśli nie zostały wprowadzone żadne zmiany, App Service istniejące warstwy na dysku lokalnym.

Jeśli z jakiegokolwiek powodu aplikacja zmienia wystąpienia obliczeniowe, takie jak skalowanie w górę i w dół warstw cenowych, App Service ponownie ściągnąć wszystkie warstwy. To samo dotyczy skalowania w celu dodania kolejnych wystąpień. Istnieją również rzadkie przypadki, w których wystąpienia aplikacji mogą ulec zmianie bez operacji skalowania.

## <a name="configure-port-number"></a>Konfigurowanie numeru portu

Domyślnie program App Service, że kontener niestandardowy nasłuchuje na porcie 80. Jeśli kontener nasłuchuje na innym porcie, ustaw ustawienie aplikacji `WEBSITES_PORT` w App Service aplikacji. Można go ustawić za pomocą [Cloud Shell](https://shell.azure.com). W powłoce Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service obecnie umożliwia kontenerowi uwidocznić tylko jeden port dla żądań HTTP. 

## <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Kontener niestandardowy może używać zmiennych środowiskowych, które muszą być dostarczane zewnętrznie. Możesz je przekazać za pośrednictwem [Cloud Shell](https://shell.azure.com). W powłoce Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Gdy aplikacja jest uruchamiana, App Service są automatycznie wstrzykiwane do procesu jako zmienne środowiskowe. Zmienne środowiskowe kontenera można zweryfikować przy użyciu adresu URL `https://<app-name>.scm.azurewebsites.net/Env)` .

Jeśli aplikacja używa obrazów z rejestru prywatnego lub z Docker Hub, poświadczenia do uzyskiwania dostępu do repozytorium są zapisywane w zmiennych środowiskowych: `DOCKER_REGISTRY_SERVER_URL` , `DOCKER_REGISTRY_SERVER_USERNAME` i `DOCKER_REGISTRY_SERVER_PASSWORD` . Ze względu na zagrożenia bezpieczeństwa żadna z tych zastrzeżonych nazw zmiennych nie jest narażona na działanie aplikacji.

::: zone pivot="container-windows"
W przypadku kontenerów opartych na usługach IIS lub .NET Framework (4.0 lub więcej) są one automatycznie wstrzykiwane jako ustawienia aplikacji .NET i parametry połączenia przez `System.ConfigurationManager` App Service. W przypadku wszystkich innych języków lub platform są one udostępniane jako zmienne środowiskowe dla procesu z jednym z następujących odpowiednich prefiksów:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Ta metoda działa zarówno w przypadku aplikacji z jednym kontenerem, jak i aplikacji z wieloma kontenerami, gdzie zmienne środowiskowe są określone w *pliku docker-compose.yml.*

::: zone-end

## <a name="use-persistent-shared-storage"></a>Używanie magazynu udostępnionego trwałego

::: zone pivot="container-windows"

Możesz użyć katalogu *C:\home* w systemie plików aplikacji, aby utrwalić pliki po ponownym uruchomieniu i udostępnić je między wystąpieniami. Plik `C:\home` w aplikacji jest dostarczany, aby umożliwić aplikacji kontenera dostęp do magazynu trwałego.

Gdy magazyn trwały jest wyłączony, zapisu w `C:\home` katalogu nie są utrwalane. [Dzienniki hosta platformy Docker i dzienniki kontenerów](#access-diagnostic-logs) są zapisywane w domyślnym trwałym magazynie udostępnionym, który nie jest dołączony do kontenera. Po włączeniu magazynu trwałego wszystkie zapisu w katalogu są utrwalane i dostępne dla wszystkich wystąpień aplikacji skalowanej w poziomie, a dziennik jest `C:\home` dostępny pod adres . `C:\home\LogFiles`

::: zone-end

::: zone pivot="container-linux"

Możesz użyć katalogu */home* w systemie plików aplikacji, aby utrwalić pliki po ponownym uruchomieniu i udostępnić je między wystąpieniami. Plik w aplikacji jest dostarczany, aby umożliwić aplikacji kontenera dostęp `/home` do magazynu trwałego.

Po wyłączeniu magazynu trwałego zapis w katalogu nie jest utrwalany po ponownym uruchomieniu aplikacji `/home` ani w wielu wystąpieniach. Jedynym wyjątkiem jest `/home/LogFiles` katalog, który jest używany do przechowywania dzienników platformy Docker i kontenera. Po włączeniu magazynu trwałego wszystkie zapis w katalogu są utrwalane i dostępne dla wszystkich wystąpień aplikacji `/home` skalowanej w zewnątrz.

::: zone-end

Domyślnie magazyn trwały jest wyłączony, a ustawienie nie jest widoczne w ustawieniach aplikacji. Aby ją włączyć, ustaw `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienie aplikacji za pomocą Cloud Shell . [](https://shell.azure.com) W powłoce Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> Możesz również skonfigurować [własny magazyn trwały](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

App Service kończy działanie protokołu TLS/SSL na frontonach. Oznacza to, że żądania TLS/SSL nigdy nie dostają się do aplikacji. Nie jest to konieczne i nie powinno się implementować w aplikacji żadnej obsługi protokołów TLS/SSL. 

Frontonie znajdują się wewnątrz centrów danych platformy Azure. Jeśli używasz protokołu TLS/SSL z aplikacją, ruch przez Internet będzie zawsze bezpiecznie szyfrowany.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Dostosowywanie ASP.NET iniekcji klucza komputera

 Podczas uruchamiania kontenera automatycznie generowane klucze są wstrzykiwane do kontenera jako klucze komputera dla ASP.NET procedur kryptograficznych. Te klucze [można znaleźć w kontenerze,](#connect-to-the-container) szukając następujących zmiennych środowiskowych: `MACHINEKEY_Decryption` , , , `MACHINEKEY_DecryptionKey` `MACHINEKEY_ValidationKey` `MACHINEKEY_Validation` . 

Nowe klucze przy każdym ponownym uruchomieniu mogą ASP.NET uwierzytelniania formularzy i wyświetlania stanu, jeśli aplikacja jest od nich zależna. Aby zapobiec automatycznemu regenerację kluczy, ustaw je ręcznie [jako App Service ustawień aplikacji.](#configure-environment-variables) 

## <a name="connect-to-the-container"></a>Nawiązywanie połączenia z kontenerem

Możesz połączyć się z kontenerem systemu Windows bezpośrednio w celu wykonywania zadań diagnostycznych, przechodząc do witryny `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Oto jak to działa:

- Konsola debugowania umożliwia wykonywanie interaktywnych poleceń, takich jak uruchamianie sesji programu PowerShell, sprawdzanie kluczy rejestru i nawigowanie po całym systemie plików kontenera.
- Działa ona niezależnie od przeglądarki graficznej powyżej, która wyświetla tylko pliki w [udostępnionym magazynie](#use-persistent-shared-storage).
- W aplikacji skalowanej w zewnątrz konsola debugowania jest połączona z jednym z wystąpień kontenera. Z listy rozwijanej Wystąpienie  w górnym menu możesz wybrać inne wystąpienie.
- Wszelkie zmiany wprowadzone w kontenerze z  poziomu konsoli nie są utrwalane po ponownym uruchomieniu aplikacji (z wyjątkiem zmian w magazynie udostępnionym), ponieważ nie są częścią obrazu platformy Docker. Aby utrwalić zmiany, takie jak ustawienia rejestru i instalacja oprogramowania, należy je dodać do pliku Dockerfile.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

App Service rejestruje akcje hosta platformy Docker, a także działania z kontenera. Dzienniki z hosta platformy Docker (dzienniki platformy) są dostarczane domyślnie, ale dzienniki aplikacji lub dzienniki serwera internetowego z poziomu kontenera muszą być włączone ręcznie. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania aplikacji i](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) Włączanie [rejestrowania serwera internetowego.](troubleshoot-diagnostic-logs.md#enable-web-server-logging) 

Istnieje kilka sposobów uzyskiwania dostępu do dzienników platformy Docker:

- [W Azure Portal](#in-azure-portal)
- [Z konsoli Kudu](#from-the-kudu-console)
- [Za pomocą interfejsu API Kudu](#with-the-kudu-api)
- [Wysyłanie dzienników do usługi Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>W Azure Portal

Dzienniki platformy Docker są wyświetlane w portalu na stronie **Ustawienia kontenera** aplikacji. Dzienniki są obcinane, ale możesz pobrać wszystkie dzienniki, klikając **pozycję Pobierz.** 

### <a name="from-the-kudu-console"></a>Z konsoli Kudu

Przejdź do `https://<app-name>.scm.azurewebsites.net/DebugConsole` folderu **LogFiles** i kliknij go, aby wyświetlić poszczególne pliki dziennika. Aby pobrać cały katalog **LogFiles,** kliknij **ikonę** Pobierz z lewej strony nazwy katalogu. Dostęp do tego folderu można również uzyskać za pomocą klienta FTP.

W terminalu konsoli nie można domyślnie uzyskać dostępu do folderu, ponieważ trwały magazyn `C:\home\LogFiles` udostępniony nie jest włączony. Aby włączyć to zachowanie w terminalu konsoli, [włącz trwały magazyn udostępniony](#use-persistent-shared-storage).

Jeśli spróbujesz pobrać dziennik platformy Docker, który jest obecnie w użyciu przy użyciu klienta FTP, może wystąpić błąd z powodu blokady pliku.

### <a name="with-the-kudu-api"></a>Za pomocą interfejsu API Kudu

Przejdź bezpośrednio do `https://<app-name>.scm.azurewebsites.net/api/logs/docker` elementu , aby wyświetlić metadane dzienników platformy Docker. Na liście może być wyświetlony więcej niż jeden plik dziennika, a właściwość `href` umożliwia bezpośrednie pobranie pliku dziennika. 

Aby pobrać wszystkie dzienniki razem w jednym pliku ZIP, uzyskaj dostęp do pliku `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Dostosowywanie pamięci kontenera

Domyślnie wszystkie kontenery systemu Windows wdrożone w Azure App Service są ograniczone do 1 GB pamięci RAM. Tę wartość można zmienić, podając `WEBSITE_MEMORY_LIMIT_MB` ustawienie aplikacji za pośrednictwem [Cloud Shell](https://shell.azure.com). W powłoce Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Wartość jest zdefiniowana w MB i musi być mniejsza i równa całkowitej pamięci fizycznej hosta. Na przykład w planie App Service 8 GB pamięci RAM łączna suma dla wszystkich aplikacji nie może `WEBSITE_MEMORY_LIMIT_MB` przekraczać 8 GB. Informacje na temat ilości dostępnej pamięci dla poszczególnych warstw cenowych można znaleźć App Service cenowych [w](https://azure.microsoft.com/pricing/details/app-service/windows/)sekcji Plan kontenera **Premium (Windows).**

## <a name="customize-the-number-of-compute-cores"></a>Dostosowywanie liczby rdzeni obliczeniowych

Domyślnie kontener systemu Windows jest uruchamiany ze wszystkimi rdzeniami dostępnymi dla wybranej warstwy cenowej. Możesz na przykład zmniejszyć liczbę rdzeni, z których korzysta miejsce przejściowe. Aby zmniejszyć liczbę rdzeni używanych przez kontener, ustaw ustawienie aplikacji na `WEBSITE_CPU_CORES_LIMIT` preferowaną liczbę rdzeni. Można go ustawić za pomocą [Cloud Shell](https://shell.azure.com). W powłoce Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Zaktualizowanie ustawienia aplikacji wyzwala automatyczne ponowne uruchomienie, co powoduje minimalny przestój. W przypadku aplikacji produkcyjnej rozważ zamianę jej na miejsce przejściowe, zmianę ustawienia aplikacji w miejscu przejściowym, a następnie zamianę jej z powrotem w środowisku produkcyjnym.

Sprawdź dostosowaną liczbę, przechodząc do konsoli Kudu ( ) i wpisując następujące polecenia przy `https://<app-name>.scm.azurewebsites.net` użyciu programu PowerShell. Każde polecenie wyprowadza liczbę.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

Procesory mogą być procesorami wielordzeniowym lub wielowątkowych. Informacje o tym, ile rdzeni jest dostępnych dla każdej warstwy cenowej, można znaleźć w te App Service [,](https://azure.microsoft.com/pricing/details/app-service/windows/)w sekcji **Premium Container (Windows) Plan (Plan kontenera Premium).**

## <a name="customize-health-ping-behavior"></a>Dostosowywanie zachowania polecenia ping kondycji

App Service, że kontener został pomyślnie uruchomiony, gdy kontener zostanie uruchomiony i odpowiada na polecenie ping protokołu HTTP. Żądanie ping kondycji zawiera nagłówek `User-Agent= "App Service Hyper-V Container Availability Check"` . Jeśli kontener zostanie uruchomiony, ale nie odpowie na polecenie ping po upływie określonego czasu, App Service rejestruje zdarzenie w dzienniku platformy Docker z informacją, że kontener nie został uruchomiony. 

Jeśli aplikacja intensywnie obciąża zasoby, kontener może nie odpowiadać na polecenie ping PROTOKOŁU HTTP w czasie. Aby kontrolować akcje w przypadku awarii żądań ping protokołu HTTP, ustaw `CONTAINER_AVAILABILITY_CHECK_MODE` ustawienie aplikacji. Można go ustawić za pomocą [Cloud Shell](https://shell.azure.com). W powłoce Bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

W poniższej tabeli przedstawiono możliwe wartości:

| Wartość | Opisy |
| - | - |
| **Naprawy** | Uruchom ponownie kontener po trzech kolejnych testach dostępności |
| **ReportOnly (Tylko raport)** | Wartość domyślna. Nie uruchamiaj ponownie kontenera, ale zgłoś go w dziennikach platformy Docker po trzech kolejnych testach dostępności. |
| **Wyłączone** | Nie sprawdzaj dostępności. |

## <a name="support-for-group-managed-service-accounts"></a>Obsługa kont usług zarządzanych przez grupę

Konta usług zarządzane przez grupę (gMSA) nie są obecnie obsługiwane w kontenerach systemu Windows w App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Włączanie SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby kontener niestandardowy obsługił SSH, należy dodać go do obrazu platformy Docker.

> [!TIP]
> Wszystkie wbudowane kontenery systemu Linux w App Service dodały instrukcje SSH w swoich repozytoriach obrazów. Poniższe instrukcje można znaleźć w repozytorium programu [Node.js 10.14,](https://github.com/Azure-App-Service/node/blob/master/10.14) aby zobaczyć, jak jest w nim włączone. Konfiguracja w Node.js wbudowanym obrazie jest nieco inna, ale w zasadzie taka sama.

- Dodaj [plik sshd_config do](https://man.openbsd.org/sshd_config) repozytorium, jak w poniższym przykładzie.

    ```
    Port            2222
    ListenAddress       0.0.0.0
    LoginGraceTime      180
    X11Forwarding       yes
    Ciphers aes128-cbc,3des-cbc,aes256-cbc,aes128-ctr,aes192-ctr,aes256-ctr
    MACs hmac-sha1,hmac-sha1-96
    StrictModes         yes
    SyslogFacility      DAEMON
    PasswordAuthentication  yes
    PermitEmptyPasswords    no
    PermitRootLogin     yes
    Subsystem sftp internal-sftp
    ```

    > [!NOTE]
    > Ten plik konfiguruje program OpenSSH i musi zawierać następujące elementy:
    > - `Port` Musi być ustawiona na 2222.
    > - Element `Ciphers` musi zawierać co najmniej jeden element z tej listy: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - Element `MACs` musi zawierać co najmniej jeden element z tej listy: `hmac-sha1,hmac-sha1-96`.

- W pliku Dockerfile dodaj następujące polecenia:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Port 2222 kontenera jest dostępny tylko w sieci mostka prywatnej sieci wirtualnej i nie jest dostępny dla osoby atakującej w Internecie.

- W skrypcie uruchamiania kontenera uruchom serwer SSH.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurowanie aplikacji z wieloma kontenerami

- [Używanie magazynu trwałego w Docker Compose](#use-persistent-storage-in-docker-compose)
- [Ograniczenia wersji zapoznawczej](#preview-limitations)
- [Docker Compose opcje](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Używanie magazynu trwałego w Docker Compose

Aplikacje z wieloma kontenerami, takie jak WordPress, potrzebują magazynu trwałego, aby działały prawidłowo. Aby ją włączyć, Docker Compose musi wskazać lokalizację magazynu *poza* kontenerem. Lokalizacje magazynu wewnątrz kontenera nie utrwalają zmian poza ponownym uruchomieniem aplikacji.

Włącz magazyn trwały, ustawiając ustawienie aplikacji za pomocą polecenia `WEBSITES_ENABLE_APP_SERVICE_STORAGE` [az webapp config appsettings set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set) w [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

W pliku *docker-compose.yml* zamapuj `volumes` opcję na `${WEBAPP_STORAGE_HOME}` . 

`WEBAPP_STORAGE_HOME` to zmienna środowiskowa w usłudze App Service mapowana na magazyn trwały aplikacji. Na przykład:

```yaml
wordpress:
  image: wordpress:latest
  volumes:
  - ${WEBAPP_STORAGE_HOME}/site/wwwroot:/var/www/html
  - ${WEBAPP_STORAGE_HOME}/phpmyadmin:/var/www/phpmyadmin
  - ${WEBAPP_STORAGE_HOME}/LogFiles:/var/log
```

### <a name="preview-limitations"></a>Ograniczenia wersji zapoznawczej

Wiele kontenerów jest obecnie w wersji zapoznawczej. Następujące funkcje App Service platformy nie są obsługiwane:

- Uwierzytelnianie/autoryzacja
- Tożsamości zarządzane
- CORS

### <a name="docker-compose-options"></a>Docker Compose opcje

Na poniższych listach przedstawiono obsługiwane i nieobsługiwane Docker Compose konfiguracji:

#### <a name="supported-options"></a>Obsługiwane opcje

- command
- entrypoint
- środowisko
- image (obraz)
- ports
- restart
- services
- volumes

#### <a name="unsupported-options"></a>Nieobsługiwane opcje

- build (niedozwolona)
- depends_on (ignorowana)
- networks (ignorowana)
- secrets (ignorowana)
- porty inne niż 80 i 8080 (ignorowane)

> [!NOTE]
> Wszystkie inne opcje, które nie są jawnie wywoływane, są ignorowane w publicznej wersji zapoznawczej.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: migrowanie niestandardowego oprogramowania Azure App Service użyciu kontenera niestandardowego](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Samouczek: aplikacja WordPress z wieloma kontenerami](tutorial-multi-container-app.md)

::: zone-end

Możesz też wyświetlić dodatkowe zasoby:

[Ładowanie certyfikatu w kontenerach systemu Windows/Linux](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
