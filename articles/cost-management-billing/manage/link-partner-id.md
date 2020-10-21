---
title: Łączenie konta platformy Azure z identyfikatorem partnera
description: Śledź kontakty z klientami platformy Azure, łącząc identyfikator partnera z kontem użytkownika, którego używasz do zarządzania zasobami klienta.
author: dhirajgandhi
ms.reviewer: dhgandhi
ms.author: banders
ms.date: 10/05/2020
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.openlocfilehash: 96b6467d0d529f5839c33182057f3aa3c39cb6e7
ms.sourcegitcommit: 33368ca1684106cb0e215e3280b828b54f7e73e8
ms.translationtype: HT
ms.contentlocale: pl-PL
ms.lasthandoff: 10/16/2020
ms.locfileid: "92132554"
---
# <a name="link-a-partner-id-to-your-azure-accounts"></a>Łączenie identyfikatora partnera z kontami platformy Azure

Partnerzy firmy Microsoft oferują usługi, które pomagają klientom osiągać cele biznesowe i ogólne przy użyciu produktów firmy Microsoft. Podejmując działania w imieniu klienta podczas konfigurowania i obsługiwania usług platformy Azure oraz zarządzania nimi, użytkownicy partnera będą musieli uzyskać dostęp do środowiska klienta. Korzystając z linku administratora partnera, partnerzy mogą powiązać identyfikator sieci partnera z poświadczeniami używanymi do dostarczania usług.

Link administratora partnera pozwala firmie Microsoft identyfikować i rozpoznawać partnerów, którzy prowadzą do sukcesu klientów platformy Azure. Firma Microsoft może przypisywać wpływ i przychody użyte przez platformę Azure w organizacji na podstawie uprawnień konta (rola na platformie Azure) i zakresu (subskrypcja, grupa zasobów, zasób).

## <a name="get-access-from-your-customer"></a>Uzyskanie dostępu od klienta

Przed połączeniem identyfikatora partnera klient musi zapewnić dostęp do swoich zasobów platformy Azure, korzystając z jednego z następujących rozwiązań:

- **Użytkownik-gość**: Klient może dodać Cię jako użytkownika-gościa i przypisać dowolne role na platformie Azure. Aby uzyskać więcej informacji, zobacz temat [Add guest users from another directory](../../active-directory/external-identities/what-is-b2b.md) (Dodawanie użytkowników-gości z innego katalogu).

- **Konto katalogu**: Klient może utworzyć dla Ciebie konto użytkownika w swoim własnym katalogu i przypisać dowolną rolę na platformie Azure.

- **Jednostka usługi**: Klient może dodać aplikację lub skrypt z organizacji w swoim katalogu i przypisać dowolną rolę na platformie Azure. Tożsamość aplikacji lub skryptu jest znana jako nazwa główna usługi.

- **Azure Lighthouse**: Klient może delegować subskrypcję (lub grupę zasobów), aby Twoi użytkownicy mogli z niej korzystać z poziomu Twojej dzierżawy. Aby uzyskać więcej informacji, zobacz [Zarządzanie zasobami delegowanymi na platformie Azure](../../lighthouse/concepts/azure-delegated-resource-management.md).

## <a name="link-to-a-partner-id"></a>Łączenie z identyfikatorem partnera

Jeśli masz dostęp do zasobów klienta, użyj witryny Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby połączyć identyfikator MPN (Microsoft Partner Network) z identyfikatorem użytkownika lub nazwą główną usługi. Połącz identyfikator partnera w każdej dzierżawie klienta.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Użycie witryny Azure Portal do łączenia z nowym identyfikatorem partnera

1. Przejdź do sekcji [Łączenie z identyfikatorem partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) w witrynie Azure Portal.

2. Zaloguj się do Portalu Azure.

3. Wprowadź identyfikator partnera firmy Microsoft. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji. Pamiętaj, aby użyć wartości **Skojarzony identyfikator MPN** wyświetlanej w profilu partnera.

   ![Zrzut ekranu przedstawiający sekcję Łączenie z identyfikatorem partnera](./media/link-partner-id/link-partner-id01.png)

4. Aby połączyć identyfikator partnera innego klienta, przełącz katalog. W obszarze **Przełącz katalog** wybierz swój katalog.

   ![Zrzut ekranu przedstawiający obszar Przełącz katalog](./media/link-partner-id/directory-switcher.png)

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Używanie programu PowerShell do łączenia z nowym identyfikatorem partnera

1. Zainstaluj moduł [Az.ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) programu PowerShell.

2. Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się w programie Azure PowerShell](/powershell/azure/authenticate-azureps).

   ```azurepowershell-interactive
    C:\> Connect-AzAccount -TenantId XXXXXXXX-XXXX-XXXX-XXXX-XXXXXXXXXXXX
   ```

3. Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji. Pamiętaj, aby użyć wartości **Skojarzony identyfikator MPN** wyświetlanej w profilu partnera.


    ```azurepowershell-interactive
    C:\> new-AzManagementPartner -PartnerId 12345
    ```

#### <a name="get-the-linked-partner-id"></a>Pobieranie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> get-AzManagementPartner
```

#### <a name="update-the-linked-partner-id"></a>Aktualizowanie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> Update-AzManagementPartner -PartnerId 12345
```
#### <a name="delete-the-linked-partner-id"></a>Usuwanie połączonego identyfikatora partnera
```azurepowershell-interactive
C:\> remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure do połączenia z nowym identyfikatorem partnera
1. Zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

    ```azurecli-interactive
    C:\ az extension add --name managementpartner
    ```

2. Zaloguj się do dzierżawy klienta, używając konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

    ```azurecli-interactive
    C:\ az login --tenant <tenant>
    ```

3. Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji.

     ```azurecli-interactive
     C:\ az managementpartner create --partner-id 12345
      ```  

#### <a name="get-the-linked-partner-id"></a>Pobieranie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner show
```

#### <a name="update-the-linked-partner-id"></a>Aktualizowanie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner update --partner-id 12345
```

#### <a name="delete-the-linked-partner-id"></a>Usuwanie połączonego identyfikatora partnera
```azurecli-interactive
C:\ az managementpartner delete --partner-id 12345
```

## <a name="next-steps"></a>Następne kroki

Dołącz do dyskusji w [społeczności partnerów firmy Microsoft](https://aka.ms/PALdiscussion), aby otrzymywać aktualizacje lub wysłać opinię.

## <a name="frequently-asked-questions"></a>Często zadawane pytania

**Kto może połączyć identyfikator partnera?**

Identyfikator partnera z kontem może połączyć każdy użytkownik z organizacji partnerskiej, który zarządza zasobami platformy Azure klienta.

**Czy identyfikator partnera można zmienić po jego połączeniu?**

Tak. Połączony identyfikator partnera można zmienić, dodać lub usunąć.

**Co należy zrobić, jeśli użytkownik ma konto w więcej niż jednej dzierżawie klienta?**

Połączenie między identyfikatorem partnera i kontem jest wykonywane dla każdej dzierżawy klienta. Połącz identyfikator partnera w każdej dzierżawie klienta.

Jeśli jednak zarządzasz zasobami klienta za pomocą usługi Azure Lighthouse, musisz utworzyć link w dzierżawie dostawcy usług za pomocą konta z dostępem do zasobów klienta. Aby uzyskać więcej informacji, zobacz [Łączenie identyfikatora partnera w celu śledzenia wpływu na delegowane zasoby](../../lighthouse/how-to/partner-earned-credit.md).

**Czy inni partnerzy lub klienci mogą edytować lub usunąć hiperlink do identyfikatora partnera?**

Hiperlink jest skojarzony na poziomie konta użytkownika. Tylko Ty możesz edytować lub usunąć hiperlink do identyfikatora partnera. Klient i inni partnerzy nie mogą zmienić hiperlinku do identyfikatora partnera.

**Którego identyfikatora MPN należy użyć, jeśli moja firma ma ich wiele?**

Pamiętaj, aby użyć wartości **Skojarzony identyfikator MPN** wyświetlanej w profilu partnera.

**Gdzie można znaleźć uwzględnione raportowanie przychodów dla połączonego identyfikatora partnera?**

Raportowanie wydajności produktu w chmurze jest dostępne dla partnerów na [pulpicie nawigacyjnym Moje usługi Insights](https://partner.microsoft.com/membership/reports/myinsights) w Centrum partnerskim. Jako typ powiązania partnera należy wybrać hiperlink administratora partnera.

**Dlaczego nie widzę mojego klienta w raportach?**

Klient może być niewidoczny w raportach z następujących powodów:

1. Połączone konto użytkownika nie ma dostępu do [kontroli dostępu na podstawie ról (RBAC) platformy Azure](../../role-based-access-control/overview.md) w żadnej subskrypcji ani zasobie klienta platformy Azure.

2. Subskrypcja platformy Azure, w której użytkownik ma dostęp do [kontroli dostępu na podstawie ról (RBAC) platformy Azure](../../role-based-access-control/overview.md), nie ma żadnego użycia.

**Czy identyfikator partnera hiperlinku współdziała z usługą Azure Stack?**

Tak. Identyfikator partnera można połączyć z usługą Azure Stack.

**Jak mogę połączyć swój identyfikator partnera, jeśli moja firma używa rozwiązania [Azure Lighthouse](../../lighthouse/overview.md) w celu uzyskania dostępu do zasobów klienta?**

Jeśli dołączasz klientów do zarządzania zasobami delegowanymi na platformie Azure przez [opublikowanie oferty usług zarządzanych w witrynie Azure Marketplace](../../lighthouse/how-to/publish-managed-services-offers.md), Twój identyfikator MPN zostanie automatycznie skojarzony.

Jeśli [dołączasz klientów przez wdrożenie szablonów usługi Azure Resource Manager](../../lighthouse/how-to/onboard-customer.md), musisz skojarzyć swój identyfikator MPN z co najmniej jednym kontem użytkownika, które ma dostęp do każdej z dołączonych subskrypcji. Musisz pamiętać, że należy to zrobić w dzierżawie dostawcy usług, a nie u każdego klienta. Dla uproszczenia zalecamy utworzenie konta jednostki usługi w dzierżawie, które będzie skojarzone z identyfikatorem MPN, i przyznanie mu dostępu do każdego dołączonego klienta za pomocą [wbudowanej roli platformy Azure, która kwalifikuje się do otrzymania środków uzyskanych przez partnerów](/partner-center/azure-roles-perms-pec). Aby uzyskać więcej informacji, zobacz [Łączenie identyfikatora partnera w celu śledzenia wpływu na delegowane zasoby](../../lighthouse/how-to/partner-earned-credit.md).

**Jak mogę wyjaśnić link administratora partnera mojemu klientowi?**

Link administratora partnera pozwala firmie Microsoft identyfikować i rozpoznawać partnerów, którzy pomagają klientom osiągnąć cele biznesowe i zrealizować wartość w chmurze. Klienci muszą najpierw zapewnić dostęp partnera do ich zasobów platformy Azure. Po udzieleniu dostępu identyfikator Microsoft Partner Network partnera (identyfikator MPN) zostaje skojarzony. To skojarzenie pomaga firmie Microsoft zrozumieć ekosystem dostawców usług IT oraz udoskonalać narzędzia i programy niezbędne do najlepszego wsparcia naszych wspólnych klientów.

**Jakie dane są zbierane przez link administratora partnera?**

Skojarzenie linku administratora partnera z istniejącymi poświadczeniami nie udostępnia żadnych nowych danych klienta firmie Microsoft. Po prostu udostępnia dane telemetryczne firmie Microsoft, gdy partner aktywnie uczestniczy w środowisku platformy Azure klienta. Firma Microsoft może przypisywać wpływ i przychody użyte przez platformę Azure ze środowiska klienta do organizacji partnera na podstawie uprawnień konta (rola na platformie Azure) i zakresu (grupa zarządzania, subskrypcja, grupa zasobów, zasób) udostępnionego partnerowi przez klienta. 

**Czy ma to wpływ na bezpieczeństwo środowiska platformy Azure klienta?**

Skojarzenie linku administratora partnera dodaje tylko identyfikator MPN partnera do już ustanowionych poświadczeń i nie zmienia żadnych uprawnień (roli na platformie Azure) ani nie udostępnia dodatkowych danych usługi platformy Azure partnerowi lub firmie Microsoft.