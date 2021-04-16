---
title: Samouczek — potwierdzanie własności certyfikatów urzędu certyfikacji w Azure IoT Hub | Microsoft Docs
description: Samouczek — potwierdzanie, że masz certyfikat urzędu certyfikacji dla Azure IoT Hub
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
ms.openlocfilehash: b7740fa1f6a54dcfcc1181dddedcdd5fdb50402c
ms.sourcegitcommit: 2654d8d7490720a05e5304bc9a7c2b41eb4ae007
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107378231"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Samouczek: potwierdzanie posiadania certyfikatu urzędu certyfikacji

Po przesłaniu certyfikatu głównego urzędu certyfikacji (CA) lub certyfikatu podrzędnego urzędu certyfikacji do centrum IoT Należy udowodnić, że jesteś właścicielem certyfikatu:

1. W witrynie Azure Portal do usługi IoTHub i wybierz pozycję **Ustawienia > certyfikatów.**

2. Wybierz **pozycję Dodaj,** aby dodać nowy certyfikat urzędu certyfikacji.

3. Wprowadź nazwę wyświetlaną w **polu Nazwa certyfikatu** i wybierz certyfikat PEM do dodania.

4. Wybierz pozycję **Zapisz**. Certyfikat jest wyświetlany na liście certyfikatów ze stanem **Niezweryfikowany.** Ten proces weryfikacji udowodni, że masz certyfikat.

5. Wybierz certyfikat, aby wyświetlić **okno dialogowe Szczegóły** certyfikatu.

6. W **oknie dialogowym wybierz** pozycję Generuj kod weryfikacyjny.

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Okno dialogowe Szczegóły certyfikatu}":::

7. Skopiuj kod weryfikacyjny do schowka. Należy ustawić kod weryfikacyjny jako podmiot certyfikatu. Jeśli na przykład kod weryfikacyjny to 75B86466DA34D2B04C0C4C4C9557A119687ADAE7D4732BDDB3, dodaj go jako temat certyfikatu, jak pokazano w następnym kroku.

8. Istnieją trzy sposoby generowania certyfikatu weryfikacji:

    * Jeśli używasz skryptu programu PowerShell dostarczonego przez firmę Microsoft, uruchom , aby `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` utworzyć certyfikat o nazwie `VerifyCert4.cer` . Aby uzyskać więcej informacji, zobacz [Using Microsoft-supplied Scripts (Używanie skryptów dostarczonych przez firmę Microsoft).](tutorial-x509-scripts.md)

    * Jeśli używasz skryptu powłoki Bash dostarczonego przez firmę Microsoft, uruchom , aby `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` utworzyć certyfikat o nazwie `verification-code.cert.pem` . Aby uzyskać więcej informacji, zobacz [Using Microsoft-supplied Scripts (Używanie skryptów dostarczonych przez firmę Microsoft).](tutorial-x509-scripts.md)

    * Jeśli używasz programu OpenSSL do generowania certyfikatów, musisz najpierw wygenerować klucz prywatny, a następnie żądanie podpisania certyfikatu (CSR):

      ```bash
      $ openssl genpkey -out pop.key -algorithm RSA -pkeyopt rsa_keygen_bits:2048

      $ openssl req -new -key pop.key -out pop.csr

      -----
      Country Name (2 letter code) [XX]:.
      State or Province Name (full name) []:.
      Locality Name (eg, city) [Default City]:.
      Organization Name (eg, company) [Default Company Ltd]:.
      Organizational Unit Name (eg, section) []:.
      Common Name (eg, your name or your server hostname) []:75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3
      Email Address []:

      Please enter the following 'extra' attributes
      to be sent with your certificate request
      A challenge password []:
      An optional company name []:
 
      ```

      Następnie utwórz certyfikat przy użyciu pliku konfiguracji głównego urzędu certyfikacji (pokazanego poniżej) lub pliku konfiguracji podrzędnego urzędu certyfikacji i pliku CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Aby uzyskać więcej informacji, zobacz [Using OpenSSL to Create Test Certificates (Tworzenie certyfikatów testowych przy użyciu programu OpenSSL).](tutorial-x509-openssl.md)

10. Wybierz nowy certyfikat w widoku **Szczegóły** certyfikatu.

11. Po przesłaniu certyfikatu wybierz pozycję **Weryfikuj**. Stan certyfikatu urzędu certyfikacji powinien zmienić się na **Zweryfikowano**.
