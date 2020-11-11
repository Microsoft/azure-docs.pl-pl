---
title: Dane poufne — Microsoft Threat Modeling Tool na platformie Azure | Microsoft Docs
description: Dowiedz się więcej na temat ograniczania wrażliwych danych w Threat Modeling Tool. Zobacz informacje o ograniczeniach i Wyświetl przykłady kodu.
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
ms.custom: devx-track-js, devx-track-csharp
ms.openlocfilehash: 0bcbe35fc6d9f104325bec8a3404ad57a6376cf2
ms.sourcegitcommit: 5831eebdecaa68c3e006069b3a00f724bea0875a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/11/2020
ms.locfileid: "94518128"
---
# <a name="security-frame-sensitive-data--mitigations"></a>Ramka zabezpieczeń: dane poufne | Środki zaradcze 
| Produkt/usługa | Artykuł |
| --------------- | ------- |
| **Granica zaufania maszyny** | <ul><li>[Upewnij się, że pliki binarne są zasłaniane, jeśli zawierają informacje poufne](#binaries-info)</li><li>[Rozważ użycie szyfrowanego systemu plików (EFS) służy do ochrony poufnych danych specyficznych dla użytkownika](#efs-user)</li><li>[Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików są szyfrowane](#filesystem)</li></ul> | 
| **Aplikacja sieci Web** | <ul><li>[Upewnij się, że poufna zawartość nie jest buforowana w przeglądarce](#cache-browser)</li><li>[Szyfruj sekcje plików konfiguracji aplikacji sieci Web, które zawierają dane poufne](#encrypt-data)</li><li>[Jawnie wyłącz atrybut HTML autouzupełniania w poufnych formularzach i danych wejściowych](#autocomplete-input)</li><li>[Upewnij się, że poufne dane wyświetlane na ekranie użytkownika są maskowane](#data-mask)</li></ul> | 
| **Database** (Baza danych) | <ul><li>[Zaimplementuj Dynamiczne maskowanie danych, aby ograniczyć narażenie danych poufnych na nieuprzywilejowanych użytkowników](#dynamic-users)</li><li>[Upewnij się, że hasła są przechowywane w formacie solonego skrótu](#salted-hash)</li><li>[Upewnij się, że poufne dane w kolumnach bazy danych są szyfrowane](#db-encrypted)</li><li>[Upewnij się, że szyfrowanie na poziomie bazy danych (TDE) jest włączone](#tde-enabled)</li><li>[Upewnij się, że kopie zapasowe bazy danych są szyfrowane](#backup)</li></ul> | 
| **Interfejs API sieci Web** | <ul><li>[Upewnij się, że poufne dane istotne dla interfejsu API sieci Web nie są przechowywane w magazynie przeglądarki](#api-browser)</li></ul> | 
| Baza danych dokumentów platformy Azure | <ul><li>[Szyfruj poufne dane przechowywane w Azure Cosmos DB](#encrypt-docdb)</li></ul> | 
| **Granica zaufania maszyny wirtualnej usługi Azure IaaS** | <ul><li>[Użyj Azure Disk Encryption do szyfrowania dysków używanych przez Virtual Machines](#disk-vm)</li></ul> | 
| **Service Fabric granic zaufania** | <ul><li>[Szyfruj wpisy tajne w aplikacjach Service Fabric](#fabric-apps)</li></ul> | 
| **Dynamics CRM** | <ul><li>[Przeprowadzenie modelowania zabezpieczeń i użycie jednostek handlowych/zespołów, gdy jest to wymagane](#modeling-teams)</li><li>[Minimalizuj dostęp do funkcji udostępniania na jednostkach krytycznych](#entities)</li><li>[Uczenie użytkowników o ryzyku związanym z funkcją udziału Dynamics CRM i dobrą praktyką w zakresie zabezpieczeń](#good-practices)</li><li>[Uwzględnij reguły proscribing dla deweloperów przedstawiające szczegóły konfiguracji w zarządzaniu wyjątkami](#exception-mgmt)</li></ul> | 
| **Azure Storage** | <ul><li>[Użyj usługi Azure szyfrowanie usługi Storage (SSE) dla danych przechowywanych w spoczynku (wersja zapoznawcza)](#sse-preview)</li><li>[Używanie szyfrowania Client-Side do przechowywania danych poufnych w usłudze Azure Storage](#client-storage)</li></ul> | 
| **Klient mobilny** | <ul><li>[Szyfruj poufne lub dane OSOBowe zapisywane w lokalnym magazynie telefonów](#pii-phones)</li><li>[Zaciemniaj wygenerowane pliki binarne przed dystrybucją do użytkowników końcowych](#binaries-end)</li></ul> | 
| **WCF** | <ul><li>[Ustaw wartość clientCredentialtype na certyfikat lub Windows](#cert)</li><li>[WCF — tryb zabezpieczeń nie jest włączony](#security)</li></ul> | 

## <a name="ensure-that-binaries-are-obfuscated-if-they-contain-sensitive-information"></a><a id="binaries-info"></a>Upewnij się, że pliki binarne są zasłaniane, jeśli zawierają informacje poufne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że pliki binarne są ukrywane, jeśli zawierają informacje poufne, takie jak tajemnice handlowe, poufne logiki biznesowej, które nie powinny być cofnięte. Jest to zatrzymywanie odtwarzania zestawów. `CryptoObfuscator`W tym celu można używać narzędzi takich jak. |

## <a name="consider-using-encrypted-file-system-efs-is-used-to-protect-confidential-user-specific-data"></a><a id="efs-user"></a>Rozważ użycie szyfrowanego systemu plików (EFS) służy do ochrony poufnych danych specyficznych dla użytkownika

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Należy rozważyć użycie szyfrowanego systemu plików (EFS) do ochrony poufnych danych specyficznych dla użytkownika z źródłami ataków za pomocą fizycznego dostępu do komputera. |

## <a name="ensure-that-sensitive-data-stored-by-the-application-on-the-file-system-is-encrypted"></a><a id="filesystem"></a>Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików są szyfrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Upewnij się, że poufne dane przechowywane przez aplikację w systemie plików są szyfrowane (np. przy użyciu funkcji DPAPI), jeśli nie można wymusić systemu szyfrowania plików |

## <a name="ensure-that-sensitive-content-is-not-cached-on-the-browser"></a><a id="cache-browser"></a>Upewnij się, że poufna zawartość nie jest buforowana w przeglądarce

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, formularze sieci Web, MVC5, MVC6 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Przeglądarki mogą przechowywać informacje na potrzeby buforowania i historii. Te buforowane pliki są przechowywane w folderze, takim jak folder Temporary Internet Files w przypadku programu Internet Explorer. Gdy te strony zostaną ponownie określone, przeglądarka wyświetli je w pamięci podręcznej. Jeśli do użytkownika są wyświetlane informacje poufne (takie jak adres, szczegóły karty kredytowej, numer PESEL lub nazwa użytkownika), te informacje mogą być przechowywane w pamięci podręcznej przeglądarki, a tym samym możliwe do pobierania za pomocą badania pamięci podręcznej przeglądarki lub po prostu naciskając przycisk "Wstecz" przeglądarki. Ustaw wartość nagłówka odpowiedzi kontroli pamięci podręcznej na "No-Store" dla wszystkich stron. |

### <a name="example"></a>Przykład
```XML
<configuration>
  <system.webServer>
   <httpProtocol>
    <customHeaders>
        <add name="Cache-Control" value="no-store" />
        <add name="Pragma" value="no-cache" />
        <add name="Expires" value="-1" />
    </customHeaders>
  </httpProtocol>
 </system.webServer>
</configuration>
```

### <a name="example"></a>Przykład
Może to być implementowane za pomocą filtru. Można użyć poniższego przykładu: 
```csharp
public override void OnActionExecuting(ActionExecutingContext filterContext)
        {
            if (filterContext == null || (filterContext.HttpContext != null && filterContext.HttpContext.Response != null && filterContext.HttpContext.Response.IsRequestBeingRedirected))
            {
                //// Since this is MVC pipeline, this should never be null.
                return;
            }

            var attributes = filterContext.ActionDescriptor.GetCustomAttributes(typeof(System.Web.Mvc.OutputCacheAttribute), false);
            if (attributes == null || **Attributes**.Count() == 0)
            {
                filterContext.HttpContext.Response.Cache.SetNoStore();
                filterContext.HttpContext.Response.Cache.SetCacheability(HttpCacheability.NoCache);
                filterContext.HttpContext.Response.Cache.SetExpires(DateTime.UtcNow.AddHours(-1));
                if (!filterContext.IsChildAction)
                {
                    filterContext.HttpContext.Response.AppendHeader("Pragma", "no-cache");
                }
            }

            base.OnActionExecuting(filterContext);
        }
``` 

## <a name="encrypt-sections-of-web-apps-configuration-files-that-contain-sensitive-data"></a><a id="encrypt-data"></a>Szyfruj sekcje plików konfiguracji aplikacji sieci Web, które zawierają dane poufne

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Instrukcje: szyfrowanie sekcji konfiguracyjnych w ASP.NET 2,0 przy użyciu funkcji DPAPI](/previous-versions/msp-n-p/ff647398(v=pandp.10)), [Określanie dostawcy konfiguracji chronionej](/previous-versions/68ze1hb2(v=vs.140)) [przy użyciu Azure Key Vault do ochrony kluczy tajnych aplikacji](/azure/architecture/multitenant-identity/web-api) |
| **Kroki** | Pliki konfiguracji, takie jak Web.config, appsettings.jssą często używane do przechowywania informacji poufnych, w tym nazw użytkowników, haseł, parametrów połączenia bazy danych i kluczy szyfrowania. Jeśli te informacje nie są chronione, aplikacja jest narażona na osoby atakujące lub Złośliwi użytkownicy otrzymujące poufne informacje, takie jak nazwy użytkowników kont i hasła, nazwy baz danych i nazwy serwerów. Na podstawie typu wdrożenia (Azure/on-Premium) Zaszyfruj poufne sekcje plików konfiguracyjnych za pomocą funkcji DPAPI lub usług, takich jak Azure Key Vault. |

## <a name="explicitly-disable-the-autocomplete-html-attribute-in-sensitive-forms-and-inputs"></a><a id="autocomplete-input"></a>Jawnie wyłącz atrybut HTML autouzupełniania w poufnych formularzach i danych wejściowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [MSDN: Autouzupełnianie atrybutów](https://msdn.microsoft.com/library/ms533486(VS.85).aspx), [Używanie autouzupełniania w kodzie HTML](/previous-versions/windows/internet-explorer/ie-developer/), [luki w zabezpieczeniach oczyszczania kodu HTML](/security-updates/SecurityBulletins/2010/ms10-071), [autouzupełniania.](https://blog.mindedsecurity.com/2011/10/autocompleteagain.html) |
| **Kroki** | Atrybut Autouzupełnianie określa, czy formularz powinien mieć Autouzupełnianie włączone czy wyłączone. Gdy Autouzupełnianie jest włączone, przeglądarka automatycznie uzupełnia wartości na podstawie wartości wprowadzonych wcześniej przez użytkownika. Na przykład, gdy w formularzu wprowadzono nową nazwę i hasło, a formularz zostanie przesłany, w przeglądarce zostanie wyświetlone pytanie o to, czy hasło powinno zostać zapisane. Po wyświetleniu formularza nazwa i hasło są wypełniane automatycznie lub są wypełniane, gdy nazwa zostanie wprowadzona. Osoba atakująca z dostępem lokalnym może uzyskać hasło czystego tekstu z pamięci podręcznej przeglądarki. Domyślnie funkcja autouzupełniania jest włączona i musi być jawnie wyłączona. |

### <a name="example"></a>Przykład
```csharp
<form action="Login.aspx" method="post " autocomplete="off" >
      Social Security Number: <input type="text" name="ssn" />
      <input type="submit" value="Submit" />    
</form>
```

## <a name="ensure-that-sensitive-data-displayed-on-the-user-screen-is-masked"></a><a id="data-mask"></a>Upewnij się, że poufne dane wyświetlane na ekranie użytkownika są maskowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Aplikacja internetowa | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Dane poufne, takie jak hasła, numery kart kredytowych, SSN itp., powinny być maskowane, gdy są wyświetlane na ekranie. Ma to na celu uniemożliwienie nieautoryzowanym pracownikom uzyskiwania dostępu do danych (np. haseł z żeglowaniem, personel pomocy technicznej przeglądający numery PESEL użytkowników). Upewnij się, że te elementy danych nie są widoczne w postaci zwykłego tekstu i są odpowiednio maskowane. Należy zachować ostrożność przy akceptowaniu ich jako danych wejściowych (np. Typ wejściowy = "hasło"), jak również Wyświetlanie z powrotem na ekranie (np. Wyświetlanie tylko ostatnich 4 cyfr numeru karty kredytowej). |

## <a name="implement-dynamic-data-masking-to-limit-sensitive-data-exposure-non-privileged-users"></a><a id="dynamic-users"></a>Zaimplementuj Dynamiczne maskowanie danych, aby ograniczyć narażenie danych poufnych na nieuprzywilejowanych użytkowników

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | SQL Azure, lokalnego |
| **Atrybuty**              | SQL Version-V12, SQL Version-MsSQL2016 |
| **Odwołania**              | [Dynamiczne maskowanie danych](/sql/relational-databases/security/dynamic-data-masking) |
| **Kroki** | Dynamiczne maskowanie danych polega na ograniczeniu narażenia danych poufnych, uniemożliwiając użytkownikom, którzy nie powinni mieć dostępu do danych. Dynamiczne maskowanie danych nie ma na celu uniemożliwić użytkownikom bazy danych bezpośrednie łączenie się z bazą danych i wykonywanie pełnych zapytań, które uwidaczniają fragmenty poufnych danych. Dynamiczne maskowanie danych stanowi uzupełnienie innych funkcji zabezpieczeń SQL Server (inspekcja, szyfrowanie, zabezpieczenia na poziomie wiersza...) i zdecydowanie zaleca się używanie tej funkcji w połączeniu z nimi w celu lepszego ochrony poufnych danych w bazie danych. Należy pamiętać, że ta funkcja jest obsługiwana tylko przez SQL Server począwszy od 2016 i Azure SQL Database. |

## <a name="ensure-that-passwords-are-stored-in-salted-hash-format"></a><a id="salted-hash"></a>Upewnij się, że hasła są przechowywane w formacie solonego skrótu

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Tworzenie skrótów haseł przy użyciu interfejsów API kryptografii platformy .NET](https://docs.asp.net/en/latest/security/data-protection/consumer-apis/password-hashing.html) |
| **Kroki** | Hasła nie powinny być przechowywane w niestandardowych bazach danych magazynu użytkownika. W zamian skróty haseł powinny być przechowywane z użyciem wartości soli. Upewnij się, że sól dla użytkownika jest zawsze unikatowa i zastosowano polecenie b-Crypt, s-Crypt lub PBKDF2 przed zapisaniem hasła, z minimalną liczbą iteracji współczynnika pracy wynoszącą 150 000 pętle w celu wyeliminowania możliwości wymuszenia odłożenia.| 

## <a name="ensure-that-sensitive-data-in-database-columns-is-encrypted"></a><a id="db-encrypted"></a>Upewnij się, że poufne dane w kolumnach bazy danych są szyfrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Wersja SQL — wszystkie |
| **Odwołania**              | [Szyfrowanie danych poufnych w programie SQL Server](/previous-versions/sql/sql-server-2008-r2/ff848751(v=sql.105)) [: szyfrowanie kolumny danych w SQL Server](/sql/relational-databases/security/encryption/encrypt-a-column-of-data), [szyfrowanie za pomocą certyfikatu](/sql/t-sql/functions/encryptbycert-transact-sql) |
| **Kroki** | Dane poufne, takie jak numery kart kredytowych, są szyfrowane w bazie danych programu. Dane można szyfrować za pomocą szyfrowania na poziomie kolumny lub funkcji aplikacji przy użyciu funkcji szyfrowania. |

## <a name="ensure-that-database-level-encryption-tde-is-enabled"></a><a id="tde-enabled"></a>Upewnij się, że szyfrowanie na poziomie bazy danych (TDE) jest włączone

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Omówienie Transparent Data Encryption SQL Server (TDE)](/previous-versions/sql/sql-server-2008-r2/bb934049(v=sql.105)) |
| **Kroki** | Funkcja Transparent Data Encryption (TDE) w programie SQL Server pomaga w szyfrowaniu poufnych danych w bazie danych i ochronie kluczy używanych do szyfrowania danych za pomocą certyfikatu. Uniemożliwia to innym osobom bez kluczy używanie danych. TDE chroni dane "w stanie spoczynku", co oznacza pliki danych i dziennika. Zapewnia ona możliwość zapewnienia zgodności z wieloma przepisami, przepisami i wskazówkami ustanowionymi w różnych branżach. |

## <a name="ensure-that-database-backups-are-encrypted"></a><a id="backup"></a>Upewnij się, że kopie zapasowe bazy danych są szyfrowane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | baza danych | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | SQL Azure, lokalnego |
| **Atrybuty**              | SQL Version-V12, SQL Version-MsSQL2014 |
| **Odwołania**              | [Szyfrowanie kopii zapasowej bazy danych SQL](/sql/relational-databases/backup-restore/backup-encryption) |
| **Kroki** | SQL Server ma możliwość szyfrowania danych podczas tworzenia kopii zapasowej. Przez określenie algorytmu szyfrowania i szyfrowania (certyfikatu lub klucza asymetrycznego) podczas tworzenia kopii zapasowej można utworzyć zaszyfrowany plik kopii zapasowej. |

## <a name="ensure-that-sensitive-data-relevant-to-web-api-is-not-stored-in-browsers-storage"></a><a id="api-browser"></a>Upewnij się, że poufne dane istotne dla interfejsu API sieci Web nie są przechowywane w magazynie przeglądarki

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Interfejs API sieci Web | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | MVC 5, MVC 6 |
| **Atrybuty**              | Dostawca tożsamości — ADFS, dostawca tożsamości — Azure AD |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | <p>W niektórych implementacjach wrażliwe artefakty związane z uwierzytelnianiem internetowego interfejsu API są przechowywane w lokalnym magazynie przeglądarki. Na przykład w przypadku artefaktów uwierzytelniania usługi Azure AD, takich jak ADAL. idtoken, ADAL. nonce. idtoken, ADAL. Access. token. Key, ADAL. token. Keys, ADAL. State. Login, ADAL. Session. State, ADAL. wygaśnięcia. Key itd.</p><p>Wszystkie te artefakty są dostępne nawet po wylogowaniu lub zamknięciu przeglądarki. Jeśli atakującej uzyska dostęp do tych artefaktów, może użyć ich ponownie w celu uzyskania dostępu do chronionych zasobów (API). Upewnij się, że wszystkie wrażliwe artefakty związane z interfejsem API sieci Web nie są przechowywane w magazynie przeglądarki. W przypadkach, gdy nie można uniknąć magazynu po stronie klienta (np. aplikacje jednostronicowe (SPA), które wykorzystują niejawne przepływy OpenIdConnect/OAuth, muszą przechowywać tokeny dostępu lokalnie), korzystać z opcji magazynu bez trwałości. na przykład Preferuj SessionStorage LocalStorage.</p>| 

### <a name="example"></a>Przykład
Poniższy fragment kodu JavaScript pochodzi z niestandardowej biblioteki uwierzytelniania, która przechowuje artefakty uwierzytelniania w magazynie lokalnym. Należy unikać takich implementacji. 
```javascript
ns.AuthHelper.Authenticate = function () {
window.config = {
instance: 'https://login.microsoftonline.com/',
tenant: ns.Configurations.Tenant,
clientId: ns.Configurations.AADApplicationClientID,
postLogoutRedirectUri: window.location.origin,
cacheLocation: 'localStorage', // enable this for IE, as sessionStorage does not work for localhost.
};
```

## <a name="encrypt-sensitive-data-stored-in-cosmos-db"></a><a id="encrypt-docdb"></a>Szyfruj poufne dane przechowywane w Cosmos DB

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Baza danych dokumentów platformy Azure | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Szyfruj poufne dane na poziomie aplikacji przed zapisaniem w bazie danych dokumentu lub przechowuj poufne dane w innych rozwiązaniach magazynu, takich jak Azure Storage lub Azure SQL| 

## <a name="use-azure-disk-encryption-to-encrypt-disks-used-by-virtual-machines"></a><a id="disk-vm"></a>Użyj Azure Disk Encryption do szyfrowania dysków używanych przez Virtual Machines

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Granica zaufania maszyny wirtualnej usługi Azure IaaS | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Używanie Azure Disk Encryption do szyfrowania dysków używanych przez maszyny wirtualne](../../storage/blobs/security-recommendations.md#data-protection) |
| **Kroki** | <p>Azure Disk Encryption to nowa funkcja, która jest obecnie dostępna w wersji zapoznawczej. Ta funkcja umożliwia szyfrowanie dysków systemu operacyjnego i dysków danych używanych przez maszynę wirtualną IaaS. W przypadku systemu Windows dyski są szyfrowane przy użyciu standardowej technologii szyfrowania funkcji BitLocker. W przypadku systemu Linux dyski są szyfrowane przy użyciu technologii DM-Crypt. Jest ona zintegrowana z Azure Key Vault, aby umożliwić sterowanie kluczami szyfrowania dysków i zarządzanie nimi. Rozwiązanie Azure Disk Encryption obsługuje następujące trzy scenariusze szyfrowania klientów:</p><ul><li>Włącz szyfrowanie na nowych maszynach wirtualnych IaaS utworzonych na podstawie plików VHD szyfrowanych przez klienta i kluczy szyfrowania dostarczonych przez klienta, które są przechowywane w Azure Key Vault.</li><li>Włącz szyfrowanie na nowych maszynach wirtualnych IaaS utworzonych w witrynie Azure Marketplace.</li><li>Włącz szyfrowanie na istniejących maszynach wirtualnych IaaS już uruchomionych na platformie Azure.</li></ul>| 

## <a name="encrypt-secrets-in-service-fabric-applications"></a><a id="fabric-apps"></a>Szyfruj wpisy tajne w aplikacjach Service Fabric

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Service Fabric granic zaufania | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Środowisko — Azure |
| **Odwołania**              | [Zarządzanie wpisami tajnymi w aplikacjach Service Fabric](../../service-fabric/service-fabric-application-secret-management.md) |
| **Kroki** | Wpisy tajne mogą być dowolnymi informacjami poufnymi, takimi jak parametry połączenia magazynu, hasła lub inne wartości, które nie powinny być obsługiwane w postaci zwykłego tekstu. Użyj Azure Key Vault do zarządzania kluczami i wpisami tajnymi w aplikacjach usługi Service Fabric. |

## <a name="perform-security-modeling-and-use-business-unitsteams-where-required"></a><a id="modeling-teams"></a>Przeprowadzenie modelowania zabezpieczeń i użycie jednostek handlowych/zespołów, gdy jest to wymagane

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Przeprowadzenie modelowania zabezpieczeń i użycie jednostek handlowych/zespołów, gdy jest to wymagane |

## <a name="minimize-access-to-share-feature-on-critical-entities"></a><a id="entities"></a>Minimalizuj dostęp do funkcji udostępniania na jednostkach krytycznych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Minimalizuj dostęp do funkcji udostępniania na jednostkach krytycznych |

## <a name="train-users-on-the-risks-associated-with-the-dynamics-crm-share-feature-and-good-security-practices"></a><a id="good-practices"></a>Uczenie użytkowników o ryzyku związanym z funkcją udziału Dynamics CRM i dobrą praktyką w zakresie zabezpieczeń

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Uczenie użytkowników o ryzyku związanym z funkcją udziału Dynamics CRM i dobrą praktyką w zakresie zabezpieczeń |

## <a name="include-a-development-standards-rule-proscribing-showing-config-details-in-exception-management"></a><a id="exception-mgmt"></a>Uwzględnij reguły proscribing dla deweloperów przedstawiające szczegóły konfiguracji w zarządzaniu wyjątkami

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Dynamics CRM | 
| **Faza SDL**               | Wdrożenie |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | Nie dotyczy  |
| **Kroki** | Uwzględnij regułę proscribing dla deweloperów pokazującą szczegóły konfiguracji w zarządzaniu wyjątkami poza programowaniem. Przetestuj je w ramach przeglądów kodu lub okresowej inspekcji.|

## <a name="use-azure-storage-service-encryption-sse-for-data-at-rest-preview"></a><a id="sse-preview"></a>Użyj usługi Azure szyfrowanie usługi Storage (SSE) dla danych przechowywanych w spoczynku (wersja zapoznawcza)

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | StorageType — obiekt BLOB |
| **Odwołania**              | [Szyfrowanie usługi Storage platformy Azure dla danych przechowywanych w spoczynku (wersja zapoznawcza)](../../storage/common/storage-service-encryption.md) |
| **Kroki** | <p>Usługa Azure szyfrowanie usługi Storage (SSE) dla danych przechowywanych w czasie spoczynku ułatwia ochronę i ochronę danych w celu spełnienia wymagań w zakresie zabezpieczeń i bezpieczeństwa organizacji. Przy użyciu tej funkcji usługa Azure Storage automatycznie szyfruje dane przed trwałym wprowadzeniem ich do magazynu i odszyfrowuje je przed pobraniem. Szyfrowanie, odszyfrowywanie i zarządzanie kluczami jest całkowicie niewidoczne dla użytkowników. SSE stosuje się tylko do blokowych obiektów blob, stronicowych obiektów blob i dołączania obiektów BLOB. Inne typy danych, w tym tabele, kolejki i pliki, nie będą szyfrowane.</p><p>Przepływ pracy szyfrowania i odszyfrowywania:</p><ul><li>Klient włącza szyfrowanie na koncie magazynu</li><li>Gdy klient zapisuje nowe dane (PUT obiekt BLOB, UMIEŚCI blok, PUT stronę itp.) do magazynu obiektów BLOB. Każdy zapis jest szyfrowany przy użyciu 256-bitowego szyfrowania AES, jednego z najsilniejszych szyfrów blokowych</li><li>Gdy klient potrzebuje dostępu do danych (GET BLOB itp.), dane są automatycznie odszyfrowywane przed zwróceniem do użytkownika</li><li>Jeśli szyfrowanie jest wyłączone, nowe zapisy nie będą już szyfrowane i istniejące zaszyfrowane dane pozostają zaszyfrowane do momentu zapisania go przez użytkownika. Gdy szyfrowanie jest włączone, operacje zapisu w magazynie obiektów BLOB będą szyfrowane. Stan danych nie zmienia się w przypadku przełączania użytkownika o włączenie/wyłączenie szyfrowania dla konta magazynu</li><li>Wszystkie klucze szyfrowania są przechowywane, szyfrowane i zarządzane przez firmę Microsoft</li></ul><p>Należy pamiętać, że w tym momencie klucze używane do szyfrowania są zarządzane przez firmę Microsoft. Firma Microsoft generuje klucze pierwotnie i zarządza bezpiecznym magazynem kluczy, a także regularnym rotacją zgodnie z zasadami wewnętrznymi firmy Microsoft. W przyszłości klienci będą mogli zarządzać własnymi kluczami szyfrowania >i udostępniać ścieżki migracji z kluczy zarządzanych przez firmę Microsoft do kluczy zarządzanych przez klienta.</p>| 

## <a name="use-client-side-encryption-to-store-sensitive-data-in-azure-storage"></a><a id="client-storage"></a>Używanie szyfrowania Client-Side do przechowywania danych poufnych w usłudze Azure Storage

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Azure Storage | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Szyfrowanie po stronie klienta i Azure Key Vault dla Microsoft Azure Storage](../../storage/common/storage-client-side-encryption.md), [Samouczek: szyfrowanie i odszyfrowywanie obiektów BLOB w Microsoft Azure Storage przy użyciu Azure Key Vault](../../storage/blobs/storage-encrypt-decrypt-blobs-key-vault.md), [bezpieczne przechowywanie danych na platformie Azure Blob Storage przy użyciu rozszerzeń szyfrowania platformy Azure](/archive/blogs/partnercatalystteam/storing-data-securely-in-azure-blob-storage-with-azure-encryption-extensions) |
| **Kroki** | <p>Biblioteka klienta usługi Azure Storage dla programu .NET NuGet obsługuje szyfrowanie danych w aplikacjach klienckich przed przekazaniem ich do usługi Azure Storage i odszyfrowywanie danych podczas pobierania ich do klienta. Biblioteka obsługuje również integrację z magazynem kluczy Azure do zarządzania kluczami konta magazynu. Poniżej znajduje się krótki opis sposobu działania szyfrowania po stronie klienta:</p><ul><li>Zestaw SDK klienta usługi Azure Storage generuje klucz szyfrowania zawartości (CEK), który jest jednorazowym użyciem klucza symetrycznego.</li><li>Dane klienta są szyfrowane przy użyciu tego CEK</li><li>CEK jest następnie opakowany (zaszyfrowany) przy użyciu klucza szyfrowania klucza (KEK). KEK jest identyfikowany przez identyfikator klucza i może być parę kluczy asymetrycznych lub kluczem symetrycznym i może być zarządzany lokalnie lub przechowywany w Azure Key Vault. Klient magazynu nigdy nie ma dostępu do KEK. Po prostu wywołuje algorytm zawijania kluczy, który jest dostarczany przez Key Vault. Klienci mogą wybrać użycie niestandardowych dostawców na potrzeby zawijania kluczy/rozpakowywania, jeśli chcą</li><li>Zaszyfrowane dane są następnie przekazywane do usługi Azure Storage. Zapoznaj się z linkami w sekcji References, aby poznać szczegóły implementacji niskiego poziomu.</li></ul>|

## <a name="encrypt-sensitive-or-pii-data-written-to-phones-local-storage"></a><a id="pii-phones"></a>Szyfruj poufne lub dane OSOBowe zapisywane w lokalnym magazynie telefonów

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, Xamarin  |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Zarządzanie ustawieniami i funkcjami na urządzeniach przy użyciu zasad Microsoft Intune](/mem/intune/configuration/), [pęku kluczy portiera](https://components.xamarin.com/view/square.valet) |
| **Kroki** | <p>Jeśli aplikacja zapisuje informacje poufne, takie jak dane OSOBowe użytkownika (adres e-mail, numer telefonu, imię, nazwisko, preferencje itp.) — w systemie plików dla urządzeń przenośnych należy go zaszyfrować przed zapisem w lokalnym systemie plików. Jeśli aplikacja jest aplikacją dla przedsiębiorstw, zapoznaj się z możliwością publikowania aplikacji za pomocą usługi Windows Intune.</p>|

### <a name="example"></a>Przykład
Usługę Intune można skonfigurować przy użyciu następujących zasad zabezpieczeń, aby chronić poufne dane: 
```csharp
Require encryption on mobile device    
Require encryption on storage cards
Allow screen capture
```

### <a name="example"></a>Przykład
Jeśli aplikacja nie jest aplikacją dla przedsiębiorstw, użyj magazynu kluczy dostarczonego przez platformę, a następnie Przechowaj klucze szyfrowania przy użyciu operacji kryptograficznej, która może być wykonywana w systemie plików. Poniższy fragment kodu pokazuje, jak uzyskać dostęp do klucza z łańcucha kluczy przy użyciu platformy Xamarin: 
```csharp
        protected static string EncryptionKey
        {
            get
            {
                if (String.IsNullOrEmpty(_Key))
                {
                    var query = new SecRecord(SecKind.GenericPassword);
                    query.Service = NSBundle.MainBundle.BundleIdentifier;
                    query.Account = "UniqueID";

                    NSData uniqueId = SecKeyChain.QueryAsData(query);
                    if (uniqueId == null)
                    {
                        query.ValueData = NSData.FromString(System.Guid.NewGuid().ToString());
                        var err = SecKeyChain.Add(query);
                        _Key = query.ValueData.ToString();
                    }
                    else
                    {
                        _Key = uniqueId.ToString();
                    }
                }

                return _Key;
            }
        }
```

## <a name="obfuscate-generated-binaries-before-distributing-to-end-users"></a><a id="binaries-end"></a>Zaciemniaj wygenerowane pliki binarne przed dystrybucją do użytkowników końcowych

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | Klient mobilny | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólny |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Mieszanie kryptograficzne dla platformy .NET](https://www.ssware.com/cryptoobfuscator/obfuscator-net.htm) |
| **Kroki** | Wygenerowane pliki binarne (zestawy w ramach apk) powinny zostać zasłonięte, aby zatrzymać odtwarzanie zestawów. `CryptoObfuscator` W tym celu można używać narzędzi takich jak. |

## <a name="set-clientcredentialtype-to-certificate-or-windows"></a><a id="cert"></a>Ustaw wartość clientCredentialtype na certyfikat lub Windows

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | .NET Framework 3 |
| **Atrybuty**              | Nie dotyczy  |
| **Odwołania**              | [Wzmacnia](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_token) |
| **Kroki** | Użycie UsernameToken z hasłem w postaci zwykłego tekstu za pośrednictwem nieszyfrowanego kanału ujawnia hasło osobom atakującym, które mogą wyrównać komunikaty protokołu SOAP. Dostawcy usług, którzy używają UsernameToken, mogą akceptować hasła wysyłane w postaci zwykłego tekstu. Wysyłanie haseł w postaci zwykłego tekstu za pośrednictwem nieszyfrowanego kanału może ujawnić poświadczenie osobom atakującym, które mogą wyrównać komunikat protokołu SOAP. | 

### <a name="example"></a>Przykład
Następująca konfiguracja dostawcy usług WCF używa UsernameToken: 
```
<security mode="Message"> 
<message clientCredentialType="UserName" />
``` 
Ustaw wartość clientCredentialtype na certyfikat lub Windows. 

## <a name="wcf-security-mode-is-not-enabled"></a><a id="security"></a>WCF — tryb zabezpieczeń nie jest włączony

| Tytuł                   | Szczegóły      |
| ----------------------- | ------------ |
| **Składnik**               | WCF | 
| **Faza SDL**               | Kompilacja |  
| **Odpowiednie technologie** | Ogólne, .NET Framework 3 |
| **Atrybuty**              | Tryb zabezpieczeń-transport, tryb zabezpieczeń — komunikat |
| **Odwołania**              | [MSDN](/previous-versions/msp-n-p/ff648500(v=pandp.10)), [wzmacnianie Brytanii](https://vulncat.fortify.com/en/detail?id=desc.config.dotnet.wcf_misconfiguration_weak_class_reference), [podstawowe informacje dotyczące magazynu kodu zabezpieczającego WCF](https://www.codemag.com/article/0611051) |
| **Kroki** | Nie zdefiniowano zabezpieczeń transportu ani komunikatów. Aplikacje, które przesyłają komunikaty bez transportu ani zabezpieczenia komunikatów, nie mogą zagwarantować integralności ani poufności komunikatów. Gdy powiązanie zabezpieczeń WCF ma wartość None, zarówno zabezpieczenia transportu, jak i komunikatów są wyłączone. |

### <a name="example"></a>Przykład
Poniższa konfiguracja ustawia tryb zabezpieczeń na brak. 
```
<system.serviceModel> 
  <bindings> 
    <wsHttpBinding> 
      <binding name=""MyBinding""> 
        <security mode=""None""/> 
      </binding> 
  </bindings> 
</system.serviceModel> 
```

### <a name="example"></a>Przykład
Tryb zabezpieczeń we wszystkich powiązaniach usług istnieje pięć możliwych trybów zabezpieczeń: 
* Brak. Wyłącza zabezpieczenia. 
* Transportu. Używa zabezpieczeń transportu do wzajemnego uwierzytelniania i ochrony komunikatów. 
* Komunikat. Używa zabezpieczeń komunikatów do wzajemnego uwierzytelniania i ochrony komunikatów. 
* Jedn. Umożliwia określenie ustawień zabezpieczeń transportu i na poziomie wiadomości (tylko usługa MSMQ obsługuje tę funkcję). 
* TransportWithMessageCredential. Poświadczenia są przekazywane z użyciem komunikatów oraz ochrony wiadomości i uwierzytelniania serwera są udostępniane przez warstwę transportu. 
* TransportCredentialOnly. Poświadczenia klienta są przesyłane z użyciem warstwy transportu i nie jest stosowana ochrona komunikatów. Zabezpieczanie i poufność komunikatów przy użyciu protokołu Transport i zabezpieczenia komunikatów. W poniższej konfiguracji nakazuje usłudze korzystanie z zabezpieczeń transportu z poświadczeniami komunikatów.
  ```
  <system.serviceModel>
  <bindings>
    <wsHttpBinding>
    <binding name=""MyBinding""> 
    <security mode=""TransportWithMessageCredential""/> 
    <message clientCredentialType=""Windows""/> 
    </binding> 
  </bindings> 
  </system.serviceModel> 
  ```