---
title: Zabezpieczenia komunikacji dla Microsoft Threat Modeling Tool
titleSuffix: Azure
description: Dowiedz się więcej na temat łagodzenia zagrożeń związanych z bezpieczeństwem komunikacji narażonych na Threat Modeling Tool. Zobacz informacje o ograniczeniach i Wyświetl przykłady kodu.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/07/2017
ms.author: jegeib
ms.custom: devx-track-csharp
ms.openlocfilehash: d9a4eabf37101622ac69ae05f3bec232fb8d2fe6
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "94517533"
---
# <a name="security-frame-communication-security--mitigations"></a>Ramka zabezpieczeń: zabezpieczenia komunikacji | Środki zaradcze 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Centrum zdarzeń Azure** | <ul><li>[Zabezpieczanie komunikacji z centrum zdarzeń przy użyciu protokołu SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Sprawdź uprawnienia konta usługi i sprawdź, czy usługi niestandardowe lub strony ASP.NET respektują zabezpieczenia CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Użyj bramy zarządzania danymi podczas łączenia SQL Server lokalnych do Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Serwer tożsamości** | <ul><li>[Upewnij się, że cały ruch do serwera tożsamości jest za pośrednictwem połączenia HTTPS](#identity-https)</li></ul> |
| **Aplikacja sieci Web** | <ul><li>[Weryfikowanie certyfikatów X. 509 używanych do uwierzytelniania połączeń SSL, TLS i DTLS](#x509-ssltls)</li><li>[Konfigurowanie certyfikatu TLS/SSL dla domeny niestandardowej w Azure App Service](#ssl-appservice)</li><li>[Wymuszaj cały ruch do Azure App Service za pośrednictwem połączenia HTTPS](#appservice-https)</li><li>[Włącz zabezpieczenia protokołu HTTP Strict Transport (HSTS)](#http-hsts)</li></ul> |
| **Database** (Baza danych) | <ul><li>[Upewnij się, że szyfrowanie połączenia z programem SQL Server i sprawdzanie poprawności certyfikatu](#sqlserver-validation)</li><li>[Wymuś zaszyfrowaną komunikację z programem SQL Server](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Upewnij się, że komunikacja z usługą Azure Storage odbywa się za pośrednictwem protokołu HTTPS](#comm-storage)</li><li>[Weryfikuj skrót MD5 po pobieraniu obiektu BLOB, jeśli nie można włączyć protokołu HTTPS](#md5-https)</li><li>[Korzystanie z klienta zgodnego z protokołem SMB 3,0 w celu zapewnienia szyfrowania danych w ramach przesyłania do udziałów plików platformy Azure](#smb-shares)</li></ul> |
| **Klient mobilny** | <ul><li>[Implementuj Przypinanie certyfikatów](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Włącz kanał transportu Secure HTTPS](#https-transport)</li><li>[WCF: Ustaw poziom ochrony zabezpieczeń wiadomości na EncryptAndSign](#message-protection)</li><li>[WCF: Użyj konta z najniższymi uprawnieniami do uruchomienia usługi WCF](#least-account-wcf)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Wymuś cały ruch do interfejsów API sieci Web za pośrednictwem połączenia HTTPS](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Upewnij się, że komunikacja z usługą Azure cache for Redis odbywa się za pośrednictwem protokołu TLS](#redis-ssl)</li></ul> |
| **Brama pola IoT** | <ul><li>[Zabezpieczanie urządzenia do komunikacji z bramą pola](#device-field)</li></ul> |
| **Brama usługi IoT Cloud** | <ul><li>[Zabezpieczanie urządzenia do komunikacji z bramą w chmurze przy użyciu protokołu SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Zabezpieczanie komunikacji z centrum zdarzeń przy użyciu protokołu SSL/TLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Omówienie modelu uwierzytelniania i zabezpieczeń Event Hubs](../../event-hubs/authenticate-shared-access-signature.md) |
| **Kroki** | Zabezpieczanie połączeń AMQP lub HTTP z centrum zdarzeń przy użyciu protokołu SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Sprawdź uprawnienia konta usługi i sprawdź, czy usługi niestandardowe lub strony ASP.NET respektują zabezpieczenia CRM

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Sprawdź uprawnienia konta usługi i sprawdź, czy usługi niestandardowe lub strony ASP.NET respektują zabezpieczenia CRM |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Użyj bramy zarządzania danymi podczas łączenia SQL Server lokalnych do Azure Data Factory

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Data Factory | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typy połączonych usług — platforma Azure i lokalna |
| **Odwołania**              |[Przeniesienie danych między środowiskiem lokalnym i Azure Data Factory](../../data-factory/v1/data-factory-move-data-between-onprem-and-cloud.md#create-gateway), [brama zarządzania danymi](../../data-factory/v1/data-factory-data-management-gateway.md) |
| **Kroki** | <p>Narzędzie Zarządzanie danymi Gateway (DMG) jest wymagane do nawiązywania połączeń ze źródłami danych, które są chronione za pomocą sieci firmowej lub zapory.</p><ol><li>Zablokowanie maszyny powoduje odizolowanie narzędzia DMG i uniemożliwia niedziałającym programom uszkadzanie lub śledzenie na maszynie źródła danych. Tj. należy zainstalować najnowsze aktualizacje, włączyć wymagania dotyczące minimalnych wymaganych portów, kontrolowanej obsługi kont, inspekcji włączonej, włączeniu szyfrowania dysków itp.).</li><li>Klucz bramy danych musi być obrócony w częstych odstępach czasu lub za każdym razem, gdy odnawiane są hasła do konta usługi DMG</li><li>Przesyłanie danych za poorednictwem usługi linku musi być zaszyfrowane</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Upewnij się, że cały ruch do serwera tożsamości jest za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [IdentityServer3 — klucze, podpisy i Kryptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3-Deployment](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroki** | Domyślnie IdentityServer wymaga, aby wszystkie połączenia przychodzące były dostarczane za pośrednictwem protokołu HTTPS. Jest absolutnie obowiązkowe, aby komunikacja z IdentityServer odbywała się tylko przez zabezpieczone transporty. Istnieją pewne scenariusze wdrażania, takie jak w przypadku odciążania protokołu TLS, gdy to wymaganie może być swobodne. Aby uzyskać więcej informacji, zobacz stronę Wdrażanie serwera tożsamości w odwołaniach. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Weryfikowanie certyfikatów X. 509 używanych do uwierzytelniania połączeń SSL, TLS i DTLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>Aplikacje korzystające z protokołu SSL, TLS lub DTLS muszą w pełni weryfikować certyfikaty X. 509 jednostek, z którymi się łączą. Obejmuje to weryfikację certyfikatów dla:</p><ul><li>Nazwa domeny</li><li>Daty ważności (daty rozpoczęcia i wygaśnięcia)</li><li>Stan odwołania</li><li>Użycie (na przykład uwierzytelnianie serwera dla serwerów, uwierzytelnianie klientów dla klientów)</li><li>Łańcuch zaufania. Certyfikaty muszą być powiązane z głównym urzędem certyfikacji (CA), który jest traktowany jako zaufany przez platformę lub jawnie skonfigurowany przez administratora</li><li>Długość klucza publicznego certyfikatu musi być >2048 bitów</li><li>Algorytm wyznaczania wartości skrótu musi być SHA256 i wyższy |

## <a name="configure-tlsssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Konfigurowanie certyfikatu TLS/SSL dla domeny niestandardowej w Azure App Service

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | EnvironmentType — Azure |
| **Odwołania**              | [Włącz protokół HTTPS dla aplikacji w Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Kroki** | Domyślnie platforma Azure włącza już protokół HTTPS dla każdej aplikacji z certyfikatem wieloznacznym dla domeny *. azurewebsites.net. Jednak podobnie jak w przypadku wszystkich domen symboli wieloznacznych nie jest tak bezpieczne, jak używanie domeny niestandardowej z [własnym certyfikatem](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Zaleca się włączenie protokołu TLS dla domeny niestandardowej, do której będzie uzyskiwany dostęp do wdrożonej aplikacji za pomocą|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Wymuszaj cały ruch do Azure App Service za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | EnvironmentType — Azure |
| **Odwołania**              | [Wymuszanie protokołu HTTPS na Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Kroki** | <p>Mimo że platforma Azure umożliwia już korzystanie z protokołu HTTPS dla usług Azure App Services z certyfikatem wieloznacznym dla domeny *. azurewebsites.net, nie wymusza protokołu HTTPS. Osoby odwiedzające mogą nadal uzyskiwać dostęp do aplikacji przy użyciu protokołu HTTP, co może naruszyć bezpieczeństwo aplikacji i dlatego protokół HTTPS musi być wymuszany jawnie. Aplikacje ASP.NET MVC powinny używać [filtru RequireHttps](/dotnet/api/system.web.mvc.requirehttpsattribute) , który wymusza, aby niezabezpieczone żądanie HTTP było ponownie wysyłane za pośrednictwem protokołu HTTPS.</p><p>W celu wymuszenia protokołu HTTPS można także użyć modułu ponownego zapisywania adresu URL, który jest dołączony do Azure App Service. Moduł ponowne zapisywanie adresów URL umożliwia deweloperom definiowanie reguł, które są stosowane do żądań przychodzących przed przekazaniem żądań do aplikacji. Reguły ponownego zapisywania adresów URL są zdefiniowane w pliku web.config przechowywanym w katalogu głównym aplikacji.</p>|

### <a name="example"></a>Przykład
Poniższy przykład zawiera regułę podstawowego ponownego zapisywania adresu URL, która wymusza użycie protokołu HTTPS przez cały ruch przychodzący
```XML
<?xml version="1.0" encoding="UTF-8"?>
<configuration>
  <system.webServer>
    <rewrite>
      <rules>
        <rule name="Force HTTPS" enabled="true">
          <match url="(.*)" ignoreCase="false" />
          <conditions>
            <add input="{HTTPS}" pattern="off" />
          </conditions>
          <action type="Redirect" url="https://{HTTP_HOST}/{R:1}" appendQueryString="true" redirectType="Permanent" />
        </rule>
      </rules>
    </rewrite>
  </system.webServer>
</configuration>
```
Ta reguła działa przez zwrócenie kodu stanu HTTP 301 (trwałe przekierowanie), gdy użytkownik zażąda strony przy użyciu protokołu HTTP. 301 przekierowuje żądanie do tego samego adresu URL, który jest żądany przez osobę odwiedzającą, ale zastępuje część HTTP żądania przy użyciu protokołu HTTPS. Na przykład `HTTP://contoso.com` zostałaby przekierowana do `HTTPS://contoso.com` . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Włącz zabezpieczenia protokołu HTTP Strict Transport (HSTS)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [OWASP HTTP Strict Transport Security Ściągawka arkusz](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Kroki** | <p>Protokół HTTP Strict Transport Security (HSTS) jest opcjonalnym ulepszeniem zabezpieczeń, który jest określany przez aplikację sieci Web przy użyciu specjalnego nagłówka odpowiedzi. Po otrzymaniu tego nagłówka przez obsługiwaną przeglądarkę przeglądarka uniemożliwi wysyłanie komunikacji za pośrednictwem protokołu HTTP do określonej domeny, a zamiast tego wyśle całą komunikację za pośrednictwem protokołu HTTPS. Zapobiega to również kliknięciu za pośrednictwem apletu polecenia w przeglądarkach.</p><p>Aby zaimplementować HSTS, należy skonfigurować następujący nagłówek odpowiedzi dla witryny sieci Web globalnie, w kodzie lub w pliku config. Rygorystyczne-Transport-Security: max-age = 300; includeSubDomains HSTS dotyczy następujących zagrożeń:</p><ul><li>Zakładki użytkownika lub ręczne typy `https://example.com` , które podlegają osobie atakującej typu man-in-the-Middle: HSTS automatycznie przekierowuje żądania HTTP do protokołu HTTPS dla domeny docelowej</li><li>Aplikacja sieci Web, która jest przeznaczona do czystego HTTPS, zawiera linki HTTP lub obsługuje zawartość za pośrednictwem protokołu HTTP: HSTS automatycznie przekierowuje żądania HTTP do protokołu HTTPS dla domeny docelowej</li><li>Osoba atakująca typu man-in-the-Middle próbuje przechwycić ruch od użytkownika ofiary przy użyciu nieprawidłowego certyfikatu i nadziei, że użytkownik zaakceptuje nieprawidłowy certyfikat: HSTS nie zezwala użytkownikowi na przesłonięcie nieprawidłowego komunikatu certyfikatu</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Upewnij się, że szyfrowanie połączenia z programem SQL Server i sprawdzanie poprawności certyfikatu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Usługi SQL Azure  |
| **Atrybuty**              | Wersja SQL — V12 |
| **Odwołania**              | [Najlepsze rozwiązania dotyczące pisania bezpiecznych parametrów połączenia dla SQL Database](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Kroki** | <p>Cała komunikacja między SQL Database i aplikacją kliencką jest szyfrowana przy użyciu Transport Layer Security (TLS), wcześniej znanej jako Secure Sockets Layer (SSL) przez cały czas. SQL Database nie obsługuje nieszyfrowanych połączeń. Aby sprawdzić poprawność certyfikatów przy użyciu kodu lub narzędzi aplikacji, jawnie Zażądaj szyfrowanego połączenia i nie ufaj certyfikatom serwera. Jeśli kod aplikacji lub narzędzia nie zażądają zaszyfrowanego połączenia, nadal będą otrzymywać połączenia szyfrowane</p><p>Jednak mogą oni nie sprawdzać poprawności certyfikatów serwera i dlatego są podatne na ataki typu man-in-Middle. Aby sprawdzić poprawność certyfikatów przy użyciu kodu aplikacji ADO.NET, ustaw `Encrypt=True` i `TrustServerCertificate=False` w parametrach połączenia z bazą danych. Aby sprawdzić poprawność certyfikatów za pośrednictwem SQL Server Management Studio, Otwórz okno dialogowe łączenie z serwerem. Kliknij pozycję Szyfruj połączenie na karcie Właściwości połączenia</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Wymuś zaszyfrowaną komunikację z programem SQL Server

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Lokalny |
| **Atrybuty**              | SQL Version-MsSQL2016, SQL Version-MsSQL2012, wersja SQL-MsSQL2014 |
| **Odwołania**              | [Włączanie szyfrowanych połączeń z aparatem bazy danych](/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine)  |
| **Kroki** | Włączenie szyfrowania TLS zwiększa bezpieczeństwo danych przesyłanych między wystąpieniami SQL Server i aplikacji. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Upewnij się, że komunikacja z usługą Azure Storage odbywa się za pośrednictwem protokołu HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Szyfrowanie Transport-Level usługi Azure Storage — używanie protokołu HTTPS](../../storage/blobs/security-recommendations.md#networking) |
| **Kroki** | Aby zapewnić bezpieczeństwo danych usługi Azure Storage, zawsze używaj protokołu HTTPS podczas wywoływania interfejsów API REST lub uzyskiwania dostępu do obiektów w magazynie. Ponadto sygnatury dostępu współdzielonego, które mogą być używane w celu delegowania dostępu do obiektów usługi Azure Storage, obejmują opcję określenia, że tylko protokół HTTPS może być używany podczas korzystania z sygnatur dostępu współdzielonego, dzięki czemu każdy wysyłane linki z tokenami SAS będą używały właściwego protokołu.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Weryfikuj skrót MD5 po pobieraniu obiektu BLOB, jeśli nie można włączyć protokołu HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | StorageType — obiekt BLOB |
| **Odwołania**              | [Windows Azure Blob MD5 — Omówienie](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Kroki** | <p>Usługa Windows Azure Blob service udostępnia mechanizmy zapewniające integralność danych zarówno na warstwach aplikacji, jak i transportowej. Jeśli z jakiegoś powodu musisz używać protokołu HTTP zamiast HTTPS i pracujesz z blokowymi obiektami BLOB, możesz użyć sprawdzania MD5, aby zweryfikować integralność transferowanych obiektów BLOB</p><p>Ułatwi to ochronę przed błędami warstwy sieci i transportu, ale nie musi być atakami pośredniczącymi. Jeśli można użyć protokołu HTTPS, który zapewnia zabezpieczenia na poziomie transportu, użycie sprawdzania MD5 jest zbędne i niepotrzebne.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Korzystanie z klienta zgodnego z protokołem SMB 3,0 w celu zapewnienia szyfrowania danych w ramach przesyłania do udziałów plików platformy Azure

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | StorageType — plik |
| **Odwołania**              | [Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [obsługa platformy Azure File Storage SMB dla klientów z systemem Windows](../../storage/files/storage-dotnet-how-to-use-files.md#understanding-the-net-apis) |
| **Kroki** | Usługa Azure File Storage obsługuje protokół HTTPS w przypadku korzystania z interfejsu API REST, ale jest bardziej często używany jako udział plików SMB dołączony do maszyny wirtualnej. Protokół SMB 2,1 nie obsługuje szyfrowania, dlatego połączenia są dozwolone tylko w obrębie tego samego regionu na platformie Azure. Protokół SMB 3,0 obsługuje jednak szyfrowanie i może być używany z systemem Windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10, co umożliwia dostęp między regionami, a nawet na pulpicie. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementuj Przypinanie certyfikatów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, Windows Phone |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Przypinanie certyfikatu i klucza publicznego](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Kroki** | <p>Przypinanie certyfikatu do obrony przed atakami typu man-in-the-Middle (MITM). Przypinanie to proces kojarzenia hosta z oczekiwanym certyfikatem x509 lub kluczem publicznym. Gdy certyfikat lub klucz publiczny jest znany lub widoczny dla hosta, certyfikat lub klucz publiczny są skojarzone lub przypięte do hosta. </p><p>W tym przypadku, gdy atakującej próbuje przeprowadzić atak metodą TLS MITM, podczas uzgadniania protokołu TLS klucz od serwera osoby atakującej będzie różny od klucza przypiętego certyfikatu, a żądanie zostanie odrzucone, w ten sposób zapobiega to zastępowaniu MITM certyfikatu przez zaimplementowanie delegata ServicePointManager `ServerCertificateValidationCallback` .</p>|

### <a name="example"></a>Przykład
```csharp
using System;
using System.Net;
using System.Net.Security;
using System.Security.Cryptography;

namespace CertificatePinningExample
{
    class CertificatePinningExample
    {
        /* Note: In this example, we're hardcoding a the certificate's public key and algorithm for 
           demonstration purposes. In a real-world application, this should be stored in a secure
           configuration area that can be updated as needed. */

        private static readonly string PINNED_ALGORITHM = "RSA";

        private static readonly string PINNED_PUBLIC_KEY = "3082010A0282010100B0E75B7CBE56D31658EF79B3A1" +
            "294D506A88DFCDD603F6EF15E7F5BCBDF32291EC50B2B82BA158E905FE6A83EE044A48258B07FAC3D6356AF09B2" +
            "3EDAB15D00507B70DB08DB9A20C7D1201417B3071A346D663A241061C151B6EC5B5B4ECCCDCDBEA24F051962809" +
            "FEC499BF2D093C06E3BDA7D0BB83CDC1C2C6660B8ECB2EA30A685ADE2DC83C88314010FFC7F4F0F895EDDBE5C02" +
            "ABF78E50B708E0A0EB984A9AA536BCE61A0C31DB95425C6FEE5A564B158EE7C4F0693C439AE010EF83CA8155750" +
            "09B17537C29F86071E5DD8CA50EBD8A409494F479B07574D83EDCE6F68A8F7D40447471D05BC3F5EAD7862FA748" +
            "EA3C92A60A128344B1CEF7A0B0D94E50203010001";


        public static void Main(string[] args)
        {
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("https://azure.microsoft.com");
            request.ServerCertificateValidationCallback = (sender, certificate, chain, sslPolicyErrors) =>
            {
                if (certificate == null || sslPolicyErrors != SslPolicyErrors.None)
                {
                    // Error getting certificate or the certificate failed basic validation
                    return false;
                }

                var targetKeyAlgorithm = new Oid(certificate.GetKeyAlgorithm()).FriendlyName;
                var targetPublicKey = certificate.GetPublicKeyString();
                
                if (targetKeyAlgorithm == PINNED_ALGORITHM &&
                    targetPublicKey == PINNED_PUBLIC_KEY)
                {
                    // Success, the certificate matches the pinned value.
                    return true;
                }
                // Reject, either the key or the algorithm does not match the expected value.
                return false;
            };

            try
            {
                var response = (HttpWebResponse)request.GetResponse();
                Console.WriteLine($"Success, HTTP status code: {response.StatusCode}");
            }
            catch(Exception ex)
            {
                Console.WriteLine($"Failure, {ex.Message}");
            }
            Console.WriteLine("Press any key to end.");
            Console.ReadKey();
        }
    }
}
```

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Włącz kanał transportu Secure HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Kroki** | Konfiguracja aplikacji powinna mieć pewność, że protokół HTTPS jest używany do uzyskiwania dostępu do poufnych informacji.<ul><li>**Wyjaśnienie:** Jeśli aplikacja obsługuje informacje poufne i nie korzysta z szyfrowania na poziomie komunikatu, powinien mieć możliwość komunikacji tylko z szyfrowanym kanałem transportowym.</li><li>**Zalecenia:** Upewnij się, że transport HTTP jest wyłączony i Włącz transport HTTPS. Na przykład Zastąp `<httpTransport/>` tag with `<httpsTransport/>` . Nie należy polegać na konfiguracji sieci (Zapora) w celu zagwarantowania, że dostęp do aplikacji będzie możliwy tylko za pośrednictwem bezpiecznego kanału. Z punktu widzenia philosophicala aplikacja nie powinna zależeć od sieci do jej zabezpieczeń.</li></ul><p>Z praktycznego punktu widzenia osoby odpowiedzialne za zabezpieczenie sieci nie zawsze śledzą wymagania dotyczące zabezpieczeń aplikacji podczas ich rozwoju.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Ustaw poziom ochrony zabezpieczeń wiadomości na EncryptAndSign

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff650862(v=pandp.10)) |
| **Kroki** | <ul><li>**Wyjaśnienie:** Jeśli poziom ochrony jest ustawiony na wartość "Brak", zostanie wyłączona Ochrona komunikatów. Poufność i integralność są uzyskiwane z odpowiednim poziomem ustawień.</li><li>**MAJĄC**<ul><li>gdy `Mode=None` -wyłącza ochronę wiadomości</li><li>gdy `Mode=Sign` — oznacza, ale nie szyfruje wiadomości; powinna być używana, jeśli integralność danych jest ważna</li><li>gdy `Mode=EncryptAndSign` — podpisuje i szyfruje komunikat</li></ul></li></ul><p>Rozważ wyłączenie szyfrowania i podpisywanie wiadomości tylko wtedy, gdy trzeba tylko sprawdzić integralność informacji bez obaw związanych z poufnością. Może to być przydatne w przypadku kontraktów operacji lub usług, w których należy zweryfikować oryginalnego nadawcy, ale nie są przesyłane poufne dane. Podczas zmniejszania poziomu ochrony należy zachować ostrożność, aby komunikat nie zawierał żadnych danych osobowych.</p>|

### <a name="example"></a>Przykład
Skonfigurowanie usługi i operacji do podpisywania wiadomości jest pokazane w poniższych przykładach. Przykład kontraktu usługi `ProtectionLevel.Sign` : poniżej przedstawiono przykład użycia ProtectionLevel. Sign na poziomie kontraktu usługi: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Przykład
Przykład kontraktu operacji `ProtectionLevel.Sign` (dla szczegółowej kontroli): poniżej przedstawiono przykład użycia `ProtectionLevel.Sign` na poziomie OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: Użyj konta z najniższymi uprawnieniami do uruchomienia usługi WCF

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648826(v=pandp.10)) |
| **Kroki** | <ul><li>**Wyjaśnienie:** Nie należy uruchamiać usług WCF w ramach konta administratora lub wysokiego poziomu uprawnień. w przypadku naruszenia bezpieczeństwa usługi spowodują duże konsekwencje.</li><li>**Zalecenia:** Użyj konta z najniższymi uprawnieniami do hostowania usługi WCF, ponieważ zmniejszy to podatność na ataki aplikacji i zmniejszy potencjalną szkodę w przypadku ataku. Jeśli konto usługi wymaga dodatkowych praw dostępu do zasobów infrastruktury, takich jak MSMQ, dziennik zdarzeń, liczniki wydajności i system plików, należy nadać tym zasobom odpowiednie uprawnienia, aby umożliwić pomyślne uruchomienie usługi WCF.</li></ul><p>Jeśli usługa musi uzyskać dostęp do określonych zasobów w imieniu oryginalnego obiektu wywołującego, użyj personifikacji i delegowania, aby przepływać tożsamość obiektu wywołującego dla kontroli autoryzacji podrzędnej. W scenariuszu deweloperskim Użyj konta usługi sieciowej lokalnego, które jest specjalnym kontem wbudowanym, które ma ograniczone uprawnienia. W scenariuszu produkcyjnym Utwórz najwyższe uprzywilejowane konto usługi domeny niestandardowej.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Wymuś cały ruch do interfejsów API sieci Web za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wymuszanie protokołu SSL w kontrolerze internetowego interfejsu API](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Kroki** | Jeśli aplikacja ma powiązanie HTTPS i HTTP, klienci mogą nadal korzystać z protokołu HTTP w celu uzyskania dostępu do lokacji. Aby tego uniknąć, należy użyć filtru akcji, aby upewnić się, że żądania do chronionych interfejsów API są zawsze za pośrednictwem protokołu HTTPS.|

### <a name="example"></a>Przykład 
Poniższy kod przedstawia filtr uwierzytelniania interfejsu API sieci Web, który sprawdza dostępność protokołu TLS: 
```csharp
public class RequireHttpsAttribute : AuthorizationFilterAttribute
{
    public override void OnAuthorization(HttpActionContext actionContext)
    {
        if (actionContext.Request.RequestUri.Scheme != Uri.UriSchemeHttps)
        {
            actionContext.Response = new HttpResponseMessage(System.Net.HttpStatusCode.Forbidden)
            {
                ReasonPhrase = "HTTPS Required"
            };
        }
        else
        {
            base.OnAuthorization(actionContext);
        }
    }
}
```
Dodaj ten filtr do wszystkich akcji internetowego interfejsu API, które wymagają protokołu TLS: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-tls"></a><a id="redis-ssl"></a>Upewnij się, że komunikacja z usługą Azure cache for Redis odbywa się za pośrednictwem protokołu TLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Cache for Redis | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Obsługa protokołu TLS w usłudze Azure Redis](../../azure-cache-for-redis/cache-faq.md) |
| **Kroki** | Serwer Redis nie obsługuje protokołu TLS, ale usługa Azure cache for Redis działa. Jeśli łączysz się z usługą Azure cache for Redis, a klient obsługuje protokół TLS, taki jak StackExchange. Redis, należy użyć protokołu TLS. Domyślnie port inny niż TLS jest wyłączony dla nowej pamięci podręcznej platformy Azure dla wystąpień Redis. Upewnij się, że zabezpieczone wartości domyślne nie są zmieniane, chyba że istnieje zależność od obsługi protokołu TLS dla klientów Redis. |

Należy pamiętać, że Redis jest dostępny dla zaufanych klientów w zaufanych środowiskach. Oznacza to, że zwykle nie jest dobrym pomysłem uwidocznienia wystąpienia Redis bezpośrednio w Internecie lub, ogólnie, do środowiska, w którym niezaufani klienci mogą bezpośrednio uzyskać dostęp do portu TCP Redis lub gniazda UNIX. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Zabezpieczanie urządzenia do komunikacji z bramą pola

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | W przypadku urządzeń opartych na protokole IP protokół komunikacyjny może być zazwyczaj hermetyzowany w kanale SSL/TLS w celu ochrony danych podczas przesyłania. W przypadku innych protokołów, które nie obsługują protokołu SSL/TLS, jeśli istnieją bezpieczne wersje protokołu, które zapewniają zabezpieczenia w transportowej lub warstwie komunikatów. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Zabezpieczanie urządzenia do komunikacji z bramą w chmurze przy użyciu protokołu SSL/TLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama usługi IoT Cloud | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wybieranie protokołu komunikacyjnego](../../iot-hub/iot-hub-devguide.md) |
| **Kroki** | Zabezpieczanie protokołów HTTP/AMQP lub MQTT przy użyciu protokołu SSL/TLS. |