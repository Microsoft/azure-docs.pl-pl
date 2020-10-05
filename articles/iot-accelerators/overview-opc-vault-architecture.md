---
title: Architektura magazynu OPC — platforma Azure | Microsoft Docs
description: Architektura usługi zarządzania certyfikatami magazynu OPC
author: mregen
ms.author: mregen
ms.date: 08/16/2019
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: eb558d967ad657d14158684fba92b13979ea5fe2
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91281592"
---
# <a name="opc-vault-architecture"></a>Architektura magazynu OPC

> [!IMPORTANT]
> Gdy aktualizujemy ten artykuł, zobacz [Azure Industrial IoT](https://azure.github.io/Industrial-IoT/) , aby uzyskać najbardziej aktualną zawartość.

Ten artykuł zawiera omówienie mikrousługi magazynu OPC oraz magazynu OPC IoT Edge.

Aplikacje OPC UA używają certyfikatów wystąpień aplikacji, aby zapewnić zabezpieczenia na poziomie aplikacji. Bezpieczne połączenie jest nawiązywane za pomocą kryptografii asymetrycznej, dla którego certyfikaty aplikacji zapewniają parę kluczy publicznych i prywatnych. Certyfikaty mogą być podpisane z podpisem własnym lub podpisane przez urząd certyfikacji (CA).

Aplikacja OPC UA ma listę zaufanych certyfikatów reprezentujących aplikacje, które ufa. Te certyfikaty mogą być podpisane lub podpisane przez urząd certyfikacji lub mogą być urzędem certyfikacji lub urzędem certyfikacji. Jeśli zaufany certyfikat jest częścią większego łańcucha certyfikatów, aplikacja ufa wszystkim certyfikatom, które są powiązane z certyfikatem na liście zaufania. Jest to prawdziwe, o ile pełny łańcuch certyfikatów może być zweryfikowany.

Główną różnicą między zaufanymi certyfikatami z podpisem własnym i zaufaniem do certyfikatu urzędu certyfikacji jest nakład pracy instalacji wymagany do wdrożenia i utrzymania zaufania. Istnieje również dodatkowy nakład pracy w celu hostowania urzędu certyfikacji specyficznego dla firmy. 

Aby dystrybuować zaufania dla certyfikatów z podpisem własnym dla wielu serwerów z jedną aplikacją kliencką, należy zainstalować wszystkie certyfikaty aplikacji serwera na liście zaufania aplikacji klienta. Ponadto należy zainstalować certyfikat aplikacji klienckiej na wszystkich listach zaufania aplikacji serwera. Ta praca administracyjna jest w pełni uciążliwa i nawet rośnie, gdy konieczne jest uwzględnienie okresów ważności certyfikatów i odnowienie certyfikatów.

Korzystanie z urzędu certyfikacji specyficznego dla firmy może znacznie uprościć zarządzanie zaufaniem z wieloma serwerami i klientami. W takim przypadku administrator generuje certyfikat wystąpienia aplikacji podpisanej przez urząd certyfikacji jeden raz dla każdego używanego klienta i serwera. Ponadto certyfikat urzędu certyfikacji jest instalowany na każdej liście zaufania aplikacji na wszystkich serwerach i klientach. W tym podejściu tylko wygasłe certyfikaty muszą zostać odnowione i zastąpione dla aplikacji, których to dotyczy.

Usługa zarządzania certyfikatami w usłudze Azure Industrial IoT OPC UA ułatwia zarządzanie urzędem certyfikacji specyficznym dla firmy dla aplikacji OPC UA. Ta usługa jest oparta na mikrousługach magazynu OPC. Magazyn OPC udostępnia mikrousługi do hostowania urzędu certyfikacji specyficznego dla danej firmy w bezpiecznej chmurze. To rozwiązanie jest obsługiwane przez usługi zabezpieczone przez usługę Azure Active Directory (Azure AD), Azure Key Vault z sprzętowymi modułami zabezpieczeń (sprzętowych modułów zabezpieczeń), Azure Cosmos DB i opcjonalnie IoT Hub jako sklepu z aplikacjami.

Mikrousługa magazynu OPC została zaprojektowana z myślą o obsłudze przepływu pracy opartego na rolach, gdy administratorzy zabezpieczeń i osoby zatwierdzające mające uprawnienia do podpisywania w Azure Key Vault zatwierdzania lub odrzucania żądań.

Aby zapewnić zgodność z istniejącymi rozwiązaniami OPC UA, usługi obejmują obsługę modułu Edge kopii zapasowej mikrousług magazynu OPC. Spowoduje to zaimplementowanie **globalnego serwera wykrywania OPC UA i interfejsu zarządzania certyfikatami** , aby dystrybuować certyfikaty i listy zaufania zgodnie z częścią 12 specyfikacji. 


## <a name="architecture"></a>Architektura

Architektura jest oparta na mikrousługach magazynu OPC z magazynem OPC, IoT Edge module dla sieci fabryki i przykładowym interfejsem użytkownika sieci Web do sterowania przepływem pracy:

![Diagram architektury magazynu OPC](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="opc-vault-microservice"></a>Mikrousługa magazynu OPC

Mikrousługa magazynu OPC składa się z następujących interfejsów, które umożliwiają wdrożenie przepływu pracy w celu dystrybuowania i zarządzania urzędem certyfikacji specyficznym dla danej firmy dla aplikacji OPC UA.

### <a name="application"></a>Aplikacja 
- Aplikacja OPC UA może być serwerem lub klientem lub jednocześnie. Magazyn OPC służy w tym przypadku jako urząd rejestrowania aplikacji. 
- Poza podstawowymi operacjami rejestrowania, aktualizowania i wyrejestrowywania aplikacji istnieją także interfejsy umożliwiające znajdowanie i wykonywanie zapytań dotyczących aplikacji z wyrażeniami wyszukiwania. 
- Żądania certyfikatu muszą odwoływać się do prawidłowej aplikacji, aby można było przetworzyć żądanie i wydać podpisany certyfikat ze wszystkimi rozszerzeniami OPC UA. 
- Usługa aplikacji jest obsługiwana przez bazę danych w Azure Cosmos DB.

### <a name="certificate-group"></a>Grupa certyfikatów
- Grupa certyfikatów to jednostka przechowująca główny urząd certyfikacji lub certyfikat podrzędnego urzędu certyfikacji, w tym klucz prywatny służący do podpisywania certyfikatów. 
- Długość wartości skrótu SHA-2 i okresy istnienia są konfigurowane zarówno dla urzędu certyfikacji wystawcy, jak i podpisanych certyfikatów aplikacji. 
- Certyfikaty urzędu certyfikacji są przechowywane w Azure Key Vault z użyciem modułu HSM FIPS 140-2 Level 2. Klucz prywatny nigdy nie opuszcza bezpiecznego magazynu, ponieważ podpisywanie odbywa się za pomocą operacji Key Vault zabezpieczonej przez usługę Azure AD. 
- Certyfikaty urzędu certyfikacji można odnowić z upływem czasu i pozostawać w bezpiecznym magazynie ze względu na historię Key Vault. 
- Lista odwołania dla każdego certyfikatu urzędu certyfikacji jest również przechowywana w Key Vault jako wpis tajny. Po wyrejestrowaniu aplikacji certyfikat aplikacji jest również odwoływany na liście odwołania certyfikatów (CRL) przez administratora.
- Można odwołać pojedyncze certyfikaty, a także certyfikaty wsadowe.

### <a name="certificate-request"></a>Żądanie certyfikatu
Żądanie certyfikatu implementuje przepływ pracy w celu wygenerowania nowej pary kluczy lub podpisanego certyfikatu przy użyciu żądania podpisania certyfikatu (CSR) dla aplikacji OPC UA. 
- Żądanie jest przechowywane w bazie danych z towarzyszącymi informacjami, takimi jak podmiot lub CSR, oraz odwołaniem do aplikacji OPC UA. 
- Logika biznesowa w usłudze sprawdza poprawność żądania względem informacji przechowywanych w bazie danych aplikacji. Na przykład identyfikator URI aplikacji w bazie danych musi być zgodny z identyfikatorem URI aplikacji w CSR.
- Administrator zabezpieczeń z prawami do podpisywania (czyli rolą osoby zatwierdzającej) zatwierdza lub odrzuca żądanie. Jeśli żądanie zostanie zatwierdzone, zostanie wygenerowana Nowa para kluczy lub podpisany certyfikat (lub oba). Nowy klucz prywatny jest bezpiecznie przechowywany w Key Vault, a nowy podpisany certyfikat publiczny jest przechowywany w bazie danych żądania certyfikatu.
- Obiekt żądający może sondować stan żądania, dopóki nie zostanie zatwierdzony lub odwołany. Jeśli żądanie zostało zatwierdzone, klucz prywatny i certyfikat można pobrać i zainstalować w magazynie certyfikatów aplikacji OPC UA.
- Obiekt żądający może teraz zaakceptować żądanie usunięcia niepotrzebnych informacji z bazy danych żądań. 

Po upływie okresu istnienia podpisanego certyfikatu aplikacja może zostać usunięta lub klucz może ulec naruszeniu. W takim przypadku Menedżer urzędu certyfikacji może:
- Usuń aplikację, która również usuwa wszystkie oczekujące i zatwierdzone żądania certyfikatów aplikacji. 
- Usuń tylko jedno żądanie certyfikatu, jeśli tylko klucz zostanie odnowiony lub naruszony.

Obecnie naruszone zabezpieczenia zatwierdzone i akceptowane żądania certyfikatów są oznaczane jako usunięte.

Menedżer może regularnie odnowić listę CRL urzędu certyfikacji wystawcy. Po odnowieniu wszystkie usunięte żądania certyfikatów zostaną odwołane, a numery seryjne certyfikatów są dodawane do listy odwołania listy CRL. Odwołane żądania certyfikatów są oznaczane jako odwołane. W przypadku pilnych zdarzeń można również odwołać pojedyncze żądania certyfikatu.

Na koniec zaktualizowane listy CRL są dostępne do dystrybucji do uczestniczących klientów i serwerów OPC UA.

## <a name="opc-vault-iot-edge-module"></a>Moduł IoT Edge magazynu OPC
W celu zapewnienia obsługi globalnego serwera odnajdywania sieci w fabryce można wdrożyć moduł magazynu OPC na krawędzi. Uruchom ją jako lokalną aplikację .NET Core lub uruchom ją w kontenerze platformy Docker. Należy pamiętać, że ze względu na brak obsługi uwierzytelniania elementu Auth2 w bieżącym stosie OPC UA .NET Standard funkcje modułu Edge magazynu OPC są ograniczone do roli czytelnik. Nie można spersonifikować użytkownika z modułu brzegowego do mikrousługi przy użyciu standardowego interfejsu OPC UA GDS.

## <a name="next-steps"></a>Następne kroki

Teraz, kiedy już wiesz o architekturze magazynu OPC, możesz:

> [!div class="nextstepaction"]
> [Kompilowanie i wdrażanie magazynu OPC](howto-opc-vault-deploy.md)
