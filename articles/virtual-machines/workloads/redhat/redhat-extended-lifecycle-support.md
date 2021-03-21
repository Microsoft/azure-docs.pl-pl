---
title: Red Hat Enterprise Linux Extended Lifecycle Support
description: Dowiedz się więcej na temat dodawania obsługi rozszerzonego cyklu życia systemu Red Hat Enterprise
author: mathapli
ms.service: virtual-machines
ms.subservice: redhat
ms.collection: linux
ms.topic: article
ms.date: 04/16/2020
ms.author: mathapli
ms.openlocfilehash: 703732725ae7215d3ff59ad92a4c171a86251c20
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/20/2021
ms.locfileid: "101677198"
---
# <a name="red-hat-enterprise-linux-rhel-extended-lifecycle-support"></a>Red Hat Enterprise Linux (RHEL) rozszerzona obsługa cyklu życia
Ten artykuł zawiera informacje o rozszerzonej obsłudze cyklu życia obrazów systemu Red Hat Enterprise:
* Red Hat Enterprise Linux 6  

## <a name="red-hat-enterprise-linux-6-lifecycle"></a>Cykl życia Red Hat Enterprise Linux 6
Począwszy od 30 listopada 2020, Red Hat Enterprise Linux 6 zostanie osiągnięty koniec fazy konserwacji. Po fazie konserwacji następuje rozbudowana faza okresu istnienia. Jako Red Hat Enterprise Linux 6 przejść z faz pełnej/konserwacji, zdecydowanie zaleca się uaktualnienie do Red Hat Enterprise Linux 7 lub 8. Jeśli klienci muszą pozostać w Red Hat Enterprise Linux 6, zaleca się dodanie dodatku Red Hat Enterprise Linux rozszerzonej pomocy technicznej cyklu życia (ELS).

## <a name="steps-to-add-extended-lifecycle-support-on-marketplace-pay-as-you-go-vms"></a>Kroki umożliwiające dodanie rozszerzonej obsługi cyklu życia na maszynach wirtualnych z systemem Marketplace z opcją płatność zgodnie z rzeczywistym użyciem
1. Wypełnij [formularz Els dostępny tutaj](https://aka.ms/els-form) wraz z informacjami dotyczącymi kontaktu i subskrypcją maszyn wirtualnych, dla których chcesz dodać obsługę Els. Szczegółowe informacje o cenach są dostępne również w formularzu.
1. Zespół usługi Azure Red Hat Enterprise Linux będzie mógł skontaktować się z listą maszyn wirtualnych na potrzeby obsługi ELS w ciągu 1-2 dni roboczych. Przejrzyj listę i odpowiadaj na dodawanie cen.
1. Zespół usługi Azure Red Hat Enterprise Linux będzie udostępniać kroki umożliwiające dodanie pakietu klienta ELS do maszyn wirtualnych. Postępuj zgodnie z instrukcjami podanymi w wiadomości e-mail, aby nadal otrzymywać konserwacje oprogramowania (poprawki usterek i zabezpieczeń) oraz obsłużyć Red Hat Enterprise Linux 6.

> [!Note]
> Nie należy udostępniać czynności związanych z korzystaniem z usługi RHEL ELS Add on z innymi osobami spoza organizacji. Skontaktuj się z Tobą, aby AzureRedHatELS@microsoft.com uzyskać pomoc techniczną lub uzyskać dodatkowe pytania.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

#### <a name="im-running-red-hat-enterprise-linux-6-and-cant-migrate-to-a-later-version-at-this-time-what-options-do-i-have"></a>Korzystam z programu Red Hat Enterprise Linux 6 i nie można teraz migrować do nowszej wersji. Jakie są dostępne opcje?
* Kontynuuj uruchamianie Red Hat Enterprise Linux 6 i Kup Add-On repozytoria, aby w dalszym ciągu odbierać ograniczoną konserwację oprogramowania i pomoc techniczną (zobacz proces uaktualniania i szczegóły cennika).
* Przeprowadź migrację do Red Hat Enterprise Linux 7 lub 8 tak szybko, jak to możliwe.

#### <a name="what-is-the-additional-charge-for-using-red-hat-enterprise-linux-extended-life-cycle-support-els-add-on"></a>Jaka jest dodatkowa opłata za korzystanie z usługi Red Hat Enterprise Linux dodatkowym obsłudze cyklu życia (ELS)?
Koszty związane z rozszerzoną obsługą cyklu życia można znaleźć za pomocą [formularza Els](https://aka.ms/els-form)

#### <a name="ive-deployed-a-vm-by-using-custom-image-how-can-i-add-extended-lifecycle-support-to-this-vm"></a>Maszyna wirtualna została wdrożona przy użyciu obrazu niestandardowego. Jak dodać rozszerzoną obsługę cyklu życia do tej maszyny wirtualnej?
Musisz bezpośrednio kontaktować się z firmą Red Hat i uzyskać pomoc techniczną bezpośrednio z nich.

#### <a name="ive-deployed-a-vm-by-using-custom-image-can-i-convert-this-vm-to-a-payg-vm"></a>Maszyna wirtualna została wdrożona przy użyciu obrazu niestandardowego. Czy mogę przekonwertować tę maszynę wirtualną na maszynę wirtualną PAYG?
Nie. Konwersja nie jest obecnie obsługiwana na platformie Azure.


## <a name="next-steps"></a>Następne kroki

* Aby wyświetlić pełną listę obrazów RHEL na platformie Azure, zobacz [obrazy Red Hat Enterprise Linux (RHEL) dostępne na platformie Azure](./redhat-imagelist.md).
* Aby dowiedzieć się więcej na temat infrastruktury aktualizacji Red Hat platformy Azure, zobacz temat [infrastruktura aktualizacji Red Hat dla maszyn wirtualnych RHEL na żądanie na platformie Azure](./redhat-rhui.md).
* Aby dowiedzieć się więcej o ofercie RHEL BYOS, zobacz [Red Hat Enterprise Linux przenoszenie własnych subskrypcji na platformie Azure](./byos.md).
* Aby uzyskać informacje na temat zasad pomocy technicznej systemu Red Hat dla wszystkich wersji programu RHEL, zobacz [Red Hat Enterprise Linux cyklu życia](https://access.redhat.com/support/policy/updates/errata).