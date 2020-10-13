---
title: Weryfikowanie certyfikatów dla urzędu certyfikacji X. 509 za pomocą usługi Azure IoT Hub Device Provisioning Service
description: Jak przeprowadzić potwierdzenie dla certyfikatów urzędu certyfikacji X. 509 za pomocą usługi Azure IoT Hub Device Provisioning Service (DPS)
author: wesmc7777
ms.author: wesmc
ms.date: 02/26/2018
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
ms.openlocfilehash: 50e8dfd2998b50bfff7341e49ac4d0770c115166
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/09/2020
ms.locfileid: "90530866"
---
# <a name="how-to-do-proof-of-possession-for-x509-ca-certificates-with-your-device-provisioning-service"></a>Jak przeprowadzić potwierdzenie dla certyfikatów urzędu certyfikacji X. 509 za pomocą usługi Device Provisioning

Zweryfikowany certyfikat urzędu certyfikacji X. 509 jest certyfikatem urzędu certyfikacji, który został przekazany i zarejestrowany w usłudze aprowizacji i został przeszedł przez dowód posiadania w usłudze. 

Dowód posiadania obejmuje następujące kroki:
1. Uzyskaj unikatowy kod weryfikacyjny wygenerowany przez usługę aprowizacji dla certyfikatu urzędu certyfikacji X. 509. Możesz to zrobić w witrynie Azure Portal.
2. Utwórz certyfikat weryfikacji X. 509 z kodem weryfikacyjnym jako podmiot i podpisz certyfikat z kluczem prywatnym skojarzonym z certyfikatem urzędu certyfikacji X. 509.
3. Przekaż podpisany certyfikat weryfikacji do usługi. Usługa sprawdza poprawność certyfikatu weryfikacji przy użyciu publicznej części certyfikatu urzędu certyfikacji do zweryfikowania, co oznacza, że dysponujesz kluczem prywatnym certyfikatu urzędu certyfikacji.

Zweryfikowane certyfikaty odgrywają ważną rolę podczas korzystania z grup rejestracji. Sprawdzenie, czy własność certyfikatu zapewnia dodatkową warstwę zabezpieczeń, upewniając się, że obiektu przekazującego certyfikatu jest posiadania klucza prywatnego certyfikatu. Weryfikacja uniemożliwia złośliwemu aktorowi wykrywanie ruchu przed wyodrębnieniem certyfikatu pośredniego i użycie tego certyfikatu do utworzenia grupy rejestracji w ramach własnej usługi aprowizacji, skutecznie przejmowanie urządzeń. Przez udowodnienie własności certyfikatu głównego lub pośredniego w łańcuchu certyfikatów potwierdzasz, że masz uprawnienia do generowania certyfikatów liści dla urządzeń, które będą się rejestrować w ramach tej grupy rejestracji. Z tego powodu certyfikat główny lub pośredni skonfigurowany w grupie rejestracji musi być zweryfikowanym certyfikatem lub musi zostać wycofany do zweryfikowanego certyfikatu w łańcuchu certyfikatów, gdy urządzenie jest wyświetlane podczas uwierzytelniania w usłudze. Aby dowiedzieć się więcej o zaświadczeniu certyfikatu X. 509, zobacz artykuł [x. 509 certyfikaty](concepts-x509-attestation.md) i [Kontrola dostępu urządzenia do usługi aprowizacji za pomocą certyfikatów x. 509](concepts-x509-attestation.md#controlling-device-access-to-the-provisioning-service-with-x509-certificates).

## <a name="register-the-public-part-of-an-x509-certificate-and-get-a-verification-code"></a>Rejestrowanie publicznej części certyfikatu X. 509 i uzyskiwanie kodu weryfikacyjnego

Aby zarejestrować certyfikat urzędu certyfikacji przy użyciu usługi aprowizacji i uzyskać kod weryfikacyjny, którego można użyć podczas weryfikacji, wykonaj następujące kroki. 

1. W Azure Portal przejdź do usługi aprowizacji, a następnie otwórz pozycję **Certyfikaty** w menu po lewej stronie. 
2. Kliknij przycisk **Dodaj** , aby dodać nowy certyfikat.
3. Wprowadź przyjazną nazwę wyświetlaną dla certyfikatu. Przejdź do pliku cer lub PEM, który reprezentuje publiczną część certyfikatu X. 509. Kliknij pozycję **Przekaż**.
4. Po otrzymaniu powiadomienia o pomyślnym przekazaniu certyfikatu kliknij przycisk **Zapisz**.

    ![Przekazywanie certyfikatu](./media/how-to-verify-certificates/add-new-cert.png)  

   Certyfikat zostanie wyświetlony na liście **Eksplorator certyfikatów** . Należy pamiętać, że nie *zweryfikowano* **stanu** tego certyfikatu.

5. Kliknij certyfikat dodany w poprzednim kroku.

6. W obszarze **Szczegóły certyfikatu**kliknij przycisk **Generuj kod weryfikacyjny**.

7. Usługa aprowizacji tworzy **kod weryfikacyjny** , którego można użyć do zweryfikowania własności certyfikatu. Skopiuj kod do Schowka. 

   ![Weryfikuj certyfikat](./media/how-to-verify-certificates/verify-cert.png)  

## <a name="digitally-sign-the-verification-code-to-create-a-verification-certificate"></a>Cyfrowe podpisywanie kodu weryfikacyjnego w celu utworzenia certyfikatu weryfikacji

Teraz musisz podpisać *kod weryfikacyjny* przy użyciu klucza prywatnego skojarzonego z certyfikatem urzędu certyfikacji X. 509, który generuje sygnaturę. Jest to tzw. [potwierdzenie posiadania](https://tools.ietf.org/html/rfc5280#section-3.1) i wyniki w podpisanym certyfikacie weryfikacji.

Firma Microsoft udostępnia narzędzia i przykłady, które mogą pomóc utworzyć podpisany certyfikat weryfikacji: 

- **Zestaw Azure IoT Hub C SDK** udostępnia skrypty programu PowerShell (Windows) i bash (Linux), które ułatwiają tworzenie certyfikatów urzędu certyfikacji i liści na potrzeby opracowywania i wykonywanie dowodu posiadania przy użyciu kodu weryfikacyjnego. [Pliki](https://github.com/Azure/azure-iot-sdk-c/tree/master/tools/CACertificates) odpowiednie dla danego systemu można pobrać do folderu roboczego i postępować zgodnie z instrukcjami w [pliku Readme zarządzania certyfikatami urzędu certyfikacji](https://github.com/Azure/azure-iot-sdk-c/blob/master/tools/CACertificates/CACertificateOverview.md) w celu przeprowadzenia dowodu posiadania certyfikatu urzędu certyfikacji. 
- **Zestaw SDK platformy Azure IoT Hub C#** zawiera [przykład weryfikacji certyfikatu grupy](https://github.com/Azure-Samples/azure-iot-samples-csharp/tree/master/provisioning/Samples/service/GroupCertificateVerificationSample), którego można użyć do potwierdzenia posiadania.
 
> [!IMPORTANT]
> Oprócz wykonywania dowodu istnienia, skrypty programu PowerShell i bash, które zostały wcześniej, również umożliwiają tworzenie certyfikatów głównych, certyfikatów pośrednich i certyfikatów liści, których można użyć do uwierzytelniania i aprowizacji urządzeń. Te certyfikaty powinny być używane tylko do celów deweloperskich. Nie powinny być używane w środowisku produkcyjnym. 

Skrypty programu PowerShell i bash dostępne w dokumentacji i zestawach SDK są zależne od [OpenSSL](https://www.openssl.org/). Możesz również użyć OpenSSL lub innych narzędzi innych firm, aby ułatwić Ci dowód posiadania. Aby uzyskać więcej informacji na temat narzędzi dostarczanych z zestawami SDK, zobacz [jak korzystać z narzędzi dostępnych w](how-to-use-sdk-tools.md)zestawach SDK. 


## <a name="upload-the-signed-verification-certificate"></a>Przekaż podpisany certyfikat weryfikacji

1. Przekaż otrzymany podpis jako certyfikat weryfikacji do usługi aprowizacji w portalu. W obszarze **Szczegóły certyfikatu** na Azure Portal Użyj ikony  _Eksploratora plików_ obok pola **plik PEM lub CER certyfikatu weryfikacji** , aby przekazać podpisany certyfikat weryfikacyjny z systemu.

2. Po pomyślnym przekazaniu certyfikatu kliknij przycisk **Weryfikuj**. **Stan** certyfikatu zmieni się na **_zweryfikowany_** na liście **Eksplorator certyfikatów** . Kliknij przycisk **Odśwież** , jeśli nie zostanie automatycznie zaktualizowany.

   ![Przekaż weryfikację certyfikatu](./media/how-to-verify-certificates/upload-cert-verification.png)  

## <a name="next-steps"></a>Następne kroki

- Aby dowiedzieć się, jak utworzyć grupę rejestracji przy użyciu portalu, zobacz [Zarządzanie rejestracjami urządzeń za pomocą Azure Portal](how-to-manage-enrollments.md).
- Aby dowiedzieć się, jak używać zestawów SDK usługi do tworzenia grupy rejestracji, zobacz [Zarządzanie rejestracjami urządzeń za pomocą zestawów SDK usług](how-to-manage-enrollments-sdks.md).










