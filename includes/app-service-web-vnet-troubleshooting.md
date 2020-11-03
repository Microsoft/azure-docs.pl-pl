---
author: ccompy
ms.service: app-service-web
ms.topic: include
ms.date: 02/27/2020
ms.author: ccompy
ms.openlocfilehash: cec44bbabdb7d528c30a8d3396b819f2eb3c5386
ms.sourcegitcommit: bbd66b477d0c8cb9adf967606a2df97176f6460b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93235892"
---
Ta funkcja jest łatwa do skonfigurowania, ale nie oznacza to, że Twoje doświadczenie będzie bezpłatne. Jeśli wystąpią problemy z uzyskaniem dostępu do żądanego punktu końcowego, istnieją pewne narzędzia, których można użyć do testowania łączności z poziomu konsoli aplikacji. Istnieją dwie konsole, których można użyć. Jedna z nich jest konsolą kudu, a druga jest konsolą w Azure Portal. Aby nawiązać połączenie z konsolą kudu z poziomu aplikacji, przejdź do pozycji **Narzędzia**  >  **kudu**. Możesz również uzyskać dostęp do konsoli Kudo na stronie [sitename]. SCM. azurewebsites. NET. Po załadowaniu witryny sieci Web przejdź do karty **konsola debugowania** . Aby uzyskać dostęp do konsoli obsługiwanej przez Azure Portal z poziomu aplikacji, przejdź **Tools** do  >  **konsoli** narzędzia.

#### <a name="tools"></a>Narzędzia
W natywnych aplikacjach systemu Windows narzędzia **ping** , **nslookup** i **tracert** nie działają przez konsolę ze względu na ograniczenia zabezpieczeń (działają w [niestandardowych kontenerach systemu Windows](../articles/app-service/quickstart-custom-container.md)). Aby wypełnić wartość void, dodawane są dwa osobne narzędzia. Aby przetestować funkcje DNS, dodaliśmy narzędzie o nazwie **nameresolver.exe**. Składnia jest następująca:

```console
nameresolver.exe hostname [optional: DNS Server]
```

Możesz użyć nameresolver, aby sprawdzić nazwy hostów, od których zależy aplikacja. W ten sposób można testować, czy masz jakieś błędnie skonfigurowane w systemie DNS lub prawdopodobnie nie masz dostępu do serwera DNS. Możesz zobaczyć serwer DNS używany przez aplikację w konsoli programu, przeglądając zmienne środowiskowe WEBSITE_DNS_SERVER i WEBSITE_DNS_ALT_SERVER.

> [!NOTE]
> nameresolver.exe obecnie nie działa w niestandardowych kontenerach systemu Windows.
>

Można użyć następnego narzędzia do testowania łączności TCP z kombinacją hosta i portu. To narzędzie ma nazwę **tcpping** , a Składnia:

```console
tcpping.exe hostname [optional: port]
```

Narzędzie **tcpping** informuje o tym, czy można nawiązać połączenie z określonym hostem i portem. Może to potrwać tylko wtedy, gdy istnieje aplikacja nasłuchująca na hoście i w kombinacji portów oraz dostęp sieciowy z aplikacji do określonego hosta i portu.

#### <a name="debug-access-to-virtual-network-hosted-resources"></a>Debugowanie dostępu do zasobów hostowanych przez sieć wirtualną
Wiele rzeczy może uniemożliwić aplikacji osiągnięcie określonego hosta i portu. Większość czasu jest jedną z następujących czynności:

* **Zapora jest w ten sposób.** Jeśli masz zaporę w taki sposób, osiągnięto limit czasu protokołu TCP. Limit czasu protokołu TCP wynosi 21 sekund w tym przypadku. Użyj narzędzia **tcpping** , aby przetestować łączność. Limity czasu protokołu TCP mogą być spowodowane przez wiele rzeczy poza zaporami, ale można je uruchomić.
* **Usługa DNS jest niedostępna.** Limit czasu DNS wynosi 3 sekundy na serwer DNS. Jeśli masz dwa serwery DNS, limit czasu wynosi 6 sekund. Użyj nameresolver, aby sprawdzić, czy usługa DNS działa. Nie można użyć polecenia nslookup, ponieważ nie używa usługi DNS, z którą jest skonfigurowana Sieć wirtualna. W przypadku niedostępności może być dostępna zapora lub sieciowej grupy zabezpieczeń blokujące dostęp do systemu DNS lub może nie działać.

Jeśli te elementy nie odpowiadają na problemy, należy najpierw sprawdzić, jak:

**Integracja z regionalną siecią wirtualną**
* Czy lokalizacja docelowa jest adresem innym niż RFC1918 i nie masz WEBSITE_VNET_ROUTE_ALL ustawionej na 1?
* Czy istnieje sieciowej grupy zabezpieczeń blokujące ruch wychodzący z podsieci integracji?
* Jeśli korzystasz z usługi Azure ExpressRoute lub sieci VPN, czy brama lokalna została skonfigurowana do kierowania ruchu z kopii zapasowej na platformę Azure? Jeśli możesz uzyskać dostęp do punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, sprawdź trasy.
* Czy masz wystarczające uprawnienia do ustawiania delegowania w podsieci integracji? Podczas konfiguracji integracji regionalnej sieci wirtualnej Twoja podsieć integracji jest delegowana do firmy Microsoft. Web/dopuszczalna. Interfejs użytkownika integracji sieci wirtualnej deleguje podsieć do firmy Microsoft. Web/dopuszczalna automatycznie. Jeśli Twoje konto nie ma wystarczających uprawnień sieciowych do ustawiania delegowania, będziesz potrzebować kogoś, kto może ustawić atrybuty w podsieci integracji w celu delegowania podsieci. Aby ręcznie delegować podsieć integracji, przejdź do interfejsu użytkownika podsieci usługi Azure Virtual Network i ustaw delegowanie dla elementu Microsoft. Web/dopuszczalna.

**Integracja z siecią wirtualną wymagana przez bramę**
* Czy zakres adresów punkt-lokacja w zakresach RFC 1918 (10.0.0.0-10.255.255.255/172.16.0.0-172.31.255.255/192.168.0.0-192.168.255.255)?
* Czy brama ma być wyświetlana w portalu? Jeśli Brama nie działa, przenieś ją z powrotem.
* Czy certyfikaty są wyświetlane jako zsynchronizowane lub czy podejrzewasz, że konfiguracja sieci została zmieniona?  Jeśli certyfikaty nie są zsynchronizowane lub podejrzewasz, że wprowadzono zmiany w konfiguracji sieci wirtualnej, która nie została zsynchronizowana z nazywani, wybierz pozycję **Synchronizuj sieć**.
* Jeśli przechodzisz przez sieć VPN, czy brama lokalna jest skonfigurowana do kierowania ruchu z kopii zapasowej na platformę Azure? Jeśli możesz uzyskać dostęp do punktów końcowych w sieci wirtualnej, ale nie w środowisku lokalnym, sprawdź trasy.
* Czy próbujesz użyć bramy współistnienia, która obsługuje oba punkty do lokacji i ExpressRoute? Bramy współistnienia nie są obsługiwane z integracją sieci wirtualnej.

Debugowanie problemów z siecią jest wyzwaniem, ponieważ nie można zobaczyć, co blokuje dostęp do określonego hosta: kombinacja portów. Oto kilka przyczyn:

* Na hoście istnieje Zapora, która uniemożliwia dostęp do portu aplikacji z zakresu adresów IP punkt-lokacja. Przekroczenia podsieci często wymagają dostępu publicznego.
* Host docelowy nie działa.
* Aplikacja nie działa.
* Masz nieprawidłowy adres IP lub nazwa hosta.
* Aplikacja nasłuchuje na innym porcie niż oczekiwano. Identyfikator procesu można dopasować do portu nasłuchiwania przy użyciu polecenia "netstat-Aon" na hoście punktu końcowego.
* Sieciowe grupy zabezpieczeń są skonfigurowane w taki sposób, że uniemożliwiają one dostęp do hosta i portu aplikacji z zakresu adresów IP punkt-lokacja.

Nie wiesz, co jest używane w Twojej aplikacji. Może to być dowolny adres w podsieci integracji lub zakres adresów punktu do lokacji, dlatego należy zezwolić na dostęp z całego zakresu adresów.

Dodatkowe kroki debugowania obejmują:

* Połącz się z MASZYNą wirtualną w sieci wirtualnej i spróbuj skontaktować się z hostem zasobów: z tego miejsca. Aby przetestować dostęp do protokołu TCP, użyj polecenia programu PowerShell **test-NetConnection**. Składnia jest następująca:

```powershell
test-netconnection hostname [optional: -Port]
```

* Wywołaj aplikację na maszynie wirtualnej i przetestuj dostęp do tego hosta i portu z konsoli programu z poziomu aplikacji za pomocą **tcpping**.

#### <a name="on-premises-resources"></a>Zasoby lokalne ####

Jeśli aplikacja nie może nawiązać połączenia z lokalnym zasobem, sprawdź, czy możesz uzyskać dostęp do zasobu z sieci wirtualnej. Użyj polecenia **test-NetConnection** PowerShell, aby sprawdzić dostęp do protokołu TCP. Jeśli maszyna wirtualna nie może nawiązać połączenia z zasobem lokalnym, połączenie sieci VPN lub ExpressRoute może być niepoprawnie skonfigurowane.

Jeśli maszyna wirtualna hostowana w sieci wirtualnej może nawiązać połączenie z systemem lokalnym, ale aplikacja nie może, przyczyna jest prawdopodobnie jedną z następujących przyczyn:

* Trasy nie są skonfigurowane z zakresem adresów podsieci lub punkt-lokacja w lokalnej bramie.
* Sieciowe grupy zabezpieczeń blokują dostęp do zakresu adresów IP punkt-lokacja.
* Zapora lokalna blokuje ruch z zakresu adresów IP punkt-lokacja.
* Próbujesz nawiązać połączenie z adresem innym niż RFC 1918 przy użyciu funkcji integracji regionalnej sieci wirtualnej.
