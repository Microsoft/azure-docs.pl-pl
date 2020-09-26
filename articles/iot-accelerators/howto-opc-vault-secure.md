---
title: Zabezpieczanie komunikacji urządzeń OPC UA z magazynem OPC — Azure | Microsoft Docs
description: Jak zarejestrować aplikacje OPC UA oraz jak wystawiać podpisane certyfikaty aplikacji dla urządzeń OPC UA z magazynem OPC.
author: mregen
ms.author: mregen
ms.date: 8/16/2018
ms.topic: conceptual
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: e95edf21b7d6dce29b31220533269439fac120e4
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/25/2020
ms.locfileid: "91281962"
---
# <a name="use-the-opc-vault-certificate-management-service"></a>Korzystanie z usługi zarządzania certyfikatami magazynu OPC

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

W tym artykule wyjaśniono, jak zarejestrować aplikacje i jak wystawiać podpisane certyfikaty aplikacji dla urządzeń z OPC UA.

## <a name="prerequisites"></a>Wymagania wstępne

### <a name="deploy-the-certificate-management-service"></a>Wdrażanie usługi zarządzania certyfikatami

Najpierw Wdróż usługę w chmurze platformy Azure. Aby uzyskać szczegółowe informacje, zobacz [wdrażanie usługi zarządzania certyfikatami magazynu OPC](howto-opc-vault-deploy.md).

### <a name="create-the-issuer-ca-certificate"></a>Tworzenie certyfikatu urzędu certyfikacji wystawcy

Jeśli jeszcze tego nie zrobiono, Utwórz certyfikat urzędu certyfikacji wystawcy. Aby uzyskać szczegółowe informacje, zobacz [Tworzenie certyfikatu wystawcy i zarządzanie nim dla magazynu OPC](howto-opc-vault-manage.md).

## <a name="secure-opc-ua-applications"></a>Bezpieczne aplikacje OPC UA

### <a name="step-1-register-your-opc-ua-application"></a>Krok 1. rejestrowanie aplikacji OPC UA 

> [!IMPORTANT]
> Rola składnika zapisywania jest wymagana do zarejestrowania aplikacji.

1. Otwórz usługę certyfikatów pod adresem `https://myResourceGroup-app.azurewebsites.net` i zaloguj się.
2. Przejdź do pozycji **Zarejestruj nowe**. W przypadku rejestracji aplikacji użytkownik musi mieć przypisaną co najmniej rolę składnika zapisywania.
2. Formularz wprowadzania jest zgodny z konwencjami nazewnictwa w OPC UA. Na przykład na poniższym zrzucie ekranu przedstawiono ustawienia przykładowego [serwera referencyjnego OPC UA](https://github.com/OPCFoundation/UA-.NETStandard/tree/master/Applications/ReferenceServer) na STOSIE OPC UA .NET standard:

   ![Zrzut ekranu rejestracji serwera odwołań UA](media/howto-opc-vault-secure/reference-server-registration.png "Rejestracja serwera odwołań UA")

5. Wybierz pozycję **zarejestruj** , aby zarejestrować aplikację w bazie danych aplikacji usługi certyfikatów. Przepływ pracy bezpośrednio prowadzi użytkownika do następnego kroku, aby zażądać certyfikatu podpisanego dla aplikacji.

### <a name="step-2-secure-your-application-with-a-ca-signed-application-certificate"></a>Krok 2. Zabezpieczanie aplikacji za pomocą certyfikatu podpisanej aplikacji urzędu certyfikacji

Zabezpiecz swoją aplikację OPC UA, wydając podpisany certyfikat na podstawie żądania podpisania certyfikatu (CSR). Alternatywnie można zażądać nowej pary kluczy, która obejmuje nowy klucz prywatny w formacie PFX lub PEM. Aby uzyskać informacje o tym, która metoda jest obsługiwana dla aplikacji, zobacz dokumentację urządzenia OPC UA. Ogólnie rzecz biorąc, zalecane jest użycie metody CSR, ponieważ nie wymaga ona transferu klucza prywatnego za pośrednictwem sieci przewodowej.

#### <a name="request-a-new-certificate-with-a-new-keypair"></a>Zażądaj nowego certyfikatu przy użyciu nowej pary kluczy

1. Przejdź do pozycji **aplikacje**.
3. Wybierz pozycję **nowe żądanie** dla wymienionej aplikacji.

   ![Zrzut ekranu żądania nowego certyfikatu](media/howto-opc-vault-secure/request-new-certificate.png "Żądaj nowego certyfikatu")

3. Wybierz pozycję **Żądaj nowej pary kluczy i certyfikatu** , aby zażądać klucza prywatnego i nowego podpisanego certyfikatu z kluczem publicznym aplikacji.

   ![Zrzut ekranu przedstawiający generowanie nowej pary kluczy i certyfikatu](media/howto-opc-vault-secure/generate-new-key-pair.png "Generuj nową parę kluczy")

4. Wypełnij formularz nazwą podmiotu i domeną. Dla klucza prywatnego wybierz PEM lub PFX z hasłem. Wybierz pozycję **Generuj nową parę kluczy** , aby utworzyć żądanie certyfikatu.

   ![Zrzut ekranu przedstawiający szczegóły żądania wyświetlenia certyfikatu](media/howto-opc-vault-secure/approve-reject.png "Zatwierdź certyfikat")

5. Zatwierdzenie wymaga użytkownika z rolą osoby zatwierdzającej i z uprawnieniami do podpisywania w Azure Key Vault. W typowym przepływie pracy, role osoby zatwierdzającej i żądającej powinny być przypisane do różnych użytkowników. Wybierz pozycję **Zatwierdź** lub **Odrzuć** , aby uruchomić lub anulować rzeczywiste Tworzenie pary kluczy i operacji podpisywania. Nowa para kluczy jest tworzona i bezpiecznie przechowywana w Azure Key Vault, dopóki nie zostanie pobrana przez zleceniodawcę certyfikatu. Certyfikat wynikający z kluczem publicznym jest podpisany przez urząd certyfikacji. Wykonywanie tych operacji może potrwać kilka sekund.

   ![Zrzut ekranu przedstawiający wyświetlanie szczegółów żądania certyfikatu z komunikatem o zatwierdzeniu u dołu](media/howto-opc-vault-secure/view-key-pair.png "Wyświetl parę kluczy")

7. Klucz prywatny (PFX lub PEM) i certyfikat (DER) można pobrać z tego miejsca w formacie wybranym jako plik binarny do pobrania. Dostępna jest również wersja zakodowana algorytmem Base64, na przykład w celu kopiowania i wklejania certyfikatu do wiersza polecenia lub wprowadzania tekstu. 
8. Po pobraniu i zapisaniu klucza prywatnego można wybrać pozycję **Usuń klucz prywatny**. Certyfikat z kluczem publicznym pozostaje dostępny do użytku w przyszłości.
9. Ze względu na użycie certyfikatu podpisanego przez urząd certyfikacji należy również pobrać certyfikat urzędu certyfikacji i listę odwołania certyfikatów (CRL).

Teraz zależy to od urządzenia OPC UA, jak zastosować nową parę kluczy. Zazwyczaj certyfikat urzędu certyfikacji i listy CRL są kopiowane do `trusted` folderu, podczas gdy klucze publiczny i prywatny certyfikatu aplikacji są stosowane do `own` folderu w magazynie certyfikatów. Niektóre urządzenia mogą już obsługiwać wypychanie serwera na potrzeby aktualizacji certyfikatów. Zapoznaj się z dokumentacją urządzenia OPC UA.

#### <a name="request-a-new-certificate-with-a-csr"></a>Zażądaj nowego certyfikatu za pomocą CSR 

1. Przejdź do pozycji **aplikacje**.
3. Wybierz pozycję **nowe żądanie** dla wymienionej aplikacji.

   ![Zrzut ekranu żądania nowego certyfikatu](media/howto-opc-vault-secure/request-new-certificate.png "Żądaj nowego certyfikatu")

3. Wybierz pozycję **Żądaj nowego certyfikatu przy użyciu żądania podpisania** , aby zażądać nowego certyfikatu podpisanego dla aplikacji.

   ![Zrzut ekranu przedstawiający generowanie nowego certyfikatu](media/howto-opc-vault-secure/generate-new-certificate.png "Generuj nowy certyfikat")

4. Przekaż CSR, wybierając plik lokalny lub wklejając w postaci zakodowany w formacie base64 CSR. Wybierz pozycję **Generuj nowy certyfikat**.

   ![Zrzut ekranu przedstawiający szczegóły żądania wyświetlenia certyfikatu](media/howto-opc-vault-secure/approve-reject-csr.png "Zatwierdź CSR")

5. Zatwierdzenie wymaga użytkownika z rolą osoby zatwierdzającej i z uprawnieniami do podpisywania w Azure Key Vault. Wybierz pozycję **Zatwierdź** lub **Odrzuć** , aby uruchomić lub anulować rzeczywistą operację podpisywania. Certyfikat wynikający z kluczem publicznym jest podpisany przez urząd certyfikacji. Wykonanie tej operacji może potrwać kilka sekund.

   ![Zrzut ekranu przedstawiający wyświetlanie szczegółów żądania certyfikatu z komunikatem o zatwierdzeniu u dołu](media/howto-opc-vault-secure/view-cert-csr.png "Wyświetl certyfikat")

6. Certyfikat wynikający (DER) można pobrać z tego miejsca jako plik binarny. Dostępna jest również wersja zakodowana algorytmem Base64, na przykład w celu kopiowania i wklejania certyfikatu do wiersza polecenia lub wprowadzania tekstu. 
10. Po pobraniu i zapisaniu certyfikatu można wybrać pozycję **Usuń certyfikat**.
11. Ze względu na użycie certyfikatu podpisanego przez urząd certyfikacji należy również pobrać certyfikat urzędu certyfikacji i listę CRL.

Teraz zależy to od urządzenia OPC UA, jak zastosować nowy certyfikat. Zwykle certyfikat urzędu certyfikacji i listy CRL są kopiowane do `trusted` folderu, podczas gdy certyfikat aplikacji jest stosowany do `own` folderu w magazynie certyfikatów. Niektóre urządzenia mogą już obsługiwać wypychanie serwera na potrzeby aktualizacji certyfikatów. Zapoznaj się z dokumentacją urządzenia OPC UA.

### <a name="step-3-device-secured"></a>Krok 3. Urządzenie zabezpieczone

Urządzenie OPC UA jest teraz gotowe do komunikowania się z innymi urządzeniami OPC UA zabezpieczonymi przez certyfikaty podpisane przez urząd certyfikacji, bez konieczności dalszej konfiguracji.

## <a name="next-steps"></a>Następne kroki

Teraz, gdy wiesz już, jak zabezpieczyć urządzenia OPC UA, możesz:

> [!div class="nextstepaction"]
> [Uruchom bezpieczną usługę zarządzania certyfikatami](howto-opc-vault-secure-ca.md)
