---
title: Uruchamianie zadań w ramach kont użytkowników
description: Dowiedz się, jakie są typy kont użytkowników i jak je skonfigurować.
ms.topic: how-to
ms.date: 04/13/2021
ms.custom: seodec18
ms.openlocfilehash: 02cad0bff9e76ec5db82c417f2439b12ef088045
ms.sourcegitcommit: aa00fecfa3ad1c26ab6f5502163a3246cfb99ec3
ms.translationtype: MT
ms.contentlocale: pl-PL
ms.lasthandoff: 04/14/2021
ms.locfileid: "107389285"
---
# <a name="run-tasks-under-user-accounts-in-batch"></a>Uruchamianie zadań w ramach kont użytkowników w u usługi Batch

> [!NOTE]
> Konta użytkowników omówione w tym artykule różnią się od kont użytkowników używanych na Remote Desktop Protocol (RDP) lub Secure Shell (SSH) ze względów bezpieczeństwa.
>
> Aby nawiązać połączenie z węzłem z konfiguracją maszyny wirtualnej z systemem Linux za pośrednictwem połączenia SSH, zobacz Install [and configure xrdp to](../virtual-machines/linux/use-remote-desktop.md)use Pulpit zdalny with Ubuntu . Aby nawiązać połączenie z węzłami z systemem Windows za pośrednictwem protokołu RDP, zobacz Jak nawiązać połączenie z maszyną wirtualną platformy Azure z systemem Windows i [zalogować się do tej maszyny.](../virtual-machines/windows/connect-logon.md)
>
> Aby nawiązać połączenie z węzłem z konfiguracją usługi w chmurze za pośrednictwem protokołu RDP, zobacz [Włączanie Podłączanie pulpitu zdalnego roli w usłudze Azure Cloud Services](../cloud-services/cloud-services-role-enable-remote-desktop-new-portal.md).

Zadanie w Azure Batch zawsze uruchamiane w ramach konta użytkownika. Domyślnie zadania są uruchamiane na kontach użytkowników standardowych, bez uprawnień administratora. W przypadku niektórych scenariuszy można skonfigurować konto użytkownika, na którym ma zostać uruchomione zadanie. W tym artykule omówiono typy kont użytkowników i sposób ich konfigurowania na podstawie scenariusza.

## <a name="types-of-user-accounts"></a>Typy kont użytkowników

Azure Batch udostępnia dwa typy kont użytkowników do uruchamiania zadań:

- **Konta użytkowników automatycznych.** Konta użytkowników automatycznych to wbudowane konta użytkowników tworzone automatycznie przez usługę Batch. Domyślnie zadania są uruchamiane w ramach konta użytkownika automatycznego. Można skonfigurować specyfikację automatycznego użytkownika dla zadania, aby wskazać, w którym koncie użytkownika automatycznego ma być uruchamiane zadanie. Specyfikacja automatycznego użytkownika umożliwia określenie poziomu podniesienia uprawnień i zakresu konta użytkownika automatycznego, które będzie uruchamiać zadanie.

- **Nazwane konto użytkownika.** Podczas tworzenia puli można określić co najmniej jedno nazwane konto użytkownika dla puli. Każde konto użytkownika jest tworzone w każdym węźle puli. Oprócz nazwy konta należy określić hasło konta użytkownika, poziom podniesienia uprawnień, a w przypadku pul systemu Linux klucz prywatny SSH. Podczas dodawania zadania można określić nazwane konto użytkownika, w ramach którego ma zostać uruchomione to zadanie.

> [!IMPORTANT]
> W usłudze Batch w wersji 2017-01-01.4.0 wprowadzono przełomową zmianę, która wymaga zaktualizowania kodu w celu wywołania tej wersji lub nowszej. Zobacz [Aktualizowanie kodu do najnowszej biblioteki klienta usługi Batch,](#update-your-code-to-the-latest-batch-client-library) aby uzyskać szybkie wskazówki dotyczące aktualizowania kodu usługi Batch ze starszej wersji.

## <a name="user-account-access-to-files-and-directories"></a>Dostęp konta użytkownika do plików i katalogów

Zarówno konto użytkownika automatycznego, jak i nazwane konto użytkownika mają dostęp do odczytu/zapisu do katalogu roboczego zadania, katalogu udostępnionego i katalogu zadań o wielu wystąpieniach. Oba typy kont mają dostęp do odczytu do katalogów uruchamiania i przygotowywania zadań.

Jeśli zadanie jest uruchamiane na tym samym koncie, które zostało użyte do uruchomienia zadania uruchamiania, ma dostęp do odczytu i zapisu w katalogu zadania uruchamiania. Podobnie jeśli zadanie jest uruchamiane na tym samym koncie, które zostało użyte do uruchomienia zadania przygotowania zadania, zadanie ma dostęp do odczytu i zapisu do katalogu zadań przygotowania zadania. Jeśli zadanie jest uruchamiane na innym koncie niż zadanie uruchamiania lub zadanie przygotowania zadania, zadanie ma dostęp tylko do odczytu do odpowiedniego katalogu.

Aby uzyskać więcej informacji na temat uzyskiwania dostępu do plików i katalogów z zadania, zobacz [Pliki i katalogi](files-and-directories.md).

## <a name="elevated-access-for-tasks"></a>Podwyższony poziom dostępu dla zadań

Poziom podniesienia uprawnień konta użytkownika wskazuje, czy zadanie jest uruchamiane z podwyższonym poziomem dostępu. Zarówno konto użytkownika automatycznego, jak i nazwane konto użytkownika mogą być uruchamiane z podwyższonym poziomem dostępu. Dostępne są dwie opcje poziomu podniesienia uprawnień:

- **NonAdmin:** Zadanie jest uruchamiane jako użytkownik standardowy bez podwyższonego poziomu dostępu. Domyślny poziom podniesienia uprawnień dla konta użytkownika usługi Batch to zawsze **NonAdmin**.
- **Administrator:** Zadanie jest uruchamiane jako użytkownik z podwyższonym poziomem dostępu i działa z pełnymi uprawnieniami administratora.

## <a name="auto-user-accounts"></a>Konta użytkowników automatycznych

Domyślnie zadania są uruchamiane w u usługi Batch w ramach automatycznego konta użytkownika, jako użytkownik standardowy bez podwyższonego poziomu dostępu i z zakresem puli. Zakres puli oznacza, że zadanie jest uruchamiane w ramach automatycznego konta użytkownika, które jest dostępne dla dowolnego zadania w puli. Aby uzyskać więcej informacji na temat zakresu puli, zobacz [Uruchamianie zadania jako użytkownik automatyczny z zakresem puli](#run-a-task-as-an-auto-user-with-pool-scope).

Alternatywą dla zakresu puli jest zakres zadań. Po skonfigurowaniu automatycznej specyfikacji użytkownika dla zakresu zadań usługa Batch tworzy tylko konto użytkownika automatycznego dla tego zadania.

Istnieją cztery możliwe konfiguracje dla specyfikacji automatycznego użytkownika, z których każda odpowiada unikatowemu kontu użytkownika automatycznego:

- Dostęp bez uprawnień administratora z zakresem zadań
- Dostęp administratora (z podwyższonym poziomem uprawnień) z zakresem zadań
- Dostęp bez uprawnień administratora z zakresem puli
- Dostęp administratora z zakresem puli

> [!IMPORTANT]
> Zadania uruchomione w zakresie zadań nie mają w rzeczywistości dostępu do innych zadań w węźle. Jednak złośliwy użytkownik z dostępem do konta może ominąć to ograniczenie, przesyłając zadanie, które jest uruchamiane z uprawnieniami administratora i uzyskuje dostęp do innych katalogów zadań. Złośliwy użytkownik może również użyć protokołu RDP lub SSH do nawiązania połączenia z węzłem. Aby zapobiec takiemu scenariuszowi, należy chronić dostęp do kluczy konta usługi Batch. Jeśli podejrzewasz, że bezpieczeństwo Twojego konta zostało naruszone, pamiętaj, aby ponownie wygenerować klucze.

### <a name="run-a-task-as-an-auto-user-with-elevated-access"></a>Uruchamianie zadania jako automatyczny użytkownik z podwyższonym poziomem dostępu

Możesz skonfigurować specyfikację automatycznego użytkownika dla uprawnień administratora, gdy musisz uruchomić zadanie z podwyższonym poziomem dostępu. Na przykład zadanie uruchamiania może wymagać podwyższonego poziomu dostępu, aby zainstalować oprogramowanie w węźle.

> [!NOTE]
> Użyj podwyższonego poziomu dostępu tylko wtedy, gdy jest to konieczne. Najlepsze rozwiązania zalecają przyznanie minimalnych uprawnień niezbędnych do osiągnięcia żądanego wyniku. Jeśli na przykład zadanie uruchamiania instaluje oprogramowanie dla bieżącego użytkownika, a nie dla wszystkich użytkowników, może być możliwe uniknięcie przyznania podwyższonego poziomu dostępu do zadań. Można skonfigurować specyfikację automatycznego użytkownika dla zakresu puli i dostępu bez uprawnień administratora dla wszystkich zadań, które muszą być uruchamiane w ramach tego samego konta, w tym zadania uruchamiania.

Poniższe fragmenty kodu pokazują, jak skonfigurować specyfikację automatycznego użytkownika. W przykładach poziom podniesienia uprawnień został ustawiony na , `Admin` a zakres na `Task` .

#### <a name="batch-net"></a>Batch .NET

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin, scope: AutoUserScope.Task));
```

#### <a name="batch-java"></a>Batch Java

```java
taskToAdd.withId(taskId)
        .withUserIdentity(new UserIdentity()
            .withAutoUser(new AutoUserSpecification()
                .withElevationLevel(ElevationLevel.ADMIN))
                .withScope(AutoUserScope.TASK));
        .withCommandLine("cmd /c echo hello");
```

#### <a name="batch-python"></a>Batch Python

```python
user = batchmodels.UserIdentity(
    auto_user=batchmodels.AutoUserSpecification(
        elevation_level=batchmodels.ElevationLevel.admin,
        scope=batchmodels.AutoUserScope.task))
task = batchmodels.TaskAddParameter(
    id='task_1',
    command_line='cmd /c "echo hello world"',
    user_identity=user)
batch_client.task.add(job_id=jobid, task=task)
```

### <a name="run-a-task-as-an-auto-user-with-pool-scope"></a>Uruchamianie zadania jako automatyczny użytkownik z zakresem puli

Po aprowizowania węzła w każdym węźle w puli są tworzone dwa konta automatycznego użytkownika w całej puli, jedno z podwyższonym poziomem uprawnień i jedno bez podwyższonego poziomu dostępu. Ustawienie zakresu użytkownika automatycznego na zakres puli dla danego zadania uruchamia zadanie w ramach jednego z tych dwóch kont użytkowników automatycznych w całej puli.

Po określeniu zakresu puli dla użytkownika automatycznego wszystkie zadania uruchamiane z dostępem administratora są uruchamiane w ramach tego samego konta użytkownika automatycznego dla całej puli. Podobnie zadania uruchamiane bez uprawnień administratora są również uruchamiane w ramach jednego konta użytkownika automatycznego w całej puli.

> [!NOTE]
> Dwa konta automatycznego użytkownika w całej puli są oddzielnymi kontami. Zadania uruchomione w ramach konta administracyjnego dla całej puli nie mogą udostępniać danych z zadaniami uruchomionymi w ramach konta standardowego i na odwrót.

Zaletą działania w ramach tego samego konta użytkownika automatycznego jest to, że zadania mogą udostępniać dane innym zadaniam uruchomionym w tym samym węźle.

Udostępnianie wpisów tajnych między zadaniami jest jednym ze scenariuszy, w którym uruchamianie zadań w ramach jednego z dwóch kont automatycznego użytkownika w całej puli jest przydatne. Załóżmy na przykład, że zadanie uruchamiania musi aprowizować klucz tajny w węźle, z których mogą korzystać inne zadania. Możesz użyć interfejsu API ochrony danych systemu Windows (DPAPI), ale wymaga to uprawnień administratora. Zamiast tego możesz chronić klucz tajny na poziomie użytkownika. Zadania uruchomione na tym samym koncie użytkownika mogą uzyskać dostęp do tajnych danych bez podwyższonego poziomu dostępu.

Innym scenariuszem, w którym można uruchamiać zadania w ramach automatycznego konta użytkownika z zakresem puli, jest udział plików Message Passing Interface (MPI). Udział plików MPI jest przydatny, gdy węzły w zadaniu MPI muszą pracować na tych samych danych plików. Węzeł główny tworzy udział plików, do których węzły podrzędne mogą uzyskać dostęp, jeśli są uruchomione w ramach tego samego konta użytkownika automatycznego.

Poniższy fragment kodu ustawia zakres użytkownika automatycznego na zakres puli dla zadania na platformie .NET usługi Batch. Poziom podniesienia uprawnień zostanie pominięty, więc zadanie jest uruchamiane w ramach standardowego konta użytkownika automatycznego dla całej puli.

```csharp
task.UserIdentity = new UserIdentity(new AutoUserSpecification(scope: AutoUserScope.Pool));
```

## <a name="named-user-accounts"></a>Nazwane konta użytkowników

Podczas tworzenia puli można zdefiniować nazwane konta użytkowników. Nazwane konto użytkownika ma podaną nazwę i hasło. Możesz określić poziom podniesienia uprawnień dla nazwanego konta użytkownika. W przypadku węzłów systemu Linux można również podać klucz prywatny SSH.

Nazwane konto użytkownika istnieje we wszystkich węzłach w puli i jest dostępne dla wszystkich zadań podrzędnych uruchomionych w tych węzłach. Dla puli można zdefiniować dowolną liczbę nazwanych użytkowników. Podczas dodawania zadania lub kolekcji zadań można określić, że zadanie jest uruchamiane w ramach jednego z nazwanych kont użytkowników zdefiniowanych w puli.

Nazwane konto użytkownika jest przydatne, gdy chcesz uruchomić wszystkie zadania w zadaniu w ramach tego samego konta użytkownika, ale odizolować je od zadań uruchomionych w innych zadaniach w tym samym czasie. Można na przykład utworzyć nazwanego użytkownika dla każdego zadania i uruchomić zadania w ramach tego nazwanego konta użytkownika. Każde zadanie może następnie współdzielić klucz tajny z własnymi zadaniami, ale nie z zadaniami uruchomionymi w innych zadaniach.

Można również użyć nazwanego konta użytkownika, aby uruchomić zadanie, które ustawia uprawnienia do zasobów zewnętrznych, takich jak udziały plików. Za pomocą nazwanego konta użytkownika możesz kontrolować tożsamość użytkownika i używać tej tożsamości użytkownika do ustawienia uprawnień.  

Nazwane konta użytkowników umożliwiają bez hasła obsługę SSH między węzłami systemu Linux. Nazwanego konta użytkownika można używać z węzłami systemu Linux, które wymagają uruchamiania zadań o wielu wystąpieniach. Każdy węzeł w puli może uruchamiać zadania w ramach konta użytkownika zdefiniowanego w całej puli. Aby uzyskać więcej informacji na temat zadań o wielu wystąpieniach, zobacz Use multi instance tasks to run MPI applications (Używanie zadań o wielu wystąpieniach [ \- do uruchamiania aplikacji MPI).](batch-mpi.md)

### <a name="create-named-user-accounts"></a>Tworzenie nazwanych kont użytkowników

Aby utworzyć nazwane konta użytkowników w udaniu Batch, dodaj do puli kolekcję kont użytkowników. Poniższe fragmenty kodu pokazują, jak tworzyć nazwane konta użytkowników w środowiskach .NET, Java i Python. Te fragmenty kodu pokazują, jak utworzyć konta nazwane zarówno administratora, jak i inne niż administratora w puli. W przykładach pule są tworzyć przy użyciu konfiguracji usługi w chmurze, ale używa się tego samego podejścia podczas tworzenia puli systemu Windows lub Linux przy użyciu konfiguracji maszyny wirtualnej.

#### <a name="batch-net-example-windows"></a>Przykład usługi Batch dla programu .NET (Windows)

```csharp
CloudPool pool = null;
Console.WriteLine("Creating pool [{0}]...", poolId);

// Create a pool using the cloud service configuration.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "standard_d1_v2",
    VirtualMachineConfiguration: new VirtualMachineConfiguration(
    imageReference: new ImageReference(
                        publisher: "MicrosoftWindowsServer",
                        offer: "WindowsServer",
                        sku: "2019-datacenter-core",
                        version: "latest"),
    nodeAgentSkuId: "batch.node.windows amd64");

// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount("adminUser", "xyz123", ElevationLevel.Admin),
    new UserAccount("nonAdminUser", "123xyz", ElevationLevel.NonAdmin),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-net-example-linux"></a>Przykład usługi Batch dla .NET (Linux)

```csharp
CloudPool pool = null;

// Obtain a collection of all available node agent SKUs.
List<NodeAgentSku> nodeAgentSkus =
    batchClient.PoolOperations.ListNodeAgentSkus().ToList();

// Define a delegate specifying properties of the VM image to use.
Func<ImageReference, bool> isUbuntu1404 = imageRef =>
    imageRef.Publisher == "Canonical" &&
    imageRef.Offer == "UbuntuServer" &&
    imageRef.Sku.Contains("14.04");

// Obtain the first node agent SKU in the collection that matches
// Ubuntu Server 14.04. 
NodeAgentSku ubuntuAgentSku = nodeAgentSkus.First(sku =>
    sku.VerifiedImageReferences.Any(isUbuntu1404));

// Select an ImageReference from those available for node agent.
ImageReference imageReference =
    ubuntuAgentSku.VerifiedImageReferences.First(isUbuntu1404);

// Create the virtual machine configuration to use to create the pool.
VirtualMachineConfiguration virtualMachineConfiguration =
    new VirtualMachineConfiguration(imageReference, ubuntuAgentSku.Id);

Console.WriteLine("Creating pool [{0}]...", poolId);

// Create the unbound pool.
pool = batchClient.PoolOperations.CreatePool(
    poolId: poolId,
    targetDedicatedComputeNodes: 3,
    virtualMachineSize: "Standard_A1",
    virtualMachineConfiguration: virtualMachineConfiguration);
// Add named user accounts.
pool.UserAccounts = new List<UserAccount>
{
    new UserAccount(
        name: "adminUser",
        password: "xyz123",
        elevationLevel: ElevationLevel.Admin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 12345,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
    new UserAccount(
        name: "nonAdminUser",
        password: "123xyz",
        elevationLevel: ElevationLevel.NonAdmin,
        linuxUserConfiguration: new LinuxUserConfiguration(
            uid: 45678,
            gid: 98765,
            sshPrivateKey: new Guid().ToString()
            )),
};

// Commit the pool.
await pool.CommitAsync();
```

#### <a name="batch-java-example"></a>Przykład usługi Batch w języku Java

```java
List<UserAccount> userList = new ArrayList<>();
userList.add(new UserAccount().withName(adminUserAccountName).withPassword(adminPassword).withElevationLevel(ElevationLevel.ADMIN));
userList.add(new UserAccount().withName(nonAdminUserAccountName).withPassword(nonAdminPassword).withElevationLevel(ElevationLevel.NONADMIN));
PoolAddParameter addParameter = new PoolAddParameter()
        .withId(poolId)
        .withTargetDedicatedNodes(POOL_VM_COUNT)
        .withVmSize(POOL_VM_SIZE)
        .withVirtualMachineConfiguration(configuration)
        .withUserAccounts(userList);
batchClient.poolOperations().createPool(addParameter);
```

#### <a name="batch-python-example"></a>Przykład usługi Batch dla języka Python

```python
users = [
    batchmodels.UserAccount(
        name='pool-admin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.admin)
    batchmodels.UserAccount(
        name='pool-nonadmin',
        password='******',
        elevation_level=batchmodels.ElevationLevel.non_admin)
]
pool = batchmodels.PoolAddParameter(
    id=pool_id,
    user_accounts=users,
    virtual_machine_configuration=batchmodels.VirtualMachineConfiguration(
        image_reference=image_ref_to_use,
        node_agent_sku_id=sku_to_use),
    vm_size=vm_size,
    target_dedicated=vm_count)
batch_client.pool.add(pool)
```

### <a name="run-a-task-under-a-named-user-account-with-elevated-access"></a>Uruchamianie zadania w ramach nazwanego konta użytkownika z podwyższonym poziomem dostępu

Aby uruchomić zadanie jako użytkownik z podwyższonym poziomem uprawnień, ustaw właściwość **UserIdentity** zadania na nazwane konto użytkownika, które zostało utworzone z **właściwością ElevationLevel** ustawioną na `Admin` wartość .

Ten fragment kodu określa, że zadanie powinno być uruchamiane w ramach nazwanego konta użytkownika. To nazwane konto użytkownika zostało zdefiniowane w puli podczas jej tworzenia. W takim przypadku nazwane konto użytkownika zostało utworzone z uprawnieniami administratora:

```csharp
CloudTask task = new CloudTask("1", "cmd.exe /c echo 1");
task.UserIdentity = new UserIdentity(AdminUserAccountName);
```

## <a name="update-your-code-to-the-latest-batch-client-library"></a>Aktualizowanie kodu do najnowszej biblioteki klienta usługi Batch

W wersji 2017-01-01.4.0 usługi Batch wprowadzono zmianę przerywaną, zastępując właściwość **runElevated** dostępną we wcześniejszych wersjach **właściwością userIdentity.** Poniższe tabele zawierają proste mapowanie, za pomocą których można zaktualizować kod z wcześniejszych wersji bibliotek klienckich.

### <a name="batch-net"></a>Batch .NET

| Jeśli kod używa...                  | Zaktualizuj go do...                                                                                                 |
|---------------------------------------|------------------------------------------------------------------------------------------------------------------|
| `CloudTask.RunElevated = true;`       | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.Admin));`    |
| `CloudTask.RunElevated = false;`      | `CloudTask.UserIdentity = new UserIdentity(new AutoUserSpecification(elevationLevel: ElevationLevel.NonAdmin));` |
| `CloudTask.RunElevated` nie określono | Nie jest wymagana żadna aktualizacja                                                                                               |

### <a name="batch-java"></a>Batch Java

| Jeśli kod używa...                      | Zaktualizuj go do...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `CloudTask.withRunElevated(true);`        | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.ADMIN));`    |
| `CloudTask.withRunElevated(false);`       | `CloudTask.withUserIdentity(new UserIdentity().withAutoUser(new AutoUserSpecification().withElevationLevel(ElevationLevel.NONADMIN));` |
| `CloudTask.withRunElevated` nie określono | Nie jest wymagana żadna aktualizacja                                                                                                                     |

### <a name="batch-python"></a>Batch Python

| Jeśli kod używa...                      | Zaktualizuj go do...                                                                                                                       |
|-------------------------------------------|----------------------------------------------------------------------------------------------------------------------------------------|
| `run_elevated=True`                       | `user_identity=user`Gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.admin))`                |
| `run_elevated=False`                      | `user_identity=user`Gdzie <br />`user = batchmodels.UserIdentity(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`auto_user=batchmodels.AutoUserSpecification(`<br />&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;`elevation_level=batchmodels.ElevationLevel.non_admin))`             |
| `run_elevated` nie określono | Nie jest wymagana żadna aktualizacja                                                                                                                                  |

## <a name="next-steps"></a>Następne kroki

- Dowiedz się więcej o [przepływie pracy usługi Batch i zasobach podstawowych,](batch-service-workflow-features.md) takich jak pule, węzły, zadania i zadania podrzędne.
- Dowiedz się [więcej o plikach i katalogach w](files-and-directories.md) Azure Batch.
