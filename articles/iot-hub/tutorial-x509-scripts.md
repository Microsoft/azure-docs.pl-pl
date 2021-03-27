---
title: Samouczek — używanie skryptów Microsoft do tworzenia certyfikatów testów x. 509 dla platformy Azure IoT Hub | Microsoft Docs
description: Samouczek — używanie niestandardowych skryptów do tworzenia certyfikatów urzędu certyfikacji i urządzeń dla platformy Azure IoT Hub
author: v-gpettibone
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: tutorial
ms.date: 02/26/2021
ms.author: robinsh
ms.custom:
- mvc
- 'Role: Cloud Development'
- 'Role: Data Analytics'
- devx-track-azurecli
ms.openlocfilehash: f11aec770818cd4ceeeda1ae7decf30acb9ca92b
ms.sourcegitcommit: a9ce1da049c019c86063acf442bb13f5a0dde213
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/27/2021
ms.locfileid: "105630713"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Samouczek: używanie skryptów dostarczonych przez firmę Microsoft do tworzenia certyfikatów testowych

Firma Microsoft udostępnia skrypty programu PowerShell i bash, które ułatwiają zrozumienie sposobu tworzenia własnych certyfikatów X. 509 i uwierzytelniania ich w IoT Hub. Skrypty znajdują się w serwisie [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates). Są one dostępne tylko w celach demonstracyjnych. Certyfikaty utworzone przez nie mogą być używane w środowisku produkcyjnym. Certyfikaty zawierają zakodowane hasła ("1234") i wygasają po upływie 30 dni. W środowisku produkcyjnym należy użyć własnych najlepszych rozwiązań w zakresie tworzenia certyfikatów i zarządzania okresem istnienia.

## <a name="powershell-scripts"></a>Skrypty środowiska PowerShell

### <a name="step-1---setup"></a>Krok 1 — Instalacja

Pobierz OpenSSL dla systemu Windows. Zapoznaj się z tematem <https://www.openssl.org/docs/faq.html#MISC4> miejsca, aby pobrać go lub <https://www.openssl.org/source/> skompilować ze źródła. Następnie Uruchom skrypty wstępne:

1. Skopiuj skrypty z usługi [GitHub](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) do katalogu lokalnego, w którym chcesz korzystać z programu. Wszystkie pliki zostaną utworzone jako elementy podrzędne tego katalogu.

1. Uruchom program PowerShell jako administrator.

1. Przejdź do katalogu, w którym załadowano skrypty.

1. W wierszu polecenia Ustaw zmienną środowiskową `$ENV:OPENSSL_CONF` na katalog, w którym znajduje się plik konfiguracyjny OpenSSL (OpenSSL. cnf).

1. Uruchom, `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` Aby program PowerShell mógł uruchamiać skrypty.

1. Uruchom polecenie `. .\ca-certs.ps1`. Dzięki temu funkcje skryptu są przywracane do globalnej przestrzeni nazw programu PowerShell.

1. Uruchom polecenie `Test-CACertsPrerequisites`. Program PowerShell używa magazynu certyfikatów systemu Windows do zarządzania certyfikatami. To polecenie sprawdza, czy nie będzie można później kolizji nazw z istniejącymi certyfikatami i czy OpenSSL jest poprawnie skonfigurowany.

### <a name="step-2---create-certificates"></a>Krok 2. Tworzenie certyfikatów

Uruchom polecenie `New-CACertsCertChain [ecc|rsa]`. W przypadku certyfikatów urzędu certyfikacji zaleca się używanie ECC, ale nie jest to wymagane. Ten skrypt aktualizuje katalog i magazyn certyfikatów systemu Windows przy użyciu następującego urzędu certyfikacji i certyfikatów pośrednich:

* intermediate1. pem
* intermediate2. pem
* intermediate3. pem
* RootCA. cer
* RootCA. pem

Po uruchomieniu skryptu Dodaj nowy certyfikat urzędu certyfikacji (RootCA. pem) do IoT Hub:

1. Przejdź do IoT Hub i przejdź do pozycji certyfikaty.

1. Wybierz pozycję **Dodaj**.

1. Wprowadź nazwę wyświetlaną dla certyfikatu urzędu certyfikacji.

1. Przekaż certyfikat urzędu certyfikacji.

1. Wybierz pozycję **Zapisz**.

### <a name="step-3---prove-possession"></a>Krok 3 — dowód posiadania

Teraz, gdy certyfikat urzędu certyfikacji został przekazany do IoT Hub, musisz udowodnić, że jesteś jego własnością:

1. Wybierz nowy certyfikat urzędu certyfikacji.

1. Wybierz pozycję **Generuj kod weryfikacyjny** w oknie dialogowym **Szczegóły certyfikatu** . Aby uzyskać więcej informacji, zobacz potwierdzenie [posiadania certyfikatu urzędu certyfikacji](tutorial-x509-prove-possession.md).

1. Utwórz certyfikat zawierający kod weryfikacyjny. Na przykład, jeśli kod weryfikacyjny to `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , uruchom następujące polecenie, aby utworzyć nowy certyfikat w katalogu roboczym zawierającym temat `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Skrypt tworzy certyfikat o nazwie `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Przekaż `VerifyCert4.cer` do IoT Hub w oknie dialogowym **Szczegóły certyfikatu** .

1. Wybierz pozycję **Weryfikuj**.

### <a name="step-4---create-a-new-device"></a>Krok 4 — Tworzenie nowego urządzenia

Utwórz urządzenie dla IoT Hub:

1. W IoT Hub przejdź do sekcji **urządzenia IoT** .

1. Dodaj nowe urządzenie o IDENTYFIKATORze `mydevice` .

1. W obszarze Uwierzytelnianie wybierz pozycję **podpisany urząd certyfikacji X. 509**.

1. Uruchom `New-CACertsDevice mydevice` , aby utworzyć nowy certyfikat urządzenia. Spowoduje to utworzenie następujących plików w katalogu roboczym:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Krok 5. Testowanie certyfikatu urządzenia

Przejdź do [testowania uwierzytelniania certyfikatu](tutorial-x509-test-certificate.md) , aby określić, czy certyfikat urządzenia może być uwierzytelniany w IoT Hub. Wymagana jest wersja PFX certyfikatu `mydevice.pfx` .

### <a name="step-6---cleanup"></a>Krok 6. Czyszczenie

Z menu Start Otwórz pozycję **Zarządzanie certyfikatami komputerów** i przejdź do menu  **Certyfikaty — komputer lokalny > osobisty**. Usuń certyfikaty wystawione przez "Azure IoT CA TestOnly *". Podobnie Usuń odpowiednie certyfikaty z **>zaufanego głównego urzędu certyfikacji > certyfikaty i >urzędów certyfikacji Pośrednich > certyfikatów**.

## <a name="bash-scripts"></a>Skrypty bash

### <a name="step-1---setup"></a>Krok 1 — Instalacja

1. Uruchom bash.

1. Przejdź do katalogu, w którym chcesz korzystać z programu. Wszystkie pliki zostaną utworzone w tym katalogu.

1. Skopiuj `*.cnf` i `*.sh` do katalogu roboczego.

### <a name="step-2---create-certificates"></a>Krok 2. Tworzenie certyfikatów

1. Uruchom polecenie `./certGen.sh create_root_and_intermediate`. Spowoduje to utworzenie następujących plików w katalogu **certyfikatów** :

    * Azure-IoT-test-Only. łańcuch. ca. CERT. pem
    * Azure-IoT-test-Only. Średniozaawansowany. CERT. pem
    * Azure-IoT-test-Only. root. ca. CERT. pem

1. Przejdź do IoT Hub i przejdź do pozycji **Certyfikaty**.

1. Wybierz pozycję **Dodaj**.

1. Wprowadź nazwę wyświetlaną dla certyfikatu urzędu certyfikacji.

1. Przekaż tylko certyfikat urzędu certyfikacji do IoT Hub. Nazwa certyfikatu to `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Wybierz pozycję **Zapisz**.

### <a name="step-3---prove-possession"></a>Krok 3 — dowód posiadania

1. Wybierz nowy certyfikat urzędu certyfikacji utworzony w poprzednim kroku.

1. Wybierz pozycję **Generuj kod weryfikacyjny** w oknie dialogowym **Szczegóły certyfikatu** . Aby uzyskać więcej informacji, zobacz potwierdzenie [posiadania certyfikatu urzędu certyfikacji](tutorial-x509-prove-possession.md).

1. Utwórz certyfikat zawierający kod weryfikacyjny. Na przykład, jeśli kod weryfikacyjny to `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` , uruchom następujące polecenie, aby utworzyć nowy certyfikat w katalogu roboczym o nazwie, `verification-code.cert.pem` który zawiera temat `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Przekaż certyfikat do centrum IoT Hub w oknie dialogowym **Szczegóły certyfikatu** .

1. Wybierz pozycję **Weryfikuj**.

### <a name="step-4---create-a-new-device"></a>Krok 4 — Tworzenie nowego urządzenia

Utwórz urządzenie dla Centrum IoT Hub:

1. W IoT Hub przejdź do sekcji urządzenia IoT.

1. Dodaj nowe urządzenie o IDENTYFIKATORze `mydevice` .

1. W obszarze Uwierzytelnianie wybierz pozycję **podpisany urząd certyfikacji X. 509**.

1. Uruchom `./certGen.sh create_device_certificate mydevice` , aby utworzyć nowy certyfikat urządzenia. Spowoduje to utworzenie dwóch plików o nazwach `new-device.cert.pem` i `new-device.cert.pfx` plikach w katalogu roboczym.

### <a name="step-5---test-your-device-certificate"></a>Krok 5. Testowanie certyfikatu urządzenia

Przejdź do [testowania uwierzytelniania certyfikatu](tutorial-x509-test-certificate.md) , aby określić, czy certyfikat urządzenia może być uwierzytelniany w IoT Hub. Wymagana jest wersja PFX certyfikatu `new-device.cert.pfx` .

### <a name="step-6---cleanup"></a>Krok 6. Czyszczenie

Ponieważ skrypt bash po prostu tworzy certyfikaty w katalogu roboczym, wystarczy je usunąć po zakończeniu testowania.

## <a name="next-steps"></a>Następne kroki

Aby przetestować certyfikat, przejdź do [testowania uwierzytelniania certyfikatu](tutorial-x509-test-certificate.md) w celu ustalenia, czy certyfikat może uwierzytelniać urządzenie w IoT Hub.
