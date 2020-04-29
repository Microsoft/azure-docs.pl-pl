---
title: 'Azure Databricks: typowe pytania i pomoc'
description: Uzyskaj odpowiedzi na często zadawane pytania i informacje dotyczące rozwiązywania problemów dotyczących Azure Databricks.
services: azure-databricks
author: mamccrea
ms.author: mamccrea
ms.reviewer: jasonh
ms.service: azure-databricks
ms.workload: big-data
ms.topic: conceptual
ms.date: 10/25/2018
ms.openlocfilehash: 8d7aab43641c6c594ff60368ccb3810e0c060dd7
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/28/2020
ms.locfileid: "78671575"
---
# <a name="frequently-asked-questions-about-azure-databricks"></a>Często zadawane pytania dotyczące Azure Databricks

W tym artykule wymieniono najważniejsze pytania, które mogą być powiązane z Azure Databricks. Znajduje się w nim również kilka typowych problemów, które mogą wystąpić podczas korzystania z kostek. Aby uzyskać więcej informacji, zobacz [co to jest Azure Databricks](what-is-azure-databricks.md). 

## <a name="can-i-use-azure-key-vault-to-store-keyssecrets-to-be-used-in-azure-databricks"></a>Czy można używać Azure Key Vault do przechowywania kluczy/wpisów tajnych, które mają być używane w Azure Databricks?
Tak. Za pomocą Azure Key Vault można przechowywać klucze/wpisy tajne do użycia z Azure Databricks. Aby uzyskać więcej informacji, zobacz [zakresy Azure Key Vault-z kopiami zapasowymi](/azure/databricks/security/secrets/secret-scopes).


## <a name="can-i-use-azure-virtual-networks-with-databricks"></a>Czy mogę używać sieci wirtualnych platformy Azure z kostkami
Tak. Możesz użyć usługi Azure Virtual Network (VNET) z Azure Databricks. Aby uzyskać więcej informacji, zobacz [wdrażanie Azure Databricks w usłudze Azure Virtual Network](/azure/databricks/administration-guide/cloud-configurations/azure/vnet-inject).

## <a name="how-do-i-access-azure-data-lake-storage-from-a-notebook"></a>Jak mogę dostępu Azure Data Lake Storage z notesu? 

Wykonaj następujące kroki:
1. W Azure Active Directory (Azure AD) Zainicjuj obsługę administracyjną jednostki usługi i Zapisz jej klucz.
1. Przypisz odpowiednie uprawnienia do nazwy głównej usługi w Data Lake Storage.
1. Aby uzyskać dostęp do pliku w Data Lake Storage, Użyj poświadczeń nazwy głównej usługi w notesie.

Aby uzyskać więcej informacji, zobacz [używanie Azure Data Lake Storage z Azure Databricks](/azure/databricks/data/data-sources/azure/azure-datalake).

## <a name="fix-common-problems"></a>Rozwiązywanie typowych problemów

Poniżej przedstawiono kilka problemów, które mogą wystąpić w przypadku korzystania z kostek.

### <a name="issue-this-subscription-is-not-registered-to-use-the-namespace-microsoftdatabricks"></a>Problem: Ta subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw "Microsoft. datakostks"

#### <a name="error-message"></a>Komunikat o błędzie

"Ta subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw" Microsoft. datakostks ". Zobacz https://aka.ms/rps-not-found , jak zarejestrować subskrypcje. (Kod: MissingSubscriptionRegistration) "

#### <a name="solution"></a>Rozwiązanie

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Wybierz **subskrypcje**, używaną subskrypcję, a następnie **dostawców zasobów**. 
1. Na liście dostawców zasobów dla elementu **Microsoft. datacegły**wybierz pozycję **zarejestruj**. Aby zarejestrować dostawcę zasobów, musisz mieć rolę współautora lub właściciela w ramach subskrypcji.


### <a name="issue-your-account-email-does-not-have-the-owner-or-contributor-role-on-the-databricks-workspace-resource-in-the-azure-portal"></a>Problem: Twoje konto {email} nie ma roli Owner lub współautor w zasobie obszaru roboczego datakosteks w Azure Portal

#### <a name="error-message"></a>Komunikat o błędzie

"Twoje konto {email}" nie ma roli właściciela lub współautora w zasobie obszaru roboczego datacegły w Azure Portal. Ten błąd może również wystąpić, jeśli jesteś użytkownikiem gościa w dzierżawie. Poproszenie administratora o przyznanie Ci dostępu lub dodanie Cię jako użytkownika bezpośrednio w obszarze roboczym dane. 

#### <a name="solution"></a>Rozwiązanie

Poniżej przedstawiono kilka rozwiązań tego problemu:

* Aby zainicjować dzierżawcę, musisz być zalogowanym jako zwykły użytkownik dzierżawy, a nie jako użytkownik-Gość. Musisz również mieć rolę współautor w zasobie obszaru roboczego datakostki. Dostęp użytkownika można udzielić na karcie **kontroli dostępu (IAM)** w obszarze roboczym datacegły w Azure Portal.

* Ten błąd może również wystąpić, jeśli nazwa domeny poczty e-mail jest przypisana do wielu katalogów w usłudze Azure AD. Aby obejść ten problem, Utwórz nowego użytkownika w katalogu zawierającym subskrypcję z obszarem roboczym datakostki.

    a. W Azure Portal przejdź do usługi Azure AD. Wybierz pozycję **Użytkownicy i grupy** > **Dodaj użytkownika**.

    b. Dodaj użytkownika z `@<tenant_name>.onmicrosoft.com` wiadomością e-mail zamiast `@<your_domain>` poczty e-mail. Tę opcję można znaleźć w obszarze **domeny niestandardowe**w obszarze usługi Azure AD w Azure Portal.
    
    c. Przyznaj temu nowemu użytkownikowi rolę **współautor** w zasobie obszaru roboczego datakostki.
    
    d. Zaloguj się do Azure Portal za pomocą nowego użytkownika i Znajdź obszar roboczy datakostki.
    
    e. Uruchom obszar roboczy datakosteks jako ten użytkownik.


### <a name="issue-your-account-email-has-not-been-registered-in-databricks"></a>Problem: Twoje konto {email} nie zostało zarejestrowane w kostkach danych 

#### <a name="solution"></a>Rozwiązanie

Jeśli nie utworzono obszaru roboczego, a użytkownik zostanie dodany jako użytkownik, skontaktuj się z osobą, która utworzyła obszar roboczy. Należy dodać tę osobę przy użyciu konsoli administracyjnej Azure Databricks. Aby uzyskać instrukcje, zobacz [Dodawanie użytkowników i zarządzanie nimi](/azure/databricks/administration-guide/users-groups/users). Jeśli obszar roboczy został utworzony i nadal pojawia się ten błąd, spróbuj ponownie wybrać opcję **zainicjuj obszar roboczy** w Azure Portal.

### <a name="issue-cloud-provider-launch-failure-while-setting-up-the-cluster-publicipcountlimitreached"></a>Problem: Wystąpił błąd podczas uruchamiania dostawcy chmury podczas konfigurowania klastra (PublicIPCountLimitReached)

#### <a name="error-message"></a>Komunikat o błędzie

"Niepowodzenie uruchomienia dostawcy chmury: Wystąpił błąd dostawcy w chmurze podczas konfigurowania klastra. Aby uzyskać więcej informacji, zobacz Przewodnik po kostkach. Kod błędu platformy Azure: PublicIPCountLimitReached. Komunikat o błędzie platformy Azure: nie można utworzyć więcej niż 10 publicznych adresów IP dla tej subskrypcji w tym regionie ".

#### <a name="background"></a>Tło

Klastry datakostek używają jeden publiczny adres IP na węzeł (w tym węzeł sterownika). Subskrypcje platformy Azure mają [limity publicznych adresów IP](/azure/azure-resource-manager/management/azure-subscription-service-limits#publicip-address) na region. W ten sposób operacje tworzenia klastrów i skalowania w górę mogą zakończyć się niepowodzeniem, jeśli spowodują, że liczba publicznych adresów IP przydzielono do tej subskrypcji w tym regionie przekroczy limit. Ten limit obejmuje również publiczne adresy IP przystosowane do użycia poza datakostki, takie jak niestandardowe maszyny wirtualne zdefiniowane przez użytkownika.

Ogólnie rzecz biorąc, klastry zużywają tylko publiczne adresy IP, gdy są aktywne. `PublicIPCountLimitReached` Błędy mogą jednak nadal występować przez krótki czas nawet po zakończeniu innych klastrów. Wynika to z faktu, że podczas kończenia klastra zasoby platformy Azure są tymczasowo buforowane w pamięci podręcznej. Buforowanie zasobów odbywa się według projektu, ponieważ znacznie zmniejsza opóźnienie uruchamiania klastra i skalowanie automatyczne w wielu typowych scenariuszach.

#### <a name="solution"></a>Rozwiązanie

Jeśli Twoja subskrypcja osiągnęła już swój publiczny limit adresów IP dla danego regionu, należy wykonać jedną lub drugą z następujących czynności.

- Utwórz nowe klastry w innym obszarze roboczym datakostki. Drugi obszar roboczy musi znajdować się w regionie, w którym nie osiągnięto limitu publicznego adresu IP Twojej subskrypcji.
- [Żądanie zwiększenia limitu publicznego adresu IP](https://docs.microsoft.com/azure/azure-portal/supportability/resource-manager-core-quotas-request). Wybierz pozycję **przydział** jako **typ problemu**i **Sieć: ARM** jako **typ przydziału**. W obszarze **szczegóły**Zażądaj zwiększenia limitu przydziału publicznego adresu IP. Na przykład, jeśli limit jest obecnie 60 i chcesz utworzyć klaster z systemem 100, zażądaj zwiększenia limitu do 160.

### <a name="issue-a-second-type-of-cloud-provider-launch-failure-while-setting-up-the-cluster-missingsubscriptionregistration"></a>Problem: Wystąpił drugi typ błędu uruchamiania dostawcy chmury podczas konfigurowania klastra (MissingSubscriptionRegistration)

#### <a name="error-message"></a>Komunikat o błędzie

"Niepowodzenie uruchomienia dostawcy chmury: Wystąpił błąd dostawcy w chmurze podczas konfigurowania klastra. Aby uzyskać więcej informacji, zobacz Przewodnik po kostkach.
Kod błędu platformy Azure: MissingSubscriptionRegistration komunikat o błędzie platformy Azure: subskrypcja nie jest zarejestrowana w celu używania przestrzeni nazw "Microsoft. COMPUTE". Aby https://aka.ms/rps-not-found dowiedzieć się, jak zarejestrować subskrypcje ".

#### <a name="solution"></a>Rozwiązanie

1. Przejdź do [Azure Portal](https://portal.azure.com).
1. Wybierz **subskrypcje**, używaną subskrypcję, a następnie **dostawców zasobów**. 
1. Na liście dostawców zasobów dla elementu **Microsoft. COMPUTE**wybierz pozycję **zarejestruj**. Aby zarejestrować dostawcę zasobów, musisz mieć rolę współautora lub właściciela w ramach subskrypcji.

Aby uzyskać bardziej szczegółowe instrukcje, zobacz [dostawcy zasobów i ich typy](../azure-resource-manager/management/resource-providers-and-types.md).

### <a name="issue-azure-databricks-needs-permissions-to-access-resources-in-your-organization-that-only-an-admin-can-grant"></a>Problem: Azure Databricks wymaga uprawnień dostępu do zasobów w organizacji, które mogą przyznawać tylko Administratorzy.

#### <a name="background"></a>Tło

Azure Databricks jest zintegrowana z Azure Active Directory. Możesz ustawić uprawnienia w Azure Databricks (na przykład w notesach lub klastrach), określając użytkowników z usługi Azure AD. Aby program Azure Databricks mógł wyświetlić listę nazw użytkowników z usługi Azure AD, musi mieć uprawnienia do odczytu tych informacji i zgody na ich udzielenie. Jeśli zgoda nie jest jeszcze dostępna, zobaczysz błąd.

#### <a name="solution"></a>Rozwiązanie

Zaloguj się jako Administrator globalny do Azure Portal. W przypadku Azure Active Directory przejdź do karty **Ustawienia użytkownika** i upewnij się, że **Użytkownicy mogą wyrazić zgodę na aplikacje uzyskujące dostęp do danych firmy w ich imieniu** **.**

## <a name="next-steps"></a>Następne kroki

- [Szybki Start: Rozpoczynanie pracy z usługą Azure Databricks](quickstart-create-databricks-workspace-portal.md)
- [Co to jest Azure Databricks?](what-is-azure-databricks.md)
