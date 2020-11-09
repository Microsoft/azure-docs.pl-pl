---
title: Red Hat Enterprise Linux rozszerzona obsługa cyklu życia
description: Dowiedz się więcej na temat dodawania obsługi rozszerzonego cyklu życia systemu Red Hat Enterprise
author: mathapli
ms.service: virtual-machines-linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.reviewer: cynthn
ms.openlocfilehash: e7c9f9b158d01204536712f8024ed098cd7a5037
ms.sourcegitcommit: 65d518d1ccdbb7b7e1b1de1c387c382edf037850
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/09/2020
ms.locfileid: "94372879"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) rozszerzona obsługa cyklu życia
Ten artykuł zawiera informacje o rozszerzonej obsłudze cyklu życia obrazów systemu Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Cykl życia Red Hat Enterprise Linux 6
Począwszy od 30 listopada 2020, Red Hat Enterprise Linux 6 zostanie osiągnięty koniec fazy konserwacji. Po fazie konserwacji następuje rozbudowana faza okresu istnienia. Jako Red Hat Enterprise Linux 6 przejść z faz pełnej/konserwacji, zdecydowanie zaleca się uaktualnienie do Red Hat Enterprise Linux 7 lub 8. Jeśli klienci muszą pozostać w Red Hat Enterprise Linux 6, zaleca się dodanie dodatku Red Hat Enterprise Linux rozszerzonej pomocy technicznej cyklu życia (ELS).

## <a name="frequently-asked-questions"></a>Często zadawane pytania

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Korzystam z programu Red Hat Enterprise Linux 6 i nie można teraz migrować do nowszej wersji. Jakie są dostępne opcje?
* Kontynuuj uruchamianie Red Hat Enterprise Linux 6 i Kup Add-On repozytoria, aby w dalszym ciągu odbierać ograniczoną konserwację oprogramowania i pomoc techniczną (zobacz proces uaktualniania i szczegóły cennika).
* Przeprowadź migrację do Red Hat Enterprise Linux 7 lub 8 tak szybko, jak to możliwe.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Jaka jest dodatkowa opłata za korzystanie z usługi Red Hat Enterprise Linux dodatkowym obsłudze cyklu życia (ELS)?

|Rozmiar maszyny wirtualnej|Dodatkowy przedział czasu opłaty|Dodatkowa kwota dolara (USD)| Uwagi|
|---|---|---|---|
| Mały wirtualny Gości (<= 4 rdzenie) | Opłata dodatkowa co godzinę | TBC | |
|  | Miesięczna opłata dodatkowa | TBC | Dla wystąpień zarezerwowanych |
|  | Roczna opłata dodatkowa | TBC | Dla wystąpień zarezerwowanych |
| Duże wirtualne gościa (>4 rdzenie) | Opłata dodatkowa co godzinę | TBC | |
|  | Miesięczna opłata dodatkowa | TBC | Dla wystąpień zarezerwowanych |
|  | Roczna opłata dodatkowa | TBC | Dla wystąpień zarezerwowanych |

#### <a name="what-is-the-process-to-add-extended-life-cycle-support-els-repositories-to-continue-to-receive-software-maintenance-bug-and-security-fixes-and--support-for-red-hat-enterprise-linux-6"></a>Co to jest proces dodawania repozytoriów z rozszerzonym cyklem życia (ELS) w celu kontynuowania otrzymywania konserwacji oprogramowania (poprawek usterek i zabezpieczeń) oraz pomocy technicznej dla Red Hat Enterprise Linux 6?

Kompleksowy proces rejestracji w usłudze ELS będzie dostępny wkrótce (najpóźniej do 15 listopada, 2020).

## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić pełną listę obrazów RHEL na platformie Azure, zobacz [obrazy Red Hat Enterprise Linux (RHEL) dostępne na platformie Azure](./redhat-imagelist.md).
* Aby dowiedzieć się więcej na temat infrastruktury aktualizacji Red Hat platformy Azure, zobacz temat [infrastruktura aktualizacji Red Hat dla maszyn wirtualnych RHEL na żądanie na platformie Azure](./redhat-rhui.md).
* Aby dowiedzieć się więcej o ofercie RHEL BYOS, zobacz [Red Hat Enterprise Linux przenoszenie własnych subskrypcji na platformie Azure](./byos.md).
* Aby uzyskać informacje na temat zasad pomocy technicznej systemu Red Hat dla wszystkich wersji programu RHEL, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata).