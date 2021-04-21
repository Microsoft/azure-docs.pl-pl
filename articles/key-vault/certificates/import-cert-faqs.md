---
title: Często zadawane pytania — importowanie Azure Key Vault certyfikatów
description: Uzyskaj odpowiedzi na często zadawane pytania dotyczące importowania Azure Key Vault certyfikatów.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: how-to
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 97dfc2db837f728b8cb4ece9a064f99006c9996b
ms.sourcegitcommit: 4b0e424f5aa8a11daf0eec32456854542a2f5df0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107767821"
---
# <a name="importing-azure-key-vault-certificates-faq"></a>Importowanie certyfikatów Azure Key Vault — często zadawane pytania

W tym artykule znajdują się odpowiedzi na często zadawane pytania dotyczące importowania Azure Key Vault certyfikatów.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Jak mogę zaimportować certyfikat do Azure Key Vault?

W przypadku operacji importowania certyfikatu Azure Key Vault dwa formaty plików certyfikatów: PEM i PFX. Chociaż istnieją pliki PEM tylko w części publicznej, Key Vault wymaga i akceptuje tylko plik PEM lub PFX z kluczem prywatnym. Aby uzyskać więcej informacji, zobacz [Importowanie certyfikatu do Key Vault](./tutorial-import-certificate.md#import-a-certificate-to-key-vault).

### <a name="after-i-import-a-password-protected-certificate-to-key-vault-and-then-download-it-why-cant-i-see-the-password-thats-associated-with-it"></a>Dlaczego po zaimportowaniu certyfikatu chronionego hasłem do usługi Key Vault i pobraniu go nie widzę skojarzonego z nim hasła?
     
Po zaimportowaniu certyfikatu i jego ochronie Key Vault jego skojarzone hasło nie jest zapisywane. Hasło jest wymagane tylko raz podczas operacji importowania. Jest to projektowe, ale zawsze można pobrać certyfikat jako klucz tajny i przekonwertować go z Base64 na PFX, dodając hasło za pomocą [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-a-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-to-key-vault"></a>Jak rozwiązać problem z błędem "Zły parametr"? Jakie formaty certyfikatów są obsługiwane do importowania do Key Vault?

Podczas importowania certyfikatu należy się upewnić, że klucz znajduje się w pliku . Jeśli masz klucz prywatny przechowywany oddzielnie w innym formacie, musisz połączyć klucz z certyfikatem. Niektóre urzędy certyfikacji zapewniają certyfikaty w innych formatach. Dlatego przed zaimportowaniem certyfikatu upewnij się, że jest on w formacie PEM lub PFX i że w kluczu jest używane szyfrowanie RSA (Rivest–Shamir–Adleman) lub kryptografia z krzywą eliptytyczną (ECC). 

Aby uzyskać więcej informacji, zobacz [wymagania dotyczące certyfikatów](./certificate-scenarios.md#formats-of-import-we-support) i wymagania dotyczące klucza [certyfikatu.](../keys/about-keys.md)

###  <a name="can-i-import-a-certificate-by-using-an-arm-template"></a>Czy mogę zaimportować certyfikat przy użyciu szablonu usługi ARM?

Nie, nie można wykonywać operacji na certyfikatach przy użyciu szablonu Azure Resource Manager (ARM). Zalecanym obejściem jest użycie metod importowania certyfikatów w interfejsie API platformy Azure, interfejsie wiersza polecenia platformy Azure lub programie PowerShell. Jeśli masz istniejący certyfikat, możesz zaimportować go jako klucz tajny.

### <a name="when-i-import-a-certificate-via-the-azure-portal-i-get-a-something-went-wrong-error-how-can-i-investigate-further"></a>Podczas importowania certyfikatu za pośrednictwem Azure Portal występuje błąd "Wystąpił problem". Jak można dokładniej zbadać ten temat?
     
Aby wyświetlić bardziej opisowy błąd, zaimportuj plik certyfikatu przy użyciu [interfejsu wiersza polecenia platformy Azure](/cli/azure/keyvault/certificate#az_keyvault_certificate_import) lub programu [PowerShell.](/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate)

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Jak rozwiązać problem "Typ błędu: odmowa dostępu lub brak autoryzacji użytkownika do importowania certyfikatu"?
    
Operacja importowania wymaga udzielenia użytkownikowi uprawnień do importowania certyfikatu w ramach zasad dostępu. Aby to zrobić, przejdź do magazynu kluczy, wybierz pozycję Zasady dostępu Dodaj zasady dostępu Wybierz jednostkę uprawnień certyfikatu, wyszukaj użytkownika, a następnie dodaj  >    >    >  adres e-mail użytkownika. 

Aby uzyskać więcej informacji na temat zasad dostępu związanych z certyfikatami, zobacz [Informacje Azure Key Vault certyfikatów .](./about-certificates.md#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Jak rozwiązać problem "Typ błędu: Konflikt podczas tworzenia certyfikatu"?
    
Każda nazwa certyfikatu musi być unikatowa. Certyfikat o tej samej nazwie może być w stanie usunięcia nieukreślone. Ponadto, zgodnie [](./about-certificates.md#composition-of-a-certificate)ze składem certyfikatu , podczas tworzenia nowego certyfikatu tworzony jest adresowalny klucz tajny o takiej samej nazwie, więc jeśli w magazynie kluczy istnieje inny klucz lub klucz tajny o takiej samej nazwie, jak nazwa, która ma być określana dla certyfikatu, tworzenie certyfikatu nie powiedzie się i konieczne będzie usunięcie tego klucza lub klucza tajnego albo użycie innej nazwy certyfikatu. 

Aby uzyskać więcej informacji, zobacz [operację Get Deleted Certificate (Uzyskiwanie usuniętego certyfikatu).](/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-error-type-char-length-is-too-long"></a>Dlaczego pojawia się komunikat "Typ błędu: długość znaków jest zbyt długa"?
Ten błąd może być spowodowany jedną z dwóch przyczyn:    
* Nazwa podmiotu certyfikatu jest ograniczona do 200 znaków.
* Hasło certyfikatu jest ograniczone do 200 znaków.


### <a name="error-the-specified-pem-x509-certificate-content-is-in-an-unexpected-format-please-check-if-certificate-is-in-valid-pem-format"></a>Błąd "Określona zawartość certyfikatu PEM X.509 jest w nieoczekiwanym formacie. Sprawdź, czy certyfikat jest w prawidłowym formacie PEM".
Sprawdź, czy zawartość pliku PEM używa separatorów wierszy w stylu systemu UNIX `(\n)`

### <a name="can-i-import-an-expired-certificate-to-azure-key-vault"></a>Czy mogę zaimportować wygasły certyfikat do Azure Key Vault?
    
Nie, wygasłych certyfikatów PFX nie można zaimportować do Key Vault.

### <a name="how-can-i-convert-my-certificate-to-the-proper-format"></a>Jak mogę przekonwertować certyfikat na odpowiedni format?

Możesz poprosić urząd certyfikacji o podanie certyfikatu w wymaganym formacie. Istnieją również narzędzia innych firm, które mogą pomóc w przekonwertowaniu certyfikatu na odpowiedni format.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Czy mogę zaimportować certyfikaty z innych niż partnerów urzędu certyfikacji?
Tak, można zaimportować certyfikaty z dowolnego urzędu certyfikacji, ale magazyn kluczy nie będzie mógł ich automatycznie odnowić. Przypomnienia można ustawić tak, aby były powiadamiane o wygaśnięciu certyfikatu.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-autorenewal-feature-still-work"></a>Jeśli zaimportować certyfikat z urzędu certyfikacji partnera, funkcja autorenewal będzie nadal działać?
Tak. Po przesłaniu certyfikatu pamiętaj o określeniu autorotacji w zasadach wystawiania certyfikatu. Ustawienia będą obowiązywać do momentu, aż zostanie wydany następny cykl lub wersja certyfikatu.

### <a name="why-cant-i-see-the-app-service-certificate-that-i-imported-to-key-vault"></a>Dlaczego nie widzę certyfikatu App Service zaimportowany do Key Vault? 
Jeśli certyfikat został zaimportowany pomyślnie, powinno być możliwe jego potwierdzenie, przechodząc do **okienka Wpisy** tajne.


## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault certyfikatów](./about-certificates.md)
