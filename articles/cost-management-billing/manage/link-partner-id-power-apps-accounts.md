---
title: Łączenie identyfikatora partnera z kontami aplikacji zaawansowanych z poświadczeniami platformy Azure
description: Ten artykuł ułatwia partnerom firmy Microsoft korzystanie z poświadczeń platformy Azure w celu ułatwienia klientom korzystania z aplikacji Microsoft PowerShell.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: adaff7a6b8559fe9604412a44eced6c490231e3c
ms.sourcegitcommit: 27cd3e515fee7821807c03e64ce8ac2dd2dd82d2
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/16/2021
ms.locfileid: "103603831"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Łączenie identyfikatora partnera z kontami aplikacji zaawansowanych

Ten artykuł pomaga partnerom firmy Microsoft, którzy są dostawcami usługi aplikacji zaawansowanych, skojarzyć ich usługę z klientami w usłudze Microsoft PowerShell. Gdy użytkownik (partner firmy Microsoft) zarządza, konfiguruje i obsługuje usługi aplikacji zaawansowanych dla klienta, masz dostęp do środowiska klienta. Możesz użyć poświadczeń platformy Azure i linku administratora partnera (PAL), aby skojarzyć identyfikator sieci partnera z poświadczeniami konta używanymi do dostarczania usług.

PAL pozwala firmie Microsoft identyfikować i rozpoznawać partnerów, którzy mają klientów korzystających z aplikacji zaawansowanych. Użycie atrybutów firmy Microsoft w organizacji partnera na podstawie uprawnień konta (rola aplikacji zaawansowanych) i zakresu (dzierżawy, Grupa zasobów, zasób).

## <a name="get-access-from-your-customer"></a>Uzyskanie dostępu od klienta

Przed połączeniem identyfikatora partnera Klient musi zapewnić dostęp do zasobów aplikacji zaawansowanych za pomocą jednej z następujących opcji:

- **Użytkownik-Gość** — klient może dodać Cię jako użytkownika-gościa i przypisać do nich role aplikacji zaawansowanych. Aby uzyskać więcej informacji, zobacz temat [Add guest users from another directory](../../active-directory/external-identities/what-is-b2b.md) (Dodawanie użytkowników-gości z innego katalogu).
- **Konto katalogu** — Twój klient może utworzyć konto użytkownika w swoim własnym katalogu i przypisać dowolną rolę aplikacji zaawansowanych.
- Nazwa **główna usługi** — klient może dodać do swojej organizacji aplikację lub skrypt, a następnie przypisać dowolną rolę usługi. Tożsamość aplikacji lub skryptu jest znana jako nazwa główna usługi.
- **Administrator delegowany** — Twój klient może delegować grupę zasobów, aby użytkownicy mogli z niej korzystać. Aby uzyskać więcej informacji, zobacz [dla partnerów: Administrator delegowany](/power-platform/admin/for-partners-delegated-administrator).

## <a name="link-customer-to-a-partner-id"></a>Połącz klienta z IDENTYFIKATORem partnera

Jeśli masz dostęp do zasobów klienta, użyj Azure Portal, PowerShell lub interfejsu wiersza polecenia platformy Azure, aby połączyć identyfikator Microsoft Partner Network (MPN ID) z IDENTYFIKATORem użytkownika lub jednostką usługi. Połącz identyfikator partnera z każdą dzierżawą klienta.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Użycie witryny Azure Portal do łączenia z nowym identyfikatorem partnera

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do sekcji [Łączenie z identyfikatorem partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) w witrynie Azure Portal.
1. Wprowadź identyfikator [Microsoft Partner Network](https://partner.microsoft.com/) dla swojej organizacji. Upewnij się, że używasz  **SKOJARZONEGO identyfikatora MPN**  pokazanego w profilu partnera.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Zrzut ekranu przedstawiający link do okna identyfikatora partnera." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Aby połączyć swój identyfikator partnera z innym klientem, przełącz katalog. W obszarze **Przełącz katalog** wybierz odpowiedni katalog.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Zrzut ekranu przedstawiający okno katalog + subskrypcja, w którym można zmienić katalog." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Używanie programu PowerShell do łączenia z nowym identyfikatorem partnera

Zainstaluj moduł Azure PowerShell [AZ. ManagementPartner](https://www.powershellgallery.com/packages/Az.ManagementPartner/) .

Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się w programie Azure PowerShell](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji. Upewnij się, że używasz **SKOJARZONEGO identyfikatora MPN**  pokazanego w profilu partnera.

```azurepowershell-interactive
new-AzManagementPartner -PartnerId 12345
```

Pobieranie połączonego identyfikatora partnera

```azurepowershell-interactive
get-AzManagementPartner
```

Aktualizowanie połączonego identyfikatora partnera

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Usuwanie połączonego identyfikatora partnera

```azurepowershell-interactive
remove-AzManagementPartner -PartnerId 12345
```

### <a name="use-the-azure-cli-to-link-to-a-new-partner-id"></a>Korzystanie z interfejsu wiersza polecenia platformy Azure do połączenia z nowym identyfikatorem partnera

Najpierw zainstaluj rozszerzenie interfejsu wiersza polecenia platformy Azure.

```azurecli-interactive
az extension add --name managementpartner
```

Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub nazwy głównej usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

```azurecli-interactive
az login --tenant TenantName
```

Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji.

```azurecli-interactive
az managementpartner create --partner-id 12345
```

Pobieranie połączonego identyfikatora partnera

```azurecli-interactive
az managementpartner show
```

Aktualizowanie połączonego identyfikatora partnera

```azurecli-interactive
az managementpartner update --partner-id 12345
```

Usuwanie połączonego identyfikatora partnera

```azurecli-interactive
az managementpartner delete --partner-id 12345
```

## <a name="frequently-asked-questions-faq"></a>Często zadawane pytania

W poniższych sekcjach opisano często zadawane pytania dotyczące łączenia identyfikatora partnera z kontami aplikacji zaawansowanych.

### <a name="who-should-link-the-partner-id"></a>Kto powinien połączyć identyfikator partnera?

Każdy użytkownik z organizacji partnerskiej, który współpracuje z zasobami aplikacji zaawansowanych klienta, może połączyć identyfikator partnera z kontem. W idealnym przypadku skojarzenie w wersji PAL należy wykonać na początku projektu. Jednak można je wykonać zawsze, gdy masz dostęp do katalogu klienta.

### <a name="can-a-partner-id-be-changed-after-its-linked"></a>Czy identyfikator partnera można zmienić po jego połączeniu?

Tak. Połączony identyfikator partnera można zmienić, dodać lub usunąć. Przykładem takiej sytuacji może być sytuacja, w której pracownik od firmy opuści Twoją organizację. Innym przykładem może być sytuacja, gdy projekt lub kontrakt zostanie zakończony przez klienta.

### <a name="what-if-a-user-has-an-account-in-more-than-one-customer-tenant"></a>Co należy zrobić, jeśli użytkownik ma konto w więcej niż jednej dzierżawie klienta?

Połączenie między identyfikatorem partnera i kontem jest wykonywane dla każdej dzierżawy klienta. Połącz identyfikator partnera w każdej dzierżawie klienta.

### <a name="can-other-partners-or-customers-edit-or-remove-the-link-to-the-partner-id"></a>Czy inni partnerzy lub klienci mogą edytować lub usunąć hiperlink do identyfikatora partnera?

Hiperlink jest skojarzony na poziomie konta użytkownika. Tylko Ty możesz edytować lub usunąć hiperlink do identyfikatora partnera. Klient i inni partnerzy nie mogą zmienić hiperlinku do identyfikatora partnera.

### <a name="which-mpn-id-should-i-use-if-my-company-has-multiple"></a>Którego identyfikatora MPN należy użyć, jeśli moja firma ma ich wiele?

Pamiętaj, aby użyć wartości **Skojarzony identyfikator MPN** wyświetlanej w profilu partnera. Zwykle jest to skojarzenie identyfikatora konta lokalnego z Twoją organizacją.

### <a name="how-do-i-explain-pal-to-my-customer"></a>Jak mogę wyjaśnić PAL dla mojego klienta?

PAL pozwala firmie Microsoft identyfikować i rozpoznawać partnerów, którzy pomagają klientom osiągać cele biznesowe i realizować wartości w chmurze. Klienci muszą najpierw zapewnić dostęp partnerów do zasobów aplikacji zaawansowanych. Po udzieleniu dostępu zostanie skojarzony identyfikator Microsoft Partner Network partnera (identyfikator MPN). To skojarzenie pomaga firmie Microsoft zrozumieć dostawców usług i uściślić narzędzia i programy, które są konieczne w celu uzyskania najlepszych klientów pomocy technicznej.

### <a name="what-data-does-pal-collect"></a>Jakie dane są zbierane przez link administratora partnera?

Skojarzenie linku administratora partnera z istniejącymi poświadczeniami nie udostępnia żadnych nowych danych klienta firmie Microsoft. Zawiera informacje dla firmy Microsoft, w przypadku których Partner aktywnie uczestniczy w środowiskach aplikacji zaawansowanych klienta. Firma Microsoft może porównać użycie i wpływ na środowisko klienta w organizacji partnerskiej na podstawie uprawnień konta (rola usługi) i zakresu (dzierżawy, grupy zasobów, zasobu) dostarczonego dla partnera przez klienta.

### <a name="does-pal-association-affect-the-security-of-a-customers-power-apps-environment"></a>Czy skojarzenie PAL ma wpływ na bezpieczeństwo środowiska aplikacji zaawansowanych klienta?

Skojarzenie PAL dodaje tylko identyfikator MPN partnera do poświadczenia, które już zainicjowano. Nie zmienia żadnych uprawnień (rola usługi) ani nie udostępnia żadnych danych usługi do partnera lub firmy Microsoft.

### <a name="next-steps"></a>Następne kroki

- Dołącz do dyskusji w [społeczności partnerów firmy Microsoft](https://aka.ms/PALdiscussion), aby otrzymywać aktualizacje lub wysłać opinię.
- Przeczytaj [często zaawansowaną specjalizację opracowywania aplikacji](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) , aby uzyskać informacje na temat skojarzenia dla aplikacji zaawansowanych opartych na standardach.
