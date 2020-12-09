---
title: Konfigurowanie aplikacji w portalu
description: Dowiedz się, jak skonfigurować typowe ustawienia aplikacji App Service w Azure Portal. Ustawienia aplikacji, parametry połączeń, platforma, stos języka, kontener itp.
keywords: Azure App Service, aplikacja sieci Web, ustawienia aplikacji, zmienne środowiskowe
ms.assetid: 9af8a367-7d39-4399-9941-b80cbc5f39a0
ms.topic: article
ms.date: 08/13/2019
ms.custom: devx-track-csharp, seodec18, devx-track-azurecli
ms.openlocfilehash: 76cfefa3f104ecef69e28fecd1c37fc336b0ce8c
ms.sourcegitcommit: 48cb2b7d4022a85175309cf3573e72c4e67288f5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 12/08/2020
ms.locfileid: "96854652"
---
# <a name="configure-an-app-service-app-in-the-azure-portal"></a>Skonfiguruj aplikację App Service w Azure Portal

W tym temacie opisano sposób konfigurowania typowych ustawień aplikacji sieci Web, zaplecza mobilnego lub aplikacji interfejsu API przy użyciu [Azure Portal].

## <a name="configure-app-settings"></a>Konfigurowanie ustawień aplikacji

W App Service ustawienia aplikacji są zmienne, które są przesyłane jako zmienne środowiskowe do kodu aplikacji. W przypadku aplikacji systemu Linux i kontenerów niestandardowych App Service przekazuje ustawienia aplikacji do kontenera przy użyciu `--env` flagi, aby ustawić zmienną środowiskową w kontenerze.

W [Azure Portal]Wyszukaj i wybierz pozycję **App Services**, a następnie wybierz aplikację. 

![Wyszukaj App Services](./media/configure-common/search-for-app-services.png)

W menu po lewej stronie aplikacji wybierz pozycję **Configuration**  >  **Ustawienia aplikacji** konfiguracji.

![Ustawienia aplikacji](./media/configure-common/open-ui.png)

W przypadku deweloperów ASP.NET i ASP.NET Core, Ustawianie ustawień aplikacji w App Service przypomina Ustawianie ich w `<appSettings>` *Web.config* lub *appsettings.jsna*, ale wartości w App Service przesłaniają te elementy w *Web.config* lub *appsettings.js*. Ustawienia programistyczne (na przykład lokalne hasło MySQL) można zachować w *Web.config* lub *appsettings.jsw systemach* tajnych (na przykład hasła bazy danych Azure mysql) bezpiecznie w App Service. Ten sam kod używa ustawień programistycznych podczas debugowania lokalnego i korzysta z wpisów tajnych produkcji w przypadku wdrożenia na platformie Azure.

Inne stosy języka, podobnie, pobierają ustawienia aplikacji jako zmienne środowiskowe w czasie wykonywania. Aby zapoznać się z procedurami specyficznymi dla stosu, zobacz:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Kontenery niestandardowe](configure-custom-container.md#configure-environment-variables)

Ustawienia aplikacji są zawsze szyfrowane, gdy są przechowywane (szyfrowane w trybie REST).

> [!NOTE]
> Ustawienia aplikacji mogą być również rozwiązywane z [Key Vault](../key-vault/index.yml) przy użyciu [Key Vault odwołań](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Pokaż ukryte wartości

Domyślnie wartości ustawień aplikacji są ukrywane w portalu w celu zabezpieczenia. Aby wyświetlić ukrytą wartość ustawienia aplikacji, kliknij pole **wartość** tego ustawienia. Aby wyświetlić wartości wszystkich ustawień aplikacji, kliknij przycisk **Pokaż wartość** .

### <a name="add-or-edit"></a>Dodaj lub edytuj

Aby dodać nowe ustawienie aplikacji, kliknij pozycję **nowe ustawienie aplikacji**. W oknie dialogowym można [przykleić ustawienie do bieżącego gniazda](deploy-staging-slots.md#which-settings-are-swapped).

Aby edytować ustawienia, kliknij przycisk **Edytuj** po prawej stronie.

Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć pozycję **Zapisz** ponownie na stronie **Konfiguracja** .

> [!NOTE]
> W domyślnym kontenerze systemu Linux lub dla niestandardowego kontenera systemu Linux każda zagnieżdżona struktura klucza JSON w nazwie ustawienia aplikacji, tak jak `ApplicationInsights:InstrumentationKey` musi być skonfigurowana w App Service jak `ApplicationInsights__InstrumentationKey` dla nazwy klucza. Innymi słowy, wszystkie `:` powinny zostać zastąpione przez `__` (podwójne podkreślenie).
>

### <a name="edit-in-bulk"></a>Edytuj luzem

Aby dodać lub edytować ustawienia aplikacji w trybie zbiorczym, kliknij przycisk **Edytuj zaawansowane** . Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć pozycję **Zapisz** ponownie na stronie **Konfiguracja** .

Ustawienia aplikacji mają następujące formatowanie JSON:

```json
[
  {
    "name": "<key-1>",
    "value": "<value-1>",
    "slotSetting": false
  },
  {
    "name": "<key-2>",
    "value": "<value-2>",
    "slotSetting": false
  },
  ...
]
```

### <a name="automate-app-settings-with-the-azure-cli"></a>Automatyzowanie ustawień aplikacji za pomocą interfejsu wiersza polecenia platformy Azure

Za pomocą interfejsu wiersza polecenia platformy Azure można tworzyć ustawienia i zarządzać nimi z poziomu wiersza poleceń.

- Przypisz wartość do ustawienia przy użyciu [AZ webapp config Settings Set](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_set):

    ```azurecli-interactive
    az webapp config appsettings set --name <app-name> --resource-group <resource-group-name> --settings <setting-name>="<value>"
    ```
        
    Zamień na `<setting-name>` nazwę ustawienia i `<value>` wartość, która ma zostać przypisana do niego. To polecenie powoduje utworzenie ustawienia, jeśli jeszcze nie istnieje.
    
- Pokaż wszystkie ustawienia i ich wartości za pomocą właściwości [AZ webapp config AppSettings list](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_list):
    
    ```azurecli-interactive
    az webapp config appsettings list --name <app-name> --resource-group <resource-group-name>
    ```
    
- Usuń co najmniej jedno ustawienie przy użyciu [AZ webapp config ustawienia aplikacji Delete](/cli/azure/webapp/config/appsettings#az_webapp_config_appsettings_delete):

    ```azurecli-interactive
    az webapp config appsettings delete --name <app-name> --resource-group <resource-group-name> --setting-names {<names>}
    ```
    
    Zamień na `<names>` rozdzieloną spacjami listę nazw ustawień.

## <a name="configure-connection-strings"></a>Konfigurowanie parametrów połączenia

W [Azure Portal]Wyszukaj i wybierz pozycję **App Services**, a następnie wybierz aplikację. W menu po lewej stronie aplikacji wybierz pozycję **Configuration**  >  **Ustawienia aplikacji** konfiguracji.

![Ustawienia aplikacji](./media/configure-common/open-ui.png)

W przypadku deweloperów ASP.NET i ASP.NET Core, ustawianie parametrów połączenia w App Service przypomina Ustawianie ich w `<connectionStrings>` *Web.config*, ale wartości ustawionych w App Service przesłaniają te elementy w *Web.config*. Możesz przechowywać ustawienia programistyczne (na przykład plik bazy danych) w *Web.config* i SQL Database wpisy tajne (na przykład poświadczenia) w App Service. Ten sam kod używa ustawień programistycznych podczas debugowania lokalnego i korzysta z wpisów tajnych produkcji w przypadku wdrożenia na platformie Azure.

W przypadku innych stosów języka lepiej jest używać [ustawień aplikacji](#configure-app-settings) , ponieważ parametry połączeń wymagają specjalnego formatowania w kluczach zmiennych w celu uzyskania dostępu do wartości. Poniżej przedstawiono jeden wyjątek: w przypadku konfigurowania parametrów połączenia w aplikacji kopie zapasowe niektórych typów baz danych platformy Azure są tworzone razem z aplikacją. Aby uzyskać więcej informacji, zobacz [co to jest kopia zapasowa](manage-backup.md#what-gets-backed-up). Jeśli ta zautomatyzowana kopia zapasowa nie jest potrzebna, Użyj ustawień aplikacji.

W czasie wykonywania parametry połączenia są dostępne jako zmienne środowiskowe poprzedzone prefiksem następujących typów połączeń:

* SqlServer `SQLCONNSTR_`  
* MySQL `MYSQLCONNSTR_` 
* Platforma SqlAzure: `SQLAZURECONNSTR_` 
* Celnej `CUSTOMCONNSTR_`
* PostgreSQL `POSTGRESQLCONNSTR_`  

Na przykład parametry połączenia MySql o nazwie *connectionstring1* są dostępne jako zmienne środowiskowe `MYSQLCONNSTR_connectionString1` . Aby zapoznać się z procedurami specyficznymi dla stosu, zobacz:

- [ASP.NET Core](configure-language-dotnetcore.md#access-environment-variables)
- [Node.js](configure-language-nodejs.md#access-environment-variables)
- [PHP](configure-language-php.md#access-environment-variables)
- [Python](configure-language-python.md#access-environment-variables)
- [Java](configure-language-java.md#configure-data-sources)
- [Ruby](configure-language-ruby.md#access-environment-variables)
- [Kontenery niestandardowe](configure-custom-container.md#configure-environment-variables)

Parametry połączenia są zawsze szyfrowane, gdy są przechowywane (szyfrowane w trybie REST).

> [!NOTE]
> Parametry połączenia można również rozpoznać z [Key Vault](../key-vault/index.yml) przy użyciu [Key Vault odwołań](app-service-key-vault-references.md).

### <a name="show-hidden-values"></a>Pokaż ukryte wartości

Domyślnie wartości parametrów połączenia są ukryte w portalu w celu zabezpieczenia. Aby wyświetlić ukrytą wartość parametrów połączenia, po prostu kliknij pole **wartość** tego ciągu. Aby wyświetlić wartości wszystkich parametrów połączenia, kliknij przycisk **Pokaż wartość** .

### <a name="add-or-edit"></a>Dodaj lub edytuj

Aby dodać nowe parametry połączenia, kliknij przycisk **nowe parametry połączenia**. W oknie dialogowym można [przykleić parametry połączenia do bieżącego gniazda](deploy-staging-slots.md#which-settings-are-swapped).

Aby edytować ustawienia, kliknij przycisk **Edytuj** po prawej stronie.

Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć pozycję **Zapisz** ponownie na stronie **Konfiguracja** .

### <a name="edit-in-bulk"></a>Edytuj luzem

Aby dodać lub edytować parametry połączenia zbiorczo, kliknij przycisk **Edytuj zaawansowane** . Po zakończeniu kliknij przycisk **Aktualizuj**. Nie zapomnij kliknąć pozycję **Zapisz** ponownie na stronie **Konfiguracja** .

Parametry połączenia mają następujące formatowanie JSON:

```json
[
  {
    "name": "name-1",
    "value": "conn-string-1",
    "type": "SQLServer",
    "slotSetting": false
  },
  {
    "name": "name-2",
    "value": "conn-string-2",
    "type": "PostgreSQL",
    "slotSetting": false
  },
  ...
]
```

<a name="platform"></a>
<a name="alwayson"></a>

## <a name="configure-general-settings"></a>Konfigurowanie ustawień ogólnych

W [Azure Portal]Wyszukaj i wybierz pozycję **App Services**, a następnie wybierz aplikację. W menu po lewej stronie aplikacji wybierz pozycję **Konfiguracja**  >  **Ustawienia ogólne**.

![Ustawienia ogólne](./media/configure-common/open-general.png)

Tutaj można skonfigurować niektóre typowe ustawienia dla aplikacji. Niektóre ustawienia wymagają [skalowania do wyższych warstw cenowych](manage-scale-up.md).

- **Ustawienia stosu**: stos oprogramowania do uruchamiania aplikacji, w tym wersje język i zestaw SDK.

    W przypadku aplikacji systemu Linux i niestandardowych aplikacji kontenera można wybrać wersję środowiska uruchomieniowego języka i ustawić opcjonalne **polecenie uruchamiania** lub plik poleceń uruchomieniowych.

    ![Ustawienia ogólne dla kontenerów systemu Linux](./media/configure-common/open-general-linux.png)

- **Ustawienia platformy**: umożliwia skonfigurowanie ustawień platformy hostingu, w tym:
    - Liczba **bitów**: 32-bitowe lub 64-bitowe.
    - **Protokół WebSocket**: na przykład dla [ASP.NET signaler] lub [Socket.IO](https://socket.io/).
    - **Zawsze włączone**: utrzymuje załadowana aplikacja nawet wtedy, gdy nie ma ruchu. Jest to wymagane w przypadku ciągłych zadań WebJob lub zadań WebJob, które są wyzwalane przy użyciu wyrażenia firmy cronus.
      > [!NOTE]
      > Dzięki funkcji Always On moduł równoważenia obciążenia frontonu wysyła żądanie do katalogu głównego aplikacji. Nie można skonfigurować tego punktu końcowego aplikacji App Service.
    - **Wersja potoku zarządzanego**: [tryb potokowy]usług IIS. Ustaw ją na **klasyczny** , jeśli masz starszą aplikację, która wymaga starszej wersji usług IIS.
    - **Wersja protokołu HTTP**: ustaw na **2,0** , aby włączyć obsługę protokołu [https/2](https://wikipedia.org/wiki/HTTP/2) .
    > [!NOTE]
    > Większość nowoczesnych przeglądarek obsługuje protokół HTTP/2 tylko w przypadku protokołu TLS, podczas gdy ruch nieszyfrowany nadal używa protokołu HTTP/1.1. Aby upewnić się, że przeglądarki klienta nawiązują połączenie z aplikacją za pomocą protokołu HTTP/2, Zabezpiecz swoją niestandardową nazwę DNS. Aby uzyskać więcej informacji, zobacz [Zabezpieczanie niestandardowej nazwy DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md).
    - **Koligacja ARR**: w przypadku wdrożenia obejmującego wiele wystąpień upewnij się, że klient jest kierowany do tego samego wystąpienia w okresie istnienia sesji. Tę opcję można ustawić na wartość **off** dla bezstanowych aplikacji.
- **Debugowanie**: Włącz debugowanie zdalne dla aplikacji [ASP.NET](troubleshoot-dotnet-visual-studio.md#remotedebug), [ASP.NET Core](/visualstudio/debugger/remote-debugging-azure)lub [Node.js](configure-language-nodejs.md#debug-remotely) . Ta opcja jest włączana automatycznie po 48 godzinach.
- **Przychodzące certyfikaty klienta**: Wymagaj certyfikatów klienta w ramach [uwierzytelniania wzajemnego](app-service-web-configure-tls-mutual-auth.md).

## <a name="configure-default-documents"></a>Konfigurowanie dokumentów domyślnych

To ustawienie dotyczy tylko aplikacji systemu Windows.

W [Azure Portal]Wyszukaj i wybierz pozycję **App Services**, a następnie wybierz aplikację. W menu po lewej stronie aplikacji wybierz kolejno pozycje **Konfiguracja**  >  **dokumenty domyślne**.

![Dokumenty domyślne](./media/configure-common/open-documents.png)

Dokument domyślny jest stroną sieci Web, która jest wyświetlana na głównym adresie URL witryny sieci Web. Używany jest pierwszy pasujący plik na liście. Aby dodać nowy dokument domyślny, kliknij pozycję **Nowy dokument**. Nie zapomnij kliknąć przycisk **Zapisz**.

Jeśli aplikacja używa modułów, które są oparte na adresie URL zamiast obsługiwać zawartość statyczną, nie ma potrzeby stosowania dokumentów domyślnych.

## <a name="configure-path-mappings"></a>Konfiguruj mapowania ścieżek

W [Azure Portal]Wyszukaj i wybierz pozycję **App Services**, a następnie wybierz aplikację. W menu po lewej stronie aplikacji wybierz pozycję **Configuration**  >  **mapowania ścieżki** konfiguracji.

![Mapowania ścieżki](./media/configure-common/open-path.png)

Na stronie **mapowania ścieżki** są wyświetlane różne elementy w oparciu o typ systemu operacyjnego.

### <a name="windows-apps-uncontainerized"></a>Aplikacje systemu Windows (niekontenery)

W przypadku aplikacji systemu Windows można dostosować mapowania programu obsługi usług IIS i wirtualne aplikacje i katalogi.

Mapowania obsługi umożliwiają dodawanie niestandardowych procesorów skryptów do obsługi żądań dla określonych rozszerzeń plików. Aby dodać niestandardową procedurę obsługi, kliknij pozycję **Nowy program obsługi**. Skonfiguruj procedurę obsługi w następujący sposób:

- **Rozszerzenie**. Rozszerzenie pliku, które ma być obsługiwane, np. *\* php* lub *Handler. FCGI*.
- **Procesor skryptu**. Ścieżka bezwzględna procesora skryptu do użytkownika. Żądania do plików, które pasują do rozszerzenia pliku, są przetwarzane przez procesor skryptów. Użyj ścieżki, `D:\home\site\wwwroot` Aby odwołać się do katalogu głównego aplikacji.
- **Argumenty**. Opcjonalne argumenty wiersza polecenia dla procesora skryptów.

Każda aplikacja ma domyślną ścieżkę katalogu głównego ( `/` ) zamapowana na `D:\home\site\wwwroot` , gdzie kod jest wdrażany domyślnie. Jeśli katalog główny aplikacji znajduje się w innym folderze lub jeśli repozytorium zawiera więcej niż jedną aplikację, możesz w tym miejscu edytować lub dodać wirtualne aplikacje i katalogi. Kliknij pozycję **Nowa aplikacja wirtualna lub katalog**.

Aby skonfigurować aplikacje i katalogi wirtualne, określ każdy katalog wirtualny i odpowiadającą mu ścieżkę fizyczną względem katalogu głównego witryny sieci Web ( `D:\home` ). Opcjonalnie możesz zaznaczyć pole wyboru **aplikacji** , aby oznaczyć katalog wirtualny jako aplikację.

### <a name="containerized-apps"></a>Aplikacje w kontenerze

Możesz [dodać niestandardowy magazyn dla aplikacji w kontenerze](configure-connect-to-azure-storage.md). Aplikacje z kontenerami obejmują wszystkie aplikacje systemu Linux, a także kontenery niestandardowe z systemami Windows i Linux działające na App Service. Kliknij pozycję **Nowa instalacja usługi Azure Storage** i skonfiguruj własny magazyn w następujący sposób:

- **Nazwa**: Nazwa wyświetlana.
- **Opcje konfiguracji**: **podstawowa** lub **zaawansowana**.
- **Konta magazynu**: konto magazynu z żądanym kontenerem.
- **Typ magazynu**: **obiekty blob platformy Azure** lub **Azure Files**.
  > [!NOTE]
  > Aplikacje kontenera systemu Windows obsługują tylko Azure Files.
- **Kontener magazynu**: Konfiguracja podstawowa — żądany kontener.
- **Nazwa udziału**: w przypadku konfiguracji zaawansowanej nazwa udziału plików.
- **Klucz dostępu**: Aby skonfigurować konfigurację zaawansowaną, klucz dostępu.
- **Ścieżka instalacji**: ścieżka bezwzględna w kontenerze, w której ma zostać zainstalowany magazyn niestandardowy.

Aby uzyskać więcej informacji, zobacz [dostęp do usługi Azure Storage jako udziału sieciowego z kontenera w App Service](configure-connect-to-azure-storage.md).

## <a name="configure-language-stack-settings"></a>Konfigurowanie ustawień stosu języka

W przypadku aplikacji systemu Linux Zobacz:

- [ASP.NET Core](configure-language-dotnetcore.md)
- [Node.js](configure-language-nodejs.md)
- [PHP](configure-language-php.md)
- [Python](configure-language-python.md)
- [Java](configure-language-java.md)
- [Ruby](configure-language-ruby.md)

## <a name="configure-custom-containers"></a>Konfigurowanie kontenerów niestandardowych

Zobacz [Konfigurowanie niestandardowego kontenera systemu Linux dla Azure App Service](configure-custom-container.md)

## <a name="next-steps"></a>Następne kroki

- [Konfigurowanie niestandardowej nazwy domeny w Azure App Service]
- [Konfigurowanie środowisk przejściowych w usłudze Azure App Service]
- [Zabezpiecz niestandardową nazwę DNS z powiązaniem TLS/SSL w Azure App Service](configure-ssl-bindings.md)
- [Włączanie dzienników diagnostycznych](troubleshoot-diagnostic-logs.md)
- [Skalowanie aplikacji w Azure App Service]
- [Podstawowe informacje o monitorowaniu w Azure App Service]
- [Zmień ustawienia applicationHost.config przy użyciu pliku applicationHost. XDT](https://github.com/projectkudu/kudu/wiki/Xdt-transform-samples)

<!-- URL List -->

[ASP.NET SignalR]: https://www.asp.net/signalr
[Azure Portal]: https://portal.azure.com/
[Konfigurowanie niestandardowej nazwy domeny w Azure App Service]: ./app-service-web-tutorial-custom-domain.md
[Konfigurowanie środowisk przejściowych w usłudze Azure App Service]: ./deploy-staging-slots.md
[How to: Monitor web endpoint status]: ./web-sites-monitor.md
[Podstawowe informacje o monitorowaniu w Azure App Service]: ./web-sites-monitor.md
[tryb potokowy]: https://www.iis.net/learn/get-started/introduction-to-iis/introduction-to-iis-architecture#Application
[Skalowanie aplikacji w Azure App Service]: ./manage-scale-up.md
