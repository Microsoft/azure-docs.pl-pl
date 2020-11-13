---
title: Oprogramowanie układowe mierzonego rozruchu i zaświadczania hosta — zabezpieczenia platformy Azure
description: Omówienie techniczne oprogramowania układowego platformy Azure mierzonym rozruchem i zaświadczeniem hosta.
author: yosharm
ms.service: security
ms.subservice: security-fundamentals
ms.topic: article
ms.author: terrylan
manager: rkarlin
ms.date: 11/10/2020
ms.openlocfilehash: 73ae811c17a578cafc557b0cda9e98b101dd5c03
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94557823"
---
# <a name="measured-boot-and-host-attestation"></a>Mierzony zaświadczanie rozruchu i hosta
W tym artykule opisano, jak firma Microsoft zapewnia integralność i bezpieczeństwo hostów przy użyciu mierzonych zaświadczania rozruchu i hosta.

## <a name="measured-boot"></a>Mierzony rozruch

[Moduł TPM](https://docs.microsoft.com/windows/security/information-protection/tpm/trusted-platform-module-top-node) (TPM) to nieautoryzowana, kryptograficzna w sposób bezpieczny składnik do przeprowadzania inspekcji z oprogramowaniem układowym oferowanym przez zaufaną firmę. Dziennik konfiguracji rozruchowych zawiera pomiary łańcucha mieszania zarejestrowane w rejestrach konfiguracji platformy (PCR), gdy host ostatnio podszedł do uruchomienia sekwencji. Na poniższej ilustracji przedstawiono ten proces nagrywania. Przyrostowe dodanie poprzednio wykorzystanej wartości zmieszanej do wartości skrótu następnej miary i uruchomienie algorytmu wyznaczania wartości skrótu w Unii pozwala uzyskać łańcuch wartości skrótu.

![Diagram przedstawiający tworzenie łańcucha skrótów usługi zaświadczania hosta.](./media/measured-boot-host-attestation/hash-chaining.png)

Zaświadczanie jest realizowane, gdy host dostarcza potwierdzenie stanu konfiguracji przy użyciu dziennika konfiguracji rozruchu (TCGLog). Fałszerstwo dziennika rozruchowego jest trudne, ponieważ moduł TPM nie ujawnia swoich wartości PCR innych niż operacje odczytu i rozszerzeń. Ponadto poświadczenia dostarczone przez usługę zaświadczania hosta są zapieczętowane do określonych wartości PCR. Korzystanie z łańcucha mieszania sprawia, że nie jest to możliwe w praktyce w przypadku fałszowania lub wyznaczania poświadczeń poza pasmem.

## <a name="host-attestation-service"></a>Usługa zaświadczania hosta

Usługa zaświadczania hosta to miara zapobiegawcza, która sprawdza, czy maszyny hosta są wiarygodne, zanim będą mogły współdziałać z danymi klienta lub obciążeniami. Sprawdzanie usług zaświadczania hosta przez zweryfikowanie instrukcji zgodności (weryfikowanie dowodu zgodności hosta) wysyłane przez każdego hosta do zasad zaświadczania (Definicja stanu bezpiecznego). Integralność tego systemu jest zapewniona przez [korzeń zaufania](https://www.uefi.org/sites/default/files/resources/UEFI%20RoT%20white%20paper_Final%208%208%2016%20%28003%29.pdf) udostępnianego przez moduł TPM.

Usługa zaświadczania hosta jest obecna w każdym klastrze systemu Azure w ramach wyspecjalizowanego środowiska w trybie blokady. Blokowane środowisko obejmuje inne usługi strażnika, które uczestniczą w protokole ładowania maszyn hosta. Infrastruktura kluczy publicznych (PKI) pełni rolę pośrednika służącą do sprawdzania poprawności żądań zaświadczania i jako wystawców tożsamości (co jest uwarunkowane zaświadczeniem hosta). Poświadczenia po zaświadczeniu wystawione dla hosta zaświadczania są zapieczętowane do jego tożsamości. Tylko Host żądający może odzapieczętować poświadczenia i wykorzystać je do uzyskania uprawnień przyrostowych. Zapobiega to atakom typu man-in-the-Middle i fałszowania.

Jeśli host platformy Azure dociera do fabryki z nieprawidłową konfiguracją zabezpieczeń lub został naruszony w centrum danych, jego TCGLog zawiera wskaźniki naruszenia zabezpieczeń oznaczone przez usługę zaświadczania hosta podczas następnego zaświadczania, co powoduje niepowodzenie zaświadczania. Niepowodzenia zaświadczania uniemożliwiają ufanie hostowi przez flotę platformy Azure. To zapobieganie efektywnie blokuje całą komunikację z i z hosta i wyzwala przepływ pracy zdarzenia. Badanie i szczegółowa analiza pośmiertna są przeprowadzane w celu określenia głównych przyczyn i wszelkich potencjalnych oznak naruszenia. Jest to możliwe dopiero po zakończeniu analizy, że host jest korygowany i ma możliwość dołączenia do floty platformy Azure i podjęcia obciążeń klientów.

Poniżej znajduje się architektura wysokiego poziomu usługi zaświadczania hosta:

![Diagram przedstawiający architekturę usługi zaświadczania hosta.](./media/measured-boot-host-attestation/host-attestation-arch.png)

## <a name="attestation-measurements"></a>Pomiary zaświadczania

Poniżej przedstawiono przykłady wielu pomiarów przechwyconych dzisiaj.

### <a name="secure-boot-and-secure-boot-keys"></a>Bezpieczny rozruch i klucze bezpiecznego rozruchu
Sprawdzając, czy skróty bazy danych sygnatur i odwołane sygnatury są poprawne, usługa zaświadczania hosta gwarantuje, że agent klienta traktuje odpowiednie oprogramowanie jako zaufane. Sprawdzając, czy sygnatura bazy danych klucza rejestracji klucza publicznego i publicznego klucza platformy, usługa zaświadczania hosta potwierdza, że tylko zaufane strony mają uprawnienia do modyfikowania definicji oprogramowania uznawanego za zaufany. Wreszcie dzięki zapewnieniu, że bezpieczny rozruch jest aktywny, usługa zaświadczania hosta weryfikuje te definicje są wymuszane.

### <a name="debug-controls"></a>Debuguj kontrolki
Debugery to zaawansowane narzędzia dla deweloperów. Jednak dostęp swobodnego do pamięci i innych poleceń debugowania może osłabić ochronę danych i integralność systemu w przypadku podanej niezaufanej strony. Usługa zaświadczania hosta zapewnia, że dowolny rodzaj debugowania jest wyłączony przy rozruchu na maszynach produkcyjnych.

### <a name="code-integrity"></a>Integralność kodu
[Bezpieczny rozruch](secure-boot.md) z interfejsem UEFI gwarantuje, że podczas sekwencji rozruchu będzie działać tylko zaufane oprogramowanie niskiego poziomu. Te same sprawdzenia, ale również muszą być stosowane w środowisku po rozruchowym do sterowników i innych plików wykonywalnych z dostępem do trybu jądra. W tym celu zasady integralności kodu są używane do definiowania, które sterowniki, pliki binarne i inne pliki wykonywalne są uznawane za zaufane przez określenie prawidłowych i nieprawidłowych podpisów. Te zasady są wymuszane. Naruszenia zasad generują alerty dla zespołu odpowiedzi na zdarzenia zabezpieczeń w celu zbadania problemu.

## <a name="next-steps"></a>Następne kroki
Aby dowiedzieć się więcej na temat tego, co robimy, aby zwiększyć integralność i bezpieczeństwo platformy, zobacz:

- [Zabezpieczenia oprogramowania układowego](firmware.md)
- [Bezpieczny rozruch](secure-boot.md)
- [Cerberus projektu](project-cerberus.md)
- [Szyfrowanie w spoczynku](encryption-atrest.md)
- [Zabezpieczenia funkcji hypervisor](hypervisor.md)