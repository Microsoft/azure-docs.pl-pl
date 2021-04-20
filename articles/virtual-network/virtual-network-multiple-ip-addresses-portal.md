---
title: Wiele adresów IP dla maszyn wirtualnych platformy Azure — portal | Microsoft Docs
description: Dowiedz się, jak przypisać wiele adresów IP do maszyny wirtualnej przy użyciu Azure Portal | Resource Manager.
services: virtual-network
documentationcenter: na
author: asudbring
manager: KumudD
ms.service: virtual-network
ms.subservice: ip-services
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/30/2016
ms.author: allensu
ms.openlocfilehash: 050d3ac23562e7822d186a16675d03c1b9dc670b
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/20/2021
ms.locfileid: "107739210"
---
# <a name="assign-multiple-ip-addresses-to-virtual-machines-using-the-azure-portal"></a>Przypisywanie wielu adresów IP do maszyn wirtualnych przy użyciu Azure Portal

> [!INCLUDE [virtual-network-multiple-ip-addresses-intro.md](../../includes/virtual-network-multiple-ip-addresses-intro.md)]
> 
> W tym artykule wyjaśniono, jak utworzyć maszynę wirtualną za pomocą Azure Resource Manager wdrażania przy użyciu Azure Portal. Do zasobów utworzonych za pomocą klasycznego modelu wdrażania nie można przypisać wielu adresów IP. Aby dowiedzieć się więcej na temat modeli wdrażania platformy Azure, przeczytaj [artykuł Understand deployment models (Informacje o modelach wdrażania).](../azure-resource-manager/management/deployment-models.md)

[!INCLUDE [virtual-network-multiple-ip-addresses-scenario.md](../../includes/virtual-network-multiple-ip-addresses-scenario.md)]

## <a name="create-a-vm-with-multiple-ip-addresses"></a><a name = "create"></a>Tworzenie maszyny wirtualnej z wieloma adresami IP

Jeśli chcesz utworzyć maszynę wirtualną z wieloma adresami IP lub statycznym prywatnym adresem IP, musisz utworzyć ją przy użyciu programu PowerShell lub interfejsu wiersza polecenia platformy Azure. Aby dowiedzieć się, jak to zrobić, kliknij opcje programu PowerShell lub interfejsu wiersza polecenia w górnej części tego artykułu. Maszynę wirtualną można utworzyć z jednym dynamicznym prywatnym adresem IP i (opcjonalnie) pojedynczym publicznym adresem IP. Użyj portalu, korzystając z procedury opisanej w artykułach [Tworzenie maszyny wirtualnej z systemem Windows](../virtual-machines/windows/quick-create-portal.md) lub Tworzenie maszyny [wirtualnej z systemem Linux.](../virtual-machines/linux/quick-create-portal.md) Po utworzeniu maszyny wirtualnej możesz zmienić typ adresu IP z dynamicznego na statyczny i dodać dodatkowe adresy IP przy użyciu portalu, korzystając z kroków w sekcji Dodawanie adresów [IP](#add) do maszyny wirtualnej w tym artykule.

## <a name="add-ip-addresses-to-a-vm"></a><a name="add"></a>Dodawanie adresów IP do maszyny wirtualnej

Prywatne i publiczne adresy IP można dodać do interfejsu sieciowego platformy Azure, wykonując poniższe kroki. W przykładach w poniższych sekcjach założono, że masz już maszynę wirtualną z trzema konfiguracjami adresów IP opisanymi w scenariuszu [,](#scenario)ale nie jest to wymagane.

### <a name="core-steps"></a><a name="coreadd"></a>Podstawowe kroki

1. Przejdź do witryny Azure Portal i w razie potrzeby https://portal.azure.com zaloguj się do niego.
2. W portalu kliknij pozycję **Więcej usług, >** *w* polu filtru wpisz maszyny wirtualne, a następnie kliknij pozycję **Maszyny wirtualne.**
3. W **okienku Maszyny** wirtualne kliknij maszynę wirtualną, do której chcesz dodać adresy IP. Przejdź do **karty Sieć.** **Kliknij pozycję Interfejs sieciowy** na stronie. Jak pokazano na poniższej ilustracji: 


    ![Dodawanie publicznego adresu IP do maszyny wirtualnej](./media/virtual-network-multiple-ip-addresses-portal/figure200319.png)
4. W **okienku Interfejs** sieciowy kliknij **konfiguracje adresów IP.**

5. W okienku wyświetlonym dla wybranej karty sieciowej kliknij pozycję **Konfiguracje adresów IP.** Kliknij **przycisk** Dodaj , wykonaj kroki opisane w jednej z poniższych sekcji na podstawie typu adresu IP, który chcesz dodać, a następnie kliknij przycisk **OK.** 

### <a name="add-a-private-ip-address"></a>Dodawanie prywatnego adresu IP

Wykonaj następujące kroki, aby dodać nowy prywatny adres IP:

1. Wykonaj kroki opisane w sekcji [Podstawowe](#coreadd) kroki tego artykułu i upewnij się, że jesteś w sekcji Konfiguracje **adresów IP** interfejsu sieciowego maszyny wirtualnej.  Przejrzyj podsieć pokazaną jako domyślną (na przykład 10.0.0.0/24).
2. Kliknij pozycję **Dodaj**. W **wyświetlonym okienku Dodaj** konfigurację adresu IP utwórz konfigurację  adresu IP o nazwie *IPConfig-4* z nowym statycznym prywatnym adresem IP, wybierając nową liczbę dla ostatniego oktetu, a następnie kliknij przycisk **OK.**  (W przypadku podsieci 10.0.0.0/24 przykładowy adres IP to *10.0.0.7).*

    > [!NOTE]
    > Podczas dodawania statycznego adresu IP należy określić nieużywany prawidłowy adres w podsieci, z którym jest połączona karta sieciowa. Jeśli wybierany adres jest niedostępny, w portalu zostanie wyświetlony znak X dla adresu IP i należy wybrać inny adres.

3. Po kliknięciu przycisku OK okienko zostanie zamknięte i zostanie wyświetlony nowy adres IP konfiguracji na liście. Kliknij **przycisk OK,** aby zamknąć **okienko Dodawanie konfiguracji adresu** IP.
4. Możesz kliknąć przycisk **Dodaj,** aby dodać dodatkowe konfiguracje adresów IP, lub zamknąć wszystkie otwarte blade, aby zakończyć dodawanie adresów IP.
5. Dodaj prywatne adresy IP do systemu operacyjnego maszyny wirtualnej, wykonując kroki opisane w sekcji Dodawanie adresów [IP](#os-config) do systemu operacyjnego maszyny wirtualnej w tym artykule.

### <a name="add-a-public-ip-address"></a>Dodawanie publicznego adresu IP

Publiczny adres IP jest dodawany przez skojarzenie zasobu publicznego adresu IP z nową konfiguracją adresu IP lub istniejącą konfiguracją adresu IP.

> [!NOTE]
> Za publiczne adresy IP pobierana jest nominalna opłata. Aby dowiedzieć się więcej o cenach adresów IP, przeczytaj [stronę cennika adresów IP.](https://azure.microsoft.com/pricing/details/ip-addresses) Istnieje ograniczenie liczby publicznych adresów IP, które mogą być używane w subskrypcji. Aby uzyskać więcej informacji o limitach, przeczytaj artykuł dotyczący [limitów platformy Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#networking-limits).
> 

### <a name="create-a-public-ip-address-resource"></a><a name="create-public-ip"></a>Tworzenie zasobu publicznego adresu IP

Publiczny adres IP to jedno ustawienie zasobu publicznego adresu IP. Jeśli masz zasób publicznego adresu IP, który nie jest obecnie skojarzony z konfiguracją adresu IP, którą chcesz skojarzyć z konfiguracją adresu IP, pomiń poniższe kroki i wykonaj kroki w jednej z poniższych sekcji, zgodnie z ich wymaganiem. Jeśli nie masz dostępnego zasobu publicznego adresu IP, wykonaj następujące kroki, aby go utworzyć:

1. Przejdź do witryny Azure Portal i w razie potrzeby https://portal.azure.com zaloguj się do niego.
3. W portalu kliknij pozycję **Utwórz zasób**  >  **Publiczny**  >  **adres IP sieci.**
4. W **wyświetlonym okienku** Tworzenie publicznego adresu IP wprowadź nazwę , wybierz typ przypisania adresu **IP,** subskrypcję, grupę zasobów i lokalizację, a następnie kliknij pozycję **Utwórz,** jak pokazano na poniższej ilustracji:

    ![Tworzenie zasobu publicznego adresu IP](./media/virtual-network-multiple-ip-addresses-portal/figure5.png)

5. Wykonaj kroki opisane w jednej z poniższych sekcji, aby skojarzyć zasób publicznego adresu IP z konfiguracją adresu IP.

#### <a name="associate-the-public-ip-address-resource-to-a-new-ip-configuration"></a>Kojarzenie zasobu publicznego adresu IP z nową konfiguracją adresu IP

1. Wykonaj kroki opisane w [sekcji Podstawowe](#coreadd) kroki tego artykułu.
2. Kliknij pozycję **Dodaj**. W **wyświetlonym okienku Dodaj** konfigurację adresu IP utwórz konfigurację adresu IP o nazwie *IPConfig-4.* Włącz publiczny **adres IP i** wybierz istniejący dostępny zasób publicznego adresu IP w wyświetlonym okienku Wybierz publiczny adres **IP.**

    Po wybraniu zasobu publicznego adresu IP kliknij przycisk **OK** i okienko zostanie zamknięte. Jeśli nie masz istniejącego publicznego adresu IP, możesz go utworzyć, wykonując kroki opisane w sekcji Tworzenie zasobu publicznego adresu [IP](#create-public-ip) tego artykułu. 

3. Przejrzyj nową konfigurację adresu IP. Mimo że prywatny adres IP nie został jawnie przypisany, został on automatycznie przypisany do konfiguracji adresu IP, ponieważ wszystkie konfiguracje adresów IP muszą mieć prywatny adres IP.
4. Możesz kliknąć przycisk **Dodaj,** aby dodać dodatkowe konfiguracje adresów IP, lub zamknąć wszystkie otwarte blade, aby zakończyć dodawanie adresów IP.
5. Dodaj prywatny adres IP do systemu operacyjnego maszyny wirtualnej, wykonując kroki dla swojego systemu operacyjnego opisane w sekcji Dodawanie adresów [IP](#os-config) do systemu operacyjnego maszyny wirtualnej w tym artykule. Nie należy dodawać publicznego adresu IP do systemu operacyjnego.

#### <a name="associate-the-public-ip-address-resource-to-an-existing-ip-configuration"></a>Kojarzenie zasobu publicznego adresu IP z istniejącą konfiguracją adresu IP

1. Wykonaj kroki opisane w sekcji [Podstawowe](#coreadd) kroki tego artykułu.
2. Kliknij konfigurację adresu IP, do której chcesz dodać zasób publicznego adresu IP.
3. W wyświetlonym okienku IPConfig kliknij pozycję **Adres IP.**
4. W **wyświetlonym okienku Wybierz** publiczny adres IP wybierz publiczny adres IP.
5. Kliknij **przycisk Zapisz** i zamknij okienka. Jeśli nie masz istniejącego publicznego adresu IP, możesz go utworzyć, wykonując kroki opisane w sekcji Tworzenie zasobu publicznego adresu [IP](#create-public-ip) tego artykułu.
3. Przejrzyj nową konfigurację adresu IP.
4. Możesz kliknąć przycisk **Dodaj,** aby dodać dodatkowe konfiguracje adresów IP, lub zamknąć wszystkie otwarte blade, aby zakończyć dodawanie adresów IP. Nie należy dodawać publicznego adresu IP do systemu operacyjnego.

> [!NOTE]
> Po zmianie konfiguracji adresu IP należy ponownie uruchomić maszynę wirtualną, aby zmiany weszły w życie na maszynie wirtualnej.


[!INCLUDE [virtual-network-multiple-ip-addresses-os-config.md](../../includes/virtual-network-multiple-ip-addresses-os-config.md)]
