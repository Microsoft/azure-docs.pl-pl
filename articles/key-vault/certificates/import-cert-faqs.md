---
title: Często zadawane pytania — Azure Key Vault importowania certyfikatów
description: Często zadawane pytania — Azure Key Vault importowania certyfikatów
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: conceptual
ms.date: 07/20/2020
ms.author: sebansal
ms.openlocfilehash: 1063f7189de4bdf1aaca4a6d72c979476433c32f
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 07/23/2020
ms.locfileid: "87098270"
---
# <a name="frequently-asked-questions---azure-key-vault-certificate-import"></a>Często zadawane pytania — Azure Key Vault importowania certyfikatów

## <a name="frequently-asked-questions"></a>Często zadawane pytania

### <a name="how-can-i-import-a-certificate-in-azure-key-vault"></a>Jak mogę zaimportować certyfikat w Azure Key Vault?

Importuj certyfikat — w przypadku operacji importowania Magazyn kluczy Azure akceptuje dwa formaty certyfikatów PEM i PFX. Chociaż istnieją pliki PEM z tylko częścią publiczną, Magazyn kluczy platformy Azure wymaga i akceptuje tylko PEM lub PFX w folderze plików i z kluczem prywatnym. Postępuj zgodnie [z samouczkiem, aby zaimportować certyfikat](https://docs.microsoft.com/azure/key-vault/certificates/tutorial-import-certificate#import-a-certificate-to-key-vault)

### <a name="after-importing-password-protected-certificate-into-the-key-vault-and-then-downloading-it-i-am-not-able-to-see-the-password-associated-with-the-certificate"></a>Po zaimportowaniu certyfikatu chronionego hasłem do magazynu kluczy, a następnie jego pobraniem nie mogę zobaczyć hasła skojarzonego z certyfikatem?
    
Przekazany chroniony certyfikat po magazynie do magazynu kluczy nie spowoduje zapisania skojarzonego z nim hasła. Jest to wymagane tylko raz podczas operacji importowania. Chociaż jest to koncepcja według projektu, zawsze można uzyskać certyfikat jako klucz tajny i przekonwertować go z formatu Base64 na plik PFX, dodając poprzednie hasło za pośrednictwem [Azure PowerShell](https://social.technet.microsoft.com/wiki/contents/articles/37431.exporting-azure-app-service-certificates.aspx).

### <a name="how-can-i-resolve-bad-parameter-error-what-are-the-supported-certificate-formats-for-importing-in-key-vault"></a>Jak mogę rozwiązać błąd "błędny parametr"? Jakie formaty certyfikatów są obsługiwane na potrzeby importowania w magazynie kluczy?

Podczas importowania certyfikatu należy upewnić się, że klucz jest dołączony do samego pliku. Jeśli klucz prywatny jest osobno w innym formacie, należy połączyć klucz z certyfikatem. Niektóre urzędy certyfikacji dostarczają certyfikaty w różnych formatach, dlatego przed zaimportowaniem certyfikatu upewnij się, że są one w formacie PEM lub PFX oraz że używany klucz ma wartość RSA lub ECC. Zapoznaj się z nimi, aby przejrzeć [wymagania dotyczące certyfikatu](https://docs.microsoft.com/azure/key-vault/certificates/certificate-scenarios#formats-of-import-we-support) i [wymagania dotyczące klucza certyfikatu](https://docs.microsoft.com/azure/key-vault/keys/about-keys#cryptographic-protection).

### <a name="error-when-importing-certificate-via-portal-something-went-wrong-how-can-i-investigate-further"></a>Wystąpił błąd podczas importowania certyfikatu za pośrednictwem portalu („Wystąpił problem”). Jak można zbadać więcej?
    
Aby wyświetlić bardziej opisowy błąd, zaimportuj plik certyfikatu za pośrednictwem [interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/cli/azure/keyvault/certificate?view=azure-cli-latest#az-keyvault-certificate-import) lub [programu PowerShell](https://docs.microsoft.com/powershell/module/azurerm.keyvault/import-azurekeyvaultcertificate?view=azurermps-6.13.0).

### <a name="how-can-i-resolve-error-type-access-denied-or-user-is-unauthorized-to-import-certificate"></a>Jak mogę rozwiązać problem "typ błędu: odmowa dostępu lub użytkownik nie ma autoryzacji do importowania certyfikatu"?
    
Ta operacja wymaga uprawnień do certyfikatów/importu. Przejdź do lokalizacji magazynu kluczy. Musisz przyznać użytkownikowi odpowiednie uprawnienia w obszarze zasad dostępu. Przejdź do Key Vault zasad dostępu> > Dodaj zasady dostępu > wybierz uprawnienia certyfikatów (lub dodawaj uprawnienia) > podmiotu zabezpieczeń > wyszukiwanie, a następnie Dodaj adres e-mail użytkownika. [Przeczytaj więcej na temat zasad dostępu związanych z certyfikatami](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#certificate-access-control)


### <a name="how-can-i-resolve-error-type-conflict-when-creating-a-certificate"></a>Jak mogę rozwiązać problem "typ błędu: konflikt podczas tworzenia certyfikatu"?
    
Nazwa certyfikatu musi być unikatowa. Certyfikat o takiej samej nazwie może znajdować się w stanie nieusuwania nietrwałego, podobnie jak w przypadku [kompozycji certyfikatu](https://docs.microsoft.com/azure/key-vault/certificates/about-certificates#composition-of-a-certificate) w magazynie kluczy platformy Azure, jeśli w Key Vault istnieje inny klucz lub wpis tajny o tej samej nazwie, którą próbujesz określić dla certyfikatu, zostanie on zakończony niepowodzeniem i konieczne będzie usunięcie tego klucza lub wpisu tajnego lub użycie innej nazwy dla certyfikatu. [Wyświetl usunięty certyfikat](https://docs.microsoft.com/rest/api/keyvault/getdeletedcertificate/getdeletedcertificate)

### <a name="why-am-i-getting-the-error-type-char-length-is-too-long"></a>Dlaczego otrzymuję komunikat "typ błędu: znak o długości jest zbyt długi"?
    
* Długość nazwy podmiotu certyfikatu ma limit znaków wynoszący 200 znaków
* Długość hasła certyfikatu ma limit znaków wynoszący 200 znaków

### <a name="can-i-import-an-expired-certificate-in-azure-key-vault"></a>Czy mogę zaimportować wygasły certyfikat w magazynie kluczy Azure?
    
Nie, wygasłe certyfikaty PFX nie zostaną zaimportowane do Azure Key Vault.

### <a name="how-can-i-convert-my-certificate-to-proper-format"></a>Jak można przekonwertować certyfikat na właściwy format?

Możesz zażądać urzędu certyfikacji, aby dostarczyć certyfikat w wymaganym formacie. Ponadto istnieją narzędzia innych firm, które mogą pomóc w konwersji do właściwego formatu, jednak firma Microsoft nie będzie mogła zalecić dalszej próby uzyskania certyfikatu w żądanym formacie.

### <a name="can-i-import-certificates-from-non-partner-cas"></a>Czy mogę zaimportować certyfikaty z urzędów certyfikacji niepartnerskich?
Tak, można importować certyfikaty z dowolnego urzędu certyfikacji, ale Magazyn kluczy nie będzie mógł automatycznie odnowić tych certyfikatów. Można ustawić powiadomienia e-mail, aby otrzymywać powiadomienia o wygaśnięciu certyfikatu.

### <a name="if-i-import-a-certificate-from-a-partner-ca-will-the-auto-renew-feature-still-work"></a>Czy jeśli zaimportuję certyfikat z urzędu certyfikacji partnera, funkcja autoodnawiania będzie nadal działała?
Tak, musisz się upewnić, że po przekazaniu należy określić autorotację w zasadach wystawiania certyfikatu. Ponadto zmiany zostaną odzwierciedlone do czasu następnego cyklu lub wersji certyfikatu.


## <a name="next-steps"></a>Następne kroki

- [Azure Key Vault certyfikaty](/azure/key-vault/certificates/about-certificates)
