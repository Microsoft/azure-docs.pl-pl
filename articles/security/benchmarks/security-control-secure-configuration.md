---
title: Kontrola zabezpieczeń platformy Azure — bezpieczna konfiguracja
description: Bezpieczna konfiguracja kontroli zabezpieczeń platformy Azure
author: msmbaldwin
ms.service: security
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: mbaldwin
ms.custom: security-benchmark
ms.openlocfilehash: 981e13e772ee21e0e4a680e6d07b4ad4892a7cee
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/16/2020
ms.locfileid: "81417067"
---
# <a name="security-control-secure-configuration"></a>Kontrola bezpieczeństwa: bezpieczna konfiguracja

Ustanawiaj, wdrażaj i aktywnie zarządzaj (śledź, raportuj, popraw) konfigurację zabezpieczeń zasobów platformy Azure, aby uniemożliwić osobom atakującym wykorzystywanie zagrożonych usług i ustawień.

## <a name="71-establish-secure-configurations-for-all-azure-resources"></a>7.1: Ustanawianie bezpiecznych konfiguracji dla wszystkich zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.1 | 5.1 | Klient |

Aliasy zasad platformy Azure umożliwia tworzenie zasad niestandardowych w celu inspekcji lub wymuszenia konfiguracji zasobów platformy Azure. Można również użyć wbudowanych definicji zasad platformy Azure.

Ponadto usługa Azure Resource Manager ma możliwość eksportowania szablonu w języku JavaScript Object Notation (JSON), który powinien zostać sprawdzony, aby upewnić się, że konfiguracje spełniają / przekraczają wymagania dotyczące zabezpieczeń dla twojej organizacji.

Można również użyć zaleceń z usługi Azure Security Center jako linii bazowej bezpiecznej konfiguracji dla zasobów platformy Azure.

- [Jak wyświetlić dostępne aliasy zasad platformy Azure](https://docs.microsoft.com/powershell/module/az.resources/get-azpolicyalias?view=azps-3.3.0)

- [Samouczek: Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Eksport pojedynczych i wielozasóbowych do szablonu w witrynie Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/templates/export-template-portal)

- [Zalecenia dotyczące zabezpieczeń - przewodnik referencyjny](https://docs.microsoft.com/azure/security-center/recommendations-reference)

## <a name="72-establish-secure-operating-system-configurations"></a>7.2: Ustanawianie bezpiecznych konfiguracji systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.2 | 5.1 | Klient |

Użyj zaleceń usługi Azure Security Center, aby zachować konfiguracje zabezpieczeń na wszystkich zasobach obliczeniowych.  Ponadto można użyć niestandardowych obrazów systemu operacyjnego lub konfiguracji stanu usługi Azure Automation State w celu ustanowienia konfiguracji zabezpieczeń systemu operacyjnego wymaganego przez organizację.

- [Jak monitorować zalecenia usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-recommendations)

- [Zalecenia dotyczące zabezpieczeń - przewodnik referencyjny](https://docs.microsoft.com/azure/security-center/recommendations-reference)

- [Omówienie konfiguracji stanu automatyzacji platformy Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Przekazywanie dysku wirtualnego i używanie jej do tworzenia nowych maszyn wirtualnych z systemem Windows na platformie Azure](https://docs.microsoft.com/azure/virtual-machines/windows/upload-generalized-managed)

- [Tworzenie maszyny Wirtualnej systemu Linux na dysku niestandardowym za pomocą interfejsu wiersza polecenia platformy Azure](https://docs.microsoft.com/azure/virtual-machines/linux/upload-vhd)

## <a name="73-maintain-secure-azure-resource-configurations"></a>7.3: Obsługa bezpiecznych konfiguracji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.3 | 5.2 | Klient |

Użyj zasad platformy Azure [odmów] i [wdrożyć, jeśli nie istnieje], aby wymusić bezpieczne ustawienia w zasobach platformy Azure.  Ponadto można użyć szablonów usługi Azure Resource Manager do obsługi konfiguracji zabezpieczeń zasobów platformy Azure wymaganych przez organizację. 

- [Opis efektów zasad platformy Azure](https://docs.microsoft.com/azure/governance/policy/concepts/effects)

- [Tworzenie zasad i zarządzanie nimi w celu wymuszania zgodności](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Omówienie szablonów usługi Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/templates/overview)

## <a name="74-maintain-secure-operating-system-configurations"></a>7.4: Obsługa bezpiecznych konfiguracji systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.4 | 5.2 | Udostępnione |

Postępuj zgodnie z zaleceniami usługi Azure Security Center dotyczącymi przeprowadzania ocen luk w zabezpieczeniach zasobów obliczeniowych platformy Azure.  Ponadto można użyć szablonów usługi Azure Resource Manager, obrazów niestandardowych systemów operacyjnych lub konfiguracji stanu usługi Azure Automation State, aby zachować konfigurację zabezpieczeń systemu operacyjnego wymaganą przez organizację.   Szablony maszyn wirtualnych firmy Microsoft w połączeniu z konfiguracją żądanego stanu automatyzacji usługi Azure może pomóc w spełnianiu i utrzymywaniu wymagań dotyczących zabezpieczeń. 

Należy również pamiętać, że obrazy maszyn wirtualnych w portalu Azure Marketplace opublikowane przez firmę Microsoft są zarządzane i obsługiwane przez firmę Microsoft. 

- [Jak zaimplementować zalecenia dotyczące oceny luk w zabezpieczeniach usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-vulnerability-assessment-recommendations)

- [Jak utworzyć maszynę wirtualną platformy Azure na podstawie szablonu usługi Azure Resource Manager](https://docs.microsoft.com/azure/virtual-machines/windows/ps-template)

- [Omówienie konfiguracji stanu automatyzacji platformy Azure](https://docs.microsoft.com/azure/automation/automation-dsc-overview)

- [Tworzenie maszyny wirtualnej systemu Windows w witrynie Azure portal](https://docs.microsoft.com/azure/virtual-machines/windows/quick-create-portal)

- [Informacje o pobieraniu szablonu maszyny Wirtualnej](https://docs.microsoft.com/azure/virtual-machines/windows/download-template)

- [Przykładowy skrypt do przekazania wirtualnego dysku twardego na platformę Azure i utworzenia nowej maszyny wirtualnej](https://docs.microsoft.com/azure/virtual-machines/scripts/virtual-machines-windows-powershell-upload-generalized-script)

## <a name="75-securely-store-configuration-of-azure-resources"></a>7.5: Bezpieczne przechowywanie konfiguracji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7,5 | 5.3 | Klient |

Użyj usługi Azure DevOps, aby bezpiecznie przechowywać kod i zarządzać nim, takich jak niestandardowe zasady platformy Azure, szablony usługi Azure Resource Manager i skrypty konfiguracji żądanego stanu. Aby uzyskać dostęp do zasobów, którymi zarządzasz w usłudze Azure DevOps, można udzielić lub odmówić uprawnień określonym użytkownikom, wbudowanym grupom zabezpieczeń lub grupom zdefiniowanym w usłudze Azure Active Directory (Azure AD), jeśli są zintegrowane z usługą Azure DevOps lub usługą Active Directory, jeśli są zintegrowane z usługą TFS.

- [Jak przechowywać kod w usłudze Azure DevOps](https://docs.microsoft.com/azure/devops/repos/git/gitworkflow?view=azure-devops)

- [Uprawnienia i grupy w usłudze Azure DevOps — informacje](https://docs.microsoft.com/azure/devops/organizations/security/about-permissions)

## <a name="76-securely-store-custom-operating-system-images"></a>7.6: Bezpieczne przechowywanie obrazów niestandardowych systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.6 | 5.3 | Klient |

Jeśli używasz obrazów niestandardowych, użyj kontroli dostępu opartej na rolach (RBAC), aby upewnić się, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do obrazów. Za pomocą udostępnionej galerii obrazów można udostępniać obrazy różnym użytkownikom, jednostkom usług lub grupom usług AD w organizacji.  W przypadku obrazów kontenerów należy przechowywać je w rejestrze kontenerów platformy Azure i korzystać z narzędzia RBAC, aby upewnić się, że tylko autoryzowani użytkownicy mogą uzyskiwać dostęp do obrazów.  

- [Opis rbac na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/rbac-and-directory-admin-roles)

- [Opis RBAC dla rejestru kontenerów](https://docs.microsoft.com/azure/container-registry/container-registry-roles)

- [Jak skonfigurować rbac na platformie Azure](https://docs.microsoft.com/azure/role-based-access-control/quickstart-assign-role-user-portal)

- [Udostępnione galerii obrazów omówienie](https://docs.microsoft.com/azure/virtual-machines/windows/shared-image-galleries)

## <a name="77-deploy-configuration-management-tools-for-azure-resources"></a>7.7: Wdrażanie narzędzi do zarządzania konfiguracją dla zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.7 | 5.4 | Klient |

Definiowanie i implementowanie standardowych konfiguracji zabezpieczeń dla zasobów platformy Azure przy użyciu zasad platformy Azure. Aliasy zasad platformy Azure umożliwia tworzenie zasad niestandardowych w celu inspekcji lub wymuszenia konfiguracji sieci zasobów platformy Azure. Można również korzystać z wbudowanych definicji zasad związanych z określonych zasobów.  Ponadto można użyć usługi Azure Automation do wdrażania zmian konfiguracji.

- [Jak skonfigurować zasady platformy Azure i zarządzać nimi](https://docs.microsoft.com/azure/governance/policy/tutorials/create-and-manage)

- [Jak używać aliasów](https://docs.microsoft.com/azure/governance/policy/concepts/definition-structure#aliases)

## <a name="78-deploy-configuration-management-tools-for-operating-systems"></a>7.8: Wdrażanie narzędzi do zarządzania konfiguracją dla systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.8 | 5.4 | Klient |

Konfiguracja stanu automatyzacji platformy Azure to usługa zarządzania konfiguracją dla węzłów konfiguracji żądanego stanu (DSC) w dowolnej chmurze lub lokalnym centrum danych. Można łatwo przywkłędować maszyny, przypisać im deklaratywnych konfiguracji i wyświetlić raporty pokazujące zgodność każdej maszyny do określonego stanu. 

- [Maszyny dołączające do zarządzania przez konfigurację stanu automatyzacji platformy Azure](https://docs.microsoft.com/azure/automation/automation-dsc-onboarding)

## <a name="79-implement-automated-configuration-monitoring-for-azure-resources"></a>7.9: Wdrażanie automatycznego monitorowania konfiguracji zasobów platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.9 | 5.5 | Klient |

Użyj usługi Azure Security Center do wykonywania skanowania według planu bazowego dla zasobów platformy Azure.  Ponadto użyj zasad platformy Azure do ostrzegania i inspekcji konfiguracji zasobów platformy Azure.

- [Jak korygować zalecenia w usłudze Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-remediate-recommendations)

## <a name="710-implement-automated-configuration-monitoring-for-operating-systems"></a>7.10: Wdrożenie automatycznego monitorowania konfiguracji systemów operacyjnych

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.10 | 5.5 | Klient |

Usługa Azure Security Center służy do skanowania według planu bazowego dla systemu operacyjnego i ustawień platformy Docker dla kontenerów.

- [Omówienie rekomendacji dotyczących kontenera usługi Azure Security Center](https://docs.microsoft.com/azure/security-center/security-center-container-recommendations)

## <a name="711-manage-azure-secrets-securely"></a>7.11: Bezpieczne zarządzanie wpisami tajnymi platformy Azure

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.11 | 13.1 | Klient |

Użyj tożsamości usługi zarządzanej w połączeniu z usługą Azure Key Vault, aby uprościć i zabezpieczyć zarządzanie tajne dla aplikacji w chmurze.

- [Jak zintegrować się z tożsamościami zarządzanymi platformy Azure](https://docs.microsoft.com/azure/azure-app-configuration/howto-integrate-azure-managed-service-identity)

- [Jak utworzyć przechowalnię kluczy](https://docs.microsoft.com/azure/key-vault/quick-create-portal)

- [Jak zapewnić uwierzytelnianie usługi Key Vault z tożsamością zarządzaną](https://docs.microsoft.com/azure/key-vault/managed-identity)

## <a name="712-manage-identities-securely-and-automatically"></a>7.12: Bezpieczne i automatyczne zarządzanie tożsamościami

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.12 | 4.1 | Klient |

Użyj tożsamości zarządzanych, aby zapewnić usługi platformy Azure z automatycznie zarządzaną tożsamością w usłudze Azure AD. Tożsamości zarządzane umożliwia uwierzytelnianie do dowolnej usługi, która obsługuje uwierzytelnianie usługi Azure AD, w tym Usługi Key Vault, bez żadnych poświadczeń w kodzie.

- [Jak skonfigurować tożsamości zarządzane](https://docs.microsoft.com/azure/active-directory/managed-identities-azure-resources/qs-configure-portal-windows-vm)

## <a name="713-eliminate-unintended-credential-exposure"></a>7.13: Wyeliminuj niezamierzone narażenie na poświadczenia

| Identyfikator platformy Azure | Identyfikatory SIC | Odpowiedzialność |
|--|--|--|
| 7.13 | 18.1, 18.7 | Klient |

Zaimplementuj skaner poświadczeń w celu zidentyfikowania poświadczeń w kodzie. Skaner poświadczeń będzie również zachęcać do przenoszenia wykrytych poświadczeń do bezpieczniejszych lokalizacji, takich jak Usługa Azure Key Vault. 

- [Jak skonfigurować Skaner poświadczeń](https://secdevtools.azurewebsites.net/helpcredscan.html)


## <a name="next-steps"></a>Następne kroki

- Zobacz następną kontrolę zabezpieczeń: [Ochrona przed złośliwym oprogramowaniem](security-control-malware-defense.md)