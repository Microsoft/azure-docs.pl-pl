---
title: Pracuj z istniejącymi lokalnymi serwerami proxy i Azure Active Directory
description: Obejmuje sposób pracy z istniejącymi lokalnymi serwerami proxy z Azure Active Directory.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 04/07/2020
ms.author: kenwith
ms.reviewer: japere
ms.custom: contperf-fy21q2
ms.openlocfilehash: b6ecea10c884bffb10c936ca7c619d70d2449467
ms.sourcegitcommit: 78ecfbc831405e8d0f932c9aafcdf59589f81978
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 01/23/2021
ms.locfileid: "98728495"
---
# <a name="work-with-existing-on-premises-proxy-servers"></a>Praca z istniejącymi lokalnymi serwerami proxy

W tym artykule opisano sposób konfigurowania łączników serwera proxy aplikacji Azure Active Directory (Azure AD) do pracy z wychodzącymi serwerami proxy. Ten artykuł jest przeznaczony dla klientów mających serwery proxy w swoich środowiskach sieciowych.

Zacznijmy od przejrzenia następujących głównych scenariuszy wdrażania:

* Skonfiguruj łączniki, aby pominąć lokalne wychodzące serwery proxy.
* Skonfiguruj łączniki do korzystania z wychodzącego serwera proxy w celu uzyskania dostępu do usługi Azure serwer proxy aplikacji usługi Azure AD.
* Skonfiguruj użycie serwera proxy między łącznikiem a aplikacją zaplecza.

Aby uzyskać więcej informacji na temat działania łączników, zobacz [Understand Azure AD Application Proxy connectors (Omówienie łączników serwerów proxy aplikacji usługi Azure AD)](application-proxy-connectors.md).

## <a name="bypass-outbound-proxies"></a>Pomijanie serwerów proxy ruchu wychodzącego

Łączniki mają bazowe składniki systemu operacyjnego, które wysyłają żądania wychodzące. Te składniki automatycznie podejmują próby zlokalizowania serwera proxy w sieci przy użyciu usługi autowykrywania serwera proxy w sieci (WPAD).

Składniki systemu operacyjnego próbują zlokalizować serwer proxy, przeprowadzając wyszukiwania DNS dla domeny wpad.domainsuffix. Jeśli wyszukiwanie zostanie rozwiązane w systemie DNS, wysyłane jest żądanie HTTP na adres IP domeny wpad.dat. To żądanie staje się skryptem konfiguracji serwera proxy w danym środowisku. Łącznik używa tego skryptu w celu wybrania serwera proxy ruchu wychodzącego. Jednak ruch łącznika może nadal nie być przepuszczany z powodu braku dodatkowych ustawień konfiguracji wymaganych na tym serwerze proxy.

Łącznik można skonfigurować w taki sposób, aby pomijał lokalny serwer proxy w celu zapewnienia korzystania z bezpośredniej łączności z usługami platformy Azure. Zalecamy takie podejście, jeśli tylko zezwalają na to zasady sieci, ponieważ w ten sposób zostanie zmniejszona o jedną liczba obsługiwanych konfiguracji.

Aby wyłączyć użycie wychodzącego serwera proxy dla łącznika, edytuj plik Connector\ApplicationProxyConnectorService.exe.config proxy aplikacji usługi AAD i Dodaj sekcję *System.NET* pokazaną w tym przykładzie kodu:

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>
    <defaultProxy enabled="false"></defaultProxy>
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Aby upewnić się, że usługa Aktualizator łączników również pomija serwer proxy, wprowadź podobną zmianę do pliku ApplicationProxyConnectorUpdaterService.exe.config. Ten plik znajduje się w folderze C:\Program Files\Microsoft AAD App proxy Aktualizator łączników.

Pamiętaj o utworzeniu kopii oryginalnych plików. W ten sposób będzie można przywrócić domyślne pliki .config, jeśli zajdzie taka potrzeba.

## <a name="use-the-outbound-proxy-server"></a>Korzystanie z serwera proxy wychodzącego

Niektóre środowiska wymagają, aby cały ruch wychodzący przechodził przez wychodzący serwer proxy, bez wyjątku. W związku z tym pomijanie serwera proxy nie jest opcją.

Ruch łącznika można skonfigurować tak, aby przechodził przez wychodzący serwer proxy, jak pokazano na poniższym diagramie:

 ![Konfigurowanie ruchu łącznika w celu przechodzenia przez wychodzący serwer proxy do usługi Azure serwer proxy aplikacji usługi Azure AD](./media/application-proxy-configure-connectors-with-proxy-servers/configure-proxy-settings.png)

W wyniku posiadania tylko ruchu wychodzącego nie ma potrzeby konfigurowania dostępu przychodzącego za pomocą zapór.

> [!NOTE]
> Serwer proxy aplikacji nie obsługuje uwierzytelniania do innych serwerów proxy. Konta usług sieciowych łączników/Aktualizator powinny mieć możliwość łączenia się z serwerem proxy bez konieczności uwierzytelniania.

### <a name="step-1-configure-the-connector-and-related-services-to-go-through-the-outbound-proxy"></a>Krok 1. Konfigurowanie łącznika i powiązanych usług w celu przechodzenia przez wychodzący serwer proxy

Jeśli funkcja WPAD jest włączona w środowisku i odpowiednio skonfigurowana, łącznik automatycznie odnajdzie wychodzący serwer proxy i spróbuje go użyć. Można jednak jawnie skonfigurować łącznik, aby przechodzić przez wychodzący serwer proxy.

Aby to zrobić, należy edytować plik Connector\ApplicationProxyConnectorService.exe.config proxy aplikacji usługi AAD i dodać sekcję *System.NET* pokazaną w tym przykładzie kodu. Zmień *ProxyServer: 8080* , aby odzwierciedlała nazwę lokalnego serwera proxy lub adres IP oraz port, na którym nasłuchuje. Wartość musi mieć prefiks http://, nawet jeśli jest używany adres IP.

```xml
<?xml version="1.0" encoding="utf-8" ?>
<configuration>
  <system.net>  
    <defaultProxy>   
      <proxy proxyaddress="http://proxyserver:8080" bypassonlocal="True" usesystemdefault="True"/>   
    </defaultProxy>  
  </system.net>
  <runtime>
    <gcServer enabled="true"/>
  </runtime>
  <appSettings>
    <add key="TraceFilename" value="AadAppProxyConnector.log" />
  </appSettings>
</configuration>
```

Następnie skonfiguruj usługę Aktualizator łączników, aby korzystała z serwera proxy, wprowadzając podobną zmianę do pliku Updater\ApplicationProxyConnectorUpdaterService.exe.config łącznika serwera proxy aplikacji usługi AAD.

### <a name="step-2-configure-the-proxy-to-allow-traffic-from-the-connector-and-related-services-to-flow-through"></a>Krok 2. Konfigurowanie serwera proxy w celu zezwalania na przepływ ruchu z łącznika i powiązanych usług

Istnieje cztery aspekty, które należy wziąć pod uwagę na wychodzącym serwerze proxy:

* Reguły ruchu wychodzącego serwera proxy
* Uwierzytelnianie serwera proxy
* Porty serwera proxy
* Inspekcja protokołu TLS

#### <a name="proxy-outbound-rules"></a>Reguły ruchu wychodzącego serwera proxy

Zezwól na dostęp do następujących adresów URL:

| Adres URL | Port |  Zastosowanie |
| --- | --- | --- |
| &ast;.msappproxy.net<br>&ast;.servicebus.windows.net | 443/HTTPS | Komunikacja między łącznikiem a usługą serwera proxy aplikacji w chmurze |
| crl3.digicert.com<br>crl4.digicert.com<br>ocsp.digicert.com<br>crl.microsoft.com<br>oneocsp.microsoft.com<br>ocsp.msocsp.com<br> | 80/HTTP | Łącznik używa tych adresów URL do weryfikowania certyfikatów. |
| login.windows.net<br>secure.aadcdn.microsoftonline-p.com<br>&ast;.microsoftonline.com<br>&ast;. microsoftonline-p.com<br>&ast;. msauth.net<br>&ast;. msauthimages.net<br>&ast;. msecnd.net<br>&ast;. msftauth.net<br>&ast;. msftauthimages.net<br>&ast;. phonefactor.net<br>enterpriseregistration.windows.net<br>management.azure.com<br>policykeyservice.dc.ad.msft.net<br>ctldl.windowsupdate.com | 443/HTTPS | Łącznik używa tych adresów URL podczas procesu rejestracji. |
| ctldl.windowsupdate.com | 80/HTTP | Łącznik używa tego adresu URL podczas procesu rejestracji. |

Jeśli zapora lub serwer proxy umożliwia skonfigurowanie list dozwolonych DNS, można zezwolić na połączenia z \* . msappproxy.NET i \* . ServiceBus.Windows.NET.

Jeśli nie możesz zezwolić na połączenie przy użyciu nazwy FQDN i należy określić zakresy adresów IP, użyj następujących opcji:

* Zezwalaj na dostęp wychodzący łącznika do wszystkich miejsc docelowych.
* Zezwalaj na dostęp wychodzący łącznika do wszystkich zakresów adresów IP centrum danych platformy Azure. Wezwanie do korzystania z listy zakresów adresów IP centrum danych platformy Azure jest aktualizowane co tydzień. Należy umieścić proces, aby upewnić się, że reguły dostępu są odpowiednio zaktualizowane. Tylko podzbiór adresów IP może spowodować przerwanie konfiguracji. Aby pobrać najnowsze zakresy adresów IP centrum danych platformy Azure, przejdź do obszaru [https://download.microsoft.com](https://download.microsoft.com) i wyszukaj ciąg "zakresy adresów IP platformy Azure i Tagi usług". Upewnij się, że wybrano odpowiednią chmurę. Na przykład zakresy adresów IP chmury publicznej można znaleźć w obszarze "zakresy adresów IP platformy Azure i Tagi usług — chmura publiczna". Chmurę dla instytucji rządowych Stanów Zjednoczonych można znaleźć, wyszukując frazę "zakresy adresów IP platformy Azure i Tagi usług — chmura dla instytucji rządowych USA".

#### <a name="proxy-authentication"></a>Uwierzytelnianie serwera proxy

Uwierzytelnianie serwera proxy nie jest obecnie obsługiwane. Nasze bieżące zalecenia polegają na umożliwieniu anonimowego dostępu do Internetu do miejsc docelowych.

#### <a name="proxy-ports"></a>Porty serwera proxy

Łącznik wykonuje wychodzące połączenia TLS przy użyciu metody CONNECT. Ta metoda zasadniczo konfiguruje tunel za pomocą wychodzącego serwera proxy. Skonfiguruj serwer proxy w taki sposób, aby zezwalał na tunelowanie do portów 443 i 80.

> [!NOTE]
> Gdy Service Bus jest uruchamiany za pośrednictwem protokołu HTTPS, korzysta z portu 443. Jednak domyślnie Service Bus próbuje kierować połączenia protokołu TCP i wraca do protokołu HTTPS tylko w przypadku niepowodzenia łączności bezpośredniej.

#### <a name="tls-inspection"></a>Inspekcja protokołu TLS

Nie należy używać inspekcji protokołu TLS dla ruchu łącznika, ponieważ powoduje to problemy związane z ruchem łącznika. Łącznik używa certyfikatu do uwierzytelniania w usłudze serwera proxy aplikacji i ten certyfikat może zostać utracony podczas inspekcji protokołu TLS.

## <a name="configure-using-a-proxy-between-the-connector-and-backend-application"></a>Konfigurowanie używania serwera proxy między łącznikiem a aplikacją zaplecza
Użycie serwera proxy przesyłania dalej do komunikacji w ramach aplikacji zaplecza może być specjalnym wymaganiem w niektórych środowiskach.
Aby je włączyć, wykonaj następujące czynności:

### <a name="step-1-add-the-required-registry-value-to-the-server"></a>Krok 1. Dodawanie wymaganej wartości rejestru do serwera
1. Aby włączyć korzystanie z domyślnego serwera proxy, Dodaj następującą wartość rejestru (DWORD) `UseDefaultProxyForBackendRequests = 1` do klucza rejestru konfiguracji łącznika znajdującego się w lokalizacji "HKEY_LOCAL_MACHINE\Software\Microsoft\Microsoft AAD App Proxy Connector".

### <a name="step-2-configure-the-proxy-server-manually-using-netsh-command"></a>Krok 2. Konfigurowanie serwera proxy ręcznie przy użyciu polecenia netsh
1.  Włącz ustawienia zasad grupy Ustaw serwer proxy dla komputera. Ten temat znajduje się w temacie: Konfiguracja komputera \ składniki systemu Windows\internet Explorer. Należy to skonfigurować zamiast mieć te zasady ustawione na użytkownika.
2.  Uruchom `gpupdate /force` na serwerze lub ponownie uruchom serwer, aby upewnić się, że używa zaktualizowanych ustawień zasad grupy.
3.  Uruchom wiersz polecenia z podwyższonym poziomem uprawnień z uprawnieniami administratora i wprowadź `control inetcpl.cpl` .
4.  Skonfiguruj wymagane ustawienia serwera proxy. 

Te ustawienia powodują, że łącznik używa tego samego serwera proxy przesyłania dalej do komunikacji z platformą Azure i aplikacji zaplecza. Jeśli Łącznik do komunikacji z platformą Azure nie wymaga serwera proxy przesyłania dalej lub innego serwera proxy do przesyłania dalej, można skonfigurować ten program przy użyciu opcji modyfikowania plików ApplicationProxyConnectorService.exe.config zgodnie z opisem w sekcji Pomijaj wychodzące serwery proxy lub użyć serwera wychodzącego.

> [!NOTE]
> Istnieją różne sposoby konfigurowania internetowego serwera proxy w systemie operacyjnym. Ustawienia serwera proxy skonfigurowane za pośrednictwem narzędzia NETSH WINHTTP (Uruchom `NETSH WINHTTP SHOW PROXY` do sprawdzenia) Zastąp ustawienia serwera proxy skonfigurowane w kroku 2. 

Usługa łącznika Aktualizator będzie również używać serwera proxy maszyny. To zachowanie można zmienić, modyfikując plik ApplicationProxyConnectorUpdaterService.exe.config.

## <a name="troubleshoot-connector-proxy-problems-and-service-connectivity-issues"></a>Rozwiązywanie problemów z serwerem proxy łącznika i problemów z łącznością usług

Teraz powinien zostać wyświetlony cały ruch przepływający przez serwer proxy. Jeśli masz problemy, następujące informacje dotyczące rozwiązywania problemów powinny pomóc.

Najlepszym sposobem identyfikowania i rozwiązywania problemów z łącznością łącznika jest przechwycenie sieci podczas uruchamiania usługi łącznika. Poniżej znajdują się krótkie porady dotyczące przechwytywania i filtrowania śladów sieci.

Możesz użyć wybranego narzędzia do monitorowania. Na potrzeby tego artykułu korzystamy z programu Microsoft Message Analyzer.

> [!NOTE]
> [Program Microsoft Message Analyzer (MMA) został wycofany](/openspecs/blog/ms-winintbloglp/dd98b93c-0a75-4eb0-b92e-e760c502394f) , a jego pakiety pobierania zostały usunięte z witryn Microsoft.com w listopadzie 25 2019.  Obecnie nie ma żadnych zastąpień firmy Microsoft dla programu Microsoft Message Analyzer w trakcie programowania.  Aby uzyskać podobną funkcjonalność, należy rozważyć użycie narzędzia Analizator protokołu sieciowego innej firmy, takiego jak Wireshark.

Poniższe przykłady są specyficzne dla analizatora komunikatów, ale zasady można zastosować do dowolnego narzędzia do analizy.

### <a name="take-a-capture-of-connector-traffic"></a>Przechwyć ruch łącznika

W celu wstępnego rozwiązywania problemów wykonaj następujące czynności:

1. Z poziomu Services. msc Zatrzymaj usługę Azure serwer proxy aplikacji usługi Azure AD Connector.

   ![Usługa łącznika usługi Azure serwer proxy aplikacji usługi Azure AD w usługach Services. msc](./media/application-proxy-configure-connectors-with-proxy-servers/services-local.png)

1. Uruchom analizatora komunikatów jako administrator.
1. Wybierz pozycję **Rozpocznij śledzenie lokalne**.
1. Uruchom usługę łącznika usługi Azure serwer proxy aplikacji usługi Azure AD.
1. Zatrzymaj przechwytywanie sieci.

   ![Zrzut ekranu przedstawia przycisk Zatrzymaj przechwytywanie sieci](./media/application-proxy-configure-connectors-with-proxy-servers/stop-trace.png)

### <a name="check-if-the-connector-traffic-bypasses-outbound-proxies"></a>Sprawdź, czy ruch łącznika pomija wychodzące serwery proxy

W przypadku skonfigurowania łącznika serwera proxy aplikacji w celu obejścia serwerów proxy i nawiązania bezpośredniego połączenia z usługą serwera proxy aplikacji należy wyszukać funkcję przechwytywania sieci pod kątem nieudanych prób nawiązania połączenia TCP.

Użyj filtru analizatora komunikatów, aby zidentyfikować te próby. Wprowadź `property.TCPSynRetransmit` wartość w polu Filtr i wybierz pozycję **Zastosuj**.

Pakiet SYN to pierwszy pakiet wysyłany w celu nawiązania połączenia TCP. Jeśli ten pakiet nie zwraca odpowiedzi, zostanie podjęta ponowna próba wykonania operacji SYN. Możesz użyć poprzedniego filtru, aby zobaczyć wszystkie reprzesłane SYNs. Następnie można sprawdzić, czy te SYNs odpowiadają jakimkolwiek ruchowi związanym z łącznikiem.

Jeśli oczekujesz, że łącznik ma kierować bezpośrednie połączenia z usługami platformy Azure, odpowiedzi SynRetransmit na porcie 443 są oznaką problemu z siecią lub zaporą.

### <a name="check-if-the-connector-traffic-uses-outbound-proxies"></a>Sprawdź, czy ruch łącznika używa wychodzących serwerów proxy

Jeśli skonfigurowano ruch łącznika serwera proxy aplikacji w celu przechodzenia przez serwery proxy, należy szukać niezakończonych połączeń HTTPS z serwerem proxy.

Aby odfiltrować przechwytywanie sieci dla tych prób połączenia, wprowadź `(https.Request or https.Response) and tcp.port==8080` w filtr analizatora komunikatów, zastępując 8080 z portem usługi serwera proxy. Wybierz pozycję **Zastosuj** , aby wyświetlić wyniki filtrowania.

Poprzedni filtr przedstawia tylko żądania HTTPs i odpowiedzi do/z portu serwera proxy. Szukasz żądań połączeń, które pokazują komunikację z serwerem proxy. Po pomyślnym otrzymaniu odpowiedzi HTTP OK (200).

Jeśli zobaczysz inne kody odpowiedzi, na przykład 407 lub 502, oznacza to, że serwer proxy wymaga uwierzytelniania lub nie zezwala na ruch z innego powodu. W tym momencie należy skontaktować się z zespołem pomocy technicznej serwera proxy.

## <a name="next-steps"></a>Następne kroki

* [Omówienie łączników serwer proxy aplikacji usługi Azure AD platformy Azure](application-proxy-connectors.md)
* Jeśli masz problemy z łącznością łącznika, poprosimy o podanie pytania na [stronie pytania firmy&Microsoft dotyczącym Azure Active Directory](/answers/topics/azure-active-directory.html) lub utworzenia biletu z zespołem pomocy technicznej.