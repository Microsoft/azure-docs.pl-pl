---
title: Połącz swoje konto GCP z Azure Security Center
description: Monitorowanie zasobów GCP z poziomu Azure Security Center
author: memildin
ms.author: memildin
ms.date: 9/22/2020
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 71f1de7b4ff265a5740181a2bb2032f33a83abe3
ms.sourcegitcommit: 3792cf7efc12e357f0e3b65638ea7673651db6e1
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 09/29/2020
ms.locfileid: "91448996"
---
#  <a name="connect-your-gcp-accounts-to-azure-security-center"></a>Połącz konta GCP z Azure Security Center

W przypadku obciążeń w chmurze zwykle obejmujących wiele platform w chmurze usługi zabezpieczeń w chmurze muszą być takie same.

Azure Security Center chroni obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Dołączanie konta GCP do Security Center integruje się z usługą GCP Security Command i Azure Security Center. Security Center w ten sposób zapewnia widoczność i ochronę w obu tych środowiskach w chmurze, aby zapewnić:

- Wykrywanie niepożądanych konfiguracji zabezpieczeń
- Pojedynczy widok przedstawiający zalecenia dotyczące Security Center i centrum poleceń zabezpieczeń GCP
- WyGCPnie zasobów platformy w celu uzyskania bezpiecznych obliczeń oceny Security Center
- Integracja z centrum poleceń zabezpieczeń GCP na podstawie standardu CIS na pulpicie nawigacyjnym zgodności z przepisami Security Center

Na poniższym zrzucie ekranu można zobaczyć GCP projekty wyświetlane na pulpicie nawigacyjnym omówienia Security Center.

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-account-in-overview.png" alt-text="3 GCP projekty wymienione na pulpicie nawigacyjnym przeglądu Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::


## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Wersja zapoznawcza|
|Wpisaną|Wymaga [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md)|
|Wymagane role i uprawnienia:|**Właściciel** lub **współautor** w odpowiedniej subskrypcji platformy Azure|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||

## <a name="connect-your-gcp-account"></a>Łączenie z kontem platformy GCP

### <a name="step-1-set-up-gcp-security-command-center-with-security-health-analytics"></a>Krok 1. Konfigurowanie centrum poleceń zabezpieczeń GCP z analizą kondycji zabezpieczeń

W przypadku wszystkich projektów GCP w organizacji należy również:

1. Skonfiguruj **centrum poleceń zabezpieczeń GCP** , korzystając [z tych instrukcji w dokumentacji GCP](https://cloud.google.com/security-command-center/docs/quickstart-scc-setup).
1. Włącz **analizę kondycji zabezpieczeń** [, korzystając z tych instrukcji z dokumentacji GCP](https://cloud.google.com/security-command-center/docs/how-to-use-security-health-analytics).
1. Sprawdź, czy istnieją dane przepływające do centrum poleceń zabezpieczeń.

Instrukcje dotyczące łączenia środowiska GCP z konfiguracją zabezpieczeń są zgodne z zaleceniami firmy Google dotyczącymi używania zaleceń dotyczących konfiguracji zabezpieczeń. Integracja wykorzystuje centrum poleceń Google Security i będzie zużywać dodatkowe zasoby, które mogą mieć wpływ na rozliczenia.

Po pierwszym włączeniu analizy kondycji zabezpieczeń może upłynąć kilka godzin, aby dane były dostępne.


### <a name="step-2-enable-gcp-security-command-center-api"></a>Krok 2. Włącz interfejs API centrum poleceń zabezpieczeń GCP

1. Z **biblioteki interfejsu API usługi Cloud Console**firmy Google wybierz projekt, z którym chcesz się połączyć Azure Security Center.
1. W bibliotece interfejsów API Znajdź i wybierz pozycję **Security API centrum poleceń**.
1. Na stronie interfejsu API wybierz pozycję **Włącz**.

Dowiedz się więcej o [interfejsie API centrum poleceń zabezpieczeń](https://cloud.google.com/security-command-center/docs/reference/rest/).


### <a name="step-3-create-a-dedicated-service-account-for-the-security-configuration-integration"></a>Krok 3. Utwórz dedykowane konto usługi na potrzeby integracji konfiguracji zabezpieczeń

1. W **konsoli GCP**wybierz projekt, z którym chcesz się połączyć Security Center.
1. W **menu nawigacji**w obszarze **IAM & Opcje administratora** wybierz pozycję **konta usług**.
1. Wybierz pozycję **Utwórz konto usługi**.
1. Wprowadź nazwę konta, a następnie wybierz pozycję **Utwórz**.
1. Określ **rolę** jako **Security Center przeglądarką administracyjną**, a następnie wybierz pozycję **Kontynuuj**.
1. Sekcja **Udziel użytkownikom dostępu do tego konta usługi** jest opcjonalna. Wybierz pozycję **Gotowe**.
1. Skopiuj **wartość adres e-mail** utworzonego konta usługi i Zapisz go do późniejszego użycia.
1. W **menu nawigacji**w obszarze **IAM & Opcje administratora** wybierz pozycję **IAM**
    1. Przejdź do poziomu organizacji.
    1. Wybierz pozycję **Dodaj**.
    1. W polu **nowe elementy członkowskie** wklej wcześniej skopiowaną **wartość wiadomości e-mail** .
    1. Określ rolę jako **Security Center przeglądarką administracyjną** , a następnie wybierz pozycję Zapisz.
        :::image type="content" source="./media/quickstart-onboard-gcp/iam-settings-gcp-permissions-admin-viewer.png" alt-text="3 GCP projekty wymienione na pulpicie nawigacyjnym przeglądu Security Center":::


### <a name="step-4-create-a-private-key-for-the-dedicated-service-account"></a>Krok 4. Utwórz klucz prywatny dla dedykowanego konta usługi
1. Przejdź do poziomu projektu.
1. W **menu nawigacji**w obszarze **IAM & Opcje administratora** wybierz pozycję **konta usług**.
1. Otwórz dedykowane konto usługi i wybierz pozycję Edytuj.
1. W sekcji **klucze** wybierz pozycję **Dodaj klucz** , a następnie **Utwórz nowy klucz**.
1. Na ekranie Tworzenie klucza prywatnego wybierz pozycję **JSON**, a następnie wybierz pozycję **Utwórz**.
1. Zapisz ten plik JSON do późniejszego użycia.


### <a name="step-5-connect-gcp-to-security-center"></a>Krok 5. Połącz GCP z Security Center 
1. Z menu Security Center wybierz pozycję **Łączniki chmurowe**.
1. Wybierz pozycję Dodaj konto GCP.
1. Na stronie Dołączanie wykonaj następujące czynności, a następnie wybierz przycisk **dalej**.
    1. Zweryfikuj wybraną subskrypcję.
    1. W polu **Nazwa wyświetlana** wprowadź nazwę wyświetlaną łącznika.
    1. W polu **identyfikator organizacji** wprowadź identyfikator swojej organizacji. Jeśli nie wiesz, zobacz [Tworzenie organizacji i zarządzanie nimi](https://cloud.google.com/resource-manager/docs/creating-managing-organization).
    1. W polu plik **klucza prywatnego** przejdź do pliku JSON pobranego w [kroku 4. Utwórz klucz prywatny dla dedykowanego konta usługi](#step-4-create-a-private-key-for-the-dedicated-service-account).


### <a name="step-6-confirmation"></a>Krok 6. Potwierdzenie

Po pomyślnym utworzeniu łącznika i poprawnym skonfigurowaniu centrum poleceń zabezpieczeń GCP:

- Standard GCP CIS zostanie wyświetlony na pulpicie nawigacyjnym zgodności z przepisami Security Center.
- Zalecenia dotyczące zabezpieczeń dla zasobów usługi GCP będą wyświetlane w portalu Security Center i na pulpicie nawigacyjnym zgodności z przepisami 5-10 minut po zakończeniu dołączania:   :::image type="content" source="./media/quickstart-onboard-gcp/gcp-resources-in-recommendations.png" alt-text="3 GCP projekty wymienione na pulpicie nawigacyjnym przeglądu Security Center":::


## <a name="monitoring-your-gcp-resources"></a>Monitorowanie zasobów GCP

Jak pokazano powyżej, na stronie zalecenia dotyczące zabezpieczeń Azure Security Center są wyświetlane zasoby GCP wraz z zasobami platformy Azure i AWS dla prawdziwego widoku wielochmurowego.

Aby wyświetlić wszystkie aktywne zalecenia dotyczące zasobów według typu zasobu, użyj strony spisu środków Security Center i przefiltruj do typu zasobu GCP, w którym jesteś zainteresowani:

:::image type="content" source="./media/quickstart-onboard-gcp/gcp-resource-types-in-inventory.png" alt-text="3 GCP projekty wymienione na pulpicie nawigacyjnym przeglądu Security Center"::: 


## <a name="next-steps"></a>Następne kroki

Połączenie konta usługi GCP jest częścią środowiska z obsługą chmury dostępnej w Azure Security Center. Aby uzyskać powiązane informacje, zobacz następującą stronę:

- [Połącz konta AWS z Azure Security Center](quickstart-onboard-aws.md)
