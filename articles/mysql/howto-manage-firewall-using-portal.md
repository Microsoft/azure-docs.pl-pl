---
title: Zarządzanie regułami zapory — Azure Portal — Azure Database for MySQL
description: Tworzenie reguł zapory Azure Database for MySQL i zarządzanie nimi za pomocą Azure Portal
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: 032db324f9dd2f0d6a5dce5c4fd5c64342fed59f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 11/12/2020
ms.locfileid: "94540488"
---
# <a name="create-and-manage-azure-database-for-mysql-firewall-rules-by-using-the-azure-portal"></a>Tworzenie reguł zapory Azure Database for MySQL i zarządzanie nimi za pomocą Azure Portal
Reguły zapory na poziomie serwera mogą służyć do zarządzania dostępem do serwera Azure Database for MySQL z określonego adresu IP lub zakresu adresów IP. 

Reguły Virtual Network (VNet) mogą również służyć do zabezpieczania dostępu do serwera. Dowiedz się więcej o [tworzeniu i zarządzaniu Virtual Network punktów końcowych usługi i reguł przy użyciu Azure Portal](howto-manage-vnet-using-portal.md).

## <a name="create-a-server-level-firewall-rule-in-the-azure-portal"></a>Tworzenie reguły zapory na poziomie serwera w witrynie Azure Portal

1. Na stronie serwer MySQL w obszarze Ustawienia kliknij pozycję **zabezpieczenia połączeń** , aby otworzyć stronę zabezpieczenia połączenia dla Azure Database for MySQL.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/1-connection-security.png" alt-text="Azure Portal kliknij pozycję zabezpieczenia połączeń":::

2. Kliknij pozycję **Dodaj mój adres IP** na pasku narzędzi. Spowoduje to automatyczne utworzenie reguły zapory z publicznym adresem IP komputera, tak jak to zostało postrzegane przez system Azure.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/2-add-my-ip.png" alt-text="Azure Portal — kliknij pozycję Dodaj mój adres IP":::

3. Sprawdź swój adres IP przed zapisaniem konfiguracji. W niektórych sytuacjach adres IP zaobserwowany przez Azure Portal różni się od adresu IP używanego podczas uzyskiwania dostępu do Internetu i serwerów platformy Azure. W związku z tym może zajść potrzeba zmiany początkowego adresu IP i końcowego adresu IP, aby zapewnić działanie reguły zgodnie z oczekiwaniami.

   Użyj wyszukiwarki lub innego narzędzia online do sprawdzenia własnego adresu IP. Na przykład wyszukaj ciąg "jaki jest mój adres IP".

4. Dodaj dodatkowe zakresy adresów. W regułach zapory dla Azure Database for MySQL można określić pojedynczy adres IP lub zakres adresów. Jeśli chcesz ograniczyć regułę do pojedynczego adresu IP, wpisz ten sam adres w polach początkowy adres IP i końcowy adres IP. Otwarcie zapory umożliwia administratorom, użytkownikom i aplikacjom dostęp do dowolnej bazy danych na serwerze MySQL, do której mają prawidłowe poświadczenia.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/4-specify-addresses.png" alt-text="Azure Portal — reguły zapory":::

5. Kliknij przycisk **Zapisz** na pasku narzędzi, aby zapisać tę regułę zapory na poziomie serwera. Poczekaj na potwierdzenie, że aktualizacja reguł zapory zakończyła się pomyślnie.

   :::image type="content" source="./media/howto-manage-firewall-using-portal/5-save-firewall-rule.png" alt-text="Azure Portal kliknij pozycję Zapisz":::

## <a name="connecting-from-azure"></a>Łączenie z platformy Azure
Aby umożliwić aplikacjom z platformy Azure Łączenie się z serwerem Azure Database for MySQL, należy włączyć połączenia platformy Azure. Na przykład w celu hostowania aplikacji Web Apps platformy Azure lub aplikacji działającej na maszynie wirtualnej platformy Azure lub w celu nawiązania połączenia z Azure Data Factory bramy zarządzania danymi. Zasoby nie muszą znajdować się w tej samej Virtual Network (VNet) lub grupie zasobów dla reguły zapory w celu włączenia tych połączeń. Gdy aplikacja platformy Azure próbuje połączyć się z serwerem bazy danych, zapora sprawdza, czy połączenia platformy Azure są dozwolone. Istnieje kilka metod włączania tych typów połączeń. Ustawienie zapory z początkowym i końcowym adresem równym 0.0.0.0 wskazuje, że te połączenia są dozwolone. Alternatywnie możesz ustawić opcję Zezwalaj na **dostęp do usług platformy Azure** **w portalu** w okienku **zabezpieczenia połączenia** i kliknąć przycisk **Zapisz**. Jeśli próba połączenia nie jest dozwolona, żądanie nie dociera do serwera Azure Database for MySQL.

> [!IMPORTANT]
> Ta opcja konfiguruje zaporę w celu zezwalania na wszystkie połączenia z platformy Azure, w tym połączenia z subskrypcji innych klientów. W przypadku wybrania tej opcji upewnij się, że uprawnienia logowania i użytkownika zezwalają na dostęp tylko uprawnionym użytkownikom.
> 

## <a name="manage-existing-server-level-firewall-rules-by-using-the-azure-portal"></a>Zarządzanie istniejącymi regułami zapory na poziomie serwera przy użyciu Azure Portal
Powtórz kroki, aby zarządzać regułami zapory.
* Aby dodać bieżący komputer, kliknij przycisk **+ Dodaj mój adres IP**. Kliknij przycisk **Zapisz** , aby zapisać zmiany.
* Aby dodać dodatkowe adresy IP, wpisz **nazwę reguły** , **początkowy adres IP** i **końcowy adres IP**. Kliknij przycisk **Zapisz** , aby zapisać zmiany.
* Aby zmodyfikować istniejącą regułę, kliknij dowolne pole w regule, a następnie zmodyfikuj. Kliknij przycisk **Zapisz** , aby zapisać zmiany.
* Aby usunąć istniejącą regułę, kliknij przycisk wielokropka [...], a następnie kliknij przycisk **Usuń**. Kliknij przycisk **Zapisz** , aby zapisać zmiany.


## <a name="next-steps"></a>Następne kroki
- Podobnie można utworzyć skrypt do [tworzenia reguł zapory Azure Database for MySQL i zarządzania nimi za pomocą interfejsu wiersza polecenia platformy Azure](howto-manage-firewall-using-cli.md).
- Aby zapewnić bardziej bezpieczny dostęp do serwera, można [utworzyć i zarządzać Virtual Network punktami końcowymi usługi i regułami przy użyciu Azure Portal](howto-manage-vnet-using-portal.md).
- Aby uzyskać pomoc w połączeniu z serwerem Azure Database for MySQL, zobacz [biblioteki połączeń dla Azure Database for MySQL](./concepts-connection-libraries.md).                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                                 
