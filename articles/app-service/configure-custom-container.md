---
title: Konfigurowanie kontenera niestandardowego
description: Dowiedz się, jak skonfigurować kontener niestandardowy w Azure App Service. W tym artykule przedstawiono najczęściej wykonywane zadania konfiguracji.
ms.topic: article
ms.date: 02/23/2021
zone_pivot_groups: app-service-containers-windows-linux
ms.openlocfilehash: 8083c3c0c88d904ccb3ec75ae69a699867bd0f25
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101704875"
---
# <a name="configure-a-custom-container-for-azure-app-service"></a>Konfigurowanie niestandardowego kontenera dla usługi Azure App Service

W tym artykule opisano sposób konfigurowania niestandardowego kontenera do uruchamiania na Azure App Service.

::: zone pivot="container-windows"

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dotyczące kontenerach aplikacji systemu Windows w App Service. Jeśli nie korzystasz z Azure App Service, postępuj zgodnie z instrukcjami w [sekcji szybki start](quickstart-custom-container.md) i [samouczek](tutorial-custom-container.md) .

::: zone-end

::: zone pivot="container-linux"

Ten przewodnik zawiera najważniejsze pojęcia i instrukcje dotyczące kontenerach aplikacji systemu Linux w App Service. Jeśli nie korzystasz z Azure App Service, postępuj zgodnie z instrukcjami w [sekcji szybki start](quickstart-custom-container.md) i [samouczek](tutorial-custom-container.md) . Dostępna jest również funkcja [szybkiego startu](quickstart-multi-container.md) i [samouczka](tutorial-multi-container-app.md)aplikacji z obsługą kontenera.

::: zone-end

::: zone pivot="container-windows"

## <a name="supported-parent-images"></a>Obsługiwane obrazy nadrzędne

W przypadku niestandardowego obrazu systemu Windows należy wybrać odpowiedni [obraz nadrzędny (obraz podstawowy)](https://docs.docker.com/develop/develop-images/baseimages/) dla potrzebnej platformy:

- Aby wdrożyć aplikacje .NET Framework, Użyj obrazu nadrzędnego na podstawie wersji Windows Server Core długoterminowego [kanału obsługi (LTSC)](/windows-server/get-started-19/servicing-channels-19#long-term-servicing-channel-ltsc) . 
- Aby wdrażać aplikacje platformy .NET Core, Użyj obrazu nadrzędnego na podstawie wydania systemu Windows Server nano [półroczing Channel (SAC)](/windows-server/get-started-19/servicing-channels-19#semi-annual-channel) . 

Pobieranie obrazu nadrzędnego podczas uruchamiania aplikacji może zająć trochę czasu. Można jednak skrócić czas uruchamiania, korzystając z jednego z następujących obrazów nadrzędnych, które już zostały zbuforowane w usłudze Azure App Service:

- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): 2004
- [MCR.Microsoft.com/Windows/ServerCore](https://hub.docker.com/_/microsoft-windows-servercore): ltsc2019
- [MCR.Microsoft.com/dotnet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-2004
- [MCR.Microsoft.com/dotnet/Framework/ASPNET](https://hub.docker.com/_/microsoft-dotnet-framework-aspnet/): 4,8-windowsservercore-ltsc2019
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/dotnet/Core/Runtime](https://hub.docker.com/_/microsoft-dotnet-core-runtime/): 3,1-nanoserver-1809
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-2004
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1909
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1903
- [MCR.Microsoft.com/dotnet/Core/ASPNET](https://hub.docker.com/_/microsoft-dotnet-core-aspnet/): 3,1-nanoserver-1809

::: zone-end

## <a name="change-the-docker-image-of-a-custom-container"></a>Zmienianie obrazu platformy Docker kontenera niestandardowego

Aby zmienić istniejącą aplikację kontenera niestandardowego z bieżącego obrazu platformy Docker na nowy, użyj następującego polecenia:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <docker-hub-repo>/<image>
```

## <a name="use-an-image-from-a-private-registry"></a>Korzystanie z obrazu z rejestru prywatnego

Aby użyć obrazu z rejestru prywatnego, takiego jak Azure Container Registry, uruchom następujące polecenie:

```azurecli-interactive
az webapp config container set --name <app-name> --resource-group <group-name> --docker-custom-image-name <image-name> --docker-registry-server-url <private-repo-url> --docker-registry-server-user <username> --docker-registry-server-password <password>
```

W *\<username>* przypadku *\<password>* usługi i podaj poświadczenia logowania dla konta w rejestrze prywatnym.

## <a name="i-dont-see-the-updated-container"></a>Nie widzę zaktualizowanego kontenera

Jeśli zmienisz ustawienia kontenera Docker tak, aby wskazywały nowy kontener, może upłynąć kilka minut, zanim aplikacja będzie obsłużyć żądania HTTP z nowego kontenera. Podczas ściągania i uruchamiania nowego kontenera App Service nadal obsłużyć żądania ze starego kontenera. Tylko wtedy, gdy nowy kontener jest uruchomiony i jest gotowy do odbierania żądań, App Service rozpocząć wysyłanie do niego żądań.

## <a name="how-container-images-are-stored"></a>Jak są przechowywane obrazy kontenerów

Przy pierwszym uruchomieniu niestandardowego obrazu platformy Docker w App Service, App Service wykonuje `docker pull` i pobiera wszystkie warstwy obrazu. Te warstwy są przechowywane na dysku, na przykład w przypadku korzystania z platformy Docker w środowisku lokalnym. Za każdym razem, gdy aplikacja zostanie ponownie uruchomiona, App Service robi `docker pull` , ale tylko pobiera warstwy, które uległy zmianie. Jeśli nie wprowadzono żadnych zmian, App Service używa istniejących warstw na dysku lokalnym.

Jeśli aplikacja zmienia wystąpienia obliczeniowe z dowolnego powodu, takiego jak skalowanie w górę i w dół warstw cenowych, App Service musi ponownie ściągnąć wszystkie warstwy. To samo ma wartość true, jeśli skalowanie w poziomie jest możliwe, aby dodać kolejne wystąpienia. Istnieją również rzadkie przypadki, w których wystąpienia aplikacji mogą ulec zmianie bez operacji skalowania.

## <a name="configure-port-number"></a>Konfiguruj numer portu

Domyślnie App Service zakłada, że kontener niestandardowy nasłuchuje na porcie 80. Jeśli kontener nasłuchuje na innym porcie, ustaw `WEBSITES_PORT` ustawienie aplikacji w aplikacji App Service. Można ustawić go za pośrednictwem [Cloud Shell](https://shell.azure.com). W bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_PORT=8000
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_PORT"="8000"}
```

App Service obecnie umożliwia kontenerowi uwidocznienie tylko jednego portu dla żądań HTTP. 

## <a name="configure-environment-variables"></a>Konfigurowanie zmiennych środowiskowych

Kontener niestandardowy może używać zmiennych środowiskowych, które muszą zostać dostarczone zewnętrznie. Można przekazać je za pośrednictwem [Cloud Shell](https://shell.azure.com). W bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings DB_HOST="myownserver.mysql.database.azure.com"
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"DB_HOST"="myownserver.mysql.database.azure.com"}
```

Gdy aplikacja zostanie uruchomiona, ustawienia aplikacji App Service są wprowadzane do procesu jako zmienne środowiskowe automatycznie. Zmienne środowiskowe kontenera można zweryfikować przy użyciu adresu URL `https://<app-name>.scm.azurewebsites.net/Env)` .

::: zone pivot="container-windows"
W przypadku kontenerów opartych na usługach IIS lub .NET Framework (4,0 lub nowsze) są one wstawiane do `System.ConfigurationManager` programu jako ustawienia aplikacji .NET i parametry połączenia automatycznie przez App Service. Dla wszystkich innych języków i struktur są one dostarczane jako zmienne środowiskowe dla procesu, z jednym z następujących prefiksów:

- `APPSETTING_`
- `SQLCONTR_`
- `MYSQLCONTR_`
- `SQLAZURECOSTR_`
- `POSTGRESQLCONTR_`
- `CUSTOMCONNSTR_`

::: zone-end

::: zone pivot="container-linux"

Ta metoda działa zarówno w przypadku aplikacji z jednym kontenerem, jak i aplikacji z obsługą kontenera, gdzie zmienne środowiskowe są określone w pliku *Docker-Compose. yml* .

::: zone-end

## <a name="use-persistent-shared-storage"></a>Używanie trwałego magazynu udostępnionego

::: zone pivot="container-windows"

Możesz użyć katalogu *C:\home* w systemie plików aplikacji, aby utrwalać pliki przez ponowne uruchomienie i udostępnić je między wystąpieniami. `C:\home`Aby umożliwić aplikacji kontenera dostęp do magazynu trwałego, należy podać w Twojej aplikacji.

Gdy trwały magazyn jest wyłączony, operacje zapisu w `C:\home` katalogu nie są utrwalane. [Dzienniki hosta platformy Docker i dzienniki kontenerów](#access-diagnostic-logs) są zapisywane w domyślnym trwałym magazynie udostępnionym, który nie jest dołączony do kontenera. Gdy magazyn trwały jest włączony, wszystkie operacje zapisu w `C:\home` katalogu są utrwalane i można uzyskać do nich dostęp przez wszystkie wystąpienia aplikacji skalowanej w poziomie, a dziennik jest dostępny pod adresem `C:\home\LogFiles` .

::: zone-end

::: zone pivot="container-linux"

Możesz użyć katalogu */Home* w systemie plików aplikacji, aby utrwalać pliki przez ponowne uruchomienie i udostępnić je między wystąpieniami. `/home`Aby umożliwić aplikacji kontenera dostęp do magazynu trwałego, należy podać w Twojej aplikacji.

Gdy trwały magazyn jest wyłączony, operacje zapisu w `/home` katalogu nie są utrwalane między ponownymi uruchomieniami aplikacji ani między wieloma wystąpieniami. Jedynym wyjątkiem jest `/home/LogFiles` katalog, który jest używany do przechowywania dzienników platformy Docker i kontenerów. Gdy magazyn trwały jest włączony, wszystkie operacje zapisu w `/home` katalogu są utrwalane i mogą być dostępne we wszystkich wystąpieniach aplikacji skalowanej w poziomie.

::: zone-end

Domyślnie magazyn trwały jest wyłączony, a ustawienie nie jest widoczne w ustawieniach aplikacji. Aby ją włączyć, należy ustawić `WEBSITES_ENABLE_APP_SERVICE_STORAGE` ustawienie aplikacji za pośrednictwem [Cloud Shell](https://shell.azure.com). W bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=true
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITES_ENABLE_APP_SERVICE_STORAGE"=true}
```

> [!NOTE]
> Możesz również [skonfigurować własny magazyn trwały](configure-connect-to-azure-storage.md).

## <a name="detect-https-session"></a>Wykrywanie sesji protokołu HTTPS

App Service przerywa protokół TLS/SSL na frontonie. Oznacza to, że żądania protokołu TLS/SSL nigdy nie są wysyłane do aplikacji. Nie jest konieczne i nie należy implementować obsługi protokołu TLS/SSL w aplikacji. 

Frontony znajdują się w centrach danych platformy Azure. Jeśli używasz protokołu TLS/SSL z aplikacją, ruch przez Internet będzie zawsze szyfrowany jako bezpieczny.

::: zone pivot="container-windows"

## <a name="customize-aspnet-machine-key-injection"></a>Dostosuj iniekcję klucza komputera ASP.NET

 Podczas uruchamiania kontenera automatycznie generowane klucze są wstrzykiwane do kontenera jako klucze komputera dla procedur kryptograficznych ASP.NET. [Te klucze można znaleźć w kontenerze](#connect-to-the-container) , wyszukując następujące zmienne środowiskowe: `MACHINEKEY_Decryption` , `MACHINEKEY_DecryptionKey` , `MACHINEKEY_ValidationKey` , `MACHINEKEY_Validation` . 

Nowe klucze przy każdym ponownym uruchomieniu mogą zresetować uwierzytelnianie formularzy ASP.NET i stan widoku, jeśli aplikacja zależy od nich. Aby zapobiec automatycznemu ponownemu generowaniu kluczy, [Ustaw je ręcznie jako ustawienia aplikacji App Service](#configure-environment-variables). 

## <a name="connect-to-the-container"></a>Nawiązywanie połączenia z kontenerem

Możesz połączyć się z kontenerem systemu Windows bezpośrednio dla zadań diagnostycznych, przechodząc do `https://<app-name>.scm.azurewebsites.net/DebugConsole` . Oto jak to działa:

- Konsola debugowania umożliwia wykonywanie poleceń interaktywnych, takich jak uruchamianie sesji programu PowerShell, inspekcja kluczy rejestru i nawigowanie w całym systemie plików kontenerów.
- Ta funkcja działa niezależnie od przeglądarki graficznej, która zawiera tylko te pliki w [magazynie udostępnionym](#use-persistent-shared-storage).
- W aplikacji skalowanej w poziomie konsola debugowania jest połączona z jednym z wystąpień kontenera. Można wybrać inne wystąpienie z listy rozwijanej **wystąpienie** w górnym menu.
- Wszelkie zmiany wprowadzone do kontenera z poziomu konsoli *programu nie są zachowywane* po ponownym uruchomieniu aplikacji (z wyjątkiem zmian w magazynie udostępnionym), ponieważ nie są one częścią obrazu platformy Docker. Aby zachować zmiany, na przykład ustawienia rejestru i instalację oprogramowania, należy je częścią pliku dockerfile.

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

App Service rejestruje akcje przez hosta platformy Docker oraz działania z poziomu kontenera. Dzienniki z hosta Docker (dzienniki platformy) są domyślnie wysyłane, ale Dzienniki aplikacji lub Dzienniki serwera sieci Web z poziomu kontenera muszą być włączone ręcznie. Aby uzyskać więcej informacji, zobacz [Włączanie rejestrowania aplikacji](troubleshoot-diagnostic-logs.md#enable-application-logging-linuxcontainer) i [Włączanie rejestrowania serwera sieci Web](troubleshoot-diagnostic-logs.md#enable-web-server-logging). 

Istnieje kilka sposobów uzyskiwania dostępu do dzienników platformy Docker:

- [W Azure Portal](#in-azure-portal)
- [Z poziomu konsoli kudu](#from-the-kudu-console)
- [Za pomocą interfejsu API kudu](#with-the-kudu-api)
- [Wysyłanie dzienników do usługi Azure Monitor](troubleshoot-diagnostic-logs.md#send-logs-to-azure-monitor-preview)

### <a name="in-azure-portal"></a>W Azure Portal

Dzienniki platformy Docker są wyświetlane w portalu, na stronie **Ustawienia kontenera** aplikacji. Dzienniki są obcinane, ale można pobrać wszystkie dzienniki, klikając przycisk **Pobierz**. 

### <a name="from-the-kudu-console"></a>Z poziomu konsoli kudu

Przejdź do `https://<app-name>.scm.azurewebsites.net/DebugConsole` folderu **LogFiles** , aby wyświetlić poszczególne pliki dziennika. Aby pobrać cały katalog **LogFiles** , kliknij ikonę **pobierania** znajdującą się po lewej stronie nazwy katalogu. Możesz również uzyskać dostęp do tego folderu przy użyciu klienta FTP.

W terminalu konsoli nie można uzyskać dostępu do `C:\home\LogFiles` folderu domyślnie, ponieważ trwały magazyn udostępniony nie jest włączony. Aby włączyć to zachowanie w terminalu konsoli, [Włącz trwały magazyn udostępniony](#use-persistent-shared-storage).

Jeśli spróbujesz pobrać dziennik platformy Docker, który jest aktualnie używany przy użyciu klienta FTP, może wystąpić błąd z powodu blokady pliku.

### <a name="with-the-kudu-api"></a>Za pomocą interfejsu API kudu

Przejdź bezpośrednio do, `https://<app-name>.scm.azurewebsites.net/api/logs/docker` Aby zobaczyć metadane dotyczące dzienników platformy Docker. Na liście znajduje się więcej niż jeden plik dziennika, a `href` Właściwość umożliwia pobranie pliku dziennika bezpośrednio. 

Aby pobrać wszystkie dzienniki w jednym pliku ZIP, należy uzyskać dostęp `https://<app-name>.scm.azurewebsites.net/api/logs/docker/zip` .

## <a name="customize-container-memory"></a>Dostosuj pamięć kontenera

Domyślnie wszystkie kontenery systemu Windows wdrożone w Azure App Service są ograniczone do 1 GB pamięci RAM. Tę wartość można zmienić, dostarczając `WEBSITE_MEMORY_LIMIT_MB` Ustawienia aplikacji za pośrednictwem [Cloud Shell](https://shell.azure.com). W bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITE_MEMORY_LIMIT_MB=2000
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_MEMORY_LIMIT_MB"=2000}
```

Wartość jest zdefiniowana w MB i musi być mniejsza i równa całkowitej pamięci fizycznej hosta. Na przykład w planie App Service z 8 GB pamięci RAM łączna suma `WEBSITE_MEMORY_LIMIT_MB` dla wszystkich aplikacji nie może przekraczać 8 GB. Informacje o ilości pamięci dostępnej dla każdej warstwy cenowej można znaleźć w [App Service cenach](https://azure.microsoft.com/pricing/details/app-service/windows/)w sekcji **Plan kontenera Premium (Windows)** .

## <a name="customize-the-number-of-compute-cores"></a>Dostosuj liczbę rdzeni obliczeniowych

Domyślnie kontener systemu Windows jest uruchamiany ze wszystkimi dostępnymi rdzeniami dla wybranej warstwy cenowej. Można na przykład zmniejszyć liczbę rdzeni używanych przez miejsce przejściowe. Aby zmniejszyć liczbę rdzeni używanych przez kontener, należy ustawić `WEBSITE_CPU_CORES_LIMIT` ustawienie aplikacji na preferowaną liczbę rdzeni. Można ustawić go za pośrednictwem [Cloud Shell](https://shell.azure.com). W bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --slot staging --settings WEBSITE_CPU_CORES_LIMIT=1
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"WEBSITE_CPU_CORES_LIMIT"=1}
```

> [!NOTE]
> Aktualizacja ustawienia aplikacji wyzwala automatyczne ponowne uruchomienie, co powoduje minimalne przestoje. W przypadku aplikacji produkcyjnej Rozważ zamienienie jej w miejsce przejściowe, zmiana ustawienia aplikacji w miejscu przejściowym, a następnie zamianę go z powrotem do środowiska produkcyjnego.

Sprawdź swój skorygowany numer, przechodząc do konsoli kudu ( `https://<app-name>.scm.azurewebsites.net` ) i wpisując następujące polecenia przy użyciu programu PowerShell. Każde polecenie wyprowadza liczbę.

```PowerShell
Get-ComputerInfo | ft CsNumberOfLogicalProcessors # Total number of enabled logical processors. Disabled processors are excluded.
Get-ComputerInfo | ft CsNumberOfProcessors # Number of physical processors.
```

Procesory mogą być procesorami wielordzeniowymi lub wielowątkowymi. Informacje o liczbie rdzeni dostępnych dla każdej warstwy cenowej można znaleźć w [App Service cenach](https://azure.microsoft.com/pricing/details/app-service/windows/)w sekcji **Plan kontenera Premium (Windows)** .

## <a name="customize-health-ping-behavior"></a>Dostosuj zachowanie polecenia ping kondycji

App Service traktuje kontener, aby mógł zostać pomyślnie uruchomiony podczas uruchamiania kontenera i reaguje na polecenie ping protokołu HTTP. Żądanie ping kondycji zawiera nagłówek `User-Agent= "App Service Hyper-V Container Availability Check"` . Jeśli kontener zostanie uruchomiony, ale nie odpowiada na polecenie ping po określonym czasie, App Service rejestruje zdarzenie w dzienniku platformy Docker, co oznacza, że kontener nie został uruchomiony. 

Jeśli aplikacja korzysta z dużej ilości zasobów, kontener może nie odpowiadać na pingowanie HTTP w czasie. Aby kontrolować akcje w przypadku niepowodzenia ping protokołu HTTP, należy ustawić `CONTAINER_AVAILABILITY_CHECK_MODE` ustawienie aplikacji. Można ustawić go za pośrednictwem [Cloud Shell](https://shell.azure.com). W bash:

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings CONTAINER_AVAILABILITY_CHECK_MODE="ReportOnly"
```

W programie PowerShell:

```azurepowershell-interactive
Set-AzWebApp -ResourceGroupName <group-name> -Name <app-name> -AppSettings @{"CONTAINER_AVAILABILITY_CHECK_MODE"="ReportOnly"}
```

W poniższej tabeli przedstawiono możliwe wartości:

| Wartość | Znajduje |
| - | - |
| **Usuwania** | Uruchom ponownie kontener po trzech kolejnych kontrolach dostępności |
| **ReportOnly** | Wartość domyślna. Nie uruchamiaj ponownie kontenera, ale Zgłoś w dziennikach platformy Docker kontener po trzech kolejnych kontrolach dostępności. |
| **Wyłączone** | Nie sprawdzaj dostępności. |

## <a name="support-for-group-managed-service-accounts"></a>Obsługa kont usług zarządzanych przez grupę

Konta usług zarządzane przez grupę (kont gMSA) nie są obecnie obsługiwane w kontenerach systemu Windows w App Service.

::: zone-end

::: zone pivot="container-linux"

## <a name="enable-ssh"></a>Włączanie protokołu SSH

Protokół SSH umożliwia bezpieczną komunikację między kontenerem i klientem. Aby kontener niestandardowy obsługiwał protokół SSH, należy dodać go do samego obrazu platformy Docker.

> [!TIP]
> Wszystkie wbudowane kontenery systemu Linux w App Service dodaliśmy instrukcje protokołu SSH w swoich repozytoriach obrazów. Aby zobaczyć, jak to jest włączone, możesz przejść przez następujące instrukcje dotyczące [ repozytoriumNode.js 10,14](https://github.com/Azure-App-Service/node/blob/master/10.14) . Konfiguracja w obrazie wbudowanym Node.js jest nieco inna, ale jest taka sama w zasadzie.

- Dodaj [plik sshd_config](https://man.openbsd.org/sshd_config) do repozytorium, jak w poniższym przykładzie.

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
    > Ten plik konfiguruje OpenSSH i musi zawierać następujące elementy:
    > - `Port` musi być ustawiona na 2222.
    > - Element `Ciphers` musi zawierać co najmniej jeden element z tej listy: `aes128-cbc,3des-cbc,aes256-cbc`.
    > - Element `MACs` musi zawierać co najmniej jeden element z tej listy: `hmac-sha1,hmac-sha1-96`.

- W pliku dockerfile Dodaj następujące polecenia:

    ```Dockerfile
    # Install OpenSSH and set the password for root to "Docker!". In this example, "apk add" is the install instruction for an Alpine Linux-based image.
    RUN apk add openssh \
         && echo "root:Docker!" | chpasswd 

    # Copy the sshd_config file to the /etc/ssh/ directory
    COPY sshd_config /etc/ssh/

    # Open port 2222 for SSH access
    EXPOSE 80 2222
    ```

    Ta konfiguracja nie zezwala na połączenia zewnętrzne z kontenerem. Port 2222 kontenera jest dostępny tylko w sieci mostkowej prywatnej sieci wirtualnej i nie jest dostępny dla osoby atakującej w Internecie.

- W skrypcie uruchamiania kontenera Uruchom serwer SSH.

    ```bash
    /usr/sbin/sshd
    ```

## <a name="access-diagnostic-logs"></a>Uzyskiwanie dostępu do dzienników diagnostycznych

[!INCLUDE [Access diagnostic logs](../../includes/app-service-web-logs-access-linux-no-h.md)]

## <a name="configure-multi-container-apps"></a>Konfigurowanie aplikacji z obsługą kontenera

- [Korzystanie z magazynu trwałego w Docker Compose](#use-persistent-storage-in-docker-compose)
- [Ograniczenia wersji zapoznawczej](#preview-limitations)
- [Opcje Docker Compose](#docker-compose-options)

### <a name="use-persistent-storage-in-docker-compose"></a>Korzystanie z magazynu trwałego w Docker Compose

Aplikacje z obsługą kontenerów, takie jak WordPress, potrzebują trwałego magazynu w celu poprawnego działania. Aby ją włączyć, konfiguracja Docker Compose musi wskazywać lokalizację magazynu *poza* kontenerem. Lokalizacje magazynu wewnątrz kontenera nie utrwalają zmian poza ponownym uruchomieniem aplikacji.

Aby włączyć magazyn trwały `WEBSITES_ENABLE_APP_SERVICE_STORAGE` , należy ustawić ustawienie aplikacji przy użyciu polecenia [AZ webapp config AppSettings set](/cli/azure/webapp/config/appsettings#az-webapp-config-appsettings-set) w [Cloud Shell](https://shell.azure.com).

```azurecli-interactive
az webapp config appsettings set --resource-group <group-name> --name <app-name> --settings WEBSITES_ENABLE_APP_SERVICE_STORAGE=TRUE
```

W pliku *Docker-Compose. yml* zamapuj `volumes` opcję na `${WEBAPP_STORAGE_HOME}` . 

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

Wiele kontenerów jest obecnie w wersji zapoznawczej. Następujące funkcje platformy App Service nie są obsługiwane:

- Uwierzytelnianie/autoryzacja
- Tożsamości zarządzane
- CORS

### <a name="docker-compose-options"></a>Opcje Docker Compose

Na poniższych listach przedstawiono obsługiwane i nieobsługiwane Docker Compose opcje konfiguracji:

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
- porty inne niż 80 i 8080 (zignorowano)

> [!NOTE]
> Wszystkie inne opcje, które nie są jawnie wywoływane, są ignorowane w publicznej wersji zapoznawczej.

[!INCLUDE [robots933456](../../includes/app-service-web-configure-robots933456.md)]

::: zone-end

## <a name="next-steps"></a>Następne kroki

> [!div class="nextstepaction"]
> [Samouczek: Migrowanie oprogramowania niestandardowego do Azure App Service przy użyciu kontenera niestandardowego](tutorial-custom-container.md)

::: zone pivot="container-linux"

> [!div class="nextstepaction"]
> [Samouczek: wielokontenerowa aplikacja WordPress](tutorial-multi-container-app.md)

::: zone-end

Lub zapoznaj się z dodatkowymi zasobami:

[Ładowanie certyfikatu w kontenerach systemu Windows/Linux](configure-ssl-certificate-in-code.md#load-certificate-in-linuxwindows-containers)
