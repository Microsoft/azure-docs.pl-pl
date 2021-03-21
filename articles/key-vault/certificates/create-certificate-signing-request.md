---
title: Tworzenie i scalanie CSR w Azure Key Vault
description: Dowiedz się, jak utworzyć i scalić CSR w Azure Key Vault.
services: key-vault
author: msmbaldwin
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: aa631f4c505200c2c8abc67d4e22ffbab23e015c
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "98789030"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Tworzenie i scalanie CSR w Key Vault

Azure Key Vault obsługuje przechowywanie certyfikatów cyfrowych wystawionych przez dowolny urząd certyfikacji (CA). Obsługuje ona tworzenie żądania podpisania certyfikatu (CSR) z parą kluczy prywatnych/publicznych. CSR może być podpisany przez dowolny urząd certyfikacji (wewnętrzny urząd certyfikacji przedsiębiorstwa lub zewnętrzny publiczny urząd certyfikacji). CSR to komunikat wysyłany do urzędu certyfikacji w celu zażądania certyfikatu cyfrowego.

Aby uzyskać więcej ogólnych informacji o certyfikatach, zobacz [Azure Key Vault Certificates](./about-certificates.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Dodawanie certyfikatów w Key Vault wystawionych przez partnerskich urzędów certyfikacji

Aby uprościć tworzenie certyfikatów, Key Vault partnerzy z poniższymi urzędami certyfikacji.

|Dostawca|Typ certyfikatu|Konfiguracja konfiguracji  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault oferuje certyfikaty SSL OV lub EV z DigiCert| [Przewodnik po integracji](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault oferuje certyfikaty SSL OV lub EV z GlobalSign| [Przewodnik po integracji](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Dodawanie certyfikatów w Key Vault wystawionych przez niepartnerskich urzędów certyfikacji

Wykonaj następujące kroki, aby dodać certyfikat z urzędów certyfikacji, które nie są obsługiwane przez program Key Vault. (Na przykład GoDaddy nie jest zaufanym urzędem certyfikacji Key Vault).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do magazynu kluczy, do którego chcesz dodać certyfikat.
1. Na stronie właściwości wybierz pozycję **Certyfikaty**.
1. Wybierz kartę **generowanie/Importowanie** .
1. Na ekranie **Tworzenie certyfikatu** wybierz następujące wartości:
    - **Metoda tworzenia certyfikatu**: generate.
    - **Nazwa certyfikatu**: ContosoManualCSRCertificate.
    - **Typ urzędu** certyfikacji: certyfikat wystawiony przez niezintegrowanego urzędu certyfikacji.
    - **Temat**: `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Jeśli używasz względnej nazwy wyróżniającej (RDN), która ma przecinek (,) w wartości, zawiń wartość zawierającą znak specjalny w podwójnych cudzysłowach. 
     >
     > Przykładowy wpis do **tematu**: `DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > W tym przykładzie RDN `OU` zawiera wartość z przecinkiem w nazwie. Wynikowe wyniki dla programu `OU` to **docs, contoso**.
1. Wybierz inne wartości zgodnie z potrzebami, a następnie wybierz pozycję **Utwórz** , aby dodać certyfikat do listy **certyfikatów** .

    ![Zrzut ekranu przedstawiający właściwości certyfikatu](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Na liście **Certyfikaty** wybierz nowy certyfikat. Bieżący stan certyfikatu jest **wyłączony** , ponieważ nie został jeszcze wystawiony przez urząd certyfikacji.
1. Na karcie **operacja certyfikatu** wybierz pozycję **Pobierz CSR**.

   ![Zrzut ekranu, który podświetla przycisk Pobierz CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Urząd certyfikacji musi podpisać CSR (CSR).
1. Po podpisaniu żądania wybierz pozycję **Scal podpisane żądanie** na karcie **operacja certyfikatu** , aby dodać podpisany certyfikat do Key Vault.

Żądanie certyfikatu zostało teraz pomyślnie scalone.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz zasady dotyczące certyfikatów. Ponieważ urząd certyfikacji wybrany w tym scenariuszu nie jest partnerem, a **wystawca** jest ustawiony na **nieznany** i Key Vault nie rejestruje ani nie odnawia certyfikatu.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Jeśli używasz względnej nazwy wyróżniającej (RDN), która ma przecinek (,) w wartości, użyj apostrofów dla pełnej wartości lub zestawu wartości i zawiń wartość zawierającą znak specjalny w podwójnych cudzysłowach. 
     >
     >Przykładowy wpis do **podmiotu**: `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . W tym przykładzie wartość jest `OU` odczytywana jako **docs, contoso**. Ten format działa dla wszystkich wartości, które zawierają przecinek.
     > 
     > W tym przykładzie RDN `OU` zawiera wartość z przecinkiem w nazwie. Wynikowe wyniki dla programu `OU` to **docs, contoso**.

1. Utwórz CSR.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Urząd certyfikacji musi podpisać CSR. `$csr.CertificateSigningRequest`Jest to podstawowy kodowany certyfikat CSR dla certyfikatu. Można zrzucić ten obiekt BLOB do witryny sieci Web żądania certyfikatu wystawcy. Ten krok różni się od urzędu certyfikacji do urzędu certyfikacji. Zapoznaj się ze wskazówkami urzędu certyfikacji dotyczącymi sposobu wykonywania tego kroku. Możesz również użyć narzędzi, takich jak CertReq lub OpenSSL, aby uzyskać podpis CSR i zakończyć proces generowania certyfikatu.

1. Scal podpisane żądanie w Key Vault. Po podpisaniu żądania certyfikatu można je scalić z początkową parą kluczy prywatny/publiczny utworzoną w Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

Żądanie certyfikatu zostało teraz pomyślnie scalone.

---

## <a name="add-more-information-to-the-csr"></a>Dodaj więcej informacji do CSR

Jeśli chcesz dodać więcej informacji podczas tworzenia CSR, zdefiniuj je w polu **SubjectName**. Możesz chcieć dodać informacje, takie jak:
- Kraj
- Miasto/miejscowość
- Województwo
- Organizacja
- Jednostka organizacyjna

Przykład

   ```azure-powershell
   SubjectName="CN = docs.microsoft.com, OU = Microsoft Corporation, O = Microsoft Corporation, L = Redmond, S = WA, C = US"
   ```

> [!NOTE]
> Jeśli żądasz certyfikatu weryfikacji domeny z dodatkowymi informacjami, urząd certyfikacji może odrzucić żądanie, jeśli nie może sprawdzić poprawności wszystkich informacji w żądaniu. Dodatkowe informacje mogą być bardziej odpowiednie, jeśli żądasz certyfikatu weryfikacji organizacji (OV).

## <a name="faqs"></a>Często zadawane pytania

- Jak mogę monitorować mój CSR i zarządzać nim?

     Zobacz [monitorowanie i zarządzanie tworzeniem certyfikatów](./create-certificate-scenarios.md).

- Co zrobić, jeśli widzę **Typ błędu "klucz publiczny certyfikatu jednostki końcowej w określonej zawartości certyfikatu X. 509 nie jest zgodny z publiczną częścią określonego klucza prywatnego. Sprawdź, czy certyfikat jest prawidłowy "**?

     Ten błąd występuje, gdy nie jest scalany podpisany kod CSR z tym samym zainicjowanym żądaniem CSR. Każdy utworzony nowy CSR ma klucz prywatny, który musi być zgodny podczas scalania podpisanego żądania.

- Czy po scaleniu CSR zostanie scalony cały łańcuch?

     Tak, nastąpi scalenie całego łańcucha, pod warunkiem, że użytkownik przewróci plik. p7b do scalenia.

- Co zrobić, jeśli wystawiony certyfikat jest w stanie wyłączenia w Azure Portal?

     Wyświetl kartę **operacja certyfikatu** , aby przejrzeć komunikat o błędzie dla tego certyfikatu.

- Co zrobić, jeśli widzę **Typ błędu "podana nazwa podmiotu nie jest prawidłową nazwą X500"**?

     Ten błąd może wystąpić, jeśli **SubjectName** zawiera wszystkie znaki specjalne. Zobacz uwagi w Azure Portal i instrukcje programu PowerShell.

---

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
- [Dokumentacja interfejsu API REST Azure Key Vault](/rest/api/keyvault)
- [Magazyny — Utwórz lub zaktualizuj](/rest/api/keyvault/vaults/createorupdate)
- [Magazyny — aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy)