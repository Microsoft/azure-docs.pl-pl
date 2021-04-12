---
title: Rotacja certyfikatów dla Azure Database for MySQL
description: Zapoznaj się z nadchodzącymi zmianami zmian certyfikatów głównych, które wpłyną na Azure Database for MySQL
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: conceptual
ms.date: 01/18/2021
ms.openlocfilehash: a80910340333c2e6c52bb32644c48fa3a28ab437
ms.sourcegitcommit: c3739cb161a6f39a9c3d1666ba5ee946e62a7ac3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/08/2021
ms.locfileid: "107210758"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-mysql-single-server"></a>Informacje o zmianach w katalogu głównym urzędu certyfikacji dla Azure Database for MySQL pojedynczego serwera

Azure Database for MySQL jeden serwer pomyślnie ukończył zmianę certyfikatu głównego od **15 lutego 2021 (02/15/2021)** w ramach standardowej konserwacji i najlepszych rozwiązań w zakresie zabezpieczeń. Ten artykuł zawiera więcej szczegółów na temat zmian, odpowiednich zasobów i czynności, które należy wykonać w celu zapewnienia, że aplikacja utrzymuje łączność z serwerem bazy danych.

> [!NOTE]
> Ten artykuł ma zastosowanie tylko do [Azure Database for MySQL-jednego serwera](single-server-overview.md) . W przypadku [serwera Azure Database for MySQL-elastyczny](flexible-server/overview.md)certyfikat wymagany do komunikacji za pośrednictwem protokołu SSL jest [DigiCert globalny główny urząd certyfikacji](https://dl.cacerts.digicert.com/DigiCertGlobalRootCA.crt.pem)
> 
> Ten artykuł zawiera odwołania do warunku _podrzędnego_, termin, który nie jest już wykorzystywany przez firmę Microsoft. Gdy termin zostanie usunięty z oprogramowania, usuniemy go z tego artykułu.
>

## <a name="why-root-certificate-update-is-required"></a>Dlaczego Aktualizacja certyfikatu głównego jest wymagana?

Użytkownicy usługi Azure Database for MySQL mogą używać wstępnie zdefiniowanego certyfikatu do łączenia się z serwerem MySQL, który znajduje się w [tym miejscu](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Jednak na [forum przeglądarki](https://cabforum.org/)urzędu certyfikacji   opublikowane niedawno raporty z wielu certyfikatów wystawionych przez dostawców urzędu certyfikacji jako niezgodne.

Zgodnie z wymaganiami dotyczącymi zgodności w branży dostawcy urzędu certyfikacji rozpoczęły Odwoływanie certyfikatów urzędu certyfikacji dla niezgodnych urzędów certyfikacji, co wymaga od serwerów używania certyfikatów wystawionych przez zgodne urzędy certyfikacji i podpisanych przez certyfikaty urzędu certyfikacji z tych zgodnych urzędów certyfikacji. Ponieważ Azure Database for MySQL użył jednego z tych niezgodnych certyfikatów, potrzebujemy obrócić certyfikat do zgodnej wersji, aby zminimalizować potencjalne zagrożenie dla serwerów MySQL.

Nowy certyfikat jest wprowadzany i obowiązuje od 15 lutego 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Jaka zmiana została wykonana od 15 lutego 2021 (02/15/2021)?

15 lutego 2021 [certyfikat główny BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) został zastąpiony **zgodną wersją** tego samego [certyfikatu głównego BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , aby upewnić się, że istniejący klienci nie muszą zmieniać niczego i nie mają wpływu na połączenia z serwerem. Podczas tej zmiany [certyfikat główny BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **nie został zastąpiony** [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) i ta zmiana jest odroczona, aby umożliwić klientom wprowadzenie zmian.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Czy muszę wprowadzić jakiekolwiek zmiany na kliencie, aby zachować łączność?

Po stronie klienta nie jest wymagana żadna zmiana. Po wykonaniu poprzedniego zalecenia poniżej nadal będzie można nadal łączyć się, o ile **certyfikat BaltimoreCyberTrustRoot nie zostanie usunięty** z certyfikatu połączonego urzędu certyfikacji. **Zalecamy, aby nie usuwać BaltimoreCyberTrustRoot z certyfikatu połączonego urzędu certyfikacji do momentu dalszej uwagi do utrzymania łączności.**

### <a name="previous-recommendation"></a>Poprzednie zalecenie

Aby uniknąć przerwania dostępności aplikacji z powodu nieoczekiwanego odwołania certyfikatów lub zaktualizowania certyfikatu, który został odwołany, wykonaj następujące czynności. Pomysłem jest utworzenie nowego pliku *PEM* , który łączy bieżący certyfikat i nowy i w trakcie weryfikacji certyfikatu SSL jedna z dozwolonych wartości zostanie użyta. Zapoznaj się z następującymi krokami:

* Pobierz BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 głównego urzędu certyfikacji z następujących linków:

  * [https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem)
  * [https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem)

* Wygeneruj połączony magazyn certyfikatów urzędu certyfikacji z certyfikatami **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .

  * W przypadku użytkowników programu Java (łącznik MySQL/J) wykonaj następujące polecenie:

    ```console
    keytool -importcert -alias MySQLServerCACert -file D:\BaltimoreCyberTrustRoot.crt.pem -keystore truststore -storepass password -noprompt
    ```

    ```console
    keytool -importcert -alias MySQLServerCACert2 -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password -noprompt
    ```

    Następnie zastąp oryginalny plik magazynu kluczy nowym wygenerowanym:

    * System. setProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file");
    * System. setProperty ("javax. NET. SSL. trustStorePassword", "Password");

  * W przypadku użytkowników platformy .NET (MySQL Connector/NET, MySQLConnector) Upewnij się, że **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** istnieją w obszarze magazyn certyfikatów systemu Windows, Zaufane główne urzędy certyfikacji. Jeśli jakieś certyfikaty nie istnieją, zaimportuj brakujący certyfikat.

    :::image type="content" source="media/overview/netconnecter-cert.png" alt-text="Diagram certyfikatu platformy .NET Azure Database for MySQL":::

  * W przypadku użytkowników platformy .NET w systemie Linux przy użyciu SSL_CERT_DIR upewnij się, że w katalogu wskazanym przez SSL_CERT_DIR istnieją zarówno **BaltimoreCyberTrustRoot** , jak i **DigiCertGlobalRootG2** . Jeśli jakieś certyfikaty nie istnieją, Utwórz plik brakującego certyfikatu.

  * W przypadku innych użytkowników (MySQL Client/MySQL Workbench/C/Go/Python/Ruby/PHP/NodeJS/Perl/Swift) można scalić dwa pliki certyfikatów urzędu certyfikacji w następującym formacie:

      ```
      -----BEGIN CERTIFICATE-----
      (Root CA1: BaltimoreCyberTrustRoot.crt.pem)
      -----END CERTIFICATE-----
      -----BEGIN CERTIFICATE-----
      (Root CA2: DigiCertGlobalRootG2.crt.pem)
      -----END CERTIFICATE-----
      ```

* Zastąp oryginalny plik PEM głównego urzędu certyfikacji z plikiem połączonego głównego urzędu certyfikacji i uruchom ponownie aplikację/klienta.
* W przyszłości po wdrożeniu nowego certyfikatu po stronie serwera można zmienić plik PEM urzędu certyfikacji na DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> Nie usuwaj ani nie zmieniaj **certyfikatu Baltimore** , dopóki nie zostanie wprowadzona zmiana certyfikatu. Po zakończeniu zmiany wyślemy powiadomienie, po czym będzie ona bezpieczna do usuwania certyfikatu Baltimore. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Dlaczego certyfikat BaltimoreCyberTrustRoot nie został zastąpiony DigiCertGlobalRootG2 w trakcie tej zmiany od 15 lutego 2021?

Oceniamy gotowość klientów do tej zmiany, a wielu klientów szuka dodatkowych czasu realizacji, aby zarządzać tą zmianą. W celu zapewnienia większej ilości czasu realizacji klientom gotowości postanowił się odroczyć zmianę certyfikatu na DigiCertGlobalRootG2 przez co najmniej rok, zapewniając użytkownikom końcowym wystarczającą ilość czasu realizacji. 

Nasze zalecenia dla użytkowników to, aby utworzyć połączony certyfikat i połączyć się z serwerem, ale nie usunąć certyfikatu BaltimoreCyberTrustRoot, dopóki nie wyślemy do niej komunikacji. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Co zrobić, jeśli usunięto certyfikat BaltimoreCyberTrustRoot?

Podczas nawiązywania połączenia z serwerem Azure Database for MySQL wystąpią błędy łączności. Należy ponownie [skonfigurować protokół SSL](howto-configure-ssl.md) z certyfikatem [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , aby zachować łączność.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="1-if-im-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Jeśli nie korzystam z protokołu SSL/TLS, czy muszę zaktualizować główny urząd certyfikacji?

  Jeśli nie używasz protokołu SSL/TLS, akcje nie są wymagane.

### <a name="2-if-im-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Jeśli korzystam z protokołu SSL/TLS, należy ponownie uruchomić serwer bazy danych w celu zaktualizowania głównego urzędu certyfikacji?

Nie musisz ponownie uruchamiać serwera bazy danych, aby rozpocząć korzystanie z nowego certyfikatu. Ten certyfikat główny jest zmianą po stronie klienta, a połączenia klienta przychodzącego muszą używać nowego certyfikatu, aby upewnić się, że mogą łączyć się z serwerem bazy danych.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Jak mogę sprawdzić, czy używam protokołu SSL/TLS z weryfikacją certyfikatu głównego?

Możesz określić, czy Twoje połączenia weryfikują certyfikat główny, przeglądając parametry połączenia.

- Jeśli parametry połączenia obejmują `sslmode=verify-ca` lub `sslmode=verify-identity` , należy zaktualizować certyfikat.
- Jeśli parametry połączenia obejmują `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` , lub `sslmode=require` , nie trzeba aktualizować certyfikatów.
- Jeśli parametry połączenia nie określają sslmode, nie trzeba aktualizować certyfikatów.

Jeśli używasz klienta, który dzieli parametry połączenia, przejrzyj dokumentację klienta, aby dowiedzieć się, czy weryfikuje certyfikaty.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-mysql"></a>4. jaki jest wpływ w przypadku używania App Service z Azure Database for MySQL?

W przypadku usług Azure App Services łączących się z usługą Azure Database for MySQL istnieją dwa możliwe scenariusze i w zależności od tego, jak korzystasz z protokołu SSL z aplikacją.

* Ten nowy certyfikat został dodany do App Service na poziomie platformy. Jeśli używasz certyfikatów SSL znajdujących się na platformie App Service w aplikacji, nie jest wymagana żadna akcja. Jest to najbardziej typowy scenariusz. 
* Jeśli jawnie dołączysz ścieżkę do pliku certyfikatu protokołu SSL w kodzie, należy pobrać nowy certyfikat i utworzyć połączony certyfikat, jak wspomniano powyżej, i użyć pliku certyfikatu. Dobrym przykładem tego scenariusza jest użycie kontenerów niestandardowych w App Service, jak udostępniono w [dokumentacji App Service](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress). Jest to typowy scenariusz, ale widzimy niektórych użytkowników.

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-mysql"></a>5. jaki jest wpływ, jeśli korzystasz z usługi Azure Kubernetes Services (AKS) z Azure Database for MySQL?

Jeśli próbujesz nawiązać połączenie z Azure Database for MySQL przy użyciu usług Azure Kubernetes Services (AKS), jest to podobne do uzyskiwania dostępu z dedykowanego środowiska hosta klientów. Zapoznaj się z [tymi](../aks/ingress-own-tls.md)krokami.

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-mysql"></a>6. jaki jest wpływ, jeśli przy użyciu Azure Data Factory połączyć się z Azure Database for MySQL?

W przypadku łącznika korzystającego z Azure Integration Runtime łącznik używa certyfikatów w magazynie certyfikatów systemu Windows w środowisku hostowanym na platformie Azure. Te certyfikaty są już zgodne z nowo zastosowanymi certyfikatami, w związku z czym nie jest wymagana żadna akcja.

W przypadku łącznika korzystającego z samoobsługowego Integration Runtime, w którym jawnie dołączysz ścieżkę do pliku certyfikatu protokołu SSL w parametrach połączenia, musisz pobrać [nowy certyfikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) i zaktualizować parametry połączenia, aby go użyć.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Czy muszę zaplanować przestoje związane z konserwacją serwera bazy danych dla tej zmiany?

Nie. Ze względu na to, że zmiana ta jest dostępna tylko po stronie klienta, aby połączyć się z serwerem bazy danych, dla tej zmiany nie jest wymagane przestoje związane z konserwacją.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. Jeśli Tworzę nowy serwer po 15 lutego 2021 (02/15/2021), wpłynie to na problem?

W przypadku serwerów utworzonych po 15 lutego 2021 (02/15/2021) będziesz nadal używać [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) do nawiązywania połączeń przy użyciu protokołu SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. jak często firma Microsoft aktualizuje swoje certyfikaty lub jakie są zasady wygasania?

Te certyfikaty używane przez Azure Database for MySQL są udostępniane przez zaufane urzędy certyfikacji. Obsługa tych certyfikatów jest związana z obsługą tych certyfikatów przez urząd certyfikacji. Zaplanowano wygaśnięcie certyfikatu [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) w 2025, aby firma Microsoft musiała przeprowadzić zmianę certyfikatu przed wygaśnięciem. W przypadku nieprzewidzianej usterki w tych wstępnie zdefiniowanych certyfikatach firma Microsoft musi najpierw przystąpić do rotacji certyfikatu podobnej do zmiany wykonanej 15 lutego 2021, aby zapewnić, że usługa jest bezpieczna i zgodna przez cały czas.

### <a name="10-if-im-using-read-replicas-do-i-need-to-perform-this-update-only-on-source-server-or-the-read-replicas"></a>10. Jeśli używam replik odczytu, czy muszę wykonać tę aktualizację tylko na serwerze źródłowym lub w replikach odczytu?

Ponieważ ta aktualizacja jest zmianą po stronie klienta, jeśli klient używany do odczytywania danych z serwera repliki, należy również zastosować zmiany dla tych klientów.

### <a name="11-if-im-using-data-in-replication-do-i-need-to-perform-any-action"></a>11. Jeśli używam replikacji danych, należy wykonać dowolną akcję?

W przypadku korzystania z [replikacji danych w](concepts-data-in-replication.md) celu nawiązania połączenia z usługą Azure Database for MySQL należy wziąć pod uwagę dwie kwestie:

* Jeśli replikacja danych jest z maszyny wirtualnej (Premium lub Azure Virtual Machine) do Azure Database for MySQL, należy sprawdzić, czy protokół SSL jest używany do tworzenia repliki. Uruchom opcję **Pokaż stan podrzędny** i sprawdź poniższe ustawienie.  

    ```azurecli-interactive
    Master_SSL_Allowed            : Yes
    Master_SSL_CA_File            : ~\azure_mysqlservice.pem
    Master_SSL_CA_Path            :
    Master_SSL_Cert               : ~\azure_mysqlclient_cert.pem
    Master_SSL_Cipher             :
    Master_SSL_Key                : ~\azure_mysqlclient_key.pem
    ```

    Jeśli zostanie wyświetlony certyfikat dla CA_file, SSL_Cert i SSL_Key, należy zaktualizować plik, dodając [nowy certyfikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) i tworząc połączony plik certyfikatu.

* Jeśli replikacja danych jest między dwoma Azure Database for MySQL, należy zresetować replikę przez wykonanie **wywołania MySQL.az_replication_change_master** i udostępnić nowy podwójny certyfikat główny jako ostatni parametr [master_ssl_ca](howto-data-in-replication.md#4-link-source-and-replica-servers-to-start-data-in-replication)

### <a name="12-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>12. Czy masz zapytanie po stronie serwera, aby sprawdzić, czy protokół SSL jest używany?

Aby sprawdzić, czy jest używane połączenie SSL do łączenia się z serwerem, odwołaj się do [weryfikacji protokołu SSL](howto-configure-ssl.md#step-4-verify-the-ssl-connection).

### <a name="13-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>13. czy jest wymagana akcja, jeśli mam już DigiCertGlobalRootG2 w moim pliku certyfikatu?

Nie. Jeśli plik certyfikatu ma już **DigiCertGlobalRootG2**, nie jest wymagana żadna akcja.

### <a name="14-what-if-i-have-further-questions"></a>14. co zrobić, jeśli mam więcej pytań?

Jeśli masz pytania, uzyskaj odpowiedzi od ekspertów społeczności w [firmie Microsoft Q&A](mailto:AzureDatabaseforMySQL@service.microsoft.com). Jeśli masz plan pomocy technicznej i potrzebujesz pomocy technicznej, [skontaktuj się z nami](mailto:AzureDatabaseforMySQL@service.microsoft.com).
