---
title: Gramatyka reguły dotyczącej zaświadczania platformy Azure
description: Szczegóły dotyczące oświadczeń i reguł oświadczeń dotyczących zasad zaświadczania platformy Azure.
services: attestation
author: msmbaldwin
ms.service: attestation
ms.topic: overview
ms.date: 08/31/2020
ms.author: mbaldwin
ms.openlocfilehash: 3ed5c3f8232047787c6f05628f1eef35a7533999
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/05/2020
ms.locfileid: "91285422"
---
# <a name="claim-and-claim-rules"></a>Reguły dotyczące roszczeń i roszczeń

Aby zrozumieć gramatykę reguł oświadczeń, należy najpierw zrozumieć oświadczenia zasad zaświadczania.

## <a name="claim"></a>Claim

Jest to zestaw właściwości zgrupowanych razem w celu zapewnienia odpowiednich informacji. W przypadku zaświadczania platformy Azure, twierdzenie zawiera następujące właściwości:

- **Typ**: wartość ciągu reprezentująca typ żądania.
- **Value**: wartość logiczna, liczba całkowita lub ciąg, który reprezentuje wartość żądania.
- **ValueType**: typ danych informacji przechowywanych we właściwości Value. Obsługiwane typy to ciąg, liczba całkowita i wartość logiczna. Jeśli nie zostanie zdefiniowany, wartość domyślna to "String".
- **wystawca**: informacje dotyczące wystawcy oświadczenia. Wystawca będzie jednym z następujących typów:
  - **AttestationService**: Niektóre oświadczenia są udostępniane autorowi zasad przez zaświadczanie o platformie Azure, który może być używany przez autora zasad zaświadczania do napływania odpowiednich zasad.
  - **AttestationPolicy**: zasady (zgodnie z definicją przez administratora) mogą dodawać oświadczenia do przychodzących dowodów podczas przetwarzania. Wystawca w tym przypadku jest ustawiony na wartość "AttestationPolicy".
  - **CustomClaim**: zaświadczanie (klient) może również dodać dodatkowe oświadczenia do dowodu zaświadczania. Wystawca w tym przypadku jest ustawiony na wartość "CustomClaim".

Jeśli nie jest zdefiniowany. wartość domyślna to "CustomClaim".

## <a name="claim-rule"></a>Reguła dotycząca roszczeń

Zestaw przychodzący jest używany przez aparat zasad do obliczania wyniku zaświadczania. Reguła oświadczenia jest zestawem warunków używanym do weryfikowania oświadczeń przychodzących i wykonywania zdefiniowanej akcji.

```
Conditions list => Action (Claim)
```

Ocena zaświadczania o usłudze Azure dla reguły roszczeń obejmuje następujące kroki:

- Jeśli nie ma listy warunków, wykonaj akcję z określonym wnioskiem. 
- W przeciwnym razie Oceń warunki z listy warunków.
- Jeśli lista warunków zwróci wartość false, Zatrzymaj. W przeciwnym razie postępuj zgodnie z instrukcjami.

Warunki w regule roszczeń są używane do określenia, czy należy wykonać akcję. Lista warunków to sekwencja warunków, które są rozdzielone operatorem "&&".

Lista warunków ma strukturę:

```
Condition && Condition && ...
```

Warunek jest uporządkowany jako:

```
Identifier:[ClaimPropertyCondition, ClaimPropertyCondition,…]
```

Lista warunków składa się z poszczególnych warunków na różnych właściwościach żądania. Warunek może mieć opcjonalny identyfikator, który może służyć do odwoływania się do roszczeń/s, które spełniają warunek. To odwołanie może być używane w innych warunkach lub akcji tej samej reguły.

Na przykład

```
F1:[type=="OSName" , issuer=="CustomClaim"] && 
[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issueproperty(type="report_validity_in_minutes", value=1440);

F1:[type=="OSName" , issuer=="CustomClaim"] && 
C2:[type=="OSName" , issuer=="AttestationService", value== F1.value ] 
=> issue(claim = C2);
```

Poniżej znajdują się operatory, których można użyć do sprawdzenia warunków:

| Polach | Obsługiwane operacje |
|--|--|
| Integer | = = (Equals), \! = (nie równa się), <= (mniejsze niż lub równe), < (mniejsze niż), >= (większe lub równe), > (większe niż) |
| String (ciąg) | = = (Equals), \! = (nie równa się) |
| Boolean (wartość logiczna) | = = (Equals), \! = (nie równa się) |

Ocena listy warunków:

- Obecność operatora "&&" oznacza, że lista warunków jest szacowana jako true tylko wtedy, gdy wszystkie warunki z listy są oceniane na wartość true.
- Warunek reprezentuje kryteria filtrowania zestawu oświadczeń. Sam warunek jest określany na wartość true, jeśli istnieje co najmniej jedno wyrażenie spełniające warunek.
- Zastrzeżenie jest określane jako spełniające kryteria filtrowania reprezentowane przez warunek, jeśli każda z jego właściwości spełnia odpowiednie warunki właściwości roszczeń obecne w warunku.  

Poniżej opisano zestaw akcji, które są dozwolone w ramach zasad.

| Zlecenie akcji | Opis | Sekcje zasad, do których mają zastosowanie |
|--|--|--|
| Zezwalaj () | Zestaw przychodzących roszczeń może służyć do obliczania **issuancerules**. Nie przyjmuje żadnych roszczeń jako parametru | **reguł autoryzacji** |
| Odmów () | Zestaw przychodzących roszczeń nie powinien być używany do obliczania **issuancerules** nie przyjmuje żadnych roszczeń jako parametru | **reguł autoryzacji** |
| Dodaj (roszczeń) | Dodaje oświadczenie do zestawu oświadczeń przychodzących. Wszelkie oświadczenia dodane do zestawu oświadczeń przychodzących będą dostępne dla kolejnych reguł oświadczeń. |**reguł autoryzacji**, **issuancerules** |
| problem (roszczeń) | Dodaje oświadczenie do zestawu oświadczeń przychodzących i wychodzących | **issuancerules** |
| issueproperty (roszczeń) | Dodaje oświadczenie do zestawu oświadczeń przychodzących i właściwości | **issuancerules**

## <a name="next-steps"></a>Następne kroki

- [Tworzenie i podpisywanie zasad zaświadczania](author-sign-policy.md)
- [Konfigurowanie zaświadczania platformy Azure przy użyciu programu PowerShell](quickstart-powershell.md)

