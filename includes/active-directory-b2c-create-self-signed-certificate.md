---
author: msmimart
ms.service: active-directory-b2c
ms.subservice: B2C
ms.topic: include
ms.date: 01/27/2021
ms.author: mimart
ms.openlocfilehash: 41d9962657aa81dbe34a52302d1b68ec655f2893
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "102095293"
---
Jeśli nie masz jeszcze certyfikatu, możesz użyć certyfikatu z podpisem własnym. Certyfikat z podpisem własnym to certyfikat zabezpieczeń, który nie jest podpisany przez urząd certyfikacji (CA) i nie zapewnia gwarancji zabezpieczeń certyfikatu podpisanego przez urząd certyfikacji. 

# <a name="windows"></a>[Windows](#tab/windows)

W systemie Windows należy wygenerować certyfikat przy użyciu polecenia cmdlet [New-SelfSignedCertificate](/powershell/module/pkiclient/new-selfsignedcertificate) programu PowerShell.

1. Wykonaj to polecenie programu PowerShell, aby wygenerować certyfikat z podpisem własnym. Zmodyfikuj `-Subject` argument jako odpowiedni dla aplikacji i Azure AD B2C nazwę dzierżawcy. Możesz również dostosować datę, `-NotAfter` Aby określić inne wygaśnięcie certyfikatu.

    ```PowerShell
    New-SelfSignedCertificate `
        -KeyExportPolicy Exportable `
        -Subject "CN=yourappname.yourtenant.onmicrosoft.com" `
        -KeyAlgorithm RSA `
        -KeyLength 2048 `
        -KeyUsage DigitalSignature `
        -NotAfter (Get-Date).AddMonths(12) `
        -CertStoreLocation "Cert:\CurrentUser\My"
    ```

1. Otwórz przystawkę **Zarządzaj certyfikatami użytkowników**  >  **bieżące**  >    >  **Certyfikaty** osobiste  >  *yourappname.yourtenant.onmicrosoft.com*.
1. Wybierz certyfikat, a następnie wybierz pozycję **Akcja**  >  **wszystkie zadania**  >  **Eksportuj**.
1. Wybierz pozycję **tak**  >  **dalej**  >  **tak, eksportuj klucz prywatny**  >  **dalej**.
1. Zaakceptuj wartości domyślne w **formacie pliku eksportu**.
1. Podaj hasło do certyfikatu.

Aby Azure AD B2C zaakceptować hasło pliku PFX, należy zaszyfrować hasło przy użyciu opcji TripleDES-SHA1 w narzędziu eksportu magazynu certyfikatów systemu Windows, a nie AES256-SHA256.

# <a name="macos"></a>[macOS](#tab/macos)

W systemie macOS Użyj [asystenta certyfikatów](https://support.apple.com/guide/keychain-access/aside/glosa3ed0609/11.0/mac/11.0) w dostępie do łańcucha kluczy w celu wygenerowania certyfikatu.

1. Postępuj zgodnie z instrukcjami dotyczącymi [tworzenia certyfikatów z podpisem własnym w dostępie do łańcucha kluczy na komputerze Mac](https://support.apple.com/guide/keychain-access/kyca8916/mac).
1. W aplikacji dostępu pęku kluczy na komputerze Mac wybierz utworzony certyfikat.
1. Wybierz pozycję **plik**  >  **Eksportuj elementy**.
1. Wybierz nazwę pliku, aby zapisać certyfikat. Na przykład certyfikat z podpisem **własnym. p12**.
1. W polu **Format pliku** wybierz opcję **wymiana informacji osobistych (. p12)**.
1. Wybierz pozycję **Zapisz**.
1. Wprowadź **hasło**, a następnie **Sprawdź** hasło.
1. Zamień rozszerzenie pliku na `.pfx` . Na przykład **self-signed-Certificate. pfx**.

---