---
title: Tworzenie i scalanie CSR w Azure Key Vault
description: Tworzenie i scalanie CSR w Azure Key Vault
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a85656909df5538f9f57e05d79ae768623d7eba6
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289603"
---
# <a name="creating-and-merging-csr-in-key-vault"></a>Tworzenie i scalanie CSR w Key Vault

Azure Key Vault obsługuje przechowywanie certyfikatu cyfrowego wystawionego przez dowolny wybrany urząd certyfikacji w magazynie kluczy. Obsługuje ona tworzenie żądania podpisania certyfikatu za pomocą pary kluczy prywatnych-publicznych, która może być podpisana przez wybrany urząd certyfikacji. Może to być wewnętrzny urząd certyfikacji przedsiębiorstwa lub zewnętrzny publiczny urząd certyfikacji. Żądanie podpisania certyfikatu (również CSR lub żądanie certyfikacji) to komunikat wysyłany przez użytkownika do urzędu certyfikacji w celu żądania wystawienia certyfikatu cyfrowego.

Aby uzyskać więcej ogólnych informacji o certyfikatach, zobacz [Azure Key Vault Certificates](./about-certificates.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="adding-certificate-in-key-vault-issued-by-a-non-trusted-ca"></a>Dodawanie certyfikatu w Key Vault wystawiony przez niezaufany urząd certyfikacji

Poniższe kroki ułatwią utworzenie certyfikatu z urzędów certyfikacji, które nie są partnerskie Key Vault (na przykład GoDaddy nie jest zaufanym urzędem certyfikacji magazynu kluczy) 


### <a name="azure-powershell"></a>Azure PowerShell



1.  Najpierw **Utwórz zasady dotyczące certyfikatów**. Key Vault nie zarejestruje ani nie odnowi certyfikatu z wystawcy w imieniu użytkownika, ponieważ urząd certyfikacji wybrany w tym scenariuszu nie jest obsługiwany, w związku z czym wystawca jest ustawiony na nieznany.

    ```azurepowershell
    $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
    ```


2. Tworzenie **żądania podpisania certyfikatu**

   ```azurepowershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

3. Pobieranie żądania CSR **podpisanego przez urząd certyfikacji** `$certificateOperation.CertificateSigningRequest` jest base4 zakodowane żądanie podpisywania certyfikatu certyfikatu. Ten obiekt BLOB i zrzut można wykonać w witrynie sieci Web żądania certyfikatu wystawcy. Ten krok różni się od urzędu certyfikacji do urzędu certyfikacji, jednak najlepszym sposobem jest wyszukanie wytycznych urzędu certyfikacji dotyczące sposobu wykonywania tego kroku. Możesz również użyć narzędzi, takich jak CertReq lub OpenSSL, aby uzyskać podpisane żądanie certyfikatu i zakończyć proces generowania certyfikatu.


4. **Scalanie podpisanego żądania** w Key Vault po podpisaniu żądania certyfikatu przez wystawcę można przywrócić podpisany certyfikat i scalić go z początkową parą kluczy prywatnych i publicznych utworzonych w Azure Key Vault

    ```azurepowershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

    Żądanie certyfikatu zostało teraz pomyślnie scalone.

### <a name="azure-portal"></a>Azure Portal

1.  Aby wygenerować CSR dla wybranego urzędu certyfikacji, przejdź do magazynu kluczy, do którego chcesz dodać certyfikat.
2.  Na stronie właściwości Key Vault wybierz pozycję **Certyfikaty**.
3.  Wybierz kartę **generowanie/Importowanie** .
4.  Na ekranie **Tworzenie certyfikatu** wybierz następujące wartości:
    - **Metoda tworzenia certyfikatu:** Utworzenie.
    - **Nazwa certyfikatu:** ContosoManualCSRCertificate.
    - **Typ urzędu certyfikacji:** Certyfikat wystawiony przez niezintegrowany urząd certyfikacji
    - **Temat:**`"CN=www.contosoHRApp.com"`
    - Wybierz inne wartości zgodnie z potrzebami. Kliknij pozycję **Utwórz**.

    ![Właściwości certyfikatu](../media/certificates/create-csr-merge-csr/create-certificate.png)
6.  Zobaczysz, że certyfikat został teraz dodany na liście certyfikatów. Wybierz ten nowy certyfikat, który został właśnie utworzony. Bieżący stan certyfikatu to "wyłączone", ponieważ nie został jeszcze wystawiony przez urząd certyfikacji.
7. Kliknij kartę **operacja certyfikatu** i wybierz pozycję **Pobierz CSR**.
 ![Zrzut ekranu, który podświetla przycisk Pobierz CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

8.  Przyjmij plik CSR do urzędu certyfikacji, aby żądanie zostało podpisane.
9.  Gdy żądanie zostanie podpisane przez urząd certyfikacji, Przywróć plik certyfikatu, aby **scalić podpisane żądanie** na tym samym ekranie operacji certyfikatu.

Żądanie certyfikatu zostało teraz pomyślnie scalone.

## <a name="adding-more-information-to-csr"></a>Dodawanie dodatkowych informacji do CSR

Jeśli chcesz dodać więcej informacji podczas tworzenia usługi CSR, na przykład 
    - Kraj:
    - Miasto/miejscowość:
    - Województwo:
    - Podmiot
    - Jednostka organizacyjna: możesz dodać wszystkie te informacje podczas tworzenia CSR, definiując je w SubjectName.

Przykład
    ```SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
    ```

>[!Note]
>Jeśli żądasz certyfikatu DV ze wszystkimi szczegółami w CSR, urząd certyfikacji może odrzucić żądanie, ponieważ urząd certyfikacji może nie być w stanie zweryfikować wszystkich informacji zawartych w żądaniu. Jeśli żądasz certyfikatu OV, warto dodać wszystkie te informacje w CSR.


## <a name="troubleshoot"></a>Rozwiązywanie problemów

- **Typ błędu "klucz publiczny certyfikatu jednostki końcowej w określonej zawartości certyfikatu X. 509 nie jest zgodny z publiczną częścią określonego klucza prywatnego. Sprawdź, czy certyfikat jest prawidłowy "** ten błąd może wystąpić, jeśli nie scalasz CSR z tym samym zainicjowanym żądaniem CSR. Za każdym razem, gdy jest tworzone żądanie podpisania certyfikatu (CSR), tworzony jest klucz prywatny, który należy dopasować podczas scalania podpisanego żądania.
    
- Czy po scaleniu CSR zostanie scalony cały łańcuch?
    Tak, nastąpi scalenie całego łańcucha, pod warunkiem, że użytkownik przejdzie do scalania z plikiem P7B.

- Jeśli certyfikat wystawiony w stanie "Disabled" w Azure Portal, należy wyświetlić **operację certyfikatu** w celu przejrzenia komunikatu o błędzie dla tego certyfikatu.

Aby uzyskać więcej informacji, zobacz [operacje na certyfikatach w dokumentacji interfejsu API REST Key Vault](/rest/api/keyvault). Aby uzyskać informacje dotyczące ustanawiania uprawnień, zobacz temat [magazyny — Tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate) i [magazyny — zasady dostępu aktualizacji](/rest/api/keyvault/vaults/updateaccesspolicy).

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)