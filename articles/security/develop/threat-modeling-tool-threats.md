---
title: Zagrożenia — Microsoft Threat Modeling Tool na platformie Azure | Microsoft Docs
description: Strona kategorii zagrożeń dla Microsoft Threat Modeling Tool zawierającej kategorie wszystkich narażonych zagrożeń.
services: security
documentationcenter: na
author: jegeib
manager: jegeib
editor: jegeib
ms.assetid: na
ms.service: security
ms.subservice: security-develop
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/17/2017
ms.author: rodsan
ms.openlocfilehash: eb006482b851e9094b82ec3d0753b74c05296994
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "68727827"
---
# <a name="microsoft-threat-modeling-tool-threats"></a>Microsoft Threat Modeling Tool zagrożenia

Threat Modeling Tool jest elementem podstawowym Microsoft Security Development Lifecycle (SDL). Dzięki temu architektom oprogramowania można szybko identyfikować i ograniczać potencjalne problemy z zabezpieczeniami, gdy są one stosunkowo proste i ekonomiczne do rozwiązania. W efekcie znacznie zmniejsza łączny koszt rozwoju. Ponadto zaprojektowano narzędzie z ekspertami nienależącymi do zabezpieczeń, co sprawia, że modelowanie zagrożeń jest łatwiejsze dla wszystkich deweloperów, oferując jasne wskazówki dotyczące tworzenia i analizowania modeli zagrożeń.

> Odwiedź **[Threat Modeling Tool](threat-modeling-tool.md)** , aby zacząć już dzisiaj!

Threat Modeling Tool pomaga odpowiedzieć na niektóre pytania, takie jak poniższe poniżej:

* Jak osoba atakująca może zmienić dane uwierzytelniania?
* Jaki jest wpływ, jeśli osoba atakująca może odczytać dane profilu użytkownika?
* Co się stanie w przypadku odmowy dostępu do bazy danych profilu użytkownika?

## <a name="stride-model"></a>Model kroków

Aby lepiej pomóc w formułowaniu tych rodzajów pytań, firma Microsoft korzysta z modelu KROKowego, który klasyfikuje różne typy zagrożeń i upraszcza ogólne konwersacje w zakresie zabezpieczeń.

| Kategoria | Opis |
| -------- | ----------- |
| **Spoofing (fałszowanie)** | Polega na niedozwolonym dostępie, a następnie użyciu informacji o uwierzytelnianiu innego użytkownika, takich jak nazwa użytkownika i hasło. |
| **Tampering (manipulowanie)** | Obejmuje złośliwe modyfikacje danych. Przykładami mogą być nieautoryzowane zmiany wprowadzone do trwałych danych, takie jak przechowywane w bazie danych, oraz zmiana danych w miarę ich przepływu między dwoma komputerami za pośrednictwem otwartej sieci, na przykład Internetu. |
| **Repudiation (wypieranie się)** | Powiązane z użytkownikami, którzy odmówią wykonania akcji bez innych stron mających na celu udowodnienie inaczej — na przykład użytkownik wykonuje niedozwoloną operację w systemie, który nie ma możliwości śledzenia zabronionych operacji. Odrzucanie odnosi się do zdolności systemu do licznika zagrożeń odrzucania. Na przykład użytkownik, który kupuje element, może chcieć podpisać element po otrzymaniu. Dostawca może następnie użyć podpisanego potwierdzenia jako dowodu, że użytkownik otrzymał pakiet |
| **Information Disclosure (ujawnienie informacji)** | Obejmuje narażenie informacji osobom, które nie powinny mieć do nich dostępu — na przykład możliwość odczytywania przez użytkowników pliku, do którego nie udzielił dostępu, lub zdolności intruza do odczytu danych przesyłanych między dwoma komputerami |
| **Denial of Service (odmowa usługi)** | Ataki typu "odmowa usługi" (DoS) odmawiają usłudze prawidłowym użytkownikom — na przykład przez udostępnienie serwera sieci Web w sposób tymczasowy lub niezdatny do użytku. Należy chronić przed pewnymi rodzajami zagrożeń systemu DoS po prostu, aby zwiększyć dostępność i niezawodność usługi |
| **Elevation of Privilege (podniesienie uprawnień)** | Użytkownik nieuprzywilejowany uzyskuje dostęp uprzywilejowany i w związku z tym ma wystarczający dostęp do naruszenia lub zniszczenia całego systemu. Podniesienie poziomu zagrożeń związanych z uprawnieniami obejmuje te sytuacje, w których osoba atakująca skutecznie przełączyła wszystkie zabezpieczenia systemu i staje się częścią zaufanego systemu, w rzeczywistości niebezpiecznej sytuacji. |

## <a name="next-steps"></a>Następne kroki

Kontynuuj, aby **[Threat Modeling Tool środki zaradcze](threat-modeling-tool-mitigations.md)** , aby poznać różne sposoby ograniczania tych zagrożeń na platformie Azure.