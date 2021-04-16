---
title: Samouczek — używanie skryptów firmy Microsoft do tworzenia certyfikatów testowych x.509 dla Azure IoT Hub | Microsoft Docs
description: Samouczek — używanie skryptów niestandardowych do tworzenia certyfikatów urzędu certyfikacji i urządzeń dla Azure IoT Hub
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
ms.openlocfilehash: ff4b63f49a87dd9ca6b0ef458bdcf1c285a34a18
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378214"
---
# <a name="tutorial-using-microsoft-supplied-scripts-to-create-test-certificates"></a>Samouczek: tworzenie certyfikatów testowych za pomocą skryptów dostarczonych przez firmę Microsoft

Firma Microsoft udostępnia skrypty programu PowerShell i powłoki Bash, które ułatwiają zrozumienie sposobu tworzenia własnych certyfikatów X.509 i uwierzytelniania ich w IoT Hub. Skrypty znajdują się w [repozytorium](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates)GitHub . Są one udostępniane wyłącznie w celach demonstracyjnych. Utworzone przez nie certyfikaty nie mogą być używane w środowisku produkcyjnym. Certyfikaty zawierają zakodowane hasła ("1234") i wygasają po upływie 30 dni. W przypadku środowiska produkcyjnego należy użyć własnych najlepszych rozwiązań w zakresie tworzenia certyfikatów i zarządzania okresem istnienia.

## <a name="powershell-scripts"></a>Skrypty środowiska PowerShell

### <a name="step-1---setup"></a>Krok 1. Konfiguracja

Pobierz program OpenSSL dla systemu Windows. Zobacz <https://www.openssl.org/docs/faq.html#MISC4> , aby znaleźć miejsca, w których można je pobrać lub <https://www.openssl.org/source/> skompilować ze źródła. Następnie uruchom skrypty wstępne:

1. Skopiuj skrypty z tego [repozytorium](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) GitHub do katalogu lokalnego, w którym chcesz pracować. Wszystkie pliki zostaną utworzone jako dzieci tego katalogu.

1. Uruchom program PowerShell jako administrator.

1. Zmień katalog na katalog, do którego załadowano skrypty.

1. W wierszu polecenia ustaw zmienną środowiskową na katalog, w którym znajduje się plik konfiguracji `$ENV:OPENSSL_CONF` openssl (openssl.cnf).

1. Uruchom `Set-ExecutionPolicy -ExecutionPolicy Unrestricted` polecenia , aby program PowerShell może uruchamiać skrypty.

1. Uruchom polecenie `. .\ca-certs.ps1`. Dzięki temu funkcje skryptu są dostępne w globalnej przestrzeni nazw programu PowerShell.

1. Uruchom polecenie `Test-CACertsPrerequisites`. Program PowerShell używa magazynu certyfikatów systemu Windows do zarządzania certyfikatami. To polecenie sprawdza, czy później nie będzie kolizji nazw z istniejącymi certyfikatami i czy program OpenSSL jest poprawnie skonfigurowany.

### <a name="step-2---create-certificates"></a>Krok 2. Tworzenie certyfikatów

Uruchom polecenie `New-CACertsCertChain [ecc|rsa]`. EcC jest zalecana dla certyfikatów urzędu certyfikacji, ale nie jest wymagana. Ten skrypt aktualizuje katalog i magazyn certyfikatów systemu Windows przy użyciu następujących certyfikatów urzędu certyfikacji i certyfikatów pośrednich:

* intermediate1.pem
* intermediate2.pem
* intermediate3.pem
* RootCA.cer
* RootCA.pem

Po uruchomieniu skryptu dodaj nowy certyfikat urzędu certyfikacji (RootCA.pem) do swojego IoT Hub:

1. Przejdź do swojego IoT Hub i przejdź do menu Certyfikaty.

1. Wybierz pozycję **Dodaj**.

1. Wprowadź nazwę wyświetlaną certyfikatu urzędu certyfikacji.

1. Przekaż certyfikat urzędu certyfikacji.

1. Wybierz pozycję **Zapisz**.

### <a name="step-3---prove-possession"></a>Krok 3. Udowodnienie posiadania

Teraz, po przesłaniu certyfikatu urzędu certyfikacji do IoT Hub, musisz udowodnić, że faktycznie jest on właścicielem:

1. Wybierz nowy certyfikat urzędu certyfikacji.

1. Wybierz **pozycję Generuj kod** weryfikacyjny w **oknie dialogowym Szczegóły** certyfikatu. Aby uzyskać więcej informacji, zobacz [Prove Possession of a CA certificate (Udowodnij posiadanie certyfikatu urzędu certyfikacji).](tutorial-x509-prove-possession.md)

1. Utwórz certyfikat zawierający kod weryfikacyjny. Jeśli na przykład kod weryfikacyjny to , uruchom następujące czynności, aby utworzyć nowy certyfikat w katalogu roboczy `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` zawierającym podmiot `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` . Skrypt tworzy certyfikat o nazwie `VerifyCert4.cer` .

    `New-CACertsVerificationCert "106A5SD242AF512B3498BD609C4941E66R34H268DDB3288"`

1. Przekaż `VerifyCert4.cer` plik do IoT Hub w **oknie dialogowym Szczegóły** certyfikatu.

1. Wybierz pozycję **Weryfikuj**.

### <a name="step-4---create-a-new-device"></a>Krok 4. Tworzenie nowego urządzenia

Utwórz urządzenie dla swojego IoT Hub:

1. W swoim IoT Hub przejdź do sekcji **Urządzenia IoT.**

1. Dodaj nowe urządzenie o identyfikatorze `mydevice` .

1. W przypadku uwierzytelniania wybierz **pozycję X.509 Ca Signed (Podpisany urząd certyfikacji X.509).**

1. Uruchom `New-CACertsDevice mydevice` , aby utworzyć nowy certyfikat urządzenia. Powoduje to utworzenie następujących plików w katalogu roboczy:

   * `mydevice.pfx`
   * `mydevice-all.pem`
   * `mydevice-private.pem`
   * `mydevice-public.pem`

### <a name="step-5---test-your-device-certificate"></a>Krok 5. Testowanie certyfikatu urządzenia

Przejdź do [testowania uwierzytelniania](tutorial-x509-test-certificate.md) certyfikatu, aby określić, czy certyfikat urządzenia może zostać uwierzytelniony w IoT Hub. Potrzebny będzie certyfikat w wersji `mydevice.pfx` PFX.

### <a name="step-6---cleanup"></a>Krok 6. Oczyszczanie

W menu Start otwórz pozycję **Zarządzaj certyfikatami** komputera i przejdź do pozycji Certyfikaty — **komputer > osobisty.** Usuń certyfikaty wystawione przez "TestOnly urzędu certyfikacji usługi Azure IoT*". Analogicznie usuń odpowiednie certyfikaty z>zaufanego głównego urzędu certyfikacji > certyfikatów i >certyfikatów pośrednich > **certyfikatów.**

## <a name="bash-scripts"></a>Skrypty powłoki Bash

### <a name="step-1---setup"></a>Krok 1. Konfiguracja

1. Uruchom powłokę Bash.

1. Zmień katalog na katalog, w którym chcesz pracować. Wszystkie pliki zostaną utworzone w tym katalogu.

1. Skopiuj `*.cnf` i do katalogu `*.sh` roboczego.

### <a name="step-2---create-certificates"></a>Krok 2. Tworzenie certyfikatów

1. Uruchom polecenie `./certGen.sh create_root_and_intermediate`. Powoduje to utworzenie następujących plików w katalogu **certs:**

    * azure-iot-test-only.chain.ca.cert.pem
    * azure-iot-test-only.intermediate.cert.pem
    * azure-iot-test-only.root.ca.cert.pem

1. Przejdź do swojego IoT Hub i przejdź do **menu Certyfikaty.**

1. Wybierz pozycję **Dodaj**.

1. Wprowadź nazwę wyświetlaną certyfikatu urzędu certyfikacji.

1. Przekaż tylko certyfikat urzędu certyfikacji do IoT Hub. Nazwa certyfikatu to `./certs/azure-iot-test-only.root.ca.cert.pem.`

1. Wybierz pozycję **Zapisz**.

### <a name="step-3---prove-possession"></a>Krok 3. Udowodnienie posiadania

1. Wybierz nowy certyfikat urzędu certyfikacji utworzony w poprzednim kroku.

1. Wybierz **pozycję Generuj** kod weryfikacyjny w **oknie dialogowym Szczegóły** certyfikatu. Aby uzyskać więcej informacji, zobacz [Prove Possession of a CA certificate (Udowodnij posiadanie certyfikatu urzędu certyfikacji).](tutorial-x509-prove-possession.md)

1. Utwórz certyfikat zawierający kod weryfikacyjny. Jeśli na przykład kod weryfikacyjny to , uruchom następujące czynności, aby utworzyć nowy certyfikat w katalogu pracy o nazwie , `"106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"` `verification-code.cert.pem` który zawiera podmiot `CN = 106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288` .

    `./certGen.sh create_verification_certificate "106A5SD242AF512B3498BD6098C4941E66R34H268DDB3288"`

1. Przekaż certyfikat do centrum IoT w **oknie dialogowym Szczegóły** certyfikatu.

1. Wybierz pozycję **Weryfikuj**.

### <a name="step-4---create-a-new-device"></a>Krok 4. Tworzenie nowego urządzenia

Utwórz urządzenie dla centrum IoT:

1. W swoim IoT Hub przejdź do sekcji Urządzenia IoT.

1. Dodaj nowe urządzenie o identyfikatorze `mydevice` .

1. W przypadku uwierzytelniania wybierz **pozycję X.509 Urząd certyfikacji ze podpisem**.

1. Uruchom `./certGen.sh create_device_certificate mydevice` , aby utworzyć nowy certyfikat urządzenia. Powoduje to utworzenie dwóch plików `new-device.cert.pem` o nazwie i w katalogu `new-device.cert.pfx` roboczy.

### <a name="step-5---test-your-device-certificate"></a>Krok 5. Testowanie certyfikatu urządzenia

Przejdź do [uwierzytelniania certyfikatu testowania,](tutorial-x509-test-certificate.md) aby określić, czy certyfikat urządzenia może zostać uwierzytelniony w IoT Hub. Potrzebny będzie certyfikat w wersji `new-device.cert.pfx` PFX.

### <a name="step-6---cleanup"></a>Krok 6. Czyszczenie

Ponieważ skrypt powłoki bash po prostu tworzy certyfikaty w katalogu pracy, po prostu usuń je po przeprowadzaniu testowania.

## <a name="next-steps"></a>Następne kroki

Aby przetestować certyfikat, przejdź do [testowania uwierzytelniania](tutorial-x509-test-certificate.md) certyfikatu, aby określić, czy certyfikat może uwierzytelnić urządzenie w IoT Hub.
