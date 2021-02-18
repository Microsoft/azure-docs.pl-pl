---
title: Konfigurowanie kluczy zarządzanych przez klienta w usłudze Azure wskaźnikowej | Microsoft Docs
description: Dowiedz się, jak skonfigurować klucze zarządzane przez klienta (CMK) na platformie Azure.
services: sentinel
documentationcenter: na
author: yelevin
manager: rkarlin
editor: ''
ms.service: azure-sentinel
ms.subservice: azure-sentinel
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 10/12/2020
ms.author: yelevin
ms.openlocfilehash: a1c2754fcae5768c6b87d6280fc882acd46d9a0a
ms.sourcegitcommit: e559daa1f7115d703bfa1b87da1cf267bf6ae9e8
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/17/2021
ms.locfileid: "100585336"
---
# <a name="set-up-azure-sentinel-customer-managed-key"></a>Konfigurowanie klucza zarządzanego przez klienta na platformie Azure

Ten artykuł zawiera ogólne informacje i kroki konfigurowania klucza zarządzanego przez klienta (CMK) dla platformy Azure. CMK umożliwia zaszyfrowanie wszystkich danych zapisanych lub wysyłanych do usługi Azure wskaźnikowych we wszystkich odpowiednich zasobach magazynu za pomocą klucza Azure Key Vault utworzonego lub należącego do użytkownika.

> [!NOTE]
> - Możliwość usługi Azure wskaźnik CMK jest dostępna tylko dla **nowych klientów**.
>
> - Dostęp do tej możliwości jest kontrolowany przez rejestrację funkcji platformy Azure. Aby zażądać dostępu, skontaktuj się z pomocą techniczną azuresentinelCMK@microsoft.com . Oczekujące żądania zostaną zatwierdzone zgodnie z dostępną pojemnością.
>
> - Funkcja CMK jest dostępna tylko dla klientów, którzy wysyłają 1 TB dziennie lub dłużej. Po zastosowaniu do firmy Microsoft informacji o dodatkowych cenach otrzymasz informacje o dodatkowym cenniku w celu udostępnienia CMK w ramach subskrypcji platformy Azure. Dowiedz się więcej o [cenach log Analytics](../azure-monitor/logs/manage-cost-storage.md#log-analytics-dedicated-clusters).

## <a name="how-cmk-works"></a>Jak działa CMK 

Rozwiązanie Azure wskaźnikowego używa kilku zasobów magazynu dla zbierania i funkcji dzienników, w tym Log Analytics i innych. W ramach konfiguracji usługi Azure wskaźnikowej CMK należy również skonfigurować ustawienia CMK dla powiązanych zasobów magazynu. Dane zapisane w zasobach magazynu innych niż Log Analytics również będą szyfrowane.

Dowiedz się więcej o [CMK](../azure-monitor/logs/customer-managed-keys.md#customer-managed-key-overview).

> [!NOTE]
> W przypadku włączenia CMK na platformie Azure — żadna publiczna funkcja w wersji zapoznawczej, która nie obsługuje CMK, nie będzie włączona.

## <a name="enable-cmk"></a>Włącz CMK 

Aby udostępnić CMK, wykonaj następujące kroki: 

1.  Utwórz Azure Key Vault i przechowywanie klucza.

2.  Włącz CMK w obszarze roboczym Log Analytics.

3.  Zarejestruj się, aby uzyskać Cosmos DB.

4.  Dodaj zasady dostępu do wystąpienia Azure Key Vault.

5.  Włącz CMK na platformie Azure — wskaźnik.

6.  Włączanie usługi Azure Sentinel.

### <a name="step-1-create-an-azure-key-vault-and-storing-key"></a>Krok 1. Tworzenie Azure Key Vault i przechowywanie klucza

1.  [Utwórz zasób Azure Key Vault](/azure-stack/user/azure-stack-key-vault-manage-portal), a następnie Wygeneruj lub zaimportuj klucz, który ma być używany do szyfrowania danych.
    > [!NOTE]
    >  Azure Key Vault należy skonfigurować jako możliwy do odzyskania, aby chronić klucz i dostęp.

1.  [Włącz opcje odzyskiwania:](../key-vault/general/key-vault-recovery.md)

    -   Upewnij się, że [usuwanie nietrwałe](../key-vault/general/soft-delete-overview.md) jest włączone.

    -   Włącz [ochronę przed przeczyszczeniem](../key-vault/general/soft-delete-overview.md#purge-protection) , aby zabezpieczyć przed wymuszonym usunięciem wpisu tajnego lub magazynu nawet po usunięciu nietrwałego.

### <a name="step-2-enable-cmk-on-your-log-analytics-workspace"></a>Krok 2. Włączanie CMK w obszarze roboczym Log Analytics

Postępuj zgodnie z instrukcjami w temacie [Azure monitor konfiguracja klucza zarządzanego przez klienta](../azure-monitor/logs/customer-managed-keys.md) w celu utworzenia obszaru roboczego CMK, który będzie używany jako obszar roboczy Azure wskaźnikowego, w poniższych krokach.

### <a name="step-3-register-for-cosmos-db"></a>Krok 3. rejestrowanie Cosmos DB

Wskaźnik platformy Azure działa z Cosmos DB jako dodatkowy zasób magazynu. Upewnij się, że zarejestrowano w Cosmos DB.

Postępuj zgodnie z instrukcjami Cosmos DB, aby zarejestrować dostawcę zasobów [Azure Cosmos DB](../cosmos-db/how-to-setup-cmk.md#register-resource-provider) dla subskrypcji platformy Azure.

### <a name="step-4-add-an-access-policy-to-your-azure-key-vault-instance"></a>Krok 4. Dodawanie zasad dostępu do wystąpienia Azure Key Vault

Upewnij się, że dodano dostęp z Cosmos DB do wystąpienia Azure Key Vault. Postępuj zgodnie z instrukcjami Cosmos DB, aby [dodać zasady dostępu do wystąpienia Azure Key Vault](../cosmos-db/how-to-setup-cmk.md#add-an-access-policy-to-your-azure-key-vault-instance) przy użyciu Azure Cosmos DB podmiotu zabezpieczeń.

### <a name="step-5-enable-cmk-in-azure-sentinel"></a>Krok 5. Włączanie CMK na platformie Azure — wskaźnik

Możliwość usługi Azure wskaźnik CMK jest udostępniana nowym klientom dopiero po odebraniu dostępu bezpośrednio z grupy produktów platformy Azure. Skontaktuj się z firmą Microsoft, aby uzyskać zgodę od zespołu ds. platformy Azure, aby umożliwić CMK w rozwiązaniu.

Po otrzymaniu zatwierdzenia zostanie wyświetlony monit o podanie następujących informacji w celu włączenia funkcji CMK.

-  Identyfikator obszaru roboczego, na którym chcesz włączyć CMK

-  Key Vault adres URL: Skopiuj klucz "Identyfikator klucza" do ostatniego ukośnika:  
    

    ![Identyfikator klucza](./media/customer-managed-keys/key-identifier.png)

    Zespół ds. platformy Azure obejmuje funkcję CMK dla danego obszaru roboczego.

-  Weryfikacja od zespołu produktów wskaźnikowego platformy Azure, który został zatwierdzony do korzystania z tej funkcji. Musisz to zrobić przed kontynuowaniem.

### <a name="step-6-enable-azure-sentinel"></a>Krok 6. Włączanie platformy Azure — Wskaźnikowanie


Przejdź do Azure Portal i Włącz wskaźnik platformy Azure w obszarze roboczym, na którym skonfigurowano CMK. Aby uzyskać więcej informacji, zobacz artykuł dotyczący dołączania do [usługi Azure](quickstart-onboard.md).

## <a name="key-encryption-key-revocation-or-deletion"></a>Odwoływanie lub usuwanie klucza szyfrowania klucza


W przypadku, gdy użytkownik odwołuje klucz szyfrowania klucza, usuwając go lub usuwając dostęp do danych wskaźnikowych platformy Azure w ciągu jednej godziny, wskaźnik na platformie Azure będzie przestrzegać zmiany i zachowuje się tak, jakby dane nie były już dostępne. W tym momencie wszystkie operacje wykonywane przy użyciu trwałych zasobów magazynu, takich jak pozyskiwanie danych, trwałe zmiany konfiguracji i Tworzenie zdarzeń, zostaną uniemożliwione. Poprzednio przechowywane dane nie zostaną usunięte, ale pozostaną niedostępne. Dane niedostępne podlegają zasadom przechowywania danych i zostaną usunięte zgodnie z tymi zasadami.

Jedyną operacją, którą można wykonać po odwołaniu klucza szyfrowania lub usunięciu jest usunięcie konta.

Jeśli dostęp zostanie przywrócony po odwołaniu, wskaźnik na platformie Azure będzie przywracał dostęp do danych w ciągu godziny.

Aby dowiedzieć się więcej o tym, jak działa Azure Monitor, zobacz [Azure monitor CMK](../azure-monitor/logs/customer-managed-keys.md#key-revocation).

## <a name="key-encryption-key-rotation"></a>Rotacja klucza szyfrowania klucza


Rotacja kluczy na platformie Azure i Log Analytics. Gdy użytkownik wykonuje rotację kluczy w Key Vault, wskaźnik na platformie Azure obsługuje nowy klucz w ciągu godziny.

W Key Vault można dokonać rotacji kluczy, tworząc nową wersję klucza:

![Rotacja kluczy](./media/customer-managed-keys/key-rotation.png)

Poprzednia wersja klucza można wyłączyć po 24 godzinach lub w dziennikach inspekcji Azure Key Vault nie będą już wyświetlane żadne działania używające poprzedniej wersji.

Jeśli używasz tego samego klucza na platformie Azure wskaźnikowej i w Log Analytics, konieczne jest przeprowadzenie kluczowych rotacji należy jawnie zaktualizować zasób klastra w Log Analytics za pomocą nowej wersji klucza Azure Key Vault. Aby uzyskać więcej informacji, zobacz [Azure monitor obrotu CMK](../azure-monitor/logs/customer-managed-keys.md#key-rotation).

## <a name="next-steps"></a>Następne kroki
W tym dokumencie przedstawiono sposób konfigurowania klucza zarządzanego przez klienta w usłudze Azure wskaźnikowej. Aby dowiedzieć się więcej na temat platformy Azure, zobacz następujące artykuły:
- Dowiedz się [, jak uzyskać wgląd w dane oraz potencjalne zagrożenia](quickstart-get-visibility.md).
- Rozpocznij [wykrywanie zagrożeń za pomocą platformy Azure — wskaźnik](./tutorial-detect-threats-built-in.md).
- [Używaj skoroszytów](tutorial-monitor-your-data.md) do monitorowania danych.