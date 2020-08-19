---
title: Uwierzytelnianie do Azure Key Vault
description: Dowiedz się, jak uwierzytelniać się w usłudze Azure Key Vault
author: ShaneBala-keyvault
ms.author: sudbalas
ms.date: 06/08/2020
ms.service: key-vault
ms.subservice: general
ms.topic: how-to
ms.openlocfilehash: 7645600a476a1c2294ddd4a24fe01e2ffe51d5ac
ms.sourcegitcommit: 02ca0f340a44b7e18acca1351c8e81f3cca4a370
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 08/19/2020
ms.locfileid: "88589986"
---
# <a name="authenticate-to-azure-key-vault"></a>Uwierzytelnianie do Azure Key Vault

## <a name="overview"></a>Omówienie

Azure Key Vault to rozwiązanie do zarządzania wpisami tajnymi, które pozwala na scentralizowanie przechowywania wpisów tajnych aplikacji i sterowanie ich dystrybucją. Azure Key Vault eliminuje konieczność przechowywania poświadczeń w aplikacjach. Aplikacja może uwierzytelniać się w magazynie kluczy, aby pobrać wymagane poświadczenia. Ten dokument zawiera podstawowe informacje dotyczące uwierzytelniania w magazynie kluczy.

Ten dokument pomoże Ci zrozumieć, jak działa uwierzytelnianie magazynu kluczy. Ten dokument wyjaśnia przepływ uwierzytelniania, pokazuje, jak udzielić dostępu do magazynu kluczy i zawiera samouczek umożliwiający pobranie przechowywanych wpisów tajnych w magazynie kluczy z przykładowej aplikacji w języku Python.

Ten dokument obejmuje następujące zagadnienia:

* Kluczowe pojęcia
* Rejestracja podmiotu zabezpieczeń
* Informacje o przepływie uwierzytelniania Key Vault
* Przyznaj jednostce usługi dostęp do Key Vault
* Samouczek (Python)

## <a name="key-concepts"></a>Kluczowe pojęcia

### <a name="azure-active-directory-concepts"></a>Koncepcje Azure Active Directory

* Azure Active Directory (AAD) — Azure Active Directory (Azure AD) to usługa firmy Microsoft do zarządzania tożsamościami i dostępem w chmurze, która ułatwia pracownikom logowanie się i dostęp do zasobów

* Definicja roli-definicja roli jest kolekcją uprawnień.  Usługa AAD ma role standardowe (właściciela, współautora lub czytelnika), które zawierają poziomy uprawnień do wykonywania operacji, takich jak Odczyt, zapis i usuwanie w zasobie platformy Azure. Role mogą również być definicjami niestandardowymi utworzonymi przez użytkowników z określonymi szczegółowymi uprawnieniami.

* Rejestracja aplikacji — podczas rejestrowania aplikacji usługi Azure AD tworzone są dwa obiekty w dzierżawie usługi Azure AD, obiekt aplikacji i obiekt główny usługi. Rozważmy obiekt aplikacji jako globalną reprezentację aplikacji do użycia we wszystkich dzierżawcach i nazwę główną usługi jako reprezentację lokalną do użycia w określonej dzierżawie.

### <a name="security-principal-concepts"></a>Pojęcia związane z podmiotami zabezpieczeń

* Podmiot zabezpieczeń — podmiot zabezpieczeń to obiekt, który reprezentuje użytkownika, grupę, nazwę główną usługi lub tożsamość zarządzaną żądającą dostępu do zasobów platformy Azure.

* Użytkownik — osoba mająca profil w usłudze Azure Active Directory.

* Grupa — zbiór użytkowników, utworzony w usłudze Azure Active Directory. W przypadku przypisania roli do grupy wszyscy użytkownicy w grupie otrzymają tę rolę.

* Jednostka usługi — tożsamość zabezpieczeń używana przez aplikacje lub usługi w celu uzyskania dostępu do określonych zasobów platformy Azure. Można traktować ją jako odpowiednik tożsamości użytkownika (nazwy użytkownika i hasła lub certyfikatu) w przypadku aplikacji.

* Tożsamość zarządzana — tożsamość w Azure Active Directory, która jest automatycznie zarządzana przez platformę Azure.

* Identyfikator obiektu (identyfikator klienta) — unikatowy identyfikator wygenerowany przez usługę Azure AD, który jest powiązany z jednostką usługi podczas początkowej aprowizacji.

## <a name="security-principal-registration"></a>Rejestracja podmiotu zabezpieczeń

1. Administrator rejestruje użytkownika lub aplikację (nazwę główną usługi) w Azure Active Directory.

2. Administrator tworzy Azure Key Vault i konfiguruje zasady dostępu (ACL).

3. Obowiązkowe Administrator konfiguruje Zaporę Azure Key Vault.

![OBRAZ](../media/authentication-1.png)

## <a name="understand-the-key-vault-authentication-flow"></a>Informacje o przepływie uwierzytelniania Key Vault

1. Jednostka usługi wykonuje wywołanie uwierzytelnienia w usłudze AAD. może się to zdarzyć na kilka sposobów:
    * Użytkownik może zalogować się do Azure Portal przy użyciu nazwy użytkownika i hasła.
    * Aplikacja używa identyfikatora klienta i prezentuje klucz tajny klienta lub certyfikat klienta do usługi AAD
    * Zasób platformy Azure, taki jak maszyna wirtualna, ma przypisany plik MSI i kontaktuje się z punktem końcowym IMDS REST w celu uzyskania tokenu dostępu.

2. W przypadku pomyślnego uwierzytelnienia w usłudze AAD zostanie udzielony token uwierzytelniania OAuth.
3. Nazwa główna usługi wywołuje Key Vault.
4. Zapora Azure Key Vault określa, czy zezwolić na wywołanie.
    * Scenariusz 1. Key Vault Zapora jest wyłączona. publiczny punkt końcowy (URI) magazynu kluczy jest dostępny z publicznego Internetu. Wywołanie jest dozwolone.
    * Scenariusz 2. obiekt wywołujący jest zaufaną usługą Azure Key Vault. Niektóre usługi platformy Azure mogą ominąć zaporę magazynu kluczy w przypadku wybrania opcji. [Key Vault listy zaufanych usług](https://docs.microsoft.com/azure/key-vault/general/overview-vnet-service-endpoints#trusted-services)
    * Scenariusz 3: element wywołujący znajduje się na liście w zaporze Azure Key Vault przez adres IP, sieć wirtualną lub punkt końcowy usługi.
    * Scenariusz 4: obiekt wywołujący może dotrzeć do Azure Key Vault za pośrednictwem skonfigurowanego połączenia z linkiem prywatnym.
    * Scenariusz 5: obiekt wywołujący nie jest autoryzowany i zwrócona jest niedozwolona odpowiedź.
5. Key Vault nawiązać połączenie z usługą AAD w celu zweryfikowania tokenu dostępu jednostki usługi.
6. Key Vault sprawdza, czy jednostka usługi ma wystarczające uprawnienia dostępu, aby wykonać żądaną operację. w tym przykładzie operacja jest pobierana jako wpis tajny.
7. Key Vault dostarcza klucz tajny do jednostki usługi.

![OBRAZ](../media/authentication-2.png)

## <a name="grant-a-service-principal-access-to-key-vault"></a>Przyznaj jednostce usługi dostęp do Key Vault

1. Utwórz nazwę główną usługi, jeśli jeszcze jej nie masz. [Tworzenie nazwy głównej usługi](https://docs.microsoft.com/azure/active-directory/develop/howto-create-service-principal-portal)
2. Dodaj przypisanie roli do nazwy głównej usługi w Azure Key Vault ustawieniach usługi IAM. Można dodać wstępnie przypisane role właściciela, współautora lub czytelnika. Możesz również utworzyć role niestandardowe dla swojej jednostki usługi. Należy przestrzegać głównego poziomu najniższych uprawnień i zapewnić tylko minimalny dostęp wymagany dla nazwy głównej usługi. 
3.  Skonfiguruj zaporę magazynu kluczy. Zaporę magazynu kluczy można pozostawić wyłączyć i zezwolić na dostęp z publicznej sieci Internet (mniej bezpieczne, łatwiej skonfigurować). Możesz również ograniczyć dostęp do określonych zakresów adresów IP, punktów końcowych usług, sieci wirtualnych lub prywatnych punktów końcowych (bezpieczniejsze).
4.  Dodawanie zasad dostępu dla nazwy głównej usługi, jest to lista operacji, które może wykonywać jednostka usługi w magazynie kluczy. Należy użyć podmiotu zabezpieczeń najniższych uprawnień i ograniczyć operacje, które może wykonywać jednostka usługi. Jednakże jeśli nie podasz wystarczających uprawnień, nazwa główna usługi zostanie odmowa dostępu.

## <a name="tutorial"></a>Samouczek

W tym samouczku dowiesz się, jak skonfigurować jednostkę usługi do uwierzytelniania w magazynie kluczy i pobrać wpis tajny. 

### <a name="part-1--create-a-service-principal-in-the-azure-portal"></a>Część 1. Tworzenie nazwy głównej usługi w Azure Portal

1. Logowanie do witryny Azure Portal
1. Wyszukaj Azure Active Directory
1. Kliknij kartę "rejestracje aplikacji".
1. Kliknij pozycję "+ Nowa rejestracja"
1. Utwórz nazwę dla nazwy głównej usługi
1. Wybierz pozycję Zarejestruj

W tym momencie masz zarejestrowaną nazwę główną usługi. Można je wyświetlić, wybierając pozycję "rejestracje aplikacji". Do nazwy głównej usługi zostanie przypisany identyfikator GUID klienta, co będzie miało charakter "username" dla nazwy głównej usługi. Teraz musimy utworzyć "hasło" dla nazwy głównej usługi, można użyć klucza tajnego klienta lub certyfikatu klienta. Należy pamiętać, że użycie klucza tajnego klienta do uwierzytelniania nie jest bezpieczne i powinno być używane tylko do celów testowych. Ten samouczek pokazuje, jak używać certyfikatu klienta.

### <a name="part-2-create-a-client-certificate-for-your-service-principal"></a>Część 2. Tworzenie certyfikatu klienta dla nazwy głównej usługi

1. Tworzenie certyfikatu

    * Opcja 1. Tworzenie certyfikatu przy użyciu [OpenSSL](https://www.openssl.org/) (tylko do celów testowych nie należy używać certyfikatów z podpisem własnym w środowisku produkcyjnym)

    ```console
    openssl req -x509 -newkey rsa:4096 -keyout key.pem -out cert.pem -days 365
    ```

    * Opcja 2: Tworzenie certyfikatu przy użyciu magazynu kluczy. [Tworzenie certyfikatu w Azure Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#creating-your-first-key-vault-certificate)

1. Pobierz certyfikat w formacie PEM
1. Zaloguj się do Azure Portal i przejdź do Azure Active Directory
1. Kliknij pozycję "rejestracje aplikacji".
1. Wybierz nazwę główną usługi utworzoną w części 1.
1. Kliknij kartę "certyfikaty i wpisy tajne" w jednostce usługi
1. Przekaż certyfikat przy użyciu przycisku "Przekaż certyfikat"

### <a name="part-3-configure-an-azure-key-vault"></a>Część 3: Konfigurowanie Azure Key Vault

1. Tworzenie [linku](https://docs.microsoft.com/azure/key-vault/secrets/quick-create-portal#create-a-vault) Azure Key Vault

2. Konfigurowanie uprawnień Key Vault IAM
    1. Przejdź do magazynu kluczy
    1. Wybierz kartę "Access Control (IAM)"
    1. Kliknij pozycję Dodaj przypisanie roli
    1. Wybierz rolę "Współautor" z listy rozwijanej
    1. Wprowadź nazwę lub identyfikator klienta utworzonej jednostki usługi
    1. Kliknij pozycję "Wyświetl przypisania ról", aby potwierdzić, że nazwa główna usługi jest wyświetlana

3. Konfigurowanie uprawnień dostępu Key Vault zasad
    1. Przejdź do magazynu kluczy
    1. Wybierz kartę "zasady dostępu" w obszarze "Ustawienia"
    1. Wybierz łącze "+ Dodawanie zasad dostępu"
    1. Na liście rozwijanej uprawnienia do wpisów tajnych zaznacz uprawnienia "Pobierz" i "Wyświetl".
    1. Wybierz nazwę główną usługi według nazwy lub identyfikatora klienta.
    1. Wybierz pozycję "Dodaj"
    1. Wybierz pozycję "Zapisz"

4. Utwórz wpis tajny w magazynie kluczy.
    1. Przejdź do magazynu kluczy
    1. Kliknij kartę "wpisy tajne" w obszarze Ustawienia
    1. Kliknij pozycję "+ Generuj/Importuj"
    1. Utwórz nazwę wpisu tajnego, w tym przykładzie Nazwij wpis tajny "test"
    1. Utwórz wartość dla wpisu tajnego, w tym przykładzie ustawimy wartość "password123"

Teraz podczas uruchamiania kodu z komputera lokalnego można uwierzytelnić się w magazynie kluczy przez uzyskanie tokenu dostępu przez przesłanie identyfikatora klienta i ścieżki do certyfikatu.

### <a name="part-4-retrieve-the-secret-from-your-azure-key-vault-in-an-application-python"></a>Część 4: pobieranie wpisu tajnego z Azure Key Vault w aplikacji (Python)

Użyj poniższego przykładu kodu, aby sprawdzić, czy aplikacja może pobrać wpis tajny z magazynu kluczy przy użyciu skonfigurowanej jednostki usługi. 

```python
from azure.keyvault.secrets import SecretClient
from azure.identity import CertificateCredential


tenant_id = ""                                             ##ENTER AZURE TENANT ID
vault_url = "https://{VAULT NAME}.vault.azure.net/"        ##ENTER THE URL OF YOUR KEY VAULT
client_id = ""                                             ##ENTER CLIENT ID OF SERVICE PRINCIPAL
cert_path = r"C:\Users\{USERNAME}\{PATH}\{CERT_NAME}.pem"  ##ENTER PATH TO CERTIFICATE

def main():

    #AUTHENTICATION TO AAD USING CLIENT ID AND CLIENT CERTIFICATE
    token = CertificateCredential(tenant_id= tenant_id, client_id=client_id, certificate_path=cert_path)

    #AUTHENTICATION TO KEY VAULT PRESENTING AAD TOKEN
    client = SecretClient(vault_url=vault_url, credential=token)

    #CALL TO KEY VAULT TO GET SECRET
    secret = client.get_secret('{SECRET_NAME}')            ##ENTER NAME OF SECRET IN KEY VAULT

    #GET PLAINTEXT OF SECRET
    print(secret.value)

#CALL MAIN()
if __name__ == "__main__":
    main()
```

![OBRAZ](../media/authentication-3.png)


## <a name="next-steps"></a>Następne kroki

1. Dowiedz się, jak rozwiązywać problemy z błędami uwierzytelniania magazynu kluczy. [Przewodnik rozwiązywania problemów Key Vault](https://docs.microsoft.com/azure/key-vault/general/rest-error-codes)
