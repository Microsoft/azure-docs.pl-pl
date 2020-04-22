---
title: Zabezpieczenia komunikacji dla narzędzia Microsoft Threat Modeling Tool
titleSuffix: Azure
description: zagrożeniach ujawnionych w narzędziu do modelowania zagrożeń
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
ms.openlocfilehash: 1945025ff89a784908a1a3dffd2240172a6e2449
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/21/2020
ms.locfileid: "81687999"
---
# <a name="security-frame-communication-security--mitigations"></a>Rama zabezpieczeń: Bezpieczeństwo komunikacji | Czynniki 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Centrum zdarzeń Azure** | <ul><li>[Bezpieczna komunikacja z Centrum zdarzeń przy użyciu protokołu SSL/TLS](#comm-ssltls)</li></ul> |
| **Dynamics CRM** | <ul><li>[Sprawdź uprawnienia do konta usługi i sprawdź, czy niestandardowe usługi lub strony ASP.NET są zgodne z zabezpieczeniami programu CRM](#priv-aspnet)</li></ul> |
| **Azure Data Factory** | <ul><li>[Korzystanie z bramy zarządzania danymi podczas łączenia lokalnego programu SQL Server z usługą Azure Data Factory](#sqlserver-factory)</li></ul> |
| **Serwer tożsamości** | <ul><li>[Upewnij się, że cały ruch na serwerze tożsamości jest za pośrednictwem połączenia HTTPS](#identity-https)</li></ul> |
| **Aplikacja internetowa** | <ul><li>[Weryfikowanie certyfikatów X.509 używanych do uwierzytelniania połączeń SSL, TLS i DTLS](#x509-ssltls)</li><li>[Konfigurowanie certyfikatu SSL dla domeny niestandardowej w usłudze Azure App Service](#ssl-appservice)</li><li>[Wymuszanie całego ruchu w usłudze Azure App Service za pośrednictwem połączenia HTTPS](#appservice-https)</li><li>[Włącz ścisłe zabezpieczenia transportu HTTP (HSTS)](#http-hsts)</li></ul> |
| **baza danych** | <ul><li>[Zapewnia szyfrowanie połączenia z serwerem SQL i sprawdzanie poprawności certyfikatów](#sqlserver-validation)</li><li>[Wymuszanie szyfrowanej komunikacji z serwerem SQL](#encrypted-sqlserver)</li></ul> |
| **Azure Storage** | <ul><li>[Upewnij się, że komunikacja z usługą Azure Storage jest za pośrednictwem protokołu HTTPS](#comm-storage)</li><li>[Sprawdzanie poprawności skrótu MD5 po pobraniu obiektu blob, jeśli nie można włączyć protokołu HTTPS](#md5-https)</li><li>[Użyj klienta zgodnego z protokołem SMB 3.0, aby zapewnić szyfrowanie danych podczas przesyłania do udziałów plików platformy Azure](#smb-shares)</li></ul> |
| **Klient mobilny** | <ul><li>[Implementowanie przypinania certyfikatów](#cert-pinning)</li></ul> |
| **WCF** | <ul><li>[Włącz https - kanał bezpiecznego transportu](#https-transport)</li><li>[WCF: Ustaw poziom ochrony zabezpieczeń wiadomości na EncryptAndSign](#message-protection)</li><li>[WCF: Uruchamianie usługi WCF: używanie konta o najniższych uprawnieniach](#least-account-wcf)</li></ul> |
| **Interfejs API sieci Web** | <ul><li>[Wymuszanie całego ruchu do interfejsów API sieci Web za pośrednictwem połączenia HTTPS](#webapi-https)</li></ul> |
| **Azure Cache for Redis** | <ul><li>[Upewnij się, że komunikacja z pamięcią podręczną Azure cache for Redis jest za pomocą ssl](#redis-ssl)</li></ul> |
| **Brama pola IoT** | <ul><li>[Bezpieczna komunikacja z bramą z urządzeniem w terenie](#device-field)</li></ul> |
| **Brama w chmurze IoT** | <ul><li>[Bezpieczna komunikacja z aplikacją Device to Cloud Gateway przy użyciu protokołu SSL/TLS](#device-cloud)</li></ul> |

## <a name="secure-communication-to-event-hub-using-ssltls"></a><a id="comm-ssltls"></a>Bezpieczna komunikacja z Centrum zdarzeń przy użyciu protokołu SSL/TLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Centrum zdarzeń Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Omówienie uwierzytelniania i modelu zabezpieczeń centrum zdarzeń](https://azure.microsoft.com/documentation/articles/event-hubs-authentication-and-security-model-overview/) |
| **Kroki** | Bezpieczne połączenia PROTOKOŁU AMQP lub HTTP z Centrum zdarzeń przy użyciu protokołu SSL/TLS |

## <a name="check-service-account-privileges-and-check-that-the-custom-services-or-aspnet-pages-respect-crms-security"></a><a id="priv-aspnet"></a>Sprawdź uprawnienia do konta usługi i sprawdź, czy niestandardowe usługi lub strony ASP.NET są zgodne z zabezpieczeniami programu CRM

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | Sprawdź uprawnienia do konta usługi i sprawdź, czy niestandardowe usługi lub strony ASP.NET są zgodne z zabezpieczeniami programu CRM |

## <a name="use-data-management-gateway-while-connecting-on-premises-sql-server-to-azure-data-factory"></a><a id="sqlserver-factory"></a>Korzystanie z bramy zarządzania danymi podczas łączenia lokalnego programu SQL Server z usługą Azure Data Factory

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Data Factory | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Połączone typy usług — azure i lokalne |
| **Dokumentacja**              |[Przenoszenie danych między lokalnymi i usługi Azure Data Factory](https://azure.microsoft.com/documentation/articles/data-factory-move-data-between-onprem-and-cloud/#create-gateway), [brama zarządzania danymi](https://azure.microsoft.com/documentation/articles/data-factory-data-management-gateway/) |
| **Kroki** | <p>Narzędzie DMG (Data Management Gateway) jest wymagane do łączenia się ze źródłami danych chronionymi za corpnet lub zaporą.</p><ol><li>Zablokowanie urządzenia izoluje narzędzie DMG i zapobiega uszkodzeniu lub podsłuchiwaniu przez nieprawidłowe działanie programów na komputerze ze źródłem danych. (Np. należy zainstalować najnowsze aktualizacje, włączyć minimalne wymagane porty, kontrolować obsługę obsługi administracyjną kont, włączono inspekcję, włączone szyfrowanie dysków itp.)</li><li>Klucz bramy danych musi być obracany w częstych odstępach czasu lub za każdym razem, gdy odnawia się hasło konta usługi DMG</li><li>Przesyłanie danych za pośrednictwem usługi Link Service musi być zaszyfrowane</li></ol> |

## <a name="ensure-that-all-traffic-to-identity-server-is-over-https-connection"></a><a id="identity-https"></a>Upewnij się, że cały ruch na serwerze tożsamości jest za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Serwer tożsamości | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [IdentityServer3 - Klucze, podpisy i kryptografia](https://identityserver.github.io/Documentation/docsv2/configuration/crypto.html), [IdentityServer3 - Wdrażanie](https://identityserver.github.io/Documentation/docsv2/advanced/deployment.html) |
| **Kroki** | Domyślnie IdentityServer wymaga wszystkich połączeń przychodzących do za pośrednictwem PROTOKOŁU HTTPS. Jest absolutnie obowiązkowe, że komunikacja z IdentityServer odbywa się tylko za pomocą zabezpieczonych transportów. Istnieją pewne scenariusze wdrażania, takie jak odciążanie SSL, gdzie to wymaganie może być złagodzone. Więcej informacji można znaleźć na stronie wdrażania serwera tożsamości w odwołaniach. |

## <a name="verify-x509-certificates-used-to-authenticate-ssl-tls-and-dtls-connections"></a><a id="x509-ssltls"></a>Weryfikowanie certyfikatów X.509 używanych do uwierzytelniania połączeń SSL, TLS i DTLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | <p>Aplikacje korzystające z protokołu SSL, TLS lub DTLS muszą w pełni zweryfikować certyfikaty X.509 jednostek, z którymi się łączą. Obejmuje to weryfikację certyfikatów dla:</p><ul><li>Nazwa domeny</li><li>Daty ważności (zarówno daty rozpoczęcia, jak i wygaśnięcia)</li><li>Stan odwołania</li><li>Użycie (na przykład uwierzytelnianie serwera dla serwerów, uwierzytelnianie klienta dla klientów)</li><li>Łańcuch zaufania. Certyfikaty muszą wiązać się z głównym urzędem certyfikacji (CA), który jest zaufany przez platformę lub jawnie skonfigurowany przez administratora</li><li>Długość klucza publicznego certyfikatu musi być >2048 bitów</li><li>Algorytm mieszania musi być SHA256 i powyżej |

## <a name="configure-ssl-certificate-for-custom-domain-in-azure-app-service"></a><a id="ssl-appservice"></a>Konfigurowanie certyfikatu SSL dla domeny niestandardowej w usłudze Azure App Service

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typ środowiska — platforma Azure |
| **Dokumentacja**              | [Włączanie protokołu HTTPS dla aplikacji w usłudze Azure App Service](../../app-service/configure-ssl-bindings.md) |
| **Kroki** | Domyślnie platforma Azure już włącza protokół HTTPS dla każdej aplikacji z certyfikatem symboli wieloznacznych dla domeny *.azurewebsites.net. Jednak podobnie jak wszystkie domeny wieloznaczne, nie jest tak bezpieczne, jak przy użyciu domeny niestandardowej z własnym certyfikatem [Refer](https://casecurity.org/2014/02/26/pros-and-cons-of-single-domain-multi-domain-and-wildcard-certificates/). Zaleca się włączenie ssl dla domeny niestandardowej, do której będzie dostępna wdrożona aplikacja za pośrednictwem|

## <a name="force-all-traffic-to-azure-app-service-over-https-connection"></a><a id="appservice-https"></a>Wymuszanie całego ruchu w usłudze Azure App Service za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typ środowiska — platforma Azure |
| **Dokumentacja**              | [Wymuszanie protokołu HTTPS w usłudze Azure App Service](../../app-service/configure-ssl-bindings.md#enforce-https) |
| **Kroki** | <p>Chociaż platforma Azure już włącza usługi aplikacji HTTPS dla platformy Azure z certyfikatem wieloznacznym dla domeny *.azurewebsites.net, nie wymusza protokołu HTTPS. Użytkownicy mogą nadal uzyskiwać dostęp do aplikacji przy użyciu protokołu HTTP, co może naruszyć bezpieczeństwo aplikacji, a zatem protokół HTTPS musi być jawnie wymuszany. ASP.NET aplikacje MVC powinny używać [filtru RequireHttps,](https://msdn.microsoft.com/library/system.web.mvc.requirehttpsattribute.aspx) który wymusza ponowne wysłanie niezabezpieczonego żądania HTTP za pośrednictwem protokołu HTTPS.</p><p>Alternatywnie moduł przepisywania adresów URL, który jest dołączony do usługi Azure App Service może służyć do wymuszania protokołu HTTPS. Moduł przepisywania adresów URL umożliwia deweloperom definiowanie reguł, które są stosowane do żądań przychodzących, zanim żądania zostaną przekazane do aplikacji. Reguły przepisywania adresów URL są definiowane w pliku web.config przechowywanym w katalogu głównym aplikacji</p>|

### <a name="example"></a>Przykład
Poniższy przykład zawiera podstawową regułę przepisywania adresów URL, która zmusza cały ruch przychodzący do korzystania z protokołu HTTPS
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
Ta reguła działa po zwróceniu kodu stanu HTTP 301 (stałe przekierowanie), gdy użytkownik zażąda strony przy użyciu protokołu HTTP. 301 przekierowuje żądanie do tego samego adresu URL, co żądany użytkownik, ale zastępuje część HTTP żądania https. Na przykład `HTTP://contoso.com` zostanie przekierowany `HTTPS://contoso.com`do . 

## <a name="enable-http-strict-transport-security-hsts"></a><a id="http-hsts"></a>Włącz ścisłe zabezpieczenia transportu HTTP (HSTS)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [OWASP HTTP Ścisła karta do bezpieczeństwa transportu](https://cheatsheetseries.owasp.org/cheatsheets/HTTP_Strict_Transport_Security_Cheat_Sheet.html) |
| **Kroki** | <p>HTTP Strict Transport Security (HSTS) to ulepszenie zabezpieczeń opt-in, które jest określone przez aplikację sieci web za pomocą nagłówka specjalnej odpowiedzi. Gdy obsługiwana przeglądarka otrzyma ten nagłówek, przeglądarka uniemożliwi wysyłanie komunikacji za pośrednictwem protokołu HTTP do określonej domeny, a zamiast tego wyśle całą komunikację za pośrednictwem protokołu HTTPS. Zapobiega to również klikaniu protokołu HTTPS w przeglądarkach.</p><p>Aby zaimplementować HSTS, następujący nagłówek odpowiedzi musi być skonfigurowany dla witryny sieci Web globalnie, w kodzie lub w konfiguracji. Ścisłe-Transport-Security: max-age = 300; includeSubDomains HSTS rozwiązuje następujące zagrożenia:</p><ul><li>Zakładki użytkownika lub ręcznie `https://example.com` wpisują i podlegają osobie atakującej typu man-in-the-middle: HSTS automatycznie przekierowuje żądania HTTP do protokołu HTTPS dla domeny docelowej</li><li>Aplikacja sieci Web, która ma być czysto HTTPS przypadkowo zawiera łącza HTTP lub obsługuje zawartość za pośrednictwem HTTP: HSTS automatycznie przekierowuje żądania HTTP do HTTPS dla domeny docelowej</li><li>Osoba atakująca próbuje przechwycić ruch od użytkownika ofiary przy użyciu nieprawidłowego certyfikatu i ma nadzieję, że użytkownik zaakceptuje zły certyfikat: HSTS nie zezwala użytkownikowi na zastąpienie nieprawidłowego komunikatu certyfikatu</li></ul>|

## <a name="ensure-sql-server-connection-encryption-and-certificate-validation"></a><a id="sqlserver-validation"></a>Zapewnia szyfrowanie połączenia z serwerem SQL i sprawdzanie poprawności certyfikatów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Usługi SQL Azure  |
| **Atrybuty**              | Wersja SQL - V12 |
| **Dokumentacja**              | [Najważniejsze wskazówki dotyczące zapisywania ciągów bezpiecznego połączenia dla bazy danych SQL](https://social.technet.microsoft.com/wiki/contents/articles/2951.windows-azure-sql-database-connection-security.aspx#best) |
| **Kroki** | <p>Cała komunikacja między bazą danych SQL a aplikacją kliencką jest zawsze szyfrowana przy użyciu warstwy SSL (Secure Sockets Layer). Baza danych SQL nie obsługuje połączeń niezaszyfrowanych. Aby sprawdzić poprawność certyfikatów za pomocą kodu aplikacji lub narzędzi, jawnie zażądaj połączenia szyfrowanego i nie ufaj certyfikatom serwera. Jeśli kod aplikacji lub narzędzia nie żądają połączenia szyfrowanego, nadal będą odbierać połączenia szyfrowane</p><p>Jednak nie mogą one zweryfikować certyfikaty serwera, a tym samym będą podatne na ataki "człowiek w środku". Aby sprawdzić poprawność certyfikatów za pomocą `Encrypt=True` `TrustServerCertificate=False` kodu aplikacji ADO.NET, ustaw i w ciągu połączenia bazy danych. Aby sprawdzić poprawność certyfikatów za pośrednictwem programu SQL Server Management Studio, otwórz okno dialogowe Połącz z serwerem. Kliknij pozycję Szyfruj połączenie na karcie Właściwości połączenia</p>|

## <a name="force-encrypted-communication-to-sql-server"></a><a id="encrypted-sqlserver"></a>Wymuszanie szyfrowanej komunikacji z serwerem SQL

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | OnPrem ( OnPrem ) |
| **Atrybuty**              | Wersja SQL — MsSQL2016, wersja SQL - MsSQL2012, wersja SQL — MsSQL2014 |
| **Dokumentacja**              | [Włączanie połączeń szyfrowanych z aparatem bazy danych](https://msdn.microsoft.com/library/ms191192)  |
| **Kroki** | Włączenie szyfrowania SSL zwiększa bezpieczeństwo danych przesyłanych przez sieci między wystąpieniami programu SQL Server i aplikacji. |

## <a name="ensure-that-communication-to-azure-storage-is-over-https"></a><a id="comm-storage"></a>Upewnij się, że komunikacja z usługą Azure Storage jest za pośrednictwem protokołu HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Szyfrowanie na poziomie transportu usługi Azure Storage — przy użyciu protokołu HTTPS](https://azure.microsoft.com/documentation/articles/storage-security-guide/#_encryption-in-transit) |
| **Kroki** | Aby zapewnić bezpieczeństwo przesyłanych danych usługi Azure Storage, należy zawsze używać protokołu HTTPS podczas wywoływania interfejsów API REST lub uzyskiwania dostępu do obiektów w magazynie. Ponadto podpisy dostępu współdzielonego, które mogą służyć do delegowania dostępu do obiektów usługi Azure Storage, zawierają opcję określającą, że tylko protokół HTTPS może być używany podczas korzystania z podpisów dostępu współdzielonego, zapewniając, że każda wysyłająca łącza z tokenami sygnatury dostępu współdzielonego użyje odpowiedniego protokołu.|

## <a name="validate-md5-hash-after-downloading-blob-if-https-cannot-be-enabled"></a><a id="md5-https"></a>Sprawdzanie poprawności skrótu MD5 po pobraniu obiektu blob, jeśli nie można włączyć protokołu HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typ magazynu — obiekt blob |
| **Dokumentacja**              | [Omówienie programu Windows Azure Blob MD5](https://blogs.msdn.microsoft.com/windowsazurestorage/2011/02/17/windows-azure-blob-md5-overview/) |
| **Kroki** | <p>Usługa obiektów Blob systemu Windows Azure udostępnia mechanizmy zapewniające integralność danych zarówno w warstwach aplikacji, jak i transportu. Jeśli z jakiegoś powodu musisz użyć protokołu HTTP zamiast HTTPS i pracujesz z blokowymi obiektami blob, możesz użyć sprawdzania MD5, aby sprawdzić integralność przesyłanych obiektów blob</p><p>Pomoże to w ochronie przed błędami warstwy sieci/transportu, ale niekoniecznie w razie ataków pośredniczących. Jeśli można użyć protokołu HTTPS, który zapewnia bezpieczeństwo na poziomie transportu, a następnie przy użyciu md5 sprawdzanie jest nadmiarowe i niepotrzebne.</p>|

## <a name="use-smb-30-compatible-client-to-ensure-in-transit-data-encryption-to-azure-file-shares"></a><a id="smb-shares"></a>Użyj klienta zgodnego z protokołem SMB 3.0, aby zapewnić szyfrowanie danych podczas przesyłania do udziałów plików platformy Azure

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Typ magazynu — plik |
| **Dokumentacja**              | [Usługa Azure File Storage](https://azure.microsoft.com/blog/azure-file-storage-now-generally-available/#comment-2529238931), [obsługa SMB usługi Azure File Storage dla klientów systemu Windows](https://azure.microsoft.com/documentation/articles/storage-dotnet-how-to-use-files/#_mount-the-file-share) |
| **Kroki** | Usługa Azure File Storage obsługuje protokół HTTPS podczas korzystania z interfejsu API REST, ale jest częściej używany jako udział plików SMB dołączony do maszyny Wirtualnej. SMB 2.1 nie obsługuje szyfrowania, więc połączenia są dozwolone tylko w tym samym regionie na platformie Azure. Jednak protokół SMB 3.0 obsługuje szyfrowanie i może być używany z systemami Windows Server 2012 R2, Windows 8, Windows 8.1 i Windows 10, umożliwiając dostęp między regionami, a nawet dostęp na pulpicie. |

## <a name="implement-certificate-pinning"></a><a id="cert-pinning"></a>Implementowanie przypinania certyfikatów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny, Windows Phone |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Przypinanie certyfikatu i klucza publicznego](https://owasp.org/www-community/controls/Certificate_and_Public_Key_Pinning) |
| **Kroki** | <p>Przypinanie certyfikatu broni się przed atakami Man-In-The-Middle (MITM). Przypinanie to proces kojarzenia hosta z oczekiwanym certyfikatem x509 lub kluczem publicznym. Gdy certyfikat lub klucz publiczny jest znany lub widoczny dla hosta, certyfikat lub klucz publiczny jest skojarzony lub "przypięty" do hosta. </p><p>W związku z tym, gdy przeciwnik próbuje wykonać atak SSL MITM, podczas uzgadniania SSL klucz z serwera atakującego będzie się różnić od klucza przypiętego certyfikatu, a żądanie `ServerCertificateValidationCallback` zostanie odrzucone, zapobiegając w ten sposób przypinaniu certyfikatu MITM można osiągnąć, implementując pełnomocnika programu ServicePointManager.</p>|

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

## <a name="enable-https---secure-transport-channel"></a><a id="https-transport"></a>Włącz https - kanał bezpiecznego transportu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Struktura NET 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [MSDN](https://msdn.microsoft.com/library/ff648500.aspx), [Wzmacnianie Królestwa](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_transport_security_enabled) |
| **Kroki** | Konfiguracja aplikacji powinna zapewnić, że protokół HTTPS jest używany dla wszystkich dostępu do poufnych informacji.<ul><li>**WYJAŚNIENIE:** Jeśli aplikacja obsługuje poufne informacje i nie używa szyfrowania na poziomie wiadomości, powinna być dozwolona tylko do komunikowania się za pomocą zaszyfrowanego kanału transportu.</li><li>**ZALECENIA:** Upewnij się, że transport HTTP jest wyłączony i zamiast tego włącz transport HTTPS. Na przykład zastąp `<httpTransport/>` `<httpsTransport/>` tag. Nie należy polegać na konfiguracji sieci (zapory), aby zagwarantować, że aplikacja może być dostępna tylko za pośrednictwem bezpiecznego kanału. Z filozoficznego punktu widzenia aplikacja nie powinna zależeć od sieci ze względu na jej bezpieczeństwo.</li></ul><p>Z praktycznego punktu widzenia osoby odpowiedzialne za zabezpieczenie sieci nie zawsze śledzą wymagania bezpieczeństwa aplikacji w miarę ich rozwoju.</p>|

## <a name="wcf-set-message-security-protection-level-to-encryptandsign"></a><a id="message-protection"></a>WCF: Ustaw poziom ochrony zabezpieczeń wiadomości na EncryptAndSign

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [MSDN](https://msdn.microsoft.com/library/ff650862.aspx) |
| **Kroki** | <ul><li>**WYJAŚNIENIE:** Gdy poziom ochrony jest ustawiony na "brak", wyłączy ochronę wiadomości. Poufność i integralność jest osiągana przy odpowiednim poziomie ustalania.</li><li>**Zalecenia:**<ul><li>kiedy `Mode=None` - Wyłącza ochronę wiadomości</li><li>kiedy `Mode=Sign` - Podpisuje, ale nie szyfruje wiadomości; powinny być stosowane, gdy integralność danych jest ważna</li><li>kiedy `Mode=EncryptAndSign` - Podpisuje i szyfruje wiadomość</li></ul></li></ul><p>Należy rozważyć wyłączenie szyfrowania i podpisywanie wiadomości tylko wtedy, gdy wystarczy zweryfikować integralność informacji bez obaw o poufność. Może to być przydatne w przypadku operacji lub umów serwisowych, w których należy sprawdzić poprawność oryginalnego nadawcy, ale nie są przesyłane żadne poufne dane. Zmniejszając poziom ochrony, należy uważać, aby wiadomość nie zawierała żadnych danych osobowych.</p>|

### <a name="example"></a>Przykład
Konfigurowanie usługi i operacji, aby tylko podpisać komunikat jest wyświetlany w poniższych przykładach. Przykład umowy `ProtectionLevel.Sign`serwisowej : Poniższy przykład jest przykładem użycia ProtectionLevel.Sign na poziomie umowy serwisowej: 
```
[ServiceContract(Protection Level=ProtectionLevel.Sign] 
public interface IService 
  { 
  string GetData(int value); 
  } 
```

### <a name="example"></a>Przykład
Przykład kontraktu `ProtectionLevel.Sign` operacji (dla kontroli granulowany): Poniższy przykład jest przykładem użycia `ProtectionLevel.Sign` na poziomie OperationContract:

```
[OperationContract(ProtectionLevel=ProtectionLevel.Sign] 
string GetData(int value);
``` 

## <a name="wcf-use-a-least-privileged-account-to-run-your-wcf-service"></a><a id="least-account-wcf"></a>WCF: Uruchamianie usługi WCF: używanie konta o najniższych uprawnieniach

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [MSDN](https://msdn.microsoft.com/library/ff648826.aspx ) |
| **Kroki** | <ul><li>**WYJAŚNIENIE:** Nie należy uruchamiać usług WCF w ramach konta administratora lub konta o wysokich uprawnieniach. w przypadku naruszenia bezpieczeństwa usług będzie to miało duży wpływ.</li><li>**ZALECENIA:** Użyj konta najmniej uprzywilejowanego do hostowania usługi WCF, ponieważ zmniejszy to obszar ataku aplikacji i zmniejszy potencjalne obrażenia w przypadku ataku. Jeśli konto usługi wymaga dodatkowych praw dostępu do zasobów infrastruktury, takich jak MSMQ, dziennik zdarzeń, liczniki wydajności i system plików, należy nałożyć odpowiednie uprawnienia do tych zasobów, aby usługa WCF mogła zostać pomyślnie uruchomiony.</li></ul><p>Jeśli usługa musi uzyskać dostęp do określonych zasobów w imieniu oryginalnego obiektu wywołującego, użyj personifikacji i delegowania do przepływu tożsamości wywołującego do sprawdzania autoryzacji podrzędnej. W scenariuszu rozwoju użyj konta usługi sieci lokalnej, które jest specjalnym kontem wbudowanym, które zmniejszyło uprawnienia. W scenariuszu produkcyjnym utwórz najmniej uprzywilejowane konto usługi domeny niestandardowej.</p>|

## <a name="force-all-traffic-to-web-apis-over-https-connection"></a><a id="webapi-https"></a>Wymuszanie całego ruchu do interfejsów API sieci Web za pośrednictwem połączenia HTTPS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Wymuszanie ssl w kontrolerze interfejsu API sieci Web](https://www.asp.net/web-api/overview/security/working-with-ssl-in-web-api) |
| **Kroki** | Jeśli aplikacja ma zarówno powiązanie HTTPS, jak i HTTP, klienci mogą nadal używać protokołu HTTP, aby uzyskać dostęp do witryny. Aby temu zapobiec, należy użyć filtru akcji, aby upewnić się, że żądania do chronionych interfejsów API są zawsze za pośrednictwem protokołu HTTPS.|

### <a name="example"></a>Przykład 
Poniższy kod przedstawia filtr uwierzytelniania interfejsu API sieci Web, który sprawdza dostępność ssl: 
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
Dodaj ten filtr do wszystkich akcji interfejsu API sieci Web, które wymagają protokołu SSL: 
```csharp
public class ValuesController : ApiController
{
    [RequireHttps]
    public HttpResponseMessage Get() { ... }
}
```
 
## <a name="ensure-that-communication-to-azure-cache-for-redis-is-over-ssl"></a><a id="redis-ssl"></a>Upewnij się, że komunikacja z pamięcią podręczną Azure cache for Redis jest za pomocą ssl

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Cache for Redis | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Pomoc techniczna usługi Azure Redis SSL](https://azure.microsoft.com/documentation/articles/cache-faq/#when-should-i-enable-the-non-ssl-port-for-connecting-to-redis) |
| **Kroki** | Serwer Redis nie obsługuje ssl po wyjęciu z pudełka, ale usługa Azure Cache for Redis nie. Jeśli łączysz się z pamięcią podręczną Azure dla programu Redis, a klient obsługuje protokół SSL, taki jak StackExchange.Redis, należy użyć pliku SSL. Domyślnie port nieobjęty SSL jest wyłączony dla nowej pamięci podręcznej platformy Azure dla wystąpień Redis. Upewnij się, że bezpieczne ustawienia domyślne nie zostaną zmienione, chyba że istnieje zależność od obsługi SSL dla klientów redis. |

Należy pamiętać, że Redis jest przeznaczony do uzyskiwania dostępu przez zaufanych klientów w zaufanych środowiskach. Oznacza to, że zwykle nie jest dobrym pomysłem, aby udostępnić wystąpienie Redis bezpośrednio do Internetu lub, ogólnie, do środowiska, w którym niezaufani klienci mogą bezpośrednio uzyskać dostęp do portu Redis TCP lub gniazda UNIX. 

## <a name="secure-device-to-field-gateway-communication"></a><a id="device-field"></a>Bezpieczna komunikacja z bramą z urządzeniem w terenie

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama pola IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | Nie dotyczy  |
| **Kroki** | W przypadku urządzeń opartych na adresie IP protokół komunikacyjny zazwyczaj może być hermetyzowany w kanale SSL/TLS w celu ochrony przesyłanych danych. W przypadku innych protokołów, które nie obsługują protokołu SSL/TLS, należy zbadać, czy istnieją bezpieczne wersje protokołu, które zapewniają bezpieczeństwo w warstwie transportu lub wiadomości. |

## <a name="secure-device-to-cloud-gateway-communication-using-ssltls"></a><a id="device-cloud"></a>Bezpieczna komunikacja z aplikacją Device to Cloud Gateway przy użyciu protokołu SSL/TLS

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Brama w chmurze IoT | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Dokumentacja**              | [Wybierz swój protokół komunikacyjny](https://azure.microsoft.com/documentation/articles/iot-hub-devguide/#messaging) |
| **Kroki** | Bezpieczne protokoły HTTP/AMQP lub MQTT przy użyciu protokołu SSL/TLS. |
