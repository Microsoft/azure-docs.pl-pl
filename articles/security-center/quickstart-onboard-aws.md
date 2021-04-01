---
title: Połącz swoje konto AWS z Azure Security Center
description: Monitorowanie zasobów AWS z poziomu Azure Security Center
author: memildin
ms.author: memildin
ms.date: 01/24/2021
ms.topic: quickstart
ms.service: security-center
manager: rkarlin
ms.openlocfilehash: 2ad3ab8c39d9f9a29e74bd139fb5ddd1e5faeff9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 03/29/2021
ms.locfileid: "98755158"
---
#  <a name="connect-your-aws-accounts-to-azure-security-center"></a>Połącz konta AWS z Azure Security Center

W przypadku obciążeń w chmurze zwykle obejmujących wiele platform w chmurze usługi zabezpieczeń w chmurze muszą być takie same.

Azure Security Center chroni obciążenia na platformie Azure, Amazon Web Services (AWS) i Google Cloud Platform (GCP).

Dołączanie konta AWS do Security Center integruje się z Centrum zabezpieczeń AWS i Azure Security Center. Security Center w ten sposób zapewnia widoczność i ochronę w obu tych środowiskach w chmurze, aby zapewnić:

- Automatyczne Inicjowanie obsługi agenta (Security Center używa [usługi Azure Arc](../azure-arc/servers/overview.md) do wdrożenia agenta log Analytics do wystąpień AWS)
- Zarządzanie zasadami
- Zarządzanie lukami w zabezpieczeniach
- Wykrywanie i odpowiedź osadzonych punktów końcowych (EDR)
- Wykrywanie niepożądanych konfiguracji zabezpieczeń
- Pojedynczy widok przedstawiający zalecenia dotyczące Security Center zaleceń i centrum zabezpieczeń AWS
- WyAWSnie zasobów platformy w celu uzyskania bezpiecznych obliczeń oceny Security Center
- Oceny zgodności z przepisami dla zasobów AWS

Na poniższym zrzucie ekranu można zobaczyć konta AWS wyświetlane na pulpicie nawigacyjnym przeglądu Security Center.

:::image type="content" source="./media/quickstart-onboard-aws/aws-account-in-overview.png" alt-text="Trzy projekty GCP wymienione na pulpicie nawigacyjnym przeglądu Security Center" lightbox="./media/quickstart-onboard-gcp/gcp-account-in-overview.png":::

## <a name="availability"></a>Dostępność

|Aspekt|Szczegóły|
|----|:----|
|Stan wydania:|Ogólna dostępność (GA)|
|Wpisaną|Wymaga [usługi Azure Defender dla serwerów](defender-for-servers-introduction.md)|
|Wymagane role i uprawnienia:|**Właściciel** w odpowiedniej subskrypcji platformy Azure<br>**Współautor** może również połączyć konto AWS, jeśli właściciel poda szczegóły jednostki usługi|
|Połączeń|![Tak](./media/icons/yes-icon.png) Chmury komercyjne<br>![Nie](./media/icons/no-icon.png) National/suwerenne (US Gov, Chiny gov, inne gov)|
|||



## <a name="connect-your-aws-account"></a>Łączenie z kontem platformy AWS

Wykonaj poniższe kroki, aby utworzyć łącznik usługi AWS Cloud. 

### <a name="step-1-set-up-aws-security-hub"></a>Krok 1. Skonfiguruj Centrum zabezpieczeń AWS:

1. Aby wyświetlić zalecenia dotyczące zabezpieczeń dla wielu regionów, powtórz następujące kroki dla każdego odpowiedniego regionu.

    > [!IMPORTANT]
    > Jeśli używasz konta głównego AWS, powtórz następujące trzy kroki, aby skonfigurować konto główne i wszystkie połączone konta członków we wszystkich odpowiednich regionach

    1. Włącz [konfigurację AWS](https://docs.aws.amazon.com/config/latest/developerguide/gs-console.html).
    1. Włącz [Centrum zabezpieczeń AWS](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-settingup.html).
    1. Sprawdź, czy istnieją dane przepływające do centrum zabezpieczeń.

        Po pierwszym włączeniu Centrum zabezpieczeń może upłynąć kilka godzin, aby dane były dostępne.

### <a name="step-2-set-up-authentication-for-security-center-in-aws"></a>Krok 2. Konfigurowanie uwierzytelniania dla Security Center w AWS

Istnieją dwa sposoby zezwalania Security Center na uwierzytelnianie AWS:

- **Utwórz rolę usługi IAM dla Security Center** — jest to najbezpieczniejsza Metoda i jest zalecana
- **AWS użytkownika Security Center** -mniej bezpieczna opcja, jeśli nie masz włączonej usługi IAM

#### <a name="create-an-iam-role-for-security-center"></a>Utwórz rolę usługi IAM dla Security Center
1. Z poziomu konsoli Amazon Web Services w obszarze **zabezpieczenia, tożsamość & zgodność**, wybierz pozycję **IAM**.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-identity-and-compliance.png" alt-text="Usługi AWS":::

1. Wybierz **role** i **Utwórz rolę**.
1. Wybierz **inne konto AWS**.
1. Wprowadź następujące informacje:

    - **Identyfikator konta** — wprowadź identyfikator konta Microsoft (**158177204117**), jak pokazano na stronie łącznika AWS w Security Center.
    - **Wymagaj zewnętrznego identyfikatora** — należy wybrać
    - **Identyfikator zewnętrzny** — wprowadź identyfikator subskrypcji, jak pokazano na stronie łącznika AWS w Security Center 

1. Wybierz opcję **Dalej**.
1. W sekcji **Dołącz zasady uprawnień** wybierz następujące zasady:

    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess

1. Opcjonalnie dodaj Tagi. Dodawanie tagów do użytkownika nie ma wpływu na połączenie.
1. Wybierz opcję **Dalej**.

1. Na liście role Wybierz utworzoną rolę

1. Zapisz nazwę zasobu Amazon (ARN) na potrzeby późniejszej. 

#### <a name="create-an-aws-user-for-security-center"></a>Utwórz użytkownika AWS na potrzeby Security Center 
1. Otwórz kartę **Użytkownicy** i wybierz pozycję **Dodaj użytkownika**.
1. W kroku **szczegóły** wprowadź nazwę użytkownika Security Center i upewnij się, że wybrano opcję **dostęp programistyczny** dla typu dostępu AWS. 
1. Wybierz pozycję **następne uprawnienia**.
1. Wybierz opcję **Dołącz istniejące zasady bezpośrednio** i Zastosuj następujące zasady:
    - SecurityAudit
    - AmazonSSMAutomationRole
    - AWSSecurityHubReadOnlyAccess
    
1. Wybierz pozycję **Dalej: Tagi**. Opcjonalnie dodaj Tagi. Dodawanie tagów do użytkownika nie ma wpływu na połączenie.
1. Wybierz pozycję **Przegląd**.
1. Zapisz automatycznie wygenerowany **Identyfikator klucza dostępu** i **klucz dostępu klucza tajnego** do późniejszej wersji.
1. Przejrzyj podsumowanie, a następnie kliknij pozycję **Utwórz użytkownika**.


### <a name="step-3-configure-the-ssm-agent"></a>Krok 3. Konfigurowanie agenta program SSM

Menedżer systemów AWS jest wymagany do automatyzowania zadań w ramach zasobów AWS. Jeśli wystąpienia EC2 nie mają agenta program SSM, postępuj zgodnie z odpowiednimi instrukcjami z usługi Amazon:

- [Instalowanie i Konfigurowanie agenta program SSM w wystąpieniach systemu Windows](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-win.html)
- [Instalowanie i Konfigurowanie agenta program SSM w wystąpieniach Amazon EC2 Linux](https://docs.aws.amazon.com/systems-manager/latest/userguide/sysman-install-ssm-agent.html)


### <a name="step-4-complete-azure-arc-prerequisites"></a>Krok 4. Ukończ wymagania wstępne usługi Azure Arc
1. Upewnij się, że odpowiednie [dostawcy zasobów platformy Azure](../azure-arc/servers/agent-overview.md#register-azure-resource-providers) są zarejestrowani:
    - Microsoft. HybridCompute
    - Microsoft. GuestConfiguration

1. Utwórz nazwę główną usługi do dołączania w odpowiedniej skali. Jako **właściciel** subskrypcji, której chcesz użyć na potrzeby dołączania, Utwórz jednostkę usługi na potrzeby dołączania w usłudze Azure ARC, zgodnie z opisem w temacie [Tworzenie jednostki usługi na potrzeby](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale)dołączania w odpowiedniej skali.


### <a name="step-5-connect-aws-to-security-center"></a>Krok 5. Połącz AWS z Security Center

1. Z menu Security Center wybierz pozycję **wiele łączników w chmurze**.
1. Wybierz pozycję **Dodaj konto AWS**.
    :::image type="content" source="./media/quickstart-onboard-aws/add-aws-account.png" alt-text="Przycisk dodawania konta AWS na stronie łączników wielochmurowych Security Center":::
1. Skonfiguruj opcje na karcie **uwierzytelnianie AWS** :
    1. Wprowadź **nazwę wyświetlaną** dla łącznika.
    1. Upewnij się, że subskrypcja jest poprawna. Jest to subskrypcja obejmująca łącznik i AWS zalecenia dotyczące usługi Centrum zabezpieczeń.
    1. W zależności od opcji uwierzytelniania wybrano w [kroku 2. Skonfiguruj uwierzytelnianie dla Security Center w AWS](#step-2-set-up-authentication-for-security-center-in-aws):
        - Wybierz pozycję  **przyjmij rolę** i wklej ARN z elementu [Utwórz rolę usługi IAM dla Security Center](#create-an-iam-role-for-security-center).
            :::image type="content" source="./media/quickstart-onboard-aws/paste-arn-in-portal.png" alt-text="Wklejanie pliku ARN w odpowiednim polu Kreatora połączenia AWS w Azure Portal":::

            LUB

        - Wybierz pozycję **poświadczenia** i wklej **klucz dostępu** i **klucz tajny** z pliku CSV zapisanego w temacie [Create a AWS User for Security Center](#create-an-aws-user-for-security-center).
1. Wybierz opcję **Dalej**.
1. Skonfiguruj opcje na karcie **Konfiguracja usługi Azure Arc** :

    Security Center odnajduje wystąpienia EC2 na połączonym koncie AWS i używa program SSM, aby dołączyć je do usługi Azure Arc. 

    > [!TIP]
    > Aby uzyskać listę obsługiwanych systemów operacyjnych, zobacz, [które systemy operacyjne dla wystąpień EC2 są obsługiwane?](#what-operating-systems-for-my-ec2-instances-are-supported) w często zadawanych pytaniach.

    1. Wybierz **grupę zasobów** i **region platformy Azure** , w ramach której zostaną dołączone odnalezione AWS EC2s w ramach wybranej subskrypcji.
    1. Wprowadź **Identyfikator jednostki usługi** i **klucz tajny klienta jednostki usługi** dla usługi Azure ARC, zgodnie z opisem w tym miejscu Utwórz jednostkę [usługi na potrzeby](../azure-arc/servers/onboard-service-principal.md#create-a-service-principal-for-onboarding-at-scale) dołączania na dużą skalę
    1. Jeśli komputer nawiązuje połączenie z Internetem za pośrednictwem serwera proxy, określ adres IP serwera proxy lub nazwę i numer portu używanego przez maszynę do komunikacji z serwerem proxy. Wprowadź wartość w formacie ```http://<proxyURL>:<proxyport>```
    1. Wybierz pozycję **Przejrzyj i utwórz**.

        Przejrzyj informacje podsumowujące

        Sekcje Tagi będą zawierać listę wszystkich tagów platformy Azure, które zostaną utworzone automatycznie dla każdego dołączanego EC2 ze swoimi odpowiednimi informacjami, aby łatwo je rozpoznać na platformie Azure. 

        Dowiedz się więcej o tagach platformy Azure w temacie [Używanie tagów do organizowania zasobów platformy Azure i hierarchii zarządzania](../azure-resource-manager/management/tag-resources.md).

### <a name="step-6-confirmation"></a>Krok 6. Potwierdzenie

Po pomyślnym utworzeniu łącznika, a centrum zabezpieczeń AWS zostało prawidłowo skonfigurowane:

- Security Center skanuje środowisko dla wystąpień AWS EC2, dołączanie ich do usługi Azure ARC, umożliwiając zainstalowanie agenta Log Analytics i zapewnienie ochrony przed zagrożeniami oraz zaleceń dotyczących zabezpieczeń. 
- Usługa ASC skanuje nowe wystąpienia usługi AWS EC2 co 6 godzin i dołączy je zgodnie z konfiguracją.
- Standard AWS CIS zostanie wyświetlony na pulpicie nawigacyjnym zgodności z przepisami Security Center.
- Jeśli zasady Centrum zabezpieczeń są włączone, zalecenia będą wyświetlane w portalu Security Center i na pulpicie nawigacyjnym zgodności z przepisami 5-10 minut po zakończeniu dołączania.
    :::image type="content" source="./media/quickstart-onboard-aws/aws-resources-in-recommendations.png" alt-text="AWS zasoby i zalecenia na stronie zalecenia dotyczące Security Center":::



## <a name="monitoring-your-aws-resources"></a>Monitorowanie zasobów AWS

Jak pokazano powyżej, na stronie zalecenia dotyczące zabezpieczeń Azure Security Center są wyświetlane zasoby AWS wraz z zasobami platformy Azure i GCP dla prawdziwego widoku wielochmurowego.

Aby wyświetlić wszystkie aktywne zalecenia dotyczące zasobów według typu zasobu, użyj strony spisu środków Security Center i przefiltruj do typu zasobu AWS, w którym jesteś zainteresowani:

:::image type="content" source="./media/quickstart-onboard-aws/aws-resource-types-in-inventory.png" alt-text="Filtr typu zasobu strony spisu zasobów przedstawiający opcje AWS"::: 


## <a name="aws-in-security-center-faq"></a>AWS w Security Center często zadawane pytania

### <a name="what-operating-systems-for-my-ec2-instances-are-supported"></a>Jakie systemy operacyjne dla wystąpień EC2 są obsługiwane?

Obsługiwane systemy operacyjne umożliwiające automatyczne dołączanie do usługi Azure ARC dla maszyn AWS

- Ubuntu 16,04 — Agent program SSM jest preinstalowany domyślnie
- Ubuntu 18,04 — Agent program SSM jest preinstalowany domyślnie
- System Windows Server — Agent program SSM jest wstępnie zainstalowany, domyślnie
- CentOS Linux 7 — program SSM należy zainstalować ręcznie lub dołączyć osobno
- SUSE Linux Enterprise Server (SLES) 15 (x64) — program SSM należy zainstalować ręcznie lub dołączyć osobno
- Red Hat Enterprise Linux (RHEL) 7 (x64) — program SSM należy zainstalować ręcznie lub dołączyć osobno


## <a name="next-steps"></a>Następne kroki

Połączenie konta usługi AWS jest częścią środowiska z obsługą chmury dostępnej w Azure Security Center. Aby uzyskać powiązane informacje, zobacz następującą stronę:

- [Połącz konta GCP z Azure Security Center](quickstart-onboard-gcp.md)
