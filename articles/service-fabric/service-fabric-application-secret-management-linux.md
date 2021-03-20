---
title: Konfigurowanie certyfikatu szyfrowania w klastrach systemu Linux
description: Dowiedz się, jak skonfigurować certyfikat szyfrowania i zaszyfrować wpisy tajne w klastrach systemu Linux.
author: shsha
ms.topic: conceptual
ms.date: 01/04/2019
ms.author: shsha
ms.openlocfilehash: b8e0a19e3f654fc561e7c7e26c6a2da463e24d5f
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "78969028"
---
# <a name="set-up-an-encryption-certificate-and-encrypt-secrets-on-linux-clusters"></a>Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Linux
W tym artykule przedstawiono sposób konfigurowania certyfikatu szyfrowania i używania go do szyfrowania wpisów tajnych w klastrach systemu Linux. W przypadku klastrów systemu Windows Zobacz [Konfigurowanie certyfikatu szyfrowania i szyfrowanie wpisów tajnych w klastrach systemu Windows][secret-management-windows-specific-link].

## <a name="obtain-a-data-encipherment-certificate"></a>Uzyskiwanie certyfikatu szyfrowania danych
Certyfikat szyfrowanie danych jest używany wyłącznie do szyfrowania i odszyfrowywania [parametrów][parameters-link] w Settings.xml i [zmiennych środowiskowych][environment-variables-link] usługi w ServiceManifest.xml usługi. Nie jest on używany do uwierzytelniania ani podpisywania tekstu szyfrowania. Certyfikat musi spełniać następujące wymagania:

* Certyfikat musi zawierać klucz prywatny.
* Użycie klucza certyfikatu musi obejmować szyfrowanie danych (10) i nie powinno obejmować uwierzytelniania serwera ani uwierzytelniania klientów.

  Na przykład następujące polecenia mogą służyć do generowania wymaganego certyfikatu przy użyciu OpenSSL:
  
  ```console
  user@linux:~$ openssl req -newkey rsa:2048 -nodes -keyout TestCert.prv -x509 -days 365 -out TestCert.pem
  user@linux:~$ cat TestCert.prv >> TestCert.pem
  ```

## <a name="install-the-certificate-in-your-cluster"></a>Instalowanie certyfikatu w klastrze
Certyfikat musi być zainstalowany na każdym węźle w klastrze `/var/lib/sfcerts` . Konto użytkownika, na którym jest uruchomiona usługa (domyślnie sfuser) **powinno mieć dostęp do odczytu** do zainstalowanego certyfikatu (czyli `/var/lib/sfcerts/TestCert.pem` dla bieżącego przykładu).

## <a name="encrypt-secrets"></a>Szyfrowanie wpisów tajnych
Poniższego fragmentu kodu można użyć do szyfrowania klucza tajnego. Ten fragment kodu szyfruje wartość. **nie podpisuje** tekstu szyfru. **Musisz użyć** tego samego certyfikatu szyfrowania, który jest zainstalowany w klastrze w celu utworzenia tekstu szyfrowanego dla wartości tajnych.

```console
user@linux:$ echo "Hello World!" > plaintext.txt
user@linux:$ iconv -f ASCII -t UTF-16LE plaintext.txt | tr -d '\n' > plaintext_UTF-16.txt
user@linux:$ openssl smime -encrypt -in plaintext_UTF-16.txt -binary -outform der TestCert.pem | base64 > encrypted.txt
```
Wynikowy tekst zakodowany w formacie Base-64 do encrypted.txt zawiera zarówno klucz tajny, jak i informacje o certyfikacie użytym do jego zaszyfrowania. Aby sprawdzić jego prawidłowość, można je odszyfrować za pomocą OpenSSL.
```console
user@linux:$ cat encrypted.txt | base64 -d | openssl smime -decrypt -inform der -inkey TestCert.prv
```

## <a name="next-steps"></a>Następne kroki
Dowiedz się, jak [określić zaszyfrowane klucze tajne w aplikacji.][secret-management-specify-encrypted-secrets-link]

<!-- Links -->
[parameters-link]:service-fabric-how-to-parameterize-configuration-files.md
[environment-variables-link]: service-fabric-how-to-specify-environment-variables.md
[secret-management-windows-specific-link]: service-fabric-application-secret-management-windows.md
[secret-management-specify-encrypted-secrets-link]: service-fabric-application-secret-management.md#specify-encrypted-secrets-in-an-application
