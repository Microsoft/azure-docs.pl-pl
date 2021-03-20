---
title: Zabezpieczenia i uwierzytelnianie — IoT Edge Azure Event Grid | Microsoft Docs
description: Zabezpieczenia i uwierzytelnianie w Event Grid na IoT Edge.
author: VidyaKukke
manager: rajarv
ms.author: vkukke
ms.reviewer: spelluru
ms.date: 07/08/2020
ms.topic: article
ms.openlocfilehash: 42f6107ff79d6262cdc0a35cf972cf65d3a9a802
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "86171384"
---
# <a name="security-and-authentication"></a>Zabezpieczenia i uwierzytelnianie

Bezpieczeństwo i uwierzytelnianie jest zaawansowaną koncepcją i najpierw wymaga znajomości Event Grid podstawowych. Zacznij [tutaj](concepts.md) , jeśli dopiero zaczynasz Event Grid w IoT Edge. Moduł Event Grid kompiluje się w istniejącej infrastrukturze zabezpieczeń na IoT Edge. Zapoznaj się z [tą dokumentacją](../../iot-edge/security.md) , aby uzyskać szczegółowe informacje i konfigurację.

W poniższych sekcjach szczegółowo opisano, jak te ustawienia są zabezpieczone i uwierzytelniane:

* Konfiguracja protokołu TLS
* Uwierzytelnianie klienta przychodzącego
* Uwierzytelnianie serwera wychodzącego
* Uwierzytelnianie klienta wychodzącego

>[!IMPORTANT]
>Event Grid zabezpieczenia i uwierzytelnianie modułu wykorzystują istniejącą infrastrukturę dostępną w IoT Edge. Przyjęto założenie, że podsystem IoT Edge jest bezpieczny.

>[!IMPORTANT]
>Konfiguracja Event Grid jest **Domyślnie zabezpieczona**. W poniższych podsekcjach objaśniono wszystkie opcje i możliwe wartości, których można użyć w celu zastąpienia aspektów uwierzytelniania. Zrozumienie wpływu przed wprowadzeniem jakichkolwiek zmian. Aby zmiany zaczęły obowiązywać, należy ponownie wdrożyć moduł Event Grid.

## <a name="tls-configuration-aka-server-authentication"></a>Konfiguracja protokołu TLS (a. k. uwierzytelnianie serwera)

Moduł Event Grid obsługuje zarówno punkty końcowe HTTP, jak i HTTPS. Każdy moduł IoT Edge ma przypisany certyfikat serwera za pomocą demona zabezpieczeń IoT Edge. Certyfikat serwera jest używany do zabezpieczenia punktu końcowego. Po wygaśnięciu moduł automatycznie odświeża przy użyciu nowego certyfikatu z poziomu demona zabezpieczeń IoT Edge.

Domyślnie dozwolona jest tylko komunikacja przy użyciu protokołu HTTPS. Zachowanie to można zastąpić za pomocą konfiguracji  **inbound__serverAuth__tlsPolicy** . W poniższej tabeli przedstawiono możliwe wartości tej właściwości.

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| Dokładny | Domyślne. Włącza tylko HTTPS
| Enabled (Włączony) | Włącza zarówno protokół HTTP, jak i HTTPS
| Disabled | Włącza tylko protokół HTTP

## <a name="inbound-client-authentication"></a>Uwierzytelnianie klienta przychodzącego

Klienci są jednostkami wykonującymi operacje zarządzania i/lub środowiska uruchomieniowego. Klienci mogą być innymi modułami IoT Edge, aplikacjami nieiot.

Moduł Event Grid obsługuje dwa typy uwierzytelniania klientów:

* Oparty na kluczu sygnatury dostępu współdzielonego (SAS)
* oparte na certyfikatach

Domyślnie moduł Event Grid jest skonfigurowany do akceptowania tylko uwierzytelniania opartego na certyfikatach. Podczas uruchamiania Moduł Event Grid pobiera "TrustBundle" z demona zabezpieczeń IoT Edge i używa go do weryfikacji dowolnego certyfikatu klienta. Certyfikaty klienta, które nie są rozpoznawane w tym łańcuchu, będą odrzucane za pomocą `UnAuthorized` .

### <a name="certificate-based-client-authentication"></a>Uwierzytelnianie klienta oparte na certyfikatach

Uwierzytelnianie oparte na certyfikatach jest domyślnie włączone. Uwierzytelnianie oparte na certyfikacie można wyłączyć za pomocą właściwości **inbound__clientAuth__clientCert__enabled**. W poniższej tabeli przedstawiono możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Wymaga, aby wszystkie żądania kierowane do modułu Event Grid zaprezentować certyfikat klienta. Ponadto należy skonfigurować **inbound__clientAuth__clientCert__source**.
| fałsz | Nie Wymuszaj, aby klient mógł przedstawić certyfikat.

W poniższej tabeli przedstawiono możliwe wartości dla **inbound__clientAuth__clientCert__source**

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| IoT Edge | Domyślne. Używa Trustbundle IoT Edge do weryfikacji wszystkich certyfikatów klienta.

Jeśli klient przedstawia z podpisem własnym, domyślnie moduł Event Grid będzie odrzucał takie żądania. Możesz zezwolić na używanie certyfikatów klienta z podpisem własnym za pomocą właściwości **inbound__clientAuth__clientCert__allowUnknownCA** . W poniższej tabeli przedstawiono możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------|
| true | Domyślne. Zezwala na pomyślne prezentowanie certyfikatów z podpisem własnym.
| fałsz | Żądania będą kończyć się niepowodzeniem, jeśli są prezentowane certyfikaty z podpisem własnym.

>[!IMPORTANT]
>W scenariuszach produkcyjnych warto ustawić **wartość false** dla **inbound__clientAuth__clientCert__allowUnknownCA** .

### <a name="sas-key-based-client-authentication"></a>Uwierzytelnianie klienta oparte na kluczach SAS

Oprócz uwierzytelniania opartego na certyfikatach moduł Event Grid może również wykonywać uwierzytelnianie oparte na kluczach SAS. Klucz sygnatury dostępu współdzielonego jest taki sam jak wpis tajny skonfigurowany w module Event Grid, który powinien być używany do weryfikacji wszystkich wywołań przychodzących. Klienci muszą określić klucz tajny w nagłówku HTTP "AEG-SAS-Key". Żądanie zostanie odrzucone `UnAuthorized` , jeśli nie jest zgodne.

Konfiguracja umożliwiająca kontrolę uwierzytelniania opartego na kluczach SAS jest **inbound__clientAuth__sasKeys__enabled**.

| Możliwe wartości | Opis  |
| ----------------  | ------------ |
| true | Zezwala na uwierzytelnianie oparte na kluczach SAS. Wymaga **inbound__clientAuth__sasKeys__key1** lub **inbound__clientAuth__sasKeys__key2**
| fałsz | Domyślne. Uwierzytelnianie oparte na kluczach SAS jest wyłączone.

 **inbound__clientAuth__sasKeys__key1** i **inbound__clientAuth__sasKeys__key2** są kluczami skonfigurowanymi do modułu Event Grid w celu sprawdzania żądań przychodzących. Należy skonfigurować co najmniej jeden klucz. Klient wykonujący żądanie będzie musiał przedstawić klucz jako część nagłówka żądania "**AEG-SAS-Key**". Jeśli oba klucze są skonfigurowane, klient może przedstawić jeden z kluczy.

> [!NOTE]
>Można skonfigurować obie metody uwierzytelniania. W takim przypadku klucz SAS przypadku jest sprawdzany jako pierwszy i tylko wtedy, gdy to się nie powiedzie, zostanie wykonane uwierzytelnianie oparte na certyfikacie. Aby żądanie powiodło się, tylko jedna z metod uwierzytelniania musi się powieść.

## <a name="outbound-client-authentication"></a>Uwierzytelnianie klienta wychodzącego

Klient w kontekście wychodzącym odwołuje się do modułu Event Grid. Wykonywana operacja jest dostarczająca zdarzenia do subskrybentów. Moduły subskrybowania są traktowane jako serwer.

Każdy moduł IoT Edge ma przypisany certyfikat tożsamości przez demona zabezpieczeń IoT Edge. Używamy certyfikatu tożsamości dla wywołań wychodzących. Po wygaśnięciu moduł automatycznie odświeża przy użyciu nowego certyfikatu z poziomu demona zabezpieczeń IoT Edge.

Konfiguracja do kontrolowania uwierzytelniania klienta wychodzącego jest **outbound__clientAuth__clientCert__enabled**.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Wymaga, aby wszystkie żądania wychodzące z modułu Event Grid zaprezentować certyfikat. Należy skonfigurować **outbound__clientAuth__clientCert__source**.
| fałsz | Nie wymagaj modułu Event Grid do zaprezentowania swojego certyfikatu.

Konfiguracja, która kontroluje Źródło certyfikatu, jest **outbound__clientAuth__clientCert__source**.

| Możliwe wartości | Opis |
| ---------------- | ------------ |
| IoT Edge | Domyślne. Używa certyfikatu tożsamości modułu skonfigurowanego za pomocą demona zabezpieczeń IoT Edge.

### <a name="outbound-server-authentication"></a>Uwierzytelnianie serwera wychodzącego

Jednym z typów docelowych dla subskrybenta Event Grid jest "webhook". Domyślnie tylko punkty końcowe HTTPS są akceptowane dla takich subskrybentów.

Konfiguracja kontrolowania zasad docelowych elementu webhook **outbound__webhook__httpsOnly**.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Zezwala tylko subskrybentom z punktem końcowym HTTPS.
| fałsz | Zezwala na subskrybenci z punktem końcowym HTTP lub HTTPS.

Domyślnie moduł Event Grid sprawdza poprawność certyfikatu serwera subskrybenta. Sprawdzanie poprawności można pominąć, zastępując **outbound__webhook__skipServerCertValidation**. Możliwe wartości:

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Nie Weryfikuj certyfikatu serwera subskrybenta.
| fałsz | Domyślne. Sprawdź poprawność certyfikatu serwera subskrybenta.

Jeśli certyfikat subskrybenta jest z podpisem własnym, domyślnie moduł Event Grid będzie odrzucać takich subskrybentów. Aby zezwolić na certyfikat z podpisem własnym, możesz przesłonić **outbound__webhook__allowUnknownCA**. W poniższej tabeli przedstawiono możliwe wartości.

| Możliwe wartości | Opis |
| ----------------  | ------------ |
| true | Domyślne. Zezwala na pomyślne prezentowanie certyfikatów z podpisem własnym.
| fałsz | Żądania będą kończyć się niepowodzeniem, jeśli są prezentowane certyfikaty z podpisem własnym.

>[!IMPORTANT]
>W scenariuszach produkcyjnych należy ustawić **wartość false** dla **outbound__webhook__allowUnknownCA** .

> [!NOTE]
>Środowisko IoT Edge generuje certyfikaty z podpisem własnym. Zalecenie polega na wygenerowaniu certyfikatów wystawionych przez autoryzowanych urzędów certyfikacji dla obciążeń produkcyjnych i ustawiania właściwości **allowUnknownCA** zarówno dla ruchu przychodzącego, jak i wychodzącego do **Fałsz**.

## <a name="summary"></a>Podsumowanie

Moduł Event Grid jest **zabezpieczony domyślnie**. Zalecamy zachowywanie tych ustawień domyślnych dla wdrożeń produkcyjnych.

Poniżej przedstawiono zasady dotyczące identyfikatorów, które mają być używane podczas konfigurowania:

* Zezwalaj tylko na żądania HTTPS do modułu.
* Zezwalaj tylko na uwierzytelnianie klienta oparte na certyfikatach. Zezwalaj tylko na te certyfikaty, które są wystawiane przez dobrze znane urzędy certyfikacji. Nie Zezwalaj na certyfikaty z podpisem własnym.
* Nie Zezwalaj na uwierzytelnianie klienta oparte na SASKey.
* Zawsze obecny certyfikat tożsamości modułu Event Grid na wywołaniach wychodzących.
* Zezwalaj tylko subskrybentom HTTPS na typy docelowe elementu webhook.
* Zawsze Weryfikuj certyfikat serwera subskrybenta dla typów docelowych elementu webhook. Zezwalaj tylko na certyfikaty wystawiane przez dobrze znane urzędy certyfikacji. Nie Zezwalaj na certyfikaty z podpisem własnym.

Domyślnie moduł Event Grid jest wdrażany z następującą konfiguracją:

 ```json
 {
  "Env": [
    "inbound__serverAuth__tlsPolicy=strict",
    "inbound__serverAuth__serverCert__source=IoTEdge",
    "inbound__clientAuth__sasKeys__enabled=false",
    "inbound__clientAuth__clientCert__enabled=true",
    "inbound__clientAuth__clientCert__source=IoTEdge",
    "inbound__clientAuth__clientCert__allowUnknownCA=true",
    "outbound__clientAuth__clientCert__enabled=true",
    "outbound__clientAuth__clientCert__source=IoTEdge",
    "outbound__webhook__httpsOnly=true",
    "outbound__webhook__skipServerCertValidation=false",
    "outbound__webhook__allowUnknownCA=true"
  ],
  "HostConfig": {
    "PortBindings": {
      "4438/tcp": [
        {
          "HostPort": "4438"
        }
      ]
    }
  }
}
```
