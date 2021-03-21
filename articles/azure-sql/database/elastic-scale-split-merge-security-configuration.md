---
title: Konfiguracja zabezpieczeń Split-Merge
description: Skonfiguruj certyfikaty x409 do szyfrowania za pomocą usługi Split/Merge na potrzeby elastycznego skalowania.
services: sql-database
ms.service: sql-database
ms.subservice: scale-out
ms.custom: sqldbrb=1
ms.devlang: ''
ms.topic: conceptual
author: VanMSFT
ms.author: vanto
ms.reviewer: sstein
ms.date: 12/18/2018
ms.openlocfilehash: 235efc550fd47d4244a5bf081c75d5e824a8e4b4
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "92793436"
---
# <a name="split-merge-security-configuration"></a>Konfiguracja zabezpieczeń Split-Merge
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Aby korzystać z usługi Split/Merge, należy prawidłowo skonfigurować zabezpieczenia. Usługa jest częścią funkcji elastycznego skalowania Azure SQL Database. Aby uzyskać więcej informacji, zobacz [Samouczek dotyczący usługi Split and Merge w skali elastycznej](elastic-scale-configure-deploy-split-and-merge.md).

## <a name="configuring-certificates"></a>Konfigurowanie certyfikatów

Certyfikaty są konfigurowane na dwa sposoby. 

1. [Aby skonfigurować certyfikat TLS/SSL](#to-configure-the-tlsssl-certificate)
2. [Aby skonfigurować certyfikaty klienta](#to-configure-client-certificates) 

## <a name="to-obtain-certificates"></a>Aby uzyskać certyfikaty

Certyfikaty mogą być uzyskiwane z publicznych urzędów certyfikacji lub z [usługi certyfikatów systemu Windows](/windows/win32/seccrypto/certificate-services). Są to preferowane metody uzyskiwania certyfikatów.

Jeśli te opcje nie są dostępne, można generować certyfikaty z podpisem **własnym**.

## <a name="tools-to-generate-certificates"></a>Narzędzia do generowania certyfikatów

* [makecert.exe](/previous-versions/dotnet/netframework-4.0/bfsktky3(v=vs.100))
* [pvk2pfx.exe](/windows-hardware/drivers/devtest/pvk2pfx)

### <a name="to-run-the-tools"></a>Aby uruchomić narzędzia

* W wiersz polecenia dla deweloperów dla programu Visual Studios zobacz [wiersz polecenia programu Visual Studio](/dotnet/framework/tools/developer-command-prompt-for-vs) . 
  
    Jeśli jest zainstalowany, przejdź do:
  
    ```console
    %ProgramFiles(x86)%\Windows Kits\x.y\bin\x86 
    ```

* Pobierz pakiet WDK z [Windows 8.1: Pobierz zestawy i narzędzia](https://msdn.microsoft.com/windows/hardware/gg454513#drivers)

## <a name="to-configure-the-tlsssl-certificate"></a>Aby skonfigurować certyfikat TLS/SSL

Certyfikat TLS/SSL jest wymagany do szyfrowania komunikacji i uwierzytelniania serwera. Wybierz najbardziej odpowiednie trzy scenariusze poniżej i wykonaj wszystkie kroki:

### <a name="create-a-new-self-signed-certificate"></a>Utwórz nowy certyfikat z podpisem własnym

1. [Tworzenie certyfikatu Self-Signed](#create-a-self-signed-certificate)
2. [Utwórz plik PFX dla Self-Signed certyfikatu TLS/SSL](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Przekazywanie certyfikatu TLS/SSL do usługi w chmurze](#upload-tlsssl-certificate-to-cloud-service)
4. [Aktualizowanie certyfikatu TLS/SSL w pliku konfiguracji usługi](#update-tlsssl-certificate-in-service-configuration-file)
5. [Importowanie urzędu certyfikacji TLS/SSL](#import-tlsssl-certification-authority)

### <a name="to-use-an-existing-certificate-from-the-certificate-store"></a>Aby użyć istniejącego certyfikatu z magazynu certyfikatów
1. [Eksportowanie certyfikatu TLS/SSL z magazynu certyfikatów](#export-tlsssl-certificate-from-certificate-store)
2. [Przekazywanie certyfikatu TLS/SSL do usługi w chmurze](#upload-tlsssl-certificate-to-cloud-service)
3. [Aktualizowanie certyfikatu TLS/SSL w pliku konfiguracji usługi](#update-tlsssl-certificate-in-service-configuration-file)

### <a name="to-use-an-existing-certificate-in-a-pfx-file"></a>Aby użyć istniejącego certyfikatu w pliku PFX
1. [Przekazywanie certyfikatu TLS/SSL do usługi w chmurze](#upload-tlsssl-certificate-to-cloud-service)
2. [Aktualizowanie certyfikatu TLS/SSL w pliku konfiguracji usługi](#update-tlsssl-certificate-in-service-configuration-file)

## <a name="to-configure-client-certificates"></a>Aby skonfigurować certyfikaty klienta
Certyfikaty klienta są wymagane w celu uwierzytelnienia żądań do usługi. Wybierz najbardziej odpowiednie trzy scenariusze poniżej i wykonaj wszystkie kroki:

### <a name="turn-off-client-certificates"></a>Wyłącz certyfikaty klienta
1. [Wyłącz uwierzytelnianie Certificate-Based klienta](#turn-off-client-certificate-based-authentication)

### <a name="issue-new-self-signed-client-certificates"></a>Wystaw nowe certyfikaty klienta z podpisem własnym
1. [Tworzenie Self-Signed urzędu certyfikacji](#create-a-self-signed-certification-authority)
2. [Przekaż certyfikat urzędu certyfikacji do usługi w chmurze](#upload-ca-certificate-to-cloud-service)
3. [Aktualizuj certyfikat urzędu certyfikacji w pliku konfiguracji usługi](#update-ca-certificate-in-service-configuration-file)
4. [Wystawianie certyfikatów klienta](#issue-client-certificates)
5. [Tworzenie plików PFX dla certyfikatów klienta](#create-pfx-files-for-client-certificates)
6. [Importuj certyfikat klienta](#import-client-certificate)
7. [Kopiuj odciski palca certyfikatu klienta](#copy-client-certificate-thumbprints)
8. [Konfigurowanie dozwolonych klientów w pliku konfiguracji usługi](#configure-allowed-clients-in-the-service-configuration-file)

### <a name="use-existing-client-certificates"></a>Użyj istniejących certyfikatów klienta
1. [Znajdź klucz publiczny urzędu certyfikacji](#find-ca-public-key)
2. [Przekaż certyfikat urzędu certyfikacji do usługi w chmurze](#upload-ca-certificate-to-cloud-service)
3. [Aktualizuj certyfikat urzędu certyfikacji w pliku konfiguracji usługi](#update-ca-certificate-in-service-configuration-file)
4. [Kopiuj odciski palca certyfikatu klienta](#copy-client-certificate-thumbprints)
5. [Konfigurowanie dozwolonych klientów w pliku konfiguracji usługi](#configure-allowed-clients-in-the-service-configuration-file)
6. [Konfigurowanie sprawdzania odwołania certyfikatu klienta](#configure-client-certificate-revocation-check)

## <a name="allowed-ip-addresses"></a>Dozwolone adresy IP
Dostęp do punktów końcowych usługi może być ograniczony do określonych zakresów adresów IP.

## <a name="to-configure-encryption-for-the-store"></a>Aby skonfigurować szyfrowanie dla sklepu
Do szyfrowania poświadczeń przechowywanych w magazynie metadanych wymagany jest certyfikat. Wybierz najbardziej odpowiednie trzy scenariusze poniżej i wykonaj wszystkie kroki:

### <a name="use-a-new-self-signed-certificate"></a>Użyj nowego certyfikatu z podpisem własnym
1. [Tworzenie certyfikatu Self-Signed](#create-a-self-signed-certificate)
2. [Utwórz plik PFX dla certyfikatu szyfrowania Self-Signed](#create-pfx-file-for-self-signed-tlsssl-certificate)
3. [Przekaż certyfikat szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
4. [Aktualizuj certyfikat szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-from-the-certificate-store"></a>Użyj istniejącego certyfikatu z magazynu certyfikatów
1. [Eksportowanie certyfikatu szyfrowania z magazynu certyfikatów](#export-encryption-certificate-from-certificate-store)
2. [Przekaż certyfikat szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
3. [Aktualizuj certyfikat szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

### <a name="use-an-existing-certificate-in-a-pfx-file"></a>Używanie istniejącego certyfikatu w pliku PFX
1. [Przekaż certyfikat szyfrowania do usługi w chmurze](#upload-encryption-certificate-to-cloud-service)
2. [Aktualizuj certyfikat szyfrowania w pliku konfiguracji usługi](#update-encryption-certificate-in-service-configuration-file)

## <a name="the-default-configuration"></a>Konfiguracja domyślna
Konfiguracja domyślna odrzuca cały dostęp do punktu końcowego HTTP. Jest to zalecane ustawienie, ponieważ żądania do tych punktów końcowych mogą zawierać poufne informacje, takie jak poświadczenia bazy danych.
Konfiguracja domyślna zezwala na dostęp do punktu końcowego HTTPS. To ustawienie może być ograniczone.

### <a name="changing-the-configuration"></a>Zmiana konfiguracji
Grupa reguł kontroli dostępu, które dotyczą i punkt końcowy są skonfigurowane w **\<EndpointAcls>** sekcji w **pliku konfiguracji usługi**.

```xml
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpIn" accessControl="DenyAll" />
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="AllowAll" />
</EndpointAcls>
```

Reguły w grupie kontroli dostępu są konfigurowane w \<AccessControl name=""> sekcji pliku konfiguracji usługi. 

Ten format jest wyjaśniony w temacie Network Access Control lists.
Na przykład, aby zezwalać tylko na adresy IP w zakresie 100.100.0.0 na 100.100.255.255 dostępu do punktu końcowego HTTPS, reguły będą wyglądać następująco:

```xml
<AccessControl name="Retricted">
    <Rule action="permit" description="Some" order="1" remoteSubnet="100.100.0.0/16"/>
    <Rule action="deny" description="None" order="2" remoteSubnet="0.0.0.0/0" />
</AccessControl>
<EndpointAcls>
    <EndpointAcl role="SplitMergeWeb" endPoint="HttpsIn" accessControl="Restricted" />
</EndpointAcls>
```

## <a name="denial-of-service-prevention"></a>Zapobieganie atakom typu "odmowa usługi"
Obsługiwane są dwa różne mechanizmy wykrywania i zapobiegania atakom typu "odmowa usługi":

* Ogranicz liczbę równoczesnych żądań na hosta zdalnego (domyślnie wyłączone)
* Ogranicz szybkość dostępu na hosta zdalnego (domyślnie włączona)

Są one oparte na funkcjach opisanych w temacie dynamiczne zabezpieczenia protokołu IP w usługach IIS. W przypadku zmiany tej konfiguracji Uważaj na następujące czynniki:

* Zachowanie serwerów proxy i translacji adresów sieciowych za pośrednictwem informacji o hoście zdalnym
* Każde żądanie do dowolnego zasobu w roli sieci Web jest uznawane za (na przykład ładowanie skryptów, obrazów itp.)

## <a name="restricting-number-of-concurrent-accesses"></a>Ograniczanie liczby współbieżnych dostępu
Ustawienia, które konfigurują to zachowanie:

```xml
<Setting name="DynamicIpRestrictionDenyByConcurrentRequests" value="false" />
<Setting name="DynamicIpRestrictionMaxConcurrentRequests" value="20" />
```

Zmień wartość DynamicIpRestrictionDenyByConcurrentRequests na true, aby włączyć tę ochronę.

## <a name="restricting-rate-of-access"></a>Ograniczanie szybkości dostępu
Ustawienia, które konfigurują to zachowanie:

```xml
<Setting name="DynamicIpRestrictionDenyByRequestRate" value="true" />
<Setting name="DynamicIpRestrictionMaxRequests" value="100" />
<Setting name="DynamicIpRestrictionRequestIntervalInMilliseconds" value="2000" />
```

## <a name="configuring-the-response-to-a-denied-request"></a>Konfigurowanie odpowiedzi na żądanie odrzucone
Następujące ustawienie konfiguruje odpowiedź na żądanie odrzucone:

```xml
<Setting name="DynamicIpRestrictionDenyAction" value="AbortRequest" />
```

Inne obsługiwane wartości zapoznaj się z dokumentacją dotyczącą dynamicznego zabezpieczenia protokołu IP w usługach IIS.

## <a name="operations-for-configuring-service-certificates"></a>Operacje konfigurowania certyfikatów usług
Ten temat jest przeznaczony wyłącznie do celów informacyjnych. Wykonaj czynności konfiguracyjne opisane w temacie:

* Konfigurowanie certyfikatu TLS/SSL
* Konfigurowanie certyfikatów klienta

## <a name="create-a-self-signed-certificate"></a>Tworzenie certyfikatu z podpisem własnym
Wykonana

```console
makecert ^
  -n "CN=myservice.cloudapp.net" ^
  -e MM/DD/YYYY ^
  -r -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.1" ^
  -a sha256 -len 2048 ^
  -sv MySSL.pvk MySSL.cer
```

Aby dostosować:

* -n przy użyciu adresu URL usługi. Obsługiwane są symbole wieloznaczne ("CN = *. cloudapp. NET") i alternatywne nazwy ("CN = myservice1. cloudapp. NET, CN = myservice2. cloudapp. NET").
* -e z datą wygaśnięcia certyfikatu Utwórz silne hasło i określ je po wyświetleniu monitu.

## <a name="create-pfx-file-for-self-signed-tlsssl-certificate"></a>Utwórz plik PFX dla certyfikatu TLS/SSL z podpisem własnym
Wykonana

```console
pvk2pfx -pvk MySSL.pvk -spc MySSL.cer
```

Wprowadź hasło, a następnie wyeksportuj certyfikat z następującymi opcjami:

* Tak, eksportuj klucz prywatny
* Eksportuj wszystkie właściwości rozszerzone

## <a name="export-tlsssl-certificate-from-certificate-store"></a>Eksportowanie certyfikatu TLS/SSL z magazynu certyfikatów
* Znajdź certyfikat
* Kliknij pozycję akcje — > wszystkie zadania — > Eksportuj...
* Eksportuj certyfikat do. Plik PFX z następującymi opcjami:
  * Tak, eksportuj klucz prywatny
  * Jeśli jest to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji * Eksportuj wszystkie właściwości rozszerzone

## <a name="upload-tlsssl-certificate-to-cloud-service"></a>Przekazywanie certyfikatu TLS/SSL do usługi w chmurze
Przekaż certyfikat z istniejącym lub wygenerowanym. Plik PFX z parą kluczy TLS:

* Wprowadź hasło chroniące informacje o kluczu prywatnym

## <a name="update-tlsssl-certificate-in-service-configuration-file"></a>Aktualizowanie certyfikatu TLS/SSL w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca następującego ustawienia w pliku konfiguracji usługi z odciskiem palca certyfikatu przekazanego do usługi w chmurze:

```console
<Certificate name="SSL" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="import-tlsssl-certification-authority"></a>Importowanie urzędu certyfikacji TLS/SSL
Wykonaj następujące kroki na wszystkich kontach/komputerach, które będą komunikować się z usługą:

* Kliknij dwukrotnie. Plik CER w Eksploratorze Windows
* W oknie dialogowym certyfikat kliknij przycisk Instaluj certyfikat...
* Importuj certyfikat do magazynu zaufanych głównych urzędów certyfikacji

## <a name="turn-off-client-certificate-based-authentication"></a>Wyłączanie uwierzytelniania opartego na certyfikatach klienta
Obsługiwane jest tylko uwierzytelnianie oparte na certyfikatach klienta, a wyłączenie go umożliwi dostęp do punktów końcowych usługi, chyba że są stosowane inne mechanizmy (na przykład Microsoft Azure Virtual Network).

Aby wyłączyć tę funkcję, należy zmienić te ustawienia na false w pliku konfiguracji usługi:

```xml
<Setting name="SetupWebAppForClientCertificates" value="false" />
<Setting name="SetupWebserverForClientCertificates" value="false" />
```

Następnie skopiuj ten sam odcisk palca co certyfikat TLS/SSL w ustawieniu certyfikatu urzędu certyfikacji:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="create-a-self-signed-certification-authority"></a>Tworzenie urzędu certyfikacji z podpisem własnym
Wykonaj następujące kroki, aby utworzyć certyfikat z podpisem własnym, który będzie pełnił rolę urzędu certyfikacji:

```console
makecert ^
-n "CN=MyCA" ^
-e MM/DD/YYYY ^
 -r -cy authority -h 1 ^
 -a sha256 -len 2048 ^
  -sr localmachine -ss my ^
  MyCA.cer
```

Aby go dostosować

* -e z datą wygaśnięcia certyfikatu

## <a name="find-ca-public-key"></a>Znajdź klucz publiczny urzędu certyfikacji
Wszystkie certyfikaty klienta muszą zostać wystawione przez urząd certyfikacji zaufany przez usługę. Znajdź klucz publiczny w urzędzie certyfikacji, który wystawił certyfikaty klienta, które będą używane do uwierzytelniania w celu przekazania go do usługi w chmurze.

Jeśli plik z kluczem publicznym nie jest dostępny, wyeksportuj go z magazynu certyfikatów:

* Znajdź certyfikat
  * Wyszukaj certyfikat klienta wystawiony przez ten sam urząd certyfikacji
* Kliknij dwukrotnie certyfikat.
* Wybierz kartę Ścieżka certyfikacji w oknie dialogowym certyfikatu.
* Kliknij dwukrotnie wpis urzędu certyfikacji w ścieżce.
* Zanotuj właściwości certyfikatu.
* Zamknij okno dialogowe **certyfikatu** .
* Znajdź certyfikat
  * Wyszukaj wskazany powyżej urząd certyfikacji.
* Kliknij pozycję akcje — > wszystkie zadania — > Eksportuj...
* Eksportuj certyfikat do. CER z następującymi opcjami:
  * **Nie Eksportuj klucza prywatnego**
  * Jeśli jest to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji.
  * Eksportuj wszystkie właściwości rozszerzone.

## <a name="upload-ca-certificate-to-cloud-service"></a>Przekaż certyfikat urzędu certyfikacji do usługi w chmurze
Przekaż certyfikat z istniejącym lub wygenerowanym. Plik CER z kluczem publicznym urzędu certyfikacji.

## <a name="update-ca-certificate-in-service-configuration-file"></a>Aktualizuj certyfikat urzędu certyfikacji w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca następującego ustawienia w pliku konfiguracji usługi z odciskiem palca certyfikatu przekazanego do usługi w chmurze:

```xml
<Certificate name="CA" thumbprint="" thumbprintAlgorithm="sha1" />
```

Zaktualizuj wartość następującego ustawienia z tym samym odciskiem palca:

```xml
<Setting name="AdditionalTrustedRootCertificationAuthorities" value="" />
```

## <a name="issue-client-certificates"></a>Wystawianie certyfikatów klienta
Każda osoba uprawniona do uzyskiwania dostępu do usługi powinna mieć certyfikat klienta wystawiony do ich wyłącznego użytku i powinien wybrać własne silne hasło, aby chronić jego klucz prywatny. 

Poniższe kroki należy wykonać na tym samym komputerze, na którym został wygenerowany i zapisany certyfikat urzędu certyfikacji z podpisem własnym:

```console
makecert ^
  -n "CN=My ID" ^
  -e MM/DD/YYYY ^
  -cy end -sky exchange -eku "1.3.6.1.5.5.7.3.2" ^
  -a sha256 -len 2048 ^
  -in "MyCA" -ir localmachine -is my ^
  -sv MyID.pvk MyID.cer
```

Dostosowanie

* -n z IDENTYFIKATORem dla klienta, który zostanie uwierzytelniony przy użyciu tego certyfikatu
* -e z datą wygaśnięcia certyfikatu
* MyID. PVK i MyID. cer z unikatowymi nazwami plików dla tego certyfikatu klienta

To polecenie wyświetli monit o podanie hasła, które ma zostać utworzone, a następnie użyte raz. Użyj silnego hasła.

## <a name="create-pfx-files-for-client-certificates"></a>Tworzenie plików PFX dla certyfikatów klienta
Dla każdego wygenerowanego certyfikatu klienta wykonaj następujące polecenie:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Dostosowanie

```console
MyID.pvk and MyID.cer with the filename for the client certificate
```

Wprowadź hasło, a następnie wyeksportuj certyfikat z następującymi opcjami:

* Tak, eksportuj klucz prywatny
* Eksportuj wszystkie właściwości rozszerzone
* Osoba, dla której jest wystawiany ten certyfikat, powinna wybrać hasło eksportu

## <a name="import-client-certificate"></a>Importuj certyfikat klienta
Każdy użytkownik, dla którego certyfikat klienta został wystawiony, powinien zaimportować parę kluczy na maszynach, które będą używane do komunikacji z usługą:

* Kliknij dwukrotnie. Plik PFX w Eksploratorze Windows
* Zaimportuj certyfikat do magazynu osobistego z co najmniej tą opcją:
  * Zaewidencjonuj wszystkie zaznaczone właściwości rozszerzone

## <a name="copy-client-certificate-thumbprints"></a>Kopiuj odciski palca certyfikatu klienta
Każdy użytkownik, dla którego certyfikat klienta został wystawiony, musi wykonać następujące kroki w celu uzyskania odcisku palca certyfikatu, który zostanie dodany do pliku konfiguracji usługi:

* Uruchom certmgr.exe
* Wybierz kartę osobistą
* Kliknij dwukrotnie certyfikat klienta, który ma być używany do uwierzytelniania
* W otwartym oknie dialogowym certyfikatu wybierz kartę Szczegóły
* Upewnij się, że w wyświetlaniu są wyświetlane wszystkie
* Wybierz z listy pole o nazwie odcisk palca
* Skopiuj wartość odcisku palca
  * Usuń niewidoczne znaki Unicode przed pierwszą cyfrą
  * Usuń wszystkie spacje

## <a name="configure-allowed-clients-in-the-service-configuration-file"></a>Konfigurowanie dozwolonych klientów w pliku konfiguracji usługi
Zaktualizuj wartość następującego ustawienia w pliku konfiguracji usługi za pomocą listy oddzielonych przecinkami odcisków palca certyfikatów klienta, którym zezwolono na dostęp do usługi:

```xml
<Setting name="AllowedClientCertificateThumbprints" value="" />
```

## <a name="configure-client-certificate-revocation-check"></a>Konfigurowanie sprawdzania odwołania certyfikatu klienta
Ustawienie domyślne nie sprawdza urzędu certyfikacji dla stanu odwołania certyfikatu klienta. Aby włączyć sprawdzanie, jeśli urząd certyfikacji, który wystawił certyfikaty klienta, obsługuje takie sprawdzenia, Zmień następujące ustawienie na jedną z wartości zdefiniowanych w X509RevocationMode Wyliczenie:

```xml
<Setting name="ClientCertificateRevocationCheck" value="NoCheck" />
```

## <a name="create-pfx-file-for-self-signed-encryption-certificates"></a>Utwórz plik PFX dla certyfikatów szyfrowania z podpisem własnym
W przypadku certyfikatu szyfrowania wykonaj następujące polecenie:

```console
pvk2pfx -pvk MyID.pvk -spc MyID.cer
```

Dostosowanie

```console
MyID.pvk and MyID.cer with the filename for the encryption certificate
```

Wprowadź hasło, a następnie wyeksportuj certyfikat z następującymi opcjami:

* Tak, eksportuj klucz prywatny
* Eksportuj wszystkie właściwości rozszerzone
* Hasło będzie potrzebne podczas przekazywania certyfikatu do usługi w chmurze.

## <a name="export-encryption-certificate-from-certificate-store"></a>Eksportowanie certyfikatu szyfrowania z magazynu certyfikatów
* Znajdź certyfikat
* Kliknij pozycję akcje — > wszystkie zadania — > Eksportuj...
* Eksportuj certyfikat do. Plik PFX z następującymi opcjami: 
  * Tak, eksportuj klucz prywatny
  * Jeśli jest to możliwe, Dołącz wszystkie certyfikaty do ścieżki certyfikacji 
* Eksportuj wszystkie właściwości rozszerzone

## <a name="upload-encryption-certificate-to-cloud-service"></a>Przekaż certyfikat szyfrowania do usługi w chmurze
Przekaż certyfikat z istniejącym lub wygenerowanym. Plik PFX z parą kluczy szyfrowania:

* Wprowadź hasło chroniące informacje o kluczu prywatnym

## <a name="update-encryption-certificate-in-service-configuration-file"></a>Aktualizuj certyfikat szyfrowania w pliku konfiguracji usługi
Zaktualizuj wartość odcisku palca następujących ustawień w pliku konfiguracji usługi do odcisku palca certyfikatu przekazanego do usługi w chmurze:

```xml
<Certificate name="DataEncryptionPrimary" thumbprint="" thumbprintAlgorithm="sha1" />
```

## <a name="common-certificate-operations"></a>Typowe operacje certyfikatów
* Konfigurowanie certyfikatu TLS/SSL
* Konfigurowanie certyfikatów klienta

## <a name="find-certificate"></a>Znajdź certyfikat
Wykonaj następujące kroki:

1. Uruchom mmc.exe.
2. Plik-> Dodaj/Usuń przystawkę...
3. wybierz opcję **Certyfikaty**.
4. Kliknij pozycję **Dodaj**.
5. Wybierz lokalizację magazynu certyfikatów.
6. Kliknij przycisk **Finish** (Zakończ).
7. Kliknij przycisk **OK**.
8. Rozwiń węzeł **Certyfikaty**.
9. Rozwiń węzeł Magazyn certyfikatów.
10. Rozwiń węzeł podrzędny certyfikatu.
11. Wybierz certyfikat z listy.

## <a name="export-certificate"></a>Eksportowanie certyfikatu
W **Kreatorze eksportu certyfikatów**:

1. Kliknij przycisk **Dalej**.
2. Wybierz opcję **tak**, a następnie **wyeksportuj klucz prywatny**.
3. Kliknij przycisk **Dalej**.
4. Wybierz żądany format pliku wyjściowego.
5. Sprawdź odpowiednie opcje.
6. Sprawdź **hasło**.
7. Wprowadź silne hasło i potwierdź je.
8. Kliknij przycisk **Dalej**.
9. Wpisz lub Przeglądaj nazwę pliku, w którym ma być przechowywany certyfikat (Użyj. Rozszerzenie PFX).
10. Kliknij przycisk **Dalej**.
11. Kliknij przycisk **Finish** (Zakończ).
12. Kliknij przycisk **OK**.

## <a name="import-certificate"></a>Importowanie certyfikatu
W Kreatorze importu certyfikatów:

1. Wybierz lokalizację magazynu.
   
   * Wybierz **bieżącego użytkownika** , jeśli tylko procesy działające w ramach bieżącego użytkownika będą miały dostęp do usługi
   * Wybierz **maszynę lokalną** , jeśli inne procesy na tym komputerze będą uzyskiwać dostęp do usługi
2. Kliknij przycisk **Dalej**.
3. W przypadku importowania z pliku Potwierdź ścieżkę pliku.
4. W przypadku importowania a. Plik PFX:
   1. Wprowadź hasło chroniące klucz prywatny
   2. Wybierz opcje importu
5. Wybierz pozycję "Umieść" certyfikatów w następującym magazynie
6. Kliknij pozycję **Browse (Przeglądaj)**.
7. Wybierz żądany magazyn.
8. Kliknij przycisk **Finish** (Zakończ).
   
   * Jeśli wybrano magazyn zaufanych głównych urzędów certyfikacji, kliknij przycisk **tak**.
9. Kliknij przycisk **OK** we wszystkich oknach okna dialogowego.

## <a name="upload-certificate"></a>Przekazywanie certyfikatu
W [Azure Portal](https://portal.azure.com/)

1. Wybierz **Cloud Services**.
2. Wybierz usługę w chmurze.
3. W górnym menu kliknij pozycję **Certyfikaty**.
4. Na dolnym pasku kliknij pozycję **Przekaż**.
5. Wybierz plik certyfikatu.
6. Jeśli jest to. Plik PFX, wprowadź hasło dla klucza prywatnego.
7. Po zakończeniu Skopiuj odcisk palca certyfikatu z nowej pozycji na liście.

## <a name="other-security-considerations"></a>Inne zagadnienia dotyczące zabezpieczeń
Ustawienia protokołu TLS opisane w tym dokumencie szyfrują komunikację między usługą i jej klientami, gdy jest używany punkt końcowy HTTPS. Jest to ważne, ponieważ poświadczenia dostępu do bazy danych i inne poufne informacje są zawarte w komunikacji. Należy jednak pamiętać, że usługa utrzymuje stan wewnętrzny, w tym poświadczenia, w swoich wewnętrznych tabelach w bazie danych w Azure SQL Database, które zostały udostępnione dla magazynu metadanych w subskrypcji Microsoft Azure. Ta baza danych została zdefiniowana jako część następującego ustawienia w pliku konfiguracji usługi (. Plik CSCFG): 

```xml
<Setting name="ElasticScaleMetadata" value="Server=…" />
```

Poświadczenia przechowywane w tej bazie danych są szyfrowane. Jednak najlepszym rozwiązaniem jest upewnienie się, że role sieci Web i procesu roboczego wdrożeń usług są aktualne i są bezpieczne, ponieważ oba mają dostęp do bazy danych metadanych i certyfikat używany do szyfrowania i odszyfrowywania przechowywanych poświadczeń. 

[!INCLUDE [elastic-scale-include](../../../includes/elastic-scale-include.md)]