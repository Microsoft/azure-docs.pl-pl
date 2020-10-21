---
title: Omówienie stosowania rabatu za rezerwację do usługi Azure Data Explorer
description: Dowiedz się, w jaki sposób rabat za rezerwację jest stosowany do miernika narzutu w usłudze Azure Data Explorer.
author: avneraa
ms.author: avnera
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 02/12/2020
ms.openlocfilehash: af2dc8378d1f1fe952a568ecb4bd89d088e3e3d3
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/17/2020
ms.locfileid: "92147238"
---
# <a name="understand-how-the-reservation-discount-is-applied-to-azure-data-explorer"></a>Omówienie stosowania rabatu za rezerwację do usługi Azure Data Explorer

Po zakupie zarezerwowanej pojemności usługi Azure Data Explorer rabat za rezerwację jest automatycznie stosowany do zasobów usługi Azure Data Explorer pasujących do atrybutów i ilości rezerwacji. Rezerwacja obejmuje opłaty narzutu usługi Azure Data Explorer. Nie obejmuje obliczeń, sieci, magazynu ani żadnego innego zasobu platformy Azure potrzebnego do obsługi klastra usługi Azure Data Explorer. Rezerwacje dla tych zasobów należy kupić osobno.

## <a name="how-reservation-discount-is-applied"></a>Jak jest naliczany rabat za rezerwację

Rabat za rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="reservation-discount-applied-to-azure-data-explorer-clusters"></a>Rabat za rezerwację zastosowany do klastrów usługi Azure Data Explorer

Rabat za rezerwację jest stosowany do godzinowego zużycia narzutu usługi Azure Data Explorer. W przypadku zasobów usługi Azure Data Explorer niepracujących przez całą godzinę rabat za rezerwację jest automatycznie stosowany dla innych zasobów usługi Azure Data Explorer zgodnych z atrybutami rezerwacji. Rabat może dotyczyć zasobów usługi Azure Data Explorer, które są uruchamiane równolegle. Jeśli nie masz zasobów usługi Azure Data Explorer działających przez całą godzinę oraz pasujących do atrybutów rezerwacji, nie wykorzystasz w pełni korzyści z rabatu za rezerwację w tej godzinie.

> [!NOTE]
> * **Zdecydowanie zaleca się** zakupienie [pojemności zarezerwowanej](../../virtual-machines/windows/prepay-reserved-vm-instances.md) dla maszyn wirtualnych używanych w klastrze usługi Azure Data Explorer, aby zmaksymalizować oszczędności związane z pojemnością zarezerwowaną.
> * Zakup rezerwacji spowoduje zastosowanie rabatów do wszystkich regionów.

## <a name="examples"></a>Przykłady

W poniższych przykładach pokazano, w jaki sposób rabat za pojemność zarezerwowaną usługi Azure Data Explorer jest stosowany w zależności od liczby zakupionych jednostek narzutu oraz czasu ich działania.
Na przykład rozmiar klastra aparatu: **2 maszyny wirtualne D11_v2** — łączne opłaty za użycie na żądanie dotyczą czterech jednostek miernika narzutu usługi Azure Data Explorer na godzinę.

**Scenariusz 1**

Kupujesz pojemność zarezerwowaną usługi Azure Data Explorer dla 8 jednostek narzutu usługi Azure Data Explorer. Uruchamiasz klaster aparatu z dwiema maszynami wirtualnymi D13_v2 o łącznej liczbie 16 rdzeni, w którym opłaty są naliczane za 16 jednostek narzutu usługi Azure Data Explorer na godzinę i który jest zgodny z resztą atrybutów rezerwacji. Opłata jest naliczana według ceny zgodnej z rzeczywistym użyciem za osiem rdzeni użycia mocy obliczeniowej usługi Azure Data Explorer, a rabat za rezerwację otrzymujesz na 1 godzinę użycia jednostki narzutu 8-rdzeniowej usługi Azure Data Explorer.

Na potrzeby pozostałych przykładów przyjęto założenie, że zakupiona pojemność zarezerwowana usługi Azure Data Explorer jest przeznaczona dla 16-rdzeniowego klastra usługi Azure Data Explorer, a pozostałe atrybuty rezerwacji pasują do działającego klastra usługi Azure Data Explorer.

**Scenariusz 2**

Uruchamiasz 2 klastry aparatu usługi Azure Data Explorer, każdy z 8 rdzeniami, na 1 godzinę w 2 różnych regionach. Rabat za rezerwację 16 rdzeni jest stosowany do obu klastrów i do 16 jednostek narzutu usługi Azure Data Explorer używanych przez te klastry.

**Scenariusz 3**

Uruchamiasz jeden 16-rdzeniowy klaster aparatu usługi Azure Data Explorer od godziny 13:00 do 13:30. Uruchamiasz kolejny 16-rdzeniowy klaster aparatu usługi Azure Data Explorer od godziny 13:30 do 14:00. Obie te bazy danych są objęte rabatem na rezerwację.

**Scenariusz 4**

Uruchamiasz jeden 16-rdzeniowy klaster aparatu usługi Azure Data Explorer od godziny 13:00 do 13:45. Uruchamiasz kolejny 16-rdzeniowy klaster aparatu usługi Azure Data Explorer od godziny 13:30 do 14:00. Za 15-minutowy okres jednoczesnego działania obu klastrów jest naliczana opłata według stawek płatności zgodnie z rzeczywistym użyciem. Na użycie narzutu usługi Azure Data Explorer przez resztę czasu jest stosowany rabat za rezerwację.

Aby poznać zastosowanie swoich rezerwacji platformy Azure w raportach rozliczeń użycia i przejrzeć je, zobacz [Omówienie użycia rezerwacji platformy Azure](understand-reserved-instance-usage-ea.md).

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji platformy Azure, zobacz następujące artykuły:

* [Opłacanie zasobów obliczeniowych usługi Azure Data Explorer z góry przy użyciu pojemności zarezerwowanej usługi Azure Data Explorer](/azure/data-explorer/pricing-reserved-capacity)  
* [Co to są rezerwacje platformy Azure](save-compute-costs-reservations.md)  
* [Zarządzanie rezerwacjami platformy Azure](manage-reserved-vm-instance.md)  
* [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
* [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)
* [Understand reservation usage for CSP subscriptions (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji CSP)](/partner-center/azure-reservations)