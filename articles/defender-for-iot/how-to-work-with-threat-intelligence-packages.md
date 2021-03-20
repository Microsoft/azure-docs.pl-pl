---
title: Aktualizowanie danych analizy zagrożeń
description: Pakiet danych analizy zagrożeń jest dostarczany z każdą nową usługą Defender for IoT lub w razie potrzeby między wersjami.
author: shhazam-ms
manager: rkarlin
ms.author: shhazam
ms.date: 12/14/2020
ms.service: azure
ms.topic: how-to
ms.openlocfilehash: ddf2eb2fab6281e76bac0a958024b639d18a7ccc
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "100521411"
---
# <a name="threat-intelligence-research-and-packages"></a>Badania i pakiety analizy zagrożeń

Zespoły ds. zabezpieczeń w firmie Microsoft wykonują własne analizy zagrożeń i luki w zabezpieczeniach. Zespoły te obejmują MSTIC (Microsoft Threat Intelligence Center), DART (zespół wykrywania i reagowania firmy Microsoft), DCU (jednostka zbrodni cyfrowych) oraz sekcja 52 (firma IoT/OT/ICS, która śledzi określone przez usługę ICS zero dni, złośliwe oprogramowanie, kampanie i źródłami ataków).

Zespoły zapewniają wykrywanie zabezpieczeń, analizowanie i reagowanie na firmę Microsoft:

- Infrastruktura i usługi w chmurze.
- Tradycyjne produkty i urządzenia.
- Wewnętrzne zasoby firmy.

Zespoły ds. zabezpieczeń mogą korzystać z zalet:

- Ochrona przed znanymi i odpowiednimi zagrożeniami.
- Szczegółowe informacje ułatwiające Klasyfikacja i określanie priorytetów.
- Zrozumienie pełnego kontekstu zagrożeń przed ich włączeniem.
- Bardziej odpowiednie, dokładne i funkcjonalne dane.

Ta analiza zawiera informacje kontekstowe umożliwiające wzbogacanie analizy platformy firmy Microsoft i obsługuje usługi zarządzane przez firmę w celu reagowania na zdarzenia i badania naruszeń. Pakiety analizy zagrożeń zawierają podpisy (w tym podpisy złośliwego oprogramowania), CVEs i inną zawartość zabezpieczeń.

Pakiety można pobrać ze strony **aktualizacje** w portalu usługi Azure Defender dla IoT.

:::image type="content" source="media/how-to-work-with-threat-intelligence-packages/download-screen.png" alt-text="Pobierz aktualizacje za pomocą portalu usługi Azure Defender dla IoT.":::

## <a name="update-threat-intelligence-data"></a>Aktualizowanie danych analizy zagrożeń

Pakiety analizy zagrożeń są dostarczane z każdą nową wersją usługi Defender dla aktualizacji IoT lub w razie potrzeby między wersjami.

Pakiety pobierane z portalu usługi Defender for IoT można przekazać ręcznie do poszczególnych czujników. Jeśli lokalna Konsola zarządzania zarządza czujnikami, można pobrać pakiety analizy zagrożeń do konsoli zarządzania i wypchnąć je do wielu czujników jednocześnie.

Aby zaktualizować pakiet na jednym czujniku:

1. Przejdź do strony **aktualizacje** usługi Azure Defender dla IoT.

2. Pobierz i Zapisz pakiet **analizy zagrożeń** .

3. Zaloguj się do konsoli czujnika.

4. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

5. Wybierz pozycję **dane analizy zagrożeń**, a następnie wybierz pozycję **Aktualizuj**.

6. Przekaż nowy pakiet.

Aby zaktualizować pakiet na wielu czujników jednocześnie:

1. Przejdź do strony **aktualizacje** usługi Azure Defender dla IoT.

2. Pobierz i Zapisz pakiet **analizy zagrożeń** .

3. Zaloguj się do konsoli zarządzania.

4. W menu po stronie wybierz pozycję **Ustawienia systemowe**.

5. W sekcji **Konfiguracja aparatu czujnika** wybierz czujniki, które powinny otrzymywać zaktualizowane pakiety.  

6. W sekcji **Wybierz dane analizy zagrożeń** wybierz znak plus ( **+** ).

7. Przekaż pakiet.

## <a name="see-also"></a>Zobacz też

[Wersje aktualizacji](how-to-manage-sensors-from-the-on-premises-management-console.md#update-versions)
