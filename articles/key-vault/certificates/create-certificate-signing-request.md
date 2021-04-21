---
title: Tworzenie i scalanie csr w Azure Key Vault
description: Dowiedz się, jak utworzyć i scalić csr w Azure Key Vault.
services: key-vault
author: msmbaldwin
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: certificates
ms.topic: tutorial
ms.date: 06/17/2020
ms.author: sebansal
ms.openlocfilehash: a4d079855e5aa05adb84b62d686d9f386608f7bb
ms.sourcegitcommit: 6686a3d8d8b7c8a582d6c40b60232a33798067be
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107752142"
---
# <a name="create-and-merge-a-csr-in-key-vault"></a>Tworzenie i scalanie csr w Key Vault

Azure Key Vault obsługuje przechowywanie certyfikatów cyfrowych wystawionych przez dowolny urząd certyfikacji. Obsługuje tworzenie żądania podpisania certyfikatu (CSR) z parą kluczy prywatnych/publicznych. Csr może być podpisany przez dowolny urząd certyfikacji (wewnętrzny urząd certyfikacji przedsiębiorstwa lub zewnętrzny publiczny urząd certyfikacji). Żądanie CSR to komunikat, który wysyłasz do urzędu certyfikacji w celu zażądania certyfikatu cyfrowego.

Aby uzyskać więcej ogólnych informacji na temat certyfikatów, [zobacz Azure Key Vault certyfikatów](./about-certificates.md).

Jeśli nie masz subskrypcji platformy Azure, przed rozpoczęciem utwórz [bezpłatne konto](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="add-certificates-in-key-vault-issued-by-partnered-cas"></a>Dodawanie certyfikatów w Key Vault wystawionych przez partnerskie certyfikaty certyfikacji

Key Vault współpracuje z następującymi urzędami certyfikacji, aby uprościć tworzenie certyfikatów.

|Dostawca|Typ certyfikatu|Konfiguracja  
|--------------|----------------------|------------------|  
|DigiCert|Key Vault oferuje certyfikaty SSL OV lub EV za pomocą firmy DigiCert| [Przewodnik integracji](./how-to-integrate-certificate-authority.md)
|GlobalSign|Key Vault oferuje certyfikaty SSL OV lub EV z globalsign| [Przewodnik integracji](https://support.globalsign.com/digital-certificates/digital-certificate-installation/generating-and-importing-certificate-microsoft-azure-key-vault)

## <a name="add-certificates-in-key-vault-issued-by-non-partnered-cas"></a>Dodawanie certyfikatów w Key Vault wystawionych przez nie partnerskich urzędu certyfikacji

Wykonaj następujące kroki, aby dodać certyfikat z urzędu certyfikacji, które nie są Key Vault. (Na przykład GoDaddy nie jest zaufanym Key Vault CA).

# <a name="portal"></a>[Portal](#tab/azure-portal)

1. Przejdź do magazynu kluczy, do którego chcesz dodać certyfikat.
1. Na stronie właściwości wybierz **pozycję Certyfikaty.**
1. Wybierz **kartę Generuj/Importuj.**
1. Na **ekranie Tworzenie certyfikatu** wybierz następujące wartości:
    - **Metoda tworzenia certyfikatu:** Generuj.
    - **Nazwa certyfikatu:** ContosoManualCSRCertificate.
    - **Typ urzędu certyfikacji ( CA):** certyfikat wystawiony przez nie zintegrowany urząd certyfikacji.
    - **Temat:** `"CN=www.contosoHRApp.com"` .
     > [!NOTE]
     > Jeśli używasz względnej nazwy wyróżniania (RDN), która zawiera przecinek (,), zawiń wartość zawierającą znak specjalny w podwójnych cudzysłowach. 
     >
     > Przykładowy wpis **tematu:**`DC=Contoso,OU="Docs,Contoso",CN=www.contosoHRApp.com`
     >
     > W tym przykładzie nazwa RDN `OU` zawiera wartość z przecinkiem w nazwie. Wynikowe dane wyjściowe dla `OU` to **Docs, Contoso**.
1. Wybierz inne wartości zgodnie z potrzebami, a następnie wybierz **pozycję Utwórz,** aby dodać certyfikat **do listy** Certyfikaty.

    ![Zrzut ekranu przedstawiający właściwości certyfikatu](../media/certificates/create-csr-merge-csr/create-certificate.png)  

1. Z **listy Certyfikaty** wybierz nowy certyfikat. Bieżący stan certyfikatu jest **wyłączony,** ponieważ nie został jeszcze wystawiony przez urząd certyfikacji.
1. Na karcie **Operacja certyfikatu** wybierz pozycję Pobierz **plik CSR.**

   ![Zrzut ekranu przedstawiający przycisk Pobierz plik CSR.](../media/certificates/create-csr-merge-csr/download-csr.png)

1. Urząd certyfikacji musi podpisać csr (csr).
1. Po podpisaniu żądania wybierz pozycję **Scal** podpisane żądanie na karcie **Operacja** certyfikatu, aby dodać podpisany certyfikat do Key Vault.

Żądanie certyfikatu zostało pomyślnie scalone.

# <a name="powershell"></a>[Program PowerShell](#tab/azure-powershell)

1. Utwórz zasady certyfikatów. Ponieważ urząd certyfikacji wybrany w tym scenariuszu nie jest partnerem, parametr **IssuerName** jest ustawiony na wartość **Nieznany** i Key Vault nie rejestruje ani nie odnawia certyfikatu.

   ```azure-powershell
   $policy = New-AzKeyVaultCertificatePolicy -SubjectName "CN=www.contosoHRApp.com" -ValidityInMonths 1  -IssuerName Unknown
   ```
     > [!NOTE]
     > Jeśli używasz względnej nazwy wyróżnianej (RDN), która zawiera przecinek (,), użyj a cudzysłowów pojedynczych dla pełnej wartości lub zestawu wartości i opakuj wartość zawierającą znak specjalny w podwójnych cudzysłowach. 
     >
     >Przykładowy wpis **w nazwie SubjectName:** `$policy = New-AzKeyVaultCertificatePolicy -SubjectName 'OU="Docs,Contoso",DC=Contoso,CN=www.contosoHRApp.com' -ValidityInMonths 1  -IssuerName Unknown` . W tym przykładzie `OU` wartość jest odczytywana jako **Docs, Contoso**. Ten format działa dla wszystkich wartości, które zawierają przecinek.
     > 
     > W tym przykładzie nazwa RDN `OU` zawiera wartość z przecinkiem w nazwie. Wynikowe dane wyjściowe dla `OU` to **Docs, Contoso**.

1. Utwórz csr.

   ```azure-powershell
   $csr = Add-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -CertificatePolicy $policy
   $csr.CertificateSigningRequest
   ```

1. Urząd certyfikacji musi podpisać csr. To `$csr.CertificateSigningRequest` zakodowany w formacie base kodowany żądania CSR dla certyfikatu. Ten obiekt blob można zrzucić do witryny internetowej żądania certyfikatu wystawcy. Ten krok różni się w zależności od urzędu certyfikacji. Sprawdź wytyczne urzędu certyfikacji dotyczące sposobu wykonania tego kroku. Możesz również użyć narzędzi, takich jak certreq lub openssl, aby podpisać plik CSR i ukończyć proces generowania certyfikatu.

1. Scal podpisane żądanie w Key Vault. Po podpisaniu żądania certyfikatu możesz scalić je z początkową parą kluczy prywatnych/publicznych utworzoną w Azure Key Vault.

    ```azure-powershell-interactive
    Import-AzKeyVaultCertificate -VaultName ContosoKV -Name ContosoManualCSRCertificate -FilePath C:\test\OutputCertificateFile.cer
    ```

Żądanie certyfikatu zostało pomyślnie scalone.

---

## <a name="add-more-information-to-the-csr"></a>Dodawanie dodatkowych informacji do csr

Jeśli chcesz dodać więcej informacji podczas tworzenia pliku CSR, zdefiniuj go w **subjectName**. Możesz dodać informacje, takie jak:
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
> Jeśli żądasz certyfikatu weryfikacji domeny (DV) z dodatkowymi informacjami, urząd certyfikacji może odrzucić żądanie, jeśli nie może zweryfikować wszystkich informacji w żądaniu. Dodatkowe informacje mogą być bardziej odpowiednie w przypadku żądania certyfikatu weryfikacji organizacji (OV).

## <a name="faqs"></a>Często zadawane pytania

- Jak mogę monitorować mój plik CSR lub zarządzać tym problemem?

     Zobacz [Monitorowanie tworzenia certyfikatów i zarządzanie nimi.](./create-certificate-scenarios.md)

- Co zrobić, jeśli widzę błąd typ "Klucz publiczny certyfikatu jednostki końcowej w określonej zawartości certyfikatu X.509 nie jest zgodne z publiczną częścią **określonego klucza prywatnego. Sprawdź, czy certyfikat jest prawidłowy"?**

     Ten błąd występuje, jeśli nie scalasz podpisanego żądania CSR z tym samym zainicjowane żądaniem CSR. Każdy nowo tworzyć żądanie CSR ma klucz prywatny, który musi być zgodne podczas scalania podpisanego żądania.

- Czy po scaleniu CSR cały łańcuch zostanie scalony?

     Tak, scali cały łańcuch, pod warunkiem, że użytkownik ponownie sprowadził plik p7b do scalenia.

- Co zrobić, jeśli wystawiony certyfikat ma stan wyłączony w Azure Portal?

     Wyświetl **kartę Operacja certyfikatu,** aby przejrzeć komunikat o błędzie dla tego certyfikatu.

- Co zrobić, jeśli widzę błąd "Podana nazwa podmiotu nie **jest prawidłową nazwą X500"?**

     Ten błąd może wystąpić, jeśli **parametr SubjectName** zawiera jakiekolwiek znaki specjalne. Zobacz uwagi w instrukcje Azure Portal programu PowerShell.

---

## <a name="next-steps"></a>Następne kroki

- [Uwierzytelnianie, żądania i odpowiedzi](../general/authentication-requests-and-responses.md)
- [Przewodnik dewelopera usługi Key Vault](../general/developers-guide.md)
- [Azure Key Vault interfejsu API REST](/rest/api/keyvault)
- [Magazyny — tworzenie lub aktualizowanie](/rest/api/keyvault/vaults/createorupdate)
- [Magazyny — aktualizowanie zasad dostępu](/rest/api/keyvault/vaults/updateaccesspolicy)