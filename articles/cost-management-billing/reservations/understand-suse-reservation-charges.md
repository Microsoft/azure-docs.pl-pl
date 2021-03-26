---
title: Rabat za plan oprogramowania — Azure | Microsoft Docs
description: Dowiedz się, jak są stosowane rabaty za plany oprogramowania na maszynach wirtualnych.
author: yashesvi
ms.reviewer: yashar
ms.service: cost-management-billing
ms.subservice: reservations
ms.topic: conceptual
ms.date: 03/25/2021
ms.author: banders
ms.openlocfilehash: 8bf53715b7f19c44d9114150e617f903cd05a51e
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/26/2021
ms.locfileid: "105566319"
---
# <a name="azure-software-plan-discount"></a>Rabat za plan oprogramowania na platformie Azure

Plany oprogramowania SUSE i Red Hat na platformie Azure to rezerwacje stosowane do wdrożonych maszyn wirtualnych. Rabat za plan oprogramowania jest stosowany do użycia oprogramowania na wdrożonych maszynach wirtualnych pasujących do rezerwacji.

Po zamknięciu maszyny wirtualnej rabat zostanie automatycznie zastosowany do innej pasującej maszyny wirtualnej, o ile jest dostępna. Plan oprogramowania pokrywa koszt korzystania z oprogramowania na maszynie wirtualnej. Pozostałe opłaty, na przykład za obliczenia, magazyn i sieć, są naliczane osobno.

Aby kupić właściwy plan, musisz wiedzieć, jakich maszyn wirtualnych używasz i ile procesorów wirtualnych mają te maszyny wirtualne. Informacje w poniższych sekcjach pomogą Ci określić, jaki plan należy kupić, na podstawie danych o użyciu.

## <a name="how-reservation-discount-is-applied"></a>Jak jest stosowany rabat na rezerwacje

Rabat na rezerwację jest dostępny na zasadzie „*wykorzystaj lub strać*”. Zatem jeśli w ciągu jakiejś godziny nie będziesz mieć pasujących zasobów, utracisz ilość rezerwacji dla tej godziny. Niewykorzystanych godzin zarezerwowanych nie można przenieść na później.

Po wyłączeniu zasobu rabat za rezerwację automatycznie stosuje się do innego pasującego zasobu w określonym zakresie. Jeśli w określonym zakresie nie uda się znaleźć pasujących zasobów, zarezerwowane godziny zostaną *utracone*.

## <a name="review-redhat-vm-usage-before-you-buy"></a>Sprawdzanie użycia maszyn wirtualnych z oprogramowaniem Red Hat przed zakupem

Sprawdź nazwę produktu w danych o użyciu i kup plan oprogramowania Red Hat z takim samym typem i rozmiarem.

Na przykład jeśli w danych o użyciu znajdziesz produkt **Red Hat Enterprise Linux — licencja na maszyny wirtualne — od 1 do 4 procesorów wirtualnych**, należy zakupić plan **Red Hat Enterprise Linux** dla **maszyny wirtualnej mającej od 1 do 4 procesorów wirtualnych**.

<!--ADD RHEL SCREENSHOT -->

## <a name="review-suse-vm-usage-before-you-buy"></a>Sprawdzanie użycia maszyn wirtualnych z oprogramowaniem SUSE przed zakupem

Sprawdź nazwę produktu w danych o użyciu i kup plan oprogramowania SUSE z takim samym typem i rozmiarem.

Na przykład jeśli w danych o użyciu znajdziesz produkt **SUSE Linux Enterprise Server — subskrypcja Priorytetowa — obsługa maszyn wirtualnych od 2 do 4 procesorów**, należy zakupić plan **SUSE Linux Enterprise Server — subskrypcja Priorytetowa** dla **2–4 procesorów wirtualnych**.

![Przykład wyboru produktu do zakupu](./media/understand-suse-reservation-charges/select-suse-linux-enterprise-server-priority-2-4-vcpu.png)

## <a name="discount-applies-to-different-vm-sizes-for-suse-plans"></a>Rabat jest stosowany do różnych rozmiarów maszyn wirtualnych w ramach planów oprogramowania SUSE

Podobnie jak wystąpienia zarezerwowane maszyn wirtualnych zakupione plany oprogramowania SUSE oferują elastyczne rozmiary wystąpień. To oznacza, że rabat zostanie zastosowany nawet wtedy, gdy wdrożysz maszynę wirtualną z inną liczbą procesorów wirtualnych. Rabat jest stosowany do maszyn wirtualnych o różnych rozmiarach w ramach planu oprogramowania.

Kwota rabatu zależy od współczynnika określonego w poniższych tabelach. Ten współczynnik porównuje względne rozmiary dla każdego miernika w tej grupie. Współczynnik zależy od liczby procesorów wirtualnych maszyny wirtualnej. Używając wartości współczynnika, możesz obliczyć, ile wystąpień maszyn wirtualnych może otrzymać rabat za plan oprogramowania SUSE Linux.

Na przykład w przypadku zakupu planu oprogramowania SUSE Linux Enterprise Server dla HPC — subskrypcja Priorytetowa dla maszyny wirtualnej z 3 lub 4 procesorami wirtualnymi współczynnik dla tej rezerwacji wynosi 2. Rabat obejmuje koszt oprogramowania SUSE dla:

- 2 wdrożonych maszyn wirtualnych mających 1–2 procesory wirtualne,
- 1 wdrożonej maszyny wirtualnej mającej 3–4 procesory wirtualne
- albo 0,77 lub około 77% maszyny wirtualnej mającej 5 lub więcej procesorów wirtualnych.

Współczynnik dla 5 lub więcej procesorów wirtualnych wynosi 2,6. Dlatego rezerwacja oprogramowania SUSE dla maszyny wirtualnej mającej 5 lub więcej procesorów wirtualnych pokrywa tylko część kosztu oprogramowania — około 77%.

W poniższych tabelach przedstawiono plany oprogramowania, dla których możesz kupić rezerwacje, odpowiednie mierniki użycia i współczynniki.

### <a name="suse-linux-enterprise-server-for-hpc-priority"></a>SUSE Linux Enterprise Server dla HPC — subskrypcja Priorytetowa

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika| Współczynnik| Przykładowy rozmiar maszyny wirtualnej|
| -------| ------------------------| --- |--- |
|SUSE Linux Enterprise Server dla HPC — subskrypcja Priorytetowa 1-2 procesorów wirtualnych vCPU|e275a668-ce79-44e2-a659-f43443265e98|1|D2s_v3|
|SUSE Linux Enterprise Server dla HPC — subskrypcja Priorytetowa 3-4 procesorów wirtualnych vCPU|e531e1c0-09c9-4d83-b7d0-a2c6741faa22|2|D4s_v3|
|SUSE Linux Enterprise Server dla HPC — subskrypcja Priorytetowa 5 i procesorów wirtualnych vCPU|4edcd5a5-8510-49a8-a9fc-c9721f501913|2,6|D8s_v3|

### <a name="suse-linux-enterprise-server-for-hpc-standard"></a>SUSE Linux Enterprise Server dla HPC — subskrypcja Standardowa

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- | --- | ------------------------| --- |
|SUSE Linux Enterprise Server dla HPC — subskrypcja Standardowa 1-2 procesorów wirtualnych vCPU |8c94ad45-b93b-4772-aab1-ff92fcec6610|1|D2s_v3|
|SUSE Linux Enterprise Server dla HPC — subskrypcja Standardowa 3-4 procesorów wirtualnych vCPU|4ed70d2d-e2bb-4dcd-b6fa-42da71861a1c|1,92308|D4s_v3|
|SUSE Linux Enterprise Server dla HPC — subskrypcja Standardowa 5 i procesorów wirtualnych vCPU |907a85de-024f-4dd6-969c-347d47a1bdff|2,92308|D8s_v3|

### <a name="suse-linux-enterprise-server-for-sap-standard"></a>SUSE Linux Enterprise Server dla oprogramowania SAP Standard

Wcześniej SUSE Linux Enterprise Server for SAP standard ma nazwę SUSE Linux Enterprise Server dla SAP — subskrypcja Priorytetowa.

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- |------------------------| --- | --- |
|SUSE Linux Enterprise Server dla oprogramowania SAP standard 1-2 procesorów wirtualnych vCPU|497fe0b6-fa3c-4e3d-a66b-836097244142|1|D2s_v3|
|SUSE Linux Enterprise Server dla oprogramowania SAP Standard 3-4 procesorów wirtualnych vCPU |847887de-68ce-4adc-8a33-7a3f4133312f|2|D4s_v3|
|SUSE Linux Enterprise Server dla oprogramowania SAP Standard 5 + procesorów wirtualnych vCPU |18ae79cd-dfce-48c9-897b-ebd3053c6058|2,41176|D8s_v3|

### <a name="suse-linux-enterprise-server-standard"></a>SUSE Linux Enterprise Server — subskrypcja Standardowa

|Maszyna wirtualna z oprogramowaniem SUSE | Identyfikator miernika | Współczynnik|Przykładowy rozmiar maszyny wirtualnej|
| ------- |------------------------| --- |--- |
|Procesorów wirtualnych vCPU 1-2 rdzeni SUSE Linux Enterprise Server — subskrypcja Standardowa |4b2fecfc-b110-4312-8f9d-807db1cb79ae|1|D2s_v3|
|Procesorów wirtualnych vCPU 3-4 rdzeni SUSE Linux Enterprise Server — subskrypcja Standardowa |0c3ebb4c-db7d-4125-b45a-0534764d4bda|1,92308|D4s_v3|
|SUSE Linux Enterprise Server — subskrypcja Standardowa 5 i procesorów wirtualnych vCPU |7b349b65-d906-42e5-833f-b2af38513468|2,30769| D8s_v3|

## <a name="need-help-contact-us"></a>Potrzebujesz pomocy? Skontaktuj się z nami

Jeśli masz pytania lub potrzebujesz pomocy, [utwórz wniosek o pomoc techniczną](https://go.microsoft.com/fwlink/?linkid=2083458).

## <a name="next-steps"></a>Następne kroki

Aby dowiedzieć się więcej na temat rezerwacji, zobacz następujące artykuły:

- [Co to są rezerwacje platformy Azure?](save-compute-costs-reservations.md)
- [Opłacanie planów oprogramowania SUSE z góry dzięki rezerwacjom platformy Azure](../../virtual-machines/linux/prepay-suse-software-charges.md)
- [Prepay for Virtual Machines with Azure Reserved VM Instances (Opłacanie maszyn wirtualnych z góry przy użyciu usługi Azure Reserved VM Instances)](../../virtual-machines/prepay-reserved-vm-instances.md)
- [Zarządzanie usługą Azure Reservations](manage-reserved-vm-instance.md)
- [Understand reservation usage for your Pay-As-You-Go subscription (Informacje na temat użycia wystąpień zarezerwowanych w przypadku subskrypcji z płatnością zgodnie z rzeczywistym użyciem)](understand-reserved-instance-usage.md)
- [Understand reservation usage for your Enterprise enrollment (Informacje na temat użycia wystąpień zarezerwowanych w przypadku rejestracji Enterprise)](understand-reserved-instance-usage-ea.md)