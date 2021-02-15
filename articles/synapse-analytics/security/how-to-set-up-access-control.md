---
title: Jak skonfigurować kontrolę dostępu dla obszaru roboczego Synapse
description: W tym artykule przedstawiono sposób kontrolowania dostępu do obszaru roboczego Synapse przy użyciu ról platformy Azure, ról Synapse, uprawnień SQL i uprawnień git.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: c6ec84d41d113a38e78ab13404ef19faf625530b
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 02/10/2021
ms.locfileid: "100102176"
---
# <a name="how-to-set-up-access-control-for-your-synapse-workspace"></a>Jak skonfigurować kontrolę dostępu dla obszaru roboczego Synapse 

W tym artykule przedstawiono sposób kontrolowania dostępu do obszaru roboczego Synapse przy użyciu ról platformy Azure, ról Synapse, uprawnień SQL i uprawnień git.   

W tym przewodniku skonfigurujesz obszar roboczy i skonfigurujesz podstawowy system kontroli dostępu odpowiedni dla wielu projektów Synapse.  Następnie w tym artykule opisano bardziej zaawansowane opcje precyzyjnej kontroli, która powinna być potrzebna.  

Kontrolę dostępu Synapse można uprościć za pomocą grup zabezpieczeń, które są wyrównane z rolami i osób w organizacji.  Aby zarządzać dostępem, wystarczy dodawać i usuwać użytkowników z grup zabezpieczeń.

Przed rozpoczęciem tego instruktażu zapoznaj się z [omówieniem kontroli dostępu Synapse](./synapse-workspace-access-control-overview.md) , aby zaznajomić się z mechanizmami kontroli dostępu używanymi przez Synapse.   

## <a name="access-control-mechanisms"></a>Mechanizmy kontroli dostępu

> [!NOTE]
> Podejście wykonane w tym przewodniku polega na utworzeniu kilku grup zabezpieczeń, a następnie przypisaniu ról do tych grup. Po skonfigurowaniu grup należy zarządzać członkostwem w grupach zabezpieczeń, aby kontrolować dostęp do obszaru roboczego.

Aby zabezpieczyć obszar roboczy Synapse, należy postępować zgodnie ze wzorcem konfigurowania następujących elementów:

- **Grupy zabezpieczeń**, które umożliwiają grupowanie użytkowników o podobnych wymaganiach dotyczących dostępu.
- **Role platformy Azure**, które umożliwiają kontrolowanie użytkowników, którzy mogą tworzyć pule SQL, pule Apache Spark i środowiska Integration Runtime oraz zarządzać nimi, a także uzyskiwać dostęp do usługi ADLS Gen2 Storage.
- **Role Synapse**, umożliwiające kontrolowanie dostępu do opublikowanych artefaktów kodu, korzystanie z Apache Spark zasobów obliczeniowych i środowiska Integration Runtime 
- **Uprawnienia SQL** do kontrolowania dostępu do pul SQL przez administrację i płaszczyzny danych. 
- **Uprawnienia git**, aby kontrolować, kto może uzyskiwać dostęp do artefaktów kodu w kontroli źródła w przypadku skonfigurowania usługi git dla obszaru roboczego 
 
## <a name="steps-to-secure-a-synapse-workspace"></a>Procedura zabezpieczania obszaru roboczego Synapse

Ten dokument używa standardowych nazw do uproszczenia instrukcji. Zastąp je wybranymi nazwami.

|Ustawienie | Nazwa standardowa | Opis |
| :------ | :-------------- | :---------- |
| **Obszar roboczy Synapse** | `workspace1` |  Nazwa, która będzie miała obszar roboczy Synapse. |
| **Konto ADLSGEN2** | `storage1` | Konto ADLS, które ma być używane z Twoim obszarem roboczym. |
| **Kontener** | `container1` | Kontener w STG1, który będzie używany domyślnie przez obszar roboczy. |
| **Dzierżawa usługi Active Directory** | `contoso` | Nazwa dzierżawy usługi Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>Krok 1. Konfigurowanie grup zabezpieczeń

>[!Note] 
>W trakcie okresu zapoznawczego zaleca się utworzenie grup zabezpieczeń zamapowanych na role administratorów Synapse **Synapse SQL** i **Synapse Apache Spark** .  Wraz z wprowadzeniem nowych Synapse ról i zakresów kontroli RBAC zaleca się używanie tych nowych funkcji w celu kontrolowania dostępu do obszaru roboczego.  Te nowe role i zakresy zapewniają większą elastyczność konfiguracji i rozpoznaje, że deweloperzy często korzystają z różnych wersji SQL i Spark w tworzeniu aplikacji analitycznych i może być konieczne udzielenie dostępu do określonych zasobów, a nie całego obszaru roboczego. [Dowiedz się więcej](./synapse-workspace-synapse-rbac.md) o Synapse RBAC.

Utwórz następujące grupy zabezpieczeń dla obszaru roboczego:

- **`workspace1_SynapseAdministrators`** dla użytkowników, którzy potrzebują pełnej kontroli nad obszarem roboczym.  Dodaj siebie do tej grupy zabezpieczeń, co najmniej na początku.
- **`workspace1_SynapseContributors`** dla deweloperów, którzy muszą opracowywać, debugować i publikować kod w usłudze.   
- **`workspace1_SynapseComputeOperators`** w przypadku użytkowników, którzy muszą zarządzać pulami Apache Spark i nimi monitorować oraz w programie Integration Runtime.
- **`workspace1_SynapseCredentialUsers`** w przypadku użytkowników, którzy muszą debugować i uruchamiać potoki aranżacji przy użyciu poświadczenie obszaru roboczego MSI (tożsamość usługi zarządzanej) i anulować uruchomienia potoku.   

Wkrótce przypiszesz role Synapse do tych grup w zakresie obszaru roboczego.  

Utwórz również tę grupę zabezpieczeń: 
- **`workspace1_SQLAdmins`**, grupy użytkowników, którzy potrzebują usługi SQL Active Directory administrator w ramach puli SQL w obszarze roboczym. 

Ta `workspace1_SQLAdmins` Grupa będzie używana podczas konfigurowania uprawnień SQL w PULACH SQL podczas ich tworzenia. 

W przypadku podstawowej instalacji tych pięciu grup wystarczą. Później możesz dodać grupy zabezpieczeń, aby obsługiwać użytkowników, którzy potrzebują bardziej wyspecjalizowanego dostępu, lub udzielić użytkownikom dostępu tylko do określonych zasobów.

> [!NOTE]
>- Dowiedz się, jak utworzyć grupę zabezpieczeń w [tym artykule](../../active-directory/fundamentals/active-directory-groups-create-azure-portal.md).
>- Dowiedz się, jak dodać grupę zabezpieczeń z innej grupy zabezpieczeń w [tym artykule](../../active-directory/fundamentals/active-directory-groups-membership-azure-portal.md).

>[!Tip]
>Indywidualni użytkownicy Synapse mogą używać Azure Active Directory w Azure Portal, aby wyświetlić ich członkostwo w grupach w celu ustalenia, które role zostały im przyznane.

## <a name="step-2-prepare-your-adls-gen2-storage-account"></a>Krok 2. Przygotowywanie konta magazynu ADLS Gen2

Obszar roboczy Synapse używa domyślnego kontenera magazynu dla:
  - Przechowywanie plików danych kopii zapasowej dla tabel platformy Spark
  - Dzienniki wykonywania dla zadań platformy Spark
  - Zarządzanie bibliotekami, które chcesz zainstalować

Zidentyfikuj następujące informacje dotyczące magazynu:

- Konto ADLS Gen2 do użycia w Twoim obszarze roboczym. Ten dokument wywołuje go `storage1` . `storage1` jest uznawany za "podstawowe" konto magazynu dla Twojego obszaru roboczego.
- Kontener, w `workspace1` którym znajduje się obszar roboczy Synapse, będzie używany domyślnie. Ten dokument wywołuje go `container1` . 

- Za pomocą Azure Portal Przypisz następujące role platformy Azure `container1` do grup zabezpieczeń 

  - Przypisz rolę **współautor danych obiektu blob magazynu** do programu `workspace1_SynapseAdmins` 
  - Przypisz rolę **współautor danych obiektu blob magazynu** do programu `workspace1_SynapseContributors`
  - Przypisz rolę **współautor danych obiektu blob magazynu** do programu `workspace1_SynapseComputeOperators`

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>Krok 3. Tworzenie i Konfigurowanie obszaru roboczego Synapse

W Azure Portal Utwórz obszar roboczy Synapse:

- Wybierz swoją subskrypcję
- Wybierz lub Utwórz grupę zasobów, dla której masz rolę **właściciela** platformy Azure.
- Nazwij obszar roboczy `workspace1`
- Wybierz `storage1` konto magazynu
- Wybierz `container1` kontener, który jest używany jako "system plików".
- Otwórz WS1 w programie Synapse Studio
- Przejdź do obszaru **Zarządzanie**  >  **Access Control** i przypisz role Synapse w *obszarze roboczym* do grup zabezpieczeń w następujący sposób:
  - Przypisz rolę **administratora Synapse** do programu `workspace1_SynapseAdministrators` 
  - Przypisz rolę **współautora Synapse** do elementu `workspace1_SynapseContributors` 
  - Przypisz rolę **operatora obliczeń Synapse** do `workspace1_SynapseComputeOperators`

## <a name="step-4-grant-the-workspace-msi-access-to-the-default-storage-container"></a>Krok 4. przyznanie do domyślnego kontenera magazynu obszaru roboczego MSI

Aby uruchamiać potoki i wykonywać zadania systemowe, Synapse wymaga, aby usługa zarządzania obszarem roboczym (MSI) musi mieć dostęp do `container1` domyślnego konta ADLS Gen2.

- Otwórz witrynę Azure Portal
- Znajdź konto magazynu, `storage1` a następnie `container1`
- Przy użyciu **Access Control (IAM)** upewnij się, że rola **współautor danych obiektów blob magazynu** jest przypisana do pliku MSI obszaru roboczego
  - Jeśli nie jest przypisana, przypisz ją.
  - Plik MSI ma taką samą nazwę jak obszar roboczy. W tym artykule `workspace1` .

## <a name="step-5-grant-synapse-administrators-the-azure-contributor-role-on-the-workspace"></a>Krok 5. przyznanie administratorom Synapse roli współautor platformy Azure w obszarze roboczym 

Aby tworzyć pule SQL, pule Apache Spark i środowiska Integration Runtime, użytkownicy muszą mieć co najmniej dostęp współautora platformy Azure do obszaru roboczego. Rola współautor umożliwia również tym użytkownikom zarządzanie zasobami, w tym Wstrzymywanie i skalowanie.

- Otwórz witrynę Azure Portal
- Zlokalizuj obszar roboczy, `workspace1`
- Przypisz rolę **współautor platformy Azure** `workspace1` do programu `workspace1_SynapseAdministrators` . 

## <a name="step-6-assign-sql-active-directory-admin-role"></a>Krok 6. Przypisywanie roli administratora Active Directory SQL

Twórca stacji roboczej jest automatycznie ustawiany jako administrator Active Directory SQL dla obszaru roboczego.  Tę rolę można udzielić tylko jednemu użytkownikowi lub grupie. W tym kroku przypiszesz administrator Active Directory SQL w obszarze roboczym do `workspace1_SQLAdmins` grupy zabezpieczeń.  Przypisanie tej roli daje grupie administratorów o wysokim poziomie uprawnień dostęp do wszystkich pul SQL i baz danych w obszarze roboczym.   

- Otwórz witrynę Azure Portal
- Przejdź do strony `workspace1`
- W obszarze **Ustawienia** wybierz pozycję **SQL Active Directory administrator**
- Wybierz pozycję **Ustaw administratora** i wybierz pozycję **`workspace1_SQLAdmins`**

>[!Note]
>Krok 6 jest opcjonalny.  Możesz zdecydować się na przyznanie `workspace1_SQLAdmins` grupie mniej uprzywilejowanej roli. Aby przypisać `db_owner` lub inne role SQL, należy uruchomić skrypty dla każdej bazy danych SQL. 

## <a name="step-7-grant-access-to-sql-pools"></a>Krok 7. udzielanie dostępu do pul SQL

Domyślnie wszyscy użytkownicy z przypisaną rolą administratora Synapse są również przypisani do roli SQL `db_owner` w puli SQL bezserwerowej, "wbudowane" i wszystkich jej bazach danych.

Dostęp do pul SQL dla innych użytkowników i dla pliku MSI obszaru roboczego jest kontrolowany przy użyciu uprawnień SQL.  Przypisanie uprawnień SQL wymaga, aby skrypty SQL były uruchamiane w każdej bazie danych SQL po utworzeniu.  Istnieją trzy przypadki, w których wymagane jest uruchomienie następujących skryptów:
1. Udzielanie innym użytkownikom dostępu do puli SQL bezserwerowej, "wbudowanej" i jej baz danych
2. Udzielanie wszystkim użytkownikom dostępu do dedykowanych baz danych puli
3. Udzielanie dostępu do pliku MSI w puli SQL w celu umożliwienia pomyślnego uruchomienia potoków, które wymagają dostępu do puli SQL.

Poniżej znajdują się przykładowe skrypty SQL.

Aby udzielić dostępu do dedykowanej bazy danych puli SQL, skrypty mogą być uruchamiane przez twórcę obszaru roboczego lub dowolnego członka `workspace1_SQLAdmins` grupy.  

Aby udzielić dostępu do puli SQL bezserwerowej, "wbudowane", skrypty mogą być uruchamiane przez dowolnego członka `workspace1_SQLAdmins` grupy lub  `workspace1_SynapseAdministrators` grupy. 

> [!TIP]
> Poniższe kroki muszą zostać uruchomione dla **każdej** puli SQL, aby umożliwić użytkownikom dostęp do wszystkich baz danych SQL, z wyjątkiem sekcji w [obszarze obszar roboczy](#workspace-scoped-permission) , w którym można przypisać użytkownika rolę administratora systemu na poziomie obszaru roboczego.

### <a name="step-71-serverless-sql-pool-built-in"></a>Krok 7,1: bezserwerowa Pula SQL, wbudowana

W tej sekcji znajdują się przykłady skryptów pokazujące, jak przyznać użytkownikowi uprawnienia dostępu do konkretnej bazy danych lub wszystkich baz danych w puli SQL bezserwerowej, "wbudowane".

> [!NOTE]
> W przykładach skryptu Zamień *alias* na alias użytkownika lub grupy, którym udzielono dostępu, i *domenę* z domeną firmy, której używasz.

#### <a name="database-scoped-permission"></a>Uprawnienie do zakresu bazy danych

Aby udzielić dostępu użytkownikowi do **pojedynczej** bezserwerowej bazy danych SQL, wykonaj czynności opisane w tym przykładzie:

1. Utwórz nazwę logowania

    ```sql
    use master
    go
    CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
    go
    ```

2. Utwórz użytkownika

    ```sql
    use yourdb -- Use your database name
    go
    CREATE USER alias FROM LOGIN [alias@domain.com];
    ```

3. Dodaj użytkownika do członków określonej roli

    ```sql
    use yourdb -- Use your database name
    go
    alter role db_owner Add member alias -- Type USER name from step 2
    ```

#### <a name="workspace-scoped-permission"></a>Uprawnienie do zakresu obszaru roboczego

Aby udzielić pełnego dostępu do **wszystkich** pul SQL bezserwerowych w obszarze roboczym, Użyj skryptu w tym przykładzie:

```sql
use master
go
CREATE LOGIN [alias@domain.com] FROM EXTERNAL PROVIDER;
ALTER SERVER ROLE sysadmin ADD MEMBER [alias@domain.com];
```

### <a name="step-72-dedicated-sql-pools"></a>Krok 7,2: dedykowane pule SQL

Aby udzielić dostępu do **pojedynczej** dedykowanej bazy danych puli SQL, wykonaj następujące kroki w Edytorze skryptów SQL Synapse:

1. Utwórz użytkownika w bazie danych, uruchamiając następujące polecenie w docelowej bazie danych, wybierane przy użyciu opcji *Połącz z* listą rozwijaną:

    ```sql
    --Create user in the database
    CREATE USER [<alias@domain.com>] FROM EXTERNAL PROVIDER;
    ```

2. Przyznaj użytkownikowi rolę dostępu do bazy danych:

    ```sql
    --Grant role to the user in the database
    EXEC sp_addrolemember 'db_owner', '<alias@domain.com>';
    ```

> [!IMPORTANT]
> *db_datareader* i *db_datawriter* mogą współdziałać z uprawnieniami do odczytu i zapisu, jeśli udzielanie *db_owner* nie jest wymagane.
> Aby użytkownik platformy Spark mógł odczytywać i zapisywać dane bezpośrednio z platformy Spark do lub z puli SQL, wymagane jest uprawnienie *db_owner* .

Po utworzeniu użytkowników Uruchom zapytania, aby sprawdzić, czy pula SQL bezserwerowa może wysyłać zapytania do konta magazynu.

### <a name="step-73-sql-access-control-for-synapse-pipeline-runs"></a>Krok 7,3: Kontrola dostępu SQL dla Synapse potoku

### <a name="workspace-managed-identity"></a>Tożsamość zarządzana obszaru roboczego

> [!IMPORTANT]
> Aby pomyślnie uruchomić potoki, które zawierają zestawy danych lub działania odwołujące się do puli SQL, tożsamość obszaru roboczego musi mieć przyznane dostęp do puli SQL.

Uruchom następujące polecenia w każdej puli SQL, aby zezwolić na tożsamość systemu zarządzanego przez obszar roboczy do uruchamiania potoków w bazie danych puli SQL:  

>[!note]
>W poniższych skryptach dla dedykowanej bazy danych puli SQL DatabaseName jest taka sama jak nazwa puli.  W przypadku bazy danych w puli SQL bezserwerowej, DatabaseName jest nazwą bazy danych.

```sql
--Create a SQL user for the workspace MSI in database
CREATE USER [<workspacename>] FROM EXTERNAL PROVIDER;

--Granting permission to the identity
GRANT CONTROL ON DATABASE::<databasename> TO <workspacename>;
```

To uprawnienie można usunąć, uruchamiając następujący skrypt w tej samej puli SQL:

```sql
--Revoke permission granted to the workspace MSI
REVOKE CONTROL ON DATABASE::<databasename> TO <workspacename>;

--Delete the workspace MSI user in the database
DROP USER [<workspacename>];
```

## <a name="step-8-add-users-to-security-groups"></a>Krok 8. Dodawanie użytkowników do grup zabezpieczeń

Początkowa konfiguracja systemu kontroli dostępu została ukończona.

Aby zarządzać dostępem, można dodawać i usuwać użytkowników z grup zabezpieczeń, które zostały skonfigurowane.  Mimo że można ręcznie przypisać użytkowników do ról Synapse, jeśli to zrobisz, nie skonfiguruje ich uprawnień konsekwentnie. Zamiast tego należy dodawać i usuwać tylko użytkowników z grup zabezpieczeń.

## <a name="step-9-network-security"></a>Krok 9. zabezpieczenia sieci

Ostatnim krokiem do zabezpieczenia obszaru roboczego jest Zabezpieczanie dostępu do sieci przy użyciu:
- [Zapora obszaru roboczego](./synapse-workspace-ip-firewall.md)
- [Zarządzana Sieć wirtualna](./synapse-workspace-managed-vnet.md) 
- [Prywatne punkty końcowe](./synapse-workspace-managed-private-endpoints.md)
- [Link prywatny](../../azure-sql/database/private-endpoint-overview.md)

## <a name="step-10-completion"></a>Krok 10. uzupełnianie

Obszar roboczy jest teraz w pełni skonfigurowany i zabezpieczony.

## <a name="supporting-more-advanced-scenarios"></a>Obsługa bardziej zaawansowanych scenariuszy

Ten przewodnik koncentruje się na konfigurowaniu podstawowego systemu kontroli dostępu. Można obsługiwać bardziej zaawansowane scenariusze, tworząc dodatkowe grupy zabezpieczeń i przypisując im bardziej szczegółowe role w bardziej szczegółowych zakresach. Należy wziąć pod uwagę następujące przypadki:

**Włącz usługę git — obsługa** obszaru roboczego w przypadku bardziej zaawansowanych scenariuszy programistycznych, takich jak Ci/CD.  W trybie git uprawnienia usługi git określają, czy użytkownik może zatwierdzić zmiany w gałęzi roboczej.  Publikowanie w usłudze odbywa się tylko z gałęzi współpracy.  Należy rozważyć utworzenie grupy zabezpieczeń dla deweloperów, którzy muszą opracowywać i debugować aktualizacje w gałęzi roboczej, ale nie muszą publikować zmian w usłudze na żywo.

**Ogranicz dostęp deweloperów** do określonych zasobów.  Utwórz dodatkowe bardziej szczegółowe grupy zabezpieczeń dla deweloperów, którzy potrzebują dostępu tylko do określonych zasobów.  Przypisz te grupy odpowiednie role Synapse do zakresu określonych pul platformy Spark, środowisk Integration Runtime lub poświadczeń.

**Ograniczanie operatorów do uzyskiwania dostępu do artefaktów kodu**.  Utwórz grupy zabezpieczeń dla operatorów, które muszą monitorować stan operacyjny zasobów obliczeniowych Synapse i wyświetlać dzienniki, ale którzy nie potrzebują dostępu do kodu lub publikowania aktualizacji usługi. Przypisz te grupy rolę operatora obliczeniowego w zakresie określonych pul platformy Spark i środowisk Integration Runtime.  

## <a name="next-steps"></a>Następne kroki

Dowiedz się, [jak zarządzać przypisaniami ról RBAC Synapse](./how-to-manage-synapse-rbac-role-assignments.md) tworzenie [obszaru roboczego Synapse](../quickstart-create-workspace.md)