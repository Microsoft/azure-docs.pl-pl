---
title: Diagnozuj problemy z konfiguracją linków prywatnych na Azure Key Vault
description: Rozwiązywanie typowych problemów z połączeniami prywatnymi przy użyciu Key Vault i głębokiego szczegółowe w konfiguracji
author: msfcolombo
ms.author: fcolombo
ms.date: 09/30/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: ea818cd14e6052da2bbcf2a4473e95c68cd5e4a9
ms.sourcegitcommit: 67e8e1caa8427c1d78f6426c70bf8339a8b4e01d
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/02/2020
ms.locfileid: "91671325"
---
# <a name="diagnose-private-links-configuration-issues-on-azure-key-vault"></a>Diagnozuj problemy z konfiguracją linków prywatnych na Azure Key Vault

## <a name="introduction"></a>Wprowadzenie

Ten artykuł ułatwia użytkownikom diagnozowanie i rozwiązywanie problemów związanych z Key Vault i funkcją linków prywatnych. Ten przewodnik ułatwia korzystanie z aspektów konfiguracji, takich jak otrzymywanie prywatnych linków po raz pierwszy lub w celu rozwiązania problemu polegającego na tym, że linki prywatne przestały działać z powodu pewnej zmiany.

Jeśli jesteś nowym elementem tej funkcji, zobacz [integracja Key Vault z prywatnym łączem platformy Azure](private-link-service.md).

### <a name="symptoms-covered-by-this-article"></a>Objawy omówione w tym artykule

- Zapytania DNS nadal zwracają publiczny adres IP dla magazynu kluczy, a nie prywatny adres IP, który powinien być używany przez funkcję łącza prywatnego.
- Wszystkie żądania wykonywane przez danego klienta, który używa linku prywatnego, kończą się niepowodzeniem z przekroczeniem limitu czasu lub błędami sieciowymi, a problem nie jest sporadyczny.
- Magazyn kluczy ma prywatny adres IP, ale żądania nadal uzyskują `403` odpowiedź z `ForbiddenByFirewall` wewnętrznym kodem błędu.
- Używasz linków prywatnych, ale Magazyn kluczy nadal akceptuje żądania z publicznego Internetu.
- Magazyn kluczy ma dwa prywatne punkty końcowe. Żądania wykorzystujące jeden z nich działają prawidłowo, ale żądania przy użyciu innych elementów kończą się niepowodzeniem.
- Masz inną subskrypcję, Magazyn kluczy lub sieć wirtualną używającą linków prywatnych. Chcesz utworzyć nowe podobne wdrożenie, ale nie możesz uzyskać połączeń prywatnych.

### <a name="symptoms-not-covered-by-this-article"></a>Objawy nieobjęte tym artykułem

- Występuje tymczasowy problem z łącznością. W danym kliencie zobaczysz, że niektóre żądania działają, a niektóre nie działają. *Sporadyczne problemy zwykle nie są spowodowane problemem z konfiguracją linków prywatnych; są one znakiem przeciążenia sieci lub klienta.*
- Używasz programu i produktu platformy Azure, który obsługuje BYOK (Bring Your Own Key) lub CMK (klucze zarządzane przez klienta) i że produkt nie może uzyskać dostępu do magazynu kluczy. *Zapoznaj się z dokumentacją innych produktów. Upewnij się, że jawnie stanowi obsługę magazynów kluczy z włączoną zaporą. W razie potrzeby skontaktuj się z pomocą techniczną dotyczącą tego konkretnego produktu.*

### <a name="how-to-read-this-article"></a>Jak przeczytać ten artykuł

Jeśli jesteś nowym łączem prywatnym lub oceniasz złożone wdrożenie, zalecamy zapoznanie się z całym artykułem. W przeciwnym razie możesz wybrać sekcję, która sprawia, że występuje problem.

Zaczynamy!

## <a name="1-confirm-that-you-own-the-client-connection"></a>1. Potwierdź, że masz połączenie z klientem

### <a name="confirm-that-your-client-runs-at-the-virtual-network"></a>Upewnij się, że klient jest uruchomiony w sieci wirtualnej

Ten przewodnik rozwiązywania problemów dotyczy połączeń z magazynem kluczy, który pochodzi z kodu aplikacji. Przykładami są aplikacje i skrypty wykonywane w maszynach wirtualnych, klastrach Service Fabric platformy Azure, Azure App Service, usłudze Azure Kubernetes Service (AKS) i podobnych innych.

Przez zdefiniowanie linków prywatnych, aplikacja lub skrypt musi działać na komputerze, klastrze lub środowisku podłączonym do Virtual Network, w którym został wdrożony [zasób prywatnego punktu końcowego](../../private-link/private-endpoint-overview.md) . Jeśli aplikacja działa w dowolnej sieci połączonej z Internetem, ten przewodnik nie ma zastosowania i prawdopodobnie nie można używać prywatnych linków.

### <a name="if-you-use-a-managed-solution-refer-to-specific-documentation"></a>Jeśli używasz rozwiązania zarządzanego, zapoznaj się z określoną dokumentacją

Ten przewodnik nie dotyczy rozwiązań zarządzanych przez firmę Microsoft, w przypadku których Magazyn kluczy jest dostępny dla produktu platformy Azure, który istnieje niezależnie od Virtual Network klienta. Przykładami takich scenariuszy są usługa Azure Storage lub Azure SQL skonfigurowana pod kątem szyfrowania w spoczynku, usługa Azure Event Hub szyfruje dane za pomocą kluczy dostarczonych przez klienta, Azure Data Factory uzyskiwania dostępu do poświadczeń usługi przechowywanych w magazynie kluczy, Azure Pipelines pobierania wpisów tajnych z magazynu kluczy i innych podobnych scenariuszy. W takich przypadkach *należy sprawdzić, czy produkt obsługuje magazyny kluczy z włączoną zaporą*. Ta obsługa jest zwykle wykonywana przy użyciu funkcji [zaufanych usług](overview-vnet-service-endpoints.md#trusted-services) zapory Key Vault. Wiele produktów nie jest jednak uwzględnionych na liście zaufanych usług z różnych powodów. W takim przypadku należy skontaktować się z pomocą techniczną specyficzną dla produktu.

Niewielka liczba produktów platformy Azure obsługuje koncepcję *iniekcji sieci wirtualnej*. W przypadku prostych postanowień produkt dodaje urządzenie sieciowe do Virtual Network klienta, co pozwala mu wysyłać żądania tak, jakby zostały wdrożone do Virtual Network. Przykładem jest [Azure Databricks](https://docs.microsoft.com/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject). Produkty takie jak to mogą zgłaszać żądania do magazynu kluczy przy użyciu linków prywatnych, a ten przewodnik rozwiązywania problemów może pomóc.

## <a name="2-confirm-that-the-connection-is-approved-and-succeeded"></a>2. Upewnij się, że połączenie zostało zatwierdzone i zakończyło się pomyślnie

Poniższe kroki sprawdzają, czy połączenie prywatnego punktu końcowego zostało zatwierdzone i zakończyło się pomyślnie:

1. Otwórz Azure Portal i Otwórz zasób magazynu kluczy.
2. W menu po lewej stronie wybierz pozycję **Sieć**.
3. Kliknij kartę **połączenia prywatne punktu końcowego** . Spowoduje to wyświetlenie wszystkich prywatnych połączeń punktów końcowych i ich odpowiednich stanów. Jeśli nie ma żadnych połączeń lub jeśli brakuje połączenia dla Virtual Network, musisz utworzyć nowy prywatny punkt końcowy. Zostanie to omówione później.
4. Nadal w **połączeniach prywatnych punktów końcowych**Znajdź diagnozę i upewnij się, że "stan połączenia" została **zatwierdzona** i "stan aprowizacji" **zakończyło się pomyślnie**.
    - Jeśli połączenie jest w stanie "oczekiwanie", można je tylko zatwierdzić.
    - Jeśli połączenie "odrzucone", "Niepowodzenie", "błąd", "Rozłączono" lub inny stan, to nie obowiązuje, należy utworzyć nowy prywatny zasób punktu końcowego.

Dobrym pomysłem jest usunięcie nieskutecznych połączeń, aby zachować przejrzystość.

## <a name="3-confirm-that-the-key-vault-firewall-is-properly-configured"></a>3. Upewnij się, że Zapora magazynu kluczy została prawidłowo skonfigurowana

>[!IMPORTANT]
> Zmiana ustawień zapory może spowodować usunięcie dostępu z uprawnionych klientów, którzy nadal nie używają linków prywatnych. Upewnij się, że masz świadomość skutków każdej zmiany w konfiguracji zapory.

Ważną kwestią jest to, że linki prywatne *dają* dostęp tylko do Twojego magazynu kluczy. Nie powoduje *usunięcia* istniejącego dostępu. Aby efektywnie blokować dostęp z publicznej sieci Internet, należy jawnie włączyć zaporę magazynu kluczy:

1. Otwórz Azure Portal i Otwórz zasób magazynu kluczy.
2. W menu po lewej stronie wybierz pozycję **Sieć**.
3. Upewnij się, że na górze jest zaznaczona karta **zapory i sieci wirtualne** .
4. Upewnij się, że wybrano opcję **prywatny punkt końcowy i wybrane sieci** . W przypadku wybrania opcji **wszystkie sieci** wybierz opcję, która wyjaśnia, dlaczego klienci zewnętrzni nadal mogą uzyskać dostęp do magazynu kluczy.

Poniższe instrukcje dotyczą także ustawień zapory:

- Funkcja linków prywatnych nie wymaga określenia żadnej opcji "Sieć wirtualna" w ustawieniach zapory magazynu kluczy. Wszystkie żądania korzystające z prywatnego adresu IP magazynu kluczy (patrz następna sekcja) muszą zadziałać, nawet jeśli w ustawieniach zapory magazynu kluczy nie określono żadnej sieci wirtualnej.
- Funkcja linków prywatnych nie wymaga określenia żadnego adresu IP w ustawieniach zapory magazynu kluczy. Ponownie wszystkie żądania korzystające z prywatnego adresu IP magazynu kluczy muszą być wykonane, nawet jeśli w ustawieniach zapory nie określono żadnego adresu IP.

Jeśli używasz linków prywatnych, jedyną motywacją do określenia sieci wirtualnej lub adresu IP w zaporze magazynu kluczy są:

- Istnieje system hybrydowy, w którym niektórzy klienci używają linków prywatnych, niektórzy korzystają z punktów końcowych usługi, ale używają publicznego adresu IP.
- Przechodzisz do linków prywatnych. W takim przypadku po potwierdzeniu, że wszyscy klienci używają linków prywatnych, należy usunąć sieci wirtualne i adresy IP z ustawień zapory magazynu kluczy.

## <a name="4-make-sure-the-key-vault-has-a-private-ip-address"></a>4. Upewnij się, że magazyn kluczy ma prywatny adres IP

### <a name="difference-between-private-and-public-ip-addresses"></a>Różnica między prywatnymi i publicznymi adresami IP

Prywatny adres IP zawsze ma jeden z następujących formatów:

- 10. x. x. x: Przykłady: `10.1.2.3` , `10.56.34.12` .
- 172.16. x. x do 172.32. x. x: Przykłady: `172.20.1.1` , `172.31.67.89` .
- 192.168. x. x: Przykłady: `192.168.0.1` , `192.168.100.7`

Niektóre adresy IP i zakresy są zastrzeżone:

- 224. x. x. x: multiemisja
- 255.255.255.255: emisja
- 127. x. x. x: sprzężenie zwrotne
- 169.254. x. x: Link lokalny
- 168.63.129.16: wewnętrzny serwer DNS

Wszystkie inne adresy IP są publiczne.

Podczas przeglądania portalu lub uruchamiania polecenia, które pokazuje adres IP, upewnij się, że można określić, czy ten adres IP jest prywatny, publiczny, czy zarezerwowany. Aby prywatne linki działały, nazwa hosta magazynu kluczy musi zostać rozpoznana jako prywatny adres IP należący do Virtual Network przestrzeni adresowej.

### <a name="find-the-key-vault-private-ip-address-in-the-virtual-network"></a>Znajdź prywatny adres IP magazynu kluczy w sieci wirtualnej

Należy zdiagnozować rozpoznawanie nazw hostów, a dla tego należy znać dokładny prywatny adres IP magazynu kluczy z włączonymi linkami prywatnymi. Aby znaleźć ten adres, wykonaj następującą procedurę:

1. Otwórz Azure Portal i Otwórz zasób magazynu kluczy.
2. W menu po lewej stronie wybierz pozycję **Sieć**.
3. Kliknij kartę **połączenia prywatne punktu końcowego** . Spowoduje to wyświetlenie wszystkich prywatnych połączeń punktów końcowych i ich odpowiednich stanów.
4. Znajdź informacje, które są diagnozowane i upewnij się, że stan połączenia jest **zatwierdzony** , a stan aprowizacji **zakończył się pomyślnie**. Jeśli nie widzisz tego, Wróć do poprzednich sekcji tego dokumentu.
5. Po znalezieniu odpowiedniego elementu kliknij link w kolumnie **prywatny punkt końcowy** . Spowoduje to otwarcie prywatnego zasobu punktu końcowego.
6. Na stronie Przegląd można wyświetlić sekcję o nazwie **niestandardowe ustawienia DNS**. Upewnij się, że istnieje tylko jeden wpis pasujący do nazwy hosta magazynu kluczy. Ten wpis pokazuje prywatny adres IP magazynu kluczy.
7. Możesz również kliknąć link w **interfejsie sieciowym** i potwierdzić, że prywatny adres IP jest taki sam, jak w poprzednim kroku. Interfejs sieciowy jest urządzeniem wirtualnym, które reprezentuje Magazyn kluczy.

Adres IP to ta, którą maszyny wirtualne i inne urządzenia *działające w tym samym Virtual Network* będą używane do nawiązywania połączenia z magazynem kluczy. Zanotuj adres IP lub pozostaw otwartą kartę przeglądarki i nie dotykaj jej w trakcie dalszych badań.

>[!NOTE]
> Jeśli magazyn kluczy ma wiele prywatnych punktów końcowych, będzie miał wiele prywatnych adresów IP. Jest to przydatne tylko wtedy, gdy masz wiele sieci wirtualnych uzyskujących dostęp do tego samego magazynu kluczy za pomocą własnego prywatnego punktu końcowego (prywatny punkt końcowy należy do jednego Virtual Network). Upewnij się, że problem dotyczy poprawnego Virtual Network i wybierz odpowiednie połączenie prywatnego punktu końcowego w powyższej procedurze. Ponadto nie **należy tworzyć wielu** prywatnych punktów końcowych dla tego samego Key Vault w tej samej Virtual Network. Nie jest to konieczność i jest źródłem pomyłek.

## <a name="5-validate-the-dns-resolution"></a>5. Sprawdź poprawność rozpoznawania nazw DNS

Rozpoznawanie nazw DNS to proces translacji nazwy hosta magazynu kluczy (przykład: `fabrikam.vault.azure.net` ) na adres IP (przykład: `10.1.2.3` ). W poniższych podsekcjach pokazano oczekiwany wynik rozpoznawania nazw DNS w każdym scenariuszu.

### <a name="key-vaults-without-private-link"></a>Magazyny kluczy bez linku prywatnego

Ta sekcja jest przeznaczona do celów edukacyjnych. Jeśli magazyn kluczy nie ma prywatnego połączenia z punktem końcowym w zatwierdzonym stanie, rozpoznawanie nazwy hosta daje wynik podobny do tego:

W systemie Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

W systemie Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

Można zobaczyć, że nazwa jest rozpoznawana jako publiczny adres IP i nie ma `privatelink` aliasu. Alias został wyjaśniony później. nie martw się o to teraz.

Powyższy wynik jest oczekiwany niezależnie od tego, czy maszyna jest podłączona do Virtual Network, czy też do dowolnego komputera z połączeniem internetowym. Dzieje się tak, ponieważ Magazyn kluczy nie ma prywatnego linku w stanie zatwierdzonym i dlatego nie ma potrzeby obsługi przez Magazyn kluczy prywatnych połączeń linków.

### <a name="key-vault-with-private-link-resolving-from-arbitrary-internet-machine"></a>Magazyn kluczy z prywatnym łączem do rozwiązania z dowolnego komputera z Internetem

Jeśli magazyn kluczy ma co najmniej jedno połączenie prywatnego punktu końcowego w stanie zatwierdzone, a nazwa hosta jest rozpoznawana z dowolnej maszyny połączonej z Internetem (maszyna, która **nie jest** połączona z Virtual Networką, w której znajduje się prywatny punkt końcowy), należy ją znaleźć:

W systemie Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  52.168.109.101
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net
              data-prod-eus.vaultcore.azure.net
              data-prod-eus-region.vaultcore.azure.net

W systemie Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net is an alias for data-prod-eus.vaultcore.azure.net.
    data-prod-eus.vaultcore.azure.net is an alias for data-prod-eus-region.vaultcore.azure.net.
    data-prod-eus-region.vaultcore.azure.net has address 52.168.109.101

Istotna różnica w stosunku do poprzedniego scenariusza polega na tym, że istnieje nowy alias o wartości `{vaultname}.privatelink.vaultcore.azure.net` . Oznacza to, że płaszczyzna danych magazynu kluczy jest gotowa do akceptowania żądań od linków prywatnych.

Nie oznacza to, że żądania wykonywane z maszyn *spoza* Virtual Network (podobnie jak używane) będą używać linków prywatnych — nie będą one. Można sprawdzić, czy nazwa hosta nadal jest rozpoznawana jako publiczny adres IP. Tylko maszyny *połączone z Virtual Network* mogą używać linków prywatnych. Poniższe instrukcje będą się pojawiać.

Jeśli alias nie jest wyświetlany `privatelink` , oznacza to, że magazyn kluczy ma zero połączeń prywatnych punktów końcowych w `Approved` stanie. Kontynuuj odczytywanie tego artykułu.

### <a name="key-vault-with-private-link-resolving-from-virtual-network"></a>Magazyn kluczy z prywatnym linkiem do rozwiązywania problemów z Virtual Network

Jeśli magazyn kluczy ma co najmniej jedno połączenie prywatnego punktu końcowego w zatwierdzonym stanie, a nazwa hosta jest rozpoznawana na komputerze podłączonym do Virtual Network, w którym utworzono prywatny punkt końcowy, jest to oczekiwana odpowiedź:

W systemie Windows:

    C:\> nslookup fabrikam.vault.azure.net

    Non-authoritative answer:
    Address:  10.1.2.3
    Aliases:  fabrikam.vault.azure.net
              fabrikam.privatelink.vaultcore.azure.net

W systemie Linux:

    joe@MyUbuntu:~$ host fabrikam.vault.azure.net

    fabrikam.vault.azure.net is an alias for fabrikam.privatelink.vaultcore.azure.net.
    fabrikam.privatelink.vaultcore.azure.net has address 10.1.2.3

Istnieją dwie istotne różnice. Najpierw nazwa jest rozpoznawana jako prywatny adres IP. Musi to być adres IP znaleziony w [odpowiedniej sekcji](#find-the-key-vault-private-ip-address-in-the-virtual-network) tego artykułu. Po drugie nie ma żadnych innych aliasów `privatelink` . Dzieje się tak, ponieważ serwery DNS Virtual Network *przechwycić* łańcuch aliasów i zwracają prywatny adres IP bezpośrednio z nazwy `fabrikam.privatelink.vaultcore.azure.net` . Ten wpis jest w rzeczywistości `A` rekordem w prywatna strefa DNS strefie. Poniższe instrukcje będą się pojawiać.

>[!NOTE]
> Wynik powyżej występuje tylko w maszynie wirtualnej połączonej z Virtual Network, w którym utworzono prywatny punkt końcowy. Jeśli nie masz wdrożonej maszyny wirtualnej w Virtual Network zawierającej prywatny punkt końcowy, wdróż ją i Połącz zdalnie z nią, a następnie wykonaj `nslookup` polecenie (Windows) lub `host` polecenie (Linux) powyżej.

Jeśli te polecenia są uruchamiane na maszynie wirtualnej połączonej z Virtual Network, w którym utworzono prywatny punkt końcowy, i **nie** jest wyświetlany prywatny adres IP magazynu kluczy, Następna sekcja może pomóc w rozwiązaniu problemu.

## <a name="6-validate-the-private-dns-zone"></a>6. Sprawdź poprawność strefy Prywatna strefa DNS

Jeśli rozpoznawanie nazw DNS nie działa zgodnie z opisem w poprzedniej sekcji, może wystąpić problem ze strefą Prywatna strefa DNS, a ta sekcja może pomóc. Jeśli rozpoznawanie nazw DNS pokazuje poprawny prywatny adres IP magazynu kluczy, strefa Prywatna strefa DNS prawdopodobnie jest poprawna. Możesz pominąć tę całą sekcję.

### <a name="confirm-that-the-required-private-dns-zone-resource-exists"></a>Upewnij się, że wymagany zasób strefy Prywatna strefa DNS istnieje

Twoja subskrypcja platformy Azure musi mieć [prywatna strefa DNS zasób strefy](../../dns/private-dns-privatednszone.md) o tej samej nazwie:

    privatelink.vaultcore.azure.net

Obecność tego zasobu można sprawdzić, przechodząc do strony subskrypcji w portalu i wybierając pozycję "zasoby" w menu po lewej stronie. Nazwa zasobu musi mieć wartość `privatelink.vaultcore.azure.net` , a typem zasobu musi być **prywatna strefa DNS strefa**.

Zazwyczaj ten zasób jest tworzony automatycznie podczas tworzenia prywatnego punktu końcowego przy użyciu typowej metody. Istnieją jednak przypadki, w których ten zasób nie jest tworzony automatycznie i trzeba będzie wykonać go ręcznie. Ten zasób mógł również zostać przypadkowo usunięty.

Jeśli nie masz tego zasobu, Utwórz nowy zasób strefy Prywatna strefa DNS w ramach subskrypcji. Należy pamiętać, że nazwa musi być dokładnie `privatelink.vaultcore.azure.net` , bez spacji lub dodatkowych kropek. W przypadku określenia nieprawidłowej nazwy rozpoznawanie nazw wyjaśnione w tym artykule nie będzie działało. Aby uzyskać więcej informacji na temat tworzenia tego zasobu, zobacz [Tworzenie prywatnej strefy DNS platformy Azure przy użyciu Azure Portal](../../dns/private-dns-getstarted-portal.md). Po wykonaniu tej strony możesz pominąć tworzenie Virtual Network, ponieważ na tym etapie powinien już istnieć. Można również pominąć procedury walidacji za pomocą Virtual Machines.

### <a name="confirm-that-the-private-dns-zone-must-be-linked-to-the-virtual-network"></a>Upewnij się, że strefa Prywatna strefa DNS musi być połączona z Virtual Network

Nie ma wystarczającej Prywatna strefa DNS strefy. Musi on również być połączony z Virtual Networką zawierającą prywatny punkt końcowy. Jeśli strefa Prywatna strefa DNS nie jest połączona z poprawnym Virtual Network, wszelkie rozpoznawanie nazw DNS z tego Virtual Network spowoduje zignorowanie strefy Prywatna strefa DNS.

Otwórz zasób strefy Prywatna strefa DNS i kliknij opcję **linki sieci wirtualnej** w menu po lewej stronie. Spowoduje to wyświetlenie listy linków z nazwami Virtual Network w subskrypcji. W tym miejscu musi być wymieniony Virtual Network zawierający prywatny zasób punktu końcowego. Jeśli tak nie jest, Dodaj go. Aby uzyskać szczegółowe instrukcje, zobacz [łączenie sieci wirtualnej](../../dns/private-dns-getstarted-portal.md#link-the-virtual-network). Opcję "Włącz autorejestrację" można pozostawić niesprawdzoną — ta funkcja nie jest powiązana z linkami prywatnymi.

Gdy Prywatna strefa DNS strefa zostanie połączona z Virtual Network, żądania DNS pochodzące z Virtual Network będą szukać nazw w strefie Prywatna strefa DNS. Jest to wymagane do poprawnego rozpoznawania adresów w Virtual Machines połączonym z Virtual Network, na którym został utworzony prywatny punkt końcowy.

>[!NOTE]
> Jeśli łącze zostało właśnie zapisane, może upłynąć trochę czasu, zanim zacznie to obowiązywać, nawet po zakończeniu działania portalu. Może być również konieczne ponowne uruchomienie maszyny wirtualnej, która jest używana do testowania rozpoznawania nazw DNS.

### <a name="confirm-that-the-private-dns-zone-contains-the-right-a-record"></a>Upewnij się, że strefa Prywatna strefa DNS zawiera prawidłowy rekord

Za pomocą portalu Otwórz strefę Prywatna strefa DNS o nazwie `privatelink.vaultcore.azure.net` . Na stronie Przegląd są wyświetlane wszystkie rekordy. Domyślnie będzie istnieć jeden rekord z nazwą `@` i typem `SOA` , co oznacza początek urzędu certyfikacji. Nie dotykaj tego.

Aby rozpoznawanie nazw magazynu kluczy działało, musi istnieć `A` rekord o prostej nazwie magazynu bez sufiksu lub kropek. Na przykład, jeśli nazwa hosta to `fabrikam.vault.azure.net` , musi istnieć `A` rekord o nazwie `fabrikam` bez żadnego sufiksu lub kropek.

Ponadto wartość `A` rekordu (adres IP) musi być [prywatnym adresem IP magazynu kluczy](#find-the-key-vault-private-ip-address-in-the-virtual-network). Jeśli rekord zostanie znaleziony `A` , ale zawiera niewłaściwy adres IP, należy usunąć niewłaściwy adres IP i dodać nowy. Zaleca się usunięcie całego `A` rekordu i dodanie nowego.

>[!NOTE]
> Po każdym usunięciu lub zmodyfikowaniu `A` rekordu, komputer może nadal zostać rozpoznany jako stary adres IP, ponieważ wartość czasu wygaśnięcia (Time to Live) może jeszcze nie zostać wygasła. Zalecane jest, aby zawsze określić wartość TTL nie mniejszą niż 60 sekund (minutę) i nie większą niż 600 sekund (10 minut). Jeśli określisz zbyt dużą wartość, klienci będą mieć problemy z odzyskiwaniem z awarii.

### <a name="dns-resolution-for-more-than-one-virtual-network"></a>Rozpoznawanie nazw DNS dla więcej niż jednego Virtual Network

Jeśli istnieje wiele sieci wirtualnych, a każdy z nich ma własny prywatny zasób punktu końcowego odwołujący się do tego samego magazynu kluczy, nazwa hosta magazynu kluczy musi zostać rozpoznana jako inny prywatny adres IP w zależności od sieci. Oznacza to, że konieczne jest również korzystanie z wielu Prywatna strefa DNS stref, z których każda jest połączona z innym Virtual Network i przy użyciu innego adresu IP w `A` rekordzie.

W bardziej zaawansowanych scenariuszach istnieje wiele sieci wirtualnych z włączoną obsługą komunikacji równorzędnej. W takim przypadku tylko jeden Virtual Network wymaga zasobu prywatnego punktu końcowego, chociaż obie mogą wymagać połączenia z zasobem strefy Prywatna strefa DNS. Ten dokument nie jest bezpośrednio objęty tym scenariuszem.

### <a name="fact-the-user-controls-dns-resolution"></a>Fakt: użytkownik kontroluje rozpoznawanie nazw DNS

Jeśli jesteś osobą Scholar Network lub chcesz wiedzieć, prawdopodobnie wiesz, jak działa rozpoznawanie nazw DNS. Zgodnie z opisem w [poprzedniej sekcji](#key-vault-with-private-link-resolving-from-arbitrary-internet-machine)Magazyn kluczy z linkami prywatnymi będzie miał alias `{vaultname}.privatelink.vaultcore.azure.net` w swojej *publicznej* rejestracji. Serwer DNS używany przez Virtual Network sprawdzi każdy alias w celu rejestracji nazwy *prywatnej* , a jeśli zostanie znaleziona, zostanie zatrzymany po aliasach rejestracji publicznej.

Rozważmy na przykład, że Virtual Network jest połączony ze strefą Prywatna strefa DNS o nazwie `privatelink.vaultcore.azure.net` , a publiczna Rejestracja w systemie DNS dla magazynu kluczy ma alias `fabrikam.privatelink.vaultcore.azure.net` . Należy pamiętać, że sufiks pasuje do nazwy strefy Prywatna strefa DNS dokładnie. Oznacza to, że rozwiązanie będzie wyglądać najpierw dla `A` rekordu o nazwie `fabrikam` w strefie prywatna strefa DNS. Jeśli `A` rekord zostanie znaleziony, jego adres IP zostanie zwrócony w zapytaniu DNS. A ten adres IP właśnie występuje jako prywatny adres IP magazynu kluczy.

Jak widać, całe rozpoznawanie nazw jest pod kontrolą użytkownika.

Istnieją dwa powody tego projektu:

- Może istnieć złożony scenariusz, który obejmuje niestandardowe serwery DNS i integrację z sieciami lokalnymi. W takim przypadku należy kontrolować sposób tłumaczenia nazw na adresy IP.
- Może być konieczne uzyskanie dostępu do magazynu kluczy bez linków prywatnych. W takim przypadku rozpoznanie nazwy hosta z Virtual Network musi zwrócić publiczny adres IP. dzieje się tak, ponieważ magazyny kluczy bez linków prywatnych nie mają `privatelink` aliasu w rejestracji nazwy.

## <a name="7-validate-that-requests-to-key-vault-use-private-link"></a>7. Sprawdź, czy żądania do magazynu kluczy używają prywatnego linku

### <a name="query-the-healthstatus-endpoint-of-the-key-vault"></a>Zbadaj `/healthstatus` punkt końcowy magazynu kluczy

Magazyn kluczy udostępnia `/healthstatus` punkt końcowy, który może być używany na potrzeby diagnostyki. Nagłówki odpowiedzi zawierają adres IP źródła, który jest widoczny dla usługi magazynu kluczy. Ten punkt końcowy można wywołać za pomocą następującego polecenia (**należy pamiętać, aby użyć nazwy hosta magazynu kluczy**):

Windows (PowerShell):

    PS C:\> $(Invoke-WebRequest -UseBasicParsing -Uri https://fabrikam.vault.azure.net/healthstatus).Headers

    Key                           Value
    ---                           -----
    Pragma                        no-cache
    x-ms-request-id               3729ddde-eb6d-4060-af2b-aac08661d2ec
    x-ms-keyvault-service-version 1.2.27.0
    x-ms-keyvault-network-info    addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security     max-age=31536000;includeSubDomains
    Content-Length                4
    Cache-Control                 no-cache
    Content-Type                  application/json; charset=utf-8

Linux lub Najnowsza wersja systemu Windows 10 obejmująca następujące elementy `curl` :

    joe@MyUbuntu:~$ curl -i https://fabrikam.vault.azure.net/healthstatus
    HTTP/1.1 200 OK
    Cache-Control: no-cache
    Pragma: no-cache
    Content-Type: application/json; charset=utf-8
    x-ms-request-id: 6c090c46-0a1c-48ab-b740-3442ce17e75e
    x-ms-keyvault-service-version: 1.2.27.0
    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;
    Strict-Transport-Security: max-age=31536000;includeSubDomains
    Content-Length: 4

Jeśli nie otrzymasz danych wyjściowych podobnych do tego, lub jeśli wystąpi błąd sieciowy, oznacza to, że magazyn kluczy nie jest dostępny za pośrednictwem określonej nazwy hosta ( `fabrikam.vault.azure.net` w tym przykładzie). Nazwa hosta nie jest rozpoznawana jako prawidłowy adres IP lub występuje problem z łącznością w warstwie transportowej. Może to być spowodowane problemami z routingiem, upuszczeniem pakietów i innymi przyczynami. Musisz dokładniej zbadać.

Odpowiedź musi zawierać nagłówek `x-ms-keyvault-network-info` :

    x-ms-keyvault-network-info: addr=10.4.5.6;act_addr_fam=InterNetworkV6;

`addr`Pole w `x-ms-keyvault-network-info` nagłówku pokazuje adres IP źródła żądania. Ten adres IP może mieć jedną z następujących wartości:

- Prywatny adres IP komputera wysyłającego żądanie. Oznacza to, że żądanie używa prywatnych linków lub punktów końcowych usługi. Jest to oczekiwany wynik linków prywatnych.
- Inny prywatny adres IP, a *nie* z komputera wykonującego żądanie. Oznacza to, że niektóre niestandardowe Routing działa efektywnie. Nadal wskazuje, że żądanie używa prywatnych linków lub punktów końcowych usługi.
- Pewien publiczny adres IP. Oznacza to, że żądanie jest przesyłane do Internetu za pomocą urządzenia bramy (NAT). Oznacza to, że żądanie nie korzysta z linków prywatnych, a część problemu musi zostać rozwiązana. Typowymi przyczynami tego są 1) prywatny punkt końcowy nie jest w stanie zatwierdzonym i zakończonym powodzeniem; i 2) nazwa hosta nie jest rozpoznawana jako prywatny adres IP magazynu kluczy. Ten artykuł zawiera akcje rozwiązywania problemów w obu przypadkach.

>[!NOTE]
> Jeśli żądanie ma `/healthstatus` działać, ale `x-ms-keyvault-network-info` brakuje nagłówka, wówczas punkt końcowy prawdopodobnie nie jest obsługiwany przez Magazyn kluczy. Ponieważ powyższe polecenia również weryfikują certyfikat HTTPS, brakujący nagłówek może być znakiem naruszenia.

### <a name="query-the-key-vault-ip-address-directly"></a>Bezpośrednie zapytanie dotyczące adresu IP magazynu kluczy

>[!IMPORTANT]
> Dostęp do magazynu kluczy bez sprawdzania poprawności certyfikatu HTTPS jest niebezpieczny i może być używany tylko do celów edukacyjnych. Kod produkcyjny nigdy nie może uzyskiwać dostępu do magazynu kluczy bez sprawdzania poprawności po stronie klienta. Nawet jeśli tylko diagnozowanie problemów jest możliwe, może być konieczne ciągłej próbie manipulowania, która nie zostanie ujawniona, jeśli w żądaniach do magazynu kluczy zawsze wyłączysz weryfikację certyfikatu HTTPS.

Jeśli zainstalowano najnowsze wersje programu PowerShell, można użyć `-SkipCertificateCheck` programu do pomijania sprawdzania certyfikatu HTTPS, a następnie można skierować [adres IP magazynu kluczy](#find-the-key-vault-private-ip-address-in-the-virtual-network) bezpośrednio:

    PS C:\> $(Invoke-WebRequest -SkipCertificateCheck -Uri https://10.1.2.3/healthstatus).Headers

Jeśli używasz programu `curl` , możesz wykonać te same czynności z `-k` argumentem:

    joe@MyUbuntu:~$ curl -i -k https://10.1.2.3/healthstatus

Odpowiedzi powinny być takie same jak w poprzedniej sekcji, co oznacza, że musi zawierać `x-ms-keyvault-network-info` nagłówek o tej samej wartości. `/healthstatus`Punkt końcowy nie ma opieki w przypadku używania nazwy hosta lub adresu IP magazynu kluczy.

Jeśli zostanie wyświetlona `x-ms-keyvault-network-info` zwracanie jednej wartości żądania przy użyciu nazwy hosta magazynu kluczy i innej wartości żądania przy użyciu adresu IP, każde żądanie jest kierowane do innego punktu końcowego. Zapoznaj się z wyjaśnieniem `addr` pola z `x-ms-keyvault-network-info` w poprzedniej sekcji, aby określić, który przypadek jest nieprawidłowy i należy go naprawić.

## <a name="8-other-changes-and-customizations-that-cause-impact"></a>8. inne zmiany i dostosowania, które powodują wpływ

Następujące elementy są niewyczerpującymi akcjami badania. Poinformują o tym, gdzie szukać dodatkowych problemów, ale musisz mieć zaawansowaną wiedzę o sieci, aby rozwiązać problemy w tych scenariuszach.

### <a name="diagnose-custom-dns-servers-at-virtual-network"></a>Diagnozowanie niestandardowych serwerów DNS w Virtual Network

W portalu Otwórz zasób Virtual Network. W menu po lewej stronie Otwórz pozycję **serwery DNS**. Jeśli używasz "Custom", rozpoznawanie nazw DNS może nie być opisane w tym dokumencie. Należy zdiagnozować, w jaki sposób serwery DNS rozwiązują nazwę hosta magazynu kluczy.

Jeśli używasz domyślnych serwerów DNS udostępnianych przez platformę Azure, ten cały dokument ma zastosowanie.

### <a name="diagnose-hosts-overriding-or-custom-dns-servers-at-virtual-machine"></a>Diagnozuj hosty przesłaniające lub niestandardowe serwery DNS na maszynie wirtualnej

Wiele systemów operacyjnych pozwala na ustawienie jawnego stałego adresu IP na nazwę hosta, zazwyczaj przez zmianę `hosts` pliku. Mogą również zezwalać na zastępowanie serwerów DNS. Jeśli używasz jednego z tych scenariuszy, Kontynuuj, korzystając z opcji diagnostycznych specyficznych dla systemu.

### <a name="promiscuous-proxies-fiddler-etc"></a>Nieograniczone serwery proxy (programu Fiddler itp.)

Z wyjątkiem jawnie zanotowanych opcji diagnostyki w tym artykule działa tylko wtedy, gdy w środowisku nie istnieje ogólny serwer proxy. Chociaż te serwery proxy są często instalowane wyłącznie na maszynach, które są diagnozowane (programu Fiddler jest najbardziej typowym przykładem), Administratorzy zaawansowani mogą zastąpić główne urzędy certyfikacji (CA) i zainstalować ogólny serwer proxy na urządzeniach bramy, które obsługują wiele maszyn w sieci. Te serwery proxy mogą znacząco wpływać na bezpieczeństwo i niezawodność. Firma Microsoft nie obsługuje konfiguracji, w których są używane te produkty.

### <a name="other-things-that-may-affect-connectivity"></a>Inne elementy, które mogą mieć wpływ na łączność

To nie jest kompletna lista elementów, które można znaleźć w scenariuszach zaawansowaną lub złożoną:

- Ustawienia zapory, Zapora platformy Azure podłączona do Virtual Network lub niestandardowe rozwiązanie zapory wdrożone w Virtual Network lub na maszynie.
- Komunikacja równorzędna sieci, która może mieć wpływ na używane serwery DNS i sposób, w jaki ruch jest kierowany.
- Rozwiązania bramy niestandardowej (NAT), które mogą mieć wpływ na sposób kierowania ruchu, w tym ruch z zapytań DNS.
