---
title: Azure IoT Hub Device Provisioning Service — zaświadczanie modułu TPM
description: Ten artykuł zawiera omówienie koncepcji przepływu zaświadczania modułu TPM przy użyciu usługi IoT Device Provisioning Service (DPS).
author: nberdy
ms.author: nberdy
ms.date: 04/04/2019
ms.topic: conceptual
ms.service: iot-dps
services: iot-dps
manager: briz
ms.openlocfilehash: 12860629d78391ed271306daba29a51aeb326c1d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "90531597"
---
# <a name="tpm-attestation"></a>Poświadczanie za pomocą modułu TPM

IoT Hub Device Provisioning Service to usługa pomocnika dla IoT Hub, która służy do konfigurowania aprowizacji urządzeń bez dotyku do określonego Centrum IoT Hub. Za pomocą usługi Device Provisioning można w bezpieczny sposób udostępnić miliony urządzeń.

W tym artykule opisano proces zaświadczania tożsamości podczas korzystania z Trusted Platform Module (TPM). Moduł TPM jest typem sprzętowego modułu zabezpieczeń (HSM). W tym artykule przyjęto założenie, że używasz dyskretnego, oprogramowania układowego lub zintegrowanego modułu TPM. Emulowane moduły TPM oprogramowania są dobrze dopasowane do prototypowania lub testowania, ale nie zapewniają tego samego poziomu zabezpieczeń, takiego jak dyskretny, oprogramowanie układowe lub zintegrowane moduły TPM do. Nie zalecamy korzystania z moduły TPM oprogramowania w środowisku produkcyjnym. Aby uzyskać więcej informacji na temat typów moduły TPM, zobacz [krótkie wprowadzenie do modułu TPM](https://trustedcomputinggroup.org/wp-content/uploads/TPM-2.0-A-Brief-Introduction.pdf).

Ten artykuł dotyczy tylko urządzeń korzystających z modułu TPM 2,0 z obsługą klucza HMAC oraz ich kluczy poręczenia. Nie dotyczy to urządzeń korzystających z certyfikatów X. 509 na potrzeby uwierzytelniania. Moduł TPM jest standardem ISO opartym na całej branży, a na podstawie Trusted Computing Group można dowiedzieć się więcej na temat modułu TPM i uzyskać [pełną specyfikację modułu tpm 2,0](https://trustedcomputinggroup.org/tpm-library-specification/) lub [specyfikację ISO/IEC 11889](https://www.iso.org/standard/66510.html). W tym artykule założono również, że znasz pary kluczy publicznych i prywatnych oraz jak są one używane do szyfrowania.

Zestawy SDK urządzeń usługi Device Provisioning Service obsługują wszystkie elementy, które zostały opisane w tym artykule. Nie ma potrzeby implementowania żadnych dodatkowych informacji w przypadku używania zestawów SDK na urządzeniach. Ten artykuł pomaga zrozumieć koncepcje, co się dzieje z Twoim układem zabezpieczeń modułu TPM, gdy urządzenie jest używane i dlaczego jest bezpieczne.

## <a name="overview"></a>Omówienie

Moduły TPM używają elementu o nazwie Key poręczenia jako bezpiecznego certyfikatu głównego zaufania. Klucz PORĘCZenia jest unikatowy dla modułu TPM i zmiana go zasadniczo zmienia urządzenie w nowe.

Istnieje inny typ klucza, który moduły TPM, nazywany kluczem głównym magazynu (SRK). Klucz główny magazynowania może zostać wygenerowany przez właściciela modułu TPM po przeprowadzeniu własności modułu TPM. Przejęcie własności modułu TPM jest specyficznym dla modułu TPM sposobem wymawiania "ktoś ustawia hasło w module HSM". Jeśli urządzenie TPM zostanie sprzedane nowemu właścicielowi, nowy właściciel może przejąć do niego własność modułu TPM w celu wygenerowania nowego klucza magazynowania. Nowa generacja klucza magazynowania gwarantuje, że poprzedni właściciel nie może użyć modułu TPM. Ponieważ główny klucz magazynowania jest unikatowy dla właściciela modułu TPM, może służyć do pieczętowania danych w module TPM dla tego właściciela. Klucz główny magazynowania udostępnia piaskownicę do przechowywania swoich kluczy i zapewnia dostęp do revocability, jeśli jest sprzedawany urządzenie lub moduł TPM. Jest to podobne do nowego domu: przyjęcie własności powoduje zmianę blokad w drzwiach i zniszczenie wszystkich mebli pozostawionych przez poprzednich właścicieli (SRK), ale nie można zmienić adresu domu (EK).

Gdy urządzenie zostanie skonfigurowane i będzie gotowe do użycia, będzie miało on dostęp do klucza poręczenia i magazynowania.

![Przejmowanie własności modułu TPM](./media/concepts-tpm-attestation/tpm-ownership.png)

Jedna Uwaga dotycząca przejmowania własności modułu TPM: przejęcie własności modułu TPM zależy od wielu rzeczy, takich jak producent modułu TPM, zestaw używanych narzędzi TPM i system operacyjny urządzenia. Postępuj zgodnie z instrukcjami dotyczącymi systemu, aby przejąć własność.

Usługa Device Provisioning używa publicznej części PORĘCZenia (EK_pub) do identyfikowania i rejestrowania urządzeń. Dostawca urządzenia może odczytywać EK_pub podczas produkcji lub testowania końcowego i przekazywać EK_pub do usługi aprowizacji, aby urządzenie było rozpoznawane po nawiązaniu połączenia z usługą. Usługa Device Provisioning nie sprawdza dostawcy magazynowania (SRK) ani właściciela, więc "czyszczenie" modułu TPM powoduje wymazanie danych klienta, ale niezależność (i inne dane dostawcy) jest zachowywana, a urządzenie jest nadal rozpoznawane przez usługę Device Provisioning, gdy nawiąże połączenie z udostępnianiem.

## <a name="detailed-attestation-process"></a>Szczegółowy proces zaświadczania

Gdy urządzenie z modułem TPM po raz pierwszy łączy się z usługą Device Provisioning, usługa najpierw sprawdzi podane EK_pub względem EK_pub przechowywanego na liście rejestracji. Jeśli EK_pubs nie są zgodne, urządzenie nie może inicjować obsługi administracyjnej. Jeśli EK_pubs są zgodne, usługa wymaga, aby urządzenie pokazało własność prywatnej części PORĘCZenia za pośrednictwem żądania nonce, które jest bezpiecznym wyzwaniem do udowodnienia tożsamości. Usługa Device Provisioning generuje identyfikator jednorazowy, a następnie szyfruje go za pomocą klucza głównego magazynowania, a następnie EK_pub, z których oba są dostarczane przez urządzenie podczas początkowego wywołania rejestracji. Moduł TPM zawsze utrzymuje prywatną część klucza Secure. Zapobiega to fałszowaniu i gwarantuje, że tokeny sygnatury dostępu współdzielonego są bezpiecznie obsługiwane na autoryzowanych urządzeniach.

Przechodźmy szczegółowo w procesie zaświadczania.

### <a name="device-requests-an-iot-hub-assignment"></a>Urządzenie żąda przypisania IoT Hub

Najpierw urządzenie nawiązuje połączenie z usługą Device Provisioning i żąda do aprowizacji. W takim przypadku urządzenie udostępnia usługę z IDENTYFIKATORem rejestracji, zakresem identyfikatorów oraz EK_pub i SRK_pub z modułu TPM. Usługa przekazuje zaszyfrowany identyfikator jednorazowy do urządzenia i prosi urządzenie o odszyfrowanie tego identyfikatora i użycie go do podpisania tokenu sygnatury dostępu współdzielonego w celu ponownego nawiązania połączenia i zakończenia aprowizacji.

![Inicjowanie obsługi żądań urządzeń](./media/concepts-tpm-attestation/step-one-request-provisioning.png)

### <a name="nonce-challenge"></a>Żądanie nonce

Urządzenie przyjmuje identyfikator jednorazowy i używa prywatnych części klucza poręczenia i klucza głównego, aby odszyfrować identyfikator jednorazowy do modułu TPM; kolejność szyfrowania nonce delegata zaufania od klucza poręczenia, który jest niezmienny, do klucza magazynowania, który można zmienić, jeśli nowy właściciel przejmuje własność modułu TPM.

![Odszyfrowywanie identyfikatora jednorazowego](./media/concepts-tpm-attestation/step-two-nonce.png)

### <a name="validate-the-nonce-and-receive-credentials"></a>Weryfikowanie identyfikatora jednorazowego i odbieranie poświadczeń

Urządzenie może następnie podpisać token sygnatury dostępu współdzielonego przy użyciu odszyfrowanego identyfikatora jednorazowego i ponownie nawiązać połączenie z usługą Device Provisioning przy użyciu sygnatury sygnatury dostępu współdzielonego. Po zakończeniu żądania nonce Usługa umożliwia obsługę administracyjną urządzenia.

![Urządzenie ponownie nawiązuje połączenie z usługą Device Provisioning w celu potwierdzenia własności PORĘCZenia](./media/concepts-tpm-attestation/step-three-validation.png)

## <a name="next-steps"></a>Następne kroki

Teraz urządzenie nawiązuje połączenie z usługą IoT Hub i zatrzymasz bezpieczeństwo w wiedzy, że klucze urządzeń są bezpiecznie przechowywane. Teraz, gdy już wiesz, jak usługa Device Provisioning bezpiecznie weryfikuje tożsamość urządzenia przy użyciu modułu TPM, zapoznaj się z następującymi artykułami, aby dowiedzieć się więcej:

* [Informacje o pojęciach dotyczących aprowizacji](about-iot-dps.md#provisioning-process)
* Rozpocznij [pracę z obsługą automatyczne](./quick-setup-auto-provision.md) przy użyciu zestawów SDK, aby zachować ostrożność przepływu.
