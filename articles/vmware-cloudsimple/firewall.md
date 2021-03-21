---
title: Rozwiązanie VMware firmy Azure według CloudSimple — Konfigurowanie tabel i reguł zapory
description: Opisuje sposób konfigurowania tabel i zasad zapory chmury prywatnej w celu ograniczenia ruchu sieciowego w podsieciach i sieci VLAN.
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/15/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 32dcfb0c6cd26b5f34afad328db8f383fa1c2a6f
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/19/2021
ms.locfileid: "97895635"
---
# <a name="set-up-firewall-tables-and-rules-for-private-clouds"></a>Konfigurowanie tabel i reguł zapory dla chmur prywatnych

Tabele zapory i skojarzone reguły pozwalają określić ograniczenia dotyczące ruchu, który ma być stosowany do określonych podsieci i sieci VLAN.

* Podsieć może być skojarzona z jedną tabelą zapory.
* Tabela zapory może być skojarzona z wieloma podsieciami.

## <a name="add-a-new-firewall-table"></a>Dodaj nową tabelę zapory

1. [Uzyskaj dostęp do portalu CloudSimple](access-cloudsimple-portal.md) i wybierz pozycję **Sieć** w menu po stronie.
2. Wybierz pozycję **tabele zapory**.
3. Wybierz pozycję **Utwórz tabelę zapory**.

    ![Strona sieci VLAN/podsieć](media/firewall-tables-page.png)

4. Wprowadź nazwę tabeli.
5. Zostanie wyświetlona domyślna reguła dla tabeli. Kliknij pozycję **Utwórz nową regułę** , aby utworzyć dodatkową regułę. Aby uzyskać szczegółowe informacje, zobacz poniższą procedurę.
6. Kliknij przycisk **gotowe** , aby zapisać tabelę zapory.

> [!IMPORTANT]
> Można utworzyć maksymalnie dwie tabele zapory na chmurę prywatną.

## <a name="firewall-rules"></a>Reguły zapory

Reguły zapory określają sposób traktowania poszczególnych typów ruchu przez zaporę. Na karcie **reguły** dla wybranej tabeli zapory są wyświetlane wszystkie skojarzone reguły.

![Tabela reguł zapory](media/firewall-rules-tab.png)

## <a name="create-a-firewall-rule"></a>Tworzenie reguły zapory

1. Wyświetl ustawienia, aby utworzyć regułę zapory w jeden z następujących sposobów:
    * Po utworzeniu tabeli zapory kliknij pozycję **Dodaj regułę** .
    * Wybierz określoną tabelę zapory na stronie **sieci > zapory** , a następnie kliknij pozycję **Utwórz nową regułę zapory**.
2. Skonfiguruj regułę w następujący sposób:
    * **Nazwa**. Nadaj regule nazwę.
    * **Priorytet**. Przypisz priorytet do reguły. Reguły o niższych numerach są wykonywane jako pierwsze.
    * **Typ ruchu sieciowego**. Wybierz, czy reguła dotyczy ruchu w chmurze prywatnej, Internetu lub sieci VPN (bezstanowej), czy też dla publicznego adresu IP (stanowy).
    * **Protokół**. Wybierz protokół objęty regułą (TCP, UDP lub dowolny protokół).
    * **Kierunek**. Wybierz, czy reguła dotyczy ruchu przychodzącego, czy wychodzącego. Należy zdefiniować osobne reguły dla ruchu przychodzącego i wychodzącego.
    * **Akcja**. Wybierz akcję, która ma zostać podjęta, jeśli reguła jest zgodna (Zezwalaj lub Odmów).
    * **Źródło**. Określ źródła objęte regułą (blok CIDR, wewnętrzne lub dowolne Źródło).
    * **Zakres portów źródłowych**. Określ zakres portów, które podlegają regule.
    * **Kierunek**. Wybierz pozycję przychodzące lub wychodzące.
    * **Miejsce docelowe**. Określ miejsca docelowe objęte regułą (blok CIDR, wewnętrzne lub dowolne Źródło).
    * **Zakres portów źródłowych**. Określ zakres portów, które podlegają regule.

    ![Reguła dodawania tabeli zapory](media/firewall-rule-create.png)

3. Kliknij przycisk **gotowe** , aby zapisać regułę i dodać ją do listy reguł dla tabeli zapory.

> [!IMPORTANT]
> Każda tabela zapory może mieć do 10 reguł ruchu przychodzącego i 20 reguł wychodzących. Limity te można zwiększyć, [kontaktując się z pomocą techniczną](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="attach-vlanssubnets"></a><a name="attach-vlans-subnet"></a>Dołącz sieci VLAN/podsieci

Po zdefiniowaniu tabeli zapory można określić podsieci, które podlegają regułom w tabeli.

1. Na stronie   >  **tabele zapory** sieciowej wybierz tabelę zapory.
2. Otwórz kartę **podłączone sieci VLAN/podsieć** .
3. Kliknij przycisk **Dołącz do sieci VLAN/podsieci**.
4. Wybierz chmurę prywatną i sieć VLAN. Wyświetlana jest nazwa skojarzonej podsieci i blok CIDR.
5. Kliknij przycisk **Prześlij**.
