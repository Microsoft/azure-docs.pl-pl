---
title: Często zadawane pytania — Azure Key Vault importowania certyfikatów
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące importowania certyfikatów Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 3b87d68fb9b5fa5f5f8dec43c39ea8b7dbf08b93
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "89651847"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Importowanie Azure Key Vault certyfikatów — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące importowania certyfikatów Azure Key Vault.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Jak mogę zaimportować certyfikat w Azure Key Vault?

W przypadku operacji importowania certyfikatu Azure Key Vault akceptuje dwa formaty plików certyfikatów: PEM i PFX. Chociaż istnieją pliki PEM z tylko częścią publiczną, Key Vault wymaga i akceptuje tylko plik PEM lub PFX z kluczem prywatnym. Aby uzyskać więcej informacji, zobacz [Importowanie certyfikatu do Key Vault](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Po zaimportowaniu certyfikatu chronionego hasłem do Key Vault a następnie jego pobrania, dlaczego nie mogę zobaczyć skojarzonego z nim hasła?
    
Gdy certyfikat zostanie zaimportowany i objęty ochroną w Key Vault, jego skojarzone hasło nie jest zapisywane. Hasło jest wymagane tylko raz podczas operacji importowania. Jest to zaprojektowane, ale zawsze można uzyskać certyfikat jako klucz tajny i przekonwertować go z formatu Base64 do PFX przez dodanie hasła za pośrednictwem [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Jak mogę rozwiązać błąd "zły parametr"? Jakie formaty certyfikatów są obsługiwane na potrzeby importowania do Key Vault?

Podczas importowania certyfikatu należy upewnić się, że klucz jest uwzględniony w pliku. Jeśli klucz prywatny jest przechowywany osobno w innym formacie, należy połączyć klucz z certyfikatem. Niektóre urzędy certyfikacji zapewniają certyfikaty w innych formatach. W związku z tym przed zaimportowaniem certyfikatu upewnij się, że jest on w formacie PEM lub PFX, i że klucz używa szyfrowania Rivest – Shamir – Adleman (RSA) lub kryptografii eliptyczna Cryptography (ECC). 

Aby uzyskać więcej informacji, zobacz Wymagania dotyczące [certyfikatu](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) i [wymagania dotyczące klucza certyfikatu](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection).

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Czy mogę zaimportować certyfikat przy użyciu szablonu usługi ARM?

Nie, nie jest możliwe wykonywanie operacji na certyfikatach przy użyciu szablonu Azure Resource Manager (ARM). Zalecanym rozwiązaniem jest użycie metod importowania certyfikatów w interfejsie API platformy Azure, interfejsu wiersza polecenia platformy Azure lub programu PowerShell. Jeśli masz istniejący certyfikat, możesz go zaimportować jako klucz tajny.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Po zaimportowaniu certyfikatu za pośrednictwem Azure Portal otrzymuję błąd "coś poszło źle". Jak można zbadać więcej?
    
Aby wyświetlić bardziej opisowy błąd, zaimportuj plik certyfikatu za pomocą [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) lub [programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Jak można rozwiązać problem "typ błędu: odmowa dostępu lub użytkownik nie ma autoryzacji do importowania certyfikatu"?
    
Operacja importowania wymaga udzielenia użytkownikowi uprawnień do importowania certyfikatu w ramach zasad dostępu. W tym celu przejdź do magazynu kluczy, wybierz pozycję **zasady dostępu**  >  **Dodaj zasady dostępu**  >  ,**Wybierz pozycję uprawnienia certyfikatów**  >  **podmiot zabezpieczeń**, Wyszukaj użytkownika, a następnie Dodaj adres e-mail użytkownika. 

Aby uzyskać więcej informacji na temat zasad dostępu związanych z certyfikatami, zobacz [Informacje o Azure Key Vault certyfikatów](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control).


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Jak mogę rozwiązać problem "typ błędu: konflikt podczas tworzenia certyfikatu"?
    
Nazwa każdego certyfikatu musi być unikatowa. Certyfikat o takiej samej nazwie może znajdować się w stanie nieusuniętym. Również, zgodnie z [kompozycją certyfikatu](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate), po utworzeniu nowego certyfikatu tworzy on adres tajny, który ma taką samą nazwę, więc jeśli w magazynie kluczy znajduje się inny klucz lub wpis tajny o takiej samej nazwie jak ten, który ma być określony dla certyfikatu, utworzenie certyfikatu zakończy się niepowodzeniem i konieczne będzie usunięcie klucza lub wpisu tajnego lub użycie innej nazwy dla certyfikatu. 

Aby uzyskać więcej informacji, zobacz [operacja pobrania usuniętego certyfikatu](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate).

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Dlaczego otrzymuję komunikat "typ błędu: długość znaku jest zbyt długa"?
Ten błąd może być spowodowany jedną z dwóch przyczyn:    
* Nazwa podmiotu certyfikatu jest ograniczona do 200 znaków.
* Hasło certyfikatu jest ograniczone do 200 znaków.

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Czy mogę zaimportować wygasły certyfikat do Azure Key Vault?
    
Nie, wygasłe certyfikaty PFX nie mogą zostać zaimportowane do Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Jak można przekonwertować certyfikat do właściwego formatu?

Aby zapewnić certyfikat w wymaganym formacie, można polecić urząd certyfikacji. Istnieją także narzędzia innych firm, które mogą pomóc w konwersji certyfikatu na właściwy format.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Czy mogę zaimportować certyfikaty z urzędów certyfikacji niepartnerskich?
Tak, można importować certyfikaty z dowolnego urzędu certyfikacji, ale Magazyn kluczy nie będzie w stanie automatycznie odnowić. Możesz ustawić przypomnienia, aby otrzymywać powiadomienia o wygaśnięciu certyfikatu.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Czy jeśli zaimportuję certyfikat z urzędu certyfikacji partnera, funkcja autoodnawiania będzie nadal działała?
Tak. Po przekazaniu certyfikatu Pamiętaj o określeniu autorotacji w zasadach wystawiania certyfikatów. Twoje ustawienia będą obowiązywać do momentu wydania następnego cyklu lub wersji certyfikatu.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Dlaczego nie widzę certyfikatu App Service, który został zaimportowany do Key Vault? 
Jeśli certyfikat został pomyślnie zaimportowany, należy go potwierdzić, przechodząc do okienka wpisy **tajne** .


## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault certyfikaty](/azure/key-vault/certificates/about-certificates)
