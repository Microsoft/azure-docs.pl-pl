---
title: Certyfikowanie urządzenia IoT Plug and Play w wersji zapoznawczej | Microsoft Docs
description: W tym samouczku opisano, jak dodać informacje o produkcie do wykazu urządzeń z certyfikatem platformy Azure dla IoT, podłączyć urządzenie do usługi certyfikacji IoT Azure, a następnie uruchomić testy certyfikacji Plug and Play IoT.
manager: philmea
ms.service: iot-pnp
services: iot-pnp
ms.topic: tutorial
ms.author: koichih
author: konichi3
ms.date: 12/27/2019
ms.openlocfilehash: dc2c33659f3f3a3df0f11fcc6ab36a9fc993da43
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/29/2020
ms.locfileid: "75550166"
---
# <a name="tutorial-certify-your-iot-plug-and-play-preview-device"></a>Samouczek: certyfikowanie urządzenia IoT Plug and Play Preview

Aby opublikować urządzenie usługi IoT Plug and Play w wersji zapoznawczej w [wykazie urządzeń z certyfikatem platformy Azure dla IoT](https://aka.ms/iotdevcat), należy przekazać zestaw testów certyfikacji. Skorzystaj z portalu [Azure Certified for IoT](https://aka.ms/ACFI) , aby przesłać urządzenie do certyfikacji. [Usługa certyfikacji IoT Azure](https://aka.ms/azure-iot-aics) uruchamia testy certyfikacji.

W ramach tego samouczka nauczysz się:

> [!div class="checklist"]
> * Jakie są wymagania certyfikacyjne Plug and Play IoT.
> * Jak dodać swoją nazwę i informacje o produkcie do portalu.
> * Jak łączyć i odnajdować interfejsy Plug and Play IoT.
> * Jak przeglądać interfejsy Plug and Play IoT i uruchamiać testy certyfikacji.
> * Jak opublikować certyfikowane urządzenie Plug and Play IoT w wykazie.

## <a name="prerequisites"></a>Wymagania wstępne

Do ukończenia tego samouczka niezbędne są następujące elementy:

* Konto Centrum partnerskiego firmy Microsoft.
* Identyfikator Microsoft Partner Network.

Aby uzyskać więcej informacji, zobacz [jak dołączyć do portalu Azure Certified for IoT](howto-onboard-portal.md) .

## <a name="certification-requirements"></a>Wymagania dotyczące certyfikacji

Aby przeprowadzić certyfikację urządzenia IoT Plug and Play, urządzenie musi spełniać następujące wymagania:

* Na urządzeniu musi być zainstalowany kod urządzenia Plug and Play IoT.
* Kod urządzenia IoT Plug and Play został utworzony przy użyciu zestawu Azure IoT SDK.
* Kod urządzenia musi obsługiwać [IoT Hub Device Provisioning Service platformy Azure](../iot-dps/about-iot-dps.md).
* Kod urządzenia musi implementować [interfejs informacji o urządzeniu](concepts-common-interfaces.md).
* Model możliwości i kod urządzenia pracują z IoT Central.

Wszystkie urządzenia znajdujące się obecnie w katalogu są traktowane jako wstępnie certyfikowane urządzenia Plug and Play IoT. Nie gwarantuje to jakości i zgodności końcowego produktu IoT Plug and Play składnikami oprogramowania, takimi jak zestaw SDK i cyfrowy język definicji sznurka.

Wszystkie wstępnie certyfikowane urządzenia Plug and Play IoT muszą zaświadczyć ogólną dostępność Plug and Play IoT w oparciu o ostateczną wersję wymagań certyfikacji oraz składniki oprogramowania udostępniane przez firmę Microsoft.

## <a name="add-product-name"></a>Dodaj nazwę produktu

**Produkt** jest przyjazną nazwą modelu produktu, który klient może zakupić. Aby dodać produkt:

1. Zaloguj się do portalu [Azure Certified for IoT](https://aka.ms/ACFI) .
1. Przejdź do strony **produkty** w portalu w menu po lewej stronie i wybierz pozycję **+ Nowy**.
1. Wprowadź przyjazną nazwę produktu i wybierz pozycję **Utwórz**. Wprowadzona nazwa jest inna niż nazwa wyświetlana w katalogu urządzeń.

## <a name="add-product-information"></a>Dodawanie informacji o produkcie

Po pomyślnym utworzeniu produktu w portalu zostanie on wyświetlony na stronie **produkty** . Aby dodać informacje o produkcie:

1. Wybierz link utworzony produkt znaleziony na stronie **produkt** w kolumnie produkt. Stan powinien być w wersji roboczej.
1. Wybierz link **Edytuj** obok nagłówka **Informacje o produkcie** . Wprowadź informacje o produkcie na stronie informacje o produkcie i upewnij się, że wszystkie wymagane pola zostały wykonane.
1. Wybierz pozycję **Zapisz**. Przycisk **Zapisz** pojawia się tylko po zakończeniu wszystkich wymaganych pól. Jeśli pola są niekompletne, przycisk mówi, że **Zapisz i zakończy później**.
1. Przejrzyj wprowadzoną zawartość. Wypełnij wszystkie wymagane pola, aby opublikować urządzenie w katalogu urządzeń. Zawsze możesz wrócić do wprowadzania zmian informacji o produkcie na stronie szczegółów produktu, klikając link **Edytuj** obok nagłówka **Informacje o produkcie** .

## <a name="connect-and-discover-interfaces"></a>Łączenie i odnajdowanie interfejsów

Aby uruchomić testy certyfikacji, Połącz urządzenie z [usługą certyfikacji Azure IoT](https://aka.ms/azure-iot-aics) (AICS) dostępną w portalu.

Te kroki to jeden krok czasu na potrzeby uruchamiania testów certyfikacji i nie trzeba zmieniać kodu urządzenia produktu. Aby rozpocząć, wykonaj następujące kroki, aby nawiązać połączenie z usługą AICS:

1. Zaloguj się do portalu przy użyciu konta Centrum partnerskiego.
1. Kliknij pozycję **Połącz i Testuj** , aby uruchomić przepływ certyfikacji.
1. Wybierz [metodę uwierzytelniania](../iot-dps/concepts-security.md#attestation-mechanism) , aby udostępnić urządzenie do AICS przy użyciu [IoT Hub Device Provisioning Service platformy Azure](../iot-dps/about-iot-dps.md).
   * Jeśli używasz [certyfikatu x. 509](../iot-hub/iot-hub-security-x509-get-started.md#prerequisites), Przekaż wygenerowany certyfikat x. 509. Warto zapoznać się z przykładowym kodem, który pokazuje, jak używać certyfikatów X. 509: [C](https://github.com/Azure/azure-iot-sdk-c/blob/master/iothub_client/samples/iothub_ll_client_x509_sample/iothub_ll_client_x509_sample.c), [C#](../iot-hub/iot-hub-security-x509-get-started.md).
   * Jeśli używasz [klucza symetrycznego](../iot-dps/concepts-symmetric-key-attestation.md), skopiuj i Wklej klucz symetryczny do kodu urządzenia.
   * Metoda uwierzytelniania modułu TPM nie jest obecnie obsługiwana.
1. Skopiuj następujące wygenerowane identyfikatory i wklej je do swojego kodu urządzenia.
   * [Identyfikator rejestracji](../iot-dps/use-hsm-with-sdk.md)
   * [IDENTYFIKATOR DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
   * [Punkt końcowy DPS](../iot-dps/tutorial-set-up-device.md#create-the-device-registration-software)
1. Gdy kod urządzenia zostanie skompilowany i wdrożony na urządzeniu, wybierz pozycję **Połącz** , aby odnaleźć interfejsy Plug and Play IoT.
1. Po pomyślnym nawiązaniu połączenia z usługą AICS wybierz pozycję **dalej** , aby przejrzeć odnalezione interfejsy Plug and Play IoT.

## <a name="run-tests-and-publish-the-device"></a>Uruchom testy i Opublikuj urządzenie

Na stronie Przegląd możesz przejrzeć odnalezione interfejsy Plug and Play IoT. Ta strona służy do sprawdzania, czy elementy pierwotne zaimplementowane w interfejsie są wyświetlane poprawnie. Możesz również sprawdzić lokalizację interfejsu.

1. Upewnij się, że dane wejściowe ładunku są wprowadzane dla wymaganych pól. Te pola zawierają informacje o ładunku dla polecenia podstawowego dla określonego interfejsu.
1. Po wprowadzeniu wszystkich wymaganych informacji wybierz pozycję **dalej**.
1. Aby uruchomić testy dla zaimplementowanych interfejsów Plug and Play IoT, wybierz pozycję **Uruchom testy**.
1. Wszystkie testy są uruchamiane automatycznie. Jeśli dowolny test zakończy się niepowodzeniem, wybierz opcję **Wyświetl dzienniki** , aby wyświetlić komunikaty o błędach z AICS i nieprzetworzoną telemetrię wysłaną do IoT Hub platformy Azure
1. Aby zakończyć testy certyfikacji, wybierz pozycję **Zakończ**.
1. Opublikuj certyfikowane urządzenie Plug and Play IoT w wykazie. Aby dodać certyfikowane urządzenie do wykazu, wybierz pozycję **Dodaj do wykazu** na pasku narzędzi. Jeśli element **Dodaj do wykazu** jest wyszarzony, oznacza to, że informacje o produkcie są niekompletne lub testy nie powiodły się. 
1. Wybierz link "CERTYFIKOWANe i w WYKAZie", aby wyświetlić opublikowane urządzenie w katalogu urządzeń.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zatwierdzić urządzenie Plug and Play IoT, sugerowanym następnym krokiem jest zapoznanie się z informacjami na temat zarządzania modelami możliwości:

> [!div class="nextstepaction"]
> [Zarządzanie modelami](./howto-manage-models.md)
