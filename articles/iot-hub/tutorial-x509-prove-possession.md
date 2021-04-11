---
title: Samouczek — udowodnić własność certyfikatów urzędu certyfikacji na platformie Azure IoT Hub | Microsoft Docs
description: Samouczek — udowadniasz, że masz certyfikat urzędu certyfikacji dla usługi Azure IoT Hub
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
ms.openlocfilehash: 0eb91754c3c70a7b477d456158454f707a874207
ms.sourcegitcommit: 32e0fedb80b5a5ed0d2336cea18c3ec3b5015ca1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/30/2021
ms.locfileid: "105630716"
---
# <a name="tutorial-proving-possession-of-a-ca-certificate"></a>Samouczek: dowód posiadania certyfikatu urzędu certyfikacji

Po przekazaniu certyfikatu głównego urzędu certyfikacji lub urzędu certyfikacji podrzędnego do centrum IoT należy udowodnić, że posiadasz certyfikat:

1. W Azure Portal przejdź do IoTHub i wybierz pozycję **ustawienia > certyfikaty**.

2. Wybierz pozycję **Dodaj** , aby dodać nowy certyfikat urzędu certyfikacji.

3. Wprowadź nazwę wyświetlaną w polu **Nazwa certyfikatu** i wybierz certyfikat PEM do dodania.

4. Wybierz pozycję **Zapisz**. Certyfikat zostanie wyświetlony na liście certyfikatów z **niezweryfikowanym** stanem. Ten proces weryfikacyjny będzie udowodnić, że posiadasz certyfikat.

5. Wybierz certyfikat, aby wyświetlić okno dialogowe **Szczegóły certyfikatu** .

6. W oknie dialogowym wybierz pozycję **Generuj kod weryfikacyjny** .

  :::image type="content" source="media/tutorial-x509-prove-possession/certificate-details.png" alt-text="{Okno dialogowe Szczegóły certyfikatu}":::

7. Skopiuj kod weryfikacyjny do schowka. Kod weryfikacyjny należy ustawić jako podmiot certyfikatu. Na przykład jeśli kod weryfikacyjny to 75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3, Dodaj go jako podmiot certyfikatu, jak pokazano w następnym kroku.

8. Istnieją trzy sposoby generowania certyfikatu weryfikacji:

    * Jeśli używasz skryptu programu PowerShell dostarczonego przez firmę Microsoft, uruchom polecenie, `New-CACertsVerificationCert "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` Aby utworzyć certyfikat o nazwie `VerifyCert4.cer` . Aby uzyskać więcej informacji, zobacz [Używanie skryptów dostarczonych przez firmę Microsoft](tutorial-x509-scripts.md).

    * Jeśli używasz skryptu bash dostarczonego przez firmę Microsoft, uruchom polecenie, `./certGen.sh create_verification_certificate "75B86466DA34D2B04C0C4C9557A119687ADAE7D4732BDDB3"` Aby utworzyć certyfikat o nazwie `verification-code.cert.pem` . Aby uzyskać więcej informacji, zobacz [Używanie skryptów dostarczonych przez firmę Microsoft](tutorial-x509-scripts.md).

    * Jeśli używasz OpenSSL do wygenerowania certyfikatów, musisz najpierw wygenerować klucz prywatny i żądanie podpisania certyfikatu (CSR):

      ```bash
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

      Następnie Utwórz certyfikat przy użyciu pliku konfiguracyjnego głównego urzędu certyfikacji (pokazanego poniżej) lub pliku konfiguracji podrzędnego urzędu certyfikacji i CSR.

      ```bash
      openssl ca -config rootca.conf -in pop.csr -out pop.crt -extensions client_ext

      ```

    Aby uzyskać więcej informacji, zobacz [using OpenSSL to Create test Certificates](tutorial-x509-openssl.md).

10. Wybierz nowy certyfikat w widoku **Szczegóły certyfikatu** .

11. Po przeładowaniu certyfikatu wybierz pozycję **Weryfikuj**. Stan certyfikatu urzędu certyfikacji powinien zostać zmieniony na **zweryfikowane**.
