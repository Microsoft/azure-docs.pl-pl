---
title: Rotacja certyfikatów dla Azure Database for PostgreSQL pojedynczego serwera
description: Zapoznaj się z nadchodzącymi zmianami zmian certyfikatów głównych, które wpłyną na Azure Database for PostgreSQL jednego serwera
author: mksuni
ms.author: sumuth
ms.service: postgresql
ms.topic: conceptual
ms.date: 09/02/2020
ms.openlocfilehash: c9bc6e3822ac6c014b9ff00e9cd81bbe707628fe
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/03/2021
ms.locfileid: "101736070"
---
# <a name="understanding-the-changes-in-the-root-ca-change-for-azure-database-for-postgresql-single-server"></a>Informacje o zmianach w katalogu głównym urzędu certyfikacji dla Azure Database for PostgreSQL pojedynczego serwera

Azure Database for PostgreSQL jeden serwer pomyślnie ukończył zmianę certyfikatu głównego od **15 lutego 2021 (02/15/2021)** w ramach standardowej konserwacji i najlepszych rozwiązań w zakresie zabezpieczeń. Ten artykuł zawiera więcej szczegółów na temat zmian, odpowiednich zasobów i czynności, które należy wykonać w celu zapewnienia, że aplikacja utrzymuje łączność z serwerem bazy danych.

## <a name="why-root-certificate-update-is-required"></a>Dlaczego Aktualizacja certyfikatu głównego jest wymagana?

Użytkownicy usługi Azure Database for PostgreSQL mogą używać wstępnie zdefiniowanego certyfikatu do nawiązywania połączenia z serwerem PostgreSQL, który znajduje się w [tym miejscu](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem). Jednak na [forum przeglądarki](https://cabforum.org/)urzędu certyfikacji   opublikowane niedawno raporty z wielu certyfikatów wystawionych przez dostawców urzędu certyfikacji jako niezgodne.

Zgodnie z wymaganiami dotyczącymi zgodności w branży dostawcy urzędu certyfikacji rozpoczęły Odwoływanie certyfikatów urzędu certyfikacji dla niezgodnych urzędów certyfikacji, co wymaga od serwerów używania certyfikatów wystawionych przez zgodne urzędy certyfikacji i podpisanych przez certyfikaty urzędu certyfikacji z tych zgodnych urzędów certyfikacji. Ponieważ Azure Database for MySQL użył jednego z tych niezgodnych certyfikatów, potrzebujemy obrócić certyfikat do zgodnej wersji, aby zminimalizować potencjalne zagrożenie dla serwerów MySQL.

Nowy certyfikat jest wprowadzany i obowiązuje od 15 lutego 2021 (02/15/2021). 

## <a name="what-change-was-performed-on-february-15-2021-02152021"></a>Jaka zmiana została wykonana od 15 lutego 2021 (02/15/2021)?

15 lutego 2021 [certyfikat główny BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) został zastąpiony **zgodną wersją** tego samego [certyfikatu głównego BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , aby upewnić się, że istniejący klienci nie muszą zmieniać niczego i nie mają wpływu na połączenia z serwerem. Podczas tej zmiany [certyfikat główny BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) **nie został zastąpiony** [DigiCertGlobalRootG2](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) i ta zmiana jest odroczona, aby umożliwić klientom wprowadzenie zmian.

## <a name="do-i-need-to-make-any-changes-on-my-client-to-maintain-connectivity"></a>Czy muszę wprowadzić jakiekolwiek zmiany na kliencie, aby zachować łączność?

Po stronie klienta nie jest wymagana żadna zmiana. Po wykonaniu poprzedniego zalecenia poniżej nadal będzie można nadal łączyć się, o ile **certyfikat BaltimoreCyberTrustRoot nie zostanie usunięty** z certyfikatu połączonego urzędu certyfikacji. **Zalecamy, aby nie usuwać BaltimoreCyberTrustRoot z certyfikatu połączonego urzędu certyfikacji do momentu dalszej uwagi do utrzymania łączności.**

### <a name="previous-recommendation"></a>Poprzednie zalecenie

*   Pobierz BaltimoreCyberTrustRoot & DigiCertGlobalRootG2 głównego urzędu certyfikacji z poniższych linków:
    *   https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem
    *   https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem

*   Wygeneruj połączony magazyn certyfikatów urzędu certyfikacji z certyfikatami **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** .
    *   W przypadku użytkowników języka Java (PostgreSQL JDBC) przy użyciu usługi DefaultJavaSSLFactory wykonaj następujące polecenie:

          ```console
          keytool -importcert -alias PostgreSQLServerCACert  -file D:\BaltimoreCyberTrustRoot.crt.pem  -keystore truststore -storepass password -noprompt
          ```

          ```console
          keytool -importcert -alias PostgreSQLServerCACert2  -file D:\DigiCertGlobalRootG2.crt.pem -keystore truststore -storepass password  -noprompt
          ```

          Następnie zastąp oryginalny plik magazynu kluczy nowym wygenerowanym:
        *   System. setProperty ("javax. NET. SSL. trustStore", "path_to_truststore_file"); 
        *   System. setProperty ("javax. NET. SSL. trustStorePassword", "Password");
        
    *   W przypadku użytkowników platformy .NET (Npgsql) w systemie Windows należy upewnić się, że **Baltimore CyberTrust root** i **DigiCert Global root** . Jeśli jakieś certyfikaty nie istnieją, zaimportuj brakujący certyfikat.

        ![Azure Database for PostgreSQL certyfikatem platformy .NET](media/overview/netconnecter-cert.png)

    *   W przypadku użytkowników platformy .NET (Npgsql) w systemie Linux przy użyciu SSL_CERT_DIR upewnij się, że **BaltimoreCyberTrustRoot** i **DigiCertGlobalRootG2** znajdują się w katalogu wskazanym przez SSL_CERT_DIR. Jeśli jakieś certyfikaty nie istnieją, Utwórz plik brakującego certyfikatu.

    *   W przypadku innych użytkowników klienta PostgreSQL można scalić dwa pliki certyfikatów urzędu certyfikacji, takie jak poniższy format

        </br>-----ROZPOCZNIJ CERTYFIKAT-----
 </br>(Root CA1: BaltimoreCyberTrustRoot. CRT. pem)
 </br>-----KOŃCOWY CERTYFIKAT-----
 </br>-----ROZPOCZNIJ CERTYFIKAT-----
 </br>(Root CA2: DigiCertGlobalRootG2. CRT. pem)
 </br>-----KOŃCOWY CERTYFIKAT-----

*   Zastąp oryginalny plik PEM głównego urzędu certyfikacji z plikiem połączonego głównego urzędu certyfikacji i uruchom ponownie aplikację/klienta.
*    W przyszłości po wdrożeniu nowego certyfikatu po stronie serwera można zmienić plik PEM urzędu certyfikacji na DigiCertGlobalRootG2. CRT. pem.

> [!NOTE]
> Nie usuwaj ani nie zmieniaj **certyfikatu Baltimore** , dopóki nie zostanie wprowadzona zmiana certyfikatu. Po zakończeniu zmiany wyślemy powiadomienie, po czym będzie ona bezpieczna do usuwania certyfikatu Baltimore. 

## <a name="why-was-baltimorecybertrustroot-certificate-not-replaced-to-digicertglobalrootg2-during-this-change-on-february-15-2021"></a>Dlaczego certyfikat BaltimoreCyberTrustRoot nie został zastąpiony DigiCertGlobalRootG2 w trakcie tej zmiany od 15 lutego 2021?

Oceniamy gotowość klientów do tej zmiany, a wielu klientów szuka dodatkowych czasu realizacji, aby zarządzać tą zmianą. W celu zapewnienia większej ilości czasu realizacji klientom gotowości postanowił się odroczyć zmianę certyfikatu na DigiCertGlobalRootG2 przez co najmniej rok, zapewniając użytkownikom końcowym wystarczającą ilość czasu realizacji. 

Nasze zalecenia dla użytkowników to, aby utworzyć połączony certyfikat i połączyć się z serwerem, ale nie usunąć certyfikatu BaltimoreCyberTrustRoot, dopóki nie wyślemy do niej komunikacji. 

## <a name="what-if-we-removed-the-baltimorecybertrustroot-certificate"></a>Co zrobić, jeśli usunięto certyfikat BaltimoreCyberTrustRoot?

Podczas nawiązywania połączenia z serwerem Azure Database for PostgreSQL wystąpią błędy łączności. Należy ponownie [skonfigurować protokół SSL](howto-configure-ssl.md) z certyfikatem [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , aby zachować łączność.


## <a name="frequently-asked-questions"></a>Często zadawane pytania

###    <a name="1-if-i-am-not-using-ssltls-do-i-still-need-to-update-the-root-ca"></a>1. Jeśli nie korzystam z protokołu SSL/TLS, czy nadal muszę zaktualizować główny urząd certyfikacji?
Jeśli nie używasz protokołu SSL/TLS, akcje nie są wymagane. 

### <a name="2-if-i-am-using-ssltls-do-i-need-to-restart-my-database-server-to-update-the-root-ca"></a>2. Jeśli używam protokołu SSL/TLS, należy ponownie uruchomić serwer bazy danych w celu zaktualizowania głównego urzędu certyfikacji?
Nie, nie trzeba ponownie uruchamiać serwera bazy danych, aby rozpocząć korzystanie z nowego certyfikatu. Jest to zmiana po stronie klienta, a połączenia klientów przychodzących muszą używać nowego certyfikatu, aby upewnić się, że mogą łączyć się z serwerem bazy danych.

### <a name="3-how-do-i-know-if-im-using-ssltls-with-root-certificate-verification"></a>3. Jak mogę sprawdzić, czy używam protokołu SSL/TLS z weryfikacją certyfikatu głównego?

Możesz określić, czy Twoje połączenia weryfikują certyfikat główny, przeglądając parametry połączenia.
-    Jeśli parametry połączenia obejmują `sslmode=verify-ca` lub `sslmode=verify-full` , należy zaktualizować certyfikat.
-    Jeśli parametry połączenia obejmują `sslmode=disable` , `sslmode=allow` , `sslmode=prefer` , lub `sslmode=require` , nie trzeba aktualizować certyfikatów. 
-    Jeśli parametry połączenia nie określają sslmode, nie trzeba aktualizować certyfikatów.

Jeśli używasz klienta, który dzieli parametry połączenia, przejrzyj dokumentację klienta, aby dowiedzieć się, czy weryfikuje certyfikaty. Aby zrozumieć PostgreSQL sslmode, zapoznaj się z [opisem trybu SSL](https://www.postgresql.org/docs/11/libpq-ssl.html#ssl-mode-descriptions) w dokumentacji PostgreSQL.

### <a name="4-what-is-the-impact-if-using-app-service-with-azure-database-for-postgresql"></a>4. jaki jest wpływ w przypadku używania App Service z Azure Database for PostgreSQL?
W przypadku usług Azure App Services łączących się z Azure Database for PostgreSQL mogą istnieć dwa możliwe scenariusze, które są zależne od tego, jak korzystasz z protokołu SSL z aplikacją.
*   Ten nowy certyfikat został dodany do App Service na poziomie platformy. Jeśli używasz certyfikatów SSL zawartych na platformie App Service w aplikacji, nie jest wymagana żadna akcja.
*   Jeśli jawnie dołączasz ścieżkę do pliku certyfikatu protokołu SSL w kodzie, należy pobrać nowy certyfikat i zaktualizować kod, aby użyć nowego certyfikatu. Dobrym przykładem tego scenariusza jest użycie kontenerów niestandardowych w App Service, jak to zostało udostępnione w [dokumentacji App Service](../app-service/tutorial-multi-container-app.md#configure-database-variables-in-wordpress)

### <a name="5-what-is-the-impact-if-using-azure-kubernetes-services-aks-with-azure-database-for-postgresql"></a>5. jaki jest wpływ, jeśli korzystasz z usługi Azure Kubernetes Services (AKS) z Azure Database for PostgreSQL?
Jeśli próbujesz nawiązać połączenie z Azure Database for PostgreSQL przy użyciu usługi Azure Kubernetes Services (AKS), będzie ona podobna do dostępu z poziomu dedykowanego środowiska macierzystego klientów. Zapoznaj się z [tymi](../aks/ingress-own-tls.md)krokami.

### <a name="6-what-is-the-impact-if-using-azure-data-factory-to-connect-to-azure-database-for-postgresql"></a>6. jaki jest wpływ, jeśli przy użyciu Azure Data Factory połączyć się z Azure Database for PostgreSQL?
W przypadku łącznika korzystającego z Azure Integration Runtime łącznik używa certyfikatów w magazynie certyfikatów systemu Windows w środowisku hostowanym na platformie Azure. Te certyfikaty są już zgodne z nowo zastosowanymi certyfikatami, w związku z czym nie jest wymagana żadna akcja.

W przypadku łącznika korzystającego z samoobsługowego Integration Runtime, w którym jawnie dołączysz ścieżkę do pliku certyfikatu protokołu SSL w parametrach połączenia, musisz pobrać [nowy certyfikat](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) i zaktualizować parametry połączenia, aby go użyć.

### <a name="7-do-i-need-to-plan-a-database-server-maintenance-downtime-for-this-change"></a>7. Czy muszę zaplanować przestoje związane z konserwacją serwera bazy danych dla tej zmiany?
Nie. Ze względu na to, że zmiana ta jest dostępna tylko po stronie klienta, aby nawiązać połączenie z serwerem bazy danych, dla tej zmiany nie jest wymagane przestoje związane z konserwacją.

### <a name="8-if-i-create-a-new-server-after-february-15-2021-02152021-will-i-be-impacted"></a>8. Jeśli Tworzę nowy serwer po 15 lutego 2021 (02/15/2021), wpłynie to na problem?
W przypadku serwerów utworzonych po 15 lutego 2021 (02/15/2021) będziesz nadal używać [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) do nawiązywania połączeń przy użyciu protokołu SSL.

### <a name="9-how-often-does-microsoft-update-their-certificates-or-what-is-the-expiry-policy"></a>9. jak często firma Microsoft aktualizuje swoje certyfikaty lub jakie są zasady wygasania?
Te certyfikaty używane przez Azure Database for PostgreSQL są udostępniane przez zaufane urzędy certyfikacji. Obsługa tych certyfikatów jest związana z obsługą tych certyfikatów przez urząd certyfikacji. Zaplanowano wygaśnięcie certyfikatu [BaltimoreCyberTrustRoot](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) w 2025, aby firma Microsoft musiała przeprowadzić zmianę certyfikatu przed wygaśnięciem. W przypadku nieprzewidzianej usterki w tych wstępnie zdefiniowanych certyfikatach firma Microsoft musi najpierw przystąpić do rotacji certyfikatu podobnej do zmiany wykonanej 15 lutego 2021, aby zapewnić, że usługa jest bezpieczna i zgodna przez cały czas.

### <a name="10-if-i-am-using-read-replicas-do-i-need-to-perform-this-update-only-on-the-primary-server-or-the-read-replicas"></a>10. w przypadku korzystania z replik odczytu należy wykonać tę aktualizację tylko na serwerze podstawowym lub w replikach odczytu?
Ponieważ ta aktualizacja jest zmianą po stronie klienta, jeśli klient używany do odczytywania danych z serwera repliki, należy również zastosować zmiany dla tych klientów. 

### <a name="11-do-we-have-server-side-query-to-verify-if-ssl-is-being-used"></a>11. Czy masz zapytanie po stronie serwera, aby sprawdzić, czy protokół SSL jest używany?
Aby sprawdzić, czy jest używane połączenie SSL do łączenia się z serwerem, odwołaj się do [weryfikacji protokołu SSL](concepts-ssl-connection-security.md#applications-that-require-certificate-verification-for-tls-connectivity).

### <a name="12-is-there-an-action-needed-if-i-already-have-the-digicertglobalrootg2-in-my-certificate-file"></a>12. czy jest wymagana akcja, jeśli mam już DigiCertGlobalRootG2 w moim pliku certyfikatu?
Nie. Jeśli plik certyfikatu ma już **DigiCertGlobalRootG2**, nie jest wymagana żadna akcja.

### <a name="13-what-if-you-are-using-docker-image-of-pgbouncer-sidecar-provided-by-microsoft"></a>13. co zrobić, jeśli używasz obrazu platformy Docker PgBouncer przyczepki udostępnionej przez firmę Microsoft?
Nowy obraz platformy Docker obsługujący zarówno [**Baltimore**](https://www.digicert.com/CACerts/BaltimoreCyberTrustRoot.crt.pem) , jak i [**DigiCert**](https://cacerts.digicert.com/DigiCertGlobalRootG2.crt.pem) jest publikowany [poniżej (](https://hub.docker.com/_/microsoft-azure-oss-db-tools-pgbouncer-sidecar) najnowszy tag). Możesz ściągnąć ten nowy obraz, aby uniknąć przerw w łączności od 15 lutego 2021. 

### <a name="14-what-if-i-have-further-questions"></a>14. co zrobić, jeśli mam więcej pytań?
Jeśli masz pytania, uzyskaj odpowiedzi od ekspertów społeczności w [firmie Microsoft Q&A](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com). Jeśli masz plan pomocy technicznej i potrzebujesz pomocy technicznej,  [skontaktuj się z nami](mailto:AzureDatabaseforPostgreSQL@service.microsoft.com)
