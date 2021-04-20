---
title: Łączenie identyfikatora partnera z kontami Power Apps przy użyciu poświadczeń platformy Azure
description: Ten artykuł pomaga partnerom firmy Microsoft używać poświadczeń platformy Azure, aby pomóc klientom w witrynie Microsoft Power Apps.
author: bandersmsft
ms.reviewer: akangaw
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: conceptual
ms.date: 03/16/2021
ms.author: banders
ms.openlocfilehash: acb22cc4b2a461e476131a83972db3e782425a39
ms.sourcegitcommit: 6f1aa680588f5db41ed7fc78c934452d468ddb84
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/19/2021
ms.locfileid: "107727712"
---
# <a name="link-a-partner-id-to-your-power-apps-accounts"></a>Łączenie identyfikatora partnera z kontami Power Apps partnera

Ten artykuł ułatwia partnerom firmy Microsoft, którzy są Power Apps dostawców usług, kojarzenie ich usług z klientami w usłudze Microsoft Power Apps. Gdy (partner firmy Microsoft) zarządzasz usługami klienta, konfigurujesz je i obsługujesz Power Apps, masz dostęp do środowiska klienta. Możesz użyć poświadczeń platformy Azure i linku administratora partnera, aby skojarzyć identyfikator sieci partnera z poświadczeniami konta używanymi do dostarczania usług.

Dostęp klienta umożliwia firmie Microsoft identyfikowanie i rozpoznawanie partnerów, którzy Power Apps klientów. Firma Microsoft przypisuje użycie do organizacji partnera na podstawie uprawnień konta (rola Power Apps) i zakresu (dzierżawa, grupa zasobów, zasób).

## <a name="get-access-from-your-customer"></a>Uzyskanie dostępu od klienta

Zanim połączysz swój identyfikator partnera, klient musi udzielić Ci dostępu do swoich zasobów Power Apps przy użyciu jednej z następujących opcji:

- **Użytkownik-gość** — klient może dodać Cię jako użytkownika-gościa i przypisać dowolne Power Apps ról. Aby uzyskać więcej informacji, zobacz temat [Add guest users from another directory](../../active-directory/external-identities/what-is-b2b.md) (Dodawanie użytkowników-gości z innego katalogu).
- **Konto katalogu** — klient może utworzyć dla Ciebie konto użytkownika we własnym katalogu i przypisać dowolną Power Apps użytkownika.
- **Jednostkę** usługi — klient może dodać aplikację lub skrypt z organizacji w swoim katalogu i przypisać dowolną Power Apps rolę. Tożsamość aplikacji lub skryptu jest znana jako nazwa główna usługi.
- **Administrator delegowany —** klient może delegować grupę zasobów, aby użytkownicy mogą nad nim pracować z poziomu dzierżawy. Aby uzyskać więcej informacji, zobacz [W przypadku partnerów: Administrator delegowany.](/power-platform/admin/for-partners-delegated-administrator)

## <a name="link-customer-to-a-partner-id"></a>Łączenie klienta z identyfikatorem partnera

Jeśli masz dostęp do zasobów klienta, użyj programu Azure Portal, programu PowerShell lub interfejsu wiersza polecenia platformy Azure, aby połączyć identyfikator Microsoft Partner Network (identyfikator MPN) z identyfikatorem użytkownika lub jednostką usługi. Połącz identyfikator partnera z każdą dzierżawą klienta.

### <a name="use-the-azure-portal-to-link-to-a-new-partner-id"></a>Użycie witryny Azure Portal do łączenia z nowym identyfikatorem partnera

1. Zaloguj się w witrynie [Azure Portal](https://portal.azure.com).
1. Przejdź do sekcji [Łączenie z identyfikatorem partnera](https://portal.azure.com/#blade/Microsoft_Azure_Billing/managementpartnerblade) w witrynie Azure Portal.
1. Wprowadź identyfikator [Microsoft Partner Network](https://partner.microsoft.com/) organizacji. Pamiętaj, aby użyć  **skojarzonego identyfikatora MPN wyświetlanego**  w profilu partnera.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/link-partner-id.png" alt-text="Zrzut ekranu przedstawiający okno Link do identyfikatora partnera." lightbox="./media/link-partner-id-power-apps-accounts/link-partner-id.png" :::
1. Aby połączyć identyfikator partnera z innym klientem, przełącz katalog. W **obszarze Przełącz katalog** wybierz odpowiedni katalog.  
    :::image type="content" source="./media/link-partner-id-power-apps-accounts/switch-directory.png" alt-text="Zrzut ekranu przedstawiający okno Katalog i subskrypcja, w którym można przełączyć katalog." lightbox="./media/link-partner-id-power-apps-accounts/switch-directory.png" :::

### <a name="use-powershell-to-link-to-a-new-partner-id"></a>Używanie programu PowerShell do łączenia z nowym identyfikatorem partnera

Zainstaluj [moduł Azure PowerShell Az.ManagementPartner.](https://www.powershellgallery.com/packages/Az.ManagementPartner/)

Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub jednostki usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się w programie Azure PowerShell](/powershell/azure/authenticate-azureps).

```azurepowershell-interactive
Update-AzManagementPartner -PartnerId 12345
```

Połącz się z nowym identyfikatorem partnera. Identyfikator partnera to identyfikator programu [Microsoft Partner Network](https://partner.microsoft.com/) dla organizacji. Pamiętaj, aby użyć **skojarzonego identyfikatora MPN wyświetlanego**  w profilu partnera.

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

Zaloguj się do dzierżawy klienta przy użyciu konta użytkownika lub jednostki usługi. Aby uzyskać więcej informacji, zobacz temat [Logowanie się za pomocą interfejsu wiersza polecenia platformy Azure](/cli/azure/authenticate-azure-cli).

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

### <a name="next-steps"></a>Następne kroki

- Przeczytaj często [zadawane Cost Management + Billing odpowiedzi na](../cost-management-billing-faq.yml) pytania i odpowiedzi dotyczące łączenia identyfikatora partnera z Power Apps kontami.
- Dołącz do dyskusji w [społeczności partnerów firmy Microsoft](https://aka.ms/PALdiscussion), aby otrzymywać aktualizacje lub wysłać opinię.
- Przeczytaj artykuł [Low Code Application Development advanced specialization FAQ](https://assetsprod.microsoft.com/mpn/faq-low-code-app-development-advanced-specialization.pdf) for PAL-based Power Apps association for Low code application development advanced specialization (Często zadawane pytania dotyczące zaawansowanej specjalizacji w zakresie tworzenia aplikacji o niskiej kodzie dla skojarzenia Power Apps palowego).
