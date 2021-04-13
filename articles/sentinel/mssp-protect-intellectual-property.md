---
title: Ochrona własności intelektualnej zarządzanego dostawcy usług zabezpieczeń (MSSPs) na platformie Azure — wskaźnik | Microsoft Docs
description: Dowiedz się więcej o tym, jak zarządzani dostawcy usług zabezpieczeń (MSSPs) mogą chronić własność intelektualną utworzoną na platformie Azure.
services: sentinel
documentationcenter: na
author: batamig
manager: rkarlin
editor: ''
ms.assetid: 10cce91a-421b-4959-acdf-7177d261f6f2
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: conceptual
ms.custom: mvc
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 04/12/2021
ms.author: bagol
ms.openlocfilehash: 1c15c341d85fae667ee23883043350340ad866b8
ms.sourcegitcommit: b4fbb7a6a0aa93656e8dd29979786069eca567dc
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/13/2021
ms.locfileid: "107315535"
---
# <a name="protecting-mssp-intellectual-property-in-azure-sentinel"></a>Ochrona własności intelektualnej MSSP na platformie Azure — wskaźnik

W tym artykule opisano metody, których zarządzani dostawcy usług zabezpieczeń (MSSPs) mogą używać do ochrony własności intelektualnej, która została opracowana na platformie Azure, na przykład w przypadku reguł analizy wskaźnikowej platformy Azure, zapytań polowających, elementy playbook i skoroszytów.

Wybrana metoda będzie zależeć od tego, jak każdy z klientów kupuje platformę Azure; bez względu na to, czy działa jako [dostawca rozwiązań w chmurze (CSP)](#cloud-solutions-providers-csp), czy klient ma konto [Enterprise Agreement (/pay-as-you-go)](#enterprise-agreements-ea--pay-as-you-go-payg) . W poniższych sekcjach opisano każdą z tych metod osobno.

## <a name="cloud-solutions-providers-csp"></a>Dostawcy rozwiązań w chmurze (CSP)

Jeśli sprzedajesz platformę Azure jako dostawcę rozwiązań w chmurze (CSP), zarządzasz subskrypcją platformy Azure klienta. Dzięki usłudze [administrator-w imieniu (AOBO)](/partner-center/azure-plan-manage)użytkownicy należący do grupy agenci administracyjni z dzierżawy MSSP są udzielani z dostępem właściciela do subskrypcji platformy Azure klienta, a klient nie ma domyślnego dostępu.

Jeśli inni użytkownicy z dzierżawy MSSP, poza grupą agenci administratora, muszą uzyskać dostęp do środowiska klienta, zalecamy korzystanie z [usługi Azure Lighthouse](multiple-tenants-service-providers.md). Usługa Azure Lighthouse umożliwia przyznawanie użytkownikom lub grupom dostępu do określonego zakresu, takiego jak grupa zasobów lub subskrypcja, przy użyciu jednej z wbudowanych ról.

Jeśli musisz zapewnić użytkownikom klienta dostęp do środowiska platformy Azure, zalecamy udzielenie im dostępu na poziomie *grupy zasobów* , a nie całej subskrypcji, dzięki czemu w razie potrzeby można pokazać lub ukryć części środowiska.

Na przykład:

- Użytkownik może udzielić klientowi dostępu do kilku grup zasobów, w których znajdują się ich aplikacje, ale nadal utrzymywać obszar roboczy wskaźnik platformy Azure w oddzielnej grupie zasobów, w której klient nie ma dostępu.

- Ta metoda umożliwia klientom Wyświetlanie wybranych skoroszytów i elementy PlayBook, które są oddzielnymi zasobami, które mogą znajdować się w ich własnej grupie zasobów.

Nawet w przypadku przyznawania dostępu na poziomie grupy zasobów klienci nadal będą mieć dostęp do danych dzienników dla zasobów, do których mają dostęp, takich jak dzienniki z maszyny wirtualnej, nawet bez dostępu do funkcji wskaźnikowej platformy Azure. Aby uzyskać więcej informacji, zobacz [Zarządzanie dostępem do danych wskaźnikowych platformy Azure według zasobu](resource-context-rbac.md).

> [!TIP]
> Jeśli potrzebujesz udzielić klientom dostępu do całej subskrypcji, możesz chcieć zobaczyć wskazówki dotyczące [umów Enterprise Agreement (EA)/płatność zgodnie z rzeczywistym użyciem (PAYG)](#enterprise-agreements-ea--pay-as-you-go-payg).
>

### <a name="sample-azure-sentinel-csp-architecture"></a>Przykładowa architektura CSP Azure wskaźnikowego

Na poniższej ilustracji opisano, jak uprawnienia opisane w [poprzedniej sekcji](#cloud-solutions-providers-csp) mogą funkcjonować w przypadku udzielenia dostępu do klientów programu CSP:

:::image type="content" source="media/mssp-protect-intellectual-property/csp-customers.png" alt-text="Ochrona własności intelektualnej usługi Azure wskaźnikowej przy użyciu klientów programu CSP.":::

Elementy na tym obrazie:

- Użytkownicy otrzymani z dostępem **właściciela** do subskrypcji dostawcy CSP są użytkownikami w grupie agenci administracyjni w dzierżawie usługi Azure AD MSSP.
- Inne grupy z MSSP uzyskują dostęp do środowiska klienta za pośrednictwem usługi Azure Lighthouse.
- Dostęp klienta do zasobów platformy Azure jest zarządzany przez usługę Azure RBAC na poziomie grupy zasobów.

    Pozwala to MSSPs na ukrycie składników wskaźnikowych platformy Azure w razie potrzeby, takich jak reguły analizy i zapytania dotyczące polowania.

Więcej informacji zawiera również [Dokumentacja usługi Azure Lighthouse](/azure/lighthouse/concepts/cloud-solution-provider).

## <a name="enterprise-agreements-ea--pay-as-you-go-payg"></a>Umowy Enterprise Agreement (EA)/płatność zgodnie z rzeczywistym użyciem (PAYG)

Jeśli klient kupuje bezpośrednio w firmie Microsoft, klient ma już pełny dostęp do środowiska platformy Azure i nie można ukryć wszystkich elementów, które znajdują się w subskrypcji platformy Azure klienta.

Zamiast tego należy chronić własność intelektualną, która została opracowana na platformie Azure, w zależności od typu zasobu, który ma być chroniony:

### <a name="analytics-rules-and-hunting-queries"></a>Reguły analizy i zapytania polowające

Reguły analizy i zapytania dotyczące polowań są zawarte w obszarze wskaźnikowym platformy Azure i dlatego nie mogą być oddzielone od obszaru roboczego wskaźnikowego platformy Azure.

Nawet jeśli użytkownik ma tylko uprawnienia czytnika wskaźnikowego platformy Azure, nadal będzie mógł wyświetlić zapytanie. W tym przypadku zalecamy hostowanie reguł analizy i zapytań polowania we własnej dzierżawie MSSP zamiast dzierżawy klienta.

Aby to zrobić, musisz mieć obszar roboczy we własnej dzierżawie z włączonym wskaźnikiem na platformie Azure i będzie on również widoczny w obszarze roboczym klienta za pośrednictwem [usługi Azure Lighthouse](multiple-tenants-service-providers.md).

Aby utworzyć regułę analityczną lub zapytanie polowania w dzierżawie MSSP, które odwołują się do danych w dzierżawie klienta, należy użyć `workspace` instrukcji w następujący sposób:

```kql
workspace('<customer-workspace>').SecurityEvent
| where EventID == ‘4625’
```

Podczas dodawania `workspace` instrukcji do reguł analizy należy wziąć pod uwagę następujące kwestie:

- **Brak alertów w obszarze roboczym klient**. Utworzone w ten sposób reguły nie tworzą alertów ani zdarzeń w obszarze roboczym klient. Zarówno alerty, jak i incydenty będą znajdować się w obszarze roboczym usługi MSSP.

- **Utwórz osobne alerty dla każdego klienta**. W przypadku korzystania z tej metody zalecamy również korzystanie z oddzielnych reguł alertów dla każdego klienta i wykrywania, ponieważ w każdym przypadku instrukcja obszaru roboczego będzie różna.

    Możesz dodać nazwę klienta do nazwy reguły alertu, aby łatwo zidentyfikować klienta, na którym alert zostanie wyzwolony. Osobne alerty mogą powodować powstanie dużej liczby reguł, które mogą być potrzebne do zarządzania przy użyciu skryptów lub [platformy Azure jako kodu](https://techcommunity.microsoft.com/t5/azure-sentinel/deploying-and-managing-azure-sentinel-as-code/ba-p/1131928).

    Na przykład:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-per-customer.png" alt-text="Utwórz osobne reguły w obszarze roboczym MSSP dla każdego klienta.":::

- **Utwórz osobne obszary robocze MSSP dla każdego klienta**. Tworzenie oddzielnych reguł dla każdego klienta i wykrywania może spowodować osiągnięcie maksymalnej liczby reguł analizy dla Twojego obszaru roboczego (512). Jeśli masz wielu klientów i oczekujesz, że ten limit został osiągnięty, możesz utworzyć oddzielny obszar roboczy MSSP dla każdego klienta.

    Na przykład:

    :::image type="content" source="media/mssp-protect-intellectual-property/mssp-rules-and-workspace-per-customer.png" alt-text="Utwórz obszar roboczy i reguły w dzierżawie MSSP dla każdego klienta.":::

> [!IMPORTANT]
> Klucz do korzystania z tej metody pomyślnie używa automatyzacji do zarządzania dużym zestawem reguł w obszarach roboczych.
>
> Aby uzyskać więcej informacji, zobacz [reguły analizy między obszarami roboczymi](https://techcommunity.microsoft.com/t5/azure-sentinel/what-s-new-cross-workspace-analytics-rules/ba-p/1664211)
>

### <a name="workbooks"></a>Skoroszyty

Jeśli opracowano skoroszyt wskaźnikowy platformy Azure, który nie powinien być kopiowany przez klienta, należy hostować skoroszyt w dzierżawie usługi MSSP. Upewnij się, że masz dostęp do obszarów roboczych klienta za pośrednictwem usługi Azure Lighthouse, a następnie upewnij się, że skoroszyt ma być używany do korzystania z tych obszarów roboczych klienta.

Na przykład:

:::image type="content" source="media/mssp-protect-intellectual-property/cross-workspace-workbook.png" alt-text="Skoroszyty między obszarami roboczymi":::

Aby uzyskać więcej informacji, zobacz [skoroszyty między obszarami roboczymi](extend-sentinel-across-workspaces-tenants.md#cross-workspace-workbooks).

Jeśli klient ma mieć możliwość wyświetlania wizualizacji w skoroszycie, przy zachowaniu tajnego kodu zaleca się wyeksportowanie skoroszytu do Power BI.

Eksportowanie skoroszytu do Power BI:

- Ułatwia **udostępnianie wizualizacji skoroszytów**. Możesz wysłać klientowi link do pulpitu nawigacyjnego Power BI, w którym mogą oni przeglądać raportowane dane, nie wymagając uprawnień dostępu do platformy Azure.
- **Włącza planowanie**. Skonfiguruj Power BI, aby okresowo wysyłać wiadomości e-mail zawierające migawkę pulpitu nawigacyjnego.

Aby uzyskać więcej informacji, zobacz [importowanie Azure monitor danych dziennika do Power BI](/azure/azure-monitor/visualize/powerbi).

### <a name="playbooks"></a>Podręczniki

Możesz chronić elementy PlayBook w następujący sposób, w zależności od tego, gdzie utworzono reguły analityczne, które wyzwalają element PlayBook:

- **Reguły analizy utworzone w obszarze roboczym MSSP**.  Upewnij się, że tworzysz elementy PlayBook w dzierżawie MSSP, a wszystkie dane dotyczące incydentu i alertu są uzyskiwane z obszaru roboczego MSSP. Możesz dołączyć elementy PlayBook za każdym razem, gdy utworzysz nową regułę w obszarze roboczym.

    Na przykład:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-mssp-workspace.png" alt-text="Reguły utworzone w obszarze roboczym MSSP.":::

- **Reguły analizy utworzone w obszarze roboczym klient**. Użyj usługi Azure Lighthouse, aby dołączyć reguły analizy z obszaru roboczego klienta do element PlayBook hostowanej w obszarze roboczym MSSP. W takim przypadku element PlayBook pobiera dane alertu i zdarzenia oraz inne informacje o klientach z obszaru roboczego klienta.

    Na przykład:

    :::image type="content" source="media/mssp-protect-intellectual-property/rules-in-customer-workspace.png" alt-text="Reguły utworzone w obszarze roboczym klient.":::

W obu przypadkach, jeśli element PlayBook musi uzyskać dostęp do środowiska platformy Azure klienta, użyj nazwy głównej użytkownika lub usługi, która ma dostęp za pośrednictwem Lighthouse.

Jeśli jednak element PlayBook musi uzyskać dostęp do zasobów spoza platformy Azure w dzierżawie klienta, takich jak Azure AD, Office 365 lub Microsoft 365 Defender, musisz utworzyć jednostkę usługi z odpowiednimi uprawnieniami w dzierżawie klienta, a następnie dodać tę tożsamość w element PlayBook.

> [!NOTE]
> Jeśli używasz reguł automatyzacji razem z elementy PlayBook, musisz ustawić uprawnienia reguły automatyzacji w grupie zasobów, w której znajduje się elementy PlayBook na żywo.
> Aby uzyskać więcej informacji, zobacz [uprawnienia dla reguł automatyzacji do uruchamiania elementy PlayBook](automate-incident-handling-with-automation-rules.md#permissions-for-automation-rules-to-run-playbooks).
>

## <a name="next-steps"></a>Następne kroki

Aby uzyskać więcej informacji, zobacz:

- [Azure — wskaźnik techniczny element PlayBook dla MSSPs](https://cloudpartners.transform.microsoft.com/download?assetname=assets/Azure-Sentinel-Technical-Playbook-for-MSSPs.pdf&download=1)
- [Zarządzanie wieloma dzierżawcami na platformie Azure — wskaźnikiem MSSP](multiple-tenants-service-providers.md)
- [Rozszerzanie usługi Azure Sentinel na obszary robocze i dzierżawy](extend-sentinel-across-workspaces-tenants.md)
- [Samouczek: wizualizowanie i monitorowanie danych](tutorial-monitor-your-data.md)
- [Samouczek: Konfigurowanie zautomatyzowanych odpowiedzi na zagrożenia na platformie Azure — wskaźnik](tutorial-respond-threats-playbook.md)
