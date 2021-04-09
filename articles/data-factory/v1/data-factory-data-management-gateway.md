---
title: Zarządzanie danymi bramę dla Data Factory
description: Użyj Zarządzanie danymi Gateway w Azure Data Factory, aby przenieść dane.
author: nabhishek
ms.service: data-factory
ms.topic: conceptual
ms.date: 01/10/2018
ms.author: abnarain
robots: noindex
ms.openlocfilehash: 708d84bdb3ebe8fbba6939aa771a9120868d5d1b
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "100375208"
---
# <a name="data-management-gateway"></a>Brama zarządzania danymi
> [!NOTE]
> Ten artykuł dotyczy wersji 1 usługi Data Factory. Jeśli używasz bieżącej wersji usługi Data Factory, zobacz [własne środowisko Integration Runtime w](../create-self-hosted-integration-runtime.md)artykule.

> [!NOTE]
> Zarządzanie danymi Brama została teraz zmieniona jako samodzielny Integration Runtime.

Brama zarządzania danymi jest agentem klienta, który należy zainstalować w środowisku lokalnym w celu kopiowania danych między magazynami danych w chmurze i lokalnymi. Lokalne magazyny danych obsługiwane przez Data Factory są wymienione w sekcji [obsługiwane źródła danych](data-factory-data-movement-activities.md#supported-data-stores-and-formats) .

Ten artykuł zawiera uzupełnienie przewodnika po artykule [przenoszenie danych między lokalnym i magazynem danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md) . W tym przewodniku utworzysz potok, który używa bramy do przenoszenia danych z bazy danych SQL Server do obiektu blob platformy Azure. Ten artykuł zawiera szczegółowe informacje na temat bramy zarządzania danymi.

Bramę zarządzania danymi można skalować w poziomie, kojarząc wiele maszyn lokalnych z bramą. Możesz skalować w górę, zwiększając liczbę zadań przenoszenia danych, które mogą być uruchamiane współbieżnie w węźle. Ta funkcja jest również dostępna dla bramy logicznej z pojedynczym węzłem. Aby uzyskać szczegółowe informacje, zobacz temat [skalowanie bramy zarządzania danymi w artykule Azure Data Factory](data-factory-data-management-gateway-high-availability-scalability.md) .

> [!NOTE]
> Obecnie Brama obsługuje tylko działanie kopiowania i działania procedury składowanej w Data Factory. Nie jest możliwe używanie bramy z działania niestandardowego w celu uzyskania dostępu do lokalnych źródeł danych.

[!INCLUDE [updated-for-az](../../../includes/updated-for-az.md)]

## <a name="overview"></a>Omówienie
### <a name="capabilities-of-data-management-gateway"></a>Możliwości bramy zarządzania danymi
Brama zarządzania danymi zapewnia następujące możliwości:

* Modelowanie lokalnych źródeł danych i źródeł danych w chmurze w ramach tej samej fabryki danych i przenoszenie danych.
* Do monitorowania i zarządzania z uwzględnieniem stanu bramy na stronie Data Factory należy mieć pojedyncze okienko Glass.
* Bezpieczne zarządzanie dostępem do lokalnych źródeł danych.
  * Nie są wymagane żadne zmiany w zaporze firmowej. Brama tworzy tylko wychodzące połączenia HTTP do otwierania Internetu.
  * Szyfruj poświadczenia dla lokalnych magazynów danych przy użyciu certyfikatu.
* Wydajne przenoszenie danych — dane są przesyłane równolegle, odporne na sporadyczne problemy z siecią za pomocą logiki autoponawiania.

### <a name="command-flow-and-data-flow"></a>Przepływ poleceń i przepływ danych
W przypadku używania działania kopiowania do kopiowania danych między środowiskiem lokalnym i chmurą działanie używa bramy do transferowania danych z lokalnego źródła danych do chmury i na odwrót.

Poniżej przedstawiono przepływ danych wysokiego poziomu dla i Podsumowanie kroków kopiowania z bramą Data Gateway: ![ przepływ danych przy użyciu bramy](./media/data-factory-data-management-gateway/data-flow-using-gateway.png)

1. Deweloper danych tworzy bramę dla Azure Data Factory przy użyciu [Azure Portal](https://portal.azure.com) lub [polecenia cmdlet programu PowerShell](/powershell/module/az.datafactory/).
2. Deweloper danych tworzy połączoną usługę dla lokalnego magazynu danych, określając bramę. W ramach konfigurowania połączonej usługi deweloperzy danych używają aplikacji Ustawienia poświadczeń, aby określić typy uwierzytelniania i poświadczenia. Okno dialogowe Ustawianie poświadczeń aplikacji komunikuje się z magazynem danych w celu przetestowania połączenia i bramy w celu zapisania poświadczeń.
3. Usługa Gateway szyfruje poświadczenia z certyfikatem skojarzonym z bramą (dostarczoną przez dewelopera danych) przed zapisaniem poświadczeń w chmurze.
4. Usługa Data Factory komunikuje się z bramą w celu planowania & zarządzania zadaniami za pośrednictwem kanału kontrolnego korzystającego z udostępnionej kolejki usługi Azure Service Bus. Gdy zadanie działania kopiowania musi zostać rozpoczęte, Data Factory kolejkuje żądanie wraz z informacjami o poświadczeniach. Brama uruchamia zadanie po sondowaniu kolejki.
5. Brama odszyfrowuje poświadczenia za pomocą tego samego certyfikatu, a następnie łączy się z lokalnym magazynem danych z prawidłowym typem uwierzytelniania i poświadczeniami.
6. Brama kopiuje dane z magazynu lokalnego do magazynu w chmurze lub odwrotnie w zależności od sposobu skonfigurowania działania kopiowania w potoku danych. Ten krok polega na tym, że brama komunikuje się bezpośrednio z usługami magazynu opartymi na chmurze, takimi jak Azure Blob Storage za pośrednictwem bezpiecznego kanału (HTTPS).

### <a name="considerations-for-using-gateway"></a>Zagadnienia dotyczące korzystania z bramy
* Pojedyncze wystąpienie bramy zarządzania danymi może być używane dla wielu lokalnych źródeł danych. Jednak **pojedyncze wystąpienie bramy jest powiązane tylko z jedną fabryką danych Azure** i nie może być współużytkowane z inną fabryką danych.
* Na jednym komputerze można zainstalować **tylko jedno wystąpienie bramy zarządzania danymi** . Załóżmy, że masz dwa fabryki danych, które muszą uzyskać dostęp do lokalnych źródeł danych, musisz zainstalować bramy na dwóch komputerach lokalnych. Innymi słowy, Brama jest powiązana z określoną fabryką danych
* **Brama nie musi znajdować się na tym samym komputerze co źródło danych**. Jednak Brama bliżej źródła danych skraca czas, w którym Brama może połączyć się ze źródłem danych. Zalecamy zainstalowanie bramy na komputerze, który jest inny niż ten, który jest hostem lokalnego źródła danych. Gdy brama i źródło danych znajdują się na różnych komputerach, Brama nie konkuruje o zasoby ze źródłem danych.
* Można mieć **wiele bram na różnych komputerach łączących się z tym samym lokalnym źródłem danych**. Na przykład mogą istnieć dwie bramy obsługujące dwa fabryki danych, ale to samo lokalne źródło danych jest rejestrowane zarówno dla fabryk danych.
* Jeśli na komputerze jest już zainstalowana Brama obsługująca **Power BI** scenariuszu, zainstaluj **oddzielną bramę dla Azure Data Factory** na innym komputerze.
* Bramy należy używać nawet w przypadku korzystania z **ExpressRoute**.
* Traktuj źródło danych jako lokalne źródło danych (znajdujące się za zaporą), nawet jeśli korzystasz z **ExpressRoute**. Użyj bramy, aby nawiązać połączenie między usługą a źródłem danych.
* Musisz **użyć bramy,** nawet jeśli magazyn danych znajduje się w chmurze na **maszynie wirtualnej IaaS platformy Azure**.

## <a name="installation"></a>Instalacja
### <a name="prerequisites"></a>Wymagania wstępne
* Obsługiwane wersje **systemu operacyjnego** to Windows 7, Windows 8/8.1, Windows 10, windows Server 2008 R2, windows Server 2012, windows Server 2012 R2. Instalacja bramy zarządzania danymi na kontrolerze domeny nie jest obecnie obsługiwana.
* Wymagany jest .NET Framework 4.5.1 lub nowszy. Jeśli instalujesz bramę na komputerze z systemem Windows 7, Zainstaluj .NET Framework 4,5 lub nowszą. Aby uzyskać szczegółowe informacje, zobacz [wymagania systemowe .NET Framework](/dotnet/framework/get-started/system-requirements) .
* Zalecana **Konfiguracja** maszyny bramy wynosi co najmniej 2 GHz, 4 rdzenie, 8 GB pamięci RAM i dysk 80 GB.
* Jeśli maszyna hosta jest w stanie hibernacji, Brama nie odpowiada na żądania danych. W związku z tym przed zainstalowaniem bramy należy skonfigurować odpowiedni **Plan** na komputerze. Jeśli komputer jest skonfigurowany do hibernacji, instalacja bramy monituje o komunikat.
* Aby pomyślnie zainstalować i skonfigurować bramę zarządzania danymi, musisz być administratorem na komputerze. Możesz dodać dodatkowych użytkowników do lokalnej grupy systemu Windows **Użytkownicy bramy zarządzania danymi** . Członkowie tej grupy mogą korzystać z narzędzia **Configuration Manager gateway zarządzanie danymi** , aby skonfigurować bramę.

Gdy przebiega działania kopiowania odbywa się z określoną częstotliwością, użycie zasobów (procesor, pamięć) na komputerze również jest zgodne z tym samym wzorcem i okresem bezczynności. Użycie zasobów również jest zależne od ilości przenoszonych danych. Gdy trwa wykonywanie wielu zadań kopiowania, zostanie wyświetlone użycie zasobów w godzinach szczytu.

### <a name="installation-options"></a>Opcje instalacji
Bramę zarządzania danymi można zainstalować w następujący sposób:

* Pobierając pakiet instalacyjny MSI z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=39717). Plik MSI może być również używany do uaktualnienia istniejącej bramy zarządzania danymi do najnowszej wersji, z zachowaniem wszystkich zachowywanych ustawień.
* Klikając pozycję **Pobierz i zainstaluj bramę Data Gateway** w obszarze Konfiguracja ręczna lub **Zainstaluj bezpośrednio na tym komputerze** w ramach instalacji ekspresowej. Instrukcje krok po kroku dotyczące korzystania z programu Express Setup można znaleźć w artykule [przenoszenie danych między środowiskiem lokalnym i chmurą](data-factory-move-data-between-onprem-and-cloud.md) . Krok ręczny spowoduje przejście do centrum pobierania. Instrukcje dotyczące pobierania i instalowania bramy z centrum pobierania znajdują się w następnej sekcji.

### <a name="installation-best-practices"></a>Najlepsze rozwiązania dotyczące instalacji:
1. Skonfiguruj plan dodatku na komputerze hosta dla bramy, aby komputer nie był w stanie hibernacji. Jeśli maszyna hosta jest w stanie hibernacji, Brama nie odpowiada na żądania danych.
2. Wykonaj kopię zapasową certyfikatu skojarzonego z bramą.

### <a name="install-the-gateway-from-download-center"></a>Zainstaluj bramę z centrum pobierania
1. Przejdź do [strony pobierania programu Microsoft zarządzanie danymi Gateway](https://www.microsoft.com/download/details.aspx?id=39717).
2. Kliknij pozycję **Pobierz**, wybierz wersję **64-** bitową (32-bit nie jest więcej obsługiwane), a następnie kliknij przycisk **dalej**.
3. Uruchom plik **MSI** bezpośrednio lub Zapisz go na dysku twardym i Uruchom.
4. Na stronie **powitalnej** wybierz **Język** , a następnie kliknij przycisk **dalej**.
5. **Zaakceptuj** umowę licencyjną End-User i kliknij przycisk **dalej**.
6. Wybierz **folder** , aby zainstalować bramę, a następnie kliknij przycisk **dalej**.
7. Na stronie **gotowy do instalacji** kliknij przycisk **Zainstaluj**.
8. Kliknij przycisk **Zakończ** , aby zakończyć instalację.
9. Pobierz klucz z Azure Portal. Instrukcje krok po kroku znajdują się w następnej sekcji.
10. Na stronie **Rejestrowanie bramy** **Zarządzanie danymi bramy Configuration Manager** uruchomionej na maszynie wykonaj następujące czynności:
    1. Wklej klucz w tekście.
    2. Opcjonalnie kliknij pozycję **Pokaż klucz bramy** , aby zobaczyć tekst klucza.
    3. Kliknij pozycję **Zarejestruj**.

### <a name="register-gateway-using-key"></a>Rejestrowanie bramy przy użyciu klucza
#### <a name="if-you-havent-already-created-a-logical-gateway-in-the-portal"></a>Jeśli nie utworzono jeszcze bramy logicznej w portalu
Aby utworzyć bramę w portalu i pobrać klucz ze strony **konfiguracji** , wykonaj kroki opisane w przewodniku [dotyczącym przenoszenia danych między środowiskiem lokalnym i chmurą](data-factory-move-data-between-onprem-and-cloud.md) .

#### <a name="if-you-have-already-created-the-logical-gateway-in-the-portal"></a>Jeśli Brama logiczna została już utworzona w portalu
1. W Azure Portal przejdź do strony **Data Factory** , a następnie kliknij kafelek **połączone usługi** .

    ![Strona Data Factory](media/data-factory-data-management-gateway/data-factory-blade.png)
2. Na stronie **połączone usługi** wybierz **bramę** logiczną utworzoną w portalu.

    ![Brama logiczna](media/data-factory-data-management-gateway/data-factory-select-gateway.png)
3. Na stronie **brama danych** kliknij pozycję **Pobierz i zainstaluj bramę Data Gateway**.

    ![Link do pobierania w portalu](media/data-factory-data-management-gateway/download-and-install-link-on-portal.png)
4. Na stronie **Konfiguracja** kliknij pozycję **Utwórz ponownie klucz**. Kliknij przycisk tak w komunikacie ostrzegawczym po starannym przeczytaniu.

    ![Przycisk ponownego tworzenia klucza](media/data-factory-data-management-gateway/recreate-key-button.png)
5. Kliknij przycisk Kopiuj obok klucza. Klucz jest kopiowany do Schowka.

    ![Kopiowanie klucza](media/data-factory-data-management-gateway/copy-gateway-key.png)

### <a name="system-tray-icons-notifications"></a>Ikony/powiadomienia zasobnika systemowego
Na poniższej ilustracji przedstawiono niektóre z ikon zasobników, które są widoczne.

![ikony paska zadań](./media/data-factory-data-management-gateway/gateway-tray-icons.png)

Po przeniesieniu kursora nad ikoną zasobnika systemowego/komunikat powiadomienia zobaczysz szczegóły dotyczące stanu operacji Brama/aktualizacja w oknie podręcznym.

### <a name="ports-and-firewall"></a>Porty i Zapora
Istnieją dwie zapory, które należy wziąć pod uwagę: **zapora firmowa** uruchomiona na centralnym routerze organizacji, a **Zapora systemu Windows** skonfigurowana jako demon na komputerze lokalnym, na którym zainstalowano bramę.

![zapory](./media/data-factory-data-management-gateway/firewalls2.png)

Na poziomie zapory korporacyjnej wymagane jest skonfigurowanie następujących domen i portów wychodzących:

| Nazwy domen | Porty | Opis |
| --- | --- | --- |
| *.servicebus.windows.net |443 |Używany do komunikacji z zapleczem usługi przenoszenia danych |
| *.core.windows.net |443 |Używane do kopiowania etapowego przy użyciu obiektu blob platformy Azure (jeśli jest skonfigurowany)|
| *.frontend.clouddatahub.net |443 |Używany do komunikacji z zapleczem usługi przenoszenia danych |
| *.servicebus.windows.net |9350-9354, 5671 |Opcjonalny przekaźnik usługi Service Bus za pośrednictwem protokołu TCP używany przez Kreatora kopiowania |

Na poziomie zapory systemu Windows te porty wychodzące są zwykle włączone. W przeciwnym razie można odpowiednio skonfigurować domeny i porty na maszynie bramy.

> [!NOTE]
> 1. W oparciu o źródło/ujścia, może być konieczne zezwolenie na dodatkowe domeny i porty wychodzące w zaporze firmowej/systemie Windows.
> 2. W przypadku niektórych baz danych w chmurze (na przykład: [Azure SQL Database](../../azure-sql/database/firewall-configure.md), [Azure Data Lake](../../data-lake-store/data-lake-store-secure-data.md#set-ip-address-range-for-data-access)itp.) może być konieczne zezwolenie na adres IP maszyny bramy w konfiguracji zapory.
>
>

#### <a name="copy-data-from-a-source-data-store-to-a-sink-data-store"></a>Kopiowanie danych ze źródłowego magazynu danych do magazynu danych ujścia
Upewnij się, że reguły zapory są prawidłowo włączone w zaporze firmowej, zaporze systemu Windows na maszynie bramy i w samym magazynie danych. Włączenie tych reguł umożliwia bramie łączenie się ze źródłem i ujściam pomyślnie. Włącz reguły dla wszystkich magazynów danych, które są związane z operacją kopiowania.

Na przykład, aby skopiować z **lokalnego magazynu danych do ujścia Azure SQL Database lub ujścia usługi Azure Synapse Analytics**, wykonaj następujące czynności:

* Zezwalaj na wychodzącą komunikację **TCP** na porcie **1433** dla zapory systemu Windows i zapory firmowej.
* Skonfiguruj ustawienia zapory dla logicznego programu SQL Server, aby dodać adres IP maszyny bramy do listy dozwolonych adresów IP.

> [!NOTE]
> Jeśli Zapora nie zezwala na port wychodzący 1433, Brama nie może bezpośrednio uzyskać dostępu do usługi Azure SQL. W takim przypadku można użyć [kopii przygotowanej](./data-factory-copy-activity-performance.md#staged-copy) do SQL Database/wystąpienia zarządzanego SQL/DW usługi SQL Azure. W tym scenariuszu do przenoszenia danych jest wymagany tylko protokół HTTPS (port 443).
>
>

### <a name="proxy-server-considerations"></a>Zagadnienia dotyczące serwera proxy
Jeśli korporacyjne środowisko sieciowe używa serwera proxy w celu uzyskania dostępu do Internetu, skonfiguruj bramę zarządzania danymi tak, aby korzystała z odpowiednich ustawień serwera proxy. Serwer proxy można ustawić podczas początkowej fazy rejestracji.

![Ustaw serwer proxy podczas rejestracji](media/data-factory-data-management-gateway/SetProxyDuringRegistration.png)

Brama używa serwera proxy w celu nawiązania połączenia z usługą w chmurze. Kliknij pozycję **Zmień** link podczas początkowej konfiguracji. Zostanie wyświetlone okno dialogowe **Ustawienia serwera proxy** .

![Ustawianie serwera proxy przy użyciu Menedżera konfiguracji 1](media/data-factory-data-management-gateway/SetProxySettings.png)

Dostępne są trzy opcje konfiguracji:

* **Nie używaj serwera proxy**: Brama nie używa jawnie serwera proxy do nawiązywania połączenia z usługami w chmurze.
* **Użyj systemowego serwera proxy**: Brama używa ustawień serwera proxy skonfigurowanych w diahost.exe.config i diawp.exe.config. Jeśli żaden serwer proxy nie jest skonfigurowany w diahost.exe.config i diawp.exe.config, Brama łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy.
* **Użyj niestandardowego serwera proxy**: Skonfiguruj ustawienia serwera proxy HTTP do użycia dla bramy, zamiast używać konfiguracji w diahost.exe.config i diawp.exe.config. Wymagany jest adres i port. Nazwa użytkownika i hasło są opcjonalne w zależności od ustawienia uwierzytelniania serwera proxy. Wszystkie ustawienia są szyfrowane za pomocą certyfikatu poświadczeń bramy i przechowywane lokalnie na komputerze hosta bramy.

Usługa hosta bramy zarządzania danymi jest uruchamiana automatycznie po zapisaniu zaktualizowanych ustawień serwera proxy.

Po pomyślnym zarejestrowaniu bramy, jeśli chcesz wyświetlić lub zaktualizować ustawienia serwera proxy, użyj Zarządzanie danymi Configuration Manager bramy.

1. Uruchom **Configuration Manager bramy zarządzanie danymi**.
2. Przejdź do karty **Ustawienia** .
3. Kliknij pozycję **Zmień** link w sekcji **serwer proxy HTTP** , aby uruchomić okno dialogowe **Ustawianie serwera proxy HTTP** .
4. Po kliknięciu przycisku **dalej** zobaczysz okno dialogowe z ostrzeżeniem z pytaniem, czy masz uprawnienia do zapisania ustawienia serwera proxy i ponownego uruchomienia usługi hosta bramy.

Serwer proxy HTTP można wyświetlić i zaktualizować za pomocą narzędzia Configuration Manager.

![Ustawianie serwera proxy przy użyciu Menedżera konfiguracji 2](media/data-factory-data-management-gateway/SetProxyConfigManager.png)

> [!NOTE]
> W przypadku skonfigurowania serwera proxy z uwierzytelnianiem NTLM usługa hosta bramy działa w ramach konta domeny. Jeśli później zmienisz hasło dla konta domeny, pamiętaj, aby zaktualizować ustawienia konfiguracji usługi i ponownie je uruchomić. Ze względu na ten wymóg zalecamy użycie dedykowanego konta domeny w celu uzyskania dostępu do serwera proxy, który nie wymaga częstego aktualizowania hasła.
>
>

### <a name="configure-proxy-server-settings"></a>Skonfiguruj ustawienia serwera proxy
W przypadku wybrania opcji **Użyj systemowego serwera proxy** dla serwera proxy HTTP brama używa ustawienia proxy w diahost.exe.config i diawp.exe.config. Jeśli w diahost.exe.config i diawp.exe.config nie określono żadnego serwera proxy, Brama łączy się bezpośrednio z usługą w chmurze bez przechodzenia przez serwer proxy. Poniższa procedura zawiera instrukcje dotyczące aktualizowania pliku diahost.exe.config.

1. W Eksploratorze plików wykonaj bezpieczną kopię *C: \\ \\ Program Files \\ Microsoft zarządzanie danymi Gateway \\ 2,0 \\ Shared \\diahost.exe.config* , aby utworzyć kopię zapasową oryginalnego pliku.
2. Uruchom Notepad.exe uruchomiony jako administrator i Otwórz plik tekstowy *C: \\ \\ Program Files \\ Microsoft zarządzanie danymi Gateway \\ 2,0 \\ Shared \\diahost.exe.config*. Znajdziesz tag domyślny dla system.net, jak pokazano w poniższym kodzie:

    ```
    <system.net>
        <defaultProxy useDefaultCredentials="true" />
    </system.net>
    ```

    Następnie można dodać Szczegóły serwera proxy, jak pokazano w następującym przykładzie:

    ```
    <system.net>
        <defaultProxy enabled="true">
            <proxy bypassonlocal="true" proxyaddress="http://proxy.domain.org:8888/" />
        </defaultProxy>
    </system.net>
    ```

    Dodatkowe właściwości są dozwolone wewnątrz tagu proxy, aby określić wymagane ustawienia, takie jak scriptLocation. Zapoznaj się z [elementem proxy (ustawienia sieciowe)](/dotnet/framework/configure-apps/file-schema/network/proxy-element-network-settings) w składni.

    ```
    <proxy autoDetect="true|false|unspecified" bypassonlocal="true|false|unspecified" proxyaddress="uriString" scriptLocation="uriString" usesystemdefault="true|false|unspecified "/>
    ```
3. Zapisz plik konfiguracji w oryginalnej lokalizacji, a następnie uruchom ponownie usługę hosta bramy Zarządzanie danymi, która pobiera zmiany. Aby ponownie uruchomić usługę: użyj apletu usługi w panelu sterowania lub **Configuration Manager zarządzanie danymi bramy** , > kliknij przycisk **Zatrzymaj usługę** , a następnie kliknij przycisk **Uruchom usługę**. Jeśli usługa nie zostanie uruchomiona, prawdopodobnie dodano niepoprawną składnię tagu XML do pliku konfiguracji aplikacji, który był edytowany.

> [!IMPORTANT]
> Nie zapomnij zaktualizować **obu** diahost.exe.config i diawp.exe.config.

Oprócz tych punktów należy również upewnić się, że Microsoft Azure znajduje się na liście dozwolonych w firmie. Listę prawidłowych adresów IP Microsoft Azure można pobrać z [Centrum pobierania Microsoft](https://www.microsoft.com/download/details.aspx?id=41653).

#### <a name="possible-symptoms-for-firewall-and-proxy-server-related-issues"></a>Ewentualne objawy dotyczące problemów z zaporą i serwerem proxy
Jeśli wystąpią błędy podobne do następujących, prawdopodobnie jest to spowodowane nieprawidłową konfiguracją zapory lub serwera proxy, co uniemożliwia bramom łączenie się z Data Factory w celu samodzielnego uwierzytelnienia. Zapoznaj się z poprzednią sekcją, aby upewnić się, że Zapora i serwer proxy zostały prawidłowo skonfigurowane.

1. Podczas próby zarejestrowania bramy pojawia się następujący błąd: "nie można zarejestrować klucza bramy. Przed ponowną próbą zarejestrowania klucza bramy upewnij się, że brama zarządzania danymi jest w stanie połączonym i uruchomiono usługę hosta bramy Zarządzanie danymi. "
2. Gdy otworzysz Configuration Manager, zobaczysz stan jako "odłączony" lub "łączenie". Podczas wyświetlania dzienników zdarzeń systemu Windows w obszarze "Podgląd zdarzeń" > "Dzienniki aplikacji i usług" > "Zarządzanie danymi brama" są wyświetlane komunikaty o błędach, takie jak następujący błąd: `Unable to connect to the remote server`
   `A component of Data Management Gateway has become unresponsive and restarts automatically. Component name: Gateway.`

### <a name="open-port-8050-for-credential-encryption"></a>Otwórz port 8050 na potrzeby szyfrowania poświadczeń
Aplikacja **Ustawienia poświadczeń** używa portu przychodzącego **8050** do przekazywania poświadczeń do bramy po skonfigurowaniu lokalnej usługi połączonej w Azure Portal. Podczas konfigurowania bramy domyślnie instalacja bramy jest otwierana na maszynie bramy.

Jeśli używasz zapory innej firmy, możesz ręcznie otworzyć port 8050. Jeśli podczas instalacji bramy wystąpi problem z zaporą, możesz użyć poniższego polecenia, aby zainstalować bramę bez konfigurowania zapory.

```cmd
msiexec /q /i DataManagementGateway.msi NOFIREWALL=1
```

W przypadku wybrania opcji nie należy otwierać portu 8050 na maszynie bramy należy użyć mechanizmów innych niż ustawienia aplikacji **poświadczenia** w celu skonfigurowania poświadczeń magazynu danych. Można na przykład użyć polecenia cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) programu PowerShell. Zapoznaj się z sekcją Ustawianie poświadczeń i zabezpieczeń w temacie jak można ustawić poświadczenia magazynu danych.

## <a name="update"></a>Aktualizacja
Domyślnie Brama zarządzania danymi jest automatycznie aktualizowana, gdy jest dostępna nowsza wersja bramy. Brama nie jest aktualizowana, dopóki nie zostaną wykonane wszystkie zaplanowane zadania. Żadne dalsze zadania nie są przetwarzane przez bramę do momentu ukończenia operacji aktualizacji. Jeśli aktualizacja nie powiedzie się, Brama zostanie wycofana do starej wersji.

Zaplanowana godzina aktualizacji zostanie wyświetlona w następujących miejscach:

* Strona właściwości bramy w Azure Portal.
* Strona główna Zarządzanie danymi bramy Configuration Manager
* Komunikat z powiadomieniem na pasku zadań.

Na karcie Narzędzia główne Zarządzanie danymi bramy Configuration Manager zostanie wyświetlony harmonogram aktualizacji oraz czas ostatniego zainstalowania/zaktualizowania bramy.

![Aktualizacje harmonogramu](media/data-factory-data-management-gateway/UpdateSection.png)

Możesz zainstalować aktualizację natychmiast lub zaczekać, aż Brama zostanie automatycznie zaktualizowana w zaplanowanym czasie. Na przykład na poniższym obrazie przedstawiono komunikat z powiadomieniem pokazanym w Configuration Manager bramy wraz z przyciskiem Aktualizuj, który można kliknąć, aby natychmiast zainstalować go.

![Aktualizacja w programie DMG Configuration Manager](./media/data-factory-data-management-gateway/gateway-auto-update-config-manager.png)

Komunikat powiadomienia na pasku zadań będzie wyglądał jak pokazano na poniższej ilustracji:

![Komunikat na pasku zadań](./media/data-factory-data-management-gateway/gateway-auto-update-tray-message.png)

Zobaczysz stan operacji aktualizacji (ręczne lub automatyczne) na pasku zadań. Po uruchomieniu Configuration Manager bramy zostanie wyświetlony komunikat na pasku powiadomień, że brama została zaktualizowana wraz z linkiem do [nowości w temacie](data-factory-gateway-release-notes.md).

### <a name="to-disableenable-auto-update-feature"></a>Aby wyłączyć/włączyć funkcję autoaktualizowania
Funkcję autoaktualizacji można wyłączyć lub włączyć, wykonując następujące czynności:

[Dla bramy o pojedynczym węźle]
1. Uruchom program Windows PowerShell na maszynie bramy.
2. Przejdź do folderu *C: \\ \\ Program Files \\ Microsoft Integration Runtime \\ 3,0 \\ PowerShellScript \\* .
3. Uruchom następujące polecenie, aby wyłączyć funkcję autoaktualizacji (Wyłącz).

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off
    ```
4. Aby włączyć ją ponownie:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on
    ```
   [W przypadku bramy o wysokiej dostępności i skalowalności wielowęzłowej](data-factory-data-management-gateway-high-availability-scalability.md)
1. Uruchom program Windows PowerShell na maszynie bramy.
2. Przejdź do folderu *C: \\ \\ Program Files \\ Microsoft Integration Runtime \\ 3,0 \\ PowerShellScript \\* .
3. Uruchom następujące polecenie, aby wyłączyć funkcję autoaktualizacji (Wyłącz).

    W przypadku bramy z funkcją wysokiej dostępności wymagany jest dodatkowy parametr AuthKey.
    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -off -AuthKey <your auth key>
    ```
4. Aby włączyć ją ponownie:

    ```powershell
    .\IntegrationRuntimeAutoUpdateToggle.ps1 -on -AuthKey <your auth key>
    ```

## <a name="configuration-manager"></a>Menedżer konfiguracji
Po zainstalowaniu bramy można uruchomić Zarządzanie danymi Configuration Manager bramy w jeden z następujących sposobów:

1. W oknie **wyszukiwania** wpisz **Zarządzanie danymi Gateway** , aby uzyskać dostęp do tego narzędzia.
2. Uruchom plik wykonywalny *ConfigManager.exe* w folderze: *C: \\ \\ Program Files \\ Microsoft zarządzanie danymi Gateway \\ 2,0 \\ Shared*.

### <a name="home-page"></a>Strona główna
Strona główna umożliwia wykonywanie następujących czynności:

* Wyświetl stan bramy (połączono z usługą w chmurze itp.).
* **Zarejestruj** się przy użyciu klucza z portalu.
* **Zatrzymaj** i uruchom **usługę hosta bramy zarządzanie danymi** na maszynie bramy.
* **Zaplanuj aktualizacje** w określonym czasie dni.
* Wyświetl datę **ostatniej aktualizacji** bramy.

### <a name="settings-page"></a>Strona Ustawienia
Strona ustawienia umożliwia wykonywanie następujących czynności:

* Wyświetlanie, zmienianie i eksportowanie **certyfikatu** używanego przez bramę. Ten certyfikat jest używany do szyfrowania poświadczeń źródła danych.
* Zmień **port HTTPS** dla punktu końcowego. Brama otwiera port do ustawiania poświadczeń źródła danych.
* **Stan** punktu końcowego
* Wyświetl **certyfikat SSL** jest używany na potrzeby komunikacji TLS/SSL między portalem a bramą, aby ustawić poświadczenia dla źródeł danych.

### <a name="remote-access-from-intranet"></a>Dostęp zdalny z intranetu
Ta funkcja zostanie włączona w przyszłości. W nadchodzących aktualizacjach (v 3.4 lub nowszych) zezwolisz na włączenie/wyłączenie wszystkich połączeń zdalnych, które dzisiaj zachodzą przy użyciu portu 8050 (patrz sekcja powyżej) podczas korzystania z programu PowerShell lub aplikacji Menedżera poświadczeń w celu szyfrowania poświadczeń.

### <a name="diagnostics-page"></a>Strona diagnostyki
Strona Diagnostyka umożliwia wykonywanie następujących czynności:

* Włącz pełne **Rejestrowanie**, wyświetl dzienniki w Podglądzie zdarzeń i Wyślij dzienniki do firmy Microsoft, jeśli wystąpił błąd.
* **Test connection** ze źródłem danych.

### <a name="help-page"></a>Strona pomocy
Na stronie Pomoc są wyświetlane następujące informacje:

* Krótki opis bramy
* Numer wersji
* Linki do pomocy online, zasad zachowania poufności informacji i umowy licencyjnej.

## <a name="monitor-gateway-in-the-portal"></a>Monitorowanie bramy w portalu
W Azure Portal można wyświetlić migawkę wykorzystania zasobów (procesor CPU, pamięć, Sieć (WE/wychodzącą), itp.) na maszynie bramy.

1. W Azure Portal przejdź do strony głównej fabryki danych, a następnie kliknij kafelek **połączone usługi** .

    ![Strona główna fabryki danych](./media/data-factory-data-management-gateway/monitor-data-factory-home-page.png)
2. Wybierz **bramę** na stronie **połączone usługi** .

    ![Strona połączone usługi](./media/data-factory-data-management-gateway/monitor-linked-services-blade.png)
3. Na stronie **brama** można zobaczyć użycie pamięci i procesora CPU bramy.

    ![Użycie procesora CPU i pamięci przez bramę](./media/data-factory-data-management-gateway/gateway-simple-monitoring.png)
4. Włącz **Ustawienia zaawansowane** , aby zobaczyć więcej szczegółów, takich jak użycie sieci.
    
    ![Zaawansowane monitorowanie bramy](./media/data-factory-data-management-gateway/gateway-advanced-monitoring.png)

Poniższa tabela zawiera opisy kolumn na liście **węzły bramy** :

Właściwość monitorowania | Opis
:------------------ | :----------
Nazwa | Nazwa bramy logicznej i węzłów skojarzonych z bramą. Węzeł to lokalna maszyna z systemem Windows, na której zainstalowano bramę. Aby uzyskać informacje na temat posiadania więcej niż jednego węzła (do czterech węzłów) w jednej bramie logicznej, zobacz [Zarządzanie danymi Gateway — wysoka dostępność i skalowalność](data-factory-data-management-gateway-high-availability-scalability.md).
Stan | Stan bramy logicznej i węzłów bramy. Przykład: online/offline/Limited/itd. Aby uzyskać informacje o tych Stanach, zobacz sekcję [stan bramy](#gateway-status) .
Wersja | Pokazuje wersję bramy logicznej i każdego węzła bramy. Wersja bramy logicznej jest określana na podstawie wersji większości węzłów w grupie. Jeśli w instalatorze bramy logicznej istnieją węzły z różnymi wersjami, poprawne jest tylko te węzły, które mają ten sam numer wersji, co funkcja bramy logicznej. Inne są w trybie ograniczonym i muszą zostać ręcznie zaktualizowane (tylko w przypadku niepowodzenia aktualizacji w przypadku awarii).
Dostępna pamięć | Dostępna pamięć w węźle bramy. Ta wartość jest migawką niemal w czasie rzeczywistym.
Wykorzystanie procesora | Użycie procesora CPU przez węzeł bramy. Ta wartość jest migawką niemal w czasie rzeczywistym.
Sieć (do/z) | Użycie sieci przez węzeł bramy. Ta wartość jest migawką niemal w czasie rzeczywistym.
Zadania współbieżne (uruchomione/ograniczone) | Liczba zadań lub zadań uruchomionych w każdym węźle. Ta wartość jest migawką niemal w czasie rzeczywistym. Wartość Ogranicz oznacza maksymalne zadania współbieżne dla każdego węzła. Ta wartość jest definiowana w zależności od rozmiaru maszyny. Można zwiększyć limit skalowania w górę współbieżnego wykonywania zadań w zaawansowanych scenariuszach, w przypadku których wykorzystanie procesora CPU/pamięci/sieci jest używane, ale działania mają limit czasu. Ta funkcja jest również dostępna w przypadku bramy z jednym węzłem (nawet wtedy, gdy funkcja skalowalności i dostępności nie jest włączona).
Rola | Istnieją dwa typy ról w ramach wielowęzłowej bramy i procesu roboczego. Wszystkie węzły są pracownikami, co oznacza, że mogą być używane do wykonywania zadań. Istnieje tylko jeden węzeł dyspozytora, który jest używany do ściągania zadań/zadań z usług w chmurze i wysyłania ich do różnych węzłów procesu roboczego (w tym samego siebie).

Na tej stronie zobaczysz niektóre ustawienia, które są bardziej zrozumiałe, gdy w bramie występuje co najmniej dwa węzły (scenariusz skalowania w poziomie). Aby uzyskać szczegółowe informacje na temat konfigurowania bramy wielowęzłowej [, zobacz Zarządzanie danymi Gateway — wysoka dostępność i skalowalność](data-factory-data-management-gateway-high-availability-scalability.md) .

### <a name="gateway-status"></a>Stan bramy
W poniższej tabeli przedstawiono możliwe stany **węzła bramy**:

Stan    | Komentarze/scenariusze
:------- | :------------------
Tryb online | Węzeł połączony z usługą Data Factory.
Tryb offline | Węzeł jest w trybie offline.
Uaktualnianie | Węzeł jest aktualizowany w sposób autouzupełniania.
Ograniczone | Z powodu problemu z łącznością. Może to być spowodowane problemem z portem HTTP 8050, problemem z łącznością usługi Service Bus lub problemem z synchronizacją poświadczeń.
Nieaktywne | Węzeł jest w konfiguracji innej niż Konfiguracja innych węzłów większości.<br/><br/> Węzeł może być nieaktywny, jeśli nie może połączyć się z innymi węzłami.

W poniższej tabeli przedstawiono możliwe stany **bramy logicznej**. Stan bramy zależy od stanu węzłów bramy.

Stan | Komentarze
:----- | :-------
Wymaga rejestracji | Żaden węzeł nie jest jeszcze zarejestrowany w tej bramie logicznej
Tryb online | Węzły bramy są w trybie online
Tryb offline | Brak węzła w stanie online.
Ograniczone | Nie wszystkie węzły w tej bramie są w dobrej kondycji. Ten stan jest ostrzeżeniem, że niektóre węzły mogą być wyłączone. <br/><br/>Może to być spowodowane problemem z synchronizacją poświadczeń w węźle dyspozytora/proces roboczy.

## <a name="scale-up-gateway"></a>Skalowanie bramy w górę
Można skonfigurować liczbę **współbieżnych zadań przenoszenia danych** , które mogą być uruchamiane w węźle w celu skalowania w górę możliwości przenoszenia danych między lokalnymi i magazynami danych w chmurze.

Gdy dostępna pamięć i procesor CPU nie są używane prawidłowo, ale bezczynna wydajność jest równa 0, należy skalować ją w górę, zwiększając liczbę współbieżnych zadań, które mogą być uruchamiane w węźle. Można również skalować w górę, gdy działania są przekroczenia limitu czasu, ponieważ Brama jest przeciążona. W obszarze Ustawienia zaawansowane węzła bramy można zwiększyć maksymalną pojemność węzła.

## <a name="troubleshooting-gateway-issues"></a>Rozwiązywanie problemów z bramą
Zobacz artykuł [Rozwiązywanie problemów z bramą](data-factory-troubleshoot-gateway-issues.md) , aby uzyskać informacje/porady dotyczące rozwiązywania problemów z używaniem bramy zarządzania danymi.

## <a name="move-gateway-from-one-machine-to-another"></a>Przenoszenie bramy z jednej maszyny do innej
Ta sekcja zawiera instrukcje dotyczące przeniesienia klienta bramy z jednego komputera na inny.

1. W portalu przejdź do **strony głównej Data Factory** i kliknij kafelek **połączone usługi** .

    ![Link do bram danych](./media/data-factory-data-management-gateway/DataGatewaysLink.png)
2. Wybierz bramę w sekcji **bramy danych** na stronie **połączone usługi** .

    ![Strona połączone usługi z wybraną bramą](./media/data-factory-data-management-gateway/LinkedServiceBladeWithGateway.png)
3. Na stronie **brama danych** kliknij pozycję **Pobierz i zainstaluj bramę Data Gateway**.

    ![Link do pobierania bramy](./media/data-factory-data-management-gateway/DownloadGatewayLink.png)
4. Na stronie **Konfiguracja** kliknij pozycję **Pobierz i zainstaluj bramę Data Gateway**, a następnie postępuj zgodnie z instrukcjami, aby zainstalować bramę danych na komputerze.

    ![Konfiguruj stronę](./media/data-factory-data-management-gateway/ConfigureBlade.png)
5. Pozostaw otwarte **Configuration Manager Microsoft zarządzanie danymi Gateway** .

    ![Menedżer konfiguracji](./media/data-factory-data-management-gateway/ConfigurationManager.png)
6. Na stronie **Konfigurowanie** w portalu kliknij pozycję **Utwórz ponownie klucz** na pasku poleceń, a następnie kliknij przycisk **tak** dla komunikatu ostrzegawczego. Kliknij przycisk **Kopiuj** obok tekstu klucza, który kopiuje klucz do Schowka. Brama na starym komputerze przestaje działać po ponownym utworzeniu klucza.

    ![Utwórz ponownie klucz 2](./media/data-factory-data-management-gateway/RecreateKey.png)
7. Wklej **klucz** do pola tekstowego na stronie **rejestracja bramy** **Zarządzanie danymi Configuration Manager bramy** na komputerze. obowiązkowe Kliknij pole wyboru **Pokaż klucz bramy** , aby wyświetlić tekst klucza.

    ![Kopiuj klucz i zarejestruj](./media/data-factory-data-management-gateway/CopyKeyAndRegister.png)
8. Kliknij pozycję **zarejestruj** , aby zarejestrować bramę w usłudze w chmurze.
9. Na karcie **Ustawienia** kliknij pozycję **Zmień** , aby wybrać ten sam certyfikat, który był używany w przypadku starej bramy, wprowadź **hasło** i kliknij przycisk **Zakończ**.

   ![Określ certyfikat](./media/data-factory-data-management-gateway/SpecifyCertificate.png)

   Certyfikat ze starej bramy można wyeksportować, wykonując następujące czynności: Uruchom Zarządzanie danymi bramę Configuration Manager na starym komputerze, przejdź do karty **certyfikat** , kliknij przycisk **Eksportuj** i postępuj zgodnie z instrukcjami.
10. Po pomyślnej rejestracji bramy powinien zostać wyświetlony zestaw **rejestracji** , który ma być **zarejestrowany** , i stan **ustawiony na na** stronie głównej Configuration Manager bramy. 

## <a name="encrypting-credentials"></a>Szyfrowanie poświadczeń
Aby zaszyfrować poświadczenia w edytorze Data Factory, wykonaj następujące czynności:

1. Uruchom przeglądarkę sieci Web na **maszynie bramy**, przejdź do [Azure Portal](https://portal.azure.com). W razie potrzeby Wyszukaj fabrykę danych, Otwórz pozycję Fabryka danych na stronie **Fabryka danych** , a następnie kliknij pozycję **Utwórz & Wdróż** , aby uruchomić Edytor Data Factory.
2. Kliknij istniejącą **połączoną usługę** w widoku drzewa, aby wyświetlić jej definicję JSON, lub Utwórz połączoną usługę, która wymaga bramy zarządzania danymi (na przykład: SQL Server lub Oracle).
3. W edytorze JSON dla właściwości **gatewayname** wprowadź nazwę bramy.
4. Wprowadź nazwę serwera dla właściwości **źródła danych** w **elemencie ConnectionString**.
5. Wprowadź nazwę bazy danych dla właściwości **katalogu początkowego** w **elemencie ConnectionString**.
6. Kliknij przycisk **Szyfruj** na pasku poleceń, który uruchamia aplikację **Menedżer poświadczeń** dwukrotnego kliknięcia. Powinno zostać wyświetlone okno dialogowe **Ustawienia poświadczeń** .

    ![Okno dialogowe poświadczeń ustawienia](./media/data-factory-data-management-gateway/setting-credentials-dialog.png)
7. W oknie dialogowym **Ustawianie poświadczeń** wykonaj następujące czynności:
   1. Wybierz pozycję **uwierzytelnianie** , której usługa Data Factory ma używać do łączenia się z bazą danych.
   2. Wprowadź nazwę użytkownika, który ma dostęp do bazy danych dla ustawienia **nazwy użytkownika** .
   3. Wprowadź hasło dla użytkownika dotyczące ustawienia **hasła** .
   4. Kliknij przycisk **OK** , aby zaszyfrować poświadczenia i zamknąć okno dialogowe.
8. Właściwość **encryptedCredential** powinna teraz zostać wyświetlona w **elemencie ConnectionString** .

    ```JSON
    {
        "name": "SqlServerLinkedService",
        "properties": {
            "type": "OnPremisesSqlServer",
            "description": "",
            "typeProperties": {
                "connectionString": "data source=myserver;initial catalog=mydatabase;Integrated Security=False;EncryptedCredential=eyJDb25uZWN0aW9uU3R",
                "gatewayName": "adftutorialgateway"
            }
        }
    }
    ```
   W przypadku uzyskania dostępu do portalu z komputera, który jest inny niż maszyna bramy, należy się upewnić, że aplikacja Menedżera poświadczeń może nawiązać połączenie z maszyną bramy. Jeśli aplikacja nie może nawiązać połączenia z maszyną bramy, nie pozwala na ustawienie poświadczeń dla źródła danych i przetestowanie połączenia ze źródłem danych.

W przypadku korzystania z aplikacji **Ustawienia poświadczeń** Portal szyfruje poświadczenia za pomocą certyfikatu określonego na karcie **certyfikat** w **Configuration Manager bramy** na maszynie bramy.

Jeśli szukasz podejścia opartego na interfejsie API na potrzeby szyfrowania poświadczeń, możesz użyć polecenia cmdlet [New-AzDataFactoryEncryptValue](/powershell/module/az.datafactory/new-azdatafactoryencryptvalue) programu PowerShell, aby zaszyfrować poświadczenia. Polecenie cmdlet używa certyfikatu, który jest skonfigurowany do szyfrowania poświadczeń przez bramę. Zaszyfrowane poświadczenia są dodawane do elementu **EncryptedCredential** obiektu **CONNECTIONSTRING** w formacie JSON. Plik JSON jest używany z poleceniem cmdlet [New-AzDataFactoryLinkedService](/powershell/module/az.datafactory/new-azdatafactorylinkedservice) lub edytorem Data Factory.

```JSON
"connectionString": "Data Source=<servername>;Initial Catalog=<databasename>;Integrated Security=True;EncryptedCredential=<encrypted credential>",
```

Istnieje jeszcze jedno podejście do ustawiania poświadczeń przy użyciu edytora Data Factory. Jeśli utworzysz połączoną usługę SQL Server przy użyciu edytora i wprowadzisz poświadczenia w postaci zwykłego tekstu, poświadczenia są szyfrowane przy użyciu certyfikatu, który jest członkiem usługi Data Factory. Nie używa certyfikatu, który jest skonfigurowany do użycia przez bramę. Chociaż takie podejście może być nieco szybsze w niektórych przypadkach, jest mniej bezpieczne. Dlatego zalecamy wykonanie tego podejścia tylko w celach deweloperskich/testowych.

## <a name="powershell-cmdlets"></a>Polecenia cmdlet programu PowerShell
W tej sekcji opisano sposób tworzenia i rejestrowania bramy przy użyciu poleceń cmdlet Azure PowerShell.

1. Uruchom **Azure PowerShell** w trybie administratora.
2. Zaloguj się do swojego konta platformy Azure, uruchamiając następujące polecenie i wprowadzając poświadczenia platformy Azure.

    ```powershell
    Connect-AzAccount
    ```
3. Użyj polecenia cmdlet **New-AzDataFactoryGateway** , aby utworzyć bramę logiczną w następujący sposób:

    ```powershell
    $MyDMG = New-AzDataFactoryGateway -Name <gatewayName> -DataFactoryName <dataFactoryName> -ResourceGroupName ADF –Description <desc>
    ```
    **Przykładowe polecenie i dane wyjściowe**:

    ```
    PS C:\> $MyDMG = New-AzDataFactoryGateway -Name MyGateway -DataFactoryName $df -ResourceGroupName ADF –Description "gateway for walkthrough"

    Name              : MyGateway
    Description       : gateway for walkthrough
    Version           :
    Status            : NeedRegistration
    VersionStatus     : None
    CreateTime        : 9/28/2014 10:58:22
    RegisterTime      :
    LastConnectTime   :
    ExpiryTime        :
    ProvisioningState : Succeeded
    Key               : ADF#00000000-0000-4fb8-a867-947877aef6cb@fda06d87-f446-43b1-9485-78af26b8bab0@4707262b-dc25-4fe5-881c-c8a7c3c569fe@wu#nfU4aBlq/heRyYFZ2Xt/CD+7i73PEO521Sj2AFOCmiI
    ```

1. W Azure PowerShell przejdź do folderu: *C: \\ \\ Program Files \\ Microsoft Integration Runtime \\ 3,0 \\ PowerShellScript \\*. Uruchom *RegisterGateway.ps1* skojarzone ze zmienną lokalną **$Key** , jak pokazano w poniższym poleceniu. Ten skrypt rejestruje agenta klienta zainstalowanego na maszynie przy użyciu utworzonej wcześniej bramy logicznej.

    ```powershell
    PS C:\> .\RegisterGateway.ps1 $MyDMG.Key
    ```
    ```
    Agent registration is successful!
    ```
    Możesz zarejestrować bramę na maszynie zdalnej przy użyciu parametru IsRegisterOnRemoteMachine. Przykład:

    ```powershell
    .\RegisterGateway.ps1 $MyDMG.Key -IsRegisterOnRemoteMachine true
    ```
2. Możesz użyć polecenia cmdlet **Get-AzDataFactoryGateway** , aby uzyskać listę bram w fabryce danych. Gdy **stan** zostanie wyświetlony w **trybie online**, oznacza to, że brama jest gotowa do użycia.

    ```powershell        
    Get-AzDataFactoryGateway -DataFactoryName <dataFactoryName> -ResourceGroupName ADF
    ```
   Bramę można usunąć za pomocą polecenia cmdlet **Remove-AzDataFactoryGateway** i zaktualizować opis bramy przy użyciu poleceń cmdlet **Set-AzDataFactoryGateway** . Aby poznać składnię i inne szczegółowe informacje o tych poleceniach cmdlet, zobacz Data Factory dokumentacja poleceń cmdlet.  

### <a name="list-gateways-using-powershell"></a>Wyświetlanie listy bram przy użyciu programu PowerShell

```powershell
Get-AzDataFactoryGateway -DataFactoryName jasoncopyusingstoredprocedure -ResourceGroupName ADF_ResourceGroup
```

### <a name="remove-gateway-using-powershell"></a>Usuwanie bramy przy użyciu programu PowerShell

```powershell
Remove-AzDataFactoryGateway -Name JasonHDMG_byPSRemote -ResourceGroupName ADF_ResourceGroup -DataFactoryName jasoncopyusingstoredprocedure -Force
```

## <a name="next-steps"></a>Następne kroki
* Zobacz [przenoszenie danych między środowiskiem lokalnym i magazynem danych w chmurze](data-factory-move-data-between-onprem-and-cloud.md) . W tym przewodniku utworzysz potok, który używa bramy do przenoszenia danych z bazy danych SQL Server do obiektu blob platformy Azure.